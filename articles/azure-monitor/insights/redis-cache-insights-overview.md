---
title: Azure Monitor for Azure Cache for Redis | Microsoft Docs
description: この記事では、キャッシュ所有者がパフォーマンスと使用状況の問題をすばやく把握できる Azure Monitor for Azure Redis Cache の機能について説明します。
ms.topic: conceptual
author: lgayhardt
ms.author: lagayhar
ms.date: 09/10/2020
ms.openlocfilehash: fee454073c50b9542e140576ef0629a39b8f4294
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "100573461"
---
# <a name="explore-azure-monitor-for-azure-cache-for-redis"></a>Azure Monitor for Azure Cache for Redis を調べる

Azure Monitor for Azure Cache for Redis では、Azure Cache for Redis リソースのすべてに対して、次のような統合された対話型ビューが用意されています。

- 全体的なパフォーマンス
- エラー
- 容量
- 運用の正常性

この記事は、この新しい監視エクスペリエンスのメリットについて説明します。 また、組織の独自のニーズに合わせて、エクスペリエンスを変更および適応させる方法についても説明します。

## <a name="introduction"></a>はじめに

エクスペリエンスを開始する前に、Azure Monitor for Azure Cache for Redis が視覚的に情報を表示する方法について理解しておく必要があります。

次のことが実現されます。

- **大規模な分析観点**: すべてのサブスクリプションにわたる Azure Cache for Redis リソースを 1 か所に表示します。 評価するサブスクリプションとリソースのみに選択的にスコープを設定できます。

- **ドリルダウン分析**: 特定の Azure Cache for Redis リソースが表示されます。 問題を診断し、使用状況、エラー、容量、および操舵の詳細な分析情報を確認できます。 これらのカテゴリのいずれかを選択すると、関連する情報の詳細なビューが表示されます。  

- このエクスペリエンスの **カスタマイズ**: Azure Monitor ブック テンプレートの上に構築されます。 エクスペリエンスでは、表示されるメトリックを変更したり、制限に合わせてしきい値を変更または設定したりできます。 カスタム ブックに変更を保存し、ブック グラフを Azure ダッシュボードにピン留めすることができます。

この機能では、何も有効にしたり、構成したりする必要はありません。 Azure Cache for Redis 情報は、既定で収集されます。

>[!NOTE]
>この機能へのアクセスには料金はかかりません。 構成または有効にした Azure Monitor の基本機能に対してのみ課金されます。[Azure Monitor の価格の詳細](https://azure.microsoft.com/pricing/details/monitor/)に関するページをご覧ください。

## <a name="view-utilization-and-performance-metrics-for-azure-cache-for-redis"></a>Azure Cache for Redis の使用率とパフォーマンスのメトリックを表示する

所有するすべてのサブスクリプションのストレージ アカウントの使用状況とパフォーマンスを確認するには、次の手順を実行します。

1. [Azure portal](https://portal.azure.com) にサインインします。

1. 「**監視**」を検索し、 **[監視]** を選択します。

    ![「監視」の語が入力された検索ボックスと、スピードメーター画像とともに「監視」を示した検索結果](./media/cosmosdb-insights-overview/search-monitor.png)

1. **[Azure Cache for Redis]** を選択します。 このオプションが存在しない場合は、 **[詳細]**  >  **[Azure Cache for Redis]** を選択します。

### <a name="overview"></a>概要

**[概要]** のテーブルには、Azure Cache for Redis の対話型メトリックが表示されます。 次のドロップダウン リストから選択するオプションに基づいて、結果をフィルター処理できます。

- **サブスクリプション**:Azure Cache for Redis リソースを含むサブスクリプションのみが一覧表示されます。  

- **Azure Cache for Redis**:すべての Azure Cache for Redis リソースか、そのサブセット、またはいずれか 1 つを選択できます。

- **時間範囲**:既定では、選択結果に応じて過去 4 時間の情報が表示されます。

ドロップダウン リストの下にカウンター タイルがあります。 このタイルには、選択したサブスクリプションの Azure Cache for Redis リソースの合計数が表示されます。 ブック列では、条件に基づくカラー コードまたはヒート マップにより、トランザクションのメトリックがレポートされます。 最も濃い色は最大値を表します。 色が薄くなるほど低い値を表します。

いずれかの Azure Cache for Redis リソースの横にあるドロップダウン リストの矢印を選択すると、個々のリソース レベルでパフォーマンス メトリックの内訳が表示されます。

![概要エクスペリエンスのスクリーンショット](./media/redis-cache-insights-overview/overview.png)

青色で強調表示されている Azure Cache for Redis リソース名を選択すると、関連付けられているアカウントの既定の **[概要]** テーブルが表示されます。 次の列が表示されます。

- **メモリ使用量**
- **使用されているメモリの割合**
- **サーバーの負荷**
- **サーバーの負荷のタイムライン**
- **CPU**
- **接続されているクライアント数**
- **キャッシュ ミス数**
- **エラー (最大)**

### <a name="operations"></a>操作

ページの上部にある **[操作]** を選択すると、ブック テンプレートの **[操作]** テーブルが開きます。 次の列が表示されます。

- **合計処理数**
- **合計処理数のタイムライン**
- **1 秒あたりの操作回数**
- **取得数**
- **設定数**

![概要エクスペリエンスのスクリーンショット](./media/redis-cache-insights-overview/operations.png)

### <a name="usage"></a>使用法

ページの上部にある **[使用法]** を選択すると、ブック テンプレートの **[使用法]** テーブルが開きます。 次の列が表示されます。

- **キャッシュの読み取り**
- **キャッシュの読み取りのタイムライン**
- **キャッシュの書き込み**
- **キャッシュ ヒット数**
- **キャッシュ ミス数**

![使用法エクスペリエンスのスクリーンショット](./media/redis-cache-insights-overview/usage.png)

### <a name="failures"></a>エラー

ページの上部にある **[エラー]** を選択すると、ブック テンプレートの **[エラー]** テーブルが開きます。 次の列が表示されます。

- **合計エラー数**
- **フェールオーバー/エラー数**
- **UnresponsiveClient/エラー数**
- **RDB/エラー数**
- **AOF/エラー数**
- **エクスポート/エラー数**
- **データ損失/エラー数**
- **インポート/エラー数**

![HTTP 要求タイプごとの内訳を含むエラーのスクリーンショット](./media/redis-cache-insights-overview/failures.png)

### <a name="metric-definitions"></a>メトリック定義

これらのブックを形成するメトリック定義の完全な一覧については、[使用可能なメトリックとレポート間隔](../../azure-cache-for-redis/cache-how-to-monitor.md#available-metrics-and-reporting-intervals)に関するページをご覧ください。

## <a name="view-from-an-azure-cache-for-redis-resource"></a>Azure Cache for Redis リソースから表示する

個々のリソースから直接 Azure Monitor for Azure Cache for Redis にアクセスするには、次の操作を実行します。

1. Azure portal で、[Azure Cache for Redis] を選択します。

2. 一覧から、個々の Azure Cache for Redis リソースを選択します。 [監視] セクションで、[分析情報] を選択します。

    ![赤色のボックスで強調表示された "分析情報" という単語を含むメニュー オプションのスクリーンショット](./media/redis-cache-insights-overview/insights.png)

これらのビューには、Azure Monitor レベルのブックから Azure Cache for Redis リソースのリソース名を選択してアクセスすることもできます。

### <a name="resource-level-overview"></a>リソースレベルの概要

Azure Redis Cache の **[概要]** ブックには、複数のパフォーマンス メトリックが表示され、次の情報にアクセスできます。

- 対話型のパフォーマンス グラフ。Azure Cache for Redis パフォーマンスに関連する、最も重要な詳細情報が示されます。

- シャードのパフォーマンス、接続されているクライアントの合計数、および全体の待機時間を強調表示したメトリックとステータス タイル。

![CPU パフォーマンス、使用メモリ、接続されているクライアント、エラー、期限切れのキー、および削除されたキーに関する情報を表示する概要ダッシュボードのスクリーンショット](./media/redis-cache-insights-overview/resource-overview.png)

**[パフォーマンス]** または **[操作]** の他のタブのいずれかを選択すると、それぞれのブックが開きます。

### <a name="resource-level-performance"></a>リソース レベルのパフォーマンス

![リソース パフォーマンス グラフのスクリーンショット](./media/redis-cache-insights-overview/resource-performance.png)

### <a name="resource-level-operations"></a>リソース レベルの操作

![リソース操作グラフのスクリーンショット](./media/redis-cache-insights-overview/resource-operations.png)

## <a name="pin-export-and-expand"></a>ピン留め、エクスポート、展開

[Azure ダッシュボード](../../azure-portal/azure-portal-dashboards.md)にメトリック セクションをピン留めするには、セクションの右上にある画びょう記号を選択します。

![画びょう記号が強調表示されているメトリック セクション](./media/cosmosdb-insights-overview/pin.png)

データを Excel 形式にエクスポートするには、画びょう記号の左側にある下矢印記号を選択します。

![強調表示されたエクスポート ブックの記号](./media/cosmosdb-insights-overview/export.png)

ブックのすべてのビューを展開したり折りたたんだりするには、エクスポート記号の左側の展開記号を選択します。

![強調表示された拡張ブック記号](./media/cosmosdb-insights-overview/expand.png)

## <a name="customize-azure-monitor-for-azure-cache-for-redis"></a>Azure Monitor for Azure Cache for Redis をカスタマイズする

このエクスペリエンスは、Azure Monitor ブック テンプレートをベースに作成されているため、 **[カスタマイズ]**  >  **[編集]**  >  **[保存]** の順に選択して、変更したバージョンのコピーをカスタム ブックに保存することができます。

![[カスタマイズ] が強調表示されたコマンド バー](./media/cosmosdb-insights-overview/customize.png)

ブックは、 **[個人用レポート]** セクションまたは **[共有レポート]** セクションのリソース グループ内に保存されます。 **個人用レポート** は自分だけが使用できます。 **共有レポート** は、リソース グループにアクセスできるすべてのユーザーが使用できます。

カスタム ブックを保存した後は、ブック ギャラリーに移動して開きます。

![[ギャラリー] が強調表示されたコマンド バー](./media/cosmosdb-insights-overview/gallery.png)

## <a name="troubleshooting"></a>トラブルシューティング

トラブルシューティングのガイダンスについては、専用のブックベースの分析情報の[トラブルシューティングの記事](troubleshoot-workbooks.md)を参照してください。

## <a name="next-steps"></a>次のステップ

* [メトリック アラート](../alerts/alerts-metric.md)と[サービス正常性通知](../../service-health/alerts-activity-log-service-notifications-portal.md)を構成して、問題の検出に役立つ自動アラートを設定します。

* ブックがサポートするシナリオ、レポートの作成方法とカスタマイズ方法などについては、「[Azure Monitor ブックを使用した対話型レポートの作成](../visualize/workbooks-overview.md)」で学習してください。
