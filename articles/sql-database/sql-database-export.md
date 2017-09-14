---
title: "Azure SQL Database を BACPAC ファイルにエクスポートする | Microsoft Docs"
description: "Azure Portal を使って BACPAC ファイルに Azure SQL Database をエクスポートする"
services: sql-database
documentationcenter: 
author: CarlRabeler
manager: jhubbard
editor: 
ms.assetid: 41d63a97-37db-4e40-b652-77c2fd1c09b7
ms.service: sql-database
ms.custom: load & move data
ms.devlang: NA
ms.date: 06/15/2017
ms.author: carlrab
ms.workload: data-management
ms.topic: article
ms.tgt_pltfrm: NA
ms.translationtype: HT
ms.sourcegitcommit: c30998a77071242d985737e55a7dc2c0bf70b947
ms.openlocfilehash: 668e7d18951412e9649e2f5273a4ef1116ee8117
ms.contentlocale: ja-jp
ms.lasthandoff: 08/02/2017

---
# <a name="export-an-azure-sql-database-to-a-bacpac-file"></a>Azure SQL Database を BACPAC ファイルにエクスポートする

アーカイブのため、または別のプラットフォームに移行するためにデータベースをエクスポートする必要がある際は、データベース スキーマとデータを [BACPAC](https://msdn.microsoft.com/library/ee210546.aspx#Anchor_4) ファイルにエクスポートできます。 BACPAC ファイルは、メタデータと SQL Server データベースからのデータを含み、BACPAC の拡張子を持つ ZIP ファイルです。 BACPAC ファイルは、Azure Blob Storage またはオンプレミスの場所にあるローカル ストレージに格納でき、後で Azure SQL Database またはオンプレミス インストールの SQL Server にインポートすることができます。 

> [!IMPORTANT] 
> Azure SQL Database の自動エクスポート機能は、2017 年 3 月 1 日に廃止されました。 [長期のバックアップ リテンション期間](sql-database-long-term-retention.md
)または [Azure Automation](https://github.com/Microsoft/azure-docs-pr/blob/2461f706f8fc1150e69312098640c0676206a531/articles/automation/automation-intro.md) を使用して、選択したスケジュールに従って PowerShell を使用して定期的に SQL データベースをアーカイブできます。 サンプルについては、GitHub から [PowerShell のサンプル スクリプト](https://github.com/Microsoft/sql-server-samples/tree/master/samples/manage/azure-automation-automated-export)をダウンロードできます。
>

## <a name="considerations-when-exporting-an-azure-sql-database"></a>Azure SQL Database をエクスポートする際の考慮事項

* エクスポートにトランザクション一貫性を持たせるために、書き込みアクティビティがエクスポート中に行われないようにするか、Azure SQL Database の[トランザクション一貫性のあるコピー](sql-database-copy.md)からエクスポートする必要があります。
* Blob Storage にエクスポートする場合、BACPAC ファイルの最大サイズは 200 GB です。 大きな BACPAC ファイルをアーカイブするには、ローカル ストレージにエクスポートします。
* この記事で説明されている方法を用いた Azure Premium Storage への BACPAC ファイルのエクスポートはサポートされていません。
* Azure SQL Database からのエクスポート操作が 20 時間を超える場合は、操作が取り消されることがあります。 エクスポート中にパフォーマンスを向上させるには、次の操作を実行します。
  * サービス レベルを一時的に上げる。
  * エクスポート中のすべての読み取りアクティビティと書き込みアクティビティを中止する。
  * すべての大きなテーブルに null 以外の値を持つ [クラスター化インデックス](https://msdn.microsoft.com/library/ms190457.aspx) を使用する。 クラスター化インデックスがないと、エクスポートが 6 ～ 12 時間よりも時間が長くかかる場合には失敗することがあります。 これは、エクスポート サービスがテーブル スキャンを実行してテーブル全体をエクスポートしようとする必要があることが原因です。 テーブルがエクスポート向けに最適化されているかを判断するための適切な方法として、**DBCC SHOW_STATISTICS** を実行し、*RANGE_HI_KEY* が null 以外の値であり、分布が適切であることを確認する方法があります。 詳細については、「[DBCC SHOW_STATISTICS](https://msdn.microsoft.com/library/ms174384.aspx)」を参照してください。

> [!NOTE]
> BACPAC はバックアップおよび復元操作に使用するためのものでありません。 Azure SQL Database では、すべてのユーザー データベースのバックアップが自動的に作成されます。 詳細については、[ビジネス継続性の概要](sql-database-business-continuity.md)に関するページと、[ バックアップ](sql-database-automated-backups.md)に関するページを参照してください。  
> 

## <a name="export-to-a-bacpac-file-using-the-azure-portal"></a>Azure Portal を使用して BACPAC ファイルにエクスポートする

[Azure Portal](https://portal.azure.com) を使用してデータベースをエクスポートするには、データベースのページを開き、ツールバーの **[エクスポート]** をクリックします。 BACPAC ファイル名を指定し、Azure ストレージ アカウントとエクスポート用コンテナーを指定してから資格情報を指定して、ソース データベースに接続します。  

![データベースのエクスポート](./media/sql-database-export/database-export.png)

エクスポート操作の進行状況を監視するには、エクスポートされたデータベースを含む論理サーバーのページを開きます。 **[操作]** までスクロールして、**[インポート/エクスポート]** 履歴をクリックします。

![エクスポート履歴](./media/sql-database-export/export-history.png)
![エクスポートの履歴の状態](./media/sql-database-export/export-history2.png)

## <a name="export-to-a-bacpac-file-using-the-sqlpackage-utility"></a>SQLPackage ユーティリティを使用して BACPAC ファイルにエクスポートする

[SqlPackage](https://msdn.microsoft.com/library/hh550080.aspx) コマンドライン ユーティリティを使用して SQL データベースをエクスポートするには、「[エクスポートのパラメーターおよびプロパティ](https://msdn.microsoft.com/library/hh550080.aspx#Export Parameters and Properties)」を参照してください。 SQL Package ユーティリティは、最新バージョンの [SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx) および [SQL Server Data Tools for Visual Studio](https://msdn.microsoft.com/library/mt204009.aspx) に付属しています。また、Microsoft ダウンロード センターから最新バージョンの [SqlPackage](https://www.microsoft.com/download/details.aspx?id=53876) を直接ダウンロードすることもできます。

ほとんどの運用環境では、スケールとパフォーマンスのために、SQLPackage ユーティリティの使用をお勧めします。 BACPAC ファイルを使用した移行に関する SQL Server Customer Advisory Team のブログについては、「[Migrating from SQL Server to Azure SQL Database using BACPAC Files (BACPAC ファイルを使用した SQL Server から Azure SQL Database への移行)](https://blogs.msdn.microsoft.com/sqlcat/2016/10/20/migrating-from-sql-server-to-azure-sql-database-using-bacpac-files/)」を参照してください。

この例では、SqlPackage.exe と Active Directory ユニバーサル認証を使用してデータベースをエクスポートする方法を示します。

```cmd
SqlPackage.exe /a:Export /tf:testExport.bacpac /scs:"Data Source=apptestserver.database.windows.net;Initial Catalog=MyDB;" /ua:True /tid:"apptest.onmicrosoft.com"
```

## <a name="export-to-a-bacpac-file-using-sql-server-management-studio-ssms"></a>SQL Server Management Studio (SSMS) を使用して BACPAC ファイルにエクスポートする

最新バージョンの SQL Server Management Studio では、Azure SQL Database を BACPAC ファイルにエクスポートするウィザードも提供します。 [「データ層アプリケーションのエクスポート」](https://docs.microsoft.com/sql/relational-databases/data-tier-applications/export-a-data-tier-application)を参照してください。

## <a name="export-to-a-bacpac-file-using-powershell"></a>PowerShell を使用して BACPAC ファイルにエクスポートする

[New-AzureRmSqlDatabaseExport](/powershell/module/azurerm.sql/new-azurermsqldatabaseexport) コマンドレットを使用して、Azure SQL Database サービスにデータベース エクスポート要求を送信します。 データベースのサイズに応じて、エクスポート操作の完了に時間がかかる場合があります。

 ```powershell
 $exportRequest = New-AzureRmSqlDatabaseExport -ResourceGroupName $ResourceGroupName -ServerName $ServerName `
   -DatabaseName $DatabaseName -StorageKeytype $StorageKeytype -StorageKey $StorageKey -StorageUri $BacpacUri `
   -AdministratorLogin $creds.UserName -AdministratorLoginPassword $creds.Password
 ```

エクスポート要求の状態を確認するには、[GetAzureRmSqlDatabaseImportExportStatus](/powershell/module/azurerm.sql/get-azurermsqldatabaseimportexportstatus) コマンドレットを使用します。 要求直後にこれを実行すると、通常、**Status: InProgress** が返されます。 **Status : Succeeded** が表示された場合、エクスポートは完了しています。

```powershell
$exportStatus = Get-AzureRmSqlDatabaseImportExportStatus -OperationStatusLink $exportRequest.OperationStatusLink
[Console]::Write("Exporting")
while ($exportStatus.Status -eq "InProgress")
{
    $exportStatus = Get-AzureRmSqlDatabaseImportExportStatus -OperationStatusLink $exportRequest.OperationStatusLink
    [Console]::Write(".")
    Start-Sleep -s 10
}
[Console]::WriteLine("")
$exportStatus
```

## <a name="next-steps"></a>次のステップ

* データベースをアーカイブ目的でエクスポートする方法の代わりとしての、Azure SQL データベース バックアップの長期バックアップ リテンション期間については、[長期バックアップ リテンション](sql-database-long-term-retention.md)に関する記事をご覧ください。
- BACPAC ファイルを使用した移行に関する SQL Server Customer Advisory Team のブログについては、「[Migrating from SQL Server to Azure SQL Database using BACPAC Files (BACPAC ファイルを使用した SQL Server から Azure SQL Database への移行)](https://blogs.msdn.microsoft.com/sqlcat/2016/10/20/migrating-from-sql-server-to-azure-sql-database-using-bacpac-files/)」を参照してください。
* SQL Server データベースへの BACPAC のインポートについては、[「SQL Server データベースへの BACPAC のインポート」](https://msdn.microsoft.com/library/hh710052.aspx)を参照してください。
* SQL Server データベースから BACPAC をエクスポートする方法については、[「データ層アプリケーションのエクスポート」](https://docs.microsoft.com/sql/relational-databases/data-tier-applications/export-a-data-tier-application)と[「最初のデータベースの移行」](sql-database-migrate-your-sql-server-database.md)を参照してください。
* Azure SQL Database への移行準備として SQL Server からエクスポートする場合は、[SQL Server データベースの Azure SQL Database への移行](sql-database-cloud-migrate.md)に関するページを参照してください。

