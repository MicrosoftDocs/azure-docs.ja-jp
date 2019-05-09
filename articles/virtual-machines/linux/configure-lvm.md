---
title: Linux を実行する仮想マシンで LVM を構成する | Microsoft Docs
description: Azure で Linux の LVM を構成する方法について説明します。
services: virtual-machines-linux
documentationcenter: na
author: szarkos
manager: jeconnoc
editor: tysonn
tag: azure-service-management,azure-resource-manager
ms.assetid: 7f533725-1484-479d-9472-6b3098d0aecc
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 09/27/2018
ms.author: szark
ms.subservice: disks
ms.openlocfilehash: 08f98775360b8c0a82f68f322053cb71f0e79af3
ms.sourcegitcommit: 698a3d3c7e0cc48f784a7e8f081928888712f34b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/31/2019
ms.locfileid: "55457329"
---
# <a name="configure-lvm-on-a-linux-vm-in-azure"></a>Azure で Linux VM の LVM を構成する
このドキュメントでは、Azure 仮想マシンに論理ボリューム マネージャー (LVM) を構成する方法を説明します。 LVM は Azure VM の OS ディスクまたはデータ ディスクで使用できますが、既定では、ほとんどのクラウド イメージの OS ディスクには LVM は構成されません。 以下の手順では、データ ディスクへの LVM の構成に焦点を当てます。

## <a name="linear-vs-striped-logical-volumes"></a>リニア論理ボリュームとストライプ論理ボリューム
LVM を使用して、複数の物理ディスクを 1 つの記憶域ボリュームにまとめることができます。 既定により、LVM は、通常はリニア論理ボリュームを作成します。これは物理記憶域が連結されることを意味します。 この場合、読み取り/書き込み操作は、通常は 1 台のディスクのみに送信されます。 これに対し、ボリューム グループに含まれる複数のディスクに読み取りと書き込みを分散させるストライプ論理ボリューム (RAID0 に似ています) を作成することもできます。 パフォーマンス上の理由で、論理ボリュームをストライピングして、アタッチされたすべてのデータ ディスクを読み取りと書き込みで利用できるようにすることを選択できます。

このドキュメントでは、複数のデータ ディスクを 1 つのボリューム グループにまとめた後、ストライプ論理ボリュームを作成する方法について説明します。 以下の手順は、大部分のディストリビューションで使用できるように、一般化されています。 ほとんどの場合、Azure で LVM を管理するためのユーティリティとワークフローは、基本的にはその他の環境と違いはありません。 特定のディストリビューションで LVM を使用するためのドキュメントとベスト プラクティスについては、通常どおり Linux ベンダーにも問い合わせてください。

## <a name="attaching-data-disks"></a>データ ディスクをアタッチする
通常、LVM の使用は、2 台以上の空のデータ ディスクから始めることができます。 IO ニーズに応じて、Standard Storage または Premium Storage に格納されているディスクをアタッチするように選択できます。Standard Storage ではディスクあたり最大 500 IO/ps が、Premium Storage ではディスクあたり最大 5000 IO/ps が実現します。 この記事では、データ ディスクをプロビジョニングし、Linux 仮想マシンにアタッチする方法については詳しく説明しません。 Azure 内の Linux 仮想マシンに空のデータ ディスクをアタッチする方法の詳細については、Microsoft Azure の記事 「[Linux VM へのディスクの追加](add-disk.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)」を参照してください。

## <a name="install-the-lvm-utilities"></a>LVM ユーティリティをインストールする
* **Ubuntu**

    ```bash  
    sudo apt-get update
    sudo apt-get install lvm2
    ```

* **RHEL、CentOS、および Oracle Linux**

    ```bash   
    sudo yum install lvm2
    ```

* **SLES 12 と openSUSE**

    ```bash   
    sudo zypper install lvm2
    ```

* **SLES 11**

    ```bash   
    sudo zypper install lvm2
    ```

    SLES11 では、`/etc/sysconfig/lvm` を編集して、`LVM_ACTIVATED_ON_DISCOVERED` を "enable" に設定する必要もあります。

    ```sh   
    LVM_ACTIVATED_ON_DISCOVERED="enable" 
    ```

## <a name="configure-lvm"></a>LVM を構成する
このガイドでは、3 台のデータ ディスクをアタッチしている想定し、それらを `/dev/sdc`、`/dev/sdd`、および `/dev/sde` と呼びます。 これらのパスは、お使いの VM でのディスク パス名と一致しない可能性があります。 '`sudo fdisk -l`' などのコマンドを実行して、使用可能なディスクを一覧表示できます。

1. 物理ボリュームを準備します。

    ```bash    
    sudo pvcreate /dev/sd[cde]
    Physical volume "/dev/sdc" successfully created
    Physical volume "/dev/sdd" successfully created
    Physical volume "/dev/sde" successfully created
    ```

2. ボリューム グループを作成します。 この例では、ボリューム グループを `data-vg01` と呼びます。

    ```bash    
    sudo vgcreate data-vg01 /dev/sd[cde]
    Volume group "data-vg01" successfully created
    ```

3. 論理ボリュームを作成します。 次のコマンドでは、ボリューム グループ全体に広がる `data-lv01` と呼ばれる 1 つの論理ボリュームを作成しますが、ボリューム グループ内に複数の論理ボリュームを作成することもできます。

    ```bash   
    sudo lvcreate --extents 100%FREE --stripes 3 --name data-lv01 data-vg01
    Logical volume "data-lv01" created.
    ```

4. 論理ボリュームをフォーマットします。

    ```bash  
    sudo mkfs -t ext4 /dev/data-vg01/data-lv01
    ```
   
   > [!NOTE]
   > SLES11 では、ext4 の代わりに `-t ext3` を使用します。 SLES11 では、ext4 ファイル システムに対する読み取り専用アクセスのみをサポートします。

## <a name="add-the-new-file-system-to-etcfstab"></a>新しいファイル システムを /etc/ fstab に追加する
> [!IMPORTANT]
> `/etc/fstab` ファイルを不適切に編集すると、システムが起動できなくなる可能性があります。 編集方法がはっきりわからない場合は、このファイルを適切に編集する方法について、ディストリビューションのドキュメントを参照してください。 編集する前に、`/etc/fstab` ファイルのバックアップを作成することもお勧めします。

1. 新しいファイル システムに必要なマウント ポイントを作成します。たとえば、次のようになります。

    ```bash  
    sudo mkdir /data
    ```

2. 論理ボリュームのパスを見つけます。

    ```bash    
    lvdisplay
    --- Logical volume ---
    LV Path                /dev/data-vg01/data-lv01
    ....
    ```

3. テキスト エディターで `/etc/fstab` を開き、新しいファイル システムのエントリを追加します。たとえば、次のようになります。

    ```bash    
    /dev/data-vg01/data-lv01  /data  ext4  defaults  0  2
    ```   
    `/etc/fstab`を保存して閉じます。

4. `/etc/fstab` のエントリが正しいことをテストします。

    ```bash    
    sudo mount -a
    ```

    このコマンドによりエラー メッセージが表示された場合は、`/etc/fstab` ファイル内の構文を確認してください。
   
    次に、 `mount` コマンドを実行して、ファイル システムがマウントされていることを確認します。

    ```bash    
    mount
    ......
    /dev/mapper/data--vg01-data--lv01 on /data type ext4 (rw)
    ```

5. (オプション) `/etc/fstab`内のフェールセーフ ブート パラメーター
   
    多くのディストリビューションでは、`nobootwait` または`nofail` のいずれかのマウント パラメーターが `/etc/fstab`ファイルに追加されている場合があります。 これらのパラメーターにより、特定のファイル システムをマウントしているときのエラーが許容されます。RAID ファイル システムを適切にマウントできない場合でも、Linux システムの起動を続行できるようになります。 これらのパラメーターの詳細については、使用しているディストリビューションのドキュメントを参照してください。
   
    例 (Ubuntu):

    ```bash 
    /dev/data-vg01/data-lv01  /data  ext4  defaults,nobootwait  0  2
    ```

## <a name="trimunmap-support"></a>TRIM/UNMAP のサポート
一部の Linux カーネルでは、ディスク上の未使用ブロックを破棄するために TRIM/UNMAP 操作がサポートされます。 これらの操作は主に、Standard Storage で、削除されたページが無効になり、破棄できるようになったことを Azure に通知するときに役立ちます。 ページを破棄すると、サイズの大きいファイルを作成して削除する場合のコストを節約できます。

Linux VM で TRIM のサポートを有効にする方法は 2 通りあります。 通常どおり、ご使用のディストリビューションで推奨される方法をお問い合わせください。

- 次のように、`/etc/fstab` で `discard` マウント オプションを使用します。

    ```bash 
    /dev/data-vg01/data-lv01  /data  ext4  defaults,discard  0  2
    ```

- 場合によっては、`discard` オプションがパフォーマンスに影響する可能性があります。 または、 `fstrim` コマンドを手動でコマンド ラインから実行するか、crontab に追加して定期的に実行することができます。

    **Ubuntu**

    ```bash 
    # sudo apt-get install util-linux
    # sudo fstrim /datadrive
    ```

    **RHEL/CentOS**

    ```bash 
    # sudo yum install util-linux
    # sudo fstrim /datadrive
    ```
