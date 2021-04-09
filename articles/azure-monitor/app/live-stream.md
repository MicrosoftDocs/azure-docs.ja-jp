---
title: Live Metrics Stream による診断 - Azure Application Insights
description: カスタム メトリックを使用して Web アプリをリアルタイムで監視し、エラー、トレース、イベントのライブ フィードを使用して問題を診断します。
ms.topic: conceptual
ms.date: 04/22/2019
ms.reviewer: sdash
ms.openlocfilehash: 865de94f1d9b4012a908643bbf87f38aeb8594a0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "98679468"
---
# <a name="live-metrics-stream-monitor--diagnose-with-1-second-latency"></a>Live Metrics Stream:1 秒の待機時間での監視と診断

[Application Insights](./app-insights-overview.md) の Live Metrics Stream (QuickPulse とも呼ばれています) を使用して、実稼働中の Web アプリケーションを監視します。 メトリックとパフォーマンス カウンターを選択してフィルタリングし、サービスに支障をきたすことなく、リアルタイムで監視します。 失敗した要求と例外のサンプルからスタック トレースを検査します。 Live Metrics Stream を[プロファイラー](./profiler.md)と[スナップショット デバッガー](./snapshot-debugger.md)と併用することにより、実稼働中の Web サイト向けの強力で非侵襲的な診断ツールを利用できます。

Live Metrics Stream を使用すると、次のことが可能になります。

* 修正がリリースされている間に、パフォーマンスと失敗の数を確認して、修正を検証します。
* テスト負荷の影響を監視し、問題をライブで診断します。
* 監視するメトリックを選択してフィルタリングすることにより、特定のテスト セッションに焦点を当てたり、既知の問題を除外したりできます。
* 発生時に例外トレースを取得します。
* フィルターを試して、最も関連性の高い KPI を検索します。
* すべての Windows パフォーマンス カウンターをライブで監視します。
* 問題が発生しているサーバーを簡単に特定し、すべての KPI/ライブ フィードをフィルター処理してそのサーバーだけに絞り込むことができます。

![[Live Metrics] タブ](./media/live-stream/live-metric.png)

現在、Live Metrics は ASP.NET、ASP.NET Core、Azure Functions、Java、および Node.js アプリでサポートされています。

## <a name="get-started"></a>はじめに

1. 言語固有のガイドラインに従い、Live Metrics を有効にします。
   * [ASP.NET](./asp-net.md) - Live Metrics は既定で有効になっています。
   * [ASP.NET Core](./asp-net-core.md) - Live Metrics は既定で有効になっています。
   * [.NET/.NET Core Console/Worker](./worker-service.md)- Live Metrics は既定で有効になっています。
   * [.NET Applications - コードを使用して有効にします](#enable-livemetrics-using-code-for-any-net-application)。
    * [Java](./java-in-process-agent.md) - Live Metrics は既定で有効になっています。
   * [Node.js](./nodejs.md#live-metrics)

2. [Azure ポータル](https://portal.azure.com)で、アプリの Application Insights リソースを開いてから、Live Stream を開きます。

3. フィルターに顧客名などの機密データを使用する場合は、[コントロール チャネルを保護](#secure-the-control-channel)します。

### <a name="enable-livemetrics-using-code-for-any-net-application"></a>あらゆる .NET アプリケーションに対してコードを使用して LiveMetrics を有効にする

.NET Applications の推奨指示を使用してオンボードするとき、LiveMetrics が既定で有効になっている場合でも、次の方法で LiveMetrics を手動設定できます。

1. NuGet パッケージ [Microsoft.ApplicationInsights.PerfCounterCollector](https://www.nuget.org/packages/Microsoft.ApplicationInsights.PerfCounterCollector) をインストールする
2. 次のサンプル コンソール アプリ コードは、LiveMetrics の設定方法を示しています。

```csharp
using Microsoft.ApplicationInsights;
using Microsoft.ApplicationInsights.Extensibility;
using Microsoft.ApplicationInsights.Extensibility.PerfCounterCollector.QuickPulse;
using System;
using System.Threading.Tasks;

namespace LiveMetricsDemo
{
    class Program
    {
        static void Main(string[] args)
        {
            // Create a TelemetryConfiguration instance.
            TelemetryConfiguration config = TelemetryConfiguration.CreateDefault();
            config.InstrumentationKey = "INSTRUMENTATION-KEY-HERE";
            QuickPulseTelemetryProcessor quickPulseProcessor = null;
            config.DefaultTelemetrySink.TelemetryProcessorChainBuilder
                .Use((next) =>
                {
                    quickPulseProcessor = new QuickPulseTelemetryProcessor(next);
                    return quickPulseProcessor;
                })
                .Build();

            var quickPulseModule = new QuickPulseTelemetryModule();

            // Secure the control channel.
            // This is optional, but recommended.
            quickPulseModule.AuthenticationApiKey = "YOUR-API-KEY-HERE";
            quickPulseModule.Initialize(config);
            quickPulseModule.RegisterTelemetryProcessor(quickPulseProcessor);

            // Create a TelemetryClient instance. It is important
            // to use the same TelemetryConfiguration here as the one
            // used to setup Live Metrics.
            TelemetryClient client = new TelemetryClient(config);

            // This sample runs indefinitely. Replace with actual application logic.
            while (true)
            {
                // Send dependency and request telemetry.
                // These will be shown in Live Metrics stream.
                // CPU/Memory Performance counter is also shown
                // automatically without any additional steps.
                client.TrackDependency("My dependency", "target", "http://sample",
                    DateTimeOffset.Now, TimeSpan.FromMilliseconds(300), true);
                client.TrackRequest("My Request", DateTimeOffset.Now,
                    TimeSpan.FromMilliseconds(230), "200", true);
                Task.Delay(1000).Wait();
            }
        }
    }
}
```

上のサンプルはコンソール アプリ用ですが、あらゆる .NET アプリケーションで同じコードを使用できます。 テレメトリを自動収集するその他の TelemetryModules が有効になっている場合、それらのモジュールの初期化に使用された同じ構成を LiveMetrics モジュールでも使用することが重要です。

## <a name="how-does-live-metrics-stream-differ-from-metrics-explorer-and-analytics"></a>Live Metrics Stream が メトリックス エクスプローラーや Analytics と異なる点

| |Live Stream | メトリックス エクスプローラーと Analytics |
|---|---|---|
|**待機時間**|1 秒以内に表示されるデータ|数分間で集計|
|**リテンション期間なし**|データは、グラフに表示されている間は保持され、その後破棄されます|[データは 90 日間保持](./data-retention-privacy.md#how-long-is-the-data-kept)|
|**[要求時]**|データは、[Live Metircs] ペインが開いている間のみストリーミングされます |SDK がインストールされて有効になるたびに、データが送信されます|
|**Free**|Live Stream データ用の料金は発生しません|[価格](./pricing.md)設定の対象
|**サンプリング**|選択したすべてのメトリックとカウンターが送信されます。 失敗やスタック トレースがサンプリングされます。 |イベントが[サンプリング](./api-filtering-sampling.md)されることがあります|
|**コントロール チャネル**|フィルターの制御シグナルが SDK に送信されます。 このチャネルをセキュリティで保護することをお勧めします。|通信はポータルへの一方向です|

## <a name="select-and-filter-your-metrics"></a>メトリックの選択とフィルタリング

(ASP.NET、ASP.NET Core、Azure Functions (v2) で使用できます。)

ポータルで Application Insights Telemetry に任意のフィルターを適用して、カスタム ライブ KPI を監視できます。 グラフをマウスでポイントしたときに表示されるフィルター コントロールをクリックします。 次のグラフは、URL 属性と期間属性にフィルターを適用して、カスタム要求数 KPI をプロットしています。 [ストリームのプレビュー] セクションでフィルターを検証します。このセクションには、指定した条件といずれかの時点で一致するテレメトリのライブ フィードが表示されます。

![要求率のフィルター処理](./media/live-stream/filter-request.png)

Count 以外の値を監視できます。 オプションはストリームの種類によって異なります。ストリームの種類には、任意の Application Insights Telemetry (要求、依存関係、例外、トレース、イベント、またはメトリック) を指定できます。 オプションには、独自の[カスタム測定](./api-custom-events-metrics.md#properties)を指定できます。

![クエリ ビルダーで要求率にカスタム メトリックを使用する](./media/live-stream/query-builder-request.png)

Application Insights Telemetry だけでなく、Windows パフォーマンス カウンターを監視することもできます。ストリーム オプションから Windows パフォーマンス カウンターを選択し、パフォーマンス カウンターの名前を指定します。

ライブ メトリックは 2 つのポイントで集計されます。具体的には、サーバーごとにローカルで集計された後、すべてのサーバーにわたって集計されます。 どちらについても、それぞれのドロップダウンで他のオプションを選択することで既定値を変更できます。

## <a name="sample-telemetry-custom-live-diagnostic-events"></a>サンプル テレメトリ:カスタム ライブ診断イベント
既定では、イベントのライブ フィードには失敗した要求と依存関係呼び出し、例外、イベント、トレースのサンプルが表示されます。 フィルター アイコンをクリックすると、任意の時点で適用された条件が表示されます。

![[フィルター] ボタン](./media/live-stream/filter.png)

メトリックと同様に、Application Insights Telemetry の種類のいずれかに任意の条件を指定できます。 この例では、特定の要求エラーとイベントを選択しています。

![[クエリ ビルダー]](./media/live-stream/query-builder.png)

> [!NOTE]
> 現時点では、例外メッセージ ベースの条件には、最も外側の例外メッセージを使用します。 前の例では、"クライアントが切断されました。" という内部例外メッセージ ("<--" 区切り記号の後) が示されている害のない例外を除外するために、 "Error reading request content (要求内容の読み取りエラー)" が含まれていないメッセージという条件を使用します。

ライブ フィードの項目をクリックして詳細を表示します。 フィードを一時停止するには、 **[一時停止]** をクリックするか、下にスクロールするか、または項目をクリックします。 スクロールして上部に戻るか、一時停止されている間に収集された項目のカウンターをクリックすると、ライブ フィードが再開されます。

![スクリーンショットには、例外が選択され、ウィンドウの下部に例外の詳細が表示された [Sample telemetry]\(サンプル テレメトリ\) ウィンドウが示されています。](./media/live-stream/sample-telemetry.png)

## <a name="filter-by-server-instance"></a>サーバー インスタンスによるフィルター処理

特定のサーバー ロール インスタンスを監視する場合は、サーバーでフィルター処理できます。 フィルターを適用するには、" *[サーバー]* " でサーバー名を選択します。

![サンプリングされたライブ エラー](./media/live-stream/filter-by-server.png)

## <a name="secure-the-control-channel"></a>コントロール チャネルの保護

> [!NOTE]
> 現時点では、コード ベースの監視を使用して認証されたチャネルのみを設定できます。コードなしアタッチを使用してサーバーを認証することはできません。

LiveMetrics ポータルで指定したカスタム フィルター条件は、Application Insights SDK の Live Metrics コンポーネントに送信されます。 フィルターに顧客 ID などの機密情報が含まれている可能性があります。 インストルメンテーション キーに加え、シークレット API キーを使用してチャネルをセキュリティで保護できます。

### <a name="create-an-api-key"></a>API キーを作成する

![[API キー] > [API キーの作成]](./media/live-stream/api-key.png)
![[API キーの作成] タブ。[SDK コントロール チャネルの認証]、[キーの生成] の順に選択する](./media/live-stream/create-api-key.png)

### <a name="add-api-key-to-configuration"></a>API キーを構成に追加する

### <a name="aspnet"></a>ASP.NET

applicationinsights.config ファイルで、AuthenticationApiKey を QuickPulseTelemetryModule に追加します。

```XML
<Add Type="Microsoft.ApplicationInsights.Extensibility.PerfCounterCollector.QuickPulse.QuickPulseTelemetryModule, Microsoft.AI.PerfCounterCollector">
      <AuthenticationApiKey>YOUR-API-KEY-HERE</AuthenticationApiKey>
</Add>
```

### <a name="aspnet-core"></a>ASP.NET Core

[ASP.NET Core](./asp-net-core.md) アプリケーションについては、以下の指示に従ってください。

Startup.cs ファイルの `ConfigureServices` を次のように変更します。

次の名前空間を追加します。

```csharp
using Microsoft.ApplicationInsights.Extensibility.PerfCounterCollector.QuickPulse;
```

次に、`ConfigureServices` メソッドを次のように変更します。

```csharp
public void ConfigureServices(IServiceCollection services)
{
    // existing code which include services.AddApplicationInsightsTelemetry() to enable Application Insights.
    services.ConfigureTelemetryModule<QuickPulseTelemetryModule> ((module, o) => module.AuthenticationApiKey = "YOUR-API-KEY-HERE");
}
```

ASP.NET Core アプリケーションを構成する方法については、[ASP.NET Core でのテレメトリ モジュールの構成](./asp-net-core.md#configuring-or-removing-default-telemetrymodules)に関する Microsoft のガイダンスにあります。

### <a name="workerservice"></a>WorkerService

[WorkerService](./worker-service.md) アプリケーションについては、以下の指示に従ってください。

次の名前空間を追加します。

```csharp
using Microsoft.ApplicationInsights.Extensibility.PerfCounterCollector.QuickPulse;
```

次に、呼び出し `services.AddApplicationInsightsTelemetryWorkerService` の前に次の行を追加します。

```csharp
    services.ConfigureTelemetryModule<QuickPulseTelemetryModule> ((module, o) => module.AuthenticationApiKey = "YOUR-API-KEY-HERE");
```

WorkerService アプリケーションを構成する方法については、[WorkerServices でのテレメトリ モジュールの構成](./worker-service.md#configuring-or-removing-default-telemetrymodules)に関する Microsoft のガイダンスにあります。

### <a name="azure-function-apps"></a>Azure Function App

Azure Function App (v2) の場合、API キーを使用してチャネルをセキュリティで保護するには、環境変数を使用して完了することができます。

Application Insights リソース内から API キーを作成し、Function App の **[設定]、[構成]** の順に移動します。 **[新しいアプリケーション設定]** を選択し、`APPINSIGHTS_QUICKPULSEAUTHAPIKEY` の名前と、API キーに対応する値を入力します。

ただし、接続されているすべてのサーバーを認識し、信頼している場合は、認証済みチャネルなしにカスタム フィルターを試すことができます。 このオプションは 6 か月間使用できます。 このオーバーライドは、新しいセッションごとに 1 回、または新しいサーバーがオンラインになったときに必要になります。

![Live Metrics の認証オプション](./media/live-stream/live-stream-auth.png)

>[!NOTE]
>フィルター条件に CustomerID などの機密情報を入力する前に、認証済みチャネルを設定することを強くお勧めします。
>

## <a name="supported-features-table"></a>サポートされる機能の表

| Language                         | 基本メトリック       | パフォーマンス メトリック | カスタム フィルター処理    | サンプル テレメトリ    | プロセス別の CPU 分割 |
|----------------------------------|:--------------------|:--------------------|:--------------------|:--------------------|:---------------------|
| .NET Framework                   | サポート対象 (V2.7.2 以降) | サポート対象 (V2.7.2 以降) | サポート対象 (V2.7.2 以降) | サポート対象 (V2.7.2 以降) | サポート対象 (V2.7.2 以降)  |
| .NET Core (ターゲット =.NET Framework)| サポート対象 (V2.4.1 以降) | サポート対象 (V2.4.1 以降) | サポート対象 (V2.4.1 以降) | サポート対象 (V2.4.1 以降) | サポート対象 (V2.4.1 以降)  |
| .NET Core (ターゲット =.NET Core)     | サポート対象 (V2.4.1 以降) | サポート対象*          | サポート対象 (V2.4.1 以降) | サポート対象 (V2.4.1 以降) | **サポートされていません**    |
| Azure Functions v2               | サポートされています           | サポートされています           | サポートされています           | サポートされています           | **サポートされていません**    |
| Java                             | サポート対象 (V2.0.0 以降) | サポート対象 (V2.0.0 以降) | **サポートされていません**   | **サポートされていません**   | **サポートされていません**    |
| Node.js                          | サポート対象 (V1.3.0 以降) | サポート対象 (V1.3.0 以降) | **サポートされていません**   | サポート対象 (V1.3.0 以降) | **サポートされていません**    |

基本メトリックには、要求、依存関係、例外率が含まれます。 パフォーマンス メトリック (パフォーマンス カウンター) には、メモリと CPU が含まれます。 テレメトリのサンプルには、失敗した要求と依存関係、例外、イベント、トレースに関する詳細情報のストリームが表示されます。

 \* PerfCounters のサポートは、.NET Framework を対象としない .NET Core のバージョンによって多少異なります。

- PerfCounters メトリックは、Windows の Azure App Service で実行する場合にサポートされます。 (AspNetCore SDK バージョン 2.4.1 以降)
- PerfCounters は、アプリが任意の Windows マシン (VM またはクラウド サービスまたはオンプレミスなど) (AspNetCore SDK バージョン 2.7.1 以降) で実行されている場合にサポートされますが、.NET Core 2.0 以降を対象とするアプリ用です。
- PerfCounters は、最新版 (すなわち、AspNetCore SDK バージョン 2.8.0 以降) では、アプリの実行場所 (Linux、Windows、Linux のアプリ サービス、コンテナーなど) を問わず、サポートされますが、.NET Core 2.0 以降を対象とするアプリに限られます。

## <a name="troubleshooting"></a>トラブルシューティング

Live Metrics Stream では、他の Application Insights テレメトリとは異なる IP アドレスを使用します。 [これらの IP アドレス](./ip-addresses.md)がファイアウォールで開いていることを確認してください。 また、サーバーのファイアウォールで、[Live Metrics Stream の発信ポート](./ip-addresses.md#outgoing-ports)が開いているか確認します。

## <a name="next-steps"></a>次のステップ

* [Application Insights による使用状況の監視](./usage-overview.md)
* [診断検索の使用](./diagnostic-search.md)
* [Profiler](./profiler.md)
* [スナップショット デバッガー](./snapshot-debugger.md)