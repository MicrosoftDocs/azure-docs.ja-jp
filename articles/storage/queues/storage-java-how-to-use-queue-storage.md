---
title: Java から Queue Storage を使用する方法 | Microsoft Docs
description: Azure Queue サービスを使用して、キューの作成と削除のほか、メッセージの挿入、取得、および削除を行う方法を説明します。 コード サンプルは Java で記述されています。
services: storage
author: roygara
ms.service: storage
ms.tgt_pltfrm: na
ms.devlang: Java
ms.topic: article
ms.date: 12/08/2016
ms.author: rogarana
ms.subservice: queues
ms.openlocfilehash: 7c2fbc168c663cc4dc5981071d40540cfc4797ca
ms.sourcegitcommit: 22ad896b84d2eef878f95963f6dc0910ee098913
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2019
ms.locfileid: "58648488"
---
# <a name="how-to-use-queue-storage-from-java"></a>Java から Queue ストレージを使用する方法
[!INCLUDE [storage-selector-queue-include](../../../includes/storage-selector-queue-include.md)]

[!INCLUDE [storage-check-out-samples-java](../../../includes/storage-check-out-samples-java.md)]

## <a name="overview"></a>概要
このガイドでは、Azure キュー ストレージ サービスを使用して一般的なシナリオを実行する方法について説明します。 サンプルは Java で記述され、 [Azure Storage SDK for Java][Azure Storage SDK for Java]を利用しています。 キュー メッセージの**挿入**、**ピーク**、**取得**、**削除**と、キューの**作成**と**削除**の各シナリオについて説明します。 キューの詳細については、「[次のステップ](#next-steps)」のセクションを参照してください。

注:SDK は、Android デバイスで Azure Storage を使用する開発者向けに用意されています。 詳細については、「[Azure Storage SDK for Android に関するページ][Azure Storage SDK for Android]｣をご覧ください。

[!INCLUDE [storage-queue-concepts-include](../../../includes/storage-queue-concepts-include.md)]

[!INCLUDE [storage-create-account-include](../../../includes/storage-create-account-include.md)]

## <a name="create-a-java-application"></a>Java アプリケーションの作成
このガイドで使用するストレージ機能は、Java アプリケーション内でローカルで実行することも、Azure の Web ロールまたは worker ロールで動作するコード内で実行することもできます。

そのためには、Java Development Kit (JDK) をインストールし、Azure サブスクリプションに Azure ストレージ アカウントを作成する必要があります。 その後、開発システムが、GitHub の [Azure Storage SDK for Java][Azure Storage SDK for Java] リポジトリに示されている最小要件と依存関係を満たしていることを確認する必要があります。 システムがそれらの要件を満たしている場合は、指示に従って、そのリポジトリからシステムに Azure Storage Libraries for Java をダウンロードしてインストールできます。 それらのタスクが完了したら、この記事の例を使用した Java アプリケーションを作成できます。

## <a name="configure-your-application-to-access-queue-storage"></a>キュー ストレージにアクセスするようにアプリケーションを構成する
Azure Storage API を使用してキューにアクセスする Java ファイルの先頭には、次の import ステートメントを追加します。

```java
// Include the following imports to use queue APIs.
import com.microsoft.azure.storage.*;
import com.microsoft.azure.storage.queue.*;
```

## <a name="setup-an-azure-storage-connection-string"></a>Azure のストレージ接続文字列の設定
Azure ストレージ クライアントでは、ストレージ接続文字列を使用して、データ管理サービスにアクセスするためのエンドポイントおよび資格情報を保存します。 クライアント アプリケーションの実行時、ストレージ接続文字列を次の形式で指定する必要があります。*AccountName* と *AccountKey* の値には、[Azure Portal](https://portal.azure.com) に表示されるストレージ アカウントの名前とプライマリ アクセス キーを使います。 この例では、接続文字列を保持する静的フィールドを宣言する方法を示しています。

```java
// Define the connection-string with your values.
public static final String storageConnectionString =
    "DefaultEndpointsProtocol=http;" +
    "AccountName=your_storage_account;" +
    "AccountKey=your_storage_account_key";
```

Microsoft Azure 上のロール内で実行されるアプリケーションでは、この文字列はサービス構成ファイルである *ServiceConfiguration.cscfg*に格納でき、 **RoleEnvironment.getConfigurationSettings** メソッドの呼び出しを使用してアクセスできます。 次の例では、サービス構成ファイル内の **StorageConnectionString** という名前の *Setting* 要素から接続文字列を取得しています。

```java
// Retrieve storage account from connection-string.
String storageConnectionString =
    RoleEnvironment.getConfigurationSettings().get("StorageConnectionString");
```

次のサンプルでは、これら 2 つのメソッドのいずれかを使用してストレージ接続文字列を取得するとします。

## <a name="how-to-create-a-queue"></a>方法:キューを作成する
**CloudQueueClient** オブジェクトを使用すると、キューの参照オブジェクトを取得できます。 次のコードでは、**CloudQueueClient** オブジェクトを作成します。 (注: **CloudStorageAccount** オブジェクトを作成する方法は他にもあります。詳しくは、「[Azure ストレージ クライアント SDK リファレンス]」の **CloudStorageAccount** をご覧ください。)

**CloudQueueClient** オブジェクトを使用して、使用するキューへの参照を取得します。 キューが存在しない場合は作成できます。

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

## <a name="how-to-add-a-message-to-a-queue"></a>方法:メッセージをキューに追加する
既存のキューにメッセージを挿入するには、最初に新しい **CloudQueueMessage**を作成します。 次に、 **addMessage** メソッドを呼び出します。 **CloudQueueMessage** は、文字列 (UTF-8 形式) またはバイト配列から作成できます。 次のコードでは、キューが存在しない場合は作成し、メッセージ "Hello, World" を挿入します。

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

## <a name="how-to-peek-at-the-next-message"></a>方法:次のメッセージをピークする
**peekMessage**を呼び出すと、キューの先頭にあるメッセージをキューから削除せずにピークできます。

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

## <a name="how-to-change-the-contents-of-a-queued-message"></a>方法:キューに配置されたメッセージの内容を変更する
キュー内のメッセージの内容をインプレースで変更できます。 メッセージが作業タスクを表している場合は、この機能を使用して、作業タスクの状態を更新できます。 次のコードでは、キュー メッセージを新しい内容に更新し、表示タイムアウトを設定して、60 秒延長します。 これにより、メッセージに関連付けられている作業の状態が保存され、クライアントにメッセージの操作を続行する時間が 1 分与えられます。 この方法を使用すると、キュー メッセージに対する複数の手順から成るワークフローを追跡でき、ハードウェアまたはソフトウェアの問題が原因で処理手順が失敗した場合に最初からやり直す必要がなくなります。 通常は、さらに再試行回数を保持し、メッセージの再試行回数が *n* 回を超えた場合はメッセージを削除するようにします。 こうすることで、処理するたびにアプリケーション エラーをトリガーするメッセージから保護されます。

次のコード サンプルでは、キュー内でメッセージを検索し、内容が "Hello, World" に一致する最初のメッセージを見つけ、メッセージの内容を変更し、終了します。

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

また、次のコード サンプルでは、キューの最初に参照したメッセージのみを更新します。

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

## <a name="how-to-get-the-queue-length"></a>方法:キューの長さを取得する
キュー内のメッセージの概数を取得できます。 **downloadAttributes** メソッドは、キューにあるメッセージの数など、現在の値をキュー サービスに要求します。 キュー サービスが要求に応答した後にメッセージが追加または削除される可能性があるため、これらの値は概数にすぎません。 **getApproximateMessageCount** メソッドは、Queue サービスを呼び出さずに、**downloadAttributes** の呼び出しによって取得された最後の値を返します。

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

## <a name="how-to-dequeue-the-next-message"></a>方法:次のメッセージをデキューする
コードでは、2 つの手順でキューからメッセージをデキューします。 **retrieveMessage**を呼び出すと、キュー内の次のメッセージを取得します。 **retrieveMessage** から返されたメッセージは、このキューからメッセージを読み取る他のコードから参照できなくなります。 既定では、このメッセージを参照できない状態は 30 秒間続きます。 また、キューからのメッセージの削除を完了するには、 **deleteMessage**を呼び出す必要があります。 このようにメッセージを 2 つの手順で削除することで、ハードウェアまたはソフトウェアの問題が原因でコードによるメッセージの処理が失敗した場合に、コードの別のインスタンスで同じメッセージを取得し、もう一度処理することができます。 コードでは、メッセージが処理された直後に **deleteMessage** を呼び出します。

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

## <a name="additional-options-for-dequeuing-messages"></a>メッセージのデキュー用の追加オプション
キューからのメッセージの取得をカスタマイズする方法は 2 つあります。 1 つ目の方法では、(最大 32 個の) メッセージのバッチを取得できます。 2 つ目の方法では、コードで各メッセージを完全に処理できるように、非表示タイムアウトの設定を長くまたは短くすることができます。

次のコード例では、**retrieveMessages** メソッドを使用して、1 回の呼び出しで 20 個のメッセージを取得します。 その後、**for** ループを使用して、各メッセージを処理します。 また、各メッセージの非表示タイムアウトを 5 分 (300 秒) に設定します。 この 5 分の非表示期間は、すべてのメッセージに対して同時に開始します。そのため、**retrieveMessages** の呼び出しから 5 分が経過すると、削除されていないすべてのメッセージが再び表示されます。

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

## <a name="how-to-list-the-queues"></a>方法:キューを一覧表示する
現在のキューの一覧を取得するには、**CloudQueueClient.listQueues()** メソッドを呼び出します。このメソッドは、**CloudQueue** オブジェクトのコレクションを返します。

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

## <a name="how-to-delete-a-queue"></a>方法:キューを削除する
キューおよびキューに格納されているすべてのメッセージを削除するには、**CloudQueue** オブジェクトの **deleteIfExists** メソッドを呼び出します。

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

## <a name="next-steps"></a>次の手順
これで、Queue ストレージの基本を学習できました。さらに複雑なストレージ タスクを実行するには、次のリンク先を参照してください。

* [Azure Storage SDK for Java][Azure Storage SDK for Java]
* [Azure ストレージ クライアント SDK リファレンス][Azure ストレージ クライアント SDK リファレンス]
* [Azure Storage Services REST API (Azure Storage サービスの REST API)][Azure Storage Services REST API]
* [Azure Storage チーム ブログ][Azure Storage Team Blog]

[Azure SDK for Java]: https://go.microsoft.com/fwlink/?LinkID=525671
[Azure Storage SDK for Java]: https://github.com/azure/azure-storage-java
[Azure Storage SDK for Android]: https://github.com/azure/azure-storage-android
[Azure ストレージ クライアント SDK リファレンス]: http://dl.windowsazure.com/storage/javadoc/
[Azure Storage Services REST API]: https://msdn.microsoft.com/library/azure/dd179355.aspx
[Azure Storage Team Blog]: https://blogs.msdn.com/b/windowsazurestorage/
