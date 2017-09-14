---
title: "Azure Linux VM のサイズ - HPC | Microsoft Docs"
description: "Azure の Linux ハイ パフォーマンス コンピューティング仮想マシンで使用できるさまざまなサイズを一覧表示します。"
services: virtual-machines-linux
documentationcenter: 
author: jonbeck7
manager: timlt
editor: 
tags: azure-resource-manager,azure-service-management
ms.assetid: 
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 07/28/2017
ms.author: jonbeck
ms.translationtype: HT
ms.sourcegitcommit: bde1bc7e140f9eb7bb864c1c0a1387b9da5d4d22
ms.openlocfilehash: 4962c26e6bf2ed36ce670cc78f4ecfcab871a8bf
ms.contentlocale: ja-jp
ms.lasthandoff: 07/21/2017

---

# <a name="high-performance-compute-linux-vm-sizes"></a>ハイ パフォーマンス コンピューティング Linux VM のサイズ

[!INCLUDE [virtual-machines-common-sizes-hpc](../../../includes/virtual-machines-common-sizes-hpc.md)]

[!INCLUDE [virtual-machines-common-sizes-table-defs](../../../includes/virtual-machines-common-sizes-table-defs.md)]

[!INCLUDE [virtual-machines-common-a8-a9-a10-a11-specs](../../../includes/virtual-machines-common-a8-a9-a10-a11-specs.md)]

## <a name="rdma-capable-instances"></a>RDMA 対応のインスタンス
コンピューティング集中型インスタンス (H16r、H16mr、A8、A9) のサブセットには、リモート ダイレクト メモリ アクセス (RDMA) 接続のためのネットワーク インターフェイスが備わっています。 このインターフェイスは、標準の Azure ネットワーク インターフェイスと同様に、他の VM サイズでも利用可能です。 
  
このインターフェイスにより、RDMA 対応インスタンスは InfiniBand ネットワークを介して通信することができ、H16r と H16mr の仮想マシンでは FDR のレートで、A8 と A9 の仮想マシンでは QDR のレートで動作します。 これらの RDMA 機能により、Message Passing Interface (MPI) アプリケーションのスケーラビリティとパフォーマンスが向上します。

RDMA 対応の Linux VM が Azure RDMA ネットワークにアクセスするための要件は、次のとおりです。
 
* **ディストリビューション** - Azure Marketplace にある RDMA 対応 SUSE Linux Enterprise Server (SLES) イメージまたは Rogue Wave Software (以前の OpenLogic) CentOS ベースの HPC イメージから VM をデプロイする必要があります。 次の Marketplace イメージは、RDMA 接続性をサポートします。
  
    * SLES 12 SP1 for HPC、SLES 12 SP1 for HPC (Premium)
    
    * CentOS ベースの 7.3 HPC、CentOS ベースの 7.1 HPC、CentOS ベースの 6.8 HPC、CentOS-based 6.5 HPC  
 
        > [!NOTE]
        > H シリーズ VM の場合、SLES 12 SP1 for HPC イメージまたは CentOS ベースの 7.1 以降の HPC イメージのいずれかをお勧めします。
        >
        > CentOS ベースの HPC イメージでは、 **yum** 構成ファイルでのカーネルの更新は無効にされています。 これは、Linux RDMA ドライバーが RPM パッケージとして配布されており、カーネルが更新された場合にドライバーの更新プログラムが機能しない可能性があるためです。
        > 
        > 
* **MPI** - Intel MPI Library 5.x
  
    選択した Marketplace イメージに応じて、次に示すような Intel MPI の個別のライセンス、インストール、または構成が必要になることがあります。 
  
  * **SLES 12 SP1 for HPC image** - Intel MPI パッケージは VM に配布されます。 次のコマンドを実行してインストールします。

      ```bash
      sudo rpm -v -i --nodeps /opt/intelMPI/intel_mpi_packages/*.rpm
      ```

  * **CentOS ベースの HPC イメージ** - Intel MPI 5.1 は既にインストール済みです。  
    
    クラスター化された VM で MPI ジョブを実行するには、追加のシステム構成が必要です。 たとえば、VM のクラスターでは、コンピューティング ノード間で信頼関係を確立する必要があります。 標準的な設定については、「[MPI アプリケーションを実行するように Linux RDMA クラスターを設定する](classic/rdma-cluster.md?toc=%2fazure%2fvirtual-machines%2flinux%2fclassic%2ftoc.json)」をご覧ください。

### <a name="network-topology-considerations"></a>ネットワーク トポロジに関する考慮事項
* Azure の RDMA 対応 Linux VM の場合、Eth1 は RDMA のネットワーク トラフィック用に予約されています。 Eth1 設定や、このネットワークを参照する構成ファイル内の情報を変更しないでください。 Eth0 は Azure の通常のネットワーク トラフィック用に予約されています。

* Azure では、IP over InfiniBand (IB) はサポートされません。 RDMA over IB のみがサポートされます。

## <a name="using-hpc-pack"></a>HPC Pack の使用
[HPC Pack](https://technet.microsoft.com/library/jj899572.aspx) (Microsoft が無償で提供している HPC クラスターとジョブの管理ソリューション) は、Linux でコンピューティング集中型インスタンスを使用するための選択肢の 1 つです。 最新リリースの HPC Pack では、Azure VM にデプロイされ、Windows Server ヘッド ノードで管理されるコンピューティング ノードで、複数の Linux ディストリビューションを実行できるようになりました。 HPC Pack を使用すると、Intel MPI を実行する RDMA 対応 Linux コンピューティング ノードで、RDMA ネットワークにアクセスする MPI アプリケーションのスケジュール設定と実行が可能です。 「[Azure の HPC Pack クラスターで Linux コンピューティング ノードの使用を開始する](classic/hpcpack-cluster.md?toc=%2fazure%2fvirtual-machines%2flinux%2fclassic%2ftoc.json)」を参照してください。

## <a name="other-sizes"></a>その他のサイズ
- [汎用](sizes-general.md)
- [コンピューティングの最適化](sizes-compute.md)
- [メモリの最適化](sizes-memory.md)
- [ストレージの最適化](sizes-storage.md)
- [GPU](../windows/sizes-gpu.md)


## <a name="next-steps"></a>次のステップ

- Linux 上の RDMA によりコンピューティング集中型サイズのデプロイと使用を開始する方法については、「[MPI アプリケーションを実行するように Linux RDMA クラスターを設定する](classic/rdma-cluster.md?toc=%2fazure%2fvirtual-machines%2flinux%2fclassic%2ftoc.json)」をご覧ください。

- [Azure コンピューティング ユニット (ACU)](acu.md) を確認することで、Azure SKU 全体の処理性能を比較できます。





