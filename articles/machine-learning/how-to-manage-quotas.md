---
title: リソースとクォータの管理
titleSuffix: Azure Machine Learning
description: Azure Machine Learning のリソースにおけるクォータと、さらに多くのクォータを要求する方法について説明します。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.reviewer: jmartens
author: nishankgu
ms.author: nigup
ms.date: 05/08/2020
ms.topic: conceptual
ms.custom: troubleshooting,contperfq4
ms.openlocfilehash: a9ae3d2789758d03405fb5be82181c799d1ea692
ms.sourcegitcommit: a2a7746c858eec0f7e93b50a1758a6278504977e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/12/2020
ms.locfileid: "88141127"
---
# <a name="manage--increase-quotas-for-resources-with-azure-machine-learning"></a>Azure Machine Learning を使用してリソースのクォータを管理し増やす
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

この記事では、[Azure Machine Learning](overview-what-is-azure-ml.md) サブスクリプションに対して事前構成された Azure リソースの制限と、管理できるクォータについて説明します。 これらの制限は、不正による予算超過を防ぎ、Azure の容量の制約を尊重するために設けられています。 

他の Azure サービスと同様に、Azure Machine Learning に関連付けられている特定のリソースにも制限があります。 これらの制限は、[ワークスペース](concept-workspace.md)の数の上限から、モデルのトレーニングや推論/スコアリングに使用される実際の基盤となるコンピューティングに対する制限まで、多岐にわたります。 

実稼働環境のワークロードに対して Azure Machine Learning リソースの設計やスケールアップを行う際は、これらの制限を考慮してください。 たとえば、クラスターのノード数がターゲットの数に満たない場合は、ご使用のサブスクリプションの Azure Machine Learning コンピューティング コアの制限に達している可能性があります。 制限を引き上げるまたは既定の制限を超えるクォータが必要な場合は、オンライン カスタマー サポートに申請 (無料) してください。 Azure の容量の制約があるため、次の表に示されている上限の値を超える制限の引き上げはできません。 上限列が存在しない場合、記載されているリソースに調整可能な制限がないことを意味します。


クォータの管理に加えて、[Azure Machine Learning のコストを計画して管理する](concept-plan-manage-cost.md)方法についても説明します。

## <a name="special-considerations"></a>特別な考慮事項

+ クォータは、容量の保証ではなく、クレジット制限です。 大規模な容量が必要な場合は、Azure サポートにお問い合わせください。 また、[クォータを増やす](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-quota-errors)こともできます。

+ クォータは、Azure Machine Learning を含め、サブスクリプション内のすべてのサービスで共有されます。 唯一の例外は、コア コンピューティング クォータから切り離されたクォータを備えた Azure Machine Learning コンピューティングです。 容量のニーズを評価する際には、すべてのサービスでのクォータ使用率を計算してください。

+ 既定の制限は、プラン カテゴリの種類 (無料試用版や従量課金制など) と VM シリーズ (Dv2、F、G など) によって異なります。

## <a name="default-resource-quotas"></a>既定のリソース クォータ

ここでは、Azure サブスクリプション内のさまざまなリソースの種類ごとのクォータ制限の内訳を示します。

> [!IMPORTANT]
> 制限は変更されることがあります。 最新の制限は、すべての Azure のサービス レベルのクォータ [ドキュメント](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits/)で見つかります。

### <a name="virtual-machines"></a>仮想マシン
Azure サブスクリプションごとに、サービス全体またはスタンドアロンの仮想マシンの数には制限があります。 仮想マシンのコアには、リージョンの合計の制限とリージョンのサイズ シリーズ (Dv2、F など) ごとの制限があり、どちらも個別に適用されます。 たとえば、米国東部で VM のコア上限が 30、A シリーズのコア上限が 30、D シリーズのコア上限が 30 のサブスクリプションがあるとします。 このサブスクリプションでは、30 の A1 VM、30 の D1 VM、または合計コア数が 30 を超えないこの 2 つの組み合わせ (例: 10 の A1 VM と 20 の D1 VM) のデプロイが許可されます。

[!INCLUDE [azure-subscription-limits-azure-resource-manager](../../includes/azure-subscription-limits-azure-resource-manager.md)]

### <a name="azure-machine-learning-compute"></a>Azure Machine Learning コンピューティング
[Azure Machine Learning コンピューティング](concept-compute-target.md#azure-machine-learning-compute-managed)では、サブスクリプションのリージョンごとに許可されるコアの数と一意のコンピューティング リソースの数の両方に対して、既定のクォータ制限があります。 AmlCompute は、hosted-on-behalf-of モデルでリソースをデプロイするマネージド サービスであるため、このクォータは上記の VM コア クォータとは別で、コアの制限は 2 つのリソースの種類間で共有されません。

使用可能なリソース:
+ リージョンあたりの専用コアには、サブスクリプション オファーの種類に応じて、24 から 300 の既定の制限があり、EA および CSP オファーの種類の既定値は大きくなります。  サブスクリプションあたりの専用コアの数は増やすことができ、VM ファミリごとに異なります。 NCv2、NCv3、ND シリーズなど、特定の特殊な VM ファミリは、ゼロ コアの既定から開始されます。 増加オプションを検討するには、クォータ要求を発行して、Azure サポートに問い合わせてください。

+ リージョンあたりの優先順位の低いコアには、サブスクリプション オファーの種類に応じて、100 から 3000 の既定の制限があり、EA および CSP オファーの種類の既定値は大きくなります。 サブスクリプションあたりの優先順位の低いコアの数は増やすことができ、VM ファミリ全体で 1 つの値になります。 このオプション値を増やす場合は、Azure サポートにお問い合わせください。

+ リージョンあたりのクラスターの既定の制限は 200 です。 これらは、トレーニング クラスターとコンピューティング インスタンス (クォータ目的で単一ノード クラスターとみなされる) 間で共有されます。 この制限を超えた引き上げを要求する場合は、Azure サポートにお問い合わせください。

+ 一度達すると超えることができないその他の厳密な制限があります。

| **リソース** | **上限** |
| --- | --- |
| リソース グループあたりの最大のワークスペース | 800 |
| 1 つの Azure Machine Learning コンピューティング (AmlCompute) リソース内の最大ノード数 | 100 ノード |
| ノードごとの最大 GPU MPI 処理 | 1-4 |
| ノードごとの最大の GPU ワークスペース | 1-4 |
| ジョブの最長有効期間 | 21 日<sup>1</sup> |
| 優先順位の低いノードでのジョブの最長有効期間 | 7 日<sup>2</sup> |
| ノードごとの最大パラメーター サーバー | 1 |

<sup>1</sup> 最長有効期間とは、実行開始から終了までの時間のことです。 完了した実行は、無期限に保持されます。最長有効期間内に完了しなかった実行のデータにはアクセスできません。
<sup>2</sup> 優先順位の低いノード上のジョブは、容量の制約があるときはいつでも横取りできます。 ジョブにチェックポイントを実装することをお勧めします。

### <a name="azure-machine-learning-pipelines"></a>Azure Machine Learning パイプライン
[Azure Machine Learning パイプライン](concept-ml-pipelines.md)には、パイプラインのステップ数にクォータ制限と、サブスクリプション内のリージョンごとの発行済みのパイプラインのスケジュールに基づく実行数に制限があります。
- パイプラインで許可されるステップの最大数は 30,000 です
- 1 か月のサブスクリプションごとに発行されるパイプラインのブログでトリガされるスケジュールで、スケジュールに基づく実行と BLOB プルの合計の最大数は 100,000 です

### <a name="container-instances"></a>コンテナー インスタンス

特定の期間内 (時間で範囲指定) またはサブスクリプション全体で起動できるコンテナー インスタンスの数にも制限があります。
制限については、「[Container Instances の制限](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits#container-instances-limits)」を参照してください。

### <a name="storage"></a>ストレージ
リージョンあたりおよび特定のサブスクリプションでもストレージ アカウントの数に制限があります。 既定の制限は 250 で、Standard Storage アカウントと Premium Storage アカウントの両方が含まれます。 指定したリージョンで 250 個を超えるストレージ アカウントが必要な場合は、[Azure サポート](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest/)からリクエストを送信してください。 Azure Storage チームがビジネス ケースを確認します。承認された場合、指定したリージョンに対して最大 250 個のストレージ アカウントが与えられます。


## <a name="workspace-level-quota"></a>ワークスペース レベル クォータ

さまざまな[ワークスペース](concept-workspace.md)間で Azure Machine Learning コンピューティング ターゲット (Amlcompute) のリソース割り当ての管理を強化するため、(VM ファミリによって) サブスクリプション レベルのクォータを分散し、ワークスペース レベルでそれらを構成できる機能を導入しました。 既定の動作は、すべてのワークスペースが VM ファミリのサブスクリプション レベル クォータと同じクォータを持つことです。 ただし、ワークスペースの数が増え、優先順位の異なるワークロードが同じリソースを共有し始めると、ユーザーは容量をより適切に共有し、リソースの競合の問題を回避する方法が必要になります。 Azure Machine Learning では、ユーザーが各ワークスペースで特定の VM ファミリの最大クォータを設定できるようにすることで、マネージド コンピューティング オファリングを含むソリューションを提供します。 これは、ワークスペース間で容量を分散することに似ています。また、ユーザーは、最大使用率に達するまで過度に割り当てることもできます。 

ワークスペース レベルでクォータを設定するには、サブスクリプションの任意のワークスペースに移動し、左側のウィンドウの **[Usages + quotas]\(使用量 + クォータ\)** をクリックします。 次に **[クォータの構成]** タブを選択してクォータを表示し、任意の VM ファミリを展開して、その VM ファミリの下に一覧表示されている任意のワークスペースにクォータ制限を設定します。 負の値またはサブスクリプション レベルのクォータよりも大きい値は設定できないことに注意してください。 さらに、お気づきのとおりに、既定ですべてのワークスペースにサブスクリプション クォータ全体が割り当てられ、割り当てられたクォータを完全利用できます。

[![Azure Machine Learning ワークスペース レベルのクォータ](./media/how-to-manage-quotas/azure-machine-learning-workspace-quota.png)](./media/how-to-manage-quotas/azure-machine-learning-workspace-quota.png)


> [!NOTE]
> これは Enterprise Edition のみの機能です。 サブスクリプションに [Basic Edition と Enterprise Edition](overview-what-is-azure-ml.md#sku) の両方のワークスペースがある場合、これを使用して、Enterprise ワークスペースに対してのみクォータを設定できます。 Basic ワークスペースでは、既定の動作であるサブスクリプション レベルのクォータが引き続き指定されます。
>
> ワークスペース レベルでクォータを設定するには、サブスクリプション レベルのアクセス許可が必要です。 これは、個々のワークスペース所有者がクォータを編集または増加して、別のワークスペース用に確保されたリソースに侵害し始めることがないように、適用されています。 そのため、サブスクリプション管理者が、ワークスペース間でこれらのクォータを割り当てて、分散するのに最も適しています。



## <a name="view-your-usage-and-quotas"></a>使用量とクォータの表示

サブスクリプションの Azure Machine Learning コンピューティング クォータは、他の Azure リソース クォータとは別に管理されます。 このクォータを表示するには、Machine Learning Services にドリルダウンする必要があります。  

1. 左側のウィンドウで、 **[Machine Learning service]** を選択して、表示された一覧からいずれかのワークスペースを選択します。

2. 次のブレードの **[Support + troubleshooting]\(サポート + トラブルシューティング\) セクション**で、 **[Usage + quotas]\(使用量 + クォータ\)** を選択して、現在のクォータ制限と使用状況を表示します。

3. クォータ制限を表示するサブスクリプションを選択します。 必ずフィルター処理を行い、対象とするリージョンに絞ります。

4. サブスクリプション レベル ビューとワークスペース レベル ビューを切り替えられるようになりました。
    + **サブスクリプション ビュー:** これを使用して、VM ファミリ別にコア クォータの使用状況を表示し、ワークスペース別にそれを展開し、さらに実際のクラスター名別にそれを展開することができます。 このビューは、特定の VM ファミリのコア使用状況の詳細をすばやく確認して、ワークスペース別、さらにそれらの各ワークスペースの基になるクラスター別に分散を確認するのに最適です。 このビューの一般的な規則は (使用量/クォータ) であり、使用量はスケールアップされた現在のコア数であり、クォータはリソースを拡大できる最大論理コア数です。 各**ワークスペース**で、クォータは、特定の VM ファミリに対して拡大できる最大コア数を示すワークスペース レベルのクォータ (前述のとおり) になります。 同様に**クラスター**の場合、クォータは、max_nodes プロパティによって定義された、クラスターが拡大できる最大ノード数に対応する実際のコアです。
    
    + **ワークスペース ビュー:** これを使用して、ワークスペース別にコア クォータの使用状況を表示し、VM ファミリ別にそれを展開し、さらに実際のクラスター名別にそれを展開することができます。 このビューは、特定のワークスペースのコア使用状況の詳細をすばやく確認して、VM ファミリ別、さらにそれらのファミリの基になるクラスター別に分散を確認するのに最適です。

Azure portal を使用すると、仮想マシン、ストレージ、ネットワークなどのさまざまな他の Azure リソースのクォータを簡単に表示できます。

1. 左側のウィンドウで、 **[すべてのサービス]** を選択し、一般カテゴリの下で **[サブスクリプション]** を選択します。

2. サブスクリプションの一覧から、検索するクォータのサブスクリプションを選択します。

3. 現在のクォータ制限と使用状況を表示するには、 **[使用量 + クォータ]** を選択します。 フィルターを使用して、プロバイダーと場所を選択します。 

## <a name="request-quota-increases"></a>クォータの増加を要求

制限を引き上げるまたは既定の制限を超えるクォータが必要な場合は、[オンライン カスタマー サポートに申請](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest/) (無料) してください。

表に示されている上限値を超える制限の引き上げはできません。 上限が存在しない場合、記載されているリソースに調整可能な制限がないことを意味します。 [クォータを増やす方法については、ステップ バイ ステップの手順をご覧ください](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-quota-errors)。

クォータの増加を要求する場合は、クォータの増加を要求するサービスを選択する必要があります。これには、Machine Learning サービスのクォータ、コンテナー インスタンス、またはストレージ クォータなどのサービスを選択できます。 Azure Machine Learning コンピューティングに加えて、上記の手順に従ってクォータを表示している間に、 **[クォータの要求]** ボタンをクリックすることも可能です。

> [!NOTE]
> [無料試用版サブスクリプション](https://azure.microsoft.com/offers/ms-azr-0044p)は、制限およびクォータ引き上げの適用対象外です。 [無料試用版](https://azure.microsoft.com/offers/ms-azr-0044p)をお持ちの場合は、[従量課金制](https://azure.microsoft.com/offers/ms-azr-0003p/)サブスクリプションにアップグレードしてください。 詳細については、「[Azure 無料試用版を従量課金制にアップグレード](../billing/billing-upgrade-azure-subscription.md)」と「[Azure 無料アカウント FAQ](https://azure.microsoft.com/free/free-account-faq)」を参照してください。

## <a name="next-steps"></a>次のステップ

+ [Azure Machine Learning のコストを計画して管理する](concept-plan-manage-cost.md)
