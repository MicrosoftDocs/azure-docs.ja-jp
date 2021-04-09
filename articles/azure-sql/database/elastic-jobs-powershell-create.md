---
title: PowerShell を使用してエラスティック ジョブ エージェントを作成する (プレビュー)
description: PowerShell を使用したエラスティック ジョブ エージェントの作成方法について学習します。
services: sql-database
ms.service: sql-database
ms.subservice: scale-out
ms.custom: seo-lt-2019, devx-track-azurepowershell
ms.devlang: ''
ms.topic: tutorial
author: johnpaulkee
ms.author: joke
ms.reviwer: sstein
ms.date: 10/21/2020
ms.openlocfilehash: 95e9ef340328bb4c1835e966cc9c3019bca88c09
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "100368831"
---
# <a name="create-an-elastic-job-agent-using-powershell-preview"></a>PowerShell を使用してエラスティック ジョブ エージェントを作成する (プレビュー)
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

[エラスティック ジョブ (プレビュー)](job-automation-overview.md) を使用すると、多数のデータベースにわたって 1 つまたは複数の Transact-SQL (T-SQL) スクリプトを同時に実行できます。

このチュートリアルでは、複数のデータベースにわたってクエリを実行するために必要な手順を学習します。

> [!div class="checklist"]
> * エラスティック ジョブ エージェントを作成する
> * ジョブによってターゲット上でスクリプトを実行できるようにジョブ認証情報を作成する
> * ジョブを実行するターゲット (サーバー、エラスティック プール、データベース、シャード マップ) を定義する
> * エージェントが接続してジョブを実行できるように、ターゲット データベースにデータベース スコープ資格情報を作成する
> * ジョブの作成
> * ジョブにジョブ ステップを追加する
> * ジョブの実行を開始する
> * ジョブを監視する

## <a name="prerequisites"></a>前提条件

アップグレード バージョンの Elastic Database ジョブには、移行時に使用する一連の新しい PowerShell コマンドレットがあります。 これらの新しいコマンドレットは、既存のジョブの資格情報、ターゲット (データベース、サーバー、カスタム コレクションを含む)、ジョブ トリガー、ジョブ スケジュール、ジョブ コンテンツ、およびジョブを新しいエラスティック ジョブ エージェントにすべて転送します。

### <a name="install-the-latest-elastic-jobs-cmdlets"></a>最新のエラスティック ジョブ コマンドレットをインストールする

Azure サブスクリプションをまだお持ちでない場合は、開始する前に[無料アカウント](https://azure.microsoft.com/free/)を作成してください。

**Az.Sql** モジュールをインストールして、エラスティック ジョブの最新のコマンドレットを入手してください。 管理アクセス権で次の PowerShell コマンドを実行します。

```powershell
# installs the latest PackageManagement and PowerShellGet packages
Find-Package PackageManagement | Install-Package -Force
Find-Package PowerShellGet | Install-Package -Force

# Restart your powershell session with administrative access

# Install and import the Az.Sql module, then confirm
Install-Module -Name Az.Sql
Import-Module Az.Sql

Get-Module Az.Sql
```

このチュートリアルには、**Az.Sql** モジュールに加えて、*SqlServer* PowerShell モジュールも必要です。 詳細については、「[SQL Server PowerShell モジュールのインストール](/sql/powershell/download-sql-server-ps-module)」を参照してください。

## <a name="create-required-resources"></a>必要なリソースを作成する

エラスティック ジョブ エージェントを作成するには、[ジョブ データベース](job-automation-overview.md#elastic-job-database)として使用するデータベース (S0 以上) が必要です。

次のスクリプトを実行すると、新しいリソース グループ、サーバー、およびジョブ データベースとして使用されるデータベースが作成されます。 2 番目のスクリプトを実行すると、ジョブを実行する対象の、2 つの空のデータベースを含む 2 番目のサーバーが作成されます。

エラスティック ジョブには特定の命名要件がないため、[Azure 要件](/azure/architecture/best-practices/resource-naming)に準拠している限り、任意の命名規則を使用できます。

```powershell
# sign in to Azure account
Connect-AzAccount

# create a resource group
Write-Output "Creating a resource group..."
$resourceGroupName = Read-Host "Please enter a resource group name"
$location = Read-Host "Please enter an Azure Region"
$rg = New-AzResourceGroup -Name $resourceGroupName -Location $location
$rg

# create a server
Write-Output "Creating a server..."
$agentServerName = Read-Host "Please enter an agent server name"
$agentServerName = $agentServerName + "-" + [guid]::NewGuid()
$adminLogin = Read-Host "Please enter the server admin name"
$adminPassword = Read-Host "Please enter the server admin password"
$adminPasswordSecure = ConvertTo-SecureString -String $AdminPassword -AsPlainText -Force
$adminCred = New-Object -TypeName "System.Management.Automation.PSCredential" -ArgumentList $adminLogin, $adminPasswordSecure
$agentServer = New-AzSqlServer -ResourceGroupName $resourceGroupName -Location $location `
    -ServerName $agentServerName -ServerVersion "12.0" -SqlAdministratorCredentials ($adminCred)

# set server firewall rules to allow all Azure IPs
Write-Output "Creating a server firewall rule..."
$agentServer | New-AzSqlServerFirewallRule -AllowAllAzureIPs
$agentServer

# create the job database
Write-Output "Creating a blank database to be used as the Job Database..."
$jobDatabaseName = "JobDatabase"
$jobDatabase = New-AzSqlDatabase -ResourceGroupName $resourceGroupName -ServerName $agentServerName -DatabaseName $jobDatabaseName -RequestedServiceObjectiveName "S0"
$jobDatabase
```

```powershell
# create a target server and sample databases - uses the same credentials
Write-Output "Creating target server..."
$targetServerName = Read-Host "Please enter a target server name"
$targetServerName = $targetServerName + "-" + [guid]::NewGuid()
$targetServer = New-AzSqlServer -ResourceGroupName $resourceGroupName -Location $location `
    -ServerName $targetServerName -ServerVersion "12.0" -SqlAdministratorCredentials ($adminCred)

# set target server firewall rules to allow all Azure IPs
$targetServer | New-AzSqlServerFirewallRule -AllowAllAzureIPs
$targetServer | New-AzSqlServerFirewallRule -StartIpAddress 0.0.0.0 -EndIpAddress 255.255.255.255 -FirewallRuleName AllowAll
$targetServer

# create sample databases to execute jobs against
$db1 = New-AzSqlDatabase -ResourceGroupName $resourceGroupName -ServerName $targetServerName -DatabaseName "database1"
$db1
$db2 = New-AzSqlDatabase -ResourceGroupName $resourceGroupName -ServerName $targetServerName -DatabaseName "database2"
$db2
```

### <a name="create-the-elastic-job-agent"></a>エラスティック ジョブ エージェントを作成する

エラスティック ジョブ エージェントは、ジョブを作成、実行、および管理するための Azure リソースです。 エージェントは、スケジュールに基づいて、または 1 回だけのジョブとして、ジョブを実行します。

**New-AzSqlElasticJobAgent** コマンドレットを実行するには、Azure SQL Database のデータベースが既に存在する必要があるため、*resourceGroupName*、*serverName*、および *databaseName* のすべてのパラメーターが既存のリソースを指している必要があります。

```powershell
Write-Output "Creating job agent..."
$agentName = Read-Host "Please enter a name for your new Elastic Job agent"
$jobAgent = $jobDatabase | New-AzSqlElasticJobAgent -Name $agentName
$jobAgent
```

### <a name="create-the-job-credentials"></a>ジョブの資格情報を作成する

ジョブは、データベース スコープ資格情報を使用して、実行時にターゲット グループによって指定されたターゲット データベースに接続し、スクリプトを実行します。 これらのデータベース スコープ資格情報は、ターゲット グループのメンバー タイプとしてサーバーまたはエラスティック プールが使用されているときにマスター データベースに接続してサーバーまたはエラスティック プール内のすべてのデータベースを列挙するためにも使用されます。

データベース スコープ資格情報はジョブ データベースに作成する必要があります。 すべてのターゲット データベースには、ジョブが正常に完了するのに十分なアクセス許可を持つログインが必要です。

![エラスティック ジョブの資格情報](./media/elastic-jobs-powershell-create/job-credentials.png)

画像内の資格情報に加えて、次のスクリプトに **GRANT** コマンドが追加されていることに注意してください。 これらのアクセス許可は、このサンプル ジョブ用に選択したスクリプトに必要です。 この例ではターゲット データベースに新しいテーブルを作成するため、各ターゲット db を正常に実行するための適切なアクセス許可が必要です。

必要なジョブ資格情報を (ジョブ データベースに) 作成するには、次のスクリプトを実行します。

```powershell
# in the master database (target server)
# create the master user login, master user, and job user login
$params = @{
  'database' = 'master'
  'serverInstance' =  $targetServer.ServerName + '.database.windows.net'
  'username' = $adminLogin
  'password' = $adminPassword
  'outputSqlErrors' = $true
  'query' = 'CREATE LOGIN masteruser WITH PASSWORD=''password!123'''
}
Invoke-SqlCmd @params
$params.query = "CREATE USER masteruser FROM LOGIN masteruser"
Invoke-SqlCmd @params
$params.query = 'CREATE LOGIN jobuser WITH PASSWORD=''password!123'''
Invoke-SqlCmd @params

# for each target database
# create the jobuser from jobuser login and check permission for script execution
$targetDatabases = @( $db1.DatabaseName, $Db2.DatabaseName )
$createJobUserScript =  "CREATE USER jobuser FROM LOGIN jobuser"
$grantAlterSchemaScript = "GRANT ALTER ON SCHEMA::dbo TO jobuser"
$grantCreateScript = "GRANT CREATE TABLE TO jobuser"

$targetDatabases | % {
  $params.database = $_
  $params.query = $createJobUserScript
  Invoke-SqlCmd @params
  $params.query = $grantAlterSchemaScript
  Invoke-SqlCmd @params
  $params.query = $grantCreateScript
  Invoke-SqlCmd @params
}

# create job credential in Job database for master user
Write-Output "Creating job credentials..."
$loginPasswordSecure = (ConvertTo-SecureString -String 'password!123' -AsPlainText -Force)

$masterCred = New-Object -TypeName "System.Management.Automation.PSCredential" -ArgumentList "masteruser", $loginPasswordSecure
$masterCred = $jobAgent | New-AzSqlElasticJobCredential -Name "masteruser" -Credential $masterCred

$jobCred = New-Object -TypeName "System.Management.Automation.PSCredential" -ArgumentList "jobuser", $loginPasswordSecure
$jobCred = $jobAgent | New-AzSqlElasticJobCredential -Name "jobuser" -Credential $jobCred
```

### <a name="define-the-target-databases-to-run-the-job-against"></a>ジョブを実行するターゲット データベースを定義する

[ターゲット グループ](job-automation-overview.md#target-group)は、ジョブ ステップによって実行される 1 つまたは複数のデータベースのセットを定義します。

次のスニペットを実行すると、*serverGroup* と *serverGroupExcludingDb2* の 2 つのターゲット グループが作成されます。 *serverGroup* では、実行時にサーバー上に存在するすべてのデータベースがターゲットになります。*serverGroupExcludingDb2* では、*targetDb2* を除くサーバー上のすべてのデータベースがターゲットになります。

```powershell
Write-Output "Creating test target groups..."
# create ServerGroup target group
$serverGroup = $jobAgent | New-AzSqlElasticJobTargetGroup -Name 'ServerGroup'
$serverGroup | Add-AzSqlElasticJobTarget -ServerName $targetServerName -RefreshCredentialName $masterCred.CredentialName

# create ServerGroup with an exclusion of db2
$serverGroupExcludingDb2 = $jobAgent | New-AzSqlElasticJobTargetGroup -Name 'ServerGroupExcludingDb2'
$serverGroupExcludingDb2 | Add-AzSqlElasticJobTarget -ServerName $targetServerName -RefreshCredentialName $masterCred.CredentialName
$serverGroupExcludingDb2 | Add-AzSqlElasticJobTarget -ServerName $targetServerName -Database $db2.DatabaseName -Exclude
```

### <a name="create-a-job-and-steps"></a>ジョブとステップを作成する

この例では、1 つのジョブと、そのジョブを実行するための 2 つのジョブ ステップを定義します。 最初のジョブ ステップ (*step1*) では、ターゲット グループ *ServerGroup* 内のすべてのデータベースに新しいテーブル (*Step1Table*) を作成します。 2 番目のジョブ ステップ (*step2*) では、*TargetDb2* を除くすべてのデータベースに新しいテーブル (*Step2Table*) を作成します。これは、ターゲット グループの事前の定義によって除外することが指定されているためです。

```powershell
Write-Output "Creating a new job..."
$jobName = "Job1"
$job = $jobAgent | New-AzSqlElasticJob -Name $jobName -RunOnce
$job

Write-Output "Creating job steps..."
$sqlText1 = "IF NOT EXISTS (SELECT * FROM sys.tables WHERE object_id = object_id('Step1Table')) CREATE TABLE [dbo].[Step1Table]([TestId] [int] NOT NULL);"
$sqlText2 = "IF NOT EXISTS (SELECT * FROM sys.tables WHERE object_id = object_id('Step2Table')) CREATE TABLE [dbo].[Step2Table]([TestId] [int] NOT NULL);"

$job | Add-AzSqlElasticJobStep -Name "step1" -TargetGroupName $serverGroup.TargetGroupName -CredentialName $jobCred.CredentialName -CommandText $sqlText1
$job | Add-AzSqlElasticJobStep -Name "step2" -TargetGroupName $serverGroupExcludingDb2.TargetGroupName -CredentialName $jobCred.CredentialName -CommandText $sqlText2
```

### <a name="run-the-job"></a>ジョブの実行

ジョブをすぐに開始するには、次のコマンドを実行します。

```powershell
Write-Output "Start a new execution of the job..."
$jobExecution = $job | Start-AzSqlElasticJob
$jobExecution
```

正常に完了すると、TargetDb1 に新しいテーブルが 2 つ、TargetDb2 に新しいテーブルが 1 つだけ表示されます。

   ![SSMS での新しいテーブルの確認](./media/elastic-jobs-powershell-create/job-execution-verification.png)

ジョブを後で実行するようにスケジュールすることもできます。 特定の時刻にジョブを実行するようにスケジュールを設定するには、次のコマンドを実行します。

```powershell
# run every hour starting from now
$job | Set-AzSqlElasticJob -IntervalType Hour -IntervalCount 1 -StartTime (Get-Date) -Enable
```

### <a name="monitor-status-of-job-executions"></a>ジョブの実行状態を監視する

次のスニペットを実行すると、ジョブの実行の詳細を取得できます。

```powershell
# get the latest 10 executions run
$jobAgent | Get-AzSqlElasticJobExecution -Count 10

# get the job step execution details
$jobExecution | Get-AzSqlElasticJobStepExecution

# get the job target execution details
$jobExecution | Get-AzSqlElasticJobTargetExecution -Count 2
```

可能なジョブの実行状態を次の表に示します。

|State|説明|
|:---|:---|
|**Created** | ジョブの実行は作成されたばかりで、まだ進行中ではありません。|
|**InProgress** | ジョブの実行は現在進行中です。|
|**WaitingForRetry** | ジョブ実行はそのアクションを完了できず、再試行を待機しています。|
|**Succeeded** | ジョブの実行は正常に完了しました。|
|**SucceededWithSkipped** | ジョブの実行は正常に完了しましたが、その子の一部がスキップされました。|
|**Failed** | ジョブの実行は失敗し、再試行回数の上限に達しました。|
|**TimedOut** | ジョブの実行はタイムアウトしました。|
|**Canceled** | ジョブの実行は取り消されました。|
|**Skipped** | 同じジョブ手順の別の実行が同じターゲットに対して既に実行されていたため、ジョブの実行はスキップされました。|
|**WaitingForChildJobExecutions** | ジョブの実行は、その子の実行が完了するまで待機しています。|

## <a name="clean-up-resources"></a>リソースをクリーンアップする

リソース グループを削除して、このチュートリアルで作成した Azure リソースを削除します。

> [!TIP]
> 引き続きこれらのジョブを使用する場合は、この記事で作成したリソースをクリーンアップしないでください。

```powershell
Remove-AzResourceGroup -ResourceGroupName $resourceGroupName
```

## <a name="next-steps"></a>次のステップ

このチュートリアルでは、データベースのセットに対して Transact-SQL スクリプトを実行しました。 次のタスクを実行する方法を学習しました。

> [!div class="checklist"]
> * エラスティック ジョブ エージェントを作成する
> * ジョブによってターゲット上でスクリプトを実行できるようにジョブ認証情報を作成する
> * ジョブを実行するターゲット (サーバー、エラスティック プール、データベース、シャード マップ) を定義する
> * エージェントが接続してジョブを実行できるように、ターゲット データベースにデータベース スコープ資格情報を作成する
> * ジョブの作成
> * ジョブにジョブ ステップを追加する
> * ジョブの実行を開始する
> * ジョブの監視

> [!div class="nextstepaction"]
> [Transact-SQL を使用してエラスティック ジョブを管理する](elastic-jobs-tsql-create-manage.md)
