---
title: "Azure IoT Hub の使用 (Java) | Microsoft Docs"
description: "Azure IoT SDK for Java を使用して Azure IoT Hub デバイスでデバイスからクラウドへのメッセージを送信する方法。 メッセージを送信するシミュレーション対象デバイス アプリ、ID レジストリにデバイスを登録するサービス アプリ、およびデバイスからクラウドへのメッセージを IoT Hub から読み取るサービス アプリを作成します。"
services: iot-hub
documentationcenter: java
author: dominicbetts
manager: timlt
editor: 
ms.assetid: 70dae4a8-0e98-4c53-b5a5-9d6963abb245
ms.service: iot-hub
ms.devlang: java
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/07/2017
ms.author: dobett
ms.custom: H1Hack27Feb2017
translationtype: Human Translation
ms.sourcegitcommit: a087df444c5c88ee1dbcf8eb18abf883549a9024
ms.openlocfilehash: b7dbfff716806e8b91488d3eb5eafab582e173ba
ms.lasthandoff: 03/15/2017


---
# <a name="connect-your-simulated-device-to-your-iot-hub-using-java"></a>Java を使用してシミュレーション対象デバイスを IoT Hub に接続する
[!INCLUDE [iot-hub-selector-get-started](../../includes/iot-hub-selector-get-started.md)]

このチュートリアルの最後には、次の&3; つの Java コンソール アプリが完成します。

* **create-device-identity**。デバイス ID と関連付けられているセキュリティ キーを作成し、シミュレーション対象デバイス アプリを接続します。
* **read-d2c-messages**。シミュレーション対象デバイス アプリから送信されたテレメトリを表示します。
* **simulated-device**。以前に作成したデバイス ID で IoT Hub に接続し、MQTT プロトコルを使用して&1; 秒ごとにテレメトリ メッセージを送信します。

> [!NOTE]
> デバイス上で動作するアプリの作成とソリューションのバックエンドで動作するアプリの開発に利用できる各種 Azure IoT SDK については、[Azure IoT SDK][lnk-hub-sdks] に関する記事を参照してください。
> 
> 

このチュートリアルを完了するには、以下が必要です。

* Java SE 8。 <br/> 「[Prepare your development environment (開発環境を準備する)][lnk-dev-setup]」では、このチュートリアルのために Java を Windows または Linux にインストールする方法が説明されています。
* Maven 3。  <br/> [開発環境の準備][lnk-dev-setup]に関するページでは、このチュートリアル用に [Maven][lnk-maven] を Windows または Linux にインストールする方法が説明されています。
* アクティブな Azure アカウント。 (アカウントがない場合は、[無料アカウント][lnk-free-trial]を数分で作成できます)。

[!INCLUDE [iot-hub-get-started-create-hub](../../includes/iot-hub-get-started-create-hub.md)]

最後に、**主キー**の値をメモします。 次に、**[エンドポイント]** をクリックし、組み込みの **[イベント]** エンドポイントをクリックします。 **[プロパティ]** ブレードで、**イベント ハブ互換名**と**イベント ハブ互換エンドポイント** アドレスをメモします。 これら&3; つの値は、**read-d2c-messages** アプリを作成するときに必要になります。

![Azure portal IoT Hub Messaging blade][6]

IoT Hub の作成は以上です。 このチュートリアルを完了するために必要な IoT Hub ホスト名、IoT Hub 接続文字列、IoT Hub 主キー、Event Hub 対応の名前、Event Hub 対応エンドポイントが入手できました。

## <a name="create-a-device-identity"></a>デバイス ID の作成
このセクションでは、IoT ハブの ID レジストリにデバイス ID を作成する Java コンソール アプリケーションを作成します。 IoT hub に接続するデバイスは、あらかじめ ID レジストリに登録されている必要があります。 詳細については、[IoT Hub 開発者ガイド][lnk-devguide-identity]の **ID レジストリ**に関するセクションをご覧ください。 このコンソール アプリケーションを実行すると、デバイスからクラウドへのメッセージを IoT Hub に送信するときにそのデバイスを識別する一意の ID とキーが生成されます。

1. "iot-java-get-started" という名前の空のフォルダーを作成します。 コマンド プロンプトで次のコマンドを実行し、iot-java-get-started フォルダーに **create-device-identity** という名前の Maven プロジェクトを作成します。 これは、1 つの長いコマンドであることに注意してください。
   
    ```
    mvn archetype:generate -DgroupId=com.mycompany.app -DartifactId=create-device-identity -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false
    ```
2. コマンド プロンプトで、create-device-identity フォルダーに移動します。
3. テキスト エディターを使用し、create-device-identity フォルダー内の pom.xml ファイルを開き、次の依存関係を **dependencies** ノードに追加します。 この依存関係により、アプリで iot-service-client パッケージを利用できるようになります。
   
    ```
    </dependency>
      <groupId>com.microsoft.azure.sdk.iot</groupId>
      <artifactId>iot-service-client</artifactId>
      <version>1.2.17</version>
    </dependency>
    ```
    
    > [!NOTE]
    > [Maven 検索][lnk-maven-service-search]を使用して、**iot-service-client** の最新バージョンを確認できます。

4. pom.xml ファイルを保存して閉じます。
5. テキスト エディターを使用し、create-device-identity\src\main\java\com\mycompany\app\App.java ファイルを開きます。
6. ファイルに次の **import** ステートメントを追加します。
   
    ```
    import com.microsoft.azure.sdk.iot.service.exceptions.IotHubException;
    import com.microsoft.azure.sdk.iot.service.Device;
    import com.microsoft.azure.sdk.iot.service.RegistryManager;
   
    import java.io.IOException;
    import java.net.URISyntaxException;
    ```
7. 次のクラスレベルの変数を **App** クラスに追加し、**{yourhubconnectionstring}** を先ほどメモした値に置き換えます。
   
    ```
    private static final String connectionString = "{yourhubconnectionstring}";
    private static final String deviceId = "myFirstJavaDevice";
   
    ```
8. **main** メソッドのシグネチャを変更し、下の例外を追加します。
   
    ```
    public static void main( String[] args ) throws IOException, URISyntaxException, Exception
    ```
9. **main** メソッドの本文として次のコードを追加します。 このコードにより、 *javadevice* という名前のデバイスが IoT ハブの ID レジストリにない場合は作成されます。 その後、デバイス ID とキーが表示されますが、これは後で必要になります。
   
    ```
    RegistryManager registryManager = RegistryManager.createFromConnectionString(connectionString);
   
    Device device = Device.createFromId(deviceId, null, null);
    try {
      device = registryManager.addDevice(device);
    } catch (IotHubException iote) {
      try {
        device = registryManager.getDevice(deviceId);
      } catch (IotHubException iotf) {
        iotf.printStackTrace();
      }
    }
    System.out.println("Device ID: " + device.getDeviceId());
    System.out.println("Device key: " + device.getPrimaryKey());
    ```
10. App.java ファイルを保存して閉じます。
11. Maven を使用して **create-device-identity** アプリをビルドするには、コマンド プロンプトで create-device-identity フォルダーに移動し、次のコマンドを実行します。
    
    ```
    mvn clean package -DskipTests
    ```
12. Maven を使用して **create-device-identity** アプリを実行するには、コマンド プロンプトで create-device-identity フォルダーに移動し、次のコマンドを実行します。
    
    ```
    mvn exec:java -Dexec.mainClass="com.mycompany.app.App"
    ```
13. **デバイス ID** と**デバイス キー**をメモします。 これらの値は、後でデバイスとして IoT Hub に接続するアプリを作成するときに必要になります。

> [!NOTE]
> IoT Hub の ID レジストリには、IoT ハブに対するセキュリティで保護されたアクセスを有効にするためのデバイス ID のみが格納されます。 セキュリティ資格情報として使用するキーとデバイス ID、そして個々のデバイスについてアクセスを無効にすることのできる有効/無効フラグが格納されます。 その他デバイス固有のメタデータをアプリで保存する必要がある場合は、アプリ固有のストアを使用する必要があります。 詳細については、[IoT Hub 開発者ガイド][lnk-devguide-identity]をご覧ください。
> 
> 

## <a name="receive-device-to-cloud-messages"></a>デバイスからクラウドへのメッセージの受信
このセクションでは、デバイスからクラウドへのメッセージを IoT Hub から読み取る Java コンソール アプリケーションを作成します。 IoT Hub は、デバイスからクラウドへのメッセージを読み取るための、[イベント ハブ][lnk-event-hubs-overview]と互換性のあるエンドポイントを公開します。 わかりやすくするために、このチュートリアルで作成するリーダーは基本的なものであり、高スループットのデプロイメントには適していません。 [デバイスからクラウドへのメッセージの処理][lnk-process-d2c-tutorial]に関するチュートリアルでは、デバイスからクラウドへのメッセージを大規模に処理する方法を紹介しています。 「[Event Hubs の使用][lnk-eventhubs-tutorial]」チュートリアルでは、Event Hubs からのメッセージを処理する方法について詳しく説明しています。また、このチュートリアルは IoT Hub のイベント ハブと互換性のあるエンドポイントに当てはまります。

> [!NOTE]
> Event Hub 対応エンドポイントは、常に、デバイスからクラウドへのメッセージを読み取るために AMQP プロトコルを使用します。
> 
> 

1. コマンド プロンプトで次のコマンドを実行し、「*デバイス ID の作成*」セクションで作成した iot-java-get-started フォルダーに **read-d2c-messages** という名前の Maven プロジェクトを作成します。 これは、1 つの長いコマンドであることに注意してください。
   
    ```
    mvn archetype:generate -DgroupId=com.mycompany.app -DartifactId=read-d2c-messages -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false
    ```
2. コマンド プロンプトで、read-d2c-messages フォルダーに移動します。
3. テキスト エディターを使用して、read-d2c-messages フォルダー内の pom.xml ファイルを開き、次の依存関係を **dependencies** ノードに追加します。 この依存関係により、アプリの eventhubs-client パッケージを利用して、イベント ハブと互換性のあるエンドポイントから読み込めるようになります。
   
    ```
    <dependency> 
        <groupId>com.microsoft.azure</groupId> 
        <artifactId>azure-eventhubs</artifactId> 
        <version>0.13.0</version> 
    </dependency>
    ```

    > [!NOTE]
    > [Maven 検索][lnk-maven-eventhubs-search]を使用して、**azure-eventhubs** の最新バージョンを確認できます。

4. pom.xml ファイルを保存して閉じます。
5. テキスト エディターを使用し、read-d2c-messages\src\main\java\com\mycompany\app\App.java ファイルを開きます。
6. ファイルに次の **import** ステートメントを追加します。
   
    ```
    import java.io.IOException;
    import com.microsoft.azure.eventhubs.*;
    import com.microsoft.azure.servicebus.*;

    import java.nio.charset.Charset;
    import java.time.*;
    import java.util.function.*;
    ```
7. 次のクラスレベル変数を **App** クラスに追加します。 **{youriothubkey}**、**{youreventhubcompatibleendpoint}**、**{youreventhubcompatiblename}** を先にメモした値に置き換えます。
   
    ```
    private static String connStr = "Endpoint={youreventhubcompatibleendpoint};EntityPath={youreventhubcompatiblename};SharedAccessKeyName=iothubowner;SharedAccessKey={youriothubkey}";
    ```
8. 次の **receiveMessages** メソッドを **App** クラスに追加します。 このメソッドは **EventHubClient** インスタンスを作成して Event Hub 対応エンドポイントに接続し、**PartitionReceiver** インスタンスを非同期的に作成してイベント ハブ パーティションから読み取ります。 これは、アプリが終了するまでループを続け、メッセージの詳細を出力します。
   
    ```
    private static EventHubClient receiveMessages(final String partitionId)
    {
      EventHubClient client = null;
      try {
        client = EventHubClient.createFromConnectionStringSync(connStr);
      }
      catch(Exception e) {
        System.out.println("Failed to create client: " + e.getMessage());
        System.exit(1);
      }
      try {
        client.createReceiver( 
          EventHubClient.DEFAULT_CONSUMER_GROUP_NAME,  
          partitionId,  
          Instant.now()).thenAccept(new Consumer<PartitionReceiver>()
        {
          public void accept(PartitionReceiver receiver)
          {
            System.out.println("** Created receiver on partition " + partitionId);
            try {
              while (true) {
                Iterable<EventData> receivedEvents = receiver.receive(100).get();
                int batchSize = 0;
                if (receivedEvents != null)
                {
                  for(EventData receivedEvent: receivedEvents)
                  {
                    System.out.println(String.format("Offset: %s, SeqNo: %s, EnqueueTime: %s", 
                      receivedEvent.getSystemProperties().getOffset(), 
                      receivedEvent.getSystemProperties().getSequenceNumber(), 
                      receivedEvent.getSystemProperties().getEnqueuedTime()));
                    System.out.println(String.format("| Device ID: %s", receivedEvent.getSystemProperties().get("iothub-connection-device-id")));
                    System.out.println(String.format("| Message Payload: %s", new String(receivedEvent.getBody(),
                      Charset.defaultCharset())));
                    batchSize++;
                  }
                }
                System.out.println(String.format("Partition: %s, ReceivedBatch Size: %s", partitionId,batchSize));
              }
            }
            catch (Exception e)
            {
              System.out.println("Failed to receive messages: " + e.getMessage());
            }
          }
        });
      }
      catch (Exception e)
      {
        System.out.println("Failed to create receiver: " + e.getMessage());
      }
      return client;
    }
    ```
   
   > [!NOTE]
   > 受信側が実行開始後、IoT Hub に送信されたメッセージのみを読み込むように、このメソッドは受信側の構築時にフィルターを称します。 この手法は、現在のメッセージのセットを表示できるので、テスト環境で便利です。 運用環境の場合、すべてのメッセージがコードで処理されるようにする必要があります。詳細については、[IoT Hub のデバイスからクラウドへのメッセージを処理する方法][lnk-process-d2c-tutorial]に関するチュートリアルを参照してください。
   > 
   > 
9. **main** メソッドのシグネチャを変更し、下の例外を追加します。
   
    ```
    public static void main( String[] args ) throws IOException
    ```
10. **App** クラスの **main** メソッドに次のコードを追加します。 このコードは&2; つのインスタンス **EventHubClient** と **PartitionReceiver** を作成し、メッセージの処理が完了したときにアプリを終了できるようにします。
    
    ```
    EventHubClient client0 = receiveMessages("0");
    EventHubClient client1 = receiveMessages("1");
    System.out.println("Press ENTER to exit.");
    System.in.read();
    try
    {
      client0.closeSync();
      client1.closeSync();
      System.exit(0);
    }
    catch (ServiceBusException sbe)
    {
      System.exit(1);
    }
    ```
    
    > [!NOTE]
    > このコードでは、F1 (free) レベルで IoT hub を作成したと想定しています。 無料の IoT Hub は、2 つのパーティションがあります。名前は「0」と「1」です。
    > 
    > 
11. App.java ファイルを保存して閉じます。
12. Maven を使用して **read-d2c-messages** アプリをビルドするには、コマンド プロンプトで read-d2c-messages フォルダーに移動し、次のコマンドを実行します。
    
    ```
    mvn clean package -DskipTests
    ```

## <a name="create-a-simulated-device-app"></a>シミュレーション対象デバイス アプリの作成
このセクションでは、デバイスからクラウドへのメッセージを IoT ハブに送信するデバイスをシミュレートする Java コンソール アプリを作成します。

1. コマンド プロンプトで次のコマンドを実行し、「*デバイス ID の作成*」セクションで作成した iot-java-get-started フォルダーに **simulated-device** という名前の Maven プロジェクトを作成します。 これは、1 つの長いコマンドであることに注意してください。
   
    ```
    mvn archetype:generate -DgroupId=com.mycompany.app -DartifactId=simulated-device -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false
    ```
2. コマンド プロンプトで、simulated-device フォルダーに移動します。
3. テキスト エディターを使用して、simulated-device フォルダー内の pom.xml ファイルを開き、次の依存関係を **dependencies** ノードに追加します。 この依存関係により、アプリの iothub-java-client パッケージを使用して IoT Hub と通信し、Java オブジェクトを JSON にシリアル化できるようになります。
   
    ```
    <dependency>
      <groupId>com.microsoft.azure.sdk.iot</groupId>
      <artifactId>iot-device-client</artifactId>
      <version>1.1.23</version>
    </dependency>
    <dependency>
      <groupId>com.google.code.gson</groupId>
      <artifactId>gson</artifactId>
      <version>2.3.1</version>
    </dependency>
    ```

    > [!NOTE]
    > [Maven 検索][lnk-maven-device-search]を使用して、**iot-device-client** の最新バージョンを確認できます。

4. pom.xml ファイルを保存して閉じます。
5. テキスト エディターを使用し、simulated-device\src\main\java\com\mycompany\app\App.java ファイルを開きます。
6. ファイルに次の **import** ステートメントを追加します。
   
    ```
    import com.microsoft.azure.sdk.iot.device.DeviceClient;
    import com.microsoft.azure.sdk.iot.device.IotHubClientProtocol;
    import com.microsoft.azure.sdk.iot.device.Message;
    import com.microsoft.azure.sdk.iot.device.IotHubStatusCode;
    import com.microsoft.azure.sdk.iot.device.IotHubEventCallback;
    import com.microsoft.azure.sdk.iot.device.MessageCallback;
    import com.microsoft.azure.sdk.iot.device.IotHubMessageResult;
    import com.google.gson.Gson;

    import java.io.IOException;
    import java.net.URISyntaxException;
    import java.util.Random;
    import java.util.concurrent.Executors;
    import java.util.concurrent.ExecutorService;
    ```
7. 次のクラスレベル変数を **App** クラスに追加します。 **{youriothubname}** を IoT Hub 名に、**{yourdevicekey}** を「*デバイス ID の作成*」セクションで生成したデバイス キーの値に置き換えます。
   
    ```
    private static String connString = "HostName={youriothubname}.azure-devices.net;DeviceId=myFirstJavaDevice;SharedAccessKey={yourdevicekey}";
    private static IotHubClientProtocol protocol = IotHubClientProtocol.MQTT;
    private static String deviceId = "myFirstJavaDevice";
    private static DeviceClient client;
    ```
   
    このサンプル アプリでは、**DeviceClient** オブジェクトをインスタンス化するときに **protocol** 変数が使用されます。 MQTT、AMQP、HTTP のいずれかのプロトコルを利用して IoT Hub と通信できます。
8. 次の入れ子になった **TelemetryDataPoint** クラスを **App** クラス内に追加し、デバイスから IoT ハブに送信するテレメトリ データを指定します。
   
    ```
    private static class TelemetryDataPoint {
      public String deviceId;
      public double windSpeed;
   
      public String serialize() {
        Gson gson = new Gson();
        return gson.toJson(this);
      }
    }
    ```
9. 次の入れ子になった **EventCallback** クラスを **App** クラス内に追加し、シミュレーション対象デバイス アプリからのメッセージを処理するときに IoT Hub が返す受信確認ステータスを表示します。 このメソッドにより、メッセージが処理されたときのアプリのメイン スレッドも通知されます。
   
    ```
    private static class EventCallback implements IotHubEventCallback
    {
      public void execute(IotHubStatusCode status, Object context) {
        System.out.println("IoT Hub responded to message with status: " + status.name());
   
        if (context != null) {
          synchronized (context) {
            context.notify();
          }
        }
      }
    }
    ```
10. 次の入れ子になった **MessageSender** クラスを **App** クラス内に追加します。 このクラスの **run** メソッドは、IoT ハブに送信するサンプルのテレメトリ データを生成し、受信確認を待ってから次のメッセージを送信します。
    
    ```
    private static class MessageSender implements Runnable {
    
      public void run()  {
        try {
          double avgWindSpeed = 10; // m/s
          Random rand = new Random();
    
          while (true) {
            double currentWindSpeed = avgWindSpeed + rand.nextDouble() * 4 - 2;
            TelemetryDataPoint telemetryDataPoint = new TelemetryDataPoint();
            telemetryDataPoint.deviceId = deviceId;
            telemetryDataPoint.windSpeed = currentWindSpeed;
    
            String msgStr = telemetryDataPoint.serialize();
            Message msg = new Message(msgStr);
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
    
    このメソッドは、IoT Hub が前のメッセージを確認してから&1; 秒後に新しい「デバイスからクラウドへの」メッセージを送信します。 このメッセージには、JSON 形式でシリアル化されたオブジェクトが、デバイス ID、ランダムに生成された番号と共に含まれ、これによって風速センサーがシミュレートされます。
11. 「デバイスからクラウドへの」メッセージを IoT Hub に送信するスレッドを作成する次のコードで **main** メソッドを置換します。
    
    ```
    public static void main( String[] args ) throws IOException, URISyntaxException {
      client = new DeviceClient(connString, protocol);
      client.open();
    
      MessageSender sender = new MessageSender();
    
      ExecutorService executor = Executors.newFixedThreadPool(1);
      executor.execute(sender);
    
      System.out.println("Press ENTER to exit.");
      System.in.read();
      executor.shutdownNow();
      client.close();
    }
    ```
12. App.java ファイルを保存して閉じます。
13. Maven を使用して **simulated-device** アプリをビルドするには、コマンド プロンプトで simulated-device フォルダーに移動し、次のコマンドを実行します。
    
    ```
    mvn clean package -DskipTests
    ```

> [!NOTE]
> わかりやすくするために、このチュートリアルでは再試行ポリシーは実装しません。 運用環境のコードでは、[一時的な障害処理][lnk-transient-faults]に関する MSDN の記事で推奨されているように、再試行ポリシー (指数関数的バックオフなど) を実装することをお勧めします。
> 
> 

## <a name="run-the-apps"></a>アプリの実行
これで、アプリを実行する準備が整いました。

1. コマンド プロンプトを使って read-d2c フォルダーで次のコマンドを実行し、IoT Hub の最初のパーティションの監視を開始します。
   
    ```
    mvn exec:java -Dexec.mainClass="com.mycompany.app.App"
    ```
   
    ![デバイスからクラウドへのメッセージを監視するための Java IoT Hub サービス アプリ][7]
2. simulated-device フォルダーで、コマンド プロンプトで次のコマンドを実行し、IoT Hub へのテレメトリ データの送信を開始します。
   
    ```
    mvn exec:java -Dexec.mainClass="com.mycompany.app.App" 
    ```
   
    ![デバイスからクラウドへのメッセージを送信するための Java IoT Hub デバイス アプリ][8]
3. [Azure Portal][lnk-portal] の **[使用状況]** タイルには、IoT Hub に送信されたメッセージ数が表示されます。
   
    ![Azure portal Usage tile showing number of messages sent to IoT Hub][43]

## <a name="next-steps"></a>次のステップ
このチュートリアルでは、Azure Portal で新しい IoT Hub を構成し、IoT Hub の ID レジストリにデバイス ID を作成しました。 シミュレーション対象デバイス アプリでデバイスからクラウドへのメッセージを IoT Hub に送信できるようにするために、このデバイス ID を使用しました。 また、IoT Hub で受け取ったメッセージを表示するアプリを作成しました。 

引き続き IoT Hub の使用方法を確認すると共に、他の IoT のシナリオについて調べるには、次のページを参照してください。

* [デバイスを接続する][lnk-connect-device]
* [デバイス管理の概要][lnk-device-management]
* [IoT Gateway SDK の概要][lnk-gateway-SDK]

既存の IoT ソリューションを拡張し、デバイスからクラウドへのメッセージを大規模に処理する方法については、[デバイスからクラウドへのメッセージの処理][lnk-process-d2c-tutorial]に関するチュートリアルを参照してください。

<!-- Images. -->
[6]: ./media/iot-hub-java-java-getstarted/create-iot-hub6.png
[7]: ./media/iot-hub-java-java-getstarted/runapp1.png
[8]: ./media/iot-hub-java-java-getstarted/runapp2.png
[43]: ./media/iot-hub-java-java-getstarted/usage.png

<!-- Links -->
[lnk-transient-faults]: https://msdn.microsoft.com/library/hh680901(v=pandp.50).aspx

[lnk-eventhubs-tutorial]: ../event-hubs/event-hubs-csharp-ephcs-getstarted.md
[lnk-devguide-identity]: iot-hub-devguide-identity-registry.md
[lnk-event-hubs-overview]: ../event-hubs/event-hubs-overview.md

[lnk-dev-setup]: https://github.com/Azure/azure-iot-sdk-java
[lnk-process-d2c-tutorial]: iot-hub-csharp-csharp-process-d2c.md

[lnk-hub-sdks]: iot-hub-devguide-sdks.md
[lnk-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[lnk-portal]: https://portal.azure.com/

[lnk-device-management]: iot-hub-node-node-device-management-get-started.md
[lnk-gateway-SDK]: iot-hub-linux-gateway-sdk-get-started.md
[lnk-connect-device]: https://azure.microsoft.com/develop/iot/
[lnk-maven]: https://maven.apache.org/what-is-maven.html
[lnk-maven-service-search]: http://search.maven.org/#search%7Cga%7C1%7Ca%3A%22iot-service-client%22%20g%3A%22com.microsoft.azure.sdk.iot%22
[lnk-maven-device-search]: http://search.maven.org/#search%7Cga%7C1%7Ca%3A%22iot-device-client%22%20g%3A%22com.microsoft.azure.sdk.iot%22
[lnk-maven-eventhubs-search]: http://search.maven.org/#search%7Cga%7C1%7Ca%3A%22azure-eventhubs%22
