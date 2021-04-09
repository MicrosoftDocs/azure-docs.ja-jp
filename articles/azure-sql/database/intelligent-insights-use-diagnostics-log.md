---
title: Intelligent Insights パフォーマンス診断ログ
description: Intelligent Insights は、Azure SQL Database と Azure SQL Managed Instance のパフォーマンスの問題の診断ログを提供します
services: sql-database
ms.service: sql-db-mi
ms.subservice: performance
ms.custom: sqldbrb=2
ms.devlang: ''
ms.topic: conceptual
author: danimir
ms.author: danil
ms.reviewer: wiassaf, sstein
ms.date: 06/12/2020
ms.openlocfilehash: b03c21eea18c966616154b5cfc5df5d8924fd335
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "100589310"
---
# <a name="use-the-intelligent-insights-performance-diagnostics-log-of-azure-sql-database-and-azure-sql-managed-instance-performance-issues"></a>Azure SQL Database と Azure SQL Managed Instance のパフォーマンスに関する問題を含んだ Intelligent Insights のパフォーマンス診断ログを使用する
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

このページでは、[Intelligent Insights](intelligent-insights-overview.md) によって生成される、Azure SQL Database と Azure SQL Managed Instance のパフォーマンス診断ログの使用方法、その形式、およびカスタム開発ニーズに対応するために含まれるデータについて説明します。 この診断ログは、[Azure Monitor ログ](../../azure-monitor/insights/azure-sql.md)、[Azure Event Hubs](../../azure-monitor/essentials/resource-logs.md#send-to-azure-event-hubs)、[Azure Storage](metrics-diagnostic-telemetry-logging-streaming-export-configure.md#stream-into-azure-storage)、または DevOps のカスタム アラートおよびレポート機能を提供するサード パーティ製ソリューションに送信できます。

> [!NOTE]
> Intelligent Insights はプレビュー機能であり、次のリージョンでは利用できません。西ヨーロッパ、北ヨーロッパ、米国西部 1、米国東部 1。

## <a name="log-header"></a>ログ ヘッダー

診断ログでは、JSON 標準形式を使用して Intelligent Insights の結果を出力します。 Intelligent Insights ログへのアクセスの正確なカテゴリ プロパティは、固定値 "SQLInsights" です。

ログのヘッダーは共通で、エントリが作成された日時を示すタイムスタンプ (TimeGenerated) で構成されます。 これには、エントリに関連する特定のデータベースを参照するリソース ID (ResourceId) も含まれます。 カテゴリ (Category)、レベル (Level)、操作名 (OperationName) は固定のプロパティで、これらの値は変化しません。 これらは、ログ エントリが情報であり、Intelligent Insights (SQLInsights) から取得されたものであることを示します。

```json
"TimeGenerated" : "2017-9-25 11:00:00", // time stamp of the log entry
"ResourceId" : "database identifier", // value points to a database resource
"Category": "SQLInsights", // fixed property
"Level" : "Informational", // fixed property
"OperationName" : "Insight", // fixed property
```

## <a name="issue-id-and-database-affected"></a>問題 ID と影響を受けているデータベース

問題識別プロパティ (issueId_d) では、パフォーマンスの問題を解決まで一意に追跡できます。 ログ内の複数のイベント レコードで同じ問題の状態が報告されている場合は、それらのレコードで同じ問題 ID が共有されます。

診断ログでは、問題 ID と共に、診断ログにレポートされている問題に関連する特定のイベントの開始 (intervalStartTime_t) と終了 (intervalEndTme_t) のタイムスタンプがレポートされます。

エラスティック プール (elasticPoolName_s) プロパティは、問題のあるデータベースが属するエラスティック プールを示します。 データベースがエラスティック プールに属していない場合、このプロパティには値がありません。 問題が検出されたデータベースは、データベース名 (databaseName_s) プロパティで公開されます。

```json
"intervalStartTime_t": "2017-9-25 11:00", // start of the issue reported time stamp
"intervalEndTme_t":"2017-9-25 12:00", // end of the issue reported time stamp
"elasticPoolName_s" : "", // resource elastic pool (if applicable)
"databaseName_s" : "db_name", // database name
"issueId_d" : 1525, // unique ID of the issue detected
"status_s" : "Active" // status of the issue – possible values: "Active", "Verifying", and "Complete"
```

## <a name="detected-issues"></a>検出された問題

Intelligent Insights パフォーマンス ログの次のセクションには、組み込みの人工知能によって検出されたパフォーマンスの問題が含まれています。 検出結果は JSON 診断ログ内のプロパティで公開されます。 これらの検出結果は、問題のカテゴリ、問題の影響、影響を受けるクエリ、およびメトリックで構成されます。 検出プロパティには、検出されたパフォーマンスの問題が複数含まれる場合もあります。

検出されたパフォーマンスの問題は、次の検出プロパティ構造でレポートされます。

```json
"detections_s" : [{
"impact" : 1 to 3, // impact of the issue detected, possible values 1-3 (1 low, 2 moderate, 3 high impact)
"category" : "Detectable performance pattern", // performance issue detected, see the table
"details": <Details outputted> // details of an issue (see the table)
}]
```

検出可能なパフォーマンス パターンと、診断ログに出力される詳細を次の表に示します。

### <a name="detection-category"></a>検出カテゴリ

カテゴリ (category) プロパティは、検出可能なパフォーマンス パターンのカテゴリを示します。 検出可能なパフォーマンス パターンについて考えられるすべてのカテゴリについては、次の表をご覧ください。 詳細については、[Intelligent Insights を使用したデータベース パフォーマンスに関する問題のトラブルシューティング](intelligent-insights-troubleshoot-performance.md)に関する記事をご覧ください。

診断ログ ファイルに出力される詳細は、検出されたパフォーマンスの問題に応じて異なります。

| 検出可能なパフォーマンス パターン | 出力される詳細 |
| :------------------- | ------------------- |
| リソースの上限に到達 | <li>影響を受けているリソース</li><li>クエリ ハッシュ</li><li>リソース使用率</li> |
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
| プランの回帰 | <li>クエリ ハッシュ</li><li>適切なプラン ID</li><li>不適切なプラン ID</li> |
| データベース スコープの構成値の変更 | <li>既定値と比較したデータベース スコープの構成変更</li> |
| 処理速度が低いクライアント | <li>クエリ ハッシュ</li><li>待機時間</li> |
| 価格レベルのダウングレード | <li>テキスト通知</li> |

### <a name="impact"></a>影響

影響 (impact) プロパティは、検出された動作がどの程度データベースの問題に影響しているかを示します。 影響の範囲は 1 から 3 で、3 が最も影響が大きいことを示し、2 は中程度、1 が最も影響が小さいことを示します。 影響値は、特定のニーズに応じて、カスタム警告の自動化のための入力として使用されることがあります。 影響を受けているクエリ (QueryHashes) プロパティでは、特定の検出の影響を受けているクエリ ハッシュの一覧を示します。

### <a name="impacted-queries"></a>影響を受けているクエリ

Intelligent Insights ログの次のセクションでは、検出されたパフォーマンスの問題の影響を受けた特定のクエリに関する情報を示します。 この情報は、impact_s プロパティに埋め込まれたオブジェクトの配列として公開されます。 影響プロパティは、エンティティとメトリックで構成されます。 エンティティは特定のクエリを参照します (タイプ: Query)。 値 (Value) プロパティでは一意のクエリ ハッシュが公開されます。 さらに、公開された各クエリの後に、検出されたパフォーマンスの問題を示すメトリックと値が続きます。

次のログの例では、ハッシュ 0x9102EXZ4 のクエリの実行時間が増加したことが検出されています (メトリック: DurationIncreaseSeconds)。 110 秒という値は、この特定のクエリの実行に 110 秒長くかかったことを示しています。 複数のクエリが検出される可能性があるため、この特定のログ セクションには複数のクエリ エントリが含まれることがあります。

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

レポートされた各メトリックの測定単位はメトリック (metric) プロパティで示されます。可能な値は秒、個数、割合です。 測定されたメトリックの値は値 (value) プロパティでレポートされます。

DurationIncreaseSeconds プロパティでは、測定単位を秒で示します。 CriticalErrorCount の測定単位はエラーの数を表す数値です。

```json
"metric" : "DurationIncreaseSeconds", // issue metric type – possible values: DurationIncreaseSeconds, CriticalErrorCount, WaitingSeconds
"value" : 102 // value of the measured metric (in this case seconds)
```

## <a name="root-cause-analysis-and-improvement-recommendations"></a>根本原因分析と向上に関する推奨事項

Intelligent Insights パフォーマンス ログの最後の部分には、識別されたパフォーマンス低下の問題の根本原因の自動分析に関連する情報が示されます。 この情報は、根本原因分析 (rootCauseAnalysis_s) プロパティでわかりやすい表現で示されます。 改善に関する推奨事項は、可能な限りログに表示されます。

```json
// example of reported root cause analysis of the detected performance issue, in a human-readable format

"rootCauseAnalysis_s" : "High data IO caused performance to degrade. It seems that this database is missing some indexes that could help."
```

Intelligent Insights パフォーマンス ログは、[Azure Monitor ログ]( https://docs.microsoft.com/azure/log-analytics/log-analytics-azure-sql)、または DevOps のカスタム アラートおよびレポート機能を提供するサード パーティ製ソリューションで使用できます。

## <a name="next-steps"></a>次のステップ

- [Intelligent Insights](intelligent-insights-overview.md) の概念の習得
- [Intelligent Insights を使用したパフォーマンスに関する問題のトラブルシューティング](intelligent-insights-troubleshoot-performance.md)方法の習得
- [Azure SQL Analytics を使用してパフォーマンスの問題を監視](../../azure-monitor/insights/azure-sql.md)する方法の習得
- [Azure リソースからのログ データの収集と使用](../../azure-monitor/essentials/platform-logs-overview.md)の習得