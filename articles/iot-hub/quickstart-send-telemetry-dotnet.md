---
title: クイックスタート - Azure IoT Hub への利用統計情報の送信に関するクイックスタート (C#) | Microsoft Docs
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
- devx-track-azurecli
ms.date: 06/01/2020
ms.openlocfilehash: 914df5b80dee7da041b268a3aaf25ac493d0cf5b
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "98624441"
---
# <a name="quickstart-send-telemetry-from-a-device-to-an-iot-hub-and-read-it-with-a-service-application-net"></a>クイックスタート: デバイスから IoT Hub にテレメトリを送信してサービス アプリケーションで読み取る (.NET)

[!INCLUDE [iot-hub-quickstarts-1-selector](../../includes/iot-hub-quickstarts-1-selector.md)]

IoT Hub は、保管や処理のために IoT デバイスから大量のテレメトリをクラウドに取り込むことを可能にする Azure サービスです。 このクイックスタートでは、シミュレートされたデバイス アプリケーションから、IoT Hub を経由して、処理のためにサービス アプリケーションにテレメトリを送信します。

このクイック スタートでは、あらかじめ作成されている 2 つの C# アプリケーションを使います。1 つは利用統計情報を送信し、もう 1 つはHub から利用統計情報を読み取ります。 これら 2 つのアプリケーションを実行する前に、IoT Hub を作成し、デバイスを Hub に登録します。

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>前提条件

* このクイック スタートで実行する 2 つのサンプル アプリケーションは、C# を使って書かれています。 開発用マシン上に .NET Core SDK 3.1 以上が必要です。

    複数のプラットフォームに対応する .NET Core SDK を [.NET](https://www.microsoft.com/net/download/all) からダウンロードできます。

    開発コンピューターに現在インストールされている C# のバージョンは、次のコマンドを使って確認できます。

    ```cmd/sh
    dotnet --version
    ```

    > [!NOTE]
    > このクイックスタートでテレメトリの読み取りに使用する Event Hubs サービス コードをコンパイルするには、.NET Core SDK 3.1 以上をお勧めします。


* [https://github.com/Azure-Samples/azure-iot-samples-csharp/archive/master.zip](https://github.com/Azure-Samples/azure-iot-samples-csharp/archive/master.zip) から Azure IoT C# サンプルをダウンロードし、ZIP アーカイブを展開します。

* ポート 8883 がファイアウォールで開放されていることを確認してください。 このクイックスタートのデバイス サンプルでは、ポート 8883 を介して通信する MQTT プロトコルを使用しています。 このポートは、企業や教育用のネットワーク環境によってはブロックされている場合があります。 この問題の詳細と対処方法については、「[IoT Hub への接続 (MQTT)](iot-hub-mqtt-support.md#connecting-to-iot-hub)」を参照してください。

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment-no-header.md)]

[!INCLUDE [iot-hub-cli-version-info](../../includes/iot-hub-cli-version-info.md)]

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
    az iot hub device-identity connection-string show --hub-name {YourIoTHubName} --device-id MyDotnetDevice --output table
    ```

    次のようなデバイス接続文字列をメモしておきます。

   `HostName={YourIoTHubName}.azure-devices.net;DeviceId=MyDotnetDevice;SharedAccessKey={YourSharedAccessKey}`

    この値は、このクイックスタートの後の方で使用します。

3. また、サービス アプリケーションが IoT Hub に接続してメッセージを取得できるようにするには、IoT Hub の "_Event Hubs 互換エンドポイント_"、"_Event Hubs 互換パス_"、"_サービス主キー_" も必要です。 次のコマンドは、お使いの IoT Hub に対するこれらの値を取得します。

   **YourIoTHubName**: このプレースホルダーは、実際の IoT Hub に対して選んだ名前に置き換えてください。

    ```azurecli-interactive
    az iot hub show --query properties.eventHubEndpoints.events.endpoint --name {YourIoTHubName}

    az iot hub show --query properties.eventHubEndpoints.events.path --name {YourIoTHubName}

    az iot hub policy show --name service --query primaryKey --hub-name {YourIoTHubName}
    ```

    クイックスタートの後の方で使うので、これら 3 つの値をメモしておきます。

## <a name="send-simulated-telemetry"></a>シミュレートされたテレメトリの送信

シミュレートされたデバイス アプリケーションは、IoT Hub 上のデバイスに固有のエンドポイントに接続し、シミュレートされた温度と湿度のテレメトリを送信します。

1. ローカル ターミナル ウィンドウで、サンプルの C# プロジェクトのルート フォルダーに移動します。 **iot-hub\Quickstarts\SimulatedDevice** フォルダーに移動します。

2. ローカル ターミナル ウィンドウで次のコマンドを実行して、シミュレートされたデバイス アプリケーションに必要なパッケージをインストールします。

    ```cmd/sh
    dotnet restore
    ```

3. ローカル ターミナル ウィンドウで、前に書き留めておいたデバイス接続文字列を指定して次のコマンドを実行し、シミュレートされたデバイス アプリケーションをビルドして実行します。

    ```cmd/sh
    dotnet run -- {DeviceConnectionString}
    ```

    次のスクリーンショットは、シミュレートされたデバイス アプリケーションが IoT Hub にテレメトリを送信したときの出力を示しています。

    ![シミュレートされたデバイスを実行する](media/quickstart-send-telemetry-dotnet/simulated-device.png)

## <a name="read-the-telemetry-from-your-hub"></a>Hub からテレメトリを読み取る

サービス アプリケーションは、IoT Hub 上のサービス側 **Events** エンドポイントに接続します。 このアプリケーションは、シミュレートされたデバイスから送信されたデバイスとクラウドの間のメッセージを受信します。 通常、IoT Hub サービス アプリケーションはクラウドで実行され、device-to-cloud メッセージを受信して処理します。

1. 別のローカル ターミナル ウィンドウで、サンプルの C# プロジェクトのルート フォルダーに移動します。 **iot-hub\Quickstarts\ReadD2cMessages** フォルダーに移動します。

2. ローカル ターミナル ウィンドウで次のコマンドを実行して、アプリケーションに必要なライブラリをインストールします。

    ```cmd/sh
    dotnet restore
    ```

3. ローカル ターミナル ウィンドウで次のコマンドを実行して、パラメーター オプションを表示します。

    ```cmd/sh
    dotnet run
    ```

4. ローカル ターミナル ウィンドウで次のいずれかのコマンドを実行し、アプリケーションをビルドして実行します。

    ```cmd/sh
    dotnet run -- -c {EventHubConnectionString}
    ```

    or

    ```cmd/sh
    dotnet run -- -e {EventHubCompatibleEndpoint} -n {EventHubName} -s {SharedAccessKey}
    ```

    次のスクリーンショットは、シミュレートされたデバイスからHub に送信されたテレメトリをサービス アプリケーションが受信したときの出力を示しています。

    ![サービス アプリケーションの実行](media/quickstart-send-telemetry-dotnet/read-device-to-cloud.png)

## <a name="clean-up-resources"></a>リソースをクリーンアップする

[!INCLUDE [iot-hub-quickstarts-clean-up-resources](../../includes/iot-hub-quickstarts-clean-up-resources.md)]

## <a name="next-steps"></a>次のステップ

このクイックスタートでは、IoT Hub を設定し、デバイスを登録しました。C# アプリケーションを使用してシミュレートされたテレメトリをHub に送信し、単純なサービス アプリケーションを使用してHub からテレメトリを読み取りました。

サービス アプリケーションからシミュレートされたデバイスを制御する方法については、次のクイックスタートに進んでください。

> [!div class="nextstepaction"]
> [クイック スタート: IoT Hub に接続されたデバイスを制御する](quickstart-control-device-dotnet.md)
