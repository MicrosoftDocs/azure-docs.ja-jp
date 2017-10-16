---
title: "Azure IoT Hub のデバイスからクラウドへのメッセージの処理 (Java) | Microsoft Docs"
description: "他のバックエンド サービスにメッセージをディスパッチするルーティング規則とカスタム エンドポイントを使用して、IoT Hub デバイスからクラウドへのメッセージを処理する方法について説明します。"
services: iot-hub
documentationcenter: java
author: dominicbetts
manager: timlt
editor: 
ms.assetid: bd9af5f9-a740-4780-a2a6-8c0e2752cf48
ms.service: iot-hub
ms.devlang: java
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/29/2017
ms.author: dobett
ms.openlocfilehash: d1aca8f39e305105d4ec9f63fbe7bee95487e294
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="process-iot-hub-device-to-cloud-messages-java"></a>IoT Hub のデバイスからクラウドへのメッセージの処理 (Java)

[!INCLUDE [iot-hub-selector-process-d2c](../../includes/iot-hub-selector-process-d2c.md)]

Azure IoT Hub は、何百万ものデバイスとソリューション バック エンド間で、セキュリティで保護された信頼性のある双方向通信を実現する、完全に管理されたサービスです。 他のチュートリアル ([IoT Hub の使用]と [IoT Hub を使用したクラウドからデバイスへのメッセージの送信][lnk-c2d]に関するチュートリアル) では、IoT Hub のデバイスからクラウドおよびクラウドからデバイスのメッセージングについて、基本的な機能の使用方法を説明しています。

このチュートリアルは、「[IoT Hub の使用]」チュートリアルに示されているコードに基づいて作成されています。このチュートリアルでは、メッセージのルーティングを使用して、デバイスからクラウドへのメッセージをスケーラブルな方法で処理する方法を示します。 このチュートリアルでは、ソリューションのバックエンドによる早急な対応を必要とするメッセージを処理する方法を示しています。 たとえば、デバイスは、CRM システムへのチケットの挿入をトリガーするアラーム メッセージを送信する場合があります。 これに対して、データポイント メッセージは、分析エンジンにフィードされるだけです。 たとえば、後の分析用に保存されるデバイスの温度テレメトリはデータポイント メッセージです。

チュートリアルの終わりとして、次の 3 つの Java コンソール アプリケーションを実行します。

* **simulated-device**: [IoT Hub の使用]に関するチュートリアルで作成したアプリを変更したものです。デバイスからクラウドへのデータポイント メッセージを 1 秒ごとに送信し、デバイスからクラウドへの対話型メッセージを 10 秒ごとに送信します。 このアプリでは、IoT Hub との通信に AMQP プロトコルを使用します。
* **read-d2c-messages**。デバイス アプリから送信されたテレメトリを表示します。
* **read-critical-queue**。IoT hub に接続された Service Bus キューから重大なメッセージをデキューします。

> [!NOTE]
> IoT Hub には、多数のデバイス プラットフォームや言語 (C、Java、JavaScript など) に対する SDK サポートがあります。 このチュートリアルのデバイスを物理デバイスに置き換える方法と、IoT Hub にデバイスを接続する方法の手順については、[Azure IoT デベロッパー センター]を参照してください。

このチュートリアルを完了するには、以下が必要です。

* [IoT Hub の使用] に関するチュートリアルで完成させたアプリ。
* 最新の [Java SE Development Kit 8](http://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html)
* [Maven 3](https://maven.apache.org/install.html)
* アクティブな Azure アカウント  (アカウントがない場合は、[無料アカウント][lnk-free-trial]を数分で作成することができます)。

[Azure Storage] と [Azure Service Bus] について、ある程度の基礎知識が必要です。

## <a name="send-interactive-messages-from-a-device-app"></a>デバイス アプリからの対話型メッセージの送信
このセクションでは、[IoT Hub の使用]に関するチュートリアルで作成したデバイス アプリを変更して、すぐに処理する必要があるメッセージをランダムに送信するようにします。

1. テキスト エディターを使用し、simulated-device\src\main\java\com\mycompany\app\App.java ファイルを開きます。 このファイルには、 **IoT Hub の概要** のチュートリアルで作成した [IoT Hub の使用] アプリのコードが含まれています。

2. **MessageSender** クラスを次のコードに置き換えます。

    ```java
    private static class MessageSender implements Runnable {

        public void run()  {
            try {
                double minTemperature = 20;
                double minHumidity = 60;
                Random rand = new Random();

                while (true) {
                    String msgStr;
                    Message msg;
                    if (new Random().nextDouble() > 0.7) {
                        msgStr = "This is a critical message.";
                        msg = new Message(msgStr);
                        msg.setProperty("level", "critical");
                    } else {
                        double currentTemperature = minTemperature + rand.nextDouble() * 15;
                        double currentHumidity = minHumidity + rand.nextDouble() * 20; 
                        TelemetryDataPoint telemetryDataPoint = new TelemetryDataPoint();
                        telemetryDataPoint.deviceId = deviceId;
                        telemetryDataPoint.temperature = currentTemperature;
                        telemetryDataPoint.humidity = currentHumidity;

                        msgStr = telemetryDataPoint.serialize();
                        msg = new Message(msgStr);
                    }
                    
                    System.out.println("Sending: " + msgStr);

                    Object lockobj = new Object();
                    EventCallback callback = new EventCallback();
                    client.sendEventAsync(msg, callback, lockobj);

                    synchronized (lockobj) {
                        lockobj.wait();
                    }
                    Thread.sleep(1000);
                }
            } catch (InterruptedException e) {
                System.out.println("Finished.");
            }
        }
    }
    ```
   
    このメソッドは、デバイスによって送信されたメッセージに `"level": "critical"` プロパティをランダムに追加して、アプリケーションのバックエンドによる早急な対応を必要とするメッセージをシミュレートします。 アプリケーションは、この情報を、メッセージの本文ではなくメッセージのプロパティに渡して、IoT Hub がそのメッセージを適切な送信先にルーティングできるようにします。
   
   > [!NOTE]
   > メッセージのプロパティを使用したメッセージのルーティングは、ここで示すホット パスの例に加え、コールド パス処理などのさまざまなシナリオで使用できます。

2. simulated-device\src\main\java\com\mycompany\app\App.java を保存して閉じます。

    > [!NOTE]
    > わかりやすくするために、このチュートリアルでは再試行ポリシーは実装しません。 運用環境のコードでは、MSDN の記事「 [Transient Fault Handling (一時的な障害の処理)]」で推奨されているように、再試行ポリシー (指数関数的バックオフなど) を実装することをお勧めします。

3. Maven を使用して **simulated-device** アプリをビルドするには、コマンド プロンプトで simulated-device フォルダーに移動し、次のコマンドを実行します。

    ```cmd/sh
    mvn clean package -DskipTests
    ```

## <a name="add-a-queue-to-your-iot-hub-and-route-messages-to-it"></a>IoT hub へのキューの追加とキューへのメッセージのルーティング

このセクションでは、Service Bus キューを作成して IoT hub に接続し、メッセージのプロパティの有無に基づいてこのキューにメッセージを送信するように IoT hub を構成します。 Service Bus キューのメッセージを処理する方法の詳細については、「[Service Bus キューの使用][lnk-sb-queues-java]」を参照してください。

1. 「[Service Bus キューの使用][lnk-sb-queues-java]」の説明に従って、Service Bus キューを作成します。 名前空間とキューの名前をメモしておきます。

2. Azure Portal で、IoT Hub を開き、**[エンドポイント]** をクリックします。

    ![IoT Hub 内のエンドポイント][30]

3. **[エンドポイント]** ブレードで、上部にある **[追加]** をクリックして、IoT Hub にキューを追加します。 エンドポイントに「**CriticalQueue**」という名前を付け、ドロップダウン リストを使用して、**Service Bus キュー**、キューを配置する Service Bus 名前空間、およびキューの名前を選択します。 完了したら、下部にある **[保存]** をクリックします。

    ![エンドポイントの追加][31]

4. 次に、IoT Hub で **[ルート]** をクリックします。 ブレードの上部にある **[追加]** をクリックして、先ほど追加したキューにメッセージをルーティングするルーティング規則を作成します。 データのソースとして **[DeviceTelemetry]** を選択します。 条件として「`level="critical"`」を入力し、カスタム エンドポイントとして先ほど追加したキューをルーティング規則エンドポイントとして選択します。 完了したら、下部にある **[保存]** をクリックします。

    ![ルートの追加][32]

    フォールバック ルートが **[オン]** に設定されていることを確認します。 この設定は IoT Hub の既定の構成です。

    ![フォールバック ルート][33]

## <a name="optional-read-from-the-queue-endpoint"></a>(省略可能) キュー エンドポイントからの読み取り

必要に応じて、[キューの使用方法][lnk-sb-queues-java]に関する記事の手順に従って、キュー エンドポイントからメッセージを読み取ることができます。 アプリに **read-critical-queue**という名前を付けます。

## <a name="run-the-applications"></a>アプリケーションの実行

これで、3 つのアプリケーションを実行する準備が整いました。

1. **read-d2c-messages** アプリケーションを実行するには、コマンド プロンプトまたはシェルで read-d2c folder フォルダーに移動し、次のコマンドを実行します。

   ```cmd/sh
   mvn exec:java -Dexec.mainClass="com.mycompany.app.App"
   ```

   ![read-d2c-messages の実行][readd2c]

2. **read-critical-queue** アプリケーションを実行するには、コマンド プロンプトまたはシェルで read-critical-queue フォルダーに移動し、次のコマンドを実行します。

   ```cmd/sh
   mvn exec:java -Dexec.mainClass="com.mycompany.app.App"
   ```
   
   ![read-critical-messages の実行][readqueue]

3. コマンド プロンプトまたはシェルで simulated-device フォルダーに移動して次のコマンドを実行し、**simulated-device** アプリを実行します。

   ```cmd/sh
   mvn exec:java -Dexec.mainClass="com.mycompany.app.App"
   ```
   
   ![simulated-device の実行][simulateddevice]

## <a name="next-steps"></a>次のステップ

このチュートリアルでは、IoT Hub のメッセージ ルーティング機能を使用して、デバイスからクラウドへのメッセージを確実にディスパッチする方法について説明しました。

[IoT Hub でクラウドからデバイスへのメッセージを送信する方法][lnk-c2d]に関するページでは、ソリューション バックエンドからデバイスにメッセージを送信する方法を説明しています。

IoT Hub を使用する完全なエンド ツー エンド ソリューションの例については、[Azure IoT Suite][lnk-suite] に関するドキュメントを参照してください。

IoT Hub を使用したソリューションの開発に関する詳細については、[IoT Hub 開発者ガイド]をご覧ください。

IoT Hub でのメッセージのルーティングの詳細については、[IoT Hub でのメッセージの送受信][lnk-devguide-messaging]に関するページを参照してください。

<!-- Images. -->
<!-- TODO: UPDATE PICTURES -->
[simulateddevice]: ./media/iot-hub-java-java-process-d2c/runsimulateddevice.png
[readd2c]: ./media/iot-hub-java-java-process-d2c/runprocessinteractive.png
[readqueue]: ./media/iot-hub-java-java-process-d2c/runprocessd2c.png

[30]: ./media/iot-hub-java-java-process-d2c/click-endpoints.png
[31]: ./media/iot-hub-java-java-process-d2c/endpoint-creation.png
[32]: ./media/iot-hub-java-java-process-d2c/route-creation.png
[33]: ./media/iot-hub-java-java-process-d2c/fallback-route.png

<!-- Links -->

[lnk-sb-queues-java]: ../service-bus-messaging/service-bus-java-how-to-use-queues.md

[Azure Storage]: https://azure.microsoft.com/documentation/services/storage/
[Azure Service Bus]: https://azure.microsoft.com/documentation/services/service-bus/

[IoT Hub 開発者ガイド]: iot-hub-devguide.md
[lnk-devguide-messaging]: iot-hub-devguide-messaging.md
[IoT Hub の使用]: iot-hub-java-java-getstarted.md
[Azure IoT デベロッパー センター]: https://azure.microsoft.com/develop/iot
[Transient Fault Handling (一時的な障害の処理)]: https://msdn.microsoft.com/library/hh675232.aspx

<!-- Links -->
[Transient Fault Handling (一時的な障害の処理) (一時的な障害の処理)]: https://msdn.microsoft.com/library/hh680901(v=pandp.50).aspx

[lnk-c2d]: iot-hub-java-java-c2d.md
[lnk-suite]: https://azure.microsoft.com/documentation/suites/iot-suite/
