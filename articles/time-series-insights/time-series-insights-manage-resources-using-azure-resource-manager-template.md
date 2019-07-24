---
title: Azure Resource Manager テンプレートを使用して Azure Time Series Insights 環境を管理する方法 | Microsoft Docs
description: この記事では、Azure Resource Manager を使用して Azure Time Series Insights 環境をプログラムによって管理する方法について説明します。
ms.service: time-series-insights
services: time-series-insights
author: ashannon7
ms.author: anshan
manager: cshankar
ms.devlang: csharp
ms.workload: big-data
ms.topic: conceptual
ms.date: 12/08/2017
ms.custom: seodec18
ms.openlocfilehash: 79751dc0de8817c940355e8b64652014b1c67c35
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/18/2019
ms.locfileid: "59045902"
---
# <a name="create-time-series-insights-resources-using-azure-resource-manager-templates"></a>Azure Resource Manager テンプレートを使用して Time Series Insights リソースを作成する

この記事では、Azure Resource Manager テンプレート、PowerShell、Time Series Insights リソースプロバイダーを使用して Time Series Insights リソースを作成し、デプロイする方法について説明します。

Time Series Insights は、次のリソースをサポートしています。

   | Resource | 説明 |
   | --- | --- |
   | 環境 | Time Series Insights 環境とは、イベント ブローカーから読み取って保存したイベントを論理的にグループ化し、クエリで使用できるようにしたものです。 詳細については、「[Azure Time Series Insights 環境の計画](time-series-insights-environment-planning.md)」を参照してください。 |
   | イベント ソース | イベント ソースとは、イベント ブローカーへの接続を指します。Time Series Insights は、このイベント ブローカーからイベントを読み取って環境に取り込みます。 現在サポートされているイベント ソースは、IoT Hub とイベント ハブです。 |
   | 参照データ セット | 参照データセットは、環境内のイベントに関するメタデータを提供します。 参照データ セット内のメタデータは、受信時にイベントと結合されます。 参照データセットは、そのイベントの主要なプロパティでリソースとして定義されています。 参照データ セットを構成する実際のメタデータをアップロードまたは変更する際は、データ プレーン API を使用します。 |
   | アクセス ポリシー | アクセス ポリシーは、データ クエリの発行、環境内での参照データの操作、環境に関連付けられた保存クエリとパースペクティブの共有を実行するためのアクセス許可を付与します。 詳細については、「[Azure Portal を使用して Time Series Insights 環境にデータ アクセスを許可する](time-series-insights-data-access.md)」を参照してください。 |

Resource Manager テンプレートは、リソース グループ内のリソースのインフラストラクチャと構成を定義する JSON ファイルです。 詳細については、以下のドキュメントをご覧ください。

- [Azure Resource Manager の概要 - テンプレートのデプロイ](../azure-resource-manager/resource-group-overview.md#template-deployment)
- [Resource Manager テンプレートと Azure PowerShell を使用したリソースのデプロイ](../azure-resource-manager/resource-group-template-deploy.md)
- [Microsoft.TimeSeriesInsights のリソースの種類](/azure/templates/microsoft.timeseriesinsights/allversions)

クイックスタート テンプレート [201-timeseriesinsights-environment-with-eventhub](https://github.com/Azure/azure-quickstart-templates/tree/master/201-timeseriesinsights-environment-with-eventhub) が GitHub で公開されています。 このテンプレートを使用して、Time Series Insights 環境、イベント ハブからイベントを読み取るよう構成された子イベント ソース、環境のデータへのアクセス権を付与するアクセス ポリシーを作成できます。 既存のイベント ハブが指定されていない場合は、デプロイ時に 1 つ作成されます。


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="deploy-the-quickstart-template-locally-using-powershell"></a>PowerShell を使用してクイックスタート テンプレートをローカルにデプロイする

以下の手順では、Time Series Insights 環境、イベント ハブからイベントを読み取るよう構成された子イベント ソース、環境のデータへのアクセス権を付与するアクセス ポリシーを作成するための Azure Resource Manager テンプレートを、PowerShell を使用してデプロイする方法について説明します。 既存のイベント ハブが指定されていない場合は、デプロイ時に 1 つ作成されます。

ワークフローの概要は次のとおりです。

1. PowerShell をインストールします。
1. テンプレートとパラメーター ファイルを作成します。
1. PowerShell で Azure アカウントにログインします。
1. リソース グループが存在しない場合は、新しいリソース グループを作成します。
1. デプロイをテストします。
1. テンプレートをデプロイします。

### <a name="install-powershell"></a>PowerShell をインストールする

「[Getting started with Azure PowerShell](/powershell/azure/get-started-azureps)」の手順に従って、Azure PowerShell をインストールします。

### <a name="create-a-template"></a>テンプレートの作成

[201-timeseriesinsights-environment-with-eventhub](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-timeseriesinsights-environment-with-eventhub/azuredeploy.json) テンプレートを GitHub からクローンまたはコピーします。

### <a name="create-a-parameters-file"></a>パラメーター ファイルを作成する

パラメーター ファイルを作成するために、[201-timeseriesinsights-environment-with-eventhub](https://github.com/Azure/azure-quickstart-templates/blob/master/201-timeseriesinsights-environment-with-eventhub/azuredeploy.parameters.json) ファイルをコピーします。

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
      "eventHubNamespaceName": {
          "value": "GEN-UNIQUE"
      },
      "eventHubName": {
          "value": "GEN-UNIQUE"
      },
      "consumerGroupName": {
          "value": "GEN-UNIQUE"
      },
      "environmentName": {
        "value": "GEN-UNIQUE"
      },
      "eventSourceName": {
        "value": "GEN-UNIQUE"
      }
  }
}
```

#### <a name="required-parameters"></a>必須のパラメーター

   | パラメーター | 説明 |
   | --- | --- |
   | eventHubNamespaceName | ソース イベント ハブの名前空間。 |
   | eventHubName | ソース イベント ハブの名前。 |
   | consumerGroupName | Time Series Insights サービスがイベント ハブからデータを読み取るために使用するコンシューマー グループの名前。 **注:** リソースの競合を避けるために、このコンシューマー グループは Time Series Insights サービス専用とし、他のリーダーと共有しないようにしてください。 |
   | environmentName | 環境の名前。 名前には、<、>、%、&、:、\\、?、/ など、いかなる制御文字も含めることができません。 それ以外の文字は使用できます。|
   | eventSourceName | イベント ソースの子リソースの名前。 名前には、<、>、%、&、:、\\、?、/ など、いかなる制御文字も含めることができません。 それ以外の文字は使用できます。 |

#### <a name="optional-parameters"></a>省略可能なパラメーター

   | パラメーター | 説明 |
   | --- | --- |
   | existingEventHubResourceId | イベント ソースを介して Time Series Insights 環境に接続する既存のイベント ハブの省略可能なリソース ID。 **注:** テンプレートを展開するユーザーには、イベント ハブに対して listkeys 操作を実行する特権が必要です。 値が渡されない場合、新しいイベント ハブがテンプレートによって作成されます。 |
   | environmentDisplayName | ツールやユーザー インターフェイスで環境名の代わりに表示される省略可能なフレンドリ名。 |
   | environmentSkuName | SKU の名前。 詳細については、「[Time Series Insights の価格](https://azure.microsoft.com/pricing/details/time-series-insights/)」ページを参照してください。  |
   | environmentSkuCapacity | SKU のユニット容量。 詳細については、「[Time Series Insights の価格](https://azure.microsoft.com/pricing/details/time-series-insights/)」ページを参照してください。|
   | environmentDataRetentionTime | 環境のイベントにクエリを実行できる最小保持期間。 値は ISO 8601 形式で指定する必要があります (たとえば、アイテム保持ポリシーが 30 日間の場合は "P30D")。 |
   | eventSourceDisplayName | ツールやユーザー インターフェイスでイベント ソース名の代わりに表示される省略可能なフレンドリ名。 |
   | eventSourceTimestampPropertyName | イベント ソースのタイムスタンプとして使用されるイベント プロパティ。 TimestampPropertyName に値が指定されていない場合や、null または空の文字列が指定されている場合は、イベントの作成時刻が使用されます。 |
   | eventSourceKeyName | Time Series Insights サービスがイベント ハブに接続するために使用する共有アクセス キーの名前。 |
   | accessPolicyReaderObjectIds | 環境への閲覧者アクセス権が必要な、Azure AD 内のユーザーまたはアプリケーションのオブジェクト ID の一覧。 サービス プリンシパルの objectId は、**Get-AzADUser** コマンドレットまたは **Get-AzADServicePrincipal** コマンドレットを呼び出すことで取得できます。 Azure AD グループ用のアクセス ポリシーの作成は、まだサポートされていません。 |
   | accessPolicyContributorObjectIds | 環境への共同作成者アクセス権が必要な、Azure AD 内のユーザーまたはアプリケーションのオブジェクト ID の一覧。 サービス プリンシパルの objectId は、**Get-AzADUser** コマンドレットまたは **Get-AzADServicePrincipal** コマンドレットを呼び出すことで取得できます。 Azure AD グループ用のアクセス ポリシーの作成は、まだサポートされていません。 |

たとえば、環境と、既存のイベント ハブからイベントを読み取るイベント ソースを作成するには、次のようなパラメーター ファイルを使用します。 このファイルによって、環境への共同作成者アクセス権を付与する 2 つのアクセス ポリシーも作成されます。

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "eventHubNamespaceName": {
            "value": "tsiTemplateTestNamespace"
        },
        "eventHubName": {
            "value": "tsiTemplateTestEventHub"
        },
        "consumerGroupName": {
            "value": "tsiTemplateTestConsumerGroup"
        },
        "environmentName": {
          "value": "tsiTemplateTestEnvironment"
        },
        "eventSourceName": {
          "value": "tsiTemplateTestEventSource"
        },
        "existingEventHubResourceId": {
          "value": "/subscriptions/{yourSubscription}/resourceGroups/MyDemoRG/providers/Microsoft.EventHub/namespaces/tsiTemplateTestNamespace/eventhubs/tsiTemplateTestEventHub"
        },
        "accessPolicyContributorObjectIds": {
            "value": [
                "AGUID001-0000-0000-0000-000000000000",
                "AGUID002-0000-0000-0000-000000000000"
            ]
        }
    }
  }
```

詳細については、「[パラメーター](../azure-resource-manager/resource-group-template-deploy.md#parameter-files)」の記事を参照してください。

### <a name="log-in-to-azure-and-set-the-azure-subscription"></a>Azure にログインして Azure サブスクリプションを設定する

PowerShell プロンプトから、次のコマンドを実行します。

```powershell
Connect-AzAccount
```

Azure アカウントにログオンするように求められます。 ログオンしたら、次のコマンドを実行して、使用できるサブスクリプションを確認します。

```powershell
Get-AzSubscription
```

このコマンドを実行すると、使用できる Azure サブスクリプションの一覧が返されます。 現在のセッションのサブスクリプションを選択するには、次のコマンドを実行します。 `<YourSubscriptionId>` は、使用する Azure サブスクリプションの GUID に置き換えてください。

```powershell
Set-AzContext -SubscriptionID <YourSubscriptionId>
```

### <a name="set-the-resource-group"></a>リソース グループを設定する

既存のリソース グループがない場合は、**New-AzResourceGroup** コマンドで新しいリソース グループを作成します。 使用するリソース グループの名前と場所を指定します。 例: 

```powershell
New-AzResourceGroup -Name MyDemoRG -Location "West US"
```

成功した場合、新しいリソース グループの概要が表示されます。

```powershell
ResourceGroupName : MyDemoRG
Location          : westus
ProvisioningState : Succeeded
Tags              :
ResourceId        : /subscriptions/<GUID>/resourceGroups/MyDemoRG
```

### <a name="test-the-deployment"></a>展開をテスト

デプロイを検証するには、`Test-AzResourceGroupDeployment` コマンドレットを実行します。 デプロイをテストする場合、デプロイの実行時と同様に、必要なパラメーターを正確に指定します。

```powershell
Test-AzResourceGroupDeployment -ResourceGroupName MyDemoRG -TemplateFile <path to template file>\azuredeploy.json -TemplateParameterFile <path to parameters file>\azuredeploy.parameters.json
```

### <a name="create-the-deployment"></a>デプロイを作成する

新しいデプロイを作成するには、`New-AzResourceGroupDeployment` コマンドレットを実行し、プロンプトに従って必要なパラメーターを指定します。 パラメーターには、デプロイの名前、リソース グループの名前、テンプレート ファイルのパスまたは URL が含まれます。 **Mode** パラメーターを指定しない場合、**Incremental** の既定値が使用されます。 詳細については、[「増分デプロイと完全デプロイ」](../azure-resource-manager/deployment-modes.md)を参照してください。

次のコマンドを実行すると、PowerShell ウィンドウに 5 つの必須パラメーターを指定するように求められます。

```powershell
New-AzResourceGroupDeployment -Name MyDemoDeployment -ResourceGroupName MyDemoRG -TemplateFile <path to template file>\azuredeploy.json 
```

代わりにパラメーター ファイルを使用するには、次のコマンドを使用します。

```powershell
New-AzResourceGroupDeployment -Name MyDemoDeployment -ResourceGroupName MyDemoRG -TemplateFile <path to template file>\azuredeploy.json -TemplateParameterFile <path to parameters file>\azuredeploy.parameters.json
```

また、デプロイ コマンドレットを実行するときに、インライン パラメーターを使用することもできます。 コマンドは次のとおりです。

```powershell
New-AzResourceGroupDeployment -Name MyDemoDeployment -ResourceGroupName MyDemoRG -TemplateFile <path to template file>\azuredeploy.json -parameterName "parameterValue"
```

[完全](../azure-resource-manager/deployment-modes.md)デプロイを実行するには、**Mode** パラメーターを **Complete** に設定します。

```powershell
New-AzResourceGroupDeployment -Name MyDemoDeployment -Mode Complete -ResourceGroupName MyDemoRG -TemplateFile <path to template file>\azuredeploy.json
```

## <a name="verify-the-deployment"></a>デプロイを検証する

リソースが正常にデプロイされると、デプロイの概要が PowerShell ウィンドウに表示されます。

```powershell
DeploymentName          : azuredeploy
ResourceGroupName       : MyDemoRG
ProvisioningState       : Succeeded
Timestamp               : 12/9/2017 01:06:54
Mode                    : Incremental
TemplateLink            :
Parameters              :
                          Name             Type                       Value
                          ===============  =========================  ==========
                          eventHubNamespaceName  String               <eventHubNamespaceName>
                          eventHubName     String                     <eventHubName>
                          consumerGroupName  String                   <consumerGroupName>
                          existingEventHubResourceId  String
                          environmentName  String                     <environmentName>
                          environmentDisplayName  String
                          environmentSkuName  String                  S1
                          environmentSkuCapacity  Int                 1
                          environmentDataRetentionTime  String        P30D
                          eventSourceName  String                     <eventSourceName>
                          eventSourceDisplayName  String
                          eventSourceTimestampPropertyName  String
                          eventSourceKeyName  String                  manage
                          accessPolicyReaderObjectIds  Array          []
                          accessPolicyContributorObjectIds  Array     [
                            "AGUID001-0000-0000-0000-000000000000",
                            "AGUID002-0000-0000-0000-000000000000"
                          ]

Outputs                 :
                          Name             Type                       Value
                          ===============  =========================  ==========
                          dataAccessFQDN   String
                          <guid>.env.timeseries.azure.com
```

## <a name="deploy-the-quickstart-template-through-the-azure-portal"></a>Azure Portal からクイックスタート テンプレートをデプロイする

GitHub のクイックスタート テンプレートのホーム ページには、**[Deploy to Azure]\(Azure へのデプロイ\)** ボタンもあります。 このボタンをクリックすると、Azure Portal の [カスタム デプロイ] ページが開きます。 このページで、各パラメーターの値を入力したり、[[必要なパラメーター]](time-series-insights-manage-resources-using-azure-resource-manager-template.md#required-parameters) テーブルまたは [[省略可能なパラメーター]](time-series-insights-manage-resources-using-azure-resource-manager-template.md#optional-parameters) テーブルから選択したりできます。 設定に値を入力し、**[購入]** ボタンをクリックすると、テンプレートのデプロイが開始されます。

<a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F201-timeseriesinsights-environment-with-eventhub%2Fazuredeploy.json" target="_blank">
    <img src="https://azuredeploy.net/deploybutton.png"/>
</a>

## <a name="next-steps"></a>次の手順

- REST API を使用して Time Series Insights リソースをプログラムによって 管理する方法の詳細については、「[Time Series Insights Management (Time Series Insights の管理)](https://docs.microsoft.com/rest/api/time-series-insights-management/)」を参照してください。
