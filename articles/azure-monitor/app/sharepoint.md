---
title: Application Insights で SharePoint を監視する
description: 新しいインストルメンテーション キーで新しいアプリケーションの監視を開始します。
ms.topic: conceptual
ms.date: 07/11/2018
ms.openlocfilehash: 392c0e0ee46e8acd540d498cbda1d240611d182e
ms.sourcegitcommit: a76ff927bd57d2fcc122fa36f7cb21eb22154cfa
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/28/2020
ms.locfileid: "87326530"
---
# <a name="monitor-a-sharepoint-site-with-application-insights"></a>Application Insights で SharePoint を監視する

Azure Application Insights を使うと、アプリの可用性、パフォーマンス、使用状況を監視できます。 ここでは、SharePoint サイトのために Application Insights を設定する方法について学習します。

> [!NOTE]
> セキュリティ上の問題により、この記事で説明されているスクリプトを SharePoint の最新 UX の Web ページに直接追加することはできません。 別の方法として、[SharePoint Framework (SPFx)](/sharepoint/dev/spfx/extensions/overview-extensions) を使用して、SharePoint サイトに Application Insights をインストールするために使用できるカスタム拡張機能を作成することもできます。 詳細については、「[How to create a SPFx extension solution with AppInsights installed from scratch (AppInsights がインストールされた SPFx 拡張機能ソリューションを最初から作成する方法)](https://github.com/microsoft/ApplicationInsights-JS/tree/master/SPO#how-to-create-a-spfx-extension-solution-with-appinsights-installed-from-scratch)」を参照するか、[サンプルを表示](https://github.com/microsoft/ApplicationInsights-JS/tree/master/SPO/AppInsightsExtensionSolutionSample)してください。 

## <a name="create-an-application-insights-resource"></a>Application Insights リソースの作成
[Azure ポータル](https://portal.azure.com)で、Application Insights の新しいリソースを作成します。 アプリケーションの種類として ASP.NET を選択します。

![[プロパティ] をクリックし、キーを選択して、Ctrl キーを押しながら C キーを押す](./media/sharepoint/001.png)

表示されるウィンドウには、アプリケーションに関するパフォーマンスと使用状況データが表示されます。 次に Azure にログインするときにこのブレードに戻るには、スタート画面でそのタイルを見つけてください。 あるいは、[参照] ボタンをクリックして探します。

## <a name="add-the-script-to-your-web-pages"></a>Web ページにスクリプトを追加する

```HTML
<!-- 
To collect user behavior analytics tools about your application, 
insert the following script into each page you want to track.
Place this code immediately before the closing </head> tag,
and before any other scripts. Your first data will appear 
automatically in just a few seconds.
-->
<script type="text/javascript">
var sdkInstance="appInsightsSDK";window[sdkInstance]="appInsights";var aiName=window[sdkInstance],aisdk=window[aiName]||function(n){var o={config:n,initialize:!0},t=document,e=window,i="script";setTimeout(function(){var e=t.createElement(i);e.src=n.url||"https://az416426.vo.msecnd.net/scripts/b/ai.2.min.js",t.getElementsByTagName(i)[0].parentNode.appendChild(e)});try{o.cookie=t.cookie}catch(e){}function a(n){o[n]=function(){var e=arguments;o.queue.push(function(){o[n].apply(o,e)})}}o.queue=[],o.version=2;for(var s=["Event","PageView","Exception","Trace","DependencyData","Metric","PageViewPerformance"];s.length;)a("track"+s.pop());var r="Track",c=r+"Page";a("start"+c),a("stop"+c);var u=r+"Event";if(a("start"+u),a("stop"+u),a("addTelemetryInitializer"),a("setAuthenticatedUserContext"),a("clearAuthenticatedUserContext"),a("flush"),o.SeverityLevel={Verbose:0,Information:1,Warning:2,Error:3,Critical:4},!(!0===n.disableExceptionTracking||n.extensionConfig&&n.extensionConfig.ApplicationInsightsAnalytics&&!0===n.extensionConfig.ApplicationInsightsAnalytics.disableExceptionTracking)){a("_"+(s="onerror"));var p=e[s];e[s]=function(e,n,t,i,a){var r=p&&p(e,n,t,i,a);return!0!==r&&o["_"+s]({message:e,url:n,lineNumber:t,columnNumber:i,error:a}),r},n.autoExceptionInstrumented=!0}return o}(
{
  instrumentationKey:"INSTRUMENTATION_KEY"
}
);(window[aiName]=aisdk).queue&&0===aisdk.queue.length&&aisdk.trackPageView({});
</script>
```

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

