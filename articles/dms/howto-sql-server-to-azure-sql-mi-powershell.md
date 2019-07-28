---
title: Database Migration Service および PowerShell を使用して SQL Server を Azure SQL Database Managed Instance に移行する | Microsoft Docs
description: Azure PowerShell を使用してオンプレミスの SQL Server から Azure SQL DB Managed Instance に移行する方法について説明します。
services: database-migration
author: hjtoland3
ms.author: jtoland
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: mvc
ms.topic: article
ms.date: 04/29/2019
ms.openlocfilehash: d83410efd26f8c2078d3abdb01d061db0b83d33d
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/13/2019
ms.locfileid: "65233719"
---
# <a name="migrate-sql-server-on-premises-to-an-azure-sql-database-managed-instance-using-azure-powershell"></a>Azure PowerShell を使用してオンプレミスの SQL Server を Azure SQL Database マネージド インスタンスに移行する
この記事では、Microsoft Azure PowerShell を使用して、SQL Server 2005 以上のオンプレミス インスタンスに復元された **Adventureworks2016** データベースを Azure SQL Database マネージド インスタンスに移行します。 Microsoft Azure PowerShell で `Az.DataMigration` モジュールを使用すると、データベースをオンプレミスの SQL Server インスタンスから Azure SQL Database マネージド インスタンスに移行できます。

この記事では、次のことについて説明します。
> [!div class="checklist"]
>
> * リソース グループを作成します。
> * Azure Database Migration Service のインスタンスを作成する。
> * Azure Database Migration Service のインスタンスで移行プロジェクトを作成する。
> * 移行を実行する。

[!INCLUDE [online-offline](../../includes/database-migration-service-offline-online.md)]

この記事には、オンラインとオフラインの両方で移行を実行する方法の詳細が含まれています。

## <a name="prerequisites"></a>前提条件

これらの手順を完了するには、以下が必要です。

* [SQL Server 2016 以上](https://www.microsoft.com/sql-server/sql-server-downloads) (任意のエディション)。
* **AdventureWorks2016** データベースのローカル コピー ([こちら](https://docs.microsoft.com/sql/samples/adventureworks-install-configure?view=sql-server-2017)からダウンロードできます)。
* TCP/IP プロトコルを有効にする (SQL Server Express のインストールでは既定で無効になっています)。 [サーバー ネットワーク プロトコルの有効化または無効化](https://docs.microsoft.com/sql/database-engine/configure-windows/enable-or-disable-a-server-network-protocol#SSMSProcedure)に関する記事の説明に従って、TCP/IP プロトコルを有効にする。
* [データベース エンジン アクセス用の Windows Firewall](https://docs.microsoft.com/sql/database-engine/configure-windows/configure-a-windows-firewall-for-database-engine-access) の構成。
* Azure サブスクリプション。 お持ちでない場合は、開始する前に[無料アカウントを作成](https://azure.microsoft.com/free/)してください。
* Azure SQL Database マネージド インスタンス。 [Azure SQL Database マネージド インスタンスの作成](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-get-started)に関する記事の詳細に従って、Azure SQL Database マネージド インスタンスを作成できます。
* [Data Migration Assistant](https://www.microsoft.com/download/details.aspx?id=53595) v3.3 以降をダウンロードしてインストールする。
* [ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) または [VPN](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways) を使用してオンプレミス ソース サーバーへのサイト間接続を Azure Database Migration Service に提供する、Azure Resource Manager デプロイ モデルを使用して作成された Azure 仮想ネットワーク (VNet)。
* [SQL Server の移行評価の実行](https://docs.microsoft.com/sql/dma/dma-assesssqlonprem)に関する記事で説明されているように、Data Migration Assistant を使用してオンプレミスのデータベースおよびスキーマの移行の評価を完了していること。
* `Az.DataMigration` モジュール (バージョン 0.7.2 以降) を PowerShell ギャラリーからダウンロードし、[Install-Module PowerShell cmdlet](https://docs.microsoft.com/powershell/module/powershellget/Install-Module?view=powershell-5.1) コマンドレットを使用してインストールする。
* ソースの SQL Server インスタンスへの接続に使用される資格情報に、[CONTROL SERVER](https://docs.microsoft.com/sql/t-sql/statements/grant-server-permissions-transact-sql) アクセス許可が含まれていることを確認する。
* ターゲットの Azure SQL Database マネージド インスタンスへの接続に使用される資格情報に、ターゲットの Azure SQL Database マネージド インスタンス データベースに対する CONTROL DATABASE アクセス許可が含まれていることを確認する。

    > [!IMPORTANT]
    > オンライン移行の場合は、Azure Active Directory の資格情報を既に設定してある必要があります。 詳細については、[リソースにアクセスできる Azure AD アプリケーションとサービス プリンシパルをポータルを使用して作成する方法](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal)に関する記事を参照してください。

## <a name="sign-in-to-your-microsoft-azure-subscription"></a>Microsoft Azure サブスクリプションにサインインする

PowerShell を使用して Azure サブスクリプションにサインインします。 詳細については、「[Azure PowerShell を使用してサインインする](https://docs.microsoft.com/powershell/azure/authenticate-azureps)」を参照してください。

## <a name="create-a-resource-group"></a>リソース グループの作成

Azure リソース グループとは、Azure リソースのデプロイと管理に使用する論理コンテナーです。

[`New-AzResourceGroup`](https://docs.microsoft.com/powershell/module/az.resources/new-azresourcegroup) コマンドを使用してリソース グループを作成します。

次の例では、*myResourceGroup* という名前のリソース グループを "*米国東部*" リージョンに作成します。

```powershell
New-AzResourceGroup -ResourceGroupName myResourceGroup -Location EastUS
```

## <a name="create-an-instance-of-azure-database-migration-service"></a>Azure Database Migration Service のインスタンスを作成する

新しい Azure Database Migration Service インスタンスを作成するには、`New-AzDataMigrationService` コマンドレットを使用します。
このコマンドレットでは、次のパラメーターが必要です。

* *Azure リソース グループ名*。 [`New-AzResourceGroup`](https://docs.microsoft.com/powershell/module/az.resources/new-azresourcegroup) コマンドを使用して前述の Azure リソース グループを作成し、パラメーターとしてその名前を指定できます。
* *サービス名*。 Azure Database Migration Service に使用する一意のサービス名に対応する文字列。
* *場所*。 サービスの場所を指定します。 米国西部や東南アジアなど、Azure データ センターの場所を指定してください。
* *SKU*。 このパラメーターは、DMS SKU 名に対応します。 現在サポートされている SKU 名は、*Basic_1vCore*、*Basic_2vCores*、*GeneralPurpose_4vCores* です。
* *仮想サブネット識別子*。 [`New-AzVirtualNetworkSubnetConfig`](https://docs.microsoft.com//powershell/module/az.network/new-azvirtualnetworksubnetconfig) コマンドレットを使用して、サブネットを作成できます。

次の例では、*MyVNET* という仮想ネットワークと *MySubnet* というサブネットを使用して、"*米国東部*" リージョンにあるリソース グループ *MyDMSResourceGroup* に *MyDMS* という名前のサービスを作成します。

> [!IMPORTANT]
> 次のコード スニペットはオフライン移行用です。Premium SKU に基づいた Azure Database Migration Service のインスタンスは必要ありません。 オンライン移行の場合は、-Sku パラメーターの値に Premium SKU を含める必要があります。

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

データベース接続情報オブジェクトを作成するには、`New-AzDmsConnInfo` コマンドレットを使用します。このコマンドレットには、次のパラメーターが必要です。

* *ServerType*。 要求するデータベース接続の種類 (SQL、Oracle、MySQL など)。 SQL Server と Azure SQL には SQL を使用します。
* *DataSource*。 SQL Server インスタンスまたは Azure SQL データベース インスタンスの名前または IP。
* *AuthType*。 接続の認証の種類。SqlAuthentication または WindowsAuthentication を指定できます。
* *TrustServerCertificate*。 このパラメーターは、信頼関係を検証するための証明書チェーンのウォークをバイパスする間にチャネルを暗号化するかどうかを示す値を設定します。 値は `$true` または `$false` です。

次の例では、SQL 認証を使用する *MySourceSQLServer* という名前のソース SQL Server の接続情報オブジェクトを作成します。

```powershell
$sourceConnInfo = New-AzDmsConnInfo -ServerType SQL `
  -DataSource MySourceSQLServer `
  -AuthType SqlAuthentication `
  -TrustServerCertificate:$true
```

次の例は、SQL 認証を使用する "targetmanagedinstance.database.windows.net" という名前の Azure SQL Database マネージド インスタンス サーバーの接続情報の作成を示しています。

```powershell
$targetConnInfo = New-AzDmsConnInfo -ServerType SQL `
  -DataSource "targetmanagedinstance.database.windows.net" `
  -AuthType SqlAuthentication `
  -TrustServerCertificate:$false
```

### <a name="provide-databases-for-the-migration-project"></a>移行プロジェクト用のデータベースを指定する

Azure Database Migration Service プロジェクトの一部としてデータベースを指定する `AzDataMigrationDatabaseInfo` オブジェクトの一覧を作成します。これはプロジェクト作成用のパラメーターとして指定できます。 `New-AzDataMigrationDatabaseInfo` コマンドレットを使用して `AzDataMigrationDatabaseInfo` を作成できます。

次の例では、**AdventureWorks2016** データベース用の `AzDataMigrationDatabaseInfo` プロジェクトを作成し、プロジェクト作成用のパラメーターとして指定される一覧に追加します。

```powershell
$dbInfo1 = New-AzDataMigrationDatabaseInfo -SourceDatabaseName AdventureWorks
$dbList = @($dbInfo1)
```

### <a name="create-a-project-object"></a>プロジェクト オブジェクトを作成する

最後に、`New-AzDataMigrationProject` を使用して "*米国東部*" に *MyDMSProject* という名前の Azure Database Migration Service プロジェクトを作成し、以前に作成したソースとターゲットの接続と、移行するデータベースの一覧を追加できます。

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

次に、Azure Database Migration Service タスクを作成して開始します。 このタスクには、移行するデータベース テーブルの一覧と、前提条件として作成されたプロジェクトで提供済みの情報のほかに、ソースとターゲットの両方の接続の資格情報が必要です。

### <a name="create-credential-parameters-for-source-and-target"></a>ソースとターゲットの資格情報パラメーターを作成する

接続のセキュリティ資格情報を [PSCredential](https://docs.microsoft.com/dotnet/api/system.management.automation.pscredential?redirectedfrom=MSDN&view=powershellsdk-1.1.0) オブジェクトとして作成します。

次の例では、ソースとターゲットの両方の接続用の *PSCredential* オブジェクトを作成し、パスワードを文字列変数 *$sourcePassword* および *$targetPassword* として指定します。

```powershell
$secpasswd = ConvertTo-SecureString -String $sourcePassword -AsPlainText -Force
$sourceCred = New-Object System.Management.Automation.PSCredential ($sourceUserName, $secpasswd)
$secpasswd = ConvertTo-SecureString -String $targetPassword -AsPlainText -Force
$targetCred = New-Object System.Management.Automation.PSCredential ($targetUserName, $secpasswd)
```

### <a name="create-a-backup-fileshare-object"></a>バックアップ用 FileShare オブジェクトを作成する

ここで、`New-AzDmsFileShare` コマンドレットを使用して、Azure Database Migration Service がソース データベースのバックアップを作成できるローカル SMB ネットワーク共有を表す FileShare オブジェクトを作成します。

```powershell
$backupPassword = ConvertTo-SecureString -String $password -AsPlainText -Force
$backupCred = New-Object System.Management.Automation.PSCredential ($backupUserName, $backupPassword)

$backupFileSharePath="\\10.0.0.76\SharedBackup"
$backupFileShare = New-AzDmsFileShare -Path $backupFileSharePath -Credential $backupCred
```

### <a name="create-selected-database-object"></a>選択したデータベース オブジェクトを作成する

次の手順では、`New-AzDmsSelectedDB` コマンドレットを使用して、ソース データベースとターゲット データベースを選択します。

次の例は、SQL Server から Azure SQL Database マネージド インスタンスに単一データベースを移行するためのものです。

```powershell
$selectedDbs = @()
$selectedDbs += New-AzDmsSelectedDB -MigrateSqlServerSqlDbMi `
  -Name AdventureWorks2016 `
  -TargetDatabaseName AdventureWorks2016 `
  -BackupFileShare $backupFileShare `
```

SQL Server インスタンス全体で Azure SQL Database マネージド インスタンスへのリフトアンドシフトが必要な場合、すべてのデータベースをソースから取得するためのループを以下に示します。 次の例では、$Server、$SourceUserName、$SourcePassword にソース SQL Server の詳細を入力します。

```powershell
$Query = "(select name as Database_Name from master.sys.databases where Database_id>4)";
$Databases= (Invoke-Sqlcmd -ServerInstance "$Server" -Username $SourceUserName
-Password $SourcePassword -database master -Query $Query)
$selectedDbs=@()
foreach($DataBase in $Databases.Database_Name)
    {
      $SourceDB=$DataBase
      $TargetDB=$DataBase
      
$selectedDbs += New-AzureRmDmsSelectedDB -MigrateSqlServerSqlDbMi `
                                              -Name $SourceDB `
                                              -TargetDatabaseName $TargetDB `
                                              -BackupFileShare $backupFileShare
      }
```

### <a name="sas-uri-for-azure-storage-container"></a>Azure Storage コンテナー用の SAS URI

SAS URI が含まれる変数を作成します。この SAS URI により、バックアップ ファイルのアップロード先ストレージ アカウント コンテナーへのアクセスが、Azure Database Migration Service に提供されます。

```powershell
$blobSasUri="https://mystorage.blob.core.windows.net/test?st=2018-07-13T18%3A10%3A33Z&se=2019-07-14T18%3A10%3A00Z&sp=rwdl&sv=2018-03-28&sr=c&sig=qKlSA512EVtest3xYjvUg139tYSDrasbftY%3D"
```

### <a name="additional-configuration-requirements"></a>追加の構成要件

対処する必要がある追加の要件がいくつかありますが、それらはオフライン移行とオンライン移行のどちらを実行するかによって異なります。

#### <a name="offline-migrations"></a>オフライン移行

オフライン移行の場合のみ、次の追加の構成タスクを実行します。

* **ログインを選択する**。 次の例に示すように、移行するログインの一覧を作成します。

    ```powershell
    $selectedLogins = @(“user1”, “user2”)
    ```

    > [!IMPORTANT]
    > 現在、Azure Database Migration Service でサポートされているのは、SQL ログインの移行のみです。

* **エージェント ジョブを選択する**。 次の例に示すように、移行するエージェント ジョブの一覧を作成します。

    ```powershell
    $selectedAgentJobs = @("agentJob1", "agentJob2")
    ```

    > [!IMPORTANT]
    > 現在、Azure Database Migration Service は、T-SQL サブシステム ジョブ ステップを含むジョブのみをサポートしています。

#### <a name="online-migrations"></a>オンライン移行

オンライン移行の場合のみ、次の追加の構成タスクを実行します。

* **Azure Active Directory アプリを構成する**

    ```powershell
    $pwd = "Azure App Key"
    $appId = "Azure App Id"
    $AppPasswd = ConvertTo-SecureString $pwd -AsPlainText -Force
    $app = New-AzDmsAdApp -ApplicationId $appId -AppKey $AppPasswd
    ```

* **ストレージ リソースを構成する**

    ```powershell
    $storageResourceId = "Storage Resource Id"
    ```

### <a name="create-and-start-the-migration-task"></a>移行タスクを作成して開始する

移行タスクを作成して開始するには、`New-AzDataMigrationTask` コマンドレットを使用します。

#### <a name="specify-parameters"></a>パラメーターの指定

##### <a name="common-parameters"></a>共通パラメーター

オフライン移行とオンライン移行のどちらを実行しているかに関係なく、`New-AzDataMigrationTask` コマンドレットには次のパラメーターが必要です。

* *TaskType*。 作成する移行タスクの種類。SQL Server から Azure SQL Database Managed Instance への移行では *MigrateSqlServerSqlDbMi* が必要です。 
* *ResourceGroupName*。 タスクを作成する Azure リソース グループの名前。
* *ServiceName*。 タスクを作成する Azure Database Migration Service インスタンス。
* *ProjectName*。 タスクを作成する Azure Database Migration Service プロジェクトの名前。 
* *TaskName*。 作成するタスクの名前。 
* *SourceConnection*。 ソース SQL Server 接続を表す AzDmsConnInfo オブジェクト。
* *TargetConnection*。 ターゲット Azure SQL Database Managed Instance 接続を表す AzDmsConnInfo オブジェクト。
* *SourceCred*。 ソース サーバーに接続するための [PSCredential](https://docs.microsoft.com/dotnet/api/system.management.automation.pscredential?redirectedfrom=MSDN&view=powershellsdk-1.1.0) オブジェクト。
* *TargetCred*。 ターゲット サーバーに接続するための [PSCredential](https://docs.microsoft.com/dotnet/api/system.management.automation.pscredential?redirectedfrom=MSDN&view=powershellsdk-1.1.0) オブジェクト。
* *SelectedDatabase*。 ソースとターゲット データベースのマッピングを表す AzDataMigrationSelectedDB オブジェクト。
* *BackupFileShare*。 Azure Database Migration Service がソース データベース バックアップを移行できるローカル ネットワーク共有を表すファイル共有オブジェクト。
* *BackupBlobSasUri*。 バックアップ ファイルのアップロード先ストレージ アカウント コンテナーへのアクセスを、Azure Database Migration Service に提供する SAS URI。 BLOB コンテナーの SAS URI を取得する方法を確認してください。
* *SelectedLogins*。 移行対象として選択したログインの一覧。
* *SelectedAgentJobs*。 移行対象として選択したエージェント ジョブの一覧。

##### <a name="additional-parameters"></a>追加パラメーター

`New-AzDataMigrationTask` コマンドレットには、実行している移行の種類 (オフラインまたはオンライン) に固有のパラメーターも必要になります。

* **オフライン移行**。 オフライン移行の場合、`New-AzDataMigrationTask` コマンドレットは次のパラメーターも必要とします。

  * *SelectedLogins*。 移行対象として選択したログインの一覧。
  * *SelectedAgentJobs*。 移行対象として選択したエージェント ジョブの一覧。

* **オンライン移行**。 オンライン移行の場合、`New-AzDataMigrationTask` コマンドレットは次のパラメーターも必要とします。

* *AzureActiveDirectoryApp*。 Active Directory アプリケーション。
* *StorageResourceID*。 ストレージ アカウントのリソース ID。

#### <a name="create-and-start-an-offline-migration-task"></a>オフライン移行タスクを作成して開始する

次の例では、**myDMSTask** という名前のオフライン移行タスクを作成して開始します。

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

#### <a name="create-and-start-an-online-migration-task"></a>オンライン移行タスクを作成して開始する

次の例では、**myDMSTask** という名前のオンライン移行タスクを作成して開始します。

```powershell
$migTask = New-AzDataMigrationTask -TaskType MigrateSqlServerSqlDbMiSync `
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
  -SelectedDatabase $selectedDbs `
  -AzureActiveDirectoryApp $app `
  -StorageResourceId $storageResourceId
```

## <a name="monitor-the-migration"></a>移行を監視する

移行を監視するには、次のタスクを実行します。

1. 移行の詳細すべてを $CheckTask という変数に統合します。

    移行に関連付けられたプロパティ、状態、データベース情報などの移行の詳細を組み合わせるには、次のコード スニペットを使用します。

    ```powershell
    $CheckTask= Get-AzDataMigrationTask     -ResourceGroupName myResourceGroup `
                                            -ServiceName $service.Name `
                                        -ProjectName $project.Name `
                                            -Name myDMSTask `
                                            -ResultType DatabaseLevelOutput `
                        -Expand 
    Write-Host ‘$CheckTask.ProjectTask.Properties.Output’
    ```

2. `$CheckTask` 変数を使用して、移行タスクの現在の状態を取得します。

    `$CheckTask` 変数を使用して移行タスクの現在の状態を取得するには、次の例に示すように、タスクの状態プロパティを照会して、実行中の移行タスクを監視します。

    ```powershell
    if (($CheckTask.ProjectTask.Properties.State -eq "Running") -or ($CheckTask.ProjectTask.Properties.State -eq "Queued"))
    {
      write-host "migration task running"
    }
    Else if($CheckTask.ProjectTask.Properties.State -eq "Succeeded")
    { 
      write-host "Migration task is completed Successfully"
    }
    Else if($CheckTask.ProjectTask.Properties.State -eq "Failed" -or $CheckTask.ProjectTask.Properties.State -eq "FailedInputValidation"  -or $CheckTask.ProjectTask.Properties.State -eq "Faulted")
    { 
      write-host “Migration Task Failed”
    }
    ```

## <a name="performing-the-cutover-online-migrations-only"></a>一括を実行する (オンライン移行のみ)

オンライン移行では、データベースの完全バックアップと復元が実行された後、BackupFileShare に格納されているトランザクション ログの復元処理が実行されます。

Azure SQL Database マネージド インスタンス内のデータベースが最新のデータで更新され、ソース データベースと同期されている場合、一括を実行できます。

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

## <a name="next-steps"></a>次の手順

「[Azure Database Migration Service とは](https://docs.microsoft.com/azure/dms/dms-overview)」の記事で、Azure Database Migration Service の詳細を確認します。
