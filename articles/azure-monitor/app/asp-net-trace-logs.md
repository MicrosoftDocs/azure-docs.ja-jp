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
ms.date: 02/19/2019
ms.author: mbullwin
ms.openlocfilehash: 8c722eb0db3022620ba03e02dd2ae00f97a78f28
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/07/2019
ms.locfileid: "57552148"
---
# <a name="explore-netnet-core-trace-logs-in-application-insights"></a>Application Insights で .NET/.NET Core のトレース ログを調べる

ASP.NET/ASP.NET Core アプリケーションで診断トレースに ILogger、NLog、log4Net、または System.Diagnostics.Trace を使用している場合、ログを [Azure Application Insights][start] に送信し、そこで調査したり、検索したりできます。 ログはアプリケーションから送信される他の利用統計情報と結合されます。それにより、互いのユーザー要求にサービスを提供することに関連付けられているトレースを特定し、それらを他のイベントや例外レポートに相互に関連付けることができます。

> [!NOTE]
> ログ キャプチャ モジュールは必要ですか。 ログ キャプチャ モジュールは、サード パーティ製のロガーの場合は便利なアダプターですが、NLog、log4Net、または System.Diagnostics.Trace をまだ使用していない場合は、 [Application Insights TrackTrace()](../../azure-monitor/app/api-custom-events-metrics.md#tracktrace) を直接呼び出すことを検討してください。
>
>

## <a name="install-logging-on-your-app"></a>アプリにログ記録フレームワークをインストールする
選択したログ記録フレームワークをプロジェクトにインストールします。 インストールすると、app.config か web.config にエントリが追加されます。

```XML
    <configuration>
      <system.diagnostics>
    <trace autoflush="true" indentsize="0">
      <listeners>
        <add name="myAppInsightsListener" type="Microsoft.ApplicationInsights.TraceListener.ApplicationInsightsTraceListener, Microsoft.ApplicationInsights.TraceListener" />
      </listeners>
    </trace>
  </system.diagnostics>
   </configuration>
```

## <a name="configure-application-insights-to-collect-logs"></a>ログを収集するよう Application Insights を構成する
**[Application Insights をプロジェクトに追加](../../azure-monitor/app/asp-net.md)** します (まだ追加していない場合)。 Log Collector を含めるオプションが表示されます。

または、ソリューション エクスプローラーでプロジェクトを右クリックし、 **Application Insights を構成** します。 **[トレース コレクションの構成]** を選択します。

*Application Insights のメニューや Log Collector のオプションが表示されない場合は、*  [トラブルシューティング](#troubleshooting)をお試しください。

## <a name="manual-installation"></a>手動のインストール
Application Insights インストーラーでサポートされていない種類のプロジェクト (Windows デスクトップ プロジェクトなど) の場合は、手動でインストールします。

1. log4Net または NLog を使用する場合は、プロジェクト内にインストールします。
2. ソリューション エクスプローラーでプロジェクトを右クリックし、**[NuGet パッケージの管理]** を選択します。
3. Search for "Application Insights"
4. 次のいずれかのパッケージを選択します。

   - ILogger の場合:[Microsoft.Extensions.Logging.ApplicationInsights](https://www.nuget.org/packages/Microsoft.Extensions.Logging.ApplicationInsights/)
[![Nuget](https://img.shields.io/nuget/vpre/Microsoft.Extensions.Logging.ApplicationInsights.svg)](https://www.nuget.org/packages/Microsoft.Extensions.Logging.ApplicationInsights/)
   - NLog の場合:[Microsoft.ApplicationInsights.NLogTarget](https://www.nuget.org/packages/Microsoft.ApplicationInsights.NLogTarget/)
[![Nuget](https://img.shields.io/nuget/vpre/Microsoft.ApplicationInsights.NLogTarget.svg)](https://www.nuget.org/packages/Microsoft.ApplicationInsights.NLogTarget/)
   - Log4Net の場合:[Microsoft.ApplicationInsights.Log4NetAppender](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Log4NetAppender/)
[![Nuget](https://img.shields.io/nuget/vpre/Microsoft.ApplicationInsights.Log4NetAppender.svg)](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Log4NetAppender/)
   - System.Diagnostics の場合:[Microsoft.ApplicationInsights.TraceListener](https://www.nuget.org/packages/Microsoft.ApplicationInsights.TraceListener/)
[![Nuget](https://img.shields.io/nuget/vpre/Microsoft.ApplicationInsights.TraceListener.svg)](https://www.nuget.org/packages/Microsoft.ApplicationInsights.TraceListener/)
   - [Microsoft.ApplicationInsights.DiagnosticSourceListener](https://www.nuget.org/packages/Microsoft.ApplicationInsights.DiagnosticSourceListener/)
[![Nuget](https://img.shields.io/nuget/vpre/Microsoft.ApplicationInsights.DiagnosticSourceListener.svg)](https://www.nuget.org/packages/Microsoft.ApplicationInsights.DiagnosticSourceListener/)
   - [Microsoft.ApplicationInsights.EtwCollector](https://www.nuget.org/packages/Microsoft.ApplicationInsights.EtwCollector/)
[![Nuget](https://img.shields.io/nuget/vpre/Microsoft.ApplicationInsights.EtwCollector.svg)](https://www.nuget.org/packages/Microsoft.ApplicationInsights.EtwCollector/)
   - [Microsoft.ApplicationInsights.EventSourceListener](https://www.nuget.org/packages/Microsoft.ApplicationInsights.EventSourceListener/)
[![Nuget](https://img.shields.io/nuget/vpre/Microsoft.ApplicationInsights.EventSourceListener.svg)](https://www.nuget.org/packages/Microsoft.ApplicationInsights.EventSourceListener/)

NuGet パッケージでは、必要なアセンブリがインストールされ、必要に応じて web.config または app.config が変更されます。

## <a name="ilogger"></a>ILogger

コンソール アプリケーションと ASP.NET Core で Application Insights ILogger の実装を使用する例については、こちらの[記事](ilogger.md)をご覧ください。

## <a name="insert-diagnostic-log-calls"></a>診断ログの呼び出しを挿入する
System.Diagnostics.Trace を使用する場合、通常の呼び出しは次のようになります。

    System.Diagnostics.Trace.TraceWarning("Slow response - database01");

log4net または NLog を使用する場合:

    logger.Warn("Slow response - database01");

## <a name="using-eventsource-events"></a>EventSource イベントを使用する
Application Insights にトレースとして送信する [System.Diagnostics.Tracing.EventSource](https://msdn.microsoft.com/library/system.diagnostics.tracing.eventsource.aspx) イベントを構成できます。 まず、`Microsoft.ApplicationInsights.EventSourceListener` NuGet パッケージをインストールします。 次に、[ApplicationInsights.config](../../azure-monitor/app/configuration-with-applicationinsights-config.md) ファイルの `TelemetryModules` セクションを編集します。

```xml
    <Add Type="Microsoft.ApplicationInsights.EventSourceListener.EventSourceTelemetryModule, Microsoft.ApplicationInsights.EventSourceListener">
      <Sources>
        <Add Name="MyCompany" Level="Verbose" />
      </Sources>
    </Add>
```

ソースごとに、次のパラメーターを設定できます。
 * `Name` では、収集する EventSource の名前を指定します。
 * `Level` では、収集するログ レベルを指定します。 `Critical`、`Error`、`Informational`、`LogAlways`、`Verbose`、`Warning` のいずれかを指定できます。
 * `Keywords` (省略可能) では、使用するキーワードの組み合わせの整数値を指定します。

## <a name="using-diagnosticsource-events"></a>DiagnosticSource イベントの使用
Application Insights にトレースとして送信する [System.Diagnostics.DiagnosticSource](https://github.com/dotnet/corefx/blob/master/src/System.Diagnostics.DiagnosticSource/src/DiagnosticSourceUsersGuide.md) イベントを構成できます。 まず、[`Microsoft.ApplicationInsights.DiagnosticSourceListener`](https://www.nuget.org/packages/Microsoft.ApplicationInsights.DiagnosticSourceListener) NuGet パッケージをインストールします。 次に、[ApplicationInsights.config](../../azure-monitor/app/configuration-with-applicationinsights-config.md) ファイルの `TelemetryModules` セクションを編集します。

```xml
    <Add Type="Microsoft.ApplicationInsights.DiagnosticSourceListener.DiagnosticSourceTelemetryModule, Microsoft.ApplicationInsights.DiagnosticSourceListener">
      <Sources>
        <Add Name="MyDiagnosticSourceName" />
      </Sources>
    </Add>
```

トレースする DiagnosticSource ごとに、DiagnosticSource の名前に設定された `Name` 属性を持つエントリを追加します。

## <a name="using-etw-events"></a>ETW イベントを使用する
Application Insights にトレースとして送信される ETW イベントを構成できます。 まず、`Microsoft.ApplicationInsights.EtwCollector` NuGet パッケージをインストールします。 次に、[ApplicationInsights.config](../../azure-monitor/app/configuration-with-applicationinsights-config.md) ファイルの `TelemetryModules` セクションを編集します。

> [!NOTE] 
> ETW イベントを収集できるのは、SDK をホストしているプロセスが、"Performance Log Users" または Administrators のメンバーである ID で実行されている場合だけです。

```xml
    <Add Type="Microsoft.ApplicationInsights.EtwCollector.EtwCollectorTelemetryModule, Microsoft.ApplicationInsights.EtwCollector">
      <Sources>
        <Add ProviderName="MyCompanyEventSourceName" Level="Verbose" />
      </Sources>
    </Add>
```

ソースごとに、次のパラメーターを設定できます。
 * `ProviderName` は、収集する ETW プロバイダーの名前です。
 * `ProviderGuid` では、収集する ETW プロバイダーの GUID を指定します。これは `ProviderName` の代わりに使用できます。
 * `Level` では、収集するログ レベルを設定します。 `Critical`、`Error`、`Informational`、`LogAlways`、`Verbose`、`Warning` のいずれかを指定できます。
 * `Keywords` (省略可能) では、使用するキーワードの組み合わせの整数値を設定します。

## <a name="using-the-trace-api-directly"></a>トレース API を直接利用する
Application Insights トレース API を直接呼び出すことができます。 ログ記録のアダプターはこの API を使用します。

例: 

    var telemetry = new Microsoft.ApplicationInsights.TelemetryClient();
    telemetry.TrackTrace("Slow response - database01");

TrackTrace の利点は、比較的長いデータをメッセージの中に配置できることです。 たとえば、その中に POST データをエンコードできます。

加えて、メッセージに重大度レベルを追加することができます。 また他のテレメトリと同様、プロパティ値を追加することで、さまざまなトレースの組み合わせをフィルタで抽出したり検索したりすることができます。 例: 

    var telemetry = new Microsoft.ApplicationInsights.TelemetryClient();
    telemetry.TrackTrace("Slow database response",
                   SeverityLevel.Warning,
                   new Dictionary<string,string> { {"database", db.ID} });

特定のデータベースに関連する特定の重大度レベルに該当するすべてのメッセージを、[検索][diagnostic]で簡単に抽出できます。

## <a name="explore-your-logs"></a>ログを調査する
アプリをデバッグ モードで実行するかデプロイします。

[Application Insights ポータル][portal]内のアプリの概要ブレードで、[[検索]][diagnostic] を選択します。

たとえば、次の操作ができます。

* ログ トレースまたは特定のプロパティを持つ項目をフィルター処理する
* 特定の項目の詳細に調べる
* 同じユーザー要求に関連する (つまり、OperationId が同じ) 他の利用統計情報を探す
* このページの構成をお気に入りとして保存する

> [!NOTE]
> **サンプリング。** アプリケーションが送信するデータ量が多く、Application Insights SDK for ASP.NET バージョン 2.0.0-beta3 以降を使用している場合は、アダプティブ サンプリング機能が動作して、テレメトリの一定の割合のみが送信される可能性があります。 [サンプリングの詳細については、こちらを参照してください。](../../azure-monitor/app/sampling.md)
>
>

## <a name="next-steps"></a>次の手順
[ASP.NET ][exceptions]のエラーと例外を診断する

[検索][diagnostic]の詳細についてはこちらを参照してください。

## <a name="troubleshooting"></a>トラブルシューティング
### <a name="how-do-i-do-this-for-java"></a>Java の場合はどうすればよいですか。
[Java ログ アダプター](../../azure-monitor/app/java-trace-logs.md)を使用します。

### <a name="theres-no-application-insights-option-on-the-project-context-menu"></a>プロジェクトのコンテキスト メニューに Application Insights のオプションがありません
* お使いの開発用コンピューターに Application Insights Tools がインストールされているかを確認してください。 Visual Studio の [ツール] メニューで [拡張機能と更新プログラム] を選択し、Application Insights Tools を探します。 Application Insights Tools が [インストール済み] タブにない場合は、[オンライン] タブを開いてインストールします。
* Application Insights Tools でサポートされていない種類のプロジェクトである可能性があります。 [手動でインストール](#manual-installation)してください。

### <a name="no-log-adapter-option-in-the-configuration-tool"></a>構成ツールにログ アダプターのオプションがありません
* まずログ記録フレームワークをインストールする必要があります。
* System.Diagnostics.Trace を使用している場合は、[`web.config`で構成済み](https://msdn.microsoft.com/library/system.diagnostics.eventlogtracelistener.aspx)であることを確認します。
* 最新バージョンの Application Insights を使用しているか確認します。 Visual Studio の **[ツール]** メニューで **[拡張機能と更新プログラム]** を選択し、**[更新]** タブを開きます。Developer Analytics Tools が表示されていたら、クリックして更新します。

### <a name="emptykey"></a>エラー「インストルメンテーション キーは空にできません」が発生しました
Application Insights をインストールしないでログ アダプターの Nuget パッケージをインストールした可能性があります。

ソリューション エクスプローラーで、 `ApplicationInsights.config` を右クリックし、**[Application Insights の更新]** を選択します。 Azure へのサインインを促すダイアログが表示されます。または、Application Insights のリソースを作成するか、既存のリソースを再利用します。 これで問題は修正されます。

### <a name="i-can-see-traces-in-diagnostic-search-but-not-the-other-events"></a>診断検索にトレースが表示されますが、他のイベントがありません
すべてのイベントと要求がパイプラインを通過するまで時間がかかることがあります。

### <a name="limits"></a>保持されるデータの量はどのくらいですか
保持されるデータの量には、さまざまな要因が影響します。 詳細については、カスタムのイベント メトリックに関するページの「[制限](../../azure-monitor/app/api-custom-events-metrics.md#limits)」セクションを参照してください。 

### <a name="im-not-seeing-some-of-the-log-entries-that-i-expect"></a>予期されるログ エントリの一部が表示されません
アプリケーションが送信するデータ量が多く、Application Insights SDK for ASP.NET バージョン 2.0.0-beta3 以降を使用している場合は、アダプティブ サンプリング機能が動作して、テレメトリの一定の割合のみが送信される可能性があります。 [サンプリングの詳細については、こちらを参照してください。](../../azure-monitor/app/sampling.md)

## <a name="add"></a>次のステップ
* [可用性と応答性のテストを設定する][availability]
* [Troubleshooting][qna]

<!--Link references-->

[availability]: ../../azure-monitor/app/monitor-web-app-availability.md
[diagnostic]: ../../azure-monitor/app/diagnostic-search.md
[exceptions]: asp-net-exceptions.md
[portal]: https://portal.azure.com/
[qna]: ../../azure-monitor/app/troubleshoot-faq.md
[start]: ../../azure-monitor/app/app-insights-overview.md
