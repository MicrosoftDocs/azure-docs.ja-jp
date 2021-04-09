---
title: Azure API Management のコストを計画および管理する
description: Azure portal でコスト分析を使用して、Azure API Management のコストを計画および管理する方法を学習します。
author: dlepow
ms.author: apimpm
ms.custom: subject-cost-optimization
ms.service: api-management
ms.topic: how-to
ms.date: 12/15/2020
ms.openlocfilehash: 1ebb89ae318e57f1d4e0708a08019515ca43158d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "99581331"
---
# <a name="plan-and-manage-costs-for-api-management"></a>API Management のコストを計画および管理する

この記事では、Azure API Management のコストを計画および管理する方法について説明します。 コストを見積もるサービスに対してリソースを追加する前に、まず、API Management のコストの計画に役立つ Azure 料金計算ツールを使用します。 API Management リソースの使用を開始したら、Cost Management 機能を使用して、予算の設定とコストの監視を行います。 また、予想コストを確認し、支出の傾向を特定して、対処が必要な領域を特定することもできます。 

API Management のコストは、Azure の請求に含まれる月額コストの一部でしかありません。 この記事では、API Management のコストを計画し、管理する方法について説明しますが、課金はサードパーティのサービスを含め、Azure サブスクリプションで使用されるすべての Azure サービスとリソースに対して行われます。

## <a name="prerequisites"></a>[前提条件]

Cost Management のコスト分析では、ほとんどの種類の Azure アカウントがサポートされますが、すべてではありません。 サポートされているアカウントの種類の完全な一覧については、「[Understand Cost Management data (Cost Management データの概要)](../cost-management-billing/costs/understand-cost-mgt-data.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)」を参照してください。 コスト データを表示するには、少なくとも Azure アカウントの読み取りアクセス許可が必要です。 Azure Cost Management データに対するアクセス権の割り当てについては、[データへのアクセス許可の割り当て](../cost-management-billing/costs/assign-access-acm-data.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)に関するページを参照してください。

## <a name="estimate-costs-before-using-api-management"></a>API Management を使用する前にコストを見積もる

API Management を追加する前に、[Azure 料金計算ツール](https://azure.microsoft.com/pricing/calculator/)を使用してコストを見積もります。 

1. *API Management* を検索するか、 **[統合]**  >  **[API Management]** を選択します。
1. **[表示]** を選択して、API Management サービス インスタンスの既定のコスト見積もりを追加します。

> [!NOTE]
> この例で示されるコストは、デモンストレーションのみを目的としています。 最新の価格情報については、「[API Management の価格](https://azure.microsoft.com/pricing/details/api-management/)」を参照してください。

:::image type="content" source="media/plan-manage-costs/pricing-calculator-developer-tier.png" alt-text="Developer レベルのコストを見積もる":::

* 既定のコスト見積もりは、 **[Developer]** [サービス レベル](api-management-features.md) ([容量ユニット](api-management-capacity.md)は 1) の API Management サービス インスタンスに基づきます。 Developer レベルは、非運用のユース ケースと評価用です。 これは、サービス レベル アグリーメントではサポートされません。

* 追加の容量ユニットまたは別のサービス レベルについてのコストを見積もるには、 **[ユニット]** と **[レベル]** の各ドロップダウンから他のオプションを選択します。

* 機能の可用性とサービス レベルによっては、[セルフホステッド ゲートウェイ](self-hosted-gateway-overview.md)の使用に追加料金が適用される場合があります。

その他の価格および機能の詳細については、以下を参照してください。

* [API Management の価格](https://azure.microsoft.com/pricing/details/api-management/)
* [Azure API Management レベルの機能に基づく比較](api-management-features.md)

### <a name="using-monetary-credit-with-api-management"></a>API Management での年額クレジットの使用

API Management の料金は、Azure 前払い (旧称: 年額コミットメント) を使用して支払うことができます。 ただし、Azure 前払いのクレジットを使用して、サードパーティの製品やサービス (Azure Marketplace からのものを含む) の料金を支払うことはできません。

## <a name="monitor-costs"></a>コストを監視する

API Management で Azure リソースを使用すると、コストが発生します。 Azure リソース使用のユニット コストは、期間 (秒、分、時間、日数) やユニット使用量 (バイト、メガバイトなど) によって異なります。 API Management の使用が開始されるとすぐにコストが発生し、[コスト分析](../cost-management-billing/costs/quick-acm-cost-analysis.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)でコストを確認できます。

コスト分析を使用すると、さまざまな期間について、API Management のコストをグラフや表で表示できます。 たとえば、日単位、現在の月、以前の月、年単位などがあります。 予算や予想コストを基準としてコストを表示することもできます。 時間経過を示す、より長い期間のビューに切り替えると、支出の傾向を特定するのに役立ちます。 超過出費が発生した可能性のある時期を確認できます。 予算を作成したら、それを超えた場所も簡単に確認できます。

> [!NOTE]
> この例で示されるコストは、デモンストレーションのみを目的としています。 コストは、リソースの使用量と現在の価格によって変わります。

API Management のコストをコスト分析で表示するには、次のようにします。

1. [Azure portal](https://azure.microsoft.com) にサインインします。
1. **[コストの管理と請求]** ウィンドウを開き、メニューから **[コスト管理]** を選択してから、**課金スコープ** を選択します。 たとえば、一覧からサブスクリプションを選択します。
1. メニューから **[コスト管理]** を選択し、 **[コスト分析]** を選択します。
1. 既定では、最初のドーナツ グラフにすべてのサービスの月額コストが表示されます。 

    :::image type="content" source="media/plan-manage-costs/api-management-cost-analysis.png" alt-text="サブスクリプションの月額コスト":::

1. API Management など、1 つのサービスのコストを絞り込むには、 **[フィルターの追加]** を選択してから、 **[サービス名]** を選択します。 次に、 **[API Management]** を選択します。

    :::image type="content" source="media/plan-manage-costs/api-management-apim-cost-analysis.png" alt-text="API Management の累積コストを示す例":::

前の例では、サービスの現在のコストが表示されます。 また、Azure リージョン (場所) 別のコストとリソース グループ別の API Management のコストも表示されます。 ここでは、コストを自分で調べることができます。

## <a name="create-budgets"></a>予算を作成する

[予算](../cost-management-billing/costs/tutorial-acm-create-budgets.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)を作成して、コストを管理し、異常な支出や浪費のリスクについて、関係者に自動的に通知する[アラート](../cost-management-billing/costs/cost-mgt-alerts-monitor-usage-spending.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)を作成できます。 アラートは、予算とコストのしきい値と比較した支出に基づきます。 予算とアラートは、Azure サブスクリプションとリソース グループに対して作成されるため、全体的なコスト監視戦略の一環として役立ちます。 

監視の粒度をさらに細かく示す必要がある場合は、Azure の特定のリソースまたはサービスに対するフィルターを使用して予算を作成できます。 フィルターを使用すると、追加のコストがかかる新しいリソースが誤って作成されないようにすることができます。 予算を作成するときのフィルター オプションの詳細については、[グループとフィルターのオプション](../cost-management-billing/costs/group-filter.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)に関する記事を参照してください。

## <a name="export-cost-data"></a>コスト データのエクスポート

また、ストレージ アカウントに[コスト データをエクスポート](../cost-management-billing/costs/tutorial-export-acm-data.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)することもできます。 これは、他のユーザーがコストに関する追加のデータ分析を行う必要がある場合に便利です。 たとえば、財務チームは、Excel や Power BI を使用してデータを分析できます。 日単位、週単位、または月単位のスケジュールでコストをエクスポートし、カスタムの日付範囲を設定することができます。 コスト データのエクスポートは、推奨されるコスト データセット取得方法です。

## <a name="other-ways-to-manage-and-reduce-costs-for-api-management"></a>API Management のコストを管理して削減するその他の方法

### <a name="choose-tier"></a>レベルを選択する

「[Azure API Management レベルの機能に基づく比較](api-management-features.md)」を確認し、シナリオに適したサービス レベルの判断に役立ててください。 さまざまなサービス レベルにおいて、各種のユース ケースに合わせて設計された機能の組み合わせがサポートされており、コストが異なります。 

* **従量課金** サービス レベルでは、固定費が発生しない、軽量のサーバーレス オプションが提供されます。 特定のしきい値を超える、サービスへの API 呼び出しの数に基づいて課金されます。 また、容量は、サービスの負荷に基づいて自動的にスケーリングされます。
* **Developer**、**Basic**、**Standard**、**Premium** の各 API Management レベルでは、月額コストが発生し、評価および運用のワークロードにより高いスループットとより豊富な機能セットが提供されます。 いつでも別のサービス レベルに[アップグレード](upgrade-and-scale.md)できます。

### <a name="scale-using-capacity-units"></a>容量ユニットを使用してスケーリングする

従量課金サービス レベルの場合を除き、API Management では "[*容量ユニット*](api-management-capacity.md)" を追加または削除することでスケーリングがサポートされます。 API Management インスタンスで負荷が増加したときに、より高いサービス レベルにアップグレードするよりも、容量ユニットを追加する方が経済的な場合があります。 ユニットの最大数は、サービス レベルによって異なります。

各容量ユニットには一定の要求の処理能力があり、これはサービス レベルに依存します。 たとえば、Basic レベルのユニットには、1 秒あたり約 1,000 件の要求の推定最大スループットがあります。 

ユニットの追加または削除に従って、容量とコストは比例してスケーリングします。 たとえば、Standard レベルの 2 つのユニットでは、1 秒あたり約 2,000 件の要求の推定スループットが提供されます。 実際のスループットは、要求または応答のサイズ、接続パターン、要求を行うクライアントの数、およびその他の要因によって異なる場合があります。

API Management インスタンスの容量メトリックを[監視](api-management-howto-use-azure-monitor.md)し、より多くの負荷に対応するために API Management インスタンスをスケーリングするか、アップグレードするかの決定に役立ててください。

## <a name="next-steps"></a>次のステップ

- [Azure Cost Management を使用してクラウドへの投資を最適化する](../cost-management-billing/costs/cost-mgt-best-practices.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)方法について説明します。
- [コスト分析](../cost-management-billing/costs/quick-acm-cost-analysis.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)を使用してコストを管理する方法について詳細に説明します。
- [予期しないコストを回避](../cost-management-billing/cost-management-billing-overview.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)する方法について説明します。
- [Cost Management](/learn/paths/control-spending-manage-bills?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) のガイド付き学習コースを受講します。
- API Management の[容量](api-management-capacity.md)について理解します。
- [Azure portal](upgrade-and-scale.md) を使用して API Management のスケーリングとアップグレードの手順を確認し、[自動スケーリング](api-management-howto-autoscale.md)について理解します。
