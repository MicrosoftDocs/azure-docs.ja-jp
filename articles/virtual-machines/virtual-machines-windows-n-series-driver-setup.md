---
title: "Windows 用 Azure N シリーズ ドライバー セットアップ | Microsoft Docs"
description: "Azure で Windows を実行する N シリーズ VM 用の NVIDIA GPU ドライバーの設定方法"
services: virtual-machines-windows
documentationcenter: 
author: dlepow
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: f3950c34-9406-48ae-bcd9-c0418607b37d
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 03/10/2017
ms.author: danlep
ms.custom: H1Hack27Feb2017
translationtype: Human Translation
ms.sourcegitcommit: afe143848fae473d08dd33a3df4ab4ed92b731fa
ms.openlocfilehash: 562ebbf815f421343c580fced111cda481aa4a5c
ms.lasthandoff: 03/17/2017


---
# <a name="set-up-gpu-drivers-for-n-series-vms-running-windows-server"></a>Windows Server を実行している N シリーズ VM の GPU ドライバーをセットアップする
Windows Server 2016 または Windows Server 2012 R2 を実行する Azure N シリーズ VM の GPU 機能を利用するには、デプロイ後に各 VM に NVIDIA グラフィック ドライバーをインストールする必要があります。 ドライバーのセットアップ情報は、[Linux VM](virtual-machines-linux-n-series-driver-setup.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) でも利用可能です。

基本的な仕様、ストレージの容量、ディスクの詳細については、[仮想マシンのサイズ](virtual-machines-windows-sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)に関する記事をご覧ください。 「[N シリーズ VM の一般的な考慮事項](#general-considerations-for-n-series-vms)」も参照してください。



## <a name="supported-gpu-drivers"></a>サポートされる GPU ドライバー

リモート デスクトップで N シリーズの各 VM に接続します。 ご使用の Windows オペレーティング システムに、サポートされるドライバーをダウンロード、抽出、インストールします。 

### <a name="nvidia-tesla-drivers-for-nc-vms-tesla-k80"></a>NC VM 用の NVIDIA Tesla ドライバー (Tesla K80)



| OS | ドライバーのバージョン |
| -------- |------------- |
| Windows Server 2016 | [376.84](http://us.download.nvidia.com/Windows/Quadro_Certified/376.84/376.84-tesla-desktop-winserver2016-international-whql.exe) (.exe) |
| Windows Server 2012 R2 | [376.84](http://us.download.nvidia.com/Windows/Quadro_Certified/376.84/376.84-tesla-desktop-winserver2008-2012r2-64bit-international-whql.exe) (.exe) |

> [!NOTE]
> ここで示されている Tesla ドライバーのダウンロード リンクは、公開時現在のものです。 最新のドライバーについては、[NVIDIA](http://www.nvidia.com/) Web サイトを参照してください。
>

### <a name="nvidia-grid-drivers-for-nv-vms-tesla-m60"></a>NV VM 用の NVIDIA GRID ドライバー (Tesla M60)

| OS | ドライバーのバージョン |
| -------- |------------- |
| Windows Server 2016 | [369.71](https://go.microsoft.com/fwlink/?linkid=836843) (.zip) |
| Windows Server 2012 R2 | [369.71](https://go.microsoft.com/fwlink/?linkid=836844) (.zip)  |



## <a name="verify-gpu-driver-installation"></a>GPU ドライバーのインストールの確認

Azure NV VM では、ドライバーのインストール後に再起動が必要です。 NC VM では、再起動は必要ありません。

ドライバーのインストールはデバイス マネージャーで確認できます。 次は、Azure NC VM で正しく構成された Tesla K80 カードの例です。

![GPU ドライバーのプロパティ](./media/virtual-machines-windows-n-series-driver-setup/GPU_driver_properties.png)

GPU デバイスの状態を照会するには、ドライバーとともにインストールされる [nvidia smi](https://developer.nvidia.com/nvidia-system-management-interface)コマンド ライン ユーティリティを実行します。 

![NVIDIA デバイスの状態](./media/virtual-machines-windows-n-series-driver-setup/smi.png)  

## <a name="rdma-network-for-nc24r-vms"></a>NC24r VM の RDMA ネットワーク

RDMA ネットワーク接続は、同じ可用性セットにデプロイされた NC24r VM で有効にすることができます。 RDMA 接続を有効にする Windows ネットワーク デバイス ドライバーをインストールするには、HpcVmDrivers 拡張機能を追加する必要があります。 VM 拡張機能を NC24r VM に追加するには、Azure Resource Manager 用の [Azure PowerShell](/powershell/azureps-cmdlets-docs) コマンドレットを使用します。

> [!NOTE]
> 現時点では、NC24r VM 上で RDMA ネットワークをサポートしているのは、Windows Server 2012 R2 のみです。
> 

米国西部リージョンの既存の RDMA 対応 VM (myVM) に HpcVMDrivers 拡張機能の最新バージョン 1.1 をインストールするには:
  ```PowerShell
  Set-AzureRmVMExtension -ResourceGroupName "myResourceGroup" -Location "westus" -VMName "myVM" -ExtensionName "HpcVmDrivers" -Publisher "Microsoft.HpcCompute" -Type "HpcVmDrivers" -TypeHandlerVersion "1.1"
  ```
  詳細については、「[Windows 用の仮想マシン拡張機能とその機能](virtual-machines-windows-extensions-features.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json)」を参照してください。

RDMA ネットワークは、[Microsoft MPI](https://msdn.microsoft.com/library/bb524831(v=vs.85).aspx) または Intel MPI 5.x で実行しているアプリケーションに対して、Message Passing Interface (MPI) トラフィックをサポートしています。 

[!INCLUDE [virtual-machines-n-series-considerations](../../includes/virtual-machines-n-series-considerations.md)]

## <a name="next-steps"></a>次のステップ

* N シリーズ VM の NVIDIA GPU の詳細については、次のサイトをご覧ください。
    * [NVIDIA Tesla K80](http://www.nvidia.com/object/tesla-k80.html) (Azure NC VM 用)
    * [NVIDIA Tesla M60](http://www.nvidia.com/object/tesla-m60.html) (Azure NV VM 用)

* NVIDIA Tesla GPU 向けに GPU アクセラレータを使用したアプリケーションを構築する開発者は、[Windows Server 2016](https://developer.nvidia.com/compute/cuda/8.0/Prod2/local_installers/cuda_8.0.61_win10-exe) または [Windows Server 2012 R2](https://developer.nvidia.com/compute/cuda/8.0/Prod2/local_installers/cuda_8.0.61_windows-exe) 用の CUDA Toolkit 8 をダウンロードしてインストールできます。 詳細については、[CUDA インストール ガイド](http://docs.nvidia.com/cuda/cuda-installation-guide-microsoft-windows/index.html#axzz4ZcwJvqYi)を参照してください。



