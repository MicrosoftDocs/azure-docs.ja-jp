---
title: Azure Stream Analytics でのリアルタイム Twitter 感情分析
description: この記事では、リアルタイム Twitter 感情分析に Stream Analytics を使う方法について説明します。 イベントの生成からライブ ダッシュボード上でのデータ操作までの手順。
services: stream-analytics
author: jseb225
ms.author: jeanb
ms.reviewer: jasonh
manager: kfile
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 06/29/2017
ms.openlocfilehash: b5ad1a790f79b11b978c32c5751a6b9333f24c85
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2019
ms.locfileid: "57994929"
---
# <a name="real-time-twitter-sentiment-analysis-in-azure-stream-analytics"></a>Azure Stream Analytics でのリアルタイム Twitter 感情分析

Azure Event Hubs に Twitter イベントをリアルタイム入力することで、ソーシャル メディア分析のためのセンチメント分析ソリューションを構築する方法について説明します。 Azure Stream Analytics クエリを作成してデータを分析し、後で使用できるように結果を保存したり、ダッシュボードや [Power BI](https://powerbi.com/) を使用してリアルタイムで洞察を提供したりできます。

ソーシャル メディア分析ツールは、組織がトレンド トピックを把握するのに役立ちます。 トレンド トピックとは、ソーシャル メディアにおいて大量の投稿が行われているテーマや考え方のことです。 センチメント分析 (*意見マイニング*ともいう) ではソーシャル メディア分析ツールを使用して、製品やアイデアに対する考え方を特定します。 

リアルタイム Twitter 傾向分析は、分析ツールの好例です。ハッシュタグ サブスクリプション モデルにより、特定のキーワード (ハッシュタグ) をリッスンし、フィードの感情分析を展開できます。

## <a name="scenario-social-media-sentiment-analysis-in-real-time"></a>シナリオ: リアルタイムのソーシャル メディア感情分析

ニュース メディア Web サイトを運営している会社は、その閲覧者に直接関係するサイト コンテンツを際立たせることで、競合他社より優位に立つことに着目しています。 そのため、会社は、Twitter データのリアルタイム感情分析を実行することで、閲覧者に関連するトピックのソーシャル メディア分析を使用します。

Twitter のトレンド トピックをリアルタイムで特定するには、主要なトピックのツイートの量とセンチメントに関するリアルタイム分析が必要です。 つまり、このソーシャル メディア フィードに基づいた感情分析エンジンが必要となります。

## <a name="prerequisites"></a>前提条件
このチュートリアルでは、Twitter に接続し、特定のハッシュタグ (設定可能) が付いたツイートを検索するクライアント アプリケーションを使用します。 アプリケーションを実行し、Azure Stream Analytics を使用してツイートを分析するには、次のものが必要です。

* Azure サブスクリプション
* Twitter アカウント 
* Twitter アプリケーションと、そのアプリケーションの [OAuth アクセス トークン](https://dev.twitter.com/oauth/overview/application-owner-access-tokens)。 Twitter アプリケーションを作成する大まかな手順については後ほど説明します。
* Twitter フィードを読み取る TwitterWPFClient アプリケーション。 このアプリケーションを入手するには、GitHub から [TwitterWPFClient.zip](https://github.com/Azure/azure-stream-analytics/blob/master/Samples/TwitterClient/TwitterWPFClient.zip) ファイルをダウンロードし、コンピューター上のフォルダーにパッケージを解凍します。 ソース コードを確認し、デバッガーでアプリケーションを実行する場合は、[GitHub](https://github.com/Azure/azure-stream-analytics/tree/master/Samples/TwitterClient) からソース コードを入手できます。 

## <a name="create-an-event-hub-for-streaming-analytics-input"></a>Stream Analytics の入力用のイベント ハブの作成

サンプル アプリケーションでは、イベントを生成し、Azure イベント ハブにプッシュします。 Azure イベント ハブは、Stream Analytics に推奨されるイベント取り込み方法です。 詳細については、[Azure Event Hubs のドキュメント](../event-hubs/event-hubs-what-is-event-hubs.md)をご覧ください。


### <a name="create-an-event-hub-namespace-and-event-hub"></a>イベント ハブの名前空間とイベント ハブを作成する
この手順では、まずイベント ハブの名前空間を作成し、その名前空間にイベント ハブを追加します。 イベント ハブの名前空間は、関連するイベント バス インスタンスを論理的にグループ化するために使われます。 

1. Azure Portal にログインし、**[リソースの作成]** > **[モノのインターネット]** > **[イベント ハブ]** をクリックします。 

2. **[名前空間の作成]** ブレードで、名前空間の名前 (例: `<yourname>-socialtwitter-eh-ns`) を入力します。 名前空間には任意の名前を使用できますが、この名前は URL で有効である必要があり、Azure 全体で一意である必要があります。 
    
3. サブスクリプションを選択し、リソース グループを作成または選択して、**[作成]** をクリックします。 

    ![イベント ハブの名前空間の作成](./media/stream-analytics-twitter-sentiment-analysis-trends/stream-analytics-create-eventhub-namespace.png)
 
4. イベント ハブの名前空間のデプロイが完了したら、Azure リソースの一覧でその名前空間を見つけます。 

5. 新しい名前空間をクリックし、名前空間ブレードで **[+&nbsp;イベント ハブ]** をクリックします。 

    ![新しいイベント ハブを作成するための [イベント ハブの追加] ボタン](./media/stream-analytics-twitter-sentiment-analysis-trends/stream-analytics-create-eventhub-button.png)    
 
6. 新しいイベント ハブに `socialtwitter-eh` という名前を付けます。 別の名前を使用してもかまいません。 その場合、名前を書き留めておきます。後でこの名前が必要になります。 イベント ハブの他のオプションを設定する必要はありません。

    ![新しいイベント ハブを作成するためのブレード](./media/stream-analytics-twitter-sentiment-analysis-trends/stream-analytics-create-eventhub.png)
 
7. **Create** をクリックしてください。


### <a name="grant-access-to-the-event-hub"></a>イベント ハブへのアクセスを許可する

プロセスがイベント ハブにデータを送信できるようにするには、イベント ハブに、適切なアクセスを許可するポリシーが必要です。 アクセス ポリシーにより、承認情報を含む接続文字列が生成されます。

1.  イベント名前空間ブレードで、**[イベント ハブ]** をクリックし、新しいイベント ハブの名前をクリックします。

2.  イベント ハブ ブレードで、**[共有アクセス ポリシー]** をクリックし、**[+&nbsp;追加]** をクリックします。

    >[!NOTE]
    >イベント ハブの名前空間ではなく、イベント ハブを操作していることを確認してください。

3.  `socialtwitter-access` という名前のポリシーを追加し、**[要求]** の **[管理]** を選択します。

    ![イベント ハブの新しいアクセス ポリシーを作成するためのブレード](./media/stream-analytics-twitter-sentiment-analysis-trends/stream-analytics-create-shared-access-policy-manage.png)
 
4.  **Create** をクリックしてください。

5.  ポリシーがデプロイされたら、共有アクセス ポリシーの一覧でそのポリシーをクリックします。

6.  **[接続文字列 - 主キー]** というボックスを見つけ、接続文字列の横のコピー ボタンをクリックします。 
    
    ![アクセス ポリシーの接続文字列の主キーのコピー](./media/stream-analytics-twitter-sentiment-analysis-trends/stream-analytics-shared-access-policy-copy-connection-string.png)
 
7.  接続文字列をテキスト エディターに貼り付けます。 この接続文字列は、少し編集を加えた後に、次のセクションで必要になります。

    接続文字列は次のようになります。

        Endpoint=sb://YOURNAME-socialtwitter-eh-ns.servicebus.windows.net/;SharedAccessKeyName=socialtwitter-access;SharedAccessKey=Gw2NFZw6r...FxKbXaC2op6a0ZsPkI=;EntityPath=socialtwitter-eh

    接続文字列には、セミコロンで区切られた複数のキーと値のペア (`Endpoint`、`SharedAccessKeyName`、`SharedAccessKey`、`EntityPath`) が含まれています。  

    > [!NOTE]
    > セキュリティのため、この例では接続文字列の一部が削除されています。

8.  テキスト エディターで、接続文字列から `EntityPath` のペアを削除します (前にあるセミコロンも忘れずに削除してください)。 作業が完了すると、接続文字列は次のようになります。

        Endpoint=sb://YOURNAME-socialtwitter-eh-ns.servicebus.windows.net/;SharedAccessKeyName=socialtwitter-access;SharedAccessKey=Gw2NFZw6r...FxKbXaC2op6a0ZsPkI=


## <a name="configure-and-start-the-twitter-client-application"></a>Twitter クライアント アプリケーションの構成および開始
クライアント アプリケーションは、Twitter からツイート イベントを直接取得します。 そのために、クライアント アプリケーションには Twitter ストリーミング API を呼び出すためのアクセス許可が必要です。 このアクセス許可を構成するために、Twitter でアプリケーションを作成し、一意の資格情報 (OAuth トークンなど) を生成します。 次に、API 呼び出しを行うときにこれらの資格情報を使用するようにクライアント アプリケーションを構成します。 

### <a name="create-a-twitter-application"></a>Twitter アプリケーションを作成する
このチュートリアルで使用できる Twitter アプリケーションがまだない場合は、アプリケーションを作成できます。 Twitter アカウントを既に持っている必要があります。

> [!NOTE]
> Twitter でアプリケーションを作成し、キー、シークレット、トークンを取得するための正確なプロセスが変更されている場合があります。 以下の手順が Twitter サイトで表示される内容と一致しない場合は、Twitter の開発者向けドキュメントを参照してください。

1. [Twitter のアプリケーション管理ページ](https://apps.twitter.com/)に移動します。 

2. 新しいアプリケーションを作成します。 

   * Web サイトの URL に有効な URL を指定します。 これはライブ サイトである必要はありません  (`localhost` だけを指定することはできません)。
   * コールバック フィールドは空白のままにしておきます。 このチュートリアルで使用するクライアント アプリケーションではコールバックは不要です。

     ![Twitter でのアプリケーションの作成](./media/stream-analytics-twitter-sentiment-analysis-trends/create-twitter-application.png)

3. 必要に応じて、アプリケーションのアクセス許可を読み取り専用に変更します。

4. アプリケーションが作成されたら、**[Keys and Access Tokens]** ページに移動します。

5. アクセス トークンとアクセス トークン シークレットを生成するボタンをクリックします。

この情報は次の手順で必要になるため、手元に置いておいてください。

>[!NOTE]
>Twitter アプリケーションのキーとシークレットにより、Twitter アカウントにアクセスできるようになります。 Twitter のパスワードと同様に、この情報は機密情報として扱ってください。 たとえば、他のユーザーに提供するアプリケーションにこの情報を埋め込まないでください。 


### <a name="configure-the-client-application"></a>クライアント アプリケーションを構成する
特定のトピックに関するツイート イベントを収集するために、[Twitter のストリーミング API](https://dev.twitter.com/streaming/overview) を使用して Twitter データに接続するクライアント アプリケーションを作成しました。 このアプリケーションでは、次のセンチメント値を各ツイートに割り当てる、[Sentiment140](http://help.sentiment140.com/) オープン ソース ツールを使用します。

* 0 = 否定
* 2 = 中立
* 4 = 肯定

ツイート イベントにセンチメント値が割り当てられると、これらのイベントは先ほど作成したイベント ハブにプッシュされます。

アプリケーションを実行するには、Twitter のキーやイベント ハブの接続文字列などの特定の情報が必要となります。 構成情報は次の方法で指定できます。

* アプリケーションを実行し、アプリケーションの UI を使用して、キー、シークレット、接続文字列を入力します。 この場合、構成情報は現在のセッションで使用されますが、保存されません。
* アプリケーションの .config ファイルを編集し、値を設定します。 この方法では構成情報が保持されますが、この機密性の高い情報がコンピューター上にプレーン テキストで保存されることになります。

次の手順は両方の方法を示しています。 

1. 前提条件に記載されている [TwitterWPFClient.zip](https://github.com/Azure/azure-stream-analytics/blob/master/Samples/TwitterClient/TwitterWPFClient.zip) アプリケーションのダウンロードと解凍が完了していることを確認します。

2. 実行時に (現在のセッションでのみ) 値を設定するには、`TwitterWPFClient.exe` アプリケーションを実行します。 値の入力を求められたら、次の値を入力します。

    * Twitter のコンシューマー キー (API キー)。
    * Twitter のコンシューマー シークレット (API シークレット)。
    * Twitter のアクセス トークン。
    * Twitter のアクセス トークン シークレット。
    * 以前に保存した接続文字列情報。 `EntityPath` のキーと値のペアを削除した接続文字列を使用していることを確認してください。
    * 感情の判定対象となる Twitter のキーワード。

   ![設定を表示する (一部ぼかし表示)、実行中の TwitterWpfClient アプリケーション](./media/stream-analytics-twitter-sentiment-analysis-trends/wpfclientlines.png)

3. 値を永続的に設定するには、テキスト エディターを使用して TwitterWpfClient.exe.config ファイルを開きます。 次に、`<appSettings>` 要素内で次の操作を行います。

   * `oauth_consumer_key` を Twitter のコンシューマー キー (API キー) に設定します。 
   * `oauth_consumer_secret` を Twitter のコンシューマー シークレット (API シークレット) に設定します。
   * `oauth_token` を Twitter のアクセス トークンに設定します。
   * `oauth_token_secret` を Twitter のアクセス トークン シークレットに設定します。

     `<appSettings>` 要素の後の部分で次の変更を加えます。

   * `EventHubName` をイベント ハブ名 (つまり、エンティティ パスの値) に設定します。
   * `EventHubNameConnectionString` を接続文字列に設定します。 `EntityPath` のキーと値のペアを削除した接続文字列を使用していることを確認してください。

     `<appSettings>` セクションは、次の例のようになります  (わかりやすさとセキュリティのために、一部の行をラップし、一部の文字を削除しています)。

     ![Twitter のキーとシークレットおよびイベント ハブの接続文字列情報を示す、テキスト エディターの TwitterWpfClient アプリケーション構成ファイル](./media/stream-analytics-twitter-sentiment-analysis-trends/stream-analytics-tiwtter-app-config.png)
 
4. アプリケーションをまだ起動していなかった場合は、TwitterWpfClient.exe を実行します。 

5. 緑の開始ボタンをクリックして、ソーシャル センチメントを収集します。 **CreatedAt**、**Topic**、**SentimentScore** の値が設定されたツイート イベントがイベント ハブに送信されていることがわかります。

    ![ツイートの一覧を表示する、実行中の TwitterWpfClient アプリケーション](./media/stream-analytics-twitter-sentiment-analysis-trends/stream-analytics-twitter-app-listing.png)

    >[!NOTE]
    >エラーが発生し、ウィンドウの下部にツイートのストリームが表示されない場合は、キーとシークレットを再確認してください。 また、接続文字列も確認してください (`EntityPath` のキーと値が含まれていないことを確認します)。


## <a name="create-a-stream-analytics-job"></a>Stream Analytics のジョブの作成

ツイート イベントが Twitter からリアルタイムでストリーミングされるようになったので、これらのイベントをリアルタイムで分析する Stream Analytics ジョブを設定します。

1. Azure Portal で、**[リソースの作成]** > **[モノのインターネット]** > **[Stream Analytics ジョブ]** の順にクリックします。

2. ジョブに `socialtwitter-sa-job` という名前を付け、サブスクリプション、リソース グループ、場所を指定します。

    最適なパフォーマンスを実現し、リージョン間でのデータ転送の料金がかからないように、ジョブとイベント ハブを同じリージョンに配置することをお勧めします。

    ![新しい Stream Analytics ジョブの作成](./media/stream-analytics-twitter-sentiment-analysis-trends/newjob.png)

3. **Create** をクリックしてください。

    ジョブが作成され、ポータルにジョブの詳細が表示されます。


## <a name="specify-the-job-input"></a>ジョブの入力の指定

1. Stream Analytics ジョブで、ジョブ ブレードの中央にある **[ジョブ トポロジ]** の **[入力]** をクリックします。 

2. **[入力]** ブレードで **[+&nbsp;追加]** をクリックし、ブレードに次の値を入力します。

   * **入力のエイリアス**:`TwitterStream` という名前を使用します。 この名前は後で必要になるため、別の名前を使用する場合は書き留めておきます。
   * **[ソースの種類]**: **[データ ストリーム]** を選択します。
   * **ソース**:**[イベント ハブ]** を選択します。
   * **[インポート オプション]**: **[現在のサブスクリプションのイベント ハブを使う]** を選択します。 
   * **[Service Bus 名前空間]**: 以前に作成したイベント ハブの名前空間 (`<yourname>-socialtwitter-eh-ns`) を選択します。
   * **[イベント ハブ]**: 以前に作成したイベント ハブ (`socialtwitter-eh`) を選択します。
   * **[イベント ハブ ポリシー名]**: 以前に作成したアクセス ポリシー (`socialtwitter-access`) を選択します。

     ![Stream Analytics ジョブの新しい入力の作成](./media/stream-analytics-twitter-sentiment-analysis-trends/stream-analytics-twitter-new-input.png)

3. **Create** をクリックしてください。


## <a name="specify-the-job-query"></a>ジョブ クエリの指定

Stream Analytics は、変換を記述するための単純な宣言型のクエリのモデルをサポートします。 言語に関する詳細については、 [Azure Stream Analytics クエリ言語リファレンス](https://msdn.microsoft.com/library/azure/dn834998.aspx)を参照してください。  このチュートリアルでは、Twitter データに対するいくつかのクエリを作成してテストすることができます。

トピック間のメンション数を比較するには、[タンブリング ウィンドウ](https://msdn.microsoft.com/library/azure/dn835055.aspx)を使用して、5 秒ごとにトピック別のメンション数を取得します。

1. **[入力]** ブレードを閉じます (まだ閉じていない場合)。

2. **[概要]** ブレードで、クエリ ボックスの右上付近にある **[クエリの編集]** をクリックします。 そのジョブ用に構成されている入力と出力が一覧表示されます。出力に送信される入力ストリームを変換できるクエリを作成できます。

3. TwitterWpfClient アプリケーションが実行されていることを確認します。 

3. **[クエリ]** ブレードで、`TwitterStream` 入力の横の点をクリックし、**[入力からのサンプル データ]** を選択します。

    ![Stream Analytics ジョブ エントリにサンプル データを使用するためのメニュー オプション - [入力からのサンプル データ] を選択](./media/stream-analytics-twitter-sentiment-analysis-trends/stream-analytics-create-sample-data-from-input.png)

    取得するサンプル データの量を指定できるブレードが開きます。データの量は、入力ストリームの読み取り期間で定義されています。

4. **[分]** を 3 に設定し、**[OK]** をクリックします。 
    
    !["3 分" が選択された入力ストリームのサンプリング オプション。](./media/stream-analytics-twitter-sentiment-analysis-trends/stream-analytics-input-create-sample-data.png)

    入力ストリームから 3 分間分のデータがサンプリングされ、サンプル データの準備ができると通知されます  (少し時間がかかります)。 

    サンプル データは一時的に保存され、クエリ ウィンドウを開いている間使用できます。 クエリ ウィンドウを閉じると、サンプル データは破棄されるので、サンプル データの新しいセットを作成する必要があります。 

5. コード エディターでクエリを次のように変更します。

    ```
    SELECT System.Timestamp as Time, Topic, COUNT(*)
    FROM TwitterStream TIMESTAMP BY CreatedAt
    GROUP BY TUMBLINGWINDOW(s, 5), Topic
    ```

    入力のエイリアスとして `TwitterStream` を使用しなかった場合は、クエリで `TwitterStream` の代わりに選択したエイリアスを使用します。  

    このクエリでは、 **TIMESTAMP BY** キーワードを使用して、一時的な計算に使用されるペイロードのタイムスタンプ フィールドを指定しています。 このフィールドが指定されていない場合は、各イベントがイベント ハブに到着した時間を使用してウィンドウ化操作が実行されます。 詳細については、「[Stream Analytics Query Language Reference](https://msdn.microsoft.com/library/azure/dn834998.aspx)」(Stream Analytics クエリ言語リファレンス) の「Arrival Time Vs Application Time」(到着時間とアプリケーション時間) を参照してください。

    このクエリは、**System.Timestamp** プロパティを使用して、各期間の終わりのタイムスタンプにもアクセスします。

5. **[Test]** をクリックします。 サンプリングされたデータに対してクエリが実行されます。
    
6. **[Save]** をクリックします。 クエリが Stream Analytics ジョブの一部として保存されます  (サンプル データは保存されません)。


## <a name="experiment-using-different-fields-from-the-stream"></a>ストリームのさまざまなフィールドを使用した実験 

次の表に、JSON ストリーミング データに含まれるフィールドを示します。 クエリ エディターで自由にお試しください。

|JSON プロパティ | 定義|
|--- | ---|
|CreatedAt | ツイートが作成された時刻|
|トピック | 指定したキーワードと一致するトピック|
|SentimentScore | Sentiment140 のセンチメント スコア|
|Author | ツイートを送信した Twitter ハンドル|
|Text | ツイートの全文|


## <a name="create-an-output-sink"></a>出力シンクの作成

イベント ストリーム、イベントを取り込むためのイベント ハブ入力、ストリームに対して変換を実行するためのクエリを定義しました。 最後に、ジョブの出力シンクを定義します。  

このチュートリアルでは、ジョブ クエリから Azure Blob Storage に集計済みのツイート イベントを書き込みます。  アプリケーションのニーズに応じて、Azure SQL Database、Azure Table Storage、Event Hubs、または Power BI に結果をプッシュすることもできます。

## <a name="specify-the-job-output"></a>ジョブの出力の指定

1. **[ジョブ トポロジ]** セクションで、**[出力]** ボックスをクリックします。 

2. **[出力]** ブレードで **[+&nbsp;追加]** をクリックし、ブレードに次の値を入力します。

   * **出力のエイリアス**:`TwitterStream-Output` という名前を使用します。 
   * **シンク**: **[Blob ストレージ]** を選択します。
   * **[インポート オプション]**: **[現在のサブスクリプションの BLOB ストレージを使う]** を選択します。
   * **[ストレージ アカウント]**。 **[新しいストレージ アカウントを作成する]** を選択します。
   * **[ストレージ アカウント]** (2 つ目のボックス):  「`YOURNAMEsa`」と入力します。`YOURNAME` は、自分の名前または別の一意の文字列です。 名前には小文字と数字だけを使用できます。名前は Azure 全体で一意である必要があります。 
   * **[コンテナー]**:  「 `socialtwitter` 」を入力します。
     ストレージ アカウント名とコンテナー名は、BLOB ストレージの URI を指定するときに次のように組み合わせて使われます。 

     `http://YOURNAMEsa.blob.core.windows.net/socialtwitter/...`
    
     ![Stream Analytics ジョブの [新しい出力] ブレード](./media/stream-analytics-twitter-sentiment-analysis-trends/stream-analytics-create-output-blob-storage.png)
    
4. **Create** をクリックしてください。 

    ストレージ アカウントが作成され、キーが自動的に生成されます。 

5. **[出力]** ブレードを閉じます。 


## <a name="start-the-job"></a>ジョブの開始

ジョブ入力、クエリ、出力が指定されました。 これで、Stream Analytics ジョブを開始する準備が整いました。

1. TwitterWpfClient アプリケーションが実行されていることを確認します。 

2. ジョブ ブレードで、**[開始]** をクリックします。

    ![Stream Analytics ジョブの開始](./media/stream-analytics-twitter-sentiment-analysis-trends/stream-analytics-sa-job-start-output.png)

3. **[ジョブの開始]** ブレードで、**[ジョブ出力の開始時刻]** の **[Now]\(今すぐ\)** を選択し、**[開始]** をクリックします。 

    ![Stream Analytics ジョブの [ジョブの開始] ブレード](./media/stream-analytics-twitter-sentiment-analysis-trends/stream-analytics-sa-job-start-job-blade.png)

    ジョブが開始されたことが通知され、ジョブ ブレードに表示される状態が **[実行中]** になります。

    ![ジョブを実行中](./media/stream-analytics-twitter-sentiment-analysis-trends/jobrunning.png)

## <a name="view-output-for-sentiment-analysis"></a>センチメント分析の出力表示

ジョブの実行が開始されたら、リアルタイム Twitter ストリームの処理中に、感情分析の出力を表示できます。

[Azure Storage エクスプローラー](https://storageexplorer.com/)や [Azure エクスプローラー](https://www.cerebrata.com/products/azure-explorer/introduction)などのツールを使用して、ジョブ出力をリアルタイムで表示できます。 ここから、[Power BI](https://powerbi.com/) を使用してアプリケーションを拡張し、次のスクリーンショットに示すようなカスタマイズされたダッシュボードを含めることができます。

![Power BI](./media/stream-analytics-twitter-sentiment-analysis-trends/power-bi.png)


## <a name="create-another-query-to-identify-trending-topics"></a>トレンド トピックを特定する別のクエリの作成

Twitter のセンチメントを理解するために使用できるもう 1 つのクエリは、[スライディング ウィンドウ](https://msdn.microsoft.com/library/azure/dn835051.aspx)に基づいています。 トレンド トピックを特定するには、指定された期間にメンションのしきい値を超えるトピックを検索します。

このチュートリアルでは、直近の 5 秒間で 20 回を超えてメンションされたトピックをチェックします。

1. ジョブ ブレードで **[停止]** をクリックしてジョブを停止します。 

2. **[ジョブ トポロジ]** セクションで、**[クエリ]** ボックスをクリックします。 

3. クエリを次のように変更します。

    ```    
    SELECT System.Timestamp as Time, Topic, COUNT(*) as Mentions
    FROM TwitterStream TIMESTAMP BY CreatedAt
    GROUP BY SLIDINGWINDOW(s, 5), topic
    HAVING COUNT(*) > 20
    ```

4. **[Save]** をクリックします。

5. TwitterWpfClient アプリケーションが実行されていることを確認します。 

6. **[開始]** をクリックし、新しいクエリを使用してジョブを再開します。


## <a name="get-support"></a>サポートを受ける
さらにサポートが必要な場合は、 [Azure Stream Analytics フォーラム](https://social.msdn.microsoft.com/Forums/azure/home?forum=AzureStreamAnalytics)を参照してください。

## <a name="next-steps"></a>次のステップ
* [Azure Stream Analytics の概要](stream-analytics-introduction.md)
* [Azure Stream Analytics の使用](stream-analytics-real-time-fraud-detection.md)
* [Azure Stream Analytics ジョブのスケーリング](stream-analytics-scale-jobs.md)
* [Stream Analytics Query Language Reference (Stream Analytics クエリ言語リファレンス)](https://msdn.microsoft.com/library/azure/dn834998.aspx)
* [Azure Stream Analytics management REST API reference (Azure ストリーム分析の管理 REST API リファレンス)](https://msdn.microsoft.com/library/azure/dn835031.aspx)
