---
title: "Azure IoT Hub (Java) を使用したクラウドからデバイスへのメッセージ | Microsoft Docs"
description: "Azure IoT SDK for Java を使用して、クラウドからデバイスへのメッセージを Azure IoT Hub からデバイスへ送信する方法。 クラウドからデバイスへのメッセージを受信するためにシミュレートされたデバイス アプリを変更し、クラウドからデバイスへのメッセージを送信するためにバックエンド アプリを変更します。"
services: iot-hub
documentationcenter: java
author: dominicbetts
manager: timlt
editor: 
ms.assetid: 7f785ea8-e7c2-40c5-87ef-96525e9b9e1e
ms.service: iot-hub
ms.devlang: java
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/07/2017
ms.author: dobett
translationtype: Human Translation
ms.sourcegitcommit: a087df444c5c88ee1dbcf8eb18abf883549a9024
ms.openlocfilehash: 624f5947e31f08f689ceb6c48bc572447acd64ac
ms.lasthandoff: 03/15/2017


---
# <a name="send-cloud-to-device-messages-with-iot-hub-java"></a>IoT Hub を使用したクラウドからデバイスへのメッセージの送信 (Java)
[!INCLUDE [iot-hub-selector-c2d](../../includes/iot-hub-selector-c2d.md)]

## <a name="introduction"></a>はじめに
Azure IoT Hub は、何百万ものデバイスとソリューション バックエンドの間に信頼性のある保護された双方向通信を確立するのに役立つ、完全に管理されたサービスです。 [IoT Hub の概要]チュートリアルには、IoT ハブの作成方法、IoT ハブでデバイス ID をプロビジョニングする方法、およびデバイスからクラウドへのメッセージを送信するシミュレートされたデバイス アプリをコード化する方法が示されています。

このチュートリアルは、[IoT Hub の概要]に関するページのチュートリアルに基づいて作成されており、 次の方法について説明します。

* ソリューション バックエンドから IoT Hub を介して単一のデバイスにクラウドからデバイスへのメッセージを送信する。
* クラウドからデバイスへのメッセージをデバイスで受信する。
* IoT Hub からデバイスに送信されたメッセージの配信確認 ("*フィードバック*") を、ソリューション バックエンドから要求する。

クラウドからデバイスへのメッセージの詳細については、[IoT Hub 開発者ガイド][IoT Hub developer guide - C2D]を参照してください。

このチュートリアルの最後に、次の&2; つの Java コンソール アプリを実行します。

* **simulated-device**。「[IoT Hub の概要]」で作成されたアプリの修正バージョン。これは、IoT Hub に接続し、C2D メッセージを受信します。
* **send-c2d-messages**。これは、C2D メッセージを IoT Hub を介してシミュレートされたデバイス アプリに送信し、その配信確認を受信します。

> [!NOTE]
> IoT Hub には、Azure IoT device SDK を介した多数のデバイス プラットフォームや言語 (C、Java、Javascript など) に対する SDK サポートがあります。 このチュートリアルのコード (一般的には Azure IoT Hub) にデバイスを接続するための詳しい手順については、 [Azure IoT デベロッパー センター]のページを参照してください。
> 
> 

このチュートリアルを完了するには、以下が必要です。

* Java SE 8。 <br/> 「[Prepare your development environment (開発環境を準備する)][lnk-dev-setup]」では、このチュートリアルのために Java を Windows または Linux にインストールする方法が説明されています。
* Maven 3。  <br/> 「[Prepare your development environment (開発環境を準備する)][lnk-dev-setup]」では、このチュートリアルのために Maven を Windows または Linux にインストールする方法が説明されています。
* アクティブな Azure アカウント。 (アカウントがない場合は、[無料アカウント][lnk-free-trial]を数分で作成できます)。

## <a name="receive-messages-in-the-simulated-device-app"></a>シミュレートされたデバイス アプリでメッセージを受信する
このセクションでは、[IoT Hub の概要]に関するページで作成したシミュレートされたデバイス アプリを、クラウドからデバイスへのメッセージを IoT ハブから受信するように変更します。

1. テキスト エディターを使用し、simulated-device\src\main\java\com\mycompany\app\App.java ファイルを開きます。
2. 次の **MessageCallback** クラスを、**App** クラス内のネストされたクラスとして追加します。 デバイスが IoT Hub からメッセージを受信すると、 **execute** メソッドが呼び出されます。 この例では、デバイスはメッセージが完了したことを常に IoT ハブに通知します。
   
    ```
    private static class AppMessageCallback implements MessageCallback {
      public IotHubMessageResult execute(Message msg, Object context) {
        System.out.println("Received message from hub: "
          + new String(msg.getBytes(), Message.DEFAULT_IOTHUB_MESSAGE_CHARSET));
    
        return IotHubMessageResult.COMPLETE;
      }
    }
    ```
3. **main** メソッドを次のとおり変更して、**AppMessageCallback** インスタンスを作成し、**setMessageCallback** メソッドを呼び出してから、クライアントを開くようにします。
   
    ```
    client = new DeviceClient(connString, protocol);
   
    MessageCallback callback = new AppMessageCallback();
    client.setMessageCallback(callback, null);
    client.open();
    ```
   
   > [!NOTE]
   > トランスポートとして AMQP の代わりに HTTP/1 を使用した場合、**DeviceClient** インスタンスが IoT Hub からのメッセージをチェックする頻度は低くなります (25 分に 1 回未満)。 MQTT、AMQP、および HTTP のサポートの相違点と、IoT Hub スロットルの詳細については、[IoT Hub 開発者ガイド][IoT Hub developer guide - C2D]を参照してください。
   > 
   > 

## <a name="send-a-cloud-to-device-message"></a>C2D メッセージを送信する
このセクションでは、クラウドからデバイスへのメッセージを、シミュレーション済みデバイス アプリに送信する Java コンソール アプリを作成します。 [IoT Hub の概要] のチュートリアルで追加したデバイスのデバイス ID が必要です。 また、ハブの IoT Hub 接続文字列も必要です ([Azure Portal] で確認できます)。

1. コマンド プロンプトで次のコマンドを使用して、**send-c2d-messages** という Maven プロジェクトを作成します。 このコマンドが&1; つの長いコマンドであることに注意してください。
   
    ```
    mvn archetype:generate -DgroupId=com.mycompany.app -DartifactId=send-c2d-messages -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false
    ```
2. コマンド プロンプトで、新しい send-c2d-messages フォルダーに移動します。
3. テキスト エディターを使用して、send-c2d-messages フォルダー内の pom.xml ファイルを開き、次の依存関係を **dependencies** ノードに追加します。 依存関係を追加することにより、アプリケーションの **iothub-java-service-client** パッケージを使用して、IoT Hub サービスと通信できます。
   
    ```
    <dependency>
      <groupId>com.microsoft.azure.sdk.iot</groupId>
      <artifactId>iot-service-client</artifactId>
      <version>1.2.17</version>
    </dependency>
    ```

    > [!NOTE]
    > [Maven 検索][lnk-maven-service-search]を使用して、**iot-service-client** の最新バージョンを確認できます。

4. pom.xml ファイルを保存して閉じます。
5. テキスト エディターを使用し、send-c2d-messages\src\main\java\com\mycompany\app\App.java ファイルを開きます。
6. ファイルに次の **import** ステートメントを追加します。
   
    ```
    import com.microsoft.azure.sdk.iot.service.*;
    import java.io.IOException;
    import java.net.URISyntaxException;
    ```
7. 次のクラス レベルの変数を **App** クラスに追加し、**{yourhubconnectionstring}** と **{yourdeviceid}** を先にメモした値に置き換えます。
   
    ```
    private static final String connectionString = "{yourhubconnectionstring}";
    private static final String deviceId = "{yourdeviceid}";
    private static final IotHubServiceClientProtocol protocol = IotHubServiceClientProtocol.AMQPS;
    ```
8. **main** メソッドを次のコードで置き換えます。 このコードは、IoT Hub に接続し、デバイスにメッセージを送信して、デバイスがメッセージを受信して処理したことを示す受信確認を受け取るまで待機します。
   
    ```
    public static void main(String[] args) throws IOException,
        URISyntaxException, Exception {
      ServiceClient serviceClient = ServiceClient.createFromConnectionString(
        connectionString, protocol);
   
      if (serviceClient != null) {
        serviceClient.open();
        FeedbackReceiver feedbackReceiver = serviceClient
          .getFeedbackReceiver(deviceId);
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
   > わかりやすくするために、このチュートリアルでは再試行ポリシーは実装しません。 運用環境のコードでは、MSDN の記事「 [Transient Fault Handling (一時的な障害の処理)]」で推奨されているように、再試行ポリシー (指数関数的バックオフなど) を実装することをお勧めします。
   > 
   > 

## <a name="run-the-applications"></a>アプリケーションの実行
これで、アプリケーションを実行する準備が整いました。

1. simulated-device フォルダーからコマンド プロンプトで次のコマンドを実行し、IoT Hub へのテレメトリの送信を開始して、IoT Hub から送信される (クラウドからデバイスへの) メッセージをリッスンします。
   
    ```
    mvn exec:java -Dexec.mainClass="com.mycompany.app.App" 
    ```
   
    ![シミュレーション済みデバイス アプリを実行する][img-simulated-device]
2. send-c2d-messages フォルダーからコマンド プロンプトで次のコマンドを実行し、クラウドからデバイスへのメッセージを送信して、フィードバックの配信確認を待機します。
   
    ```
    mvn exec:java -Dexec.mainClass="com.mycompany.app.App"
    ```
   
    ![クラウドからデバイスへのメッセージを送信するコマンドを実行する][img-send-command]

## <a name="next-steps"></a>次のステップ
このチュートリアルでは、クラウドからデバイスへのメッセージを送受信する方法を学習しました。 

IoT Hub を使用する完全なエンド ツー エンド ソリューションの例については、 [Azure IoT Suite]に関するドキュメントをご覧ください。

IoT Hub を使用したソリューションの開発に関する詳細については、[IoT Hub 開発者ガイド]をご覧ください。

<!-- Images -->
[img-simulated-device]: media/iot-hub-java-java-c2d/receivec2d.png
[img-send-command]:  media/iot-hub-java-java-c2d/sendc2d.png
<!-- Links -->

[IoT Hub の概要]: iot-hub-java-java-getstarted.md
[IoT Hub developer guide - C2D]: iot-hub-devguide-messaging.md
[IoT Hub 開発者ガイド]: iot-hub-devguide.md
[Azure IoT デベロッパー センター]: http://www.azure.com/develop/iot
[lnk-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[lnk-dev-setup]: https://github.com/Azure/azure-iot-sdk-java
[Transient Fault Handling (一時的な障害の処理)]: https://msdn.microsoft.com/library/hh680901(v=pandp.50).aspx
[Azure Portal]: https://portal.azure.com
[Azure IoT Suite]: https://azure.microsoft.com/documentation/suites/iot-suite/
[lnk-maven-service-search]: http://search.maven.org/#search%7Cga%7C1%7Ca%3A%22iot-service-client%22%20g%3A%22com.microsoft.azure.sdk.iot%22
