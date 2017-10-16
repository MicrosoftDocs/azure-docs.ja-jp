---
title: "Intelligent Insights のパフォーマンス診断ログ - Azure SQL Database | Microsoft Docs"
description: "Intelligent Insights は、Azure SQL Database のパフォーマンスの問題の診断ログを提供します"
services: sql-database
documentationcenter: 
author: danimir
manager: drasumic
editor: carlrab
ms.assetid: 
ms.service: sql-database
ms.custom: monitor & tune
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: NA
ms.date: 09/25/2017
ms.author: v-daljep
ms.openlocfilehash: 4890baa4ead3323834a82b3f9340cf751bf0c755
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="use-intelligent-insights-azure-sql-database-performance-diagnostics-log"></a>Intelligent Insights Azure SQL Database パフォーマンス診断ログを使用する

このページでは、[Intelligent Insights](sql-database-intelligent-insights.md) によって生成される Azure SQL Database パフォーマンス診断ログの使用、その形式、およびカスタム開発ニーズに対応するために含まれるデータについて説明します。 この診断ログを、[Azure Log Analytics](../log-analytics/log-analytics-azure-sql.md)、[Azure Event Hub](../monitoring-and-diagnostics/monitoring-stream-diagnostic-logs-to-event-hubs.md)、[Azure Storage](sql-database-metrics-diag-logging.md#stream-into-azure-storage)、または DevOps のカスタム アラートおよびレポート機能を提供するサード パーティー製ソリューションに送信できます。

## <a name="log-header"></a>ログ ヘッダー

診断ログは、Intelligent Insights の結果の出力に JSON 標準形式を使用します。 Intelligent Insights ログへのアクセスの正確なカテゴリ プロパティは、固定値 &#8220**SQLInsights**&#8221; です。

ログのヘッダーは共通で、エントリが作成されたときのタイムスタンプ (TimeGenerated) で構成されています。これには、エントリに関連する特定の Azure SQL Database を表すリソース ID (ResourceId) が含まれます。 カテゴリ (Category)、レベル (Level)、操作名 (OperationName) は固定のプロパティで、その値は変化しません。これらは、ログ エントリが情報であり、Intelligent Insights (SQLInsights) から取得されたものであることを示します。

```json
"TimeGenerated" : "2017-9-25 11:00:00", // time stamp of the log entry
"ResourceId" : "database identifier", // value points to a database resource
"Category": "SQLInsights", // fixed property
"Level" : "Informational", // fixed property
"OperationName" : "Insight", // fixed property
```

## <a name="issue-id-and-database-affected"></a>問題 ID と影響を受けているデータベース

問題識別プロパティ (issueId_d) では、パフォーマンスの問題を解決まで一意に追跡できます。 Intelligent Insights は、各問題のライフサイクルを Active、Verifying、Completed として監視します。 これらの各状態フェーズ中、Intelligent Insights はログに複数のイベント レコードを記録でき、各エントリについて問題 ID 番号が一意に維持されます。 Intelligent Insights は、ライフサイクルを通じて問題を追跡し、診断ログに 15 分ごとに洞察を生成します。

パフォーマンスの問題が検出されると、それが継続する限り、問題は状態 (Status) プロパティで "Active" としてレポートされます。 検出された問題が緩和されると、検証され、状態 (Status) プロパティに "Verifying" としてレポートされます。 問題が存在しなくなった場合は、状態 (Status) プロパティでこの問題が "Completed" としてレポートされます。
問題 ID と共に、診断ログには、診断ログにレポートされている問題に関連する特定のイベントの開始 (intervalStartTime_t) と終了 (intervalEndTme_t) のタイムスタンプがレポートされます。

エラスティック プール プロパティ (elasticPoolName_s) は、問題のあるデータベースが属するエラスティック プールを示します。 データベースがエラスティック プールに属していない場合、このプロパティには値がありません。 問題が検出されたデータベースは、データベース名 (databaseName_s) プロパティで公開されます。

```json
"intervalStartTime_t": "2017-9-25 11:00", // start of the issue reported timestamp
"intervalEndTme_t":"2017-9-25 12:00", // end of the issue reported timestamp
"elasticPoolName_s" : "", // resource elastic pool (if applicable) 
"databaseName_s" : "db_name",  // database name
"issueId_d" : 1525, // unique ID of the issue detected
"status_s" : "Active" // status of the issue – possible values: Active, Verifying and Complete
```

## <a name="detected-issues"></a>検出された問題

Intelligent Insights パフォーマンス ログの次のセクションには、組み込みの人工知能によって検出されたパフォーマンスの問題が含まれています。 検出は、問題のカテゴリ、問題の影響、影響を受けているクエリ、メトリックから構成される JSON プロパティ検出内で公開されます。 検出プロパティには、検出されたパフォーマンスの問題が複数含まれる可能性があります。

検出されたパフォーマンスの問題は、次の検出プロパティ構造でレポートされます。

```json
"detections_s" : [{
"impact" : 1 to 3, // impact of the issue detected, possible values 1-3 (1 low, 2 moderate, 3 high impact)
"category" : "Detectable performance pattern", // tperformance issue detected, see the table
"details": <Details outputted> // details of an issue (see the table)
}] 
```

検出可能なパフォーマンス パターンと診断ログに出力される詳細を次の表に示します。

### <a name="detection-category"></a>検出カテゴリ

カテゴリ (category) プロパティは、検出可能なパフォーマンス パターンのカテゴリについて説明します。 検出可能なパフォーマンス パターンについて考えられるすべてのカテゴリについては、次の表をご覧ください。 詳細については、[Intelligent Insights を使用したデータベース パフォーマンスの問題のトラブルシューティング](sql-database-intelligent-insights-troubleshoot-performance.md)に関するページをご覧ください。

検出されたパフォーマンスの問題に応じて、診断ログ ファイルに出力される詳細は異なります。

| 検出可能なパフォーマンス パターン | 出力される詳細 |
| :------------------- | ------------------- |
| リソース制限に到達 | <li>影響を受けているリソース</li><li>クエリ ハッシュ</li><li>リソース使用率</li> |
| ワークロードの増加 | <li>実行が増加したクエリの数</li><li>ワークロードの増加に最も影響しているクエリのクエリ ハッシュ</li> |
| メモリ不足 | <li>メモリ クラーク</li> |
| ロック | <li>影響を受けているクエリ ハッシュ</li><li>ブロックしているクエリ ハッシュ</li> |
| MAXDOP の増加 | <li>クエリ ハッシュ</li><li>CXP 待機時間</li><li>待機時間</li> |
| ページラッチの競合 | <li>競合の原因となっているクエリのクエリ ハッシュ</li> |
| インデックスの不足 | <li>クエリ ハッシュ</li> |
| 新しいクエリ | <li>新しいクエリのクエリ ハッシュ</li> |
| 異常な待機の統計 | <li>異常な待機の種類</li><li>クエリ ハッシュ</li><li>クエリ待機時間</li> |
| TempDB の競合 | <li>競合の原因となっているクエリのクエリ ハッシュ</li><li>全体的なデータベース ページラッチ競合の待機時間に対するクエリの寄与 [%]</li> |
| エラスティック プールの DTU の不足 | <li>エラスティック プール</li><li>DTU を最も多く消費しているデータベース</li><li>最上位のコンシューマーによって使用されているプール DTU の割合</li> |
| プランの回帰 | <li>クエリ ハッシュ</li><li>適切なプラン ID</li><li>不適切なプラン ID</li><li>クエリ ハッシュ</li> |
| データベース スコープの構成値の変更 | <li>既定値と比較した DB スコープの構成変更</li> |
| 処理速度が低いクライアント | <li>クエリ ハッシュ</li><li>待機時間</li> |
| 価格レベルのダウングレード | <li>テキスト通知</li> |

### <a name="impact"></a>影響

影響 (impact) プロパティは、検出されたビヘイビアーがデータベースの問題にどの程度影響しているかを 1 ～ 3 で示します。3 が最大の影響で、2 は中程度、1 は最も低い影響です。 影響値は、特定のニーズに応じて、カスタム警告の自動化のための入力として使用されることがあります。 影響を受けているクエリ (QueryHashes) プロパティは、特定の検出の影響を受けているクエリ ハッシュの一覧を提供します。

### <a name="impacted-queries"></a>影響を受けているクエリ

Intelligent Insights ログの次のセクションは、検出されたパフォーマンスの問題の影響を受けている特定のクエリに関する情報を提供します。 この情報は、impact_s プロパティに埋め込まれたオブジェクトの配列として公開されます。 影響プロパティは、エンティティとメトリックで構成されます。 エンティティは、特定のクエリ (タイプ: Query) と、プロパティ値 (Value) で公開される一意のクエリ ハッシュを表します。 さらに、公開された各クエリの後に、検出されたパフォーマンスの問題を示すメトリックと値が続きます。

次のログ例では、ハッシュ 0x9102EXZ4 のクエリ実行時間の値が 110 秒増加したことがわかります (メトリック: DurationIncreaseSeconds)。これは、この特定のクエリの実行に要する時間が 110 秒長くなったことを示します。 複数のクエリが検出される可能性があるため、この特定のログ セクションには複数のクエリ エントリが含まれることがあります。

```json
"impact" : [{
"entity" : { 
"Type" : "Query", // type of entity - query
"Value" : "query hash value", // for example "0x9102EXZ4" query hash value },
"Metric" : "DurationIncreaseSeconds", // measured metric and the measurement unit (in this case seconds)
"Value" : 110 // value of the measured metric (in this case seconds)
}]
```

### <a name="metrics"></a>メトリック

レポートされる各メトリックの測定単位は、メトリック (metric) プロパティで提供され、可能な値は秒、個数、割合です。 測定されたメトリックの値は、値 (value) プロパティでレポートされます。

DurationIncreaseSeconds プロパティは秒単位の測定を示し、CriticalErrorCount の測定単位はエラー数を表す数値です。

```json
"metric" : "DurationIncreaseSeconds", // issue metric type – possible values: DurationIncreaseSeconds, CriticalErrorCount, WaitingSeconds
"value" : 102 // value of the measured metric (in this case seconds)
```

## <a name="root-cause-analysis-and-improvement-recommendations"></a>根本原因分析と向上に関する推奨事項

Intelligent Insights パフォーマンス ログの最後の部分は、識別されたパフォーマンス低下の問題の自動的な根本原因分析に関連します。これは、根本原因分析 (rootCauseAnalysis_s) プロパティを通じてわかりやすく表現されています。 向上に関する推奨事項は、可能な場合に、ログに記録された表現に含まれます。

```json
// example of reported root cause analysis of the detected performance issue, in a human-readable format

"rootCauseAnalysis_s" : "High data IO caused performance to degrade. It seems that this database is missing some indexes that could help."
```

Intelligent Insights パフォーマンス ログは、[Azure Log Analytics]( https://docs.microsoft.com/en-us/azure/log-analytics/log-analytics-azure-sql)、または DevOps のカスタム アラートおよびレポート機能を提供するサード パーティー製ソリューションで使用できます。

## <a name="next-steps"></a>次のステップ
- [Intelligent Insights](sql-database-intelligent-insights.md) の概念の習得
- [Intelligent Insights を使用した Azure SQL Database のパフォーマンスに関する問題のトラブルシューティング](sql-database-intelligent-insights-troubleshoot-performance.md)の習得
- [Azure SQL Analytics を使用した Azure SQL Database の監視](https://docs.microsoft.com/en-us/azure/log-analytics/log-analytics-azure-sql)の習得
- [Azure リソースからのログ データの収集と使用](https://docs.microsoft.com/en-us/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs)



