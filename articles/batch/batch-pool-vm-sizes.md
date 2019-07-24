---
title: プールの VM サイズを選択する - Azure Batch | Microsoft Docs
description: Azure Batch プールのコンピューティング ノード用に使用可能な VM サイズを選択する方法
services: batch
documentationcenter: ''
author: laurenhughes
manager: jeconnoc
editor: ''
ms.assetid: ''
ms.service: batch
ms.workload: ''
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/25/2019
ms.author: lahugh
ms.custom: seodec18
ms.openlocfilehash: 43094839c9da9b00c97d1dffd53f98a3acd119d5
ms.sourcegitcommit: 79038221c1d2172c0677e25a1e479e04f470c567
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/19/2019
ms.locfileid: "56417144"
---
# <a name="choose-a-vm-size-for-compute-nodes-in-an-azure-batch-pool"></a>Azure Batch プールのコンピューティング ノード用の VM サイズを選択する

Azure Batch プールのノード サイズを選択するときは、Azure で使用可能なほぼすべての VM サイズを選択することができます。 Azure では、さまざまなワークロードに対応する Linux および Windows VM の幅広いサイズが提供されています。

VM サイズを選択する際には次のような例外と制限事項があります。

* いくつかの VM ファミリまたは VM サイズはバッチではサポートされていません。 
* いくつかの VM サイズは制限されており、割り当てる前に個別に有効化する必要があります。

## <a name="supported-vm-families-and-sizes"></a>サポートされている VM ファミリおよびサイズ

### <a name="pools-in-virtual-machine-configuration"></a>仮想マシン構成のプール

仮想マシン構成のバッチ プールでは、次を "*除く*" すべての VM サイズ ([Linux](../virtual-machines/linux/sizes.md)、[Windows](../virtual-machines/windows/sizes.md)) がサポートされます。

| ファミリ  | サポートされないサイズ  |
|---------|---------|
| 基本の A シリーズ | Basic_A0 (A0) |
| A シリーズ | Standard_A0 |
| B シリーズ | All |
| DC シリーズ | All |
| 最大メモリ最適化 | All |
| Hb シリーズ<sup>1、2</sup> | All |
| Hc シリーズ<sup>1、2</sup> | All |
| Lsv2 シリーズ | All |
| NDv2 シリーズ<sup>1、2</sup> | All |
| NVv2 シリーズ<sup>1</sup> | All |
| SAP HANA | All |


<sup>1</sup> サポートが計画されています。  
<sup>2</sup> Batch アカウントがユーザー サブスクリプション モードで使用できます。ユーザー サブスクリプション モードの Batch アカウントでは、コア クォータが設定されている必要があります。 詳細については、「[ユーザー サブスクリプション モードのための構成](batch-account-create-portal.md#additional-configuration-for-user-subscription-mode)」を参照してください。

次の VM サイズは優先順位の低いノードでのみサポートされます。

| ファミリ  | サポートされるサイズ  |
|---------|---------|
| M シリーズ | Standard_M64ms |
| M シリーズ | Standard_M128s |

M シリーズ ファミリの他の VM サイズは、現在サポートされていません。

### <a name="pools-in-cloud-service-configuration"></a>クラウド サービス構成のプール

クラウド サービス構成のバッチ プールでは、次を "*除く*" すべての [Cloud Services 向け VM サイズ](../cloud-services/cloud-services-sizes-specs.md)がサポートされます。

| ファミリ  | サポートされないサイズ  |
|---------|---------|
| A シリーズ | ExtraSmall |
| Av2 シリーズ | Standard_A1_v2、Standard_A2_v2、Standard_A2m_v2 |

## <a name="restricted-vm-families"></a>制限される VM ファミリ

次の VM ファミリはバッチ プールに割り当てることができますが、特定のクォータ引き上げを要求する必要があります ([この記事](batch-quota-limit.md#increase-a-quota)を参照)。

* NCv2 シリーズ
* NCv3 シリーズ
* ND シリーズ

これらのサイズは、仮想マシン構成のプールでのみ使用できます。

## <a name="size-considerations"></a>サイズの考慮事項

* **アプリケーションの要件** - ノード上で実行するアプリケーションの特性と要件を考慮してください。 アプリケーションがマルチスレッドであるかどうかや、どのくらいのメモリが消費されるかという点が、最も適切でコスト効率の高いノード サイズを選ぶうえでのヒントになります。 マルチインスタンスの [MPI ワークロード](batch-mpi.md)または CUDA アプリケーションについては、それぞれ特別な [HPC](../virtual-machines/linux/sizes-hpc.md) または [GPU 対応](../virtual-machines/linux/sizes-gpu.md)の VM サイズを検討してください。 (「[Batch プールでの RDMA 対応または GPU 対応インスタンスの使用](batch-pool-compute-intensive-sizes.md)」を参照してください。)

* **ノードあたりのタスク数** - ノードのサイズは、そこで一度に実行されるタスクが 1 つであるという想定で選択するのが一般的です。 しかしジョブの実行中に、コンピューティング ノードで複数のタスク (ひいては複数のアプリケーション インスタンス) が[並列実行](batch-parallel-node-tasks.md)できるとメリットがあります。 そのような場合は、タスクを並列に実行するという需要の増大に対応するために、マルチコア ノード サイズを選ぶことが基本です。

* **さまざまなタスクの負荷水準** - プール内のすべてのノードは同じサイズです。 システム要件や負荷水準の異なる複数のアプリケーションを実行する場合は、プールを分けることをお勧めします。 

* **利用可能なリージョン** - VM ファミリまたはサイズが、Batch アカウントを作成したリージョンで使用できない場合があります。 特定のサイズが使用可能かどうかを確認するには、「[リージョン別の利用可能な製品](https://azure.microsoft.com/regions/services/)」をご覧ください。

* **クォータ** - Batch アカウントの[コア クォータ](batch-quota-limit.md#resource-quotas)により、Batch プールに追加できる指定されたサイズのノードの数を制限できます。 クォータの引き上げを要求するには、[この記事](batch-quota-limit.md#increase-a-quota)をご覧ください。 

* **プールの構成** - 一般的に、仮想マシン構成のプールを作成する際は、クラウド サービス構成と比較して VM サイズ オプションが増えます。

## <a name="next-steps"></a>次の手順

* Batch の詳細な概要については、「[Batch を使って大規模な並列コンピューティング ソリューションを開発する](batch-api-basics.md)」を参照してください。
* コンピューティング集中型 VM のサイズを使用する方法について詳しくは、「[Batch プールでの RDMA 対応または GPU 対応インスタンスの使用](batch-pool-compute-intensive-sizes.md)」をご覧ください。
