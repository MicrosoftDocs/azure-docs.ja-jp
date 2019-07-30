---
title: REST API で Azure サブスクリプション課金データを確認する | Microsoft Docs
description: Azure REST API を使用してサブスクリプションの課金詳細を確認する方法を説明します。
services: billing
documentationcenter: na
author: lleonard-msft
manager: ''
editor: ''
ms.assetid: 82D50B98-40F2-44B1-A445-4391EA9EBBAA
ms.service: billing
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/06/2018
ms.author: banders
ms.openlocfilehash: 8cfa429b18fb282f5c1f85d2fd1637704653b855
ms.sourcegitcommit: a874064e903f845d755abffdb5eac4868b390de7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/24/2019
ms.locfileid: "68443047"
---
# <a name="review-subscription-billing-using-rest-apis"></a>REST API を使用してサブスクリプションの課金を確認する

Azure レポート API は Azure コストの確認や管理に役立ちます。

フィルターを使用すると、ニーズに合わせて結果をカスタマイズできます。

ここでは、REST API を使用して、特定の日付範囲のサブスクリプションの課金詳細情報を返す方法を説明します。

``` http
GET https://management.azure.com/subscriptions/${subscriptionID}/providers/Microsoft.Billing/billingPeriods/${billingPeriod}/providers/Microsoft.Consumption/usageDetails?$filter=properties/usageEnd ge '${startDate}' AND properties/usageEnd le '${endDate}'
Content-Type: application/json
Authorization: Bearer
```

## <a name="build-the-request"></a>要求を作成する

`{subscriptionID}` パラメーターが必須です。これでターゲット サブスクリプションを指定します。

`{billingPeriod}` パラメーターが必須です。これで現在の[請求期間](https://docs.microsoft.com/rest/api/billing/enterprise/billing-enterprise-api-billing-periods)を指定します。

この例では `${startDate}` パラメーターおよび `${endDate}` パラメーターが必須ですが、エンドポイントでは省略可能です。 日付範囲を文字列として YYYY-MM-DD の形式で指定します (例: `'20180501'` および `'20180615'`)。

次のヘッダーは必須です｡

|要求ヘッダー|説明|
|--------------------|-----------------|
|*Content-Type:*|必須。 `application/json` を設定します。|
|*Authorization:*|必須。 有効な `Bearer` [ アクセス トークン](https://docs.microsoft.com/rest/api/azure/#authorization-code-grant-interactive-clients)を設定します｡ |

## <a name="response"></a>Response

応答に成功すると､状態コード 200 (OK) が返されます｡内容は､アカウントの詳しいコストの一覧です｡

``` json
{
  "value": [
    {
      "id": "/subscriptions/{$subscriptionID}/providers/Microsoft.Billing/billingPeriods/201702/providers/Microsoft.Consumption/usageDetails/{$detailsID}",
      "name": "{$detailsID}",
      "type": "Microsoft.Consumption/usageDetails",
      "properties": {
        "billingPeriodId": "/subscriptions/${subscriptionID}/providers/Microsoft.Billing/billingPeriods/${billingPeriod}",
        "invoiceId": "/subscriptions/${subscriptionID}/providers/Microsoft.Billing/invoices/${invoiceID}",
        "usageStart": "${startDate}}",
        "usageEnd": "${endDate}",
        "currency": "USD",
        "usageQuantity": "${usageQuantity}",
        "billableQuantity": "${billableQuantity}",
        "pretaxCost": "${cost}",
        "meterId": "${meterID}",
        "meterDetails": "${meterDetails}"
      }
    }
  ],
  "nextLink": "${nextLinkURL}"
}
```

**value** の各項目は、サービスの使用状況に案する詳細を表します。

|Response プロパティ|説明|
|----------------|----------|
|**subscriptionGuid** | サブスクリプションのグローバルに一意の ID。 |
|**startDate** | 使用開始の日付。 |
|**endDate** | 使用終了の日付。 |
|**useageQuantity** | 使用量。 |
|**billableQuantity** | 実際に課金される量。 |
|**pretaxCost** | 請求額 (外税)。 |
|**meterDetails** | 使用に関する詳細情報。 |
|**nextLink**| 設定時には、詳細の次のページの URL が指定されます。 ページが最終ページの場合は空白です。 |

この例は省略されたものです。response の各フィールドの詳しい説明については、[使用状況の詳細の一覧](https://docs.microsoft.com/rest/api/consumption/usagedetails/list#usagedetailslistforbillingperiod)をご覧ください。

その他の状態コードは､エラー状態を示します｡ そのような場合､response オブジェクトによって､要求が失敗した理由が説明されます。

``` json
{
  "error": [
    {
      "code": "Error type.",
      "message": "Error response describing why the operation failed."
    }
  ]
}
```

## <a name="next-steps"></a>次の手順
- 「[Enterprise Reporting の概要](https://docs.microsoft.com/azure/billing/billing-enterprise-api)」を参照してください。
- [Enterprise Billing REST API](https://docs.microsoft.com/rest/api/billing/) について調べます。
- [Azure Rest API の開始](https://docs.microsoft.com/rest/api/azure/)
