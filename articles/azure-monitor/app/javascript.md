---
title: JavaScript Web アプリのための Azure Application Insights
description: ページ ビューとセッション数、Web クライアントのデータ、シングル ページ アプリケーション (SPA) を取得し、使用パターンを追跡します。 JavaScript Web ページの例外とパフォーマンスの問題を検出します。
ms.topic: conceptual
ms.date: 08/06/2020
ms.custom: devx-track-js
ms.openlocfilehash: 9b8824a0f73f3a79ab70810c529cb0ed9331a797
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102485488"
---
# <a name="application-insights-for-web-pages"></a>Web ページ向けの Application Insights

Web ページまたはアプリのパフォーマンスと使用状況について調べます。 [Application Insights](app-insights-overview.md) をページ スクリプトに追加すると、ページの読み込みと AJAX 呼び出しのタイミング、ブラウザーの例外や AJAX エラーの数と詳細、ユーザー数とセッション数を取得できます。 いずれの情報も、ページ、クライアントの OS とブラウザー バージョン、geo ロケーションなどのディメンションごとにセグメント化することができます。 エラーの数やページ読み込みの遅延に基づくアラートを設定することもできます。 また、JavaScript コードにトレースの呼び出しを挿入することで、Web ページ アプリケーションのさまざまな機能がどのように使用されているかを追跡できます。

短い JavaScript コードを追加するだけで、Application Insights をあらゆる Web ページで使用できます。 Web サービスが [Java](java-get-started.md) または [ASP.NET](asp-net.md) の場合は、サーバー側 SDK をクライアント側 JavaScript SDK と共に使用して、アプリのパフォーマンスを総合的に理解することができます。

## <a name="adding-the-javascript-sdk"></a>JavaScript SDK を追加する

> [!IMPORTANT]
> 新しい Azure リージョンでは、インストルメンテーション キーの代わりに接続文字列を使用する **必要** があります。 [接続文字列](./sdk-connection-string.md?tabs=js)により、利用統計情報と関連付けるリソースが識別されます。 また、リソースでテレメトリの宛先として使用するエンドポイントを変更することもできます。 接続文字列をコピーし、アプリケーションのコードまたは環境変数に追加する必要があります。

1. まず Application Insights リソースが必要です。 リソースとインストルメンテーション キーがまだない場合は、[新しいリソースの作成手順](create-new-resource.md)に従います。
2. (手順 1. の) JavaScript テレメトリの送信先となるリソースの "_インストルメンテーション キー_" ("iKey" とも呼ばれます) または [接続文字列](#connection-string-setup)をコピーします。これを Application Insights JavaScript SDK の `instrumentationKey` または `connectionString` 設定に追加します。
3. 次の 2 つの方法のいずれかを使用して、Application Insights JavaScript SDK を Web ページまたはアプリに追加します。
    * [npm のセットアップ](#npm-based-setup)
    * [JavaScript スニペット](#snippet-based-setup)

> [!IMPORTANT]
> JavaScript SDK をアプリケーションに追加するには、1 つのメソッドのみを使用します。 npm のセットアップを使用する場合は、スニペットを使用しないでください。その逆の場合も同様です。

> [!NOTE]
> npm セットアップでは、JavaScript SDK がプロジェクトへの依存関係としてインストールされ、IntelliSense が有効になります。一方、スニペットは実行時に SDK を取り込みます。 どちらも同じ機能をサポートしています。 ただし、より多くのカスタムイベントと構成を必要とする開発者は、通常は NPM セットアップを選択します。一方で、すぐに使用できる Web 分析を簡単に有効にしたいユーザーはスニペットを選択します。

### <a name="npm-based-setup"></a>npm ベースのセットアップ

NPM を使用してインストールします。

```sh
npm i --save @microsoft/applicationinsights-web
```

> [!Note]
> **typings はこのパッケージに含まれている** ため、typings パッケージを別途インストールする必要は **ありません**。
    
```js
import { ApplicationInsights } from '@microsoft/applicationinsights-web'

const appInsights = new ApplicationInsights({ config: {
  instrumentationKey: 'YOUR_INSTRUMENTATION_KEY_GOES_HERE'
  /* ...Other Configuration Options... */
} });
appInsights.loadAppInsights();
appInsights.trackPageView(); // Manually call trackPageView to establish the current user/session/pageview
```

### <a name="snippet-based-setup"></a>スニペット ベースのセットアップ

アプリで npm が使用されていない場合は、各ページの上部にこのスニペットを貼り付けることによって、Application Insights で Web ページを直接インストルメント化できます。 可能であれば、これを `<head>` セクションの最初のスクリプトとして指定することで、すべての依存関係に関する潜在的な問題と、必要に応じて JavaScript エラーを監視できます。 Blazor サーバー アプリを使用している場合は、ファイル `_Host.cshtml` の先頭の `<head>` セクションにスニペットを追加します。

アプリケーションで使用されているスニペットのバージョンの追跡を支援するために、バージョン 2.5.5 以降では、識別されたスニペット バージョンを含む "ai.internal.snippet" という新しいタグがページ ビュー イベントに含まれます。

現在のスニペット (下に示されています) はバージョン "5" で、バージョンは sv: "#" としてスニペットでエンコードされています。[現在のバージョンは GitHub で入手することもできます](https://go.microsoft.com/fwlink/?linkid=2156318)。

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
    instrumentationKey: "YOUR_INSTRUMENTATION_KEY_GOES_HERE"
    /* ...Other Configuration Options... */
}});
</script>
```

> [!NOTE]
> 読みやすくするためと、発生する可能性のある JavaScript エラーを減らすために、上記のスニペット コードでは、使用可能なすべての構成オプションが新しい行に示されています。コメント行の値を変更しない場合は、それを削除できます。


#### <a name="reporting-script-load-failures"></a>スクリプトの読み込みエラーのレポート

このバージョンのスニペットでは、CDN から SDK を読み込むときのエラーを検出し、Azure Monitor ポータル ([エラー] &gt; [例外] &gt; [ブラウザー]) に例外としてレポートします。この例外により、この種のエラーが可視化されるので、アプリケーションが想定どおりにテレメトリ (または他の例外) をレポートしていないことがわかるようになります。 このシグナルは、以下の原因となる可能性がある、SDK の読み込みまたは初期化が行われなかったためにテレメトリが失われたことを理解するうえで重要な指標となります。
- ユーザーがサイトをどのように使用しているか (または使用しようとしているか) についての過少レポート
- エンド ユーザーによるサイトの使用状況に関するテレメトリの欠落
- エンド ユーザーがサイトを正常に使用できなくなる可能性のある JavaScript エラーの欠落

この例外の詳細については、[SDK 読み込みエラー](javascript-sdk-load-failure.md)のトラブルシューティングに関するページをご覧ください。

このエラーを例外としてポータルにレポートする場合、Application Insights の構成の ```disableExceptionTracking``` 構成オプションは使用されないため、このエラーが発生した場合、window.onerror のサポートが無効になっていても、スニペットによって常にレポートされます。

SDK 読み込みエラーのレポートは、IE 8 以前では特にサポートされていません。 これにより、ほとんどの環境が IE 8 以前に限定されないことが前提となって、スニペットのサイズ削減が促されます。 この要件があり、これらの例外を受け取りたい場合は、fetch polyfill を含めるか、```XMLHttpRequest``` の代わりに ```XDomainRequest``` を使用する独自のスニペット バージョンを作成する必要があります。[用意されているスニペットのソース コード](https://github.com/microsoft/ApplicationInsights-JS/blob/master/AISKU/snippet/snippet.js)を出発点として使用することをお勧めします。

> [!NOTE]
> 以前のバージョンのスニペットを使用している場合は、これまでレポートされていなかったこれらの問題を受け取ることができるように、最新バージョンに更新することを強くお勧めします。

#### <a name="snippet-configuration-options"></a>スニペットの構成オプション

SDK の読み込みに失敗する原因となるだけでなく、エラーのレポートを無効にする JavaScript エラーが誤って発生するのを防ぐために、すべての構成オプションがスクリプトの最後に移動されました。

上記では、各構成オプションが新しい行に示されています。[省略可能] と示されている項目の既定値を上書きしたくない場合は、その行を削除することで、返されるページのサイズを最小限に抑えることができます。

使用できる構成オプションは次のとおりです。

| 名前 | 種類 | 説明
|------|------|----------------
| src | string **[必須]** | SDK の読み込み元の完全な URL。 この値は、動的に追加される &lt;script /&gt; タグの "src" 属性に使用されます。 パブリック CDN の場所を使用することも、プライベートにホストされている独自の場所を使用することもできます。
| name | string *[省略可能]* | 初期化された SDK のグローバル名。既定値は `appInsights` です。 ```window.appInsights``` は、初期化されたインスタンスへの参照になります。 注: name 値を指定した場合や、(グローバル名 appInsightsSDK によって) 以前のインスタンスが割り当てられているように見える場合は、この name 値もグローバル名前空間で ```window.appInsightsSDK=<name value>``` として定義されます。これは、スニペットの正しいスケルトン メソッドとプロキシ メソッドを確実に初期化および更新できるように、SDK の初期化コードで必要となります。
| ld | ミリ秒単位の数値 *[省略可能]* | SDK の読み込みを試行するまで待機する読み込み遅延を定義します。 既定値は 0ms です。負の値を指定すると、スクリプト タグがページの &lt;head&gt; 領域に即座に追加され、スクリプトが読み込まれる (または失敗する) までページ読み込みイベントがブロックされます。
| useXhr | ブール値 *[省略可能]* | この設定は、SDK 読み込みエラーのレポートにのみ使用されます。 レポートでは、まず fetch() の使用を試みてから (使用可能な場合)、XHR にフォールバックします。この値を true に設定すると、フェッチ チェックがバイパスされます。 この値を使用する必要があるのは、フェッチが失敗したときにエラー イベントを送信する環境でアプリケーションが使用されている場合だけです。
| crossOrigin | string *[省略可能]* | この設定を含めると、SDK をダウンロードするために追加されたスクリプト タグに、この文字列値が設定された crossOrigin 属性が含まれるようになります。 定義されていない場合 (既定値)、crossOrigin 属性は追加されません。 推奨値は未定義 (既定値) を表す ""、または "anonymous" です (すべての有効な値については、[HTML 属性: `crossorigin`](https://developer.mozilla.org/en-US/docs/Web/HTML/Attributes/crossorigin) のドキュメントをご覧ください)。
| cfg | object **[必須]** | 初期化中に Application Insights SDK に渡される構成。

### <a name="connection-string-setup"></a>接続文字列の設定

NPM またはスニペットのセットアップでは、接続文字列を使用して Application Insights のインスタンスを構成することもできます。 単に `instrumentationKey` フィールドを `connectionString` フィールドに置き換えるだけです。
```js
import { ApplicationInsights } from '@microsoft/applicationinsights-web'

const appInsights = new ApplicationInsights({ config: {
  connectionString: 'YOUR_CONNECTION_STRING_GOES_HERE'
  /* ...Other Configuration Options... */
} });
appInsights.loadAppInsights();
appInsights.trackPageView();
```

### <a name="sending-telemetry-to-the-azure-portal"></a>テレメトリを Azure portal に送信する

既定では Application Insights JavaScript SDK は、アプリケーションの正常性や基になるユーザー エクスペリエンスを判別するのに役立つ多くのテレメトリ項目を自動収集します。 これには以下が含まれます。

- アプリでの **キャッチされない例外** (以下の情報が含まれる)
    - スタック トレース
    - 例外の詳細とエラーに付随するメッセージ
    - エラーの行番号と列番号
    - エラーが発生した URL
- アプリの **XHR** および **Fetch** (フェッチ コレクションは既定では無効) 要求によって実行される **ネットワーク依存関係要求** (以下の情報が含まれる)
    - 依存関係ソースの URL
    - 依存関係の要求に使用されたコマンドとメソッド
    - 要求の期間
    - 要求の結果コードと成功状態
    - 要求を行うユーザーの ID (存在する場合)
    - 要求が行われた相関関係コンテキスト (存在する場合)
- **ユーザー情報** (場所、ネットワーク、IP など)
- **デバイス情報** (ブラウザー、OS、バージョン、言語、モデルなど)
- **セッション情報**

### <a name="telemetry-initializers"></a>テレメトリ初期化子
テレメトリ初期化子は、収集されたテレメトリの内容を変更してからユーザーのブラウザーに送信するために使用されます。 また、`false` を返すことで、特定のテレメトリの送信を防ぐためにも使用されます。 複数のテレメトリ初期化子を Application Insights インスタンスに追加することができ、それらは追加した順序で実行されます。

`addTelemetryInitializer` に対する入力引数は、[`ITelemetryItem`](https://github.com/microsoft/ApplicationInsights-JS/blob/master/API-reference.md#addTelemetryInitializer) を引数として受け取り、`boolean` または `void` を返すコールバックです。 `false` を返す場合、テレメトリ項目は送信されません。それ以外の場合は次のテレメトリ初期化子 (ある場合) に進むか、テレメトリ コレクション エンドポイントに送信されます。

テレメトリ初期化子の使用例を次に示します。
```ts
var telemetryInitializer = (envelope) => {
  envelope.data.someField = 'This item passed through my telemetry initializer';
};
appInsights.addTelemetryInitializer(telemetryInitializer);
appInsights.trackTrace({message: 'This message will use a telemetry initializer'});

appInsights.addTelemetryInitializer(() => false); // Nothing is sent after this is executed
appInsights.trackTrace({message: 'this message will not be sent'}); // Not sent
```

## <a name="configuration"></a>構成
ほとんどの構成フィールドは、既定値を false にできるように指定されています。 `instrumentationKey`以外のすべてのフィールドは省略可能です。

| 名前 | Default | 説明 |
|------|---------|-------------|
| instrumentationKey | null | **必須**<br>Azure portal で入手したインストルメンテーション キー |
| accountId | null | 省略可能なアカウント ID (アプリによってユーザーがアカウントにグループ化される場合)。 スペース、コンマ、セミコロン、等号、または縦棒は使用できません。 |
| sessionRenewalMs | 1800000 | この時間 (ミリ秒単位) にわたってユーザーが非アクティブである場合に、セッションがログに記録されます。 既定値は 30 分です。 |
| sessionExpirationMs | 86400000 | セッションがこの時間 (ミリ秒単位) にわたって継続する場合に、セッションがログに記録されます。 既定値は 24 時間です。 |
| maxBatchSizeInBytes | 10000 | テレメトリ バッチの最大サイズ。 バッチがこの制限を超えると、すぐに送信され、新しいバッチが開始されます。 |
| maxBatchInterval | 15000 | 送信前にテレメトリをバッチ処理する時間 (ミリ秒) |
| disableExceptionTracking | false | true の場合、例外は自動収集されません。 既定値は false です。 |
| disableTelemetry | false | true の場合、テレメトリは収集または送信されません。 既定値は false です。 |
| enableDebug | false | true の場合、SDK ログ設定に関わらず、**内部** デバッグ データはログに記録される **代わりに** 例外としてスローされます。 既定値は false です。 <br>**_注:_** この設定を有効にすると、内部エラーが発生するたびにテレメトリが削除されます。 これは、SDK の構成または使用に関する問題をすばやく特定するのに役立ちます。 デバッグ時にテレメトリが削除されないようにするには、`enableDebug` の代わりに `consoleLoggingLevel` または `telemetryLoggingLevel` の使用を検討してください。 |
| loggingLevelConsole | 0 | **内部** Application Insights エラーをコンソールに記録します。 <br>0: オフ <br>1:重大なエラーのみ <br>2:すべて (エラーおよび警告) |
| loggingLevelTelemetry | 1 | **内部** Application Insights エラーをテレメトリとして送信します。 <br>0: オフ <br>1:重大なエラーのみ <br>2:すべて (エラーおよび警告) |
| diagnosticLogInterval | 10000 | 内部ログ キューの (内部) ポーリング間隔 (ミリ秒) |
| samplingPercentage | 100 | 送信されるイベントの割合。 既定値は 100 で、すべてのイベントが送信されます。 大規模なアプリケーションでデータ上限を維持する場合は、これを設定します。 |
| autoTrackPageVisitTime | false | true の場合、ページビューに関して、前にインストルメント化されたページのビュー時間が追跡されてテレメトリとして送信されます。また、現在のページビューについて新しいタイマーが開始されます。 既定値は false です。 |
| disableAjaxTracking | false | true の場合、Ajax 呼び出しは自動収集されません。 既定値は false です。 |
| disableFetchTracking | true | true の場合、フェッチ要求は自動収集されません。 既定値は true です。 |
| overridePageViewDuration | false | true の場合、trackPageView の既定の動作が変わり、trackPageView の呼び出し時にページビュー期間の終了を記録します。 false の場合に、trackPageView にカスタム期間が指定されていないと、Navigation Timing API を使用してページ ビューのパフォーマンスが計算されます。 既定値は false です。 |
| maxAjaxCallsPerView | 500 | 既定値: 500 - ページ ビュー 1 回あたりの Ajax 呼び出し数を監視し制御します。 -1 に設定すると、ページで発行されたすべて (無制限) の Ajax 呼び出しを監視します。 |
| disableDataLossAnalysis | true | false の場合、まだ送信されていない項目について、内部テレメトリ センダー バッファーがスタートアップ時にチェックされます。 |
| disableCorrelationHeaders | false | false の場合、SDK によって 2 つのヘッダー (Request-Id と Request-Context) がすべての依存関係要求に追加され、サーバー側の対応する要求と関連付けられます。 既定値は false です。 |
| correlationHeaderExcludedDomains |  | 特定のドメインの関連付けヘッダーを無効にします。 |
| correlationHeaderDomains |  | 特定のドメインの関連付けヘッダーを有効にします。 |
| disableFlushOnBeforeUnload | false | 既定値は false です。 true の場合、onBeforeUnload イベントによってトリガーされても、flush メソッドは呼び出されません。 |
| enableSessionStorageBuffer | true | 既定値は true です。 true の場合、未送信のすべてのテレメトリを含むバッファーがセッション ストレージに格納されます。 バッファーはページの読み込み時に復元されます。 |
| isCookieUseDisabled | false | 既定値は false です。 true の場合、SDK によって cookie に対するデータの格納や読み取りは行われません。 これにより、ユーザーとセッションの Cookie が無効になり、使用状況ブレードとエクスペリエンスが利用できなくなります。 |
| cookieDomain | null | カスタム Cookie ドメイン。 これは、サブドメイン間で Application Insights Cookie を共有する場合に便利です。 |
| isRetryDisabled | false | 既定値は false です。 false の場合、206 (部分的な成功)、408 (タイムアウト)、429 (要求が多すぎる)、500 (内部サーバー エラー)、503 (サービス利用不可)、および 0 (オフライン、検出された場合のみ) で再試行します。 |
| isStorageUseDisabled | false | true の場合、SDK によってローカルおよびセッション ストレージに対するデータの格納や読み取りは行われません。 既定値は false です。 |
| isBeaconApiDisabled | true | false の場合、SDK が [Beacon API](https://www.w3.org/TR/beacon) を使用してすべてのテレメトリが送信されます。 |
| onunloadDisableBeacon | false | 既定値は false です。 タブが閉じられると、SDK により [Beacon API](https://www.w3.org/TR/beacon) を使用してすべてのテレメトリが送信されます。 |
| sdkExtension | null | sdk 拡張機能の名前を設定します。 英字のみを使用できます。 拡張機能名はプレフィックスとして ai.internal.sdkVersion タグに付けられます (ext_javascript:2.0.0 など)。 既定値は Null です。 |
| isBrowserLinkTrackingEnabled | false | 既定値は false です。 true の場合、SDK によってすべての[ブラウザー リンク](/aspnet/core/client-side/using-browserlink)要求が追跡されます。 |
| appId | null | appId は、サーバー側の要求によってクライアント側で発生する AJAX 依存関係の相関関係のために使用されます。 Beacon API が有効になっているとき、これを自動的に使用することはできませんが、構成で手動で設定できます。 既定値は null です。 |
| enableCorsCorrelation | false | truee の場合、SDK によって 2 つのヘッダー (Request-Id と Request-Context) がすべての CORS 要求に追加され、送信される AJAX 依存関係がサーバー側の対応する要求と関連付けられます。 既定値は false です。 |
| namePrefix | undefined | localStorage および Cookie 名の接尾語として使用される省略可能な値。
| enableAutoRouteTracking | false | シングル ページ アプリケーション (SPA) でのルート変更を自動的に追跡します。 true の場合、ルートの変更ごとに Application Insights に新しいページビューが送信されます。 ハッシュ ルート変更 (`example.com/foo#bar`) も新しいページ ビューとして記録されます。
| enableRequestHeaderTracking | false | true の場合、AJAX と Fetch の要求ヘッダーが追跡されます。既定値は false です。
| enableResponseHeaderTracking | false | true の場合、AJAX と Fetch の要求の応答ヘッダーが追跡されます。既定値は false です。
| distributedTracingMode | `DistributedTracingModes.AI` | 分散トレース モードを設定します。 AI_AND_W3C モードまたは W3C モードが設定されている場合、W3C トレース コンテキスト ヘッダー (traceparent/traceparent) が生成され、送信されるすべての要求に組み込まれます。 AI_AND_W3C は、従来の Application Insights のインストルメント化されたサービスとの下位互換性を保つために用意されています。 [こちら](./correlation.md#enable-w3c-distributed-tracing-support-for-web-apps)の例を参照してください。
| enableAjaxErrorStatusText | false | 既定値は false です。 true の場合、失敗した AJAX 要求の依存関係イベントに応答エラー データ テキストを含めます。
| enableAjaxPerfTracking | false | 既定値は false です。 ブラウザーの window.performance の追加のタイミングを検索し、レポートされる `ajax` (XHR および fetch) のレポートされるメトリックに含めることを可能にするフラグを設定します。
| maxAjaxPerfLookupAttempts | 3 | 既定値は 3 です。 window.performance のタイミング (使用可能な場合) を検索する最大回数。すべてのブラウザーが、XHR 要求の終了をレポートする前に window.performance を設定するわけではないため、これは必須です。fetch 要求の場合、これは要求の完了後に追加されます。
| ajaxPerfLookupDelay | 25 | 既定値は 25 ミリ秒です。 `ajax` 要求で windows.performance のタイミングの検索を再試行するまでの待ち時間。時間はミリ秒単位であり、setTimeout() に直接渡されます。
| enableUnhandledPromiseRejectionTracking | false | true の場合、未処理の Promise 拒否が自動収集され、JavaScript エラーとしてレポートされます。 disableExceptionTracking が true (例外を追跡しない) の場合、この構成値は無視され、未処理の Promise 拒否はレポートされません。

## <a name="enable-time-on-page-tracking"></a>ページ滞在時間の追跡を有効にする

`autoTrackPageVisitTime: true` を設定することで、ユーザーが各ページで費やした時間が追跡されます。 新しい PageView のたびに、"*前の*" ページでユーザーが費やした時間が `PageVisitTime` という名前の [カスタム メトリック](../essentials/metrics-custom-overview.md)に送信されます。 このカスタム メトリックは、"ログベースのメトリック" として[メトリックス エクスプローラー](../essentials/metrics-getting-started.md)に表示されます。

## <a name="enable-correlation"></a>関連付けを有効にする

関連付けにより、分散トレースを有効にするデータが生成および送信され、[アプリケーション マップ](../app/app-map.md)、[エンド ツー エンド トランザクション ビュー](../app/app-map.md#go-to-details)、その他の診断ツールが強化されます。

次の例は、関連付けを有効にするために必要なすべての構成を示しており、それに続いてシナリオ固有の注意事項があります。

```javascript
// excerpt of the config section of the JavaScript SDK snippet with correlation
// between client-side AJAX and server requests enabled.
cfg: { // Application Insights Configuration
    instrumentationKey: "YOUR_INSTRUMENTATION_KEY_GOES_HERE"
    disableFetchTracking: false,
    enableCorsCorrelation: true,
    enableRequestHeaderTracking: true,
    enableResponseHeaderTracking: true,
    correlationHeaderExcludedDomains: ['myapp.azurewebsites.net', '*.queue.core.windows.net']
    /* ...Other Configuration Options... */
}});
</script>

``` 

クライアントが通信するサードパーティのサーバーが `Request-Id` と `Request-Context` ヘッダーを受け入れられず、構成を更新できない場合は、`correlationHeaderExcludeDomains` 構成プロパティを使用してそれらを除外リストに入れる必要があります。 このプロパティでは、ワイルドカードがサポートされています。

サーバー側は、これらのヘッダーが存在する接続を受け入れる必要があります。 サーバー側の `Access-Control-Allow-Headers` 構成によっては、`Request-Id` と `Request-Context` を手動で追加してサーバー側の一覧を拡張することが必要になることがよくあります。

Access-Control-Allow-Headers: `Request-Id`、`Request-Context`、`<your header>`

> [!NOTE]
> 2020 年以降にリリースされた OpenTelemtry または Application Insights SDK を使用している場合は、[WC3 TraceContext](https://www.w3.org/TR/trace-context/) を使用することをお勧めします。 [こちら](../app/correlation.md#enable-w3c-distributed-tracing-support-for-web-apps)の構成ガイダンスを参照してください。

## <a name="single-page-applications"></a>シングル ページ アプリケーション

既定では、この SDK では、シングル ページ アプリケーションで発生する状態ベースのルート変更は処理され **ません**。 シングル ページ アプリケーションの自動ルート変更追跡を有効にするには、`enableAutoRouteTracking: true` をセットアップ構成に追加します。

現在、この SDK で初期化できる個別の [React プラグイン](javascript-react-plugin.md)が提供されています。 これによって、ルート変更追跡が実現し、他の React 固有テレメトリの収集も行われます。
> [!NOTE]
> `enableAutoRouteTracking: true` は、React プラグインを使用して **いない** 場合にのみ使用します。 いずれも、ルートの変更時、新しい PageViews を送信できます。 両方が有効になっている場合、PageViews が重複して送信されることがあります。

## <a name="extensions"></a>拡張機能

| 拡張機能 |
|---------------|
| [React](javascript-react-plugin.md)|
| [React Native](javascript-react-native-plugin.md)|
| [Angular](javascript-angular-plugin.md)|
| [クリック分析自動収集](javascript-click-analytics-plugin.md)|

## <a name="explore-browserclient-side-data"></a>ブラウザー/クライアント側データの参照

ブラウザー/クライアント側データを表示するには、 **[メトリック]** に移動して、興味がある個別のメトリックを追加します。

![Web アプリケーションのメトリック データのグラフィック表示を示す、Application Insights の [メトリック] ページのスクリーンショット。](./media/javascript/page-view-load-time.png)

また、JavaScript SDK のデータもポータルのブラウザー エクスペリエンスを使用して表示できます。

**[ブラウザー]** を選択してから、 **[エラー]** または **[パフォーマンス]** を選択します。

![Web アプリケーションの表示できるメトリックにブラウザーのエラーまたはブラウザーのパフォーマンスを追加する方法を示す、Application Insights の [ブラウザー] ページのスクリーンショット。](./media/javascript/browser.png)

### <a name="performance"></a>パフォーマンス

![Web アプリケーションの操作メトリックのグラフィック表示を示す、Application Insights の [パフォーマンス] ページのスクリーンショット。](./media/javascript/performance-operations.png)

### <a name="dependencies"></a>依存関係

![Web アプリケーションの依存関係メトリックのグラフィック表示を示す、Application Insights の [パフォーマンス] ページのスクリーンショット。](./media/javascript/performance-dependencies.png)

### <a name="analytics"></a>Analytics

JavaScript SDK によって収集されたテレメトリに対してクエリを実行するには、 **[ログに表示 (Analytics)]** ボタンを選択します。 `client_Type == "Browser"`の `where` ステートメントを追加すると、JavaScript SDK のデータのみが表示され、他の SDK によって収集されるサーバー側のテレメトリがすべて除外されます。
 
```kusto
// average pageView duration by name
let timeGrain=5m;
let dataset=pageViews
// additional filters can be applied here
| where timestamp > ago(1d)
| where client_Type == "Browser" ;
// calculate average pageView duration for all pageViews
dataset
| summarize avg(duration) by bin(timestamp, timeGrain)
| extend pageView='Overall'
// render result in a chart
| render timechart
```

### <a name="source-map-support"></a>ソース マップのサポート

例外テレメトリのミニファイされたコール スタックは、Azure portal でアンミニファイすることができます。 [例外の詳細] パネルの既存の統合はすべて、新たにアンミニファイされるコール スタックに対して動作します。

#### <a name="link-to-blob-storage-account"></a>Azure BLOB ストレージ アカウントにリンクする

Application Insights リソースを独自の Azure Blob Storage コンテナーに リンクして、コール スタックを自動的にアンミニファイすることができます。 開始するには、[ソース マップの自動サポート](./source-map-support.md)に関する記事を参照してください。

### <a name="drag-and-drop"></a>ドラッグ アンド ドロップ

1. Azure portal で例外テレメトリ項目を選択し、[エンド ツー エンド トランザクションの詳細] を表示します。
2. このコール スタックにどのソース マップが対応しているかを識別します。 ソース マップは、スタック フレームのソース ファイルと同じ名前であることが必要です。接尾辞は `.map` です。
3. Azure portal で呼び出し履歴にソース マップをドラッグ アンド ドロップします ![Azure portal でソース マップ ファイルをビルド フォルダーから [呼び出し履歴] ウィンドウにドラッグ アンド ドロップする方法を示す、アニメーション化された画像。](https://i.imgur.com/Efue9nU.gif)

### <a name="application-insights-web-basic"></a>Application Insights Web Basic

軽量エクスペリエンスのためには、代わりに Application Insights の基本バージョンをインストールすることもできます。
```
npm i --save @microsoft/applicationinsights-web-basic
```
このバージョンは最小限の機能を備え、必要に応じて発展させるかどうかはユーザー次第です。 たとえば、自動収集 (キャッチされていない例外、AJAX など) は行われません。 `trackTrace` や `trackException` など特定の種類のテレメトリを送信するための API はこのバージョンには含まれないため、独自にラッパーを用意する必要があります。 使用できる唯一の API は `track` です。 [サンプル](https://github.com/Azure-Samples/applicationinsights-web-sample1/blob/master/testlightsku.html)については、こちらを参照してください。

## <a name="examples"></a>例

実行できる例については、[Application Insights JavaScript SDK サンプル](https://github.com/Azure-Samples?q=applicationinsights-js-demo)を参照してください。

## <a name="upgrading-from-the-old-version-of-application-insights"></a>以前のバージョンの Application Insights からのアップグレード

SDK V2 バージョンでの破壊的変更:
- API 署名の向上のため、trackPageView や trackException などの一部の API 呼び出しが更新されています。 Internet Explorer 8 以前のバージョンのブラウザーでの実行はサポートされません。
- データ スキーマの更新により、テレメトリ エンベロープのフィールド名と構造が変更されています。
- `context.operation` が `context.telemetryTrace` に移動されました。 一部のフィールドも変更されました (`operation.id` --> `telemetryTrace.traceID`)。
  - (たとえば、SPA アプリで) 現在のページビュー ID を手動で更新するには、`appInsights.properties.context.telemetryTrace.traceID = Microsoft.ApplicationInsights.Telemetry.Util.generateW3CId()` を使用します。
    > [!NOTE]
    > トレース ID を一意に保つには、以前に `Util.newId()` を使用した場所で今度は `Util.generateW3CId()` を使用します。 両方とも、最終的には操作 ID になります。

現在の Application Insights PRODUCTION SDK (1.0.20) を使用しており、新しい SDK がランタイムで動作するかどうかを確認する場合は、現在の SDK 読み込みシナリオに応じて URL を更新します。

- CDN を使用したダウンロードのシナリオ:現在使用しているコード スニペットが次の URL を指すように更新します。
   ```
   "https://js.monitor.azure.com/scripts/b/ai.2.min.js"
   ```

- npm のシナリオ:`downloadAndSetup` を呼び出して、完全な ApplicationInsights スクリプトを CDN からダウンロードし、インストルメンテーション キーを使用して初期化します。

   ```ts
   appInsights.downloadAndSetup({
     instrumentationKey: "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxx",
     url: "https://js.monitor.azure.com/scripts/b/ai.2.min.jss"
     });
   ```

内部環境でテストして、監視テレメトリが想定どおりに動作していることを確認します。 すべて動作している場合は、API 署名を SDK V2 バージョンに適切に更新し、実稼働環境にデプロイします。

## <a name="sdk-performanceoverhead"></a>SDK のパフォーマンス/オーバーヘッド

Application Insights は、ちょうど 36 KB に gzip 圧縮され、初期化に約 15 ミリ秒しかかからないため、Web サイトの読み込み時間にほとんど影響はありません。 スニペットを使用すると、ライブラリの最小コンポーネントがすばやく読み込まれます。 その間に、スクリプト全体がバックグラウンドでダウンロードされます。

スクリプトが CDN からダウンロードされている間に、ページのすべての追跡がキューに登録されます。 ダウンロードしたスクリプトの初期化が非同期で完了すると、キューに登録されたすべてのイベントが追跡されます。 この結果、ページのライフ サイクル全体でテレメトリが失われることはありません。 このセットアップ プロセスでは、ユーザーには見えないシームレスな分析システムを使用してページが提供されます。

> 概要:
> - ![npm バージョン](https://badge.fury.io/js/%40microsoft%2Fapplicationinsights-web.svg)
> - ![gzip 圧縮サイズ](https://img.badgesize.io/https://js.monitor.azure.com/scripts/b/ai.2.min.js.svg?compression=gzip)
> - **15 ms**: 全体の初期化時間
> - **ゼロ**: ページのライフ サイクルで失われる追跡

## <a name="browser-support"></a>ブラウザーのサポート

![Chrome](https://raw.githubusercontent.com/alrra/browser-logos/master/src/chrome/chrome_48x48.png) | ![Firefox](https://raw.githubusercontent.com/alrra/browser-logos/master/src/firefox/firefox_48x48.png) | ![IE](https://raw.githubusercontent.com/alrra/browser-logos/master/src/edge/edge_48x48.png) | ![Opera](https://raw.githubusercontent.com/alrra/browser-logos/master/src/opera/opera_48x48.png) | ![Safari](https://raw.githubusercontent.com/alrra/browser-logos/master/src/safari/safari_48x48.png)
--- | --- | --- | --- | --- |
Chrome 最新バージョン ✔ |  Firefox 最新バージョン ✔ | IE 9+ & Edge ✔<br>IE 8 互換 | Opera 最新バージョン ✔ | Safari 最新バージョン ✔ |

## <a name="es3ie8-compatibility"></a>ES3 および IE8 の互換性

SDK として、顧客が使用するブラウザーを制御できない多数のユーザーがいます。 そのため、古いブラウザーで読み込まれたときに、この SDK が引き続き "動作" し、JS の実行が中断されないようにする必要があります。 IE8 および古い世代 (ES3) のブラウザーをサポートしないことが理想的ですが、ページが "動作する" ことを引き続き必要としている多数の顧客やユーザーがいます。前述のように、そういった方はエンド ユーザーが選択するブラウザーを制御できる場合とできない場合があります。

これは、最も一般的ではない機能セットだけをサポートすることを意味するわけではありません。ES3 コードの互換性を維持する必要があり、新しい機能を追加するときは、ES3 JavaScript 解析を中断させない方法で、オプション機能として追加する必要があるということです。

[IE8 のサポートの詳細については、GitHub を参照してください](https://github.com/Microsoft/ApplicationInsights-JS#es3ie8-compatibility)

## <a name="open-source-sdk"></a>オープンソース SDK

Application Insights JavaScript SDK はオープンソースです。ソース コードを表示したり、プロジェクトに参加したりするには、[GitHub の公式リポジトリ](https://github.com/Microsoft/ApplicationInsights-JS)にアクセスしてください。 

最新の更新プログラムとバグ修正については、[リリース ノートを参照してください](./release-notes.md)。

## <a name="next-steps"></a><a name="next"></a> 次のステップ
* [利用状況を追跡する](usage-overview.md)
* [カスタム イベントとメトリックス](api-custom-events-metrics.md)
* [ビルド - 測定 - 学習](usage-overview.md)
* [SDK 読み込みエラーのトラブルシューティング](javascript-sdk-load-failure.md)
