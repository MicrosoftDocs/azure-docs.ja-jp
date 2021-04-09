---
title: ApplicationInsights.config リファレンス - Azure | Microsoft Docs
description: データ コレクション モジュールを有効または無効にし、パフォーマンス カウンターとその他のパラメーターを追加します。
ms.topic: conceptual
ms.date: 05/22/2019
ms.custom: devx-track-csharp
ms.reviewer: olegan
ms.openlocfilehash: c6d51210867e83c6acc74ca890ab65b195dc64fc
ms.sourcegitcommit: 24a12d4692c4a4c97f6e31a5fbda971695c4cd68
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/05/2021
ms.locfileid: "102176673"
---
# <a name="configuring-the-application-insights-sdk-with-applicationinsightsconfig-or-xml"></a>ApplicationInsights.config または .xml を使った Application Insights SDK の構成
Application Insights .NET SDK は、いくつかの NuGet パッケージで構成されます。 [コア パッケージ](https://www.nuget.org/packages/Microsoft.ApplicationInsights) は、テレメトリを Application Insights に送信するための API を提供します。 [その他のパッケージ](https://www.nuget.org/packages?q=Microsoft.ApplicationInsights)は、アプリケーションとそのコンテキストからテレメトリを自動的に追跡するためのテレメトリ *モジュール* と *初期化子* を提供します。 構成ファイルを調整することによって、テレメトリ モジュールと初期化子を有効または無効にしたり、その中のいくつかに対してパラメーターを設定したりできます。

アプリケーションの種類に応じて、構成ファイルの名前は `ApplicationInsights.config` または `ApplicationInsights.xml` になります。 構成ファイルは、[SDK のほとんどのバージョンのインストール][start]時にプロジェクトに自動的に追加されます。 既定では、 **[追加] > [Application Insights Telemetry]** がサポートされている Visual Studio テンプレート プロジェクトからの自動化されたエクスペリエンスを使用すると、ApplicationInsights.config ファイルがプロジェクトのルート フォルダーに作成され、コンパイル時に bin フォルダーにコピーされます。 また、[IIS サーバー上の Status Monitor][redfield] によって Web アプリに追加されます。 [Azure Web サイトの拡張機能](azure-web-apps.md)または [Azure VM の拡張機能と仮想マシン スケールセット](azure-vm-vmss-apps.md)が使用されている場合、構成ファイルは無視されます。

[Web ページの SDK][client] を制御するための同等のファイルはありません。

このドキュメントでは、構成ファイルの各セクション、SDK のコンポーネントの制御方法、それらのコンポーネントを読み込む NuGet パッケージについて説明します。

> [!NOTE]
> ApplicationInsights.config および .xml の手順は、.NET Core SDK には適用されません。 .NET Core アプリケーションを構成するには、[こちら](./asp-net-core.md)のガイドに従ってください。

## <a name="telemetry-modules-aspnet"></a>テレメトリ モジュール (ASP.NET)
各テレメトリ モジュールでは特定の種類のデータを収集し、コア API を利用してデータを送信します。 モジュールはさまざまな NuGet パッケージによりインストールされます。NuGet パッケージはまた、必要な行を .config ファイルに追加します。

各モジュールの構成ファイルにノードが存在します。 モジュールを無効にするには、ノードを削除するか、コメント アウトします。

### <a name="dependency-tracking"></a>依存関係の追跡
[依存関係の追跡](./asp-net-dependencies.md) により、アプリがデータベースと外部サービスに行った呼び出しに関するテレメトリが回収されます。 このモジュールを IIS サーバーで機能させるには、[Status Monitor をインストール][redfield]する必要があります。

[TrackDependency API](./api-custom-events-metrics.md#trackdependency)を使用して、独自の依存関係追跡コードを記述することもできます。

* `Microsoft.ApplicationInsights.DependencyCollector.DependencyTrackingTelemetryModule`
* [Microsoft.ApplicationInsights.DependencyCollector](https://www.nuget.org/packages/Microsoft.ApplicationInsights.DependencyCollector) NuGet パッケージ

エージェントベース (コードなし) アタッチを使用してコードを変更することなく、依存関係を自動収集できます。 これを Azure Web アプリで使用するには、[Application Insights 拡張機能](azure-web-apps.md)を有効にします。 Azure VM または Azure 仮想マシン スケール セットで使用するには、[VM と仮想マシン スケール セットのアプリケーション監視拡張](azure-vm-vmss-apps.md)を有効にします。

### <a name="performance-collector"></a>パフォーマンス コレクター
CPU、メモリ、IIS インストールのネットワーク負荷など、[システム パフォーマンス カウンターを収集](./performance-counters.md)します。 自分で設定したパフォーマンス カウンターなど、回収するカウンターを指定できます。

* `Microsoft.ApplicationInsights.Extensibility.PerfCounterCollector.PerformanceCollectorModule`
* [Microsoft.ApplicationInsights.PerfCounterCollector](https://www.nuget.org/packages/Microsoft.ApplicationInsights.PerfCounterCollector) NuGet パッケージ

### <a name="application-insights-diagnostics-telemetry"></a>Application Insights 診断テレメトリ
`DiagnosticsTelemetryModule` は Application Insights インストルメンテーション コード自体のエラーを報告します。 たとえば、コードのパフォーマンス カウンターにアクセスできない場合、または `ITelemetryInitializer` が例外をスローする場合です。 [診断検索][diagnostic]に、このモジュールが追跡するトレース テレメトリが表示されます。

```
* `Microsoft.ApplicationInsights.Extensibility.Implementation.Tracing.DiagnosticsTelemetryModule`
* [Microsoft.ApplicationInsights](https://www.nuget.org/packages/Microsoft.ApplicationInsights) NuGet package. If you only install this package, the ApplicationInsights.config file is not automatically created.
```

### <a name="developer-mode"></a>開発者モード
`DeveloperModeWithDebuggerAttachedTelemetryModule` は、デバッガーがアプリケーション プロセスに接続されているときに、Application Insights の `TelemetryChannel` にデータを即座に、一度に 1 つのテレメトリ項目を送信するよう強制します。 これにより、アプリケーションによるテレメトリの追跡時や、アプリケーションが Application Insights ポータルに表示されるときの時間間隔が削減されます。 ここでは、CPU とネットワーク帯域幅のオーバーヘッドが著しく費やされます。

* `Microsoft.ApplicationInsights.WindowsServer.DeveloperModeWithDebuggerAttachedTelemetryModule`
* [Application Insights Windows Server](https://www.nuget.org/packages/Microsoft.ApplicationInsights.WindowsServer/) NuGet パッケージ

### <a name="web-request-tracking"></a>Web 要求の追跡
HTTP 要求の [応答時間と結果コード](../../azure-monitor/app/asp-net.md) を報告します。

* `Microsoft.ApplicationInsights.Web.RequestTrackingTelemetryModule`
* [Microsoft.ApplicationInsights.Web](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Web) NuGet パッケージ

### <a name="exception-tracking"></a>例外の追跡
`ExceptionTrackingTelemetryModule` は Web アプリで処理されない例外を追跡します。 [失敗と例外][exceptions]に関する記事をご覧ください。

* `Microsoft.ApplicationInsights.Web.ExceptionTrackingTelemetryModule`
* [Microsoft.ApplicationInsights.Web](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Web) NuGet パッケージ
* `Microsoft.ApplicationInsights.WindowsServer.UnobservedExceptionTelemetryModule` - 監視されていないタスクの例外を追跡します
* `Microsoft.ApplicationInsights.WindowsServer.UnhandledExceptionTelemetryModule` - worker ロール、Windows サービス、コンソール アプリケーションの未処理例外を追跡します。
* [Application Insights Windows Server](https://www.nuget.org/packages/Microsoft.ApplicationInsights.WindowsServer/) NuGet パッケージ

### <a name="eventsource-tracking"></a>EventSource の追跡
`EventSourceTelemetryModule` を使用すると、Application Insights にトレースとして送信される EventSource イベントを構成できます。 EventSource イベントの追跡については、「[EventSource イベントを使用する](./asp-net-trace-logs.md#use-eventsource-events)」をご覧ください。

* `Microsoft.ApplicationInsights.EventSourceListener.EventSourceTelemetryModule`
* [Microsoft.ApplicationInsights.EventSourceListener](https://www.nuget.org/packages/Microsoft.ApplicationInsights.EventSourceListener) 

### <a name="etw-event-tracking"></a>ETW イベントの追跡
`EtwCollectorTelemetryModule` を使用すると、Application Insights にトレースとして送信される ETW プロバイダーを構成できます。 ETW イベントの追跡については、「[ETW イベントを使用する](../../azure-monitor/app/asp-net-trace-logs.md#use-etw-events)」をご覧ください。

* `Microsoft.ApplicationInsights.EtwCollector.EtwCollectorTelemetryModule`
* [Microsoft.ApplicationInsights.EtwCollector](https://www.nuget.org/packages/Microsoft.ApplicationInsights.EtwCollector) 

### <a name="microsoftapplicationinsights"></a>Microsoft.ApplicationInsights
Microsoft.ApplicationInsights パッケージには、SDK の[コア API](/dotnet/api/microsoft.applicationinsights) が含まれています。 その他のテレメトリ モジュールではこれが使用されると共に、[独自のテレメトリを定義するために使用する](./api-custom-events-metrics.md)ことも可能です。

* ApplicationInsights.config にエントリがありません。
* [Microsoft.ApplicationInsights](https://www.nuget.org/packages/Microsoft.ApplicationInsights) NuGet パッケージ この NuGet だけをインストールする場合、.config ファイルは生成されません。

## <a name="telemetry-channel"></a>テレメトリ チャネル
[テレメトリ チャネル](telemetry-channels.md)では、テレメトリのバッファリングと Application Insights サービスへの送信が管理されます。

* `Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel.ServerTelemetryChannel` は Web アプリケーション用の既定のチャネルです。 メモリ内にデータをバッファーし、より信頼性の高いテレメトリ配信のために、再試行のメカニズムとローカル ディスク ストレージを採用しています。
* `Microsoft.ApplicationInsights.InMemoryChannel` は軽量なテレメトリ チャネルであり、他のチャネルが構成されていない場合に使用されます。 

## <a name="telemetry-initializers-aspnet"></a>テレメトリの初期化子 (ASP.NET)
テレメトリの初期化子は、テレメトリのあらゆる項目と共に送信されるコンテキスト プロパティを設定します。

[独自の初期化子を記述し](./api-filtering-sampling.md#add-properties) 、コンテキスト プロパティを設定できます。

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
* `SyntheticTelemetryInitializer` または `SyntheticUserAgentTelemetryInitializer` は、可用性テストや検索エンジン ボットなど、合成ソースからの要求の処理時に追跡されるすべてのテレメトリ項目の `User`、`Session`、`Operation` コンテキスト プロパティを更新します。 既定では、 [メトリックス エクスプローラー](../essentials/metrics-charts.md) には合成テレメトリは表示されません。

    `<Filters>` は、要求の識別プロパティを設定します。
* `UserTelemetryInitializer` は、ユーザーのブラウザーで実行する Application Insights JavaScript インストルメンテーション コードが生成する `ai_user` Cookie から抽出された値を使用して、すべてのテレメトリ項目の `User` コンテキストの `Id` および `AcquisitionDate` プロパティを更新します。
* `WebTestTelemetryInitializer` は、[可用性テスト](./monitor-web-app-availability.md)からの HTTP 要求に対してユーザー ID、セッション ID、および合成ソース プロパティを設定します。
  `<Filters>` は、要求の識別プロパティを設定します。

Service Fabric で実行されている .NET アプリケーションの場合、`Microsoft.ApplicationInsights.ServiceFabric` NuGet パッケージを含めることができます。 このパッケージには、Service Fabric のプロパティをテレメトリ項目に追加する `FabricTelemetryInitializer` が含まれています。 詳細については、この NuGet パッケージによって追加されるプロパティに関する [GitHub のページ](https://github.com/Microsoft/ApplicationInsights-ServiceFabric/blob/master/README.md)をご覧ください。

## <a name="telemetry-processors-aspnet"></a>テレメトリ プロセッサ (ASP.NET)
テレメトリ プロセッサは、SDK からポータルに送信される直前の各テレメトリ項目をフィルター処理して変更できます。

[独自のテレメトリ プロセッサを記述](./api-filtering-sampling.md#filtering)できます。

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

[サンプリングの詳細についてはこちらを参照してください](./sampling.md)。

#### <a name="fixed-rate-sampling-telemetry-processor-from-200-beta1"></a>固定レート サンプリング テレメトリ プロセッサー (2.0.0-beta1 以降)
標準的な[サンプリング テレメトリ プロセッサ](./api-filtering-sampling.md)も用意されています (2.0.1 以降)。

```XML

    <TelemetryProcessors>
     <Add Type="Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel.SamplingTelemetryProcessor, Microsoft.AI.ServerTelemetryChannel">

     <!-- Set a percentage close to 100/N where N is an integer. -->
     <!-- E.g. 50 (=100/2), 33.33 (=100/3), 25 (=100/4), 20, 1 (=100/100), 0.1 (=100/1000) -->
     <SamplingPercentage>10</SamplingPercentage>
     </Add>
   </TelemetryProcessors>

```

## <a name="instrumentationkey"></a>InstrumentationKey
これは、データが表示される Application Insights のリソースを決定します。 通常、アプリケーションごとに、個別のキーを持つリソースを作成します。

キーを動的に設定する場合 (たとえば、アプリケーションからの結果を別のリソースに送信する場合)、構成ファイルからキーを省略し、代わりにコードで設定することができます。

標準のテレメトリ モジュールを含め、TelemetryClient のすべてのインスタンスにキーを設定するには、 ASP.NET サービスの global.aspx.cs など、初期化メソッドでキーの設定を行います。

```csharp
using Microsoft.ApplicationInsights.Extensibility;
using Microsoft.ApplicationInsights;

    protected void Application_Start()
    {
        TelemetryConfiguration configuration = TelemetryConfiguration.CreateDefault();
        configuration.InstrumentationKey = "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx";
        var telemetryClient = new TelemetryClient(configuration);
   
```

特定のイベント セットを異なるリソースに送信する場合、特定の TelemetryClient のキーを設定できます。

```csharp

    var tc = new TelemetryClient();
    tc.Context.InstrumentationKey = "----- my key ----";
    tc.TrackEvent("myEvent");
    // ...

```

新しいキーを取得するには、[Application Insights ポータルで新しいリソースを作成][new]します。



## <a name="applicationid-provider"></a>ApplicationId プロバイダー

_v2.6.0 以降で利用可能_

このプロバイダーの目的は、インストルメンテーション キーに基づいてアプリケーション ID を参照することです。 アプリケーション ID は RequestTelemetry と DependencyTelemetry に含まれ、ポータルでの相関関係を決定するために使用されます。

これは、`TelemetryConfiguration.ApplicationIdProvider` をコード内または構成内に設定することで有効にできます。

### <a name="interface-iapplicationidprovider"></a>インターフェイス:IApplicationIdProvider

```csharp
public interface IApplicationIdProvider
{
    bool TryGetApplicationId(string instrumentationKey, out string applicationId);
}
```


[Microsoft.ApplicationInsights](https://www.nuget.org/packages/Microsoft.ApplicationInsights) SDK 内に次の 2 つの実装が用意されています。`ApplicationInsightsApplicationIdProvider` と `DictionaryApplicationIdProvider`。

### <a name="applicationinsightsapplicationidprovider"></a>ApplicationInsightsApplicationIdProvider

これはプロファイル API のラッパーです。 要求とキャッシュの結果を調整します。

このプロバイダーは、[Microsoft.ApplicationInsights.DependencyCollector](https://www.nuget.org/packages/Microsoft.ApplicationInsights.DependencyCollector) または [Microsoft.ApplicationInsights.Web](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Web/) のいずれかをインストールしたときに、構成ファイルに追加されます。

このクラスには、省略可能なプロパティ `ProfileQueryEndpoint` があります。
既定では、これは `https://dc.services.visualstudio.com/api/profiles/{0}/appId` に設定されます。
この構成のプロキシを構成する必要がある場合は、ベース アドレスのプロキシ化と "/api/profiles/{0}/appId" を含めることをお勧めします。 '{0}' は実行時に要求ごとにインストルメンテーション キーに置き換わることに注意してください。

#### <a name="example-configuration-via-applicationinsightsconfig"></a>ApplicationInsights.config による構成の例:
```xml
<ApplicationInsights>
    ...
    <ApplicationIdProvider Type="Microsoft.ApplicationInsights.Extensibility.Implementation.ApplicationId.ApplicationInsightsApplicationIdProvider, Microsoft.ApplicationInsights">
        <ProfileQueryEndpoint>https://dc.services.visualstudio.com/api/profiles/{0}/appId</ProfileQueryEndpoint>
    </ApplicationIdProvider>
    ...
</ApplicationInsights>
```

#### <a name="example-configuration-via-code"></a>コードによる構成の例:
```csharp
TelemetryConfiguration.Active.ApplicationIdProvider = new ApplicationInsightsApplicationIdProvider();
```

### <a name="dictionaryapplicationidprovider"></a>DictionaryApplicationIdProvider

これは、構成済みのインストルメンテーション キーとアプリケーション ID のペアに依存する静的プロバイダーです。

このクラスには、`Defined` プロパティがあります。これは、インストルメンテーション キーとアプリケーション ID のペアである Dictionary<string,string> です。

このクラスには、省略可能なプロパティ `Next` があります。これを使用して、構成に存在しないインストルメンテーション キーが要求されたときに使用する別のプロバイダーを構成できます。

#### <a name="example-configuration-via-applicationinsightsconfig"></a>ApplicationInsights.config による構成の例:
```xml
<ApplicationInsights>
    ...
    <ApplicationIdProvider Type="Microsoft.ApplicationInsights.Extensibility.Implementation.ApplicationId.DictionaryApplicationIdProvider, Microsoft.ApplicationInsights">
        <Defined>
            <Type key="InstrumentationKey_1" value="ApplicationId_1"/>
            <Type key="InstrumentationKey_2" value="ApplicationId_2"/>
        </Defined>
        <Next Type="Microsoft.ApplicationInsights.Extensibility.Implementation.ApplicationId.ApplicationInsightsApplicationIdProvider, Microsoft.ApplicationInsights" />
    </ApplicationIdProvider>
    ...
</ApplicationInsights>
```

#### <a name="example-configuration-via-code"></a>コードによる構成の例:
```csharp
TelemetryConfiguration.Active.ApplicationIdProvider = new DictionaryApplicationIdProvider{
 Defined = new Dictionary<string, string>
    {
        {"InstrumentationKey_1", "ApplicationId_1"},
        {"InstrumentationKey_2", "ApplicationId_2"}
    }
};
```




## <a name="next-steps"></a>次のステップ
API の詳細については、[こちら][api]をご覧ください。

<!--Link references-->

[api]: ./api-custom-events-metrics.md
[client]: ./javascript.md
[diagnostic]: ./diagnostic-search.md
[exceptions]: ./asp-net-exceptions.md
[netlogs]: ./asp-net-trace-logs.md
[new]: ./create-new-resource.md
[redfield]: ./monitor-performance-live-website-now.md
[start]: ./app-insights-overview.md

