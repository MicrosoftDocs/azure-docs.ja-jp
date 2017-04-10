---
title: "Azure Cloud Services 向けの Application Insights | Microsoft Docs"
description: "Application Insights で Web と worker ロールを効果的に監視する"
services: application-insights
documentationcenter: 
author: alancameronwills
manager: carmonm
editor: alancameronwills
ms.assetid: 5c7a5b34-329e-42b7-9330-9dcbb9ff1f88
ms.service: application-insights
ms.devlang: na
ms.tgt_pltfrm: ibiza
ms.topic: article
ms.workload: tbd
ms.date: 03/14/2017
ms.author: awills
translationtype: Human Translation
ms.sourcegitcommit: fd35f1774ffda3d3751a6fa4b6e17f2132274916
ms.openlocfilehash: 5ea258a8c790dd94e019243e77f2ff694c2515d7
ms.lasthandoff: 03/16/2017


---
# <a name="application-insights-for-azure-cloud-services"></a>Azure Cloud Services 向けの Application Insights
[Microsoft Azure クラウド サービス アプリ](https://azure.microsoft.com/services/cloud-services/)の可用性、パフォーマンス、障害、および使用状況を [Application Insights][start] で監視できます。 アプリのパフォーマンスと効果に関するフィードバックが得られたら、各開発ライフサイクルにおける設計の方向性について、情報に基づいて選択できます。

![例](./media/app-insights-cloudservices/sample.png)

## <a name="before-you-start"></a>開始する前に
必要なものは次のとおりです。

* [Microsoft Azure](http://azure.com) のサブスクリプション。 Windows、XBox Live、またはその他の Microsoft クラウド サービスに与えられる Microsoft アカウントでサインインします。 
* Microsoft Azure Tools 2.9 以降
* Developer Analytics Tools 7.10 以降

## <a name="quick-start"></a>クイック スタート
Application Insights を使ってクラウド サービスを監視する最も早くて簡単な方法は、サービスを Azure に発行するときにそのオプションを選択することです。

![例](./media/app-insights-cloudservices/azure-cloud-application-insights.png)

このオプションではアプリが実行時にインストルメント化され、Web ロールで要求、例外、依存関係を監視したり、worker ロールからパフォーマンス カウンターを監視したりするのに必要なすべてのテレメトリが提供されます。 アプリによって生成された診断トレースも Application Insights に送信されます。

このほかに必要な情報がない場合は、これで完了です。 続く手順は、[アプリからのメトリックの表示](app-insights-metrics-explorer.md)、[Analytics によるデータのクエリの実行](app-insights-analytics.md)、および場合によっては[ダッシュボード](app-insights-dashboards.md)の設定です。 [可用性テスト](app-insights-monitor-web-app-availability.md)を設定し、[Web ページにコードを追加](app-insights-javascript.md)してブラウザーでのパフォーマンスを監視することもできます。

次のようなその他のオプションもあります。

* さまざまなコンポーネントからデータを送信し、リソースを分離するための構成をビルドします。
* アプリからカスタム テレメトリを追加します。

これらのオプションに関心がある場合は、引き続きお読みください。

## <a name="sample-application-instrumented-with-application-insights"></a>Application Insights を使用してインストルメント化されたサンプル アプリケーション
Application Insights がクラウド サービスに追加され、2 つの woker ロールが Azure でホストされている [サンプル アプリケーション](https://github.com/Microsoft/ApplicationInsights-Home/tree/master/Samples/AzureEmailService) をご覧ください。 

以降では、同じようにして独自のクラウド サービス プロジェクトを調整する方法について説明します。

## <a name="plan-resources-and-resource-groups"></a>リソースとリソース グループを計画する
アプリからのテレメトリは、Application Insights 型の Azure リソースで保存、分析、表示されます。 

各リソースはリソース グループに属しています。 リソース グループは、コストの管理、チーム メンバーへのアクセスの許可、単一の調整されたトランザクションでの更新のデプロイに使用します。 たとえば、1 回の操作で Azure クラウド サービスと Application Insights 監視リソースを[デプロイするスクリプトを記述](../azure-resource-manager/resource-group-template-deploy.md)できます。

### <a name="resources-for-components"></a>コンポーネント用のリソース
推奨されているスキームは、アプリケーションの各コンポーネント (それぞれの Web ロールと worker ロール) 用に個別のリソースを作成することです。 各コンポーネントを別個に分析できますが、すべてのコンポーネントから重要なグラフをまとめる[ダッシュボード](app-insights-dashboards.md)を作成し、まとめて監視することもできます。 

代替スキームでは、複数のロールから同じリソースにテレメトリを送信しますが、ソース ロールを識別する[各テレメトリ項目にディメンション プロパティを追加](app-insights-api-filtering-sampling.md#add-properties-itelemetryinitializer)します。 このスキームでは、例外などのメトリック グラフに通常はさまざまなロールからのカウントの集計が表示されますが、必要な場合にロール識別子を使用してグラフを分割することができます。 検索も同じディメンションによってフィルターできます。 このスキームではすべてを同時に表示するのが少し簡単になりますが、ロール間で混乱を招く可能性もあります。

通常、ブラウザーのテレメトリはサーバー側の Web ロールと同じリソースに含まれています。

さまざまなコンポーネント用の Application Insights リソースを 1 つのリソース グループに配置します。 これにより、まとめて管理することが簡単になります。 

### <a name="separating-development-test-and-production"></a>開発、テスト、および運用の分離
前のバージョンがライブである間に次の機能用のカスタム イベントを開発する場合、開発に関するテレメトリを別の Application Insights リソースに送信する必要があります。 そうしないと、ライブ サイトからのすべてのトラフィックの中からテストに関するテレメトリを見つけることは難しくなります。

この状況を回避するには、システムの各ビルド構成または「スタンプ」(開発、テスト、運用など) 用に別個のリソースを作成します。 各ビルド構成用のリソースを別個のリソース グループに配置します。 

テレメトリを適切なリソースに送信するには、ビルド構成に応じて異なるインストルメンテーション キーを選択するように Application Insights SDK を設定できます。 

## <a name="create-an-application-insights-resource-for-each-role"></a>役割ごとに Application Insights リソースを作成する
各ロール用に別個のリソースを作成する (場合によっては各ビルド構成用に別個のセットも作成する) ことにした場合は、Application Insights ポータルでそのすべてを作成するのが最も簡単です。 (リソースを多く作成する場合は、[プロセスを自動化](app-insights-powershell.md)できます。

1. [Azure Portal][portal] で、新しい Application Insights リソースを作成します。 アプリケーションの種類として ASP.NET アプリを選択します。 

    ![[新規]、[Application Insights] の順にクリックする](./media/app-insights-cloudservices/01-new.png)
2. 各リソースはインストルメンテーション キーによって識別されることに注意してください。 これは、後で SDK を手動で構成する場合や SDK の構成を確認する場合に必要になる可能性があります。

    ![[プロパティ] をクリックし、キーを選択して、Ctrl キーを押しながら C キーを押す](./media/app-insights-cloudservices/02-props.png) 

## <a name="set-up-azure-diagnostics-for-each-role"></a>Set up Azure Diagnostics for each role (各ロール用の Azure 診断を設定する)
このオプションは、Application Insights でアプリを監視する場合に設定します。 Web ロールの場合は、これによってパフォーマンスの監視、アラート、診断に加え、使用状況の分析が提供されます。 その他のロールの場合は、再起動、パフォーマンス カウンター、System.Diagnostics.Trace への呼び出しなどの Azure 診断を検索して監視できます。 

1. Visual Studio ソリューション エクスプローラーで、&lt;[YourCloudService]&gt; の [ロール] から、各ロールのプロパティを開きます。
2. **[構成]** で **[Application Insights への診断データの送信]** を設定してから、先ほど作成した該当する Application Insights リソースを選択します。

各ビルド構成に対して別個の Application Insights リソースを使用することにした場合は、まず構成を選択します。

![各 Azure ロールのプロパティで、Application Insights を構成します。](./media/app-insights-cloudservices/configure-azure-diagnostics.png)

これにより、Application Insights のインストルメンテーション キーが `ServiceConfiguration.*.cscfg` という名前のファイルに挿入されます  ([サンプル コード](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/Samples/AzureEmailService/AzureEmailService/ServiceConfiguration.Cloud.cscfg))。

Application Insights に送信される診断情報のレベルを変更する場合は、[`.cscfg` ファイルを直接編集](app-insights-azure-diagnostics.md)できます。

## <a name="sdk"></a>各プロジェクトに SDK をインストールする
このオプションを選択すると、アプリケーションの使用状況やパフォーマンスを詳しく分析するためにカスタム ビジネス テレメトリを任意のロールに追加できるようになります。

Visual Studio で、Application Insights SDK を各クラウド アプリ プロジェクト用に構成します。

1. **Web ロール**: プロジェクトを右クリックし、**[Application Insights の構成]** または **[追加] > [Application Insights Telemetry]** を選択します。

2. **Worker ロール**: 
 * プロジェクトを右クリックし、**[Nuget パッケージの管理]** を選択します。
 * [Windows サーバー用の Application Insights](https://www.nuget.org/packages/Microsoft.ApplicationInsights.WindowsServer/) を追加します。

    ![Search for "Application Insights"](./media/app-insights-cloudservices/04-ai-nuget.png)

3. データを Application Insights リソースに送信するように SDK を構成します。

    適切なスタートアップ関数で、.cscfg ファイル内の構成設定から入手したインストルメンテーション キーを設定します。
 
    ```C#
   
     TelemetryConfiguration.Active.InstrumentationKey = RoleEnvironment.GetConfigurationSettingValue("APPINSIGHTS_INSTRUMENTATIONKEY");
    ```
   
    この手順をアプリケーションの各ロールで実行します。 次の例を参照してください。
   
   * [Web ロール](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/Samples/AzureEmailService/MvcWebRole/Global.asax.cs#L27)
   * [worker ロール](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/Samples/AzureEmailService/WorkerRoleA/WorkerRoleA.cs#L232)
   * [Web ページ向け](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/Samples/AzureEmailService/MvcWebRole/Views/Shared/_Layout.cshtml#L13) 
4. ApplicationInsights.config ファイルが常に出力ディレクトリにコピーされるように設定します。 
   
    (.config ファイルの中に、インストルメンテーション キーの配置を求めるメッセージがあります。 ただし、クラウド アプリケーションでは、それは .cscfg ファイルから設定することをお勧めします。 これにより、ポータルでロールが正確に識別されます)。

#### <a name="run-and-publish-the-app"></a>アプリを実行して発行する
アプリを実行し、Azure にサインインします。 作成した Application Insights リソースを開くと、[検索](app-insights-diagnostic-search.md)に個々のデータ ポイントが表示され、[メトリックス エクスプローラー](app-insights-metrics-explorer.md)に集計データが表示されます。 

さらにテレメトリを追加し (後のセクションを参照)、アプリを発行して、ライブの診断と使用状況のフィードバックを取得します。 

#### <a name="no-data"></a>データが表示されない場合
* [[検索]][diagnostic] タイルを開き、個々のイベントを表示します。
* アプリケーションを使用して、テレメトリがいくつか生成されるようにさまざまなページを開きます。
* 数秒待機してから [最新の情報に更新] をクリックします。
* [トラブルシューティング][qna]に関するページを参照してください。

## <a name="view-azure-diagnostic-events"></a>Azure 診断イベントを表示する
診断データは次の場所で見つかります。

* パフォーマンス カウンターは、カスタム メトリックとして表示されます。 
* Windows イベント ログは、トレースとカスタム イベントとして表示されます。
* アプリケーション ログ、ETW ログ、診断インフラストラクチャ ログは、トレースとして表示されます。

パフォーマンス カウンターおよびイベント数を表示するには、 [メトリックス エクスプローラー](app-insights-metrics-explorer.md) を開き、新しいグラフを追加します。

![Azure 診断のデータ](./media/app-insights-cloudservices/23-wad.png)

[検索](app-insights-diagnostic-search.md)または [Analytics クエリ](app-insights-analytics-tour.md)を使用して、Azure 診断によって送信されるさまざまなトレース ログを検索します。 たとえば、ハンドルされない例外が発生して、ロールがクラッシュし、リサイクルされたとします。 その情報は、Windows イベント ログのアプリケーション チャンネルに表示されます。 ユーザーは、検索を使用して Windows イベント ログのエラーを確認し、例外の完全なスタック トレースを取得できます。 この機能は、問題の根本原因の発見に役立ちます。

![Azure 診断の検索](./media/app-insights-cloudservices/25-wad.png)

## <a name="more-telemetry"></a>テレメトリの追加
以下のセクションでは、アプリケーションの異なる部分から他のテレメトリを取得する方法を示します。

## <a name="track-requests-from-worker-roles"></a>ワーカー ロールからの要求を追跡する
Web ロールでは、HTTP 要求に関するデータが要求モジュールによって自動的に収集されます。 既定の収集動作をオーバーライドする方法の例については、 [サンプルの MVCWebRole](https://github.com/Microsoft/ApplicationInsights-Home/tree/master/Samples/AzureEmailService/MvcWebRole) をご覧ください。 

HTTP 要求の場合と同じ方法で追跡することで、worker ロールの呼び出しのパフォーマンスを記録できます。 Application Insights では、"要求" 型のテレメトリは、サーバー側で名前を付け、時間を指定し、個別に成功と失敗を判定できる作業単位を測定できます。 SDK は HTTP 要求を自動的に記録します。一方で、独自のコードを挿入し、worker ロールへの要求を追跡できます。

要求を報告するためにインストルメント化された 2 つのサンプル worker ロール ([WorkerRoleA](https://github.com/Microsoft/ApplicationInsights-Home/tree/master/Samples/AzureEmailService/WorkerRoleA) と [WorkerRoleB](https://github.com/Microsoft/ApplicationInsights-Home/tree/master/Samples/AzureEmailService/WorkerRoleB)) を参照してください。

## <a name="exceptions"></a>例外
各種 Web アプリケーションから未処理の例外を収集する方法の詳細については、 [Application Insights での例外の監視](app-insights-asp-net-exceptions.md) に関するページをご覧ください。

サンプルの web ロールには MVC5 コントローラーおよび Web API 2 コントローラーが含まれています。 この 2 つからのハンドルされない例外は、以下のハンドラーを使用してキャプチャされます。

* MVC5 コントローラー向けの [AiHandleErrorAttribute](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/Samples/AzureEmailService/MvcWebRole/Telemetry/AiHandleErrorAttribute.cs) セットアップ ([こちら](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/Samples/AzureEmailService/MvcWebRole/App_Start/FilterConfig.cs#L12)を参照)
* Web API 2 コントローラー向けの [AiWebApiExceptionLogger](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/Samples/AzureEmailService/MvcWebRole/Telemetry/AiWebApiExceptionLogger.cs) セットアップ ([こちら](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/Samples/AzureEmailService/MvcWebRole/App_Start/WebApiConfig.cs#L25)を参照)

worker ロールの場合、例外を追跡する方法は 2 つあります。

* TrackException(ex)
* Application Insights トレース リスナー NuGet パッケージを追加した場合は、**System.Diagnostics.Trace** を使用して例外を記録できます。 [コード例はこちらです。](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/Samples/AzureEmailService/WorkerRoleA/WorkerRoleA.cs#L107)

## <a name="performance-counters"></a>パフォーマンス カウンター
既定では、次のカウンターが収集されます。

    * \Process(??APP_WIN32_PROC??)\%Processor Time
    * \Memory\Available Bytes
    * \.NET CLR Exceptions(??APP_CLR_PROC??)\# of Exceps Thrown / sec
    * \Process(??APP_WIN32_PROC??)\Private Bytes
    * \Process(??APP_WIN32_PROC??)\IO Data Bytes/sec
    * \Processor(_Total)\% Processor Time

Web ロールの場合、以下のカウンターも収集されます。

    * \ASP.NET Applications(??APP_W3SVC_PROC??)\Requests/Sec
    * \ASP.NET Applications(??APP_W3SVC_PROC??)\Request Execution Time
    * \ASP.NET Applications(??APP_W3SVC_PROC??)\Requests In Application Queue

追加のカスタム パフォーマンス カウンターやその他の Windows パフォーマンス カウンターは、[この例のように](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/Samples/AzureEmailService/WorkerRoleA/ApplicationInsights.config#L14) ApplicationInsights.config を編集することで指定できます。

  ![パフォーマンス カウンター](./media/app-insights-cloudservices/OLfMo2f.png)

## <a name="correlated-telemetry-for-worker-roles"></a>worker ロールのテレメトリの関連付け
豊富な診断のエクスペリエンスでは、要求が失敗した場合や待機時間が長い場合の原因を確認できます。 Web ロールを使用すると、SDK によって関係するテレメトリの間に関連付けが自動的に設定されます。 worker ロールの場合は、カスタムのテレメトリ初期化子を使用して、すべてのテレメトリに共通の Operation.Id context 属性を設定することで、これを実現できます。 これにより、待機時間/失敗の問題の原因が依存関係とコードのどちらにあるかを一目で把握することができます。 

その方法は次のとおりです。

* [こちら](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/Samples/AzureEmailService/WorkerRoleA/WorkerRoleA.cs#L36)に示すように、CallContext に関連付け ID を設定します。 このケースでは、要求 ID を関連付け ID として使用しています。
* カスタムの TelemetryInitializer 実装を追加します。これにより、上で設定した correlationId に Operation.Id が設定されます。 例についてはこちらをご覧ください ([ItemCorrelationTelemetryInitializer](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/Samples/AzureEmailService/WorkerRoleA/Telemetry/ItemCorrelationTelemetryInitializer.cs#L13))。
* カスタムのテレメトリ初期化子を追加します。 これは、ApplicationInsights.config ファイル内か、 [こちら](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/Samples/AzureEmailService/WorkerRoleA/WorkerRoleA.cs#L233)

これで完了です。 ポータル エクスペリエンスは既に、関連付けられたすべてのテレメトリを一目で確認できるように設定されています。

![相関関係を持つテレメトリ](./media/app-insights-cloudservices/bHxuUhd.png)

## <a name="client-telemetry"></a>クライアント テレメトリ
[Web ページに JavaScript SDK を追加][client]して、ページ ビュー数、ページの読み込み時間、スクリプトの例外などのブラウザー ベースのテレメトリを取得し、ページ スクリプトにカスタムのテレメトリを記述できます。

## <a name="availability-tests"></a>可用性テスト
[Web テストを設定][availability]して、アプリケーションが動作していて応答できることを確認します。

## <a name="display-everything-together"></a>すべてをまとめて表示する
システムの全体像を把握するため、主要な監視グラフを[ダッシュ ボード](app-insights-dashboards.md)でまとめることができます。 たとえば、各ロールの要求と失敗のカウントをピン留めできます。 

システムで Stream Analytics などの他の Azure サービスが使用されている場合は、その監視グラフも含めます。 

クライアント モバイル アプリがある場合は、主要なユーザー操作の際にカスタム イベントを送信するコードを挿入してから、[HockeyApp ブリッジ](app-insights-hockeyapp-bridge-app.md)を作成します。 [Analytics](app-insights-analytics.md) でクエリを作成してイベント カウントを表示してから、それをダッシュボードにピン留めします。

## <a name="example"></a>例
[この例](https://github.com/Microsoft/ApplicationInsights-Home/tree/master/Samples/AzureEmailService) では、1 つの Web ロールと 2 つの worker ロールが含まれたサービスを監視します。

## <a name="exception-method-not-found-on-running-in-azure-cloud-services"></a>Azure Cloud Services で実行する際の例外「メソッドが見つかりません」
.NET 4.6 でビルドした場合 4.6 は Azure Cloud Services のロールでは自動的にサポートされていません。 アプリを実行する前に、[各ロールに 4.6 をインストール](../cloud-services/cloud-services-dotnet-install-dotnet.md)してください。

## <a name="video"></a>ビデオ

> [!VIDEO https://channel9.msdn.com/events/Connect/2016/100/player]

## <a name="next-steps"></a>次のステップ
* [Application Insights に Azure 診断を送信するための構成](app-insights-azure-diagnostics.md)
* [Application Insights リソースの作成の自動化](app-insights-powershell.md)
* [Azure 診断の自動化](app-insights-powershell-azure-diagnostics.md)

[api]: app-insights-api-custom-events-metrics.md
[availability]: app-insights-monitor-web-app-availability.md
[azure]: app-insights-azure.md
[client]: app-insights-javascript.md
[diagnostic]: app-insights-diagnostic-search.md
[netlogs]: app-insights-asp-net-trace-logs.md
[portal]: http://portal.azure.com/
[qna]: app-insights-troubleshoot-faq.md
[redfield]: app-insights-monitor-performance-live-website-now.md
[start]: app-insights-overview.md 

