---
title: プールの VM サイズを選択する
description: Azure Batch プールのコンピューティング ノード用に使用可能な VM サイズを選択する方法
ms.topic: conceptual
ms.date: 08/07/2020
ms.custom: seodec18
ms.openlocfilehash: 9aef1fc21120401252d188b7373c6ce4139c71c4
ms.sourcegitcommit: 98854e3bd1ab04ce42816cae1892ed0caeedf461
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/07/2020
ms.locfileid: "88005143"
---
# <a name="choose-a-vm-size-for-compute-nodes-in-an-azure-batch-pool"></a>Azure Batch プールのコンピューティング ノード用の VM サイズを選択する

Azure Batch プールのノード サイズを選択するときは、Azure で使用可能なほぼすべての VM サイズを選択することができます。 Azure では、さまざまなワークロードに対応する Linux および Windows VM の幅広いサイズが提供されています。

VM サイズを選択する際には次のような例外と制限事項があります。

* Batch ではサポートされていない VM シリーズまたは VM サイズもあります。
* いくつかの VM サイズは制限されており、割り当てる前に個別に有効化する必要があります。

## <a name="supported-vm-series-and-sizes"></a>サポートされている VM シリーズおよびサイズ

### <a name="pools-in-virtual-machine-configuration"></a>仮想マシン構成のプール

仮想マシン構成の Batch プールでは、ほぼすべての VM サイズ ([Linux](../virtual-machines/linux/sizes.md)、[Windows](../virtual-machines/windows/sizes.md)) がサポートされています。 サポートされているサイズと制限の詳細については、次の表を参照してください。

| VM シリーズ  | サポートされるサイズ |
|------------|---------|
| Basic A | Basic_A0 (A0) を*除く*すべてのサイズ |
| A | Standard_A0 を*除く*すべてのサイズ |
| Av2 | すべてのサイズ |
| B | なし |
| DC | なし |
| Dv2、DSv2 | すべてのサイズ |
| Dv3、Dsv3 | すべてのサイズ |
| Dav4<sup>1</sup> | すべてのサイズ |
| Dasv4<sup>1</sup> | すべてのサイズ |
| Ddv4、Ddsv4 |  なし - まだ使用できません |
| Ev3、Esv3 | すべてのサイズ (E64is_v3 と E64i_v3 を除く) |
| Eav4<sup>1</sup> | すべてのサイズ |
| Easv4<sup>1</sup> | すべてのサイズ |
| Edv4、Edsv4 |  なし - まだ使用できません |
| F、Fs | すべてのサイズ |
| Fsv2 | すべてのサイズ |
| G、Gs | すべてのサイズ |
| H | すべてのサイズ |
| HB<sup>1</sup> | すべてのサイズ |
| HBv2<sup>1</sup> | すべてのサイズ |
| HC<sup>1</sup> | すべてのサイズ |
| Ls | すべてのサイズ |
| Lsv2<sup>1</sup> | すべてのサイズ |
| M<sup>1</sup> | すべてのサイズ |
| Mv2 | なし - まだ使用できません |
| NC | すべてのサイズ |
| NCv2<sup>1</sup> | すべてのサイズ |
| NCv3<sup>1</sup> | すべてのサイズ |
| ND<sup>1</sup> | すべてのサイズ |
| NDv2<sup>1</sup> | なし - まだ使用できません |
| NV | すべてのサイズ |
| NVv3<sup>1</sup> | すべてのサイズ |
| NVv4 | なし |
| SAP HANA | なし |

<sup>1</sup> これらの VM サイズは仮想マシン構成の Batch プールに割り当てることができますが、新しい Batch アカウントを作成し、特定の[クォータ引き上げ](batch-quota-limit.md#increase-a-quota)を要求する必要があります。 この制限は、VM シリーズごとの vCPU クォータが Batch アカウントで完全にサポートされると削除されます。

### <a name="pools-in-cloud-service-configuration"></a>クラウド サービス構成のプール

クラウド サービス構成のバッチ プールでは、次を**除く**すべての [Cloud Services 向け VM サイズ](../cloud-services/cloud-services-sizes-specs.md)がサポートされます。

| VM シリーズ  | サポートされないサイズ |
|------------|-------------------|
| A シリーズ   | XS       |
| Av2 シリーズ | Standard_A1_v2、Standard_A2_v2、Standard_A2m_v2 |

## <a name="size-considerations"></a>サイズの考慮事項

* **アプリケーションの要件** - ノード上で実行するアプリケーションの特性と要件を考慮してください。 アプリケーションがマルチスレッドであるかどうかや、どのくらいのメモリが消費されるかという点が、最も適切でコスト効率の高いノード サイズを選ぶうえでのヒントになります。 マルチインスタンスの [MPI ワークロード](batch-mpi.md)または CUDA アプリケーションについては、それぞれ特別な [HPC](../virtual-machines/sizes-hpc.md) または [GPU 対応](../virtual-machines/sizes-gpu.md)の VM サイズを検討してください。 (「[Batch プールでの RDMA 対応または GPU 対応インスタンスの使用](batch-pool-compute-intensive-sizes.md)」を参照してください。)

* **ノードあたりのタスク数** - ノードのサイズは、そこで一度に実行されるタスクが 1 つであるという想定で選択するのが一般的です。 しかしジョブの実行中に、コンピューティング ノードで複数のタスク (ひいては複数のアプリケーション インスタンス) が[並列実行](batch-parallel-node-tasks.md)できるとメリットがあります。 そのような場合は、タスクを並列に実行するという需要の増大に対応するために、マルチコア ノード サイズを選ぶことが基本です。

* **さまざまなタスクの負荷水準** - プール内のすべてのノードは同じサイズです。 システム要件や負荷水準の異なる複数のアプリケーションを実行する場合は、プールを分けることをお勧めします。

* **利用可能なリージョン** - Batch アカウントを作成したリージョンで、VM シリーズまたはサイズが利用できない場合があります。 特定のサイズが使用可能かどうかを確認するには、「[リージョン別の利用可能な製品](https://azure.microsoft.com/regions/services/)」をご覧ください。

* **クォータ** - Batch アカウントの[コア クォータ](batch-quota-limit.md#resource-quotas)により、Batch プールに追加できる指定されたサイズのノードの数を制限できます。 クォータの引き上げを要求するには、[この記事](batch-quota-limit.md#increase-a-quota)をご覧ください。 

* **プールの構成** - 一般的に、仮想マシン構成のプールを作成する際は、クラウド サービス構成と比較して VM サイズ オプションが増えます。

## <a name="next-steps"></a>次のステップ

* [Batch サービスのワークフローと主要なリソース](batch-service-workflow-features.md) (プール、ノード、ジョブ、タスクなど) について学習します。
* コンピューティング集中型 VM のサイズを使用する方法について詳しくは、「[Batch プールでの RDMA 対応または GPU 対応インスタンスの使用](batch-pool-compute-intensive-sizes.md)」をご覧ください。
