---
title: カスタム プロバイダーを作成して使用する
description: このチュートリアルでは、Azure カスタム プロバイダーを作成して使用する方法について説明します。 カスタム プロバイダーを使用して、Azure 上のワークフローに変更を加えます。
author: jjbfour
ms.topic: tutorial
ms.date: 06/19/2019
ms.author: jobreen
ms.custom: devx-track-azurecli
ms.openlocfilehash: 4f425af7681b666b42fbcc70ac0e4c31d9df6d49
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "87503754"
---
# <a name="create-and-use-a-custom-provider"></a>カスタム プロバイダーを作成して使用する

カスタム プロバイダーは、Azure とエンドポイントの間のコントラクトです。 カスタム プロバイダーを使うと、Azure 上のワークフローに変更を加えることができます。 このチュートリアルでは、カスタム プロバイダーを作成する手順について説明します。 Azure カスタム プロバイダーについてなじみがない場合は、[Azure カスタム リソースプロバイダーの概要](overview.md)に関するページを参照してください。

## <a name="create-a-custom-provider"></a>カスタム プロバイダーを作成する

> [!NOTE]
> このチュートリアルでは、エンドポイントの作成方法については取り上げません。 RESTFUL エンドポイントがない場合は、このチュートリアルの土台となっている、[RESTful エンドポイントの作成に関するチュートリアル](./tutorial-custom-providers-function-authoring.md)に従ってください。

エンドポイントを作成したら、カスタム プロバイダーを作成して、プロバイダーとエンドポイントとの間のコントラクトを生成できます。 カスタム プロバイダーには、一連のエンドポイント定義を指定できます。

```JSON
{
  "name": "myEndpointDefinition",
  "routingType": "Proxy",
  "endpoint": "https://<yourapp>.azurewebsites.net/api/<funcname>?code=<functionkey>"
}
```

プロパティ | 必須 | 説明
---|---|---
**name** | はい | エンドポイント定義の名前。 この名前は、次の場所の対応する API を通じて公開されます: /subscriptions/<サブスクリプション ID>/resourceGroups/<リソース グループ名>/providers/Microsoft.CustomProviders<br>/resourceProviders/<リソース プロバイダー名>/<エンドポイント定義名>
**routingType** | いいえ | エンドポイントのコントラクトの種類。 この値が指定されなかった場合は、"Proxy" が既定値となります。
**endpoint** | はい | 要求をルーティングするエンドポイント。 このエンドポイントによって応答が処理され、また要求の副作用があればそれも処理されます。

Azure 関数アプリのトリガー URL が **endpoint** の値になります。 `<yourapp>`、`<funcname>`、`<functionkey>` の各プレースホルダーは、作成した関数アプリの値に置き換える必要があります。

## <a name="define-custom-actions-and-resources"></a>カスタム アクションとリソースを定義する

カスタム プロバイダーには、**actions** プロパティと **resourceTypes** プロパティの下でモデル化される一連のエンドポイント定義が含まれています。 **actions** プロパティは、カスタム プロバイダーによって公開されるカスタム アクションに対応し、**resourceTypes** プロパティはカスタム リソースを表します。 このチュートリアルのカスタム プロバイダーには、`myCustomAction` という名前の **actions** プロパティと `myCustomResources` という名前の **resourceTypes** プロパティが存在します。

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

## <a name="deploy-the-custom-provider"></a>カスタム プロバイダーをデプロイする

> [!NOTE]
> **endpoint** の値は、前のチュートリアルで作成した関数アプリのトリガー URL に置き換える必要があります。

前出のカスタム プロバイダーは、Azure Resource Manager テンプレートを使用してデプロイできます。

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

## <a name="use-custom-actions-and-resources"></a>カスタム アクションとカスタム リソースを使用する

カスタム プロバイダーを作成したら、その新しい Azure API を使用できます。 カスタム プロバイダーを呼び出して使用する方法を以下のタブで説明します。

### <a name="custom-actions"></a>カスタム アクション

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

> [!NOTE]
> `{subscriptionId}` と `{resourceGroupName}` の各プレースホルダーは、カスタム プロバイダーのデプロイ先のサブスクリプションとリソース グループに置き換えてください。

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
*action* | はい | カスタム プロバイダーに定義されているアクションの名前
*ids* | はい | カスタム プロバイダーのリソース ID
*request-body* | いいえ | エンドポイントに送信される要求本文

# <a name="template"></a>[テンプレート](#tab/template)

[なし] :

---

### <a name="custom-resources"></a>カスタム リソース

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

> [!NOTE]
> `{subscriptionId}` と `{resourceGroupName}` の各プレースホルダーは、カスタム プロバイダーのデプロイ先のサブスクリプションとリソース グループに置き換えてください。

#### <a name="create-a-custom-resource"></a>カスタム リソースの作成

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
*is-full-object* | はい | properties オブジェクトに、場所、タグ、SKU、プランなどの他のオプションが含まれるかどうかを示します。
*ID* | はい | カスタム リソースのリソース ID。 この ID は、カスタム プロバイダーのリソース ID の拡張情報です。
*properties* | はい | エンドポイントに送信される要求本文。

#### <a name="delete-a-custom-resource"></a>カスタム リソースを削除する

```azurecli-interactive
az resource delete --id /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/myCustomProvider/myCustomResources/myTestResourceName1
```

パラメーター | 必須 | 説明
---|---|---
*ID* | はい | カスタム リソースのリソース ID。 この ID は、カスタム プロバイダーのリソース ID の拡張情報です。

#### <a name="retrieve-a-custom-resource"></a>カスタム リソースの取得

```azurecli-interactive
az resource show --id /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/myCustomProvider/myCustomResources/myTestResourceName1
```

パラメーター | 必須 | 説明
---|---|---
*ID* | はい | カスタム リソースのリソース ID。 この ID は、カスタム プロバイダーのリソース ID の拡張情報です。

# <a name="template"></a>[テンプレート](#tab/template)

サンプル Resource Manager テンプレート:

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
*resourceTypeName* | はい | カスタム プロバイダーに定義されている **resourceTypes** プロパティの `name` 値。
*resourceProviderName* | はい | カスタム プロバイダーのインスタンス名。
*customResourceName* | はい | カスタム リソースの名前。

---

> [!NOTE]
> カスタム プロバイダーのデプロイと使用が完了した後は、Azure 関数アプリも含めて、作成したリソースをすべてクリーンアップしてください。

## <a name="next-steps"></a>次のステップ

この記事では、カスタム プロバイダーについて学習しました。 詳細については、次を参照してください。

- [方法: カスタム アクションを Azure REST API に追加する](./custom-providers-action-endpoint-how-to.md)
- [方法: カスタム リソースを Azure REST API に追加する](./custom-providers-resources-endpoint-how-to.md)
