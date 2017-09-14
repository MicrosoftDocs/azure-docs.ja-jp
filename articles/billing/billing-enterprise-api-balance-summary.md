---
title: "Azure Billing Enterprise API - 残高とサマリー |Microsoft Docs"
description: "Azure Billing Usage API と RateCard API について説明します。これらの API を使用すると、Azure のリソース消費と傾向を把握できます。"
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
ms.openlocfilehash: f6b149f0e656d2263705048aa5b644f5bb4a5712
ms.contentlocale: ja-jp
ms.lasthandoff: 07/31/2017

---
# <a name="reporting-apis-for-enterprise-customers---balance-and-summary"></a>企業ユーザー向けの Reporting API - 残高とサマリー

Balance and Summary API を使用すると、残高、新規購入、Azure Marketplace サービス料金、調整、および超過料金に関する情報の月別サマリーが提供されます。


##<a name="request"></a>要求 
追加の必要がある共通のヘッダー プロパティは、[ここ](billing-enterprise-api.md)で指定します。 請求期間が指定されていない場合は、現在の請求期間に対するデータが返されます。

|メソッド | 要求 URI|
|-|-|
|GET| https://consumption.azure.com/v2/enrollments/{enrollmentNumber}/balancesummary|
|GET| https://consumption.azure.com/v2/enrollments/{enrollmentNumber}/billingPeriods/{billingPeriod}/balancesummary|

> [!Note]
> プレビュー版の API を使用するには、上記の URL で v2 を v1 で置き換えます。
>

## <a name="response"></a>応答

        {
            "id": "enrollments/100/billingperiods/201507/balancesummaries",
            "billingPeriodId": 201507,
            "currencyCode": "USD",
            "beginningBalance": 0,
            "endingBalance": 1.1,
            "newPurchases": 1,
            "adjustments": 1.1,
            "utilized": 1.1,
            "serviceOverage": 1,
            "chargesBilledSeparately": 1,
            "totalOverage": 1,
            "totalUsage": 1.1,
            "azureMarketplaceServiceCharges": 1,
            "newPurchasesDetails": [
                {
                "name": "",
                "value": 1
                }
            ],
            "adjustmentDetails": [
                {
                "name": "Promo Credit",
                "value": 1.1
                },
                {
                "name": "SIE Credit",
                "value": 1.0
                }
            ]
        }


**応答プロパティの定義**

|プロパティ名| 型| Description
|-|-|-|
|id|string|特定の請求期間と加入契約に対する一意の ID|
|billingPeriodId|string |請求期間 ID|
|currencyCode|string |通貨コード|
|beginningBalance|小数点| 請求期間の期首残高|
|endingBalance|小数点| 請求期間の期末残高 (オープン期間の場合は毎日更新されます)|
|newPurchases|小数点| 新規購入額の合計|
|adjustments|小数点| 調整額の合計|
|utilized|小数点| コミットメントの合計使用率|
|serviceOverage|小数点| Azure サービスの超過料金|
|chargesBilledSeparately|小数点| 別途料金請求|
|totalOverage|小数点| serviceOverage と chargesBilledSeparately の合計|
|totalUsage|小数点| Azure サービス コミットメント と 超過料金総額の合計|
|azureMarketplaceServiceCharges|小数点| Azure Marketplace の請求額合計|
|newPurchasesDetails|名前値ペアの JSON 文字列配列|新規購入の一覧|
|adjustmentDetails|名前値ペアの JSON 文字列配列|調整 (プロモーション クレジット、SIE クレジットなど) の一覧 |


<br/>
## <a name="see-also"></a>関連項目

* [Billing Periods API](billing-enterprise-api-billing-periods.md)

* [Usage Detail API](billing-enterprise-api-usage-detail.md) 

* [Marketplace Store Charge API](billing-enterprise-api-marketplace-storecharge.md) 

* [Price Sheet API](billing-enterprise-api-pricesheet.md)
