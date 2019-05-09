---
title: C# を使用した Application Insights
titleSuffix: Azure Cognitive Services
description: C# を使用して LUIS アプリケーションおよび Application Insights と統合されるボットを構築します。
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: article
ms.date: 01/23/2019
ms.author: diberry
ms.openlocfilehash: d546951cf283cf15874b1b6d95da75549a8a93ac
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/07/2019
ms.locfileid: "55884938"
---
# <a name="add-luis-results-to-application-insights-with-a-bot-in-c"></a>C# からボットを使用して LUIS の結果を Application Insights に追加する

このチュートリアルでは、LUIS 応答の情報を [Application Insights](https://azure.microsoft.com/services/application-insights/) テレメトリ データ ストレージに追加します。 データを用意したら、Kusto 言語または PowerBI を使用して、意図および発話のエンティティについてリアルタイムで分析、集計、およびレポートできます。 この分析は、LUIS アプリの意図およびエンティティを追加または編集する必要があるかどうかの判断に役立ちます。

ボットは、Bot Framework 3.x と Azure Web アプリ ボットで構築します。

このチュートリアルでは、以下の内容を学習します。

> [!div class="checklist"]
> * Application Insights を Web アプリ ボットに追加する
> * LUIS クエリの結果をキャプチャし、Application Insights に送信する
> * Application Insights で最上位の意図、スコア、および発話のクエリを実行する

## <a name="prerequisites"></a>前提条件

* **[前のチュートリアル](luis-csharp-tutorial-build-bot-framework-sample.md)** の LUIS Web アプリ ボット。Application Insights が有効になっている必要があります。
* [Visual Studio 2017](https://www.visualstudio.com/downloads/) がお使いのローカル コンピューターにインストールされている。

> [!Tip]
> サブスクリプションがない場合は、[無料アカウント](https://azure.microsoft.com/free/)に登録できます。

このチュートリアルのコードはすべて、[Azure-Samples GitHub リポジトリ](https://github.com/Azure-Samples/cognitive-services-language-understanding/tree/master/documentation-samples/tutorial-web-app-bot-application-insights/csharp)から入手できます。このチュートリアルに関連付けられている各行に、`//LUIS Tutorial:` というコメントが付いています。

## <a name="review-luis-web-app-bot"></a>LUIS Web アプリ ボットの確認

このチュートリアルでは、次のようなコードがあること、または[他のチュートリアル](luis-csharp-tutorial-build-bot-framework-sample.md)を完了していることを前提としています。

   [!code-csharp[Web app bot with LUIS](~/samples-luis/documentation-samples/tutorial-web-app-bot/csharp/BasicLuisDialog.cs "Web app bot with LUIS")]

## <a name="application-insights-in-web-app-bot"></a>Web アプリ ボットの Application Insights

現在、Web アプリ ボット サービスの作成の一環として追加された Application Insights サービスで収集されるのは、ボット用の一般的な状態テレメトリです。 LUIS 応答情報は収集されません。 LUIS を分析および改善するには、LUIS 応答情報が必要です。  

LUIS 応答をキャプチャするには、Web アプリ ボットで、**[Application Insights](https://www.nuget.org/packages/Microsoft.ApplicationInsights/)** がプロジェクト用にインストールおよび構成されている必要があります。

## <a name="download-web-app-bot"></a>Web アプリ ボットのダウンロード

[Visual Studio 2017](https://www.visualstudio.com/downloads/) を使用して、Web アプリ ボット用に Application Insights を追加し、構成します。 Visual Studio で Web アプリ ボットを使用するには、Web アプリ ボット コードをダウンロードします。

1. Azure portal で、Web アプリ ボットに対して、**[ビルド]** を選択します。

    ![ポータルで [ビルド] を選択する](./media/luis-tutorial-bot-csharp-appinsights/download-build-menu.png)

2. **[ZIP 形式でダウンロード]** を選択し、ファイルの準備が完了するまで待ちます。

    ![ZIP 形式でダウンロード](./media/luis-tutorial-bot-csharp-appinsights/download-link.png)

3. ポップアップ ウィンドウで **[ZIP 形式でダウンロード]** を選択します。 コンピューター上の場所を覚えておいてください。次のセクションで必要になります。

    ![[ZIP 形式でダウンロード] ポップアップ](./media/luis-tutorial-bot-csharp-appinsights/download-popup.png)

## <a name="open-solution-in-visual-studio-2017"></a>Visual Studio 2017 でソリューションを開く

1. ファイルをフォルダーに抽出します。

2. Visual Studio 2017 を開いて、ソリューション ファイル `Microsoft.Bot.Sample.LuisBot.sln` を開きます。 セキュリティ警告が表示された場合は、[OK] を選択します。

    ![Visual Studio 2017 でソリューションを開く](./media/luis-tutorial-bot-csharp-appinsights/vs-2017-security-warning.png)

3. Visual Studio では、依存関係をソリューションに追加する必要があります。 **ソリューション エクスプローラー**で、**[参照設定]** を右クリックし、**[NuGet パッケージの管理]** を選択します。

    ![Manage NuGet packages](./media/luis-tutorial-bot-csharp-appinsights/vs-2017-manage-nuget-packages.png)

4. NuGet パッケージ マネージャーに、インストールされているパッケージの一覧が表示されます。 黄色のバーで **[復元]** を選択します。 復元プロセスが完了するまで待ちます。

    ![NuGet パッケージの復元](./media/luis-tutorial-bot-csharp-appinsights/vs-2017-restore-packages.png)

## <a name="add-application-insights-to-the-project"></a>プロジェクトへの Application Insights の追加

Visual Studio で Application Insights をインストールし、構成します。

1. Visual Studio 2017 の上部のメニューで、**[プロジェクト]** を選択し、**[Application Insights Telemetry の追加]** を選択します。

2. **[Application Insights の構成]** ウィンドウで、**[無料で開始する]** を選択します

    ![Application Insights の構成の開始](./media/luis-tutorial-bot-csharp-appinsights/vs-2017-configure-app-insights.png)

3. お使いのアプリを Application Insights に登録します。 Azure portal 資格情報の入力が必要になる場合があります。

4. Visual Studio によって Application Insights がプロジェクトに追加され、その状態が次のように表示されます。

    ![Application Insights の状態](./media/luis-tutorial-bot-csharp-appinsights/vs-2017-adding-application-insights-to-project.png)

    プロセスが完了したら、**[Application Insights の構成]** に進行状況が表示されます。

    ![Application Insights の進行状況](./media/luis-tutorial-bot-csharp-appinsights/vs-2017-configured-application-insights-to-project.png)

    **[トレースの収集を有効にします]** が赤で表示されています。これら、この機能が無効であることを意味します。 この機能は、このチュートリアルでは使用しません。

## <a name="build-and-resolve-errors"></a>ビルドとエラーの解決

1. ソリューションをビルドするには、**[ビルド]** メニューを選択し、**[ソリューションのリビルド]** を選択します。 ビルドが完了するまで待ちます。

2. ビルドが `CS0104` エラーで失敗した場合は、修正する必要があります。 `Controllers` フォルダーの `MessagesController.cs file` で、アクティビティの種類にプレフィックスとしてコネクタの種類を付けることで、`Activity` の種類の用途のあいまいさを明確にします。 これを行うには、22 行目から 36 行目までの `Activity` の名前を `Activity` から `Connector.Activity` に変更します。 ソリューションをもう一度ビルドします。 これでビルド エラーは発生しないはずです。

    そのファイルの完全なソースは次のとおりです。

    [!code-csharp[MessagesController.cs file](~/samples-luis/documentation-samples/tutorial-web-app-bot-application-insights/csharp/MessagesController.cs "MessagesController.cs file")]

## <a name="publish-project-to-azure"></a>Azure へのプロジェクトの発行

Azure portal で **Application Insights** パッケージがプロジェクトに追加され、資格情報に対して適切に構成されました。 プロジェクトに対する変更は、Azure に再度発行する必要があります。

1. **ソリューション エクスプローラー**で、プロジェクト名を右クリックし、**[発行]** を選択します。

    ![ポータルへのプロジェクトの発行](./media/luis-tutorial-bot-csharp-appinsights/vs-2017-publish.png)

2. **[発行]** ウィンドウで、**[新しいプロファイルの作成]** を選択します。

    ![発行の一部として、新しいプロファイルを作成します。](./media/luis-tutorial-bot-csharp-appinsights/vs-2017-publish-1.png)

3. **[プロファイルのインポート]** を選択し、**[OK]** を選択します。

    ![発行の一部として、プロファイルをインポートします。](./media/luis-tutorial-bot-csharp-appinsights/vs-2017-publish-2.png)

4. **[発行設定ファイルのインポート]** ウィンドウで、お使いのプロジェクト フォルダー、`PostDeployScripts` フォルダーの順に移動し、末尾が `.PublishSettings` のファイル、`Open` の順に選択します。 これで、このプロジェクトの発行が構成されました。

5. **[発行]** を選択して、ローカル ソース コードを Bot Service に発行します。 **[出力]** ウィンドウに状態が表示されます。 このチュートリアルの残りの部分は、Azure portal で実行します。 Visual Studio 2017 を閉じます。

## <a name="open-three-browser-tabs"></a>3 つのブラウザー タブを開く

Azure portal で、Web アプリ ボットを見つけて開きます。 次の手順では、Web アプリ ボットの 3 つの異なるビューを使用します。 操作しやすいようにブラウザーで 3 つのタブを個別に開くことをお勧めします。
  
>  * Web チャットでのテスト
>  * オンライン コード エディターをビルド/開く -> App Service Editor
>  * App Service Editor/Kudu コンソールを開く -> 診断コンソール

## <a name="modify-basicluisdialogcs-code"></a>BasicLuisDialog.cs コードの変更

1. **App Service Editor** のブラウザー タブで、`BasicLuisDialog.cs` ファイルを開きます。

2. 次の NuGet 依存関係を既存の `using` 行の下に追加します。

   [!code-csharp[Add using statement](~/samples-luis/documentation-samples/tutorial-web-app-bot-application-insights/csharp/BasicLuisDialog.cs?range=11-12 "Add using statement")]

3. `LogToApplicationInsights` 関数を追加します。

   [!code-csharp[Add the LogToApplicationInsights function](~/samples-luis/documentation-samples/tutorial-web-app-bot-application-insights/csharp/BasicLuisDialog.cs?range=61-92 "Add the LogToApplicationInsights function")]

    Application Insights インストルメンテーション キーは、`BotDevInsightsKey` という名前の Web アプリ ボットのアプリケーション設定に既に存在します。

    関数の最後の行によって、データが Application Insights に追加されます。 トレースの名前は `LUIS` です。これは、この Web アプリ ボットによって収集される他のテレメトリ データとは別の一意の名前です。 また、すべてのプロパティにプレフィックス `LUIS_` が付くため、このチュートリアルで追加されたデータを、Web アプリ ボットによって提供された情報と比較して確認できます。

4. `ShowLuisResult` 関数の上部で `LogToApplicationInsights` 関数を呼び出します。

   [!code-csharp[Use the LogToApplicationInsights function](~/samples-luis/documentation-samples/tutorial-web-app-bot-application-insights/csharp/BasicLuisDialog.cs?range=114-115 "Use the LogToApplicationInsights function")]

## <a name="build-web-app-bot"></a>Web アプリ ボットのビルド

1. 2 つの方法のいずれかで Web アプリ ボットをビルドします。 1 つは **App Service Editor** で `build.cmd` を右クリックし、**[Run from Console]\(コンソールから実行\)** を選択する方法です。 コンソールの出力が表示され、`Finished successfully.` で完了します

2. 正常に完了しない場合は、コンソールを開き、スクリプトに移動して、次の手順でそのスクリプトを実行します。 **App Service Editor** の上部の青いバーで、お使いのボットの名前を選択し、ドロップダウン リストで **[Open Kudu Console]\(Kudu コンソールを開く\)** を選択します。

    ![Kudu コンソールを開く](./media/luis-tutorial-bot-csharp-appinsights/open-kudu-console.png)

3. コンソール ウィンドウで、次のコマンドを入力します。

    ```console
    cd site\wwwroot && build.cmd
    ```

    ビルドが `Finished successfully.` で完了するまで待ちます

## <a name="test-the-web-app-bot"></a>Web アプリ ボットのテスト

1. Web アプリ ボットをテストするには、ポータルの **[Test in Web Chat]\(Web チャットでのテスト\)** を開きます。

2. フレーズ「`Coffee bar on please`」を入力します。  

    ![チャットでの Web アプリ ボットのテスト](./media/luis-tutorial-bot-csharp-appinsights/test-in-web-chat.png)

3. チャットボット応答は変わりません。 この変更により、データが、ボット応答ではなく、Application Insights に送信されます。 発話をいくつか追加して、Application Insights のデータを少し増やします。

|発話|
|--|
|ピザを宅配して|
|照明をすべて消して|
|玄関の照明を付けて|


## <a name="view-luis-entries-in-application-insights"></a>Application Insights の LUIS エントリの表示

Application Insights を開いて、LUIS エントリを表示します。

1. ポータルで、**[すべてのリソース]** を選択し、Web アプリ ボット名でフィルター処理します。 **Application Insights** 型のリソースをクリックします。 Application Insights のアイコンは電球です。

    ![Azure Portal で Application Insights を探す](./media/luis-tutorial-bot-csharp-appinsights/portal-service-list-app-insights.png)

2. リソースが開いたら、一番右のパネルにある虫眼鏡の**検索**アイコンをクリックします。 右側に新しいパネルが表示されます。 見つかったテレメトリ データの量に応じて、パネルが表示されるまでに少し間が空くことがあります。 `LUIS` を検索します。 このチュートリアルを使用して追加された LUIS クエリ結果だけに一覧が絞り込まれます。

    ![トレースの検索](./media/luis-tutorial-bot-csharp-appinsights/portal-service-list-app-insights-search-luis-trace.png)

3. 最上位のエントリを選択します。 右端の新しいウィンドウに、LUIS クエリのカスタム データを含む詳細なデータが表示されます。 データには、最上位の意図とそのスコアが含まれています。

    ![トレース項目のレビュー](./media/luis-tutorial-bot-csharp-appinsights/portal-service-list-app-insights-search-luis-trace-item.png)

    終了したら、右端の上にある **X** を選択して、依存関係の項目の一覧に戻ります。

> [!Tip]
> 依存関係の一覧を保存し、後でその一覧に戻る場合は、**[詳細]**、**[お気に入りの保存]** の順にクリックします。

## <a name="query-application-insights-for-intent-score-and-utterance"></a>Application Insights で意図、スコア、および発話のクエリを実行する

Application Insights を使用すると、[Kusto](https://docs.microsoft.com/azure/application-insights/app-insights-analytics#query-data-in-analytics) 言語を使用してデータにクエリを実行したり、[PowerBI](https://powerbi.microsoft.com) にデータをエクスポートしたりできます。

1. フィルター ボックスの上の、依存関係の一覧の一番上にある **[分析]** をクリックします。

    ![[Analytics] ボタン](./media/luis-tutorial-bot-csharp-appinsights/portal-service-list-app-insights-search-luis-analytics-button.png)

2. 新しいウィンドウが開きます。このウィンドウは、上部にクエリ ウィンドウがあり、その下にデータ テーブル ウィンドウがあります。 データベースを使用したことがある方は、よくご存じの配置でしょう。 クエリには、過去 24 時間の `LUIS` という名前で始まる項目がすべて含まれています。 **CustomDimensions** 列には、LUIS のクエリ結果が名前/値ペアとして含まれています。

    ![既定の分析レポート](./media/luis-tutorial-bot-csharp-appinsights/analytics-query-1.png)

3. 最上位の意図、スコア、および発話を引き出すには、クエリ ウィンドウで、最後の行のすぐ上に次を追加します。

    ```kusto
    | extend topIntent = tostring(customDimensions.LUIS_topScoringIntent)
    | extend score = todouble(customDimensions.LUIS_topScoringIntentScore)
    | extend utterance = tostring(customDimensions.LUIS_query)
    ```

4. クエリを実行します。 データ テーブルの右端までスクロールします。 topIntent、score、および utterance の新しい列を利用できます。 並べ替える topIntent 列をクリックします。

    ![カスタム分析レポート](./media/luis-tutorial-bot-csharp-appinsights/analytics-query-2.png)

[Kusto クエリ言語](https://docs.microsoft.com/azure/log-analytics/query-language/get-started-queries)または [PowerBI へのデータのエクスポート](https://docs.microsoft.com/azure/application-insights/app-insights-export-power-bi)の詳細を確認してください。

## <a name="learn-more-about-bot-framework"></a>Bot Framework の詳細

[Bot Framework](https://dev.botframework.com/) の詳細を確認してください。

## <a name="next-steps"></a>次の手順

Application Insights データに追加する必要があるその他の情報としては、アプリ ID、バージョン ID、モデルの最終変更日、前回のトレーニング日、最後の発行日などがあります。 これらの値は、エンドポイントの URL (アプリ ID とバージョン ID) または[オーサリング API](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c3d) 呼び出しから取得し、Web アプリ ボットの設定で指定して、そこからプルすることができます。  

複数の LUIS アプリで同じエンドポイント サブスクリプションを使用している場合は、サブスクリプション ID と、それが共有キーであることを示すプロパティも含める必要があります。

> [!div class="nextstepaction"]
> [発話の例の詳細](luis-how-to-add-example-utterances.md)
