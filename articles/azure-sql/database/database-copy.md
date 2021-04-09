---
title: データベースをコピーする
description: トランザクション上一貫性のある Azure SQL Database の既存のデータベースコピーを、同じサーバーまたは別のサーバーのいずれかに作成します。
services: sql-database
ms.service: sql-database
ms.subservice: data-movement
ms.custom: sqldbrb=1, devx-track-azurecli
ms.devlang: ''
ms.topic: how-to
author: stevestein
ms.author: sashan
ms.reviewer: wiassaf
ms.date: 03/10/2021
ms.openlocfilehash: 1a86522975ffb7b5b2bd514402dd97a76aa2506e
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "103014606"
---
# <a name="copy-a-transactionally-consistent-copy-of-a-database-in-azure-sql-database"></a>トランザクション上一貫性のある Azure SQL Database のデータベースのコピーを作成する

[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

Azure SQL Database では、同じサーバーまたは別のサーバーのいずれかに、既存の[データベース](single-database-overview.md)のコピーを作成するための複数の方法が用意されています。 Azure Portal、PowerShell、Azure CLI、または T-SQL を使って、データベースをコピーできます。

## <a name="overview"></a>概要

データベースのコピーは、コピー要求が開始された時点でのソース データベースのトランザクション上一貫性のあるスナップショットになります。 コピーには、同じサーバーまたは別のサーバーを選択できます。 また、ソース データベースのバックアップの冗長性、サービス レベル、およびコンピューティング サイズを維持するか、同じまたは異なるサービス レベル内で別のバックアップ ストレージの冗長性とコンピューティング サイズ、またはそのいずれかを使用することもできます。 コピーの完了後、コピーは完全に機能する独立したデータベースになります。 コピーしたデータベースのログイン、ユーザー、およびアクセス許可は、ソース データベースとは別に管理されます。 コピーは geo レプリケーション テクノロジを使用して作成されます。 レプリカのシード処理が完了すると、geo レプリケーション リンクは自動的に終了します。 geo レプリケーションを使用するためのすべての要件が、データベースのコピー操作に適用されます。 詳細については、「[アクティブ geo レプリケーションの作成と使用](active-geo-replication-overview.md)」を参照してください。

> [!NOTE]
> Azure SQL Database の [Configurable Backup Storage Redundancy]\(構成可能なバックアップ ストレージの冗長性\) は、ブラジル南部ではパブリック プレビューとして利用でき、一般公開されているのは東南アジアの Azure リージョンのみです。 プレビューでは、ソース データベースがローカル冗長またはゾーン冗長のバックアップ ストレージの冗長性を使用して作成されている場合、別の Azure リージョンのサーバーにデータベースをコピーすることはできません。 

## <a name="logins-in-the-database-copy"></a>データベースのコピーへのログイン

データベースを同じサーバーにコピーすると、両方のデータベースで同じログインを利用できます。 データベースをコピーするために使うセキュリティ プリンシパルが、新しいデータベースのデータベース所有者になります。

データベースを別のサーバーにコピーすると、ターゲット サーバーでコピー操作を開始したセキュリティ プリンシパルが、新しいデータベースの所有者になります。

ターゲットサーバーに関係なく、すべてのデータベース ユーザー、そのアクセス許可、およびそれらのセキュリティ識別子 (SID) がデータベースのコピーにコピーされます。 データ アクセスに [包含データベース ユーザー](logins-create-manage.md) を使用すると、コピーしたデータベースのユーザー資格情報が同じであることが確認されます。これにより、コピーの完了後すぐに同じ資格情報でアクセスできるようになります。

データ アクセスにサーバー レベルのログインを使用し、別のサーバーにデータベースをコピーする場合、ログイン ベースのアクセスが機能しない可能性があります。 これは、ログインがターゲット サーバーに存在しないか、パスワードとセキュリティ識別子 (SID) が異なるために発生する場合があります。 別のサーバーにデータベースをコピーする場合のログイン管理の詳細については、[ディザスター リカバリー後に Azure SQL Database のセキュリティを管理する方法](active-geo-replication-security-configure.md)に関するページをご覧ください。 別のサーバーへのコピー操作が成功した後、他のユーザーが再マップされる前に、データベース所有者に関連付けられているログインのみ、またはサーバー管理者がコピーしたデータベースにログインできます。 コピー操作の完了後にログインを解決し、データ アクセスを確立する方法については、「[ログインの解決](#resolve-logins)」をご参照ください。

## <a name="copy-using-the-azure-portal"></a>Azure Portal を使用したコピー

Azure Portal を使ってデータベースをコピーするには、データベースのページを開き、 **[コピー]** をクリックします。

   ![データベースのコピー](./media/database-copy/database-copy.png)

## <a name="copy-using-powershell-or-the-azure-cli"></a>PowerShell または Azure CLI を使用したコピー

データベースをコピーするには、次の例を使用します。

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

PowerShell の場合、[New-AzSqlDatabaseCopy](/powershell/module/az.sql/new-azsqldatabasecopy) コマンドレットを使用します。

> [!IMPORTANT]
> PowerShell Azure Resource Manager (RM) モジュールは Azure SQL Database で引き続きサポートされますが、今後の開発はすべて Az.Sql モジュールを対象に行われます。 AzureRM モジュールのバグ修正は、少なくとも 2020 年 12 月までは引き続き受け取ることができます。  Az モジュールと AzureRm モジュールのコマンドの引数は実質的に同じです。 その互換性の詳細については、「[新しい Azure PowerShell Az モジュールの概要](/powershell/azure/new-azureps-module-az)」を参照してください。

```powershell
New-AzSqlDatabaseCopy -ResourceGroupName "<resourceGroup>" -ServerName $sourceserver -DatabaseName "<databaseName>" `
    -CopyResourceGroupName "myResourceGroup" -CopyServerName $targetserver -CopyDatabaseName "CopyOfMySampleDatabase"
```

データベースのコピーは非同期操作ですが、要求が受け入れられた直後にターゲット データベースが作成されます。 まだ進行中のコピー操作を取り消す必要がある場合は、[Remove-AzSqlDatabase](/powershell/module/az.sql/new-azsqldatabase) コマンドレットを使用してターゲット データベースをドロップします。

完全な PowerShell のサンプル スクリプトについては、「[新しいサーバーにデータベースをコピーする](scripts/copy-database-to-new-server-powershell.md)」をご覧ください。

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli
az sql db copy --dest-name "CopyOfMySampleDatabase" --dest-resource-group "myResourceGroup" --dest-server $targetserver `
    --name "<databaseName>" --resource-group "<resourceGroup>" --server $sourceserver
```

データベースのコピーは非同期操作ですが、要求が受け入れられた直後にターゲット データベースが作成されます。 まだ進行中のコピー操作を取り消す必要がある場合は、[az sql db delete](/cli/azure/sql/db#az-sql-db-delete) コマンドを使用してターゲット データベースをドロップします。

* * *

## <a name="copy-using-transact-sql"></a>Transact-SQL を使用したコピー

サーバー管理者のログイン、またはコピーするデータベースを作成したログインを使用して、マスター データベースにログインします。 データベースのコピーを成功させるには、サーバー管理者ではないログインが `dbmanager` ロールのメンバーによるものである必要があります。 ログインおよびサーバーへの接続の詳細については、 [ログインの管理](logins-create-manage.md)に関するページを参照してください。

[CREATE DATABASE AS COPY OF ...](/sql/t-sql/statements/create-database-transact-sql?view=azuresqldb-current&preserve-view=true#copy-a-database) ステートメントを使用して、ソース データベースのコピーを開始します。 T-SQL ステートメントは、データベースのコピー操作が完了するまで実行を続けます。

> [!NOTE]
> T-SQL ステートメントを終了しても、データベース コピー操作は終了しません。 操作を終了するには、ターゲット データベースを削除します。
>

> [!IMPORTANT]
> T-SQL CREATE DATABASE ...AS COPY OF コマンドを使用する際に、バックアップ ストレージの冗長性を選択することはできません。 

### <a name="copy-to-the-same-server"></a>同じサーバーにコピーする

サーバー管理者のログイン、またはコピーするデータベースを作成したログインを使用して、マスター データベースにログインします。 データベースのコピーを成功させるには、サーバー管理者ではないログインが `dbmanager` ロールのメンバーによるものである必要があります。

このコマンドは、Database1 を、同じサーバー上の Database2 という名前の新しいデータベースにコピーします。 データベースのサイズに応じて、コピー操作の完了に時間がかかる場合があります。

   ```sql
   -- Execute on the master database to start copying
   CREATE DATABASE Database2 AS COPY OF Database1;
   ```

### <a name="copy-to-an-elastic-pool"></a>エラスティック プールにコピーする

サーバー管理者のログイン、またはコピーするデータベースを作成したログインを使用して、マスター データベースにログインします。 データベースのコピーを成功させるには、サーバー管理者ではないログインが `dbmanager` ロールのメンバーによるものである必要があります。

このコマンドは、Database1 を、pool1 という名前のエラスティック プール内の Database2 という名前の新しいデータベースにコピーします。 データベースのサイズに応じて、コピー操作の完了に時間がかかる場合があります。

Database1 には、単一データベースまたはプールされたデータベースを指定できます。 異なる層のプール間のコピーはサポートされていますが、一部の層間のコピーは成功しません。 たとえば、単一またはエラスティックの Standard データベースは汎用プールにコピーできますが、エラスティックの Standard データベースを Premium プールにコピーすることはできません。 

   ```sql
   -- Execute on the master database to start copying
   CREATE DATABASE "Database2"
   AS COPY OF "Database1"
   (SERVICE_OBJECTIVE = ELASTIC_POOL( name = "pool1" ) );
   ```

### <a name="copy-to-a-different-server"></a>別のサーバーへのコピー

新しいデータベースを作成するターゲット サーバーのマスター データベースにログインします。 コピー元の サーバーのコピー元データベースのデータベース所有者と同じ名前とパスワードを持つログインを使用します。 また、ターゲット サーバーでのログインは、`dbmanager` ロールのメンバーであるか、またはサーバー管理者のログインである必要があります。

このコマンドは、server1 上の Database1 を、server2 上の Database2 という新しいデータベースにコピーします。 データベースのサイズに応じて、コピー操作の完了に時間がかかる場合があります。

```sql
-- Execute on the master database of the target server (server2) to start copying from Server1 to Server2
CREATE DATABASE Database2 AS COPY OF server1.Database1;
```

> [!IMPORTANT]
> 両方のサーバーのファイアウォールは、T-SQL CREATE DATABASE ...AS COPY OF コマンドを発行するクライアントの IP からの受信接続を許可するように構成する必要があります。

### <a name="copy-to-a-different-subscription"></a>別のサブスクリプションへのコピー

「[SQL データベースを別のサーバーにコピーする](#copy-to-a-different-server)」セクションの手順を使用して、T-SQL を使用して別のサブスクリプションでサーバーにデータベースをコピーできます。 ソース データベースのデータベース所有者と同じ名前とパスワードを持つログインを使用していることを確認します。 また、ログインは、ソース サーバーとターゲット サーバーの両方で、`dbmanager` ロールまたはサーバー管理者のメンバーである必要があります。

```sql
--Step# 1
--Create login and user in the master database of the source server.

CREATE LOGIN loginname WITH PASSWORD = 'xxxxxxxxx'
GO
CREATE USER [loginname] FOR LOGIN [loginname] WITH DEFAULT_SCHEMA=[dbo];
GO
ALTER ROLE dbmanager ADD MEMBER loginname;
GO

--Step# 2
--Create the user in the source database and grant dbowner permission to the database.

CREATE USER [loginname] FOR LOGIN [loginname] WITH DEFAULT_SCHEMA=[dbo];
GO
ALTER ROLE db_owner ADD MEMBER loginname;
GO

--Step# 3
--Capture the SID of the user "loginname" from master database

SELECT [sid] FROM sysusers WHERE [name] = 'loginname';

--Step# 4
--Connect to Destination server.
--Create login and user in the master database, same as of the source server.

CREATE LOGIN loginname WITH PASSWORD = 'xxxxxxxxx', SID = [SID of loginname login on source server];
GO
CREATE USER [loginname] FOR LOGIN [loginname] WITH DEFAULT_SCHEMA=[dbo];
GO
ALTER ROLE dbmanager ADD MEMBER loginname;
GO

--Step# 5
--Execute the copy of database script from the destination server using the credentials created

CREATE DATABASE new_database_name
AS COPY OF source_server_name.source_database_name;
```

> [!NOTE]
> [Azure portal](https://portal.azure.com)、PowerShell、および Azure CLI は、別のサブスクリプションへのデータベースのコピーをサポートしていません。

> [!TIP]
> T-SQL を使用するデータベースのコピーでは、異なる Azure テナント内のサブスクリプションからのデータベースのコピーがサポートされています。 これは、SQL 認証ログインを使用して対象サーバーにログインする場合にのみサポートされます。

## <a name="monitor-the-progress-of-the-copying-operation"></a>コピー操作の進行状況を監視する

[の](/sql/relational-databases/system-catalog-views/sys-databases-transact-sql) に対してクエリを実行してコピー処理を監視し、[ sys. dm_database_copies](/sql/relational-databases/system-dynamic-management-views/sys-dm-database-copies-azure-sql-database)、[dm_operation_status](/sql/relational-databases/system-dynamic-management-views/sys-dm-operation-status-azure-sql-database) を表示します。 コピー操作の進行中は、新しいデータベースの sys.databases ビューの **state_desc** 列が **COPYING** に設定されます。

* コピー操作が失敗すると、新しいデータベースの sys.databases ビューの **state_desc** 列が **SUSPECT** に設定されます。 新しいデータベースに対して DROP ステートメントを実行した後でもう一度やり直してください。
* コピー操作が成功すると、新しいデータベースの sys.databases ビューの **state_desc** 列が **ONLINE** に設定されます。 コピー操作は完了しています。新しいデータベースは通常のデータベースであり、コピー元データベースから独立して変更することができます。

> [!NOTE]
> 進行中のコピー操作を取り消すには、新しいデータベースに対して [DROP DATABASE](/sql/t-sql/statements/drop-database-transact-sql) ステートメントを実行します。

> [!IMPORTANT]
> ソースよりも非常に小さいサービス目標を使用してコピーを作成する必要がある場合、ターゲット データベースには、シード処理を完了するための十分なリソースがない可能性があります。これにより、コピー操作が失敗する可能性があります。 このシナリオでは、geo リストア要求を使用して、別のサーバーや別のリージョンにコピーを作成します。 詳細については、[データベースのバックアップを使用した Azure SQL Database の復旧](recovery-using-backups.md#geo-restore)に関するページを参照してください。

## <a name="azure-rbac-roles-and-permissions-to-manage-database-copy"></a>データベース コピーを管理するための Azure RBAC のロールとアクセス許可

データベースのコピーを作成するには、次のロールが必要です

* サブスクリプションの所有者または
* SQL Server 共同作成者ロールまたは
* 次のアクセス許可を持つ、ソース データベースとターゲット データベースのカスタム ロール:

   Microsoft.Sql/servers/databases/read  Microsoft.Sql/servers/databases/write

データベースのコピーを取り消すには、次のロールが必要です

* サブスクリプションの所有者または
* SQL Server 共同作成者ロールまたは
* 次のアクセス許可を持つ、ソース データベースとターゲット データベースのカスタム ロール:

   Microsoft.Sql/servers/databases/read  Microsoft.Sql/servers/databases/write

Azure portal を使用してデータベースのコピーを管理するには、以下のアクセス許可も必要です。

   Microsoft.Resources/subscriptions/resources/read Microsoft.Resources/subscriptions/resources/write Microsoft.Resources/deployments/read Microsoft.Resources/deployments/write Microsoft.Resources/deployments/operationstatuses/read

ポータルでリソース グループのデプロイの下の操作と、SQL 操作を含む、複数のリソースプロバイダーにまたがる操作を表示するには、以下の追加の Azure ロールが必要です。

   Microsoft.Resources/subscriptions/resourcegroups/deployments/operations/read Microsoft.Resources/subscriptions/resourcegroups/deployments/operationstatuses/read

## <a name="resolve-logins"></a>ログインの解決

新しいデータベースがターゲット サーバーでオンラインになった後、 [ALTER USER](/sql/t-sql/statements/alter-user-transact-sql?view=azuresqldb-current&preserve-view=true) ステートメントを使用して、ユーザーを新しいデータベースからターゲット サーバーのログインに再マップします。 孤立したユーザーを解決するには、「 [孤立ユーザーのトラブルシューティング](/sql/sql-server/failover-clusters/troubleshoot-orphaned-users-sql-server)」をご覧ください。 [ディザスター リカバリー後に Azure SQL Database のセキュリティを管理する方法](active-geo-replication-security-configure.md)に関するページもご覧ください。

新しいデータベースのすべてのユーザーのアクセス許可は、コピー元データベースで保持していたものと同じです。 データベースのコピーを開始したユーザーが、新しいデータベースのデータベース所有者になります。 コピーが成功した後、他のユーザーが再マップされる前に、データベース所有者のみが新しいデータベースにログインできます。

別のサーバーにデータベースをコピーする場合のユーザーおよびログインの管理の詳細については、[ディザスター リカバリー後に Azure SQL Database のセキュリティを管理する方法](active-geo-replication-security-configure.md)に関するページをご覧ください。

## <a name="database-copy-errors"></a>データベース コピー エラー

次のエラーは、Azure SQL Database でデータベースをコピーしているときに発生する可能性があります。 詳細については、「 [Azure SQL Database のコピー](database-copy.md)」を参照してください。

| エラー コード | 重大度 | 説明 |
| ---:| ---:|:--- |
| 40635 |16 |IP アドレス '%.&#x2a;ls' のクライアントは一時的に無効化されています。 |
| 40637 |16 |データベース コピーの作成は現在無効です。 |
| 40561 |16 |データベースのコピーに失敗しました。 ソースまたはターゲットのデータベースが存在しません。 |
| 40562 |16 |データベースのコピーに失敗しました。 ソース データベースは削除されました。 |
| 40563 |16 |データベースのコピーに失敗しました。 ターゲット データベースは削除されました。 |
| 40564 |16 |データベースのコピーに失敗しました。内部エラーが発生しました。 ターゲット データベースを削除してやり直してください。 |
| 40565 |16 |データベースのコピーに失敗しました。 同じソースから複数のデータベース コピーを同時に実行することはできません。 ターゲット データベースを削除してやり直してください。 |
| 40566 |16 |データベースのコピーに失敗しました。内部エラーが発生しました。 ターゲット データベースを削除してやり直してください。 |
| 40567 |16 |データベースのコピーに失敗しました。内部エラーが発生しました。 ターゲット データベースを削除してやり直してください。 |
| 40568 |16 |データベースのコピーに失敗しました。 ソース データベースが使用できなくなりました。 ターゲット データベースを削除してやり直してください。 |
| 40569 |16 |データベースのコピーに失敗しました。 ターゲット データベースが使用できなくなりました。 ターゲット データベースを削除してやり直してください。 |
| 40570 |16 |データベースのコピーに失敗しました。内部エラーが発生しました。 ターゲット データベースを削除してやり直してください。 |
| 40571 |16 |データベースのコピーに失敗しました。内部エラーが発生しました。 ターゲット データベースを削除してやり直してください。 |

## <a name="next-steps"></a>次のステップ

* ログインについては、[ログインの管理](logins-create-manage.md)に関するページおよび[ディザスター リカバリー後に Azure SQL Database のセキュリティを管理する方法](active-geo-replication-security-configure.md)に関するページをご覧ください。
* データベースをエクスポートするには、「[BACPAC へのデータベースのエクスポート](database-export.md)」をご覧ください。
