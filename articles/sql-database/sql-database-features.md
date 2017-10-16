---
title: "Azure SQL Database の機能の概要 | Microsoft Docs"
description: "このページには、Azure SQL Database の論理サーバーとデータベースについての概要が記載され、各機能へのリンク付きの機能のサポート マトリックスが含まれています。"
services: sql-database
documentationcenter: na
author: CarlRabeler
manager: jhubbard
editor: 
ms.assetid: d1a46fa4-53d2-4d25-a0a7-92e8f9d70828
ms.service: sql-database
ms.custom: DBs & servers
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-management
ms.date: 09/22/2017
ms.author: carlrab
ms.translationtype: HT
ms.sourcegitcommit: c3a2462b4ce4e1410a670624bcbcec26fd51b811
ms.openlocfilehash: 59bb9778c89142212708b660fb200de3f0f1f9d4
ms.contentlocale: ja-jp
ms.lasthandoff: 09/25/2017

---

# <a name="azure-sql-database-features"></a>Azure SQL Database の機能

Azure SQL Database は、SQL Server と共通コード ベースを共有します。また、データベース レベルでほとんどの同じ機能をサポートします。 Azure SQL Database と SQL Server の機能の主な違いは、インスタンス レベルにあります。 

Azure SQL Database には継続的に機能が追加されます。 したがって、Azure のサービス更新情報に関する Web ページにアクセスし、次のフィルターを使用することをお勧めします。

* サービスを [[SQL Database]](https://azure.microsoft.com/updates/?service=sql-database)でフィルター処理します。
* SQL Database の機能について、[General Availability] [(GA) のアナウンス](http://azure.microsoft.com/updates/?service=sql-database&update-type=general-availability) でフィルター処理します。

## <a name="sql-server-and-sql-database-feature-support"></a>SQL Server と SQL Database 機能のサポート

次の表は、SQL Server の主な機能の一覧です。各機能がサポートされるかどうかの情報と、機能の詳細情報のリンクについてまとめています。 既存のデータベース ソリューションを移行するときに考慮する必要がある Transact-SQL の相違点については、「[SQL Database への移行時に Transact-SQL の相違点を解決する](sql-database-transact-sql-information.md)」を参照してください。


| **SQL Server 機能** | **Azure SQL Database でのサポート** | 
| --- | --- |  
| [常に暗号化](https://docs.microsoft.com/sql/relational-databases/security/encryption/always-encrypted-database-engine) | ○ - [証明書ストア](sql-database-always-encrypted.md)と[キー コンテナー](sql-database-always-encrypted-azure-key-vault.md)に関する記事を参照してください|
| [AlwaysOn 可用性グループ](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/always-on-availability-groups-sql-server) | × - [フェールオーバー グループとアクティブ geo レプリケーション](sql-database-geo-replication-overview.md)に関する記事を参照してください |
| [データベースの接続](https://docs.microsoft.com/sql/relational-databases/databases/attach-a-database) | いいえ |
| [アプリケーション ロール](https://docs.microsoft.com/sql/relational-databases/security/authentication-access/application-roles) | あり |
| [BACPAC ファイル (エクスポート)](https://docs.microsoft.com/sql/relational-databases/data-tier-applications/export-a-data-tier-application) | ○ - [SQL Database のエクスポート](sql-database-export.md)に関する記事を参照してください |
| [BACPAC ファイル (インポート)](https://docs.microsoft.com/sql/relational-databases/data-tier-applications/import-a-bacpac-file-to-create-a-new-user-database) | ○ - [SQL Database のインポート](sql-database-import.md)に関する記事を参照してください |
| [BACKUP コマンド](https://docs.microsoft.com/sql/t-sql/statements/backup-transact-sql) | × - [自動バックアップ](sql-database-automated-backups.md)に関する記事を参照してください |
| [組み込み関数](https://docs.microsoft.com/sql/t-sql/functions/functions) | ほとんどの場合 - 個々の関数に関する記事を参照してください |
| [変更データのキャプチャ](https://docs.microsoft.com/sql/relational-databases/track-changes/about-change-data-capture-sql-server) | いいえ |
| [変更の追跡](https://docs.microsoft.com/sql/relational-databases/track-changes/about-change-tracking-sql-server) | あり |
| [照合順序ステートメント](https://docs.microsoft.com/sql/t-sql/statements/collations) | あり |
| [列ストア インデックス](https://docs.microsoft.com/sql/relational-databases/indexes/columnstore-indexes-overview) | ○ - [Premium Edition のみ](https://docs.microsoft.com/sql/relational-databases/indexes/columnstore-indexes-overview) |
| [共通言語ランタイム (CLR)](https://docs.microsoft.com/sql/relational-databases/clr-integration/common-language-runtime-clr-integration-programming-concepts) | いいえ |
| [包含データベース](https://docs.microsoft.com/sql/relational-databases/databases/contained-databases) | あり |
| [包含ユーザー](https://docs.microsoft.com/sql/relational-databases/security/contained-database-users-making-your-database-portable) | あり |
| [フロー制御言語のキーワード](https://docs.microsoft.com/sql/t-sql/language-elements/control-of-flow) | あり |
| [複数データベース間のクエリ](https://docs.microsoft.com/sql/relational-databases/in-memory-oltp/cross-database-queries) | 部分的 - [エラスティック クエリ](sql-database-elastic-query-overview.md)に関する記事を参照してください |
| [カーソル](https://docs.microsoft.com/sql/t-sql/language-elements/cursors-transact-sql) | あり | 
| [データ圧縮](https://docs.microsoft.com/sql/relational-databases/data-compression/data-compression) | あり |
| [データベース メール](https://docs.microsoft.com/sql/relational-databases/database-mail/database-mail) | いいえ |
| [データベース ミラーリング](https://docs.microsoft.com/sql/database-engine/database-mirroring/database-mirroring-sql-server) | いいえ |
| [データベースの構成設定](https://docs.microsoft.com/sql/t-sql/statements/alter-database-scoped-configuration-transact-sql) | あり |
| [Data Quality Services (DQS)](https://docs.microsoft.com/sql/data-quality-services/data-quality-services) | いいえ |
| [データベース スナップショット](https://docs.microsoft.com/sql/relational-databases/databases/database-snapshots-sql-server) | いいえ |
| [データ型](https://docs.microsoft.com/sql/t-sql/data-types/data-types-transact-sql) | あり |  
| [DBCC ステートメント](https://docs.microsoft.com/sql/t-sql/database-console-commands/dbcc-transact-sql) | ほとんどの場合 - 個々のステートメントに関する記事を参照してください |
| [DDL ステートメント](https://docs.microsoft.com/sql/t-sql/statements/statements) | ほとんどの場合 - 個々のステートメントに関する記事を参照してください
| [DDL トリガー](https://docs.microsoft.com/sql/relational-databases/triggers/ddl-triggers) | データベースのみ |
| [分散トランザクション - MS DTC](https://docs.microsoft.com/sql/relational-databases/native-client-ole-db-transactions/supporting-distributed-transactions) | × - [エラスティック トランザクション](sql-database-elastic-transactions-overview.md)に関する記事を参照してください |
| [DML ステートメント](https://docs.microsoft.com/sql/t-sql/queries/queries) | ほとんどの場合 - 個々のステートメントに関する記事を参照してください |
| [DML トリガー](https://docs.microsoft.com/sql/relational-databases/triggers/dml-triggers) |
| [DMV](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/system-dynamic-management-views) | 一部 - 個々の DMV に関する記事を参照してください |
| [イベント通知](https://docs.microsoft.com/sql/relational-databases/service-broker/event-notifications) | × - [アラート](sql-database-insights-alerts-portal.md)に関する記事を参照してください |
| [式](https://docs.microsoft.com/sql/t-sql/language-elements/expressions-transact-sql) |あり |
| [拡張イベント](https://docs.microsoft.com/sql/relational-databases/extended-events/extended-events) | 一部 - 個々のイベントに関する記事を参照してください |
| [拡張ストアド プロシージャ](https://docs.microsoft.com/sql/relational-databases/extended-stored-procedures-programming/creating-extended-stored-procedures) | いいえ |
| [ファイルおよびファイル グループ](https://docs.microsoft.com/sql/relational-databases/databases/database-files-and-filegroups) | プライマリ ファイル グループのみ |
| [FileStream](https://docs.microsoft.com/sql/relational-databases/blob/filestream-sql-server) | いいえ |
| [フルテキスト検索](https://docs.microsoft.com/sql/relational-databases/search/full-text-search) | サード パーティのワード ブレーカーはサポートされていません |
| [関数](https://docs.microsoft.com/sql/t-sql/functions/functions) | ほとんどの場合 - 個々の関数に関する記事を参照してください |
| [グラフの処理](/sql/relational-databases/graphs/sql-graph-overview) | あり |
| [メモリ内の最適化](https://docs.microsoft.com/sql/relational-databases/in-memory-oltp/in-memory-oltp-in-memory-optimization) | ○ - [Premium Edition のみ](sql-database-in-memory.md) |
| [JSON データのサポート](https://docs.microsoft.com/sql/relational-databases/json/json-data-sql-server) | あり |
| [言語要素](https://docs.microsoft.com/sql/t-sql/language-elements/language-elements-transact-sql) | ほとんどの場合 - 個々の要素に関する記事を参照してください |  
| [リンク サーバー](https://docs.microsoft.com/sql/relational-databases/linked-servers/linked-servers-database-engine) | × - [エラスティック クエリ](sql-database-elastic-query-horizontal-partitioning.md)に関する記事を参照してください |
| [ログ配布](https://docs.microsoft.com/sql/database-engine/log-shipping/about-log-shipping-sql-server) | × - [フェールオーバー グループとアクティブ geo レプリケーション](sql-database-geo-replication-overview.md)に関する記事を参照してください |
| [マスター データ サービス (MDS)](https://docs.microsoft.com/sql/master-data-services/master-data-services-overview-mds) | いいえ |
| [一括インポートでの最小ログ記録](https://docs.microsoft.com/sql/relational-databases/import-export/prerequisites-for-minimal-logging-in-bulk-import) | いいえ |
| [システム データの変更](https://docs.microsoft.com/sql/relational-databases/databases/system-databases) | いいえ |
| [オンライン インデックス操作](https://docs.microsoft.com/sql/relational-databases/indexes/perform-index-operations-online) | サポートされています - トランザクションのサイズがサービス レベルによって制限されます |
| [演算子](https://docs.microsoft.com/sql/t-sql/language-elements/operators-transact-sql) | ほとんどの場合 - 個々の演算子に関する記事を参照してください |
| [データベースのポイントインタイム リストア](https://docs.microsoft.com/sql/relational-databases/backup-restore/restore-a-sql-server-database-to-a-point-in-time-full-recovery-model) | ○ - [SQL Database の復旧](sql-database-recovery-using-backups.md#point-in-time-restore)に関する記事を参照してください |
| [PolyBase](https://docs.microsoft.com/sql/relational-databases/polybase/polybase-guide) | いいえ |
| [ポリシーベースの管理](https://docs.microsoft.com/sql/relational-databases/policy-based-management/administer-servers-by-using-policy-based-management) | いいえ |
| [述語](https://docs.microsoft.com/sql/t-sql/queries/predicates) | ほとんどの場合 - 個々の述語に関する記事を参照してください |
| [R Services](https://docs.microsoft.com/sql/advanced-analytics/r-services/sql-server-r-services) | プレビュー リリース: [Machine Learning の新機能](https://docs.microsoft.com/sql/advanced-analytics/what-s-new-in-sql-server-machine-learning-services) に関する記事をご覧ください  |
| [リソース ガバナー](https://docs.microsoft.com/sql/relational-databases/resource-governor/resource-governor) | いいえ |
| [RESTORE ステートメント](https://docs.microsoft.com/sql/t-sql/statements/restore-statements-for-restoring-recovering-and-managing-backups-transact-sql) | いいえ | 
| [バックアップからデータベースを復元する](https://docs.microsoft.com/sql/relational-databases/backup-restore/back-up-and-restore-of-sql-server-databases#restore-data-backups) | 組み込みのバックアップからのみ - [SQL Database の復旧](sql-database-recovery-using-backups.md)に関する記事を参照してください |
| [行レベルのセキュリティ](https://docs.microsoft.com/sql/relational-databases/security/row-level-security) | あり |
| [セマンティック検索](https://docs.microsoft.com/sql/relational-databases/search/semantic-search-sql-server) | いいえ |
| [シーケンス番号](https://docs.microsoft.com/sql/relational-databases/sequence-numbers/sequence-numbers) | あり |
| [Service Broker](https://docs.microsoft.com/sql/database-engine/configure-windows/sql-server-service-broker) | いいえ |
| [サーバーの構成設定](https://docs.microsoft.com/sql/database-engine/configure-windows/server-configuration-options-sql-server) | いいえ |
| [SET ステートメント](https://docs.microsoft.com/sql/t-sql/statements/set-statements-transact-sql) | ほとんどの場合 - 個々のステートメントに関する記事を参照してください 
| [Spatial](https://docs.microsoft.com/sql/relational-databases/spatial/spatial-data-sql-server) | あり |
| [SQL Server エージェント](https://docs.microsoft.com/sql/ssms/agent/sql-server-agent) | × - [エラスティック ジョブ](sql-database-elastic-jobs-getting-started.md)に関する記事を参照してください |
| [SQL Server Analysis Services (SSAS)](https://docs.microsoft.com/sql/analysis-services/analysis-services) | × - [Azure Analysis Services](https://azure.microsoft.com/services/analysis-services/) に関する記事を参照してください |
| [SQL Server 監査](https://docs.microsoft.com/sql/relational-databases/security/auditing/sql-server-audit-database-engine) | × - [SQL Database の監査](sql-database-auditing.md)に関する記事を参照してください |
| [SQL Server Integration Services (SSIS)](https://docs.microsoft.com/sql/integration-services/sql-server-integration-services) | × - [Azure Data Factory](https://azure.microsoft.com/services/data-factory/) に関する記事を参照してください |
| [SQL Server PowerShell](https://docs.microsoft.com/sql/relational-databases/scripting/sql-server-powershell) | あり |
| [SQL Server Profiler](https://docs.microsoft.com/sql/tools/sql-server-profiler/sql-server-profiler) | × - [拡張イベント](sql-database-xevent-db-diff-from-svr.md)に関する記事を参照してください |
| [SQL Server レプリケーション](https://docs.microsoft.com/sql/relational-databases/replication/sql-server-replication) | [トランザクション レプリケーションとスナップショット レプリケーションのサブスクライバーのみ](sql-database-cloud-migrate.md) |
| [SQL Server Reporting Services (SSRS)](https://docs.microsoft.com/sql/reporting-services/create-deploy-and-manage-mobile-and-paginated-reports) | いいえ |
| [ストアド プロシージャ](https://docs.microsoft.com/sql/relational-databases/stored-procedures/stored-procedures-database-engine) | あり |
| [システム ストアド関数](https://docs.microsoft.com/sql/relational-databases/system-functions/system-functions-for-transact-sql) | 一部 - 個々の関数に関する記事を参照してください |
| [システム ストアド プロシージャ](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/system-stored-procedures-transact-sql) | 一部 - 個々のストアド プロシージャに関する記事を参照してください |
| [システム テーブル](https://docs.microsoft.com/sql/relational-databases/system-tables/system-tables-transact-sql) | 一部 - 個々のテーブルに関する記事を参照してください |
| [システム カタログ ビュー](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/catalog-views-transact-sql) | 一部 - 個々のビューに関する記事を参照してください |
| [テーブル パーティション](https://docs.microsoft.com/sql/relational-databases/partitions/partitioned-tables-and-indexes) | ○ - プライマリ ファイルグループのみ |
| [一時テーブル](https://docs.microsoft.com/sql/t-sql/statements/create-table-transact-sql#temporary-tables) | ローカルおよびデータベース スコープのグローバル一時テーブルのみ |
| [テンポラル テーブル](https://docs.microsoft.com/sql/relational-databases/tables/temporal-tables) | あり |
| [変数](https://docs.microsoft.com/sql/t-sql/language-elements/variables-transact-sql) | あり | 
| [透過的なデータ暗号化 (TDE)](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption-tde) | あり |
| [Windows Server フェールオーバー クラスタリング](https://docs.microsoft.com/sql/sql-server/failover-clusters/windows/windows-server-failover-clustering-wsfc-with-sql-server) | × - [フェールオーバー グループとアクティブ geo レプリケーション](sql-database-geo-replication-overview.md)に関する記事を参照してください |
| [XML インデックス](https://docs.microsoft.com/sql/t-sql/statements/create-xml-index-transact-sql) | あり |

## <a name="next-steps"></a>次のステップ

- Azure SQL Database のサービスについては、「[SQL Database とは](sql-database-technical-overview.md)」を参照してください。
- Transact-SQL のサポートと違いについては、「[SQL Database への移行時に Transact-SQL の相違点を解決する](sql-database-transact-sql-information.md)」を参照してください。

