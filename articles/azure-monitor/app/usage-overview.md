---
title: Azure Application Insights による利用状況分析 | Microsoft docs
description: ユーザーを理解し、提供しているアプリでユーザーが何を実行するかを理解します。
ms.topic: conceptual
ms.date: 03/25/2019
ms.openlocfilehash: 4ace90b0893eed24227cadcb9a5b9fae7d502ff8
ms.sourcegitcommit: fbb66a827e67440b9d05049decfb434257e56d2d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/05/2020
ms.locfileid: "87800213"
---
# <a name="usage-analysis-with-application-insights"></a>Application Insights による利用状況分析

最も人気のある Web アプリまたはモバイル アプリの機能は何か。 そのアプリによりユーザーは目標を達成したか。 特定の時点でアプリを離れたか。その後、利用を再開したか。  [Azure Application Insights](./app-insights-overview.md) は、ユーザーのアプリの使用方法に関する強力な洞察を得るのに役立ちます。 アプリを更新するたびに、アプリがユーザーにどの程度役立っているかを確認できます。 この知識により、次の開発サイクルに関してデータ駆動型の意思決定を行うことができます。

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE4Cijb]

## <a name="send-telemetry-from-your-app"></a>アプリからテレメトリを送信する

Application Insights をアプリのサーバー コードと Web ページの両方にインストールすることにより、最適な操作環境が得られます。 アプリのクライアントおよびサーバー コンポーネントから Azure Portal に分析用のテレメトリが送信されます。

1. **サーバー コード:** [ASP.NET](./asp-net.md)、[Azure](./app-insights-overview.md)、[Java](./java-get-started.md)、[Node.js](./nodejs.md)、または[その他](./platforms.md)のアプリ向けの適切なモジュールをインストールします。

    * "*サーバー コードをインストールしたくない場合は、[Azure Application Insights リソースの作成](./create-new-resource.md)のみを行ってください。* "

2. **Web ページ コード:** Web ページの閉じる ``</head>`` タグの前に、次のスクリプトを追加します。 インストルメンテーション キーを Application Insights リソースの適切な値に置き換えます。
    
    ```html
    <script type="text/javascript">
    var sdkInstance="appInsightsSDK";window[sdkInstance]="appInsights";var aiName=window[sdkInstance],aisdk=window[aiName]||function(e){function n(e){t[e]=function(){var n=arguments;t.queue.push(function(){t[e].apply(t,n)})}}var t={config:e};t.initialize=!0;var i=document,a=window;setTimeout(function(){var n=i.createElement("script");n.src=e.url||"https://az416426.vo.msecnd.net/scripts/b/ai.2.min.js",i.getElementsByTagName("script")[0].parentNode.appendChild(n)});try{t.cookie=i.cookie}catch(e){}t.queue=[],t.version=2;for(var r=["Event","PageView","Exception","Trace","DependencyData","Metric","PageViewPerformance"];r.length;)n("track"+r.pop());n("startTrackPage"),n("stopTrackPage");var s="Track"+r[0];if(n("start"+s),n("stop"+s),n("setAuthenticatedUserContext"),n("clearAuthenticatedUserContext"),n("flush"),!(!0===e.disableExceptionTracking||e.extensionConfig&&e.extensionConfig.ApplicationInsightsAnalytics&&!0===e.extensionConfig.ApplicationInsightsAnalytics.disableExceptionTracking)){n("_"+(r="onerror"));var o=a[r];a[r]=function(e,n,i,a,s){var c=o&&o(e,n,i,a,s);return!0!==c&&t["_"+r]({message:e,url:n,lineNumber:i,columnNumber:a,error:s}),c},e.autoExceptionInstrumented=!0}return t}(
    {
      instrumentationKey:"INSTRUMENTATION_KEY"
    }
    );window[aiName]=aisdk,aisdk.queue&&0===aisdk.queue.length&&aisdk.trackPageView({});
    </script>
    ```

    Web サイトを監視するためのより高度な構成については、[JavaScript SDK の参照記事](./javascript.md)を参照してください。

3. **モバイル アプリ コード:** [このガイド](../learn/mobile-center-quickstart.md)に従い、App Center SDK を使ってアプリからイベントを収集し、これらのイベントのコピーを分析のために Application Insights に送信します。

4. **テレメトリの取得:** プロジェクトをデバッグ モードで数分間実行し、Application Insights の [概要] ブレードで結果を確認します。

    アプリを発行し、アプリのパフォーマンスを監視してユーザーがアプリを使って何をしているか確認します。

## <a name="include-user-and-session-id-in-your-telemetry"></a>ユーザー ID とセッション ID をテレメトリに含める
Application Insights で一定期間にわたってユーザーを追跡するためには、それらのユーザーを識別する手段が必要となります。 ユーザー ID やセッション ID を必要としない使用状況ツールはイベント ツールだけです。

[このプロセス](./usage-send-user-context.md)を使用してユーザー ID とセッション ID の送信を開始します。

## <a name="explore-usage-demographics-and-statistics"></a>使用状況の人口統計データや統計を調査する
ユーザーがアプリをいつ使い、どのページに最も興味があり、ユーザーがどこにいて、どのようなブラウザーやオペレーティング システムを使っているかを確認しましょう。 

[ユーザー] および [セッション] レポートでは、ページごとまたはカスタム イベントごとにデータをフィルター処理し、場所、環境、ページなどのプロパティでデータをセグメント化できます。 独自のフィルターを追加することもできます。

![ユーザー](./media/usage-overview/users.png)  

右側の洞察では、データのセットで興味深いパターンが示されています。  

* **[ユーザー]** レポートは、選択した期間内にページにアクセスした一意のユーザー数をカウントします。 Web アプリの場合、ユーザーは Cookie を使用してカウントされます。 ユーザーが異なるブラウザーまたはクライアント マシンを使用してサイトにアクセスした場合、または Cookie を消去した場合は、複数回カウントされます。
* **[セッション]** レポートは、サイトにアクセスしたユーザー セッションの数をカウントします。 セッションとは、ユーザーによるアクティビティの期間で、30 分以上の非アクティブ状態で終了します。

[ユーザー、セッション、およびイベント ツールに関する詳細](usage-segmentation.md)  

## <a name="retention---how-many-users-come-back"></a>リテンション期間 - サービスの利用を再開したユーザーの数

リテンション期間では、一定のタイム バケットでビジネス アクションを実行したユーザーのコーホートに基づいて、ユーザーがアプリの利用を再開した頻度を把握できます。 

- どのような特定の機能により、どのような特定のユーザーが使用を再開したかを把握します 
- 実際のユーザー データに基づいて仮説を立てます 
- 製品でリテンション期間が問題になるかどうかを確認します。 

![保持](./media/usage-overview/retention.png) 

上部のリテンション期間コントロールでは、特定のイベントと時間範囲を定義して、リテンション期間を計算できます。 中央のグラフは、指定した時間範囲別のリテンション期間全体のパーセンテージを視覚的に表しています。 下部のグラフは、特定の期間における個々のリテンション期間を表しています。 この詳細レベルでは、ユーザーが何を実行し、どのような理由でユーザーが使用を再開するかをさらに詳しく把握できます。  

[リテンション期間について詳細](usage-retention.md)

## <a name="custom-business-events"></a>カスタム ビジネス イベント

アプリでユーザーが何を行っているかを明確に把握するには、カスタム イベントをログに記録するコード行を挿入すると便利です。 これらのイベントにより、特定のボタンのクリックなどの詳細なユーザー アクションから、購入、ゲームに勝つなどのより重要なビジネス イベントまで追跡できます。 

ページ ビューでは、役立つイベントを表すことができる場合もありますが、通常そうではないことがほとんどです。 ユーザーは、製品を購入しなくても製品ページを開くことができます。 

特定のビジネス イベントを使用して、サイトからユーザーの進行状況をグラフ化できます。 さまざまなオプションについて、ユーザーの嗜好や、どの段階で使用を止め、どのような問題が発生しているかを確認できます。 この知識があれば、開発バックログでの優先順位について情報に基づいた意思決定を行うことができます。

イベントは、アプリのクライアント側からログに記録することができます。

```JavaScript

    appInsights.trackEvent("ExpandDetailTab", {DetailTab: tabName});
```

または、サーバー側から記録することもできます。

```csharp
    var tc = new Microsoft.ApplicationInsights.TelemetryClient();
    tc.TrackEvent("CreatedAccount", new Dictionary<string,string> {"AccountType":account.Type}, null);
    ...
    tc.TrackEvent("AddedItemToCart", new Dictionary<string,string> {"Item":item.Name}, null);
    ...
    tc.TrackEvent("CompletedPurchase");
```

これらのイベントにプロパティ値をアタッチできます。これにより、ポータルでイベントを確認するときに、イベントをフィルター処理または分割できます。 また、匿名ユーザー ID などのプロパティの標準セットが各イベントにアタッチされます。これにより、個人ユーザーのアクティビティのシーケンスを追跡できます。

[カスタム イベント](./api-custom-events-metrics.md#trackevent)と[プロパティ](./api-custom-events-metrics.md#properties)の詳細については、こちらを参照してください。

### <a name="slice-and-dice-events"></a>イベントの詳細な分析

[ユーザー]、[セッション]、および [イベント] ツールでは、ユーザー、イベント名、プロパティごとにカスタム イベントを詳細に分析することができます。
![ユーザー](./media/usage-overview/users.png)  
  
## <a name="design-the-telemetry-with-the-app"></a>アプリでのテレメトリの設計

アプリの各機能を設計するときに、ユーザーの満足度をどのように測定するかを検討します。 記録する必要のあるビジネス イベントを決定し、これらのイベントの追跡呼び出しを一からアプリにコーディングします。

## <a name="a--b-testing"></a>A | B テスト
ある機能のどちらのバージョンが成功するかわからない場合は、その両方をリリースして、それぞれを異なるユーザーが利用できるようにします。 各バージョンの成功の度合いを測定してから、統合したバージョンに移行します。

この手法では、アプリの各バージョンから送信されるすべてのテレメトリに異なるプロパティ値をアタッチします。 これは、アクティブな TelemetryContext のプロパティを定義することで実行できます。 このような既定のプロパティは、カスタム メッセージだけでなく、標準のテレメトリも同様に、アプリケーションから送信されるすべてのテレメトリ メッセージに追加されます。

Application Insights ポータルでは、プロパティ値に基づいてデータをフィルター選択および分割して、異なるバージョンを比較します。

これを行うには、[テレメトリ初期化子を設定](./api-filtering-sampling.md#addmodify-properties-itelemetryinitializer)します。

**ASP.NET アプリ**

```csharp
    // Telemetry initializer class
    public class MyTelemetryInitializer : ITelemetryInitializer
    {
        public void Initialize(ITelemetry item)
            {
                var itemProperties = item as ISupportProperties;
                if (itemProperties != null && !itemProperties.Properties.ContainsKey("AppVersion"))
                {
                    itemProperties.Properties["AppVersion"] = "v2.1";
                }
            }
    }
```

Web アプリ初期化子 (Global.asax.cs など) 内:

```csharp

    protected void Application_Start()
    {
        // ...
        TelemetryConfiguration.Active.TelemetryInitializers
         .Add(new MyTelemetryInitializer());
    }
```

**ASP.NET Core アプリ**

> [!NOTE]
> `ApplicationInsights.config` または `TelemetryConfiguration.Active` を使用して初期化子を追加することは、ASP.NET Core アプリケーションでは無効です。 

[ASP.NET Core](asp-net-core.md#adding-telemetryinitializers) アプリケーションの場合、新しい `TelemetryInitializer` を追加するには、次に示すように Dependency Injection コンテナーに追加します。 これは `Startup.cs` クラスの `ConfigureServices` メソッドで行われます。

```csharp
 using Microsoft.ApplicationInsights.Extensibility;
 using CustomInitializer.Telemetry;
 public void ConfigureServices(IServiceCollection services)
{
    services.AddSingleton<ITelemetryInitializer, MyTelemetryInitializer>();
}
```

すべての新しい TelemetryClients により、指定したプロパティ値が自動的に追加されます。 個々のテレメトリ イベントは、既定値をオーバーライドすることができます。

## <a name="next-steps"></a>次のステップ
   - [ユーザー、セッション、イベント](usage-segmentation.md)
   - [ファネル](usage-funnels.md)
   - [保持](usage-retention.md)
   - [ユーザー フロー](usage-flows.md)
   - [ブック](../platform/workbooks-overview.md)
   - [ユーザー コンテキストの追加](usage-send-user-context.md)

