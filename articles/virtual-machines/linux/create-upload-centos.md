---
title: CentOS ベースの Linux VHD の作成とアップロード
description: CentOS ベースの Linux オペレーティング システムを格納した Azure 仮想ハード ディスク (VHD) を作成してアップロードする方法について説明します。
author: danielsollondon
ms.service: virtual-machines
ms.collection: linux
ms.topic: how-to
ms.date: 12/01/2020
ms.author: danis
ms.openlocfilehash: 4745e631bd92675f8dd1ef0d390baa88f7666552
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102554668"
---
# <a name="prepare-a-centos-based-virtual-machine-for-azure"></a>Azure 用の CentOS ベースの仮想マシンの準備

CentOS ベースの Linux オペレーティング システムを格納した Azure 仮想ハード ディスク (VHD) を作成してアップロードする方法について説明します。

* [Azure 用の CentOS 6.x 仮想マシンの準備](#centos-6x)
* [Azure 用の CentOS 7.0 以上の仮想マシンの準備](#centos-70)


## <a name="prerequisites"></a>前提条件

この記事では、既に CentOS (または同様な派生版) Linux オペレーティング システムを仮想ハード ディスクにインストールしていることを前提にしています。 .vhd ファイルを作成するツールは、Hyper-V のような仮想化ソリューションなど複数あります。 詳細については、「 [Hyper-V の役割のインストールと仮想マシンの構成](/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/hh846766(v=ws.11))」を参照してください。

**CentOS のインストールに関する注記**

* Azure で Linux を準備する際のその他のヒントについては、「 [Linux のインストールに関する注記](create-upload-generic.md#general-linux-installation-notes) 」も参照してください。
* VHDX 形式は Azure ではサポートされていません。サポートされるのは **固定 VHD** のみです。  Hyper-V マネージャーまたは convert-vhd コマンドレットを使用して、ディスクを VHD 形式に変換できます。 VirtualBox を使用する場合は、ディスクの作成時に、既定で動的に割り当てられるサイズではなく、**固定サイズ** を選択することを意味します。
* Linux システムをインストールする場合は、LVM (通常、多くのインストールで既定) ではなく標準パーティションを使用することを *お勧めします*。 これにより、特に OS ディスクをトラブルシューティングのために別の同じ VM に接続する必要がある場合に、LVM 名と複製された VM の競合が回避されます。 [LVM](/previous-versions/azure/virtual-machines/linux/configure-lvm) または [RAID](/previous-versions/azure/virtual-machines/linux/configure-raid) をデータ ディスク上で使用できます。
* **UDF ファイル システムをマウントするためのカーネル サポートが必要です。** Azure での最初の起動時に、ゲストに接続されている UDF でフォーマットされたメディアを介して、プロビジョニング構成が Linux VM に渡されます。 Azure Linux エージェントは、その構成を読み取り、VM をプロビジョニングする UDF ファイル システムをマウントできる必要があります。
* 2\.6.37 未満の Linux カーネル バージョンは、HYPER-V で大きい VM サイズの NUMA をサポートできません。 この問題は、主に、アップストリームの Red Hat 2.6.32 カーネルを使用した古いディストリビューションに影響し、RHEL 6.6 (kernel-2.6.32-504) で修正されました。 2\.6.37 より古いカスタム カーネルまたは2.6.32-504 より古い RHEL ベースのカーネルを実行しているシステムでは、grub.conf のカーネル コマンドラインで、ブート パラメーター `numa=off` を設定する必要があります。 詳細については、Red Hat [KB 436883](https://access.redhat.com/solutions/436883) を参照してください。
* OS ディスクにスワップ パーティションを構成しないでください。 このことに関する詳細については、次の手順を参照してください。
* Azure の VHD の仮想サイズはすべて、1 MB にアラインメントさせる必要があります。 未フォーマット ディスクから VHD に変換するときに、変換する前の未フォーマット ディスクのサイズが 1 MB の倍数であることを確認する必要があります。 詳細については、[Linux のインストールに関する注記](create-upload-generic.md#general-linux-installation-notes)のセクションを参照してください。

## <a name="centos-6x"></a>CentOS 6.x

1. Hyper-V マネージャーで仮想マシンを選択します。

2. **[接続]** をクリックすると、仮想マシンのコンソール ウィンドウが開きます。

3. CentOS 6 では、NetworkManager が Azure Linux エージェントと干渉する可能性があります。 次のコマンドを実行して、このパッケージをアンインストールします。

    ```bash
    sudo rpm -e --nodeps NetworkManager
    ```

4. `/etc/sysconfig/network` ファイルを作成または編集して次のテキストを追加します。

    ```console
    NETWORKING=yes
    HOSTNAME=localhost.localdomain
    ```

5. `/etc/sysconfig/network-scripts/ifcfg-eth0` ファイルを作成または編集して次のテキストを追加します。

    ```console
    DEVICE=eth0
    ONBOOT=yes
    BOOTPROTO=dhcp
    TYPE=Ethernet
    USERCTL=no
    PEERDNS=yes
    IPV6INIT=no
    ```

6. udev ルールを編集して、イーサネット インターフェイスの静的ルールが生成されないようにします。 これらのルールは、Microsoft Azure または Hyper-V で仮想マシンを複製する際に問題の原因となる可能性があります。

    ```bash
    sudo ln -s /dev/null /etc/udev/rules.d/75-persistent-net-generator.rules
    sudo rm -f /etc/udev/rules.d/70-persistent-net.rules
    ```

7. 次のコマンドを実行して、起動時にネットワーク サービスが開始されるようにします。

    ```bash
    sudo chkconfig network on
    ```

8. Azure データセンター内でホストされている OpenLogic のミラーを使用する場合は、`/etc/yum.repos.d/CentOS-Base.repo` ファイルを次のリポジトリに置き換えます。  これにより、Azure Linux エージェントなどの追加パッケージを含む **[openlogic]** リポジトリも追加されます。

   ```console
   [openlogic]
   name=CentOS-$releasever - openlogic packages for $basearch
   baseurl=http://olcentgbl.trafficmanager.net/openlogic/$releasever/openlogic/$basearch/
   enabled=1
   gpgcheck=0

   [base]
   name=CentOS-$releasever - Base
   #mirrorlist=http://mirrorlist.centos.org/?release=$releasever&arch=$basearch&repo=os&infra=$infra
   baseurl=http://olcentgbl.trafficmanager.net/centos/$releasever/os/$basearch/
   gpgcheck=1
   gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-CentOS-6

   #released updates
   [updates]
   name=CentOS-$releasever - Updates
   #mirrorlist=http://mirrorlist.centos.org/?release=$releasever&arch=$basearch&repo=updates&infra=$infra
   baseurl=http://olcentgbl.trafficmanager.net/centos/$releasever/updates/$basearch/
   gpgcheck=1
   gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-CentOS-6

   #additional packages that may be useful
   [extras]
   name=CentOS-$releasever - Extras
   #mirrorlist=http://mirrorlist.centos.org/?release=$releasever&arch=$basearch&repo=extras&infra=$infra
   baseurl=http://olcentgbl.trafficmanager.net/centos/$releasever/extras/$basearch/
   gpgcheck=1
   gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-CentOS-6

   #additional packages that extend functionality of existing packages
   [centosplus]
   name=CentOS-$releasever - Plus
   #mirrorlist=http://mirrorlist.centos.org/?release=$releasever&arch=$basearch&repo=centosplus&infra=$infra
   baseurl=http://olcentgbl.trafficmanager.net/centos/$releasever/centosplus/$basearch/
   gpgcheck=1
   enabled=0
   gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-CentOS-6

   #contrib - packages by Centos Users
   [contrib]
   name=CentOS-$releasever - Contrib
   #mirrorlist=http://mirrorlist.centos.org/?release=$releasever&arch=$basearch&repo=contrib&infra=$infra
   baseurl=http://olcentgbl.trafficmanager.net/centos/$releasever/contrib/$basearch/
   gpgcheck=1
   enabled=0
   gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-CentOS-6
   ```

    > [!Note]
    > これ以降の説明では、少なくとも `[openlogic]` リポジトリを使用していることを前提とします。このリポジトリは、以下の Azure Linux エージェントのインストールに使用されます。

9. /etc/yum.conf ファイルに次の行を追加します。

    ```console
    http_caching=packages
    ```

10. 次のコマンドを実行して、現在の yum メタデータを消去し、最新のパッケージでシステムを更新します。

    ```bash
    yum clean all
    ```

    古いバージョンの CentOS 用のイメージを作成している場合を除き、すべてのパッケージを最新のものに更新することをお勧めします。

    ```bash
    sudo yum -y update
    ```

    このコマンドを実行すると、再起動が必要になることがあります。

11. (省略可能) Linux Integration Services (LIS) 用ドライバーをインストールします。

    > [!IMPORTANT]
    > この手順は、CentOS 6.3 およびそれ以前では **必須** です。それ以降のリリースでは、この手順を省略できます。

    ```bash
    sudo rpm -e hypervkvpd  ## (may return error if not installed, that's OK)
    sudo yum install microsoft-hyper-v
    ```

    または、[LIS ダウンロード ページ](https://www.microsoft.com/download/details.aspx?id=55106)の手動インストール手順に従って、RPM を VM にインストールします。

12. Azure Linux エージェントと依存関係をインストールします。 waagent サービスを開始して有効にします。

    ```bash
    sudo yum install python-pyasn1 WALinuxAgent
    sudo service waagent start
    sudo chkconfig waagent on
    ```


    手順 3. で説明した NetworkManager パッケージおよび NetworkManager-gnome パッケージを削除していない場合、WALinuxAgent パッケージによってこれらのパッケージが削除されます。

13. GRUB 構成でカーネルのブート行を変更して Azure の追加のカーネル パラメーターを含めます。 これを行うには、テキスト エディターで `/boot/grub/menu.lst` を開き、既定のカーネルに次のパラメーターが含まれていることを確認します。

    ```console
    console=ttyS0 earlyprintk=ttyS0 rootdelay=300
    ```

    これにより、すべてのコンソール メッセージが最初のシリアル ポートに送信され、メッセージを Azure での問題のデバッグに利用できるようになります。

    上記のほかに、次のパラメーターを *削除* することをお勧めします。

    ```console
    rhgb quiet crashkernel=auto
    ```

    クラウド環境では、すべてのログをシリアル ポートに送信するため、グラフィカル ブートおよびクワイエット ブートは役立ちません。  必要に応じて `crashkernel` オプションは構成したままにすることができますが、このパラメーターにより、VM 内の使用可能なメモリ量が 128 MB 以上減少するため、比較的小さなサイズの VM では問題となる可能性がある点に注意してください。

    > [!Important]
    > CentOS 6.5 およびそれ以前では、カーネル パラメーター `numa=off` も設定する必要があります。 Red Hat [KB 436883](https://access.redhat.com/solutions/436883) を参照してください。

14. SSH サーバーがインストールされており、起動時に開始するように構成されていることを確認します。  通常これが既定です。

15. OS ディスクにスワップ領域を作成しないでください。

    Azure Linux エージェントは、Azure でプロビジョニングされた後に VM に接続されたローカルのリソース ディスクを使用してスワップ領域を自動的に構成します。 ローカル リソース ディスクは *一時* ディスクであるため、VM のプロビジョニングが解除されると空になることに注意してください。 Azure Linux Agent のインストール後に (前の手順を参照)、 `/etc/waagent.conf` にある次のパラメーターを正確に変更します。

    ```console
    ResourceDisk.Format=y
    ResourceDisk.Filesystem=ext4
    ResourceDisk.MountPoint=/mnt/resource
    ResourceDisk.EnableSwap=y
    ResourceDisk.SwapSizeMB=2048 ## NOTE: set this to whatever you need it to be.
    ```

16. 次のコマンドを実行して仮想マシンをプロビジョニング解除し、Azure でのプロビジョニング用に準備します。

    ```bash
    sudo waagent -force -deprovision+user
    export HISTSIZE=0
    logout
    ```

17. Hyper-V マネージャーで **[アクション]、[シャットダウン]** の順にクリックします。 これで、Linux VHD を Azure にアップロードする準備が整いました。



## <a name="centos-70"></a>CentOS 7.0+

**CentOS 7 (および同様な派生版) への変更**

Azure 用の CentOS 7 仮想マシンを準備する手順は、CentOS 6 の場合とほとんど同じですが、次のように、重要な違いがいくつかあります。

* NetworkManager パッケージが、Azure Linux エージェントと競合しなくなりました。 このパッケージは既定でインストールされます。このパッケージを削除しないことをお勧めします。
* GRUB2 が、既定のブートローダーとして使用されるようになったため、カーネル パラメーターの編集手順が変更されました (以下を参照)。
* XFS が既定のファイル システムになりました。 必要に応じて、引き続き ext4 ファイル システムを使用できます。

**構成の手順**

1. Hyper-V マネージャーで仮想マシンを選択します。

2. **[接続]** をクリックすると、仮想マシンのコンソール ウィンドウが開きます。

3. `/etc/sysconfig/network` ファイルを作成または編集して次のテキストを追加します。

    ```console
    NETWORKING=yes
    HOSTNAME=localhost.localdomain
    ```

4. `/etc/sysconfig/network-scripts/ifcfg-eth0` ファイルを作成または編集して次のテキストを追加します。

    ```console
    DEVICE=eth0
    ONBOOT=yes
    BOOTPROTO=dhcp
    TYPE=Ethernet
    USERCTL=no
    PEERDNS=yes
    IPV6INIT=no
    NM_CONTROLLED=no
    ```

5. udev ルールを編集して、イーサネット インターフェイスの静的ルールが生成されないようにします。 これらのルールは、Microsoft Azure または Hyper-V で仮想マシンを複製する際に問題の原因となる可能性があります。

    ```bash
    sudo ln -s /dev/null /etc/udev/rules.d/75-persistent-net-generator.rules
    ```

6. Azure データセンター内でホストされている OpenLogic のミラーを使用する場合は、`/etc/yum.repos.d/CentOS-Base.repo` ファイルを次のリポジトリに置き換えます。  これにより、Azure Linux エージェント用のパッケージを含む **[openlogic]** リポジトリも追加されます。

   ```console
   [openlogic]
   name=CentOS-$releasever - openlogic packages for $basearch
   baseurl=http://olcentgbl.trafficmanager.net/openlogic/$releasever/openlogic/$basearch/
   enabled=1
   gpgcheck=0
    
   [base]
   name=CentOS-$releasever - Base
   #mirrorlist=http://mirrorlist.centos.org/?release=$releasever&arch=$basearch&repo=os&infra=$infra
   baseurl=http://olcentgbl.trafficmanager.net/centos/$releasever/os/$basearch/
   gpgcheck=1
   gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-CentOS-7
    
   #released updates
   [updates]
   name=CentOS-$releasever - Updates
   #mirrorlist=http://mirrorlist.centos.org/?release=$releasever&arch=$basearch&repo=updates&infra=$infra
   baseurl=http://olcentgbl.trafficmanager.net/centos/$releasever/updates/$basearch/
   gpgcheck=1
   gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-CentOS-7
    
   #additional packages that may be useful
   [extras]
   name=CentOS-$releasever - Extras
   #mirrorlist=http://mirrorlist.centos.org/?release=$releasever&arch=$basearch&repo=extras&infra=$infra
   baseurl=http://olcentgbl.trafficmanager.net/centos/$releasever/extras/$basearch/
   gpgcheck=1
   gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-CentOS-7
    
   #additional packages that extend functionality of existing packages
   [centosplus]
   name=CentOS-$releasever - Plus
   #mirrorlist=http://mirrorlist.centos.org/?release=$releasever&arch=$basearch&repo=centosplus&infra=$infra
   baseurl=http://olcentgbl.trafficmanager.net/centos/$releasever/centosplus/$basearch/
   gpgcheck=1
   enabled=0
   gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-CentOS-7
   ```
    
   > [!Note]
   > これ以降の説明では、少なくとも `[openlogic]` リポジトリを使用していることを前提とします。このリポジトリは、以下の Azure Linux エージェントのインストールに使用されます。

7. 次のコマンドを実行して、現在の yum メタデータをクリアし、更新をインストールします。

    ```bash
    sudo yum clean all
    ```

    古いバージョンの CentOS 用のイメージを作成している場合を除き、すべてのパッケージを最新のものに更新することをお勧めします。

    ```bash
    sudo yum -y update
    ```

    このコマンドを実行すると、再起動が必要になることがあります。

8. GRUB 構成でカーネルのブート行を変更して Azure の追加のカーネル パラメーターを含めます。 これを行うには、テキスト エディターで `/etc/default/grub` を開き、次のように `GRUB_CMDLINE_LINUX` パラメーターを編集します。

    ```console
    GRUB_CMDLINE_LINUX="rootdelay=300 console=ttyS0 earlyprintk=ttyS0 net.ifnames=0"
    ```

   これにより、すべてのコンソール メッセージが最初のシリアル ポートに送信され、メッセージを Azure での問題のデバッグに利用できるようになります。 NIC の新しい CentOS 7 名前付け規則もオフになります。 上記のほかに、次のパラメーターを *削除* することをお勧めします。

    ```console
    rhgb quiet crashkernel=auto
    ```

    クラウド環境では、すべてのログをシリアル ポートに送信するため、グラフィカル ブートおよびクワイエット ブートは役立ちません。 必要に応じて `crashkernel` オプションは構成したままにすることができますが、このパラメーターにより、VM 内の使用可能なメモリ量が 128 MB 以上減少するため、比較的小さなサイズの VM では問題となる可能性がある点に注意してください。

9. 上記のとおりに `/etc/default/grub` の編集を終了したら、次のコマンドを実行して GRUB 構成をリビルドします。

    ```bash
    sudo grub2-mkconfig -o /boot/grub2/grub.cfg
    ```

10. **VMware、VirtualBox、または KVM** からイメージをビルドする場合:Hyper-V ドライバーを確実に initramfs に含めます。

    `/etc/dracut.conf`を編集して、コンテンツを追加します。

    ```console
    add_drivers+=" hv_vmbus hv_netvsc hv_storvsc "
    ```

    Initramfs を再構築します。

    ```bash
    sudo dracut -f -v
    ```

11. Azure Linux エージェントと Azure VM 拡張機能用の依存関係をインストールします。

    ```bash
    sudo yum install python-pyasn1 WALinuxAgent
    sudo systemctl enable waagent
    ```

12. プロビジョニングを処理する cloud-init をインストールします

    ```console
    yum install -y cloud-init cloud-utils-growpart gdisk hyperv-daemons

    # Configure waagent for cloud-init
    sed -i 's/Provisioning.UseCloudInit=n/Provisioning.UseCloudInit=y/g' /etc/waagent.conf
    sed -i 's/Provisioning.Enabled=y/Provisioning.Enabled=n/g' /etc/waagent.conf


    echo "Adding mounts and disk_setup to init stage"
    sed -i '/ - mounts/d' /etc/cloud/cloud.cfg
    sed -i '/ - disk_setup/d' /etc/cloud/cloud.cfg
    sed -i '/cloud_init_modules/a\\ - mounts' /etc/cloud/cloud.cfg
    sed -i '/cloud_init_modules/a\\ - disk_setup' /etc/cloud/cloud.cfg

    echo "Allow only Azure datasource, disable fetching network setting via IMDS"
    cat > /etc/cloud/cloud.cfg.d/91-azure_datasource.cfg <<EOF
    datasource_list: [ Azure ]
    datasource:
    Azure:
        apply_network_config: False
    EOF

    if [[ -f /mnt/resource/swapfile ]]; then
    echo Removing swapfile - RHEL uses a swapfile by default
    swapoff /mnt/resource/swapfile
    rm /mnt/resource/swapfile -f
    fi

    echo "Add console log file"
    cat >> /etc/cloud/cloud.cfg.d/05_logging.cfg <<EOF

    # This tells cloud-init to redirect its stdout and stderr to
    # 'tee -a /var/log/cloud-init-output.log' so the user can see output
    # there without needing to look on the console.
    output: {all: '| tee -a /var/log/cloud-init-output.log'}
    EOF

    ```


13. スワップの構成オペレーティング システム ディスクにスワップ領域を作成しないでください。

    以前は、Azure で仮想マシンがプロビジョニングされた後に、仮想マシンに接続されたローカル リソース ディスクを使用してスワップ領域を自動的に構成するために、Azure Linux エージェントが使用されていました。 しかし、これは cloud-init によって処理されるようになったので、Linux エージェントを使用して、スワップ ファイルを作成するリソース ディスクをフォーマット **しないでください**。`/etc/waagent.conf` で次のパラメーターを適切に変更します。

    ```console
    sed -i 's/ResourceDisk.Format=y/ResourceDisk.Format=n/g' /etc/waagent.conf
    sed -i 's/ResourceDisk.EnableSwap=y/ResourceDisk.EnableSwap=n/g' /etc/waagent.conf
    ```

    スワップをマウント、フォーマット、作成する場合は、次のいずれかの方法を使用できます。
    * VM を作成するたびに、cloud-init 構成としてこれを渡す
    * VM が作成されるたびに、これを実行するイメージに組み込まれている cloud-init ディレクティブを使用します。

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

14. 次のコマンドを実行して仮想マシンをプロビジョニング解除し、Azure でのプロビジョニング用に準備します。

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

15. Hyper-V マネージャーで **[アクション]、[シャットダウン]** の順にクリックします。 これで、Linux VHD を Azure にアップロードする準備が整いました。

## <a name="next-steps"></a>次のステップ

これで、CentOS Linux 仮想ハード ディスク を使用して、Azure に新しい仮想マシンを作成する準備が整いました。 .vhd ファイルを Azure に初めてアップロードする場合は、「[Create a Linux VM from a custom disk (カスタム ディスクから Linux VM を作成する)](upload-vhd.md#option-1-upload-a-vhd)」を参照してください。