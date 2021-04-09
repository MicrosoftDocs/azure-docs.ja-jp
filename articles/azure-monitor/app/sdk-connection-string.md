---
title: Azure Application Insights の接続文字列 | Microsoft Docs
description: 接続文字列の使用方法。
ms.topic: conceptual
author: timothymothra
ms.author: tilee
ms.date: 01/17/2020
ms.custom: devx-track-js, devx-track-csharp
ms.reviewer: mbullwin
ms.openlocfilehash: df87b060423aeff9fa5f83f21634395fe30e0bbb
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102486286"
---
# <a name="connection-strings"></a>Connection strings

## <a name="overview"></a>概要

接続文字列を使用すると、Application Insights ユーザーは 1 つの構成設定を使用でき、複数のプロキシ設定が不要になります。 監視サービスにデータを送信することを検討しているイントラネット Web サーバー、ソブリン クラウド環境またはハイブリッド クラウド環境で非常に便利です。

キーと値のペアを使用すると、ユーザーが各 Application Insights (AI) サービスまたは製品のプレフィックスとサフィックスの組み合わせを簡単に定義できます。

> [!IMPORTANT]
> 接続文字列とインストルメンテーション キーの両方を設定することはお勧めしません。 ユーザーが両方を設定した場合は、最後に設定した方が優先されます。 


## <a name="scenario-overview"></a>シナリオの概要 

これが最も影響があると考えられるお客様のシナリオ:

- ファイアウォールの例外またはプロキシのリダイレクト 

    イントラネット Web サーバーの監視が必要な場合、以前のソリューションでは、お使いの構成に個々のサービスエンド ポイントを追加するようにお客様に求めていました。 詳細については、[このページ](../faq.md#can-i-monitor-an-intranet-web-server)を参照してください。 
    接続文字列により、この作業の設定が 1 つですみ、より優れた代替方法が提供されます。 プレフィックスとサフィックスを修正するだけで、すべてのエンドポイントを適切なサービスに自動的に設定してリダイレクトすることができます。 

- ソブリン クラウド環境またはハイブリッド クラウド環境

    ユーザーは、定義された [Azure Government リージョン](../../azure-government/compare-azure-government-global-azure.md#application-insights)にデータを送信できます。
    接続文字列を使用すると、イントラネット サーバーまたはハイブリッド クラウド設定のエンドポイント設定を定義できます。 

## <a name="getting-started"></a>作業の開始

### <a name="finding-my-connection-string"></a>接続文字列を見つける

接続文字列は、Application Insights リソースの [概要] ブレードに表示されます。

![概要ブレードの接続文字列](media/overview-dashboard/overview-connection-string.png)

### <a name="schema"></a>スキーマ

#### <a name="max-length"></a>最大長

接続でサポートされている最大長は 4096 文字です。

#### <a name="key-value-pairs"></a>キー/値ペア

接続文字列は、キーと値のペアがセミコロンで区切られた設定のリストで構成されます: `key1=value1;key2=value2;key3=value3`

#### <a name="syntax"></a>構文

- `InstrumentationKey` (例:00000000-0000-0000-0000-000000000000) 接続文字列は **必須** フィールドです。
- `Authorization` (例: ikey) (現時点では ikey 認証のみがサポートされているため、この設定は省略可能です)。
- `EndpointSuffix` (例: applicationinsights.azure.cn) エンドポイント サフィックスを設定すると、接続先の Azure クラウドが SDK に指示されます。 SDK により、個々のサービスに対してエンドポイントの残りの部分がアセンブルされます。
- 明示的なエンドポイント。
  どのサービスも、接続文字列内で明示的にオーバーライドできます。
   - `IngestionEndpoint` (例: `https://dc.applicationinsights.azure.com`)
   - `LiveEndpoint` (例: `https://live.applicationinsights.azure.com`)
   - `ProfilerEndpoint` (例: `https://profiler.monitor.azure.com`)
   - `SnapshotEndpoint` (例: `https://snapshot.monitor.azure.com`)

#### <a name="endpoint-schema"></a>エンドポイント スキーマ

`<prefix>.<suffix>`
- プレフィックス:サービスを定義します。 
- サフィックス:共通ドメイン名を定義します。

##### <a name="valid-suffixes"></a>有効なサフィックス

有効なサフィックスの一覧を次に示します 
- applicationinsights.azure.cn
- applicationinsights.us


関連項目: https://docs.microsoft.com/azure/azure-monitor/app/custom-endpoints#regions-that-require-endpoint-modification


##### <a name="valid-prefixes"></a>有効なプレフィックス

- [テレメトリ インジェスト](./app-insights-overview.md): `dc`
- [Live Metrics](./live-stream.md): `live`
- [Profiler](./profiler-overview.md): `profiler`
- [スナップショット](./snapshot-debugger.md): `snapshot`



## <a name="connection-string-examples"></a>接続文字列の例


### <a name="minimal-valid-connection-string"></a>最小の有効な接続文字列

`InstrumentationKey=00000000-0000-0000-0000-000000000000;`

この例では、インストルメンテーション キーだけが設定されています。

- 認証方式の既定値は "ikey" です 
- インストルメンテーション キー:00000000-0000-0000-0000-000000000000
- リージョン サービスの URI は、[SDK の既定値 ](https://github.com/microsoft/ApplicationInsights-dotnet/blob/develop/BASE/src/Microsoft.ApplicationInsights/Extensibility/Implementation/Endpoints/Constants.cs)に基づき、パブリック グローバル Azure に接続されます。
   - インジェスト: `https://dc.services.visualstudio.com/`
   - Live Metrics: `https://rt.services.visualstudio.com/`
   - Profiler: `https://profiler.monitor.azure.com/`
   - デバッガー: `https://snapshot.monitor.azure.com/`



### <a name="connection-string-with-endpoint-suffix"></a>エンドポイント サフィックスが付いた接続文字列

`InstrumentationKey=00000000-0000-0000-0000-000000000000;EndpointSuffix=ai.contoso.com;`

この例では、この接続文字列で、エンドポイント サフィックスを指定し、SDK によりサービス エンドポイントが構築されます。

- 認証方式の既定値は "ikey" です 
- インストルメンテーション キー:00000000-0000-0000-0000-000000000000
- リージョン サービスの URI は、指定されたエンドポイント サフィックスに基づきます。 
   - インジェスト: `https://dc.ai.contoso.com`
   - Live Metrics: `https://live.ai.contoso.com`
   - Profiler: `https://profiler.ai.contoso.com`
   - デバッガー: `https://snapshot.ai.contoso.com`  



### <a name="connection-string-with-explicit-endpoint-overrides"></a>明示的なエンドポイント オーバーライドを含む接続文字列 

`InstrumentationKey=00000000-0000-0000-0000-000000000000;IngestionEndpoint=https://custom.com:111/;LiveEndpoint=https://custom.com:222/;ProfilerEndpoint=https://custom.com:333/;SnapshotEndpoint=https://custom.com:444/;`

この例では、この接続文字列で、すべてのサービスに明示的なオーバーライドを指定しています。 SDK により、変更なしで提供される正確なエンドポイントが使用されます。

- 認証方式の既定値は "ikey" です 
- インストルメンテーション キー:00000000-0000-0000-0000-000000000000
- リージョン サービスの URI は、明示的なオーバーライド値に基づきます。 
   - インジェスト: `https://custom.com:111/`
   - Live Metrics: `https://custom.com:222/`
   - Profiler: `https://custom.com:333/`
   - デバッガー: `https://custom.com:444/`  


## <a name="how-to-set-a-connection-string"></a>接続文字列を追加する方法

接続文字列は、次の SDK バージョンでサポートされています。
- .NET および .NET Core v2.12.0
- Java v2.5.1 および Java 3.0
- JavaScript v2.3.0
- NodeJS v1.5.0
- Python v1.0.0

接続文字列は、コード、環境変数、または構成ファイルのいずれかで設定できます。



### <a name="environment-variable"></a>環境変数

- 接続文字列: `APPLICATIONINSIGHTS_CONNECTION_STRING`

### <a name="code-samples"></a>コード サンプル

# <a name="netnetcore"></a>[.NET/.NetCore](#tab/net)

プロパティ [TelemetryConfiguration.ConnectionString](https://github.com/microsoft/ApplicationInsights-dotnet/blob/add45ceed35a817dc7202ec07d3df1672d1f610d/BASE/src/Microsoft.ApplicationInsights/Extensibility/TelemetryConfiguration.cs#L271-L274) または [ApplicationInsightsServiceOptions.ConnectionString](https://github.com/microsoft/ApplicationInsights-dotnet/blob/81288f26921df1e8e713d31e7e9c2187ac9e6590/NETCORE/src/Shared/Extensions/ApplicationInsightsServiceOptions.cs#L66-L69) を設定します

.NET の明示的な設定:
```csharp
var configuration = new TelemetryConfiguration
{
    ConnectionString = "InstrumentationKey=00000000-0000-0000-0000-000000000000;"
};
```

.NET 構成ファイル:

```xml
<?xml version="1.0" encoding="utf-8"?>
<ApplicationInsights xmlns="http://schemas.microsoft.com/ApplicationInsights/2013/Settings">
    <ConnectionString>InstrumentationKey=00000000-0000-0000-0000-000000000000</ConnectionString>
</ApplicationInsights>
```

NetCore の明示的な設定:
```csharp
public void ConfigureServices(IServiceCollection services)
{
    var options = new ApplicationInsightsServiceOptions { ConnectionString = "InstrumentationKey=00000000-0000-0000-0000-000000000000;" };
    services.AddApplicationInsightsTelemetry(options: options);
}
```

NetCore config.json: 

```json
{
  "ApplicationInsights": {
    "ConnectionString" : "InstrumentationKey=00000000-0000-0000-0000-000000000000;"
    }
  }
```


# <a name="java"></a>[Java](#tab/java)


Java (v2.5.x) の明示的な設定:
```java
TelemetryConfiguration.getActive().setConnectionString("InstrumentationKey=00000000-0000-0000-0000-000000000000");
```

ApplicationInsights.xml
```xml
<?xml version="1.0" encoding="utf-8"?>
<ApplicationInsights xmlns="http://schemas.microsoft.com/ApplicationInsights/2013/Settings">
    <ConnectionString>InstrumentationKey=00000000-0000-0000-0000-000000000000;</ConnectionString>
</ApplicationInsights>
```

# <a name="javascript"></a>[JavaScript](#tab/js)

重要:JavaScript では、環境変数の使用はサポートされていません。

スニペットの使用:

現在のスニペット (下に示されています) はバージョン "5" で、バージョンは sv:"#" としてスニペットでエンコードされています。[現在のバージョンは GitHub で入手することもできます](https://go.microsoft.com/fwlink/?linkid=2156318)。

```html
<script type="text/javascript">
!function(T,l,y){var S=T.location,k="script",D="instrumentationKey",C="ingestionendpoint",I="disableExceptionTracking",E="ai.device.",b="toLowerCase",w="crossOrigin",N="POST",e="appInsightsSDK",t=y.name||"appInsights";(y.name||T[e])&&(T[e]=t);var n=T[t]||function(d){var g=!1,f=!1,m={initialize:!0,queue:[],sv:"5",version:2,config:d};function v(e,t){var n={},a="Browser";return n[E+"id"]=a[b](),n[E+"type"]=a,n["ai.operation.name"]=S&&S.pathname||"_unknown_",n["ai.internal.sdkVersion"]="javascript:snippet_"+(m.sv||m.version),{time:function(){var e=new Date;function t(e){var t=""+e;return 1===t.length&&(t="0"+t),t}return e.getUTCFullYear()+"-"+t(1+e.getUTCMonth())+"-"+t(e.getUTCDate())+"T"+t(e.getUTCHours())+":"+t(e.getUTCMinutes())+":"+t(e.getUTCSeconds())+"."+((e.getUTCMilliseconds()/1e3).toFixed(3)+"").slice(2,5)+"Z"}(),iKey:e,name:"Microsoft.ApplicationInsights."+e.replace(/-/g,"")+"."+t,sampleRate:100,tags:n,data:{baseData:{ver:2}}}}var h=d.url||y.src;if(h){function a(e){var t,n,a,i,r,o,s,c,u,p,l;g=!0,m.queue=[],f||(f=!0,t=h,s=function(){var e={},t=d.connectionString;if(t)for(var n=t.split(";"),a=0;a<n.length;a++){var i=n[a].split("=");2===i.length&&(e[i[0][b]()]=i[1])}if(!e[C]){var r=e.endpointsuffix,o=r?e.location:null;e[C]="https://"+(o?o+".":"")+"dc."+(r||"services.visualstudio.com")}return e}(),c=s[D]||d[D]||"",u=s[C],p=u?u+"/v2/track":d.endpointUrl,(l=[]).push((n="SDK LOAD Failure: Failed to load Application Insights SDK script (See stack for details)",a=t,i=p,(o=(r=v(c,"Exception")).data).baseType="ExceptionData",o.baseData.exceptions=[{typeName:"SDKLoadFailed",message:n.replace(/\./g,"-"),hasFullStack:!1,stack:n+"\nSnippet failed to load ["+a+"] -- Telemetry is disabled\nHelp Link: https://go.microsoft.com/fwlink/?linkid=2128109\nHost: "+(S&&S.pathname||"_unknown_")+"\nEndpoint: "+i,parsedStack:[]}],r)),l.push(function(e,t,n,a){var i=v(c,"Message"),r=i.data;r.baseType="MessageData";var o=r.baseData;return o.message='AI (Internal): 99 message:"'+("SDK LOAD Failure: Failed to load Application Insights SDK script (See stack for details) ("+n+")").replace(/\"/g,"")+'"',o.properties={endpoint:a},i}(0,0,t,p)),function(e,t){if(JSON){var n=T.fetch;if(n&&!y.useXhr)n(t,{method:N,body:JSON.stringify(e),mode:"cors"});else if(XMLHttpRequest){var a=new XMLHttpRequest;a.open(N,t),a.setRequestHeader("Content-type","application/json"),a.send(JSON.stringify(e))}}}(l,p))}function i(e,t){f||setTimeout(function(){!t&&m.core||a()},500)}var e=function(){var n=l.createElement(k);n.src=h;var e=y[w];return!e&&""!==e||"undefined"==n[w]||(n[w]=e),n.onload=i,n.onerror=a,n.onreadystatechange=function(e,t){"loaded"!==n.readyState&&"complete"!==n.readyState||i(0,t)},n}();y.ld<0?l.getElementsByTagName("head")[0].appendChild(e):setTimeout(function(){l.getElementsByTagName(k)[0].parentNode.appendChild(e)},y.ld||0)}try{m.cookie=l.cookie}catch(p){}function t(e){for(;e.length;)!function(t){m[t]=function(){var e=arguments;g||m.queue.push(function(){m[t].apply(m,e)})}}(e.pop())}var n="track",r="TrackPage",o="TrackEvent";t([n+"Event",n+"PageView",n+"Exception",n+"Trace",n+"DependencyData",n+"Metric",n+"PageViewPerformance","start"+r,"stop"+r,"start"+o,"stop"+o,"addTelemetryInitializer","setAuthenticatedUserContext","clearAuthenticatedUserContext","flush"]),m.SeverityLevel={Verbose:0,Information:1,Warning:2,Error:3,Critical:4};var s=(d.extensionConfig||{}).ApplicationInsightsAnalytics||{};if(!0!==d[I]&&!0!==s[I]){var c="onerror";t(["_"+c]);var u=T[c];T[c]=function(e,t,n,a,i){var r=u&&u(e,t,n,a,i);return!0!==r&&m["_"+c]({message:e,url:t,lineNumber:n,columnNumber:a,error:i}),r},d.autoExceptionInstrumented=!0}return m}(y.cfg);function a(){y.onInit&&y.onInit(n)}(T[t]=n).queue&&0===n.queue.length?(n.queue.push(a),n.trackPageView({})):a()}(window,document,{
src: "https://js.monitor.azure.com/scripts/b/ai.2.min.js", // The SDK URL Source
// name: "appInsights", // Global SDK Instance name defaults to "appInsights" when not supplied
// ld: 0, // Defines the load delay (in ms) before attempting to load the sdk. -1 = block page load and add to head. (default) = 0ms load after timeout,
// useXhr: 1, // Use XHR instead of fetch to report failures (if available),
crossOrigin: "anonymous", // When supplied this will add the provided value as the cross origin attribute on the script tag
// onInit: null, // Once the application insights instance has loaded and initialized this callback function will be called with 1 argument -- the sdk instance (DO NOT ADD anything to the sdk.queue -- As they won't get called)
cfg: { // Application Insights Configuration
  connectionString:"InstrumentationKey=00000000-0000-0000-0000-000000000000;"
}});
</script>
```

> [!NOTE]
> 読みやすくするためと、発生する可能性のある JavaScript エラーを減らすために、上記のスニペット コードでは、使用可能なすべての構成オプションが新しい行に示されています。コメント行の値を変更しない場合は、それを削除できます。

手動セットアップ:
```javascript
import { ApplicationInsights } from '@microsoft/applicationinsights-web'

const appInsights = new ApplicationInsights({ config: {
  connectionString: 'InstrumentationKey=00000000-0000-0000-0000-000000000000;'
  /* ...Other Configuration Options... */
} });
appInsights.loadAppInsights();
appInsights.trackPageView();
```

# <a name="nodejs"></a>[Node.js](#tab/nodejs)

```javascript
const appInsights = require("applicationinsights");
appInsights.setup("InstrumentationKey=00000000-0000-0000-0000-000000000000;");
appInsights.start();
```

# <a name="python"></a>[Python](#tab/python)

ユーザーが環境変数を設定することをお勧めします。

接続文字列を明示的に設定するには:

```python
from opencensus.ext.azure.trace_exporter import AzureExporter
from opencensus.trace.samplers import ProbabilitySampler
from opencensus.trace.tracer import Tracer

tracer = Tracer(exporter=AzureExporter(connection_string='InstrumentationKey=00000000-0000-0000-0000-000000000000'), sampler=ProbabilitySampler(1.0))
```

---

## <a name="next-steps"></a>次のステップ

実行時に開始する:

* [Azure VM と Azure 仮想マシン スケール セットの IIS でホストされたアプリ](./azure-vm-vmss-apps.md)
* [IIS サーバー](./monitor-performance-live-website-now.md)
* [Azure Web Apps](./azure-web-apps.md)

開発時に開始する:

* [ASP.NET](./asp-net.md)
* [ASP.NET Core](./asp-net-core.md)
* [Java](./java-get-started.md)
* [Node.js](./nodejs.md)
* [Python](./opencensus-python.md)

