---
title: Azure の請求およびコスト管理の自動化シナリオ
description: 一般的な請求およびコスト管理のシナリオがどのようにさまざまな API にマッピングされているかについて学習します。
author: bandersmsft
ms.reviewer: adwise
tags: billing
ms.service: cost-management-billing
ms.subservice: common
ms.topic: reference
ms.date: 01/26/2021
ms.author: banders
ms.openlocfilehash: 12c13b8a65296fb0ee74e0ee0449b604facf2f48
ms.sourcegitcommit: d1e56036f3ecb79bfbdb2d6a84e6932ee6a0830e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/29/2021
ms.locfileid: "99051263"
---
# <a name="automation-scenarios-for-billing-and-cost-management"></a>請求およびコスト管理の自動化シナリオ

この記事では、Azure の請求とコスト管理の一般的なシナリオを示します。 これらのシナリオは、使用できる API に対応しています。 シナリオと API の対応ごとに、API および API で提供される機能の概要を参照できます。

## <a name="common-scenarios"></a>一般的なシナリオ

請求およびコスト管理 API を複数のシナリオで使用して、コストと使用状況に関する質問に答えることができます。 一般的なシナリオの概要を以下に示します。

- **請求書の調整**:Microsoft からの請求金額が正しいか。  請求書の内容はどうなっているか。自分で料金を計算できるか。

- **クロス請求**:請求金額がわかったところで、組織の誰がその料金を支払う必要があるか。

- **コストの最適化**:請求金額はわかった。 どうすれば、Azure に関する支出から利益を得ることができるか。

- **コスト管理**:Azure に関する支出と使用状況を時間の経過に沿って確認したい。 どのような傾向があるか。 どうすればもっとよくなるか。

- **1 か月間の Azure の支出**:今月の当日までの支出はどれくらいか。 Azure に関する支出と使用状況に何か変更を加える必要があるか。 Azure を最も消費しているのは月内のいつか。

- **アラート**:リソース ベースの消費量または通貨ベースのアラートの設定方法。

## <a name="scenario-to-api-mapping"></a>シナリオと API の対応

|         API        | 請求書の調整    | クロス請求    | コストの最適化    | コストの追跡    | 月中支出    | 警告    |
|:---------------------------:|:-------------------------:|:----------------:|:--------------------:|:----------------:|:------------------:|:---------:|
| Budgets                     |                           |                  |           X          |                  |                    |     X     |
| Marketplace Charges                |             X             |         X        |           X          |         X        |          X         |     X     |
| Price Sheet                 |             X             |         X        |           X          |         X        |          X         |           |
| Reservation Recommendations |                           |                  |           X          |                  |                    |           |
| Reservation Details         |                           |                  |           X          |         X        |                    |           |
| Reservation Summaries       |                           |                  |           X          |         X        |                    |           |
| Usage Details               |             X             |         X        |           X          |         X        |          X         |     X     |
| Billing Periods             |             X             |         X        |           X          |         X        |                    |           |
| Invoices                    |             X             |         X        |           X          |         X        |                    |           |
| Azure Retail Prices                    |             X             |                  |           X          |         X        |                    |           |


> [!NOTE]
> シナリオと API の対応には、Enterprise Consumption API は含まれていません。 可能であれば、新しい開発シナリオに対して一般的な Consumption API を使用します。

## <a name="api-summaries"></a>API の概要

### <a name="consumption"></a>従量課金
Web Direct および Enterprise のお客様は、特に明記されていない限り、以下のすべての API を使用できます。

-    [Budgets API](/rest/api/consumption/budgets) (*Enterprise のお客様のみ*):リソース、リソース グループ、または課金測定のコストまたは使用予算を作成します。 予算を作成した場合は、定義されている予算しきい値を超えたときに通知するアラートを構成できます。 また、予算額に達したときに実行するアクションも構成できます。

-    [Marketplace Charges API](/rest/api/consumption/marketplaces):すべての Azure Marketplace リソース (Azure パートナーのオファリング) の料金と使用状況のデータが取得されます。 このデータを使用すると、すべての Marketplace リソースのコストを合算したり、特定のリソースのコスト/使用状況を調査したりすることができます。

-    [Price Sheet API](/rest/api/consumption/pricesheet) (*Enterprise のお客様のみ*):すべての測定のカスタム価格が取得されます。 企業は、このデータを使用状況の詳細とマーケットプレースの使用状況情報の情報と共に使用して、使用状況およびマーケットプレース データを使用してコストの計算を行うことができます。

-    [Reservation Recommendations API](/rest/api/consumption/reservationrecommendations):予約 VM インスタンスの購入に関する推奨事項が取得されます。 推奨事項は、予想されるコスト削減や購入金額を分析するのに役立ちます。 詳細については、「[APIs for Azure reservation automation](../reservations/reservation-apis.md)」(Azure の予約自動化の API) を参照してください。

-    [Reservation Details API](/rest/api/consumption/reservationsdetails):予約された消費と使用された量の比較など、以前に購入した VM 予約に関する情報が表示されます。 データは、VM 単位の詳細度で表示することができます。 詳細については、「[APIs for Azure reservation automation](../reservations/reservation-apis.md)」(Azure の予約自動化の API) を参照してください。

-    [Reservation Summaries API](/rest/api/consumption/reservationssummaries):集計での予約された消費と使用された量の比較など、組織が購入した VM 予約に関する集計情報が表示されます。 詳細については、「[APIs for Azure reservation automation](../reservations/reservation-apis.md)」(Azure の予約自動化の API) を参照してください。

-    [Usage Details API](/rest/api/consumption/usagedetails):Microsoft からすべての Azure リソースの料金と使用状況の情報が取得されます。 情報は、現在 1 日あたり測定あたり 1 回発行される使用状況詳細レコード形式で提供されます。 その情報を使用すると、すべてのリソースのコストを合算したり、特定のリソースのコスト/使用状況を調査したりすることができます。

-    [Azure Retail Prices](/rest/api/cost-management/retail-prices/azure-retail-prices): 従量課金制価格を含む測定レートが取得されます。 その後、返された情報をリソース使用状況情報と共に使用して、予想される請求書を手動で計算することができます。

### <a name="billing"></a>課金
-    [Billing Periods API](/rest/api/billing/enterprise/billing-enterprise-api-billing-periods):分析する請求期間を、その期間の請求書 ID と共に決定します。 Invoices API で請求書 ID を使用できます。

-    [Invoices API](/rest/api/billing/2019-10-01-preview/invoices):ある請求期間に対する PDF 形式の請求書のダウンロード URL が取得されます。

### <a name="enterprise-consumption"></a>Enterprise consumption
以下の API は Enterprise 専用です。

-    [Balance Summary API](/rest/api/billing/enterprise/billing-enterprise-api-balance-summary):残高、新規購入、Azure Marketplace サービス料金、調整、および超過料金に関する情報の月別サマリーが取得されます。 この情報は、現在の請求期間または過去の任意の期間について取得できます。 企業はこのデータを使用して、手動で計算された集計料金と比較することができます。 この API では、リソースに固有の情報またはコストの集計ビューは提供されません。

-    [Usage Details API](/rest/api/billing/enterprise/billing-enterprise-api-usage-detail):当月、特定の請求期間、またはカスタム日付期間の (Microsoft オファリングの) Azure 使用状況に関する情報が取得されます。 企業では、このデータを使用して、料金と消費に基づいて請求書を手動で計算できます。 また、部門/組織の情報を使用して、組織全体にわたってコストを計算することもできます。 データは、使用状況/コストのリソースに固有のビューを提供します。

-    [Marketplace Store Charge API](/rest/api/billing/enterprise/billing-enterprise-api-marketplace-storecharge):当月、特定の請求期間、またはカスタム日付期間の (パートナー オファリングの) Azure 使用状況に関する情報が取得されます。 企業では、このデータを使用して、料金と消費に基づいて請求書を手動で計算できます。 また、部門/組織の情報を使用して、組織全体にわたってコストを計算することもできます。 この API では、使用状況/コストのリソースに固有のビューが提供されます。

-    [Price Sheet API](/rest/api/billing/enterprise/billing-enterprise-api-pricesheet):特定の加入契約と請求期間に対する各測定に適用可能な課金率が取得されます。 この率情報を使用状況の詳細およびマーケットプレースの使用状況情報と組み合わせて使用して、予測される請求書を手動で計算することができます。

-    [Billing Periods API](/rest/api/billing/enterprise/billing-enterprise-api-billing-periods):請求期間の一覧が取得されます。 また、その請求期間に関連する次の 4 セットの Enterprise API データの API ルートを示すプロパティが提供されます:BalanceSummary、UsageDetails、Marketplace Charges、PriceSheet。

-    [Reserved Instance Recommendations API](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-recommendation):7 日、30 日、または 60 日間の仮想マシンの使用状況を調べて、単一および共有購入の推奨事項が取得されます。 この API を使用すると、予想されるコスト削減と推奨される購入額を分析することができます。 詳細については、「[APIs for Azure reservation automation](../reservations/reservation-apis.md)」(Azure の予約自動化の API) を参照してください。

## <a name="frequently-asked-questions"></a>よく寄せられる質問

### <a name="whats-the-difference-between-the-enterprise-reporting-apis-and-the-consumption-apis-when-should-i-use-each"></a>Enterprise Reporting API と Consumption API の違いは何ですか? それぞれはいつ使用すればよいですか?
これらの API には同様の機能セットがあり、同じ領域の課金とコスト管理に関する問題に対応できます。 ただし、対象ユーザーが異なります。

- Enterprise Reporting API は、交渉された Azure 前払い (旧称: 年額コミットメント) とカスタムの価格設定にアクセス可能な Enterprise Agreement を Microsoft と締結したお客様のみが利用できます。 使用するには、[Enterprise Portal](https://ea.azure.com) で取得できるキーが必要です。 この API の説明については、「[企業ユーザー向けの Reporting API の概要](enterprise-api.md)」を参照してください。

- Consumption API は、いくつかの例外はありますが、すべてのユーザーが利用できます。 詳細については、「[Azure Consumption API の概要](consumption-api-overview.md)」と [Azure Consumption API リファレンス](/rest/api/consumption/)のページを参照してください。 用意されている API を最新の開発シナリオに対するソリューションとして使用することをお勧めします。

### <a name="whats-the-difference-between-the-invoice-api-and-the-usage-details-api"></a>Invoice API と Usage Details API の違いは何ですか?
これらの API は、同じデータの異なるビューを提供します。

- [Invoice API](/rest/api/billing/2019-10-01-preview/invoices) は、Web Direct のお客様専用です。 各測定の種類の集計料金に基づいた請求の毎月のまとめが提供されます。

- [Usage Details API](/rest/api/consumption/usagedetails) では、毎日の使用状況/コスト レコードの詳細が提供されます。 Enterprise と Web Direct 両方のお客様が使用できます。

### <a name="whats-the-difference-between-the-price-sheet-api-and-the-ratecard-api"></a>Price Sheet API と RateCard API の違いは何ですか?
これらの API は、同様のデータ セットを提供しますが、対象ユーザーが異なります。

- [Price Sheet API](/rest/api/consumption/pricesheet) は、1 社のお客様向けの交渉されたカスタムの価格を提供します。

- [Azure Retail Prices API](/rest/api/cost-management/retail-prices/azure-retail-prices) では、Web Direct のお客様に適用される公開従量課金制価格が提供されます。

## <a name="next-steps"></a>次のステップ

- REST API を使用してすべての Azure サービスの価格を取得する方法については、「[Azure 小売価格の概要](/rest/api/cost-management/retail-prices/azure-retail-prices)」を参照してください。

- 詳細な毎日の使用状況ファイルおよび Azure portal のコスト管理レポートと、請求書を比較する方法については、「[Microsoft Azure の課金内容の確認](../understand/review-individual-bill.md)」をご覧ください。

- ご質問がある場合やヘルプが必要な場合は、[サポート要求を作成](https://go.microsoft.com/fwlink/?linkid=2083458)してください。