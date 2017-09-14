---
title: "Azure Billing Enterprise API - 価格シート |Microsoft Docs"
description: "Enterprise Azure の顧客がプログラムで消費データを取り出せるようにする Reporting API について説明します。"
services: 
documentationcenter: 
author: aedwin
manager: aedwin
editor: 
tags: billing
ms.assetid: 3e817b43-0696-400c-a02e-47b7817f9b77
ms.service: billing
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: billing
ms.date: 04/25/2017
ms.author: aedwin
ms.translationtype: HT
ms.sourcegitcommit: 6e76ac40e9da2754de1d1aa50af3cd4e04c067fe
ms.openlocfilehash: 2e7d6e883abe4cee13bc5f684baf2a1ea9c6c397
ms.contentlocale: ja-jp
ms.lasthandoff: 07/31/2017

---
# <a name="reporting-apis-for-enterprise-customers---price-sheet"></a>企業ユーザー向けの Reporting API - 価格シート

Price Sheet API を使用すると、特定の加入契約と請求期間に対する各測定に適用可能な課金率が提供されます。

##<a name="request"></a>要求
追加の必要がある共通のヘッダー プロパティは、[ここ](billing-enterprise-api.md)で指定します。 請求期間が指定されていない場合は、現在の請求期間に対するデータが返されます。

|メソッド | 要求 URI|
|-|-|
|GET|https://consumption.azure.com/v2/enrollments/{enrollmentNumber}/pricesheet|
|GET|https://consumption.azure.com/v2/enrollments/{enrollmentNumber}/billingPeriods/{billingPeriod}/pricesheet|

> [!Note]
> プレビュー版の API を使用するには、上記の URL で v2 を v1 で置き換えます。
>

## <a name="response"></a>応答

    
        [
            {
                "id": "enrollments/57354989/billingperiods/201601/products/343/pricesheets",
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
                "id": "enrollments/57354989/billingperiods/201601/products/2884/pricesheets",
                "billingPeriodId": "201404",
                "meterId": "dc210ecb-97e8-4522-8134-5385494233c0",
                "meterName": "Locally Redundant Storage Premium Storage - Snapshots - AU East",
                "unitOfMeasure": "100 GB",
                "includedQuantity": 0,
                "partNumber": "N9H-00402",
                "unitPrice": 0.00,
                "currencyCode": "USD"
            },
            ...
        ]
    

> [!Note]
>プレビュー版の API を使用している場合は、meterId フィールドは使用できません。
>

**応答プロパティの定義**

|プロパティ名| 型| Description
|-|-|-|
|id| string| 特定の PriceSheet 項目 (請求期間ごとの測定) を表す一意の ID|
|billingPeriodId| string| 特定の請求期間を表す一意の ID|
|meterId| string| メーターの識別子。 使用状況の meterId にマップすることができます。|
|meterName| string| 測定名|
|unitOfMeasure| string| サービスを測定するための測定単位|
|includedQuantity| 小数点| 含まれる数量 |
|partNumber| string| 測定に関連付けられた部品番号|
|unitPrice| 小数点| 測定の単価|
|currencyCode| string| 単価の通貨コード|
<br/>
## <a name="see-also"></a>関連項目

* [Billing Periods API](billing-enterprise-api-billing-periods.md)

* [Usage Detail API](billing-enterprise-api-usage-detail.md)

* [Balance and Summary API](billing-enterprise-api-balance-summary.md)

* [Marketplace Store Charge API](billing-enterprise-api-marketplace-storecharge.md)

