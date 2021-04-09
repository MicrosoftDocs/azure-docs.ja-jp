---
title: Event Grid ソースとしての Azure IoT Hub
description: この記事では、Azure IoT Hub イベントのプロパティとスキーマについて説明します。 使用可能なイベントの種類、イベントの例、およびイベントのプロパティが一覧表示されます。
ms.topic: conceptual
ms.date: 02/11/2021
ms.openlocfilehash: 5f43b9d0041fa5842bc2557a61c5145ce588758a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "100363528"
---
# <a name="azure-iot-hub-as-an-event-grid-source"></a>Event Grid ソースとしての Azure IoT Hub
この記事では、Azure IoT Hub イベントのプロパティとスキーマについて説明します。 イベント スキーマの概要については、「[Azure Event Grid イベント スキーマ](event-schema.md)」を参照してください。 

## <a name="available-event-types"></a>使用可能なイベントの種類

Azure IoT Hub から出力されるイベントの種類は次のとおりです。

| イベントの種類 | 説明 |
| ---------- | ----------- |
| Microsoft.Devices.DeviceCreated | デバイスが IoT Hub に登録されると発行されます。 |
| Microsoft.Devices.DeviceDeleted | デバイスが IoT Hub から削除されると発行されます。 | 
| Microsoft.Devices.DeviceConnected | デバイスが IoT Hub に接続されると発行されます。 |
| Microsoft.Devices.DeviceDisconnected | デバイスが IoT Hub から切断されると発行されます。 | 
| Microsoft.Devices.DeviceTelemetry | 利用統計情報が IoT Hub に送信されると発行されます。 |

## <a name="example-event"></a>イベントの例

# <a name="event-grid-event-schema"></a>[Event Grid イベント スキーマ](#tab/event-grid-event-schema)

DeviceConnected イベントと DeviceDisconnected イベントのスキーマは同じ構造です。 このサンプル イベントでは、デバイスが IoT Hub に接続されると発生するイベントのスキーマを示します。

```json
[{
  "id": "f6bbf8f4-d365-520d-a878-17bf7238abd8", 
  "topic": "/SUBSCRIPTIONS/<subscription ID>/RESOURCEGROUPS/<resource group name>/PROVIDERS/MICROSOFT.DEVICES/IOTHUBS/<hub name>", 
  "subject": "devices/LogicAppTestDevice", 
  "eventType": "Microsoft.Devices.DeviceConnected", 
  "eventTime": "2018-06-02T19:17:44.4383997Z", 
  "data": {
    "deviceConnectionStateEventInfo": {
      "sequenceNumber":
        "000000000000000001D4132452F67CE200000002000000000000000000000001"
    },
    "hubName": "egtesthub1",
    "deviceId": "LogicAppTestDevice",
    "moduleId" : "DeviceModuleID"
  }, 
  "dataVersion": "1", 
  "metadataVersion": "1" 
}]
```

DeviceTelemetry イベントは、利用統計情報が IoT Hub に送信されると発生します。 このイベントのサンプル スキーマを次に示します。

```json
[{
  "id": "9af86784-8d40-fe2g-8b2a-bab65e106785",
  "topic": "/SUBSCRIPTIONS/<subscription ID>/RESOURCEGROUPS/<resource group name>/PROVIDERS/MICROSOFT.DEVICES/IOTHUBS/<hub name>", 
  "subject": "devices/LogicAppTestDevice", 
  "eventType": "Microsoft.Devices.DeviceTelemetry",
  "eventTime": "2019-01-07T20:58:30.48Z",
  "data": {        
      "body": {            
          "Weather": {                
              "Temperature": 900            
          },
          "Location": "USA"        
      },
        "properties": {            
          "Status": "Active"        
        },
        "systemProperties": {            
            "iothub-content-type": "application/json",
            "iothub-content-encoding": "utf-8",
            "iothub-connection-device-id": "d1",
            "iothub-connection-auth-method": "{\"scope\":\"device\",\"type\":\"sas\",\"issuer\":\"iothub\",\"acceptingIpFilterRule\":null}",
            "iothub-connection-auth-generation-id": "123455432199234570",
            "iothub-enqueuedtime": "2019-01-07T20:58:30.48Z",
            "iothub-message-source": "Telemetry"        
        }    
    },
  "dataVersion": "",
  "metadataVersion": "1"
}]
```

DeviceCreated イベントと DeviceDeleted イベントのスキーマは同じ構造です。 このサンプル イベントでは、デバイスが IoT Hub に登録されると発生するイベントのスキーマを示します。

```json
[{
  "id": "56afc886-767b-d359-d59e-0da7877166b2",
  "topic": "/SUBSCRIPTIONS/<subscription ID>/RESOURCEGROUPS/<resource group name>/PROVIDERS/MICROSOFT.DEVICES/IOTHUBS/<hub name>",
  "subject": "devices/LogicAppTestDevice",
  "eventType": "Microsoft.Devices.DeviceCreated",
  "eventTime": "2018-01-02T19:17:44.4383997Z",
  "data": {
    "twin": {
      "deviceId": "LogicAppTestDevice",
      "etag": "AAAAAAAAAAE=",
      "deviceEtag": "null",
      "status": "enabled",
      "statusUpdateTime": "0001-01-01T00:00:00",
      "connectionState": "Disconnected",
      "lastActivityTime": "0001-01-01T00:00:00",
      "cloudToDeviceMessageCount": 0,
      "authenticationType": "sas",
      "x509Thumbprint": {
        "primaryThumbprint": null,
        "secondaryThumbprint": null
      },
      "version": 2,
      "properties": {
        "desired": {
          "$metadata": {
            "$lastUpdated": "2018-01-02T19:17:44.4383997Z"
          },
          "$version": 1
        },
        "reported": {
          "$metadata": {
            "$lastUpdated": "2018-01-02T19:17:44.4383997Z"
          },
          "$version": 1
        }
      }
    },
    "hubName": "egtesthub1",
    "deviceId": "LogicAppTestDevice"
  },
  "dataVersion": "1",
  "metadataVersion": "1"
}]
```

# <a name="cloud-event-schema"></a>[クラウド イベント スキーマ](#tab/cloud-event-schema)

DeviceConnected イベントと DeviceDisconnected イベントのスキーマは同じ構造です。 このサンプル イベントでは、デバイスが IoT Hub に接続されると発生するイベントのスキーマを示します。

```json
[{
  "id": "f6bbf8f4-d365-520d-a878-17bf7238abd8", 
  "source": "/SUBSCRIPTIONS/<subscription ID>/RESOURCEGROUPS/<resource group name>/PROVIDERS/MICROSOFT.DEVICES/IOTHUBS/<hub name>", 
  "subject": "devices/LogicAppTestDevice", 
  "type": "Microsoft.Devices.DeviceConnected", 
  "time": "2018-06-02T19:17:44.4383997Z", 
  "data": {
    "deviceConnectionStateEventInfo": {
      "sequenceNumber":
        "000000000000000001D4132452F67CE200000002000000000000000000000001"
    },
    "hubName": "egtesthub1",
    "deviceId": "LogicAppTestDevice",
    "moduleId" : "DeviceModuleID"
  }, 
  "specversion": "1.0"
}]
```

DeviceTelemetry イベントは、利用統計情報が IoT Hub に送信されると発生します。 このイベントのサンプル スキーマを次に示します。

```json
[{
  "id": "9af86784-8d40-fe2g-8b2a-bab65e106785",
  "source": "/SUBSCRIPTIONS/<subscription ID>/RESOURCEGROUPS/<resource group name>/PROVIDERS/MICROSOFT.DEVICES/IOTHUBS/<hub name>", 
  "subject": "devices/LogicAppTestDevice", 
  "type": "Microsoft.Devices.DeviceTelemetry",
  "time": "2019-01-07T20:58:30.48Z",
  "data": {        
      "body": {            
          "Weather": {                
              "Temperature": 900            
          },
          "Location": "USA"        
      },
        "properties": {            
          "Status": "Active"        
        },
        "systemProperties": {            
            "iothub-content-type": "application/json",
            "iothub-content-encoding": "utf-8",
            "iothub-connection-device-id": "d1",
            "iothub-connection-auth-method": "{\"scope\":\"device\",\"type\":\"sas\",\"issuer\":\"iothub\",\"acceptingIpFilterRule\":null}",
            "iothub-connection-auth-generation-id": "123455432199234570",
            "iothub-enqueuedtime": "2019-01-07T20:58:30.48Z",
            "iothub-message-source": "Telemetry"        
        }    
    },
  "specversion": "1.0"
}]
```

DeviceCreated イベントと DeviceDeleted イベントのスキーマは同じ構造です。 このサンプル イベントでは、デバイスが IoT Hub に登録されると発生するイベントのスキーマを示します。

```json
[{
  "id": "56afc886-767b-d359-d59e-0da7877166b2",
  "source": "/SUBSCRIPTIONS/<subscription ID>/RESOURCEGROUPS/<resource group name>/PROVIDERS/MICROSOFT.DEVICES/IOTHUBS/<hub name>",
  "subject": "devices/LogicAppTestDevice",
  "type": "Microsoft.Devices.DeviceCreated",
  "time": "2018-01-02T19:17:44.4383997Z",
  "data": {
    "twin": {
      "deviceId": "LogicAppTestDevice",
      "etag": "AAAAAAAAAAE=",
      "deviceEtag": "null",
      "status": "enabled",
      "statusUpdateTime": "0001-01-01T00:00:00",
      "connectionState": "Disconnected",
      "lastActivityTime": "0001-01-01T00:00:00",
      "cloudToDeviceMessageCount": 0,
      "authenticationType": "sas",
      "x509Thumbprint": {
        "primaryThumbprint": null,
        "secondaryThumbprint": null
      },
      "version": 2,
      "properties": {
        "desired": {
          "$metadata": {
            "$lastUpdated": "2018-01-02T19:17:44.4383997Z"
          },
          "$version": 1
        },
        "reported": {
          "$metadata": {
            "$lastUpdated": "2018-01-02T19:17:44.4383997Z"
          },
          "$version": 1
        }
      }
    },
    "hubName": "egtesthub1",
    "deviceId": "LogicAppTestDevice"
  },
  "specversion": "1.0"
}]
```

---


### <a name="event-properties"></a>イベントのプロパティ

# <a name="event-grid-event-schema"></a>[Event Grid イベント スキーマ](#tab/event-grid-event-schema)

すべてのイベントには、同じ最上位レベルのデータが格納されます。 

| プロパティ | Type | 説明 |
| -------- | ---- | ----------- |
| `id` | string | イベントの一意識別子。 |
| `topic` | string | イベント ソースの完全なリソース パス。 このフィールドは書き込み可能ではありません。 この値は Event Grid によって指定されます。 |
| `subject` | string | 発行元が定義したイベントの対象のパス。 |
| `eventType` | string | このイベント ソース用に登録されたイベントの種類のいずれか。 |
| `eventTime` | string | プロバイダーの UTC 時刻に基づくイベントの生成時刻。 |
| `data` | object | IoT Hub イベントのデータ。  |
| `dataVersion` | string | データ オブジェクトのスキーマ バージョン。 スキーマ バージョンは発行元によって定義されます。 |
| `metadataVersion` | string | イベント メタデータのスキーマ バージョン。 最上位プロパティのスキーマは Event Grid によって定義されます。 この値は Event Grid によって指定されます。 |

# <a name="cloud-event-schema"></a>[クラウド イベント スキーマ](#tab/cloud-event-schema)

すべてのイベントには、同じ最上位レベルのデータが格納されます。 


| プロパティ | Type | 説明 |
| -------- | ---- | ----------- |
| `id` | string | イベントの一意識別子。 |
| `source` | string | イベント ソースの完全なリソース パス。 このフィールドは書き込み可能ではありません。 この値は Event Grid によって指定されます。 |
| `subject` | string | 発行元が定義したイベントの対象のパス。 |
| `type` | string | このイベント ソース用に登録されたイベントの種類のいずれか。 |
| `time` | string | プロバイダーの UTC 時刻に基づくイベントの生成時刻。 |
| `data` | object | IoT Hub イベントのデータ。  |
| `specversion` | string | CloudEvents スキーマ仕様バージョン。 |

---

すべての IoT Hub イベントの場合、データ オブジェクトには次のプロパティが含まれます。

| プロパティ | Type | 説明 |
| -------- | ---- | ----------- |
| `hubName` | string | デバイスが作成または削除された IoT Hub の名前。 |
| `deviceId` | string | デバイスの一意識別子。 この文字列は大文字と小文字が区別され、最大 128 文字まで指定でき、ASCII 7 ビットの英数字と、特殊文字 (`- : . + % _ # * ? ! ( ) , = @ ; $ '`) を使うことができます。 |

データ オブジェクトの内容は、イベント発行元ごとに異なります。 

**デバイス接続** および **デバイス切断** IoT Hub イベントの場合、データ オブジェクトには次のプロパティが含まれます。

| プロパティ | Type | 説明 |
| -------- | ---- | ----------- |
| `moduleId` | string | モジュールの一意の識別子。 このフィールドは、モジュール デバイスに対してのみ出力されます。 この文字列は大文字と小文字が区別され、最大 128 文字まで指定でき、ASCII 7 ビットの英数字と、特殊文字 (`- : . + % _ # * ? ! ( ) , = @ ; $ '`) を使うことができます。 |
| `deviceConnectionStateEventInfo` | object | デバイス接続状態イベント情報
| `sequenceNumber` | string | デバイス接続イベントまたはデバイス切断イベントの順序を示すのに役立つ数字。 最新のイベントには、前のイベントよりも大きいシーケンス番号が与えられます。 この数字は 1 を超えて変更されることがありますが、狭義に増加します。 [シーケンス番号の使用方法](../iot-hub/iot-hub-how-to-order-connection-state-events.md)に関するページを参照してください。 |

**デバイス テレメトリ** IoT Hub イベントでは、データ オブジェクトに [IoT ハブ メッセージ形式](../iot-hub/iot-hub-devguide-messages-construct.md)の device-to-cloud メッセージが含まれ、次のプロパティを含みます。

| プロパティ | Type | 説明 |
| -------- | ---- | ----------- |
| `body` | string | デバイスからのメッセージの内容。 |
| `properties` | string | アプリケーション プロパティは、メッセージに追加できるユーザー定義の文字列です。 これらのフィールドは省略可能です。 |
| `system properties` | string | [システム プロパティ](../iot-hub/iot-hub-devguide-routing-query-syntax.md#system-properties)は、メッセージのコンテンツとソースを特定するのに役立ちます。 デバイス テレメトリ メッセージは、メッセージ システム プロパティで contentType が JSON に設定され、contentEncoding が UTF-8 に設定された有効な JSON 形式でなければなりません。 これが設定されていない場合、IoT Hub は Base 64 エンコード形式でメッセージを書き込みます。  |

**デバイス接続** および **デバイス削除** IoT Hub イベントの場合、データ オブジェクトには次のプロパティが含まれます。

| プロパティ | Type | 説明 |
| -------- | ---- | ----------- |
| `twin` | object | デバイス ツインについての情報。アプリケーション デバイス メタデータのクラウド表現です。 | 
| `deviceID` | string | デバイス ツインの一意識別子。 | 
| `etag` | string | デバイス ツインへの更新の一貫性を確保するための検証コントロール。 各 etag は、デバイス ツインごとに一意であることが保証されます。 |  
| `deviceEtag` | string | デバイス レジストリへの更新の一貫性を確保するための検証コントロール。 各 deviceEtag は、デバイス レジストリごとに一意であることが保証されます。 |
| `status` | string | デバイス ツインが有効か無効か。 | 
| `statusUpdateTime` | string | デバイス ツインの状態が最後に更新されたときの ISO8601 タイムスタンプ。 |
| `connectionState` | string | デバイスが接続されているか切断されているか。 | 
| `lastActivityTime` | string | 最後のアクティビティの ISO8601 タイムスタンプ。 | 
| `cloudToDeviceMessageCount` | 整数 (integer) | このデバイスに送信された、クラウドからデバイスへのメッセージの数。 | 
| `authenticationType` | string | このデバイスに使われる認証の種類 (`SAS`、`SelfSigned`、または `CertificateAuthority`)。 |
| `x509Thumbprint` | string | サムプリントは x509 証明書の一意の値であり、証明書ストアで特定の証明書を検索するためによく使われます。 サムプリントは、SHA1 アルゴリズムを使って動的に生成され、証明書に物理的に存在することはありません。 | 
| `primaryThumbprint` | string | x509 証明書のプライマリ サムプリント。 |
| `secondaryThumbprint` | string | x509 証明書のセカンダリ サムプリント。 | 
| `version` | 整数 (integer) | デバイス ツインが更新されるたびに 1 ずつ増加する整数値。 |
| `desired` | object | アプリケーション バックエンドだけが書き込むことができ、デバイスで読み取ることができる、プロパティの一部分。 | 
| `reported` | object | デバイスだけが書き込むことができ、アプリケーション バックエンドで読み取ることができる、プロパティの一部分。 |
| `lastUpdated` | string | デバイス ツインのプロパティが最後に更新されたときの ISO8601 タイムスタンプ。 | 

## <a name="tutorials-and-how-tos"></a>チュートリアルと方法
|タイトル  |説明  |
|---------|---------|
| [Logic Apps を使用して Azure IoT Hub イベントに関する電子メール通知を送信する](publish-iot-hub-events-to-logic-apps.md) | ロジック アプリは、お使いの IoT Hub にデバイスが追加されるたびに、通知メールを送信します。 |
| [Event Grid を使用し IoT Hub のイベントに対応してアクションをトリガーする](../iot-hub/iot-hub-event-grid.md) | IoT Hub と Event Grid の統合の概要です。 |
| [デバイス接続イベントおよびデバイス切断イベントの順序を設定する](../iot-hub/iot-hub-how-to-order-connection-state-events.md) | デバイス接続状態イベントの順序付け方法を示します。 |

## <a name="next-steps"></a>次のステップ

* Azure Event Grid の概要については、[Event Grid の紹介](overview.md)に関する記事を参照してください。
* IoT Hub と Event Grid の連携方法については、「[Event Grid を使用し IoT Hub のイベントに対応してアクションをトリガーする](../iot-hub/iot-hub-event-grid.md)」をご覧ください。