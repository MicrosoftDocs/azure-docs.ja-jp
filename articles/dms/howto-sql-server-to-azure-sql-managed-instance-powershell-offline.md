---
title: PowerShell:SQL Server から SQL Managed Instance にオフラインで移行する
titleSuffix: Azure Database Migration Service
description: Azure PowerShell と Azure Database Migration Service を使用して、SQL Server から Azure SQL Managed Instance にオフラインで移行する方法について説明します。
services: database-migration
author: pochiraju
ms.author: rajpo
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: seo-lt-2019,fasttrack-edit, devx-track-azurepowershell
ms.topic: how-to
ms.date: 12/16/2020
ms.openlocfilehash: 90663b6beb4f1e3f7ade32e603a53c8b9d9158f5
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "98697750"
---
# <a name="migrate-sql-server-to-sql-managed-instance-offline-with-powershell--azure-database-migration-service"></a>PowerShell と Azure Database Migration Service を使用して SQL Server から SQL Managed Instance にオフラインで移行する

この記事では、Microsoft Azure PowerShell を使用して、SQL Server 2005 以上のオンプレミス インスタンスに復元された **Adventureworks2016** データベースを Azure SQL の SQL Managed Instance にオフラインで移行します。 Microsoft Azure PowerShell の `Az.DataMigration` モジュールを使用して、SQL Server インスタンスから SQL マネージド インスタンスにデータベースを移行できます。

この記事では、次の方法について説明します。
> [!div class="checklist"]
>
> * リソース グループを作成します。
> * Azure Database Migration Service のインスタンスを作成する。
> * Azure Database Migration Service のインスタンスで移行プロジェクトを作成する。
> * オフラインで移行を実行します。

[!INCLUDE [online-offline](../../includes/database-migration-service-offline-online.md)]

この記事では、オフライン移行の手順について説明しますが、[オンラインで](howto-sql-server-to-azure-sql-managed-instance-powershell-online.md)移行することもできます。


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


## <a name="sign-in-to-your-microsoft-azure-subscription"></a>Microsoft Azure サブスクリプションにサインインする

PowerShell を使用して Azure サブスクリプションにサインインします。 詳細については、「[Azure PowerShell を使用してサインインする](/powershell/azure/authenticate-azureps)」を参照してください。

## <a name="create-a-resource-group"></a>リソース グループを作成する

Azure リソース グループとは、Azure リソースのデプロイと管理に使用する論理コンテナーです。

[`New-AzResourceGroup`](/powershell/module/az.resources/new-azresourcegroup) コマンドを使用してリソース グループを作成します。

次の例では、*myResourceGroup* という名前のリソース グループを "*米国東部*" リージョンに作成します。

```powershell
New-AzResourceGroup -ResourceGroupName myResourceGroup -Location EastUS
```

## <a name="create-an-instance-of-azure-database-migration-service"></a>Azure Database Migration Service のインスタンスを作成する

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

次の例は、SQL 認証を使用する "targetmanagedinstance" という名前の Azure SQL Managed Instance への接続情報の作成を示しています。

```powershell
$targetResourceId = (Get-AzSqlInstance -Name "targetmanagedinstance").Id
$targetConnInfo = New-AzDmsConnInfo -ServerType SQLMI -MiResourceId $targetResourceId
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

接続のセキュリティ資格情報を [PSCredential](/dotnet/api/system.management.automation.pscredential) オブジェクトとして作成します。

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

次の例では、SQL Server から Azure SQL マネージド インスタンスに単一のデータベースを移行しています。

```powershell
$selectedDbs = @()
$selectedDbs += New-AzDmsSelectedDB -MigrateSqlServerSqlDbMi `
  -Name AdventureWorks2016 `
  -TargetDatabaseName AdventureWorks2016 `
  -BackupFileShare $backupFileShare `
```

SQL Server インスタンス全体で Azure SQL マネージド インスタンスへのリフトアンドシフトが必要な場合、すべてのデータベースをソースから取得するためのループを以下に示します。 次の例では、$Server、$SourceUserName、$SourcePassword にソース SQL Server の詳細を入力します。

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

> [!NOTE]
> Azure Database Migration Service では、アカウント レベルの SAS トークンの使用はサポートされません。 ストレージ アカウント コンテナーには SAS URI を使用する必要があります。 [Blob コンテナーの SAS URI を取得する方法について説明します](../vs-azure-tools-storage-explorer-blobs.md#get-the-sas-for-a-blob-container)。

### <a name="additional-configuration-requirements"></a>追加の構成要件

いくつかの追加要件に対処する必要があります。


* **ログインを選択する**。 次の例に示すように、移行するログインの一覧を作成します。

    ```powershell
    $selectedLogins = @("user1", "user2")
    ```

    > [!IMPORTANT]
    > 現在、Azure Database Migration Service でサポートされているのは、SQL ログインの移行のみです。

* **エージェント ジョブを選択する**。 次の例に示すように、移行するエージェント ジョブの一覧を作成します。

    ```powershell
    $selectedAgentJobs = @("agentJob1", "agentJob2")
    ```

    > [!IMPORTANT]
    > 現在、Azure Database Migration Service は、T-SQL サブシステム ジョブ ステップを含むジョブのみをサポートしています。



### <a name="create-and-start-the-migration-task"></a>移行タスクを作成して開始する

移行タスクを作成して開始するには、`New-AzDataMigrationTask` コマンドレットを使用します。

#### <a name="specify-parameters"></a>パラメーターの指定

`New-AzDataMigrationTask` コマンドレットでは、次のパラメーターが必要です。

* *TaskType*。 作成する移行タスクの種類。SQL Server から Azure SQL Managed Instance への移行の種類である *MigrateSqlServerSqlDbMi* が必要です。 
* *ResourceGroupName*。 タスクを作成する Azure リソース グループの名前。
* *ServiceName*。 タスクを作成する Azure Database Migration Service インスタンス。
* *ProjectName*。 タスクを作成する Azure Database Migration Service プロジェクトの名前。 
* *TaskName*。 作成するタスクの名前。 
* *SourceConnection*。 ソース SQL Server 接続を表す AzDmsConnInfo オブジェクト。
* *TargetConnection*。 ターゲットの Azure SQL マネージド インスタンスの接続を表す AzDmsConnInfo オブジェクト。
* *SourceCred*。 ソース サーバーに接続するための [PSCredential](/dotnet/api/system.management.automation.pscredential) オブジェクト。
* *TargetCred*。 ターゲット サーバーに接続するための [PSCredential](/dotnet/api/system.management.automation.pscredential) オブジェクト。
* *SelectedDatabase*。 ソースとターゲット データベースのマッピングを表す AzDataMigrationSelectedDB オブジェクト。
* *BackupFileShare*。 Azure Database Migration Service がソース データベース バックアップを移行できるローカル ネットワーク共有を表すファイル共有オブジェクト。
* *BackupBlobSasUri*。 バックアップ ファイルのアップロード先ストレージ アカウント コンテナーへのアクセスを、Azure Database Migration Service に提供する SAS URI。 BLOB コンテナーの SAS URI を取得する方法を確認してください。
* *SelectedLogins*。 移行対象として選択したログインの一覧。
* *SelectedAgentJobs*。 移行対象として選択したエージェント ジョブの一覧。
* *SelectedLogins*。 移行対象として選択したログインの一覧。
* *SelectedAgentJobs*。 移行対象として選択したエージェント ジョブの一覧。



#### <a name="create-and-start-a-migration-task"></a>移行タスクを作成して開始する

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
      Write-Host "migration task running"
    }
    else if($CheckTask.ProjectTask.Properties.State -eq "Succeeded")
    { 
      Write-Host "Migration task is completed Successfully"
    }
    else if($CheckTask.ProjectTask.Properties.State -eq "Failed" -or $CheckTask.ProjectTask.Properties.State -eq "FailedInputValidation" -or $CheckTask.ProjectTask.Properties.State -eq "Faulted")
    { 
      Write-Host "Migration Task Failed"
    }
    ```


## <a name="delete-the-instance-of-azure-database-migration-service"></a>Azure Database Migration Service のインスタンスを削除する

移行が完了したら、Azure Database Migration Service インスタンスを削除できます。

```powershell
Remove-AzDms -ResourceGroupName myResourceGroup -ServiceName MyDMS
```


## <a name="next-steps"></a>次のステップ

「[Azure Database Migration Service とは](./dms-overview.md)」の記事で、Azure Database Migration Service の詳細を確認します。

追加の移行シナリオ (ソースとターゲットのペア) については、Microsoft の[データベース移行ガイド](https://datamigration.microsoft.com/)に関するページを参照してください。