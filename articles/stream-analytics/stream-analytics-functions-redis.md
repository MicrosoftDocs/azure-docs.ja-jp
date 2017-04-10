---
title: "Stream Analytics による Azure Functions のリアルタイム処理 | Microsoft Docs"
description: "Service Bus キューに接続した Azure 関数を使用して、Stream Analytics ジョブの出力から Azure Redis Cache にデータを格納する方法を説明します。"
keywords: "データ ストリーム, Redis Cache, Service Bus キュー"
services: stream-analytics
author: ryancrawcour
manager: jhubbard
documentationcenter: 
ms.assetid: d428bb33-4244-4001-b93d-c77bed816527
ms.service: stream-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/28/2017
ms.author: ryancraw
translationtype: Human Translation
ms.sourcegitcommit: 9afd26024d2aa0d3d732ddc6f54e591715afca69
ms.openlocfilehash: 3753846e955308a7607d92ed25648d75b9cc3a35
ms.lasthandoff: 01/24/2017


---
# <a name="how-to-store-data-from-azure-stream-analytics-in-an-azure-redis-cache-using-azure-functions"></a>Azure Functions を使用して Azure Stream Analytics から Azure Redis Cache にデータを格納する方法
Azure Stream Analytics では、デバイス、センサー、インフラストラクチャ、アプリケーション、または任意のデータ ストリームからリアルタイムの洞察を得られるようにする、低コストの分析ソリューションを迅速に開発、デプロイすることができます。 リアルタイムの管理と監視、コマンドと制御、不正行為の検出、コネクテッド カーなど、さまざまな用途に利用できます。 このようなシナリオの多くで、Azure Stream Analytics が出力したデータを Azure Redis Cache などの分散データ ストアに格納する必要があります。

ある通信会社の社員が、 SIM の不正行為（地理的に異なる場所でほぼ同じ時刻に同じ ID から複数の着信がある）を検出するために、 不正使用の可能性があるすべての着信を Azure Redis Cache に格納しようとしています。 このブログでは、このタスクを簡単に行う方法を説明します。 

## <a name="prerequisites"></a>前提条件
ASA のチュートリアル「[リアルタイムの不正行為の検出][fraud-detection]」を完了していること

## <a name="architecture-overview"></a>アーキテクチャの概要
![アーキテクチャのスクリーン ショット](./media/stream-analytics-functions-redis/architecture-overview.png)

上の図に示すように、Stream Analytics によって、ストリーミング入力データに対してクエリが実行され、出力に送信されます。 この出力に基づいて、Azure Functions によって特定のイベントがトリガーされます。 

このブログでは、パイプラインの Azure Functions の部分、つまり不正なデータをキャッシュに格納するイベントをトリガーする部分を主に取り上げます。
「[リアルタイムの不正行為の検出][fraud-detection]」チュートリアルを完了していれば、入力 (イベント ハブ)、クエリ、および出力 (Blob Storage) が既に構成され、実行されています。 このブログでは、出力を変更して Service Bus キューが使用されるようにします。 その後、このキューに Azure Function を接続します。 

## <a name="create-and-connect-a-service-bus-queue-output"></a>Service Bus キューの出力の作成と接続
Service Bus キューを作成するには、「[Service Bus キューの使用][servicebus-getstarted]」の .NET セクションの手順 1 と 2 を実行します。
次に、このキューを前の「不正行為の検出」チュートリアルで作成した Stream Analytics ジョブに接続します。

1. Azure Portal でジョブの **[出力]** ブレードに移動し、ページ上部にある **[追加]** を選択します。
   
    ![出力の追加](./media/stream-analytics-functions-redis/adding-outputs.png)
2. **[シンク]** として **[Service Bus キュー]** を選択し、画面の指示に従います。 必ず、「[Service Bus キューの使用][servicebus-getstarted]」で作成した Service Bus キューの名前空間を選択してください。 完了したら、右矢印ボタンをクリックします。
3. 次の値を指定します。
   
   * **[イベント シリアライザー形式]**: JSON
   * **[エンコード]**: UTF8
   * **[形式]**: 行区切り
4. **[作成]** ボタンをクリックしてこのソースを追加し、Stream Analytics からストレージ アカウントに正常に接続できることを確認します。
5. **[クエリ]** タブで、現在のクエリを次のクエリに置き換えます。 *[YOUR SERVICE BUS NAME] * は、手順 3 で作成した出力名に置き換えてください。 
   
    ```    
   
        SELECT 
            System.Timestamp as Time, CS1.CallingIMSI, CS1.CallingNum as CallingNum1, 
            CS2.CallingNum as CallingNum2, CS1.SwitchNum as Switch1, CS2.SwitchNum as Switch2
   
        INTO [YOUR SERVICE BUS NAME]
   
        FROM CallStream CS1 TIMESTAMP BY CallRecTime
        JOIN CallStream CS2 TIMESTAMP BY CallRecTime
            ON CS1.CallingIMSI = CS2.CallingIMSI AND DATEDIFF(ss, CS1, CS2) BETWEEN 1 AND 5
   
        WHERE CS1.SwitchNum != CS2.SwitchNum
   
    ```

## <a name="create-an-azure-redis-cache"></a>Azure Redis Cache の作成
「[Azure Redis Cache の使用方法][use-rediscache]」の .NET セクションの手順 (「***キャッシュ クライアントの構成***」セクションまで) に従って、Azure Redis Cache を作成します。
手順を完了すると、新しい Redis Cache が作成されます。 **[すべての設定]** の下にある **[アクセス キー]** を選択し、***プライマリ接続文字列***をメモしておきます。

![アーキテクチャのスクリーン ショット](./media/stream-analytics-functions-redis/redis-cache-keys.png)

## <a name="create-an-azure-function"></a>Azure 関数の作成
「[初めての Azure 関数の作成][functions-getstarted]」チュートリアルに従って、Azure Functions を作成します。 使用する Azure 関数が既にある場合、この手順はスキップして「 [Redis Cache への書き込み](#Writing-to-Redis-Cache)

1. ポータルの左側のナビゲーションから [App Services] を選択し、Azure 関数アプリ名をクリックして、関数のアプリの Web サイトにアクセスします。
    ![App Services 関数一覧のスクリーン ショット](./media/stream-analytics-functions-redis/app-services-function-list.png)
2. **[新しい関数] > [ServiceBusQueueTrigger – C#]** をクリックします。 次のフィールドでは、以下のように指定します。
   
   * **[キュー名]**: 「[Service Bus キューの使用][servicebus-getstarted]」でキューを作成したときに入力した名前と同じ名前 (Service Bus の名前ではなく) を指定します。 必ず、Stream Analytics の出力に接続されているキューを使用してください。
   * **[Service Bus 接続]**: **[接続文字列の追加]** を選択します。 接続文字列を見つけるには、Azure クラシック ポータルに移動し、**[Service Bus]**、作成したサービス バス、画面下部にある **[接続情報]** の順に選択します。 このページのメイン画面を表示していることを確認します。 接続文字列をコピーして貼り付けます。 任意の接続名を入力します。
     
       ![Service Bus 接続のスクリーンショット](./media/stream-analytics-functions-redis/servicebus-connection.png)
   * **[AccessRights]**: **[管理]** を選択します。
3. **[作成]**

## <a name="writing-to-redis-cache"></a>Redis Cache への書き込み
以上で、Service Bus キューからデータを読み取る Azure 関数が作成されました。 残りの作業は、関数を使用して、Redis Cache にこのデータを書き込むことです。 

1. 新たに作成した **[ServiceBusQueueTrigger]** を選択し、右上隅にある **[Function app settings (関数アプリの設定)]** をクリックします。 **[Go to App Service Settings (App Service の設定へ移動)] > [設定] > [アプリケーション設定]** を選択します。
2. [接続文字列] セクションの **[名前]** セクションで名前を作成します。 「**Redis Cache の作成**」手順で見つけたプライマリ接続文字列を、**[値]** セクションに貼り付けます。 **[SQL Database]** で **[カスタム]** を選択します。
3. 上部にある **[保存]** をクリックします。
   
    ![Service Bus 接続のスクリーンショット](./media/stream-analytics-functions-redis/function-connection-string.png)
4. App Service の設定に戻り、**[ツール] > [App Service Editor (プレビュー)] > [On (オン)] > [Go (移動)]** を選択します。
   
    ![Service Bus 接続のスクリーンショット](./media/stream-analytics-functions-redis/app-service-editor.png)
5. 任意のエディターで、次の内容の JSON ファイルを作成し、 **project.json** という名前でローカル ディスクに保存します。
   
        {
            "frameworks": {
                "net46": {
                    "dependencies": {
                        "StackExchange.Redis":"1.1.603",
                        "Newtonsoft.Json": "9.0.1"
                    }
                }
            }
        }
6. このファイルを、(WWWROOT ではなく) 関数のルート ディレクトリにアップロードします。 **project.lock.json** という名前のファイルが自動的に表示されるので、Nuget パッケージの "StackExchange.Redis" と "Newtonsoft.Json" がインポートされていることを確認します。
7. **run.csx** ファイルで、生成されているコードを次のコードに置き換えます。 LazyConnection 関数で、"CONN NAME" を「 **Redis Cache へのデータの格納**」の手順 2 で作成した名前に置き換えます。

````

    using System;
    using System.Threading.Tasks;
    using StackExchange.Redis;
    using Newtonsoft.Json;
    using System.Configuration;

    public static void Run(string myQueueItem, TraceWriter log)
    {
        log.Info($"Function processed message: {myQueueItem}");

        // Connection refers to a property that returns a ConnectionMultiplexer
        IDatabase db = Connection.GetDatabase();
        log.Info($"Created database {db}");

        // Parse JSON and extract the time
        var message = JsonConvert.DeserializeObject<dynamic>(myQueueItem);
        string time = message.time;
        string callingnum1 = message.callingnum1;

        // Perform cache operations using the cache object...
        // Simple put of integral data types into the cache
        string key = time + " - " + callingnum1;
        db.StringSet(key, myQueueItem);
        log.Info($"Object put in database. Key is {key} and value is {myQueueItem}");

        // Simple get of data types from the cache
        string value = db.StringGet(key);
        log.Info($"Database got: {value}"); 
    }

    // Connect to the Service Bus
    private static Lazy<ConnectionMultiplexer> lazyConnection = 
        new Lazy<ConnectionMultiplexer>(() =>
            {
                var cnn = ConfigurationManager.ConnectionStrings["CONN NAME"].ConnectionString
                return ConnectionMultiplexer.Connect();
            });

    public static ConnectionMultiplexer Connection
    {
        get
        {
            return lazyConnection.Value;
        }
    }

````

## <a name="start-the-stream-analytics-job"></a>Stream Analytics ジョブの開始
1. telcodatagen.exe アプリケーションを起動します。 使用方法は次のとおりです。````telcodatagen.exe [#NumCDRsPerHour] [SIM Card Fraud Probability] [#DurationHours]````
2. ポータルの [Stream Analytics ジョブ] ブレードから、ページ上部にある **[開始]** をクリックします。
   
    ![ジョブの開始画面のスクリーンショット](./media/stream-analytics-functions-redis/starting-job.png)
3. **[ジョブの開始]** ブレードが表示されたら、**[Now (今すぐ)]** を選択し、画面下部にある **[開始]** ボタンをクリックします。 ジョブの状態が「開始中」になった後、しばらくすると「実行中」に変わります。
   
    ![ジョブの開始時間の選択画面のスクリーン ショット](./media/stream-analytics-functions-redis/start-job-time.png)

## <a name="run-solution-and-check-results"></a>ソリューションの実行と結果の確認
**[ServiceBusQueueTrigger]** ページに戻ると、ログ ステートメントが表示されています。 このログを見ると、時間をキーとして使用して、Service Bus キューからデータを取得し、それをデータベースに挿入し、そこからフェッチしたことが示されています。

データが Redis Cache 内にあることを確認するには、新しいポータルで Redis Cache ページに移動し (前述の「 [Azure Redis Cache の作成](#Create-an-Azure-Redis-Cache) 」手順を参照)、[コンソール] を選択します。

ここで、データが実際にキャッシュにあることを確認するための Redis コマンドを作成できます。

![Redis コンソールのスクリーン ショット](./media/stream-analytics-functions-redis/redis-console.png)

## <a name="next-steps"></a>次のステップ
Azure Functions と Stream Analytics の併用によって可能になる、新たな機能をご活用いただければ幸いです。 ご意見、ご提案がございましたら、 [Azure UserVoice サイト](https://feedback.azure.com/forums/270577-stream-analytics)からお寄せください。

Microsoft Azure を初めてお使いになる場合は、 [Azure 無料試用版アカウント](https://azure.microsoft.com/pricing/free-trial/)にサインアップしてご利用ください。 Stream Analytics を初めてお使いになる場合、 [最初の Stream Analytics ジョブを作成](stream-analytics-create-a-job.md)していただけます。

サポートが必要な場合やご不明な点がある場合は、[MSDN](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureStreamAnalytics) または [Stackoverflow](http://stackoverflow.com/questions/tagged/azure-stream-analytics) フォーラムにご投稿ください。 

次のリソースも参照してください。

* [Azure Functions developer reference (Azure Functions 開発者向けリファレンス)](../azure-functions/functions-reference.md)
* [Azure Functions C# developer reference (Azure Functions C# 開発者向けリファレンス)](../azure-functions/functions-reference-csharp.md)
* [Azure Functions F# 開発者向けリファレンス](../azure-functions/functions-reference-fsharp.md)
* [Azure Functions NodeJS 開発者向けリファレンス](../azure-functions/functions-reference.md)
* [Azure Functions のトリガーとバインドに関する記事](../azure-functions/functions-triggers-bindings.md)
* [Azure Redis Cache の監視方法](../redis-cache/cache-how-to-monitor.md)

最新のニュースと機能については、Twitter で [@AzureStreaming](https://twitter.com/AzureStreaming) をフォローしてください。

[fraud-detection]: stream-analytics-real-time-fraud-detection.md
[servicebus-getstarted]: ../service-bus-messaging/service-bus-dotnet-get-started-with-queues.md
[use-rediscache]: ../redis-cache/cache-dotnet-how-to-use-azure-redis-cache.md
[functions-getstarted]: ../azure-functions/functions-create-first-azure-function.md

