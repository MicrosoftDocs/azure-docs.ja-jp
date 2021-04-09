---
title: クイックスタート - Azure CLI を使用して Azure Stream Analytics ジョブを作成する
description: このクイックスタートでは、Azure CLI を使用して Azure Stream Analytics ジョブを作成する方法を示します。
services: stream-analytics
ms.service: stream-analytics
author: sidramadoss
ms.author: sidram
ms.reviewer: jasonh
ms.workload: big-data
ms.topic: quickstart
ms.custom: mvc, devx-track-azurecli
ms.date: 07/01/2020
ms.openlocfilehash: a3cc4c3d6936a51ca2010209ce23e4d82c9333eb
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "98016342"
---
# <a name="quickstart-create-an-azure-stream-analytics-job-using-the-azure-cli"></a>クイック スタート:Azure CLI を使用して Azure Stream Analytics ジョブを作成する

このクイックスタートでは、Azure CLI を使用して、温度が 27 度を超えるリアルタイム センサー メッセージをフィルター処理する、Stream Analytics ジョブを定義します。 お客様の Stream Analytics ジョブによって IoT Hub からデータが読み取られ、変換されて BLOB ストレージ内のコンテナーに書き戻されます。 このクイック スタートで使用される入力データは、Raspberry Pi オンライン シミュレーターによって生成されます。

## <a name="before-you-begin"></a>開始する前に

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

- リソース グループを作成します。 Azure リソースはすべてリソース グループにデプロイする必要があります。 リソース グループを使用すると、関連する Azure リソースを整理して管理できます。

   このクイックスタートでは、次の [az group create](/cli/azure/group#az-group-create) コマンドを使用して、*streamanalyticsrg* という名前のリソース グループを *eastus* の場所に作成します。

   ```azurecli
   az group create --name streamanalyticsrg --location eastus
   ```

## <a name="prepare-the-input-data"></a>入力データを準備する

Stream Analytics ジョブを定義する前に、ジョブの入力に使用されるデータを準備します。

次の Azure CLI コード ブロックは、ジョブに必要な入力データを準備するコマンドです。 コードを理解するためにセクションを確認してください。

1. [az iot hub create](../iot-hub/iot-hub-create-using-cli.md#create-an-iot-hub) コマンドを使用して、IoT ハブを作成します。 この例では、**MyASAIoTHub** という名前の IoT ハブを作成します。 IoT ハブの名前は一意であるため、独自の IoT ハブ名を考案する必要があります。 サブスクリプションで Free レベルを使用できる場合は、SKU を F1 に設定して Free レベルを使用します。 そうでない場合は、次に低いレベルを選択します。

    ```azurecli
    az iot hub create --name "MyASAIoTHub" --resource-group streamanalyticsrg --sku S1
    ```

    IoT ハブが作成されたら、[az iot hub show-connection-string](/cli/azure/iot/hub) コマンドを使用して IoT ハブの接続文字列を取得します。 IoT ハブを Stream Analytics ジョブへの入力として追加するときに備えて、接続文字列全体をコピーして保存します。

    ```azurecli
    az iot hub show-connection-string --hub-name "MyASAIoTHub"
    ```

2. [az iothub device-identity create](../iot-hub/quickstart-send-telemetry-c.md#register-a-device) コマンドを使用して、デバイスを IoT ハブに追加します。 この例では、**MyASAIoTDevice** という名前のデバイスを作成します。

    ```azurecli
    az iot hub device-identity create --hub-name "MyASAIoTHub" --device-id "MyASAIoTDevice"
    ```

3. [az iot hub device-identity show-connection-string](/cli/azure/ext/azure-iot/iot/hub/device-identity#ext-azure-iot-az-iot-hub-device-identity-show-connection-string) コマンドを使用してデバイスの接続文字列を取得します。 Raspberry Pi シミュレーターを作成するときに備えて、接続文字列全体をコピーして保存します。

    ```azurecli
    az iot hub device-identity show-connection-string --hub-name "MyASAIoTHub" --device-id "MyASAIoTDevice" --output table
    ```

    **出力例:**

    ```output
    HostName=MyASAIoTHub.azure-devices.net;DeviceId=MyASAIoTDevice;SharedAccessKey=a2mnUsg52+NIgYudxYYUNXI67r0JmNubmfVafojG8=
    ```

## <a name="create-a-blob-storage-account"></a>BLOB ストレージ アカウントの作成

次の Azure CLI コード ブロックでは、ジョブ出力に使用される BLOB ストレージ アカウントを作成します。 コードを理解するためにセクションを確認してください。

1. [az storage account create](/cli/azure/storage/account) コマンドで汎用のストレージ アカウントを作成します。 汎用のストレージ アカウントは、BLOB、ファイル、テーブル、およびキューという 4 つのサービスすべてに使用できます。

   山かっこ内のプレースホルダーをお客様独自の値に置き換えてください。

   ```azurecli
   az storage account create \
       --name <storage-account> \
       --resource-group streamanalyticsrg \
       --location eastus \
       --sku Standard_ZRS \
       --encryption-services blob
   ```

2. [az storage account keys list](/cli/azure/storage/account/keys) コマンドを実行して、ストレージ アカウントのキーを取得します。 次の手順で使用するために、このキーを保存します。

   ```azurecli
   az storage account keys list -g streamanalyticsrg -n <storage-account>
   ```

3. BLOB を格納するコンテナーは、[az storage container create](/cli/azure/storage/container) コマンドで作成します。 ストレージ アカウント キーを使用して、コンテナーの作成操作を承認します。 Azure CLI を使用したデータ操作の承認について詳しくは、「[Azure CLI を使用して BLOB またはキュー データへのアクセスを承認する](../storage/blobs/authorize-data-operations-cli.md)」を参照してください。

   ```azurecli
   az storage container create \
       --account-name <storage-account> \
       --name sample-container \
       --account-key <key>
       --auth-mode key
   ```

## <a name="create-a-stream-analytics-job"></a>Stream Analytics のジョブの作成

次の Azure CLI コード ブロックでは、Stream Analytics ジョブを作成します。 各セクションを確認してコードを理解してください

1. [az stream-analytics job create](/cli/azure/ext/stream-analytics/stream-analytics/job#ext-stream-analytics-az-stream-analytics-job-create) コマンドを使用して、Stream Analytics ジョブを作成します。

```azurecli
az stream-analytics job create \
    --resource-group streamanalyticsrg 
    --name streamanalyticsjob \
    --location eastus \
    --output-error-policy "Drop" \
    --events-outoforder-policy "Drop" \
    --events-outoforder-max-delay 5 \
    --events-late-arrival-max-delay 16 \     
    --data-locale "en-US"
```

## <a name="configure-input-to-the-job"></a>ジョブへの入力を構成する

[az stream-analytics input](/cli/azure/ext/stream-analytics/stream-analytics/input#ext-stream-analytics-az-stream-analytics-input-create) コマンドレットを使用して、ジョブへの入力を追加します。 このコマンドレットは、パラメーターとして、ジョブ名、ジョブ入力名、リソース グループ名、およびジョブ入力定義を受け取ります。 ジョブ入力定義は、ジョブの入力の構成に必要なプロパティを含む JSON ファイルです。 この例では、入力として IoT Hub を作成します。

ローカル マシンに `datasource.json` という名前のファイルを作成し、次の JSON データを追加します。 `sharedAccessPolicyKey` の値は必ず、前のセクションで保存した IoT ハブの接続文字列の `SharedAccessKey` 部分に置き換えてください。

```json
{
    "type": "Microsoft.Devices/IotHubs",
    "properties": {
        "iotHubNamespace": "iothub",
        "sharedAccessPolicyName": "iothubowner",
        "sharedAccessPolicyKey": "sharedAccessPolicyKey=",
        "consumerGroupName": "sdkconsumergroup",
        "endpoint": "messages/events"
    }
}
```

ローカル マシンに `serialization.json` という名前のファイルを作成し、次の JSON データを追加します。

```json
{
     "type": "Json",
     "properties": {
         "encoding": "UTF8"
     }
}
```

次に、`az stream-analytics input create` コマンドレットを実行します。 必ず、`datasource` 変数の値は、ジョブ入力定義の JSON ファイルを格納したパスに、`serialization` 変数の値は、シリアル化 JSON ファイルを格納したパスに置き換えてください。

```azurecli
az stream-analytics input create \
    --resource-group streamanalyticsrg 
    --job-name streamanalyticsjob \
    --name asaiotinput \
    --type Stream \
    --datasource datasource.json \
    --serialization serialization.json
```

## <a name="configure-output-to-the-job"></a>ジョブへの出力を構成する

[az stream-analytics output create](/cli/azure/ext/stream-analytics/stream-analytics/output#ext-stream-analytics-az-stream-analytics-output-create) コマンドレットを使用して、ジョブへの出力を追加します。 このコマンドレットは、パラメーターとして、ジョブ名、ジョブ出力名、リソース グループ名、およびジョブ出力定義を受け取ります。 ジョブ出力定義は、ジョブの出力の構成に必要なプロパティを含む JSON ファイルです。 この例では、BLOB ストレージを出力として使用します。

ローカル マシンに `datasink.json` という名前のファイルを作成し、次の JSON データを追加します。 `accountKey` の値は、ストレージ アカウントのアクセス キー ($storageAccountKey 値に格納されている値) に置き換えてください。

```json
{
    "type": "Microsoft.Storage/Blob",
    "properties": {
        "storageAccounts": [
            {
                "accountName": "<storage-account>",
                "accountKey": "accountKey=="
            }
        ],
        "container": "state",
        "pathPattern": "{date}/{time}",
        "dateFormat": "yyyy/MM/dd",
        "timeFormat": "HH"
    }
}
```

次に、`az stream-analytics output` コマンドレットを実行します。 必ず、`datasource` 変数の値は、ジョブ出力定義の JSON ファイルを格納したパスに、`serialization` 変数の値は、シリアル化 JSON ファイルを格納したパスに置き換えてください。

```azurecli
az stream-analytics output create \
    --resource-group streamanalyticsrg \
    --job-name streamanalyticsjob \
    --name asabloboutput \
    --datasource datasink.json \
    --serialization serialization.json
```

## <a name="define-the-transformation-query"></a>変換クエリを定義する

[az stream-analytics transformation create](/cli/azure/ext/stream-analytics/stream-analytics/transformation#ext-stream-analytics-az-stream-analytics-transformation-create) コマンドレットを使用して、ジョブの変換を追加します。 このコマンドレットは、パラメーターとして、ジョブ名、ジョブ変換名、リソース グループ名、およびジョブ変換定義を受け取ります。 

`az stream-analytics transformation create` コマンドレットを実行します。

```azurecli
az stream-analytics transformation create \
    --resource-group streamanalyticsrg \
    --job-name streamanalyticsjob \
    --name Transformation \
    --streaming-units "6" \
    --transformation-query "SELECT * INTO asabloboutput FROM asaiotinput HAVING Temperature > 27"
```
## <a name="run-the-iot-simulator"></a>IoT シミュレーターを実行する

1. [Raspberry Pi Azure IoT オンライン シミュレーター](https://azure-samples.github.io/raspberry-pi-web-simulator/)を開きます。

2. 行 15 のプレースホルダーを、前のセクションで保存した Azure IoT Hub デバイスの接続文字列全体で置き換えます。

3. **[実行]** をクリックします。 お客様の IoT ハブに送信されているセンサー データとメッセージが出力に表示されます。

    ![Raspberry Pi Azure IoT オンライン シミュレーター](./media/stream-analytics-quick-create-powershell/ras-pi-connection-string.png)

## <a name="start-the-stream-analytics-job-and-check-the-output"></a>Stream Analytics ジョブを開始して出力をチェックする

[az stream-analytics job start](/cli/azure/ext/stream-analytics/stream-analytics/job#ext-stream-analytics-az-stream-analytics-job-start) コマンドレットを使用して、ジョブを開始します。 このコマンドレットは、パラメーターとして、ジョブ名、リソース グループ名、出力開始モード、および開始時刻を受け取ります。 `OutputStartMode` には、`JobStartTime`、`CustomTime`、または `LastOutputEventTime` の値が使用できます。

次のコマンドレットを実行すると、ジョブが開始されたときに出力として `True` が返されます。 変換されたデータが含まれた出力フォルダーがストレージ コンテナーに作成されます。

```azurecli
az stream-analytics job start \
    --resource-group streamanalyticsrg \
    --name streamanalyticsjob \
    --output-start-mode JobStartTime
```

## <a name="clean-up-resources"></a>リソースをクリーンアップする

リソース グループ、ストリーミング ジョブ、および関連するすべてのリソースは、不要になったら削除します。 ジョブを削除すると、ジョブによって消費されるストリーミング ユニットに対する課金を回避することができます。 ジョブを後で使用する計画がある場合、削除をスキップしてこのジョブを一時的に停止できます。 このジョブを今後使用しない場合には、次のコマンドレットを実行して、このクイック スタートで作成したすべてのリソースを削除してください。

```azurecli
az group delete \
    --name streamanalyticsrg \
    --no-wait
```

## <a name="next-steps"></a>次のステップ

このクイックスタートでは、Azure CLI を使用して、簡単な Stream Analytics ジョブをデプロイしました。 [Azure portal](stream-analytics-quick-create-portal.md) と [Visual Studio](stream-analytics-quick-create-vs.md) を使用して Stream Analytics ジョブをデプロイすることもできます。

その他の入力ソースの構成やリアルタイム検出の実行について学習するには、次の記事に進んでください。

> [!div class="nextstepaction"]
> [Azure Stream Analytics を使用したリアルタイムの不正行為の検出](stream-analytics-real-time-fraud-detection.md)