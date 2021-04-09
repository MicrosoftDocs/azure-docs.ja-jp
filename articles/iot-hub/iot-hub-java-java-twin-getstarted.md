---
title: Azure IoT Hub デバイス ツインの使用 (Java) | Microsoft Docs
description: Azure IoT Hub デバイス ツインを使用してタグを追加し、IoT Hub クエリを使用する方法。 Java 用 Azure IoT device SDK を使用してデバイス アプリを実装し、タグの追加と IoT Hub のクエリを実行するサービス アプリを実装します。
author: wesmc7777
manager: philmea
ms.author: wesmc
ms.service: iot-hub
services: iot-hub
ms.devlang: java
ms.topic: conceptual
ms.date: 08/26/2019
ms.custom: mqtt, devx-track-java
ms.openlocfilehash: 53a82ba545fa1709d1b79f3ff6b18f1817736bb7
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102217858"
---
# <a name="get-started-with-device-twins-java"></a>デバイス ツインの概要 (Java)

[!INCLUDE [iot-hub-selector-twin-get-started](../../includes/iot-hub-selector-twin-get-started.md)]

このチュートリアルでは、次の 2 つの Java コンソール アプリを作成します。

* **add-tags-query**: タグの追加とデバイス ツインのクエリを実行する Java バックエンド アプリ。
* **simulated-device**: IoT ハブに接続し、報告されるプロパティを使用して接続状態を報告する Java デバイス アプリ。

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

## <a name="get-the-iot-hub-connection-string"></a>IoT ハブ接続文字列を取得する

[!INCLUDE [iot-hub-howto-twin-shared-access-policy-text](../../includes/iot-hub-howto-twin-shared-access-policy-text.md)]

[!INCLUDE [iot-hub-include-find-custom-connection-string](../../includes/iot-hub-include-find-custom-connection-string.md)]

## <a name="create-the-service-app"></a>デバイス アプリを作成する

このセクションでは、**myDeviceId** に関連付けられている IoT Hub 内のデバイス ツインに、場所のメタデータをタグとして追加する Java アプリを作成します。 このアプリは、IoT hub で米国内のデバイスのクエリを最初に実行した後、移動体通信ネットワーク接続を報告しているデバイスのクエリを実行します。

1. 開発用コンピューターで、**iot-java-twin-getstarted** という名前の空のフォルダーを作成します。

2. コマンド プロンプトで次のコマンドを使用して、**iot-java-twin-getstarted** フォルダー内に **add-tags-query** という名前の Maven プロジェクトを作成します。

    ```cmd/sh
    mvn archetype:generate -DgroupId=com.mycompany.app -DartifactId=add-tags-query -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false
    ```

3. コマンド プロンプトで、**add-tags-query** フォルダーに移動します。

4. テキスト エディターを使用して、**add-tags-query** フォルダー内の **pom.xml** ファイルを開き、次の依存関係を **dependencies** ノードに追加します。 この依存関係により、アプリで **iot-service-client** パッケージを使用して IoT Hub と通信できるようになります。

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

7. テキスト エディターを使用して、**add-tags-query\src\main\java\com\mycompany\app\App.java** ファイルを開きます。

8. ファイルに次の **import** ステートメントを追加します。

    ```java
    import com.microsoft.azure.sdk.iot.service.devicetwin.*;
    import com.microsoft.azure.sdk.iot.service.exceptions.IotHubException;

    import java.io.IOException;
    import java.util.HashSet;
    import java.util.Set;
    ```

9. 次のクラスレベル変数を **App** クラスに追加します。 `{youriothubconnectionstring}` を、「[IoT ハブ接続文字列を取得する](#get-the-iot-hub-connection-string)」でコピーしておいた IoT ハブ接続文字列に置き換えます。

    ```java
    public static final String iotHubConnectionString = "{youriothubconnectionstring}";
    public static final String deviceId = "myDeviceId";

    public static final String region = "US";
    public static final String plant = "Redmond43";
    ```

10. **main** メソッドのシグネチャを、次の `throws` 句を含むように更新します。

    ```java
    public static void main( String[] args ) throws IOException
    ```

11. **main** メソッドのコードを、**DeviceTwin** オブジェクトと **DeviceTwinDevice** オブジェクトを作成する次のコードに置き換えます。 **DeviceTwin** オブジェクトは、IoT Hub との通信を処理します。 **DeviceTwinDevice** オブジェクトは、プロパティとタグを使用してデバイス ツインを表現します。

    ```java
    // Get the DeviceTwin and DeviceTwinDevice objects
    DeviceTwin twinClient = DeviceTwin.createFromConnectionString(iotHubConnectionString);
    DeviceTwinDevice device = new DeviceTwinDevice(deviceId);
    ```

12. 次の `try/catch` ブロックを **main** メソッドに追加します。

    ```java
    try {
      // Code goes here
    } catch (IotHubException e) {
      System.out.println(e.getMessage());
    } catch (IOException e) {
      System.out.println(e.getMessage());
    }
    ```

13. デバイス ツインの **region** タグと **plant** タグを更新するには、次のコードを `try` ブロックに追加します。

    ```java
    // Get the device twin from IoT Hub
    System.out.println("Device twin before update:");
    twinClient.getTwin(device);
    System.out.println(device);

    // Update device twin tags if they are different
    // from the existing values
    String currentTags = device.tagsToString();
    if ((!currentTags.contains("region=" + region) && !currentTags.contains("plant=" + plant))) {
      // Create the tags and attach them to the DeviceTwinDevice object
      Set<Pair> tags = new HashSet<Pair>();
      tags.add(new Pair("region", region));
      tags.add(new Pair("plant", plant));
      device.setTags(tags);

      // Update the device twin in IoT Hub
      System.out.println("Updating device twin");
      twinClient.updateTwin(device);
    }

    // Retrieve the device twin with the tag values from IoT Hub
    System.out.println("Device twin after update:");
    twinClient.getTwin(device);
    System.out.println(device);
    ```

14. IoT hub でデバイス ツインのクエリを実行するには、前の手順で追加した `try` ブロックの後ろに次のコードを追加します。 このコードは、2 つのクエリを実行します。 各クエリでは、最大 100 台のデバイスが返されます。

    ```java
    // Query the device twins in IoT Hub
    System.out.println("Devices in Redmond:");

    // Construct the query
    SqlQuery sqlQuery = SqlQuery.createSqlQuery("*", SqlQuery.FromType.DEVICES, "tags.plant='Redmond43'", null);

    // Run the query, returning a maximum of 100 devices
    Query twinQuery = twinClient.queryTwin(sqlQuery.getQuery(), 100);
    while (twinClient.hasNextDeviceTwin(twinQuery)) {
      DeviceTwinDevice d = twinClient.getNextDeviceTwin(twinQuery);
      System.out.println(d.getDeviceId());
    }

    System.out.println("Devices in Redmond using a cellular network:");

    // Construct the query
    sqlQuery = SqlQuery.createSqlQuery("*", SqlQuery.FromType.DEVICES, "tags.plant='Redmond43' AND properties.reported.connectivityType = 'cellular'", null);

    // Run the query, returning a maximum of 100 devices
    twinQuery = twinClient.queryTwin(sqlQuery.getQuery(), 3);
    while (twinClient.hasNextDeviceTwin(twinQuery)) {
      DeviceTwinDevice d = twinClient.getNextDeviceTwin(twinQuery);
      System.out.println(d.getDeviceId());
    }
    ```

15. **add-tags-query\src\main\java\com\mycompany\app\App.java** ファイルを保存して閉じます。

16. **add-tags-query** アプリをビルドし、エラーを修正します。 コマンド プロンプトで **add-tags-query** フォルダーに移動し、次のコマンドを実行します。

    ```cmd/sh
    mvn clean package -DskipTests
    ```

## <a name="create-a-device-app"></a>デバイス アプリの作成

このセクションでは、IoT Hub に送信される、報告されるプロパティ値を設定する Java コンソール アプリを作成します。

1. コマンド プロンプトで次のコマンドを使用して、**iot-java-twin-getstarted** フォルダー内に **simulated-device** という名前の Maven プロジェクトを作成します。

    ```cmd/sh
    mvn archetype:generate -DgroupId=com.mycompany.app -DartifactId=simulated-device -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false
    ```

2. コマンド プロンプトで、**simulated-device** フォルダーに移動します。

3. テキスト エディターを使用して、**simulated-device** フォルダー内の **pom.xml** ファイルを開き、次の依存関係を **dependencies** ノードに追加します。 この依存関係により、アプリの **iot-device-client** パッケージを使用して IoT ハブと通信できるようになります。

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

9. 次のクラスレベル変数を **App** クラスに追加します。 `{yourdeviceconnectionstring}` を、「[IoT ハブに新しいデバイスを登録する](#register-a-new-device-in-the-iot-hub)」でコピーしておいたデバイス接続文字列に置き換えます。

    ```java
    private static String connString = "{yourdeviceconnectionstring}";
    private static IotHubClientProtocol protocol = IotHubClientProtocol.MQTT;
    private static String deviceId = "myDeviceId";
    ```

    このサンプル アプリでは、**DeviceClient** オブジェクトをインスタンス化するときに **protocol** 変数が使用されます。

10. デバイス ツインの更新に関する情報を出力するには、次のメソッドを **App** クラスに追加します。

    ```java
    protected static class DeviceTwinStatusCallBack implements IotHubEventCallback {
        @Override
        public void execute(IotHubStatusCode status, Object context) {
          System.out.println("IoT Hub responded to device twin operation with status " + status.name());
        }
      }
    ```

11. **main** メソッドのコードを次のコードに置き換えます。

    * IoT Hub と通信するデバイス クライアントを作成します。

    * デバイス ツインのプロパティを格納する **Device** オブジェクトを作成します。

    ```java
    DeviceClient client = new DeviceClient(connString, protocol);

    // Create a Device object to store the device twin properties
    Device dataCollector = new Device() {
      // Print details when a property value changes
      @Override
      public void PropertyCall(String propertyKey, Object propertyValue, Object context) {
        System.out.println(propertyKey + " changed to " + propertyValue);
      }
    };
    ```

12. 報告されるプロパティ **connectivityType** を作成して IoT Hub に送信する次のコードを **main** メソッドに追加します。

    ```java
    try {
      // Open the DeviceClient and start the device twin services.
      client.open();
      client.startDeviceTwin(new DeviceTwinStatusCallBack(), null, dataCollector, null);

      // Create a reported property and send it to your IoT hub.
      dataCollector.setReportedProp(new Property("connectivityType", "cellular"));
      client.sendReportedProperties(dataCollector.getReportedProp());
    }
    catch (Exception e) {
      System.out.println("On exception, shutting down \n" + " Cause: " + e.getCause() + " \n" + e.getMessage());
      dataCollector.clean();
      client.closeNow();
      System.out.println("Shutting down...");
    }
    ```

13. 次のコードを **main** メソッドの末尾に追加します。 **Enter** キーが押されるまで待機することで、IoT Hub がデバイス ツイン操作の状態を報告するための時間を取ることができます。

    ```java
    System.out.println("Press any key to exit...");

    Scanner scanner = new Scanner(System.in);
    scanner.nextLine();

    dataCollector.clean();
    client.close();
    ```

14. **main** メソッドのシグネチャを変更し、下の例外を追加します。

     ```java
     public static void main(String[] args) throws URISyntaxException, IOException
     ```

15. **simulated-device\src\main\java\com\mycompany\app\App.java** ファイルを保存して閉じます。

16. **simulated-device** アプリをビルドし、エラーを修正します。 コマンド プロンプトで **simulated-device** フォルダーに移動し、次のコマンドを実行します。

    ```cmd/sh
    mvn clean package -DskipTests
    ```

## <a name="run-the-apps"></a>アプリの実行

これで、コンソール アプリを実行する準備が整いました。

1. **add-tags-query** フォルダーのコマンド プロンプトで、次のコマンドを実行して、**add-tags-query** サービス アプリを実行します。

    ```cmd/sh
    mvn exec:java -Dexec.mainClass="com.mycompany.app.App"
    ```

    ![add-tags-query サービス アプリを実行するコマンドからの出力のスクリーンショット。](./media/iot-hub-java-java-twin-getstarted/service-app-1.png)

    デバイス ツインに **plant** タグと **region** タグが追加されていることを確認できます。 最初のクエリはデバイスを返しますが、2 つ目のクエリは返しません。

2. **simulated-device** フォルダーのコマンド プロンプトで、次のコマンドを実行して、報告されるプロパティ **connectivityType** をデバイス ツインに追加します。

    ```cmd/sh
    mvn exec:java -Dexec.mainClass="com.mycompany.app.App"
    ```

    ![デバイス クライアントによって、報告されるプロパティ connectivity Type が追加される](./media/iot-hub-java-java-twin-getstarted/device-app-1.png)

3. **add-tags-query** フォルダーのコマンド プロンプトで、次のコマンドを実行して、**add-tags-query** サービス アプリの 2 回目の実行を行います。

    ```cmd/sh
    mvn exec:java -Dexec.mainClass="com.mycompany.app.App"
    ```

    ![タグの値を更新し、デバイスのクエリを実行する Java IoT Hub サービス アプリ](./media/iot-hub-java-java-twin-getstarted/service-app-2.png)

    デバイスによって **connectivityType** プロパティが IoT Hub に送信されているため、2 回目のクエリではデバイスが返ります。

## <a name="next-steps"></a>次のステップ

このチュートリアルでは、Azure Portal で新しい IoT Hub を構成し、IoT Hub の ID レジストリにデバイス ID を作成しました。 バックエンド アプリからデバイスのメタデータをタグとして追加し、デバイス ツインのデバイスの接続情報を報告するデバイス アプリを記述しました。 さらに、SQL に似た IoT Hub クエリ言語を使用して、デバイス ツイン情報のクエリを実行する方法も学習しました。

詳細については、次のリソースをご覧ください。

* [IoT Hub の概要](quickstart-send-telemetry-java.md)に関するチュートリアルでデバイスからテレメトリを送信する。

* 「[ダイレクト メソッドの使用](quickstart-control-device-java.md)」チュートリアルで、デバイスを対話形式で制御する (ユーザー制御アプリからファンをオンにするなど)。