---
title: "Web ジョブ SDK を使用して Azure キュー ストレージを操作する方法"
description: "Web ジョブ SDK を使用して Azure キュー ストレージを操作する方法について説明します。 キューの作成と削除のほか、キュー メッセージの挿入、ピーク、取得、削除を行います。"
services: app-service\web, storage
documentationcenter: .net
author: tdykstra
manager: erikre
editor: jimbe
ms.assetid: dbfac5d9-f4a0-4e3e-9ecc-af3d7bf80463
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 06/01/2016
ms.author: glenga
translationtype: Human Translation
ms.sourcegitcommit: fcbd9e10e4cc336dc6ea37f84201249e14b1af91
ms.openlocfilehash: 5110a86c3cc25ada27ddba9b0caef68e4509aa73
ms.lasthandoff: 02/16/2017


---
# <a name="how-to-use-azure-queue-storage-with-the-webjobs-sdk"></a>Web ジョブ SDK を使用して Azure キュー ストレージを操作する方法
## <a name="overview"></a>概要
このガイドでは、Azure キュー ストレージ サービスを使用して Azure Web ジョブ SDK バージョン 1.x を操作する方法について説明します。

このガイドは、Visual Studio で[ストレージ アカウント](websites-dotnet-webjobs-sdk-get-started.md)または[複数のストレージ アカウント](https://github.com/Azure/azure-webjobs-sdk/blob/master/test/Microsoft.Azure.WebJobs.Host.EndToEndTests/MultipleStorageAccountsEndToEndTests.cs)を指定する接続文字列を使って Web ジョブ プロジェクトを作成する方法を理解していることを前提としています。

ほとんどのコード スニペットは、この例のように `JobHost` オブジェクトを作成するコードではなく、関数のみを示しています。

        static void Main(string[] args)
        {
            JobHost host = new JobHost();
            host.RunAndBlock();
        }

このガイドには、次のトピックが含まれています。

* [キュー メッセージを受信したときに関数をトリガーする方法](#trigger)
  * 文字列のキュー メッセージ
  * POCO キュー メッセージ
  * Async 関数
  * QueueTrigger 属性が連携する種類
  * ポーリング アルゴリズム
  * 複数のインスタンス
  * 並列実行
  * キューまたはキュー メッセージ メタデータの取得
  * グレースフル シャットダウン
* [キュー メッセージの処理中にキュー メッセージを作成する方法](#createqueue)
  * 文字列のキュー メッセージ
  * POCO キュー メッセージ
  * (非同期関数での) 複数のメッセージの作成
  * キューの属性が連携する種類
  * 関数本体での Web ジョブ SDK 属性の使用
* [キュー メッセージの処理中に、BLOB の読み書きをする方法](#blobs)
  * 文字列のキュー メッセージ
  * POCO キュー メッセージ
  * BLOB の属性が連携する種類
* [有害メッセージの処理方法](#poison)
  * 有害メッセージの自動処理
  * 有害メッセージの手動処理
* [構成オプションの設定方法](#config)
  * コード内で SDK の接続文字列を設定する
  * QueueTrigger 設定の構成
  * コードの Web ジョブ SDK コンストラクター パラメーター値の設定
* [関数を手動でトリガーする方法](#manual)
* [ログを書き込む方法](#logs)
* [エラーを処理する方法とタイムアウトを構成する方法](#errors)
* [次のステップ](#nextsteps)

## <a id="trigger"></a> キュー メッセージを受信したときに関数をトリガーする方法
キュー メッセージを受信したときに WebJobs SDK が呼び出す関数を記述するには、 `QueueTrigger` 属性を使用します。 属性コンストラクターは、ポーリングのためにキューの名前を指定する文字列パラメーター受け取ります。 また、 [キューの名前を動的に設定する](#config)ことも可能です。

### <a name="string-queue-messages"></a>文字列のキュー メッセージ
次の例では、キューに文字列メッセージが含まれます。キューメッセージの内容が含まれる `logMessage` と呼ばれる文字列パラメーターに `QueueTrigger` が適用されます。 この関数は[ダッシュボードにログ メッセージを書き込みます](#logs)。

        public static void ProcessQueueMessage([QueueTrigger("logqueue")] string logMessage, TextWriter logger)
        {
            logger.WriteLine(logMessage);
        }

`string` だけでなく、パラメーターにはバイト配列、`CloudQueueMessage` オブジェクト、自分で定義した POCO があります。

### <a name="poco-plain-old-clr-objecthttpenwikipediaorgwikiplainoldclrobject-queue-messages"></a>POCO ( [Plain Old CLR Object](http://en.wikipedia.org/wiki/Plain_Old_CLR_Object)) キュー メッセージ
次の例では、キュー メッセージには `BlobName` プロパティを含む `BlobInformation` オブジェクトの JSON が含まれます。 SDK は自動的にオブジェクトを逆シリアル化します。

        public static void WriteLogPOCO([QueueTrigger("logqueue")] BlobInformation blobInfo, TextWriter logger)
        {
            logger.WriteLine("Queue message refers to blob: " + blobInfo.BlobName);
        }

SDK は [Newtonsoft.Json NuGet パッケージ](http://www.nuget.org/packages/Newtonsoft.Json) を使用してメッセージのシリアル化と逆シリアル化を行います。 キュー メッセージを Web ジョブ SDK を使用しないプログラムで作成する場合は、SDK が解析できる POCO キュー メッセージを作成する次の例のようなコードを記述できます。

        BlobInformation blobInfo = new BlobInformation() { BlobName = "log.txt" };
        var queueMessage = new CloudQueueMessage(JsonConvert.SerializeObject(blobInfo));
        logQueue.AddMessage(queueMessage);

### <a name="async-functions"></a>Async 関数
次の async 関数は [ダッシュボードにログを書き込みます](#logs)。

        public async static Task ProcessQueueMessageAsync([QueueTrigger("logqueue")] string logMessage, TextWriter logger)
        {
            await logger.WriteLineAsync(logMessage);
        }

Async 関数は、BLOB をコピーする次の例が示すように、[キャンセル トークン](http://www.asp.net/mvc/overview/performance/using-asynchronous-methods-in-aspnet-mvc-4#CancelToken)を必要とする場合があります  (`queueTrigger` プレースホルダーの詳細については、[Blobs](#blobs) のセクションを参照してください)。

        public async static Task ProcessQueueMessageAsyncCancellationToken(
            [QueueTrigger("blobcopyqueue")] string blobName,
            [Blob("textblobs/{queueTrigger}",FileAccess.Read)] Stream blobInput,
            [Blob("textblobs/{queueTrigger}-new",FileAccess.Write)] Stream blobOutput,
            CancellationToken token)
        {
            await blobInput.CopyToAsync(blobOutput, 4096, token);
        }

### <a id="qtattributetypes"></a> QueueTrigger 属性が連携する種類
次の種類の `QueueTrigger` を使用できます。

* `string`
* JSON としてシリアル化された POCO の型
* `byte[]`
* `CloudQueueMessage`

### <a id="polling"></a> ポーリング アルゴリズム
SDK はランダムな指数バックオフ アルゴリズムを実装することで、ストレージ トランザクション コストにおけるアイドル状態のキューのポーリングの影響を軽減しています。  SDK はメッセージを見つけると 2 秒間待ってから別のメッセージを確認します。メッセージが見つからなかった場合は、約 4 秒間待ってから再試行します。 再試行後もキュー メッセージが取得できなかった場合、待ち時間が最大になるまで再試行が続けられます。既定の最大待ち時間は 1 分間です。 [この最大待機時間の設定は変更可能です](#config)。

### <a id="instances"></a> 複数のインスタンス
Web アプリが複数のインスタンス上で稼働している場合、継続的な Web ジョブは各マシン上で実行され、各マシンがトリガーを待機して関数の実行を試行します。 Web ジョブ SDK キュー トリガーにより、関数がキュー メッセージを複数回処理するのを自動的に回避できます。関数はべき等として記述する必要はありません。 ただし、ホスト Web アプリの複数のインスタンスがある場合でも、関数の 1 つのインスタンスのみを実行するには `Singleton` 属性を使用できます。

### <a id="parallel"></a> 並列実行
異なるキューをリッスンする複数の関数を使用している場合、複数のメッセージを同時に受信したとき、SDK では並行してそれらを呼び出します。

1 つのキューに対して複数のメッセージが受信される場合も同様に処理されます。 既定では、SDK は一度にキュー メッセージ 16 個のバッチを取得し、それらを並列処理する関数を実行します。 [バッチ サイズの設定は変更可能です](#config)。 処理中のメッセージの数がバッチ サイズの半分まで減少すると、SDK は別のバッチを取得し、そのメッセージの処理を開始します。 そのため、1 つの関数につき同時に処理されるメッセージの最大数は、バッチ サイズの 1.5 倍です。 この制限は、 `QueueTrigger` 属性を持つ各関数に個別に適用されます。

1 つのキューで受信した複数のメッセージを並列に実行したくない場合は、バッチ サイズを 1 に設定します。 「 **Azure WebJobs SDK 1.1.0 RTM** 」の [キュー処理のさらに詳細な制御](https://azure.microsoft.com/blog/azure-webjobs-sdk-1-1-0-rtm/)に関するページを参照してください。

### <a id="queuemetadata"></a>キューまたはキュー メッセージ メタデータの取得
メソッド シグネチャにパラメーターを追加することで、次のメッセージ プロパティを取得できます。

* `DateTimeOffset` expirationTime
* `DateTimeOffset` insertionTime
* `DateTimeOffset` nextVisibleTime
* `string` queueTrigger (メッセージのテキストが含まれます)
* `string` id
* `string` popReceipt
* `int` dequeueCount

Azure Storage API を直接操作する場合は、 `CloudStorageAccount` パラメーターを追加することもできます。

次の例では、このメタデータをすべて INFO アプリケーション ログに書き込みます。 この例では logMessage と queueTrigger の両方にキュー メッセージの内容が含まれます。

        public static void WriteLog([QueueTrigger("logqueue")] string logMessage,
            DateTimeOffset expirationTime,
            DateTimeOffset insertionTime,
            DateTimeOffset nextVisibleTime,
            string id,
            string popReceipt,
            int dequeueCount,
            string queueTrigger,
            CloudStorageAccount cloudStorageAccount,
            TextWriter logger)
        {
            logger.WriteLine(
                "logMessage={0}\n" +
            "expirationTime={1}\ninsertionTime={2}\n" +
                "nextVisibleTime={3}\n" +
                "id={4}\npopReceipt={5}\ndequeueCount={6}\n" +
                "queue endpoint={7} queueTrigger={8}",
                logMessage, expirationTime,
                insertionTime,
                nextVisibleTime, id,
                popReceipt, dequeueCount,
                cloudStorageAccount.QueueEndpoint,
                queueTrigger);
        }

サンプル コードによって書き込まれたサンプル ログを次に示します。

        logMessage=Hello world!
        expirationTime=10/14/2014 10:31:04 PM +00:00
        insertionTime=10/7/2014 10:31:04 PM +00:00
        nextVisibleTime=10/7/2014 10:41:23 PM +00:00
        id=262e49cd-26d3-4303-ae88-33baf8796d91
        popReceipt=AgAAAAMAAAAAAAAAfc9H0n/izwE=
        dequeueCount=1
        queue endpoint=https://contosoads.queue.core.windows.net/
        queueTrigger=Hello world!

### <a id="graceful"></a>グレースフル シャットダウン
継続的な Web ジョブで実行されている関数は、`CancellationToken` パラメーターを受け取ることができます。これは、Web ジョブの停止がオペレーティング システムによってその関数に通知されるものです。 この通知を使用すれば、関数が予期せず終了してデータが不整合な状態になることを防止できます。

次の例では、関数内で Web ジョブの終了が迫っているか確認する方法を示します。

    public static void GracefulShutdownDemo(
                [QueueTrigger("inputqueue")] string inputText,
                TextWriter logger,
                CancellationToken token)
    {
        for (int i = 0; i < 100; i++)
        {
            if (token.IsCancellationRequested)
            {
                logger.WriteLine("Function was cancelled at iteration {0}", i);
                break;
            }
            Thread.Sleep(1000);
            logger.WriteLine("Normal processing for queue message={0}", inputText);
        }
    }

**注:** このダッシュボードではシャットダウンされた関数のステータスや出力が正しく示されていない可能性があります。

詳細については、 [Web ジョブのグレースフル シャットダウン](http://blog.amitapple.com/post/2014/05/webjobs-graceful-shutdown/#.VCt1GXl0wpR)に関するページを参照してください。   

## <a id="createqueue"></a> キュー メッセージの処理中にキュー メッセージを作成する方法
新しいキュー メッセージを作成する関数を記述するには、 `Queue` 属性を使用します。 `QueueTrigger`のように、キューの名前を文字列として渡すことも、 [キューの名前を動的に設定](#config)することもできます。

### <a name="string-queue-messages"></a>文字列のキュー メッセージ
次の非 Async コード サンプルでは、"inputqueue" という名前のキューに受信したキュー メッセージと同じ内容で、"outputqueue" という名前のキューにキュー メッセージを新しく作成します (このセクションの後半に示すように、非同期関数では、`IAsyncCollector<T>` を使用します)。

        public static void CreateQueueMessage(
            [QueueTrigger("inputqueue")] string queueMessage,
            [Queue("outputqueue")] out string outputQueueMessage )
        {
            outputQueueMessage = queueMessage;
        }

### <a name="poco-plain-old-clr-objecthttpenwikipediaorgwikiplainoldclrobject-queue-messages"></a>POCO ( [Plain Old CLR Object](http://en.wikipedia.org/wiki/Plain_Old_CLR_Object)) キュー メッセージ
文字列ではなく POCO オブジェクトを含むキュー メッセージを作成するには、出力パラメーターとして POCO 型を `Queue` 属性のコンス トラクターに渡します。

        public static void CreateQueueMessage(
            [QueueTrigger("inputqueue")] BlobInformation blobInfoInput,
            [Queue("outputqueue")] out BlobInformation blobInfoOutput )
        {
            blobInfoOutput = blobInfoInput;
        }

SDK はオブジェクトを JSON に自動的にシリアル化します。 オブジェクトが null の場合でもキュー メッセージは常に作成されます。

### <a name="create-multiple-messages-or-in-async-functions"></a>(非同期関数での) 複数のメッセージの作成
複数のメッセージを作成するには、次の例に示すように、パラメーターの種類を出力キュー `ICollector<T>` または `IAsyncCollector<T>` としてください。

        public static void CreateQueueMessages(
            [QueueTrigger("inputqueue")] string queueMessage,
            [Queue("outputqueue")] ICollector<string> outputQueueMessage,
            TextWriter logger)
        {
            logger.WriteLine("Creating 2 messages in outputqueue");
            outputQueueMessage.Add(queueMessage + "1");
            outputQueueMessage.Add(queueMessage + "2");
        }

`Add` メソッドが呼び出されると、すぐに各キュー メッセージが作成されます。

### <a name="types-that-the-queue-attribute-works-with"></a>キューの属性が連携する種類
次のパラメーターの種類に `Queue` 属性を使用できます。

* `out string` (関数が終了したときに、パラメーター値が null でない場合は、キュー メッセージを作成します)
* `out byte[]` (`string` と同様に動作)
* `out CloudQueueMessage` (`string` と同様に動作)
* `out POCO` (シリアル化可能な型で、関数が終了したときに、パラメーターが null である場合は、null オブジェクトでメッセージを作成します)
* `ICollector`
* `IAsyncCollector`
* `CloudQueue` (Azure Storage API を直接使用して、手動でメッセージを作成します)

### <a id="ibinder"></a>関数本体での WebJobs SDK 属性の使用
`Queue`、`Blob`、`Table` などの WebJobs SDK 属性を使用する前に関数で何らかの処理を行う必要がある場合は、`IBinder` インターフェイスを使用できます。

次の例では、入力キュー メッセージを取得して同じ内容の新しい出力キュー メッセージを作成します。 出力キュー名は、関数本体のコードによって設定されます。

        public static void CreateQueueMessage(
            [QueueTrigger("inputqueue")] string queueMessage,
            IBinder binder)
        {
            string outputQueueName = "outputqueue" + DateTime.Now.Month.ToString();
            QueueAttribute queueAttribute = new QueueAttribute(outputQueueName);
            CloudQueue outputQueue = binder.Bind<CloudQueue>(queueAttribute);
            outputQueue.AddMessage(new CloudQueueMessage(queueMessage));
        }

`Table` と `Blob` 属性で `IBinder` インターフェイスを使用することもできます。

## <a id="blobs"></a> キュー メッセージの処理中に BLOB およびテーブルの読み書きを行う方法
`Blob` と `Table` 属性を使用して、BLOB と テーブルを読み書きすることができます。 このセクションのサンプルは、BLOB に適用されます。 BLOB が作成されるか更新されたときにプロセスをトリガーするコード サンプルについては「[WebJobs SDK で Azure BLOB ストレージを使用する方法](websites-dotnet-webjobs-sdk-storage-blobs-how-to.md)」をご覧ください。テーブルを読み書きするコード サンプルについては、「[WebJobs SDK を使用して Azure テーブル ストレージを使用する方法](websites-dotnet-webjobs-sdk-storage-tables-how-to.md)」をご覧ください。

### <a name="string-queue-messages-triggering-blob-operations"></a>BLOB の操作を開始する文字列キュー メッセージ
文字列を含むキュー メッセージでは、`queueTrigger` はメッセージの内容を含む `Blob` 属性の `blobPath` パラメーターで使用できるプレース ホルダーです。

次の例では、BLOB の読み取りと書き込みに `Stream` オブジェクトを使用しています。 キュー メッセージは、textblobs コンテナーにある BLOB の名前です。 名前に "-new" を加えた BLOB のコピーが同じコンテナー内に作成されます。

        public static void ProcessQueueMessage(
            [QueueTrigger("blobcopyqueue")] string blobName,
            [Blob("textblobs/{queueTrigger}",FileAccess.Read)] Stream blobInput,
            [Blob("textblobs/{queueTrigger}-new",FileAccess.Write)] Stream blobOutput)
        {
            blobInput.CopyTo(blobOutput, 4096);
        }

`Blob` 属性コンストラクターが、コンテナーと BLOB 名を指定する `blobPath` パラメーターを受け取ります。 このプレース ホルダーの詳細については、「 [WebJobs SDK で Azure BLOB ストレージを使用する方法](websites-dotnet-webjobs-sdk-storage-blobs-how-to.md)」を参照してください。

属性が `Stream` オブジェクトを修飾するともう 1 つのコンス トラクターのパラメーターが `FileAccess` モードを読み取り、書き込み、読み取り/書き込みとして指定します。

次の例では、`CloudBlockBlob` オブジェクトを使用して BLOB を削除しています。 キュー メッセージは、BLOB の名前です。

        public static void DeleteBlob(
            [QueueTrigger("deleteblobqueue")] string blobName,
            [Blob("textblobs/{queueTrigger}")] CloudBlockBlob blobToDelete)
        {
            blobToDelete.Delete();
        }

### <a id="pocoblobs"></a> POCO ( [Plain Old CLR Object](http://en.wikipedia.org/wiki/Plain_Old_CLR_Object)) キュー メッセージ
キュー メッセージに JSON として格納されている POCO では、`Queue` 属性の `blobPath` パラメーターでオブジェクトのプロパティを指定するプレースホルダーを使用できます。 また、 [キュー メタデータのプロパティ名](#queuemetadata) もプレースホルダーとして使用できます。

次の例では、BLOB を別の拡張子を持つ新しい BLOB にコピーします。 キュー メッセージは、`BlobName` と `BlobNameWithoutExtension` プロパティを含む `BlobInformation` オブジェクトです。 プロパティの名前は、 `Blob` 属性の BLOB パスのプレース ホルダーとして使用されます

        public static void CopyBlobPOCO(
            [QueueTrigger("copyblobqueue")] BlobInformation blobInfo,
            [Blob("textblobs/{BlobName}", FileAccess.Read)] Stream blobInput,
            [Blob("textblobs/{BlobNameWithoutExtension}.txt", FileAccess.Write)] Stream blobOutput)
        {
            blobInput.CopyTo(blobOutput, 4096);
        }

SDK は [Newtonsoft.Json NuGet パッケージ](http://www.nuget.org/packages/Newtonsoft.Json) を使用してメッセージのシリアル化と逆シリアル化を行います。 キュー メッセージを Web ジョブ SDK を使用しないプログラムで作成する場合は、SDK が解析できる POCO キュー メッセージを作成する次の例のようなコードを記述できます。

        BlobInformation blobInfo = new BlobInformation() { BlobName = "boot.log", BlobNameWithoutExtension = "boot" };
        var queueMessage = new CloudQueueMessage(JsonConvert.SerializeObject(blobInfo));
        logQueue.AddMessage(queueMessage);

BLOB をオブジェクトにバインドする前に関数内でいくつかの処理を行う必要がある場合、 [Queue 属性について前述したように](#ibinder)関数の本体で属性を使用することが可能です。

### <a id="blobattributetypes"></a> BLOB 属性と連携して使用できる種類
`Blob` 属性は、次の種類で使用できます。

* `Stream` (読み取りまたは書き込み、FileAccess コンス トラクターのパラメーターを使用して指定)
* `TextReader`
* `TextWriter`
* `string` (読み取り)
* `out string` (書き込みは、関数を返されたときに、文字列パラメーターが null 以外の場合にのみ BLOB を作成します)
* POCO (読み取り)
* out POCO (書き込みは常に、BLOB を作成し、関数が返されたときに、POCO のパラメーターが null の場合は、null オブジェクトとして作成します)
* `CloudBlobStream` (書き込み)
* `ICloudBlob` (読み取りまたは書き込み)
* `CloudBlockBlob` (読み取りまたは書き込み)
* `CloudPageBlob` (読み取りまたは書き込み)

## <a id="poison"></a> 有害メッセージの処理方法
関数の失敗を引き起こす内容を含むメッセージは*有害メッセージ*と呼ばれます。 関数が失敗してもキュー メッセージは削除されず、最終的には回収されて、このサイクルを繰り返します。 SDK では一定数繰り返し送信されると自動的にそのサイクルを中断します。また手動でも処理できます。

### <a name="automatic-poison-message-handling"></a>有害メッセージの自動処理
SDKでは、キュー メッセージを処理する関数を最大 5 回呼び出します。 5 回目の実行に失敗した場合、メッセージは有害キューに移動されます。 [再試行回数の最大値の設定は変更可能です](#config)。

有害キューには *{originalqueuename}*-poison という名前が付けられます。 メッセージのログを取得するか、手動での対処が必要であるという通知を送信することにより有害キューからのメッセージを処理する関数が記述できます。

次の例では、`CopyBlob` 関数の失敗例を示します。失敗の原因は、存在しない BLOB の名前がキュー メッセージに含まれるためです。 その場合、メッセージは copyblobqueue キューから copyblobqueue-poison キューへと移動されます。 その後、`ProcessPoisonMessage` は有害メッセージをログに記録します。

        public static void CopyBlob(
            [QueueTrigger("copyblobqueue")] string blobName,
            [Blob("textblobs/{queueTrigger}", FileAccess.Read)] Stream blobInput,
            [Blob("textblobs/{queueTrigger}-new", FileAccess.Write)] Stream blobOutput)
        {
            blobInput.CopyTo(blobOutput, 4096);
        }

        public static void ProcessPoisonMessage(
            [QueueTrigger("copyblobqueue-poison")] string blobName, TextWriter logger)
        {
            logger.WriteLine("Failed to copy blob, name=" + blobName);
        }

次の画像は、これらの関数が有害メッセージを処理したときのコンソール出力を表しています。

![有害メッセージ処理のためのコンソール出力](./media/websites-dotnet-webjobs-sdk-storage-queues-how-to/poison.png)

### <a name="manual-poison-message-handling"></a>有害メッセージの手動処理
`dequeueCount` という名前の `int` パラメーターを関数に追加することで、メッセージが処理のために検知された回数を取得できます。 関数コードの dequeue 回数を確認し、特定の閾値を超えたときにメッセージを処理する、独自の有害メッセージの処理を実行できます。具体例を次に示します。

        public static void CopyBlob(
            [QueueTrigger("copyblobqueue")] string blobName, int dequeueCount,
            [Blob("textblobs/{queueTrigger}", FileAccess.Read)] Stream blobInput,
            [Blob("textblobs/{queueTrigger}-new", FileAccess.Write)] Stream blobOutput,
            TextWriter logger)
        {
            if (dequeueCount > 3)
            {
                logger.WriteLine("Failed to copy blob, name=" + blobName);
            }
            else
            {
            blobInput.CopyTo(blobOutput, 4096);
            }
        }

## <a id="config"></a> 構成オプションの設定方法
`JobHostConfiguration` 型を使用して、次の構成オプションを設定できます。

* コード内で SDK の接続文字列を設定する。
* 最大 dequeue 回数などの `QueueTrigger` 設定の構成
* 構成からキューの名前を取得する。

### <a id="setconnstr"></a>コード内で SDK の接続文字列を設定する
コード内で SDK の接続文字列を設定することにより、次の例に示すように、構成ファイルまたは環境変数に独自の接続文字列の名前を使用できます。

        static void Main(string[] args)
        {
            var _storageConn = ConfigurationManager
                .ConnectionStrings["MyStorageConnection"].ConnectionString;

            var _dashboardConn = ConfigurationManager
                .ConnectionStrings["MyDashboardConnection"].ConnectionString;

            var _serviceBusConn = ConfigurationManager
                .ConnectionStrings["MyServiceBusConnection"].ConnectionString;

            JobHostConfiguration config = new JobHostConfiguration();
            config.StorageConnectionString = _storageConn;
            config.DashboardConnectionString = _dashboardConn;
            config.ServiceBusConnectionString = _serviceBusConn;
            JobHost host = new JobHost(config);
            host.RunAndBlock();
        }

### <a id="configqueue"></a>QueueTrigger 設定の構成
キュー メッセージの処理に適用される次の設定を構成できます。

* 並列実行のために同時に取得するキュー メッセージの最大数 (既定値は 16)。
* キュー メッセージが有害キューに送られるまでの最大再試行回数 (既定値は 5)。
* キューが空の場合に再度ポーリングを実行するまでの最大待機時間 (既定値は 1 分)。

次の例は、これらの設定の構成方法を示しています。

        static void Main(string[] args)
        {
            JobHostConfiguration config = new JobHostConfiguration();
            config.Queues.BatchSize = 8;
            config.Queues.MaxDequeueCount = 4;
            config.Queues.MaxPollingInterval = TimeSpan.FromSeconds(15);
            JobHost host = new JobHost(config);
            host.RunAndBlock();
        }

### <a id="setnamesincode"></a>コードの WebJobs SDK コンストラクター パラメーター値の設定
キュー名、BLOB 名、コンテナー、テーブル名をハード コーディングではなく、コードに指定する場合もあります。 たとえば、`QueueTrigger` のキュー名を構成ファイルか環境変数に指定します。

`NameResolver` オブジェクトを `JobHostConfiguration` 型に渡して実行します。 WebJobs SDK 属性コンストラクターのパラメーターにパーセント (%) 記号で囲まれた特殊なプレースホルダーを追加し、 `NameResolver` コードでこれらのプレースホルダーの代わりに使用する実際の値を指定します。

たとえば、テスト環境で「logqueuetest」という名前のキューを、実行環境で「logqueueprod」という名前のキューを使用したいとします。 キュー名をハードコードする代わりに、実際のキュー名が保持される `appSettings` コレクションのエントリの名前を指定できます。 `appSettings` キーが、logqueue である場合は、関数は次の例のようになります。

        public static void WriteLog([QueueTrigger("%logqueue%")] string logMessage)
        {
            Console.WriteLine(logMessage);
        }

`NameResolver` クラスは、次の例に示すように `appSettings` からキューの名前を取得できます。

        public class QueueNameResolver : INameResolver
        {
            public string Resolve(string name)
            {
                return ConfigurationManager.AppSettings[name].ToString();
            }
        }

次の例に示すように `NameResolver` クラスを `JobHost` オブジェクトに渡します。

        static void Main(string[] args)
        {
            JobHostConfiguration config = new JobHostConfiguration();
            config.NameResolver = new QueueNameResolver();
            JobHost host = new JobHost(config);
            host.RunAndBlock();
        }

**注:** 関数が呼び出されるたびに、キュー、テーブル、BLOB の名前は解決されますが、BLOB コンテナーの名前はアプリケーションの起動時にのみ解決されます。 ジョブの実行中には、BLOB コンテナーの名前を変更することはできません。

## <a id="manual"></a>関数を手動でトリガーする方法
関数を手動でトリガーするには、次の例に示すように `JobHost` オブジェクトの `Call` または `CallAsync` メソッド、関数の `NoAutomaticTrigger` 属性を使用します。

        public class Program
        {
            static void Main(string[] args)
            {
                JobHost host = new JobHost();
                host.Call(typeof(Program).GetMethod("CreateQueueMessage"), new { value = "Hello world!" });
            }

            [NoAutomaticTrigger]
            public static void CreateQueueMessage(
                TextWriter logger,
                string value,
                [Queue("outputqueue")] out string message)
            {
                message = value;
                logger.WriteLine("Creating queue message: ", message);
            }
        }

## <a id="logs"></a>ログを書き込む方法
ダッシュボードは次の 2 つの場所でログを表示します。Web ジョブのページと特定の Web ジョブの呼び出しのページです。

![Web ジョブ ページのログ](./media/websites-dotnet-webjobs-sdk-storage-queues-how-to/dashboardapplogs.png)

![関数の呼び出しページのログ](./media/websites-dotnet-webjobs-sdk-storage-queues-how-to/dashboardlogs.png)

関数または `Main()` メソッドに呼び出すコンソール メソッドの出力は、特定の メソッド呼び出しのページではなく、WebJob のダッシュボード ページに表示されます。 メソッド シグネチャのパラメーターから取得した TextWriter オブジェクトの出力は、メソッド呼び出しのダッシュボード ページに表示されます。

コンソールはシングル スレッドで、多くのジョブ関数が同時に実行されるため、コンソールの出力を特定のメソッド呼び出しにリンクすることはできません。 そのため、SDK は各関数の呼び出しに独自のログ書き込みオブジェクトを提供しています。

[アプリケーション トレース ログ](web-sites-dotnet-troubleshoot-visual-studio.md#logsoverview)を書き込むには、`Console.Out`(INFO としてマークされたログを作成) および `Console.Error` (ERROR としてマークされたログを作成) を使用します。 代わりの方法としては、INFO と ERROR に加え、詳細、警告、重大レベルを提供する [トレースまたはトレース ソース](http://blogs.msdn.com/b/mcsuksoldev/archive/2014/09/04/adding-trace-to-azure-web-sites-and-web-jobs.aspx)を使用します。 ログをトレースするアプリケーションは、Azure Web アプリの構成方法によって、Web アプリ ログ ファイル、Azure テーブル、Azure BLOB に表示されます。 すべてのコンソール出力と同様に、最新の 100 のアプリケーションのログは関数呼び出しのページではなく、Web ジョブのダッシュボード ページに表示されます。

プログラムがローカルまたはその他の環境で実行されているのではなく、Azure Web ジョブで実行されている場合は、コンソール出力はダッシュボードにのみ表示されます。

高スループットのシナリオ用にダッシュボード ログを無効にします。 既定では、SDK はストレージにログを書き込みます。このアクティビティにより、多数のメッセージの処理時にパフォーマンスが低下する場合があります。 ログを無効にするには、ダッシュボード接続文字列を、次の例に示すように null に設定します。

        JobHostConfiguration config = new JobHostConfiguration();       
        config.DashboardConnectionString = "";        
        JobHost host = new JobHost(config);
        host.RunAndBlock();

次の例では、ログを書き込むいくつかの方法を示しています。

        public static void WriteLog(
            [QueueTrigger("logqueue")] string logMessage,
            TextWriter logger)
        {
            Console.WriteLine("Console.Write - " + logMessage);
            Console.Out.WriteLine("Console.Out - " + logMessage);
            Console.Error.WriteLine("Console.Error - " + logMessage);
            logger.WriteLine("TextWriter - " + logMessage);
        }

特定の関数呼び出しのページに移動して、**[出力切り替え]** をクリックすると、WebJobs SDK のダッシュボードで `TextWriter` オブジェクトからの出力が表示されます。

![関数呼び出しのリンクをクリックします](./media/websites-dotnet-webjobs-sdk-storage-queues-how-to/dashboardinvocations.png)

![関数の呼び出しページのログ](./media/websites-dotnet-webjobs-sdk-storage-queues-how-to/dashboardlogs.png)

Web ジョブのページ (特定の関数呼び出しのページではなく) に移動して、 **[出力切り替え]**をクリックすると、WebJobs SDK のダッシュボードでアプリケーション ログの最新 100 件が表示されます。

![[出力切り替え] をクリックします](./media/websites-dotnet-webjobs-sdk-storage-queues-how-to/dashboardapplogs.png)

連続的な Web ジョブでは、Web アプリのファイル システム内の /data/jobs/continuous/*{webjobname}*/job_log.txt にアプリケーション ログが表示されます。

        [09/26/2014 21:01:13 > 491e54: INFO] Console.Write - Hello world!
        [09/26/2014 21:01:13 > 491e54: ERR ] Console.Error - Hello world!
        [09/26/2014 21:01:13 > 491e54: INFO] Console.Out - Hello world!

次に示したのは、Azure BLOB におけるアプリケーション ログの例です。2014-09-26T21:01:13,Information,contosoadsnew,491e54,635473620738373502,0,17404,17,Console.Write - Hello world!, 2014-09-26T21:01:13,Error,contosoadsnew,491e54,635473620738373502,0,17404,19,Console.Error - Hello world!, 2014-09-26T21:01:13,Information,contosoadsnew,491e54,635473620738529920,0,17404,17,Console.Out - Hello world!,

また、Azure テーブルでは、`Console.Out` と `Console.Error` のログは次のようになります。

![テーブル内の INFO ログ](./media/websites-dotnet-webjobs-sdk-storage-queues-how-to/tableinfo.png)

![テーブル内の ERROR ログ](./media/websites-dotnet-webjobs-sdk-storage-queues-how-to/tableerror.png)

独自のロガーにプラグインする必要がある場合は、 [この例](http://github.com/Azure/azure-webjobs-sdk-samples/blob/master/BasicSamples/MiscOperations/Program.cs)を参照してください。

## <a id="errors"></a>エラーを処理する方法とタイムアウトを構成する方法
Web ジョブ SDK に含まれる [Timeout](http://github.com/Azure/azure-webjobs-sdk-samples/blob/master/BasicSamples/MiscOperations/Functions.cs) 属性を使用して、指定した時間内に完了しない場合に、関数が取り消されるようにすることもできます。 指定した時間内に発生するエラーの数が多すぎる場合にアラートを生成するには、 `ErrorTrigger` 属性を使用します。 [ErrorTrigger の例](https://github.com/Azure/azure-webjobs-sdk-extensions/wiki/Error-Monitoring)を次に示します。

```
public static void ErrorMonitor(
[ErrorTrigger("00:01:00", 1)] TraceFilter filter, TextWriter log,
[SendGrid(
    To = "admin@emailaddress.com",
    Subject = "Error!")]
 SendGridMessage message)
{
    // log last 5 detailed errors to the Dashboard
   log.WriteLine(filter.GetDetailedMessage(5));
   message.Text = filter.GetDetailedMessage(1);
}
```

また、アプリ設定または環境変数名である構成スイッチを使用して、トリガーできるかどうかを制御する関数を動的に無効または有効にすることもできます。 サンプル コードについては、[Web ジョブ SDK サンプル リポジトリ](https://github.com/Azure/azure-webjobs-sdk-samples/blob/master/BasicSamples/MiscOperations/Functions.cs)の `Disable` 属性を参照してください。

## <a id="nextsteps"></a> 次のステップ
このガイドでは、Azure キューを操作するための一般的なシナリオの処理方法を示すコードのサンプルを提供しました。 Azure Web ジョブ および Web ジョブ SDK の使用方法の詳細については、「 [Azure Web ジョブの推奨リソース](http://go.microsoft.com/fwlink/?linkid=390226)」を参照してください。

