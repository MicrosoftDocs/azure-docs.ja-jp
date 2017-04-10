---
title: "Azure Application Insights SDK におけるフィルター処理および前処理 | Microsoft Docs"
description: "テレメトリが Application Insights ポータルに送信される前に、SDK でフィルター処理またはデータへのプロパティの追加を行うためのテレメトリ プロセッサおよびテレメトリ初期化子を記述します。"
services: application-insights
documentationcenter: 
author: beckylino
manager: carmonm
ms.assetid: 38a9e454-43d5-4dba-a0f0-bd7cd75fb97b
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: multiple
ms.topic: article
ms.date: 11/23/2016
ms.author: awills
translationtype: Human Translation
ms.sourcegitcommit: 4813610bc938c1a2fb6581bc9d8352b519a2d8ef
ms.openlocfilehash: ea5285a7997beed926e35649362cee7de53e1efe


---
# <a name="filtering-and-preprocessing-telemetry-in-the-application-insights-sdk"></a>Application Insights SDK におけるテレメトリのフィルター処理および前処理


Application Insights SDK のプラグインを作成および構成して、Application Insights サービスに送信される前のテレメトリのキャプチャと処理の方法をカスタマイズできます。

* [サンプリング](app-insights-sampling.md) は、統計に影響を与えることなくテレメトリの量を削減します。 関連するデータ ポイントが一緒に保持されるので、問題の診断時にそれらのデータ ポイント間を移動することができます。 ポータルでは、サンプリングを補正するために合計数が乗算されます。
* [ASP.NET](#filtering) または [Java](app-insights-java-filter-telemetry.md) 向けテレメトリ プロセッサによるフィルター処理では、テレメトリがサーバーに送信される前に、SDK でテレメトリを選択または変更することができます。 たとえば、ロボットからの要求を除外することでテレメトリの量を削減することができます。 ただし、フィルター処理は、トラフィックを削減するうえでは、サンプリングよりも基本的な方法です。 フィルター処理を使用すると、送信する内容をより細かく制御することができます。ただし、統計に影響を及ぼすことになるので注意が必要です (たとえば、すべての成功した要求を除外する場合など)。
* [プロパティを追加](#add-properties) できます。 たとえば、算出値や、ポータルでデータをフィルター処理するのに使用できるバージョン番号を追加することが可能です。
* [SDK API](app-insights-api-custom-events-metrics.md) は、カスタム イベントとメトリックの送信に使用します。

開始する前に次の操作を実行してください。

* アプリに Application Insights [SDK for ASP.NET](app-insights-asp-net.md) または [SDK for Java](app-insights-java-get-started.md) をインストールします。

<a name="filtering"></a>

## <a name="filtering-itelemetryprocessor"></a>フィルター: ITelemetryProcessor
この手法では、テレメトリ ストリームに含める内容またはテレメトリ ストリームから除外する内容をより直接的に制御できます。 フィルター処理はサンプリングと組み合わせて使用することも別々に使用することもできます。

テレメトリのフィルター処理を行うには、テレメトリ プロセッサを記述し、それを SDK に登録します。 どのテレメトリもこのプロセッサを通過します。テレメトリをストリームから除外するように選択することも、プロパティを追加することもできます。 これには、HTTP 要求コレクターや依存関係コレクターなどの標準的なモジュールのテレメトリに加えて、自身で作成したテレメトリも含まれます。 たとえば、ロボットからの要求や成功した依存関係の呼び出しについてのテレメトリをフィルターで除外できます。

> [!WARNING]
> プロセッサを使用して SDK から送信されるテレメトリをフィルター処理すると、ポータルに表示される統計にゆがみが生じ、関連項目を追跡するのが困難になる可能性があります。
>
> 代わりに、 [サンプリング](app-insights-sampling.md)の使用を検討します。
>
>

### <a name="create-a-telemetry-processor-c"></a>テレメトリ プロセッサを作成する (C#)
1. プロジェクトの Application Insights SDK がバージョン 2.0.0 以降であることを確認してください。 Visual Studio ソリューション エクスプローラーでプロジェクトを右クリックし、[NuGet パッケージの管理] をクリックします。 NuGet パッケージ マネージャーで、Microsoft.ApplicationInsights.Web をオンにします。
2. フィルターを作成するには、ITelemetryProcessor を実装します。 これは、テレメトリ モジュール、テレメトリ初期化子、テレメトリ チャネルと同じく、機能拡張ポイントの 1 つです。

    テレメトリ プロセッサが処理のチェーンを構築することに注意してください。 テレメトリ プロセッサをインスタンス化するときは、リンクをチェーン内の次のプロセッサに渡します。 テレメトリ データ ポイントが Process メソッドに渡されると、作業が実行され、そのチェーンの次のテレメトリ プロセッサが呼び出されます。

    ``` C#

    using Microsoft.ApplicationInsights.Channel;
    using Microsoft.ApplicationInsights.Extensibility;

    public class SuccessfulDependencyFilter : ITelemetryProcessor
      {

        private ITelemetryProcessor Next { get; set; }

        // You can pass values from .config
        public string MyParamFromConfigFile { get; set; }

        // Link processors to each other in a chain.
        public SuccessfulDependencyFilter(ITelemetryProcessor next)
        {
            this.Next = next;
        }
        public void Process(ITelemetry item)
        {
            // To filter out an item, just return
            if (!OKtoSend(item)) { return; }
            // Modify the item if required
            ModifyItem(item);

            this.Next.Process(item);
        }

        // Example: replace with your own criteria.
        private bool OKtoSend (ITelemetry item)
        {
            var dependency = item as DependencyTelemetry;
            if (dependency == null) return true;

            return dependency.Success != true;
        }

        // Example: replace with your own modifiers.
        private void ModifyItem (ITelemetry item)
        {
            item.Context.Properties.Add("app-version", "1." + MyParamFromConfigFile);
        }
    }

    ```
1. 次の内容を ApplicationInsights.config に挿入します

```XML

    <TelemetryProcessors>
      <Add Type="WebApplication9.SuccessfulDependencyFilter, WebApplication9">
         <!-- Set public property -->
         <MyParamFromConfigFile>2-beta</MyParamFromConfigFile>
      </Add>
    </TelemetryProcessors>

```

(これは、サンプリング フィルターを初期化するセクションと同じです)。

名前付きのパブリック プロパティをクラス内に指定することにより、.config ファイルから文字列値を渡すことができます。

> [!WARNING]
> .config ファイル内の型名とプロパティ名をコード内のクラスおよびプロパティ名と慎重に照合してください。 存在しない型またはプロパティが .config ファイルによって参照されていると、SDK は何も通知せずにテレメトリの送信に失敗する場合があります。
>
>

**あるいは** 、コード内でフィルターを初期化することもできます。 適切な初期化クラス (たとえば Global.asax.cs の AppStart) で、プロセッサをチェーンに挿入します。

```C#

    var builder = TelemetryConfiguration.Active.TelemetryProcessorChainBuilder;
    builder.Use((next) => new SuccessfulDependencyFilter(next));

    // If you have more processors:
    builder.Use((next) => new AnotherProcessor(next));

    builder.Build();

```

この時点より後に作成された TelemetryClients はプロセッサを使用します。

### <a name="example-filters"></a>フィルターの例
#### <a name="synthetic-requests"></a>人工的な要求
ボットと Web テストを除外します。 メトリックス エクスプローラーで人工的なソースを除外することもできますが、SDK でそのソースをフィルター処理することでトラフィックが削減されます。

``` C#

    public void Process(ITelemetry item)
    {
      if (!string.IsNullOrEmpty(item.Context.Operation.SyntheticSource)) {return;}

      // Send everything else:
      this.Next.Process(item);
    }

```

#### <a name="failed-authentication"></a>失敗した認証
"401" 応答が返された要求を除外します。

```C#

public void Process(ITelemetry item)
{
    var request = item as RequestTelemetry;

    if (request != null &&
    request.ResponseCode.Equals("401", StringComparison.OrdinalIgnoreCase))
    {
        // To filter out an item, just terminate the chain:
        return;
    }
    // Send everything else:
    this.Next.Process(item);
}

```

#### <a name="filter-out-fast-remote-dependency-calls"></a>リモートの依存関係の高速呼び出しを除外する
低速な呼び出しの診断のみを実行する場合は、高速呼び出しを除外します。

> [!NOTE]
> これによって、ポータルに表示される統計にゆがみが生じます。 依存関係のグラフは、依存関係の呼び出しがすべてエラーのように表示されます。
>
>

``` C#

public void Process(ITelemetry item)
{
    var request = item as DependencyTelemetry;

    if (request != null && request.Duration.TotalMilliseconds < 100)
    {
        return;
    }
    this.Next.Process(item);
}

```

#### <a name="diagnose-dependency-issues"></a>依存関係の問題の診断
[このブログ](https://azure.microsoft.com/blog/implement-an-application-insights-telemetry-processor/) では、依存関係に対して定期的な ping を自動送信することによって依存関係の問題を診断するプロジェクトについて説明します。


<a name="add-properties"></a>

## <a name="add-properties-itelemetryinitializer"></a>プロパティの追加: ITelemetryInitializer
テレメトリ初期化子を使用して、すべてのテレメトリで送信されるグローバル プロパティを定義し、標準テレメトリ モジュールの選択された動作を上書きします。

たとえば、Web 向けの Application Insights パッケージでは HTTP 要求に関するテレメトリが収集されます。 既定では、応答コードが 400 以上の要求はすべて失敗としてフラグが設定されます。 これに対して 400 を成功として処理する場合は、"成功" プロパティを設定するテレメトリ初期化子を指定できます。

テレメトリ初期化子を指定すると、Track*() メソッドのいずれかが呼び出されるたびに、テレメトリ初期化子も呼び出されます。 これには、標準のテレメトリ モジュールによって呼び出されるメソッドも含まれます。 通常、これらのモジュールでは、初期化子によって既に設定されているプロパティは設定されません。

**初期化子を定義する**

*C#*

```C#

    using System;
    using Microsoft.ApplicationInsights.Channel;
    using Microsoft.ApplicationInsights.DataContracts;
    using Microsoft.ApplicationInsights.Extensibility;

    namespace MvcWebRole.Telemetry
    {
      /*
       * Custom TelemetryInitializer that overrides the default SDK
       * behavior of treating response codes >= 400 as failed requests
       *
       */
      public class MyTelemetryInitializer : ITelemetryInitializer
      {
        public void Initialize(ITelemetry telemetry)
        {
            var requestTelemetry = telemetry as RequestTelemetry;
            // Is this a TrackRequest() ?
            if (requestTelemetry == null) return;
            int code;
            bool parsed = Int32.TryParse(requestTelemetry.ResponseCode, out code);
            if (!parsed) return;
            if (code >= 400 && code < 500)
            {
                // If we set the Success property, the SDK won't change it:
                requestTelemetry.Success = true;
                // Allow us to filter these requests in the portal:
                requestTelemetry.Context.Properties["Overridden400s"] = "true";
            }
            // else leave the SDK to set the Success property      
        }
      }
    }
```

**初期化子を読み込む**

ApplicationInsights.config で:

    <ApplicationInsights>
      <TelemetryInitializers>
        <!-- Fully qualified type name, assembly name: -->
        <Add Type="MvcWebRole.Telemetry.MyTelemetryInitializer, MvcWebRole"/>
        ...
      </TelemetryInitializers>
    </ApplicationInsights>

*または、* Global.aspx.cs などのコード内で初期化子をインスタンス化することもできます。

```C#
    protected void Application_Start()
    {
        // ...
        TelemetryConfiguration.Active.TelemetryInitializers
        .Add(new MyTelemetryInitializer());
    }
```


[このトピックのその他のサンプルについては、こちらをご覧ください。](https://github.com/Microsoft/ApplicationInsights-Home/tree/master/Samples/AzureEmailService/MvcWebRole)

<a name="js-initializer"></a>

### <a name="javascript-telemetry-initializers"></a>JavaScript テレメトリ初期化子
*JavaScript*

ポータルから取得した初期化コードの直後にテレメトリ初期化子を挿入します。

```JS

    <script type="text/javascript">
        // ... initialization code
        ...({
            instrumentationKey: "your instrumentation key"
        });
        window.appInsights = appInsights;


        // Adding telemetry initializer.
        // This is called whenever a new telemetry item
        // is created.

        appInsights.queue.push(function () {
            appInsights.context.addTelemetryInitializer(function (envelope) {
                var telemetryItem = envelope.data.baseData;

                // To check the telemetry item’s type - for example PageView:
                if (envelope.name == Microsoft.ApplicationInsights.Telemetry.PageView.envelopeType) {
                    // this statement removes url from all page view documents
                    telemetryItem.url = "URL CENSORED";
                }

                // To set custom properties:
                telemetryItem.properties = telemetryItem.properties || {};
                telemetryItem.properties["globalProperty"] = "boo";

                // To set custom metrics:
                telemetryItem.measurements = telemetryItem.measurements || {};
                telemetryItem.measurements["globalMetric"] = 100;
            });
        });

        // End of inserted code.

        appInsights.trackPageView();
    </script>
```

telemetryItem で使用できる非カスタム プロパティの概要については、[Application Insights エクスポート データ モデル](app-insights-export-data-model.md)に関するページを参照してください。

任意の数の初期化子を追加できます。

## <a name="itelemetryprocessor-and-itelemetryinitializer"></a>ITelemetryProcessor と ITelemetryInitializer
テレメトリ プロセッサとテレメトリ初期化子は何が違うのでしょうか。

* 共通する機能もあり、どちらもテレメトリにプロパティを追加するために使用できます。
* TelemetryInitializer は常に TelemetryProcessor の前に実行します。
* TelemetryProcessor を使用すると、テレメトリ項目を完全に置換または破棄できます。
* TelemetryProcessor は、パフォーマンス カウンター テレメトリを処理しません。


## <a name="reference-docs"></a>リファレンス ドキュメント
* [API の概要](app-insights-api-custom-events-metrics.md)
* [ASP.NET リファレンス](https://msdn.microsoft.com/library/dn817570.aspx)

## <a name="sdk-code"></a>SDK コード
* [ASP.NET コア SDK](https://github.com/Microsoft/ApplicationInsights-dotnet)
* [ASP.NET 5](https://github.com/Microsoft/ApplicationInsights-aspnet5)
* [JavaScript SDK](https://github.com/Microsoft/ApplicationInsights-JS)

## <a name="a-namenextanext-steps"></a><a name="next"></a>次のステップ
* [イベントおよびログを検索する][diagnostic]
* [サンプリング](app-insights-sampling.md)
* [Troubleshooting][qna]

<!--Link references-->

[client]: app-insights-javascript.md
[config]: app-insights-configuration-with-applicationinsights-config.md
[create]: app-insights-create-new-resource.md
[data]: app-insights-data-retention-privacy.md
[diagnostic]: app-insights-diagnostic-search.md
[exceptions]: app-insights-asp-net-exceptions.md
[greenbrown]: app-insights-asp-net.md
[java]: app-insights-java-get-started.md
[metrics]: app-insights-metrics-explorer.md
[qna]: app-insights-troubleshoot-faq.md
[trace]: app-insights-search-diagnostic-logs.md
[windows]: app-insights-windows-get-started.md



<!--HONumber=Feb17_HO3-->


