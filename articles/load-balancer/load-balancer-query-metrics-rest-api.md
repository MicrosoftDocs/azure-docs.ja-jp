---
title: REST API でメトリックを取得する
titleSuffix: Azure Load Balancer
description: この記事では、Azure REST API を使用して Azure Load Balancer の正常性および使用状況メトリックを収集する操作を開始します。
services: sql-database
author: asudbring
manager: KumudD
ms.service: load-balancer
ms.custom: REST, seodec18
ms.topic: how-to
ms.date: 11/19/2019
ms.author: allensu
ms.openlocfilehash: 391be596d890e05e6a8fdaf35d2cade371e468d6
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102213185"
---
# <a name="get-load-balancer-usage-metrics-using-the-rest-api"></a>REST API を使用して Load Balancer の使用状況メトリックを取得する

[Azure REST API](/rest/api/azure/) を使用して、ある期間に [Standard Load Balancer](./load-balancer-overview.md) によって処理されたバイト数を収集します。

完全なリファレンス ドキュメントと REST API の他の例は、[Azure Monitor REST リファレンス](/rest/api/monitor)で確認できます。 

## <a name="build-the-request"></a>要求を作成する

Standard Load Balancer から [ByteCount メトリック](./load-balancer-standard-diagnostics.md#multi-dimensional-metrics)を収集するには、次の GET 要求を使用します。 

```http
GET https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Network/loadBalancers/{loadBalancerName}/providers/microsoft.insights/metrics?api-version=2018-01-01&metricnames=ByteCount&timespan=2018-06-05T03:00:00Z/2018-06-07T03:00:00Z
```

### <a name="request-headers"></a>要求ヘッダー

次のヘッダーは必須です｡ 

|要求ヘッダー|説明|  
|--------------------|-----------------|  
|*Content-Type:*|必須。 `application/json` を設定します。|  
|*Authorization:*|必須。 有効な `Bearer` [アクセス トークン](/rest/api/azure/#authorization-code-grant-interactive-clients)を設定します。 |  

### <a name="uri-parameters"></a>URI パラメーター

| 名前 | 説明 |
| :--- | :---------- |
| subscriptionId | Azure サブスクリプションを識別するサブスクリプション ID。 複数のサブスクリプションをお持ちの場合は､[Working with multiple subscriptions](/cli/azure/manage-azure-subscriptions-azure-cli)を参照してください｡ |
| resourceGroupName | リソースが含まれているリソース グループの名前。 この値は、Azure Resource Manager API、CLI、またはポータルから取得できます。 |
| loadBalancerName | Azure Load Balancer の名前。 |
| metric names | 有効な[Load Balancer メトリック](./load-balancer-standard-diagnostics.md)のコンマ区切りリスト。 |
| api-version | 要求で使用する API のバージョン。<br /><br /> このドキュメントでは、api-version `2018-01-01` を使用し、上記の URL に含まれています。  |
| TimeSpan | クエリの期間。 これは `startDateTime_ISO/endDateTime_ISO` の形式を持つ文字列です。 この省略可能なパラメーターは、例では 1 日分のデータを返すように設定されています。 |
| &nbsp; | &nbsp; |

### <a name="request-body"></a>要求本文

この操作では、要求本文は必要ありません。

## <a name="handle-the-response"></a>応答の処理

メトリック値の一覧が正常に返された場合は、状態コード 200 が返されます。 エラー コードの完全な一覧は、[リファレンス ドキュメント](/rest/api/monitor/metrics/list#errorresponse)で確認できます。

## <a name="example-response"></a>応答の例 

```json
{
    "cost": 0,
    "timespan": "2018-06-05T03:00:00Z/2018-06-07T03:00:00Z",
    "interval": "PT1M",
    "value": [
        {
            "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Network/loadBalancers/{loadBalancerName}/providers/Microsoft.Insights/metrics/ByteCount",
            "type": "Microsoft.Insights/metrics",
            "name": {
                "value": "ByteCount",
                "localizedValue": "Byte Count"
            },
            "unit": "Count",
            "timeseries": [
                {
                    "metadatavalues": [],
                    "data": [
                        {
                            "timeStamp": "2018-06-06T17:24:00Z",
                            "total": 1067921034.0
                        },
                        {
                            "timeStamp": "2018-06-06T17:25:00Z",
                            "total": 0.0
                        },
                        {
                            "timeStamp": "2018-06-06T17:26:00Z",
                            "total": 3781344.0
                        },
                    ]
                }
            ]
        }
    ],
    "namespace": "Microsoft.Network/loadBalancers",
    "resourceregion": "eastus"
}
```