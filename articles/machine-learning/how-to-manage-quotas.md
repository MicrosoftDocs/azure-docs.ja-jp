---
title: リソースとクォータを管理する
titleSuffix: Azure Machine Learning
description: Azure Machine Learning のリソースにおけるクォータと制限、およびクォータの引き上げを要求する方法について説明します。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.reviewer: jmartens
author: SimranArora904
ms.author: siarora
ms.date: 12/1/2020
ms.topic: conceptual
ms.custom: troubleshooting,contperf-fy20q4, contperf-fy21q2
ms.openlocfilehash: 4e61a15b86d1d2d05889253f615eec0865c87a70
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102520389"
---
# <a name="manage-and-increase-quotas-for-resources-with-azure-machine-learning"></a>Azure Machine Learning を使用するリソースのクォータの管理と引き上げ

Azure では、不正による予算超過を防ぎ、Azure の容量の制約を尊重するために制限とクォータを使用しています。 運用環境のワークロードに合わせてスケーリングするときは、これらの制限事項について考慮してください。 この記事では、次の内容について説明します。

> [!div class="checklist"]
> + [Azure Machine Learning](overview-what-is-azure-ml.md) に関連する Azure リソースの既定の制限事項。
> + ワークスペースレベルのクォータを作成する。
> + クォータと制限を表示する。
> + クォータの引き上げを依頼する。
> + プライベート エンドポイントと DNS クォータ。

クォータの管理に加えて、[Azure Machine Learning のコストを計画して管理する](concept-plan-manage-cost.md)方法や、[Azure Machine Learning のサービスの制限値](resource-limits-quotas-capacity.md)について説明します。

## <a name="special-considerations"></a>特別な考慮事項

+ クォータは、容量の保証ではなく、クレジット制限です。 大規模な容量が必要な場合は、[Azure サポートに連絡してクォータを引き上げてください](#request-quota-increases)。

+ クォータは、Azure Machine Learning を含め、サブスクリプション内のすべてのサービスで共有されます。 容量を評価するときは、すべてのサービスの使用量を計算します。
 
  Azure Machine Learning コンピューティングは例外です。 コア コンピューティング クォータとは別のクォータがあります。 

+ 既定の制限は、プラン カテゴリの種類 (無料試用版や従量課金制など) と仮想マシン (VM) シリーズ (Dv2、F、G など) によって異なります。

## <a name="default-resource-quotas"></a>既定のリソース クォータ

このセクションでは、次のリソースの既定値とクォータの上限について説明します。

+ Azure Machine Learning 資産
  + Azure Machine Learning コンピューティング
  + Azure Machine Learning パイプライン
+ 仮想マシン
+ Azure Container Instances
+ Azure Storage

> [!IMPORTANT]
> 制限は変更されることがあります。 最新の情報については、「[Azure Machine Learning のサービスの制限値](resource-limits-quotas-capacity.md)」を参照してください。



### <a name="azure-machine-learning-assets"></a>Azure Machine Learning 資産
資産に関する次の制限は、ワークスペースごとに適用されます。 

| **リソース** | **上限** |
| --- | --- |
| データセット | 1,000 万 |
| 実行 | 1,000 万 |
| モデル | 1,000 万|
| Artifacts | 1,000 万 |

さらに、最長 **実行時間** は 30 日、**実行ごとにログされるメトリック** の最大数は 100 万です。

### <a name="azure-machine-learning-compute"></a>Azure Machine Learning コンピューティング
[Azure Machine Learning コンピューティング](concept-compute-target.md#azure-machine-learning-compute-managed)には、サブスクリプションのリージョンごとに許可されるコアの数 (各 VM ファミリと累積合計コア数で分割) と一意のコンピューティング リソースの数の両方に対して、既定のクォータ制限があります。 このクォータは、Azure Machine Learning のマネージド コンピューティング リソースにのみ適用されるため、前のセクションに記載されている VM コア クォータとは異なります。

さまざまな VM ファミリ コアのクォータ、このセクションの合計サブスクリプション コア クォータおよびリソースの制限を引き上げるには、[クォータの引き上げを依頼してください](#request-quota-increases)。

使用可能なリソース:
+ **リージョンあたりの専用コア** には、サブスクリプション プランの種類に応じて、24 から 300 の既定の制限があります。 サブスクリプションあたりの専用コアの数は VM ファミリごとに引き上げることができます。 NCv2、NCv3、ND シリーズなど、特殊な VM ファミリは、ゼロ コアの既定から開始されます。

+ **リージョンあたりの低優先度のコア** には、サブスクリプション プランの種類に応じて、100 から 3,000 の既定の制限があります。 サブスクリプションあたりの優先順位の低いコアの数は増やすことができ、VM ファミリ全体で 1 つの値になります。

+ **リージョンあたりのクラスター** の既定の制限は 200 です。 これらは、トレーニング クラスターとコンピューティング インスタンスの間で共有されます (コンピューティング インスタンスは、クォータの目的では 1 つのノード クラスターと見なされます)。

> [!TIP]
> クォータの引き上げを依頼する VM ファミリの詳細については、[Azure の仮想マシンのサイズ](../virtual-machines/sizes.md)に関するページを参照してください。 たとえば、GPU VM ファミリは、ファミリ名が "N" で始まります (例: NCv3 シリーズ)

次の表は、プラットフォームにおけるその他の制限を示しています。 例外をリクエストするには、**テクニカル** サポート チケットを通じて AzureML 製品チームに連絡してください。

| **リソースまたはアクション** | **上限** |
| --- | --- |
| リソース グループあたりのワークスペース数 | 800 |
| 通信が有効になっていないプールとしてセットアップされた (つまり、MPI ジョブを実行できない) 1 つの Azure Machine Learning コンピューティング (AmlCompute) **クラスター** 内のノード数 | 100 ノード。ただし、65000 ノードまで構成可能 |
| Azure Machine Learning コンピューティング (AmlCompute) クラスターでの 1 回の並列実行ステップ **実行** 時のノード数 | 100 ノード。ただし、クラスターが上記のようにスケーリングするようにセットアップされている場合は、65000 ノードまで構成可能 |
| 通信が有効になっているプールとしてセットアップされた 1 つの Azure Machine Learning コンピューティング (AmlCompute) **クラスター** 内のノード数 | 300 ノード。ただし、4000 ノードまで構成可能 |
| RDMA 対応の VM ファミリ上で通信が有効になっているプールとしてセットアップされた 1 つの Azure Machine Learning コンピューティング (AmlCompute) **クラスター** 内のノード数 | 100 ノード |
| Azure Machine Learning コンピューティング (AmlCompute) クラスターでの 1 回の MPI **実行** 時のノード数 | 100 ノード。ただし、300 ノードまで増やすことが可能 |
| ノードあたりの GPU MPI プロセス数 | 1-4 |
| ノードあたりの GPU worker 数 | 1-4 |
| ジョブの有効期間 | 21 日<sup>1</sup> |
| 優先順位の低いノードでのジョブの有効期間 | 7 日<sup>2</sup> |
| ノードあたりのパラメーター サーバー数 | 1 |

<sup>1</sup> 最大有効期間は、実行が開始されてから完了するまでの期間です。 完了した実行は無期限に保持されます。 最大有効期間内に完了しなかった実行のデータにはアクセスできません。
<sup>2</sup> 容量の制約がある場合は、優先度の低いノードのジョブをいつでも横取りできます。 ジョブにチェックポイントを実装することをお勧めします。

#### <a name="azure-machine-learning-pipelines"></a>Azure Machine Learning パイプライン
[Azure Machine Learning パイプライン](concept-ml-pipelines.md)には次の制限事項があります。

| **リソース** | **制限** |
| --- | --- |
| パイプラインのステップ | 30,000 |
| リソース グループあたりのワークスペース数 | 800 |

### <a name="virtual-machines"></a>仮想マシン
各 Azure サブスクリプションには、すべてのサービスにわたる仮想マシン数の制限があります。 仮想マシン コアには、リージョンの合計の制限とサイズシリーズあたりのリージョンの制限があります。 どちらの制限も個別に適用されます。

たとえば、米国東部で VM のコア上限が 30、A シリーズのコア上限が 30、D シリーズのコア上限が 30 のサブスクリプションがあるとします。 このサブスクリプションを使用すると、30 個の A1 VM、30 個の D1 VM、または合計 30 コアを超えないこれら 2 つの組み合わせをデプロイできます。

次の表に示す値を超えて仮想マシンの制限を引き上げることはできません。

[!INCLUDE [azure-subscription-limits-azure-resource-manager](../../includes/azure-subscription-limits-azure-resource-manager.md)]

### <a name="container-instances"></a>Container Instances

詳細については、「[Container Instances の制限](../azure-resource-manager/management/azure-subscription-service-limits.md#container-instances-limits)」を参照してください。

### <a name="storage"></a>記憶域
Azure Storage では、サブスクリプションおよびリージョンあたりのストレージ アカウント数が 250 に制限されています。 この制限には、Standard および Premium ストレージ アカウントの両方が含まれます。

上限を引き上げるには [Azure サポート](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest/)を通じて依頼してください。 Azure Storage チームがお客様のケースを確認します。1 つのリージョンに対して最大 250 個のストレージ アカウントを承認する場合があります。


## <a name="workspace-level-quotas"></a>ワークスペースレベルのクォータ

同じサブスクリプション内の複数の[ワークスペース](concept-workspace.md)間の Azure Machine Learning コンピューティング ターゲットの割り当てを管理するには、ワークスペース レベル クォータを使用します。

既定では、すべてのワークスペースが VM ファミリのサブスクリプション レベル クォータと同じクォータを共有しています。 ただし、サブスクリプション内のワークスペース上の個々の VM ファミリに最大クォータを設定できます。 これにより、容量を共有し、リソース競合の問題を回避できます。

1. サブスクリプション内の任意のワークスペースに移動します。
1. 左側のペインで、 **[使用量 + クォータ]** を選択します。
1. **[クォータの構成]** タブを選択して、クォータを表示します。
1. VM ファミリを展開します。
1. その VM ファミリの下に表示されているワークスペースにクォータ制限を設定します。

負の値またはサブスクリプション レベルのクォータよりも大きい値は設定できません。

[![Azure Machine Learning のワークスペースレベルのクォータを示すスクリーンショット。](./media/how-to-manage-quotas/azure-machine-learning-workspace-quota.png)](./media/how-to-manage-quotas/azure-machine-learning-workspace-quota.png)

> [!NOTE]
> ワークスペース レベルでクォータを設定するには、サブスクリプションレベルのアクセス許可が必要です。

## <a name="view-your-usage-and-quotas"></a>使用量とクォータの表示

仮想マシン、ストレージ、ネットワークなどのさまざまな Azure リソースのクォータを表示するには、Azure portal を使用します。

1. 左側のペインで、 **[すべてのサービス]** を選択し、 **[一般]** カテゴリの下にある **[サブスクリプション]** を選択します。

2. サブスクリプションの一覧から、検索するクォータのサブスクリプションを選択します。

3. 現在のクォータ制限と使用状況を表示するには、 **[使用量 + クォータ]** を選択します。 フィルターを使用して、プロバイダーと場所を選択します。 

サブスクリプションの Azure Machine Learning コンピューティング クォータは、他の Azure のクォータとは別に管理します。 

1. **Azure portal** で Azure Machine Learning ワークスペースに移動します。

2. 左側のペインにある **[サポート + トラブルシューティング] セクション** で **[使用量 + クォータ]** を選択して、現在のクォータ制限と使用状況を表示します。

3. クォータ制限を表示するサブスクリプションを選択します。 目的のリージョンにフィルターを適用します。

4. サブスクリプション レベル ビューとワークスペース レベル ビューを切り替えることができます。

## <a name="request-quota-increases"></a>クォータの増加を要求

制限を引き上げるまたは既定の制限を超えるクォータが必要な場合は、[オンライン カスタマー サポートに申請](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest/) (無料) してください。

前の表に示されている最大値を超えて制限を引き上げることはできません。 上限がない場合、リソースの制限を調整することはできません。

クォータの引き上げを依頼する場合は、念頭に置いているサービスを選択します。 たとえば、Azure Machine Learning、Container Instances、または Storage を選択します。 Azure Machine Learning コンピューティングの場合、前述の手順でクォータを表示している間に、 **[クォータを要求する]** ボタンを選択する方法もあります。

> [!NOTE]
> [無料試用版サブスクリプション](https://azure.microsoft.com/offers/ms-azr-0044p)は、制限およびクォータ引き上げの適用対象外です。 無料試用版をお持ちの場合は、[従量課金制](https://azure.microsoft.com/offers/ms-azr-0003p/)サブスクリプションにアップグレードしてください。 詳細については、[Azure 無料試用版の従量課金制へのアップグレード](../cost-management-billing/manage/upgrade-azure-subscription.md)と [Azure 無料アカウントに関する FAQ](https://azure.microsoft.com/free/free-account-faq) に関するページを参照してください。

## <a name="private-endpoint-and-private-dns-quota-increases"></a>プライベート エンドポイントとプライベート DNS クォータの引き上げ

1 つのサブスクリプションで作成できるプライベート エンドポイントとプライベート DNS ゾーンの数には制限があります。

Azure Machine Learning の場合、(お客様の) サブスクリプションでリソースが作成されますが、Microsoft が所有するサブスクリプションでリソースが作成されるシナリオがいくつかあります。

 次のシナリオでは、場合によっては Microsoft が所有するサブスクリプションでクォータの割り当てを依頼する必要があります。

* カスタマーマネージド キー (CMK) を使用する Azure Private Link 対応ワークスペース
* Private Link 対応の Azure Kubernetes Service クラスターのワークスペースへのアタッチ

このようなシナリオの許容を依頼するには、次の手順を実行します。

1. [Azure サポート リクエストを作成し](../azure-portal/supportability/how-to-create-azure-support-request.md#create-a-support-request)、 __[基本]__ セクションで次のオプションを選択します。

    | フィールド | 選択 |
    | ----- | ----- |
    | 問題の種類 | **テクニカル** |
    | サービス | **使用中のサービス**。 次に、ドロップダウン リストで __[Machine Learning]__ を選択します。 |
    | 問題の種類 | **ワークスペースの構成とセキュリティ** |
    | 問題のサブタイプ | **プライベート エンドポイントとプライベート DNS ゾーンの許容依頼** |

2. __[詳細]__ セクションで、 __[説明]__ フィールドを使用して、使用する予定の Azure リージョンとシナリオを指定します。 複数のサブスクリプションについてクォータの引き上げを依頼する必要がある場合は、このフィールドにサブスクリプション ID を列挙します。

3. __[作成]__ を選択してリクエストを作成します。

:::image type="content" source="media/how-to-manage-quotas/quota-increase-private-endpoint.png" alt-text="プライベート エンドポイントとプライベート DNS のクォータの引き上げ依頼のスクリーンショット。":::

## <a name="next-steps"></a>次のステップ

+ [Azure Machine Learning のコストを計画して管理する](concept-plan-manage-cost.md)
+ [Azure Machine Learning のサービスの制限値](resource-limits-quotas-capacity.md)