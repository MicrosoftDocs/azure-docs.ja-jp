---
title: 検索トラフィックの分析を実装する - Azure Search
description: テレメトリおよびユーザーによって開始されたイベントをログ ファイルに追加するため、Azure Search の検索トラフィックの分析を有効にします。
author: HeidiSteen
manager: cgronlun
services: search
ms.service: search
ms.topic: conceptual
ms.date: 01/25/2019
ms.author: heidist
ms.custom: seodec2018
ms.openlocfilehash: b15ae30151b22509a78b9a39d258991363a05e5b
ms.sourcegitcommit: 2d3b1d7653c6c585e9423cf41658de0c68d883fa
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/20/2019
ms.locfileid: "67295418"
---
# <a name="implement-search-traffic-analytics-in-azure-search"></a>Azure Search で検索トラフィックの分析を実装する
検索トラフィックの分析は、検索サービスのフィードバック ループの実装パターンです。 このパターンでは、必要なデータと、Application Insights を使用してデータを収集する方法を示します。Application Insights は、複数のプラットフォームでサービスを監視する業界最先端のサービスです。

検索トラフィックの分析により、検索サービスを可視化し、ユーザーとその行動に関する洞察を明らかにすることができます。 ユーザーが選択した内容に関するデータを得ることで、検索エクスペリエンスをさらに向上させるための意思決定が可能となり、結果が期待していたものではない場合にバックオフできます。

Azure Search は、Azure Application Insights と Power BI を統合して詳細な監視と追跡を行うことができるテレメトリ ソリューションを提供します。 Azure Search を操作するには API が必要となるため、検索を使用する開発者は、このページの手順に従ってテレメトリを実装する必要があります。

## <a name="identify-relevant-search-data"></a>関連する検索データを識別する

有用な検索メトリックを得るには、検索アプリケーションのユーザーからのシグナルをログに記録する必要があります。 これらのシグナルは、ユーザーが興味を持っているコンテンツやニーズに関連するとみなしたコンテンツを示しています。

検索トラフィックの分析には、次の 2 つのシグナルが必要です。

1. ユーザーによって生成された検索イベント: ユーザーによって開始された検索クエリだけが対象となります。 ファセット、追加コンテンツ、または内部情報の設定に使用される検索要求は重要ではありません。これらの要求は結果を歪曲し、偏った結果を招きます。

2. ユーザーによって生成されたクリック イベント:このドキュメントでの "クリック" は、検索クエリから返された特定の検索結果を選択するユーザーを指します。 一般に、クリックは、ドキュメントが特定の検索クエリの適切な結果であることを意味します。

検索イベントとクリック イベントを関連付け ID と結び付けることで、アプリケーションでのユーザーの行動を分析できます。 検索に関するこれらの洞察は、検索トラフィックのログだけでは得られません。

## <a name="add-search-traffic-analytics"></a>検索トラフィックの分析を追加する

前のセクションで説明したシグナルは、ユーザーが検索アプリケーションを操作するときに、そのアプリケーションから収集する必要があります。 Application Insights は、柔軟なインストルメンテーション オプションを備え、複数のプラットフォームで利用できる拡張可能な監視ソリューションです。 Application Insights を使用すると、Azure Search で作成された Power BI 検索レポートを活用してデータを簡単に分析できます。

Azure Search サービスの[ポータル](https://portal.azure.com) ページの [検索トラフィックの分析] ブレードには、このテレメトリ パターンに従うためのチート シートが含まれています。 また、Application Insights リソースの選択や作成、必要なデータの表示をすべて 1 か所で行うことができます。

![検索トラフィックの分析の手順][1]

## <a name="1---select-a-resource"></a>1 - リソースを選択する

使用する Application Insights リソースを選択するか、リソースがまだない場合は作成する必要があります。 既に使用中のリソースを使って、必要なカスタム イベントをログに記録できます。

新しい Application Insights リソースを作成する場合、このシナリオにはすべてのアプリケーションの種類が有効です。 使用しているプラットフォームに最適なアプリケーションの種類を選択します。

アプリケーションのテレメトリ クライアントを作成するために、インストルメンテーション キーが必要です。 このキーは、Application Insights ポータル ダッシュボードから取得することも、使用するインスタンスを選択して、[検索トラフィックの分析] ページから取得することもできます。

## <a name="2---add-instrumentation"></a>2 - インストルメンテーションを追加する

このフェーズでは、前の手順で作成した Application Insights リソースを使用して、独自の検索アプリケーションをインストルメント化します。 このプロセスには、次の 4 つの手順があります。

**手順 1:テレメトリ クライアントを作成する**: これは、Application Insights リソースにイベントを送信するオブジェクトです。

*C#*

    private TelemetryClient telemetryClient = new TelemetryClient();
    telemetryClient.InstrumentationKey = "<YOUR INSTRUMENTATION KEY>";

*JavaScript*

    <script type="text/javascript">var appInsights=window.appInsights||function(config){function r(config){t[config]=function(){var i=arguments;t.queue.push(function(){t[config].apply(t,i)})}}var t={config:config},u=document,e=window,o="script",s=u.createElement(o),i,f;s.src=config.url||"//az416426.vo.msecnd.net/scripts/a/ai.0.js";u.getElementsByTagName(o)[0].parentNode.appendChild(s);try{t.cookie=u.cookie}catch(h){}for(t.queue=[],i=["Event","Exception","Metric","PageView","Trace","Dependency"];i.length;)r("track"+i.pop());return r("setAuthenticatedUserContext"),r("clearAuthenticatedUserContext"),config.disableExceptionTracking||(i="onerror",r("_"+i),f=e[i],e[i]=function(config,r,u,e,o){var s=f&&f(config,r,u,e,o);return s!==!0&&t["_"+i](config,r,u,e,o),s}),t}
    ({
    instrumentationKey: "<YOUR INSTRUMENTATION KEY>"
    });
    window.appInsights=appInsights;
    </script>

他の言語とプラットフォームについては、こちらの[一覧](https://docs.microsoft.com/azure/application-insights/app-insights-platforms)をご覧ください。

**手順 2:関連付けのための検索 ID を要求する**: 検索要求をクリックに関連付けるには、この 2 つの異なるイベントを関連付ける関連付け ID が必要です。 ヘッダーで検索 ID を要求すると、Azure Search から ID が提供されます。

*C#*

    // This sample uses the Azure Search .NET SDK https://www.nuget.org/packages/Microsoft.Azure.Search

    var client = new SearchIndexClient(<SearchServiceName>, <IndexName>, new SearchCredentials(<QueryKey>)
    var headers = new Dictionary<string, List<string>>() { { "x-ms-azs-return-searchid", new List<string>() { "true" } } };
    var response = await client.Documents.SearchWithHttpMessagesAsync(searchText: searchText, searchParameters: parameters, customHeaders: headers);
    IEnumerable<string> headerValues;
    string searchId = string.Empty;
    if (response.Response.Headers.TryGetValues("x-ms-azs-searchid", out headerValues)){
     searchId = headerValues.FirstOrDefault();
    }

*JavaScript*

    request.setRequestHeader("x-ms-azs-return-searchid", "true");
    request.setRequestHeader("Access-Control-Expose-Headers", "x-ms-azs-searchid");
    var searchId = request.getResponseHeader('x-ms-azs-searchid');

**手順 3:検索イベントをログに記録する**:

ユーザーによって検索要求が発行されるたびに、Application Insights のカスタム イベントで次のスキーマを使用して、検索要求を検索イベントとしてログに記録します。

**SearchServiceName**: (文字列) 検索サービス名 **SearchId**: (GUID) 検索クエリの一意の識別子 (検索応答で提供) **IndexName**: (文字列) 照会する検索サービス インデックス **QueryTerms**: (文字列) ユーザーが入力した検索語句 **ResultCount**: (int) 返されたドキュメントの数 (検索応答で提供) **ScoringProfile**: (文字列) 使用するスコアリング プロファイルの名前 (存在する場合)。

> [!NOTE]
> 検索クエリに $count=true を追加して、ユーザーによって生成されたクエリのカウントを要求します。 詳細については、[こちら](https://docs.microsoft.com/rest/api/searchservice/search-documents#request)をご覧ください。
>

> [!NOTE]
> ログに記録するのは、ユーザーによって生成された検索クエリだけであることに注意してください。
>

*C#*

    var properties = new Dictionary <string, string> {
    {"SearchServiceName", <service name>},
    {"SearchId", <search Id>},
    {"IndexName", <index name>},
    {"QueryTerms", <search terms>},
    {"ResultCount", <results count>},
    {"ScoringProfile", <scoring profile used>}
    };
    telemetryClient.TrackEvent("Search", properties);

*JavaScript*

    appInsights.trackEvent("Search", {
    SearchServiceName: <service name>,
    SearchId: <search id>,
    IndexName: <index name>,
    QueryTerms: <search terms>,
    ResultCount: <results count>,
    ScoringProfile: <scoring profile used>
    });

**手順 4:クリック イベントをログに記録する**:

ユーザーがドキュメントをクリックするたびに、それをシグナルとして、検索分析用にログに記録する必要があります。 Application Insights のカスタム イベントで次のスキーマを使用して、これらのイベントをログに記録します。

**ServiceName**: (文字列) 検索サービス名 **SearchId**: (GUID) 関連する検索クエリの一意の識別子 **DocId**: (文字列) ドキュメント識別子 **Position**: (int) 検索結果ページでのドキュメントの順位

> [!NOTE]
> Position は、アプリケーションでの基本的な順序を指します。 比較できるように、この数値は常に同じであれば自由に設定できます。
>

*C#*

    var properties = new Dictionary <string, string> {
    {"SearchServiceName", <service name>},
    {"SearchId", <search id>},
    {"ClickedDocId", <clicked document id>},
    {"Rank", <clicked document position>}
    };
    telemetryClient.TrackEvent("Click", properties);

*JavaScript*

    appInsights.trackEvent("Click", {
        SearchServiceName: <service name>,
        SearchId: <search id>,
        ClickedDocId: <clicked document id>,
        Rank: <clicked document position>
    });

## <a name="3---analyze-in-power-bi"></a>3 - Power BI で分析する

アプリケーションをインストルメント化し、アプリケーションが Application Insights に正しく接続されていることを確認したら、Azure Search で作成された定義済みのテンプレートを Power BI Desktop に使用できます。 

ログ データを分析できるように、Azure Search には監視用 [Power BI コンテンツ パック](https://app.powerbi.com/getdata/services/azure-search)が用意されています。 コンテンツ パックでは、検索トラフィックの分析用にキャプチャされた追加データの分析に役立つ定義済みのグラフとテーブルが追加されます。 詳しくは、[コンテンツ パックのヘルプ ページ](https://powerbi.microsoft.com/documentation/powerbi-content-pack-azure-search/)をご覧ください。 

1. Azure Search ダッシュボードの左側のナビゲーション ウィンドウで、 **[設定]** の **[検索トラフィックの分析]** をクリックします。

2. **[検索トラフィックの分析]** ページのステップ 3 で、 **[Get Power BI Desktop]\(Power BI Desktop の取得\)** をクリックして Power BI をインストールします。

   ![Power BI レポートを取得する](./media/search-traffic-analytics/get-use-power-bi.png "Power BI レポートを取得する")

2. 同じページで、 **[PowerBI レポートのダウンロード]** をクリックします。

3. Power BI Desktop でレポートが開かれ、Application Insights に接続するよう求められます。 この情報は、Azure portal の Application Insights リソースに関するページで見つかります。

   ![Application Insights に接続する](./media/search-traffic-analytics/connect-to-app-insights.png "Application Insights に接続する")

4. **[読み込み]** をクリックします。

レポートには、検索のパフォーマンスと関連性を向上させるために、多くの情報に基づいた意思決定を行う上で役立つグラフとテーブルが含まれています。

メトリックには、次のものが含まれています。

* クリック率 (CTR): 検索総数に対する特定のドキュメントをクリックしたユーザー数の割合。
* クリックされていない検索語句: クリックが登録されていない上位クエリの語句
* 最もクリックされたドキュメント: 過去 24 時間、7 日間、30 日間の ID 別の最もクリックされたドキュメント
* 語句とドキュメントの一般的な組み合わせ: クリック数で並べ替えられた、結果的に同じドキュメントがクリックされた語句
* クリック時間: 検索クエリ後の経過時間でバケット化されたクリック

次のスクリーンショットでは、検索トラフィックを分析するための組み込みのレポートとグラフを示します。

![Azure Search 用の power BI ダッシュボード](./media/search-traffic-analytics/AzureSearch-PowerBI-Dashboard.png "Azure Search 用の Power BI ダッシュボード")

## <a name="next-steps"></a>次の手順
検索アプリケーションをインストルメント化して、検索サービスに関する有益で洞察に富んだデータを取得します。

[Application Insights](https://docs.microsoft.com/azure/azure-monitor/app/app-insights-overview) では詳しい情報が見つかります。また、さまざまなサービス レベルについて詳しくは、[価格に関するページ](https://azure.microsoft.com/pricing/details/application-insights/)をご覧ください。

レポートの作成についての詳細。 「[Power BI Desktop の概要](https://powerbi.microsoft.com/documentation/powerbi-desktop-getting-started/)」をご覧ください。

<!--Image references-->
[1]: ./media/search-traffic-analytics/AzureSearch-TrafficAnalytics.png
[2]: ./media/search-traffic-analytics/AzureSearch-AppInsightsData.png
[3]: ./media/search-traffic-analytics/AzureSearch-PBITemplate.png
