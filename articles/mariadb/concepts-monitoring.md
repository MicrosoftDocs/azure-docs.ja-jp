---
title: 監視 - Azure Database for MariaDB
description: この記事では、Azure Database for MariaDB での監視およびアラート生成のためのメトリック (CPU、ストレージ、および接続の統計を含む) について説明します。
author: savjani
ms.author: pariks
ms.service: mariadb
ms.topic: conceptual
ms.custom: references_regions
ms.date: 10/21/2020
ms.openlocfilehash: f63b0b523184917097c73c852a7624b366fbbb24
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "100595211"
---
# <a name="monitoring-in-azure-database-for-mariadb"></a>Azure Database for MariaDB での監視
サーバーに関する監視データは、ワークロードをトラブルシューティングしたり最適化したりするのに役立ちます。 Azure Database for MariaDB には、サーバーの動作への洞察を提供する各種のメトリックが用意されています。

## <a name="metrics"></a>メトリック
すべての Azure メトリックは 1 分間隔で、各メトリックの 30 日間の履歴が保持されます。 メトリックにアラートを構成できます。 その他のタスクとして、自動化されたアクションの設定、高度な分析の実行、履歴のアーカイブなどがあります。 詳細については、[Azure のメトリックの概要](../azure-monitor/data-platform.md)に関する記事をご覧ください。

詳細な手順については、[アラートの設定方法](howto-alert-metric.md)に関する記事をご覧ください。

### <a name="list-of-metrics"></a>メトリックの一覧
これらのメトリックは、Azure Database for MariaDB に使用できます。

|メトリック|メトリックの表示名|ユニット|説明|
|---|---|---|---|
|cpu_percent|CPU 使用率|Percent|使用されている CPU の割合|
|memory_percent|メモリの割合|Percent|使用されているメモリの割合|
|io_consumption_percent|IO の割合|Percent|使用されている IO の割合 (Basic レベルのサーバーには適用されません)|
|storage_percent|ストレージの割合|Percent|サーバーの最大数のうち使用されているストレージの割合|
|storage_used|使用済みストレージ|バイト|使用されているストレージの量。 サービスで使用されるストレージには、データベース ファイル、トランザクション ログ、サーバー ログが含まれることがあります。|
|serverlog_storage_percent|サーバー ログ ストレージの割合|Percent|サーバーの最大サーバー ログ ストレージのうち、使用されているサーバー ログ ストレージの割合。|
|serverlog_storage_usage|サーバー ログ ストレージの使用量|バイト|使用されているサーバー ログ ストレージの量。|
|serverlog_storage_limit|サーバー ログ ストレージの上限|バイト|このサーバーの最大サーバー ログ ストレージ。|
|storage_limit|ストレージの制限|バイト|このサーバーの最大のストレージ|
|active_connections|アクティブな接続|Count|サーバーへのアクティブな接続の数|
|connections_failed|失敗した接続|Count|サーバーへの失敗した接続の数|
|seconds_behind_master|レプリケーションのラグ (秒単位)|Count|レプリカ サーバーがソース サーバーから遅延している秒数。 (Basic レベルのサーバーには適用されません)|
|network_bytes_egress|Network Out|バイト|アクティブな接続全体のネットワーク送信。|
|network_bytes_ingress|Network In|バイト|アクティブな接続全体のネットワーク受信。|
|backup_storage_used|使用済みバックアップ ストレージ|バイト|使用されているバックアップ ストレージの量。 このメトリックは、サーバーに設定されているバックアップ保持期間に基づいて保持されているすべてのデータベースの完全バックアップ、差分バックアップ、ログ バックアップによって使用されるストレージの合計を表します。 バックアップの頻度はサービスによって管理され、[概念に関する記事](concepts-backup.md)で説明されています。 geo 冗長ストレージの場合、バックアップ ストレージの使用量は、ローカル冗長ストレージの 2 倍になります。|

## <a name="server-logs"></a>サーバー ログ

サーバーで低速クエリ ログを有効にできます。 これらのログは、Azure Monitor ログ、Event Hubs、およびストレージ アカウントでの Azure 診断ログを通じて入手することもできます。 ログ記録の詳細については、[サーバー ログ](concepts-server-logs.md)に関するページをご覧ください。

## <a name="query-store"></a>クエリ ストア

[クエリ ストア](concepts-query-store.md)は、クエリ ランタイム統計や待機イベントなど、一定期間のクエリ パフォーマンスを追跡記録します。 この機能は、**mysql** スキーマにクエリ ランタイムのパフォーマンス情報を保持します。 さまざまな構成ノブを介してデータのコレクションとストレージを制御できます。

## <a name="query-performance-insight"></a>Query Performance Insight

[Query Performance Insight](concepts-query-performance-insight.md) はクエリ ストアと連動し、データを視覚化します。視覚化したデータには Azure portal からアクセスできます。 これらのグラフにより、パフォーマンスに影響を与える主要なクエリを特定できます。 Query Performance Insight には、Azure Database for MariaDB サーバーのポータル ページの「**インテリジェント パフォーマンス**」セクションからアクセスできます。

## <a name="performance-recommendations"></a>パフォーマンスに関する推奨事項

[パフォーマンスの推奨事項](concepts-performance-recommendations.md)機能によって、ワークロード パフォーマンス改善の機会が特定されます。 パフォーマンスの推奨事項によって、ワークロードのパフォーマンスを改善する可能性がある新しいインデックスを作成するための推奨事項が提供されます。 推奨インデックスを作成するために、この機能は、クエリ ストアから報告されたスキーマ、ワークロードなどのさまざまなデータベースの特性を考慮します。 顧客は、パフォーマンスに関する推奨事項を実装した後、パフォーマンスをテストし、変更の影響を評価する必要があります。

## <a name="planned-maintenance-notification"></a>計画メンテナンスの通知

[計画メンテナンスの通知](./concepts-planned-maintenance-notification.md)によって、Azure Database for MariaDB に対して今後予定されているメンテナンスに関するアラートを受信できます。 これらの通知は [Service Health の](../service-health/overview.md)計画メンテナンスに統合されており、サブスクリプションに対してスケジュールされたすべてのメンテナンスを 1 か所に表示できます。 また、異なるリソースに対しては異なる連絡先が必要になる場合があるため、さまざまなリソース グループに対して適切なユーザーへの通知をスケーリングすることも可能です。 今後のメンテナンスに関する通知は、イベントの 72 時間前に受信します。

通知の設定方法の詳細については、[計画メンテナンスの通知](./concepts-planned-maintenance-notification.md)に関するドキュメントを参照してください。

## <a name="next-steps"></a>次のステップ

- Azure Portal、REST API、または CLI を使用してメトリックへのアクセスおよびメトリックのエクスポートを行う方法の詳細については、[Azure のメトリックの概要](../azure-monitor/data-platform.md)に関する記事をご覧ください。
- メトリックに対するアラートの作成のガイダンスについては、[アラートを設定する方法](howto-alert-metric.md)に関するページをご覧ください。
- Azure Database for MariaDB での[計画メンテナンスの通知](./concepts-planned-maintenance-notification.md)に関するドキュメントを参照してください。