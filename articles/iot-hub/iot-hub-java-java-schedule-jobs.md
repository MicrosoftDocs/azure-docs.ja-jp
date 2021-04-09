---
title: Azure IoT Hub を使用してジョブのスケジュールを設定する (Java) | Microsoft Docs
description: 複数のデバイスでダイレクト メソッドを呼び出して、必要なプロパティを設定するように Azure IoT Hub ジョブのスケジュールを設定する方法。 Azure IoT device SDK for Java を使用して、シミュレートされたデバイス アプリを実装し、Azure IoT service SDK for Java を使用して、ジョブを実行するサービス アプリを実装します。
author: wesmc7777
manager: philmea
ms.author: wesmc
ms.service: iot-hub
services: iot-hub
ms.devlang: java
ms.topic: conceptual
ms.date: 08/16/2019
ms.custom: mqtt, devx-track-java
ms.openlocfilehash: 3e98cfc2d8c7fb8d40c8565a1c620f123ce171ff
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102217841"
---
# <a name="schedule-and-broadcast-jobs-java"></a>ジョブのスケジュールとブロードキャスト (Java)

[!INCLUDE [iot-hub-selector-schedule-jobs](../../includes/iot-hub-selector-schedule-jobs.md)]

Azure IoT Hub を使用して、数百万のデバイスを更新するジョブのスケジュールと追跡を行います。 ジョブを使用して、次の操作を行います。

* 必要なプロパティを更新する
* タグを更新する
* ダイレクト メソッドを呼び出す

ジョブはこれらのアクションの 1 つをラップし、デバイスのセットに対して実行状況を追跡します。 ジョブが実行されるデバイスのセットはデバイス ツイン クエリで定義されます。 たとえば、バックエンド アプリは、ジョブを使用して 10,000 台のデバイスでダイレクト メソッドを呼び出し、デバイスを再起動できます。 デバイス ツイン クエリでデバイスのセットを指定し、ジョブが将来実行されるようにスケジュールを設定します。 各デバイスが再起動のダイレクト メソッドを受信し、実行するたびに、ジョブは進行状況を追跡します。

これらの各機能の詳細については、次の記事をご覧ください。

* デバイス ツインとプロパティ: [デバイス ツインを使ってみる](iot-hub-java-java-twin-getstarted.md)

* ダイレクト メソッド: [IoT Hub 開発者ガイド - ダイレクト メソッド](iot-hub-devguide-direct-methods.md)および[チュートリアル: ダイレクト メソッドの使用](quickstart-control-device-java.md)

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-whole.md)]

このチュートリアルでは、次の操作方法について説明します。

* **lockDoor** と呼ばれるダイレクト メソッドを実装するデバイス アプリの作成。 デバイス アプリは、バックエンド アプリから必要なプロパティの変更も受信します。

* 複数のデバイスで **lockDoor** ダイレクト メソッドを呼び出すジョブを作成するバックエンド アプリの作成。 もう 1 つのジョブは、必要なプロパティの更新を複数のデバイスに送信します。

このチュートリアルが終わると、次の java コンソール デバイス アプリと java コンソール バックエンド アプリが完成しています。

**simulated-device** は IoT Hub に接続して、**lockDoor** ダイレクト メソッドを実装し、必要なプロパティの変更を処理します。

**schedule-jobs** はジョブを使用して **lockDoor** ダイレクト メソッドを呼び出し、複数のデバイスでデバイス ツインの必要なプロパティを更新します。

> [!NOTE]
> デバイス アプリとバックエンド アプリの両方をビルドするために使用できる Azure IoT SDK については、記事「[Azure IoT SDK](iot-hub-devguide-sdks.md)」を参照してください。

## <a name="prerequisites"></a>前提条件

* [Java SE Development Kit 8](/java/azure/jdk/)。 JDK 8 のダウンロードを利用するには、「**長期サポート**」の「**Java 8**」を選択します。

* [Maven 3](https://maven.apache.org/download.cgi)

* アクティブな Azure アカウントアカウントがない場合、Azure 試用版にサインアップして、最大 10 件の無料 Mobile Apps を入手できます。 (アカウントがない場合は、[無料アカウント](https://azure.microsoft.com/pricing/free-trial/) を数分で作成できます)。

* ポート 8883 がファイアウォールで開放されていることを確認してください。 この記事のデバイス サンプルでは、ポート 8883 を介して通信する MQTT プロトコルを使用しています。 このポートは、企業や教育用のネットワーク環境によってはブロックされている場合があります。 この問題の詳細と対処方法については、「[IoT Hub への接続 (MQTT)](iot-hub-mqtt-support.md#connecting-to-iot-hub)」を参照してください。

## <a name="create-an-iot-hub"></a>IoT Hub の作成

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="register-a-new-device-in-the-iot-hub"></a>IoT ハブに新しいデバイスを登録する

[!INCLUDE [iot-hub-include-create-device](../../includes/iot-hub-include-create-device.md)]

[Azure CLI 向け IoT 拡張機能](https://github.com/Azure/azure-iot-cli-extension)ツールを使って、デバイスを IoT Hub に追加することもできます。

## <a name="get-the-iot-hub-connection-string"></a>IoT ハブ接続文字列を取得する

[!INCLUDE [iot-hub-howto-schedule-jobs-shared-access-policy-text](../../includes/iot-hub-howto-schedule-jobs-shared-access-policy-text.md)]

[!INCLUDE [iot-hub-include-find-registryrw-connection-string](../../includes/iot-hub-include-find-registryrw-connection-string.md)]

## <a name="create-the-service-app"></a>デバイス アプリを作成する

このセクションでは、ジョブを使用する Java コンソール アプリを作成して、次の操作を行います。

* 複数のデバイスで **lockDoor** ダイレクト メソッドを呼び出す。

* 必要なプロパティを複数のデバイスに送信する。

アプリを作成するには:

1. 開発用コンピューターで、**iot-java-schedule-jobs** という名前の空のフォルダーを作成します。

2. コマンド プロンプトで次のコマンドを使用して、**iot-java-schedule-jobs** フォルダー内に **schedule-jobs** という名前の Maven プロジェクトを作成します。 これは、1 つの長いコマンドであることに注意してください。

   ```cmd/sh
   mvn archetype:generate -DgroupId=com.mycompany.app -DartifactId=schedule-jobs -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false
   ```

3. コマンド プロンプトで、**schedule-jobs** フォルダーに移動します。

4. テキスト エディターを使用して、**schedule-jobs** フォルダー内の **pom.xml** ファイルを開き、次の依存関係を **dependencies** ノードに追加します。 この依存関係により、アプリで **iot-service-client** パッケージを使用して IoT Hub と通信できるようになります。

    ```xml
    <dependency>
      <groupId>com.microsoft.azure.sdk.iot</groupId>
      <artifactId>iot-service-client</artifactId>
      <version>1.17.1</version>
      <type>jar</type>
    </dependency>
    ```

    > [!NOTE]
    > [Maven 検索](https://search.maven.org/#search%7Cga%7C1%7Ca%3A%22iot-service-client%22%20g%3A%22com.microsoft.azure.sdk.iot%22)を使用して、**iot-service-client** の最新バージョンを確認できます。

5. **dependencies** ノードの後に、次の **build** ノードを追加します。 この構成では、Java 1.8 を使用してアプリをビルドするように Maven に指示しています。

    ```xml
    <build>
      <plugins>
        <plugin>
          <groupId>org.apache.maven.plugins</groupId>
          <artifactId>maven-compiler-plugin</artifactId>
          <version>3.3</version>
          <configuration>
            <source>1.8</source>
            <target>1.8</target>
          </configuration>
        </plugin>
      </plugins>
    </build>
    ```

6. **pom.xml** ファイルを保存して閉じます。

7. テキスト エディターを使用して、**schedule-jobs\src\main\java\com\mycompany\app\App.java** ファイルを開きます。

8. ファイルに次の **import** ステートメントを追加します。

    ```java
    import com.microsoft.azure.sdk.iot.service.devicetwin.DeviceTwinDevice;
    import com.microsoft.azure.sdk.iot.service.devicetwin.Pair;
    import com.microsoft.azure.sdk.iot.service.devicetwin.Query;
    import com.microsoft.azure.sdk.iot.service.devicetwin.SqlQuery;
    import com.microsoft.azure.sdk.iot.service.jobs.JobClient;
    import com.microsoft.azure.sdk.iot.service.jobs.JobResult;
    import com.microsoft.azure.sdk.iot.service.jobs.JobStatus;

    import java.util.Date;
    import java.time.Instant;
    import java.util.HashSet;
    import java.util.Set;
    import java.util.UUID;
    ```

9. 次のクラスレベル変数を **App** クラスに追加します。 `{youriothubconnectionstring}` は、先ほど「[IoT ハブ接続文字列を取得する](#get-the-iot-hub-connection-string)」でコピーしておいた IoT ハブ接続文字列に置き換えてください。

    ```java
    public static final String iotHubConnectionString = "{youriothubconnectionstring}";
    public static final String deviceId = "myDeviceId";

    // How long the job is permitted to run without
    // completing its work on the set of devices
    private static final long maxExecutionTimeInSeconds = 30;
    ```

10. デバイス ツインで次のメソッドを **App** クラスに追加して、必要なプロパティ **Building** および **Floor** を更新するようにジョブのスケジュールを設定します。

    ```java
    private static JobResult scheduleJobSetDesiredProperties(JobClient jobClient, String jobId) {
      DeviceTwinDevice twin = new DeviceTwinDevice(deviceId);
      Set<Pair> desiredProperties = new HashSet<Pair>();
      desiredProperties.add(new Pair("Building", 43));
      desiredProperties.add(new Pair("Floor", 3));
      twin.setDesiredProperties(desiredProperties);
      // Optimistic concurrency control
      twin.setETag("*");

      // Schedule the update twin job to run now
      // against a single device
      System.out.println("Schedule job " + jobId + " for device " + deviceId);
      try {
        JobResult jobResult = jobClient.scheduleUpdateTwin(jobId, 
          "deviceId='" + deviceId + "'",
          twin,
          new Date(),
          maxExecutionTimeInSeconds);
        return jobResult;
      } catch (Exception e) {
        System.out.println("Exception scheduling desired properties job: " + jobId);
        System.out.println(e.getMessage());
        return null;
      }
    }
    ```

11. **lockDoor** メソッドを呼び出すようにジョブのスケジュールを設定するには、次のメソッドを **App** クラスに追加します。

    ```java
    private static JobResult scheduleJobCallDirectMethod(JobClient jobClient, String jobId) {
      // Schedule a job now to call the lockDoor direct method
      // against a single device. Response and connection
      // timeouts are set to 5 seconds.
      System.out.println("Schedule job " + jobId + " for device " + deviceId);
      try {
        JobResult jobResult = jobClient.scheduleDeviceMethod(jobId,
          "deviceId='" + deviceId + "'",
          "lockDoor",
          5L, 5L, null,
          new Date(),
          maxExecutionTimeInSeconds);
        return jobResult;
      } catch (Exception e) {
        System.out.println("Exception scheduling direct method job: " + jobId);
        System.out.println(e.getMessage());
        return null;
      }
    };
    ```

12. ジョブを監視するには、次のメソッドを **App** クラスに追加します。

    ```java
    private static void monitorJob(JobClient jobClient, String jobId) {
      try {
        JobResult jobResult = jobClient.getJob(jobId);
        if(jobResult == null)
        {
          System.out.println("No JobResult for: " + jobId);
          return;
        }
        // Check the job result until it's completed
        while(jobResult.getJobStatus() != JobStatus.completed)
        {
          Thread.sleep(100);
          jobResult = jobClient.getJob(jobId);
          System.out.println("Status " + jobResult.getJobStatus() + " for job " + jobId);
        }
        System.out.println("Final status " + jobResult.getJobStatus() + " for job " + jobId);
      } catch (Exception e) {
        System.out.println("Exception monitoring job: " + jobId);
        System.out.println(e.getMessage());
        return;
      }
    }
    ```

13. 実行したジョブの詳細に関するクエリを実行するには、次のメソッドを追加します。

    ```java
    private static void queryDeviceJobs(JobClient jobClient, String start) throws Exception {
      System.out.println("\nQuery device jobs since " + start);

      // Create a jobs query using the time the jobs started
      Query deviceJobQuery = jobClient
          .queryDeviceJob(SqlQuery.createSqlQuery("*", SqlQuery.FromType.JOBS, "devices.jobs.startTimeUtc > '" + start + "'", null).getQuery());

      // Iterate over the list of jobs and print the details
      while (jobClient.hasNextJob(deviceJobQuery)) {
        System.out.println(jobClient.getNextJob(deviceJobQuery));
      }
    }
    ```

14. **main** メソッドのシグネチャを、次の `throws` 句を含むように更新します。

    ```java
    public static void main( String[] args ) throws Exception
    ```

15. 2 つのジョブを順番に実行して監視するには、**main** メソッドのコードを、次のコードに置き換えます。

    ```java
    // Record the start time
    String start = Instant.now().toString();

    // Create JobClient
    JobClient jobClient = JobClient.createFromConnectionString(iotHubConnectionString);
    System.out.println("JobClient created with success");

    // Schedule twin job desired properties
    // Maximum concurrent jobs is 1 for Free and S1 tiers
    String desiredPropertiesJobId = "DPCMD" + UUID.randomUUID();
    scheduleJobSetDesiredProperties(jobClient, desiredPropertiesJobId);
    monitorJob(jobClient, desiredPropertiesJobId);

    // Schedule twin job direct method
    String directMethodJobId = "DMCMD" + UUID.randomUUID();
    scheduleJobCallDirectMethod(jobClient, directMethodJobId);
    monitorJob(jobClient, directMethodJobId);

    // Run a query to show the job detail
    queryDeviceJobs(jobClient, start);

    System.out.println("Shutting down schedule-jobs app");
    ```

16. **schedule-jobs\src\main\java\com\mycompany\app\App.java** ファイルを保存して閉じます。

17. **schedule-jobs** アプリをビルドし、エラーを修正します。 コマンド プロンプトで **schedule-jobs** フォルダーに移動し、次のコマンドを実行します。

    ```cmd/sh
    mvn clean package -DskipTests
    ```

## <a name="create-a-device-app"></a>デバイス アプリの作成

このセクションでは、IoT Hub から送信された必要なプロパティを処理する Java コンソール アプリを作成し、ダイレクト メソッド呼び出しを実装します。

1. コマンド プロンプトで次のコマンドを実行して、**iot-java-schedule-jobs** フォルダー内に **simulated-device** という名前の Maven プロジェクトを作成します。 これは、1 つの長いコマンドであることに注意してください。

   ```cmd/sh
   mvn archetype:generate -DgroupId=com.mycompany.app -DartifactId=simulated-device -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false
   ```

2. コマンド プロンプトで、**simulated-device** フォルダーに移動します。

3. テキスト エディターを使用して、**simulated-device** フォルダー内の **pom.xml** ファイルを開き、次の依存関係を **dependencies** ノードに追加します。 この依存関係により、アプリで **iot-device-client** パッケージを使用して IoT Hub と通信できるようになります。

    ```xml
    <dependency>
      <groupId>com.microsoft.azure.sdk.iot</groupId>
      <artifactId>iot-device-client</artifactId>
      <version>1.17.5</version>
    </dependency>
    ```

    > [!NOTE]
    > [Maven 検索](https://search.maven.org/#search%7Cga%7C1%7Ca%3A%22iot-device-client%22%20g%3A%22com.microsoft.azure.sdk.iot%22)を使用して、**iot-device-client** の最新バージョンを確認できます。

4. **dependencies** ノードに、次の依存関係を追加します。 この依存関係によって、Apache [SLF4J](https://www.slf4j.org/) ログ記録ファサード用の NOP が構成され、ログ記録を実装するためにデバイス クライアント SDK によって使用されます。 この構成は省略可能ですが、省略した場合、アプリの実行時にコンソールに警告が表示される可能性があります。 デバイス クライアント SDK でのログ記録の詳細については、*Samples for the Azure IoT device SDK for Java* readme ファイルに含まれている [ログ記録](https://github.com/Azure/azure-iot-sdk-java/blob/master/device/iot-device-samples/readme.md#logging)を参照してください。

    ```xml
    <dependency>
      <groupId>org.slf4j</groupId>
      <artifactId>slf4j-nop</artifactId>
      <version>1.7.28</version>
    </dependency>
    ```

5. **dependencies** ノードの後に、次の **build** ノードを追加します。 この構成では、Java 1.8 を使用してアプリをビルドするように Maven に指示しています。

    ```xml
    <build>
      <plugins>
        <plugin>
          <groupId>org.apache.maven.plugins</groupId>
          <artifactId>maven-compiler-plugin</artifactId>
          <version>3.3</version>
          <configuration>
            <source>1.8</source>
            <target>1.8</target>
          </configuration>
        </plugin>
      </plugins>
    </build>
    ```

6. **pom.xml** ファイルを保存して閉じます。

7. テキスト エディターを使用して、**simulated-device\src\main\java\com\mycompany\app\App.java** ファイルを開きます。

8. ファイルに次の **import** ステートメントを追加します。

    ```java
    import com.microsoft.azure.sdk.iot.device.*;
    import com.microsoft.azure.sdk.iot.device.DeviceTwin.*;

    import java.io.IOException;
    import java.net.URISyntaxException;
    import java.util.Scanner;
    ```

9. 次のクラスレベル変数を **App** クラスに追加します。 `{yourdeviceconnectionstring}` を、「[IoT ハブに新しいデバイスを登録する](#register-a-new-device-in-the-iot-hub)」セクションでコピーしたデバイス接続文字列に置き換えます。

    ```java
    private static String connString = "{yourdeviceconnectionstring}";
    private static IotHubClientProtocol protocol = IotHubClientProtocol.MQTT;
    private static final int METHOD_SUCCESS = 200;
    private static final int METHOD_NOT_DEFINED = 404;
    ```

    このサンプル アプリでは、**DeviceClient** オブジェクトをインスタンス化するときに **protocol** 変数が使用されます。

10. デバイス ツイン通知をコンソールに出力するには、次の入れ子になったクラスを **App** クラスに追加します。

    ```java
    // Handler for device twin operation notifications from IoT Hub
    protected static class DeviceTwinStatusCallBack implements IotHubEventCallback {
      public void execute(IotHubStatusCode status, Object context) {
        System.out.println("IoT Hub responded to device twin operation with status " + status.name());
      }
    }
    ```

11. ダイレクト メソッド通知をコンソールに出力するには、次の入れ子になったクラスを **App** クラスに追加します。

    ```java
    // Handler for direct method notifications from IoT Hub
    protected static class DirectMethodStatusCallback implements IotHubEventCallback {
      public void execute(IotHubStatusCode status, Object context) {
        System.out.println("IoT Hub responded to direct method operation with status " + status.name());
      }
    }
    ```

12. IoT Hub からのダイレクト メソッド呼び出しを処理するには、次の入れ子になったクラスを **App** クラスに追加します。

    ```java
    // Handler for direct method calls from IoT Hub
    protected static class DirectMethodCallback
        implements DeviceMethodCallback {
      @Override
      public DeviceMethodData call(String methodName, Object methodData, Object context) {
        DeviceMethodData deviceMethodData;
        switch (methodName) {
          case "lockDoor": {
            System.out.println("Executing direct method: " + methodName);
            deviceMethodData = new DeviceMethodData(METHOD_SUCCESS, "Executed direct method " + methodName);
            break;
          }
          default: {
            deviceMethodData = new DeviceMethodData(METHOD_NOT_DEFINED, "Not defined direct method " + methodName);
          }
        }
        // Notify IoT Hub of result
        return deviceMethodData;
      }
    }
    ```

13. **main** メソッドのシグネチャを、次の `throws` 句を含むように更新します。

    ```java
    public static void main( String[] args ) throws IOException, URISyntaxException
    ```

14. **main** メソッドのコードを次のコードに置き換えます。
    * IoT Hub と通信するデバイス クライアントを作成します。
    * デバイス ツインのプロパティを格納する **Device** オブジェクトを作成します。

    ```java
    // Create a device client
    DeviceClient client = new DeviceClient(connString, protocol);

    // An object to manage device twin desired and reported properties
    Device dataCollector = new Device() {
      @Override
      public void PropertyCall(String propertyKey, Object propertyValue, Object context)
      {
        System.out.println("Received desired property change: " + propertyKey + " " + propertyValue);
      }
    };
    ```

15. デバイス クライアント サービスを開始するには、次のコードを **main** メソッドに追加します。

    ```java
    try {
      // Open the DeviceClient
      // Start the device twin services
      // Subscribe to direct method calls
      client.open();
      client.startDeviceTwin(new DeviceTwinStatusCallBack(), null, dataCollector, null);
      client.subscribeToDeviceMethod(new DirectMethodCallback(), null, new DirectMethodStatusCallback(), null);
    } catch (Exception e) {
      System.out.println("Exception, shutting down \n" + " Cause: " + e.getCause() + " \n" + e.getMessage());
      dataCollector.clean();
      client.closeNow();
      System.out.println("Shutting down...");
    }
    ```

16. ユーザーが **Enter** キーを押してからシャット ダウンするには、次のコードを **main** メソッドの末尾に追加します。

    ```java
    // Close the app
    System.out.println("Press any key to exit...");
    Scanner scanner = new Scanner(System.in);
    scanner.nextLine();
    dataCollector.clean();
    client.closeNow();
    scanner.close();
    ```

17. **simulated-device\src\main\java\com\mycompany\app\App.java** ファイルを保存して閉じます。

18. **simulated-device** アプリをビルドし、エラーを修正します。 コマンド プロンプトで **simulated-device** フォルダーに移動し、次のコマンドを実行します。

    ```cmd/sh
    mvn clean package -DskipTests
    ```

## <a name="run-the-apps"></a>アプリの実行

これで、コンソール アプリを実行する準備が整いました。

1. **simulated-device** フォルダーのコマンド プロンプトで、次のコマンドを実行して、必要なプロパティの変更とダイレクト メソッド呼び出しをリッスンするデバイス アプリを開始します。

   ```cmd/sh
   mvn exec:java -Dexec.mainClass="com.mycompany.app.App"
   ```

   ![デバイス クライアントが開始する](./media/iot-hub-java-java-schedule-jobs/device-app-1.png)

2. `schedule-jobs` フォルダーのコマンド プロンプトで、次のコマンドによって **schedule-jobs** サービス アプリを実行し、2 つのジョブを実行します。 最初のジョブで必要なプロパティの値が設定され、2 つ目のジョブでダイレクト メソッドが呼び出されます。

   ```cmd\sh
   mvn exec:java -Dexec.mainClass="com.mycompany.app.App"
   ```

   ![Java IoT Hub サービス アプリで 2 つのジョブが作成される](./media/iot-hub-java-java-schedule-jobs/service-app-1.png)

3. デバイス アプリは必要なプロパティの変更とダイレクト メソッド呼び出しを処理します。

   ![デバイス クライアントは変更に応答する](./media/iot-hub-java-java-schedule-jobs/device-app-2.png)

## <a name="next-steps"></a>次のステップ

このチュートリアルでは、ジョブを使用して、デバイスへのダイレクト メソッドと、デバイス ツインのプロパティの更新をスケジュールしました。

詳細については、次のリソースをご覧ください。

* [IoT Hub の概要](quickstart-send-telemetry-java.md)に関するチュートリアルでデバイスからテレメトリを送信する。

* デバイスを対話形式で制御する (ユーザー制御アプリからファンをオンにするなど): [ダイレクト メソッドの使用](quickstart-control-device-java.md)に関するチュートリアル。