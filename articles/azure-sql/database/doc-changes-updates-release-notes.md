---
title: 新機能
titleSuffix: Azure SQL Database & SQL Managed Instance
description: Azure SQL Database と SQL Managed Instance の新機能とドキュメントの改善について説明します。
services: sql-database
author: stevestein
ms.service: sql-db-mi
ms.subservice: service
ms.custom: sqldbrb=2
ms.devlang: ''
ms.topic: conceptual
ms.date: 03/10/2021
ms.author: sstein
ms.openlocfilehash: 0ddd2c96be3513d253537cefd5b9eb83da2b3c12
ms.sourcegitcommit: b572ce40f979ebfb75e1039b95cea7fce1a83452
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/11/2021
ms.locfileid: "102634840"
---
# <a name="whats-new-in-azure-sql-database--sql-managed-instance"></a>Azure SQL Database と SQL Managed Instance の新機能
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

この記事では、現在パブリック プレビュー段階にある Azure SQL Database と Azure SQL Managed Instance の機能を示します。 SQL Database と SQL Managed Instance の更新情報や機能強化については、[SQL Database と SQL Managed Instance サービスの更新情報](https://azure.microsoft.com/updates/?product=sql-database)に関するページを参照してください。 他の Azure サービスの更新情報や機能強化については、[サービスの更新情報](https://azure.microsoft.com/updates)を参照してください。

## <a name="whats-new"></a>新機能

Azure SQL Database と Azure SQL Managed Instance のドキュメントは別々のセクションに分割されました。 また、*Azure SQL Database マネージド インスタンス* からマネージド インスタンスを参照する方法を *Azure SQL Managed Instance* へ更新しました。

これは、一部の特徴や機能が単一データベースとマネージド インスタンスの間で大きく異なるための処置です。個々の共有記事で Azure SQL Database と Azure SQL Managed Instance 間の複雑な差異を説明することはますます困難になっています。

さまざまな Azure SQL 製品間を明確に区別することで、Azure での SQL Server データベース エンジンの操作が簡素化され、合理化されます。これは、Azure SQL Database の単一マネージド データベースであるか、Azure SQL Managed Instance で複数のデータベースをホストする本格的なマネージド インスタンスであるか、それとも Azure の仮想マシンでホストされる使い慣れたオンプレミスの SQL Server 製品であるかを問いません。

これは進行中の作業であり、まだ更新されていない記事があることを考慮してください。 たとえば、Azure SQL Database と Azure SQL Managed Instance 間で共有される Transact-SQL (T-SQL) ステートメントやストアド プロシージャ、その他多くの機能についてのドキュメント化はまだ完了していません。コンテンツの明確化作業が続く間、ご不便をおかけして申し訳ございません。 

次の表は、用語の変更に関して簡単に比較しています。 


|**新しい用語**  | **従来の用語**  |**説明** |
|---------|---------|---------|
|**Azure SQL Managed Instance** | Azure SQL Database *マネージド インスタンス*| Azure SQL Managed Instance は、Azure SQL Database 内のデプロイ オプションではなく、Azure SQL ファミリ内の独自の製品です。 | 
|**Azure SQL Database**|Azure SQL Database *単一データベース*| 明示的に指定しない限り、"Azure SQL Database" という製品名には、単一データベースと、エラスティック プールにデプロイされたデータベースの両方が含まれます。 |
|**Azure SQL Database**|Azure SQL Database *エラスティック プール*| 明示的に指定しない限り、"Azure SQL Database" という製品名には、単一データベースと、エラスティック プールにデプロイされたデータベースの両方が含まれます。  |
|**Azure SQL Database** |Azure SQL データベース | この用語は同じままですが、単一データベースとエラスティック プールのデプロイにのみ適用され、マネージド インスタンスは含まれません。 |
| **Azure SQL**| 該当なし | これは、Azure で使用可能な SQL Server データベース エンジン製品のファミリを指します。Azure SQL Database、Azure SQL Managed Instance、および Azure VM 上の SQL Server。 | 

## <a name="features-in-public-preview"></a>パブリック プレビュー段階の機能

### <a name="azure-sql-database"></a>[Azure SQL Database](#tab/single-database)

| 機能 | 詳細 |
| ---| --- |
| Elastic Database ジョブ (プレビュー) | 詳しくは、「[エラスティック ジョブの作成、構成、および管理](elastic-jobs-overview.md)」をご覧ください。 |
| エラスティック クエリ | 詳しくは、[エラスティック クエリの概要](elastic-query-overview.md)に関する記事をご覧ください。 |
| エラスティック トランザクション | [クラウド データベースにまたがる分散トランザクション](elastic-transactions-overview.md)。 |
| Azure portal のクエリ エディター |詳しくは、「[Azure portal の SQL クエリ エディターを使用した接続とデータの照会](connect-query-portal.md)」をご覧ください。|
|SQL Analytics|詳細については、[Azure SQL Analytics](../../azure-monitor/insights/azure-sql.md) に関するページをご覧ください。|
| &nbsp; |

### <a name="azure-sql-managed-instance"></a>[Azure SQL Managed Instance](#tab/managed-instance)

| 機能 | 詳細 |
| ---| --- |
| <a href="/azure/azure-sql/database/elastic-transactions-overview">分散トランザクション</a> | マネージド インスタンスにまたがる分散トランザクション。 |
| <a href="/azure/sql-database/sql-database-instance-pools">インスタンス プール</a> | 比較的小規模な SQL インスタンスをクラウドに移行するための、便利かつ費用対効果に優れた方法です。 |
| <a href="/en-gb/sql/t-sql/statements/create-login-transact-sql">インスタンス レベルの Azure AD サーバー プリンシパル (ログイン)</a> | <a href="/sql/t-sql/statements/create-login-transact-sql?view=azuresqldb-mi-current&preserve-view=true">CREATE LOGIN FROM EXTERNAL PROVIDER</a> ステートメントを使って、インスタンスレベルのログインを作成します。 |
| [トランザクション レプリケーション](../managed-instance/replication-transactional-overview.md) | テーブルの変更を、SQL Managed Instance、SQL Database、または SQL Server の他のデータベースにレプリケートします。 または、SQL Managed Instance や SQL Server の他のインスタンスで一部の行が変更されたときに、テーブルを更新します。 詳しくは、[Azure SQL Managed Instance にレプリケーションを構成する方法](../managed-instance/replication-between-two-instances-configure-tutorial.md)に関する記事をご覧ください。 |
| 脅威の検出 |詳しくは、[Azure SQL Managed Instance で脅威検出を構成する方法](../managed-instance/threat-detection-configure.md)に関する記事をご覧ください。|
| 長期のバックアップ リテンション期間 | 詳細については、現在は制限付きパブリック プレビュー段階である [Azure SQL Managed Instance での長期のバックアップ リテンション期間の構成](../managed-instance/long-term-backup-retention-configure.md)に関するページをご覧ください。 | 

---

## <a name="new-features"></a>新機能

### <a name="sql-managed-instance-h2-2019-updates"></a>2019 年下期の SQL Managed Instance の更新プログラム

- [サービス支援サブネット構成](https://azure.microsoft.com/updates/service-aided-subnet-configuration-for-managed-instance-in-azure-sql-database-available/)は、ユーザーがデータ トラフィックを制御しながら、SQL Managed Instance によって管理トラフィックのフローが中断されないことが保証される、サブネット構成を管理するためのセキュリティで保護された便利な方法です。
- [Bring Your Own Key (BYOK) による Transparent Data Encryption (TDE)](https://azure.microsoft.com/updates/general-avilability-transparent-data-encryption-with-customer-managed-keys-for-azure-sql-database-managed-instance/) により、保存時のデータ保護として Bring Your Own Key (BYOK) シナリオが可能になり、組織はキーとデータに関する管理の職務を分離できるようになります。
- [自動フェールオーバー グループ](https://azure.microsoft.com/updates/azure-sql-database-auto-failover-groups-feature-now-available-in-all-regions/)では、プライマリ インスタンスから別のリージョンにあるセカンダリ インスタンスへの、すべてのデータベースのレプリケートが有効になります。
- [グローバル トレース フラグ](https://azure.microsoft.com/updates/global-trace-flags-are-now-available-in-azure-sql-database-managed-instance/)を使用すると、SQL Managed Instance の動作を構成できます。

### <a name="sql-managed-instance-h1-2019-updates"></a>2019 年上期の SQL Managed Instance の更新プログラム

2019 年上期の SQL Managed Instance のデプロイ モデルでは、次の機能が有効になっています。
  - <a href="/azure/azure-sql/managed-instance/resource-limits">Visual Studio サブスクライバー向けの Azure の月単位のクレジット</a>と強化された[リージョン制限](../managed-instance/resource-limits.md#regional-resource-limitations)を備えたサブスクリプションのサポート
  - <a href="/sharepoint/administration/deploy-azure-sql-managed-instance-with-sharepoint-servers-2016-2019"> SharePoint 2016、SharePoint 2019</a>、および <a href="/business-applications-release-notes/october18/dynamics365-business-central/support-for-azure-sql-database-managed-instance">Dynamics 365 Business Central</a> のサポート。
  - 選択した<a href="/azure/azure-sql/managed-instance/scripts/create-powershell-azure-resource-manager-template">インスタンスレベルの照合順序</a>と<a href="https://azure.microsoft.com/updates/managed-instance-time-zone-ga/">タイム ゾーン</a>を使用して、マネージド インスタンスを作成します。
  - マネージド インスタンスは現在、[組み込みのファイアウォール](../managed-instance/management-endpoint-verify-built-in-firewall.md)によって保護されています。
  - より優れたネットワーク パフォーマンスと、<a href="https://aka.ms/four-cores-sql-mi-update">Gen5 ハードウェア世代における 4 仮想コア</a>またはポイントインタイム リストアのための<a href="/azure/azure-sql/database/automated-backups-overview">最大 35 日間のバックアップ リテンション期間の構成</a>を得るために、[パブリック エンドポイント](../managed-instance/public-endpoint-configure.md)と [Proxy Override](connectivity-architecture.md#connection-policy) 接続を使用するように、SQL Managed Instance を構成します。 [長期のバックアップ リテンション期間](long-term-retention-overview.md) (最長 10 年間) は現在、パブリック プレビュー段階です。  
  - 新しい機能を利用すると、<a href="https://medium.com/@jocapc/geo-restore-your-databases-on-azure-sql-instances-1451480e90fa">PowerShell を使用してお使いのデータベースを別のデータ センターに geo リストアして</a>、[データベース名を変更し](https://azure.microsoft.com/updates/azure-sql-database-managed-instance-database-rename-is-supported/)、[仮想クラスターを削除する](../managed-instance/virtual-cluster-delete.md)ことができます。
  - 新しい組み込みの[インスタンス共同作成者ロール](../../role-based-access-control/built-in-roles.md#sql-managed-instance-contributor)を使用すると、セキュリティ原則による職務の分離 (SoD) のコンプライアンスとエンタープライズ標準によるコンプライアンスを有効にできます。
  - SQL Managed Instance は、次の Azure Government リージョン (US Gov テキサス、US Gov アリゾナ) で GA として、さらに中国北部 2 と中国東部 2 で使用できます。 また、次のパブリック リージョンでも利用できます。オーストラリア中部、オーストラリア中部 2、ブラジル南部、フランス南部、アラブ首長国連邦中部、アラブ首長国連邦北部、南アフリカ北部、南アフリカ西部です。

## <a name="known-issues"></a>既知の問題

|問題  |検出した日  |Status  |解決した日  |
|---------|---------|---------|---------|
|[@query パラメーターの使用時、プロシージャ sp_send_dbmail が一時的に失敗する可能性がある](#procedure-sp_send_dbmail-may-transiently-fail-when--parameter-is-used)|2021 年 1 月|回避策あり||
|[サーバー信頼グループからマネージド インスタンスを削除した後、分散トランザクションを実行できる](#distributed-transactions-can-be-executed-after-removing-managed-instance-from-server-trust-group)|2020 年 10 月|回避策あり||
|[マネージド インスタンスのスケーリング操作の後、分散トランザクションを実行できない](#distributed-transactions-cannot-be-executed-after-managed-instance-scaling-operation)|2020 年 10 月|回避策あり||
|Azure SQL の [BULK INSERT](/sql/t-sql/statements/bulk-insert-transact-sql)/[OPENROWSET](/sql/t-sql/functions/openrowset-transact-sql)、およびマネージド インスタンスの `BACKUP`/`RESTORE` ステートメントで、Azure AD の Manage Identity を使用して Azure Storage に対する認証を実行できない|2020 年 9 月|回避策あり||
|[サービス プリンシパルから Azure AD および AKV にアクセスできません](#service-principal-cannot-access-azure-ad-and-akv)|2020 年 8 月|回避策あり||
|[CHECKSUM を使用せずに手動バックアップを復元すると失敗することがある](#restoring-manual-backup-without-checksum-might-fail)|2020 年 5 月|解決済み|2020 年 6 月|
|[既存のジョブを変更、無効化、または有効化するとエージェントが応答しなくなる](#agent-becomes-unresponsive-upon-modifying-disabling-or-enabling-existing-jobs)|2020 年 5 月|解決済み|2020 年 6 月|
|[リソース グループに対するアクセス許可が SQL Managed Instance に適用されない](#permissions-on-resource-group-not-applied-to-sql-managed-instance)|2020 年 2 月|解決済み|2020 年 11 月|
|[ポータルを使用したフェールオーバー グループに対する手動フェールオーバーの制限](#limitation-of-manual-failover-via-portal-for-failover-groups)|2020 年 1 月|回避策あり||
|[SQL Agent ロールには、sysadmin 以外のログインに対する明示的な EXECUTE 権限が必要です](#in-memory-oltp-memory-limits-are-not-applied)|2019 年 12 月|回避策あり||
|[エージェント プロセスを再起動すると、SQL Agent ジョブが中断されることがある](#sql-agent-jobs-can-be-interrupted-by-agent-process-restart)|2019 年 12 月|解決済み|2020 年 3 月|
|[SSDT 内で Azure AD のログインとユーザーがサポートされない](#azure-ad-logins-and-users-are-not-supported-in-ssdt)|2019 年 11 月|回避策なし||
|[インメモリ OLTP のメモリ制限が適用されない](#in-memory-oltp-memory-limits-are-not-applied)|2019 年 10 月|回避策あり||
|[空ではないファイルを削除しようとしたときに誤ったエラーが返される](#wrong-error-returned-while-trying-to-remove-a-file-that-is-not-empty)|2019 年 10 月|回避策あり||
|[サービス レベルの変更とインスタンスの作成操作が、進行中のデータベースの復元によってブロックされる](#change-service-tier-and-create-instance-operations-are-blocked-by-ongoing-database-restore)|2019 年 9 月|回避策あり||
|[Business Critical サービス レベルの Resource Governor をフェールオーバー後に再構成しなければならない場合がある](#resource-governor-on-business-critical-service-tier-might-need-to-be-reconfigured-after-failover)|2019 年 9 月|回避策あり||
|[サービス レベルのアップグレード後は、複数データベースにまたがる Service Broker のダイアログを再初期化する必要がある](#cross-database-service-broker-dialogs-must-be-reinitialized-after-service-tier-upgrade)|2019 年 8 月|回避策あり||
|[Azure AD ログイン タイプの偽装がサポートされない](#impersonation-of-azure-ad-login-types-is-not-supported)|2019 年 7 月|回避策なし||
|[sp_send_db_mail の @query パラメーターはサポートされない](#-parameter-not-supported-in-sp_send_db_mail)|2019 年 4 月|解決済み|2021 年 1 月|
|[geo フェールオーバー後、トランザクション レプリケーションを再構成する必要がある](#transactional-replication-must-be-reconfigured-after-geo-failover)|2019 年 3 月|回避策なし||
|[一時的なデータベースが RESTORE 操作中に使用される](#temporary-database-is-used-during-restore-operation)||回避策あり||
|[TEMPDB の構造と内容は再作成される](#tempdb-structure-and-content-is-re-created)||回避策なし||
|[小さなデータベース ファイルによる記憶域の超過](#exceeding-storage-space-with-small-database-files)||回避策あり||
|[データベース名の代わりに GUID 値が表示される](#guid-values-shown-instead-of-database-names)||回避策あり||
|[エラー ログが非永続的である](#error-logs-arent-persisted)||回避策なし||
|[同じインスタンス内にある 2 つのデータベース上でトランザクション スコープがサポートされない](#transaction-scope-on-two-databases-within-the-same-instance-isnt-supported)||回避策あり|2020 年 3 月|
|[CLR モジュールとリンク サーバーでローカル IP アドレスを参照できないことがある](#clr-modules-and-linked-servers-sometimes-cant-reference-a-local-ip-address)||回避策あり||
|Azure Blob Storage からデータベースを復元した後、DBCC CHECKDB を使用してデータベースの整合性が検証されません。||解決済み|2019 年 11 月|
|ソース データベースにインメモリ OLTP オブジェクトが含まれている場合、Business Critical レベルから General Purpose レベルへの組み込みのポイントインタイム データベース復元は成功しません。||解決済み|2019 年 10 月|
|セキュリティで保護された接続を使用する外部 (Azure 以外) メール サーバーのデータベース メール機能||解決済み|2019 年 10 月|
|包含データベースは、SQL Managed Instance 内でサポートされている||解決済み|2019 年 8 月|

### <a name="procedure-sp_send_dbmail-may-transiently-fail-when-query-parameter-is-used"></a>@query パラメーターの使用時、プロシージャ sp_send_dbmail が一時的に失敗する可能性がある

`@query` パラメーターが使用されているとき、プロシージャ sp_send_dbmail が一時的に失敗する可能性があります。 この問題が発生した場合は、プロシージャ sp_send_dbmail の実行が 2 回に 1 回、エラー `Msg 22050, Level 16, State 1` とメッセージ `Failed to initialize sqlcmd library with error number -2147467259` で失敗します。 このエラーを正しく確認できるようにするには、パラメーター `@exclude_query_output` を既定値 0 にしてこのプロシージャを呼び出す必要があります。そうしないと、このエラーは伝達されません。
この問題は、sp_send_dbmail での権限借用と接続プールの使用方法に関連した既知のバグが原因で発生します。
この問題を回避するには、電子メールを送信するためのコードを、出力パラメーター `@mailitem_id` に依存する再試行ロジックにラップします。 実行が失敗した場合は、このパラメーター値が NULL になり、電子メールを正常に送信するには sp_send_dbmail をもう 1 回呼び出す必要があることを示します。 この再試行ロジックの例を次に示します。
```sql
CREATE PROCEDURE send_dbmail_with_retry AS
BEGIN
    DECLARE @miid INT
    EXEC msdb.dbo.sp_send_dbmail
        @recipients = 'name@mail.com', @subject = 'Subject', @query = 'select * from dbo.test_table',
        @profile_name ='AzureManagedInstance_dbmail_profile', @execute_query_database = 'testdb',
        @mailitem_id = @miid OUTPUT

    -- If sp_send_dbmail returned NULL @mailidem_id then retry sending email.
    --
    IF (@miid is NULL)
    EXEC msdb.dbo.sp_send_dbmail
        @recipients = 'name@mail.com', @subject = 'Subject', @query = 'select * from dbo.test_table',
        @profile_name ='AzureManagedInstance_dbmail_profile', @execute_query_database = 'testdb',
END
```

### <a name="distributed-transactions-can-be-executed-after-removing-managed-instance-from-server-trust-group"></a>サーバー信頼グループからマネージド インスタンスを削除した後、分散トランザクションを実行できる

[サーバー信頼グループ](../managed-instance/server-trust-group-overview.md)は、[分散トランザクション](./elastic-transactions-overview.md)を実行するための前提条件である、マネージド インスタンス間の信頼を確立するために使用されます。 サーバー信頼グループからマネージド インスタンスを削除した後、またはグループを削除した後も、分散トランザクションを実行できる可能性があります。 分散トランザクションを確実に無効にするための回避策を適用できます。それは、マネージド インスタンスで[手動フェールオーバーを開始](../managed-instance/user-initiated-failover.md)することです。

### <a name="distributed-transactions-cannot-be-executed-after-managed-instance-scaling-operation"></a>マネージド インスタンスのスケーリング操作の後、分散トランザクションを実行できない

サービス レベルまたは仮想コア数の変更といったマネージド インスタンスのスケーリング操作を実行すると、バックエンドのサーバー信頼グループの設定がリセットされ、[分散トランザクション](./elastic-transactions-overview.md)の実行が無効になります。 回避するには、Azure portal で[サーバー信頼グループ](../managed-instance/server-trust-group-overview.md)を削除して、新しく作成します。

### <a name="bulk-insert-and-backuprestore-statements-cannot-use-managed-identity-to-access-azure-storage"></a>BULK INSERT および BACKUP/RESTORE ステートメントで、マネージド ID を使用して Azure storage にアクセスできない

BULK INSERT、BACKUP、RESTORE ステートメント、および OPENROWSET 関数では、Azure storage に対する認証に、`DATABASE SCOPED CREDENTIAL` をマネージド ID と共に使用することはできません。 回避するには、Shared Access Signature 認証に切り替えます。 次の例は、Azure SQL (データベースと Managed Instance の両方) では機能しません。

```sql
CREATE DATABASE SCOPED CREDENTIAL msi_cred WITH IDENTITY = 'Managed Identity';
GO
CREATE EXTERNAL DATA SOURCE MyAzureBlobStorage
  WITH ( TYPE = BLOB_STORAGE, LOCATION = 'https://****************.blob.core.windows.net/curriculum', CREDENTIAL= msi_cred );
GO
BULK INSERT Sales.Invoices FROM 'inv-2017-12-08.csv' WITH (DATA_SOURCE = 'MyAzureBlobStorage');
```

**回避策**:[Shared Access Signature を使用して、ストレージに対する認証を実行します](/sql/t-sql/statements/bulk-insert-transact-sql#f-importing-data-from-a-file-in-azure-blob-storage)。

### <a name="service-principal-cannot-access-azure-ad-and-akv"></a>サービス プリンシパルから Azure AD および AKV にアクセスできません

場合によっては、Azure AD および Azure Key Vault (AKV) サービスへのアクセスに使用されるサービス プリンシパルに問題が存在することがあります。 そのため、この問題は SQL Managed Instance での Azure AD 認証および Transparent Database Encryption (TDE) の使用に影響します。 これは、断続的な接続の問題として発生する可能性があります。または、CREATE LOGIN/USER FROM EXTERNAL PROVIDER または EXECUTE AS LOGIN/USER などのステートメントを実行できない場合に発生する可能性があります。 新しい Azure SQL Managed Instance 上でカスタマー マネージド キーを使用して TDE を設定しても、状況によっては機能しないことがあります。

**回避策**:更新コマンドを実行する前に、ご利用の SQL Managed Instance 上でこの問題が発生しないようにするには、または更新コマンドの実行後にこの問題が既に発生している場合は、Azure portal に移動し、SQL Managed Instance の [[Active Directory 管理者]](./authentication-aad-configure.md?tabs=azure-powershell#azure-portal) ブレード にアクセスします。 "Azure Active Directory にアクセスするには、Managed Instance にサービス プリンシパルが必要です。 サービス プリンシパルを作成するには、ここをクリックします" というエラー メッセージが表示されるかどうかを確認します。 このエラー メッセージが表示された場合は、それをクリックし、このエラーが解決されるまで、示されるステップ バイ ステップの手順に従います。

### <a name="restoring-manual-backup-without-checksum-might-fail"></a>CHECKSUM を使用せずに手動バックアップを復元すると失敗することがある

特定の状況で、CHECKSUM なしでマネージド インスタンス上に作成されたデータベースの手動バックアップが復元に失敗することがあります。 このような場合は、成功するまでバックアップの復元を再試行してください。

**回避策**:CHECKSUM を有効にして、マネージド インスタンス上のデータベースの手動バックアップを実行します。

### <a name="agent-becomes-unresponsive-upon-modifying-disabling-or-enabling-existing-jobs"></a>既存のジョブを変更、無効化、または有効化するとエージェントが応答しなくなる

状況によっては、既存のジョブを変更したり、無効にしたり、有効にしたりすると、エージェントが応答しなくなることがあります。 この問題は、検出されると自動的に軽減され、エージェント プロセスが再起動されます。

### <a name="permissions-on-resource-group-not-applied-to-sql-managed-instance"></a>リソース グループに対するアクセス許可が SQL Managed Instance に適用されない

リソース グループ (RG) に SQL Managed Instance 共同作成者 Azure ロールが適用されている場合、SQL Managed Instance には適用されず、効果がありません。

**回避策**:ユーザーの SQL Managed Instance 共同作成者ロールをサブスクリプション レベルで設定します。

### <a name="limitation-of-manual-failover-via-portal-for-failover-groups"></a>ポータルを使用したフェールオーバー グループに対する手動フェールオーバーの制限

フェールオーバー グループが、異なる Azure サブスクリプションやリソース グループのインスタンスにまたがっている場合、フェールオーバー グループのプライマリ インスタンスから手動フェールオーバーを開始することはできません。

**回避策**:Geo セカンダリ インスタンスからポータル経由でフェールオーバーを開始します。

### <a name="sql-agent-roles-need-explicit-execute-permissions-for-non-sysadmin-logins"></a>SQL エージェント ロールには、sysadmin 以外のログインに対する明示的な EXECUTE 権限が必要です

sysadmin 以外のログインが [SQL Agent の固定データベース ロール](/sql/ssms/agent/sql-server-agent-fixed-database-roles)に追加されると、これらのログインを機能させるには、明示的な EXECUTE 権限を Master ストアド プロシージャに付与する必要があるという問題が存在します。 この問題が発生した場合は、エラー メッセージ "EXECUTE 権限がオブジェクト <object_name> で拒否されました (Microsoft SQL Server、エラー:229)" が表示されます。

**回避策**:SQL Agent 固定データベース ロール (SQLAgentUserRole、SQLAgentReaderRole、または SQLAgentOperatorRole) にログインを追加した後、これらのロールに追加された各ログインに対して次の T-SQL スクリプトを実行して、一覧表示されているストアド プロシージャに明示的に EXECUTE 権限を付与します。

```tsql
USE [master]
GO
CREATE USER [login_name] FOR LOGIN [login_name]
GO
GRANT EXECUTE ON master.dbo.xp_sqlagent_enum_jobs TO [login_name]
GRANT EXECUTE ON master.dbo.xp_sqlagent_is_starting TO [login_name]
GRANT EXECUTE ON master.dbo.xp_sqlagent_notify TO [login_name]
```

### <a name="sql-agent-jobs-can-be-interrupted-by-agent-process-restart"></a>エージェント プロセスを再起動すると、SQL エージェント ジョブが中断されることがある

**(2020 年 3 月に解決済み)** SQL Agent では、ジョブが開始されるたびに新しいセッションが作成され、メモリ消費量が徐々に増加します。 内部メモリの制限に達してスケジュールされたジョブの実行がブロックされることのないように、エージェント プロセスのメモリ使用量がしきい値に達すると、エージェント プロセスが再起動されます。 これにより、再起動の時点で実行中のジョブの実行が中断される可能性があります。

### <a name="in-memory-oltp-memory-limits-are-not-applied"></a>インメモリ OLTP のメモリ制限が適用されない

Business Critical サービス レベルでは、[メモリ最適化オブジェクトの最大メモリ制限](../managed-instance/resource-limits.md#in-memory-oltp-available-space)が正しく適用されない場合があります。 SQL Managed Instance では、ワークロードが、インメモリ OLTP 操作に対してより多くのメモリを使用できる場合があり、これがインスタンスの可用性と安定性に影響を及ぼすことがあります。 インメモリ OLTP クエリは、制限に達しても、すぐには失敗しない可能性があります。 この問題は、まもなく解決されます。 さらに多くのインメモリ OLTP メモリを使用するクエリは、[制限](../managed-instance/resource-limits.md#in-memory-oltp-available-space)に達するとすぐに失敗するようになります。

**回避策**:[SQL Server Management Studio](/sql/relational-databases/in-memory-oltp/monitor-and-troubleshoot-memory-usage#bkmk_Monitoring) を使用して [インメモリ OLTP ストレージの使用状況を監視](../in-memory-oltp-monitor-space.md)し、使用可能な量を超えるメモリがワークロードによって使用されないようにします。 仮想コアの数に応じてメモリ制限を増やすか、ワークロードを最適化して、使用するメモリを減らします。
 
### <a name="wrong-error-returned-while-trying-to-remove-a-file-that-is-not-empty"></a>空ではないファイルを削除しようとしたときに誤ったエラーが返される

SQL Server と SQL Managed Instance では、[ユーザーは空でないファイルを削除できません](/sql/relational-databases/databases/delete-data-or-log-files-from-a-database#Prerequisites)。 `ALTER DATABASE REMOVE FILE` ステートメントを使用して空でないデータ ファイルを削除しようとすると、エラー `Msg 5042 – The file '<file_name>' cannot be removed because it is not empty` はすぐには返されません。 SQL Managed Instance では引き続きファイルの削除が試行されますが、操作は 30 分後に `Internal server error` で失敗します。

**回避策**:`DBCC SHRINKFILE (N'<file_name>', EMPTYFILE)` コマンドを使用して、ファイルの内容を削除します。 ファイル グループ内の唯一のファイルの場合は、ファイルを圧縮する前に、このファイル グループに関連付けられているテーブルまたはパーティションからデータを削除する必要があります。また、必要に応じて、このデータを別のテーブルまたはパーティションに読み込みます。

### <a name="change-service-tier-and-create-instance-operations-are-blocked-by-ongoing-database-restore"></a>サービス レベルの変更とインスタンスの作成操作が、進行中のデータベースの復元によってブロックされる

進行中の `RESTORE` ステートメント、データ移行サービスの移行プロセス、組み込みのポイントインタイム リストアでは、復元プロセスが完了するまで、サービス レベルの更新や既存のインスタンスのサイズ変更、新しいインスタンスの作成がブロックされます。 

復元プロセスでは、復元プロセスが実行されているのと同じサブネット内のマネージド インスタンスとインスタンス プールでこれらの操作がブロックされます。 インスタンス プール内のインスタンスは影響を受けません。 サービス レベルの作成または変更操作は失敗したり、タイムアウトになったりしません。復元プロセスが完了するかキャンセルされると、処理が続行されます。

**回避策**:サービス レベルの作成または更新操作の優先順位が高い場合は、復元プロセスが完了するか、復元プロセスがキャンセルされるまで待機します。

### <a name="resource-governor-on-business-critical-service-tier-might-need-to-be-reconfigured-after-failover"></a>Business Critical サービス レベルの Resource Governor をフェールオーバー後に再構成しなければならない場合がある

ユーザー ワークロードに割り当てられるリソースを制限することを可能にする [Resource Governor](/sql/relational-databases/resource-governor/resource-governor) 機能では、フェールオーバー後、またはユーザーが開始したサービス レベルの変更 (たとえば、最大仮想コア数やインスタンスの最大ストレージ サイズの変更) 後に、一部のユーザー ワークロードが誤って分類されることがあります。

**回避策**:[Resource Governor](/sql/relational-databases/resource-governor/resource-governor) を使用している場合、`ALTER RESOURCE GOVERNOR RECONFIGURE` を定期的に、または、インスタンスの開始時に SQL タスクを実行する SQL Agent ジョブの一環として実行します。

### <a name="cross-database-service-broker-dialogs-must-be-reinitialized-after-service-tier-upgrade"></a>サービス レベルのアップグレード後は、複数データベースにまたがる Service Broker のダイアログを再初期化する必要がある

サービス レベルの変更操作の後、複数データベースにまたがる Service Broker のダイアログで、他のデータベースのサービスにメッセージが配信されなくなります。 メッセージは "*失われたわけではなく*"、センダーのキューに存在しています。 SQL Managed Instance の仮想コア数やインスタンスのストレージ サイズを変更すると、すべてのデータベースについて、[sys.databases](/sql/relational-databases/system-catalog-views/sys-databases-transact-sql) ビューの `service_broke_guid` 値が変更されます。 [BEGIN DIALOG](/sql/t-sql/statements/begin-dialog-conversation-transact-sql) ステートメントを使用して作成された、他のデータベースの Service Broker を参照する `DIALOG` から、ターゲット サービスにメッセージが配信されなくなります。

**回避策**:複数データベースにまたがる Service Broker ダイアログのメッセージ交換を使用するアクティビティはすべて、サービス レベルを更新する前に停止し、後で再初期化してください。 サービス レベルの変更後、未配信のままのメッセージがあった場合は、それらのメッセージをソース キューから読み取って、ターゲット キューに再送信します。

### <a name="impersonation-of-azure-ad-login-types-is-not-supported"></a>Azure AD ログイン タイプの偽装がサポートされない

次の Azure Active Directory (Azure AD) プリンシパルの、`EXECUTE AS USER` または `EXECUTE AS LOGIN` を使用した偽装はサポートされていません。
-   別名が付けられた Azure AD ユーザー。 この場合は `15517` エラーが返されます。
- Azure AD アプリケーションまたはサービス プリンシパルに基づく Azure AD のログインおよびユーザー。 この場合は `15517` および `15406` エラーが返されます。

### <a name="query-parameter-not-supported-in-sp_send_db_mail"></a>sp_send_db_mail の @query パラメーターはサポートされない

[sp_send_db_mail](/sql/relational-databases/system-stored-procedures/sp-send-dbmail-transact-sql) プロシージャの `@query` パラメーターは機能しません。

### <a name="transactional-replication-must-be-reconfigured-after-geo-failover"></a>geo フェールオーバー後、トランザクション レプリケーションを再構成する必要がある

自動フェールオーバー グループ内のデータベースでトランザクション レプリケーションが有効な場合、SQL Managed Instance 管理者は、別のリージョンへのフェールオーバーが発生した後で、古いプライマリ上のすべてのパブリケーションをクリーンアップしてから、新しいプライマリ上でそれらを再構成する必要があります。 詳細については、「[レプリケーション](../managed-instance/transact-sql-tsql-differences-sql-server.md#replication)」をご覧ください。

### <a name="azure-ad-logins-and-users-are-not-supported-in-ssdt"></a>SSDT 内で Azure AD のログインとユーザーがサポートされない

SQL Server Data Tools では、Azure AD のログインとユーザーが完全にはサポートされません。

### <a name="temporary-database-is-used-during-restore-operation"></a>一時的なデータベースが RESTORE 操作中に使用される

SQL Managed Instance でデータベースが復元されるとき、復元サービスではまず、目的の名前で空のデータベースが作成され、インスタンス上でその名前が割り当てられます。 しばらくすると、このデータベースは削除され、実際のデータベースの復元が開始されます。 

"*復元中*" 状態のデータベースには、名前ではなくランダムな GUID 値が一時的に与えられます。 復元プロセスが終了すると、一時的な名前は、`RESTORE` ステートメントで指定した目的の名前に変更されます。 

初期フェーズでは、ユーザーは空のデータベースにアクセスしたり、さらにはこのデータベースにテーブルを作成したり、データを読み込んだりできます。 この一時的なデータベースは、復元サービスで 2 つ目のフェーズが開始されると削除されます。

**回避策**:復元の完了を確認するまで、復元中のデータベースにはアクセスしないでください。

### <a name="tempdb-structure-and-content-is-re-created"></a>TEMPDB の構造と内容は再作成される

`tempdb` は常に 12 個のデータ ファイルに分割され、ファイルの構造は変更できません。 ファイルあたりの最大サイズは変更できず、`tempdb` に新しいファイルを追加することはできません。 `Tempdb` は、インスタンスが開始またはフェールオーバーしたときに、常に空のデータベースとして再作成され、`tempdb` で行われたどの変更も保持されません。

### <a name="exceeding-storage-space-with-small-database-files"></a>小さなデータベース ファイルによる記憶域の超過

インスタンスが Azure ストレージの制限に達する可能性があるため、`CREATE DATABASE`、`ALTER DATABASE ADD FILE`、および `RESTORE DATABASE` ステートメントが失敗することがあります。

SQL Managed Instance の各 General Purpose インスタンスには、Azure Premium ディスク領域用に予約された最大 35 TB のストレージがあります。 各データベース ファイルは、個別の物理ディスクに配置されます。 ディスク サイズとして、128 GB、256 GB、512 GB、1 TB、4 TB のいずれかを指定できます。 ディスク上の未使用領域については請求されませんが、Azure Premium ディスクのサイズ合計が 35 TB を超えることはできません。 場合によっては、内部の断片化のために、合計で 8 TB を必要としない Managed Instance が、記憶域のサイズに関する 35 TB の Azure での制限を超える場合があります。

たとえば、SQL Managed Instance の General Purpose インスタンスに、4 TB のディスクに配置されているサイズが 1.2 TB の大きなファイルが 1 つあるとします。 また、個別の 128 GB のディスクに配置される、それぞれ 1 GB のファイルが 248 個あるとします。 次の点に注意してください。

- 割り当てられるディスク ストレージの合計サイズは、1 x 4 TB + 248 x 128 GB = 35 TB となります。
- インスタンス上のデータベースの予約済み領域の合計は、1 x 1.2 TB + 248 x 1 GB = 1.4 TB となります。

この例は、特定の状況下では特殊なファイルの配分が原因で、SQL Managed Instance のインスタンスが、想定していないときにアタッチ済み Azure Premium ディスク用に予約されている 35 TB の制限に到達する可能性があることを示しています。

この例では既存のデータベースは引き続き機能し、新しいファイルを追加しない限りは問題なく拡張できます。 すべてのデータベースの合計サイズがインスタンス サイズの上限に到達しない場合でも、新しいディスク ドライブ用の十分な領域がないため、新しいデータベースの作成や復元はできません。 その場合に返されるエラーは明確ではありません。

システム ビューを使用して、[残りのファイルの数を特定](https://medium.com/azure-sqldb-managed-instance/how-many-files-you-can-create-in-general-purpose-azure-sql-managed-instance-e1c7c32886c1)できます。 この制限に達した場合は、[DBCC SHRINKFILE ステートメントを使用して、より小さなファイルをいくつか空にして削除](/sql/t-sql/database-console-commands/dbcc-shrinkfile-transact-sql#d-emptying-a-file)してみるか、[この制限のない Business Critical レベル](../managed-instance/resource-limits.md#service-tier-characteristics)に切り替えてください。

### <a name="guid-values-shown-instead-of-database-names"></a>データベース名の代わりに GUID 値が表示される

複数のシステム ビュー、パフォーマンス カウンター、エラー メッセージ、XEvent、およびエラー ログ エントリで、実際のデータベース名ではなく GUID データベース識別子が表示されています。 将来、実際のデータベース名に置き換えられるため、これらの GUID 識別子には依存しないでください。

**回避策**:sys.databases ビューを使用して、実際のデータベース名を、GUID データベース識別子の形式で指定した物理データベース名から解決します。

```tsql
SELECT name as ActualDatabaseName, physical_database_name as GUIDDatabaseIdentifier 
FROM sys.databases
WHERE database_id > 4
```

### <a name="error-logs-arent-persisted"></a>エラー ログが非永続的である

SQL Managed Instance で利用可能なエラー ログは永続的ではなく、このログのサイズは、最大ストレージ上限には含まれません。 フェールオーバーが発生した場合、エラー ログは自動的に消去される可能性があります。 SQL Managed Instance が複数の仮想マシンで複数回移動されたことが原因で、エラー ログの履歴に欠落部分が生じる可能性があります。

### <a name="transaction-scope-on-two-databases-within-the-same-instance-isnt-supported"></a>同じインスタンス内にある 2 つのデータベース上でトランザクション スコープがサポートされない

**(2020 年 3 月に解決済み)** `TransactionScope` クラス (.NET) は、同じトランザクション スコープ下では、同一インスタンス内にある 2 つのデータベースに対して 2 つのクエリが送信された場合に機能しません。

```csharp
using (var scope = new TransactionScope())
{
    using (var conn1 = new SqlConnection("Server=quickstartbmi.neu15011648751ff.database.windows.net;Database=b;User ID=myuser;Password=mypassword;Encrypt=true"))
    {
        conn1.Open();
        SqlCommand cmd1 = conn1.CreateCommand();
        cmd1.CommandText = string.Format("insert into T1 values(1)");
        cmd1.ExecuteNonQuery();
    }

    using (var conn2 = new SqlConnection("Server=quickstartbmi.neu15011648751ff.database.windows.net;Database=b;User ID=myuser;Password=mypassword;Encrypt=true"))
    {
        conn2.Open();
        var cmd2 = conn2.CreateCommand();
        cmd2.CommandText = string.Format("insert into b.dbo.T2 values(2)");        cmd2.ExecuteNonQuery();
    }

    scope.Complete();
}

```

**回避策 (2020 年 3 月以降は不要)** :2 つの接続を使用する代わりに [SqlConnection.ChangeDatabase(String)](/dotnet/api/system.data.sqlclient.sqlconnection.changedatabase) を使って、接続コンテキスト内で他のデータベースを使用します。

### <a name="clr-modules-and-linked-servers-sometimes-cant-reference-a-local-ip-address"></a>CLR モジュールとリンク サーバーでローカル IP アドレスを参照できないことがある

SQL Managed Instance 内の CLR モジュールと、現在のインスタンスを参照しているリンク サーバーまたは分散クエリでは、ローカル インスタンスの IP を解決できないことがあります。 このエラーは一時的な問題です。

**回避策**:可能であれば、CLR モジュールでコンテキスト接続を使用します。

## <a name="updates"></a>更新プログラム

SQL Database の更新情報や機能強化の一覧については、[SQL Database サービスの更新情報](https://azure.microsoft.com/updates/?product=sql-database)を参照してください。

すべての Azure サービスの更新情報や機能強化については、[サービスの更新情報](https://azure.microsoft.com/updates)を参照してください。

## <a name="contribute-to-content"></a>コンテンツの改善への協力

Azure SQL のドキュメントに投稿するには、[Docs 共同作成者ガイド](/contribute/)に関する記事をご覧ください。
