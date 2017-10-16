---
title: "Azure Application Insights のクイック スタート | Microsoft docs"
description: "Application Insights で監視する Java Web アプリを迅速にセットアップする手順を説明します"
services: application-insights
keywords: 
author: mrbullwinkle
ms.author: mbullwin
ms.date: 09/10/2017
ms.service: application-insights
ms.custom: mvc
ms.topic: quickstart
manager: carmonm
ms.openlocfilehash: 3c56719d99a5a3cc3e650b72ecd2e92438533448
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="start-monitoring-your-java-web-application"></a>Java Web アプリケーションの監視を開始する

Azure Application Insights を使うと、Web アプリケーションの可用性、パフォーマンス、利用状況を簡単に監視できます。 アプリケーションのエラーを、ユーザーからの報告を待つことなく、迅速に特定して診断することもできます。 Application Insights の Java SDK では、MongoDB、MySQL、Redis を含む一般的なサードパーティ製パッケージを監視できます。

このクイック スタートでは、既存の Java Dynamic Web プロジェクトに Application Insights SDK を追加する方法を説明します。

## <a name="prerequisites"></a>前提条件

このクイック スタートを完了するには、以下が必要です。

- Oracle JRE 1.6 以降、または Zulu JRE 1.6 以降をインストールする
- [無料の Eclipse IDE for Java EE Developers](http://www.eclipse.org/downloads/) をインストールします。 このクイック スタートでは Eclipse Oxygen (4.7) を使用します。
- Azure サブスクリプションと既存の Java Dynamic Web プロジェクトが必要です。
 
Java Dynamic Web プロジェクトをお持ちでない場合は、[Java Web アプリ作成のクイック スタート](https://docs.microsoft.com/en-us/azure/app-service-web/app-service-web-get-started-java)に従って作成できます。

Azure サブスクリプションをお持ちでない場合は、開始する前に[無料](https://azure.microsoft.com/free/)アカウントを作成してください。

## <a name="log-in-to-the-azure-portal"></a>Azure ポータルにログインする

[Azure Portal](https://portal.azure.com/) にログインします。

## <a name="enable-application-insights"></a>Application Insights を有効にする

Application Insights は、オンプレミスとクラウドのどちらで実行されているかに関係なく、インターネットに接続された任意のアプリケーションからテレメトリ データを収集できます。 このデータの表示を開始するには、次の手順を実行します。

1. **[新規]** > **[監視 + 管理]** > **[Application Insights]** を選択します。

   ![Application Insights リソースの追加](./media/app-insights-java-quick-start/001-j.png)

   構成ボックスが表示されたら、下の表を使用して入力フィールドに入力します。

    | 設定        | 値           | Description  |
   | ------------- |:-------------|:-----|
   | **名前**      | グローバルに一意の値 | 監視しているアプリを識別する名前 |
   | **アプリケーションの種類** | Java Web アプリケーション | 監視しているアプリの種類 |
   | **リソース グループ**     | myResourceGroup      | App Insights データをホストする新しいリソース グループの名前 |
   | **場所** | 米国東部 | 近くにある場所か、アプリがホストされている場所の近くを選択します。 |

2. **Create** をクリックしてください。

## <a name="install-app-insights-plugin"></a>App Insights プラグインをインストールする

1. **Eclipse** を起動し、**[Help]** > **[Install New Software]** を選択します。

   ![新しい App Insights リソースのフォーム](./media/app-insights-java-quick-start/000-j.png)

2. ```http://dl.microsoft.com/eclipse``` を "Work With" フィールドにコピーし、**[Azure Toolkit for Java]** チェックボックスをオンにした後、**[Applicatin Insights Plugin for Java] を選択し**  > **[Contact all update sites during install to find required software]** チェックボックスをオフにします。

3. インストールが完了すると、**Eclipse を再起動**するように求められます。

## <a name="configure-app-insights-plugin"></a>App Insights プラグインを構成する

1. **Eclipse** を起動し、**プロジェクト**を開いた後、**Project Explorer** でプロジェクト名を右クリックし、**[Azure]** を選択してから **[Sign In]** をクリックします。

2. 認証方法に **[Interactive]** を選択して **[Sign In]** をクリックした後、プロンプトが表示されたら **Azure の資格情報**を入力し、**Azure サブスクリプション**を選択します。

3. **Project Explorer** でプロジェクト名を右クリックし、**[Azure]** を選択した後、**[Configure Application Insights]** をクリックします。

4. **[Enable telemetry with Application Insights]** チェックボックスをオンにし、Java アプリにリンクする App Insights リソースおよび関連する**インストルメンテーション キー**を選択します。

   ![Eclipse の Azure 構成メニュー](./media/app-insights-java-quick-start/0007-j.png)

> [!NOTE]
> Application Insights SDK for Java はライブ メトリックをキャプチャして視覚化できますが、テレメトリの収集を初めて有効にした場合は、ポータルでデータの表示が開始されるまで数分かかる可能性があります。 このアプリがトラフィックの少ないテスト アプリである場合、ほとんどのメトリックはアクティブな要求や操作がある場合にのみキャプチャされることに留意してください。

## <a name="start-monitoring-in-the-azure-portal"></a>Azure Portal で監視を開始する

1. Azure Portal で、インストルメンテーション キーを取得した Application Insights の**概要**ページを再度開き、現在実行中のアプリケーションに関する詳細情報を表示できます。

   ![Application Insights の概要メニュー](./media/app-insights-java-quick-start/0008-j.png)

2. **[App map (アプリ マップ)]** をクリックして、アプリケーション コンポーネント間の依存関係の視覚的レイアウトを取得します。 各コンポーネントには、負荷、パフォーマンス、障害、アラートなどの KPI が表示されます。

   ![アプリケーション マップ](./media/app-insights-java-quick-start/005-j.png)

3. **[アプリ分析]** アイコンをクリックし、![[アプリケーション マップ]](./media/app-insights-java-quick-start/006.png) アイコンをクリックします。 これにより、Application Insights で収集されたすべてのデータを分析するための多機能なクエリ言語を利用可能な **Application Insights Analytics** が開きます。 この場合は、要求の数をグラフとして描画するクエリが生成されます。 自分でクエリを作成して他のデータを分析することができます。

   ![一定の期間にわたるユーザー要求に関する Analytics グラフ](./media/app-insights-java-quick-start/0010-j.png)

4. **概要**ページに戻り、**正常性の概要のタイムライン**を確認します。  このダッシュ ボードでは、着信要求の数、要求の期間、発生したエラーなど、アプリケーションの正常性に関する統計情報が提供されます。

   ![正常性の概要のタイムライン グラフ](./media/app-insights-java-quick-start/0009-j.png)

   **[ページ ビューの読み込み時間]** グラフに**クライアント側のテレメトリ** データを入力できるようにするには、このスクリプトを追跡する各ページに追加します。

   ```HTML
   <!-- 
   To collect end-user usage analytics about your application, 
   insert the following script into each page you want to track.
   Place this code immediately before the closing </head> tag,
   and before any other scripts. Your first data will appear 
   automatically in just a few seconds.
   -->
   <script type="text/javascript">
     var appInsights=window.appInsights||function(config){
     function i(config){t[config]=function(){var i=arguments;t.queue.push(function(){t[config].apply(t,i)})}}var t={config:config},u=document,e=window,o="script",s="AuthenticatedUserContext",h="start",c="stop",l="Track",a=l+"Event",v=l+"Page",y=u.createElement(o),r,f;y.src=config.url||"https://az416426.vo.msecnd.net/scripts/a/ai.0.js";u.getElementsByTagName(o)[0].parentNode.appendChild(y);try{t.cookie=u.cookie}catch(p){}for(t.queue=[],t.version="1.0",r=["Event","Exception","Metric","PageView","Trace","Dependency"];r.length;)i("track"+r.pop());return i("set"+s),i("clear"+s),i(h+a),i(c+a),i(h+v),i(c+v),i("flush"),config.disableExceptionTracking||(r="onerror",i("_"+r),f=e[r],e[r]=function(config,i,u,e,o){var s=f&&f(config,i,u,e,o);return s!==!0&&t["_"+r](config,i,u,e,o),s}),t
    }({
        instrumentationKey:"<instrumentation key>"
    });

    window.appInsights=appInsights;
    appInsights.trackPageView();
   </script>
    ```

5. **[ライブ ストリーム]** をクリックします。 ここで、Java Web アプリのパフォーマンスに関連するライブ メトリックを見つけます。 **Live Metrics Stream** には、着信要求の数、要求の期間、発生したエラーなどのデータが含まれます。 また、プロセッサやメモリなどの重要なパフォーマンス メトリックをリアルタイムで監視することもできます。

   ![サーバー メトリックのグラフ](./media/app-insights-java-quick-start/livemetricsjava.png)

Java の監視に関する詳細については、[App Insights Java の追加ドキュメント](.\app-insights-java-get-started.md)を参照してください。

## <a name="clean-up-resources"></a>リソースのクリーンアップ

引き続きクイック スタートまたはチュートリアルの作業を行う場合は、このクイック スタートで作成したリソースをクリーンアップしないでください。 これ以上作業を行わない場合は、以下の手順に従い、このクイック スタートで作成したすべてのリソースを Azure Portal で削除してください。

1. Azure Portal の左側のメニューから、**[リソース グループ]**、**[myResourceGroup]** の順にクリックします。
2. リソース グループのページで **[削除]** をクリックし、テキスト ボックスに「**myResourceGroup**」と入力してから **[削除]** をクリックします。

## <a name="next-steps"></a>次のステップ

> [!div class="nextstepaction"]
> [パフォーマンスの問題の特定と診断](https://docs.microsoft.com/en-us/azure/application-insights/app-insights-analytics)