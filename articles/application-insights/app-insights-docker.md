---
title: "Application Insights で Docker アプリケーションを監視する | Microsoft Docs"
description: "Docker のパフォーマンス カウンター、イベント、および例外を、コンテナー化されたアプリからのテレメトリと共に Application Insights に表示できます。"
services: application-insights
documentationcenter: 
author: alancameronwills
manager: carmonm
ms.assetid: 27a3083d-d67f-4a07-8f3c-4edb65a0a685
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 03/14/2017
ms.author: awills
translationtype: Human Translation
ms.sourcegitcommit: fd35f1774ffda3d3751a6fa4b6e17f2132274916
ms.openlocfilehash: b888f3e7fd56aaf8104c1b76264a3753ca37f7c9
ms.lasthandoff: 03/16/2017


---
# <a name="monitor-docker-applications-in-application-insights"></a>Application Insights で Docker アプリケーションを監視する
[Docker](https://www.docker.com/) コンテナーから取得したライフサイクル イベントとパフォーマンス カウンターを Application Insights でグラフ化できます。 [Application Insights](app-insights-overview.md) イメージをホストのコンテナーにインストールすると、ホストとその他のイメージのパフォーマンス カウンターが表示されます。

Docker で、すべての依存関係を備えた軽量コンテナーにアプリを配布します。 これらは、Docker エンジンを実行しているすべてのホスト コンピューターで実行します。

Docker ホストで [Application Insights イメージ](https://hub.docker.com/r/microsoft/applicationinsights/) を実行すると、次のメリットが得られます。

* ホストで実行されているすべてのコンテナーに関するライフサイクル テレメトリ (開始や停止など)。
* すべてのコンテナーのパフォーマンス カウンター。 CPU、メモリ、ネットワークの使用状況など。
* コンテナーで実行されているアプリに [Application Insights SDK for Java をインストールした](app-insights-java-live.md) 場合、これらのアプリのすべてのテレメトリにコンテナーとホスト マシンを識別するプロパティが追加されます。 たとえば、1 つ以上のホストで実行中のアプリのインスタンスがある場合、アプリのテレメトリをホスト別に簡単にフィルター処理できます。

![例](./media/app-insights-docker/00.png)

## <a name="set-up-your-application-insights-resource"></a>Application Insights リソースを設定する
1. [Microsoft Azure Portal](https://azure.com) にサインインし、アプリ用の Application Insights リソースを開きます。または[新しく作成](app-insights-create-new-resource.md)します。 
   
    *どのリソースを使用する必要があるか。* ホストで実行されているアプリが他者によって開発されている場合は、[新しい Application Insights リソースを作成する](app-insights-create-new-resource.md)必要があります。 テレメトリの表示と分析はこの場所で行います  (アプリの種類には [General (一般)] を選択します)。
   
    ただし、アプリの開発者である場合は、各アプリに [Application Insights SDK を追加する](app-insights-java-live.md) ことをお勧めします。 すべてのアプリが 1 つのビジネス アプリケーションのコンポーネントである場合は、テレメトリを 1 つのリソースに送信するように構成することで、同じリソースを使用して Docker のライフサイクルとパフォーマンスのデータを表示できます。 
   
    3 番目のシナリオは、アプリの大半は自分で開発しているが、それらのテレメトリの表示には別のリソースを使用している場合です。 その場合は、Docker データ用の別個のリソースを作成できます。 
2. Docker タイルを追加します。**[タイルの追加]** を選択し、ギャラリーから Docker タイルをドラッグした後、**[完了]** をクリックします。 
   
    ![例](./media/app-insights-docker/03.png)
3. **[要点]** ドロップダウンをクリックし、インストルメンテーション キーをコピーします。 これを使用して SDK にテレメトリの送信先を指示します。

    ![例](./media/app-insights-docker/02-props.png)

すぐにまた戻ってテレメトリを見るので、ブラウザー ウィンドウはそのままにします。

## <a name="run-the-application-insights-monitor-on-your-host"></a>ホスト上で Application Insights モニターを実行する
テレメトリを表示する場所ができたので、テレメトリを収集して送信するコンテナー化されたアプリを設定できます。

1. Docker ホストに接続します。 
2. インストルメンテーション キーを次のコマンドで編集して実行します。
   
   ```
   
   docker run -v /var/run/docker.sock:/docker.sock -d microsoft/applicationinsights ikey=000000-1111-2222-3333-444444444
   ```

Docker ホストごとに 1 つの Application Insights イメージが必要です。 アプリケーションが複数の Docker ホストにデプロイされている場合は、上のコマンドをすべてのホストで繰り返します。

## <a name="update-your-app"></a>アプリケーションを更新する
アプリケーションが [Application Insights SDK for Java](app-insights-java-get-started.md) でインストルメント化されている場合は、次の行を、プロジェクト内の ApplicationInsights.xml ファイルの `<TelemetryInitializers>` 要素の下に追加します。

```xml

    <Add type="com.microsoft.applicationinsights.extensibility.initializer.docker.DockerContextInitializer"/> 
```

これで、アプリから送信されるすべてのテレメトリ項目にコンテナーやホスト ID などの Docker 情報が追加されます。

## <a name="view-your-telemetry"></a>テレメトリの表示
Azure ポータルで Application Insights リソースに戻ります。

Docker タイルをクリックします。

特に Docker エンジンで他のコンテナーを実行している場合は、Docker コンテナーから到着するデータがすぐに表示されます 。

取得できるビューのいくつかを次に示します。

### <a name="perf-counters-by-host-activity-by-image"></a>ホスト、アクティビティ、イメージ別のパフォーマンス カウンター
![例](./media/app-insights-docker/10.png)

![例](./media/app-insights-docker/11.png)

詳細を表示するホストまたはイメージ名をクリックします。

ビューをカスタマイズするには、グラフまたはグリッド見出しをクリックするか、[グラフの追加] を使用します。 

[メトリックス エクスプローラーの詳細情報](app-insights-metrics-explorer.md)

### <a name="docker-container-events"></a>Docker コンテナーのイベント
![例](./media/app-insights-docker/13.png)

個々のイベントを調べるには、 [[検索]](app-insights-diagnostic-search.md)をクリックします。 検索およびフィルターを使用して必要なイベントを探します。 イベントをクリックすると詳細情報が表示されます。

### <a name="exceptions-by-container-name"></a>コンテナー名別の例外
![例](./media/app-insights-docker/14.png)

### <a name="docker-context-added-to-app-telemetry"></a>アプリのテレメトリに追加された Docker コンテキスト
AI SDK でインストルメント化されたアプリケーションから送信されたテレメトリを Docker コンテキスト付きで要求します。

![例](./media/app-insights-docker/16.png)

プロセッサ時間と使用可能メモリのパフォーマンス カウンター。Docker コンテナー名によってグループ化されています。

![例](./media/app-insights-docker/15.png)

## <a name="q--a"></a>Q & A
*Docker からは取得できず、Application Insights からは取得できるものは何ですか*

* コンテナーおよびイメージ別のパフォーマンス カウンターの詳細情報です。
* コンテナーとアプリ データが 1 つのダッシュボードに統合されます。
* [テレメトリをエクスポート](app-insights-export-telemetry.md) できます。

*アプリ自体からテレメトリを取得するにはどうすればよいですか*

* Application Insights SDK をアプリにインストールします。 詳細情報: [Java Web アプリ](app-insights-java-get-started.md)、[Windows Web アプリ](app-insights-asp-net.md)。

## <a name="video"></a>ビデオ

> [!VIDEO https://channel9.msdn.com/events/Connect/2016/100/player]

## <a name="next-steps"></a>次のステップ

* [Java 向けの Application Insights](app-insights-java-get-started.md)
* [Node.js 向けの Application Insights](app-insights-nodejs.md)
* [ASP.NET 向けの Application Insights](app-insights-asp-net.md)

