---
title: BACPAC ファイルをインポートして Azure SQL データベースを作成する | Microsoft Docs
description: BACPAC ファイルをインポートして新しい Azure SQL Database を作成します。
services: sql-database
ms.service: sql-database
ms.subservice: migration
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: ''
manager: craigg
ms.date: 06/20/2019
ms.openlocfilehash: 0b92fb9c9bf022adce4cc0dd3e58ce8e476ed5b7
ms.sourcegitcommit: 82efacfaffbb051ab6dc73d9fe78c74f96f549c2
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/20/2019
ms.locfileid: "67303508"
---
# <a name="quickstart-import-a-bacpac-file-to-a-database-in-azure-sql-database"></a>クイック スタート:BACPAC ファイルを Azure SQL Database 内のデータベースにインポートする

[BACPAC](https://docs.microsoft.com/sql/relational-databases/data-tier-applications/data-tier-applications#bacpac) ファイルを使用して、Azure SQL Database 内のデータベースに SQL Server データベースをインポートできます。 Azure Blob Storage (Standard ストレージのみ) 内に格納された `BACPAC` ファイルから、またはオンプレミスの場所にあるローカル ストレージから、データをインポートできます。 より高速なリソースを多く提供することでインポート速度を最大化するには、インポート プロセスの中でデータベースをより高いサービス層とコンピューティング サイズにスケーリングします。 インポートが正常に完了した後でスケールダウンすることができます。

> [!NOTE]
> インポートされたデータベースの互換性レベルは、ソース データベースの互換性レベルに基づきます。
> [!IMPORTANT]
> データベースをデータベースに移行した後に、データベースを現在の互換性レベル (AdventureWorks2008R2 データベースの場合はレベル 100) またはそれ以上のレベルで運用できます。 データベースを指定の互換性レベルで運用する影響とオプションについて詳しくは、[ALTER DATABASE の互換性レベル](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql-compatibility-level)に関する記事をご覧ください。 また、互換性レベルに関連する追加のデータベースレベルの設定について詳しくは、[ALTER DATABASE SCOPED CONFIGURATION](https://docs.microsoft.com/sql/t-sql/statements/alter-database-scoped-configuration-transact-sql) に関する記事をご覧ください。

## <a name="import-from-a-bacpac-file-in-the-azure-portal"></a>Azure portal で BACPAC ファイルからインポートする

[Azure portal](https://portal.azure.com) では、Azure SQL Database 内の単一データベースの作成 "*だけ*" が、Azure Blob Storage に格納されている BACPAC ファイルから "*のみ*" サポートされています。

> [!NOTE]
> [マネージド インスタンス](sql-database-managed-instance.md)では現在、Azure portal を使用した BACPAC ファイルからインスタンス データベースへのデータベースの移行はサポートされていません。 マネージド インスタンスにインポートするには、SQL Server Management Studio または SQLPackage を使用します。

> [!NOTE]
> portal または Powershell から送信されたインポート/エクスポート要求を処理するマシンは、bacpac ファイルとデータ層アプリケーション フレームワーク (DacFX) によって生成された一時ファイルを格納する必要があります。 必要なディスク領域は、同じサイズの DB でも大きく異なり、最大 3 倍のデータベースのサイズが必要になります。 インポート/エクスポート要求のみを実行するマシンには、450 GB のローカル ディスク領域があります。 結果として、一部の要求が「十分なディスク領域が存在しない」のエラーで失敗する場合があります。 この場合の回避策は、十分なローカル ディスク領域を持つマシンで sqlpackage.exe を実行することです。 150 GB を超えるデータベースをインポート/エクスポートする場合は、[SqlPackage](#import-from-a-bacpac-file-using-sqlpackage) を使用してこの問題を回避します。
 
1. Azure portal を使用して新しい単一データベースに BACPAC ファイルからインポートするには、適切なデータベース サーバーのページを開き、 **[データベースのインポート]** を選択します。  

   ![データベース インポート 1](./media/sql-database-import/import1.png)

2. ストレージ アカウントと BACPAC ファイルのコンテナーを選択してから、インポート元の BACPAC ファイルを選択します。
3. 新しいデータベースのサイズ (通常、元のサイズと同じ) を指定し、インポート先の SQL Server 資格情報を指定します。 新しい Azure SQL データベースに使用できる値の一覧については、[データベースの作成](https://docs.microsoft.com/sql/t-sql/statements/create-database-transact-sql?view=azuresqldb-current)に関するページをご覧ください。

   ![データベース インポート 2](./media/sql-database-import/import2.png)

4. Click **OK**.

5. インポートの進行状況を監視するには、データベースのサーバー ページを開き、 **[設定]** の下にある **[インポート/エクスポート履歴]** を選択します。 成功すると、インポートは**完了**状態になります。

   ![データベース インポートの状態](./media/sql-database-import/import-status.png)

6. データベースがデータベース サーバー上で稼働していることを確認するには、 **[SQL データベース]** を選択し、新しいデータベースが **[オンライン]** であることを確認します。

## <a name="import-from-a-bacpac-file-using-sqlpackage"></a>SqlPackage を使用して BACPAC ファイルからインポートする

[SqlPackage](https://docs.microsoft.com/sql/tools/sqlpackage) コマンドライン ユーティリティを使用して SQL Server データベースをインポートするには、「[インポート パラメーターとプロパティ](https://docs.microsoft.com/sql/tools/sqlpackage#import-parameters-and-properties)」をご覧ください。 SqlPackage には、最新の [SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms) と [SQL Server Data Tools for Visual Studio](https://msdn.microsoft.com/library/mt204009.aspx) が含まれています。 最新の [SqlPackage](https://www.microsoft.com/download/details.aspx?id=53876) は、Microsoft ダウンロード センターからダウンロードすることもできます。

スケールとパフォーマンスのために、ほとんどの運用環境では、Azure portal の使用ではなく SqlPackage の使用をお勧めします。 `BACPAC` ファイルを使用した移行に関する SQL Server Customer Advisory Team のブログについては、「[Migrating from SQL Server to Azure SQL Database using BACPAC Files (BACPAC ファイルを使用した SQL Server から Azure SQL Database への移行)](https://blogs.msdn.microsoft.com/sqlcat/2016/10/20/migrating-from-sql-server-to-azure-sql-database-using-bacpac-files/)」をご覧ください。

スケールとパフォーマンスを考慮して、ほとんどの運用環境では SqlPackage の使用をお勧めします。 BACPAC ファイルを使用した移行に関する SQL Server Customer Advisory Team のブログについては、「[Migrating from SQL Server to Azure SQL Database using BACPAC Files (BACPAC ファイルを使用した SQL Server から Azure SQL Database への移行)](https://blogs.msdn.microsoft.com/sqlcat/20../../migrating-from-sql-server-to-azure-sql-database-using-bacpac-files/)」を参照してください。

次の SqlPackage コマンドにより、**AdventureWorks2008R2** データベースが、ローカル ストレージから **mynewserver20170403** という Azure SQL Database サーバーにインポートされます。 **Premium** サービス層と **P6** サービス オブジェクトがある **myMigratedDatabase** という新しいデータベースが作成されます。 これらの値は、お使いの環境に合わせて変更してください。

```cmd
SqlPackage.exe /a:import /tcs:"Data Source=mynewserver20170403.database.windows.net;Initial Catalog=myMigratedDatabase;User Id=<your_server_admin_account_user_id>;Password=<your_server_admin_account_password>" /sf:AdventureWorks2008R2.bacpac /p:DatabaseEdition=Premium /p:DatabaseServiceObjective=P6
```

> [!IMPORTANT]
> 企業のファイアウォールの外側から単一のデータベースを管理している SQL Database サーバーに接続するには、そのファイアウォールでポート 1433 が開かれている必要があります。 マネージド インスタンスに接続するには、[ポイント対サイト接続](sql-database-managed-instance-configure-p2s.md)または Express Route 接続が使用できる必要があります。
>

この例では、SqlPackage と Active Directory ユニバーサル認証を使用してデータベースをインポートする方法を示します。

```cmd
SqlPackage.exe /a:Import /sf:testExport.bacpac /tdn:NewDacFX /tsn:apptestserver.database.windows.net /ua:True /tid:"apptest.onmicrosoft.com"
```

## <a name="import-into-a-single-database-from-a-bacpac-file-using-powershell"></a>PowerShell を使用して BACPAC ファイルから単一データベースにインポートする

> [!NOTE]
> [マネージド インスタンス](sql-database-managed-instance.md)では現在、Azure PowerShell を使用した BACPAC ファイルからインスタンス データベースへのデータベースの移行はサポートされていません。 マネージド インスタンスにインポートするには、SQL Server Management Studio または SQLPackage を使用します。

> [!NOTE]
> portal または Powershell から送信されたインポート/エクスポート要求を処理するマシンは、bacpac ファイルとデータ層アプリケーション フレームワーク (DacFX) によって生成された一時ファイルを格納する必要があります。 必要なディスク領域は、同じサイズの DB でも大きく異なり、最大 3 倍のデータベースのサイズが必要になります。 インポート/エクスポート要求のみを実行するマシンには、450 GB のローカル ディスク領域があります。 結果として、一部の要求が「十分なディスク領域が存在しない」のエラーで失敗する場合があります。 この場合の回避策は、十分なローカル ディスク領域を持つマシンで sqlpackage.exe を実行することです。 150 GB を超えるデータベースをインポート/エクスポートする場合は、[SqlPackage](#import-from-a-bacpac-file-using-sqlpackage) を使用してこの問題を回避します。

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]
> [!IMPORTANT]
> PowerShell Azure Resource Manager モジュールは Azure SQL Database で引き続きサポートされますが、今後の開発はすべて Az.Sql モジュールを対象に行われます。 これらのコマンドレットについては、「[AzureRM.Sql](https://docs.microsoft.com/powershell/module/AzureRM.Sql/)」を参照してください。 Az モジュールと AzureRm モジュールのコマンドの引数は実質的に同じです。

[New-AzSqlDatabaseImport](/powershell/module/az.sql/new-azsqldatabaseimport) コマンドレットを使用して、Azure SQL Database サービスにデータベースのインポート要求を送信します。 データベースのサイズによって、インポートが完了するまでに時間がかかる場合があります。

 ```powershell
 $importRequest = New-AzSqlDatabaseImport 
    -ResourceGroupName "<your_resource_group>" `
    -ServerName "<your_server>" `
    -DatabaseName "<your_database>" `
    -DatabaseMaxSizeBytes "<database_size_in_bytes>" `
    -StorageKeyType "StorageAccessKey" `
    -StorageKey $(Get-AzStorageAccountKey -ResourceGroupName "<your_resource_group>" -StorageAccountName "<your_storage_account").Value[0] `
    -StorageUri "https://myStorageAccount.blob.core.windows.net/importsample/sample.bacpac" `
    -Edition "Standard" `
    -ServiceObjectiveName "P6" `
    -AdministratorLogin "<your_server_admin_account_user_id>" `
    -AdministratorLoginPassword $(ConvertTo-SecureString -String "<your_server_admin_account_password>" -AsPlainText -Force)

 ```

 インポートの進行状況を確認するには、[Get-AzSqlDatabaseImportExportStatus](/powershell/module/az.sql/get-azsqldatabaseimportexportstatus) コマンドレットを使用できます。 要求直後にこのコマンドレットを実行すると、通常は、 **Status :InProgress** が返されます。 **Status :Succeeded** が表示された場合、インポートは完了しています。

```powershell
$importStatus = Get-AzSqlDatabaseImportExportStatus -OperationStatusLink $importRequest.OperationStatusLink
[Console]::Write("Importing")
while ($importStatus.Status -eq "InProgress")
{
    $importStatus = Get-AzSqlDatabaseImportExportStatus -OperationStatusLink $importRequest.OperationStatusLink
    [Console]::Write(".")
    Start-Sleep -s 10
}
[Console]::WriteLine("")
$importStatus
```

> [!TIP]
> 別のスクリプト例については、「[データベースを BACPAC ファイルからインポートする](scripts/sql-database-import-from-bacpac-powershell.md)」を参照してください。

## <a name="limitations"></a>制限事項

エラスティック プール内のデータベースへのインポートはサポートされていません。 データを単一データベースにインポートしてから、そのデータベースをエラスティック プールに移動することはできます。

## <a name="import-using-wizards"></a>ウィザードを使用したインポート

次のウィザードも利用できます。

- [SQL Server Management Studio のデータ層アプリケーションのインポート ウィザード](https://docs.microsoft.com/sql/relational-databases/data-tier-applications/import-a-bacpac-file-to-create-a-new-user-database#using-the-import-data-tier-application-wizard)。
- [SQL Server のインポートおよびエクスポート ウィザード](https://docs.microsoft.com/sql/integration-services/import-export-data/start-the-sql-server-import-and-export-wizard)。

## <a name="next-steps"></a>次の手順

- インポートした SQL Database に接続してそれを照会する方法については、「[クイック スタート:Azure SQL Database:SQL Server Management Studio を使って接続とデータの照会を行う](sql-database-connect-query-ssms.md)」という記事をご覧ください。
- BACPAC ファイルを使用した移行に関する SQL Server Customer Advisory Team のブログについては、「[Migrating from SQL Server to Azure SQL Database using BACPAC Files (BACPAC ファイルを使用した SQL Server から Azure SQL Database への移行)](https://techcommunity.microsoft.com/t5/DataCAT/Migrating-from-SQL-Server-to-Azure-SQL-Database-using-Bacpac/ba-p/305407)」を参照してください。
- パフォーマンスの推奨事項も含む、SQL Server データベースの移行プロセス全体の詳細については、[Azure SQL Database への SQL Server データベースの移行](sql-database-single-database-migrate.md)に関するページを参照してください。
- ストレージ キーと共有アクセス署名を管理および共有する方法については、「[Azure Storage セキュリティ ガイド](https://docs.microsoft.com/azure/storage/common/storage-security-guide)」をご覧ください。
