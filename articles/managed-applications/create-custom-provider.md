---
title: Azure Custom Providers プレビューを使用したリソース プロバイダーの作成
description: リソース プロバイダーを作成し、そのカスタム リソースの種類をデプロイする方法について説明します。
services: managed-applications
author: MSEvanhi
ms.service: managed-applications
ms.topic: tutorial
ms.date: 05/01/2019
ms.author: evanhi
ms.openlocfilehash: 41200139ef55fa1ae441192e2d81b5228cf29bad
ms.sourcegitcommit: 66237bcd9b08359a6cce8d671f846b0c93ee6a82
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/11/2019
ms.locfileid: "67795313"
---
# <a name="quickstart-create-custom-provider-and-deploy-custom-resources"></a>クイック スタート:カスタム プロバイダーの作成とカスタム リソースのデプロイ

このクイックスタートでは、独自のリソース プロバイダーを作成し、そのリソース プロバイダーのカスタム リソースの種類をデプロイします。 カスタム プロバイダーの詳細については、「[Azure Custom Providers プレビューの概要](custom-providers-overview.md)」を参照してください。

## <a name="prerequisites"></a>前提条件

このクイックスタートの手順を完了するには、REST 操作を呼び出す必要があります。 [REST 要求を送信するさまざまな方法](/rest/api/azure/)があります。 REST 操作用のツールがまだない場合は、[ARMClient](https://github.com/projectkudu/ARMClient) をインストールしてください。 これは、Azure Resource Manager API の呼び出しを簡略化する、オープン ソースのコマンドライン ツールです。

## <a name="deploy-custom-provider"></a>カスタム プロバイダーをデプロイする

カスタム プロバイダーを設定するには、お使いの Azure サブスクリプションに[テンプレートの例](https://github.com/Azure/azure-docs-json-samples/blob/master/custom-providers/customprovider.json)をデプロイします。

テンプレートをデプロイすると、お使いのサブスクリプションに次のリソースが作成されます。

* リソースおよびアクションの操作が含まれている関数アプリ。
* カスタム プロバイダーを通じて作成されたユーザーを格納するためのストレージ アカウント。
* カスタム リソースの種類とアクションを定義するカスタム プロバイダー。 これは、要求を送信するために、関数アプリ エンドポイントを使用します。
* カスタム プロバイダーからのカスタム リソース。

PowerShell を使用してカスタム プロバイダーをデプロイするには、以下を使用します。

```azurepowershell-interactive
$rgName = "<resource-group-name>"
$funcName = "<function-app-name>"

New-AzResourceGroup -Name $rgName -Location eastus
New-AzResourceGroupDeployment -ResourceGroupName $rgName `
  -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/custom-providers/customprovider.json `
  -funcname $funcName
```

または、次のボタンを使用して、このソリューションをデプロイできます。

<a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-docs-json-samples%2Fmaster%2Fcustom-providers%2Fcustomprovider.json" target="_blank">
    <img src="https://azuredeploy.net/deploybutton.png"/>
</a>

## <a name="view-custom-provider-and-resource"></a>リソース プロバイダーとリソースを確認する

ポータル内では、カスタム プロバイダーは非表示のリソースの種類です。 リソース プロバイダーがデプロイされていることを確認するには、リソース グループに移動します。 **[非表示の型の表示]** オプションを選択します。

![非表示のリソースの種類を表示する](./media/create-custom-providers/show-hidden.png)

デプロイしたカスタム リソースの種類を表示するには、お使いのリソースの種類に対して GET 操作を使用します。

```
GET https://management.azure.com/subscriptions/<sub-id>/resourceGroups/<rg-name>/providers/Microsoft.CustomProviders/resourceProviders/<provider-name>/users?api-version=2018-09-01-preview
```

ARMClient の場合は、以下を使用します。

```powershell
$subID = (Get-AzContext).Subscription.Id
$requestURI = "https://management.azure.com/subscriptions/$subID/resourceGroups/$rgName/providers/Microsoft.CustomProviders/resourceProviders/$funcName/users?api-version=2018-09-01-preview"

armclient GET $requestURI
```

応答を受信します。

```json
{
  "value": [
    {
      "properties": {
        "provisioningState": "Succeeded",
        "FullName": "Santa Claus",
        "Location": "NorthPole"
      },
      "id": "/subscriptions/<sub-id>/resourceGroups/<rg-name>/providers/Microsoft.CustomProviders/resourceProviders/<provider-name>/users/santa",
      "name": "santa",
      "type": "Microsoft.CustomProviders/resourceProviders/users"
    }
  ]
}
```

## <a name="call-action"></a>アクションを呼び出す

お使いのカスタム プロバイダーには、**ping** という名前のアクションがあります。 要求を処理するコードは、関数アプリ内に実装されています。 ping アクションは、応答メッセージで応答します。

ping 要求を送信するには、お使いのカスタム プロバイダーに対して POST 操作を使用します。

```
POST https://management.azure.com/subscriptions/<sub-id>/resourceGroups/<rg-name>/providers/Microsoft.CustomProviders/resourceProviders/<provider-name>/ping?api-version=2018-09-01-preview
```

ARMClient の場合は、以下を使用します。

```powershell
$pingURI = "https://management.azure.com/subscriptions/$subID/resourceGroups/$rgName/providers/Microsoft.CustomProviders/resourceProviders/$funcName/ping?api-version=2018-09-01-preview"

armclient POST $pingURI
```

応答を受信します。

```json
{
  "pingcontent": {
    "source": "<function-name>.azurewebsites.net"
  },
  "message": "hello <function-name>.azurewebsites.net"
}
```

## <a name="create-resource-type"></a>リソースの種類を作成する

カスタム リソースの種類を作成する場合、テンプレート内のリソースをデプロイできます。 この方法は、このクイックスタートでデプロイしたテンプレートで示されています。 また、リソースの種類の PUT 要求を送信することもできます。

```
PUT https://management.azure.com/subscriptions/<sub-id>/resourceGroups/<rg-name>/providers/Microsoft.CustomProviders/resourceProviders/<provider-name>/users/<resource-name>?api-version=2018-09-01-preview

{"properties":{"FullName": "Test User", "Location": "Earth"}}
```

ARMClient の場合は、以下を使用します。

```powershell
$addURI = "https://management.azure.com/subscriptions/$subID/resourceGroups/$rgName/providers/Microsoft.CustomProviders/resourceProviders/$funcName/users/testuser?api-version=2018-09-01-preview"
$requestBody = "{'properties':{'FullName': 'Test User', 'Location': 'Earth'}}"

armclient PUT $addURI $requestBody
```

応答を受信します。

```json
{
  "properties": {
    "provisioningState": "Succeeded",
    "FullName": "Test User",
    "Location": "Earth"
  },
  "id": "/subscriptions/<sub-ID>/resourceGroups/<rg-name>/providers/Microsoft.CustomProviders/resourceProviders/<provider-name>/users/testuser",
  "name": "testuser",
  "type": "Microsoft.CustomProviders/resourceProviders/users"
}
```

## <a name="next-steps"></a>次の手順

カスタム プロバイダーの概要については、「[Azure Custom Providers プレビューの概要](custom-providers-overview.md)」を参照してください。
