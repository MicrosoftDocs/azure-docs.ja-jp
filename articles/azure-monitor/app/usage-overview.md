---
title: Azure Application Insights による利用状況分析 | Microsoft docs
description: ユーザーを理解し、提供しているアプリでユーザーが何を実行するかを理解します。
services: application-insights
documentationcenter: ''
author: NumberByColors
manager: carmonm
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 10/10/2017
ms.pm_owner: daviste;NumberByColors
ms.reviewer: mbullwin
ms.author: daviste
ms.openlocfilehash: ba29688958ee11aa9906a820f7a3d2bf41223743
ms.sourcegitcommit: 66237bcd9b08359a6cce8d671f846b0c93ee6a82
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/11/2019
ms.locfileid: "67798177"
---
# <a name="usage-analysis-with-application-insights"></a>Application Insights による利用状況分析

最も人気のある Web アプリまたはモバイル アプリの機能は何か。 そのアプリによりユーザーは目標を達成したか。 特定の時点でアプリを離れたか。その後、利用を再開したか。  [Azure Application Insights](../../azure-monitor/app/app-insights-overview.md) は、ユーザーのアプリの使用方法に関する強力な洞察を得るのに役立ちます。 アプリを更新するたびに、アプリがユーザーにどの程度役立っているかを確認できます。 この知識により、次の開発サイクルに関してデータ駆動型の意思決定を行うことができます。

## <a name="send-telemetry-from-your-app"></a>アプリからテレメトリを送信する

Application Insights をアプリのサーバー コードと Web ページの両方にインストールすることにより、最適な操作環境が得られます。 アプリのクライアントおよびサーバー コンポーネントから Azure Portal に分析用のテレメトリが送信されます。

1. **サーバー コード:** [ASP.NET](../../azure-monitor/app/asp-net.md)、[Azure](../../azure-monitor/app/app-insights-overview.md)、[Java](../../azure-monitor/app/java-get-started.md)、[Node.js](../../azure-monitor/app/nodejs.md)、または[その他](../../azure-monitor/app/platforms.md)のアプリ向けの適切なモジュールをインストールします。

    * "*サーバー コードをインストールしたくない場合は、[Azure Application Insights リソースの作成](../../azure-monitor/app/create-new-resource.md )のみを行ってください。* "

2. **Web ページ コード:** Web ページの閉じる ``</head>`` タグの前に、次のスクリプトを追加します。 インストルメンテーション キーを Application Insights リソースの適切な値に置き換えます。

   ```javascript
      <script type="text/javascript">
        var appInsights=window.appInsights||function(a){
            function b(a){c[a]=function(){var b=arguments;c.queue.push(function(){c[a].apply(c,b)})}}var c={config:a},d=document,e=window;setTimeout(function(){var b=d.createElement("script");b.src=a.url||"https://az416426.vo.msecnd.net/scripts/a/ai.0.js",d.getElementsByTagName("script")[0].parentNode.appendChild(b)});try{c.cookie=d.cookie}catch(a){}c.queue=[];for(var f=["Event","Exception","Metric","PageView","Trace","Dependency"];f.length;)b("track"+f.pop());if(b("setAuthenticatedUserContext"),b("clearAuthenticatedUserContext"),b("startTrackEvent"),b("stopTrackEvent"),b("startTrackPage"),b("stopTrackPage"),b("flush"),!a.disableExceptionTracking){f="onerror",b("_"+f);var g=e[f];e[f]=function(a,b,d,e,h){var i=g&&g(a,b,d,e,h);return!0!==i&&c["_"+f](a,b,d,e,h),i}}return c
        }({
            instrumentationKey: "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxx"
        });
        
        window.appInsights=appInsights,appInsights.queue&&0===appInsights.queue.length&&appInsights.trackPageView();
    </script>
    ```
    Web サイトを監視するためのより高度な構成については、[JavaScript SDK の API リファレンス](https://github.com/Microsoft/ApplicationInsights-JS/blob/master/API-reference.md)を参照してください。

3. **モバイル アプリ コード:** [このガイド](../../azure-monitor/learn/mobile-center-quickstart.md)に従い、App Center SDK を使ってアプリからイベントを収集し、これらのイベントのコピーを分析のために Application Insights に送信します。

4. **テレメトリの取得:** プロジェクトをデバッグ モードで数分間実行し、Application Insights の [概要] ブレードで結果を確認します。

    アプリを発行し、アプリのパフォーマンスを監視してユーザーがアプリを使って何をしているか確認します。

## <a name="include-user-and-session-id-in-your-telemetry"></a>ユーザー ID とセッション ID をテレメトリに含める
Application Insights で一定期間にわたってユーザーを追跡するためには、それらのユーザーを識別する手段が必要となります。 ユーザー ID やセッション ID を必要としない使用状況ツールはイベント ツールだけです。

[このプロセス](https://docs.microsoft.com/azure/application-insights/app-insights-usage-send-user-context)を使用してユーザー ID とセッション ID の送信を開始します。

## <a name="explore-usage-demographics-and-statistics"></a>使用状況の人口統計データや統計を調査する
ユーザーがアプリをいつ使い、どのページに最も興味があり、ユーザーがどこにいて、どのようなブラウザーやオペレーティング システムを使っているかを確認しましょう。 

[ユーザー] および [セッション] レポートでは、ページごとまたはカスタム イベントごとにデータをフィルター処理し、場所、環境、ページなどのプロパティでデータをセグメント化できます。 独自のフィルターを追加することもできます。

![ユーザー](./media/usage-overview/users.png)  

右側の洞察では、データのセットで興味深いパターンが示されています。  

* **[ユーザー]** レポートは、選択した期間内にページにアクセスした一意のユーザー数をカウントします。 Web アプリの場合、ユーザーは Cookie を使用してカウントされます。 ユーザーが異なるブラウザーまたはクライアント マシンを使用してサイトにアクセスした場合、または Cookie を消去した場合は、複数回カウントされます。
* **[セッション]** レポートは、サイトにアクセスしたユーザー セッションの数をカウントします。 セッションとは、ユーザーによるアクティビティの期間で、30 分以上の非アクティブ状態で終了します。

[ユーザー、セッション、およびイベント ツールに関する詳細](usage-segmentation.md)  

## <a name="retention---how-many-users-come-back"></a>リテンション期間 - サービスの利用を再開したユーザーの数

リテンション期間では、一定のタイム バケットでビジネス アクションを実行したユーザーのコーホートに基づいて、ユーザーがアプリの利用を再開した頻度を把握できます。 

- どのような特定の機能により、どのような特定のユーザーが使用を再開したかを把握します 
- 実際のユーザー データに基づいて仮説を立てます 
- 製品でリテンション期間が問題になるかどうかを確認します。 

![保持](./media/usage-overview/retention.png) 

上部のリテンション期間コントロールでは、特定のイベントと時間範囲を定義して、リテンション期間を計算できます。 中央のグラフは、指定した時間範囲別のリテンション期間全体のパーセンテージを視覚的に表しています。 下部のグラフは、特定の期間における個々のリテンション期間を表しています。 この詳細レベルでは、ユーザーが何を実行し、どのような理由でユーザーが使用を再開するかをさらに詳しく把握できます。  

[リテンション期間について詳細](usage-retention.md)

## <a name="custom-business-events"></a>カスタム ビジネス イベント

アプリでユーザーが何を行っているかを明確に把握するには、カスタム イベントをログに記録するコード行を挿入すると便利です。 これらのイベントにより、特定のボタンのクリックなどの詳細なユーザー アクションから、購入、ゲームに勝つなどのより重要なビジネス イベントまで追跡できます。 

ページ ビューでは、役立つイベントを表すことができる場合もありますが、通常そうではないことがほとんどです。 ユーザーは、製品を購入しなくても製品ページを開くことができます。 

特定のビジネス イベントを使用して、サイトからユーザーの進行状況をグラフ化できます。 さまざまなオプションについて、ユーザーの嗜好や、どの段階で使用を止め、どのような問題が発生しているかを確認できます。 この知識があれば、開発バックログでの優先順位について情報に基づいた意思決定を行うことができます。

イベントは、アプリのクライアント側からログに記録することができます。

```JavaScript

    appInsights.trackEvent("ExpandDetailTab", {DetailTab: tabName});
```

または、サーバー側から記録することもできます。

```csharp
    var tc = new Microsoft.ApplicationInsights.TelemetryClient();
    tc.TrackEvent("CreatedAccount", new Dictionary<string,string> {"AccountType":account.Type}, null);
    ...
    tc.TrackEvent("AddedItemToCart", new Dictionary<string,string> {"Item":item.Name}, null);
    ...
    tc.TrackEvent("CompletedPurchase");
```

これらのイベントにプロパティ値をアタッチできます。これにより、ポータルでイベントを確認するときに、イベントをフィルター処理または分割できます。 また、匿名ユーザー ID などのプロパティの標準セットが各イベントにアタッチされます。これにより、個人ユーザーのアクティビティのシーケンスを追跡できます。

[カスタム イベント](../../azure-monitor/app/api-custom-events-metrics.md#trackevent)と[プロパティ](../../azure-monitor/app/api-custom-events-metrics.md#properties)の詳細については、こちらを参照してください。

### <a name="slice-and-dice-events"></a>イベントの詳細な分析

[ユーザー]、[セッション]、および [イベント] ツールでは、ユーザー、イベント名、プロパティごとにカスタム イベントを詳細に分析することができます。
![ユーザー](./media/usage-overview/users.png)  
  
## <a name="design-the-telemetry-with-the-app"></a>アプリでのテレメトリの設計

アプリの各機能を設計するときに、ユーザーの満足度をどのように測定するかを検討します。 記録する必要のあるビジネス イベントを決定し、これらのイベントの追跡呼び出しを一からアプリにコーディングします。

## <a name="a--b-testing"></a>A | B テスト
ある機能のどちらのバージョンが成功するかわからない場合は、その両方をリリースして、それぞれを異なるユーザーが利用できるようにします。 各バージョンの成功の度合いを測定してから、統合したバージョンに移行します。

この手法では、アプリの各バージョンから送信されるすべてのテレメトリに異なるプロパティ値をアタッチします。 これは、アクティブな TelemetryContext のプロパティを定義することで実行できます。 このような既定のプロパティは、カスタム メッセージだけでなく、標準のテレメトリも同様に、アプリケーションから送信されるすべてのテレメトリ メッセージに追加されます。

Application Insights ポータルでは、プロパティ値に基づいてデータをフィルター選択および分割して、異なるバージョンを比較します。

これを行うには、[テレメトリ初期化子を設定](../../azure-monitor/app/api-filtering-sampling.md#add-properties-itelemetryinitializer)します。

**ASP.NET アプリ**

```csharp
    // Telemetry initializer class
    public class MyTelemetryInitializer : ITelemetryInitializer
    {
        public void Initialize (ITelemetry telemetry)
        {
            telemetry.Properties["AppVersion"] = "v2.1";
        }
    }
```

Web アプリ初期化子 (Global.asax.cs など) 内:

```csharp

    protected void Application_Start()
    {
        // ...
        TelemetryConfiguration.Active.TelemetryInitializers
         .Add(new MyTelemetryInitializer());
    }
```

**ASP.NET Core アプリ**

> [!NOTE]
> `ApplicationInsights.config` または `TelemetryConfiguration.Active` を使用して初期化子を追加することは、ASP.NET Core アプリケーションでは無効です。 

[ASP.NET Core](asp-net-core.md#adding-telemetryinitializers) アプリケーションの場合、新しい `TelemetryInitializer` を追加するには、次に示すように Dependency Injection コンテナーに追加します。 これは `Startup.cs` クラスの `ConfigureServices` メソッドで行われます。

```csharp
 using Microsoft.ApplicationInsights.Extensibility;
 using CustomInitializer.Telemetry;
 public void ConfigureServices(IServiceCollection services)
{
    services.AddSingleton<ITelemetryInitializer, MyTelemetryInitializer>();
}
```

すべての新しい TelemetryClients により、指定したプロパティ値が自動的に追加されます。 個々のテレメトリ イベントは、既定値をオーバーライドすることができます。

## <a name="next-steps"></a>次の手順
   - [ユーザー、セッション、イベント](usage-segmentation.md)
   - [ファネル](usage-funnels.md)
   - [保持](usage-retention.md)
   - [ユーザー フロー](usage-flows.md)
   - [ブック](../../azure-monitor/app/usage-workbooks.md)
   - [ユーザー コンテキストの追加](usage-send-user-context.md)
