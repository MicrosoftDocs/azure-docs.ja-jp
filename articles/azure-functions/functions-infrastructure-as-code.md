---
title: "Azure Functions アプリのリソース デプロイの自動化 | Microsoft Docs"
description: "Azure Functions アプリをデプロイする Azure Resource Manager テンプレートを作成する方法について説明します。"
services: Functions
documtationcenter: na
author: mattchenderson
manager: erikre
editor: 
tags: 
keywords: "Azure Functions, 関数, サーバーレス アーキテクチャ, コードとしてのインフラストラクチャ, Azure Resource Manager"
ms.assetid: 
ms.server: functions
ms.devlang: multiple
ms.topic: 
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 01/23/2017
ms.author: cfowler;glenga
translationtype: Human Translation
ms.sourcegitcommit: 360abaa575e473e18e55d0784730f4bd5635f3eb
ms.openlocfilehash: 979537bfe6b0e14a9208871fc9862661d2fb2e6c


---

# <a name="automate-resource-deployment-for-your-azure-functions-app"></a>Azure Functions アプリのリソース デプロイの自動化

Azure Resource Manager テンプレートを使用して、Azure Functions アプリをデプロイできます。 Azure Functions アプリに必要なベースライン リソースと、デプロイ中に指定されるパラメーターを定義する方法について説明します。 アプリケーションのコードとしてのインフラストラクチャを適切に構成するには、Functions アプリの[トリガーとバインド](functions-triggers-bindings.md)に応じて、追加リソースのデプロイが必要になる可能性があります。

テンプレートの作成の詳細については、「 [Azure Resource Manager のテンプレートの作成](../azure-resource-manager/resource-group-authoring-templates.md)」を参照してください。

完全なテンプレートの例については、[重量課金プラン ベースの Azure Functions アプリの作成](https://github.com/Azure/azure-quickstart-templates/blob/052db5feeba11f85d57f170d8202123511f72044/101-function-app-create-dynamic/azuredeploy.json)や [App Service プラン ベースの Azure Functions アプリの作成](https://github.com/Azure/azure-quickstart-templates/blob/master/101-function-app-create-dedicated/azuredeploy.json)に関するページをご覧ください。

## <a name="required-resources"></a>必要なリソース

この記事の例を使用して、ベースライン Azure Functions アプリを作成することができます。 アプリには次のリソースが必要です。

* [Azure Storage](../storage/index.md) アカウント
* ホスティング プラン (従量課金プランまたは Azure App Service プラン)
* Functions アプリ (`type`: **Microsoft.Web/Site**、`kind`: **functionapp**)

## <a name="parameters"></a>parameters

Azure Resource Manager を使用して、テンプレートのデプロイ時に指定する値のパラメーターを定義できます。 テンプレートの **[パラメーター]** セクションには、すべてのパラメーター値があります。 デプロイするプロジェクトまたはデプロイ先の環境に応じて異なる値に対してパラメーターを定義します。

[変数](../azure-resource-manager/resource-group-authoring-templates.md#variables)は、(たとえば、検証規則に合格するために) 個別のデプロイに基づいて変更されない値や、テンプレートで使用する前に変換が必要なパラメーターに使用すると便利です。

パラメーターを定義するときに、**allowedValues** フィールドを使用して、デプロイ中にユーザーが指定できる値を指定します。 デプロイ中に値が指定されなかった場合のパラメーターの値は、**defaultValue** フィールドを使用して指定します。

Azure Resource Manager テンプレートでは、次のパラメーターが使用されています。

### <a name="appname"></a>appName

作成する Azure Functions アプリの名前。

```json
"appName": {
    "type": "string"
}
```

### <a name="location"></a>location

Functions アプリをデプロイする場所。

> [!NOTE]
> リソース グループの場所を継承するには、または PowerShell や CLI のデプロイ時にパラメーター値が指定されていない場合は、**defaultValue** パラメーターを使用します。 Azure Portal からアプリをデプロイする場合は、**allowedValues** パラメーターのドロップダウン ボックスで値を指定します。

> [!TIP]
> Azure Functions を使用できる最新のリージョンの一覧については、「[リージョン別の利用可能な製品](https://azure.microsoft.com/regions/services/)」を参照してください。

```json
"location": {
    "type": "string",
    "allowedValues": [
        "Brazil South",
        "East US",
        "East US 2",
        "Central US",
        "North Central US",
        "South Central US",
        "West US",
        "West US 2"
    ],
    "defaultValue": "[resourceGroup().location]"
}
```

### <a name="sourcecoderepositoryurl-optional"></a>sourceCodeRepositoryURL (省略可能)

```json
"sourceCodeRepositoryURL": {
    "type": "string",
    "defaultValue": "",
    "metadata": {
    "description": "Source code repository URL"
}
```

### <a name="sourcecodebranch-optional"></a>sourceCodeBranch (省略可能)

```json
    "sourceCodeBranch": {
      "type": "string",
      "defaultValue": "master",
      "metadata": {
        "description": "Source code repository branch"
      }
    }
```

### <a name="sourcecodemanualintegration-optional"></a>sourceCodeManualIntegration (省略可能)

```json
"sourceCodeManualIntegration": {
    "type": "bool",
    "defaultValue": false,
    "metadata": {
        "description": "Use 'true' if you are deploying from the base repo. Use 'false' if you are deploying from your own fork. If you use 'false', make sure that you have Administrator rights in the repo. If you get an error, manually add GitHub integration to another web app, to associate a GitHub access token with your Azure subscription."
    }
}
```

## <a name="variables"></a>変数

Azure Resource Manager テンプレートでは、変数を使用してパラメーターを組み込むため、より具体的な設定をテンプレートで使用することができます。

次の例では、Azure ストレージ アカウントの[名前付け要件](../storage/storage-create-storage-account.md#create-a-storage-account)に対応するために、変数を使用して [Azure Resource Manager テンプレート関数](../azure-resource-manager/resource-group-template-functions.md)を適用し、入力された **appName** 値を小文字に変換します。

```json
"variables": {
    "lowerSiteName": "[toLower(parameters('appName'))]",
    "storageAccountName": "[concat(variables('lowerSiteName'))]"
}
```

## <a name="resources-to-deploy"></a>デプロイ対象のリソース

### <a name="storage-account"></a>ストレージ アカウント

Azure ストレージ アカウントは、Azure Functions アプリに必要です。

```json
{
    "type": "Microsoft.Storage/storageAccounts",
    "name": "[variables('storageAccountName')]",
    "apiVersion": "2015-05-01-preview",
    "location": "[variables('storageLocation')]",
    "properties": {
        "accountType": "[variables('storageAccountType')]"
    }
}
```

### <a name="hosting-plan-consumption-vs-app-service"></a>ホスティング プラン: 従量課金とApp Service

シナリオによっては、プラットフォームによるオンデマンドでの関数スケーリングが必要になることがあります。これは、完全管理のスケーリングとも呼ばれます (従量課金ホスティング プランを使用)。 また、ユーザー管理の関数スケーリングを選択することもできます。 ユーザー管理のスケーリングでは、関数は専用ハードウェアで 24 時間 365 日実行されます (App Service ホスティング プランを使用)。 インスタンス数は、手動または自動で設定できます。 どのホスティング プランを選択するかは、そのプランで使用可能な機能、またはコスト別設計に基づいて判断できます。 ホスティング プランの詳細については、「[Azure Functions のスケーリング](functions-scale.md)」を参照してください。

#### <a name="consumption-plan"></a>従量課金プラン

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

#### <a name="app-service-plan"></a>App Service プラン

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

### <a name="functions-app-a-site"></a>Functions アプリ (サイト)

スケーリング オプションを選択したら、Functions アプリを作成します。 アプリは、すべての関数が保持されているコンテナーです。

Functions アプリには、アプリ設定、ソース管理オプションなど、デプロイで使用できる子リソースが多数含まれます。 **sourcecontrols** 子リソースを削除して、別の[デプロイ オプション](functions-continuous-deployment.md)を代わりに使用することもできます。

> [!IMPORTANT]
> Azure Resource Manager を使用して、アプリケーション向けの適切なコードとしてのインフラストラクチャ構成を作成するには、リソースが Azure でどのようにデプロイされているかを理解することが重要です。 次の例では、**siteConfig** を使用して最上位レベル構成が適用されます。 この構成は、情報を Azure Functions ランタイムとデプロイ エンジンに提供するため、最上位レベルで設定することが重要です。 **sourcecontrols/web** 子リソースが適用される前に、最上位の情報が必要です。 この設定は、子レベルの **config/appSettings** リソースで構成することもできますが、シナリオによっては、Functions アプリと関数を、**config/appSettings** が適用される "*前*" にデプロイする必要があります。 この場合、たとえば、[Logic Apps](../logic-apps/index.md) では、関数は他のリソースと依存関係にあります。

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
> このテンプレートでは、[プロジェクト](https://github.com/projectkudu/kudu/wiki/Customizing-deployments#using-app-settings-instead-of-a-deployment-file) アプリの設定値を使用します。これによって、関数デプロイ エンジン (Kudu) がデプロイ可能なコードを検索するベース ディレクトリを設定します。 リポジトリでは、関数は **src** フォルダーのサブフォルダーにあります。 したがって、この例では、アプリ設定値を `src` に設定します。 関数がリポジトリのルートにある場合、またはソース管理からデプロイしない場合、このアプリ設定値は削除できます。

## <a name="deploy-your-template"></a>テンプレートのデプロイ

* [PowerShell](../azure-resource-manager/resource-group-template-deploy.md)
* [Azure CLI](../azure-resource-manager/resource-group-template-deploy-cli.md)
* [Azure ポータル](../azure-resource-manager/resource-group-template-deploy-portal.md)
* [REST API](../azure-resource-manager/resource-group-template-deploy-rest.md)

### <a name="deploy-to-azure-button"></a>[Azure にデプロイ] ボタン

```<url-encoded-path-to-azuredeploy-json>``` を、GitHub の `azuredeploy.json` ファイルの生のパスの[エンコードされた URL](https://www.bing.com/search?q=url+encode) で置き換えます。

#### <a name="markdown"></a>Markdown

```markdown
[![Deploy to Azure](http://azuredeploy.net/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/<url-encoded-path-to-azuredeploy-json>)
```

#### <a name="html"></a>HTML

```html
<a href="https://portal.azure.com/#create/Microsoft.Template/uri/<url-encoded-path-to-azuredeploy-json>" target="_blank"><img src="http://azuredeploy.net/deploybutton.png"></a>
```

## <a name="next-steps"></a>次のステップ

Azure Functions を開発および構成する方法について学習します。

* [Azure Functions 開発者向けリファレンス](functions-reference.md)
* [Azure Functions App の設定の構成方法](functions-how-to-use-azure-function-app-settings.md)
* [初めての Azure 関数の作成](functions-create-first-azure-function.md)



<!--HONumber=Feb17_HO1-->


