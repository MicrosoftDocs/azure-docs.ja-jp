---
title: "Linux でのコンピューティング集中型 VM について | Microsoft Docs"
description: "Linux VM の H シリーズと A8、A9、A10、A11 というコンピューティング集中型サイズの背景情報と使用上の注意事項について説明します。"
services: virtual-machines-linux
documentationcenter: 
author: dlepow
manager: timlt
editor: 
tags: azure-resource-manager,azure-service-management
ms.assetid: 16cf6e2d-f401-4b22-af8c-9a337179f5f6
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 03/14/2017
ms.author: danlep
ms.custom: H1Hack27Feb2017
translationtype: Human Translation
ms.sourcegitcommit: 356de369ec5409e8e6e51a286a20af70a9420193
ms.openlocfilehash: e2e39b0fa9c45027c51bc5e063df990faf934ff5
ms.lasthandoff: 03/27/2017

---
# <a name="about-h-series-and-compute-intensive-a-series-vms-for-linux"></a>Linux 用の H シリーズとコンピューティング集中型 A シリーズの VM について
この記事では、新しい Azure H シリーズとそれ以前の A8、A9、A10、A11 サイズ (*コンピューティング集中型*インスタンスとも呼ばれます) の背景情報と使用上の考慮事項を示します。 この記事は、Linux VM のこれらのサイズの使用方法について説明していますが、 [Windows VM](virtual-machines-windows-a8-a9-a10-a11-specs.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) に使用することもできます。 

基本的な仕様、ストレージの容量、ディスクの詳細については、[仮想マシンのサイズ](virtual-machines-linux-sizes.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)に関する記事をご覧ください。

[!INCLUDE [virtual-machines-common-a8-a9-a10-a11-specs](../../includes/virtual-machines-common-a8-a9-a10-a11-specs.md)]

## <a name="access-to-the-rdma-network"></a>RDMA ネットワークへのアクセス
Azure RDMA ネットワークを活用するために、次のサポートされる Linux HPC ディストリビューションのいずれかとサポートされる MPI 実装を実行する、RDMA 対応 Linux VM のクラスターを作成することができます。 デプロイのオプションとサンプルの構成手順については、「[MPI アプリケーションを実行するように Linux RDMA クラスターを設定する](linux/classic/rdma-cluster.md?toc=%2fazure%2fvirtual-machines%2flinux%2fclassic%2ftoc.json)」をご覧ください。

* **ディストリビューション** - Azure Marketplace にある RDMA 対応 SUSE Linux Enterprise Server (SLES) イメージまたは OpenLogic CentOS ベースの HPC イメージから VM をデプロイする必要があります。 次の Marketplace イメージは、RDMA 接続性をサポートします。
  
    * SLES 12 SP1 for HPC、SLES 12 SP1 for HPC (Premium)
    
    * CentOS ベースの 7.1 HPC、CentOS ベースの 6.5 HPC  
 
        > [!NOTE]
        > H シリーズ VM の場合、SLES 12 SP1 for HPC イメージまたは CentOS ベースの 7.1 HPC イメージのいずれかをお勧めします。
        >
        > CentOS ベースの HPC イメージでは、 **yum** 構成ファイルでのカーネルの更新は無効にされています。 これは、Linux RDMA ドライバーが RPM パッケージとして配布されており、カーネルが更新された場合にドライバーの更新プログラムが機能しない可能性があるためです。
        > 
        > 
* **MPI** - Intel MPI Library 5.x
  
    選択した Marketplace イメージに応じて、次に示すような Intel MPI の個別のライセンス、インストール、または構成が必要になることがあります。 
  
  * **SLES 12 SP1 for HPC image** - Intel MPI パッケージは VM に配布されます。 次のコマンドを実行して をインストールします。

      ```bash
      sudo rpm -v -i --nodeps /opt/intelMPI/intel_mpi_packages/*.rpm
      ```

  * **CentOS ベースの HPC イメージ** - Intel MPI 5.1 は既にインストール済みです。  
    
    クラスター化された VM で MPI ジョブを実行するには、追加のシステム構成が必要です。 たとえば、VM のクラスターでは、コンピューティング ノード間で信頼関係を確立する必要があります。 標準的な設定については、「[MPI アプリケーションを実行するように Linux RDMA クラスターを設定する](linux/classic/rdma-cluster.md?toc=%2fazure%2fvirtual-machines%2flinux%2fclassic%2ftoc.json)」をご覧ください。

## <a name="considerations-for-hpc-pack-and-linux"></a>HPC Pack および Linux を使用する場合の考慮事項
[HPC Pack](https://technet.microsoft.com/library/jj899572.aspx)(Microsoft が無償で提供している HPC クラスターとジョブの管理ソリューション) は、Linux でコンピューティング集中型インスタンスを使用するための選択肢の 1 つです。 最新リリースの HPC Pack では、Azure VM にデプロイされ、Windows Server ヘッド ノードで管理されるコンピューティング ノードで、複数の Linux ディストリビューションを実行できるようになりました。 HPC Pack を使用すると、Intel MPI を実行する RDMA 対応 Linux コンピューティング ノードで、RDMA ネットワークにアクセスする MPI アプリケーションのスケジュール設定と実行が可能です。 作業を開始するには、「[Azure の HPC Pack クラスターで Linux コンピューティング ノードの使用を開始する](linux/classic/hpcpack-cluster.md?toc=%2fazure%2fvirtual-machines%2flinux%2fclassic%2ftoc.json)」をご覧ください。

## <a name="network-topology-considerations"></a>ネットワーク トポロジに関する考慮事項
* Azure の RDMA 対応 Linux VM の場合、Eth1 は RDMA のネットワーク トラフィック用に予約されています。 Eth1 設定や、このネットワークを参照する構成ファイル内の情報を変更しないでください。 Eth0 は Azure の通常のネットワーク トラフィック用に予約されています。
* Azure では、IP over InfiniBand (IB) はサポートされません。 RDMA over IB のみがサポートされます。



## <a name="next-steps"></a>次のステップ
* コンピューティング集中型サイズの可用性と価格の詳細については、 [Virtual Machines の価格](https://azure.microsoft.com/pricing/details/virtual-machines/#Linux)に関するページを参照してください。
* ストレージの容量とディスクの詳細については、「[仮想マシンのサイズ](virtual-machines-linux-sizes.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)」をご覧ください。
* Linux 上の RDMA によりコンピューティング集中型サイズのデプロイと使用を開始する方法については、「[MPI アプリケーションを実行するように Linux RDMA クラスターを設定する](linux/classic/rdma-cluster.md?toc=%2fazure%2fvirtual-machines%2flinux%2fclassic%2ftoc.json)」をご覧ください。


