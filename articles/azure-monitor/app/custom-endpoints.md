---
title: Azure Application Insights による既定の SDK エンドポイントのオーバーライド
description: Azure Government などのリージョンの既定の Azure Monitor Application Insights SDK エンドポイントを変更します。
ms.topic: conceptual
ms.date: 07/26/2019
ms.custom: references_regions, devx-track-javascript
ms.openlocfilehash: 22f81b327c161df4f6605cfcb64b48b8d75beed3
ms.sourcegitcommit: f353fe5acd9698aa31631f38dd32790d889b4dbb
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/29/2020
ms.locfileid: "87373132"
---
# <a name="application-insights-overriding-default-endpoints"></a>既定のエンドポイントをオーバーライドする Application Insights

Application Insights から特定のリージョンにデータを送信するには、既定のエンドポイント アドレスをオーバーライドする必要があります。 SDK ごとに必要な変更点は若干異なります。そのすべてについてこの記事で説明します。 これらの変更には、サンプル コードを調整し、`QuickPulse_Endpoint_Address`、`TelemetryChannel_Endpoint_Address`、および `Profile_Query_Endpoint_address` のプレースホルダー値をお客様の特定のリージョンに対応する実際のエンドポイント アドレスに置き換える必要があります。 この記事の末尾には、この構成が必要なリージョンのエンドポイント アドレスのリンクを記載しています。

> [!NOTE]
> Application Insights 内にカスタム エンドポイントを設定する方法として、新たに[接続文字列](./sdk-connection-string.md?tabs=net)が推奨されています。

---

## <a name="sdk-code-changes"></a>SDK コードの変更

# <a name="net"></a>[.NET](#tab/net)

> [!NOTE]
> applicationinsights.config ファイルは、SDK のアップグレードが実行されるたびに自動的に上書きされます。 SDK のアップグレードを実行した後は、リージョン固有のエンドポイント値を必ず再入力します。

```xml
<ApplicationInsights>
  ...
  <TelemetryModules>
    <Add Type="Microsoft.ApplicationInsights.Extensibility.PerfCounterCollector.QuickPulse.QuickPulseTelemetryModule, Microsoft.AI.PerfCounterCollector">
      <QuickPulseServiceEndpoint>Custom_QuickPulse_Endpoint_Address</QuickPulseServiceEndpoint>
    </Add>
  </TelemetryModules>
    ...
  <TelemetryChannel>
     <EndpointAddress>TelemetryChannel_Endpoint_Address</EndpointAddress>
  </TelemetryChannel>
  ...
  <ApplicationIdProvider Type="Microsoft.ApplicationInsights.Extensibility.Implementation.ApplicationId.ApplicationInsightsApplicationIdProvider, Microsoft.ApplicationInsights">
    <ProfileQueryEndpoint>Profile_Query_Endpoint_address</ProfileQueryEndpoint>
  </ApplicationIdProvider>
  ...
</ApplicationInsights>
```

# <a name="net-core"></a>[.NET Core](#tab/netcore)

メイン エンドポイントを調整するには、プロジェクト内の appsettings.json ファイルを次のように変更します。

```json
"ApplicationInsights": {
    "InstrumentationKey": "instrumentationkey",
    "TelemetryChannel": {
      "EndpointAddress": "TelemetryChannel_Endpoint_Address"
    }
  }
```

Live Metrics と Profile Query Endpoint の値を設定するには、コードを使用する必要があります。 コードを使用してすべてのエンドポイント値の既定値をオーバーライドするには、`Startup.cs` ファイルの `ConfigureServices` メソッドを次のように変更します。

```csharp
using Microsoft.ApplicationInsights.Extensibility.Implementation.ApplicationId;
using Microsoft.ApplicationInsights.Extensibility.PerfCounterCollector.QuickPulse; //Place at top of Startup.cs file

   services.ConfigureTelemetryModule<QuickPulseTelemetryModule>((module, o) => module.QuickPulseServiceEndpoint="QuickPulse_Endpoint_Address");

   services.AddSingleton<IApplicationIdProvider, ApplicationInsightsApplicationIdProvider>(_ => new ApplicationInsightsApplicationIdProvider() { ProfileQueryEndpoint = "Profile_Query_Endpoint_address" });

   services.AddSingleton<ITelemetryChannel>(_ => new ServerTelemetryChannel() { EndpointAddress = "TelemetryChannel_Endpoint_Address" });

    //Place in the ConfigureServices method. Place this before services.AddApplicationInsightsTelemetry("instrumentation key"); if it's present
```

# <a name="azure-functions"></a>[Azure Functions](#tab/functions)

Azure Functions では、関数のアプリケーション設定で設定された[接続文字列](./sdk-connection-string.md?tabs=net)を使用することをお勧めします。 関数のアプリケーション設定にアクセスするには、関数ペインから **[設定]**  >  **[構成]**  >  **[アプリケーション設定]** の順に選択します。 

名前:`APPLICATIONINSIGHTS_CONNECTION_STRING`値:`Connection String Value`

# <a name="java"></a>[Java](#tab/java)

既定のエンドポイント アドレスを変更するように applicationinsights.xml ファイルを変更します。

```xml
<?xml version="1.0" encoding="utf-8"?>
<ApplicationInsights xmlns="http://schemas.microsoft.com/ApplicationInsights/2013/Settings">
  <InstrumentationKey>ffffeeee-dddd-cccc-bbbb-aaaa99998888</InstrumentationKey>
  <TelemetryModules>
    <Add type="com.microsoft.applicationinsights.web.extensibility.modules.WebRequestTrackingTelemetryModule"/>
    <Add type="com.microsoft.applicationinsights.web.extensibility.modules.WebSessionTrackingTelemetryModule"/>
    <Add type="com.microsoft.applicationinsights.web.extensibility.modules.WebUserTrackingTelemetryModule"/>
  </TelemetryModules>
  <TelemetryInitializers>
    <Add type="com.microsoft.applicationinsights.web.extensibility.initializers.WebOperationIdTelemetryInitializer"/>
    <Add type="com.microsoft.applicationinsights.web.extensibility.initializers.WebOperationNameTelemetryInitializer"/>
    <Add type="com.microsoft.applicationinsights.web.extensibility.initializers.WebSessionTelemetryInitializer"/>
    <Add type="com.microsoft.applicationinsights.web.extensibility.initializers.WebUserTelemetryInitializer"/>
    <Add type="com.microsoft.applicationinsights.web.extensibility.initializers.WebUserAgentTelemetryInitializer"/>
  </TelemetryInitializers>
  <!--Add the following Channel value to modify the Endpoint address-->
  <Channel type="com.microsoft.applicationinsights.channel.concrete.inprocess.InProcessTelemetryChannel">
  <EndpointAddress>TelemetryChannel_Endpoint_Address</EndpointAddress>
  </Channel>
</ApplicationInsights>
```

### <a name="spring-boot"></a>Spring Boot

`application.properties` ファイルを変更して以下を追加します。

```yaml
azure.application-insights.channel.in-process.endpoint-address= TelemetryChannel_Endpoint_Address
```

# <a name="nodejs"></a>[Node.js](#tab/nodejs)

```javascript
var appInsights = require("applicationinsights");
appInsights.setup('INSTRUMENTATION_KEY');
appInsights.defaultClient.config.endpointUrl = "TelemetryChannel_Endpoint_Address"; // ingestion
appInsights.defaultClient.config.profileQueryEndpoint = "Profile_Query_Endpoint_address"; // appid/profile lookup
appInsights.defaultClient.config.quickPulseHost = "QuickPulse_Endpoint_Address"; //live metrics
appInsights.Configuration.start();
```

エンドポイントは、環境変数を使用して構成することもできます。

```
Instrumentation Key: "APPINSIGHTS_INSTRUMENTATIONKEY"
Profile Endpoint: "Profile_Query_Endpoint_address"
Live Metrics Endpoint: "QuickPulse_Endpoint_Address"
```

# <a name="javascript"></a>[JavaScript](#tab/js)

```javascript
<script type="text/javascript">
    var sdkInstance="appInsightsSDK";window[sdkInstance]="appInsights";var aiName=window[sdkInstance],aisdk=window[aiName]||function(e){function n(e){t[e]=function(){var n=arguments;t.queue.push(function(){t[e].apply(t,n)})}}var t={config:e};t.initialize=!0;var i=document,a=window;setTimeout(function(){var n=i.createElement("script");n.src=e.url||"https://az416426.vo.msecnd.net/scripts/b/ai.2.min.js",i.getElementsByTagName("script")[0].parentNode.appendChild(n)});try{t.cookie=i.cookie}catch(e){}t.queue=[],t.version=2;for(var r=["Event","PageView","Exception","Trace","DependencyData","Metric","PageViewPerformance"];r.length;)n("track"+r.pop());n("startTrackPage"),n("stopTrackPage");var s="Track"+r[0];if(n("start"+s),n("stop"+s),n("setAuthenticatedUserContext"),n("clearAuthenticatedUserContext"),n("flush"),!(!0===e.disableExceptionTracking||e.extensionConfig&&e.extensionConfig.ApplicationInsightsAnalytics&&!0===e.extensionConfig.ApplicationInsightsAnalytics.disableExceptionTracking)){n("_"+(r="onerror"));var o=a[r];a[r]=function(e,n,i,a,s){var c=o&&o(e,n,i,a,s);return!0!==c&&t["_"+r]({message:e,url:n,lineNumber:i,columnNumber:a,error:s}),c},e.autoExceptionInstrumented=!0}return t}(
    {
      instrumentationKey:"INSTRUMENTATION_KEY",
      endpointUrl: "TelemetryChannel_Endpoint_Address"
    }
    );window[aiName]=aisdk,aisdk.queue&&0===aisdk.queue.length&&aisdk.trackPageView({});
</script>
```

# <a name="python"></a>[Python](#tab/python)

opencensus-python SDK のインジェスト エンドポイントの変更に関するガイダンスについては、[opencensus-python リポジトリ](https://github.com/census-instrumentation/opencensus-python/blob/af284a92b80bcbaf5db53e7e0813f96691b4c696/contrib/opencensus-ext-azure/opencensus/ext/azure/common/__init__.py)を参照してください。

---

## <a name="regions-that-require-endpoint-modification"></a>エンドポイントの変更が必要なリージョン

現在、エンドポイントの変更が必要なリージョンは [Azure Government](../../azure-government/compare-azure-government-global-azure.md#application-insights) と [Azure China](/azure/china/resources-developer-guide) のみです。

|リージョン |  エンドポイント名 | 値 |
|-----------------|:------------|:-------------|
| Azure 中国 | テレメトリ チャネル | `https://dc.applicationinsights.azure.cn/v2/track` |
| Azure 中国 | QuickPulse (Live Metrics) |`https://live.applicationinsights.azure.cn/QuickPulseService.svc` |
| Azure 中国 | Profile Query |`https://dc.applicationinsights.azure.cn/api/profiles/{0}/appId`  |
| Azure Government | テレメトリ チャネル |`https://dc.applicationinsights.us/v2/track` |
| Azure Government | QuickPulse (Live Metrics) |`https://quickpulse.applicationinsights.us/QuickPulseService.svc` |
| Azure Government | Profile Query |`https://dc.applicationinsights.us/api/profiles/{0}/appId` |

現在、一般的に `api.applicationinsights.io' によってアクセスされる [Application Insights REST API](https://dev.applicationinsights.io/
) をお使いの場合、お客様のリージョンのローカルのエンドポイントを使用する必要があります。

|リージョン |  エンドポイント名 | 値 |
|-----------------|:------------|:-------------|
| Azure 中国 | REST API | `api.applicationinsights.azure.cn` |
| Azure Government | REST API | `api.applicationinsights.us`|

> [!NOTE]
> Azure App Services のコードレス エージェント/拡張機能ベースの監視は、これらのリージョンでは**現在サポートされていません**。 この機能を利用できるようになり次第、この記事は更新される予定です。

## <a name="next-steps"></a>次のステップ

- Azure Government のカスタム変更の詳細については、[Azure の監視と管理](../../azure-government/compare-azure-government-global-azure.md#application-insights)の詳細なガイダンスを参照してください。
- Azure China の詳細については、[Azure China プレイブック](/azure/china/)のページを参照してください。
