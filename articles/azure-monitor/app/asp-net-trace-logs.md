---
title: Application Insights の .NET トレース ログを調べる
description: Trace、NLog、または Log4Net で生成されたログを検索します。
services: application-insights
documentationcenter: .net
author: mrbullwinkle
manager: carmonm
ms.assetid: 0c2a084f-6e71-467b-a6aa-4ab222f17153
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 05/08/2019
ms.author: mbullwin
ms.openlocfilehash: 125f1bc14a376523a22984e9d8efa7848408bf7a
ms.sourcegitcommit: 94ee81a728f1d55d71827ea356ed9847943f7397
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/26/2019
ms.locfileid: "70035217"
---
# <a name="explore-netnet-core-trace-logs-in-application-insights"></a>Application Insights で .NET/.NET Core のトレース ログを調べる

ASP.NET または ASP.NET Core アプリケーションの診断トレース ログを ILogger、NLog、log4Net、または System.Diagnostics.Trace から [Azure Application Insights][start] に送信します。 その後、探索して検索できます。 これらのログはアプリケーションからの他のログ ファイルと結合されます。したがって、各ユーザー要求に関連付けられているトレースを特定し、それらを他のイベントや例外レポートに関連付けることができます。

> [!NOTE]
> ログ キャプチャ モジュールは必要ですか。 これは、サード パーティ製のロガーの場合に便利なアダプターです。 しかし、NLog、log4Net、または System.Diagnostics.Trace をまだ使用していない場合は、単に [**Application Insights TrackTrace()** ](../../azure-monitor/app/api-custom-events-metrics.md#tracktrace) を直接呼び出すことを検討してください。
>
>
## <a name="install-logging-on-your-app"></a>アプリにログ記録フレームワークをインストールする
プロジェクトで選択したログ記録フレームワークをインストールします。これにより、app.config または web.config にエントリが追加されます。

```XML
 <configuration>
  <system.diagnostics>
    <trace>
      <listeners>
        <add name="myAppInsightsListener" type="Microsoft.ApplicationInsights.TraceListener.ApplicationInsightsTraceListener, Microsoft.ApplicationInsights.TraceListener" />
      </listeners>
    </trace>
  </system.diagnostics>
</configuration>
```

## <a name="configure-application-insights-to-collect-logs"></a>ログを収集するよう Application Insights を構成する
[Application Insights をプロジェクトに追加](../../azure-monitor/app/asp-net.md)します (まだ追加していない場合)。 Log Collector を含めるオプションが表示されます。

または、ソリューション エクスプローラーでプロジェクトを右クリックし、**Application Insights を構成**します。 **[トレース コレクションの構成]** オプションを選択します。

> [!NOTE]
> Application Insights のメニューやログ コレクターのオプションが表示されない場合は、 [トラブルシューティング](#troubleshooting)をお試しください。

## <a name="manual-installation"></a>手動のインストール
Application Insights インストーラーでサポートされていない種類のプロジェクト (Windows デスクトップ プロジェクトなど) の場合は、手動でインストールします。

1. log4Net または NLog を使用する場合は、プロジェクト内にインストールします。
2. ソリューション エクスプローラーで、プロジェクトを右クリックし、 **[NuGet パッケージの管理]** を選択します。
3. "Application Insights" を検索します。
4. 次のいずれかのパッケージを選択します。

   - ILogger の場合:[Microsoft.Extensions.Logging.ApplicationInsights](https://www.nuget.org/packages/Microsoft.Extensions.Logging.ApplicationInsights/)
[![NuGet](https://img.shields.io/nuget/vpre/Microsoft.Extensions.Logging.ApplicationInsights.svg)](https://www.nuget.org/packages/Microsoft.Extensions.Logging.ApplicationInsights/)
   - NLog の場合:[Microsoft.ApplicationInsights.NLogTarget](https://www.nuget.org/packages/Microsoft.ApplicationInsights.NLogTarget/)
[![NuGet](https://img.shields.io/nuget/vpre/Microsoft.ApplicationInsights.NLogTarget.svg)](https://www.nuget.org/packages/Microsoft.ApplicationInsights.NLogTarget/)
   - Log4Net の場合:[Microsoft.ApplicationInsights.Log4NetAppender](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Log4NetAppender/)
[![NuGet](https://img.shields.io/nuget/vpre/Microsoft.ApplicationInsights.Log4NetAppender.svg)](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Log4NetAppender/)
   - System.Diagnostics の場合:[Microsoft.ApplicationInsights.TraceListener](https://www.nuget.org/packages/Microsoft.ApplicationInsights.TraceListener/)
[![NuGet](https://img.shields.io/nuget/vpre/Microsoft.ApplicationInsights.TraceListener.svg)](https://www.nuget.org/packages/Microsoft.ApplicationInsights.TraceListener/)
   - [Microsoft.ApplicationInsights.DiagnosticSourceListener](https://www.nuget.org/packages/Microsoft.ApplicationInsights.DiagnosticSourceListener/)
[![NuGet](https://img.shields.io/nuget/vpre/Microsoft.ApplicationInsights.DiagnosticSourceListener.svg)](https://www.nuget.org/packages/Microsoft.ApplicationInsights.DiagnosticSourceListener/)
   - [Microsoft.ApplicationInsights.EtwCollector](https://www.nuget.org/packages/Microsoft.ApplicationInsights.EtwCollector/)
[![NuGet](https://img.shields.io/nuget/vpre/Microsoft.ApplicationInsights.EtwCollector.svg)](https://www.nuget.org/packages/Microsoft.ApplicationInsights.EtwCollector/)
   - [Microsoft.ApplicationInsights.EventSourceListener](https://www.nuget.org/packages/Microsoft.ApplicationInsights.EventSourceListener/)
[![Nuget](https://img.shields.io/nuget/vpre/Microsoft.ApplicationInsights.EventSourceListener.svg)](https://www.nuget.org/packages/Microsoft.ApplicationInsights.EventSourceListener/)

NuGet パッケージでは、必要なアセンブリがインストールされ、必要に応じて web.config または app.config が変更されます。

## <a name="ilogger"></a>ILogger

コンソール アプリケーションと ASP.NET Core で Application Insights ILogger の実装を使用する例については、「[ApplicationInsightsLoggerProvider for .NET Core の ILogger ログ](ilogger.md)」を参照してください。

## <a name="insert-diagnostic-log-calls"></a>診断ログの呼び出しを挿入する
System.Diagnostics.Trace を使用する場合、通常の呼び出しは次のようになります。

    System.Diagnostics.Trace.TraceWarning("Slow response - database01");

log4net または NLog を使う場合は、以下を使用します。

    logger.Warn("Slow response - database01");

## <a name="use-eventsource-events"></a>EventSource イベントを使用する
Application Insights にトレースとして送信する [System.Diagnostics.Tracing.EventSource](https://msdn.microsoft.com/library/system.diagnostics.tracing.eventsource.aspx) イベントを構成できます。 まず、`Microsoft.ApplicationInsights.EventSourceListener` NuGet パッケージをインストールします。 次に、[ApplicationInsights.config](../../azure-monitor/app/configuration-with-applicationinsights-config.md) ファイルの `TelemetryModules` セクションを編集します。

```xml
    <Add Type="Microsoft.ApplicationInsights.EventSourceListener.EventSourceTelemetryModule, Microsoft.ApplicationInsights.EventSourceListener">
      <Sources>
        <Add Name="MyCompany" Level="Verbose" />
      </Sources>
    </Add>
```

ソースごとに、次のパラメーターを設定できます。
 * **Name** では、収集する EventSource の名前を指定します。
 * **Level** では、収集するログ レベルを指定します。*Critical*、*Error*、*Informational*、*LogAlways*、*Verbose*、*Warning* があります。
 * **Keywords** (省略可能) では、使用するキーワードの組み合わせの整数値を指定します。

## <a name="use-diagnosticsource-events"></a>DiagnosticSource イベントを使用する
Application Insights にトレースとして送信する [System.Diagnostics.DiagnosticSource](https://github.com/dotnet/corefx/blob/master/src/System.Diagnostics.DiagnosticSource/src/DiagnosticSourceUsersGuide.md) イベントを構成できます。 まず、[`Microsoft.ApplicationInsights.DiagnosticSourceListener`](https://www.nuget.org/packages/Microsoft.ApplicationInsights.DiagnosticSourceListener) NuGet パッケージをインストールします。 次に、[ApplicationInsights.config](../../azure-monitor/app/configuration-with-applicationinsights-config.md) ファイルの "TelemetryModules" セクションを編集します。

```xml
    <Add Type="Microsoft.ApplicationInsights.DiagnosticSourceListener.DiagnosticSourceTelemetryModule, Microsoft.ApplicationInsights.DiagnosticSourceListener">
      <Sources>
        <Add Name="MyDiagnosticSourceName" />
      </Sources>
    </Add>
```

トレースする DiagnosticSource ごとに、DiagnosticSource の名前に設定された **Name** 属性を持つエントリを追加します。

## <a name="use-etw-events"></a>ETW イベントを使用する
Application Insights にトレースとして送信される Event Tracing for Windows (ETW) イベントを構成できます。 まず、`Microsoft.ApplicationInsights.EtwCollector` NuGet パッケージをインストールします。 次に、[ApplicationInsights.config](../../azure-monitor/app/configuration-with-applicationinsights-config.md) ファイルの "TelemetryModules" セクションを編集します。

> [!NOTE] 
> ETW イベントを収集できるのは、SDK をホストするプロセスが、Performance Log Users または Administrators のメンバーである ID で実行されている場合だけです。

```xml
    <Add Type="Microsoft.ApplicationInsights.EtwCollector.EtwCollectorTelemetryModule, Microsoft.ApplicationInsights.EtwCollector">
      <Sources>
        <Add ProviderName="MyCompanyEventSourceName" Level="Verbose" />
      </Sources>
    </Add>
```

ソースごとに、次のパラメーターを設定できます。
 * **ProviderName** は、収集する ETW プロバイダーの名前です。
 * **ProviderGuid** では、収集する ETW プロバイダーの GUID を指定します。 `ProviderName` の代わりに使用できます。
 * **Level** では、収集するログ レベルを設定します。 *Critical*、*Error*、*Informational*、*LogAlways*、*Verbose*、または *Warning* にすることができます。
 * **Keywords** (省略可能) では、使用するキーワードの組み合わせの整数値を設定します。

## <a name="use-the-trace-api-directly"></a>トレース API を直接使用する
Application Insights トレース API を直接呼び出すことができます。 ログ記録のアダプターはこの API を使用します。

例:

    var telemetry = new Microsoft.ApplicationInsights.TelemetryClient();
    telemetry.TrackTrace("Slow response - database01");

TrackTrace の利点は、比較的長いデータをメッセージの中に配置できることです。 たとえば、メッセージ中で POST データをエンコードできます。

メッセージに重大度レベルを追加することもできます。 また、他のテレメトリと同様、プロパティ値を追加することで、さまざまなトレース セットをフィルター処理したり、検索したりすることができます。 例:

    var telemetry = new Microsoft.ApplicationInsights.TelemetryClient();
    telemetry.TrackTrace("Slow database response",
                   SeverityLevel.Warning,
                   new Dictionary<string,string> { {"database", db.ID} });

これにより、特定のデータベースに関連する、特定の重大度レベルのメッセージすべてを、[[検索]][diagnostic] で簡単に抽出できます。

## <a name="explore-your-logs"></a>ログを調査する
アプリをデバッグ モードで実行するか、ライブでデプロイします。

[Application Insights ポータル][portal]にあるアプリの概要ウィンドウで、[[検索]][diagnostic] を選択します。

たとえば、次の操作ができます。

* ログ トレースまたは特定のプロパティを持つ項目をフィルター処理する。
* 特定の項目の詳細に調べる
* 同じユーザー要求に関連する (つまり、OperationId が同じ) 他のシステム ログ データを探す。
* ページの構成をお気に入りとして保存する。

> [!NOTE]
>アプリケーションで大量のデータが送信され、Application Insights SDK for ASP.NET バージョン 2.0.0-beta3 以降を使用している場合は、"*アダプティブ サンプリング*" 機能が動作して、テレメトリの一部のみが送信される可能性があります。 [サンプリングの詳細については、こちらを参照してください。](../../azure-monitor/app/sampling.md)
>

## <a name="troubleshooting"></a>トラブルシューティング
### <a name="how-do-i-do-this-for-java"></a>Java の場合はどうすればよいですか。
[Java ログ アダプター](../../azure-monitor/app/java-trace-logs.md)を使用します。

### <a name="theres-no-application-insights-option-on-the-project-context-menu"></a>プロジェクトのコンテキスト メニューに Application Insights のオプションがありません
* 開発用マシンに Developer Analytics Tools がインストールしてあることを確認します。 Visual Studio の **[ツール]**  >  **[拡張機能と更新プログラム]** で、**Developer Analytics Tools** を探します。 **[インストール済み]** タブにない場合は、 **[オンライン]** タブを開いてインストールします。
* これは、Developer Analytics Tools でサポートされていない種類のプロジェクトの可能性があります。 [手動でインストール](#manual-installation)してください。

### <a name="theres-no-log-adapter-option-in-the-configuration-tool"></a>構成ツールにログ アダプターのオプションがありません
* まず、ログ記録フレームワークをインストールします。
* System.Diagnostics.Trace を使用している場合は、[*web.config* で構成済み](https://msdn.microsoft.com/library/system.diagnostics.eventlogtracelistener.aspx)であることを確認します。
* 最新バージョンの Application Insights があることを確認します。 Visual Studio で、 **[ツール]**  >  **[拡張機能と更新プログラム]** の順に移動し、 **[更新]** タブを開きます。そこに **Developer Analytics Tools** がある場合は、それを選択して更新します。

### <a name="emptykey"></a>"インストルメンテーション キーは空にできません" というエラーメッセージが表示されました
Application Insights をインストールしないでログ アダプターの Nuget パッケージをインストールした可能性があります。 ソリューション エクスプローラーで、*ApplicationInsights.config* を右クリックし、 **[Application Insights の更新]** を選択します。 Azure にサインインし、Application Insights リソースを作成するか、既存のものを再利用することを求めるメッセージが表示されます。 これで問題は修正されます。

### <a name="i-can-see-traces-but-not-other-events-in-diagnostic-search"></a>診断検索にトレースが表示されますが、他のイベントがありません
すべてのイベントと要求がパイプラインを通過するまでしばらく時間がかかることがあります。

### <a name="limits"></a>保持されるデータの量はどのくらいですか
いくつかの要因が、保持されるデータの量に影響します。 詳細については、顧客イベント メトリック ページの「[制限](../../azure-monitor/app/api-custom-events-metrics.md#limits)」セクションを参照してください。

### <a name="i-dont-see-some-log-entries-that-i-expected"></a>予期していたいくつかのログ エントリが表示されません
アプリケーションで膨大な量のデータが送信され、Application Insights SDK for ASP.NET バージョン 2.0.0-beta3 以降を使用している場合は、アダプティブ サンプリング機能が動作して、テレメトリの一部のみが送信される可能性があります。 [サンプリングの詳細については、こちらを参照してください。](../../azure-monitor/app/sampling.md)

## <a name="add"></a>次のステップ

* [ASP.NET のエラーと例外を診断する][exceptions]
* [検索についてさらに学習する][diagnostic]
* [可用性と応答性のテストを設定する][availability]
* [トラブルシューティング][qna]

<!--Link references-->

[availability]: ../../azure-monitor/app/monitor-web-app-availability.md
[diagnostic]: ../../azure-monitor/app/diagnostic-search.md
[exceptions]: asp-net-exceptions.md
[portal]: https://portal.azure.com/
[qna]: ../../azure-monitor/app/troubleshoot-faq.md
[start]: ../../azure-monitor/app/app-insights-overview.md