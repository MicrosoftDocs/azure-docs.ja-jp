---
title: チュートリアル - Azure PowerShell を使用して Azure IoT Hub のメッセージ ルーティングを構成する
description: チュートリアル - Azure PowerShell を使用して Azure IoT Hub のメッセージ ルーティングを構成します。 メッセージ内のプロパティに応じて、ストレージ アカウントまたは Service Bus キューのどちらかにルーティングします。
author: robinsh
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.topic: tutorial
ms.date: 03/25/2019
ms.author: robinsh
ms.custom: mvc, devx-track-azurepowershell
ms.openlocfilehash: be1560bcc03ec7a26f4bc374392c746243cd731a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "98624074"
---
# <a name="tutorial-use-azure-powershell-to-configure-iot-hub-message-routing"></a>チュートリアル:Azure PowerShell を使用して IoT Hub のメッセージ ルーティングを構成する

[!INCLUDE [iot-hub-include-routing-intro](../../includes/iot-hub-include-routing-intro.md)]

[!INCLUDE [iot-hub-include-routing-create-resources](../../includes/iot-hub-include-routing-create-resources.md)]

## <a name="download-the-script-optional"></a>スクリプトのダウンロード (任意)

このチュートリアルのパート 2 では、IoT ハブにメッセージを送信する Visual Studio アプリケーションをダウンロードして実行します。 ダウンロード内のフォルダーには、Azure Resource Manager テンプレートとパラメーター ファイルのほか、Azure CLI と PowerShell のスクリプトが含まれています。 

完成したスクリプトを確認したい場合は、[Azure IoT C# サンプル](https://github.com/Azure-Samples/azure-iot-samples-csharp/archive/master.zip)をダウンロードしてください。 master.zip ファイルを解凍します。 Azure CLI スクリプトは、**iothub_routing_psh.ps1** という名前で /iot-hub/Tutorials/Routing/SimulatedDevice/resources/ にあります。

## <a name="create-your-resources"></a>リソースの作成

最初に PowerShell を使用してリソースを作成します。

### <a name="use-powershell-to-create-your-base-resources"></a>PowerShell を使用してベース リソースを作成する

以下のスクリプトをコピーして Cloud Shell に貼り付け、Enter キーを押してください。 スクリプトが 1 行ずつ実行されます。 この最初のセクションのスクリプトでは、ストレージ アカウント、IoT ハブ、Service Bus 名前空間、Service Bus キューなど、このチュートリアルのベース リソースが作成されます。 チュートリアルを読み進めながら、各ブロックのスクリプトをコピーし、Cloud Shell に貼り付けて実行してください。

IoT ハブ名やストレージ アカウント名など、いくつかのリソース名はグローバルに一意であることが必要です。 それを簡単にするために、それらのリソース名には、*randomValue* という英数字のランダム値が追加されます。 randomValue はスクリプトの冒頭で 1 度生成され、スクリプト全体で必要に応じてリソース名に追加されます。 これをランダムにしたくない場合は、空の文字列または特定の値に設定できます。 

> [!IMPORTANT]
> 最初のスクリプトで設定された変数はルーティング スクリプトによっても使用されるため、すべてのスクリプトは、同じ Cloud Shell セッションで実行してください。 新しいセッションを開いてルーティングの設定用のスクリプトを実行した場合、いくつかの変数に値が格納されません。 
>

```azurepowershell-interactive
# This command retrieves the subscription id of the current Azure account.
# This field is used when setting up the routing queries.
$subscriptionID = (Get-AzContext).Subscription.Id

# Concatenate this number onto the resources that have to be globally unique.
# You can set this to "" or to a specific value if you don't want it to be random.
# This retrieves the first 6 digits of a random value.
$randomValue = "$(Get-Random)".Substring(0,6)

# Set the values for the resource names that don't have to be globally unique.
$location = "West US"
$resourceGroup = "ContosoResources"
$iotHubConsumerGroup = "ContosoConsumers"
$containerName = "contosoresults"

# Create the resource group to be used 
#   for all resources for this tutorial.
New-AzResourceGroup -Name $resourceGroup -Location $location

# The IoT hub name must be globally unique, 
#   so add a random value to the end.
$iotHubName = "ContosoTestHub" + $randomValue
Write-Host "IoT hub name is " $iotHubName

# Create the IoT hub.
New-AzIotHub -ResourceGroupName $resourceGroup `
    -Name $iotHubName `
    -SkuName "S1" `
    -Location $location `
    -Units 1 

# Add a consumer group to the IoT hub for the 'events' endpoint.
Add-AzIotHubEventHubConsumerGroup -ResourceGroupName $resourceGroup `
  -Name $iotHubName `
  -EventHubConsumerGroupName $iotHubConsumerGroup `
  -EventHubEndpointName "events"

# The storage account name must be globally unique, so add a random value to the end.
$storageAccountName = "contosostorage" + $randomValue
Write-Host "storage account name is " $storageAccountName

# Create the storage account to be used as a routing destination.
# Save the context for the storage account 
#   to be used when creating a container.
$storageAccount = New-AzStorageAccount -ResourceGroupName $resourceGroup `
    -Name $storageAccountName `
    -Location $location `
    -SkuName Standard_LRS `
    -Kind Storage
# Retrieve the connection string from the context. 
$storageConnectionString = $storageAccount.Context.ConnectionString
Write-Host "storage connection string = " $storageConnectionString 

# Create the container in the storage account.
New-AzStorageContainer -Name $containerName `
    -Context $storageAccount.Context

# The Service Bus namespace must be globally unique,
#   so add a random value to the end.
$serviceBusNamespace = "ContosoSBNamespace" + $randomValue
Write-Host "Service Bus namespace is " $serviceBusNamespace

# Create the Service Bus namespace.
New-AzServiceBusNamespace -ResourceGroupName $resourceGroup `
    -Location $location `
    -Name $serviceBusNamespace 

# The Service Bus queue name must be globally unique,
#  so add a random value to the end.
$serviceBusQueueName  = "ContosoSBQueue" + $randomValue
Write-Host "Service Bus queue name is " $serviceBusQueueName 

# Create the Service Bus queue to be used as a routing destination.
New-AzServiceBusQueue -ResourceGroupName $resourceGroup `
    -Namespace $serviceBusNamespace `
    -Name $serviceBusQueueName  `
    -EnablePartitioning $False 
```

### <a name="create-a-simulated-device"></a>シミュレート対象デバイスを作成します

[!INCLUDE [iot-hub-include-create-simulated-device-portal](../../includes/iot-hub-include-create-simulated-device-portal.md)]

ベース リソースが設定されたら、メッセージ ルーティングを構成できます。

## <a name="set-up-message-routing"></a>メッセージ ルーティングを設定する

[!INCLUDE [iot-hub-include-create-routing-description](../../includes/iot-hub-include-create-routing-description.md)]

ルーティング エンドポイントを作成するには、[Add-AzIotHubRoutingEndpoint](/powershell/module/az.iothub/Add-AzIotHubRoutingEndpoint) を使用します。 エンドポイントに対するメッセージ ルートを作成するには、[Add-AzIotHubRoute](/powershell/module/az.iothub/Add-AzIoTHubRoute) を使用します。

### <a name="route-to-a-storage-account"></a>ストレージ アカウントへのルーティング 

まず、ストレージ アカウントのエンドポイントを設定して、メッセージ ルートを作成します。

[!INCLUDE [iot-hub-include-blob-storage-format](../../includes/iot-hub-include-blob-storage-format.md)]

これらは、Cloud Shell セッション内で設定する必要のある、スクリプトによって使用される変数です。

**resourceGroup**: このフィールドは 2 回出現します。どちらもご自分のリソース グループに設定してください。

**name**:このフィールドは、ルーティングの適用先となる IoT ハブの名前です。

**endpointName**: このフィールドは、エンドポイントを特定する名前です。 

**endpointType**: このフィールドは、エンドポイントの種類です。 この値は `azurestoragecontainer`、`eventhub`、`servicebusqueue`、`servicebustopic` のいずれかに設定する必要があります。 ここでは目的上、`azurestoragecontainer` に設定します。

**subscriptionID**: このフィールドは、ご自分の Azure アカウントの subscriptionID に設定されます。

**storageConnectionString**: この値は、前のスクリプトで設定したストレージ アカウントから取得されます。 ストレージ アカウントにアクセスするためのルーティングに使用されます。

**containerName**: このフィールドは、データの書き込み先となるストレージ アカウント内のコンテナーの名前です。

**Encoding**: このフィールドは、`AVRO` または `JSON` に設定します。 これで格納データの形式が指定されます。 既定値は AVRO です。

**routeName**: このフィールドは、自分が設定するルートの名前です。 

**condition**: このフィールドは、このエンドポイントに送信されるメッセージのフィルター処理に使用されるクエリです。 ストレージにルーティングされるメッセージのクエリ条件は `level="storage"` です。

**enabled**: このフィールドの既定値は `true` です。これは、メッセージ ルートを作成した後、そのルートを有効にする必要があることを示します。

このスクリプトをコピーして、Cloud Shell ウィンドウに貼り付けます。

```powershell
##### ROUTING FOR STORAGE #####

$endpointName = "ContosoStorageEndpoint"
$endpointType = "azurestoragecontainer"
$routeName = "ContosoStorageRoute"
$condition = 'level="storage"'
```

次の手順では、ストレージ アカウントのルーティング エンドポイントを作成します。 結果の格納先となるコンテナーも指定します。 コンテナーは、ストレージ アカウントを作成したときに作成済みです。

```powershell
# Create the routing endpoint for storage.
# Specify 'AVRO' or 'JSON' for the encoding of the data.
Add-AzIotHubRoutingEndpoint `
  -ResourceGroupName $resourceGroup `
  -Name $iotHubName `
  -EndpointName $endpointName `
  -EndpointType $endpointType `
  -EndpointResourceGroup $resourceGroup `
  -EndpointSubscriptionId $subscriptionId `
  -ConnectionString $storageConnectionString  `
  -ContainerName $containerName `
  -Encoding AVRO
```

次に、ストレージ エンドポイントのメッセージ ルートを作成します。 このメッセージ ルートによって、クエリの指定を満たすメッセージの送信先が指定されます。

```powershell
# Create the route for the storage endpoint.
Add-AzIotHubRoute `
   -ResourceGroupName $resourceGroup `
   -Name $iotHubName `
   -RouteName $routeName `
   -Source DeviceMessages `
   -EndpointName $endpointName `
   -Condition $condition `
   -Enabled 
```

### <a name="route-to-a-service-bus-queue"></a>Service Bus キューへのルーティング

次に、Service Bus キューへのルーティングを設定します。 Service Bus キューの接続文字列を取得するには、適切な権限を定義した承認規則を作成する必要があります。 以下のスクリプトでは、Service Bus キューに `sbauthrule` という承認規則を作成し、その権限を `Listen Manage Send` に設定しています。 この承認規則が設定されると、それを使用して、キューの接続文字列を取得できます。

```powershell
##### ROUTING FOR SERVICE BUS QUEUE #####

# Create the authorization rule for the Service Bus queue.
New-AzServiceBusAuthorizationRule `
  -ResourceGroupName $resourceGroup `
  -NamespaceName $serviceBusNamespace `
  -Queue $serviceBusQueueName `
  -Name "sbauthrule" `
  -Rights @("Manage","Listen","Send")
```

次に、承認規則を使用して Service Bus キューのキーを取得します。 この承認規則は、スクリプトで後に接続文字列を取得するために使用されます。

```powershell
$sbqkey = Get-AzServiceBusKey `
    -ResourceGroupName $resourceGroup `
    -NamespaceName $serviceBusNamespace `
    -Queue $servicebusQueueName `
    -Name "sbauthrule"
```

次に、Service Bus キューのルーティング エンドポイントとメッセージ ルートを設定します。 これらは、Cloud Shell セッション内で設定する必要のある、スクリプトによって使用される変数です。

**endpointName**: このフィールドは、エンドポイントを特定する名前です。 

**endpointType**: このフィールドは、エンドポイントの種類です。 この値は `azurestoragecontainer`、`eventhub`、`servicebusqueue`、`servicebustopic` のいずれかに設定する必要があります。 ここでは目的上、`servicebusqueue` に設定します。

**routeName**: このフィールドは、自分が設定するルートの名前です。 

**condition**: このフィールドは、このエンドポイントに送信されるメッセージのフィルター処理に使用されるクエリです。 Service Bus キューにルーティングされるメッセージのクエリ条件は `level="critical"` です。

Service Bus キューに対するメッセージ ルーティングには、次の Azure PowerShell を使用します。

```powershell
$endpointName = "ContosoSBQueueEndpoint"
$endpointType = "servicebusqueue"
$routeName = "ContosoSBQueueRoute"
$condition = 'level="critical"'

# Add the routing endpoint, using the connection string property from the key.
Add-AzIotHubRoutingEndpoint `
  -ResourceGroupName $resourceGroup `
  -Name $iotHubName `
  -EndpointName $endpointName `
  -EndpointType $endpointType `
  -EndpointResourceGroup $resourceGroup `
  -EndpointSubscriptionId $subscriptionId `
  -ConnectionString $sbqkey.PrimaryConnectionString

# Set up the message route for the Service Bus queue endpoint.
Add-AzIotHubRoute `
   -ResourceGroupName $resourceGroup `
   -Name $iotHubName `
   -RouteName $routeName `
   -Source DeviceMessages `
   -EndpointName $endpointName `
   -Condition $condition `
   -Enabled 
```

### <a name="view-message-routing-in-the-portal"></a>ポータルでのメッセージ ルーティングの表示

[!INCLUDE [iot-hub-include-view-routing-in-portal](../../includes/iot-hub-include-view-routing-in-portal.md)]

## <a name="next-steps"></a>次のステップ

リソースを設定してメッセージ ルートを構成したら、次のチュートリアルに進んで、IoT ハブにメッセージを送信する方法と、それらがさまざまな宛先にルーティングされるようすを確認しましょう。 

> [!div class="nextstepaction"]
> [パート 2 - メッセージ ルーティング結果の表示](tutorial-routing-view-message-routing-results.md)
