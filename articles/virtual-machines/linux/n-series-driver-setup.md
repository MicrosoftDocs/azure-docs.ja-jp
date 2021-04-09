---
title: Linux 用 Azure N シリーズ GPU ドライバーのセットアップ
description: Azure で Linux を実行する N シリーズ VM 用の NVIDIA GPU ドライバーの設定方法を説明します。
services: virtual-machines
author: vikancha-MSFT
ms.service: virtual-machines
ms.subervice: vm-sizes-gpu
ms.collection: linux
ms.topic: how-to
ms.workload: infrastructure-services
ms.date: 11/11/2019
ms.author: vikancha
ms.openlocfilehash: c4c6bee6d3f9e423d83458ad48d213fe65223514
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/30/2021
ms.locfileid: "102551762"
---
# <a name="install-nvidia-gpu-drivers-on-n-series-vms-running-linux"></a>Linux を実行している N シリーズ VM に NVIDIA GPU ドライバーをインストールする

NVIDIA GPU を動力源とする Azure N シリーズ VM の GPU 機能を利用するには、NVIDIA GPU ドライバーをインストールする必要があります。 [NVIDIA GPU ドライバー拡張機能](../extensions/hpccompute-gpu-linux.md)は、N シリーズ VM 上に適切な NVIDIA CUDA または GRID ドライバーをインストールします。 この拡張機能は、Azure Portal または Azure CLI や Azure Resource Manager テンプレートなどのツールを使用してインストールまたは管理します。 サポートされるディストリビューションおよびデプロイ手順については、[NVIDIA GPU ドライバー拡張機能のドキュメント](../extensions/hpccompute-gpu-linux.md)を参照してください。

NVIDIA GPU ドライバーを手動でインストールすることを選択した場合、この記事では、サポートされるディストリビューション、ドライバー、インストールおよび検証手順について説明します。 手動でのドライバーのセットアップ情報は、[Windows VM](../windows/n-series-driver-setup.md) でも利用可能です。

N シリーズ VM の仕様、ストレージの容量、およびディスクの詳細については、「[GPU Linux VM のサイズ](../sizes-gpu.md?toc=/azure/virtual-machines/linux/toc.json)」を参照してください。 

[!INCLUDE [virtual-machines-n-series-linux-support](../../../includes/virtual-machines-n-series-linux-support.md)]

## <a name="install-cuda-drivers-on-n-series-vms"></a>N シリーズ VM への CUDA ドライバーのインストール

NVIDIA CUDA Toolkit から N シリーズ VM に CUDA ドライバーをインストールする手順は次のとおりです。 

C および C++ の開発者は、GPU アクセラレータを使用したアプリケーションを構築するために、必要に応じて Toolkit 全体をインストールできます。 詳細については、[CUDA インストール ガイド](https://docs.nvidia.com/cuda/cuda-installation-guide-linux/index.html)を参照してください。

CUDA ドライバーをインストールするには、各 VM への SSH 接続を作成します。 システムに CUDA 対応の GPU が備わっているかどうかを確認するには、次のコマンドを実行します。

```bash
lspci | grep -i NVIDIA
```
次の例のような出力が表示されます (NVIDIA Tesla K80 カードが表示されています)。

![lspci コマンドの出力](./media/n-series-driver-setup/lspci.png)

lspci では、VM 上の PCIe デバイス (InfiniBand NIC と GPU を含む) の一覧が表示されます (存在する場合)。 lspci で正常に返されない場合は、CentOS/RHEL への LIS のインストールが必要な場合があります (以下の手順を参照)。
次に、ディストリビューションに固有のインストール コマンドを実行します。

### <a name="ubuntu"></a>Ubuntu 

1. NVIDIA Web サイトから CUDA ドライバーをダウンロードしてインストールします。 
    > [!NOTE]
   >  次の例は、Ubuntu 16.04 の CUDA パッケージ パスを示しています。 使用する予定のバージョンに固有のパスを置き換えます。 
   >  
   >  各バージョンに固有の完全なパスについては、[Nvidia ダウンロード センター] (https://developer.download.nvidia.com/compute/cuda/repos/) を参照してください。 
   > 
   ```bash
   CUDA_REPO_PKG=cuda-repo-ubuntu1604_10.0.130-1_amd64.deb
   wget -O /tmp/${CUDA_REPO_PKG} https://developer.download.nvidia.com/compute/cuda/repos/ubuntu1604/x86_64/${CUDA_REPO_PKG} 

   sudo dpkg -i /tmp/${CUDA_REPO_PKG}
   sudo apt-key adv --fetch-keys https://developer.download.nvidia.com/compute/cuda/repos/ubuntu1604/x86_64/7fa2af80.pub 
   rm -f /tmp/${CUDA_REPO_PKG}

   sudo apt-get update
   sudo apt-get install cuda-drivers
   ```

   インストールには数分かかる場合があります。
 

2. 必要に応じて完全な CUDA ツールキットをインストールするには、次のように入力します。

   ```bash
   sudo apt-get install cuda
   ```

3. VM を再起動して、インストールの確認に進みます。

#### <a name="cuda-driver-updates"></a>CUDA ドライバーの更新

デプロイ後は、定期的に CUDA ドライバーを更新することをお勧めします。

```bash
sudo apt-get update
sudo apt-get upgrade -y
sudo apt-get dist-upgrade -y
sudo apt-get install cuda-drivers

sudo reboot
```

### <a name="centos-or-red-hat-enterprise-linux"></a>CentOS または Red Hat Enterprise Linux

1. カーネルを更新します (推奨)。 カーネルを更新しない場合は、`kernel-devel` と `dkms` のバージョンがカーネルに対して確実に適切であるようにします。

   ```
   sudo yum install kernel kernel-tools kernel-headers kernel-devel
   sudo reboot
   ```

2. [Hyper-V と Azure 用の最新 Linux Integration Services](https://www.microsoft.com/download/details.aspx?id=55106) をインストールします。 lspci の結果を検証して、LIS が必要かどうかを確認します。 すべての GPU デバイスが想定どおりに (および前述のとおりに) 一覧表示されている場合は、LIS をインストールする必要はありません。

   LIS は Red Hat Enterprise Linux、CentOS、Oracle Linux Red Hat 互換カーネル 5.2 - 5.11、6.0 - 6.10、7.0 - 7.7 などに適用されることに注意してください。 詳細については、[Linux Integration Services のドキュメント] (https://www.microsoft.com/en-us/download/details.aspx?id=55106) を参照してください。 
   CentOS/RHEL 7.8 (またはそれ以降のバージョン) を使用する予定の場合、この手順はスキップしてください。これは、これらのバージョンでは LIS が不要になったためです。

      ```bash
      wget https://aka.ms/lis
      tar xvzf lis
      cd LISISO

      sudo ./install.sh
      sudo reboot
      ```

3. VM に再接続し、次のコマンドを使用してインストールを続行します。

   ```bash
   sudo rpm -Uvh https://dl.fedoraproject.org/pub/epel/epel-release-latest-7.noarch.rpm
   sudo yum install dkms
   
   CUDA_REPO_PKG=cuda-repo-rhel7-10.0.130-1.x86_64.rpm
   wget https://developer.download.nvidia.com/compute/cuda/repos/rhel7/x86_64/${CUDA_REPO_PKG} -O /tmp/${CUDA_REPO_PKG}

   sudo rpm -ivh /tmp/${CUDA_REPO_PKG}
   rm -f /tmp/${CUDA_REPO_PKG}

   sudo yum install cuda-drivers
   ```

   インストールには数分かかる場合があります。 

4. 必要に応じて完全な CUDA ツールキットをインストールするには、次のように入力します。

   ```bash
   sudo yum install cuda
   ```
   > [!NOTE]
   >  vulkan-filesystem などの不足しているパッケージに関連するエラー メッセージが表示された場合は、/etc/yum.repos.d/rh-cloud を編集して、optional-rpms を探し、enabled を 1 に設定する必要があることがあります。
   >  

5. VM を再起動して、インストールの確認に進みます。

### <a name="verify-driver-installation"></a>ドライバーのインストールの確認

GPU デバイスの状態を照会するには、VM に SSH 接続し、ドライバーと共にインストールされる [nvidia-smi](https://developer.nvidia.com/nvidia-system-management-interface) コマンド ライン ユーティリティを実行します。 

ドライバーがインストールされると、次のような出力が表示されます。 **GPU-Util** は、その時点で VM で GPU ワークロードを実行していない限り、0% と表示されます。 ドライバーのバージョンと GPU の詳細は、次の表示と異なる場合があります。

![NVIDIA デバイスの状態](./media/n-series-driver-setup/smi.png)

## <a name="rdma-network-connectivity"></a>RDMA ネットワーク接続

RDMA ネットワーク接続は、同じ可用性セット内にデプロイまたは仮想マシン (VM) スケール セット内の単一の配置グループにデプロイされた NC24r など、RDMA 対応の N シリーズ VM で有効にすることができます。 RDMA ネットワークは、Intel MPI 5.x 以降のバージョンで実行しているアプリケーションに対して、Message Passing Interface (MPI) トラフィックをサポートしています。 その他の要件は次のとおりです。

### <a name="distributions"></a>ディストリビューション

N シリーズ VM で RDMA 接続をサポートする Azure Marketplace で、次のイメージの 1 つから RDMA 対応の N シリーズ VM をデプロイします。
  
* **Ubuntu 16.04 LTS** - VM で RDMA ドライバーを構成し、Intel に登録して Intel MPI をダウンロードします。

  [!INCLUDE [virtual-machines-common-ubuntu-rdma](../../../includes/virtual-machines-common-ubuntu-rdma.md)]

* **CentOS ベースの 7.4 HPC** - RDMA ドライバーおよび Intel MPI 5.1 は、VM にインストールされます。

* **CentOS ベースの HPC** - CentOS-HPC 7.6 以降 (InfiniBand が SR-IOV 経由でサポートされている SKU の場合)。 これらのイメージには、Mellanox OFED と MPI ライブラリが事前にインストールされています。

> [!NOTE]
> CX3-Pro カードは、Mellanox OFED の LTS バージョンによってのみサポートされます。 ConnectX3-Pro カードを搭載する N シリーズ VM では、LTS Mellanox OFED バージョン (4.9-0.1.7.0) を使用してください。 詳細については、[Linux ドライバー](https://www.mellanox.com/products/infiniband-drivers/linux/mlnx_ofed)に関するページを参照してください。
>
> また、最新の Azure Marketplace HPC イメージの一部には、ConnectX3-Pro カードをサポートしていない Mellanox OFED 5.1 以降が使用されています。 ConnectX3-Pro カードを搭載する VM で使用する前に、HPC イメージの Mellanox OFED のバージョンを確認してください。
>
> 次の図は、ConnectX3-Pro カードをサポートする最新の CentOS-HPC イメージです。
>
> - OpenLogic:CentOS-HPC:7.6:7.6.2020062900
> - OpenLogic:CentOS-HPC:7_6gen2:7.6.2020062901
> - OpenLogic:CentOS-HPC:7.7:7.7.2020062600
> - OpenLogic:CentOS-HPC:7_7-gen2:7.7.2020062601
> - OpenLogic:CentOS-HPC:8_1:8.1.2020062400
> - OpenLogic:CentOS-HPC:8_1-gen2:8.1.2020062401
>

## <a name="install-grid-drivers-on-nv-or-nvv3-series-vms"></a>NV または NVv3 シリーズの VM に GRID ドライバーをインストールする

NV シリーズまたは NVv3 シリーズの VM に NVIDIA GRID ドライバーをインストールするには、各 VM への SSH 接続を作成して、Linux ディストリビューションに応じた手順に従います。 

### <a name="ubuntu"></a>Ubuntu 

1. `lspci` コマンドを実行します。 NVIDIA M60 カードが PCI デバイスとして表示されていることを確認します。

2. 更新プログラムをインストールします。

   ```bash
   sudo apt-get update
   sudo apt-get upgrade -y
   sudo apt-get dist-upgrade -y
   sudo apt-get install build-essential ubuntu-desktop -y
   sudo apt-get install linux-azure -y
   ```
3. NVIDIA ドライバーと互換性がない、Nouveau カーネル ドライバーを無効にします (NV または NVv2 の VM では NVIDIA ドライバーのみを使用)。これを行うには、次の内容を含む `nouveau.conf` という名前のファイルを `/etc/modprobe.d` に作成します。

   ```
   blacklist nouveau
   blacklist lbm-nouveau
   ```


4. VM を再起動し、再接続します。 X サーバーを終了します。

   ```bash
   sudo systemctl stop lightdm.service
   ```

5. GRID ドライバーをダウンロードしてインストールします。

   ```bash
   wget -O NVIDIA-Linux-x86_64-grid.run https://go.microsoft.com/fwlink/?linkid=874272  
   chmod +x NVIDIA-Linux-x86_64-grid.run
   sudo ./NVIDIA-Linux-x86_64-grid.run
   ``` 

6. nvidia-xconfig ユーティリティを実行して X 構成ファイルを更新するかどうかを尋ねられたら、 **[はい]** を選択します。

7. インストールが完了したら、/etc/nvidia/gridd.conf.template を、/etc/nvidia/ にある新しいファイル gridd.conf にコピーします。

   ```bash
   sudo cp /etc/nvidia/gridd.conf.template /etc/nvidia/gridd.conf
   ```

8. 次を `/etc/nvidia/gridd.conf` に追加します。
 
   ```
   IgnoreSP=FALSE
   EnableUI=FALSE
   ```
   
9. 存在する場合、`/etc/nvidia/gridd.conf` から次を削除します。
 
   ```
   FeatureType=0
   ```
10. VM を再起動して、インストールの確認に進みます。


### <a name="centos-or-red-hat-enterprise-linux"></a>CentOS または Red Hat Enterprise Linux 

1. カーネルと DKMS を更新します (推奨)。 カーネルを更新しない場合は、`kernel-devel` と `dkms` のバージョンがカーネルに対して確実に適切であるようにします。
 
   ```bash  
   sudo yum update
   sudo yum install kernel-devel
   sudo rpm -Uvh https://dl.fedoraproject.org/pub/epel/epel-release-latest-7.noarch.rpm
   sudo yum install dkms
   sudo yum install hyperv-daemons
   ```

2. NVIDIA ドライバーと互換性がない、Nouveau カーネル ドライバーを無効にします (NV または NV3 の VM では NVIDIA ドライバーのみを使用)。これを行うには、次の内容を含む `nouveau.conf` という名前のファイルを `/etc/modprobe.d` に作成します。

   ```
   blacklist nouveau
   blacklist lbm-nouveau
   ```

3. VM を再起動して再接続し、[Hyper-V と Azure 用の最新の Linux Integration Services](https://www.microsoft.com/download/details.aspx?id=55106) をインストールします。 lspci の結果を検証して、LIS が必要かどうかを確認します。 すべての GPU デバイスが想定どおりに (および前述のとおりに) 一覧表示されている場合は、LIS をインストールする必要はありません。 

   CentOS/RHEL 7.8 (またはそれ以降のバージョン) を使用する予定の場合、この手順はスキップしてください。これは、これらのバージョンでは LIS が不要になったためです。

      ```bash
      wget https://aka.ms/lis
      tar xvzf lis
      cd LISISO

      sudo ./install.sh
      sudo reboot

      ```
 
4. VM に再接続して、`lspci` コマンドを実行します。 NVIDIA M60 カードが PCI デバイスとして表示されていることを確認します。
 
5. GRID ドライバーをダウンロードしてインストールします。

   ```bash
   wget -O NVIDIA-Linux-x86_64-grid.run https://go.microsoft.com/fwlink/?linkid=874272  
   chmod +x NVIDIA-Linux-x86_64-grid.run

   sudo ./NVIDIA-Linux-x86_64-grid.run
   ``` 
6. nvidia-xconfig ユーティリティを実行して X 構成ファイルを更新するかどうかを尋ねられたら、 **[はい]** を選択します。

7. インストールが完了したら、/etc/nvidia/gridd.conf.template を、/etc/nvidia/ にある新しいファイル gridd.conf にコピーします。
  
   ```bash
   sudo cp /etc/nvidia/gridd.conf.template /etc/nvidia/gridd.conf
   ```
  
8. 次を `/etc/nvidia/gridd.conf` に追加します。
 
   ```
   IgnoreSP=FALSE
   EnableUI=FALSE 
   ```
9. 存在する場合、`/etc/nvidia/gridd.conf` から次を削除します。
 
   ```
   FeatureType=0
   ```
10. VM を再起動して、インストールの確認に進みます。


### <a name="verify-driver-installation"></a>ドライバーのインストールの確認


GPU デバイスの状態を照会するには、VM に SSH 接続し、ドライバーと共にインストールされる [nvidia-smi](https://developer.nvidia.com/nvidia-system-management-interface) コマンド ライン ユーティリティを実行します。 

ドライバーがインストールされると、次のような出力が表示されます。 **GPU-Util** は、その時点で VM で GPU ワークロードを実行していない限り、0% と表示されます。 ドライバーのバージョンと GPU の詳細は、次の表示と異なる場合があります。

![GPU デバイスの状態が照会されたときの出力を示すスクリーンショット。](./media/n-series-driver-setup/smi-nv.png)
 

### <a name="x11-server"></a>X11 サーバー
NV または NVv2 の VM へのリモート接続用に X11 サーバーが必要な場合は、グラフィックスのハードウェア アクセラレータを許可している [x11vnc](http://www.karlrunge.com/x11vnc/) をお勧めします。 M60 デバイスの BusID は、X11 構成ファイル (通常、 `etc/X11/xorg.conf`) に手動で追加する必要があります。 次のような `"Device"` セクションを追加します。
 
```
Section "Device"
    Identifier     "Device0"
    Driver         "nvidia"
    VendorName     "NVIDIA Corporation"
    BoardName      "Tesla M60"
    BusID          "PCI:0@your-BusID:0:0"
EndSection
```
 
また、このデバイスを使用するように `"Screen"` セクションを更新します。
 
10 進数 BusID は次を実行して見つけることができます。

```bash
nvidia-xconfig --query-gpu-info | awk '/PCI BusID/{print $4}'
```
 
VM が再割り当てまたは再起動されると、BusID が変更される場合があります。 そのため、スクリプトを作成して、VM が再起動されたときに、X11 の構成で BusID を更新することができます。 たとえば、次のような内容で `busidupdate.sh` という名前 (または別の目的の名前) のスクリプトを作成します。

```bash 
#!/bin/bash
XCONFIG="/etc/X11/xorg.conf"
OLDBUSID=`awk '/BusID/{gsub(/"/, "", $2); print $2}' ${XCONFIG}`
NEWBUSID=`nvidia-xconfig --query-gpu-info | awk '/PCI BusID/{print $4}'`

if [[ "${OLDBUSID}" == "${NEWBUSID}" ]] ; then
        echo "NVIDIA BUSID not changed - nothing to do"
else
        echo "NVIDIA BUSID changed from \"${OLDBUSID}\" to \"${NEWBUSID}\": Updating ${XCONFIG}" 
        sed -e 's|BusID.*|BusID          '\"${NEWBUSID}\"'|' -i ${XCONFIG}
fi
```

次に、ブート時にスクリプトがルートとして呼び出されるように、`/etc/rc.d/rc3.d` に更新スクリプト用のエントリを作成します。

## <a name="troubleshooting"></a>トラブルシューティング

* カードを照会する必要があるときにコマンドがより高速に出力されるように、`nvidia-smi` を使って永続化モードを設定できます。 永続化モードを設定するには、`nvidia-smi -pm 1` を実行します。 VM を再起動すると、モード設定は消失することに注意してください。 常にスタートアップ時に実行するように、モード設定をスクリプト処理できます。
* NVIDIA CUDA ドライバーを最新バージョンに更新して、RDMA 接続が動作しなくなっていることが判明した場合は、[RDMA ドライバーを再インストールして](#rdma-network-connectivity)、その接続を再確立してください。 
* LIS で特定の CentOS/RHEL OS バージョン (またはカーネル) がサポートされていない場合は、LIS のインストール中に、"サポートされていないカーネル バージョン" というエラーがスローされます。 OS およびカーネルのバージョンと共に、このエラーを報告してください。

## <a name="next-steps"></a>次のステップ

* NVIDIA ドライバーがインストールされている Linux VM のイメージをキャプチャするには、「[Linux 仮想マシンを一般化してキャプチャする方法](capture-image.md)」を参照してください。
