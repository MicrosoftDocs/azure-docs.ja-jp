---
title: Azure IoT Hub デバイス ストリーム C# クイック スタート (プレビュー) | Microsoft Docs
description: このクイック スタートでは、IoT Hub を通じて確立されたデバイス ストリームを介して通信する 2 つのサンプル C# アプリケーションを実行します。
author: rezasherafat
manager: briz
ms.service: iot-hub
services: iot-hub
ms.devlang: csharp
ms.topic: quickstart
ms.custom: mvc
ms.date: 03/14/2019
ms.author: rezas
ms.openlocfilehash: 2853bd5539a40e3b38927f619756fe37a4cec984
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/18/2019
ms.locfileid: "59006863"
---
# <a name="quickstart-communicate-to-device-applications-in-c-via-iot-hub-device-streams-preview"></a>クイック スタート:IoT Hub デバイス ストリームを介して C# でデバイス アプリケーションと通信する (プレビュー)

[!INCLUDE [iot-hub-quickstarts-3-selector](../../includes/iot-hub-quickstarts-3-selector.md)]

Microsoft Azure IoT Hub は現在、[プレビュー機能](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)としてデバイス ストリームをサポートしています。

[IoT Hub デバイス ストリーム](./iot-hub-device-streams-overview.md)を使用すると、サービス アプリケーションとデバイス アプリケーションが、安全でファイアウォールに対応した方法で通信できます。 このクイック スタートには、デバイス ストリームを利用してデータをやり取り (エコー) する 2 つの C# プログラムが含まれています。

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Azure サブスクリプションがない場合は、開始する前に[無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)を作成してください。

## <a name="prerequisites"></a>前提条件

デバイス ストリームのプレビューは現在、次のリージョンで作成された IoT Hub に対してのみサポートされています。

  - **米国中部**
  - **米国中部 EUAP**

このクイック スタートで実行する 2 つのサンプル アプリケーションは、C# を使って書かれています。 開発用コンピューター上に .NET Core SDK 2.1.0 以降が必要です。

複数のプラットフォームに対応する .NET Core SDK を [.NET](https://www.microsoft.com/net/download/all) からダウンロードできます。

開発コンピューターに現在インストールされている C# のバージョンは、次のコマンドを使って確認できます。

```
dotnet --version
```

次のコマンドを実行して、Microsoft Azure IoT Extension for Azure CLI を Cloud Shell インスタンスに追加します。 IoT Hub、IoT Edge、IoT Device Provisioning Service (DPS) 固有のコマンドが Azure CLI に追加されます。

```azurecli-interactive
az extension add --name azure-cli-iot-ext
```

https://github.com/Azure-Samples/azure-iot-samples-csharp/archive/master.zip からサンプル C# プロジェクトをダウンロードし、ZIP アーカイブを抽出します。 デバイス側とサービス側の両方で必要になります。

## <a name="create-an-iot-hub"></a>IoT Hub の作成

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub-device-streams.md)]

## <a name="register-a-device"></a>デバイスの登録

デバイスを IoT ハブに接続するには、あらかじめ IoT ハブに登録しておく必要があります。 このクイック スタートでは、Azure Cloud Shell を使用して、シミュレートされたデバイスを登録します。

1. Azure Cloud Shell で次のコマンドを実行してデバイス ID を作成します。

   **YourIoTHubName**: このプレースホルダーは、実際の IoT ハブに対して選んだ名前に置き換えてください。

   **MyDevice**: これは、登録済みデバイスに付けられた名前です。 示されているように、MyDevice を使用します。 デバイスに別の名前を選択した場合は、この記事全体でその名前を使用する必要があります。また、サンプル アプリケーションを実行する前に、アプリケーション内のデバイス名を更新してください。

    ```azurecli-interactive
    az iot hub device-identity create --hub-name YourIoTHubName --device-id MyDevice
    ```

2. Azure Cloud Shell で次のコマンドを実行して、登録したデバイスの "_デバイス接続文字列_" を取得します。

   **YourIoTHubName**: このプレースホルダーは、実際の IoT ハブに対して選んだ名前に置き換えてください。

    ```azurecli-interactive
    az iot hub device-identity show-connection-string --hub-name YourIoTHubName --device-id MyDevice --output table
    ```

    次の例のような、デバイスの接続文字列をメモしておきます。

   `HostName={YourIoTHubName}.azure-devices.net;DeviceId=MyDevice;SharedAccessKey={YourSharedAccessKey}`

    この値は、このクイック スタートの後の方で使います。

3. また、サービス側アプリケーションが IoT ハブに接続してデバイス ストリームを確立できるようにするには、IoT ハブの "_サービス接続文字列_" も必要です。 次のコマンドを実行すると、自分の IoT ハブのこの値が取得されます。

   **YourIoTHubName**: このプレースホルダーは、実際の IoT ハブに対して選んだ名前に置き換えてください。

    ```azurecli-interactive
    az iot hub show-connection-string --policy-name service --name YourIoTHubName
    ```

    次のような戻り値をメモしておきます。

   `"HostName={YourIoTHubName}.azure-devices.net;SharedAccessKeyName=service;SharedAccessKey={YourSharedAccessKey}"`

## <a name="communicate-between-device-and-service-via-device-streams"></a>デバイス ストリームを介したデバイスとサービスの間の通信

### <a name="run-the-service-side-application"></a>サービス側アプリケーションの実行

解凍したプロジェクト フォルダーの `iot-hub/Quickstarts/device-streams-echo/service` に移動します。 以下の情報が必要になります。

| パラメーター名 | パラメーター値 |
|----------------|-----------------|
| `ServiceConnectionString` | お使いの IoT ハブのサービス接続文字列を指定します。 |
| `DeviceId` | 前に作成したデバイスの ID を指定します (例: MyDevice)。 |

次のようにコードをコンパイルして実行します。

```
cd ./iot-hub/Quickstarts/device-streams-echo/service/

# Build the application
dotnet build

# Run the application
# In Linux/MacOS
dotnet run "<ServiceConnectionString>" "<MyDevice>"

# In Windows
dotnet run <ServiceConnectionString> <MyDevice>
```

> [!NOTE]
> デバイス側のアプリケーションが時間内に応答しない場合、タイムアウトが発生します。

### <a name="run-the-device-side-application"></a>デバイス側アプリケーションの実行

解凍したプロジェクト フォルダーの `iot-hub/Quickstarts/device-streams-echo/device` ディレクトリに移動します。 以下の情報が必要になります。

| パラメーター名 | パラメーター値 |
|----------------|-----------------|
| `DeviceConnectionString` | お使いの IoT ハブのデバイス接続文字列を指定します。 |

次のようにコードをコンパイルして実行します。

```
cd ./iot-hub/Quickstarts/device-streams-echo/device/

# Build the application
dotnet build

# Run the application
# In Linux/MacOS
dotnet run "<DeviceConnectionString>"

# In Windows
dotnet run <DeviceConnectionString>
```

最後の手順の終わりに、サービス側プログラムはデバイスへのストリームを開始し、確立後はストリームを介してサービスに文字列バッファーを送信します。 このサンプルでは、​​サービス側プログラムは単に同じデータをデバイスにエコーバックして、2 つのアプリケーション間の双方向の通信が成功したことを示します。 次の図を見てください。

デバイス側のコンソール出力: ![代替テキスト](./media/quickstart-device-streams-echo-csharp/device-console-output.png "デバイス側のコンソール出力")

サービス側のコンソール出力: ![代替テキスト](./media/quickstart-device-streams-echo-csharp/service-console-output.png "サービス側のコンソール出力")

ストリームを介して送信されるトラフィックは、直接送信されるのではなく、IoT Hub を通じてトンネリングされます。 このことには、[これらの利点](./iot-hub-device-streams-overview.md#benefits)があります。

## <a name="clean-up-resources"></a>リソースのクリーンアップ

[!INCLUDE [iot-hub-quickstarts-clean-up-resources](../../includes/iot-hub-quickstarts-clean-up-resources-device-streams.md)]

## <a name="next-steps"></a>次の手順

このクイック スタートでは、IoT ハブの設定、デバイスの登録、デバイス側とサービス側の C# アプリケーション間のデバイス ストリームの確立、そのストリームを使用したアプリケーション間のデータのやり取りを行いました。

以下のリンクを使用して、デバイス ストリームについてさらに詳しく学習します。

> [!div class="nextstepaction"]
> [デバイス ストリームの概要](./iot-hub-device-streams-overview.md)
