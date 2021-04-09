---
title: リソース プロバイダーを作成する
description: リソース プロバイダーを作成し、そのカスタム リソースの種類をデプロイする方法について説明します。
author: MSEvanhi
ms.topic: tutorial
ms.date: 06/24/2020
ms.author: evanhi
ms.openlocfilehash: 23ae69f49d37a210dd9fa1bfeedeca6c1b461e20
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "94888863"
---
# <a name="quickstart-create-a-custom-provider-and-deploy-custom-resources"></a>クイック スタート:カスタム プロバイダーの作成とカスタム リソースのデプロイ

このクイックスタートでは、独自のリソース プロバイダーを作成し、そのリソース プロバイダーのカスタム リソースの種類をデプロイします。 カスタム プロバイダーの詳細については、「[Azure Custom Providers プレビューの概要](overview.md)」を参照してください。

## <a name="prerequisites"></a>前提条件

- Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) を作成してください。
- このクイックスタートの手順を完了するには、`REST` 操作を呼び出す必要があります。 [REST 要求を送信するさまざまな方法](/rest/api/azure/)があります。

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Azure CLI の環境を準備します。

[!INCLUDE [azure-cli-prepare-your-environment-no-header.md](../../../includes/azure-cli-prepare-your-environment-no-header.md)]

Azure CLI の例では、`REST` 要求に `az rest` を使用しています。 詳細については、「[az rest](/cli/azure/reference-index#az-rest)」を参照してください。

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

- PowerShell コマンドは、PowerShell 7 以降と Azure PowerShell モジュールを使用してローカルで実行します。 詳しくは、[Azure PowerShell のインストール](/powershell/azure/install-az-ps)に関する記事をご覧ください。
- `REST` 操作用のツールがまだない場合は、[ARMClient](https://github.com/projectkudu/ARMClient) をインストールしてください。 これは、Azure Resource Manager API の呼び出しを簡略化する、オープン ソースのコマンドライン ツールです。
- **ARMClient** のインストール後は、PowerShell コマンド プロンプトから「`armclient.exe`」と入力することによって、使用法についての情報を表示できます。 または、[ARMClient wiki](https://github.com/projectkudu/ARMClient/wiki) にアクセスしてください。

---

## <a name="deploy-custom-provider"></a>カスタム プロバイダーをデプロイする

カスタム プロバイダーを設定するには、お使いの Azure サブスクリプションに[テンプレートの例](https://github.com/Azure/azure-docs-json-samples/blob/master/custom-providers/customprovider.json)をデプロイします。

テンプレートをデプロイすると、お使いのサブスクリプションに次のリソースが作成されます。

- リソースおよびアクションの操作が含まれている関数アプリ。
- カスタム プロバイダーを通じて作成されたユーザーを格納するためのストレージ アカウント。
- カスタム リソースの種類とアクションを定義するカスタム プロバイダー。 これは、要求を送信するために、関数アプリ エンドポイントを使用します。
- カスタム プロバイダーからのカスタム リソース。

カスタム プロバイダーをデプロイするには、Azure CLI、PowerShell、または Azure portal を使用します。

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

この例では、リソース グループ、場所、プロバイダーの関数アプリ名を入力するように求められます。 これらの名前は変数に格納され、他のコマンドで使用されます。 リソースは、[az group create](/cli/azure/group#az-group-create) コマンドと [az deployment group create](/cli/azure/deployment/group#az-deployment-group-create) コマンドでデプロイします。

```azurecli-interactive
read -p "Enter a resource group name:" rgName &&
read -p "Enter the location (i.e. eastus):" location &&
read -p "Enter the provider's function app name:" funcName &&
templateUri="https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/custom-providers/customprovider.json" &&
az group create --name $rgName --location "$location" &&
az deployment group create --resource-group $rgName --template-uri $templateUri --parameters funcName=$funcName &&
echo "Press [ENTER] to continue ..." &&
read
```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

この例では、リソース グループ、場所、プロバイダーの関数アプリ名を入力するように求められます。 これらの名前は変数に格納され、他のコマンドで使用されます。 [az group create](/powershell/module/az.resources/new-azresourcegroup) コマンドと [az deployment group create](/powershell/module/az.resources/new-azresourcegroupdeployment) コマンドによってリソースがデプロイされます。

```powershell
$rgName = Read-Host -Prompt "Enter a resource group name"
$location = Read-Host -Prompt "Enter the location (i.e. eastus)"
$funcName = Read-Host -Prompt "Enter the provider's function app name"
$templateUri = "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/custom-providers/customprovider.json"
New-AzResourceGroup -Name $rgName -Location "$location"
New-AzResourceGroupDeployment -ResourceGroupName $rgName -TemplateUri $templateUri -funcName $funcName
Read-Host -Prompt "Press [ENTER] to continue ..."
```

---

Azure portal からソリューションをデプロイすることもできます。 **[Azure へのデプロイ]** ボタンを選択すると、Azure portal でテンプレートが表示されます。

[![Azure へのデプロイ](../../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-docs-json-samples%2Fmaster%2Fcustom-providers%2Fcustomprovider.json)

## <a name="view-custom-provider-and-resource"></a>リソース プロバイダーとリソースを確認する

ポータル内では、カスタム プロバイダーは非表示のリソースの種類です。 リソース プロバイダーがデプロイされたことを確認するには、リソース グループに移動します。 **[非表示の型の表示]** オプションを選択します。

![非表示のリソースの種類を表示する](./media/create-custom-provider/show-hidden.png)

デプロイしたカスタム リソースの種類を表示するには、お使いのリソースの種類に対して `GET` 操作を使用します。

```http
GET https://management.azure.com/subscriptions/<sub-id>/resourceGroups/<rg-name>/providers/Microsoft.CustomProviders/resourceProviders/<provider-name>/users?api-version=2018-09-01-preview
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli-interactive
subID=$(az account show --query id --output tsv)
requestURI="https://management.azure.com/subscriptions/$subID/resourceGroups/$rgName/providers/Microsoft.CustomProviders/resourceProviders/$funcName/users?api-version=2018-09-01-preview"
az rest --method get --uri $requestURI
```

応答を受信します。

```json
{
  "value": [
    {
      "id": "/subscriptions/<sub-id>/resourceGroups/<rg-name>/providers/Microsoft.CustomProviders/resourceProviders/<provider-name>/users/santa",
      "name": "santa",
      "properties": {
        "FullName": "Santa Claus",
        "Location": "NorthPole",
        "provisioningState": "Succeeded"
      },
      "resourceGroup": "<rg-name>",
      "type": "Microsoft.CustomProviders/resourceProviders/users"
    }
  ]
}
```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

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

---

## <a name="call-action"></a>アクションを呼び出す

お使いのカスタム プロバイダーには、`ping` という名前のアクションがあります。 要求を処理するコードは、関数アプリ内に実装されています。 `ping` アクションは、応答メッセージで応答します。

`ping` 要求を送信するには、お使いのカスタム プロバイダーに対して `POST` 操作を使用します。

```http
POST https://management.azure.com/subscriptions/<sub-id>/resourceGroups/<rg-name>/providers/Microsoft.CustomProviders/resourceProviders/<provider-name>/ping?api-version=2018-09-01-preview
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli-interactive
pingURI="https://management.azure.com/subscriptions/$subID/resourceGroups/$rgName/providers/Microsoft.CustomProviders/resourceProviders/$funcName/ping?api-version=2018-09-01-preview"
az rest --method post --uri $pingURI
```

応答を受信します。

```json
{
  "message": "hello <function-name>.azurewebsites.net",
  "pingcontent": {
    "source": "<function-name>.azurewebsites.net"
  }
}
```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

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

---

## <a name="create-a-resource-type"></a>リソースの種類の作成

カスタム リソースの種類を作成する場合、テンプレート内のリソースをデプロイできます。 この方法は、このクイックスタートでデプロイしたテンプレートで示されています。 また、リソースの種類の `PUT` 要求を送信することもできます。

```http
PUT https://management.azure.com/subscriptions/<sub-id>/resourceGroups/<rg-name>/providers/Microsoft.CustomProviders/resourceProviders/<provider-name>/users/<resource-name>?api-version=2018-09-01-preview

{"properties":{"FullName": "Test User", "Location": "Earth"}}
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli-interactive
addURI="https://management.azure.com/subscriptions/$subID/resourceGroups/$rgName/providers/Microsoft.CustomProviders/resourceProviders/$funcName/users/testuser?api-version=2018-09-01-preview"
az rest --method put --uri $addURI --body "{'properties':{'FullName': 'Test User', 'Location': 'Earth'}}"
```

応答を受信します。

```json
{
  "id": "/subscriptions/<sub-ID>/resourceGroups/<rg-name>/providers/Microsoft.CustomProviders/resourceProviders/<provider-name>/users/testuser",
  "name": "testuser",
  "properties": {
    "FullName": "Test User",
    "Location": "Earth",
    "provisioningState": "Succeeded"
  },
  "resourceGroup": "<rg-name>",
  "type": "Microsoft.CustomProviders/resourceProviders/users"
}
```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

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

---

## <a name="custom-resource-provider-commands"></a>カスタム リソース プロバイダーのコマンド

カスタム リソース プロバイダーを操作するには、[custom-providers](/cli/azure/ext/custom-providers/custom-providers/resource-provider) コマンドを使用します。

### <a name="list-custom-resource-providers"></a>カスタム リソース プロバイダーの一覧表示

サブスクリプション内のカスタム リソースプロバイダーをすべて表示するには、`list` コマンドを使用します。 既定では、現在のサブスクリプションのカスタム リソースプロバイダーが一覧表示されますが、`--subscription` パラメーターを指定することもできます。 リソース グループに対して一覧表示するには、`--resource-group` パラメーターを使用します。

```azurecli-interactive
az custom-providers resource-provider list --subscription $subID
```

```json
[
  {
    "actions": [
      {
        "endpoint": "https://<provider-name>.azurewebsites.net/api/{requestPath}",
        "name": "ping",
        "routingType": "Proxy"
      }
    ],
    "id": "/subscriptions/<sub-id>/resourceGroups/<rg-name>/providers/Microsoft.CustomProviders/resourceproviders/<provider-name>",
    "location": "eastus",
    "name": "<provider-name>",
    "provisioningState": "Succeeded",
    "resourceGroup": "<rg-name>",
    "resourceTypes": [
      {
        "endpoint": "https://<provider-name>.azurewebsites.net/api/{requestPath}",
        "name": "users",
        "routingType": "Proxy, Cache"
      }
    ],
    "tags": {},
    "type": "Microsoft.CustomProviders/resourceproviders",
    "validations": null
  }
]
```

### <a name="show-the-properties"></a>プロパティの表示

カスタム リソースプロバイダーのプロパティを表示するには、`show` コマンドを使用します。 出力形式は `list` の出力に似ています。

```azurecli-interactive
az custom-providers resource-provider show --resource-group $rgName --name $funcName
```

### <a name="create-a-new-resource"></a>新しいリソースを作成

カスタム リソース プロバイダーを作成または更新するには、`create` コマンドを使用します。 この例では、`actions` と `resourceTypes` を更新します。

```azurecli-interactive
az custom-providers resource-provider create --resource-group $rgName --name $funcName \
--action name=ping endpoint=https://myTestSite.azurewebsites.net/api/{requestPath} routing_type=Proxy \
--resource-type name=users endpoint=https://myTestSite.azurewebsites.net/api{requestPath} routing_type="Proxy, Cache"
```

```json
"actions": [
  {
    "endpoint": "https://myTestSite.azurewebsites.net/api/{requestPath}",
    "name": "ping",
    "routingType": "Proxy"
  }
],

"resourceTypes": [
  {
    "endpoint": "https://myTestSite.azurewebsites.net/api{requestPath}",
    "name": "users",
    "routingType": "Proxy, Cache"
  }
],
```

### <a name="update-the-providers-tags"></a>プロバイダーのタグの更新

`update` コマンドでは、カスタム リソース プロバイダーのタグのみを更新します。 Azure portal では、カスタム リソース プロバイダーの App Service にタグが表示されます。

```azurecli-interactive
az custom-providers resource-provider update --resource-group $rgName --name $funcName --tags new=tag
```

```json
"tags": {
  "new": "tag"
},
```

### <a name="delete-a-custom-resource-provider"></a>カスタム リソース プロバイダーの削除

`delete` コマンドでは、プロンプトを表示し、カスタム リソース プロバイダーのみを削除します。 ストレージ アカウント、App Service、App Service プランは削除されません。 プロバイダーが削除されると、コマンド プロンプトに戻ります。

```azurecli-interactive
az custom-providers resource-provider delete --resource-group $rgName --name $funcName
```

## <a name="next-steps"></a>次のステップ

カスタム プロバイダーの概要については、次の記事をご覧ください。

> [!div class="nextstepaction"]
> [Azure Custom Providers プレビューの概要](overview.md)
