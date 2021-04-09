---
title: Azure Backup エージェントのトラブルシューティング
description: この記事では、Azure Backup エージェントのインストールと登録のトラブルシューティング方法について説明します。
ms.topic: troubleshooting
ms.date: 07/15/2019
ms.openlocfilehash: 3203d5604f1bd5db9cf579af01b2ae6f34032d89
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/30/2021
ms.locfileid: "103467614"
---
# <a name="troubleshoot-the-microsoft-azure-recovery-services-mars-agent"></a>Microsoft Azure Recovery Services (MARS) エージェントをトラブルシューティングする

この記事では、構成、登録、バックアップ、および復元中に発生する可能性があるエラーの解決方法を示します。

## <a name="basic-troubleshooting"></a>基本的なトラブルシューティング

Microsoft Azure Recovery Services (MARS) のトラブルシューティングを開始する前に、以下を確認することをお勧めします。

- [MARS エージェントが最新であることを確認します](https://go.microsoft.com/fwlink/?linkid=229525&clcid=0x409)。
- [MARS エージェントと Azure の間にネットワーク接続が存在することを確認します](#the-microsoft-azure-recovery-service-agent-was-unable-to-connect-to-microsoft-azure-backup)。
- MARS が (サービス コンソールで) 実行されていることを確認します。 必要な場合は、再起動して操作をやり直します。
- [スクラッチ フォルダーの場所に 5% から 10% の空きボリューム領域があることを確認します](./backup-azure-file-folder-backup-faq.md#whats-the-minimum-size-requirement-for-the-cache-folder)。
- [別のプロセスまたはウイルス対策ソフトウェアによって Azure Backup が妨げられているかどうかを確認します](./backup-azure-troubleshoot-slow-backup-performance-issue.md#cause-another-process-or-antivirus-software-interfering-with-azure-backup)。
- バックアップ ジョブが警告付きで完了した場合は、「[バックアップ ジョブが警告付きで完了した](#backup-jobs-completed-with-warning)」を参照してください
- スケジュールされたバックアップが失敗したが、手動バックアップは機能する場合は、「[バックアップがスケジュールに従って実行されない](#backups-dont-run-according-to-schedule)」を参照してください。
- OS に最新の更新プログラムが適用されていることを確認します。
- [サポートされていない属性を持つサポートされていないドライブとファイルはバックアップから除外されることを確認します](backup-support-matrix-mars-agent.md#supported-drives-or-volumes-for-backup)。
- 保護されているシステム上のクロックが適切なタイム ゾーンに構成されていることを確認します。
- [サーバーに .NET Framework 4.5.2 以降がインストールされていることを確認します](https://www.microsoft.com/download/details.aspx?id=30653)。
- コンテナーにサーバーを再登録する場合は、次のことを行います。
  - エージェントがサーバーからアンインストールされていることと、ポータルから削除されていることを確認します。
  - 最初にサーバーの登録に使用したのと同じパスフレーズを使用します。
- オフライン バックアップの場合は、ソース コンピューターとコピー用コンピューターの両方に Azure PowerShell 3.7.0 がインストールされていることを確認します。
- Azure 仮想マシンで Backup エージェントが実行されている場合は、[こちらの記事](./backup-azure-troubleshoot-slow-backup-performance-issue.md#cause-backup-agent-running-on-an-azure-virtual-machine)を参照してください。

## <a name="invalid-vault-credentials-provided"></a>無効なコンテナーの資格情報が指定されました

**エラー メッセージ**:無効なコンテナーの資格情報が指定されました。 ファイルが破損しているか、最新の資格情報が回復サービスと関連付けられていません。 (ID: 34513)

| 原因 | 推奨アクション |
| ---     | ---    |
| **コンテナーの資格情報が有効ではありません** <br/> <br/> コンテナー資格情報ファイルが、壊れている、期限が切れている、または *.vaultCredentials* と異なるファイル拡張子を持っている可能性があります。 (たとえば、登録の時刻より 10 日間以上前にダウンロードされている可能性があります。)| Azure portal で Recovery Services コンテナーから[新しい資格情報をダウンロード](backup-azure-file-folder-backup-faq.md#where-can-i-download-the-vault-credentials-file)します。 その後、必要に応じて次の手順に従います。 <ul><li> MARS を既にインストールして登録している場合は、Microsoft Azure Backup エージェント MMC コンソールを開きます。 次に、 **[アクション]** ウィンドウで **[サーバーの登録]** を選択して、新しい資格情報での登録を完了します。 <br/> <li> 新規インストールに失敗した場合は、新しい資格情報で再度インストールしてみてください。</ul> **注**: 複数のコンテナー資格情報ファイルがダウンロードされている場合、次の 10 日間の間は最新のファイルのみが有効になります。 新しいコンテナー資格情報ファイルをダウンロードすることをお勧めします。
| **プロキシ サーバー/ファイアウォールによって登録がブロックされています** <br/>or <br/>**インターネットに接続されていません** <br/><br/> マシンまたはプロキシ サーバーでインターネット接続が制限されていて、必要な URL へのアクセスが許可されていない場合、登録は失敗します。| 次の手順を実行します。<br/> <ul><li> IT チームと連携して、システムでインターネットに接続できることを確認します。<li> プロキシ サーバーがない場合は、エージェントを登録するときにプロキシのオプションが選択されていないことを確認します。 [プロキシ設定を確認します](#verifying-proxy-settings-for-windows)。<li> ファイアウォール/プロキシ サーバーがある場合は、ネットワーク チームと連携して、以下の URL と IP アドレスにアクセスできることを確認します。<br/> <br> **URL**<br> `www.msftncsi.com` <br> .Microsoft.com <br> .WindowsAzure.com <br> .microsoftonline.com <br> .windows.net <br>`www.msftconnecttest.com`<br><br>**IP アドレス**<br>  20.190.128.0/18 <br>  40.126.0.0/18<br> <br/></ul></ul>上記のトラブルシューティングの手順が完了したら、もう一度登録してみてください。<br></br> Azure ExpressRoute 経由で接続している場合は、「[Azure ExpressRoute のサポート](backup-support-matrix-mars-agent.md#azure-expressroute-support)」の説明に従って設定が構成されていることを確認してください。
| **ウイルス対策ソフトウェアによって登録をブロックされています** | サーバーにウイルス対策ソフトウェアがインストールされている場合は、以下のファイルとフォルダーのウイルス対策スキャンに必要な除外ルールを追加します。 <br/><ul> <li> CBengine.exe <li> CSC.exe<li> スクラッチ フォルダー。 この既定の場所は C:\Program Files\Microsoft Azure Recovery Services Agent\Scratch です。 <li> C:\Program Files\Microsoft Azure Recovery Services Agent\Bin にある bin フォルダー。

### <a name="additional-recommendations"></a>その他の推奨事項

- C:/Windows/Temp に移動し、拡張子が .tmp のファイルが 60,000 または 65,000 個より多くあるかどうかを確認します。 ある場合は、それらのファイルを削除します。
- マシンの日付と時刻がローカルのタイム ゾーンに一致していることを確認します。
- [これらのサイト](install-mars-agent.md#verify-internet-access)が Internet Explorer の信頼済みサイトに追加されていることを確認します。

### <a name="verifying-proxy-settings-for-windows"></a>Windows のプロキシ設定を確認する

1. [[Sysinternals]](/sysinternals/downloads/psexec) ページから PsExec をダウンロードします。
1. 管理者特権でのコマンド プロンプトから、`psexec -i -s "c:\Program Files\Internet Explorer\iexplore.exe"` を実行します。

   このコマンドで Internet Explorer が開きます。
1. **[ツール]**  >  **[インターネット オプション]**  >  **[接続]**  >  **[LAN の設定]** の順に移動します。
1. システム アカウントのプロキシ設定を確認します。
1. プロキシが構成されていてプロキシの詳細が提供されている場合は、詳細を削除します。
1. プロキシが構成されていてプロキシの詳細が正しくない場合は、**プロキシの IP** と **ポート** の詳細が正しいことを確認します。
1. Internet Explorer を閉じます。

## <a name="unable-to-download-vault-credential-file"></a>コンテナーの資格情報ファイルをダウンロードできない

| エラー   | 推奨アクション |
| ---     | ---    |
|コンテナー資格情報ファイルをダウンロードできませんでした。 (ID: 403) | <ul><li> 別のブラウザーを使用してコンテナー資格情報のダウンロードを試みるか、次の手順を実行します。 <ul><li> Internet Explorer を起動します。 F12 キーを押します。 </li><li> **[ネットワーク]** タブに移動して、キャッシュと Cookie をクリアします。 </li> <li> ページを更新します。<br></li></ul> <li> サブスクリプションが無効/期限切れかどうかを確認します。<br></li> <li> 何らかのファイアウォール規則によってダウンロードがブロックされているかどうかを確認します。 <br></li> <li> コンテナーでの制限に達していないことを確認します (コンテナーあたり 50 マシン)。<br></li>  <li> コンテナー資格情報をダウンロードしてサーバーをコンテナーに登録するために必要な Azure Backup のアクセス許可をユーザーが持っていることを確認します。 「[Azure ロール ベースのアクセス制御を使用した Azure Backup の回復ポイントの管理](backup-rbac-rs-vault.md)」を参照してください。</li></ul> |

## <a name="the-microsoft-azure-recovery-service-agent-was-unable-to-connect-to-microsoft-azure-backup"></a>Microsoft Azure Recovery Services エージェントは Microsoft Azure Backup に接続できませんでした

| エラー  | 考えられる原因 | 推奨アクション |
| ---     | ---     | ---    |
| <br /><ul><li>Microsoft Azure Recovery Services エージェントは Microsoft Azure Backup に接続できませんでした。 (ID: 100050) ネットワーク設定を調べて、インターネットに接続できることを確認してください。<li>(407) プロキシの認証が必要です。 |プロキシによって接続がブロックされています。 |  <ul><li>Internet Explorer で、 **[ツール]**  >  **[インターネット オプション]**  >  **[セキュリティ]**  >  **[インターネット]** の順に移動します。 次に、 **[レベルのカスタマイズ]** を選択し、 **[ファイルのダウンロード]** セクションまで下にスクロールします。 **[有効化]** を選択します。<p>また、Internet Explorer で信頼済みサイトに [URL と IP アドレス](install-mars-agent.md#verify-internet-access)を追加する必要がある場合もあります。<li>プロキシ サーバーを使用するように設定を変更します。 その後、プロキシ サーバーの詳細を指定します。<li> マシンのインターネットへのアクセスが制限されている場合は、マシンまたはプロキシのファイアウォール設定によって次の [URL と IP アドレス](install-mars-agent.md#verify-internet-access)が許可されることを確認します。 <li>サーバーにウイルス対策ソフトウェアがインストールされている場合は、これらのファイルをウイルス対策スキャンから除外します。 <ul><li>CBEngine.exe (dpmra.exe ではありません)。<li>CSC.exe (.NET Framework に関連するもの)。 CSC.exe は、サーバーにインストールされているすべての .NET Framework のバージョンに対して存在します。 影響を受けるサーバー上のすべてのバージョンの .NET Framework 用の CSC.exe ファイルを除外してください。 <li>スクラッチ フォルダーまたはキャッシュの場所。 <br>スクラッチ フォルダーまたはキャッシュのパスの既定の場所は、C:\Program Files\Microsoft Azure Recovery Services Agent\Scratch です。<li>C:\Program Files\Microsoft Azure Recovery Services Agent\Bin にある bin フォルダー。

## <a name="the-specified-vault-credential-file-cannot-be-used-as-it-is-not-downloaded-from-the-vault-associated-with-this-server"></a>指定された資格情報コンテナーの資格情報ファイルは、このサーバーに関連付けられている資格情報コンテナーからダウンロードされていないため、使用できません

| エラー  | 考えられる原因 | 推奨アクション |
| ---     | ---     | ---    |
| 指定された資格情報コンテナーの資格情報ファイルは、このサーバーに関連付けられている資格情報コンテナーからダウンロードされていないため、使用できません。 (ID: 100110) 適切な資格情報コンテナーの資格情報を指定してください。 | このコンテナーの資格情報ファイルは、このサーバーに既に登録されているものとは別のコンテナーのものです。 | ターゲット コンピューターとソース コンピューターが同じ Recovery Services コンテナーに登録されていることを確認します。 ターゲット サーバーが既に別のコンテナーに登録されている場合は、 **[サーバーの登録]** オプションを使用して適切なコンテナーに登録します。  

## <a name="backup-jobs-completed-with-warning"></a>バックアップ ジョブが警告付きで完了した

- バックアップ中に MARS エージェントによってファイルとフォルダーが反復処理されると、さまざまな状況が発生し、バックアップが "完了 (警告あり)" とマークされることがあります。 このような状況では、ジョブは "完了 (警告あり)" と表示されます。 これは問題ありませんが、少なくとも 1 つのファイルをバックアップできなかったことを意味しています。 そのため、ジョブではそのファイルがスキップされ、データ ソースにある問題の他のすべてのファイルがバックアップされています。

  ![バックアップ ジョブが警告ありで完了した](./media/backup-azure-mars-troubleshoot/backup-completed-with-warning.png)

- バックアップでファイルがスキップされる可能性がある条件は次のとおりです。
  - サポートされていないファイル属性 (例: OneDrive フォルダー内、圧縮ストリーム、再解析ポイント)。 完全な一覧については、[サポート マトリックス](./backup-support-matrix-mars-agent.md#supported-file-types-for-backup)を参照してください。
  - ファイル システムの問題
  - 別のプロセスとの干渉 (たとえば、ウイルス対策ソフトウェアがファイルへのハンドルを保持していると、MARS エージェントがファイルにアクセスできなくなることがあります)
  - ファイルがアプリケーションによってロックされている  

- バックアップ サービスでは、次の名前付け規則に従って、これらのファイルは失敗としてログ ファイルにマークされます。*C:\Program Files\Microsoft Azure Recovery Service Agent\temp* フォルダー下の *LastBackupFailedFilesxxxx.txt*。
- この問題を解決するには、ログ ファイルを確認して、問題の性質を理解します。

  | エラー コード             | 理由                                             | 推奨事項                                              |
  | ---------------------- | --------------------------------------------------- | ------------------------------------------------------------ |
  | 0x80070570             | ファイルまたはディレクトリが壊れているため、読み取ることができません。 | ソース ボリュームで **chkdsk** を実行します。                             |
  | 0x80070002、0x80070003 | 指定されたファイルが見つかりません。         | [スクラッチ フォルダーがいっぱいになっていないことを確認します](./backup-azure-file-folder-backup-faq.md#manage-the-backup-cache-folder)  <br><br>  スクラッチ領域が構成されているボリュームが存在するかどうか (削除されていないかどうか) を確認します  <br><br>   [マシンにインストールされているウイルス対策から MARS エージェントが除外されていることを確認します](./backup-azure-troubleshoot-slow-backup-performance-issue.md#cause-another-process-or-antivirus-software-interfering-with-azure-backup)  |
  | 0x80070005             | アクセスが拒否されました                                    | [ウイルス対策ソフトまたはその他のサードパーティ製ソフトウェアによってアクセスがブロックされているかどうかを確認します](./backup-azure-troubleshoot-slow-backup-performance-issue.md#cause-another-process-or-antivirus-software-interfering-with-azure-backup)     |
  | 0x8007018b             | クラウド ファイルへのアクセスが拒否されました。                | OneDrive ファイル、Git ファイル、またはコンピューター上でオフライン状態になる可能性のあるその他のファイル |

- [[既存のポリシーに除外ルールを追加する]](./backup-azure-manage-mars.md#add-exclusion-rules-to-existing-policy) を使用して、バックアップを正常に実行できるよう、サポートされていないファイル、不明なファイル、または削除されたファイルをバックアップ ポリシーから除外できます。

- 最上位フォルダーで保護されたフォルダーを削除して同じ名前で再作成することは避けてください。 この操作を行うと、「*重大な不整合が検出されたため、変更をレプリケートできません*」という警告とともにバックアップが完了する可能性があります。  フォルダーを削除して再作成する必要がある場合は、保護されている最上位のフォルダーの下のサブ フォルダーで行うことを検討してください。

## <a name="failed-to-set-the-encryption-key-for-secure-backups"></a>セキュリティで保護されたバックアップ用に暗号化キーを設定できませんでした

| エラー | 考えられる原因 | 推奨アクション |
| ---     | ---     | ---    |
| <br />セキュリティで保護されたバックアップ用に暗号化キーを設定できませんでした。 ライセンス認証は完全には成功しませんでしたが、暗号化パスフレーズが次のファイルに保存されました。 |<li>サーバーは既に別のコンテナーに登録されています。<li>構成時に、パスフレーズが破損しました。| コンテナーからサーバーの登録を解除した後、新しいパスフレーズを使ってもう一度登録します。

## <a name="the-activation-did-not-complete-successfully"></a>ライセンス認証が正常に完了しませんでした

| エラー  | 考えられる原因 | 推奨アクション |
|---------|---------|---------|
|<br />ライセンス認証は正常に完了しませんでした。 サービスの内部エラー [0x1FC07] が発生したため、現在の操作を実行できませんでした。 しばらくしてから操作を再試行してください。 問題が解決しない場合は、Microsoft サポートにお問い合わせください。     | <li> 十分な領域のないボリュームにスクラッチ フォルダーがあります。 <li> スクラッチ フォルダーが誤って移動されました。 <li> OnlineBackup.KEK ファイルが見つかりません。         | <li>[最新バージョン](https://aka.ms/azurebackup_agent)の MARS エージェントにアップグレードしてください。<li>バックアップ データの合計サイズの 5% ～ 10% の空き領域があるボリュームに、スクラッチ フォルダーまたはキャッシュの場所を移動します。 キャッシュの場所を正しく移動する方法については、「[ファイルとフォルダーのバックアップに関する一般的な質問](./backup-azure-file-folder-backup-faq.md#manage-the-backup-cache-folder)」の手順を参照してください。<li> OnlineBackup.KEK ファイルが存在することを確認します。 <br>*スクラッチ フォルダーまたはキャッシュのパスの既定の場所は、C:\Program Files\Microsoft Azure Recovery Services Agent\Scratch です*。        |

## <a name="encryption-passphrase-not-correctly-configured"></a>暗号化のパスフレーズが正しく構成されていません

| エラー  | 考えられる原因 | 推奨アクション |
|---------|---------|---------|
| <br />エラー 34506。 このコンピューター用に保存されている暗号化のパスフレーズは、正しく構成されていません。    | <li> 十分な領域のないボリュームにスクラッチ フォルダーがあります。 <li> スクラッチ フォルダーが誤って移動されました。 <li> OnlineBackup.KEK ファイルが見つかりません。        | <li>[最新バージョン](https://aka.ms/azurebackup_agent)の MARS エージェントにアップグレードしてください。<li>バックアップ データの合計サイズの 5% ～ 10% の空き領域があるボリュームに、スクラッチ フォルダーまたはキャッシュの場所を移動します。 キャッシュの場所を正しく移動する方法については、「[ファイルとフォルダーのバックアップに関する一般的な質問](./backup-azure-file-folder-backup-faq.md#manage-the-backup-cache-folder)」の手順を参照してください。<li> OnlineBackup.KEK ファイルが存在することを確認します。 <br>*スクラッチ フォルダーまたはキャッシュのパスの既定の場所は、C:\Program Files\Microsoft Azure Recovery Services Agent\Scratch です*。         |

## <a name="backups-dont-run-according-to-schedule"></a>バックアップがスケジュールに従って実行されません

スケジュールされたバックアップが自動的にトリガーされないが、手動によるバックアップが正しく動作している場合は、以下の操作を試してください。

- Windows Server のバックアップ スケジュールが、Azure のファイルとフォルダーのバックアップ スケジュールと競合していないことを確認します。

- オンライン バックアップ状態が **[有効]** に設定されていることを確認します。 状態を確認するには、次の手順を実行します。

  1. タスク スケジューラで **[Microsoft]** を展開し、 **[オンライン バックアップ]** を選択します。
  1. **[Microsoft-OnlineBackup]** をダブルクリックし、 **[トリガー]** タブに移動します。
  1. 状態が **[有効]** に設定されていることを確認します。 設定されていない場合は、 **[編集]** を選択し、 **[有効]** を選択してから、 **[OK]** を選択します。

- タスク実行のために選択したユーザー アカウントが **SYSTEM**、またはサーバー上の **ローカルの Administrators グループ** であることを確認します。 ユーザー アカウントを確認するには、 **[全般]** タブに移動し、 **[セキュリティ]** オプションを確認します。

- PowerShell 3.0 以降がサーバーにインストールされていることを確認します。 PowerShell のバージョンを確認するには、次のコマンドを実行し、`Major` のバージョン番号が 3 以上であることを確認します。

  `$PSVersionTable.PSVersion`

- `PSMODULEPATH` 環境変数に次のパスが含まれていることを確認します。

  `<MARS agent installation path>\Microsoft Azure Recovery Services Agent\bin\Modules\MSOnlineBackup`

- `LocalMachine` の PowerShell 実行ポリシーが `restricted` に設定されている場合は、バックアップ タスクをトリガーする PowerShell コマンドレットが失敗することがあります。 次のコマンドを管理者特権モードで実行して、実行ポリシーを確認します。次に、実行ポリシーを `Unrestricted` または `RemoteSigned` に設定します。

 ```PowerShell
 Get-ExecutionPolicy -List

Set-ExecutionPolicy Unrestricted
 ```

- PowerShell モジュール MSonlineBackup ファイルに不足や破損がないことを確認します。 見つからないファイルや破損したファイルがある場合は、次の手順を実行します。

  1. MARS エージェントが適切に動作しているマシンの C:\Program Files\Microsoft Azure Recovery Services Agent\bin\Modules から、MSOnlineBackup フォルダーをコピーします。
  1. このコピーしたファイルを、問題のあるマシンの同じフォルダーの場所 (C:\Program Files\Microsoft Azure Recovery Services Agent\bin\Modules) に貼り付けます。

     マシン上に既に MSOnlineBackup フォルダーがある場合は、その中にファイルを貼り付けるか、既存のファイルを置き換えます。

> [!TIP]
> 変更を確実に適用するために、上記の手順を実行した後で、サーバーを再起動します。

## <a name="resource-not-provisioned-in-service-stamp"></a>サービス スタンプにプロビジョニングされていないリソース

エラー | 考えられる原因 | 推奨アクション
--- | --- | ---
現在の操作は、内部サービス エラー "サービス スタンプにプロビジョニングされていないリソース" が原因で失敗しました。 しばらくしてから、操作を再試行してください。 (ID: 230006) | 保護されたサーバーの名前が変更されました。 | <li> サーバーの名前を、コンテナーに登録されている元の名前に戻します。 <br> <li> 新しい名前を使用して、サーバーをコンテナーに再登録します。

## <a name="job-could-not-be-started-as-another-job-was-in-progress"></a>別のジョブが進行中であったため、ジョブを開始できませんでした

**MARS コンソール** >  **[ジョブ履歴]** に "別のジョブが進行中であったため、ジョブを開始できませんでした" という警告メッセージが表示された場合は、タスク スケジューラによってトリガーされたジョブのインスタンスが重複している可能性があります。

![別のジョブが進行中であったため、ジョブを開始できませんでした](./media/backup-azure-mars-troubleshoot/job-could-not-be-started.png)

この問題を解決するには、次の手順を実行します。

1. [ファイル名を指定して実行] ウィンドウで *taskschd.msc* と入力して、タスク スケジューラ スナップインを起動します。
1. 左側のペインで、 **[タスク スケジューラ ライブラリ]**  ->  **[Microsoft]**  ->  **[OnlineBackup]** に移動します。
1. このライブラリ内の各タスクについて、タスクをダブルクリックしてプロパティを開き、次の手順を実行します。
    1. **[設定]** タブに切り替えます。

         ![Settings tab](./media/backup-azure-mars-troubleshoot/settings-tab.png)

    1. **[タスクが既に実行中の場合に適用される規則]** のオプションを選択します。 **[新しいインスタンスを開始しない]** を選択します。

         ![新しいインスタンスを開始しないように規則を変更する](./media/backup-azure-mars-troubleshoot/change-rule.png)

## <a name="troubleshoot-restore-problems"></a>復元の問題のトラブルシューティング

Azure Backup が、数分たっても回復ボリュームに正常にマウントできないことがあります。 また、処理中にエラー メッセージを受け取ることもあります。 正常に回復を開始するには、次の手順を実行します。

1. マウント プロセスが数分間実行されている場合は取り消します。

2. 最新バージョンの Backup エージェントがあるかどうかを確認します。 バージョンを確認するには、MARS コンソールの **[アクション]** ウィンドウで、 **[About Microsoft Azure Recovery Services Agent]\(Microsoft Azure Recovery Services エージェントについて\)** を選択します。 **バージョン** 番号が、[この記事](https://go.microsoft.com/fwlink/?linkid=229525)に記載されているバージョン以上であることを確認します。 このリンクを選択して[最新バージョンをダウンロード](https://go.microsoft.com/fwLink/?LinkID=288905)します。

3. **[デバイス マネージャー]**  >  **[ストレージ コントローラー]** の順に移動し、**Microsoft iSCSI イニシエーター** を探します。 見つかった場合は、手順 7 に進みます。

4. Microsoft iSCSI イニシエーター サービスが見つからない場合は、 **[デバイス マネージャー]**  >  **[記憶域コントローラー]** の下で、 **[不明なデバイス]** という名前でハードウェア ID が **ROOT\ISCSIPRT** のエントリを探します。

5. **[不明なデバイス]** を右クリックし、 **[ドライバー ソフトウェアの更新]** を選択します。

6. **[自動的に更新されたドライバ ソフトウェアを検索します]** を選択して、ドライバーを更新します。 この更新により、 **[不明なデバイス]** が **[Microsoft iSCSI イニシエーター]** に変わります。

    ![[記憶域コントローラー] が強調表示されている Azure Backup デバイス マネージャーのスクリーンショット](./media/backup-azure-restore-windows-server/UnknowniSCSIDevice.png)

7. **[タスク マネージャー]**  >  **[サービス (ローカル)]**  >  **[Microsoft iSCSI イニシエーター サービス]** に移動します。

    ![[サービス (ローカル)] が強調表示されている Azure Backup タスク マネージャーのスクリーンショット](./media/backup-azure-restore-windows-server/MicrosoftInitiatorServiceRunning.png)

8. Microsoft iSCSI イニシエーター サービスを再開します。 そのためには、サービスを右クリックして、 **[停止]** を選択します。 次に、もう一度右クリックして、 **[開始]** を選択します。

9. [インスタント リストア](backup-instant-restore-capability.md)を使用して回復を再試行します。

それでも回復が失敗する場合は、サーバーまたはクライアントを再起動します。 再起動したくない場合、またはサーバーを再起動しても回復が失敗する場合は、[別のマシンから回復](backup-azure-restore-windows-server.md#use-instant-restore-to-restore-data-to-an-alternate-machine)してみてください。

## <a name="troubleshoot-cache-problems"></a>キャッシュに関する問題のトラブルシューティング

キャッシュ フォルダー (スクラッチ フォルダーとも呼ばれます) が正しく構成されていないか、前提条件を満たしていないか、アクセスが制限されている場合、バックアップ操作が失敗することがあります。

### <a name="prerequisites"></a>前提条件

MARS エージェントの操作を成功させるには、キャッシュ フォルダーが以下の要件に従う必要があります。

- [スクラッチ フォルダーの場所に 5% から 10% の空きボリューム領域があることを確認します](backup-azure-file-folder-backup-faq.md#whats-the-minimum-size-requirement-for-the-cache-folder)
- [スクラッチ フォルダーの場所が有効かつアクセス可能であることを確認します](backup-azure-file-folder-backup-faq.md#how-to-check-if-scratch-folder-is-valid-and-accessible)
- [キャッシュ フォルダーのファイル属性がサポートされていることを確認します](backup-azure-file-folder-backup-faq.md#are-there-any-attributes-of-the-cache-folder-that-arent-supported)
- [割り当てられたシャドウ コピーの記憶域がバックアップ プロセスに十分であることを確認します](#increase-shadow-copy-storage)
- [キャッシュ フォルダーへのアクセスを制限する他のプロセス (ウイルス対策ソフトウェアなど) がないことを確認します](#another-process-or-antivirus-software-blocking-access-to-cache-folder)

### <a name="increase-shadow-copy-storage"></a>シャドウ コピーの記憶域を増やす

データ ソースを保護するために必要なシャドウ コピーの記憶域が不足している場合、バックアップ操作が失敗することがあります。 この問題を解決するには、次に示すように **vssadmin** を使用して、保護されたボリューム上のシャドウ コピーの記憶域を増やします。

- 管理者特権でのコマンド プロンプトから、現在のシャドウ記憶域を確認します。<br/>
  `vssadmin List ShadowStorage /For=[Volume letter]:`
- 次のコマンドを使用してシャドウ記憶域を増やします。<br/>
  `vssadmin Resize ShadowStorage /On=[Volume letter]: /For=[Volume letter]: /Maxsize=[size]`

### <a name="another-process-or-antivirus-software-blocking-access-to-cache-folder"></a>キャッシュ フォルダーへのアクセスをブロックしている別のプロセスまたはウイルス対策ソフトウェア

サーバーにウイルス対策ソフトウェアがインストールされている場合は、以下のファイルとフォルダーのウイルス対策スキャンに必要な除外ルールを追加します。  

- スクラッチ フォルダー。 既定の場所は `C:\Program Files\Microsoft Azure Recovery Services Agent\Scratch` です
- `C:\Program Files\Microsoft Azure Recovery Services Agent\Bin` の bin フォルダー
- CBengine.exe
- CSC.exe

## <a name="common-issues"></a>一般的な問題

ここでは、MARS エージェントの使用中に発生する一般的なエラーについて説明します。

### <a name="salchecksumstoreinitializationfailed"></a>SalChecksumStoreInitializationFailed

エラー メッセージ | 推奨される操作
--|--
Microsoft Azure Recovery Services Agent was unable to access backup checksum stored in scratch location (Microsoft Azure Recovery Services Agent は、スクラッチ場所に格納されているバックアップ チェックサムにアクセスできませんでした) | この問題を解決するには、次の手順を行ってサーバーを再起動します <br/> - [スクラッチ場所ファイルをロックしているウイルス対策またはその他のプロセスがあるかどうか確認します](#another-process-or-antivirus-software-blocking-access-to-cache-folder)<br/> - [スクラッチ場所が有効で、MARS エージェントにアクセスできるかどうか確認します。](backup-azure-file-folder-backup-faq.md#how-to-check-if-scratch-folder-is-valid-and-accessible)

### <a name="salvhdinitializationerror"></a>SalVhdInitializationError

エラー メッセージ | 推奨される操作
--|--
Microsoft Azure Recovery Services Agent was unable to access the scratch location to initialize VHD (Microsoft Azure Recovery Services Agent は、スクラッチ場所にアクセスして VHD を初期化できませんでした) | この問題を解決するには、次の手順を行ってサーバーを再起動します <br/> - [スクラッチ場所ファイルをロックしているウイルス対策またはその他のプロセスがあるかどうか確認します](#another-process-or-antivirus-software-blocking-access-to-cache-folder)<br/> - [スクラッチ場所が有効で、MARS エージェントにアクセスできるかどうか確認します。](backup-azure-file-folder-backup-faq.md#how-to-check-if-scratch-folder-is-valid-and-accessible)

### <a name="sallowdiskspace"></a>SalLowDiskSpace

エラー メッセージ | 推奨される操作
--|--
Backup failed due to insufficient storage in volume  where the scratch folder is located (スクラッチ フォルダーがあるボリュームの記憶域不足のためバックアップに失敗しました) | この問題を解決するには、次の手順を確認し、操作を再試行してください。<br/>- [MARS エージェントが最新であることを確認します](https://go.microsoft.com/fwlink/?linkid=229525&clcid=0x409)<br/> - [バックアップ スクラッチ領域に影響を及ぼすストレージの問題を確認および解決します](#prerequisites)

### <a name="salbitmaperror"></a>SalBitmapError

エラー メッセージ | 推奨される操作
--|--
ファイル内の変更を見つけることができない。 これにはさまざまな理由が考えられます。 操作をやり直してください | この問題を解決するには、次の手順を確認し、操作を再試行してください。<br/> - [MARS エージェントが最新であることを確認します](https://go.microsoft.com/fwlink/?linkid=229525&clcid=0x409) <br/> - [バックアップ スクラッチ領域に影響を及ぼすストレージの問題を確認および解決します](#prerequisites)

## <a name="next-steps"></a>次のステップ

- [Azure Backup エージェントでの Windows Server のバックアップ方法](tutorial-backup-windows-server-to-azure.md)についての詳しい情報を見ます。
- バックアップを復元する必要がある場合は、[Windows コンピューターへのファイルの復元](backup-azure-restore-windows-server.md)に関する記事を参照してください。
