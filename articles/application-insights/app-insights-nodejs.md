---
title: "Azure Application Insights を使用して Node.js サービスを監視する | Microsoft Docs"
description: "Application Insights を使用して Node.js サービスのパフォーマンスを監視して問題を診断します。"
services: application-insights
documentationcenter: nodejs
author: joshgav
manager: carmonm
ms.assetid: 2ec7f809-5e1a-41cf-9fcd-d0ed4bebd08c
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: get-started-article
ms.date: 05/01/2017
ms.author: bwren
ms.translationtype: Human Translation
ms.sourcegitcommit: 64bd7f356673b385581c8060b17cba721d0cf8e3
ms.openlocfilehash: 76a8025cd2a67533beb321c88e924517c1977dfc
ms.contentlocale: ja-jp
ms.lasthandoff: 05/02/2017

---

# <a name="monitor-your-nodejs-services-and-apps-with-application-insights"></a>Application Insights を使用して Node.js サービスとアプリを監視する

[Azure Application Insights](app-insights-overview.md) では、[パフォーマンスなどの問題を検出し、迅速に診断](app-insights-detect-triage-diagnose.md)できるように、デプロイしたバックエンド サービスとコンポーネントを監視します。 Application Insights を、任意の場所 (データ センター、Azure VM と Web Apps、その他のパブリック クラウド) でホストされている Node.js サービスに対して使用します。

監視データの受け取り、保存、探索を行うためには、次の手順に従って、コードにエージェントを含め、Azure の対応する Application Insights リソースを設定します。 エージェントは、詳細な分析と探索のために、そのリソースにデータを送信します。

Node.js エージェントでは、受信と発信 HTTP 要求、システムのいくつかのメトリック、および例外を自動的に監視できます。 v0.20 以降では、`mongodb`、`mysql`、`redis` などの一般的なサード パーティ パッケージも監視できます。 受信 HTTP 要求に関連するすべてのイベントは、トラブルシューティングを迅速に行えるように関連付けられます。

後で説明するエージェント API を使用して手動でインストルメント化することで、アプリとシステムのさまざまな側面を監視できます。

![Example performance monitoring charts](./media/app-insights-nodejs/10-perf.png)

## <a name="getting-started"></a>Getting Started (概要)

アプリまたはサービスの監視を設定する手順について説明します。

### <a name="resource"></a> App Insights リソースを設定する

**開始する前に**、Azure サブスクリプションがあることを確認するか、[無償で新しい Azure サブスクリプションを取得][azure-free-offer]してください。 組織に Azure サブスクリプションが既にある場合は、管理者が[これらの手順][add-aad-user]に従って、あなたを追加できます。

[azure-free-offer]: https://azure.microsoft.com/en-us/free/
[add-aad-user]: https://docs.microsoft.com/en-us/azure/active-directory/active-directory-users-create-azure-portal

[Azure Portal][portal] にログインし、次の図に示すように、[新規] > [開発者ツール] > [Application Insights] の順にクリックして、Application Insights リソースを作成します。 このリソースには、テレメトリ データを受信するためのエンドポイント、そのデータのストレージ、保存済みのレポートとダッシュボード、ルールとアラートの構成などが含まれています。

![App Insights リソースを作成する](./media/app-insights-nodejs/03-new_appinsights_resource.png)

リソース作成ページで、アプリケーションの種類のドロップダウン リストから [Node.js アプリケーション] を選択します。 アプリの種類により、自動的に作成されるダッシュボードとレポートの既定のセットが決定されます。 しかし、心配は不要です。実のところ、どの App Insights リソースでも、言語やプラットフォームに関係なくデータを収集できます。

![新しい App Insights リソースのフォーム](./media/app-insights-nodejs/04-create_appinsights_resource.png)

### <a name="agent"></a> Node.js エージェントを設定する

次に、データを収集できるように、アプリにエージェントを含めます。
まず、以下に示すように、ポータルからリソースのインストルメンテーション キー (以降は `ikey` と呼びます) をコピーします。 App Insights システムでは、このキーを使用して、データを Azure リソースにマップします。そのため、エージェントが使用する環境変数やコードでこのキーを指定する必要があります。  

![インストルメンテーション キーのコピー](./media/app-insights-nodejs/05-appinsights_ikey_portal.png)

次に、package.json を使用して、Node.js エージェント ライブラリをアプリの依存関係に追加します。 アプリのルート フォルダーから次を実行します。

```bash
npm install applicationinsights --save
```

次に、コードでライブラリを明示的に読み込む必要があります。 エージェントはインストルメンテーションを他のさまざまなライブラリに挿入するため、できる限り早く (`require` ステートメントよりも前に) ライブラリを読み込む必要があります。 まず、最初の .js ファイルの先頭に次を追加します。

```javascript
const appInsights = require("applicationinsights");
appInsights.setup("<instrumentation_key>");
appInsights.start();
```

`setup` メソッドは、インストルメンテーション キー (および Azure リソース) をすべての追跡項目で既定で使用するよう構成します。 構成の終了後に `start` を呼び出して、テレメトリ データの収集と送信を開始します。

ikey は、`setup()` または `getClient()` に手動で渡す代わりに、APPINSIGHTS\_INSTRUMENTATIONKEY 環境変数を使用して渡すこともできます。 この方法では、コミットされたソース コードに ikey を含めず、異なる環境に合わせて異なる ikey を指定できます。

その他の構成オプションについては、以下で説明しています。

インストルメンテーション キーを空でない文字列に設定することで、テレメトリを送信せずにエージェントを試してみることができます。

### <a name="monitor"></a> アプリを監視する

エージェントは、Node.js ランタイムおよび一般的なサード パーティ モジュールに関するテレメトリを自動的に収集します。 今アプリケーションを使用して、そうしたデータを生成します。

次に、[Azure Portal][portal] で、前に作成した Application Insights リソースを参照し、次の図に示すように、概要タイムラインで最初のいくつかのデータ ポイントを探します。 グラフをクリックすると、詳細が表示されます。

![最初のデータ ポイント](./media/app-insights-nodejs/12-first-perf.png)

[アプリケーション マップ] ボタンをクリックすると、次の図に示すように、アプリの検出されたトポロジが表示されます。 マップ内のコンポーネントをクリックすると、詳細が表示されます。

![簡単なアプリ マップ](./media/app-insights-nodejs/06-appinsights_appmap.png)

[調査] セクションにある利用可能なその他のビューを使用すると、アプリの詳細を確認することや、問題のトラブルシューティングを行うことができます。

![[調査] セクション](./media/app-insights-nodejs/07-appinsights_investigate_blades.png)

#### <a name="no-data"></a>データが表示されない場合

エージェントでは送信するデータをバッチ処理するため、ポータルに項目が表示されるまでに遅延が発生する場合があります。 リソースのデータが表示されない場合は、次の対処方法を試してください。

* アプリケーションをもう少し使用します。操作を実行して、テレメトリをもっと生成します。
* ポータルのリソース ビューで **[最新の情報に更新]** をクリックします。 グラフは定期的に自動的に更新されますが、クリックすると、すぐに更新されます。
* [必要な送信ポート](app-insights-ip-addresses.md)が開いていることを確認します。
* [[検索]](app-insights-diagnostic-search.md) タイルを開き、個々のイベントを探します。
* [よく寄せられる質問][]を確認します。


## <a name="agent-configuration"></a>エージェントの構成

エージェントの構成方法とその既定値を次に示します。

サービスのイベントを完全に関連付けるには、必ず `.setAutoDependencyCorrelation(true)` を設定します。 これにより、エージェントで Node.js の非同期コールバック全体のコンテキストを追跡できます。

```javascript
const appInsights = require("applicationinsights");
appInsights.setup("<instrumentation_key>")
    .setAutoDependencyCorrelation(false)
    .setAutoCollectRequests(true)
    .setAutoCollectPerformance(true)
    .setAutoCollectExceptions(true)
    .setAutoCollectDependencies(true)
    .start();
```

## <a name="agent-api"></a>エージェント API

<!-- TODO: Fully document agent API. -->

.NET エージェント API については、[こちら](app-insights-api-custom-events-metrics.md)で詳しく説明しています。

Application Insights の Node.js クライアントを使用して、任意の要求、イベント、メトリック、または例外を追跡できます。 次に、使用できる API の使用例を示します。

```javascript
let appInsights = require("applicationinsights");
appInsights.setup().start(); // assuming ikey in env var
let client = appInsights.getClient();

client.trackEvent("my custom event", {customProperty: "custom property value"});
client.trackException(new Error("handled exceptions can be logged with this method"));
client.trackMetric("custom metric", 3);
client.trackTrace("trace message");

let http = require("http");
http.createServer( (req, res) => {
  client.trackRequest(req, res); // Place at the beginning of your request handler
});
```

### <a name="track-your-dependencies"></a>依存関係を追跡する

```javascript
let appInsights = require("applicationinsights");
let client = appInsights.getClient();

var success = false;
let startTime = Date.now();
// execute dependency call here....
let duration = Date.now() - startTime;
success = true;

client.trackDependency("dependency name", "command name", duration, success);
```

### <a name="add-a-custom-property-to-all-events"></a>すべてのイベントにカスタム プロパティを追加する

```javascript
appInsights.client.commonProperties = {
    environment: process.env.SOME_ENV_VARIABLE
};
```

### <a name="track-http-get-requests"></a>HTTP GET 要求を追跡する

```javascript
var server = http.createServer((req, res) => {
    if ( req.method === "GET" ) {
            appInsights.client.trackRequest(req, res);
    }
    // other work here....
    res.end();
});
```

### <a name="track-server-startup-time"></a>サーバーの起動時間を追跡する

```javascript
let start = Date.now();
server.on("listening", () => {
    let duration = Date.now() - start;
    appInsights.client.trackMetric("server startup time", duration);
});
```

## <a name="more-resources"></a>その他のリソース

* [ポータル内でテレメトリを監視する](app-insights-dashboards.md)
* [テレメトリに関する分析クエリを記述する](app-insights-analytics-tour.md)

<!--references-->

[portal]: https://portal.azure.com/
[よく寄せられる質問]: app-insights-troubleshoot-faq.md

