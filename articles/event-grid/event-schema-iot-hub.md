---
title: IoT Hub 用の Azure Event Grid スキーマ | Microsoft Docs
description: IoT Hub のイベント スキーマ形式とプロパティのリファレンス ページです
services: iot-hub
documentationcenter: ''
author: kgremban
manager: timlt
editor: ''
ms.service: event-grid
ms.topic: reference
ms.date: 01/17/2019
ms.author: kgremban
ms.openlocfilehash: 5fcd7c10002e7e1ae9683fdd89d3af14a1500050
ms.sourcegitcommit: 7e772d8802f1bc9b5eb20860ae2df96d31908a32
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/06/2019
ms.locfileid: "57449191"
---
# <a name="azure-event-grid-event-schema-for-iot-hub"></a>IoT Hub 用の Azure Event Grid イベント スキーマ

この記事では、Azure IoT Hub イベントのプロパティとスキーマについて説明します。 イベント スキーマの概要については、「[Azure Event Grid イベント スキーマ](event-schema.md)」を参照してください。 

サンプル スクリプトとチュートリアルの一覧については、[IoT Hub のイベント ソース](event-sources.md#iot-hub)に関する記事をご覧ください。

## <a name="available-event-types"></a>使用可能なイベントの種類

Azure IoT Hub から出力されるイベントの種類は次のとおりです。

| イベントの種類 | 説明 |
| ---------- | ----------- |
| Microsoft.Devices.DeviceCreated | デバイスが IoT Hub に登録されると発行されます。 |
| Microsoft.Devices.DeviceDeleted | デバイスが IoT Hub から削除されると発行されます。 | 
| Microsoft.Devices.DeviceConnected | デバイスが IoT Hub に接続されると発行されます。 |
| Microsoft.Devices.DeviceDisconnected | デバイスが IoT Hub から切断されると発行されます。 | 

## <a name="example-event"></a>イベントの例

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

### <a name="event-properties"></a>イベントのプロパティ

すべてのイベントには、同じ最上位レベルのデータが格納されます。 

| プロパティ | type | 説明 |
| -------- | ---- | ----------- |
| id | 文字列 | イベントの一意識別子。 |
| topic | 文字列 | イベント ソースの完全なリソース パス。 このフィールドは書き込み可能ではありません。 この値は Event Grid によって指定されます。 |
| subject | 文字列 | 発行元が定義したイベントの対象のパス。 |
| eventType | 文字列 | このイベント ソース用に登録されたイベントの種類のいずれか。 |
| eventTime | 文字列 | プロバイダーの UTC 時刻に基づくイベントの生成時刻。 |
| data | オブジェクト | IoT Hub イベントのデータ。  |
| dataVersion | 文字列 | データ オブジェクトのスキーマ バージョン。 スキーマ バージョンは発行元によって定義されます。 |
| metadataVersion | 文字列 | イベント メタデータのスキーマ バージョン。 最上位プロパティのスキーマは Event Grid によって定義されます。 この値は Event Grid によって指定されます。 |

すべての IoT Hub イベントの場合、データ オブジェクトには次のプロパティが含まれます。

| プロパティ | type | 説明 |
| -------- | ---- | ----------- |
| hubName | 文字列 | デバイスが作成または削除された IoT Hub の名前。 |
| deviceId | 文字列 | デバイスの一意識別子。 この文字列は大文字と小文字が区別され、最大 128 文字まで指定でき、ASCII 7 ビットの英数字と、特殊文字 (`- : . + % _ # * ? ! ( ) , = @ ; $ '`) を使うことができます。 |

データ オブジェクトの内容は、イベント発行元ごとに異なります。 **デバイス接続**および**デバイス切断** IoT Hub イベントの場合、データ オブジェクトには次のプロパティが含まれます。

| プロパティ | type | 説明 |
| -------- | ---- | ----------- |
| moduleId | 文字列 | モジュールの一意の識別子。 このフィールドは、モジュール デバイスに対してのみ出力されます。 この文字列は大文字と小文字が区別され、最大 128 文字まで指定でき、ASCII 7 ビットの英数字と、特殊文字 (`- : . + % _ # * ? ! ( ) , = @ ; $ '`) を使うことができます。 |
| deviceConnectionStateEventInfo | オブジェクト | デバイス接続状態イベント情報
| sequenceNumber | 文字列 | デバイス接続イベントまたはデバイス切断イベントの順序を示すのに役立つ数字。 最新のイベントには、前のイベントよりも大きいシーケンス番号が与えられます。 この数字は 1 を超えて変更されることがありますが、狭義に増加します。 [シーケンス番号の使用方法](../iot-hub/iot-hub-how-to-order-connection-state-events.md)に関するページを参照してください。 |

データ オブジェクトの内容は、イベント発行元ごとに異なります。 **デバイス接続**および**デバイス削除** IoT Hub イベントの場合、データ オブジェクトには次のプロパティが含まれます。

| プロパティ | type | 説明 |
| -------- | ---- | ----------- |
| twin | オブジェクト | デバイス ツインについての情報。アプリケーション デバイス メタデータのクラウド表現です。 | 
| deviceID | 文字列 | デバイス ツインの一意識別子。 | 
| etag | 文字列 | デバイス ツインへの更新の一貫性を確保するための検証コントロール。 各 etag は、デバイス ツインごとに一意であることが保証されます。 |  
| deviceEtag| 文字列 | デバイス レジストリへの更新の一貫性を確保するための検証コントロール。 各 deviceEtag は、デバイス レジストリごとに一意であることが保証されます。 |
| status | 文字列 | デバイス ツインが有効か無効か。 | 
| statusUpdateTime | 文字列 | デバイス ツインの状態が最後に更新されたときの ISO8601 タイムスタンプ。 |
| connectionState | 文字列 | デバイスが接続されているか切断されているか。 | 
| lastActivityTime | 文字列 | 最後のアクティビティの ISO8601 タイムスタンプ。 | 
| cloudToDeviceMessageCount | integer | このデバイスに送信された、クラウドからデバイスへのメッセージの数。 | 
| authenticationType | 文字列 | このデバイスに使われる認証の種類 (`SAS`、`SelfSigned`、または `CertificateAuthority`)。 |
| x509Thumbprint | 文字列 | サムプリントは x509 証明書の一意の値であり、証明書ストアで特定の証明書を検索するためによく使われます。 サムプリントは、SHA1 アルゴリズムを使って動的に生成され、証明書に物理的に存在することはありません。 | 
| primaryThumbprint | 文字列 | x509 証明書のプライマリ サムプリント。 |
| secondaryThumbprint | 文字列 | x509 証明書のセカンダリ サムプリント。 | 
| version | integer | デバイス ツインが更新されるたびに 1 ずつ増加する整数値。 |
| desired | オブジェクト | アプリケーション バックエンドだけが書き込むことができ、デバイスで読み取ることができる、プロパティの一部分。 | 
| reported | オブジェクト | デバイスだけが書き込むことができ、アプリケーション バックエンドで読み取ることができる、プロパティの一部分。 |
| lastUpdated | 文字列 | デバイス ツインのプロパティが最後に更新されたときの ISO8601 タイムスタンプ。 | 

## <a name="next-steps"></a>次の手順

* Azure Event Grid の概要については、[Event Grid の紹介](overview.md)に関する記事を参照してください。
* IoT Hub と Event Grid の連携方法については、「[Event Grid を使用し IoT Hub のイベントに対応してアクションをトリガーする](../iot-hub/iot-hub-event-grid.md)」をご覧ください。