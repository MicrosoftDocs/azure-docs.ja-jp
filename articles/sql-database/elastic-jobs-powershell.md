---
title: PowerShell を使用した Azure SQL Database エラスティック ジョブ エージェントの作成 | Microsoft Docs
description: PowerShell を使用したエラスティック ジョブ エージェントの作成方法について学習します。
services: sql-database
ms.service: sql-database
ms.subservice: scale-out
ms.custom: ''
ms.devlang: ''
ms.topic: tutorial
author: johnpaulkee
ms.author: joke
ms.reviwer: sstein
ms.date: 03/13/2019
ms.openlocfilehash: 064d55b96c8817f4b7ccc5f0925eeecfaf310424
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/26/2019
ms.locfileid: "68550530"
---
# <a name="create-an-elastic-job-agent-using-powershell"></a>PowerShell を使用したエラスティック ジョブ エージェントの作成

[エラスティック ジョブ](sql-database-job-automation-overview.md#elastic-database-jobs)を使用すると、複数のデータベースにわたって 1 つまたは複数の Transact-SQL (T-SQL) スクリプトを同時に実行できます。

このチュートリアルでは、複数のデータベースにわたってクエリを実行するために必要な手順を学習します。

> [!div class="checklist"]
> * エラスティック ジョブ エージェントを作成する
> * ジョブによってターゲット上でスクリプトを実行できるようにジョブ認証情報を作成する
> * ジョブを実行するターゲット (サーバー、エラスティック プール、データベース、シャード マップ) を定義する
> * エージェントが接続してジョブを実行できるように、ターゲット データベースにデータベース スコープ資格情報を作成する
> * ジョブを作成する
> * ジョブにジョブ ステップを追加する
> * ジョブの実行を開始する
> * ジョブを監視する

## <a name="prerequisites"></a>前提条件

アップグレード バージョンの Elastic Database ジョブには、移行時に使用する一連の新しい PowerShell コマンドレットがあります。 これらの新しいコマンドレットは、既存のジョブの資格情報、ターゲット (データベース、サーバー、カスタム コレクションを含む)、ジョブ トリガー、ジョブ スケジュール、ジョブ コンテンツ、およびジョブを新しいエラスティック ジョブ エージェントにすべて転送します。

### <a name="install-the-latest-elastic-jobs-cmdlets"></a>最新のエラスティック ジョブ コマンドレットをインストールする

Azure サブスクリプションをまだお持ちでない場合は、開始する前に[無料アカウント](https://azure.microsoft.com/free/)を作成してください。

**Az.Sql** 1.1.1-preview モジュールをインストールして、エラスティック ジョブの最新のコマンドレットを入手してください。 管理アクセス権で次の PowerShell コマンドを実行します。

```powershell
# Installs the latest PackageManagement powershell package which PowershellGet v1.6.5 is dependent on
Find-Package PackageManagement -RequiredVersion 1.1.7.2 | Install-Package -Force

# Installs the latest PowershellGet module which adds the -AllowPrerelease flag to Install-Module
Find-Package PowerShellGet -RequiredVersion 1.6.5 | Install-Package -Force

# Restart your powershell session with administrative access

# Places Az.Sql preview cmdlets side by side with existing Az.Sql version
Install-Module -Name Az.Sql -RequiredVersion 1.1.1-preview -AllowPrerelease

# Import the Az.Sql module
Import-Module Az.Sql -RequiredVersion 1.1.1

# Confirm if module successfully imported - if the imported version is 1.1.1, then continue
Get-Module Az.Sql
```

- このチュートリアルには、**Az.Sql** 1.1.1-preview モジュールに加えて、*sqlserver* PowerShell モジュールも必要です。 詳細については、「[SQL Server PowerShell モジュールのインストール](https://docs.microsoft.com/sql/powershell/download-sql-server-ps-module)」を参照してください。


## <a name="create-required-resources"></a>必要なリソースを作成する

エラスティック ジョブ エージェントを作成するには、[ジョブ データベース](sql-database-job-automation-overview.md#job-database)として使用するデータベース (S0 以上) が必要です。 

"*次のスクリプトを実行すると、新しいリソース グループ、サーバー、およびジョブ データベースとして使用されるデータベースが作成されます。さらに、このスクリプトを実行すると、ジョブを実行する対象の、2 つの空のデータベースを含む 2 番目のサーバーも作成されます。* "

エラスティック ジョブには特定の命名要件がないため、[Azure 要件](https://docs.microsoft.com/azure/architecture/best-practices/naming-conventions)に準拠している限り、任意の命名規則を使用できます。

```powershell
# Sign in to your Azure account
Connect-AzAccount

# Create a resource group
Write-Output "Creating a resource group..."
$ResourceGroupName = Read-Host "Please enter a resource group name"
$Location = Read-Host "Please enter an Azure Region"
$Rg = New-AzResourceGroup -Name $ResourceGroupName -Location $Location
$Rg

# Create a server
Write-Output "Creating a server..."
$AgentServerName = Read-Host "Please enter an agent server name"
$AgentServerName = $AgentServerName + "-" + [guid]::NewGuid()
$AdminLogin = Read-Host "Please enter the server admin name"
$AdminPassword = Read-Host "Please enter the server admin password"
$AdminPasswordSecure = ConvertTo-SecureString -String $AdminPassword -AsPlainText -Force
$AdminCred = New-Object -TypeName "System.Management.Automation.PSCredential" -ArgumentList $AdminLogin, $AdminPasswordSecure
$AgentServer = New-AzSqlServer -ResourceGroupName $ResourceGroupName -Location $Location -ServerName $AgentServerName -ServerVersion "12.0" -SqlAdministratorCredentials ($AdminCred)

# Set server firewall rules to allow all Azure IPs
Write-Output "Creating a server firewall rule..."
$AgentServer | New-AzSqlServerFirewallRule -AllowAllAzureIPs
$AgentServer

# Create the job database
Write-Output "Creating a blank SQL database to be used as the Job Database..."
$JobDatabaseName = "JobDatabase"
$JobDatabase = New-AzSqlDatabase -ResourceGroupName $ResourceGroupName -ServerName $AgentServerName -DatabaseName $JobDatabaseName -RequestedServiceObjectiveName "S0"
$JobDatabase
```

```powershell
# Create a target server and some sample databases - uses the same admin credential as the agent server just for simplicity
Write-Output "Creating target server..."
$TargetServerName = Read-Host "Please enter a target server name"
$TargetServerName = $TargetServerName + "-" + [guid]::NewGuid()
$TargetServer = New-AzSqlServer -ResourceGroupName $ResourceGroupName -Location $Location -ServerName $TargetServerName -ServerVersion "12.0" -SqlAdministratorCredentials ($AdminCred)

# Set target server firewall rules to allow all Azure IPs
$TargetServer | New-AzSqlServerFirewallRule -AllowAllAzureIPs
$TargetServer | New-AzSqlServerFirewallRule -StartIpAddress 0.0.0.0 -EndIpAddress 255.255.255.255 -FirewallRuleName AllowAll
$TargetServer

# Create some sample databases to execute jobs against...
$Db1 = New-AzSqlDatabase -ResourceGroupName $ResourceGroupName -ServerName $TargetServerName -DatabaseName "TargetDb1"
$Db1
$Db2 = New-AzSqlDatabase -ResourceGroupName $ResourceGroupName -ServerName $TargetServerName -DatabaseName "TargetDb2"
$Db2
```

## <a name="enable-the-elastic-jobs-preview-for-your-subscription"></a>サブスクリプションのエラスティック ジョブ プレビューを有効にする

エラスティック ジョブを使用するには、お使いの Azure サブスクリプションで次のコマンドを実行して、その機能を登録する必要があります。 エラスティック ジョブ エージェントのプロビジョニング先となるサブスクリプションごとに 1 回このコマンドを実行してください。 ジョブのターゲットとなるデータベースしか存在しないサブスクリプションは登録の必要はありません。

```powershell
Register-AzProviderFeature -FeatureName sqldb-JobAccounts -ProviderNamespace Microsoft.Sql
```

## <a name="create-the-elastic-job-agent"></a>エラスティック ジョブ エージェントを作成する

エラスティック ジョブ エージェントは、ジョブを作成、実行、および管理するための Azure リソースです。 エージェントは、スケジュールに基づいて、または 1 回だけのジョブとして、ジョブを実行します。

**New-AzSqlElasticJobAgent** コマンドレットを実行するには、Azure SQL データベースが既に存在する必要があるため、*ResourceGroupName*、*ServerName*、および *DatabaseName* のすべてのパラメーターが既存のリソースを指している必要があります。

```powershell
Write-Output "Creating job agent..."
$AgentName = Read-Host "Please enter a name for your new Elastic Job agent"
$JobAgent = $JobDatabase | New-AzSqlElasticJobAgent -Name $AgentName
$JobAgent
```

## <a name="create-job-credentials-so-that-jobs-can-execute-scripts-on-its-targets"></a>ジョブによってターゲット上でスクリプトを実行できるようにジョブ認証情報を作成する

ジョブは、データベース スコープ資格情報を使用して、実行時にターゲット グループによって指定されたターゲット データベースに接続します。 これらのデータベース スコープ資格情報は、ターゲット グループのメンバー タイプとしてサーバーまたはエラスティック プールが使用されているときにマスター データベースに接続してサーバーまたはエラスティック プール内のすべてのデータベースを列挙するためにも使用されます。

データベース スコープ資格情報はジョブ データベースに作成する必要があります。  
すべてのターゲット データベースには、ジョブが正常に完了するのに十分なアクセス許可を持つログインが必要です。

![エラスティック ジョブの資格情報](media/elastic-jobs-overview/job-credentials.png)

画像内の資格情報に加えて、次のスクリプトに *GRANT* コマンドが追加されていることに注意してください。 これらのアクセス許可は、このサンプル ジョブ用に選択したスクリプトに必要です。 この例ではターゲット データベースに新しいテーブルを作成するため、各ターゲット db を正常に実行するための適切なアクセス許可が必要です。

必要なジョブ資格情報を (ジョブ データベースに) 作成するには、次のスクリプトを実行します。

```powershell
# In the master database (target server)
# - Create the master user login
# - Create the master user from master user login
# - Create the job user login
$Params = @{
  'Database' = 'master'
  'ServerInstance' =  $TargetServer.ServerName + '.database.windows.net'
  'Username' = $AdminLogin
  'Password' = $AdminPassword
  'OutputSqlErrors' = $true
  'Query' = "CREATE LOGIN masteruser WITH PASSWORD='password!123'"
}
Invoke-SqlCmd @Params
$Params.Query = "CREATE USER masteruser FROM LOGIN masteruser"
Invoke-SqlCmd @Params
$Params.Query = "CREATE LOGIN jobuser WITH PASSWORD='password!123'"
Invoke-SqlCmd @Params

# For each of the target databases
# - Create the jobuser from jobuser login
# - Make sure they have the right permissions for successful script execution
$TargetDatabases = @( $Db1.DatabaseName, $Db2.DatabaseName )
$CreateJobUserScript =  "CREATE USER jobuser FROM LOGIN jobuser"
$GrantAlterSchemaScript = "GRANT ALTER ON SCHEMA::dbo TO jobuser"
$GrantCreateScript = "GRANT CREATE TABLE TO jobuser"

$TargetDatabases | % {
  $Params.Database = $_

  $Params.Query = $CreateJobUserScript
  Invoke-SqlCmd @Params

  $Params.Query = $GrantAlterSchemaScript
  Invoke-SqlCmd @Params

  $Params.Query = $GrantCreateScript
  Invoke-SqlCmd @Params
}

# Create job credential in Job database for master user
Write-Output "Creating job credentials..."
$LoginPasswordSecure = (ConvertTo-SecureString -String "password!123" -AsPlainText -Force)

$MasterCred = New-Object -TypeName "System.Management.Automation.PSCredential" -ArgumentList "masteruser", $LoginPasswordSecure
$MasterCred = $JobAgent | New-AzSqlElasticJobCredential -Name "masteruser" -Credential $MasterCred

$JobCred = New-Object -TypeName "System.Management.Automation.PSCredential" -ArgumentList "jobuser", $LoginPasswordSecure
$JobCred = $JobAgent | New-AzSqlElasticJobCredential -Name "jobuser" -Credential $JobCred
```

## <a name="define-the-target-databases-you-want-to-run-the-job-against"></a>ジョブを実行するターゲット データベースを定義する

[ターゲット グループ](sql-database-job-automation-overview.md#target-group)は、ジョブ ステップによって実行される 1 つまたは複数のデータベースのセットを定義します。 

次のスニペットでは、*ServerGroup* と *ServerGroupExcludingDb2* の 2 つのターゲット グループを作成しています。 *ServerGroup* では、実行時にサーバー上に存在するすべてのデータベースがターゲットになります。*ServerGroupExcludingDb2* では、*TargetDb2* を除くサーバー上のすべてのデータベースがターゲットになります。

```powershell
Write-Output "Creating test target groups..."
# Create ServerGroup target group
$ServerGroup = $JobAgent | New-AzSqlElasticJobTargetGroup -Name 'ServerGroup'
$ServerGroup | Add-AzSqlElasticJobTarget -ServerName $TargetServerName -RefreshCredentialName $MasterCred.CredentialName

# Create ServerGroup with an exclusion of Db2
$ServerGroupExcludingDb2 = $JobAgent | New-AzSqlElasticJobTargetGroup -Name 'ServerGroupExcludingDb2'
$ServerGroupExcludingDb2 | Add-AzSqlElasticJobTarget -ServerName $TargetServerName -RefreshCredentialName $MasterCred.CredentialName
$ServerGroupExcludingDb2 | Add-AzSqlElasticJobTarget -ServerName $TargetServerName -Database $Db2.DatabaseName -Exclude
```

## <a name="create-a-job"></a>ジョブを作成する

```powershell
Write-Output "Creating a new job"
$JobName = "Job1"
$Job = $JobAgent | New-AzSqlElasticJob -Name $JobName -RunOnce
$Job
```

## <a name="create-a-job-step"></a>ジョブ ステップを作成する

この例では、ジョブを実行するための 2 つのジョブ ステップを定義します。 最初のジョブ ステップ (*step1*) では、ターゲット グループ *ServerGroup* 内のすべてのデータベースに新しいテーブル (*Step1Table*) を作成します。 2 番目のジョブ ステップ (*step2*) では、*TargetDb2* を除くすべてのデータベースに新しいテーブル (*Step2Table*) を作成します。これは、ターゲット グループの[事前の定義](#define-the-target-databases-you-want-to-run-the-job-against)によって除外することが指定されているためです。

```powershell
Write-Output "Creating job steps"
$SqlText1 = "IF NOT EXISTS (SELECT * FROM sys.tables WHERE object_id = object_id('Step1Table')) CREATE TABLE [dbo].[Step1Table]([TestId] [int] NOT NULL);"
$SqlText2 = "IF NOT EXISTS (SELECT * FROM sys.tables WHERE object_id = object_id('Step2Table')) CREATE TABLE [dbo].[Step2Table]([TestId] [int] NOT NULL);"

$Job | Add-AzSqlElasticJobStep -Name "step1" -TargetGroupName $ServerGroup.TargetGroupName -CredentialName $JobCred.CredentialName -CommandText $SqlText1
$Job | Add-AzSqlElasticJobStep -Name "step2" -TargetGroupName $ServerGroupExcludingDb2.TargetGroupName -CredentialName $JobCred.CredentialName -CommandText $SqlText2
```


## <a name="run-the-job"></a>ジョブを実行する

ジョブをすぐに開始するには、次のコマンドを実行します。

```powershell
Write-Output "Start a new execution of the job..."
$JobExecution = $Job | Start-AzSqlElasticJob
$JobExecution
```

正常に完了すると、TargetDb1 に新しいテーブルが 2 つ、TargetDb2 に新しいテーブルが 1 つだけ表示されます。


   ![SSMS での新しいテーブルの確認](media/elastic-jobs-overview/job-execution-verification.png)




## <a name="monitor-status-of-job-executions"></a>ジョブの実行状態を監視する

次のスニペットを実行すると、ジョブの実行の詳細を取得できます。

```powershell
# Get the latest 10 executions run
$JobAgent | Get-AzSqlElasticJobExecution -Count 10

# Get the job step execution details
$JobExecution | Get-AzSqlElasticJobStepExecution

# Get the job target execution details
$JobExecution | Get-AzSqlElasticJobTargetExecution -Count 2
```

### <a name="job-execution-states"></a>ジョブの実行状態

可能なジョブの実行状態を次の表に示します。

|状態|説明|
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

## <a name="schedule-the-job-to-run-later"></a>ジョブを後で実行するようにスケジュールする

特定の時刻にジョブを実行するようにスケジュールを設定するには、次のコマンドを実行します。

```powershell
# Run every hour starting from now
$Job | Set-AzSqlElasticJob -IntervalType Hour -IntervalCount 1 -StartTime (Get-Date) -Enable
```

## <a name="clean-up-resources"></a>リソースのクリーンアップ

リソース グループを削除して、このチュートリアルで作成した Azure リソースを削除します。

> [!TIP]
> 引き続きこれらのジョブを使用する場合は、この記事で作成したリソースをクリーンアップしないでください。 使用する予定がない場合は、次の手順を使用して、この記事で作成したすべてのリソースを削除してください。
>

```powershell
Remove-AzResourceGroup -ResourceGroupName $ResourceGroupName
```


## <a name="next-steps"></a>次の手順

このチュートリアルでは、データベースのセットに対して Transact-SQL スクリプトを実行しました。  次のタスクを実行する方法を学習しました。

> [!div class="checklist"]
> * エラスティック ジョブ エージェントを作成する
> * ジョブによってターゲット上でスクリプトを実行できるようにジョブ認証情報を作成する
> * ジョブを実行するターゲット (サーバー、エラスティック プール、データベース、シャード マップ) を定義する
> * エージェントが接続してジョブを実行できるように、ターゲット データベースにデータベース スコープ資格情報を作成する
> * ジョブを作成する
> * ジョブにジョブ ステップを追加する
> * ジョブの実行を開始する
> * ジョブの監視

> [!div class="nextstepaction"]
>[Transact-SQL を使用してエラスティック ジョブを管理する](elastic-jobs-tsql.md)
