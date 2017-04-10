---
title: "Azure Application Insights の検索の使用 | Microsoft Docs"
description: "Web アプリによって送信された未加工のテレメトリを検索およびフィルター処理します。"
services: application-insights
documentationcenter: 
author: alancameronwills
manager: carmonm
ms.assetid: 2a437555-8043-45ec-937a-225c9bf0066b
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 03/14/2017
ms.author: awills
translationtype: Human Translation
ms.sourcegitcommit: fd35f1774ffda3d3751a6fa4b6e17f2132274916
ms.openlocfilehash: 5e3cf1407079a166fbe5e24abae2248ef467ebbb
ms.lasthandoff: 03/16/2017


---
# <a name="using-search-in-application-insights"></a>Application Insights の検索の使用
検索は、ページ ビュー、例外、Web 要求などの個々のテレメトリ項目を検索または探索するために使用する [Application Insights](app-insights-overview.md) の機能です。 診断検索を使用すると、作成したログ トレースやイベントを表示できます。

(データでのより複雑なクエリについては、[Analytics](app-insights-analytics-tour.md) を使用してください。)

## <a name="where-do-you-see-search"></a>検索が表示される場所
### <a name="in-the-azure-portal"></a>Azure ポータルで次の操作を行います。
診断検索は、アプリケーションの Application Insights の概要ブレードから明示的に開くことができます。

![Open diagnostic search](./media/app-insights-diagnostic-search/01-open-Diagnostic.png)

診断検索は、特定のグラフやグリッド項目をクリックしたときにも表示されます。 この場合、選択した項目の種類に合わせてフィルターが事前設定されます。 

たとえば、概要ブレードには、要求の量が応答時間別に棒グラフで示されています。 パフォーマンス範囲をクリックすると、その応答時間の範囲にある要求の一覧が表示されます。

![要求パフォーマンスをクリック](./media/app-insights-diagnostic-search/07-open-from-filters.png)

診断検索の本体は、テレメトリ項目 (サーバー要求、ページ ビュー、お客様が作成したカスタム イベントなど) の一覧です。 一覧の上部には、一定期間に発生したイベントの数を示す概要グラフが表示されます。

[最新の情報に更新] をクリックして、新しいイベントを取得します。

### <a name="in-visual-studio"></a>Visual Studio で使用する

Visual Studio には、[Application Insights の検索] ウィンドウもあります。 このウィンドウは、デバッグ対象アプリケーションによって生成されたテレメトリ イベントを表示する際に便利ですが、 Azure Portal の発行済みアプリから収集されたイベントを表示することもできます。

Visual Studio で [検索] ウィンドウを開きます。

![Visual Studio で [Application Insights の検索] を開く](./media/app-insights-diagnostic-search/32.png)

[検索] ウィンドウには、Web ポータルと似た機能があります。

![Visual Studio の [Application Insights の検索] ウィンドウ](./media/app-insights-diagnostic-search/34.png)

[操作の追跡] タブは、要求またはページ ビューを開くときに使用できます。 "操作" は、1 つの要求やページ ビューに関連付けられているイベントのシーケンスです。 たとえば、依存関係の呼び出し、例外、トレース ログ、およびカスタム イベントが、1 つの操作に含まれる可能性があります。 [操作の追跡] タブには、要求またはページ ビューとの関連で、こうしたイベントのタイミングと期間がグラフィカルに表示されます。 

## <a name="inspect-individual-items"></a>個々の項目の確認
任意のテレメトリ項目を選択すると、キー フィールドと関連項目が表示されます。 フィールドの完全なセットを表示するには、[...] をクリックします。 

![新しい作業項目をクリックし、フィールドを編集して [OK] をクリックします。](./media/app-insights-diagnostic-search/10-detail.png)

## <a name="filter-event-types"></a>イベントの種類のフィルター選択
[フィルター] ブレードを開き、表示するイベントの種類を選択します  (後でブレードを開いたときに表示されるフィルターを復元するには、[リセット] をクリックします)。

![[フィルター] を選択し、テレメトリの種類を選択します](./media/app-insights-diagnostic-search/02-filter-req.png)

イベントの種類は、次のとおりです。

* **トレース** - [診断ログ](app-insights-asp-net-trace-logs.md) (TrackTrace、log4Net、NLog、System.Diagnostic.Trace の呼び出しなど)。
* **要求** - サーバー アプリケーションで受信した HTTP 要求 (ページ、スクリプト、画像、スタイル ファイル、データなど)。 これらのイベントは、要求と応答の概要グラフの作成に使用されます。
* **ページ ビュー** - [Web クライアントによって送信されたテレメトリ](app-insights-javascript.md)。ページ ビュー レポートの作成に使用されます。 
* **カスタム イベント** - [利用状況の監視](app-insights-api-custom-events-metrics.md)のために TrackEvent() への呼び出しを挿入した場合は、ここで検索できます。
* **例外** - TrackException() を使用してログに記録した、[サーバーでキャッチされていない例外](app-insights-asp-net-exceptions.md)。
* **依存関係** - 他のサービスに対する[サーバー アプリケーションからの呼び出し](app-insights-asp-net-dependencies.md) (REST API、データベースなど)、および[クライアント コード](app-insights-javascript.md)からの AJAX 呼び出し。
* **可用性** - [可用性テスト](app-insights-monitor-web-app-availability.md)の結果。

## <a name="filter-on-property-values"></a>プロパティの値に基づくフィルター選択
プロパティの値に基づいてイベントをフィルター選択できます。 使用可能なプロパティは、選択したイベントの種類によって異なります。 

たとえば、特定の応答コードを持つ要求を選択できます。 

![プロパティを展開し、値を選択します](./media/app-insights-diagnostic-search/03-response500.png)

特定のプロパティの値を選択しない場合、すべての値を選択するのと同じ意味になり、 そのプロパティに基づくフィルターはオフになります。

### <a name="narrow-your-search"></a>検索の絞り込み
フィルター値の右側の値は、現在のフィルター選択されたセットに含まれるイベントの発生回数を表します。 

この例では、"500" エラーのほとんどが "Rpt/Employees" 要求であることがわかります。

![プロパティを展開し、値を選択します](./media/app-insights-diagnostic-search/04-failingReq.png)




## <a name="find-events-with-the-same-property"></a>同じプロパティを持つイベントの検索
同じプロパティ値を持つすべての項目を検索できます。

![プロパティを右クリックします](./media/app-insights-diagnostic-search/12-samevalue.png)


## <a name="search-the-data"></a>データの検索

> [!NOTE]
> さらに複雑なクエリを作成するには、[検索] ブレードの上部から [**Analytics**](app-insights-analytics-tour.md) を開きます。
> 

すべてのプロパティ値について語句を検索できます。 この機能は、プロパティ値を持つ[カスタム イベント](app-insights-api-custom-events-metrics.md)を作成している場合に特に便利です。 

時間範囲を設定することもできます。範囲が狭いほど、検索時間が短くなります。 

![Open diagnostic search](./media/app-insights-diagnostic-search/appinsights-311search.png)

部分文字列ではなく、語句全体を検索します。 引用符で特殊文字を囲みます。

| string | 一致 "*しない*" | 一致する |
| --- | --- | --- |
| HomeController.About |home<br/>controller<br/>out | homecontroller<br/>about<br/>"homecontroller.about"|
|米国|Uni<br/>ted|united<br/>states<br/>united AND states<br/>"united states"

使用できる検索表現を次に示します。

| サンプル クエリ | 効果 |
| --- | --- |
| `apple` |時間範囲内でフィールドに "apple" という単語が含まれるすべてのイベントを検索します |
| `apple AND banana` |両方の単語を含むイベントを検索します。 "and" ではなく大文字の "AND" を使用します。 |
| `apple OR banana`<br/>`apple banana` |どちらかの単語を含むイベントを検索します。 "or" ではなく "OR" を使用します。<br/>短縮形。 |
| `apple NOT banana` |ある単語を含む一方で他方の単語を含まないイベントを検索します。 |



## <a name="sampling"></a>サンプリング
(ASP.NET SDK バージョン 2.0.0-beta3 以降を使用している状態で) アプリから大量のテレメトリが生成されると、アダプティブ サンプリング モジュールからイベントの代表的な部分のみが送信され、ポータルに送信されるデータ量が自動的に削減されます。 ただし、同じ要求に関連するイベントはグループ単位で選択または選択解除されるため、関連するイベントごとに操作できます。 

[サンプリングについてはこちらを参照してください](app-insights-sampling.md)。



## <a name="create-work-item"></a>作業項目を作成する
任意のテレメトリ項目の詳細を使用して、GitHub または Visual Studio Team Services でバグを作成できます。 

![新しい作業項目をクリックし、フィールドを編集して [OK] をクリックします。](./media/app-insights-diagnostic-search/42.png)

これを初めて行う場合は、Team Services のアカウントおよびプロジェクトへのリンクを構成するように求められます。

![Team Services サーバーとプロジェクト名の URL を入力し、[承認] をクリックします](./media/app-insights-diagnostic-search/41.png)

([作業項目] ブレードでリンクを構成することもできます)。

## <a name="save-your-search"></a>検索条件の保存
必要なフィルターをすべて設定した後、この検索条件をお気に入りとして保存できます。 組織のアカウントで作業している場合は、これを他のチーム メンバーと共有するかどうかを選択できます。

![[お気に入り] をクリックし、名前を設定して [保存] をクリックします](./media/app-insights-diagnostic-search/08-favorite-save.png)

検索条件をもう一度表示するには、 **概要ブレードに移動** し、[お気に入り] を開きます。

![[お気に入り] タイル](./media/app-insights-diagnostic-search/09-favorite-get.png)

相対的な時間範囲を選択して保存した場合は、ブレードを開くたびに最新のデータが表示されます。 絶対的な時間範囲を選択して保存した場合は、毎回同じデータが表示されます。 お気に入りを保存するときに、"相対的" を使用できない場合は、[時間の範囲] をクリックし、ユーザー設定の範囲ではない時間の範囲を設定します。

## <a name="send-more-telemetry-to-application-insights"></a>さらに多くのテレメトリを Application Insights に送信する
Application Insights SDK によって送信される標準のテレメトリに加えて、次の操作を実行できます。

* [.NET](app-insights-asp-net-trace-logs.md) または [Java](app-insights-java-trace-logs.md) の好みのログ記録フレームワークからのログ トレースをキャプチャする。 これは、ログ トレースを検索し、ページ ビュー、例外、その他のイベントと関連付けることができることを意味します。 
* カスタム イベント、ページ ビュー、および例外を送信する[コードを作成](app-insights-api-custom-events-metrics.md)する。 

[ログとカスタム テレメトリを Application Insights に送信する方法についてはこちら](app-insights-search-diagnostic-logs.md)。

## <a name="questions"></a>Q & A
### <a name="limits"></a>保持されるデータの量はどのくらいですか

「[制限の概要](app-insights-pricing.md#limits-summary)」を参照してください。

### <a name="how-can-i-see-post-data-in-my-server-requests"></a>サーバーの要求の POST データを表示するにはどうしたらよいですか
POST データは自動的に記録されませんが、[TrackTrace または log の呼び出し](app-insights-search-diagnostic-logs.md)を使用できます。 メッセージ パラメーターに POST データを格納します。 プロパティと同じ方法でメッセージをフィルター処理することはできませんが、サイズの制限が緩和されます。

## <a name="video"></a>ビデオ

> [!VIDEO https://channel9.msdn.com/events/Connect/2016/112/player]

## <a name="add"></a>次のステップ
* [Analytics で複雑なクエリを作成する](app-insights-analytics-tour.md)
* [Application Insights にログとカスタム テレメトリを送信する](app-insights-search-diagnostic-logs.md)
* [可用性と応答性のテストを設定する](app-insights-monitor-web-app-availability.md)
* [トラブルシューティング](app-insights-troubleshoot-faq.md)

