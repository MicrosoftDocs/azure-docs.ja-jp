---
title: カスタム リソースを Azure REST API に追加する
description: カスタム リソースを Azure REST API に追加する方法について説明します。 この記事では、カスタム リソースの実装を検討しているエンドポイントの要件とベスト プラクティスについて説明します。
services: managed-applications
ms.service: managed-applications
ms.topic: conceptual
ms.author: jobreen
author: jjbfour
ms.date: 06/20/2019
ms.openlocfilehash: a3cd1fe69a0d99f9faf3a451f76a3a420d713711
ms.sourcegitcommit: 66237bcd9b08359a6cce8d671f846b0c93ee6a82
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/11/2019
ms.locfileid: "67795212"
---
# <a name="adding-custom-resources-to-azure-rest-api"></a>カスタム リソースを Azure REST API に追加する

この記事では、カスタム リソースを実装する Azure カスタム リソース プロバイダー エンドポイントの作成に関する要件とベスト プラクティスについて説明します。 Azure カスタム リソース プロバイダーについてよくご存じでない場合は、[カスタム リソース プロバイダーの概要](./custom-providers-overview.md)に関するページを参照してください。

## <a name="how-to-define-a-resource-endpoint"></a>リソース エンドポイントの定義方法

**エンドポイント**はサービスを指す URL であり、サービスと Azure 間の基になるコントラクトを実装するものです。 エンドポイントはカスタム リソース プロバイダー内に定義されており、一般にアクセス可能な URL にすることができます。 次のサンプルには、`endpointURL`によって実装される、`myCustomResource` と呼ばれる**resourceType**が含まれています。

サンプルの **ResourceProvider**:

```JSON
{
  "properties": {
    "resourceTypes": [
      {
        "name": "myCustomResource",
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

## <a name="building-a-resource-endpoint"></a>リソース エンドポイントの構築

**resourceType**を実装する**エンドポイント**では、Azure で新しい API 向けに要求と応答を処理する必要があります。 **resourceType** のあるカスタム リソース プロバイダーが作成されると、Azure に API の新しいセットが生成されます。 この場合、**resourceType** により、シングル リソース上で CRUD を実行するための `PUT`、`GET`、`DELETE` 用と、すべての既存リソースを取得するための `GET` 用に、新しい Azure リソース API が生成されます。

1 つのリソースの操作 (`PUT`、`GET`、および `DELETE`):

``` JSON
/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/myCustomResource/{myCustomResourceName}
```

すべてのリソースを取得 (`GET`):

``` JSON
/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/myCustomResource
```

カスタム リソースの場合、カスタム リソース プロバイダーが 2 種類の **routingTypes** として "`Proxy`" および "`Proxy, Cache`" を提供します。

### <a name="proxy-routing-type"></a>プロキシのルーティングの種類

"`Proxy`" **routingType** は、すべての要求メソッドを、カスタム リソース プロバイダーで指定された**エンドポイント**にプロキシします。 "`Proxy`" を使用するのは次の場合です。

- 応答の完全な制御が必要。
- システムを既存のリソースと統合する。

"`Proxy`" リソースの詳細については、[カスタム リソース プロキシ リファレンス](./custom-providers-proxy-resource-endpoint-reference.md)を参照してください

### <a name="proxy-cache-routing-type"></a>プロキシ キャッシュ ルーティングの種類

"`Proxy, Cache`" **routingType** は、`PUT` および `DELETE` 要求メソッドのみを、カスタム リソース プロバイダーで指定された**エンドポイント**にプロキシします。 カスタム リソース プロバイダーは、そのキャッシュに格納されている内容に基づいて、自動的に `GET` 要求を返します。 カスタム リソースがキャッシュでマークされている場合、カスタム リソース プロバイダーはさらに応答内のフィールドを追加/上書きして、API を Azure 準拠にします。 "`Proxy, Cache`" を使用するのは次の場合です。

- 既存のリソースがない新しいシステムを作成する。
- 既存の Azure エコシステムと連携する。

"`Proxy, Cache`" リソースの詳細については、[カスタム リソース キャッシュ リファレンス](./custom-providers-proxy-cache-resource-endpoint-reference.md)を参照してください

## <a name="creating-a-custom-resource"></a>カスタム リソースの作成

カスタム リソース プロバイダーからカスタム リソースを作成するには、主に 2 つの方法があります。

- Azure CLI
- Azure Resource Manager のテンプレート

### <a name="azure-cli"></a>Azure CLI

カスタム リソースを作成します。

```azurecli-interactive
az resource create --is-full-object \
                   --id /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/{resourceTypeName}/{customResourceName} \
                   --properties \
                    '{
                        "location": "eastus",
                        "properties": {
                            "myProperty1": "myPropertyValue1",
                            "myProperty2": {
                                "myProperty3": "myPropertyValue3"
                            }
                        }
                    }'
```

パラメーター | 必須 | 説明
---|---|---
is-full-object | *はい* | properties オブジェクトに、場所、タグ、SKU、プランなどの他のオプションが含まれることを示します。
id | *はい* | カスタム リソースのリソース ID。 これは **ResourceProvider** から存在するはずです
properties | *はい* | **エンドポイント**に送信される要求本文。

Azure のカスタム リソースを削除します。

```azurecli-interactive
az resource delete --id /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/{resourceTypeName}/{customResourceName}
```

パラメーター | 必須 | 説明
---|---|---
id | *はい* | カスタム リソースのリソース ID。 これは **ResourceProvider** から存在するはずです。

Azure のカスタム リソースを取得します。

```azurecli-interactive
az resource show --id /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/{resourceTypeName}/{customResourceName}
```

パラメーター | 必須 | 説明
---|---|---
id | *はい* | カスタム リソースのリソース ID。 これは **ResourceProvider** から存在するはずです

### <a name="azure-resource-manager-template"></a>Azure Resource Manager テンプレート

> [!NOTE]
> リソースは、**エンドポイント**からの適切な `id`、`name`、および `type` が応答に含まれていることを要求します。

Azure Resource Manager テンプレートは、`id`、`name`、および `type` がダウンストリーム エンドポイントから正しく返されることを要求します。 返されるリソース応答は次の形式である必要があります。

**エンドポイント**の応答のサンプル:

``` JSON
{
  "properties": {
    "myProperty1": "myPropertyValue1",
    "myProperty2": {
        "myProperty3": "myPropertyValue3"
    }
  },
  "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{customResourceName}",
  "name": "{customResourceName}",
  "type": "Microsoft.CustomProviders/resourceProviders/{resourceTypeName}"
}
```

サンプルの Azure Resource Manager テンプレート:

```JSON
{
    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "resources": [
        {
            "type": "Microsoft.CustomProviders/resourceProviders/{resourceTypeName}",
            "name": "{resourceProviderName}/{customResourceName}",
            "apiVersion": "2018-09-01-preview",
            "location": "eastus",
            "properties": {
                "myProperty1": "myPropertyValue1",
                "myProperty2": {
                    "myProperty3": "myPropertyValue3"
                }
            }
        }
    ]
}
```

パラメーター | 必須 | 説明
---|---|---
resourceTypeName | *はい* | カスタム プロバイダーで定義されている **resourceType** の**名前**。
resourceProviderName | *はい* | カスタム リソース プロバイダーのインスタンス名。
customResourceName | *はい* | カスタム リソースの名前。

## <a name="next-steps"></a>次の手順

- [Azure カスタム リソースプロバイダーの概要](./custom-providers-overview.md)
- [クイック スタート:Azure カスタム リソースプロバイダーの作成とカスタム リソースのデプロイ](./create-custom-provider.md)
- [チュートリアル:Azure でカスタム アクションとカスタム リソースを作成する](./tutorial-custom-providers-101.md)
- [方法:カスタム アクションを Azure REST API に追加する](./custom-providers-action-endpoint-how-to.md)
- [リファレンス: カスタム リソースのプロキシのリファレンス](./custom-providers-proxy-resource-endpoint-reference.md)
- [リファレンス: カスタム リソースのキャッシュのリファレンス](./custom-providers-proxy-cache-resource-endpoint-reference.md)
