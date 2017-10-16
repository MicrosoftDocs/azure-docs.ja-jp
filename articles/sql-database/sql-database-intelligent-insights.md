---
title: "Intelligent Insights によるデータベースの使用状況の監視 - Azure SQL Database | Microsoft Docs"
description: "Intelligent Insights では、データベースのパフォーマンスに何が起きているかを把握できます"
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
ms.translationtype: HT
ms.sourcegitcommit: c3a2462b4ce4e1410a670624bcbcec26fd51b811
ms.openlocfilehash: c3b11dd50fa8c94d3bf80e02a8a319030c375133
ms.contentlocale: ja-jp
ms.lasthandoff: 09/25/2017

---
# <a name="intelligent-insights"></a>Intelligent Insights

"***Intelligent Insights では、データベースのパフォーマンスに何が起きているかを把握できます。***"

Azure SQL Database の組み込みインテリジェンスは、人工知能を通じてデータベースの使用状況を継続的に監視し、パフォーマンス低下の原因となる破壊的なイベントを検出します。 検出されると、詳細な分析が実行され、問題のインテリジェントなアセスメントを含む診断ログが生成されます。 このアセスメントは、データベース パフォーマンスの問題の根本原因分析と、可能な場合にはパフォーマンス向上に関する推奨事項 (これが "***インテリジェントな洞察***" です) で構成されます。 

## <a name="what-can-intelligent-insights-do-for-you"></a>Intelligent Insights でできること

Intelligent Insights は、Azure の組み込みインテリジェンスの固有の機能であり、次の価値を提供します。

- プロアクティブな監視
- カスタマイズされたパフォーマンスの洞察
- データベースのパフォーマンス低下の早期検出
- 検出された問題の根本原因分析 (RCA)
- パフォーマンス向上に関する推奨事項
- 数十万のデータベースのスケールアウト機能
- DevOps リソースと総保有コストに対する良い影響

## <a name="how-does-intelligent-insights-work"></a>Intelligent Insights のしくみ

Intelligent Insights は、過去 1 時間のデータベース ワークロードを過去 7 日間のベースライン ワークロードと比較して、Azure SQL Database のパフォーマンスを分析します。 データベースのワークロードは、データベースのパフォーマンスに最も影響していると判断されたクエリから構成されます。たとえば、最も反復されているクエリや最大のクエリなどです。 各データベースは、その構造体、データ、使用状況、アプリケーションに基づいて一意です。そのため、生成される各ワークロード ベースラインは個々のインスタンスに固有です。 Intelligent Insights はワークロード ベースラインに依存せず、絶対的な運用しきい値も監視し、過剰な待機時間、重大な例外、パフォーマンスに影響を与える可能性のあるクエリ パラメーター化の問題を検出します。

人工知能を使用して複数の監視メトリックからパフォーマンス低下の問題が検出されると、分析が実行され、データベースで何が起きているかについてのインテリジェントな洞察を含む診断ログが出力されます。 Intelligent Insights では、最初の出現から解決まで、データベース パフォーマンスの問題を簡単に追跡できます。 これは、最初の問題検出からパフォーマンス向上の検証、その完了までのライフ サイクル全体を通じて、検出された各問題の状態を追跡することで実現されます。 診断ログは 15 分ごとに更新されます。 

<center>![サーバー](./media/sql-database-intelligent-insights/intelligent-insights-concept.png)</center>

データベース パフォーマンスの問題の測定と検出に使用されるメトリックは、クエリ実行時間、タイムアウト要求、過剰な待機時間、エラーが発生した要求に基づきます。これについては、このドキュメントの「[検出メトリック](sql-database-intelligent-insights.md#detection-metrics)」のセクションで詳細に説明します。

## <a name="degradations-detected"></a>検出される低下

識別された Azure SQL Database のパフォーマンス低下は、次のプロパティで構成されるインテリジェントなエントリを含む診断ログに記録されます。

| プロパティ             | 詳細              |
| :------------------- | ------------------- |
| データベース情報  | リソース URI など、洞察が検出されたデータベースに関するメタデータ |
| 監視された時間の範囲 | 検出された洞察の期間の開始日時と終了日時 |
| 影響を受けているメトリック | 洞察が生成される原因となったメトリック: <ul><li>クエリ実行時間の増加 [秒]</li><li>過剰な待機 [秒]</li><li>タイムアウト要求 [割合]</li><li>エラーが発生した要求 [割合]</li></ul>|
| 影響値 | 測定されたメトリックの値 |
| 影響を受けているクエリとエラー コード | クエリ ハッシュまたはエラー コード。 これらは、影響を受けているクエリを簡単に関連付けるために使用できます。 クエリ実行時間の増加、待機時間、タイムアウトの数、またはエラー コードで構成されるメトリックが提供されます。 |
| 検出 | イベントの期間中に、データベースで識別された検出。 15 の検出パターンがあります。 [Intelligent Insights を使用したデータベース パフォーマンスに関する問題のトラブルシューティング](sql-database-intelligent-insights-troubleshoot-performance.md)に関する記事をご覧ください。 |
| 根本原因分析 | 人間が判読可能な形式で識別された、問題の根本原因分析 (RCA)。 一部の洞察には、可能な場合にパフォーマンス向上に関する推奨事項が含まれます。 |
|||

## <a name="issues-state-lifecycle-active-verifying-and-completed"></a>問題の状態のライフサイクル: Active、Verifying、Completed

診断ログに記録されたパフォーマンスの問題には、問題のライフ サイクルの 3 つの状態 (Active、Verifying、Completed) のいずれかのフラグが設定されます。 パフォーマンスの問題が検出されると、Azure SQL の組み込みインテリジェンスによってその存在が認められている限り、問題には "***Active***" のフラグが設定されます。 問題は緩和されたと見なされた時点で検証され、問題の状態が "***Verifying***" に変更されます。 Azure SQL の組み込みインテリジェンスによって問題が解決済みと見なされると、問題の状態フラグが "***Completed***" に設定されます。

## <a name="using-intelligent-insights"></a>Intelligent Insights の使用

「[Azure SQL Database のメトリックと診断のロギング](sql-database-metrics-diag-logging.md)」で説明しているように、Intelligent Insight 診断ログは Azure Log Analytics、Azure Event Hub、Azure Storage に送信できます。 ログがこれらのターゲットのいずれかに送信されると、Microsoftまたはサード パーティー製のツールを使用したカスタム アラートおよび監視の開発にログを使用できます。 Intelligent Insights を使用したAzure SQL Database パフォーマンスのトラブルシューティング方法については、「[Troubleshoot Azure SQL Database performance issues with Intelligent Insights](sql-database-intelligent-insights-troubleshoot-performance.md)」(Intelligent Insights を使用した Azure SQL Database のパフォーマンスに関する問題のトラブルシューティング) をご覧ください。

## <a name="built-in-intelligent-insights-analytics-with-azure-log-analytics"></a>Azure Log Analytics での組み込みの Intelligent Insights 分析 

Azure Log Analytics ソリューションは、Intelligent Insights 診断ログ データに基づくレポートとアラートの機能を提供します。 Azure SQL Analytics の Intelligent Insights レポートの例を以下に示します。

![サーバー](./media/sql-database-intelligent-insights/intelligent-insights-azure-sql-analytics.png)

Intelligent Insights 診断ログが Azure SQL Analytics にデータをストリーミングするように構成されたら、[Azure SQL Analytics を使用して Azure SQL Database を監視](../log-analytics/log-analytics-azure-sql.md)できます。

## <a name="custom-integrations-of-intelligent-insights-log"></a>Intelligent Insights ログのカスタム統合

Microsoft またはサード パーティー製のツールを使用してカスタムのアラートと監視を開発するには、[Intelligent Insights データベース パフォーマンス診断ログの使用](sql-database-intelligent-insights-use-diagnostics-log.md)に関する記事をご覧ください。

## <a name="how-to-set-up-intelligent-insights-with-azure-event-hub"></a>Azure Event Hub で Intelligent Insights を設定する方法

- [Event Hubs への Azure 診断ログのストリーミング](../monitoring-and-diagnostics/monitoring-stream-diagnostic-logs-to-event-hubs.md)に関する記事に従って、Azure Event Hub にログ イベントをストリーミングするように Intelligent Insights を構成します。
- [Event Hub でメトリックと診断ログに対して何を行うか](sql-database-metrics-diag-logging.md#stream-into-azure-storage)に関するページに従って、Azure Event Hub をカスタムの監視とアラートに使用します。 

## <a name="how-to-set-up-intelligent-insights-with-azure-storage"></a>Azure Storage で Intelligent Insights を設定する方法

- 「[Azure Storage にストリーミングする](sql-database-metrics-diag-logging.md#stream-into-azure-storage)」に従って、Azure Storage に格納するように Intelligent Insights を構成します。

## <a name="detection-metrics"></a>検出メトリック

Intelligent Insights を生成する検出モデルに使用されるメトリックは、以下の監視に基づいています。

- クエリ実行時間
- タイムアウト要求
- 過剰な待機時間 
- エラーが発生した要求

クエリ実行時間とタイムアウト要求は、データベース ワークロードのパフォーマンスに関する問題を検出する際にプライマリ モデルとして使用されます。 これは、ワークロードで何が起きているのかを直接測定するためです。 ワークロードのパフォーマンス低下について考えられるすべてのケースを検出するために、過剰な待機時間とエラーが発生した要求が、ワークロードのパフォーマンスに影響する問題を示すための追加モデルとして使用されます。

ワークロードに対する変更とデータベースに対するクエリ要求の数の変化がシステムによって自動的に考慮されて、標準と標準外のデータベース パフォーマンスしきい値が動的に決定されます。

科学的に導出されたデータ モデルを通じてすべてのメトリックがさまざまなリレーションシップで考慮され、検出された各パフォーマンス問題が分類されます。 Intelligent Insight を通じて提供される情報には、検出されたパフォーマンスの問題の詳細、問題発生の根本原因分析、監視対象 Azure SQL Database のパフォーマンスを向上させる方法に関する推奨事項 (可能な場合) が含まれます。

## <a name="query-duration"></a>クエリ実行時間

クエリ実行時間のパフォーマンス低下モデルでは、個々のクエリを分析し、パフォーマンス ベースラインと比較して、クエリのコンパイルと実行にかかる時間の増加を検出します。

Azure SQL Database の組み込みインテリジェンスによってクエリのコンパイルまたはクエリ実行時間の大幅な増加がワークロードのパフォーマンスに影響を与えていることが検出された場合、これらのクエリには、クエリ実行時間のパフォーマンス低下の問題のフラグが設定されます。 

Intelligent Insights 診断ログは、パフォーマンスを低下させたクエリのクエリ ハッシュ、パフォーマンス低下がクエリのコンパイルとクエリ実行時間のどちらの増加に関連していたか、および増加したクエリ実行時間を出力します。

## <a name="timeout-requests"></a>タイムアウト要求

タイムアウト要求のパフォーマンス低下モデルでは、個々のクエリを分析し、クエリの実行レベルでのタイムアウトの増加と、パフォーマンス ベースライン期間と比較した、データベース レベルでの全体的な要求タイムアウトの増加を検出します。

クエリの一部は実行段階に達する前にタイムアウトになる場合があるため、Azure SQL Database の組み込みインテリジェンスは、中止された worker と行われた要求を使用して、実行段階に達したかどうかにかかわらず、データベースに到達したすべてのクエリを測定および分析します。 

実行されたクエリのタイムアウト数、または中止された要求 worker 数がシステムで管理されているしきい値を上回ると、診断ログに Intelligent Insights が設定されます。

生成される洞察には、タイムアウトになった要求の数、タイムアウトになったクエリの数、およびパフォーマンス低下が実行段階におけるタイムアウトの増加と全体的なデータベース レベルのどちらに関連していたかが含まれます。 タイムアウトの増加がデータベース パフォーマンスに影響していると見なされた場合、これらのクエリにはタイムアウトのパフォーマンス低下の問題のフラグが設定されます。 

## <a name="excessive-wait-times"></a>過剰な待機時間

過剰な待機時間モデルでは、個々のデータベース クエリを監視し、システムで管理される絶対しきい値を超える異常に高いクエリ待機状態を検出します。 次の過剰クエリ待機時間メトリックは、新しい SQL Server 機能であるクエリ ストア待機統計 (sys.query_store_wait_stats) 機能を利用して監視されます。

- リソース制限に到達
- エラスティック プール リソース制限に到達
- 過剰な worker またはセッション スレッド数
- 過剰なデータベース ロック
- メモリ不足
- その他の待機統計

リソース制限またはエラスティック プール リソースの制限への到達は、サブスクリプションまたはエラスティック プールで使用可能なリソースの消費が、ワークロードのパフォーマンス低下を示す絶対しきい値を超えて増加したことを示します。 過剰な worker またはセッションのスレッド数は、開始された worker スレッドまたはセッションの数が、ワークロードのパフォーマンス低下を示す絶対しきい値を超えていることを示します。

過剰なデータベース ロックは、データベースのロックの数が、ワークロードのパフォーマンス低下を示す絶対しきい値を超えていることを示します。 メモリ不足は、メモリ許可を要求しているスレッドの数が、ワークロードのパフォーマンス低下を示す絶対しきい値を超えて増加した状態です。

その他の待機統計検出は、クエリ ストア待機統計で測定されるその他のメトリックが、ワークロードのパフォーマンス低下を示す絶対しきい値を超えたことを示します。

過剰な待機時間が検出されると、Intelligent Insights 診断ログは、使用可能なデータに応じて、パフォーマンス低下に影響しているクエリと影響を受けているクエリのハッシュ、クエリが実行を待機する原因となったメトリックの詳細、測定された待機時間を出力します。

## <a name="errored-requests"></a>エラーが発生した要求

エラーが発生した要求のパフォーマンス低下モデルでは、個々のクエリを監視し、ベースライン期間と比較してエラーが発生したクエリ数の増加を検出します。 このモデルでは、Azure Database の組み込みインテリジェンスによって管理されている絶対しきい値に達した重大な例外も監視されます。 システムでは、データベースに対して行われたクエリ要求の数が自動的に考慮され、監視対象の期間中のワークロードの変化が考慮されます。

行われた全体的な要求数との関連で、エラーが発生した要求について測定された増加がワークロードのパフォーマンスに影響していると見なされる場合、影響を受けているクエリには、エラーが発生した要求のパフォーマンス低下の問題があることを示すフラグが設定されます。

Intelligent Insights ログは、エラーが発生した要求の数、パフォーマンスの低下がエラーになった要求の増加と監視対象の重大な例外のしきい値への到達のどちらに関連するものであるか、およびパフォーマンス低下の測定時間を出力します。 

監視対象の重大な例外のいずれかが、システムによって管理されている絶対しきい値を超えた場合は、インテリジェントな洞察が重大な例外の詳細と共に生成されます。

## <a name="next-steps"></a>次のステップ
* [Intelligent Insights を使用した Azure SQL Database のパフォーマンスに関する問題のトラブルシューティング](sql-database-intelligent-insights-troubleshoot-performance.md)の習得
* [Intelligent Insights Azure SQL Database パフォーマンス診断ログ](sql-database-intelligent-insights-use-diagnostics-log.md)の使用
* [Azure SQL Analytics を使用した Azure SQL Database の監視](../log-analytics/log-analytics-azure-sql.md)の習得
* [Azure リソースからのログ データの収集と使用](../monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs.md)の習得



