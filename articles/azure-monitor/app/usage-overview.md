---
title: Azure Application Insights による利用状況分析 | Microsoft docs
description: ユーザーを理解し、提供しているアプリでユーザーが何を実行するかを理解します。
ms.topic: conceptual
ms.date: 03/25/2019
ms.openlocfilehash: d9de1e10363f2100b9dfe557dc12e0be951ce6b8
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102489040"
---
# <a name="usage-analysis-with-application-insights"></a>Application Insights による利用状況分析

最も人気のある Web アプリまたはモバイル アプリの機能は何か。 そのアプリによりユーザーは目標を達成したか。 特定の時点でアプリを離れたか。その後、利用を再開したか。  [Azure Application Insights](./app-insights-overview.md) は、ユーザーのアプリの使用方法に関する強力な洞察を得るのに役立ちます。 アプリを更新するたびに、アプリがユーザーにどの程度役立っているかを確認できます。 この知識により、次の開発サイクルに関してデータ駆動型の意思決定を行うことができます。

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE4Cijb]

## <a name="send-telemetry-from-your-app"></a>アプリからテレメトリを送信する

Application Insights をアプリのサーバー コードと Web ページの両方にインストールすることにより、最適な操作環境が得られます。 アプリのクライアントおよびサーバー コンポーネントから Azure Portal に分析用のテレメトリが送信されます。

1. **サーバー コード:** [ASP.NET](./asp-net.md)、[Azure](./app-insights-overview.md)、[Java](./java-get-started.md)、[Node.js](./nodejs.md)、または [その他](./platforms.md)のアプリ向けの適切なモジュールをインストールします。

    * "*サーバー コードをインストールしたくない場合は、[Azure Application Insights リソースの作成](./create-new-resource.md)のみを行ってください。* "

2. **Web ページ コード:** Web ページの閉じる ``</head>`` タグの前に、次のスクリプトを追加します。 インストルメンテーション キーを Application Insights リソースの適切な値に置き換えます。
    
    現在のスニペット (下に示されています) はバージョン "5" で、バージョンは sv:"#" としてスニペット内でエンコードされています。[現在のバージョンは GitHub で入手することもできます](https://go.microsoft.com/fwlink/?linkid=2156318)。

    ```html
    <script type="text/javascript">
    !function(T,l,y){var S=T.location,k="script",D="instrumentationKey",C="ingestionendpoint",I="disableExceptionTracking",E="ai.device.",b="toLowerCase",w="crossOrigin",N="POST",e="appInsightsSDK",t=y.name||"appInsights";(y.name||T[e])&&(T[e]=t);var n=T[t]||function(d){var g=!1,f=!1,m={initialize:!0,queue:[],sv:"5",version:2,config:d};function v(e,t){var n={},a="Browser";return n[E+"id"]=a[b](),n[E+"type"]=a,n["ai.operation.name"]=S&&S.pathname||"_unknown_",n["ai.internal.sdkVersion"]="javascript:snippet_"+(m.sv||m.version),{time:function(){var e=new Date;function t(e){var t=""+e;return 1===t.length&&(t="0"+t),t}return e.getUTCFullYear()+"-"+t(1+e.getUTCMonth())+"-"+t(e.getUTCDate())+"T"+t(e.getUTCHours())+":"+t(e.getUTCMinutes())+":"+t(e.getUTCSeconds())+"."+((e.getUTCMilliseconds()/1e3).toFixed(3)+"").slice(2,5)+"Z"}(),iKey:e,name:"Microsoft.ApplicationInsights."+e.replace(/-/g,"")+"."+t,sampleRate:100,tags:n,data:{baseData:{ver:2}}}}var h=d.url||y.src;if(h){function a(e){var t,n,a,i,r,o,s,c,u,p,l;g=!0,m.queue=[],f||(f=!0,t=h,s=function(){var e={},t=d.connectionString;if(t)for(var n=t.split(";"),a=0;a<n.length;a++){var i=n[a].split("=");2===i.length&&(e[i[0][b]()]=i[1])}if(!e[C]){var r=e.endpointsuffix,o=r?e.location:null;e[C]="https://"+(o?o+".":"")+"dc."+(r||"services.visualstudio.com")}return e}(),c=s[D]||d[D]||"",u=s[C],p=u?u+"/v2/track":d.endpointUrl,(l=[]).push((n="SDK LOAD Failure: Failed to load Application Insights SDK script (See stack for details)",a=t,i=p,(o=(r=v(c,"Exception")).data).baseType="ExceptionData",o.baseData.exceptions=[{typeName:"SDKLoadFailed",message:n.replace(/\./g,"-"),hasFullStack:!1,stack:n+"\nSnippet failed to load ["+a+"] -- Telemetry is disabled\nHelp Link: https://go.microsoft.com/fwlink/?linkid=2128109\nHost: "+(S&&S.pathname||"_unknown_")+"\nEndpoint: "+i,parsedStack:[]}],r)),l.push(function(e,t,n,a){var i=v(c,"Message"),r=i.data;r.baseType="MessageData";var o=r.baseData;return o.message='AI (Internal): 99 message:"'+("SDK LOAD Failure: Failed to load Application Insights SDK script (See stack for details) ("+n+")").replace(/\"/g,"")+'"',o.properties={endpoint:a},i}(0,0,t,p)),function(e,t){if(JSON){var n=T.fetch;if(n&&!y.useXhr)n(t,{method:N,body:JSON.stringify(e),mode:"cors"});else if(XMLHttpRequest){var a=new XMLHttpRequest;a.open(N,t),a.setRequestHeader("Content-type","application/json"),a.send(JSON.stringify(e))}}}(l,p))}function i(e,t){f||setTimeout(function(){!t&&m.core||a()},500)}var e=function(){var n=l.createElement(k);n.src=h;var e=y[w];return!e&&""!==e||"undefined"==n[w]||(n[w]=e),n.onload=i,n.onerror=a,n.onreadystatechange=function(e,t){"loaded"!==n.readyState&&"complete"!==n.readyState||i(0,t)},n}();y.ld<0?l.getElementsByTagName("head")[0].appendChild(e):setTimeout(function(){l.getElementsByTagName(k)[0].parentNode.appendChild(e)},y.ld||0)}try{m.cookie=l.cookie}catch(p){}function t(e){for(;e.length;)!function(t){m[t]=function(){var e=arguments;g||m.queue.push(function(){m[t].apply(m,e)})}}(e.pop())}var n="track",r="TrackPage",o="TrackEvent";t([n+"Event",n+"PageView",n+"Exception",n+"Trace",n+"DependencyData",n+"Metric",n+"PageViewPerformance","start"+r,"stop"+r,"start"+o,"stop"+o,"addTelemetryInitializer","setAuthenticatedUserContext","clearAuthenticatedUserContext","flush"]),m.SeverityLevel={Verbose:0,Information:1,Warning:2,Error:3,Critical:4};var s=(d.extensionConfig||{}).ApplicationInsightsAnalytics||{};if(!0!==d[I]&&!0!==s[I]){var c="onerror";t(["_"+c]);var u=T[c];T[c]=function(e,t,n,a,i){var r=u&&u(e,t,n,a,i);return!0!==r&&m["_"+c]({message:e,url:t,lineNumber:n,columnNumber:a,error:i}),r},d.autoExceptionInstrumented=!0}return m}(y.cfg);function a(){y.onInit&&y.onInit(n)}(T[t]=n).queue&&0===n.queue.length?(n.queue.push(a),n.trackPageView({})):a()}(window,document,{
    src: "https://js.monitor.azure.com/scripts/b/ai.2.min.js", // The SDK URL Source
    // name: "appInsights", // Global SDK Instance name defaults to "appInsights" when not supplied
    // ld: 0, // Defines the load delay (in ms) before attempting to load the sdk. -1 = block page load and add to head. (default) = 0ms load after timeout,
    // useXhr: 1, // Use XHR instead of fetch to report failures (if available),
    crossOrigin: "anonymous", // When supplied this will add the provided value as the cross origin attribute on the script tag
    // onInit: null, // Once the application insights instance has loaded and initialized this callback function will be called with 1 argument -- the sdk instance (DO NOT ADD anything to the sdk.queue -- As they won't get called)
    cfg: { // Application Insights Configuration
      instrumentationKey:"INSTRUMENTATION_KEY"
    }});
    </script>
    ```

    Web サイトを監視するためのより高度な構成については、[JavaScript SDK の参照記事](./javascript.md)を参照してください。

3. **モバイル アプリ コード:** [このガイド](../app/mobile-center-quickstart.md)に従い、App Center SDK を使ってアプリからイベントを収集し、これらのイベントのコピーを分析のために Application Insights に送信します。

4. **テレメトリの取得:** プロジェクトをデバッグ モードで数分間実行し、Application Insights の [概要] ブレードで結果を確認します。

    アプリを発行し、アプリのパフォーマンスを監視してユーザーがアプリを使って何をしているか確認します。

## <a name="explore-usage-demographics-and-statistics"></a>使用状況の人口統計データや統計を調査する
ユーザーがアプリをいつ使い、どのページに最も興味があり、ユーザーがどこにいて、どのようなブラウザーやオペレーティング システムを使っているかを確認しましょう。 

[ユーザー] および [セッション] レポートでは、ページごとまたはカスタム イベントごとにデータをフィルター処理し、場所、環境、ページなどのプロパティでデータをセグメント化できます。 独自のフィルターを追加することもできます。

![スクリーン キャプチャからは、架空の会社の [ユーザー概要] ページを確認できます。](./media/usage-overview/users.png)  

右側の洞察では、データのセットで興味深いパターンが示されています。  

* **[ユーザー]** レポートは、選択した期間内にページにアクセスした一意のユーザー数をカウントします。 Web アプリの場合、ユーザーは Cookie を使用してカウントされます。 ユーザーが異なるブラウザーまたはクライアント マシンを使用してサイトにアクセスした場合、または Cookie を消去した場合は、複数回カウントされます。
* **[セッション]** レポートは、サイトにアクセスしたユーザー セッションの数をカウントします。 セッションとは、ユーザーによるアクティビティの期間で、30 分以上の非アクティブ状態で終了します。

[ユーザー、セッション、およびイベント ツールに関する詳細](usage-segmentation.md)  

## <a name="retention---how-many-users-come-back"></a>リテンション期間 - サービスの利用を再開したユーザーの数

リテンション期間では、一定のタイム バケットでビジネス アクションを実行したユーザーのコーホートに基づいて、ユーザーがアプリの利用を再開した頻度を把握できます。 

- どのような特定の機能により、どのような特定のユーザーが使用を再開したかを把握します 
- 実際のユーザー データに基づいて仮説を立てます 
- 製品でリテンション期間が問題になるかどうかを確認します。 

![スクリーン キャプチャからは、[リテンション期間の概要] ページを確認できます。ユーザーがアプリの使用を再開する頻度に関する情報が表示されています。](./media/usage-overview/retention.png) 

上部のリテンション期間コントロールでは、特定のイベントと時間範囲を定義して、リテンション期間を計算できます。 中央のグラフは、指定した時間範囲別のリテンション期間全体のパーセンテージを視覚的に表しています。 下部のグラフは、特定の期間における個々のリテンション期間を表しています。 この詳細レベルでは、ユーザーが何を実行し、どのような理由でユーザーが使用を再開するかをさらに詳しく把握できます。  

[リテンション期間について詳細](usage-retention.md)

## <a name="custom-business-events"></a>カスタム ビジネス イベント

アプリでユーザーが何を行っているかを明確に把握するには、カスタム イベントをログに記録するコード行を挿入すると便利です。 これらのイベントにより、特定のボタンのクリックなどの詳細なユーザー アクションから、購入、ゲームに勝つなどのより重要なビジネス イベントまで追跡できます。

[Click Analytics Auto-collection Plugin](javascript-click-analytics-plugin.md) を使用してカスタム イベントを回収することもできます。

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
![スクリーン キャプチャからは、架空の会社の [ユーザー概要] ページを確認できます。](./media/usage-overview/users.png)  
  
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
   - [ブック](../visualize/workbooks-overview.md)
