---
title: プールの VM サイズを選択する - Azure Batch | Microsoft Docs
description: Azure Batch プールのコンピューティング ノード用に使用可能な VM サイズを選択する方法
services: batch
documentationcenter: ''
author: laurenhughes
manager: gwallace
editor: ''
ms.assetid: ''
ms.service: batch
ms.workload: ''
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/01/2019
ms.author: lahugh
ms.custom: seodec18
ms.openlocfilehash: fd88f8e9b32b3fe5a0d7ab0caf233098ea19fde0
ms.sourcegitcommit: 4b431e86e47b6feb8ac6b61487f910c17a55d121
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/18/2019
ms.locfileid: "68323104"
---
# <a name="choose-a-vm-size-for-compute-nodes-in-an-azure-batch-pool"></a>Azure Batch プールのコンピューティング ノード用の VM サイズを選択する

Azure Batch プールのノード サイズを選択するときは、Azure で使用可能なほぼすべての VM サイズを選択することができます。 Azure では、さまざまなワークロードに対応する Linux および Windows VM の幅広いサイズが提供されています。

VM サイズを選択する際には次のような例外と制限事項があります。

* Batch ではサポートされていない VM シリーズまたは VM サイズもあります。
* いくつかの VM サイズは制限されており、割り当てる前に個別に有効化する必要があります。

## <a name="supported-vm-series-and-sizes"></a>サポートされている VM シリーズおよびサイズ

### <a name="pools-in-virtual-machine-configuration"></a>仮想マシン構成のプール

仮想マシン構成の Batch プールでは、ほぼすべての VM サイズ ([Linux](../virtual-machines/linux/sizes.md)、[Windows](../virtual-machines/windows/sizes.md)) がサポートされています。 サポートされているサイズと制限の詳細については、次の表を参照してください。

記載されていないプロモーション用またはプレビュー VM サイズのサポートは保証されていません。

| VM シリーズ  | サポートされるサイズ | Batch アカウントのプール割り当てモード<sup>1</sup> |
|------------|---------|-----------------|
| 基本の A シリーズ | Basic_A0 (A0) を*除く*すべてのサイズ | 任意 |
| A シリーズ | Standard_A0 を*除く*すべてのサイズ | 任意 |
| Av2 シリーズ | すべてのサイズ | 任意 |
| B シリーズ | なし | 使用できません。 |
| DC シリーズ | なし | 使用できません。 |
| Dv2/DSv2 シリーズ | すべてのサイズ | 任意 |
| Dv3/DSv3 シリーズ | すべてのサイズ | 任意 |
| [メモリ最適化済みのサイズ](../virtual-machines/linux/sizes-memory.md) | なし | 使用できません。 |
| Fsv2 シリーズ | すべてのサイズ | 任意 |
| H シリーズ | すべてのサイズ | 任意 |
| HB シリーズ<sup>2</sup> | すべてのサイズ | 任意 |
| HC シリーズ<sup>2</sup> | すべてのサイズ | 任意 |
| Ls シリーズ | すべてのサイズ | 任意 |
| Lsv2 シリーズ | なし | 使用できません。 |
| M シリーズ | Standard_M64ms (低優先度のみ)、Standard_M128s (低優先度のみ) | 任意 |  
| NCv2 シリーズ<sup>2</sup> | すべてのサイズ | 任意 |
| NCv3 シリーズ<sup>2</sup> | すべてのサイズ | 任意 |
| ND シリーズ<sup>2</sup> | すべてのサイズ | 任意 |
| NDv2 シリーズ | すべてのサイズ | ユーザー サブスクリプション モード |
| NV シリーズ | すべてのサイズ | 任意 |
| NVv3 シリーズ | なし | 使用できません。 |
| SAP HANA | なし | 使用できません。 |

<sup>1</sup> 一部の新しい VM シリーズは、最初は部分的にサポートされます。 これらの VM シリーズは、**プール割り当てモード**が**ユーザー サブスクリプション**に設定された Batch アカウントで割り当てることができます。 Batch アカウントの構成の詳細については、[Batch アカウントの管理](batch-account-create-portal.md#additional-configuration-for-user-subscription-mode)に関するセクションをご覧ください。 **ユーザー サブスクリプション**の Batch アカウントを対象とする、これらの部分的にサポートされた VM シリーズのクォータを要求する方法については、[クォータと制限](batch-quota-limit.md)に関する記事をご覧ください。  

<sup>2</sup> これらの VM サイズは仮想マシン構成の Batch プールに割り当てることができますが、特定の[クォータ引き上げ](batch-quota-limit.md#increase-a-quota)を要求する必要があります。

### <a name="pools-in-cloud-service-configuration"></a>クラウド サービス構成のプール

クラウド サービス構成のバッチ プールでは、次を "**除く**" すべての [Cloud Services 向け VM サイズ](../cloud-services/cloud-services-sizes-specs.md)がサポートされます。

| VM シリーズ  | サポートされないサイズ |
|------------|-------------------|
| A シリーズ   | XS       |
| Av2 シリーズ | Standard_A1_v2、Standard_A2_v2、Standard_A2m_v2 |

## <a name="size-considerations"></a>サイズの考慮事項

* **アプリケーションの要件** - ノード上で実行するアプリケーションの特性と要件を考慮してください。 アプリケーションがマルチスレッドであるかどうかや、どのくらいのメモリが消費されるかという点が、最も適切でコスト効率の高いノード サイズを選ぶうえでのヒントになります。 マルチインスタンスの [MPI ワークロード](batch-mpi.md)または CUDA アプリケーションについては、それぞれ特別な [HPC](../virtual-machines/linux/sizes-hpc.md) または [GPU 対応](../virtual-machines/linux/sizes-gpu.md)の VM サイズを検討してください。 (「[Batch プールでの RDMA 対応または GPU 対応インスタンスの使用](batch-pool-compute-intensive-sizes.md)」を参照してください。)

* **ノードあたりのタスク数** - ノードのサイズは、そこで一度に実行されるタスクが 1 つであるという想定で選択するのが一般的です。 しかしジョブの実行中に、コンピューティング ノードで複数のタスク (ひいては複数のアプリケーション インスタンス) が[並列実行](batch-parallel-node-tasks.md)できるとメリットがあります。 そのような場合は、タスクを並列に実行するという需要の増大に対応するために、マルチコア ノード サイズを選ぶことが基本です。

* **さまざまなタスクの負荷水準** - プール内のすべてのノードは同じサイズです。 システム要件や負荷水準の異なる複数のアプリケーションを実行する場合は、プールを分けることをお勧めします。

* **利用可能なリージョン** - Batch アカウントを作成したリージョンで、VM シリーズまたはサイズが利用できない場合があります。 特定のサイズが使用可能かどうかを確認するには、「[リージョン別の利用可能な製品](https://azure.microsoft.com/regions/services/)」をご覧ください。

* **クォータ** - Batch アカウントの[コア クォータ](batch-quota-limit.md#resource-quotas)により、Batch プールに追加できる指定されたサイズのノードの数を制限できます。 クォータの引き上げを要求するには、[この記事](batch-quota-limit.md#increase-a-quota)をご覧ください。 

* **プールの構成** - 一般的に、仮想マシン構成のプールを作成する際は、クラウド サービス構成と比較して VM サイズ オプションが増えます。

## <a name="next-steps"></a>次の手順

* Batch の詳細な概要については、「[Batch を使って大規模な並列コンピューティング ソリューションを開発する](batch-api-basics.md)」を参照してください。
* コンピューティング集中型 VM のサイズを使用する方法について詳しくは、「[Batch プールでの RDMA 対応または GPU 対応インスタンスの使用](batch-pool-compute-intensive-sizes.md)」をご覧ください。
