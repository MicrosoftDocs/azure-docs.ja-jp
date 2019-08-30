---
title: Enterprise Agreement から Microsoft 顧客契約 API に移行する - Azure | Microsoft Docs
description: この記事では、Microsoft Enterprise Agreement (EA) から Microsoft 顧客契約への移行とその結果について説明します。
services: cost-management
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 05/20/2019
ms.topic: conceptual
ms.service: cost-management
manager: micflan
ms.custom: ''
ms.openlocfilehash: 5c2041984ffa2c455ea4d60a756fcb4142219d91
ms.sourcegitcommit: 6d2a147a7e729f05d65ea4735b880c005f62530f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/22/2019
ms.locfileid: "69981456"
---
# <a name="migrate-from-enterprise-agreement-to-microsoft-customer-agreement-apis"></a>Enterprise Agreement から Microsoft 顧客契約 API に移行する

この記事では、Enterprise Agreement (EA) と Microsoft 顧客契約 (MCA) アカウントとの、データ構造、API、およびその他のシステム統合に関する違いについて説明します。 Azure Cost Management では、両方のアカウントの種類に応じた API がサポートされています。 続行する前に、Microsoft 顧客契約の[請求先アカウントの設定](../billing/billing-mca-setup-account.md)に関する記事を参照してください。

既存の EA アカウントを含む組織は、MCA アカウントを設定すると共に、この記事を参照する必要があります。 以前、EA アカウントを更新するには、古い加入契約から新しい加入契約に移行するための最低限の複数の作業が必要でした。 ただし、MCA アカウントへの移行には、追加の作業が必要です。 追加の作業は、基になる課金サブシステムでの変更がすべてのコスト関連 API およびサービス内容に影響するという理由で行います。

## <a name="mca-apis-and-integration"></a>MCA API と統合

MCA API と新しい統合により、以下の操作が可能になります。

- ネイティブ Azure API から API を完全に利用できる。
- 単一の請求先アカウントで複数の請求書を構成する。
- Azure サービスの使用状況、サードパーティ製 Marketplace の使用状況、および Marketplace の購入で組み合わされた API にアクセスする。
- Azure Cost Management を使用して複数の課金プロファイル (加入契約と同じ) にわたってコストを表示する。
- コストを表示し、コストが事前定義されたしきい値を超えたときに通知を受け、および生データをエクスポートするために新しい API にアクセスする。

## <a name="migration-checklist"></a>移行チェックリスト

次の項目は、MCA API への移行に役立ちます。

- 新しい [Microsoft 顧客契約課金アカウント](../billing/billing-mca-overview.md)について詳しく理解します。
- 次のセクションで、使用する API を特定し、どの API が置き換えられているかを調べます。
- [Azure Resource Manager REST API](/rest/api/azure) について詳しく理解します。を習熟する。
- まだ Azure Resource Manager API を使用していない場合は、[クライアント アプリを Azure AD に登録](/rest/api/azure/#register-your-client-application-with-azure-ad)します。
- [Azure AD 認証を使用](/rest/api/azure/#create-the-request)するようにすべてのプログラミング コードを更新します。
- EA API 呼び出しを置き換える MCA API 呼び出しのプログラム コードを更新します。
- 新しいエラー コードを使用するように、エラー処理を更新します。
- その他の必要なアクションについて、Cloudyn や Power BI などの追加統合内容を確認します。

## <a name="ea-apis-replaced-with-mca-apis"></a>MCA API で置き換えられた EA API

EA API は、認証と承認に API キーを使用します。 MCA API は Azure AD 認証を使用します。

| 目的 | EA API | MCA API |
| --- | --- | --- |
| 残高とクレジット | [/balancesummary](/rest/api/billing/enterprise/billing-enterprise-api-balance-summary) | Microsoft.Billing/billingAccounts/billingProfiles/availableBalanceussae |
| 使用状況 (JSON) | [/usagedetails](/rest/api/billing/enterprise/billing-enterprise-api-usage-detail#json-format)[/usagedetailsbycustomdate](/rest/api/billing/enterprise/billing-enterprise-api-usage-detail#json-format) | [Microsoft.Consumption/usageDetails](/rest/api/consumption/usagedetails)<sup>1</sup> |
| 使用状況 (CSV) | [/usagedetails/download](/rest/api/billing/enterprise/billing-enterprise-api-usage-detail#csv-format)[/usagedetails/submit](/rest/api/billing/enterprise/billing-enterprise-api-usage-detail#csv-format) | [Microsoft.Consumption/usageDetails/download](/rest/api/consumption/usagedetails)<sup>1</sup> |
| Marketplace の使用状況 (CSV) | [/marketplacecharges](/rest/api/billing/enterprise/billing-enterprise-api-marketplace-storecharge)[/marketplacechargesbycustomdate](/rest/api/billing/enterprise/billing-enterprise-api-marketplace-storecharge) | [Microsoft.Consumption/usageDetails/download](/rest/api/consumption/usagedetails)<sup>1</sup> |
| Billing periods | [/billingperiods](/rest/api/billing/enterprise/billing-enterprise-api-billing-periods) | Microsoft.Billing/billingAccounts/billingProfiles/invoices |
| Price Sheet | [/pricesheet](/rest/api/billing/enterprise/billing-enterprise-api-pricesheet) | Microsoft.Billing/billingAccounts/billingProfiles/pricesheet/default/download format=json|csv Microsoft.Billing/billingAccounts/…/billingProfiles/…/invoices/… /pricesheet/default/download format=json|csv Microsoft.Billing/billingAccounts/../billingProfiles/../providers/Microsoft.Consumption/pricesheets/download  |
| 予約購入 | [/reservationcharges](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-charges) | Microsoft.Billing/billingAccounts/billingProfiles/transactions |
| Reservation recommendations | [/SharedReservationRecommendations](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-recommendation#request-for-shared-reserved-instance-recommendations)[/](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-recommendation#request-for-single-reserved-instance-recommendations)[SingleReservationRecommendations](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-recommendation#request-for-single-reserved-instance-recommendations) | [Microsoft.Consumption/reservationRecommendations](/rest/api/consumption/reservationrecommendations/list) |
| 予約の使用状況 | [/reservationdetails](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-usage#request-for-reserved-instance-usage-details)[/reservationsummaries](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-usage) | [Microsoft.Consumption/reservationDetails](/rest/api/consumption/reservationsdetails)[Microsoft.Consumption/reservationSummaries](/rest/api/consumption/reservationssummaries) |

<sup>1</sup> Azure サービスとサード パーティ製 Marketplace の使用状況は、[Usage Details API](/rest/api/consumption/usagedetails) で利用できます。

次の API は MCA 請求先アカウントで使用できます。

| 目的 | Microsoft 顧客契約 (MCA) API |
| --- | --- |
| 請求先アカウント<sup>2</sup> | Microsoft.Billing/billingAccounts |
| 課金プロファイル<sup>2</sup> | Microsoft.Billing/billingAccounts/billingProfiles |
| 請求書セクション<sup>2</sup> | Microsoft.Billing/billingAccounts/invoiceSections |
| Invoices | Microsoft.Billing/billingAccounts/billingProfiles/invoices |
| 課金サブスクリプション | {scope}/billingSubscriptions |

<sup>2</sup> API は、Azure portal および API でのコスト管理のエクスペリエンスが動作するスコープであるオブジェクトの一覧を返します。 Azure Cost Management のスコープの詳細については、「[スコープを理解して使用する](understand-work-scopes.md)」を参照してください。

任意の既存の EA API を使用する場合は、MCA 請求先アカウントをサポートするように更新する必要があります。 次の表は、その他の統合変更を示しています。

| 目的 | 古いオファリング | 新しいオファリング |
| --- | --- | --- |
| Cloudyn | [Cloudyn.com](https://www.cloudyn.com) | [Azure Cost Management](https://azure.microsoft.com/services/cost-management/) |
| Power BI | [Microsoft Consumption Insights](/power-bi/desktop-connect-azure-consumption-insights) コンテンツ パックおよびコネクタ | [Microsoft Azure Consumption Insights Power BI アプリ](https://appsource.microsoft.com/product/power-bi/pbi_azureconsumptioninsights.pbi-azure-consumptioninsights?tab=overview)および [Azure Consumption Insights コネクタ](/power-bi/desktop-connect-azure-consumption-insights) |

## <a name="apis-to-get-balance-and-credits"></a>残高とクレジットを取得するための API

[Get Balance Summary](/rest/api/billing/enterprise/billing-enterprise-api-balance-summary) API は次の月次概要を示します。

- Balances
- 新規購入
- Azure Marketplace のサービス料金
- 調整
- サービス超過分の請求金額

すべての Consumption API は、認証と承認に Azure AD を使用するネイティブ Azure API に置き換えられます。 呼び出し元の Azure REST API の詳細については、[REST の作業開始](/rest/api/azure/#create-the-request)に関するページを参照してください。

Get Balance Summary API は、Microsoft.Billing/billingAccounts/billingProfiles/availableBalance API に置き換えられます。

Available Balance API で利用可能な残高を取得するには:

| Method | 要求 URI |
| --- | --- |
| GET | `https://management.azure.com/providers/Microsoft.Billing/billingAccounts/{billingAccountId}/billingProfiles/{billingProfileId}/availableBalances?api-version=2018-11-01-preview` |

## <a name="apis-to-get-cost-and-usage"></a>コストと使用状況を取得する API

次の API を使用して、Azure サービスの使用状況、サードパーティ製 Marketplace の使用状況、およびその他の Marketplace での購入から毎日のコストの内訳を取得します。 以下の個別の API は、Azure サービスとサードパーティ製 Marketplace の使用状況についてマージされました。 古い API は [Microsoft.Consumption/usageDetails](/rest/api/consumption/usagedetails) API に置き換えられます。 Marketplace での購入を追加します。これは以前には、現在までの残高サマリーにのみ表示されていました。

- [使用状況の詳細を取得する/download](/rest/api/billing/enterprise/billing-enterprise-api-usage-detail#csv-format)
- [使用状況の詳細を取得する/submit](/rest/api/billing/enterprise/billing-enterprise-api-usage-detail#csv-format)
- [使用状況の詳細を取得する/usagedetails](/rest/api/billing/enterprise/billing-enterprise-api-usage-detail#json-format)
- [使用状況の詳細を取得する/usagedetailsbycustomdate](/rest/api/billing/enterprise/billing-enterprise-api-usage-detail#json-format)
- [Marketplace ストアの請求料金を取得する/marketplacecharges](/rest/api/billing/enterprise/billing-enterprise-api-marketplace-storecharge)
- [Marketplace ストアの請求料金を取得する/marketplacechargesbycustomdate](/rest/api/billing/enterprise/billing-enterprise-api-marketplace-storecharge)

すべての Consumption API は、認証と承認に Azure AD を使用するネイティブ Azure API に置き換えられます。 呼び出し元の Azure REST API の詳細については、[REST の作業開始](/rest/api/azure/#create-the-request)に関するページを参照してください。

上記のすべての API は、Consumption/Usage Details API に置き換えられます。

Usage Details API で使用状況の詳細を取得するには:

| Method | 要求 URI |
| --- | --- |
| GET | `https://management.azure.com/{scope}/providers/Microsoft.Consumption/usageDetails?api-version=2019-01-01` |

Usage Details API は、すべての Cost Management API と同様に複数のスコープで利用できます。 請求されたコストについては、従来より登録レベルで受信するので、課金プロファイルのスコープを使用してください。  Azure Cost Management のスコープの詳細については、「[スコープを理解して使用する](understand-work-scopes.md)」を参照してください。

| Type | ID 形式 |
| --- | --- |
| 請求先アカウント | `/Microsoft.Billing/billingAccounts/{billingAccountId}` |
| 請求プロファイル | `/Microsoft.Billing/billingAccounts/{billingAccountId}/billingProfiles/{billingProfileId}` |
| Subscription | `/subscriptions/{subscriptionId}` |
| Resource group | `/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}` |

任意のプログラミング コードを更新するには、次のクエリ文字列パラメーターを使用します。

| 古いパラメーター | 新しいパラメーター |
| --- | --- |
| `billingPeriod={billingPeriod}` | サポートされていません |
| `endTime=yyyy-MM-dd` | `endDate=yyyy-MM-dd` |
| `startTime=yyyy-MM-dd` | `startDate=yyyy-MM-dd` |

応答の本文も変更しました。

古い応答本文:

```
{
  "id": "string",
  "data": [{...}, ...],
  "nextLink": "string"
}
```

新しい応答本文:

```
{
  "value": [{
    "id": "{scope}/providers/Microsoft.Consumption/usageDetails/###",
    "name": "###",
    "type": "Microsoft.Consumption/usageDetails",
    "tags": {...},
    "properties": [{...}, ...],
    "nextLink": "string"
  }, ...]
}
```

使用状況レコードの配列を含むプロパティ名がデータから _values_ に変更しました。 それぞれのレコードにはかつて、詳細なプロパティの単純なリストがありました。 ただし、各レコードは現在、タグを除いてすべての詳細が、_properties_ という名前の入れ子になったプロパティにあります。 新しい構造は、他の Azure API と一貫しています。 一部のプロパティ名が変更しました。 次の表では、対応するプロパティを示します。

| 古いプロパティ | 新しいプロパティ | メモ |
| --- | --- | --- |
| AccountId | 該当なし | サブスクリプションの作成者は追跡されません。 invoiceSectionId (departmentId と同じ) を使用します。 |
| AccountNameAccountOwnerId および AccountOwnerEmail | 該当なし | サブスクリプションの作成者は追跡されません。 invoiceSectionName (departmentName と同じ) を使用します。 |
| AdditionalInfo | additionalInfo | &nbsp;  |
| ChargesBilledSeparately | isAzureCreditEligible | これらのプロパティは正反対であることに注意してください。 isAzureCreditEnabled が true の場合、ChargesBilledSeparately は false になります。 |
| ConsumedQuantity | 数量 | &nbsp; |
| ConsumedService | consumedService | 正確な文字列値は異なる場合があります。 |
| ConsumedServiceId | なし | &nbsp; |
| CostCenter | costCenter | &nbsp; |
| Date および usageStartDate | date | &nbsp;  |
| 日 | なし | 日付から日を解析します。 |
| DepartmentId | invoiceSectionId | 正確な値が異なります。 |
| DepartmentName | invoiceSectionName | 正確な文字列値は異なる場合があります。 必要に応じて、部門に適合するように請求書セクションを構成します。 |
| ExtendedCost および Cost | costInBillingCurrency | &nbsp;  |
| InstanceId | resourceId | &nbsp;  |
| Is Recurring Charge | なし | &nbsp;  |
| Location | location | &nbsp;  |
| MeterCategory | meterCategory | 正確な文字列値は異なる場合があります。 |
| MeterId | meterId | 正確な文字列値は異なります。 |
| MeterName | meterName | 正確な文字列値は異なる場合があります。 |
| MeterRegion | meterRegion | 正確な文字列値は異なる場合があります。 |
| MeterSubCategory | meterSubCategory | 正確な文字列値は異なる場合があります。 |
| 月 | なし | 日付から月を解析します。 |
| プラン名 | なし | publisherName と productOrderName を使用します。 |
| OfferId | なし | &nbsp;  |
| Order Number | なし | &nbsp;  |
| PartNumber | なし | 価格を一意に識別するには、meterId と productOrderName を使用します。 |
| Plan Name | productOrderName | &nbsp;  |
| Product | Product |   |
| ProductId | productId | 正確な文字列値は異なります。 |
| 発行元の名前 | publisherName | &nbsp;  |
| ResourceGroup | resourceGroupName | &nbsp;  |
| ResourceGuid | meterId | 正確な文字列値は異なります。 |
| ResourceLocation | resourceLocation | &nbsp;  |
| ResourceLocationId | なし | &nbsp;  |
| ResourceRate | effectivePrice | &nbsp;  |
| ServiceAdministratorId | 該当なし | &nbsp;  |
| ServiceInfo1 | serviceInfo1 | &nbsp;  |
| ServiceInfo2 | serviceInfo2 | &nbsp;  |
| ServiceName | meterCategory | 正確な文字列値は異なる場合があります。 |
| ServiceTier | meterSubCategory | 正確な文字列値は異なる場合があります。 |
| StoreServiceIdentifier | 該当なし | &nbsp;  |
| SubscriptionGuid | subscriptionId | &nbsp;  |
| SubscriptionId | subscriptionId | &nbsp;  |
| SubscriptionName | subscriptionName | &nbsp;  |
| Tags | tags | タグ プロパティは、入れ子になったプロパティのプロパティではなく、ルート オブジェクトに適用されます。 |
| UnitOfMeasure | unitOfMeasure | 正確な文字列値は異なります。 |
| usageEndDate | date | &nbsp;  |
| 年 | なし | 日付から年を解析します。 |
| (新規) | billingCurrency | 料金に使用される通貨。 |
| (新規) | billingProfileId | 課金プロファイルの一意の ID (登録と同じ)。 |
| (新規) | billingProfileName | 課金プロファイルの名前 (登録と同じ)。 |
| (新規) | chargeType | Azure サービスの使用状況、Marketplace の使用状況および購入を区別するために使用します。 |
| (新規) | invoiceId | 請求書の一意の ID。 現在開いている月については空白です。 |
| (新規) | publisherType | 購入に関するパブリッシャーの種類。 使用状況については空白です。 |
| (新規) | serviceFamily | 購入のタイプ。 使用状況については空白です。 |
| (新規) | servicePeriodEndDate | 購入済みのサービスの終了日。 |
| (新規) | servicePeriodStartDate | 購入済みのサービスの開始日。 |

## <a name="billing-periods-api-replaced-by-invoices-api"></a>Invoices API で置き換えられた Billing Periods API

MCA 請求先アカウントは、請求期間を使用しません。 代わりに、特定の請求期間にコストの範囲を定めるために請求書を使用します。 [Billing Periods API](/rest/api/billing/enterprise/billing-enterprise-api-billing-periods) は Invoices API に置き換えられます。 すべての Consumption API は、認証と承認に Azure AD を使用するネイティブ Azure API に置き換えられます。 呼び出し元の Azure REST API の詳細については、[REST の作業開始](/rest/api/azure/#create-the-request)に関するページを参照してください。

Invoices API で請求書を取得するには:

| Method | 要求 URI |
| --- | --- |
| GET | `https://management.azure.com/providers/Microsoft.Billing/billingAccounts/{billingAccountId}/billingProfiles/{billingProfileId}/invoices?api-version=2018-11-01-preview` |

## <a name="price-sheet-apis"></a>Price Sheet API

このセクションでは、既存の Price Sheet API について説明し、Microsoft 顧客契約用の Price Sheet API への移行に関する推奨事項を示します。 また、Microsoft 顧客契約用の Price Sheet API について説明し、価格シートのフィールドについて説明します。 [Enterprise Get price sheet](/rest/api/billing/enterprise/billing-enterprise-api-pricesheet) API および [Enterprise Get billing periods](/rest/api/billing/enterprise/billing-enterprise-api-billing-periods) API は、Api は、Microsoft 顧客契約用の Price Sheet API に置き換えられます (Microsoft.Billing/billingAccounts/billingProfiles/pricesheet)。 新しい API は、非同期の REST 形式での JSON と CSV の両方の形式をサポートしています。 すべての Consumption API は、認証と承認に Azure AD を使用するネイティブ Azure API に置き換えられます。 呼び出し元の Azure REST API の詳細については、[REST の作業開始](/rest/api/azure/#create-the-request)に関するページを参照してください。

### <a name="billing-enterprise-apis"></a>Billing Enterprise API

価格および請求期間情報を取得するために、Billing Enterprise API をエンタープライズ登録で使用していました。 認証と承認では Azure Active Directory Web トークンを使用していました。

Price Sheet および Billing Period API を使用して、指定されたエンタープライズ登録の適用可能な価格を取得するには:

| Method | 要求 URI |
| --- | --- |
| GET | `https://consumption.azure.com/v2/enrollments/{enrollmentNumber}/pricesheet` |
| GET | `https://consumption.azure.com/v2/enrollments/{enrollmentNumber}/billingPeriods/{billingPeriod}/pricesheet` |

### <a name="price-sheet-api-for-microsoft-customer-agreements"></a>Microsoft 顧客契約の Price Sheet API

Microsoft 顧客契約の Price Sheet API を使用して、すべての Azure Consumption および Marketplace 従量課金サービスの価格を表示します。 課金プロファイル用に示した料金は課金プロファイルに属しているすべてのサブスクリプションに適用されます。

Price Sheet API を使用して、CSV 形式ですべての Azure Consumption サービスの価格シート データを表示します。

| Method | 要求 URI |
| --- | --- |
| POST | `https://management.azure.com/providers/Microsoft.Billing/billingAccounts/{billingAccountId}/billingProfiles/{billingProfileId}/pricesheet/default/download?api-version=2018-11-01-preview&startDate=2019-01-01&endDate=2019-01-31&format=csv` |

Price Sheet API を使用して、JSON 形式ですべての Azure Consumption サービスの価格シート データを表示します。

| Method | 要求 URI |
| --- | --- |
| POST | `https://management.azure.com/providers/Microsoft.Billing/billingAccounts/{billingAccountId}/billingProfiles/{billingProfileId}/pricesheet/default/download?api-version=2018-11-01-preview&startDate=2019-01-01&endDate=2019-01-31&format=json` |

API を使用すると、アカウント全体の価格シートが返されます。 ただし、PDF 形式で、簡略版の価格シートを取得することもできます。 概要には、特定の請求書について請求されている Azure Consumption および Marketplace 従量課金サービスが含まれています。 請求書は、{invoiceId} で識別され、これは請求書の概要 PDF ファイルに示された**請求書番号**と同じです。 次に例を示します。

![InvoiceId に対応する請求書番号を示す画像例](./media/migrate-cost-management-api/invoicesummary.png)

CSV 形式で Price Sheet API に関する請求書情報を表示するには:

| Method | 要求 URI |
| --- | --- |
| POST | `https://management.azure.com/providers/Microsoft.Billing/billingAccounts/2909cffc-b0a2-5de1-bb7b-5d3383764184/billingProfiles/2dcffe0c-ee92-4265-8647-515b8fe7dc78/invoices/{invoiceId}/pricesheet/default/download?api-version=2018-11-01-preview&format=csv` |

JSON 形式で Price Sheet API に関する請求書情報を表示するには:

| Method | 要求 URI |
| --- | --- |
| POST | `https://management.azure.com/providers/Microsoft.Billing/billingAccounts/2909cffc-b0a2-5de1-bb7b-5d3383764184/billingProfiles/2dcffe0c-ee92-4265-8647-515b8fe7dc78/invoices/{invoiceId}/pricesheet/default/download?api-version=2018-11-01-preview&format=json` |

現在開いている請求期間またはサービス期間におけるすべての Azure Consumption または Marketplace 従量課金サービスについて見積もり価格を表示することもできます。

Price Sheet API を使用して従量課金サービスの見積もり価格を CSV 形式で表示するには:

| Method | 要求 URI |
| --- | --- |
| POST | `https://management.azure.com/providers/Microsoft.Billing/billingAccounts/{billing AccountId}/billingProfiles/{billingProfileId}/pricesheet/default/download?api-version=2018-11-01-preview&format=csv` |

Price Sheet API を使用して従量課金サービスの見積もり価格を JSON 形式で表示するには:

| Method | 要求 URI |
| --- | --- |
| POST | `https://management.azure.com/providers/Microsoft.Billing/billingAccounts/{billing AccountId}/billingProfiles/{billingProfileId}/pricesheet/default/download?api-version=2018-11-01-preview&format=json` |

Microsoft 顧客契約の Price Sheet API は、*非同期 REST API* です。 API の応答は、以前の同期 API から変更しました。 API 応答の本文も変更しました。

#### <a name="old-response-body"></a>古い応答本文

同期 REST API 応答の一例を次に示します。

```
[
        {
            "id": "enrollments/573549891/billingperiods/2016011/products/343/pricesheets",
            "billingPeriodId": "201704",
            "meterId": "dc210ecb-97e8-4522-8134-2385494233c0",
            "meterName": "A1 VM",
            "unitOfMeasure": "100 Hours",
            "includedQuantity": 0,
            "partNumber": "N7H-00015",
            "unitPrice": 0.00,
            "currencyCode": "USD"
        },
        {
    ]
```

#### <a name="new-response-body"></a>新しい応答本文

この API は、[Azure REST の非同期](../azure-resource-manager/resource-manager-async-operations.md)形式をサポートします。 GET を使用して API を呼び出すと、次の応答が表示されます。

```
No Response Body

HTTP Status 202 Accepted
```

出力の場所を示した次のヘッダーが送信されます。

```
Location:https://management.azure.com/providers/Microsoft.Consumption/operationresults/{operationId}?sessiontoken=XZDFSnvdkbkdsb==

Azure-AsyncOperation:https://managment.azure.com/providers/Microsoft.Consumption/operationStatus/{operationId}?sessiontoken=XZDFSnvdkbkdsb==

Retry-After: 10

OData-EntityId: {operationId}

```

その場所に別の GET を呼び出します。 GET 呼び出しへの応答は、操作が完了または失敗の状態に到達するまでは同じです。 完了したら、GET 呼び出しの場所への応答は、ダウンロード URL を返します。 操作が同時に実行されたかのようになります。 次に例を示します。

```
HTTP Status 200

                                    {
                            “id”: “providers/Microsoft.Consumption/operationresults/{operationId}”,
                            “name”: {operationId},
                           “type”: “Microsoft.Consumption/operationResults”,
                           “properties” : {
                                  “downloadUrl”: {urltoblob},
                                  “vaildTill”: “Date”
}
                     }
```

クライアントは、`Azure-AsyncOperation` に対しても GET 呼び出しを行えます。 エンドポイントは、操作の状態を返します。

次の表は、以前の Enterprise Get price sheet API のフィールドを示しています。 Microsoft の顧客契約の新しい価格シートの対応するフィールドを含んでいます。

| 古いプロパティ | 新しいプロパティ | メモ |
| --- | --- | --- |
| billingPeriodId  | _適用不可_ | 適用不可。 Microsoft の顧客契約の場合、請求書と関連する価格シートが billingPeriodId の概念を置き換えました。 |
| meterId  | meterId | &nbsp;  |
| unitOfMeasure  | unitOfMeasure | 正確な文字列値は異なる場合があります。 |
| includedQuantity  | includedQuantity | Microsoft 顧客契約のサービスには適用されません。 |
| partNumber  | _適用不可_ | 代わりに、productOrderName (offerId と同じ) と meterid の組み合わせを使用します。 |
| unitPrice  | unitPrice | 単価は、Microsoft 顧客契約で利用されるサービスに適用されます。 |
| currencyCode  | pricingCurrency | Microsoft 顧客契約では、価格通貨と請求通貨で価格が表現されます。 currencyCode は、Microsoft 顧客契約での pricingCurrency に対応します。 |
| offerId | productOrderName | OfferId の代わりに、productOrderName を使用できますが、OfferId と同じではありません。 ただし、productOrderName と meter は、従来の登録の meterId と Offerid に関連して Microsoft 顧客契約の価格を決定します。 |

## <a name="consumption-price-sheet-api-operations"></a>Consumption Price Sheet API の操作

Enterprise 契約の場合、subscriptionId によるスコープや請求期間に、Consumption Price Sheet API [Get](/rest/api/consumption/pricesheet/get) および [Get By Billing Period](/rest/api/consumption/pricesheet/getbybillingperiod) を使用しました。 API は、Azure Resource Management 認証を使用します。

Price Sheet API を使用してスコープの価格シート情報を取得するには:

| Method | 要求 URI |
| --- | --- |
| GET | `https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.Consumption/pricesheets/default?api-version=2018-10-01` |

Price Sheet API を使用して課金期間ごとの価格シート情報を取得するには:

| Method | 要求 URI |
| --- | --- |
| GET | `https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.Billing/billingPeriods/{billingPeriodName}/providers/Microsoft.Consumption/pricesheets/default?api-version=2018-10-01` |

上記の API エンドポイントの代わりに、Microsoft 顧客契約には次のものを使用します。

**Microsoft 顧客契約の Price Sheet API (非同期 REST API)**

この API は、Microsoft 顧客契約用であり、その他の属性を提供します。

**請求先アカウントの課金プロファイル スコープの価格シート**

この API は、既存の API です。 これは、請求先アカウントの課金プロファイルに価格シートを提供するように更新されました。

## <a name="price-sheet-for-a-scope-by-billing-account"></a>請求先アカウントごとのスコープの価格シート

Azure Resource Manager 認証は、請求先アカウントの登録スコープで価格シートを取得するときに使用されます。

請求先アカウントの登録アカウントで価格シートを取得するには:

| Method | 要求 URI |
| --- | --- |
| GET | `/providers/Microsoft.Billing/billingAccounts/65085863/providers/Microsoft.Consumption/pricesheets/download?api-version=2019-01-01` |

Microsoft 顧客契約では、次のセクションの情報を使用します。 Microsoft 顧客契約に使用されるフィールド プロパティを提供します。

### <a name="price-sheet-for-a-billing-profile-scope-in-a-billing-account"></a>請求先アカウントの課金プロファイル スコープの価格シート

請求先アカウント API により更新された価格シートは、CSV 形式で価格シートを取得します。 MCA の課金プロファイル スコープで価格シートを取得するには:

| Method | 要求 URI |
| --- | --- |
| GET | `/providers/Microsoft.Billing/billingAccounts/28ae4b7f-41bb-581e-9fa4-8270c857aa5f/billingProfiles/ef37facb-cd6f-437a-9261-65df15b673f9/providers/Microsoft.Consumption/pricesheets/download?api-version=2019-01-01` |

EA の登録スコープでは、API の応答およびプロパティは同じです。 プロパティは、同じ MCA プロパティに対応します。

[Azure Resource Manager Price Sheet API](/rest/api/consumption/pricesheet) の以前のプロパティと新しい同じプロパティは、次の表に示されています。

| 古い Azure Resource Manager Price Sheet API プロパティ  | 新しい Microsoft 顧客契約 Price Sheet API プロパティ   | 説明 |
| --- | --- | --- |
| 測定 ID | _meterId_ | メーターの一意の識別子。 meterId と同じです。 |
| Meter name | meterName | メーターの名前。 メーターは、Azure サービスのデプロイ可能なリソースを表します。 |
| Meter category  | service | メーターの分類カテゴリの名前。 Microsoft 顧客契約価格シートのサービスと同じです。 正確な文字列値は異なります。 |
| Meter subcategory | meterSubCategory | メーターのサブ分類カテゴリの名前。 サービスの高レベル機能セット比較の分類に基づきます。 たとえば、Basic SQL DB と Standard SQL DB。 |
| Meter region | meterRegion | &nbsp;  |
| 単位 | _適用不可_ | unitOfMeasure から解析できます。 |
| Unit of measure | unitOfMeasure | &nbsp;  |
| Part number | _適用不可_ | partNumber ではなく、productOrderName と MeterId を使用して、課金プロファイルの価格を一意に識別します。 フィールドは、MCA 請求書の partNumber ではなく MCA 請求書に一覧表示されます。 |
| Unit price | unitPrice | Microsoft 顧客契約の単価。 |
| Currency code | pricingCurrency | Microsoft 顧客契約では、価格通貨と請求通貨で価格を表します。 Currency code は、Microsoft 顧客契約での pricingCurrency と同じです。 |
| Included quantity | includedQuantity | Microsoft 顧客契約のサービスには適用されません。 ゼロの値を表示します。 |
|  Offer Id  | productOrderName | OfferId ではなく productOrderName を使用します。 ただし、OfferId とは同じではなく、productOrderName と meter が、Microsoft 顧客契約の価格を決定します。 従来の登録では、meterId と Offerid に関連していました。 |

Microsoft 顧客契約の価格は、エンタープライズ契約とは異なって定義されます。 エンタープライズ加入契約のサービスの価格は、製品、PartNumber、メーター、およびオファーで一意です。 PartNumber は、Microsoft 顧客契約で使用されていません。

Microsoft 顧客契約の一部である Azure 従量課金サービスの価格は、productOrderName と meterId で一意です。 これらは、サービス メーターと製品プランを表します。

価格シートと、Usage Details API での使用状況の間の調整をするために、productOrderName と meterId を使用できます。

課金プロファイル所有者、共同作成者、閲覧者、および請求書管理者の権限を持つユーザーには、価格シートをダウンロードできます。

価格シートには、価格が使用量に基づくサービスの価格が含まれています。 サービスには、Azure の消費量と Marketplace の消費量が含まれます。 各サービス期間の最後の最新価格がロックされ、単一のサービス期間における使用料に適用されます。 Azure 従量課金サービスの場合、サービス期間は通常、カレンダー月です。

### <a name="retired-price-sheet-api-fields"></a>廃止された Price Sheet API フィールド

次のフィールドは、Microsoft 顧客契約 Price Sheet API で使用されなくなったか、同じフィールドがあります。

|廃止されたフィールド| 説明|
|---|---|
| billingPeriodId | 適用不可。 MCA の InvoiceId に対応しています。 |
| offerId | 適用不可。 MCA の productOrderName に対応しています。 |
| meterCategory  | 適用不可。 MCA の Service に対応しています。 |
| unit | 適用不可。 unitOfMeasure から解析できます。 |
| currencyCode | MCA の pricingCurrency と同じです。 |
| meterLocation | MCA の meterRegion と同じです。 |
| partNumber partnumber | 部品番号が MCA 請求書に一覧表示されていないため、適用できません。 partnumber の代わりに meterId と productOrderName の組み合わせを使用して、価格を一意に識別します。 |
| totalIncludedQuantity | 適用不可。 |
| pretaxStandardRate  | 適用不可。 |

## <a name="reservation-instance-charge-api-replaced"></a>置き換えられた Reservation Instance Charge API

[Reserved Instance Charge API](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-charges) で予約購入の課金トランザクションを取得できます。 新しい API には、サード パーティ製 Marketplace のオファリングを含め、すべての購入が含まれています。 すべての Consumption API は、認証と承認に Azure AD を使用するネイティブ Azure API に置き換えられます。 呼び出し元の Azure REST API の詳細については、[REST の作業開始](/rest/api/azure/#create-the-request)に関するページを参照してください。 Reserved Instance Charge API は Transactions API に置き換えられます。

Transactions API を使用して予約購入トランザクションを取得するには:

| Method | 要求 URI |
| --- | --- |
| GET | `https://management.azure.com/providers/Microsoft.Billing/billingAccounts/{billingAccountId}/billingProfiles/{billingProfileId}/transactions?api-version=2018-11-01-preview` |

## <a name="recommendations-apis-replaced"></a>置き換えられた推奨の API

Reserved Instance Purchase Recommendations API は、過去 7 日、30 日、または 60 日にわたる仮想マシンの使用状況を示します。 API は、予約購入の推奨事項も提供します。 以下が含まれます。

- [Shared Reserved Instance Recommendation API](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-recommendation#request-for-shared-reserved-instance-recommendations)
- [Single Reserved Instance Recommendations API](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-recommendation#request-for-single-reserved-instance-recommendations)

すべての Consumption API は、認証と承認に Azure AD を使用するネイティブ Azure API に置き換えられます。 呼び出し元の Azure REST API の詳細については、[REST の作業開始](/rest/api/azure/#create-the-request)に関するページを参照してください。 前述の Reservation Recommendations API は、[Microsoft.Consumption/reservationRecommendations](/rest/api/consumption/reservationrecommendations/list) API に置き換えられました。

Reservation Recommendations API を使用して予約推奨事項を取得するには:

| Method | 要求 URI |
| --- | --- |
| GET | `https://management.azure.com/providers/Microsoft.Consumption/reservationRecommendations?api-version=2019-01-01` |

## <a name="reservation-usage-apis-replaced"></a>置き換えられた Reservation Usage API

Reserved Instance Usage API を使用して、加入契約での予約の使用状況を取得できます。 加入契約に複数の予約済みインスタンスがある場合は、この API を使用して、すべての予約済みインスタンス購入の使用状況も取得できます。

以下が含まれます。

- [Reserved Instance 使用量の詳細](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-usage#request-for-reserved-instance-usage-details)
- [Reserved Instance 使用量の概要](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-usage)

すべての Consumption API は、認証と承認に Azure AD を使用するネイティブ Azure API に置き換えられます。 呼び出し元の Azure REST API の詳細については、[REST の作業開始](/rest/api/azure/#create-the-request)に関するページを参照してください。 前述の Reservation Recommendations API は、[Microsoft.Consumption/reservationDetails](/rest/api/consumption/reservationsdetails) および [Microsoft.Consumption/reservationSummaries](/rest/api/consumption/reservationssummaries) API に置き換えられました。

Reservation Details API を使用して予約の詳細を取得するには:

| Method | 要求 URI |
| --- | --- |
| GET | `https://management.azure.com/providers/Microsoft.Consumption/reservationDetails?api-version=2019-01-01` |

Reservation Details API を使用して予約の概要を取得するには:

| Method | 要求 URI |
| --- | --- |
| GET | `https://management.azure.com/providers/Microsoft.Consumption/reservationSummaries?api-version=2019-01-01` |



## <a name="move-from-cloudyn-to-cost-management"></a>Cost Management に Cloudyn から移行する

[Cloudyn](https://cloudyn.com) を使用する組織は、あらゆるコスト管理ニーズに対して [Azure Cost Management](https://azure.microsoft.com/services/cost-management/) から使用し始める必要があります。 Cost Management はオンボードせずに、8 時間の待機時間で Azure portal で使用できます。 詳細については、[Cost Management のドキュメント](index.yml)を参照してください。

Azure Cost Management では、次の操作が可能になります。

- 事前に定義された予算に対してコストを時系列で表示します。 毎日のコスト パターンを分析して、異常な支出を特定し停止します。 タグ、リソース グループ、サービス、および場所別にコストを分割します。
- 使用状況とコストの制限を設定し、重要なしきい値に近づくと通知を受け取るように予算を作成します。 カスタム イベントをトリガーし、お客様の条件に対する厳密な制限を適用するアクション グループを使用した自動化を設定します。
- Azure Advisor からの推奨事項を使用してコストと使用状況を最適化します。 予約により購入の最適化を見つけ、使用率の低い仮想マシンを小型化し、使用率の低いリソースを削除して、予算内に収めます。
- コストと使用量のデータ エクスポートのスケジュールを設定し、CSV ファイルを毎日、ストレージ アカウントに発行します。 課金データを同期させ最新の状態に保つために、外部システムとの統合を自動化します。

## <a name="power-bi-integration"></a>Power BI 統合

コストのレポートで Power BI を使用する場合は、次に移行する必要があります。

- Microsoft Azure Consumption Insights Power BI アプリ
- Azure Consumption Insights デスクトップ コネクタ


最高の柔軟性を必要としている組織には、コネクタを使用することをお勧めします。 ただし、Power BI アプリは、クイック セットアップでの利用できます。

- [Microsoft Azure Consumption Insights Power BI アプリ](https://appsource.microsoft.com/product/power-bi/pbi_azureconsumptioninsights.pbi-azure-consumptioninsights?tab=overview)をインストールする
- [Azure Consumption Insights コネクタで接続する](/power-bi/desktop-connect-azure-consumption-insights)

以前の Consumption Insights コンテンツ パックおよびコネクタは、加入契約レベルで機能していました。 少なくとも読み取りアクセス許可が必要でした。 新しい Consumption Insights Power BI アプリと新しい Azure Consumption Insights コネクタは、課金プロファイル ユーザーが使用できます。 コストをレビューしたり、複数の課金プロファイルにわたるコストを表示するための追加オプションが必要なチームは、Azure portal の[コスト分析](https://ms.portal.azure.com/#blade/Microsoft_Azure_CostManagement/Menu/costanalysis)を使用する必要があります。

## <a name="next-steps"></a>次の手順

- Azure の支出を監視し制御する方法については、[Cost Management のドキュメント](index.yml)を参照してください。 または、Cost Management でリソースの使用を最適化したい場合。
