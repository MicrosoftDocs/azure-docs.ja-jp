---
title: Azure クラウド サービス向けの Application Insights | Microsoft Docs
description: Application Insights で Web と worker ロールを効果的に監視する
services: application-insights
documentationcenter: ''
keywords: WAD2AI, Azure Diagnostics
author: mrbullwinkle
manager: carmonm
ms.assetid: 5c7a5b34-329e-42b7-9330-9dcbb9ff1f88
ms.service: application-insights
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.workload: tbd
ms.date: 09/05/2018
ms.author: mbullwin
ms.openlocfilehash: 64995ad0560efd06bfa0084c948527e8a01e1890
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/28/2019
ms.locfileid: "67443329"
---
# <a name="application-insights-for-azure-cloud-services"></a>Azure クラウド サービス向けの Application Insights
[Application Insights][start] では、Application Insights SDK からのデータとお客様のクラウド サービスからの [Azure 診断](https://docs.microsoft.com/azure/monitoring-and-diagnostics/azure-diagnostics)データを組み合わせることで、[Azure クラウド サービス アプリ](https://azure.microsoft.com/services/cloud-services/)の可用性、パフォーマンス、障害、使用状況を監視できます。 アプリのパフォーマンスと効果に関するフィードバックが得られたら、各開発ライフサイクルにおける設計の方向性について、情報に基づいて選択できます。

![概要ダッシュボード](./media/cloudservices/overview-graphs.png)

## <a name="prerequisites"></a>前提条件
開始する前に、次のことが必要です。

* [Azure](https://azure.com) サブスクリプション。 Windows、Xbox Live、またはその他の Microsoft クラウド サービスの Microsoft アカウントでサインインします。 
* Microsoft Azure Tools 2.9 以降。
* Developer Analytics Tools 7.10 以降。

## <a name="get-started-quickly"></a>すぐに使い始められる
Application Insights を使ってクラウド サービスを監視する最も早くて簡単な方法は、サービスを Azure に発行するときにそのオプションを選択することです。

![[診断設定] ページの例](./media/cloudservices/azure-cloud-application-insights.png)

このオプションでは、お客様のアプリが実行時にインストルメント化され、お客様の Web ロールで要求、例外、依存関係を監視するのに必要なすべてのテレメトリが提供されます。 また、お客様の worker ロールからのパフォーマンス カウンターを監視します。 お客様のアプリによって生成された診断トレースも Application Insights に送信されます。

このオプションで十分な場合、これで完了です。 

次の手順は、[アプリからのメトリックの表示](../../azure-monitor/app/metrics-explorer.md)、[Analytics によるデータのクエリの実行](../../azure-monitor/app/analytics.md)です。 

ブラウザーでパフォーマンスを監視するには、[可用性テスト](../../azure-monitor/app/monitor-web-app-availability.md)を設定して、[お客様の Web ページにコードを追加](../../azure-monitor/app/javascript.md)することもできます。

以下のセクションでは、次の追加のオプションについて説明します。

* さまざまなコンポーネントからデータを送信し、リソースを分離するための構成をビルドします。
* アプリからカスタム テレメトリを追加します。

## <a name="sample-app-instrumented-with-application-insights"></a>Application Insights を使用してインストルメント化されたサンプル アプリ
こちらの[サンプル アプリ](https://github.com/Microsoft/ApplicationInsights-Home/tree/master/Samples/AzureEmailService)では、Application Insights がクラウド サービスに追加され、2 つの worker ロールが Azure でホストされています。 

次のセクションでは、同じ方法でお客様自身のクラウド サービス プロジェクトを調整する方法について説明します。

## <a name="plan-resources-and-resource-groups"></a>リソースとリソース グループを計画する
お客様のアプリからのテレメトリは、Application Insights 型の Azure リソースで格納、分析、表示されます。 

各リソースはリソース グループに属しています。 リソース グループは、コストの管理、チーム メンバーへのアクセスの許可、調整された単一のトランザクションでの更新のデプロイに使用されます。 たとえば、1 回の操作で Azure クラウド サービスとその Application Insights 監視リソースを[デプロイするスクリプトを記述](../../azure-resource-manager/resource-group-template-deploy.md)できます。

### <a name="resources-for-components"></a>コンポーネント用のリソース
お客様のアプリのコンポーネントごとに別個のリソースを作成することをお勧めします。 つまり、web ロールと worker ロールにそれぞれリソースを作成します。 各コンポーネントを別個に分析できますが、すべてのコンポーネントから主要なグラフをまとめる[ダッシュボード](../../azure-monitor/app/overview-dashboard.md)を作成して、1 つのビューでまとめて比較および監視することもできます。 

別の方法として、複数のロールから同じリソースにテレメトリを送信することもできますが、ソース ロールを識別する[各テレメトリ項目にディメンション プロパティを追加](../../azure-monitor/app/api-filtering-sampling.md#add-properties-itelemetryinitializer)します。 この方法では、例外などのメトリック グラフに通常はさまざまなロールからのカウントの集計が表示されます。しかし、必要な場合にはロール識別子を使用してグラフを分割することができます。 同じディメンションによって検索をフィルターすることもできます。 この代替方法ではすべてを同時に表示するのが若干簡単になりますが、ロール間で混乱を招く可能性もあります。

通常、ブラウザーのテレメトリはサーバー側の Web ロールと同じリソースに含まれています。

さまざまなコンポーネント用の Application Insights リソースを 1 つのリソース グループに配置します。 この方法により、まとめて管理することが簡単になります。 

### <a name="separate-development-test-and-production"></a>開発、テスト、運用の分離
前のバージョンがライブである間に次の機能用のカスタム イベントを開発する場合、開発に関するテレメトリを別の Application Insights リソースに送信する必要があります。 そうしないと、ライブ サイトから送信されるすべてのトラフィックの中からお客様のテスト テレメトリを見つけることが難しくなります。

この状況を回避するには、お客様のシステムの各ビルド構成または "スタンプ" (開発、テスト、運用など) 用に別個のリソースを作成します。 各ビルド構成用のリソースを別個のリソース グループに配置します。 

テレメトリを適切なリソースに送信するには、ビルド構成に応じて異なるインストルメンテーション キーを選択するように Application Insights SDK を設定できます。 

## <a name="create-an-application-insights-resource-for-each-role"></a>役割ごとに Application Insights リソースを作成する

各ロール用に別個のリソースを作成する (場合によっては各ビルド構成用に別個のセットも作成する) ことにした場合、Application Insights ポータルでそれらすべてを作成するのが最も簡単です。 リソースを多数作成する場合は、[プロセスを自動化](../../azure-monitor/app/powershell.md)できます。

1. [Azure portal][portal] で、 **[新規作成]**  >  **[開発者サービス]**  >  **[Application Insights]** の順に選択します。  

    ![[Application Insights] ウィンドウ](./media/cloudservices/01-new.png)

1. **[アプリケーションの種類]** ドロップダウン リストで **[ASP.NET Web アプリケーション]** を選択します。  
    各リソースは、インストルメンテーション キーによって識別されます。 このキーは、SDK を手動で構成したい場合や SDK の構成を確認したい場合に後で必要になる可能性があります。


## <a name="set-up-azure-diagnostics-for-each-role"></a>Set up Azure Diagnostics for each role (各ロール用の Azure 診断を設定する)
このオプションは、Application Insights でアプリを監視する場合に設定します。 Web ロールの場合は、このオプションによってパフォーマンスの監視、アラート、診断、使用状況の分析が提供されます。 その他のロールの場合は、再起動、パフォーマンス カウンター、System.Diagnostics.Trace への呼び出しなど、Azure 診断を検索して監視できます。 

1. Visual Studio ソリューション エクスプローラーの **[\<YourCloudService>]**  >  **[ロール]** で、各ロールのプロパティを開きます。

1. **[構成]** で **[診断データを Application Insights に送信する]** チェック ボックスをオンにしてから、お客様が先ほど作成した Application Insights リソースを選択します。

各ビルド構成に対して別個の Application Insights リソースを使用することにした場合は、まず構成を選択します。

![Application Insights の構成](./media/cloudservices/configure-azure-diagnostics.png)

これにより、お客様の Application Insights インストルメンテーション キーが *ServiceConfiguration.\*.cscfg* という名前のファイルに挿入されます。 こちらに[サンプル コード](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/Samples/AzureEmailService/AzureEmailService/ServiceConfiguration.Cloud.cscfg)があります。

Application Insights に送信される診断情報のレベルを変更したい場合、[ *.cscfg* ファイルを直接編集することで](../../azure-monitor/platform/diagnostics-extension-to-application-insights.md)実行できます。

## <a name="sdk"></a>各プロジェクトに SDK をインストールする
このオプションでは、カスタム ビジネス テレメトリを任意のロールに追加できます。 お客様のアプリの使用状況とパフォーマンスについてより詳しい分析が得られます。

Visual Studio で、Application Insights SDK を各クラウド アプリ プロジェクト用に構成します。

1. **Web ロール**を構成するには、プロジェクトを右クリックし、 **[Application Insights の構成]** を選択するか、または **[追加]、[Application Insights のテレメトリ]** の順に選択します。

1. **worker ロール**を構成するには: 

    a. プロジェクトを右クリックし、 **[NuGet パッケージの管理]** を選択します。

    b. [Windows サーバー用の Application Insights](https://www.nuget.org/packages/Microsoft.ApplicationInsights.WindowsServer/) を追加します。

    ![Search for "Application Insights"](./media/cloudservices/04-ai-nuget.png)

1. データを Application Insights リソースに送信するように SDK を構成するには:

    a. 適切なスタートアップ関数で、 *.cscfg* ファイルに含まれている構成設定のインストルメンテーション キーを設定します。
 
    ```csharp
   
     TelemetryConfiguration.Active.InstrumentationKey = RoleEnvironment.GetConfigurationSettingValue("APPINSIGHTS_INSTRUMENTATIONKEY");
    ```
   
    b. お客様のアプリの各ロールについて "手順 a." を繰り返します。 次の例を参照してください。
   
    * [Web ロール](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/Samples/AzureEmailService/MvcWebRole/Global.asax.cs#L27)
    * [worker ロール](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/Samples/AzureEmailService/WorkerRoleA/WorkerRoleA.cs#L232)
    * [Web ページの場合](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/Samples/AzureEmailService/MvcWebRole/Views/Shared/_Layout.cshtml#L13) 

1. *ApplicationInsights.config* ファイルが常に出力ディレクトリにコピーされるように設定します。  
    *.config* ファイルのメッセージによって、インストルメンテーション キーをそこに配置するよう求められます。 ただし、クラウド アプリでは、それは *.cscfg* ファイルから設定することをお勧めします。 この方法により、ポータルでロールが正しく識別されます。

## <a name="set-up-status-monitor-to-collect-full-sql-queries-optional"></a>完全な SQL クエリを収集するように Status Monitor を設定する (省略可能)

この手順は、.NET Framework で完全な SQL クエリをキャプチャする場合にのみ必要です。 

1. `\*.csdef` ファイルに、次のように各ロールの[スタートアップ タスク](https://docs.microsoft.com/azure/cloud-services/cloud-services-startup-tasks)を追加します 

    ```xml
    <Startup>
      <Task commandLine="AppInsightsAgent\InstallAgent.bat" executionContext="elevated" taskType="simple">
        <Environment>
          <Variable name="ApplicationInsightsAgent.DownloadLink" value="http://go.microsoft.com/fwlink/?LinkID=522371" />
          <Variable name="RoleEnvironment.IsEmulated">
            <RoleInstanceValue xpath="/RoleEnvironment/Deployment/@emulated" />
          </Variable>
        </Environment>
      </Task>
    </Startup>
    ```
    
2. [InstallAgent.bat](https://github.com/microsoft/ApplicationInsights-Home/blob/master/Samples/AzureEmailService/WorkerRoleA/AppInsightsAgent/InstallAgent.bat) と [InstallAgent.ps1](https://github.com/microsoft/ApplicationInsights-Home/blob/master/Samples/AzureEmailService/WorkerRoleA/AppInsightsAgent/InstallAgent.ps1) をダウンロードし、各ロール プロジェクトの `AppInsightsAgent` フォルダーに配置します。 必ず Visual Studio のファイルのプロパティまたはビルド スクリプトを使用して、それらを出力ディレクトリにコピーします。

3. すべての worker ロールに環境変数を追加します。 

    ```xml
      <Environment>
        <Variable name="COR_ENABLE_PROFILING" value="1" />
        <Variable name="COR_PROFILER" value="{324F817A-7420-4E6D-B3C1-143FBED6D855}" />
        <Variable name="MicrosoftInstrumentationEngine_Host" value="{CA487940-57D2-10BF-11B2-A3AD5A13CBC0}" />
      </Environment>
    ```
    
## <a name="run-and-publish-the-app"></a>アプリを実行して発行する

1. お客様のアプリを実行し、Azure にサインインします。 

1. お客様が作成した Application Insights リソースを開きます。  
    個別のデータ ポイントは [[検索]](../../azure-monitor/app/diagnostic-search.md) に表示され、集計されたデータは [[メトリック エクスプローラー]](../../azure-monitor/app/metrics-explorer.md) に表示されます。 

1. さらにテレメトリを追加し (以下のセクションを参照)、お客様のアプリを発行して、ライブの診断と使用状況のフィードバックを取得します。 

データがない場合、以下の手順を実行します。
1. 個別のイベントを表示するには、[[検索]][diagnostic] タイルを開きます。
1. アプリで、テレメトリがいくつか生成されるようにさまざまなページを開きます。
1. 数秒待ってから **[最新の情報に更新]** をクリックします。  
    詳細については、「 [トラブルシューティング][qna]」を参照してください。

## <a name="view-azure-diagnostics-events"></a>Azure 診断イベントを表示する
Application Insights の [Azure 診断](https://docs.microsoft.com/azure/monitoring-and-diagnostics/azure-diagnostics)情報は、以下の場所にあります。

* パフォーマンス カウンターは、カスタム メトリックとして表示されます。 
* Windows イベント ログは、トレースとカスタム イベントとして表示されます。
* アプリケーション ログ、ETW ログ、診断インフラストラクチャ ログは、トレースとして表示されます。

パフォーマンス カウンターおよびイベント数を表示するには、[[メトリックス エクスプローラー]](../../azure-monitor/app/metrics-explorer.md) を開き、次のグラフを追加します。

![Azure 診断データ](./media/cloudservices/23-wad.png)

Azure 診断によって送信されるさまざまなトレース ログ全体を検索するには、[[検索]](../../azure-monitor/app/diagnostic-search.md) または [Analytics クエリ](../../azure-monitor/log-query/get-started-portal.md)を使用します。 たとえば、ハンドルされない例外が発生し、それによってロールがクラッシュしてリサイクルされたとします。 その情報は、Windows イベント ログのアプリケーション チャンネルに表示されます。 [検索] を使用して Windows イベント ログのエラーを表示し、例外の完全なスタック トレースを取得できます。 これは、問題の根本原因の発見に役立ちます。

![Azure 診断の検索](./media/cloudservices/25-wad.png)

## <a name="more-telemetry"></a>テレメトリの追加
以下のセクションでは、お客様のアプリのさまざまな側面に関して追加のテレメトリを取得する方法を説明します。

## <a name="track-requests-from-worker-roles"></a>worker ロールからの要求を追跡する
Web ロールでは、HTTP 要求に関するデータが要求モジュールによって自動的に収集されます。 既定の収集動作をオーバーライドする方法の例については、[サンプルの MVCWebRole](https://github.com/Microsoft/ApplicationInsights-Home/tree/master/Samples/AzureEmailService/MvcWebRole) を参照してください。 

HTTP 要求の場合と同じ方法で追跡することで、worker ロールの呼び出しのパフォーマンスを記録できます。 Application Insights では、"要求" 型のテレメトリは、サーバー側で名前を付け、時間を指定し、個別に成功と失敗を判定できる作業単位を測定できます。 SDK によって HTTP 要求が自動的にキャプチャされますが、独自のコードを挿入して worker ロールへの要求を追跡できます。

要求を報告するためにインストルメント化された 2 つのサンプル worker ロール 
* [WorkerRoleA](https://github.com/Microsoft/ApplicationInsights-Home/tree/master/Samples/AzureEmailService/WorkerRoleA)
* [WorkerRoleB](https://github.com/Microsoft/ApplicationInsights-Home/tree/master/Samples/AzureEmailService/WorkerRoleB)

## <a name="exceptions"></a>例外
ハンドルされない例外を各種の Web アプリから収集する方法の詳細については、[Application Insights での例外の監視](../../azure-monitor/app/asp-net-exceptions.md)に関するページを参照してください。

サンプルの web ロールには MVC5 コントローラーおよび Web API 2 コントローラーが含まれています。 この 2 つからのハンドルされない例外は、以下のハンドラーを使用してキャプチャされます。

* MVC5 コントローラー向けの [AiHandleErrorAttribute](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/Samples/AzureEmailService/MvcWebRole/Telemetry/AiHandleErrorAttribute.cs) 設定 ([こちらの例を参照](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/Samples/AzureEmailService/MvcWebRole/App_Start/FilterConfig.cs#L12)) 
* Web API 2 コントローラー向けの [AiWebApiExceptionLogger](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/Samples/AzureEmailService/MvcWebRole/Telemetry/AiWebApiExceptionLogger.cs) 設定 ([こちらの例を参照](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/Samples/AzureEmailService/MvcWebRole/App_Start/WebApiConfig.cs#L25) ) 

worker ロールの場合、2 つの方法で例外を追跡できます。

* TrackException(ex) を使用します。
* Application Insights トレース リスナー NuGet パッケージを追加した場合、[こちらの例に示すように](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/Samples/AzureEmailService/WorkerRoleA/WorkerRoleA.cs#L107)、System.Diagnostics.Trace を使用して例外を記録できます。

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

追加のカスタム パフォーマンス カウンターやその他の Windows パフォーマンス カウンターは、[こちらの例に示すように](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/Samples/AzureEmailService/WorkerRoleA/ApplicationInsights.config#L14)、*ApplicationInsights.config* を編集することで指定できます。

  ![パフォーマンス カウンター](./media/cloudservices/002-servers.png)

## <a name="correlated-telemetry-for-worker-roles"></a>worker ロールの関連付けられたテレメトリ
リッチな診断エクスペリエンスでは、失敗した要求や待ち時間の長い要求の原因を確認できます。 Web ロールを使用すると、関連するテレメトリ間の関連付けが SDK によって自動的に設定されます。 

worker ロールでこのビューを実現するには、カスタムのテレメトリ初期化子を使用して、共通の Operation.Id context 属性をすべてのテレメトリに設定します。 これを行うと、待ち時間または失敗の問題の原因が依存関係とコードのどちらにあったかを一目で把握することができます。 

その方法は次のとおりです。

* [こちらの例に示すように](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/Samples/AzureEmailService/WorkerRoleA/WorkerRoleA.cs#L36)、correlationId を CallContext に設定します。 このケースでは、要求 ID を correlationId として使用しています。
* カスタムの TelemetryInitializer 実装を追加して、先ほど設定された correlationId に Operation.Id を設定します。 例については、[ItemCorrelationTelemetryInitializer](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/Samples/AzureEmailService/WorkerRoleA/Telemetry/ItemCorrelationTelemetryInitializer.cs#L13) を参照してください。
* カスタムのテレメトリ初期化子を追加します。 これは、*ApplicationInsights.config* ファイル内で実行できます。または、[こちらの例に示すように](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/Samples/AzureEmailService/WorkerRoleA/WorkerRoleA.cs#L233)コードで実行できます。

## <a name="client-telemetry"></a>クライアント テレメトリ
ページ ビュー数、ページの読み込み時間、スクリプトの例外など、ブラウザーベースのテレメトリを取得し、お客様のページ スクリプトにカスタム テレメトリを記述するには、[Web ページへの JavaScript SDK の追加][client]に関するページを参照してください。

## <a name="availability-tests"></a>可用性テスト
お客様のアプリが動作していて応答することを確認するには、[Web テストを設定します][availability]。

## <a name="display-everything-together"></a>すべてをまとめて表示する
お客様のシステムの全体像を把握したい場合、主要な監視グラフを 1 つの[ダッシュ ボード](../../azure-monitor/app/overview-dashboard.md)にまとめて表示できます。 たとえば、各ロールの要求と失敗のカウントをピン留めできます。 

お客様のシステムで他の Azure サービス (Stream Analytics など) が使用されている場合は、それらの監視グラフも含めます。 

クライアントのモバイル アプリがある場合は、[App Center](../../azure-monitor/learn/mobile-center-quickstart.md) を使用してください。 [Analytics](../../azure-monitor/app/analytics.md) でクエリを作成してイベント カウントを表示してから、それをダッシュボードにピン留めします。

## <a name="example"></a>例
[この例](https://github.com/Microsoft/ApplicationInsights-Home/tree/master/Samples/AzureEmailService) では、1 つの Web ロールと 2 つの worker ロールが含まれたサービスを監視します。

## <a name="exception-method-not-found-on-running-in-azure-cloud-services"></a>Azure クラウド サービスにおける実行での例外 "メソッドが見つかりません"
.NET 4.6 でビルドした場合 .NET 4.6 は、Azure クラウド サービスのロールでは自動的にサポートされていません。 お客様のアプリを実行する前に、[.NET 4.6 を各ロールにインストールしてください](../../cloud-services/cloud-services-dotnet-install-dotnet.md)。

## <a name="video"></a>ビデオ

> [!VIDEO https://channel9.msdn.com/events/Connect/2016/100/player]

## <a name="next-steps"></a>次の手順
* [Application Insights に Azure Diagnostics を送信するための構成](../../azure-monitor/platform/diagnostics-extension-to-application-insights.md)
* [Application Insights リソースの自動的な作成](../../azure-monitor/app/powershell.md)
* [Azure 診断の自動化](../../azure-monitor/app/powershell-azure-diagnostics.md)
* [Azure Functions](https://github.com/christopheranderson/azure-functions-app-insights-sample)

[api]: ../../azure-monitor/app/api-custom-events-metrics.md
[availability]: ../../azure-monitor/app/monitor-web-app-availability.md
[azure]: ../../azure-monitor/app/app-insights-overview.md
[client]: ../../azure-monitor/app/javascript.md
[diagnostic]: ../../azure-monitor/app/diagnostic-search.md
[netlogs]: ../../azure-monitor/app/asp-net-trace-logs.md
[portal]: https://portal.azure.com/
[qna]: ../../azure-monitor/app/troubleshoot-faq.md
[redfield]: ../../azure-monitor/app/monitor-performance-live-website-now.md
[start]: ../../azure-monitor/app/app-insights-overview.md 
