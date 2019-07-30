---
title: Azure で使用するための Red Hat Enterprise Linux VHD の作成とアップロード | Microsoft Docs
description: Red Hat Linux オペレーティング システムを格納した Azure 仮想ハード ディスク (VHD) を作成してアップロードする方法について説明します。
services: virtual-machines-linux
documentationcenter: ''
author: szarkos
manager: gwallace
editor: tysonn
tags: azure-resource-manager,azure-service-management
ms.assetid: 6c6b8f72-32d3-47fa-be94-6cb54537c69f
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 05/17/2019
ms.author: szark
ms.openlocfilehash: f7f9baec5421a63e46536480cde8d60d3877d4d7
ms.sourcegitcommit: 2e4b99023ecaf2ea3d6d3604da068d04682a8c2d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/09/2019
ms.locfileid: "67670974"
---
# <a name="prepare-a-red-hat-based-virtual-machine-for-azure"></a>Azure 用の Red Hat ベースの仮想マシンの準備
この記事では、Red Hat Enterprise Linux (RHEL) の仮想マシンを Azure で使用できるように準備する方法について説明します。 この記事で取り上げる RHEL のバージョンは 6.7+ と 7.1+ で、 準備対象のハイパーバイザーは Hyper-V、Kernel-based Virtual Machine (KVM)、VMware です。 Red Hat の Cloud Access プログラムに参加するための資格要件の詳細については、[Red Hat の Cloud Access Web サイト](https://www.redhat.com/en/technologies/cloud-computing/cloud-access)と [Azure での RHEL の実行](https://access.redhat.com/ecosystem/ccsp/microsoft-azure)に関するページを参照してください。 RHEL イメージの作成を自動化する方法については、[Azure Image Builder](https://docs.microsoft.com/azure/virtual-machines/linux/image-builder-overview) を参照してください。

## <a name="prepare-a-red-hat-based-virtual-machine-from-hyper-v-manager"></a>Hyper-V マネージャーからの Red Hat ベースの仮想マシンの準備

### <a name="prerequisites"></a>前提条件
このセクションは、Red Hat の Web サイトから取得した ISO ファイルの RHEL イメージが仮想ハード ディスク (VHD) にインストール済みであることを前提としています。 Hyper-V マネージャーを使用してオペレーティング システム イメージをインストールする方法の詳細については、[Hyper-V の役割のインストールと仮想マシンの構成](https://technet.microsoft.com/library/hh846766.aspx)に関するページを参照してください。

**RHEL のインストールに関する注記**

* Azure では、VHDX 形式がサポートされません。 Azure でサポートされるのは、容量固定の VHD のみです。 Hyper-V マネージャーを使ってディスクの形式を VHD に変換するか、または convert-vhd コマンドレットを使用してください。 VirtualBox を使用する場合は、ディスクの作成時に、既定の動的割り当てオプションではなく、 **[容量固定]** を選択します。
* Azure でサポートされるのは、世代 1 の仮想マシンのみです。 第 1 世代の仮想マシンを、VHDX ファイル形式から VHD ファイル形式に、容量可変から容量固定ディスクに変換できます。 ただし仮想マシンの世代を変更することはできません。 詳細については、「[Should I create a generation 1 or 2 virtual machine in Hyper-V? (Hyper-V で第 1 世代または第 2 世代の仮想マシンを作成する必要はありますか)](https://technet.microsoft.com/windows-server-docs/compute/hyper-v/plan/should-i-create-a-generation-1-or-2-virtual-machine-in-hyper-v)」を参照してください。
* VHD のサイズの上限は、1,023 GB です。
* Logical Volume Manager (LVM) がサポートされており、Azure 仮想マシンの OS ディスクやデータ ディスクに使用できます。 ただし、一般に、LVM ではなく OS ディスクの標準パーティションを使用することをお勧めします。 特にオペレーティング システム ディスクをトラブルシューティングのために別の同じ仮想マシンに接続する必要がある場合、そうすることで、複製された仮想マシンとの LVM 名の競合を回避することができます。 [LVM](configure-lvm.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) および [RAID](configure-raid.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) のドキュメントもご覧ください。
* ユニバーサル ディスク フォーマット (UDF) ファイル システムをマウントするためのカーネル サポートが必要です。 Azure での最初の起動時に、ゲストに接続されている UDF でフォーマットされたメディアを介して、プロビジョニング構成が Linux 仮想マシンに渡されます。 Azure Linux エージェントは、その構成を読み取り、仮想マシンをプロビジョニングする UDF ファイル システムをマウントできる必要があります。
* オペレーティング システム ディスクでスワップ パーティションを構成しないでください。 Linux エージェントは、一時的なリソース ディスク上にスワップ ファイルを作成するよう構成できます。  このことに関する詳細については、次の手順を参照してください。
* Azure の VHD の仮想サイズはすべて、1 MB にアラインメントさせる必要があります。 未フォーマット ディスクから VHD に変換するときに、変換する前の未フォーマット ディスクのサイズが 1 MB の倍数であることを確認する必要があります。 詳細については、後述の手順を参照してください。 また、[Linux のインストールに関する注記](create-upload-generic.md#general-linux-installation-notes)のセクションも参照してください。

### <a name="prepare-a-rhel-6-virtual-machine-from-hyper-v-manager"></a>Hyper-V マネージャーからの RHEL 6 仮想マシンの準備

1. Hyper-V マネージャーで仮想マシンを選択します。

1. **[接続]** をクリックすると、仮想マシンのコンソール ウィンドウが開きます。

1. RHEL 6 では、NetworkManager が Azure Linux エージェントと干渉する可能性があります。 次のコマンドを実行して、このパッケージをアンインストールします。
   
        # sudo rpm -e --nodeps NetworkManager

1. `/etc/sysconfig/network` ファイルを作成または編集して次のテキストを追加します。
   
        NETWORKING=yes
        HOSTNAME=localhost.localdomain

1. `/etc/sysconfig/network-scripts/ifcfg-eth0` ファイルを作成または編集して次のテキストを追加します。
   
        DEVICE=eth0
        ONBOOT=yes
        BOOTPROTO=dhcp
        TYPE=Ethernet
        USERCTL=no
        PEERDNS=yes
        IPV6INIT=no

1. udev ルールを移動 (または削除) して、イーサネット インターフェイスの静的ルールが生成されないようにします。 これらのルールは、Microsoft Azure または Hyper-V で仮想マシンを複製する際に問題の原因となります。

        # sudo ln -s /dev/null /etc/udev/rules.d/75-persistent-net-generator.rules
        
        # sudo rm -f /etc/udev/rules.d/70-persistent-net.rules

1. 次のコマンドを実行して、起動時にネットワーク サービスが開始されるようにします。

        # sudo chkconfig network on

1. RHEL リポジトリからパッケージをインストールできるように、次のコマンドを実行して Red Hat のサブスクリプションを登録します。

        # sudo subscription-manager register --auto-attach --username=XXX --password=XXX

1. WALinuxAgent パッケージ `WALinuxAgent-<version>` が Red Hat extras リポジトリにプッシュされました。 次のコマンドを実行して extras リポジトリを有効にします。

        # subscription-manager repos --enable=rhel-6-server-extras-rpms

1. GRUB 構成でカーネルのブート行を変更して Azure の追加のカーネル パラメーターを含めます。 この変更を行うには、テキスト エディターで `/boot/grub/menu.lst` を開き、既定のカーネルに次のパラメーターが含まれていることを確認します。
    
        console=ttyS0 earlyprintk=ttyS0 rootdelay=300
    
    これにより、すべてのコンソール メッセージが最初のシリアル ポートに送信され、メッセージを Azure での問題のデバッグに利用できるようになります。
    
    上記の他に、次のパラメーターを削除することをお勧めします。
    
        rhgb quiet crashkernel=auto
    
    クラウド環境では、すべてのログをシリアル ポートに送信するため、グラフィカル ブートおよびクワイエット ブートは役立ちません。  `crashkernel` オプションの構成は、必要であればそのままにしてかまいません。 ただし、このパラメーターにより、仮想マシン内の使用可能なメモリ量が 128 MB 以上減少します。 仮想マシンのサイズが小さいと、この構成が問題となる可能性があります。


1. SSH (Secure Shell) サーバーがインストールされており、起動時に開始するように構成されていることを確認します。通常は、既定でそのように構成されています。 /etc/ssh/sshd_config を変更して、次の行を含めます。

        ClientAliveInterval 180

1. 次のコマンドを実行して Azure Linux エージェントをインストールします。

        # sudo yum install WALinuxAgent

        # sudo chkconfig waagent on

    手順 3. で NetworkManager パッケージと NetworkManager-gnome パッケージを削除しなかった場合は、WALinuxAgent パッケージをインストールすると、これらのパッケージが削除されます。

1. オペレーティング システム ディスクにスワップ領域を作成しないでください。

    Azure Linux エージェントは、Azure で仮想マシンがプロビジョニングされた後に仮想マシンに接続されたローカルのリソース ディスクを使用してスワップ領域を自動的に構成できます。 ローカル リソース ディスクは一時ディスクであるため、仮想マシンのプロビジョニングが解除されると空になる場合があることにご注意ください。 前の手順で Azure Linux エージェントのインストール後に、/etc/waagent.conf にある次のパラメーターを適切に変更します。

        ResourceDisk.Format=y
        ResourceDisk.Filesystem=ext4
        ResourceDisk.MountPoint=/mnt/resource
        ResourceDisk.EnableSwap=y
        ResourceDisk.SwapSizeMB=2048    ## NOTE: set this to whatever you need it to be.

1. 次のコマンドを実行して、サブスクリプションを登録解除します (必要な場合)。

        # sudo subscription-manager unregister

1. 次のコマンドを実行して仮想マシンをプロビジョニング解除し、Azure でのプロビジョニング用に準備します。

        # Mote: if you are migrating a specific virtual machine and do not wish to create a generalized image,
        # skip the deprovision step
        # sudo waagent -force -deprovision

        # export HISTSIZE=0

        # logout

1. Hyper-V マネージャーで **[アクション]**  >  **[シャットダウン]** の順にクリックします。 これで、Linux VHD を Azure にアップロードする準備が整いました。


### <a name="prepare-a-rhel-7-virtual-machine-from-hyper-v-manager"></a>Hyper-V マネージャーからの RHEL 7 仮想マシンの準備

1. Hyper-V マネージャーで仮想マシンを選択します。

1. **[接続]** をクリックすると、仮想マシンのコンソール ウィンドウが開きます。

1. `/etc/sysconfig/network` ファイルを作成または編集して次のテキストを追加します。
   
        NETWORKING=yes
        HOSTNAME=localhost.localdomain

1. `/etc/sysconfig/network-scripts/ifcfg-eth0` ファイルを作成または編集して次のテキストを追加します。
   
        DEVICE=eth0
        ONBOOT=yes
        BOOTPROTO=dhcp
        TYPE=Ethernet
        USERCTL=no
        PEERDNS=yes
        IPV6INIT=no
        NM_CONTROLLED=no

1. 次のコマンドを実行して、起動時にネットワーク サービスが開始されるようにします。

        # sudo systemctl enable network

1. RHEL リポジトリからパッケージをインストールできるように、次のコマンドを実行して Red Hat のサブスクリプションを登録します。

        # sudo subscription-manager register --auto-attach --username=XXX --password=XXX

1. GRUB 構成でカーネルのブート行を変更して Azure の追加のカーネル パラメーターを含めます。 この変更を行うには、テキスト エディターで `/etc/default/grub` を開き、`GRUB_CMDLINE_LINUX` パラメーターを編集します。 例:
   
        GRUB_CMDLINE_LINUX="rootdelay=300 console=ttyS0 earlyprintk=ttyS0 net.ifnames=0"
   
   これにより、すべてのコンソール メッセージが最初のシリアル ポートに送信され、メッセージを Azure での問題のデバッグに利用できるようになります。 NIC の新しい RHEL 7 名前付け規則もオフになります。 上記の他に、次のパラメーターを削除することをお勧めします。
   
        rhgb quiet crashkernel=auto
   
    クラウド環境では、すべてのログをシリアル ポートに送信するため、グラフィカル ブートおよびクワイエット ブートは役立ちません。 `crashkernel` オプションの構成は、必要であればそのままにしてかまいません。 ただし、このパラメーターにより、仮想マシン内の使用可能なメモリ量が 128 MB 以上減少します。仮想マシンのサイズが小さいと、このことが問題になる可能性があります。

1. `/etc/default/grub`の編集を終了したら、次のコマンドを実行して GRUB 構成を再構築します。

        # sudo grub2-mkconfig -o /boot/grub2/grub.cfg

1. SSH サーバーがインストールされており、起動時に開始するように構成されていることを確認します。通常は、既定でそのように構成されています。 `/etc/ssh/sshd_config` を変更して、次の行を含めます。

        ClientAliveInterval 180

1. WALinuxAgent パッケージ `WALinuxAgent-<version>` が Red Hat extras リポジトリにプッシュされました。 次のコマンドを実行して extras リポジトリを有効にします。

        # subscription-manager repos --enable=rhel-7-server-extras-rpms

1. 次のコマンドを実行して Azure Linux エージェントをインストールします。

        # sudo yum install WALinuxAgent

        # sudo systemctl enable waagent.service

1. オペレーティング システム ディスクにスワップ領域を作成しないでください。

    Azure Linux エージェントは、Azure で仮想マシンがプロビジョニングされた後に仮想マシンに接続されたローカルのリソース ディスクを使用してスワップ領域を自動的に構成できます。 ローカル リソース ディスクは一時ディスクであるため、仮想マシンのプロビジョニングが解除されると空になる場合があることにご注意ください。 前の手順で Azure Linux エージェントのインストール後に、`/etc/waagent.conf` にある次のパラメーターを適切に変更します。

        ResourceDisk.Format=y
        ResourceDisk.Filesystem=ext4
        ResourceDisk.MountPoint=/mnt/resource
        ResourceDisk.EnableSwap=y
        ResourceDisk.SwapSizeMB=2048    ## NOTE: set this to whatever you need it to be.

1. サブスクリプションを登録解除する場合は、次のコマンドを実行します。

        # sudo subscription-manager unregister

1. 次のコマンドを実行して仮想マシンをプロビジョニング解除し、Azure でのプロビジョニング用に準備します。

        # Mote: if you are migrating a specific virtual machine and do not wish to create a generalized image,
        # skip the deprovision step
        # sudo waagent -force -deprovision

        # export HISTSIZE=0

        # logout

1. Hyper-V マネージャーで **[アクション]**  >  **[シャットダウン]** の順にクリックします。 これで、Linux VHD を Azure にアップロードする準備が整いました。


## <a name="prepare-a-red-hat-based-virtual-machine-from-kvm"></a>KVM からの Red Hat ベースの仮想マシンの準備
### <a name="prepare-a-rhel-6-virtual-machine-from-kvm"></a>KVM からの RHEL 6 仮想マシンの準備

1. Red Hat の Web サイトから、RHEL 6 の KVM イメージをダウンロードします。

1. ルート パスワードを設定します。

    暗号化されたパスワードを生成し、コマンドの出力をコピーします。

        # openssl passwd -1 changeme

    guestfish でルート パスワードを設定します。
        
        # guestfish --rw -a <image-name>
        > <fs> run
        > <fs> list-filesystems
        > <fs> mount /dev/sda1 /
        > <fs> vi /etc/shadow
        > <fs> exit

   ルート ユーザーの 2 番目のフィールドを、"!!" から 暗号化されたパスワードに変更します。

1. qcow2 イメージから KVM の仮想マシンを作成します。 ディスクの種類を **qcow2** に設定して、仮想ネットワーク インターフェイスのデバイス モデルを **virtio** に設定します。 その後、仮想マシンを起動し、root としてサインインします。

1. `/etc/sysconfig/network` ファイルを作成または編集して次のテキストを追加します。
   
        NETWORKING=yes
        HOSTNAME=localhost.localdomain

1. `/etc/sysconfig/network-scripts/ifcfg-eth0` ファイルを作成または編集して次のテキストを追加します。
   
        DEVICE=eth0
        ONBOOT=yes
        BOOTPROTO=dhcp
        TYPE=Ethernet
        USERCTL=no
        PEERDNS=yes
        IPV6INIT=no

1. udev ルールを移動 (または削除) して、イーサネット インターフェイスの静的ルールが生成されないようにします。 これらのルールは、Azure または Hyper-V で仮想マシンを複製する際に問題の原因となります。

        # sudo ln -s /dev/null /etc/udev/rules.d/75-persistent-net-generator.rules

        # sudo rm -f /etc/udev/rules.d/70-persistent-net.rules

1. 次のコマンドを実行して、起動時にネットワーク サービスが開始されるようにします。

        # chkconfig network on

1. RHEL リポジトリからパッケージをインストールできるように、次のコマンドを実行して Red Hat のサブスクリプションを登録します。

        # subscription-manager register --auto-attach --username=XXX --password=XXX

1. GRUB 構成でカーネルのブート行を変更して Azure の追加のカーネル パラメーターを含めます。 この構成を行うには、テキスト エディターで `/boot/grub/menu.lst` を開き、既定のカーネルに次のパラメーターが含まれていることを確認します。
    
        console=ttyS0 earlyprintk=ttyS0 rootdelay=300
    
    これにより、すべてのコンソール メッセージが最初のシリアル ポートに送信され、メッセージを Azure での問題のデバッグに利用できるようになります。
    
    上記の他に、次のパラメーターを削除することをお勧めします。
    
        rhgb quiet crashkernel=auto
    
    クラウド環境では、すべてのログをシリアル ポートに送信するため、グラフィカル ブートおよびクワイエット ブートは役立ちません。 `crashkernel` オプションの構成は、必要であればそのままにしてかまいません。 ただし、このパラメーターにより、仮想マシン内の使用可能なメモリ量が 128 MB 以上減少します。仮想マシンのサイズが小さいと、このことが問題になる可能性があります。


1. Hyper-V モジュールを initramfs に追加します。  

    `/etc/dracut.conf` を編集し、次の内容を追加します。

        add_drivers+=" hv_vmbus hv_netvsc hv_storvsc "

    initramfs を再構築します。

        # dracut -f -v

1. cloud-init をアンインストールします。

        # yum remove cloud-init

1. SSH サーバーがインストールされており、起動時に開始するように構成されていることを確認します。

        # chkconfig sshd on

    /etc/ssh/sshd_config を変更して、次の行を含めます。

        PasswordAuthentication yes
        ClientAliveInterval 180

1. WALinuxAgent パッケージ `WALinuxAgent-<version>` が Red Hat extras リポジトリにプッシュされました。 次のコマンドを実行して extras リポジトリを有効にします。

        # subscription-manager repos --enable=rhel-6-server-extras-rpms

1. 次のコマンドを実行して Azure Linux エージェントをインストールします。

        # yum install WALinuxAgent

        # chkconfig waagent on

1. Azure Linux エージェントは、Azure で仮想マシンがプロビジョニングされた後に仮想マシンに接続されたローカルのリソース ディスクを使用してスワップ領域を自動的に構成できます。 ローカル リソース ディスクは一時ディスクであるため、仮想マシンのプロビジョニングが解除されると空になる場合があることにご注意ください。 前の手順で Azure Linux エージェントのインストール後に、 **/etc/waagent.conf** にある次のパラメーターを適切に変更します。

        ResourceDisk.Format=y
        ResourceDisk.Filesystem=ext4
        ResourceDisk.MountPoint=/mnt/resource
        ResourceDisk.EnableSwap=y
        ResourceDisk.SwapSizeMB=2048    ## NOTE: set this to whatever you need it to be.

1. 次のコマンドを実行して、サブスクリプションを登録解除します (必要な場合)。

        # subscription-manager unregister

1. 次のコマンドを実行して仮想マシンをプロビジョニング解除し、Azure でのプロビジョニング用に準備します。

        # Mote: if you are migrating a specific virtual machine and do not wish to create a generalized image,
        # skip the deprovision step
        # waagent -force -deprovision

        # export HISTSIZE=0

        # logout

1. KVM で仮想マシンをシャットダウンします。

1. qcow2 イメージを VHD 形式に変換します。

> [!NOTE]
> qemu-img のバージョン 2.2.1 以降には VHD が適切にフォーマットされないというバグがあることがわかっています。 この問題は QEMU 2.6 で修正されています。 qemu-img 2.2.0 以前を使用するか、2.6 以降に更新することをお勧めします。 [https://bugs.launchpad.net/qemu/+bug/1490611](https://bugs.launchpad.net/qemu/+bug/1490611 ) を参照してください。
>


    First convert the image to raw format:

        # qemu-img convert -f qcow2 -O raw rhel-6.9.qcow2 rhel-6.9.raw

    Make sure that the size of the raw image is aligned with 1 MB. Otherwise, round up the size to align with 1 MB:

        # MB=$((1024*1024))
        # size=$(qemu-img info -f raw --output json "rhel-6.9.raw" | \
          gawk 'match($0, /"virtual-size": ([0-9]+),/, val) {print val[1]}')

        # rounded_size=$((($size/$MB + 1)*$MB))
        # qemu-img resize rhel-6.9.raw $rounded_size

    Convert the raw disk to a fixed-sized VHD:

        # qemu-img convert -f raw -o subformat=fixed -O vpc rhel-6.9.raw rhel-6.9.vhd

    Or, with qemu version **2.6+** include the `force_size` option:

        # qemu-img convert -f raw -o subformat=fixed,force_size -O vpc rhel-6.9.raw rhel-6.9.vhd

        
### <a name="prepare-a-rhel-7-virtual-machine-from-kvm"></a>KVM からの RHEL 7 仮想マシンの準備

1. Red Hat の Web サイトから、RHEL 7 の KVM イメージをダウンロードします。 この手順では、例として RHEL 7 を使用します。

1. ルート パスワードを設定します。

    暗号化されたパスワードを生成し、コマンドの出力をコピーします。

        # openssl passwd -1 changeme

    guestfish でルート パスワードを設定します。

        # guestfish --rw -a <image-name>
        > <fs> run
        > <fs> list-filesystems
        > <fs> mount /dev/sda1 /
        > <fs> vi /etc/shadow
        > <fs> exit

   ルート ユーザーの 2 番目のフィールドを、"!!" から 暗号化されたパスワードに変更します。

1. qcow2 イメージから KVM の仮想マシンを作成します。 ディスクの種類を **qcow2** に設定して、仮想ネットワーク インターフェイスのデバイス モデルを **virtio** に設定します。 その後、仮想マシンを起動し、root としてサインインします。

1. `/etc/sysconfig/network` ファイルを作成または編集して次のテキストを追加します。
   
        NETWORKING=yes
        HOSTNAME=localhost.localdomain

1. `/etc/sysconfig/network-scripts/ifcfg-eth0` ファイルを作成または編集して次のテキストを追加します。
   
        DEVICE=eth0
        ONBOOT=yes
        BOOTPROTO=dhcp
        TYPE=Ethernet
        USERCTL=no
        PEERDNS=yes
        IPV6INIT=no
        NM_CONTROLLED=no

1. 次のコマンドを実行して、起動時にネットワーク サービスが開始されるようにします。

        # sudo systemctl enable network

1. RHEL リポジトリからパッケージをインストールできるように、次のコマンドを実行して Red Hat のサブスクリプションを登録します。

        # subscription-manager register --auto-attach --username=XXX --password=XXX

1. GRUB 構成でカーネルのブート行を変更して Azure の追加のカーネル パラメーターを含めます。 この構成を行うには、テキスト エディターで `/etc/default/grub` を開き、`GRUB_CMDLINE_LINUX` パラメーターを編集します。 例:
   
        GRUB_CMDLINE_LINUX="rootdelay=300 console=ttyS0 earlyprintk=ttyS0 net.ifnames=0"
   
   このコマンドにより、すべてのコンソール メッセージが最初のシリアル ポートに送信され、メッセージを Azure での問題のデバッグに利用できるようになります。 NIC の新しい RHEL 7 名前付け規則も、このコマンドでオフになります。 上記の他に、次のパラメーターを削除することをお勧めします。
   
        rhgb quiet crashkernel=auto
   
    クラウド環境では、すべてのログをシリアル ポートに送信するため、グラフィカル ブートおよびクワイエット ブートは役立ちません。 `crashkernel` オプションの構成は、必要であればそのままにしてかまいません。 ただし、このパラメーターにより、仮想マシン内の使用可能なメモリ量が 128 MB 以上減少します。仮想マシンのサイズが小さいと、このことが問題になる可能性があります。

1. `/etc/default/grub`の編集を終了したら、次のコマンドを実行して GRUB 構成を再構築します。

        # grub2-mkconfig -o /boot/grub2/grub.cfg

1. Hyper-V モジュールを initramfs に追加します。

    `/etc/dracut.conf` を編集して、コンテンツを追加します。

        add_drivers+=" hv_vmbus hv_netvsc hv_storvsc "

    initramfs を再構築します。

        # dracut -f -v

1. cloud-init をアンインストールします。

        # yum remove cloud-init

1. SSH サーバーがインストールされており、起動時に開始するように構成されていることを確認します。

        # systemctl enable sshd

    /etc/ssh/sshd_config を変更して、次の行を含めます。

        PasswordAuthentication yes
        ClientAliveInterval 180

1. WALinuxAgent パッケージ `WALinuxAgent-<version>` が Red Hat extras リポジトリにプッシュされました。 次のコマンドを実行して extras リポジトリを有効にします。

        # subscription-manager repos --enable=rhel-7-server-extras-rpms

1. 次のコマンドを実行して Azure Linux エージェントをインストールします。

        # yum install WALinuxAgent

    waagent サービスを有効にします。

        # systemctl enable waagent.service

1. オペレーティング システム ディスクにスワップ領域を作成しないでください。

    Azure Linux エージェントは、Azure で仮想マシンがプロビジョニングされた後に仮想マシンに接続されたローカルのリソース ディスクを使用してスワップ領域を自動的に構成できます。 ローカル リソース ディスクは一時ディスクであるため、仮想マシンのプロビジョニングが解除されると空になる場合があることにご注意ください。 前の手順で Azure Linux エージェントのインストール後に、`/etc/waagent.conf` にある次のパラメーターを適切に変更します。

        ResourceDisk.Format=y
        ResourceDisk.Filesystem=ext4
        ResourceDisk.MountPoint=/mnt/resource
        ResourceDisk.EnableSwap=y
        ResourceDisk.SwapSizeMB=2048    ## NOTE: set this to whatever you need it to be.

1. 次のコマンドを実行して、サブスクリプションを登録解除します (必要な場合)。

        # subscription-manager unregister

1. 次のコマンドを実行して仮想マシンをプロビジョニング解除し、Azure でのプロビジョニング用に準備します。

        # Mote: if you are migrating a specific virtual machine and do not wish to create a generalized image,
        # skip the deprovision step
        # sudo waagent -force -deprovision

        # export HISTSIZE=0

        # logout

1. KVM で仮想マシンをシャットダウンします。

1. qcow2 イメージを VHD 形式に変換します。

> [!NOTE]
> qemu-img のバージョン 2.2.1 以降には VHD が適切にフォーマットされないというバグがあることがわかっています。 この問題は QEMU 2.6 で修正されています。 qemu-img 2.2.0 以前を使用するか、2.6 以降に更新することをお勧めします。 [https://bugs.launchpad.net/qemu/+bug/1490611](https://bugs.launchpad.net/qemu/+bug/1490611 ) を参照してください。
>


    First convert the image to raw format:

        # qemu-img convert -f qcow2 -O raw rhel-7.4.qcow2 rhel-7.4.raw

    Make sure that the size of the raw image is aligned with 1 MB. Otherwise, round up the size to align with 1 MB:

        # MB=$((1024*1024))
        # size=$(qemu-img info -f raw --output json "rhel-7.4.raw" | \
          gawk 'match($0, /"virtual-size": ([0-9]+),/, val) {print val[1]}')

        # rounded_size=$((($size/$MB + 1)*$MB))
        # qemu-img resize rhel-7.4.raw $rounded_size

    Convert the raw disk to a fixed-sized VHD:

        # qemu-img convert -f raw -o subformat=fixed -O vpc rhel-7.4.raw rhel-7.4.vhd

    Or, with qemu version **2.6+** include the `force_size` option:

        # qemu-img convert -f raw -o subformat=fixed,force_size -O vpc rhel-7.4.raw rhel-7.4.vhd


## <a name="prepare-a-red-hat-based-virtual-machine-from-vmware"></a>VMware からの Red Hat ベースの仮想マシンの準備
### <a name="prerequisites"></a>前提条件
このセクションでは、VMware に RHEL の仮想マシンが既にインストールされていると仮定します。 VMware にオペレーティング システムをインストールする方法の詳細については、[VMware のゲスト オペレーティング システムのインストール ガイド](https://partnerweb.vmware.com/GOSIG/home.html)を参照してください。

* Linux オペレーティング システムをインストールする場合は、LVM (通常、多くのインストールで既定) ではなく標準パーティションを使用することをお勧めします。 特にオペレーティング システム ディスクをトラブルシューティングのために別の仮想マシンに接続する必要がある場合、そうすることで、複製された仮想マシンとの LVM 名の競合を回避することができます。 必要な場合は、LVM または RAID をデータ ディスク上で使用できます。
* オペレーティング システム ディスクでスワップ パーティションを構成しないでください。 一時的なリソース ディスク上にスワップ ファイルを作成するよう Linux エージェントを構成できます。 この詳細については、次の手順を参照してください。
* 仮想ハード ディスクを作成する場合は、 **[Store virtual disk as a single file (仮想ディスクを 1 つのファイルとして格納する)]** を選択します。

### <a name="prepare-a-rhel-6-virtual-machine-from-vmware"></a>VMware からの RHEL 6 仮想マシンの準備
1. RHEL 6 では、NetworkManager が Azure Linux エージェントと干渉する可能性があります。 次のコマンドを実行して、このパッケージをアンインストールします。
   
        # sudo rpm -e --nodeps NetworkManager

1. /etc/sysconfig/ ディレクトリに **network** という名前のファイルを作成し、次のテキストを追加します。

        NETWORKING=yes
        HOSTNAME=localhost.localdomain

1. `/etc/sysconfig/network-scripts/ifcfg-eth0` ファイルを作成または編集して次のテキストを追加します。
   
        DEVICE=eth0
        ONBOOT=yes
        BOOTPROTO=dhcp
        TYPE=Ethernet
        USERCTL=no
        PEERDNS=yes
        IPV6INIT=no

1. udev ルールを移動 (または削除) して、イーサネット インターフェイスの静的ルールが生成されないようにします。 これらのルールは、Azure または Hyper-V で仮想マシンを複製する際に問題の原因となります。

        # sudo ln -s /dev/null /etc/udev/rules.d/75-persistent-net-generator.rules

        # sudo rm -f /etc/udev/rules.d/70-persistent-net.rules

1. 次のコマンドを実行して、起動時にネットワーク サービスが開始されるようにします。

        # sudo chkconfig network on

1. RHEL リポジトリからパッケージをインストールできるように、次のコマンドを実行して Red Hat のサブスクリプションを登録します。

        # sudo subscription-manager register --auto-attach --username=XXX --password=XXX

1. WALinuxAgent パッケージ `WALinuxAgent-<version>` が Red Hat extras リポジトリにプッシュされました。 次のコマンドを実行して extras リポジトリを有効にします。

        # subscription-manager repos --enable=rhel-6-server-extras-rpms

1. GRUB 構成でカーネルのブート行を変更して Azure の追加のカーネル パラメーターを含めます。 これを行うには、テキスト エディターで `/etc/default/grub` を開き、`GRUB_CMDLINE_LINUX` パラメーターを編集します。 例:
   
        GRUB_CMDLINE_LINUX="rootdelay=300 console=ttyS0 earlyprintk=ttyS0"
   
   これにより、すべてのコンソール メッセージが最初のシリアル ポートに送信され、メッセージを Azure での問題のデバッグに利用できるようになります。 上記の他に、次のパラメーターを削除することをお勧めします。
   
        rhgb quiet crashkernel=auto
   
    クラウド環境では、すべてのログをシリアル ポートに送信するため、グラフィカル ブートおよびクワイエット ブートは役立ちません。 `crashkernel` オプションの構成は、必要であればそのままにしてかまいません。 ただし、このパラメーターにより、仮想マシン内の使用可能なメモリ量が 128 MB 以上減少します。仮想マシンのサイズが小さいと、このことが問題になる可能性があります。

1. Hyper-V モジュールを initramfs に追加します。

    `/etc/dracut.conf` を編集し、次の内容を追加します。

        add_drivers+=" hv_vmbus hv_netvsc hv_storvsc "

    initramfs を再構築します。

        # dracut -f -v

1. SSH サーバーがインストールされており、起動時に開始するように構成されていることを確認します。通常は、既定でそのように構成されています。 `/etc/ssh/sshd_config` を変更して、次の行を含めます。

    ClientAliveInterval 180

1. 次のコマンドを実行して Azure Linux エージェントをインストールします。

        # sudo yum install WALinuxAgent

        # sudo chkconfig waagent on

1. オペレーティング システム ディスクにスワップ領域を作成しないでください。

    Azure Linux エージェントは、Azure で仮想マシンがプロビジョニングされた後に仮想マシンに接続されたローカルのリソース ディスクを使用してスワップ領域を自動的に構成できます。 ローカル リソース ディスクは一時ディスクであるため、仮想マシンのプロビジョニングが解除されると空になる場合があることにご注意ください。 前の手順で Azure Linux エージェントのインストール後に、`/etc/waagent.conf` にある次のパラメーターを適切に変更します。

        ResourceDisk.Format=y
        ResourceDisk.Filesystem=ext4
        ResourceDisk.MountPoint=/mnt/resource
        ResourceDisk.EnableSwap=y
        ResourceDisk.SwapSizeMB=2048    ## NOTE: set this to whatever you need it to be.

1. 次のコマンドを実行して、サブスクリプションを登録解除します (必要な場合)。

        # sudo subscription-manager unregister

1. 次のコマンドを実行して仮想マシンをプロビジョニング解除し、Azure でのプロビジョニング用に準備します。

        # Mote: if you are migrating a specific virtual machine and do not wish to create a generalized image,
        # skip the deprovision step
        # sudo waagent -force -deprovision

        # export HISTSIZE=0

        # logout

1. 仮想マシンをシャットダウンし、VMDK ファイルを .vhd ファイルに変換します。

> [!NOTE]
> qemu-img のバージョン 2.2.1 以降には VHD が適切にフォーマットされないというバグがあることがわかっています。 この問題は QEMU 2.6 で修正されています。 qemu-img 2.2.0 以前を使用するか、2.6 以降に更新することをお勧めします。 [https://bugs.launchpad.net/qemu/+bug/1490611](https://bugs.launchpad.net/qemu/+bug/1490611 ) を参照してください。
>


    First convert the image to raw format:

        # qemu-img convert -f vmdk -O raw rhel-6.9.vmdk rhel-6.9.raw

    Make sure that the size of the raw image is aligned with 1 MB. Otherwise, round up the size to align with 1 MB:

        # MB=$((1024*1024))
        # size=$(qemu-img info -f raw --output json "rhel-6.9.raw" | \
          gawk 'match($0, /"virtual-size": ([0-9]+),/, val) {print val[1]}')

        # rounded_size=$((($size/$MB + 1)*$MB))
        # qemu-img resize rhel-6.9.raw $rounded_size

    Convert the raw disk to a fixed-sized VHD:

        # qemu-img convert -f raw -o subformat=fixed -O vpc rhel-6.9.raw rhel-6.9.vhd

    Or, with qemu version **2.6+** include the `force_size` option:

        # qemu-img convert -f raw -o subformat=fixed,force_size -O vpc rhel-6.9.raw rhel-6.9.vhd


### <a name="prepare-a-rhel-7-virtual-machine-from-vmware"></a>VMware からの RHEL 7 仮想マシンの準備
1. `/etc/sysconfig/network` ファイルを作成または編集して次のテキストを追加します。
   
        NETWORKING=yes
        HOSTNAME=localhost.localdomain

1. `/etc/sysconfig/network-scripts/ifcfg-eth0` ファイルを作成または編集して次のテキストを追加します。
   
        DEVICE=eth0
        ONBOOT=yes
        BOOTPROTO=dhcp
        TYPE=Ethernet
        USERCTL=no
        PEERDNS=yes
        IPV6INIT=no
        NM_CONTROLLED=no

1. 次のコマンドを実行して、起動時にネットワーク サービスが開始されるようにします。

        # sudo systemctl enable network

1. RHEL リポジトリからパッケージをインストールできるように、次のコマンドを実行して Red Hat のサブスクリプションを登録します。

        # sudo subscription-manager register --auto-attach --username=XXX --password=XXX

1. GRUB 構成でカーネルのブート行を変更して Azure の追加のカーネル パラメーターを含めます。 この変更を行うには、テキスト エディターで `/etc/default/grub` を開き、`GRUB_CMDLINE_LINUX` パラメーターを編集します。 例:
   
        GRUB_CMDLINE_LINUX="rootdelay=300 console=ttyS0 earlyprintk=ttyS0 net.ifnames=0"
   
   この構成により、すべてのコンソール メッセージが最初のシリアル ポートに送信され、メッセージを Azure での問題のデバッグに利用できるようになります。 NIC の新しい RHEL 7 名前付け規則もオフになります。 上記の他に、次のパラメーターを削除することをお勧めします。
   
        rhgb quiet crashkernel=auto
   
    クラウド環境では、すべてのログをシリアル ポートに送信するため、グラフィカル ブートおよびクワイエット ブートは役立ちません。 `crashkernel` オプションの構成は、必要であればそのままにしてかまいません。 ただし、このパラメーターにより、仮想マシン内の使用可能なメモリ量が 128 MB 以上減少します。仮想マシンのサイズが小さいと、このことが問題になる可能性があります。

1. `/etc/default/grub`の編集を終了したら、次のコマンドを実行して GRUB 構成を再構築します。

        # sudo grub2-mkconfig -o /boot/grub2/grub.cfg

1. Hyper-V モジュールを initramfs に追加します。

    `/etc/dracut.conf`を編集して、コンテンツを追加します。

        add_drivers+=" hv_vmbus hv_netvsc hv_storvsc "

    initramfs を再構築します。

        # dracut -f -v

1. SSH サーバーがインストールされており、起動時に開始するように構成されていることを確認します。 通常これが既定の設定です。 `/etc/ssh/sshd_config` を変更して、次の行を含めます。

        ClientAliveInterval 180

1. WALinuxAgent パッケージ `WALinuxAgent-<version>` が Red Hat extras リポジトリにプッシュされました。 次のコマンドを実行して extras リポジトリを有効にします。

        # subscription-manager repos --enable=rhel-7-server-extras-rpms

1. 次のコマンドを実行して Azure Linux エージェントをインストールします。

        # sudo yum install WALinuxAgent

        # sudo systemctl enable waagent.service

1. オペレーティング システム ディスクにスワップ領域を作成しないでください。

    Azure Linux エージェントは、Azure で仮想マシンがプロビジョニングされた後に仮想マシンに接続されたローカルのリソース ディスクを使用してスワップ領域を自動的に構成できます。 ローカル リソース ディスクは一時ディスクであるため、仮想マシンのプロビジョニングが解除されると空になる場合があることにご注意ください。 前の手順で Azure Linux エージェントのインストール後に、`/etc/waagent.conf` にある次のパラメーターを適切に変更します。

        ResourceDisk.Format=y
        ResourceDisk.Filesystem=ext4
        ResourceDisk.MountPoint=/mnt/resource
        ResourceDisk.EnableSwap=y
        ResourceDisk.SwapSizeMB=2048    ## NOTE: set this to whatever you need it to be.

1. サブスクリプションを登録解除する場合は、次のコマンドを実行します。

        # sudo subscription-manager unregister

1. 次のコマンドを実行して仮想マシンをプロビジョニング解除し、Azure でのプロビジョニング用に準備します。

        # Mote: if you are migrating a specific virtual machine and do not wish to create a generalized image,
        # skip the deprovision step
        # sudo waagent -force -deprovision

        # export HISTSIZE=0

        # logout

1. 仮想マシンをシャットダウンし、VMDK ファイルを VHD 形式に変換します。

> [!NOTE]
> qemu-img のバージョン 2.2.1 以降には VHD が適切にフォーマットされないというバグがあることがわかっています。 この問題は QEMU 2.6 で修正されています。 qemu-img 2.2.0 以前を使用するか、2.6 以降に更新することをお勧めします。 [https://bugs.launchpad.net/qemu/+bug/1490611](https://bugs.launchpad.net/qemu/+bug/1490611 ) を参照してください。
>


    First convert the image to raw format:

        # qemu-img convert -f vmdk -O raw rhel-7.4.vmdk rhel-7.4.raw

    Make sure that the size of the raw image is aligned with 1 MB. Otherwise, round up the size to align with 1 MB:

        # MB=$((1024*1024))
        # size=$(qemu-img info -f raw --output json "rhel-7.4.raw" | \
          gawk 'match($0, /"virtual-size": ([0-9]+),/, val) {print val[1]}')

        # rounded_size=$((($size/$MB + 1)*$MB))
        # qemu-img resize rhel-7.4.raw $rounded_size

    Convert the raw disk to a fixed-sized VHD:

        # qemu-img convert -f raw -o subformat=fixed -O vpc rhel-7.4.raw rhel-7.4.vhd

    Or, with qemu version **2.6+** include the `force_size` option:

        # qemu-img convert -f raw -o subformat=fixed,force_size -O vpc rhel-7.4.raw rhel-7.4.vhd


## <a name="prepare-a-red-hat-based-virtual-machine-from-an-iso-by-using-a-kickstart-file-automatically"></a>kickstart ファイルを使用して ISO から Red Hat ベースの仮想マシンを自動的に準備する
### <a name="prepare-a-rhel-7-virtual-machine-from-a-kickstart-file"></a>kickstart ファイルからの RHEL 7 仮想マシンの準備

1.  次の内容を含んだ kickstart ファイルを作成し、そのファイルを保存します。 kickstart のインストールの詳細については、 [kickstart インストール ガイド](https://access.redhat.com/documentation/en-US/Red_Hat_Enterprise_Linux/7/html/Installation_Guide/chap-kickstart-installations.html)を参照してください。

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

        # Disable the root account
        usermod root -p '!!'

        # Configure swap in WALinuxAgent
        sed -i 's/^\(ResourceDisk\.EnableSwap\)=[Nn]$/\1=y/g' /etc/waagent.conf
        sed -i 's/^\(ResourceDisk\.SwapSizeMB\)=[0-9]*$/\1=2048/g' /etc/waagent.conf

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
        NM_CONTROLLED=no
        EOF

        # Deprovision and prepare for Azure if you are creating a generalized image
        waagent -force -deprovision

        %end

1. インストール システムからアクセスできる場所に kickstart ファイルを置きます。

1. Hyper-V マネージャーで新しい仮想マシンを作成します。 **[仮想ハード ディスクの接続]** ページで、 **[後で仮想ハード ディスクを接続する]** を選択し、仮想マシンの新規作成ウィザードを完了します。

1. 仮想マシンの設定を開きます。

    a.  新しい仮想ハード ディスクを仮想マシンに接続します。 **[VHD 形式]** と **[固定サイズ]** を選択します。

    b.  インストール ISO を DVD ドライブに接続します。

    c.  CD から起動するように BIOS を設定します。

1. 仮想マシンを開始します。 インストール ガイドが表示されたら、 **Tab** キーを押してブート オプションを構成します。

1. ブート オプションの最後に `inst.ks=<the location of the kickstart file>` を入力し、 **Enter**キーを押します。

1. インストールが完了するのを待ちます。 完了すると、仮想マシンが自動的にシャットダウンされます。 これで、Linux VHD を Azure にアップロードする準備が整いました。

## <a name="known-issues"></a>既知の問題
### <a name="the-hyper-v-driver-could-not-be-included-in-the-initial-ram-disk-when-using-a-non-hyper-v-hypervisor"></a>非 Hyper-V ハイパーバイザーの使用時に Hyper-V ドライバーを初期 RAM ディスクに含めることができない

Hyper-V 環境で実行されていることを Linux が検出しなかった場合、Linux インストーラーは、初期 RAM ディスク (initrd または initramfs) に Hyper-V 用のドライバーを追加しないことがあります。

別の仮想化システム (Virtualbox、Xen など) を使用して Linux イメージを準備する場合は、少なくとも hv_vmbus と hv_storvsc のカーネル モジュールを初期 RAM ディスクで使用できるように initrd の再構築が必要になる場合があります。 これは少なくとも、アップストリームの Red Hat ディストリビューションに基づくシステムの既知の問題です。

この問題を解決するには、initramfs に Hyper-V モジュールを追加して再構築する必要があります。

`/etc/dracut.conf` を編集し、次の内容を追加します。

        add_drivers+=" hv_vmbus hv_netvsc hv_storvsc "

initramfs を再構築します。

        # dracut -f -v

詳細については、 [initramfs の再構築](https://access.redhat.com/solutions/1958)に関する情報を参照してください。

## <a name="next-steps"></a>次の手順
これで、Red Hat Enterprise Linux 仮想ハード ディスク を使用して、Azure に新しい仮想マシンを作成する準備が整いました。 .vhd ファイルを Azure に初めてアップロードする場合は、「[Create a Linux VM from a custom disk (カスタム ディスクから Linux VM を作成する)](upload-vhd.md#option-1-upload-a-vhd)」を参照してください。

Red Hat Enterprise Linux の実行が認定されているハイパーバイザーの詳細については、 [Red Hat の Web サイト](https://access.redhat.com/certified-hypervisors)を参照してください。
