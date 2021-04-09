---
title: 構成および管理に関するコンテンツ参照
description: Azure SQL Database の構成および管理について説明するコンテンツの参照を見つけます。
services: sql-database
ms.service: sql-database
ms.subservice: single-database
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: guide
author: jovanpop-msft
ms.author: jovanpop
ms.reviewer: sstein
ms.date: 01/14/2020
ms.openlocfilehash: 19cec1deeef344ea5897061db0fec88066b90b05
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "92791768"
---
# <a name="configure-and-manage-content-reference---azure-sql-database"></a>構成と管理に関するコンテンツ参照 - Azure SQL Database
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

この記事には、Azure SQL Database を管理および構成する際に役立つさまざまなガイド、スクリプト、説明へのコンテンツ参照が含まれています。 

## <a name="load-data"></a>データの読み込み

- [SQL データベースへの移行](migrate-to-database-from-sql-server.md)
- [移行後に SQL Database を管理する](manage-data-after-migrating-to-database.md)方法を確認する。
- [データベースをコピーする](database-copy.md)
- [BACPAC からの DB のインポート](database-import.md)
- [BACPAC への DB のエクスポート](database-export.md)
- [BCP を使用したデータの読み込み](../load-from-csv-with-bcp.md)
- [ADF を使用したデータの読み込み](../../data-factory/connector-azure-sql-database.md?toc=/azure/sql-database/toc.json)

## <a name="configure-features"></a>機能の構成

- [Azure Active Directory (Azure AD) 認証を構成する](authentication-aad-configure.md)
- [条件付きアクセスを構成する](conditional-access-configure.md)
- [Multi-factor Azure AD 認証](authentication-mfa-ssms-overview.md)
- [Multi-Factor Authentication を構成する](authentication-mfa-ssms-configure.md)
- [一時的なアイテム保持ポリシーの構成](temporal-tables-retention-policy.md)
- [BYOK を使用した TDE の構成](transparent-data-encryption-byok-configure.md)
- [TDE BYOK キーの回転](transparent-data-encryption-byok-key-rotation.md)
- [TDE 保護機能の削除](transparent-data-encryption-byok-remove-tde-protector.md)
- [インメモリ OLTP の構成](../in-memory-oltp-configure.md)
- [Azure Automation の構成](automation-manage.md)
- [トランザクション レプリケーションを構成](replication-to-sql-database.md)して、データベース間でお客様のデータをレプリケートする。
- [脅威検出を構成](threat-detection-configure.md)して、SQL インジェクションや疑わしい場所からのアクセスなど、疑わしいアクティビティが Azure SQL Database によって特定されるようにする。
- [動的データ マスクを構成](dynamic-data-masking-configure-portal.md)して、お客様の機密データを保護する。
- データベース対して[バックアップ保有期間を構成](long-term-backup-retention-configure.md)し、お客様のバックアップを Azure Blob Storage に保持する。 
- [geo レプリケーションを構成](active-geo-replication-overview.md)して、お客様のデータベースのレプリカを別のリージョンに保持する。
- [geo レプリカのセキュリティを構成する](active-geo-replication-security-configure.md)。

## <a name="monitor-and-tune-your-database"></a>データベースの監視と調整

- [手動チューニング](performance-guidance.md)
- [DMV を使用したパフォーマンスの監視](monitoring-with-dmvs.md)
- [クエリ ストアを使用したパフォーマンスの監視](/sql/relational-databases/performance/best-practice-with-the-query-store#Insight)
- お客様のワークロードのパフォーマンスが Azure SQL Database によって最適化されるよう、[自動チューニングを有効にする](automatic-tuning-enable.md)。
- [自動チューニングの電子メール通知を有効](automatic-tuning-email-notifications-configure.md)にして、チューニング推奨事項に関する情報を取得する。
- [パフォーマンスに関する推奨事項を適用](database-advisor-find-recommendations-portal.md)してお客様のデータベースを最適化する。
- Azure SQL Database から通知を受け取るために[アラートを作成](alerts-insights-configure-portal.md)する。
- アプリケーションとデータベースの間で接続に関する問題が発生したことに気付いた場合に、[接続のトラブルシューティング](troubleshoot-common-errors-issues.md)を行う。 また、[接続の問題には Resource Health](resource-health-to-troubleshoot-connectivity.md) を使用することもできます。
- [Intelligent Insights を使用したパフォーマンスのトラブルシューティング](intelligent-insights-troubleshoot-performance.md)
- [ファイル領域を管理](file-space-manage.md)して、お客様のデータベースのストレージ使用量を監視する。
- [Intelligent Insights 診断ログの使用](intelligent-insights-use-diagnostics-log.md)
- [インメモリ OLTP スペースの監視](../in-memory-oltp-monitor-space.md)

### <a name="extended-events"></a>拡張イベント

- [拡張イベント](xevent-db-diff-from-svr.md)
- [イベント ファイルへの拡張イベントの格納](xevent-code-event-file.md)
- [リング バッファーへの拡張イベントの格納](xevent-code-ring-buffer.md)

## <a name="query-distributed-data"></a>分散データにクエリを実行する

- 複数のデータベース全体で[垂直方向にパーティション分割されたデータにクエリを実行](elastic-query-getting-started-vertical.md)する。
- [スケールアウトされたデータ層全体をレポートする](elastic-query-horizontal-partitioning.md)。
- [スキーマが異なるテーブル全体に対してクエリを実行する](elastic-query-vertical-partitioning.md)。

### <a name="data-sync"></a>データ同期

- [SQL データ同期](sql-data-sync-data-sql-server-sql-database.md)
- [データ同期エージェント](sql-data-sync-agent-overview.md)
- [スキーマ変更のレプリケート](sql-data-sync-update-sync-schema.md)
- [OMS で監視する](./monitor-tune-overview.md)
- [データ同期のベスト プラクティス](sql-data-sync-best-practices.md)
- [データ同期のトラブルシューティング](sql-data-sync-troubleshoot.md)

## <a name="elastic-database-jobs"></a>Elastic Database ジョブ

- PowerShell を使用して Elastic Database ジョブを[作成および管理](elastic-jobs-powershell-create.md)する。
- Transact-SQL を使用して Elastic Database ジョブを[作成および管理](elastic-jobs-tsql-create-manage.md)する。
- [古いエラスティック ジョブから移行する](elastic-jobs-migrate.md)。

## <a name="database-sharding"></a>データベース シャーディング

- [エラスティック データベース クライアント ライブラリをアップグレードする](elastic-scale-upgrade-client-library.md)。
- [シャード化されたアプリを作成する](elastic-scale-get-started.md)。
- [水平方向にシャード化されたデータにクエリを実行する](elastic-query-getting-started.md)。
- [マルチシャード クエリ](elastic-scale-multishard-querying.md)を実行する。
- [シャード化されたデータを移動する](elastic-scale-configure-deploy-split-and-merge.md)。
- データベース シャードの[セキュリティを構成](elastic-scale-split-merge-security-configuration.md)する。
- データベース シャードの現在のセットに[シャードを追加する](elastic-scale-add-a-shard.md)。
- [シャード マップに関する問題を修正する](elastic-database-recovery-manager.md)。
- [シャード化された DB を移行する](elastic-convert-to-use-elastic-tools.md)。
- [カウンターを作成する](elastic-database-perf-counters.md)。
- [Entity Framework を使用](elastic-scale-use-entity-framework-applications-visual-studio.md)して、シャード化されたデータにクエリを実行する。
- [Dapper Framework を使用](elastic-scale-working-with-dapper.md)して、シャード化されたデータにクエリを実行する。

## <a name="develop-applications"></a>アプリケーションを開発する

- [接続](connect-query-content-reference-guide.md#libraries)
- [Spark コネクタの使用](spark-connector.md)
- [アプリの認証](application-authentication-get-client-id-keys.md)
- [バッチ処理を使用したパフォーマンスの向上](../performance-improve-use-batching.md)
- [接続のガイダンス](troubleshoot-common-connectivity-issues.md)
- [DNS エイリアス](dns-alias-overview.md)
- [DNS エイリアスの PowerShell の設定](dns-alias-powershell-create.md)
- [ポート - ADO.NET](adonet-v12-develop-direct-route-ports.md)
- [C と C++](develop-cplusplus-simple.md)
- [Excel](connect-excel.md)

## <a name="design-applications"></a>アプリケーションの設計

- [ディザスター リカバリーの設計](designing-cloud-solutions-for-disaster-recovery.md)
- [エラスティック プールの設計](disaster-recovery-strategies-for-applications-with-elastic-pool.md)
- [アプリのアップグレードの設計](manage-application-rolling-upgrade.md)

### <a name="design-multi-tenant-software-as-a-service-saas-applications"></a>マルチテナント SaaS (サービスとしてのソフトウェア) アプリケーションを設計する

- [SaaS の設計パターン](saas-tenancy-app-design-patterns.md)
- [SaaS Video Indexer](saas-tenancy-video-index-wingtip-brk3120-20171011.md)
- [SaaS アプリのセキュリティ](saas-tenancy-elastic-tools-multi-tenant-row-level-security.md)

## <a name="next-steps"></a>次のステップ

- [Azure SQL Managed Instance の攻略ガイド](../managed-instance/how-to-content-reference-guide.md)の詳細を確認する