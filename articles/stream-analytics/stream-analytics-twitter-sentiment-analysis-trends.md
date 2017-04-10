---
title: "Azure Stream Analytics でのリアルタイム Twitter 感情分析 | Microsoft Docs"
description: "リアルタイム Twitter センチメント分析で Stream Analytics を使用する方法について説明します。 イベントの生成からライブ ダッシュボード上でのデータ操作までの手順。"
keywords: "リアルタイム Twitter 傾向分析、センチメント分析、ソーシャル メディア分析、傾向分析の例"
services: stream-analytics
documentationcenter: 
author: jeffstokes72
manager: jhubbard
editor: cgronlun
ms.assetid: 42068691-074b-4c3b-a527-acafa484fda2
ms.service: stream-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 03/28/2017
ms.author: jeffstok
translationtype: Human Translation
ms.sourcegitcommit: 503f5151047870aaf87e9bb7ebf2c7e4afa27b83
ms.openlocfilehash: 0ba5baf71176e8d5967775dae50b6577f8b5c54c
ms.lasthandoff: 03/29/2017

---

# <a name="real-time-twitter-sentiment-analysis-in-azure-stream-analytics"></a>Azure Stream Analytics でのリアルタイム Twitter 感情分析

Azure Event Hubs に Twitter イベントをリアルタイム入力することで、ソーシャル メディア分析のためのセンチメント分析ソリューションを構築する方法について説明します。 このシナリオでは、Azure Stream Analytics クエリを記述してデータを分析します。 後で使用するために結果を保存するか、ダッシュボードと [Power BI](https://powerbi.com/) を使用して洞察をリアルタイムで表示します。

ソーシャル メディア分析ツールは、組織がトレンド トピックを把握するのに役立ちます。 トレンド トピックとは、ソーシャル メディアにおいて大量の投稿が行われているテーマや考え方のことです。 センチメント分析 (*意見マイニング*ともいう) ではソーシャル メディア分析ツールを使用して、製品やアイデアに対する考え方を特定します。

リアルタイム Twitter 傾向分析は、分析ツールの優れた例です。ハッシュタグ サブスクリプション モデルにより、特定のキーワードをリッスンし、フィードの感情分析を展開できます。

## <a name="scenario-social-media-sentiment-analysis-in-real-time"></a>シナリオ: リアルタイムのソーシャル メディア感情分析

ニュース メディア Web サイトを運営している会社は、その閲覧者に直接関係するサイト コンテンツを際立たせることで、競合他社より優位に立つことに着目しています。 そのため、会社は、Twitter データのリアルタイム感情分析を実行することで、閲覧者に関連するトピックのソーシャル メディア分析を使用します。

特に、Twitter のトレンディング トピックをリアルタイムで特定するには、主要なトピックのツイートの量とセンチメントに関するリアルタイム分析が必要です。 つまり、このソーシャル メディア フィードに基づいた感情分析エンジンが必要となります。

## <a name="prerequisites"></a>前提条件

* Azure サブスクリプション
* Twitter アカウントと [OAuth アクセス トークン](https://dev.twitter.com/oauth/overview/application-owner-access-tokens)
* GitHub の [TwitterWPFClient.zip](https://github.com/Azure/azure-stream-analytics/blob/master/Samples/TwitterClient/TwitterWPFClient.zip) ファイル
* 省略可能: [GitHub](https://aka.ms/azure-stream-analytics-twitterclient) の Twitter クライアントのソース コード

## <a name="create-an-event-hub-input"></a>イベント ハブへの入力の作成

サンプル アプリケーションでは、イベントを生成し、それらを Event Hubs インスタンス (略して、イベント ハブ) にプッシュします。 Azure Service Bus のイベント ハブは、Stream Analytics の場合に推奨されるイベント取り込み方法です。 詳細については、[Azure Service Bus のドキュメント](/azure/service-bus/)に関するページでイベント ハブのドキュメントを参照してください。

### <a name="create-an-event-hub"></a>イベント ハブの作成

イベント ハブを作成するには、次の手順に従います。

1. [Azure Portal](https://portal.azure.com) で、**[新規]** を選択し、「**Event Hubs**」と入力します。検索結果から **[Event Hubs]** を選択します。 **[作成]** を選択します。

2. イベント ハブの名前を指定し、リソース グループを作成します。 ここでは、それぞれ、`socialtwitter-eh` と `socialtwitter-rg` を指定しています。 アカウントをダッシュボードにピン留めするためのオプションを選択し、**[作成]** ボタンをクリックします。

3. デプロイが完了したら、イベント ハブを選択します。 次に、**[エンティティ]** で **[イベント ハブ]** を選択します。

4. イベント ハブを作成するには、**[+ イベント ハブ]** ボタンをクリックします。 もう一度名前を指定し (ここでは `socialtwitter-eh`)、**[作成]** を選択します。

5. イベント ハブへのアクセスを許可するには、共有アクセス ポリシーを作成する必要があります。 イベント ハブを選択し、**[設定]** の **[共有アクセス ポリシー]** を選択します。

6. **[共有アクセス ポリシー]** で **[+ 追加]** を選択し、**[管理]** アクセス許可を持つポリシーを作成します。 ポリシーに名前を付け、**[管理]** チェック ボックスをオンにし、**[作成]** を選択します。

7. 新しいポリシーが作成されたら、そのポリシーを選択し、**[接続文字列 – 主キー]** エンティティのコピー ボタンをクリックします。 この情報はこの演習の後半で必要になります。 ダッシュボードに戻ります。

![共有アクセス ポリシー エンドポイント](./media/stream-analytics-twitter-sentiment-analysis-trends/keysandendpoints.png)

## <a name="configure-and-start-the-twitter-client-application"></a>Twitter クライアント アプリケーションの構成および開始

パラメーター化された一連のトピックに関するツイート イベントを収集するために、[Twitter のストリーミング API](https://dev.twitter.com/streaming/overview) を介して Twitter データに接続するクライアント アプリケーションが作成されています。 [Sentiment140](http://help.sentiment140.com/) オープン ソース ツールにより、各ツイートにセンチメント値が次のように代入されます。

* 0 = 否定
* 2 = 中立
* 4 = 肯定

すると、ツイート イベントがイベント ハブにプッシュされます。  

### <a name="set-up-the-application"></a>アプリケーションの設定
 アプリケーションを設定するには、次の手順に従います。

1. [TwitterWPFClient.zip をダウンロード](https://github.com/Azure/azure-stream-analytics/blob/master/Samples/TwitterClient/TwitterWPFClient.zip)して、解凍します。

2. `TwitterWPFClient.exe` アプリケーションを実行します。 次に、Twitter API キーとシークレット、Twitter アクセス トークンとシークレット、さらにイベント ハブ情報のデータを入力します。 最後に、感情を判定するための基準となるキーワードを定義します。

### <a name="generate-an-oauth-access-token"></a>OAuth アクセス トークンの生成
詳細については、[OAuth アクセス トークンを生成する手順](https://dev.twitter.com/oauth/overview/application-owner-access-tokens)に関するページを参照してください。  

 トークンを生成するには、空のアプリケーションを作成する必要があります。  

1. TwitterWpfClient.exe.config 内の EventHubConnectionString 値と EventHubName 値を、イベント ハブの接続文字列と名前に置き換えます。 先ほどコピーした接続文字列は、イベント ハブの接続文字列と名前の両方を示しています。 それぞれを別々に適切なフィールドに入力するようにしてください。 たとえば、次の接続文字列の場合を考えてみましょう。

 ```
    Endpoint=sb://your.servicebus.windows.net/;SharedAccessKeyName=yourpolicy;SharedAccessKey=yoursharedaccesskey;EntityPath=yourhub
 ```  

   TwitterWpfClient.exe.config ファイルには、次の例で示されているように、設定を含める必要があります。

 ```
      add key="EventHubConnectionString" value="Endpoint=sb://your.servicebus.windows.net/;SharedAccessKeyName=yourpolicy;SharedAccessKey=yoursharedaccesskey"
      add key="EventHubName" value="yourhub"
   ```
   > [!NOTE]
   > EventHubName の値には "EntityPath=" というテキストが含まれて**いません**。

    Twitter と Azure の接続情報の値は、クライアントに直接入力することもできます。 "EntityPath=" は使用されないという、同じロジックが適用されます。

   ![wpfclient](./media/stream-analytics-twitter-sentiment-analysis-trends/wpfclientlines.png)

2. **省略可能:** 検索するキーワードを調整します。 既定では、このアプリケーションは、いくつかのゲームに関係するキーワードを検索します。  必要に応じて、TwitterWpfClient.exe.config の **twitter_keywords** の値を調整できます。

3. TwitterWpfClient.exe を実行します。 その後、緑のスタート ボタンを選択してソーシャル センチメントを収集します。 **CreatedAt**、**Topic**、**SentimentScore** の値が設定されたツイート イベントがイベント ハブに送信されていることがわかります。

## <a name="create-a-stream-analytics-job"></a>Stream Analytics のジョブの作成

Twitter からのリアルタイムのツイート イベントのストリーミングが行われているため、リアルタイムでこれらのイベントを分析するための Stream Analytics ジョブを設定できます。

### <a name="provision-a-stream-analytics-job"></a>Stream Analytics のジョブの準備

Stream Analytics のジョブをプロビジョニングするには、次の手順を実行します。

1. [Azure Portal](https://portal.azure.com/) で、**[新規]** を選択し、「**Stream Analytics**」と入力して、Stream Analytics タイルを選択します。

2. 次の値を指定し、**[作成]** を選択します。

   * **[ジョブ名]**: ジョブ名を入力します。

   * **[リソース グループ]**: [既存のものを使用] オプションから、この演習で先ほど作成したリソース グループを選択します。

   * **[ストレージ アカウント]**: このリージョン内で実行されているすべての Stream Analytics ジョブの監視データを格納するために使用する Azure ストレージ アカウントを選択します。 既存のストレージ アカウントを選択するか、新しいストレージ アカウントを作成することができます。   

![新しいジョブ](./media/stream-analytics-twitter-sentiment-analysis-trends/newjob.png)

ジョブは作成されると、Azure Portal で開かれます。

## <a name="specify-the-job-input"></a>ジョブの入力の指定
ジョブの入力を指定するには、次の手順を実行します。

1. Stream Analytics ジョブで、ジョブ ウィンドウの中央にある **[ジョブ トポロジ]** の **[入力]** をクリックします。 その後、**[追加]** を選択します。

    次に、ポータルでは、次の情報の一部を入力するよう求められます。 既定値のほとんどはそのまま使用できます。定義は次のとおりです。

   * **[入力のエイリアス]**: このジョブの入力のフレンドリ名を入力します (`TwitterStream` など)。 この名前は、後でクエリで使用します。  

   * **[イベント ハブ名]**: イベント ハブの名前を選択します。

   * **[イベント ハブ ポリシー名]**: このチュートリアルで前に作成したイベント ハブのポリシーを選択します。

2. **[作成]** ボタンを選択します。

## <a name="specify-the-job-query"></a>ジョブ クエリの指定

Stream Analytics は、変換を記述するための単純な宣言型のクエリのモデルをサポートします。 言語に関する詳細については、 [Azure Stream Analytics クエリ言語リファレンス](https://msdn.microsoft.com/library/azure/dn834998.aspx)を参照してください。  このチュートリアルでは、Twitter データに対するいくつかのクエリを作成してテストすることができます。

トピック間のメンション数を比較するには、[タンブリング ウィンドウ](https://msdn.microsoft.com/library/azure/dn835055.aspx)を使用して、5 秒ごとにトピック別のメンション数を取得します。

コード エディターで、クエリを次のように変更し、**[保存]** を選択します。

```
SELECT System.Timestamp as Time, Topic, COUNT(*)
FROM TwitterStream TIMESTAMP BY CreatedAt
GROUP BY TUMBLINGWINDOW(s, 5), Topic
```   

このクエリでは、 **TIMESTAMP BY** キーワードを使用して、一時的な計算に使用されるペイロードのタイムスタンプ フィールドを指定しています。 このフィールドが指定されていない場合は、各イベントがイベント ハブに到着した時間を使用してウィンドウ化操作が実行されます。 詳細については、「[Stream Analytics Query Language Reference](https://msdn.microsoft.com/library/azure/dn834998.aspx)」(Stream Analytics クエリ言語リファレンス) の「Arrival Time Vs Application Time」(到着時間とアプリケーション時間) を参照してください。

このクエリは、**System.Timestamp** プロパティを使用して、各期間の終わりのタイムスタンプにもアクセスします。

![クエリ ボックス](./media/stream-analytics-twitter-sentiment-analysis-trends/querybox.png)

## <a name="create-an-output-sink"></a>出力シンクの作成

イベント ストリーム、イベントを取り込むためのイベント ハブ入力、ストリームに対して変換を実行するためのクエリを定義したところで、最後に、ジョブの出力シンクを定義します。  

ジョブ クエリから Azure Blob Storage に集計済みツイート イベントを書き込みます。  また、結果は、具体的なアプリケーションのニーズに応じて、Azure SQL Database、Azure Table Storage、または Event Hubs にプッシュすることもできます。

## <a name="specify-the-job-output"></a>ジョブの出力の指定
ジョブの出力を指定するには、次の手順を実行します。

1. Stream Analytics ジョブで、**[ジョブ トポロジ]** の **[出力]** を選択し、**[追加]** を選択します。

2. ウィンドウで、次の値を入力または選択します。

   * **[出力のエイリアス]**: このジョブの出力のフレンドリ名を入力します。 このデモでは、`Output` を使用します。

   * **[シンク]**: **[Blob ストレージ]** を選択します。

   * **[ストレージ アカウント]**: **[新しいストレージ アカウントの作成]** を選択します。

    * **[ストレージ アカウント]**: 新しいストレージ アカウントに名前を指定します (この例では `socialtwitter`)。

    * **[コンテナー]**: 新しいコンテナーに名前を指定します (この例では `socialtwitter`)。

3. 他のエントリは既定値のままにします。 最後に、**[作成]** を選択します。

## <a name="start-the-job"></a>ジョブの開始

ジョブの入力、クエリ、および出力がすべて指定されたため、いつでも Stream Analytics ジョブを開始できます。

ジョブを開始するには、次の手順を実行します。

1. ジョブの概要ウィンドウで、ページの上部にある **[開始]** を選択します。

2. 開いたダイアログ ボックスで、**[ジョブ開始時刻]** を選択し、ダイアログ ボックスの下部にある**チェック** ボタンを選択します。 ジョブの状態がまず **[開始中]** に変化し、その後 **[実行中]** に変化します。

![ジョブを実行中](./media/stream-analytics-twitter-sentiment-analysis-trends/jobrunning.png)

## <a name="view-output-for-sentiment-analysis"></a>センチメント分析の出力表示

ジョブの実行が開始され、リアルタイム Twitter ストリームが処理されているときに、感情分析の出力の表示方法を選択します。

[Azure Storage エクスプローラー](https://http://storageexplorer.com/)や [Azure エクスプローラー](http://www.cerebrata.com/products/azure-explorer/introduction)などのツールを使用して、ジョブの出力をリアルタイムに表示できます。 ここで、[Power BI](https://powerbi.com/) を使用して、次のスクリーンショットに示されているように、カスタマイズされたダッシュボードを含むようにアプリケーションを拡張できます。

![Power BI](./media/stream-analytics-twitter-sentiment-analysis-trends/power-bi.png)

## <a name="create-another-query-to-identify-trending-topics"></a>トレンド トピックを特定する別のクエリの作成

このシナリオのために作成した別のサンプル クエリは、[スライディング ウィンドウ](https://msdn.microsoft.com/library/azure/dn835051.aspx)に基づいています。 トレンド トピックを特定するには、一定期間にメンションのしきい値を超えるトピックを検索します。

このチュートリアルでは、直近の 5 秒間で 20 回を超えてメンションされたトピックをチェックします。

```
SELECT System.Timestamp as Time, Topic, COUNT(*) as Mentions
FROM TwitterStream TIMESTAMP BY CreatedAt
GROUP BY SLIDINGWINDOW(s, 5), topic
HAVING COUNT(*) > 20
```

## <a name="use-field-headers"></a>フィールドのヘッダーの使用

この演習で使用できるフィールド ラベルを次の表に示します。 クエリ エディターで自由にお試しください。

JSON プロパティ | 定義
--- | ---
CreatedAt | ツイートが作成された時刻
トピック | 指定したキーワードと一致するトピック
SentimentScore | Sentiment140 のセンチメント スコア
Author | ツイートを送信した Twitter ハンドル
テキスト | ツイートの全文


## <a name="get-support"></a>サポートを受ける
さらにサポートが必要な場合は、 [Azure Stream Analytics フォーラム](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureStreamAnalytics)を参照してください。

## <a name="next-steps"></a>次のステップ
* [Azure Stream Analytics の概要](stream-analytics-introduction.md)
* [Azure Stream Analytics の使用](stream-analytics-get-started.md)
* [Azure Stream Analytics ジョブのスケーリング](stream-analytics-scale-jobs.md)
* [Stream Analytics Query Language Reference (Stream Analytics クエリ言語リファレンス)](https://msdn.microsoft.com/library/azure/dn834998.aspx)
* [Azure Stream Analytics management REST API reference (Azure ストリーム分析の管理 REST API リファレンス)](https://msdn.microsoft.com/library/azure/dn835031.aspx)

