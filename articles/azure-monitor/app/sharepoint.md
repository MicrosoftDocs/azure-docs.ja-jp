---
title: Application Insights で SharePoint を監視する
description: 新しいインストルメンテーション キーで新しいアプリケーションの監視を開始します。
ms.topic: conceptual
ms.date: 09/08/2020
ms.openlocfilehash: 74f0eeecb303d68af8aaa48b5c1806ceeef4d318
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102486371"
---
# <a name="monitor-a-sharepoint-site-with-application-insights"></a>Application Insights で SharePoint を監視する

Azure Application Insights を使うと、アプリの可用性、パフォーマンス、使用状況を監視できます。 ここでは、SharePoint サイトのために Application Insights を設定する方法について学習します。

> [!NOTE]
> セキュリティ上の問題により、この記事で説明されているスクリプトを SharePoint の最新 UX の Web ページに直接追加することはできません。 別の方法として、[SharePoint Framework (SPFx)](/sharepoint/dev/spfx/extensions/overview-extensions) を使用して、SharePoint サイトに Application Insights をインストールするために使用できるカスタム拡張機能を作成することもできます。

## <a name="create-an-application-insights-resource"></a>Application Insights リソースの作成
[Azure ポータル](https://portal.azure.com)で、Application Insights の新しいリソースを作成します。 アプリケーションの種類として ASP.NET を選択します。

![[プロパティ] をクリックし、キーを選択して、Ctrl キーを押しながら C キーを押す](./media/sharepoint/001.png)

表示されるウィンドウには、アプリケーションに関するパフォーマンスと使用状況データが表示されます。 次に Azure にログインするときにこのブレードに戻るには、スタート画面でそのタイルを見つけてください。 あるいは、[参照] ボタンをクリックして探します。

## <a name="add-the-script-to-your-web-pages"></a>Web ページにスクリプトを追加する

現在のスニペット (下に示されています) はバージョン "5" で、バージョンは sv:"#" としてスニペット内でエンコードされています。[現在のバージョンは GitHub で入手することもできます](https://go.microsoft.com/fwlink/?linkid=2156318)。

```HTML
<!-- 
To collect user behavior analytics tools about your application, 
insert the following script into each page you want to track.
Place this code immediately before the closing </head> tag,
and before any other scripts. Your first data will appear 
automatically in just a few seconds.
-->
<script type="text/javascript">
!function(T,l,y){var S=T.location,k="script",D="instrumentationKey",C="ingestionendpoint",I="disableExceptionTracking",E="ai.device.",b="toLowerCase",w="crossOrigin",N="POST",e="appInsightsSDK",t=y.name||"appInsights";(y.name||T[e])&&(T[e]=t);var n=T[t]||function(d){var g=!1,f=!1,m={initialize:!0,queue:[],sv:"5",version:2,config:d};function v(e,t){var n={},a="Browser";return n[E+"id"]=a[b](),n[E+"type"]=a,n["ai.operation.name"]=S&&S.pathname||"_unknown_",n["ai.internal.sdkVersion"]="javascript:snippet_"+(m.sv||m.version),{time:function(){var e=new Date;function t(e){var t=""+e;return 1===t.length&&(t="0"+t),t}return e.getUTCFullYear()+"-"+t(1+e.getUTCMonth())+"-"+t(e.getUTCDate())+"T"+t(e.getUTCHours())+":"+t(e.getUTCMinutes())+":"+t(e.getUTCSeconds())+"."+((e.getUTCMilliseconds()/1e3).toFixed(3)+"").slice(2,5)+"Z"}(),iKey:e,name:"Microsoft.ApplicationInsights."+e.replace(/-/g,"")+"."+t,sampleRate:100,tags:n,data:{baseData:{ver:2}}}}var h=d.url||y.src;if(h){function a(e){var t,n,a,i,r,o,s,c,u,p,l;g=!0,m.queue=[],f||(f=!0,t=h,s=function(){var e={},t=d.connectionString;if(t)for(var n=t.split(";"),a=0;a<n.length;a++){var i=n[a].split("=");2===i.length&&(e[i[0][b]()]=i[1])}if(!e[C]){var r=e.endpointsuffix,o=r?e.location:null;e[C]="https://"+(o?o+".":"")+"dc."+(r||"services.visualstudio.com")}return e}(),c=s[D]||d[D]||"",u=s[C],p=u?u+"/v2/track":d.endpointUrl,(l=[]).push((n="SDK LOAD Failure: Failed to load Application Insights SDK script (See stack for details)",a=t,i=p,(o=(r=v(c,"Exception")).data).baseType="ExceptionData",o.baseData.exceptions=[{typeName:"SDKLoadFailed",message:n.replace(/\./g,"-"),hasFullStack:!1,stack:n+"\nSnippet failed to load ["+a+"] -- Telemetry is disabled\nHelp Link: https://go.microsoft.com/fwlink/?linkid=2128109\nHost: "+(S&&S.pathname||"_unknown_")+"\nEndpoint: "+i,parsedStack:[]}],r)),l.push(function(e,t,n,a){var i=v(c,"Message"),r=i.data;r.baseType="MessageData";var o=r.baseData;return o.message='AI (Internal): 99 message:"'+("SDK LOAD Failure: Failed to load Application Insights SDK script (See stack for details) ("+n+")").replace(/\"/g,"")+'"',o.properties={endpoint:a},i}(0,0,t,p)),function(e,t){if(JSON){var n=T.fetch;if(n&&!y.useXhr)n(t,{method:N,body:JSON.stringify(e),mode:"cors"});else if(XMLHttpRequest){var a=new XMLHttpRequest;a.open(N,t),a.setRequestHeader("Content-type","application/json"),a.send(JSON.stringify(e))}}}(l,p))}function i(e,t){f||setTimeout(function(){!t&&m.core||a()},500)}var e=function(){var n=l.createElement(k);n.src=h;var e=y[w];return!e&&""!==e||"undefined"==n[w]||(n[w]=e),n.onload=i,n.onerror=a,n.onreadystatechange=function(e,t){"loaded"!==n.readyState&&"complete"!==n.readyState||i(0,t)},n}();y.ld<0?l.getElementsByTagName("head")[0].appendChild(e):setTimeout(function(){l.getElementsByTagName(k)[0].parentNode.appendChild(e)},y.ld||0)}try{m.cookie=l.cookie}catch(p){}function t(e){for(;e.length;)!function(t){m[t]=function(){var e=arguments;g||m.queue.push(function(){m[t].apply(m,e)})}}(e.pop())}var n="track",r="TrackPage",o="TrackEvent";t([n+"Event",n+"PageView",n+"Exception",n+"Trace",n+"DependencyData",n+"Metric",n+"PageViewPerformance","start"+r,"stop"+r,"start"+o,"stop"+o,"addTelemetryInitializer","setAuthenticatedUserContext","clearAuthenticatedUserContext","flush"]),m.SeverityLevel={Verbose:0,Information:1,Warning:2,Error:3,Critical:4};var s=(d.extensionConfig||{}).ApplicationInsightsAnalytics||{};if(!0!==d[I]&&!0!==s[I]){var c="onerror";t(["_"+c]);var u=T[c];T[c]=function(e,t,n,a,i){var r=u&&u(e,t,n,a,i);return!0!==r&&m["_"+c]({message:e,url:t,lineNumber:n,columnNumber:a,error:i}),r},d.autoExceptionInstrumented=!0}return m}(y.cfg);function a(){y.onInit&&y.onInit(n)}(T[t]=n).queue&&0===n.queue.length?(n.queue.push(a),n.trackPageView({})):a()}(window,document,{
src: "https://js.monitor.azure.com/scripts/b/ai.2.gbl.min.js", // The SDK URL Source
// name: "appInsights", // Global SDK Instance name defaults to "appInsights" when not supplied
// ld: 0, // Defines the load delay (in ms) before attempting to load the sdk. -1 = block page load and add to head. (default) = 0ms load after timeout,
// useXhr: 1, // Use XHR instead of fetch to report failures (if available),
crossOrigin: "anonymous", // When supplied this will add the provided value as the cross origin attribute on the script tag
// onInit: null, // Once the application insights instance has loaded and initialized this callback function will be called with 1 argument -- the sdk instance (DO NOT ADD anything to the sdk.queue -- As they won't get called)
cfg: { // Application Insights Configuration
  instrumentationKey:"INSTRUMENTATION_KEY"
}});
</script>
```

> [!NOTE]
> SharePoint の Url では、別のモジュール形式 "...\ai.2.gbl.min.js" が使用されています (追加の **.gbl** に注意してください)。この代替モジュール形式は、SDK の初期化に失敗し、テレメトリ イベントが失われる原因となる、スクリプトの読み込み順序に起因する問題を回避するために必要です。
>
> この問題は、requireJS が SDK より前に読み込まれ、初期化されていることが原因で発生します。

追跡するすべてのページの &lt;/head&gt; タグの直前に、スクリプトを挿入します。Web サイトにマスター ページがある場合は、そこにスクリプトを配置できます。 たとえば、ASP.NET MVC プロジェクトで、View\Shared\_Layout.cshtml にスクリプトを配置します。

このスクリプトには、Application Insights リソースに利用統計情報を転送するインストルメンテーション キーが含まれています。

### <a name="add-the-code-to-your-site-pages"></a>コードをサイト ページに追加する
#### <a name="on-the-master-page"></a>マスター ページで
サイトのマスター ページを編集すれば、そのサイトのすべてのページを監視できます。

マスター ページを確認し、SharePoint Designer またはその他のエディターを使用して編集します。

![SharePoint Designer または別のエディターを使用してマスターページを編集する方法を示すスクリーンショット。](./media/sharepoint/03-master.png)

タグの直前にコードを追加 </head> します。 

![コードをサイト ページに追加する場所を示すスクリーンショット。](./media/sharepoint/04-code.png)

#### <a name="or-on-individual-pages"></a>あるいは個別ページで
ページを限定して監視するには、ページ別にスクリプトを追加します。 

Web パーツを挿入し、コード スニペットをそれに埋め込みます。

![限られたページのセットを監視するスクリプトの追加を示すスクリーンショット。](./media/sharepoint/05-page.png)

## <a name="view-data-about-your-app"></a>アプリに関するデータを表示する
アプリケーションを再デプロイします。

[Azure ポータル](https://portal.azure.com)で、アプリケーションのブレードに戻ります。

検索に最初のイベントが表示されます。 

![アプリに表示できる新しいデータを示すスクリーンショット。](./media/sharepoint/09-search.png)

大量のデータが予想される場合は、数秒後に [最新の情報に更新] をクリックします。

## <a name="capturing-user-id"></a>ユーザー ID のキャプチャ
Web ページの標準のコード スニペットでは SharePoint からユーザー ID はキャプチャされませんが、少し変更すればキャプチャできます。

1. Application Insights の [要点] ドロップダウンから、アプリのインストルメンテーション キーをコピーします。 

    ![Application Insights の [要点] ドロップダウンからのアプリのインストルメンテーションのコピーを示すスクリーンショット。](./media/sharepoint/02-props.png)

1. 次のスニペットで "XXXX" のインストルメンテーション キーを置き換えます。 
2. ポータルから取得したスニペットの代わりに、スクリプトを SharePoint アプリに埋め込みます。

```


<SharePoint:ScriptLink ID="ScriptLink1" name="SP.js" runat="server" localizable="false" loadafterui="true" /> 
<SharePoint:ScriptLink ID="ScriptLink2" name="SP.UserProfiles.js" runat="server" localizable="false" loadafterui="true" /> 

<script type="text/javascript"> 
var personProperties; 

// Ensure that the SP.UserProfiles.js file is loaded before the custom code runs. 
SP.SOD.executeOrDelayUntilScriptLoaded(getUserProperties, 'SP.UserProfiles.js'); 

function getUserProperties() { 
    // Get the current client context and PeopleManager instance. 
    var clientContext = new SP.ClientContext.get_current(); 
    var peopleManager = new SP.UserProfiles.PeopleManager(clientContext); 

    // Get user properties for the target user. 
    // To get the PersonProperties object for the current user, use the 
    // getMyProperties method. 

    personProperties = peopleManager.getMyProperties(); 

    // Load the PersonProperties object and send the request. 
    clientContext.load(personProperties); 
    clientContext.executeQueryAsync(onRequestSuccess, onRequestFail); 
} 

// This function runs if the executeQueryAsync call succeeds. 
function onRequestSuccess() { 
var appInsights=window.appInsights||function(config){
function s(config){t[config]=function(){var i=arguments;t.queue.push(function(){t[config].apply(t,i)})}}var t={config:config},r=document,f=window,e="script",o=r.createElement(e),i,u;for(o.src=config.url||"//az416426.vo.msecnd.net/scripts/a/ai.0.js",r.getElementsByTagName(e)[0].parentNode.appendChild(o),t.cookie=r.cookie,t.queue=[],i=["Event","Exception","Metric","PageView","Trace"];i.length;)s("track"+i.pop());return config.disableExceptionTracking||(i="onerror",s("_"+i),u=f[i],f[i]=function(config,r,f,e,o){var s=u&&u(config,r,f,e,o);return s!==!0&&t["_"+i](config,r,f,e,o),s}),t
    }({
        instrumentationKey:"XXXX"
    });
    window.appInsights=appInsights;
    appInsights.trackPageView(document.title,window.location.href, {User: personProperties.get_displayName()});
} 

// This function runs if the executeQueryAsync call fails. 
function onRequestFail(sender, args) { 
} 
</script> 


```



## <a name="next-steps"></a>次の手順
* [Web テスト](./monitor-web-app-availability.md) はサイトの可用性を監視します。
* [Application Insights](./app-insights-overview.md) 。

<!--Link references-->
