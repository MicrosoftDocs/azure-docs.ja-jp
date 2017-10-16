---
title: "BACPAC ファイルをインポートして Azure SQL Database を作成する | Microsoft Docs"
description: "BACPAC ファイルをインポートして新しい Azure SQL Database を作成します。"
services: sql-database
documentationcenter: 
author: CarlRabeler
manager: jhubbard
editor: 
ms.assetid: cf9a9631-56aa-4985-a565-1cacc297871d
ms.service: sql-database
ms.custom: load & move data
ms.devlang: NA
ms.date: 06/26/2017
ms.author: carlrab
ms.workload: data-management
ms.topic: article
ms.tgt_pltfrm: NA
ms.openlocfilehash: 5ff4184a6147c367c64b5c90c090e1abe59edb90
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="import-a-bacpac-file-to-a-new-azure-sql-database"></a>BACPAC ファイルを新しい Azure SQL Database にインポートする

データベースをアーカイブからインポートまたは別のプラットフォームから移行するときに、[BACPAC](https://msdn.microsoft.com/library/ee210546.aspx#Anchor_4) ファイルからデータベース スキーマとデータをインポートできます。 BACPAC ファイルは、メタデータと SQL Server データベースからのデータを含み、BACPAC の拡張子を持つ ZIP ファイルです。 BACPAC ファイルは Azure Blob Storage (Standard ストレージのみ) またオンプレミスの保存先のローカル ストレージからインポートできます。 インポート速度を最大限高めるために、より高いサービス階層とパフォーマンス レベル (P6 など) を指定し、インポートが正常に完了した後は必要に応じてスケール ダウンすることをお勧めします。 また、インポート後のデータベースの互換性レベルはソース データベースの互換性レベルに基づきます。 

> [!IMPORTANT] 
> データベースを Azure SQL Database に移行した後に、データベースを現在の互換性レベル (AdventureWorks2008R2 データベースの場合はレベル 100) またはそれ以上のレベルで運用できます。 データベースを指定の互換性レベルで運用する影響とオプションについて詳しくは、[ALTER DATABASE の互換性レベル](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql-compatibility-level)に関する記事をご覧ください。 また、互換性レベルに関連する追加のデータベースレベルの設定について詳しくは、[ALTER DATABASE SCOPED CONFIGURATION](https://docs.microsoft.com/sql/t-sql/statements/alter-database-scoped-configuration-transact-sql) に関する記事をご覧ください。   >

> [!NOTE]
> BACPAC を新しいデータベースにインポートするには、まず Azure SQL Database 論理サーバーを作成する必要があります。 SQLPackage を使用して Azure SQL Databaseに SQL Server データベースを移行する方法について詳しくは、「[SQL Server データベースを移行する](sql-database-migrate-your-sql-server-database.md)」をご覧ください。
>

## <a name="import-from-a-bacpac-file-using-azure-portal"></a>Azure Portal を使用して BACPAC ファイルからインポートする

この記事では、[Azure Portal](https://portal.azure.com) を使って、Azure Blob Storage に保存されている BACPAC ファイルから Azure SQL Database を作成する手順について説明します。 Azure Portal を使用する場合は、Azure Blob Storage からのインポートのみをサポートします。

Azure Portal を使用してデータベースをインポートするには、データベースに関連付けられたサーバーのページを開き、ツールバーの **[インポート]** をクリックします。 ストレージ アカウントとコンテナーを指定し、インポートする BACPAC ファイルを選択します。 新しいデータベースのサイズ (通常、元のサイズと同じ) を選択し、インポート先の SQL Server 資格情報を指定します。  

   ![データベースのインポート](./media/sql-database-import/import.png)

インポート操作の進行状況を監視するには、インポートされるデータベースが含まれる論理サーバーのページを開きます。 **[操作]** までスクロールして、**[インポート/エクスポート]** 履歴をクリックします。

### <a name="monitor-the-progress-of-an-import-operation"></a>インポート操作の進行状況の監視

インポート操作の進行状況を監視するには、データベースがインポートされる論理サーバーのページを開きます。 **[操作]** までスクロールして、**[インポート/エクスポート]** 履歴をクリックします。
   
   ![インポート](./media/sql-database-import/import-history.png) ![インポートの状態](./media/sql-database-import/import-status.png)

データベースがサーバーで稼働していることを確認するには、**[SQL データベース]** をクリックし、新しいデータベースが **[オンライン]** であることを確認します。

## <a name="import-from-a-bacpac-file-using-sqlpackage"></a>SQLPackage を使用して BACPAC ファイルからインポートする

[SqlPackage](https://msdn.microsoft.com/library/hh550080.aspx) コマンドライン ユーティリティを使用して SQL データベースをインポートするには、「[Import のパラメーターとプロパティ](https://msdn.microsoft.com/library/hh550080.aspx#Import Parameters and Properties)」をご覧ください。 SQLPackage ユーティリティは、最新バージョンの [SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx) および [SQL Server Data Tools for Visual Studio](https://msdn.microsoft.com/library/mt204009.aspx) に付属しています。また、Microsoft ダウンロード センターから最新バージョンの [SqlPackage](https://www.microsoft.com/download/details.aspx?id=53876) を直接ダウンロードすることもできます。

ほとんどの運用環境では、スケールとパフォーマンスのために、SQLPackage ユーティリティの使用をお勧めします。 BACPAC ファイルを使用した移行に関する SQL Server Customer Advisory Team のブログについては、「[Migrating from SQL Server to Azure SQL Database using BACPAC Files (BACPAC ファイルを使用した SQL Server から Azure SQL Database への移行)](https://blogs.msdn.microsoft.com/sqlcat/2016/10/20/migrating-from-sql-server-to-azure-sql-database-using-bacpac-files/)」を参照してください。

**AdventureWorks2008R2** データベースをローカル ストレージから Azure SQL Database 論理サーバーにインポートするスクリプト例については、次の SQLPackage コマンドをご覧ください。この例では **mynewserver20170403** という名前です。 このスクリプトは、サービス階層が **Premium**、サービス目標が **P6** の **myMigratedDatabase** という新しいデータベースを作成する方法を示します。 これらの値は、お使いの環境に合わせて変更してください。

```cmd
SqlPackage.exe /a:import /tcs:"Data Source=mynewserver20170403.database.windows.net;Initial Catalog=myMigratedDatabase;User Id=ServerAdmin;Password=<change_to_your_password>" /sf:AdventureWorks2008R2.bacpac /p:DatabaseEdition=Premium /p:DatabaseServiceObjective=P6
```

> [!IMPORTANT]
> Azure SQL Database 論理サーバーは、ポート 1433 でリッスンします。 企業のファイアウォール内から Azure SQL Database 論理サーバーに接続する場合、企業のファイアウォールでこのポートが開放されていないと、正しく接続することができません。
>

この例では、SqlPackage.exe と Active Directory ユニバーサル認証を使用してデータベースをインポートする方法を示します。

```cmd
SqlPackage.exe /a:Import /sf:testExport.bacpac /tdn:NewDacFX /tsn:apptestserver.database.windows.net /ua:True /tid:"apptest.onmicrosoft.com"
```

## <a name="import-from-a-bacpac-file-using-powershell"></a>PowerShell を使用して BACPAC ファイルからインポートする

[New-AzureRmSqlDatabaseImport](/powershell/module/azurerm.sql/new-azurermsqldatabaseimport) コマンドレットを使用して、Azure SQL Database サービスにデータベースのインポート要求を送信します。 データベースのサイズに応じて、インポート操作の完了に時間がかかる場合があります。

 ```powershell
 $importRequest = New-AzureRmSqlDatabaseImport -ResourceGroupName "myResourceGroup" `
    -ServerName $servername `
    -DatabaseName "MyImportSample" `
    -DatabaseMaxSizeBytes "262144000" `
    -StorageKeyType "StorageAccessKey" `
    -StorageKey $(Get-AzureRmStorageAccountKey -ResourceGroupName "myResourceGroup" -StorageAccountName $storageaccountname).Value[0] `
    -StorageUri "http://$storageaccountname.blob.core.windows.net/importsample/sample.bacpac" `
    -Edition "Standard" `
    -ServiceObjectiveName "P6" `
    -AdministratorLogin "ServerAdmin" `
    -AdministratorLoginPassword $(ConvertTo-SecureString -String "ASecureP@assw0rd" -AsPlainText -Force)

 ```

インポート要求の状態を確認するには、[Get-AzureRmSqlDatabaseImportExportStatus](/powershell/module/azurerm.sql/get-azurermsqldatabaseimportexportstatus) コマンドレットを使用します。 要求直後にこれを実行すると、通常は、 **Status : InProgress**が返されます。 **Status : Succeeded** が表示された場合、インポートは完了しています。

```powershell
$importStatus = Get-AzureRmSqlDatabaseImportExportStatus -OperationStatusLink $importRequest.OperationStatusLink
[Console]::Write("Importing")
while ($importStatus.Status -eq "InProgress")
{
    $importStatus = Get-AzureRmSqlDatabaseImportExportStatus -OperationStatusLink $importRequest.OperationStatusLink
    [Console]::Write(".")
    Start-Sleep -s 10
}
[Console]::WriteLine("")
$importStatus
```

> [!TIP]
別のスクリプト例については、「[データベースを BACPAC ファイルからインポートする](scripts/sql-database-import-from-bacpac-powershell.md)」を参照してください。

## <a name="next-steps"></a>次のステップ
* インポートされた SQL データベースへの接続とクエリの実行については、「[SQL Server Management Studio を使用して SQL Database に接続し、T-SQL サンプル クエリを実行する](sql-database-connect-query-ssms.md)」を参照してください。
* BACPAC ファイルを使用した移行に関する SQL Server Customer Advisory Team のブログについては、「[Migrating from SQL Server to Azure SQL Database using BACPAC Files (BACPAC ファイルを使用した SQL Server から Azure SQL Database への移行)](https://blogs.msdn.microsoft.com/sqlcat/2016/10/20/migrating-from-sql-server-to-azure-sql-database-using-bacpac-files/)」を参照してください。
* パフォーマンスの推奨事項も含む、SQL Server データベースの移行プロセス全体の詳細については、[Azure SQL Database への SQL Server データベースの移行](sql-database-cloud-migrate.md)に関するページを参照してください。



