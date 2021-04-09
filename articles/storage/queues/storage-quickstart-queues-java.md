---
title: 'クイックスタート: Azure Queue Storage クライアント ライブラリ v12 - Java'
description: Java 用 Azure Queue Storage クライアント ライブラリ v12 を使用して、キューを作成し、そこにメッセージを追加する方法について説明します。 次に、キューからメッセージを読み取って削除する方法について説明します。 キューを削除する方法についても説明します。
author: mhopkins-msft
ms.author: mhopkins
ms.date: 12/01/2020
ms.topic: quickstart
ms.service: storage
ms.subservice: queues
ms.custom: devx-track-java
ms.openlocfilehash: 814531adc9dafa524797d0c2674a1e600e407bed
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "97588218"
---
# <a name="quickstart-azure-queue-storage-client-library-v12-for-java"></a>クイックスタート: Java 用 Azure Queue Storage クライアント ライブラリ v12

Java 用 Azure Queue Storage クライアント ライブラリ v12 を使用してみましょう。 Azure Queue Storage は、後で取得して処理するために多数のメッセージを格納するためのサービスです。 以下の手順に従って、パッケージをインストールし、基本タスクのコード例を試してみましょう。

Java 用 Azure Queue Storage クライアント ライブラリ v12 を使用すると、以下のことができます。

- キューを作成する
- メッセージをキューに追加する
- キュー内のメッセージを表示する
- キュー内のメッセージを更新する
- キューからメッセージを受信して削除する
- キューを削除する

その他のリソース:

- [API リファレンス ドキュメント](/java/api/overview/azure/storage-queue-readme)
- [ライブラリ ソース コード](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/storage/azure-storage-queue)
- [パッケージ (Maven)](https://mvnrepository.com/artifact/com.azure/azure-storage-queue)
- [サンプル](../common/storage-samples-java.md?toc=%2fazure%2fstorage%2fqueues%2ftoc.json#queue-samples)

## <a name="prerequisites"></a>前提条件

- [Java Development Kit (JDK)](/java/azure/jdk/) バージョン 8 以降
- [Apache Maven](https://maven.apache.org/download.cgi)
- Azure サブスクリプション - [無料アカウントを作成する](https://azure.microsoft.com/free/)
- Azure Storage アカウント - [ストレージ アカウントを作成する](../common/storage-account-create.md)

## <a name="setting-up"></a>設定

このセクションでは、Java 用 Azure Queue Storage クライアント ライブラリ v12 を操作するためのプロジェクトの準備について説明します。

### <a name="create-the-project"></a>プロジェクトを作成する

`queues-quickstart-v12` という名前の Java アプリケーションを作成します。

1. コンソール ウィンドウ (cmd、PowerShell、Bash など) で、Maven コマンドを使用し、`queues-quickstart-v12` という名前で新しいコンソール アプリを作成します。 次の `mvn` コマンドを入力して "hello world" Java プロジェクトを作成します。

    # <a name="powershell"></a>[PowerShell](#tab/powershell)

    ```powershell
    mvn archetype:generate `
        --define interactiveMode=n `
        --define groupId=com.queues.quickstart `
        --define artifactId=queues-quickstart-v12 `
        --define archetypeArtifactId=maven-archetype-quickstart `
        --define archetypeVersion=1.4
    ```

    # <a name="bash"></a>[Bash](#tab/bash)

    ```bash
    mvn archetype:generate \
        --define interactiveMode=n \
        --define groupId=com.queues.quickstart \
        --define artifactId=queues-quickstart-v12 \
        --define archetypeArtifactId=maven-archetype-quickstart \
        --define archetypeVersion=1.4
    ```

    ---

1. プロジェクトの生成からの出力は、次のようになります。

    ```console
    [INFO] Scanning for projects...
    [INFO]
    [INFO] ------------------< org.apache.maven:standalone-pom >-------------------
    [INFO] Building Maven Stub Project (No POM) 1
    [INFO] --------------------------------[ pom ]---------------------------------
    [INFO]
    [INFO] >>> maven-archetype-plugin:3.1.2:generate (default-cli) > generate-sources @ standalone-pom >>>
    [INFO]
    [INFO] <<< maven-archetype-plugin:3.1.2:generate (default-cli) < generate-sources @ standalone-pom <<<
    [INFO]
    [INFO]
    [INFO] --- maven-archetype-plugin:3.1.2:generate (default-cli) @ standalone-pom ---
    [INFO] Generating project in Batch mode
    [INFO] ----------------------------------------------------------------------------
    [INFO] Using following parameters for creating project from Archetype: maven-archetype-quickstart:1.4
    [INFO] ----------------------------------------------------------------------------
    [INFO] Parameter: groupId, Value: com.queues.quickstart
    [INFO] Parameter: artifactId, Value: queues-quickstart-v12
    [INFO] Parameter: version, Value: 1.0-SNAPSHOT
    [INFO] Parameter: package, Value: com.queues.quickstart
    [INFO] Parameter: packageInPathFormat, Value: com/queues/quickstart
    [INFO] Parameter: version, Value: 1.0-SNAPSHOT
    [INFO] Parameter: package, Value: com.queues.quickstart
    [INFO] Parameter: groupId, Value: com.queues.quickstart
    [INFO] Parameter: artifactId, Value: queues-quickstart-v12
    [INFO] Project created from Archetype in dir: C:\quickstarts\queues\queues-quickstart-v12
    [INFO] ------------------------------------------------------------------------
    [INFO] BUILD SUCCESS
    [INFO] ------------------------------------------------------------------------
    [INFO] Total time:  6.394 s
    [INFO] Finished at: 2019-12-03T09:58:35-08:00
    [INFO] ------------------------------------------------------------------------
    ```

1. 新しく作成した `queues-quickstart-v12` ディレクトリに切り替えます。

   ```console
   cd queues-quickstart-v12
   ```

### <a name="install-the-package"></a>パッケージをインストールする

テキスト エディターで `pom.xml` ファイルを開きます。 依存関係のグループに、次の dependency 要素を追加します。

```xml
<dependency>
  <groupId>com.azure</groupId>
  <artifactId>azure-storage-queue</artifactId>
  <version>12.0.1</version>
</dependency>
```

### <a name="set-up-the-app-framework"></a>アプリのフレームワークを設定する

プロジェクト ディレクトリで次の操作を行います。

1. `/src/main/java/com/queues/quickstart` ディレクトリに移動します
1. エディターで `App.java` ファイルを開きます
1. `System.out.println("Hello, world");` ステートメントを削除します
1. `import` ディレクティブを追加します

コードは次のとおりです。

```java
package com.queues.quickstart;

/**
 * Azure Queue Storage client library v12 quickstart
 */
import com.azure.storage.queue.*;
import com.azure.storage.queue.models.*;
import java.io.*;
import java.time.*;

public class App
{
    public static void main( String[] args ) throws IOException
    {
    }
}
```

[!INCLUDE [storage-quickstart-credentials-include](../../../includes/storage-quickstart-credentials-include.md)]

## <a name="object-model"></a>オブジェクト モデル

Azure Queue storage は、多数のメッセージを格納するためのサービスです。 キュー メッセージの許容される最大サイズは 64 KB です。 キューには、ストレージ アカウントの総容量の上限を超えない限り、数百万のメッセージを含めることができます。 キューは通常、非同期的な処理用に作業のバックログを作成するために使用されます。 Queue Storage には、次の 3 種類のリソースがあります。

- ストレージ アカウント
- ストレージ アカウント内のキュー
- キュー内のメッセージ

次の図に、これらのリソースの関係を示します。

![Queue storage のアーキテクチャ図](./media/storage-queues-introduction/queue1.png)

これらのリソースとやり取りするには、以下の Java クラスを使用します。

- [`QueueClientBuilder`](/java/api/com.azure.storage.queue.queueclientbuilder): `QueueClientBuilder` クラスは、`QueueClient` オブジェクトを構成し、インスタンス化します。
- [`QueueServiceClient`](/java/api/com.azure.storage.queue.queueserviceclient): `QueueServiceClient` を使用すると、ストレージ アカウント内のすべてのキューを管理できます。
- [`QueueClient`](/java/api/com.azure.storage.queue.queueclient): `QueueClient` クラスを使用すると、個々のキューとそのメッセージを管理および操作できます。
- [`QueueMessageItem`](/java/api/com.azure.storage.queue.models.queuemessageitem): `QueueMessageItem` クラスは、キューに対して [`ReceiveMessages`](/java/api/com.azure.storage.queue.queueclient.receivemessages) を呼び出したときに返される個々のオブジェクトを表します。

## <a name="code-examples"></a>コード例

以下のサンプル コード スニペットは、Java 用 Azure Queue Storage クライアント ライブラリを使用して以下の操作を実行する方法を示します。

- [接続文字列を取得する](#get-the-connection-string)
- [キューを作成する](#create-a-queue)
- [メッセージをキューに追加する](#add-messages-to-a-queue)
- [キュー内のメッセージを表示する](#peek-at-messages-in-a-queue)
- [キュー内のメッセージを更新する](#update-a-message-in-a-queue)
- [キューからメッセージを受信して削除する](#receive-and-delete-messages-from-a-queue)
- [キューを削除する](#delete-a-queue)

### <a name="get-the-connection-string"></a>接続文字列を取得する

以下のコードは、ストレージ アカウントの接続文字列を取得します。 この接続文字列は、「[ストレージ接続文字列の構成](#configure-your-storage-connection-string)」セクションで作成した環境変数に格納されます。

このコードを `main` メソッド内に追加します。

```java
System.out.println("Azure Queue Storage client library v12 - Java quickstart sample\n");

// Retrieve the connection string for use with the application. The storage
// connection string is stored in an environment variable on the machine
// running the application called AZURE_STORAGE_CONNECTION_STRING. If the environment variable
// is created after the application is launched in a console or with
// Visual Studio, the shell or application needs to be closed and reloaded
// to take the environment variable into account.
String connectStr = System.getenv("AZURE_STORAGE_CONNECTION_STRING");
```

### <a name="create-a-queue"></a>キューを作成する

新しいキューの名前を決定します。 次のコードは、キュー名に GUID 値を追加して、確実に一意になるようにします。

> [!IMPORTANT]
> キュー名に使用できるのは小文字、数字、ハイフンのみであり、名前の先頭は文字または数字にする必要があります。 各ハイフンの前後にはハイフン以外の文字を指定する必要があります。 また、名前は 3 から 63 文字で指定する必要があります。 キューの名前付けの詳細については、「[キューとメタデータの名前付け](/rest/api/storageservices/naming-queues-and-metadata)」を参照してください。

[`QueueClient`](/java/api/com.azure.storage.queue.queueclient) クラスのインスタンスを作成します。 次に、[`Create`](/java/api/com.azure.storage.queue.queueclient.create) メソッドを呼び出して、ストレージ アカウントにキューを作成します。

`main` メソッドの末尾に次のコードを追加します。

```java
// Create a unique name for the queue
String queueName = "quickstartqueues-" + java.util.UUID.randomUUID();

System.out.println("Creating queue: " + queueName);

// Instantiate a QueueClient which will be
// used to create and manipulate the queue
QueueClient queueClient = new QueueClientBuilder()
                                .connectionString(connectStr)
                                .queueName(queueName)
                                .buildClient();

// Create the queue
queueClient.create();
```

### <a name="add-messages-to-a-queue"></a>メッセージをキューに追加する

以下のコード スニペットは、[`sendMessage`](/java/api/com.azure.storage.queue.queueclient.sendmessage) メソッドを呼び出してキューにメッセージを追加します。 さらに、`sendMessage` の呼び出しから返された [`SendMessageResult`](/java/api/com.azure.storage.queue.models.sendmessageresult) を保存します。 この結果は、後でプログラムの中でメッセージを更新する際に使用します。

`main` メソッドの末尾に次のコードを追加します。

```java
System.out.println("\nAdding messages to the queue...");

// Send several messages to the queue
queueClient.sendMessage("First message");
queueClient.sendMessage("Second message");

// Save the result so we can update this message later
SendMessageResult result = queueClient.sendMessage("Third message");
```

### <a name="peek-at-messages-in-a-queue"></a>キュー内のメッセージを表示する

キュー内のメッセージをクイック表示するには、[`peekMessages`](/java/api/com.azure.storage.queue.queueclient.peekmessages) メソッドを呼び出します。 このメソッドは、キューの先頭からメッセージを 1 つ以上取得しますが、メッセージの可視性は変更しません。

`main` メソッドの末尾に次のコードを追加します。

```java
System.out.println("\nPeek at the messages in the queue...");

// Peek at messages in the queue
queueClient.peekMessages(10, null, null).forEach(
    peekedMessage -> System.out.println("Message: " + peekedMessage.getMessageText()));
```

### <a name="update-a-message-in-a-queue"></a>キュー内のメッセージを更新する

メッセージの内容を更新するには、[`updateMessage`](/java/api/com.azure.storage.queue.queueclient.updatemessage) メソッドを呼び出します。 メッセージの表示タイムアウトと内容は、このメソッドで変更できます。 メッセージの内容には UTF-8 でエンコードされた文字列を指定してください。最大サイズは 64 KB です。 先ほどこのコードの中で保存した `SendMessageResult` を使用して、メッセージ ID と PopReceipt を、新しいメッセージの内容と共に渡します。 更新するメッセージは、メッセージ ID と PopReceipt によって識別されます。

```java
System.out.println("\nUpdating the third message in the queue...");

// Update a message using the result that
// was saved when sending the message
queueClient.updateMessage(result.getMessageId(),
                          result.getPopReceipt(),
                          "Third message has been updated",
                          Duration.ofSeconds(1));
```

### <a name="receive-and-delete-messages-from-a-queue"></a>キューからメッセージを受信して削除する

[`receiveMessages`](/java/api/com.azure.storage.queue.queueclient.receivemessages) メソッドを呼び出して、先ほど追加したメッセージをダウンロードします。 このコード例では、メッセージを受信して処理した後、キューからメッセージを削除します。 ここでの処理は、単にメッセージをコンソールに表示するだけです。

ユーザーからの入力を待ってメッセージを受信、削除するために、`System.console().readLine();` を呼び出してアプリを一時停止させます。 リソースが正しく作成されたことを [Azure portal](https://portal.azure.com) で確認してから、それらを削除してください。 明示的に削除されなかったメッセージは、最終的にキューに再表示され、別の機会に処理されることになります。

`main` メソッドの末尾に次のコードを追加します。

```java
System.out.println("\nPress Enter key to receive messages and delete them from the queue...");
System.console().readLine();

// Get messages from the queue
queueClient.receiveMessages(10).forEach(
    // "Process" the message
    receivedMessage -> {
        System.out.println("Message: " + receivedMessage.getMessageText());

        // Let the service know we're finished with
        // the message and it can be safely deleted.
        queueClient.deleteMessage(receivedMessage.getMessageId(), receivedMessage.getPopReceipt());
    }
);
```

### <a name="delete-a-queue"></a>キューを削除する

次のコードでは、[`Delete`](/java/api/com.azure.storage.queue.queueclient.delete) メソッドを使用してキューを削除することにより、アプリによって作成されたリソースがクリーンアップされます。

`main` メソッドの末尾に次のコードを追加します。

```java
System.out.println("\nPress Enter key to delete the queue...");
System.console().readLine();

// Clean up
System.out.println("Deleting queue: " + queueClient.getQueueName());
queueClient.delete();

System.out.println("Done");
```

## <a name="run-the-code"></a>コードの実行

このアプリは、3 つのメッセージを作成して Azure のキューに追加します。 コードでは、キュー内のメッセージを一覧表示した後にそれらを取得して削除してから、最後にキューを削除します。

コンソール ウィンドウで、お使いのアプリケーションのディレクトリに移動し、アプリケーションをビルドして実行します。

```console
mvn compile
```

次に、パッケージをビルドします。

```console
mvn package
```

次の `mvn` コマンドを使用して、アプリを実行します。

```console
mvn exec:java -Dexec.mainClass="com.queues.quickstart.App" -Dexec.cleanupDaemonThreads=false
```

アプリの出力は、次の例のようになります。

```output
Azure Queue Storage client library v12 - Java quickstart sample

Adding messages to the queue...

Peek at the messages in the queue...
Message: First message
Message: Second message
Message: Third message

Updating the third message in the queue...

Press Enter key to receive messages and delete them from the queue...

Message: First message
Message: Second message
Message: Third message has been updated

Press Enter key to delete the queue...

Deleting queue: quickstartqueues-fbf58f33-4d5a-41ac-ac0e-1a05d01c7003
Done
```

メッセージを受信する前にアプリが一時停止したら、[Azure portal](https://portal.azure.com) でストレージ アカウントを確認してください。 キューにメッセージが存在することを確認します。

`Enter` キーを押してメッセージを受信し、削除します。 確認を求められたら、もう一度 `Enter` キーを押してキューを削除し、デモを終了します。

## <a name="next-steps"></a>次のステップ

このクイックスタートでは、Java コードを使用して、キューを作成し、そこにメッセージを追加する方法について説明しました。 その後、メッセージの表示、取得、削除について説明しました。 最後に、メッセージ キューを削除する方法を説明しました。

チュートリアル、サンプル、クイック スタートなどのドキュメントについては、次のページを参照してください。

> [!div class="nextstepaction"]
> [Java クラウド開発者向けの Azure](/azure/developer/java/)

- その他の Azure Queue Storage サンプル アプリについては、「[Azure Queue Storage client library v12 for Java - samples (Java 用 Azure Queue Storage クライアント ライブラリ v12 - サンプル)](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/storage/azure-storage-queue/src/samples/java/com/azure/storage/queue)」を参照してください。
