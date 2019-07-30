---
title: Azure カスタム プロバイダーを作成して利用する
description: このチュートリアルでは、カスタム プロバイダーを作成して利用する方法を説明します。
author: jjbfour
ms.service: managed-applications
ms.topic: tutorial
ms.date: 06/19/2019
ms.author: jobreen
ms.openlocfilehash: 65a8e60d8216e1da16af987c9e699e24ecaec3ec
ms.sourcegitcommit: 66237bcd9b08359a6cce8d671f846b0c93ee6a82
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/11/2019
ms.locfileid: "67799131"
---
# <a name="authoring-a-restful-endpoint-for-custom-providers"></a>カスタム プロバイダー用の RESTful エンドポイントを作成する

カスタム プロバイダーを使うと、Azure 上でワークフローをカスタマイズできます。 カスタム プロバイダーは、Azure と`endpoint`の間のコントラクトです。 このチュートリアルでは、カスタム プロバイダーを作成する手順について説明します。 Azure カスタム プロバイダーについてよくご存じでない場合は、[カスタム リソース プロバイダーの概要](./custom-providers-overview.md)に関するページをご覧ください。

このチュートリアルは、次のステップに分かれています。

- カスタム プロバイダーとは
- カスタム アクションとリソースを定義する
- カスタム プロバイダーをデプロイする

このチュートリアルは、次のチュートリアルが基になっています。

- [カスタム プロバイダー用の RESTful エンドポイントを作成する](./tutorial-custom-providers-function-authoring.md)

## <a name="creating-a-custom-provider"></a>カスタム プロバイダーを作成する

> [!NOTE]
> このチュートリアルでは、エンドポイントの作成については説明しません。 RESTful エンドポイントがない場合は、[RESTful エンドポイントの作成に関するチュートリアル](./tutorial-custom-providers-function-authoring.md)に従ってください。

`endpoint`を作成した後は、カスタム プロバイダーを作成し、それと`endpoint`の間のコントラクトを生成できます。 カスタム プロバイダーを使うと、エンドポイント定義の一覧を指定できます。

```JSON
{
  "name": "myEndpointDefinition",
  "routingType": "Proxy",
  "endpoint": "https://<yourapp>.azurewebsites.net/api/<funcname>?code=<functionkey>"
}
```

プロパティ | 必須 | 説明
---|---|---
名前 | *はい* | エンドポイント定義の名前。 Azure では、この名前は '/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/<br>resourceProviders/{resourceProviderName}/{endpointDefinitionName}' の下にあるその API によって公開されます。
routingType | *いいえ* | `endpoint`とのコントラクトの種類を決定します。 指定しないと、既定で "Proxy" になります。
endpoint | *はい* | 要求をルーティングするエンドポイント。 これにより、応答と要求の副作用がすべて処理されます。

ここでは、`endpoint`は Azure 関数のトリガー URL です。 `<yourapp>`、`<funcname>`、`<functionkey>` は、作成した関数の値に置き換える必要があります。

## <a name="defining-custom-actions-and-resources"></a>カスタム アクションとリソースを定義する

カスタム プロバイダーには、`actions` と `resourceTypes` の下でモデル化されているエンドポイント定義の一覧が含まれます。 `actions` はカスタム プロバイダーによって公開されるカスタム アクションに対応し、`resourceTypes` はカスタム リソースです。 このチュートリアルでは、`myCustomAction` という名前の `action` および `myCustomResources` という名前の `resourceType` で、カスタム プロバイダーを定義します。

```JSON
{
  "properties": {
    "actions": [
      {
        "name": "myCustomAction",
        "routingType": "Proxy",
        "endpoint": "https://<yourapp>.azurewebsites.net/api/<funcname>?code=<functionkey>"
      }
    ],
    "resourceTypes": [
      {
        "name": "myCustomResources",
        "routingType": "Proxy",
        "endpoint": "https://<yourapp>.azurewebsites.net/api/<funcname>?code=<functionkey>"
      }
    ]
  },
  "location": "eastus"
}
```

`endpoint` は、前のチュートリアルで作成した関数のトリガー URL に置き換えます。

## <a name="deploying-the-custom-provider"></a>カスタム プロバイダーをデプロイする

> [!NOTE]
> `endpoint` は関数の URL に置き換える必要があります。

Azure Resource Manager テンプレートを使って、上記のカスタム プロバイダーをデプロイできます。

```JSON
{
    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "resources": [
        {
            "type": "Microsoft.CustomProviders/resourceProviders",
            "name": "myCustomProvider",
            "apiVersion": "2018-09-01-preview",
            "location": "eastus",
            "properties": {
                "actions": [
                    {
                        "name": "myCustomAction",
                        "routingType": "Proxy",
                        "endpoint": "https://<yourapp>.azurewebsites.net/api/<funcname>?code=<functionkey>"
                    }
                ],
                "resourceTypes": [
                    {
                        "name": "myCustomResources",
                        "routingType": "Proxy",
                        "endpoint": "https://<yourapp>.azurewebsites.net/api/<funcname>?code=<functionkey>"
                    }
                ]
            }
        }
    ]
}
```

## <a name="using-custom-actions-and-resources"></a>カスタム アクションとリソースを使用する

カスタム プロバイダーを作成した後は、新しい Azure API を利用できます。 次のセクションでは、カスタム プロバイダーを呼び出して利用する方法を説明します。

### <a name="custom-actions"></a>カスタム アクション

# <a name="azure-clitabazure-cli"></a>[Azure CLI](#tab/azure-cli)

> [!NOTE]
> `{subscriptionId}` と `{resourceGroupName}` は、カスタム プロバイダーがデプロイされたサブスクリプションとリソース グループに置き換える必要があります。

```azurecli-interactive
az resource invoke-action --action myCustomAction \
                          --ids /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/myCustomProvider \
                          --request-body
                            '{
                                "hello": "world"
                            }'
```

パラメーター | 必須 | 説明
---|---|---
action | *はい* | 作成したカスタム プロバイダーで定義されているアクションの名前。
ids | *はい* | 作成したカスタム プロバイダーのリソース ID。
request-body | *いいえ* | `endpoint`に送信される要求本文。

# <a name="templatetabtemplate"></a>[テンプレート](#tab/template)

なし。

---

### <a name="custom-resources"></a>カスタム リソース

# <a name="azure-clitabazure-cli"></a>[Azure CLI](#tab/azure-cli)

> [!NOTE]
> `{subscriptionId}` と `{resourceGroupName}` は、カスタム プロバイダーがデプロイされたサブスクリプションとリソース グループに置き換える必要があります。

カスタム リソースを作成します。

```azurecli-interactive
az resource create --is-full-object \
                   --id /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/myCustomProvider/myCustomResources/myTestResourceName1 \
                   --properties
                    '{
                        "location": "eastus",
                        "properties": {
                            "hello" : "world"
                        }
                    }'
```

パラメーター | 必須 | 説明
---|---|---
is-full-object | *はい* | properties オブジェクトに、場所、タグ、SKU、プランなどの他のオプションが含まれることを示します。
id | *はい* | カスタム リソースのリソース ID。 これは、作成したカスタム プロバイダーのものが存在する必要があります。
properties | *はい* | `endpoint`に送信される要求本文。

Azure のカスタム リソースを削除します。

```azurecli-interactive
az resource delete --id /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/myCustomProvider/myCustomResources/myTestResourceName1
```

パラメーター | 必須 | 説明
---|---|---
id | *はい* | カスタム リソースのリソース ID。 これは、作成したカスタム プロバイダーのものが存在する必要があります。

Azure のカスタム リソースを取得します。

```azurecli-interactive
az resource show --id /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/myCustomProvider/myCustomResources/myTestResourceName1
```

パラメーター | 必須 | 説明
---|---|---
id | *はい* | カスタム リソースのリソース ID。 これは、作成したカスタム プロバイダーのものが存在する必要があります。

# <a name="templatetabtemplate"></a>[テンプレート](#tab/template)

サンプルの Azure Resource Manager テンプレート:

```JSON
{
    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "resources": [
        {
            "type": "Microsoft.CustomProviders/resourceProviders/myCustomResources",
            "name": "myCustomProvider/myTestResourceName1",
            "apiVersion": "2018-09-01-preview",
            "location": "eastus",
            "properties": {
                "hello": "world"
            }
        }
    ]
}
```

パラメーター | 必須 | 説明
---|---|---
resourceTypeName | *はい* | カスタム プロバイダーで定義されている *resourceType* の `name`。
resourceProviderName | *はい* | カスタム プロバイダーのインスタンス名。
customResourceName | *はい* | カスタム リソースの名前。

---

> [!NOTE]
> カスタム プロバイダーのデプロイと使用が完了した後は、Azure 関数も含めて、作成したリソースをクリーンアップしてください。

## <a name="next-steps"></a>次の手順

この記事では、カスタム プロバイダーについて学習しました。 次の記事にアクセスしてカスタム プロバイダーを作成しましょう。

- [方法:カスタム アクションを Azure REST API に追加する](./custom-providers-action-endpoint-how-to.md)
- [方法:カスタム リソースを Azure REST API に追加する](./custom-providers-resources-endpoint-how-to.md)
