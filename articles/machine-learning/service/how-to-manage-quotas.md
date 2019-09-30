---
title: リソースのクォータの管理と要求
titleSuffix: Azure Machine Learning
description: この攻略ガイドでは、Azure Machine Learning のリソースにおけるさまざまなクォータと、さらに多くのクォータを表示および要求する方法について説明します。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: jmartens
author: nishankgu
ms.author: nigup
ms.date: 05/10/2019
ms.custom: seodec18
ms.openlocfilehash: 2ee819625f772c96ac6c5a771935da17ee05f3aa
ms.sourcegitcommit: e97a0b4ffcb529691942fc75e7de919bc02b06ff
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/15/2019
ms.locfileid: "71002684"
---
# <a name="manage-and-request-quotas-for-azure-resources"></a>Azure リソースのクォータの管理と要求

他の Azure サービスと同様に、Azure Machine Learning に関連付けられている特定のリソースにも制限があります。 これらの制限は、作成できるワークスペースの数の上限から、モデルのトレーニングや推論/スコアリングに使用される実際の基盤となるコンピューティングに対する制限まで、多岐にわたります。 

この記事では、サブスクリプションのさまざまな Azure リソースに対して事前に構成された制限に関する詳細について説明します。また、リソースの種類ごとにクォータの拡張を要求する便利なリンクも含まれています。 これらの制限は、不正による予算超過を防ぎ、Azure の容量の制約を尊重するために設けられています。

実稼働環境のワークロードに対して Azure Machine Learning リソースの設計やスケールアップを行う際は、これらのクォータに留意してください。 たとえば、クラスターのノード数がターゲットとして指定した数に満たない場合は、ご使用のサブスクリプションの Azure Machine Learning コンピューティング コアの制限に達している可能性があります。 制限を引き上げるまたは既定の制限を超えるクォータが必要な場合は、オンライン カスタマー サポートに申請 (無料) してください。 Azure の容量の制約があるため、次の表に示されている上限の値を超える制限の引き上げはできません。 上限列が存在しない場合、記載されているリソースに調整可能な制限がないことを意味します。

## <a name="special-considerations"></a>特別な考慮事項

+ クォータは、容量の保証ではなく、クレジット制限です。 大規模な容量が必要な場合は、Azure サポートにお問い合わせください。

+ クォータは、Azure Machine Learning を含め、サブスクリプション内のすべてのサービスで共有されます。 唯一の例外は、コア コンピューティング クォータから切り離されたクォータを備えた Azure Machine Learning コンピューティングです。 容量のニーズを評価する際には、すべてのサービスでのクォータ使用率を計算してください。

+ 既定の制限は、プラン カテゴリの種類 (無料試用版や従量課金制など) とシリーズ (Dv2、F、G など) によって異なります。

## <a name="default-resource-quotas"></a>既定のリソース クォータ

ここでは、Azure サブスクリプション内のさまざまなリソースの種類ごとのクォータ制限の内訳を示します。

> [!Important]
> 制限は変更されることがあります。 最新の制限は、すべての Azure のサービス レベルのクォータ [ドキュメント](https://docs.microsoft.com/azure/azure-subscription-service-limits/)で見つかります。

### <a name="virtual-machines"></a>仮想マシン
サービス全体で、またはスタンドアロンの方法で、Azure サブスクリプションでプロビジョニングできる仮想マシンの数には制限があります。 この制限はリージョン レベルで、コアの合計数とファミリ ベースの両方に適用されます。

仮想マシンのコアには、リージョンの合計の制限だけでなく、別に適用されるリージョンのサイズ シリーズ (Dv2、F など) ごとの制限もあることに注意してください。 たとえば、米国東部で VM のコア上限が 30、A シリーズのコア上限が 30、D シリーズのコア上限が 30 のサブスクリプションがあるとします。 このサブスクリプションでは、30 の A1 VM、30 の D1 VM、または合計コア数が 30 を超えないこの 2 つの組み合わせ (例: 10 の A1 VM と 20 の D1 VM) のデプロイが許可されます。

[!INCLUDE [azure-subscription-limits-azure-resource-manager](../../../includes/azure-subscription-limits-azure-resource-manager.md)]

クォータ制限の詳細および最新のリストについては、[こちら](https://docs.microsoft.com/azure/azure-subscription-service-limits)の Azure 全体のクォータの記事を確認してください。

### <a name="azure-machine-learning-compute"></a>Azure Machine Learning コンピューティング
Azure Machine Learning コンピューティングでは、サブスクリプションのリージョンごとに許可されるコアの数と一意のコンピューティング リソースの数の両方に対して、既定のクォータ制限があります。 このクォータは上記の VM コア クォータとは別で、コアの制限は 2 つのリソースの種類間で現在共有されていません。

使用可能なリソース:
+ リージョンあたりの専用コアには、サブスクリプション オファーの種類に応じて、24 から 300 の既定の制限があります。  サブスクリプションあたりの専用コアの数は増やすことができます。 このオプション値を増やす場合は、Azure サポートにお問い合わせください。

+ リージョンあたりの低優先度のコアには、サブスクリプション オファーの種類に応じて、24 から 300 の既定の制限があります。  サブスクリプションあたりの優先順位の低いコアの数は増やすことができます。 このオプション値を増やす場合は、Azure サポートにお問い合わせください。

+ リージョンごとのクラスターの既定の制限は 100 で、上限は 200 です。 この制限を超えた引き上げを要求する場合は、Azure サポートにお問い合わせください。

+ 以下に示す**その他の厳密な制限**は、一度達すると超えることはできません。

| **リソース** | **上限** |
| --- | --- |
| リソース グループあたりの最大のワークスペース | 800 |
| 1 つの Azure Machine Learning コンピューティング (AmlCompute) リソース内の最大ノード数 | 100 ノード |
| ノードごとの最大 GPU MPI 処理 | 1-4 |
| ノードごとの最大の GPU ワークスペース | 1-4 |
| ジョブの最長有効期間 | 90 日<sup>1</sup> |
| 低優先度のノードでのジョブの最長有効期間 | 1 日<sup>2</sup> |
| ノードごとの最大パラメーター サーバー | 1 |

<sup>1</sup> 最長有効期間とは、実行開始から終了までの時間のことです。 完了した実行は、無期限に保持されます。最長有効期間内に完了しなかった実行のデータにはアクセスできません。
<sup>2</sup> 低優先度のノード上のジョブは、容量の制約があるときはいつでも横取りできます。 ジョブにチェックポイントを実装することをお勧めします。

### <a name="azure-machine-learning-pipelines"></a>Azure Machine Learning パイプライン
Azure Machine Learning パイプラインには、パイプラインのステップ数にクォータ制限と、サブスクリプション内のリージョンごとの発行済みのパイプラインのスケジュールに基づく実行数に制限があります。
- パイプラインで許可されるステップの最大数は 30,000 です
- 1 か月のサブスクリプションごとに発行されるパイプラインのブログでトリガされるスケジュールで、スケジュールに基づく実行と BLOB プルの合計の最大数は 100,000 です

> [!NOTE]
> この制限を引き上げるには、[Microsoft サポート](https://azure.microsoft.com/support/options/)にお問い合わせください。

### <a name="container-instances"></a>コンテナー インスタンス

特定の期間内 (時間で範囲指定) またはサブスクリプション全体で起動できるコンテナー インスタンスの数にも制限があります。

[!INCLUDE [container-instances-limits](../../../includes/container-instances-limits.md)]

クォータ制限の詳細および最新のリストについては、[こちら](https://docs.microsoft.com/azure/azure-subscription-service-limits#container-instances-limits)の Azure 全体のクォータの記事を確認してください。

### <a name="storage"></a>Storage
リージョンあたりおよび特定のサブスクリプションでもストレージ アカウントの数に制限があります。 既定の制限は 200 で、Standard Storage アカウントと Premium Storage アカウントの両方が含まれます。 指定したリージョンで 200 個を超えるストレージ アカウントが必要な場合は、[Azure サポート](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest/)からリクエストを送信してください。 Azure Storage チームがビジネス ケースを確認します。承認された場合、指定したリージョンに対して最大 250 個のストレージ アカウントが与えられます。


## <a name="find-your-quotas"></a>クォータの検索

Azure portal を使用すると、仮想マシン、ストレージ、ネットワークなどのさまざまなリソースのクォータを簡単に表示できます。

1. 左側のウィンドウで、 **[すべてのサービス]** を選択し、一般カテゴリの下で **[サブスクリプション]** を選択します。

1. サブスクリプションの一覧から、検索するクォータのサブスクリプションを選択します。

   特に Azure Machine Learning コンピューティングのクォータの表示については、**1 つの注意点があります**。 前述のように、サブスクリプションではクォータはコンピューティング クォータとは切り離されています。

1. 左側のウィンドウで、 **[Machine Learning サービス]** を選択して、表示された一覧からいずれかのワークスペースを選択します。

1. 次のブレードの **[Support + troubleshooting]\(サポート + トラブルシューティング\) セクション**で、 **[Usage + quotas]\(使用量 + クォータ\)** を選択して、現在のクォータ制限と使用状況を表示します。

1. クォータ制限を表示するサブスクリプションを選択します。 必ずフィルター処理を行い、対象とするリージョンに絞ります。


## <a name="request-quota-increases"></a>クォータの増加を要求

制限を引き上げるまたは既定の制限を超えるクォータが必要な場合は、[オンライン カスタマー サポートに申請](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest/) (無料) してください。

表に示されている上限値を超える制限の引き上げはできません。 上限が存在しない場合、記載されているリソースに調整可能な制限がないことを意味します。 クォータの増加プロセスについては、[こちらの](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-quota-errors)記事で詳しく説明されています。

クォータの増加を要求する場合は、クォータの増加を要求するサービスを選択する必要があります。これには、Machine Learning サービスのクォータ、コンテナー インスタンス、またはストレージ クォータなどのサービスを選択できます。 Azure Machine Learning コンピューティングに加えて、上記の手順に従ってクォータを表示している間に、簡単に **[クォータの要求]** ボタンをクリックすることも可能です。

> [!NOTE]
> [無料試用版サブスクリプション](https://azure.microsoft.com/offers/ms-azr-0044p)は、制限およびクォータ引き上げの適用対象外です。 [無料試用版](https://azure.microsoft.com/offers/ms-azr-0044p)をお持ちの場合は、[従量課金制](https://azure.microsoft.com/offers/ms-azr-0003p/)サブスクリプションにアップグレードしてください。 詳細については、「[Azure 無料試用版を従量課金制にアップグレード](../../billing/billing-upgrade-azure-subscription.md)」と「[Azure 無料アカウント FAQ](https://azure.microsoft.com/free/free-account-faq)」を参照してください。
