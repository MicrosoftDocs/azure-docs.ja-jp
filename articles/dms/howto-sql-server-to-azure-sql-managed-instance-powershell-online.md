---
title: PowerShell:SQL Server から SQL Managed Instance にオンラインで移行する
titleSuffix: Azure Database Migration Service
description: Azure PowerShell と Azure Database Migration Service を使用して、SQL Server から Azure SQL Managed Instance にオンラインで移行する方法について説明します。
services: database-migration
author: pochiraju
ms.author: rajpo
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: ''
ms.topic: how-to
ms.date: 12/16/2020
ms.openlocfilehash: 7f09db2e1f98d48e91dfea2642969ff4ca360967
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "98697743"
---
# <a name="migrate-sql-server-to-sql-managed-instance-online-with-powershell--azure-database-migration-service"></a>PowerShell と Azure Database Migration Service を使用して SQL Server から SQL Managed Instance にオンラインで移行する

この記事では、Microsoft Azure PowerShell を使用して、SQL Server 2005 以上のオンプレミス インスタンスに復元された **Adventureworks2016** データベースを Azure SQL の SQL Managed Instance にオンラインで移行します。 Microsoft Azure PowerShell の `Az.DataMigration` モジュールを使用して、SQL Server インスタンスから SQL マネージド インスタンスにデータベースを移行できます。

この記事では、次の方法について説明します。
> [!div class="checklist"]
>
> * リソース グループを作成します。
> * Azure Database Migration Service のインスタンスを作成する。
> * Azure Database Migration Service のインスタンスで移行プロジェクトを作成する。
> * オンラインで移行を実行します。

[!INCLUDE [online-offline](../../includes/database-migration-service-offline-online.md)]

この記事では、オンライン移行の手順について説明しますが、[オフラインで](howto-sql-server-to-azure-sql-managed-instance-powershell-offline.md)移行することもできます。


## <a name="prerequisites"></a>前提条件

これらの手順を完了するには、以下が必要です。

* [SQL Server 2016 以上](https://www.microsoft.com/sql-server/sql-server-downloads) (任意のエディション)。
* **AdventureWorks2016** データベースのローカル コピー ([こちら](/sql/samples/adventureworks-install-configure)からダウンロードできます)。
* TCP/IP プロトコルを有効にする (SQL Server Express のインストールでは既定で無効になっています)。 [サーバー ネットワーク プロトコルの有効化または無効化](/sql/database-engine/configure-windows/enable-or-disable-a-server-network-protocol#SSMSProcedure)に関する記事の説明に従って、TCP/IP プロトコルを有効にする。
* [データベース エンジン アクセス用の Windows Firewall](/sql/database-engine/configure-windows/configure-a-windows-firewall-for-database-engine-access) の構成。
* Azure サブスクリプション。 お持ちでない場合は、開始する前に[無料アカウントを作成](https://azure.microsoft.com/free/)してください。
* SQL マネージド インスタンス。 [SQL マネージド インスタンスの作成](../azure-sql/managed-instance/instance-create-quickstart.md)に関する記事にある詳しい手順に従って、SQL マネージド インスタンスを作成できます。
* [Data Migration Assistant](https://www.microsoft.com/download/details.aspx?id=53595) v3.3 以降をダウンロードしてインストールする。
* [ExpressRoute](../expressroute/expressroute-introduction.md) または [VPN](../vpn-gateway/vpn-gateway-about-vpngateways.md) を使用してオンプレミス ソース サーバーへのサイト間接続を Azure Database Migration Service に提供する、Azure Resource Manager デプロイ モデルを使用して作成された Microsoft Azure 仮想ネットワーク。
* [SQL Server の移行評価の実行](/sql/dma/dma-assesssqlonprem)に関する記事で説明されているように、Data Migration Assistant を使用してオンプレミスのデータベースおよびスキーマの移行の評価を完了していること。
* `Az.DataMigration` モジュール (バージョン 0.7.2 以降) を PowerShell ギャラリーからダウンロードし、[Install-Module PowerShell cmdlet](/powershell/module/powershellget/Install-Module) コマンドレットを使用してインストールする。
* ソースの SQL Server インスタンスへの接続に使用される資格情報に、[CONTROL SERVER](/sql/t-sql/statements/grant-server-permissions-transact-sql) アクセス許可が含まれていることを確認する。
* ターゲットの SQL マネージド インスタンスへの接続に使用される資格情報に、ターゲットの SQL マネージド インスタンス データベースに対する CONTROL DATABASE アクセス許可が含まれていることを確認する。

    > [!IMPORTANT]
    > オンライン移行の場合は、Azure Active Directory の資格情報を既に設定してある必要があります。 詳細については、[リソースにアクセスできる Azure AD アプリケーションとサービス プリンシパルをポータルを使用して作成する方法](../active-directory/develop/howto-create-service-principal-portal.md)に関する記事を参照してください。

## <a name="create-a-resource-group"></a>リソース グループを作成する

Azure リソース グループとは、Azure リソースのデプロイと管理に使用する論理コンテナーです。

[`New-AzResourceGroup`](/powershell/module/az.resources/new-azresourcegroup) コマンドを使用してリソース グループを作成します。

次の例では、*myResourceGroup* という名前のリソース グループを "*米国東部*" リージョンに作成します。

```powershell
New-AzResourceGroup -ResourceGroupName myResourceGroup -Location EastUS
```

## <a name="create-an-instance-of-dms"></a>DMS のインスタンスを作成します。

新しい Azure Database Migration Service インスタンスを作成するには、`New-AzDataMigrationService` コマンドレットを使用します。
このコマンドレットでは、次のパラメーターが必要です。

* *Azure リソース グループ名*。 [`New-AzResourceGroup`](/powershell/module/az.resources/new-azresourcegroup) コマンドを使用して前述の Azure リソース グループを作成し、パラメーターとしてその名前を指定できます。
* *サービス名*。 Azure Database Migration Service に使用する一意のサービス名に対応する文字列。
* *場所*。 サービスの場所を指定します。 米国西部や東南アジアなど、Azure データ センターの場所を指定してください。
* *SKU*。 このパラメーターは、DMS SKU 名に対応します。 現在サポートされている SKU 名は、*Basic_1vCore*、*Basic_2vCores*、*GeneralPurpose_4vCores* です。
* *仮想サブネット識別子*。 [`New-AzVirtualNetworkSubnetConfig`](/powershell/module/az.network/new-azvirtualnetworksubnetconfig) コマンドレットを使用して、サブネットを作成できます。

次の例では、*MyVNET* という仮想ネットワークと *MySubnet* というサブネットを使用して、"*米国東部*" リージョンにあるリソース グループ *MyDMSResourceGroup* に *MyDMS* という名前のサービスを作成します。


```powershell
$vNet = Get-AzVirtualNetwork -ResourceGroupName MyDMSResourceGroup -Name MyVNET

$vSubNet = Get-AzVirtualNetworkSubnetConfig -VirtualNetwork $vNet -Name MySubnet

$service = New-AzDms -ResourceGroupName myResourceGroup `
  -ServiceName MyDMS `
  -Location EastUS `
  -Sku Basic_2vCores `  
  -VirtualSubnetId $vSubNet.Id`
```

## <a name="create-a-migration-project"></a>移行プロジェクトを作成する

Azure Database Migration Service インスタンスを作成した後、移行プロジェクトを作成します。 Azure Database Migration Service プロジェクトでは、ソースとターゲットの両方のインスタンスの接続情報に加え、プロジェクトの一部として移行するデータベースの一覧が必要です。
ソースとターゲットの接続の接続文字列を定義します。

次のスクリプトでは、ソースの SQL Server 接続の詳細を定義しています。 

```powershell
# Source connection properties
$sourceDataSource = "<mysqlserver.domain.com/privateIP of source SQL>"
$sourceUserName = "domain\user"
$sourcePassword = "mypassword"
```

次のスクリプトでは、ターゲットの SQL Managed Instance 接続の詳細を定義しています。 

```powershell
# Target MI connection properties
$targetMIResourceId = "/subscriptions/<subid>/resourceGroups/<rg>/providers/Microsoft.Sql/managedInstances/<myMI>"
$targetUserName = "<user>"
$targetPassword = "<password>"
```



### <a name="define-source-and-target-database-mapping"></a>ソースとターゲットのデータベース マッピングを定義する

この移行プロジェクトで移行するデータベースを指定します

次のスクリプトでは、ソース データベースを、指定された名前のターゲット SQL Managed Instance 上のそれぞれの新しいデータベースにマップします。

```powershell
# Selected databases (Source database name to target database name mapping)
$selectedDatabasesMap = New-Object System.Collections.Generic.Dictionary"[String,String]" 
$selectedDatabasesMap.Add("<source  database name>", "<target database name> ")
```

複数のデータベースの場合は、次の形式を使用して、データベースの一覧を上記のスクリプトに追加します。

```powershell
$selectedDatabasesMap = New-Object System.Collections.Generic.Dictionary"[String,String]" 
$selectedDatabasesMap.Add("<source  database name1>", "<target database name1> ")
$selectedDatabasesMap.Add("<source  database name2>", "<target database name2> ")
```

### <a name="create-dms-project"></a>DMS のプロジェクトを作成する

DMS インスタンス内に Azure Database Migration Service プロジェクトを作成できます。

```powershell
# Create DMS project
$project = New-AzDataMigrationProject `
  -ResourceGroupName $dmsResourceGroupName `
  -ServiceName $dmsServiceName `
  -ProjectName $dmsProjectName `
  -Location $dmsLocation `
  -SourceType SQL `
  -TargetType SQLMI `

# Create selected databases object
$selectedDatabases = @();
foreach ($sourceDbName in $selectedDatabasesMap.Keys){
    $targetDbName = $($selectedDatabasesMap[$sourceDbName])
    $selectedDatabases += New-AzDmsSelectedDB -MigrateSqlServerSqlDbMi `
      -Name $sourceDbName `
      -TargetDatabaseName $targetDbName `
      -BackupFileShare $backupFileShare `
}
```



### <a name="create-a-backup-fileshare-object"></a>バックアップ用 FileShare オブジェクトを作成する

ここで、New-AzDmsFileShare コマンドレットを使用して、Azure Database Migration Service がソース データベースのバックアップを作成できるローカル SMB ネットワーク共有を表す FileShare オブジェクトを作成します。

```powershell
# SMB Backup share properties
$smbBackupSharePath = "\\shareserver.domain.com\mybackup"
$smbBackupShareUserName = "domain\user"
$smbBackupSharePassword = "<password>"

# Create backup file share object
$smbBackupSharePasswordSecure = ConvertTo-SecureString -String $smbBackupSharePassword -AsPlainText -Force
$smbBackupShareCredentials = New-Object System.Management.Automation.PSCredential ($smbBackupShareUserName, $smbBackupSharePasswordSecure)
$backupFileShare = New-AzDmsFileShare -Path $smbBackupSharePath -Credential $smbBackupShareCredentials
```

### <a name="define-the-azure-storage"></a>Azure Storage を定義する 

移行に使用する Azure Storage コンテナーを選択します。 

```powershell
# Storage resource id
$storageAccountResourceId = "/subscriptions/<subscriptionname>/resourceGroups/<rg>/providers/Microsoft.Storage/storageAccounts/<mystorage>"
```


### <a name="configure-azure-active-directory-app"></a>Azure Active Directory アプリを構成する

オンラインでの SQL Managed Instance の移行のために Azure Active Directory の必要な詳細を入力します。 

```powershell
# AAD properties
$AADAppId = "<appid-guid>"
$AADAppKey = "<app-key>"

# Create AAD object
$AADAppKeySecure = ConvertTo-SecureString $AADAppKey -AsPlainText -Force
$AADApp = New-AzDmsAadApp -ApplicationId $AADAppId -AppKey $AADAppKeySecure
```


## <a name="create-and-start-a-migration-task"></a>移行タスクを作成して開始する

次に、Azure Database Migration Service タスクを作成して開始します。 変数を使用してソースとターゲットを呼び出し、移行するデータベース テーブルを一覧表示します。 


```powershell
# Managed Instance online migration properties
$dmsTaskName = "testmigration1"

# Create source connection info
$sourceConnInfo = New-AzDmsConnInfo -ServerType SQL `
  -DataSource $sourceDataSource `
  -AuthType WindowsAuthentication `
  -TrustServerCertificate:$true
$sourcePasswordSecure = ConvertTo-SecureString -String $sourcePassword -AsPlainText -Force
$sourceCredentials = New-Object System.Management.Automation.PSCredential ($sourceUserName, $sourcePasswordSecure)

# Create target connection info
$targetConnInfo = New-AzDmsConnInfo -ServerType SQLMI `
    -MiResourceId $targetMIResourceId
$targetPasswordSecure = ConvertTo-SecureString -String $targetPassword -AsPlainText -Force
$targetCredentials = New-Object System.Management.Automation.PSCredential ($targetUserName, $targetPasswordSecure)
```

次の例では、オンライン移行タスクを作成して開始します。 

```powershell
# Create DMS migration task
$migTask = New-AzDataMigrationTask -TaskType MigrateSqlServerSqlDbMiSync `
  -ResourceGroupName $dmsResourceGroupName `
  -ServiceName $dmsServiceName `
  -ProjectName $dmsProjectName `
  -TaskName $dmsTaskName `
  -SourceConnection $sourceConnInfo `
  -SourceCred $sourceCredentials `
  -TargetConnection $targetConnInfo `
  -TargetCred $targetCredentials `
  -SelectedDatabase  $selectedDatabases `
  -BackupFileShare $backupFileShare `
  -AzureActiveDirectoryApp $AADApp `
  -StorageResourceId $storageAccountResourceId
```

詳細については、「[New-AzDataMigrationTask](/powershell/module/az.datamigration/new-azdatamigrationtask)」を参照してください。

## <a name="monitor-the-migration"></a>移行を監視する

移行を監視するには、次のタスクを実行します。

### <a name="check-the-status-of-task"></a>タスクの状態を確認する

```powershell
# Get migration task status details
$migTask = Get-AzDataMigrationTask `
                    -ResourceGroupName $dmsResourceGroupName `
                    -ServiceName $dmsServiceName `
                    -ProjectName $dmsProjectName `
                    -Name $dmsTaskName `
                    -ResultType DatabaseLevelOutput `
                    -Expand

# Task state will be either of 'Queued', 'Running', 'Succeeded', 'Failed', 'FailedInputValidation' or 'Faulted'
$taskState = $migTask.ProjectTask.Properties.State

# Display task state
$taskState | Format-List
```

エラーの一覧を取得するには、次を使用します。

```powershell
# Get task errors
$taskErrors = $migTask.ProjectTask.Properties.Errors

# Display task errors
foreach($taskError in $taskErrors){
    $taskError |  Format-List
}


# Get database level details
$databaseLevelOutputs = $migTask.ProjectTask.Properties.Output

# Display database level details
foreach($databaseLevelOutput in $databaseLevelOutputs){

    # This is the source database name.
    $databaseName = $databaseLevelOutput.SourceDatabaseName;

    Write-Host "=========="
    Write-Host "Start migration details for database " $databaseName
    # This is the status for that database - It will be either of:
    # INITIAL, FULL_BACKUP_UPLOADING, FULL_BACKUP_UPLOADED, LOG_FILES_UPLOADING,
    # CUTOVER_IN_PROGRESS, CUTOVER_INITIATED, CUTOVER_COMPLETED, COMPLETED, CANCELLED, FAILED
    $databaseMigrationState = $databaseLevelOutput.MigrationState;

    # Details about last restored backup. This contains file names, LSN, backup date, etc 
    $databaseLastRestoredBackup = $databaseLevelOutput.LastRestoredBackupSetInfo
        
    # Details about last restored backup. This contains file names, LSN, backup date, etc 
    $databaseLastRestoredBackup = $databaseLevelOutput.LastRestoredBackupSetInfo

    # Details about last Currently active/most recent backups. This contains file names, LSN, backup date, etc 
    $databaseActiveBackpusets = $databaseLevelOutput.ActiveBackupSets

    # Display info
    $databaseLevelOutput | Format-List

    Write-Host "Currently active/most recent backupset details:"
    $databaseActiveBackpusets  | select BackupStartDate, BackupFinishedDate, FirstLsn, LastLsn -ExpandProperty ListOfBackupFiles | Format-List

    Write-Host "Last restored backupset details:"
    $databaseLastRestoredBackupFiles  | Format-List

    Write-Host "End migration details for database " $databaseName
    Write-Host "=========="
}
```

## <a name="performing-the-cutover"></a>カットオーバーの実行 

オンライン移行では、データベースの完全バックアップと復元が実行された後、BackupFileShare に格納されているトランザクション ログの復元処理が実行されます。

Azure SQL マネージド インスタンス内のデータベースが最新のデータで更新され、ソース データベースと同期されている場合、一括での実行が可能です。

次の例では、一括移行を実行しています。 ユーザーは、それぞれの判断でこのコマンドを呼び出します。

```powershell
$command = Invoke-AzDmsCommand -CommandType CompleteSqlMiSync `
                               -ResourceGroupName myResourceGroup `
                               -ServiceName $service.Name `
                               -ProjectName $project.Name `
                               -TaskName myDMSTask `
                               -DatabaseName "Source DB Name"
```

## <a name="deleting-the-instance-of-azure-database-migration-service"></a>Azure Database Migration Service のインスタンスの削除

移行が完了したら、Azure Database Migration Service インスタンスを削除できます。

```powershell
Remove-AzDms -ResourceGroupName myResourceGroup -ServiceName MyDMS
```

## <a name="additional-resources"></a>その他のリソース

追加の移行シナリオ (ソースとターゲットのペア) については、Microsoft の[データベース移行ガイド](https://datamigration.microsoft.com/)に関するページを参照してください。

## <a name="next-steps"></a>次のステップ

「[Azure Database Migration Service とは](./dms-overview.md)」の記事で、Azure Database Migration Service の詳細を確認します。