---
title: "Azure Application Insights におけるテレメトリ サンプリング | Microsoft Docs"
description: "テレメトリのボリュームを抑制する方法。"
services: application-insights
documentationcenter: windows
author: vgorbenko
manager: carmonm
ms.assetid: 015ab744-d514-42c0-8553-8410eef00368
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 03/24/2017
ms.author: awills
translationtype: Human Translation
ms.sourcegitcommit: 611f4222b5ab1530658f612de39dd2712f98c250
ms.openlocfilehash: cbc622a959c402fe25ce9ab026c1ae05f194d884
ms.lasthandoff: 02/03/2017


---
# <a name="sampling-in-application-insights"></a>Application Insights におけるサンプリング


サンプリングは [Azure Application Insights](app-insights-overview.md) の機能です。 サンプリングは、アプリケーション データの分析に関して統計的な正しさを保ちながら、テレメトリのトラフィックと保存スペースを減らす方法として推奨されます。 フィルターによって関連のある項目が選択されるため、診断調査を行うときに項目間を移動できるようになります。
ポータルでメトリック数が表示されるときは、統計値への影響を最小限に抑えるために、メトリック数を再正規化してサンプリングに配慮します。

サンプリングによってトラフィックとデータ コストが減り、スロットルを回避できます。

## <a name="in-brief"></a>概要:
* サンプリングでは、 *n* 個のレコードのうちの 1 個が保持されて、残りは破棄されます。 たとえば、5 イベントのうち 1 個を残した場合、サンプリング レートは 20% です。 
* ASP.NET Web サーバー アプリの場合、アプリケーションが大量のテレメトリを送信するとサンプリングが自動的に行われます。
* サンプリングは、ポータルの価格ページで、または ASP.NET SDK の .config ファイルで、ネットワーク トラフィックを減らすために、手動で設定することもできます。
* カスタム イベントをログに記録していて、イベントのセットがまとめて保持または破棄されていることを確認したい場合は、同じ OperationId 値があることを確認します。
* サンプリング除数 *n* は、`itemCount` プロパティでレコードごとに報告されます。この値は、"要求カウント" または "イベント数" というわかりやすい名前で検索結果に表示されます。 サンプリング操作が行われていない場合、`itemCount==1` となります。
* Analytics クエリを作成する場合は、 [サンプリングを考慮する](app-insights-analytics-tour.md#counting-sampled-data)必要があります。 具体的には、単純にレコードをカウントするのではなく、 `summarize sum(itemCount)`を使用する必要があります。

## <a name="types-of-sampling"></a>サンプリングの種類
現在は&3; つのサンプリング メソッドがあります。

* **アダプティブ サンプリング** は、ASP.NET アプリの SDK から送信されるテレメトリの量を自動的に調整します。 SDK v 2.0.0-beta3 からの既定値です。 現在は、ASP.NET サーバー側テレメトリでのみ使用できます。 
* **固定レート サンプリング** は、ASP.NET サーバーおよびユーザーのブラウザーから送信されるテレメトリの量を削減します。 管理者がレートを設定します。 クライアントとサーバーはサンプリングを同期するので、検索では関連のあるページ ビューと要求の間を移動できます。
* **インジェスト サンプリング**は Azure Portal で動作し、 アプリケーションから受信したテレメトリの一部を、設定した比率で破棄します。 インジェスト サンプリングはテレメトリのトラフィックを削減しませんが、トラフィックを月間のクォータ (上限) 以内で維持するのに役立ちます。 インジェスト サンプリングの大きな利点は、アプリを再デプロイしなくても設定でき、すべてのサーバーとクライアントで一様に動作することです。 

アダプティブ サンプリングまたは固定レート サンプリングの実行中は、インジェスト サンプリングが無効になります。

## <a name="ingestion-sampling"></a>インジェスト サンプリング
この形式のサンプリングは、Web サーバー、ブラウザー、デバイスからのテレメトリが Application Insights サービス エンドポイントに到達した場所で動作します。 アプリから送信されるテレメトリ トラフィックを削減することはありませんが、Application Insights によって処理および維持 (そして課金) される量を削減します。

アプリが頻繁に月間クォータを超えて、SDK ベースのいずれかのサンプリング種類を使用するオプションがない場合は、この種類のサンプリングを使用します。 

サンプリング レートは [クォータと価格] ブレードで設定します。

![アプリケーションの [概要] ブレードで、[設定]、[クォータ]、[サンプル] の順にクリックし、サンプリング レートを選択して、[更新] をクリックします。](./media/app-insights-sampling/04.png)

他のサンプリング種類と同様に、このアルゴリズムは関連するテレメトリ項目を維持します。 たとえば、検索でテレメトリを調べているときは、特定の例外に関連する要求を検索できます。 メトリックはそのような要求のレートをカウントし、例外レートを正しく維持します。

サンプリングによって破棄されたデータ ポイントは、 [連続エクスポート](app-insights-export-telemetry.md)などの Application Insights の機能では使用できません。

SDK ベースのアダプティブ サンプリングまたは固定レート サンプリングの実行中は、インジェスト サンプリングは動作しません。 SDK のサンプリング レートが 100% 未満であれば、設定したインジェスト サンプリング レートは無視されます。

> [!WARNING]
> タイルに表示される値は、インジェスト サンプリングに設定した値を示します。 SDK サンプリングの実行中は、この値は実際のサンプリング レートを表しません。
> 
> 

## <a name="adaptive-sampling-at-your-web-server"></a>Web サーバーでのアダプティブ サンプリング
アダプティブ サンプリングは、ASP.NET v 2.0.0-beta3 以降の Application Insights SDK で使用でき、既定で有効になります。 

アダプティブ サンプリングは、Web サーバー アプリから Application Insights サービスに送信されるテレメトリの量に適用されます。 量が自動的に調整されて、指定した最大トラフィック レート以下に維持されます。

量の少ないテレメトリでは動作しないので、デバッグ中のアプリまたは使用頻度の低い Web サイトは影響を受けません。

目標の量を達成するため、生成されたテレメトリの一部は破棄されます。 他のサンプリング種類と同様に、このアルゴリズムは関連するテレメトリ項目を維持します。 たとえば、検索でテレメトリを調べているときは、特定の例外に関連する要求を検索できます。 

要求レートや例外レートなどのメトリック カウントはサンプリング レートを補正するように調整され、メトリックス エクスプローラーにはほぼ正しい値が表示されます。

**プロジェクトの NuGet** パッケージを Application Insights の最新の*プレリリース* バージョンに更新します。ソリューション エクスプ ローラーでプロジェクトを右クリックし、[NuGet パッケージの管理] を選択し、**[プレリリースを含める]** をオンにして、Microsoft.ApplicationInsights.Web を検索します。 

[ApplicationInsights.config](app-insights-configuration-with-applicationinsights-config.md) で、`AdaptiveSamplingTelemetryProcessor` ノードのいくつかのパラメーターを調整できます。 次の図は既定値です。

* `<MaxTelemetryItemsPerSecond>5</MaxTelemetryItemsPerSecond>`
  
    アダプティブ アルゴリズムが **各サーバー ホスト**で目標とするレート。 Web アプリが多数のホストで実行される場合は、Application Insights ポータルのトラフィックの目標レート内に収まるようにこの値を減らします。
* `<EvaluationInterval>00:00:15</EvaluationInterval>` 
  
    テレメトリの現在のレートを再評価する間隔。 評価は移動平均として実行されます。 急変しやすいテレメトリの場合は、この間隔を短くすることもできます。
* `<SamplingPercentageDecreaseTimeout>00:02:00</SamplingPercentageDecreaseTimeout>`
  
    サンプリング率が変化してから、どのくらいの時間が経過すると、サンプリング率を下げてキャプチャ データ量を減らすことができるようになるかを指定します。
* `<SamplingPercentageIncreaseTimeout>00:15:00</SamplingPercentageIncreaseTimeout>`
  
    サンプリング率が変化してから、どのくらいの時間が経過すると、サンプリング率を上げてキャプチャ データ量を増やすことができるようになるかを指定します。
* `<MinSamplingPercentage>0.1</MinSamplingPercentage>`
  
    サンプリング率の変化に合わせて、設定できる最小値。
* `<MaxSamplingPercentage>100.0</MaxSamplingPercentage>`
  
    サンプリング率の変化に合わせて、設定できる最大値。
* `<MovingAverageRatio>0.25</MovingAverageRatio>` 
  
    移動平均の計算で最新値に割り当てられる重み。 1 以下の値を使用します。 小さい値にすると、急変に対する反応が低いアルゴリズムになります。
* `<InitialSamplingPercentage>100</InitialSamplingPercentage>`
  
    アプリの起動直後に割り当てられる値。 デバッグ中はこの値を減らさないでください。 

* `<ExcludedTypes>Trace;Exception</ExcludedTypes>`
  
    サンプリング対象にしない型のセミコロン区切りのリスト。 認識される型は、Dependency、Event、Exception、PageView、Request、Trace です。 指定した型はすべてのインスタンスが転送されます。指定されていない型はサンプリングされます。

* `<IncludedTypes>Request;Dependency</IncludedTypes>`
  
    サンプリング対象にする型のセミコロン区切りのリスト。 認識される型は、Dependency、Event、Exception、PageView、Request、Trace です。 指定した型はサンプリングされます。他の型のすべてのインスタンスは常に転送されます。


アダプティブ サンプリングを**オフにする**には、applicationinsights-config から AdaptiveSamplingTelemetryProcessor ノードを削除します。

### <a name="alternative-configure-adaptive-sampling-in-code"></a>代替方法: コードでのアダプティブ サンプリングの構成
.config ファイルでサンプリングを調整する代わりに、コードを使用できます。 この方法では、サンプリング レートが再評価されるたびに呼び出されるコールバック関数を指定できます。 たとえば、この方法を使用して、どのようなサンプリング レートが使用されているかを確認できます。

.config ファイルから `AdaptiveSamplingTelemetryProcessor` ノードを削除します。

*C#*

```C#

    using Microsoft.ApplicationInsights;
    using Microsoft.ApplicationInsights.Extensibility;
    using Microsoft.ApplicationInsights.WindowsServer.Channel.Implementation;
    using Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel;
    ...

    var adaptiveSamplingSettings = new SamplingPercentageEstimatorSettings();

    // Optional: here you can adjust the settings from their defaults.

    var builder = TelemetryConfiguration.Active.TelemetryProcessorChainBuilder;

    builder.UseAdaptiveSampling(
         adaptiveSamplingSettings,

        // Callback on rate re-evaluation:
        (double afterSamplingTelemetryItemRatePerSecond,
         double currentSamplingPercentage,
         double newSamplingPercentage,
         bool isSamplingPercentageChanged,
         SamplingPercentageEstimatorSettings s
        ) =>
        {
          if (isSamplingPercentageChanged)
          {
             // Report the sampling rate.
             telemetryClient.TrackMetric("samplingPercentage", newSamplingPercentage);
          }
      });

    // If you have other telemetry processors:
    builder.Use((next) => new AnotherProcessor(next));

    builder.Build();

```

([テレメトリ プロセッサについてはこちらをご覧ください](app-insights-api-filtering-sampling.md#filtering)。)

<a name="other-web-pages"></a>

## <a name="sampling-for-web-pages-with-javascript"></a>JavaScript を使用した Web ページのサンプリング
任意のサーバーから固定レートのサンプリング用に Web ページを構成できます。 

[Application Insights 用に Web ページを構成する](app-insights-javascript.md)場合は、Application Insights ポータルからスニペットを入手して、それを変更します (通常、ASP.NET アプリでは _Layout.cshtml を利用できます)。`samplingPercentage: 10,` のような行をインストルメンテーション キーの前に挿入します。

    <script>
    var appInsights= ... 
    }({ 


    // Value must be 100/N where N is an integer.
    // Valid examples: 50, 25, 20, 10, 5, 1, 0.1, ...
    samplingPercentage: 10, 

    instrumentationKey:...
    }); 

    window.appInsights=appInsights; 
    appInsights.trackPageView(); 
    </script> 

サンプリング率には、N を整数として 100/N に近い割合を選択します。  サンプリングでは現在、その他の値はサポートされていません。

サーバーでも固定レート サンプリングを有効にしている場合は、クライアントとサーバーはサンプリングを同期するので、検索で関連のあるページ ビューと要求の間を移動できます。

## <a name="fixed-rate-sampling-for-aspnet-web-sites"></a>ASP.NET Web サイトの固定レート サンプリング
固定レート サンプリングは、Web サーバーおよび Web ブラウザーから送信されるトラフィックを削減します。 アダプティブ サンプリングとは異なり、管理者によって決定された固定レートでテレメトリを削減します。 また、クライアントとサーバーのサンプリングが同期されて、関連する項目が維持されます。たとえば、検索でページ ビューを見た場合、それに関連する要求を検索できます。

サンプリング アルゴリズムでは関連する項目が維持されます。 HTTP 要求イベントごとに、そのイベントおよびそれに関連するイベントが破棄または転送されます。 

メトリックス エクスプローラーでは、要求や例外の数などのレートに係数が乗算されて、サンプリング レートに対してほぼ正しくなるように補正されます。

1. **プロジェクトの NuGet パッケージ**を Application Insights の最新の*プレリリース* バージョンに更新します。 ソリューション エクスプ ローラーでプロジェクトを右クリックし、[NuGet パッケージの管理] を選択します。**[プレリリースを含める]** をオンにし、Microsoft.ApplicationInsights.Web を検索します。 
2. **アダプティブ サンプリングを無効にする**: [ApplicationInsights.config](app-insights-configuration-with-applicationinsights-config.md) で、`AdaptiveSamplingTelemetryProcessor` ノードを削除するか、コメントにします。
   
    ```xml
   
    <TelemetryProcessors>
   
    <!-- Disabled adaptive sampling:
      <Add Type="Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel.AdaptiveSamplingTelemetryProcessor, Microsoft.AI.ServerTelemetryChannel">
        <MaxTelemetryItemsPerSecond>5</MaxTelemetryItemsPerSecond>
      </Add>
    -->

    ```

1. **固定レート サンプリング モジュールを有効にします。** 次のスニペットを [ApplicationInsights.config](app-insights-configuration-with-applicationinsights-config.md)に追加します。
   
    ```XML
   
    <TelemetryProcessors>
     <Add  Type="Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel.SamplingTelemetryProcessor, Microsoft.AI.ServerTelemetryChannel">
   
      <!-- Set a percentage close to 100/N where N is an integer. -->
     <!-- E.g. 50 (=100/2), 33.33 (=100/3), 25 (=100/4), 20, 1 (=100/100), 0.1 (=100/1000) -->
      <SamplingPercentage>10</SamplingPercentage>
      </Add>
    </TelemetryProcessors>
   
    ```

> [!NOTE]
> サンプリング率には、N を整数として 100/N に近い割合を選択します。  サンプリングでは現在、その他の値はサポートされていません。
> 
> 

### <a name="alternative-enable-fixed-rate-sampling-in-your-server-code"></a>代替方法: サーバー コードでの固定レート サンプリングの有効化
.config ファイルにサンプリング パラメーターを設定する代わりに、コードを使用できます。 

*C#*

```C#

    using Microsoft.ApplicationInsights.Extensibility;
    using Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel;
    ...

    var builder = TelemetryConfiguration.Active.GetTelemetryProcessorChainBuilder();
    builder.UseSampling(10.0); // percentage

    // If you have other telemetry processors:
    builder.Use((next) => new AnotherProcessor(next));

    builder.Build();

```

([テレメトリ プロセッサについてはこちらをご覧ください](app-insights-api-filtering-sampling.md#filtering)。)

## <a name="when-to-use-sampling"></a>サンプリングを使用する場合
ASP.NET SDK バージョン 2.0.0-beta3 以降を使用している場合、アダプティブ サンプリングは自動的に有効になります。 使用している SDK のバージョンに関係なく、(Microsoft のサーバーで) インジェスト サンプリングを使用できます。

ほとんどの中小規模アプリケーションには、サンプリングは不要です。 最も役立つ診断情報や最も正確な統計は、すべてのユーザー アクティビティからデータを収集することで入手します。 

サンプリングの主な利点:

* アプリが短期間に非常に高いレートのテレメトリを送信すると、Application Insights サービスがデータ ポイントを削減 ("スロットル") する。 
* データ ポイントを、価格レベルの [クォータ](app-insights-pricing.md) 以内に抑える。 
* テレメトリの収集によるネットワーク トラフィックを削減する。 

### <a name="which-type-of-sampling-should-i-use"></a>使用する必要があるサンプリングの種類
**次の場合はインジェスト サンプリングを使用します。**

* テレメトリの月間クォータを超えることが多い場合。
* サンプリングをサポートしていないバージョンの SDK を使用している場合。Java SDK またはバージョン 2 より前の ASP.NET。
* ユーザーの Web ブラウザーから多量のテレメトリを取得する場合。

**次の場合は固定レート サンプリングを使用します。**

* ASP.NET Web サービス バージョン 2.0.0 以降の Application Insights SDK を使用している場合。
* クライアントとサーバーのサンプリングを同期する場合。[検索](app-insights-diagnostic-search.md)でイベントを調査するときに、クライアントとサーバーの関連するイベント単位 (ページ ビュー、HTTP 要求など) で操作できます。
* アプリに適したサンプリング率を確保する場合。 正確なメトリックを取得できる程度に高く、価格クォータとスロットル制限を超えないレートにする必要があります。 

**アダプティブ サンプリングを使用する場合。**

それ以外の場合は、アダプティブ サンプリングをお勧めします。 ASP.NET サーバー SDK バージョン 2.0.0-beta3 以降では、アダプティブ サンプリングが既定で有効になります。 一定の最低レートになるまでトラフィックは削減されないので、使用頻度の低いサイトには影響しません。

## <a name="how-do-i-know-whether-sampling-is-in-operation"></a>サンプリングが実行中かどうかを知る方法
適用されている場所に関係なく、実際のサンプリング レートを検出するには、次のように [Analytics クエリ](app-insights-analytics.md) を使用します。

    requests | where timestamp > ago(1d)
    | summarize 100/avg(itemCount) by bin(timestamp, 1h) 
    | render areachart 

保持されている各レコードで、 `itemCount` は、それが表す元のレコードの数 (1 + 以前に破棄されたレコードの数と同じ) を示します。 

## <a name="how-does-sampling-work"></a>サンプリングのしくみ
固定レート サンプリングおよびアダプティブ サンプリングは、ASP.NET バージョン 2.0.0 以降の SDK の機能です。 インジェスト サンプリングは Application Insights サービスの機能であり、SDK がサンプリングを実行していない場合に有効になります。 

サンプリング アルゴリズムでは、削除するテレメトリ項目と、保持するテレメトリ項目を決定します (SDK または Application Insights サービスのいずれで動作しているかに関係なく)。 サンプリングはいくつかのルールに基づいて決定されますが、このルールのねらいは、削減されたデータ セットを使用する場合でも、Application Insights の診断機能を実用的で信頼性の高いものに維持しながら、すべての相互に関連するデータ ポイントはそのままの状態で保持することです。 たとえば、失敗した要求に対してアプリが追加のテレメトリ項目 (この要求からログに記録された例外やトレースなど) を送信した場合、サンプリングでは、この要求とその他のテレメトリを分割することはありません。 すべてをまとめて維持するか、削除するかのどちらかです。 そのため、Application Insights で要求の詳細を表示する場合は、その要求に加えて、関連付けられているテレメトリ項目も常に表示されます。 

"ユーザー" を定義するアプリケーション (つまり、最も一般的な Web アプリケーション) の場合、サンプリングはユーザー ID のハッシュに基づいて決定されるため、特定のユーザーのすべてのテレメトリが保持されるか、削除されることになります。 ユーザーを定義しないタイプのアプリケーション (Web サービスなど) の場合、サンプリングは要求の操作 ID に基づいて決定されます。 最後に、ユーザーも操作 ID も設定されていないテレメトリ項目 (http コンテキストのない、非同期スレッドから報告されたテレメトリ項目など) の場合、サンプリングでは各タイプのテレメトリ項目を単純に一定の割合ずつキャプチャします。 

テレメトリを表示する場合、Application Insights サービスは、不足しているデータ ポイントを補正するために、収集時に使用したものと同じサンプリング率でメトリックを調整します。 そのため、Application Insights でテレメトリを表示する場合、ユーザーは統計的に正しく、実際の数値に非常に近い近似値を見ていることになります。

近似精度は、構成したサンプリング率に大きく左右されます。 また、多数のユーザーからの同じような要求を大量に処理するアプリケーションの場合は、近似精度が高くなります。 一方で、処理量が膨大ではないアプリケーションの場合、通常はクォータの範囲内ですべてのテレメトリを送信でき、スロットルによるデータの損失もないため、サンプリングは必要ありません。 

Application Insights では、精度が問題のあるレベルまで低下する可能性があるため、メトリックおよびセッションのテレメトリ タイプに対するサンプリングは行いません。 

### <a name="adaptive-sampling"></a>アダプティブ サンプリング
アダプティブ サンプリングの場合、SDK からの現在の転送速度を監視し、目標最大レート以下になるようにサンプリング率を調整するコンポーネントが追加されます。 調整は、定期的に、送信レートの移動平均に基づいて再計算されます。

## <a name="sampling-and-the-javascript-sdk"></a>サンプリングと JavaScript SDK
固定レート サンプリングでは、クライアント側 (JavaScript) SDK とサーバー側 SDK を組み合わせて使用します。 SDK が追加されたページは、サーバー側が "サンプリングに入れる" と判断したユーザーと同じユーザーからのクライアント側テレメトリのみを送信します。 これは、クライアント側とサーバー側の間でユーザー セッションの整合性を維持するためのロジックです。 その結果、Application Insights 内の特定のテレメトリ項目から、このユーザーまたはセッションに関するその他すべてのテレメトリ項目を見つけることができます。 

*"クライアント側とサーバー側のテレメトリに、前述されているような調整済みのサンプルが表示されない場合。"*

* サーバーとクライアントの両方で固定レート サンプリングを有効にしていることを確認してください。
* SDK のバージョンが 2.0 以降であることを確認してください。
* クライアントとサーバーの両方で同じサンプリング率を設定していることを確認してください。

## <a name="frequently-asked-questions"></a>よく寄せられる質問
*サンプリングを、"各テレメトリ タイプの X% を収集" という単純な方法にしないのはなぜですか。*

* このサンプリング方法の場合、メトリックの近似精度は非常に高くなりますが、一方でユーザーごと、セッションごと、要求ごとに診断データを関連付けるという、診断には欠かせない機能が利用できなくなります。 そのため、サンプリングには "アプリ ユーザーの X% に関するすべてのテレメトリ項目を収集"、または "アプリ要求の X% に関するすべてのテレメトリを収集" というロジックが適しています。 要求に関連付けられていないテレメトリ項目 (バックグラウンドの非同期処理など) の場合は、代わりに "各テレメトリ タイプに関するすべての項目の X% を収集" とします。 

*サンプリング率は、時間経過に伴い変化する可能性がありますか。*

* はい。アダプティブ サンプリングの場合、現在監視されているテレメトリのデータ量に基づいて、サンプリング率が徐々に変化します。

*固定レート サンプリング率を使用している場合、自分のアプリに最適なサンプリング率は、どのようにして確認できますか。*

* 1 つの方法として、アダプティブ サンプリングから始め、決定されたレートを確認したら (前の質問を参照)、そのレートを使って固定レート サンプリングに切り替えます。 
  
    それ以外では、推測するしかありません。 AI での現在のテレメトリ使用状況を分析し、発生しているスロットルを観察して、収集されるテレメトリの量を見積もります。 この&3; つの情報に、選択した価格レベルを合わせて考えると、収集されるテレメトリの量をどれくらい削減すればよいかがわかります。 ただし、ユーザー数の増加やテレメトリ量の何らかの変化によって、見積りが無効になることがあります。

*サンプリング率を低く構成しすぎると、どうなりますか。*

* Application Insights がデータ量の減少に関してデータの可視化を補正しようとしている場合、極端に低いサンプリング率 (過度にアグレッシブなサンプリング) は、近似精度の低下につながります。 また、低頻度の失敗や遅い要求の一部はサンプリングから除外される場合があるため、診断機能に悪影響を及ぼす可能性もあります。

*サンプリング率を高く構成しすぎると、どうなりますか。*

* サンプリング率を高く構成しすぎた (十分にアグレッシブでない) 場合は、収集されるテレメトリの量が十分に減少しないことになります。 スロットルに関連するテレメトリ データが失われる可能性が残り、超過料金によって Application Insights の使用コストが予定額を超える場合もあります。

*サンプリングはどのようなプラットフォームで使用できますか。*

* インジェスト サンプリングは、SDK がサンプリングを実行していない場合に任意のテレメトリが特定の量を超えると、自動的に実行される場合があります。 たとえば、アプリが Java サーバーを使用している場合、または以前のバージョンの ASP.NET SDK を使用している場合に動作します。
* ASP.NET SDK バージョン 2.0.0 以上 (Azure または独自のサーバーのいずれかでホストされる) を使用している場合、既定ではアダプティブ サンプリングが行われますが、前述の方法で固定レートに切り替えることができます。 固定レート サンプリングの場合、ブラウザー SDK は自動的にサンプル関連のイベントに同期します。 

*常に確認したい頻度の低いイベントがあります。サンプリング モジュールを使わずに確認するにはどうすればよいですか。*

* (既定のアクティブ インスタンスではなく) 新しい TelemetryConfiguration で TelemetryClient の別のインスタンスを初期化します。 そのインスタンスを使用して、頻度の低いイベントを送信してください。

## <a name="next-steps"></a>次のステップ
* [フィルター](app-insights-api-filtering-sampling.md) を使用して、SDK から送信される情報についてさらに厳密に制御できます。


