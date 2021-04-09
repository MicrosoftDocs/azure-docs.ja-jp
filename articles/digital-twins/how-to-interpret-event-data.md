---
title: イベント データを解釈する
titleSuffix: Azure Digital Twins
description: さまざまなイベントの種類と、その異なる通知メッセージを解釈する方法を参照してください。
author: baanders
ms.author: baanders
ms.date: 6/23/2020
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: a0f2b971eae5d37e8fb0771e213075289af6c519
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "98045259"
---
# <a name="understand-event-data"></a>イベント データについて

Azure Digital Twins のさまざまなイベントでは **通知** が生成されます。これにより、さまざまなアクションが発生したときにソリューション バックエンドが認識されるようになります。 これらは、この情報を基に操作が実行される Azure Digital Twins 内外のさまざまな場所に[ルーティング](concepts-route-events.md)されます。

生成される通知にはいくつかの種類があり、通知メッセージの表示は、その通知の生成元のイベントの種類によって異なる場合があります。 この記事では、さまざまな種類のメッセージについての詳細と、表示される内容について説明します。

この表は、さまざまな通知の種類を示しています。

[!INCLUDE [digital-twins-notifications.md](../../includes/digital-twins-notifications.md)]

一般的に、通知はヘッダーと本文の 2 つの部分で構成されます。 

### <a name="event-notification-headers"></a>イベント通知ヘッダー

通知メッセージ ヘッダーは、キーと値のペアで表されます。 メッセージ ヘッダーは、使用されるプロトコル (MQTT、AMQP、または HTTP) に応じて異なる方法でシリアル化されます。 このセクションでは、選択した特定のプロトコルとシリアル化に関係しない、通知メッセージの一般的なヘッダー情報について説明します。

一部の通知は [CloudEvents](https://cloudevents.io/) 標準に準拠しています。 CloudEvents 準拠は次のとおりです。
* デバイスから生成された通知が、引き続き通知の既存の仕様に従っている
* IoT Hub によって処理および出力された通知が、引き続き通知の既存の仕様に従っている。ただし、IoT Hub で Event Grid などの CloudEvents をサポートすることを選択した場合を除く
* [モデル](concepts-models.md)がある [Digital Twins](concepts-twins-graph.md) から生成された通知は、CloudEvents に準拠している
* Azure Digital Twins によって処理および出力された通知は、CloudEvents に準拠している

サービスでは、通知の順序を示すため、そのすべてにシーケンス番号を追加するか、他の独自の方法で順序を維持する必要があります。 

Azure Digital Twins から Event Grid に出力される通知は、Event Grid トピックで定義されているスキーマの種類に応じて、CloudEvents スキーマまたは EventGridEvent スキーマのいずれかに自動的にフォーマットされます。 

ヘッダーの拡張属性は、ペイロード内の Event Grid スキーマのプロパティとして追加されます。 

### <a name="event-notification-bodies"></a>イベント通知本文

ここでは、通知メッセージの本文を JSON で説明します。 メッセージ本文に必要なシリアル化 (JSON、CBOR、Protobuf など) に応じて、メッセージ本文をシリアル化する方法は異なる場合があります。

本文に含まれる一連のフィールドは、通知の種類によって異なります。 次の 2 つのサンプル メッセージ本文では、表示される一般的な内容と、含まれる可能な内容を把握できます。

テレメトリ メッセージ:

```json
{
  "specversion": "1.0",
  "id": "df5a5992-817b-4e8a-b12c-e0b18d4bf8fb",
  "type": "microsoft.iot.telemetry",
  "source": "contoso-adt.api.wus2.digitaltwins.azure.net/digitaltwins/room1",
  "data": {
    "Temperature": 10
  },
  "dataschema": "dtmi:example:com:floor4;2",
  "datacontenttype": "application/json",
  "traceparent": "00-7e3081c6d3edfb4eaf7d3244b2036baa-23d762f4d9f81741-01"
}
```

ライフサイクルの通知メッセージ:

```json
{
  "specversion": "1.0",
  "id": "d047e992-dddc-4a5a-b0af-fa79832235f8",
  "type": "Microsoft.DigitalTwins.Twin.Create",
  "source": "contoso-adt.api.wus2.digitaltwins.azure.net",
  "data": {
    "$dtId": "floor1",
    "$etag": "W/\"e398dbf4-8214-4483-9d52-880b61e491ec\"",
    "$metadata": {
      "$model": "dtmi:example:Floor;1"
    }
  },
  "subject": "floor1",
  "time": "2020-06-23T19:03:48.9700792Z",
  "datacontenttype": "application/json",
  "traceparent": "00-18f4e34b3e4a784aadf5913917537e7d-691a71e0a220d642-01"
}
```

## <a name="message-format-detail-for-different-event-types"></a>さまざまなイベントの種類のメッセージ形式の詳細

このセクションでは、IoT Hub と Azure Digital Twins (またはその他の Azure IoT サービス) によって生成されるさまざまな種類の通知について詳しく説明します。 各通知の種類のトリガーとなるものと、通知本文の種類ごとに含まれる一連のフィールドについて説明します。

### <a name="digital-twin-life-cycle-notifications"></a>デジタル ツインのライフサイクル通知

すべての[デジタル ツイン](concepts-twins-graph.md)は、[Azure Digital Twins 内の IoT Hub デバイス](how-to-ingest-iot-hub-data.md)を表すかどうかにかかわらず、通知を出力します。 これは、デジタル ツイン自体に関する **ライフサイクル通知** が理由です。

ライフサイクル通知は、次の場合にトリガーされます。
* デジタル ツインの作成
* デジタル ツインの削除

#### <a name="properties"></a>Properties

ライフサイクル通知の本文に含まれるフィールドを次に示します。

| 名前 | 値 |
| --- | --- |
| `id` | サービスによって管理される UUID やカウンターなどの、通知の識別子。 `source` + `id` は、個別のイベントごとに一意です。 |
| `source` | IoT ハブや Azure Digital Twins インスタンスの名前 (*myhub.azure-devices.net* や *mydigitaltwins.westus2.azuredigitaltwins.net など)* |
| `specversion` | *1.0*<br>メッセージは、[CloudEvents 標準](https://github.com/cloudevents/spec)のこのバージョンに準拠しています。 |
| `type` | `Microsoft.DigitalTwins.Twin.Create`<br>`Microsoft.DigitalTwins.Twin.Delete` |
| `datacontenttype` | `application/json` |
| `subject` | デジタル ツインの ID |
| `time` | ツインで操作が発生したときのタイムスタンプ |
| `traceparent` | イベントの W3C トレース コンテキスト |

#### <a name="body-details"></a>本文の詳細

本文は、影響を受けるデジタル ツインで、JSON 形式で表されます。 このスキーマは、*Digital Twins リソース 7.1* です。

作成イベントの場合、ペイロードには、リソースが作成された後のツインの状態が反映されます。したがって、`GET` 呼び出しと同様に、システムによって生成されたすべての要素を含める必要があります。

コンポーネントと最上位プロパティがない、[IoT プラグ アンド プレイ (PnP)](../iot-pnp/overview-iot-plug-and-play.md) デバイスの本文の例を次に示します。 デバイスにとって意味のないプロパティ (報告されたプロパティなど) は、省略する必要があります。

```json
{
  "$dtId": "device-digitaltwin-01",
  "$etag": "W/\"e59ce8f5-03c0-4356-aea9-249ecbdc07f9\"",
  "thermostat": {
    "temperature": 80,
    "humidity": 45,
    "$metadata": {
      "$model": "dtmi:com:contoso:Thermostat;1",
      "temperature": {
        "desiredValue": 85,
        "desiredVersion": 3,
        "ackVersion": 2,
        "ackCode": 200,
        "ackDescription": "OK"
      },
      "humidity": {
        "desiredValue": 40,
        "desiredVersion": 1,
        "ackVersion": 1,
        "ackCode": 200,
        "ackDescription": "OK"
      }
    }
  },
  "$metadata": {
    "$model": "dtmi:com:contoso:Thermostat_X500;1",
  }
}
```

デジタル ツインのもう 1 つの例を次に示します。 これは[モデル](concepts-models.md)に基づいており、コンポーネントはサポートされません。

```json
{
  "$dtId": "logical-digitaltwin-01",
  "$etag": "W/\"e59ce8f5-03c0-4356-aea9-249ecbdc07f9\"",
  "avgTemperature": 70,
  "comfortIndex": 85,
  "$metadata": {
    "$model": "dtmi:com:contoso:Building;1",
    "avgTemperature": {
      "desiredValue": 72,
      "desiredVersion": 5,
      "ackVersion": 4,
      "ackCode": 200,
      "ackDescription": "OK"
    },
    "comfortIndex": {
      "desiredValue": 90,
      "desiredVersion": 1,
      "ackVersion": 3,
      "ackCode": 200,
      "ackDescription": "OK"
    }
  }
}
```

### <a name="digital-twin-relationship-change-notifications"></a>デジタル ツインのリレーションシップの変更通知

**リレーションシップの変更通知** は、デジタル ツインのリレーションシップが作成、更新、または削除されたときにトリガーされます。 

#### <a name="properties"></a>Properties

エッジ変更通知の本文に含まれるフィールドを次に示します。

| 名前    | 値 |
| --- | --- |
| `id` | サービスによって管理される UUID やカウンターなどの、通知の識別子。 `source` + `id` は個別のイベントごとに一意です |
| `source` | Azure Digital Twins インスタンスの名前 (*mydigitaltwins.westus2.azuredigitaltwins.net など)* |
| `specversion` | *1.0*<br>メッセージは、[CloudEvents 標準](https://github.com/cloudevents/spec)のこのバージョンに準拠しています。 |
| `type` | `Microsoft.DigitalTwins.Relationship.Create`<br>`Microsoft.DigitalTwins.Relationship.Update`<br>`Microsoft.DigitalTwins.Relationship.Delete`
|`datacontenttype`| `application/json` |
| `subject` | リレーションシップの ID (`<twinID>/relationships/<relationshipID>` など) |
| `time` | リレーションシップで操作が発生したときのタイムスタンプ |
| `traceparent` | イベントの W3C トレース コンテキスト |

#### <a name="body-details"></a>本文の詳細

本文は、リレーションシップのペイロードで、これも JSON 形式です。 これは、[DigitalTwins API](/rest/api/digital-twins/dataplane/twins) を介して、リレーションシップの `GET` 要求と同じ形式を使用します。 

"リレーションシップの更新" とは、リレーションシップのプロパティが変更されたことを意味します。 

プロパティを更新するためのリレーションシップの更新の通知を示す例を次に示します。

```json
{
    "modelId": "dtmi:example:Floor;1",
    "patch": [
      {
        "value": "user3",
        "path": "/ownershipUser",
        "op": "replace"
      }
    ]
  }
```

`Relationship.Delete` の場合、本文は `GET` 要求と同じであり、削除前の最新の状態が取得されます。

リレーションシップの作成または削除の通知の例を次に示します。

```json
{
    "$relationshipId": "device_to_device",
    "$etag": "W/\"72479873-0083-41a8-83e2-caedb932d881\"",
    "$relationshipName": "Connected",
    "$targetId": "device2",
    "connectionType": "WIFI"
}
```

### <a name="digital-twin-change-notifications"></a>デジタル ツインの変更通知

**デジタル ツインの変更通知** は、次のようなデジタル ツインの更新時にトリガーされます。
* プロパティ値またはメタデータが変更した場合。
* デジタル ツインまたはコンポーネント メタデータが変更された場合。 このシナリオの例として、デジタル ツインのモデルの変更が挙げられます。

#### <a name="properties"></a>Properties

デジタル ツイン変更通知の本文に含まれるフィールドを次に示します。

| 名前    | 値 |
| --- | --- |
| `id` | サービスによって管理される UUID やカウンターなどの、通知の識別子。 `source` + `id` は個別のイベントごとに一意です |
| `source` | IoT ハブや Azure Digital Twins インスタンスの名前 (*myhub.azure-devices.net* や *mydigitaltwins.westus2.azuredigitaltwins.net など)*
| `specversion` | *1.0*<br>メッセージは、[CloudEvents 標準](https://github.com/cloudevents/spec)のこのバージョンに準拠しています。 |
| `type` | `Microsoft.DigitalTwins.Twin.Update` |
| `datacontenttype` | `application/json` |
| `subject` | デジタル ツインの ID |
| `time` | デジタル ツインで操作が発生したときのタイムスタンプ |
| `traceparent` | イベントの W3C トレース コンテキスト |

#### <a name="body-details"></a>本文の詳細

`Twin.Update` 通知の本文は、デジタル ツインの更新が含まれる JSON パッチ ドキュメントです。

たとえば、次のパッチを使用してデジタル ツインが更新されたとします。

:::code language="json" source="~/digital-twins-docs-samples/models/patch-component-2.json":::

対応する通知 (Azure Digital Twins によるデジタル ツインの更新など、サービスによって同期的に実行される場合) の本文は次のようになります。

```json
{
    "modelId": "dtmi:example:com:floor4;2",
    "patch": [
      {
        "value": 40,
        "path": "/Temperature",
        "op": "replace"
      },
      {
        "value": 30,
        "path": "/comp1/prop1",
        "op": "add"
      }
    ]
  }
```

## <a name="next-steps"></a>次のステップ

イベントを配信するためのエンドポイントとルートを作成する方法を参照してください。
* [*方法: エンドポイントとルートを管理する*](how-to-manage-routes-apis-cli.md)

または、Azure Digital Twins の API と SDK オプションの詳細を確認してください。
* [*方法: Azure Digital Twins の API および SDK を使用する*](how-to-use-apis-sdks.md)