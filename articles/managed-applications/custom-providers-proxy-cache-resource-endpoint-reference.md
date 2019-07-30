---
title: カスタム リソースのキャッシュのリファレンス
description: Azure カスタム リソースプロバイダーのカスタム リソースのキャッシュのリファレンス。 この記事では、キャッシュ カスタム リソースを実装するエンドポイントの要件について説明します。
services: managed-applications
ms.service: managed-applications
ms.topic: conceptual
ms.author: jobreen
author: jjbfour
ms.date: 06/20/2019
ms.openlocfilehash: 2970e69e825dacb6f548b3e66a830f221ece0b1c
ms.sourcegitcommit: 66237bcd9b08359a6cce8d671f846b0c93ee6a82
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/11/2019
ms.locfileid: "67795224"
---
# <a name="custom-resource-cache-reference"></a>カスタム リソースのキャッシュのリファレンス

この記事では、キャッシュ カスタム リソースを実装するエンドポイントの要件について説明します。 Azure カスタム リソースプロバイダーについてよくご存じでない場合は、[カスタム リソースプロバイダーの概要](./custom-providers-overview.md)に関するページを参照してください。

## <a name="how-to-define-a-cache-resource-endpoint"></a>キャッシュ リソース エンドポイントの定義方法

プロキシ リソースは、**routingType** を "Proxy, Cache" と指定することで作成できます。

サンプルのカスタム リソースプロバイダー:

```JSON
{
  "properties": {
    "resourceTypes": [
      {
        "name": "myCustomResources",
        "routingType": "Proxy, Cache",
        "endpoint": "https://{endpointURL}/"
      }
    ]
  },
  "location": "eastus",
  "type": "Microsoft.CustomProviders/resourceProviders",
  "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}",
  "name": "{resourceProviderName}"
}
```

## <a name="building-proxy-resource-endpoint"></a>プロキシ リソース エンドポイントの構築

"Proxy, Cache" リソース **エンドポイント**を実装する**エンドポイント**では、Azure の新しい API に対する要求と応答を処理する必要があります。 この場合、**resourceType** により、シングル リソース上で CRUD を実行するための `PUT`、`GET`、`DELETE` 用と、すべての既存リソースを取得するための `GET` 用に、新しい Azure リソース API が生成されます。

> [!NOTE]
> Azure API は要求メソッドの `PUT`、`GET`、および `DELETE` を生成しますが、キャッシュ **エンドポイント**は `PUT` と `DELETE` だけを処理する必要があります。
> **エンドポイント**に `GET` も実装することをお勧めします。

### <a name="create-a-custom-resource"></a>カスタム リソースの作成

Azure API 着信要求:

``` HTTP
PUT https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/myCustomResources/{myCustomResourceName}?api-version=2018-09-01-preview
Authorization: Bearer eyJ0e...
Content-Type: application/json

{
    "properties": {
        "myProperty1": "myPropertyValue1",
        "myProperty2": {
            "myProperty3" : "myPropertyValue3"
        }
    }
}
```

この要求は、その後、次の形式で**エンドポイント**に転送されます。

``` HTTP
PUT https://{endpointURL}/?api-version=2018-09-01-preview
Content-Type: application/json
X-MS-CustomProviders-RequestPath: /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/myCustomResources/{myCustomResourceName}

{
    "properties": {
        "myProperty1": "myPropertyValue1",
        "myProperty2": {
            "myProperty3" : "myPropertyValue3"
        }
    }
}
```

同様に、**エンドポイント**からの応答は、その後顧客に返されます。 エンドポイントからの応答は、次のように返される必要があります。

- 有効な JSON オブジェクト ドキュメント。 すべての配列と文字列は、最上位のオブジェクトの下で入れ子にする必要があります。
- `Content-Type` ヘッダーは "application/json; charset=utf-8" に設定される必要があります。
- カスタム リソースプロバイダーは、要求の `name`、`type`、および `id` フィールドを上書きします。
- カスタム リソースプロバイダーは、キャッシュ エンドポイントの `properties` オブジェクトの下にあるフィールドのみを返します。

**エンドポイント**の応答:

``` HTTP
HTTP/1.1 200 OK
Content-Type: application/json; charset=utf-8

{
    "properties": {
        "myProperty1": "myPropertyValue1",
        "myProperty2": {
            "myProperty3" : "myPropertyValue3"
        }
    }
}
```

`name`、`id`、および `type` フィールドは、カスタム リソースプロバイダーによって、カスタム リソース用に自動的に生成されます。

Azure カスタム リソースプロバイダーの応答:

``` HTTP
HTTP/1.1 200 OK
Content-Type: application/json; charset=utf-8

{
    "name": "{myCustomResourceName}",
    "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/myCustomResources/{myCustomResourceName}",
    "type": "Microsoft.CustomProviders/resourceProviders/myCustomResources",
    "properties": {
        "myProperty1": "myPropertyValue1",
        "myProperty2": {
            "myProperty3" : "myPropertyValue3"
        }
    }
}
```

### <a name="remove-a-custom-resource"></a>カスタム リソースの削除

Azure API 着信要求:

``` HTTP
Delete https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/myCustomResources/{myCustomResourceName}?api-version=2018-09-01-preview
Authorization: Bearer eyJ0e...
Content-Type: application/json
```

この要求は、その後、次の形式で**エンドポイント**に転送されます。

``` HTTP
Delete https://{endpointURL}/?api-version=2018-09-01-preview
Content-Type: application/json
X-MS-CustomProviders-RequestPath: /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/myCustomResources/{myCustomResourceName}
```

同様に、**エンドポイント**からの応答は、その後顧客に返されます。 エンドポイントからの応答は、次のように返される必要があります。

- 有効な JSON オブジェクト ドキュメント。 すべての配列と文字列は、最上位のオブジェクトの下で入れ子にする必要があります。
- `Content-Type` ヘッダーは "application/json; charset=utf-8" に設定される必要があります。
- Azure カスタム リソースプロバイダーは、200 レベルの応答が返された場合にのみ、アイテムをキャッシュから削除します。 リソースが存在しない場合でも、**エンドポイント**は 204 を返す必要があります。

**エンドポイント**の応答:

``` HTTP
HTTP/1.1 200 OK
Content-Type: application/json; charset=utf-8
```

Azure カスタム リソースプロバイダーの応答:

``` HTTP
HTTP/1.1 200 OK
Content-Type: application/json; charset=utf-8
```

### <a name="retrieve-a-custom-resource"></a>カスタム リソースの取得

Azure API 着信要求:

``` HTTP
GET https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/myCustomResources/{myCustomResourceName}?api-version=2018-09-01-preview
Authorization: Bearer eyJ0e...
Content-Type: application/json
```

この要求は、**エンドポイント**に転送され**ません**。

Azure カスタム リソースプロバイダーの応答:

``` HTTP
HTTP/1.1 200 OK
Content-Type: application/json; charset=utf-8

{
    "name": "{myCustomResourceName}",
    "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/myCustomResources/{myCustomResourceName}",
    "type": "Microsoft.CustomProviders/resourceProviders/myCustomResources",
    "properties": {
        "myProperty1": "myPropertyValue1",
        "myProperty2": {
            "myProperty3" : "myPropertyValue3"
        }
    }
}
```

### <a name="enumerate-all-custom-resources"></a>すべてのカスタム リソースの列挙

Azure API 着信要求:

``` HTTP
GET https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/myCustomResources?api-version=2018-09-01-preview
Authorization: Bearer eyJ0e...
Content-Type: application/json
```

この要求は、**エンドポイント**に転送され**ません**。

Azure カスタム リソースプロバイダーの応答:

``` HTTP
HTTP/1.1 200 OK
Content-Type: application/json; charset=utf-8

{
    "value" : [
        {
            "name": "{myCustomResourceName}",
            "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/myCustomResources/{myCustomResourceName}",
            "type": "Microsoft.CustomProviders/resourceProviders/myCustomResources",
            "properties": {
                "myProperty1": "myPropertyValue1",
                "myProperty2": {
                    "myProperty3" : "myPropertyValue3"
                }
            }
        }
    ]
}
```

## <a name="next-steps"></a>次の手順

- [Azure カスタム リソースプロバイダーの概要](./custom-providers-overview.md)
- [クイック スタート:Azure カスタム リソースプロバイダーの作成とカスタム リソースのデプロイ](./create-custom-provider.md)
- [チュートリアル:Azure でカスタム アクションとカスタム リソースを作成する](./tutorial-custom-providers-101.md)
- [方法:カスタム アクションを Azure REST API に追加する](./custom-providers-action-endpoint-how-to.md)
- [リファレンス: カスタム リソースのプロキシのリファレンス](./custom-providers-proxy-resource-endpoint-reference.md)
