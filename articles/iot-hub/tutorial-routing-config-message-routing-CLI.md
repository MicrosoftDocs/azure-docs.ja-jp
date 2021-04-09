---
title: チュートリアル - Azure CLI を使用して Azure IoT Hub のメッセージ ルーティングを構成する
description: チュートリアル - Azure CLI を使用して Azure IoT Hub のメッセージ ルーティングを構成します。 メッセージ内のプロパティに応じて、ストレージ アカウントまたは Service Bus キューのどちらかにルーティングします。
author: robinsh
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.topic: tutorial
ms.date: 03/25/2019
ms.author: robinsh
ms.custom: mvc, devx-track-azurecli
ms.openlocfilehash: a1cce7264e56b16d285685392186a05e2f075439
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102199766"
---
# <a name="tutorial-use-the-azure-cli-to-configure-iot-hub-message-routing"></a>チュートリアル:Azure CLI を使用して IoT Hub のメッセージ ルーティングを構成する

[!INCLUDE [iot-hub-include-routing-intro](../../includes/iot-hub-include-routing-intro.md)]

[!INCLUDE [iot-hub-include-routing-create-resources](../../includes/iot-hub-include-routing-create-resources.md)]

## <a name="download-the-script-optional"></a>スクリプトのダウンロード (任意)

このチュートリアルのパート 2 では、IoT ハブにメッセージを送信する Visual Studio アプリケーションをダウンロードして実行します。 ダウンロード内のフォルダーには、Azure Resource Manager テンプレートとパラメーター ファイルのほか、Azure CLI と PowerShell のスクリプトが含まれています。

完成したスクリプトを確認したい場合は、[Azure IoT C# サンプル](https://github.com/Azure-Samples/azure-iot-samples-csharp/archive/master.zip)をダウンロードしてください。 master.zip ファイルを解凍します。 Azure CLI スクリプトは、**iothub_routing_cli.azcli** という名前で /iot-hub/Tutorials/Routing/SimulatedDevice/resources/ に存在します。

## <a name="use-the-azure-cli-to-create-your-resources"></a>Azure CLI を使用したリソースの作成

以下のスクリプトをコピーして Cloud Shell に貼り付け、Enter キーを押してください。 スクリプトが 1 行ずつ実行されます。 この最初のセクションのスクリプトでは、ストレージ アカウント、IoT ハブ、Service Bus 名前空間、Service Bus キューなど、このチュートリアルのベース リソースが作成されます。 チュートリアルの残りの部分を読み進めながら、各ブロックのスクリプトをコピーし、Cloud Shell に貼り付けて実行してください。

> [!TIP]
> デバッグのヒント: このスクリプトでは、読みやすくするために継続記号 (バックスラッシュ `\`) を使用しています。 スクリプトの実行で問題が生じた場合は、Cloud Shell セッションで `bash` が実行されていること、およびバックスラッシュの後ろにスペースがないことを確認してください。
> 

IoT ハブ名やストレージ アカウント名など、いくつかのリソース名はグローバルに一意であることが必要です。 それを簡単にするために、それらのリソース名には、*randomValue* という英数字のランダム値が追加されます。 randomValue はスクリプトの冒頭で 1 度生成され、スクリプト全体で必要に応じてリソース名に追加されます。 これをランダムにしたくない場合は、空の文字列または特定の値に設定できます。 

> [!IMPORTANT]
> 最初のスクリプトで設定された変数はルーティング スクリプトによっても使用されるため、すべてのスクリプトは、同じ Cloud Shell セッションで実行してください。 新しいセッションを開いてルーティングの設定用のスクリプトを実行した場合、いくつかの変数に値が格納されません。
>

```azurecli-interactive
# This command retrieves the subscription id of the current Azure account. 
# This field is used when setting up the routing queries.
subscriptionID=$(az account show --query id -o tsv)

# Concatenate this number onto the resources that have to be globally unique.
# You can set this to "" or to a specific value if you don't want it to be random.
# This retrieves a random value.
randomValue=$RANDOM

# This command installs the IOT Extension for Azure CLI.
# You only need to install this the first time.
# You need it to create the device identity. 
az extension add --name azure-iot

# Set the values for the resource names that 
#   don't have to be globally unique.
location=westus
resourceGroup=ContosoResources
iotHubConsumerGroup=ContosoConsumers
containerName=contosoresults
iotDeviceName=Contoso-Test-Device

# Create the resource group to be used
#   for all the resources for this tutorial.
az group create --name $resourceGroup \
    --location $location

# The IoT hub name must be globally unique, 
#   so add a random value to the end.
iotHubName=ContosoTestHub$randomValue 
echo "IoT hub name = " $iotHubName

# Create the IoT hub.
az iot hub create --name $iotHubName \
    --resource-group $resourceGroup \
    --sku S1 --location $location

# Add a consumer group to the IoT hub for the 'events' endpoint.
az iot hub consumer-group create --hub-name $iotHubName \
    --name $iotHubConsumerGroup

# The storage account name must be globally unique, 
#   so add a random value to the end.
storageAccountName=contosostorage$randomValue
echo "Storage account name = " $storageAccountName

# Create the storage account to be used as a routing destination.
az storage account create --name $storageAccountName \
    --resource-group $resourceGroup \
    --location $location \
    --sku Standard_LRS

# Get the primary storage account key. 
#    You need this to create the container.
storageAccountKey=$(az storage account keys list \
    --resource-group $resourceGroup \
    --account-name $storageAccountName \
    --query "[0].value" | tr -d '"') 

# See the value of the storage account key.
echo "storage account key = " $storageAccountKey

# Create the container in the storage account. 
az storage container create --name $containerName \
    --account-name $storageAccountName \
    --account-key $storageAccountKey \
    --public-access off

# The Service Bus namespace must be globally unique, 
#   so add a random value to the end.
sbNamespace=ContosoSBNamespace$randomValue
echo "Service Bus namespace = " $sbNamespace

# Create the Service Bus namespace.
az servicebus namespace create --resource-group $resourceGroup \
    --name $sbNamespace \
    --location $location

# The Service Bus queue name must be globally unique, 
#   so add a random value to the end.
sbQueueName=ContosoSBQueue$randomValue
echo "Service Bus queue name = " $sbQueueName

# Create the Service Bus queue to be used as a routing destination.
az servicebus queue create --name $sbQueueName \
    --namespace-name $sbNamespace \
    --resource-group $resourceGroup

# Create the IoT device identity to be used for testing.
az iot hub device-identity create --device-id $iotDeviceName \
    --hub-name $iotHubName

# Retrieve the information about the device identity, then copy the primary key to
#   Notepad. You need this to run the device simulation during the testing phase.
az iot hub device-identity show --device-id $iotDeviceName \
    --hub-name $iotHubName
```

ベース リソースが設定されたら、メッセージ ルーティングを構成できます。

## <a name="set-up-message-routing"></a>メッセージ ルーティングを設定する

[!INCLUDE [iot-hub-include-create-routing-description](../../includes/iot-hub-include-create-routing-description.md)]

ルーティング エンドポイントを作成するには、[az iot hub routing-endpoint create](/cli/azure/iot/hub/routing-endpoint#az-iot-hub-routing-endpoint-create) を使用します。 そのエンドポイントに対するメッセージ ルートを作成するには、[az iot hub route create](/cli/azure/iot/hub/route#az-iot-hub-route-create) を使用します。

### <a name="route-to-a-storage-account"></a>ストレージ アカウントへのルーティング

[!INCLUDE [iot-hub-include-blob-storage-format](../../includes/iot-hub-include-blob-storage-format.md)]

まず、ストレージ アカウントのエンドポイントを設定して、ルートを設定します。 

これらは、Cloud Shell セッション内で設定する必要のある、スクリプトによって使用される変数です。

**storageConnectionString**: この値は、前のスクリプトで設定したストレージ アカウントから取得されます。 ストレージ アカウントにアクセスするためのメッセージ ルーティングに使用されます。

  **resourceGroup**: リソース グループは 2 回出現します。ご自分のリソース グループに設定してください。

**endpoint subscriptionID**: このフィールドは、エンドポイントの Azure subscriptionID に設定されます。 

**endpointType**: このフィールドは、エンドポイントの種類です。 この値は `azurestoragecontainer`、`eventhub`、`servicebusqueue`、`servicebustopic` のいずれかに設定する必要があります。 ここでは目的上、`azurestoragecontainer` に設定します。

**iotHubName**: このフィールドは、ルーティングを実行することになるハブの名前です。

**containerName**: このフィールドは、データの書き込み先となるストレージ アカウント内のコンテナーの名前です。

**encoding**: このフィールドは `avro` または `json` になります。 これは格納データの形式を表します。

**routeName**: このフィールドは、自分が設定するルートの名前です。 

**endpointName**: このフィールドは、エンドポイントを特定する名前です。 

**enabled**: このフィールドの既定値は `true` です。これは、メッセージ ルートを作成した後、そのルートを有効にする必要があることを示します。

**condition**: このフィールドは、このエンドポイントに送信されるメッセージのフィルター処理に使用されるクエリです。 ストレージにルーティングされるメッセージのクエリ条件は `level="storage"` です。

このスクリプトをコピーし、Cloud Shell ウィンドウに貼り付けて実行してください。

```azurecli
##### ROUTING FOR STORAGE ##### 

endpointName="ContosoStorageEndpoint"
endpointType="azurestoragecontainer"
routeName="ContosoStorageRoute"
condition='level="storage"'

# Get the connection string for the storage account.
# Adding the "-o tsv" makes it be returned without the default double quotes around it.
storageConnectionString=$(az storage account show-connection-string \
  --name $storageAccountName --query connectionString -o tsv)
```

次の手順では、ストレージ アカウントのルーティング エンドポイントを作成します。 結果の格納先となるコンテナーも指定します。 コンテナーは、先ほどストレージ アカウントを作成したときに作成済みです。

```azurecli
# Create the routing endpoint for storage.
az iot hub routing-endpoint create \
  --connection-string $storageConnectionString \
  --endpoint-name $endpointName \
  --endpoint-resource-group $resourceGroup \
  --endpoint-subscription-id $subscriptionID \
  --endpoint-type $endpointType \
  --hub-name $iotHubName \
  --container $containerName \
  --resource-group $resourceGroup \
  --encoding avro
```

次に、ストレージ エンドポイントのルートを作成します。 このメッセージ ルートによって、クエリの指定を満たすメッセージの送信先が指定されます。 

```azurecli
# Create the route for the storage endpoint.
az iot hub route create \
  --name $routeName \
  --hub-name $iotHubName \
  --source devicemessages \
  --resource-group $resourceGroup \
  --endpoint-name $endpointName \
  --enabled \
  --condition $condition
```

### <a name="route-to-a-service-bus-queue"></a>Service Bus キューへのルーティング

次に、Service Bus キューへのルーティングを設定します。 Service Bus キューの接続文字列を取得するには、適切な権限を定義した承認規則を作成する必要があります。 以下のスクリプトでは、Service Bus キューに `sbauthrule` という承認規則を作成し、その権限を `Listen Manage Send` に設定しています。 この承認規則が定義されると、それを使用して、キューの接続文字列を取得できます。

```azurecli
# Create the authorization rule for the Service Bus queue.
az servicebus queue authorization-rule create \
  --name "sbauthrule" \
  --namespace-name $sbNamespace \
  --queue-name $sbQueueName \
  --resource-group $resourceGroup \
  --rights Listen Manage Send \
  --subscription $subscriptionID
```

今度は、承認規則を使用して Service Bus キューへの接続文字列を取得します。

```azurecli
# Get the Service Bus queue connection string.
# The "-o tsv" ensures it is returned without the default double-quotes.
sbqConnectionString=$(az servicebus queue authorization-rule keys list \
  --name "sbauthrule" \
  --namespace-name $sbNamespace \
  --queue-name $sbQueueName \
  --resource-group $resourceGroup \
  --subscription $subscriptionID \
  --query primaryConnectionString -o tsv)

# Show the Service Bus queue connection string.
echo "service bus queue connection string = " $sbqConnectionString
```

次に、Service Bus キューのルーティング エンドポイントとメッセージ ルートを設定します。 これらは、Cloud Shell セッション内で設定する必要のある、スクリプトによって使用される変数です。

**endpointName**: このフィールドは、エンドポイントを特定する名前です。 

**endpointType**: このフィールドは、エンドポイントの種類です。 この値は `azurestoragecontainer`、`eventhub`、`servicebusqueue`、`servicebustopic` のいずれかに設定する必要があります。 ここでは目的上、`servicebusqueue` に設定します。

**routeName**: このフィールドは、自分が設定するルートの名前です。 

**condition**: このフィールドは、このエンドポイントに送信されるメッセージのフィルター処理に使用されるクエリです。 Service Bus キューにルーティングされるメッセージのクエリ条件は `level="critical"` です。

Service Bus キューのルーティング エンドポイントとメッセージ ルートに関する Azure CLI を次に示します。

```azurecli
endpointName="ContosoSBQueueEndpoint"
endpointType="ServiceBusQueue"
routeName="ContosoSBQueueRoute"
condition='level="critical"'

# Set up the routing endpoint for the Service Bus queue.
# This uses the Service Bus queue connection string.
az iot hub routing-endpoint create \
  --connection-string $sbqConnectionString \
  --endpoint-name $endpointName \
  --endpoint-resource-group $resourceGroup \
  --endpoint-subscription-id $subscriptionID \
  --endpoint-type $endpointType \
  --hub-name $iotHubName \
  --resource-group $resourceGroup 

# Set up the message route for the Service Bus queue endpoint.
az iot hub route create --name $routeName \
  --hub-name $iotHubName \
  --source-type devicemessages \
  --resource-group $resourceGroup \
  --endpoint-name $endpointName \
  --enabled \
  --condition $condition
  ```

### <a name="view-message-routing-in-the-portal"></a>ポータルでのメッセージ ルーティングの表示

[!INCLUDE [iot-hub-include-view-routing-in-portal](../../includes/iot-hub-include-view-routing-in-portal.md)]

## <a name="next-steps"></a>次のステップ

リソースを設定してメッセージ ルートを構成したら、次のチュートリアルに進んで、IoT ハブにメッセージを送信する方法と、それらがさまざまな宛先にルーティングされるようすを確認しましょう。 

> [!div class="nextstepaction"]
> [パート 2 - メッセージ ルーティング結果の表示](tutorial-routing-view-message-routing-results.md)
