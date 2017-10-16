---
title: "Windows のシステム状態を Azure にバックアップする | Microsoft Docs"
description: "Windows Server コンピューターまたは Windows コンピューターのシステム状態を Azure にバックアップする方法を紹介します。"
services: backup
documentationcenter: 
author: saurabhsensharma
manager: carmonm
editor: 
keywords: "バックアップ方法; バックアップ方法; ファイルとフォルダーのバックアップ"
ms.assetid: 5b15ebf1-2214-4722-b937-96e2be8872bb
ms.service: backup
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/31/2017
ms.author: saurse;markgal
ms.openlocfilehash: 6fbd96935f444d8b0c6d068ebd0d28e612f19816
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="back-up-windows-system-state-in-resource-manager-deployment"></a>Windows のシステム状態を Resource Manager デプロイメントにバックアップする
この記事では、Windows Server のシステム状態を Azure にバックアップする方法について説明します。 基本事項に関するチュートリアルです。

Azure Backup の詳細については、こちらの [概要記事](backup-introduction-to-azure-backup.md)を参照してください。

Azure サブスクリプションがない場合は、すべての Azure サービスにアクセスできる [無料アカウント](https://azure.microsoft.com/free/) を作成します。

## <a name="create-a-recovery-services-vault"></a>Recovery Services コンテナーを作成する
ファイルとフォルダーをバックアップするには、データを保存するリージョンに Recovery Services コンテナーを作成する必要があります。 また、ストレージのレプリケート方法を決定する必要もあります。

### <a name="to-create-a-recovery-services-vault"></a>Recovery Services コンテナーを作成するには
1. まだサインインしていない場合は、Azure サブスクリプションを使用して [Azure Portal](https://portal.azure.com/) にサインインします。
2. ハブ メニューの **[その他のサービス]** をクリックし、リソースの一覧で「**Recovery Services**」と入力して、**[Recovery Services コンテナー]** をクリックします。

    ![Create Recovery Services Vault step 1](./media/backup-try-azure-backup-in-10-mins/open-rs-vault-list.png) <br/>

    サブスクリプションに Recovery Services コンテナーがある場合は、そのコンテナーが一覧表示されます。
3. **[Recovery Services コンテナー]** メニューの **[追加]** をクリックします。

    ![Create Recovery Services Vault step 2](./media/backup-try-azure-backup-in-10-mins/rs-vault-menu.png)

    [Recovery Services コンテナー] ブレードが開き、**[名前]**、**[サブスクリプション]**、**[リソース グループ]**、**[場所]** を指定するよう求められます。

    ![Recovery Services コンテナーの作成手順 3](./media/backup-try-azure-backup-in-10-mins/rs-vault-step-3.png)

4. **[名前]**ボックスに、コンテナーを識別する表示名を入力します。 名前は Azure サブスクリプションに対して一意である必要があります。 2 ～ 50 文字の名前を入力します。 名前の先頭にはアルファベットを使用する必要があります。また、名前に使用できるのはアルファベット、数字、ハイフンのみです。

5. **[サブスクリプション]** セクションで、ドロップダウン メニューを使用して Azure サブスクリプションを選択します。 サブスクリプションが 1 つのみの場合は、そのサブスクリプションが表示されるので、次の手順に進んでください。 どのサブスクリプションを使用すればよいかがわからない場合は、既定 (または推奨) のサブスクリプションを使用してください。 組織のアカウントが複数の Azure サブスクリプションに関連付けられている場合に限り、複数の選択肢が存在します。

6. **[リソース グループ]** セクションで、次のことを行います。

    * リソース グループを作成する場合は、**[新規作成]** を選択します。
    または
    * 使用可能なリソース グループの一覧を表示するには、**[既存のものを使用]** を選択し、ドロップダウン メニューをクリックします。

  リソース グループの詳細については、「[Azure Resource Manager の概要](../azure-resource-manager/resource-group-overview.md)」をご覧ください。

7. **[場所]** をクリックして、コンテナーの地理的リージョンを選択します。 この選択により、バックアップ データの送信先となるリージョンが決まります。

8. [Recovery Services コンテナー] ブレードの下部にある **[作成]** をクリックします。

    Recovery Services コンテナーの作成には数分かかることがあります。 ポータルの右上の領域で、状態の通知を監視します。 コンテナーが作成されると、Recovery Services コンテナーの一覧に表示されます。 数分経過してもコンテナーが表示されない場合は、**[最新の情報に更新]** をクリックしてください。

    ![[最新の情報に更新] ボタンをクリックする](./media/backup-try-azure-backup-in-10-mins/refresh-button.png)</br>

    Recovery Services コンテナーの一覧にコンテナーが表示されたら、ストレージ冗長性を設定する準備が整いました。

### <a name="set-storage-redundancy-for-the-vault"></a>コンテナーのストレージ冗長性を設定する
Recovery Services コンテナーを作成する際は、必要に応じてストレージの冗長性が構成されるようにしてください。

1. **[Recovery Services コンテナー]** ブレードで、新しいコンテナーをクリックします。

    ![Recovery Services コンテナーの一覧から新しいコンテナーを選択する](./media/backup-try-azure-backup-in-10-mins/rs-vault-list.png)

    コンテナーを選択すると、**[Recovery Services コンテナー]** ブレードが縮小され、"*上部にコンテナー名が表示された*" 設定ブレードとコンテナーの詳細ブレードが開きます。

    ![新しいコンテナーのストレージ構成を表示する](./media/backup-try-azure-backup-in-10-mins/set-storage-configuration-2.png)
2. 新しいコンテナーの設定ブレードで、垂直スライドを使って下へスクロールして [管理] セクションに移動し、**[バックアップ インフラストラクチャ]** をクリックします。
    [バックアップ インフラストラクチャ] ブレードが開きます。
3. [バックアップ インフラストラクチャ] ブレードで、**[バックアップ構成]** をクリックして **[バックアップ構成]** ブレードを開きます。

    ![新しいコンテナーのストレージ構成を設定する](./media/backup-try-azure-backup-in-10-mins/set-storage-configuration.png)
4. コンテナーの適切なストレージ レプリケーション オプションを選択します。

    ![ストレージ構成の選択](./media/backup-try-azure-backup-in-10-mins/choose-storage-configuration.png)

    既定では、コンテナーには geo 冗長ストレージがあります。 プライマリ バックアップ ストレージ エンドポイントとして Azure を使用する場合は、引き続き **[geo 冗長]** を使用します。 プライマリ バックアップ ストレージ エンドポイントとして Azure を使用しない場合、**[ローカル冗長]** を選択します。これにより、Azure Storage のコストを削減できます。 [geo 冗長](../storage/common/storage-redundancy.md#geo-redundant-storage)ストレージと[ローカル冗長](../storage/common/storage-redundancy.md#locally-redundant-storage)ストレージのオプションの詳細については、こちらの[ストレージ冗長性の概要](../storage/common/storage-redundancy.md)を参照してください。

コンテナーを作成したら、Windows のシステム状態をバックアップするための構成を行います。

## <a name="configure-the-vault"></a>コンテナーの構成
1. Recovery Services コンテナー (先ほど作成したコンテナー) のブレードの [作業の開始] セクションで **[バックアップ]** をクリックし、**[バックアップ作業の開始]** ブレードで、**[バックアップの目標]** を選択します。

    ![Open backup goal blade](./media/backup-try-azure-backup-in-10-mins/open-backup-settings.png)

    **[バックアップの目標]** ブレードが開きます。

    ![Open backup goal blade](./media/backup-try-azure-backup-in-10-mins/backup-goal-blade.png)

2. **[ワークロードはどこで実行されていますか?]** ボックスの一覧の **[オンプレミス]** を選択します。

    Windows Server または Windows コンピューターが Azure にない物理マシンであるため、**[オンプレミス]** を選択します。

3. **[何をバックアップしますか?]** メニューの **[システム状態]** を選択し、**[OK]** をクリックします。

    ![ファイルとフォルダーの構成](./media/backup-azure-system-state/backup-goal-system-state.png)

    [OK] をクリックすると、**[バックアップの目標]** の横にチェックマークが表示され、**[インフラストラクチャの準備]** ブレードが開きます。

    ![バックアップの目標の構成完了、次はインフラストラクチャの準備](./media/backup-try-azure-backup-in-10-mins/backup-goal-configed.png)

4. **[インフラストラクチャの準備]** ブレードで、**[Windows Server または Windows クライアント用エージェントのダウンロード]** をクリックします。

    ![[Download Agent for Windows Server or Windows Client]](./media/backup-try-azure-backup-in-10-mins/choose-agent-for-server-client.png)

    Windows Server Essentials を使用している場合は、Windows Server Essentials 用エージェントのダウンロードを選択します。 MARSAgentInstaller.exe を実行するか、保存するかをたずねるポップアップ メニューが表示されます。

    ![MARSAgentInstaller ダイアログ](./media/backup-try-azure-backup-in-10-mins/mars-installer-run-save.png)

5. ダウンロードのポップアップ メニューで、**[保存]** をクリックします。

    既定では、 **MARSagentinstaller.exe** ファイルがダウンロード フォルダーに保存されます。 インストーラーのダウンロードが完了すると、インストーラーを実行するかフォルダーを開くかをたずねるポップアップが表示されます。

    ![[Download Agent for Windows Server or Windows Client]](./media/backup-try-azure-backup-in-10-mins/mars-installer-complete.png)

    まだ、エージェントをインストールする必要はありません。 エージェントはコンテナー資格情報をダウンロードした後にインストールできます。

6. **[インフラストラクチャの準備]** ブレードで、**[ダウンロード]** をクリックします。

    ![コンテナー資格情報のダウンロード](./media/backup-try-azure-backup-in-10-mins/download-vault-credentials.png)

    コンテナー資格情報は、ダウンロード フォルダーにダウンロードされます。 コンテナー資格情報のダウンロードが完了すると、資格情報を開くか保存するかをたずねるポップアップが表示されます。 **[保存]**をクリックします。 誤って **[開く]** をクリックすると、コンテナー資格情報を開こうとして失敗します。 コンテナー資格情報を開くことはできません。 次の手順に進みます。 コンテナー資格情報はダウンロード フォルダーにあります。   

    ![コンテナー資格情報のダウンロード完了](./media/backup-try-azure-backup-in-10-mins/vault-credentials-downloaded.png)

## <a name="install-and-register-the-agent"></a>エージェントをインストールして登録する

> [!NOTE]
> Azure Portal を使用してバックアップを有効にすることはまだできません。 Microsoft Azure Recovery Services エージェントを使用して、Windows Server のシステム状態をバックアップしてください。
>

1. ダウンロード フォルダー (または他の保存場所) で **MARSagentinstaller.exe** を見つけて、ダブルクリックします。

    インストーラーは一連のメッセージを表示しながら、Recovery Services エージェントの抽出、インストール、登録を実行します。

    ![Recovery Services エージェントのインストーラーの実行](./media/backup-try-azure-backup-in-10-mins/mars-installer-registration.png)

2. Microsoft Azure Recovery Services エージェント セットアップ ウィザードを実行します。 ウィザードでは以下のことを行う必要があります。

   * インストールとキャッシュ フォルダーの場所を選択します。
   * プロキシ サーバーを使用してインターネットに接続する場合は、プロキシ サーバーの情報を指定します。
   * 認証済みのプロキシを使用する場合は、ユーザー名とパスワードの詳細を入力します。
   * ダウンロードしたコンテナーの資格情報を指定します。
   * 暗号化パスフレーズを安全な場所に保存します。

     > [!NOTE]
     > パスフレーズを紛失または忘れた場合、Microsoft はバックアップ データの回復を支援することはできません。 安全な場所にファイルを保存してください。 バックアップを復元するために必要です。
     >
     >

エージェントがインストールされ、コンピューターがコンテナーに登録されました。 バックアップを構成してスケジュールする準備ができました。

## <a name="back-up-windows-server-system-state-preview"></a>Windows Server のシステム状態のバックアップ (プレビュー)
初回バックアップには、次の 3 つのタスクが含まれています。

* Azure Backup エージェントを使用したシステム状態バックアップの有効化
* バックアップのスケジュール
* 初回のファイルとフォルダーのバックアップ

初回バックアップを実行するには、Microsoft Azure Recovery Services エージェントを使用します。

### <a name="to-enable-system-state-backup-using-the-azure-backup-agent"></a>Azure Backup エージェントを使用したシステム状態バックアップを有効にするには

1. PowerShell セッションで、次のコマンドを実行して Azure Backup エンジンを停止します。

  ```
  PS C:\> Net stop obengine
  ```

2. Windows レジストリを開きます。

  ```
  PS C:\> regedit.exe
  ```

3. 指定の DWord 値と共に次のレジストリ キーを追加します。

  | レジストリ パス | レジストリ キー | DWord 値 |
  |---------------|--------------|-------------|
  | HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows Azure Backup\Config\CloudBackupProvider | TurnOffSSBFeature | 2 |

4. 管理者特権でのコマンド プロンプトで次のコマンドを実行してバックアップ エンジンを再起動します。

  ```
  PS C:\> Net start obengine
  ```

### <a name="to-schedule-the-backup-job"></a>バックアップ ジョブのスケジュールを設定するには

1. Microsoft Azure Recovery Services エージェントを開きます。 エージェントは、コンピューターで **Microsoft Azure Backup**を検索すると見つかります。

    ![Launch the Azure Recovery Services agent](./media/backup-try-azure-backup-in-10-mins/snap-in-search.png)

2. Recovery Services エージェントで、 **[バックアップのスケジュール]**をクリックします。

    ![Windows Server のバックアップのスケジュール](./media/backup-try-azure-backup-in-10-mins/schedule-first-backup.png)

3. バックアップのスケジュール ウィザードの [作業の開始] ページで、 **[次へ]**をクリックします。

4. [バックアップする項目の選択] 画面で、 **[項目の追加]**をクリックします。

5. **[システム状態]** を選択し、**[OK]** をクリックします。

6. **[次へ]** をクリックします。

7. システム状態バックアップと保持スケジュールは、毎週日曜日の午後 9 時 (現地時刻) にバックアップするよう自動的に設定され、保持期間は 60 日に設定されます。

   > [!NOTE]
   > システム状態のバックアップ スケジュールと保持ポリシーは自動的に構成されます。 Windows Server のシステム状態に加えてファイルとフォルダーをバックアップする場合は、ファイル バックアップについてのみ、ウィザードからバックアップ スケジュールと保持ポリシーを指定してください。 
   >

8. [確認] ページで情報を確認し、 **[完了]**をクリックします。

9. ウィザードでバックアップ スケジュールの作成が完了したら、 **[閉じる]**をクリックします。

### <a name="to-back-up-windows-server-system-state-for-the-first-time"></a>初めて Windows Server のシステム状態をバックアップするには

1. Windows Server に、再起動を伴う保留中の更新が存在しないことを確認します。

2. Recovery Services エージェントで **[今すぐバックアップ]** をクリックして、ネットワーク経由での最初のシード処理を完了します。

    ![Windows Server を今すぐバックアップする](./media/backup-try-azure-backup-in-10-mins/backup-now.png)

3. [確認] ページで、今すぐバックアップ ウィザードによってコンピューターのバックアップに使用される設定を確認します。 次に、 **[バックアップ]**をクリックします。

4. **[閉じる]** をクリックしてウィザードを閉じます。 バックアップ プロセスが完了する前にウィザードを閉じても、ウィザードはバックグラウンドで引き続き実行されます。

5. Windows Server のシステム状態に加えてサーバー上のファイルとフォルダーをバックアップする場合、"今すぐバックアップ" ウィザードによってバックアップされるのはファイルだけです。 システム状態のアドホック バックアップを実行するには、次の PowerShell コマンドを使用します。

    ```
    PS C:\> Start-OBSystemStateBackup
    ```

  初回バックアップが完了すると、 **[ジョブは完了しました]** 状態が Backup コンソールに表示されます。

  ![IR complete](./media/backup-try-azure-backup-in-10-mins/ircomplete.png)

## <a name="frequently-asked-questions"></a>よく寄せられる質問

次の質問と回答は、補足情報としてお読みください。

### <a name="what-is-the-staging-volume"></a>ステージング ボリュームとは何ですか。

ステージング ボリュームとは、ネイティブの Windows Server バックアップによってシステム状態バックアップが段階的に実行される中間的な場所を表します。 この中間バックアップは、その後 Azure Backup エージェントによって圧縮、暗号化され、セキュリティで保護された HTTPS プロトコルを介して、構成済みの Recovery Services コンテナーに送信されます。 **ステージング ボリュームは Windows OS 以外のボリュームに構築するよう強くお勧めします。システム状態バックアップに問題が見つかった場合、そのトラブルシューティング作業でまず行うことは、ステージング ボリュームの場所を確認することです。** 

### <a name="how-can-i-change-the-staging-volume-path-specified-in-the-azure-backup-agent"></a>Azure Backup エージェントに指定されているステージング ボリュームのパスを変更するにはどうすればよいでしょうか。

既定では、ステージング ボリュームがキャッシュ フォルダーに置かれます。 

1. この場所を変更するには、管理者特権でのコマンド プロンプトで次のコマンドを実行します。
  ```
  PS C:\> Net stop obengine
  ```

2. 次のレジストリ エントリを、新しいステージング ボリューム フォルダーのパスで更新します。

  |レジストリ パス|レジストリ キー|値|
  |-------------|------------|-----|
  |HKEY_LOCAL_MACHINE\Software\Microsoft\Windows Azure Backup\Config\CloudBackupProvider | SSBStagingPath | 新しいステージング ボリュームの場所 |

ステージング パスは、大文字と小文字が区別されます。サーバーに実在するパスを大文字と小文字の違いを含めて忠実に指定してください。 

3. ステージング ボリュームのパスを変更したら、バックアップ エンジンを再起動します。
  ```
  PS C:\> Net start obengine
  ```
4. 変更後のパスを使用するには、Microsoft Azure Recovery Services エージェントを開き、システム状態のアドホック バックアップをトリガーします。

### <a name="why-is-the-system-state-default-retention-set-to-60-days"></a>システム状態の既定の保持期間が 60 日に設定されているのはなぜでしょうか。

システム状態バックアップの耐用年数は、Windows Server Active Directory ロールの "廃棄 (tombstone) の有効期間" 設定と同じです。 "廃棄 (tombstone) の有効期間" 設定の既定値は 60 日です。 この値は、ディレクトリ サービス (NTDS) 構成オブジェクトで設定できます。

### <a name="how-do-i-change-the-default-backup-and-retention-policy-for-system-state"></a>システム状態に使用する既定のバックアップ スケジュールと保持ポリシーを変更するにはどうすればよいでしょうか。

システム状態に使用する既定のバックアップ スケジュールと保持ポリシーを変更するには、次の手順を実行します。
1. バックアップ エンジンを停止します。 管理者特権でのコマンド プロンプトで、次のコマンドを実行します。

  ```
  PS C:\> Net stop obengine
  ```

2. HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows Azure Backup\Config\CloudBackupProvider で次のレジストリ キー エントリを追加または更新します。

  |レジストリ名|Description|値|
  |-------------|-----------|-----|
  |SSBScheduleTime|バックアップの時刻を構成します。 既定値は、現地時刻の午後 9 時です。|DWord: HHMM 形式 (10 進数)。たとえば、現地時刻の午後 9 時 30 分なら「2130」と指定します。|
  |SSBScheduleDays|何曜日の決まった時刻にシステム状態バックアップを実行するかを構成します。 週の曜日を 1 桁の数字で指定します。 たとえば 0 は日曜日を、1 は月曜日を表します。 既定では日曜日にバックアップが実行されます。|DWord: バックアップを実行する曜日 (10 進数)。たとえば、「1230」を指定した場合、月曜日、火曜日、水曜日、日曜日にバックアップがスケジュールされます。|
  |SSBRetentionDays|バックアップを保持する日数を構成します。 既定値は 60 です。 指定できる最大値は 180 です。|DWord: バックアップを保持する日数 (10 進数)。|

3. 次のコマンドを使用して、バックアップ エンジンを再起動します。
    ```
    PS C:\> Net start obengine
    ```

4. Microsoft Recovery Services エージェントを開きます。

5. **[バックアップのスケジュール]** をクリックし、変更が反映されていることが確認できるまで **[次へ]** をクリックします。

6. **[完了]** をクリックして変更を適用します。


## <a name="questions"></a>疑問がある場合
ご不明な点がある場合や今後搭載を希望する機能がある場合は、 [フィードバックをお送りください](http://aka.ms/azurebackup_feedback)。

## <a name="next-steps"></a>次のステップ
* [Windows コンピューターのバックアップ](backup-configure-vault.md)の詳細を参照してください。
* ファイルとフォルダーをバックアップしたので、 [コンテナーとサーバーを管理](backup-azure-manage-windows-server.md)できます。
* バックアップを復元する必要がある場合は、 [Windows コンピューターへのファイルの復元](backup-azure-restore-windows-server.md)に関する記事を参照してください。
