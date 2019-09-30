---
title: JavaScript Web アプリのための Azure Application Insights | Microsoft Docs
description: ページ ビューとセッション数、Web クライアントのデータを取得し、使用パターンを追跡します。 JavaScript Web ページの例外とパフォーマンスの問題を検出します。
services: application-insights
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.assetid: 3b710d09-6ab4-4004-b26a-4fa840039500
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 09/12/2019
ms.author: mbullwin
ms.openlocfilehash: f3b093b8d5f772bad759d3384405f4ca9f0cee15
ms.sourcegitcommit: f3f4ec75b74124c2b4e827c29b49ae6b94adbbb7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/12/2019
ms.locfileid: "70933768"
---
# <a name="application-insights-for-web-pages"></a>Web ページ向けの Application Insights

Web ページまたはアプリのパフォーマンスと使用状況について調べます。 [Application Insights](app-insights-overview.md) をページ スクリプトに追加すると、ページの読み込みと AJAX 呼び出しのタイミング、ブラウザーの例外や AJAX エラーの数と詳細、ユーザー数とセッション数を取得できます。 いずれの情報も、ページ、クライアントの OS とブラウザー バージョン、geo ロケーションなどのディメンションごとにセグメント化することができます。 エラーの数やページ読み込みの遅延に基づくアラートを設定することもできます。 また、JavaScript コードにトレースの呼び出しを挿入することで、Web ページ アプリケーションのさまざまな機能がどのように使用されているかを追跡できます。

短い JavaScript コードを追加するだけで、Application Insights をあらゆる Web ページで使用できます。 Web サービスが [Java](java-get-started.md) または [ASP.NET](asp-net.md) の場合は、サーバー側 SDK をクライアント側 JavaScript SDK と共に使用して、アプリのパフォーマンスを総合的に理解することができます。

## <a name="adding-the-javascript-sdk"></a>Javascript SDK を追加する

1. まず Application Insights リソースが必要です。 リソースとインストルメンテーション キーがまだない場合は、[新しいリソースの作成手順](create-new-resource.md)に従います。
2. JavaScript テレメトリの送信先となるリソースからインストルメンテーション キーをコピーします。
3. 次の 2 つの方法のいずれかを使用して、Application Insights JavaScript SDK を Web ページまたはアプリに追加します。
    * [NPM セットアップ](#npm-based-setup)
    * [JavaScript スニペット](#snippet-based-setup)

> [!IMPORTANT]
> Application Insights JavaScript SDK をアプリケーションに追加するには、次のいずれか 1 つの方法のみを使用してください。 NPM ベースのセットアップを使用する場合は、スニペット ベースのセットアップを使用しないでください。 逆にスニペット ベースのアプローチを使用するシナリオでも同じことが言えます。NPM ベースのセットアップを使用しないでください。 

### <a name="npm-based-setup"></a>NPM ベースのセットアップ

```js
import { ApplicationInsights } from '@microsoft/applicationinsights-web'

const appInsights = new ApplicationInsights({ config: {
  instrumentationKey: 'YOUR_INSTRUMENTATION_KEY_GOES_HERE'
  /* ...Other Configuration Options... */
} });
appInsights.loadAppInsights();
```

### <a name="snippet-based-setup"></a>スニペット ベースのセットアップ

アプリで NPM が使用されない場合は、各ページの上部にこのスニペットを貼り付けることによって、Application Insights で Web ページを直接インストルメント化できます。 可能であれば、これを `<head>` セクションの最初のスクリプトとして指定すると、すべての依存関係に関する潜在的な問題を監視することができます。

```html
<script type="text/javascript">
var sdkInstance="appInsightsSDK";window[sdkInstance]="appInsights";var aiName=window[sdkInstance],aisdk=window[aiName]||function(e){function n(e){t[e]=function(){var n=arguments;t.queue.push(function(){t[e].apply(t,n)})}}var t={config:e};t.initialize=!0;var i=document,a=window;setTimeout(function(){var n=i.createElement("script");n.src=e.url||"https://az416426.vo.msecnd.net/scripts/b/ai.2.min.js",i.getElementsByTagName("script")[0].parentNode.appendChild(n)});try{t.cookie=i.cookie}catch(e){}t.queue=[],t.version=2;for(var r=["Event","PageView","Exception","Trace","DependencyData","Metric","PageViewPerformance"];r.length;)n("track"+r.pop());n("startTrackPage"),n("stopTrackPage");var s="Track"+r[0];if(n("start"+s),n("stop"+s),n("setAuthenticatedUserContext"),n("clearAuthenticatedUserContext"),n("flush"),!(!0===e.disableExceptionTracking||e.extensionConfig&&e.extensionConfig.ApplicationInsightsAnalytics&&!0===e.extensionConfig.ApplicationInsightsAnalytics.disableExceptionTracking)){n("_"+(r="onerror"));var o=a[r];a[r]=function(e,n,i,a,s){var c=o&&o(e,n,i,a,s);return!0!==c&&t["_"+r]({message:e,url:n,lineNumber:i,columnNumber:a,error:s}),c},e.autoExceptionInstrumented=!0}return t}(
{
  instrumentationKey:"INSTRUMENTATION_KEY"
}
);window[aiName]=aisdk,aisdk.queue&&0===aisdk.queue.length&&aisdk.trackPageView({});
</script>
```

### <a name="sending-telemetry-to-the-azure-portal"></a>テレメトリを Azure portal に送信する

既定では Application Insights JavaScript SDK は、アプリケーションの正常性や基になるユーザー エクスペリエンスを判別するのに役立つ多くのテレメトリ項目を自動収集します。 チェックの内容は次のとおりです

- アプリでの**キャッチされない例外** (以下の情報が含まれる)
    - スタック トレース
    - 例外の詳細とエラーに付随するメッセージ
    - エラーの行番号と列番号
    - エラーが発生した URL
- アプリの **XHR** および **Fetch** (フェッチ コレクションは既定では無効) 要求によって実行される**ネットワーク依存関係要求** (以下の情報が含まれる)
    - 依存関係ソースの URL
    - 依存関係の要求に使用されたコマンドとメソッド
    - 要求の期間
    - 要求の結果コードと成功状態
    - 要求を行うユーザーの ID (存在する場合)
    - 要求が行われた相関関係コンテキスト (存在する場合)
- **ユーザー情報** (場所、ネットワーク、IP など)
- **デバイス情報** (ブラウザー、OS、バージョン、言語、解像度、モデルなど)
- **セッション情報**

### <a name="telemetry-initializers"></a>テレメトリ初期化子
テレメトリ初期化子は、収集されたテレメトリの内容を変更してからユーザーのブラウザーに送信するために使用されます。 また、`false` を返すことで、特定のテレメトリの送信を防ぐためにも使用されます。 複数のテレメトリ初期化子を Application Insights インスタンスに追加することができ、それらは追加した順序で実行されます。

`addTelemetryInitializer` に対する入力引数は、[`ITelemetryItem`](https://github.com/microsoft/ApplicationInsights-JS/blob/master/API.md#addTelemetryInitializer) を引数として受け取り、`boolean` または `void` を返すコールバックです。 `false` を返す場合、テレメトリ項目は送信されません。それ以外の場合は次のテレメトリ初期化子 (ある場合) に進むか、テレメトリ コレクション エンドポイントに送信されます。

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
| enableDebug | false | true の場合、SDK ログ設定に関わらず、**内部**デバッグ データはログに記録される**代わりに**例外としてスローされます。 既定値は false です。 <br>***注:*** この設定を有効にすると、内部エラーが発生するたびにテレメトリが削除されます。 これは、SDK の構成または使用に関する問題をすばやく特定するのに役立ちます。 デバッグ時にテレメトリが削除されないようにするには、`enableDebug` の代わりに `consoleLoggingLevel` または `telemetryLoggingLevel` の使用を検討してください。 |
| loggingLevelConsole | 0 | **内部** Application Insights エラーをコンソールに記録します。 <br>0: オフ <br>1:重大なエラーのみ <br>2\.すべて (エラーおよび警告) |
| loggingLevelTelemetry | 1 | **内部** Application Insights エラーをテレメトリとして送信します。 <br>0: オフ <br>1:重大なエラーのみ <br>2\.すべて (エラーおよび警告) |
| diagnosticLogInterval | 10000 | 内部ログ キューの (内部) ポーリング間隔 (ミリ秒) |
| samplingPercentage | 100 | 送信されるイベントの割合。 既定値は 100 で、すべてのイベントが送信されます。 大規模なアプリケーションでデータ上限を維持する場合は、これを設定します。 |
| autoTrackPageVisitTime | false | true の場合、ページビューに関して、前にインストルメント化されたページのビュー時間が追跡されてテレメトリとして送信されます。また、現在のページビューについて新しいタイマーが開始されます。 既定値は false です。 |
| disableAjaxTracking | false | true の場合、Ajax 呼び出しは自動収集されません。 既定値は false です。 |
| disableFetchTracking | true | true の場合、フェッチ要求は自動収集されません。 既定値は true です。 |
| overridePageViewDuration | false | true の場合、trackPageView の既定の動作が変わり、trackPageView の呼び出し時にページビュー期間の終了を記録します。 false の場合に、trackPageView にカスタム期間が指定されていないと、Navigation Timing API を使用してページ ビューのパフォーマンスが計算されます。 既定値は false です。 |
| maxAjaxCallsPerView | 500 | 既定値: 500 - ページ ビュー 1 回あたりの AJAX 呼び出し数を監視し制御します。 -1 に設定すると、ページで発行されたすべて (無制限) の AJAX 呼び出しを監視します。 |
| disableDataLossAnalysis | true | false の場合、まだ送信されていない項目について、内部テレメトリ センダー バッファーがスタートアップ時にチェックされます。 |
| disableCorrelationHeaders | false | false の場合、SDK によって 2 つのヘッダー (Request-Id と Request-Context) がすべての依存関係要求に追加され、サーバー側の対応する要求と関連付けられます。 既定値は false です。 |
| correlationHeaderExcludedDomains |  | 特定のドメインの関連付けヘッダーを無効にします。 |
| correlationHeaderDomains |  | 特定のドメインの関連付けヘッダーを有効にします。 |
| disableFlushOnBeforeUnload | false | 既定値は false です。 true の場合、onBeforeUnload イベントによってトリガーされても、flush メソッドは呼び出されません。 |
| enableSessionStorageBuffer | true | 既定値は true です。 true の場合、未送信のすべてのテレメトリを含むバッファーがセッション ストレージに格納されます。 バッファーはページの読み込み時に復元されます。 |
| isCookieUseDisabled | false | 既定値は false です。 true の場合、SDK によって cookie に対するデータの格納や読み取りは行われません。|
| cookieDomain | null | カスタム Cookie ドメイン。 これは、サブドメイン間で Application Insights Cookie を共有する場合に便利です。 |
| isRetryDisabled | false | 既定値は false です。 false の場合、206 (部分的な成功)、408 (タイムアウト)、429 (要求が多すぎる)、500 (内部サーバー エラー)、503 (サービス利用不可)、および 0 (オフライン、検出された場合のみ) で再試行します。 |
| isStorageUseDisabled | false | true の場合、SDK によってローカルおよびセッション ストレージに対するデータの格納や読み取りは行われません。 既定値は false です。 |
| isBeaconApiDisabled | true | false の場合、SDK が [Beacon API](https://www.w3.org/TR/beacon) を使用してすべてのテレメトリが送信されます。 |
| sdkExtension | null | sdk 拡張機能の名前を設定します。 英字のみを使用できます。 拡張機能名はプレフィックスとして ai.internal.sdkVersion タグに付けられます (ext_javascript:2.0.0 など)。 既定値は Null です。 |
| isBrowserLinkTrackingEnabled | false | 既定値は false です。 true の場合、SDK によってすべての[ブラウザー リンク](https://docs.microsoft.com/aspnet/core/client-side/using-browserlink)要求が追跡されます。 |
| appId | null | appId は、サーバー側の要求によってクライアント側で発生する AJAX 依存関係の相関関係のために使用されます。 Beacon API が有効になっているとき、これを自動的に使用することはできませんが、構成で手動で設定できます。 既定値は null です。 |
| enableCorsCorrelation | false | truee の場合、SDK によって 2 つのヘッダー (Request-Id と Request-Context) がすべての CORS 要求に追加され、送信される AJAX 依存関係がサーバー側の対応する要求と関連付けられます。 既定値は false です。 |
| namePrefix | undefined | localStorage および Cookie 名の接尾語として使用される省略可能な値。
| enableAutoRouteTracking | false | シングル ページ アプリケーション (SPA) でのルート変更を自動的に追跡します。 true の場合、ルートの変更ごとに Application Insights に新しいページビューが送信されます。 ハッシュ ルート変更 (`example.com/foo#bar`) も新しいページ ビューとして記録されます。

## <a name="single-page-applications"></a>シングル ページ アプリケーション

既定では、この SDK では、シングル ページ アプリケーションで発生する状態ベースのルート変更は処理され**ません**。 シングル ページ アプリケーションの自動ルート変更追跡を有効にするには、`enableAutoRouteTracking: true` をセットアップ構成に追加します。

現在、この SDK で初期化できる個別の [React プラグイン](#react-extensions)が提供されています。 これによって、ルート変更追跡が実現し、[他の React 固有テレメトリ](https://github.com/microsoft/ApplicationInsights-JS/blob/17ef50442f73fd02a758fbd74134933d92607ecf/extensions/applicationinsights-react-js/README.md)の収集も行われます。

## <a name="react-extensions"></a>React の拡張機能

| Extensions |
|---------------|
| [React](https://github.com/microsoft/ApplicationInsights-JS/blob/17ef50442f73fd02a758fbd74134933d92607ecf/extensions/applicationinsights-react-js/README.md)|
| [React Native](https://github.com/microsoft/ApplicationInsights-JS/blob/17ef50442f73fd02a758fbd74134933d92607ecf/extensions/applicationinsights-react-native/README.md)|

## <a name="explore-browserclient-side-data"></a>ブラウザー/クライアント側データの参照

ブラウザー/クライアント側データを表示するには、 **[メトリック]** に移動して、興味がある個別のメトリックを追加します。 

![](./media/javascript/page-view-load-time.png)

また、JavaScript SDK のデータもポータルのブラウザー エクスペリエンスを使用して表示できます。

**[ブラウザー]** を選択してから、 **[エラー]** または **[パフォーマンス]** を選択します。

![](./media/javascript/browser.png)

### <a name="performance"></a>パフォーマンス 

![](./media/javascript/performance-operations.png)

### <a name="dependencies"></a>依存関係

![](./media/javascript/performance-dependencies.png)

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

例外テレメトリのミニファイされたコール スタックは、Azure portal でアンミニファイすることができます。 [例外の詳細] パネルの既存の統合はすべて、新たにアンミニファイされるコール スタックに対して動作します。 アンミニファイするためのソース マップのドラッグ アンド ドロップでは、既存および将来のすべての JS SDK (+Node.JS) がサポートされるため、SDK バージョンをアップグレードする必要はありません。 アンミニファイされたコール スタックを表示するには、次の手順に従います。
1. Azure portal で例外テレメトリ項目を選択し、[エンド ツー エンド トランザクションの詳細] を表示します。
2. このコール スタックにどのソース マップが対応しているかを識別します。 ソース マップは、スタック フレームのソース ファイルと同じ名前であることが必要です。接尾辞は `.map` です。
3. ソース マップを Azure portal のコール スタックにドラッグ アンド ドロップします。![](https://i.imgur.com/Efue9nU.gif)

### <a name="application-insights-web-basic"></a>Application Insights Web Basic

軽量エクスペリエンスのためには、代わりに Application Insights の基本バージョンをインストールすることもできます。
```
npm i --save @microsoft/applicationinsights-web-basic
```
このバージョンは最小限の機能を備え、必要に応じて発展させるかどうかはユーザー次第です。 たとえば、自動収集 (キャッチされていない例外、AJAX など) は行われません。 `trackTrace` や `trackException` など特定の種類のテレメトリを送信するための API はこのバージョンには含まれないため、独自にラッパーを用意する必要があります。 使用できる唯一の API は `track` です。 [サンプル](https://github.com/Azure-Samples/applicationinsights-web-sample1/blob/master/testlightsku.html)については、こちらを参照してください。

## <a name="examples"></a>例

実行できる例については、[Application Insights Javascript SDK サンプル](https://github.com/topics/applicationinsights-js-demo)を参照してください。

## <a name="upgrading-from-the-old-version-of-application-insights"></a>以前のバージョンの Application Insights からアップグレードする

SDK V2 バージョンでの破壊的変更:
- API 署名の向上のため、trackPageView や trackException など一部の API 呼び出しが更新されています。 IE8 以前のバージョンのブラウザーでの実行はサポートされません。
- データ スキーマの更新により、テレメトリ エンベロープのフィールド名と構造が変更されています。
- `context.operation` が `context.telemetryTrace` に移動されました。 一部のフィールドも変更されました (`operation.id` --> `telemetryTrace.traceID`)。
  - 現在のページビュー ID (SPA アプリなど) を手動で更新する場合、`appInsights.properties.context.telemetryTrace.traceID = Util.newId()` を使用して実行できます。

現在の Application Insights PRODUCTION SDK (1.0.20) を使用しており、新しい SDK がランタイムで動作するかどうかを確認する場合は、現在の SDK 読み込みシナリオに応じて URL を更新します。

- CDN を使用したダウンロードのシナリオ:現在使用しているコード スニペットが次の URL を指すように更新します。
   ```
   "https://az416426.vo.msecnd.net/scripts/b/ai.2.min.js"
   ```

- NPM のシナリオ:`downloadAndSetup` を呼び出して、完全な ApplicationInsights スクリプトを CDN からダウンロードし、インストルメンテーション キーを使用して初期化します。

   ```ts
   appInsights.downloadAndSetup({
     instrumentationKey: "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxx",
     url: "https://az416426.vo.msecnd.net/scripts/b/ai.2.min.js"
     });
   ```

内部環境でテストして、監視テレメトリが想定どおりに動作していることを確認します。 すべて動作している場合は、API 署名を SDK V2 バージョンに適切に更新し、実稼働環境にデプロイします。

## <a name="sdk-performanceoverhead"></a>SDK のパフォーマンス/オーバーヘッド

Application Insights は、ちょうど 25 KB に GZip され、初期化には最長で 15 ミリ秒しかかからないため、Web サイトの読み込み時間にはほとんど影響ありません。 スニペットを使用すると、ライブラリの最小コンポーネントがすばやく読み込まれます。 その間に、スクリプト全体がバックグラウンドでダウンロードされます。

スクリプトが CDN からダウンロードされている間に、ページのすべての追跡がキューに登録されます。 ダウンロードしたスクリプトの初期化が非同期で完了すると、キューに登録されたすべてのイベントが追跡されます。 この結果、ページのライフ サイクル全体でテレメトリが失われることはありません。 このセットアップ プロセスでは、ユーザーには見えないシームレスな分析システムを使用してページが提供されます。

> 概要:
> - **25 KB**: GZip 圧縮
> - **15 ms**: 全体の初期化時間
> - **ゼロ**: ページのライフ サイクルで失われる追跡

## <a name="browser-support"></a>ブラウザーのサポート

![Chrome](https://raw.githubusercontent.com/alrra/browser-logos/master/src/chrome/chrome_48x48.png) | ![Firefox](https://raw.githubusercontent.com/alrra/browser-logos/master/src/firefox/firefox_48x48.png) | ![IE](https://raw.githubusercontent.com/alrra/browser-logos/master/src/edge/edge_48x48.png) | ![Opera](https://raw.githubusercontent.com/alrra/browser-logos/master/src/opera/opera_48x48.png) | ![Safari](https://raw.githubusercontent.com/alrra/browser-logos/master/src/safari/safari_48x48.png)
--- | --- | --- | --- | --- |
最新 ✔ | 最新 ✔ | 9+ ✔ | 最新 ✔ | 最新 ✔ |

## <a name="open-source-sdk"></a>オープンソース SDK

Application Insights JavaScript SDK はオープンソースです。ソース コードを表示したり、プロジェクトに参加したりするには、[GitHub の公式リポジトリ](https://github.com/Microsoft/ApplicationInsights-JS)にアクセスしてください。

## <a name="next"></a> 次のステップ
* [利用状況を追跡する](usage-overview.md)
* [カスタム イベントとメトリックス](api-custom-events-metrics.md)
* [ビルド - 測定 - 学習](usage-overview.md)

