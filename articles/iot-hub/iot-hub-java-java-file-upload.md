---
title: Java を使用してデバイスから Azure IoT Hub にファイルをアップロードする | Microsoft Docs
description: Java 用 Azure IoT device SDK を使用してデバイスからクラウドにファイルをアップロードする方法。 アップロードしたファイルは Azure Storage Blob コンテナーに格納されます。
author: wesmc7777
manager: philmea
ms.author: wesmc
ms.service: iot-hub
services: iot-hub
ms.devlang: java
ms.topic: conceptual
ms.date: 06/28/2017
ms.openlocfilehash: 27cdada0bfbb4236e16d17c263aaba0f4f5c511f
ms.sourcegitcommit: 6a42dd4b746f3e6de69f7ad0107cc7ad654e39ae
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/07/2019
ms.locfileid: "67620130"
---
# <a name="upload-files-from-your-device-to-the-cloud-with-iot-hub"></a>IoT Hub を使用してデバイスからクラウドにファイルをアップロードする

[!INCLUDE [iot-hub-file-upload-language-selector](../../includes/iot-hub-file-upload-language-selector.md)]

このチュートリアルは、[IoT Hub を使用したクラウドからデバイスへのメッセージの送信](iot-hub-java-java-c2d.md)に関するチュートリアル内のコードに基づいて、[IoT Hub のファイル アップロード機能](iot-hub-devguide-file-upload.md)を使用して [Azure Blob Storage](../storage/index.yml) にファイルをアップロードする方法を示しています。 このチュートリアルでは、次の操作方法について説明します。

* ファイルのアップロードで Azure BLOB URI を使用してデバイスをセキュリティで保護する。

* IoT Hub ファイル アップロード通知を使用して、アプリのバックエンドでのファイルの処理を開始する。

[デバイスから IoT ハブにテレメトリを送信する方法](quickstart-send-telemetry-java.md)のクイックスタートと [IoT Hub で cloud-to-device メッセージを送信する方法](iot-hub-java-java-c2d.md)のチュートリアルには、IoT Hub のデバイスからクラウドへのメッセージングと cloud-to-device メッセージの基本的な機能が示されています。 「[IoT Hub を使用してメッセージ ルーティングを構成する](tutorial-routing.md)」チュートリアルでは、Azure Blob Storage にデバイスからクラウドへのメッセージを確実に格納する方法を説明しています。 ただし、一部のシナリオでは、デバイスから送信されるデータを、IoT Hub が受け取る、クラウドからデバイスへの比較的小さなメッセージにマッピングすることは簡単ではありません。 例:

* イメージを含む大きなファイル
* ビデオ
* 高頻度でサンプリングされる振動データ
* 何らかの形式の前処理済みデータ

これらのファイルは通常、[Azure Data Factory](../data-factory/introduction.md) や [Hadoop](../hdinsight/index.yml) スタックなどのツールを使用してクラウドでバッチ処理されます。 デバイスからファイルをアップロードする必要がある場合も、IoT Hub のセキュリティを信頼性を使用できます。

このチュートリアルの最後に、次の 2 つの Java コンソール アプリを実行します。

* **simulated-device**: [IoT Hub を使用したクラウドからデバイスへのメッセージの送信] チュートリアルで作成したアプリの修正バージョンです。 このアプリは、IoT Hub によって提供された SAS URI を使用してストレージにファイルをアップロードします。

* **read-file-upload-notification**: IoT Hub からファイル アップロード通知を受信します。

> [!NOTE]
> IoT Hub は、Azure IoT Device SDK を介して多数のデバイス プラットフォームと言語 (C、.NET、Javascript など) をサポートしています。 Azure IoT Hub にデバイスを接続するための詳しい手順については、[Azure IoT デベロッパー センター](https://azure.microsoft.com/develop/iot)を参照してください。

このチュートリアルを完了するには、以下が必要です。

* 最新の [Java SE Development Kit 8](https://aka.ms/azure-jdks)

* [Maven 3](https://maven.apache.org/install.html)

* アクティブな Azure アカウントアカウントがない場合、Azure 試用版にサインアップして、最大 10 件の無料 Mobile Apps を入手できます。 (アカウントがない場合は、[無料アカウント](https://azure.microsoft.com/pricing/free-trial/) を数分で作成することができます)。

[!INCLUDE [iot-hub-associate-storage](../../includes/iot-hub-associate-storage.md)]

## <a name="upload-a-file-from-a-device-app"></a>デバイス アプリからのファイルのアップロード

このセクションでは、「[IoT Hub を使用したクラウドからデバイスへのメッセージの送信](iot-hub-java-java-c2d.md)」で作成したデバイス アプリを変更して、ファイルを IoT Hub にアップロードするようにします。

1. イメージ ファイルを `simulated-device` フォルダーにコピーし、名前を `myimage.png` に変更します。

2. テキスト エディターで、`simulated-device\src\main\java\com\mycompany\app\App.java` ファイルを開きます。

3. 変数宣言を **App** クラスに追加します。

    ```java
    private static String fileName = "myimage.png";
    ```

4. ファイル アップロード ステータス コールバック メッセージを処理するには、次の入れ子になったクラスを **App** クラスに追加します。

    ```java
    // Define a callback method to print status codes from IoT Hub.
    protected static class FileUploadStatusCallBack implements IotHubEventCallback {
      public void execute(IotHubStatusCode status, Object context) {
        System.out.println("IoT Hub responded to file upload for " + fileName
            + " operation with status " + status.name());
      }
    }
    ```

5. IoT Hub にイメージをアップロードするには、次のメソッドを **App** クラスに追加して、IoT Hub にイメージをアップロードします。

    ```java
    // Use IoT Hub to upload a file asynchronously to Azure blob storage.
    private static void uploadFile(String fullFileName) throws FileNotFoundException, IOException
    {
      File file = new File(fullFileName);
      InputStream inputStream = new FileInputStream(file);
      long streamLength = file.length();

      client.uploadToBlobAsync(fileName, inputStream, streamLength, new FileUploadStatusCallBack(), null);
    }
    ```

6. 次のスニペットに示すように、**main** メソッドを **uploadFile** メソッドを呼び出すように変更します。

    ```java
    client.open();

    try
    {
      // Get the filename and start the upload.
      String fullFileName = System.getProperty("user.dir") + File.separator + fileName;
      uploadFile(fullFileName);
      System.out.println("File upload started with success");
    }
    catch (Exception e)
    {
      System.out.println("Exception uploading file: " + e.getCause() + " \nERROR: " + e.getMessage());
    }

    MessageSender sender = new MessageSender();
    ```

7. 次のコマンドを使用して、**simulated-device** アプリをビルドし、エラーを確認します。

    ```cmd/sh
    mvn clean package -DskipTests
    ```

## <a name="receive-a-file-upload-notification"></a>ファイル アップロードの通知の受信

このセクションでは、IoT Hub からファイル アップロードの通知メッセージを受信する Java コンソール アプリケーションを作成します。

このセクションを完了するには、IoT Hub の **iothubowner** 接続文字列が必要です。 この接続文字列は、[Azure ポータル](https://portal.azure.com/)の **[共有アクセス ポリシー]** ブレードで確認できます。

1. コマンド プロンプトで次のコマンドを使用して、**read-file-upload-notification** という Maven プロジェクトを作成します。 このコマンドが 1 つの長いコマンドであることに注意してください。

    ```cmd/sh
    mvn archetype:generate -DgroupId=com.mycompany.app -DartifactId=read-file-upload-notification -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false
    ```

2. コマンド プロンプトで、新しい `read-file-upload-notification` フォルダーに移動します。

3. テキスト エディターを使用して、`read-file-upload-notification` フォルダー内の `pom.xml` ファイルを開き、次の依存関係を **dependencies** ノードに追加します。 依存関係を追加することにより、アプリケーションの **iothub-java-service-client** パッケージを使用して、IoT Hub サービスと通信できます。

    ```xml
    <dependency>
      <groupId>com.microsoft.azure.sdk.iot</groupId>
      <artifactId>iot-service-client</artifactId>
      <version>1.7.23</version>
    </dependency>
    ```

    > [!NOTE]
    > [Maven 検索](https://search.maven.org/#search%7Cga%7C1%7Ca%3A%22iot-service-client%22%20g%3A%22com.microsoft.azure.sdk.iot%22)を使用して、**iot-service-client** の最新バージョンを確認できます。

4. `pom.xml` ファイルを保存して閉じます。

5. テキスト エディターで、`read-file-upload-notification\src\main\java\com\mycompany\app\App.java` ファイルを開きます。

6. ファイルに次の **import** ステートメントを追加します。

    ```java
    import com.microsoft.azure.sdk.iot.service.*;
    import java.io.IOException;
    import java.net.URISyntaxException;
    import java.util.concurrent.ExecutorService;
    import java.util.concurrent.Executors;
    ```

7. 次のクラスレベル変数を **App** クラスに追加します。

    ```java
    private static final String connectionString = "{Your IoT Hub connection string}";
    private static final IotHubServiceClientProtocol protocol = IotHubServiceClientProtocol.AMQPS;
    private static FileUploadNotificationReceiver fileUploadNotificationReceiver = null;
    ```

8. ファイルのアップロードに関する情報をコンソールに出力するには、次の入れ子になったクラスを **App** クラスに追加します。

    ```java
    // Create a thread to receive file upload notifications.
    private static class ShowFileUploadNotifications implements Runnable {
      public void run() {
        try {
          while (true) {
            System.out.println("Receive file upload notifications...");
            FileUploadNotification fileUploadNotification = fileUploadNotificationReceiver.receive();
            if (fileUploadNotification != null) {
              System.out.println("File Upload notification received");
              System.out.println("Device Id : " + fileUploadNotification.getDeviceId());
              System.out.println("Blob Uri: " + fileUploadNotification.getBlobUri());
              System.out.println("Blob Name: " + fileUploadNotification.getBlobName());
              System.out.println("Last Updated : " + fileUploadNotification.getLastUpdatedTimeDate());
              System.out.println("Blob Size (Bytes): " + fileUploadNotification.getBlobSizeInBytes());
              System.out.println("Enqueued Time: " + fileUploadNotification.getEnqueuedTimeUtcDate());
            }
          }
        } catch (Exception ex) {
          System.out.println("Exception reading reported properties: " + ex.getMessage());
        }
      }
    }
    ```

9. ファイル アップロードの通知をリッスンするスレッドを開始するには、次のコードを**main** メソッドに追加します。

    ```java
    public static void main(String[] args) throws IOException, URISyntaxException, Exception {
      ServiceClient serviceClient = ServiceClient.createFromConnectionString(connectionString, protocol);

      if (serviceClient != null) {
        serviceClient.open();

        // Get a file upload notification receiver from the ServiceClient.
        fileUploadNotificationReceiver = serviceClient.getFileUploadNotificationReceiver();
        fileUploadNotificationReceiver.open();

        // Start the thread to receive file upload notifications.
        ShowFileUploadNotifications showFileUploadNotifications = new ShowFileUploadNotifications();
        ExecutorService executor = Executors.newFixedThreadPool(1);
        executor.execute(showFileUploadNotifications);

        System.out.println("Press ENTER to exit.");
        System.in.read();
        executor.shutdownNow();
        System.out.println("Shutting down sample...");
        fileUploadNotificationReceiver.close();
        serviceClient.close();
      }
    }
    ```

10. `read-file-upload-notification\src\main\java\com\mycompany\app\App.java` ファイルを保存して閉じます。

11. 次のコマンドを使用して **read-file-upload-notification** アプリをビルドし、エラーを確認します。

    ```cmd/sh
    mvn clean package -DskipTests
    ```

## <a name="run-the-applications"></a>アプリケーションの実行

これで、アプリケーションを実行する準備が整いました。

`read-file-upload-notification` フォルダーのコマンド プロンプトで、次のコマンドを実行します。

```cmd/sh
mvn exec:java -Dexec.mainClass="com.mycompany.app.App"
```

`simulated-device` フォルダーのコマンド プロンプトで、次のコマンドを実行します。

```cmd/sh
mvn exec:java -Dexec.mainClass="com.mycompany.app.App"
```

次のスクリーン ショットは、**simulated-device** アプリからの出力を示しています。

![simulated-device アプリからの出力](media/iot-hub-java-java-upload/simulated-device.png)

次のスクリーン ショットは、**read-file-upload-notification** アプリからの出力を示しています。

![read-file-upload-notification アプリからの出力](media/iot-hub-java-java-upload/read-file-upload-notification.png)

ポータルを使用して、構成したストレージ コンテナーにアップロードされたファイルを表示できます。

![アップロードされたファイル](media/iot-hub-java-java-upload/uploaded-file.png)

## <a name="next-steps"></a>次の手順

このチュートリアルでは、IoT Hub のファイル アップロード機能を使用して、デバイスからのファイルのアップロードを簡素化する方法を学習しました。 次の記事で IoT Hub の機能やシナリオをさらに詳しく調べることができます。

* [プログラムによる IoT Hub の作成](iot-hub-rm-template-powershell.md)

* [C SDK の概要](iot-hub-device-sdk-c-intro.md)

* [Azure IoT SDK](iot-hub-devguide-sdks.md)

IoT Hub の機能を詳しく調べるには、次のリンクを使用してください。

* [IoT Edge を使用したデバイスのシミュレーション](../iot-edge/tutorial-simulate-device-linux.md)
