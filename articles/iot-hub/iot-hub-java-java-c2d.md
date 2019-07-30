---
title: Azure IoT Hub (Java) を使用したクラウドからデバイスへのメッセージ | Microsoft Docs
description: Azure IoT SDK for Java を使用して、クラウドからデバイスへのメッセージを Azure IoT Hub からデバイスへ送信する方法。 クラウドからデバイスへのメッセージを受信するためにシミュレートされたデバイス アプリを変更し、クラウドからデバイスへのメッセージを送信するためにバックエンド アプリを変更します。
author: wesmc7777
manager: philmea
ms.author: wesmc
ms.service: iot-hub
services: iot-hub
ms.devlang: java
ms.topic: conceptual
ms.date: 06/28/2017
ms.openlocfilehash: a74058c509c8031d0ac53c0d9cdf91e6f933ea1f
ms.sourcegitcommit: 6a42dd4b746f3e6de69f7ad0107cc7ad654e39ae
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/07/2019
ms.locfileid: "67620142"
---
# <a name="send-cloud-to-device-messages-with-iot-hub-java"></a>IoT Hub を使用したクラウドからデバイスへのメッセージの送信 (Java)

[!INCLUDE [iot-hub-selector-c2d](../../includes/iot-hub-selector-c2d.md)]

Azure IoT Hub は、何百万ものデバイスとソリューション バックエンドの間に信頼性のある保護された双方向通信を確立するのに役立つ、フル マネージドのサービスです。 「[Send telemetry from a device to an IoT hub](quickstart-send-telemetry-java.md)」(デバイスから IoT ハブへのテレメトリの送信) のクイックスタートには、IoT ハブの作成方法、IoT ハブでデバイス ID をプロビジョニングする方法、および device-to-cloud メッセージを送信するシミュレートされたデバイス アプリをコード化する方法が示されています。

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-whole.md)]

このチュートリアルは、[デバイスから IoT ハブへのテレメトリ送信](quickstart-send-telemetry-java.md)に関する記事に基づいて作成されています。 次の操作の実行方法について説明しています。

* ソリューション バックエンドから IoT Hub を介して単一のデバイスにクラウドからデバイスへのメッセージを送信する。

* クラウドからデバイスへのメッセージをデバイスで受信する。

* IoT Hub からデバイスに送信されたメッセージの配信確認 ("*フィードバック*") を、ソリューション バックエンドから要求する。

クラウドからデバイスへのメッセージの詳細については、[IoT Hub 開発者ガイド](iot-hub-devguide-messaging.md)を参照してください。

このチュートリアルの最後に、次の 2 つの Java コンソール アプリを実行します。

* **simulated-device**: [デバイスから IoT ハブへのテレメトリの送信](quickstart-send-telemetry-java.md)に関するページで作成されたアプリの修正版であり、IoT ハブに接続し、cloud-to-device メッセージを受信します。

* **send-c2d-messages**。これは、C2D メッセージを IoT Hub を介してシミュレートされたデバイス アプリに送信し、その配信確認を受信します。

> [!NOTE]
> IoT Hub には、Azure IoT device SDK を介した多数のデバイス プラットフォームや言語 (C、Java、Javascript など) に対する SDK サポートがあります。 このチュートリアルのコード (一般的には Azure IoT Hub) にデバイスを接続するための詳しい手順については、 [Azure IoT デベロッパー センター](https://azure.microsoft.com/develop/iot)のページを参照してください。

このチュートリアルを完了するには、以下が必要です。

* [デバイスから IoT ハブへのテレメトリ送信](quickstart-send-telemetry-java.md)のクイックスタートまたは [IoT Hub を使用したメッセージ ルーティングの構成](tutorial-routing.md)のチュートリアルの完全に動作するバージョン。

* 最新の [Java SE Development Kit 8](https://aka.ms/azure-jdks)

* [Maven 3](https://maven.apache.org/install.html)

* アクティブな Azure アカウントアカウントがない場合、Azure 試用版にサインアップして、最大 10 件の無料 Mobile Apps を入手できます。 アカウントがない場合は、 [無料アカウント](https://azure.microsoft.com/pricing/free-trial/) を数分で作成することができます。

## <a name="receive-messages-in-the-simulated-device-app"></a>シミュレートされたデバイス アプリでメッセージを受信する

このセクションでは、[デバイスから IoT Hub へのテレメトリ送信](quickstart-send-telemetry-java.md)に関するページで作成したシミュレート デバイス アプリを変更して、cloud-to-device メッセージを IoT ハブから受信するようにします。

1. テキスト エディターを使用し、simulated-device\src\main\java\com\mycompany\app\App.java ファイルを開きます。

2. 次の **MessageCallback** クラスを、**App** クラス内のネストされたクラスとして追加します。 デバイスが IoT Hub からメッセージを受信すると、 **execute** メソッドが呼び出されます。 この例では、デバイスはメッセージが完了したことを常に IoT ハブに通知します。

    ```java
    private static class AppMessageCallback implements MessageCallback {
      public IotHubMessageResult execute(Message msg, Object context) {
        System.out.println("Received message from hub: "
          + new String(msg.getBytes(), Message.DEFAULT_IOTHUB_MESSAGE_CHARSET));

        return IotHubMessageResult.COMPLETE;
      }
    }
    ```

3. **main** メソッドを次のとおり変更して、**AppMessageCallback** インスタンスを作成し、**setMessageCallback** メソッドを呼び出してから、クライアントを開くようにします。

    ```java
    client = new DeviceClient(connString, protocol);

    MessageCallback callback = new AppMessageCallback();
    client.setMessageCallback(callback, null);
    client.open();
    ```

    > [!NOTE]
    > トランスポートとして AMQP の代わりに HTTPS を使用した場合、**DeviceClient** インスタンスが IoT Hub からのメッセージをチェックする頻度は低くなります (25 分に 1 回未満)。 MQTT、AMQP、および HTTPS のサポートの相違点と、IoT Hub スロットルの詳細については、[IoT Hub 開発者ガイドのメッセージングのセクション](iot-hub-devguide-messaging.md)を参照してください。

4. Maven を使用して **simulated-device** アプリをビルドするには、コマンド プロンプトで simulated-device フォルダーに移動し、次のコマンドを実行します。

    ```cmd/sh
    mvn clean package -DskipTests
    ```

## <a name="send-a-cloud-to-device-message"></a>C2D メッセージを送信する

このセクションでは、クラウドからデバイスへのメッセージを、シミュレーション済みデバイス アプリに送信する Java コンソール アプリを作成します。 [デバイスから IoT Hub へのテレメトリ送信](quickstart-send-telemetry-java.md)のクイックスタートで追加したデバイスのデバイス ID が必要です。 また、ハブの IoT Hub 接続文字列も必要です ([Azure Portal](https://portal.azure.com) で確認できます)。

1. コマンド プロンプトで次のコマンドを使用して、**send-c2d-messages** という Maven プロジェクトを作成します。 このコマンドが 1 つの長いコマンドであることに注意してください。

    ```cmd/sh
    mvn archetype:generate -DgroupId=com.mycompany.app -DartifactId=send-c2d-messages -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false
    ```

2. コマンド プロンプトで、新しい send-c2d-messages フォルダーに移動します。

3. テキスト エディターを使用して、send-c2d-messages フォルダー内の pom.xml ファイルを開き、次の依存関係を **dependencies** ノードに追加します。 依存関係を追加することにより、アプリケーションの **iothub-java-service-client** パッケージを使用して、IoT Hub サービスと通信できます。

    ```xml
    <dependency>
      <groupId>com.microsoft.azure.sdk.iot</groupId>
      <artifactId>iot-service-client</artifactId>
      <version>1.7.23</version>
    </dependency>
    ```

    > [!NOTE]
    > [Maven 検索](https://search.maven.org/#search%7Cga%7C1%7Ca%3A%22iot-service-client%22%20g%3A%22com.microsoft.azure.sdk.iot%22)を使用して、**iot-service-client** の最新バージョンを確認できます。

4. pom.xml ファイルを保存して閉じます。

5. テキスト エディターを使用し、send-c2d-messages\src\main\java\com\mycompany\app\App.java ファイルを開きます。

6. ファイルに次の **import** ステートメントを追加します。

    ```java
    import com.microsoft.azure.sdk.iot.service.*;
    import java.io.IOException;
    import java.net.URISyntaxException;
    ```

7. 次のクラス レベルの変数を **App** クラスに追加し、 **{yourhubconnectionstring}** と **{yourdeviceid}** を先にメモした値に置き換えます。

    ```java
    private static final String connectionString = "{yourhubconnectionstring}";
    private static final String deviceId = "{yourdeviceid}";
    private static final IotHubServiceClientProtocol protocol =    
        IotHubServiceClientProtocol.AMQPS;
    ```

8. **main** メソッドを次のコードで置き換えます。 このコードは、IoT Hub に接続し、デバイスにメッセージを送信して、デバイスがメッセージを受信して処理したことを示す受信確認を受け取るまで待機します。

    ```java
    public static void main(String[] args) throws IOException,
        URISyntaxException, Exception {
      ServiceClient serviceClient = ServiceClient.createFromConnectionString(
        connectionString, protocol);

      if (serviceClient != null) {
        serviceClient.open();
        FeedbackReceiver feedbackReceiver = serviceClient
          .getFeedbackReceiver();
        if (feedbackReceiver != null) feedbackReceiver.open();

        Message messageToSend = new Message("Cloud to device message.");
        messageToSend.setDeliveryAcknowledgement(DeliveryAcknowledgement.Full);

        serviceClient.send(deviceId, messageToSend);
        System.out.println("Message sent to device");

        FeedbackBatch feedbackBatch = feedbackReceiver.receive(10000);
        if (feedbackBatch != null) {
          System.out.println("Message feedback received, feedback time: "
            + feedbackBatch.getEnqueuedTimeUtc().toString());
        }

        if (feedbackReceiver != null) feedbackReceiver.close();
        serviceClient.close();
      }
    }
    ```

    > [!NOTE]
    > わかりやすくするために、このチュートリアルでは再試行ポリシーは実装しません。 運用環境のコードでは、「[一時的な障害の処理](/azure/architecture/best-practices/transient-faults)」の記事で推奨されているように、再試行ポリシー (指数関数的バックオフなど) を実装することをお勧めします。

9. Maven を使用して **simulated-device** アプリをビルドするには、コマンド プロンプトで simulated-device フォルダーに移動し、次のコマンドを実行します。

    ```cmd/sh
    mvn clean package -DskipTests
    ```

## <a name="run-the-applications"></a>アプリケーションの実行

これで、アプリケーションを実行する準備が整いました。

1. simulated-device フォルダーからコマンド プロンプトで次のコマンドを実行し、IoT Hub へのテレメトリの送信を開始して、IoT Hub から送信される (クラウドからデバイスへの) メッセージをリッスンします。

    ```cmd/sh
    mvn exec:java -Dexec.mainClass="com.mycompany.app.App"
    ```

    ![シミュレーション済みデバイス アプリを実行する](./media/iot-hub-java-java-c2d/receivec2d.png)

2. send-c2d-messages フォルダーからコマンド プロンプトで次のコマンドを実行し、クラウドからデバイスへのメッセージを送信して、フィードバックの配信確認を待機します。

    ```cmd/sh
    mvn exec:java -Dexec.mainClass="com.mycompany.app.App"
    ```

    ![クラウドからデバイスへのメッセージを送信するコマンドを実行する](media/iot-hub-java-java-c2d/sendc2d.png)

## <a name="next-steps"></a>次の手順

このチュートリアルでは、クラウドからデバイスへのメッセージを送受信する方法を学習しました。

IoT Hub を使用する完全なエンド ツー エンド ソリューションの例については、 [Azure IoT ソリューション アクセラレータ](https://azure.microsoft.com/documentation/suites/iot-suite/)に関するドキュメントをご覧ください。

IoT Hub を使用したソリューションの開発に関する詳細については、[IoT Hub 開発者ガイド](iot-hub-devguide.md)をご覧ください。
