---
title: Azure Backup での SQL Server データベースのバックアップのトラブルシューティング | Microsoft Docs
description: Azure VM で実行されている SQL Server データベースの Azure Backup によるバックアップに関するトラブルシューティング情報です。
services: backup
author: anuragm
manager: shivamg
ms.service: backup
ms.topic: article
ms.date: 03/13/2019
ms.author: anuragm
ms.openlocfilehash: db204c0e881200f667484daf4348c336f94a0ce7
ms.sourcegitcommit: f093430589bfc47721b2dc21a0662f8513c77db1
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/04/2019
ms.locfileid: "58916687"
---
# <a name="troubleshoot-back-up-sql-server-on-azure"></a>SQL Server を Azure にバックアップする場合のトラブルシューティング

この記事では、Azure で SQL Server VM を保護する (プレビュー) 場合のトラブルシューティング情報について説明します。

## <a name="feature-consideration-and-limitations"></a>機能の考慮事項と制限事項

機能の考慮事項を確認するには、記事「[Azure VM での SQL Server Backup について](backup-azure-sql-database.md#feature-consideration-and-limitations)」をご覧ください。

## <a name="sql-server-permissions"></a>SQL Server のアクセス許可

仮想マシン上で SQL Server データベースの保護を構成するには、その仮想マシンに **AzureBackupWindowsWorkload** 拡張機能をインストールする必要があります。 **UserErrorSQLNoSysadminMembership** エラーが発生した場合は、必要なバックアップ アクセス許可が SQL インスタンスにないことを意味します。 このエラーを修正するには、[マーケットプレース以外の SQL VM にアクセス許可を設定する](backup-azure-sql-database.md#fix-sql-sysadmin-permissions)手順に従います。

## <a name="troubleshooting-errors"></a>エラーのトラブルシューティング

次の表の情報を参考にして、SQL Server を Azure に保護する際に発生した問題とエラーを解決します。

## <a name="alerts"></a>アラート

### <a name="backup-type-unsupported"></a>バックアップの種類がサポートされていません

| severity | 説明 | 考えられる原因 | 推奨される操作 |
|---|---|---|---|
| 警告 | このデータベースの現在の設定は、関連付けられているポリシーに存在する特定のバックアップ タイプをサポートしていません。 | <li>**Master DB**: マスター データベースに対して実行できるのは、データベースの完全バックアップ操作だけです。**差分**バックアップやトランザクション **ログ** バックアップは実行できません。 </li> <li>**単純復旧モデル**のデータベースは、トランザクション **ログ** バックアップを実行できません。</li> | ポリシー内のバックアップ タイプがすべてサポートされるようにデータベースの設定を変更してください。 または、サポート対象のバックアップ タイプだけを含むように現在のポリシーを変更してもかまいません。 それ以外の場合、スケジュールされたバックアップ中、サポート対象外のバックアップ タイプはスキップされるか、アドホック バックアップのバックアップ ジョブでエラーが発生します。


## <a name="backup-failures"></a>バックアップ エラー

次の表は、エラー コード別にまとめられています。

### <a name="usererrorsqlpodoesnotsupportbackuptype"></a>UserErrorSQLPODoesNotSupportBackupType

| エラー メッセージ | 考えられる原因 | 推奨される操作 |
|---|---|---|
| この SQL データベースは、要求されたバックアップの種類をサポートしていません。 | データベース復旧モデルが要求されたバックアップの種類を許可していない場合に発生します。 このエラーは、以下の状況で発生する可能性があります。 <br/><ul><li>単純復旧モデルを使用するデータベースでは、ログ バックアップが許可されていません。</li><li>マスター データベースでは、差分バックアップとログ バックアップが許可されていません。</li></ul>詳細については、[SQL 復旧モデル](https://docs.microsoft.com/sql/relational-databases/backup-restore/recovery-models-sql-server)に関するドキュメントを参照してください。 | 単純復旧モデルの DB のログ バックアップが失敗した場合は、次のいずれかのオプションを試してください。<ul><li>データベースが単純復旧モードの場合は、ログ バックアップを無効にします。</li><li>データベースの復旧モデルを完全または一括ログに変更するには、[SQL ドキュメント](https://docs.microsoft.com/sql/relational-databases/backup-restore/view-or-change-the-recovery-model-of-a-database-sql-server)を参照してください。 </li><li> 復旧モデルを変更したくない場合で、変更できない複数のデータベースをバックアップする標準ポリシーがある場合は、エラーを無視してください。 完全バックアップと差分バックアップはスケジュールに従って動作します。 ログ バックアップはスキップされますが、この場合は想定どおりの動作です。</li></ul>マスター データベースで、差分バックアップまたはログ バックアップを構成した場合は、次のいずれかの手順を実行します。<ul><li>ポータルを使用して、マスター データベースのバックアップ ポリシー スケジュールを [完全] に変更します。</li><li>変更できない複数のデータベースをバックアップする標準ポリシーがある場合は、エラーを無視してください。 完全バックアップはスケジュールに従って動作します。 差分バックアップまたはログ バックアップは行われませんが、この場合は想定どおりの動作です。</li></ul> |
| 競合する操作が既に同じデータベースに対して実行されているため、操作がキャンセルされました。 | 同時に実行される[バックアップと復元の制限事項に関するブログ エントリ](https://blogs.msdn.microsoft.com/arvindsh/2008/12/30/concurrency-of-full-differential-and-log-backups-on-the-same-database)を参照してください。| [SQL Server Management Studio (SSMS) を使用してバックアップ ジョブを監視します。](manage-monitor-sql-database-backup.md) 競合する操作が失敗したら、操作を再開します。|

### <a name="usererrorsqlpodoesnotexist"></a>UserErrorSQLPODoesNotExist

| エラー メッセージ | 考えられる原因 | 推奨される操作 |
|---|---|---|
| SQL データベースが存在しません。 | データベースは削除されたか、名前が変更されました。 | データベースが誤って削除されたか、名前が変更されたかを確認してください。<br/><br/> 誤ってデータベースを削除した場合、バックアップを続行するために、元の場所にデータベースを復元してください。<br/><br/> データベースを削除し、今後のバックアップが必要ない場合は、Recovery Services のコンテナーの [[データの削除]/[データの保持] で [バックアップの停止] をクリックします](manage-monitor-sql-database-backup.md)。

### <a name="usererrorsqllsnvalidationfailure"></a>UserErrorSQLLSNValidationFailure

| エラー メッセージ | 考えられる原因 | 推奨される操作 |
|---|---|---|
| ログ チェーンが壊れています。 | データベースまたは VM は、ログ チェーンを切り捨てる別のバックアップ ソリューションを使用してバックアップされます。|<ul><li>他のバックアップ ソリューションまたはスクリプトが使用されているかどうかを確認します。 使用されている場合は、他のバックアップ ソリューションを停止します。 </li><li>バックアップがアドホック ログ バックアップの場合は、完全バックアップをトリガーして新しいログ チェーンを開始します。 スケジュールされた定期的なログ バックアップの場合、Azure Backup サービスはこの問題を解決するために、完全バックアップを自動的に開始します。</li>|

### <a name="usererroropeningsqlconnection"></a>UserErrorOpeningSQLConnection

| エラー メッセージ | 考えられる原因 | 推奨される操作 |
|---|---|---|
| Azure Backup は SQL インスタンスに接続できません。 | Azure Backup は SQL インスタンスに接続できません。 | Azure portal のエラー メニューで詳細を追加して、根本原因を絞り込みます。 エラーの修正については、[SQL バックアップのトラブルシューティング](https://docs.microsoft.com/sql/database-engine/configure-windows/troubleshoot-connecting-to-the-sql-server-database-engine)に関するページを参照してください。<br/><ul><li>既定の SQL 設定でリモート接続が許可されていない場合は、設定を変更します。 設定の変更については、次の記事を参照してください。<ul><li>[MSSQLSERVER_-1](/previous-versions/sql/sql-server-2016/bb326495(v=sql.130))</li><li>[MSSQLSERVER_2](/sql/relational-databases/errors-events/mssqlserver-2-database-engine-error)</li><li>[MSSQLSERVER_53](/sql/relational-databases/errors-events/mssqlserver-53-database-engine-error)</li></ul></li></ul><ul><li>ログインに関する問題がある場合は、以下のリンクを参照して修正してください。<ul><li>[MSSQLSERVER_18456](/sql/relational-databases/errors-events/mssqlserver-18456-database-engine-error)</li><li>[MSSQLSERVER_18452](/sql/relational-databases/errors-events/mssqlserver-18452-database-engine-error)</li></ul></li></ul> |

### <a name="usererrorparentfullbackupmissing"></a>UserErrorParentFullBackupMissing

| エラー メッセージ | 考えられる原因 | 推奨される操作 |
|---|---|---|
| このデータ ソースの最初の完全バックアップが見つかりません。 | データベースの完全バックアップが見つかりません。 ログ バックアップと差分バックアップは、完全バックアップの親なので、差分バックアップまたはログ バックアップをトリガーする前に完全バックアップを作成する必要があります。 | アドホック完全バックアップをトリガーします。   |

### <a name="usererrorbackupfailedastransactionlogisfull"></a>UserErrorBackupFailedAsTransactionLogIsFull

| エラー メッセージ | 考えられる原因 | 推奨される操作 |
|---|---|---|
| データ ソースのトランザクション ログがいっぱいなので、バックアップを作成できません。 | データベースのトランザクション ログ領域がいっぱいです。 | この問題を解決するには、[SQL ドキュメント](https://docs.microsoft.com/sql/relational-databases/errors-events/mssqlserver-9002-database-engine-error)を参照してください。 |
| この SQL データベースは、要求されたバックアップの種類をサポートしていません。 | Always On AG セカンダリ レプリカは、完全バックアップと差分バックアップをサポートしていません。 | <ul><li>アドホック バックアップをトリガーした場合、プライマリ ノードでバックアップをトリガーします。</li><li>ポリシーによってバックアップがスケジュールされている場合は、プライマリ ノードが登録されていることを確認します。 ノードを登録するには、[SQL Server データベースの検出手順に従ってください](backup-sql-server-database-azure-vms.md#discover-sql-server-databases)。</li></ul> |

## <a name="restore-failures"></a>復元エラー

復元ジョブに失敗すると、次のエラー コードが表示されます。

### <a name="usererrorcannotrestoreexistingdbwithoutforceoverwrite"></a>UserErrorCannotRestoreExistingDBWithoutForceOverwrite

| エラー メッセージ | 考えられる原因 | 推奨される操作 |
|---|---|---|
| 同じ名前のデータベースが既にターゲットの場所に存在しています | ターゲットの復元先には既に同じ名前のデータベースがあります。  | <ul><li>ターゲット データベース名を変更する</li><li>または、復元ページで強制上書きオプションを使用します</li> |

### <a name="usererrorrestorefaileddatabasecannotbeofflined"></a>UserErrorRestoreFailedDatabaseCannotBeOfflined

| エラー メッセージ | 考えられる原因 | 推奨される操作 |
|---|---|---|
| データベースをオフラインにできないため、復元に失敗しました。 | 復元の実行時は、ターゲット データベースをオフラインにする必要があります。 Azure Backup は、このデータをオフラインにすることができません。 | Azure portal のエラー メニューで詳細を追加して、根本原因を絞り込みます。 詳細については [SQL のドキュメント](https://docs.microsoft.com/sql/relational-databases/backup-restore/restore-a-database-backup-using-ssms)を参照してください。 |

###  <a name="usererrorcannotfindservercertificatewiththumbprint"></a>UserErrorCannotFindServerCertificateWithThumbprint

| エラー メッセージ | 考えられる原因 | 推奨される操作 |
|---|---|---|
| ターゲット上に拇印のあるサーバー証明書が見つかりません。 | 対象のインスタンス上のマスター データベースに有効な暗号化サムプリントがありません。 | ソース インスタンスで使用されている有効な証明書の拇印をターゲット インスタンスにインポートします。 |

### <a name="usererrorrestorenotpossiblebecauselogbackupcontainsbulkloggedchanges"></a>UserErrorRestoreNotPossibleBecauseLogBackupContainsBulkLoggedChanges

| エラー メッセージ | 考えられる原因 | 推奨される操作 |
|---|---|---|
| 復旧に使用されるログ バックアップに一括ログの変更が含まれています。 これを、SQL ガイドラインに従って任意の時点で停止するために使用することはできません。 | データベースが一括ログ復旧モードにある場合は、一括ログ トランザクションと次のログ トランザクションの間のデータを復旧できません。 | 復旧のための別の時点を選択してください。 [詳細情報](https://docs.microsoft.com/previous-versions/sql/sql-server-2008-r2/ms186229(v=sql.105))


## <a name="registration-failures"></a>登録エラー

次のエラー コードは登録失敗のコードです。

### <a name="fabricsvcbackuppreferencecheckfailedusererror"></a>FabricSvcBackupPreferenceCheckFailedUserError

| エラー メッセージ | 考えられる原因 | 推奨される操作 |
|---|---|---|
| 可用性グループの一部のノードが登録されていないため、SQL Always On 可用性グループのバックアップ設定を満たすことができません。 | バックアップを実行するために必要なノードが登録されていないか、到達できません。 | <ul><li>このデータベースのバックアップを実行するために必要なすべてのノードが登録され、正常であることを確認してから、操作を再試行してください。</li><li>SQL Always On 可用性グループのバックアップ設定を変更します。</li></ul> |

### <a name="vmnotinrunningstateusererror"></a>VMNotInRunningStateUserError

| エラー メッセージ | 考えられる原因 | 推奨される操作 |
|---|---|---|
| SQL Server VM はシャットダウンされており、Azure Backup サービスにアクセスできません。 | VM がシャットダウンされています | SQL サーバーが実行されていることを確認します。 |

### <a name="guestagentstatusunavailableusererror"></a>GuestAgentStatusUnavailableUserError

| エラー メッセージ | 考えられる原因 | 推奨される操作 |
|---|---|---|
| Azure Backup サービスは Azure VM ゲスト エージェントを使用してバックアップを行いますが、ターゲット サーバーではゲスト エージェントを使用できません。 | ゲスト エージェントが有効ではないか、正常ではありません | [VM ゲスト エージェントを手動でインストール](../virtual-machines/extensions/agent-windows.md)します。 |

## <a name="configure-backup-failures"></a>バックアップの構成のエラー

次のエラー コードはバックアップの構成のエラーに関するものです。

### <a name="autoprotectioncancelledornotvalid"></a>AutoProtectionCancelledOrNotValid

| エラー メッセージ | 考えられる原因 | 推奨される操作 |
|---|---|---|
| 自動保護の意図が削除されたか、有効でなくなりました。 | SQL インスタンスで自動保護を有効にすると、そのインスタンス内のすべてのデータベースに対して **[Configure Backup]\(バックアップの構成\)** ジョブが実行されます。 ジョブの実行中に自動保護を無効にした場合、**[In-Progress]\(進行中\)** のジョブはこのエラー コードでキャンセルされます。 | 残りすべてのデータベースを保護するには、自動保護をもう一度有効にします。 |

## <a name="re-registration-failures"></a>再登録エラー

再登録操作をトリガーする前に、[こちらの兆候](#symptoms)がないか確認してください。

### <a name="symptoms"></a>現象

* バックアップ、復元、バックアップの構成などすべての操作が失敗するとき、VM で次のいずれかのエラー コードが生成されます。**WorkloadExtensionNotReachable**、**UserErrorWorkloadExtensionNotInstalled**、**WorkloadExtensionNotPresent**、**WorkloadExtensionDidntDequeueMsg**
* バックアップ項目の **[バックアップの状態]** に **[到達不能]** と表示されます。 ただし、同じ状態を引き起こす可能性がある他の理由は除外する必要があります。

  * VM でバックアップ関連の操作を実行する権限がありません。  
  * バックアップを実行できないため、VM がシャットダウンされています。
  * ネットワークの問題  

    ![VM の再登録](./media/backup-azure-sql-database/re-register-vm.png)

* Always On 可用性グループの場合、バックアップ設定を変更した後、またはフェイルオーバーが発生したときに、バックアップが失敗するようになります。

### <a name="causes"></a>原因
これらの現象は、次の 1 つ以上の理由によって発生する可能性があります。

  * 拡張機能がポータルから削除またはアンインストールされました。 
  * VM の **[コントロール パネル]** の **[プログラムのアンインストールと変更]** UI で拡張機能が安易ストールされました。
  * VM がインプレース ディスク復元を使用して時間内に復元されました。
  * VM 上の拡張機能構成の期限が切れたため、VM が長期間にわたってシャットダウンされました。
  * VM が削除され、削除された VM と同じ名前で同じリソース グループに別の VM が作成されました。
  * AG ノードの 1 つが、完全なバックアップ構成を受け取りませんでした。これは可用性グループのコンテナーへの登録時、または新しいノードの追加時に発生する可能性があります。  <br>
   
上のシナリオでは、VM での再登録操作をトリガーすることをお勧めします。 この方法は PowerShell のみで使用できますが、まもなく Azure portal でも使用できるようになります。


## <a name="next-steps"></a>次の手順

SQL Server VM の Azure Backup (パブリック プレビュー) の詳細については、「[SQL VM の Azure Backup (パブリック プレビュー)](../virtual-machines/windows/sql/virtual-machines-windows-sql-backup-recovery.md#azbackup)」を参照してください。
