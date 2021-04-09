---
title: チュートリアル - Azure Stream Analytics ジョブで Azure Functions を実行する
description: このチュートリアルでは、Stream Analytics ジョブへの出力シンクとして Azure Functions を構成する方法を説明します。
author: enkrumah
ms.author: ebnkruma
ms.service: stream-analytics
ms.topic: tutorial
ms.custom: mvc, devx-track-csharp
ms.date: 01/27/2020
ms.openlocfilehash: 74e09e61a6132858d716686bdb6687bb670f0d33
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "98879513"
---
# <a name="tutorial-run-azure-functions-from-azure-stream-analytics-jobs"></a>チュートリアル:Azure Stream Analytics ジョブから Azure Functions を実行する 

Azure Stream Analytics から Azure Functions を実行するには、Stream Analytics ジョブへの出力シンクの 1 つとして Functions を構成します。 Functions はイベント ドリブン型コンピューティング オンデマンド エクスペリエンスであり、これにより、Azure またはサード パーティのサービスで発生するイベントによってトリガーされるコードを実装できます。 トリガーに応答する Azure Functions の機能によって、それは Azure Stream Analytics への自然な出力になります。

Stream Analytics では、HTTP トリガーを使用して Functions を呼び出します。 Functions の出力アダプターにより、ユーザーは Functions を Stream Analytics に接続し、Stream Analytics クエリに基づいてイベントをトリガーできるようになります。 

> [!NOTE]
> マルチテナント クラスターで実行されている Stream Analytics ジョブから仮想ネットワーク (VNet) 内の Azure Functions への接続はサポートされていません。

このチュートリアルでは、以下の内容を学習します。

> [!div class="checklist"]
> * Stream Analytics ジョブの作成と実行
> * Azure Cache for Redis インスタンスを作成する
> * Azure Function の作成
> * Azure Cache for Redis の結果を確認する

Azure サブスクリプションがない場合は、開始する前に[無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)を作成してください。

## <a name="configure-a-stream-analytics-job-to-run-a-function"></a>関数を実行するための Stream Analytics ジョブの構成を行う 

このセクションでは、Azure Cache for Redis にデータを書き込む関数を実行するための Stream Analytics ジョブの構成方法について説明します。 Stream Analytics ジョブは、Azure Event Hubs からイベントを読み取り、関数を呼び出すクエリを実行します。 この関数では、Stream Analytics ジョブからデータを読み取って、Azure Cache for Redis に書き込みます。

![Azure サービス間の関係を示す図](./media/stream-analytics-with-azure-functions/image1.png)

## <a name="create-a-stream-analytics-job-with-event-hubs-as-input"></a>入力としての Event Hubs で Stream Analytics ジョブを作成する

チュートリアル「[Azure Stream Analytics の使用 | リアルタイムの不正行為の検出](stream-analytics-real-time-fraud-detection.md)」の説明に従って、イベント ハブを作成し、イベント ジェネレーター アプリケーションを起動し、Stream Analytics ジョブを作成します クエリおよび出力を作成する手順は省略します。 Azure Functions の出力を設定するには後続のセクションを参照してください。

## <a name="create-an-azure-cache-for-redis-instance"></a>Azure Cache for Redis インスタンスを作成する

1. 「[Create a cache](../azure-cache-for-redis/cache-dotnet-how-to-use-azure-redis-cache.md#create-a-cache)」 (キャッシュを作成する) で説明されている手順を使用して、Azure Cache for Redis でキャッシュを作成します。  

2. キャッシュを作成したら、 **[設定]** にある **[アクセス キー]** を選択します。 **プライマリ接続文字列** をメモします。

   ![Azure Cache for Redis の接続文字列のスクリーンショット](./media/stream-analytics-with-azure-functions/image2.png)

## <a name="create-a-function-in-azure-functions-that-can-write-data-to-azure-cache-for-redis"></a>Azure Cache for Redis にデータを書き込むことができる関数を Azure Functions で作成する

1. Functions ドキュメントの[関数アプリの作成](../azure-functions/functions-get-started.md)に関するセクションを参照してください。 このセクションでは、CSharp 言語を使用して、関数アプリと [HTTP によってトリガーされる関数を Azure Functions で作成](../azure-functions/functions-get-started.md)する方法について説明します。  

2. **run.csx** 関数を参照します。 これを以下のコードで更新します **"\<your Azure Cache for Redis connection string goes here\>"** を、前のセクションで取得した Azure Cache for Redis のプライマリ接続文字列に置き換えます。 

    ```csharp
    using System;
    using System.Net;
    using System.Threading.Tasks;
    using StackExchange.Redis;
    using Newtonsoft.Json;
    using System.Configuration;

    public static async Task<HttpResponseMessage> Run(HttpRequestMessage req, TraceWriter log)
    {
        log.Info($"C# HTTP trigger function processed a request. RequestUri={req.RequestUri}");
    
        // Get the request body
        dynamic dataArray = await req.Content.ReadAsAsync<object>();

        // Throw an HTTP Request Entity Too Large exception when the incoming batch(dataArray) is greater than 256 KB. Make sure that the size value is consistent with the value entered in the Stream Analytics portal.

        if (dataArray.ToString().Length > 262144)
        {
            return new HttpResponseMessage(HttpStatusCode.RequestEntityTooLarge);
        }
        var connection = ConnectionMultiplexer.Connect("<your Azure Cache for Redis connection string goes here>");
        log.Info($"Connection string.. {connection}");
    
        // Connection refers to a property that returns a ConnectionMultiplexer
        IDatabase db = connection.GetDatabase();
        log.Info($"Created database {db}");
    
        log.Info($"Message Count {dataArray.Count}");

        // Perform cache operations using the cache object. For example, the following code block adds few integral data types to the cache
        for (var i = 0; i < dataArray.Count; i++)
        {
            string time = dataArray[i].time;
            string callingnum1 = dataArray[i].callingnum1;
            string key = time + " - " + callingnum1;
            db.StringSet(key, dataArray[i].ToString());
            log.Info($"Object put in database. Key is {key} and value is {dataArray[i].ToString()}");
       
            // Simple get of data types from the cache
            string value = db.StringGet(key);
            log.Info($"Database got: {value}");
        }

        return req.CreateResponse(HttpStatusCode.OK, "Got");
    }

   ```

   Stream Analytics では、関数から "HTTP 要求エンティティが大きすぎる" という例外を受け取ると、Functions に送信するバッチのサイズを削減します。 次のコードでは、Stream Analytics からサイズ超過のバッチが送信されていないことを確認します。 関数で使用する最大バッチ カウントおよび最大バッチ サイズの値が Stream Analytics ポータルに入力した値と矛盾しないことを確認します。

    ```csharp
    if (dataArray.ToString().Length > 262144)
        {
            return new HttpResponseMessage(HttpStatusCode.RequestEntityTooLarge);
        }
   ```

3. 任意のテキスト エディターで、**project.json** という名前の JSON ファイルを作成します。 次のコードを貼り付け、ローカル コンピューターに保存します。 このファイルには、C# 関数で必要とされる NuGet パッケージの依存関係が含まれています。  
   
    ```json
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

   ```
 
4. Azure Portal に戻ります。 **[プラットフォーム機能]** タブで、目的の関数を参照します。 **[開発ツール]** で **[Azure App Service]** を選択します。 
 
   ![[プラットフォーム機能] タブを示すスクリーンショット。[App Service Editor] が選択されています。](./media/stream-analytics-with-azure-functions/image3.png)

5. App Service エディターで、ルート ディレクトリを右クリックし、**project.json** ファイルをアップロードします。 アップロードが成功したら、ページを更新します。 これで、自動生成された **project.lock.json** という名前のファイルが表示されるはずです。 自動生成されたファイルには、project.json ファイルで指定されている .dll ファイルへの参照が含まれています。  

   ![メニューから選択された [ファイルのアップロード] を示すスクリーンショット。](./media/stream-analytics-with-azure-functions/image4.png)

## <a name="update-the-stream-analytics-job-with-the-function-as-output"></a>出力としての関数で Stream Analytics ジョブを更新する

1. Azure Portal で Stream Analytics ジョブを開きます。  

2. 目的の関数を参照し、 **[概要]**  >  **[出力]**  >  **[追加]** の順に選択します。 新しい出力を追加するには、シンク オプションとして **Azure Function** を選択します。 Functions の出力アダプターには次のプロパティがあります。  

   |**プロパティ名**|**説明**|
   |---|---|
   |出力エイリアス| 入力を参照するジョブのクエリで使用するわかりやすい名前です。 |
   |インポート オプション| 現在のサブスクリプションから関数を使用できます。あるいは関数が別のサブスクリプションにある場合は、設定を手動で指定できます。 |
   |Function App| Function App の名前です。 |
   |Function| Function App にある関数の名前です (run.csx 関数の名前)。|
   |最大バッチ サイズ|関数に送信される、各出力バッチの最大サイズをバイト単位で設定します。 既定では、この値は 262,144 バイト (256 KB) に設定されます。|
   |最大バッチ カウント|関数に送信される各バッチ内の最大イベント数を指定します。 既定値は 100 です。 このプロパティは省略可能です。|
   |Key|別のサブスクリプションから関数を使用できるようにします。 関数にアクセスするキー値を指定します。 このプロパティは省略可能です。|

3. 出力エイリアスの名前を指定します。 このチュートリアルでは **saop1** という名前ですが、任意の名前を使用できます。 その他の詳細を入力します。

4. Stream Analytics ジョブを開き、クエリを次の内容に更新します 出力シンクの名前を **saop1** にしなかった場合は、忘れずにクエリで変更してください。  

   ```sql
    SELECT
            System.Timestamp as Time, CS1.CallingIMSI, CS1.CallingNum as CallingNum1,
            CS2.CallingNum as CallingNum2, CS1.SwitchNum as Switch1, CS2.SwitchNum as Switch2
        INTO saop1
        FROM CallStream CS1 TIMESTAMP BY CallRecTime
           JOIN CallStream CS2 TIMESTAMP BY CallRecTime
            ON CS1.CallingIMSI = CS2.CallingIMSI AND DATEDIFF(ss, CS1, CS2) BETWEEN 1 AND 5
        WHERE CS1.SwitchNum != CS2.SwitchNum
   ```

5. コマンドラインで次のコマンドを実行して telcodatagen.exe アプリケーションを起動します。 コマンドには形式 `telcodatagen.exe [#NumCDRsPerHour] [SIM Card Fraud Probability] [#DurationHours]` が使用されます。  
   
   ```cmd
   telcodatagen.exe 1000 0.2 2
   ```
    
6.  Stream Analytics ジョブを開始します。

## <a name="check-azure-cache-for-redis-for-results"></a>Azure Cache for Redis の結果を確認する

1. Azure portal を参照し、Azure Cache for Redis を見つけます。 **[コンソール]** を説明します。  

2. [Azure Cache for Redis コマンド](https://redis.io/commands)を使用して、Azure Cache for Redis にデータがあることを確認します (コマンドの形式は Get {key} となります)。次に例を示します。

   **Get "12/19/2017 21:32:24 - 123414732"**

   このコマンドにより、指定したキーの値が出力されるはずです。

   ![Azure Cache for Redis 出力のスクリーンショット](./media/stream-analytics-with-azure-functions/image5.png)

## <a name="error-handling-and-retries"></a>エラー処理と再試行

Azure Functions へのイベントの送信中にエラーが発生した場合、Stream Analytics はほとんどの操作を再試行します。 http エラー 413 (エンティティが大きすぎます) を除き、すべての http 例外は、成功するまで再試行されます。 "エンティティが大きすぎます" エラーは、[再試行またはドロップ ポリシー](stream-analytics-output-error-policy.md)の対象となるデータ エラーとして扱われます。

> [!NOTE]
> Stream Analytics から Azure Functions への HTTP 要求のタイムアウトは、100 秒に設定されています。 Azure Functions アプリでのバッチ処理に 100 秒以上かかる場合、Stream Analytics エラーが出力され、バッチが再試行されます。

タイムアウトを再試行すると、重複するイベントが出力シンクに書き込まれる可能性があります。 Stream Analytics では、失敗したバッチを再試行するときに、バッチ内のすべてのイベントが再試行されます。 たとえば、Stream Analytics から Azure Functions に送信される 20 個のイベントからなるバッチを考えてみます。 Azure Functions によってこのバッチの最初の 10 個のイベントが処理されるのに 100 秒かかるとします。 Stream Analytics は、100 秒が経過した時点で Azure Functions から肯定応答を受信していないため、要求を中断します。さらに、同じバッチに対して別の要求が送信されます。 バッチ内の最初の 10 個のイベントが Azure Functions によって再度処理され、これによって重複が発生します。 

## <a name="known-issues"></a>既知の問題

Azure Portal では、最大バッチ サイズ/最大バッチ カウントの値を空 (既定値) にリセットしようとしても、保存時には以前に入力した値に戻ります。 この場合は、それらのフィールドに既定値を手動で入力します。

Azure Functions での [HTTP ルーティング](/sandbox/functions-recipes/routes?tabs=csharp)の使用は、現在、Stream Analytics ではサポートされていません。

仮想ネットワークでホストされている Azure Functions に接続するためのサポートが、有効になっていません。

## <a name="clean-up-resources"></a>リソースをクリーンアップする

リソース グループ、ストリーミング ジョブ、および関連するすべてのリソースは、不要になったら削除します。 ジョブを削除すると、ジョブによって消費されるストリーミング ユニットに対する課金を回避することができます。 ジョブを後で使用する計画がある場合は、ジョブを停止し、必要なときに再起動することができます。 このジョブの使用を続けない場合は、以下の手順に従って、このクイック スタートで作成したすべてのリソースを削除してください。

1. Azure Portal の左側のメニューで、 **[リソース グループ]** をクリックしてから、作成したリソースの名前をクリックします。  
2. リソース グループのページで **[削除]** をクリックし、削除するリソースの名前をテキスト ボックスに入力してから **[削除]** をクリックします。

## <a name="next-steps"></a>次のステップ

このチュートリアルでは、Azure 関数を実行する単純な Stream Analytics ジョブを作成しました。 Stream Analytics ジョブの詳細については、次のチュートリアルに進んでください。

> [!div class="nextstepaction"]
> [Stream Analytics ジョブ内で JavaScript ユーザー定義関数を実行する](stream-analytics-javascript-user-defined-functions.md)
