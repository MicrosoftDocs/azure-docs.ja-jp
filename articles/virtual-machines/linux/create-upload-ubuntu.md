---
title: Azure 上での Ubuntu Linux VHD の作成とアップロード
description: Ubuntu Linux オペレーティング システムを格納した Azure 仮想ハード ディスク (VHD) を作成してアップロードする方法について説明します。
author: danielsollondon
ms.service: virtual-machines
ms.collection: linux
ms.topic: how-to
ms.date: 06/06/2020
ms.author: danis
ms.openlocfilehash: 92ceecd16a428593764fe5ab6478cc4ea7ab91d7
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102554617"
---
# <a name="prepare-an-ubuntu-virtual-machine-for-azure"></a>Azure 用の Ubuntu 仮想マシンの準備


Ubuntu は、現在、公式の Azure VHD を公開しており、[https://cloud-images.ubuntu.com/](https://cloud-images.ubuntu.com/) でダウンロードできます。 Azure 用に特殊な Ubuntu イメージを独自に構築する必要がある場合は、以下の手動の手順を使用するのではなく、このように動作している既知の VHD を基にして、必要に応じてカスタマイズすることをお勧めします。 最新のイメージ リリースは、常に次の場所にあります。

* Ubuntu 16.04/Xenial: [ubuntu-16.04-server-cloudimg-amd64-disk1.vmdk](https://cloud-images.ubuntu.com/releases/xenial/release/ubuntu-16.04-server-cloudimg-amd64-disk1.vmdk)
* Ubuntu 18.04/Bionic: [bionic-server-cloudimg-amd64.vmdk](https://cloud-images.ubuntu.com/bionic/current/bionic-server-cloudimg-amd64.vmdk)

## <a name="prerequisites"></a>前提条件
この記事では、既に Ubuntu Linux オペレーティング システムを仮想ハード ディスクにインストールしていることを前提にしています。 .vhd ファイルを作成するツールは、Hyper-V のような仮想化ソリューションなど複数あります。 詳細については、「 [Hyper-V の役割のインストールと仮想マシンの構成](/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/hh846766(v=ws.11))」を参照してください。

**Ubuntu のインストールに関する注記**

* Azure で Linux を準備する際のその他のヒントについては、「 [Linux のインストールに関する注記](create-upload-generic.md#general-linux-installation-notes) 」も参照してください。
* VHDX 形式は Azure ではサポートされていません。サポートされるのは **固定 VHD** のみです。  Hyper-V マネージャーまたは `Convert-VHD` コマンドレットを使用して、ディスクを VHD 形式に変換できます。
* Linux システムをインストールする場合は、LVM (通常、多くのインストールで既定) ではなく標準パーティションを使用することをお勧めします。 これにより、特に OS ディスクをトラブルシューティングのために別の VM に接続する必要がある場合に、LVM 名と複製された VM の競合が回避されます。 必要な場合は、[LVM](/previous-versions/azure/virtual-machines/linux/configure-lvm) または [RAID](/previous-versions/azure/virtual-machines/linux/configure-raid) をデータ ディスク上で使用できます。
* OS ディスクにスワップ パーティションまたはスワップ ファイルを構成しないでください。 cloud-init プロビジョニング エージェントを構成して、一時リソース ディスク上にスワップ ファイルまたはスワップ パーティションを作成できます。 このことに関する詳細については、次の手順を参照してください。
* Azure の VHD の仮想サイズはすべて、1 MB にアラインメントさせる必要があります。 未フォーマット ディスクから VHD に変換するときに、変換する前の未フォーマット ディスクのサイズが 1 MB の倍数であることを確認する必要があります。 詳細については、[Linux のインストールに関する注記](create-upload-generic.md#general-linux-installation-notes)のセクションを参照してください。

## <a name="manual-steps"></a>手動の手順
> [!NOTE]
> Azure 用のカスタム Ubuntu イメージを独自に作成する前に、[https://cloud-images.ubuntu.com/](https://cloud-images.ubuntu.com/) にある構築およびテスト済みのイメージを使用することを検討してください。
> 
> 

1. Hyper-V マネージャーの中央のウィンドウで仮想マシンを選択します。

2. **[接続]** をクリックすると、仮想マシンのウィンドウが開きます。

3. イメージ内の現在のリポジトリを置き換えて、Ubuntu の Azure リポジトリを使用します。

    `/etc/apt/sources.list` を編集する前にバックアップを作成することをお勧めします。

    ```console
    # sudo cp /etc/apt/sources.list /etc/apt/sources.list.bak
    ```

    Ubuntu 16.04 および Ubuntu 18.04:

    ```console
    # sudo sed -i 's/http:\/\/archive\.ubuntu\.com\/ubuntu\//http:\/\/azure\.archive\.ubuntu\.com\/ubuntu\//g' /etc/apt/sources.list
    # sudo sed -i 's/http:\/\/[a-z][a-z]\.archive\.ubuntu\.com\/ubuntu\//http:\/\/azure\.archive\.ubuntu\.com\/ubuntu\//g' /etc/apt/sources.list
    # sudo apt-get update
    ```

4. Ubuntu Azure イメージでは、[Azure 向けに調整されたカーネル](https://ubuntu.com/blog/microsoft-and-canonical-increase-velocity-with-azure-tailored-kernel)が使用されるようになりました。 次のコマンドを実行して、オペレーティング システムを Azure 向けに調整された最新カーネルに更新し、Azure Linux ツール (Hyper-V の依存関係を含む) をインストールします。

    Ubuntu 16.04 および Ubuntu 18.04:

    ```console
    # sudo apt update
    # sudo apt install linux-azure linux-image-azure linux-headers-azure linux-tools-common linux-cloud-tools-common linux-tools-azure linux-cloud-tools-azure
    (recommended) # sudo apt full-upgrade

    # sudo reboot
    ```

5. Grub のカーネルのブート行を変更して Azure の追加のカーネル パラメーターを含めます。 これを行うには、テキスト エディターで `/etc/default/grub` を開き、`GRUB_CMDLINE_LINUX_DEFAULT` という変数を探して (または、必要であれば追加して)、次のパラメーターが含まれるように編集します。

    ```text
    GRUB_CMDLINE_LINUX_DEFAULT="console=tty1 console=ttyS0,115200n8 earlyprintk=ttyS0,115200 rootdelay=300 quiet splash"
    ```

    このファイルを保存して閉じてから、`sudo update-grub` を実行します。 これにより、すべてのコンソール メッセージが最初のシリアル ポートに送信され、メッセージを Azure での問題のデバッグに利用できるようになります。

6. SSH サーバーがインストールされており、起動時に開始するように構成されていることを確認します。  通常これが既定です。

7. cloud-init (プロビジョニング エージェント) と Azure Linux エージェント (ゲスト拡張機能ハンドラー) をインストールします。 cloud-init では、netplan を使用して、プロビジョニングおよびその後の各ブート中にシステム ネットワークが構成されます。

    ```console
    # sudo apt update
    # sudo apt install cloud-init netplan.io walinuxagent && systemctl stop walinuxagent
    ```

   > [!Note]
   >  `NetworkManager` パッケージおよび `NetworkManager-gnome` パッケージがインストールされている場合、`walinuxagent` パッケージによってこれらのパッケージが削除されます。

8. Azure での cloud-init のプロビジョニングと競合する可能性のある、cloud-init の既定の構成と残った netplan 成果物を削除します。

    ```console
    # rm -f /etc/cloud/cloud.cfg.d/50-curtin-networking.cfg /etc/cloud/cloud.cfg.d/curtin-preserve-sources.cfg
    # rm -f /etc/cloud/ds-identify.cfg
    # rm -f /etc/netplan/*.yaml
    ```

9. cloud-init を構成して、Azure データソースを使用してシステムをプロビジョニングするようにします。

    ```console
    # cat > /etc/cloud/cloud.cfg.d/90_dpkg.cfg << EOF
    datasource_list: [ Azure ]
    EOF

    # cat > /etc/cloud/cloud.cfg.d/90-azure.cfg << EOF
    system_info:
       package_mirrors:
         - arches: [i386, amd64]
           failsafe:
             primary: http://archive.ubuntu.com/ubuntu
             security: http://security.ubuntu.com/ubuntu
           search:
             primary:
               - http://azure.archive.ubuntu.com/ubuntu/
             security: []
         - arches: [armhf, armel, default]
           failsafe:
             primary: http://ports.ubuntu.com/ubuntu-ports
             security: http://ports.ubuntu.com/ubuntu-ports
    EOF

    # cat > /etc/cloud/cloud.cfg.d/10-azure-kvp.cfg << EOF
    reporting:
      logging:
        type: log
      telemetry:
        type: hyperv
    EOF
    ```

10. Azure Linux エージェントを構成して、プロビジョニングを実行するために cloud-init に依存するようにします。 これらのオプションの詳細については、[WALinuxAgent プロジェクト](https://github.com/Azure/WALinuxAgent)を参照してください。

    ```console
    sed -i 's/Provisioning.Enabled=y/Provisioning.Enabled=n/g' /etc/waagent.conf
    sed -i 's/Provisioning.UseCloudInit=n/Provisioning.UseCloudInit=y/g' /etc/waagent.conf
    sed -i 's/ResourceDisk.Format=y/ResourceDisk.Format=n/g' /etc/waagent.conf
    sed -i 's/ResourceDisk.EnableSwap=y/ResourceDisk.EnableSwap=n/g' /etc/waagent.conf

    cat >> /etc/waagent.conf << EOF
    # For Azure Linux agent version >= 2.2.45, this is the option to configure,
    # enable, or disable the provisioning behavior of the Linux agent.
    # Accepted values are auto (default), waagent, cloud-init, or disabled.
    # A value of auto means that the agent will rely on cloud-init to handle
    # provisioning if it is installed and enabled, which in this case it will.
    Provisioning.Agent=auto
    EOF
    ```

11. cloud-init と Azure Linux エージェントのランタイム成果物およびログを消去します。

    ```console
    # sudo cloud-init clean --logs --seed
    # sudo rm -rf /var/lib/cloud/
    # sudo systemctl stop walinuxagent.service
    # sudo rm -rf /var/lib/waagent/
    # sudo rm -f /var/log/waagent.log
    ```

12. 次のコマンドを実行して仮想マシンをプロビジョニング解除し、Azure でのプロビジョニング用に準備します。

    > [!NOTE]
    > `sudo waagent -force -deprovision+user` コマンドは、システムをクリーンアップし、再プロビジョニングに適した状態にしようとします。 `+user` オプションを指定すると、前回プロビジョニングされたユーザー アカウントおよび関連付けられたデータも削除されます。

    > [!WARNING]
    > 上記のコマンドを使用したプロビジョニング解除により、イメージからすべての機密情報が削除され、イメージが再配布に適した状態になることが保証されるわけではありません。

    ```console
    # sudo waagent -force -deprovision+user
    # rm -f ~/.bash_history
    # export HISTSIZE=0
    # logout
    ```

13. Hyper-V マネージャーで **[アクション]、[シャットダウン]** の順にクリックします。

14. Azure では、固定サイズの VHD のみが許容されます。 VM の OS ディスクが固定サイズの VHD でない場合は、`Convert-VHD` PowerShell コマンドレットを使用して `-VHDType Fixed` オプションを指定します。 `Convert-VHD` については、次のドキュメントを参照してください:「[Convert-VHD](/powershell/module/hyper-v/convert-vhd)」。


## <a name="next-steps"></a>次のステップ
これで、Ubuntu Linux 仮想ハード ディスク を使用して、Azure に新しい仮想マシンを作成する準備が整いました。 .vhd ファイルを Azure に初めてアップロードする場合は、「[Create a Linux VM from a custom disk (カスタム ディスクから Linux VM を作成する)](upload-vhd.md#option-1-upload-a-vhd)」を参照してください。