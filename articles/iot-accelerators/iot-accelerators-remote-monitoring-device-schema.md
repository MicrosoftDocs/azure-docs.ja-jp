---
title: リモート監視ソリューションのデバイス スキーマ - Azure | Microsoft Docs
description: この記事では、リモート監視ソリューションのシミュレートされたデバイスを定義する JSON スキーマについて説明します。
author: dominicbetts
manager: philmea
ms.author: dobett
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 12/18/2018
ms.topic: conceptual
ms.custom:
- amqp
- mqtt
ms.openlocfilehash: ac681bb13ccea49c7a2f566a6fcdb6adb8cec5bb
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/02/2020
ms.locfileid: "81683745"
---
# <a name="understand-the-device-model-schema"></a>デバイス モデル スキーマについて

リモート監視ソリューションでシミュレートされたデバイスを使用して、その動作をテストすることができます。 リモート監視ソリューションには、シミュレートされたデバイスを実行するためのデバイス シミュレーション サービスが含まれています。 リモート監視ソリューションをデプロイすると、一連のシミュレートされたデバイスが自動的にプロビジョニングされます。 既存のシミュレートされたデバイスをカスタマイズすることも、独自のデバイスを作成することもできます。

この記事では、シミュレートされたデバイスの機能と動作を指定するデバイス モデル スキーマについて説明します。 デバイス モデルは JSON ファイルに格納されます。

> [!NOTE]
> このデバイス モデル スキーマは、デバイス シミュレーション サービスでホストされているシミュレートされたデバイスに対してのみ有効です。 実際のデバイスを作成する場合は、「[デバイスをリモート監視ソリューション アクセラレータに接続する](iot-accelerators-connecting-devices.md)」を参照してください。

次の記事は、現在の記事に関連しています。

* 「[デバイス モデルの動作の実装](iot-accelerators-remote-monitoring-device-behavior.md)」では、シミュレートされたデバイスの動作を実装するために使用する JavaScript ファイルについて説明します。
* 「[新しいシミュレートされたデバイスの作成](iot-accelerators-remote-monitoring-create-simulated-device.md)」では、すべてをうまくまとめて、新しいシミュレートされたデバイスの種類をソリューションにデプロイする方法を示しています。

この記事では、次のことについて説明します。

>[!div class="checklist"]
> * JSON ファイルを使用して、シミュレートされたデバイス モデルを定義する
> * シミュレートされたデバイスのプロパティを指定する
> * シミュレートされたデバイスが送信するテレメトリを指定する
> * デバイスが応答する cloud-to-device メソッドを指定する

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

デバイス状態オブジェクトを更新する JavaScript ファイルの詳細については、[デバイスモデルの動作の概要](../../articles/iot-accelerators/iot-accelerators-device-simulation-advanced-device.md)に関する記事を参照してください。

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

スキーマの `Properties` セクションでは、デバイスがソリューションに報告するプロパティ値を定義します。 次に例を示します。

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

デバイスの動作を実装する JavaScript ファイルの詳細については、[デバイス モデルの動作の概要](../../articles/iot-accelerators/iot-accelerators-device-simulation-advanced-device.md)に関する記事を参照してください。

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

## <a name="next-steps"></a>次のステップ

この記事では、独自のシミュレートされたカスタム デバイス モデルを作成する方法について説明しました。 この記事では、次の方法について説明しました。

<!-- Repeat task list from intro -->
>[!div class="checklist"]
> * JSON ファイルを使用して、シミュレートされたデバイス モデルを定義する
> * シミュレートされたデバイスのプロパティを指定する
> * シミュレートされたデバイスが送信するテレメトリを指定する
> * デバイスが応答する cloud-to-device メソッドを指定する

JSON スキーマについて理解できたら、次のステップとして、[シミュレートされたデバイスの動作を実装する](iot-accelerators-remote-monitoring-device-behavior.md)方法について学習することをお勧めします。

リモート監視ソリューションに関する開発者向け情報の詳細については、以下をご覧ください。

* [開発者向けリファレンス ガイド](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet/wiki/Developer-Reference-Guide)
* [開発者向けトラブルシューティング ガイド](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet/wiki/Developer-Troubleshooting-Guide)
