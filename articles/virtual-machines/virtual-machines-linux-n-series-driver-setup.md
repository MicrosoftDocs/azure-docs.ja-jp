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
ms.date: 03/10/2017
ms.author: danlep
ms.custom: H1Hack27Feb2017
translationtype: Human Translation
ms.sourcegitcommit: c1cd1450d5921cf51f720017b746ff9498e85537
ms.openlocfilehash: 1701646ce8cb9494561c37d46cd435b4e7608fe8
ms.lasthandoff: 03/14/2017


---

# <a name="set-up-gpu-drivers-for-n-series-vms-running-linux"></a>Linux を実行している N シリーズ VM の GPU ドライバーをセットアップする

対応する Linux ディストリビューションを実行する Azure N シリーズ VM の GPU 機能を利用するには、デプロイ後に各 VM に NVIDIA グラフィック ドライバーをインストールする必要があります。 ドライバーのセットアップ情報は、[Windows VM](virtual-machines-windows-n-series-driver-setup.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) でも利用可能です。


> [!IMPORTANT]
> 現時点では、Linux GPU のサポートは、Ubuntu Server 16.04 LTS を実行する Azure NC VM でのみご利用いただけます。
> 

N シリーズ VM の仕様、ストレージの容量、ディスクの詳細については、[仮想マシンのサイズ](virtual-machines-linux-sizes.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)に関する記事をご覧ください。 「[N シリーズ VM の一般的な考慮事項](#general-considerations-for-n-series-vms)」も参照してください。



## <a name="install-nvidia-cuda-drivers"></a>NVIDIA CUDA ドライバーのインストール

NVIDIA CUDA Toolkit 8.0 から NVIDIA ドライバーを Linux NC VM にインストールする手順は次のとおりです。 C および C++ の開発者は、GPU アクセラレータを使用したアプリケーションを構築するために、必要に応じて Toolkit 全体をインストールできます。 詳細については、[CUDA インストール ガイド](http://docs.nvidia.com/cuda/cuda-installation-guide-linux/index.html)を参照してください。


> [!NOTE]
> ここで示されているドライバーのダウンロード リンクは、公開時現在のものです。 最新のドライバーについては、[NVIDIA](http://www.nvidia.com/) Web サイトを参照してください。

CUDA Toolkit をインストールするには、各 VM への SSH 接続を作成します。 システムに CUDA 対応の GPU が備わっているかどうかを確認するには、次のコマンドを実行します。

```bash
lspci | grep -i NVIDIA
```
次の例のような出力が表示されます (NVIDIA Tesla K80 カードが表示されています)。

![lspci コマンドの出力](./media/virtual-machines-linux-n-series-driver-setup/lspci.png)

次に、ディストリビューションに固有のコマンドを実行します。

### <a name="ubuntu-1604-lts"></a>Ubuntu 16.04 LTS

```bash
CUDA_REPO_PKG=cuda-repo-ubuntu1604_8.0.61-1_amd64.deb

wget -O /tmp/${CUDA_REPO_PKG} http://developer.download.nvidia.com/compute/cuda/repos/ubuntu1604/x86_64/${CUDA_REPO_PKG} 

sudo dpkg -i /tmp/${CUDA_REPO_PKG}

rm -f /tmp/${CUDA_REPO_PKG}

sudo apt-get update

sudo apt-get install cuda-drivers

```
インストールには数分かかる場合があります。

必要に応じて完全な CUDA ツールキットをインストールするには、次のように入力します。

```bash
sudo apt-get install cuda
```

VM を再起動して、インストールの確認に進みます。

## <a name="verify-driver-installation"></a>ドライバーのインストールの確認


GPU デバイスの状態を照会するには、VM に SSH 接続し、ドライバーと共にインストールされる [nvidia-smi](https://developer.nvidia.com/nvidia-system-management-interface) コマンド ライン ユーティリティを実行します。 

![NVIDIA デバイスの状態](./media/virtual-machines-linux-n-series-driver-setup/smi.png)

## <a name="cuda-driver-updates"></a>CUDA ドライバーの更新

デプロイ後は、定期的に CUDA ドライバーを更新することをお勧めします。

### <a name="ubuntu-1604-lts"></a>Ubuntu 16.04 LTS

```bash
sudo apt-get update

sudo apt-get upgrade -y

sudo apt-get dist-upgrade -y

sudo apt-get install cuda-drivers
```

更新の完了後、VM を再起動します。


[!INCLUDE [virtual-machines-n-series-considerations](../../includes/virtual-machines-n-series-considerations.md)]

* X サーバーや、nouveau ドライバーを使用するその他のシステムを Ubuntu NC VM にインストールすることはお勧めしません。 NVIDIA GPU ドライバーをインストールする前に、nouveau ドライバーを無効にする必要があります。  

* NVIDIA ドライバーをインストールした Linux VM のイメージをキャプチャする場合は、「[Linux 仮想マシンを一般化してキャプチャする方法](virtual-machines-linux-capture-image.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)」を参照してください。

## <a name="next-steps"></a>次のステップ

* N シリーズ VM の NVIDIA GPU の詳細については、次のサイトをご覧ください。
    * [NVIDIA Tesla K80](http://www.nvidia.com/object/tesla-k80.html) (Azure NC VM 用)
    * [NVIDIA Tesla M60](http://www.nvidia.com/object/tesla-m60.html) (Azure NV VM 用)


