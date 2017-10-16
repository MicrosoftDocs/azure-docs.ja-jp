---
title: "Azure Application Insights による Web アプリケーションの利用状況分析 | Microsoft Docs"
description: "ユーザーを理解し、提供している Web アプリでユーザーが何を実行するかを理解します。"
services: application-insights
documentationcenter: 
author: botatoes
manager: carmonm
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: multiple
ms.topic: article
ms.date: 05/03/2017
ms.author: bwren
ms.openlocfilehash: edf15e72c822ea5e045895c6f03477c613c0a6c0
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="usage-analysis-for-web-applications-with-application-insights"></a>Application Insights による Web アプリケーションの利用状況の分析

最も人気のある Web アプリの機能は何か。 そのアプリによりユーザーは目標を達成したか。 特定の時点でアプリを離れたか。その後、利用を再開したか。  [Azure Application Insights](app-insights-overview.md) では、ユーザーの Web アプリの使用方法に関する強力な洞察を得ることができます。 アプリを更新するたびに、アプリがユーザーにどの程度役立っているかを確認できます。 この知識により、次の開発サイクルに関してデータ駆動型の意思決定を行うことができます。

## <a name="send-telemetry-from-your-app"></a>アプリからテレメトリを送信する

Application Insights をアプリのサーバー コードと Web ページの両方にインストールすることにより、最適な操作環境が得られます。 アプリのクライアントおよびサーバー コンポーネントから Azure Portal に分析用のテレメトリが送信されます。

1. **サーバー コード:** [ASP.NET](app-insights-asp-net.md)、[Azure](app-insights-azure.md)、[Java](app-insights-java-get-started.md)、[Node.js](app-insights-nodejs.md)、または[その他](app-insights-platforms.md)のアプリ向けの適切なモジュールをインストールします。

    * "*サーバー コードをインストールしたくない場合は、[Azure Application Insights リソースの作成](app-insights-create-new-resource.md)のみを行ってください。*"

2. **Web ページ コード:** [Azure Portal](https://portal.azure.com) でアプリの Application Insights リソースを開いてから、**[はじめに]、[クライアント側アプリケーションの監視と診断]** の順に開きます。 

    ![マスター Web ページの先頭にスクリプトをコピーします。](./media/app-insights-usage-overview/02-monitor-web-page.png)


3. **テレメトリの取得:** プロジェクトをデバッグ モードで数分間実行し、Application Insights の [概要] ブレードで結果を確認します。

    アプリを発行し、アプリのパフォーマンスを監視してユーザーがアプリを使って何をしているか確認します。

## <a name="include-user-and-session-id-in-your-telemetry"></a>ユーザー ID とセッション ID をテレメトリに含める
Application Insights で一定期間にわたってユーザーを追跡するためには、それらのユーザーを識別する手段が必要となります。 ユーザー ID やセッション ID を必要としない使用状況ツールはイベント ツールだけです。

[このプロセス](https://docs.microsoft.com/azure/application-insights/app-insights-usage-send-user-context)を使用してユーザー ID とセッション ID の送信を開始します。

## <a name="explore-usage-demographics-and-statistics"></a>使用状況の人口統計データや統計を調査する
ユーザーがアプリをいつ使い、どのページに最も興味があり、ユーザーがどこにいて、どのようなブラウザーやオペレーティング システムを使っているかを確認しましょう。 

[ユーザー] および [セッション] レポートでは、ページごとまたはカスタム イベントごとにデータをフィルター処理し、場所、環境、ページなどのプロパティでデータをセグメント化できます。 独自のフィルターを追加することもできます。

![ユーザー](./media/app-insights-usage-overview/users.png)  

右側の洞察では、データのセットで興味深いパターンが示されています。  

* **[ユーザー]** レポートは、選択した期間内にページにアクセスした一意のユーザー数をカウントします。 (ユーザーは Cookie を使用してカウントされます。 ユーザーが複数のブラウザーまたはクライアント コンピューターでサイトにアクセスした場合、または Cookie を消去した場合、カウントは複数回行われます。)
* **[セッション]** レポートは、サイトにアクセスしたユーザー セッションの数をカウントします。 セッションとは、ユーザーによるアクティビティの期間で、30 分以上の非アクティブ状態で終了します。

[ユーザー、セッション、およびイベント ツールに関する詳細](app-insights-usage-segmentation.md)  

## <a name="page-views"></a>ページ ビュー

[使用状況] ブレードで、[ページ ビュー] タイルをクリックすると、最も人気のあるページの内訳が表示されます。

![概要ブレードで、[ページ ビュー] グラフをクリックします](./media/app-insights-usage-overview/05-games.png)

上の例はゲーム関連の Web サイトの情報を示したグラフです。 このグラフから、すぐに以下のような情報を読み取ることができます。

* 利用状況はこの 1 週間改善されていません。 おそらく、検索エンジンの最適化について検討する必要があります。
* テニスが最も人気のあるゲーム ページです。 このページを重点的に改善しましょう。
* 平均すると、ユーザーは 1 週間に約 3 回テニス ページにアクセスしています。 (セッション数がユーザー数の約 3 倍になっています)。
* ほとんどのユーザーは、米国の就業日かつ就業時間内にこのサイトにアクセスしています。 Web ページ上に "すばやく隠す" ボタンを用意する必要があるかもしれません。
* グラフ上の[注釈](app-insights-annotations.md)は、Web サイトの新しいバージョンがデプロイされた日時を示しています。 最近行われたデプロイのうち、利用状況に顕著な影響を与えたものはありません。

ページ ビュー テレメトリで、サイトが送信したカスタム プロパティ別に分割するなど、サイトへのトラフィックをさらに詳しく調査するにはどうすればよいでしょうか。

1. Application Insights リソース メニューで **[イベント]** ツールを開きます。 このツールでは、アプリから送信されたページ ビューとカスタム イベントの数を分析できます。ここでは、フィルター処理、コーホート、セグメンテーションなどさまざまなオプションを使用できます。
2. "次を使用した" ドロップダウンで、"任意のページ ビュー" を選択します。
3. "次で分割" ドロップダウンで、ページビュー テレメトリの分割に使用するプロパティを選択します。

## <a name="retention---how-many-users-come-back"></a>リテンション期間 - サービスの利用を再開したユーザーの数

リテンション期間では、一定のタイム バケットでビジネス アクションを実行したユーザーのコーホートに基づいて、ユーザーがアプリの利用を再開した頻度を把握できます。 

- どのような特定の機能により、どのような特定のユーザーが使用を再開したかを把握します 
- 実際のユーザー データに基づいて仮説を立てます 
- 製品でリテンション期間が問題になるかどうかを確認します。 

![保持](./media/app-insights-usage-overview/retention.png) 

上部のリテンション期間コントロールでは、特定のイベントと時間範囲を定義して、リテンション期間を計算できます。 中央のグラフは、指定した時間範囲別のリテンション期間全体のパーセンテージを視覚的に表しています。 下部のグラフは、特定の期間における個々のリテンション期間を表しています。 この詳細レベルでは、ユーザーが何を実行し、どのような理由でユーザーが使用を再開するかをさらに詳しく把握できます。  

[リテンション期間について詳細](app-insights-usage-retention.md)

## <a name="custom-business-events"></a>カスタム ビジネス イベント

Web アプリでユーザーが何を行っているか明確に把握するには、コード行を挿入して、カスタム イベントをログに記録することが役立ちます。 これらのイベントにより、特定のボタンのクリックなどの詳細なユーザー アクションから、購入、ゲームに勝つなどのより重要なビジネス イベントまで追跡できます。 

ページ ビューでは、役立つイベントを表すことができる場合もありますが、通常そうではないことがほとんどです。 ユーザーは、製品を購入しなくても製品ページを開くことができます。 

特定のビジネス イベントを使用して、サイトからユーザーの進行状況をグラフ化できます。 さまざまなオプションについて、ユーザーの嗜好や、どの段階で使用を止め、どのような問題が発生しているかを確認できます。 この知識があれば、開発バックログでの優先順位について情報に基づいた意思決定を行うことができます。

イベントは、Web ページに記録することができます。

```JavaScript

    appInsights.trackEvent("ExpandDetailTab", {DetailTab: tabName});
```

または、Web アプリのサーバー側で記録することもできます。

```C#
    var tc = new Microsoft.ApplicationInsights.TelemetryClient();
    tc.TrackEvent("CreatedAccount", new Dictionary<string,string> {"AccountType":account.Type}, null);
    ...
    tc.TrackEvent("AddedItemToCart", new Dictionary<string,string> {"Item":item.Name}, null);
    ...
    tc.TrackEvent("CompletedPurchase");
```

これらのイベントにプロパティ値をアタッチできます。これにより、ポータルでイベントを確認するときに、イベントをフィルター処理または分割できます。 また、匿名ユーザー ID などのプロパティの標準セットが各イベントにアタッチされます。これにより、個人ユーザーのアクティビティのシーケンスを追跡できます。

[カスタム イベント](app-insights-api-custom-events-metrics.md#trackevent)と[プロパティ](app-insights-api-custom-events-metrics.md#properties)の詳細については、こちらを参照してください。

### <a name="slice-and-dice-events"></a>イベントの詳細な分析

[ユーザー]、[セッション]、および [イベント] ツールでは、ユーザー、イベント名、プロパティごとにカスタム イベントを詳細に分析することができます。
![ユーザー](./media/app-insights-usage-overview/users.png)  
  
## <a name="design-the-telemetry-with-the-app"></a>アプリでのテレメトリの設計

アプリの各機能を設計するときに、ユーザーの満足度をどのように測定するかを検討します。 記録する必要のあるビジネス イベントを決定し、これらのイベントの追跡呼び出しを一からアプリにコーディングします。

## <a name="a--b-testing"></a>A | B テスト
ある機能のどちらのバージョンが成功するかわからない場合は、その両方をリリースして、それぞれを異なるユーザーが利用できるようにします。 各バージョンの成功の度合いを測定してから、統合したバージョンに移行します。

この手法では、アプリの各バージョンから送信されるすべてのテレメトリに異なるプロパティ値をアタッチします。 これは、アクティブな TelemetryContext のプロパティを定義することで実行できます。 このような既定のプロパティは、カスタム メッセージだけでなく、標準のテレメトリも同様に、アプリケーションから送信されるすべてのテレメトリ メッセージに追加されます。

Application Insights ポータルでは、プロパティ値に基づいてデータをフィルター選択および分割して、異なるバージョンを比較します。

これを行うには、[テレメトリ初期化子を設定](app-insights-api-filtering-sampling.md##add-properties-itelemetryinitializer)します。

```C#


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

```C#

    protected void Application_Start()
    {
        // ...
        TelemetryConfiguration.Active.TelemetryInitializers
        .Add(new MyTelemetryInitializer());
    }
```

すべての新しい TelemetryClients により、指定したプロパティ値が自動的に追加されます。 個々のテレメトリ イベントは、既定値を上書きすることができます。

## <a name="next-steps"></a>次のステップ
   - [ユーザー、セッション、イベント](app-insights-usage-segmentation.md)
   - [ファネル](usage-funnels.md)
   - [保持](app-insights-usage-retention.md)
   - [ユーザー フロー](app-insights-usage-flows.md)
   - [ブック](app-insights-usage-workbooks.md)
   - [ユーザー コンテキストの追加](app-insights-usage-send-user-context.md)
