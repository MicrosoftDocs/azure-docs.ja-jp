---
title: "Azure での Debian Linux VHD の準備 | Microsoft Docs"
description: "Azure へのデプロイ用に Debian 7 および 8 の VHD ファイルを作成する方法について説明します。"
services: virtual-machines-linux
documentationcenter: 
author: szarkos
manager: timlt
editor: 
tags: azure-resource-manager,azure-service-management
ms.assetid: a6de7a7c-cc70-44e7-aed0-2ae6884d401a
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 02/02/2017
ms.author: szark
translationtype: Human Translation
ms.sourcegitcommit: 356de369ec5409e8e6e51a286a20af70a9420193
ms.openlocfilehash: 93d30941af689e19e48c9ee204867c96348ecc5f
ms.lasthandoff: 03/27/2017


---
# <a name="prepare-a-debian-vhd-for-azure"></a>Azure 用の Debian VHD の準備
## <a name="prerequisites"></a>前提条件
このセクションでは、 [Debian Web サイト](https://www.debian.org/distrib/) から仮想ハード ディスクにダウンロードした .iso ファイルから Debian Linux オペレーティング システムを既にインストールしているものと想定しています。 .vhd ファイルを作成するためのツールは複数あり、Hyper-V は 1 つの例にすぎません。 Hyper-V の使い方の詳細については、 [Hyper-V の役割のインストールと仮想マシンの構成](https://technet.microsoft.com/library/hh846766.aspx)に関するページを参照してください。

## <a name="installation-notes"></a>インストールに関する注記
* Azure で Linux を準備する際のその他のヒントについては、「 [Linux のインストールに関する注記](virtual-machines-linux-create-upload-generic.md#general-linux-installation-notes) 」も参照してください。
* 新しい VHDX 形式は、Azure ではサポートされていません。 Hyper-V マネージャーまたは **convert-vhd** コマンドレットを使用して、ディスクを VHD 形式に変換できます。
* Linux システムをインストールする場合は、LVM (通常、多くのインストールで既定) ではなく標準パーティションを使用することをお勧めします。 これにより、特に OS ディスクをトラブルシューティングのために別の VM に接続する必要がある場合に、LVM 名と複製された VM の競合が回避されます。 必要な場合は、[LVM](virtual-machines-linux-configure-lvm.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) または [RAID](virtual-machines-linux-configure-raid.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) をデータ ディスク上で使用できます。
* OS ディスクにスワップ パーティションを構成しないでください。 Azure Linux エージェントは、一時的なリソース ディスク上にスワップ ファイルを作成するよう構成できます。 このことに関する詳細については、次の手順を参照してください。
* すべての VHD のサイズは 1 MB の倍数であることが必要です。

## <a name="use-azure-manage-to-create-debian-vhds"></a>Azure-Manage を使用した Debian VHD の作成
[credativ](http://www.credativ.com/) の [azure-manage](https://github.com/credativ/azure-manage) スクリプトなど、Azure 用に Debian VHD を生成するために使用できるツールがあります。 これは、最初からイメージを作成するよりもお勧めの方法です。 たとえば、Debian 8 VHD を作成するには、次のコマンドを実行して azure-manage (と依存関係) をダウンロードし、azure_build_image スクリプトを実行します。

    # sudo apt-get update
    # sudo apt-get install git qemu-utils mbr kpartx debootstrap

    # sudo apt-get install python3-pip python3-dateutil python3-cryptography
    # sudo pip3 install azure-storage azure-servicemanagement-legacy azure-common pytest pyyaml
    # git clone https://github.com/credativ/azure-manage.git
    # cd azure-manage
    # sudo pip3 install .

    # sudo azure_build_image --option release=jessie --option image_size_gb=30 --option image_prefix=debian-jessie-azure section


## <a name="manually-prepare-a-debian-vhd"></a>手動での Debian VHD の準備
1. Hyper-V マネージャーで仮想マシンを選択します。
2. **[接続]** をクリックすると、仮想マシンのコンソール ウィンドウが開きます。
3. ISO ファイルに対して VM を設定する場合は、`/etc/apt/source.list` の **deb cdrom** の行をコメント アウトします。
4. `/etc/default/grub` ファイルを編集し、**GRUB_CMDLINE_LINUX** パラメーターを次のように変更して、Azure の追加のカーネル パラメーターを含めます。
   
        GRUB_CMDLINE_LINUX="console=tty0 console=ttyS0,115200 earlyprintk=ttyS0,115200 rootdelay=30"
5. GRUB をリビルドして、次のスクリプトを実行します。
   
        # sudo update-grub
6. Debian 7 または 8 については、/Etc/apt/sources.list に Debian の Azure リポジトリを追加します。
   
    **Debian 7.x "Wheezy"**
   
        deb http://debian-archive.trafficmanager.net/debian wheezy-backports main
        deb-src http://debian-archive.trafficmanager.net/debian wheezy-backports main
        deb http://debian-archive.trafficmanager.net/debian-azure wheezy main
        deb-src http://debian-archive.trafficmanager.net/debian-azure wheezy main

    **Debian 8.x "Jessie"**

        deb http://debian-archive.trafficmanager.net/debian jessie-backports main
        deb-src http://debian-archive.trafficmanager.net/debian jessie-backports main
        deb http://debian-archive.trafficmanager.net/debian-azure jessie main
        deb-src http://debian-archive.trafficmanager.net/debian-azure jessie main


1. Azure Linux エージェントをインストールします。
   
        # sudo apt-get update
        # sudo apt-get install waagent
2. Debian 7 場合、wheezy backports リポジトリから 3.16 ベース カーネルを実行する必要があります。 まず、次の内容を含む /etc/apt/preferences.d/linux.pref という名前のファイルを作成します。
   
        Package: linux-image-amd64 initramfs-tools
        Pin: release n=wheezy-backports
        Pin-Priority: 500
   
    次に、"sudo apt-get install linux-image-amd64" を実行して、新しいカーネルをインストールします。
3. 仮想マシンをプロビジョニング解除し、Azure でのプロビジョニング用に準備して、次のスクリプトを実行します。
   
        # sudo waagent –force -deprovision
        # export HISTSIZE=0
        # logout
4. Hyper-V マネージャーで **[アクション]、[シャットダウン]** の順にクリックします。 これで、Linux VHD を Azure にアップロードする準備が整いました。

## <a name="next-steps"></a>次のステップ
これで、Debian 仮想ハード ディスク を使用して、Azure に新しい仮想マシンを作成する準備が整いました。 .vhd ファイルを Azure に初めてアップロードする場合は、「 [Linux オペレーティング システムを格納した仮想ハード ディスクの作成とアップロード](linux/classic/create-upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2fclassic%2ftoc.json)」の手順 2 と 3 をご覧ください。


