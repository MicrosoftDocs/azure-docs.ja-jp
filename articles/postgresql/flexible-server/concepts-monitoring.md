---
title: 監視とメトリック - Azure Database for PostgreSQL - フレキシブル サーバー
description: この記事では、Azure Database for PostgreSQL - フレキシブル サーバーの監視およびメトリックの機能について説明します。
author: lfittl-msft
ms.author: lufittl
ms.service: postgresql
ms.topic: conceptual
ms.date: 09/23/2020
ms.openlocfilehash: 5e7063cd1ae560fa077bd0b1b1279e4515e70464
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "100579020"
---
# <a name="monitor-metrics-on-azure-database-for-postgresql---flexible-server"></a>Azure Database for PostgreSQL - フレキシブル サーバーでメトリックを監視する

> [!IMPORTANT]
> Azure Database for PostgreSQL - フレキシブル サーバーはプレビュー段階です

サーバーに関する監視データは、ワークロードをトラブルシューティングしたり最適化したりするのに役立ちます。 Azure Database for PostgreSQL には、サーバーの動作の分析情報を提供する各種の監視オプションが用意されています。

## <a name="metrics"></a>メトリック
Azure Database for PostgreSQL には、PostgreSQL サーバーをサポートするリソースの動作に関する洞察を提供する各種のメトリックが用意されています。 各メトリックは 1 分間隔で出力され、最大 [93 日分の履歴](../../azure-monitor/essentials/data-platform-metrics.md#retention-of-metrics)が保持されます。 メトリックにアラートを構成できます。 その他のオプションには、自動化されたアクションの設定、高度な分析の実行、履歴のアーカイブなどがあります。 詳細については、[Azure のメトリックの概要](../../azure-monitor/essentials/data-platform-metrics.md)に関する記事をご覧ください。

### <a name="list-of-metrics"></a>メトリックの一覧
PostgreSQL フレキシブル サーバーでは以下のメトリックを使用できます。


|メトリック|メトリックの表示名|ユニット|説明|
|---|---|---|---|
| active_connections | アクティブな接続 | Count | サーバーへの接続数。 | 
| backup_storage_used | 使用済みバックアップ ストレージ | バイト | 使用されたバックアップ ストレージの量。 このメトリックは、サーバーに設定されているバックアップ保持期間に基づいて保持されているすべてのデータベースの完全バックアップ、差分バックアップ、ログ バックアップによって使用されるストレージの合計を表します。 バックアップの頻度はサービスによって管理されます。 geo 冗長ストレージの場合、バックアップ ストレージの使用量は、ローカル冗長ストレージの 2 倍になります。 |
| connections_failed | 失敗した接続 | Count | 失敗した接続数。 |
| connections_succeeded | Succeeded Connections (成功した接続) | Count | 成功した接続数。 |
| cpu_credits_consumed | 使用済みの CPU クレジット | Count | フレキシブル サーバーによって使用されているクレジットの数。 バースト可能レベルに適用されます。 |
| cpu_credits_remaining | 未使用の CPU クレジット | Count | バーストに使用できるクレジットの数。 バースト可能レベルに適用されます。 |
| cpu_percent | CPU 使用率 | Percent | 使用中の CPU の割合。 | 
| disk_queue_depth | ディスクのキューの深さ | Count | データ ディスクに対する未処理の I/O 操作の数。 |
| iops | IOPS | Count | ディスクに対する 1 秒あたりの I/O 操作の数。 |
| maximum_used_transactionIDs | Maximum Used Transaction IDs (使用されるトランザクション ID の最大数) | Count | 使用中の最大トランザクション ID。 |
| memory_percent | メモリの割合 | Percent | 使用中のメモリの割合。 |
| network_bytes_egress | Network Out | バイト | 送信ネットワーク トラフィックの量。 |
| network_bytes_ingress | Network In | バイト | 受信ネットワーク トラフィックの量。 |
| read_iops | 読み取り IOPS | Count | データ ディスク I/O の 1 秒あたりの読み取り操作の数。 |
| read_throughput | 読み取りスループット | バイト | ディスクから読み取られる 1 秒あたりのバイト数。 |
| storage_free | 空き記憶域 | バイト | 使用可能なストレージ領域の量。 |
| storage_percent | ストレージの割合 | パーセント | 使用されているストレージ領域の割合。 サービスで使用されるストレージには、データベース ファイル、トランザクション ログ、サーバー ログが含まれることがあります。|
| storage_used | 使用されているストレージ | バイト | 使用されているストレージ領域の割合。 サービスで使用されるストレージには、データベース ファイル、トランザクション ログ、サーバー ログが含まれることがあります。 |
| txlogs_storage_used | 使用されているトランザクション ログ ストレージ | バイト | トランザクション ログによって使用されているストレージ領域の量。 | 
| write_throughput | 書き込みスループット | バイト | ディスクに書き込まれた 1 秒あたりのバイト数。 |
| write_iops | 書き込み IOPS | Count | データ ディスク I/O の 1 秒あたりの書き込み操作の数。 |

## <a name="server-logs"></a>サーバー ログ
Azure Database for PostgreSQL では、Postgres の標準ログを構成してアクセスできます。 ログの詳細については、[ログ記録の概念に関するドキュメント](concepts-logging.md)を参照してください。
