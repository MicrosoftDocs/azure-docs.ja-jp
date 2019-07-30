---
title: Azure Backup を使用した SQL Server データベースのバックアップのトラブルシューティング | Microsoft Docs
description: Azure VM で実行されている SQL Server データベースの Azure Backup によるバックアップに関するトラブルシューティング情報です。
author: anuragm
manager: sivan
ms.service: backup
ms.topic: article
ms.date: 06/18/2019
ms.author: anuragm
ms.openlocfilehash: c601ecb6997834aa216de094e2809670833dd9cb
ms.sourcegitcommit: c72ddb56b5657b2adeb3c4608c3d4c56e3421f2c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/24/2019
ms.locfileid: "68464917"
---
# <a name="troubleshoot-sql-server-database-backup-by-using-azure-backup"></a>Azure Backup を使用した SQL Server データベースのバックアップのトラブルシューティング

この記事では、Azure 仮想マシンで実行されている SQL Server データベースに関するトラブルシューティング情報を提供します。

バックアップ プロセスと制限事項の詳細については、「[Azure VM での SQL Server Backup について](backup-azure-sql-database.md#feature-consideration-and-limitations)」を参照してください。

## <a name="sql-server-permissions"></a>SQL Server のアクセス許可

仮想マシン上で SQL Server データベースの保護を構成するには、その仮想マシンに **AzureBackupWindowsWorkload** 拡張機能をインストールする必要があります。 **UserErrorSQLNoSysadminMembership** エラーが発生した場合は、必要なバックアップ アクセス許可が SQL Server インスタンスにないことを意味します。 このエラーを解決するには、「[VM のアクセス許可を設定する](backup-azure-sql-database.md#set-vm-permissions)」の手順に従います。

## <a name="error-messages"></a>エラー メッセージ

### <a name="backup-type-unsupported"></a>バックアップの種類がサポートされていません

| Severity | 説明 | 考えられる原因 | 推奨される操作 |
|---|---|---|---|
| 警告 | このデータベースの現在の設定は、関連するポリシーに存在する特定のバックアップ タイプをサポートしていません。 | <li>マスター データベースに対して実行できるのは、データベース完全バックアップ操作だけです。 差分バックアップやトランザクション ログ バックアップは実行できません。 </li> <li>単純復旧モデルのデータベースでは、トランザクション ログのバックアップはできません。</li> | ポリシー内のバックアップ タイプがすべてサポートされるようにデータベースの設定を変更してください。 または、現在のポリシーを変更して、サポート対象のバックアップ タイプだけを含めてください。 それ以外の場合、スケジュールされたバックアップ中、サポート対象外のバックアップ タイプはスキップされるか、アドホック バックアップのバックアップ ジョブでエラーが発生します。


### <a name="usererrorsqlpodoesnotsupportbackuptype"></a>UserErrorSQLPODoesNotSupportBackupType

| エラー メッセージ | 考えられる原因 | 推奨される操作 |
|---|---|---|
| この SQL データベースは、要求されたバックアップの種類をサポートしていません。 | データベース復旧モデルが要求されたバックアップの種類を許可していない場合に発生します。 このエラーは、以下の状況で発生する可能性があります。 <br/><ul><li>単純復旧モデルを使用するデータベースで、ログ バックアップが許可されていない。</li><li>マスター データベースで、差分バックアップとログ バックアップが許可されていない。</li></ul>詳細については、[SQL Server 復旧モデル](https://docs.microsoft.com/sql/relational-databases/backup-restore/recovery-models-sql-server)に関するドキュメントを参照してください。 | 単純復旧モデルのデータベースのログ バックアップが失敗した場合は、次のいずれかのオプションを試してください。<ul><li>データベースが単純復旧モードの場合は、ログ バックアップを無効にします。</li><li>データベースの復旧モデルを完全または一括ログに変更するには、[SQL Server のドキュメント](https://docs.microsoft.com/sql/relational-databases/backup-restore/view-or-change-the-recovery-model-of-a-database-sql-server)を参照してください。 </li><li> 復旧モデルを変更したくない場合で、変更できない複数のデータベースをバックアップする標準ポリシーがある場合は、エラーを無視してください。 完全バックアップと差分バックアップはスケジュールに従って動作します。 ログ バックアップはスキップされますが、この場合は想定どおりの動作です。</li></ul>マスター データベースで、差分バックアップまたはログ バックアップを構成した場合は、次のいずれかの手順を実行します。<ul><li>ポータルを使用して、マスター データベースのバックアップ ポリシー スケジュールを [完全] に変更します。</li><li>変更できない複数のデータベースをバックアップする標準ポリシーがある場合は、エラーを無視してください。 完全バックアップはスケジュールに従って動作します。 差分バックアップまたはログ バックアップは行われませんが、この場合は想定どおりの動作です。</li></ul> |
| 競合する操作が既に同じデータベースに対して実行されているため、操作がキャンセルされました。 | 同時に実行される[バックアップと復元の制限事項に関するブログ エントリ](https://blogs.msdn.microsoft.com/arvindsh/2008/12/30/concurrency-of-full-differential-and-log-backups-on-the-same-database)を参照してください。| [SQL Server Management Studio (SSMS) を使用してバックアップ ジョブを監視します](manage-monitor-sql-database-backup.md)。 競合する操作が失敗したら、操作を再開します。|

### <a name="usererrorsqlpodoesnotexist"></a>UserErrorSQLPODoesNotExist

| エラー メッセージ | 考えられる原因 | 推奨される操作 |
|---|---|---|
| SQL データベースが存在しません。 | データベースは削除されたか、名前が変更されました。 | データベースが誤って削除されたか、名前が変更されたかを確認してください。<br/><br/> 誤ってデータベースを削除した場合、バックアップを続行するために、元の場所にデータベースを復元してください。<br/><br/> データベースを削除し、今後のバックアップが必要ない場合は、Recovery Services コンテナーで **[バックアップの停止]** を選択し、 **[バックアップ データの保持]** または **[バックアップ データの削除]** を指定します。 詳細については、「[バックアップされる SQL Server データベースを管理および監視する](manage-monitor-sql-database-backup.md)」を参照してください。

### <a name="usererrorsqllsnvalidationfailure"></a>UserErrorSQLLSNValidationFailure

| エラー メッセージ | 考えられる原因 | 推奨される操作 |
|---|---|---|
| ログ チェーンが壊れています。 | データベースまたは VM は、ログ チェーンを切り捨てる別のバックアップ ソリューションを使用してバックアップされます。|<ul><li>他のバックアップ ソリューションまたはスクリプトが使用されているかどうかを確認します。 使用されている場合は、他のバックアップ ソリューションを停止します。 </li><li>バックアップがアドホック ログ バックアップの場合は、完全バックアップをトリガーして新しいログ チェーンを開始します。 スケジュールされたログ バックアップの場合、Azure Backup サービスがこの問題を解決するために完全バックアップを自動的にトリガーするため、対処は必要ありません。</li>|

### <a name="usererroropeningsqlconnection"></a>UserErrorOpeningSQLConnection

| エラー メッセージ | 考えられる原因 | 推奨される操作 |
|---|---|---|
| Azure Backup は SQL インスタンスに接続できません。 | Azure Backup は SQL Server インスタンスに接続できません。 | Azure portal のエラー メニューで追加情報を使用して、根本原因を絞り込んでください。 エラーの修正については、[SQL バックアップのトラブルシューティング](https://docs.microsoft.com/sql/database-engine/configure-windows/troubleshoot-connecting-to-the-sql-server-database-engine)に関するページを参照してください。<br/><ul><li>既定の SQL 設定でリモート接続が許可されていない場合は、設定を変更します。 設定の変更については、次の記事を参照してください。<ul><li>[MSSQLSERVER_-1](/previous-versions/sql/sql-server-2016/bb326495(v=sql.130))</li><li>[MSSQLSERVER_2](/sql/relational-databases/errors-events/mssqlserver-2-database-engine-error)</li><li>[MSSQLSERVER_53](/sql/relational-databases/errors-events/mssqlserver-53-database-engine-error)</li></ul></li></ul><ul><li>ログインの問題がある場合は、これらのリンクを使用して修正してください。<ul><li>[MSSQLSERVER_18456](/sql/relational-databases/errors-events/mssqlserver-18456-database-engine-error)</li><li>[MSSQLSERVER_18452](/sql/relational-databases/errors-events/mssqlserver-18452-database-engine-error)</li></ul></li></ul> |

### <a name="usererrorparentfullbackupmissing"></a>UserErrorParentFullBackupMissing

| エラー メッセージ | 考えられる原因 | 推奨される操作 |
|---|---|---|
| このデータ ソースの最初の完全バックアップが見つかりません。 | データベースの完全バックアップが見つかりません。 ログ バックアップと差分バックアップは、完全バックアップの親なので、差分バックアップまたはログ バックアップをトリガーする前に必ず完全バックアップを作成してください。 | アドホック完全バックアップをトリガーします。   |

### <a name="usererrorbackupfailedastransactionlogisfull"></a>UserErrorBackupFailedAsTransactionLogIsFull

| エラー メッセージ | 考えられる原因 | 推奨される操作 |
|---|---|---|
| データ ソースのトランザクション ログがいっぱいなので、バックアップを作成できません。 | データベースのトランザクション ログ領域がいっぱいです。 | この問題を解決するには、[SQL Server のドキュメント](https://docs.microsoft.com/sql/relational-databases/errors-events/mssqlserver-9002-database-engine-error)を参照してください。 |

### <a name="usererrorcannotrestoreexistingdbwithoutforceoverwrite"></a>UserErrorCannotRestoreExistingDBWithoutForceOverwrite

| エラー メッセージ | 考えられる原因 | 推奨される操作 |
|---|---|---|
| 同じ名前のデータベースが既にターゲットの場所に存在しています | ターゲットの復元先には既に同じ名前のデータベースがあります。  | <ul><li>ターゲット データベース名を変更してください。</li><li>または、復元ページで強制上書きオプションを使用します。</li> |

### <a name="usererrorrestorefaileddatabasecannotbeofflined"></a>UserErrorRestoreFailedDatabaseCannotBeOfflined

| エラー メッセージ | 考えられる原因 | 推奨される操作 |
|---|---|---|
| データベースをオフラインにできないため、復元に失敗しました。 | 復元の実行中は、ターゲット データベースをオフラインにする必要があります。 Azure Backup は、このデータをオフラインにできません。 | Azure portal のエラー メニューで追加情報を使用して、根本原因を絞り込んでください。 詳細については [SQL Server のドキュメント](https://docs.microsoft.com/sql/relational-databases/backup-restore/restore-a-database-backup-using-ssms)を参照してください。 |

###  <a name="usererrorcannotfindservercertificatewiththumbprint"></a>UserErrorCannotFindServerCertificateWithThumbprint

| エラー メッセージ | 考えられる原因 | 推奨される操作 |
|---|---|---|
| ターゲット上に拇印のあるサーバー証明書が見つかりません。 | 宛先のインスタンス上のマスター データベースに有効な暗号化拇印がありません。 | ソース インスタンスで使用されている有効な証明書の拇印をターゲット インスタンスにインポートしてください。 |

### <a name="usererrorrestorenotpossiblebecauselogbackupcontainsbulkloggedchanges"></a>UserErrorRestoreNotPossibleBecauseLogBackupContainsBulkLoggedChanges

| エラー メッセージ | 考えられる原因 | 推奨される操作 |
|---|---|---|
| 復旧に使用されるログ バックアップに一括ログの変更が含まれています。 これを、SQL ガイドラインに従って任意の時点で停止するために使用することはできません。 | データベースが一括ログ復旧モードである場合は、一括ログ トランザクションと次のログ トランザクションの間のデータを復旧できません。 | 別の復旧時点を選択してください。 [詳細情報](https://docs.microsoft.com/previous-versions/sql/sql-server-2008-r2/ms186229(v=sql.105))。


### <a name="fabricsvcbackuppreferencecheckfailedusererror"></a>FabricSvcBackupPreferenceCheckFailedUserError

| エラー メッセージ | 考えられる原因 | 推奨される操作 |
|---|---|---|
| 可用性グループの一部のノードが登録されていないため、SQL Always On 可用性グループのバックアップ設定を満たすことができません。 | バックアップを実行するために必要なノードが登録されていないか、到達できません。 | <ul><li>このデータベースのバックアップを実行するために必要なすべてのノードが登録され、正常であることを確認してから、操作を再試行してください。</li><li>SQL Server Always On 可用性グループのバックアップ設定を変更してください。</li></ul> |

### <a name="vmnotinrunningstateusererror"></a>VMNotInRunningStateUserError

| エラー メッセージ | 考えられる原因 | 推奨される操作 |
|---|---|---|
| SQL Server VM はシャットダウンされており、Azure Backup サービスにアクセスできません。 | VM がシャットダウンされています。 | SQL Server インスタンスが実行されていることを確認してください。 |

### <a name="guestagentstatusunavailableusererror"></a>GuestAgentStatusUnavailableUserError

| エラー メッセージ | 考えられる原因 | 推奨される操作 |
|---|---|---|
| Azure Backup サービスは Azure VM ゲスト エージェントを使用してバックアップを行いますが、ターゲット サーバーではゲスト エージェントを使用できません。 | ゲスト エージェントが有効ではないか、正常ではありません。 | [VM ゲスト エージェントを手動でインストール](../virtual-machines/extensions/agent-windows.md)します。 |

### <a name="autoprotectioncancelledornotvalid"></a>AutoProtectionCancelledOrNotValid

| エラー メッセージ | 考えられる原因 | 推奨される操作 |
|---|---|---|
| 自動保護の意図が削除されたか、有効でなくなりました。 | SQL Server インスタンスで自動保護を有効にすると、そのインスタンス内のすべてのデータベースに対して **[バックアップの構成]** のジョブが実行されます。 ジョブの実行中に自動保護を無効にした場合、 **[In-Progress]\(進行中\)** のジョブはこのエラー コードでキャンセルされます。 | 残りすべてのデータベースを保護するために、自動保護をもう一度有効にしてください。 |

## <a name="re-registration-failures"></a>再登録エラー

再登録操作をトリガーする前に、次の兆候が 1 つ以上ないか確認してください。

* VM ですべての操作 (バックアップ、復元、バックアップの構成など) が次のいずれかのエラー コードで失敗している。**WorkloadExtensionNotReachable**、**UserErrorWorkloadExtensionNotInstalled**、**WorkloadExtensionNotPresent**、**WorkloadExtensionDidntDequeueMsg**。
* バックアップ項目の **[バックアップの状態]** 領域に **[到達できません]** と表示されている。 同じ状態になる可能性があるその他のすべての原因を除外してください。

  * VM でバックアップ関連の操作を実行する権限がない  
  * VM のシャット ダウン。そのため、バックアップが実行できない
  * ネットワークの問題  

  ![VM の再登録での [到達できません] 状態](./media/backup-azure-sql-database/re-register-vm.png)

* Always On 可用性グループの場合、バックアップ設定を変更した後、またはフェールオーバーの後、バックアップが失敗するようになった。

これらの兆候は、次の 1 つ以上の理由によって発生する可能性があります。

* 拡張機能がポータルから削除またはアンインストールされた。 
* VM の **[コントロール パネル]** の **[プログラムのアンインストールと変更]** で拡張機能がアンインストールされた。
* VM がインプレース ディスク復元を使用して時間内に復元された。
* VM が長期間シャットダウンされたため、その拡張機能構成の期限が切れた。
* VM が削除され、削除された VM と同じ名前で同じリソース グループに別の VM が作成された。
* 可用性グループ ノードのいずれかが、完全なバックアップ構成を受信しなかった。 これは、可用性グループがコンテナーに登録されるか、新しいノードが追加されると発生する場合があります。

前のシナリオにおいて、VM で再登録操作をトリガーすることをお勧めします。 現時点では、このオプションは PowerShell を通じてのみ使用できます。

## <a name="size-limit-for-files"></a>ファイルのサイズ制限

ファイルの文字列の合計サイズは、ファイル数のみでなく、それらの名前とパスによっても決まります。 データベース ファイルごとに、論理ファイル名と物理パスを取得します。 この SQL クエリを使用できます。

```
SELECT mf.name AS LogicalName, Physical_Name AS Location FROM sys.master_files mf
               INNER JOIN sys.databases db ON db.database_id = mf.database_id
               WHERE db.name = N'<Database Name>'"
```

次に、それらを次の形式に調整します。

```
[{"path":"<Location>","logicalName":"<LogicalName>","isDir":false},{"path":"<Location>","logicalName":"<LogicalName>","isDir":false}]}
```

次に例を示します。

```
[{"path":"F:\\Data\\TestDB12.mdf","logicalName":"TestDB12","isDir":false},{"path":"F:\\Log\\TestDB12_log.ldf","logicalName":"TestDB12_log","isDir":false}]}
```

コンテンツの文字列サイズが 20,000 バイトを超えている場合、データベース ファイルは異なる方法で格納されます。 復旧時に、復元のターゲット ファイルのパスを設定できません。 ファイルは、SQL Server によって指定される既定の SQL パスに復元されます。

### <a name="override-the-default-target-restore-file-path"></a>ターゲットの復元ファイルの既定のパスのオーバーライド

復元操作時に、データベース ファイルと、ターゲット復元のパスとのマッピングが含まれる JSON ファイルを配置することで、ターゲット復元ファイルのパスをオーバーライドできます。 `database_name.json` ファイルを作成し、*C:\Program Files\Azure Workload Backup\bin\plugins\SQL* の場所に配置します。

このファイルの内容は、次の形式である必要があります。
```
[
  {
    "Path": "<Restore_Path>",
    "LogicalName": "<LogicalName>",
    "IsDir": "false"
  },
  {
    "Path": "<Restore_Path>",
    "LogicalName": "LogicalName",
    "IsDir": "false"
  },  
]
```

次に例を示します。

```
[
  {
   "Path": "F:\\Data\\testdb2_1546408741449456.mdf",
   "LogicalName": "testdb7",
   "IsDir": "false"
  },
  {
    "Path": "F:\\Log\\testdb2_log_1546408741449456.ldf",
    "LogicalName": "testdb7_log",
    "IsDir": "false"
  },  
]
```

前の内容で、次の SQL クエリを使用してデータベース ファイルの論理名を取得できます。

```
SELECT mf.name AS LogicalName FROM sys.master_files mf
                INNER JOIN sys.databases db ON db.database_id = mf.database_id
                WHERE db.name = N'<Database Name>'"
  ```


このファイルは、復元操作をトリガーする前に配置する必要があります。

## <a name="next-steps"></a>次の手順

SQL Server VM の Azure Backup (パブリック プレビュー) の詳細については、「[SQL VM の Azure Backup](../virtual-machines/windows/sql/virtual-machines-windows-sql-backup-recovery.md#azbackup)」を参照してください。
