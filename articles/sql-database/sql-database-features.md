---
title: Azure SQL Database の機能の比較 | Microsoft Docs
description: この記事では、各種 Azure SQL Database で使用できる SQL Server の機能を比較します。
services: sql-database
ms.service: sql-database
ms.subservice: service
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: jovanpop-msft
ms.author: jovanpop
ms.reviewer: bonova, sstein
manager: craigg
ms.date: 02/08/2019
ms.openlocfilehash: e1c15b78b93c638c8941356319de2c5e17712795
ms.sourcegitcommit: 43b85f28abcacf30c59ae64725eecaa3b7eb561a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/09/2019
ms.locfileid: "59358268"
---
# <a name="feature-comparison-azure-sql-database-versus-sql-server"></a>機能の比較:Azure SQL Database と SQL Server

Azure SQL Database は、SQL Server と共通のコード ベースを共有します。 Azure SQL Database でサポートされる SQL Server の機能は、作成する Azure SQL データベースの種類に応じて変わります。 Azure SQL Database を使用すると、[マネージド インスタンス](sql-database-managed-instance.md)の一部、単一データベース、またはエラスティック プールの一部として、データベースを作成できます。

マイクロソフトは、Azure SQL Database に継続的に機能を追加します。 最新の更新プログラムについては、以下のフィルターを使用して、Azure のサービスの更新情報 Web ページを参照してください。

- サービスを [[SQL Database]](https://azure.microsoft.com/updates/?service=sql-database)でフィルター処理します。
- SQL Database の機能について、[General Availability] [(GA) のアナウンス](https://azure.microsoft.com/updates/?service=sql-database&update-type=general-availability) でフィルター処理します。

## <a name="sql-server-feature-support-in-azure-sql-database"></a>Azure SQL Database における SQL Server の機能のサポート

次の表は、SQL Server の主要な機能を一覧にして、その機能が一部または完全にサポートされるかに関する情報と、機能に関する詳細情報へのリンクを示します。

| **SQL の機能** | **単一データベースとエラスティック プールでサポートされる** | **マネージド インスタンスでサポートされる** |
| --- | --- | --- |
| [アクティブ geo レプリケーション](sql-database-active-geo-replication.md) | はい - ハイパースケール以外のすべてのサービス レベル | いいえ。[自動フェールオーバー グループ](sql-database-auto-failover-group.md)に関するページを参照してください |
| [自動フェールオーバー グループ](sql-database-auto-failover-group.md) | はい - ハイパースケール以外のすべてのサービス レベル | はい、[パブリック プレビュー](sql-database-auto-failover-group.md)中|
| [Always Encrypted](https://docs.microsoft.com/sql/relational-databases/security/encryption/always-encrypted-database-engine) | ○ - [証明書ストア](sql-database-always-encrypted.md)と[キー コンテナー](sql-database-always-encrypted-azure-key-vault.md)に関する記事を参照してください | ○ - [証明書ストア](sql-database-always-encrypted.md)と[キー コンテナー](sql-database-always-encrypted-azure-key-vault.md)に関する記事を参照してください |
| [AlwaysOn 可用性グループ](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/always-on-availability-groups-sql-server) | [高可用性](sql-database-high-availability.md)は、どのデータベースにも組み込まれています。 ディザスター リカバリーに関する解説は、「[Azure SQL Database によるビジネス継続性の概要](sql-database-business-continuity.md)」を参照してください | [高可用性](sql-database-high-availability.md)は、どのデータベースにも組み込まれています。 ディザスター リカバリーに関する解説は、「[Azure SQL Database によるビジネス継続性の概要](sql-database-business-continuity.md)」を参照してください |
| [データベースの接続](https://docs.microsoft.com/sql/relational-databases/databases/attach-a-database) | いいえ  | いいえ  |
| [アプリケーション ロール](https://docs.microsoft.com/sql/relational-databases/security/authentication-access/application-roles) | はい | はい |
|[監査](https://docs.microsoft.com/sql/relational-databases/security/auditing/sql-server-audit-database-engine) | [はい](sql-database-auditing.md)| [はい](sql-database-managed-instance-auditing.md) |
| [自動バックアップ](sql-database-automated-backups.md) | はい | はい |
| [自動チューニング (プランの強制)](https://docs.microsoft.com/sql/relational-databases/automatic-tuning/automatic-tuning)| [はい](sql-database-automatic-tuning.md)| [はい](https://docs.microsoft.com/sql/relational-databases/automatic-tuning/automatic-tuning) |
| [自動チューニング (インデックス)](https://docs.microsoft.com/sql/relational-databases/automatic-tuning/automatic-tuning)| [はい](sql-database-automatic-tuning.md)| いいえ  |
| [Azure Data Studio](https://docs.microsoft.com/sql/azure-data-studio/what-is) | はい | はい |
| [BACPAC ファイル (エクスポート)](https://docs.microsoft.com/sql/relational-databases/data-tier-applications/export-a-data-tier-application) | ○ - [SQL Database のエクスポート](sql-database-export.md)に関する記事を参照してください | はい |
| [BACPAC ファイル (インポート)](https://docs.microsoft.com/sql/relational-databases/data-tier-applications/import-a-bacpac-file-to-create-a-new-user-database) | ○ - [SQL Database のインポート](sql-database-import.md)に関する記事を参照してください | はい |
| [BACKUP コマンド](https://docs.microsoft.com/sql/t-sql/statements/backup-transact-sql) | いいえ、システムによって開始された自動バックアップのみ - [自動バックアップ](sql-database-automated-backups.md)に関するページを参照してください | システムによって開始された自動バックアップと、ユーザーによって開始されたコピー専用バックアップ - [バックアップの相違点](sql-database-managed-instance-transact-sql-information.md#backup)に関するページを参照してください |
| [組み込み関数](https://docs.microsoft.com/sql/t-sql/functions/functions) | ほとんどの場合 - 個々の関数に関する記事を参照してください | はい - [ストアド プロシージャ、関数、トリガーの相違点](sql-database-managed-instance-transact-sql-information.md#stored-procedures-functions-triggers)に関するページを参照してください |
| [変更データのキャプチャ](https://docs.microsoft.com/sql/relational-databases/track-changes/about-change-data-capture-sql-server) | いいえ  | はい |
| [変更の追跡](https://docs.microsoft.com/sql/relational-databases/track-changes/about-change-tracking-sql-server) | はい |はい |
| [照合順序 - データベース](https://docs.microsoft.com/sql/relational-databases/collations/set-or-change-the-database-collation) | はい | はい |
| [照合順序 - サーバー/インスタンス](https://docs.microsoft.com/sql/relational-databases/collations/set-or-change-the-server-collation) | いいえ  | はい、[パブリック プレビュー](scripts/sql-managed-instance-create-powershell-azure-resource-manager-template.md)中|
| [列ストア インデックス](https://docs.microsoft.com/sql/relational-databases/indexes/columnstore-indexes-overview) | はい - [Premium レベル、Standard レベル - S3 以上、General Purpose レベル、および Business Critical レベル](https://docs.microsoft.com/sql/relational-databases/indexes/columnstore-indexes-overview) |はい |
| [共通言語ランタイム (CLR)](https://docs.microsoft.com/sql/relational-databases/clr-integration/common-language-runtime-clr-integration-programming-concepts) | いいえ  | はい - [CLR の相違点](sql-database-managed-instance-transact-sql-information.md#clr)に関する記事を参照してください |
| [包含データベース](https://docs.microsoft.com/sql/relational-databases/databases/contained-databases) | はい | いいえ、[ポイントインタイム リストアなどの復元の欠陥に起因](sql-database-managed-instance-transact-sql-information.md#cannot-restore-contained-database) |
| [包含ユーザー](https://docs.microsoft.com/sql/relational-databases/security/contained-database-users-making-your-database-portable) | はい | はい |
| [フロー制御言語のキーワード](https://docs.microsoft.com/sql/t-sql/language-elements/control-of-flow) | はい | はい |
| [複数データベース間のクエリ](https://docs.microsoft.com/sql/relational-databases/linked-servers/linked-servers-database-engine) | いいえ - [エラスティック クエリ](sql-database-elastic-query-overview.md)に関する記事を参照してください | はい - [エラスティック クエリ](sql-database-elastic-query-overview.md)に関する記事も参照してください |
| [データベースにまたがるトランザクション](https://docs.microsoft.com/sql/relational-databases/linked-servers/linked-servers-database-engine) | いいえ  | はい、インスタンス内で。 クロスインスタンス クエリについては、[リンク サーバーの違い](sql-database-managed-instance-transact-sql-information.md#linked-servers)に関する記事を参照してください。 |
| [カーソル](https://docs.microsoft.com/sql/t-sql/language-elements/cursors-transact-sql) | はい |はい |
| [データ圧縮](https://docs.microsoft.com/sql/relational-databases/data-compression/data-compression) | はい |はい |
| [データベース メール](https://docs.microsoft.com/sql/relational-databases/database-mail/database-mail) | いいえ  | はい |
| [データ移行サービス (DMS)](https://docs.microsoft.com/sql/dma/dma-overview) | はい | はい |
| [データベース ミラーリング](https://docs.microsoft.com/sql/database-engine/database-mirroring/database-mirroring-sql-server) | いいえ  | いいえ  |
| [データベースの構成設定](https://docs.microsoft.com/sql/t-sql/statements/alter-database-scoped-configuration-transact-sql) | はい | はい |
| [Data Quality Services (DQS)](https://docs.microsoft.com/sql/data-quality-services/data-quality-services) | いいえ  | いいえ  |
| [データベース スナップショット](https://docs.microsoft.com/sql/relational-databases/databases/database-snapshots-sql-server) | いいえ  | いいえ  |
| [データの種類](https://docs.microsoft.com/sql/t-sql/data-types/data-types-transact-sql) | はい |はい |
| [DBCC ステートメント](https://docs.microsoft.com/sql/t-sql/database-console-commands/dbcc-transact-sql) | ほとんどの場合 - 個々のステートメントに関する記事を参照してください | はい - [DBCC の相違点](sql-database-managed-instance-transact-sql-information.md#dbcc)に関する記事を参照してください |
| [DDL ステートメント](https://docs.microsoft.com/sql/t-sql/statements/statements) | ほとんどの場合 - 個々のステートメントに関する記事を参照してください | はい - [T-SQL の相違点](sql-database-managed-instance-transact-sql-information.md)に関する記事を参照してください |
| [DDL トリガー](https://docs.microsoft.com/sql/relational-databases/triggers/ddl-triggers) | データベースのみ |  はい |
| [分散パーティション ビュー](https://docs.microsoft.com/sql/t-sql/statements/create-view-transact-sql#partitioned-views) | いいえ  | はい |
| [分散トランザクション - MS DTC](https://docs.microsoft.com/sql/relational-databases/native-client-ole-db-transactions/supporting-distributed-transactions) | × - [エラスティック トランザクション](sql-database-elastic-transactions-overview.md)に関する記事を参照してください |  いいえ - [リンク サーバーの違い](sql-database-managed-instance-transact-sql-information.md#linked-servers)に関する記事を参照してください |
| [DML ステートメント](https://docs.microsoft.com/sql/t-sql/queries/queries) | はい | はい |
| [DML トリガー](https://docs.microsoft.com/sql/relational-databases/triggers/create-dml-triggers) | ほとんどの場合 - 個々のステートメントに関する記事を参照してください |  はい |
| [DMV](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/system-dynamic-management-views) | ほとんどの場合 - 個々の DMV に関する記事を参照してください |  はい - [T-SQL の相違点](sql-database-managed-instance-transact-sql-information.md)に関する記事を参照してください |
|[動的データ マスク](https://docs.microsoft.com/sql/relational-databases/security/dynamic-data-masking)|[はい](sql-database-dynamic-data-masking-get-started.md)| [はい](sql-database-dynamic-data-masking-get-started.md) |
| [エラスティック プール](sql-database-elastic-pool.md) | はい | 組み込み - 単一のマネージド インスタンスは、同じリソース プールを共有する複数のデータベースを保持できます |
| [イベント通知](https://docs.microsoft.com/sql/relational-databases/service-broker/event-notifications) | × - [アラート](sql-database-insights-alerts-portal.md)に関する記事を参照してください | いいえ  |
| [式](https://docs.microsoft.com/sql/t-sql/language-elements/expressions-transact-sql) |はい | はい |
| [拡張イベント](https://docs.microsoft.com/sql/relational-databases/extended-events/extended-events) | 一部 - 「[SQL Database の拡張イベント](sql-database-xevent-db-diff-from-svr.md)」を参照してください | はい - [拡張イベントの相違点](sql-database-managed-instance-transact-sql-information.md#extended-events)を参照してください |
| [拡張ストアド プロシージャ](https://docs.microsoft.com/sql/relational-databases/extended-stored-procedures-programming/creating-extended-stored-procedures) | いいえ  | いいえ  |
[ファイルおよびファイル グループ](https://docs.microsoft.com/sql/relational-databases/databases/database-files-and-filegroups) | プライマリ ファイル グループのみ | はい |
| [Filestream](https://docs.microsoft.com/sql/relational-databases/blob/filestream-sql-server) | いいえ  | いいえ  |
| [フルテキスト検索](https://docs.microsoft.com/sql/relational-databases/search/full-text-search) |  サード パーティのワード ブレーカーはサポートされていません |サード パーティのワード ブレーカーはサポートされていません |
| [Functions](https://docs.microsoft.com/sql/t-sql/functions/functions) | ほとんどの場合 - 個々の関数に関する記事を参照してください | はい - [ストアド プロシージャ、関数、トリガーの相違点](sql-database-managed-instance-transact-sql-information.md#stored-procedures-functions-triggers)に関するページを参照してください |
| [geo リストア](sql-database-recovery-using-backups.md#geo-restore) | はい - ハイパースケール以外のすべてのサービス レベル | いいえ (定期的に取得する COPY_ONLY の完全バックアップを復元できます) - [バックアップの相違点](sql-database-managed-instance-transact-sql-information.md#backup)と[復元の相違点](sql-database-managed-instance-transact-sql-information.md#restore-statement)に関する記事を参照してください |
| [グラフの処理](https://docs.microsoft.com/sql/relational-databases/graphs/sql-graph-overview) | はい | はい |
| [メモリ内の最適化](https://docs.microsoft.com/sql/relational-databases/in-memory-oltp/in-memory-oltp-in-memory-optimization) | はい - [Premium レベルと Business Critical レベルのみ](sql-database-in-memory.md) | はい - [Business Critical レベルのみ](sql-database-managed-instance.md) |
| [JSON データのサポート](https://docs.microsoft.com/sql/relational-databases/json/json-data-sql-server) | [はい](sql-database-json-features.md) | [はい](sql-database-json-features.md) |
| [言語要素](https://docs.microsoft.com/sql/t-sql/language-elements/language-elements-transact-sql) | ほとんどの場合 - 個々の要素に関する記事を参照してください |  はい - [T-SQL の相違点](sql-database-managed-instance-transact-sql-information.md)に関する記事を参照してください |
| [リンク サーバー](https://docs.microsoft.com/sql/relational-databases/linked-servers/linked-servers-database-engine) | × - [エラスティック クエリ](sql-database-elastic-query-horizontal-partitioning.md)に関する記事を参照してください | SQL Server and SQL Database のみ |
| [ログ配布](https://docs.microsoft.com/sql/database-engine/log-shipping/about-log-shipping-sql-server) | [高可用性](sql-database-high-availability.md)は、どのデータベースにも組み込まれています。 ディザスター リカバリーに関する解説は、「[Azure SQL Database によるビジネス継続性の概要](sql-database-business-continuity.md)」を参照してください |[高可用性](sql-database-high-availability.md)は、どのデータベースにも組み込まれています。 ディザスター リカバリーに関する解説は、「[Azure SQL Database によるビジネス継続性の概要](sql-database-business-continuity.md)」を参照してください |
| [Master Data Services (MDS)](https://docs.microsoft.com/sql/master-data-services/master-data-services-overview-mds) | いいえ  | いいえ  |
| [一括インポートでの最小ログ記録](https://docs.microsoft.com/sql/relational-databases/import-export/prerequisites-for-minimal-logging-in-bulk-import) | いいえ  | いいえ  |
| [システム データの変更](https://docs.microsoft.com/sql/relational-databases/databases/system-databases) | いいえ  | はい |
| [OLE オートメーション](https://docs.microsoft.com/sql/database-engine/configure-windows/ole-automation-procedures-server-configuration-option) | いいえ  | いいえ  |
| [オンライン インデックス操作](https://docs.microsoft.com/sql/relational-databases/indexes/perform-index-operations-online) | はい | はい |
| [OPENDATASOURCE](https://docs.microsoft.com/sql/t-sql/functions/opendatasource-transact-sql)|いいえ |はい - [T-SQL の相違点](sql-database-managed-instance-transact-sql-information.md)に関する記事を参照してください|
| [OPENJSON](https://docs.microsoft.com/sql/t-sql/functions/openjson-transact-sql)|はい|はい|
| [OPENQUERY](https://docs.microsoft.com/sql/t-sql/functions/openquery-transact-sql)|いいえ |はい - [T-SQL の相違点](sql-database-managed-instance-transact-sql-information.md)に関する記事を参照してください|
| [OPENROWSET](https://docs.microsoft.com/sql/t-sql/functions/openrowset-transact-sql)|いいえ |はい - [T-SQL の相違点](sql-database-managed-instance-transact-sql-information.md)に関する記事を参照してください|
| [OPENXML](https://docs.microsoft.com/sql/t-sql/functions/openxml-transact-sql)|はい|はい|
| [演算子](https://docs.microsoft.com/sql/t-sql/language-elements/operators-transact-sql) | ほとんどの場合 - 個々の演算子に関する記事を参照してください |はい - [T-SQL の相違点](sql-database-managed-instance-transact-sql-information.md)に関する記事を参照してください |
| [パーティション分割](https://docs.microsoft.com/sql/relational-databases/partitions/partitioned-tables-and-indexes) | はい | はい |
| [データベースのポイントインタイム リストア](https://docs.microsoft.com/sql/relational-databases/backup-restore/restore-a-sql-server-database-to-a-point-in-time-full-recovery-model) | はい - ハイパースケール以外のすべてのサービス レベル - [SQL Database の復旧](sql-database-recovery-using-backups.md#point-in-time-restore)に関する記事を参照してください | ○ - [SQL Database の復旧](sql-database-recovery-using-backups.md#point-in-time-restore)に関する記事を参照してください |
| [PolyBase](https://docs.microsoft.com/sql/relational-databases/polybase/polybase-guide) | いいえ  | いいえ  |
| [ポリシー ベースの管理](https://docs.microsoft.com/sql/relational-databases/policy-based-management/administer-servers-by-using-policy-based-management) | いいえ  | いいえ  |
| [Predicates](https://docs.microsoft.com/sql/t-sql/queries/predicates) | はい | はい |
| [クエリ通知](https://docs.microsoft.com/sql/relational-databases/native-client/features/working-with-query-notifications) | いいえ  | はい |
| [Query Performance Insights](sql-database-query-performance.md) | はい | いいえ  |
| [R Services](https://docs.microsoft.com/sql/advanced-analytics/r-services/sql-server-r-services) | はい、[パブリック プレビュー](https://docs.microsoft.com/sql/advanced-analytics/what-s-new-in-sql-server-machine-learning-services)中  | いいえ  |
| [リソース ガバナー](https://docs.microsoft.com/sql/relational-databases/resource-governor/resource-governor) | いいえ  | はい |
| [RESTORE ステートメント](https://docs.microsoft.com/sql/t-sql/statements/restore-statements-for-restoring-recovering-and-managing-backups-transact-sql) | いいえ  | はい - [復元の相違点](sql-database-managed-instance-transact-sql-information.md#restore-statement)に関する記事を参照してください |
| [バックアップからデータベースを復元する](https://docs.microsoft.com/sql/relational-databases/backup-restore/back-up-and-restore-of-sql-server-databases#restore-data-backups) | 自動バックアップからのみ - [SQL Database の復旧](sql-database-recovery-using-backups.md)に関する記事をご覧ください | 自動バックアップから - [SQL Database の復旧](sql-database-recovery-using-backups.md)を参照してください。完全バックアップから - [バックアップの相違点](sql-database-managed-instance-transact-sql-information.md#backup)を参照してください |
| [行レベルのセキュリティ](https://docs.microsoft.com/sql/relational-databases/security/row-level-security) | はい | はい |
| [セマンティック検索](https://docs.microsoft.com/sql/relational-databases/search/semantic-search-sql-server) | いいえ  | いいえ  |
| [シーケンス番号](https://docs.microsoft.com/sql/relational-databases/sequence-numbers/sequence-numbers) | はい | はい |
| [Service Broker](https://docs.microsoft.com/sql/database-engine/configure-windows/sql-server-service-broker) | いいえ  | はい - [Service Broker の相違点](sql-database-managed-instance-transact-sql-information.md#service-broker)に関する記事を参照してください |
| [サーバーの構成設定](https://docs.microsoft.com/sql/database-engine/configure-windows/server-configuration-options-sql-server) | いいえ  | はい - [T-SQL の相違点](sql-database-managed-instance-transact-sql-information.md)に関する記事を参照してください |
| [SET ステートメント](https://docs.microsoft.com/sql/t-sql/statements/set-statements-transact-sql) | ほとんどの場合 - 個々のステートメントに関する記事を参照してください | はい - [T-SQL の相違点](sql-database-managed-instance-transact-sql-information.md)に関する記事を参照してください|
| [SMO](https://docs.microsoft.com/sql/relational-databases/server-management-objects-smo/sql-server-management-objects-smo-programming-guide) | はい | はい |
| [Spatial](https://docs.microsoft.com/sql/relational-databases/spatial/spatial-data-sql-server) | はい | はい |
| [SQL Analytics](https://docs.microsoft.com/azure/azure-monitor/insights/azure-sql) | はい | はい |
| [SQL データ同期](sql-database-get-started-sql-data-sync.md) | はい | いいえ  |
| [SQL Server エージェント](https://docs.microsoft.com/sql/ssms/agent/sql-server-agent) | × - [エラスティック ジョブ](sql-database-elastic-jobs-getting-started.md)に関する記事を参照してください | はい - [SQL Server エージェントの相違点](sql-database-managed-instance-transact-sql-information.md#sql-server-agent)に関する記事を参照してください |
| [SQL Server Analysis Services (SSAS)](https://docs.microsoft.com/sql/analysis-services/analysis-services) | いいえ - [Azure Analysis Services](https://azure.microsoft.com/services/analysis-services/) に関する記事を参照してください | × - [Azure Analysis Services](https://azure.microsoft.com/services/analysis-services/) に関する記事を参照してください |
| [SQL Server 監査](https://docs.microsoft.com/sql/relational-databases/security/auditing/sql-server-audit-database-engine) | × - [SQL Database の監査](sql-database-auditing.md)に関する記事を参照してください | はい - [監査の相違点](sql-database-managed-instance-transact-sql-information.md#auditing)に関するページを参照してください |
| [SQL Server Data Tools (SSDT)](https://docs.microsoft.com/sql/ssdt/download-sql-server-data-tools-ssdt) | はい | はい |
| [SQL Server Integration Services (SSIS)](https://docs.microsoft.com/sql/integration-services/sql-server-integration-services) | はい。Azure Data Factory (ADF) 環境で管理された SSIS を使用します。このパッケージは、Azure SQL Database でホストされている SSISDB に格納され、Azure SSIS Integration Runtime (IR) で実行されます。[ADF での Azure-SSIS IR の作成](https://docs.microsoft.com/azure/data-factory/create-azure-ssis-integration-runtime)に関するページをご覧ください。 <br/><br/>SQL Database サーバーと Managed Instance の SSIS 機能を比較するには、[Azure SQL Database の単一データベース/エラスティック プールおよび Managed Instance の比較](../data-factory/create-azure-ssis-integration-runtime.md#compare-sql-database-single-databaseelastic-pool-and-sql-database-managed-instance)に関する記事を参照してください。 | はい。Azure Data Factory (ADF) 環境で管理された SSIS を使用します。このパッケージは、マネージド インスタンスによってホストされている SSISDB に格納され、Azure SSIS Integration Runtime (IR) で実行されます。[ADF での Azure-SSIS IR の作成](https://docs.microsoft.com/azure/data-factory/create-azure-ssis-integration-runtime)に関するページをご覧ください。 <br/><br/>SQL Database と Managed Instance の SSIS 機能を比較するには、[Azure SQL Database の単一データベース/エラスティック プールおよび Managed Instance の比較](../data-factory/create-azure-ssis-integration-runtime.md#compare-sql-database-single-databaseelastic-pool-and-sql-database-managed-instance)に関するページを参照してください。 |
| [SQL Server Management Studio (SSMS)](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms) | はい | はい |
| [SQL Server PowerShell](https://docs.microsoft.com/sql/relational-databases/scripting/sql-server-powershell) | はい | はい |
| [SQL Server プロファイラー](https://docs.microsoft.com/sql/tools/sql-server-profiler/sql-server-profiler) | × - [拡張イベント](sql-database-xevent-db-diff-from-svr.md)に関する記事を参照してください | はい |
| [SQL Server レプリケーション](https://docs.microsoft.com/sql/relational-databases/replication/sql-server-replication) | [トランザクション レプリケーションとスナップショット レプリケーションのサブスクライバーのみ](sql-database-single-database-migrate.md) | はい、[パブリック プレビュー](https://docs.microsoft.com/sql/relational-databases/replication/replication-with-sql-database-managed-instance)中 |
| [SQL Server Reporting Services (SSRS)](https://docs.microsoft.com/sql/reporting-services/create-deploy-and-manage-mobile-and-paginated-reports) | いいえ - [Power BI](https://docs.microsoft.com/power-bi/) に関する記事を参照してください | いいえ - [Power BI](https://docs.microsoft.com/power-bi/) に関する記事を参照してください |
| [ストアド プロシージャ](https://docs.microsoft.com/sql/relational-databases/stored-procedures/stored-procedures-database-engine) | はい | はい |
| [システム ストアド関数](https://docs.microsoft.com/sql/relational-databases/system-functions/system-functions-for-transact-sql) | ほとんどの場合 - 個々の関数に関する記事を参照してください | はい - [ストアド プロシージャ、関数、トリガーの相違点](sql-database-managed-instance-transact-sql-information.md#stored-procedures-functions-triggers)に関するページを参照してください |
| [システム ストアド プロシージャ](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/system-stored-procedures-transact-sql) | 一部 - 個々のストアド プロシージャに関する記事を参照してください | はい - [ストアド プロシージャ、関数、トリガーの相違点](sql-database-managed-instance-transact-sql-information.md#stored-procedures-functions-triggers)に関するページを参照してください |
| [システム テーブル](https://docs.microsoft.com/sql/relational-databases/system-tables/system-tables-transact-sql) | 一部 - 個々のテーブルに関する記事を参照してください | はい - [T-SQL の相違点](sql-database-managed-instance-transact-sql-information.md)に関する記事を参照してください |
| [システム カタログ ビュー](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/catalog-views-transact-sql) | 一部 - 個々のビューに関する記事を参照してください | はい - [T-SQL の相違点](sql-database-managed-instance-transact-sql-information.md)に関する記事を参照してください |
| [一時テーブル](https://docs.microsoft.com/sql/t-sql/statements/create-table-transact-sql#database-scoped-global-temporary-tables-azure-sql-database) | ローカルおよびデータベース スコープのグローバル一時テーブル | ローカルおよびインスタンス スコープのグローバル一時テーブル |
| [テンポラル テーブル](https://docs.microsoft.com/sql/relational-databases/tables/temporal-tables) | [はい](sql-database-temporal-tables.md) | [はい](sql-database-temporal-tables.md) |
|脅威の検出|  [はい](sql-database-threat-detection.md)|[はい](sql-database-managed-instance-threat-detection.md)|
| [トレース フラグ](https://docs.microsoft.com/sql/t-sql/database-console-commands/dbcc-traceon-trace-flags-transact-sql) | いいえ  | いいえ  |
| [variables](https://docs.microsoft.com/sql/t-sql/language-elements/variables-transact-sql) | はい | はい |
| [Transparent Data Encryption (TDE)](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption-tde) | はい - General Purpose および Business Critical サービス レベルのみ| [はい](transparent-data-encryption-azure-sql.md) |
[VNet](../virtual-network/virtual-networks-overview.md) | 一部 - [VNET エンドポイント](sql-database-vnet-service-endpoint-rule-overview.md)に関する記事を参照してください | はい (Resource Manager モデルのみ) |
| [Windows Server フェールオーバー クラスタリング](https://docs.microsoft.com/sql/sql-server/failover-clusters/windows/windows-server-failover-clustering-wsfc-with-sql-server) | [高可用性](sql-database-high-availability.md)は、どのデータベースにも組み込まれています。 ディザスター リカバリーに関する解説は、「[Azure SQL Database によるビジネス継続性の概要](sql-database-business-continuity.md)」を参照してください | [高可用性](sql-database-high-availability.md)は、どのデータベースにも組み込まれています。 ディザスター リカバリーに関する解説は、「[Azure SQL Database によるビジネス継続性の概要](sql-database-business-continuity.md)」を参照してください |
| [XML インデックス](https://docs.microsoft.com/sql/t-sql/statements/create-xml-index-transact-sql) | はい | はい |

## <a name="next-steps"></a>次の手順

- Azure SQL Database のサービスについては、「[SQL Database とは](sql-database-technical-overview.md)」を参照してください。
- マネージド インスタンスについては、「[What is a Managed Instance?](sql-database-managed-instance.md)」(マネージド インスタンスとは) を参照してください。
