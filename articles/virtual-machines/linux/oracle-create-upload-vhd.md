---
title: Oracle Linux VHD の作成とアップロード | Microsoft Docs
description: Oracle Linux オペレーティング システムを格納した Azure 仮想ハード ディスク (VHD) を作成してアップロードする方法について説明します。
services: virtual-machines-linux
documentationcenter: ''
author: szarkos
manager: gwallace
editor: tysonn
tags: azure-service-management,azure-resource-manager
ms.assetid: dd96f771-26eb-4391-9a89-8c8b6d691822
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 03/12/2018
ms.author: szark
ms.openlocfilehash: 0d83a6f9b42a56799371c5cdf82422ab73b8859a
ms.sourcegitcommit: 2e4b99023ecaf2ea3d6d3604da068d04682a8c2d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/09/2019
ms.locfileid: "67671086"
---
# <a name="prepare-an-oracle-linux-virtual-machine-for-azure"></a>Azure 用の Oracle Linux 仮想マシンの準備
[!INCLUDE [learn-about-deployment-models](../../../includes/learn-about-deployment-models-both-include.md)]

## <a name="prerequisites"></a>前提条件
この記事では、既に Oracle Linux オペレーティング システムを仮想ハード ディスクにインストールしていることを前提にしています。 .vhd ファイルを作成するツールは、Hyper-V のような仮想化ソリューションなど複数あります。 詳細については、「 [Hyper-V の役割のインストールと仮想マシンの構成](https://technet.microsoft.com/library/hh846766.aspx)」を参照してください。

### <a name="oracle-linux-installation-notes"></a>Oracle Linux のインストールに関する注記
* Azure で Linux を準備する際のその他のヒントについては、「 [Linux のインストールに関する注記](create-upload-generic.md#general-linux-installation-notes) 」も参照してください。
* Oracle の Red Hat 互換カーネルとその UEK3 (Unbreakable Enterprise Kernel) は、両方とも Hyper-V と Azure でサポートされています。 最良の結果を得るために、Oracle Linux VHD を準備する際に、最新のカーネルに更新してください。
* Oracle の UEK2 は必要なドライバーを含んでいないため、Hyper-V と Azure ではサポートされていません。
* VHDX 形式は Azure ではサポートされていません。サポートされるのは **固定 VHD** のみです。  Hyper-V マネージャーまたは convert-vhd コマンドレットを使用して、ディスクを VHD 形式に変換できます。
* Linux システムをインストールする場合は、LVM (通常、多くのインストールで既定) ではなく標準パーティションを使用することをお勧めします。 これにより、特に OS ディスクをトラブルシューティングのために別の VM に接続する必要がある場合に、LVM 名と複製された VM の競合が回避されます。 必要な場合は、[LVM](configure-lvm.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) または [RAID](configure-raid.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) をデータ ディスク上で使用できます。
* さらに大きいサイズの VM では NUMA はサポートされていません。2.6.37 以下のバージョンの Linux カーネルにバグがあるためです。 この問題は、主に、アップストリームの Red Hat 2.6.32 カーネルを使用したディストリビューションに影響します。 Azure Linux エージェント (waagent) を手動でインストールすると、Linux カーネルの GRUB 構成で NUMA が自動的に無効になります。 このことに関する詳細については、次の手順を参照してください。
* OS ディスクにスワップ パーティションを構成しないでください。 Linux エージェントは、一時的なリソース ディスク上にスワップ ファイルを作成するよう構成できます。  このことに関する詳細については、次の手順を参照してください。
* Azure の VHD の仮想サイズはすべて、1 MB にアラインメントさせる必要があります。 未フォーマット ディスクから VHD に変換するときに、変換する前の未フォーマット ディスクのサイズが 1 MB の倍数であることを確認する必要があります。 詳細については、[Linux のインストールに関する注記](create-upload-generic.md#general-linux-installation-notes)のセクションを参照してください。
* `Addons` リポジトリが有効になっていることを確認します。 `/etc/yum.repos.d/public-yum-ol6.repo` ファイル (Oracle Linux 6) または `/etc/yum.repos.d/public-yum-ol7.repo` ファイル (Oracle Linux 7) を編集し、このファイルの **[ol6_addons]** または **[ol7_addons]** の下の行 `enabled=0` を `enabled=1` に変更します。

## <a name="oracle-linux-64"></a>Oracle Linux 6.4+
Azure 上で実行する仮想マシンのオペレーティング システムで固有の構成手順を完了する必要があります。

1. Hyper-V マネージャーの中央のウィンドウで仮想マシンを選択します。
2. **[接続]** をクリックすると、仮想マシンのウィンドウが開きます。
3. 次のコマンドを実行して NetworkManager をアンインストールします。
   
        # sudo rpm -e --nodeps NetworkManager
   
    **注:** パッケージがまだインストールされていない場合、このコマンドは失敗してエラー メッセージが表示されます。 これは予期されることです。
4. `/etc/sysconfig/` ディレクトリに **network** という名前のファイルを作成し、次のテキストを追加します。
   
        NETWORKING=yes
        HOSTNAME=localhost.localdomain
5. `/etc/sysconfig/network-scripts/` ディレクトリに **ifcfg-eth0** という名前のファイルを作成し、次のテキストを追加します。
   
        DEVICE=eth0
        ONBOOT=yes
        BOOTPROTO=dhcp
        TYPE=Ethernet
        USERCTL=no
        PEERDNS=yes
        IPV6INIT=no
6. udev ルールを編集して、イーサネット インターフェイスの静的ルールが生成されないようにします。 これらのルールは、Microsoft Azure または Hyper-V で仮想マシンを複製する際に問題の原因となる可能性があります。
   
        # sudo ln -s /dev/null /etc/udev/rules.d/75-persistent-net-generator.rules
        # sudo rm -f /etc/udev/rules.d/70-persistent-net.rules
7. 次のコマンドを実行して、起動時にネットワーク サービスが開始されるようにします。
   
        # chkconfig network on
8. 次のコマンドを実行して python-pyasn1 をインストールします。
   
        # sudo yum install python-pyasn1
9. GRUB 構成でカーネルのブート行を変更して Azure の追加のカーネル パラメーターを含めます。 これを行うには、テキスト エディターで "/boot/grub/menu.lst" を開き、既定のカーネルに次のパラメーターが含まれていることを確認します。
   
        console=ttyS0 earlyprintk=ttyS0 rootdelay=300 numa=off
   
   これにより、すべてのコンソール メッセージが最初のシリアル ポートに送信され、メッセージを Azure での問題のデバッグに利用できるようになります。 これにより、Oracle の Red Hat 互換カーネルのバグが原因で NUMA が無効になります。
   
   上記のほかに、次のパラメーターを *削除* することをお勧めします。
   
        rhgb quiet crashkernel=auto
   
   クラウド環境では、すべてのログをシリアル ポートに送信するため、グラフィカル ブートおよびクワイエット ブートは役立ちません。
   
   必要に応じて `crashkernel` オプションは構成したままにすることができますが、このパラメーターにより、VM 内の使用可能なメモリ量が 128 MB 以上減少するため、比較的小さなサイズの VM では問題となる可能性がある点に注意してください。
10. SSH サーバーがインストールされており、起動時に開始するように構成されていることを確認します。  通常これが既定です。
11. 次のコマンドを実行して Azure Linux エージェントをインストールします。 最新バージョンは 2.0.15 です。
    
        # sudo yum install WALinuxAgent
    
    手順 2. で説明したように NetworkManager パッケージおよび NetworkManager-gnome パッケージがまだ削除されていない場合、WALinuxAgent パッケージをインストールすると、これらのパッケージが削除されることに注意してください。
12. OS ディスクにスワップ領域を作成しないでください。
    
    Azure Linux エージェントは、Azure でプロビジョニングされた後に VM に接続されたローカルのリソース ディスクを使用してスワップ領域を自動的に構成します。 ローカル リソース ディスクは *一時* ディスクであるため、VM のプロビジョニングが解除されると空になることに注意してください。 Azure Linux エージェントのインストール後に (前の手順を参照)、/etc/waagent.conf にある次のパラメーターを適切に変更します。
    
        ResourceDisk.Format=y
        ResourceDisk.Filesystem=ext4
        ResourceDisk.MountPoint=/mnt/resource
        ResourceDisk.EnableSwap=y
        ResourceDisk.SwapSizeMB=2048    ## NOTE: set this to whatever you need it to be.
13. 次のコマンドを実行して仮想マシンをプロビジョニング解除し、Azure でのプロビジョニング用に準備します。
    
        # sudo waagent -force -deprovision
        # export HISTSIZE=0
        # logout
14. Hyper-V マネージャーで **[アクション]、[シャットダウン]** の順にクリックします。 これで、Linux VHD を Azure にアップロードする準備が整いました。

---
## <a name="oracle-linux-70"></a>Oracle Linux 7.0 以上
**Oracle Linux 7 での変更**

Azure 用の Oracle Linux 7 仮想マシンを準備する手順は、Oracle Linux 6 の場合とほとんど同じですが、次のように、重要な違いがいくつかあります。

* Red Hat 互換カーネルと Oracle の UEK3 は、両方とも Azure でサポートされています。  UEK3 カーネルをお勧めします。
* NetworkManager パッケージが、Azure Linux エージェントと競合しなくなりました。 このパッケージは既定でインストールされます。このパッケージを削除しないことをお勧めします。
* GRUB2 が、既定のブートローダーとして使用されるようになったため、カーネル パラメーターの編集手順が変更されました (以下を参照)。
* XFS が既定のファイル システムになりました。 必要に応じて、引き続き ext4 ファイル システムを使用できます。

**構成の手順**

1. Hyper-V マネージャーで仮想マシンを選択します。
2. **[接続]** をクリックすると、仮想マシンのコンソール ウィンドウが開きます。
3. `/etc/sysconfig/` ディレクトリに **network** という名前のファイルを作成し、次のテキストを追加します。
   
        NETWORKING=yes
        HOSTNAME=localhost.localdomain
4. `/etc/sysconfig/network-scripts/` ディレクトリに **ifcfg-eth0** という名前のファイルを作成し、次のテキストを追加します。
   
        DEVICE=eth0
        ONBOOT=yes
        BOOTPROTO=dhcp
        TYPE=Ethernet
        USERCTL=no
        PEERDNS=yes
        IPV6INIT=no
5. udev ルールを編集して、イーサネット インターフェイスの静的ルールが生成されないようにします。 これらのルールは、Microsoft Azure または Hyper-V で仮想マシンを複製する際に問題の原因となる可能性があります。
   
        # sudo ln -s /dev/null /etc/udev/rules.d/75-persistent-net-generator.rules
6. 次のコマンドを実行して、起動時にネットワーク サービスが開始されるようにします。
   
        # sudo chkconfig network on
7. 次のコマンドを実行して python-pyasn1 パッケージをインストールします。
   
        # sudo yum install python-pyasn1
8. 次のコマンドを実行して、現在の yum メタデータをクリアし、更新をインストールします。
   
        # sudo yum clean all
        # sudo yum -y update
9. GRUB 構成でカーネルのブート行を変更して Azure の追加のカーネル パラメーターを含めます。 これを行うには、テキスト エディターで "/etc/default/grub" を開き、次のように、 `GRUB_CMDLINE_LINUX` パラメーターを編集します。
   
        GRUB_CMDLINE_LINUX="rootdelay=300 console=ttyS0 earlyprintk=ttyS0 net.ifnames=0"
   
   これにより、すべてのコンソール メッセージが最初のシリアル ポートに送信され、メッセージを Azure での問題のデバッグに利用できるようになります。 NIC の新しい OEL 7 名前付け規則もオフになります。 上記のほかに、次のパラメーターを *削除* することをお勧めします。
   
       rhgb quiet crashkernel=auto
   
   クラウド環境では、すべてのログをシリアル ポートに送信するため、グラフィカル ブートおよびクワイエット ブートは役立ちません。
   
   必要に応じて `crashkernel` オプションは構成したままにすることができますが、このパラメーターにより、VM 内の使用可能なメモリ量が 128 MB 以上減少するため、比較的小さなサイズの VM では問題となる可能性がある点に注意してください。
10. 上記のとおりに "/etc/default/grub" の編集を終了したら、次のコマンドを実行して GRUB 構成をリビルドします。
    
        # sudo grub2-mkconfig -o /boot/grub2/grub.cfg
11. SSH サーバーがインストールされており、起動時に開始するように構成されていることを確認します。  通常これが既定です。
12. 次のコマンドを実行して Azure Linux エージェントをインストールします。
    
        # sudo yum install WALinuxAgent
        # sudo systemctl enable waagent
13. OS ディスクにスワップ領域を作成しないでください。
    
    Azure Linux エージェントは、Azure でプロビジョニングされた後に VM に接続されたローカルのリソース ディスクを使用してスワップ領域を自動的に構成します。 ローカル リソース ディスクは *一時* ディスクであるため、VM のプロビジョニングが解除されると空になることに注意してください。 Azure Linux エージェントのインストール後に (前の手順を参照)、/etc/waagent.conf にある次のパラメーターを適切に変更します。
    
        ResourceDisk.Format=y
        ResourceDisk.Filesystem=ext4
        ResourceDisk.MountPoint=/mnt/resource
        ResourceDisk.EnableSwap=y
        ResourceDisk.SwapSizeMB=2048    ## NOTE: set this to whatever you need it to be.
14. 次のコマンドを実行して仮想マシンをプロビジョニング解除し、Azure でのプロビジョニング用に準備します。
    
        # sudo waagent -force -deprovision
        # export HISTSIZE=0
        # logout
15. Hyper-V マネージャーで **[アクション]、[シャットダウン]** の順にクリックします。 これで、Linux VHD を Azure にアップロードする準備が整いました。

## <a name="next-steps"></a>次の手順
これで、Oracle Linux .vhd を使用して、Azure に新しい仮想マシンを作成する準備が整いました。 .vhd ファイルを Azure に初めてアップロードする場合は、「[Create a Linux VM from a custom disk (カスタム ディスクから Linux VM を作成する)](upload-vhd.md#option-1-upload-a-vhd)」を参照してください。

