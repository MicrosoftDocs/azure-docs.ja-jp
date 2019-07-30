---
title: 'Azure Backup: Windows Server へのシステム状態の復元'
description: Azure のバックアップから Windows Server のシステム状態を復元する手順について説明します。
author: saurabhsensharma
manager: shivamg
ms.service: backup
ms.topic: conceptual
ms.date: 8/18/2017
ms.author: saurse
ms.openlocfilehash: 48eb9fce7b9863c84518546270a6566bb0cec711
ms.sourcegitcommit: c72ddb56b5657b2adeb3c4608c3d4c56e3421f2c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/24/2019
ms.locfileid: "68465649"
---
# <a name="restore-system-state-to-windows-server"></a>Windows Server へのシステム状態の復元

この記事では、Azure Recovery Services コンテナーから Windows Server のシステム状態バックアップを復元する方法について説明します。 システム状態を復元するには、システム状態バックアップ ([システム状態のバックアップ](backup-azure-system-state.md#back-up-windows-server-system-state)に関する記事の手順に従って作成) が必要であり、[最新バージョンの Microsoft Azure Recovery Services (MARS) エージェント](https://aka.ms/azurebackup_agent)がインストールされていることを確認する必要があります。 Azure Recovery Services コンテナーからの Windows Server システム状態データの回復は、次の 2 段階のプロセスです。

1. Azure Backup からシステム状態をファイルとして復元します。 Azure Backup からシステム状態をファイルとして復元するときは、次のいずれかを実行できます。
   * バックアップが作成されたサーバーにシステム状態を復元する。または、
   * システム状態ファイルを別のサーバーに復元する。

2. 復元されたシステム状態ファイルを Windows Server に適用します。


## <a name="recover-system-state-files-to-the-same-server"></a>システム状態ファイルを同じサーバーに回復する
次の手順では、Windows Server の構成を以前の状態にロールバックする方法を説明します。 サーバー構成を既知の安定した状態にロールバックすると非常に役立ちます。 次の手順では、Recovery Services コンテナーからサーバーのシステム状態を復元します。

1. **Microsoft Azure Backup** スナップインを開きます。 スナップインがインストールされている場所がわからない場合は、コンピューターまたはサーバーで **Microsoft Azure Backup** を検索します。

    デスクトップ アプリが検索結果に表示されます。

2. **[データの回復]** をクリックして、ウィザードを開始します。

    ![データの回復](./media/backup-azure-restore-windows-server/recover.png)

3. 同じサーバーまたはコンピューターにデータを復元するには、 **[作業の開始]** ウィンドウで、 **[このサーバー] \ (`<server name>`)** を選択し、 **[次へ]** をクリックします。

    ![[このサーバー] オプションを選択すると、データが同じマシンに復元されます](./media/backup-azure-restore-system-state/samemachine.png)

4. **[回復モードの選択]** ウィンドウで、 **[システム状態]** を選択し、 **[次へ]** をクリックします。

    ![Browse files](./media/backup-azure-restore-system-state/recover-type-selection.png)

5. **[ボリュームと日付の選択]** ウィンドウのカレンダーで回復ポイントを選択します。

    任意の時点の回復ポイントから復元できます。 **太字**になっている日付では、少なくとも 1 つの回復ポイントを利用できます。 選択した日付で複数の回復ポイントを利用できる場合は、 **[時間]** ドロップダウン メニューから、特定の回復ポイントを選択します。

    ![Volume and Date](./media/backup-azure-restore-system-state/select-date.png)

6. 復元する回復ポイントを選択したら、 **[次へ]** をクリックします。

    Azure Backup がローカルの回復ポイントをマウントし、回復ボリュームとして使用します。

7. 次のウィンドウでは、システム状態ファイルの回復先を指定します。 **[参照]** をクリックして Windows エクスプローラーを開き、必要なファイルとフォルダーを見つけます。 **[コピーを作成して両方のバージョンを保持する]** を選択すると、システム状態アーカイブ全体のコピーが作成されるのではなく、既存のシステム状態ファイル アーカイブの個々のファイルのコピーが作成されます。

    ![Recovery options](./media/backup-azure-restore-system-state/recover-as-files.png)

8. **[確認]** ウィンドウで回復の詳細を確認し、 **[回復]** をクリックします。

   ![[回復] をクリックして回復操作を承認する](./media/backup-azure-restore-system-state/confirm-recovery.png)

9. 回復先の *WindowsImageBackup* ディレクトリを、サーバーの重要ではないボリュームにコピーします。 通常は、Windows OS ボリュームが重要なボリュームです。

10. 回復が正常に完了したら、「[復元されたシステム状態を Windows Server に適用する](backup-azure-restore-system-state.md)」の手順に従って、システム状態回復プロセスを完了します。

## <a name="recover-system-state-files-to-an-alternate-server"></a>システム状態ファイルを別のサーバーに回復する

Windows Server が破損したり、アクセス不能になったりしたときに、Windows Server のシステム状態を回復して安定した状態に復元する場合は、破損したサーバーのシステム状態を別のサーバーから復元できます。 次の手順に従って、別のサーバーでシステム状態を復元します。  

この手順で使用される用語は次のとおりです。

- *ソース コンピューター* – バックアップが実行され、現在は使用できなくなっている元のコンピューター。
- *ターゲット コンピューター* – データの回復先となるコンピューター。
- "*サンプルのコンテナー*" – "*ソース コンピューター*" と "*ターゲット コンピューター*" が登録されている Recovery Services コンテナー。 <br/>

> [!NOTE]
> コンピューターのバックアップは、以前のバージョンのオペレーティング システムを実行しているコンピューターには復元できません。 たとえば、Windows Server 2016 コンピューターから作成されたバックアップを Windows Server 2012 R2 に復元することはできません。 ただし、逆は可能です。 Windows Server 2012 R2 のバックアップを使用して、Windows Server 2016 を復元できます。
>

1. **ターゲット コンピューター**で *Microsoft Azure Backup* スナップインを開きます。
2. "*ターゲット コンピューター*" および "*ソース コンピューター*" が同じ Recovery Services コンテナーに登録されていることを確認します。
3. **[データの回復]** をクリックして、ワークフローを開始します。
4. **[別のサーバー]**

    ![[別のサーバー]](./media/backup-azure-restore-system-state/anotherserver.png)

5. *サンプルの資格情報コンテナー*に対応するコンテナー資格情報ファイルを指定します。 コンテナー資格情報ファイルが無効である (または有効期限が切れている) 場合、Azure Portal の *サンプルの資格情報コンテナー*から、新しいコンテナー資格情報ファイルをダウンロードします。 コンテナー資格情報ファイルを指定すると、そのコンテナー資格情報ファイルに関連付けられている Recovery Services コンテナーが表示されます。

6. [バックアップ サーバーの選択] ウィンドウで、表示されているコンピューターの一覧から*ソース コンピューター*を選択します。
7. [回復モードの選択] ウィンドウで、 **[システム状態]** を選択し、 **[次へ]** をクリックします。

    ![Search](./media/backup-azure-restore-system-state/recover-type-selection.png)

8. **[ボリュームと日付の選択]** ウィンドウのカレンダーで回復ポイントを選択します。 任意の時点の回復ポイントから復元できます。 **太字**になっている日付では、少なくとも 1 つの回復ポイントを利用できます。 選択した日付で複数の回復ポイントを利用できる場合は、 **[時間]** ドロップダウン メニューから特定の回復ポイントを選択します。

    ![Search items](./media/backup-azure-restore-system-state/select-date.png)

9. 復元する回復ポイントを選択したら、 **[次へ]** をクリックします。

10. **[システム状態の回復モードの選択]** ウィンドウで、システム状態ファイルの回復先を指定し、 **[次へ]** をクリックします。

    ![暗号化](./media/backup-azure-restore-system-state/recover-as-files.png)

    **[コピーを作成して両方のバージョンを保持する]** を選択すると、システム状態アーカイブ全体のコピーが作成されるのではなく、既存のシステム状態ファイル アーカイブの個々のファイルのコピーが作成されます。

11. [確認] ウィンドウで回復の詳細を確認し、 **[回復]** をクリックします。

    ![[回復] をクリックして回復プロセスを確定する](./media/backup-azure-restore-system-state/confirm-recovery.png)

12. *WindowsImageBackup* ディレクトリを、サーバーの重要ではないボリューム (D: など\) にコピーします。 通常は、Windows OS ボリュームが重要なボリュームです。

13. 回復プロセスを完了するには、次のセクションを参照して、[復元されたシステム状態ファイルを Windows Server に適用](#apply-restored-system-state-on-a-windows-server)します。




## <a name="apply-restored-system-state-on-a-windows-server"></a>復元されたシステム状態を Windows Server に適用する

Azure Recovery Services エージェントを使用してシステム状態をファイルとして回復したら、Windows Server バックアップ ユーティリティを使用して、回復したシステム状態を Windows Server に適用します。 Windows Server バックアップ ユーティリティは、サーバーで既に使用可能です。 次の手順では、回復したシステム状態を適用する方法を説明します。

1. 次のコマンドを使用して、"*ディレクトリ サービスの修復モード*" でサーバーを再起動します。 管理者特権でのコマンド プロンプトで、次のように入力します。

    ```
    PS C:\> Bcdedit /set safeboot dsrepair
    PS C:\> Shutdown /r /t 0
    ```

2. 再起動後に、Windows Server バックアップ スナップインを開きます。 スナップインがインストールされている場所がわからない場合は、コンピューターまたはサーバーで **Windows Server バックアップ**を検索します。

    デスクトップ アプリが検索結果に表示されます。

3. スナップインで **[ローカル バックアップ]** を選択します。

    ![復元元として [ローカル バックアップ] を選択する](./media/backup-azure-restore-system-state/win-server-backup-local-backup.png)

4. [ローカル バックアップ] コンソールで、**操作ウィンドウ**の **[回復]** をクリックして回復ウィザードを開きます。

5. **[別の場所に保存されているバックアップ]** を選択し、 **[次へ]** をクリックします。

   ![別のサーバーへの回復を選択する](./media/backup-azure-restore-system-state/backup-stored-in-diff-location.png)

6. 場所の種類を指定します。システム状態バックアップを別のサーバーに回復した場合は、 **[リモート共有フォルダー]** を選択します。 システム状態をローカルに回復した場合は、 **[ローカル ドライブ]** を選択します。

    ![ローカル サーバーと別のサーバーのどちらから回復するかを選択する](./media/backup-azure-restore-system-state/ss-recovery-remote-shared-folder.png)

7. Azure Recovery Services エージェントを使用したシステム状態ファイルの回復の一環として回復された、*WindowsImageBackup* ディレクトリのパスを入力するか、このディレクトリが格納されているローカル ドライブ (例: D:\WindowsImageBackup) を選択し、 **[次へ]** をクリックします。

    ![共有ファイルのパス](./media/backup-azure-restore-system-state/ss-recovery-remote-folder.png)

8. 復元するシステム状態のバージョンを選択し、 **[次へ]** をクリックします。

9. [回復の種類の選択] ウィンドウで、 **[システム状態]** を選択し、 **[次へ]** をクリックします。

10. システム状態の回復の場所として、 **[元の場所]** を選択し、 **[次へ]** をクリックします。

11. [確認] で詳細を確認し、再起動設定を確認します。 **[回復]** をクリックして、復元されたシステム状態ファイルを適用します。

    ![システム状態ファイルの復元を開始する](./media/backup-azure-restore-system-state/launch-ss-recovery.png)

## <a name="special-considerations-for-system-state-recovery-on-active-directory-server"></a>Active Directory サーバーでのシステム状態の回復に関する特別な考慮事項

システム状態バックアップには、Active Directory のデータが含まれています。 Active Directory Domain Services (AD DS) を現在の状態から以前の状態に復元するには、次の手順に従います。

1. ディレクトリ サービス復元モード (DSRM) でドメイン コントローラーを再起動します。
2. [ここ](https://technet.microsoft.com/library/cc794755(v=ws.10).aspx)での手順に従い、Windows Server バックアップ コマンドレットを使用して、AD DS を回復します。


## <a name="troubleshoot-failed-system-state-restore"></a>システム状態の復元に失敗した場合のトラブルシューティング

前述のシステム状態の適用プロセスが正常に完了しなかった場合は、Windows 回復環境 (Win RE) を使用して Windows Server を回復します。 次の手順では、Win RE を使用して回復する方法を説明します。 このオプションを使用するのは、システム状態の復元後に Windows Server が正常に起動しない場合だけです。 次のプロセスでは、システム データ以外のデータが消去されるので注意してください。

1. Windows Server を Windows 回復環境 (Win RE) で起動します。

2. 3 つの使用可能なオプションの中から [トラブルシューティング] を選択します。

    ![初期メニュー](./media/backup-azure-restore-system-state/winre-1.png)

3. **[詳細オプション]** 画面で **[コマンド プロンプト]** を選択し、サーバー管理者のユーザー名とパスワードを入力します。

   ![初期メニュー](./media/backup-azure-restore-system-state/winre-2.png)

4. サーバー管理者のユーザー名とパスワードを入力します。

    ![初期メニュー](./media/backup-azure-restore-system-state/winre-3.png)

5. コマンド プロンプトを管理者モードで開いたら、次のコマンドを実行して、システム状態バックアップのバージョンを取得します。

    ```
    Wbadmin get versions -backuptarget:<Volume where WindowsImageBackup folder is copied>:
    ```
    ![システム状態バックアップのバージョンを取得する](./media/backup-azure-restore-system-state/winre-4.png)

6. 次のコマンドを実行して、バックアップの使用可能なすべてのボリュームを取得します。

    ```
    Wbadmin get items -version:<copy version from above step> -backuptarget:<Backup volume>
    ```

    ![システム状態バックアップのバージョンを取得する](./media/backup-azure-restore-system-state/winre-5.png)

7. 次のコマンドは、システム状態バックアップに含まれているすべてのボリュームを回復します。 この手順では、システム状態に含まれている重要なボリュームだけが回復されることに注意してください。 システム データ以外のデータはすべて消去されます。

    ```
    Wbadmin start recovery -items:C: -itemtype:Volume -version:<Backupversion> -backuptarget:<backup target volume>
    ```
     ![システム状態バックアップのバージョンを取得する](./media/backup-azure-restore-system-state/winre-6.png)



## <a name="next-steps"></a>次の手順
* ファイルとフォルダーを回復したので、 [バックアップを管理](backup-azure-manage-windows-server.md)できます。
