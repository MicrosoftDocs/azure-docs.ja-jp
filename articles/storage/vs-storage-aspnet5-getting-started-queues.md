---
title: "Queue Storage と Visual Studio 接続済みサービスの概要 (ASP.NET 5) | Microsoft Docs"
description: "Visual Studio の ASP.NET 5 プロジェクトで Azure キュー ストレージの使用を開始する方法"
services: storage
documentationcenter: 
author: TomArcher
manager: douge
editor: 
ms.assetid: 04977069-5b2d-4cba-84ae-9fb2f5eb1006
ms.service: storage
ms.workload: web
ms.tgt_pltfrm: vs-getting-started
ms.devlang: na
ms.topic: article
ms.date: 12/02/2016
ms.author: tarcher
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: b166ced70b845dd297a1eb87aaec5456ec3e0b31


---
# <a name="get-started-with-queue-storage-and-visual-studio-connected-services-aspnet-5"></a>キュー ストレージと Visual Studio 接続済みサービスの概要 (ASP.NET 5)
[!INCLUDE [storage-try-azure-tools-queues](../../includes/storage-try-azure-tools-queues.md)]

## <a name="overview"></a>概要
この記事では、Visual Studio の **[Add Connected Services (接続済みサービスの追加)]** ダイアログを使用して ASP.NET 5 プロジェクトで Azure ストレージ アカウントを作成または参照した後に、Visual Studio で Azure Queue Storage の使用を開始する方法について説明します。 **接続済みサービスの追加** 操作によって、プロジェクト内の Azure ストレージにアクセスする適切な NuGet パッケージがインストールされ、プロジェクトの構成ファイルに、ストレージ アカウントの接続文字列が追加されます。

Azure キュー ストレージは、HTTP または HTTPS を使用した認証された呼び出しを介して世界中のどこからでもアクセスできる大量のメッセージを格納するためのサービスです。 キューの 1 つのメッセージの最大サイズは 64 キロバイト (KB) で、1 つのキューには、ストレージ アカウントの合計容量の上限に達するまで、数百万のメッセージを格納できます。

最初に、ストレージ アカウント内に Azure キューを作成する必要があります。 コードでキューを作成する方法を説明します。 また、キュー メッセージの追加、変更、読み取り、削除などの基本的なキュー操作を実行する方法についても説明します。 例は C\# のコードで記述され、Azure .NET 用ストレージ クライアント ライブラリを利用しています。 ASP.NET の詳細については、 [ASP.NET](http://www.asp.net)に関するページを参照してください。

**注:** ASP.NET 5 で Azure ストレージへの呼び出しを実行する API の一部は非同期です。 詳細については、 [Async および Await を使用した非同期プログラミング](http://msdn.microsoft.com/library/hh191443.aspx) に関するページをご覧ください。 次のコードでは、非同期のプログラミング方法を使用していることを前提としています。

* プログラムを使用してキューを操作する方法の詳細については、「 [.NET を使用して Azure Queue Storage を使用する](storage-dotnet-how-to-use-queues.md) 」をご覧ください。
* Azure Storage の一般情報については、「 [ストレージのドキュメント](https://azure.microsoft.com/documentation/services/storage/) 」をご覧ください。
* Azure Cloud Services の一般情報については、「 [Cloud Services のドキュメント](https://azure.microsoft.com/documentation/services/cloud-services/) 」をご覧ください。
* ASP.NET アプリケーションのプログラミングについての詳細は、「 [ASP.NET](http://www.asp.net) 」をご覧ください。

## <a name="access-queues-in-code"></a>コードでキューにアクセスする
ASP.NET 5 プロジェクトでキューにアクセスするには、Azure キュー ストレージにアクセスする C# ソース ファイルに、次の項目を含める必要があります。

1. C# ファイル冒頭の名前空間宣言に、次の **using** ステートメントが含まれていることを確認します。
   
        using Microsoft.Framework.Configuration;
        using Microsoft.WindowsAzure.Storage;
        using Microsoft.WindowsAzure.Storage.Queue;
        using System.Threading.Tasks;
        using LogLevel = Microsoft.Framework.Logging.LogLevel;
2. ストレージ アカウント情報を表す **CloudStorageAccount** オブジェクトを取得します。 次のコードを使用して、Azure サービス構成からストレージ接続文字列とストレージ アカウント情報を取得できます。
   
         CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
           CloudConfigurationManager.GetSetting("<storage-account-name>_AzureStorageConnectionString"));
3. ストレージ アカウント内のキュー オブジェクトを参照する **CloudQueueClient** オブジェクトを取得します。  
   
        // Create the CloudQueueClient object for the storage account.
        CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();
4. 特定のキューを参照する **CloudQueue** オブジェクトを取得します。
   
        // Get a reference to the CloudQueue named "messageQueue"
        CloudQueue messageQueue = queueClient.GetQueueReference("messageQueue");

**注:** 上記のコードはすべて、以下の例に示すコードの前に使用してください。

### <a name="create-a-queue-in-code"></a>コードでキューを作成する
**CreateIfNotExistsAsync**への呼び出しを追加するだけで、コード内に Azure キューが作成されます。

    // Create the CloudQueue if it does not exist.
    await messageQueue.CreateIfNotExistsAsync();

## <a name="add-a-message-to-a-queue"></a>メッセージをキューに追加する
既存のキューにメッセージを挿入するには、新しい **CloudQueueMessage** オブジェクトを作成した後、**AddMessageAsync** メソッドを呼び出します。

**CloudQueueMessage** オブジェクトは、文字列 (UTF-8 形式) またはバイト配列から作成できます。

次の例では、'Hello, World' というメッセージを挿入します。

    // Create a message and add it to the queue.
    CloudQueueMessage message = new CloudQueueMessage("Hello, World");
    await messageQueue.AddMessageAsync(message);

## <a name="read-a-message-in-a-queue"></a>キュー内のメッセージを読み取る
**PeekMessageAsync** メソッドを呼び出すことで、キューの先頭にあるメッセージをキューから削除せずにピークできます。

    // Peek the next message in the queue. 
    CloudQueueMessage peekedMessage = await messageQueue.PeekMessageAsync();


## <a name="read-and-remove-a-message-in-a-queue"></a>キュー内のメッセージを読み取って削除する
コードでは、2 つの手順でキューからメッセージを削除 (デキュー) できます。

1. **GetMessageAsync** を呼び出して、キュー内の次のメッセージを取得します。 **GetMessageAsync** から返されたメッセージは、このキューからメッセージを読み取る他のコードから参照できなくなります。 既定では、このメッセージを参照できない状態は 30 秒間続きます。
2. **DeleteMessageAsync**を呼び出して、キューからのメッセージの削除を終了します。

このようにメッセージを 2 つの手順で削除することで、ハードウェアまたはソフトウェアの問題が原因でコードによるメッセージの処理が失敗した場合に、コードの別のインスタンスで同じメッセージを取得し、もう一度処理することができます。 次のコードでは、メッセージが処理された直後に **DeleteMessageAsync** を呼び出しています。

    // Get the next message in the queue.
    CloudQueueMessage retrievedMessage = await messageQueue.GetMessageAsync();

    // Process the message in less than 30 seconds.

    // Then delete the message.
    await messageQueue.DeleteMessageAsync(retrievedMessage);

## <a name="leverage-additional-options-for-dequeuing-messages"></a>追加オプションを利用してメッセージをデキューする
キューからのメッセージの取得をカスタマイズする方法は 2 つあります。
1 つ目の方法では、(最大 32 個の) メッセージのバッチを取得できます。 2 つ目の方法では、コードで各メッセージを完全に処理できるように、非表示タイムアウトの設定を長くまたは短くすることができます。 次のコード例では、**GetMessages** メソッドを使用して、1 回の呼び出しで 20 個のメッセージを取得します。 その後、**foreach** ループを使用して、各メッセージを処理します。 また、各メッセージの非表示タイムアウトを 5 分に設定します。 この 5 分の非表示期間は、すべてのメッセージに対して同時に開始されます。そのため、**GetMessages** の呼び出しから 5 分が経過すると、削除されていないすべてのメッセージが再び表示されます。

    // Retrieve 20 messages at a time, keeping those messages invisible for 5 minutes, 
    //   delete each message after processing.

    foreach (CloudQueueMessage message in messageQueue.GetMessages(20, TimeSpan.FromMinutes(5)))
    {
        // Process all messages in less than 5 minutes, deleting each message after processing.
        queue.DeleteMessage(message);
    }

## <a name="get-the-queue-length"></a>キューの長さを取得する
キュー内のメッセージの概数を取得できます。 **FetchAttributes** メソッドは、メッセージ数などのキューの属性を取得するようにキュー サービスに要求します。 **ApproximateMethodCount** プロパティは、Queue サービスを呼び出さずに、**FetchAttributes** メソッドによって取得された最後の値を返します。

    // Fetch the queue attributes.
    messageQueue.FetchAttributes();

    // Retrieve the cached approximate message count.
    int? cachedMessageCount = messageQueue.ApproximateMessageCount;

    // Display the number of messages.
    Console.WriteLine("Number of messages in queue: " + cachedMessageCount);

## <a name="use-the-async-await-pattern-with-common-queue-apis"></a>Async-Await パターンを一般的なキュー API で使用する
この例では、Async-Await パターンを一般的なキュー API で使用する方法を示します。 サンプルでは、特定のメソッドの非同期バージョンをそれぞれ呼び出します。 これは、各メソッドの Async 接尾辞によって確認できます。 非同期のメソッドを使用するとき、Async-Await パターンは、呼び出しが完了するまでローカルでの実行を中断します。 この動作により、現在のスレッドで別の作業を実行できるようになるため、パフォーマンスのボトルネックを回避し、アプリケーションの全体的な応答性が向上させることができます。 .NET での Async-Await パターンの使用方法の詳細については、 [Async と Await (C# と Visual Basic)](https://msdn.microsoft.com/library/hh191443.aspx)

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
## <a name="delete-a-queue"></a>キューを削除する
キューおよびキューに格納されているすべてのメッセージを削除するには、キュー オブジェクトの **Delete** メソッドを呼び出します。

    // Delete the queue.
    messageQueue.Delete();


## <a name="next-steps"></a>次のステップ
[!INCLUDE [vs-storage-dotnet-queues-next-steps](../../includes/vs-storage-dotnet-queues-next-steps.md)]




<!--HONumber=Nov16_HO3-->


