---
title: "Linux 用 Azure N シリーズ ドライバー セットアップ | Microsoft Docs"
description: "Azure で Linux を実行する N シリーズ VM 用の NVIDIA GPU ドライバーの設定方法を説明します。"
services: virtual-machines-linux
documentationcenter: 
author: dlepow
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: d91695d0-64b9-4e6b-84bd-18401eaecdde
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 07/25/2017
ms.author: danlep
ms.custom: H1Hack27Feb2017
ms.translationtype: HT
ms.sourcegitcommit: 54774252780bd4c7627681d805f498909f171857
ms.openlocfilehash: bdeb4d5ca1d9ff4d7dfd0961690412dd7530572a
ms.contentlocale: ja-jp
ms.lasthandoff: 07/28/2017

---

# <a name="install-nvidia-gpu-drivers-on-n-series-vms-running-linux"></a>Linux を実行している N シリーズ VM に NVIDIA GPU ドライバーをインストールする

Linux を実行する Azure N シリーズ VM の GPU 機能を利用するには、サポートされている NVIDIA グラフィック ドライバーをインストールします。 この記事では、N シリーズ VM をデプロイした後のドライバーのセットアップ手順について説明します。 ドライバーのセットアップ情報は、[Windows VM](../windows/n-series-driver-setup.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) でも利用可能です。


N シリーズ VM の仕様、ストレージの容量、およびディスクの詳細については、「[GPU Linux VM のサイズ](sizes-gpu.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)」を参照してください。 



[!INCLUDE [virtual-machines-n-series-linux-support](../../../includes/virtual-machines-n-series-linux-support.md)]

## <a name="install-grid-drivers-for-nv-vms"></a>NV VM 用の GRID ドライバーのインストール

NV VM に NVIDIA GRID ドライバーをインストールするには、各 VM に SSH 接続を作成して、Linux ディストリビューションの手順に従います。 

### <a name="ubuntu-1604-lts"></a>Ubuntu 16.04 LTS

1. `lspci` コマンドを実行します。 NVIDIA M60 カードが PCI デバイスとして表示されていることを確認します。

2. 更新プログラムをインストールします。

  ```bash
  sudo apt-get update

  sudo apt-get upgrade -y

  sudo apt-get dist-upgrade -y

  sudo apt-get install build-essential ubuntu-desktop -y
  ```
3. NVIDIA ドライバーと互換性がない、Nouveau カーネル ドライバーを無効にします  (NV VM では NVIDIA ドライバーのみを使用)。これを行うには、次のコンテンツを使用して `/etc/modprobe.d `named`nouveau.conf` でファイルを作成します。

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
  wget -O NVIDIA-Linux-x86_64-367.106-grid.run https://go.microsoft.com/fwlink/?linkid=849941  

  chmod +x NVIDIA-Linux-x86_64-367.106-grid.run

  sudo ./NVIDIA-Linux-x86_64-367.106-grid.run
  ``` 

6. nvidia-xconfig ユーティリティを実行して X 構成ファイルを更新するかどうかを尋ねられたら、**[はい]** を選択します。

7. インストールが完了したら、/etc/nvidia/gridd.conf.template を、/etc/nvidia/ にある新しいファイル gridd.conf にコピーします。

  ```bash
  sudo cp /etc/nvidia/gridd.conf.template /etc/nvidia/gridd.conf
  ```

8. 次を `/etc/nvidia/gridd.conf` に追加します。
 
  ```
  IgnoreSP=TRUE
  ```
9. VM を再起動して、インストールの確認に進みます。


### <a name="centos-based-73-or-red-hat-enterprise-linux-73"></a>CentOS ベース 7.3 または Red Hat Enterprise Linux 7.3


1. カーネルと DKMS を更新します。
 
  ```bash  
  sudo yum update
 
  sudo yum install kernel-devel
 
  sudo rpm -Uvh https://dl.fedoraproject.org/pub/epel/epel-release-latest-7.noarch.rpm
 
  sudo yum install dkms
  ```

2. NVIDIA ドライバーと互換性がない、Nouveau カーネル ドライバーを無効にします  (NV VM では NVIDIA ドライバーのみを使用)。これを行うには、次のコンテンツを使用して `/etc/modprobe.d `named`nouveau.conf` でファイルを作成します。

  ```
  blacklist nouveau

  blacklist lbm-nouveau
  ```
 
3. VM を再起動して再接続し、HYPER-V の最新の Linux Integration Services をインストールします。
 
  ```bash
  wget http://download.microsoft.com/download/6/8/F/68FE11B8-FAA4-4F8D-8C7D-74DA7F2CFC8C/lis-rpms-4.2.2-2.tar.gz
 
  tar xvzf lis-rpms-4.2.2-2.tar.gz
 
  cd LISISO
 
  sudo ./install.sh
 
  sudo reboot
  ```
 
4. VM に再接続して、`lspci` コマンドを実行します。 NVIDIA M60 カードが PCI デバイスとして表示されていることを確認します。
 
5. GRID ドライバーをダウンロードしてインストールします。

  ```bash
  wget -O NVIDIA-Linux-x86_64-367.106-grid.run https://go.microsoft.com/fwlink/?linkid=849941  

  chmod +x NVIDIA-Linux-x86_64-367.106-grid.run

  sudo ./NVIDIA-Linux-x86_64-367.106-grid.run
  ``` 
6. nvidia-xconfig ユーティリティを実行して X 構成ファイルを更新するかどうかを尋ねられたら、**[はい]** を選択します。

7. インストールが完了したら、/etc/nvidia/gridd.conf.template を、/etc/nvidia/ にある新しいファイル gridd.conf にコピーします。
  
  ```bash
  sudo cp /etc/nvidia/gridd.conf.template /etc/nvidia/gridd.conf
  ```
  
8. 次を `/etc/nvidia/gridd.conf` に追加します。
 
  ```
  IgnoreSP=TRUE
  ```
9. VM を再起動して、インストールの確認に進みます。

### <a name="verify-driver-installation"></a>ドライバーのインストールの確認


GPU デバイスの状態を照会するには、VM に SSH 接続し、ドライバーと共にインストールされる [nvidia-smi](https://developer.nvidia.com/nvidia-system-management-interface) コマンド ライン ユーティリティを実行します。 

次のような出力が表示されます。

![NVIDIA デバイスの状態](./media/n-series-driver-setup/smi-nv.png)
 

### <a name="x11-server"></a>X11 サーバー
NV VM へのリモート接続用に X11 サーバーが必要な場合は、グラフィックスのハードウェア アクセラレータを許可している [x11vnc](http://www.karlrunge.com/x11vnc/) をお勧めします。 M60 デバイスの BusID は xconfig ファイルに手動で追加する必要があります (Ubuntu 16.04 LTS では `etc/X11/xorg.conf`、CentOS 7.3 または Red Hat Enterprise Server 7.3 では `/etc/X11/XF86config`)。 次のような `"Device"` セクションを追加します。
 
```
Section "Device"
    Identifier     "Device0"
    Driver         "nvidia"
    VendorName     "NVIDIA Corporation"
    BoardName      "Tesla M60"
    BusID          "your-BusID:0:0:0"
EndSection
```
 
また、このデバイスを使用するように `"Screen"` セクションを更新します。
 
BusID は次を実行して見つけることができます。

```bash
/usr/bin/nvidia-smi --query-gpu=pci.bus_id --format=csv | tail -1 | cut -d ':' -f 1
```
 
VM が再割り当てまたは再起動されると、BusID が変更される場合があります。 そのため、スクリプトを使用して、VM が再起動されたときに、X11 の構成で BusID を更新することができます。 For example:

```bash 
#!/bin/bash
BUSID=$((16#`/usr/bin/nvidia-smi --query-gpu=pci.bus_id --format=csv | tail -1 | cut -d ':' -f 1`))

if grep -Fxq "${BUSID}" /etc/X11/XF86Config; then     echo "BUSID is matching"; else   echo "BUSID changed to ${BUSID}" && sed -i '/BusID/c\    BusID          \"PCI:0@'${BUSID}':0:0:0\"' /etc/X11/XF86Config; fi
```

このファイルは、`/etc/rc.d/rc3.d` でそのファイルのエントリを作成することで、起動時にルートとして呼び出すことができます。


## <a name="install-cuda-drivers-for-nc-vms"></a>NC VM 用の CUDA ドライバーのインストール

NVIDIA CUDA Toolkit 8.0 から NVIDIA ドライバーを Linux NC VM にインストールする手順は次のとおりです。 

C および C++ の開発者は、GPU アクセラレータを使用したアプリケーションを構築するために、必要に応じて Toolkit 全体をインストールできます。 詳細については、[CUDA インストール ガイド](http://docs.nvidia.com/cuda/cuda-installation-guide-linux/index.html)を参照してください。


> [!NOTE]
> ここで示されている CUDA ドライバーのダウンロード リンクは、公開された時点のものです。 最新の CUDA ドライバーについては、[NVIDIA](http://www.nvidia.com/) の Web サイトを参照してください。
>

CUDA Toolkit をインストールするには、各 VM への SSH 接続を作成します。 システムに CUDA 対応の GPU が備わっているかどうかを確認するには、次のコマンドを実行します。

```bash
lspci | grep -i NVIDIA
```
次の例のような出力が表示されます (NVIDIA Tesla K80 カードが表示されています)。

![lspci コマンドの出力](./media/n-series-driver-setup/lspci.png)

次に、ディストリビューションに固有のインストール コマンドを実行します。

### <a name="ubuntu-1604-lts"></a>Ubuntu 16.04 LTS

1. CUDA ドライバーをダウンロードしてインストールします。
  ```bash
  CUDA_REPO_PKG=cuda-repo-ubuntu1604_8.0.61-1_amd64.deb

  wget -O /tmp/${CUDA_REPO_PKG} http://developer.download.nvidia.com/compute/cuda/repos/ubuntu1604/x86_64/${CUDA_REPO_PKG} 

  sudo dpkg -i /tmp/${CUDA_REPO_PKG}

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

### <a name="centos-based-73-or-red-hat-enterprise-linux-73"></a>CentOS ベース 7.3 または Red Hat Enterprise Linux 7.3

1. 更新プログラムを取得します。 

  ```bash
  sudo yum update

  sudo reboot
  ```
2. VM に再接続し、Hyper-V の最新の Linux Integration Services をインストールします。

  > [!IMPORTANT]
  > NC24r VM に CentOS ベースの HPC イメージをインストールしている場合は、手順 3 に進みます。 Azure RDMA ドライバーと Linux Integration Services は、イメージにプレインストールされているため、LIS はアップグレードされません。また、既定でカーネルの更新は無効になっています。
  >

  ```bash
  wget http://download.microsoft.com/download/6/8/F/68FE11B8-FAA4-4F8D-8C7D-74DA7F2CFC8C/lis-rpms-4.2.1.tar.gz
 
  tar xvzf lis-rpms-4.2.1.tar.gz
 
  cd LISISO
 
  sudo ./install.sh
 
  sudo reboot
  ```
 
3. VM に再接続し、次のコマンドを使用してインストールを続行します。

  ```bash
  sudo yum install kernel-devel

  sudo rpm -Uvh https://dl.fedoraproject.org/pub/epel/epel-release-latest-7.noarch.rpm

  sudo yum install dkms

  CUDA_REPO_PKG=cuda-repo-rhel7-8.0.61-1.x86_64.rpm

  wget http://developer.download.nvidia.com/compute/cuda/repos/rhel7/x86_64/${CUDA_REPO_PKG} -O /tmp/${CUDA_REPO_PKG}

  sudo rpm -ivh /tmp/${CUDA_REPO_PKG}

  rm -f /tmp/${CUDA_REPO_PKG}

  sudo yum install cuda-drivers
  ```

  インストールには数分かかる場合があります。 

4. 必要に応じて完全な CUDA ツールキットをインストールするには、次のように入力します。

  ```bash
  sudo yum install cuda
  ```

5. VM を再起動して、インストールの確認に進みます。


### <a name="verify-driver-installation"></a>ドライバーのインストールの確認


GPU デバイスの状態を照会するには、VM に SSH 接続し、ドライバーと共にインストールされる [nvidia-smi](https://developer.nvidia.com/nvidia-system-management-interface) コマンド ライン ユーティリティを実行します。 

次のような出力が表示されます。

![NVIDIA デバイスの状態](./media/n-series-driver-setup/smi.png)


### <a name="cuda-driver-updates"></a>CUDA ドライバーの更新

デプロイ後は、定期的に CUDA ドライバーを更新することをお勧めします。

#### <a name="ubuntu-1604-lts"></a>Ubuntu 16.04 LTS

```bash
sudo apt-get update

sudo apt-get upgrade -y

sudo apt-get dist-upgrade -y

sudo apt-get install cuda-drivers

sudo reboot
```


#### <a name="centos-based-73-or-red-hat-enterprise-linux-73"></a>CentOS ベース 7.3 または Red Hat Enterprise Linux 7.3

```bash
sudo yum update

sudo reboot
```



## <a name="troubleshooting"></a>トラブルシューティング

* Ubuntu 16.04 LTS で 4.4.0-75 Linux カーネルを実行している、Azure N シリーズ VM の CUDA ドライバーには既知の問題があります。 以前のカーネル バージョンからアップグレードする場合は、カーネル バージョン 4.4.0-77 以上にアップグレードします。 



## <a name="next-steps"></a>次のステップ

* N シリーズ VM の NVIDIA GPU の詳細については、次のサイトをご覧ください。
    * [NVIDIA Tesla K80](http://www.nvidia.com/object/tesla-k80.html) (Azure NC VM 用)
    * [NVIDIA Tesla M60](http://www.nvidia.com/object/tesla-m60.html) (Azure NV VM 用)

* NVIDIA ドライバーがインストールされている Linux VM のイメージをキャプチャするには、「[Linux 仮想マシンを一般化してキャプチャする方法](capture-image.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)」を参照してください。

