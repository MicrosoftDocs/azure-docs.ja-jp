---
title: シミュレートされた高度なデバイス モデルを作成する - Azure| Microsoft Docs
description: この攻略ガイドでは、デバイス シミュレーション ソリューション アクセラレータで使用する高度なデバイス モデルを作成する方法について学習します。
author: troyhopwood
manager: timlt
ms.service: iot-accelerators
services: iot-accelerators
ms.topic: conceptual
ms.date: 03/18/2019
ms.author: troyhop
ms.custom:
- mvc
- amqp
- mqtt
- devx-track-js
ms.openlocfilehash: 58891bdb4601744129c97454f43202d3ba07b709
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "96852559"
---
# <a name="create-an-advanced-device-model"></a>高度なデバイス モデルを作成する

この攻略ガイドでは、カスタム デバイス モデルを定義する JSON および JavaScript ファイルについて説明します。 記事にはサンプルのデバイス モデル定義ファイルがいくつか含まれており、デバイス シミュレーション インスタンスへのアップロード方法が示されています。 高度なデバイス モデルを作成し、テストのためのより現実的なデバイスの動作をシミュレートすることができます。

Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) を作成してください。

## <a name="prerequisites"></a>前提条件

この攻略ガイドの手順に従うには、Azure サブスクリプションにデプロイされたデバイス シミュレーションのインスタンスが必要です。

まだデバイス シミュレーションをデプロイしていない場合は、GitHub の[デバイス シミュレーションのデプロイ](https://github.com/Azure/device-simulation-dotnet/blob/master/README.md)に関する記事を参照してください。

### <a name="open-device-simulation"></a>デバイス シミュレーションを開く

まだデバイス シミュレーションをデプロイしていない場合は、GitHub の[デバイス シミュレーションのデプロイ](https://github.com/Azure/device-simulation-dotnet/blob/master/README.md)に関する記事を参照してください。

## <a name="device-models"></a>デバイス モデル

シミュレートされた各デバイスは、シミュレーションの動作を定義する特定のデバイス モデルに属しています。 この動作には、テレメトリの送信頻度、送信するメッセージの種類、サポートされるメソッドが含まれます。

JSON のデバイス定義ファイルと一連の JavaScript ファイルを使用して、デバイス モデルを定義します。 これらの JavaScript ファイルでは、ランダムなテレメトリやメソッド ロジックなどのシミュレーション動作が定義されます。

一般的なデバイス モデルには以下のものが含まれます。

* デバイス モデルごとに 1 つの JSON ファイル (elevator.json など)。
* デバイス モデルごとに 1 つの JavaScript 動作スクリプト ファイル (elevator-state.js など)
* デバイス メソッドごとに 1 つの JavaScript メソッド スクリプト ファイル (elevator-go-down.js など)

> [!NOTE]
> すべてのデバイス モデルでメソッドが定義されるわけではありません。 したがって、デバイス モデルにメソッド スクリプトがある場合とない場合があります。 しかし、すべてのデバイス モデルに動作スクリプトが必要です。

## <a name="device-definition-file"></a>デバイス定義ファイル

各デバイス定義ファイルには、次の情報を含む、シミュレートされたデバイス モデルの詳細が含まれています。

* デバイス モデル名: 文字列。
* プロトコル:AMQP | MQTT | HTTP。
* デバイスの初期状態。
* デバイス状態の更新頻度。
* デバイス状態を更新するために使用する JavaScript ファイル。
* 送信するテレメトリ メッセージと、それぞれの特定の頻度を示すリスト。
* 受信されたテレメトリを解析するためにバックエンド アプリケーションで使用される、テレメトリ メッセージのスキーマ。
* サポートされているメソッドと、各メソッドをシミュレートするために使用する JavaScript ファイルを示すリスト。

### <a name="file-schema"></a>ファイル スキーマ

スキーマ バージョンは常に "1.0.0" となり、このファイルの形式に固有です。

```json
"SchemaVersion": "1.0.0"
```

### <a name="device-model-description"></a>デバイス モデルの説明

次のプロパティでは、デバイス モデルについて説明します。 各種類には一意識別子、セマンティック バージョン、名前、および説明があります。

```json
"Id": "chiller-01",
"Version": "0.0.1",
"Name": "Chiller",
"Description": "Chiller with external temperature and humidity sensors."
```

### <a name="iot-protocol"></a>IoT プロトコル

IoT デバイスは、さまざまなプロトコルを使用して接続できます。 シミュレーションでは、**AMQP**、**MQTT**、または **HTTP** を使用できます。

```json
"Protocol": "AMQP"
```

### <a name="simulated-device-state"></a>シミュレートされたデバイスの状態

シミュレートされたデバイスにはそれぞれ、定義する必要がある内部状態があります。 状態では、テレメトリで報告できるプロパティも定義されます。 たとえば、冷却装置の初期状態は次のようになる場合があります。

```json
"InitialState": {
    "temperature": 50,
    "humidity": 40
},
```

いくつかのセンサーが搭載されている移動デバイスには、次のように、さらに多くのプロパティがある場合があります。

```json
"InitialState": {
    "latitude": 47.445301,
    "longitude": -122.296307,
    "speed": 30.0,
    "speed_unit": "mph",
    "cargotemperature": 38.0,
    "cargotemperature_unit": "F"
}
```

デバイスの状態はシミュレーション サービスによってインメモリに保持され、JavaScript 関数への入力として提供されます。 JavaScript 関数では、次のようにすることを決定できます。

* 状態を無視して、ランダム データをいくつか生成する。
* 特定のシナリオの何らかの現実的な方法でデバイスの状態を更新する。

状態を生成する関数では、入力として以下のものも受け取ります。

* デバイス ID。
* デバイス モデル。
* 現在の時刻。 この値では、デバイスや時間によって異なるデータを生成できます。

### <a name="generating-telemetry-messages"></a>テレメトリ メッセージの生成

シミュレーション サービスでは、デバイスごとにいくつかのテレメトリの種類を送信できます。 通常、テレメトリにはデバイス状態からのデータが含まれます。 たとえば、シミュレートされた部屋では、10 秒ごとに温度と湿度に関する情報が送信される場合があります。 デバイス状態からの値に自動的に置き換えられる、以下のスニペットのプレースホルダーに注目してください。

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
    }
],
```

プレースホルダーでは、特別な構文 **${NAME}** (ここで、**NAME** は JavaScript の **main** 関数によって返されるデバイス状態オブジェクトからのキーです) が使用されます。 文字列は引用符で囲む必要がありますが、数値はその必要がありません。

#### <a name="message-schema"></a>メッセージ スキーマ

各メッセージの種類には、明確に定義されたスキーマが必要です。 メッセージ スキーマは、バックエンド アプリケーションで情報を再利用して受信テレメトリを解釈できるように、IoT Hub にも発行されます。

スキーマでは JSON 形式がサポートされるため、複数のシステムやサービス間で、解析、変換、分析を簡単に行うことができます。

スキーマに記載されているフィールドには、次の型を使用できます。

* オブジェクト - JSON を使用してシリアル化
* バイナリ - base64 を使用してシリアル化
* Text
* Boolean
* Integer
* Double
* DateTime

### <a name="supported-methods"></a>サポートされるメソッド

シミュレートされたデバイスはメソッド呼び出しに対応できます。その場合、何らかのロジックが実行され、何らかの応答が提供されます。 シミュレーションと同様、メソッド ロジックは JavaScript ファイルに格納され、デバイス状態を操作できます。 次に例を示します。

```json
"CloudToDeviceMethods": {
    "Start": {
        "Type": "JavaScript",
        "Path": "release-pressure.js"
    }
}
```

## <a name="create-a-device-definition-file"></a>デバイス定義ファイルを作成する

この攻略ガイドでは、ドローンのデバイス モデルを作成する方法について説明します。 ドローンは、場所と高度を変更する座標の初期セットを中心にランダムに飛行します。

テキスト エディターに次の JSON をコピーし、**drone.json** として保存します。

### <a name="device-definition-json-example"></a>デバイス定義の JSON の例

```json
{
  "SchemaVersion": "1.0.0",
  "Id": "drone",
  "Version": "0.0.1",
  "Name": "Drone",
  "Description": "Simple drone.",
  "Protocol": "AMQP",
  "Simulation": {
    "InitialState": {
      "velocity": 0.0,
      "velocity_unit": "mm/sec",
      "acceleration": 0.0,
      "acceleration_unit": "mm/sec^2",
      "latitude": 47.476075,
      "longitude": -122.192026,
      "altitude": 0.0
    },
    "Interval": "00:00:05",
    "Scripts": [{
      "Type": "JavaScript",
      "Path": "drone-state.js"
    }]
  },
  "Properties": {
    "Type": "Drone",
    "Firmware": "1.0",
    "Model": "P-96"
  },
  "Tags": {
    "Owner": "Contoso"
  },
  "Telemetry": [{
      "Interval": "00:00:05",
      "MessageTemplate": "{\"velocity\":\"${velocity}\",\"acceleration\":\"${acceleration}\",\"position\":\"${latitude}|${longitude}|${altitude}\"}",
      "MessageSchema": {
        "Name": "drone-event-sensor;v1",
        "Format": "JSON",
        "Fields": {
          "velocity": "double",
          "velocity_unit": "text",
          "acceleration": "double",
          "acceleration_unit": "text",
          "latitude": "double",
          "longitude": "double",
          "altitude": "double"
        }
      }
    }
  ],
    "CloudToDeviceMethods": {
        "RecallDrone": {
            "Type": "JavaScript",
            "Path": "droneRecall-method.js"
        }
    }
}
```

## <a name="behavior-script-files"></a>動作スクリプト ファイル

動作スクリプト ファイル内のコードで、ドローンを移動させます。 スクリプトでは、デバイスのインメモリ状態を操作することで、ドローンの高度と場所を変更します。

JavaScript ファイルには、次の 2 つのパラメーターを受け入れる、**main** 関数が必要です。

* 次の 3 つのプロパティを含む **context** オブジェクト。
    * **yyyy-MM-dd'T'HH:mm:sszzz** という形式の文字列としての **currentTime**。
    * **deviceId**。 たとえば、**Simulated.Elevator.123** です。
    * **deviceModel**。 たとえば、**Elevator** です。
* 前の呼び出しで関数によって返された値である、**state** オブジェクト。 このデバイス状態はシミュレーション サービスによって維持され、テレメトリ メッセージを生成するために使用されます。

**main** 関数では新しいデバイスの状態が返されます。 次に例を示します。

```JavaScript
function main(context, state) {

    // Use context if the simulation depends on
    // time or device details.
    // Execute some logic, updating 'state'

    return state;
}
```

## <a name="create-a-behavior-script-file"></a>動作スクリプト ファイルを作成する

テキスト エディターに次の JavaScript をコピーし、**drone-state.js** として保存します。

### <a name="device-model-javascript-simulation-example"></a>デバイス モデルの JavaScript シミュレーションの例

```JavaScript
"use strict";

// Position control
const DefaultLatitude = 47.476075;
const DefaultLongitude = -122.192026;
const DistanceVariation = 10;

// Altitude control
const DefaultAltitude = 0.0;
const AverageAltitude = 499.99;
const AltitudeVariation = 5;

// Velocity control
const AverageVelocity = 60.00;
const MinVelocity = 20.00;
const MaxVelocity = 120.00;
const VelocityVariation = 5;

// Acceleration control
const AverageAcceleration = 2.50;
const MinAcceleration = 0.01;
const MaxAcceleration = 9.99;
const AccelerationVariation = 1;

// Display control for position and other attributes
const GeoSpatialPrecision = 6;
const DecimalPrecision = 2;

// Default state
var state = {
    velocity: 0.0,
    velocity_unit: "mm/sec",
    acceleration: 0.0,
    acceleration_unit: "mm/sec^2",
    latitude: DefaultLatitude,
    longitude: DefaultLongitude,
    altitude: DefaultAltitude
};

// Default device properties
var properties = {};

/**
 * Restore the global state using data from the previous iteration.
 *
 * @param previousState device state from the previous iteration
 * @param previousProperties device properties from the previous iteration
 */
function restoreSimulation(previousState, previousProperties) {
    // If the previous state is null, force a default state
    if (previousState) {
        state = previousState;
    } else {
        log("Using default state");
    }

    if (previousProperties) {
        properties = previousProperties;
    } else {
        log("Using default properties");
    }
}

/**
 * Simple formula generating a random value around the average
 * in between min and max
 */
function vary(avg, percentage, min, max) {
    var value = avg * (1 + ((percentage / 100) * (2 * Math.random() - 1)));
    value = Math.max(value, min);
    value = Math.min(value, max);
    return value;
}

/**
 * Entry point function called by the simulation engine.
 * Returns updated simulation state.
 * Device property updates must call updateProperties() to persist.
 *
 * @param context             The context contains current time, device model and id
 * @param previousState       The device state since the last iteration
 * @param previousProperties  The device properties since the last iteration
 */
/*jslint unparam: true*/
function main(context, previousState, previousProperties) {

    // Restore the global state before generating the new telemetry, so that
    // the telemetry can apply changes using the previous function state.
    restoreSimulation(previousState, previousProperties);

    state.acceleration = vary(AverageAcceleration, AccelerationVariation, MinAcceleration, MaxAcceleration).toFixed(DecimalPrecision);
    state.velocity = vary(AverageVelocity, VelocityVariation, MinVelocity, MaxVelocity).toFixed(DecimalPrecision);

    // Use the last coordinates to calculate the next set with a given variation
    var coords = varylocation(Number(state.latitude), Number(state.longitude), DistanceVariation);
    state.latitude = Number(coords.latitude).toFixed(GeoSpatialPrecision);
    state.longitude = Number(coords.longitude).toFixed(GeoSpatialPrecision);

    // Fluctuate altitude between given variation constant by more or less
    state.altitude = vary(AverageAltitude, AltitudeVariation, AverageAltitude - AltitudeVariation, AverageAltitude + AltitudeVariation).toFixed(DecimalPrecision);

    return state;
}

/**
 * Generate a random geolocation at some distance (in miles)
 * from a given location
 */
function varylocation(latitude, longitude, distance) {
    // Convert to meters, use Earth radius, convert to radians
    var radians = (distance * 1609.344 / 6378137) * (180 / Math.PI);
    return {
        latitude: latitude + radians,
        longitude: longitude + radians / Math.cos(latitude * Math.PI / 180)
    };
}
```

## <a name="create-a-method-script-file"></a>メソッド スクリプト ファイルを作成する

メソッド スクリプトは、動作スクリプトに似ています。 特定のクラウドからデバイスへのメソッドの呼び出し時に、デバイスの動作が定義されます。

ドローンの呼び戻しスクリプトでは、ホームに戻るドローンをシミュレートするためにドローンの座標を固定点に設定します。

テキスト エディターに次の JavaScript をコピーし、**droneRecall-method.js** として保存します。

### <a name="device-model-javascript-simulation-example"></a>デバイス モデルの JavaScript シミュレーションの例

```JavaScript
"use strict";

// Default state
var state = {
    velocity: 0.0,
    velocity_unit: "mm/sec",
    acceleration: 0.0,
    acceleration_unit: "mm/sec^2",
    latitude: 0.0,
    longitude: 0.0,
    altitude: 0.0
};

// Default device properties
var properties = {};

/**
 * Restore the global state using data from the previous iteration.
 *
 * @param previousState device state from the previous iteration
 * @param previousProperties device properties from the previous iteration
 */
function restoreSimulation(previousState, previousProperties) {
    // If the previous state is null, force a default state
    if (previousState) {
        state = previousState;
    } else {
        log("Using default state");
    }

    if (previousProperties) {
        properties = previousProperties;
    } else {
        log("Using default properties");
    }
}

/**
 * Entry point function called by the simulation engine.
 *
 * @param context        The context contains current time, device model and id, not used
 * @param previousState  The device state since the last iteration, not used
 * @param previousProperties  The device properties since the last iteration
 */
/*jslint unparam: true*/
function main(context, previousState, previousProperties) {

    // Restore the global device properties and the global state before
    // generating the new telemetry, so that the telemetry can apply changes
    // using the previous function state.
    restoreSimulation(previousState, previousProperties);

    //simulate the behavior of a drone when recalled
  state.latitude = 47.476075;
  state.longitude = -122.192026;
  return state;
}
```

## <a name="debugging-script-files"></a>スクリプト ファイルのデバッグ

実行中の動作ファイルにデバッガーを添付することはできませんが、**log** 関数を使用して、サービス ログに情報を書き込むことはできます。 構文エラーの場合、インタープリターは失敗し、例外に関する情報がログに書き込まれます。

ログの例:

```JavaScript
function main(context, state) {

    log("This message will appear in the service logs.");

    log(context.deviceId);

    if (typeof(state) !== "undefined" && state !== null) {
        log("Previous value: " + state.temperature);
    }

    // ...

    return updateState;
}
```

## <a name="deploy-an-advanced-device-model"></a>高度なデバイス モデルをデプロイする

高度なデバイス モデルをデプロイするには、ファイルをデバイス シミュレーション インスタンスにアップロードします。

メニュー バーで **[Device models]\(デバイス モデル\)** を選択します。 **[デバイス モデル]** ページに、デバイス シミュレーションのこのインスタンスで利用可能なデバイス モデルがリストされます。

![デバイス モデル](media/iot-accelerators-device-simulation-advanced-device/devicemodelnav.png)

ページの右上隅にある **[+ Add Device Models]\(+ デバイス モデルの追加\)** をクリックします。

![デバイス モデルを追加する](media/iot-accelerators-device-simulation-advanced-device/devicemodels.png)

**[詳細]** をクリックして、高度なデバイス モデルのタブを開きます。

![[詳細設定] タブ](media/iot-accelerators-device-simulation-advanced-device/advancedtab.png)

**[参照]** をクリックし、作成した JSON および JavaScript ファイルを選択します。 必ず、3 つのファイルをすべて選択してください。 ファイルのいずれか 1 つが欠落している場合、検証は失敗します。

![ファイルを参照する](media/iot-accelerators-device-simulation-advanced-device/browse.png)

ファイルが検証に合格した場合は、 **[保存]** をクリックします。これで、デバイス モデルをシミュレーションで使用する準備が整います。 それ以外の場合は、エラーを修正して、ファイルを再アップロードします。

![保存](media/iot-accelerators-device-simulation-advanced-device/validated.png)

## <a name="next-steps"></a>次のステップ

この攻略ガイドでは、デバイス シミュレーションで使用するデバイス モデル ファイルについてと、高度なデバイス モデルの作成方法を学習しました。 次は、[Time Series Insights を使用して、デバイス シミュレーション ソリューション アクセラレータから送信されたテレメトリを視覚化する](./iot-accelerators-device-simulation-time-series-insights.md)方法を確認できます。