---
title: Azure IoT プラグ アンド プレイ プレビュー デバイスを作成し、テストする | Microsoft Docs
description: デバイス開発者向けに、VS Code を使用して IoT プラグ アンド プレイ プレビュー デバイス用の新しいデバイス機能モデルを作成し、テストする方法について説明します。
author: dominicbetts
ms.author: dobett
ms.date: 07/10/2019
ms.topic: tutorial
ms.custom: mvc
ms.service: iot-pnp
services: iot-pnp
manager: philmea
ms.openlocfilehash: 5a03eea84d0d68f06b432014fc4374e799914d67
ms.sourcegitcommit: b3bad696c2b776d018d9f06b6e27bffaa3c0d9c3
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/21/2019
ms.locfileid: "69878723"
---
# <a name="tutorial-create-and-test-a-device-capability-model-using-visual-studio-code"></a>チュートリアル:Visual Studio Code を使用してデバイス機能モデルを作成し、テストする

このチュートリアルでは、デバイス開発者向けに、Visual Studio Code を使用して "_デバイス機能モデル_" を作成する方法について説明します。 このモデルを使用することで、スケルトン コードを生成して、クラウドの Azure IoT Hub インスタンスに接続されているデバイス上で実行できます。

本チュートリアルのうち、生成したスケルトン コードのビルド方法を説明するセクションでは、Windows の使用を前提としています。

このチュートリアルでは、以下の内容を学習します。

> [!div class="checklist"]
> * デバイス機能モデルを作成する
> * モデルからスケルトン デバイス コードを生成する
> * 生成したコードにスタブを実装する
> * コードを実行して IoT ハブとのやり取りをテストする

## <a name="prerequisites"></a>前提条件

このチュートリアルでデバイス機能モデルを扱う作業を行うには、次のものが必要です。

* [Visual Studio Code](https://code.visualstudio.com/download):VS Code は複数のプラットフォームに対応しています
* VS Code の Azure IoT Device Workbench 拡張機能。 VS Code に Azure IoT Device Workbench 拡張機能をインストールするには、次の手順に従います。

    1. VS Code で、 **[拡張機能]** タブを選択します。
    1. **Azure IoT Device Workbench** を検索します。
    1. **[インストール]** を選択します。

このチュートリアルで生成した C コードを Windows 上でビルドするには、次のものが必要です。

* [Visual Studio (Community、Professional、または Enterprise)](https://visualstudio.microsoft.com/downloads/): Visual Studio のインストール時に、**NuGet パッケージ マネージャー** コンポーネントと **C++ によるデスクトップ開発**ワークロードを必ず含めるようにしてください。
* [Git](https://git-scm.com/download)
* [CMake](https://cmake.org/download/)
* Azure IoT C SDK のローカル コピー:

    ```cmd
    git clone https://github.com/Azure/azure-iot-sdk-c.git --recursive -b public-preview
    ```

このチュートリアルでデバイス コードのテストを行うには、次のものが必要です。

* [Azure IoT エクスプローラー](https://github.com/Azure/azure-iot-explorer/releases)。
* Azure サブスクリプション。 Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) を作成してください。

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="model-your-device"></a>デバイスのモデル化

デバイス機能モデルを作成するには、"_デジタル ツイン定義言語_" を使用します。 一般的に、モデルは、複数の "_インターフェイス_" 定義ファイルと、1 つのモデル ファイルで構成されます。 **VS Code 向け Azure IoT Device Workbench 拡張機能**には、こうした JSON ファイルを作成および編集する際に役立つツールが揃っています。

### <a name="create-the-interface-file"></a>インターフェイス ファイルの作成

IoT デバイスの機能を定義したインターフェイス ファイルを VS Code で作成するには:

1. **devicemodel** という名前のフォルダーを作成します。

1. VS Code を起動し、**Ctrl + Shift + P** キーを押してコマンド パレットを開きます。

1. 「**Plug and Play**」と入力して、 **[IoT Plug & Play: Create Interface]\(IoT プラグ アンド プレイ: インターフェイスの作成\)** コマンドを選択します。

1. 作成した **devicemodel** フォルダーを参照して、選択します。

1. 次に、インターフェイス名として「**EnvironmentalSensor**」と入力し、**Enter** キーを押します。 VS Code によって、**EnvironmentalSensor.interface.json** という名前のサンプル インターフェイス ファイルが作成されます。

1. このファイルの内容を次の JSON で置き換えます。`@id` フィールドの `{your name}` は、一意の値に置き換えてください。 英数字 (a から z、A から Z、0 から 9) とアンダースコアのみを使用してください。 詳細については、「[デジタル ツイン識別子の形式](https://github.com/Azure/IoTPlugandPlay/tree/master/DTDL#digital-twin-identifier-format)」を参照してください。 インターフェイスをリポジトリに保存するには、インターフェイス ID を一意のものにする必要があります。

    ```json
    {
      "@id": "urn:{your name}:EnvironmentalSensor:1",
      "@type": "Interface",
      "displayName": "Environmental Sensor",
      "description": "Provides functionality to report temperature, humidity. Provides telemetry, commands and read-write properties",
      "comment": "Requires temperature and humidity sensors.",
      "contents": [
        {
          "@type": "Property",
          "displayName": "Device State",
          "description": "The state of the device. Two states online/offline are available.",
          "name": "state",
          "schema": "boolean"
        },
        {
          "@type": "Property",
          "displayName": "Customer Name",
          "description": "The name of the customer currently operating the device.",
          "name": "name",
          "schema": "string",
          "writable": true
        },
        {
          "@type": "Property",
          "displayName": "Brightness Level",
          "description": "The brightness level for the light on the device. Can be specified as 1 (high), 2 (medium), 3 (low)",
          "name": "brightness",
          "writable": true,
          "schema": "long"
        },
        {
          "@type": [
            "Telemetry",
            "SemanticType/Temperature"
          ],
          "description": "Current temperature on the device",
          "displayName": "Temperature",
          "name": "temp",
          "schema": "double",
          "unit": "Units/Temperature/fahrenheit"
        },
        {
          "@type": [
            "Telemetry",
            "SemanticType/Humidity"
          ],
          "description": "Current humidity on the device",
          "displayName": "Humidity",
          "name": "humid",
          "schema": "double",
          "unit": "Units/Humidity/percent"
        },
        {
          "@type": "Telemetry",
          "name": "magnetometer",
          "displayName": "Magnetometer",
          "comment": "This shows a complex telemetry that contains a magnetometer reading.",
          "schema": {
            "@type": "Object",
            "fields": [
              {
                "name": "x",
                "schema": "integer"
              },
              {
                "name": "y",
                "schema": "integer"
              },
              {
                "name": "z",
                "schema": "integer"
              }
            ]
          }
        },
        {
          "@type": "Command",
          "name": "turnon",
          "response": {
            "name": "turnon",
            "schema": "string"
          },
          "comment": "This Commands will turn-on the LED light on the device.",
          "commandType": "synchronous"
        },
        {
          "@type": "Command",
          "name": "turnoff",
          "comment": "This Commands will turn-off the LED light on the device.",
          "response": {
            "name": "turnoff",
            "schema": "string"
          },
          "commandType": "synchronous"
        }
      ],
      "@context": "http://azureiot.com/v1/contexts/Interface.json"
    }
    ```

    このインターフェイスでは、**Customer Name** などのデバイス プロパティ、**Temperature** などのテレメトリの種類、**turnon** などのコマンドを定義しています。

1. このインターフェイス ファイルの末尾に、**blink** というコマンド機能を追加します。 コマンドの追加前に、必ずコンマを追加してください。 定義を入力して、インターフェイス定義を編集するときの IntelliSense、オートコンプリート、検証機能の働きを確認してみてください。

    ```json
    {
      "@type": "Command",
      "description": "This command will begin blinking the LED for given time interval.",
      "name": "blink",
      "request": {
        "name": "blinkRequest",
        "schema": {
          "@type": "Object",
          "fields": [
            {
              "name": "interval",
              "schema": "long"
            }
          ]
        }
      },
      "response": {
        "name": "blinkResponse",
        "schema": "string"
      },
      "commandType": "synchronous"
    }
    ```

1. ファイルを保存します。

### <a name="create-the-model-file"></a>モデル ファイルの作成

モデル ファイルでは、IoT プラグ アンド プレイ デバイスで実装するインターフェイスを指定します。 通常、モデルには少なくとも 2 つのインターフェイスが存在することになります。デバイスの特定の機能を定義するインターフェイス (1 つ以上) と、すべての IoT プラグ アンド プレイ デバイスで実装が必要な標準インターフェイスです。

IoT プラグ アンド プレイ デバイスで実装するインターフェイスを指定するモデル ファイルを VS Code で作成するには:

1. **Ctrl + Shift + P** キーを押してコマンド パレットを開きます。

1. 「**Plug and Play**」と入力して、 **[IoT Plug & Play: Create Capability Model]\(IoT プラグ アンド プレイ: 機能モデルの作成\)** コマンドを選択します。 次に、モデルの名前として「**SensorboxModel**」と入力します。 VS Code によって、**SensorboxModel.capabilitymodel.json** という名前のサンプル インターフェイス ファイルが作成されます。

1. このファイルの内容を次の JSON で置き換えます。`@id` フィールドと `EnvironmentalSensor` インターフェイスの `{your name}` は、**EnvironmentalSensor.interface.json** ファイルで使用したのと同じ値に置き換えてください。 インターフェイスをリポジトリに保存するには、インターフェイス ID を一意のものにする必要があります。

    ```json
    {
      "@id": "urn:{your name}:SensorboxModel:1",
      "@type": "CapabilityModel",
      "displayName": "Environmental Sensorbox Model",
      "implements": [
        {
          "schema": "urn:{your name}:EnvironmentalSensor:1",
          "name": "environmentalSensor"
        },
        {
          "schema": "urn:azureiot:DeviceManagement:DeviceInformation:1",
          "name": "deviceinfo"
        }
      ],
      "@context": "http://azureiot.com/v1/contexts/CapabilityModel.json"
    }
    ```

    このモデルでは、独自の **EnvironmentalSensor** インターフェイスと標準の **DeviceInformation** インターフェイスを実装したデバイスを定義しています。

1. ファイルを保存します。

### <a name="download-the-deviceinformation-interface"></a>DeviceInformation インターフェイスのダウンロード

モデルからスケルトン コードを生成するには、"*パブリック モデル リポジトリ*" にある **DeviceInformation** のローカル コピーを作成する必要があります。 パブリック モデル リポジトリには、**DeviceInformation** インターフェイスが既に存在しています。

VS Code を使用してパブリック モデル リポジトリから **DeviceInformation** インターフェイスをダウンロードするには:

1. **Ctrl + Shift + P** キーを押してコマンド パレットを開きます。

1. 「**Plug and Play**」と入力し、 **[Open Model Repository]\(モデル リポジトリを開く\)** コマンドを選択して、 **[Open Public Model Repository]\(パブリック モデル リポジトリを開く\)** を選択します。

1. **[インターフェイス]** を選択し、ID が `urn:azureiot:DeviceManagement:DeviceInformation:1` であるデバイス情報インターフェイスを選んで、 **[ダウンロード]** を選択します。

これで、デバイス機能モデルを構成する 3 つのファイルが揃いました。

* urn_azureiot_DeviceManagement_DeviceInformation_1.interface.json
* EnvironmentalSensor.interface.json
* SensorboxModel.capabilitymodel.json

## <a name="publish-the-model"></a>モデルを発行する

デバイス機能モデルを Azure IoT エクスプローラー ツールで読み取るには、会社リポジトリに発行する必要があります。 VS Code から発行する場合、会社リポジトリの接続文字列が必要です。

1. [Azure Certified for IoT ポータル](https://aka.ms/ACFI)にアクセスします。

1. Microsoft の "_職場アカウント_" でポータルにサインインします。

1. **[Company repository]\(会社リポジトリ\)** 、 **[接続文字列]** の順に選択します。

1. 接続文字列をコピーします。

VS Code で会社リポジトリを開くには:

1. **Ctrl + Shift + P** キーを押してコマンド パレットを開きます。

1. 「**Plug and Play**」と入力し、 **[IoT Plug & Play: Open Model Repository]\(IoT プラグ アンド プレイ: モデル リポジトリを開く\)** コマンドを選択します。

1. **[Open Organizational Model Repository]\(組織のモデル リポジトリを開く\)** を選択して、接続文字列を貼り付けます。

1. **Enter** キーを押して会社リポジトリを開きます。

会社リポジトリにデバイス機能モデルとインターフェイスを発行するには:

1. **Ctrl + Shift + P** キーを押してコマンド パレットを開きます。

1. 「**Plug and Play**」と入力し、 **[IoT Plug & Play: Submit files to Model Repository]\(IoT プラグ アンド プレイ: モデル リポジトリにファイルを送信する\)** コマンドを選択します。

1. **EnvironmentalSensor.interface.json** ファイルと **SensorboxModel.capabilitymodel.json** ファイルを選択して、 **[OK]** を選択します。

これで、ファイルが会社リポジトリに保存されました。

## <a name="generate-code"></a>コードを生成する

**VS Code 向け Azure IoT Device Workbench 拡張機能**を使用して、モデルからスケルトン C コードを生成できます。 VS Code でスケルトン コードを生成するには:

1. **Ctrl + Shift + P** キーを押してコマンド パレットを開きます。

1. 「**Plug and Play**」と入力し、 **[IoT Plug and Play: Generate Device Code Stub]\(IoT プラグ アンド プレイ: デバイス コード スタブの生成\)** コマンドを選択します。

1. **SensorboxModel.capabilitymodel.json** 機能モデル ファイルを選択します。

1. プロジェクト名として、「**sensorbox_app**」と入力します。

1. 言語として、 **[ANSI C]** を選択します。

1. ターゲットとして、 **[CMake プロジェクト]** を選択します。

1. 接続方法として、 **[Via IoT Hub device connection string]\(IoT Hub デバイス接続文字列による\)** を選択します。

VS Code によってスケルトン C コードが生成され、**modelcode** フォルダー内の **sensorbox_app** フォルダーにファイルが保存されます。 VS Code によって新しいウィンドウが開かれ、生成されたコード ファイルが表示されます。

## <a name="update-the-generated-code"></a>生成されたコードの更新

コードをビルドして実行する前に、スタブ化したプロパティ、テレメトリ、コマンドを実装する必要があります。

VS Code でスタブ化済みコードの実装を提供するには:

1. **SensorboxModel_impl.c** ファイルを開きます。

1. スタブ化した関数を実装するコードを追加します。

1. 変更を保存します。

## <a name="build-the-code"></a>コードのビルド

Azure IoT ハブでコードを実行して IoT プラグ アンド プレイ デバイスをテストするには、コードをコンパイルする必要があります。

**sensorbox_app** フォルダー内の **Readme.md** ファイルに記載されている手順に従って、Windows 上でコードをビルドして実行してください。 以下のセクションでは、デバイス コードの実行時に使用するデバイスの接続文字列を取得する方法について説明します。

## <a name="test-the-code"></a>コードのテスト

コードを実行すると、IoT Hub と接続され、サンプルのテレメトリ値とプロパティの送信が開始されます。 また、デバイスが IoT Hub から送信されたコードに反応します。 この動作を検証するには:

1. IoT ハブを作成するには:

    ```azurecli-interactive
    az group create --name environmentalsensorresources --location eastus
    az iot hub create --name {your iot hub name} \
      --resource-group environmentalsensorresources --sku F1
    ```

1. デバイスを IoT ハブに追加し、その接続文字列を取得します。

    ```azurecli-interactive
    az iot hub device-identity create --hub-name {your iot hub name} --device-id MyPnPDevice
    az iot hub device-identity show-connection-string --hub-name {your iot hub name} --device-id MyPnPDevice --output table
    ```

    接続文字列をメモします。

1. コマンド プロンプトで、SDK とサンプルをビルドした **azure-iot-sdk-c** フォルダーに移動します。 そこから、**cmake\\sensorbox_app\\Release** フォルダーに移動します。

1. 次のコマンドを実行します。

    ```cmd
    sensorbox_app.exe {your device connection string}
    ```

1. Azure IoT エクスプローラー ツールを使用して、IoT ハブに接続された IoT プラグ アンド プレイ デバイスを操作します。 詳細については、「[Azure IoT エクスプローラーをインストールして使用する](./howto-install-iot-explorer.md)」を参照してください。

## <a name="next-steps"></a>次の手順

これで、認定用の IoT プラグ アンド プレイを構築できたので、次の方法を確認してください。

> [!div class="nextstepaction"]
> [認定に対応したデバイスを構築する](tutorial-build-device-certification.md)
