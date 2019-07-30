---
title: クイック スタート:Java Web アプリの監視
description: Application Insights で監視する Java Web アプリを迅速にセットアップする手順を説明します
services: application-insights
keywords: ''
author: mrbullwinkle
ms.author: mbullwin
ms.reviewer: lagayhar
ms.date: 07/15/2019
ms.service: application-insights
ms.custom: mvc, seo-java-july2019
ms.topic: quickstart
manager: carmonm
ms.openlocfilehash: 160c0bd90e2e733cd2d57798c0e80f2df569cf0a
ms.sourcegitcommit: bafb70af41ad1326adf3b7f8db50493e20a64926
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/25/2019
ms.locfileid: "68489750"
---
# <a name="start-monitoring-your-java-web-application"></a>Java Web アプリケーションの監視を開始する

Azure Application Insights を使うと、Web アプリケーションの可用性、パフォーマンス、利用状況を簡単に監視できます。 アプリケーションのエラーを、ユーザーからの報告を待つことなく、迅速に特定して診断することもできます。 Application Insights の Java SDK では、MongoDB、MySQL、Redis を含む一般的なサードパーティ製パッケージを監視できます。

このクイック スタートでは、既存の Java Dynamic Web プロジェクトに Application Insights SDK を追加する方法を説明します。

## <a name="prerequisites"></a>前提条件

このクイック スタートを完了するには、以下が必要です。

- JRE 1.7 または 1.8 のインストール
- [無料の Eclipse IDE for Java EE Developers](https://www.eclipse.org/downloads/) をインストールします。 このクイック スタートでは Eclipse Oxygen (4.7) を使用します。
- Azure サブスクリプションと既存の Java Dynamic Web プロジェクトが必要です。
 
Java Dynamic Web プロジェクトをお持ちでない場合は、[Java Web アプリ作成のクイック スタート](https://docs.microsoft.com/azure/app-service-web/app-service-web-get-started-java)に従って作成できます。

Azure サブスクリプションをお持ちでない場合は、開始する前に[無料](https://azure.microsoft.com/free/)アカウントを作成してください。

Spring フレームワークの方がよければ、[Spring Boot 初期化子アプリを構成して Application Insights ガイドを使用](https://docs.microsoft.com/java/azure/spring-framework/configure-spring-boot-java-applicationinsights)してみてください

## <a name="sign-in-to-the-azure-portal"></a>Azure portal にサインインします

[Azure Portal](https://portal.azure.com/) にサインインします。

## <a name="enable-application-insights"></a>Application Insights を有効にする

Application Insights は、オンプレミスとクラウドのどちらで実行されているかに関係なく、インターネットに接続された任意のアプリケーションからテレメトリ データを収集できます。 このデータの表示を開始するには、次の手順を実行します。

1. **[リソースの作成]**  >  **[開発者ツール]**  >  **[Application Insights]** の順に選択します。

   ![Application Insights リソースの追加](./media/java-quick-start/1createresourseappinsights.png)

   構成ボックスが表示されたら、次の表を使用して入力フィールドに入力します。

    | 設定        | 値           | 説明  |
   | ------------- |:-------------|:-----|
   | **Name**      | グローバルに一意の値 | 監視しているアプリを識別する名前 |
   | **リソース グループ**     | myResourceGroup      | App Insights データをホストする新しいリソース グループの名前 |
   | **Location** | East US | 近くにある場所か、アプリがホストされている場所の近くを選択します。 |

2. **Create** をクリックしてください。

## <a name="install-app-insights-plugin"></a>App Insights プラグインをインストールする

1. **Eclipse** を起動し、 **[Help]** > **[Install New Software]** を選択します。

   ![新しい App Insights リソースのフォーム](./media/java-quick-start/000-j.png)

2. ```https://dl.microsoft.com/eclipse``` を "Work With" フィールドにコピーし、 **[Azure Toolkit for Java]** チェックボックスをオンにした後、 **[Applicatin Insights Plugin for Java] を選択し**  >  **[Contact all update sites during install to find required software]** チェックボックスをオフにします。

3. インストールが完了すると、**Eclipse を再起動**するように求められます。

## <a name="configure-app-insights-plugin"></a>App Insights プラグインを構成する

1. **Eclipse** を起動し、**プロジェクト**を開いた後、**Project Explorer** でプロジェクト名を右クリックし、 **[Azure]** を選択してから **[Sign In]** をクリックします。

2. 認証方法に **[Interactive]** を選択して **[Sign In]** をクリックした後、プロンプトが表示されたら **Azure の資格情報**を入力し、**Azure サブスクリプション**を選択します。

3. **Project Explorer** でプロジェクト名を右クリックし、 **[Azure]** を選択した後、 **[Configure Application Insights]** をクリックします。

4. **[Enable telemetry with Application Insights]** チェックボックスをオンにし、Java アプリにリンクする App Insights リソースおよび関連する**インストルメンテーション キー**を選択します。

   ![Eclipse の Azure 構成メニュー](./media/java-quick-start/0007-j.png)

5. Application Insights プラグインを構成した後、テレメトリの送信を始める前に、アプリケーションをもう一度[発行/再発行](https://docs.microsoft.com/azure/app-service/app-service-web-get-started-java#deploy-the-app)する必要があります。

> [!NOTE]
> Application Insights SDK for Java はライブ メトリックをキャプチャして視覚化できますが、テレメトリの収集を初めて有効にした場合は、ポータルでデータの表示が開始されるまで数分かかる可能性があります。 このアプリがトラフィックの少ないテスト アプリである場合、ほとんどのメトリックはアクティブな要求や操作がある場合にのみキャプチャされることに留意してください。

## <a name="start-monitoring-in-the-azure-portal"></a>Azure Portal で監視を開始する

1. Azure portal で Application Insights の **[概要]** ページを再度開き、現在実行中のアプリケーションに関する詳細情報を表示できます。

   ![Application Insights の概要メニュー](./media/java-quick-start/3overview.png)

2. **[アプリケーション マップ]** をクリックして、アプリケーション コンポーネント間の依存関係の視覚的レイアウトを取得します。 各コンポーネントには、負荷、パフォーマンス、障害、アラートなどの KPI が表示されます。

   ![アプリケーション マップ](./media/java-quick-start/4appmap.png)

3.  **[アプリ分析]** アイコン ![[アプリケーション マップ] アイコン](./media/java-quick-start/006.png) **[Analytics 内のビュー]** をクリックします。  これにより、Application Insights で収集されたすべてのデータを分析するための豊富なクエリ言語を備えた **Application Insights 分析**が開きます。 この場合は、要求の数をグラフとして描画するクエリが生成されます。 自分でクエリを作成して他のデータを分析することができます。

   ![一定の期間にわたるユーザー要求に関する Analytics グラフ](./media/java-quick-start/5analytics.png)

4. **[概要]** ページに戻って KPI グラフを観察します。 このダッシュ ボードでは、着信要求の数、要求の期間、発生したエラーなど、アプリケーションの正常性に関する統計情報が提供されます。

   ![正常性の概要のタイムライン グラフ](./media/java-quick-start/6kpidashboards.png)

   **[ページ ビューの読み込み時間]** グラフに**クライアント側のテレメトリ** データを入力できるようにするには、このスクリプトを追跡する各ページに追加します。

   ```HTML
   <!-- 
   To collect user behavior analytics about your application, 
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

   ![サーバー メトリックのグラフ](./media/java-quick-start/7livemetrics.png)

Java の監視に関する詳細については、[App Insights Java の追加ドキュメント](./../../azure-monitor/app/java-get-started.md)を参照してください。

## <a name="clean-up-resources"></a>リソースのクリーンアップ

テストが完了したら、リソース グループとすべての関連リソースを削除できます。 これを行うには、次の手順に従います。

1. Azure Portal の左側のメニューから、 **[リソース グループ]** 、 **[myResourceGroup]** の順にクリックします。
2. リソース グループのページで **[削除]** をクリックし、テキスト ボックスに「**myResourceGroup**」と入力してから **[削除]** をクリックします。

## <a name="next-steps"></a>次の手順

> [!div class="nextstepaction"]
> [パフォーマンスの問題の特定と診断](https://docs.microsoft.com/azure/application-insights/app-insights-analytics)
