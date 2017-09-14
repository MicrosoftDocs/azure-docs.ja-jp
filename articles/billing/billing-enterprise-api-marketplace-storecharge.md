---
title: "Azure Billing Enterprise API - Marketplace 料金 |Microsoft Docs"
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
ms.openlocfilehash: 5539623f7ae35e14b6dafe6fdf9efe4bcaba4fd3
ms.contentlocale: ja-jp
ms.lasthandoff: 07/31/2017

---
# <a name="reporting-apis-for-enterprise-customers---marketplace-store-charge"></a>企業ユーザー向けの Reporting API - Marketplace Store Charge

Marketplace Store Charge API を使用すると、指定された請求期間または開始日と終了日 (1 回払いの料金は含まず) の使用量に基づく Marketplace 料金の日別内訳が返されます。

##<a name="request"></a>要求 
追加の必要がある共通のヘッダー プロパティは、[ここ](billing-enterprise-api.md)で指定します。 請求期間が指定されていない場合は、現在の請求期間に対するデータが返されます。 カスタム期間の範囲は、開始日および終了日パラメーターを yyyy-MM-dd 形式で指定します。サポートされている最大の時間範囲は、36 か月間です。  

|メソッド | 要求 URI|
|-|-|
|GET|https://consumption.azure.com/v2/enrollments/{enrollmentNumber}/marketplacecharges|
|GET|https://consumption.azure.com/v2/enrollments/{enrollmentNumber}/billingPeriods/{billingPeriod}/marketplacecharges|
|GET|https://consumption.azure.com/v2/enrollments/{enrollmentNumber}/marketplacechargesbycustomdate?startTime=2017-01-01&endTime=2017-01-10|

> [!Note]
> プレビュー版の API を使用するには、上記の URL で v2 を v1 で置き換えます。
>

## <a name="response"></a>応答
 
    
        [
            {
                "id": "id",
                "subscriptionGuid": "00000000-0000-0000-0000-000000000000",
                "subscriptionName": "subName",
                "meterId": "2core",
                "usageStartDate": "2015-09-17T00:00:00Z",
                "usageEndDate": "2015-09-17T23:59:59Z",
                "offerName": "Virtual LoadMaster™ (VLM) for Azure",
                "resourceGroup": "Res group",
                "instanceId": "id",
                "additionalInfo": "{\"ImageType\":null,\"ServiceType\":\"Medium\"}",
                "tags": "",
                "orderNumber": "order",
                "unitOfMeasure": "",
                "costCenter": "100",
                "accountId": 100,
                "accountName": "Account Name",
                "accountOwnerId": "account@live.com",
                "departmentId": 101,
                "departmentName": "Department 1",
                "publisherName": "Publisher 1",
                "planName": "Plan name",
                "consumedQuantity": 1.15,
                "resourceRate": 0.1,
                "extendedCost": 1.11
            },
            ...
        ]
    

**応答プロパティの定義**

|プロパティ名| 型| Description
|-|-|-|
|id|string|Marketplace の料金項目の一意の ID|
|subscriptionGuid|Guid|サブスクリプション Guid|
|subscriptionName|string|サブスクリプション名|
|meterId|string|出力された測定の ID|
|usageStartDate|DateTime|使用状況記録の開始時間|
|usageEndDate|DateTime|使用状況記録の終了時間|
|offerName|string|プラン名|
|resourceGroup|string|リソース グループ|
|instanceId|string|インスタンス ID|
|additionalInfo|string|追加情報を示すJSON 文字列|
|tags|string|JSON のタグ文字列|
|orderNumber|string|注文番号|
|unitOfMeasure|string|測定の計測単位|
|costCenter|string|コスト センター|
|accountId|int|アカウント ID|
|accountName|string |アカウント名|
|accountOwnerId|string|アカウント所有者 ID|
|departmentId|int|部署 ID|
|departmentName|string|部署名|
|publisherName|string|発行元の名前|
|planName|string|プラン名|
|consumedQuantity|小数点|この期間内の消費量|
|resourceRate|小数点|測定の単価|
|extendedCost|小数点|消費量と拡張原価に基づく推定料金|
<br/>
## <a name="see-also"></a>関連項目

* [Billing Periods API](billing-enterprise-api-billing-periods.md)

* [Usage Detail API](billing-enterprise-api-usage-detail.md) 

* [Balance and Summary API](billing-enterprise-api-balance-summary.md)

* [Price Sheet API](billing-enterprise-api-pricesheet.md)
