---
title: Microsoft Azure Recovery Services コンテナーを削除する
description: この記事では、依存関係を削除してから Azure Backup Recovery Services コンテナーを削除する方法について説明します。
ms.topic: conceptual
ms.date: 06/04/2020
ms.openlocfilehash: c0b75d147abba45a745f811de5e4b8ac45088bd8
ms.sourcegitcommit: ac7ae29773faaa6b1f7836868565517cd48561b2
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/25/2020
ms.locfileid: "88826737"
---
# <a name="delete-an-azure-backup-recovery-services-vault"></a>Azure Backup Recovery Services コンテナーを削除する

この記事では、[Azure Backup](backup-overview.md) Recovery Services コンテナーを削除する方法について説明します。 依存関係を削除してからコンテナーの削除を行うための手順が含まれています。

## <a name="before-you-start"></a>開始する前に

次の依存関係いずれかを含む Recovery Services コンテナーは削除できません。

- 保護されたデータ ソース (IaaS VM、SQL データベース、Azure ファイル共有) が含まれるコンテナーを削除することはできません。
- バックアップ データが含まれるコンテナーを削除することはできません。 バックアップ データが削除されると、論理的に削除された状態になります。
- 論理的に削除された状態のバックアップ データが含まれるコンテナーを削除することはできません。
- 登録済みのストレージ アカウントがあるコンテナーを削除することはできません。

依存関係を削除せずにコンテナーを削除しようとすると、次のいずれかのエラー メッセージが表示されます。

- 内部にリソースが存在するため、資格情報コンテナーを削除できません。 バックアップ項目、保護されたサーバー、またはこのコンテナーに関連付けられたバックアップ管理サーバーがないことを確認してください。 削除を続行する前に、このコンテナーに関連付けられた次のコンテナーの登録を解除します。

- Recovery Services コンテナー内に論理的に削除された状態のバックアップ項目があるため、そのコンテナーを削除できません。 論理的に削除された項目は、削除操作の 14 日後に完全に削除されます。 バックアップ項目が完全に削除され、コンテナーに論理的な削除状態の項目がなくなってからコンテナーの削除を試みてください。 詳細については、「[Azure Backup の論理的な削除](./backup-azure-security-feature-cloud.md)」を参照してください。

## <a name="proper-way-to-delete-a-vault"></a>コンテナーを削除する正しい方法

>[!WARNING]
>下記の操作は破壊的であり、元に戻すことはできません。 保護されたサーバーに関連付けられているすべてのバックアップ データとバックアップ項目が、完全に削除されます。 慎重に進めてください。

コンテナーを正しく削除するには、次の順序で手順に従う必要があります。

- **手順 1.** :論理的な削除機能を無効にします。 論理的な削除を無効にする手順については、[ここを参照](./backup-azure-security-feature-cloud.md#enabling-and-disabling-soft-delete)してください。

- **手順 2**:論理的な削除を無効にしたら、以前に論理的に削除された状態で残っている項目があるかどうかを確認します。 論理的に削除された状態の項目がある場合は、*削除の取り消し*と、再度の*削除*を行う必要があります。 [次の手順に従って](./backup-azure-security-feature-cloud.md#permanently-deleting-soft-deleted-backup-items)論理的な削除の項目を見つけ、それらを完全に削除します。

- **手順 3**:次の 3 つの場所すべてを調べて、保護された項目があるかどうかを確認する必要があります。

  - **クラウドで保護されている項目**:コンテナーのダッシュボード メニューの **[バックアップ項目]** に移動します。 ここに一覧表示されるすべての項目を、 **[バックアップの停止]** または **[バックアップ データの削除]** を使用して、それらのバックアップ データと一緒に削除する必要があります。  [これらの手順に従って](#delete-protected-items-in-the-cloud)それらの項目を削除します。
  - **SQL Server インスタンス**:コンテナーのダッシュボード メニューの **[バックアップ インフラストラクチャ]**  >  **[保護されたサーバー]** に移動します。 [保護されたサーバー] で、登録解除するサーバーを選択します。 コンテナーを削除するには、すべてのサーバーの登録を解除する必要があります。 保護されたサーバーを右クリックし、 **[登録解除]** を選択します。
  - **MARS で保護されているサーバー**:コンテナーのダッシュボード メニューの **[バックアップ インフラストラクチャ]**  >  **[保護されたサーバー]** に移動します。 MARS で保護されているサーバーがある場合は、ここに一覧表示されるすべての項目を、それらのバックアップ データと一緒に削除する必要があります。 [こちらの手順](#delete-protected-items-on-premises)に従って、MARS で保護されているサーバーを削除します。
  - **MABS または DPM 管理サーバー**:コンテナーのダッシュボード メニューの **[バックアップ インフラストラクチャ]**  >  **[バックアップ管理サーバー]** に移動します。 DPM または Azure Backup Server (MABS) を使用している場合は、ここに一覧表示されるすべての項目を、それらのバックアップ データと一緒に削除または登録解除する必要があります。 [これらの手順に従って](#delete-protected-items-on-premises)管理サーバーを削除します。

- **手順 4**:登録されているすべてのストレージ アカウントが確実に削除されるようにする必要があります。 コンテナーのダッシュボード メニューの **[バックアップ インフラストラクチャ]**  >  **[ストレージ アカウント]** に移動します。 ここに一覧表示されるストレージ アカウントがある場合は、それらすべての登録を解除する必要があります。 アカウントの登録を解除する方法については、「[ストレージアカウントの登録を解除する](manage-afs-backup.md#unregister-a-storage-account)」を参照してください。

これらの手順を完了したら、引き続き[コンテナーを削除](#delete-the-recovery-services-vault)できます。

保護されている項目がオンプレミスやクラウドになくても、引き続きコンテナーの削除エラーが発生している場合は、「[Azure Resource Manager を使用して Recovery Services コンテナーを削除する](#delete-the-recovery-services-vault-by-using-azure-resource-manager)」の手順を実行します

## <a name="delete-protected-items-in-the-cloud"></a>クラウド内の保護されたアイテムを削除する

最初に「 **[開始する前に](#before-you-start)** 」セクションを読み、依存関係とコンテナーの削除プロセスを理解してください。

保護を停止してバックアップ データを削除するには、次の手順を実行します。

1. ポータルから **[Recovery Services コンテナー]** に移動し、次に **[バックアップ アイテム]** に移動します。 次に、クラウド内の保護されたアイテムを選択します (Azure Virtual Machines、Azure Storage の Azure Files サービス、Azure Virtual Machines 上の SQL Server など)。

    ![バックアップの種類を選択する。](./media/backup-azure-delete-vault/azure-storage-selected.png)

2. 右クリックしてバックアップ アイテムを選択します。 バックアップ アイテムが保護されているかどうかに応じて、メニューに **[バックアップの停止]** ウィンドウまたは **[バックアップ データの削除]** ウィンドウのいずれかが表示されます。

    - **[バックアップの停止]** ウィンドウが表示されたら、ボックスの一覧から **[バックアップ データの削除]** を選択します。 バックアップ アイテムの名前を入力し (このフィールドでは大文字と小文字が区別されます)、ボックスの一覧から理由を選択します。 コメントがあれば、入力します。 次に、 **[バックアップの停止]** を選択します。

        ![[バックアップの停止] ウィンドウ。](./media/backup-azure-delete-vault/stop-backup-item.png)

    - **[バックアップ データの削除]** ウィンドウが表示されたら、バックアップ アイテムの名前を入力し (このフィールドでは大文字と小文字が区別されます)、ボックスの一覧から理由を選択します。 コメントがあれば、入力します。 次に、 **[削除]** を選択します。

         ![[バックアップ データの削除] ウィンドウ。](./media/backup-azure-delete-vault/stop-backup-blade-delete-backup-data.png)

3. 次の **[通知]** アイコンを確認します: ![[通知] アイコン。](./media/backup-azure-delete-vault/messages.png) プロセスが完了すると、サービスによって次のメッセージが表示されます。*バックアップを停止し、"* バックアップ アイテム *" のバックアップ データを削除しています*。 *操作は正常に完了しました*。
4. **[バックアップ アイテム]** メニューで **[更新]** を選択して、バックアップ アイテムが削除されたことを確認します。

      ![バックアップ アイテムの削除に関するページ。](./media/backup-azure-delete-vault/empty-items-list.png)

## <a name="delete-protected-items-on-premises"></a>オンプレミスの保護されたアイテムを削除する

最初に「 **[開始する前に](#before-you-start)** 」セクションを読み、依存関係とコンテナーの削除プロセスを理解してください。

1. コンテナーのダッシュボード メニューから **[バックアップ インフラストラクチャ]** を選択します。
2. オンプレミスのシナリオに応じて、次のオプションのいずれかを選択します。

      - MARS の場合は、 **[保護されたサーバー]** 、 **[Azure Backup エージェント]** の順に選択します。 次に、削除するサーバーを選択します。

        ![MARS の場合、目的のコンテナーを選択してそのダッシュボードを開く。](./media/backup-azure-delete-vault/identify-protected-servers.png)

      - MABS または DPM の場合、 **[バックアップ管理サーバー]** を選択します。 次に、削除するサーバーを選択します。

          ![MABS または DPM の場合、目的のコンテナーを選択してそのダッシュボードを開く。](./media/backup-azure-delete-vault/delete-backup-management-servers.png)

3. 警告メッセージのある **[削除]** ウィンドウが表示されます。

     ![[削除] ウィンドウ。](./media/backup-azure-delete-vault/delete-protected-server.png)

     警告メッセージと、同意のチェック ボックスの指示を確認します。
    > [!NOTE]
    >
    >- 保護されたサーバーが Azure サービスと同期していて、バックアップ アイテムが存在する場合は、同意のチェック ボックスに、依存するバックアップ アイテムの数と、バックアップ アイテムを表示するためのリンクが表示されます。
    >- 保護されたサーバーが Azure サービスと同期しておらず、バックアップ アイテムが存在する場合は、同意のチェック ボックスに、バックアップ アイテムの数だけが表示されます。
    >- バックアップ アイテムが存在しない場合は、同意のチェック ボックスで削除が求められます。

4. 同意のチェック ボックスをオンにして、 **[削除]** を選択します。

5. **通知**アイコン ![バックアップデータの削除](./media/backup-azure-delete-vault/messages.png) を確認します。 操作が完了すると、次のメッセージが表示されます。"*バックアップを停止し、"バックアップ アイテム" のバックアップ データを削除しています。* " *操作は正常に完了しました*。
6. **[バックアップ アイテム]** メニューで **[更新]** を選択して、バックアップ アイテムが削除されていることを確認します。

>[!NOTE]
>依存関係が含まれるオンプレミスの保護されている項目をポータルから削除すると、次の警告が表示されます: "サーバーの登録の削除は破壊的な操作であり、元に戻すことはできません。 すべてのバックアップ データ (データの復元に必要な回復ポイント) と保護されたサーバーに関連付けられたバックアップ項目は、完全に削除されます。"

このプロセスが完了したら、管理コンソールからバックアップ アイテムを削除できます。

- [MARS 管理コンソールからバックアップ アイテムを削除する](#delete-backup-items-from-the-mars-management-console)
- [MABS または DPM 管理コンソールからバックアップ項目を削除する](#delete-backup-items-from-the-mabs-or-dpm-management-console)

### <a name="delete-backup-items-from-the-mars-management-console"></a>MARS 管理コンソールからバックアップ アイテムを削除する

>[!NOTE]
>バックアップを停止せずにソース マシンを削除したり失ったりした場合、次のスケジュールされたバックアップが失敗します。 古い復旧ポイントはポリシーに従って期限切れになりますが、最後の 1 つの復旧ポイントは、ユーザーがバックアップを停止してデータを削除するまで常に保持されます。 これは、[このセクション](#delete-protected-items-on-premises)の手順に従うことで可能です。

1. MARS 管理コンソールを開き、 **[アクション]** ウィンドウに移動して、 **[バックアップのスケジュール]** を選択します。
2. **[スケジュールされたバックアップの変更または停止]** ページで、 **[このバックアップ スケジュールの使用を中止して、保存されているバックアップをすべて削除する]** を選択します。 次に、 **[次へ]** を選択します。

    ![スケジュールされたバックアップを変更または停止する。](./media/backup-azure-delete-vault/modify-schedule-backup.png)

3. **[スケジュールされたバックアップの停止]** ページで、 **[完了]** を選択します。

    ![スケジュールされたバックアップを停止する。](./media/backup-azure-delete-vault/stop-schedule-backup.png)
4. セキュリティ PIN (暗証番号) の入力を求められます。これは手動で生成する必要があります。 これを行うには、最初に Azure portal にサインインします。
5. **[Recovery Services コンテナー]**  >  **[設定]**  >  **[プロパティ]** の順に移動します。
6. **[セキュリティ PIN]** の下にある **[生成]** を選択します。 この PIN をコピーします。 この PIN は 5 分間だけ有効です。
7. 管理コンソールに PIN を貼り付け、 **[OK]** を選択します。

    ![セキュリティ PIN を生成する。](./media/backup-azure-delete-vault/security-pin.png)

8. **[バックアップの進行状況の変更]** ページに、次のメッセージが表示されます。"*削除されたバックアップ データは 14 日間保持されます。14 日が経過すると、バックアップ データが完全に削除されます。* " と表示されます。  

    ![バックアップ インフラストラクチャを削除する。](./media/backup-azure-delete-vault/deleted-backup-data.png)

オンプレミスのバックアップ アイテムを削除したら、ポータルから次の手順を実行します。

### <a name="delete-backup-items-from-the-mabs-or-dpm-management-console"></a>MABS または DPM 管理コンソールからバックアップ項目を削除する

>[!NOTE]
>バックアップを停止せずにソース マシンを削除したり失ったりした場合、次のスケジュールされたバックアップが失敗します。 古い復旧ポイントはポリシーに従って期限切れになりますが、最後の 1 つの復旧ポイントは、ユーザーがバックアップを停止してデータを削除するまで常に保持されます。 これは、[このセクション](#delete-protected-items-on-premises)の手順に従うことで可能です。

MABS または DPM 管理コンソールからバックアップ項目を削除するには、次の 2 つの方法が使用できます。

#### <a name="method-1"></a>方法 1

保護を停止してバックアップ データを削除するには、次の手順を実行します。

1. DPM 管理者コンソールを開いてから、ナビゲーション バーの **[保護]** を選択します。
2. 表示ウィンドウで、削除する保護グループのメンバーを選択します。 右クリックして **[Stop Protection of Group Members]\(グループ メンバーの保護を停止する\)** オプションを選択します。
3. **[保護の停止]** ダイアログ ボックスで、 **[保護されるデータを削除する]** を選択してから、 **[Delete storage online]\(オンラインでストレージを削除する\)** チェック ボックスをオンにします。 次に、 **[保護の停止]** を選択します。

    ![[保護の停止] ウィンドウで [保護されるデータを削除する] を選択する。](./media/backup-azure-delete-vault/delete-storage-online.png)

    保護されたメンバーの状態が、"*非アクティブなレプリカを利用可能*" に変わります。

4. 非アクティブな保護グループを右クリックして、 **[非アクティブな保護の削除]** を選択します。

    ![非アクティブな保護を削除する。](./media/backup-azure-delete-vault/remove-inactive-protection.png)

5. **[非アクティブな保護の削除]** ウィンドウで **[Delete storage online]\(オンラインでストレージを削除する\)** チェック ボックスをオンにしてから、 **[OK]** を選択します。

    ![オンライン ストレージを削除する。](./media/backup-azure-delete-vault/remove-replica-on-disk-and-online.png)

#### <a name="method-2"></a>方法 2

**MABS 管理**コンソールまたは **DPM 管理**コンソールを開きます。 **[データの保護方法の選択]** で、 **[オンライン保護を利用する]** チェック ボックスをオフにします。

  ![データ保護方法を選択します。](./media/backup-azure-delete-vault/data-protection-method.png)

オンプレミスのバックアップ アイテムを削除したら、ポータルから次の手順を実行します。

## <a name="delete-the-recovery-services-vault"></a>Recovery Services コンテナーを削除する

1. すべての依存関係が削除されたら、コンテナー メニュー内の **[Essentials]** ウィンドウにスクロールします。
2. バックアップ アイテム、バックアップ管理サーバー、レプリケートされたアイテムがどれも一覧に表示されていないことを確認します。 アイテムがコンテナーにまだ表示されている場合は、「[開始する前に](#before-you-start)」セクションを参照してください。

3. コンテナーにアイテムがなくなったら、コンテナー ダッシュボードで **[削除]** を選択します。

    ![コンテナー ダッシュボードで [削除] を選択する。](./media/backup-azure-delete-vault/vault-ready-to-delete.png)

4. コンテナーを削除することを確認するために、 **[はい]** を選択します。 コンテナーが削除されます。 ポータルが **[新規作成]** サービス メニューに戻ります。

## <a name="delete-the-recovery-services-vault-by-using-powershell"></a>PowerShell を使用して Recovery Services コンテナーを削除する

最初に「 **[開始する前に](#before-you-start)** 」セクションを読み、依存関係とコンテナーの削除プロセスを理解してください。

保護を停止してバックアップ データを削除するには:

- Azure VM バックアップで SQL を使用しており、SQL インスタンスの自動保護を有効にしている場合は、まずこの自動保護を無効にします。

    ```PowerShell
        Disable-AzRecoveryServicesBackupAutoProtection
           [-InputItem] <ProtectableItemBase>
           [-BackupManagementType] <BackupManagementType>
           [-WorkloadType] <WorkloadType>
           [-PassThru]
           [-VaultId <String>]
           [-DefaultProfile <IAzureContextContainer>]
           [-WhatIf]
           [-Confirm]
           [<CommonParameters>]
    ```

  Azure Backup で保護された項目の保護を無効にする方法について、[詳細を参照](/powershell/module/az.recoveryservices/disable-azrecoveryservicesbackupautoprotection)してください。

- クラウド内のバックアップで保護されているすべての項目の保護を停止し、データを削除します (例:IaaS VM、Azure ファイル共有など)。

    ```PowerShell
       Disable-AzRecoveryServicesBackupProtection
       [-Item] <ItemBase>
       [-RemoveRecoveryPoints]
       [-Force]
       [-VaultId <String>]
       [-DefaultProfile <IAzureContextContainer>]
       [-WhatIf]
       [-Confirm]
       [<CommonParameters>]
    ```

    Backup で保護された項目の保護の無効化について、[詳細を学習](/powershell/module/az.recoveryservices/disable-azrecoveryservicesbackupprotection) してください。

- Azure をバックアップ先とする Azure Backup エージェント (MARS) を使用して保護されているオンプレミスのファイルとフォルダーの場合は、次の PowerShell コマンドを使用して、各 MARS PowerShell モジュールからバックアップ データを削除します。

    ```powershell
    Get-OBPolicy | Remove-OBPolicy -DeleteBackup -SecurityPIN <Security Pin>
    ```

    その後、次のプロンプトが表示されます。

    *Microsoft Azure Backup このバックアップ ポリシーを削除してよろしいですか? 削除されたバックアップ データは 14 日間保持されます。14 日が経過すると、バックアップ データが完全に削除されます。<br/> [Y] はい [A] すべてはい [N] いいえ [L] すべていいえ [S] 中断 [?] ヘルプ (既定値は "Y"):*

- MABS (Microsoft Azure Backup Server) または Azure への DPM (System Center Data Protection Manager) を使用して保護されているオンプレミスのマシンの場合、次のコマンドを使用して、Azure にバックアップされているデータを削除します。

    ```powershell
    Get-OBPolicy | Remove-OBPolicy -DeleteBackup -SecurityPIN <Security Pin>
    ```

    その後、次のプロンプトが表示されます。

   *Microsoft Azure Backup* このバックアップ ポリシーを削除してよろしいですか? 削除されたバックアップ データは 14 日間保持されます。 その期間が経過すると、バックアップ データが完全に削除されます。 <br/>
   [Y] はい [A] すべてはい [N] いいえ [L] すべていいえ [S] 中断 [?] ヘルプ (既定値は "Y"):*

バックアップされたデータを削除した後、オンプレミスのすべてのコンテナーと管理サーバーの登録を解除します。

- Azure をバックアップ先とする Azure Backup エージェント (MARS) を使用して保護されているオンプレミスのファイルとフォルダーの場合:

    ```PowerShell
    Unregister-AzRecoveryServicesBackupContainer
              [-Container] <ContainerBase>
              [-PassThru]
              [-VaultId <String>]
              [-DefaultProfile <IAzureContextContainer>]
              [-WhatIf]
              [-Confirm]
              [<CommonParameters>]
    ```

    ボールトからの Windows Server またはその他のコンテナーの登録解除について、[詳細を学習](/powershell/module/az.recoveryservices/unregister-azrecoveryservicesbackupcontainer)してください。

- MABS (Microsoft Azure Backup Server) または Azure への DPM (System Center Data Protection Manager) を使用して保護されたオンプレミスのマシンの場合:

    ```PowerShell
        Unregister-AzRecoveryServicesBackupManagementServer
          [-AzureRmBackupManagementServer] <BackupEngineBase>
          [-PassThru]
          [-VaultId <String>]
          [-DefaultProfile <IAzureContextContainer>]
          [-WhatIf]
          [-Confirm]
          [<CommonParameters>]
    ```

    ボールトからのバックアップ管理コンテナーの登録解除について、[詳細を学習](/powershell/module/az.recoveryservices/unregister-azrecoveryservicesbackupcontainer)してください。

バックアップされたデータを完全に削除し、すべてのコンテナーの登録を解除してから、ボールトの削除に進みます。

Recovery Services コンテナーを削除するには:

   ```PowerShell
       Remove-AzRecoveryServicesVault
      -Vault <ARSVault>
      [-DefaultProfile <IAzureContextContainer>]
      [-WhatIf]
      [-Confirm]
      [<CommonParameters>]
   ```

Recovery Services コンテナーの削除について、[詳細を学習](/powershell/module/az.recoveryservices/remove-azrecoveryservicesvault)してください。

## <a name="delete-the-recovery-services-vault-by-using-cli"></a>CLI を使用して Recovery Services コンテナーを削除する

最初に「 **[開始する前に](#before-you-start)** 」セクションを読み、依存関係とコンテナーの削除プロセスを理解してください。

> [!NOTE]
> 現在、Azure Backup CLI は Azure VM のバックアップの管理のみをサポートしているため、ボールトを削除する次のコマンドは、ボールトに Azure VM のバックアップが含まれている場合にのみ機能します。 ボールトに Azure VM 以外の種類のバックアップ項目が含まれている場合、Azure Backup CLI を使用してボールトを削除することはできません。

既存の Recovery Services コンテナーを削除するには、次の手順を行います。

- 保護を停止してバックアップ データを削除するには

    ```azurecli
    az backup protection disable --container-name
                             --item-name
                             [--delete-backup-data {false, true}]
                             [--ids]
                             [--resource-group]
                             [--subscription]
                             [--vault-name]
                             [--yes]
    ```

    詳細については、こちらの [記事](/cli/azure/backup/protection#az-backup-protection-disable)を参照してください。

- 既存の Recovery Services コンテナーを削除します。

    ```azurecli
    az backup vault delete [--force]
                       [--ids]
                       [--name]
                       [--resource-group]
                       [--subscription]
                       [--yes]
    ```

    詳細については、こちらの [記事](/cli/azure/backup/vault?view=azure-cli-latest)を参照してください

## <a name="delete-the-recovery-services-vault-by-using-azure-resource-manager"></a>Azure Resource Manager を使用して Recovery Services コンテナーを削除する

Recovery Services コンテナーを削除するこのオプションは、すべての依存関係が削除されても "*コンテナーの削除エラー*" が引き続き発生する場合にのみ、お勧めします。 次のヒントのいずれかまたはすべてをお試しください。

- コンテナー メニューの **[基本]** ウィンドウから、バックアップ アイテム、バックアップ管理サーバー、レプリケートされたアイテムがどれも一覧に表示されていないことを確認します。 バックアップ項目がある場合は、「[開始する前に](#before-you-start)」セクションを参照してください。
- [ポータルからのコンテナーの削除](#delete-the-recovery-services-vault)を再試行します。
- 依存関係をすべて削除してもなお、"*コンテナーの削除エラー*" が発生する場合は、ARMClient ツールを使用して次の手順を実行します (注記の後に記載)。

1. [chocolatey.org](https://chocolatey.org/) にアクセスし、Chocolatey をダウンロードしてインストールします。 次に、次のコマンドを実行して ARMClient エージェントをインストールします。

   `choco install armclient --source=https://chocolatey.org/api/v2/`
2. 自分の Azure アカウントにサインインして、次のコマンドを実行します。

    `ARMClient.exe login [environment name]`

3. Azure portal で、削除するコンテナーのサブスクリプション ID とリソース グループ名を収集します。

ARMClient コマンドの詳細については、[ARMClient の README](https://github.com/projectkudu/ARMClient/blob/master/README.md) を参照してください。

### <a name="use-the-azure-resource-manager-client-to-delete-a-recovery-services-vault"></a>Azure Resource Manager クライアントを使用して Recovery Services コンテナーを削除する

1. ご利用のサブスクリプション ID、リソース グループ名、コンテナー名を使用して、次のコマンドを実行します。 依存関係がなければ、そのコンテナーは次のコマンドを実行するときに削除されます。

   ```azurepowershell
   ARMClient.exe delete /subscriptions/<subscriptionID>/resourceGroups/<resourcegroupname>/providers/Microsoft.RecoveryServices/vaults/<Recovery Services vault name>?api-version=2015-03-15
   ```

2. コンテナーが空でない場合は、次のエラー メッセージが表示されます:"*内部にリソースが存在するため、資格情報コンテナーを削除できません*"。 コンテナー内にある保護されたアイテムまたはコンテナーを削除するには、次のコマンドを実行します。

   ```azurepowershell
   ARMClient.exe delete /subscriptions/<subscriptionID>/resourceGroups/<resourcegroupname>/providers/Microsoft.RecoveryServices/vaults/<Recovery Services vault name>/registeredIdentities/<container name>?api-version=2016-06-01
   ```

3. Azure portal で、値が削除されていることを確認します。

## <a name="next-steps"></a>次のステップ

[Recovery Services コンテナーの詳細情報](backup-azure-recovery-services-vault-overview.md)
[Recovery Services コンテナーの監視と管理の詳細情報](backup-azure-manage-windows-server.md)
