---
title: Azure Monitor Application Insights による Web サイトの監視のクイック スタート | Microsoft Docs
description: Azure Monitor Application Insights を使用したクライアント/ブラウザー側の Web サイト監視を迅速にセットアップする手順を説明します
services: application-insights
keywords: ''
author: mrbullwinkle
ms.author: mbullwin
ms.date: 10/29/2018
ms.service: application-insights
ms.custom: mvc
ms.topic: quickstart
manager: carmonm
ms.openlocfilehash: a35f4d8c1d5bf5943ecba02ff262fbc7fc0730fe
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2019
ms.locfileid: "58108230"
---
# <a name="start-monitoring-your-website"></a>Web サイトの監視を開始する

Azure Monitor Application Insights を使用すると、Web サイトの可用性、パフォーマンス、および利用状況を簡単に監視できます。 アプリケーションのエラーを、ユーザーからの報告を待つことなく、迅速に特定して診断することもできます。 Application Insights では、サーバー側の監視機能とクライアント/ブラウザー側の監視機能の両方が提供されます。

このクイック スタートでは、[オープン ソースの Application Insights JavaScript SDK](https://github.com/Microsoft/ApplicationInsights-JS) の追加方法を説明します。これによって、Web サイトへの訪問者にとってのクライアント/ブラウザー側のエクスペリエンスを理解することができます。

## <a name="prerequisites"></a>前提条件

このクイック スタートを完了するには、以下が必要です。

- Azure サブスクリプションが必要です。

Azure サブスクリプションをお持ちでない場合は、開始する前に[無料](https://azure.microsoft.com/free/)アカウントを作成してください。

## <a name="sign-in-to-the-azure-portal"></a>Azure ポータルにサインインします。

[Azure Portal](https://portal.azure.com/) にサインインします。

## <a name="enable-application-insights"></a>Application Insights を有効にする

Application Insights は、オンプレミスまたはクラウドで実行されている、インターネットに接続された任意のアプリケーションからテレメトリ データを収集できます。 このデータの表示を開始するには、次の手順を実行します。

1. **[リソースの作成]** > **[管理ツール]** > **[Application Insights]** の順に選択します。

   構成ボックスが表示されたら、次の表を使用して入力フィールドに入力します。

    | 設定        | 値           | 説明  |
   | ------------- |:-------------|:-----|
   | **Name**      | グローバルに一意の値 | 監視しているアプリを識別する名前 |
   | **アプリケーションの種類** | 汎用アプリケーション | 監視しているアプリの種類 |
   | **リソース グループ**     | myResourceGroup      | App Insights データをホストする新しいリソース グループの名前 |
   | **場所** | 米国東部 | 近くにある場所か、アプリがホストされている場所の近くを選択します。 |

2. **Create** をクリックしてください。

## <a name="create-an-html-file"></a>HTML ファイルを作成する

1. ローカル コンピューターで、``hello_world.html`` という名前のファイルを作成します。 この例では、``C:\hello_world.html`` のように、ファイルを C: ドライブのルートに配置します。
2. 以下のスクリプトを ``hello_world.html`` にコピーします。

    ```html
    <!DOCTYPE html>
    <html>
    <head>
    <title>Azure Monitor Application Insights</title>
    </head>
    <body>
    <h1>Azure Monitor Application Insights Hello World!</h1>
    <p>You can use the Application Insights JavaScript SDK to perform client/browser-side monitoring of your website. To learn about more advanced JavaScript SDK configurations visit the <a href="https://github.com/Microsoft/ApplicationInsights-JS/blob/master/API-reference.md" title="API Reference">API reference</a>.</p>
    </body>
    </html>
    ```

## <a name="configure-app-insights-sdk"></a>App Insights SDK を構成する

1. **[概要]** > **[Essentials]** を選択し、アプリケーションの**インストルメンテーション キー**をコピーします。

   ![新しい App Insights リソースのフォーム](media/website-monitoring/instrumentation-key-001.png)

2. ``hello_world.html`` の閉じる ``</head>`` タグの前に、次のスクリプトを追加します。

   ```javascript
      <script type="text/javascript">
        var appInsights=window.appInsights||function(a){
            function b(a){c[a]=function(){var b=arguments;c.queue.push(function(){c[a].apply(c,b)})}}var c={config:a},d=document,e=window;setTimeout(function(){var b=d.createElement("script");b.src=a.url||"https://az416426.vo.msecnd.net/scripts/a/ai.0.js",d.getElementsByTagName("script")[0].parentNode.appendChild(b)});try{c.cookie=d.cookie}catch(a){}c.queue=[];for(var f=["Event","Exception","Metric","PageView","Trace","Dependency"];f.length;)b("track"+f.pop());if(b("setAuthenticatedUserContext"),b("clearAuthenticatedUserContext"),b("startTrackEvent"),b("stopTrackEvent"),b("startTrackPage"),b("stopTrackPage"),b("flush"),!a.disableExceptionTracking){f="onerror",b("_"+f);var g=e[f];e[f]=function(a,b,d,e,h){var i=g&&g(a,b,d,e,h);return!0!==i&&c["_"+f](a,b,d,e,h),i}}return c
        }({
            instrumentationKey: "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxx"
        });
        
        window.appInsights=appInsights,appInsights.queue&&0===appInsights.queue.length&&appInsights.trackPageView();
   </script>
   ```

3. ``hello_world.html`` を編集し、インストルメンテーション キーを追加します。

4. ローカル ブラウザー セッションで、``hello_world.html`` を開きます。 これによって、単一のページビューが作成されます。 ブラウザーを最新の情報に更新して、複数のテスト ページビューを生成することができます。

## <a name="start-monitoring-in-the-azure-portal"></a>Azure Portal で監視を開始する

1. Azure Portal で、インストルメンテーション キーを取得した Application Insights の**概要**ページを再度開き、現在実行中のアプリケーションに関する詳細情報を表示できます。 概要ページの 4 つの既定のグラフでは、サーバー側のアプリケーション データが対象になっています。 ここではクライアント/ブラウザー側の JavaScript SDK との対話をインストルメント化しているため、この特定のビューは、サーバー側の SDK もインストールされない限り、適用されません。

2. [![アプリケーション マップ アイコン](media/website-monitoring/006.png) **分析]** をクリックします。  これにより、Application Insights で収集されたすべてのデータを分析するための豊富なクエリ言語を備えた **Analytics** が開きます。 クライアント側のブラウザー要求に関連しているデータを表示するには、次のクエリを実行します。

    ```kusto
    // average pageView duration by name
    let timeGrain=1s;
    let dataset=pageViews
    // additional filters can be applied here
    | where timestamp > ago(15m)
    | where client_Type == "Browser" ;
    // calculate average pageView duration for all pageViews
    dataset
    | summarize avg(duration) by bin(timestamp, timeGrain)
    | extend pageView='Overall'
    // render result in a chart
    | render timechart
    ```

   ![一定の期間にわたるユーザー要求に関する Analytics グラフ](./media/website-monitoring/analytics-query.png)

3. **[概要]** ページに戻ります。 **[調査]** ヘッダーの下の **[ブラウザー]** をクリックし、**[パフォーマンス]** を選択します。ここに、Web サイトのパフォーマンスに関連するメトリックが表示されます。 Web サイトのエラーと例外を分析するためのビューもあります。 **[サンプル]** をクリックすると、個々のトランザクションの詳細にドリルダウンすることができます。 ここから、[エンド ツー エンドのトランザクションの詳細](../../azure-monitor/app/transaction-diagnostics.md)のエクスペリエンスにアクセスすることができます。

   ![サーバー メトリックのグラフ](./media/website-monitoring/browser-performance.png)

4. [ユーザー動作分析ツール](../../azure-monitor/app/usage-overview.md)の調査を始めるには、Application Insights のメイン メニューで、**[使用状況]** ヘッダーの下の [**[ユーザー]**](../../azure-monitor/app/usage-segmentation.md) を選択します。 1 台のマシンからテストを行っているため、1 人のユーザーのデータのみが表示されます。 ライブの Web サイトの場合、ユーザーの分布は次のようになります。

     ![ユーザーのグラフ](./media/website-monitoring/usage-users.png)

5. 複数のページを持つより複雑な Web サイトをインストルメント化した場合、もう 1 つの便利なツールは [**User Flows**](../../azure-monitor/app/usage-flows.md) です。 **User Flows** を使用すると、Web サイトのさまざまな部分を通じて訪問者がたどった経路を追跡することができます。

   ![User Flows の視覚化](./media/website-monitoring/user-flows.png)

Web サイトを監視するためのより高度な構成については、[JavaScript SDK の API リファレンス](https://github.com/Microsoft/ApplicationInsights-JS/blob/master/API-reference.md)を参照してください。

## <a name="clean-up-resources"></a>リソースのクリーンアップ

引き続きクイックスタートまたはチュートリアルの作業を行う場合は、このクイックスタートで作成したリソースをクリーンアップしないでください。 そうではなく、これ以上作業を行わない場合は、次の手順に従って、このクイック スタートで作成したすべてのリソースを Azure portal で削除してください。

1. Azure Portal の左側のメニューから、**[リソース グループ]**、**[myResourceGroup]** の順にクリックします。
2. リソース グループのページで **[削除]** をクリックし、テキスト ボックスに「**myResourceGroup**」と入力してから **[削除]** をクリックします。

## <a name="next-steps"></a>次の手順

> [!div class="nextstepaction"]
> [パフォーマンスの問題の特定と診断](https://docs.microsoft.com/azure/application-insights/app-insights-analytics)
