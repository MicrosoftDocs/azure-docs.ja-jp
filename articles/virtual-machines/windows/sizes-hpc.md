---
title: Azure Windows VM のサイズ - HPC | Microsoft Docs
description: Azure の Windows ハイ パフォーマンス コンピューティング仮想マシンで使用できるさまざまなサイズを一覧表示します。 このシリーズのストレージのスループットとネットワーク帯域幅に加え、vCPU、データ ディスク、NIC の数に関する情報を一覧表示します。
services: virtual-machines-windows
documentationcenter: ''
author: jonbeck7
manager: jeconnoc
editor: ''
tags: azure-resource-manager,azure-service-management
ms.assetid: ''
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 10/12/2018
ms.author: jonbeck
ms.openlocfilehash: 58d4ced041b6f5cf767b45191e28a4b395f584b6
ms.sourcegitcommit: 0dd053b447e171bc99f3bad89a75ca12cd748e9c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/26/2019
ms.locfileid: "58480262"
---
# <a name="high-performance-compute-vm-sizes"></a>ハイ パフォーマンス コンピューティング VM のサイズ

[!INCLUDE [virtual-machines-common-sizes-hpc](../../../includes/virtual-machines-common-sizes-hpc.md)]

[!INCLUDE [virtual-machines-common-sizes-table-defs](../../../includes/virtual-machines-common-sizes-table-defs.md)]

[!INCLUDE [virtual-machines-common-a8-a9-a10-a11-specs](../../../includes/virtual-machines-common-a8-a9-a10-a11-specs.md)]


* **オペレーティング システム** - Windows Server 2016、Windows Server 2012 R2、Windows Server 2012

* **MPI** -Microsoft MPI (MS-MPI) 2012 R2 以降、Intel MPI ライブラリ 5.x

  サポートされている MPI 実装では、インスタンス間の通信に Microsoft Network Direct インターフェイスが使用されます。 

* **RDMA ネットワーク アドレス空間** - Azure の RDMA ネットワークでは、アドレス空間 172.16.0.0/16 は予約済みです。 Azure 仮想ネットワークにデプロイ済みのインスタンスで MPI アプリケーションを実行する場合、仮想ネットワークのアドレス空間が RDMA ネットワークと重複しないようにしてください。

* **HpcVmDrivers VM 拡張機能** - RDMA 対応の VM では、HpcVmDrivers 拡張機能を追加して、RDMA 接続用に Windows ネットワーク デバイス ドライバーをインストールします。 (A8 インスタンスと A9 インスタンスの特定のデプロイでは、HpcVmDrivers 拡張機能は自動的に追加されます)。VM に VM 拡張機能を追加するには、[Azure PowerShell](/powershell/azure/overview) コマンドレットを使用できます。 

  
  次のコマンドは、*West US* リージョン内の *myResourceGroup* という名前のリソース グループにデプロイされた *myVM* という名前の既存の RDMA 対応 VM に、最新バージョン 1.1 の HpcVMDrivers 拡張機能をインストールします。

  ```powershell
  Set-AzVMExtension -ResourceGroupName "myResourceGroup" -Location "westus" -VMName "myVM" -ExtensionName "HpcVmDrivers" -Publisher "Microsoft.HpcCompute" -Type "HpcVmDrivers" -TypeHandlerVersion "1.1"
  ```
  
  詳しくは、[仮想マシン拡張機能とその機能](extensions-features.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)に関する記事をご覧ください。 [クラシック デプロイ モデル](classic/manage-extensions.md)にデプロイされている VM にも拡張機能を使用できます。

### <a name="cluster-configuration-options"></a>クラスター構成オプション

Azure では、次のような、RDMA ネットワークを使用して通信できる Windows HPC VM のクラスターを作成するためのオプションがいくつか提供されます。 

* **仮想マシン** - 同じ可用性セット内に RDMA 対応の HPC VM をデプロイします (Azure Resource Manager デプロイ モデルを使用する場合)。 クラシック デプロイ モデルを使用する場合は、同じクラウド サービス内に VM をデプロイします。 

* **Virtual Machine Scale Sets** - VM スケール セットで、単一の配置グループにデプロイを制限するようにします。 たとえば、Resource Manager テンプレートで、`singlePlacementGroup` プロパティを `true` に設定します。 

* **Azure CycleCloud** - [Azure CycleCloud](/azure/cyclecloud/) で HPC クラスターを作成し、Windows ノード上で MPI ジョブを実行します。

* **Azure Batch** - [Azure Batch](/azure/batch/) プールを作成し、Windows Server コンピューティング ノード上で MPI ワークロードを実行します。 詳細については、「[Batch プールでの RDMA 対応または GPU 対応インスタンスの使用](../../batch/batch-pool-compute-intensive-sizes.md)」を参照してください。 Batch でのコンテナー ベースのワークロードの実行について、[Batch Shipyard](https://github.com/Azure/batch-shipyard) プロジェクトも参照してください。

* **Microsoft HPC Pack** - [HPC Pack](https://docs.microsoft.com/powershell/high-performance-computing/overview) には、RDMA 対応の Windows VM 上にデプロイした場合に Azure RDMA ネットワークを使用する MS-MPI 用のランタイム環境が含まれています。 デプロイの例については、「[Set up a Windows RDMA cluster with HPC Pack to run MPI applications (HPC Pack を使用して Windows RDMA クラスターをセットアップして MPI アプリケーションを実行する)](classic/hpcpack-rdma-cluster.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json)」を参照してください。

## <a name="other-sizes"></a>その他のサイズ
- [汎用](sizes-general.md)
- [コンピューティングの最適化](sizes-compute.md)
- [メモリの最適化](../virtual-machines-windows-sizes-memory.md)
- [ストレージの最適化](../virtual-machines-windows-sizes-storage.md)
- [GPU の最適化](sizes-gpu.md)
- [旧世代](sizes-previous-gen.md)

## <a name="next-steps"></a>次の手順

- Windows Server で HPC Pack を使ってコンピューティング集中型インスタンスを使用するためのチェックリストについては、「[HPC Pack を使用して Windows RDMA クラスターをセットアップして MPI アプリケーションを実行する](classic/hpcpack-rdma-cluster.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json)」を参照してください。

- Azure Batch で MPI アプリケーションを実行するときにコンピューティング集中型インスタンスを使用するには、「[Azure Batch でのマルチインスタンス タスクを使用した Message Passing Interface (MPI) アプリケーションの実行](../../batch/batch-mpi.md)」をご覧ください。

- [Azure コンピューティング ユニット (ACU)](acu.md) を確認することで、Azure SKU 全体の処理性能を比較できます。




