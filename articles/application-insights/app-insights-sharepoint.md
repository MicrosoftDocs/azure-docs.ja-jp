---
title: "Application Insights で SharePoint を監視する"
description: "新しいインストルメンテーション キーで新しいアプリケーションの監視を開始します。"
services: application-insights
documentationcenter: 
author: alancameronwills
manager: douge
ms.assetid: 2bfe5910-d673-4cf6-a5c1-4c115eae1be0
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 03/24/2016
ms.author: awills
translationtype: Human Translation
ms.sourcegitcommit: 41ce9b0e323c0938b6db98b99d8d687d1ed0f0ef
ms.openlocfilehash: 3977cbc0f111de39621a875cce2d6714559a73fd


---
# <a name="monitor-a-sharepoint-site-with-application-insights"></a>Application Insights で SharePoint を監視する
Azure Application Insights を使うと、アプリの可用性、パフォーマンス、使用状況を監視できます。 ここでは、SharePoint サイトのために Application Insights を設定する方法について学習します。

## <a name="create-an-application-insights-resource"></a>Application Insights リソースの作成
[Azure ポータル](https://portal.azure.com)で、Application Insights の新しいリソースを作成します。 アプリケーションの種類として ASP.NET を選択します。

![[プロパティ] をクリックし、キーを選択して、Ctrl キーを押しながら C キーを押す](./media/app-insights-sharepoint/01-new.png)

表示されるブレードには、アプリケーションに関するパフォーマンスと使用状況データが表示されます。 次に Azure にログインするときにこのブレードに戻るには、スタート画面でそのタイルを見つけてください。 あるいは、[参照] ボタンをクリックして探します。

## <a name="add-our-script-to-your-web-pages"></a>Web ページに、スクリプトを追加する
クイック スタートで、Web ページのスクリプトを取得します。

![](./media/app-insights-sharepoint/02-monitor-web-page.png)

追跡するすべてのページの &lt;/head&gt; タグの直前に、スクリプトを挿入します。 Web サイトにマスター ページがある場合は、そこにスクリプトを配置できます。 たとえば、ASP.NET MVC プロジェクトで、View\Shared\_Layout.cshtml にスクリプトを配置します。

このスクリプトには、Application Insights リソースに利用統計情報を転送するインストルメンテーション キーが含まれています。

### <a name="add-the-code-to-your-site-pages"></a>コードをサイト ページに追加する
#### <a name="on-the-master-page"></a>マスター ページで
サイトのマスター ページを編集すれば、そのサイトのすべてのページを監視できます。

マスター ページを確認し、SharePoint Designer またはその他のエディターを使用して編集します。

![](./media/app-insights-sharepoint/03-master.png)

</head> タグの直前にコードを追加します。 

![](./media/app-insights-sharepoint/04-code.png)

#### <a name="or-on-individual-pages"></a>あるいは個別ページで
ページを限定して監視するには、ページ別にスクリプトを追加します。 

Web パーツを挿入し、コード スニペットをそれに埋め込みます。

![](./media/app-insights-sharepoint/05-page.png)

## <a name="view-data-about-your-app"></a>アプリに関するデータを表示する
アプリケーションを再デプロイします。

[Azure ポータル](https://portal.azure.com)で、アプリケーションのブレードに戻ります。

検索に最初のイベントが表示されます。 

![](./media/app-insights-sharepoint/09-search.png)

大量のデータが予想される場合は、数秒後に [最新の情報に更新] をクリックします。

概要ブレードから **[使用状況の分析]** をクリックし、ユーザー、セッション、およびページ ビューのグラフに表示します。

![](./media/app-insights-sharepoint/06-usage.png)

詳細については、任意のグラフ (例: ページ ビュー) をクリックしてください。

![](./media/app-insights-sharepoint/07-pages.png)

または、ユーザーをクリックしてください。

![](./media/app-insights-sharepoint/08-users.png)

## <a name="capturing-user-id"></a>ユーザー ID のキャプチャ
Web ページの標準のコード スニペットでは SharePoint からユーザー ID はキャプチャされませんが、少し変更すればキャプチャできます。

1. Application Insights の [要点] ドロップダウンから、アプリのインストルメンテーション キーをコピーします。 

    ![](./media/app-insights-sharepoint/02-props.png)

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



## <a name="next-steps"></a>次のステップ
* [Web テスト](app-insights-monitor-web-app-availability.md) はサイトの可用性を監視します。
* [Application Insights](app-insights-overview.md) 。

<!--Link references-->





<!--HONumber=Nov16_HO3-->


