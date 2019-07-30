---
title: Azure Linux VM のサイズ - HPC | Microsoft Docs
description: Azure の Linux ハイ パフォーマンス コンピューティング仮想マシンで使用できるさまざまなサイズを一覧表示します。 このシリーズのストレージのスループットとネットワーク帯域幅に加え、vCPU、データ ディスク、NIC の数に関する情報を一覧表示します。
services: virtual-machines-linux
documentationcenter: ''
author: jonbeck7
manager: gwallace
editor: ''
tags: azure-resource-manager,azure-service-management
ms.assetid: ''
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 10/12/2018
ms.author: jonbeck
ms.openlocfilehash: 847f25d9be1a8654bbc0435d7874acb0ff793304
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/09/2019
ms.locfileid: "67695596"
---
# <a name="high-performance-compute-virtual-machine-sizes"></a>ハイ パフォーマンス コンピューティング仮想マシンのサイズ

[!INCLUDE [virtual-machines-common-sizes-hpc](../../../includes/virtual-machines-common-sizes-hpc.md)]

[!INCLUDE [virtual-machines-common-sizes-table-defs](../../../includes/virtual-machines-common-sizes-table-defs.md)]

[!INCLUDE [virtual-machines-common-a8-a9-a10-a11-specs](../../../includes/virtual-machines-common-a8-a9-a10-a11-specs.md)]


### <a name="mpi"></a>MPI 

Azure 上の SR-IOV に対応した VM のサイズでは、ほぼすべての種類の MPI を使用できます。
SR-IOV に対応していない VM では、Intel MPI 5.x バージョンのみがサポートされています。 Intel MPI ランタイム ライブラリの以降のバージョン (2017、2018) は、Azure Linux RDMA ドライバーと互換性がある場合と無い場合があります。


### <a name="supported-os-images"></a>サポート対象の OS イメージ
 
Azure Marketplace には、RDMA 接続をサポートする多くの Linux ディストリビューションがあります。
  
* **CentOS ベースの HPC** - SR-IOV に対応していない VM の場合、CentOS ベースのバージョン 6.5 の HPC またはそれ以降 (7.5 まで) のバージョンが適しています。 H シリーズの VM では、バージョン 7.1 から 7.5 をお勧めします。 RDMA ドライバーおよび Intel MPI 5.1 は、VM にインストールされます。
  SR-IOV 対応の VM では、CentOS HPC 7.6 は最適化され、RDMA ドライバーが事前に読み込まれ、さまざまな MPI パッケージがインストールされています。
  その他の RHEL/CentOS VM イメージの場合、InfiniBandLinux 拡張機能を追加して InfiniBand を有効にします。 この Linux VM 拡張機能は、RDMA 接続用に Mellanox OFED ドライバーを (SR-IOV 対応の VM 上に) インストールします。 次の PowerShell コマンドレットは、InfiniBandDriverLinux 拡張機能の最新バージョン (バージョン 1.0) を既存の RDMA 対応の VM にインストールします。 RDMA 対応の VM は、名前が *myVM* で、次のように *West US* リージョンの *myResourceGroup* という名前のリソース グループにデプロイされます。

  ```powershell
  Set-AzVMExtension -ResourceGroupName "myResourceGroup" -Location "westus" -VMName "myVM" -ExtensionName "InfiniBandDriverLinux" -Publisher "Microsoft.HpcCompute" -Type "InfiniBandDriverLinux" -TypeHandlerVersion "1.0"
  ```
  また、次の JSON 要素を使用して、簡単にデプロイするために VM 拡張機能を Azure Resource Manager テンプレートに含めることができます。
  ```json
  "properties":{
  "publisher": "Microsoft.HpcCompute",
  "type": "InfiniBandDriverLinux",
  "typeHandlerVersion": "1.0",
  } 
  ```
  
  次のコマンドでは、*myResourceGroup* という名前のリソース グループにデプロイされた *myVMSS* という名前の既存の VM スケール セットのすべての RDMA 対応 VM に、最新バージョンの 1.0 の InfiniBandDriverLinux 拡張機能をインストールします。
  ```powershell
  $VMSS = Get-AzVmss -ResourceGroupName "myResourceGroup" -VMScaleSetName "myVMSS"
  Add-AzVmssExtension -VirtualMachineScaleSet $VMSS -Name "InfiniBandDriverLinux" -Publisher "Microsoft.HpcCompute" -Type "InfiniBandDriverLinux" -TypeHandlerVersion "1.0"
  Update-AzVmss -ResourceGroupName "myResourceGroup" -VMScaleSetName "MyVMSS" -VirtualMachineScaleSet $VMSS
  Update-AzVmssInstance -ResourceGroupName "myResourceGroup" -VMScaleSetName "myVMSS" -InstanceId "*"
  ```
  
  > [!NOTE]
  > CentOS ベースの HPC イメージでは、 **yum** 構成ファイルでのカーネルの更新は無効にされています。 これは、Linux RDMA ドライバーが RPM パッケージとして配布されており、カーネルが更新された場合にドライバーの更新プログラムが機能しない可能性があるためです。
  >
  

* **SUSE Linux Enterprise Server** - SLES 12 SP3 for HPC、SLES 12 SP3 for HPC (Premium)、SLES 12 SP1 for HPC、SLES 12 SP1 for HPC (Premium)、SLES 12 SP4 および SLES 15。 RDMA ドライバーがインストールされ、Intel MPI パッケージが VM に配布されます。 次のコマンドを実行して MPI をインストールします。

  ```bash
  sudo rpm -v -i --nodeps /opt/intelMPI/intel_mpi_packages/*.rpm
  ```
  
* **Ubuntu** - Ubuntu Server 16.04 LTS、18.04 LTS。 VM で RDMA ドライバーを構成し、Intel に登録して Intel MPI をダウンロードします。

  [!INCLUDE [virtual-machines-common-ubuntu-rdma](../../../includes/virtual-machines-common-ubuntu-rdma.md)]  

  InfiniBand の有効化と MPI の設定の詳細については、[Infiniband の有効化](../workloads/hpc/enable-infiniband.md)に関するページを参照してください。


### <a name="cluster-configuration-options"></a>クラスター構成オプション

Azure には、次に示すような、RDMA ネットワークを使用して通信できる Linux HPC VM のクラスターを作成するためのオプションがいくつか用意されています。 

* **仮想マシン** - 同じ可用性セット内に RDMA 対応の HPC VM をデプロイします (Azure Resource Manager デプロイ モデルを使用する場合)。 クラシック デプロイ モデルを使用する場合は、同じクラウド サービス内に VM をデプロイします。 

* **Virtual Machine Scale Sets** - 仮想マシン スケール セットで、単一の配置グループにデプロイを制限するようにします。 たとえば、Resource Manager テンプレートでは、`singlePlacementGroup` プロパティを `true` に設定します。 

* **仮想マシン間の MPI** - 仮想マシン (VM) 間で MPI 通信が必要な場合は、VM が同じ可用性セットまたは仮想マシン スケール セットに含まれていることを確認します。

* **Azure CycleCloud** - [Azure CycleCloud](/azure/cyclecloud/) 内に HPC クラスターを作成して、Linux ノード上で MPI ジョブを実行します。

* **Azure Batch** - [Azure Batch](/azure/batch/) プールを作成して、Linux コンピューティング ノード上で MPI ワークロードを実行します。 詳細については、「[Batch プールでの RDMA 対応または GPU 対応インスタンスの使用](../../batch/batch-pool-compute-intensive-sizes.md)」を参照してください。 また、Batch でのコンテナー ベースのワークロードの実行について [Batch Shipyard](https://github.com/Azure/batch-shipyard) プロジェクトも参照してください。

* **Microsoft HPC Pack** - [HPC Pack](https://docs.microsoft.com/powershell/high-performance-computing/overview) は、Windows Server ヘッド ノードによって管理される、RDMA 対応の Azure VM にデプロイされたコンピューティング ノード上での複数の Linux ディストリビューションの実行をサポートします。 デプロイの例については、[Azure での HPC Pack Linux RDMA クラスターの作成](https://docs.microsoft.com/powershell/high-performance-computing/hpcpack-linux-openfoam)に関するページを参照してください。


### <a name="network-considerations"></a>ネットワークに関する考慮事項
* SR-IOV に対応していない、Azure の RDMA 対応 Linux VM の場合、eth1 は RDMA のネットワーク トラフィック用に予約されています。 eth1 設定や、このネットワークを参照する構成ファイル内の情報を変更しないでください。
* SR-IOV に対応した VM (HB および HC シリーズ) では、ib0 は RDMA のネットワーク トラフィック用に予約されています。
* Azure の RDMA ネットワークでは、アドレス空間 172.16.0.0/16 は予約済みです。 Azure 仮想ネットワークにデプロイ済みのインスタンスで MPI アプリケーションを実行する場合、仮想ネットワークのアドレス空間が RDMA ネットワークと重複しないようにしてください。
* クラスター管理ツールの選択によっては、MPI ジョブを実行するために追加のシステム構成が必要になることがあります。 たとえば、VM のクラスター上では、SSH キーを生成するか、またはパスワードなしの SSH ログインを確立することによって、クラスター ノード間の信頼を確立することが必要になる場合があります。


## <a name="other-sizes"></a>その他のサイズ
- [汎用](sizes-general.md)
- [コンピューティングの最適化](sizes-compute.md)
- [メモリの最適化](sizes-memory.md)
- [ストレージの最適化](sizes-storage.md)
- [GPU](../windows/sizes-gpu.md)
- [旧世代](sizes-previous-gen.md)

## <a name="next-steps"></a>次の手順

- Azure 上で [HPC ワークロード](../workloads/hpc/configure.md)をセットアップ、最適化、およびスケーリングする方法を確認します。
- [Azure コンピューティング ユニット (ACU)](acu.md) を確認することで、Azure SKU 全体の処理性能を比較できます。
