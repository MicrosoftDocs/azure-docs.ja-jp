---
title: Azure Resource Manager テンプレートを使用してお使いの環境を管理する - Azure Time Series Insights | Microsoft Docs
description: Azure Resource Manager を使用してお使いの Azure Time Series Insights 環境をプログラムによって管理する方法について説明します。
ms.service: time-series-insights
services: time-series-insights
author: deepakpalled
ms.author: dpalled
manager: cshankar
ms.devlang: csharp
ms.workload: big-data
ms.topic: conceptual
ms.date: 12/06/2019
ms.custom: seodec18
ms.openlocfilehash: e5e04716ae67df94428a678f6572738d4ad46232
ms.sourcegitcommit: 375b70d5f12fffbe7b6422512de445bad380fe1e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/06/2019
ms.locfileid: "74901457"
---
# <a name="create-time-series-insights-resources-using-azure-resource-manager-templates"></a>Azure Resource Manager テンプレートを使用して Time Series Insights リソースを作成する

この記事では、[Azure Resource Manager テンプレート](https://docs.microsoft.com/azure/azure-resource-manager/)、PowerShell、Time Series Insights リソース プロバイダーを使用して Time Series Insights リソースを作成し、デプロイする方法について説明します。

Time Series Insights は、次のリソースをサポートしています。

   | リソース | 説明 |
   | --- | --- |
   | 環境 | Time Series Insights 環境とは、イベント ブローカーから読み取って保存したイベントを論理的にグループ化し、クエリで使用できるようにしたものです。 詳細については、「[Azure Time Series Insights 環境の計画](time-series-insights-environment-planning.md)」を参照してください。 |
   | イベント ソース | イベント ソースとは、イベント ブローカーへの接続を指します。Time Series Insights は、このイベント ブローカーからイベントを読み取って環境に取り込みます。 現在サポートされているイベント ソースは、IoT Hub とイベント ハブです。 |
   | 参照データ セット | 参照データセットは、環境内のイベントに関するメタデータを提供します。 参照データ セット内のメタデータは、受信時にイベントと結合されます。 参照データセットは、そのイベントの主要なプロパティでリソースとして定義されています。 参照データ セットを構成する実際のメタデータをアップロードまたは変更する際は、データ プレーン API を使用します。 |
   | アクセス ポリシー | アクセス ポリシーは、データ クエリの発行、環境内での参照データの操作、環境に関連付けられた保存クエリとパースペクティブの共有を実行するためのアクセス許可を付与します。 詳細については、[Azure portal を使用して Time Series Insights 環境にデータ アクセスを許可する](time-series-insights-data-access.md)方法に関するページを参照してください。 |

Resource Manager テンプレートは、リソース グループ内のリソースのインフラストラクチャと構成を定義する JSON ファイルです。 以下のドキュメントでは、テンプレート ファイルについてさらに詳しく説明しています。

- [Azure Resource Manager テンプレートのデプロイ](../azure-resource-manager/template-deployment-overview.md)
- [Resource Manager テンプレートと Azure PowerShell を使用したリソースのデプロイ](../azure-resource-manager/resource-group-template-deploy.md)
- [Microsoft.TimeSeriesInsights のリソースの種類](/azure/templates/microsoft.timeseriesinsights/allversions)

クイックスタート テンプレート [201-timeseriesinsights-environment-with-eventhub](https://github.com/Azure/azure-quickstart-templates/tree/master/201-timeseriesinsights-environment-with-eventhub) が GitHub で公開されています。 このテンプレートを使用して、Time Series Insights 環境、イベント ハブからイベントを読み取るよう構成された子イベント ソース、環境のデータへのアクセス権を付与するアクセス ポリシーを作成できます。 既存のイベント ハブが指定されていない場合は、デプロイ時に 1 つ作成されます。

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="specify-deployment-template-and-parameters"></a>デプロイ テンプレートとパラメーターを指定する

以下の手順では、Time Series Insights 環境、イベント ハブからイベントを読み取るよう構成された子イベント ソース、環境のデータへのアクセス権を付与するアクセス ポリシーを作成するための Azure Resource Manager テンプレートを、PowerShell を使用してデプロイする方法について説明します。 既存のイベント ハブが指定されていない場合は、デプロイ時に 1 つ作成されます。

1. 「[Getting started with Azure PowerShell](https://docs.microsoft.com/powershell/azure/get-started-azureps)」の手順に従って、Azure PowerShell をインストールします。

1. [201-timeseriesinsights-environment-with-eventhub](https://github.com/Azure/azure-quickstart-templates/blob/master/201-timeseriesinsights-environment-with-eventhub/azuredeploy.json) テンプレートを GitHub からクローンまたはコピーします。

   * パラメーター ファイルを作成する

     パラメーター ファイルを作成するために、[201-timeseriesinsights-environment-with-eventhub](https://github.com/Azure/azure-quickstart-templates/blob/master/201-timeseriesinsights-environment-with-eventhub/azuredeploy.parameters.json) ファイルをコピーします。

      [!code-json[deployment-parameters](~/quickstart-templates/201-timeseriesinsights-environment-with-eventhub/azuredeploy.parameters.json)]

    <div id="required-parameters"></div>

   * 必須のパラメーター

     | パラメーター | 説明 |
     | --- | --- |
     | eventHubNamespaceName | ソース イベント ハブの名前空間。 |
     | eventHubName | ソース イベント ハブの名前。 |
     | consumerGroupName | Time Series Insights サービスがイベント ハブからデータを読み取るために使用するコンシューマー グループの名前。 **注:** リソースの競合を避けるために、このコンシューマー グループは Time Series Insights サービス専用とし、他のリーダーと共有しないようにしてください。 |
     | environmentName | 環境の名前。 名前には、`<`、`>`、`%`、`&`、`:`、`\\`、`?`、`/`、およびどの制御文字も含めることができません。 それ以外の文字は使用できます。|
     | eventSourceName | イベント ソースの子リソースの名前。 名前には、`<`、`>`、`%`、`&`、`:`、`\\`、`?`、`/`、およびどの制御文字も含めることができません。 それ以外の文字は使用できます。 |

    <div id="optional-parameters"></div>

   * 省略可能なパラメーター

     | パラメーター | 説明 |
     | --- | --- |
     | existingEventHubResourceId | イベント ソースを介して Time Series Insights 環境に接続する既存のイベント ハブの省略可能なリソース ID。 **注:** テンプレートを展開するユーザーには、イベント ハブに対して listkeys 操作を実行する特権が必要です。 値が渡されない場合、新しいイベント ハブがテンプレートによって作成されます。 |
     | environmentDisplayName | ツールやユーザー インターフェイスで環境名の代わりに表示される省略可能なフレンドリ名。 |
     | environmentSkuName | SKU の名前。 詳細については、「[Time Series Insights の価格](https://azure.microsoft.com/pricing/details/time-series-insights/)」ページを参照してください。  |
     | environmentSkuCapacity | SKU のユニット容量。 詳細については、「[Time Series Insights の価格](https://azure.microsoft.com/pricing/details/time-series-insights/)」ページを参照してください。|
     | environmentDataRetentionTime | 環境のイベントにクエリを実行できる最小保持期間。 値は ISO 8601 形式で指定する必要があります (たとえば、アイテム保持ポリシーが 30 日間の場合は `P30D`)。 |
     | eventSourceDisplayName | ツールやユーザー インターフェイスでイベント ソース名の代わりに表示される省略可能なフレンドリ名。 |
     | eventSourceTimestampPropertyName | イベント ソースのタイムスタンプとして使用されるイベント プロパティ。 TimestampPropertyName に値が指定されていない場合や、null または空の文字列が指定されている場合は、イベントの作成時刻が使用されます。 |
     | eventSourceKeyName | Time Series Insights サービスがイベント ハブに接続するために使用する共有アクセス キーの名前。 |
     | accessPolicyReaderObjectIds | 環境への閲覧者アクセス権が必要な、Azure AD 内のユーザーまたはアプリケーションのオブジェクト ID の一覧。 サービス プリンシパルの objectId は、**Get-AzADUser** コマンドレットまたは **Get-AzADServicePrincipal** コマンドレットを呼び出すことで取得できます。 Azure AD グループ用のアクセス ポリシーの作成は、まだサポートされていません。 |
     | accessPolicyContributorObjectIds | 環境への共同作成者アクセス権が必要な、Azure AD 内のユーザーまたはアプリケーションのオブジェクト ID の一覧。 サービス プリンシパルの objectId は、**Get-AzADUser** コマンドレットまたは **Get-AzADServicePrincipal** コマンドレットを呼び出すことで取得できます。 Azure AD グループ用のアクセス ポリシーの作成は、まだサポートされていません。 |

   * たとえば、環境と、既存のイベント ハブからイベントを読み取るイベント ソースを作成するには、次のようなパラメーター ファイルを使用します。 このファイルによって、環境への共同作成者アクセス権を付与する 2 つのアクセス ポリシーも作成されます。

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
  
    * 詳細については、「[パラメーター](../azure-resource-manager/resource-group-template-deploy.md#parameter-files)」の記事を参照してください。

## <a name="deploy-the-quickstart-template-locally-using-powershell"></a>PowerShell を使用してクイックスタート テンプレートをローカルにデプロイする

> [!IMPORTANT]
> 以下に表示されるコマンド ライン操作は、[Az PowerShell モジュール](https://docs.microsoft.com/powershell/azure/overview)を説明しています。

1. PowerShell で Azure アカウントにログインします。

    * PowerShell プロンプトから、次のコマンドを実行します。

      ```powershell
      Connect-AzAccount
      ```

    * Azure アカウントにログオンするように求められます。 ログオンしたら、次のコマンドを実行して、使用できるサブスクリプションを確認します。

      ```powershell
      Get-AzSubscription
      ```

    * このコマンドを実行すると、使用できる Azure サブスクリプションの一覧が返されます。 現在のセッションのサブスクリプションを選択するには、次のコマンドを実行します。 `<YourSubscriptionId>` は、使用する Azure サブスクリプションの GUID に置き換えてください。

      ```powershell
      Set-AzContext -SubscriptionID <YourSubscriptionId>
      ```

1. リソース グループが存在しない場合は、新しいリソース グループを作成します。

   * 既存のリソース グループがない場合は、**New-AzResourceGroup** コマンドで新しいリソース グループを作成します。 使用するリソース グループの名前と場所を指定します。 例:

     ```powershell
     New-AzResourceGroup -Name MyDemoRG -Location "West US"
     ```

   * 成功した場合、新しいリソース グループの概要が表示されます。

     ```powershell
     ResourceGroupName : MyDemoRG
     Location          : westus
     ProvisioningState : Succeeded
     Tags              :
     ResourceId        : /subscriptions/<GUID>/resourceGroups/MyDemoRG
     ```

1. デプロイをテストします。

   * デプロイを検証するには、`Test-AzResourceGroupDeployment` コマンドレットを実行します。 デプロイをテストする場合、デプロイの実行時と同様に、必要なパラメーターを正確に指定します。

     ```powershell
     Test-AzResourceGroupDeployment -ResourceGroupName MyDemoRG -TemplateFile <path to template file>\azuredeploy.json -TemplateParameterFile <path to parameters file>\azuredeploy.parameters.json
     ```

1. デプロイを作成する

    * 新しいデプロイを作成するには、`New-AzResourceGroupDeployment` コマンドレットを実行し、プロンプトに従って必要なパラメーターを指定します。 パラメーターには、デプロイの名前、リソース グループの名前、テンプレート ファイルのパスまたは URL が含まれます。 **Mode** パラメーターを指定しない場合、**Incremental** の既定値が使用されます。 詳細については、[「増分デプロイと完全デプロイ」](../azure-resource-manager/deployment-modes.md)を参照してください。

    * 次のコマンドを実行すると、PowerShell ウィンドウに 5 つの必須パラメーターを指定するように求められます。

      ```powershell
      New-AzResourceGroupDeployment -Name MyDemoDeployment -ResourceGroupName MyDemoRG -TemplateFile <path to template file>\azuredeploy.json 
      ```

    * 代わりにパラメーター ファイルを使用するには、次のコマンドを使用します。

      ```powershell
      New-AzResourceGroupDeployment -Name MyDemoDeployment -ResourceGroupName MyDemoRG -TemplateFile <path to template file>\azuredeploy.json -TemplateParameterFile <path to parameters file>\azuredeploy.parameters.json
      ```

    * また、デプロイ コマンドレットを実行するときに、インライン パラメーターを使用することもできます。 コマンドは次のとおりです。

      ```powershell
      New-AzResourceGroupDeployment -Name MyDemoDeployment -ResourceGroupName MyDemoRG -TemplateFile <path to template file>\azuredeploy.json -parameterName "parameterValue"
      ```

    * [完全](../azure-resource-manager/deployment-modes.md)デプロイを実行するには、**Mode** パラメーターを **Complete** に設定します。

      ```powershell
      New-AzResourceGroupDeployment -Name MyDemoDeployment -Mode Complete -ResourceGroupName MyDemoRG -TemplateFile <path to template file>\azuredeploy.json
      ```

1. デプロイを検証する

    * リソースが正常にデプロイされると、デプロイの概要が PowerShell ウィンドウに表示されます。

      ```powershell
       DeploymentName          : MyDemoDeployment
       ResourceGroupName       : MyDemoRG
       ProvisioningState       : Succeeded
       Timestamp               : 10/11/2019 3:20:37 AM
       Mode                    : Incremental
       TemplateLink            :
       Parameters              :
                                 Name                                Type                       Value
                                 ==================================  =========================  ==========
                                 eventHubNewOrExisting               String                     new
                                 eventHubResourceGroup               String                     MyDemoRG
                                 eventHubNamespaceName               String                     tsiquickstartns
                                 eventHubName                        String                     tsiquickstarteh
                                 consumerGroupName                   String                     tsiquickstart
                                 environmentName                     String                     tsiquickstart
                                 environmentDisplayName              String                     tsiquickstart
                                 environmentSkuName                  String                     S1
                                 environmentSkuCapacity              Int                        1
                                 environmentDataRetentionTime        String                     P30D
                                 eventSourceName                     String                     tsiquickstart
                                 eventSourceDisplayName              String                     tsiquickstart
                                 eventSourceTimestampPropertyName    String
                                 eventSourceKeyName                  String                     manage
                                 accessPolicyReaderObjectIds         Array                      []
                                 accessPolicyContributorObjectIds    Array                      []
                                 location                            String                     westus

       Outputs                 :
                                  Name              Type                       Value
                                  ================  =========================  ==========
                                  dataAccessFQDN    String
                                  11aa1aa1-a1aa-1a1a-a11a-aa111a111a11.env.timeseries.azure.com

       DeploymentDebugLogLevel :
      ```

1. Azure Portal からクイックスタート テンプレートをデプロイする

   * GitHub のクイックスタート テンプレートのホーム ページには、 **[Deploy to Azure]\(Azure へのデプロイ\)** ボタンもあります。 このボタンをクリックすると、Azure Portal の [カスタム デプロイ] ページが開きます。 このページで、各パラメーターの値を入力したり、[[必要なパラメーター]](#required-parameters) テーブルまたは [[省略可能なパラメーター]](#optional-parameters) テーブルから選択したりできます。 設定に値を入力し、 **[購入]** ボタンをクリックすると、テンプレートのデプロイが開始されます。
    </br>
    </br>
    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F201-timeseriesinsights-environment-with-eventhub%2Fazuredeploy.json" target="_blank">
       <img src="https://azuredeploy.net/deploybutton.png"/>
    </a>

## <a name="next-steps"></a>次の手順

- REST API を使用して Time Series Insights リソースをプログラムによって 管理する方法の詳細については、「[Time Series Insights Management (Time Series Insights の管理)](https://docs.microsoft.com/rest/api/time-series-insights-management/)」を参照してください。
