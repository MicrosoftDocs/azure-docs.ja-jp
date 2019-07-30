---
title: Azure IoT Hub からのデバイスの制御に関するクイック スタート (.NET) | Microsoft Docs
description: このクイック スタートでは、2 つのサンプル C# アプリケーションを実行します。 1 つのアプリケーションは、ハブに接続されたデバイスをリモートで制御できるバックエンド アプリケーションです。 もう 1 つのアプリケーションは、ハブに接続されたリモートで制御できるデバイスをシミュレートします。
author: robinsh
manager: philmea
ms.author: robinsh
ms.service: iot-hub
services: iot-hub
ms.devlang: csharp
ms.topic: quickstart
ms.custom: mvc
ms.date: 06/21/2019
ms.openlocfilehash: 751db0effb57f19db47be1eed166d7053d617e3d
ms.sourcegitcommit: ac1cfe497341429cf62eb934e87f3b5f3c79948e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/01/2019
ms.locfileid: "67491967"
---
# <a name="quickstart-control-a-device-connected-to-an-iot-hub-net"></a>クイック スタート:IoT ハブに接続されたデバイスを制御する (.NET)

[!INCLUDE [iot-hub-quickstarts-2-selector](../../includes/iot-hub-quickstarts-2-selector.md)]

IoT Hub は、IoT デバイスからクラウドに大量の利用統計情報を取り込み、クラウドからデバイスを管理することができる、Azure サービスです。 このクイック スタートでは、"*ダイレクト メソッド*" を使って、IoT ハブに接続されているシミュレートされたデバイスを制御します。 ダイレクト メソッドを使うと、IoT ハブに接続されたデバイスの動作をリモートで変更できます。

このクイック スタートでは、あらかじめ作成されている次の 2 つの .NET アプリケーションを使います。

* シミュレートされたデバイス アプリケーションは、バックエンド アプリケーションから呼び出されたダイレクト メソッドに応答します。 ダイレクト メソッドの呼び出しを受け取るため、このアプリケーションは IoT ハブ上のデバイス固有のエンドポイントに接続します。

* バックエンド アプリケーションは、シミュレートされたデバイスでダイレクト メソッドを呼び出します。 デバイスでダイレクト メソッドを呼び出すため、このアプリケーションは IoT ハブ上のサービス側エンドポイントに接続します。

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Azure サブスクリプションがない場合は、開始する前に[無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)を作成してください。

## <a name="prerequisites"></a>前提条件

このクイック スタートで実行する 2 つのサンプル アプリケーションは、C# を使って書かれています。 開発用コンピューター上に .NET Core SDK 2.1.0 以降が必要です。

複数のプラットフォームに対応する .NET Core SDK を [.NET](https://www.microsoft.com/net/download/all) からダウンロードできます。

開発コンピューターに現在インストールされている C# のバージョンは、次のコマンドを使って確認できます。

```cmd/sh
dotnet --version
```

次のコマンドを実行して、Microsoft Azure IoT Extension for Azure CLI を Cloud Shell インスタンスに追加します。 IoT Hub、IoT Edge、IoT Device Provisioning Service (DPS) 固有のコマンドが Azure CLI に追加されます。

```azurecli-interactive
az extension add --name azure-cli-iot-ext
```

まだ行っていない場合は、 https://github.com/Azure-Samples/azure-iot-samples-csharp/archive/master.zip からサンプル C# プロジェクトをダウンロードし、ZIP アーカイブを抽出します。

## <a name="create-an-iot-hub"></a>IoT Hub の作成

前出の[デバイスから IoT ハブへの利用統計情報の送信に関するクイック スタート](quickstart-send-telemetry-dotnet.md)を完了した場合は、この手順を省略できます。

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="register-a-device"></a>デバイスの登録

前出の[デバイスから IoT ハブへの利用統計情報の送信に関するクイック スタート](quickstart-send-telemetry-dotnet.md)を完了した場合は、この手順を省略できます。

デバイスを IoT ハブに接続するには、あらかじめ IoT ハブに登録しておく必要があります。 このクイック スタートでは、Azure Cloud Shell を使用して、シミュレートされたデバイスを登録します。

1. Azure Cloud Shell で次のコマンドを実行してデバイス ID を作成します。

   **YourIoTHubName**: このプレースホルダーは、実際の IoT ハブに対して選んだ名前に置き換えてください。

   **MyDotnetDevice**: 登録するデバイスの名前。 示されているように、**MyDotnetDevice** を使用します。 デバイスに別の名前を選択した場合は、この記事全体でその名前を使用し、サンプル アプリケーションを実行する前に、アプリケーション内のデバイス名を更新する必要があります。

    ```azurecli-interactive
    az iot hub device-identity create \
      --hub-name YourIoTHubName --device-id MyDotnetDevice
    ```

2. Azure Cloud Shell で次のコマンドを実行して、登録したデバイスの "_デバイス接続文字列_" を取得します。

   **YourIoTHubName**: このプレースホルダーは、実際の IoT ハブに対して選んだ名前に置き換えてください。

    ```azurecli-interactive
    az iot hub device-identity show-connection-string \
      --hub-name YourIoTHubName \
      --device-id MyDotnetDevice \
      --output table
    ```

    次のようなデバイス接続文字列をメモしておきます。

   `HostName={YourIoTHubName}.azure-devices.net;DeviceId=MyNodeDevice;SharedAccessKey={YourSharedAccessKey}`

    この値は、このクイック スタートの後の方で使います。

## <a name="retrieve-the-service-connection-string"></a>サービス接続文字列を取得する

また、バックエンド アプリケーションがハブに接続してメッセージを取得できるようにするには、お使いの IoT ハブの "_サービス接続文字列_" が必要です。 次のコマンドを実行すると、IoT ハブのサービス接続文字列が取得されます。

```azurecli-interactive
az iot hub show-connection-string --name YourIoTHubName --policy-name service --output table
```

次のようなサービス接続文字列をメモしておきます。

   `HostName={YourIoTHubName}.azure-devices.net;SharedAccessKeyName=service;SharedAccessKey={YourSharedAccessKey}`

この値は、このクイック スタートの後の方で使います。 サービス接続文字列はデバイス接続文字列とは異なります。  

## <a name="listen-for-direct-method-calls"></a>ダイレクト メソッドの呼び出しをリッスンする

シミュレートされたデバイス アプリケーションは、IoT ハブ上のデバイス固有エンドポイントに接続し、シミュレートされた利用統計情報を送信して、ハブからのダイレクト メソッド呼び出しをリッスンします。 このクイック スタートでは、ハブからのダイレクト メソッド呼び出しは、利用統計情報の送信間隔を変更するようデバイスに指示します。 シミュレートされたデバイスは、ダイレクト メソッドを実行した後、ハブに受信確認を返送します。

1. ローカル ターミナル ウィンドウで、サンプルの C# プロジェクトのルート フォルダーに移動します。 次に、**iot-hub\Quickstarts\simulated-device-2** フォルダーに移動します。

2. 適当なテキスト エディターで **SimulatedDevice.cs** ファイルを開きます。

    `s_connectionString` 変数の値を、前にメモしたデバイス接続文字列に置き換えます。 その後、変更を **SimulatedDevice.cs** ファイルに保存します。

3. ローカル ターミナル ウィンドウで次のコマンドを実行して、シミュレートされたデバイス アプリケーションに必要なパッケージをインストールします。

    ```cmd/sh
    dotnet restore
    ```

4. ローカル ターミナル ウィンドウで次のコマンドを実行し、シミュレートされたデバイス アプリケーションをビルドして実行します。

    ```cmd/sh
    dotnet run
    ```

    次のスクリーンショットは、シミュレートされたデバイス アプリケーションが IoT ハブに利用統計情報を送信したときの出力を示しています。

    ![シミュレートされたデバイスを実行する](./media/quickstart-control-device-dotnet/SimulatedDevice-1.png)

## <a name="call-the-direct-method"></a>ダイレクト メソッドを呼び出す

バックエンド アプリケーションは、IoT ハブ上のサービス側エンドポイントに接続します。 アプリケーションは、IoT ハブを通してデバイスへのダイレクト メソッド呼び出しを行った後、受信確認をリッスンします。 通常、IoT Hub のバックエンド アプリケーションはクラウドで実行されます。

1. 別のローカル ターミナル ウィンドウで、サンプルの C# プロジェクトのルート フォルダーに移動します。 その後、**iot-hub\Quickstarts\back-end-application** フォルダーに移動します。

2. 適当なテキスト エディターで **BackEndApplication.cs** ファイルを開きます。

    `s_connectionString` 変数の値を、前にメモしたサービス接続文字列に置き換えます。 変更を **BackEndApplication.cs** ファイルに保存します。

3. ローカル ターミナル ウィンドウで次のコマンドを実行して、バックエンド アプリケーションに必要なライブラリをインストールします。

    ```cmd/sh
    dotnet restore
    ```

4. ローカル ターミナル ウィンドウで次のコマンドを実行し、バックエンド アプリケーションをビルドして実行します。

    ```cmd/sh
    dotnet run
    ```

    次のスクリーンショットは、アプリケーションがデバイスに対してダイレクト メソッド呼び出しを行い、受信確認を受け取ったときの出力を示します。

    ![バックエンド アプリケーションを実行する](./media/quickstart-control-device-dotnet/BackEndApplication.png)

    バックエンド アプリケーションを実行した後、シミュレートされたデバイスを実行しているコンソール ウィンドウにメッセージが表示され、メッセージの送信速度が変わります。

    ![シミュレートされたクライアントでの変更](./media/quickstart-control-device-dotnet/SimulatedDevice-2.png)

## <a name="clean-up-resources"></a>リソースのクリーンアップ

[!INCLUDE [iot-hub-quickstarts-clean-up-resources](../../includes/iot-hub-quickstarts-clean-up-resources.md)]

## <a name="next-steps"></a>次の手順

このクイック スタートでは、バックエンド アプリケーションからデバイス上のダイレクト メソッドを呼び出し、シミュレートされたデバイス アプリケーションでダイレクト メソッド呼び出しに応答しました。

デバイスからクラウドへのメッセージをクラウド内の異なる宛先にルーティングする方法を学習するには、次のチュートリアルに進んでください。

> [!div class="nextstepaction"]
> [チュートリアル: 処理のために利用統計情報を異なるエンドポイントにルーティングする](tutorial-routing.md)
