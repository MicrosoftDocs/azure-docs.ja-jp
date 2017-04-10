---
title: "Web アプリと Redis Cache にプロビジョニングする"
description: "Azure リソース マネージャー テンプレートを使用し、Web アプリと Redis Cache を展開します。"
services: app-service
documentationcenter: 
author: steved0x
manager: erickson-doug
editor: 
ms.assetid: 6e99c71f-ef8e-4570-a307-e4c059e60c35
ms.service: app-service
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/06/2017
ms.author: sdanie
translationtype: Human Translation
ms.sourcegitcommit: 65385aa918222837468f88246d0527c22c677ba7
ms.openlocfilehash: 20af9a9bfa1086d13a770e4cd7cd8c58a8060b0b


---
# <a name="create-a-web-app-plus-redis-cache-using-a-template"></a>テンプレートを使用し、Web アプリと Redis Cache を作成する
このトピックでは、Azure Web アプリと Redis Cache をデプロイする Azure リソース マネージャーのテンプレートを作成する方法について説明します。 さらに、デプロイ対象のリソースを定義する方法と、デプロイの実行時に指定されるパラメーターを定義する方法について説明します。 このテンプレートは、独自のデプロイに使用することも、要件に合わせてカスタマイズすることもできます。

テンプレートの作成の詳細については、「 [Authoring Azure Resource Manager Templates (Azure リソース マネージャー テンプレートのオーサリング)](../azure-resource-manager/resource-group-authoring-templates.md)」を参照してください。

完全なテンプレートについては、「 [Web アプリと Redis Cache のテンプレート](https://github.com/Azure/azure-quickstart-templates/blob/master/201-web-app-with-redis-cache/azuredeploy.json)」を参照してください。

## <a name="what-you-will-deploy"></a>デプロイ対象
このテンプレートでは、以下をデプロイします。

* Azure Web アプリ
* Azure Redis Cache。

デプロイメントを自動的に実行するには、次のボタンをクリックします。

[![Azure へのデプロイ](./media/cache-web-app-arm-with-redis-cache-provision/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F201-web-app-with-redis-cache%2Fazuredeploy.json)

## <a name="parameters-to-specify"></a>指定するパラメーター
[!INCLUDE [app-service-web-deploy-web-parameters](../../includes/app-service-web-deploy-web-parameters.md)]

[!INCLUDE [cache-deploy-parameters](../../includes/cache-deploy-parameters.md)]

## <a name="variables-for-names"></a>名前用の変数
このテンプレートでは、リソースの名前を作成する変数を使用します。 [uniqueString](../azure-resource-manager/resource-group-template-functions.md#uniquestring) 関数を使用し、リソース グループの ID に基づく値を構築します。

    "variables": {
      "hostingPlanName": "[concat('hostingplan', uniqueString(resourceGroup().id))]",
      "webSiteName": "[concat('webSite', uniqueString(resourceGroup().id))]",
      "cacheName": "[concat('cache', uniqueString(resourceGroup().id))]"
    },


## <a name="resources-to-deploy"></a>デプロイ対象のリソース
[!INCLUDE [app-service-web-deploy-web-host](../../includes/app-service-web-deploy-web-host.md)]

### <a name="redis-cache"></a>Redis Cache
Web アプリで使用される Azure Redis Cache を作成します。 キャッシュの名前は **cacheName** 変数で指定されます。

テンプレートによって、リソース グループと同じ場所にキャッシュが作成されます。

    {
      "name": "[variables('cacheName')]",
      "type": "Microsoft.Cache/Redis",
      "location": "[resourceGroup().location]",
      "apiVersion": "2015-08-01",
      "dependsOn": [ ],
      "tags": {
        "displayName": "cache"
      },
      "properties": {
        "sku": {
          "name": "[parameters('cacheSKUName')]",
          "family": "[parameters('cacheSKUFamily')]",
          "capacity": "[parameters('cacheSKUCapacity')]"
        }
      }
    }


### <a name="web-app"></a>Web アプリ
**webSiteName** 変数で指定された名前で Web アプリを作成します。

Redis Cache との連動を可能にするアプリ設定プロパティで Web アプリが構成されることに注意してください。 このアプリ設定は展開時に指定された値に基づいて動的に作成されます。

    {
      "apiVersion": "2015-08-01",
      "name": "[variables('webSiteName')]",
      "type": "Microsoft.Web/sites",
      "location": "[resourceGroup().location]",
      "dependsOn": [
        "[concat('Microsoft.Web/serverFarms/', variables('hostingPlanName'))]",
        "[concat('Microsoft.Cache/Redis/', variables('cacheName'))]"
      ],
      "tags": {
        "[concat('hidden-related:', resourceGroup().id, '/providers/Microsoft.Web/serverfarms/', variables('hostingPlanName'))]": "empty",
        "displayName": "Website"
      },
      "properties": {
        "name": "[variables('webSiteName')]",
        "serverFarmId": "[resourceId('Microsoft.Web/serverfarms', variables('hostingPlanName'))]"
      },
      "resources": [
        {
          "apiVersion": "2015-08-01",
          "type": "config",
          "name": "appsettings",
          "dependsOn": [
            "[concat('Microsoft.Web/Sites/', variables('webSiteName'))]",
            "[concat('Microsoft.Cache/Redis/', variables('cacheName'))]"
          ],
          "properties": {
            "CacheConnection": "[concat(variables('cacheName'),'.redis.cache.windows.net,abortConnect=false,ssl=true,password=', listKeys(resourceId('Microsoft.Cache/Redis', variables('cacheName')), '2015-08-01').primaryKey)]"
          }
        }
      ]
    }

## <a name="commands-to-run-deployment"></a>デプロイを実行するコマンド
[!INCLUDE [app-service-deploy-commands](../../includes/app-service-deploy-commands.md)]

### <a name="powershell"></a>PowerShell
    New-AzureRmResourceGroupDeployment -TemplateUri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-web-app-with-redis-cache/azuredeploy.json -ResourceGroupName ExampleDeployGroup

### <a name="azure-cli"></a>Azure CLI
    azure group deployment create --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-web-app-with-redis-cache/azuredeploy.json -g ExampleDeployGroup



<!--HONumber=Feb17_HO3-->


