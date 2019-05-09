---
title: インクルード ファイル
description: インクルード ファイル
services: iot-accelerators
author: dominicbetts
ms.service: iot-accelerators
ms.topic: include
ms.date: 07/26/2018
ms.author: dobett
ms.custom: include file
ms.openlocfilehash: 414bb0183e68cb46e52c379ea3f7aceda5d4170e
ms.sourcegitcommit: a65b424bdfa019a42f36f1ce7eee9844e493f293
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/04/2019
ms.locfileid: "55701184"
---
## <a name="the-parts-of-the-device-model-schema"></a>デバイス モデル スキーマの各パーツ

冷却装置やトラックなど、各デバイス モデルは、シミュレーション サービスがシミュレートできるデバイスの種類を定義します。 各デバイス モデルは、次の上位スキーマを含む JSON ファイルに格納されます。

```json
{
  "SchemaVersion": "1.0.0",
  "Id": "elevator-01",
  "Version": "0.0.1",
  "Name": "Elevator",
  "Description": "Elevator with floor, vibration and temperature sensors.",
  "Protocol": "AMQP",
  "Simulation": {
    // Specify the simulation behavior
  },
  "Properties": {
    // Define properties
  },
  "Telemetry": [
    // Specify telemetry
  ],
  "CloudToDeviceMethods": {
    // Specify methods
  }
}
```

既定のシミュレートされたデバイスのスキーマ ファイルは、GitHub 上の [devicemodels フォルダー](https://github.com/Azure/device-simulation-dotnet/tree/master/Services/data/devicemodels)に表示されます。

次の表では、上位スキーマのエントリを説明します。

| スキーマのエントリ | 説明 |
| -- | --- |
| `SchemaVersion` | スキーマ バージョンは常に `1.0.0` となり、このファイルの形式に固有です。 |
| `Id` | このデバイス モデルの一意の ID。 |
| `Version` | デバイス モデルのバージョンを識別します。 |
| `Name` | デバイス モデルのわかりやすい名前。 |
| `Description` | デバイス モデルの説明。 |
| `Protocol` | デバイスで使用される接続プロトコル。 `AMQP`、`MQTT`、`HTTP` のいずれかを指定できます。 |

以下のセクションでは、JSON スキーマのその他のセクションについて説明します。

## <a name="simulation"></a>シミュレーション

`Simulation` セクションでは、シミュレートされたデバイスの内部状態を定義します。 デバイスから送信されたテレメトリ値は、このデバイスの状態に含まれている必要があります。

デバイスの状態の定義には 2 つの要素があります。

* `InitialState` では、デバイス状態オブジェクトのすべてのプロパティの初期値を定義します。
* `Script` は、デバイスの状態を更新するためにスケジュールに従って実行される JavaScript ファイルを示します。 このスクリプト ファイルを使用すると、デバイスから送信されるテレメトリ値をランダム化することができます。

デバイス状態オブジェクトを更新する JavaScript ファイルの詳細については、[デバイスモデルの動作の概要](../articles/iot-accelerators/iot-accelerators-device-simulation-device-behavior.md)に関する記事を参照してください。

次の例では、シミュレートされた冷却装置デバイスのデバイス状態オブジェクトの定義を示します。

```json
"Simulation": {
  "InitialState": {
    "online": true,
    "temperature": 75.0,
    "temperature_unit": "F",
    "humidity": 70.0,
    "humidity_unit": "%",
    "pressure": 150.0,
    "pressure_unit": "psig",
    "simulation_state": "normal_pressure"
  },
  "Interval": "00:00:10",
  "Scripts": {
    "Type": "javascript",
    "Path": "chiller-01-state.js"
  }
}
```

シミュレーション サービスは、デバイスの状態を更新するために **chiller-01-state.js** ファイルを 5 秒ごとに実行します。 既定のシミュレートされたデバイスの JavaScript ファイルは、GitHub 上の [scripts フォルダー](https://github.com/Azure/device-simulation-dotnet/tree/master/Services/data/devicemodels/scripts)に表示されます。 慣例により、これらの JavaScript ファイルには、メソッドの動作を実装するファイルから区別するために **-state** というサフィックスが付いています。

## <a name="properties"></a>Properties

スキーマの `Properties` セクションでは、デバイスがソリューションに報告するプロパティ値を定義します。 例: 

```json
"Properties": {
  "Type": "Elevator",
  "Location": "Building 2",
  "Latitude": 47.640792,
  "Longitude": -122.126258
}
```

ソリューションは、開始されると、シミュレートされたすべてのデバイスにクエリを実行して、UI で使用する `Type` 値の一覧を作成します。 このソリューションでは、`Latitude` プロパティと `Longitude` プロパティを使用して、デバイスの位置情報をダッシュボード上のマップに追加します。

## <a name="telemetry"></a>テレメトリ

`Telemetry` 配列では、シミュレートされたデバイスがソリューションに送信するテレメトリの種類すべてが一覧表示されます。

次の例では、エレベーターのセンサーから `floor`、`vibration`、`temperature` のデータが含まれている JSON テレメトリ メッセージを 10 秒ごとに送信します。

```json
"Telemetry": [
  {
    "Interval": "00:00:10",
    "MessageTemplate": "{\"floor\":${floor},\"vibration\":${vibration},\"vibration_unit\":\"${vibration_unit}\",\"temperature\":${temperature},\"temperature_unit\":\"${temperature_unit}\"}",
    "MessageSchema": {
      "Name": "elevator-sensors;v1",
      "Format": "JSON",
      "Fields": {
        "floor": "integer",
        "vibration": "double",
        "vibration_unit": "text",
        "temperature": "double",
        "temperature_unit": "text"
      }
    }
  }
]
```

`MessageTemplate` では、シミュレートされたデバイスから送信された JSON メッセージの構造を定義します。 `MessageTemplate` 内のプレースホルダーでは、構文 `${NAME}` が使用されます。ここでは、`NAME` は[デバイス状態オブジェクト](#simulation) のキーになります。 文字列は引用符で囲む必要がありますが、数値はその必要がありません。

`MessageSchema` では、シミュレートされたデバイスから送信されたメッセージのスキーマを定義します。 メッセージ スキーマは、バックエンド アプリケーションが情報を再利用して受信テレメトリを解釈できるように、IoT Hub にも発行されます。

現在、使用できるのは JSON メッセージ スキーマのみです。 スキーマに記載されているフィールドには、次の型を使用できます。

* オブジェクト - JSON を使用してシリアル化
* バイナリ - base64 を使用してシリアル化
* Text
* Boolean
* 整数
* Double
* DateTime

さまざまな間隔でテレメトリ メッセージを送信するには、複数のテレメトリの種類を `Telemetry` 配列に追加します。 次の例では、10 秒ごとに温度と湿度のデータ、1 分ごとにライトの状態を送信します。

```json
"Telemetry": [
  {
    "Interval": "00:00:10",
    "MessageTemplate":
      "{\"temperature\":${temperature},\"temperature_unit\":\"${temperature_unit}\",\"humidity\":\"${humidity}\"}",
    "MessageSchema": {
      "Name": "RoomComfort;v1",
      "Format": "JSON",
      "Fields": {
        "temperature": "double",
        "temperature_unit": "text",
        "humidity": "integer"
      }
    }
  },
  {
    "Interval": "00:01:00",
    "MessageTemplate": "{\"lights\":${lights_on}}",
    "MessageSchema": {
      "Name": "RoomLights;v1",
      "Format": "JSON",
      "Fields": {
        "lights": "boolean"
      }
    }
  }
],
```

## <a name="cloudtodevicemethods"></a>CloudToDeviceMethods

シミュレートされたデバイスは、IoT Hub から呼び出された cloud-to-device メソッドに対応できます。 デバイス モデル スキーマ ファイルの `CloudToDeviceMethods` セクションでは、次のようにします。

* シミュレートされたデバイスが応答できるメソッドを定義します。
* 実行するロジックが含まれている JavaScript ファイルを示します。

シミュレートされたデバイスは、サポートするメソッドの一覧を、それが接続されている IoT Hub に送信します。

デバイスの動作を実装する JavaScript ファイルの詳細については、[デバイス モデルの動作の概要](../articles/iot-accelerators/iot-accelerators-device-simulation-device-behavior.md)に関する記事を参照してください。

次の例では、サポートされている 3 つのメソッドと、それらのメソッドを実装する JavaScript ファイルを指定しています。

```json
"CloudToDeviceMethods": {
  "Reboot": {
    "Type": "javascript",
    "Path": "Reboot-method.js"
  },
  "EmergencyValveRelease": {
    "Type": "javascript",
    "Path": "EmergencyValveRelease-method.js"
  },
  "IncreasePressure": {
    "Type": "javascript",
    "Path": "IncreasePressure-method.js"
  }
}
```

既定のシミュレートされたデバイスの JavaScript ファイルは、GitHub 上の [scripts フォルダー](https://github.com/Azure/device-simulation-dotnet/tree/master/Services/data/devicemodels/scripts)に表示されます。 慣例により、これらの JavaScript ファイルには、状態の動作を実装するファイルから区別するために **-method** というサフィックスが付いています。