---
title: "Azure Web アプリのパフォーマンスの監視 |Microsoft Docs"
description: "Azure Web アプリのアプリケーション パフォーマンスの監視。 チャートの読み込みおよび応答時間、依存関係の情報やパフォーマンス警告を設定します。"
services: application-insights
documentationcenter: .net
author: alancameronwills
manager: carmonm
ms.assetid: 0b2deb30-6ea8-4bc4-8ed0-26765b85149f
ms.service: azure-portal
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/14/2017
ms.author: awills
translationtype: Human Translation
ms.sourcegitcommit: fd35f1774ffda3d3751a6fa4b6e17f2132274916
ms.openlocfilehash: d7a8d669e75991f6707641e4ce53c9b63d04853c
ms.lasthandoff: 03/16/2017


---
# <a name="monitor-azure-web-app-performance"></a>Azure Web アプリのパフォーマンスの監視
[Azure Portal](https://portal.azure.com) では、[Azure Web アプリ](../app-service-web/app-service-web-overview.md)のアプリケーション パフォーマンス監視を設定できます。 [Azure Application Insights](app-insights-overview.md) は、アクティビティに関するテレメトリを Application Insights サービスに送信するようにアプリをインストルメント化します。これにより、Application Insights サービスでテレメトリを保存および分析できるようになります。 Application Insights では、メトリック グラフや検索ツールを使用して、問題の診断、パフォーマンスの改善、使用状況の評価などを行うことができます。

## <a name="run-time-or-build-time"></a>実行時またはビルド時
監視は、アプリを次の 2 つの方法のどちらかでインストルメント化することによって構成できます。

* **実行時** - Web アプリが既に実行されているときにパフォーマンス監視拡張機能を選択できます。 アプリを再構築または再インストールする必要はありません。 応答時間、成功率、例外、依存関係などを監視するパッケージの標準セットを利用できます。 
* **ビルド時** - 開発時にアプリにパッケージをインストールすることができます。 これは、汎用性が高い方法です。 同じ標準パッケージに加えて、コードを記述してテレメトリをカスタマイズしたり、独自のテレメトリを送信したりすることができます。 アプリのドメインのセマンティクスに従って、特定のアクティビティをログに記録したり、イベントを記録したりすることができます。 

## <a name="run-time-instrumentation-with-application-insights"></a>Application Insights での実行時のインストルメント化
Azure で Web アプリを既に実行している場合、要求率とエラー発生率を既に監視しています。 応答時間、依存関係の呼び出しの監視、スマート検出、強力な Analytics クエリ言語など、さらに多くの機能を利用するには、Application Insights を追加します。 

1. Web アプリの Azure コントロール パネルで **[Application Insights] を選択**します。
   
    ![[監視] の [Application Insights] を選択する](./media/app-insights-azure-web-apps/05-extend.png)
   
   * このアプリの Application Insights リソースを別のルートで既に設定している場合を除き、新しいリソースの作成を選択します。
2. Application Insights をインストールしたら、**Web アプリをインストルメント化**します。 
   
    ![Web アプリをインストルメント化する](./media/app-insights-azure-web-apps/restart-web-app-for-insights.png)
3. **アプリを監視**し、  [データを探索](#explore-the-data)します。

後で必要に応じて、Application Insights でアプリをビルドして再デプロイできます。

*Application Insights を削除するか、送信先を別のリソースに切り替えるにはどうすればよいですか。*

* Azure で Web アプリの制御ブレードを開き、[開発ツール] の **[拡張機能]** を開きます。 Application Insights 拡張機能を削除します。 次に、[監視] の [Application Insights] を選択し、目的のリソースを作成または選択します。

## <a name="build-the-app-with-application-insights"></a>Application Insights でのアプリのビルド
Application Insights では、アプリへの SDK のインストールによって、より詳細なテレメトリを提供できます。 具体的には、トレース ログの収集、[カスタム テレメトリの作成](app-insights-api-custom-events-metrics.md)、より詳細な例外レポートの取得が可能です。

1. **Visual Studio** (2013 Update 2 以降) で、Application Insights SDK をプロジェクト用に構成します。
   
    ![Web プロジェクトを右クリックし、Application Insights の追加または構成を選択する](./media/app-insights-azure-web-apps/03-add.png)
   
    サインインが要求されたら、Azure アカウントの資格情報を使用します。
   
    この操作には、次の 2 つの効果があります。
   
   1. Azure に Application Insights リソースが作成されます。このリソースでテレメトリが格納、分析、表示されます。
   2. Application Insights NuGet パッケージがコードに追加され (まだ追加されていない場合)、テレメトリを Azure リソースに送信するように構成されます。
2. 開発用コンピューターでアプリを実行して (F5)、**テレメトリをテスト**します。
3. 通常の方法で Azure に**アプリを発行**します。 

*送信先を別の Application Insights リソースに切り替えるにはどうすればよいですか。*

* Visual Studio でプロジェクトを右クリックし、**[Application Insights の構成]** を選択して、目的のリソースを選択します。 新しいリソースを作成するオプションが表示されます。 リビルドして再デプロイします。

## <a name="explore-the-data"></a>データを検索する
1. Web アプリのコントロール パネルの Application Insights ブレードで Live Metrics を確認します。Live Metrics には、要求と失敗が発生後 1 ～ 2 秒以内に表示されます。 問題をすぐに確認できるため、この表示はアプリを再発行する際に非常に役立ちます。
2. すべての Application Insights リソースをクリックします。

    ![クリックする](./media/app-insights-azure-web-apps/view-in-application-insights.png)

    Azure リソースのナビゲーションから直接移動することもできます。

1. 任意のグラフをクリックすると、より詳細な情報が表示されます。
   
    ![On the Application Insights overview blade, click a chart](./media/app-insights-azure-web-apps/07-dependency.png)
   
    [メトリック ブレードをカスタマイズする](app-insights-metrics-explorer.md)ことができます。
2. 個々のイベントとそのプロパティを表示するには、さらにクリックします。
   
    ![Click an event type to open a search filtered on that type](./media/app-insights-azure-web-apps/08-requests.png)
   
    "..." というリンクを使用すると、すべてのプロパティが開かれます。
   
    [検索をカスタマイズ](app-insights-diagnostic-search.md)できます。

テレメトリに対するより強力な検索については、 [Analytics クエリ言語](app-insights-analytics-tour.md)を使用してください。

## <a name="video"></a>ビデオ

> [!VIDEO https://channel9.msdn.com/events/Connect/2016/100/player]

## <a name="next-steps"></a>次のステップ
* [Azure 診断](app-insights-azure-diagnostics.md) が Application Insights に送信されるように設定します。
* [サービスの正常性メトリックを監視](../monitoring-and-diagnostics/insights-how-to-customize-monitoring.md)して、サービスの可用性と応答性を確認します。
* 操作イベントが発生したり、メトリックがしきい値を超えたりするたびに、[アラート通知を受け取り](../monitoring-and-diagnostics/insights-receive-alert-notifications.md)ます。
* [JavaScript のアプリや Web ページに Application Insights](app-insights-web-track-usage.md) を使用して、Web ページを参照しているブラウザーからクライアント テレメトリを取得します。
* [可用性 Web テストを設定](app-insights-monitor-web-app-availability.md) して、サイトがダウンした場合にアラートを送信するようにします。


