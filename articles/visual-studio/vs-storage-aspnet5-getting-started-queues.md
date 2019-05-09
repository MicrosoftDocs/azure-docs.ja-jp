---
title: Queue Storage と Visual Studio 接続済みサービスの概要 (ASP.NET Core) | Microsoft Docs
description: Visual Studio の ASP.NET Core プロジェクトで Azure Queue Storage の使用を開始する方法
services: storage
author: ghogen
manager: douge
ms.assetid: 04977069-5b2d-4cba-84ae-9fb2f5eb1006
ms.prod: visual-studio-dev15
ms.technology: vs-azure
ms.custom: vs-azure
ms.workload: azure-vs
ms.topic: article
ms.date: 11/14/2017
ms.author: ghogen
ms.openlocfilehash: 8a91614e7dfb804e6a902967ce60f898ed0e54ad
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2019
ms.locfileid: "57999744"
---
# <a name="get-started-with-queue-storage-and-visual-studio-connected-services-aspnet-core"></a>Queue Storage と Visual Studio 接続済みサービスの概要 (ASP.NET Core)

[!INCLUDE [storage-try-azure-tools-queues](../../includes/storage-try-azure-tools-queues.md)]

この記事では、Visual Studio の**接続済みサービス**機能を使用して ASP.NET Core プロジェクトで Azure ストレージ アカウントを参照または作成した後に、Visual Studio で Azure Queue Storage の使用を開始する方法について説明します。 **接続済みサービス**の操作によって、プロジェクト内の Azure Storage にアクセスする適切な NuGet パッケージがインストールされ、プロジェクトの構成ファイルにストレージ アカウントの接続文字列が追加されます。 (Azure Storage の一般情報については、「[Azure Storage のドキュメント](https://azure.microsoft.com/documentation/services/storage/)」をご覧ください。)

Azure キュー ストレージは、HTTP または HTTPS を使用した認証された呼び出しを介して世界中のどこからでもアクセスできる大量のメッセージを格納するためのサービスです。 キューの 1 つのメッセージの最大サイズは 64 キロバイト (KB) で、1 つのキューには、ストレージ アカウントの合計容量の上限に達するまで、数百万のメッセージを格納できます。 プログラムを使用してキューを操作する方法の詳細については、「[.NET を使用して Azure Queue Storage を使用する](../storage/queues/storage-dotnet-how-to-use-queues.md)」もご覧ください。

最初に、ストレージ アカウント内に Azure キューを作成します。 そしてこの記事では、C# でキューを作成する方法と、キュー メッセージの追加、変更、読み取り、および削除などの基本的なキュー操作を実行する方法を示します。  コードでは .NET 用の Azure Storage クライアント ライブラリを使用します。 ASP.NET の詳細については、 [ASP.NET](https://www.asp.net)に関するページを参照してください。

一部の Azure Storage API は非同期であるため、この記事のコードでは非同期メソッドが使用されている前提とします。 詳細については、[非同期プログラミング](https://docs.microsoft.com/dotnet/csharp/async)に関するページをご覧ください。

## <a name="access-queues-in-code"></a>コードでキューにアクセスする

ASP.NET Core プロジェクトでキューにアクセスするには、Azure Queue Storage にアクセスする C# ソース ファイルに、次の項目を含めます。 このコードはすべて、以下のセクションのコードの前に使用してください。

1. 必要な `using` ステートメントを追加します。
    ```cs
    using Microsoft.Framework.Configuration;
    using Microsoft.WindowsAzure.Storage;
    using Microsoft.WindowsAzure.Storage.Queue;
    using System.Threading.Tasks;
    using LogLevel = Microsoft.Framework.Logging.LogLevel;
    ```

1. お客様のストレージ アカウント情報を表す `CloudStorageAccount` オブジェクトを取得します。 次のコードを使用して、Azure サービス構成からストレージ接続文字列とストレージ アカウント情報を取得します。

    ```cs
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("<storage-account-name>_AzureStorageConnectionString"));
    ```

1. ストレージ アカウント内のキュー オブジェクトを参照する `CloudQueueClient` オブジェクトを取得します。

    ```cs
    // Create the CloudQueueClient object for the storage account.
    CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();
    ```
1. 特定のキューを参照する `CloudQueue` オブジェクトを取得します。

    ```cs
    // Get a reference to the CloudQueue named "messagequeue"
    CloudQueue messageQueue = queueClient.GetQueueReference("messagequeue");
    ```

### <a name="create-a-queue-in-code"></a>コードでキューを作成する

Azure キューをコードで作成するには、`CreateIfNotExistsAsync` を呼び出します。

```cs
// Create the CloudQueue if it does not exist.
await messageQueue.CreateIfNotExistsAsync();
```

## <a name="add-a-message-to-a-queue"></a>メッセージをキューに追加する

既存のキューにメッセージを挿入するには、新しい `CloudQueueMessage` オブジェクトを作成した後、`AddMessageAsync` メソッドを呼び出します。 `CloudQueueMessage` オブジェクトは、文字列 (UTF-8 形式) またはバイト配列で作成できます。

```cs
// Create a message and add it to the queue.
CloudQueueMessage message = new CloudQueueMessage("Hello, World");
await messageQueue.AddMessageAsync(message);
```

## <a name="read-a-message-in-a-queue"></a>キュー内のメッセージを読み取る

`PeekMessageAsync` メソッドを呼び出すと、キューの先頭にあるメッセージをキューから削除せずにピークできます。

```cs
// Peek the next message in the queue.
CloudQueueMessage peekedMessage = await messageQueue.PeekMessageAsync();
```

## <a name="read-and-remove-a-message-in-a-queue"></a>キュー内のメッセージを読み取って削除する

コードでは、2 つの手順でキューからメッセージを削除 (デキュー) できます。

1. `GetMessageAsync` を呼び出して、キュー内の次のメッセージを取得します。 `GetMessageAsync` から返されたメッセージは、このキューからメッセージを読み取る他のコードから参照できなくなります。 既定では、このメッセージを参照できない状態は 30 秒間続きます。
1. キューからのメッセージの削除を完了するには、`DeleteMessageAsync` を呼び出します。

このようにメッセージを 2 つの手順で削除することで、ハードウェアまたはソフトウェアの問題が原因でコードによるメッセージの処理が失敗した場合に、コードの別のインスタンスで同じメッセージを取得し、もう一度処理することができます。 次のコードは、メッセージが処理された直後に `DeleteMessageAsync` を呼び出します。

```cs
// Get the next message in the queue.
CloudQueueMessage retrievedMessage = await messageQueue.GetMessageAsync();

// Process the message in less than 30 seconds.

// Then delete the message.
await messageQueue.DeleteMessageAsync(retrievedMessage);
```

## <a name="additional-options-for-dequeuing-messages"></a>メッセージのデキュー用の追加オプション

キューからのメッセージの取得をカスタマイズする方法は 2 つあります。 1 つ目の方法では、(最大 32 個の) メッセージのバッチを取得できます。 2 つ目の方法では、コードで各メッセージを完全に処理できるように、非表示タイムアウトの設定を長くまたは短くすることができます。 次のコード例では、`GetMessages` メソッドを使用して、1 回の呼び出しで 20 個のメッセージを取得します。 その後、`foreach` ループを使用して、各メッセージを処理します。 また、各メッセージの非表示タイムアウトを 5 分に設定します。 この 5 分のタイマーは、すべてのメッセージに対して同時に開始します。そのため、5 分が経過すると、削除されていないすべてのメッセージが再び表示されます。

```cs
// Retrieve 20 messages at a time, keeping those messages invisible for 5 minutes, 
//   delete each message after processing.

foreach (CloudQueueMessage message in messageQueue.GetMessages(20, TimeSpan.FromMinutes(5)))
{
    // Process all messages in less than 5 minutes, deleting each message after processing.
    queue.DeleteMessage(message);
}
```

## <a name="get-the-queue-length"></a>キューの長さを取得する

キュー内のメッセージの概数を取得できます。 `FetchAttributes` メソッドは、メッセージ数などのキューの属性を取得するように Queue サービスに要求します。 `ApproximateMethodCount` プロパティは、Queue サービスを呼び出さずに、`FetchAttributes` メソッドによって取得された最後の値を返します。

```cs
// Fetch the queue attributes.
messageQueue.FetchAttributes();

// Retrieve the cached approximate message count.
int? cachedMessageCount = messageQueue.ApproximateMessageCount;

// Display the number of messages.
Console.WriteLine("Number of messages in queue: " + cachedMessageCount);
```

## <a name="use-the-async-await-pattern-with-common-queue-apis"></a>Async-Await パターンを一般的なキュー API で使用する

この例では、`Async` で終わる一般的なキュー API で Async-Await パターンを使用する方法を示します。 非同期のメソッドを使用する場合、Async-Await パターンは呼び出しが完了するまでローカルでの実行を中断します。 この動作により、現在のスレッドで、パフォーマンスのボトルネックを回避し、アプリケーションの全体的な応答性を向上させるような別の作業を実行できるようになります。

```cs
// Create a message to add to the queue.
CloudQueueMessage cloudQueueMessage = new CloudQueueMessage("My message");

// Async enqueue the message.
await messageQueue.AddMessageAsync(cloudQueueMessage);
Console.WriteLine("Message added");

// Async dequeue the message.
CloudQueueMessage retrievedMessage = await messageQueue.GetMessageAsync();
Console.WriteLine("Retrieved message with content '{0}'", retrievedMessage.AsString);

// Async delete the message.
await messageQueue.DeleteMessageAsync(retrievedMessage);
Console.WriteLine("Deleted message");
```

## <a name="delete-a-queue"></a>キューを削除する

キューおよびキューに格納されているすべてのメッセージを削除するには、キュー オブジェクトの `Delete` メソッドを呼び出します。

```cs
// Delete the queue.
messageQueue.Delete();
```

## <a name="next-steps"></a>次の手順

[!INCLUDE [vs-storage-dotnet-queues-next-steps](../../includes/vs-storage-dotnet-queues-next-steps.md)]
