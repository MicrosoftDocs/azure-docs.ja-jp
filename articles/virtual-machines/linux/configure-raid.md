---
title: Linux を実行する仮想マシンでのソフトウェア RAID の構成 | Microsoft Docs
description: mdadm を使用して Azure 内の Linux で RAID を構成する方法について説明します。
services: virtual-machines-linux
documentationcenter: na
author: rickstercdn
manager: gwallace
editor: tysonn
tag: azure-service-management,azure-resource-manager
ms.assetid: f3cb2786-bda6-4d2c-9aaf-2db80f490feb
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.topic: article
ms.date: 02/02/2017
ms.author: rclaus
ms.subservice: disks
ms.openlocfilehash: d0658af090d9a3f39bee69f5103a78a329fe189c
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/28/2019
ms.locfileid: "70083807"
---
# <a name="configure-software-raid-on-linux"></a>Linux でのソフトウェア RAID の構成
一般的なシナリオは、Azure 内の Linux 仮想マシンでソフトウェア RAID を使用して、複数のデータ ディスクを 1 つの RAID デバイスとしてアタッチすることです。 このシナリオを使用すると通常、1 つのみのディスクを使用するシナリオよりもパフォーマンスとスループットが向上します。

## <a name="attaching-data-disks"></a>データ ディスクをアタッチする
RAID デバイスの構成には、2 つ以上の空のデータ ディスクが必要です。  RAID デバイスを作成する主な目的は、ディスク I/O のパフォーマンスを向上させることです。  IO ニーズに応じて、Standard Storage または Premium Storage に格納されているディスクをアタッチするように選択できます。Standard Storage ではディスクあたり最大 500 IO/ps が、Premium Storage ではディスクあたり最大 5000 IO/ps が実現します。 この記事では、データ ディスクをプロビジョニングし、Linux 仮想マシンにアタッチする方法については詳しく説明しません。  Azure 内の Linux 仮想マシンに空のデータ ディスクをアタッチする方法の詳細については、Microsoft Azure の記事 「[Linux VM へのディスクの追加](add-disk.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)」を参照してください。

## <a name="install-the-mdadm-utility"></a>mdadm ユーティリティをインストールする
* **Ubuntu**
  ```bash
  sudo apt-get update
  sudo apt-get install mdadm
  ```

* **CentOS と Oracle Linux**
  ```bash
  sudo yum install mdadm
  ```

* **SLES と openSUSE**
  ```bash  
  zypper install mdadm
  ```

## <a name="create-the-disk-partitions"></a>ディスク パーティションを作成する
この例では、/dev/sdc に 1 つのディスク パーティションを作成します。 その後、新しいディスク パーティションに /dev/sdc1 という名前を付けます。

1. `fdisk` を使用してパーティションの作成を開始する

    ```bash
    sudo fdisk /dev/sdc
    Device contains neither a valid DOS partition table, nor Sun, SGI or OSF disklabel
    Building a new DOS disklabel with disk identifier 0xa34cb70c.
    Changes will remain in memory only, until you decide to write them.
    After that, of course, the previous content won't be recoverable.

    WARNING: DOS-compatible mode is deprecated. It's strongly recommended to
                    switch off the mode (command 'c') and change display units to
                    sectors (command 'u').
    ```

1. プロンプトが表示されたら 'n' キーを押して、**新しい**パーティションを作成します。

    ```bash
    Command (m for help): n
    ```

1. 次に、'p' キーを押して、**プライマリ** パーティションを作成します。

    ```bash 
    Command action
            e   extended
            p   primary partition (1-4)
    ```

1. 1 キーを押して、パーティション番号 1 を選択します。

    ```bash
    Partition number (1-4): 1
    ```

1. 新しいパーティションの開始位置を選択するか、`<enter>` キーを押して既定の設定をそのまま使用します。既定では、ドライブの空き領域の先頭にパーティションが配置されます。

    ```bash   
    First cylinder (1-1305, default 1):
    Using default value 1
    ```

1. パーティションのサイズとして、たとえば「+10G」と入力して、10 GB のパーティションを作成します。 または、 `<enter>` キーを押して、ドライブ全体にまたがる 1 つのパーティションを作成します。

    ```bash   
    Last cylinder, +cylinders or +size{K,M,G} (1-1305, default 1305): 
    Using default value 1305
    ```

1. 次に、パーティションの ID と**タイプ**を変更します (コマンド 't')。既定の ID '83' (Linux) から ID 'fd' (Linux raid auto) に変更します。

    ```bash  
    Command (m for help): t
    Selected partition 1
    Hex code (type L to list codes): fd
    ```

1. 最後に、パーティション テーブルをドライブに書き込み、fdisk を終了します。

    ```bash   
    Command (m for help): w
    The partition table has been altered!
    ```

## <a name="create-the-raid-array"></a>RAID アレイを作成する
1. 次の例では、3 つの個別のデータ ディスク (sdc1、sdd1、sde1) にある 3 つのパーティションを "ストライピング" (RAID レベル 0) にします。  このコマンドを実行した後、新しい RAID デバイスが **/dev/md127** という名前で作成されます。 これらのデータ ディスクが前に別の無効 RAID アレイの一部となっていた場合は、必要に応じて `--force` パラメーターを `mdadm` コマンドに追加してください。

    ```bash  
    sudo mdadm --create /dev/md127 --level 0 --raid-devices 3 \
        /dev/sdc1 /dev/sdd1 /dev/sde1
    ```

1. 新しい RAID デバイスにファイル システムを作成します。
   
    **CentOS、Oracle Linux、SLES 12、openSUSE、Ubuntu**

    ```bash   
    sudo mkfs -t ext4 /dev/md127
    ```
   
    **SLES 11**

    ```bash
    sudo mkfs -t ext3 /dev/md127
    ```
   
    **SLES 11** - boot.md を有効にし、mdadm.conf を作成します。

    ```bash
    sudo -i chkconfig --add boot.md
    sudo echo 'DEVICE /dev/sd*[0-9]' >> /etc/mdadm.conf
    ```
   
   > [!NOTE]
   > SUSE システムでこれらの変更を行った後は再起動が必要になる場合があります。 SLES 12 では、この手順は必須では *ありません* 。
   > 
   

## <a name="add-the-new-file-system-to-etcfstab"></a>新しいファイル システムを /etc/ fstab に追加する
> [!IMPORTANT]
> /etc/fstab ファイルを不適切に編集すると、システムが起動できなくなる可能性があります。 編集方法がはっきりわからない場合は、このファイルを適切に編集する方法について、ディストリビューションのドキュメントを参照してください。 編集する前に、/etc/fstab ファイルのバックアップを作成することもお勧めします。

1. 新しいファイル システムに必要なマウント ポイントを作成します。たとえば、次のようになります。

    ```bash
    sudo mkdir /data
    ```
1. /etc/fstab を編集するとき、 **UUID** は、デバイス名ではなくファイル システムを参照するために使用する必要があります。  新しいファイル システムの UUID を調べるには、 `blkid` ユーティリティを使用します。

    ```bash   
    sudo /sbin/blkid
    ...........
    /dev/md127: UUID="aaaaaaaa-bbbb-cccc-dddd-eeeeeeeeeeee" TYPE="ext4"
    ```

1. テキスト エディターで /etc/fstab を開き、新しいファイル システムのエントリを追加します。たとえば、次のようになります。

    ```bash   
    UUID=aaaaaaaa-bbbb-cccc-dddd-eeeeeeeeeeee  /data  ext4  defaults  0  2
    ```
   
    または、**SLES 11** 上で次のように指定します。

    ```bash
    /dev/disk/by-uuid/aaaaaaaa-bbbb-cccc-dddd-eeeeeeeeeeee  /data  ext3  defaults  0  2
    ```
   
    その後、/etc/fstab を保存して閉じます。

1. /etc/fstab のエントリが正しいことをテストします。

    ```bash  
    sudo mount -a
    ```

    このコマンドによりエラー メッセージが表示された場合は、/etc/fstab ファイル内の構文を確認してください。
   
    次に、 `mount` コマンドを実行して、ファイル システムがマウントされていることを確認します。

    ```bash   
    mount
    .................
    /dev/md127 on /data type ext4 (rw)
    ```

1. (オプション) フェールセーフ ブート パラメーター
   
    **fstab 構成**
   
    多くのディストリビューションでは、`nobootwait` または `nofail` のいずれかのマウント パラメーターが /etc/fstab ファイルに追加されている場合があります。 これらのパラメーターにより、特定のファイル システムをマウントしているときのエラーが許容されます。RAID ファイル システムを適切にマウントできない場合でも、Linux システムの起動を続行できるようになります。 これらのパラメーターの詳細については、使用しているディストリビューションのドキュメントを参照してください。
   
    例 (Ubuntu):

    ```bash  
    UUID=aaaaaaaa-bbbb-cccc-dddd-eeeeeeeeeeee  /data  ext4  defaults,nobootwait  0  2
    ```   

    **Linux ブート パラメーター**
   
    これらのパラメーターの他にも、カーネル パラメーター "`bootdegraded=true`" では、RAID が破損または劣化として認識された場合、たとえばデータ ドライブが誤って仮想マシンから削除された場合でも、システムを起動できるようになります。 既定では、この場合はシステムが起動できなくなる可能性があります。
   
    カーネル パラメーターの適切な編集方法については、使用しているディストリビューションのドキュメントを参照してください。 たとえば、多くディストリビューション (CentOS、Oracle Linux、SLES 11) では、これらのパラメーターを "`/boot/grub/menu.lst`" ファイルに手動で追加することもできます。  Ubuntu では、このパラメーターを "/etc/default/ grub" の `GRUB_CMDLINE_LINUX_DEFAULT` 変数に追加できます。


## <a name="trimunmap-support"></a>TRIM/UNMAP のサポート
一部の Linux カーネルでは、ディスク上の未使用ブロックを破棄するために TRIM/UNMAP 操作がサポートされます。 これらの操作は主に、Standard Storage で、削除されたページが無効になり、破棄できるようになったことを Azure に通知するときに役立ちます。 ページを破棄すると、サイズの大きいファイルを作成して削除する場合のコストを節約できます。

> [!NOTE]
> 配列のチャンク サイズが既定値 (512 KB) よりも小さく設定されている場合、RAID が破棄コマンドを発行しない可能性があります。 これは、ホストでの UNMAP の粒度も 512 KB であるためです。 mdadm の `--chunk=` パラメーターを使用して配列のチャンク サイズを変更した場合、TRIM/UNMAP 要求がカーネルによって無視される可能性があります。

Linux VM で TRIM のサポートを有効にする方法は 2 通りあります。 通常どおり、ご使用のディストリビューションで推奨される方法をお問い合わせください。

- 次のように、`/etc/fstab` で `discard` マウント オプションを使用します。

    ```bash
    UUID=aaaaaaaa-bbbb-cccc-dddd-eeeeeeeeeeee  /data  ext4  defaults,discard  0  2
    ```

- 場合によっては、`discard` オプションがパフォーマンスに影響する可能性があります。 または、 `fstrim` コマンドを手動でコマンド ラインから実行するか、crontab に追加して定期的に実行することができます。

    **Ubuntu**

    ```bash
    # sudo apt-get install util-linux
    # sudo fstrim /data
    ```

    **RHEL/CentOS**
    ```bash
    # sudo yum install util-linux
    # sudo fstrim /data
    ```
