---
title: Windows 用 Azure N シリーズ GPU ドライバーのセットアップ | Microsoft Docs
description: Azure で Windows Server または Windows を実行する N シリーズ VM 用の NVIDIA GPU ドライバーの設定方法
services: virtual-machines-windows
author: cynthn
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: f3950c34-9406-48ae-bcd9-c0418607b37d
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 09/24/2018
ms.author: cynthn
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: d173f1f9048a46f3fb2500d225ec121157d5d960
ms.sourcegitcommit: 0dd053b447e171bc99f3bad89a75ca12cd748e9c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/26/2019
ms.locfileid: "58483544"
---
# <a name="install-nvidia-gpu-drivers-on-n-series-vms-running-windows"></a>Windows を実行している N シリーズ VM に NVIDIA GPU ドライバーをインストールする 

Windows を実行している Azure N シリーズ VM の GPU 機能を利用するには、NVIDIA GPU ドライバーがインストールされている必要があります。 [NVIDIA GPU ドライバー拡張機能](../extensions/hpccompute-gpu-windows.md)は、N シリーズ VM 上に適切な NVIDIA CUDA または GRID ドライバーをインストールします。 この拡張機能は、Azure Portal または Azure PowerShell や Azure Resource Manager テンプレートなどのツールを使用してインストールまたは管理します。 サポートされるオペレーティング システムおよびデプロイ手順については、[NVIDIA GPU ドライバー拡張機能のドキュメント](../extensions/hpccompute-gpu-windows.md)を参照してください。

GPU ドライバーを手動でインストールすることを選択した場合、この記事では、サポートされるオペレーティング システム、ドライバー、インストールおよび検証手順について説明します。 手動でのドライバー セットアップ情報は、[Linux VM](../linux/n-series-driver-setup.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) でも使用できます。

基本仕様、ストレージの容量、およびディスクの詳細については、「[GPU Windows VM のサイズ](sizes-gpu.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)」を参照してください。 

[!INCLUDE [virtual-machines-n-series-windows-support](../../../includes/virtual-machines-n-series-windows-support.md)]

## <a name="driver-installation"></a>ドライバーのインストール

1. リモート デスクトップで N シリーズの各 VM に接続します。

2. ご使用の Windows オペレーティング システムに、サポートされるドライバーをダウンロード、抽出、インストールします。

VM に GRID ドライバーをインストールした後は、再起動が必要です。 CUDA ドライバーをインストールした後は、再起動は必要ありません。

## <a name="verify-driver-installation"></a>ドライバーのインストールの確認

ドライバーのインストールはデバイス マネージャーで確認できます。 次は、Azure NC VM で正しく構成された Tesla K80 カードの例です。

![GPU ドライバーのプロパティ](./media/n-series-driver-setup/GPU_driver_properties.png)

GPU デバイスの状態を照会するには、ドライバーとともにインストールされる [nvidia smi](https://developer.nvidia.com/nvidia-system-management-interface)コマンド ライン ユーティリティを実行します。

1. コマンド プロンプトを開き、**C:\Program Files\NVIDIA Corporation\NVSMI** ディレクトリに変更します。

2. `nvidia-smi` を実行します。 ドライバーがインストールされると、次のような出力が表示されます。 **GPU-Util** は、その時点で VM で GPU ワークロードを実行していない限り、**0%** と表示されます。 ドライバーのバージョンと GPU の詳細は、次の表示と異なる場合があります。

![NVIDIA デバイスの状態](./media/n-series-driver-setup/smi.png)  

## <a name="rdma-network-connectivity"></a>RDMA ネットワーク接続

RDMA ネットワーク接続は、同じ可用性セット内にデプロイまたは仮想マシン スケール セット内の単一の配置グループにデプロイされた NC24r など、RDMA 対応の N シリーズ VM で有効にすることができます。 RDMA 接続を有効にする Windows ネットワーク デバイス ドライバーをインストールするには、HpcVmDrivers 拡張機能を追加する必要があります。 VM 拡張機能を RDMA 対応の N シリーズ VM に追加するには、Azure Resource Manager 用の [Azure PowerShell](/powershell/azure/overview) コマンドレットを使います。

米国西部リージョンの既存の RDMA 対応 VM (myVM) に HpcVMDrivers 拡張機能の最新バージョン 1.1 をインストールするには:
  ```powershell
  Set-AzVMExtension -ResourceGroupName "myResourceGroup" -Location "westus" -VMName "myVM" -ExtensionName "HpcVmDrivers" -Publisher "Microsoft.HpcCompute" -Type "HpcVmDrivers" -TypeHandlerVersion "1.1"
  ```
  詳細については、「[Windows 用の仮想マシン拡張機能とその機能](extensions-features.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json)」を参照してください。

RDMA ネットワークは、[Microsoft MPI](https://docs.microsoft.com/message-passing-interface/microsoft-mpi) または Intel MPI 5.x で実行しているアプリケーションに対して、Message Passing Interface (MPI) トラフィックをサポートしています。 


## <a name="next-steps"></a>次の手順

* NVIDIA Tesla GPU 向けに GPU アクセラレータを使用したアプリケーションを構築する開発者は、最新の [CUDA Toolkit](https://developer.nvidia.com/cuda-downloads) をダウンロードしてインストールできます。 詳細については、[CUDA インストール ガイド](https://docs.nvidia.com/cuda/cuda-installation-guide-microsoft-windows/index.html#axzz4ZcwJvqYi)を参照してください。


