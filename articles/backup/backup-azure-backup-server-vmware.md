---
title: Azure Backup Server を使用して VMware VM をバックアップする
description: Azure Backup Server を使用して、VMware vCenter/ESXi サーバー上で実行している VMware VM をバックアップします。
author: rayne-wiselman
manager: carmonm
ms.service: backup
ms.topic: conceptual
ms.date: 12/11/2018
ms.author: raynew
ms.openlocfilehash: adb8cb6a5740b7d09848c792093ea62f69f521a7
ms.sourcegitcommit: c72ddb56b5657b2adeb3c4608c3d4c56e3421f2c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/24/2019
ms.locfileid: "68466807"
---
# <a name="back-up-vmware-vms-with-azure-backup-server"></a>Azure Backup Server を使用して VMware VM をバックアップする

この記事では、Azure Backup Server を使用して、VMware ESXi ホスト/vCenter Server 上で実行されている VMware VM を Azure にバックアップする方法について説明します。

この記事では、次の方法について説明します。

- Azure Backup Server が HTTPS 経由で VMware サーバーと通信できるように、セキュリティで保護されたチャネルを設定します。
- Azure Backup Server が VMware サーバーにアクセスするために使用する VMware アカウントを設定します。
- Azure Backup にアカウントの資格情報を追加します。
- vCenter Server または ESXi サーバーを Azure Backup Server に追加します。
- バックアップする VMware VM が含まれている保護グループを設定し、バックアップの設定を指定して、バックアップをスケジュールします。

## <a name="before-you-start"></a>開始する前に
- バックアップに対してサポートされている vCenter/ESXi のバージョン (バージョン 6.5、6.0、5.5) を実行していることを確認します。
- Azure Backup Server がセットアップされていることを確認します。 まだの場合は、始める前に[行います](backup-azure-microsoft-azure-backup.md)。 Azure Backup Server が最新の更新プログラムで実行されている必要があります。


## <a name="create-a-secure-connection-to-the-vcenter-server"></a>vCenter Server へのセキュリティで保護された接続の作成

既定では、Azure Backup Server は HTTPS 経由で VMware サーバーと通信します。 HTTPS 接続を設定するには、VMware 証明機関 (CA) 証明書をダウンロードして、Azure Backup Server にインポートします。


### <a name="before-you-start"></a>開始する前に

- HTTPS を使用しない場合は、[すべての VMware サーバーの HTTPS 証明書の検証を無効](backup-azure-backup-server-vmware.md#disable-https-certificate-validation)にできます。
- 通常は、Azure Backup Server マシン上のブラウザーから、vSphere Web Client を使用して、vCenter/ESXi サーバーに接続します。 初めてこれを行う場合、接続はセキュリティで保護されておらず、次のように表示されます。
- Azure Backup Server がバックアップを処理する方法を理解しておくことが重要です。
    - 最初のステップとして、Azure Backup Server はローカル ディスク ストレージにデータをバックアップします。 Azure Backup Server ではストレージ プールが使用されます。ストレージ プールとは、保護対象データに対するディスク復旧ポイントを Azure Backup Server が格納するディスクとボリュームのセットです。 ストレージ プールには、直接接続ストレージ (DAS)、ファイバー チャネル SAN、iSCSI ストレージ デバイスまたは SAN を使用できます。 VMware VM データのローカル バックアップ用に十分なストレージがあることを確認することが重要です。
    - その後、ローカル ディスク ストレージから Azure にバックアップが行われます。
    - 必要なストレージ容量を確認するには、[こちらをご覧ください](https://docs.microsoft.com/system-center/dpm/create-dpm-protection-groups?view=sc-dpm-1807#figure-out-how-much-storage-space-you-need)。 情報は DPM に対するものですが、Azure Backup Server にも使用できます。

### <a name="set-up-the-certificate"></a>証明書を設定する

次のようにセキュリティ保護されたチャネルを設定します。

1. Azure Backup Server のブラウザーで、vSphere Web Client の URL を入力します。 ログイン ページが表示されない場合は、接続とブラウザーのプロキシ設定を確認します。

    ![vSphere Web Client](./media/backup-azure-backup-server-vmware/vsphere-web-client.png)

2. VSphere Web Client のログイン ページで、 **[Download trusted root CA certificates]\(信頼されたルート CA 証明書のダウンロード\)** をクリックします。

    ![Download trusted root CA certificates (信頼されたルート CA 証明書のダウンロード)](./media/backup-azure-backup-server-vmware/vmware-download-ca-cert-prompt.png)

3. **download** という名前のファイルがダウンロードされます。 ブラウザーに応じて、ファイルを開くか保存するかを確認するメッセージが表示されます。

    ![CA 証明書をダウンロードする](./media/backup-azure-backup-server-vmware/download-certs.png)

4. Azure Backup Server マシンに .zip 拡張子を付けてファイルを保存します。

5. **download.zip** > を右クリックして、 **[すべて展開]** を選択します。 .zip ファイルの内容が **certs** フォルダーに展開されます。次のものが含まれます。
   - ルート証明書ファイル.0 や .1 などの連番で始まる拡張子が付きます。
   - CRL ファイルには、.r0 や .r1 などの連番で始まる拡張子が付きます。 CRL ファイルは、証明書と関連付けられています。

     ![ダウンロードされた証明書](./media/backup-azure-backup-server-vmware/extracted-files-in-certs-folder.png)

5. **certs** フォルダーで、ルート証明書ファイルを右クリックし、 **[名前の変更]** をクリックします。

    ![ルート証明書の名前の変更](./media/backup-azure-backup-server-vmware/rename-cert.png)

6. ルート証明書の拡張子を .crt に変更して、確認します。 ファイルのアイコンが、ルート証明書を表すものに変わります。

7. ルート証明書を右クリックし、ポップアップ メニューから **[証明書のインストール]** を選択します。

8. **証明書のインポート ウィザード**で、証明書のインポート先として **[ローカル コンピューター]** を選択し、 **[次へ]** をクリックします。 コンピューターへの変更を許可するかどうかの確認を求められたら、確認します。

    ![ウィザードのようこそ画面](./media/backup-azure-backup-server-vmware/certificate-import-wizard1.png)


9. **[証明書ストア]** ページで、 **[証明書をすべて次のストアに配置する]** を選択し、 **[参照]** をクリックして、証明書ストアを選択します。

     ![証明書ストレージ](./media/backup-azure-backup-server-vmware/cert-import-wizard-local-store.png)

10. **[証明書ストアの選択]** で、証明書のインポート先フォルダーとして **[信頼されたルート証明機関]** を選択し、 **[OK]** をクリックします。

    ![証明書の格納先フォルダー](./media/backup-azure-backup-server-vmware/certificate-store-selected.png)

11. **[証明書のインポート ウィザードの完了]** で、フォルダーを確認して、 **[完了]** をクリックします。

    ![証明書が適切なフォルダーにあることを確認する](./media/backup-azure-backup-server-vmware/cert-wizard-final-screen.png)


12. 証明書のインポートが完了した後、vCenter Server にサインインし、接続がセキュリティ保護されていることを確認します。




### <a name="disable-https-certificate-validation"></a>HTTPS 証明書の検証の無効化

組織内にセキュリティ保護された境界があり、VMware サーバーと Azure Backup Server マシンの間で HTTPS プロトコルを使いたくない場合は、次のように HTTPS を無効にします。
1. 以下のテキストをコピーして、.txt ファイルに貼り付けます。

      ```
      Windows Registry Editor Version 5.00
      [HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Microsoft Data Protection Manager\VMWare]
      "IgnoreCertificateValidation"=dword:00000001
      ```

2. そのファイルを **DisableSecureAuthentication.reg** という名前で Azure Backup Server マシンに保存します。

3. ファイルをダブルクリックして、レジストリ エントリをアクティブ化します。


## <a name="create-a-vmware-role"></a>VMware ロールを作成する

Azure Backup Server では、v-Center Server/ESXi ホストへのアクセス許可を持つユーザー アカウントが必要です。 特定の権限を持つ VMware ロールを作成し、ユーザー アカウントをそのロールと関連付けます。

1. vCenter Server (または、vCenter Server を使用していない場合は ESXi ホスト) にサインインします。
2. **[Navigator]\(ナビゲーター\)** パネルで、 **[Administration]\(管理\)** をクリックします。

    ![管理](./media/backup-azure-backup-server-vmware/vmware-navigator-panel.png)

3. **[Administration]\(管理\)**  >  **[Roles]\(ロール\)** で、ロールの追加アイコン (+ 記号) をクリックします。

    ![ロールの追加](./media/backup-azure-backup-server-vmware/vmware-define-new-role.png)


4. **[Create Role]\(ロールの作成\)**  >  **[Role name]\(ロール名\)** で、「*BackupAdminRole*」と入力します。 ロール名は任意の名前でかまいませんが、ロールの目的を識別できる名前である必要があります。

5. 次の表にまとめたように特権を選択して、 **[OK]** をクリックします。  **[Roles]\(ロール\)** パネルの一覧に新しいロールが表示されます。
   - 親ラベルの横にあるアイコンをクリックして親を展開し、子権限を表示します。
   - VirtualMachine の権限を選択するには、親子階層を複数のレベルにわたって移動する必要があります。
   - 親権限のすべての子権限を選択する必要はありません。

     ![権限の親子階層](./media/backup-azure-backup-server-vmware/cert-add-privilege-expand.png)

### <a name="role-permissions"></a>ロールのアクセス許可
**6.5/6.0** | **5.5**
--- | ---
Datastore.AllocateSpace | Datastore.AllocateSpace
Global.ManageCustomFields | Global.ManageCustomFields
Global.SetCustomField |
Host.Local.CreateVM | Network.Assign
Network.Assign |
Resource.AssignVMToPool |
VirtualMachine.Config.AddNewDisk  | VirtualMachine.Config.AddNewDisk   
VirtualMachine.Config.AdvancedConfig| VirtualMachine.Config.AdvancedConfig
VirtualMachine.Config.ChangeTracking| VirtualMachine.Config.ChangeTracking
VirtualMachine.Config.HostUSBDevice |
VirtualMachine.Config.QueryUnownedFiles |
VirtualMachine.Config.SwapPlacement| VirtualMachine.Config.SwapPlacement
VirtualMachine.Interact.PowerOff| VirtualMachine.Interact.PowerOff
VirtualMachine.Inventory.Create| VirtualMachine.Inventory.Create
VirtualMachine.Provisioning.DiskRandomAccess |
VirtualMachine.Provisioning.DiskRandomRead | VirtualMachine.Provisioning.DiskRandomRead
VirtualMachine.State.CreateSnapshot | VirtualMachine.State.CreateSnapshot
VirtualMachine.State.RemoveSnapshot | VirtualMachine.State.RemoveSnapshot




## <a name="create-a-vmware-account"></a>VMware アカウントを作成する

1. vCenter Server の **[Navigator]\(ナビゲーター\)** パネルで、 **[Users and Groups]\(ユーザーとグループ\)** をクリックします。 vCenter Server を使用しない場合は、適切な ESXi ホスト上にアカウントを作成します。

    ![[ユーザーとグループ] のオプション](./media/backup-azure-backup-server-vmware/vmware-userandgroup-panel.png)

    **[vCenter Users and Groups]\(vCenter のユーザーとグループ\)** パネルが表示されます。


2. **[vCenter Users and Groups]\(vCenter のユーザーとグループ\)** パネルで **[ユーザー]** タブを選択し、ユーザーの追加アイコン (+ 記号) をクリックします。

     ![[vCenter Users and Groups]\(vCenter のユーザーとグループ\) パネル](./media/backup-azure-backup-server-vmware/usersandgroups.png)


3. **[New User]\(新規ユーザー\)** ダイアログ ボックスで、ユーザー情報を追加して、 **[OK]** をクリックします。 この手順では、ユーザー名は BackupAdmin です。

    ![[新しいユーザー] ダイアログ ボックス](./media/backup-azure-backup-server-vmware/vmware-new-user-account.png)


4. ユーザー アカウントをロールに関連付けるには、 **[ナビゲーター]** パネルで **[グローバル アクセス許可]** をクリックします。 **[グローバル アクセス許可]** パネルで **[管理]** タブを選択し、追加アイコン (+ 記号) をクリックします。

    ![[グローバル アクセス許可] パネル](./media/backup-azure-backup-server-vmware/vmware-add-new-perms.png)


5. **[Global Permissions Root - Add Permission]\(グローバル アクセス許可のルート - アクセス許可の追加\)** ダイアログ ボックスで、 **[Add]\(追加\)** をクリックして、ユーザーまたはグループを選択します。

    ![ユーザーまたはグループの選択](./media/backup-azure-backup-server-vmware/vmware-add-new-global-perm.png)

6. **[Select Users/Groups]\(ユーザー/グループの選択\)** で、 **[BackupAdmin]**  >  **[Add]\(追加\)** を選択します。 **[ユーザー]** では、ユーザー アカウントに *domain\username* の形式が使用されます。 別のドメインを使用する場合は、 **[ドメイン]** の一覧からドメインを選択します。 **[OK]** をクリックして、選択したユーザーを **[アクセス許可の追加]** ダイアログ ボックスに追加します。

    ![BackupAdmin ユーザーを追加する](./media/backup-azure-backup-server-vmware/vmware-assign-account-to-role.png)


7.  **[Assigned Role]\(割り当て済みロール\)** で、ドロップダウン リストから **[BackupAdminRole]**  >  を選択し、 **[OK]** をクリックします。

    ![ユーザーをロールに割り当てる](./media/backup-azure-backup-server-vmware/vmware-choose-role.png)


**[グローバル アクセス許可]** パネルの **[管理]** タブの一覧に新しいユーザー アカウントと、関連付けられているロールが表示されます。


## <a name="add-the-account-on-azure-backup-server"></a>Azure Backup Server でアカウントを追加する


1. Azure Backup Server を開きます。 デスクトップにアイコンが見つからない場合は、アプリの一覧から Azure Backup Server を開きます。

    ![Azure Backup Server アイコン](./media/backup-azure-backup-server-vmware/mabs-icon.png)

2. Azure Backup Server コンソールで、 **[管理]**  >   **[運用サーバー]**  >  **[VMware の管理]** をクリックします。

    ![Azure Backup Server コンソール](./media/backup-azure-backup-server-vmware/add-vmware-credentials.png)


3. **[資格情報の管理]** ダイアログ ボックスで、 **[追加]** をクリックします。

    ![Azure Backup Server の [資格情報の管理] ダイアログ ボックス](./media/backup-azure-backup-server-vmware/mabs-manage-credentials-dialog.png)

4. **[資格情報の追加]** で、新しい資格情報の名前と説明を入力し、VMware サーバーで定義したユーザー名とパスワードを指定します。 この手順では資格情報を識別するために *Contoso Vcenter credential* という名前を使用します。 VMware サーバーと Azure Backup Server が同じドメイン内にない場合は、ユーザー名でドメインを指定します。

    ![Azure Backup Server の [資格情報の追加] ダイアログ ボックス](./media/backup-azure-backup-server-vmware/mabs-add-credential-dialog2.png)

5. **[追加]** をクリックして、新しい資格情報を追加します。

    ![Azure Backup Server の [資格情報の管理] ダイアログ ボックス](./media/backup-azure-backup-server-vmware/new-list-of-mabs-creds.png)


## <a name="add-the-vcenter-server"></a>vCenter Server を追加する

Azure Backup Server に vCenter Server を追加します。


1. Azure Backup Server コンソールで、 **[管理]**  >  **[運用サーバー]**  >  **[追加]** をクリックします。

    ![運用サーバーの追加ウィザードを開く](./media/backup-azure-backup-server-vmware/add-vcenter-to-mabs.png)


2. **[運用サーバーの追加ウィザード]**  >  **[運用サーバーの種類を選択]** ページで、 **[VMware サーバー]** を選択し、 **[次へ]** をクリックします。

     ![運用サーバーの追加ウィザード](./media/backup-azure-backup-server-vmware/production-server-add-wizard.png)

3. **[Select Computers]** > **[サーバー名/IP アドレス]** で、VMware サーバーの FQDN または IP アドレスを指定します。 すべての ESXi サーバーが同じ vCenter で管理されている場合は、その vCenter の名前を指定します。 それ以外の場合は、ESXi ホストを追加します。

    ![VMware サーバーを指定する](./media/backup-azure-backup-server-vmware/add-vmware-server-provide-server-name.png)

4. **[SSL ポート]** で、VMware サーバーと通信するために使用するポートを入力します。 443 が既定のポートですが、VMware サーバーが別のポートでリッスンしている場合は変更できます。

5. **[資格情報の指定]** で、前に作成した資格情報を選択します。

    ![資格情報を指定する](./media/backup-azure-backup-server-vmware/identify-creds.png)

6. **[追加]** をクリックして、VMware サーバーをサーバーのリストに追加します。 その後、 **[次へ]** をクリックします。

    ![VMWare サーバーと資格情報を追加する](./media/backup-azure-backup-server-vmware/add-vmware-server-credentials.png)

7. **[概要]** ページで、 **[追加]** をクリックして VMware サーバーを Azure Backup Server に追加します。 新しいサーバーはすぐに追加され、VMware サーバー上にエージェントは必要ありません。

    ![Azure Backup Server への VMware サーバーの追加](./media/backup-azure-backup-server-vmware/tasks-screen.png)

8. **[完了]** ページで設定を確認します。

   ![[完了] ページ](./media/backup-azure-backup-server-vmware/summary-screen.png)

vCenter Server によって管理されていない ESXi ホストが複数ある場合、または vCenter Server の複数のインスタンスがある場合は、ウィザードを再度実行してサーバーを追加する必要があります。




## <a name="configure-a-protection-group"></a>保護グループの構成

バックアップ対象の VMware VM を追加します。 保護グループでは、複数の VM が収集されて、グループ内のすべての VM に、同じデータ保持とバックアップの設定が適用されます。


1. Azure Backup Server コンソールで、 **[保護]** > **[新規]** をクリックします。

    ![新しい保護グループの作成ウィザードを開く](./media/backup-azure-backup-server-vmware/open-protection-wizard.png)

1. **新しい保護グループの作成**ウィザードのようこそページで、 **[次へ]** をクリックします。

    ![[新しい保護グループの作成ウィザード] ダイアログ ボックス](./media/backup-azure-backup-server-vmware/protection-wizard.png)

1. **[保護グループの種類の選択]** ページで、 **[サーバー]** を選択し、 **[次へ]** をクリックします。 **[グループ メンバーの選択]** ページが表示されます。

1. **[グループ メンバーの選択]** で、バックアップする VM (または VM フォルダー) を選択します。 その後、 **[次へ]** をクリックします。

    - フォルダーを選択すると、そのフォルダー内の VM またはフォルダーもバックアップ対象に選択されます。 バックアップしたくないフォルダーや VM はオフにすることができます。
1. VM またはフォルダーが既にバックアップされている場合、それを選択することはできません。 これにより、1 つの VM に足して重複する復旧ポイントが作成されないことが保証されます。 。

     ![グループ メンバーの選択](./media/backup-azure-backup-server-vmware/server-add-selected-members.png)


1. **[データの保護方法の選択]** ページで、保護グループの名前と保護の設定を入力します。 Azure にバックアップするには、短期的な保護を **[ディスク]** に設定して、オンライン保護を有効にします。 その後、 **[次へ]** をクリックします。

    ![データ保護方法の選択](./media/backup-azure-backup-server-vmware/name-protection-group.png)

1. **[短期的な目標値の指定]** で、ディスクにバックアップされたデータを保持する期間を指定します。
   - **[リテンション期間]** で、ディスクの復旧ポイントを保持する日数を指定します。
   - **[同期の間隔]** で、ディスクの復旧ポイントを作成する頻度を指定します。
       - バックアップ間隔を設定しない場合は、 **[回復ポイントの直前]** をオンにして、各復旧ポイントがスケジュールされている直前にバックアップを実行できます。
       - 短期的なバックアップは完全バックアップであり、増分ではありません。
       - 短期的なバックアップを実行する日付/時刻を変更するには、 **[変更]** をクリックします。

     ![短期的な目標の指定](./media/backup-azure-backup-server-vmware/short-term-goals.png)

1. **[ディスク割り当ての確認]** では、VM に対して VM のバックアップ用に提供されているディスク領域を確認します 。

   - 推奨されるディスク割り当ては、指定した保有期間の範囲、ワークロードの種類、および保護データのサイズに基づきます。 必要な変更を行い、 **[次へ]** をクリックします。
   - **データ サイズ:** 保護グループ内のデータのサイズです。
   - **ディスク領域:** 保護グループの推奨ディスク領域の量です。 この設定を変更する場合は、どのデータ ソースも拡大するので、予想よりも少し大きめに合計領域を割り当てる必要があります。
   - **データの併置:** 併置を有効にすると、保護対象の複数のデータ ソースを 1 つのレプリカと復旧ポイントのボリュームにマップできます。 すべてのワークロードで併置がサポートされているわけではありません。
   - **自動的に拡張:** この設定を有効にした場合、保護グループ内のデータが最初に割り当てたサイズよりも大きくなると、Azure Backup Server はディスク サイズを 25% 増やそうとします。
   - **ストレージ プールの詳細:** 合計や残りのディスク サイズなど、ストレージ プールの状態が表示されます。

     ![ディスクの割り当てを確認する](./media/backup-azure-backup-server-vmware/review-disk-allocation.png)

1. **[レプリカの作成方法の選択]** ページで、初期バックアップの作成方法を指定し、 **[次へ]** をクリックします。
   - 既定値は、 **[自動 (ネットワーク経由)]** と **[今すぐ]** です。
   - 既定値を使用する場合は、ピーク時以外を指定することをお勧めします。 **[時間指定]** を選択し、日時を指定します。
   - データが大量にある場合や、ネットワークの状態が最適でない場合は、リムーバブル メディアを使用してオフラインでデータをレプリケートすることを検討してください。

     ![レプリカ作成方法の選択](./media/backup-azure-backup-server-vmware/replica-creation.png)

1. **[整合性チェック オプション]** で、整合性チェックを自動化する方法とタイミングを選択します。 その後、 **[次へ]** をクリックします。
      - 整合性チェックは、レプリカ データが不整合になったときに実行することや、設定したスケジュールで実行することができます。
      - 自動整合性チェックを構成しない場合は、チェックを手動で実行できます。 これを行うには、保護グループを右クリックして、 **[Perform Consistency Check]\(整合性チェックの実行\)** を選択します。

1. **[オンライン保護するデータの指定]** ページで、バックアップする VM または VM フォルダーを選択します。 メンバーを個別に選択するか、 **[すべて選択]** をクリックしてすべてのメンバーを選択することができます。 その後、 **[次へ]** をクリックします。

      ![オンライン保護データの指定](./media/backup-azure-backup-server-vmware/select-data-to-protect.png)

1. **[オンライン バックアップ スケジュールの指定]** ページで、ローカル ストレージから Azure にデータをバックアップする頻度を指定します。

    - スケジュールに従って、データのクラウド復旧ポイントが生成されます。 その後、 **[次へ]** をクリックします。
    - 復旧ポイントは生成されると、Azure の Recovery Services コンテナーに転送されます。

      ![オンライン バックアップ スケジュールの指定](./media/backup-azure-backup-server-vmware/online-backup-schedule.png)

1. **[オンライン保持ポリシーの指定]** ページで、Azure への毎日/毎週/毎月/毎年のバックアップから作成される復旧ポイントを保持する期間を指定します。 その後、 **[次へ]** をクリックします。

    - Azure にデータを保持できる期間に制限はありません。
    - 唯一の制限は、保護されたインスタンスあたりの復旧ポイントの数が 9999 までであることです。 この例では、保護するインスタンスは VMware サーバーです。

      ![オンライン保持ポリシーの指定](./media/backup-azure-backup-server-vmware/retention-policy.png)


1. **[概要]** ページで設定を確認して、 **[グループの作成]** をクリックします。

     ![保護グループのメンバーと設定の概要](./media/backup-azure-backup-server-vmware/protection-group-summary.png)

## <a name="vmware-vsphere-67"></a>VMware vSphere 6.7

vSphere 6.7 をバックアップするには、次の操作を行います。

- DPM サーバー上で TLS 1.2 を有効にします
  >[!Note]
  >VMware 6.7 以降では、通信プロトコルとして TLS が有効になっていました。

- 次のようにレジストリ キーを設定します。  

  Windows レジストリ エディター バージョン 5.00

  [HKEY_LOCAL_MACHINE\SOFTWARE\WOW6432Node\Microsoft\\.NETFramework\v2.0.50727] "SystemDefaultTlsVersions"=dword:00000001 "SchUseStrongCrypto"=dword:00000001

  [HKEY_LOCAL_MACHINE\SOFTWARE\WOW6432Node\Microsoft\\.NETFramework\v4.0.30319] "SystemDefaultTlsVersions"=dword:00000001 "SchUseStrongCrypto"=dword:00000001

  [HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\\.NETFramework\v2.0.50727] "SystemDefaultTlsVersions"=dword:00000001 "SchUseStrongCrypto"=dword:00000001

  [HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\\.NETFramework\v4.0.30319] "SystemDefaultTlsVersions"=dword:00000001 s"SchUseStrongCrypto"=dword:00000001


## <a name="next-steps"></a>次の手順

バックアップを設定するときの問題をトラブルシューティングする場合は、「[Azure Backup Server のトラブルシューティング](./backup-azure-mabs-troubleshoot.md)」をご覧ください。
