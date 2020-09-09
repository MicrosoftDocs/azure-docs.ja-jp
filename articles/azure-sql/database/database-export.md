---
title: Azure SQL データベースを BACPAC ファイルにエクスポートする (Azure portal)
titleSuffix: Azure SQL Database & Azure SQL Managed Instance
description: Azure portal を使用して BACPAC ファイルにデータベースをエクスポートします。
services: sql-database
ms.service: sql-db-mi
ms.subservice: data-movement
author: stevestein
ms.custom: sqldbrb=2
ms.author: sstein
ms.reviewer: carlrab
ms.date: 07/16/2019
ms.topic: conceptual
ms.openlocfilehash: 10d10ccfbd04a52f6d522700d66d2c959fed24ee
ms.sourcegitcommit: 93462ccb4dd178ec81115f50455fbad2fa1d79ce
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/06/2020
ms.locfileid: "85982418"
---
# <a name="export-to-a-bacpac-file---azure-sql-database-and-azure-sql-managed-instance"></a>BACPAC ファイルへのエクスポート - Azure SQL Database および Azure SQL Managed Instance

[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

アーカイブのため、または別のプラットフォームに移行するためにデータベースをエクスポートする必要がある際は、データベース スキーマとデータを [BACPAC](https://msdn.microsoft.com/library/ee210546.aspx#Anchor_4) ファイルにエクスポートできます。 BACPAC ファイルは、データベースのメタデータとデータを含む BACPAC の拡張子を持つ ZIP ファイルです。 BACPAC ファイルは、Azure Blob Storage またはオンプレミスの場所にあるローカル ストレージに格納でき、後で Azure SQL Database、Azure SQL Managed Instance、または SQL Server インスタンスにインポートすることができます。

## <a name="considerations"></a>考慮事項

- エクスポートにトランザクション一貫性を持たせるために、書き込みアクティビティがエクスポート中に行われないようにするか、データベースの[トランザクション上の一貫性が確保された](database-copy.md)コピーからエクスポートを行うようにする必要があります
- Blob Storage にエクスポートする場合、BACPAC ファイルの最大サイズは 200 GB です。 大きな BACPAC ファイルをアーカイブするには、ローカル ストレージにエクスポートします。
- この記事で説明されている方法を用いた Azure Premium Storage への BACPAC ファイルのエクスポートはサポートされていません。
- ファイアウォールの背後にある Storage は現在サポートされていません。
- エクスポート操作が 20 時間を超える場合は取り消されることがあります。 エクスポート中にパフォーマンスを向上させるには、次の操作を実行します。

  - コンピューティング サイズを一時的に増やします。
  - エクスポート中のすべての読み取りアクティビティと書き込みアクティビティを中止する。
  - すべての大きなテーブルに null 以外の値を持つ [クラスター化インデックス](https://msdn.microsoft.com/library/ms190457.aspx) を使用する。 クラスター化インデックスがないと、エクスポートが 6 ～ 12 時間よりも時間が長くかかる場合には失敗することがあります。 これは、エクスポート サービスがテーブル スキャンを実行してテーブル全体をエクスポートしようとする必要があることが原因です。 テーブルがエクスポート向けに最適化されているかを判断するための適切な方法として、**DBCC SHOW_STATISTICS** を実行し、*RANGE_HI_KEY* が null 以外の値であり、分布が適切であることを確認する方法があります。 詳細については、「[DBCC SHOW_STATISTICS](https://msdn.microsoft.com/library/ms174384.aspx)」を参照してください。

> [!NOTE]
> BACPAC はバックアップおよび復元操作に使用するためのものでありません。 Azure では、すべてのユーザー データベースのバックアップが自動的に作成されます。 詳しくは、[ビジネス継続性の概要](business-continuity-high-availability-disaster-recover-hadr-overview.md)に関するページと、[SQL Database のバックアップ](automated-backups-overview.md)に関するページをご覧ください。

## <a name="the-azure-portal"></a>Azure ポータル

Azure portal を使用して [Azure SQL Managed Instance](../managed-instance/sql-managed-instance-paas-overview.md) からデータベースの BACPAC をエクスポートすることは現在サポートされていません。 代わりに、SQL Server Management Studio または SQLPackage を使用してください。

> [!NOTE]
> Azure portal または PowerShell から送信されたインポート/エクスポート要求を処理するマシンは、BACPAC ファイルとデータ層アプリケーション フレームワーク (DacFX) によって生成された一時ファイルを格納する必要があります。 必要なディスク領域は、同じサイズのデータベースでも大きく異なります。データベースのサイズの最大 3 倍のディスク領域が必要になることがあります。 インポート/エクスポート要求を実行するマシンには、450 GB のローカル ディスク領域しかありません。 その結果、一部の要求がエラー `There is not enough space on the disk` で失敗することがあります。 この場合の回避策は、十分なローカル ディスク領域を持つマシンで sqlpackage.exe を実行することです。 150 GB を超えるデータベースをインポート/エクスポートする場合は、[SqlPackage](#sqlpackage-utility) を使用してこの問題を回避することをお勧めします。

1. [Azure Portal](https://portal.azure.com) を使用してデータベースをエクスポートするには、データベースのページを開き、ツールバーの **[エクスポート]** をクリックします。

   ![データベースのエクスポート](./media/database-export/database-export1.png)

2. BACPAC ファイル名を指定し、エクスポートに使用する既存の Azure ストレージ アカウントとコンテナーを選択した後、ソース データベースにアクセスするための適切な資格情報を指定します。 ここでは、Azure 管理者であっても SQL **サーバー管理者ログイン**が必要になります。Azure 管理者であることと、Azure SQL Database または Azure SQL Managed Instance で管理者のアクセス許可を持っていることは同じではないためです。

    ![データベースのエクスポート](./media/database-export/database-export2.png)

3. **[OK]** をクリックします。

4. エクスポート操作の進行状況を監視するには、エクスポートされたデータベースを含むサーバーのページを開きます。 **[設定]** の下で、 **[インポート/エクスポート履歴]** をクリックします。

   ![エクスポート履歴](./media/database-export/export-history.png)

## <a name="sqlpackage-utility"></a>SQLPackage ユーティリティ

[SqlPackage](https://docs.microsoft.com/sql/tools/sqlpackage) コマンドライン ユーティリティを使用して SQL Database のデータベースをエクスポートするには、「[エクスポートのパラメーターとプロパティ](https://docs.microsoft.com/sql/tools/sqlpackage#export-parameters-and-properties)」を参照してください。 SQLPackage ユーティリティは、最新バージョンの [SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx) および [SQL Server Data Tools for Visual Studio](https://msdn.microsoft.com/library/mt204009.aspx) に付属しています。また、Microsoft ダウンロード センターから最新バージョンの [SqlPackage](https://www.microsoft.com/download/details.aspx?id=53876) を直接ダウンロードすることもできます。

ほとんどの運用環境では、スケールとパフォーマンスのために、SQLPackage ユーティリティの使用をお勧めします。 BACPAC ファイルを使用した移行に関する SQL Server Customer Advisory Team のブログについては、「[Migrating from SQL Server to Azure SQL Database using BACPAC Files (BACPAC ファイルを使用した SQL Server から Azure SQL Database への移行)](https://blogs.msdn.microsoft.com/sqlcat/20../../migrating-from-sql-server-to-azure-sql-database-using-bacpac-files/)」を参照してください。

この例では、SqlPackage.exe と Active Directory ユニバーサル認証を使用してデータベースをエクスポートする方法を示します。

```cmd
SqlPackage.exe /a:Export /tf:testExport.bacpac /scs:"Data Source=apptestserver.database.windows.net;Initial Catalog=MyDB;" /ua:True /tid:"apptest.onmicrosoft.com"
```

## <a name="sql-server-management-studio-ssms"></a>SQL Server Management Studio (SSMS)

最新バージョンの SQL Server Management Studio には、Azure SQL Database のデータベースまたは SQL Managed Instance データベースを BACPAC ファイルにエクスポートするウィザードが用意されています。 [「データ層アプリケーションのエクスポート」](https://docs.microsoft.com/sql/relational-databases/data-tier-applications/export-a-data-tier-application)を参照してください。

## <a name="powershell"></a>PowerShell

> [!NOTE]
> [Azure SQL Managed Instance](../managed-instance/sql-managed-instance-paas-overview.md) では現在、Azure PowerShell を使用して BACPAC ファイルにデータベースをエクスポートすることはサポートされていません。 マネージド インスタンスを BACPAC ファイルにエクスポートするには、SQL Server Management Studio または SQLPackage を使用します。

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

## <a name="next-steps"></a>次のステップ

- アーカイブ目的でデータベースをエクスポートする方法の代わりとしての、単一データベースおよびプールされたデータベースの長期的なバックアップ保有期間については、[長期的なバックアップ保有期間](long-term-retention-overview.md)に関するページを参照してください。 SQL エージェント ジョブを使用すれば、長期バックアップ リテンションの代替手段として、[コピーのみのデータベース バックアップ](https://docs.microsoft.com/sql/relational-databases/backup-restore/copy-only-backups-sql-server)をスケジュールすることができます。
- BACPAC ファイルを使用した移行に関する SQL Server Customer Advisory Team のブログについては、「[Migrating from SQL Server to Azure SQL Database using BACPAC Files (BACPAC ファイルを使用した SQL Server から Azure SQL Database への移行)](https://blogs.msdn.microsoft.com/sqlcat/2016/10/20/migrating-from-sql-server-to-azure-sql-database-using-bacpac-files/)」を参照してください。
- SQL Server データベースへの BACPAC のインポートについては、[「SQL Server データベースへの BACPAC のインポート」](https://msdn.microsoft.com/library/hh710052.aspx)を参照してください。
- SQL Server データベースから BACPAC をエクスポートする方法については、「[データ層アプリケーションのエクスポート](https://docs.microsoft.com/sql/relational-databases/data-tier-applications/export-a-data-tier-application)」をご覧ください
- Database Migration Service を使用してデータベースを移行する方法については、[DMS を使用した SQL Server から Azure SQL Database へのオフライン移行](../../dms/tutorial-sql-server-to-azure-sql.md)に関するページを参照してください。
- Azure SQL Database への移行準備として SQL Server からエクスポートする場合は、[SQL Server データベースの Azure SQL Database への移行](migrate-to-database-from-sql-server.md)に関するページを参照してください。
- ストレージ キーと共有アクセス署名を管理および共有する方法については、「[Azure Storage セキュリティ ガイド](https://docs.microsoft.com/azure/storage/common/storage-security-guide)」をご覧ください。
