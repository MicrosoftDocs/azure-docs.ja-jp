---
title: 管理と監視 - クエリ アクティビティ、リソース使用状況
description: Azure Synapse Analytics の管理と監視に使用できる機能について説明します。 データ ウェアハウスのクエリ アクティビティとリソース使用状況を把握するには、Azure portal と動的管理ビュー (DMV) を使用します。
services: synapse-analytics
author: gaursa
manager: craigg-msft
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 04/09/2020
ms.author: gaursa
ms.reviewer: jrasnick
ms.custom: azure-synapse
ms.openlocfilehash: e7f093860e9962db055a7551f5cdb608ecad9b29
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "104585674"
---
# <a name="monitoring-resource-utilization-and-query-activity-in-azure-synapse-analytics"></a>Azure Synapse Analytics でのリソース使用状況とクエリ アクティビティの監視

Azure portal 内にある Azure Synapse Analytics のリッチな監視エクスペリエンスでは、データ ウェアハウスのワークロードに関する分析情報が表示されます。 データ ウェアハウスを監視するときの推奨されるツールである Azure portal では、構成可能なリテンション期間、アラート、推奨事項、およびメトリックとログのカスタマイズ可能なグラフとダッシュボードが提供されます。 ポータルでは、ログ分析を使用して Azure Monitor (ログ) などの他の Azure 監視サービスと統合して、お使いのデータ ウェアハウスだけでなく、統合された監視エクスペリエンスに対する Azure 分析プラットフォーム全体も含む、総合的な監視エクスペリエンスを提供することもできます。 このドキュメントでは、Synapse SQL で分析プラットフォームの最適化と管理に使用できる監視機能について説明します。

## <a name="resource-utilization"></a>リソース使用率

Azure portal では、Synapse SQL に対して以下のメトリックを使用できます。 これらのメトリックは、[Azure Monitor](../../azure-monitor/data-platform.md?bc=%2fazure%2fsynapse-analytics%2fsql-data-warehouse%2fbreadcrumb%2ftoc.json&toc=%2fazure%2fsynapse-analytics%2fsql-data-warehouse%2ftoc.json#metrics) を通じて表示できます。

| メトリックの名前             | 説明                                                  | 集計の種類 |
| ----------------------- | ------------------------------------------------------------ | ---------------- |
| CPU の割合          | データ ウェアハウスの全ノードでの CPU 使用率      | 平均、最小、最大    |
| データ IO の割合      | データ ウェアハウスの全ノードでの IO 使用率       | 平均、最小、最大    |
| メモリの割合       | データ ウェアハウスの全ノードでのメモリ使用率 (SQL Server) | 平均、最小、最大   |
| アクティブなクエリ          | システムで実行されているアクティブなクエリの数             | SUM              |
| キューに置かれたクエリ          | 実行の開始を待機している、キューに置かれたクエリの数          | SUM              |
| 成功した接続  | データベースに対する成功した接続 (ログイン) の数 | 合計、数       |
| 失敗した接続      | データベースに対する失敗した接続 (ログイン) の数 | 合計、数       |
| ファイアウォールによってブロックされる     | ブロックされたデータ ウェアハウスへのログインの数     | 合計、数       |
| DWU 上限               | データ ウェアハウスのサービス レベル目標                | 平均、最小、最大    |
| DWU の割合          | CPU の割合とデータ IO の割合の最大値        | 平均、最小、最大    |
| 使用済み DWU                | DWU の制限 * DWU の割合                                   | 平均、最小、最大    |
| キャッシュ ヒットの割合    | (キャッシュ ヒット数/キャッシュ ミス数) * 100: キャッシュ ヒット数はローカル SSD キャッシュでのすべての列ストア セグメント ヒット数の合計、キャッシュ ミス数はローカル SSD キャッシュでの列ストア セグメント ミス数を全ノードについて合計した値 | 平均、最小、最大    |
| 使用されたキャッシュの割合   | (使用されたキャッシュ/キャッシュ容量) * 100: 使用されたキャッシュは全ノードのローカル SSD キャッシュでの全バイト数の合計、キャッシュ容量は全ノードのローカル SSD キャッシュのストレージ容量の合計 | 平均、最小、最大    |
| ローカル tempdb の割合 | すべてのコンピューティング ノードでの、ローカル tempdb の使用率 - 値は 5 分ごとに出力されます | 平均、最小、最大    |

メトリックを表示してアラートを設定するときに考慮が必要な事項は次のとおりです。

- 使用される DWU は、SQL プール全体の **使用に関する高レベル表現** のみを表しており、使用率を包括的に示すものではありません。 スケールアップまたはスケールダウンするかどうかを判断するには、コンカレンシー、メモリ、tempdb、アダプティブ キャッシュ容量など、DWU の影響を受ける可能性があるすべての要素を考慮してください。 [さまざまな DWU 設定でワークロードを実行して](sql-data-warehouse-manage-compute-overview.md#finding-the-right-size-of-data-warehouse-units)、ビジネス目標を達成するために最適な動作を判断することをお勧めします。
- サーバー自体ではなく、特定のデータ ウェアハウスについての接続の失敗と成功がレポートされます。
- データ ウェアハウスがアイドル状態であっても、メモリの割合には使用率が反映されます。アクティブなワークロードのメモリ消費は反映されません。 追加のキャッシュ容量のスケーリングによってワークロードのパフォーマンスが要件を満たすように向上するかどうかに関する総合的な意思決定を行うために、このメトリックとその他 (tempdb、gen2 キャッシュ) を使用して追跡します。

## <a name="query-activity"></a>クエリ アクティビティ

T-SQL を使用して Synapse SQL を監視するときのプログラム エクスペリエンスでは、動的管理ビュー (DMV) のセットがサービスによって提供されます。 これらのビューは、アクティブのトラブルシューティングを行うときと、ワークロードでパフォーマンスのボトルネックを特定するときに役に立ちます。

Synapse SQL に適用される DMV の一覧を表示するには、この[ドキュメント](../sql/reference-tsql-system-views.md#dedicated-sql-pool-dynamic-management-views-dmvs)を参照してください。 

## <a name="metrics-and-diagnostics-logging"></a>メトリックと診断のロギング 

メトリックとログはどちらも、Azure Monitor (具体的には [Azure Monitor ログ](../../azure-monitor/logs/log-query-overview.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) コンポーネント) にエクスポートでき、[ログ クエリ](../../azure-monitor/logs/log-analytics-tutorial.md?bc=%2fazure%2fsynapse-analytics%2fsql-data-warehouse%2fbreadcrumb%2ftoc.json&toc=%2fazure%2fsynapse-analytics%2fsql-data-warehouse%2ftoc.json)を使用してプログラムでアクセスできます。 Synapse SQL のログの待機時間は約 10 から 15 分です。 待ち時間に影響を与える要因については、次のドキュメントをご覧ください。

## <a name="next-steps"></a>次のステップ

次のハウツー ガイドでは、データ ウェアハウスを監視および管理するときの一般的なシナリオとユース ケースについて説明しています。

- [DMV を利用してデータ ウェアハウスのワークロードを監視する](sql-data-warehouse-manage-monitor.md)