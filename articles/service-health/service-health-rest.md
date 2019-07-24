---
title: REST API を使用して Azure リソースの正常性イベントを取得する |Microsoft Docs
description: Azure REST API を使用して、Azure リソースの正常性イベントを取得します。
author: stephbaron
ms.author: stbaron
ms.service: service-health
ms.custom: REST
ms.topic: article
ms.date: 06/06/2017
ms.openlocfilehash: 6d83aed6910127ceb34b9a694f48ca9c19ab6d18
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/07/2019
ms.locfileid: "55878393"
---
# <a name="get-resource-health-using-the-rest-api"></a>REST API を使用して Resource Health を取得する 

この例の記事では、[Azure REST API](/rest/api/azure/) を使用してサブスクリプションの Azure リソースの正常性イベント一覧を取得する方法を説明します。

完全なリファレンス ドキュメントと REST API の他の例は、[Azure Monitor REST リファレンス](/rest/api/monitor)で確認できます。 

## <a name="build-the-request"></a>要求を作成する

以下の `GET` HTTP 要求を使用して `2018-05-16` から `2018-06-20` までの時間範囲でのサブスクリプションの正常性イベントを一覧します。

```http
https://management.azure.com/subscriptions/{subscription-id}/providers/microsoft.insights/eventtypes/management/values?api-version=2015-04-01&%24filter=eventTimestamp%20ge%20'2018-05-16T04%3A36%3A37.6407898Z'%20and%20eventTimestamp%20le%20'2018-06-20T04%3A36%3A37.6407898Z'
```

### <a name="request-headers"></a>要求ヘッダー

次のヘッダーは必須です｡ 

|要求ヘッダー|説明|  
|--------------------|-----------------|  
|*Content-Type:*|必須。 `application/json` を設定します。|  
|*Authorization:*|必須。 有効な `Bearer` [ アクセス トークン](/rest/api/azure/#authorization-code-grant-interactive-clients)を設定します｡ |  

### <a name="uri-parameters"></a>URI パラメーター

| Name | 説明 |
| :--- | :---------- |
| subscriptionId | Azure サブスクリプションを識別するサブスクリプション ID。 複数のサブスクリプションをお持ちの場合は､[Working with multiple subscriptions](https://docs.microsoft.com/cli/azure/manage-azure-subscriptions-azure-cli?view=azure-cli-latest)を参照してください｡ |
| api-version | 要求で使用する API のバージョン。<br /><br /> このドキュメントでは、api-version `2015-04-01` を使用し、上記の URL に含まれています。  |
| $filter | 返される結果のセットを減らすフィルター処理オプション。 このパラメーターの使用可能なパターンは、[アクティビティ ログ操作の参照で](/rest/api/monitor/activitylogs/list#uri-parameters)使用できます。 この例は 2018-05-16 と 2018-06-20 の間の時間範囲のすべてのイベントをキャプチャします |
| &nbsp; | &nbsp; |

### <a name="request-body"></a>要求本文

この操作では、要求本文は必要ありません。

## <a name="handle-the-response"></a>応答の処理

状態コード 200 は、結果の次のページを取得する `nextlink` URL と共に、フィルター パラメーターに対応する正常性イベントの値のリストと共に返されます。

## <a name="example-response"></a>応答の例 

```json
{
  "value": [
    {
      "correlationId": "1e121103-0ba6-4300-ac9d-952bb5d0c80f",
      "eventName": {
        "value": "EndRequest",
        "localizedValue": "End request"
      },
      "id": "/subscriptions/{subscription-id}/resourceGroups/MSSupportGroup/providers/microsoft.support/supporttickets/115012112305841/events/44ade6b4-3813-45e6-ae27-7420a95fa2f8/ticks/635574752669792776",
      "resourceGroupName": "MSSupportGroup",
      "resourceProviderName": {
        "value": "microsoft.support",
        "localizedValue": "microsoft.support"
      },
      "operationName": {
        "value": "microsoft.support/supporttickets/write",
        "localizedValue": "microsoft.support/supporttickets/write"
      },
      "status": {
        "value": "Succeeded",
        "localizedValue": "Succeeded"
      },
      "eventTimestamp": "2015-01-21T22:14:26.9792776Z",
      "submissionTimestamp": "2015-01-21T22:14:39.9936304Z",
      "level": "Informational"
    }
  ],
  "nextLink": "https://management.azure.com/########-####-####-####-############$skiptoken=######"
}
```
