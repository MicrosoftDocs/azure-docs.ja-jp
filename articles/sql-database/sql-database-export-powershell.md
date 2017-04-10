---
title: "PowerShell: Azure SQL Database を BACPAC ファイルにエクスポートする | Microsoft Docs"
description: "PowerShell を使って BACPAC ファイルに Azure SQL Database をエクスポートする"
services: sql-database
documentationcenter: 
author: stevestein
manager: jhubbard
editor: 
ms.assetid: 9439dd83-812f-4688-97ea-2a89a864d1f3
ms.service: sql-database
ms.custom: migrate and move
ms.devlang: NA
ms.date: 02/07/2017
ms.author: sstein
ms.workload: data-management
ms.topic: article
ms.tgt_pltfrm: NA
translationtype: Human Translation
ms.sourcegitcommit: 503f5151047870aaf87e9bb7ebf2c7e4afa27b83
ms.openlocfilehash: 41774c7f1c038ee017d719e59ed79fb68999dac8
ms.lasthandoff: 03/29/2017


---
# <a name="export-an-azure-sql-database-or-a-sql-server-to-a-bacpac-file-by-using-powershell"></a>PowerShell または SQL Server を使って BACPAC ファイルに Azure SQL Database をエクスポートする

この記事では PowerShell を使って (Azure Blob Storage に格納されている) BACPAC ファイルに Azure SQL Database または SQL Server データベースをエクスポートする方法について説明します。 BACPAC ファイルへのエクスポートの概要については、[BACPAC へのエクスポート](sql-database-export.md)に関する記事をご覧ください。

> [!NOTE]
> また、[Azure Portal](sql-database-export-portal.md)、[SQL Server Management Studio](sql-database-export-ssms.md) または [SQLPackage](sql-database-export-sqlpackage.md) を使用して、Azure SQL データベース フィルを BACPAC ファイルにエクスポートすることもできます。
>

## <a name="prerequisites"></a>前提条件

この記事を完了するには、以下が必要です。

* Azure サブスクリプション。
* Azure SQL Database。
* [Azure Standard Storage アカウント](../storage/storage-create-storage-account.md)(標準的なストレージに BACPAC を格納する BLOB コンテナーを含む)。

[!INCLUDE [Start your PowerShell session](../../includes/sql-database-powershell.md)]

## <a name="export-your-database"></a>データベースのエクスポート
[New-AzureRmSqlDatabaseExport](https://docs.microsoft.com/powershell/resourcemanager/azurerm.sql/v2.7.0/new-azurermsqldatabaseexport) コマンドレットは、サービスに対してデータベースのエクスポート要求を送信します。 データベースのサイズに応じて、エクスポート操作の完了に時間がかかる場合があります。

> [!IMPORTANT]
> トランザクションに関する BACPAC ファイルの一貫性を保証するには、最初に [データベースのコピーを作成](scripts/sql-database-copy-database-to-new-server-powershell.md)してエクスポートする必要があります。
> 
> 

     $exportRequest = New-AzureRmSqlDatabaseExport -ResourceGroupName $ResourceGroupName -ServerName $ServerName `
       -DatabaseName $DatabaseName -StorageKeytype $StorageKeytype -StorageKey $StorageKey -StorageUri $BacpacUri `
       -AdministratorLogin $creds.UserName -AdministratorLoginPassword $creds.Password


## <a name="monitor-the-progress-of-the-export-operation"></a>エクスポート操作の進行状況の監視
[New-AzureRmSqlDatabaseExport](https://docs.microsoft.com//powershell/resourcemanager/azurerm.sql/v2.7.0/new-azurermsqldatabaseexport) の実行後に、[Get-AzureRmSqlDatabaseImportExportStatus](https://docs.microsoft.com/powershell/resourcemanager/azurerm.sql/v2.7.0/get-azurermsqldatabaseimportexportstatus) を実行して要求の状態を確認できます。 要求直後にこれを実行すると、通常は、 **Status : InProgress**が返されます。 **Status : Succeeded** が表示された場合、エクスポートは完了しています。

    Get-AzureRmSqlDatabaseImportExportStatus -OperationStatusLink $exportRequest.OperationStatusLink



## <a name="export-sql-database-example"></a>SQL Database のエクスポート例
次の例では、既存の SQL Database を BACPAC にエクスポートしてから、エクスポート操作の状態を確認する方法を示します。

この例を実行する場合、いくつかの変数を、使用するデータベースとストレージ アカウントの固有の値に置き換える必要があります。 ストレージ アカウント名、BLOB コンテナー名、およびキーの値を取得するには、 [Azure ポータル](https://portal.azure.com)でストレージ アカウントを参照します。 キーは、[ストレージ アカウント] ブレードにある **[アクセス キー]** をクリックして検索できます。

以下の `VARIABLE-VALUES` を Azure リソース固有の値に置換します。 データベース名は、エクスポートする既存のデータベースになります。

    $subscriptionId = "YOUR AZURE SUBSCRIPTION ID"

    Login-AzureRmAccount
    Set-AzureRmContext -SubscriptionId $subscriptionId

    # Database to export
    $DatabaseName = "DATABASE-NAME"
    $ResourceGroupName = "RESOURCE-GROUP-NAME"
    $ServerName = "SERVER-NAME"
    $serverAdmin = "ADMIN-NAME"
    $serverPassword = "ADMIN-PASSWORD" 
    $securePassword = ConvertTo-SecureString -String $serverPassword -AsPlainText -Force
    $creds = New-Object -TypeName System.Management.Automation.PSCredential -ArgumentList $serverAdmin, $securePassword

    # Generate a unique filename for the BACPAC
    $bacpacFilename = $DatabaseName + (Get-Date).ToString("yyyyMMddHHmm") + ".bacpac"

    # Storage account info for the BACPAC
    $BaseStorageUri = "https://STORAGE-NAME.blob.core.windows.net/BLOB-CONTAINER-NAME/"
    $BacpacUri = $BaseStorageUri + $bacpacFilename
    $StorageKeytype = "StorageAccessKey"
    $StorageKey = "YOUR STORAGE KEY"

    $exportRequest = New-AzureRmSqlDatabaseExport -ResourceGroupName $ResourceGroupName -ServerName $ServerName `
       -DatabaseName $DatabaseName -StorageKeytype $StorageKeytype -StorageKey $StorageKey -StorageUri $BacpacUri `
       -AdministratorLogin $creds.UserName -AdministratorLoginPassword $creds.Password
    $exportRequest

    # Check status of the export
    Get-AzureRmSqlDatabaseImportExportStatus -OperationStatusLink $exportRequest.OperationStatusLink

## <a name="automate-export-using-azure-automation"></a>Azure Automation を使用してエクスポートを自動化する

Azure SQL Database の自動エクスポート機能は現在プレビュー段階であり、2017 年 3 月 1 日に廃止されます。 2016 年 12 月 1 日から、SQL データベースの自動エクスポートを構成することはできなくなります。 既存のエクスポート ジョブは、引き続き 2017 年 3 月 1 日まで実行されます。 2016 年 12 月 1 日以降は、[長期のバックアップ リテンション期間](sql-database-long-term-retention.md)または [Azure Automation](../automation/automation-intro.md) を使用してください。PowerShell を使用して、選択したスケジュールに従って定期的に SQL データベースをアーカイブできます。 サンプル スクリプトは [GitHub からダウンロード](https://github.com/Microsoft/sql-server-samples/tree/master/samples/manage/azure-automation-automated-export)できます。 


## <a name="next-steps"></a>次のステップ
* Powershell を使用して Azure SQL Database をインポートする方法については、 [PowerShell を使用した BACPAC のインポート](scripts/sql-database-import-from-bacpac-powershell.md)に関するページをご覧ください。
* SQLPackage を使用した BACPAC のインポートについては、「[Import a BACPAC to Azure SQL Database using SqlPackage](sql-database-import-sqlpackage.md)」(SqlPackage を使用した Azure SQL Database への BACPAC のインポート) をご覧ください。
* Azure Portal を使用した BACPAC のインポートについては、「[Import a BACPAC to Azure SQL Database using the Azure portal](sql-database-import-portal.md)」(Azure Portal を使用した Azure SQL Database への BACPAC のインポート) をご覧ください。
* パフォーマンスの推奨事項も含む、SQL Server データベースの移行プロセス全体の詳細については、[Azure SQL Database への SQL Server データベースの移行](sql-database-cloud-migrate.md)に関するページを参照してください。
* データベースをアーカイブ目的でエクスポートする方法の代わりとしての、Azure SQL データベース バックアップの長期バックアップ リテンション期間については、[長期バックアップ リテンション期間](sql-database-long-term-retention.md)に関する記事をご覧ください。
* SQL Server データベースへの BACPAC のインポートについては、 [SQL Server データベースへの BACPAC のインポート](https://msdn.microsoft.com/library/hh710052.aspx)



## <a name="additional-resources"></a>その他のリソース
* [New-AzureRmSqlDatabaseExport](https://docs.microsoft.com/powershell/resourcemanager/azurerm.sql/v2.7.0/new-azurermsqldatabaseexport)
* [Get-AzureRmSqlDatabaseImportExportStatus](https://docs.microsoft.com/powershell/resourcemanager/azurerm.sql/v2.7.0/get-azurermsqldatabaseimportexportstatus)


