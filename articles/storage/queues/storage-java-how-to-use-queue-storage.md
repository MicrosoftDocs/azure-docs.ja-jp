---
title: Java から Queue Storage を使用する方法 - Azure Storage
description: Queue Storage を使用してキューを作成および削除する方法について説明します。 Java 用の Azure Storage クライアント ライブラリを使用して、メッセージの挿入、ピーク、取得、削除を行う方法について説明します。
author: mhopkins-msft
ms.author: mhopkins
ms.reviewer: dineshm
ms.date: 08/19/2020
ms.topic: how-to
ms.service: storage
ms.subservice: queues
ms.custom: devx-track-java
ms.openlocfilehash: 997a37ac4252813abf1b35877cd34e192ec3e2ae
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "97585719"
---
# <a name="how-to-use-queue-storage-from-java"></a>Java から Queue Storage を使用する方法

[!INCLUDE [storage-selector-queue-include](../../../includes/storage-selector-queue-include.md)]

## <a name="overview"></a>概要

このガイドでは、Azure Queue Storage サービスを使用して一般的なシナリオのためのコードを作成する方法について説明します。 サンプルは Java で記述され、 [Azure Storage SDK for Java](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/storage)を利用しています。 シナリオには、キュー メッセージの **挿入**、**ピーク**、**取得**、**削除** が含まれます。 キューを **作成** および **削除** するためのコードについても説明します。 キューの詳細については、「[次のステップ](#next-steps)」のセクションを参照してください。

[!INCLUDE [storage-queue-concepts-include](../../../includes/storage-queue-concepts-include.md)]

[!INCLUDE [storage-create-account-include](../../../includes/storage-create-account-include.md)]

## <a name="create-a-java-application"></a>Java アプリケーションの作成

# <a name="java-v12"></a>[Java v12](#tab/java)

最初に、[Java 用の Azure Queue Storage クライアント ライブラリ v12](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/storage/azure-storage-queue) に関する記事に記載されている前提条件が、開発システムによって満たされていることを確認します。

`queues-how-to-v12` という名称の Java アプリケーションを作成するには:

1. コンソール ウィンドウ (cmd、PowerShell、Bash など) で、Maven コマンドを使用し、`queues-how-to-v12` という名前で新しいコンソール アプリを作成します。 次の `mvn` コマンドを入力して "hello world" Java プロジェクトを作成します。

   ```bash
    mvn archetype:generate \
        --define interactiveMode=n \
        --define groupId=com.queues.howto \
        --define artifactId=queues-howto-v12 \
        --define archetypeArtifactId=maven-archetype-quickstart \
        --define archetypeVersion=1.4
   ```

   ```powershell
    mvn archetype:generate `
        --define interactiveMode=n `
        --define groupId=com.queues.howto `
        --define artifactId=queues-howto-v12 `
        --define archetypeArtifactId=maven-archetype-quickstart `
        --define archetypeVersion=1.4
    ```

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
    [INFO] Parameter: groupId, Value: com.queues.howto
    [INFO] Parameter: artifactId, Value: queues-howto-v12
    [INFO] Parameter: version, Value: 1.0-SNAPSHOT
    [INFO] Parameter: package, Value: com.queues.howto
    [INFO] Parameter: packageInPathFormat, Value: com/queues/howto
    [INFO] Parameter: version, Value: 1.0-SNAPSHOT
    [INFO] Parameter: package, Value: com.queues.howto
    [INFO] Parameter: groupId, Value: com.queues.howto
    [INFO] Parameter: artifactId, Value: queues-howto-v12
    [INFO] Project created from Archetype in dir: C:\queues\queues-howto-v12
    [INFO] ------------------------------------------------------------------------
    [INFO] BUILD SUCCESS
    [INFO] ------------------------------------------------------------------------
    [INFO] Total time:  6.775 s
    [INFO] Finished at: 2020-08-17T15:27:31-07:00
    [INFO] ------------------------------------------------------------------------
        ```

1. Switch to the newly created `queues-howto-v12` directory.

   ```console
   cd queues-howto-v12
   ```

### <a name="install-the-package"></a>パッケージをインストールする

テキスト エディターで `pom.xml` ファイルを開きます。 依存関係のグループに、次の dependency 要素を追加します。

```xml
<dependency>
  <groupId>com.azure</groupId>
  <artifactId>azure-storage-queue</artifactId>
  <version>12.6.0</version>
</dependency>
```

# <a name="java-v8"></a>[Java v8](#tab/java8)

最初に、[Java v8 用の Azure Storage SDK](https://github.com/azure/azure-storage-java) に記載されている前提条件が、開発システムによって満たされていることを確認します。 Azure Storage Libraries for Java をダウンロードしてインストールする手順に従います。 その後、この記事の例を使用して Java アプリケーションを作成できます。

---

## <a name="configure-your-application-to-access-queue-storage"></a>Queue ストレージにアクセスするようにアプリケーションを構成する

Azure Storage API を使用してキューにアクセスする Java ファイルの先頭には、次の import ステートメントを追加します。

# <a name="java-v12"></a>[Java v12](#tab/java)

:::code language="java" source="~/azure-storage-snippets/queues/howto/java/java-v12/src/main/java/com/queues/howto/App.java" id="Snippet_ImportStatements":::

# <a name="java-v8"></a>[Java v8](#tab/java8)

```java
// Include the following imports to use queue APIs.
import com.microsoft.azure.storage.*;
import com.microsoft.azure.storage.queue.*;
```

---

## <a name="set-up-an-azure-storage-connection-string"></a>Azure Storage 接続文字列の設定

Azure Storage クライアントでは、データ管理サービスにアクセスするためにストレージ接続文字列が使用されます。 ストレージ アカウントの名前とプライマリ アクセス キーを、[Azure portal](https://portal.azure.com) で確認します。 これらを接続文字列の `AccountName` および `AccountKey` の値として使用します。 この例では、接続文字列を保持する静的フィールドを宣言する方法を示しています。

# <a name="java-v12"></a>[Java v12](#tab/java)

:::code language="java" source="~/azure-storage-snippets/queues/howto/java/java-v12/src/main/java/com/queues/howto/App.java" id="Snippet_ConnectionString":::

# <a name="java-v8"></a>[Java v8](#tab/java8)

```java
// Define the connection-string with your values.
final String storageConnectionString =
    "DefaultEndpointsProtocol=https;" +
    "AccountName=your_storage_account;" +
    "AccountKey=your_storage_account_key";
```

この文字列を、`ServiceConfiguration.cscfg` という名前のサービス構成ファイルに格納できます。 Microsoft Azure ロール内で実行されているアプリの場合は、`RoleEnvironment.getConfigurationSettings` を呼び出して接続文字列にアクセスします。 次の例では、`Setting` という名前の `StorageConnectionString` 要素から接続文字列を取得しています。

```java
// Retrieve storage account from connection-string.
String storageConnectionString =
    RoleEnvironment.getConfigurationSettings().get("StorageConnectionString");
```

---

次のサンプルでは、ストレージ接続文字列が格納されている `String` オブジェクトがあるものとします。

## <a name="how-to-create-a-queue"></a>方法:キューを作成する

# <a name="java-v12"></a>[Java v12](#tab/java)

`QueueClient` オブジェクトには、キューとやりとりするための操作が含まれています。 次のコードでは、`QueueClient` オブジェクトを作成します。 使用するキューを作成するには、`QueueClient` オブジェクトを使用します。

:::code language="java" source="~/azure-storage-snippets/queues/howto/java/java-v12/src/main/java/com/queues/howto/App.java" id="Snippet_CreateQueue":::

# <a name="java-v8"></a>[Java v8](#tab/java8)

`CloudQueueClient` オブジェクトを使用すると、キューの参照オブジェクトを取得できます。 次のコードでは、使用するキューの参照を与える `CloudQueueClient` オブジェクトが作成されます。 キューが存在しない場合は作成できます。

> [!NOTE]
> `CloudStorageAccount` オブジェクトは他の方法でも作成できます。 詳細については、[Azure Storage クライアント SDK リファレンス ドキュメント](https://azure.github.io/azure-sdk-for-java/storage.html)の「`CloudStorageAccount`」を参照してください。

```java
try
{
    // Retrieve storage account from connection-string.
    CloudStorageAccount storageAccount =
       CloudStorageAccount.parse(storageConnectionString);

   // Create the queue client.
   CloudQueueClient queueClient = storageAccount.createCloudQueueClient();

   // Retrieve a reference to a queue.
   CloudQueue queue = queueClient.getQueueReference("myqueue");

   // Create the queue if it doesn't already exist.
   queue.createIfNotExists();
}
catch (Exception e)
{
    // Output the stack trace.
    e.printStackTrace();
}
```

---

## <a name="how-to-add-a-message-to-a-queue"></a>方法:メッセージをキューに追加する

# <a name="java-v12"></a>[Java v12](#tab/java)

既存のキューにメッセージを挿入するには、`sendMessage` メソッドを呼び出します。 メッセージには、文字列 (UTF-8 形式) またはバイト配列のいずれかを指定できます。 キューに文字列メッセージを送信するコードを次に示します。

:::code language="java" source="~/azure-storage-snippets/queues/howto/java/java-v12/src/main/java/com/queues/howto/App.java" id="Snippet_AddMessage":::

# <a name="java-v8"></a>[Java v8](#tab/java8)

既存のキューにメッセージを挿入するには、最初に新しい `CloudQueueMessage`を作成します。 次に、`addMessage` メソッドを呼び出します。 `CloudQueueMessage` は、文字列 (UTF-8 形式) またはバイト配列で作成できます。 次のコードでは、キューが存在しない場合は作成し、メッセージ `Hello, World` を挿入します。

```java
try
{
    // Retrieve storage account from connection-string.
    CloudStorageAccount storageAccount =
       CloudStorageAccount.parse(storageConnectionString);

    // Create the queue client.
    CloudQueueClient queueClient = storageAccount.createCloudQueueClient();

    // Retrieve a reference to a queue.
    CloudQueue queue = queueClient.getQueueReference("myqueue");

    // Create the queue if it doesn't already exist.
    queue.createIfNotExists();

    // Create a message and add it to the queue.
    CloudQueueMessage message = new CloudQueueMessage("Hello, World");
    queue.addMessage(message);
}
catch (Exception e)
{
    // Output the stack trace.
    e.printStackTrace();
}
```

---

## <a name="how-to-peek-at-the-next-message"></a>方法:次のメッセージをピークする

`peekMessage` を呼び出すと、キューの先頭にあるメッセージをキューから削除せずにクイック表示することができます。

# <a name="java-v12"></a>[Java v12](#tab/java)

:::code language="java" source="~/azure-storage-snippets/queues/howto/java/java-v12/src/main/java/com/queues/howto/App.java" id="Snippet_PeekMessage":::

# <a name="java-v8"></a>[Java v8](#tab/java8)

```java
try
{
    // Retrieve storage account from connection-string.
    CloudStorageAccount storageAccount =
       CloudStorageAccount.parse(storageConnectionString);

    // Create the queue client.
    CloudQueueClient queueClient = storageAccount.createCloudQueueClient();

    // Retrieve a reference to a queue.
    CloudQueue queue = queueClient.getQueueReference("myqueue");

    // Peek at the next message.
    CloudQueueMessage peekedMessage = queue.peekMessage();

    // Output the message value.
    if (peekedMessage != null)
    {
      System.out.println(peekedMessage.getMessageContentAsString());
   }
}
catch (Exception e)
{
    // Output the stack trace.
    e.printStackTrace();
}
```

---

## <a name="how-to-change-the-contents-of-a-queued-message"></a>方法:キューに配置されたメッセージの内容を変更する

キュー内のメッセージの内容をインプレースで変更できます。 メッセージで作業タスクが表されている場合は、この機能を使用して、その状態を更新できます。 次のコードでは、キュー メッセージを新しい内容に更新し、表示タイムアウトを設定して 30 秒延長します。 表示タイムアウトを延長すると、クライアントでメッセージの処理をさらに 30 秒続けることができます。 再試行回数も維持できます。 メッセージの再試行回数が *n* 回を超えた場合は、それを削除します。 このシナリオでは、処理されるたびにアプリケーション エラーが発生するメッセージから保護されます。

# <a name="java-v12"></a>[Java v12](#tab/java)

次のコード サンプルでは、メッセージのキューを検索し、内容が検索文字列と一致する最初のメッセージを見つけて、メッセージの内容を変更し、終了します。

:::code language="java" source="~/azure-storage-snippets/queues/howto/java/java-v12/src/main/java/com/queues/howto/App.java" id="Snippet_UpdateSearchMessage":::

# <a name="java-v8"></a>[Java v8](#tab/java8)

次のコード サンプルでは、メッセージのキューを検索し、内容が `Hello, world` と一致する最初のメッセージを見つけて、メッセージの内容を変更し、終了します。

```java
try
{
    // Retrieve storage account from connection-string.
    CloudStorageAccount storageAccount =
        CloudStorageAccount.parse(storageConnectionString);

    // Create the queue client.
    CloudQueueClient queueClient = storageAccount.createCloudQueueClient();

    // Retrieve a reference to a queue.
    CloudQueue queue = queueClient.getQueueReference("myqueue");

    // The maximum number of messages that can be retrieved is 32.
    final int MAX_NUMBER_OF_MESSAGES_TO_PEEK = 32;

    // Loop through the messages in the queue.
    for (CloudQueueMessage message : queue.retrieveMessages(MAX_NUMBER_OF_MESSAGES_TO_PEEK,1,null,null))
    {
        // Check for a specific string.
        if (message.getMessageContentAsString().equals("Hello, World"))
        {
            // Modify the content of the first matching message.
            message.setMessageContent("Updated contents.");
            // Set it to be visible in 30 seconds.
            EnumSet<MessageUpdateFields> updateFields =
                EnumSet.of(MessageUpdateFields.CONTENT,
                MessageUpdateFields.VISIBILITY);
            // Update the message.
            queue.updateMessage(message, 30, updateFields, null, null);
            break;
        }
    }
}
catch (Exception e)
{
    // Output the stack trace.
    e.printStackTrace();
}
```

---

次のコード サンプルでは、キューで最初に参照したメッセージのみが更新されます。

# <a name="java-v12"></a>[Java v12](#tab/java)

:::code language="java" source="~/azure-storage-snippets/queues/howto/java/java-v12/src/main/java/com/queues/howto/App.java" id="Snippet_UpdateFirstMessage":::

# <a name="java-v8"></a>[Java v8](#tab/java8)

```java
try
{
    // Retrieve storage account from connection-string.
    CloudStorageAccount storageAccount =
       CloudStorageAccount.parse(storageConnectionString);

    // Create the queue client.
    CloudQueueClient queueClient = storageAccount.createCloudQueueClient();

    // Retrieve a reference to a queue.
    CloudQueue queue = queueClient.getQueueReference("myqueue");

    // Retrieve the first visible message in the queue.
    CloudQueueMessage message = queue.retrieveMessage();

    if (message != null)
    {
        // Modify the message content.
        message.setMessageContent("Updated contents.");
        // Set it to be visible in 60 seconds.
        EnumSet<MessageUpdateFields> updateFields =
            EnumSet.of(MessageUpdateFields.CONTENT,
            MessageUpdateFields.VISIBILITY);
        // Update the message.
        queue.updateMessage(message, 60, updateFields, null, null);
    }
}
catch (Exception e)
{
    // Output the stack trace.
    e.printStackTrace();
}
```

---

## <a name="how-to-get-the-queue-length"></a>方法:キューの長さを取得する

キュー内のメッセージの概数を取得できます。

# <a name="java-v12"></a>[Java v12](#tab/java)

`getProperties` メソッドから、キューに現在置かれているメッセージの数など、いくつかの値が返されます。 要求の後でメッセージが追加または削除される可能性があるため、これらの値はあくまでも概数です。 `getApproximateMessageCount` メソッドからは、Queue Storage を呼び出さず、`getProperties` を呼び出すことで取得された最後の値が返されます。

:::code language="java" source="~/azure-storage-snippets/queues/howto/java/java-v12/src/main/java/com/queues/howto/App.java" id="Snippet_GetQueueLength":::

# <a name="java-v8"></a>[Java v8](#tab/java8)

`downloadAttributes` メソッドでは、キューに現在置かれているメッセージの数など、いくつかの値が取得されます。 要求の後でメッセージが追加または削除される可能性があるため、これらの値はあくまでも概数です。 `getApproximateMessageCount` メソッドからは、Queue Storage を呼び出さず、`downloadAttributes` を呼び出すことで取得された最後の値が返されます。

```java
try
{
    // Retrieve storage account from connection-string.
    CloudStorageAccount storageAccount =
       CloudStorageAccount.parse(storageConnectionString);

    // Create the queue client.
    CloudQueueClient queueClient = storageAccount.createCloudQueueClient();

    // Retrieve a reference to a queue.
    CloudQueue queue = queueClient.getQueueReference("myqueue");

   // Download the approximate message count from the server.
    queue.downloadAttributes();

    // Retrieve the newly cached approximate message count.
    long cachedMessageCount = queue.getApproximateMessageCount();

    // Display the queue length.
    System.out.println(String.format("Queue length: %d", cachedMessageCount));
}
catch (Exception e)
{
    // Output the stack trace.
    e.printStackTrace();
}
```

---

## <a name="how-to-dequeue-the-next-message"></a>方法:次のメッセージをデキューする

# <a name="java-v12"></a>[Java v12](#tab/java)

コードでは、2 つの手順でキューからメッセージをデキューします。 `receiveMessage`を呼び出すと、キュー内の次のメッセージを取得します。 `receiveMessage` から返されたメッセージは、このキューからメッセージを読み取る他のコードから参照できなくなります。 既定では、このメッセージを参照できない状態は 30 秒間続きます。 また、キューからのメッセージの削除を完了するには、`deleteMessage` を呼び出す必要があります。 コードでのメッセージの処理が失敗した場合でも、この 2 段階のプロセスにより、同じメッセージを取得して再試行できます。 ご自分のコードで、メッセージが処理された直後に `deleteMessage` を呼び出します。

:::code language="java" source="~/azure-storage-snippets/queues/howto/java/java-v12/src/main/java/com/queues/howto/App.java" id="Snippet_DequeueMessage":::

# <a name="java-v8"></a>[Java v8](#tab/java8)

コードでは、2 つの手順でキューからメッセージをデキューします。 `retrieveMessage`を呼び出すと、キュー内の次のメッセージを取得します。 `retrieveMessage` から返されたメッセージは、このキューからメッセージを読み取る他のコードから参照できなくなります。 既定では、このメッセージを参照できない状態は 30 秒間続きます。 また、キューからのメッセージの削除を完了するには、`deleteMessage` を呼び出す必要があります。 コードでのメッセージの処理が失敗した場合でも、この 2 段階のプロセスにより、同じメッセージを取得して再試行できます。 ご自分のコードで、メッセージが処理された直後に `deleteMessage` を呼び出します。

```java
try
{
    // Retrieve storage account from connection-string.
    CloudStorageAccount storageAccount =
        CloudStorageAccount.parse(storageConnectionString);

    // Create the queue client.
    CloudQueueClient queueClient = storageAccount.createCloudQueueClient();

    // Retrieve a reference to a queue.
    CloudQueue queue = queueClient.getQueueReference("myqueue");

    // Retrieve the first visible message in the queue.
    CloudQueueMessage retrievedMessage = queue.retrieveMessage();

    if (retrievedMessage != null)
    {
        // Process the message in less than 30 seconds, and then delete the message.
        queue.deleteMessage(retrievedMessage);
    }
}
catch (Exception e)
{
    // Output the stack trace.
    e.printStackTrace();
}
```

---

## <a name="additional-options-for-dequeuing-messages"></a>メッセージのデキュー用の追加オプション

キューからのメッセージの取得をカスタマイズする方法は 2 つあります。 1 つ目の方法では、メッセージのバッチ (最大 32 個) を取得します。 2 つ目の方法では、コードで各メッセージを完全に処理できるように、非表示タイムアウトの設定を長くまたは短くします。

# <a name="java-v12"></a>[Java v12](#tab/java)

次のコード例では、`receiveMessages` メソッドを使用して、1 回の呼び出しで 20 個のメッセージを取得します。 その後、`for` ループを使用して、各メッセージを処理します。 また、各メッセージの非表示タイムアウトを 5 分 (300 秒) に設定します。 タイムアウトは、すべてのメッセージに対して同時に開始されます。 `receiveMessages` を呼び出してから 5 分経過すると、削除されていないメッセージは再び見えるようになります。

:::code language="java" source="~/azure-storage-snippets/queues/howto/java/java-v12/src/main/java/com/queues/howto/App.java" id="Snippet_DequeueMessages":::

# <a name="java-v8"></a>[Java v8](#tab/java8)

次のコード例では、`retrieveMessages` メソッドを使用して、1 回の呼び出しで 20 個のメッセージを取得します。 その後、`for` ループを使用して、各メッセージを処理します。 また、各メッセージの非表示タイムアウトを 5 分 (300 秒) に設定します。 タイムアウトは、すべてのメッセージに対して同時に開始されます。 `retrieveMessages` を呼び出してから 5 分経過すると、削除されていないメッセージは再び見えるようになります。

```java
try
{
    // Retrieve storage account from connection-string.
    CloudStorageAccount storageAccount =
        CloudStorageAccount.parse(storageConnectionString);

    // Create the queue client.
    CloudQueueClient queueClient = storageAccount.createCloudQueueClient();

    // Retrieve a reference to a queue.
    CloudQueue queue = queueClient.getQueueReference("myqueue");

    // Retrieve 20 messages from the queue with a visibility timeout of 300 seconds.
    for (CloudQueueMessage message : queue.retrieveMessages(20, 300, null, null)) {
        // Do processing for all messages in less than 5 minutes,
        // deleting each message after processing.
        queue.deleteMessage(message);
    }
}
catch (Exception e)
{
    // Output the stack trace.
    e.printStackTrace();
}
```

---

## <a name="how-to-list-the-queues"></a>方法:キューを一覧表示する

# <a name="java-v12"></a>[Java v12](#tab/java)

現在のキューの一覧を取得するには、`QueueServiceClient.listQueues()` メソッドを呼び出します。このメソッドからは、`QueueItem` オブジェクトのコレクションが返されます。

:::code language="java" source="~/azure-storage-snippets/queues/howto/java/java-v12/src/main/java/com/queues/howto/App.java" id="Snippet_ListQueues":::

# <a name="java-v8"></a>[Java v8](#tab/java8)

現在のキューの一覧を取得するには、`CloudQueueClient.listQueues()` メソッドを呼び出します。このメソッドは、`CloudQueue` オブジェクトのコレクションを返します。

```java
try
{
    // Retrieve storage account from connection-string.
    CloudStorageAccount storageAccount =
        CloudStorageAccount.parse(storageConnectionString);

    // Create the queue client.
    CloudQueueClient queueClient =
        storageAccount.createCloudQueueClient();

    // Loop through the collection of queues.
    for (CloudQueue queue : queueClient.listQueues())
    {
        // Output each queue name.
        System.out.println(queue.getName());
    }
}
catch (Exception e)
{
    // Output the stack trace.
    e.printStackTrace();
}
```

---

## <a name="how-to-delete-a-queue"></a>方法:キューを削除する

# <a name="java-v12"></a>[Java v12](#tab/java)

キューおよびキューに格納されているすべてのメッセージを削除するには、`QueueClient` オブジェクトで `delete` メソッドを呼び出します。

:::code language="java" source="~/azure-storage-snippets/queues/howto/java/java-v12/src/main/java/com/queues/howto/App.java" id="Snippet_DeleteMessageQueue":::

# <a name="java-v8"></a>[Java v8](#tab/java8)

キューおよびそれに格納されているすべてのメッセージを削除するには、`CloudQueue` オブジェクトの `deleteIfExists` メソッドを呼び出します。

```java
try
{
    // Retrieve storage account from connection-string.
    CloudStorageAccount storageAccount =
        CloudStorageAccount.parse(storageConnectionString);

    // Create the queue client.
    CloudQueueClient queueClient = storageAccount.createCloudQueueClient();

    // Retrieve a reference to a queue.
    CloudQueue queue = queueClient.getQueueReference("myqueue");

    // Delete the queue if it exists.
    queue.deleteIfExists();
}
catch (Exception e)
{
    // Output the stack trace.
    e.printStackTrace();
}
```

---

[!INCLUDE [storage-check-out-samples-java](../../../includes/storage-check-out-samples-java.md)]

## <a name="next-steps"></a>次のステップ

これで、Queue Storage の基本を学習できました。さらに複雑なストレージ タスクを実行するには、次のリンク先を参照してください。

- [Azure Storage SDK for Java](https://github.com/Azure/Azure-SDK-for-Java)
- [Azure Storage クライアント SDK リファレンス](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/storage)
- [Azure Storage サービス REST API](/rest/api/storageservices/)
- [Azure Storage チーム ブログ](https://techcommunity.Microsoft.com/t5/Azure-storage/bg-p/azurestorageblog)
