---
title: "Azure Functions の関数アプリのリソース デプロイを自動化 | Microsoft Docs"
description: "関数アプリをデプロイする Azure Resource Manager テンプレートを作成する方法について説明します。"
services: Functions
documtationcenter: na
author: lindydonna
manager: cfowler
editor: 
tags: 
keywords: "Azure Functions, 関数, サーバーレス アーキテクチャ, コードとしてのインフラストラクチャ, Azure Resource Manager"
ms.assetid: d20743e3-aab6-442c-a836-9bcea09bfd32
ms.server: functions
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 05/25/2017
ms.author: glenga
ms.translationtype: Human Translation
ms.sourcegitcommit: 532ff423ff53567b6ce40c0ea7ec09a689cee1e7
ms.openlocfilehash: 9458b3b619649d094ddab1638e146571d9268fb0
ms.contentlocale: ja-jp
ms.lasthandoff: 06/06/2017

---

# <a name="automate-resource-deployment-for-your-function-app-in-azure-functions"></a>Azure Functions の関数アプリのリソース デプロイを自動化

Azure Resource Manager テンプレートを使用して、関数アプリをデプロイできます。 この記事では、これを行う際に必要なリソースとパラメーターについて説明します。 関数アプリの[トリガーとバインド](functions-triggers-bindings.md)によっては、追加のリソースのデプロイが必要になる可能性があります。

テンプレートの作成の詳細については、「 [Azure Resource Manager のテンプレートの作成](../azure-resource-manager/resource-group-authoring-templates.md)」を参照してください。

サンプル テンプレートについては、以下を参照してください。
- [従量課金プランの関数アプリ]
- [Azure App Service プランの関数アプリ]

## <a name="required-resources"></a>必要なリソース

関数アプリには次のリソースが必要です。

* [Azure Storage](../storage/index.md) アカウント
* ホスティング プラン (従量課金プランまたは App Service プラン)
* 関数アプリ 

### <a name="storage-account"></a>ストレージ アカウント

関数アプリには、Azure ストレージ アカウントが必要です。 必要なのは BLOB、テーブル、キュー、およびファイルをサポートする汎用アカウントです。 詳細については、[Azure Functions ストレージ アカウント要件](functions-create-function-app-portal.md#storage-account-requirements)に関するページをご覧ください。

```json
{
    "type": "Microsoft.Storage/storageAccounts",
    "name": "[variables('storageAccountName')]",
    "apiVersion": "2015-06-15",
    "location": "[resourceGroup().location]",
    "properties": {
        "accountType": "[parameters('storageAccountType')]"
    }
}
```

さらに、`AzureWebJobsStorage` プロパティと `AzureWebJobsDashboard` プロパティを、サイト構成でアプリ設定として指定する必要があります。 `AzureWebJobsStorage` 接続文字列は、Azure Functions ランタイムが内部キューを作成するときに使用します。 接続文字列 `AzureWebJobsDashboard` を使用すると、Azure Table Storage にログを記録し、ポータルの **[モニター]** タブを操作できます。

こうしたプロパティは、`siteConfig` オブジェクトの `appSettings` コレクションで指定します。

```json
"appSettings": [
    {
        "name": "AzureWebJobsStorage",
        "value": "[concat('DefaultEndpointsProtocol=https;AccountName=', variables('storageAccountName'), ';AccountKey=', listKeys(variables('storageAccountid'),'2015-05-01-preview').key1)]"
    },
    {
        "name": "AzureWebJobsDashboard",
        "value": "[concat('DefaultEndpointsProtocol=https;AccountName=', variables('storageAccountName'), ';AccountKey=', listKeys(variables('storageAccountid'),'2015-05-01-preview').key1)]"
    }
```    

### <a name="hosting-plan"></a>ホスティング プラン

ホスティング プランの定義は、従量課金プランまたは App Service プランのどちらを使用しているかによって異なります。 「[従量課金プランで関数アプリをデプロイする](#consumption)」および「[App Service プランで関数アプリをデプロイする](#app-service-plan)」を参照してください。

### <a name="function-app"></a>関数アプリ

関数アプリのリソースは、タイプが **Microsoft.Web/Site**、サブタイプが **functionapp** のリソースを使用して定義されます。

```json
{
    "apiVersion": "2015-08-01",
    "type": "Microsoft.Web/sites",
    "name": "[variables('functionAppName')]",
    "location": "[resourceGroup().location]",
    "kind": "functionapp",            
    "dependsOn": [
        "[resourceId('Microsoft.Web/serverfarms', variables('hostingPlanName'))]",
        "[resourceId('Microsoft.Storage/storageAccounts', variables('storageAccountName'))]"
    ]
```

<a name="consumption"></a>

## <a name="deploy-a-function-app-on-the-consumption-plan"></a>従量課金プランで関数アプリをデプロイする

関数アプリは、従量課金プランと App Service プランという 2 つの異なるモードで実行できます。 従量課金プランでは、コードの実行時にコンピューティング能力を自動的に割り当て、負荷の処理の必要性に応じてスケールアウトし、コードを実行していないときはスケールダウンします。 そのため、アイドル状態の VM に対して課金されることがなく、事前に容量を予約する必要もありません。 ホスティング プランの詳細については、[Azure Functions の従量課金プランと App Service プラン](functions-scale.md)に関するページをご覧ください。

Azure Resource Manager テンプレートのサンプルについては、[従量課金プランの関数アプリ]に関するページをご覧ください。

### <a name="create-a-consumption-plan"></a>従量課金プランを作成する

従量課金プランは、特殊なタイプの "serverfarm" リソースです。 これを指定するには、`computeMode` プロパティと `sku` プロパティに `Dynamic` 値を使用します。

```json
{
    "type": "Microsoft.Web/serverfarms",
    "apiVersion": "2015-04-01",
    "name": "[variables('hostingPlanName')]",
    "location": "[resourceGroup().location]",
    "properties": {
        "name": "[variables('hostingPlanName')]",
        "computeMode": "Dynamic",
        "sku": "Dynamic"
    }
}
```

### <a name="create-a-function-app"></a>Function App を作成する

さらに、従量課金プランでは、サイト構成に `WEBSITE_CONTENTAZUREFILECONNECTIONSTRING` と `WEBSITE_CONTENTSHARE` の 2 つの追加設定が必要です。 このプロパティによって、関数アプリ コードと構成が格納されているストレージ アカウントとファイル パスが構成されます。

```json
{
    "apiVersion": "2015-08-01",
    "type": "Microsoft.Web/sites",
    "name": "[variables('functionAppName')]",
    "location": "[resourceGroup().location]",
    "kind": "functionapp",            
    "dependsOn": [
        "[resourceId('Microsoft.Web/serverfarms', variables('hostingPlanName'))]",
        "[resourceId('Microsoft.Storage/storageAccounts', variables('storageAccountName'))]"
    ],
    "properties": {
        "serverFarmId": "[resourceId('Microsoft.Web/serverfarms', variables('hostingPlanName'))]",
        "siteConfig": {
            "appSettings": [
                {
                    "name": "AzureWebJobsDashboard",
                    "value": "[concat('DefaultEndpointsProtocol=https;AccountName=', variables('storageAccountName'), ';AccountKey=', listKeys(variables('storageAccountid'),'2015-05-01-preview').key1)]"
                },
                {
                    "name": "AzureWebJobsStorage",
                    "value": "[concat('DefaultEndpointsProtocol=https;AccountName=', variables('storageAccountName'), ';AccountKey=', listKeys(variables('storageAccountid'),'2015-05-01-preview').key1)]"
                },
                {
                    "name": "WEBSITE_CONTENTAZUREFILECONNECTIONSTRING",
                    "value": "[concat('DefaultEndpointsProtocol=https;AccountName=', variables('storageAccountName'), ';AccountKey=', listKeys(variables('storageAccountid'),'2015-05-01-preview').key1)]"
                },
                {
                    "name": "WEBSITE_CONTENTSHARE",
                    "value": "[toLower(variables('functionAppName'))]"
                },
                {
                    "name": "FUNCTIONS_EXTENSION_VERSION",
                    "value": "~1"
                }
            ]
        }
    }
}
```                    

<a name="app-service-plan"></a> 

## <a name="deploy-a-function-app-on-the-app-service-plan"></a>App Service プランで関数アプリをデプロイする

App Service プランでは、関数アプリは、Web アプリと同様に、Basic、Standard、および Premium SKU の専用 VM 上で実行されます。 App Service プランの仕組みの詳細については、「[Azure App Service プランの詳細な概要](../app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md)」を参照してください。 

Azure Resource Manager テンプレートのサンプルについては、[Azure App Service プランの関数アプリ]に関するページをご覧ください。

### <a name="create-an-app-service-plan"></a>App Service プランを作成する

```json
{
    "type": "Microsoft.Web/serverfarms",
    "apiVersion": "2015-04-01",
    "name": "[variables('hostingPlanName')]",
    "location": "[resourceGroup().location]",
    "properties": {
        "name": "[variables('hostingPlanName')]",
        "sku": "[parameters('sku')]",
        "workerSize": "[parameters('workerSize')]",
        "hostingEnvironment": "",
        "numberOfWorkers": 1
    }
}
```

### <a name="create-a-function-app"></a>Function App を作成する 

拡大縮小オプションを選択したら、関数アプリを作成します。 アプリは、すべての関数が保持されているコンテナーです。

関数アプリには、アプリ設定オプション、ソース管理オプションなど、デプロイで使用できる子リソースが多数含まれます。 **sourcecontrols** 子リソースを削除して、別の[デプロイ オプション](functions-continuous-deployment.md)を代わりに使用することもできます。

> [!IMPORTANT]
> Azure Resource Manager を使用して、アプリケーションを適切にデプロイするには、リソースが Azure でどのようにデプロイされているかを理解することが重要です。 次の例では、**siteConfig** を使用して最上位レベル構成が適用されます。 この構成は、情報を Functions ランタイムとデプロイ エンジンに提供するため、最上位レベルで設定することが重要です。 **sourcecontrols/web** 子リソースが適用される前に、最上位の情報が必要です。 この設定は、子レベルの **config/appSettings** リソースで構成することもできますが、場合によっては、関数アプリを、**config/appSettings** が適用される "*前*" にデプロイする必要があります。 たとえば、[Logic Apps](../logic-apps/index.md) で関数を使用している場合、関数は他のリソースと依存関係にあります。

```json
{
  "apiVersion": "2015-08-01",
  "name": "[parameters('appName')]",
  "type": "Microsoft.Web/sites",
  "kind": "functionapp",
  "location": "[parameters('location')]",
  "dependsOn": [
    "[resourceId('Microsoft.Storage/storageAccounts', variables('storageAccountName'))]",
    "[resourceId('Microsoft.Web/serverfarms', parameters('appName'))]"
  ],
  "properties": {
     "serverFarmId": "[variables('appServicePlanName')]",
     "siteConfig": {
        "alwaysOn": true,
        "appSettings": [
            { "name": "FUNCTIONS_EXTENSION_VERSION", "value": "~1" },
            { "name": "Project", "value": "src" }
        ]
     }
  },
  "resources": [
     {
        "apiVersion": "2015-08-01",
        "name": "appsettings",
        "type": "config",
        "dependsOn": [
          "[resourceId('Microsoft.Web/Sites', parameters('appName'))]",
          "[resourceId('Microsoft.Web/Sites/sourcecontrols', parameters('appName'), 'web')]",
          "[resourceId('Microsoft.Storage/storageAccounts', variables('storageAccountName'))]"
        ],
        "properties": {
          "AzureWebJobsStorage": "[concat('DefaultEndpointsProtocol=https;AccountName=', variables('storageAccountName'), ';AccountKey=', listKeys(variables('storageAccountid'),'2015-05-01-preview').key1)]",
          "AzureWebJobsDashboard": "[concat('DefaultEndpointsProtocol=https;AccountName=', variables('storageAccountName'), ';AccountKey=', listKeys(variables('storageAccountid'),'2015-05-01-preview').key1)]"
        }
     },
     {
          "apiVersion": "2015-08-01",
          "name": "web",
          "type": "sourcecontrols",
          "dependsOn": [
            "[resourceId('Microsoft.Web/sites/', parameters('appName'))]"
          ],
          "properties": {
            "RepoUrl": "[parameters('sourceCodeRepositoryURL')]",
            "branch": "[parameters('sourceCodeBranch')]",
            "IsManualIntegration": "[parameters('sourceCodeManualIntegration')]"
          }
     }
  ]
}
```
> [!TIP]
> このテンプレートでは、[プロジェクト](https://github.com/projectkudu/kudu/wiki/Customizing-deployments#using-app-settings-instead-of-a-deployment-file) アプリの設定値を使用します。これによって、Functions デプロイ エンジン (Kudu) がデプロイ可能なコードを検索するベース ディレクトリを設定します。 リポジトリでは、関数は **src** フォルダーのサブフォルダーにあります。 したがって、この例では、アプリ設定値を `src` に設定します。 関数がリポジトリのルートにある場合、またはソース管理からデプロイしない場合、このアプリ設定値は削除できます。

## <a name="deploy-your-template"></a>テンプレートのデプロイ

次のいずれかの方法を使用して、テンプレートをデプロイできます。

* [PowerShell](../azure-resource-manager/resource-group-template-deploy.md)
* [Azure CLI](../azure-resource-manager/resource-group-template-deploy-cli.md)
* [Azure ポータル](../azure-resource-manager/resource-group-template-deploy-portal.md)
* [REST API](../azure-resource-manager/resource-group-template-deploy-rest.md)

### <a name="deploy-to-azure-button"></a>[Azure にデプロイ] ボタン

```<url-encoded-path-to-azuredeploy-json>``` を、GitHub の `azuredeploy.json` ファイルの生のパスの[エンコードされた URL](https://www.bing.com/search?q=url+encode) で置き換えます。

マークダウンを使用する例を次に示します。

```markdown
[![Deploy to Azure](http://azuredeploy.net/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/<url-encoded-path-to-azuredeploy-json>)
```

HTML を使用する例を次に示します。

```html
<a href="https://portal.azure.com/#create/Microsoft.Template/uri/<url-encoded-path-to-azuredeploy-json>" target="_blank"><img src="http://azuredeploy.net/deploybutton.png"></a>
```

## <a name="next-steps"></a>次のステップ

Azure Functions を開発および構成する方法について学習します。

* [Azure Functions 開発者向けリファレンス](functions-reference.md)
* [Azure 関数アプリの設定を構成する方法](functions-how-to-use-azure-function-app-settings.md)
* [初めての Azure 関数の作成](functions-create-first-azure-function.md)

<!-- LINKS -->

[従量課金プランの関数アプリ]: https://github.com/Azure/azure-quickstart-templates/blob/master/101-function-app-create-dynamic/azuredeploy.json
[Azure App Service プランの関数アプリ]: https://github.com/Azure/azure-quickstart-templates/blob/master/101-function-app-create-dedicated/azuredeploy.json

