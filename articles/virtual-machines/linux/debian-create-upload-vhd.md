---
title: Azure での Debian Linux VHD の準備 | Microsoft Docs
description: Azure 内にデプロイする Debian VHD イメージの作成方法について説明します。
services: virtual-machines-linux
documentationcenter: ''
author: szarkos
manager: gwallace
editor: ''
tags: azure-resource-manager,azure-service-management
ms.assetid: a6de7a7c-cc70-44e7-aed0-2ae6884d401a
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 11/13/2018
ms.author: szark
ms.openlocfilehash: bdeaf4ec4a276e7cdd94402159f6adac474b3af8
ms.sourcegitcommit: 2e4b99023ecaf2ea3d6d3604da068d04682a8c2d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/09/2019
ms.locfileid: "67671525"
---
# <a name="prepare-a-debian-vhd-for-azure"></a>Azure 用の Debian VHD の準備
## <a name="prerequisites"></a>前提条件
このセクションでは、 [Debian Web サイト](https://www.debian.org/distrib/) から仮想ハード ディスクにダウンロードした .iso ファイルから Debian Linux オペレーティング システムを既にインストールしているものと想定しています。 .vhd ファイルを作成するためのツールは複数あり、Hyper-V は 1 つの例にすぎません。 Hyper-V の使い方の詳細については、 [Hyper-V の役割のインストールと仮想マシンの構成](https://technet.microsoft.com/library/hh846766.aspx)に関するページを参照してください。

## <a name="installation-notes"></a>インストールに関する注記
* Azure で Linux を使用するためのその他のヒントについては、「[Linux のインストールに関する一般的な注記](create-upload-generic.md#general-linux-installation-notes)」も参照してください。
* 新しい VHDX 形式は、Azure ではサポートされていません。 Hyper-V マネージャーまたは **convert-vhd** コマンドレットを使用して、ディスクを VHD 形式に変換できます。
* Linux システムをインストールする場合は、LVM (通常、多くのインストールで既定) ではなく標準パーティションを使用することをお勧めします。 これにより、特に OS ディスクをトラブルシューティングのために別の VM に接続する必要がある場合に、LVM 名と複製された VM の競合が回避されます。 必要な場合は、[LVM](configure-lvm.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) または [RAID](configure-raid.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) をデータ ディスク上で使用できます。
* OS ディスクにスワップ パーティションを構成しないでください。 Azure Linux エージェントは、一時的なリソース ディスク上にスワップ ファイルを作成するよう構成できます。 詳細については、以下の手順を参照してください。
* Azure の VHD の仮想サイズはすべて、1 MB にアラインメントさせる必要があります。 未フォーマット ディスクから VHD に変換するときに、変換する前の未フォーマット ディスクのサイズが 1 MB の倍数であることを確認する必要があります。 詳細については、「[Linux のインストールに関する一般的な注記](create-upload-generic.md#general-linux-installation-notes)」に関する記事を参照してください。

## <a name="use-azure-manage-to-create-debian-vhds"></a>Azure-Manage を使用した Debian VHD の作成
[credativ](https://www.credativ.com/) の [azure-manage](https://github.com/credativ/azure-manage) スクリプトなど、Azure 用に Debian VHD を生成するために使用できるツールがあります。 これは、最初からイメージを作成するよりもお勧めの方法です。 たとえば、Debian 8 VHD を作成するには、次のコマンドを実行して `azure-manage` ユーティリティ (および依存関係ファイル) をダウンロードし、`azure_build_image` スクリプトを実行します。

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
3. ISO を使用して OS をインストールした場合は、`/etc/apt/source.list` 内の "`deb cdrom`" に関連するすべての行をコメント化します。

4. `/etc/default/grub` ファイルを編集し、**GRUB_CMDLINE_LINUX** パラメーターを次のように変更して、Azure の追加のカーネル パラメーターを含めます。
   
        GRUB_CMDLINE_LINUX="console=tty0 console=ttyS0,115200n8 earlyprintk=ttyS0,115200"

5. GRUB をリビルドして、次のスクリプトを実行します。

        # sudo update-grub

6. Debian 8 または 9 については、/etc/apt/sources.list に Debian の Azure リポジトリを追加します。

    **Debian 8.x "Jessie"**

        deb http://debian-archive.trafficmanager.net/debian jessie main
        deb-src http://debian-archive.trafficmanager.net/debian jessie main
        deb http://debian-archive.trafficmanager.net/debian-security jessie/updates main
        deb-src http://debian-archive.trafficmanager.net/debian-security jessie/updates
        deb http://debian-archive.trafficmanager.net/debian jessie-updates main
        deb-src http://debian-archive.trafficmanager.net/debian jessie-updates main
        deb http://debian-archive.trafficmanager.net/debian jessie-backports main
        deb-src http://debian-archive.trafficmanager.net/debian jessie-backports main

    **Debian 9.x "Stretch"**

        deb http://debian-archive.trafficmanager.net/debian stretch main
        deb-src http://debian-archive.trafficmanager.net/debian stretch main
        deb http://debian-archive.trafficmanager.net/debian-security stretch/updates main
        deb-src http://debian-archive.trafficmanager.net/debian-security stretch/updates main
        deb http://debian-archive.trafficmanager.net/debian stretch-updates main
        deb-src http://debian-archive.trafficmanager.net/debian stretch-updates main
        deb http://debian-archive.trafficmanager.net/debian stretch-backports main
        deb-src http://debian-archive.trafficmanager.net/debian stretch-backports main


7. Azure Linux エージェントをインストールします。
   
        # sudo apt-get update
        # sudo apt-get install waagent

8. Debian 9+ の場合は、Azure 内の VM で使用する新しい Debian Cloud カーネルを使用することをお勧めします。 この新しいカーネルをインストールするには、最初に /etc/apt/preferences.d/linux.pref というファイルを作成して、次の内容を設定します。
   
        Package: linux-* initramfs-tools
        Pin: release n=stretch-backports
        Pin-Priority: 500
   
    次に、"sudo apt-get install linux-image-cloud-amd64" を実行して、新しい Debian Cloud カーネルをインストールします。

9. 仮想マシンをプロビジョニング解除し、Azure でのプロビジョニング用に準備して、次のスクリプトを実行します。
   
        # sudo waagent –force -deprovision
        # export HISTSIZE=0
        # logout

10. Hyper-V マネージャーで **[アクション]、[シャットダウン]** の順にクリックします。 これで、Linux VHD を Azure にアップロードする準備が整いました。

## <a name="next-steps"></a>次の手順
これで、Debian 仮想ハード ディスク を使用して、Azure に新しい仮想マシンを作成する準備が整いました。 .vhd ファイルを Azure に初めてアップロードする場合は、「[Create a Linux VM from a custom disk (カスタム ディスクから Linux VM を作成する)](upload-vhd.md#option-1-upload-a-vhd)」を参照してください。

