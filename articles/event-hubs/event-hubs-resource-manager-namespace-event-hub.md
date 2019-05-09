---
title: コンシューマー グループを含むイベント ハブを作成する - Azure Event Hubs | Microsoft Docs
description: イベント ハブとコンシューマー グループを含んだ Event Hubs 名前空間を Azure Resource Manager テンプレートで作成する
services: event-hubs
documentationcenter: .net
author: ShubhaVijayasarathy
manager: timlt
editor: ''
ms.assetid: 28bb4591-1fd7-444f-a327-4e67e8878798
ms.service: event-hubs
ms.devlang: tbd
ms.topic: article
ms.tgt_pltfrm: dotnet
ms.workload: na
ms.date: 10/16/2018
ms.author: shvija
ms.openlocfilehash: 8664b431239f7b288deccedeadff0806ab600bcd
ms.sourcegitcommit: de81b3fe220562a25c1aa74ff3aa9bdc214ddd65
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/13/2019
ms.locfileid: "56232515"
---
# <a name="quickstart-create-an-event-hub-using-azure-resource-manager-template"></a>クイック スタート:Azure Resource Manager テンプレートを使用してイベント ハブを作成する
Azure Event Hubs はビッグ データ ストリーミング プラットフォームであり、毎秒数百万のイベントを受け取って処理できるイベント インジェスト サービスです。 Event Hubs では、分散されたソフトウェアやデバイスから生成されるイベント、データ、またはテレメトリを処理および格納できます。 イベント ハブに送信されたデータは、任意のリアルタイム分析プロバイダーやバッチ処理/ストレージ アダプターを使用して、変換および保存できます。 Event Hubs の詳しい概要については、[Event Hubs の概要](event-hubs-about.md)と [Event Hubs の機能](event-hubs-features.md)に関するページをご覧ください。

このクイック スタートでは、Azure Resource Manager テンプレートを使ってイベント ハブを作成します。 Azure Resource Manager テンプレートを使用して、イベント ハブとコンシューマー グループをそれぞれ 1 つずつ含んだ [Event Hubs](event-hubs-what-is-event-hubs.md) タイプの名前空間を作成します。 記事では、デプロイ対象のリソースを定義する方法と、デプロイの実行時に指定されるパラメーターを定義する方法を説明します。 このテンプレートは、独自のデプロイに使用することも、要件に合わせてカスタマイズすることもできます。 テンプレートの作成の詳細については、[Azure Resource Manager テンプレートのオーサリング][Authoring Azure Resource Manager templates]に関する記事をご覧ください。 テンプレートで使用する JSON の構文とプロパティについては、「[Microsoft.EventHub resource types (Microsoft.EventHub のリソースの種類)](/azure/templates/microsoft.eventhub/allversions)」を参照してください。

> [!NOTE]
> 完全なテンプレートについては、GitHub の[イベント ハブとコンシューマー グループを作成するためのテンプレート][Event Hub and consumer group template]を参照してください。 このテンプレートでは、イベント ハブの名前空間とイベント ハブだけでなく、コンシューマー グループも作成しました。 最新のテンプレートを確認する場合は、「[Azure クイックスタート テンプレート][Azure Quickstart Templates]」ギャラリーで "Event Hubs" を検索してください。

## <a name="prerequisites"></a>前提条件

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

このクイック スタートを完了するには、Azure サブスクリプションが必要です。 お持ちでない場合は、開始する前に[無料アカウントを作成](https://azure.microsoft.com/free/)してください。

**Azure PowerShell** を使用して Resource Manager テンプレートをデプロイするには、[Azure PowerShell をインストール](https://docs.microsoft.com/powershell/azure/install-az-ps)します。

**Azure CLI** を使用して Resource Manager テンプレートをデプロイするには、[Azure CLI をインストール]( /cli/azure/install-azure-cli)します。

## <a name="create-the-resource-manager-template-json"></a>Resource Manager テンプレート JSON を作成する
以下の内容の MyEventHub.json という名前の JSON ファイルを作成してフォルダー (例:C:\EventHubsQuickstarts\ResourceManagerTemplate) に保存します。

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "eventhub-namespace-name": {
            "type": "String"
        },
        "eventhub_name": {
            "type": "String"
        }
    },
    "resources": [
        {
            "type": "Microsoft.EventHub/namespaces",
            "sku": {
                "name": "Standard",
                "tier": "Standard",
                "capacity": 1
            },
            "name": "[parameters('eventhub-namespace-name')]",
            "apiVersion": "2017-04-01",
            "location": "East US",
            "tags": {},
            "scale": null,
            "properties": {
                "isAutoInflateEnabled": false,
                "maximumThroughputUnits": 0
            },
            "dependsOn": []
        },
        {
            "type": "Microsoft.EventHub/namespaces/eventhubs",
            "name": "[concat(parameters('eventhub-namespace-name'), '/', parameters('eventhub_name'))]",
            "apiVersion": "2017-04-01",
            "location": "East US",
            "scale": null,
            "properties": {
                "messageRetentionInDays": 7,
                "partitionCount": 1,
                "status": "Active"
            },
            "dependsOn": [
                "[resourceId('Microsoft.EventHub/namespaces', parameters('eventhub-namespace-name'))]"
            ]
        }
    ]
}
```

## <a name="create-the-parameters-json"></a>パラメーター JSON を作成する
Azure Resource Manager テンプレートのパラメーターを含む MyEventHub-Parameters.json という名前の JSON ファイルを作成します。 

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
        "eventhub-namespace-name": {
            "value": "<specify a name for the event hub namespace>"
        },
        "eventhub_name": {
            "value": "<Specify a name for the event hub in the namespace>"
        }
  }
}
```



## <a name="use-azure-powershell-to-deploy-the-template"></a>Azure PowerShell を使用してテンプレートをデプロイする

### <a name="sign-in-to-azure"></a>Azure へのサインイン
1. Azure PowerShell を起動します。

2. 次のコマンドを実行して、Azure にサインインします。

   ```azurepowershell
   Login-AzAccount
   ```
3. 次のコマンドを発行して、現在のサブスクリプション コンテキストを設定します。

   ```azurepowershell
   Select-AzSubscription -SubscriptionName "<YourSubscriptionName>" 
   ```

### <a name="provision-resources"></a>リソースをプロビジョニングする
Azure PowerShell を使用してリソースをデプロイ/プロビジョニングするには、C:\EventHubsQuickStart\ARM\ フォルダーに切り替え、次のコマンドを実行します。

> [!IMPORTANT]
> これらのコマンドを実行する前に、$resourceGroupName の値として Azure リソース グループの名前を指定します。 

```azurepowershell
$resourceGroupName = "<Specify a name for the Azure resource group>"

# Create an Azure resource group
New-AzResourceGroup $resourceGroupName -location 'East US'

# Deploy the Resource Manager template. Specify the names of deployment itself, resource group, JSON file for the template, JSON file for parameters
New-AzResourceGroupDeployment -Name MyARMDeployment -ResourceGroupName $resourceGroupName -TemplateFile MyEventHub.json -TemplateParameterFile MyEventHub-Parameters.json
```

## <a name="use-azure-cli-to-deploy-the-template"></a>Azure CLI を使用してテンプレートをデプロイする

## <a name="sign-in-to-azure"></a>Azure へのサインイン

Cloud Shell でコマンドを実行している場合、次の手順は必要ありません。 ローカルで CLI を実行している場合、次の手順を実行して Azure にサインインし、現在のサブスクリプションを設定します。

次のコマンドを実行して、Azure にサインインします。

```azurecli
az login
```

現在のサブスクリプションのコンテキストを設定します。 `MyAzureSub` は、使用する Azure サブスクリプションの名前に置き換えてください。

```azurecli
az account set --subscription <Name of your Azure subscription>
``` 

### <a name="provision-resources"></a>リソースをプロビジョニングする
Azure CLI を使用してリソースをデプロイするには、C:\EventHubsQuickStart\ARM\ フォルダーに移動し、次のコマンドを実行します。

> [!IMPORTANT]
> az group create コマンドで Azure リソース グループの名前を指定します 。

```azurecli
# Create an Azure resource group
az group create --name <YourResourceGroupName> --location eastus

# # Deploy the Resource Manager template. Specify the names of resource group, deployment, JSON file for the template, JSON file for parameters
az group deployment create --name <Specify a name for the deployment> --resource-group <YourResourceGroupName> --template-file MyEventHub.json --parameters @MyEventHub-Parameters.json
```

お疲れさまでした。 Azure Resource Manager テンプレートを使用して Event Hubs 名前空間を作成し、その名前空間内にイベント ハブを作成しました。

## <a name="next-steps"></a>次の手順

この記事では、Event Hubs 名前空間を作成し、サンプル アプリケーションを使用してイベント ハブからイベントを送受信しました。 イベント ハブに対してイベントを送信または受信するためのステップ バイ ステップの手順については、次のチュートリアルをご覧ください。 

- **イベントをイベント ハブに送信する**: [.NET Core](event-hubs-dotnet-standard-getstarted-send.md)、[.NET Framework](event-hubs-dotnet-framework-getstarted-send.md)、[Java](event-hubs-java-get-started-send.md)、[Python](event-hubs-python-get-started-send.md)、[Node.js](event-hubs-node-get-started-send.md)、[Go](event-hubs-go-get-started-send.md)、[C](event-hubs-c-getstarted-send.md)
- **イベント ハブからイベントを受信する**: [.NET Core](event-hubs-dotnet-standard-getstarted-receive-eph.md)、[.NET Framework](event-hubs-dotnet-framework-getstarted-receive-eph.md)、[Java](event-hubs-java-get-started-receive-eph.md)、[Python](event-hubs-python-get-started-receive.md)、[Node.js](event-hubs-node-get-started-receive.md)、[Go](event-hubs-go-get-started-receive-eph.md)、[Apache Storm](event-hubs-storm-getstarted-receive.md)

[3]: ./media/event-hubs-quickstart-powershell/sender1.png
[4]: ./media/event-hubs-quickstart-powershell/receiver1.png
[5]: ./media/event-hubs-quickstart-powershell/metrics.png

[Authoring Azure Resource Manager templates]: ../azure-resource-manager/resource-group-authoring-templates.md
[Azure Quickstart Templates]:  https://azure.microsoft.com/documentation/templates/?term=event+hubs
[Using Azure PowerShell with Azure Resource Manager]: ../powershell-azure-resource-manager.md
[Using the Azure CLI for Mac, Linux, and Windows with Azure Resource Management]: ../xplat-cli-azure-resource-manager.md
[Event hub and consumer group template]: https://github.com/Azure/azure-quickstart-templates/blob/master/201-event-hubs-create-event-hub-and-consumer-group/
