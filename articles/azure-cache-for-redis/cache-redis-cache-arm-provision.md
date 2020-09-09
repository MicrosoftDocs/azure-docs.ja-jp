---
title: Azure Resource Manager を使用して Azure Cache for Redis をデプロイする
description: Azure Resource Manager テンプレートを使用して Azure Cache for Redis をデプロイする方法について説明します。 テンプレートは、一般的なシナリオ向けに用意されています。
author: yegu-ms
ms.author: yegu
ms.service: cache
ms.topic: conceptual
ms.date: 01/23/2017
ms.openlocfilehash: 2d00a6b7753a61bb2527a56231b2fe054736f1b0
ms.sourcegitcommit: 98854e3bd1ab04ce42816cae1892ed0caeedf461
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/07/2020
ms.locfileid: "88008578"
---
# <a name="create-an-azure-cache-for-redis-using-a-template"></a>テンプレートを使用して Azure Cache for Redis を作成する

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

このトピックでは、Azure Cache for Redis をデプロイする Azure Resource Manager テンプレートを作成する方法について説明します。 キャッシュを既存のストレージ アカウントで使用することで、診断データを保持することができます。 さらに、デプロイ対象のリソースを定義する方法と、デプロイの実行時に指定されるパラメーターを定義する方法についても説明します。 このテンプレートは、独自のデプロイに使用することも、要件に合わせてカスタマイズすることもできます。

現時点では、診断設定は、サブスクリプションの同じリージョン内のすべてのキャッシュで共有されます。 領域内の 1 つのキャッシュを更新すると、領域内の他のすべてのキャッシュに反映されます。

テンプレートの作成の詳細については、「 [Authoring Azure Resource Manager Templates (Azure リソース マネージャー テンプレートのオーサリング)](../azure-resource-manager/templates/template-syntax.md)」を参照してください。 キャッシュ リソースの種類の JSON 構文とプロパティについては、「[Microsoft.Cache resource types (Microsoft.Cache リソースの種類)](/azure/templates/microsoft.cache/allversions)」を参照してください。

完成したテンプレートについては、[Azure Cache for Redis テンプレート](https://github.com/Azure/azure-quickstart-templates/blob/master/101-redis-cache/azuredeploy.json)のページを参照してください。

> [!NOTE]
> 新しい [Premium レベル](cache-overview.md#service-tiers) の Resource Manager テンプレートを利用できます。 
> 
> * [クラスタリングを使用する Premium Azure Cache for Redis の作成](https://azure.microsoft.com/resources/templates/201-redis-premium-cluster-diagnostics/)
> * [データの永続化を使用する Premium Azure Cache for Redis の作成](https://azure.microsoft.com/resources/templates/201-redis-premium-persistence/)
> * [仮想ネットワークにデプロイされる Premium Redis Cache の作成](https://azure.microsoft.com/resources/templates/201-redis-premium-vnet/)
> 
> 最新のテンプレートを確認する場合は、「[Azure クイックスタート テンプレート](https://azure.microsoft.com/documentation/templates/)」で `Azure Cache for Redis` を検索してください。
> 
> 

## <a name="what-you-will-deploy"></a>デプロイ対象
このテンプレートでは、診断データ用に既存のストレージ アカウントを使用する Azure Cache for Redis をデプロイします。

デプロイメントを自動的に実行するには、次のボタンをクリックします。

[![Azure へのデプロイ](./media/cache-redis-cache-arm-provision/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-redis-cache%2Fazuredeploy.json)

## <a name="parameters"></a>パラメーター
Azure リソース マネージャーを使用して、テンプレートのデプロイ時に値を指定するパラメーターを定義します。 テンプレートには、すべてのパラメーター値を含む Parameters という名前のセクションがあります。
これらの値のパラメーターを定義する必要があります。これらの値は、デプロイするプロジェクトやデプロイ先の環境に応じて異なります。 常に同じ値に対してはパラメーターを定義しないでください。 テンプレート内のそれぞれのパラメーターの値は、デプロイされるリソースを定義するために使用されます。 

[!INCLUDE [app-service-web-deploy-redis-parameters](../../includes/cache-deploy-parameters.md)]

### <a name="rediscachelocation"></a>redisCacheLocation
Azure Cache for Redis の場所です。 最適なパフォーマンスのために、アプリケーションとキャッシュで同じ場所を使用することをお勧めします。

```json
  "redisCacheLocation": {
    "type": "string"
  }
```

### <a name="existingdiagnosticsstorageaccountname"></a>existingDiagnosticsStorageAccountName
診断用に使用する既存のストレージ アカウントの名前。 

```json
  "existingDiagnosticsStorageAccountName": {
    "type": "string"
  }
```

### <a name="enablenonsslport"></a>enableNonSslPort
非 SSL ポート経由のアクセスを許可するかどうかを示すブール値です。

```json
  "enableNonSslPort": {
    "type": "bool"
  }
```

### <a name="diagnosticsstatus"></a>diagnosticsStatus
診断が有効かどうかを示す値です。 ON または OFF を使用します。

```json
  "diagnosticsStatus": {
    "type": "string",
    "defaultValue": "ON",
    "allowedValues": [
          "ON",
          "OFF"
      ]
  }
```

## <a name="resources-to-deploy"></a>デプロイ対象のリソース
### <a name="azure-cache-for-redis"></a>Azure Cache for Redis
Azure Cache for Redis を作成します。

```json
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
        "apiVersion": "2017-05-01-preview",
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
```

## <a name="commands-to-run-deployment"></a>デプロイを実行するコマンド
[!INCLUDE [app-service-deploy-commands](../../includes/app-service-deploy-commands.md)]

### <a name="powershell"></a>PowerShell

```azurepowershell
    New-AzResourceGroupDeployment -TemplateUri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-redis-cache/azuredeploy.json -ResourceGroupName ExampleDeployGroup -redisCacheName ExampleCache
```

### <a name="azure-cli"></a>Azure CLI

```azurecli
    azure group deployment create --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-redis-cache/azuredeploy.json -g ExampleDeployGroup
```
