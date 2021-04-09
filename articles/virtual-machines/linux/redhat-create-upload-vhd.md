---
title: Azure で使用するための Red Hat Enterprise Linux VHD の作成とアップロード
description: Red Hat Linux オペレーティング システムを格納した Azure 仮想ハード ディスク (VHD) を作成してアップロードする方法について説明します。
author: danielsollondon
ms.service: virtual-machines
ms.subservice: redhat
ms.collection: linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.topic: how-to
ms.date: 12/01/2020
ms.author: danis
ms.openlocfilehash: ff22010f12a7d76e60b629c4c1c4e24946d4af5a
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102552968"
---
# <a name="prepare-a-red-hat-based-virtual-machine-for-azure"></a>Azure 用の Red Hat ベースの仮想マシンの準備

この記事では、Red Hat Enterprise Linux (RHEL) の仮想マシンを Azure で使用できるように準備する方法について説明します。 この記事で取り上げる RHEL のバージョンは 6.7+ と 7.1+ で、 準備対象のハイパーバイザーは Hyper-V、Kernel-based Virtual Machine (KVM)、VMware です。 Red Hat の Cloud Access プログラムに参加するための資格要件の詳細については、[Red Hat の Cloud Access Web サイト](https://www.redhat.com/en/technologies/cloud-computing/cloud-access)と [Azure での RHEL の実行](https://access.redhat.com/ecosystem/ccsp/microsoft-azure)に関するページを参照してください。 RHEL イメージの作成を自動化する方法については、[Azure Image Builder](../image-builder-overview.md) に関するページを参照してください。

## <a name="hyper-v-manager"></a>Hyper-V マネージャーは

このセクションでは、Hyper-V マネージャーを使用して、[RHEL 6](#rhel-6-using-hyper-v-manager)、[RHEL 7](#rhel-7-using-hyper-v-manager)、または [RHEL 8](#rhel-8-using-hyper-v-manager) 仮想マシンを準備する方法について説明します。

### <a name="prerequisites"></a>前提条件
このセクションは、Red Hat の Web サイトから取得した ISO ファイルの RHEL イメージが仮想ハード ディスク (VHD) にインストール済みであることを前提としています。 Hyper-V マネージャーを使用してオペレーティング システム イメージをインストールする方法の詳細については、[Hyper-V の役割のインストールと仮想マシンの構成](/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/hh846766(v=ws.11))に関するページを参照してください。

**RHEL のインストールに関する注記**

* Azure では、VHDX 形式がサポートされません。 Azure でサポートされるのは、容量固定の VHD のみです。 Hyper-V マネージャーを使ってディスクの形式を VHD に変換するか、または convert-vhd コマンドレットを使用してください。 VirtualBox を使用する場合は、ディスクの作成時に、既定の動的割り当てオプションではなく、 **[容量固定]** を選択します。
* Azure では、Gen1 (BIOS ブート) および Gen2 (UEFI ブート) 仮想マシンがサポートされています。
* VHD のサイズの上限は、1,023 GB です。
* Logical Volume Manager (LVM) がサポートされており、Azure 仮想マシンの OS ディスクやデータ ディスクに使用できます。 ただし、一般に、LVM ではなく OS ディスクの標準パーティションを使用することをお勧めします。 特にオペレーティング システム ディスクをトラブルシューティングのために別の同じ仮想マシンに接続する必要がある場合、そうすることで、複製された仮想マシンとの LVM 名の競合を回避することができます。 [LVM](/previous-versions/azure/virtual-machines/linux/configure-lvm) および [RAID](/previous-versions/azure/virtual-machines/linux/configure-raid) のドキュメントもご覧ください。
* **ユニバーサル ディスク フォーマット (UDF) ファイル システムをマウントするためのカーネル サポートが必要です**。 Azure での最初の起動時に、ゲストに接続されている UDF でフォーマットされたメディアを介して、プロビジョニング構成が Linux 仮想マシンに渡されます。 Azure Linux エージェントは、その構成を読み取り、仮想マシンをプロビジョニングする UDF ファイル システムをマウントできる必要があります。これがないと、プロビジョニングは失敗します。
* オペレーティング システム ディスクでスワップ パーティションを構成しないでください。 このことに関する詳細については、次の手順を参照してください。

* Azure の VHD の仮想サイズはすべて、1 MB にアラインメントさせる必要があります。 未フォーマット ディスクから VHD に変換するときに、変換する前の未フォーマット ディスクのサイズが 1 MB の倍数であることを確認する必要があります。 詳細については、後述の手順を参照してください。 また、[Linux のインストールに関する注記](create-upload-generic.md#general-linux-installation-notes)のセクションも参照してください。

### <a name="rhel-6-using-hyper-v-manager"></a>Hyper-V マネージャーを使用した RHEL 6

1. Hyper-V マネージャーで仮想マシンを選択します。

1. **[接続]** をクリックすると、仮想マシンのコンソール ウィンドウが開きます。

1. RHEL 6 では、NetworkManager が Azure Linux エージェントと干渉する可能性があります。 次のコマンドを実行して、このパッケージをアンインストールします。

    ```console
    # sudo rpm -e --nodeps NetworkManager
    ```

1. `/etc/sysconfig/network` ファイルを作成または編集して次のテキストを追加します。

    ```config
    NETWORKING=yes
    HOSTNAME=localhost.localdomain
    ```

1. `/etc/sysconfig/network-scripts/ifcfg-eth0` ファイルを作成または編集して次のテキストを追加します。

    ```config
    DEVICE=eth0
    ONBOOT=yes
    BOOTPROTO=dhcp
    TYPE=Ethernet
    USERCTL=no
    PEERDNS=yes
    IPV6INIT=no
    ```

1. udev ルールを移動 (または削除) して、イーサネット インターフェイスの静的ルールが生成されないようにします。 これらのルールは、Microsoft Azure または Hyper-V で仮想マシンを複製する際に問題の原因となります。

    ```console
    # sudo ln -s /dev/null /etc/udev/rules.d/75-persistent-net-generator.rules

    # sudo rm -f /etc/udev/rules.d/70-persistent-net.rules
    ```

1. 次のコマンドを実行して、起動時にネットワーク サービスが開始されるようにします。

    ```console
    # sudo chkconfig network on
    ```

1. RHEL リポジトリからパッケージをインストールできるように、次のコマンドを実行して Red Hat のサブスクリプションを登録します。

    ```console
    # sudo subscription-manager register --auto-attach --username=XXX --password=XXX
    ```

1. WALinuxAgent パッケージ `WALinuxAgent-<version>` が Red Hat extras リポジトリにプッシュされました。 次のコマンドを実行して extras リポジトリを有効にします。

    ```console
    # subscription-manager repos --enable=rhel-6-server-extras-rpms
    ```

1. GRUB 構成でカーネルのブート行を変更して Azure の追加のカーネル パラメーターを含めます。 この変更を行うには、テキスト エディターで `/boot/grub/menu.lst` を開き、既定のカーネルに次のパラメーターが含まれていることを確認します。

    ```config-grub
    console=ttyS0 earlyprintk=ttyS0 rootdelay=300
    ```

    これにより、すべてのコンソール メッセージが最初のシリアル ポートに送信され、メッセージを Azure での問題のデバッグに利用できるようになります。
    
    上記の他に、次のパラメーターを削除することをお勧めします。

    ```config-grub
    rhgb quiet crashkernel=auto
    ```
    
    クラウド環境では、すべてのログをシリアル ポートに送信するため、グラフィカル ブートおよびクワイエット ブートは役立ちません。  `crashkernel` オプションの構成は、必要であればそのままにしてかまいません。 ただし、このパラメーターにより、仮想マシン内の使用可能なメモリ量が 128 MB 以上減少します。 仮想マシンのサイズが小さいと、この構成が問題となる可能性があります。


1. SSH (Secure Shell) サーバーがインストールされており、起動時に開始するように構成されていることを確認します。通常は、既定でそのように構成されています。 /etc/ssh/sshd_config を変更して、次の行を含めます。

    ```config
    ClientAliveInterval 180
    ```

1. 次のコマンドを実行して Azure Linux エージェントをインストールします。

    ```console
    # sudo yum install WALinuxAgent

    # sudo chkconfig waagent on
    ```

    手順 3. で NetworkManager パッケージと NetworkManager-gnome パッケージを削除しなかった場合は、WALinuxAgent パッケージをインストールすると、これらのパッケージが削除されます。

1. オペレーティング システム ディスクにスワップ領域を作成しないでください。

    Azure Linux エージェントは、Azure で仮想マシンがプロビジョニングされた後に仮想マシンに接続されたローカルのリソース ディスクを使用してスワップ領域を自動的に構成できます。 ローカル リソース ディスクは一時ディスクであるため、仮想マシンのプロビジョニングが解除されると空になる場合があることにご注意ください。 前の手順で Azure Linux エージェントのインストール後に、/etc/waagent.conf にある次のパラメーターを適切に変更します。

    ```config-cong
    ResourceDisk.Format=y
    ResourceDisk.Filesystem=ext4
    ResourceDisk.MountPoint=/mnt/resource
    ResourceDisk.EnableSwap=y
    ResourceDisk.SwapSizeMB=2048    ## NOTE: set this to whatever you need it to be.
    ```

1. 次のコマンドを実行して、サブスクリプションを登録解除します (必要な場合)。

    ```console
    # sudo subscription-manager unregister
    ```

1. 次のコマンドを実行して仮想マシンをプロビジョニング解除し、Azure でのプロビジョニング用に準備します。

    ```console
    # Note: if you are migrating a specific virtual machine and do not wish to create a generalized image,
    # skip the deprovision step
    # sudo waagent -force -deprovision

    # export HISTSIZE=0

    # logout
    ```

1. Hyper-V マネージャーで **[アクション]**  >  **[シャットダウン]** の順にクリックします。 これで、Linux VHD を Azure にアップロードする準備が整いました。


### <a name="rhel-7-using-hyper-v-manager"></a>Hyper-V マネージャーを使用した RHEL 7

1. Hyper-V マネージャーで仮想マシンを選択します。

1. **[接続]** をクリックすると、仮想マシンのコンソール ウィンドウが開きます。

1. `/etc/sysconfig/network` ファイルを作成または編集して次のテキストを追加します。

    ```config
    NETWORKING=yes
    HOSTNAME=localhost.localdomain
    ```

1. `/etc/sysconfig/network-scripts/ifcfg-eth0` ファイルを作成または編集して次のテキストを追加します。

    ```config
    DEVICE=eth0
    ONBOOT=yes
    BOOTPROTO=dhcp
    TYPE=Ethernet
    USERCTL=no
    PEERDNS=yes
    IPV6INIT=no
    PERSISTENT_DHCLIENT=yes
    NM_CONTROLLED=yes
    ```

1. 次のコマンドを実行して、起動時にネットワーク サービスが開始されるようにします。

    ```console
    # sudo systemctl enable network
    ```

1. RHEL リポジトリからパッケージをインストールできるように、次のコマンドを実行して Red Hat のサブスクリプションを登録します。

    ```console
    # sudo subscription-manager register --auto-attach --username=XXX --password=XXX
    ```

1. GRUB 構成でカーネルのブート行を変更して Azure の追加のカーネル パラメーターを含めます。 この変更を行うには、テキスト エディターで `/etc/default/grub` を開き、`GRUB_CMDLINE_LINUX` パラメーターを編集します。 次に例を示します。

    
    ```config-grub
    GRUB_CMDLINE_LINUX="rootdelay=300 console=tty1 console=ttyS0,115200n8 earlyprintk=ttyS0,115200 earlyprintk=ttyS0 net.ifnames=0"
    GRUB_TERMINAL_OUTPUT="serial console"
    GRUB_SERIAL_COMMAND="serial --speed=115200 --unit=0 --word=8 --parity=no --stop=1
    ```
   
    これにより、すべてのコンソール メッセージが最初のシリアル ポートに送信され、シリアル コンソールとの対話が可能になります。こうすることで、Azure での問題のデバッグが支援されます。 NIC の新しい RHEL 7 名前付け規則もオフになります。

    ```config
    rhgb quiet crashkernel=auto
    ```
   
    クラウド環境では、すべてのログをシリアル ポートに送信するため、グラフィカル ブートおよびクワイエット ブートは役立ちません。 `crashkernel` オプションの構成は、必要であればそのままにしてかまいません。 ただし、このパラメーターにより、仮想マシン内の使用可能なメモリ量が 128 MB 以上減少します。仮想マシンのサイズが小さいと、このことが問題になる可能性があります。

1. `/etc/default/grub`の編集を終了したら、次のコマンドを実行して GRUB 構成を再構築します。

    ```console
    # sudo grub2-mkconfig -o /boot/grub2/grub.cfg
    ```
    > [!NOTE]
    > UEFI 対応 VM をアップロードする場合、GRUB を更新するコマンドは `grub2-mkconfig -o /boot/efi/EFI/redhat/grub.cfg` です。

1. SSH サーバーがインストールされており、起動時に開始するように構成されていることを確認します。通常は、既定でそのように構成されています。 `/etc/ssh/sshd_config` を変更して、次の行を含めます。

    ```config
    ClientAliveInterval 180
    ```

1. WALinuxAgent パッケージ `WALinuxAgent-<version>` が Red Hat extras リポジトリにプッシュされました。 次のコマンドを実行して extras リポジトリを有効にします。

    ```console
    # subscription-manager repos --enable=rhel-7-server-extras-rpms
    ```

1. 次のコマンドを実行して、Azure Linux エージェント、cloud-init、その他の必要なユーティリティをインストールします。

    ```console
    # sudo yum install -y WALinuxAgent cloud-init cloud-utils-growpart gdisk hyperv-daemons

    # sudo systemctl enable waagent.service
    # sudo systemctl enable cloud-init.service
    ```

1. プロビジョニングを処理するように cloud-init を構成します。

    1. cloud-init に対して waagent を構成します。

    ```console
    sed -i 's/Provisioning.Agent=auto/Provisioning.Agent=cloud-init/g' /etc/waagent.conf
    sed -i 's/ResourceDisk.Format=y/ResourceDisk.Format=n/g' /etc/waagent.conf
    sed -i 's/ResourceDisk.EnableSwap=y/ResourceDisk.EnableSwap=n/g' /etc/waagent.conf
    ```
    > [!NOTE]
    > 特定の仮想マシンを移行する際に、一般化されたイメージを作成しない場合は、`/etc/waagent.conf` 構成で `Provisioning.Agent=disabled` を設定します。
    
    1. マウントを構成します。

    ```console
    echo "Adding mounts and disk_setup to init stage"
    sed -i '/ - mounts/d' /etc/cloud/cloud.cfg
    sed -i '/ - disk_setup/d' /etc/cloud/cloud.cfg
    sed -i '/cloud_init_modules/a\\ - mounts' /etc/cloud/cloud.cfg
    sed -i '/cloud_init_modules/a\\ - disk_setup' /etc/cloud/cloud.cfg
    ```
    
    1. Azure データソースを構成します。

    ```console
    echo "Allow only Azure datasource, disable fetching network setting via IMDS"
    cat > /etc/cloud/cloud.cfg.d/91-azure_datasource.cfg <<EOF
    datasource_list: [ Azure ]
    datasource:
        Azure:
            apply_network_config: False
    EOF
    ```

    1. 既存の swapfile が構成されている場合は削除します。

    ```console
    if [[ -f /mnt/resource/swapfile ]]; then
    echo "Removing swapfile" #RHEL uses a swapfile by defaul
    swapoff /mnt/resource/swapfile
    rm /mnt/resource/swapfile -f
    fi
    ```
    1. cloud-init のログを構成します。
    ```console
    echo "Add console log file"
    cat >> /etc/cloud/cloud.cfg.d/05_logging.cfg <<EOF

    # This tells cloud-init to redirect its stdout and stderr to
    # 'tee -a /var/log/cloud-init-output.log' so the user can see output
    # there without needing to look on the console.
    output: {all: '| tee -a /var/log/cloud-init-output.log'}
    EOF

    ```

1. スワップの構成: オペレーティング システム ディスクにスワップ領域を作成しないでください。

    以前は、Azure で仮想マシンがプロビジョニングされた後に、仮想マシンに接続されたローカル リソース ディスクを使用してスワップ領域を自動的に構成するために Azure Linux エージェントが使用されていました。 しかし、これは cloud-init によって処理されるようになったので、Linux エージェントを使用して、スワップ ファイルを作成するリソース ディスクをフォーマット **しないでください**。`/etc/waagent.conf` で次のパラメーターを適切に変更します。

    ```console
    ResourceDisk.Format=n
    ResourceDisk.EnableSwap=n
    ```

    スワップをマウント、フォーマット、作成する場合は、次のいずれかの方法を使用できます。
    * VM を作成するたびに、cloud-init 構成としてこれを渡す
    * VM が作成されるたびにこれを実行する、イメージに組み込まれている cloud-init ディレクティブを使用する

        ```console
        cat > /etc/cloud/cloud.cfg.d/00-azure-swap.cfg << EOF
        #cloud-config
        # Generated by Azure cloud image build
        disk_setup:
          ephemeral0:
            table_type: mbr
            layout: [66, [33, 82]]
            overwrite: True
        fs_setup:
          - device: ephemeral0.1
            filesystem: ext4
          - device: ephemeral0.2
            filesystem: swap
        mounts:
          - ["ephemeral0.1", "/mnt"]
          - ["ephemeral0.2", "none", "swap", "sw", "0", "0"]
        EOF
        ```
1. サブスクリプションを登録解除する場合は、次のコマンドを実行します。

    ```console
    # sudo subscription-manager unregister
    ```

1. プロビジョニング解除

    次のコマンドを実行して仮想マシンをプロビジョニング解除し、Azure でのプロビジョニング用に準備します。

    > [!CAUTION]
    > 特定の仮想マシンを移行する際に、一般化されたイメージを作成しない場合は、プロビジョニング解除手順はスキップしてください。 コマンド `waagent -force -deprovision` を実行すると、ソース マシンが使用できなくなります。この手順は一般化されたイメージを作成することのみを目的としています。
    ```console
    # sudo waagent -force -deprovision

    # export HISTSIZE=0

    # logout
    ```
    

1. Hyper-V マネージャーで **[アクション]**  >  **[シャットダウン]** の順にクリックします。 これで、Linux VHD を Azure にアップロードする準備が整いました。

### <a name="rhel-8-using-hyper-v-manager"></a>Hyper-V マネージャーを使用した RHEL 8

1. Hyper-V マネージャーで仮想マシンを選択します。

1. **[接続]** をクリックすると、仮想マシンのコンソール ウィンドウが開きます。

1. 次のコマンドを実行して、起動時にネットワーク マネージャー サービスが開始されるようにします。

    ```console
    # sudo systemctl enable NetworkManager.service
    ```

1. ネットワーク インターフェイスが起動時に自動的に開始し、DHCP を使用するように構成します。

    ```console
    # nmcli con mod eth0 connection.autoconnect yes ipv4.method auto
    ```


1. RHEL リポジトリからパッケージをインストールできるように、次のコマンドを実行して Red Hat のサブスクリプションを登録します。

    ```console
    # sudo subscription-manager register --auto-attach --username=XXX --password=XXX
    ```

1. GRUB 構成でカーネルのブート行を変更して Azure の追加のカーネル パラメーターを含め、シリアル コンソールを有効にします。 

    1. 現在の GRUB パラメーターを削除します。
    ```console
    # grub2-editenv - unset kernelopts
    ```

    1. テキスト エディターで `/etc/default/grub` を編集し、次のパラメーターを追加します。

    ```config-grub
    GRUB_CMDLINE_LINUX="rootdelay=300 console=tty1 console=ttyS0,115200n8 earlyprintk=ttyS0,115200 earlyprintk=ttyS0 net.ifnames=0"
    GRUB_TERMINAL_OUTPUT="serial console"
    GRUB_SERIAL_COMMAND="serial --speed=115200 --unit=0 --word=8 --parity=no --stop=1"
    ```
   
   これにより、すべてのコンソール メッセージが最初のシリアル ポートに送信され、シリアル コンソールとの対話が可能になります。こうすることで、Azure での問題のデバッグが支援されます。 NIC の新しい名前付け規則もオフになります。
   
   1. また、次のパラメーターを削除することをお勧めします。

    ```config
    rhgb quiet crashkernel=auto
    ```
   
    クラウド環境では、すべてのログをシリアル ポートに送信するため、グラフィカル ブートおよびクワイエット ブートは役立ちません。 `crashkernel` オプションの構成は、必要であればそのままにしてかまいません。 ただし、このパラメーターにより、仮想マシン内の使用可能なメモリ量が 128 MB 以上減少します。仮想マシンのサイズが小さいと、このことが問題になる可能性があります。

1. `/etc/default/grub`の編集を終了したら、次のコマンドを実行して GRUB 構成を再構築します。

    ```console
    # sudo grub2-mkconfig -o /boot/grub2/grub.cfg
    ```
    UEFI 対応 VM の場合は、次のコマンドを実行します。

    ```console
    # sudo grub2-mkconfig -o /boot/efi/EFI/redhat/grub.cfg
    ```

1. SSH サーバーがインストールされており、起動時に開始するように構成されていることを確認します。通常は、既定でそのように構成されています。 `/etc/ssh/sshd_config` を変更して、次の行を含めます。

    ```config
    ClientAliveInterval 180
    ```

1. 次のコマンドを実行して、Azure Linux エージェント、cloud-init、その他の必要なユーティリティをインストールします。

    ```console
    # sudo yum install -y WALinuxAgent cloud-init cloud-utils-growpart gdisk hyperv-daemons

    # sudo systemctl enable waagent.service
    # sudo systemctl enable cloud-init.service
    ```

1. プロビジョニングを処理するように cloud-init を構成します。

    1. cloud-init に対して waagent を構成します。

    ```console
    sed -i 's/Provisioning.Agent=auto/Provisioning.Agent=cloud-init/g' /etc/waagent.conf
    sed -i 's/ResourceDisk.Format=y/ResourceDisk.Format=n/g' /etc/waagent.conf
    sed -i 's/ResourceDisk.EnableSwap=y/ResourceDisk.EnableSwap=n/g' /etc/waagent.conf
    ```
    > [!NOTE]
    > 特定の仮想マシンを移行する際に、一般化されたイメージを作成しない場合は、`/etc/waagent.conf` 構成で `Provisioning.Agent=disabled` を設定します。
    
    1. マウントを構成します。

    ```console
    echo "Adding mounts and disk_setup to init stage"
    sed -i '/ - mounts/d' /etc/cloud/cloud.cfg
    sed -i '/ - disk_setup/d' /etc/cloud/cloud.cfg
    sed -i '/cloud_init_modules/a\\ - mounts' /etc/cloud/cloud.cfg
    sed -i '/cloud_init_modules/a\\ - disk_setup' /etc/cloud/cloud.cfg
    ```
    
    1. Azure データソースを構成します。

    ```console
    echo "Allow only Azure datasource, disable fetching network setting via IMDS"
    cat > /etc/cloud/cloud.cfg.d/91-azure_datasource.cfg <<EOF
    datasource_list: [ Azure ]
    datasource:
        Azure:
            apply_network_config: False
    EOF
    ```

    1. 既存の swapfile が構成されている場合は削除します。

    ```console
    if [[ -f /mnt/resource/swapfile ]]; then
    echo "Removing swapfile" #RHEL uses a swapfile by defaul
    swapoff /mnt/resource/swapfile
    rm /mnt/resource/swapfile -f
    fi
    ```
    1. cloud-init のログを構成します。
    ```console
    echo "Add console log file"
    cat >> /etc/cloud/cloud.cfg.d/05_logging.cfg <<EOF

    # This tells cloud-init to redirect its stdout and stderr to
    # 'tee -a /var/log/cloud-init-output.log' so the user can see output
    # there without needing to look on the console.
    output: {all: '| tee -a /var/log/cloud-init-output.log'}
    EOF

    ```

1. スワップの構成: オペレーティング システム ディスクにスワップ領域を作成しないでください。

    以前は、Azure で仮想マシンがプロビジョニングされた後に、仮想マシンに接続されたローカル リソース ディスクを使用してスワップ領域を自動的に構成するために Azure Linux エージェントが使用されていました。 しかし、これは cloud-init によって処理されるようになったので、Linux エージェントを使用して、スワップ ファイルを作成するリソース ディスクをフォーマット **しないでください**。`/etc/waagent.conf` で次のパラメーターを適切に変更します。

    ```console
    ResourceDisk.Format=n
    ResourceDisk.EnableSwap=n
    ```

    スワップをマウント、フォーマット、作成する場合は、次のいずれかの方法を使用できます。
    * VM を作成するたびに、cloud-init 構成としてこれを渡す
    * VM が作成されるたびにこれを実行する、イメージに組み込まれている cloud-init ディレクティブを使用する

        ```console
        cat > /etc/cloud/cloud.cfg.d/00-azure-swap.cfg << EOF
        #cloud-config
        # Generated by Azure cloud image build
        disk_setup:
          ephemeral0:
            table_type: mbr
            layout: [66, [33, 82]]
            overwrite: True
        fs_setup:
          - device: ephemeral0.1
            filesystem: ext4
          - device: ephemeral0.2
            filesystem: swap
        mounts:
          - ["ephemeral0.1", "/mnt"]
          - ["ephemeral0.2", "none", "swap", "sw", "0", "0"]
        EOF
        ```
1. サブスクリプションを登録解除する場合は、次のコマンドを実行します。

    ```console
    # sudo subscription-manager unregister
    ```

1. プロビジョニング解除

    次のコマンドを実行して仮想マシンをプロビジョニング解除し、Azure でのプロビジョニング用に準備します。

    ```console
    # sudo waagent -force -deprovision

    # export HISTSIZE=0

    # logout
    ```
    > [!CAUTION]
    > 特定の仮想マシンを移行する際に、一般化されたイメージを作成しない場合は、プロビジョニング解除手順はスキップしてください。 コマンド `waagent -force -deprovision` を実行すると、ソース マシンが使用できなくなります。この手順は一般化されたイメージを作成することのみを目的としています。


1. Hyper-V マネージャーで **[アクション]**  >  **[シャットダウン]** の順にクリックします。 これで、Linux VHD を Azure にアップロードする準備が整いました。


## <a name="kvm"></a>KVM

このセクションでは、Azure にアップロードするために、KVM を使用して [RHEL 6](#rhel-6-using-kvm) または [RHEL 7](#rhel-7-using-kvm) ディストリビューションを準備する方法について説明します。 

### <a name="rhel-6-using-kvm"></a>KVM を使用した RHEL 6

1. Red Hat の Web サイトから、RHEL 6 の KVM イメージをダウンロードします。

1. ルート パスワードを設定します。

    暗号化されたパスワードを生成し、コマンドの出力をコピーします。

    ```console
    # openssl passwd -1 changeme
    ```

    guestfish でルート パスワードを設定します。

    ```console
    # guestfish --rw -a <image-name>
    > <fs> run
    > <fs> list-filesystems
    > <fs> mount /dev/sda1 /
    > <fs> vi /etc/shadow
    > <fs> exit
    ```

   ルート ユーザーの 2 番目のフィールドを、"!!" から 暗号化されたパスワードに変更します。

1. qcow2 イメージから KVM の仮想マシンを作成します。 ディスクの種類を **qcow2** に設定して、仮想ネットワーク インターフェイスのデバイス モデルを **virtio** に設定します。 その後、仮想マシンを起動し、root としてサインインします。

1. `/etc/sysconfig/network` ファイルを作成または編集して次のテキストを追加します。

    ```config
    NETWORKING=yes
    HOSTNAME=localhost.localdomain
    ```

1. `/etc/sysconfig/network-scripts/ifcfg-eth0` ファイルを作成または編集して次のテキストを追加します。

    ```config
    DEVICE=eth0
    ONBOOT=yes
    BOOTPROTO=dhcp
    TYPE=Ethernet
    USERCTL=no
    PEERDNS=yes
    IPV6INIT=no
    ```

1. udev ルールを移動 (または削除) して、イーサネット インターフェイスの静的ルールが生成されないようにします。 これらのルールは、Azure または Hyper-V で仮想マシンを複製する際に問題の原因となります。

    ```console
    # sudo ln -s /dev/null /etc/udev/rules.d/75-persistent-net-generator.rules

    # sudo rm -f /etc/udev/rules.d/70-persistent-net.rules
    ```

1. 次のコマンドを実行して、起動時にネットワーク サービスが開始されるようにします。

    ```console
    # chkconfig network on
    ```

1. RHEL リポジトリからパッケージをインストールできるように、次のコマンドを実行して Red Hat のサブスクリプションを登録します。

    ```console
    # subscription-manager register --auto-attach --username=XXX --password=XXX
    ```

1. GRUB 構成でカーネルのブート行を変更して Azure の追加のカーネル パラメーターを含めます。 この構成を行うには、テキスト エディターで `/boot/grub/menu.lst` を開き、既定のカーネルに次のパラメーターが含まれていることを確認します。

    ```config-grub
    console=ttyS0 earlyprintk=ttyS0 rootdelay=300
    ```

    これにより、すべてのコンソール メッセージが最初のシリアル ポートに送信され、メッセージを Azure での問題のデバッグに利用できるようになります。
    
    上記の他に、次のパラメーターを削除することをお勧めします。

    ```config-grub
    rhgb quiet crashkernel=auto
    ```

    クラウド環境では、すべてのログをシリアル ポートに送信するため、グラフィカル ブートおよびクワイエット ブートは役立ちません。 `crashkernel` オプションの構成は、必要であればそのままにしてかまいません。 ただし、このパラメーターにより、仮想マシン内の使用可能なメモリ量が 128 MB 以上減少します。仮想マシンのサイズが小さいと、このことが問題になる可能性があります。


1. Hyper-V モジュールを initramfs に追加します。  

    `/etc/dracut.conf` を編集し、次の内容を追加します。

    ```config-conf
    add_drivers+=" hv_vmbus hv_netvsc hv_storvsc "
    ```

    initramfs を再構築します。

    ```config-conf
    # dracut -f -v
    ```

1. cloud-init をアンインストールします。

    ```console
    # yum remove cloud-init
    ```

1. SSH サーバーがインストールされており、起動時に開始するように構成されていることを確認します。

    ```console
    # chkconfig sshd on
    ```

    /etc/ssh/sshd_config を変更して、次の行を含めます。

    ```config
    PasswordAuthentication yes
    ClientAliveInterval 180
    ```

1. WALinuxAgent パッケージ `WALinuxAgent-<version>` が Red Hat extras リポジトリにプッシュされました。 次のコマンドを実行して extras リポジトリを有効にします。

    ```console
    # subscription-manager repos --enable=rhel-6-server-extras-rpms
    ```

1. 次のコマンドを実行して Azure Linux エージェントをインストールします。

    ```console
    # yum install WALinuxAgent

    # chkconfig waagent on
    ```

1. Azure Linux エージェントは、Azure で仮想マシンがプロビジョニングされた後に仮想マシンに接続されたローカルのリソース ディスクを使用してスワップ領域を自動的に構成できます。 ローカル リソース ディスクは一時ディスクであるため、仮想マシンのプロビジョニングが解除されると空になる場合があることにご注意ください。 前の手順で Azure Linux エージェントのインストール後に、 **/etc/waagent.conf** にある次のパラメーターを適切に変更します。

    ```config-conf
    ResourceDisk.Format=y
    ResourceDisk.Filesystem=ext4
    ResourceDisk.MountPoint=/mnt/resource
    ResourceDisk.EnableSwap=y
    ResourceDisk.SwapSizeMB=2048    ## NOTE: set this to whatever you need it to be.
    ```

1. 次のコマンドを実行して、サブスクリプションを登録解除します (必要な場合)。

    ```console-conf
    # subscription-manager unregister
    ```

1. 次のコマンドを実行して仮想マシンをプロビジョニング解除し、Azure でのプロビジョニング用に準備します。

    ```console
    # Note: if you are migrating a specific virtual machine and do not wish to create a generalized image,
    # skip the deprovision step
    # sudo rm -rf /var/lib/waagent/
    # sudo rm -f /var/log/waagent.log

    # waagent -force -deprovision+user
    # rm -f ~/.bash_history
    

    # export HISTSIZE=0

    # logout
    ```

1. KVM で仮想マシンをシャットダウンします。

1. qcow2 イメージを VHD 形式に変換します。

    > [!NOTE]
    > qemu-img のバージョン 2.2.1 以降には VHD が適切にフォーマットされないというバグがあることがわかっています。 この問題は QEMU 2.6 で修正されています。 qemu-img 2.2.0 以前を使用するか、2.6 以降に更新することをお勧めします。 [https://bugs.launchpad.net/qemu/+bug/1490611](https://bugs.launchpad.net/qemu/+bug/1490611 ) を参照してください。
    >

    まず、イメージを未加工の形式に変換します。

    ```console
    # qemu-img convert -f qcow2 -O raw rhel-6.9.qcow2 rhel-6.9.raw
    ```

    未加工のイメージのサイズが 1 MB になっていることを確認します。 そうでない場合は、1 MB になるようにサイズの端数を切り上げます。

    ```console
    # MB=$((1024*1024))
    # size=$(qemu-img info -f raw --output json "rhel-6.9.raw" | \
      gawk 'match($0, /"virtual-size": ([0-9]+),/, val) {print val[1]}')

    # rounded_size=$((($size/$MB + 1)*$MB))
    # qemu-img resize rhel-6.9.raw $rounded_size
    ```

    未フォーマット ディスクを固定サイズの VHD に変換します。

    ```console
    # qemu-img convert -f raw -o subformat=fixed -O vpc rhel-6.9.raw rhel-6.9.vhd
    ```

    または、qemu のバージョン **2.6 以降** を使用して `force_size` オプションを含めます。

    ```console
    # qemu-img convert -f raw -o subformat=fixed,force_size -O vpc rhel-6.9.raw rhel-6.9.vhd
    ```

        
### <a name="rhel-7-using-kvm"></a>KVM を使用した RHEL 7

1. Red Hat の Web サイトから、RHEL 7 の KVM イメージをダウンロードします。 この手順では、例として RHEL 7 を使用します。

1. ルート パスワードを設定します。

    暗号化されたパスワードを生成し、コマンドの出力をコピーします。

    ```console
    # openssl passwd -1 changeme
    ```

    guestfish でルート パスワードを設定します。

    ```console
    # guestfish --rw -a <image-name>
    > <fs> run
    > <fs> list-filesystems
    > <fs> mount /dev/sda1 /
    > <fs> vi /etc/shadow
    > <fs> exit
    ```

   ルート ユーザーの 2 番目のフィールドを、"!!" から 暗号化されたパスワードに変更します。

1. qcow2 イメージから KVM の仮想マシンを作成します。 ディスクの種類を **qcow2** に設定して、仮想ネットワーク インターフェイスのデバイス モデルを **virtio** に設定します。 その後、仮想マシンを起動し、root としてサインインします。

1. `/etc/sysconfig/network` ファイルを作成または編集して次のテキストを追加します。

    ```config
    NETWORKING=yes
    HOSTNAME=localhost.localdomain
    ```

1. `/etc/sysconfig/network-scripts/ifcfg-eth0` ファイルを作成または編集して次のテキストを追加します。

    ```config
    DEVICE=eth0
    ONBOOT=yes
    BOOTPROTO=dhcp
    TYPE=Ethernet
    USERCTL=no
    PEERDNS=yes
    IPV6INIT=no
    PERSISTENT_DHCLIENT=yes
    NM_CONTROLLED=yes
    ```

1. 次のコマンドを実行して、起動時にネットワーク サービスが開始されるようにします。

    ```console
    # sudo systemctl enable network
    ```

1. RHEL リポジトリからパッケージをインストールできるように、次のコマンドを実行して Red Hat のサブスクリプションを登録します。

    ```console
    # subscription-manager register --auto-attach --username=XXX --password=XXX
    ```

1. GRUB 構成でカーネルのブート行を変更して Azure の追加のカーネル パラメーターを含めます。 この構成を行うには、テキスト エディターで `/etc/default/grub` を開き、`GRUB_CMDLINE_LINUX` パラメーターを編集します。 次に例を示します。

    ```config-grub
    GRUB_CMDLINE_LINUX="rootdelay=300 console=ttyS0 earlyprintk=ttyS0 net.ifnames=0"
    ```

   このコマンドにより、すべてのコンソール メッセージが最初のシリアル ポートに送信され、メッセージを Azure での問題のデバッグに利用できるようになります。 NIC の新しい RHEL 7 名前付け規則も、このコマンドでオフになります。 上記の他に、次のパラメーターを削除することをお勧めします。

    ```config-grub
    rhgb quiet crashkernel=auto
    ```

    クラウド環境では、すべてのログをシリアル ポートに送信するため、グラフィカル ブートおよびクワイエット ブートは役立ちません。 `crashkernel` オプションの構成は、必要であればそのままにしてかまいません。 ただし、このパラメーターにより、仮想マシン内の使用可能なメモリ量が 128 MB 以上減少します。仮想マシンのサイズが小さいと、このことが問題になる可能性があります。

1. `/etc/default/grub`の編集を終了したら、次のコマンドを実行して GRUB 構成を再構築します。

    ```console
    # grub2-mkconfig -o /boot/grub2/grub.cfg
    ```

1. Hyper-V モジュールを initramfs に追加します。

    `/etc/dracut.conf` を編集して、コンテンツを追加します。

    ```config-conf
    add_drivers+=" hv_vmbus hv_netvsc hv_storvsc "
    ```

    initramfs を再構築します。

    ```config-conf
    # dracut -f -v
    ```

1. cloud-init をアンインストールします。

    ```console
    # yum remove cloud-init
    ```

1. SSH サーバーがインストールされており、起動時に開始するように構成されていることを確認します。

    ```console
    # systemctl enable sshd
    ```

    /etc/ssh/sshd_config を変更して、次の行を含めます。

    ```config
    PasswordAuthentication yes
    ClientAliveInterval 180
    ```

1. WALinuxAgent パッケージ `WALinuxAgent-<version>` が Red Hat extras リポジトリにプッシュされました。 次のコマンドを実行して extras リポジトリを有効にします。

    ```config
    # subscription-manager repos --enable=rhel-7-server-extras-rpms
    ```

1. 次のコマンドを実行して Azure Linux エージェントをインストールします。

    ```console
    # yum install WALinuxAgent
    ```

    waagent サービスを有効にします。

    ```console
    # systemctl enable waagent.service
    ```

1. cloud-init のインストール: Hyper-V マネージャーからの RHEL 7 仮想マシンの準備に関するセクションの手順 12.「プロビジョニングを処理する cloud-init をインストールします」に従います。

1. スワップの構成 

    オペレーティング システム ディスクにスワップ領域を作成しないでください。
    Hyper-V マネージャーからの RHEL 7 仮想マシンの準備に関するセクションの手順 13.「スワップの構成」に従います。


1. 次のコマンドを実行して、サブスクリプションを登録解除します (必要な場合)。

    ```console
    # subscription-manager unregister
    ```


1. プロビジョニング解除

    Hyper-V マネージャーからの RHEL 7 仮想マシンの準備に関するセクションの手順 15.「プロビジョニング解除」に従います。

1. KVM で仮想マシンをシャットダウンします。

1. qcow2 イメージを VHD 形式に変換します。

    > [!NOTE]
    > qemu-img のバージョン 2.2.1 以降には VHD が適切にフォーマットされないというバグがあることがわかっています。 この問題は QEMU 2.6 で修正されています。 qemu-img 2.2.0 以前を使用するか、2.6 以降に更新することをお勧めします。 [https://bugs.launchpad.net/qemu/+bug/1490611](https://bugs.launchpad.net/qemu/+bug/1490611 ) を参照してください。
    >

    まず、イメージを未加工の形式に変換します。

    ```console
    # qemu-img convert -f qcow2 -O raw rhel-7.4.qcow2 rhel-7.4.raw
    ```

    未加工のイメージのサイズが 1 MB になっていることを確認します。 そうでない場合は、1 MB になるようにサイズの端数を切り上げます。

    ```console
    # MB=$((1024*1024))
    # size=$(qemu-img info -f raw --output json "rhel-7.4.raw" | \
      gawk 'match($0, /"virtual-size": ([0-9]+),/, val) {print val[1]}')

    # rounded_size=$((($size/$MB + 1)*$MB))
    # qemu-img resize rhel-7.4.raw $rounded_size
    ```

    未フォーマット ディスクを固定サイズの VHD に変換します。

    ```console
    # qemu-img convert -f raw -o subformat=fixed -O vpc rhel-7.4.raw rhel-7.4.vhd
    ```

    または、qemu のバージョン **2.6 以降** を使用して `force_size` オプションを含めます。

    ```console
    # qemu-img convert -f raw -o subformat=fixed,force_size -O vpc rhel-7.4.raw rhel-7.4.vhd
    ```

## <a name="vmware"></a>VMware

このセクションでは、VMware から [RHEL 6](#rhel-6-using-vmware) または [RHEL 7](#rhel-6-using-vmware) ディストリビューションを準備する方法について説明します。

### <a name="prerequisites"></a>前提条件
このセクションでは、VMware に RHEL の仮想マシンが既にインストールされていると仮定します。 VMware にオペレーティング システムをインストールする方法の詳細については、[VMware のゲスト オペレーティング システムのインストール ガイド](https://partnerweb.vmware.com/GOSIG/home.html)を参照してください。

* Linux オペレーティング システムをインストールする場合は、LVM (通常、多くのインストールで既定) ではなく標準パーティションを使用することをお勧めします。 特にオペレーティング システム ディスクをトラブルシューティングのために別の仮想マシンに接続する必要がある場合、そうすることで、複製された仮想マシンとの LVM 名の競合を回避することができます。 必要な場合は、LVM または RAID をデータ ディスク上で使用できます。
* オペレーティング システム ディスクでスワップ パーティションを構成しないでください。 一時的なリソース ディスク上にスワップ ファイルを作成するよう Linux エージェントを構成できます。 この詳細については、次の手順を参照してください。
* 仮想ハード ディスクを作成する場合は、 **[Store virtual disk as a single file (仮想ディスクを 1 つのファイルとして格納する)]** を選択します。

### <a name="rhel-6-using-vmware"></a>VMware を使用した RHEL 6
1. RHEL 6 では、NetworkManager が Azure Linux エージェントと干渉する可能性があります。 次のコマンドを実行して、このパッケージをアンインストールします。

    ```console
    # sudo rpm -e --nodeps NetworkManager
    ```

1. /etc/sysconfig/ ディレクトリに **network** という名前のファイルを作成し、次のテキストを追加します。

    ```console
    NETWORKING=yes
    HOSTNAME=localhost.localdomain
    ```

1. `/etc/sysconfig/network-scripts/ifcfg-eth0` ファイルを作成または編集して次のテキストを追加します。

    ```config
    DEVICE=eth0
    ONBOOT=yes
    BOOTPROTO=dhcp
    TYPE=Ethernet
    USERCTL=no
    PEERDNS=yes
    IPV6INIT=no
    ```

1. udev ルールを移動 (または削除) して、イーサネット インターフェイスの静的ルールが生成されないようにします。 これらのルールは、Azure または Hyper-V で仮想マシンを複製する際に問題の原因となります。

    ```console
    # sudo ln -s /dev/null /etc/udev/rules.d/75-persistent-net-generator.rules

    # sudo rm -f /etc/udev/rules.d/70-persistent-net.rules
    ```

1. 次のコマンドを実行して、起動時にネットワーク サービスが開始されるようにします。

    ```console
    # sudo chkconfig network on
    ```

1. RHEL リポジトリからパッケージをインストールできるように、次のコマンドを実行して Red Hat のサブスクリプションを登録します。

    ```console
    # sudo subscription-manager register --auto-attach --username=XXX --password=XXX
    ```

1. WALinuxAgent パッケージ `WALinuxAgent-<version>` が Red Hat extras リポジトリにプッシュされました。 次のコマンドを実行して extras リポジトリを有効にします。

    ```console
    # subscription-manager repos --enable=rhel-6-server-extras-rpms
    ```

1. GRUB 構成でカーネルのブート行を変更して Azure の追加のカーネル パラメーターを含めます。 これを行うには、テキスト エディターで `/etc/default/grub` を開き、`GRUB_CMDLINE_LINUX` パラメーターを編集します。 次に例を示します。

    ```config-grub
    GRUB_CMDLINE_LINUX="rootdelay=300 console=ttyS0 earlyprintk=ttyS0"
    ```

   これにより、すべてのコンソール メッセージが最初のシリアル ポートに送信され、メッセージを Azure での問題のデバッグに利用できるようになります。 上記の他に、次のパラメーターを削除することをお勧めします。

    ```config-grub
    rhgb quiet crashkernel=auto
    ```
   
    クラウド環境では、すべてのログをシリアル ポートに送信するため、グラフィカル ブートおよびクワイエット ブートは役立ちません。 `crashkernel` オプションの構成は、必要であればそのままにしてかまいません。 ただし、このパラメーターにより、仮想マシン内の使用可能なメモリ量が 128 MB 以上減少します。仮想マシンのサイズが小さいと、このことが問題になる可能性があります。

1. Hyper-V モジュールを initramfs に追加します。

    `/etc/dracut.conf` を編集し、次の内容を追加します。

    ```config-conf
    add_drivers+=" hv_vmbus hv_netvsc hv_storvsc "
    ```

    initramfs を再構築します。

    ```config-cong
    # dracut -f -v
    ```

1. SSH サーバーがインストールされており、起動時に開始するように構成されていることを確認します。通常は、既定でそのように構成されています。 `/etc/ssh/sshd_config` を変更して、次の行を含めます。

    ```config
    ClientAliveInterval 180
    ```

1. 次のコマンドを実行して Azure Linux エージェントをインストールします。

    ```console
    # sudo yum install WALinuxAgent

    # sudo chkconfig waagent on
    ```

1. オペレーティング システム ディスクにスワップ領域を作成しないでください。

    Azure Linux エージェントは、Azure で仮想マシンがプロビジョニングされた後に仮想マシンに接続されたローカルのリソース ディスクを使用してスワップ領域を自動的に構成できます。 ローカル リソース ディスクは一時ディスクであるため、仮想マシンのプロビジョニングが解除されると空になる場合があることにご注意ください。 前の手順で Azure Linux エージェントのインストール後に、`/etc/waagent.conf` にある次のパラメーターを適切に変更します。

    ```config-conf
    ResourceDisk.Format=y
    ResourceDisk.Filesystem=ext4
    ResourceDisk.MountPoint=/mnt/resource
    ResourceDisk.EnableSwap=y
    ResourceDisk.SwapSizeMB=2048    ## NOTE: set this to whatever you need it to be.
    ```

1. 次のコマンドを実行して、サブスクリプションを登録解除します (必要な場合)。

    ```console
    # sudo subscription-manager unregister
    ```

1. 次のコマンドを実行して仮想マシンをプロビジョニング解除し、Azure でのプロビジョニング用に準備します。

    ```console
    # Note: if you are migrating a specific virtual machine and do not wish to create a generalized image,
    # skip the deprovision step
    # sudo rm -rf /var/lib/waagent/
    # sudo rm -f /var/log/waagent.log

    # waagent -force -deprovision+user
    # rm -f ~/.bash_history
    

    # export HISTSIZE=0

    # logout
    ```

1. 仮想マシンをシャットダウンし、VMDK ファイルを .vhd ファイルに変換します。

    > [!NOTE]
    > qemu-img のバージョン 2.2.1 以降には VHD が適切にフォーマットされないというバグがあることがわかっています。 この問題は QEMU 2.6 で修正されています。 qemu-img 2.2.0 以前を使用するか、2.6 以降に更新することをお勧めします。 [https://bugs.launchpad.net/qemu/+bug/1490611](https://bugs.launchpad.net/qemu/+bug/1490611 ) を参照してください。
    >

    まず、イメージを未加工の形式に変換します。

    ```console
    # qemu-img convert -f vmdk -O raw rhel-6.9.vmdk rhel-6.9.raw
    ```

    未加工のイメージのサイズが 1 MB になっていることを確認します。 そうでない場合は、1 MB になるようにサイズの端数を切り上げます。

    ```console
    # MB=$((1024*1024))
    # size=$(qemu-img info -f raw --output json "rhel-6.9.raw" | \
      gawk 'match($0, /"virtual-size": ([0-9]+),/, val) {print val[1]}')

    # rounded_size=$((($size/$MB + 1)*$MB))
    # qemu-img resize rhel-6.9.raw $rounded_size
    ```

    未フォーマット ディスクを固定サイズの VHD に変換します。

    ```console
    # qemu-img convert -f raw -o subformat=fixed -O vpc rhel-6.9.raw rhel-6.9.vhd
    ```

    または、qemu のバージョン **2.6 以降** を使用して `force_size` オプションを含めます。

    ```console
    # qemu-img convert -f raw -o subformat=fixed,force_size -O vpc rhel-6.9.raw rhel-6.9.vhd
    ```

### <a name="rhel-7-using-vmware"></a>VMware を使用した RHEL 7
1. `/etc/sysconfig/network` ファイルを作成または編集して次のテキストを追加します。

    ```config
    NETWORKING=yes
    HOSTNAME=localhost.localdomain
    ```

1. `/etc/sysconfig/network-scripts/ifcfg-eth0` ファイルを作成または編集して次のテキストを追加します。

    ```config
    DEVICE=eth0
    ONBOOT=yes
    BOOTPROTO=dhcp
    TYPE=Ethernet
    USERCTL=no
    PEERDNS=yes
    IPV6INIT=no
    PERSISTENT_DHCLIENT=yes
    NM_CONTROLLED=yes
    ```

1. 次のコマンドを実行して、起動時にネットワーク サービスが開始されるようにします。

    ```console
    # sudo systemctl enable network
    ```

1. RHEL リポジトリからパッケージをインストールできるように、次のコマンドを実行して Red Hat のサブスクリプションを登録します。

    ```console
    # sudo subscription-manager register --auto-attach --username=XXX --password=XXX
    ```

1. GRUB 構成でカーネルのブート行を変更して Azure の追加のカーネル パラメーターを含めます。 この変更を行うには、テキスト エディターで `/etc/default/grub` を開き、`GRUB_CMDLINE_LINUX` パラメーターを編集します。 次に例を示します。

    ```config-grub
    GRUB_CMDLINE_LINUX="rootdelay=300 console=ttyS0 earlyprintk=ttyS0 net.ifnames=0"
    ```

   この構成により、すべてのコンソール メッセージが最初のシリアル ポートに送信され、メッセージを Azure での問題のデバッグに利用できるようになります。 NIC の新しい RHEL 7 名前付け規則もオフになります。 上記の他に、次のパラメーターを削除することをお勧めします。

    ```config-grub
    rhgb quiet crashkernel=auto
    ```

    クラウド環境では、すべてのログをシリアル ポートに送信するため、グラフィカル ブートおよびクワイエット ブートは役立ちません。 `crashkernel` オプションの構成は、必要であればそのままにしてかまいません。 ただし、このパラメーターにより、仮想マシン内の使用可能なメモリ量が 128 MB 以上減少します。仮想マシンのサイズが小さいと、このことが問題になる可能性があります。

1. `/etc/default/grub`の編集を終了したら、次のコマンドを実行して GRUB 構成を再構築します。

    ```console
    # sudo grub2-mkconfig -o /boot/grub2/grub.cfg
    ```

1. Hyper-V モジュールを initramfs に追加します。

    `/etc/dracut.conf`を編集して、コンテンツを追加します。

    ```config-conf
    add_drivers+=" hv_vmbus hv_netvsc hv_storvsc "
    ```

    initramfs を再構築します。

    ```console
    # dracut -f -v
    ```

1. SSH サーバーがインストールされており、起動時に開始するように構成されていることを確認します。 通常これが既定の設定です。 `/etc/ssh/sshd_config` を変更して、次の行を含めます。

    ```config
    ClientAliveInterval 180
    ```

1. WALinuxAgent パッケージ `WALinuxAgent-<version>` が Red Hat extras リポジトリにプッシュされました。 次のコマンドを実行して extras リポジトリを有効にします。

    ```console
    # subscription-manager repos --enable=rhel-7-server-extras-rpms
    ```

1. 次のコマンドを実行して Azure Linux エージェントをインストールします。

    ```console
    # sudo yum install WALinuxAgent

    # sudo systemctl enable waagent.service
    ```

1. cloud-init のインストール

    Hyper-V マネージャーからの RHEL 7 仮想マシンの準備に関するセクションの手順 12.「プロビジョニングを処理する cloud-init をインストールします」に従います。

1. スワップの構成

    オペレーティング システム ディスクにスワップ領域を作成しないでください。
    Hyper-V マネージャーからの RHEL 7 仮想マシンの準備に関するセクションの手順 13.「スワップの構成」に従います。

1. サブスクリプションを登録解除する場合は、次のコマンドを実行します。

    ```console
    # sudo subscription-manager unregister
    ```

1. プロビジョニング解除

    Hyper-V マネージャーからの RHEL 7 仮想マシンの準備に関するセクションの手順 15.「プロビジョニング解除」に従います。


1. 仮想マシンをシャットダウンし、VMDK ファイルを VHD 形式に変換します。

    > [!NOTE]
    > qemu-img のバージョン 2.2.1 以降には VHD が適切にフォーマットされないというバグがあることがわかっています。 この問題は QEMU 2.6 で修正されています。 qemu-img 2.2.0 以前を使用するか、2.6 以降に更新することをお勧めします。 [https://bugs.launchpad.net/qemu/+bug/1490611](https://bugs.launchpad.net/qemu/+bug/1490611 ) を参照してください。
    >

    まず、イメージを未加工の形式に変換します。

    ```console
    # qemu-img convert -f vmdk -O raw rhel-7.4.vmdk rhel-7.4.raw
    ```

    未加工のイメージのサイズが 1 MB になっていることを確認します。 そうでない場合は、1 MB になるようにサイズの端数を切り上げます。

    ```console
    # MB=$((1024*1024))
    # size=$(qemu-img info -f raw --output json "rhel-7.4.raw" | \
      gawk 'match($0, /"virtual-size": ([0-9]+),/, val) {print val[1]}')

    # rounded_size=$((($size/$MB + 1)*$MB))
    # qemu-img resize rhel-7.4.raw $rounded_size
    ```

    未フォーマット ディスクを固定サイズの VHD に変換します。

    ```console
    # qemu-img convert -f raw -o subformat=fixed -O vpc rhel-7.4.raw rhel-7.4.vhd
    ```

    または、qemu のバージョン **2.6 以降** を使用して `force_size` オプションを含めます。

    ```console
    # qemu-img convert -f raw -o subformat=fixed,force_size -O vpc rhel-7.4.raw rhel-7.4.vhd
    ```

## <a name="kickstart-file"></a>kickstart ファイル

このセクションでは、kickstart ファイルを使用して、ISO から RHEL 7 ディストリビューションを準備する方法について説明します。

### <a name="rhel-7-from-a-kickstart-file"></a>kickstart ファイルからの RHEL 7

1.  次の内容を含んだ kickstart ファイルを作成し、そのファイルを保存します。 kickstart のインストールの詳細については、 [kickstart インストール ガイド](https://access.redhat.com/documentation/en-US/Red_Hat_Enterprise_Linux/7/html/Installation_Guide/chap-kickstart-installations.html)を参照してください。

    ```text
    # Kickstart for provisioning a RHEL 7 Azure VM

    # System authorization information
      auth --enableshadow --passalgo=sha512

    # Use graphical install
    text

    # Do not run the Setup Agent on first boot
    firstboot --disable

    # Keyboard layouts
    keyboard --vckeymap=us --xlayouts='us'

    # System language
    lang en_US.UTF-8

    # Network information
    network  --bootproto=dhcp

    # Root password
    rootpw --plaintext "to_be_disabled"

    # System services
    services --enabled="sshd,waagent,NetworkManager"

    # System timezone
    timezone Etc/UTC --isUtc --ntpservers 0.rhel.pool.ntp.org,1.rhel.pool.ntp.org,2.rhel.pool.ntp.org,3.rhel.pool.ntp.org

    # Partition clearing information
    clearpart --all --initlabel

    # Clear the MBR
    zerombr

    # Disk partitioning information
    part /boot --fstype="xfs" --size=500
    part / --fstyp="xfs" --size=1 --grow --asprimary

    # System bootloader configuration
    bootloader --location=mbr

    # Firewall configuration
    firewall --disabled

    # Enable SELinux
    selinux --enforcing

    # Don't configure X
    skipx

    # Power down the machine after install
    poweroff

    %packages
    @base
    @console-internet
    chrony
    sudo
    parted
    -dracut-config-rescue

    %end

    %post --log=/var/log/anaconda/post-install.log

    #!/bin/bash

    # Register Red Hat Subscription
    subscription-manager register --username=XXX --password=XXX --auto-attach --force

    # Install latest repo update
    yum update -y

    # Enable extras repo
    subscription-manager repos --enable=rhel-7-server-extras-rpms

    # Install WALinuxAgent
    yum install -y WALinuxAgent

    # Unregister Red Hat subscription
    subscription-manager unregister

    # Enable waaagent at boot-up
    systemctl enable waagent

    # Install cloud-init
    yum install -y cloud-init cloud-utils-growpart gdisk hyperv-daemons

    # Configure waagent for cloud-init
    sed -i 's/Provisioning.UseCloudInit=n/Provisioning.UseCloudInit=y/g' /etc/waagent.conf
    sed -i 's/Provisioning.Enabled=y/Provisioning.Enabled=n/g' /etc/waagent.conf
    sed -i 's/ResourceDisk.Format=y/ResourceDisk.Format=n/g' /etc/waagent.conf
    sed -i 's/ResourceDisk.EnableSwap=y/ResourceDisk.EnableSwap=n/g' /etc/waagent.conf

    echo "Adding mounts and disk_setup to init stage"
    sed -i '/ - mounts/d' /etc/cloud/cloud.cfg
    sed -i '/ - disk_setup/d' /etc/cloud/cloud.cfg
    sed -i '/cloud_init_modules/a\\ - mounts' /etc/cloud/cloud.cfg
    sed -i '/cloud_init_modules/a\\ - disk_setup' /etc/cloud/cloud.cfg

    # Disable the root account
    usermod root -p '!!'

    # Disabke swap in WALinuxAgent
    ResourceDisk.Format=n
    ResourceDisk.EnableSwap=n

    # Configure swap using cloud-init
    cat > /etc/cloud/cloud.cfg.d/00-azure-swap.cfg << EOF
    #cloud-config
    # Generated by Azure cloud image build
    disk_setup:
    ephemeral0:
        table_type: mbr
        layout: [66, [33, 82]]
        overwrite: True
    fs_setup:
    - device: ephemeral0.1
        filesystem: ext4
    - device: ephemeral0.2
        filesystem: swap
    mounts:
    - ["ephemeral0.1", "/mnt"]
    - ["ephemeral0.2", "none", "swap", "sw", "0", "0"]
    EOF

    # Set the cmdline
    sed -i 's/^\(GRUB_CMDLINE_LINUX\)=".*"$/\1="console=tty1 console=ttyS0 earlyprintk=ttyS0 rootdelay=300"/g' /etc/default/grub

    # Enable SSH keepalive
    sed -i 's/^#\(ClientAliveInterval\).*$/\1 180/g' /etc/ssh/sshd_config

    # Build the grub cfg
    grub2-mkconfig -o /boot/grub2/grub.cfg

    # Configure network
    cat << EOF > /etc/sysconfig/network-scripts/ifcfg-eth0
    DEVICE=eth0
    ONBOOT=yes
    BOOTPROTO=dhcp
    TYPE=Ethernet
    USERCTL=no
    PEERDNS=yes
    IPV6INIT=no
    PERSISTENT_DHCLIENT=yes
    NM_CONTROLLED=yes
    EOF

    # Deprovision and prepare for Azure if you are creating a generalized image
    sudo cloud-init clean --logs --seed
    sudo rm -rf /var/lib/cloud/
    sudo rm -rf /var/lib/waagent/
    sudo rm -f /var/log/waagent.log

    sudo waagent -force -deprovision+user
    rm -f ~/.bash_history
    export HISTSIZE=0

    %end
    ```

1. インストール システムからアクセスできる場所に kickstart ファイルを置きます。

1. Hyper-V マネージャーで新しい仮想マシンを作成します。 **[仮想ハード ディスクの接続]** ページで、 **[後で仮想ハード ディスクを接続する]** を選択し、仮想マシンの新規作成ウィザードを完了します。

1. 仮想マシンの設定を開きます。

    a.  新しい仮想ハード ディスクを仮想マシンに接続します。 **[VHD 形式]** と **[固定サイズ]** を選択します。

    b.  インストール ISO を DVD ドライブに接続します。

    c.  CD から起動するように BIOS を設定します。

1. 仮想マシンを開始します。 インストール ガイドが表示されたら、 **Tab** キーを押してブート オプションを構成します。

1. ブート オプションの最後に `inst.ks=<the location of the kickstart file>` を入力し、 **Enter** キーを押します。

1. インストールが完了するのを待ちます。 完了すると、仮想マシンが自動的にシャットダウンされます。 これで、Linux VHD を Azure にアップロードする準備が整いました。

## <a name="known-issues"></a>既知の問題
### <a name="the-hyper-v-driver-could-not-be-included-in-the-initial-ram-disk-when-using-a-non-hyper-v-hypervisor"></a>非 Hyper-V ハイパーバイザーの使用時に Hyper-V ドライバーを初期 RAM ディスクに含めることができない

Hyper-V 環境で実行されていることを Linux が検出しなかった場合、Linux インストーラーは、初期 RAM ディスク (initrd または initramfs) に Hyper-V 用のドライバーを追加しないことがあります。

別の仮想化システム (VirtualBox、Xen など) を使用して Linux イメージを準備する場合は、少なくとも hv_vmbus と hv_storvsc のカーネル モジュールを初期 RAM ディスクで使用できるように initrd の再構築が必要になる場合があります。 これは少なくとも、アップストリームの Red Hat ディストリビューションに基づくシステムの既知の問題です。

この問題を解決するには、initramfs に Hyper-V モジュールを追加して再構築する必要があります。

`/etc/dracut.conf` を編集し、次の内容を追加します。

```config-conf
add_drivers+=" hv_vmbus hv_netvsc hv_storvsc "
```

initramfs を再構築します。

```console
# dracut -f -v
```

詳細については、 [initramfs の再構築](https://access.redhat.com/solutions/1958)に関する情報を参照してください。

## <a name="next-steps"></a>次のステップ
* これで、Red Hat Enterprise Linux 仮想ハード ディスク を使用して、Azure に新しい仮想マシンを作成する準備が整いました。 .vhd ファイルを Azure に初めてアップロードする場合は、「[Create a Linux VM from a custom disk (カスタム ディスクから Linux VM を作成する)](upload-vhd.md#option-1-upload-a-vhd)」を参照してください。
* Red Hat Enterprise Linux の実行が認定されているハイパーバイザーの詳細については、 [Red Hat の Web サイト](https://access.redhat.com/certified-hypervisors)を参照してください。
* 実稼働可能な RHEL BYOS イメージの詳細については、[BYOS](../workloads/redhat/byos.md)のドキュメント ページにアクセスしてください。
