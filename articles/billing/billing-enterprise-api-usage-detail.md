---
title: "Azure Billing Enterprise API - 使用状況の詳細 |Microsoft Docs"
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
ms.openlocfilehash: 0f751063510707d53ac76a287aa420818a31b04b
ms.contentlocale: ja-jp
ms.lasthandoff: 07/31/2017

---
# <a name="reporting-apis-for-enterprise-customers---usage-details"></a>企業ユーザー向けの Reporting API - 使用状況の詳細

Usage Detail API を使用すると、毎日の消費量の内訳と加入契約別の推定料金が提示されます。 この結果には、インスタンス、測定、および部署に関する情報も含まれています。 この API では、請求期間または指定された開始日と終了日によってクエリを実行できます。 
## <a name="consumption-apis"></a>Consumption API


##<a name="request"></a>要求 
追加の必要がある共通のヘッダー プロパティは、[ここ](billing-enterprise-api.md)で指定します。 請求期間が指定されていない場合は、現在の請求期間に対するデータが返されます。 カスタム期間の範囲は、開始日および終了日パラメーターを yyyy-MM-dd 形式で指定します。 サポートされている最大の時間範囲は、36 か月間です。  

|メソッド | 要求 URI|
|-|-|
|GET|https://consumption.azure.com/v2/enrollments/{enrollmentNumber}/usagedetails 
|GET|https://consumption.azure.com/v2/enrollments/{enrollmentNumber}/billingPeriods/{billingPeriod}/usagedetails|
|GET|https://consumption.azure.com/v2/enrollments/{enrollmentNumber}/usagedetailsbycustomdate?startTime=2017-01-01&endTime=2017-01-10|

> [!Note]
> プレビュー版の API を使用するには、上記の URL で v2 を v1 で置き換えます。
>

## <a name="response"></a>応答

> データが大量になる可能性があるため、結果セットはページングされます。 nextLink プロパティ (ある場合) は、データの次のページへのリンクを指定します。 リンクが空の場合は、最後のページであることを示します。 
<br/>

    {
        "id": "string",
        "data": [
            {                       
            "accountId": 0,
            "productId": 0,
            "resourceLocationId": 0,
            "consumedServiceId": 0,
            "departmentId": 0,
            "accountOwnerEmail": "string",
            "accountName": "string",
            "serviceAdministratorId": "string",
            "subscriptionId": 0,
            "subscriptionGuid": "string",
            "subscriptionName": "string",
            "date": "2017-04-27T23:01:43.799Z",
            "product": "string",
            "meterId": "string",
            "meterCategory": "string",
            "meterSubCategory": "string",
            "meterRegion": "string",
            "meterName": "string",
            "consumedQuantity": 0,
            "resourceRate": 0,
            "Cost": 0,
            "resourceLocation": "string",
            "consumedService": "string",
            "instanceId": "string",
            "serviceInfo1": "string",
            "serviceInfo2": "string",
            "additionalInfo": "string",
            "tags": "string",
            "storeServiceIdentifier": "string",
            "departmentName": "string",
            "costCenter": "string",
            "unitOfMeasure": "string",
            "resourceGroup": "string"
            }
        ],
        "nextLink": "string"
    }

<br/>
**応答プロパティの定義**

|プロパティ名| 型| Description
|-|-|-|
|id| string| API 呼び出しの一意の ID。 |
|data| JSON 配列| 各インスタンス\メーターに対する毎日の使用状況詳細の配列。|
|nextLink| string| データに複数のページがある場合、nextLink はデータの次のページを返す URL を示します。 |
|accountId| int| 古いフィールド。 旧バージョンとの互換性のために存在します。 |
|productId| int| 古いフィールド。 旧バージョンとの互換性のために存在します。 |
|resourceLocationId| int| 古いフィールド。 旧バージョンとの互換性のために存在します。 |
|consumedServiceID| int| 古いフィールド。 旧バージョンとの互換性のために存在します。 |
|departmentId| int| 古いフィールド。 旧バージョンとの互換性のために存在します。 |
|accountOwnerEmail| string| アカウント所有者の電子メール アカウント。 |
|accountName| string| 顧客が入力したアカウントの名前。 |
|serviceAdministratorId| string| サービス管理者のメール アドレス。 |
|subscriptionId| int| 古いフィールド。 旧バージョンとの互換性のために存在します。 |
|subscriptionGuid| string| サブスクリプションのグローバル一意識別子。 |
|subscriptionName| string| サブスクリプションの名前。 |
|date| string| 消費が発生した日付。 |
|product| string| メーターの追加情報。 例: A1(VM)Windows - アジア太平洋東部|
|meterId| string| 使用状況を出力したメーターの識別子。 |
|meterCategory| string| 使用した Azure プラットフォーム サービス。 |
|meterSubCategory| string| 料金に影響する Azure サービスの種類を定義します。 例: A1 VM (非 Windows)|
|meterRegion| string| データセンターの場所に基づいて価格が設定されるサービスについて、データセンターの場所を示します。 |
|meterName| string| メーターの名前。 |
|consumedQuantity| double| 消費されたメーターの量。 |
|resourceRate| double| 課金対象項目ごとに適用されるレート。 |
|cost| double| メーターで発生した料金。 |
|resourceLocation| string| メーターが実行されているデータ センターを示します。 |
|consumedService| string| 使用した Azure プラットフォーム サービス。 |
|instanceId| string| この識別子はリソース名または完全修飾リソース ID です。 詳細については、[Azure Resource Manager API](https://docs.microsoft.com/rest/api/resources/resources) に関するページを参照してください。 |
|serviceInfo1| string| Azure サービスの内部メタデータ。 |
|serviceInfo2| string| たとえば、仮想マシンのイメージの種類や ExpressRoute の ISP 名です。 |
|additionalInfo| string| サービス固有のメタデータ。 たとえば、仮想マシンのイメージの種類です。 |
|tags| string| 顧客が追加したタグ。 詳細については、[タグを使用した Azure リソースの整理](https://docs.microsoft.com/en-us/azure/azure-resource-manager/resource-group-using-tags)に関するページを参照してください。 |
|storeServiceIdentifier| string| この列は使用されません。 旧バージョンとの互換性のために存在します。 |
|departmentName| string| 部署の名前。 |
|costCenter| string| 使用状況が関連付けられているコスト センター。 |
|unitOfMeasure| string| サービスが課金される単位を特定します。 例: GB、時間数、10,000 単位 |
|resourceGroup| string| デプロイされたメーターが実行されているリソース グループ。 詳細については、「[Azure Resource Manager の概要](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview)」を参照してください。 |
<br/>
## <a name="see-also"></a>関連項目

* [Billing Periods API](billing-enterprise-api-billing-periods.md)

* [Balance and Summary API](billing-enterprise-api-balance-summary.md)

* [Marketplace Store Charge API](billing-enterprise-api-marketplace-storecharge.md) 

* [Price Sheet API](billing-enterprise-api-pricesheet.md)

