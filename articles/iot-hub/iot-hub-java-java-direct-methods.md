---
title: "Azure IoT Hub のダイレクト メソッドの使用 (Java) | Microsoft Docs"
description: "Azure IoT Hub のダイレクト メソッドの使用方法。 Azure IoT device SDK for Java を使用して、ダイレクト メソッドを含むシミュレートされたデバイス アプリを実装し、Azure IoT service SDK for Java を使用して、ダイレクト メソッドを呼び出すサービス アプリを実装します。"
services: iot-hub
documentationcenter: 
author: dominicbetts
manager: timlt
editor: 
ms.assetid: ab035b8e-bff8-4e12-9536-f31d6b6fe425
ms.service: iot-hub
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/08/2017
ms.author: dobett
ms.translationtype: HT
ms.sourcegitcommit: f9003c65d1818952c6a019f81080d595791f63bf
ms.openlocfilehash: 5fa42c4fe7ad04bc74f70b023715bb61f81806ab
ms.contentlocale: ja-jp
ms.lasthandoff: 08/09/2017

---
# <a name="use-direct-methods-java"></a>ダイレクト メソッドの使用 (Java)

[!INCLUDE [iot-hub-selector-c2d-methods](../../includes/iot-hub-selector-c2d-methods.md)]

このチュートリアルでは、次の 2 つの Java コンソール アプリを作成します。

* **invoke-direct-method**。Java バックエンド アプリ。シミュレートされたデバイス アプリでメソッドを呼び出し、応答を表示します。
* **simulated-device**。Java アプリ。作成したデバイス ID を使用して IoT Hub に接続するデバイスをシミュレートします。 このアプリはバックエンドから呼び出されるダイレクトに応答します。

> [!NOTE]
> デバイス上で動作するアプリケーションの作成とソリューションのバックエンドで動作するアプリケーションの開発に利用できる各種 SDK については、「[Azure IoT SDK][lnk-hub-sdks]」をご覧ください。

このチュートリアルを完了するには、次のものが必要です。

* Java SE 8。 <br/> 「[Prepare your development environment (開発環境を準備する)][lnk-dev-setup]」では、このチュートリアルのために Java を Windows または Linux にインストールする方法が説明されています。
* Maven 3。  <br/> [開発環境の準備][lnk-dev-setup]に関するページでは、このチュートリアル用に [Maven][lnk-maven] を Windows または Linux にインストールする方法が説明されています。
* [Node.js のバージョン 0.10.0 以降](http://nodejs.org)。

[!INCLUDE [iot-hub-get-started-create-hub](../../includes/iot-hub-get-started-create-hub.md)]

[!INCLUDE [iot-hub-get-started-create-device-identity](../../includes/iot-hub-get-started-create-device-identity.md)]

## <a name="create-a-simulated-device-app"></a>シミュレーション対象デバイス アプリの作成

このセクションでは、ソリューション バックエンドによって呼び出されたメソッドに応答する Java コンソール アプリを作成します。

1. "iot-java-direct-method" という名前の空のフォルダーを作成します。

1. コマンド プロンプトで次のコマンドを実行し、iot-java-direct-method フォルダーに **simulated-device** という名前の Maven プロジェクトを作成します。 1 つの長いコマンドを次に示します。

    `mvn archetype:generate -DgroupId=com.mycompany.app -DartifactId=simulated-device -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false`

1. コマンド プロンプトで、simulated-device フォルダーに移動します。

1. テキスト エディターを使用して、simulated-device フォルダー内の pom.xml ファイルを開き、次の依存関係を **dependencies** ノードに追加します。 この依存関係により、アプリの iot-device-client パッケージを使用して IoT Hub と通信できるようになります。

    ```xml
    <dependency>
      <groupId>com.microsoft.azure.sdk.iot</groupId>
      <artifactId>iot-device-client</artifactId>
      <version>1.3.32</version>
    </dependency>
    ```

    > [!NOTE]
    > [Maven 検索][lnk-maven-device-search]を使用して、**iot-device-client** の最新バージョンを確認できます。

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

1. pom.xml ファイルを保存して閉じます。

1. テキスト エディターを使用し、simulated-device\src\main\java\com\mycompany\app\App.java ファイルを開きます。

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
    private static String deviceId = "myDeviceId";
    private static final int METHOD_SUCCESS = 200;
    private static final int METHOD_NOT_DEFINED = 404;
    ```

    このサンプル アプリでは、**DeviceClient** オブジェクトをインスタンス化するときに **protocol** 変数が使用されます。 現時点では、ダイレクト メソッドを使用するには、MQTT プロトコルを使用する必要があります。

1. IoT Hub に状態コードを返すには、次の入れ子になったクラスを **App** クラスに追加します。

    ```java
    protected static class DirectMethodStatusCallback implements IotHubEventCallback
    {
      public void execute(IotHubStatusCode status, Object context)
      {
        System.out.println("IoT Hub responded to device method operation with status " + status.name());
      }
    }
    ```

1. ソリューション バック エンドからのダイレクト メソッドの呼び出しを処理するには、次の入れ子になったクラスを **App** クラスに追加します。

    ```java
    protected static class DirectMethodCallback implements com.microsoft.azure.sdk.iot.device.DeviceTwin.DeviceMethodCallback
    {
      @Override
      public DeviceMethodData call(String methodName, Object methodData, Object context)
      {
        DeviceMethodData deviceMethodData;
        switch (methodName)
        {
          case "writeLine" :
          {
            int status = METHOD_SUCCESS;
            System.out.println(new String((byte[])methodData));
            deviceMethodData = new DeviceMethodData(status, "Executed direct method " + methodName);
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

1. **DeviceClient** を作成し、ダイレクト メソッドの呼び出しをリッスンするには、**main** メソッドを **App** クラスに追加します。

    ```java
    public static void main(String[] args)
      throws IOException, URISyntaxException
    {
      System.out.println("Starting device sample...");

      DeviceClient client = new DeviceClient(connString, protocol);

      try
      {
        client.open();
        client.subscribeToDeviceMethod(new DirectMethodCallback(), null, new DirectMethodStatusCallback(), null);
        System.out.println("Subscribed to direct methods. Waiting...");
      }
      catch (Exception e)
      {
        System.out.println("On exception, shutting down \n" + " Cause: " + e.getCause() + " \n" +  e.getMessage());
        client.close();
        System.out.println("Shutting down...");
      }

      System.out.println("Press any key to exit...");
      Scanner scanner = new Scanner(System.in);
      scanner.nextLine();
      scanner.close();
      client.close();
      System.out.println("Shutting down...");
    }
    ```

1. simulated-device\src\main\java\com\mycompany\app\App.java ファイルを保存して閉じます。

1. **simulated-device** アプリをビルドし、エラーを修正します。 コマンド プロンプトで simulated-device フォルダーに移動し、次のコマンドを実行します。

    `mvn clean package -DskipTests`

## <a name="call-a-direct-method-on-a-device"></a>デバイスのダイレクト メソッドを呼び出す

このセクションでは、ダイレクト メソッドを呼び出し、応答を表示する Java コンソール アプリを作成します。 このコンソール アプリは IoT Hub に接続して、ダイレクト メソッドを呼び出します。

1. コマンド プロンプトで次のコマンドを実行し、iot-java-direct-method フォルダーに **invoke-direct-method** という名前の Maven プロジェクトを作成します。 1 つの長いコマンドを次に示します。

    `mvn archetype:generate -DgroupId=com.mycompany.app -DartifactId=invoke-direct-method -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false`

1. コマンド プロンプトで、invoke-direct-method フォルダーに移動します。

1. テキスト エディターを使用して、invoke-direct-method フォルダー内の pom.xml ファイルを開き、次の依存関係を **dependencies** ノードに追加します。 この依存関係により、アプリの iot-service-client パッケージを使用して IoT Hub と通信できるようになります。

    ```xml
    <dependency>
      <groupId>com.microsoft.azure.sdk.iot</groupId>
      <artifactId>iot-service-client</artifactId>
      <version>1.7.23</version>
      <type>jar</type>
    </dependency>
    ```

    > [!NOTE]
    > [Maven 検索][lnk-maven-service-search]を使用して、**iot-service-client** の最新バージョンを確認できます。

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

1. pom.xml ファイルを保存して閉じます。

1. テキスト エディターを使用し、invoke-direct-method\src\main\java\com\mycompany\app\App.java ファイルを開きます。

1. ファイルに次の **import** ステートメントを追加します。

    ```java
    import com.microsoft.azure.sdk.iot.service.devicetwin.DeviceMethod;
    import com.microsoft.azure.sdk.iot.service.devicetwin.MethodResult;
    import com.microsoft.azure.sdk.iot.service.exceptions.IotHubException;

    import java.io.IOException;
    import java.util.concurrent.TimeUnit;
    ```

1. 次のクラスレベル変数を **App** クラスに追加します。 `{youriothubconnectionstring}` を、「*IoT Hub の作成*」セクションで書き留めた IoT Hub 接続文字列で置換します。

    ```java
    public static final String iotHubConnectionString = "{youriothubconnectionstring}";
    public static final String deviceId = "myDeviceId";

    public static final String methodName = "writeLine";
    public static final Long responseTimeout = TimeUnit.SECONDS.toSeconds(30);
    public static final Long connectTimeout = TimeUnit.SECONDS.toSeconds(5);
    public static final String payload = "a line to be written";
    ```

1. シミュレートされたデバイス上のメソッドを呼び出すには、次のコードを **main** メソッドに追加します。

    ```java
    System.out.println("Starting sample...");
    DeviceMethod methodClient = DeviceMethod.createFromConnectionString(iotHubConnectionString);

    try
    {
        System.out.println("Invoke direct method");
        MethodResult result = methodClient.invoke(deviceId, methodName, responseTimeout, connectTimeout, payload);

        if(result == null)
        {
            throw new IOException("Direct method invoke returns null");
        }
        System.out.println("Status=" + result.getStatus());
        System.out.println("Payload=" + result.getPayload());
    }
    catch (IotHubException e)
    {
        System.out.println(e.getMessage());
    }

    System.out.println("Shutting down sample...");
    ```

1. invoke-direct-method\src\main\java\com\mycompany\app\App.java ファイルを保存して閉じます。

1. **invoke-direct-method** アプリを構築して、エラーを修正します。 コマンド プロンプトで invoke-direct-method フォルダーに移動し、次のコマンドを実行します。

    `mvn clean package -DskipTests`

## <a name="run-the-apps"></a>アプリの実行

これで、コンソール アプリを実行する準備が整いました。

1. simulated-device フォルダーで、コマンド プロンプトから次のコマンドを実行し、IoT Hub からのメソッド呼び出しのリッスンを開始します。

    `mvn exec:java -Dexec.mainClass="com.mycompany.app.App"`

    ![ダイレクト メソッドの呼び出しをリッスンする Java IoT Hub シミュレートされたデバイス アプリ][8]

1. invoke-direct-method フォルダーで、コマンド プロンプトから次のコマンドを実行し、シミュレートされたデバイス上のメソッドを IoT Hub から呼び出します。

    `mvn exec:java -Dexec.mainClass="com.mycompany.app.App"`

    ![ダイレクト メソッドを呼び出す Java IoT Hub サービス アプリ][7]

1. シミュレートされたデバイスは、ダイレクト メソッドの次の呼び出しに応答します。

    ![ダイレクト メソッドの呼び出しに応答する Java IoT Hub シミュレートされたデバイス アプリ][9]

## <a name="next-steps"></a>次のステップ

このチュートリアルでは、Azure Portal で新しい IoT Hub を構成し、IoT Hub の ID レジストリにデバイス ID を作成しました。 シミュレーション対象デバイス アプリでクラウドから呼び出したメソッドに対応できるようにするために、このデバイス ID を使用しました。 また、デバイスでメソッドを呼び出し、デバイスからの応答を表示するアプリも作成しました。

他の IoT シナリオを検証するには、「[複数デバイスでのジョブをスケジュール設定する][lnk-devguide-jobs]」を参照してください。

IoT ソリューションの拡張と複数のデバイスでのメソッドの呼び出しをスケジュールする方法については、「[ジョブのスケジュールとブロードキャスト][lnk-tutorial-jobs]」チュートリアルを参照してください。

<!-- Images. -->
[7]: ./media/iot-hub-java-java-direct-methods/invoke-method.png
[8]: ./media/iot-hub-java-java-direct-methods/device-listen.png
[9]: ./media/iot-hub-java-java-direct-methods/device-respond.png

<!-- Links -->
[lnk-dev-setup]: https://github.com/Azure/azure-iot-sdk-java/blob/master/doc/java-devbox-setup.md
[lnk-maven]: https://maven.apache.org/what-is-maven.html
[lnk-hub-sdks]: iot-hub-devguide-sdks.md

[lnk-devguide-jobs]: iot-hub-devguide-jobs.md
[lnk-tutorial-jobs]: iot-hub-node-node-schedule-jobs.md
[lnk-maven-service-search]: http://search.maven.org/#search%7Cga%7C1%7Ca%3A%22iot-service-client%22%20g%3A%22com.microsoft.azure.sdk.iot%22
[lnk-maven-device-search]: http://search.maven.org/#search%7Cga%7C1%7Ca%3A%22iot-device-client%22%20g%3A%22com.microsoft.azure.sdk.iot%22

