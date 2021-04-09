---
title: チュートリアル - Windows Server を Azure にバックアップする
description: このチュートリアルでは、オンプレミスの Windows Server を Recovery Services コンテナーにバックアップする方法について説明します。
ms.topic: tutorial
ms.date: 08/22/2018
ms.custom: mvc
ms.openlocfilehash: d2990b5950cf8812367c3a59c6cace39e4085e2a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "88261907"
---
# <a name="back-up-windows-server-to-azure"></a>Windows Server を Azure にバックアップする

Azure Backup を利用し、破損、攻撃、災害から Windows Server を守ることができます。 Azure Backup では、Microsoft Azure Recovery Services (MARS) エージェントという軽量のツールが使用されます。 この MARS エージェントが Windows Server にインストールされ、Windows Server システム状態を介し、ファイル、フォルダー、サーバー構成情報が保護されます。 このチュートリアルでは、MARS エージェントを利用し、Azure に Windows Server をバックアップする方法について説明します。 このチュートリアルで学習する内容は次のとおりです。

> [!div class="checklist"]
>
> * MARS エージェントをダウンロードし、設定する
> * サーバー バックアップの時間とリテンション期間スケジュールを設定する
> * オンデマンド バックアップを実行する

## <a name="sign-in-to-azure"></a>Azure へのサインイン

Azure Portal ( <https://portal.azure.com> ) にサインインします。

## <a name="create-a-recovery-services-vault"></a>Recovery Services コンテナーを作成する

Windows Server をバックアップするには、まず、保存するバックアップの場所 (復元ポイント) を作成する必要があります。 [Recovery Services コンテナー](backup-azure-recovery-services-vault-overview.md)は Azure にあるコンテナーで、そこに Windows Server からのバックアップを保存します。 以下の手順で Azure Portal に Recovery Services コンテナーを作成します。

1. 左側のメニューで **[すべてのサービス]** を選択し、サービスの一覧に **「Recovery Services」** と入力します。 **[Recovery Services コンテナー]** を選択します。

   ![Open Recovery Services vault](./media/tutorial-backup-windows-server-to-azure/full-browser-open-rs-vault_2.png)

2. **[Recovery Services コンテナー]** メニューの **[追加]** を選択します。

   ![コンテナーの情報を指定する](./media/tutorial-backup-windows-server-to-azure/provide-vault-detail-2.png)

3. **[Recovery Services コンテナー]** メニューで、

    * **[名前]** に「*myRecoveryServicesVault*」と入力します。
    * 現在のサブスクリプション ID が **[サブスクリプション]** に表示されます。
    * **[リソース グループ]** で **[既存のものを使用]** を選択し、 *[myResourceGroup]* を選択します。 *[myResourceGroup]* が存在しない場合は、 **[新規作成]** を選択し、「*myResourceGroup*」と入力します。
    * **[場所]** ドロップダウン メニューから *[西ヨーロッパ]* を選択します。
    * **[作成]** を選択し、Recovery Services コンテナーを作成します。

コンテナーが作成されると、Recovery Services コンテナーの一覧に表示されます。

## <a name="download-recovery-services-agent"></a>Recovery Services エージェントをダウンロードする

Microsoft Azure Recovery Services (MARS) エージェントは、Windows Server と Recovery Services コンテナーを関連付けます。 以下は、サーバーにエージェントをダウンロードする方法です。

1. Recovery Services コンテナーの一覧から **[myRecoveryServicesVault]** を選択し、そのダッシュボードを開きます。

   ![コンテナーを選択してダッシュボードを開く](./media/tutorial-backup-windows-server-to-azure/open-vault-from-list.png)

2. コンテナー ダッシュボード メニューで **[バックアップ]** を選択します。

3. **[バックアップの目標]** メニューで:

   * **[ワークロードはどこで実行されていますか?]** で **[オンプレミス]** を選択します。
   * **[何をバックアップしますか?]** に **[ファイルとフォルダー]** と **[システム状態]** を選択します。

   ![[バックアップの目標] メニュー](./media/tutorial-backup-windows-server-to-azure/backup-goal.png)

4. **[インフラストラクチャの準備]** を選択し、 **[インフラストラクチャの準備]** メニューを開きます。

5. **[インフラストラクチャの準備]** メニューで、 **[Windows Server または Windows クライアント用エージェントのダウンロード]** を選択し、*MARSAgentInstaller.exe* をダウンロードします。

    ![Windows Server または Windows クライアント用エージェントのダウンロード](./media/tutorial-backup-windows-server-to-azure/prepare-infrastructure.png)

    インストーラーが別のブラウザーで開き、**MARSAgentInstaller.exe** をダウンロードします。

6. ダウンロードしたファイルを実行する前に、[インフラストラクチャの準備] メニューの **[ダウンロード]** を選択し、**コンテナー資格情報** ファイルを保存します。 コンテナー資格情報は、MARS エージェントと Recovery Services コンテナーを結び付けるために必要です。

    ![コンテナー資格情報のダウンロード](./media/tutorial-backup-windows-server-to-azure/download-vault-credentials.png)

## <a name="install-and-register-the-agent"></a>エージェントをインストールして登録する

1. ダウンロードした **MARSagentinstaller.exe** を見つけ、ダブルクリックします。
2. **[Microsoft Azure Recovery Services エージェント セットアップ ウィザード]** が表示されます。 ウィザードを進めながら次の情報を入力し、 **[登録]** を選択します。
   * インストールとキャッシュ フォルダーの場所。
   * プロキシ サーバーを使用してインターネットに接続する場合、プロキシ サーバーの情報。
   * 認証済みのプロキシを使用する場合、ユーザー名とパスワードの詳細。

     ![Azure Recovery Services Agent セットアップ ウィザード](./media/tutorial-backup-windows-server-to-azure/mars-installer.png)

3. ウィザードの終わりで、 **[登録処理を続行]** を選択し、前の手順でダウンロードした **コンテナー資格情報** ファイルを指定します。

4. 入力が求められたら、暗号化パスフレーズを指定し、Windows Server からのバックアップを暗号化します。 安全な場所にパスフレーズを保存します。パスフレーズを失った場合、Microsoft では回復することができません。

5. **[完了]** を選択します。

## <a name="configure-backup-and-retention"></a>バックアップとリテンション期間を設定する

Microsoft Azure Recovery Services エージェントを利用し、Azure へのバックアップを Windows Server で行うスケジュールを設定します。 エージェントをダウンロードしたサーバーで次の手順を実行します。

1. Microsoft Azure Recovery Services エージェントを開きます。 エージェントは、コンピューターで **Microsoft Azure Backup** を検索すると見つかります。

2. Recovery Services エージェント コンソールで、 **[操作]** ペインにある **[バックアップのスケジュール]** を選択します。

    ![Schedule Backup](./media/tutorial-backup-windows-server-to-azure/mars-schedule-backup.png)

3. **[次へ]** を選択し、 **[Select Items to Back up]\(バックアップする項目の選択\)** ページに移動します。

4. **[項目の追加]** を選択し、表示されたダイアログ ボックスで **[システム状態]** とバックアップするファイルまたはフォルダーを選択します。 **[OK]** をクリックします。

5. **[次へ]** を選択します。

6. **[バックアップ スケジュールの選択 (システム状態)]** ページで、システム状態に対してバックアップを開始する時刻や曜日を指定し、 **[次へ]** を選択します。

7. **[保持ポリシーの選択 (システム状態)]** ページで、システム状態を対象に、バックアップ コピーの保持ポリシーを選択し、 **[次へ]** を選択します。

8. 同様に、選択したファイルとフォルダーに対して、バックアップ スケジュールと保持ポリシーを選択します。

9. **[Choose Initial Back up Type]\(初回バックアップの種類\)** ページで、 **[自動でネットワーク経由]** を選択し、 **[次へ]** を選択します。

10. **[確認]** ページで情報を確認し、 **[完了]** を選択します。

11. ウィザードでバックアップ スケジュールの作成が完了したら、 **[閉じる]** を選択します。

## <a name="perform-an-on-demand-backup"></a>オンデマンド バックアップを実行する

バックアップ ジョブを実行するスケジュールが作成されました。 ただし、サーバーはまだバックアップされていません。 ディザスター リカバリーのベスト プラクティスとして、オンデマンドのバックアップを実行し、サーバーのデータ回復性を確認することが挙げられます。

1. Microsoft Azure Recovery Services エージェント コンソールで **[今すぐバックアップ]** を選択します。

    ![今すぐバックアップ](./media/tutorial-backup-windows-server-to-azure/backup-now.png)

2. **[今すぐバックアップ]** ウィザードで、 **[ファイルとフォルダー]** または **[システム状態]** からバックアップ対象を選択し、 **[次へ]** を選択します
3. **[確認]** ページで、 **[今すぐバックアップ]** ウィザードのサーバー バックアップ設定を確認します。 次に、 **[バックアップ]** を選択します。
4. **[閉じる]** を選択してウィザードを閉じます。 バックアップ プロセスが完了する前にウィザードを閉じても、ウィザードはバックグラウンドで引き続き実行されます。
5. 初回バックアップが完了すると、 **[ジョブは完了しました]** 状態が MARS エージェント コンソールの **[ジョブ]** ウィンドウに表示されます。

## <a name="next-steps"></a>次のステップ

このチュートリアルでは、Azure portal を使用して以下を行いました。

> [!div class="checklist"]
>
> * Recovery Services コンテナーを作成する
> * Microsoft Azure Recovery Services エージェントをダウンロードする
> * エージェントをインストールする
> * Windows Server のバックアップを構成する
> * オンデマンド バックアップを実行する

次のチュートリアルに進み、Azure から Windows Server にファイルを復元してください。

> [!div class="nextstepaction"]
> [Azure から Windows Server にファイルを復元する](./tutorial-backup-restore-files-windows-server.md)
