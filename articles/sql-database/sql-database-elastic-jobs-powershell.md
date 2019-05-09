---
title: PowerShell を使用したエラスティック ジョブの作成と管理 | Microsoft Docs
description: Azure SQL Database プールを管理するために使用するPowerShell
services: sql-database
ms.service: sql-database
ms.subservice: scale-out
ms.custom: ''
ms.devlang: powershell
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: ''
manager: craigg
ms.date: 03/12/2019
ms.openlocfilehash: 52a12486add25cd32400af755aa6cd8cac07c6f4
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2019
ms.locfileid: "57905064"
---
# <a name="create-and-manage-sql-database-elastic-jobs-using-powershell-preview"></a>PowerShell を使用した SQL Database のエラスティック ジョブの作成と管理 (プレビュー)

**エラスティック データベース ジョブ** 用 PowerShell API (プレビュー) を使うと、スクリプトの実行対象となるデータベースのグループを定義できます。 この記事では、PowerShell のコマンドレットを使用して **エラスティック データベース ジョブ** を作成して管理する方法について説明します。 [エラスティック ジョブの概要](sql-database-elastic-jobs-overview.md)に関するページを参照してください。 

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]
> [!IMPORTANT]
> PowerShell Azure Resource Manager モジュールは Azure SQL Database で引き続きサポートされますが、今後の開発はすべて Az.Sql モジュールを対象に行われます。 これらのコマンドレットについては、「[AzureRM.Sql](https://docs.microsoft.com/powershell/module/AzureRM.Sql/)」を参照してください。 Az モジュールと AzureRm モジュールのコマンドの引数は実質的に同じです。

## <a name="prerequisites"></a>前提条件
* Azure サブスクリプション。 無料評価版については、「 [1 か月間の無料評価版](https://azure.microsoft.com/pricing/free-trial/)」をご覧ください。
* エラスティック データベース ツールで作成された一連のデータベース。 「 [エラスティック データベース ツールの概要](sql-database-elastic-scale-get-started.md)」を参照してください。
* Azure PowerShell。 詳細については、「 [Azure PowerShell のインストールと構成の方法](https://docs.microsoft.com/powershell/azure/overview)」をご覧ください。
* **Elastic Database ジョブ** PowerShell パッケージ:「[Elastic Database ジョブのインストールの概要](sql-database-elastic-jobs-service-installation.md)」を参照してください。

### <a name="select-your-azure-subscription"></a>Azure サブスクリプションを選択します。
サブスクリプションを選択するには、サブスクリプション ID (**-SubscriptionId**) とサブスクリプション名 (**-SubscriptionName**) が必要です。 複数のサブスクリプションがある場合は、**Get-AzSubscription** コマンドレットを実行して、結果セットから目的のサブスクリプション情報をコピーできます。 サブスクリプション情報を取得したら、次のコマンドレットを実行して、そのサブスクリプションを既定 (つまりジョブの作成および管理のターゲット) として設定します。

    Select-AzSubscription -SubscriptionId {SubscriptionID}

エラスティック データベース ジョブに対して PowerShell スクリプトを開発して実行する場合には、 [PowerShell ISE](https://technet.microsoft.com/library/dd315244.aspx) をお勧めします。

## <a name="elastic-database-jobs-objects"></a>エラスティック データベース ジョブ オブジェクト
次の表は、 **エラスティック データベース ジョブ** のオブジェクトの種類、説明、関連する PowerShell API の一覧です。

<table style="width:100%">
  <tr>
    <th>オブジェクトの種類</th>
    <th>説明</th>
    <th>関連する PowerShell API</th>
  </tr>
  <tr>
    <td>資格情報</td>
    <td>DACPAC のスクリプトまたはアプリケーションを実行する際に、データベースに接続するときに使用するユーザー名とパスワード。 <p>パスワードは暗号化されてから送信され、エラスティック データベース ジョブ データベースに格納されます。  パスワードは、インストール スクリプトから作成およびアップロードされた資格情報を使用して、エラスティック データベース ジョブ サービスで復号されます。</td>
    <td><p>Get-AzureSqlJobCredential</p>
    <p>New-AzureSqlJobCredential</p><p>Set-AzureSqlJobCredential</p></td></td>
  </tr>

  <tr>
    <td>スクリプト</td>
    <td>データベースに全体に対する実行に使用される Transact-SQL。  サービスにエラーが発生したときはスクリプトの実行が再試行されるので、スクリプトをべき等として作成する必要があります。
    </td>
    <td>
    <p>Get-AzureSqlJobContent</p>
    <p>Get-AzureSqlJobContentDefinition</p>
    <p>New-AzureSqlJobContent</p>
    <p>Set-AzureSqlJobContentDefinition</p>
    </td>
  </tr>

  <tr>
    <td>DACPAC</td>
    <td>データベース全体に適用される<a href="https://msdn.microsoft.com/library/ee210546.aspx">データ層アプリケーション</a> パッケージ。
    </td>
    <td>
    <p>Get-AzureSqlJobContent</p>
    <p>New-AzureSqlJobContent</p>
    <p>Set-AzureSqlJobContentDefinition</p>
    </td>
  </tr>
  <tr>
    <td>データベース ターゲット</td>
    <td>Azure SQL Database を示すデータベースとサーバーの名前。
    </td>
    <td>
    <p>Get-AzureSqlJobTarget</p>
    <p>New-AzureSqlJobTarget</p>
    </td>
  </tr>
  <tr>
    <td>シャード マップ ターゲット</td>
    <td>エラスティック データベース シャード マップ内に格納されている情報を決定するために使用される、データベース ターゲットと資格情報の組み合わせ。
    </td>
    <td>
    <p>Get-AzureSqlJobTarget</p>
    <p>New-AzureSqlJobTarget</p>
    <p>Set-AzureSqlJobTarget</p>
    </td>
  </tr>
<tr>
    <td>カスタム コレクション ターゲット</td>
    <td>一括実行に使用される定義済みのデータベース グループ。</td>
    <td>
    <p>Get-AzureSqlJobTarget</p>
    <p>New-AzureSqlJobTarget</p>
    </td>
  </tr>
<tr>
    <td>カスタム コレクション子ターゲット</td>
    <td>カスタム コレクションから参照されるデータベース ターゲット。</td>
    <td>
    <p>Add-AzureSqlJobChildTarget</p>
    <p>Remove-AzureSqlJobChildTarget</p>
    </td>
  </tr>

<tr>
    <td>ジョブ</td>
    <td>
    <p>実行のトリガーまたはスケジュールの設定に使用できるジョブのパラメーターの定義。</p>
    </td>
    <td>
    <p>Get-AzureSqlJob</p>
    <p>New-AzureSqlJob</p>
    <p>Set-AzureSqlJob</p>
    </td>
  </tr>

<tr>
    <td>ジョブ実行</td>
    <td>
    <p>実行ポリシーに従って処理されるエラーが発生した際に、スクリプトを実行するため、またはデータベース接続の資格情報を使用してターゲットに DACPAC を適用するために必要なタスクのコンテナー。</p>
    </td>
    <td>
    <p>Get-AzureSqlJobExecution</p>
    <p>Start-AzureSqlJobExecution</p>
    <p>Stop-AzureSqlJobExecution</p>
    <p>Wait-AzureSqlJobExecution</p>
  </tr>

<tr>
    <td>ジョブ タスク実行</td>
    <td>
    <p>ジョブを実行する 1 つの作業ユニット。</p>
    <p>ジョブ タスクの実行に成功しなかった場合、結果の例外メッセージがログに記録され、新しい対応するジョブ タスクが作成され、指定された実行ポリシーに従って実行されます。</p></p>
    </td>
    <td>
    <p>Get-AzureSqlJobExecution</p>
    <p>Start-AzureSqlJobExecution</p>
    <p>Stop-AzureSqlJobExecution</p>
    <p>Wait-AzureSqlJobExecution</p>
  </tr>

<tr>
    <td>ジョブの実行ポリシー</td>
    <td>
    <p>ジョブ実行のタイムアウト、再試行回数の条件、再試行間隔を制御します。</p>
    <p>エラスティック データベース ジョブには、既定のジョブ実行ポリシーがあります。既定のポリシーでは、ジョブ タスクのエラーが発生した場合の再試行回数は基本的に無限であり、再試行間隔は指数関数的に長くなります。</p>
    </td>
    <td>
    <p>Get-AzureSqlJobExecutionPolicy</p>
    <p>New-AzureSqlJobExecutionPolicy</p>
    <p>Set-AzureSqlJobExecutionPolicy</p>
    </td>
  </tr>

<tr>
    <td>スケジュール</td>
    <td>
    <p>一定間隔で繰り返し実行するか、1 回のみ実行するかの時間ベースの指定。</p>
    </td>
    <td>
    <p>Get-AzureSqlJobSchedule</p>
    <p>New-AzureSqlJobSchedule</p>
    <p>Set-AzureSqlJobSchedule</p>
    </td>
  </tr>

<tr>
    <td>ジョブのトリガー</td>
    <td>
    <p>スケジュールに従ってジョブ実行をトリガーするジョブとスケジュール間のマッピング。</p>
    </td>
    <td>
    <p>New-AzureSqlJobTrigger</p>
    <p>Remove-AzureSqlJobTrigger</p>
    </td>
  </tr>
</table>

## <a name="supported-elastic-database-jobs-group-types"></a>サポートされているエラスティック データベース ジョブ グループの種類
このジョブは、Transact-SQL (T-SQL) スクリプトまたは DACPAC のアプリケーションをデータベースのグループに対して実行します。 データベース グループ全体に対して実行するジョブを送信すると、対象のジョブが子ジョブにまで "拡張" されます。各子ジョブでは、グループ内の個々のデータベースに対して要求されたジョブ実行が実行されます。 

次の 2 種類のグループを作成できます。 

* [シャード マップ](sql-database-elastic-scale-shard-map-management.md) グループ:シャード マップを対象に送信されたジョブは、まずシャード マップを照会して現在のシャード セットを特定し、その後、シャード マップ内の各シャードに対応する子ジョブを作成します。
* カスタム コレクション グループ:カスタムの定義済みデータベース セット。 カスタム コレクションを対象とするジョブは、現在カスタム コレクション内に存在する各データベースについて子ジョブを作成します。

## <a name="to-set-the-elastic-database-jobs-connection"></a>エラスティック データベース ジョブの接続を設定するには
ジョブの API を使うには、ジョブの *管理データベース* に対する接続を先に設定しておく必要があります。 このコマンドレットを実行すると、資格情報ウィンドウがトリガーされ、エラスティック データベース ジョブをインストールするときに作成したユーザー名とパスワードの入力を求めるポップアップが表示されます。 このトピックで挙げるすべての例は、この最初の手順が実行済みであると想定しています。

エラスティック データベース ジョブに対する接続を開きます。

    Use-AzureSqlJobConnection -CurrentAzureSubscription 

## <a name="encrypted-credentials-within-the-elastic-database-jobs"></a>エラスティック データベース ジョブ内の暗号化された資格情報
データベースの資格情報は、パスワードを暗号化してジョブの*管理データベース*に挿入できます。 ジョブを後で (スケジュールを使用して) 実行できるようにするには、資格情報を保存する必要があります。

暗号化は、インストール スクリプトの一部として作成される証明書を介して利用できます。 インストール スクリプトによって、証明書が作成され、Azure クラウド サービスにアップロードされます。Azure クラウド サービスでは、保存されている暗号化済みパスワードが復号されます。 後で Azure のクラウド サービスによってジョブの*管理データベース*内にパブリック キーが保存されます。PowerShell API と Azure Portal インターフェイスは、このキーを使用して、入力されたパスワードを暗号化できます。証明書をローカルにインストールする必要はありません。

資格情報のパスワードは暗号化され、エラスティック データベース ジョブ オブジェクトへの読み取り専用アクセス権を持つユーザーから保護されます。 ただし、エラスティック データベース ジョブ オブジェクトに対する読み取り/書き込みアクセス権を持ったユーザーが悪意を持ってパスワードを抽出することは可能です。 資格情報は、ジョブ実行全体で再利用されるように設計されています。 接続を確立すると、資格情報はターゲット データベースに渡されます。 個々の資格情報に使用されるターゲット データベースについては、現在制限がありません。悪意を持ったユーザーの管理の下で、データベース ターゲットが追加される可能性は否定できません。 このデータベースを対象とするジョブがそのユーザーによって実行され、資格情報のパスワードが奪われる可能性はあります。

エラスティック データベース ジョブに関しては、セキュリティ上、以下のことが推奨されます。

* API の使用を信頼できるユーザーに制限します。
* 資格情報には、ジョブ タスクの実行に必要最小限の特権を持たせます。  詳細については、MSDN の記事「 [SQL Server の承認とアクセス許可](https://msdn.microsoft.com/library/bb669084.aspx) 」を参照してください。

### <a name="to-create-an-encrypted-credential-for-job-execution-across-databases"></a>データベース全体に対するジョブ実行用に暗号化された資格情報を作成するには
新しい暗号化された資格情報を作成するには、[**Get-Credential コマンドレット**](/powershell/module/microsoft.powershell.security/get-credential)を実行します。ユーザー名とパスワードの入力が求められます。この資格情報は [**New-AzureSqlJobCredential コマンドレット**](/powershell/module/elasticdatabasejobs/new-azuresqljobcredential)に渡すことができます。

    $credentialName = "{Credential Name}"
    $databaseCredential = Get-Credential
    $credential = New-AzureSqlJobCredential -Credential $databaseCredential -CredentialName $credentialName
    Write-Output $credential

### <a name="to-update-credentials"></a>資格情報を更新するには
パスワードが変わったときは、[**Set-AzureSqlJobCredential コマンドレット**](/powershell/module/elasticdatabasejobs/set-azuresqljobcredential)を使用して、**CredentialName** パラメーターを設定します。

    $credentialName = "{Credential Name}"
    Set-AzureSqlJobCredential -CredentialName $credentialName -Credential $credential 

## <a name="to-define-an-elastic-database-shard-map-target"></a>エラスティック データベースのシャード マップ ターゲットを定義するには
( [エラスティック データベース クライアント ライブラリ](sql-database-elastic-database-client-library.md)を使用して作成された) シャード セット内のすべてのデータベースに対してジョブを実行するには、データベース ターゲットとしてシャード マップを使用します。 この例は、エラスティック データベース クライアント ライブラリを使用して作成されたシャード アプリケーションがあることを前提としています。 「 [エラスティック データベース ツールの概要](sql-database-elastic-scale-get-started.md)」を参照してください。

シャード マップ マネージャー データベースをデータベース ターゲットとして設定する必要があります。そのうえで、特定のシャード マップをターゲットとして設定してください。

    $shardMapCredentialName = "{Credential Name}"
    $shardMapDatabaseName = "{ShardMapDatabaseName}" #example: ElasticScaleStarterKit_ShardMapManagerDb
    $shardMapDatabaseServerName = "{ShardMapServerName}"
    $shardMapName = "{MyShardMap}" #example: CustomerIDShardMap
    $shardMapDatabaseTarget = New-AzureSqlJobTarget -DatabaseName $shardMapDatabaseName -ServerName $shardMapDatabaseServerName
    $shardMapTarget = New-AzureSqlJobTarget -ShardMapManagerCredentialName $shardMapCredentialName -ShardMapManagerDatabaseName $shardMapDatabaseName -ShardMapManagerServerName $shardMapDatabaseServerName -ShardMapName $shardMapName
    Write-Output $shardMapTarget

## <a name="create-a-t-sql-script-for-execution-across-databases"></a>データベース全体に対して実行する T-SQL スクリプトを作成する
実行する T-SQL スクリプトを作成する場合、 [べき等](https://en.wikipedia.org/wiki/Idempotence) として構築し、エラーに対して回復力を持たせることをお勧めします。 エラスティック データベース ジョブでは、実行時にエラーが発生すると、エラーの分類に関係なく、スクリプトの実行が再試行されます。

[**New-AzureSqlJobContent コマンドレット**](/powershell/module/elasticdatabasejobs/new-azuresqljobcontent)を使用して、実行のスクリプトを作成して保存し、**-ContentName** および **-CommandText** パラメーターを設定します。

    $scriptName = "Create a TestTable"

    $scriptCommandText = "
    IF NOT EXISTS (SELECT name FROM sys.tables WHERE name = 'TestTable')
    BEGIN
        CREATE TABLE TestTable(
            TestTableId INT PRIMARY KEY IDENTITY,
            InsertionTime DATETIME2
        );
    END
    GO
    INSERT INTO TestTable(InsertionTime) VALUES (sysutcdatetime());
    GO"

    $script = New-AzureSqlJobContent -ContentName $scriptName -CommandText $scriptCommandText
    Write-Output $script

### <a name="create-a-new-script-from-a-file"></a>ファイルから新しいスクリプトを作成する
T-SQL スクリプトがファイル内に定義されている場合は、次のようにしてスクリプトをインポートしてください。

    $scriptName = "My Script Imported from a File"
    $scriptPath = "{Path to SQL File}"
    $scriptCommandText = Get-Content -Path $scriptPath
    $script = New-AzureSqlJobContent -ContentName $scriptName -CommandText $scriptCommandText
    Write-Output $script

### <a name="to-update-a-t-sql-script-for-execution-across-databases"></a>複数のデータベースに対して横断的に実行するよう T-SQL スクリプトを更新するには
以下に示したのは、既存のスクリプトの T-SQL コマンド テキストを更新する PowerShell スクリプトです。

目的のスクリプト定義の設定を反映するように次の変数を設定します。

    $scriptName = "Create a TestTable"
    $scriptUpdateComment = "Adding AdditionalInformation column to TestTable"
    $scriptCommandText = "
    IF NOT EXISTS (SELECT name FROM sys.tables WHERE name = 'TestTable')
    BEGIN
    CREATE TABLE TestTable(
        TestTableId INT PRIMARY KEY IDENTITY,
        InsertionTime DATETIME2
    );
    END
    GO

    IF NOT EXISTS (SELECT columns.name FROM sys.columns INNER JOIN sys.tables on columns.object_id = tables.object_id WHERE tables.name = 'TestTable' AND columns.name = 'AdditionalInformation')
    BEGIN
    ALTER TABLE TestTable
    ADD AdditionalInformation NVARCHAR(400);
    END
    GO

    INSERT INTO TestTable(InsertionTime, AdditionalInformation) VALUES (sysutcdatetime(), 'test');
    GO"

### <a name="to-update-the-definition-to-an-existing-script"></a>既存のスクリプトに対する定義を更新するには
    Set-AzureSqlJobContentDefinition -ContentName $scriptName -CommandText $scriptCommandText -Comment $scriptUpdateComment 

## <a name="to-create-a-job-to-execute-a-script-across-a-shard-map"></a>シャード マップ全体に対してスクリプトを実行するジョブを作成するには
この PowerShell スクリプトは、Elastic Scale シャード マップ内の各シャードに対して横断的にスクリプトを実行するためのジョブを開始します。

目的のスクリプトとターゲットを反映するように次の変数を設定します。

    $jobName = "{Job Name}"
    $scriptName = "{Script Name}"
    $shardMapServerName = "{Shard Map Server Name}"
    $shardMapDatabaseName = "{Shard Map Database Name}"
    $shardMapName = "{Shard Map Name}"
    $credentialName = "{Credential Name}"
    $shardMapTarget = Get-AzureSqlJobTarget -ShardMapManagerDatabaseName $shardMapDatabaseName -ShardMapManagerServerName $shardMapServerName -ShardMapName $shardMapName 
    $job = New-AzureSqlJob -ContentName $scriptName -CredentialName $credentialName -JobName $jobName -TargetId $shardMapTarget.TargetId
    Write-Output $job

## <a name="to-execute-a-job"></a>ジョブを実行するには
以下に示したのは、既存のジョブを実行する PowerShell スクリプトです。

次の変数を使用して、実行する目的のジョブ名を反映します。

    $jobName = "{Job Name}"
    $jobExecution = Start-AzureSqlJobExecution -JobName $jobName 
    Write-Output $jobExecution

## <a name="to-retrieve-the-state-of-a-single-job-execution"></a>1 つのジョブ実行の状態を取得するには
ジョブ実行の状態を確認するには、[**Get-AzureSqlJobExecution コマンドレット**](/powershell/module/elasticdatabasejobs/get-azuresqljobexecution)を使用して、**JobExecutionId** パラメーターを設定します。

    $jobExecutionId = "{Job Execution Id}"
    $jobExecution = Get-AzureSqlJobExecution -JobExecutionId $jobExecutionId
    Write-Output $jobExecution

子ジョブ実行の状態 (つまり、ジョブのターゲットである各データベースに対する各ジョブ実行の状態) を確認するには、**IncludeChildren** パラメーターを指定して、同じ **Get-AzureSqlJobExecution** コマンドレットを実行します。

    $jobExecutionId = "{Job Execution Id}"
    $jobExecutions = Get-AzureSqlJobExecution -JobExecutionId $jobExecutionId -IncludeChildren
    Write-Output $jobExecutions 

## <a name="to-view-the-state-across-multiple-job-executions"></a>複数のジョブ実行全体の状態を確認するには
[**Get AzureSqlJobExecution コマンドレット**](/powershell/module/elasticdatabasejobs/new-azuresqljob)には、省略可能なパラメーターが複数あり、指定したパラメーターでフィルターされた複数のジョブ実行を表示できます。 次に、Get-AzureSqlJobExecution の使用例を示します。

すべてのアクティブな最上位レベル ジョブ実行を取得します。

    Get-AzureSqlJobExecution

非アクティブなジョブ実行を含む、すべての最上位レベルのジョブ実行を取得します。

    Get-AzureSqlJobExecution -IncludeInactive

非アクティブなジョブ実行を含め、指定したジョブ実行 ID のすべての子ジョブ実行を取得します。

    $parentJobExecutionId = "{Job Execution Id}"
    Get-AzureSqlJobExecution -AzureSqlJobExecution -JobExecutionId $parentJobExecutionId -IncludeInactive -IncludeChildren

非アクティブなジョブ実行を含め、スケジュールとジョブの組み合わせを使用して作成したすべてのジョブ実行を取得します。

    $jobName = "{Job Name}"
    $scheduleName = "{Schedule Name}"
    Get-AzureSqlJobExecution -JobName $jobName -ScheduleName $scheduleName -IncludeInactive

非アクティブなジョブを含め、指定したシャード マップを対象とするすべてのジョブ実行を取得します。

    $shardMapServerName = "{Shard Map Server Name}"
    $shardMapDatabaseName = "{Shard Map Database Name}"
    $shardMapName = "{Shard Map Name}"
    $target = Get-AzureSqlJobTarget -ShardMapManagerDatabaseName $shardMapDatabaseName -ShardMapManagerServerName $shardMapServerName -ShardMapName $shardMapName
    Get-AzureSqlJobExecution -TargetId $target.TargetId -IncludeInactive

非アクティブなジョブを含め、指定したカスタム コレクションを対象とするすべてのジョブ実行を取得します。

    $customCollectionName = "{Custom Collection Name}"
    $target = Get-AzureSqlJobTarget -CustomCollectionName $customCollectionName
    Get-AzureSqlJobExecution -TargetId $target.TargetId -IncludeInactive

指定したジョブ実行内のジョブ タスク実行の一覧を取得します。

    $jobExecutionId = "{Job Execution Id}"
    $jobTaskExecutions = Get-AzureSqlJobTaskExecution -JobExecutionId $jobExecutionId
    Write-Output $jobTaskExecutions 

ジョブ タスク実行の詳細を取得します。

次の PowerShell スクリプトを使用すると、ジョブ タスク実行の詳細を確認できます。実行エラーをデバッグするときに特に役立ちます。

    $jobTaskExecutionId = "{Job Task Execution Id}"
    $jobTaskExecution = Get-AzureSqlJobTaskExecution -JobTaskExecutionId $jobTaskExecutionId
    Write-Output $jobTaskExecution

## <a name="to-retrieve-failures-within-job-task-executions"></a>ジョブ タスク実行内のエラーを取得するには
**JobTaskExecution オブジェクト** には、タスクの Lifecycle プロパティと Message プロパティが含まれています。 ジョブ タスク実行に失敗すると、Lifecycle プロパティは *Failed* に設定され、Message プロパティは結果の例外メッセージとそのスタックに設定されます。 ジョブが成功しなかった場合、特定のジョブが成功しなかったジョブ タスクの詳細を確認することをお勧めします。

    $jobExecutionId = "{Job Execution Id}"
    $jobTaskExecutions = Get-AzureSqlJobTaskExecution -JobExecutionId $jobExecutionId
    Foreach($jobTaskExecution in $jobTaskExecutions) 
        {
        if($jobTaskExecution.Lifecycle -ne 'Succeeded')
            {
            Write-Output $jobTaskExecution
            }
        }

## <a name="to-wait-for-a-job-execution-to-complete"></a>ジョブ実行の完了を待機するには
次の PowerShell スクリプトを使用すると、ジョブ タスクが完了するまで待機することができます。

    $jobExecutionId = "{Job Execution Id}"
    Wait-AzureSqlJobExecution -JobExecutionId $jobExecutionId 

## <a name="create-a-custom-execution-policy"></a>カスタム実行ポリシーを作成する
Elastic Database ジョブは、ジョブの開始時に適用できるカスタム実行ポリシーの作成をサポートしています。

現在、実行ポリシーでは以下を定義できます。

* 名前:実行ポリシーの識別子。
* ジョブのタイムアウト:Elastic Database ジョブによってジョブが取り消されるまでの合計時間。
* 初期再試行間隔:最初の再試行する前に待機する間隔。
* 最大再試行間隔:使用する再試行間隔の上限。
* 再試行間隔のバックオフ係数:次回の再試行間隔の計算に使用される係数。  (初期再試行間隔) * Math.pow((間隔のバックオフ係数), (再試行回数) - 2) という式が使用されます。 
* 最大試行回数:1 つのジョブ内で実行する最大再試行回数。

既定の実行ポリシーでは、次の値を使用します。

* 名前:既定の実行ポリシー
* ジョブのタイムアウト:1 週間
* 初期再試行間隔:100 ミリ秒
* 最大再試行間隔:30 分
* 再試行間隔係数:2
* 最大試行回数:2,147,483,647

必要な実行ポリシーを作成します。

    $executionPolicyName = "{Execution Policy Name}"
    $initialRetryInterval = New-TimeSpan -Seconds 10
    $jobTimeout = New-TimeSpan -Minutes 30
    $maximumAttempts = 999999
    $maximumRetryInterval = New-TimeSpan -Minutes 1
    $retryIntervalBackoffCoefficient = 1.5
    $executionPolicy = New-AzureSqlJobExecutionPolicy -ExecutionPolicyName $executionPolicyName -InitialRetryInterval $initialRetryInterval -JobTimeout $jobTimeout -MaximumAttempts $maximumAttempts -MaximumRetryInterval $maximumRetryInterval 
    -RetryIntervalBackoffCoefficient $retryIntervalBackoffCoefficient
    Write-Output $executionPolicy

### <a name="update-a-custom-execution-policy"></a>カスタム実行ポリシーを更新する
更新対象の実行ポリシーを更新します。

    $executionPolicyName = "{Execution Policy Name}"
    $initialRetryInterval = New-TimeSpan -Seconds 15
    $jobTimeout = New-TimeSpan -Minutes 30
    $maximumAttempts = 999999
    $maximumRetryInterval = New-TimeSpan -Minutes 1
    $retryIntervalBackoffCoefficient = 1.5
    $updatedExecutionPolicy = Set-AzureSqlJobExecutionPolicy -ExecutionPolicyName $executionPolicyName -InitialRetryInterval $initialRetryInterval -JobTimeout $jobTimeout -MaximumAttempts $maximumAttempts -MaximumRetryInterval $maximumRetryInterval -RetryIntervalBackoffCoefficient $retryIntervalBackoffCoefficient
    Write-Output $updatedExecutionPolicy

## <a name="cancel-a-job"></a>ジョブを取り消す
エラスティック データベース ジョブは、ジョブの取り消し要求をサポートしています。  エラスティック データベース ジョブで、現在実行されているジョブの取り消し要求が検出されると、ジョブの停止が試行されます。

エラスティック データベース ジョブには、2 種類の取り消し方法があります。

1. 現在実行中のタスクを取り消す:タスクの実行中に取り消しが検出されると、現在実行中のタスクの範囲内で取り消しが試行されます。  例: 取り消しを試行したときに、実行時間が長いクエリが実行中の場合、クエリの取り消しが試行されます。
2. タスクの再試行を取り消す:タスクの実行が開始される前に、コントロール スレッドによって取り消しが検出されると、タスクの開始が回避され、要求は取り消し済みと宣言されます。

親ジョブについてジョブの取り消しが要求されると、親ジョブとそのすべての子ジョブについて、取り消し要求が受け入れられます。

取り消し要求を送信するには、[**Stop-AzureSqlJobExecution コマンドレット**](/powershell/module/elasticdatabasejobs/stop-azuresqljobexecution)を使用し、**JobExecutionId** パラメーターを設定します。

    $jobExecutionId = "{Job Execution Id}"
    Stop-AzureSqlJobExecution -JobExecutionId $jobExecutionId

## <a name="to-delete-a-job-and-job-history-asynchronously"></a>ジョブとジョブ履歴を非同期的に削除するには
エラスティック データベース ジョブは、ジョブの非同期削除をサポートしています。 ジョブを削除対象としてマークすることができます。マークされたジョブのすべてのジョブ実行が完了すると、ジョブとそのジョブ履歴は削除されます。 アクティブなジョブ実行は自動的に取り消されません。  

アクティブなジョブ実行を取り消すには、[**Stop-AzureSqlJobExecution**](/powershell/module/elasticdatabasejobs/stop-azuresqljobexecution) を呼び出します。

ジョブの削除をトリガーするには、[**Remove-AzureSqlJob コマンドレット**](/powershell/module/elasticdatabasejobs/remove-azuresqljob)を使用して、**JobName** パラメーターを設定します。

    $jobName = "{Job Name}"
    Remove-AzureSqlJob -JobName $jobName

## <a name="to-create-a-custom-database-target"></a>カスタム データベース ターゲットを作成するには
カスタム データベース ターゲットは、直接実行するように定義できるほか、カスタム データベース グループに含めて定義することができます。 たとえば、**エラスティック プール**を PowerShell API で直接扱うことが現時点ではできません。そこで、カスタム データベース ターゲットとカスタム データベース コレクション ターゲットを作成し、そこにプール内のデータベースをすべて含めるようにします。

目的のデータベース情報を反映するように、次の変数を設定します。

    $databaseName = "{Database Name}"
    $databaseServerName = "{Server Name}"
    New-AzureSqlJobTarget -DatabaseName $databaseName -ServerName $databaseServerName 

## <a name="to-create-a-custom-database-collection-target"></a>カスタム データベース コレクション ターゲットを作成するには
[**New-AzureSqlJobTarget**](/powershell/module/elasticdatabasejobs/new-azuresqljobtarget) コマンドレットを使用してカスタム データベース コレクション ターゲットを定義します。これで、定義済みの複数のデータベース ターゲットに対する横断的な実行が可能になります。 データベース グループを作成したら、カスタム コレクション ターゲットにデータベースを関連付けてください。

目的のカスタム コレクション ターゲットの構成を反映するように次の変数を設定します。

    $customCollectionName = "{Custom Database Collection Name}"
    New-AzureSqlJobTarget -CustomCollectionName $customCollectionName 

### <a name="to-add-databases-to-a-custom-database-collection-target"></a>カスタム データベース コレクション ターゲットにデータベースを追加するには
特定のカスタム コレクションにデータベースを追加するには、[**Add-AzureSqlJobChildTarget**](/powershell/module/elasticdatabasejobs/add-azuresqljobchildtarget) コマンドレットを使用します。

    $databaseServerName = "{Database Server Name}"
    $databaseName = "{Database Name}"
    $customCollectionName = "{Custom Database Collection Name}"
    Add-AzureSqlJobChildTarget -CustomCollectionName $customCollectionName -DatabaseName $databaseName -ServerName $databaseServerName 

#### <a name="review-the-databases-within-a-custom-database-collection-target"></a>カスタム データベース コレクション ターゲット内のデータベースを確認する
[**Get-AzureSqlJobTarget**](/powershell/module/elasticdatabasejobs/new-azuresqljobtarget) コマンドレットを使用して、カスタム データベース コレクション ターゲット内の子データベースを取得します。 

    $customCollectionName = "{Custom Database Collection Name}"
    $target = Get-AzureSqlJobTarget -CustomCollectionName $customCollectionName
    $childTargets = Get-AzureSqlJobTarget -ParentTargetId $target.TargetId
    Write-Output $childTargets

### <a name="create-a-job-to-execute-a-script-across-a-custom-database-collection-target"></a>カスタム データベース コレクション ターゲット全体に対してスクリプトを実行するジョブを作成する
[**New-AzureSqlJob**](/powershell/module/elasticdatabasejobs/new-azuresqljob) コマンドレットを使用して、カスタム データベース コレクション ターゲットに定義されているデータベース グループに対するジョブを作成します。 Elastic Database ジョブによって、カスタム データベース コレクション ターゲットに関連付けられているデータベースに対応する複数の子ジョブにまでジョブの対象が拡張されるので、各データベースに対して確実にスクリプトが実行されます。 ここでも、スクリプトをべき等にして、再試行に対して回復力を持たせるようにすることが重要です。

    $jobName = "{Job Name}"
    $scriptName = "{Script Name}"
    $customCollectionName = "{Custom Collection Name}"
    $credentialName = "{Credential Name}"
    $target = Get-AzureSqlJobTarget -CustomCollectionName $customCollectionName
    $job = New-AzureSqlJob -JobName $jobName -CredentialName $credentialName -ContentName $scriptName -TargetId $target.TargetId
    Write-Output $job

## <a name="data-collection-across-databases"></a>データベース全体のデータ収集
ジョブを使用してデータベースのグループ全体に対してクエリを実行し、その結果を特定のテーブルに送信することができます。 その結果の後にテーブルに対してクエリを実行して、各データベースのクエリ結果を確認することができます。 この方法で、多数のデータベース全体に対してクエリを実行する非同期メソッドを実現できます。 試行の失敗は、再試行によって自動的に処理されます。

指定した対象テーブルは、既存のテーブルがない場合、自動的に作成されます。 新たに作成されるテーブルには、返された結果セットと同じスキーマが適用されます。 複数の結果セットが返された場合、指定した対象テーブルにエラスティック データベース ジョブから送信されるのは、最初の結果セットのみです。

以下の PowerShell スクリプトは、その実行結果を特定のテーブルに収集しています。 このスクリプトは、単一の結果セットを出力する T-SQL スクリプトが作成済みであること、また、カスタム データベース コレクション ターゲットが作成済みであることを前提としています。

このスクリプトは、[**Get-AzureSqlJobTarget**](/powershell/module/elasticdatabasejobs/new-azuresqljobtarget) コマンドレットを使用しています。 スクリプト、資格情報、実行ターゲットのパラメーターを設定してください。

    $jobName = "{Job Name}"
    $scriptName = "{Script Name}"
    $executionCredentialName = "{Execution Credential Name}"
    $customCollectionName = "{Custom Collection Name}"
    $destinationCredentialName = "{Destination Credential Name}"
    $destinationServerName = "{Destination Server Name}"
    $destinationDatabaseName = "{Destination Database Name}"
    $destinationSchemaName = "{Destination Schema Name}"
    $destinationTableName = "{Destination Table Name}"
    $target = Get-AzureSqlJobTarget -CustomCollectionName $customCollectionName

### <a name="to-create-and-start-a-job-for-data-collection-scenarios"></a>データ収集シナリオのジョブを作成して開始するには
このスクリプトは、[**Start-AzureSqlJobExecution**](/powershell/module/elasticdatabasejobs/start-azuresqljobexecution) コマンドレットを使用しています。

    $job = New-AzureSqlJob -JobName $jobName 
    -CredentialName $executionCredentialName 
    -ContentName $scriptName 
    -ResultSetDestinationServerName $destinationServerName 
    -ResultSetDestinationDatabaseName $destinationDatabaseName 
    -ResultSetDestinationSchemaName $destinationSchemaName 
    -ResultSetDestinationTableName $destinationTableName 
    -ResultSetDestinationCredentialName $destinationCredentialName 
    -TargetId $target.TargetId
    Write-Output $job
    $jobExecution = Start-AzureSqlJobExecution -JobName $jobName
    Write-Output $jobExecution

## <a name="to-schedule-a-job-execution-trigger"></a>ジョブ実行トリガーのスケジュールを設定するには
次の PowerShell スクリプトを使用すると、定期的なスケジュールを作成できます。 このスクリプトでは分間隔を使用していますが、[**New-AzureSqlJobSchedule**](/powershell/module/elasticdatabasejobs/new-azuresqljobschedule) は -DayInterval、-HourInterval、-MonthInterval、-WeekInterval の各パラメーターもサポートしています。 1 回だけ実行するスケジュールを作成するには、-OneTime を渡します。

新しいスケジュールを作成します。

    $scheduleName = "Every one minute"
    $minuteInterval = 1
    $startTime = (Get-Date).ToUniversalTime()
    $schedule = New-AzureSqlJobSchedule 
    -MinuteInterval $minuteInterval 
    -ScheduleName $scheduleName 
    -StartTime $startTime 
    Write-Output $schedule

### <a name="to-trigger-a-job-executed-on-a-time-schedule"></a>トリガーによって時間スケジュールに従ってジョブを実行するには
時間スケジュールに従ってジョブを実行するようにジョブ トリガーを定義できます。 次の PowerShell スクリプトを使用すると、ジョブ トリガーを作成できます。

[New-AzureSqlJobTrigger](/powershell/module/elasticdatabasejobs/new-azuresqljobtrigger) を使用し、目的のジョブとスケジュールに対応するように次の変数を設定します。

    $jobName = "{Job Name}"
    $scheduleName = "{Schedule Name}"
    $jobTrigger = New-AzureSqlJobTrigger
    -ScheduleName $scheduleName
    -JobName $jobName
    Write-Output $jobTrigger

### <a name="to-remove-a-scheduled-association-to-stop-job-from-executing-on-schedule"></a>スケジュールされた関連付けを解除してジョブのスケジュール実行を停止するには
ジョブ トリガーによるジョブ実行の繰り返しを停止するには、ジョブ トリガーを削除します。 [**Remove-AzureSqlJobTrigger コマンドレット**](/powershell/module/elasticdatabasejobs/remove-azuresqljobtrigger)を使用してジョブ トリガーを削除すると、スケジュールに従ってジョブが実行されなくなります。

    $jobName = "{Job Name}"
    $scheduleName = "{Schedule Name}"
    Remove-AzureSqlJobTrigger 
    -ScheduleName $scheduleName 
    -JobName $jobName

### <a name="retrieve-job-triggers-bound-to-a-time-schedule"></a>時間スケジュールにバインドされているジョブ トリガーを取得する
次の PowerShell スクリプトを使用すると、特定の時間スケジュール内に登録されているジョブ トリガーが取得され、表示されます。

    $scheduleName = "{Schedule Name}"
    $jobTriggers = Get-AzureSqlJobTrigger -ScheduleName $scheduleName
    Write-Output $jobTriggers

### <a name="to-retrieve-job-triggers-bound-to-a-job"></a>ジョブにバインドされているジョブ トリガーを取得するには
登録されているジョブを含んだスケジュールを取得して表示するには、 [Get-AzureSqlJobTrigger](/powershell/module/elasticdatabasejobs/get-azuresqljobtrigger) を使用します。

    $jobName = "{Job Name}"
    $jobTriggers = Get-AzureSqlJobTrigger -JobName $jobName
    Write-Output $jobTriggers

## <a name="to-create-a-data-tier-application-dacpac-for-execution-across-databases"></a>複数のデータベースに対する横断的実行に対応したデータ層アプリケーション (DACPAC) を作成するには
DACPAC の作成については、 [データ層アプリケーション](https://msdn.microsoft.com/library/ee210546.aspx)に関するページを参照してください。 DACPAC をデプロイするには、 [New-AzureSqlJobContent コマンドレット](/powershell/module/elasticdatabasejobs/new-azuresqljobcontent)を使用します。 サービスから DACPAC にアクセスできることが必要です。 作成した DACPAC は Azure Storage にアップロードし、DACPAC 用の [Shared Access Signature](../storage/common/storage-dotnet-shared-access-signature-part-1.md) を作成することをお勧めします。

    $dacpacUri = "{Uri}"
    $dacpacName = "{Dacpac Name}"
    $dacpac = New-AzureSqlJobContent -DacpacUri $dacpacUri -ContentName $dacpacName 
    Write-Output $dacpac

### <a name="to-update-a-data-tier-application-dacpac-for-execution-across-databases"></a>複数のデータベースに対する横断的実行に対応したデータ層アプリケーション (DACPAC) を更新するには
エラスティック データベース ジョブ内に登録されている既存の DACPAC を、新しい URI を示すように更新することができます。 登録されている既存の DACPAC に対し、[**Set-AzureSqlJobContentDefinition コマンドレット**](/powershell/module/elasticdatabasejobs/set-azuresqljobcontentdefinition)を使用して DACPAC の URI を更新します。

    $dacpacName = "{Dacpac Name}"
    $newDacpacUri = "{Uri}"
    $updatedDacpac = Set-AzureSqlJobDacpacDefinition -ContentName $dacpacName -DacpacUri $newDacpacUri
    Write-Output $updatedDacpac

## <a name="to-create-a-job-to-apply-a-data-tier-application-dacpac-across-databases"></a>複数のデータベースに対して横断的にデータ層アプリケーション (DACPAC) を適用するジョブを作成するには
エラスティック データベース ジョブ内に DACPAC を作成すると、データベース グループ全体に対して DACPAC を適用するジョブを作成できます。 次の PowerShell スクリプトを使用すると、カスタムのデータベース コレクション全体に対する DACPAC ジョブを作成できます。

    $jobName = "{Job Name}"
    $dacpacName = "{Dacpac Name}"
    $customCollectionName = "{Custom Collection Name}"
    $credentialName = "{Credential Name}"
    $target = Get-AzureSqlJobTarget 
    -CustomCollectionName $customCollectionName
    $job = New-AzureSqlJob 
    -JobName $jobName 
    -CredentialName $credentialName 
    -ContentName $dacpacName -TargetId $target.TargetId
    Write-Output $job 

[!INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]

<!--Image references-->
[1]: ./media/sql-database-elastic-jobs-powershell/cmd-prompt.png
[2]: ./media/sql-database-elastic-jobs-powershell/portal.png
<!--anchors-->
