---
title: Azure Stream Analytics でのリアルタイム Twitter 感情分析
description: この記事では、リアルタイム Twitter 感情分析に Stream Analytics を使う方法について説明します。 イベントの生成からライブ ダッシュボード上でのデータ操作までの手順。
services: stream-analytics
author: enkrumah
ms.author: ebnkruma
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: how-to
ms.date: 02/10/2020
ms.openlocfilehash: 6a461ad906f7611c8a13e2ee495f4d2f62fedd53
ms.sourcegitcommit: 78ecfbc831405e8d0f932c9aafcdf59589f81978
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/23/2021
ms.locfileid: "98734842"
---
# <a name="real-time-twitter-sentiment-analysis-in-azure-stream-analytics"></a>Azure Stream Analytics でのリアルタイム Twitter 感情分析

この記事では、Azure Event Hubs に Twitter イベントをリアルタイム入力することで、ソーシャル メディアのセンチメント分析ソリューションを構築する方法について説明します。 Azure Stream Analytics クエリを作成してデータを分析し、後で使用できるように結果を保存したり、[Power BI](https://powerbi.com/) ダッシュボードを作成してリアルタイムで分析情報を提供したりすることができます。

ソーシャル メディア分析ツールは、組織がトレンド トピックを把握するのに役立ちます。 トレンド トピックとは、ソーシャル メディアにおいて大量の投稿が行われているテーマや考え方のことです。 センチメント分析 (*意見マイニング* ともいう) ではソーシャル メディア分析ツールを使用して、製品やアイデアに対する考え方を特定します。 

リアルタイム Twitter 傾向分析は、分析ツールの好例です。ハッシュタグ サブスクリプション モデルにより、特定のキーワード (ハッシュタグ) をリッスンし、フィードの感情分析を展開できます。

## <a name="scenario-social-media-sentiment-analysis-in-real-time"></a>シナリオ:リアルタイムのソーシャル メディア感情分析

ニュース メディア Web サイトを運営している会社は、その閲覧者に直接関係するサイト コンテンツを際立たせることで、競合他社より優位に立つことに着目しています。 そのため、会社は、Twitter データのリアルタイム感情分析を実行することで、閲覧者に関連するトピックのソーシャル メディア分析を使用します。

Twitter のトレンド トピックをリアルタイムで特定するには、主要なトピックのツイートの量とセンチメントに関するリアルタイム分析が必要です。

## <a name="prerequisites"></a>前提条件

この攻略ガイドでは、Twitter に接続し、特定のハッシュタグ (設定可能) が付いたツイートを検索するクライアント アプリケーションを使用します。 アプリケーションを実行し、Azure Stream Analytics を使用してツイートを分析するには、次のものが必要です。

* Azure サブスクリプションをお持ちでない場合は、[無料アカウント](https://azure.microsoft.com/free/)を作成してください。

* [Twitter](https://twitter.com) アカウント。

* Twitter フィードを読み取る TwitterClientCore アプリケーション。 このアプリケーションを入手するには、[TwitterClientCore](https://github.com/Azure/azure-stream-analytics/tree/master/DataGenerators/TwitterClientCore) をダウンロードしてください。

* [.NET Core CLI](/dotnet/core/tools/?tabs=netcore2x) バージョン 2.1.0 をインストールします。

実装するソリューション アーキテクチャを下に示します。

   ![ソリューションの構築に使用されるさまざまなサービスとアプリケーションの部分を示す図。](./media/stream-analytics-twitter-sentiment-analysis-trends/solution-diagram.png "ソリューション ダイアグラム")

## <a name="create-an-event-hub-for-streaming-input"></a>入力をストリームするためのイベント ハブの作成

サンプル アプリケーションでは、イベントを生成し、Azure イベント ハブにプッシュします。 Azure Event Hubs は、Stream Analytics に推奨されるイベント取り込み方法です。 詳細については、[Azure Event Hubs のドキュメント](../event-hubs/event-hubs-about.md)をご覧ください。

### <a name="create-an-event-hub-namespace-and-event-hub"></a>イベント ハブの名前空間とイベント ハブを作成する
このセクションでは、イベント ハブの名前空間を作成し、その名前空間にイベント ハブを追加します。 イベント ハブの名前空間は、関連するイベント バス インスタンスを論理的にグループ化するために使われます。 

1. Azure portal にログインし、 **[リソースの作成]** を選択します。 次に、 **[Event Hubs]** を検索し、 **[作成]** を選択します。

2. **[名前空間の作成]** ページで、名前空間の名前を入力します。 名前空間には任意の名前を使用できますが、この名前は URL で有効である必要があり、Azure 全体で一意である必要があります。 
    
3. 価格レベルとサブスクリプションを選択し、リソース グループを作成または選択します。 次に場所を選択し、 **[作成]** を選択します。 
 
4. 名前空間のデプロイが完了したら、リソース グループに移動し、Azure リソースの一覧でそのイベント ハブの名前空間を見つけます。 

5. 新しい名前空間で、 **[+&nbsp; イベントハブ]** を選択します。 

6. 新しいイベント ハブに *socialtwitter-eh* という名前を指定します。 別の名前を使用してもかまいません。 その場合、名前を書き留めておきます。後でこの名前が必要になります。 イベント ハブの他のオプションを設定する必要はありません。
 
7. **［作成］** を選択します

### <a name="grant-access-to-the-event-hub"></a>イベント ハブへのアクセスを許可する

プロセスがイベント ハブにデータを送信できるようにするには、イベント ハブに、アクセスを許可するポリシーが必要です。 アクセス ポリシーにより、承認情報を含む接続文字列が生成されます。

1.  イベント ハブの名前空間の左側にあるナビゲーション バーで、 **[Event Hubs]** を選択します。これは、 **[エンティティ]** セクションにあります。 次に、先ほど作成したイベント ハブを選択します。

2.  左側のナビゲーション バーで、 **[設定]** の下にある **[共有アクセス ポリシー]** を選択します。

    >[!NOTE]
    >[共有アクセス ポリシー] のオプションは、イベント ハブの名前空間用とイベント ハブ用があります。 イベント ハブの名前空間全体ではなく、イベント ハブのコンテキストで作業していることを確認してください。

3.  [アクセス ポリシー] ページで、 **[+ 追加]** を選択します。 次に、 **[ポリシー名]** に *socialtwitter-access* と入力し、 **[管理]** チェックボックスをオンにします。
 
4.  **［作成］** を選択します

5.  ポリシーがデプロイされたら、共有アクセス ポリシーの一覧からそのポリシーを選択します。

6.  **[接続文字列 - 主キー]** というボックスを見つけ、接続文字列の横のコピー ボタンを選択します。
 
7.  接続文字列をテキスト エディターに貼り付けます。 この接続文字列は、少し編集を加えた後に、次のセクションで必要になります。

   接続文字列は次のようになります。
   
   ```
   Endpoint=sb://EVENTHUBS-NAMESPACE.servicebus.windows.net/;SharedAccessKeyName=socialtwitter-access;SharedAccessKey=Gw2NFZw6r...FxKbXaC2op6a0ZsPkI=;EntityPath=socialtwitter-eh
   ```

   接続文字列には、セミコロンで区切られた複数のキーと値のペア (`Endpoint`、`SharedAccessKeyName`、`SharedAccessKey`、`EntityPath`) が含まれています。  

   > [!NOTE]
   > セキュリティのため、この例では接続文字列の一部が削除されています。

## <a name="configure-and-start-the-twitter-client-application"></a>Twitter クライアント アプリケーションの構成および開始

クライアント アプリケーションは、Twitter からツイート イベントを直接取得します。 そのために、クライアント アプリケーションには Twitter ストリーミング API を呼び出すためのアクセス許可が必要です。 このアクセス許可を構成するために、Twitter でアプリケーションを作成し、一意の資格情報 (OAuth トークンなど) を生成します。 次に、API 呼び出しを行うときにこれらの資格情報を使用するようにクライアント アプリケーションを構成します。 

### <a name="create-a-twitter-application"></a>Twitter アプリケーションを作成する
この攻略ガイドで使用できる Twitter アプリケーションがまだない場合は、アプリケーションを作成できます。 Twitter アカウントを既に持っている必要があります。

> [!NOTE]
> Twitter でアプリケーションを作成し、キー、シークレット、トークンを取得するための正確なプロセスが変更されている場合があります。 以下の手順が Twitter サイトで表示される内容と一致しない場合は、Twitter の開発者向けドキュメントを参照してください。

1. Web ブラウザーで [Twitter の開発者用ページ](https://developer.twitter.com/en/apps)に移動して、開発者アカウントを作成し、 **[Create an app]\(アプリの作成\)** を選択します。 Twitter 開発者アカウントを申請する必要があるというメッセージが表示される場合があります。 お気軽にお申し込みください。申請が承認されると、確認メールを受け取ります。 開発者アカウントの承認には、数日かかることがあります。

   ![スクリーンショットには、[Create an app]\(アプリの作成\) ボタンが示されています。](./media/stream-analytics-twitter-sentiment-analysis-trends/provide-twitter-app-details.png "Twitter アプリケーションの詳細")

2. **[Create an application]\(アプリケーションの作成\)** ページで新しいアプリの詳細を入力し、 **[Create your Twitter application]\(Twitter アプリケーションの作成\)** を選択します。

   ![スクリーンショットには、アプリの値を入力できるアプリの詳細ウィンドウが示されています。](./media/stream-analytics-twitter-sentiment-analysis-trends/provide-twitter-app-details-create.png "Twitter アプリケーションの詳細")

3. アプリケーションのページで **[Keys and Tokens]\(キーとトークン\)** タブを選択し、 **[Consumer API Key]\(コンシューマー API キー\)** と **[Consumer API Secret Key]\(コンシューマー API シークレット キー\)** の値をコピーします。 さらに、 **[Access Token and Access Token Secret]\(アクセス トークンとアクセス トークン シークレット\)** の下の **[Create]\(作成\)** を選択して、アクセス トークンを生成します。 **[Access Token]\(アクセス トークン\)** と **[Access Token Secret]\(アクセス トークン シークレット\)** の値をコピーします。

   Twitter アプリケーションについて取得した値を保存します。 これらの値は後で必要になります。

> [!NOTE]
> Twitter アプリケーションのキーとシークレットにより、Twitter アカウントにアクセスできるようになります。 Twitter のパスワードと同様に、この情報は機密情報として扱ってください。 たとえば、他のユーザーに提供するアプリケーションにこの情報を埋め込まないでください。 

### <a name="configure-the-client-application"></a>クライアント アプリケーションを構成する

特定のトピックに関するツイート イベントを収集するために、[Twitter のストリーミング API](https://dev.twitter.com/streaming/overview) を使用して Twitter データに接続するクライアント アプリケーションを作成しました。

アプリケーションを実行するには、Twitter のキーやイベント ハブの接続文字列などの特定の情報が必要となります。

1. 前提条件に記載されている [TwitterClientCore](https://github.com/Azure/azure-stream-analytics/tree/master/DataGenerators/TwitterClientCore) アプリケーションのダウンロードが完了していることを確認します。

2. テキスト エディターを使用して、*App.config* ファイルを開きます。 `<appSettings>` 要素を次のように変更します。

   * `oauth_consumer_key` を Twitter のコンシューマー キー (API キー) に設定します。 
   * `oauth_consumer_secret` を Twitter のコンシューマー シークレット (API シークレット) に設定します。
   * `oauth_token` を Twitter のアクセス トークンに設定します。
   * `oauth_token_secret` を Twitter のアクセス トークン シークレットに設定します。
   * `EventHubNameConnectionString` を接続文字列に設定します。
   * `EventHubName` をイベント ハブ名 (つまり、エンティティ パスの値) に設定します。

3. コマンド ラインを開き、TwitterClientCore アプリが配置されているディレクトリに移動します。 コマンド `dotnet build` を使用してプロジェクトをビルドします。 次にコマンド `dotnet run` を使用してアプリを実行します。 アプリによりツイートがイベント ハブに送信されます。

## <a name="create-a-stream-analytics-job"></a>Stream Analytics のジョブの作成

ツイート イベントが Twitter からリアルタイムでストリーミングされるようになったので、これらのイベントをリアルタイムで分析する Stream Analytics ジョブを設定します。

1. Azure portal で、お使いのリソース グループに移動し、 **[+ 追加]** を選択します。 次に、**Stream Analytics ジョブ** を検索し、 **[作成]** 選択します。

2. ジョブに `socialtwitter-sa-job` という名前を付け、サブスクリプション、リソース グループ、場所を指定します。

    最適なパフォーマンスを実現し、リージョン間でのデータ転送の料金がかからないように、ジョブとイベント ハブを同じリージョンに配置することをお勧めします。

3. **［作成］** を選択します デプロイが完了したら、ジョブに移動します。

## <a name="specify-the-job-input"></a>ジョブの入力の指定

1. Stream Analytics ジョブで、 **[ジョブ トポロジ]** の下の左側のメニューで **[入力]** を選択します。

2. **[+&nbsp;ストリーム入力の追加]**  >  **[イベント ハブ]** を選択します。 **[新規入力]** フォームに次の情報を入力します。

   |**設定**  |**推奨値**  |**説明**  |
   |---------|---------|---------|
   |入力のエイリアス| *TwitterStream* | 入力のエイリアスを入力します。 |
   |サブスクリプション  | \<Your subscription\> |  使用する Azure サブスクリプションを選択します。 |
   |Event Hub 名前空間 | *asa-twitter-eventhub* |
   |イベント ハブ名 | *socialtwitter-eh* | [*既存を使用*] を選択します。 次に、作成したイベント ハブを選択します。|
   |イベントの圧縮タイプ| GZip | データ圧縮の種類。|

   他の既定値はそのままにし、 **[保存]** を選択します。

## <a name="specify-the-job-query"></a>ジョブ クエリの指定

Stream Analytics は、変換を記述するための単純な宣言型のクエリのモデルをサポートします。 言語に関する詳細については、 [Azure Stream Analytics クエリ言語リファレンス](/stream-analytics-query/stream-analytics-query-language-reference)を参照してください。 この攻略ガイドでは、Twitter データに対するいくつかのクエリを作成してテストすることができます。

トピック間のメンション数を比較するには、[タンブリング ウィンドウ](/stream-analytics-query/tumbling-window-azure-stream-analytics)を使用して、5 秒ごとにトピック別のメンション数を取得します。

1. ジョブの **[概要]** で、クエリ ボックスの右上付近にある **[クエリの編集]** を選択します。 そのジョブ用に構成されている入力と出力が一覧表示され、出力に送信される入力ストリームを変換するクエリを作成できます。

2. クエリ エディターでクエリを次のように変更します。

   ```sql
   SELECT *
   FROM TwitterStream
   ```

3. メッセージのイベント データは、クエリの下の **[入力プレビュー]** ウィンドウに表示されます。 **[ビュー]** が **[JSON]** に設定されていることを確認します。 データが表示されない場合は、データ ジェネレーターがイベント ハブにイベントを送信していること、および入力の圧縮の種類として **GZip** が選択されていることを確認します。

4. **[クエリのテスト]** を選択し、クエリの下の **[テスト結果]** ウィンドウで結果を確認します。

5. コード エディターでクエリを次のように変更し、 **[クエリのテスト]** を選択します。

   ```sql
   SELECT System.Timestamp as Time, text
   FROM TwitterStream
   WHERE text LIKE '%Azure%'
   ```

6. このクエリは、キーワード *Azure* を含むすべてのツイートを返します。

## <a name="create-an-output-sink"></a>出力シンクの作成

イベント ストリーム、イベントを取り込むためのイベント ハブ入力、ストリームに対して変換を実行するためのクエリを定義しました。 最後に、ジョブの出力シンクを定義します。  

この攻略ガイドでは、ジョブ クエリから Azure Blob Storage に集計済みのツイート イベントを書き込みます。  アプリケーションのニーズに応じて、Azure SQL Database、Azure Table Storage、Event Hubs、または Power BI に結果をプッシュすることもできます。

## <a name="specify-the-job-output"></a>ジョブの出力の指定

1. 左側のナビゲーションメニューの **[ジョブ トポロジ]** セクションで、 **[出力]** を選択します。 

2. **[出力]** ページで、 **[+&nbsp;追加]** 、 **[Blob Storage/Data Lake Storage Gen2]** の順にクリックします。

   * **出力のエイリアス**:`TwitterStream-Output` という名前を使用します。 
   * **[インポート オプション]** : **[サブスクリプションからストレージを選択する]** を選択します。
   * **[ストレージ アカウント]** 。 使うストレージ アカウントを選びます。
   * **[コンテナー]** 。 **[新規作成]** を選択して `socialtwitter` と入力します。
   
4. **[保存]** を選択します。   

## <a name="start-the-job"></a>ジョブの開始

ジョブ入力、クエリ、出力が指定されました。 これで、Stream Analytics ジョブを開始する準備が整いました。

1. TwitterClientCore アプリケーションが実行されていることを確認します。 

2. ジョブの概要で、 **[開始]** を選択します。

3. **[ジョブの開始]** ページで、 **[ジョブ出力の開始時刻]** の **[Now]\(今すぐ\)** を選択し、 **[開始]** を選択します。

## <a name="get-support"></a>サポートを受ける
詳細については、[Azure Stream Analytics に関する Microsoft Q&A 質問ページ](/answers/topics/azure-stream-analytics.html)を参照してください。

## <a name="next-steps"></a>次のステップ
* [Azure Stream Analytics の概要](stream-analytics-introduction.md)
* [Azure Stream Analytics の使用](stream-analytics-real-time-fraud-detection.md)
* [Azure Stream Analytics ジョブのスケーリング](stream-analytics-scale-jobs.md)
* [Stream Analytics Query Language Reference (Stream Analytics クエリ言語リファレンス)](/stream-analytics-query/stream-analytics-query-language-reference)
* [Azure Stream Analytics management REST API reference (Azure ストリーム分析の管理 REST API リファレンス)](/rest/api/streamanalytics/)
