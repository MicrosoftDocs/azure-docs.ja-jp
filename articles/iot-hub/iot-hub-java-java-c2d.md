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
ms.custom:
- amqp
- mqtt
- devx-track-java
ms.openlocfilehash: 5ae1850add94d83278b0fe1905dfa6e53c71fc8e
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102217892"
---
# <a name="send-cloud-to-device-messages-with-iot-hub-java"></a>IoT Hub を使用したクラウドからデバイスへのメッセージの送信 (Java)

[!INCLUDE [iot-hub-selector-c2d](../../includes/iot-hub-selector-c2d.md)]

Azure IoT Hub は、何百万ものデバイスとソリューション バックエンドの間に信頼性のある保護された双方向通信を確立するのに役立つ、フル マネージドのサービスです。 [デバイスから IoT ハブへのテレメトリの送信](quickstart-send-telemetry-java.md)に関するクイックスタートには、IoT ハブの作成方法、IoT ハブでデバイス ID をプロビジョニングする方法、および device-to-cloud メッセージを送信するシミュレートされたデバイス アプリをコード化する方法が示されています。

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-whole.md)]

このチュートリアルは、[デバイスから IoT ハブへのテレメトリ送信](quickstart-send-telemetry-java.md)に関する記事に基づいて作成されています。 次の操作の実行方法について説明しています。

* ソリューション バックエンドから IoT Hub を介して単一のデバイスにクラウドからデバイスへのメッセージを送信する。

* クラウドからデバイスへのメッセージをデバイスで受信する。

* ソリューション バックエンドから、IoT Hub からデバイスに送信されたメッセージの配信確認 ("*フィードバック*") を要求する。

クラウドからデバイスへのメッセージの詳細については、[IoT Hub 開発者ガイド](iot-hub-devguide-messaging.md)を参照してください。

このチュートリアルの最後に、次の 2 つの Java コンソール アプリを実行します。

* **simulated-device**: [デバイスから IoT ハブへのテレメトリの送信](quickstart-send-telemetry-java.md)に関するページで作成されたアプリの修正版であり、IoT ハブに接続し、cloud-to-device メッセージを受信します。

* **send-c2d-messages**。これは、C2D メッセージを IoT Hub を介してシミュレートされたデバイス アプリに送信し、その配信確認を受信します。

> [!NOTE]
> IoT Hub には、Azure IoT device SDK シリーズを介した多数のデバイス プラットフォームや言語 (C、Java、Python、JavaScript など) に対する SDK サポートがあります。 このチュートリアルのコード (一般的には Azure IoT Hub) にデバイスを接続するための詳しい手順については、 [Azure IoT デベロッパー センター](https://azure.microsoft.com/develop/iot)のページを参照してください。

## <a name="prerequisites"></a>前提条件

* [デバイスから IoT ハブへのテレメトリ送信](quickstart-send-telemetry-java.md)のクイックスタートまたは [IoT Hub を使用したメッセージ ルーティングの構成](tutorial-routing.md)のチュートリアルの完全に動作するバージョン。

* [Java SE Development Kit 8](/java/azure/jdk/)。 JDK 8 のダウンロードを利用するには、「**長期サポート**」の「**Java 8**」を選択します。

* [Maven 3](https://maven.apache.org/download.cgi)

* アクティブな Azure アカウントアカウントがない場合、Azure 試用版にサインアップして、最大 10 件の無料 Mobile Apps を入手できます。 アカウントがない場合は、 [無料アカウント](https://azure.microsoft.com/pricing/free-trial/) を数分で作成することができます。

* ポート 8883 がファイアウォールで開放されていることを確認してください。 この記事のデバイス サンプルでは、ポート 8883 を介して通信する MQTT プロトコルを使用しています。 このポートは、企業や教育用のネットワーク環境によってはブロックされている場合があります。 この問題の詳細と対処方法については、「[IoT Hub への接続 (MQTT)](iot-hub-mqtt-support.md#connecting-to-iot-hub)」を参照してください。

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

4. Maven を使用して **simulated-device** アプリをビルドするには、コマンド プロンプトで simulated-device フォルダーに移動し、次のコマンドを実行します。

    ```cmd/sh
    mvn clean package -DskipTests
    ```

`AppMessageCallback` クラスの `execute` メソッドは、`IotHubMessageResult.COMPLETE` を返します。 これによって、メッセージが正常に処理されたこと、およびメッセージはデバイスのキューから安全に削除できることが IoT Hub に通知されます。 デバイスは、使用しているプロトコルに関係なく、処理が正常に完了したときに、この値を返す必要があります。

MQTT ではなく AMQP と HTTPS を使用する場合、デバイスは次の処理もできます。

* メッセージを破棄します。この場合、IoT Hub は将来の使用に備えてメッセージをデバイスのキュー内に保持します。
* メッセージを拒否します。この場合、メッセージはデバイスのキューから完全に削除されます。

デバイスがメッセージを完了、破棄、または拒否することを妨げる問題が発生した場合、IoT Hub は一定のタイムアウト期間が経過した後で、メッセージの配信をキューに入れます。 このような理由から、同じメッセージを複数回受信した場合に生成される結果が毎回同じになるように、デバイス アプリ内のメッセージ処理ロジックを *べき等* にする必要があります。

cloud-to-device メッセージのライフサイクルの詳細など、IoT Hub が cloud-to-device メッセージを処理する方法の詳細については、「[IoT ハブから cloud-to-device メッセージを送信する](iot-hub-devguide-messages-c2d.md)」を参照してください。

> [!NOTE]
> トランスポートとして AMQP の代わりに HTTPS を使用した場合、**DeviceClient** インスタンスが IoT Hub からのメッセージをチェックする頻度は低くなります (最小 25 分の間隔)。 MQTT、AMQP、および HTTPS のサポートの相違点の詳細については、「[cloud-to-device 通信に関するガイダンス](iot-hub-devguide-c2d-guidance.md)」と「[通信プロトコルの選択](iot-hub-devguide-protocols.md)」を参照してください。

## <a name="get-the-iot-hub-connection-string"></a>IoT ハブ接続文字列を取得する

この記事では、[デバイスから IoT ハブへのテレメトリの送信](quickstart-send-telemetry-java.md)に関するページで作成した IoT ハブを介して cloud-to-device メッセージを送信するバックエンド サービスを作成します。 cloud-to-device メッセージを送信するサービスには、**サービス接続** のアクセス許可が必要となります。 既定では、どの IoT Hub も、このアクセス許可を付与する **service** という名前の共有アクセス ポリシーがある状態で作成されます。

[!INCLUDE [iot-hub-include-find-service-connection-string](../../includes/iot-hub-include-find-service-connection-string.md)]

## <a name="send-a-cloud-to-device-message"></a>C2D メッセージを送信する

このセクションでは、クラウドからデバイスへのメッセージを、シミュレーション済みデバイス アプリに送信する Java コンソール アプリを作成します。 [デバイスから IoT ハブへのテレメトリの送信](quickstart-send-telemetry-java.md)に関するクイックスタートで追加したデバイスのデバイス ID が必要です。 また、先ほど「[IoT ハブ接続文字列を取得する](#get-the-iot-hub-connection-string)」でコピーしておいた IoT ハブ接続文字列も必要です。

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

## <a name="next-steps"></a>次のステップ

このチュートリアルでは、クラウドからデバイスへのメッセージを送受信する方法を学習しました。

IoT Hub を使用する完全なエンド ツー エンド ソリューションの例については、 [Azure IoT ソリューション アクセラレータ](https://azure.microsoft.com/documentation/suites/iot-suite/)に関するドキュメントをご覧ください。

IoT Hub を使用したソリューションの開発に関する詳細については、[IoT Hub 開発者ガイド](iot-hub-devguide.md)をご覧ください。