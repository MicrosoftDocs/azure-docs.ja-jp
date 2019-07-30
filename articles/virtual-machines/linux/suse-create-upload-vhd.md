---
title: Azure 上での SUSE Linux VHD の作成とアップロード
description: SUSE Linux オペレーティング システムを格納した Azure 仮想ハード ディスク (VHD) を作成してアップロードする方法について説明します。
services: virtual-machines-linux
documentationcenter: ''
author: szarkos
manager: gwallace
editor: tysonn
tags: azure-resource-manager,azure-service-management
ms.assetid: 066d01a6-2a54-4718-bcd0-90fe7a5303a1
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 03/12/2018
ms.author: szark
ms.openlocfilehash: 05731acd5e808075145c50281063e8990129d882
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/09/2019
ms.locfileid: "67708687"
---
# <a name="prepare-a-sles-or-opensuse-virtual-machine-for-azure"></a>Azure 用の SLES または openSUSE 仮想マシンの準備
[!INCLUDE [learn-about-deployment-models](../../../includes/learn-about-deployment-models-both-include.md)]

## <a name="prerequisites"></a>前提条件
この記事では、既に SUSE または openSUSE Linux オペレーティング システムを仮想ハード ディスクにインストールしていることを前提にしています。 .vhd ファイルを作成するツールは、Hyper-V のような仮想化ソリューションなど複数あります。 詳細については、「 [Hyper-V の役割のインストールと仮想マシンの構成](https://technet.microsoft.com/library/hh846766.aspx)」を参照してください。

### <a name="sles--opensuse-installation-notes"></a>SLES/openSUSE のインストールに関する注記
* Azure で Linux を準備する際のその他のヒントについては、「 [Linux のインストールに関する一般的な注記](create-upload-generic.md#general-linux-installation-notes) 」も参照してください。
* VHDX 形式は Azure ではサポートされていません。サポートされるのは **固定 VHD** のみです。  Hyper-V マネージャーまたは convert-vhd コマンドレットを使用して、ディスクを VHD 形式に変換できます。
* Linux システムをインストールする場合は、LVM (通常、多くのインストールで既定) ではなく標準パーティションを使用することをお勧めします。 これにより、特に OS ディスクをトラブルシューティングのために別の VM に接続する必要がある場合に、LVM 名と複製された VM の競合が回避されます。 必要な場合は、[LVM](configure-lvm.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) または [RAID](configure-raid.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) をデータ ディスク上で使用できます。
* OS ディスクにスワップ パーティションを構成しないでください。 Linux エージェントは、一時的なリソース ディスク上にスワップ ファイルを作成するよう構成できます。  このことに関する詳細については、次の手順を参照してください。
* Azure の VHD の仮想サイズはすべて、1 MB にアラインメントさせる必要があります。 未フォーマット ディスクから VHD に変換するときに、変換する前の未フォーマット ディスクのサイズが 1 MB の倍数であることを確認する必要があります。 詳細については、[Linux のインストールに関する注記](create-upload-generic.md#general-linux-installation-notes)のセクションを参照してください。

## <a name="use-suse-studio"></a>SUSE Studio を使用する
[SUSE Studio](http://www.susestudio.com) を使用すると、Azure および Hyper-V 用の SLES イメージと openSUSE イメージを簡単に作成、管理できます。 独自の SLES イメージや openSUSE イメージをカスタマイズする場合はこの方法をお勧めします。

SUSE では、独自の VHD を構築する代わりに、 [VMDepot](https://www.microsoft.com/en-us/research/wp-content/uploads/2016/04/using-and-contributing-vms-to-vm-depot.pdf)に SLES の BYOS (Bring Your Own Subscription) イメージを発行することもできます。

## <a name="prepare-suse-linux-enterprise-server-11-sp4"></a>SUSE Linux Enterprise Server 11 SP4 を準備する
1. Hyper-V マネージャーの中央のウィンドウで仮想マシンを選択します。
2. **[接続]** をクリックすると、仮想マシンのウィンドウが開きます。
3. SUSE Linux Enterprise システムを登録すると、更新プログラムをダウンロードしてパッケージをインストールできるようになります。
4. 最新の更新ログラムでシステムを更新します。
   
        # sudo zypper update
5. SLES リポジトリから Azure Linux エージェントをインストールします。
   
        # sudo zypper install python-azure-agent
6. chkconfig で waagent が "on" に設定されていることを確認し、設定されていない場合は自動起動するために有効にします。
   
        # sudo chkconfig waagent on
7. waagent サービスが実行されているかどうかを確認し、実行されていない場合は開始します。 
   
        # sudo service waagent start
8. GRUB 構成でカーネルのブート行を変更して Azure の追加のカーネル パラメーターを含めます。 これを行うには、テキスト エディターで "/boot/grub/menu.lst" を開き、既定のカーネルに次のパラメーターが含まれていることを確認します。
   
        console=ttyS0 earlyprintk=ttyS0 rootdelay=300
   
    これにより、すべてのコンソール メッセージが最初のシリアル ポートに送信され、メッセージを Azure での問題のデバッグに利用できるようになります。
9. /boot/grub/menu.lst と /etc/fstab の両方で、ディスク ID (by-id) ではなく UUID (by-uuid) を使用してディスクを参照していることを確認します。 
   
    ディスクの UUID を取得します。
   
        # ls /dev/disk/by-uuid/
   
    /dev/disk/by-id/ を使用する場合は、/boot/grub/menu.lst と /etc/fstab の両方を適切な by-uuid 値で更新します。
   
    変更前
   
        root=/dev/disk/by-id/SCSI-xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxxx-part1
   
    変更後
   
        root=/dev/disk/by-uuid/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx
10. udev ルールを編集して、イーサネット インターフェイスの静的ルールが生成されないようにします。 これらのルールは、Microsoft Azure または Hyper-V で仮想マシンを複製する際に問題の原因となる可能性があります。
    
        # sudo ln -s /dev/null /etc/udev/rules.d/75-persistent-net-generator.rules
        # sudo rm -f /etc/udev/rules.d/70-persistent-net.rules
11. "/etc/sysconfig/network/dhcp" ファイルを編集して、次のように `DHCLIENT_SET_HOSTNAME` パラメーターを変更することをお勧めします。
    
     DHCLIENT_SET_HOSTNAME="no"
12. "/etc/sudoers" で、次の行をコメント アウトするか削除する必要があります (ある場合)。
    
     Defaults targetpw   # ask for the password of the target user i.e. root ALL    ALL=(ALL) ALL   # WARNING! Only use this together with 'Defaults targetpw'!
13. SSH サーバーがインストールされており、起動時に開始するように構成されていることを確認します。  通常これが既定です。
14. OS ディスクにスワップ領域を作成しないでください。
    
    Azure Linux エージェントは、Azure でプロビジョニングされた後に VM に接続されたローカルのリソース ディスクを使用してスワップ領域を自動的に構成します。 ローカル リソース ディスクは *一時* ディスクであるため、VM のプロビジョニングが解除されると空になることに注意してください。 Azure Linux エージェントのインストール後に (前の手順を参照)、/etc/waagent.conf にある次のパラメーターを適切に変更します。
    
     ResourceDisk.Format=y  ResourceDisk.Filesystem=ext4  ResourceDisk.MountPoint=/mnt/resource  ResourceDisk.EnableSwap=y  ResourceDisk.SwapSizeMB=2048    ## 注: これは必要に応じて設定してください。
15. 次のコマンドを実行して仮想マシンをプロビジョニング解除し、Azure でのプロビジョニング用に準備します。
    
        # sudo waagent -force -deprovision
        # export HISTSIZE=0
        # logout
16. Hyper-V マネージャーで **[アクション]、[シャットダウン]** の順にクリックします。 これで、Linux VHD を Azure にアップロードする準備が整いました。

---
## <a name="prepare-opensuse-131"></a>openSUSE 13.1 以上の準備
1. Hyper-V マネージャーの中央のウィンドウで仮想マシンを選択します。
2. **[接続]** をクリックすると、仮想マシンのウィンドウが開きます。
3. シェルでコマンド "`zypper lr`" を実行します。 このコマンドから次のような出力が返された場合、リポジトリは予想どおりに構成されているため、調整は不要です (バージョン番号が異なる場合があります)。
   
        # | Alias                 | Name                  | Enabled | Refresh
        --+-----------------------+-----------------------+---------+--------
        1 | Cloud:Tools_13.1      | Cloud:Tools_13.1      | Yes     | Yes
        2 | openSUSE_13.1_OSS     | openSUSE_13.1_OSS     | Yes     | Yes
        3 | openSUSE_13.1_Updates | openSUSE_13.1_Updates | Yes     | Yes
   
    コマンドによって "No repositories defined..." が返された場合は、次のコマンドを実行してこれらのリポジトリを追加します。
   
        # sudo zypper ar -f http://download.opensuse.org/repositories/Cloud:Tools/openSUSE_13.1 Cloud:Tools_13.1
        # sudo zypper ar -f https://download.opensuse.org/distribution/13.1/repo/oss openSUSE_13.1_OSS
        # sudo zypper ar -f http://download.opensuse.org/update/13.1 openSUSE_13.1_Updates
   
    "`zypper lr`" コマンドをもう一度実行してリポジトリが追加されたことを確認できます。 更新したリポジトリのいずれかが有効になっていない場合は、次のコマンドを使用して有効にします。
   
        # sudo zypper mr -e [NUMBER OF REPOSITORY]
4. カーネルを最新のバージョンに更新します。
   
        # sudo zypper up kernel-default
   
    または、次のように、すべての最新のパッチでシステムを更新します。
   
        # sudo zypper update
5. Azure Linux エージェントをインストールします。
   
        # sudo zypper install WALinuxAgent
6. GRUB 構成でカーネルのブート行を変更して Azure の追加のカーネル パラメーターを含めます。 これを行うには、テキスト エディターで "/boot/grub/menu.lst" を開き、既定のカーネルに次のパラメーターが含まれていることを確認します。
   
     console=ttyS0 earlyprintk=ttyS0 rootdelay=300
   
   これにより、すべてのコンソール メッセージが最初のシリアル ポートに送信され、メッセージを Azure での問題のデバッグに利用できるようになります。 また、カーネルのブート行に次のパラメーターがある場合は削除します。
   
     libata.atapi_enabled=0 reserve=0x1f0,0x8
7. "/etc/sysconfig/network/dhcp" ファイルを編集して、次のように `DHCLIENT_SET_HOSTNAME` パラメーターを変更することをお勧めします。
   
     DHCLIENT_SET_HOSTNAME="no"
8. **重要:** "/etc/sudoers" で、次の行をコメント アウトするか削除する必要があります (ある場合)。
   
     Defaults targetpw   # ask for the password of the target user i.e. root ALL    ALL=(ALL) ALL   # WARNING! Only use this together with 'Defaults targetpw'!
9. SSH サーバーがインストールされており、起動時に開始するように構成されていることを確認します。  通常これが既定です。
10. OS ディスクにスワップ領域を作成しないでください。
    
    Azure Linux エージェントは、Azure でプロビジョニングされた後に VM に接続されたローカルのリソース ディスクを使用してスワップ領域を自動的に構成します。 ローカル リソース ディスクは *一時* ディスクであるため、VM のプロビジョニングが解除されると空になることに注意してください。 Azure Linux エージェントのインストール後に (前の手順を参照)、/etc/waagent.conf にある次のパラメーターを適切に変更します。
    
     ResourceDisk.Format=y  ResourceDisk.Filesystem=ext4  ResourceDisk.MountPoint=/mnt/resource  ResourceDisk.EnableSwap=y  ResourceDisk.SwapSizeMB=2048    ## 注: これは必要に応じて設定してください。
11. 次のコマンドを実行して仮想マシンをプロビジョニング解除し、Azure でのプロビジョニング用に準備します。
    
        # sudo waagent -force -deprovision
        # export HISTSIZE=0
        # logout
12. 起動時に Azure Linux エージェントが実行されるようにします。
    
        # sudo systemctl enable waagent.service
13. Hyper-V マネージャーで **[アクション]、[シャットダウン]** の順にクリックします。 これで、Linux VHD を Azure にアップロードする準備が整いました。

## <a name="next-steps"></a>次の手順
これで、SUSE Linux 仮想ハード ディスク を使用して、Azure に新しい仮想マシンを作成する準備が整いました。 .vhd ファイルを Azure に初めてアップロードする場合は、「[Create a Linux VM from a custom disk (カスタム ディスクから Linux VM を作成する)](upload-vhd.md#option-1-upload-a-vhd)」を参照してください。
