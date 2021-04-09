---
author: dominicbetts
ms.author: dobett
ms.service: iot-pnp
ms.topic: include
ms.date: 11/20/2020
ms.openlocfilehash: 8d3f35a733a0f78fabc33df857d911ba3cd222f5
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "102244892"
---
IoT プラグ アンド プレイを使用すると、基盤となるデバイスの実装に関する知識がなくてもデバイスの機能とやり取りできるので、IoT が簡略化されます。 このクイックスタートでは、Java を使用して、ご利用のソリューションに接続されている IoT プラグ アンド プレイ デバイスに接続して制御する方法について説明します。

## <a name="prerequisites"></a>前提条件

[!INCLUDE [iot-pnp-prerequisites](iot-pnp-prerequisites.md)]

Windows でこのクイックスタートを完了するには、ご利用のローカル Windows 環境に次のソフトウェアをインストールします。

* Java SE Development Kit 8。 「[Azure および Azure Stack の Java 長期サポート](/java/azure/jdk/)」の「**長期サポート**」で「**Java 8**」を選択します。
* [Apache Maven 3](https://maven.apache.org/download.cgi)。

### <a name="clone-the-sdk-repository-with-the-sample-code"></a>サンプル コードを使用して SDK リポジトリをクローンする

「[クイックスタート: Windows 上で実行されている IoT プラグ アンド プレイのサンプル デバイス アプリケーションを IoT Hub に接続する (Java)](../articles/iot-pnp/quickstart-connect-device.md)」を完了している場合は、リポジトリを既にクローンしています。

任意のディレクトリでコマンド プロンプトを開きます。 次のコマンドを実行して、[Java 用 Microsoft Azure IoT SDK](https://github.com/Azure/azure-iot-sdk-java) GitHub リポジトリをこの場所にクローンします。

```cmd
git clone https://github.com/Azure/azure-iot-sdk-java.git
```

## <a name="build-and-run-the-sample-device"></a>サンプル デバイスのビルドと実行

[!INCLUDE [iot-pnp-environment](iot-pnp-environment.md)]

サンプル構成の詳細については、[サンプルの Readme](https://github.com/Azure/azure-iot-sdk-java/blob/master/device/iot-device-samples/readme.md) を参照してください。

このクイックスタートでは、IoT プラグ アンド プレイ デバイスとして、Java で記述されたサンプルのサーモスタット デバイスを使用します。 サンプル デバイスを実行するには、次のようにします。

1. ターミナル ウィンドウを開いて、GitHub からクローンした Microsoft Azure IoT SDK for Java リポジトリが格納されているローカル フォルダーに移動します。

1. このターミナル ウィンドウは、**デバイス** ターミナルとして使用されます。 クローンしたリポジトリのルート フォルダーに移動します。 次のコマンドを実行して、すべての依存関係をインストールします。

1. 次のコマンドを実行して、サンプル デバイス アプリケーションをビルドします。

    ```cmd
    mvn install -T 2C -DskipTests
    ```

1. サンプル デバイス アプリケーションを実行するには、*device\iot-device-samples\pnp-device-sample\thermostat-device-sample* フォルダーに移動し、次のコマンドを実行します。

    ```cmd
    mvn exec:java -Dexec.mainClass="samples.com.microsoft.azure.sdk.iot.device.Thermostat"
    ```

これで、デバイスはコマンドとプロパティの更新情報を受信する準備ができ、ハブへのテレメトリ データの送信が開始されました。 次の手順を完了するまで、サンプル デバイスを実行したままにしておきます。

## <a name="run-the-sample-solution"></a>サンプル ソリューションを実行する

「[IoT プラグ アンド プレイのクイックスタートとチュートリアル用の環境の設定](../articles/iot-pnp/set-up-environment.md)」では、IoT ハブとデバイスに接続するようにサンプルを構成するための 2 つの環境変数を作成しました。

* **IOTHUB_CONNECTION_STRING**: 先ほどメモした IoT ハブ接続文字列。
* **IOTHUB_DEVICE_ID**: `"my-pnp-device"`。

このクイックスタートでは、Java で記述されたサンプルの IoT ソリューションを使用して、先ほど設定したサンプル デバイスとやり取りします。

> [!NOTE]
> このサンプルでは、**IoT Hub サービス クライアント** の **com.microsoft.azure.sdk.iot.service** 名前空間を使用します。 デジタル ツイン API を含む API の詳細については、[サービス開発者ガイド](../articles/iot-pnp/concepts-developer-guide-service.md)を参照してください。

1. **サービス** ターミナルとして使用する別のターミナル ウィンドウを開きます。

1. クローンした Java SDK リポジトリで、*service\iot-service-samples\pnp-service-sample\thermostat-service-sample* フォルダーに移動します。

1. サンプル サービス アプリケーションを実行するには、次のコマンドを実行します。

    ```cmd
    mvm exec:java -Dexec.mainClass="samples.com.microsoft.azure.sdk.iot.service.Thermostat"
    ```

### <a name="get-device-twin"></a>デバイス ツインを取得する

次のコード スニペットは、サービス内でデバイス ツインを取得する方法を示しています。

```java
 // Get the twin and retrieve model Id set by Device client.
DeviceTwinDevice twin = new DeviceTwinDevice(deviceId);
twinClient.getTwin(twin);
System.out.println("Model Id of this Twin is: " + twin.getModelId());
```

### <a name="update-a-device-twin"></a>デバイス ツインを更新する

次のコード スニペットは、"*パッチ*" を使用して、デバイス ツインを介してプロパティを更新する方法を示しています。

```java
String propertyName = "targetTemperature";
double propertyValue = 60.2;
twin.setDesiredProperties(Collections.singleton(new Pair(propertyName, propertyValue)));
twinClient.updateTwin(twin);
```

デバイス出力には、デバイスがこのプロパティの更新にどのように応答するかが示されます。

### <a name="invoke-a-command"></a>コマンドを呼び出す

次のコード スニペットは、デバイスでコマンドを呼び出す方法を示しています。

```java
// The method to invoke for a device without components should be "methodName" as defined in the DTDL.
String methodToInvoke = "getMaxMinReport";
System.out.println("Invoking method: " + methodToInvoke);

Long responseTimeout = TimeUnit.SECONDS.toSeconds(200);
Long connectTimeout = TimeUnit.SECONDS.toSeconds(5);

// Invoke the command.
String commandInput = ZonedDateTime.now(ZoneOffset.UTC).minusMinutes(5).format(DateTimeFormatter.ISO_DATE_TIME);
MethodResult result = methodClient.invoke(deviceId, methodToInvoke, responseTimeout, connectTimeout, commandInput);
if(result == null)
{
    throw new IOException("Method result is null");
}

System.out.println("Method result status is: " + result.getStatus());
```

デバイス出力には、デバイスがこのコマンドにどのように応答するかが示されます。
