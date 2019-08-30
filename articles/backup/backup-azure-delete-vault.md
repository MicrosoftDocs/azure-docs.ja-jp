---
title: Azure の Recovery Services コンテナーを削除する
description: Recovery Services コンテナーを削除する方法について説明します。
author: dcurwin
manager: carmonm
ms.service: backup
ms.topic: conceptual
ms.date: 07/29/2019
ms.author: dacurwin
ms.openlocfilehash: 9c63170b60a871182042acab8a35e505c603f260
ms.sourcegitcommit: b12a25fc93559820cd9c925f9d0766d6a8963703
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/14/2019
ms.locfileid: "69018887"
---
# <a name="delete-a-recovery-services-vault"></a>Recovery Services コンテナーを削除する

この記事では、[Azure Backup](backup-overview.md) Recovery Services コンテナーを削除する方法について説明します。 依存関係を削除してからコンテナーの削除を行うための手順が含まれています。


## <a name="before-you-start"></a>開始する前に

コンテナーに関連付けられている保護されたサーバーやバックアップ管理サーバーなどの依存関係を持つ Recovery Services コンテナーを削除することはできません。

- バックアップ データを含むコンテナーを削除することはできません (つまり、保護を停止したけれどもバックアップデータを保持している場合であっても)。

- 依存関係が含まれるコンテナーを削除すると、次のエラーが表示されます。

  ![コンテナーの削除エラー](./media/backup-azure-delete-vault/error.png)

- 依存関係を含むオンプレミスの保護された項目 (MARS、MABS、または Azure への DPM) をポータルから削除すると、警告メッセージが表示されます。

  ![保護されたサーバーの削除エラー](./media/backup-azure-delete-vault/error-message.jpg)

  
コンテナーを正常に削除するには、ご使用のセットアップに一致するシナリオを選択し、推奨される手順に従います。

シナリオ | コンテナーを削除するために依存関係を削除する手順 |
-- | --
Azure への Azure Backup エージェント (MARS) バックアップを使用して保護されている、オンプレミスのファイルとフォルダーがある | 「バックアップ データとバックアップ アイテムを削除する」の「[MARS エージェントの場合](#delete-backup-items-from-mars-management-console)」にある手順を実行します
MABS (Microsoft Azure Backup Server) または Azure への DPM (System Center Data Protection Manager) を使用して保護されたオンプレミスのマシンがある | 「バックアップ データとバックアップ アイテムを削除する」の「[MABS エージェントの場合](#delete-backup-items-from-mabs-management-console)」にある手順を実行します
保護されたアイテムがクラウドにある (例: laaS VM、Azure ファイル共有など)  | 「バックアップ データとバックアップ アイテムを削除する」の「[クラウドの保護されたアイテムの場合](#delete-protected-items-in-cloud)」にある手順を実行します
保護されたアイテムがオンプレミスとクラウドの両方にある | 「バックアップ データとバックアップ アイテムを削除する」の手順を次の順番で実行します: <br> - [クラウドの保護されたアイテムの場合](#delete-protected-items-in-cloud)<br> - [MARS エージェントの場合](#delete-backup-items-from-mars-management-console) <br> - [MABS エージェントの場合](#delete-backup-items-from-mabs-management-console)
保護されたアイテムはオンプレミスまたはクラウドのどちらにもないが、コンテナーの削除エラーが引き続き発生している | 「[Azure Resource Manager クライアントを使用して Recovery Services コンテナーを削除する](#delete-the-recovery-services-vault-using-azure-resource-manager-client)」の手順を実行します


## <a name="delete-protected-items-in-cloud"></a>クラウド内の保護された項目の削除

続行する前に、 **[この](#before-you-start)** セクションを読み、依存関係とコンテナーの削除プロセスを理解してください。

保護を停止してバックアップ データを削除するには、以下の手順を実行します。

1. ポータルから、 **[Recovery Services コンテナー]**  >  **[バックアップ項目]** で、クラウド内の保護された項目 (AzureVirtual Machine、Azure Storage (Azure Files)、SQL on Azure VM など) を選択します。

    ![バックアップの種類を選択する](./media/backup-azure-delete-vault/azure-storage-selected.png)

2. バックアップ項目を右クリックします。 バックアップ項目が保護されているかどうかに応じて、メニューに **[バックアップの停止]** または **[バックアップ データの削除]** が表示されます。

    - **[バックアップの停止]** では、ドロップ ダウンから **[バックアップ データの削除]** を選択します。 バックアップ項目の**名前** (大文字と小文字を区別する) を入力し、**理由**を選択して、**コメント**を入力し、 **[バックアップの停止]** をクリックします。

        ![バックアップの種類を選択する](./media/backup-azure-delete-vault/stop-backup-item.png)

    - **[バックアップ データの削除]** の場合、バックアップ項目の名前 (大文字と小文字を区別する) を入力し、**理由**を選択して、**コメント**を入力し、 **[削除]** をクリックします。 

         ![[バックアップ データの削除]](./media/backup-azure-delete-vault/stop-backup-blade-delete-backup-data.png)

5. **[通知]** ![バックアップデータの削除](./media/backup-azure-delete-vault/messages.png)を確認します。 完了すると、サービスにより次のメッセージが表示されます: **バックアップを停止し、"*バックアップ アイテム*" のバックアップ データを削除しています**。 **操作は正常に完了しました**。
6. **[バックアップ アイテム]** メニューで **[更新]** をクリックして、バックアップ アイテムが削除されたかを確認します。

      ![[バックアップ データの削除]](./media/backup-azure-delete-vault/empty-items-list.png)

## <a name="delete-protected-items-on-premises"></a>オンプレミスの保護された項目の削除

作業を進める前に、 **[この](#before-you-start)** セクションを読み、依存関係とコンテナーの削除プロセスを理解してください。

1. コンテナーのダッシュボード メニューから **[インフラストラクチャのバックアップ]** をクリックします。
2. オンプレミスのシナリオに応じて、次のオプションを選択します。

      - **Azure Backup エージェント**の場合は、 **[保護されたサーバー]**  >  **[Azure Backup エージェント]** を選択し、削除するサーバーを選択します。 

        ![目的のコンテナーを選択してそのダッシュボードを開く](./media/backup-azure-delete-vault/identify-protected-servers.png)

      - **Azure Backup Server**/**DPM** の場合は、 **[バックアップ管理サーバー]** を選択します。 削除するサーバーを選択します。 


          ![コンテナーを選択してダッシュボードを選択する](./media/backup-azure-delete-vault/delete-backup-management-servers.png)

3. **[削除]** ブレードに警告メッセージが表示されます。

     ![[バックアップ データの削除]](./media/backup-azure-delete-vault/delete-protected-server.png)

     警告メッセージと、同意のチェック ボックスに示されている指示を確認します。
    
    > [!NOTE]
    >- 保護されたサーバーが Azure Service と同期していて、バックアップ項目が存在する場合は、同意のチェック ボックスに、依存するバックアップ項目の数と、バックアップ項目を表示するためのリンクが表示されます。
    >- 保護されたサーバーが Azure Service と同期しておらず、バックアップ項目が存在する場合は、同意のチェック ボックスに、バックアップ項目の数が表示されます。
    >- バックアップ項目が存在しない場合は、同意のチェック ボックスで削除が求められます。

4. 同意のチェック ボックスを選択して **[削除]** をクリックします。




5. **[通知]** ![バックアップデータの削除](./media/backup-azure-delete-vault/messages.png)を確認します。 完了すると、サービスにより次のメッセージが表示されます: **バックアップを停止し、"*バックアップ アイテム*" のバックアップ データを削除しています**。 **操作は正常に完了しました**。
6. **[バックアップ アイテム]** メニューで **[更新]** をクリックして、バックアップ アイテムが削除されたかを確認します。

これで、管理コンソールからバックアップ項目の削除に進むことができます。
    
   - [MARS を使用して保護された項目](#delete-backup-items-from-mars-management-console)
    - [MABS を使用して保護された項目](#delete-backup-items-from-mabs-management-console)


### <a name="delete-backup-items-from-mars-management-console"></a>MARS 管理コンソールからバックアップ アイテムを削除する

MARS 管理コンソールからバックアップ項目を削除するには

- MARS 管理コンソールを起動し、 **[操作]** ウィンドウに移動して **[バックアップのスケジュール]** を選択します。
- **[スケジュールされたバックアップの変更または停止]** ウィザードで、オプション **[このバックアップ スケジュールの使用を中止して、保存されているバックアップをすべて削除する]** を選択し、 **[次へ]** をクリックします。

    ![スケジュールされたバックアップの変更または停止](./media/backup-azure-delete-vault/modify-schedule-backup.png)

- **[スケジュールされたバックアップの変更または停止]** ウィザードで **[完了]** をクリックします。

    ![スケジュールされたバックアップの停止](./media/backup-azure-delete-vault/stop-schedule-backup.png)
- セキュリティ PIN の入力を求められます。 PIN を生成するには、次の手順を実行します。
  - Azure ポータルにサインインします。
  - **[Recovery Services コンテナー]**  >  **[設定]**  >  **[プロパティ]** の順に参照します。
  - **[セキュリティ PIN]** の下にある **[生成]** をクリックします。 この PIN をコピーします。 (この PIN は 5 分間だけ有効です。)
- 管理コンソール (クライアント アプリ) に PIN を貼り付けて **[OK]** をクリックします。

  ![セキュリティ PIN](./media/backup-azure-delete-vault/security-pin.png)

- **[バックアップの進行状況の変更]** ウィザードに "*削除されたバックアップ データは 14 日間保持されます。14 日が経過すると、バックアップ データが完全に削除されます。* " と表示されます。  

    ![バックアップ インフラストラクチャを削除する](./media/backup-azure-delete-vault/deleted-backup-data.png)

オンプレミスからバックアップ アイテムを削除したので、ポータルから次の手順を実行します。

### <a name="delete-backup-items-from-mabs-management-console"></a>MABS 管理コンソールからバックアップ アイテムを削除する

MABS 管理コンソールからバックアップ項目を削除するには

**方法 1** 保護を停止してバックアップ データを削除するには、以下の手順を実行します。

1.  DPM 管理者コンソールで、ナビゲーション バーの **[保護]** をクリックします。
2.  表示ウィンドウで、削除する保護グループのメンバーを選択します。 右クリックして **[Stop Protection of Group Members]\(グループ メンバーの保護を停止する\)** オプションを選択します。
3.  **[保護の停止]** ダイアログ ボックスで、 **[保護されるデータを削除する]**  >  **[オンライン記憶域を削除する]** チェック ボックスをオンにして、 **[保護の停止]** をクリックします。

    ![オンラインでストレージを削除](./media/backup-azure-delete-vault/delete-storage-online.png)

保護されたメンバーの状態は、"**利用可能な非アクティブなレプリカ**" に変わりました。

4. 非アクティブな保護グループを右クリックして、 **[非アクティブな保護の削除]** を選択します。

    ![非アクティブな保護の削除](./media/backup-azure-delete-vault/remove-inactive-protection.png)

5. **[非アクティブな保護の削除]** ウィンドウで **[オンライン記憶域を削除する]** を選択して、 **[OK]** をクリックします。

    ![ディスクとオンライン上のレプリカを削除する](./media/backup-azure-delete-vault/remove-replica-on-disk-and-online.png)

**方法 2** **MABS 管理**コンソールを起動します。 **[データの保護方法の選択]** セクションで、 **[オンライン保護を利用する]** をオフにします。

  ![データ保護方法の選択](./media/backup-azure-delete-vault/data-protection-method.png)

オンプレミスからバックアップ アイテムを削除したので、ポータルから次の手順を実行します。


## <a name="delete-the-recovery-services-vault"></a>Recovery Services コンテナーを削除する

1. すべての依存関係が削除されたら、コンテナー メニュー内の **[Essentials]** ウィンドウにスクロールします。
2. 一覧に **[バックアップ アイテム]** 、 **[バックアップ管理サーバー]** 、 **[レプリケートされたアイテム]** のどれも表示されないことを確認します。 項目がコンテナーにまだ表示されている場合は、「[開始する前に](#before-you-start)」セクションを参照してください。

3. コンテナーにアイテムがなくなったら、コンテナー ダッシュボードで **[削除]** をクリックします。

    ![[バックアップ データの削除]](./media/backup-azure-delete-vault/vault-ready-to-delete.png)

4. コンテナーを削除することを確認するために、 **[はい]** をクリックします。 コンテナーが削除され、ポータルが **[新規]** サービス メニューに戻ります。

## <a name="delete-the-recovery-services-vault-using-azure-resource-manager-client"></a>Azure Resource Manager クライアントを使用して Recovery Services コンテナーを削除する

Recovery Services コンテナーを削除するこのオプションは、すべての依存関係が削除されても、*コンテナーの削除エラー*が引き続き発生する場合にのみ、お勧めします。

- コンテナー メニューの **[基本]** ウィンドウから、一覧に **[バックアップ アイテム]** 、 **[バックアップ管理サーバー]** 、 **[レプリケートされたアイテム]** のどれも表示されないことを確認します。 バックアップ項目がある場合は、「[開始する前に](#before-you-start)」セクションを参照してください。
- [ポータルからのコンテナーの削除](#delete-the-recovery-services-vault)を再試行します。
- すべての依存関係が削除されても、*コンテナーの削除エラー*が引き続き発生する場合は、ARMClient ツールを使用して次の手順を実行します。

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

1. [ここ](https://chocolatey.org/)から Chocolatey をインストールして、ARMClient をインストールするために、以下のコマンドを実行します。

   `choco install armclient --source=https://chocolatey.org/api/v2/`
2. Azure アカウントにサインインして、次のコマンドを実行します。

    `ARMClient.exe login [environment name]`

3. Azure portal で、削除するコンテナーのサブスクリプション ID とリソース グループ名を収集します。

ARMClient コマンドの詳細については、この[ドキュメント](https://github.com/projectkudu/ARMClient/blob/master/README.md)を参照してください。

### <a name="use-azure-resource-manager-client-to-delete-recovery-services-vault"></a>Azure Resource Manager クライアントを使用して Recovery Services コンテナーを削除する

1. ご利用のサブスクリプション ID、リソース グループ名、コンテナー名を使用して、次のコマンドを実行します。 このコマンドを実行すると、依存関係がなければ、そのコンテナーが削除されます。

   ```azurepowershell
   ARMClient.exe delete /subscriptions/<subscriptionID>/resourceGroups/<resourcegroupname>/providers/Microsoft.RecoveryServices/vaults/<recovery services vault name>?api-version=2015-03-15
   ```
2. コンテナーが空ではない場合、"内部にリソースが存在するため、コンテナーを削除できません" という内容のエラーが発生します。 コンテナー内にある保護されたアイテムまたはコンテナーを削除するには、次の操作を行います。

   ```azurepowershell
   ARMClient.exe delete /subscriptions/<subscriptionID>/resourceGroups/<resourcegroupname>/providers/Microsoft.RecoveryServices/vaults/<recovery services vault name>/registeredIdentities/<container name>?api-version=2016-06-01
   ```

3. Azure portal で、値が削除されていることを確認します。


## <a name="next-steps"></a>次の手順

Recovery Services コンテナーの[詳細情報](backup-azure-recovery-services-vault-overview.md)。<br/>
Recovery Services コンテナーの監視と管理の[詳細情報](backup-azure-manage-windows-server.md)。
