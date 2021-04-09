---
title: チュートリアル - Azure Stream Analytics で不正な通話のデータを分析し、Power BI ダッシュボードで結果を視覚化する
description: このチュートリアルでは、通話ストリームにおける不正な呼び出しを Azure Stream Analytics を使用して分析する方法を、デモでまるごとお見せします。
author: enkrumah
ms.author: ebnkruma
ms.service: stream-analytics
ms.topic: tutorial
ms.custom: contperf-fy21q2
ms.date: 12/17/2020
ms.openlocfilehash: 1e26159c07ca551a78ee2f83a0ca64779c60f7b4
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "98018872"
---
# <a name="tutorial-analyze-fraudulent-call-data-with-stream-analytics-and-visualize-results-in-power-bi-dashboard"></a>チュートリアル:Stream Analytics で不正な通話のデータを分析し、Power BI ダッシュボードで結果を視覚化する

このチュートリアルでは、Azure Stream Analytics を使用して通話データを分析する方法について説明します。 クライアント アプリケーションによって生成される通話データには不正な通話が含まれており、これは Stream Analytics ジョブによってフィルター処理されます。 このチュートリアルの技法は、クレジット カード詐欺やなりすましなど、他の種類の不正行為検出にも使用できます。

このチュートリアルでは、以下の内容を学習します。

> [!div class="checklist"]
> * サンプル通話データを生成して Azure Event Hubs に送信します。
> * Stream Analytics ジョブを作成します。
> * ジョブの入力と出力を構成します。
> * 不正な通話をフィルター処理するようクエリを定義します。
> * ジョブをテストして開始します。
> * Power BI で結果を視覚化します。

## <a name="prerequisites"></a>[前提条件]

始める前に、次の手順が完了していることを確認してください。

* Azure サブスクリプションをお持ちでない場合は、[無料アカウント](https://azure.microsoft.com/free/)を作成してください。
* Microsoft ダウンロード センターから通話イベント ジェネレーター アプリ [TelcoGenerator.zip](https://download.microsoft.com/download/8/B/D/8BD50991-8D54-4F59-AB83-3354B69C8A7E/TelcoGenerator.zip) をダウンロードします。または、[GitHub](https://aka.ms/azure-stream-analytics-telcogenerator) からソース コードを入手します。
* Power BI アカウントが必要になります。

## <a name="sign-in-to-azure"></a>Azure へのサインイン

[Azure portal](https://portal.azure.com) にサインインします。

## <a name="create-an-azure-event-hub"></a>Azure Event Hub を作成します

Stream Analytics で不正な呼び出しデータ ストリームを分析できるようにするには、データが Azure に送信される必要があります。 このチュートリアルでは、[Azure Event Hubs](../event-hubs/event-hubs-about.md) を使用して Azure にデータを送信します。

イベント ハブを作成して呼び出しデータをそのイベント ハブに送信するには、次の手順を使用します。

1. [Azure portal](https://portal.azure.com/) にサインインします。
2. **[リソースの作成]**  >  **[モノのインターネット (IoT)]**  >  **[Event Hubs]** の順に選択します。

   ![Azure イベント ハブをポータルで作成する](media/stream-analytics-real-time-fraud-detection/find-event-hub-resource.png)
3. **[名前空間の作成]** ウィンドウで次の値を入力します。

   |**設定**  |**推奨値** |**説明**  |
   |---------|---------|---------|
   |名前     | asaTutorialEventHub        |  イベント ハブの名前空間を識別する一意の名前。       |
   |サブスクリプション     |   \<Your subscription\>      |   イベント ハブを作成する Azure サブスクリプションを選択します。      |
   |Resource group     |   MyASADemoRG      |  **[新規作成]** を選択し、アカウントの新しいリソース グループ名を入力します。       |
   |場所     |   米国西部 2      |    イベント ハブの名前空間をデプロイできる場所です。     |

4. 残りの設定では既定のオプションを使用し、 **[確認と作成]** を選択します。 次に、 **[作成]** を選択してデプロイを開始します。

   ![イベント ハブの名前空間を Azure portal で作成する](media/stream-analytics-real-time-fraud-detection/create-event-hub-namespace.png)

5. 名前空間のデプロイが完了したら、 **[すべてのリソース]** に移動し、Azure リソースの一覧で *asaTutorialEventHub* を見つけます。 *[asaTutorialEventHub]* を選択して開きます。

6. 次に、 **[+ イベント ハブ]** を選択して、イベント ハブの **名前** を入力します。 **[パーティション数]** を 2 に設定します。  残りの設定では既定のオプションを使用し、 **[作成]** を選択します。 デプロイが成功するまで待ちます。

   ![Azure portal でのイベント ハブの構成](media/stream-analytics-real-time-fraud-detection/create-event-hub-portal.png)

### <a name="grant-access-to-the-event-hub-and-get-a-connection-string"></a>イベント ハブへのアクセスを許可し、接続文字列を取得する

アプリケーションから Azure Event Hubs にデータを送信できるようにするには、アクセスを許可するポリシーがイベント ハブに必要です。 アクセス ポリシーにより、承認情報を含む接続文字列が生成されます。

1. 前の手順で作成したイベント ハブ *MyEventHub* に移動します。 **[設定]** で **[共有アクセス ポリシー]** を選択してから、**[+ 追加]** を選択します。

2. ポリシーに「**MyPolicy**」という名前を付け、**[管理]** が選択されていることを確認します。 **[作成]** を選択します。

   ![イベント ハブの共有アクセス ポリシーの作成](media/stream-analytics-real-time-fraud-detection/create-event-hub-access-policy.png)

3. ポリシーが作成されたら、ポリシー名を選択してポリシーを開きます。 **[接続文字列 - 主キー]** を探します。 接続文字列の横にある **コピー** ボタンを選択します。

   ![共有アクセス ポリシーの接続文字列の保存](media/stream-analytics-real-time-fraud-detection/save-connection-string.png)

4. 接続文字列をテキスト エディターに貼り付けます。 この接続文字列は、次のセクションで必要になります。

   接続文字列は次のようになります。

   `Endpoint=sb://<Your event hub namespace>.servicebus.windows.net/;SharedAccessKeyName=<Your shared access policy name>;SharedAccessKey=<generated key>;EntityPath=<Your event hub name>`

   接続文字列には、**Endpoint**、**SharedAccessKeyName**、**SharedAccessKey**、**EntityPath** という複数のキーと値のペアが含まれ、セミコロンで区切られていることに注目してください。

## <a name="start-the-event-generator-application"></a>イベント ジェネレーター アプリケーションを起動する

TelcoGenerator アプリを起動する前に、以前に作成した Azure Event Hubs にデータを送信するよう構成する必要があります。

1. [TelcoGenerator.zip](https://download.microsoft.com/download/8/B/D/8BD50991-8D54-4F59-AB83-3354B69C8A7E/TelcoGenerator.zip) ファイルの内容を抽出します。
2. 任意のテキスト エディターで `TelcoGenerator\TelcoGenerator\telcodatagen.exe.config` ファイルを開きます。複数の `.config` ファイルがあるので、正しいファイルを開くように注意してください。

3. 構成ファイル内の `<appSettings>` 要素を次の詳細で更新します。

   * *EventHubName* キーの値を、接続文字列の EntityPath の値に設定します。
   * *Microsoft.ServiceBus.ConnectionString* キーの値を、EntityPath の値を除いた接続文字列に設定します。 EntityPath 値の前にあるセミコロンを必ず削除してください。

4. ファイルを保存します。

5. 次に、コマンド ウィンドウを開き、TelcoGenerator アプリケーションを解凍したフォルダーに変更します。 次のコマンドを入力します。

   ```cmd
   .\telcodatagen.exe 1000 0.2 2
   ```

   このコマンドは、次のパラメーターを受け取ります。
   * 1 時間あたりの呼び出しデータ レコードの数。
   * 不正の確率のパーセンテージ。これは、アプリが不正な呼び出しをシミュレートする頻度です。 値 0.2 は、呼び出しレコードの約 20% が不正に見えることを意味します。
   * 継続時間。これはアプリを実行する時間数です。 また、コマンド ラインでプロセスを終了する (**Ctrl + C**) ことで、いつでもアプリを停止できます。

   数秒後に、アプリはイベント ハブに送信する呼び出しレコードを画面に表示し始めます。 通話データには、次のフィールドが含まれています。

   |**レコード**  |**定義**  |
   |---------|---------|
   |CallrecTime    |  通話開始時刻のタイムスタンプ。       |
   |SwitchNum     |  通話の接続に使われた電話交換機。 この例では、交換機は発信国/地域を表す文字列です (US、China、UK、Germany、Australia)。       |
   |CallingNum     |  発信元の電話番号。       |
   |CallingIMSI     |  IMSI (International Mobile Subscriber Identity: 国際携帯機器加入者識別番号)。 発信元の一意識別子。       |
   |CalledNum     |   通話受信者の電話番号。      |
   |CalledIMSI     |  IMSI (International Mobile Subscriber Identity: 国際携帯機器加入者識別番号)。 通話受信者の一意識別子。       |

## <a name="create-a-stream-analytics-job"></a>Stream Analytics のジョブの作成

通話イベントのストリームを準備できたら、イベント ハブからデータを読み取る Stream Analytics ジョブを作成できます。

1. Stream Analytics ジョブを作成するには、[Azure portal](https://portal.azure.com/) に移動します。

2. **[リソースの作成]** を選択し、 **[Stream Analytics ジョブ]** を検索します。 **[Stream Analytics ジョブ]** タイルを選択し、*[作成]** を選択します。

3. **[新しい Stream Analytics ジョブ]** フォームで、次の値を入力します。

   |**設定**  |**推奨値**  |**説明**  |
   |---------|---------|---------|
   |ジョブ名     |  ASATutorial       |   イベント ハブの名前空間を識別する一意の名前。      |
   |サブスクリプション    |  \<Your subscription\>   |   ジョブを作成する Azure サブスクリプションを選択します。       |
   |Resource group   |   MyASADemoRG      |   **[既存のものを使用]** を選択し、アカウントの新しいリソース グループ名を入力します。      |
   |場所   |    米国西部 2     |      ジョブをデプロイできる場所。 最適なパフォーマンスを実現し、リージョン間でのデータ転送の料金がかからないように、ジョブとイベント ハブを同じリージョンに配置することをお勧めします。      |
   |ホスティング環境    | クラウド        |     Stream Analytics ジョブは、クラウドまたはエッジにデプロイすることができます。 クラウドでは Azure Cloud にデプロイすることができ、エッジでは IoT Edge デバイスにデプロイすることができます。    |
   |[ストリーミング ユニット]     |    1       |      ストリーミング ユニットとは、ジョブの実行に必要なコンピューティング リソースのことです。 既定では、この値は 1 に設定されています。 ストリーミング ユニットのスケーリングについては、[ストリーミング ユニットの理解と調整](stream-analytics-streaming-unit-consumption.md)に関する記事を参照してください。      |

4. 残りの設定では既定のオプションを使用し、 **[作成]** を選択して、デプロイが成功するまで待ちます。

   ![Azure Stream Analytics ジョブの作成](media/stream-analytics-real-time-fraud-detection/create-stream-analytics-job.png)

## <a name="configure-job-input"></a>ジョブの入力を構成する

次の手順では、前のセクションで作成したイベント ハブを使用してデータを読み取るためにジョブの入力ソースを定義します。

1. Azure portal から **[すべてのリソース]** ページを開き、*ASATutorial* Stream Analytics ジョブを見つけます。

2. [Stream Analytics ジョブ] の **[ジョブ トポロジ]** セクションで、 **[入力]** を選択します。

3. **+ [ストリーム入力の追加]**、**[イベント ハブ]** の順に選択します。 入力フォームで次の値を入力します。

   |**設定**  |**推奨値**  |**説明**  |
   |---------|---------|---------|
   |入力のエイリアス     |  CallStream       |  入力を識別するためのわかりやすい名前を入力します。 入力のエイリアスに含めることのできる文字は、英数字、ハイフン、アンダースコアのみであり、長さは 3 文字以上 63 文字以下でなければなりません。       |
   |サブスクリプション    |   \<Your subscription\>      |   イベント ハブを作成した Azure サブスクリプションを選択します。 イベント ハブは、Stream Analytics ジョブと同じサブスクリプションにも別のサブスクリプションにも含めることができます。       |
   |イベント ハブの名前空間    |  asaTutorialEventHub       |  前のセクションで作成した、イベント ハブの名前空間を選択します。 現在のサブスクリプションで利用可能なイベント ハブの名前空間がすべて、ドロップダウンに表示されます。       |
   |イベント ハブ名    |   MyEventHub      |  前のセクションで作成したイベント ハブを選択します。 現在のサブスクリプションで利用可能なイベント ハブがすべて、ドロップダウンに表示されます。       |
   |イベント ハブ ポリシー名   |  Mypolicy       |  前のセクションで作成した、イベント ハブの共有アクセス ポリシーを選択します。 現在のサブスクリプションで利用可能なイベント ハブ ポリシーがすべて、ドロップダウンに表示されます。       |

4. 残りの設定では既定のオプションを使用し、**[保存]** を選択します。

   ![Azure Stream Analytics の入力を構成する](media/stream-analytics-real-time-fraud-detection/configure-stream-analytics-input.png)

## <a name="configure-job-output"></a>ジョブの出力を構成する

最後の手順では、変換後のデータを書き込むことができる場所として、ジョブの出力シンクを定義します。 このチュートリアルでは、Power BI を使用してデータの出力と視覚化を行います。

1. Azure portal から **[すべてのリソース]** を開き、*ASATutorial* Stream Analytics ジョブを選択します。

2. [Stream Analytics ジョブ] の **[ジョブ トポロジ]** セクションで、 **[出力]** オプションを選択します。

3. **[+ 追加]**  >  **[Power BI]** の順に選択します。 次に、 **[承認]** を選択し、プロンプトに従って Power BI を認証します。

:::image type="content" source="media/stream-analytics-real-time-fraud-detection/authorize-power-bi.png" alt-text="Power BI の承認ボタン":::

4. 出力フォームに次の詳細を入力して、 **[保存]** を選択します。

   |**設定**  |**推奨値**  |
   |---------|---------|
   |出力エイリアス  |  MyPBIoutput  |
   |グループ ワークスペース| マイ ワークスペース |
   |データセットの名前  |   ASAdataset  |
   |テーブル名 |  ASATable  |
   | 認証モード | ユーザー トークン |

   ![Stream Analytics の出力を構成する](media/stream-analytics-real-time-fraud-detection/configure-stream-analytics-output.png)

   このチュートリアルでは、 *[ユーザー トークン]* 認証モードを使用します。 マネージド ID を使用するには、「[マネージド ID を使用して、Power BI 出力に対して Azure Stream Analytics ジョブを認証する](powerbi-output-managed-identity.md)」を参照してください。

## <a name="create-queries-to-transform-real-time-data"></a>リアルタイム データを変換するクエリを作成する

ここまでで、着信データ ストリームを読み取る Stream Analytics ジョブを設定しました。 次に、リアルタイムでデータを分析するクエリを作成します。 このクエリは、Stream Analytics に固有のいくつかの拡張を含む SQL に似た言語を使います。

チュートリアルのこのセクションでは、複数のクエリを作成してテストし、分析のために入力ストリームを変換するいくつかの方法を学習します。 

ここで作成するクエリは、変換されたデータを画面に表示するだけです。 後のセクションでは、変換されたデータを Power BI に書き込みます。

言語に関する詳細については、 [Azure Stream Analytics クエリ言語リファレンス](/stream-analytics-query/stream-analytics-query-language-reference)を参照してください。

### <a name="test-using-a-pass-through-query"></a>パススルー クエリを使ってテストする

すべてのイベントをアーカイブする場合は、パススルー クエリを使って、イベントのペイロード内のすべてのフィールドを読み取ることができます。

1. Azure portal で Stream Analytics ジョブに移動し、 *[ジョブ トポロジ]* で **[クエリ]** を選択します。 

2. クエリ ウィンドウに次のクエリを入力します。

   ```SQL
   SELECT 
       *
   FROM 
       CallStream
   ```

    >[!NOTE]
    >SQL と同じように、キーワードに大文字と小文字の区別はなく、空白は重要ではありません。

    このクエリで、`CallStream` は入力を作成するときに指定した別名です。 別の別名を使った場合は、代わりにその名前を使います。

3. **[Test query]\(クエリのテスト\)** を選択します。

    Stream Analytics ジョブは、入力からのサンプル データに対してクエリを実行し、ウィンドウの下部に出力を表示します。 結果は、イベント ハブと Streaming Analytics ジョブが正しく構成されていることを示します

    :::image type="content" source="media/stream-analytics-real-time-fraud-detection/sample-output-passthrough.png" alt-text="クエリのテストからのサンプル出力":::

    表示される正確なレコード数は、サンプルでキャプチャされたレコードの数によって異なります。

### <a name="reduce-the-number-of-fields-using-a-column-projection"></a>列のプロジェクションを使ってフィールドの数を減らす

多くの場合、分析に入力ストリームのすべての列は必要ありません。 クエリを使って、パススルー クエリより少ない数のフィールドを返すことができます。

次のクエリを実行し、出力を確認します。

```SQL
SELECT CallRecTime, SwitchNum, CallingIMSI, CallingNumCalledNum 
FROM 
    CallStream
```

### <a name="count-incoming-calls-by-region-tumbling-window-with-aggregation"></a>リージョン別に着信通話の数をカウントする:集計を含むタンブリング ウィンドウ

リージョンごとに受信通話の数をカウントしたいものとします。 ストリーミング データでは、カウントのような集計関数を実行する場合、ストリームをテンポラル ユニットに分割する必要があります。これは、データ ストリーム自体が事実上エンドレスであるためです。 これを行うには、Streaming Analytics の[ウィンドウ関数](stream-analytics-window-functions.md)を使います。 その後、そのウィンドウ内のデータを単位として処理できます。

この変換では、重ならないテンポラル ウィンドウのシーケンスを使います。各ウィンドウの個別のデータ セットをグループ化して集計できます。 この種のウィンドウは、"*タンブリング ウィンドウ*" と呼ばれます。 タンブリング ウィンドウでは、発信元の国/地域を表す `SwitchNum` でグループ化された受信通話の数を取得できます。

1. 次のクエリをクエリ エディターに貼り付けます。

    ```SQL
    SELECT 
        System.Timestamp as WindowEnd, SwitchNum, COUNT(*) as CallCount 
    FROM
        CallStream TIMESTAMP BY CallRecTime 
    GROUP BY TUMBLINGWINDOW(s, 5), SwitchNum
    ```

    このクエリでは、`FROM` 句で `Timestamp By` キーワードを使って、タンブリング ウィンドウの定義に使う入力ストリームのタイムスタンプ フィールドを指定します。 この場合、ウィンドウは各レコードの `CallRecTime` フィールドによってデータをセグメントに分割します。 フィールドを指定しないと、各イベントがイベント ハブに到着した時刻がウィンドウ化操作に使われます。 「[Stream Analytics Query Language Reference](/stream-analytics-query/stream-analytics-query-language-reference)」(Stream Analytics クエリ言語リファレンス) の「Arrival Time Vs Application Time」(到着時刻とアプリケーション時刻) をご覧ください。 

    プロジェクションに含まれる `System.Timestamp` は、各ウィンドウの終わりのタイムスタンプを返します。 

    タンブリング ウィンドウを使うことを指定するには、`GROUP BY` 句で [TUMBLINGWINDOW](/stream-analytics-query/tumbling-window-azure-stream-analytics) 関数を使います。 この関数では、時間単位 (1 マイクロ秒から 1 日まで) とウィンドウ サイズ (単位数) を指定します。 この例では、タンブリング ウィンドウは 5 秒間隔で構成されるので、5 秒間の国/地域別の通話数が表示されます。

2. **[Test query]\(クエリのテスト\)** を選択します。 結果では、**WindowEnd** のタイムスタンプが 5 秒刻みになっていることに注目してください。

### <a name="detect-sim-fraud-using-a-self-join"></a>自己結合を使って SIM 不正を検出する

この例では、不正な使用を、5 秒以内に別の場所で同じユーザーから発信された呼び出しと考えます。 たとえば、合法的に同じユーザーが米国とオーストラリアで同時に呼び出しを行うことはできません。

このようなケースをチェックするには、ストリーミング データの自己結合を使って、`CallRecTime` の値に基づいてストリームをそれ自体に結合します。 その後、`CallingIMSI` の値 (発信番号) が同じなのに `SwitchNum` の値 (発信国/地域) が同じではない呼び出しレコードを探すことができます。

ストリーミング データで結合を使うときは、一致する行と見なす最大時間差を結合で制限する必要があります 前に説明したように、ストリーミング データは事実上エンドレスです。 リレーションシップの時間限界は、結合の `ON` 句で `DATEDIFF` 関数を使って指定します。 この例では、結合は 5 秒間隔の通話データに基づきます。

1. 次のクエリをクエリ エディターに貼り付けます。

    ```SQL
    SELECT System.Timestamp AS WindowEnd, COUNT(*) AS FraudulentCalls
    INTO "MyPBIoutput"
    FROM "CallStream" CS1 TIMESTAMP BY CallRecTime
    JOIN "CallStream" CS2 TIMESTAMP BY CallRecTime
    ON CS1.CallingIMSI = CS2.CallingIMSI
    AND DATEDIFF(ss, CS1, CS2) BETWEEN 1 AND 5
    WHERE CS1.SwitchNum != CS2.SwitchNum
    GROUP BY TumblingWindow(Duration(second, 1))
    ```

    このクエリは、`DATEDIFF` 関数が結合に含まれることを除けば、他の SQL 結合と似ています。 `DATEDIFF` のこのバージョンは Streaming Analytics に固有であり、`ON...BETWEEN` 句で使う必要があります。 パラメーターは、時間単位 (この例では秒) と、結合の 2 つのソースの別名です これは、SQL の標準的な `DATEDIFF` 関数と異なります。

    `WHERE` 句には、発信元の交換機が同じではないという、通話を不正と見なす条件が含まれます。

2. **[Test query]\(クエリのテスト\)** を選択します。 出力を確認し、 **[クエリの保存]** を選択します。

## <a name="start-the-job-and-visualize-output"></a>ジョブを開始して出力を視覚化する

1. ジョブを開始するには、ジョブの **[概要]** に移動し、 **[開始]** を選択します。

2. ジョブ出力の開始時刻に **[現在]** を選択し、 **[開始]** を選択します。 通知バーでジョブの状態を確認できます。

3. ジョブが成功したら [Power BI](https://powerbi.com/) に移動し、職場または学校アカウントを使用してサインインします。 Stream Analytics ジョブ クエリによる結果の出力が進行中の場合、作成した *ASAdataset* データセットは **[データセット]** タブにあります。

4. Power BI ワークスペースで **[+ 作成]** を選択し、*Fraudulent Calls* という名前の新しいダッシュボードを作成します。

5. ウィンドウの上部にある **[編集]** と **[タイルの追加]** を選択します。 次に、 **[カスタム ストリーミング データ]** と **[次に]** を選択します。 **[データセット]** の **ASAdataset** を選択します。 **[視覚化タイプ]** ドロップダウンで **[カード]** を選択し、**[フィールド]** に **不正な呼び出し** を追加します。 **[次へ]** を選択してタイルに名前を入力し、 **[適用]** を選択してタイルを作成します。

   ![Power BI ダッシュボードのタイルを作成する](media/stream-analytics-real-time-fraud-detection/create-power-bi-dashboard-tiles.png)

6. 次のオプションを使用して、もう一度手順 5. を実行します。
   * [視覚化タイプ] では、[折れ線グラフ] を選択します。
   * 軸を追加し、 **[windowend]** を選びます。
   * 値を追加し、 **[fraudulentcalls]** を選びます。
   * **[表示する時間枠]** で、過去 10 分間を選びます。

7. 2 つのタイルが追加されると、ダッシュボードは下の例のようになります。 イベント ハブの送信側アプリケーションと Streaming Analytics アプリケーションが実行されている場合に新しいデータが到着すると Power BI ダッシュボードが定期的に更新されることに注目してください。

   ![Power BI ダッシュボードで結果を確認する](media/stream-analytics-real-time-fraud-detection/power-bi-results-dashboard.png)

## <a name="embedding-your-power-bi-dashboard-in-a-web-application"></a>Web アプリケーションに Power BI ダッシュボードを埋め込む

チュートリアルのこの部分では、PowerBI チームが作成したサンプルの [ASP.NET](https://asp.net/) Web アプリケーションを使用して、ダッシュボードを埋め込みます。 ダッシュボードの埋め込みの詳細については、記事「[Power BI で埋め込み](/power-bi/developer/embedding)」を参照してください。

アプリケーションを設定するには、[PowerBI-Developer-Samples](https://github.com/Microsoft/PowerBI-Developer-Samples) GitHub リポジトリに移動し、**User Owns Data** セクションの指示に従います (**integrate-web-app** サブセクションのリダイレクト URL およびホーム ページ URL を使用します)。 ダッシュボードの例を使用しているため、[GitHub リポジトリ](https://github.com/microsoft/PowerBI-Developer-Samples/tree/master/.NET%20Framework/Embed%20for%20your%20organization/)にある **integrate-web-app** サンプル コードを使用します。
ブラウザーでアプリケーションが実行されるようになったら、次の手順に従って、先ほど作成したダッシュボードを Web ページに埋め込みます。

1. **[Power BI にサインイン]** を選択します。これにより、Power BI アカウントのダッシュボードへのアクセスがアプリケーションに許可されます。

2. **[ダッシュボードの取得]** ボタンを選択します。これにより、アカウントのダッシュボードがテーブルに表示されます。 前に作成したダッシュボードの名前 **powerbi-embedded-dashboar** を見つけて、対応する **EmbedUrl** をコピーします。

3. 最後に、その **EmbedUrl** を対応するテキスト フィールドに貼り付け、**[Embed Dashboard]\(ダッシュボードの埋め込み\)** を選択します。 これで、Web アプリケーション内に埋め込まれた同じダッシュボードを表示できるようになりました。

## <a name="next-steps"></a>次のステップ

このチュートリアルでは、簡単な Stream Analytics ジョブを作成し、着信データを分析して、結果を Power BI ダッシュボードに表示しました。 Stream Analytics ジョブの詳細については、次のチュートリアルに進んでください。

> [!div class="nextstepaction"]
> [Stream Analytics ジョブ内から Azure Functions を実行する](stream-analytics-with-azure-functions.md)
