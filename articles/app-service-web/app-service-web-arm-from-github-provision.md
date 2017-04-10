---
title: "GitHub リポジトリにリンクされる Web アプリのデプロイ | Microsoft Docs"
description: "GitHub リポジトリのプロジェクトを含む Web アプリをデプロイするには、Azure Resource Manager テンプレートを使用します。"
services: app-service
documentationcenter: 
author: cephalin
manager: erikre
editor: 
ms.assetid: 32739607-85fe-43c8-a4dc-1feb46d93a4d
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/27/2016
ms.author: cephalin
translationtype: Human Translation
ms.sourcegitcommit: 0921b01bc930f633f39aba07b7899ad60bd6a234
ms.openlocfilehash: 92408d6358516cdb2cea068553757c036143e955
ms.lasthandoff: 03/01/2017


---
# <a name="deploy-a-web-app-linked-to-a-github-repository"></a>GitHub リポジトリにリンクされる Web アプリのデプロイ
このトピックでは、GitHub リポジトリ内のプロジェクトにリンクされる Web アプリをデプロイする Azure Resource Manager テンプレートを作成する方法について説明します。 デプロイ対象のリソースを定義する方法と、デプロイの実行時に指定されるパラメーターを定義する方法について説明します。 このテンプレートは、独自のデプロイに使用することも、要件に合わせてカスタマイズすることもできます。

テンプレートの作成の詳細については、「 [Authoring Azure Resource Manager Templates (Azure リソース マネージャー テンプレートのオーサリング)](../azure-resource-manager/resource-group-authoring-templates.md)」を参照してください。

完全なテンプレートについては、「 [Web App Linked to GitHub template (GitHub にリンクされる Web アプリ用のテンプレート)](https://github.com/Azure/azure-quickstart-templates/blob/master/201-web-app-github-deploy/azuredeploy.json)」を参照してください。

[!INCLUDE [app-service-web-to-api-and-mobile](../../includes/app-service-web-to-api-and-mobile.md)]

## <a name="what-you-will-deploy"></a>デプロイ対象
このテンプレートを使用して、GitHub 内のプロジェクトのコードを含む Web アプリケーションをデプロイします。

デプロイメントを自動的に実行するには、次のボタンをクリックします。

[![Azure へのデプロイ](./media/app-service-web-arm-from-github-provision/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F201-web-app-github-deploy%2Fazuredeploy.json)

## <a name="parameters"></a>パラメーター
[!INCLUDE [app-service-web-deploy-web-parameters](../../includes/app-service-web-deploy-web-parameters.md)]

### <a name="repourl"></a>repoURL
デプロイするプロジェクトを含む GitHub リポジトリの URL。 このパラメーターは既定値を含みますが、この値は、リポジトリに URL を指定する方法を示すことのみを目的としています。 この値はテンプレートのテスト時に使用できますが、テンプレートを使用する場合には、独自のリポジトリに URL を指定します。

    "repoURL": {
        "type": "string",
        "defaultValue": "https://github.com/davidebbo-test/Mvc52Application.git"
    }

### <a name="branch"></a>分岐
アプリケーションのデプロイ時に使用するリポジトリの分岐。 既定値は master ですが、デプロイするリポジトリ内のいずれかの分岐の名前を指定することもできます。

    "branch": {
        "type": "string",
        "defaultValue": "master"
    }

## <a name="resources-to-deploy"></a>デプロイ対象のリソース
[!INCLUDE [app-service-web-deploy-web-host](../../includes/app-service-web-deploy-web-host.md)]

### <a name="web-app"></a>Web アプリ
GitHub 内のプロジェクトにリンクされる Web アプリを作成します。 

**siteName** パラメーターに Web アプリの名前を、**siteLocation** パラメーターに Web アプリの場所を指定します。 テンプレートの **dependsOn** 要素に、Web アプリをサービス ホスティング プランに依存するものとして定義します。 ホスティング プランに依存するため、ホスティング プランの作成が完了するまで、Web アプリは作成されません。 **dependsOn** 要素は、デプロイの順序を指定するためにのみ使用されます。 Web アプリがホスティング プランに依存するとマークしない場合、Azure Resource Mananger は両方のリソースを同時に作成しようとします。このとき、ホスティング プランの前に Web アプリが作成された場合はエラーが発生することがあります。

また、Web アプリには **resources** セクションに定義される子リソースがあります。 この子リソースは、Web アプリと共にデプロイされるプロジェクトのソース管理を定義します。 このテンプレートでは、ソース管理は、特定の GitHub リポジトリにリンクされます。 GitHub リポジトリは、**"RepoUrl":"https://github.com/davidebbo-test/Mvc52Application.git"** コードによって定義されます。最小数のパラメーターを使用して単一のプロジェクトを繰り返しデプロイするテンプレートを作成する場合は、リポジトリの URL をハードコーディングできます。
リポジトリの URL をハードコーディングする代わりに、リポジトリの URL 用のパラメーターを追加し、その値を **RepoUrl** プロパティで使用できます。

    {
      "apiVersion": "2015-08-01",
      "name": "[parameters('siteName')]",
      "type": "Microsoft.Web/sites",
      "location": "[resourceGroup().location]",
      "dependsOn": [
        "[resourceId('Microsoft.Web/serverfarms', parameters('hostingPlanName'))]"
      ],
      "properties": {
        "serverFarmId": "[parameters('hostingPlanName')]"
      },
      "resources": [
        {
          "apiVersion": "2015-08-01",
          "name": "web",
          "type": "sourcecontrols",
          "dependsOn": [
            "[resourceId('Microsoft.Web/Sites', parameters('siteName'))]"
          ],
          "properties": {
            "RepoUrl": "[parameters('repoURL')]",
            "branch": "[parameters('branch')]",
            "IsManualIntegration": true
          }
        }
      ]
    }

## <a name="commands-to-run-deployment"></a>デプロイを実行するコマンド
[!INCLUDE [app-service-deploy-commands](../../includes/app-service-deploy-commands.md)]

### <a name="powershell"></a>PowerShell
    New-AzureRmResourceGroupDeployment -TemplateUri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-web-app-github-deploy/azuredeploy.json -siteName ExampleSite -hostingPlanName ExamplePlan -siteLocation "West US" -ResourceGroupName ExampleDeployGroup

### <a name="azure-cli"></a>Azure CLI

    azure group deployment create -g {resource-group-name} --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-web-app-github-deploy/azuredeploy.json

### <a name="azure-cli-20"></a>Azure CLI 2.0

    az group deployment create -g {resource-group-name} --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-web-app-github-deploy/azuredeploy.json --parameters '@azuredeploy.parameters.json'

> [!NOTE] 
> パラメーター JSON ファイルの内容については、[azuredeploy.parameters.json](https://github.com/Azure/azure-quickstart-templates/blob/master/201-web-app-github-deploy/azuredeploy.parameters.json) をご覧ください。
>
>


