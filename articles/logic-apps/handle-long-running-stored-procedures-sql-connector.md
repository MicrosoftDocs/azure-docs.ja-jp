---
title: SQL コネクタの実行時間の長いストアド プロシージャを処理する
description: Azure Logic Apps で SQL コネクタを使用しているときにタイムアウトになるストアド プロシージャを処理する方法
services: logic-apps
ms.suite: integration
ms.reviewer: camerost, logicappspm
ms.topic: conceptual
ms.date: 10/27/2020
ms.openlocfilehash: f5b04c563dc81497f591788dc4890d379c0f898f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "93102740"
---
# <a name="handle-stored-procedure-timeouts-in-the-sql-connector-for-azure-logic-apps"></a>Azure Logic Apps での SQL コネクタのストアド プロシージャのタイムアウトを処理する

ロジック アプリで、[SQL コネクタ](../connectors/connectors-create-api-sqlazure.md)がすべての結果を同時に返さないような大きな結果セットを処理する場合、または結果セットのサイズと構造をより細かく制御する必要がある場合は、結果を必要な方法で整理する[ストアド プロシージャ](/sql/relational-databases/stored-procedures/stored-procedures-database-engine)を作成できます。 SQL データベース テーブルを操作するビジネス タスクをより簡単に自動化できるように、SQL コネクタには、[Azure Logic Apps](../logic-apps/logic-apps-overview.md) を使用してアクセスできる多数のバックエンド機能が用意されています。

たとえば、複数の行を取得または挿入する場合、ロジック アプリは、こちらの [制限](../logic-apps/logic-apps-limits-and-config.md)の中で [**Until** ループ](../logic-apps/logic-apps-control-flow-loops.md#until-loop) を使用することで、行を反復処理できます。 ただし、ロジック アプリは、数千から数百万の行があるレコード セットを処理する場合があります。このような場合は、データベースへの呼び出しコストを最小限にする必要があります。 詳細については、[SQL コネクタを使用した一括データの処理](../connectors/connectors-create-api-sqlazure.md#handle-bulk-data)に関するページを参照してください。

<a name="timeout-limit"></a>

## <a name="timeout-limit-on-stored-procedure-execution"></a>ストアド プロシージャの実行のタイムアウト上限

SQL コネクタには、[2 分未満](/connectors/sql/#known-issues-and-limitations)のストアド プロシージャのタイムアウト上限があります。 一部のストアド プロシージャでは、この上限を超えることがあり、`504 Timeout` エラーが発生します。 一部の場合、長期実行されるいくつかのプロセスは、この目的のために明示的にストアド プロシージャとしてコード化されます。 このタイムアウト上限が原因で、これらのプロシージャを Azure Logic Apps から呼び出すと問題が発生する可能性があります。 SQL コネクタはネイティブで非同期モードをサポートしていませんが、SQL 補完のトリガー、ネイティブ SQL パススルー クエリ、状態テーブル、およびサーバー側ジョブを使用してこの問題を回避し、このモードをシミュレートできます。 このタスクでは、[Azure SQL Database](../azure-sql/database/sql-database-paas-overview.md) で [Azure エラスティック ジョブ エージェント](../azure-sql/database/elastic-jobs-overview.md)を使用できます。 [オンプレミスの SQL Server](/sql/sql-server/sql-server-technical-documentation) と [Azure SQL Managed Instance](../azure-sql/managed-instance/sql-managed-instance-paas-overview.md) では、[SQL Server エージェント](/sql/ssms/agent/sql-server-agent)を使用できます。

たとえば、次の実行時間の長いストアド プロシージャがあるとします。この処理を完了するには、タイムアウト上限を超えてしまいます。 SQL コネクタを使用してロジック アプリからこのストアド プロシージャを実行すると、結果として `HTTP 504 Gateway Timeout` エラーが発生します。

```sql
CREATE PROCEDURE [dbo].[WaitForIt]
   @delay char(8) = '00:03:00'
AS
BEGIN
   SET NOCOUNT ON;
   WAITFOR DELAY @delay
END
```

ストアド プロシージャを直接呼び出すのではなく、 *ジョブ エージェント* を使用して、バックグラウンドでプロシージャを非同期に実行することができます。 入力と出力を状態テーブルに格納して、ロジック アプリを使用して操作できるようにすることができます。 入力と出力が不要な場合、またはストアド プロシージャのテーブルに既に結果を書き込んでいる場合は、この方法を簡略化できます。

> [!IMPORTANT]
> ストアド プロシージャとすべてのジョブが *べき等* であることを確認します。これは、結果に影響を与えずに複数回実行できることを意味します。 非同期処理が失敗した場合、またはタイムアウトになった場合は、ジョブ エージェントがステップを再試行することがあります。この場合、ストアド プロシージャは複数回再試行されます。 出力が重複しないようにするには、オブジェクトを作成する前に、これらの[ベストプラクティスと方法](../azure-sql/database/elastic-jobs-overview.md#idempotent-scripts)を確認してください。

次のセクションでは、Azure SQL Database で Azure エラスティック ジョブ エージェントを使用する方法を説明します。 SQL Server と Azure SQL Managed Instance では、SQL Server エージェントを使用できます。 管理の詳細は異なりますが、基本的な手順は、Azure SQL Database のジョブ エージェントの設定と同じです。

<a name="azure-sql-database"></a>

## <a name="job-agent-for-azure-sql-database"></a>Azure SQL Database のジョブ エージェント

[Azure SQL Database](../azure-sql/database/sql-database-paas-overview.md) に対してストアド プロシージャを実行できるジョブを作成するには、[Azure エラスティック ジョブ エージェント](../azure-sql/database/elastic-jobs-overview.md)を使用します。 Azure portal でジョブ エージェントを作成します。 この方法では、エージェントによって使用されるデータベース (*エージェント データベース* とも呼ばれる) にいくつかのストアド プロシージャが追加されます。 次に、ターゲット データベースでストアド プロシージャを実行し、完了したら出力をキャプチャするジョブを作成できます。

ジョブを作成する前に、[Azure エラスティック ジョブ エージェントに関する完全なドキュメント](../azure-sql/database/elastic-jobs-overview.md)で説明されているように、アクセス許可、グループ、およびターゲットを設定する必要があります。 また、次のセクションで説明するように、対象のデータベースにサポート テーブルを作成する必要があります。

<a name="create-state-table"></a>

### <a name="create-state-table-for-registering-parameters-and-storing-inputs"></a>パラメーターの登録と入力の格納を行う状態テーブルを作成する

SQL Agent ジョブでは、入力パラメーターを使用できません。 代わりに、ターゲット データベースで、パラメーターの登録とストアド プロシージャの呼び出しに使用する入力の格納をする状態テーブルを作成します。 エージェントのすべてのジョブ ステップは、ターゲット データベースに対して実行されますが、そのジョブのストアド プロシージャはエージェント データベースに対して実行されます。 

状態テーブルを作成するには、次のスキーマを使用します。

```sql
CREATE TABLE [dbo].[LongRunningState](
   [jobid] [uniqueidentifier] NOT NULL,
   [rowversion] [timestamp] NULL,
   [parameters] [nvarchar](max) NULL,
   [start] [datetimeoffset](7) NULL,
   [complete] [datetimeoffset](7) NULL,
   [code] [int] NULL,
   [result] [nvarchar](max) NULL,
   CONSTRAINT [PK_LongRunningState] PRIMARY KEY CLUSTERED
      (   [jobid] ASC
      )WITH (STATISTICS_NORECOMPUTE = OFF, IGNORE_DUP_KEY = OFF) ON [PRIMARY]
      ) ON [PRIMARY] TEXTIMAGE_ON [PRIMARY]
```

結果のテーブルが [SQL Server Management Studio (SMSS)](/sql/ssms/download-sql-server-management-studio-ssms) でどのように表示されるかを次に示します。

![ストアド プロシージャの入力を格納する、作成された状態テーブルを示すスクリーンショット。](media/handle-long-running-stored-procedures-sql-connector/state-table-input-parameters.png)

確実にパフォーマンスを向上させ、エージェント ジョブが関連レコードを見つけることができるようにするため、テーブルでジョブ実行 ID (`jobid`) が主キーとして使用されます。 必要に応じて、入力パラメーター用に個別の列を追加することもできます。 前述のスキーマでは、より広く複数のパラメーターを処理できますが、 `NVARCHAR(MAX)` によって計算されるサイズに制限されています。

<a name="create-top-level-job"></a>

### <a name="create-a-top-level-job-to-run-the-stored-procedure"></a>ストアド プロシージャを実行する最上位レベルのジョブを作成する

実行時間の長いストアド プロシージャを実行するには、エージェント データベースにこの最上位のジョブ エージェントを作成します。

```sql
EXEC jobs.sp_add_job 
   @job_name='LongRunningJob',
   @description='Execute Long-Running Stored Proc',
   @enabled = 1
```

ここで、ストアド プロシージャをパラメーター化、実行、および完了するステップをジョブに追加します。 既定では、ジョブ ステップは 12 時間後にタイムアウトになります。 ストアド プロシージャにより多くの時間が必要な場合、またはプロシージャをもっと前にタイムアウトにする場合は、 `step_timeout_seconds` パラメーターを秒単位で指定する別の値に変更できます。 既定では、各再試行の間にバックオフのタイムアウトが設定された 10 回の組み込み再試行があり、活用することができます。

追加する手順は次のとおりです。

1. `LongRunningState` テーブルにパラメーターが表示されるまで待ちます。

   この最初の手順では、ジョブの開始直後に `LongRunningState` テーブルにパラメーターが追加されるのを待ちます。 ジョブ実行 ID (`jobid`) が `LongRunningState` テーブルに追加されない場合、ステップは単に失敗し、既定の再試行またはバックオフ タイムアウトによる待機が行われます。

   ```sql
   EXEC jobs.sp_add_jobstep
      @job_name='LongRunningJob',
      @step_name= 'Parameterize WaitForIt',
      @step_timeout_seconds = 30,
      @command= N'
         IF NOT EXISTS(SELECT [jobid] FROM [dbo].[LongRunningState]
            WHERE jobid = $(job_execution_id))
            THROW 50400, ''Failed to locate call parameters (Step1)'', 1',
      @credential_name='JobRun',
      @target_group_name='DatabaseGroupLongRunning'
   ```

1. 状態テーブルからパラメーターをクエリし、ストアド プロシージャに渡します。 さらに、この手順ではバックグラウンドでプロシージャが実行されます。 

   ストアド プロシージャがパラメーターを必要としない場合は、ストアド プロシージャを直接呼び出します。 それ以外の場合、 `@timespan` パラメーターを渡すには、 `@callparams` を使用します。これを拡張して、追加のパラメーターを渡すこともできます。

   ```sql
   EXEC jobs.sp_add_jobstep
      @job_name='LongRunningJob',
      @step_name='Execute WaitForIt',
      @command=N'
         DECLARE @timespan char(8)
         DECLARE @callparams NVARCHAR(MAX)
         SELECT @callparams = [parameters] FROM [dbo].[LongRunningState]
            WHERE jobid = $(job_execution_id))
         SET @timespan = @callparams
         EXECUTE [dbo].[WaitForIt] @delay = @timespan', 
      @credential_name='JobRun',
      @target_group_name='DatabaseGroupLongRunning'
   ```

1. ジョブを完了し、結果を記録します。

   ```sql
   EXEC jobs.sp_add_jobstep
      @job_name='LongRunningJob',
      @step_name='Complete WaitForIt',
      @command=N'
         UPDATE [dbo].[LongRunningState]
            SET [complete] = GETUTCDATE(),
               [code] = 200,
               [result] = ''Success''
            WHERE jobid = $(job_execution_id)',
      @credential_name='JobRun',
      @target_group_name='DatabaseGroupLongRunning'
   ```

<a name="start-job-pass-parameters"></a>

### <a name="start-the-job-and-pass-the-parameters"></a>ジョブを開始しパラメーターを渡す

ジョブを開始するには、でパススルー ネイティブ クエリを使用して、[**SQL クエリ** アクションを実行](/connectors/sql/#execute-a-sql-query-(v2))し、ジョブのパラメーターを状態テーブルにすぐにプッシュします。 ターゲット テーブルの `jobid` 属性に入力を提供するために、Logic Apps は前のアクションからのテーブル出力を反復処理する **For each** ループを追加します。 各ジョブの実行 ID に対して、動的データ出力の `ResultSets JobExecutionId` を使用する **行挿入** アクションを実行して、ジョブのパラメーターを追加してアンパックし、対象のストアド プロシージャに渡します。

![ジョブを開始し、ストアド プロシージャにパラメーターを渡すために使用するアクションを示すスクリーンショット。](media/handle-long-running-stored-procedures-sql-connector/start-job-actions.png)

ジョブが完了すると、ジョブによって `LongRunningState` テーブルが更新されます。これにより、[**項目が変更されたとき** のトリガー](/connectors/sql/#when-an-item-is-modified-(v2))を使用して結果を簡単にトリガーできるようになります。 出力が不要な場合、または出力テーブルを監視するトリガーが既にある場合は、この部分をスキップできます。

![項目が変更されたときの SQL トリガーを示すスクリーンショット。](media/handle-long-running-stored-procedures-sql-connector/trigger-on-results-after-job-completes.png)

<a name="sql-on-premises-or-managed-instance"></a>

## <a name="job-agent-for-sql-server-or-azure-sql-managed-instance"></a>SQL Server のジョブ エージェントまたは Azure SQL Managed Instance

同じシナリオで、[オンプレミスの SQL Server](/sql/ssms/agent/sql-server-agent) 用の [SQL Server エージェント](../azure-sql/managed-instance/sql-managed-instance-paas-overview.md)と [Azure SQL Managed Instance](/sql/sql-server/sql-server-technical-documentation) を使用できます。 管理の詳細は異なりますが、基本的な手順は、Azure SQL Database のジョブ エージェントの設定と同じです。

## <a name="next-steps"></a>次のステップ

[SQL Server、Azure SQL Database、または Azure SQL Managed Instance に接続する](../connectors/connectors-create-api-sqlazure.md)

