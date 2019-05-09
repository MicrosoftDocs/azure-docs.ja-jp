---
title: Application Insights (Node.js)
titleSuffix: Azure Cognitive Services
description: Node.js を使用して LUIS アプリケーションおよび Application Insights と統合されるボットを構築する
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: article
ms.date: 01/23/2019
ms.author: diberry
ms.openlocfilehash: 1c44d2e41d37a9236ee6d6936c349acf5ca5e44c
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2019
ms.locfileid: "58098774"
---
# <a name="add-luis-results-to-application-insights-and-azure-functions"></a>LUIS の結果を Application Insights と Azure Functions に追加する
このチュートリアルでは、LUIS 要求と応答の情報を [Application Insights](https://azure.microsoft.com/services/application-insights/) テレメトリ データ ストレージに追加します。 データを用意したら、Kusto 言語または PowerBi を使用して、意図および発話のエンティティについてリアルタイムで分析、集計、およびレポートすることができます。 この分析は、LUIS アプリの意図およびエンティティを追加または編集する必要があるかどうかの判断に役立ちます。

ボットは、Bot Framework 3.x と Azure Web アプリ ボットで構築します。

このチュートリアルでは、以下の内容を学習します。

> [!div class="checklist"]
> * Application Insights ライブラリを Web アプリ ボットに追加する
> * LUIS クエリの結果をキャプチャし、Application Insights に送信する
> * Application Insights で最上位の意図、スコア、および発話のクエリを実行する

## <a name="prerequisites"></a>前提条件

* **[前のチュートリアル](luis-nodejs-tutorial-build-bot-framework-sample.md)** の LUIS Web アプリ ボット。Application Insights が有効になっている必要があります。 

> [!Tip]
> サブスクリプションがない場合は、[無料アカウント](https://azure.microsoft.com/free/)に登録できます。

このチュートリアルのコードはすべて、[Azure-Samples GitHub リポジトリ](https://github.com/Azure-Samples/cognitive-services-language-understanding/tree/master/documentation-samples/tutorial-web-app-bot-application-insights/nodejs)から入手できます。このチュートリアルに関連付けられている各行に、`//APPINSIGHT:` というコメントが付いています。 

## <a name="web-app-bot-with-luis"></a>LUIS を使用する Web アプリ ボット
このチュートリアルでは、次のようなコードがあるか、[他のチュートリアル](luis-nodejs-tutorial-build-bot-framework-sample.md)を完了していることを前提としています。 

   [!code-javascript[Web app bot with LUIS](~/samples-luis/documentation-samples/tutorial-web-app-bot/nodejs/app.js "Web app bot with LUIS")]

## <a name="add-application-insights-library-to-web-app-bot"></a>Application Insights ライブラリを Web アプリ ボットに追加する
現在、この Web アプリ ボットで使用されている Application Insights サービスは、ボット用の一般的な状態テレメトリを収集します。 意図とエンティティを確認および修正するために必要な LUIS の要求と応答の情報は収集しません。 

LUIS 要求と応答をキャプチャするために、Web アプリ ボットは、 **[Application Insights](https://www.npmjs.com/package/applicationinsights)** NPM パッケージがインストールされ、**app.js**ファイルで構成されていることを必要とします。 意図ダイアログ ハンドラーは、LUIS 要求と応答の情報を Application Insights に送信する必要があります。 

1. Azure ポータルの Web アプリ ボット サービスで、**[Bot Management]\(ボットの管理\)** セクションの **[ビルド]** を選択します。 

    ![Azure portal の Web アプリ ボット サービスで、[Bot Management]\(ボットの管理\) セクションの [ビルド] を選択します。](./media/luis-tutorial-appinsights/build.png)

2. App Service エディターで新しいブラウザー タブを開きます。 上部のバーでアプリの名前を選択し、**[Open Kudu Console]\(Kudu コンソールを開く\)** を選択します。 

    ![上部のバーでアプリの名前を選択し、[Open Kudu Console]\(Kudu コンソールを開く\) を選択します。](./media/luis-tutorial-appinsights/kudu-console.png)

3. コンソールで、次のコマンドを入力して、Application Insights と Underscore パッケージをインストールします。

    ```console
    cd site\wwwroot && npm install applicationinsights && npm install underscore
    ```

    ![npm コマンドを使用して Application Insights と Underscore パッケージをインストールする](./media/luis-tutorial-appinsights/npm-install.png)

    パッケージがインストールされるまで待機します。

    ```console
    luisbot@1.0.0 D:\home\site\wwwroot
    `-- applicationinsights@1.0.1 
      +-- diagnostic-channel@0.2.0 
      +-- diagnostic-channel-publishers@0.2.1 
      `-- zone.js@0.7.6 
    
    npm WARN luisbot@1.0.0 No repository field.
    luisbot@1.0.0 D:\home\site\wwwroot
    +-- botbuilder-azure@3.0.4
    | `-- azure-storage@1.4.0
    |   `-- underscore@1.4.4 
    `-- underscore@1.8.3 
    ```

    Kudu コンソールのブラウザー タブの作業が終了しました。

## <a name="capture-and-send-luis-query-results-to-application-insights"></a>LUIS クエリの結果をキャプチャし、Application Insights に送信する
1. App Service Editor のブラウザー タブで、**app.js** ファイルを開きます。

2. NPM ライブラリを既存の `requires` 行の下に追加します。

   [!code-javascript[Add NPM packages to app.js](~/samples-luis/documentation-samples/tutorial-web-app-bot-application-insights/nodejs/app.js?range=12-16 "Add NPM packages to app.js")]

3. Application Insights オブジェクトを作成し、Web アプリ ボット アプリケーション設定 **BotDevInsightsKey** を使用します。 

   [!code-javascript[Create the Application Insights object](~/samples-luis/documentation-samples/tutorial-web-app-bot-application-insights/nodejs/app.js?range=68-80 "Create the Application Insights object")]

4. **appInsightsLog** 関数を追加します。

   [!code-javascript[Add the appInsightsLog function](~/samples-luis/documentation-samples/tutorial-web-app-bot-application-insights/nodejs/app.js?range=82-109 "Add the appInsightsLog function")]

    関数の最後の行で、データを Application Insights に追加します。 イベントの名前は **LUIS-results** です。この Web アプリ ボットによって収集されたその他テレメトリ データとは別の一意の名前です。 

5. **appInsightsLog** 関数を使用します。 すべての意図ダイアログに追加します。

   [!code-javascript[Use the appInsightsLog function](~/samples-luis/documentation-samples/tutorial-web-app-bot-application-insights/nodejs/app.js?range=117-118 "Use the appInsightsLog function")]

6. Web アプリ ボットをテストするには、**Test in Web Chat** 機能を使用します。 すべての作業は、ボットの応答ではなく Application Insights で実行されるため、違いは表示されません。

## <a name="view-luis-entries-in-application-insights"></a>Application Insights での LUIS エントリの表示
Application Insights を開いて、LUIS エントリを表示します。 

1. ポータルで、**[すべてのリソース]** を選択し、Web アプリ ボット名でフィルター処理します。 **Application Insights** 型のリソースをクリックします。 Application Insights のアイコンは電球です。 

    ![[Azure Portal で Application Insights を探す](./media/luis-tutorial-appinsights/search-for-app-insights.png)

2. リソースが開いたら、一番右のパネルにある虫眼鏡の**検索**アイコンをクリックします。 右側に新しいパネルが表示されます。 見つかったテレメトリ データの量に応じて、パネルの表示に 1 秒かかる場合があります。 `LUIS-results` を検索し、キーボードで Enter キーを押します。 リストが、このチュートリアルを使用して追加された LUIS クエリ結果だけに絞り込まれます。

    ![依存関係のフィルター処理](./media/luis-tutorial-appinsights/app-insights-filter.png)

3. 最上位のエントリを選択します。 右端の新しいウィンドウに、LUIS クエリのカスタム データを含む詳細なデータが表示されます。 データには、最上位の意図とそのスコアが含まれています。

    ![依存関係の詳細](./media/luis-tutorial-appinsights/app-insights-detail.png)

    終了したら、右端の上にある **X** を選択して、依存項目の一覧に戻ります。 


> [!Tip]
> 依存関係の一覧を保存し、後でその一覧に戻る場合は、**[詳細]**、**[お気に入りの保存]** の順にクリックします。

## <a name="query-application-insights-for-intent-score-and-utterance"></a>Application Insights で意図、スコア、および発話のクエリを実行する
Application Insights を使用すると、[Kusto](https://docs.microsoft.com/azure/application-insights/app-insights-analytics#query-data-in-analytics) 言語を使用してデータにクエリを実行したり、[PowerBI](https://powerbi.microsoft.com) にデータをエクスポートしたりできます。 

1. フィルター ボックスの上の、依存関係の一覧の一番上にある **[分析]** をクリックします。 

    ![[Analytics] ボタン](./media/luis-tutorial-appinsights/analytics-button.png)

2. 新しいウィンドウが開きます。このウィンドウは、上部にクエリ ウィンドウがあり、その下にデータ テーブル ウィンドウがあります。 データベースを使用したことがある方は、よくご存じの配置でしょう。 クエリには、過去 24 時間の `LUIS-results` という名前で始まる項目がすべて含まれています。 **CustomDimensions** 列には、名前/値のペアとして LUIS クエリの結果が含まれています。

    ![Analytics クエリ ウィンドウ](./media/luis-tutorial-appinsights/analytics-query-window.png)

3. 最上位の意図、スコア、および発話を引き出すには、クエリ ウィンドウで、最後の行のすぐ上に次を追加します。

    ```kusto
    | extend topIntent = tostring(customDimensions.LUIS_intent_intent)
    | extend score = todouble(customDimensions.LUIS_intent_score)
    | extend utterance = tostring(customDimensions.LUIS_text)
    ```

4. クエリを実行します。 データ テーブルの右端までスクロールします。 topIntent、score、および utterance の新しい列を利用できます。 並べ替える topIntent 列をクリックします。

    ![Analytics の最上位の意図](./media/luis-tutorial-appinsights/app-insights-top-intent.png)


[Kusto クエリ言語](https://docs.microsoft.com/azure/log-analytics/query-language/get-started-queries)または [PowerBI へのデータのエクスポート](https://docs.microsoft.com/azure/application-insights/app-insights-export-power-bi)の詳細を確認してください。 

## <a name="next-steps"></a>次の手順

Application Insights データに追加する必要があるその他の情報としては、アプリ ID、バージョン ID、モデルの最終変更日、前回のトレーニング日、最後の発行日などがあります。 これらの値は、エンドポイントの URL (アプリ ID とバージョン ID) または[オーサリング API](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c3d) 呼び出しから取得し、Web アプリ ボットの設定で指定して、そこからプルすることができます。  

複数の LUIS アプリで同じエンドポイント サブスクリプションを使用している場合は、サブスクリプション ID と、それが共有キーであることを示すプロパティも含める必要があります。 

> [!div class="nextstepaction"]
> [発話の例の詳細](luis-how-to-add-example-utterances.md)
