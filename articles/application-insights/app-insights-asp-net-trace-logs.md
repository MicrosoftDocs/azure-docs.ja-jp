---
title: "Application Insights の .NET トレース ログを調べる"
description: "Trace、NLog、または Log4Net で生成されたログを検索します。"
services: application-insights
documentationcenter: .net
author: alancameronwills
manager: douge
ms.assetid: 0c2a084f-6e71-467b-a6aa-4ab222f17153
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 07/21/2016
ms.author: awills
translationtype: Human Translation
ms.sourcegitcommit: a087df444c5c88ee1dbcf8eb18abf883549a9024
ms.openlocfilehash: f803b44172b068b7ba65047c769421e39445ce10
ms.lasthandoff: 03/15/2017


---
# <a name="explore-net-trace-logs-in-application-insights"></a>Application Insights の .NET トレース ログを調べる
ASP.NET アプリケーションで診断トレースに NLog、log4Net、または System.Diagnostics.Trace を使用している場合、ログを [Azure Application Insights][start] に送信し、そこで調査したり、検索したりできます。 ログはアプリケーションから送信される他の利用統計情報と結合されます。それにより、互いのユーザー要求にサービスを提供することに関連付けられているトレースを特定し、それらを他のイベントや例外レポートに相互に関連付けることができます。

> [!NOTE]
> ログ キャプチャ モジュールは必要ですか。 ログ キャプチャ モジュールは、サード パーティ製のロガーの場合は便利なアダプターですが、NLog、log4Net、または System.Diagnostics.Trace をまだ使用していない場合は、 [Application Insights TrackTrace()](app-insights-api-custom-events-metrics.md#tracktrace) を直接呼び出すことを検討してください。
>
>

## <a name="install-logging-on-your-app"></a>アプリにログ記録フレームワークをインストールする
選択したログ記録フレームワークをプロジェクトにインストールします。 インストールすると、app.config か web.config にエントリが追加されます。

System.Diagnostics.Trace を使用している場合は、web.config にエントリを追加する必要があります。

```XML

    <configuration>
     <system.diagnostics>
       <trace autoflush="false" indentsize="4">
         <listeners>
           <add name="myListener"
             type="System.Diagnostics.TextWriterTraceListener"
             initializeData="TextWriterOutput.log" />
           <remove name="Default" />
         </listeners>
       </trace>
     </system.diagnostics>
   </configuration>
```

## <a name="configure-application-insights-to-collect-logs"></a>ログを収集するよう Application Insights を構成する
**[Application Insights をプロジェクトに追加](app-insights-asp-net.md)**します (まだ追加していない場合)。 Log Collector を含めるオプションが表示されます。

または、ソリューション エクスプローラーでプロジェクトを右クリックし、 **Application Insights を構成** します。 **[トレース コレクションの構成]** を選択します。

*Application Insights のメニューや Log Collector のオプションが表示されない場合は、* [トラブルシューティング](#troubleshooting)をお試しください。

## <a name="manual-installation"></a>手動のインストール
Application Insights インストーラーでサポートされていない種類のプロジェクト (Windows デスクトップ プロジェクトなど) の場合は、手動でインストールします。

1. log4Net または NLog を使用する場合は、プロジェクト内にインストールします。
2. ソリューション エクスプローラーでプロジェクトを右クリックし、[ **NuGet パッケージの管理**] を選択します。
3. Search for "Application Insights"

    ![適切なパッケージのプレリリース バージョンを入手する](./media/app-insights-asp-net-trace-logs/appinsights-36nuget.png)
4. 次のいずれかの適切なパッケージを選択します。

   * Microsoft.ApplicationInsights.TraceListener (System.Diagnostics.Trace コールをキャプチャするため)
   * Microsoft.ApplicationInsights.NLogTarget
   * Microsoft.ApplicationInsights.Log4NetAppender

NuGet パッケージは、必要なアセンブリをインストールし、web.config または app.config も変更します。

## <a name="insert-diagnostic-log-calls"></a>診断ログの呼び出しを挿入する
System.Diagnostics.Trace を使用する場合、通常の呼び出しは次のようになります。

    System.Diagnostics.Trace.TraceWarning("Slow response - database01");

log4net、または NLog を使用する場合

    logger.Warn("Slow response - database01");


## <a name="using-the-trace-api-directly"></a>トレース API を直接利用する
Application Insights トレース API を直接呼び出すことができます。 ログ記録のアダプターはこの API を使用します。

次に例を示します。

    var telemetry = new Microsoft.ApplicationInsights.TelemetryClient();
    telemetry.TrackTrace("Slow response - database01");

TrackTrace の利点は、比較的長いデータをメッセージの中に配置できることです。 たとえば、その中に POST データをエンコードできます。

加えて、メッセージに重大度レベルを追加することができます。 また他のテレメトリと同様、プロパティ値を追加することで、さまざまなトレースの組み合わせをフィルタで抽出したり検索したりすることができます。 For example:

    var telemetry = new Microsoft.ApplicationInsights.TelemetryClient();
    telemetry.TrackTrace("Slow database response",
                   SeverityLevel.Warning,
                   new Dictionary<string,string> { {"database", db.ID} });

特定のデータベースに関連する特定の重大度レベルに該当するすべてのメッセージを、[検索][diagnostic]で簡単に抽出できます。

## <a name="explore-your-logs"></a>ログを調査する
アプリをデバッグ モードで実行するかデプロイします。

[Application Insights ポータル][portal]内のアプリの概要ブレードで、[[検索]][diagnostic] を選択します。

![Application Insights で、[検索] を選択する](./media/app-insights-asp-net-trace-logs/020-diagnostic-search.png)

![[検索]](./media/app-insights-asp-net-trace-logs/10-diagnostics.png)

たとえば、次の操作ができます。

* ログ トレースまたは特定のプロパティを持つ項目をフィルター処理する
* 特定の項目の詳細に調べる
* 同じユーザー要求に関連する (つまり、OperationId が同じ) 他の利用統計情報を探す
* このページの構成をお気に入りとして保存する

> [!NOTE]
> **サンプリング。** アプリケーションが送信するデータ量が多く、Application Insights SDK for ASP.NET バージョン 2.0.0-beta3 以降を使用している場合は、アダプティブ サンプリング機能が動作して、テレメトリの一定の割合のみが送信される可能性があります。 [サンプリングの詳細については、こちらを参照してください。](app-insights-sampling.md)
>
>

## <a name="next-steps"></a>次のステップ
[ASP.NET ][exceptions]のエラーと例外を診断する

[検索][diagnostic]の詳細についてはこちらを参照してください。

## <a name="troubleshooting"></a>トラブルシューティング
### <a name="how-do-i-do-this-for-java"></a>Java の場合はどうすればよいですか。
[Java ログ アダプター](app-insights-java-trace-logs.md)を使用します。

### <a name="theres-no-application-insights-option-on-the-project-context-menu"></a>プロジェクトのコンテキスト メニューに Application Insights のオプションがありません
* お使いの開発用コンピューターに Application Insights Tools がインストールされているか確認してください。 Visual Studio の [ツール] メニューで [拡張機能と更新プログラム] を選択し、Application Insights Tools を探します。 Application Insights Tools が [インストール済み] タブにない場合は、[オンライン] タブを開いてインストールします。
* Application Insights Tools でサポートされていない種類のプロジェクトである可能性があります。 [手動でインストール](#manual-installation)してください。

### <a name="no-log-adapter-option-in-the-configuration-tool"></a>構成ツールにログ アダプターのオプションがありません
* まずログ記録フレームワークをインストールする必要があります。
* System.Diagnostics.Trace を使用している場合は、[`web.config`で構成済み](https://msdn.microsoft.com/library/system.diagnostics.eventlogtracelistener.aspx)であることを確認します。
* 最新バージョンの Application Insights を使用しているか確認します。 Visual Studio の **[ツール]** メニューで **[拡張機能と更新プログラム]** を選択し、**[更新]** タブを開きます。 Developer Analytics Tools が表示されていたら、クリックして更新します。

### <a name="emptykey"></a>エラー「インストルメンテーション キーは空にできません」が発生しました
Application Insights をインストールしないでログ アダプターの Nuget パッケージをインストールした可能性があります。

ソリューション エクスプローラーで、 `ApplicationInsights.config` を右クリックし、[ **Application Insights の更新**] を選択します。 Azure へのサインインを促すダイアログが表示されます。または、Application Insights のリソースを作成するか、既存のリソースを再利用します。 これで問題は修正されます。

### <a name="i-can-see-traces-in-diagnostic-search-but-not-the-other-events"></a>診断検索にトレースが表示されますが、他のイベントがありません
すべてのイベントと要求がパイプラインを通過するまで時間がかかることがあります。

### <a name="limits"></a>保持されるデータの量はどのくらいですか
各アプリケーションで、1 秒あたり 500 イベントまでです。 イベントは&7; 日間保持されます。

### <a name="im-not-seeing-some-of-the-log-entries-that-i-expect"></a>予期されるログ エントリの一部が表示されません
アプリケーションが送信するデータ量が多く、Application Insights SDK for ASP.NET バージョン 2.0.0-beta3 以降を使用している場合は、アダプティブ サンプリング機能が動作して、テレメトリの一定の割合のみが送信される可能性があります。 [サンプリングの詳細については、こちらを参照してください。](app-insights-sampling.md)

## <a name="add"></a>次のステップ
* [可用性と応答性のテストを設定する][availability]
* [Troubleshooting][qna]

<!--Link references-->

[availability]: app-insights-monitor-web-app-availability.md
[diagnostic]: app-insights-diagnostic-search.md
[exceptions]: app-insights-asp-net-exceptions.md
[portal]: https://portal.azure.com/
[qna]: app-insights-troubleshoot-faq.md
[start]: app-insights-overview.md

