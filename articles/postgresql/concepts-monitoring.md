---
title: Azure Database for PostgreSQL (単一サーバー) を監視およびチューニングする
description: この記事では、Azure Database for PostgreSQL (単一サーバー) の監視およびチューニング機能について説明します。
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 06/19/2019
ms.openlocfilehash: c69ffb30a37de8e6dc3e15aa1f7dcd6a9311d614
ms.sourcegitcommit: a52d48238d00161be5d1ed5d04132db4de43e076
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/20/2019
ms.locfileid: "67274289"
---
# <a name="monitor-and-tune-azure-database-for-postgresql---single-server"></a>Azure Database for PostgreSQL (単一サーバー) を監視およびチューニングする
サーバーに関する監視データは、ワークロードをトラブルシューティングしたり最適化したりするのに役立ちます。 Azure Database for PostgreSQL には、サーバーの動作の分析情報を提供する各種の監視オプションが用意されています。

## <a name="metrics"></a>メトリック
Azure Database for PostgreSQL には、PostgreSQL サーバーをサポートするリソースの動作に関する洞察を提供する各種のメトリックが用意されています。 各メトリックは 1 分間隔で出力されます。履歴は最大 30 日分です。 メトリックにアラートを構成できます。 詳細な手順については、[アラートの設定方法](howto-alert-on-metric.md)に関する記事をご覧ください。 その他のタスクとして、自動化されたアクションの設定、高度な分析の実行、履歴のアーカイブなどがあります。 詳細については、[Azure のメトリックの概要](../monitoring-and-diagnostics/monitoring-overview-metrics.md)に関する記事をご覧ください。

### <a name="list-of-metrics"></a>メトリックの一覧
Azure Database for PostgreSQL では、次のメトリックを使用できます。

|メトリック|メトリックの表示名|単位|説明|
|---|---|---|---|
|cpu_percent|CPU 使用率|Percent|使用されている CPU の割合|
|memory_percent|メモリの割合|Percent|使用されているメモリの割合|
|io_consumption_percent|IO の割合|Percent|使用されている IO の割合|
|storage_percent|ストレージの割合|Percent|サーバーの最大数のうち使用されているストレージの割合|
|storage_used|使用済みストレージ|Bytes|使用されているストレージの量。 サービスで使用されるストレージには、データベース ファイル、トランザクション ログ、サーバー ログが含まれることがあります。|
|storage_limit|ストレージの制限|Bytes|このサーバーの最大のストレージ|
|serverlog_storage_percent|サーバー ログ ストレージの割合|Percent|サーバーの最大サーバー ログ ストレージのうち、使用されているサーバー ログ ストレージの割合。|
|serverlog_storage_usage|サーバー ログ ストレージの使用量|Bytes|使用されているサーバー ログ ストレージの量。|
|serverlog_storage_limit|サーバー ログ ストレージの上限|Bytes|このサーバーの最大サーバー ログ ストレージ。|
|active_connections|アクティブな接続|Count|サーバーへのアクティブな接続の数|
|connections_failed|失敗した接続|Count|サーバーへの失敗した接続の数|
|network_bytes_egress|ネットワーク送信|Bytes|アクティブな接続全体のネットワーク送信。|
|network_bytes_ingress|Network In|Bytes|アクティブな接続全体のネットワーク受信。|
|backup_storage_used|使用済みバックアップ ストレージ|Bytes|使用されているバックアップ ストレージの量。|
|pg_replica_log_delay_in_bytes|Max Lag Across Replicas (レプリカ間の最大ラグ)|Bytes|マスターと最も遅れているレプリカの間のバイト単位でのラグ。 このメトリックは、マスター サーバーのみで使用できます。|
|pg_replica_log_delay_in_seconds|Replica Lag (レプリカ ラグ)|Seconds|最後に再生されたトランザクションからの時間。 このメトリックは、レプリカ サーバーのみで使用できます。|

## <a name="server-logs"></a>サーバー ログ
サーバーでのログ記録を有効にできます。 これらのログは、[Azure Monitor ログ](../azure-monitor/log-query/log-query-overview.md)、Event Hubs、およびストレージ アカウントでの Azure 診断ログを通じて入手することもできます。 ログ記録の詳細については、[サーバー ログ](concepts-server-logs.md)に関するページをご覧ください。

## <a name="query-store"></a>クエリ ストア
[クエリ ストア](concepts-query-store.md)は、クエリ ランタイム統計や待機イベントなど、一定期間のクエリ パフォーマンスを追跡記録します。 この機能により、**azure_sys** という名前のシステム データベースの query_store スキーマにクエリ ランタイム パフォーマンス情報が保持されます。 さまざまな構成ノブを介してデータのコレクションとストレージを制御できます。

## <a name="query-performance-insight"></a>Query Performance Insight
[Query Performance Insight](concepts-query-performance-insight.md) はクエリ ストアと連動し、データを視覚化します。視覚化したデータには Azure portal からアクセスできます。 これらのグラフにより、パフォーマンスに影響を与える主要なクエリを特定できます。 Query Performance Insight には、Azure Database for PostgreSQL サーバーのポータル ページの **[サポート + トラブルシューティング]** セクションからアクセスできます。

## <a name="performance-recommendations"></a>パフォーマンスに関する推奨事項
[パフォーマンスの推奨事項](concepts-performance-recommendations.md)機能によって、ワークロード パフォーマンス改善の機会が特定されます。 パフォーマンスの推奨事項によって、ワークロードのパフォーマンスを改善する可能性がある新しいインデックスを作成するための推奨事項が提供されます。 推奨インデックスを作成するために、この機能は、クエリ ストアから報告されたスキーマ、ワークロードなどのさまざまなデータベースの特性を考慮します。 顧客は、パフォーマンスに関する推奨事項を実装した後、パフォーマンスをテストし、変更の影響を評価する必要があります。 

## <a name="next-steps"></a>次の手順
- メトリックに対するアラートの作成のガイダンスについては、[アラートを設定する方法](howto-alert-on-metric.md)に関するページをご覧ください。
- Azure Portal、REST API、または CLI を使用してメトリックへのアクセスおよびメトリックのエクスポートを行う方法の詳細については、[Azure のメトリックの概要](../monitoring-and-diagnostics/monitoring-overview-metrics.md)に関する記事をご覧ください。
- [サーバーの監視のベスト プラクティス](https://azure.microsoft.com/blog/best-practices-for-alerting-on-metrics-with-azure-database-for-postgresql-monitoring/)に関するブログをお読みください。
