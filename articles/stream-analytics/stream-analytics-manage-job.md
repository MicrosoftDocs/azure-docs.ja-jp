---
title: 'チュートリアル: Azure portal を使用した Stream Analytics ジョブの作成および管理'
description: このチュートリアルでは、通話ストリームにおける不正な呼び出しを Azure Stream Analytics を使用して分析する方法を、デモでまるごとお見せします。
services: stream-analytics
author: mamccrea
ms.author: mamccrea
ms.service: stream-analytics
ms.workload: data-services
ms.topic: tutorial
ms.custom: mvc
ms.date: 06/03/2019
ms.openlocfilehash: d09ed0585250d078f728aa4e7272cca147a40c38
ms.sourcegitcommit: 6a42dd4b746f3e6de69f7ad0107cc7ad654e39ae
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/07/2019
ms.locfileid: "67612376"
---
# <a name="analyze-phone-call-data-with-stream-analytics-and-visualize-results-in-power-bi-dashboard"></a>Stream Analytics で通話データを分析し、Power BI ダッシュボードで結果を視覚化する

このチュートリアルでは、Azure Stream Analytics を使用して通話データを分析する方法について説明します。 クライアント アプリケーションによって生成される通話データには、Stream Analytics ジョブによってフィルター処理される不正な呼び出しが含まれています。

このチュートリアルでは、以下の内容を学習します。

> [!div class="checklist"]
> * サンプル通話データを生成して Azure Event Hubs に送信する
> * Stream Analytics のジョブの作成
> * ジョブの入力と出力を構成する
> * 不正な呼び出しをフィルター処理するようクエリを定義する
> * ジョブをテストして開始する
> * Power BI で結果を視覚化する

## <a name="prerequisites"></a>前提条件

開始する前に、次の操作を行います。

* Azure サブスクリプションをお持ちでない場合は、[無料アカウント](https://azure.microsoft.com/free/)を作成してください。
* [Azure Portal](https://portal.azure.com/) にサインインします。
* Microsoft ダウンロード センターから通話イベント ジェネレーター アプリ [TelcoGenerator.zip](https://download.microsoft.com/download/8/B/D/8BD50991-8D54-4F59-AB83-3354B69C8A7E/TelcoGenerator.zip) をダウンロードします。または、[GitHub](https://aka.ms/azure-stream-analytics-telcogenerator) からソース コードを入手します。
* Power BI アカウントが必要になります。

## <a name="create-an-azure-event-hub"></a>Azure Event Hub を作成します

Stream Analytics で不正な呼び出しデータ ストリームを分析できるようにするには、データが Azure に送信される必要があります。 このチュートリアルでは、[Azure Event Hubs](https://docs.microsoft.com/azure/event-hubs/event-hubs-what-is-event-hubs) を使用して Azure にデータを送信します。

イベント ハブを作成して呼び出しデータをそのイベント ハブに送信するには、次の手順を使用します。

1. [Azure Portal](https://portal.azure.com/) にサインインします。
2. **[リソースの作成]**  >  **[モノのインターネット (IoT)]**  >  **[Event Hubs]** の順に選択します。

   ![Azure イベント ハブをポータルで作成する](media/stream-analytics-manage-job/find-event-hub-resource.png)
3. **[名前空間の作成]** ウィンドウで次の値を入力します。

   |**設定**  |**推奨値** |**説明**  |
   |---------|---------|---------|
   |Name     | myEventHubsNS        |  イベント ハブの名前空間を識別する一意の名前。       |
   |サブスクリプション     |   \<該当するサブスクリプション\>      |   イベント ハブを作成する Azure サブスクリプションを選択します。      |
   |リソース グループ     |   MyASADemoRG      |  **[新規作成]** を選択し、アカウントの新しいリソース グループ名を入力します。       |
   |Location     |   米国西部 2      |    イベント ハブの名前空間をデプロイできる場所です。     |

4. 残りの設定では既定のオプションを使用し、 **[作成]** を選択します。

   ![イベント ハブの名前空間を Azure portal で作成する](media/stream-analytics-manage-job/create-event-hub-namespace.png)

5. 名前空間のデプロイが完了したら、 **[すべてのリソース]** に移動し、Azure リソースの一覧で *myEventHubsNS* を見つけます。 *myEventHubsNS* を選択して開きます。
6. 次に **+ [イベント ハブ]** を選択して、 **[名前]** に「*MyEventHub*」と入力するか、または別の任意の名前を入力します。 残りの設定では既定のオプションを使用し、 **[作成]** を選択します。 デプロイが成功するまで待ちます。

   ![Azure portal でのイベント ハブの構成](media/stream-analytics-manage-job/create-event-hub-portal.png)

### <a name="grant-access-to-the-event-hub-and-get-a-connection-string"></a>イベント ハブへのアクセスを許可し、接続文字列を取得する

アプリケーションから Azure Event Hubs にデータを送信できるようにするには、適切なアクセスを許可するポリシーがイベント ハブに必要です。 アクセス ポリシーにより、承認情報を含む接続文字列が生成されます。

1. 前の手順で作成したイベント ハブ MyEventHub* に移動します。 **[設定]** で **[共有アクセス ポリシー]** を選択してから、 **[+ 追加]** を選択します。

2. ポリシーに「**MyPolicy**」という名前を付け、 **[管理]** が選択されていることを確認します。 **[作成]** を選択します。

   ![イベント ハブの共有アクセス ポリシーの作成](media/stream-analytics-manage-job/create-event-hub-access-policy.png)

3. ポリシーが作成されたら、そのポリシーを選択して開き、 **[接続文字列 – 主キー]** を見つけます。 接続文字列の横にある青い**コピー** ボタンを選択します。

   ![共有アクセス ポリシーの接続文字列の保存](media/stream-analytics-manage-job/save-connection-string.png)

4. 接続文字列をテキスト エディターに貼り付けます。 この接続文字列は、次のセクションで必要になります。

   接続文字列は次のようになります。

   `Endpoint=sb://<Your event hub namespace>.servicebus.windows.net/;SharedAccessKeyName=<Your shared access policy name>;SharedAccessKey=<generated key>;EntityPath=<Your event hub name>`

   接続文字列には、セミコロンで区切られた複数のキーと値のペアが含まれています (**Endpoint**、**SharedAccessKeyName**、**SharedAccessKey**、**EntityPath**)。

## <a name="start-the-event-generator-application"></a>イベント ジェネレーター アプリケーションを起動する

TelcoGenerator アプリを起動する前に、以前に作成した Azure Event Hubs にデータを送信するよう構成する必要があります。

1. [TelcoGenerator.zip](https://download.microsoft.com/download/8/B/D/8BD50991-8D54-4F59-AB83-3354B69C8A7E/TelcoGenerator.zip) ファイルの内容を抽出します。
2. 任意のテキスト エディターで `TelcoGenerator\TelcoGenerator\telcodatagen.exe.config` ファイルを開きます (複数の .config ファイルがあるので、正しいファイルを開くように注意してください)。

3. 構成ファイル内の `<appSettings>` 要素を次の詳細で更新します。

   * *EventHubName* キーの値を、接続文字列の EntityPath の値に設定します。
   * *Microsoft.ServiceBus.ConnectionString* キーの値を、EntityPath の値を除いた接続文字列に設定します (前にあるセミコロンを削除することを忘れないでください)。

4. ファイルを保存します。
5. 次に、コマンド ウィンドウを開き、TelcoGenerator アプリケーションを解凍したフォルダーに変更します。 次のコマンドを入力します。

   ```cmd
   telcodatagen.exe 1000 0.2 2
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

2. **[リソースの作成]**  >  **[モノのインターネット (IoT)]**  >  **[Stream Analytics ジョブ]** の順に選択します。

3. **[新しい Stream Analytics ジョブ]** ウィンドウで、次の値を入力します。

   |**設定**  |**推奨値**  |**説明**  |
   |---------|---------|---------|
   |ジョブ名     |  ASATutorial       |   イベント ハブの名前空間を識別する一意の名前。      |
   |サブスクリプション    |  \<該当するサブスクリプション\>   |   ジョブを作成する Azure サブスクリプションを選択します。       |
   |リソース グループ   |   MyASADemoRG      |   **[既存のものを使用]** を選択し、アカウントの新しいリソース グループ名を入力します。      |
   |Location   |    米国西部 2     |      ジョブをデプロイできる場所。 最適なパフォーマンスを実現し、リージョン間でのデータ転送の料金がかからないように、ジョブとイベント ハブを同じリージョンに配置することをお勧めします。      |
   |ホスティング環境    | クラウド        |     Stream Analytics ジョブは、クラウドまたはエッジにデプロイすることができます。 クラウドでは Azure Cloud にデプロイすることができ、エッジでは IoT Edge デバイスにデプロイすることができます。    |
   |[ストリーミング ユニット]     |    1       |      ストリーミング ユニットとは、ジョブの実行に必要なコンピューティング リソースのことです。 既定では、この値は 1 に設定されています。 ストリーミング ユニットのスケーリングについては、[ストリーミング ユニットの理解と調整](stream-analytics-streaming-unit-consumption.md)に関する記事を参照してください。      |

4. 残りの設定では既定のオプションを使用し、 **[作成]** を選択して、デプロイが成功するまで待ちます。

   ![Azure Stream Analytics ジョブの作成](media/stream-analytics-manage-job/create-stream-analytics-job.png)

## <a name="configure-job-input"></a>ジョブの入力を構成する

次の手順では、前のセクションで作成したイベント ハブを使用してデータを読み取るためにジョブの入力ソースを定義します。

1. Azure portal から **[すべてのリソース]** ウィンドウを開き、*ASATutorial* Stream Analytics ジョブを見つけます。

2. [Stream Analytics ジョブ] ウィンドウの **[ジョブ トポロジ]** セクションで、 **[入力]** オプションを選択します。

3. **+ [ストリーム入力の追加]** 、 **[イベント ハブ]** の順に選択します。 ウィンドウで次の値を入力します。

   |**設定**  |**推奨値**  |**説明**  |
   |---------|---------|---------|
   |入力のエイリアス     |  CallStream       |  入力を識別するためのわかりやすい名前を入力します。 入力のエイリアスに含めることのできる文字は、英数字、ハイフン、アンダースコアのみであり、長さは 3 文字以上 63 文字以下でなければなりません。       |
   |サブスクリプション    |   \<該当するサブスクリプション\>      |   イベント ハブを作成した Azure サブスクリプションを選択します。 イベント ハブは、Stream Analytics ジョブと同じサブスクリプションにも別のサブスクリプションにも含めることができます。       |
   |イベント ハブの名前空間    |  myEventHubsNS       |  前のセクションで作成した、イベント ハブの名前空間を選択します。 現在のサブスクリプションで利用可能なイベント ハブの名前空間がすべて、ドロップダウンに表示されます。       |
   |イベント ハブ名    |   MyEventHub      |  前のセクションで作成したイベント ハブを選択します。 現在のサブスクリプションで利用可能なイベント ハブがすべて、ドロップダウンに表示されます。       |
   |イベント ハブ ポリシー名   |  Mypolicy       |  前のセクションで作成した、イベント ハブの共有アクセス ポリシーを選択します。 現在のサブスクリプションで利用可能なイベント ハブ ポリシーがすべて、ドロップダウンに表示されます。       |

4. 残りの設定では既定のオプションを使用し、 **[保存]** を選択します。

   ![Azure Stream Analytics の入力を構成する](media/stream-analytics-manage-job/configure-stream-analytics-input.png)

## <a name="configure-job-output"></a>ジョブの出力を構成する

最後の手順では、変換後のデータを書き込むことができる場所として、ジョブの出力シンクを定義します。 このチュートリアルでは、Power BI を使用してデータの出力と視覚化を行います。

1. Azure portal から **[すべてのリソース]** ウィンドウを開き、*ASATutorial* Stream Analytics ジョブを開きます。

2. [Stream Analytics ジョブ] ウィンドウの **[ジョブ トポロジ]** セクションで、 **[出力]** オプションを選択します。

3. **[+ 追加]**  >  **[Power BI]** の順に選択します。 次に、フォームに次の詳細を入力して、 **[承認する]** を選択します。

   |**設定**  |**推奨値**  |
   |---------|---------|
   |出力エイリアス  |  MyPBIoutput  |
   |データセットの名前  |   ASAdataset  |
   |テーブル名 |  ASATable  |

   ![Stream Analytics の出力を構成する](media/stream-analytics-manage-job/configure-stream-analytics-output.png)

4. **[承認する]** を選択すると、ポップアップ ウィンドウが開き、Power BI アカウントに対する認証のための資格情報を入力するよう求められます。 認可が成功したら、設定を**保存**します。

## <a name="define-a-query-to-analyze-input-data"></a>入力データを分析するようクエリを定義する

次の手順では、リアルタイムでデータを分析する変換を作成します。 変換クエリの定義には、[Stream Analytics クエリ言語](https://docs.microsoft.com/stream-analytics-query/stream-analytics-query-language-reference)を使用します。 このチュートリアルで使用されるクエリでは、電話データから不正な呼び出しを検出します。

この例では、同じユーザーによって、5 秒以内に別々の場所から不正な呼び出しが行われます。 たとえば、合法的に同じユーザーが米国とオーストラリアで同時に呼び出しを行うことはできません。 Stream Analytics ジョブの変換クエリを定義するには:

1. Azure portal から **[すべてのリソース]** ウィンドウを開き、前に作成した **ASATutorial** Stream Analytics ジョブに移動します。

2. [Stream Analytics ジョブ] ウィンドウの **[ジョブ トポロジ]** セクションで、 **[クエリ]** オプションを選択します。 クエリ ウィンドウでは、そのジョブ用に構成されている入力と出力が一覧表示されており、入力ストリームを変換するためのクエリを作成できます。

3. エディターの既存のクエリを次のクエリに置き換えます。これで、5 秒間隔の呼び出しデータに対して自己結合を実行します。

   ```sql
   SELECT System.Timestamp AS WindowEnd, COUNT(*) AS FraudulentCalls
   INTO "MyPBIoutput"
   FROM "CallStream" CS1 TIMESTAMP BY CallRecTime
   JOIN "CallStream" CS2 TIMESTAMP BY CallRecTime
   ON CS1.CallingIMSI = CS2.CallingIMSI
   AND DATEDIFF(ss, CS1, CS2) BETWEEN 1 AND 5
   WHERE CS1.SwitchNum != CS2.SwitchNum
   GROUP BY TumblingWindow(Duration(second, 1))
   ```

   不正な呼び出しを確認するために、`CallRecTime` の値に基づいてストリーミング データを自己結合できます。 その後、`CallingIMSI` の値 (発信番号) は同じなのに `SwitchNum` の値 (発信国/地域) が異なる通話レコードを探すことができます。 ストリーミング データで JOIN 操作を使用する際は、一致する行と見なす最大時間差を結合で制限する必要があります。 ストリーミング データは無限であるため、リレーションシップの時間限界は、結合の **ON** 句内で [DATEDIFF](https://docs.microsoft.com/stream-analytics-query/datediff-azure-stream-analytics) 関数を使用して指定されます。

   このクエリは、**DATEDIFF** 関数を除けば、通常の SQL 結合と似ています。 このクエリで使用されている **DATEDIFF** 関数は、Stream Analytics に固有であり、`ON...BETWEEN` 句内で使用する必要があります。

4. クエリを**保存**します。

   ![Stream Analytics クエリをポータルで定義する](media/stream-analytics-manage-job/define-stream-analytics-query.png)

## <a name="test-your-query"></a>クエリをテストする

サンプル データを使用して、クエリ エディターでクエリをテストできます。 クエリをテストするには、次の手順を実行します。

1. TelcoGenerator アプリが実行されていて、通話レコードを生成していることを確認します。

2. **[クエリ]** ウィンドウで、*CallStream* 入力の横にある点を選択し、 **[入力からのサンプル データ]** を選択します。

3. **[分]** を 3 に設定し、 **[OK]** を選択します。 3 分間分のデータが入力ストリームからサンプリングされ、サンプル データの準備ができると通知されます。 通知バーからサンプリングの状態を確認できます。

   サンプル データは一時的に保存され、クエリ ウィンドウを開いている間使用できます。 クエリ ウィンドウを閉じると、サンプル データは破棄されます。テストしたい場合は、サンプル データの新しいセットを作成する必要があります。 または、[GitHub](https://github.com/Azure/azure-stream-analytics/blob/master/Sample%20Data/telco.json) からサンプル データ JSON ファイルを使用して、その JSON ファイルを *CallStream* 入力のサンプル データとして使うこともできます。

   ![Stream Analytics の入力データをサンプリングするところ](media/stream-analytics-manage-job/sample-input-data-asa.png)

4. **[テスト]** を選択してクエリをテストします。 次のような結果が表示されます。

   ![Stream Analytics クエリ テストからの出力](media/stream-analytics-manage-job/sample-test-output-restuls.png)

## <a name="start-the-job-and-visualize-output"></a>ジョブを開始して出力を視覚化する

1. ジョブを開始するには、ジョブの **[概要]** ウィンドウに移動し、 **[開始]** を選択します。

2. ジョブ出力の開始時刻に **[現在]** を選択し、 **[開始]** を選択します。 通知バーでジョブの状態を確認できます。

3. ジョブが成功したら [Power BI](https://powerbi.com/) に移動し、職場または学校アカウントを使用してサインインします。 Stream Analytics ジョブ クエリによる結果の出力が進行中の場合、作成した *ASAdataset* データセットは **[データセット]** タブにあります。

4. Power BI ワークスペースで **[+ 作成]** を選択し、*Fraudulent Calls* という名前の新しいダッシュボードを作成します。

5. ウィンドウの上部にある **[タイルの追加]** を選択します。 次に、 **[カスタム ストリーミング データ]** と **[次に]** を選択します。 **[データセット]** の **ASAdataset** を選択します。 **[視覚化タイプ]** ドロップダウンで **[カード]** を選択し、 **[フィールド]** に**不正な呼び出し**を追加します。 **[次へ]** を選択してタイルに名前を入力し、 **[適用]** を選択してタイルを作成します。

   ![Power BI ダッシュボードのタイルを作成する](media/stream-analytics-manage-job/create-power-bi-dashboard-tiles.png)

6. 次のオプションを使用して、もう一度手順 5. を実行します。
   * [視覚化タイプ] では、[折れ線グラフ] を選択します。
   * 軸を追加し、 **[windowend]** を選びます。
   * 値を追加し、 **[fraudulentcalls]** を選びます。
   * **[表示する時間枠]** で、過去 10 分間を選びます。

7. 2 つのタイルが追加されると、ダッシュボードは下の例のようになります。 イベント ハブの送信側アプリケーションと Streaming Analytics アプリケーションが実行されている場合に新しいデータが到着すると Power BI ダッシュボードが定期的に更新されることに注目してください。

   ![Power BI ダッシュボードで結果を確認する](media/stream-analytics-manage-job/power-bi-results-dashboard.png)

## <a name="embedding-your-power-bi-dashboard-in-a-web-application"></a>Web アプリケーションに Power BI ダッシュボードを埋め込む

チュートリアルのこの部分では、PowerBI チームが作成したサンプルの [ASP.NET](https://asp.net/) Web アプリケーションを使用して、ダッシュボードを埋め込みます。 ダッシュボードの埋め込みの詳細については、記事「[Power BI で埋め込み](https://docs.microsoft.com/power-bi/developer/embedding)」を参照してください。

アプリケーションを設定するには、[PowerBI-Developer-Samples](https://github.com/Microsoft/PowerBI-Developer-Samples) GitHub リポジトリに移動し、**User Owns Data** セクションの指示に従います (**integrate-dashboard-web-app** サブセクションのリダイレクト URL およびホーム ページ URL を使用します)。 ダッシュボードの例を使用しているため、[GitHub リポジトリ](https://github.com/Microsoft/PowerBI-Developer-Samples/tree/master/User%20Owns%20Data/integrate-dashboard-web-app)にある **integrate-dashboard-web-app** サンプル コードを使用します。
ブラウザーでアプリケーションが実行されるようになったら、次の手順に従って、先ほど作成したダッシュボードを Web ページに埋め込みます。

1. **[Power BI にサインイン]** を選択します。これにより、Power BI アカウントのダッシュボードへのアクセスがアプリケーションに許可されます。

2. **[ダッシュボードの取得]** ボタンを選択します。これにより、アカウントのダッシュボードがテーブルに表示されます。 前に作成したダッシュボードの名前 **powerbi-embedded-dashboar** を見つけて、対応する **EmbedUrl** をコピーします。

3. 最後に、その **EmbedUrl** を対応するテキスト フィールドに貼り付け、 **[Embed Dashboard]\(ダッシュボードの埋め込み\)** を選択します。 これで、Web アプリケーション内に埋め込まれた同じダッシュボードを表示できるようになりました。

## <a name="next-steps"></a>次の手順

このチュートリアルでは、簡単な Stream Analytics ジョブを作成し、着信データを分析して、結果を Power BI ダッシュボードに表示しました。 Stream Analytics ジョブの詳細については、次のチュートリアルに進んでください。

> [!div class="nextstepaction"]
> [Stream Analytics ジョブ内から Azure Functions を実行する](stream-analytics-with-azure-functions.md)
