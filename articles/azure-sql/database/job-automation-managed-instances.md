---
title: Azure SQL Managed Instance での SQL Agent ジョブを使用したジョブの自動化
description: Azure SQL Managed Instance で Transact-SQL (T-SQL) スクリプトを実行するためのオートメーション オプション
services: sql-database
ms.service: sql-db-mi
ms.subservice: features
ms.custom: sqldbrb=1
dev_langs:
- TSQL
ms.topic: conceptual
author: WilliamDAssafMSFT
ms.author: wiassaf
ms.reviewer: ''
ms.date: 02/01/2021
ms.openlocfilehash: 3be01c304a40317e0d21baf6789ef1376cd89b80
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "104608080"
---
# <a name="automate-management-tasks-using-sql-agent-jobs-in-azure-sql-managed-instance"></a>Azure SQL Managed Instance で SQL Agent ジョブを使用して管理タスクを自動化する
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

SQL Server および [SQL Managed Instance](../../azure-sql/managed-instance/sql-managed-instance-paas-overview.md) で [SQL Server エージェント](/sql/ssms/agent/sql-server-agent)を使用すると、1 つまたは多数のデータベースに対して定期的に実行されるジョブを作成してスケジュールし、Transact-SQL (T-SQL) クエリを実行してメンテナンス タスクを行うことができます。 この記事では、SQL Managed Instance 用の SQL Agent を導入しました。

> [!Note]
> SQL Agent は、Azure SQL Database または Azure Synapse Analytics では使用できません。 代わりに、[エラスティック ジョブを使用したジョブの自動化](job-automation-overview.md)をお勧めします。

### <a name="sql-agent-job-limitations-in-azure-sql-managed-instance"></a>Azure SQL Managed Instance での SQL Agent ジョブの制限事項

SQL Server で使用できる SQL Agent と SQL Managed Instance の一部としての SQL Agent の違いに注意してください。 SQL Server と SQL Managed Instance でサポートされる機能の違いの詳細については、[Azure SQL Managed Instance と SQL Server の T-SQL の相違点](../../azure-sql/managed-instance/transact-sql-tsql-differences-sql-server.md#sql-server-agent)に関するページを参照してください。 

SQL Server で利用できる SQL Agent の機能の一部は、SQL Managed Instance ではサポートされていません。

- SQL エージェントの設定は読み取り専用です。 
    - システム ストアド プロシージャ `sp_set_agent_properties` は、SQL Managed Instance ではサポートされていません。
- 現在、SQL Managed Instance では、SQL Agent の有効化と無効化はサポートされていません。 SQL エージェントは常に実行されています。
- 通知は部分的にサポートされています。
  - ポケットベルはサポートされていません。
  - NetSend はサポートされていません。
  - アラートはサポートされていません。
- プロキシはサポートされていません。
- Eventlog はサポートされていません。
- アイドル状態の CPU に基づくジョブ スケジュール トリガーはサポートされていません。

## <a name="when-to-use-sql-agent-jobs"></a>どのようなときに SQL Agent ジョブを使用するか 

SQL Agent ジョブを使用できるシナリオをいくつか次に示します。

- 管理タスクの自動化とスケジュール (平日に毎日、業務時間後に実行するなど)
  - スキーマの変更、資格情報の管理、パフォーマンス データの収集、テナント (顧客) テレメトリの収集をデプロイします。
  - 参照データ (全データベースに共通の情報) を更新したり、Azure Blob Storage からデータを読み込んだりします。
  - データの整合性を確認するための DBCC CHECKDB や、クエリのパフォーマンスを向上させるためのインデックスのメンテナンスなど、一般的なメンテナンス タスク。 データベース コレクションを対象として (ピーク外の時間などに) 定期的に実行するジョブを構成します。
  - データベース セットのクエリ結果をリアルタイムで中央のテーブルに収集します。 パフォーマンス クエリを継続的に実行して、その他のタスクの実行をトリガーするように構成できます。
- レポート用データの収集
  - データベースのコレクションから 1 つのテーブルにデータを集約します。
  - 顧客の製品利用統計情報収集など、大規模なデータセット全体に対して、時間がかかるデータ処理クエリを実行します。 結果は 1 つの対象テーブルに収集され、分析に使用されます。
- データの移動
  - お客様のデータベースで行われた変更を他のデータベースにレプリケートしたり、リモート データベースに加えられた更新を収集してデータベースに変更を適用したりするジョブを作成します。
  - SQL Server Integration Services (SSIS) を使用してお客様のデータベースとの間で双方向にデータを読み込むジョブを作成します。

## <a name="sql-agent-jobs-in-azure-sql-managed-instance"></a>Azure SQL Managed Instance での SQL Agent ジョブ

SQL Agent ジョブは、SQL Server と SQL Managed Instance でタスクの自動化に引き続き使用される SQL エージェント サービスによって実行されます。 

SQL エージェント ジョブは、お客様のデータベースに対して指定される一連の T-SQL スクリプトです。 ジョブを使用し、1 回または複数回実行してその成否を監視できる管理タスクを定義します。 

ジョブは、1 つのローカル サーバーで実行することも、複数のリモート サーバーで実行することもできます。 SQL Agent ジョブは、SQL Managed Instance サービス内で実行されるデータベース エンジンの内部コンポーネントです。

SQL エージェント ジョブには、いくつかの重要な概念があります。

- **ジョブ ステップ**: ジョブ内で実行されるべき 1 つまたは複数のステップのセットです。 ジョブ ステップが成功または失敗した場合に実行されるアクションと再試行方法をジョブ ステップごとに定義することができます。
- **スケジュール**: ジョブを実行すべきタイミングを定義します。
- **通知**: ジョブが完了したときにメール経由でオペレーターに通知するために使用されるルールを定義できます。

### <a name="sql-agent-job-steps"></a>SQL Agent のジョブ ステップ

SQL エージェントのジョブ ステップは、SQL エージェントが実行する必要のある一連のアクションです。 それぞれのステップには、それが成功または失敗した場合に実行される後続のステップ (失敗した場合には再試行回数) があります。

SQL Agent を使用すると、さまざまな種類のジョブ ステップを作成できます。たとえば、データベースに対して単一の Transact-SQL バッチを実行する Transact-SQL ジョブ ステップ、カスタム OS スクリプトを実行できる OS コマンドまたは PowerShell のステップ、SSIS ランタイムを使用してデータを読み込むことができる [SSIS ジョブ ステップ](../../data-factory/how-to-invoke-ssis-package-managed-instance-agent.md)、お客様のデータベースから他のデータベースに変更を発行できる[レプリケーション](../managed-instance/replication-transactional-overview.md) ステップなどがあります。

> [!Note]
> Azure SQL Managed Instance でホストされている SSISDB で Azure SSIS Integration Runtime を利用する方法の詳細については、「[Azure Data Factory 内で SQL Server Integration Services (SSIS) と共に Azure SQL Managed Instance を使用する](../../data-factory/how-to-use-sql-managed-instance-with-ir.md)」を参照してください。

[トランザクション レプリケーション](../managed-instance/replication-transactional-overview.md)を使用すると、テーブルの変更を、Azure SQL Managed Instance、Azure SQL Database、または SQL Server の他のデータベースにレプリケートできます。 詳しくは、[Azure SQL Managed Instance にレプリケーションを構成する方法](../../azure-sql/managed-instance/replication-between-two-instances-configure-tutorial.md)に関する記事をご覧ください。 

現在、SQL Managed Instance では、次のような他の種類のジョブ ステップはサポートされていません。

- マージ レプリケーション ジョブ ステップはサポートされていません。
- キュー リーダーはサポートされていません。
- Analysis Services はサポートされていません。
 
### <a name="sql-agent-job-schedules"></a>SQL Agent のジョブ スケジュール

スケジュールによって、ジョブが実行されるタイミングが指定されます。 同じスケジュールで複数のジョブを実行できるほか、同じジョブに複数のスケジュールを適用することもできます。

ジョブが実行されるタイミングに関して、次の条件をスケジュールで定義できます。

- SQL Server エージェントが開始されるたび。 ジョブは、フェールオーバー後に毎回アクティブ化されます。
- 特定の日時に 1 回。これは、なんらかのジョブを遅延実行する際に便利です。
- 定期的なスケジュール。

> [!Note]
> 現在、SQL Managed Instance を使用して、CPU がアイドル状態のときにジョブを開始することはできません。

### <a name="sql-agent-job-notifications"></a>SQL Agent のジョブ通知

SQL エージェント ジョブでは、ジョブが正常に完了したとき、または失敗したときに通知を受け取ることができます。 通知はメール経由で受け取ることができます。

まだ有効になっていない場合は、最初に、Azure SQL Managed Instance で[データベース メール機能](/sql/relational-databases/database-mail/database-mail)を構成する必要があります。

```sql
GO
EXEC sp_configure 'show advanced options', 1;
GO
RECONFIGURE;
GO
EXEC sp_configure 'Database Mail XPs', 1;
GO
RECONFIGURE
```

たとえば、メール通知の送信に使用されるメール アカウントを設定します。 `AzureManagedInstance_dbmail_profile` という名前のメール プロファイルにアカウントを割り当てます。 SQL Managed Instance で SQL Agent ジョブを使用して電子メールを送信するには、`AzureManagedInstance_dbmail_profile` という名前のプロファイルが存在する必要があります。 そうしないと、SQL Managed Instance で SQL Agent を使用してメールを送信することはできません。 次の例を参照してください。

```sql
-- Create a Database Mail account
EXECUTE msdb.dbo.sysmail_add_account_sp
    @account_name = 'SQL Agent Account',
    @description = 'Mail account for Azure SQL Managed Instance SQL Agent system.',
    @email_address = '$(loginEmail)',
    @display_name = 'SQL Agent Account',
    @mailserver_name = '$(mailserver)' ,
    @username = '$(loginEmail)' ,
    @password = '$(password)';

-- Create a Database Mail profile
EXECUTE msdb.dbo.sysmail_add_profile_sp
    @profile_name = 'AzureManagedInstance_dbmail_profile',
    @description = 'E-mail profile used for messages sent by Managed Instance SQL Agent.';

-- Add the account to the profile
EXECUTE msdb.dbo.sysmail_add_profileaccount_sp
    @profile_name = 'AzureManagedInstance_dbmail_profile',
    @account_name = 'SQL Agent Account',
    @sequence_number = 1;
```

[sp_send_db_mail](/sql/relational-databases/system-stored-procedures/sp-send-dbmail-transact-sql) システム ストアド プロシージャを使用して、T-SQL でデータベース メールの構成をテストします。

```sql
DECLARE @body VARCHAR(4000) = 'The email is sent from ' + @@SERVERNAME;
EXEC msdb.dbo.sp_send_dbmail  
    @profile_name = 'AzureManagedInstance_dbmail_profile',  
    @recipients = 'ADD YOUR EMAIL HERE',  
    @body = 'Add some text',  
    @subject = 'Azure SQL Instance - test email';  
```

お客様の SQL エージェント ジョブに関する出来事をオペレーターに通知できます。 SQL Managed Instance の 1 つまたは複数のインスタンスを保守するメンテナンス担当者の連絡先情報は、オペレーターが定義します。 オペレーターの責任が 1 人の担当者に割り当てられることもあります。

SQL Managed Instance または SQL Server に複数のインスタンスがあるシステムでは、オペレーターの責任を多数の担当者で共有することができます。 オペレーターは、セキュリティ情報を持たず、セキュリティ プリンシパルも定義しません。 オペレーターは、役割が変わる可能性がある個人ではなく、メール配布グループにするのが理想的です。   

SQL Server Management Studio (SSMS) または次の例で示す Transact-SQL スクリプトを使用して、[オペレーターを作成する](/sql/relational-databases/system-stored-procedures/sp-add-operator-transact-sql)ことができます。

```sql
EXEC msdb.dbo.sp_add_operator
    @name=N'AzureSQLTeam',
    @enabled=1,
    @email_address=N'AzureSQLTeamn@contoso.com';
```

SSMS の[データベース メール ログ](/sql/relational-databases/database-mail/database-mail-log-and-audits)を使用して、メールの成功または失敗を確認します。

その後、SSMS または次の Transact-SQL スクリプトを使用して、[SQL Agent ジョブを変更](/sql/relational-databases/system-stored-procedures/sp-update-job-transact-sql)したり、ジョブが完了、失敗、成功した場合にメールで通知を受け取るオペレーターを割り当てたりすることができます。

```sql
EXEC msdb.dbo.sp_update_job @job_name=N'Load data using SSIS',
    @notify_level_email=3, -- Options are: 1 on succeed, 2 on failure, 3 on complete
    @notify_email_operator_name=N'AzureSQLTeam';
```

### <a name="sql-agent-job-history"></a>SQL Agent のジョブ履歴

SQL Agent のプロパティは基になるレジストリ値に格納されているため、現在、Azure SQL Managed Instance でそれを変更することはできません。 これは、ジョブ履歴レコードの Agent 保持ポリシーを調整するためのオプションは、既定値である合計 1000 レコードと、ジョブあたり最大 100 履歴レコードに固定されていることを意味します。

### <a name="sql-agent-fixed-database-role-membership"></a>SQL Agent の固定データベース ロールのメンバーシップ

sysadmin 以外のログインにリンクされているユーザーが、msdb システム データベースの 3 つの SQL Agent 固定データベース ロールのいずれかに追加された場合、これらのログインを機能させるには、明示的な EXECUTE 権限を master ストアド プロシージャに付与する必要がある、という問題があります。 この問題が発生した場合は、エラー メッセージ "EXECUTE 権限がオブジェクト <object_name> で拒否されました (Microsoft SQL Server、エラー:229)" が表示されます。 

msdb の SQL Agent 固定データベース ロール (SQLAgentUserRole、SQLAgentReaderRole、または SQLAgentOperatorRole) にユーザーを追加した後、これらのロールに追加されたユーザーの各ログインについて、次の T-SQL スクリプトを実行し、一覧のシステム ストアド プロシージャに EXECUTE 権限を明示的に付与します。 この例では、ユーザー名とログイン名が同じであることを前提としています。 

```sql
USE [master]
GO
CREATE USER [login_name] FOR LOGIN [login_name];
GO
GRANT EXECUTE ON master.dbo.xp_sqlagent_enum_jobs TO [login_name];
GRANT EXECUTE ON master.dbo.xp_sqlagent_is_starting TO [login_name];
GRANT EXECUTE ON master.dbo.xp_sqlagent_notify TO [login_name];
```

## <a name="learn-more"></a>詳細情報

- [Azure SQL Managed Instance とは](../managed-instance/sql-managed-instance-paas-overview.md)
- [Azure SQL Database と SQL Managed Instance の新機能](../../azure-sql/database/doc-changes-updates-release-notes.md?tabs=managed-instance)
- [Azure SQL Managed Instance と SQL Server の T-SQL の相違点](../../azure-sql/managed-instance/transact-sql-tsql-differences-sql-server.md#sql-server-agent)
- [機能の比較:Azure SQL Database と Azure SQL Managed Instance](../../azure-sql/database/features-comparison.md)
