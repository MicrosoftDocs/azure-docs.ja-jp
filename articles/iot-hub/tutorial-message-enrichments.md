---
title: チュートリアル - Azure IoT Hub のメッセージ エンリッチメントを使用する
description: Azure IoT Hub のメッセージに対してメッセージ エンリッチメントを使用する方法を紹介するチュートリアル
author: robinsh
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 05/10/2019
ms.author: robinsh
ms.openlocfilehash: 2c115bf0ad21e905e998692fbbc175f5aa52b86d
ms.sourcegitcommit: fe50db9c686d14eec75819f52a8e8d30d8ea725b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/14/2019
ms.locfileid: "69014235"
---
# <a name="tutorial-using-azure-iot-hub-message-enrichments-preview"></a>チュートリアル:Azure IoT Hub のメッセージ エンリッチメント (プレビュー) を使用する

"*メッセージ エンリッチメント*" は、指定エンドポイントに送信される前のメッセージに対し、追加情報を含んだ "*スタンプ*" を適用する IoT Hub の機能です。 メッセージ エンリッチメントを使用する理由の 1 つは、ダウンストリームの処理を単純化するために用いることのできるデータを追加することです。 たとえば、デバイス ツイン タグを使用してデバイスのテレメトリ メッセージのエンリッチメントを行えば、この情報のために顧客側でデバイス ツイン API を呼び出す負担を軽減することができます。 詳細については、[メッセージ エンリッチメントの概要](iot-hub-message-enrichments-overview.md)に関するページを参照してください。

このチュートリアルでは、2 つの異なるストレージ コンテナーを指し示す 2 つのエンドポイント (**enriched** および **original**) を含むリソースを、Azure CLI を使用して設定します。 次に、**enriched** ストレージ コンテナーのエンドポイントに送信されるメッセージにのみ適用されるメッセージ エンリッチメントを、[Azure portal](https://portal.azure.com) を使用して構成します。 IoT Hub にメッセージを送信すると、そのメッセージが両方のストレージ コンテナーにルーティングされます。 **enriched** ストレージ コンテナーのエンドポイントに送信されるメッセージだけがエンリッチされます。

このチュートリアルを完了するために行うタスクは次のとおりです。

**IoT Hub のメッセージ エンリッチメントを使用する**
> [!div class="checklist"]
> * Azure CLI を使用してリソース (IoT ハブ、2 つのエンドポイントを含むストレージ アカウント、ルーティング構成) を作成する。
> * Azure portal を使用してメッセージ エンリッチメントを構成する。
> * IoT デバイスをシミュレートするアプリを実行し、ハブにメッセージを送信する。
> * その結果を表示して、メッセージ エンリッチメントが想定どおりに機能していることを確認する。

## <a name="prerequisites"></a>前提条件

* Azure サブスクリプションが必要です。 Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) を作成してください。

* [Visual Studio](https://www.visualstudio.com/) のインストール。

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="retrieve-the-sample-code"></a>サンプル コードを取得する

[IoT デバイス シミュレーション](https://github.com/Azure-Samples/azure-iot-samples-csharp/archive/master.zip)をダウンロードしてファイルを解凍します。 このリポジトリには、IoT ハブにメッセージを送信する際に使用するアプリケーションなど、いくつかのアプリケーションが存在します。

このダウンロードには、メッセージ エンリッチメントのテストに使用するリソースを作成するためのスクリプトも含まれています。 そのスクリプトは /azure-iot-samples-csharp/iot-hub/Tutorials/Routing/SimulatedDevice/resources/iothub_msgenrichment_cli.azcli にあります。 差し当たり、このスクリプトを参照して利用できます。 この記事から直接、スクリプトをコピーすることもできます。

テストを始める準備が整ったら、このダウンロードに含まれるデバイス シミュレーション アプリケーションを使用して、IoT ハブにメッセージを送信します。

## <a name="set-up-and-configure-resources"></a>リソースをセットアップして構成する

この Azure CLI スクリプトは、必要なリソースを作成することに加え、別個のストレージ コンテナーであるエンドポイントへの 2 つのルートを構成します。 ルーティングの構成の詳細については、[ルーティングのチュートリアル](tutorial-routing.md)を参照してください。 リソースのセットアップ後、[Azure portal](https://portal.azure.com) を使用して各エンドポイントのメッセージ エンリッチメントを構成し、テスト手順に進みます。

> [!NOTE]
> メッセージはすべて両方のエンドポイントにルーティングされますが、エンリッチされるのは、メッセージ エンリッチメントが構成されたエンドポイントに向かうメッセージだけです。
>

以下のスクリプトを使用するか、ダウンロードしたリポジトリの /resources フォルダーにあるスクリプトを開いてください。 このスクリプトによって実行される手順を次に示します。

* IoT Hub を作成します。
* ストレージ アカウントを作成します。
* エンリッチされたメッセージ用とエンリッチされていないメッセージ用の 2 つのコンテナーをストレージ アカウントに作成します。
* 2 つの異なるストレージ アカウントのルーティングを設定します。
    * 各ストレージ アカウント コンテナーのエンドポイントを作成します。
    * 各ストレージ アカウント コンテナーのエンドポイントに対するルートを作成します。

IoT ハブ名やストレージ アカウント名など、いくつかのリソース名はグローバルに一意であることが必要です。 スクリプトを実行しやすいように、これらのリソース名の末尾には、ランダムな英数字の値 (*randomValue*) が追加されます。 randomValue はスクリプトの冒頭で 1 度生成され、スクリプト全体で必要に応じてリソース名に追加されます。 これをランダムにしたくない場合は、空の文字列または特定の値に設定できます。

[Bash の Cloud Shell ウィンドウ](https://shell.azure.com)を開いてください (まだ開いていない場合)。 解凍したリポジトリ内のスクリプトを開き、Ctrl + A キーを使用して全選択し、Ctrl + C キーを使用してそれをコピーします。 または、以下の CLI スクリプトをコピーするか、Cloud Shell で直接開いてください。 Azure Cloud Shell ウィンドウのコマンド ラインを右クリックし、 **[貼り付け]** を選択してスクリプトを貼り付けます。 一度に 1 ステートメントずつスクリプトが実行されます。 スクリプトの実行が停止したら、**Enter** キーを押して最後のコマンドを確実に実行します。 以下のコード ブロックは、使用されているスクリプトとその実行内容を説明するコメントを示したものです。

次に示したのは、このスクリプトによって作成されるリソースです。 **Enriched** と表示されているリソースは、エンリッチメントが適用されたメッセージ用であることを意味します。 **Original** と表示されているリソースは、エンリッチメントが適用されていないメッセージ用であることを意味します。

| EnableAdfsAuthentication | 値 |
|-----|-----|
| resourceGroup | ContosoResourcesMsgEn |
| container name | original  |
| container name | enriched  |
| IoT device name | Contoso-Test-Device |
| IoT Hub 名 | ContosoTestHubMsgEn |
| storage Account Name | contosostorage |
| endpoint Name 1 | ContosoStorageEndpointOriginal |
| endpoint Name 2 | ContosoStorageEndpointEnriched|
| route Name 1 | ContosoStorageRouteOriginal |
| route Name 2 | ContosoStorageRouteEnriched |

```azurecli-interactive
# This command retrieves the subscription id of the current Azure account.
# This field is used when setting up the routing rules.
subscriptionID=$(az account show --query id -o tsv)

# Concatenate this number onto the resources that have to be globally unique.
# You can set this to "" or to a specific value if you don't want it to be random.
# This retrieves a random value.
randomValue=$RANDOM

# This command installs the IOT Extension for Azure CLI.
# You only need to install this the first time.
# You need it to create the device identity.
az extension add --name azure-cli-iot-ext

# Set the values for the resource names that
#   don't have to be globally unique.
location=westus2
resourceGroup=ContosoResourcesMsgEn
containerName1=original
containerName2=enriched
iotDeviceName=Contoso-Test-Device

# Create the resource group to be used
#   for all the resources for this tutorial.
az group create --name $resourceGroup \
    --location $location

# The IoT hub name must be globally unique,
#   so add a random value to the end.
iotHubName=ContosoTestHubMsgEn$randomValue
echo "IoT hub name = " $iotHubName

# Create the IoT hub.
az iot hub create --name $iotHubName \
    --resource-group $resourceGroup \
    --sku S1 --location $location

# You need a storage account that will have two containers
#   -- one for the original messages and
#   one for the enriched messages.
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
#    You need this to create the containers.
storageAccountKey=$(az storage account keys list \
    --resource-group $resourceGroup \
    --account-name $storageAccountName \
    --query "[0].value" | tr -d '"')

# See the value of the storage account key.
echo "storage account key = " $storageAccountKey

# Create the containers in the storage account.
az storage container create --name $containerName1 \
    --account-name $storageAccountName \
    --account-key $storageAccountKey \
    --public-access off

az storage container create --name $containerName2 \
    --account-name $storageAccountName \
    --account-key $storageAccountKey \
    --public-access off

# Create the IoT device identity to be used for testing.
az iot hub device-identity create --device-id $iotDeviceName \
    --hub-name $iotHubName

# Retrieve the information about the device identity, then copy the primary key to
#   Notepad. You need this to run the device simulation during the testing phase.
# If you are using Cloud Shell, you can scroll the window back up to retrieve this value.
az iot hub device-identity show --device-id $iotDeviceName \
    --hub-name $iotHubName

##### ROUTING FOR STORAGE #####

# You're going to have two routes and two endpoints.
# One points to container1 in the storage account
#   and includes all messages.
# The other points to container2 in the same storage account
#   and only includes enriched messages.

endpointType="azurestoragecontainer"
endpointName1="ContosoStorageEndpointOriginal"
endpointName2="ContosoStorageEndpointEnriched"
routeName1="ContosoStorageRouteOriginal"
routeName2="ContosoStorageRouteEnriched"

# for both endpoints, retrieve the messages going to storage
condition='level="storage"'

# Get the connection string for the storage account.
# Adding the "-o tsv" makes it be returned without the default double quotes around it.
storageConnectionString=$(az storage account show-connection-string \
  --name $storageAccountName --query connectionString -o tsv)

# Create the routing endpoints and routes.
# Set the encoding format to either avro or json.

# This is the endpoint for container 1, for endpoint messages that are not enriched.
az iot hub routing-endpoint create \
  --connection-string $storageConnectionString \
  --endpoint-name $endpointName1 \
  --endpoint-resource-group $resourceGroup \
  --endpoint-subscription-id $subscriptionID \
  --endpoint-type $endpointType \
  --hub-name $iotHubName \
  --container $containerName1 \
  --resource-group $resourceGroup \
  --encoding json

# This is the endpoint for container 2, for endpoint messages that are enriched.
az iot hub routing-endpoint create \
  --connection-string $storageConnectionString \
  --endpoint-name $endpointName2 \
  --endpoint-resource-group $resourceGroup \
  --endpoint-subscription-id $subscriptionID \
  --endpoint-type $endpointType \
  --hub-name $iotHubName \
  --container $containerName2 \
  --resource-group $resourceGroup \
  --encoding json

# This is the route for messages that are not enriched.
# Create the route for the first storage endpoint.
az iot hub route create \
  --name $routeName1 \
  --hub-name $iotHubName \
  --source devicemessages \
  --resource-group $resourceGroup \
  --endpoint-name $endpointName1 \
  --enabled \
  --condition $condition

# This is the route for messages that are not enriched.
az iot hub route create \
  --name $routeName2 \
  --hub-name $iotHubName \
  --source devicemessages \
  --resource-group $resourceGroup \
  --endpoint-name $endpointName2 \
  --enabled \
  --condition $condition
```

この時点で、リソースはすべてセットアップされ、ルーティングが構成されました。 ポータルでメッセージのルーティング構成を確認し、**enriched** ストレージ コンテナーに向かうメッセージに対するエンリッチメントを設定することができます。

### <a name="view-routing-and-configure-the-message-enrichments"></a>ルーティングを確認してメッセージ エンリッチメントを構成する

1. **[リソース グループ]** を選択して IoT ハブに移動し、このチュートリアル用に設定されたリソース グループ (**ContosoResources_MsgEn**) を選択します。 一覧から IoT ハブを探して選択します。 IoT ハブの *[メッセージ ルーティング]* * を選択します。

   ![メッセージのルーティングを選択する](./media/tutorial-message-enrichments/select-iot-hub.png)

   メッセージ ルーティング ウィンドウには、 **[ルート]** 、 **[カスタム エンドポイント]** 、 **[Enrich messages]\(メッセージのエンリッチ\)** の 3 つのタブがあります。 最初の 2 つのタブを参照すると、スクリプトによって設定された構成を確認できます。 メッセージ エンリッチメントは、3 つ目のタブを使用して追加します。 **enriched** というストレージ コンテナーのエンドポイントに向かうメッセージをエンリッチしてみましょう。 名前と値を入力した後、ドロップダウン リストからエンドポイント **ContosoStorageEndpointEnriched** を選択します。 次に示したのは、IoT ハブの名前をメッセージに追加するエンリッチメントの設定例です。

   ![1 つ目のエンリッチメントを追加する](./media/tutorial-message-enrichments/add-message-enrichments.png)

2. ContosoStorageEndpointEnriched エンドポイントのリストに次の値を追加します。

   | EnableAdfsAuthentication | 値 | エンドポイント (ドロップダウン リスト) |
   | ---- | ----- | -------------------------|
   | myIotHub | $iothubname | AzureStorageContainers > ContosoStorageEndpointEnriched |
   | DeviceLocation | $twin.tags.location | AzureStorageContainers > ContosoStorageEndpointEnriched |
   |customerID | 6ce345b8-1e4a-411e-9398-d34587459a3a | AzureStorageContainers > ContosoStorageEndpointEnriched |

   > [!NOTE]
   > デバイスにツインが存在しない場合、メッセージ エンリッチメントの値には、ここに入力した値が文字列のスタンプとして適用されます。 デバイス ツイン情報を確認するには、ポータルで対象のハブに移動して、 **[IoT デバイス]** を選択し、対象のデバイスを選択して、ページの上部にある **[デバイス ツイン]** を選択します。
   >
   > ツイン情報を編集して、タグ (location など) を追加し、必要に応じて特定の値を設定します。 詳細については、「[IoT Hub のデバイス ツインの理解と使用](iot-hub-devguide-device-twins.md)」を参照してください。

3. 作業が完了すると、ウィンドウは次の画像のようになります。

   ![すべてのエンリッチメントが追加された状態の表](./media/tutorial-message-enrichments/all-message-enrichments.png)

4. **[適用]** を選択して変更を保存します。

## <a name="send-messages-to-the-iot-hub"></a>IoT ハブにメッセージを送信する

エンドポイントに対するメッセージ エンリッチメントの構成が完了したら、シミュレートされたデバイスのアプリケーションを実行して、IoT ハブにメッセージを送信します。 ハブは、次の処理を遂行するルールと共に設定されています。

* ストレージ エンドポイント ContosoStorageEndpointOriginal にルーティングされたメッセージはエンリッチされず、ストレージ コンテナー `original` に格納されます。

* ストレージ エンドポイント ContosoStorageEndpointEnriched にルーティングされたメッセージはエンリッチされて、ストレージ コンテナー `enriched` に格納されます。

シミュレートされたデバイスのアプリケーションは、解凍後のダウンロードに含まれているアプリケーションの 1 つです。 このアプリケーションは、[ルーティングのチュートリアル](tutorial-routing.md)で取り上げられている各種メッセージ ルーティング方法 (Azure Storage など) ごとにメッセージを送信します。

ソリューション ファイル (IoT_SimulatedDevice.sln) をダブルクリックしてコードを Visual Studio で開いてから、Program.cs を開きます。 `{your hub name}` は、IoT ハブの名前で置き換えてください。 IoT ハブのホスト名の形式は、 **{自分のハブ名}.azure-devices.net** です。 このチュートリアルでのハブのホスト名は、**ContosoTestHubMsgEn.azure-devices.net** です。 次に、`{device key}` を、先ほどリソースを作成するためのスクリプトを実行するときに保存したデバイス キーに置き換えます。

デバイス キーがない場合は、ポータルから取得できます。 ログイン後、 **[リソース グループ]** に移動し、リソース グループを選択して、目的の IoT ハブを選択します。 テスト デバイスの **[IoT デバイス]** から該当するデバイスを選択してください。 **[プライマリ キー]** の横にあるコピー アイコンを選択して、クリップボードにコピーします。

   ```csharp
        static string myDeviceId = "contoso-test-device";
        static string iotHubUri = "ContosoTestHubMsgEn.azure-devices.net";
        // This is the primary key for the device. This is in the portal.
        // Find your IoT hub in the portal > IoT devices > select your device > copy the key.
        static string deviceKey = "{your device key here}";
   ```

## <a name="run-and-test"></a>実行してテストする

コンソール アプリケーションを実行します。 数分待ちます。 送信中のメッセージがアプリケーションのコンソール画面に表示されます。

アプリは、1 秒おきにデバイスからクラウドへの新しいメッセージを IoT ハブに送信します。 メッセージには、デバイス ID、温度、湿度、およびメッセージ レベル (既定値は `normal`) を含む、JSON でシリアル化されたオブジェクトが含まれます。 アプリはランダムにレベル `critical` または `storage` を割り当てるので、メッセージはストレージ アカウントまたは既定のエンドポイントにルーティングされます。 ストレージ アカウント内の **enriched** コンテナーに送信されたメッセージがエンリッチされます。

いくつかのストレージ メッセージが送信されたら、データを確認します。

1. **[リソース グループ]** を選択し、対象のリソース グループ (ContosoResourcesMsgEn) を探して選択します。

2. 該当するストレージ アカウント (contosostorage) を選択します。 左側にある選択ウィンドウから **[ストレージ エクスプローラー (プレビュー)]** を選択します。

   ![ストレージ エクスプローラーを選択する](./media/tutorial-message-enrichments/select-storage-explorer.png)

   **[BLOB コンテナー]** を選択すると、使用可能な 2 つのコンテナーが表示されます。

   ![ストレージ アカウント内のコンテナーを表示する](./media/tutorial-message-enrichments/show-blob-containers.png)

**enriched** というコンテナー内のメッセージには、メッセージ エンリッチメントが適用されています。 **original** コンテナー内のメッセージには、エンリッチメントが適用されていない生のメッセージが格納されます。 いずれかのコンテナーを最下部に到達するまでドリルダウンし、最新のメッセージ ファイルを開きます。次に、もう一方のコンテナーについても同じ操作を行い、そのコンテナー内のメッセージにはエンリッチメントが追加されていないことを確認します。

エンリッチされたメッセージを見ると、次のように、"My IoT Hub" とハブ名のほか、場所と顧客 ID が表示されていることがわかります。

```json
{"EnqueuedTimeUtc":"2019-05-10T06:06:32.7220000Z","Properties":{"level":"storage","my IoT Hub":"contosotesthubmsgen3276","devicelocation":"$twin.tags.location","customerID":"6ce345b8-1e4a-411e-9398-d34587459a3a"},"SystemProperties":{"connectionDeviceId":"Contoso-Test-Device","connectionAuthMethod":"{\"scope\":\"device\",\"type\":\"sas\",\"issuer\":\"iothub\",\"acceptingIpFilterRule\":null}","connectionDeviceGenerationId":"636930642531278483","enqueuedTime":"2019-05-10T06:06:32.7220000Z"},"Body":"eyJkZXZpY2VJZCI6IkNvbnRvc28tVGVzdC1EZXZpY2UiLCJ0ZW1wZXJhdHVyZSI6MjkuMjMyMDE2ODQ4MDQyNjE1LCJodW1pZGl0eSI6NjQuMzA1MzQ5NjkyODQ0NDg3LCJwb2ludEluZm8iOiJUaGlzIGlzIGEgc3RvcmFnZSBtZXNzYWdlLiJ9"}
```

以下に示したのが unenriched のメッセージです。 ここでは "My IoT Hub"、"devicelocation"、および "customerID" が表示されていません。これは、このエンドポイントにはエンリッチメントが適用されていないためです。

```json
{"EnqueuedTimeUtc":"2019-05-10T06:06:32.7220000Z","Properties":{"level":"storage"},"SystemProperties":{"connectionDeviceId":"Contoso-Test-Device","connectionAuthMethod":"{\"scope\":\"device\",\"type\":\"sas\",\"issuer\":\"iothub\",\"acceptingIpFilterRule\":null}","connectionDeviceGenerationId":"636930642531278483","enqueuedTime":"2019-05-10T06:06:32.7220000Z"},"Body":"eyJkZXZpY2VJZCI6IkNvbnRvc28tVGVzdC1EZXZpY2UiLCJ0ZW1wZXJhdHVyZSI6MjkuMjMyMDE2ODQ4MDQyNjE1LCJodW1pZGl0eSI6NjQuMzA1MzQ5NjkyODQ0NDg3LCJwb2ludEluZm8iOiJUaGlzIGlzIGEgc3RvcmFnZSBtZXNzYWdlLiJ9"}
```

## <a name="clean-up-resources"></a>リソースのクリーンアップ

このチュートリアルで作成したリソースをすべて削除するには、リソース グループを削除します。 これにより、そのグループ内に含まれているすべてのリソースも削除されます。 この場合は、IoT ハブ、ストレージ アカウント、リソース グループ自体が削除されます。

### <a name="use-the-azure-cli-to-clean-up-resources"></a>Azure CLI を使用してリソースをクリーンアップする

リソース グループを削除するには、[az group delete](https://docs.microsoft.com/cli/azure/group?view=azure-cli-latest#az-group-delete) コマンドを使います。 `$resourceGroup` は、このチュートリアルの開始時に **ContosoResources** に設定されました。

```azurecli-interactive
az group delete --name $resourceGroup
```

## <a name="next-steps"></a>次の手順

このチュートリアルでは、次の手順を使用して、IoT Hub メッセージへのメッセージ エンリッチメントの追加を構成、テストしました。

**IoT Hub のメッセージ エンリッチメントを使用する**
> [!div class="checklist"]
> * Azure CLI を使用してリソース (IoT ハブ、2 つのエンドポイントを含むストレージ アカウント、ルーティング構成) を作成する。
> * Azure portal を使用してメッセージ エンリッチメントを構成する。
> * IoT デバイスをシミュレートするアプリを実行し、ハブにメッセージを送信する。
> * その結果を表示して、メッセージ エンリッチメントが想定どおりに機能していることを確認する。

メッセージ エンリッチメントの詳細については、[メッセージ エンリッチメントの概要](iot-hub-message-enrichments-overview.md)に関するページを参照してください。

メッセージ ルーティングの詳細については、次の記事を参照してください。

* [IoT Hub メッセージ ルーティングを使用して device-to-cloud メッセージを別のエンドポイントに送信する](iot-hub-devguide-messages-d2c.md)

* [チュートリアル:IoT Hub のルーティング](tutorial-routing.md)