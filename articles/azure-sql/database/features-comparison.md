---
title: SQL Database と SQL Managed Instance のデータベース エンジンの機能を比較する
titleSuffix: Azure SQL Database & SQL Managed Instance
description: この記事では、Azure SQL Database と Azure SQL Managed Instance のデータベース エンジンの機能を比較します
services: sql-database
ms.service: sql-db-mi
ms.subservice: features
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: danimir
ms.author: danil
ms.reviewer: bonova, sstein, danil
ms.date: 03/08/2021
ms.openlocfilehash: 8c98ce661e7bb753d4e62d1eaf98702de91c5106
ms.sourcegitcommit: 8d1b97c3777684bd98f2cfbc9d440b1299a02e8f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/09/2021
ms.locfileid: "102489771"
---
# <a name="features-comparison-azure-sql-database-and-azure-sql-managed-instance"></a>機能の比較:Azure SQL Database と Azure SQL Managed Instance

[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

Azure SQL Database と SQL Managed Instance は、SQL Server の最新の安定したバージョンと共通のコード ベースを共有します。 標準の SQL 言語、クエリ処理、データベース管理機能のほとんどは同一です。 SQL Server と SQL Database または SQL Managed Instance に共通する機能は次のとおりです。

- 言語機能 - [フロー制御言語のキーワード](/sql/t-sql/language-elements/control-of-flow)、[カーソル](/sql/t-sql/language-elements/cursors-transact-sql)、[データ型](/sql/t-sql/data-types/data-types-transact-sql)、[DML ステートメント](/sql/t-sql/queries/queries)、[述語](/sql/t-sql/queries/predicates)、[シーケンス番号](/sql/relational-databases/sequence-numbers/sequence-numbers)、[ストアド プロシージャ](/sql/relational-databases/stored-procedures/stored-procedures-database-engine)、[変数](/sql/t-sql/language-elements/variables-transact-sql)。
- データベース機能 - [自動チューニング (プランの強制)](/sql/relational-databases/automatic-tuning/automatic-tuning)、[変更の追跡](/sql/relational-databases/track-changes/about-change-tracking-sql-server)、[データベース照合順序](/sql/relational-databases/collations/set-or-change-the-database-collation)、[包含データベース](/sql/relational-databases/databases/contained-databases)、[包含ユーザー](/sql/relational-databases/security/contained-database-users-making-your-database-portable)、[データ圧縮](/sql/relational-databases/data-compression/data-compression)、[データベースの構成設定](/sql/t-sql/statements/alter-database-scoped-configuration-transact-sql)、[オンライン インデックス操作](/sql/relational-databases/indexes/perform-index-operations-online)、[パーティション分割](/sql/relational-databases/partitions/partitioned-tables-and-indexes)、[テンポラル テーブル](/sql/relational-databases/tables/temporal-tables) ([ファースト ステップ ガイドを参照](../temporal-tables.md))。
- セキュリティ機能 - [アプリケーション ロール](/sql/relational-databases/security/authentication-access/application-roles)、[動的データ マスク](/sql/relational-databases/security/dynamic-data-masking) ([ファースト ステップ ガイドを参照](dynamic-data-masking-overview.md))、[行レベル セキュリティ](/sql/relational-databases/security/row-level-security)、および脅威の検出。[SQL Database](threat-detection-configure.md) および [SQL Managed Instance](../managed-instance/threat-detection-configure.md) についてはファースト ステップ ガイドを参照。
- マルチモデル機能 - [グラフの処理](/sql/relational-databases/graphs/sql-graph-overview)、[JSON データ](/sql/relational-databases/json/json-data-sql-server) ([ファースト ステップ ガイドを参照](json-features.md))、[OPENXML](/sql/t-sql/functions/openxml-transact-sql)、[空間](/sql/relational-databases/spatial/spatial-data-sql-server)、[OPENJSON](/sql/t-sql/functions/openjson-transact-sql)、および [XML インデックス](/sql/t-sql/statements/create-xml-index-transact-sql)。

Azure がデータベースを管理し、高可用性を保証します。 高可用性に影響する可能性がある、または PaaS では使用できない一部の機能は、SQL Database および SQL Managed Instance では機能が制限されています。 これらの機能について、以下の表で説明します。

違いについてさらに詳しく知りたい場合は、次の各ページで見つけることができます。
- [Azure SQL Database とSQL Server との相違点](transact-sql-tsql-differences-sql-server.md)
- [Azure SQL Managed Instance とSQL Server との相違点](../managed-instance/transact-sql-tsql-differences-sql-server.md)

## <a name="features-of-sql-database-and-sql-managed-instance"></a>SQL Database と SQL Managed Instance の機能

次の表は、SQL Server の主要な機能を一覧にして、その機能が SQL Database および SQL Managed Instance で一部または完全にサポートされるかどうかについての情報と、機能に関する詳細情報へのリンクを示します。

| **機能** | **Azure SQL Database** | **Azure SQL Managed Instance** |
| --- | --- | --- |
| [常に暗号化](/sql/relational-databases/security/encryption/always-encrypted-database-engine) | はい - [証明書ストア](always-encrypted-certificate-store-configure.md)と[キー コンテナー](always-encrypted-azure-key-vault-configure.md)に関する記事を参照してください | はい - [証明書ストア](always-encrypted-certificate-store-configure.md)と[キー コンテナー](always-encrypted-azure-key-vault-configure.md)に関する記事を参照してください |
| [Always On 可用性グループ](/sql/database-engine/availability-groups/windows/always-on-availability-groups-sql-server) | [99.99 から 99.995% の可用性](high-availability-sla.md)がすべてのデータベースで保証されます。 ディザスター リカバリーに関する解説は、「[Azure SQL Database によるビジネス継続性の概要](business-continuity-high-availability-disaster-recover-hadr-overview.md)」を参照してください | [99.99% の可用性](high-availability-sla.md)がすべてのデータベースで保証され、[ユーザーが管理することはできません](../managed-instance/transact-sql-tsql-differences-sql-server.md#availability)。 ディザスター リカバリーに関する解説は、「[Azure SQL Database によるビジネス継続性の概要](business-continuity-high-availability-disaster-recover-hadr-overview.md)」を参照してください。 別のリージョンにセカンダリ SQL Managed Instance を構成するには、[自動フェールオーバー グループ](auto-failover-group-overview.md)を使用します。 SQL Server インスタンスと SQL Database を SQL Managed Instance のセカンダリとして使用することはできません。 |
| [データベースの接続](/sql/relational-databases/databases/attach-a-database) | いいえ | いいえ |
| [監査](/sql/relational-databases/security/auditing/sql-server-audit-database-engine) | [はい](auditing-overview.md)| [はい](../managed-instance/auditing-configure.md)。ただし、いくつかの[相違点](../managed-instance/transact-sql-tsql-differences-sql-server.md#auditing)があります |
| [Azure Active Directory (Azure AD) 認証](authentication-aad-overview.md) | はい。 Azure AD ユーザーのみ。 | はい。 サーバーレベルの Azure AD ログインを含む。 |
| [BACKUP コマンド](/sql/t-sql/statements/backup-transact-sql) | いいえ、システムによって開始された自動バックアップのみ - [自動バックアップ](automated-backups-overview.md)に関するページを参照してください | はい、ユーザーによって開始された Azure BLOB ストレージへのコピーのみのバックアップ (ユーザーがシステムの自動バックアップを開始することはできません) - [バックアップの相違点](../managed-instance/transact-sql-tsql-differences-sql-server.md#backup)に関する記述を参照してください |
| [組み込み関数](/sql/t-sql/functions/functions) | ほとんどの場合 - 個々の関数に関する記事を参照してください | はい - [ストアド プロシージャ、関数、トリガーの相違点](../managed-instance/transact-sql-tsql-differences-sql-server.md#stored-procedures-functions-and-triggers)に関するページを参照してください |
| [BULK INSERT ステートメント](/sql/relational-databases/import-export/import-bulk-data-by-using-bulk-insert-or-openrowset-bulk-sql-server) | はい、ただし、ソースとしての Azure Blob Storage からのみとなります。 | はい、ただし、ソースとしての Azure Blob Storage からのみとなります - [相違点](../managed-instance/transact-sql-tsql-differences-sql-server.md#bulk-insert--openrowset)に関する記述を参照してください。 |
| [証明書と非対称キー](/sql/relational-databases/security/sql-server-certificates-and-asymmetric-keys) | はい、ただし、`BACKUP` および `CREATE` 操作のためにファイル システムにアクセスすることはできません。 | はい、ただし、`BACKUP` および `CREATE` 操作のためにファイル システムにアクセスすることはできません - [証明書の相違点](../managed-instance/transact-sql-tsql-differences-sql-server.md#certificates)に関する記述を参照してください。 |
| [変更データ キャプチャ - CDC](/sql/relational-databases/track-changes/about-change-data-capture-sql-server) | いいえ | はい |
| [照合順序 - サーバー/インスタンス](/sql/relational-databases/collations/set-or-change-the-server-collation) | いいえ、既定のサーバーの照合順序である `SQL_Latin1_General_CP1_CI_AS` が常に使用されます。 | はい、[インスタンスが作成される](../managed-instance/create-template-quickstart.md)ときに設定できます。後で更新することはできません。 |
| [列ストア インデックス](/sql/relational-databases/indexes/columnstore-indexes-overview) | はい - [Premium レベル、Standard レベル - S3 以上、General Purpose レベル、Business Critical、および HyperScale レベル](/sql/relational-databases/indexes/columnstore-indexes-overview) |はい |
| [共通言語ランタイム - CLR](/sql/relational-databases/clr-integration/common-language-runtime-clr-integration-programming-concepts) | いいえ | はい、ただし、`CREATE ASSEMBLY` ステートメントでファイル システムにアクセスすることはできません - [CLR の相違点](../managed-instance/transact-sql-tsql-differences-sql-server.md#clr)に関する記述を参照してください |
| [資格情報](/sql/relational-databases/security/authentication-access/credentials-database-engine) | はい、ただし、[データベース スコープの資格情報](/sql/t-sql/statements/create-database-scoped-credential-transact-sql)のみとなります。 | はい。ただし、サポートされるのは **Azure Key Vault** と `SHARED ACCESS SIGNATURE` のみです。[詳細](../managed-instance/transact-sql-tsql-differences-sql-server.md#credential)をご覧ください |
| [クロスデータベース/3 部構成の名前のクエリ](/sql/relational-databases/linked-servers/linked-servers-database-engine) | いいえ - [エラスティック クエリ](elastic-query-overview.md)に関する記事を参照してください | はい - [エラスティック クエリ](elastic-query-overview.md)に関する記事も参照してください |
| [データベースにまたがるトランザクション](/sql/relational-databases/linked-servers/linked-servers-database-engine) | いいえ | はい、インスタンス内で。 クロスインスタンス クエリについては、[リンク サーバーの違い](../managed-instance/transact-sql-tsql-differences-sql-server.md#linked-servers)に関する記事を参照してください。 |
| [データベース メール - DbMail](/sql/relational-databases/database-mail/database-mail) | いいえ | はい |
| [データベース ミラーリング](/sql/database-engine/database-mirroring/database-mirroring-sql-server) | いいえ | [いいえ](../managed-instance/transact-sql-tsql-differences-sql-server.md#database-mirroring) |
| [データベース スナップショット](/sql/relational-databases/databases/database-snapshots-sql-server) | いいえ | いいえ |
| [DBCC ステートメント](/sql/t-sql/database-console-commands/dbcc-transact-sql) | ほとんどの場合 - 個々のステートメントに関する記事を参照してください | はい - [DBCC の相違点](../managed-instance/transact-sql-tsql-differences-sql-server.md#dbcc)に関する記事を参照してください |
| [DDL ステートメント](/sql/t-sql/statements/statements) | ほとんどの場合 - 個々のステートメントに関する記事を参照してください | はい - [T-SQL の相違点](../managed-instance/transact-sql-tsql-differences-sql-server.md)に関する記事を参照してください |
| [DDL トリガー](/sql/relational-databases/triggers/ddl-triggers) | データベースのみ |  はい |
| [分散パーティション ビュー](/sql/t-sql/statements/create-view-transact-sql#partitioned-views) | いいえ | はい |
| [分散トランザクション - MS DTC](/sql/relational-databases/native-client-ole-db-transactions/supporting-distributed-transactions) | いいえ - [エラスティック トランザクション](elastic-transactions-overview.md)に関する記事を参照してください |  いいえ - [リンク サーバーの違い](../managed-instance/transact-sql-tsql-differences-sql-server.md#linked-servers)に関する記事を参照してください。 移行時に、複数の分散 SQL Server インスタンスから 1 つの SQL Managed Instance にデータベースを統合してみてください。 |
| [DML トリガー](/sql/relational-databases/triggers/create-dml-triggers) | ほとんどの場合 - 個々のステートメントに関する記事を参照してください |  はい |
| [DMV](/sql/relational-databases/system-dynamic-management-views/system-dynamic-management-views) | ほとんどの場合 - 個々の DMV に関する記事を参照してください |  はい - [T-SQL の相違点](../managed-instance/transact-sql-tsql-differences-sql-server.md)に関する記事を参照してください |
| [エラスティック クエリ](elastic-query-overview.md) (パブリック プレビュー) | はい。必要な RDBMS の種類を使用します。 | はい。必要な RDBMS の種類を使用します。 |
| [イベント通知](/sql/relational-databases/service-broker/event-notifications) | いいえ - [アラート](alerts-insights-configure-portal.md)に関する記事を参照してください | いいえ |
| [式](/sql/t-sql/language-elements/expressions-transact-sql) |はい | はい |
| [拡張イベント (XEvent)](/sql/relational-databases/extended-events/extended-events) | 一部 - 「[SQL Database の拡張イベント](xevent-db-diff-from-svr.md)」を参照してください | はい - [拡張イベントの相違点](../managed-instance/transact-sql-tsql-differences-sql-server.md#extended-events)を参照してください |
| [拡張ストアド プロシージャ](/sql/relational-databases/extended-stored-procedures-programming/creating-extended-stored-procedures) | いいえ | いいえ |
| [ファイルおよびファイル グループ](/sql/relational-databases/databases/database-files-and-filegroups) | プライマリ ファイル グループのみ | はい。 ファイル パスは自動的に割り当てられ、`ALTER DATABASE ADD FILE` [ステートメント](../managed-instance/transact-sql-tsql-differences-sql-server.md#alter-database-statement)でファイルの場所を指定することはできません。  |
| [FileStream](/sql/relational-databases/blob/filestream-sql-server) | いいえ | [いいえ](../managed-instance/transact-sql-tsql-differences-sql-server.md#filestream-and-filetable) |
| [フルテキスト検索 (FTS)](/sql/relational-databases/search/full-text-search) |  はい、ただし、サード パーティのワード ブレーカーはサポートされていません | はい、ただし、[サード パーティのワード ブレーカーはサポートされていません](../managed-instance/transact-sql-tsql-differences-sql-server.md#full-text-semantic-search) |
| [関数](/sql/t-sql/functions/functions) | ほとんどの場合 - 個々の関数に関する記事を参照してください | はい - [ストアド プロシージャ、関数、トリガーの相違点](../managed-instance/transact-sql-tsql-differences-sql-server.md#stored-procedures-functions-and-triggers)に関するページを参照してください |
| [メモリ内の最適化](/sql/relational-databases/in-memory-oltp/in-memory-oltp-in-memory-optimization) | はい ([Premium および Business Critical サービス レベル](../in-memory-oltp-overview.md)の場合)。</br> [Hyperscale サービス レベル](service-tier-hyperscale.md)の場合、メモリ最適化テーブル変数など、非永続的なインメモリ OLTP オブジェクトのサポートは限定的です。| はい ([Business Critical サービス レベル](../managed-instance/sql-managed-instance-paas-overview.md)の場合) |
| [言語要素](/sql/t-sql/language-elements/language-elements-transact-sql) | ほとんどの場合 - 個々の要素に関する記事を参照してください |  はい - [T-SQL の相違点](../managed-instance/transact-sql-tsql-differences-sql-server.md)に関する記事を参照してください |
| [リンク サーバー](/sql/relational-databases/linked-servers/linked-servers-database-engine) | いいえ - [エラスティック クエリ](elastic-query-horizontal-partitioning.md)に関する記事を参照してください | はい。 分散トランザクションなしの [SQL Server と SQL Database](../managed-instance/transact-sql-tsql-differences-sql-server.md#linked-servers) のみ。 |
| ファイル (CSV、Excel) から読み取られる[リンク サーバー](/sql/relational-databases/linked-servers/linked-servers-database-engine)| いいえ。 CSV 形式の代わりとして、[BULK INSERT](/sql/t-sql/statements/bulk-insert-transact-sql#e-importing-data-from-a-csv-file) または [OPENROWSET](/sql/t-sql/functions/openrowset-transact-sql#g-accessing-data-from-a-csv-file-with-a-format-file) を使用します。 | いいえ。 CSV 形式の代わりとして、[BULK INSERT](/sql/t-sql/statements/bulk-insert-transact-sql#e-importing-data-from-a-csv-file) または [OPENROWSET](/sql/t-sql/functions/openrowset-transact-sql#g-accessing-data-from-a-csv-file-with-a-format-file) を使用します。 [SQL Managed Instance フィードバック項目](https://feedback.azure.com/forums/915676-sql-managed-instance/suggestions/35657887-linked-server-to-non-sql-sources)でこれらの要求を追跡します|
| [ログ配布](/sql/database-engine/log-shipping/about-log-shipping-sql-server) | [高可用性](high-availability-sla.md)は、どのデータベースにも組み込まれています。 ディザスター リカバリーに関する解説は、[ビジネス継続性の概要](business-continuity-high-availability-disaster-recover-hadr-overview.md)で説明されています。 | [Azure Data Migration Service (DMS)](../../dms/tutorial-sql-server-to-managed-instance.md) の移行プロセスの一環としてネイティブに組み込まれています。 外部の[ログ再生サービス (LRS)](../managed-instance/log-replay-service-migrate.md) としてカスタム データ移行プロジェクト用にネイティブに組み込まれています。<br /> 高可用性ソリューションとしては使用できません。その理由は、他の[高可用性](high-availability-sla.md)メソッドがすべてのデータベースに含まれており、HA の代替手段としてログ配布を使用することは推奨されないからです。 ディザスター リカバリーに関する解説は、[ビジネス継続性の概要](business-continuity-high-availability-disaster-recover-hadr-overview.md)で説明されています。 データベースの間のレプリケーション メカニズムとしては使用できません。[Business Critical レベル](service-tier-business-critical.md)、[自動フェールオーバー グループ](auto-failover-group-overview.md)、または[トランザクション レプリケーション](../managed-instance/replication-transactional-overview.md)では、代替手段としてセカンダリ レプリカを使用してください。 |
| [ログインとユーザー](/sql/relational-databases/security/authentication-access/principals-database-engine) | はい、ただし、`CREATE` および `ALTER` ログイン ステートメントでは、すべてのオプションが提供されるわけではありません (Windows およびサーバーレベルの Azure Active Directory ログインはありません)。 `EXECUTE AS LOGIN` はサポートされていません。代わりに `EXECUTE AS USER` を使用してください。  | はい、ただし、いくつかの[相違点](../managed-instance/transact-sql-tsql-differences-sql-server.md#logins-and-users)があります。 Windows ログインはサポートされておらず、Azure Active Directory ログインに置き換える必要があります。 |
| [一括インポートでの最小ログ記録](/sql/relational-databases/import-export/prerequisites-for-minimal-logging-in-bulk-import) | いいえ、完全復旧モデルのみがサポートされています。 | いいえ、完全復旧モデルのみがサポートされています。 |
| [システム データの変更](/sql/relational-databases/databases/system-databases) | いいえ | はい |
| [OLE オートメーション](/sql/database-engine/configure-windows/ole-automation-procedures-server-configuration-option) | いいえ | いいえ |
| [OPENDATASOURCE](/sql/t-sql/functions/opendatasource-transact-sql)|いいえ|はい、SQL Database、SQL Managed Instance、および SQL Server に対してのみとなります。 [T-SQL の相違点](../managed-instance/transact-sql-tsql-differences-sql-server.md)に関するページを参照してください|
| [OPENQUERY](/sql/t-sql/functions/openquery-transact-sql)|いいえ|はい、SQL Database、SQL Managed Instance、および SQL Server に対してのみとなります。 [T-SQL の相違点](../managed-instance/transact-sql-tsql-differences-sql-server.md)に関するページを参照してください|
| [OPENROWSET](/sql/t-sql/functions/openrowset-transact-sql)|はい、Azure Blob Storage からのインポートのみとなります。 |はい、SQL Database、SQL Managed Instance と SQL Server に対してのみ、および Azure Blob Storage からのインポートに対してのみとなります。 [T-SQL の相違点](../managed-instance/transact-sql-tsql-differences-sql-server.md)に関するページを参照してください|
| [オペレーター](/sql/t-sql/language-elements/operators-transact-sql) | ほとんどの場合 - 個々の演算子に関する記事を参照してください |はい - [T-SQL の相違点](../managed-instance/transact-sql-tsql-differences-sql-server.md)に関する記事を参照してください |
| [Polybase](/sql/relational-databases/polybase/polybase-guide) | いいえ。 Azure Blob Storage に配置されているファイル内のデータに対してクエリを実行するには、`OPENROWSET` 関数を使用するか、[Synapse Analytics 内のサーバーレス SQL プールを参照する外部テーブル](https://devblogs.microsoft.com/azure-sql/read-azure-storage-files-using-synapse-sql-external-tables/)を使用します。 | いいえ。 Azure Blob Storage に配置されているファイル内のデータに対してクエリを実行するには、`OPENROWSET` 関数、[Synapse Analytics 内のサーバーレス SQL プールを参照するリンク サーバー](https://devblogs.microsoft.com/azure-sql/linked-server-to-synapse-sql-to-implement-polybase-like-scenarios-in-managed-instance/)、または [Synapse Analytics 内のサーバーレス SQL プール](https://devblogs.microsoft.com/azure-sql/read-azure-storage-files-using-synapse-sql-external-tables/)または SQL Server を参照する外部テーブル (パブリック プレビュー段階) を使用します。 |
| [クエリ通知](/sql/relational-databases/native-client/features/working-with-query-notifications) | いいえ | はい |
| [Machine Learning Services](/sql/advanced-analytics/what-is-sql-server-machine-learning) (_以前の R サービス_)| はい、[パブリック プレビュー](/sql/advanced-analytics/what-s-new-in-sql-server-machine-learning-services)中  | いいえ |
| [復旧モデル](/sql/relational-databases/backup-restore/recovery-models-sql-server) | 高可用性が保証される完全復旧のみがサポートされています。 単純復旧モデルと一括ログ復旧モデルは利用できません。 | 高可用性が保証される完全復旧のみがサポートされています。 単純復旧モデルと一括ログ復旧モデルは利用できません。 |
| [リソース ガバナー](/sql/relational-databases/resource-governor/resource-governor) | いいえ | はい |
| [RESTORE ステートメント](/sql/t-sql/statements/restore-statements-for-restoring-recovering-and-managing-backups-transact-sql) | いいえ | はい、Azure Blob Storage に配置されているバックアップ ファイルでは、`FROM URL` オプションが必須となります。 [復元の相違点](../managed-instance/transact-sql-tsql-differences-sql-server.md#restore-statement)に関する記述を参照してください |
| [バックアップからデータベースを復元する](/sql/relational-databases/backup-restore/back-up-and-restore-of-sql-server-databases#restore-data-backups) | 自動バックアップからのみ - [SQL Database の復旧](recovery-using-backups.md)に関する記事をご覧ください | 自動バックアップから - [SQL Database の復旧](recovery-using-backups.md)に関するページを参照してください。Azure Blob Storage に配置されている完全バックアップから - [バックアップの相違点](../managed-instance/transact-sql-tsql-differences-sql-server.md#backup)に関する記述を参照してください |
| [データベースを SQL Server に復元する](/sql/relational-databases/backup-restore/back-up-and-restore-of-sql-server-databases#restore-data-backups) | いいえ。 ネイティブ復元の代わりに BACPAC または BCP を使用します。 | いいえ、SQL Managed Instance で使用されている SQL Server データベース エンジンには、オンプレミスで使用されている SQL Server のどの RTM バージョンよりも新しいバージョンがあります。 代わりに BACPAC、BCP、またはトランザクション レプリケーションを使用します。 |
| [セマンティック検索](/sql/relational-databases/search/semantic-search-sql-server) | いいえ | いいえ |
| [Service Broker](/sql/database-engine/configure-windows/sql-server-service-broker) | いいえ | はい、ただし、インスタンス内でのみとなります。 リモート Service Broker ルートを使用している場合は、移行時に複数の分散 SQL Server インスタンスから 1 つの SQL Managed Instance にデータベースを統合し、ローカル ルートのみを使用してみてください。 [Service Broker の相違点](../managed-instance/transact-sql-tsql-differences-sql-server.md#service-broker)に関する記述を参照してください |
| [サーバーの構成設定](/sql/database-engine/configure-windows/server-configuration-options-sql-server) | いいえ | はい - [T-SQL の相違点](../managed-instance/transact-sql-tsql-differences-sql-server.md)に関する記事を参照してください |
| [SET ステートメント](/sql/t-sql/statements/set-statements-transact-sql) | ほとんどの場合 - 個々のステートメントに関する記事を参照してください | はい - [T-SQL の相違点](../managed-instance/transact-sql-tsql-differences-sql-server.md)に関する記事を参照してください|
| [SQL Server エージェント](/sql/ssms/agent/sql-server-agent) | いいえ - [エラスティック ジョブ (プレビュー)](elastic-jobs-overview.md) に関する記事を参照してください | はい - [SQL Server エージェントの相違点](../managed-instance/transact-sql-tsql-differences-sql-server.md#sql-server-agent)に関する記事を参照してください |
| [SQL Server 監査](/sql/relational-databases/security/auditing/sql-server-audit-database-engine) | いいえ - [SQL Database の監査](auditing-overview.md)に関する記事を参照してください | はい - [監査の相違点](../managed-instance/transact-sql-tsql-differences-sql-server.md#auditing)に関するページを参照してください |
| [システム ストアド関数](/sql/relational-databases/system-functions/system-functions-for-transact-sql) | ほとんどの場合 - 個々の関数に関する記事を参照してください | はい - [ストアド プロシージャ、関数、トリガーの相違点](../managed-instance/transact-sql-tsql-differences-sql-server.md#stored-procedures-functions-and-triggers)に関するページを参照してください |
| [システム ストアド プロシージャ](/sql/relational-databases/system-stored-procedures/system-stored-procedures-transact-sql) | 一部 - 個々のストアド プロシージャに関する記事を参照してください | はい - [ストアド プロシージャ、関数、トリガーの相違点](../managed-instance/transact-sql-tsql-differences-sql-server.md#stored-procedures-functions-and-triggers)に関するページを参照してください |
| [システム テーブル](/sql/relational-databases/system-tables/system-tables-transact-sql) | 一部 - 個々のテーブルに関する記事を参照してください | はい - [T-SQL の相違点](../managed-instance/transact-sql-tsql-differences-sql-server.md)に関する記事を参照してください |
| [システム カタログ ビュー](/sql/relational-databases/system-catalog-views/catalog-views-transact-sql) | 一部 - 個々のビューに関する記事を参照してください | はい - [T-SQL の相違点](../managed-instance/transact-sql-tsql-differences-sql-server.md)に関する記事を参照してください |
| [TempDB](/sql/relational-databases/databases/tempdb-database) | はい。 [すべてのデータベースのコアあたり 32 GB のサイズ](resource-limits-vcore-single-databases.md)。 | はい。 [GP 層全体で仮想コアあたり 24 GB のサイズで、BC 層のインスタンスのサイズに制限されます](../managed-instance/resource-limits.md#service-tier-characteristics)  |
| [一時テーブル](/sql/t-sql/statements/create-table-transact-sql#database-scoped-global-temporary-tables-azure-sql-database) | ローカルおよびデータベース スコープのグローバル一時テーブル | ローカルおよびインスタンス スコープのグローバル一時テーブル |
| タイム ゾーンの選択 | いいえ | [はい](../managed-instance/timezones-overview.md)、SQL Managed Instance の作成時に構成する必要があります。 |
| [トレース フラグ](/sql/t-sql/database-console-commands/dbcc-traceon-trace-flags-transact-sql) | いいえ | はい。ただし、グローバル トレース フラグは限られています。 [DBCC の相違点](../managed-instance/transact-sql-tsql-differences-sql-server.md#dbcc)に関するページを参照してください |
| [トランザクション レプリケーション](../managed-instance/replication-transactional-overview.md) | はい、[トランザクション レプリケーションとスナップショット レプリケーションのサブスクライバーのみ](migrate-to-database-from-sql-server.md) | はい、[パブリック プレビュー](/sql/relational-databases/replication/replication-with-sql-database-managed-instance)中。 [こちら](../managed-instance/transact-sql-tsql-differences-sql-server.md#replication)で制約を参照してください。 |
| [透過的なデータ暗号化 (TDE)](/sql/relational-databases/security/encryption/transparent-data-encryption-tde) | はい - General Purpose および Business Critical サービス レベルのみ| [はい](transparent-data-encryption-tde-overview.md) |
| Windows 認証 | いいえ | いいえ |
| [Windows Server フェールオーバー クラスタリング](/sql/sql-server/failover-clusters/windows/windows-server-failover-clustering-wsfc-with-sql-server) | いいえ。 [高可用性](high-availability-sla.md)を提供するその他の手法は、すべてのデータベースに含まれています。 ディザスター リカバリーに関する解説は、「[Azure SQL Database によるビジネス継続性の概要](business-continuity-high-availability-disaster-recover-hadr-overview.md)」を参照してください。 | いいえ。 [高可用性](high-availability-sla.md)を提供するその他の手法は、すべてのデータベースに含まれています。 ディザスター リカバリーに関する解説は、「[Azure SQL Database によるビジネス継続性の概要](business-continuity-high-availability-disaster-recover-hadr-overview.md)」を参照してください。 |

## <a name="platform-capabilities"></a>プラットフォームの機能

Azure プラットフォームには、標準のデータベース機能に追加の価値として追加される多数の PaaS 機能が用意されています。 Azure SQL Database で使用できる外部サービスは多数あります。

| **プラットフォーム機能** | **Azure SQL Database** | **Azure SQL Managed Instance** |
| --- | --- | --- |
| [アクティブ geo レプリケーション](active-geo-replication-overview.md) | はい - ハイパースケール以外のすべてのサービス レベル | いいえ。代わりに[自動フェールオーバー グループ](auto-failover-group-overview.md)に関するページを参照してください |
| [自動フェールオーバー グループ](auto-failover-group-overview.md) | はい - ハイパースケール以外のすべてのサービス レベル | はい。[自動フェールオーバー グループ](auto-failover-group-overview.md)に関するページを参照してください|
| 自動スケール | はい、[サーバーレス モデル](serverless-tier-overview.md)でのみ。 サーバーレス モデル以外では、サービス レベルの変更 (仮想コア、ストレージ、または DTU の変更) は高速でオンラインで行われます。 サービス レベルの変更には、最小限またはゼロ ダウンタイムが必要です。 | いいえ、予約済みのコンピューティングとストレージを選択する必要があります。 サービス レベル (仮想コアまたは最大ストレージ) の変更はオンラインで行われ、最小限またはゼロ ダウンタイムが必要です。 |
| [自動バックアップ](automated-backups-overview.md) | はい。 完全バックアップは 7 日おき、差分は 12 時間おきに行われ、ログ バックアップは 5 分から 10 分おきに行われます。 | はい。 完全バックアップは 7 日おき、差分は 12 時間おきに行われ、ログ バックアップは 5 分から 10 分おきに行われます。 |
| [自動チューニング (インデックス)](/sql/relational-databases/automatic-tuning/automatic-tuning)| [はい](automatic-tuning-overview.md)| いいえ |
| [可用性ゾーン](../../availability-zones/az-overview.md) | はい | いいえ |
| [Azure Resource Health](../../service-health/resource-health-overview.md) | はい | いいえ |
| バックアップ保有期間 | はい。 既定値は 7 日、最大値は 35 日です。 | はい。 既定値は 7 日、最大値は 35 日です。 |
| [データ移行サービス (DMS)](/sql/dma/dma-overview) | はい | はい |
| [エラスティック ジョブ](elastic-jobs-overview.md) | はい - [エラスティック ジョブ (プレビュー)](elastic-jobs-overview.md) に関する記事をご覧ください | いいえ ([SQL Agent](../managed-instance/transact-sql-tsql-differences-sql-server.md#sql-server-agent) を代わりに使用できます)。 |
| ファイル システムへのアクセス | いいえ。 代わりとして [BULK INSERT](/sql/t-sql/statements/bulk-insert-transact-sql#f-importing-data-from-a-file-in-azure-blob-storage) または [OPENROWSET](/sql/t-sql/functions/openrowset-transact-sql#i-accessing-data-from-a-file-stored-on-azure-blob-storage) を使用して、Azure Blob Storage のデータにアクセスし、Azure Blob Storage からデータを読み込みます。 | いいえ。 代わりとして [BULK INSERT](/sql/t-sql/statements/bulk-insert-transact-sql#f-importing-data-from-a-file-in-azure-blob-storage) または [OPENROWSET](/sql/t-sql/functions/openrowset-transact-sql#i-accessing-data-from-a-file-stored-on-azure-blob-storage) を使用して、Azure Blob Storage のデータにアクセスし、Azure Blob Storage からデータを読み込みます。 |
| [geo リストア](recovery-using-backups.md#geo-restore) | はい | はい |
| [Hyperscale アーキテクチャ](service-tier-hyperscale.md) | はい | いいえ |
| [長期的なバックアップ保有期間 - (LTR)](long-term-retention-overview.md) | はい、自動的に取られたバックアップを最大 10 年間保持します。 | まだありません。 一時的な回避策として`COPY_ONLY` [手動バックアップ](../managed-instance/transact-sql-tsql-differences-sql-server.md#backup)を使用してください。 |
| 一時停止/再開 | はい、[サーバー レスモデル](serverless-tier-overview.md)で | いいえ |
| [ポリシーベースの管理](/sql/relational-databases/policy-based-management/administer-servers-by-using-policy-based-management) | いいえ | いいえ |
| パブリック IP アドレス | はい。 アクセスは、ファイアウォールまたはサービス エンドポイントを使用して制限できます。  | はい。 明示的に有効にする必要があり、NSG ルールでポート 3342 を有効にする必要があります。 必要に応じて、パブリック IP を無効にすることができます。 詳細については、[パブリック エンドポイント](../managed-instance/public-endpoint-overview.md)に関するページを参照してください。 |
| [データベースのポイントインタイム リストア](/sql/relational-databases/backup-restore/restore-a-sql-server-database-to-a-point-in-time-full-recovery-model) | はい - ハイパースケール以外のすべてのサービス レベル - [SQL Database の復旧](recovery-using-backups.md#point-in-time-restore)に関する記事を参照してください | はい - [SQL Database の復旧](recovery-using-backups.md#point-in-time-restore)に関する記事を参照してください |
| リソース プール | はい、[エラスティック プール](elastic-pool-overview.md)として | はい。 SQL Managed Instance の単一のインスタンスは、同じリソース プールを共有する複数のデータベースを保持できます。 さらに、リソースを共有できる [インスタンス プール (プレビュー)](../managed-instance/instance-pools-overview.md) に SQL Managed Instance の複数のインスタンスをデプロイすることもできます。 |
| スケールアップまたはスケールダウン (オンライン) | はい、最小限のダウンタイムで、DTU または予約済みの仮想コアまたは最大ストレージを変更できます。 | はい、最小限のダウンタイムで予約済みの仮想コアまたは最大ストレージを変更できます。 |
| [SQL エイリアス](/sql/database-engine/configure-windows/create-or-delete-a-server-alias-for-use-by-a-client) | いいえ、[DNS エイリアス](dns-alias-overview.md)を使用します。 | いいえ、[Clicongf](https://techcommunity.microsoft.com/t5/Azure-Database-Support-Blog/Lesson-Learned-33-How-to-make-quot-cliconfg-quot-to-work-with/ba-p/369022) を使用してクライアント コンピューターにエイリアスを設定します。 |
| [SQL Analytics](../../azure-monitor/insights/azure-sql.md) | はい | はい |
| [SQL データ同期](sql-data-sync-sql-server-configure.md) | はい | いいえ |
| [SQL Server Analysis Services (SSAS)](/sql/analysis-services/analysis-services) | いいえ、[Azure Analysis Services](https://azure.microsoft.com/services/analysis-services/) は別の Azure クラウド サービスです。 | いいえ、[Azure Analysis Services](https://azure.microsoft.com/services/analysis-services/) は別の Azure クラウド サービスです。 |
| [SQL Server Integration Services (SSIS)](/sql/integration-services/sql-server-integration-services) | はい。Azure Data Factory (ADF) 環境で管理された SSIS を使用します。このパッケージは、Azure SQL Database でホストされている SSISDB に格納され、Azure SSIS Integration Runtime (IR) で実行されます。[ADF での Azure-SSIS IR の作成](../../data-factory/create-azure-ssis-integration-runtime.md)に関するページをご覧ください。 <br/><br/>SQL Database と SQL Managed Instance の SSIS 機能を比較するには、[SQL Database と SQL Managed Instance の比較](../../data-factory/create-azure-ssis-integration-runtime.md#comparison-of-sql-database-and-sql-managed-instance)に関するセクションを参照してください。 | はい。Azure Data Factory (ADF) 環境で管理された SSIS を使用します。このパッケージは、SQL Managed Instance によってホストされている SSISDB に格納され、Azure SSIS Integration Runtime (IR) で実行されます。[ADF での Azure-SSIS IR の作成](../../data-factory/create-azure-ssis-integration-runtime.md)に関するページをご覧ください。 <br/><br/>SQL Database と SQL Managed Instance の SSIS 機能を比較するには、[SQL Database と SQL Managed Instance の比較](../../data-factory/create-azure-ssis-integration-runtime.md#comparison-of-sql-database-and-sql-managed-instance)に関するセクションを参照してください。 |
| [SQL Server Reporting Services (SSRS)](/sql/reporting-services/create-deploy-and-manage-mobile-and-paginated-reports) | いいえ - [Power BI](/power-bi/) に関する記事を参照してください | いいえ - 代わりに [Power BI のページ分割されたレポート](/power-bi/paginated-reports/paginated-reports-report-builder-power-bi)を使用するか、Azure VM で SSRS をホストしてください。 SQL Managed Instance では SSRS をサービスとして実行できませんが、SQL Server 認証を使用して、Azure 仮想マシンにインストールされているレポート サーバーに対して [SSRS カタログ データベース](/sql/reporting-services/install-windows/ssrs-report-server-create-a-report-server-database#database-server-version-requirements)をホストできます。 |
| [Query Performance Insights (QPI)](query-performance-insight-use.md) | はい | いいえ。 SQL Server Management Studio と Azure Data Studio で組み込みのレポートを使用します。 |
| [VNet](../../virtual-network/virtual-networks-overview.md) | 部分的、[VNet エンドポイント](vnet-service-endpoint-rule-overview.md)を使用して制限付きアクセスを有効にします | はい、SQL Managed Instance は顧客の VNet に組み込まれます。 [サブネット](../managed-instance/transact-sql-tsql-differences-sql-server.md#subnet)と [VNet](../managed-instance/transact-sql-tsql-differences-sql-server.md#vnet) を参照してください |
| VNet サービス エンドポイント | [はい](vnet-service-endpoint-rule-overview.md) | いいえ |
| VNet グローバル ピアリング | はい、[Private IP とサービス エンドポイント](vnet-service-endpoint-rule-overview.md)を使用します。 | はい。[仮想ネットワーク ピアリング](https://techcommunity.microsoft.com/t5/azure-sql/new-feature-global-vnet-peering-support-for-azure-sql-managed/ba-p/1746913)を使用します。 |

## <a name="tools"></a>ツール

Azure SQL Database と Azure SQL Managed Instance では、データの管理に役立つさまざまなデータ ツールがサポートされています。

| **ツール** | **Azure SQL Database** | **Azure SQL Managed Instance** |
| --- | --- | --- |
| Azure portal | はい | はい |
| Azure CLI | はい | はい|
| [Azure Data Studio](/sql/azure-data-studio/what-is) | はい | はい |
| Azure PowerShell | はい | はい |
| [BACPAC ファイル (エクスポート)](/sql/relational-databases/data-tier-applications/export-a-data-tier-application) | はい - [SQL Database のエクスポート](database-export.md)に関する記事を参照してください | はい - [SQL Managed Instance のエクスポート](database-export.md)に関する記事を参照してください |
| [BACPAC ファイル (インポート)](/sql/relational-databases/data-tier-applications/import-a-bacpac-file-to-create-a-new-user-database) | はい - [SQL Database のインポート](database-import.md)に関する記事を参照してください | はい - [SQL Managed Instance のインポート](database-import.md)に関する記事を参照してください |
| [Data Quality Services (DQS)](/sql/data-quality-services/data-quality-services) | いいえ | いいえ |
| [マスター データ サービス (MDS)](/sql/master-data-services/master-data-services-overview-mds) | いいえ | いいえ |
| [SMO](/sql/relational-databases/server-management-objects-smo/sql-server-management-objects-smo-programming-guide) | [はい](https://www.nuget.org/packages/Microsoft.SqlServer.SqlManagementObjects) | はい。[バージョン 150](https://www.nuget.org/packages/Microsoft.SqlServer.SqlManagementObjects) に関するページを参照してください |
| [SQL Server Data Tools (SSDT)](/sql/ssdt/download-sql-server-data-tools-ssdt) | はい | はい |
| [SQL Server Management Studio (SSMS)](/sql/ssms/download-sql-server-management-studio-ssms) | はい | はい。[バージョン 18.0 以降](/sql/ssms/download-sql-server-management-studio-ssms)に関するページを参照してください |
| [SQL Server PowerShell](/sql/relational-databases/scripting/sql-server-powershell) | はい | はい |
| [SQL Server Profiler](/sql/tools/sql-server-profiler/sql-server-profiler) | いいえ - [拡張イベント](xevent-db-diff-from-svr.md)に関する記事を参照してください | はい |
| [System Center Operations Manager (SCOM)](/system-center/scom/welcome) | [はい](https://www.microsoft.com/download/details.aspx?id=38829) | [はい](https://www.microsoft.com/en-us/download/details.aspx?id=101203) |

## <a name="migration-methods"></a>移行の方法

さまざまな移行方法を使用して、SQL Server、Azure SQL Database、Azure SQL Managed Instance 間でデータを移動することができます。 **オンライン** の手法の場合、移行中、ソースで行われた変更がすべて集められます。一方で **オフライン** の手法の場合、移行の進行中、ソースでデータを変更しているワークロードを停止する必要があります。

| **ソース** | **Azure SQL Database** | **Azure SQL Managed Instance** |
| --- | --- | --- |
| SQL Server (オンプレミス、AzureVM、Amazon RDS) | **オンライン:** [データ移行サービス (DMS)](/sql/dma/dma-overview)、[トランザクション レプリケーション](../managed-instance/replication-transactional-overview.md) <br/> **オフライン:** [BACPAC ファイル (インポート)](/sql/relational-databases/data-tier-applications/import-a-bacpac-file-to-create-a-new-user-database)、BCP | **オンライン:** [データ移行サービス (DMS)](/sql/dma/dma-overview)、[トランザクション レプリケーション](../managed-instance/replication-transactional-overview.md) <br/> **オフライン:** ネイティブ バックアップ/復元、[BACPAC ファイル (インポート)](/sql/relational-databases/data-tier-applications/import-a-bacpac-file-to-create-a-new-user-database)、BCP、[スナップショット レプリケーション](../managed-instance/replication-transactional-overview.md) |
| 単一データベース | **オフライン:** [BACPAC ファイル (インポート)](/sql/relational-databases/data-tier-applications/import-a-bacpac-file-to-create-a-new-user-database)、BCP | **オフライン:** [BACPAC ファイル (インポート)](/sql/relational-databases/data-tier-applications/import-a-bacpac-file-to-create-a-new-user-database)、BCP |
| SQL Managed Instance | **オンライン:** [トランザクション レプリケーション](../managed-instance/replication-transactional-overview.md) <br/> **オフライン:** [BACPAC ファイル (インポート)](/sql/relational-databases/data-tier-applications/import-a-bacpac-file-to-create-a-new-user-database)、BCP、[スナップショット レプリケーション](../managed-instance/replication-transactional-overview.md) | **オンライン:** [トランザクション レプリケーション](../managed-instance/replication-transactional-overview.md) <br/> **オフライン:** クロス インスタンスのポイントインタイム リストア ([Azure PowerShell](/powershell/module/az.sql/restore-azsqlinstancedatabase#examples) または [Azure CLI](https://techcommunity.microsoft.com/t5/Azure-SQL-Database/Cross-instance-point-in-time-restore-in-Azure-SQL-Database/ba-p/386208))、[ネイティブ バックアップ/復元](../managed-instance/restore-sample-database-quickstart.md)、[BACPAC ファイル (インポート)](/sql/relational-databases/data-tier-applications/import-a-bacpac-file-to-create-a-new-user-database)、BCP、[スナップショット レプリケーション](../managed-instance/replication-transactional-overview.md) |

## <a name="next-steps"></a>次のステップ

マイクロソフトは、Azure SQL Database に継続的に機能を追加します。 最新の更新プログラムについては、以下のフィルターを使用して、Azure のサービスの更新情報 Web ページを参照してください。

- [Azure SQL Database](https://azure.microsoft.com/updates/?service=sql-database) にフィルターが適用されています。
- SQL Database の機能について、[General Availability \(GA\) のアナウンス](https://azure.microsoft.com/updates/?service=sql-database&update-type=general-availability)でフィルターが適用されています。

Azure SQL Database と Azure SQL Managed Instance の詳細については、以下を参照してください。

- [Azure SQL Database とは何ですか?](sql-database-paas-overview.md)
- [Azure SQL Managed Instance とは](../managed-instance/sql-managed-instance-paas-overview.md)
- [Azure SQL Managed Instance プールとは](../managed-instance/instance-pools-overview.md)
