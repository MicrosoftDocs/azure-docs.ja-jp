---
title: カスタム アクションを Azure REST API に追加する
description: カスタム アクションを Azure REST API に追加する方法について説明します。 この記事では、カスタム アクションの実装を検討しているエンドポイントの要件とベスト プラクティスについて説明します。
services: managed-applications
ms.service: managed-applications
ms.topic: conceptual
ms.author: jobreen
author: jjbfour
ms.date: 06/20/2019
ms.openlocfilehash: 6fbd20c201e1b141b7276e3283599b00cdefd118
ms.sourcegitcommit: 66237bcd9b08359a6cce8d671f846b0c93ee6a82
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/11/2019
ms.locfileid: "67795295"
---
# <a name="adding-custom-actions-to-azure-rest-api"></a>カスタム アクションを Azure REST API に追加する

この記事では、カスタム アクションを実装する Azure カスタム リソース プロバイダー エンドポイントの作成に関する要件とベスト プラクティスについて説明します。 Azure カスタム リソース プロバイダーについてよくご存じでない場合は、[カスタム リソース プロバイダーの概要](./custom-providers-overview.md)に関する記事を参照してください。

## <a name="how-to-define-an-action-endpoint"></a>アクション エンドポイントの定義方法

**エンドポイント**はサービスを指す URL であり、サービスと Azure 間の基になるコントラクトを実装するものです。 エンドポイントはカスタム リソース プロバイダー内に定義されており、一般にアクセス可能な URL にすることができます。 次のサンプルには、`endpointURL`によって実装される、`myCustomAction` と呼ばれる**アクション**が含まれています。

サンプルの **ResourceProvider**:

```JSON
{
  "properties": {
    "actions": [
      {
        "name": "myCustomAction",
        "routingType": "Proxy",
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

## <a name="building-an-action-endpoint"></a>アクション エンドポイントを構築する

**アクション**を実装する**エンドポイント**では、Azure で新しい API 向けに要求と応答を処理する必要があります。 **アクション**のあるカスタム リソース プロバイダーが作成されると、Azure に API の新しいセットが生成されます。 この場合、アクションにより `POST` 呼び出し用の新しい Azure アクション API が生成されます。

``` JSON
/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/myCustomAction
```

Azure API 受信要求:

``` HTTP
POST https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/myCustomAction?api-version=2018-09-01-preview
Authorization: Bearer eyJ0e...
Content-Type: application/json

{
    "myProperty1": "myPropertyValue1",
    "myProperty2": {
        "myProperty3" : "myPropertyValue3"
    }
}
```

この要求は、その後、次の形式で**エンドポイント**に転送されます。

``` HTTP
POST https://{endpointURL}/?api-version=2018-09-01-preview
Content-Type: application/json
X-MS-CustomProviders-RequestPath: /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/myCustomAction

{
    "myProperty1": "myPropertyValue1",
    "myProperty2": {
        "myProperty3" : "myPropertyValue3"
    }
}
```

同様に、**エンドポイント**からの応答は、その後顧客に返されます。 エンドポイントからの応答は、次のように返される必要があります。

- 有効な JSON オブジェクト ドキュメント。 すべての配列と文字列は、最上位のオブジェクトの下で入れ子にする必要があります。
- `Content-Type` ヘッダーは "application/json; charset=utf-8" に設定される必要があります。

``` HTTP
HTTP/1.1 200 OK
Content-Type: application/json; charset=utf-8

{
    "myProperty1": "myPropertyValue1",
    "myProperty2": {
        "myProperty3" : "myPropertyValue3"
    }
}
```

Azure カスタム リソース プロバイダーの応答:

``` HTTP
HTTP/1.1 200 OK
Content-Type: application/json; charset=utf-8

{
    "myProperty1": "myPropertyValue1",
    "myProperty2": {
        "myProperty3" : "myPropertyValue3"
    }
}
```

## <a name="calling-a-custom-action"></a>カスタム アクションを呼び出す

カスタム リソース プロバイダーのカスタム アクションの呼び出しには主に 2 つの方法があります。

- Azure CLI
- Azure Resource Manager のテンプレート

### <a name="azure-cli"></a>Azure CLI

```azurecli-interactive
az resource invoke-action --action {actionName} \
                          --ids /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName} \
                          --request-body \
                            '{
                                "myProperty1": "myPropertyValue1",
                                "myProperty2": {
                                    "myProperty3": "myPropertyValue3"
                                }
                            }'
```

パラメーター | 必須 | 説明
---|---|---
action | *はい* | **ResourceProvider** で定義されているアクションの名前。
ids | *はい* | **ResourceProvider** のリソース ID。
request-body | *いいえ* | **エンドポイント**に送信される要求本文。

### <a name="azure-resource-manager-template"></a>Azure Resource Manager テンプレート

> [!NOTE]
> Azure Resource Manager テンプレートでのアクションのサポートは制限されています。 テンプレート内でアクションを呼び出すためには、その名前に [`list`](../azure-resource-manager/resource-group-template-functions-resource.md#list) プレフィックスが含まれている必要があります。

リスト アクションのあるサンプルの **ResourceProvider**:

```JSON
{
  "properties": {
    "actions": [
      {
        "name": "listMyCustomAction",
        "routingType": "Proxy",
        "endpoint": "https://{endpointURL}/"
      }
    ]
  },
  "location": "eastus"
}
```

サンプルの Azure Resource Manager テンプレート:

``` JSON
{
    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "variables": {
        "resourceIdentifier": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}",
        "apiVersion": "2018-09-01-preview",
        "functionValues": {
            "myProperty1": "myPropertyValue1",
            "myProperty2": {
                "myProperty3": "myPropertyValue3"
            }
        }
    },
    "resources": [],
    "outputs": {
        "myCustomActionOutput": {
            "type": "object",
            "value": "[listMyCustomAction(variables('resourceIdentifier'), variables('apiVersion'), variables('functionValues'))]"
        }
    }
}
```

パラメーター | 必須 | 説明
---|---|---
resourceIdentifier | *はい* | **ResourceProvider** のリソース ID。
apiVersion | *はい* | リソースのランタイムの API バージョン。 常に "2018-09-01-preview" に設定する必要があります。
functionValues | *いいえ* | **エンドポイント**に送信される要求本文。

## <a name="next-steps"></a>次の手順

- [Azure カスタム リソースプロバイダーの概要](./custom-providers-overview.md)
- [クイック スタート:Azure カスタム リソースプロバイダーの作成とカスタム リソースのデプロイ](./create-custom-provider.md)
- [チュートリアル:Azure でカスタム アクションとカスタム リソースを作成する](./tutorial-custom-providers-101.md)
- [方法:カスタム リソースを Azure REST API に追加する](./custom-providers-resources-endpoint-how-to.md)
