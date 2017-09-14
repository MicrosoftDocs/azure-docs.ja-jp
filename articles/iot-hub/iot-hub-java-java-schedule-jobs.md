---
title: "Azure IoT Hub を使用してジョブのスケジュールを設定する (Java) | Microsoft Docs"
description: "複数のデバイスでダイレクト メソッドを呼び出して、必要なプロパティを設定するように Azure IoT Hub ジョブのスケジュールを設定する方法。 Azure IoT device SDK for Java を使用して、シミュレートされたデバイス アプリを実装し、Azure IoT service SDK for Java を使用して、ジョブを実行するサービス アプリを実装します。"
services: iot-hub
documentationcenter: java
author: dominicbetts
manager: timlt
editor: 
ms.service: iot-hub
ms.devlang: java
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/10/2017
ms.author: dobett
ms.translationtype: HT
ms.sourcegitcommit: 5b6c261c3439e33f4d16750e73618c72db4bcd7d
ms.openlocfilehash: 003a548ef2da2921a699df1aa9f7aee366d341ab
ms.contentlocale: ja-jp
ms.lasthandoff: 08/28/2017

---
# <a name="schedule-and-broadcast-jobs-java"></a>ジョブのスケジュールとブロードキャスト (Java)

[!INCLUDE [iot-hub-selector-schedule-jobs](../../includes/iot-hub-selector-schedule-jobs.md)]

Azure IoT Hub を使用して、数百万のデバイスを更新するジョブのスケジュールと追跡を行います。 ジョブを使用して、次の操作を行います。

* 必要なプロパティを更新する
* タグを更新する
* ダイレクト メソッドを呼び出す

ジョブはこれらのアクションの 1 つをラップし、デバイスのセットに対して実行状況を追跡します。 ジョブが実行されるデバイスのセットはデバイス ツイン クエリで定義されます。 たとえば、バックエンド アプリは、ジョブを使用して 10,000 台のデバイスでダイレクト メソッドを呼び出し、デバイスを再起動できます。 デバイス ツイン クエリでデバイスのセットを指定し、ジョブが将来実行されるようにスケジュールを設定します。 各デバイスが再起動のダイレクト メソッドを受信し、実行するたびに、ジョブは進行状況を追跡します。

これらの各機能の詳細については、次の記事をご覧ください。

* デバイス ツインとプロパティ: [デバイス ツインの概要](iot-hub-java-java-twin-getstarted.md)
* ダイレクト メソッド: [ダイレクト メソッドに関する IoT Hub 開発者ガイド](iot-hub-devguide-direct-methods.md)と[ダイレクト メソッドの使用に関するチュートリアル](iot-hub-java-java-direct-methods.md)

このチュートリアルでは、次の操作方法について説明します。

* **lockDoor** と呼ばれるダイレクト メソッドを実装するデバイス アプリの作成。 デバイス アプリは、バックエンド アプリから必要なプロパティの変更も受信します。
* 複数のデバイスで **lockDoor** ダイレクト メソッドを呼び出すジョブを作成するバックエンド アプリの作成。 もう 1 つのジョブは、必要なプロパティの更新を複数のデバイスに送信します。

このチュートリアルが終わると、次の java コンソール デバイス アプリと java コンソール バックエンド アプリが完成しています。

**simulated-device** は IoT Hub に接続して、**lockDoor** ダイレクト メソッドを実装し、必要なプロパティの変更を処理します。

**schedule-jobs** はジョブを使用して **lockDoor** ダイレクト メソッドを呼び出し、複数のデバイスでデバイス ツインの必要なプロパティを更新します。

> [!NOTE]
> デバイス アプリとバックエンド アプリの両方をビルドするために使用できる Azure IoT SDK については、記事「[Azure IoT SDK](iot-hub-devguide-sdks.md)」を参照してください。

## <a name="prerequisites"></a>前提条件

このチュートリアルを完了するには、次のものが必要です。

* 最新の [Java SE Development Kit 8](http://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html)
* [Maven 3](https://maven.apache.org/install.html)
* アクティブな Azure アカウント  (アカウントがない場合は、[無料アカウント](http://azure.microsoft.com/pricing/free-trial/) を数分で作成できます)。

[!INCLUDE [iot-hub-get-started-create-hub](../../includes/iot-hub-get-started-create-hub.md)]

[!INCLUDE [iot-hub-get-started-create-device-identity-portal](../../includes/iot-hub-get-started-create-device-identity-portal.md)]

デバイス ID をプログラムで作成する場合は、記事「[Java を使用してデバイスを IoT Hub に接続する](iot-hub-java-java-getstarted.md#create-a-device-identity)」の該当セクションを参照してください。 [iothub-explorer](https://github.com/Azure/iothub-explorer) ツールを使用して、デバイスを IoT Hub に追加することもできます。

## <a name="create-the-service-app"></a>サービス アプリを作成する

このセクションでは、ジョブを使用する Java コンソール アプリを作成して、次の操作を行います。

* 複数のデバイスで **lockDoor** ダイレクト メソッドを呼び出す。
* 必要なプロパティを複数のデバイスに送信する。

アプリを作成するには:

1. 開発用コンピューターで、`iot-java-schedule-jobs` という名前の空のフォルダーを作成します。

1. コマンド プロンプトで次のコマンドを使用して、`iot-java-schedule-jobs` フォルダー内に **schedule-jobs** という名前の Maven プロジェクトを作成します。 これは、1 つの長いコマンドであることに注意してください。

    `mvn archetype:generate -DgroupId=com.mycompany.app -DartifactId=schedule-jobs -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false`

1. コマンド プロンプトで、`schedule-jobs` フォルダーに移動します。

1. テキスト エディターを使用して、`schedule-jobs` フォルダー内の `pom.xml` ファイルを開き、次の依存関係を **dependencies** ノードに追加します。 この依存関係により、アプリで **iot-service-client** パッケージを使用して IoT Hub と通信できるようになります。

    ```xml
    <dependency>
      <groupId>com.microsoft.azure.sdk.iot</groupId>
      <artifactId>iot-service-client</artifactId>
      <version>1.7.23</version>
      <type>jar</type>
    </dependency>
    ```

    > [!NOTE]
    > [Maven 検索](http://search.maven.org/#search%7Cga%7C1%7Ca%3A%22iot-service-client%22%20g%3A%22com.microsoft.azure.sdk.iot%22)を使用して、**iot-service-client** の最新バージョンを確認できます。

1. **dependencies** ノードの後に、次の **build** ノードを追加します。 この構成では、Java 1.8 を使用してアプリをビルドするように Maven に指示しています。

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

1. `pom.xml` ファイルを保存して閉じます。

1. テキスト エディターで、`schedule-jobs\src\main\java\com\mycompany\app\App.java` ファイルを開きます。

1. ファイルに次の **import** ステートメントを追加します。

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

1. 次のクラスレベル変数を **App** クラスに追加します。 `{youriothubconnectionstring}` を、「*IoT Hub の作成*」セクションで書き留めた IoT Hub 接続文字列で置換します。

    ```java
    public static final String iotHubConnectionString = "{youriothubconnectionstring}";
    public static final String deviceId = "myDeviceId";

    // How long the job is permitted to run without
    // completing its work on the set of devices
    private static final long maxExecutionTimeInSeconds = 30;
    ```

1. デバイス ツインで次のメソッドを **App** クラスに追加して、必要なプロパティ **Building** および **Floor** を更新するようにジョブのスケジュールを設定します。

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

1. **lockDoor** メソッドを呼び出すようにジョブのスケジュールを設定するには、次のメソッドを **App** クラスに追加します。

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

1. ジョブを監視するには、次のメソッドを **App** クラスに追加します。

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

1. 実行したジョブの詳細に関するクエリを実行するには、次のメソッドを追加します。

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

1. **main** メソッドのシグネチャを、次の `throws` 句を含むように更新します。

    ```java
    public static void main( String[] args ) throws Exception
    ```

1. 2 つのジョブの順番に実行して監視するには、次のコードを **main** メソッドに追加します。

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

1. `schedule-jobs\src\main\java\com\mycompany\app\App.java` ファイルを保存して閉じます。

1. **schedule-jobs** アプリをビルドし、エラーを修正します。 コマンド プロンプトで `schedule-jobs` フォルダーに移動し、次のコマンドを実行します。

    `mvn clean package -DskipTests`

## <a name="create-a-device-app"></a>デバイス アプリの作成

このセクションでは、IoT Hub から送信された必要なプロパティを処理する Java コンソール アプリを作成し、ダイレクト メソッド呼び出しを実装します。

1. コマンド プロンプトで次のコマンドを実行して、`iot-java-schedule-jobs` フォルダー内に **simulated-device** という名前の Maven プロジェクトを作成します。 これは、1 つの長いコマンドであることに注意してください。

    `mvn archetype:generate -DgroupId=com.mycompany.app -DartifactId=simulated-device -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false`

1. コマンド プロンプトで、`simulated-device` フォルダーに移動します。

1. テキスト エディターを使用して、`simulated-device` フォルダー内の `pom.xml` ファイルを開き、次の依存関係を **dependencies** ノードに追加します。 この依存関係により、アプリで **iot-device-client** パッケージを使用して IoT Hub と通信できるようになります。

    ```xml
    <dependency>
      <groupId>com.microsoft.azure.sdk.iot</groupId>
      <artifactId>iot-device-client</artifactId>
      <version>1.3.32</version>
    </dependency>
    ```

    > [!NOTE]
    > [Maven 検索](http://search.maven.org/#search%7Cga%7C1%7Ca%3A%22iot-device-client%22%20g%3A%22com.microsoft.azure.sdk.iot%22)を使用して、**iot-device-client** の最新バージョンを確認できます。

1. **dependencies** ノードの後に、次の **build** ノードを追加します。 この構成では、Java 1.8 を使用してアプリをビルドするように Maven に指示しています。

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

1. `pom.xml` ファイルを保存して閉じます。

1. テキスト エディターで、`simulated-device\src\main\java\com\mycompany\app\App.java` ファイルを開きます。

1. ファイルに次の **import** ステートメントを追加します。

    ```java
    import com.microsoft.azure.sdk.iot.device.*;
    import com.microsoft.azure.sdk.iot.device.DeviceTwin.*;

    import java.io.IOException;
    import java.net.URISyntaxException;
    import java.util.Scanner;
    ```

1. 次のクラスレベル変数を **App** クラスに追加します。 `{youriothubname}` を IoT Hub 名に置き換え、`{yourdevicekey}` を「*デバイス ID の作成*」セクションで生成したデバイス キーの値に置き換えます。

    ```java
    private static String connString = "HostName={youriothubname}.azure-devices.net;DeviceId=myDeviceID;SharedAccessKey={yourdevicekey}";
    private static IotHubClientProtocol protocol = IotHubClientProtocol.MQTT;
    private static final int METHOD_SUCCESS = 200;
    private static final int METHOD_NOT_DEFINED = 404;
    ```

    このサンプル アプリでは、**DeviceClient** オブジェクトをインスタンス化するときに **protocol** 変数が使用されます。 現時点では、デバイス ツインの機能を使用するには、MQTT プロトコルを使用する必要があります。

1. デバイス ツイン通知をコンソールに出力するには、次の入れ子になったクラスを **App** クラスに追加します。

    ```java
    // Handler for device twin operation notifications from IoT Hub
    protected static class DeviceTwinStatusCallBack implements IotHubEventCallback {
      public void execute(IotHubStatusCode status, Object context) {
        System.out.println("IoT Hub responded to device twin operation with status " + status.name());
      }
    }
    ```

1. ダイレクト メソッド通知をコンソールに出力するには、次の入れ子になったクラスを **App** クラスに追加します。

    ```java
    // Handler for direct method notifications from IoT Hub
    protected static class DirectMethodStatusCallback implements IotHubEventCallback {
      public void execute(IotHubStatusCode status, Object context) {
        System.out.println("IoT Hub responded to direct method operation with status " + status.name());
      }
    }
    ```

1. IoT Hub からのダイレクト メソッド呼び出しを処理するには、次の入れ子になったクラスを **App** クラスに追加します。

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

1. **main** メソッドのシグネチャを、次の `throws` 句を含むように更新します。

    ```java
    public static void main( String[] args ) throws IOException, URISyntaxException
    ```

1. 次の操作を行うコードを **main** メソッドに追加します。
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

1. デバイス クライアント サービスを開始するには、次のコードを **main** メソッドに追加します。

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

1. ユーザーが **Enter** キーを押してからシャット ダウンするには、次のコードを **main** メソッドの末尾に追加します。

    ```java
    // Close the app
    System.out.println("Press any key to exit...");
    Scanner scanner = new Scanner(System.in);
    scanner.nextLine();
    dataCollector.clean();
    client.closeNow();
    scanner.close();
    ```

1. `simulated-device\src\main\java\com\mycompany\app\App.java` ファイルを保存して閉じます。

1. **simulated-device** アプリをビルドし、エラーを修正します。 コマンド プロンプトで `simulated-device` フォルダーに移動し、次のコマンドを実行します。

    `mvn clean package -DskipTests`

## <a name="run-the-apps"></a>アプリの実行

これで、コンソール アプリを実行する準備が整いました。

1. `simulated-device` フォルダーのコマンド プロンプトで、次のコマンドを実行して、必要なプロパティの変更とダイレクト メソッド呼び出しをリッスンするデバイス アプリを開始します。

    `mvn exec:java -Dexec.mainClass="com.mycompany.app.App"`

    ![デバイス クライアントが開始する](media/iot-hub-java-java-schedule-jobs/device-app-1.png)

1. `schedule-jobs` フォルダーのコマンド プロンプトで、次のコマンドによって **schedule-jobs** サービス アプリを実行し、2 つのジョブを実行します。 最初のジョブで必要なプロパティの値が設定され、2 つ目のジョブでダイレクト メソッドが呼び出されます。

    `mvn exec:java -Dexec.mainClass="com.mycompany.app.App"`

    ![Java IoT Hub サービス アプリで 2 つのジョブが作成される](media/iot-hub-java-java-schedule-jobs/service-app-1.png)

1. デバイス アプリは必要なプロパティの変更とダイレクト メソッド呼び出しを処理します。

    ![デバイス クライアントは変更に応答する](media/iot-hub-java-java-schedule-jobs/device-app-2.png)

## <a name="next-steps"></a>次のステップ

このチュートリアルでは、Azure Portal で新しい IoT Hub を構成し、IoT Hub の ID レジストリにデバイス ID を作成しました。 また、バックエンド アプリを作成して、2 つのジョブを実行しました。 最初のジョブは必要なプロパティの値を設定し、2 つ目のジョブはダイレクト メソッドを呼び出しました。

詳細については、次のリソースをご覧ください。

* [IoT Hub の概要](iot-hub-java-java-getstarted.md)に関するチュートリアルでデバイスからテレメトリを送信する。
* 「[ダイレクト メソッドの使用](iot-hub-java-java-direct-methods.md)」チュートリアルで、デバイスを対話形式で制御する (ユーザー制御アプリからファンをオンにするなど)。

