---
title: IoT Hub からテレメトリを取り込む
titleSuffix: Azure Digital Twins
description: IoT Hub からデバイスのテレメトリ メッセージを取り込む方法について説明します。
author: alexkarcher-msft
ms.author: alkarche
ms.date: 8/11/2020
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: 8d720d77773e506a13f176723ab4583613f1e625
ms.sourcegitcommit: 58d3b3314df4ba3cabd4d4a6016b22fa5264f05a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/02/2020
ms.locfileid: "89291757"
---
# <a name="ingest-iot-hub-telemetry-into-azure-digital-twins"></a>Azure Digital Twins に IoT Hub テレメトリを取り込む

Azure Digital Twins は、IoT デバイスやその他のソースからのデータに基づいています。 Azure Digital Twins で使用するデバイス データの一般的なソースは [IoT Hub](../iot-hub/about-iot-hub.md) です。

Azure Digital Twins にデータを取り込むプロセスとは、外部コンピューティング リソースを設定することです。たとえば、データを受け取り、[DigitalTwins API](how-to-use-apis-sdks.md) を使用してプロパティを設定したり、それに応じて、[デジタル ツイン](concepts-twins-graph.md)に対してテレメトリ イベントを発生させたりする [Azure 関数](../azure-functions/functions-overview.md)などです。 

このハウツー ドキュメントでは、IoT Hub からテレメトリを取り込むことができる Azure 関数を作成するプロセスについて手順を追って説明します。

## <a name="prerequisites"></a>前提条件

この例を続行する前に、前提条件として次のリソースを設定する必要があります。
* **IoT ハブ** 手順については、[この IoT Hub のクイック スタート](../iot-hub/quickstart-send-telemetry-cli.md)の「*IoT Hub の作成*」のセクションを参照してください。
* **Azure 関数** デジタル ツイン インスタンスを呼び出すための適切なアクセス許可を備えています。 手順については、[*方法: データを処理するための Azure 関数の設定*](how-to-create-azure-function.md)に関するページを参照してください。 
* デバイス テレメトリを受信する **Azure Digital Twins インスタンス**。 手順については、[*方法: Azure Digital Twins インスタンスと認証の設定*](./how-to-set-up-instance-portal.md)に関するページを参照してください。

### <a name="example-telemetry-scenario"></a>テレメトリのシナリオ例

このハウツーでは、Azure 関数を使用して、IoT Hub から Azure Digital Twins にメッセージを送信する方法の概要について説明します。 このために使用できる構成とマッチング戦略は多数ありますが、この記事の例には次のパーツが含まれています。
* 既知のデバイス ID を持つ IoT Hub の温度計デバイス。
* ID が一致するデバイスを表すデジタル ツイン

> [!NOTE]
> この例では、デバイス ID と対応するデジタル ツインの ID の間の単純な ID 一致を使用していますが、(マッピング テーブルなどを使用して) デバイスからツインへのより高度なマッピングを用意することもできます。

温度計デバイスから温度テレメトリ イベントが送信されるたびに、デジタル ツインの *temperature* プロパティが更新されます。 このシナリオの概要を次の図に示します。

:::image type="content" source="media/how-to-ingest-iot-hub-data/events.png" alt-text="フロー チャートを示す図。このグラフでは、IoT Hub を通じて、温度テレメトリが IoT Hub デバイスから Azure 関数に送信されます。これにより、Azure Digital Twins 内にあるツインの temperature プロパティが更新されます。" border="false":::

## <a name="add-a-model-and-twin"></a>モデルとツインの追加

IoT ハブの情報を更新するには、ツインが必要です。

モデルは次のようになります。
```JSON
{
  "@id": "dtmi:contosocom:DigitalTwins:Thermostat;1",
  "@type": "Interface",
  "@context": "dtmi:dtdl:context;2",
  "contents": [
    {
      "@type": "Property",
      "name": "Temperature",
      "schema": "double"
    }
  ]
}
```

**このモデルをツイン インスタンスにアップロードする**には、Azure CLI を開き、次のコマンドを実行します。

```azurecli-interactive
az dt model create --models '{  "@id": "dtmi:contosocom:DigitalTwins:Thermostat;1",  "@type": "Interface",  "@context": "dtmi:dtdl:context;2",  "contents": [    {      "@type": "Property",      "name": "Temperature",      "schema": "double"    }  ]}' -n {digital_twins_instance_name}
```

[!INCLUDE [digital-twins-known-issue-cloud-shell](../../includes/digital-twins-known-issue-cloud-shell.md)]

次に、**このモデルを使用して 1 つのツインを作成**します。 次のコマンドを使用してツインを作成して、初期温度値を 0.0 に設定します。

```azurecli-interactive
az dt twin create --dtmi "dtmi:contosocom:DigitalTwins:Thermostat;1" --twin-id thermostat67 --properties '{"Temperature": 0.0,}' --dt-name {digital_twins_instance_name}
```

[!INCLUDE [digital-twins-known-issue-cloud-shell](../../includes/digital-twins-known-issue-cloud-shell.md)]

ツイン作成コマンドが成功した場合の出力は、次のようになります。
```json
{
  "$dtId": "thermostat67",
  "$etag": "W/\"0000000-9735-4f41-98d5-90d68e673e15\"",
  "$metadata": {
    "$model": "dtmi:contosocom:DigitalTwins:Thermostat;1",
    "Temperature": {
      "ackCode": 200,
      "ackDescription": "Auto-Sync",
      "ackVersion": 1,
      "desiredValue": 0.0,
      "desiredVersion": 1
    }
  },
  "Temperature": 0.0
}
```

## <a name="create-an-azure-function"></a>Azure 関数の作成

このセクションでは、同じ Visual Studio のスタートアップ手順と、Azure 関数スケルトンを使用します。これについては、[*データを処理するための Azure 関数の設定*](how-to-create-azure-function.md)に関するページを参照してください。 このスケルトンを使用すると、認証を処理し、サービス クライアントを作成することができます。これにより、データを処理し、応答として Azure Digital Twins API を呼び出すことができるようになります。 

次の手順では、IoT Hub からの IoT テレメトリ イベントを処理するための特定のコードを追加します。  

### <a name="add-telemetry-processing"></a>テレメトリの処理を追加する
    
テレメトリ イベントは、デバイスからのメッセージの形式で送信されます。 テレメトリ処理コードを追加する最初の手順は、このデバイス メッセージの関連部分を Event Grid イベントから抽出することです。 

デバイスによってメッセージの構成が異なる場合があるため、**この手順のコードは接続されているデバイスによって変わります**。 

次のコードは、テレメトリを JSON として送信する単純なデバイスの例を示しています。 このサンプルについては、["*チュートリアル: エンドツーエンドのソリューションの接続*](./tutorial-end-to-end.md)" に関するページを参照してください。 次のコードによって、メッセージを送信したデバイスのデバイス ID と温度値が検索されます。

```csharp
JObject deviceMessage = (JObject)JsonConvert.DeserializeObject(eventGridEvent.Data.ToString());
string deviceId = (string)deviceMessage["systemProperties"]["iothub-connection-device-id"];
var temperature = deviceMessage["body"]["Temperature"];
```

次のコード サンプルでは、ID と温度値を取得し、それらを使用して、そのツインを "パッチ" (更新) します。

```csharp
//Update twin using device temperature
var uou = new UpdateOperationsUtility();
uou.AppendReplaceOp("/Temperature", temperature.Value<double>());
await client.UpdateDigitalTwinAsync(deviceId, uou.Serialize());
...
```

### <a name="update-your-azure-function-code"></a>Azure 関数コードの更新

前のサンプルのコードを理解したので、Visual Studio を開き、Azure 関数のコードを次のサンプル コードに置き換えます。

```csharp
using System;
using System.Net.Http;
using Azure.Core.Pipeline;
using Azure.DigitalTwins.Core;
using Azure.DigitalTwins.Core.Serialization;
using Azure.Identity;
using Microsoft.Azure.EventGrid.Models;
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Extensions.EventGrid;
using Microsoft.Extensions.Logging;
using Newtonsoft.Json;
using Newtonsoft.Json.Linq;

namespace IotHubtoTwins
{
    public class IoTHubtoTwins
    {
        private static readonly string adtInstanceUrl = Environment.GetEnvironmentVariable("ADT_SERVICE_URL");
        private static readonly HttpClient httpClient = new HttpClient();

        [FunctionName("IoTHubtoTwins")]
        public async void Run([EventGridTrigger] EventGridEvent eventGridEvent, ILogger log)
        {
            if (adtInstanceUrl == null) log.LogError("Application setting \"ADT_SERVICE_URL\" not set");

            try
            {
                //Authenticate with Digital Twins
                ManagedIdentityCredential cred = new ManagedIdentityCredential("https://digitaltwins.azure.net");
                DigitalTwinsClient client = new DigitalTwinsClient(
                    new Uri(adtInstanceUrl), cred, new DigitalTwinsClientOptions 
                    { Transport = new HttpClientTransport(httpClient) });
                log.LogInformation($"ADT service client connection created.");
            
                if (eventGridEvent != null && eventGridEvent.Data != null)
                {
                    log.LogInformation(eventGridEvent.Data.ToString());

                    // Reading deviceId and temperature for IoT Hub JSON
                    JObject deviceMessage = (JObject)JsonConvert.DeserializeObject(eventGridEvent.Data.ToString());
                    string deviceId = (string)deviceMessage["systemProperties"]["iothub-connection-device-id"];
                    var temperature = deviceMessage["body"]["Temperature"];
                    
                    log.LogInformation($"Device:{deviceId} Temperature is:{temperature}");

                    //Update twin using device temperature
                    var uou = new UpdateOperationsUtility();
                    uou.AppendReplaceOp("/Temperature", temperature.Value<double>());
                    await client.UpdateDigitalTwinAsync(deviceId, uou.Serialize());
                }
            }
            catch (Exception e)
            {
                log.LogError($"Error in ingest function: {e.Message}");
            }
        }
    }
}
```

## <a name="connect-your-function-to-iot-hub"></a>関数の IoT Hub への接続

1. ハブ データのイベントの宛先を設定します。 [Azure portal](https://portal.azure.com/) で、IoT Hub インスタンスに移動します。 **[イベント]** で、Azure 関数のサブスクリプションを作成します。 

    :::image type="content" source="media/how-to-ingest-iot-hub-data/add-event-subscription.png" alt-text="イベント サブスクリプションの追加を示す Azure portal のスクリーンショット。":::

2. **[イベント サブスクリプションの作成]** ページで、各フィールドに次のように入力します。
    1. **[名前]** で、サブスクリプションに任意の名前を付けます。
    2. **[イベント スキーマ]** で、 **[Event Grid スキーマ]** を選択します。
    3. **[システム トピック名]** で、一意の名前を選択します。
    4. **[イベントの種類]** で、フィルター処理するイベントの種類として **[デバイス テレメトリ]** を選択します。
    5. **[エンドポイントの詳細]** で、エンドポイントとして Azure 関数を選択します。

    :::image type="content" source="media/how-to-ingest-iot-hub-data/event-subscription-2.png" alt-text="イベント サブスクリプションの詳細を示す Azure portal のスクリーンショット。":::

## <a name="send-simulated-iot-data"></a>シミュレートされた IoT データの送信

新しいイングレス機能をテストするには、デバイス シミュレーターを使用します。このデバイス シミュレーターについては、["*チュートリアル: エンドツーエンドのソリューションの接続*](./tutorial-end-to-end.md)" に関するページを参照してください。 このチュートリアルは、C# で記述されたサンプル プロジェクトによって進められます。 サンプル コードについては、こちらを参照してください。[Azure Digital Twins サンプル](https://docs.microsoft.com/samples/azure-samples/digital-twins-samples/digital-twins-samples)。 このリポジトリでは、**DeviceSimulator** プロジェクトを使用します。

このエンド ツー エンドのチュートリアルでは、次の手順を実行します。
1. [*シミュレートされたデバイスを IoT Hub に登録する*](./tutorial-end-to-end.md#register-the-simulated-device-with-iot-hub)
2. [*シミュレーションを構成して実行する*](./tutorial-end-to-end.md#configure-and-run-the-simulation)

## <a name="validate-your-results"></a>結果の検証

上記のデバイス シミュレーターを実行中に、デジタル ツインの温度値が変化します。 Azure CLI で、次のコマンドを実行して、温度値を確認します。

[!INCLUDE [digital-twins-known-issue-cloud-shell](../../includes/digital-twins-known-issue-cloud-shell.md)]

```azurecli-interactive
az dt twin query -q "select * from digitaltwins" -n {digital_twins_instance_name}
```

出力には、次のような温度値が含まれている必要があります。

```json
{
  "result": [
    {
      "$dtId": "thermostat67",
      "$etag": "W/\"0000000-1e83-4f7f-b448-524371f64691\"",
      "$metadata": {
        "$model": "dtmi:contosocom:DigitalTwins:Thermostat;1",
        "Temperature": {
          "ackCode": 200,
          "ackDescription": "Auto-Sync",
          "ackVersion": 1,
          "desiredValue": 69.75806974934324,
          "desiredVersion": 1
        }
      },
      "Temperature": 69.75806974934324
    }
  ]
}
```

値の変化を確認するには、上記のクエリ コマンドを繰り返し実行します。

## <a name="next-steps"></a>次のステップ

Azure Digital Twins を使用したデータのイングレスとエグレスについて確認します。
* "[*概念: 他のサービスとの統合*](concepts-integration.md)
