---
title: クイックスタート - Azure IoT Hub デバイス ストリームを使用して C# でデバイス アプリと通信する
description: このクイックスタートでは、IoT Hub を通じて確立されたデバイス ストリームを介して通信する 2 つのサンプル C# アプリケーションを実行します。
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.devlang: csharp
ms.topic: quickstart
ms.custom: mvc, devx-track-azurecli
ms.date: 03/14/2019
ms.author: robinsh
ms.openlocfilehash: 3eb65db27e5b96f4b12973154bc860a2ab3df020
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "98624610"
---
# <a name="quickstart-communicate-to-a-device-application-in-c-via-iot-hub-device-streams-preview"></a>クイック スタート:IoT Hub デバイス ストリームを介して C# でデバイス アプリケーションと通信する (プレビュー)

[!INCLUDE [iot-hub-quickstarts-3-selector](../../includes/iot-hub-quickstarts-3-selector.md)]

Azure IoT Hub は現在、[プレビュー機能](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)としてデバイス ストリームをサポートしています。

[IoT Hub デバイス ストリーム](./iot-hub-device-streams-overview.md)を使用すると、サービス アプリケーションとデバイス アプリケーションが、安全でファイアウォールに対応した方法で通信できます。 このクイックスタートには、デバイス ストリームを利用してデータをやり取り (エコー) する 2 つの C# アプリケーションが含まれています。

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>前提条件

* デバイス ストリームのプレビューは現在、以下のリージョンで作成された IoT ハブに対してのみサポートされています。
  * 米国中部
  * 米国中部 EUAP
  * 北ヨーロッパ
  * 東南アジア

* このクイックスタートで実行する 2 つのサンプル アプリケーションは、C# を使って書かれています。 開発マシン上に .NET Core SDK 2.1.0 以降が必要です。

    [複数のプラットフォームに対応する .NET Core SDK を .NET から](https://www.microsoft.com/net/download/all)ダウンロードします。

    次のコマンドを使用して、開発マシンの C# の現在のバージョンを確認します。

    ```
    dotnet --version
    ```

* [Azure IoT C# サンプルをダウンロード](https://github.com/Azure-Samples/azure-iot-samples-csharp/archive/master.zip)し、ZIP アーカイブを展開します。 デバイス側とサービス側の両方で必要になります。

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment-no-header.md)]

[!INCLUDE [iot-hub-cli-version-info](../../includes/iot-hub-cli-version-info.md)]

## <a name="create-an-iot-hub"></a>IoT Hub の作成

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="register-a-device"></a>デバイスの登録

デバイスを IoT Hub に接続するには、あらかじめ IoT Hub に登録しておく必要があります。 このセクションでは、Azure Cloud Shell を使用して、シミュレートされたデバイスを登録します。

1. Cloud Shell で次のコマンドを実行してデバイス ID を作成します。

   > [!NOTE]
   > * *YourIoTHubName* プレースホルダーを、IoT ハブ用に選択した名前に置き換えます。
   > * 登録しているデバイスの名前については、示されているように、*MyDevice* を使用することをお勧めします。 デバイスに別の名前を選択した場合は、この記事全体でその名前を使用し、サンプル アプリケーションを実行する前に、アプリケーション内のデバイス名を更新します。

    ```azurecli-interactive
    az iot hub device-identity create --hub-name {YourIoTHubName} --device-id MyDevice
    ```

1. 登録したデバイスの "*デバイス接続文字列*" を取得するには、Cloud Shell で次のコマンドを実行します。

   > [!NOTE]
   > *YourIoTHubName* プレースホルダーを、IoT ハブ用に選択した名前に置き換えます。

    ```azurecli-interactive
    az iot hub device-identity connection-string show --hub-name {YourIoTHubName} --device-id MyDevice --output table
    ```

    このクイックスタートの後の方で使用できるように、返されたデバイス接続文字列を書き留めておきます。 次の例のようになります。

   `HostName={YourIoTHubName}.azure-devices.net;DeviceId=MyDevice;SharedAccessKey={YourSharedAccessKey}`

3. また、サービス側アプリケーションが IoT ハブに接続してデバイス ストリームを確立できるようにするには、IoT ハブの "*サービス接続文字列*" も必要です。 次のコマンドを実行すると、自分の IoT ハブのこの値が取得されます。

   > [!NOTE]
   > *YourIoTHubName* プレースホルダーを、IoT ハブ用に選択した名前に置き換えます。

    ```azurecli-interactive
    az iot hub show-connection-string --policy-name service --name {YourIoTHubName} --output table
    ```

    このクイックスタートの後の方で使用できるように、返されたサービス接続文字列を書き留めておきます。 次の例のようになります。

   `"HostName={YourIoTHubName}.azure-devices.net;SharedAccessKeyName=service;SharedAccessKey={YourSharedAccessKey}"`

## <a name="communicate-between-the-device-and-the-service-via-device-streams"></a>デバイス ストリームを介したデバイスとサービスの間の通信

このセクションでは、デバイス側アプリケーションとサービス側アプリケーションの両方を実行し、両者間で通信します。

### <a name="run-the-service-side-application"></a>サービス側アプリケーションの実行

ローカル ターミナル ウィンドウで、解凍したプロジェクト フォルダーの `iot-hub/Quickstarts/device-streams-echo/service` ディレクトリに移動します。 以下の情報を手元に用意しておいてください。

| パラメーター名 | パラメーター値 |
|----------------|-----------------|
| `ServiceConnectionString` | IoT Hub のサービス接続文字列。 |
| `MyDevice` | 前に作成したデバイスの識別子。 |

以下のコマンドを使用して、コードをコンパイルし、実行します。

```
cd ./iot-hub/Quickstarts/device-streams-echo/service/

# Build the application
dotnet build

# Run the application
# In Linux or macOS
dotnet run "{ServiceConnectionString}" "MyDevice"

# In Windows
dotnet run {ServiceConnectionString} MyDevice
```
アプリケーションは、デバイス アプリケーションが使用可能になるまで待機します。

> [!NOTE]
> デバイス側のアプリケーションが時間内に応答しない場合、タイムアウトが発生します。

### <a name="run-the-device-side-application"></a>デバイス側アプリケーションの実行

別のローカル ターミナル ウィンドウで、解凍したプロジェクト フォルダーの `iot-hub/Quickstarts/device-streams-echo/device` ディレクトリに移動します。 以下の情報を手元に用意しておいてください。

| パラメーター名 | パラメーター値 |
|----------------|-----------------|
| `DeviceConnectionString` | IoT ハブのデバイス接続文字列。 |

以下のコマンドを使用して、コードをコンパイルし、実行します。

```
cd ./iot-hub/Quickstarts/device-streams-echo/device/

# Build the application
dotnet build

# Run the application
# In Linux or macOS
dotnet run "{DeviceConnectionString}"

# In Windows
dotnet run {DeviceConnectionString}
```

最後の手順の最後で、サービス側のアプリケーションによってデバイスへのストリームが開始されます。 ストリームが確立された後、アプリケーションはストリーム経由でサービスに文字列バッファーを送信します。 このサンプルでは、​​サービス側アプリケーションは単に同じデータをデバイスにエコーバックします。これは、2 つのアプリケーション間の双方向通信が成功したことを示します。

デバイス側でのコンソール出力:

![デバイス側でのコンソール出力](./media/quickstart-device-streams-echo-csharp/device-console-output.png)

サービス側でのコンソール出力:

![サービス側でのコンソール出力](./media/quickstart-device-streams-echo-csharp/service-console-output.png)

ストリームを介して送信されるトラフィックは、直接送信されるのではなく、IoT ハブを通じてトンネリングされます。 提供されるベネフィットについては、[デバイス ストリームのベネフィット](./iot-hub-device-streams-overview.md#benefits)に関する記事で詳しく説明しています。

## <a name="clean-up-resources"></a>リソースをクリーンアップする

[!INCLUDE [iot-hub-quickstarts-clean-up-resources-device-streams](../../includes/iot-hub-quickstarts-clean-up-resources-device-streams.md)]

## <a name="next-steps"></a>次のステップ

このクイックスタートでは、IoT ハブの設定、デバイスの登録、デバイス側とサービス側の C# アプリケーション間のデバイス ストリームの確立、そのストリームを使用したアプリケーション間のデータのやり取りを行いました。

デバイス ストリームについて詳しく学習するには、次を参照してください。

> [!div class="nextstepaction"]
> [デバイス ストリームの概要](./iot-hub-device-streams-overview.md)
