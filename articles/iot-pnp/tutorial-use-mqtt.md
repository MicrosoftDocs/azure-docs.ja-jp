---
title: チュートリアル - MQTT を使用して Azure IoT プラグ アンド プレイ デバイス クライアントを作成する | Microsoft Docs
description: チュートリアル - MQTT プロトコルを直接使用して、Azure IoT Device SDK を使用せずに、IoT プラグ アンド プレイ デバイス クライアントを作成します
author: ericmitt
ms.author: ericmitt
ms.date: 05/13/2020
ms.topic: tutorial
ms.service: iot-pnp
services: iot-pnp
ms.openlocfilehash: e56142dfc68767945cd0d08c87f14e19551a156e
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/03/2021
ms.locfileid: "101741465"
---
# <a name="tutorial---use-mqtt-to-develop-an-iot-plug-and-play-device-client"></a>チュートリアル - MQTT を使用して IoT プラグ アンド プレイ デバイス クライアントを開発する

可能であれば、いずれかの Azure IoT Device SDK を使用して、IoT プラグ アンド プレイ デバイス クライアントを構築する必要があります。 ただし、メモリに制約のあるデバイスの使用時などのシナリオでは、MQTT ライブラリを使用して IoT ハブと通信することが必要になる場合があります。

このチュートリアルのサンプルでは、[Eclipse Mosquitto](http://mosquitto.org/) MQTT ライブラリと Visual Studio を使用します。 このチュートリアルの手順では、開発用コンピューターで Windows を使用していることを前提としています。

このチュートリアルでは、以下の内容を学習します。

> [!div class="checklist"]
> * Eclipse Mosquitto ライブラリをダウンロードしてビルドする。
> * デバイスが IoT プラグ アンド プレイ デバイスになるように、C ベースの MQTT サンプル コードを変更する。
> * IoT プラグ アンド プレイ デバイスで使用される MQTT トピックを特定する。

## <a name="prerequisites"></a>前提条件

[!INCLUDE [iot-pnp-prerequisites](../../includes/iot-pnp-prerequisites.md)]

Windows でこのチュートリアルを完了するには、ご利用のローカル Windows 環境に次のソフトウェアをインストールします。

* [Visual Studio (Community、Professional、または Enterprise)](https://visualstudio.microsoft.com/downloads/)。Visual Studio を [インストール](/cpp/build/vscpp-step-0-installation?preserve-view=true&view=vs-2019)するときに、 **[C++ によるデスクトップ開発]** ワークロードを必ず含めてください
* [Git](https://git-scm.com/download/)
* [CMake](https://cmake.org/download/)

*Azure IoT エクスプローラー* ツールを使用して、新しいデバイスを IoT ハブに追加します。 「[IoT プラグ アンド プレイのクイックスタートとチュートリアル用の環境の設定](set-up-environment.md)」を完了している場合、IoT ハブと Azure IoT エクスプローラー ツールは構成済みです。

1. **Azure IoT エクスプローラー** ツールを起動します。
1. **[IoT Hub]** ページで、 **[View devices in this hub]\(このハブのデバイスを表示\)** を選択します。
1. **[デバイス]** ページで **[+ 新規]** を選択します。
1. 自動生成された対称キーを使用する *my-mqtt-device* というデバイスを作成します。
1. **[デバイス ID]** ページで、 **[Connection string with SAS token]\(SAS トークンを含む接続文字列\)** を展開します。
1. **対称キー** として使用する **主キー** を選択し、有効期限を 60 分に設定して、 **[生成]** を選択します。
1. 生成された **SAS トークン接続文字列** をコピーします。この値は、このチュートリアルで後ほど使用します。

## <a name="clone-sample-repo"></a>サンプル リポジトリを複製する

次のコマンドを使用して、サンプル リポジトリをローカル コンピューター上の適切な場所に複製します。

```cmd
git clone https://github.com/Azure-Samples/IoTMQTTSample.git
```

## <a name="install-mqtt-library"></a>MQTT ライブラリをインストールする

`vcpkg` ツールを使用して、Eclipse Mosquitto ライブラリをダウンロードしてビルドします。

次のコマンドを使用して、**Vcpkg** リポジトリをローカル コンピューター上の適切な場所に複製します。

```cmd
git clone https://github.com/Microsoft/vcpkg.git
```

次のコマンドを使用して、`vcpkg` 環境を準備します。 `vcpkg integrate install` を実行するときは、管理者特権でのコマンド プロンプトが必要になります。

```cmd
cd vcpkg
.\bootstrap-vcpkg.bat
.\vcpkg integrate install
```

次のコマンドを使用して、Eclipse Mosquitto ライブラリをダウンロードしてビルドします。

```cmd
.\vcpkg install mosquitto:x64-windows
```

## <a name="migrate-the-sample-to-iot-plug-and-play"></a>サンプルを IoT プラグ アンド プレイに移行する

### <a name="review-the-non-iot-plug-and-play-sample-code"></a>IoT プラグ アンド プレイではないサンプル コードを確認する

ビルドして実行する前に、IoT ハブとデバイスの詳細でコードを更新します。

Visual Studio でサンプル コードを表示するには、*IoTMQTTSample\src\Windows* フォルダーにある *MQTTWin32.sln* ソリューション ファイルを開きます。

**ソリューション エクスプローラー **で、** TelemetryMQTTWin32** プロジェクトを右クリックし、 **[スタートアップ プロジェクトに設定]** を選択します。

**TelemetryMQTTWin32** プロジェクトで、**MQTT_Mosquitto.cpp** ソース ファイルを開きます。 前に記録したデバイスの詳細を使用して、接続情報の定義を更新します。 トークン文字列プレースホルダーを次のように置き換えます。

* `IOTHUBNAME` を IoT ハブの名前に置き換えます。
* `DEVICEID` 識別子を `my-mqtt-device` に置き換えます。
* `PWD` 識別子を、デバイス用に生成した SAS トークン接続文字列の適切な部分に置き換えます。 接続文字列の `SharedAccessSignature sr=` から最後までの部分を使用します。

Azure IoT エクスプローラーを起動し、テレメトリのリッスンを開始して、コードが正常に動作していることを確認します。

アプリケーションを実行し (Ctrl + F5)、数秒すると、次のような出力が表示されます。

:::image type="content" source="media/tutorial-use-mqtt/mqtt-sample-output.png" alt-text="MQTT サンプル アプリケーションからの出力":::

Azure IoT エクスプローラーでは、デバイスが IoT プラグ アンド プレイ デバイスではないことがわかります。

:::image type="content" source="media/tutorial-use-mqtt/non-pnp-iot-explorer.png" alt-text="Azure IoT エクスプローラーでの IoT プラグ アンド プレイではないデバイス":::

### <a name="make-the-device-an-iot-plug-and-play-device"></a>デバイスを IoT プラグ アンド プレイ デバイスにする

IoT プラグ アンド プレイ デバイスでは、一連の簡単な規則に従う必要があります。 デバイスは、接続時にモデル ID を送信すると、IoT プラグ アンド プレイ デバイスになります。

このサンプルでは、モデル ID を MQTT 接続パケットに追加します。 `USERNAME` のクエリ文字列パラメーターとしてモデル ID を渡し、`api-version` を `2020-09-30` に変更します。

```c
// computed Host Username and Topic
//#define USERNAME IOTHUBNAME ".azure-devices.net/" DEVICEID "/?api-version=2018-06-30"
#define USERNAME IOTHUBNAME ".azure-devices.net/" DEVICEID "/?api-version=2020-09-30&model-id=dtmi:com:example:Thermostat;1"
#define PORT 8883
#define HOST IOTHUBNAME //".azure-devices.net"
#define TOPIC "devices/" DEVICEID "/messages/events/"
```

サンプルをリビルドして実行します。

デバイス ツインにモデル ID が含まれるようになりました。

:::image type="content" source="media/tutorial-use-mqtt/model-id-iot-explorer.png" alt-text="Azure IoT エクスプローラーでモデル ID を表示する":::

IoT プラグ アンド プレイ コンポーネント内を移動できるようになりました。

:::image type="content" source="media/tutorial-use-mqtt/components-iot-explorer.png" alt-text="Azure IoT エクスプローラーでのコンポーネントの表示":::

これで、デバイス コードを変更して、モデルで定義されているテレメトリ、プロパティ、コマンドを実装できます。 Mosquitto ライブラリを使用したサーモスタット デバイスの実装例については、GitHub の「[Windows の IoT SDK を使用せずに Azure IoTHub で MQTT PnP を使用する](https://github.com/Azure-Samples/IoTMQTTSample/tree/master/src/Windows/PnPMQTTWin32)」を参照してください。

> [!NOTE]
>クライアントでは、`IoTHubRootCA_Baltimore.pem` ルート証明書ファイルを使用して、接続先の IoT ハブの ID を検証します。

### <a name="mqtt-topics"></a>MQTT のトピック

次の定義は、デバイスで IoT ハブに情報を送信するために使用される MQTT のトピックに対するものです。 詳しくは、「[MQTT プロトコルを使用した IoT Hub との通信](../iot-hub/iot-hub-mqtt-support.md)」をご覧ください。

* `DEVICE_CAPABILITIES_MESSAGE` では、実装されているインターフェイスをレポートするためにデバイスによって使用されるトピックが定義されています。
* `DEVICETWIN_PATCH_MESSAGE` では、IoT ハブに対するプロパティの更新を報告するためにデバイスによって使用されるトピックが定義されています。
* `DEVICE_TELEMETRY_MESSAGE` では、IoT ハブにテレメトリを送信対するためにデバイスによって使用されるトピックが定義されています。

MQTT の詳細については、「[Azure IoT の MQTT サンプル](https://github.com/Azure-Samples/IoTMQTTSample/)」GitHub リポジトリを参照してください。

## <a name="clean-up-resources"></a>リソースをクリーンアップする

[!INCLUDE [iot-pnp-clean-resources](../../includes/iot-pnp-clean-resources.md)]

## <a name="next-steps"></a>次のステップ

このチュートリアルでは、IoT プラグ アンド プレイの規則に従うように MQTT デバイス クライアントを変更する方法を説明しました。 IoT Hub による MQTT プロトコルのサポートの詳細については、以下を参照してください。

> [!div class="nextstepaction"]
> [MQTT プロトコルを使用した IoT Hub との通信](../iot-hub/iot-hub-mqtt-support.md)