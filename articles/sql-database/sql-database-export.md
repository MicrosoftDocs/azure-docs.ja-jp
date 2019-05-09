---
title: 単一の、またはプールされた Azure SQL データベースを BACPAC ファイルにエクスポートする | Microsoft Docs
description: Azure Portal を使用して BACPAC ファイルに Azure SQL Database をエクスポートする
services: sql-database
ms.service: sql-database
ms.subservice: data-movement
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: CarlRabeler
ms.author: carlrab
ms.reviewer: carlrab
manager: craigg
ms.date: 03/11/2019
ms.openlocfilehash: 27a65a871264fa13a42acfb5be2d4b5f99d31adc
ms.sourcegitcommit: 5fbca3354f47d936e46582e76ff49b77a989f299
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/12/2019
ms.locfileid: "57758694"
---
# <a name="export-an-azure-sql-database-to-a-bacpac-file"></a>Azure SQL Database を BACPAC ファイルにエクスポートする

アーカイブのため、または別のプラットフォームに移行するためにデータベースをエクスポートする必要がある際は、データベース スキーマとデータを [BACPAC](https://msdn.microsoft.com/library/ee210546.aspx#Anchor_4) ファイルにエクスポートできます。 BACPAC ファイルは、メタデータと SQL Server データベースからのデータを含み、BACPAC の拡張子を持つ ZIP ファイルです。 BACPAC ファイルは、Azure Blob Storage またはオンプレミスの場所にあるローカル ストレージに格納でき、後で Azure SQL Database またはオンプレミス インストールの SQL Server にインポートすることができます。

## <a name="considerations-when-exporting-an-azure-sql-database"></a>Azure SQL Database をエクスポートする際の考慮事項

- エクスポートにトランザクション一貫性を持たせるために、書き込みアクティビティがエクスポート中に行われないようにするか、Azure SQL Database の[トランザクション一貫性のあるコピー](sql-database-copy.md)からエクスポートする必要があります。
- Blob Storage にエクスポートする場合、BACPAC ファイルの最大サイズは 200 GB です。 大きな BACPAC ファイルをアーカイブするには、ローカル ストレージにエクスポートします。
- この記事で説明されている方法を用いた Azure Premium Storage への BACPAC ファイルのエクスポートはサポートされていません。
- ファイアウォールの背後にある Storage は現在サポートされていません。
- Azure SQL Database からのエクスポート操作が 20 時間を超える場合は、操作が取り消されることがあります。 エクスポート中にパフォーマンスを向上させるには、次の操作を実行します。

  - コンピューティング サイズを一時的に増やします。
  - エクスポート中のすべての読み取りアクティビティと書き込みアクティビティを中止する。
  - すべての大きなテーブルに null 以外の値を持つ [クラスター化インデックス](https://msdn.microsoft.com/library/ms190457.aspx) を使用する。 クラスター化インデックスがないと、エクスポートが 6 ～ 12 時間よりも時間が長くかかる場合には失敗することがあります。 これは、エクスポート サービスがテーブル スキャンを実行してテーブル全体をエクスポートしようとする必要があることが原因です。 テーブルがエクスポート向けに最適化されているかを判断するための適切な方法として、**DBCC SHOW_STATISTICS** を実行し、*RANGE_HI_KEY* が null 以外の値であり、分布が適切であることを確認する方法があります。 詳細については、「[DBCC SHOW_STATISTICS](https://msdn.microsoft.com/library/ms174384.aspx)」を参照してください。

> [!NOTE]
> BACPAC はバックアップおよび復元操作に使用するためのものでありません。 Azure SQL Database では、すべてのユーザー データベースのバックアップが自動的に作成されます。 詳しくは、[ビジネス継続性の概要](sql-database-business-continuity.md)に関するページと、[SQL Database のバックアップ](sql-database-automated-backups.md)に関するページをご覧ください。

## <a name="export-to-a-bacpac-file-using-the-azure-portal"></a>Azure Portal を使用して BACPAC ファイルにエクスポートする

> [!NOTE]
> [マネージド インスタンス](sql-database-managed-instance.md)では現在、Azure portal を使用して BACPAC ファイルにデータベースをエクスポートすることはサポートされていません。 マネージド インスタンスを BACPAC ファイルにエクスポートするには、SQL Server Management Studio または SQLPackage を使用します。

1. [Azure Portal](https://portal.azure.com) を使用してデータベースをエクスポートするには、データベースのページを開き、ツールバーの **[エクスポート]** をクリックします。

   ![データベースのエクスポート](./media/sql-database-export/database-export1.png)

2. BACPAC ファイル名を指定し、エクスポートに使用する既存の Azure ストレージ アカウントとコンテナーを選択した後、ソース データベースにアクセスするための適切な資格情報を指定します。

    ![データベースのエクスポート](./media/sql-database-export/database-export2.png)

3. Click **OK**.

4. エクスポート操作の進行状況を監視するには、エクスポートされたデータベースを含む SQL Database サーバーのページを開きます。 **[設定]** の下で、**[インポート/エクスポート履歴]** をクリックします。

   ![エクスポート履歴](./media/sql-database-export/export-history.png)

## <a name="export-to-a-bacpac-file-using-the-sqlpackage-utility"></a>SQLPackage ユーティリティを使用して BACPAC ファイルにエクスポートする

[SqlPackage](https://docs.microsoft.com/sql/tools/sqlpackage) コマンドライン ユーティリティを使用して SQL データベースをエクスポートするには、「[エクスポートのパラメーターおよびプロパティ](https://docs.microsoft.com/sql/tools/sqlpackage#export-parameters-and-properties)」を参照してください。 SQL Package ユーティリティは、最新バージョンの [SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx) および [SQL Server Data Tools for Visual Studio](https://msdn.microsoft.com/library/mt204009.aspx) に付属しています。また、Microsoft ダウンロード センターから最新バージョンの [SqlPackage](https://www.microsoft.com/download/details.aspx?id=53876) を直接ダウンロードすることもできます。

ほとんどの運用環境では、スケールとパフォーマンスのために、SQLPackage ユーティリティの使用をお勧めします。 BACPAC ファイルを使用した移行に関する SQL Server Customer Advisory Team のブログについては、「[Migrating from SQL Server to Azure SQL Database using BACPAC Files (BACPAC ファイルを使用した SQL Server から Azure SQL Database への移行)](https://blogs.msdn.microsoft.com/sqlcat/20../../migrating-from-sql-server-to-azure-sql-database-using-bacpac-files/)」を参照してください。

この例では、SqlPackage.exe と Active Directory ユニバーサル認証を使用してデータベースをエクスポートする方法を示します。

```cmd
SqlPackage.exe /a:Export /tf:testExport.bacpac /scs:"Data Source=apptestserver.database.windows.net;Initial Catalog=MyDB;" /ua:True /tid:"apptest.onmicrosoft.com"
```

## <a name="export-to-a-bacpac-file-using-sql-server-management-studio-ssms"></a>SQL Server Management Studio (SSMS) を使用して BACPAC ファイルにエクスポートする

最新バージョンの SQL Server Management Studio では、Azure SQL データベースを BACPAC ファイルにエクスポートするためのウィザードが提供されています。 [「データ層アプリケーションのエクスポート」](https://docs.microsoft.com/sql/relational-databases/data-tier-applications/export-a-data-tier-application)を参照してください。

## <a name="export-to-a-bacpac-file-using-powershell"></a>PowerShell を使用して BACPAC ファイルにエクスポートする

> [!NOTE]
> [マネージド インスタンス](sql-database-managed-instance.md)では現在、Azure PowerShell を使用して BACPAC ファイルにデータベースをエクスポートすることはサポートされていません。 マネージド インスタンスを BACPAC ファイルにエクスポートするには、SQL Server Management Studio または SQLPackage を使用します。

[New-AzSqlDatabaseExport](/powershell/module/az.sql/new-azsqldatabaseexport) コマンドレットを使用して、Azure SQL Database サービスにデータベース エクスポート要求を送信します。 データベースのサイズに応じて、エクスポート操作の完了に時間がかかる場合があります。

```powershell
$exportRequest = New-AzSqlDatabaseExport -ResourceGroupName $ResourceGroupName -ServerName $ServerName `
  -DatabaseName $DatabaseName -StorageKeytype $StorageKeytype -StorageKey $StorageKey -StorageUri $BacpacUri `
  -AdministratorLogin $creds.UserName -AdministratorLoginPassword $creds.Password
```

エクスポート要求の状態を確認するには、[Get-AzSqlDatabaseImportExportStatus](/powershell/module/az.sql/get-azsqldatabaseimportexportstatus) コマンドレットを使用します。 要求直後にこれを実行すると、通常は、**Status : InProgress** が返されます。 **Status:Succeeded** が表示された場合、エクスポートは完了しています。

```powershell
$exportStatus = Get-AzSqlDatabaseImportExportStatus -OperationStatusLink $exportRequest.OperationStatusLink
[Console]::Write("Exporting")
while ($exportStatus.Status -eq "InProgress")
{
    Start-Sleep -s 10
    $exportStatus = Get-AzSqlDatabaseImportExportStatus -OperationStatusLink $exportRequest.OperationStatusLink
    [Console]::Write(".")
}
[Console]::WriteLine("")
$exportStatus
```

## <a name="next-steps"></a>次の手順

- データベースをアーカイブ目的でエクスポートする方法の代わりとしての、単一データベースおよびプールされたデータベースの長期バックアップ リテンションについては、[長期バックアップ リテンション](sql-database-long-term-retention.md)に関する記事をご覧ください。 SQL エージェント ジョブを使用すれば、長期バックアップ リテンションの代替手段として、[コピーのみのデータベース バックアップ](https://docs.microsoft.com/sql/relational-databases/backup-restore/copy-only-backups-sql-server)をスケジュールすることができます。
- BACPAC ファイルを使用した移行に関する SQL Server Customer Advisory Team のブログについては、「[Migrating from SQL Server to Azure SQL Database using BACPAC Files (BACPAC ファイルを使用した SQL Server から Azure SQL Database への移行)](https://blogs.msdn.microsoft.com/sqlcat/2016/10/20/migrating-from-sql-server-to-azure-sql-database-using-bacpac-files/)」を参照してください。
- SQL Server データベースへの BACPAC のインポートについては、[「SQL Server データベースへの BACPAC のインポート」](https://msdn.microsoft.com/library/hh710052.aspx)を参照してください。
- SQL Server データベースから BACPAC をエクスポートする方法については、「[データ層アプリケーションのエクスポート](https://docs.microsoft.com/sql/relational-databases/data-tier-applications/export-a-data-tier-application)」をご覧ください
- データ移行サービスを使用してデータベースを移行する方法については、「[DMS を使用して SQL Server を Azure SQL Database にオフラインで移行する](../dms/tutorial-sql-server-to-azure-sql.md)」をご覧ください。
- Azure SQL Database への移行準備として SQL Server からエクスポートする場合は、[SQL Server データベースの Azure SQL Database への移行](sql-database-single-database-migrate.md)に関するページを参照してください。
- ストレージ キーと共有アクセス署名を管理および共有する方法については、「[Azure Storage セキュリティ ガイド](https://docs.microsoft.com/azure/storage/common/storage-security-guide)」をご覧ください。
