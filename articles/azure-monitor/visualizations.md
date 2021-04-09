---
title: Azure Monitor からのデータを視覚化する | Microsoft Docs
description: Azure Monitor に格納されたメトリックとログ データを視覚化するために使用可能な方法の概要を提供します。
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 01/25/2021
ms.openlocfilehash: b90d628f0d24e43d7b9f2e3fa87e74d426648c6e
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102048575"
---
# <a name="visualizing-data-from-azure-monitor"></a>Azure Monitor からのデータを視覚化する
この記事では、Azure Monitor に格納されたログとメトリック データを視覚化するために使用可能な方法の概要を提供します。

チャートやグラフなどの視覚化は、監視データを分析して問題をドリルダウンし、パターンを特定するのに役立ちます。 使用するツールによっては、組織の内外の他のユーザーと視覚化を共有するオプションもあります。

## <a name="workbooks"></a>Workbooks
[Workbooks](./visualize/workbooks-overview.md) は、データの詳細な分析情報、調査、およびチーム内のコラボレーションを提供する対話型のドキュメントです。 Workbooks が役立つ具体的な例は、トラブルシューティング ガイドとインシデントの事後分析です。

![図には、ページ ビューの分析、使用状況、ページで費やされた時間を含む、ブックからのいくつかのページのスクリーンショットが示されています。](media/visualizations/workbook.png)

### <a name="advantages"></a>長所
- メトリックとログの両方をサポート。
- テーブル内の要素を選択すると、関連付けられたグラフと視覚化が動的に更新される、対話型レポートを有効にするパラメーターをサポート。
- ドキュメントのようなフロー。
- 個人または共有の Workbooks のオプション。
- 簡単でコラボレーションしやすい作成エクスペリエンス。
- テンプレートでの GitHub ベースのパブリック テンプレート ギャラリーのサポート。

### <a name="limitations"></a>制限事項
- 自動更新なし。
- ダッシュボードのような密度の高いレイアウトがなく、Workbooks が 1 つのウィンドウとして表示されるため、利便性には劣りますが、 詳細な分析情報を提供することを目的としています。


## <a name="azure-dashboards"></a>Azure ダッシュボード
[Azure ダッシュボード](../azure-portal/azure-portal-dashboards.md)は、Azure の主要なダッシュボード テクノロジです。 ダッシュボードは、重要な問題をすばやく特定できるように、Azure インフラストラクチャとサービスに関する 1 つのウィンドウを提供するという点で特に役立ちます。

![スクリーンショットには、Azure ダッシュボードの例とカスタマイズ可能な情報が表示されています。](media/visualizations/dashboard.png)

ダッシュボードの作成に関するビデオ チュートリアルはこちらです。

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE4AslH]

### <a name="advantages"></a>長所
- Azure との緊密な統合。 視覚化は、[メトリックス エクスプローラー](essentials/metrics-charts.md)、[Log Analytics](logs/log-analytics-overview.md)、[Application Insights](app/app-insights-overview.md) など、複数の Azure ページからダッシュボードにピン留めできます。
- メトリックとログの両方をサポート。
- [メトリックス エクスプローラー](essentials/metrics-charts.md)、[ログ クエリ](logs/log-query-overview.md)、[Application Insights](app/app-insights-overview.md) の[マップ](app/app-map.md)と可用性からの出力など、複数のソースからのデータを結合します。
- 個人または共有のダッシュボードのオプション。 [Azure ロールベースのアクセス制御 (Azure RBAC)](../role-based-access-control/overview.md) と統合。
- 自動更新。 メトリックの更新は、最短 5 分の時間範囲に依存します。 特定の視覚エフェクトの [更新] アイコンをクリックするか、ダッシュボード全体を更新することにより、オンデマンドで手動更新オプションを使って 1 時間ごとに更新をログに記録します。
- タイムスタンプとカスタム パラメーターでパラメーター化されたメトリック ダッシュボード。
- 柔軟なレイアウト オプション。
- 全画面表示モード。


### <a name="limitations"></a>制限事項
- ログの視覚化に対する制御が限定的であり、データ テーブルはサポートされません。 データ系列の合計数は 50 に制限されていて、それ以上のデータ系列は "_その他_" のバケットの下にグループ化されます。
- ログのグラフに対するカスタム パラメーターのサポートはありません。
- ログのグラフは、過去 30 日間に制限されます。
- ログのグラフは、共有ダッシュボードにのみピン留めできます。
- ダッシュボード データとのインタラクティビティはありません。
- 制限されたコンテキストのドリルダウン。


## <a name="power-bi"></a>Power BI
[Power BI](https://powerbi.microsoft.com/documentation/powerbi-service-get-started/) は、ビジネス中心のダッシュボードとレポート、および長期的な KPI 傾向の分析レポートを作成するのに特に便利です。 [ログ クエリの結果を Power BI のデータセットにインポート](visualize/powerbi.md)できます。そうすることでさまざまなソースのデータを組み合わせたり、Web とモバイル デバイス上でレポートを共有したりするような機能を利用できます。

![Power BI](media/visualizations/power-bi.png)

### <a name="advantages"></a>長所
- 高度な視覚化。
- ズームインやクロスフィルタリングを含む広範なインタラクティビティ。
- 組織全体で共有が簡単。
- 複数のデータ ソースからの他のデータとの統合。
- キューブにキャッシュされる結果のパフォーマンスが向上。


### <a name="limitations"></a>制限事項
- ログはサポートされるがメトリックはサポートされない。
- Azure の統合はなし。 Azure Resource Manager からダッシュボードとモデルを管理できない。
- 構成するには、クエリ結果を Power BI モデルにインポートする必要がある。 結果のサイズと更新への制限。
- 1 日 8 回に制限されたデータ更新。


## <a name="grafana"></a>Grafana
[Grafana](https://grafana.com/) は、運用ダッシュボードに優れたオープン プラットフォームです。 運用上のインシデントの検出、分離、トリアージに特に役立ちます。 お使いの Azure サブスクリプションに [Grafana Azure Monitor データ ソース プラグイン](visualize/grafana-plugin.md)を追加することで、Azure のメトリック データを視覚化することができます。

![Grafana 視覚化のスクリーンショット。](media/visualizations/grafana.png)

### <a name="advantages"></a>長所
- 高度な視覚化。
- データ ソースの充実したエコシステム。
- ズーム インなどのデータ インタラクティビティ。
- パラメーターのサポート。

### <a name="limitations"></a>制限事項
- Azure の統合はなし。 Azure Resource Manager からダッシュボードとモデルを管理できない。
- 追加の Grafana インフラストラクチャをサポートするためのコストまたは Grafana クラウドの追加コスト。


## <a name="build-your-own-custom-application"></a>独自のカスタム アプリケーションのビルド
Azure Monitor のメトリック データとログ データには、その API を通じ、任意の REST クライアントを使用してアクセスできます。これにより、独自のカスタム Web サイトとアプリケーションを構築できます。

### <a name="advantages"></a>長所
- UI、視覚化、インタラクティビティ、および機能における完全な柔軟性。
- メトリックとログ データを他のデータ ソースと結合する。

### <a name="disadvantages"></a>短所
- 多くのエンジニア リング作業が必要。


## <a name="azure-monitor-views"></a>Azure Monitor ビュー

> [!IMPORTANT]
> ビューは今後非推奨となる予定です。 ビューをブックに変換する方法のガイドラインについては、「[Azure Monitor ビュー デザイナーからブックへの移行ガイド](visualize/view-designer-conversion-overview.md)」を参照してください。

[Azure Monitor のビュー](visualize/view-designer.md)では、ログ データを使用して、カスタム視覚化を作成できます。 これらのビューは、[監視ソリューション](insights/solutions.md)が収集したデータを表示するために使用します。


![スクリーンショットには、コンテナー監視ソリューション タイルと、選択すると開く詳細な Azure Monitor ビューが表示されています。](media/visualizations/view.png)

### <a name="advantages"></a>長所
- ログ データの高度な視覚化。
- ビューを他のリソース グループとサブスクリプションに転送するためのビューのエクスポートとインポート。
- ワークスペースと監視ソリューションを持つ Azure Monitor 管理モデルへの統合。
- カスタム パラメーターの[フィルター処理](visualize/view-designer-filters.md)。
- 対話型、複数レベルのドリルイン (別のビューに絞り込むビュー) をサポート

### <a name="limitations"></a>制限事項
- ログはサポートされるがメトリックはサポートされない。
- 個人用ビューはなし。 ワークスペースへのアクセス権を持つすべてのユーザーが使用可能。
- 自動更新なし。
- 制限されたレイアウト オプション。
- 複数のワークスペースや Application Insights アプリケーションに対する横断的なクエリ実行がサポートされない。
- クエリの応答サイズは 8 MB に制限され、クエリの実行時間は 110 秒に制限される。

## <a name="next-steps"></a>次のステップ
- [Azure Monitor によって収集されたデータ](data-platform.md)について学習します。
- [Azure ダッシュボード](../azure-portal/azure-portal-dashboards.md)について学習します。
- [メトリックス エクスプローラー](essentials/metrics-getting-started.md)について学習します。
- [Workbooks](./visualize/workbooks-overview.md) について学習します。
- [ログ データの Power BI へのインポート](./visualize/powerbi.md)について学習します。
- [Grafana Azure Monitor データ ソース プラグイン](./visualize/grafana-plugin.md)について学習します。
- [Azure Monitor のビュー](visualize/view-designer.md)について学習します。

