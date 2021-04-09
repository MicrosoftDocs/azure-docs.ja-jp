---
title: 関数アプリ リソースの Azure へのデプロイを自動化する
description: 関数アプリをデプロイする Azure Resource Manager テンプレートを作成する方法について説明します。
ms.assetid: d20743e3-aab6-442c-a836-9bcea09bfd32
ms.topic: conceptual
ms.date: 04/03/2019
ms.custom: fasttrack-edit
ms.openlocfilehash: 9df4c62a65fd133c6ea8dc84e33d7c7b02d94cbf
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "99494041"
---
# <a name="automate-resource-deployment-for-your-function-app-in-azure-functions"></a>Azure Functions の関数アプリのリソース デプロイを自動化

Azure Resource Manager テンプレートを使用して、関数アプリをデプロイできます。 この記事では、これを行う際に必要なリソースとパラメーターについて説明します。 関数アプリの[トリガーとバインド](functions-triggers-bindings.md)によっては、追加のリソースのデプロイが必要になる可能性があります。

テンプレートの作成の詳細については、「 [Azure Resource Manager のテンプレートの作成](../azure-resource-manager/templates/template-syntax.md)」を参照してください。

サンプル テンプレートについては、以下を参照してください。
- [従量課金プランの関数アプリ]
- [Azure App Service プランの関数アプリ]

## <a name="required-resources"></a>必要なリソース

Azure Functions のデプロイは通常、次のリソースで構成されています。

| リソース                                                                           | 要件 | 構文とプロパティの参照                                                         |
|------------------------------------------------------------------------------------|-------------|-----------------------------------------------------------------------------------------|
| 関数アプリ                                                                     | 必須    | [Microsoft.Web/sites](/azure/templates/microsoft.web/sites)                             |
| [Azure Storage](../storage/index.yml) アカウント                                   | 必須    | [Microsoft.Storage/storageAccounts](/azure/templates/microsoft.storage/storageaccounts) |
| [Application Insights](../azure-monitor/app/app-insights-overview.md) コンポーネント | 省略可能    | [Microsoft.Insights/components](/azure/templates/microsoft.insights/components)         |
| [ホスティング プラン](./functions-scale.md)                                             | 省略可能<sup>1</sup>    | [Microsoft.Web/serverfarms](/azure/templates/microsoft.web/serverfarms)                 |

<sup>1</sup>ホスティング プランは、[Premium プラン](./functions-premium-plan.md) または [App Service プラン](../app-service/overview-hosting-plans.md)で関数アプリを実行することを選択する場合にのみ必要です。

> [!TIP]
> 必須ではありませんが、Application Insights をアプリ用に構成することを強くお勧めします。

<a name="storage"></a>
### <a name="storage-account"></a>ストレージ アカウント

関数アプリには、Azure ストレージ アカウントが必要です。 必要なのは BLOB、テーブル、キュー、およびファイルをサポートする汎用アカウントです。 詳細については、[Azure Functions ストレージ アカウント要件](storage-considerations.md#storage-account-requirements)に関するページをご覧ください。

```json
{
    "type": "Microsoft.Storage/storageAccounts",
    "name": "[variables('storageAccountName')]",
    "apiVersion": "2019-06-01",
    "location": "[resourceGroup().location]",
    "kind": "StorageV2",
    "sku": {
        "name": "[parameters('storageAccountType')]"
    }
}
```

さらに、`AzureWebJobsStorage` プロパティを、サイト構成でアプリ設定として指定する必要があります。 関数アプリで監視に Application Insights を使用していない場合は、`AzureWebJobsDashboard` もアプリ設定として指定する必要があります。

`AzureWebJobsStorage` 接続文字列は、Azure Functions ランタイムが内部キューを作成するときに使用します。  Application Insights が有効でない場合、ランタイムでは `AzureWebJobsDashboard` 接続文字列を使用して、Azure Table Storage にログを記録し、ポータルの **[監視]** タブをオンにします。

こうしたプロパティは、`siteConfig` オブジェクトの `appSettings` コレクションで指定します。

```json
"appSettings": [
    {
        "name": "AzureWebJobsStorage",
        "value": "[concat('DefaultEndpointsProtocol=https;AccountName=', variables('storageAccountName'), ';AccountKey=', listKeys(variables('storageAccountid'),'2019-06-01').keys[0].value)]"
    },
    {
        "name": "AzureWebJobsDashboard",
        "value": "[concat('DefaultEndpointsProtocol=https;AccountName=', variables('storageAccountName'), ';AccountKey=', listKeys(variables('storageAccountid'),'2019-06-01').keys[0].value)]"
    }
]
```

### <a name="application-insights"></a>Application Insights

Application Insights は、関数アプリを監視するために推奨されます。 Application Insights リソースは、タイプが **Microsoft.Insights/components**、サブタイプが **web** で定義されます。

```json
        {
            "apiVersion": "2015-05-01",
            "name": "[variables('appInsightsName')]",
            "type": "Microsoft.Insights/components",
            "kind": "web",
            "location": "[resourceGroup().location]",
            "tags": {
                "[concat('hidden-link:', resourceGroup().id, '/providers/Microsoft.Web/sites/', variables('functionAppName'))]": "Resource"
            },
            "properties": {
                "Application_Type": "web",
                "ApplicationId": "[variables('appInsightsName')]"
            }
        },
```

さらに、`APPINSIGHTS_INSTRUMENTATIONKEY` のアプリケーション設定を使用して、関数アプリにインストルメンテーション キーを提供する必要があります。 このプロパティは、`siteConfig` オブジェクト内の `appSettings` コレクションで指定されます。

```json
"appSettings": [
    {
        "name": "APPINSIGHTS_INSTRUMENTATIONKEY",
        "value": "[reference(resourceId('microsoft.insights/components/', variables('appInsightsName')), '2015-05-01').InstrumentationKey]"
    }
]
```

### <a name="hosting-plan"></a>ホスティング プラン

ホスティング プランの定義はさまざまあり、次のいずれかを指定できます。
* [従量課金プラン](#consumption) (既定)
* [Premium プラン](#premium)
* [App Service プラン](#app-service-plan)

### <a name="function-app"></a>関数アプリ

関数アプリのリソースは、タイプが **Microsoft.Web/sites**、サブタイプが **functionapp** のリソースを使用して定義されます。

```json
{
    "apiVersion": "2015-08-01",
    "type": "Microsoft.Web/sites",
    "name": "[variables('functionAppName')]",
    "location": "[resourceGroup().location]",
    "kind": "functionapp",
    "dependsOn": [
        "[resourceId('Microsoft.Storage/storageAccounts', variables('storageAccountName'))]",
        "[resourceId('Microsoft.Insights/components', variables('appInsightsName'))]"
    ]
}
```

> [!IMPORTANT]
> ホスティング プランを明示的に定義している場合は、dependsOn 配列に次の追加の項目が必要になります: `"[resourceId('Microsoft.Web/serverfarms', variables('hostingPlanName'))]"`

関数アプリには、次のアプリケーション設定を含める必要があります。

| 設定名                 | 説明                                                                               | 値の例                        |
|------------------------------|-------------------------------------------------------------------------------------------|---------------------------------------|
| AzureWebJobsStorage          | Functions ランタイムが内部キューイングのために使用するストレージ アカウントへの接続文字列 | 「[ストレージ アカウント](#storage)」を参照       |
| FUNCTIONS_EXTENSION_VERSION  | Azure Functions ランタイムのバージョン                                                | `~3`                                  |
| FUNCTIONS_WORKER_RUNTIME     | このアプリ内の関数で使用される言語スタック                                   | `dotnet`、`node`、`java`、`python`、または `powershell` |
| WEBSITE_NODE_DEFAULT_VERSION | `node` 言語スタックを使用している場合にのみ必要、使用するバージョンを指定します              | `10.14.1`                             |

これらのプロパティは、`siteConfig` プロパティ内の `appSettings` コレクションで指定されます。

```json
"properties": {
    "siteConfig": {
        "appSettings": [
            {
                "name": "AzureWebJobsStorage",
                "value": "[concat('DefaultEndpointsProtocol=https;AccountName=', variables('storageAccountName'), ';AccountKey=', listKeys(variables('storageAccountid'),'2019-06-01').keys[0].value)]"
            },
            {
                "name": "FUNCTIONS_WORKER_RUNTIME",
                "value": "node"
            },
            {
                "name": "WEBSITE_NODE_DEFAULT_VERSION",
                "value": "10.14.1"
            },
            {
                "name": "FUNCTIONS_EXTENSION_VERSION",
                "value": "~3"
            }
        ]
    }
}
```

<a name="consumption"></a>

## <a name="deploy-on-consumption-plan"></a>従量課金プランでデプロイする

従量課金プランでは、コードの実行時にコンピューティング能力を自動的に割り当て、負荷の処理の必要性に応じてスケールアウトし、コードを実行していないときはスケールインします。 アイドル状態の VM に対して支払う必要はなく、事前に容量を予約する必要もありません。 詳細については、「[Azure Functions のスケールとホスティング](consumption-plan.md)」を参照してください。

Azure Resource Manager テンプレートのサンプルについては、[従量課金プランの関数アプリ]に関するページをご覧ください。

### <a name="create-a-consumption-plan"></a>従量課金プランを作成する

従量課金プランを定義する必要はありません。 関数アプリのリソース自体を作成したときに、このプランがリージョンごとに自動的に作成または選択されます。

従量課金プランは、特殊なタイプの "serverfarm" リソースです。 Windows では、`computeMode` および `sku` プロパティに `Dynamic` 値を使用して指定できます。

```json
{  
   "type":"Microsoft.Web/serverfarms",
   "apiVersion":"2016-09-01",
   "name":"[variables('hostingPlanName')]",
   "location":"[resourceGroup().location]",
   "properties":{  
      "name":"[variables('hostingPlanName')]",
      "computeMode":"Dynamic"
   },
   "sku":{  
      "name":"Y1",
      "tier":"Dynamic",
      "size":"Y1",
      "family":"Y",
      "capacity":0
   }
}
```

> [!NOTE]
> Linux では、従量課金プランを明示的に定義できません。 これは自動的に作成されます。

従量課金プランを明示的に定義する場合は、アプリで `serverFarmId` プロパティを設定して、それがプランのリソース ID を指すようにする必要があります。 関数アプリにプランに対する `dependsOn` 設定も含まれていることを確認する必要があります。

### <a name="create-a-function-app"></a>Function App を作成する

従量課金プランで実行されている関数アプリに必要な設定は、Windows と Linux の間で異なります。 

#### <a name="windows"></a>Windows

Windows の場合、従量課金プランでは、サイト構成に追加の設定 [`WEBSITE_CONTENTAZUREFILECONNECTIONSTRING`](functions-app-settings.md#website_contentazurefileconnectionstring) が必要です。 このプロパティによって、関数アプリのコードと構成が格納されているストレージ アカウントが構成されます。

```json
{
    "apiVersion": "2016-03-01",
    "type": "Microsoft.Web/sites",
    "name": "[variables('functionAppName')]",
    "location": "[resourceGroup().location]",
    "kind": "functionapp",
    "dependsOn": [
        "[resourceId('Microsoft.Storage/storageAccounts', variables('storageAccountName'))]"
    ],
    "properties": {
        "siteConfig": {
            "appSettings": [
                {
                    "name": "AzureWebJobsStorage",
                    "value": "[concat('DefaultEndpointsProtocol=https;AccountName=', variables('storageAccountName'), ';AccountKey=', listKeys(variables('storageAccountid'),'2019-06-01').keys[0].value)]"
                },
                {
                    "name": "WEBSITE_CONTENTAZUREFILECONNECTIONSTRING",
                    "value": "[concat('DefaultEndpointsProtocol=https;AccountName=', variables('storageAccountName'), ';AccountKey=', listKeys(variables('storageAccountid'),'2019-06-01').keys[0].value)]"
                },
                {
                    "name": "FUNCTIONS_WORKER_RUNTIME",
                    "value": "node"
                },
                {
                    "name": "WEBSITE_NODE_DEFAULT_VERSION",
                    "value": "10.14.1"
                },
                {
                    "name": "FUNCTIONS_EXTENSION_VERSION",
                    "value": "~3"
                }
            ]
        }
    }
}
```

> [!IMPORTANT]
> [`WEBSITE_CONTENTSHARE`](functions-app-settings.md#website_contentshare) 設定は、サイトが最初に作成されるときに生成されるため、設定しないでください。  

#### <a name="linux"></a>Linux

Linux の場合、関数アプリは `kind` が `functionapp,linux` に設定され、`reserved` プロパティが `true` に設定されている必要があります。 

```json
{
    "apiVersion": "2016-03-01",
    "type": "Microsoft.Web/sites",
    "name": "[variables('functionAppName')]",
    "location": "[resourceGroup().location]",
    "kind": "functionapp,linux",
    "dependsOn": [
        "[resourceId('Microsoft.Storage/storageAccounts', variables('storageAccountName'))]"
    ],
    "properties": {
        "siteConfig": {
            "appSettings": [
                {
                    "name": "AzureWebJobsStorage",
                    "value": "[concat('DefaultEndpointsProtocol=https;AccountName=', variables('storageAccountName'), ';AccountKey=', listKeys(variables('storageAccountName'),'2019-06-01').keys[0].value)]"
                },
                {
                    "name": "FUNCTIONS_WORKER_RUNTIME",
                    "value": "node"
                },
                {
                    "name": "WEBSITE_NODE_DEFAULT_VERSION",
                    "value": "10.14.1"
                },
                {
                    "name": "FUNCTIONS_EXTENSION_VERSION",
                    "value": "~3"
                }
            ]
        },
        "reserved": true
    }
}
```

[`WEBSITE_CONTENTAZUREFILECONNECTIONSTRING`](functions-app-settings.md#website_contentazurefileconnectionstring) および [`WEBSITE_CONTENTSHARE`](functions-app-settings.md#website_contentshare) の設定は、Linux ではサポートされていません。

<a name="premium"></a>
## <a name="deploy-on-premium-plan"></a>Premium プランでデプロイする

Premium プランでは、従量課金プランと同じスケーリングが提供されますが、専用リソースと追加の機能が含まれています。 詳細については、「[Azure Functions の Premium プラン](./functions-premium-plan.md)」を参照してください。

### <a name="create-a-premium-plan"></a>Premium プランを作成する

Premium プランは、特殊なタイプの "serverfarm" リソースです。 これは、`sku` [説明オブジェクト](/azure/templates/microsoft.web/2018-02-01/serverfarms#skudescription-object)の `Name` プロパティ値に `EP1`、`EP2`、または `EP3` のいずれかを使用することで指定できます。

```json
{
    "type": "Microsoft.Web/serverfarms",
    "apiVersion": "2018-02-01",
    "name": "[parameters('hostingPlanName')]",
    "location": "[resourceGroup().location]",
    "properties": {
        "name": "[parameters('hostingPlanName')]",
        "workerSize": "[parameters('workerSize')]",
        "workerSizeId": "[parameters('workerSizeId')]",
        "numberOfWorkers": "[parameters('numberOfWorkers')]",
        "hostingEnvironment": "[parameters('hostingEnvironment')]",
        "maximumElasticWorkerCount": "20"
    },
    "sku": {
        "Tier": "ElasticPremium",
        "Name": "EP1"
    }
}
```

### <a name="create-a-function-app"></a>Function App を作成する

Premium プランでの関数アプリは、`serverFarmId` プロパティが、前に作成されたプランのリソース ID に設定されている必要があります。 さらに、Premium プランでは、サイト構成に追加の設定 [`WEBSITE_CONTENTAZUREFILECONNECTIONSTRING`](functions-app-settings.md#website_contentazurefileconnectionstring) が必要です。 このプロパティによって、関数アプリのコードと構成が格納されているストレージ アカウントが構成されます。

```json
{
    "apiVersion": "2016-03-01",
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
                    "name": "AzureWebJobsStorage",
                    "value": "[concat('DefaultEndpointsProtocol=https;AccountName=', variables('storageAccountName'), ';AccountKey=', listKeys(variables('storageAccountid'),'2019-06-01').keys[0].value)]"
                },
                {
                    "name": "WEBSITE_CONTENTAZUREFILECONNECTIONSTRING",
                    "value": "[concat('DefaultEndpointsProtocol=https;AccountName=', variables('storageAccountName'), ';AccountKey=', listKeys(variables('storageAccountid'),'2019-06-01').keys[0].value)]"
                },
                {
                    "name": "FUNCTIONS_WORKER_RUNTIME",
                    "value": "node"
                },
                {
                    "name": "WEBSITE_NODE_DEFAULT_VERSION",
                    "value": "10.14.1"
                },
                {
                    "name": "FUNCTIONS_EXTENSION_VERSION",
                    "value": "~3"
                }
            ]
        }
    }
}
```
> [!IMPORTANT]
> [`WEBSITE_CONTENTSHARE`](functions-app-settings.md#website_contentshare) 設定は、サイトが最初に作成されるときに生成されるため、設定しないでください。  

<a name="app-service-plan"></a>

## <a name="deploy-on-app-service-plan"></a>App Service プランでデプロイする

App Service プランでは、関数アプリは、Web アプリと同様に、Basic、Standard、および Premium SKU の専用 VM 上で実行されます。 App Service プランの仕組みの詳細については、「[Azure App Service プランの詳細な概要](../app-service/overview-hosting-plans.md)」を参照してください。

Azure Resource Manager テンプレートのサンプルについては、[Azure App Service プランの関数アプリ]に関するページをご覧ください。

### <a name="create-an-app-service-plan"></a>App Service プランを作成する

App Service プランは、"serverfarm" リソースによって定義されます。

```json
{
    "type": "Microsoft.Web/serverfarms",
    "apiVersion": "2018-02-01",
    "name": "[variables('hostingPlanName')]",
    "location": "[resourceGroup().location]",
    "sku": {
        "name": "S1",
        "tier": "Standard",
        "size": "S1",
        "family": "S",
        "capacity": 1
    }
}
```

Linux でアプリを実行するには、`kind` を `Linux` に設定することも必要です。

```json
{
    "type": "Microsoft.Web/serverfarms",
    "apiVersion": "2018-02-01",
    "name": "[variables('hostingPlanName')]",
    "location": "[resourceGroup().location]",
    "kind": "Linux",
    "sku": {
        "name": "S1",
        "tier": "Standard",
        "size": "S1",
        "family": "S",
        "capacity": 1
    }
}
```

### <a name="create-a-function-app"></a>Function App を作成する

App Service プランでの関数アプリは、`serverFarmId` プロパティが、前に作成されたプランのリソース ID に設定されている必要があります。

```json
{
    "apiVersion": "2016-03-01",
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
                    "name": "AzureWebJobsStorage",
                    "value": "[concat('DefaultEndpointsProtocol=https;AccountName=', variables('storageAccountName'), ';AccountKey=', listKeys(variables('storageAccountid'),'2019-06-01').keys[0].value)]"
                },
                {
                    "name": "FUNCTIONS_WORKER_RUNTIME",
                    "value": "node"
                },
                {
                    "name": "WEBSITE_NODE_DEFAULT_VERSION",
                    "value": "10.14.1"
                },
                {
                    "name": "FUNCTIONS_EXTENSION_VERSION",
                    "value": "~3"
                }
            ]
        }
    }
}
```

Linux アプリでは、`siteConfig` の下に `linuxFxVersion` プロパティも含める必要があります。 単にコードをデプロイしている場合、この値は、目的のランタイム スタックによって決まります (```runtime|runtimeVersion``` の形式)。

| スタック            | 値の例                                         |
|------------------|-------------------------------------------------------|
| Python           | `python|3.7`      |
| JavaScript       | `node|12`          |
| .NET             | `dotnet|3.1` |

```json
{
    "apiVersion": "2016-03-01",
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
                    "name": "AzureWebJobsStorage",
                    "value": "[concat('DefaultEndpointsProtocol=https;AccountName=', variables('storageAccountName'), ';AccountKey=', listKeys(variables('storageAccountid'),'2019-06-01').keys[0].value)]"
                },
                {
                    "name": "FUNCTIONS_WORKER_RUNTIME",
                    "value": "node"
                },
                {
                    "name": "WEBSITE_NODE_DEFAULT_VERSION",
                    "value": "10.14.1"
                },
                {
                    "name": "FUNCTIONS_EXTENSION_VERSION",
                    "value": "~3"
                }
            ],
            "linuxFxVersion": "node|12"
        }
    }
}
```

[カスタム コンテナー イメージをデプロイ](./functions-create-function-linux-custom-image.md)している場合は、それを `linuxFxVersion` で指定し、[Web App for Containers](../app-service/index.yml) のように、イメージをプルできるようにする構成を含める必要があります。 また、アプリのコンテンツはコンテナー自体で提供されるため、`WEBSITES_ENABLE_APP_SERVICE_STORAGE` を `false` に設定します。

```json
{
    "apiVersion": "2016-03-01",
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
                    "name": "AzureWebJobsStorage",
                    "value": "[concat('DefaultEndpointsProtocol=https;AccountName=', variables('storageAccountName'), ';AccountKey=', listKeys(variables('storageAccountid'),'2019-06-01').keys[0].value)]"
                },
                {
                    "name": "FUNCTIONS_WORKER_RUNTIME",
                    "value": "node"
                },
                {
                    "name": "WEBSITE_NODE_DEFAULT_VERSION",
                    "value": "10.14.1"
                },
                {
                    "name": "FUNCTIONS_EXTENSION_VERSION",
                    "value": "~3"
                },
                {
                    "name": "DOCKER_REGISTRY_SERVER_URL",
                    "value": "[parameters('dockerRegistryUrl')]"
                },
                {
                    "name": "DOCKER_REGISTRY_SERVER_USERNAME",
                    "value": "[parameters('dockerRegistryUsername')]"
                },
                {
                    "name": "DOCKER_REGISTRY_SERVER_PASSWORD",
                    "value": "[parameters('dockerRegistryPassword')]"
                },
                {
                    "name": "WEBSITES_ENABLE_APP_SERVICE_STORAGE",
                    "value": "false"
                }
            ],
            "linuxFxVersion": "DOCKER|myacr.azurecr.io/myimage:mytag"
        }
    }
}
```

## <a name="customizing-a-deployment"></a>デプロイのカスタマイズ

関数アプリには、アプリ設定オプション、ソース管理オプションなど、デプロイで使用できる子リソースが多数含まれます。 **sourcecontrols** 子リソースを削除して、別の [デプロイ オプション](functions-continuous-deployment.md)を代わりに使用することもできます。

> [!IMPORTANT]
> Azure Resource Manager を使用して、アプリケーションを適切にデプロイするには、リソースが Azure でどのようにデプロイされているかを理解することが重要です。 次の例では、**siteConfig** を使用して最上位レベル構成が適用されます。 この構成は、情報を Functions ランタイムとデプロイ エンジンに提供するため、最上位レベルで設定することが重要です。 **sourcecontrols/web** 子リソースが適用される前に、最上位の情報が必要です。 これらの設定は、子レベルの **config/appSettings** リソースで構成できますが、場合によっては、関数アプリを、**config/appSettings** が適用される "*前*" にデプロイする必要があります。 たとえば、[Logic Apps](../logic-apps/index.yml) で関数を使用している場合、関数は他のリソースと依存関係にあります。

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
            {
                "name": "FUNCTIONS_EXTENSION_VERSION",
                "value": "~3"
            },
            {
                "name": "Project",
                "value": "src"
            }
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
          "AzureWebJobsStorage": "[concat('DefaultEndpointsProtocol=https;AccountName=', variables('storageAccountName'), ';AccountKey=', listKeys(variables('storageAccountid'),'2019-06-01').keys[0].value)]",
          "AzureWebJobsDashboard": "[concat('DefaultEndpointsProtocol=https;AccountName=', variables('storageAccountName'), ';AccountKey=', listKeys(variables('storageAccountid'),'2019-06-01').keys[0].value)]",
          "FUNCTIONS_EXTENSION_VERSION": "~3",
          "FUNCTIONS_WORKER_RUNTIME": "dotnet",
          "Project": "src"
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

* [PowerShell](../azure-resource-manager/templates/deploy-powershell.md)
* [Azure CLI](../azure-resource-manager/templates/deploy-cli.md)
* [Azure Portal](../azure-resource-manager/templates/deploy-portal.md)
* [REST API](../azure-resource-manager/templates/deploy-rest.md)

### <a name="deploy-to-azure-button"></a>[Azure にデプロイ] ボタン

```<url-encoded-path-to-azuredeploy-json>``` を、GitHub の `azuredeploy.json` ファイルの生のパスの[エンコードされた URL](https://www.bing.com/search?q=url+encode) で置き換えます。

マークダウンを使用する例を次に示します。

```markdown
[![Deploy to Azure](https://azuredeploy.net/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/<url-encoded-path-to-azuredeploy-json>)
```

HTML を使用する例を次に示します。

```html
<a href="https://portal.azure.com/#create/Microsoft.Template/uri/<url-encoded-path-to-azuredeploy-json>" target="_blank"><img src="https://azuredeploy.net/deploybutton.png"></a>
```

### <a name="deploy-using-powershell"></a>PowerShell を使用したデプロイ

次の PowerShell コマンドを実行すると、リソース グループが作成され、必要なリソースを使って関数アプリを作成するテンプレートがデプロイされます。 ローカルで実行するには、[Azure PowerShell](/powershell/azure/install-az-ps) がインストールされている必要があります。 [`Connect-AzAccount`](/powershell/module/az.accounts/connect-azaccount) を実行してサインインします。

```powershell
# Register Resource Providers if they're not already registered
Register-AzResourceProvider -ProviderNamespace "microsoft.web"
Register-AzResourceProvider -ProviderNamespace "microsoft.storage"

# Create a resource group for the function app
New-AzResourceGroup -Name "MyResourceGroup" -Location 'West Europe'

# Create the parameters for the file, which for this template is the function app name.
$TemplateParams = @{"appName" = "<function-app-name>"}

# Deploy the template
New-AzResourceGroupDeployment -ResourceGroupName "MyResourceGroup" -TemplateFile template.json -TemplateParameterObject $TemplateParams -Verbose
```

このデプロイをテストするには、従量課金プランで Windows 上に関数アプリを作成する[このようなテンプレート](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-function-app-create-dynamic/azuredeploy.json)を使用できます。 `<function-app-name>` は、関数アプリの一意の名前に置き換えてください。

## <a name="next-steps"></a>次のステップ

Azure Functions を開発および構成する方法について学習します。

* [Azure Functions 開発者向けリファレンス](functions-reference.md)
* [Azure 関数アプリの設定を構成する方法](functions-how-to-use-azure-function-app-settings.md)
* [初めての Azure 関数の作成](./functions-get-started.md)

<!-- LINKS -->

[従量課金プランの関数アプリ]: https://github.com/Azure/azure-quickstart-templates/blob/master/101-function-app-create-dynamic/azuredeploy.json
[Azure App Service プランの関数アプリ]: https://github.com/Azure/azure-quickstart-templates/blob/master/101-function-app-create-dedicated/azuredeploy.json
