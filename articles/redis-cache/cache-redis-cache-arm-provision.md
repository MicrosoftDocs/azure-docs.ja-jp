---
title: "Azure Resource Manager を使用した Redis Cache のプロビジョニング | Microsoft Docs"
description: "Azure リソース マネージャー テンプレートを使用し、Redis Cache をデプロイします。"
services: app-service
documentationcenter: 
author: steved0x
manager: douge
editor: 
ms.assetid: ce6f5372-7038-4655-b1c5-108f7c148282
ms.service: cache
ms.workload: web
ms.tgt_pltfrm: cache-redis
ms.devlang: na
ms.topic: article
ms.date: 01/23/2017
ms.author: sdanie
translationtype: Human Translation
ms.sourcegitcommit: c885edd7064f7ee3c9907e6b938d826e181dcae5
ms.openlocfilehash: cce5d63e8bad2dd066cb4c28e2a8a9cb16c47953


---
# <a name="create-a-redis-cache-using-a-template"></a>テンプレートを使用して Redis Cache を作成する
このトピックでは、Azure Redis Cache をデプロイする Azure Resource Manager のテンプレートを作成する方法について説明します。 キャッシュを既存のストレージ アカウントで使用することで、診断データを保持することができます。 さらに、デプロイ対象のリソースを定義する方法と、デプロイの実行時に指定されるパラメーターを定義する方法についても説明します。 このテンプレートは、独自のデプロイに使用することも、要件に合わせてカスタマイズすることもできます。

現時点では、診断設定は、サブスクリプションの同じリージョン内のすべてのキャッシュで共有されます。 領域内の&1; つのキャッシュを更新すると、領域内の他のすべてのキャッシュに反映されます。

テンプレートの作成の詳細については、「 [Authoring Azure Resource Manager Templates (Azure リソース マネージャー テンプレートのオーサリング)](../azure-resource-manager/resource-group-authoring-templates.md)」を参照してください。

完全なテンプレートについては、「 [Redis Cache のテンプレート](https://github.com/Azure/azure-quickstart-templates/blob/master/101-redis-cache/azuredeploy.json)」を参照してください。

> [!NOTE]
> 新しい [Premium レベル](cache-premium-tier-intro.md) の Resource Manager テンプレートを利用できます。 
> 
> * [クラスタリングを使用する Premium Redis Cache の作成](https://azure.microsoft.com/documentation/templates/201-redis-premium-cluster-diagnostics/)
> * [データ の永続化を使用する Premium Redis Cache の作成](https://azure.microsoft.com/documentation/templates/201-redis-premium-persistence/)
> * [VNet とオプションのクラスタリングを使用する Premium Redis Cache の作成](https://azure.microsoft.com/documentation/templates/201-redis-premium-vnet-cluster-diagnostics/)
> 
> 最新のテンプレートを確認する場合は、「[Azure クイックスタート テンプレート](https://azure.microsoft.com/documentation/templates/)」で `Redis Cache` を検索してください。
> 
> 

## <a name="what-you-will-deploy"></a>デプロイ対象
このテンプレートでは、診断データ用に既存のストレージ アカウントを使用する Azure Redis Cache をデプロイします。

デプロイメントを自動的に実行するには、次のボタンをクリックします。

[![Azure へのデプロイ](./media/cache-redis-cache-arm-provision/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-redis-cache%2Fazuredeploy.json)

## <a name="parameters"></a>パラメーター
Azure リソース マネージャーを使用して、テンプレートのデプロイ時に値を指定するパラメーターを定義します。 テンプレートには、すべてのパラメーター値を含む Parameters という名前のセクションがあります。
これらの値のパラメーターを定義する必要があります。これらの値は、デプロイするプロジェクトやデプロイ先の環境に応じて異なります。 常に同じ値に対してはパラメーターを定義しないでください。 テンプレート内のそれぞれのパラメーターの値は、デプロイされるリソースを定義するために使用されます。 

[!INCLUDE [app-service-web-deploy-redis-parameters](../../includes/cache-deploy-parameters.md)]

### <a name="rediscachelocation"></a>redisCacheLocation
Redis Cache の場所。 最適なパフォーマンスのために、アプリケーションとキャッシュで同じ場所を使用することをお勧めします。

    "redisCacheLocation": {
      "type": "string"
    }

### <a name="existingdiagnosticsstorageaccountname"></a>existingDiagnosticsStorageAccountName
診断用に使用する既存のストレージ アカウントの名前。 

    "existingDiagnosticsStorageAccountName": {
      "type": "string"
    }

### <a name="enablenonsslport"></a>enableNonSslPort
非 SSL ポート経由のアクセスを許可するかどうかを示すブール値です。

    "enableNonSslPort": {
      "type": "bool"
    }

### <a name="diagnosticsstatus"></a>diagnosticsStatus
診断が有効かどうかを示す値です。 ON または OFF を使用します。

    "diagnosticsStatus": {
      "type": "string",
      "defaultValue": "ON",
      "allowedValues": [
            "ON",
            "OFF"
        ]
    }

## <a name="resources-to-deploy"></a>デプロイ対象のリソース
### <a name="redis-cache"></a>Redis Cache
Azure Redis Cache を作成します。

    {
      "apiVersion": "2015-08-01",
      "name": "[parameters('redisCacheName')]",
      "type": "Microsoft.Cache/Redis",
      "location": "[parameters('redisCacheLocation')]",
      "properties": {
        "enableNonSslPort": "[parameters('enableNonSslPort')]",
        "sku": {
          "capacity": "[parameters('redisCacheCapacity')]",
          "family": "[parameters('redisCacheFamily')]",
          "name": "[parameters('redisCacheSKU')]"
        }
      },
      "resources": [
        {
          "apiVersion": "2015-07-01",
          "type": "Microsoft.Cache/redis/providers/diagnosticsettings",
          "name": "[concat(parameters('redisCacheName'), '/Microsoft.Insights/service')]",
          "location": "[parameters('redisCacheLocation')]",
          "dependsOn": [
            "[concat('Microsoft.Cache/Redis/', parameters('redisCacheName'))]"
          ],
          "properties": {
            "status": "[parameters('diagnosticsStatus')]",
            "storageAccountName": "[parameters('existingDiagnosticsStorageAccountName')]"
          }
        }
      ]
    }



## <a name="commands-to-run-deployment"></a>デプロイメントを実行するコマンド
[!INCLUDE [app-service-deploy-commands](../../includes/app-service-deploy-commands.md)]

### <a name="powershell"></a>PowerShell
    New-AzureRmResourceGroupDeployment -TemplateUri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-redis-cache/azuredeploy.json -ResourceGroupName ExampleDeployGroup -redisCacheName ExampleCache

### <a name="azure-cli"></a>Azure CLI
    azure group deployment create --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-redis-cache/azuredeploy.json -g ExampleDeployGroup





<!--HONumber=Jan17_HO4-->


