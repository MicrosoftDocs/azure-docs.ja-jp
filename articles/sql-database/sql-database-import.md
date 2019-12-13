---
title: BACPAC ファイルをインポートしてデータベースを作成する
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
ms.date: 06/20/2019
ms.openlocfilehash: 8d147248245ca06c145c68ee8ca9cfd02015aa9c
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/08/2019
ms.locfileid: "74929166"
---
# <a name="quickstart-import-a-bacpac-file-to-a-database-in-azure-sql-database"></a>クイック スタート:BACPAC ファイルを Azure SQL Database 内のデータベースにインポートする

[BACPAC](https://docs.microsoft.com/sql/relational-databases/data-tier-applications/data-tier-applications#bacpac) ファイルを使用して、Azure SQL Database 内のデータベースに SQL Server データベースをインポートできます。 Azure Blob Storage (Standard ストレージのみ) 内に格納された `BACPAC` ファイルから、またはオンプレミスの場所にあるローカル ストレージから、データをインポートできます。 より高速なリソースを多く提供することでインポート速度を最大化するには、インポート プロセスの中でデータベースをより高いサービス層とコンピューティング サイズにスケーリングします。 インポートが正常に完了した後でスケールダウンすることができます。

> [!NOTE]
> インポートされたデータベースの互換性レベルは、ソース データベースの互換性レベルに基づきます。

> [!IMPORTANT]
> データベースをデータベースに移行した後に、データベースを現在の互換性レベル (AdventureWorks2008R2 データベースの場合はレベル 100) またはそれ以上のレベルで運用できます。 データベースを指定の互換性レベルで運用する影響とオプションについて詳しくは、[ALTER DATABASE の互換性レベル](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql-compatibility-level)に関する記事をご覧ください。 また、互換性レベルに関連する追加のデータベースレベルの設定について詳しくは、[ALTER DATABASE SCOPED CONFIGURATION](https://docs.microsoft.com/sql/t-sql/statements/alter-database-scoped-configuration-transact-sql) に関する記事をご覧ください。

## <a name="using-azure-portal"></a>Azure Portal の使用

このビデオをご覧になり、Azure portal で BACPAC ファイルからインポートする方法を確認するか、以下の続きをお読みください。

> [!VIDEO https://channel9.msdn.com/Shows/Data-Exposed/Its-just-SQL-Restoring-a-database-to-Azure-SQL-DB-from-backup/player?WT.mc_id=dataexposed-c9-niner]

[Azure portal](https://portal.azure.com) では、Azure SQL Database 内の単一データベースの作成 "*だけ*" が、Azure Blob Storage に格納されている BACPAC ファイルから "*のみ*" サポートされています。

現在のところ、Azure PowerShell を使用し、BACPAC ファイルから[マネージド インスタンス](sql-database-managed-instance.md)にデータベースをエクスポートすることはサポートされていません。 代わりに、SQL Server Management Studio または SQLPackage を使用してください。

> [!NOTE]
> Azure portal または PowerShell から送信されたインポート/エクスポート要求を処理するマシンは、BACPAC ファイルとデータ層アプリケーション フレームワーク (DacFX) によって生成された一時ファイルを格納する必要があります。 必要なディスク領域は、同じサイズのデータベースでも大きく異なります。データベースのサイズの最大 3 倍のディスク領域が必要になることがあります。 インポート/エクスポート要求のみを実行するマシンには、450 GB のローカル ディスク領域があります。 その結果、一部の要求がエラー `There is not enough space on the disk` で失敗することがあります。 この場合の回避策は、十分なローカル ディスク領域を持つマシンで sqlpackage.exe を実行することです。 150 GB を超えるデータベースをインポート/エクスポートする場合は、SqlPackage を使用してこの問題を回避することをお勧めします。

1. Azure portal を使用して新しい単一データベースに BACPAC ファイルからインポートするには、適切なデータベース サーバーのページを開き、 **[データベースのインポート]** を選択します。  

   ![データベース インポート 1](./media/sql-database-import/sql-server-import-database.png)

1. ストレージ アカウントと BACPAC ファイルのコンテナーを選択してから、インポート元の BACPAC ファイルを選択します。

1. 新しいデータベースのサイズ (通常、元のサイズと同じ) を指定し、インポート先の SQL Server 資格情報を指定します。 新しい Azure SQL データベースに使用できる値の一覧については、[データベースの作成](https://docs.microsoft.com/sql/t-sql/statements/create-database-transact-sql?view=azuresqldb-current)に関するページをご覧ください。

   ![データベース インポート 2](./media/sql-database-import/sql-server-import-database-settings.png)

1. Click **OK**.

1. インポートの進行状況を監視するには、データベースのサーバー ページを開き、 **[設定]** の下にある **[インポート/エクスポート履歴]** を選択します。 成功すると、インポートは**完了**状態になります。

   ![データベース インポートの状態](./media/sql-database-import/sql-server-import-database-history.png)

1. データベースがデータベース サーバー上で稼働していることを確認するには、 **[SQL データベース]** を選択し、新しいデータベースが **[オンライン]** であることを確認します。

## <a name="using-sqlpackage"></a>SqlPackage の使用

[SqlPackage](https://docs.microsoft.com/sql/tools/sqlpackage) コマンドライン ユーティリティを使用して SQL Server データベースをインポートするには、「[インポート パラメーターとプロパティ](https://docs.microsoft.com/sql/tools/sqlpackage#import-parameters-and-properties)」をご覧ください。 SqlPackage には、最新の [SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms) と [SQL Server Data Tools for Visual Studio](https://msdn.microsoft.com/library/mt204009.aspx) が含まれています。 最新の [SqlPackage](https://www.microsoft.com/download/details.aspx?id=53876) は、Microsoft ダウンロード センターからダウンロードすることもできます。

スケールとパフォーマンスのために、ほとんどの運用環境では、Azure portal の使用ではなく SqlPackage の使用をお勧めします。 `BACPAC` ファイルを使用した移行に関する SQL Server Customer Advisory Team のブログについては、「[Migrating from SQL Server to Azure SQL Database using BACPAC Files (BACPAC ファイルを使用した SQL Server から Azure SQL Database への移行)](https://blogs.msdn.microsoft.com/sqlcat/2016/10/20/migrating-from-sql-server-to-azure-sql-database-using-bacpac-files/)」をご覧ください。

次の SqlPackage コマンドにより、**AdventureWorks2008R2** データベースが、ローカル ストレージから **mynewserver20170403** という Azure SQL Database サーバーにインポートされます。 **Premium** サービス層と **P6** サービス オブジェクトがある **myMigratedDatabase** という新しいデータベースが作成されます。 これらの値は、お使いの環境に合わせて変更してください。

```cmd
sqlpackage.exe /a:import /tcs:"Data Source=<serverName>.database.windows.net;Initial Catalog=<migratedDatabase>;User Id=<userId>;Password=<password>" /sf:AdventureWorks2008R2.bacpac /p:DatabaseEdition=Premium /p:DatabaseServiceObjective=P6
```

> [!IMPORTANT]
> 企業のファイアウォールの外側から単一のデータベースを管理している SQL Database サーバーに接続するには、そのファイアウォールでポート 1433 が開かれている必要があります。 マネージド インスタンスに接続するには、[ポイント対サイト接続](sql-database-managed-instance-configure-p2s.md)または Express Route 接続が使用できる必要があります。

この例では、SqlPackage と Active Directory ユニバーサル認証を使用してデータベースをインポートする方法を示します。

```cmd
sqlpackage.exe /a:Import /sf:testExport.bacpac /tdn:NewDacFX /tsn:apptestserver.database.windows.net /ua:True /tid:"apptest.onmicrosoft.com"
```

## <a name="using-powershell"></a>PowerShell の使用

> [!NOTE]
> [マネージド インスタンス](sql-database-managed-instance.md)では現在、Azure PowerShell を使用した BACPAC ファイルからインスタンス データベースへのデータベースの移行はサポートされていません。 マネージド インスタンスにインポートするには、SQL Server Management Studio または SQLPackage を使用します。

> [!NOTE]
> portal または Powershell から送信されたインポート/エクスポート要求を処理するマシンは、bacpac ファイルとデータ層アプリケーション フレームワーク (DacFX) によって生成された一時ファイルを格納する必要があります。 必要なディスク領域は、同じサイズの DB でも大きく異なり、最大 3 倍のデータベースのサイズが必要になります。 インポート/エクスポート要求のみを実行するマシンには、450 GB のローカル ディスク領域があります。 結果として、一部の要求が「十分なディスク領域が存在しない」のエラーで失敗する場合があります。 この場合の回避策は、十分なローカル ディスク領域を持つマシンで sqlpackage.exe を実行することです。 150 GB を超えるデータベースをインポート/エクスポートする場合は、SqlPackage を使用してこの問題を回避します。

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

> [!IMPORTANT]
> PowerShell Azure Resource Manager (RM) モジュールは Azure SQL Database で引き続きサポートされますが、今後の開発はすべて Az.Sql モジュールを対象に行われます。 AzureRM モジュールのバグ修正は、少なくとも 2020 年 12 月までは引き続き受け取ることができます。  Az モジュールと AzureRm モジュールのコマンドの引数は実質的に同じです。 その互換性の詳細については、「[新しい Azure PowerShell Az モジュールの概要](/powershell/azure/new-azureps-module-az)」を参照してください。

[New-AzSqlDatabaseImport](/powershell/module/az.sql/new-azsqldatabaseimport) コマンドレットを使用して、Azure SQL Database サービスにデータベースのインポート要求を送信します。 データベースのサイズによって、インポートが完了するまでに時間がかかる場合があります。

```powershell
$importRequest = New-AzSqlDatabaseImport -ResourceGroupName "<resourceGroupName>" `
    -ServerName "<serverName>" -DatabaseName "<databaseName>" `
    -DatabaseMaxSizeBytes "<databaseSizeInBytes>" -StorageKeyType "StorageAccessKey" `
    -StorageKey $(Get-AzStorageAccountKey `
        -ResourceGroupName "<resourceGroupName>" -StorageAccountName "<storageAccountName>").Value[0] `
        -StorageUri "https://myStorageAccount.blob.core.windows.net/importsample/sample.bacpac" `
        -Edition "Standard" -ServiceObjectiveName "P6" `
        -AdministratorLogin "<userId>" `
        -AdministratorLoginPassword $(ConvertTo-SecureString -String "<password>" -AsPlainText -Force)
```

インポートの進行状況を確認するには、[Get-AzSqlDatabaseImportExportStatus](/powershell/module/az.sql/get-azsqldatabaseimportexportstatus) コマンドレットを使用できます。 要求直後にこのコマンドレットを実行すると、通常は `Status: InProgress` が返されます。 `Status: Succeeded` を確認したらインポートは完了です。

```powershell
$importStatus = Get-AzSqlDatabaseImportExportStatus -OperationStatusLink $importRequest.OperationStatusLink

[Console]::Write("Importing")
while ($importStatus.Status -eq "InProgress") {
    $importStatus = Get-AzSqlDatabaseImportExportStatus -OperationStatusLink $importRequest.OperationStatusLink
    [Console]::Write(".")
    Start-Sleep -s 10
}

[Console]::WriteLine("")
$importStatus
```

# <a name="azure-clitabazure-cli"></a>[Azure CLI](#tab/azure-cli)

[az-sql-db-import](/cli/azure/sql/db#az-sql-db-import) コマンドを使用して、Azure SQL Database サービスにデータベースのインポート要求を送信します。 データベースのサイズによって、インポートが完了するまでに時間がかかる場合があります。

```azure-cli
# get the storage account key
az storage account keys list --resource-group "<resourceGroupName>" --account-name "<storageAccountName>"

az sql db import --resource-group "<resourceGroupName>" --server "<serverName>" --name "<databaseName>" `
    --storage-key-type "StorageAccessKey" --storage-key "<storageAccountKey>" `
    --storage-uri "https://myStorageAccount.blob.core.windows.net/importsample/sample.bacpac" `
    -u "<userId>" -p $(ConvertTo-SecureString -String "<password>" -AsPlainText -Force)
```

* * *

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
