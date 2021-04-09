---
title: Intelligent Insights でデータベース パフォーマンスを監視する
description: Azure SQL Database と Azure SQL Managed Instance の Intelligent Insights では、組み込まれているインテリジェンスを使って、人工知能によりデータベースの使用状況が継続的に監視され、パフォーマンス低下の原因となる破壊的なイベントが検出されます。
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
ms.openlocfilehash: 61033e3eb8264c1e462faac3e4553a855a1d06c7
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "100592088"
---
# <a name="intelligent-insights-using-ai-to-monitor-and-troubleshoot-database-performance-preview"></a>AI を使用してデータベース パフォーマンスの監視とトラブルシューティングを行う Intelligent Insights (プレビュー)
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

Azure SQL Database と Azure SQL Managed Instance の Intelligent Insights では、データベースのパフォーマンスに起きていることを把握できます。

Intelligent Insights は、組み込まれているインテリジェンスを使って、人工知能によりデータベースの使用状況を継続的に監視し、パフォーマンス低下の原因となる破壊的なイベントを検出します。 検出されると、詳細な分析が実行され、問題のインテリジェントなアセスメントを含む Intelligent Insights リソース ログ (通称 SQLInsights) が生成されます。 このアセスメントは、データベース パフォーマンスの問題の根本原因分析と、可能な場合にはパフォーマンス向上に関する推奨事項で構成されます。

## <a name="what-can-intelligent-insights-do-for-you"></a>Intelligent Insights でできること

Intelligent Insights は、Azure の組み込みインテリジェンスの固有の機能であり、次の価値を提供します。

- プロアクティブな監視
- カスタマイズされたパフォーマンスの洞察
- データベースのパフォーマンス低下の早期検出
- 検出された問題の根本原因分析
- パフォーマンス向上に関する推奨事項
- 数十万のデータベースのスケールアウト機能
- DevOps リソースと総保有コストに対する良い影響

## <a name="how-does-intelligent-insights-work"></a>Intelligent Insights のしくみ

Intelligent Insights は、過去 1 時間のデータベース ワークロードを過去 7 日間のベースライン ワークロードと比較して、データベースのパフォーマンスを分析します。 データベースのワークロードは、データベースのパフォーマンスに最も影響していると判断されたクエリから構成されます。たとえば、最も反復されているクエリや最大のクエリなどです。 各データベースは、その構造体、データ、使用状況、アプリケーションに基づいて一意です。そのため、生成される各ワークロード ベースラインはそのワークロードに固有です。 Intelligent Insights はワークロード ベースラインに依存せず、絶対的な運用しきい値も監視し、過剰な待機時間、重大な例外、パフォーマンスに影響を与える可能性のあるクエリ パラメーター化の問題を検出します。

人工知能を使用して複数の監視対象メトリックからパフォーマンスの低下問題が検出されると、分析が実行されます。 データベースで発生している状況のインテリジェントな洞察を含む診断ログが生成されます。 Intelligent Insights では、最初の出現から解決まで、データベース パフォーマンスの問題を簡単に追跡できます。 検出された各問題は、最初の問題検出からパフォーマンス向上の検証、その完了までのライフ サイクル全体を通じて追跡されます。

![データベース パフォーマンスの分析ワークフロー](./media/intelligent-insights-overview/intelligent-insights-concept.png)

データベース パフォーマンスの問題の測定と検出に使用されるメトリックは、クエリ実行時間、タイムアウト要求、過剰な待機時間、エラーが発生した要求に基づきます。 メトリックの詳細については、「[検出メトリック](#detection-metrics)」を参照してください。

特定されたデータベースのパフォーマンス低下は、次のプロパティで構成されるインテリジェントなエントリを含む SQLInsights ログに記録されます。

| プロパティ | 詳細 |
| :------------------- | ------------------- |
| データベース情報 | リソース URI など、洞察が検出されたデータベースに関するメタデータ |
| 監視された時間の範囲 | 検出された洞察の期間の開始日時と終了日時。 |
| 影響を受けているメトリック | 洞察が生成される原因となったメトリック: <ul><li>クエリ実行時間の増加 [秒]。</li><li>過剰な待機 [秒]。</li><li>タイムアウト要求 [割合]。</li><li>エラーが発生した要求 [割合]。</li></ul>|
| 影響値 | 測定されたメトリックの値。 |
| 影響を受けているクエリとエラー コード | クエリ ハッシュまたはエラー コード。 これらは、影響を受けているクエリを簡単に関連付けるために使用できます。 クエリ実行時間の増加、待機時間、タイムアウトの数、またはエラー コードで構成されるメトリックが提供されます。 |
| 検出 | イベントの期間中に、データベースで識別された検出。 15 の検出パターンがあります。 詳細については、[Intelligent Insights を使用したデータベース パフォーマンスに関する問題のトラブルシューティング](intelligent-insights-troubleshoot-performance.md)に関する記事をご覧ください。 |
| 根本原因分析 | 人間が判読可能な形式で特定された、問題の根本原因分析。 一部の洞察には、可能な場合にパフォーマンス向上に関する推奨事項が含まれます。 |
|||

Azure SQL Analytics を伴う Intelligent Insights の使用に関する実践的な概要と、一般的な使用シナリオについては、このビデオをご覧ください。

> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Get-Intelligent-Insights-for-Improving-Azure-SQL-Database-Performance/player]
>

Intelligent Insights は、データベースのパフォーマンス問題の検出とトラブルシューティングで威力を発揮します。 Intelligent Insights を使用してデータベース パフォーマンスの問題を解決する方法については、「[Intelligent Insights を使用したパフォーマンスに関する問題のトラブルシューティング](intelligent-insights-troubleshoot-performance.md)」をご覧ください。

## <a name="intelligent-insights-options"></a>Intelligent Insights のオプション

使用可能な Intelligent Insights のオプションは次のとおりです。

| Intelligent Insights のオプション | Azure SQL Database のサポート | Azure SQL Managed Instance のサポート |
| :----------------------------- | ----- | ----- |
| **[Configure Intelligent Insights]\(Intelligent Insights を構成する\)** - データベース用に Intelligent Insights の分析を構成します。 | はい | はい |
| **[Stream insights to Azure SQL Analytics]\(Azure SQL Analytics に分析情報をストリーム配信する\)** - Azure SQL Analytics に分析情報をストリーム配信します。 | はい | はい |
| **[Stream insights to Azure Event Hubs]\(Azure Event Hubs に分析情報をストリーム配信する\)** - 詳細なカスタム統合のために Event Hubs に分析情報をストリーム配信します。 | はい | はい |
| **[Stream insights to Azure Storage]\(Azure Storage に分析情報をストリーム配信する\)** - 詳細な分析と長期的なアーカイブのために、Azure Storage に分析情報をストリーム配信します。 | はい | はい |

> [!NOTE]
> インテリジェントな分析情報はプレビュー機能であり、次のリージョンでは利用できません。西ヨーロッパ、北ヨーロッパ、米国西部 1、米国東部 1。

## <a name="configure-the-export-of-the-intelligent-insights-log"></a>Intelligent Insights ログのエクスポートを構成する

Intelligent Insights の出力は、分析のためにいずれかの宛先にストリーム配信できます。

- Azure portal のユーザー インターフェイスで分析情報を表示する目的で、Log Analytics ワークスペースにストリーム配信された出力を [Azure SQL Analytics](../../azure-monitor/insights/azure-sql.md) と共に使用できます。 これは統合された Azure ソリューションであり、分析情報を表示する最も一般的な方法です。
- カスタムの監視および警告シナリオを開発する目的で、Azure Event Hubs にストリーム配信された出力を使用できます。
- カスタム レポートや長期データ アーカイブなどのカスタム アプリケーションを開発する目的で、Azure Storage にストリーム配信された出力を使用できます。

Azure SQL Analytics、Azure Event Hubs、Azure Storage、または情報を利用するサード パーティ製品は、最初にデータベースの診断設定ブレードで Intelligent Insights ログ記録 ("SQLInsights" ログ) を有効にしてから、これらの宛先のいずれかに Intelligent Insights ログ データがストリーミングされるように構成することで統合されます。

Intelligent Insights のログ記録を有効にして、情報を利用する製品にメトリックとリソース ログ データがストリーミングされるように構成する方法については、[メトリックと診断のロギング](metrics-diagnostic-telemetry-logging-streaming-export-configure.md)に関するページをご覧ください。

### <a name="set-up-with-azure-sql-analytics"></a>Azure SQL Analytics で使用するように設定する

Azure SQL Analytics ソリューションでは、Intelligent Insights リソース ログ データを利用し、データベースのパフォーマンスについてのグラフィカル ユーザー インターフェイス、レポート、アラート機能を提供します。

Marketplace から Azure portal ダッシュボードに Azure SQL Analytics を追加して、ワークスペースを作成する方法については、[Azure SQL Analytics の構成](../../azure-monitor/insights/azure-sql.md#configuration)に関するページをご覧ください。

Azure SQL Analytics と共に Intelligent Insights を使用するには、前のステップで作成した Azure SQL Analytics ワークスペースにストリーミングされるように、Intelligent Insights ログ データを構成します。[メトリックと診断のロギング](metrics-diagnostic-telemetry-logging-streaming-export-configure.md)に関するページをご覧ください。

Azure SQL Analytics で表示された Intelligent Insights の例を以下に示します。

![Intelligent Insights レポート](./media/intelligent-insights-overview/intelligent-insights-azure-sql-analytics.png)

### <a name="set-up-with-event-hubs"></a>Event Hubs で使用するように設定する

Event Hubs と共に Intelligent Insights を使用する方法と、Event Hubs に Intelligent Insights ログ データがストリーミングされるように構成する方法については、[メトリックと診断のロギング](metrics-diagnostic-telemetry-logging-streaming-export-configure.md)と、[Azure Diagnostics ログを Event Hubs にストリーミングする](../../azure-monitor/essentials/resource-logs.md#send-to-azure-event-hubs)ことに関するページをご覧ください。

Event Hubs をカスタムの監視とアラートの設定に使用するには、「[Event Hubs におけるメトリックと診断ログの活用方法](metrics-diagnostic-telemetry-logging-streaming-export-configure.md#what-to-do-with-metrics-and-resource-logs-in-event-hubs)」をご覧ください。

### <a name="set-up-with-azure-storage"></a>Azure Storage で使用するように設定する

Storage と共に Intelligent Insights を使用する方法と、Storage に Intelligent Insights ログ データがストリーミングされるように構成する方法については、[メトリックと診断のロギング](metrics-diagnostic-telemetry-logging-streaming-export-configure.md)に関するページと、「[Azure Storage にストリーミングする](metrics-diagnostic-telemetry-logging-streaming-export-configure.md#stream-into-azure-storage)」をご覧ください。

### <a name="custom-integrations-of-intelligent-insights-log"></a>Intelligent Insights ログのカスタム統合

サード パーティ製のツールと共に、または、カスタムのアラートと監視の開発に Intelligent Insights を使用するには、「[Intelligent Insights Azure SQL Database パフォーマンス診断ログを使用する](intelligent-insights-use-diagnostics-log.md)」をご覧ください。

## <a name="detection-metrics"></a>検出メトリック

Intelligent Insights を生成する検出モデルに使用されるメトリックは、以下の監視に基づいています。

- クエリ実行時間
- タイムアウト要求
- 過剰な待機時間
- エラーが発生した要求

クエリ実行時間とタイムアウト要求は、データベース ワークロードのパフォーマンスに関する問題を検出する際にプライマリ モデルとして使用されます。 これらの情報は、ワークロードで何が起きているのかを直接測定するために使用されます。 ワークロードのパフォーマンス低下について考えられるすべてのケースを検出するために、過剰な待機時間とエラーが発生した要求が、ワークロードのパフォーマンスに影響する問題を示すための追加モデルとして使用されます。

ワークロードに対する変更とデータベースに対するクエリ要求の数の変化がシステムによって自動的に考慮されて、標準と標準外のデータベース パフォーマンスしきい値が動的に決定されます。

科学的に導出されたデータ モデルを通じてすべてのメトリックがさまざまなリレーションシップで考慮され、検出された各パフォーマンス問題が分類されます。 インテリジェントな洞察によって、次のような情報がわかります。

- 検出されたパフォーマンスの問題の詳細。
- 検出された問題の根本原因分析。
- 監視対象のデータベースのパフォーマンスを改善する方法 (可能な場合) に関する推奨事項。

## <a name="query-duration"></a>クエリ実行時間

クエリ実行時間のパフォーマンス低下モデルでは、個々のクエリを分析し、パフォーマンス ベースラインと比較して、クエリのコンパイルと実行にかかる時間の増加を検出します。

組み込みインテリジェンスによってクエリのコンパイルまたはクエリ実行時間の大幅な増加がワークロードのパフォーマンスに影響を与えていることが検出された場合、これらのクエリには、クエリ実行時間のパフォーマンス低下の問題のフラグが設定されます。

Intelligent Insights 診断ログからは、パフォーマンスが低下したクエリのクエリ ハッシュが出力されます。 クエリ ハッシュは、パフォーマンスの低下がクエリのコンパイル時間または実行時間が長くなった (その結果、クエリの実行時間が長くなった) ことに関係があるかどうかを示します。

## <a name="timeout-requests"></a>タイムアウト要求

タイムアウト要求のパフォーマンス低下モデルでは、個々のクエリを分析し、クエリの実行レベルでのタイムアウトの増加と、パフォーマンス ベースライン期間と比較した、データベース レベルでの全体的な要求タイムアウトの増加を検出します。

一部のクエリは、実行段階に達する前でもタイムアウトすることがあります。 組み込みの Intelligence では、実行され、中止された worker と要求を利用して、クエリが実行段階に達したかどうかにかかわらず、データベースに到達したすべてのクエリを測定し、分析します。

実行されたクエリのタイムアウト数、または中止された要求 worker 数がシステムで管理されているしきい値を上回ると、診断ログに Intelligent Insights が設定されます。

生成される洞察には、タイムアウトした要求数とタイムアウトしたクエリ数が含まれます。 パフォーマンスの低下は、実行段階でのタイムアウトの増加、またはデータベース レベル全体が提供されたことに関連します。 タイムアウトの増加がデータベース パフォーマンスに影響していると見なされた場合、これらのクエリにはタイムアウトのパフォーマンス低下の問題のフラグが設定されます。

## <a name="excessive-wait-times"></a>過剰な待機時間

過剰な待機時間モデルは、個々のデータベース クエリを監視します。 また、システムで管理される絶対しきい値を超える異常に高いクエリ待機状態を検出します。 次の過剰クエリ待機時間メトリックは、[クエリ ストア待機統計 (sys.query_store_wait_stats)](/sql/relational-databases/system-catalog-views/sys-query-store-wait-stats-transact-sql) を利用して監視されます。

- リソースの上限に到達
- エラスティック プール リソース制限に到達
- 過剰な worker またはセッション スレッド数
- 過剰なデータベース ロック
- メモリ不足
- その他の待機統計

リソース制限またはエラスティック プール リソースの制限への到達は、サブスクリプションまたはエラスティック プールで使用可能なリソースの消費が絶対しきい値を超えて増加したことを示します。 これらの状態は、ワークロード パフォーマンスの低下を示しています。 過剰な worker またはセッションのスレッド数は、開始された worker スレッドまたはセッションの数が絶対しきい値を超えていることを示します。 これらの状態は、ワークロード パフォーマンスの低下を示しています。

過剰なデータベース ロックは、データベースのロックの数が絶対しきい値を超えていることを示します。 この状態は、ワークロード パフォーマンスの低下を示しています。 メモリ不足は、メモリ許可を要求するスレッド数が絶対しきい値を超える条件です。 この状態は、ワークロード パフォーマンスの低下を示しています。

その他の待機統計検出は、クエリ ストア待機統計で測定されるその他のメトリックが絶対しきい値を超えたことを示します。 これらの状態は、ワークロード パフォーマンスの低下を示しています。

過剰な待機時間が検出されると、Intelligent Insights 診断ログは、使用可能なデータに応じて、パフォーマンス低下に影響しているクエリと影響を受けているクエリのハッシュ、クエリが実行を待機する原因となったメトリックの詳細、測定された待機時間を出力します。

## <a name="errored-requests"></a>エラーが発生した要求

エラーが発生した要求のパフォーマンス低下モデルでは、個々のクエリを監視し、ベースライン期間と比較してエラーが発生したクエリ数の増加を検出します。 このモデルでは、組み込みインテリジェンスによって管理されている絶対しきい値を超えた重大な例外も監視されます。 システムでは、データベースに対して行われたクエリ要求の数が自動的に考慮され、監視対象の期間中のワークロードの変化が考慮されます。

行われた全体的な要求数との関連で、エラーが発生した要求について測定された増加がワークロードのパフォーマンスに影響していると見なされる場合、影響を受けているクエリには、エラーが発生した要求のパフォーマンス低下の問題があることを示すフラグが設定されます。

Intelligent Insights ログには、エラーになった要求数が出力されます。 この数値は、エラーになった要求の増加や、監視対象の重大な例外のしきい値とパフォーマンス低下の測定時間を超えたことにパフォーマンスの低下が関連したかどうかを示します。

監視対象の重大な例外のいずれかが、システムによって管理されている絶対しきい値を超えた場合は、インテリジェントな洞察が重大な例外の詳細と共に生成されます。

## <a name="next-steps"></a>次のステップ

- [SQL Analytics を使用したデータベースの監視](../../azure-monitor/insights/azure-sql.md)について説明します。
- [Intelligent Insights を使用したパフォーマンスに関する問題のトラブルシューティング](intelligent-insights-troubleshoot-performance.md)について説明します。