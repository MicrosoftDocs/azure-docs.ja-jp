---
title: 開発者ガイド - IoT プラグ アンド プレイ プレビュー | Microsoft Docs
description: 開発者向けの IoT プラグ アンド プレイの説明
author: rido-min
ms.author: rmpablos
ms.date: 07/16/2020
ms.topic: conceptual
ms.service: iot-pnp
services: iot-pnp
ms.openlocfilehash: f1860f9645a50789803d509a6a1ea98bc0ea1a9e
ms.sourcegitcommit: e69bb334ea7e81d49530ebd6c2d3a3a8fa9775c9
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/27/2020
ms.locfileid: "88950138"
---
# <a name="iot-plug-and-play-preview-developer-guide"></a>IoT プラグ アンド プレイ プレビュー開発者ガイド

IoT プラグ アンド プレイ プレビューでは、Azure IoT アプリケーションに機能を宣伝するスマート デバイスをビルドできます。 IoT プラグ アンド プレイ デバイスでは、ユーザーが IoT プラグ アンド プレイ対応アプリケーションに接続するときに、手動で構成する必要がありません。

このガイドでは、[IoT プラグ アンド プレイ規則](concepts-convention.md)に従ってデバイスを作成するために必要な基本的な手順と、デバイスとの対話に使用できる REST API について説明します。

IoT プラグ アンド プレイ デバイスを構築するには、次の手順を実行します。

1. デバイスでは、MQTT または MQTT over WebSockets プロトコルを使用して Azure IoT Hub に接続していることを確認します。
1. デバイスを記述する [Digital Twins Definition Language (DTDL)](https://github.com/Azure/opendigitaltwins-dtdl) モデルを作成します。 詳細については、[IoT プラグ アンド プレイ モデルのコンポーネントの概要](concepts-components.md)に関するページを参照してください。
1. デバイス接続の一部として `model-id` をアナウンスするようにデバイスを更新します。
1. [IoT プラグ アンド プレイ規則](concepts-convention.md)を使用してテレメトリ、プロパティ、コマンドを実装します

デバイスの実装の準備ができたら、[Azure IoT エクスプローラー](howto-use-iot-explorer.md)を使用して、デバイスが IoT プラグ アンド プレイ規則に従っていることを確認します。

> [!Tip]
> この記事のすべてのコード フラグメントには C# を使用していますが、その概念は C、Python、Node、Java 用の使用可能な SDK のいずれにも適用できます。

## <a name="model-id-announcement"></a>モデル ID のアナウンス

モデル ID をアナウンスするには、デバイスが接続情報に含まれている必要があります。

```csharp
DeviceClient.CreateFromConnectionString(
  connectionString,
  TransportType.Mqtt,
  new ClientOptions() { ModelId = modelId })
```

新しい `ClientOptions` のオーバーロードは、接続を初期化するために使用されるすべての `DeviceClient` メソッドで使用できます。

モデル ID のアナウンスが、次のバージョンの SDK に追加されました

|SDK|Version|
|---|-------|
|C-SDK|1.3.9|
|.NET|1.27.0|
|Java|1.14.0|
|Node|1.17.0|
|Python|2.1.4|

## <a name="dps-payload"></a>DPS ペイロード

[Device Provisioning Service (DPS)](../iot-dps/about-iot-dps.md) を使用するデバイスには、次の JSON ペイロードを使用して、プロビジョニング プロセス中に使用される `modelId` を含めることができます。

```json
{
    "modelId" : "dtmi:com:example:Thermostat;1"
}
```

## <a name="implement-telemetry-properties-and-commands"></a>テレメトリ、プロパティ、およびコマンドを実装する

[IoT プラグ アンド プレイ モデルのコンポーネントの概要](concepts-components.md)に関するページで説明されているように、デバイス ビルダーは、コンポーネントを使用してデバイスを記述するかどうかを決定する必要があります。 コンポーネントを使用する場合、デバイスはこのセクションで説明されている規則に従う必要があります。

### <a name="telemetry"></a>テレメトリ

コンポーネントのないモデルには、特別なプロパティは必要ありません。

コンポーネントを使用する場合、デバイスでは、メッセージ プロパティにコンポーネント名を設定する必要があります。

```c#
public async Task SendComponentTelemetryValueAsync(string componentName, string serializedTelemetry)
{
  var message = new Message(Encoding.UTF8.GetBytes(serializedTelemetry));
  message.Properties.Add("$.sub", componentName);
  message.ContentType = "application/json";
  message.ContentEncoding = "utf-8";
  await deviceClient.SendEventAsync(message);
}
```

### <a name="read-only-properties"></a>読み取り専用プロパティ

コンポーネントのないモデルには、特別なコンストラクトは必要ありません。

```csharp
TwinCollection reportedProperties = new TwinCollection();
reportedProperties["maxTemperature"] = 38.7;
await client.UpdateReportedPropertiesAsync(reportedProperties);
```

デバイス ツインは、次の報告されるプロパティを使用して更新されます。

```json
{
  "reported": {
      "maxTemperature" : 38.7
  }
}
```

コンポーネントを使用する場合、次のコンポーネントの名前内にプロパティを作成する必要があります。

```csharp
TwinCollection reportedProperties = new TwinCollection();
TwinCollection component = new TwinCollection();
component["maxTemperature"] = 38.7;
component["__t"] = "c"; // marker to identify a component
reportedProperties["thermostat1"] = component;
await client.UpdateReportedPropertiesAsync(reportedProperties);
```

デバイス ツインは、次の報告されるプロパティを使用して更新されます。

```json
{
  "reported": {
    "thermostat1" : {  
      "__t" : "c",  
      "maxTemperature" : 38.7
     } 
  }
}
```

### <a name="writable-properties"></a>書き込み可能なプロパティ

これらのプロパティは、デバイスから設定するか、ソリューションから更新することができます。 ソリューションからプロパティを更新すると、クライアントでは `DeviceClient` でコールバックとして通知を受け取ります。 IoT プラグ アンド プレイ規則に従うために、デバイスからサービスに対して、プロパティが正常に受信されたことを通知する必要があります。

#### <a name="report-a-writable-property"></a>書き込み可能なプロパティを報告する

デバイスから書き込み可能なプロパティが報告された場合は、規則に定義されている `ack` 値を含める必要があります。

コンポーネントなしで書き込み可能なプロパティを報告するには、次のようにします。

```csharp
TwinCollection reportedProperties = new TwinCollection();
TwinCollection ackProps = new TwinCollection();
ackProps["value"] = 23.2;
ackProps["ac"] = 200; // using HTTP status codes
ackProps["av"] = 0; // not readed from a desired property
ackProps["ad"] = "reported default value";
reportedProperties["targetTemperature"] = ackProps;
await client.UpdateReportedPropertiesAsync(reportedProperties);
```

デバイス ツインは、次の報告されるプロパティを使用して更新されます。

```json
{
  "reported": {
      "targetTemperature": {
          "value": 23.2,
          "ac": 200,
          "av": 3,
          "ad": "complete"
      }
  }
}
```

コンポーネントから書き込み可能なプロパティを報告するには、ツインにマーカーを含める必要があります。

```csharp
TwinCollection reportedProperties = new TwinCollection();
TwinCollection component = new TwinCollection();
TwinCollection ackProps = new TwinCollection();
component["__t"] = "c"; // marker to identify a component
ackProps["value"] = 23.2;
ackProps["ac"] = 200; // using HTTP status codes
ackProps["av"] = 0; // not read from a desired property
ackProps["ad"] = "reported default value";
component["targetTemperature"] = ackProps;
reportedProperties["thermostat1"] = component;
await client.UpdateReportedPropertiesAsync(reportedProperties);
```

デバイス ツインは、次の報告されるプロパティを使用して更新されます。

```json
{
  "reported": {
    "thermostat1": {
      "__t" : "c",
      "targetTemperature": {
          "value": 23.2,
          "ac": 200,
          "av": 3,
          "ad": "complete"
      }
    }
  }
}
```

#### <a name="subscribe-to-desired-property-updates"></a>必要なプロパティの更新をサブスクライブする

サービスでは、接続されたデバイスで通知をトリガーする目的のプロパティを更新できます。 この通知には、更新を識別するバージョン番号など、更新された目的のプロパティが含まれます。 デバイスでは、報告されたプロパティと同じ `ack` メッセージで応答する必要があります。

コンポーネントのないモデルでは、1 つのプロパティを参照し、受け取ったバージョンでレポートされた `ack` を作成します。

```csharp
await client.SetDesiredPropertyUpdateCallbackAsync(async (desired, ctx) => 
{
  JValue targetTempJson = desired["targetTemperature"];
  double targetTemperature = targetTempJson.Value<double>();

  TwinCollection reportedProperties = new TwinCollection();
  TwinCollection ackProps = new TwinCollection();
  ackProps["value"] = targetTemperature;
  ackProps["ac"] = 200;
  ackProps["av"] = desired.Version; 
  ackProps["ad"] = "desired property received";
  reportedProperties["targetTemperature"] = ackProps;

  await client.UpdateReportedPropertiesAsync(reportedProperties);
}, null);
```

デバイス ツインでは、目的のセクションと報告されるセクションにプロパティが表示されます。

```json
{
  "desired" : {
    "targetTemperature": 23.2,
    "$version" : 3
  },
  "reported": {
      "targetTemperature": {
          "value": 23.2,
          "ac": 200,
          "av": 3,
          "ad": "complete"
      }
  }
}
```

コンポーネントを含むモデルでは、コンポーネント名でラップされた目的のプロパティを受け取り、`ack` で報告されたプロパティを報告する必要があります。

```csharp
await client.SetDesiredPropertyUpdateCallbackAsync(async (desired, ctx) =>
{
  JObject thermostatComponent = desired["thermostat1"];
  JToken targetTempProp = thermostatComponent["targetTemperature"];
  double targetTemperature = targetTempProp.Value<double>();

  TwinCollection reportedProperties = new TwinCollection();
  TwinCollection component = new TwinCollection();
  TwinCollection ackProps = new TwinCollection();
  component["__t"] = "c"; // marker to identify a component
  ackProps["value"] = targetTemperature;
  ackProps["ac"] = 200; // using HTTP status codes
  ackProps["av"] = desired.Version; // not readed from a desired property
  ackProps["ad"] = "desired property received";
  component["targetTemperature"] = ackProps;
  reportedProperties["thermostat1"] = component;

  await client.UpdateReportedPropertiesAsync(reportedProperties);
}, null);
```

コンポーネントのデバイス ツインには、次のように目的のセクションと報告されたセクションを示します。

```json
{
  "desired" : {
    "thermostat1" : {
        "__t" : "c",
        "targetTemperature": 23.2,
    }
    "$version" : 3
  },
  "reported": {
    "thermostat1" : {
        "__t" : "c",
      "targetTemperature": {
          "value": 23.2,
          "ac": 200,
          "av": 3,
          "ad": "complete"
      }
    }
  }
}
```

### <a name="commands"></a>コマンド

コンポーネントのないモデルは、サービスから呼び出されたときにコマンド名を受け取ります。

コンポーネントを含むモデルは、コンポーネントと `*` 区切り記号が前に付いたコマンド名を受け取ります。

```csharp
await client.SetMethodHandlerAsync("themostat*reboot", (MethodRequest req, object ctx) =>
{
  Console.WriteLine("REBOOT");
  return Task.FromResult(new MethodResponse(200));
},
null);
```

#### <a name="request-and-response-payloads"></a>要求と応答のペイロード

コマンドでは、型を使用して、要求と応答のペイロードを定義します。 デバイスでは、受け取った入力パラメーターを逆シリアル化し、応答をシリアル化する必要があります。 ペイロードで定義された複合型を使用してコマンドを実装する例を次に示します。

```json
{
  "@type": "Command",
  "name": "start",
  "request": {
    "name": "startRequest",
    "schema": {
      "@type": "Object",
      "fields": [
        {
          "name": "startPriority",
          "schema": "integer"
        },
        {
          "name": "startMessage",
          "schema" : "string"
        }
      ]
    }
  },
  "response": {
    "name": "startReponse",
    "schema": {
      "@type": "Object",
      "fields": [
        {
            "name": "startupTime",
            "schema": "integer" 
        },
        {
          "name": "startupMessage",
          "schema": "string"
        }
      ]
    }
  }
}
```

次のコード スニペットは、デバイスでこのコマンド定義がどのように実装されるかを示しています。これには、シリアル化と逆シリアル化を有効にするために使用される型が含まれます。

```csharp
class startRequest
{
  public int startPriority { get; set; }
  public string startMessage { get; set; }
}

class startResponse
{
  public int startupTime { get; set; }
  public string startupMessage { get; set; }
}

// ... 

await client.SetMethodHandlerAsync("start", (MethodRequest req, object ctx) =>
{
  var startRequest = JsonConvert.DeserializeObject<startRequest>(req.DataAsJson);
  Console.WriteLine($"Received start command with priority ${startRequest.startPriority} and ${startRequest.startMessage}");

  var startResponse = new startResponse
  {
    startupTime = 123,
    startupMessage = "device started with message " + startRequest.startMessage
  };

  string responsePayload = JsonConvert.SerializeObject(startResponse);
  MethodResponse response = new MethodResponse(Encoding.UTF8.GetBytes(responsePayload), 200);
  return Task.FromResult(response);
},null);
```

> [!Tip]
> 要求と応答の名前は、ネットワーク経由で送信されるシリアル化されたペイロードには存在しません。

## <a name="interact-with-the-device"></a>デバイスと対話する 

IoT プラグ アンド プレイでは、モデル ID をアナウンスしたデバイスを IoT ハブで使用できます。 たとえば、デバイスのプロパティとコマンドに直接アクセスできます。

IoT ハブに接続されている IoT プラグ アンド プレイ デバイスを使用するには、IoT Hub REST API またはいずれかの IoT 言語 SDK を使用します。 次の例では、IoT Hub REST API を使用します。 API の最新バージョンは `2020-05-31-preview` です。 REST PI 呼び出しに `?api-version=2020-05-31` を追加してください。

サーモスタット デバイスが `t-123` の場合、次の REST API GET 呼び出しにより、デバイスによって実装されているすべてのインターフェイス上のすべてのプロパティが取得されます。

```REST
GET /digitalTwins/t-123
```

この呼び出しには、デバイスからアナウンスされたモデル ID の Json プロパティ `$metadata.$model` が含まれます。

`GET /DigitalTwin/{device-id}` REST API テンプレートを使用することで、すべてのインターフェイス上のすべてのプロパティにアクセスできます。ここで、`{device-id}` はデバイスの識別子です。

```REST
GET /digitalTwins/{device-id}
```

IoT プラグ アンド プレイ デバイス コマンドを直接呼び出すことができます。 `t-123` デバイスの `Thermostat` コンポーネントに `restart` コマンドがある場合は、REST API POST 呼び出しを使用して呼び出すことができます。

```REST
POST /digitalTwins/t-123/components/Thermostat/commands/restart
```

より一般的に、コマンドはこの REST API テンプレートを使用して呼び出すことができます。

- `device-id`: デバイスの識別子。
- `component-name`: デバイス機能モデルの implements セクションのインターフェイスの名前。
- `command-name`: コマンドの名前。

```REST
/digitalTwins/{device-id}/components/{component-name}/commands/{command-name}
```

## <a name="next-steps"></a>次のステップ

ここまでで、デバイスのモデリングについて学習しました。その他のリソースを次に示します。

- [Digital Twins Definition Language (DTDL)](https://github.com/Azure/opendigitaltwins-dtdl)
- [C デバイス SDK](https://docs.microsoft.com/azure/iot-hub/iot-c-sdk-ref/)
- [IoT REST API](https://docs.microsoft.com/rest/api/iothub/device)
- [モデル コンポーネント](./concepts-components.md)
