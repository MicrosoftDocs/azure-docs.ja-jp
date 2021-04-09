---
title: Azure Application Insights を使用して Node.js サービスを監視する | Microsoft Docs
description: Application Insights を使用して Node.js サービスのパフォーマンスを監視して問題を診断します。
ms.topic: conceptual
ms.date: 06/01/2020
ms.custom: devx-track-js
ms.openlocfilehash: 9eba74bcf2b4b047b3ed881342db4f7b1011f928
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "100585760"
---
# <a name="monitor-your-nodejs-services-and-apps-with-application-insights"></a>Application Insights を使用して Node.js サービスとアプリを監視する

[Application Insights](./app-insights-overview.md) では、パフォーマンスなどの問題を検出し、迅速に診断できるように、デプロイしたバックエンド サービスとコンポーネントを監視します。 Application Insights は、自社のデータセンターや、Azure VM、Web アプリ、さらには他のパブリック クラウドにホストされた Node.js サービスに対しても使用できます。

監視データを受信、保存、探索するためには、ご使用のコードに SDK を追加し、対応する Application Insights リソースを Azure で設定します。 そのリソースに対して SDK からデータが送信され、詳細な分析と探索が行えるようになります。

Node.js SDK では、受信と発信 HTTP 要求、例外のほか、システムのいくつかのメトリックを自動的に監視できます。 さらに、バージョン 0.20 以降の SDK では、MongoDB、MySQL、Redis など、一般的な[サードパーティ製パッケージ](https://github.com/microsoft/node-diagnostic-channel/tree/master/src/diagnostic-channel-publishers#currently-supported-modules)を監視することもできます。 受信 HTTP 要求に関連するすべてのイベントは、トラブルシューティングを迅速に行えるように関連付けられます。

TelemetryClient API を使用して手動でインストルメント化すれば、ご使用のアプリとシステムのその他の側面も監視できます。 TelemetryClient API については、この記事の後半でさらに詳しく説明します。

## <a name="get-started"></a>はじめに

アプリまたはサービスの監視を設定するには、次の作業を完了します。

### <a name="prerequisites"></a>前提条件

開始する前に、Azure サブスクリプションがあることを確認するか、[無償で新しい Azure サブスクリプションを取得][azure-free-offer]してください。 組織に Azure サブスクリプションが既にある場合は、管理者に[こちらの手順][add-aad-user]に従ってもらうことで、追加してもらうことができます。

[azure-free-offer]: https://azure.microsoft.com/free/
[add-aad-user]: ../../active-directory/fundamentals/add-users-azure-active-directory.md

### <a name="set-up-an-application-insights-resource"></a><a name="resource"></a>Application Insights リソースを設定する

1. [Azure portal][portal] にサインインします。
2. [Application Insights リソースを作成します](create-new-resource.md)。

### <a name="set-up-the-nodejs-sdk"></a><a name="sdk"></a> Node.js SDK の設定

アプリでデータを収集できるように、アプリに SDK を追加します。

> [!IMPORTANT]
> 新しい Azure リージョンでは、インストルメンテーション キーの代わりに接続文字列を使用する **必要** があります。 [接続文字列](./sdk-connection-string.md?tabs=nodejs)により、利用統計情報と関連付けるリソースが識別されます。 また、リソースでテレメトリの宛先として使用するエンドポイントを変更することもできます。 接続文字列をコピーし、アプリケーションのコードまたは環境変数に追加する必要があります。

1. 新しく作成したリソースから、リソースのインストルメンテーション キー (*ikey* とも呼ばれます) をコピーします。 Application Insights では、この ikey を使用して、対象の Azure リソースにデータをマッピングします。 SDK で ikey を使用するためには、その ikey を環境変数またはコードの中で指定する必要があります。  

   ![インストルメンテーション キーのコピー](./media/nodejs/instrumentation-key-001.png)

2. package.json を使用して、Node.js SDK ライブラリをアプリの依存関係に追加します。 アプリのルート フォルダーから次を実行します。

   ```bash
   npm install applicationinsights --save
   ```

    > [!NOTE]
    > TypeScript を使用している場合は、個別の "typings" パッケージをインストールしないでください。 この NPM パッケージには、組み込みの型指定が含まれています。

3. コードでライブラリを明示的に読み込みます。 インストルメンテーションは、SDK によって他の多数のライブラリに挿入されます。そのため、ライブラリはできる限り早く (`require` ステートメントよりも前に) 読み込むようにしてください。

   ```javascript
   let appInsights = require('applicationinsights');
   ```
4.  また、`setup()` または `new appInsights.TelemetryClient()` に手動で渡すのではなく、環境変数 `APPINSIGHTS_INSTRUMENTATIONKEY` を使用して ikey を指定することもできます。 この方法では、コミットされたソース コードに ikey を含めず、異なる環境に合わせて異なる ikey を指定できます。 手動で構成するには、`appInsights.setup('[your ikey]');` を呼び出します。

    その他の構成方法については、以降のセクションを参照してください。

    `appInsights.defaultClient.config.disableAppInsights = true` を設定すると、テレメトリを送信することなく SDK を試すことができます。

5. `appInsights.start();` を呼び出して、データの収集と送信を自動的に開始します。

### <a name="monitor-your-app"></a><a name="monitor"></a> アプリを監視する

SDK は、Node.js ランタイムおよび一般的なサード パーティ モジュールに関するテレメトリを自動的に収集します。 アプリケーションを使用して、そうしたデータを生成します。

次に、[Azure portal][portal] で、先ほど作成した Application Insights リソースに移動します。 **[概要のタイムライン]** に、いくつかの最初のデータ ポイントが現れます。 さらに詳しいデータを表示するには、グラフ内の別のコンポーネントを選択してください。

対象のアプリに関して検出されたトポロジを表示するには、[[アプリケーション マップ]](app-map.md) を使用できます。

#### <a name="no-data"></a>データなし

SDK では送信するデータをバッチ処理するため、ポータルに項目が表示されるまでに遅延が発生する場合があります。 リソースのデータが表示されない場合は、次の対処方法を試してください。

* 引き続きアプリケーションを使用します。 操作を実行して、テレメトリをさらに生成します。
* ポータルのリソース ビューで **[最新の情報に更新]** をクリックします。 グラフは定期的に自動で更新されますが、手動で更新を強制することで最新の情報が直ちに表示されます。
* [必要な送信ポート](./ip-addresses.md)が開いていることを確認します。
* [[検索]](./diagnostic-search.md) を使用して特定のイベントを探します。
* [よく寄せられる質問][FAQ]を確認します。

## <a name="basic-usage"></a>基本的な使用方法

すぐに利用できる HTTP 要求のコレクション、一般的なサードパーティ ライブラリ イベント、未処理の例外、およびシステム メトリックの場合は、次のとおりです。

```javascript

let appInsights = require("applicationinsights");
appInsights.setup("[your ikey]").start();

```

> [!NOTE]
> インストルメンテーション キーが環境変数 `APPINSIGHTS_INSTRUMENTATIONKEY` で設定されている場合は、引数なしで `.setup()` を呼び出すことができます。 この方法により、さまざまな環境で異なる ikey を簡単に使用できるようになります。

他のパッケージを読み込む前に、スクリプトのできるだけ早い段階で Application Insights ライブラリ `require("applicationinsights")` を読み込みます。 これは、Application Insights ライブラリが追跡のために後でパッケージを準備できるようにするために必要です。 同様の準備を行った他のライブラリとの競合が発生した場合は、それらの後に Application Insights ライブラリを読み込んでみてください。

JavaScript がコールバックを処理する方法が原因で、外部依存関係および後のコールバックで要求を追跡するために追加の作業が必要になります。 既定では、この追加の追跡は有効になっています。次の[構成](#sdk-configuration)セクションの説明に従い、`setAutoDependencyCorrelation(false)` を呼び出すことで無効にします。

## <a name="migrating-from-versions-prior-to-022"></a>0\.22 より前のバージョンからの移行

バージョン 0.22 より前のリリースと、それ以降のリリースの間には、破壊的変更があります。 これらの変更は、他の Application Insights SDK との一貫性を保ち、将来の拡張を可能にするように設計されています。

一般に、次を行うことで移行できます。

- `appInsights.client` への参照を `appInsights.defaultClient` に置き換えます。
- `appInsights.getClient()` への参照を `new appInsights.TelemetryClient()` に置き換えます
- client.track* メソッドのすべての引数を、名前付きプロパティを引数として含む 1 つのオブジェクトに置き換えます。 それぞれの型のテレメトリに対して除外されるオブジェクトについては、IDE の組み込みの型ヒントまたは [TelemetryTypes](https://github.com/Microsoft/ApplicationInsights-node.js/tree/develop/Declarations/Contracts/TelemetryTypes) を参照してください。

`appInsights.setup()` にチェーンせずに SDK 構成関数にアクセスすると、これらの関数を `appInsights.Configurations` (たとえば `appInsights.Configuration.setAutoCollectDependencies(true)` など) で見つけることができるようになります。 次のセクションで、既定の構成に対する変更を確認してください。

## <a name="sdk-configuration"></a>SDK の構成

`appInsights` オブジェクトは、いくつかの構成メソッドを提供します。 これらを既定値と共に、次のスニペットに一覧表示します。

```javascript
let appInsights = require("applicationinsights");
appInsights.setup("<instrumentation_key>")
    .setAutoDependencyCorrelation(true)
    .setAutoCollectRequests(true)
    .setAutoCollectPerformance(true, true)
    .setAutoCollectExceptions(true)
    .setAutoCollectDependencies(true)
    .setAutoCollectConsole(true)
    .setUseDiskRetryCaching(true)
    .setSendLiveMetrics(false)
    .setDistributedTracingMode(appInsights.DistributedTracingModes.AI)
    .start();
```

サービスのイベントを完全に関連付けるには、必ず `.setAutoDependencyCorrelation(true)` を設定します。 このオプションを設定することにより、Node.js の非同期コールバック全体のコンテキストを SDK で追跡できます。

IDE の組み込みの型ヒントで説明を確認するか、または [applicationinsights.ts](https://github.com/microsoft/ApplicationInsights-node.js/blob/develop/applicationinsights.ts) を参照して、これらがコントロールできる内容と省略可能なセカンダリ引数についての詳細を確認してください。

> [!NOTE]
>  既定では、`setAutoCollectConsole` は `console.log` (およびその他のコンソール メソッド) の呼び出しを *除外* するように構成されています。 サポートされているサードパーティのロガー (たとえば、winston や bunyan) の呼び出しのみが収集されます。 `setAutoCollectConsole(true, true)` を使用することで、この動作を変更して `console` メソッドの呼び出しを含めることができます。

### <a name="sampling"></a>サンプリング

既定では、SDK は収集されたすべてのデータを Application Insights サービスに送信します。 大量のデータを収集する場合は、サンプリングを有効にして、送信されるデータの量を減らすことができます。 これを実現するには、クライアントの `config` オブジェクトの `samplingPercentage` フィールドを設定します。 `samplingPercentage` を 100 (既定値) に設定すると、すべてのデータが送信され、0 にした場合は何も送信されません。

自動関連付けを使用している場合は、1 つの要求に関連付けられているすべてのデータが 1 つの単位として、追加または除外されます。

次のようなコードを追加して、サンプリングを有効にします。

```javascript
const appInsights = require("applicationinsights");
appInsights.setup("<instrumentation_key>");
appInsights.defaultClient.config.samplingPercentage = 33; // 33% of all telemetry will be sent to Application Insights
appInsights.start();
```

### <a name="multiple-roles-for-multi-components-applications"></a>複数コンポーネント アプリケーションの複数のロール

アプリケーションが複数のコンポーネントで構成されていて、同じインストルメンテーション キーでそのすべてをインストルメント化しつつ、それらのコンポーネントをポータルでは個別のユニットとして表示したい場合は、別のインストルメンテーション キーが使用されているかのように (たとえば、アプリケーション マップの個別のノードのように) RoleName フィールドを手動で構成して、あるコンポーネントのテレメトリを、Application Insights リソースにデータを送信する他のコンポーネントから区別する必要があるかもしれません。

次を使用して、RoleName フィールドを設定します。

```javascript
const appInsights = require("applicationinsights");
appInsights.setup("<instrumentation_key>");
appInsights.defaultClient.context.tags[appInsights.defaultClient.context.keys.cloudRole] = "MyRoleName";
appInsights.start();
```

### <a name="automatic-third-party-instrumentation"></a>自動サードパーティ インストルメンテーション

非同期呼び出し間でコンテキストを追跡するには、MongoDB や Redis などのサードパーティ製のライブラリでいくつかの変更が必要になります。 既定では、Application Insights は [`diagnostic-channel-publishers`](https://github.com/Microsoft/node-diagnostic-channel/tree/master/src/diagnostic-channel-publishers) を使用して、これらのライブラリの一部をモンキーパッチにより修正します。 これは、`APPLICATION_INSIGHTS_NO_DIAGNOSTIC_CHANNEL` 環境変数を設定することによって無効にできます。

> [!NOTE]
> この環境変数を設定することで、イベントが適切な操作に正しく関連付けられなくなる可能性があります。

 個々のモンキーパッチを無効にするには、`APPLICATION_INSIGHTS_NO_PATCH_MODULES` 環境変数に、無効にするパッケージをコンマ区切りリスト (たとえば `APPLICATION_INSIGHTS_NO_PATCH_MODULES=console,redis` など) で設定し、`console` および `redis` パッケージに修正プログラムを適用しないようにします。

現時点では、`bunyan`、`console`、`mongodb`、`mongodb-core`、`mysql`、`redis`、`winston`、`pg`、`pg-pool` の 9 つのパッケージがインストルメント化されています。 これらのパッケージのどのバージョンにパッチが適用されているかについては、「[diagnostic-channel-publishers の README](https://github.com/Microsoft/node-diagnostic-channel/blob/master/src/diagnostic-channel-publishers/README.md)」を参照してください。

`bunyan`、`winston`、および `console` のパッチにより、`setAutoCollectConsole` が有効になっているかどうかに基づいて Application Insights トレース イベントが生成されます。 残りにより、`setAutoCollectDependencies` が有効になっているかどうかに基づいて Application Insights 依存関係イベントが生成されます。

### <a name="live-metrics"></a>ライブ メトリック

アプリから Azure へのライブ メトリックの送信を有効にするには、`setSendLiveMetrics(true)` を使用します。 ポータルでのライブ メトリックのフィルター処理は、現在サポートされていません。

### <a name="extended-metrics"></a>拡張メトリック

> [!NOTE]
> 拡張ネイティブ メトリックを送信する機能が、バージョン 1.4.0 で追加されました。

アプリから Azure に拡張ネイティブ メトリックを送信できるようにするには、個別のネイティブ メトリック パッケージをインストールします。 SDK はインストールされると自動的に読み込まれ、Node.js ネイティブ メトリックの収集を開始します。

```bash
npm install applicationinsights-native-metrics
```

現在、ネイティブ メトリック パッケージでは、ガベージ コレクションの CPU 時間、イベント ループのティック、およびヒープの使用率の自動収集が実行されます。

- **ガベージ コレクション**:各種類のガベージコレクションに費やされた CPU 時間と、それぞれの種類の発生回数。
- **イベント ループ**:発生したティックの数と、合計で費やされた CPU 時間。
- **ヒープと非ヒープ**:ヒープまたは非ヒープの、アプリのメモリ使用量。

### <a name="distributed-tracing-modes"></a>分散トレース モード

既定では SDK は、Application Insights SDK でインストルメント化された他のアプリケーションおよびサービスによって認識されるヘッダーを送信します。 必要に応じて、既存の AI ヘッダーに加えて [W3C トレース コンテキスト](https://github.com/w3c/trace-context) ヘッダーの送信および受信を有効にすることができます。そのため、既存のレガシ サービスとの関連付けが中断されることはありません。 W3C ヘッダーを有効にすると、アプリは Application Insights でインストルメント化されていない他のサービスと関連付けることが可能になりますが、この W3C 標準が採用されます。

```Javascript
const appInsights = require("applicationinsights");
appInsights
  .setup("<your ikey>")
  .setDistributedTracingMode(appInsights.DistributedTracingModes.AI_AND_W3C)
  .start()
```

## <a name="telemetryclient-api"></a>TelemetryClient API

TelemetryClient API の詳細な説明については、「[カスタムのイベントとメトリックのための Application Insights API](./api-custom-events-metrics.md)」を参照してください。

Application Insights の Node.js SDK を使用して、任意の要求、イベント、メトリック、または例外を追跡できます。 次のコード サンプルで、使用できるいくつかの API を紹介します。

```javascript
let appInsights = require("applicationinsights");
appInsights.setup().start(); // assuming ikey in env var. start() can be omitted to disable any non-custom data
let client = appInsights.defaultClient;
client.trackEvent({name: "my custom event", properties: {customProperty: "custom property value"}});
client.trackException({exception: new Error("handled exceptions can be logged with this method")});
client.trackMetric({name: "custom metric", value: 3});
client.trackTrace({message: "trace message"});
client.trackDependency({target:"http://dbname", name:"select customers proc", data:"SELECT * FROM Customers", duration:231, resultCode:0, success: true, dependencyTypeName: "ZSQL"});
client.trackRequest({name:"GET /customers", url:"http://myserver/customers", duration:309, resultCode:200, success:true});

let http = require("http");
http.createServer( (req, res) => {
  client.trackNodeHttpRequest({request: req, response: res}); // Place at the beginning of your request handler
});
```

### <a name="track-your-dependencies"></a>依存関係を追跡する

依存関係を追跡するには、次のコードを使用します。

```javascript
let appInsights = require("applicationinsights");
let client = new appInsights.TelemetryClient();

var success = false;
let startTime = Date.now();
// execute dependency call here....
let duration = Date.now() - startTime;
success = true;

client.trackDependency({target:"http://dbname", name:"select customers proc", data:"SELECT * FROM Customers", duration:duration, resultCode:0, success: true, dependencyTypeName: "ZSQL"});;
```

`trackMetric` を使用して、イベント ループのスケジュール設定にかかる時間を測定するユーティリティの例を次に示します。  

```javascript
function startMeasuringEventLoop() {
  var startTime = process.hrtime();
  var sampleSum = 0;
  var sampleCount = 0;

  // Measure event loop scheduling delay
  setInterval(() => {
    var elapsed = process.hrtime(startTime);
    startTime = process.hrtime();
    sampleSum += elapsed[0] * 1e9 + elapsed[1];
    sampleCount++;
  }, 0);

  // Report custom metric every second
  setInterval(() => {
    var samples = sampleSum;
    var count = sampleCount;
    sampleSum = 0;
    sampleCount = 0;

    if (count > 0) {
      var avgNs = samples / count;
      var avgMs = Math.round(avgNs / 1e6);
      client.trackMetric({name: "Event Loop Delay", value: avgMs});
    }
  }, 1000);
}
```

### <a name="add-a-custom-property-to-all-events"></a>すべてのイベントにカスタム プロパティを追加する

すべてのイベントにカスタム プロパティを追加するには、次のコードを使用します。

```javascript
appInsights.defaultClient.commonProperties = {
  environment: process.env.SOME_ENV_VARIABLE
};
```

### <a name="track-http-get-requests"></a>HTTP GET 要求を追跡する

HTTP GET 要求を手動で追跡するには、次のコードを使用します。

> [!NOTE]
> 既定では、すべての要求が追跡されます。 自動収集を無効にするには、start () を呼び出す前に .setAutoCollectRequests(false) を呼び出します。

```javascript
appInsights.defaultClient.trackRequest({name:"GET /customers", url:"http://myserver/customers", duration:309, resultCode:200, success:true});
```

または、`trackNodeHttpRequest` メソッドを使用して要求を追跡することもできます。

```javascript
var server = http.createServer((req, res) => {
  if ( req.method === "GET" ) {
      appInsights.defaultClient.trackNodeHttpRequest({request:req, response:res});
  }
  // other work here....
  res.end();
});
```

### <a name="track-server-startup-time"></a>サーバーの起動時間を追跡する

サーバーの起動時間を追跡するには、次のコードを使用します。

```javascript
let start = Date.now();
server.on("listening", () => {
  let duration = Date.now() - start;
  appInsights.defaultClient.trackMetric({name: "server startup time", value: duration});
});
```

### <a name="flush"></a>フラッシュ

既定では、テレメトリは 15 秒間バッファリングされた後、インジェスト サーバーへ送信されます。 存続期間の短いアプリケーションの場合 (CLI ツールなど) は、アプリケーションの終了時に、バッファリングされたテレメトリを手動でフラッシュすることが必要になる場合があります (`appInsights.defaultClient.flush()`)。

SDK はアプリケーションがクラッシュしたことを検知すると、フラッシュを自動的に呼び出します (`appInsights.defaultClient.flush({ isAppCrashing: true })`)。 フラッシュ オプション (`isAppCrashing`) によって、アプリケーションが異常な状態であり、テレメトリ送信に適していないと見なされます。 代わりに、SDK はバッファリングしたすべてのテレメトリを[永続ストレージ](./data-retention-privacy.md#nodejs)に保存して、アプリケーションを終了させます。 アプリケーションは、再起動すると、永続ストレージに保存されたテレメトリを送信しようと試みます。

### <a name="preprocess-data-with-telemetry-processors"></a>テレメトリ プロセッサを使用したデータの前処理

*テレメトリ プロセッサ* を使用して、収集されたデータを保持のために送信する前に、データ処理とフィルター処理を行うことができます。 テレメトリ プロセッサは、テレメトリ項目がクラウドに送信される前に、追加された順序で、1 つずつ呼び出されます。

```javascript
public addTelemetryProcessor(telemetryProcessor: (envelope: Contracts.Envelope, context: { http.RequestOptions, http.ClientRequest, http.ClientResponse, correlationContext }) => boolean)
```

テレメトリ プロセッサが false を返した場合、そのテレメトリ項目は送信されません。

すべてのテレメトリ プロセッサは、検査と変更のために、テレメトリ データとそのエンベロープを受け取ります。 また、コンテキスト オブジェクトも受け取ります。 このオブジェクトの内容は、手動で追跡されたテレメトリの追跡メソッド呼び出し時に、`contextObjects` パラメーターによって定義されます。 自動収集されたテレメトリについては、このオブジェクトには、`appInsights.getCorrelationContext()` によって提供される、使用可能な要求情報と永続的な要求コンテンツが格納されます (依存関係の自動関連付けが有効になっている場合)。

テレメトリ プロセッサの TypeScript の種類は次のとおりです。

```javascript
telemetryProcessor: (envelope: ContractsModule.Contracts.Envelope, context: { http.RequestOptions, http.ClientRequest, http.ClientResponse, correlationContext }) => boolean;
```

たとえば、スタック トレースのデータを例外から削除するプロセッサは、次のように記述、および追加できます。

```javascript
function removeStackTraces ( envelope, context ) {
  if (envelope.data.baseType === "Microsoft.ApplicationInsights.ExceptionData") {
    var data = envelope.data.baseData;
    if (data.exceptions && data.exceptions.length > 0) {
      for (var i = 0; i < data.exceptions.length; i++) {
        var exception = data.exceptions[i];
        exception.parsedStack = null;
        exception.hasFullStack = false;
      }
    }
  }
  return true;
}

appInsights.defaultClient.addTelemetryProcessor(removeStackTraces);
```

## <a name="use-multiple-instrumentation-keys"></a>複数のインストルメンテーション キーの使用

複数の Application Insights リソースを作成し、それぞれのインストルメンテーション キー ("ikey") を使用してそれぞれに異なるデータを送信することができます。

 次に例を示します。

```javascript
let appInsights = require("applicationinsights");

// configure auto-collection under one ikey
appInsights.setup("_ikey-A_").start();

// track some events manually under another ikey
let otherClient = new appInsights.TelemetryClient("_ikey-B_");
otherClient.trackEvent({name: "my custom event"});
```

## <a name="advanced-configuration-options"></a>高度な構成オプション

クライアント オブジェクトには、高度なシナリオ用の多くのオプション設定を持つ `config` プロパティが含まれています。 これらは次のように設定できます。

```javascript
client.config.PROPERTYNAME = VALUE;
```

これらのプロパティはクライアント固有であるため、`new appInsights.TelemetryClient()` で作成されたクライアントとは別に `appInsights.defaultClient` を構成することができます。

| プロパティ                        | 説明                                                                                                |
| ------------------------------- |------------------------------------------------------------------------------------------------------------|
| instrumentationKey              | Application Insights リソースの識別子。                                                      |
| endpointUrl                     | テレメトリ ペイロードを送信するインジェスト エンドポイント。                                                      |
| quickPulseHost                  | ライブ メトリック テレメトリの送信先である Live Metrics Stream ホスト。                                            |
| proxyHttpUrl                    | SDK HTTP トラフィック用のプロキシ サーバー (省略可能、既定値は `http_proxy` 環境変数からプルされる)。     |
| proxyHttpsUrl                   | SDK HTTPS トラフィック用のプロキシ サーバー (省略可能、既定値は `https_proxy` 環境変数からプルされる)。   |
| httpAgent                       | SDK HTTP トラフィックに使用する http.Agent (省略可能、既定値は未定義)。                                   |
| httpsAgent                      | SDK HTTPS トラフィックに使用する https.Agent (省略可能、既定値は未定義)。                                 |
| maxBatchSize                    | インジェスト エンドポイントへのペイロードに含めるテレメトリ項目の最大数 (既定 `250`)。   |
| maxBatchIntervalMs              | ペイロードが maxBatchSize になるまでの最大待機時間 (既定 `15000`)。               |
| disableAppInsights              | テレメトリの送信が無効になっているかどうかを示すフラグ (既定 `false`)。                                 |
| samplingPercentage              | 送信すべき追跡されたテレメトリ項目の割合 (既定 `100`)。                      |
| correlationIdRetryIntervalMs    | コンポーネント間の関連付けのために ID 取得を再試行するまでの待機時間 (既定 `30000`)。     |
| correlationHeaderExcludedDomains| コンポーネント間の関連付けヘッダーの挿入から除外するドメインの一覧 (既定は [Config.ts](https://github.com/Microsoft/ApplicationInsights-node.js/blob/develop/Library/Config.ts) を参照してください)。|

## <a name="next-steps"></a>次のステップ

* [ポータル内でテレメトリを監視する](./overview-dashboard.md)
* [テレメトリに関する分析クエリを記述する](../logs/log-analytics-tutorial.md)

<!--references-->

[portal]: https://portal.azure.com/
[FAQ]: ../faq.md
