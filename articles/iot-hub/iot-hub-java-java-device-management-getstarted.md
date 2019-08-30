---
title: Azure IoT Hub デバイス管理の開始 (Java) | Microsoft Docs
description: Azure IoT Hub デバイス管理を使用してリモート デバイスの再起動を開始する方法。 Azure IoT device SDK for Java を使用して、ダイレクト メソッドを含むシミュレートされたデバイス アプリを実装し、Azure IoT service SDK for Java を使用して、ダイレクト メソッドを呼び出すサービス アプリを実装します。
author: wesmc7777
manager: philmea
ms.author: wesmc
ms.service: iot-hub
services: iot-hub
ms.devlang: java
ms.topic: conceptual
ms.date: 08/08/2017
ms.openlocfilehash: 87e6f69c06fb9f8bc03d184cfe160964403e7a2a
ms.sourcegitcommit: 6cbf5cc35840a30a6b918cb3630af68f5a2beead
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/05/2019
ms.locfileid: "68780866"
---
# <a name="get-started-with-device-management-java"></a>デバイス管理の開始 (Java)

[!INCLUDE [iot-hub-selector-dm-getstarted](../../includes/iot-hub-selector-dm-getstarted.md)]

このチュートリアルでは、次の操作方法について説明します。

* Azure Portal を使用して IoT Hub を作成し、IoT Hub 内にデバイス ID を作成します。

* ダイレクト メソッドを実装して、デバイスを再起動するシミュレートされたデバイス アプリを作成します。 ダイレクト メソッドは、クラウドから呼び出されます。

* シミュレートされたデバイス アプリで　再起動ダイレクト メソッドを IoT Hub 経由で呼び出すアプリを作成します。 このアプリは、デバイスから報告されたプロパティを監視し、再起動操作が完了したときに確認します。

このチュートリアルの最後には、次の 2 つの Java コンソール アプリが完成します。

**simulated-device**. このアプリでは

* 以前に作成したデバイス ID を持つ IoT hub に接続します。

* 再起動ダイレクト メソッドの呼び出しを受け取ります。

* 物理的な再起動をシミュレートします。

* 報告されたプロパティを介して、最後の再起動の時間を報告します。

**trigger-reboot**. このアプリでは

* シミュレートされたデバイス アプリでダイレクト メソッドを呼び出します。

* シミュレートされたデバイスから送信されたダイレクト メソッド呼び出しへの応答を表示します。

* 更新済みの報告されたプロパティを表示します。

> [!NOTE]
> デバイス上で動作するアプリケーションの作成とソリューションのバックエンドで動作するアプリケーションの開発に利用できる各種 SDK については、「[Azure IoT SDK](iot-hub-devguide-sdks.md)」をご覧ください。

このチュートリアルを完了するには、次のものが必要です。

* Java SE 8。 <br/> 「[Prepare your development environment (開発環境を準備する)](https://github.com/Azure/azure-iot-sdk-java/blob/master/doc/java-devbox-setup.md)」では、このチュートリアルのために Java を Windows または Linux にインストールする方法が説明されています。

* Maven 3。  <br/> [開発環境の準備](https://github.com/Azure/azure-iot-sdk-java/blob/master/doc/java-devbox-setup.md)に関するページでは、このチュートリアル用に [Maven](https://maven.apache.org/what-is-maven.html) を Windows または Linux にインストールする方法が説明されています。

* アクティブな Azure アカウントアカウントがない場合、Azure 試用版にサインアップして、最大 10 件の無料 Mobile Apps を入手できます。 (アカウントがない場合は、[無料アカウント](https://azure.microsoft.com/pricing/free-trial/) を数分で作成できます)。

## <a name="create-an-iot-hub"></a>IoT Hub の作成

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="register-a-new-device-in-the-iot-hub"></a>IoT ハブに新しいデバイスを登録する

[!INCLUDE [iot-hub-get-started-create-device-identity](../../includes/iot-hub-get-started-create-device-identity.md)]

## <a name="get-the-iot-hub-connection-string"></a>IoT ハブ接続文字列を取得する

[!INCLUDE [iot-hub-howto-device-management-shared-access-policy-text](../../includes/iot-hub-howto-device-management-shared-access-policy-text.md)]

[!INCLUDE [iot-hub-include-find-service-connection-string](../../includes/iot-hub-include-find-service-connection-string.md)]

## <a name="trigger-a-remote-reboot-on-the-device-using-a-direct-method"></a>ダイレクト メソッドを使用してデバイスのリモート再起動をトリガーする

このセクションでは、次の操作を行う Java コンソール アプリを作成します。

1. シミュレートされたデバイス アプリで再起動ダイレクト メソッドを呼び出します。

2. 応答を表示します。

3. デバイスから送信された報告されたプロパティをポーリングして、再起動が完了したタイミングを判断します。

このコンソール アプリは IoT Hub に接続して、ダイレクト メソッドを呼び出し、報告されたプロパティを読み取ります。

1. dm-get-started という名前の空のフォルダーを作成します。

2. コマンド プロンプトで次のコマンドを実行し、dm-get-started フォルダーに **trigger-reboot** という名前の Maven プロジェクトを作成します。 1 つの長いコマンドを次に示します。

    `mvn archetype:generate -DgroupId=com.mycompany.app -DartifactId=trigger-reboot -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false`

3. コマンド プロンプトで、trigger-reboot フォルダーに移動します。

4. テキスト エディターを使用して、trigger-reboot フォルダー内の pom.xml ファイルを開き、次の依存関係を **dependencies** ノードに追加します。 この依存関係により、アプリの iot-service-client パッケージを使用して IoT Hub と通信できるようになります。

    ```xml
    <dependency>
      <groupId>com.microsoft.azure.sdk.iot</groupId>
      <artifactId>iot-service-client</artifactId>
      <version>1.7.23</version>
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

6. pom.xml ファイルを保存して閉じます。

7. テキスト エディターを使用して、trigger-reboot\src\main\java\com\mycompany\app\App.java ソース ファイルを開きます。

8. ファイルに次の **import** ステートメントを追加します。

    ```java
    import com.microsoft.azure.sdk.iot.service.devicetwin.DeviceMethod;
    import com.microsoft.azure.sdk.iot.service.devicetwin.MethodResult;
    import com.microsoft.azure.sdk.iot.service.exceptions.IotHubException;
    import com.microsoft.azure.sdk.iot.service.devicetwin.DeviceTwin;
    import com.microsoft.azure.sdk.iot.service.devicetwin.DeviceTwinDevice;

    import java.io.IOException;
    import java.util.concurrent.TimeUnit;
    import java.util.concurrent.Executors;
    import java.util.concurrent.ExecutorService;
    ```

9. 次のクラスレベル変数を **App** クラスに追加します。 `{youriothubconnectionstring}` は、先ほど「[IoT ハブ接続文字列を取得する](#get-the-iot-hub-connection-string)」でコピーしておいた IoT Hub 接続文字列に置き換えてください。

    ```java
    public static final String iotHubConnectionString = "{youriothubconnectionstring}";
    public static final String deviceId = "myDeviceId";

    private static final String methodName = "reboot";
    private static final Long responseTimeout = TimeUnit.SECONDS.toSeconds(30);
    private static final Long connectTimeout = TimeUnit.SECONDS.toSeconds(5);
    ```

10. 10 秒ごとにデバイス ツインから報告されたプロパティを読み取るスレッドを実装するには、次の入れ子になったクラスを **App** クラスに追加します。

    ```java
    private static class ShowReportedProperties implements Runnable {
      public void run() {
        try {
          DeviceTwin deviceTwins = DeviceTwin.createFromConnectionString(iotHubConnectionString);
          DeviceTwinDevice twinDevice = new DeviceTwinDevice(deviceId);
          while (true) {
            System.out.println("Get reported properties from device twin");
            deviceTwins.getTwin(twinDevice);
            System.out.println(twinDevice.reportedPropertiesToString());
            Thread.sleep(10000);
          }
        } catch (Exception ex) {
          System.out.println("Exception reading reported properties: " + ex.getMessage());
        }
      }
    }
    ```

11. **main** メソッドのシグネチャを変更し、次の例外をスローします。

    ```java
    public static void main(String[] args) throws IOException
    ```

12. シミュレートされたデバイスで再起動ダイレクト メソッドを呼び出すには、**main** メソッドに次のコードを追加します。

    ```java
    System.out.println("Starting sample...");
    DeviceMethod methodClient = DeviceMethod.createFromConnectionString(iotHubConnectionString);

    try
    {
      System.out.println("Invoke reboot direct method");
      MethodResult result = methodClient.invoke(deviceId, methodName, responseTimeout, connectTimeout, null);

      if(result == null)
      {
        throw new IOException("Invoke direct method reboot returns null");
      }
      System.out.println("Invoked reboot on device");
      System.out.println("Status for device:   " + result.getStatus());
      System.out.println("Message from device: " + result.getPayload());
    }
    catch (IotHubException e)
    {
        System.out.println(e.getMessage());
    }
    ```

13. シミュレートされたデバイスから報告されたプロパティをポーリングするスレッドを開始するには、**main** メソッドに次のコードを追加します。

    ```java
    ShowReportedProperties showReportedProperties = new ShowReportedProperties();
    ExecutorService executor = Executors.newFixedThreadPool(1);
    executor.execute(showReportedProperties);
    ```

14. アプリを停止できるようにするには、**main** メソッドに次のコードを追加します。

    ```java
    System.out.println("Press ENTER to exit.");
    System.in.read();
    executor.shutdownNow();
    System.out.println("Shutting down sample...");
    ```

15. trigger-reboot\src\main\java\com\mycompany\app\App.java ファイルを保存して閉じます。

16. **trigger-reboot** バックエンド アプリをビルドし、エラーを修正します。 コマンド プロンプトで trigger-reboot フォルダーに移動し、次のコマンドを実行します。

    `mvn clean package -DskipTests`

## <a name="create-a-simulated-device-app"></a>シミュレート対象デバイス アプリの作成

このセクションでは、デバイスをシミュレートする Java コンソール アプリを作成します。 アプリは、IoT hub からの再起動ダイレクト メソッド呼び出しをリッスンし、その呼び出しに直ちに応答します。 次にアプリは、しばらくスリープし、再起動プロセスをシミュレートしてから、報告されたプロパティを使用して、**trigger-reboot** バックエンド アプリに、再起動が完了したことを通知します。

1. コマンド プロンプトで次のコマンドを実行し、dm-get-started フォルダーに **simulated-device** という名前の Maven プロジェクトを作成します。 1 つの長いコマンドを次に示します。

    `mvn archetype:generate -DgroupId=com.mycompany.app -DartifactId=simulated-device -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false`

2. コマンド プロンプトで、simulated-device フォルダーに移動します。

3. テキスト エディターを使用して、simulated-device フォルダー内の pom.xml ファイルを開き、次の依存関係を **dependencies** ノードに追加します。 この依存関係により、アプリの iot-service-client パッケージを使用して IoT Hub と通信できるようになります。

    ```xml
    <dependency>
      <groupId>com.microsoft.azure.sdk.iot</groupId>
      <artifactId>iot-device-client</artifactId>
      <version>1.3.32</version>
    </dependency>
    ```

    > [!NOTE]
    > [Maven 検索](https://search.maven.org/#search%7Cga%7C1%7Ca%3A%22iot-device-client%22%20g%3A%22com.microsoft.azure.sdk.iot%22)を使用して、**iot-device-client** の最新バージョンを確認できます。

4. **dependencies** ノードの後に、次の **build** ノードを追加します。 この構成では、Java 1.8 を使用してアプリをビルドするように Maven に指示しています。

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

5. pom.xml ファイルを保存して閉じます。

6. テキスト エディターを使用し、simulated-device\src\main\java\com\mycompany\app\App.java ソース ファイルを開きます。

7. ファイルに次の **import** ステートメントを追加します。

    ```java
    import com.microsoft.azure.sdk.iot.device.*;
    import com.microsoft.azure.sdk.iot.device.DeviceTwin.*;

    import java.io.IOException;
    import java.net.URISyntaxException;
    import java.time.LocalDateTime;
    import java.util.Scanner;
    import java.util.Set;
    import java.util.HashSet;
    ```

7. 次のクラスレベル変数を **App** クラスに追加します。 `{yourdeviceconnectionstring}` を「[IoT ハブに新しいデバイスを登録する](#register-a-new-device-in-the-iot-hub)」セクションで書き留めたデバイス接続文字列に置き換えます。

    ```java
    private static final int METHOD_SUCCESS = 200;
    private static final int METHOD_NOT_DEFINED = 404;

    private static IotHubClientProtocol protocol = IotHubClientProtocol.MQTT;
    private static String connString = "{yourdeviceconnectionstring}";
    private static DeviceClient client;
    ```

8. ダイレクト メソッド ステータス イベントのコールバック ハンドラーを実装するには、次の入れ子になったクラスを **App** クラスに追加します。

    ```java
    protected static class DirectMethodStatusCallback implements IotHubEventCallback
    {
      public void execute(IotHubStatusCode status, Object context)
      {
        System.out.println("IoT Hub responded to device method operation with status " + status.name());
      }
    }
    ```

9. デバイス ツイン ステータス イベントのコールバック ハンドラーを実装するには、次の入れ子になったクラスを **App** クラスに追加します。

    ```java
    protected static class DeviceTwinStatusCallback implements IotHubEventCallback
    {
        public void execute(IotHubStatusCode status, Object context)
        {
            System.out.println("IoT Hub responded to device twin operation with status " + status.name());
        }
    }
    ```

10. プロパティ イベントのコールバック ハンドラーを実装するには、次の入れ子になったクラスを **App** クラスに追加します。

    ```java
    protected static class PropertyCallback implements PropertyCallBack<String, String>
    {
      public void PropertyCall(String propertyKey, String propertyValue, Object context)
      {
        System.out.println("PropertyKey:     " + propertyKey);
        System.out.println("PropertyKvalue:  " + propertyKey);
      }
    }
    ```

11. デバイスの再起動をシミュレートするスレッドを実装するには、次の入れ子になったクラスを **App** クラスに追加します。 スレッドは 5 秒間スリープしてから、報告された **lastReboot** プロパティを設定します。

    ```java
    protected static class RebootDeviceThread implements Runnable {
      public void run() {
        try {
          System.out.println("Rebooting...");
          Thread.sleep(5000);
          Property property = new Property("lastReboot", LocalDateTime.now());
          Set<Property> properties = new HashSet<Property>();
          properties.add(property);
          client.sendReportedProperties(properties);
          System.out.println("Rebooted");
        }
        catch (Exception ex) {
          System.out.println("Exception in reboot thread: " + ex.getMessage());
        }
      }
    }
    ```

12. デバイスにダイレクト メソッドを実装するには、次の入れ子になったクラスを **App** クラスに追加します。 シミュレートされたアプリが、**reboot** ダイレクト メソッドへの呼び出しを受け取ると、呼び出し元に受信確認を返し、再起動を処理するスレッドを起動します。

    ```java
    protected static class DirectMethodCallback implements com.microsoft.azure.sdk.iot.device.DeviceTwin.DeviceMethodCallback
    {
      @Override
      public DeviceMethodData call(String methodName, Object methodData, Object context)
      {
        DeviceMethodData deviceMethodData;
        switch (methodName)
        {
          case "reboot" :
          {
            int status = METHOD_SUCCESS;
            System.out.println("Received reboot request");
            deviceMethodData = new DeviceMethodData(status, "Started reboot");
            RebootDeviceThread rebootThread = new RebootDeviceThread();
            Thread t = new Thread(rebootThread);
            t.start();
            break;
          }
          default:
          {
            int status = METHOD_NOT_DEFINED;
            deviceMethodData = new DeviceMethodData(status, "Not defined direct method " + methodName);
          }
        }
        return deviceMethodData;
      }
    }
    ```

13. **main** メソッドのシグネチャを変更し、次の例外をスローします。

    ```java
    public static void main(String[] args) throws IOException, URISyntaxException
    ```

14. **DeviceClient** をインスタンス化するには、**main** メソッドに次のコードを追加します。

    ```java
    System.out.println("Starting device client sample...");
    client = new DeviceClient(connString, protocol);
    ```

15. ダイレクト メソッド呼び出しのリッスンを開始するには、**main** メソッドに次のコードを追加します。

    ```java
    try
    {
      client.open();
      client.subscribeToDeviceMethod(new DirectMethodCallback(), null, new DirectMethodStatusCallback(), null);
      client.startDeviceTwin(new DeviceTwinStatusCallback(), null, new PropertyCallback(), null);
      System.out.println("Subscribed to direct methods and polling for reported properties. Waiting...");
    }
    catch (Exception e)
    {
      System.out.println("On exception, shutting down \n" + " Cause: " + e.getCause() + " \n" +  e.getMessage());
      client.close();
      System.out.println("Shutting down...");
    }
    ```

16. デバイス シミュレーターをシャットダウンするには、**main** メソッドに次のコードを追加します。

    ```java
    System.out.println("Press any key to exit...");
    Scanner scanner = new Scanner(System.in);
    scanner.nextLine();
    scanner.close();
    client.close();
    System.out.println("Shutting down...");
    ```

17. simulated-device\src\main\java\com\mycompany\app\App.java を保存して閉じます。

18. **simulated-device** バックエンド アプリをビルドし、エラーを修正します。 コマンド プロンプトで simulated-device フォルダーに移動し、次のコマンドを実行します。

    `mvn clean package -DskipTests`

## <a name="run-the-apps"></a>アプリの実行

これで、アプリを実行する準備が整いました。

1. simulated-device フォルダーで、コマンド プロンプトから次のコマンドを実行し、IoT Hub からの再起動メソッド呼び出しのリッスンを開始します。

    `mvn exec:java -Dexec.mainClass="com.mycompany.app.App"`

    ![再起動ダイレクト メソッドの呼び出しをリッスンする Java IoT Hub シミュレートされたデバイス アプリ](./media/iot-hub-java-java-device-management-getstarted/launchsimulator.png)

2. trigger-reboot フォルダーで、コマンド プロンプトから次のコマンドを実行し、シミュレートされたデバイス上の再起動メソッドを IoT Hub から呼び出します。

    `mvn exec:java -Dexec.mainClass="com.mycompany.app.App"`

    ![再起動ダイレクト メソッドを呼び出す Java IoT Hub サービス アプリ](./media/iot-hub-java-java-device-management-getstarted/triggerreboot.png)

3. シミュレートされたデバイスは、再起動 ダイレクト メソッドの次の呼び出しに応答します。

    ![ダイレクト メソッドの呼び出しに応答する Java IoT Hub シミュレートされたデバイス アプリ](./media/iot-hub-java-java-device-management-getstarted/respondtoreboot.png)

[!INCLUDE [iot-hub-dm-followup](../../includes/iot-hub-dm-followup.md)]
