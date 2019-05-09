---
title: インクルード ファイル
description: インクルード ファイル
services: iot-accelerators
author: dominicbetts
ms.service: iot-accelerators
ms.topic: include
ms.date: 09/28/2018
ms.author: dobett
ms.custom: include file
ms.openlocfilehash: cda08d44cba9e59af853b1705f538ec199ec4d3a
ms.sourcegitcommit: fec96500757e55e7716892ddff9a187f61ae81f7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/16/2019
ms.locfileid: "59630522"
---
1 つ目のシナリオでは、新しいテレメトリ タイプを Contoso の既存の **Chiller** デバイスの種類に追加します。

2 つ目のシナリオでは、Contoso がスマート電球の新しいデバイスをテストします。 このテストを実行するために、次の特性を備える、新しいシミュレートされたデバイスを作成します。

*プロパティ*

| 名前                     | 値                      |
| ------------------------ | --------------------------- |
| 色                    | 白、赤、青            |
| 明るさ               | 0 から 100                    |
| 推定寿命 | 10,000 時間からカウントダウン |

*テレメトリ*

次の表に、電球がクラウドにデータ ストリームとしてレポートするデータを示します。

| Name   | 値      |
| ------ | ----------- |
| 状態 | "オン"、"オフ" |
| 気温 | 度 (華氏) |
| オンライン | true、false |

> [!NOTE]
> **オンライン** テレメトリ値は、シミュレートされるすべての種類で必須です。

*メソッド*

次の表に、新しいデバイスがサポートするアクションを示します。

| 名前        |
| ----------- |
| オンにする   |
| オフにする  |

*初期状態*

次の表に、デバイスの初期状態を示します。

| 名前                     | 値 |
| ------------------------ | -------|
| 初期の色            | 白  |
| 初期の明るさ       | 75     |
| 初期の寿命   | 10,000 |
| 初期のテレメトリ状態 | "オン"   |
| 初期のテレメトリ温度 | 200   |

このハウツー ガイドの手順を完了するには、アクティブな Azure サブスクリプションが必要です。

Azure サブスクリプションがない場合は、開始する前に[無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)を作成してください。

[!INCLUDE [cloud-shell-try-it.md](cloud-shell-try-it.md)]

## <a name="prerequisites"></a>前提条件

このハウツー ガイドに従うには、次のものが必要です。

* Visual Studio Code。 [Mac、Linux、および Windows 用の Visual Studio Code をダウンロード](https://code.visualstudio.com/download)できます。
* .NET Core。 [Mac、Linux、および Windows 用の .NET Core をダウンロード](https://www.microsoft.com/net/download)できます。
* [C# for Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp)
* Postman。 [Mac、Windows、または Linux 用の Postman をダウンロード](https://www.getpostman.com/apps)できます。
* [Azure サブスクリプションにデプロイされた IoT ハブ](../articles/iot-hub/iot-hub-create-through-portal.md)。 このガイドの手順を完了するには、IoT ハブの接続文字列が必要です。 この接続文字列は、Azure portal から取得できます。
* [強力な一貫性](../articles/cosmos-db/manage-account.md)を確保するよう構成された、SQL API を使用する Cosmos DB データベース。 このガイドの手順を完了するには、Cosmos DB データベースの接続文字列が必要です。 この接続文字列は、Azure portal から取得できます。

## <a name="prepare-your-development-environment"></a>開発環境を準備する

開発環境を準備するには、次のタスクを実行します。

* デバイス シミュレーション マイクロサービスのソースをダウンロードします。
* ストレージ アダプター マイクロサービスのソースをダウンロードします。
* ストレージ アダプター マイクロサービスをローカルで実行します。

この記事の手順では、Windows の使用を想定しています。 別のオペレーティング システムを使用している場合は、環境に合わせてファイル パスとコマンドの一部を調整することが必要になる場合があります。

### <a name="download-the-microservices"></a>マイクロサービスをダウンロードする

[リモート監視マイクロサービス](https://github.com/Azure/remote-monitoring-services-dotnet/archive/master.zip)を GitHub からローカル コンピューター上の適切な場所にダウンロードし、解凍します。 この記事では、このフォルダーの名前は **remote-monitoring-services-dotnet-master** であることを前提としています。

[デバイス シミュレーション マイクロサービス](https://github.com/Azure/device-simulation-dotnet/archive/master.zip)を GitHub からローカル コンピューター上の適切な場所にダウンロードし、解凍します。 この記事では、このフォルダーの名前は **device-simulation-dotnet-master** であることを前提としています。

### <a name="run-the-storage-adapter-microservice"></a>ストレージ アダプター マイクロサービスを実行する

Visual Studio Code で **remote-monitoring-services-dotnet-master\storage-adapter** フォルダーを開きます。 未解決の依存関係がある場合は、**[復元]** ボタンをクリックして修正します。

**storage-adapter/WebService/appsettings.ini** ファイルを開き、Cosmos DB 接続文字列を **documentDBConnectionString** 変数に割り当てます。

マイクロサービスをローカルで実行するために、**[デバッグ] > [デバッグの開始]** をクリックします。

Visual Studio Code の**ターミナル** ウィンドウに、実行中のマイクロサービスからの出力が表示されます。これに、Web サービスの正常性チェック用の URL ([http://127.0.0.1:9022/v1/status](http://127.0.0.1:9022/v1/status)) が含まれます。 このアドレスに移動すると、状態が "OK: Alive and well" と表示されます。

ストレージ アダプター マイクロサービスは、次の手順を実行している間もこの Visual Studio Code のインスタンスで実行したままにしておきます。

## <a name="modify-the-chiller"></a>冷却装置を変更する

このセクションでは、新しい**内部温度**テレメトリの種類を既存の **Chiller** デバイスの種類に追加します。

1. ローカル コンピューター上に新しいフォルダー **C:\temp\devicemodels** を作成します。

1. ダウンロードしたデバイス シミュレーション マイクロサービスのコピーから、次のファイルを新しいフォルダーにコピーします。

    | ソース | 宛先 |
    | ------ | ----------- |
    | Services\data\devicemodels\chiller-01.json | C:\temp\devicemodels\chiller-01.json |
    | Services\data\devicemodels\scripts\chiller-01-state.js | C:\temp\devicemodels\scripts\chiller-01-state.js |
    | Services\data\devicemodels\scripts\Reboot-method.js | C:\temp\devicemodels\scripts\Reboot-method.js |
    | Services\data\devicemodels\scripts\FirmwareUpdate-method.js | C:\temp\devicemodels\scripts\FirmwareUpdate-method.js |
    | Services\data\devicemodels\scripts\EmergencyValveRelease-method.js | C:\temp\devicemodels\scripts\EmergencyValveRelease-method.js |
    | Services\data\devicemodels\scripts\IncreasePressure-method.js | C:\temp\devicemodels\scripts\IncreasePressure-method.js |

1. **C:\temp\devicemodels\chiller-01.json** ファイルを開きます。

1. **InitialState** セクションに、次の 2 つの定義を追加します。

    ```json
    "internal_temperature": 65.0,
    "internal_temperature_unit": "F",
    ```

1. **Telemetry** 配列に、次の定義を追加します。

    ```json
    {
      "Interval": "00:00:05",
      "MessageTemplate": "{\"internal_temperature\":${internal_temperature},\"internal_temperature_unit\":\"${internal_temperature_unit}\"}",
      "MessageSchema": {
        "Name": "chiller-internal-temperature;v1",
        "Format": "JSON",
        "Fields": {
          "temperature": "double",
          "temperature_unit": "text"
        }
      }
    },
    ```

1. **C:\temp\devicemodels\chiller-01.json** ファイルを保存します。

1. **C:\temp\devicemodels\scripts\chiller-01-state.js** ファイルを開きます。

1. **state** 変数に次のフィールドを追加します。

    ```js
    internal_temperature: 65.0,
    internal_temperature_unit: "F",
    ```

1. **main** 関数を次のように更新します。

    ```js
    function main(context, previousState, previousProperties) {

        // Restore the global state before generating the new telemetry, so that
        // the telemetry can apply changes using the previous function state.
        restoreSimulation(previousState, previousProperties);

        // 75F +/- 5%,  Min 25F, Max 100F
        state.temperature = vary(75, 5, 25, 100);

        // 70% +/- 5%,  Min 2%, Max 99%
        state.humidity = vary(70, 5, 2, 99);

        // 65F +/- 2%,  Min 15F, Max 125F
        state.internal_temperature = vary(65, 2, 15, 125);

        log("Simulation state: " + state.simulation_state);
        if (state.simulation_state === "high_pressure") {
            // 250 psig +/- 25%,  Min 50 psig, Max 300 psig
            state.pressure = vary(250, 25, 50, 300);
        } else {
            // 150 psig +/- 10%,  Min 50 psig, Max 300 psig
            state.pressure = vary(150, 10, 50, 300);
        }

        updateState(state);
        return state;
    }
    ```

1. **C:\temp\devicemodels\scripts\chiller-01-state.js** ファイルを保存します。

## <a name="create-the-lightbulb"></a>lightbulb を作成する

このセクションでは、新しい **Lightbulb** デバイスの種類を定義します。

1. ファイル **C:\temp\devicemodels\lightbulb-01.json** を作成し、次のコンテンツを追加します。

    ```json
    {
      "SchemaVersion": "1.0.0",
      "Id": "lightbulb-01",
      "Version": "0.0.1",
      "Name": "Lightbulb",
      "Description": "Smart lightbulb device.",
      "Protocol": "MQTT",
      "Simulation": {
        "InitialState": {
          "online": true,
          "temperature": 200.0,
          "temperature_unit": "F",
          "status": "on"
        },
        "Interval": "00:00:20",
        "Scripts": [
          {
            "Type": "javascript",
            "Path": "lightbulb-01-state.js"
          }
        ]
      },
      "Properties": {
        "Type": "Lightbulb",
        "Color": "White",
        "Brightness": 75,
        "EstimatedRemainingLife": 10000
      },
      "Tags": {
        "Location": "Building 2",
        "Floor": "2",
        "Campus": "Redmond"
      },
      "Telemetry": [
        {
          "Interval": "00:00:20",
          "MessageTemplate": "{\"temperature\":${temperature},\"temperature_unit\":\"${temperature_unit}\",\"status\":\"${status}\"}",
          "MessageSchema": {
            "Name": "lightbulb-status;v1",
            "Format": "JSON",
            "Fields": {
              "temperature": "double",
              "temperature_unit": "text",
              "status": "text"
            }
          }
        }
      ],
      "CloudToDeviceMethods": {
        "SwitchOn": {
          "Type": "javascript",
          "Path": "SwitchOn-method.js"
        },
        "SwitchOff": {
          "Type": "javascript",
          "Path": "SwitchOff-method.js"
        }
      }
    }
    ```

    変更を **C:\temp\devicemodels\lightbulb-01.json** に保存します。

1. ファイル **C:\temp\devicemodels\scripts\lightbulb-01-state.js** を作成し、次のコンテンツを追加します。

    ```javascript
    "use strict";

    // Default state
    var state = {
      online: true,
      temperature: 200.0,
      temperature_unit: "F",
      status: "on"
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
     *
     * @returns random value with given parameters
     */
    function vary(avg, percentage, min, max) {
      var value = avg * (1 + ((percentage / 100) * (2 * Math.random() - 1)));
      value = Math.max(value, min);
      value = Math.min(value, max);
      return value;
    }

    /**
     * Simple formula that sometimes flips the status of the lightbulb
     */
    function flip(value) {
      if (Math.random() < 0.2) {
        return (value == "on") ? "off" : "on"
      }
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
    function main(context, previousState, previousProperties) {

      // Restore the global device properties and the global state before
      // generating the new telemetry, so that the telemetry can apply changes
      // using the previous function state.
      restoreSimulation(previousState, previousProperties);

      state.temperature = vary(200, 5, 150, 250);

      // Make this flip every so often
      state.status = flip(state.status);

      updateState(state);

      return state;
    }
    ```

    変更を **C:\temp\devicemodels\scripts\lightbulb-01-state.js** に保存します。

1. ファイル **C:\temp\devicemodels\scripts\SwitchOn-method.js** を作成し、次のコンテンツを追加します。

    ```javascript
    "use strict";

    // Default state
    var state = {
      status: "on"
    };

    /**
     * Entry point function called by the method.
     *
     * @param context        The context contains current time, device model and id
     * @param previousState  The device state since the last iteration
     * @param previousProperties  The device properties since the last iteration
     */
    function main(context, previousState) {
      log("Executing lightbulb Switch On method.");
      state.status = "on";
      updateState(state);
    }
    ```

    変更を **C:\temp\devicemodels\scripts\SwitchOn-method.js** に保存します。

1. ファイル **C:\temp\devicemodels\scripts\SwitchOff-method.js** を作成し、次のコンテンツを追加します。

    ```javascript
    "use strict";

    // Default state
    var state = {
      status: "on"
    };

    /**
     * Entry point function called by the method.
     *
     * @param context        The context contains current time, device model and id
     * @param previousState  The device state since the last iteration
     * @param previousProperties  The device properties since the last iteration
     */
    function main(context, previousState) {
      log("Executing lightbulb Switch Off method.");
      state.status = "off";
      updateState(state);
    }
    ```

    変更を **C:\temp\devicemodels\scripts\SwitchOff-method.js** に保存します。

これで、**Chiller** デバイスの種類のカスタマイズされたバージョンが作成され、新しい **Lightbulb** デバイスの種類が作成されました。

## <a name="test-the-devices"></a>デバイスをテストする

このセクションでは、前のセクションで作成したデバイスの種類をローカルでテストします。

### <a name="run-the-device-simulation-microservice"></a>デバイス シミュレーション マイクロサービスを実行する

Visual Studio Code の新しいインスタンスで、GitHub からダウンロードした **device-simulation-dotnet-master** フォルダーを開きます。 未解決の依存関係がある場合は、**[復元]** ボタンをクリックして修正します。

**WebService/appsettings.ini** ファイルを開き、Cosmos DB 接続文字列を **documentdb_connstring** 変数に割り当て、次のように設定を変更します。

```ini
device_models_folder = C:\temp\devicemodels\

device_models_scripts_folder = C:\temp\devicemodels\scripts\
```

マイクロサービスをローカルで実行するために、**[デバッグ] > [デバッグの開始]** をクリックします。

Visual Studio Code の**ターミナル** ウィンドウに、実行中のマイクロサービスからの出力が表示されます。

デバイス シミュレーション マイクロサービスは、次の手順を実行している間もこの Visual Studio Code のインスタンスで実行したままにしておきます。

### <a name="set-up-a-monitor-for-device-events"></a>デバイス イベントのモニターを設定する

このセクションでは、Azure CLI を使用して、IoT ハブに接続されたデバイスから送信されたテレメトリを表示するイベント モニターを設定します。

次のスクリプトでは、IoT ハブの名前を **device-simulation-test** と想定しています。

```azurecli-interactive
# Install the IoT extension if it's not already installed
az extension add --name azure-cli-iot-ext

# Monitor telemetry sent to your hub
az iot hub monitor-events --hub-name device-simulation-test
```

イベント モニターは、シミュレートされたデバイスをテストしている間も実行したままにしておきます。

### <a name="create-a-simulation-with-the-updated-chiller-device-type"></a>更新された chiller デバイスの種類を使用してシミュレーションを作成する

このセクションでは、Postman ツールを使用して、更新された chiller デバイスの種類を使ってシミュレーションを実行するようデバイス シミュレーション マイクロサービスに要求します。 Postman は、Web サービスに REST 要求を送信するためのツールです。 必要な Postman 構成ファイルは、**device-simulation-dotnet** リポジトリのローカル コピーに含まれています。

Postman を設定するには:

1. ローカル コンピューターで Postman を開きます。

1. **[File]\(ファイル\) > [Import]\(インポート\)** をクリックします。 次に、**[Choose Files]\(ファイルの選択\)** をクリックします。

1. **device-simulation-dotnet-master/docs/postman** フォルダーに移動します。 **Azure IoT Device Simulation solution accelerator.postman_collection** と **Azure IoT Device Simulation solution accelerator.postman_environment** を選択し、**[Open]\(開く\)** をクリックします。

1. **[Azure IoT Device Simulation solution accelerator]\(Azure IoT デバイス シミュレーション ソリューション アクセラレータ\)** を、送信可能な要求に展開します。

1. **[No Environment]\(環境なし\)** をクリックし、**[Azure IoT Device Simulation solution accelerator]\(Azure IoT デバイス シミュレーション ソリューション アクセラレータ\)** を選択します。

これで、デバイス シミュレーション マイクロサービスとやり取りするためのコレクションと環境が Postman ワークスペースに読み込まれました。

シミュレーションを構成および実行するには:

1. Postman コレクション内で、**[Create modified chiller simulation]\(変更された chiller シミュレーションの作成\)** を選択し、**[Send]\(送信\)** をクリックします。 この要求により、シミュレートされた chiller デバイスの種類のインスタンスが 4 つ作成されます。

1. Azure CLI ウィンドウのイベント モニター出力に、新しい **internal_temperature** 値を含む、シミュレートされたデバイスからのテレメトリが表示されます。

シミュレーションを停止するには、Postman で **[Stop simulation]\(シミュレーションの停止\)** 要求を選択し、**[Send]\(送信\)** をクリックします。

### <a name="create-a-simulation-with-the-lightbulb-device-type"></a>lightbulb デバイスの種類を使用してシミュレーションを作成する

このセクションでは、Postman ツールを使用して、lightbulb デバイスの種類を使ってシミュレーションを実行するようデバイス シミュレーション マイクロサービスに要求します。 Postman は、Web サービスに REST 要求を送信するためのツールです。

シミュレーションを構成および実行するには:

1. Postman コレクション内で、**[Create lightbulb simulation]\(lightbulb シミュレーションの作成\)** を選択し、**[Send]\(送信\)** をクリックします。 この要求により、シミュレートされた lightbulb デバイスの種類のインスタンスが 2 つ作成されます。

1. Azure CLI ウィンドウのイベント モニター出力に、シミュレートされた lightbulb からのテレメトリが表示されます。

シミュレーションを停止するには、Postman で **[Stop simulation]\(シミュレーションの停止\)** 要求を選択し、**[Send]\(送信\)** をクリックします。

## <a name="clean-up-resources"></a>リソースのクリーンアップ

ローカルで実行されている 2 つのマイクロサービスを Visual Studio Code インスタンスで停止することができます (**[デバッグ] > [デバッグの停止]**)。

IoT Hub および Cosmos DB のインスタンスが必要でなくなった場合は、不必要な課金を回避するために、Azure サブスクリプションからそれらを削除します。