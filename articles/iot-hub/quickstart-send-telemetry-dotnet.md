---
title: Azure IoT Hub への利用統計情報の送信に関するクイック スタート (C#) | Microsoft Docs
description: このクイック スタートでは、2 つのサンプル C# アプリケーションを実行して、IoT Hub にシミュレートされた利用統計情報を送信し、クラウドで処理するために IoT Hub から利用統計情報を読み取ります。
author: robinsh
manager: philmea
ms.author: robinsh
ms.service: iot-hub
services: iot-hub
ms.devlang: csharp
ms.topic: quickstart
ms.custom:
- mvc
- mqtt
- 'Role: Cloud Development'
ms.date: 06/01/2020
ms.openlocfilehash: 98b50649b5a788270fa2b4cd8b62ca5598daa25f
ms.sourcegitcommit: a76ff927bd57d2fcc122fa36f7cb21eb22154cfa
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/28/2020
ms.locfileid: "87320478"
---
# <a name="quickstart-send-telemetry-from-a-device-to-an-iot-hub-and-read-it-with-a-back-end-application-net"></a>クイック スタート:デバイスから IoT Hub に利用統計情報を送信してバックエンド アプリケーションで読み取る (.NET)

[!INCLUDE [iot-hub-quickstarts-1-selector](../../includes/iot-hub-quickstarts-1-selector.md)]

IoT Hub は、保管や処理のために IoT デバイスから大量のテレメトリをクラウドに取り込むことを可能にする Azure サービスです。 このクイック スタートでは、シミュレートされたデバイス アプリケーションから、IoT Hub を経由して、処理のためのバックエンド アプリケーションに、テレメトリを送信します。

このクイック スタートでは、あらかじめ作成されている 2 つの C# アプリケーションを使います。1 つは利用統計情報を送信し、もう 1 つはハブから利用統計情報を読み取ります。 これら 2 つのアプリケーションを実行する前に、IoT Hub を作成し、デバイスを Hub に登録します。

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) を作成してください。

## <a name="prerequisites"></a>前提条件

このクイック スタートで実行する 2 つのサンプル アプリケーションは、C# を使って書かれています。 開発用マシン上に .NET Core SDK 3.0 以降が必要です。

複数のプラットフォームに対応する .NET Core SDK を [.NET](https://www.microsoft.com/net/download/all) からダウンロードできます。

開発コンピューターに現在インストールされている C# のバージョンは、次のコマンドを使って確認できます。

```cmd/sh
dotnet --version
```

> [!NOTE]
> このクイックスタートでテレメトリの読み取りに使用する Event Hubs サービス コードをコンパイルするには、.NET Core SDK 3.0 以上をお勧めします。 「[Hub からテレメトリを読み取る](#read-the-telemetry-from-your-hub)」セクションの説明に従って、サービス コードの言語バージョンを preview に設定した場合は、.NET Core SDK 2.1 を使用できます。

次のコマンドを実行して、Microsoft Azure IoT Extension for Azure CLI を Cloud Shell インスタンスに追加します。 IoT Hub、IoT Edge、IoT Device Provisioning Service (DPS) 固有のコマンドが Azure CLI に追加されます。

```azurecli-interactive
az extension add --name azure-iot
```

[!INCLUDE [iot-hub-cli-version-info](../../includes/iot-hub-cli-version-info.md)]

[https://github.com/Azure-Samples/azure-iot-samples-csharp/archive/master.zip](https://github.com/Azure-Samples/azure-iot-samples-csharp/archive/master.zip) から Azure IoT C# サンプルをダウンロードし、ZIP アーカイブを展開します。

ポート 8883 がファイアウォールで開放されていることを確認してください。 このクイックスタートのデバイス サンプルでは、ポート 8883 を介して通信する MQTT プロトコルを使用しています。 このポートは、企業や教育用のネットワーク環境によってはブロックされている場合があります。 この問題の詳細と対処方法については、「[IoT Hub への接続 (MQTT)](iot-hub-mqtt-support.md#connecting-to-iot-hub)」を参照してください。

## <a name="create-an-iot-hub"></a>IoT Hub の作成

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="register-a-device"></a>デバイスの登録

デバイスを IoT Hub に接続するには、あらかじめ IoT Hub に登録しておく必要があります。 このクイック スタートでは、Azure Cloud Shell を使用して、シミュレートされたデバイスを登録します。

1. Azure Cloud Shell で次のコマンドを実行してデバイス ID を作成します。

   **YourIoTHubName**: このプレースホルダーは、実際の IoT Hub に対して選んだ名前に置き換えてください。

   **MyDotnetDevice**: これは、登録するデバイスの名前です。 示されているように、**MyDotnetDevice** を使用することをお勧めします。 デバイスに別の名前を選択した場合は、この記事全体でその名前を使用する必要があります。また、サンプル アプリケーションを実行する前に、アプリケーション内のデバイス名を更新してください。

    ```azurecli-interactive
    az iot hub device-identity create --hub-name {YourIoTHubName} --device-id MyDotnetDevice
    ```

2. Azure Cloud Shell で次のコマンドを実行して、登録したデバイスの "_デバイス接続文字列_" を取得します。

   **YourIoTHubName**: このプレースホルダーは、実際の IoT Hub に対して選んだ名前に置き換えてください。

    ```azurecli-interactive
    az iot hub device-identity show-connection-string --hub-name {YourIoTHubName} --device-id MyDotnetDevice --output table
    ```

    次のようなデバイス接続文字列をメモしておきます。

   `HostName={YourIoTHubName}.azure-devices.net;DeviceId=MyDotnetDevice;SharedAccessKey={YourSharedAccessKey}`

    この値は、このクイックスタートの後の方で使用します。

3. また、バックエンド アプリケーションが IoT Hub に接続してメッセージを取得できるようにするには、IoT Hub の "_Event Hubs 互換エンドポイント_"、"_Event Hubs 互換パス_"、および "_サービス主キー_" も必要です。 次のコマンドは、お使いの IoT Hub に対するこれらの値を取得します。

   **YourIoTHubName**: このプレースホルダーは、実際の IoT Hub に対して選んだ名前に置き換えてください。

    ```azurecli-interactive
    az iot hub show --query properties.eventHubEndpoints.events.endpoint --name {YourIoTHubName}

    az iot hub show --query properties.eventHubEndpoints.events.path --name {YourIoTHubName}

    az iot hub policy show --name service --query primaryKey --hub-name {YourIoTHubName}
    ```

    クイックスタートの後の方で使うので、これら 3 つの値をメモしておきます。

## <a name="send-simulated-telemetry"></a>シミュレートされたテレメトリの送信

シミュレートされたデバイス アプリケーションは、IoT Hub 上のデバイスに固有のエンドポイントに接続し、シミュレートされた温度と湿度のテレメトリを送信します。

1. ローカル ターミナル ウィンドウで、サンプルの C# プロジェクトのルート フォルダーに移動します。 **iot-hub\Quickstarts\simulated-device** フォルダーに移動します。

2. 適当なテキスト エディターで **SimulatedDevice.cs** ファイルを開きます。

    `s_connectionString` 変数の値を、前にメモしたデバイス接続文字列に置き換えます。 その後、変更を **SimulatedDevice.cs** に保存します。

3. ローカル ターミナル ウィンドウで次のコマンドを実行して、シミュレートされたデバイス アプリケーションに必要なパッケージをインストールします。

    ```cmd/sh
    dotnet restore
    ```

4. ローカル ターミナル ウィンドウで次のコマンドを実行し、シミュレートされたデバイス アプリケーションをビルドして実行します。

    ```cmd/sh
    dotnet run
    ```

    次のスクリーンショットは、シミュレートされたデバイス アプリケーションが IoT Hub にテレメトリを送信したときの出力を示しています。

    ![シミュレートされたデバイスを実行する](media/quickstart-send-telemetry-dotnet/simulated-device.png)

## <a name="read-the-telemetry-from-your-hub"></a>Hub からテレメトリを読み取る

バックエンド アプリケーションは、IoT Hub 上のサービス側 **Events** エンドポイントに接続します。 このアプリケーションは、シミュレートされたデバイスから送信されたデバイスとクラウドの間のメッセージを受信します。 通常、IoT Hub のバックエンド アプリケーションはクラウド内で実行され、デバイスとクラウドの間のメッセージを受信して処理します。

1. 別のローカル ターミナル ウィンドウで、サンプルの C# プロジェクトのルート フォルダーに移動します。 **iot-hub\Quickstarts\read-d2c-messages** フォルダーに移動します。

2. 適当なテキスト エディターで **ReadDeviceToCloudMessages.cs** ファイルを開きます。 次の変数を更新し、ご自身の変更をファイルに保存します。

    | 変数 | 値 |
    | -------- | ----------- |
    | `EventHubsCompatibleEndpoint` | 変数の値を、前にメモした Event Hubs 互換エンドポイントに置き換えます。 |
    | `EventHubName`                | 変数の値を、前にメモした Event Hubs 互換パスに置き換えます。 |
    | `IotHubSasKey`                | 変数の値を、前にメモしたサービス主キーに置き換えます。 |

    > [!NOTE]
    > .NET Core SDK 2.1 を使用している場合、コードをコンパイルするには、言語バージョンを preview に設定する必要があります。 これを行うには、**read-d2c-messages.csproj** ファイルを開き、`<LangVersion>` 要素の値を `preview` に設定します。

3. ローカル ターミナル ウィンドウで次のコマンドを実行して、バックエンド アプリケーションに必要なライブラリをインストールします。

    ```cmd/sh
    dotnet restore
    ```

4. ローカル ターミナル ウィンドウで次のコマンドを実行し、バックエンド アプリケーションをビルドして実行します。

    ```cmd/sh
    dotnet run
    ```

    次のスクリーンショットは、シミュレートされたデバイスがハブに送信した利用統計情報をバックエンド アプリケーションが受信したときの出力を示しています。

    ![バックエンド アプリケーションを実行する](media/quickstart-send-telemetry-dotnet/read-device-to-cloud.png)

## <a name="clean-up-resources"></a>リソースをクリーンアップする

[!INCLUDE [iot-hub-quickstarts-clean-up-resources](../../includes/iot-hub-quickstarts-clean-up-resources.md)]

## <a name="next-steps"></a>次のステップ

このクイックスタートでは、IoT Hub を設定し、デバイスを登録し、C# アプリケーションを使ってシミュレートされた利用統計情報をハブに送信し、簡単なバックエンド アプリケーションを使ってハブから利用統計情報を読み取りました。

バックエンド アプリケーションからシミュレートされたデバイスを制御する方法を学習するには、次のクイック スタートに進んでください。

> [!div class="nextstepaction"]
> [クイック スタート: IoT Hub に接続されたデバイスを制御する](quickstart-control-device-dotnet.md)
