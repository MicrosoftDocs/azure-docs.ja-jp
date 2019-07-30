---
title: Transact-SQL (T-SQL) を使用して Azure SQL Elastic Database ジョブを作成および管理する | Microsoft Docs
description: Transact-SQL (T-SQL) を使用する Elastic Database ジョブ エージェントでは多くのデータベース間でスクリプトを実行します。
services: sql-database
ms.service: sql-database
ms.subservice: scale-out
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
ms.author: jaredmoo
author: jaredmoo
ms.reviewer: sstein
manager: craigg
ms.date: 01/25/2019
ms.openlocfilehash: 683297e32c40f73c64dc40b18f279d92e2396e8d
ms.sourcegitcommit: 3107874d7559ea975e4d55ae33cdf45f4b5485e4
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/04/2019
ms.locfileid: "67568269"
---
# <a name="use-transact-sql-t-sql-to-create-and-manage-elastic-database-jobs"></a>Transact-SQL (T-SQL) を使用して Elastic Database ジョブを作成および管理する

この記事では、T-SQL を使用してエラスティック ジョブを使い始めるためのシナリオの例を多数提供します。

これらの例では、"[*ジョブ データベース*](sql-database-job-automation-overview.md#job-database)" で利用できる[ストアド プロシージャ](#job-stored-procedures)と[ビュー](#job-views)を使います。

ジョブの作成、構成、実行、管理には、Transact-SQL (T-SQL) を使います。 エラスティック ジョブ エージェントの作成は T-SQL ではサポートされていないので、最初に、ポータルまたは [PowerShell](elastic-jobs-powershell.md#create-the-elastic-job-agent) を使って "*エラスティック ジョブ エージェント*" を作成する必要があります。


## <a name="create-a-credential-for-job-execution"></a>ジョブ実行用の資格情報を作成する

スクリプトを実行するためにターゲット データベースに接続するには、資格情報を使います。 資格情報を使ってスクリプトを正常に実行するには、ターゲット グループによって指定されているデータベースに対する適切なアクセス許可が必要です。 サーバーやプールのターゲット グループ メンバーを使うときは、ジョブの実行時にサーバーやプールを展開する前に資格情報を更新するために使うマスター資格情報を作成することを強くお勧めします。 データベース スコープの資格情報は、ジョブ エージェント データベース上で作成されます。 ターゲット データベース上での "*ログインの作成*" と "*ログイン データベース アクセス許可を付与するためのログインからのユーザーの作成*" の両方に、同じ資格情報を使う必要があります。


```sql
--Connect to the job database specified when creating the job agent

-- Create a db master key if one does not already exist, using your own password.  
CREATE MASTER KEY ENCRYPTION BY PASSWORD='<EnterStrongPasswordHere>';  
  
-- Create a database scoped credential.  
CREATE DATABASE SCOPED CREDENTIAL myjobcred WITH IDENTITY = 'jobcred',
    SECRET = '<EnterStrongPasswordHere>'; 
GO

-- Create a database scoped credential for the master database of server1.
CREATE DATABASE SCOPED CREDENTIAL mymastercred WITH IDENTITY = 'mastercred',
    SECRET = '<EnterStrongPasswordHere>'; 
GO
```

## <a name="create-a-target-group-servers"></a>ターゲット グループ (サーバー) を作成する

次の例では、サーバーのすべてのデータベースに対してジョブを実行する方法を示します。  
"[*ジョブ データベース*](sql-database-job-automation-overview.md#job-database)" に接続して、次のコマンドを実行します。


```sql
-- Connect to the job database specified when creating the job agent

-- Add a target group containing server(s)
EXEC jobs.sp_add_target_group 'ServerGroup1'

-- Add a server target member
EXEC jobs.sp_add_target_group_member
'ServerGroup1',
@target_type = 'SqlServer',
@refresh_credential_name='mymastercred', --credential required to refresh the databases in server
@server_name='server1.database.windows.net'

--View the recently created target group and target group members
SELECT * FROM jobs.target_groups WHERE target_group_name='ServerGroup1';
SELECT * FROM jobs.target_group_members WHERE target_group_name='ServerGroup1';
```


## <a name="exclude-an-individual-database"></a>データベースを個別に除外する

次の例では、*MappingDB* という名前のデータベースを除く、SQL Database サーバーのすべてのデータベースに対してジョブを実行する方法を示します。  
"[*ジョブ データベース*](sql-database-job-automation-overview.md#job-database)" に接続して、次のコマンドを実行します。

```sql
--Connect to the job database specified when creating the job agent

-- Add a target group containing server(s)
EXEC [jobs].sp_add_target_group N'ServerGroup'
GO

-- Add a server target member
EXEC [jobs].sp_add_target_group_member
@target_group_name = N'ServerGroup',
@target_type = N'SqlServer',
@refresh_credential_name=N'mymastercred', --credential required to refresh the databases in server
@server_name=N'London.database.windows.net'
GO

-- Add a server target member
EXEC [jobs].sp_add_target_group_member
@target_group_name = N'ServerGroup',
@target_type = N'SqlServer',
@refresh_credential_name=N'mymastercred', --credential required to refresh the databases in server
@server_name='server2.database.windows.net'
GO

--Exclude a database target member from the server target group
EXEC [jobs].sp_add_target_group_member
@target_group_name = N'ServerGroup',
@membership_type = N'Exclude',
@target_type = N'SqlDatabase',
@server_name = N'server1.database.windows.net',
@database_name =N'MappingDB'
GO

--View the recently created target group and target group members
SELECT * FROM [jobs].target_groups WHERE target_group_name = N'ServerGroup';
SELECT * FROM [jobs].target_group_members WHERE target_group_name = N'ServerGroup';
```


## <a name="create-a-target-group-pools"></a>ターゲット グループ (プール) を作成する

次の例では、1 つまたは複数のエラスティック プール内のすべてのデータベースをターゲットにする方法を示します。  
"[*ジョブ データベース*](sql-database-job-automation-overview.md#job-database)" に接続して、次のコマンドを実行します。

```sql
--Connect to the job database specified when creating the job agent

-- Add a target group containing pool(s)
EXEC jobs.sp_add_target_group 'PoolGroup'

-- Add an elastic pool(s) target member
EXEC jobs.sp_add_target_group_member
'PoolGroup',
@target_type = 'SqlElasticPool',
@refresh_credential_name='mymastercred', --credential required to refresh the databases in server
@server_name='server1.database.windows.net',
@elastic_pool_name='ElasticPool-1'

-- View the recently created target group and target group members
SELECT * FROM jobs.target_groups WHERE target_group_name = N'PoolGroup';
SELECT * FROM jobs.target_group_members WHERE target_group_name = N'PoolGroup';
```


## <a name="deploy-new-schema-to-many-databases"></a>多数のデータベースに新しいスキーマをデプロイする

次の例では、すべてのデータベースに新しいスキーマをデプロイする方法を示します。  
"[*ジョブ データベース*](sql-database-job-automation-overview.md#job-database)" に接続して、次のコマンドを実行します。


```sql
--Connect to the job database specified when creating the job agent

--Add job for create table
EXEC jobs.sp_add_job @job_name='CreateTableTest', @description='Create Table Test'

-- Add job step for create table
EXEC jobs.sp_add_jobstep @job_name='CreateTableTest',
@command=N'IF NOT EXISTS (SELECT * FROM sys.tables 
            WHERE object_id = object_id(''Test''))
CREATE TABLE [dbo].[Test]([TestId] [int] NOT NULL);',
@credential_name='myjobcred',
@target_group_name='PoolGroup'
```


## <a name="data-collection-using-built-in-parameters"></a>組み込みパラメーターを使用したデータ収集

多くのデータ収集シナリオでは、以下のスクリプト変数を含めると、ジョブの結果の後処理に役立ちます。

- $(job_name)
- $(job_id)
- $(job_version)
- $(step_id)
- $(step_name)
- $(job_execution_id)
- $(job_execution_create_time)
- $(target_group_name)

たとえば、同じジョブ実行のすべての結果をグループ化するには、次のコマンドで示すように *$(job_execution_id)* を使います。


```sql
@command= N' SELECT DB_NAME() DatabaseName, $(job_execution_id) AS job_execution_id, * FROM sys.dm_db_resource_stats WHERE end_time > DATEADD(mi, -20, GETDATE());'
```


## <a name="monitor-database-performance"></a>データベース パフォーマンスの監視

次の例では、複数のデータベースからパフォーマンス データを収集する新しいジョブを作成します。

既定では、ジョブ エージェントは返された結果を格納するテーブルを作成しようとします。 そのため、出力資格情報に使用される資格情報に関連付けられたログインには、これを実行できる十分なアクセス許可が必要です。 事前にテーブルを手動で作成する場合は、次のプロパティが必要です。
1. 結果セットの正しい名前とデータ型を含む列。
2. データ型が uniqueidentifier である internal_execution_id 用の追加の列。
3. internal_execution_id 列上の `IX_<TableName>_Internal_Execution_ID` という名前の非クラスター化インデックス。

"[*ジョブ データベース*](sql-database-job-automation-overview.md#job-database)" に接続して、次のコマンドを実行します。

```sql
--Connect to the job database specified when creating the job agent

-- Add a job to collect perf results
EXEC jobs.sp_add_job @job_name ='ResultsJob', @description='Collection Performance data from all customers'

-- Add a job step w/ schedule to collect results
EXEC jobs.sp_add_jobstep
@job_name='ResultsJob',
@command= N' SELECT DB_NAME() DatabaseName, $(job_execution_id) AS job_execution_id, * FROM sys.dm_db_resource_stats WHERE end_time > DATEADD(mi, -20, GETDATE());',
@credential_name='myjobcred',
@target_group_name='PoolGroup',
@output_type='SqlDatabase',
@output_credential_name='myjobcred',
@output_server_name='server1.database.windows.net',
@output_database_name='<resultsdb>',
@output_table_name='<resutlstable>'
Create a job to monitor pool performance
--Connect to the job database specified when creating the job agent

-- Add a target group containing master database
EXEC jobs.sp_add_target_group 'MasterGroup'

-- Add a server target member
EXEC jobs.sp_add_target_group_member
@target_group_name='MasterGroup',
@target_type='SqlDatabase',
@server_name='server1.database.windows.net',
@database_name='master'

-- Add a job to collect perf results
EXEC jobs.sp_add_job
@job_name='ResultsPoolsJob',
@description='Demo: Collection Performance data from all pools',
@schedule_interval_type='Minutes',
@schedule_interval_count=15

-- Add a job step w/ schedule to collect results
EXEC jobs.sp_add_jobstep
@job_name='ResultsPoolsJob',
@command=N'declare @now datetime
DECLARE @startTime datetime
DECLARE @endTime datetime
DECLARE @poolLagMinutes datetime
DECLARE @poolStartTime datetime
DECLARE @poolEndTime datetime
SELECT @now = getutcdate ()
SELECT @startTime = dateadd(minute, -15, @now)
SELECT @endTime = @now
SELECT @poolStartTime = dateadd(minute, -30, @startTime)
SELECT @poolEndTime = dateadd(minute, -30, @endTime)

SELECT elastic_pool_name , end_time, elastic_pool_dtu_limit, avg_cpu_percent, avg_data_io_percent, avg_log_write_percent, max_worker_percent, max_session_percent,
        avg_storage_percent, elastic_pool_storage_limit_mb FROM sys.elastic_pool_resource_stats
        WHERE end_time > @poolStartTime and end_time <= @poolEndTime;
'),
@credential_name='myjobcred',
@target_group_name='MasterGroup',
@output_type='SqlDatabase',
@output_credential_name='myjobcred',
@output_server_name='server1.database.windows.net',
@output_database_name='resultsdb',
@output_table_name='resutlstable'
```


## <a name="view-job-definitions"></a>ジョブの定義を表示する

次の例では、現在のジョブの定義を表示する方法を示します。  
"[*ジョブ データベース*](sql-database-job-automation-overview.md#job-database)" に接続して、次のコマンドを実行します。

```sql
--Connect to the job database specified when creating the job agent

-- View all jobs
SELECT * FROM jobs.jobs

-- View the steps of the current version of all jobs
SELECT js.* FROM jobs.jobsteps js
JOIN jobs.jobs j 
  ON j.job_id = js.job_id AND j.job_version = js.job_version

-- View the steps of all versions of all jobs
select * from jobs.jobsteps
```


## <a name="begin-ad-hoc-execution-of-a-job"></a>ジョブのアドホックな実行を開始する

次の例では、ジョブをすぐに開始する方法を示します。  
"[*ジョブ データベース*](sql-database-job-automation-overview.md#job-database)" に接続して、次のコマンドを実行します。

```sql
--Connect to the job database specified when creating the job agent

-- Execute the latest version of a job
EXEC jobs.sp_start_job 'CreateTableTest'

-- Execute the latest version of a job and receive the execution id
declare @je uniqueidentifier
exec jobs.sp_start_job 'CreateTableTest', @job_execution_id = @je output
select @je

select * from jobs.job_executions where job_execution_id = @je

-- Execute a specific version of a job (e.g. version 1)
exec jobs.sp_start_job 'CreateTableTest', 1
```


## <a name="schedule-execution-of-a-job"></a>ジョブの実行をスケジュールする

次の例では、後で実行するようにジョブのスケジュールを設定する方法を示します。  
"[*ジョブ データベース*](sql-database-job-automation-overview.md#job-database)" に接続して、次のコマンドを実行します。

```sql
--Connect to the job database specified when creating the job agent

EXEC jobs.sp_update_job
@job_name='ResultsJob',
@enabled=1,
@schedule_interval_type='Minutes',
@schedule_interval_count=15
```

## <a name="monitor-job-execution-status"></a>ジョブの実行状態を監視する

次の例では、すべてのジョブの実行状態の詳細を表示する方法を示します。  
"[*ジョブ データベース*](sql-database-job-automation-overview.md#job-database)" に接続して、次のコマンドを実行します。

```sql
--Connect to the job database specified when creating the job agent

--View top-level execution status for the job named 'ResultsPoolJob'
SELECT * FROM jobs.job_executions 
WHERE job_name = 'ResultsPoolsJob' and step_id IS NULL
ORDER BY start_time DESC

--View all top-level execution status for all jobs
SELECT * FROM jobs.job_executions WHERE step_id IS NULL
ORDER BY start_time DESC

--View all execution statuses for job named 'ResultsPoolsJob'
SELECT * FROM jobs.job_executions 
WHERE job_name = 'ResultsPoolsJob' 
ORDER BY start_time DESC

-- View all active executions
SELECT * FROM jobs.job_executions 
WHERE is_active = 1
ORDER BY start_time DESC
```


## <a name="cancel-a-job"></a>ジョブを取り消す

次の例では、ジョブを取り消す方法を示します。  
"[*ジョブ データベース*](sql-database-job-automation-overview.md#job-database)" に接続して、次のコマンドを実行します。

```sql
--Connect to the job database specified when creating the job agent

-- View all active executions to determine job execution id
SELECT * FROM jobs.job_executions 
WHERE is_active = 1 AND job_name = 'ResultPoolsJob'
ORDER BY start_time DESC
GO

-- Cancel job execution with the specified job execution id
EXEC jobs.sp_stop_job '01234567-89ab-cdef-0123-456789abcdef'
```


## <a name="delete-old-job-history"></a>古いジョブの履歴を削除する

次の例では、特定の日付より前のジョブ履歴を削除する方法を示します。  
"[*ジョブ データベース*](sql-database-job-automation-overview.md#job-database)" に接続して、次のコマンドを実行します。

```sql
--Connect to the job database specified when creating the job agent

-- Delete history of a specific job’s executions older than the specified date
EXEC jobs.sp_purge_jobhistory @job_name='ResultPoolsJob', @oldest_date='2016-07-01 00:00:00'

--Note: job history is automatically deleted if it is >45 days old
```

## <a name="delete-a-job-and-all-its-job-history"></a>ジョブとそのすべてのジョブ履歴を削除する

次の例では、特定のジョブとそれに関連するすべてのジョブ履歴を削除する方法を示します。  
"[*ジョブ データベース*](sql-database-job-automation-overview.md#job-database)" に接続して、次のコマンドを実行します。

```sql
--Connect to the job database specified when creating the job agent

EXEC jobs.sp_delete_job @job_name='ResultsPoolsJob'

--Note: job history is automatically deleted if it is >45 days old
```




## <a name="job-stored-procedures"></a>ジョブのストアド プロシージャ

次のストアド プロシージャは、[ジョブ データベース](sql-database-job-automation-overview.md#job-database)内にあります。



|ストアド プロシージャ  |説明  |
|---------|---------|
|[sp_add_job](#sp_add_job)     |     新しいジョブを追加します。    |
|[sp_update_job](#sp_update_job)    |      既存のジョブを更新します。   |
|[sp_delete_job](#sp_delete_job)     |      既存のジョブを削除します。   |
|[sp_add_jobstep](#sp_add_jobstep)    |    ジョブにステップを追加します。     |
|[sp_update_jobstep](#sp_update_jobstep)     |     ジョブのステップを更新します。    |
|[sp_delete_jobstep](#sp_delete_jobstep)     |     ジョブのステップを削除します。    |
|[sp_start_job](#sp_start_job)    |  ジョブの実行を開始します。       |
|[sp_stop_job](#sp_stop_job)     |     ジョブの実行を停止します。   |
|[sp_add_target_group](#sp_add_target_group)    |     ターゲット グループを追加します。    |
|[sp_delete_target_group](#sp_delete_target_group)     |    ターゲット グループを削除します。     |
|[sp_add_target_group_member](#sp_add_target_group_member)     |    データベースまたはデータベースのグループをターゲット グループに追加します。     |
|[sp_delete_target_group_member](#sp_delete_target_group_member)     |     ターゲット グループかターゲット グループのメンバーを削除します。    |
|[sp_purge_jobhistory](#sp_purge_jobhistory)    |    ジョブの履歴レコードを削除します。     |





### <a name="sp_add_job"></a>sp_add_job

新しいジョブを追加します。 
  
#### <a name="syntax"></a>構文  
  

```sql
[jobs].sp_add_job [ @job_name = ] 'job_name'  
    [ , [ @description = ] 'description' ]   
    [ , [ @enabled = ] enabled ]
    [ , [ @schedule_interval_type = ] schedule_interval_type ]  
    [ , [ @schedule_interval_count = ] schedule_interval_count ]   
    [ , [ @schedule_start_time = ] schedule_start_time ]   
    [ , [ @schedule_end_time = ] schedule_end_time ]   
    [ , [ @job_id = ] job_id OUTPUT ]
```

  
#### <a name="arguments"></a>引数  

[ **\@job_name =** ] 'job_name'  
ジョブの名前。 名前は一意である必要があり、パーセント (%) 文字を含めることはできません。 job_name は nvarchar(128) であり、既定値はありません。

[ **\@description =** ] 'description'  
ジョブの説明。 description は nvarchar(512) であり、既定値は NULL です。 省略すると、空の文字列が使われます。

[ **\@enabled =** ] enabled  
ジョブのスケジュールを有効にするかどうか。 Enabled は bit であり、既定値は 0 (無効) です。 0 の場合、ジョブは有効ではなく、そのスケジュールどおりには実行されません。ただし、手動で実行することはできます。 1 の場合、ジョブはそのスケジュールに従って実行され、手動で実行することもできます。

[ **\@schedule_interval_type =** ] schedule_interval_type  
値はジョブが実行されるときを示します。 schedule_interval_type は nvarchar(50)、既定値は Once であり、次のいずれかの値を指定できます。
- 'Once'
- 'Minutes'
- 'Hours'
- 'Days'
- 'Weeks'
- 'Months'

[ **\@schedule_interval_count =** ] schedule_interval_count  
ジョブの各実行の間に発生する schedule_interval_count 期間の数。 schedule_interval_count は int であり、既定値は 1 です。 1 以上の値を指定する必要があります。

[ **\@schedule_start_time =** ] schedule_start_time  
ジョブの実行を開始できる日付。 schedule_start_time は DATETIME2 であり、既定値は 0001-01-01 00:00:00.0000000 です。

[ **\@schedule_end_time =** ] schedule_end_time  
ジョブの実行を停止できる日付。 schedule_end_time は DATETIME2 であり、既定値は 9999-12-31 11:59:59.0000000 です。 

[ **\@job_id =** ] job_id OUTPUT  
正常に作成された場合にジョブに割り当てられるジョブ識別番号。 job_id は、uniqueidentifier 型の出力変数です。

#### <a name="return-code-values"></a>リターン コードの値

0 (成功) または 1 (失敗)

#### <a name="remarks"></a>解説
sp_add_job は、ジョブ エージェント作成時に指定したジョブ エージェント データベースから実行する必要があります。
sp_add_job を実行してジョブを追加した後、sp_add_jobstep を使ってジョブのアクティビティを実行するステップを追加できます。 ジョブの初期バージョン番号は 0 で、最初のステップが追加されると 1 に増分されます。

#### <a name="permissions"></a>アクセス許可
既定では、sysadmin 固定サーバー ロールのメンバーは、このストアド プロシージャを実行できます。 これにより、ジョブの監視しか行えないようにユーザーが制限されます。そのユーザーには、ジョブ エージェントを作成するときに、指定されたジョブ エージェント データベースの次のデータベース ロールのいずれかを許可できます。

- jobs_reader

これらのロールのアクセス許可について詳しくは、このドキュメントの「アクセス許可」セクションをご覧ください。 このストアド プロシージャを使って、他のユーザーが所有するジョブの属性を編集できるのは、sysadmin のメンバーだけです。

### <a name="sp_update_job"></a>sp_update_job

既存のジョブを更新します。

#### <a name="syntax"></a>構文

```sql
[jobs].sp_update_job [ @job_name = ] 'job_name'  
    [ , [ @new_name = ] 'new_name' ]
    [ , [ @description = ] 'description' ]   
    [ , [ @enabled = ] enabled ]
    [ , [ @schedule_interval_type = ] schedule_interval_type ]  
    [ , [ @schedule_interval_count = ] schedule_interval_count ]   
    [ , [ @schedule_start_time = ] schedule_start_time ]   
    [ , [ @schedule_end_time = ] schedule_end_time ]   
```

#### <a name="arguments"></a>引数
[ **\@job_name =** ] 'job_name'  
更新するジョブの名前。 job_name は nvarchar(128) です。

[ **\@new_name =** ] 'new_name'  
ジョブの新しい名前。 new_name は nvarchar(128) です。

[ **\@description =** ] 'description'  
ジョブの説明。 description は nvarchar(512) です。

[ **\@enabled =** ] enabled  
ジョブのスケジュールが有効か (1) 無効か (0) を指定します。 enabled は bit です。

[ **\@schedule_interval_type=** ] schedule_interval_type  
値はジョブが実行されるときを示します。 schedule_interval_type は nvarchar(50) であり、次のいずれかの値を指定できます。

- 'Once'
- 'Minutes'
- 'Hours'
- 'Days'
- 'Weeks'
- 'Months'

[ **\@schedule_interval_count=** ] schedule_interval_count  
ジョブの各実行の間に発生する schedule_interval_count 期間の数。 schedule_interval_count は int であり、既定値は 1 です。 1 以上の値を指定する必要があります。

[ **\@schedule_start_time=** ] schedule_start_time  
ジョブの実行を開始できる日付。 schedule_start_time は DATETIME2 であり、既定値は 0001-01-01 00:00:00.0000000 です。

[ **\@schedule_end_time=** ] schedule_end_time  
ジョブの実行を停止できる日付。 schedule_end_time は DATETIME2 であり、既定値は 9999-12-31 11:59:59.0000000 です。 

#### <a name="return-code-values"></a>リターン コードの値
0 (成功) または 1 (失敗)

#### <a name="remarks"></a>解説
sp_add_job を実行してジョブを追加した後、sp_add_jobstep を使ってジョブのアクティビティを実行するステップを追加できます。 ジョブの初期バージョン番号は 0 で、最初のステップが追加されると 1 に増分されます。

#### <a name="permissions"></a>アクセス許可
既定では、sysadmin 固定サーバー ロールのメンバーは、このストアド プロシージャを実行できます。 これにより、ジョブの監視しか行えないようにユーザーが制限されます。そのユーザーには、ジョブ エージェントを作成するときに、指定されたジョブ エージェント データベースの次のデータベース ロールのいずれかを許可できます。
- jobs_reader

これらのロールのアクセス許可について詳しくは、このドキュメントの「アクセス許可」セクションをご覧ください。 このストアド プロシージャを使って、他のユーザーが所有するジョブの属性を編集できるのは、sysadmin のメンバーだけです。



### <a name="sp_delete_job"></a>sp_delete_job

既存のジョブを削除します。

#### <a name="syntax"></a>構文

```sql
[jobs].sp_delete_job [ @job_name = ] 'job_name'
    [ , [ @force = ] force ]
```

#### <a name="arguments"></a>引数
[ **\@job_name =** ] 'job_name'  
削除するジョブの名前。 job_name は nvarchar(128) です。

[ **\@force =** ] force  
ジョブに進行中の実行がある場合に削除するかどうかを指定します。進行中のすべての実行を取り消すか (1)、または進行中のジョブ実行がある場合は削除しません (0)。 force は bit です。

#### <a name="return-code-values"></a>リターン コードの値
0 (成功) または 1 (失敗)

#### <a name="remarks"></a>解説
ジョブを削除すると、ジョブ履歴も自動的に削除されます。

#### <a name="permissions"></a>アクセス許可
既定では、sysadmin 固定サーバー ロールのメンバーは、このストアド プロシージャを実行できます。 これにより、ジョブの監視しか行えないようにユーザーが制限されます。そのユーザーには、ジョブ エージェントを作成するときに、指定されたジョブ エージェント データベースの次のデータベース ロールのいずれかを許可できます。
- jobs_reader

これらのロールのアクセス許可について詳しくは、このドキュメントの「アクセス許可」セクションをご覧ください。 このストアド プロシージャを使って、他のユーザーが所有するジョブの属性を編集できるのは、sysadmin のメンバーだけです。



### <a name="sp_add_jobstep"></a>sp_add_jobstep

ジョブにステップを追加します。

#### <a name="syntax"></a>構文


```sql
[jobs].sp_add_jobstep [ @job_name = ] 'job_name'   
     [ , [ @step_id = ] step_id ]   
     [ , [ @step_name = ] step_name ]   
     [ , [ @command_type = ] 'command_type' ]   
     [ , [ @command_source = ] 'command_source' ]  
     , [ @command = ] 'command'
     , [ @credential_name = ] 'credential_name'
     , [ @target_group_name = ] 'target_group_name'
     [ , [ @initial_retry_interval_seconds = ] initial_retry_interval_seconds ]   
     [ , [ @maximum_retry_interval_seconds = ] maximum_retry_interval_seconds ]   
     [ , [ @retry_interval_backoff_multiplier = ] retry_interval_backoff_multiplier ]   
     [ , [ @retry_attempts = ] retry_attempts ]   
     [ , [ @step_timeout_seconds = ] step_timeout_seconds ]   
     [ , [ @output_type = ] 'output_type' ]   
     [ , [ @output_credential_name = ] 'output_credential_name' ]   
     [ , [ @output_subscription_id = ] 'output_subscription_id' ]   
     [ , [ @output_resource_group_name = ] 'output_resource_group_name' ]   
     [ , [ @output_server_name = ] 'output_server_name' ]   
     [ , [ @output_database_name = ] 'output_database_name' ]   
     [ , [ @output_schema_name = ] 'output_schema_name' ]   
     [ , [ @output_table_name = ] 'output_table_name' ]
     [ , [ @job_version = ] job_version OUTPUT ]
     [ , [ @max_parallelism = ] max_parallelism ]
```

#### <a name="arguments"></a>引数

[ **\@job_name =** ] 'job_name'  
ステップを追加するジョブの名前。 job_name は nvarchar(128) です。

[ **\@step_id =** ] step_id  
ジョブ ステップのシーケンス ID 番号。 ステップ ID 番号は 1 から始まり、隙間なく増加します。 指定した ID を持つステップが既に存在する場合は、既存のステップとそれ以降のすべてのステップの ID が増分されて、新しいステップをシーケンスに挿入できるようになります。 step_id を指定しないと、ステップのシーケンスの最後に自動的に割り当てられます。 step_id は int です。

[ **\@step_name =** ] step_name  
ステップの名前。 最初のステップを除き、必ず指定する必要があります。最初のステップは (利便性のため)、既定の名前 "JobStep" になります。 step_name は nvarchar(128) です。

[ **\@command_type =** ] 'command_type'  
このジョブ ステップで実行されるコマンドの種類。 command_type は nvarchar(50) です。既定値は TSql であり、@command_type パラメーターの値が T-SQL スクリプトであることを意味します。

指定する場合、値は TSql である必要があります。

[ **\@command_source =** ] 'command_source'  
コマンドが格納される場所の種類。 command_source は nvarchar(50) です。既定値は Inline であり、@command_source パラメーターの値がコマンドのリテラル テキストであることを意味します。

指定する場合、値は Inline である必要があります。

[ **\@command =** ] 'command'  
コマンドは、有効な T-SQL スクリプトでなければならず、このジョブ ステップによって実行されます。 command は nvarchar(max) であり、既定値は NULL です。

[ **\@credential_name =** ] 'credential_name'  
このジョブ管理データベースに格納される、データベース スコープの資格情報の名前。このステップの実行時に、ターゲット グループ内の各ターゲット データベースに接続するために使われます。 credential_name は nvarchar(128) です。

[ **\@target_group_name =** ] 'target-group_name'  
ジョブ ステップが実行されるターゲット データベースを含むターゲット グループの名前。 target_group_name は nvarchar(128) です。

[ **\@initial_retry_interval_seconds =** ] initial_retry_interval_seconds  
ジョブ ステップの最初の実行の試行が失敗した場合に、最初の再試行を試みるまでの遅延。 initial_retry_interval_seconds は int であり、既定値は 1 です。

[ **\@maximum_retry_interval_seconds =** ] maximum_retry_interval_seconds  
再試行の間の最大遅延。 再試行の間の遅延がこの値より大きくなった場合は、この値に制限されます。 maximum_retry_interval_seconds は int であり、既定値は 120 です。

[ **\@retry_interval_backoff_multiplier =** ] retry_interval_backoff_multiplier  
ジョブ ステップの実行が複数回にわたり失敗した場合に、再試行の遅延に適用する乗数。 たとえば、最初の再試行の遅延が 5 秒で、バックオフ乗数が 2.0 の場合、2 回目の再試行の遅延は 10 秒、3 回目の再試行の遅延は 20 秒になります。 retry_interval_backoff_multiplier は real では、既定値は 2.0 です。

[ **\@retry_attempts =** ] retry_attempts  
最初の試行が失敗した場合に、実行を再試行する回数。 たとえば、retry_attempts の値が 10 の場合、最初の試行 1 回と再試行 10 回で、実行は合計 11 回試行されます。 最後の再試行が失敗した場合、ジョブの実行は Failed のライフサイクルで終了します。 retry_attempts は int であり、既定値は 10 です。

[ **\@step_timeout_seconds =** ] step_timeout_seconds  
ステップの実行に許可されている最大時間。 この時間を超えた場合、ジョブの実行は TimedOut のライフサイクルで終了します。 step_timeout_seconds は int であり、既定値は 43,200 秒 (12 時間) です。

[ **\@output_type =** ] 'output_type'  
null ではない場合は、コマンドの最初の結果セットの書き込み先の種類。 output_type は nvarchar(50) であり、既定値は NULL です。

指定する場合、値は SqlDatabase である必要があります。

[ **\@output_credential_name =** ] 'output_credential_name'  
null ではない場合は、出力先データベースへの接続に使用するデータベース スコープの資格情報の名前。 output_type が SqlDatabase の場合は、指定する必要があります。 output_credential_name は nvarchar(128) であり、既定値は NULL です。

[ **\@output_subscription_id =** ] 'output_subscription_id'  
説明が必要です。

[ **\@output_resource_group_name =** ] 'output_resource_group_name'  
説明が必要です。

[ **\@output_server_name =** ] 'output_server_name'  
null ではない場合は、出力先データベースを含むサーバーの完全修飾 DNS 名。 output_type が SqlDatabase の場合は、指定する必要があります。 output_server_name は nvarchar(256) であり、既定値は NULL です。

[ **\@output_database_name =** ] 'output_database_name'  
null ではない場合は、出力先テーブルを含むデータベースの名前。 output_type が SqlDatabase の場合は、指定する必要があります。 output_database_name は nvarchar(128) であり、既定値は NULL です。

[ **\@output_schema_name =** ] 'output_schema_name'  
null ではない場合は、出力先テーブルを含む SQL スキーマの名前。 output_type が SqlDatabase の場合、既定値は dbo です。 output_schema_name は nvarchar (128) です。

[ **\@output_table_name =** ] 'output_table_name'  
null ではない場合は、コマンドの最初の結果セットの書き込み先のテーブルの名前。 テーブルがまだ存在しない場合は、返される結果セットのスキーマに基づいて作成されます。 output_type が SqlDatabase の場合は、指定する必要があります。 output_table_name は nvarchar(128) であり、既定値は NULL です。

[ **\@job_version =** ] job_version OUTPUT  
新しいジョブのバージョン番号が割り当てられる出力パラメーター。 job_version は int です。

[ **\@max_parallelism =** ] max_parallelism OUTPUT  
エラスティック プールあたりの並列処理の最大レベル。 設定した場合、ジョブ ステップはエラスティック プールあたり最大でもその指定数のデータベースでのみ実行するように制限されます。 この設定は、ターゲット グループに直接含まれる、またはターゲット グループに含まれるサーバー内に存在する、各エラスティック プールに適用されます。 max_parallelism は int です。


#### <a name="return-code-values"></a>リターン コードの値
0 (成功) または 1 (失敗)

#### <a name="remarks"></a>解説
sp_add_jobstep が成功した場合、ジョブの現在のバージョン番号が増分されます。 次にジョブを実行するときは、新しいバージョンが使われます。 ジョブが現在実行中の場合、その実行には新しいステップは含まれません。

#### <a name="permissions"></a>アクセス許可
既定では、sysadmin 固定サーバー ロールのメンバーは、このストアド プロシージャを実行できます。 これにより、ジョブの監視しか行えないようにユーザーが制限されます。そのユーザーには、ジョブ エージェントを作成するときに、指定されたジョブ エージェント データベースの次のデータベース ロールのいずれかを許可できます。  

- jobs_reader

これらのロールのアクセス許可について詳しくは、このドキュメントの「アクセス許可」セクションをご覧ください。 このストアド プロシージャを使って、他のユーザーが所有するジョブの属性を編集できるのは、sysadmin のメンバーだけです。



### <a name="sp_update_jobstep"></a>sp_update_jobstep

ジョブのステップを更新します。

#### <a name="syntax"></a>構文

```sql
[jobs].sp_update_jobstep [ @job_name = ] 'job_name'   
     [ , [ @step_id = ] step_id ]   
     [ , [ @step_name = ] 'step_name' ]   
     [ , [ @new_id = ] new_id ]   
     [ , [ @new_name = ] 'new_name' ]   
     [ , [ @command_type = ] 'command_type' ]   
     [ , [ @command_source = ] 'command_source' ]  
     , [ @command = ] 'command'
     , [ @credential_name = ] 'credential_name'
     , [ @target_group_name = ] 'target_group_name'
     [ , [ @initial_retry_interval_seconds = ] initial_retry_interval_seconds ]   
     [ , [ @maximum_retry_interval_seconds = ] maximum_retry_interval_seconds ]   
     [ , [ @retry_interval_backoff_multiplier = ] retry_interval_backoff_multiplier ]   
     [ , [ @retry_attempts = ] retry_attempts ]   
     [ , [ @step_timeout_seconds = ] step_timeout_seconds ]   
     [ , [ @output_type = ] 'output_type' ]   
     [ , [ @output_credential_name = ] 'output_credential_name' ]   
     [ , [ @output_server_name = ] 'output_server_name' ]   
     [ , [ @output_database_name = ] 'output_database_name' ]   
     [ , [ @output_schema_name = ] 'output_schema_name' ]   
     [ , [ @output_table_name = ] 'output_table_name' ]   
     [ , [ @job_version = ] job_version OUTPUT ]
     [ , [ @max_parallelism = ] max_parallelism ]
```

#### <a name="arguments"></a>引数
[ **\@job_name =** ] 'job_name'  
ステップが属するジョブの名前。 job_name は nvarchar(128) です。

[ **\@step_id =** ] step_id  
変更するジョブ ステップの ID 番号。 step_id または step_name のいずれかを指定する必要があります。 step_id は int です。

[ **\@step_name =** ] 'step_name'  
変更するステップの名前。 step_id または step_name のいずれかを指定する必要があります。 step_name は nvarchar(128) です。

[ **\@new_id =** ] new_id  
ジョブ ステップの新しいシーケンス ID 番号。 ステップ ID 番号は 1 から始まり、隙間なく増加します。 ステップの順序を変更する場合、他のステップの番号は自動的に変更されます。

[ **\@new_name =** ] 'new_name'  
ステップの新しい名前。 new_name は nvarchar(128) です。

[ **\@command_type =** ] 'command_type'  
このジョブ ステップで実行されるコマンドの種類。 command_type は nvarchar(50) です。既定値は TSql であり、@command_type パラメーターの値が T-SQL スクリプトであることを意味します。

指定する場合、値は TSql である必要があります。

[ **\@command_source =** ] 'command_source'  
コマンドが格納される場所の種類。 command_source は nvarchar(50) です。既定値は Inline であり、@command_source パラメーターの値がコマンドのリテラル テキストであることを意味します。

指定する場合、値は Inline である必要があります。

[ **\@command =** ] 'command'  
コマンドは、有効な T-SQL スクリプトでなければならず、このジョブ ステップによって実行されます。 command は nvarchar(max) であり、既定値は NULL です。

[ **\@credential_name =** ] 'credential_name'  
このジョブ管理データベースに格納される、データベース スコープの資格情報の名前。このステップの実行時に、ターゲット グループ内の各ターゲット データベースに接続するために使われます。 credential_name は nvarchar(128) です。

[ **\@target_group_name =** ] 'target-group_name'  
ジョブ ステップが実行されるターゲット データベースを含むターゲット グループの名前。 target_group_name は nvarchar(128) です。

[ **\@initial_retry_interval_seconds =** ] initial_retry_interval_seconds  
ジョブ ステップの最初の実行の試行が失敗した場合に、最初の再試行を試みるまでの遅延。 initial_retry_interval_seconds は int であり、既定値は 1 です。

[ **\@maximum_retry_interval_seconds =** ] maximum_retry_interval_seconds  
再試行の間の最大遅延。 再試行の間の遅延がこの値より大きくなった場合は、この値に制限されます。 maximum_retry_interval_seconds は int であり、既定値は 120 です。

[ **\@retry_interval_backoff_multiplier =** ] retry_interval_backoff_multiplier  
ジョブ ステップの実行が複数回にわたり失敗した場合に、再試行の遅延に適用する乗数。 たとえば、最初の再試行の遅延が 5 秒で、バックオフ乗数が 2.0 の場合、2 回目の再試行の遅延は 10 秒、3 回目の再試行の遅延は 20 秒になります。 retry_interval_backoff_multiplier は real では、既定値は 2.0 です。

[ **\@retry_attempts =** ] retry_attempts  
最初の試行が失敗した場合に、実行を再試行する回数。 たとえば、retry_attempts の値が 10 の場合、最初の試行 1 回と再試行 10 回で、実行は合計 11 回試行されます。 最後の再試行が失敗した場合、ジョブの実行は Failed のライフサイクルで終了します。 retry_attempts は int であり、既定値は 10 です。

[ **\@step_timeout_seconds =** ] step_timeout_seconds  
ステップの実行に許可されている最大時間。 この時間を超えた場合、ジョブの実行は TimedOut のライフサイクルで終了します。 step_timeout_seconds は int であり、既定値は 43,200 秒 (12 時間) です。

[ **\@output_type =** ] 'output_type'  
null ではない場合は、コマンドの最初の結果セットの書き込み先の種類。 output_type の値を NULL にリセットするには、このパラメーターの値を '' (空の文字列) に設定します。 output_type は nvarchar(50) であり、既定値は NULL です。

指定する場合、値は SqlDatabase である必要があります。

[ **\@output_credential_name =** ] 'output_credential_name'  
null ではない場合は、出力先データベースへの接続に使用するデータベース スコープの資格情報の名前。 output_type が SqlDatabase の場合は、指定する必要があります。 output_credential_name の値を NULL にリセットするには、このパラメーターの値を '' (空の文字列) に設定します。 output_credential_name は nvarchar(128) であり、既定値は NULL です。

[ **\@output_server_name =** ] 'output_server_name'  
null ではない場合は、出力先データベースを含むサーバーの完全修飾 DNS 名。 output_type が SqlDatabase の場合は、指定する必要があります。 output_server_name の値を NULL にリセットするには、このパラメーターの値を '' (空の文字列) に設定します。 output_server_name は nvarchar(256) であり、既定値は NULL です。

[ **\@output_database_name =** ] 'output_database_name'  
null ではない場合は、出力先テーブルを含むデータベースの名前。 output_type が SqlDatabase の場合は、指定する必要があります。 output_database_name の値を NULL にリセットするには、このパラメーターの値を '' (空の文字列) に設定します。 output_database_name は nvarchar(128) であり、既定値は NULL です。

[ **\@output_schema_name =** ] 'output_schema_name'  
null ではない場合は、出力先テーブルを含む SQL スキーマの名前。 output_type が SqlDatabase の場合、既定値は dbo です。 output_schema_name の値を NULL にリセットするには、このパラメーターの値を '' (空の文字列) に設定します。 output_schema_name は nvarchar (128) です。

[ **\@output_table_name =** ] 'output_table_name'  
null ではない場合は、コマンドの最初の結果セットの書き込み先のテーブルの名前。 テーブルがまだ存在しない場合は、返される結果セットのスキーマに基づいて作成されます。 output_type が SqlDatabase の場合は、指定する必要があります。 output_server_name の値を NULL にリセットするには、このパラメーターの値を '' (空の文字列) に設定します。 output_table_name は nvarchar(128) であり、既定値は NULL です。

[ **\@job_version =** ] job_version OUTPUT  
新しいジョブのバージョン番号が割り当てられる出力パラメーター。 job_version は int です。

[ **\@max_parallelism =** ] max_parallelism OUTPUT  
エラスティック プールあたりの並列処理の最大レベル。 設定した場合、ジョブ ステップはエラスティック プールあたり最大でもその指定数のデータベースでのみ実行するように制限されます。 この設定は、ターゲット グループに直接含まれる、またはターゲット グループに含まれるサーバー内に存在する、各エラスティック プールに適用されます。 max_parallelism の値を null にリセットするには、このパラメーターの値を -1 に設定します。 max_parallelism は int です。


#### <a name="return-code-values"></a>リターン コードの値
0 (成功) または 1 (失敗)

#### <a name="remarks"></a>解説
ジョブの進行中の実行には影響しません。 sp_update_jobstep が成功した場合、ジョブのバージョン番号が増分されます。 次にジョブを実行するときは、新しいバージョンが使われます。

#### <a name="permissions"></a>アクセス許可
既定では、sysadmin 固定サーバー ロールのメンバーは、このストアド プロシージャを実行できます。 これにより、ジョブの監視しか行えないようにユーザーが制限されます。そのユーザーには、ジョブ エージェントを作成するときに、指定されたジョブ エージェント データベースの次のデータベース ロールのいずれかを許可できます。

- jobs_reader

これらのロールのアクセス許可について詳しくは、このドキュメントの「アクセス許可」セクションをご覧ください。 このストアド プロシージャを使って、他のユーザーが所有するジョブの属性を編集できるのは、sysadmin のメンバーだけです。




### <a name="sp_delete_jobstep"></a>sp_delete_jobstep

ジョブからジョブ ステップを削除します。

#### <a name="syntax"></a>構文


```sql
[jobs].sp_delete_jobstep [ @job_name = ] 'job_name'   
     [ , [ @step_id = ] step_id ]
     [ , [ @step_name = ] 'step_name' ]   
     [ , [ @job_version = ] job_version OUTPUT ]
```

#### <a name="arguments"></a>引数
[ **\@job_name =** ] 'job_name'  
ステップを削除するジョブの名前。 job_name は nvarchar(128) であり、既定値はありません。

[ **\@step_id =** ] step_id  
削除するジョブ ステップの ID 番号。 step_id または step_name のいずれかを指定する必要があります。 step_id は int です。

[ **\@step_name =** ] 'step_name'  
削除するステップの名前。 step_id または step_name のいずれかを指定する必要があります。 step_name は nvarchar(128) です。

[ **\@job_version =** ] job_version OUTPUT  
新しいジョブのバージョン番号が割り当てられる出力パラメーター。 job_version は int です。

#### <a name="return-code-values"></a>リターン コードの値
0 (成功) または 1 (失敗)

#### <a name="remarks"></a>解説
ジョブの進行中の実行には影響しません。 sp_update_jobstep が成功した場合、ジョブのバージョン番号が増分されます。 次にジョブを実行するときは、新しいバージョンが使われます。

削除されたジョブ ステップによって残された隙間を埋めるため、他のジョブ ステップの番号が自動的に変更されます。
 
#### <a name="permissions"></a>アクセス許可
既定では、sysadmin 固定サーバー ロールのメンバーは、このストアド プロシージャを実行できます。 これにより、ジョブの監視しか行えないようにユーザーが制限されます。そのユーザーには、ジョブ エージェントを作成するときに、指定されたジョブ エージェント データベースの次のデータベース ロールのいずれかを許可できます。
- jobs_reader

これらのロールのアクセス許可について詳しくは、このドキュメントの「アクセス許可」セクションをご覧ください。 このストアド プロシージャを使って、他のユーザーが所有するジョブの属性を編集できるのは、sysadmin のメンバーだけです。






### <a name="sp_start_job"></a>sp_start_job

ジョブの実行を開始します。

#### <a name="syntax"></a>構文


```sql
[jobs].sp_start_job [ @job_name = ] 'job_name'   
     [ , [ @job_execution_id = ] job_execution_id OUTPUT ]   
```

#### <a name="arguments"></a>引数
[ **\@job_name =** ] 'job_name'  
ステップを削除するジョブの名前。 job_name は nvarchar(128) であり、既定値はありません。

[ **\@job_execution_id =** ] job_execution_id OUTPUT  
ジョブ実行の ID が割り当てられる出力パラメーター。job_version は uniqueidentifier です。

#### <a name="return-code-values"></a>リターン コードの値
0 (成功) または 1 (失敗)

#### <a name="remarks"></a>解説
なし。
 
#### <a name="permissions"></a>アクセス許可
既定では、sysadmin 固定サーバー ロールのメンバーは、このストアド プロシージャを実行できます。 これにより、ジョブの監視しか行えないようにユーザーが制限されます。そのユーザーには、ジョブ エージェントを作成するときに、指定されたジョブ エージェント データベースの次のデータベース ロールのいずれかを許可できます。
- jobs_reader

これらのロールのアクセス許可について詳しくは、このドキュメントの「アクセス許可」セクションをご覧ください。 このストアド プロシージャを使って、他のユーザーが所有するジョブの属性を編集できるのは、sysadmin のメンバーだけです。

### <a name="sp_stop_job"></a>sp_stop_job

ジョブの実行を停止します。

#### <a name="syntax"></a>構文


```sql
[jobs].sp_stop_job [ @job_execution_id = ] ' job_execution_id '
```


#### <a name="arguments"></a>引数
[ **\@job_execution_id =** ] job_execution_id  
停止するジョブ実行の ID 番号。 job_execution_id は uniqueidentifier であり、既定値は NULL です。

#### <a name="return-code-values"></a>リターン コードの値
0 (成功) または 1 (失敗)

#### <a name="remarks"></a>解説
なし。
 
#### <a name="permissions"></a>アクセス許可
既定では、sysadmin 固定サーバー ロールのメンバーは、このストアド プロシージャを実行できます。 これにより、ジョブの監視しか行えないようにユーザーが制限されます。そのユーザーには、ジョブ エージェントを作成するときに、指定されたジョブ エージェント データベースの次のデータベース ロールのいずれかを許可できます。
- jobs_reader

これらのロールのアクセス許可について詳しくは、このドキュメントの「アクセス許可」セクションをご覧ください。 このストアド プロシージャを使って、他のユーザーが所有するジョブの属性を編集できるのは、sysadmin のメンバーだけです。


### <a name="sp_add_target_group"></a>sp_add_target_group

ターゲット グループを追加します。

#### <a name="syntax"></a>構文


```sql
[jobs].sp_add_target_group [ @target_group_name = ] 'target_group_name'   
     [ , [ @target_group_id = ] target_group_id OUTPUT ]
```


#### <a name="arguments"></a>引数
[ **\@target_group_name =** ] 'target_group_name'  
作成するターゲット グループの名前。 target_group_name は nvarchar(128) であり、既定値はありません。

[ **\@target_group_id =** ] target_group_id OUTPUT 正常に作成された場合にジョブに割り当てられるターゲット グループの ID 番号。 target_group_id は uniqueidentifier 型の出力変数であり、既定値は NULL です。

#### <a name="return-code-values"></a>リターン コードの値
0 (成功) または 1 (失敗)

#### <a name="remarks"></a>解説
ターゲット グループを使うと、簡単にデータベースのコレクションをジョブのターゲットにできます。

#### <a name="permissions"></a>アクセス許可
既定では、sysadmin 固定サーバー ロールのメンバーは、このストアド プロシージャを実行できます。 これにより、ジョブの監視しか行えないようにユーザーが制限されます。そのユーザーには、ジョブ エージェントを作成するときに、指定されたジョブ エージェント データベースの次のデータベース ロールのいずれかを許可できます。
- jobs_reader

これらのロールのアクセス許可について詳しくは、このドキュメントの「アクセス許可」セクションをご覧ください。 このストアド プロシージャを使って、他のユーザーが所有するジョブの属性を編集できるのは、sysadmin のメンバーだけです。

### <a name="sp_delete_target_group"></a>sp_delete_target_group

ターゲット グループを削除します。

#### <a name="syntax"></a>構文


```sql
[jobs].sp_delete_target_group [ @target_group_name = ] 'target_group_name'
```


#### <a name="arguments"></a>引数
[ **\@target_group_name =** ] 'target_group_name'  
削除するターゲット グループの名前。 target_group_name は nvarchar(128) であり、既定値はありません。

#### <a name="return-code-values"></a>リターン コードの値
0 (成功) または 1 (失敗)

#### <a name="remarks"></a>解説
なし。

#### <a name="permissions"></a>アクセス許可
既定では、sysadmin 固定サーバー ロールのメンバーは、このストアド プロシージャを実行できます。 これにより、ジョブの監視しか行えないようにユーザーが制限されます。そのユーザーには、ジョブ エージェントを作成するときに、指定されたジョブ エージェント データベースの次のデータベース ロールのいずれかを許可できます。
- jobs_reader

これらのロールのアクセス許可について詳しくは、このドキュメントの「アクセス許可」セクションをご覧ください。 このストアド プロシージャを使って、他のユーザーが所有するジョブの属性を編集できるのは、sysadmin のメンバーだけです。

### <a name="sp_add_target_group_member"></a>sp_add_target_group_member

データベースまたはデータベースのグループをターゲット グループに追加します。

#### <a name="syntax"></a>構文

```sql
[jobs].sp_add_target_group_member [ @target_group_name = ] 'target_group_name'
         [ @membership_type = ] 'membership_type' ]   
        [ , [ @target_type = ] 'target_type' ]   
        [ , [ @refresh_credential_name = ] 'refresh_credential_name' ]   
        [ , [ @server_name = ] 'server_name' ]   
        [ , [ @database_name = ] 'database_name' ]   
        [ , [ @elastic_pool_name = ] 'elastic_pool_name' ]   
        [ , [ @shard_map_name = ] 'shard_map_name' ]   
        [ , [ @target_id = ] 'target_id' OUTPUT ]
```

#### <a name="arguments"></a>引数
[ **\@target_group_name =** ] 'target_group_name'  
メンバーを追加するターゲット グループの名前。 target_group_name は nvarchar(128) であり、既定値はありません。

[ **\@membership_type =** ] 'membership_type'  
ターゲット グループのメンバーが含まれるか除外されるかを指定します。 target_group_name は nvarchar(128) であり、既定値は 'Include' です。 target_group_name の有効な値は、'Include' または 'Exclude' です。

[ **\@target_type =** ] 'target_type'  
ターゲット データベースまたはデータベースのコレクションの種類。サーバー内のすべてのデータベース、エラスティック プール内のすべてのデータベース、シャード マップ内のすべてのデータベース、または個々のデータベースです。 target_type は nvarchar(128) であり、既定値はありません。 target_type の有効な値は、'SqlServer'、'SqlElasticPool'、'SqlDatabase'、または 'SqlShardMap' です。 

[ **\@refresh_credential_name =** ] 'refresh_credential_name'  
SQL Database サーバーの名前。 refresh_credential_name は nvarchar(128) であり、既定値はありません。

[ **\@server_name =** ] 'server_name'  
指定したターゲット グループに追加する必要のある SQL Database サーバーの名前。 target_type が 'SqlServer' の場合は、server_name を指定する必要があります。 server_name は nvarchar(128) であり、既定値はありません。

[ **\@database_name =** ] 'database_name'  
指定したターゲット グループに追加する必要があるデータベースの名前。 target_type が 'SqlDatabase' の場合は、database_name を指定する必要があります。 database_name は nvarchar(128) であり、既定値はありません。

[ **\@elastic_pool_name =** ] 'elastic_pool_name'  
指定したターゲット グループに追加する必要のあるエラスティック プールの名前。 target_type が 'SqlElasticPool' の場合は、elastic_pool_name を指定する必要があります。 elastic_pool_name は nvarchar(128) であり、既定値はありません。

[ **\@shard_map_name =** ] 'shard_map_name'  
指定したターゲット グループに追加する必要があるシャード マップ プールの名前。 target_type が 'SqlSqlShardMap' の場合は、shard_map_name を指定する必要があります。 shard_map_name は nvarchar(128) であり、既定値はありません。

[ **\@target_id =** ] target_group_id OUTPUT  
ターゲット グループに追加された場合にターゲット グループ メンバーに割り当てられるターゲット ID 番号。 target_id は uniqueidentifier 型の出力変数であり、既定値は NULL です。
リターン コードの値 0 (成功) または 1 (失敗)

#### <a name="remarks"></a>解説
SQL Database サーバーまたはエラスティック プールがターゲット グループに含まれている場合、実行時には SQL Database サーバーまたはエラスティック プール内のすべての単一データベースでジョブが実行されます。

#### <a name="permissions"></a>アクセス許可
既定では、sysadmin 固定サーバー ロールのメンバーは、このストアド プロシージャを実行できます。 これにより、ジョブの監視しか行えないようにユーザーが制限されます。そのユーザーには、ジョブ エージェントを作成するときに、指定されたジョブ エージェント データベースの次のデータベース ロールのいずれかを許可できます。
- jobs_reader

これらのロールのアクセス許可について詳しくは、このドキュメントの「アクセス許可」セクションをご覧ください。 このストアド プロシージャを使って、他のユーザーが所有するジョブの属性を編集できるのは、sysadmin のメンバーだけです。

#### <a name="examples"></a>例
次の例では、London サーバーと NewYork サーバーのすべてのデータベースが、Servers Maintaining Customer Information グループに追加されます。 ジョブ エージェントを作成するときに指定したジョブ データベース (この場合は ElasticJobs) に接続する必要があります。

```sql
--Connect to the jobs database specified when creating the job agent
USE ElasticJobs ; 
GO

-- Add a target group containing server(s)
EXEC jobs.sp_add_target_group @target_group_name =  N'Servers Maintaining Customer Information'
GO

-- Add a server target member
EXEC jobs.sp_add_target_group_member
@target_group_name = N'Servers Maintaining Customer Information',
@target_type = N'SqlServer',
@refresh_credential_name=N'mymastercred', --credential required to refresh the databases in server
@server_name=N'London.database.windows.net' ;
GO

-- Add a server target member
EXEC jobs.sp_add_target_group_member
@target_group_name = N'Servers Maintaining Customer Information',
@target_type = N'SqlServer',
@refresh_credential_name=N'mymastercred', --credential required to refresh the databases in server
@server_name=N'NewYork.database.windows.net' ;
GO

--View the recently added members to the target group
SELECT * FROM [jobs].target_group_members WHERE target_group_name= N'Servers Maintaining Customer Information';
GO
```

### <a name="sp_delete_target_group_member"></a>sp_delete_target_group_member

ターゲット グループかターゲット グループのメンバーを削除します。

#### <a name="syntax"></a>構文


```sql
[jobs].sp_delete_target_group_member [ @target_group_name = ] 'target_group_name'
        [ , [ @target_id = ] 'target_id']
```



[ @target_group_name = ] 'target_group_name'  
ターゲット グループ メンバーを削除するターゲット グループの名前。 target_group_name は nvarchar(128) であり、既定値はありません。

[ @target_id = ] target_id  
 削除するターゲット グループ メンバーに割り当てられているターゲット ID 番号。 target_id は uniqueidentifier であり、既定値は NULL です。

#### <a name="return-code-values"></a>リターン コードの値
0 (成功) または 1 (失敗)

#### <a name="remarks"></a>解説
ターゲット グループを使うと、簡単にデータベースのコレクションをジョブのターゲットにできます。

#### <a name="permissions"></a>アクセス許可
既定では、sysadmin 固定サーバー ロールのメンバーは、このストアド プロシージャを実行できます。 これにより、ジョブの監視しか行えないようにユーザーが制限されます。そのユーザーには、ジョブ エージェントを作成するときに、指定されたジョブ エージェント データベースの次のデータベース ロールのいずれかを許可できます。
- jobs_reader

これらのロールのアクセス許可について詳しくは、このドキュメントの「アクセス許可」セクションをご覧ください。 このストアド プロシージャを使って、他のユーザーが所有するジョブの属性を編集できるのは、sysadmin のメンバーだけです。

#### <a name="examples"></a>例
次の例では、London サーバーを Servers Maintaining Customer Information グループから削除します。 ジョブ エージェントを作成するときに指定したジョブ データベース (この場合は ElasticJobs) に接続する必要があります。

```sql
--Connect to the jobs database specified when creating the job agent
USE ElasticJobs ; 
GO

-- Retrieve the target_id for a target_group_members
declare @tid uniqueidentifier
SELECT @tid = target_id FROM [jobs].target_group_members WHERE target_group_name = 'Servers Maintaining Customer Information' and server_name = 'London.database.windows.net'

-- Remove a target group member of type server
EXEC jobs.sp_delete_target_group_member
@target_group_name = N'Servers Maintaining Customer Information',
@target_id = @tid
GO
```

### <a name="sp_purge_jobhistory"></a>sp_purge_jobhistory

ジョブの履歴レコードを削除します。

#### <a name="syntax"></a>構文


```sql
[jobs].sp_purge_jobhistory [ @job_name = ] 'job_name'   
      [ , [ @job_id = ] job_id ]
      [ , [ @oldest_date = ] oldest_date []
```

#### <a name="arguments"></a>引数
[ **\@job_name =** ] 'job_name'  
履歴レコードを削除するジョブの名前。 job_name は nvarchar(128) であり、既定値は NULL です。 job_id または job_name のいずれかを指定する必要がありますが、両方を指定することはできません。

[ **\@job_id =** ] job_id  
 レコードを削除するジョブのジョブ ID 番号。 job_id は uniqueidentifier であり、既定値は NULL です。 job_id または job_name のいずれかを指定する必要がありますが、両方を指定することはできません。

[ **\@oldest_date =** ] oldest_date  
 履歴に保持する最も古いレコード。 oldest_date は DATETIME2 であり、既定値は NULL です。 oldest_date を指定すると、sp_purge_jobhistory は指定した値より古いレコードのみを削除します。

#### <a name="return-code-values"></a>リターン コードの値
0 (成功) または 1 (失敗) 解説 ターゲット グループを使うと、簡単にデータベースのコレクションをジョブのターゲットにできます。

#### <a name="permissions"></a>アクセス許可
既定では、sysadmin 固定サーバー ロールのメンバーは、このストアド プロシージャを実行できます。 これにより、ジョブの監視しか行えないようにユーザーが制限されます。そのユーザーには、ジョブ エージェントを作成するときに、指定されたジョブ エージェント データベースの次のデータベース ロールのいずれかを許可できます。
- jobs_reader

これらのロールのアクセス許可について詳しくは、このドキュメントの「アクセス許可」セクションをご覧ください。 このストアド プロシージャを使って、他のユーザーが所有するジョブの属性を編集できるのは、sysadmin のメンバーだけです。

#### <a name="examples"></a>例
次の例では、London サーバーと NewYork サーバーのすべてのデータベースが、Servers Maintaining Customer Information グループに追加されます。 ジョブ エージェントを作成するときに指定したジョブ データベース (この場合は ElasticJobs) に接続する必要があります。

```sql
--Connect to the jobs database specified when creating the job agent

EXEC sp_delete_target_group_member   
    @target_group_name = N'Servers Maintaining Customer Information',  
    @server_name = N'London.database.windows.net';  
GO
```


## <a name="job-views"></a>ジョブ ビュー

[ジョブ データベース](sql-database-job-automation-overview.md#job-database)では次のビューを使用できます。


|表示  |説明  |
|---------|---------|
|[jobs_executions](#jobs_executions-view)     |  ジョブの実行履歴を表示します。      |
|[jobs](#jobs-view)     |   すべてのジョブを表示します。      |
|[job_versions](#job_versions-view)     |   すべてのジョブのバージョンを表示します。      |
|[jobsteps](#jobsteps-view)     |     各ジョブの現在のバージョンのすべてのステップを表示します。    |
|[jobstep_versions](#jobstep_versions-view)     |     各ジョブのすべてのバージョンのすべてのステップを表示します。    |
|[target_groups](#target_groups-view)     |      すべてのターゲット グループを表示します。   |
|[target_group_members](#target_groups_members-view)     |   すべてのターゲット グループのすべてのメンバーを表示します。      |


### <a name="jobs_executions-view"></a>jobs_executions ビュー

[jobs].[jobs_executions]

ジョブの実行履歴を表示します。


|列名|   データ型   |説明|
|---------|---------|---------|
|**job_execution_id**   |uniqueidentifier|  ジョブ実行のインスタンスの一意の ID。
|**job_name**   |nvarchar(128)  |ジョブの名前。
|**job_id** |uniqueidentifier|  ジョブの一意の ID。
|**job_version**    |int    |ジョブのバージョン (ジョブを変更するたびに自動的に更新されます)。
|**step_id**    |int|   ステップの (このジョブで) 一意の ID。 NULL は、これが親ジョブの実行であることを示します。
|**is_active**| bit |情報がアクティブか非アクティブかを示します。 1 はアクティブなジョブを示し、0 は非アクティブなジョブを示します。
|**lifecycle**| nvarchar(50)|ジョブの状態を示す値: 'Created'、'In Progress'、'Failed'、'Succeeded'、'Skipped'、'SucceededWithSkipped'|
|**create_time**|   datetime2(7)|   ジョブが作成された日付と時刻。
|**start_time** |datetime2(7)|  ジョブが実行を開始した日付と時刻。 ジョブがまだ実行されていない場合は NULL です。
|**end_time**|  datetime2(7)    |ジョブが実行を終了した日付と時刻。 ジョブがまだ実行されていない場合、またはまだ実行を完了していない場合は、NULL です。
|**current_attempts**   |int    |ステップが再試行された回数。 親ジョブの場合は 0 です。子ジョブの場合は、実行ポリシーに基づいて 1 以上になります。
|**current_attempt_start_time** |datetime2(7)|  ジョブが実行を開始した日付と時刻。 NULL は、これが親ジョブの実行であることを示します。
|**last_message**   |nvarchar(max)| ジョブまたはステップの履歴メッセージ。 
|**target_type**|   nvarchar(128)   |ターゲット データベースまたはデータベースのコレクションの種類。サーバー内のすべてのデータベース、エラスティック プール内のすべてのデータベース、またはデータベースです。 target_type の有効な値は、'SqlServer'、'SqlElasticPool'、または 'SqlDatabase' です。 NULL は、これが親ジョブの実行であることを示します。
|**target_id**  |uniqueidentifier|  ターゲット グループ メンバーの一意の ID。  NULL は、これが親ジョブの実行であることを示します。
|**target_group_name**  |nvarchar(128)  |ターゲット グループの名前。 NULL は、これが親ジョブの実行であることを示します。
|**target_server_name**|    nvarchar(256)|  ターゲット グループに含まれる SQL Database サーバーの名前。 target_type が 'SqlServer' の場合にのみ指定されます。 NULL は、これが親ジョブの実行であることを示します。
|**target_database_name**   |nvarchar(128)| ターゲット グループに含まれるデータベースの名前。 target_type が 'SqlDatabase' の場合にのみ指定されます。 NULL は、これが親ジョブの実行であることを示します。


### <a name="jobs-view"></a>jobs ビュー

[jobs].[jobs]

すべてのジョブを表示します。

|列名|   データ型|  説明|
|------|------|-------|
|**job_name**|  nvarchar(128)   |ジョブの名前。|
|**job_id**|    uniqueidentifier    |ジョブの一意の ID。|
|**job_version**    |int    |ジョブのバージョン (ジョブを変更するたびに自動的に更新されます)。|
|**description**    |nvarchar(512)| ジョブの説明。 enabled bit    ジョブが有効か無効かを示します。 1 は有効なジョブを示し、0 は無効なジョブを示します。|
|**schedule_interval_type** |nvarchar(50)   |ジョブが実行されるときを示す値: 'Once'、'Minutes'、'Hours'、'Days'、'Weeks'、'Months'
|**schedule_interval_count**|   int|    ジョブの各実行の間に発生する schedule_interval_type 期間の数。|
|**schedule_start_time**    |datetime2(7)|  ジョブが最後に実行を開始した日付と時刻。|
|**schedule_end_time**| datetime2(7)|   ジョブが最後に実行を完了した日付と時刻。|


### <a name="job_versions-view"></a>job_versions ビュー

[jobs].[job_versions]

すべてのジョブのバージョンを表示します。

|列名|   データ型|  説明|
|------|------|-------|
|**job_name**|  nvarchar(128)   |ジョブの名前。|
|**job_id**|    uniqueidentifier    |ジョブの一意の ID。|
|**job_version**    |int    |ジョブのバージョン (ジョブを変更するたびに自動的に更新されます)。|


### <a name="jobsteps-view"></a>jobsteps ビュー

[jobs].[jobsteps]

各ジョブの現在のバージョンのすべてのステップを表示します。

|列名    |データ型| 説明|
|------|------|-------|
|**job_name**   |nvarchar(128)| ジョブの名前。|
|**job_id** |uniqueidentifier   |ジョブの一意の ID。|
|**job_version**|   int|    ジョブのバージョン (ジョブを変更するたびに自動的に更新されます)。|
|**step_id**    |int    |ステップの (このジョブで) 一意の ID。|
|**step_name**  |nvarchar(128)  |ステップの (このジョブで) 一意の名前。|
|**command_type**   |nvarchar(50)   |ジョブ ステップで実行されるコマンドの種類。 v1 の場合、値は 'TSql' でなければならず、これが既定値です。|
|**command_source** |nvarchar(50)|  コマンドの場所。 v1 の場合、'Inline' が既定値であり、受け入れられる値はこれだけです。|
|**command**|   nvarchar(max)|  command_type によってエラスティック ジョブで実行されるコマンド。|
|**credential_name**|   nvarchar(128)   |ジョブの実行に使われるデータベース スコープの資格情報の名前。|
|**target_group_name**| nvarchar(128)   |ターゲット グループの名前。|
|**target_group_id**|   uniqueidentifier|   ターゲット グループの一意の ID。|
|**initial_retry_interval_seconds**|    int |最初の再試行の前の遅延。 既定値は 1 です。|
|**maximum_retry_interval_seconds** |int|   再試行の間の最大遅延。 再試行の間の遅延がこの値より大きくなった場合は、この値に制限されます。 既定値は 120 です。|
|**retry_interval_backoff_multiplier**  |real|  ジョブ ステップの実行が複数回にわたり失敗した場合に、再試行の遅延に適用する乗数。 既定値は 2.0 です。|
|**retry_attempts** |int|   このステップが失敗した場合に使う再試行の回数。 既定値は 10 で、10 回再試行することを示します。|
|**step_timeout_seconds**   |int|   再試行する間隔を示す分単位の時間。 既定値は 0 で、0 分間隔を示します。|
|**output_type**    |nvarchar(11)|  コマンドの場所。 現在のプレビューでは、'Inline' が既定値であり、受け入れられる値はこれだけです。|
|**output_credential_name**|    nvarchar(128)   |結果セット格納先サーバーへの接続に使われる資格情報の名前。|
|**output_subscription_id**|    uniqueidentifier|   クエリ実行からの結果セットの格納先サーバー/データベースのサブスクリプションの一意の ID。|
|**output_resource_group_name** |nvarchar(128)| 格納先サーバーが存在するリソース グループの名前。|
|**output_server_name**|    nvarchar(256)   |結果セットの格納先サーバーの名前。|
|**output_database_name**   |nvarchar(128)| 結果セットの格納先データベースの名前。|
|**output_schema_name** |nvarchar(max)| 格納先スキーマの名前。 指定されていない場合の既定値は dbo です。|
|**output_table_name**| nvarchar(max)|  クエリ結果からの結果セットを格納するテーブルの名前。 テーブルがまだ存在しない場合は、結果セットのスキーマに基づいて自動的に作成されます。 スキーマは、結果セットのスキーマと一致する必要があります。|
|**max_parallelism**|   int|    ジョブ ステップが一度に実行される、エラスティック プールごとのデータベースの最大数。 既定値は NULL で、無制限を意味します。 |


### <a name="jobstep_versions-view"></a>jobstep_versions ビュー

[jobs].[jobstep_versions]

各ジョブのすべてのバージョンのすべてのステップを表示します。 スキーマは [jobsteps](#jobsteps-view) と同じです。

### <a name="target_groups-view"></a>target_groups ビュー

[jobs].[target_groups]

すべてのターゲット グループを一覧表示します。

|列名|データ型| 説明|
|-----|-----|-----|
|**target_group_name**| nvarchar(128)   |ターゲット グループ (データベースのコレクション) の名前。 
|**target_group_id**    |uniqueidentifier   |ターゲット グループの一意の ID。

### <a name="target_groups_members-view"></a>target_groups_members ビュー

[jobs].[target_groups_members]

すべてのターゲット グループのすべてのメンバーを表示します。

|列名|データ型| 説明|
|-----|-----|-----|
|**target_group_name**  |nvarchar(128)|ターゲット グループ (データベースのコレクション) の名前。 |
|**target_group_id**    |uniqueidentifier   |ターゲット グループの一意の ID。|
|**membership_type**    |int|   ターゲット グループ メンバーがターゲット グループに含まれるか除外されるかを指定します。 target_group_name の有効な値は、'Include' または 'Exclude' です。|
|**target_type**    |nvarchar(128)| ターゲット データベースまたはデータベースのコレクションの種類。サーバー内のすべてのデータベース、エラスティック プール内のすべてのデータベース、またはデータベースです。 target_type の有効な値は、'SqlServer'、'SqlElasticPool'、'SqlDatabase'、または 'SqlShardMap' です。|
|**target_id**  |uniqueidentifier|  ターゲット グループ メンバーの一意の ID。|
|**refresh_credential_name**    |nvarchar(128)  |ターゲット グループ メンバーへの接続に使われるデータベース スコープの資格情報の名前。|
|**subscription_id**    |uniqueidentifier|  サブスクリプションの一意の ID。|
|**resource_group_name**    |nvarchar(128)| ターゲット グループ メンバーが存在するリソース グループの名前。|
|**server_name**    |nvarchar(128)  |ターゲット グループに含まれる SQL Database サーバーの名前。 target_type が 'SqlServer' の場合にのみ指定されます。 |
|**database_name**  |nvarchar(128)  |ターゲット グループに含まれるデータベースの名前。 target_type が 'SqlDatabase' の場合にのみ指定されます。|
|**elastic_pool_name**  |nvarchar(128)| ターゲット グループに含まれるエラスティック プールの名前。 target_type が 'SqlElasticPool' の場合にのみ指定されます。|
|**shard_map_name** |nvarchar(128)| ターゲット グループに含まれるシャード マップの名前。 target_type が 'SqlShardMap' の場合にのみ指定されます。|


## <a name="resources"></a>リソース

 - ![トピック リンク アイコン](https://docs.microsoft.com/sql/database-engine/configure-windows/media/topic-link.gif "トピック リンク アイコン") [Transact-SQL 構文の表記規則](https://docs.microsoft.com/sql/t-sql/language-elements/transact-sql-syntax-conventions-transact-sql)  


## <a name="next-steps"></a>次の手順

- [PowerShell を使用したエラスティック ジョブの作成と管理](elastic-jobs-powershell.md)
- [SQL Server の承認と権限](https://docs.microsoft.com/dotnet/framework/data/adonet/sql/authorization-and-permissions-in-sql-server)
