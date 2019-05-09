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
ms.openlocfilehash: 0b2b6814ad0b55d8f56998f6d4c9b6bb88663e04
ms.sourcegitcommit: c2e61b62f218830dd9076d9abc1bbcb42180b3a8
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/15/2018
ms.locfileid: "53444219"
---
## <a name="state-behavior"></a>状態の動作

デバイス モデル スキーマの [Simulation](../articles/iot-accelerators/iot-accelerators-remote-monitoring-device-schema.md#simulation) セクションでは、シミュレートされたデバイスの内部状態を定義します。

- `InitialState` では、デバイス状態オブジェクトのすべてのプロパティの初期値を定義します。
- `Script` は、デバイスの状態を更新するためにスケジュールに従って実行される JavaScript ファイルを示します。

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
  "Interval": "00:00:05",
  "Scripts": {
    "Type": "javascript",
    "Path": "chiller-01-state.js"
  }
}
```

`InitialState` セクションで定義されているように、シミュレートされたデバイスの状態は、シミュレーション サービスによってメモリに保持されます。 状態情報は、**chiller-01-state.js** で定義された `main` 関数に入力として渡されます。 この例では、シミュレーション サービスが 5 秒ごとに **chiller-01-state.js** ファイルを実行します。 このスクリプトでは、シミュレートされたデバイスの状態を変更できます。

以下に、一般的な `main` 関数の概要を示します。

```javascript
function main(context, previousState, previousProperties) {

  // Use the previous device state to
  // generate the new device state
  // returned by the function.

  return state;
}
```

`context` パラメーターには、次のプロパティがあります。

- `currentTime` (`yyyy-MM-dd'T'HH:mm:sszzz` 形式の文字列)
- `deviceId` (例: `Simulated.Chiller.123`)
- `deviceModel` (例: `Chiller`)

`state` パラメーターには、デバイス シミュレーション サービスによって保持されるデバイスの状態が含まれます。 この値は、前回の `main` の呼び出しによって返された `state` オブジェクトです。

次の例では、シミュレーション サービスによって保持されているデバイス状態を処理するための `main` メソッドの一般的な実装を示します。

```javascript
// Default state
var state = {
  online: true,
  temperature: 75.0,
  temperature_unit: "F",
  humidity: 70.0,
  humidity_unit: "%",
  pressure: 150.0,
  pressure_unit: "psig",
  simulation_state: "normal_pressure"
};

function restoreState(previousState) {
  // If the previous state is null, force a default state
  if (previousState !== undefined && previousState !== null) {
      state = previousState;
  } else {
      log("Using default state");
  }
}

function main(context, previousState, previousProperties) {

  restoreState(previousState);

  // Update state

  return state;
}
```

次の例では、`main` メソッドが時間の経過と共に変化するテレメトリ値をどのようにシミュレートできるかを示しています。

```javascript
/**
 * Simple formula generating a random value
 * around the average and between min and max
 */
function vary(avg, percentage, min, max) {
  var value = avg * (1 + ((percentage / 100) * (2 * Math.random() - 1)));
  value = Math.max(value, min);
  value = Math.min(value, max);
  return value;
}


function main(context, previousState, previousProperties) {

    restoreState(previousState);

    // 75F +/- 5%,  Min 25F, Max 100F
    state.temperature = vary(75, 5, 25, 100);

    // 70% +/- 5%,  Min 2%, Max 99%
    state.humidity = vary(70, 5, 2, 99);

    log("Simulation state: " + state.simulation_state);
    if (state.simulation_state === "high_pressure") {
        // 250 psig +/- 25%,  Min 50 psig, Max 300 psig
        state.pressure = vary(250, 25, 50, 300);
    } else {
        // 150 psig +/- 10%,  Min 50 psig, Max 300 psig
        state.pressure = vary(150, 10, 50, 300);
    }

    return state;
}
```

[chiller-01-state.js](https://github.com/Azure/device-simulation-dotnet/blob/master/Services/data/devicemodels/scripts/chiller-01-state.js) 全体は GitHub 上で確認できます。

## <a name="method-behavior"></a>メソッドの動作

デバイス モデル スキーマの [CloudToDeviceMethods](../articles/iot-accelerators/iot-accelerators-remote-monitoring-device-schema.md#cloudtodevicemethods) セクションでは、シミュレートされたデバイスが応答するメソッドを定義します。

次の例では、シミュレートされた冷却装置デバイスでサポートされるメソッドの一覧を示しています。

```json
"CloudToDeviceMethods": {
  "Reboot": {
    "Type": "javascript",
    "Path": "Reboot-method.js"
  },
  "FirmwareUpdate": {
    "Type": "javascript",
    "Path": "FirmwareUpdate-method.js"
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

各メソッドには、メソッドの動作を実装する関連付けられた JavaScript ファイルがあります。

スキーマの `InitialState` セクションで定義されているように、シミュレートされたデバイスの状態は、シミュレーション サービスによってメモリに保持されます。 状態情報は、メソッドが呼び出されたときに、JavaScript ファイルで定義された `main` 関数への入力として渡されます。 このスクリプトでは、シミュレートされたデバイスの状態を変更できます。

以下に、一般的な `main` 関数の概要を示します。

```javascript
function main(context, previousState, previousProperties) {

}
```

`context` パラメーターには、次のプロパティがあります。

- `currentTime` (`yyyy-MM-dd'T'HH:mm:sszzz` 形式の文字列)
- `deviceId` (例: `Simulated.Chiller.123`)
- `deviceModel` (例: `Chiller`)

`state` パラメーターには、デバイス シミュレーション サービスによって保持されるデバイスの状態が含まれます。

`properties` パラメーターには、IoT Hub デバイス ツインに報告されるプロパティとして書き込まれるデバイスのプロパティが含まれます。

メソッドの動作を実装する際に使用できる 3 つのグローバル関数があります。

- シミュレーション サービスで保持されている状態を更新するための `updateState`。
- 1 つのデバイス プロパティを更新するための `updateProperty`。
- 長時間実行されるタスクをシミュレートするために実行を一時停止するための `sleep`。

次の例では、シミュレートされた冷却装置デバイスによって使用される **IncreasePressure-method.js** スクリプトを省略したものを示します。

```javascript
function main(context, previousState, previousProperties) {

    log("Starting 'Increase Pressure' method simulation (5 seconds)");

    // Pause the simulation and change the simulation mode so that the
    // temperature will fluctuate at ~250 when it resumes
    var state = {
        simulation_state: "high_pressure",
        CalculateRandomizedTelemetry: false
    };
    updateState(state);

    // Increase
    state.pressure = 210;
    updateState(state);
    log("Pressure increased to " + state.pressure);
    sleep(1000);

    // Increase
    state.pressure = 250;
    updateState(state);
    log("Pressure increased to " + state.pressure);
    sleep(1000);

    // Resume the simulation
    state.CalculateRandomizedTelemetry = true;
    updateState(state);

    log("'Increase Pressure' method simulation completed");
}
```

## <a name="debugging-script-files"></a>スクリプト ファイルのデバッグ

状態スクリプトとメソッド スクリプトを実行するためにデバイス シミュレーション サービスで使用される JavaScript インタープリターにデバッガーをアタッチすることはできません。 ただし、サービス ログに情報を記録することはできます。 組み込みの `log()` 関数を使用すると、関数の実行を追跡してデバッグするための情報を保存できます。

構文エラーがあると、インタープリターは失敗し、サービス ログに `Jint.Runtime.JavaScriptException` エントリが書き込まれます。

GitHub 上の[ローカルでのサービスの実行](https://github.com/Azure/device-simulation-dotnet#running-the-service-locally-eg-for-development-tasks)に関する記事では、デバイス シミュレーション サービスをローカルで実行する方法を示しています。 サービスをローカルで実行すると、シミュレートされたデバイスをクラウドにデプロイする前に簡単にデバッグすることができます。