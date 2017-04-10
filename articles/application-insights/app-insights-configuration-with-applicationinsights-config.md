---
title: "ApplicationInsights.config リファレンス - Azure | Microsoft Docs"
description: "データ コレクション モジュールを有効または無効にし、パフォーマンス カウンターとその他のパラメーターを追加します。"
services: application-insights
documentationcenter: 
author: OlegAnaniev-MSFT
editor: alancameronwills
manager: douge
ms.assetid: 6e397752-c086-46e9-8648-a1196e8078c2
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 03/12/2016
ms.author: awills
translationtype: Human Translation
ms.sourcegitcommit: 9a3df0ad2483471023ebb954d613bc5cad8fb7bf
ms.openlocfilehash: a43eca9878881731f54dc1ec3bc8a9cd15bf2c5e
ms.lasthandoff: 02/02/2017


---
# <a name="configuring-the-application-insights-sdk-with-applicationinsightsconfig-or-xml"></a>ApplicationInsights.config または .xml を使った Application Insights SDK の構成
Application Insights .NET SDK は、いくつかの NuGet パッケージで構成されます。 [コア パッケージ](http://www.nuget.org/packages/Microsoft.ApplicationInsights) は、テレメトリを Application Insights に送信するための API を提供します。 [その他のパッケージ](http://www.nuget.org/packages?q=Microsoft.ApplicationInsights)は、アプリケーションとそのコンテキストからテレメトリを自動的に追跡するためのテレメトリ *モジュール*と*初期化子*を提供します。 構成ファイルを調整することによって、テレメトリ モジュールと初期化子を有効または無効にしたり、その中のいくつかのモジュールのパラメーターを設定したりできます。

アプリケーションの種類に応じて、構成ファイルの名前は `ApplicationInsights.config` または `ApplicationInsights.xml` になります。 構成ファイルは、[SDK のほとんどのバージョンのインストール][start]時にプロジェクトに自動的に追加されます。 また、[IIS サーバー上の Status Monitor][redfield] によって Web アプリにも追加されます。Web アプリには、[Azure Web サイトまたは VM の Appplication Insights 拡張機能](app-insights-azure-web-apps.md)を選択したときにも追加されます。

[Web ページの SDK][client] を制御するための同等のファイルはありません。

このドキュメントでは、構成ファイルの各セクション、SDK のコンポーネントの制御方法、それらのコンポーネントを読み込む NuGet パッケージについて説明します。

## <a name="telemetry-modules-aspnet"></a>テレメトリ モジュール (ASP.NET)
各テレメトリ モジュールは特定の種類のデータを回収し、コア API を利用してデータを送信します。 モジュールはさまざまな NuGet パッケージによりインストールされます。NuGet パッケージはまた、必要な行を .config ファイルに追加します。

各モジュールの構成ファイルにノードが存在します。 モジュールを無効にするには、ノードを削除するか、コメント アウトします。

### <a name="dependency-tracking"></a>依存関係の追跡
[依存関係の追跡](app-insights-asp-net-dependencies.md) により、アプリがデータベースと外部サービスに行った呼び出しに関するテレメトリが回収されます。 このモジュールを IIS サーバーで機能させるには、[Status Monitor をインストール][redfield]する必要があります。 これを Azure Web アプリまたは VM で使用するには、 [Application Insights 拡張を選択します](app-insights-azure-web-apps.md)。

[TrackDependency API](app-insights-api-custom-events-metrics.md#trackdependency)を使用して、独自の依存関係追跡コードを記述することもできます。

* `Microsoft.ApplicationInsights.DependencyCollector.DependencyTrackingTelemetryModule`
* [Microsoft.ApplicationInsights.DependencyCollector](http://www.nuget.org/packages/Microsoft.ApplicationInsights.DependencyCollector) NuGet パッケージ

### <a name="performance-collector"></a>パフォーマンス コレクター
CPU、メモリ、IIS インストールのネットワーク負荷など、[システム パフォーマンス カウンターを収集](app-insights-performance-counters.md)します。 自分で設定したパフォーマンス カウンターなど、回収するカウンターを指定できます。

* `Microsoft.ApplicationInsights.Extensibility.PerfCounterCollector.PerformanceCollectorModule`
* [Microsoft.ApplicationInsights.PerfCounterCollector](http://www.nuget.org/packages/Microsoft.ApplicationInsights.PerfCounterCollector) NuGet パッケージ

### <a name="application-insights-diagnostics-telemetry"></a>Application Insights 診断テレメトリ
`DiagnosticsTelemetryModule` は Application Insights インストルメンテーション コード自体のエラーを報告します。 たとえば、コードのパフォーマンス カウンターにアクセスできない場合、または `ITelemetryInitializer` が例外をスローする場合です。 [診断検索][diagnostic]に、このモジュールが追跡するトレース テレメトリが表示されます。 dc.services.vsallin.net に診断データが送信されます。

* `Microsoft.ApplicationInsights.Extensibility.Implementation.Tracing.DiagnosticsTelemetryModule`
* [Microsoft.ApplicationInsights](http://www.nuget.org/packages/Microsoft.ApplicationInsights) NuGet パッケージ このパッケージのみをインストールする場合、ApplicationInsights.config ファイルは自動作成されません。

### <a name="developer-mode"></a>開発者モード
`DeveloperModeWithDebuggerAttachedTelemetryModule` は、デバッガーがアプリケーション プロセスに接続されているときに、Application Insights の `TelemetryChannel` にデータを即座に、一度に&1; つのテレメトリ項目を送信するよう強制します。 これにより、アプリケーションによるテレメトリの追跡時や、アプリケーションが Application Insights ポータルに表示されるときの時間間隔が削減されます。 ここでは、CPU とネットワーク帯域幅のオーバーヘッドが著しく費やされます。

* `Microsoft.ApplicationInsights.WindowsServer.DeveloperModeWithDebuggerAttachedTelemetryModule`
* [Application Insights Windows Server](http://www.nuget.org/packages/Microsoft.ApplicationInsights.WindowsServer/) NuGet パッケージ

### <a name="web-request-tracking"></a>Web 要求の追跡
HTTP 要求の [応答時間と結果コード](app-insights-asp-net.md) を報告します。

* `Microsoft.ApplicationInsights.Web.RequestTrackingTelemetryModule`
* [Microsoft.ApplicationInsights.Web](http://www.nuget.org/packages/Microsoft.ApplicationInsights.Web) NuGet パッケージ

### <a name="exception-tracking"></a>例外の追跡
`ExceptionTrackingTelemetryModule` は Web アプリで処理されない例外を追跡します。 [失敗と例外][exceptions]に関する記事をご覧ください。

* `Microsoft.ApplicationInsights.Web.ExceptionTrackingTelemetryModule`
* [Microsoft.ApplicationInsights.Web](http://www.nuget.org/packages/Microsoft.ApplicationInsights.Web) NuGet パッケージ
* `Microsoft.ApplicationInsights.WindowsServer.UnobservedExceptionTelemetryModule` - [監視されていないタスクの例外](http://blogs.msdn.com/b/pfxteam/archive/2011/09/28/task-exception-handling-in-net-4-5.aspx)を追跡します。
* `Microsoft.ApplicationInsights.WindowsServer.UnhandledExceptionTelemetryModule` - worker ロール、Windows サービス、コンソール アプリケーションの未処理例外を追跡します。
* [Application Insights Windows Server](http://www.nuget.org/packages/Microsoft.ApplicationInsights.WindowsServer/) NuGet パッケージ

### <a name="microsoftapplicationinsights"></a>Microsoft.ApplicationInsights
Microsoft.ApplicationInsights パッケージには、SDK の[コア API](https://msdn.microsoft.com/library/mt420197.aspx) が含まれています。 コア API は、他のテレメトリ モジュールで使用されます。[これを使用して独自のテレメトリを定義](app-insights-api-custom-events-metrics.md)することもできます。

* ApplicationInsights.config にエントリがありません。
* [Microsoft.ApplicationInsights](http://www.nuget.org/packages/Microsoft.ApplicationInsights) NuGet パッケージ この NuGet だけをインストールする場合、.config ファイルは生成されません。

## <a name="telemetry-channel"></a>テレメトリ チャネル
テレメトリ チャネルにより、テレメトリのバッファリングと Application Insights サービスへの送信が管理されます。

* `Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel.ServerTelemetryChannel` がサービスの既定のチャネルです。 メモリにデータをバッファーします。
* `Microsoft.ApplicationInsights.PersistenceChannel` はコンソール アプリケーションの代替です。 アプリが停止したときにフラッシュされていないデータを永続ストレージに保存できます。また、アプリが再開したときにそのデータを送信します。

## <a name="telemetry-initializers-aspnet"></a>テレメトリの初期化子 (ASP.NET)
テレメトリの初期化子は、テレメトリのあらゆるアイテムと共に送信されるコンテキスト プロパティを設定します。

[独自の初期化子を記述し](app-insights-api-filtering-sampling.md#add-properties) 、コンテキスト プロパティを設定できます。

標準の初期化子は Web または WindowsServer NuGet パッケージによりすべて設定されます。

* `AccountIdTelemetryInitializer` は AccountId プロパティを設定します。
* `AuthenticatedUserIdTelemetryInitializer` は JavaScript SDK による設定に基づき AuthenticatedUserId プロパティを設定します。
* `AzureRoleEnvironmentTelemetryInitializer` は、Azure ランタイム環境から抽出された情報により、すべてのテレメトリ項目の `Device` コンテキストの `RoleName` および `RoleInstance` プロパティを更新します。
* `BuildInfoConfigComponentVersionTelemetryInitializer` は、MS ビルドによって生成された `BuildInfo.config` ファイルから抽出された値を使用して、すべてのテレメトリ項目の `Component` コンテキストの `Version` プロパティを更新します。
* `ClientIpHeaderTelemetryInitializer` は、要求の `X-Forwarded-For` HTTP ヘッダーに基づいて、すべてのテレメトリ項目の `Location` コンテキストの `Ip` プロパティを更新します。
* `DeviceTelemetryInitializer` は、すべてのテレメトリ項目の `Device` コンテキストの次のプロパティを更新します。
  * `Type` は "PC" に設定します
  * `Id` は Web アプリケーションが実行中のコンピューターのドメイン名に設定します。
  * `OemName` は WMI を使用して `Win32_ComputerSystem.Manufacturer` フィールドから抽出された値に設定します。
  * `Model` は WMI を使用して `Win32_ComputerSystem.Model` フィールドから抽出された値に設定します。
  * `NetworkType` は `NetworkInterface` から抽出された値に設定します。
  * `Language` は `CurrentCulture` の名前に設定します。
* `DomainNameRoleInstanceTelemetryInitializer` は、Web アプリケーションを実行中のコンピューターのドメイン名を使用して、すべてのテレメトリ項目の `Device` コンテキストの `RoleInstance` プロパティを更新します。
* `OperationNameTelemetryInitializer` は、HTTP メソッドのほか、ASP.NET MVC コントローラーの名前、要求の処理のために呼び出されるアクションに基づいて、すべてのテレメトリ項目の`RequestTelemetry` の `Name` プロパティと `Operation` コンテキストの `Name` プロパティを更新します。
* `OperationIdTelemetryInitializer` または `OperationCorrelationTelemetryInitializer` は、追跡されたすべてのテレメトリ項目の `Operation.Id` コンテキスト プロパティを更新し、自動生成された `RequestTelemetry.Id` が付いた要求を処理します。
* `SessionTelemetryInitializer` は、ユーザーのブラウザーで実行する Application Insights JavaScript インストルメンテーション コードが生成する `ai_session` Cookie から抽出された値を使用して、すべてのテレメトリ項目の `Session` コンテキストの `Id` プロパティを更新します。
* `SyntheticTelemetryInitializer` または `SyntheticUserAgentTelemetryInitializer` は、可用性テストや検索エンジン ボットなど、合成ソースからの要求の処理時に追跡されるすべてのテレメトリ項目の `User`、`Session`、および `Operation` コンテキスト プロパティを更新します。 既定では、 [メトリックス エクスプローラー](app-insights-metrics-explorer.md) には合成テレメトリは表示されません。

    `<Filters>` は、要求の識別プロパティを設定します。
* `UserAgentTelemetryInitializer` は、要求の `User-Agent` HTTP ヘッダーに基づいて、すべてのテレメトリ項目の `User` コンテキストの `UserAgent` プロパティを更新します。
* `UserTelemetryInitializer` は、ユーザーのブラウザーで実行する Application Insights JavaScript インストルメンテーション コードが生成する `ai_user` Cookie から抽出された値を使用して、すべてのテレメトリ項目の `User` コンテキストの `Id` および `AcquisitionDate` プロパティを更新します。
* `WebTestTelemetryInitializer` は、 [可用性テスト](app-insights-monitor-web-app-availability.md)からの HTTP 要求のユーザー ID、セッション ID および合成ソース プロパティを設定します。
  `<Filters>` は、要求の識別プロパティを設定します。

## <a name="telemetry-processors-aspnet"></a>テレメトリ プロセッサ (ASP.NET)
テレメトリ プロセッサは、SDK からポータルに送信される直前の各テレメトリ アイテムをフィルター処理し、変更できます。

[独自のテレメトリ プロセッサを記述](app-insights-api-filtering-sampling.md#filtering)できます。

#### <a name="adaptive-sampling-telemetry-processor-from-200-beta3"></a>アダプティブ サンプリング テレメトリ プロセッサー (2.0.0-beta3 以降)
この機能は、既定では有効になっています。 アプリが多数のテレメトリを送信する場合、このプロセッサはその一部を削除します。

```xml

    <TelemetryProcessors>
      <Add Type="Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel.AdaptiveSamplingTelemetryProcessor, Microsoft.AI.ServerTelemetryChannel">
        <MaxTelemetryItemsPerSecond>5</MaxTelemetryItemsPerSecond>
      </Add>
    </TelemetryProcessors>

```

パラメーターは、アルゴリズムが実現しようとするターゲットを指定します。 SDK の各インスタンスは独立して機能するため、サーバーが複数のコンピューターのクラスターである場合、テレメトリの実際の量もそれに応じて増加します。

[サンプリングの詳細についてはこちらを参照してください](app-insights-sampling.md)。

#### <a name="fixed-rate-sampling-telemetry-processor-from-200-beta1"></a>固定レート サンプリング テレメトリ プロセッサー (2.0.0-beta1 以降)
標準的な [サンプリング テレメトリ プロセッサ](app-insights-api-filtering-sampling.md) も用意されています (2.0.1 以降)。

```XML

    <TelemetryProcessors>
     <Add Type="Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel.SamplingTelemetryProcessor, Microsoft.AI.ServerTelemetryChannel">

     <!-- Set a percentage close to 100/N where N is an integer. -->
     <!-- E.g. 50 (=100/2), 33.33 (=100/3), 25 (=100/4), 20, 1 (=100/100), 0.1 (=100/1000) -->
     <SamplingPercentage>10</SamplingPercentage>
     </Add>
   </TelemetryProcessors>

```



## <a name="channel-parameters-java"></a>チャネル パラメーター (Java)
これらのパラメーターは、Java SDK が収集するテレメトリ データの格納とフラッシュする方法に影響します。

#### <a name="maxtelemetrybuffercapacity"></a>MaxTelemetryBufferCapacity
SDK のメモリー内ストレージに格納できるテレメトリ項目の数。 この数に達すると、テレメトリのバッファーがフラッシュされます。つまり、テレメトリの項目が、Application Insights サーバーに送信されます。

* 最小: 1
* 最大: 1000
* 既定値: 500

```

  <ApplicationInsights>
      ...
      <Channel>
       <MaxTelemetryBufferCapacity>100</MaxTelemetryBufferCapacity>
      </Channel>
      ...
  </ApplicationInsights>
```

#### <a name="flushintervalinseconds"></a>FlushIntervalInSeconds
メモリー内ストレージに格納されているデータがフラッシュされる (Application Insights に送信される) 頻度を決定します。

* 最小: 1
* 最大: 300
* 既定値: 5

```

    <ApplicationInsights>
      ...
      <Channel>
        <FlushIntervalInSeconds>100</FlushIntervalInSeconds>
      </Channel>
      ...
    </ApplicationInsights>
```

#### <a name="maxtransmissionstoragecapacityinmb"></a>MaxTransmissionStorageCapacityInMB
ローカル ディスクの永続的なストレージに割り当てられる MB 単位の最大サイズを決定します。 このストレージは、Application Insights のエンドポイントへの転送に失敗したテレメトリの項目を保存するために使用されます。 ストレージのサイズが満たされている場合は、テレメトリの新しい項目は破棄されます。

* 最小: 1
* 最大: 100
* 既定値: 10

```

   <ApplicationInsights>
      ...
      <Channel>
        <MaxTransmissionStorageCapacityInMB>50</MaxTransmissionStorageCapacityInMB>
      </Channel>
      ...
   </ApplicationInsights>
```



## <a name="instrumentationkey"></a>InstrumentationKey
これは、データが表示される Application Insights のリソースを決定します。 通常、アプリケーションごとに、個別のキーを持つリソースを作成します。

キーを動的に設定する場合 (たとえば、アプリケーションからの結果を別のリソースに送信する場合)、構成ファイルからキーを省略し、代わりにコードで設定することができます。

TelemetryClient のすべてのインスタンスのキーを設定するには (標準のテレメトリ モジュールを含む)、TelemetryConfiguration.Active でキーを設定します。 ASP.NET サービスの global.aspx.cs など、初期化メソッドでキーの設定を行います。

```C#

    protected void Application_Start()
    {
      Microsoft.ApplicationInsights.Extensibility.
        TelemetryConfiguration.Active.InstrumentationKey =
          // - for example -
          WebConfigurationManager.Settings["ikey"];
      //...
```

特定のイベント セットを異なるリソースに送信する場合、特定の TelemetryClient のキーを設定できます。

```C#

    var tc = new TelemetryClient();
    tc.Context.InstrumentationKey = "----- my key ----";
    tc.TrackEvent("myEvent");
    // ...

```

新しいキーを取得するには、[Application Insights ポータルで新しいリソースを作成][new]します。

## <a name="next-steps"></a>次のステップ
API の詳細については、[こちら][api]をご覧ください。

<!--Link references-->

[api]: app-insights-api-custom-events-metrics.md
[client]: app-insights-javascript.md
[diagnostic]: app-insights-diagnostic-search.md
[exceptions]: app-insights-asp-net-exceptions.md
[netlogs]: app-insights-asp-net-trace-logs.md
[new]: app-insights-create-new-resource.md
[redfield]: app-insights-monitor-performance-live-website-now.md
[start]: app-insights-overview.md

