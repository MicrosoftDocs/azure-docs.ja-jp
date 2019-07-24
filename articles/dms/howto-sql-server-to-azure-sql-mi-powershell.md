---
title: Database Migration Service および PowerShell を使用して SQL Server を Azure SQL Database Managed Instance に移行する | Microsoft Docs
description: Azure PowerShell を使用してオンプレミスの SQL Server から Azure SQL DB Managed Instance に移行する方法について説明します。
services: database-migration
author: HJToland3
ms.author: jtoland
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: mvc
ms.topic: article
ms.date: 03/12/2019
ms.openlocfilehash: ed0d65a0f00bd5ebc3227a249beec6bafd791347
ms.sourcegitcommit: 02d17ef9aff49423bef5b322a9315f7eab86d8ff
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/21/2019
ms.locfileid: "58335931"
---
# <a name="migrate-sql-server-on-premises-to-azure-sql-database-managed-instance-using-azure-powershell"></a>Azure PowerShell を使用してオンプレミスの SQL Server を Azure SQL Database Managed Instance に移行する
この記事では、Microsoft Azure PowerShell を使用して、SQL Server 2005 以上のオンプレミス インスタンスに復元された **Adventureworks2012** データベースを Azure SQL Database Managed Instance に移行します。 データベースをオンプレミスの SQL Server インスタンスから Azure SQL Database Managed Instance に移行するには、Microsoft Azure PowerShell で `Az.DataMigration` モジュールを使用します。

この記事では、次のことについて説明します。
> [!div class="checklist"]
> * リソース グループを作成します。
> * Azure Database Migration Service のインスタンスを作成する。
> * Azure Database Migration Service インスタンスで移行プロジェクトを作成します。
> * 移行を実行する。

## <a name="prerequisites"></a>前提条件
これらの手順を完了するには、以下が必要です。

- [SQL Server 2016 以上](https://www.microsoft.com/sql-server/sql-server-downloads) (任意のエディション)
- TCP/IP プロトコルを有効にする (SQL Server Express のインストールでは既定で無効になっています)。 [サーバー ネットワーク プロトコルの有効化または無効化](https://docs.microsoft.com/sql/database-engine/configure-windows/enable-or-disable-a-server-network-protocol#SSMSProcedure)に関する記事の説明に従って、TCP/IP プロトコルを有効にする。
- [データベース エンジン アクセス用の Windows Firewall](https://docs.microsoft.com/sql/database-engine/configure-windows/configure-a-windows-firewall-for-database-engine-access) の構成。
- Azure SQL データベース インスタンス。 Azure SQL データベース インスタンスを作成するには、「[Azure Portal で Azure SQL データベースを作成する](https://docs.microsoft.com/azure/sql-database/sql-database-get-started-portal)」にある手順に従ってください。
- [Data Migration Assistant](https://www.microsoft.com/download/details.aspx?id=53595) v3.3 以降。
- Azure Resource Manager デプロイ モデルを使用して VNET を作成する。これにより、[ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) または [VPN](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways) を使用して、Azure Database Migration Service に、オンプレミス ソース サーバーへのサイト間接続が提供されます。
- [SQL Server の移行評価の実行](https://docs.microsoft.com/sql/dma/dma-assesssqlonprem)に関する記事に従って、Data Migration Assistant を使用して、オンプレミスのデータベースおよびスキーマの移行の評価を完了させる
- Az.DataMigration モジュールを PowerShell ギャラリーからダウンロードし、[Install-Module PowerShell](https://docs.microsoft.com/powershell/module/powershellget/Install-Module?view=powershell-5.1) コマンドレットを使用してインストールする
- ソースの SQL Server インスタンスへの接続に使用される資格情報に、[CONTROL SERVER](https://docs.microsoft.com/sql/t-sql/statements/grant-server-permissions-transact-sql) アクセス許可を含める。
- ターゲットの Azure SQL DB インスタンスへの接続に使用される資格情報に、ターゲットの Azure SQL Database に対する CONTROL DATABASE アクセス許可が含まれていることを確認する。
- Azure サブスクリプション。 お持ちでない場合は、開始する前に[無料](https://azure.microsoft.com/free/)アカウントを作成してください。

## <a name="log-in-to-your-microsoft-azure-subscription"></a>Microsoft Azure サブスクリプションにログインする
「[Azure PowerShell でのログイン](https://docs.microsoft.com/powershell/azure/authenticate-azureps)」にある手順に従い、PowerShell を使用して Azure サブスクリプションにログインします。

## <a name="create-a-resource-group"></a>リソース グループの作成
Azure リソース グループとは、Azure リソースのデプロイと管理に使用する論理コンテナーです。 仮想マシンを作成する前に、リソース グループを作成する必要があります。

[New-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/new-azresourcegroup) コマンドを使用してリソース グループを作成します。 

次の例では、*myResourceGroup* という名前のリソース グループを*米国東部*リージョンに作成します。

```powershell
New-AzResourceGroup -ResourceGroupName myResourceGroup -Location EastUS
```
## <a name="create-an-instance-of-the-azure-database-migration-service"></a>Azure Database Migration Service のインスタンスの作成 
新しい Azure Database Migration Service インスタンスを作成するには、`New-AzDataMigrationService` コマンドレットを使用します。 このコマンドレットでは、次のパラメーターが必要です。
- *Azure リソース グループ名*。 [New-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/new-azresourcegroup) コマンドを使用して前述の Azure リソース グループを作成し、パラメーターとしてその名前を指定できます。
- *サービス名*。 Azure Database Migration Service に使用する一意のサービス名に対応する文字列。 
- *場所*。 サービスの場所を指定します。 米国西部や東南アジアなど、Azure データ センターの場所を指定してください。
- *SKU*。 このパラメーターは、DMS SKU 名に対応します。 現在サポートされている SKU 名は、*Basic_1vCore*、*Basic_2vCores*、*GeneralPurpose_4vCores* です。
- *仮想サブネット識別子*。 [New-AzVirtualNetworkSubnetConfig](https://docs.microsoft.com//powershell/module/az.network/new-azvirtualnetworksubnetconfig) コマンドレットを使用して、サブネットを作成できます。 

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

### <a name="create-a-database-connection-info-object-for-the-source-and-target-connections"></a>ソースとターゲットの接続用のデータベース接続情報オブジェクトを作成する
データベース接続情報オブジェクトを作成するには、`New-AzDmsConnInfo` コマンドレットを使用します。 このコマンドレットでは、次のパラメーターが必要です。
- *ServerType*。 要求するデータベース接続の種類 (SQL、Oracle、MySQL など)。 SQL Server と Azure SQL には SQL を使用します。
- *DataSource*。 SQL Server インスタンスや Azure SQL データベースの名前または IP。
- *AuthType*。 接続の認証の種類。SqlAuthentication または WindowsAuthentication を指定できます。
- *TrustServerCertificate* パラメーターは、信頼関係を検証するための証明書チェーンのウォークをバイパスする間にチャネルを暗号化するかどうかを示す値を設定します。 値には true または false を指定できます。

次の例では、SQL 認証を使用する MySourceSQLServer という名前のソース SQL Server の接続情報オブジェクトが作成されます。 

```powershell
$sourceConnInfo = New-AzDmsConnInfo -ServerType SQL `
  -DataSource MySourceSQLServer `
  -AuthType SqlAuthentication `
  -TrustServerCertificate:$true
```

次の例は、SQL 認証を使用する "targetmanagedinstance.database.windows.net" と呼ばれる Azure SQL Database Managed Instance サーバーの接続情報の作成を示しています。

```powershell
$targetConnInfo = New-AzDmsConnInfo -ServerType SQL `
  -DataSource "targetmanagedinstance.database.windows.net" `
  -AuthType SqlAuthentication `
  -TrustServerCertificate:$false
```

### <a name="provide-databases-for-the-migration-project"></a>移行プロジェクト用のデータベースを指定する
Azure Database Migration プロジェクトの一部としてデータベースを指定する `AzDataMigrationDatabaseInfo` オブジェクトの一覧を作成します。これはプロジェクト作成用のパラメーターとして指定できます。 AzDataMigrationDatabaseInfo の作成にはコマンドレット `New-AzDataMigrationDatabaseInfo` を使用できます。 

次の例では、**AdventureWorks** データベース用の `AzDataMigrationDatabaseInfo` プロジェクトが作成され、プロジェクト作成用のパラメーターとして指定される一覧に追加されます。

```powershell
$dbInfo1 = New-AzDataMigrationDatabaseInfo -SourceDatabaseName AdventureWorks
$dbList = @($dbInfo1)
```

### <a name="create-a-project-object"></a>プロジェクト オブジェクトを作成する
最後に、`New-AzDataMigrationProject` を使用し、以前に作成したソースとターゲットの接続と、移行するデータベースの一覧を追加することで、*MyDMSProject* という名前の Azure Database Migration プロジェクトを*米国東部*で作成できます。

```powershell
$project = New-AzDataMigrationProject -ResourceGroupName myResourceGroup `
  -ServiceName $service.Name `
  -ProjectName MyDMSProject `
  -Location EastUS `
  -SourceType SQL `
  -TargetType SQLMI `
  -SourceConnection $sourceConnInfo `
  -TargetConnection $targetConnInfo `
  -DatabaseInfo $dbList
```

## <a name="create-and-start-a-migration-task"></a>移行タスクを作成して開始する
最後に、Azure Database Migration タスクを作成して開始します。 Azure Database Migration タスクには、前提条件として作成されたプロジェクトで提供済みの情報に加えて、ソースとターゲットと両方の接続の資格情報と、移行するデータベース テーブルの一覧が必要です。 

### <a name="create-credential-parameters-for-source-and-target"></a>ソースとターゲットの資格情報パラメーターを作成する
接続のセキュリティ資格情報は [PSCredential](https://docs.microsoft.com/dotnet/api/system.management.automation.pscredential?redirectedfrom=MSDN&view=powershellsdk-1.1.0) オブジェクトとして作成できます。 

次の例は、ソースとターゲットの両方の接続用の *PSCredential* オブジェクトを作成し、パスワードを文字列変数 *$sourcePassword* および *$targetPassword* として提供する方法を示しています。 

```powershell
$secpasswd = ConvertTo-SecureString -String $sourcePassword -AsPlainText -Force
$sourceCred = New-Object System.Management.Automation.PSCredential ($sourceUserName, $secpasswd)
$secpasswd = ConvertTo-SecureString -String $targetPassword -AsPlainText -Force
$targetCred = New-Object System.Management.Automation.PSCredential ($targetUserName, $secpasswd)
```

### <a name="create-backup-fileshare-object"></a>バックアップ ファイル共有オブジェクトを作成する
次は、New-AzDmsFileShare コマンドレットを使用して、Azure Database Migration Service がソース データベース バックアップを移行できるローカル SMB ネットワーク共有を表すファイル共有オブジェクトを作成します。

```powershell
$backupPassword = ConvertTo-SecureString -String $password -AsPlainText -Force
$backupCred = New-Object System.Management.Automation.PSCredential ($backupUserName, $backupPassword)

$backupFileSharePath="\\10.0.0.76\SharedBackup"
$backupFileShare = New-AzDmsFileShare -Path $backupFileSharePath -Credential $backupCred
```

### <a name="create-selected-database-object"></a>選択したデータベース オブジェクトを作成する
次の手順では、次の例のように、New-AzDmsSelectedDB コマンドレットを使用して、ソース データベースとターゲット データベースを選択します。

```powershell
$selectedDbs = @()
$selectedDbs += New-AzDmsSelectedDB -MigrateSqlServerSqlDbMi `
  -Name AdventureWorks2016 `
  -TargetDatabaseName AdventureWorks2016 `
  -BackupFileShare $backupFileShare `
```

### <a name="sas-uri-for-azure-storage-container"></a>Azure Storage コンテナー用の SAS URI
SAS URI が含まれる変数を作成します。この SAS URI により、バックアップ ファイルのアップロード先ストレージ アカウント コンテナーへのアクセスが、Azure Database Migration Service に提供されます。

```powershell
$blobSasUri="https://mystorage.blob.core.windows.net/test?st=2018-07-13T18%3A10%3A33Z&se=2019-07-14T18%3A10%3A00Z&sp=rwdl&sv=2018-03-28&sr=c&sig=qKlSA512EVtest3xYjvUg139tYSDrasbftY%3D"
```

### <a name="select-logins"></a>ログインを選択する
以下の例で示すように、移行するログインの一覧を作成します。現在、DMS では SQL ログインの移行のみがサポートされることに注意してください。 

```powershell
$selectedLogins = @("user1", "user2")
```

### <a name="select-agent-jobs"></a>エージェント ジョブを選択する
以下の例で示すように、移行するエージェント ジョブの一覧を作成します。

>[!NOTE]
>現在 DMS でサポートされているのは、T-SQL サブシステム ジョブ ステップのみを含むジョブです。

```powershell
$selectedAgentJobs = @("agentJob1", "agentJob2")
```

### <a name="create-and-start-a-migration-task"></a>移行タスクを作成して開始する

移行タスクを作成して開始するには、`New-AzDataMigrationTask` コマンドレットを使用します。 このコマンドレットでは、次のパラメーターが必要です。
- *TaskType*。 作成する移行タスクの種類。SQL Server から Azure SQL Database Managed Instance への移行では *MigrateSqlServerSqlDbMi* が必要です。 
- *ResourceGroupName*。 タスクを作成する Azure リソース グループの名前。
- *ServiceName*。 タスクを作成する Azure Database Migration Service インスタンス。
- *ProjectName*。 タスクを作成する Azure Database Migration Service プロジェクトの名前。 
- *TaskName*。 作成するタスクの名前。 
- *SourceConnection*。 ソース SQL Server 接続を表す AzDmsConnInfo オブジェクト。
- *TargetConnection*。 ターゲット Azure SQL Database Managed Instance 接続を表す AzDmsConnInfo オブジェクト。
- *SourceCred*。 ソース サーバーに接続するための [PSCredential](https://docs.microsoft.com/dotnet/api/system.management.automation.pscredential?redirectedfrom=MSDN&view=powershellsdk-1.1.0) オブジェクト。
- *TargetCred*。 ターゲット サーバーに接続するための [PSCredential](https://docs.microsoft.com/dotnet/api/system.management.automation.pscredential?redirectedfrom=MSDN&view=powershellsdk-1.1.0) オブジェクト。
- *SelectedDatabase*。 ソースとターゲット データベースのマッピングを表す AzDataMigrationSelectedDB オブジェクト。
- *BackupFileShare*。 Azure Database Migration Service がソース データベース バックアップを移行できるローカル ネットワーク共有を表すファイル共有オブジェクト。
- *BackupBlobSasUri*。 バックアップ ファイルのアップロード先ストレージ アカウント コンテナーへのアクセスを、Azure Database Migration Service に提供する SAS URI。 BLOB コンテナーの SAS URI を取得する方法を確認してください。
- *SelectedLogins*。 移行対象として選択したログインの一覧。
- *SelectedAgentJobs*。 移行対象として選択したエージェント ジョブの一覧。

次の例では、myDMSTask という名前の移行タスクが作成されて開始します。

```powershell
$migTask = New-AzDataMigrationTask -TaskType MigrateSqlServerSqlDbMi `
  -ResourceGroupName myResourceGroup `
  -ServiceName $service.Name `
  -ProjectName $project.Name `
  -TaskName myDMSTask `
  -SourceConnection $sourceConnInfo `
  -SourceCred $sourceCred `
  -TargetConnection $targetConnInfo `
  -TargetCred $targetCred `
  -SelectedDatabase  $selectedDbs `
  -BackupFileShare $backupFileShare `
  -BackupBlobSasUri $blobSasUri `
  -SelectedLogins $selectedLogins `
  -SelectedAgentJobs $selectedJobs `
```

## <a name="monitor-the-migration"></a>移行を監視する
次の例で示すように、タスクの状態プロパティを照会することにより、実行中の移行タスクを監視することができます。

```powershell
if (($mytask.ProjectTask.Properties.State -eq "Running") -or ($mytask.ProjectTask.Properties.State -eq "Queued"))
{
  write-host "migration task running"
}
```

## <a name="deleting-the-dms-instance"></a>DMS インスタンスの削除
移行が完了したら、Azure DMS インスタンスを削除できます。

```powershell
Remove-AzDms -ResourceGroupName myResourceGroup -ServiceName MyDMS
```

## <a name="next-steps"></a>次の手順
- 「[Microsoft Database Migration Guide](https://datamigration.microsoft.com/)」にある移行ガイドを確認する。
