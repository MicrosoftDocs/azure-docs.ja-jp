---
title: 監視とパフォーマンスのチューニング
description: Azure SQL Database と Azure SQL Managed Instance での監視とパフォーマンス チューニングの機能および手法の概要。
services: sql-database
ms.service: sql-db-mi
ms.subservice: performance
ms.custom: sqldbrb=2
ms.devlang: ''
ms.topic: conceptual
author: jovanpop-msft
ms.author: jovanpop
ms.reviewer: wiassaf, sstein
ms.date: 03/17/2021
ms.openlocfilehash: 4c9edaea75215bc4b471cd2e0ac50c4e69546bb0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/30/2021
ms.locfileid: "104592134"
---
# <a name="monitoring-and-performance-tuning-in-azure-sql-database-and-azure-sql-managed-instance"></a>Azure SQL Database と Azure SQL Managed Instance での監視とパフォーマンス チューニング
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

Azure SQL Database と Azure SQL Managed Instance 内のデータベースのパフォーマンスを監視するには、まず、特定のサービス レベルとパフォーマンス レベルの選択時に選択したデータベース パフォーマンスのレベルを基準にして、ワークロードで使用される CPU および IO リソースを監視します。 これを実現するために、Azure SQL Database と Azure SQL Managed Instance では、Azure portal 内で表示できる、または次のいずれかの SQL Server 管理ツールを使用して表示できるリソース メトリックを出力します:[Azure Data Studio](/sql/azure-data-studio/what-is) または [SQL Server Management Studio](/sql/ssms/sql-server-management-studio-ssms) (SSMS)。

Azure SQL Database には、パフォーマンスを向上させるために、インテリジェントなパフォーマンス チューニングの推奨設定と自動チューニング オプションを提供するデータベース アドバイザーが多数用意されています。 また、Query Performance Insight では、単一データベースとプールされたデータベースの CPU と IO の使用率が最も高いクエリについての詳細が表示されます。

Azure SQL Database と Azure SQL Managed Instance では、データベースやソリューションのパフォーマンスのトラブルシューティングを行い、これらを最大化するのに役立つ、人工知能によって支えられる高度な監視およびチューニング機能が提供されます。 使用と分析 (具体的には [SQL Analytics](../../azure-monitor/insights/azure-sql.md) を使用) を行うために、複数の宛先のいずれかに対して、これらの [Intelligent Insights](intelligent-insights-overview.md)、およびその他のデータベース リソース ログとメトリックの[ストリーミング エクスポート](metrics-diagnostic-telemetry-logging-streaming-export-configure.md)を構成することを選択できます。 Azure SQL Analytics は、1 つのビューですべてのデータベースのパフォーマンスを、複数のサブスクリプションにわたって大規模に監視するための先進のクラウド監視ソリューションです。 エクスポートできるログとメトリックの一覧については、[エクスポートの診断テレメトリ](metrics-diagnostic-telemetry-logging-streaming-export-configure.md#diagnostic-telemetry-for-export)に関するページをご覧ください

SQL Server には、[クエリ ストア](/sql/relational-databases/performance/monitoring-performance-by-using-the-query-store)や[動的管理ビュー (DMV)](/sql/relational-databases/system-dynamic-management-views/system-dynamic-management-views) など、SQL Database と SQL Managed Instance によって使用される独自の監視機能と診断機能が用意されています。 さまざまなパフォーマンスの問題を監視するスクリプトについては、[DMV を使用した監視](monitoring-with-dmvs.md)に関する記事をご覧ください。

## <a name="monitoring-and-tuning-capabilities-in-the-azure-portal"></a>Azure portal の監視およびチューニング機能

Azure portal では、Azure SQL Database と Azure SQL Managed Instance により、リソース メトリックの監視が提供されます。 Azure SQL Database はデータベース アドバイザーを提供し、Query Performance Insight はクエリ チューニングの推奨設定とクエリ パフォーマンスの分析情報を提供しています。 Azure portal では、[論理 SQL サーバー](logical-servers.md)、およびその単一データベースとプールされたデータベースに対して自動チューニングを有効にすることができます。

> [!NOTE]
> 使用率が非常に低いデータベースは、ポータルに実際の使用量より少なく表示されることがあります。 double 値を最も近い整数値に変換する場合のテレメトリ生成方法が理由で、0.5 未満の特定の使用量は 0 に丸められます。これにより、生成されたテレメトリの細分性は低下します。 詳細については、「[使用率が低いデータベースおよびエラスティック プールのメトリックの 0 への丸め処理](#low-database-and-elastic-pool-metrics-rounding-to-zero)」を参照してください。

### <a name="monitor-with-sql-insights"></a>SQL insights で監視する

[Azure Monitor SQL insights](../../azure-monitor/insights/sql-insights-overview.md) は、Azure SQL VM 内の Azure SQL マネージド インスタンス、Azure SQL データベース、SQL Server インスタンスを監視するためのツールです。 このサービスでは、リモート エージェントを使用して動的管理ビュー (DMV) からデータをキャプチャして、Azure Log Analytics にルーティングし、ここでそのデータを監視および分析することができます。 このデータは、[Azure Monitor](../../azure-monitor/overview.md) から提供されたビューで表示することも、ログ データに直接アクセスしてクエリを実行したり、傾向を分析したりすることもできます。 Azure Monitor SQL 分析情報の使用を開始するには、「[SQL insights を有効にする](../../azure-monitor/insights/sql-insights-enable.md)」を参照してください。

### <a name="azure-sql-database-and-azure-sql-managed-instance-resource-monitoring"></a>Azure SQL Database と Azure SQL Managed Instance のリソースの監視

Azure portal 内の **[メトリック]** ビューでは、さまざまなリソース メトリックをすばやく監視できます。 これらのメトリックを使用すると、データベースのプロセッサ、メモリ、または IO リソースの使用率が 100% に達しているかどうかを確認できます。 DTU またはプロセッサの使用率が高い場合および IO の割合が高い場合は、ワークロードでより多くの CPU または IO リソースが必要になる可能性があることを示します。 最適化が必要なクエリを示している場合もあります。

  ![リソース メトリック](./media/monitor-tune-overview/resource-metrics.png)

### <a name="database-advisors-in-azure-sql-database"></a>Azure SQL Database 内のデータベース アドバイザー

Azure SQL Database には、単一データベースとプールされたデータベースのパフォーマンス チューニングに関する推奨設定を提供する[データベース アドバイザー](database-advisor-implement-performance-recommendations.md)が含まれています。 これらの推奨設定は、Azure portal と [PowerShell](/powershell/module/az.sql/get-azsqldatabaseadvisor) を使用して入手できます。 また、これらのチューニング推奨設定を Azure SQL Database で自動的に実装できるように、[自動チューニング](automatic-tuning-overview.md)を有効にすることもできます。

### <a name="query-performance-insight-in-azure-sql-database"></a>Azure SQL Database 内の Query Performance Insight

[Query Performance Insight](query-performance-insight-use.md) では、単一データベースとプールされたデータベースに対して実行するクエリのうち、最も負荷が高いものと実行時間が長いもののパフォーマンスが Azure portal に示されます。

### <a name="low-database-and-elastic-pool-metrics-rounding-to-zero"></a>使用率が低いデータベースおよびエラスティック プールのメトリックの 0 への丸め処理

2020 年 9 月以降、使用率が非常に低いデータベースは、ポータルに実際の使用量より少なく表示されることがあります。 double 値を最も近い整数値に変換する場合のテレメトリ生成方法が理由で、0.5 未満の特定の使用量は 0 に丸められます。これにより、生成されたテレメトリの細分性は低下します。

次に例を示します。1 分間に 4 つのデータ ポイント 0.1、0.1、0.1、0.1 があると考えてみましょう。これらの低値は 0、0、0、0 に切り捨てられ、平均は 0 であると示されます。 データ ポイントのいずれかが 0.5 より大きい場合は、0.1、0.1、0.9、0.1 となり、これらは 0、0、1、0 に丸められ、平均は 0.25 であると表示されます。

影響を受けるデータベースのメトリックは次のとおりです。
- cpu_percent
- log_write_percent
- workers_percent
- sessions_percent
- physical_data_read_percent
- dtu_consumption_percent2
- xtp_storage_percent

影響を受けるエラスティック プールのメトリックは次のとおりです。
- cpu_percent
- physical_data_read_percent
- log_write_percent
- memory_usage_percent
- data_storage_percent
- peak_worker_percent
- peak_session_percent
- xtp_storage_percent
- allocated_data_storage_percent


## <a name="generate-intelligent-assessments-of-performance-issues"></a>パフォーマンスの問題のインテリジェントな評価を生成する

Azure SQL Database と Azure SQL Managed Instance の [Intelligent Insights](intelligent-insights-overview.md) では、組み込まれているインテリジェンスを使って、人工知能によりデータベースの使用状況が継続的に監視され、パフォーマンス低下の原因となる破壊的なイベントが検出されます。 Intelligent Insights は、クエリ実行の待機時間、エラー、またはタイムアウトに基づいて、データベースのパフォーマンスの問題を自動的に検出します。 検出されると、詳細な分析が実行され、[問題のインテリジェントな評価](intelligent-insights-troubleshoot-performance.md)を含むリソース ログ (通称 SQLInsights) が生成されます。 このアセスメントは、データベース パフォーマンスの問題の根本原因分析と、可能な場合にはパフォーマンス向上に関する推奨事項で構成されます。

Intelligent Insights は、Azure の組み込みインテリジェンスの固有の機能であり、次の価値を提供します。

- プロアクティブな監視
- カスタマイズされたパフォーマンスの洞察
- データベースのパフォーマンス低下の早期検出
- 検出された問題の根本原因分析
- パフォーマンス向上に関する推奨事項
- 数十万のデータベースのスケールアウト機能
- DevOps リソースと総保有コストに対する良い影響

## <a name="enable-the-streaming-export-of-metrics-and-resource-logs"></a>メトリックとリソース ログのストリーミング エクスポートを有効にする

Intelligent Insights リソース ログを含む複数の宛先のいずれかに対する[診断テレメトリのストリーミング エクスポート](metrics-diagnostic-telemetry-logging-streaming-export-configure.md)を有効にし、構成できます。 この追加の診断テレメトリを使用してパフォーマンスの問題を特定し、解決するには、[SQL Analytics](../../azure-monitor/insights/azure-sql.md) およびその他の機能を使用します。

診断設定を構成して、単一データベース、プールされたデータベース、エラスティック プール、マネージド インスタンス、インスタンス データベースのメトリックおよびリソース ログを次のいずれかの Azure リソースにストリーム配信できます。

### <a name="log-analytics-workspace-in-azure-monitor"></a>Azure Monitor の Log Analytics ワークスペース

[Azure Monitor の Log Analytics ワークスペース](../../azure-monitor/essentials/resource-logs.md#send-to-log-analytics-workspace)にメトリックおよびリソース ログをストリーム配信できます。 ここでストリーム配信されたデータは、[SQL Analytics](../../azure-monitor/insights/azure-sql.md) で使用できます。これは、パフォーマンス レポート、アラート、軽減策の推奨事項を含むデータベースのインテリジェントな監視機能を提供する、クラウドのみの監視ソリューションです。 Log Analytics ワークスペースにストリーム配信されたデータは、収集された他の監視データと組み合わせて分析できます。また、アラートや視覚化などの他の Azure Monitor 機能を利用することもできます。

### <a name="azure-event-hubs"></a>Azure Event Hubs

メトリックとリソース ログは、[Azure Event Hubs](../../azure-monitor/essentials/resource-logs.md#send-to-azure-event-hubs) にストリーム配信できます。 診断テレメトリをイベント ハブにストリーム配信して、次の機能を提供します。

- **サード パーティ製のロギングおよびテレメトリ システムにログをストリーミングする**

  すべてのメトリックとリソース ログを 1 つのイベント ハブにストリーミングして、ログ データをサード パーティの SIEM またはログ分析ツールにパイプします。
- **カスタムのテレメトリおよびログ プラットフォームを構築する**

  高い拡張性の公開サブスクライブを特長とするイベント ハブを使用することで、メトリックとリソース ログをカスタム テレメトリ プラットフォームに柔軟に取り込むことができます。 詳細については、「[Azure Event Hubs でのグローバル スケール テレメトリ プラットフォームの設計とサイズ変更](https://azure.microsoft.com/documentation/videos/build-2015-designing-and-sizing-a-global-scale-telemetry-platform-on-azure-event-Hubs/)」を参照してください。
- **データを Power BI にストリーム配信してサービスの正常性を表示する**

  Event Hubs、Stream Analytics、Power BI を使用して、診断データを Azure サービスのほぼリアルタイムの分析情報に転換します。 このソリューションの詳細については、「[Stream Analytics と Power BI:ストリーミング データのリアルタイム分析ダッシュボード](../../stream-analytics/stream-analytics-power-bi-dashboard.md)」を参照してください。

### <a name="azure-storage"></a>Azure Storage

メトリックとリソース ログを [Azure Storage](../../azure-monitor/essentials/resource-logs.md#send-to-azure-storage) にストリーム配信します。 Azure ストレージを使用して、前述の 2 つのストリーミング オプションの何分の 1 かのわずかなコストで、膨大な量の診断テレメトリをアーカイブできます。

## <a name="use-extended-events"></a>拡張イベントを使用する 

さらに、SQL Server の[拡張イベント](/sql/relational-databases/extended-events/extended-events)を使用して、高度な監視とトラブルシューティングを行うことができます。 拡張イベントのアーキテクチャを使用すると、ユーザーは、パフォーマンスの問題のトラブルシューティングや特定に必要なデータを過不足なく収集できます。 Azure SQL Database の拡張イベントの使用の詳細については、[Azure SQL Database での拡張イベント](xevent-db-diff-from-svr.md)に関するページを参照してください。

## <a name="next-steps"></a>次のステップ

- 単一データベースとプールされたデータベースのインテリジェント パフォーマンスに関する推奨事項の詳細については、「[データベース アドバイザーのパフォーマンスに関する推奨事項](database-advisor-implement-performance-recommendations.md)」を参照してください。
- パフォーマンスの問題の自動診断および根本原因分析を含むデータベース パフォーマンスの自動監視については、[Azure SQL Intelligent Insights](intelligent-insights-overview.md) に関するページを参照してください。