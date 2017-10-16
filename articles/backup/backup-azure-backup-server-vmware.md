---
title: "Azure Backup Server を使用した VMware サーバーのバックアップ | Microsoft Docs"
description: "Azure Backup Server を使用して、Azure またはディスクに VMware vCenter/ESXi サーバーをバックアップします。 この記事では、VMware のワークロードをバックアップ (または保護) するための詳細な手順を説明します。"
services: backup
documentationcenter: 
author: markgalioto
manager: carmonm
ms.assetid: 6b131caf-de85-4eba-b8e6-d8a04545cd9d
ms.service: backup
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 07/24/2017
ms.author: markgal;
ms.openlocfilehash: ad331dffb7c31d12290f4223967c568e4535fe3c
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="back-up-a-vmware-server-to-azure"></a>Azure への VMware サーバーのバックアップ

この記事では、VMware サーバーのワークロードを保護するように Azure Backup Server を構成する方法について説明します。 Azure Backup Server が既にインストールされていることを前提としています。 Azure Backup Server がインストールされていない場合は、「[Azure Backup Server を使用してワークロードをバックアップするための準備](backup-azure-microsoft-azure-backup.md)」をご覧ください。

Azure Backup Server では、VMware vCenter Server バージョン 6.5、6.0、5.5 をバックアップまたは保護できます。


## <a name="create-a-secure-connection-to-the-vcenter-server"></a>vCenter Server へのセキュリティで保護された接続の作成

既定では、Azure Backup Server は HTTPS チャネル経由で各 vCenter Server と通信します。 セキュリティで保護された通信を有効にするには、VMware 証明機関 (CA) 証明書を Azure Backup Server にインストールすることをお勧めします。 セキュリティで保護された通信が必要ない場合、HTTPS 要件を無効にする方法については、「[セキュリティで保護された通信プロトコルを無効にする](backup-azure-backup-server-vmware.md#disable-secure-communication-protocol)」をご覧ください。 Azure Backup Server と vCenter Server の間にセキュリティで保護された接続を確立するには、信頼された証明書を Azure Backup Server にインポートします。

通常は、Azure Backup Server コンピューターのブラウザーを使用して、vSphere Web Client 経由で vCenter Server に接続します。 Azure Backup Server のブラウザーで vCenter Server に接続する場合、最初の接続はセキュリティで保護されていません。 次の図は、セキュリティで保護されていない接続を示しています。

![vCenter Server へのセキュリティで保護されていない接続の例](./media/backup-azure-backup-server-vmware/unsecure-url.png)

この問題を解決し、セキュリティで保護された接続を確立するには、信頼されたルート CA 証明書をダウンロードします。

1. Azure Backup Server のブラウザーに、vSphere Web Client への URL を入力します。 vSphere Web Client のログイン ページが表示されます。

    ![vSphere Web Client](./media/backup-azure-backup-server-vmware/vsphere-web-client.png)

    管理者および開発者を対象とした情報の下部で、**[Download trusted root CA certificates]\(信頼されたルート CA 証明書のダウンロード\)** リンクを探します。

    ![信頼されたルート CA 証明書をダウンロードするためのリンク](./media/backup-azure-backup-server-vmware/vmware-download-ca-cert-prompt.png)

  vSphere Web Client のログイン ページが表示されない場合は、ブラウザーのプロキシ設定を確認します。

2. **[Download trusted root CA certificates (信頼されたルート CA 証明書のダウンロード)]** をクリックします。

    vCenter Server によって、ファイルがローカル コンピューターにダウンロードされます。 ファイル名は **download** です。 ブラウザーに応じて、ファイルを開くか保存するかを確認するメッセージが表示されます。

    ![証明書ダウンロードのメッセージ](./media/backup-azure-backup-server-vmware/download-certs.png)

3. Azure Backup Server の場所にファイルを保存します。 ファイルを保存するとき、ファイル名拡張子 .zip を追加します。

    このファイルは、証明書に関する情報を含む .zip ファイルです。 .zip 拡張子により、展開ツールを使用できます。

4. **download.zip** を右クリックし、**[すべて展開]** を選択してコンテンツを展開します。

    .zip ファイルのコンテンツが **certs** フォルダーに展開されます。 certs フォルダーには 2 種類のファイルがあります。 ルート証明書ファイルには、.0 や .1 などの連番で始まる拡張子が付きます。
    
    CRL ファイルには、.r0 や .r1 などの連番で始まる拡張子が付きます。 CRL ファイルは、証明書と関連付けられています。

    ![ローカルに展開されたダウンロード ファイル ](./media/backup-azure-backup-server-vmware/extracted-files-in-certs-folder.png)

5. **certs** フォルダーで、ルート証明書ファイルを右クリックし、**[名前の変更]** をクリックします。

    ![ルート証明書の名前の変更 ](./media/backup-azure-backup-server-vmware/rename-cert.png)

    ルート証明書の拡張子を .crt に変更します。 拡張子の変更を確認するメッセージが表示されたら、**[はい]** または **[OK]** をクリックします。 それ以外の場合、ファイルの目的の機能を変更します。 ファイルのアイコンが、ルート証明書を示すアイコンに変わります。

6. ルート証明書を右クリックし、ポップアップ メニューから **[証明書のインストール]** を選択します。

    **[証明書のインポート ウィザード]** ダイアログ ボックスが表示されます。

7. **[証明書のインポート ウィザード]** ダイアログ ボックスで、証明書のインポート先として **[ローカル コンピューター]** を選択し、**[次へ]** をクリックして、続行します。

    ![証明書の格納先のオプション ](./media/backup-azure-backup-server-vmware/certificate-import-wizard1.png)

    コンピューターへの変更を許可するかどうかを確認するメッセージが表示されたら、すべての変更に対して、**[はい]** または **[OK]** をクリックします。

8. **[証明書ストア]** ページで、**[証明書をすべて次のストアに配置する]** を選択し、**[参照]** をクリックして、証明書ストアを選択します。

    ![証明書を特定の格納場所に配置する](./media/backup-azure-backup-server-vmware/cert-import-wizard-local-store.png)

    **[証明書ストアの選択]** ダイアログ ボックスが表示されます。

    ![証明書の格納フォルダーの階層](./media/backup-azure-backup-server-vmware/cert-store.png)

9. **[信頼されたルート証明機関]** を証明書のインポート先フォルダーとして選択し、**[OK]** をクリックします。

    ![証明書の格納先フォルダー](./media/backup-azure-backup-server-vmware/certificate-store-selected.png)

    **[信頼されたルート証明機関]** フォルダーが証明書ストアとして確定します。 **[次へ]** をクリックします。

    ![証明書ストアのフォルダー](./media/backup-azure-backup-server-vmware/certificate-import-wizard2.png)

10. **[証明書のインポート ウィザードの完了]** ページで、目的のフォルダーに証明書があることを確認し、**[完了]** をクリックします。

    ![証明書が適切なフォルダーにあることを確認する](./media/backup-azure-backup-server-vmware/cert-wizard-final-screen.png)

    ダイアログ ボックスが表示され、証明書のインポートが正しく行われたことを示します。

11. vCenter Server にサインインして、接続がセキュリティで保護されていることを確認します。

  証明書のインポートが正しく行われず、セキュリティで保護された接続を確立できない場合は、VMware vSphere のドキュメントの[サーバー証明書の取得](http://pubs.vmware.com/vsphere-60/index.jsp#com.vmware.wssdk.dsg.doc/sdk_sg_server_certificate_Appendixes.6.4.html)に関する記事をご覧ください。

  組織内で境界をセキュリティで保護しており、HTTPS プロトコルを有効にしたくない場合は、次の手順でセキュリティで保護された通信を無効にします。

### <a name="disable-secure-communication-protocol"></a>セキュリティで保護された通信プロトコルを無効にする

HTTPS プロトコルを必要としない場合は、次の手順で HTTPS を無効にします。 既定の動作を無効にするには、既定の動作を無視するレジストリ キーを作成します。

1. 以下のテキストをコピーして、.txt ファイルに貼り付けます。

  ```
  Windows Registry Editor Version 5.00
  [HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Microsoft Data Protection Manager\VMWare]
  "IgnoreCertificateValidation"=dword:00000001
  ```

2. お使いの Azure Backup Server のコンピューターにファイルを保存します。 ファイル名は DisableSecureAuthentication.reg にします。

3. ファイルをダブルクリックして、レジストリ エントリをアクティブ化します。


## <a name="create-a-role-and-user-account-on-the-vcenter-server"></a>vCenter Server でのロールとユーザー アカウントの作成

vCenter Server では、定義済み権限セットがロールです。 ロールは vCenter Server の管理者が作成します。 管理者はロールをユーザー アカウントにペアリングすることで、アクセス許可を割り当てます。 vCenter Server のコンピューターのバックアップに必要なユーザー資格情報を作成するには、特定の権限を持つロールを作成し、ユーザー アカウントをそのロールに関連付けます。

Azure Backup Server では、ユーザー名とパスワードを使用して、vCenter Server による認証が行われます。 Azure Backup Server は、その資格情報を、すべてのバックアップ操作に対する認証として使用します。

バックアップ管理者の vCenter Server のロールと権限を追加するには:

1. VCenter Server にサインインし、vCenter Server の **[ナビゲーター]**パネルで **[管理]** をクリックします。

    ![VCenter Server の [ナビゲーター] パネルの [管理] オプション](./media/backup-azure-backup-server-vmware/vmware-navigator-panel.png)

2. **[管理]** で **[ロール]** を選択し、**[ロール]** パネルでロールの追加アイコン (+ 記号) をクリックします。

    ![ロールの追加](./media/backup-azure-backup-server-vmware/vmware-define-new-role.png)

    **[ロールの作成]** ダイアログ ボックスが表示されます。

    ![ロールを作成する](./media/backup-azure-backup-server-vmware/vmware-define-new-role-priv.png)

3. **[ロールの作成]** ダイアログ ボックスの **[ロール名]** ボックスに「*BackupAdminRole*」と入力します。 ロール名は任意の名前でかまいませんが、ロールの目的を識別できる名前である必要があります。

4. 適切なバージョンの vCenter の権限を選択し、**[OK]** をクリックします。 次の表に、vCenter 6.0 と vCenter 5.5 に必要な権限を示します。

  権限を選択するときは、親ラベルの横にあるアイコンをクリックして親を展開し、子権限を表示します。 VirtualMachine の権限を選択するには、親子階層を複数のレベルにわたって移動する必要があります。 親権限のすべての子権限を選択する必要はありません。

  ![権限の親子階層](./media/backup-azure-backup-server-vmware/cert-add-privilege-expand.png)

  **[OK]** をクリックすると、新しいロールが、[ロール] パネルの一覧に表示されます。

|vCenter 6.0 の権限| vCenter 5.5 の権限|
|--------------------------|---------------------------|
|Datastore.AllocateSpace   | Datastore.AllocateSpace|
|Global.ManageCustomFields | Global.ManageCustomerFields|
|Global.SetCustomFields    |   |
|Host.Local.CreateVM       | Network.Assign |
|Network.Assign            |  |
|Resource.AssignVMToPool   |  |
|VirtualMachine.Config.AddNewDisk  | VirtualMachine.Config.AddNewDisk   |
|VirtualMachine.Config.AdvanceConfig| VirtualMachine.Config.AdvancedConfig|
|VirtualMachine.Config.ChangeTracking| VirtualMachine.Config.ChangeTracking |
|VirtualMachine.Config.HostUSBDevice||
|VirtualMachine.Config.QueryUnownedFiles|    |
|VirtualMachine.Config.SwapPlacement| VirtualMachine.Config.SwapPlacement |
|VirtualMachine.Interact.PowerOff| VirtualMachine.Interact.PowerOff |
|VirtualMachine.Inventory.Create| VirtualMachine.Inventory.Create |
|VirtualMachine.Provisioning.DiskRandomAccess| |
|VirtualMachine.Provisioning.DiskRandomRead|VirtualMachine.Provisioning.DiskRandomRead |
|VirtualMachine.State.CreateSnapshot| VirtualMachine.State.CreateSnapshot|
|VirtualMachine.State.RemoveSnapshot|VirtualMachine.State.RemoveSnapshot |
</br>



## <a name="create-a-vcenter-server-user-account-and-permissions"></a>vCenter Server のユーザー アカウントとアクセス許可の作成

権限が付与されたロールを設定したら、ユーザー アカウントを作成します。 ユーザー アカウントにはパスワードと名前が含まれます。これが認証に使用する資格情報となります。

1. ユーザー アカウントを作成するには、vCenter Server の **[ナビゲーター]** パネルで、**[ユーザーとグループ]** をクリックします。

    ![[ユーザーとグループ] のオプション](./media/backup-azure-backup-server-vmware/vmware-userandgroup-panel.png)

    **[vCenter Users and Groups]\(vCenter のユーザーとグループ\)** パネルが表示されます。

    ![[vCenter Users and Groups]\(vCenter のユーザーとグループ\) パネル](./media/backup-azure-backup-server-vmware/usersandgroups.png)

2. **[vCenter Users and Groups]\(vCenter のユーザーとグループ\)** パネルで **[ユーザー]** タブを選択し、ユーザーの追加アイコン (+ 記号) をクリックします。

    **[新しいユーザー]** ダイアログ ボックスが表示されます。

3. **[新しいユーザー]** ダイアログ ボックスにユーザーの情報を追加して、**[OK]** をクリックします。 この手順では、ユーザー名は BackupAdmin です。

    ![[新しいユーザー] ダイアログ ボックス](./media/backup-azure-backup-server-vmware/vmware-new-user-account.png)

    一覧に新しいユーザー アカウントが表示されます。

4. ユーザー アカウントをロールに関連付けるには、**[ナビゲーター]** パネルで **[グローバル アクセス許可]** をクリックします。 **[グローバル アクセス許可]** パネルで **[管理]** タブを選択し、追加アイコン (+ 記号) をクリックします。

    ![[グローバル アクセス許可] パネル](./media/backup-azure-backup-server-vmware/vmware-add-new-perms.png)

    **[Global Permission Root - Add Permission]\(グローバル アクセス許可のルート - アクセス許可の追加\)** ダイアログ ボックスが表示されます。

5. **[Global Permissions Root - Add Permission]\(グローバル アクセス許可のルート - アクセス許可の追加\)** ダイアログ ボックスで、**[追加]** をクリックして、ユーザーまたはグループを選択します。

    ![ユーザーまたはグループの選択](./media/backup-azure-backup-server-vmware/vmware-add-new-global-perm.png)

    **[Select Users/Groups]\(ユーザー/グループの選択\)** ダイアログ ボックスが表示されます。

6. **[Select Users/Groups]\(ユーザー/グループの選択\)** ダイアログ ボックスで **[BackupAdmin]** を選択し、**[追加]** をクリックします。

    **[ユーザー]** では、ユーザー アカウントに *domain\username* の形式が使用されます。 別のドメインを使用する場合は、**[ドメイン]** の一覧からドメインを選択します。

    ![BackupAdmin ユーザーを追加する](./media/backup-azure-backup-server-vmware/vmware-assign-account-to-role.png)

    **[OK]** をクリックして、選択したユーザーを **[アクセス許可の追加]** ダイアログ ボックスに追加します。

7. ユーザーを特定したので、ユーザーをロールに割り当てます。 **[Assigned Role]\(割り当て済みロール\)** で、ドロップダウン リストから **[BackupAdminRole]** を選択し、**[OK]** をクリックします。

    ![ユーザーをロールに割り当てる](./media/backup-azure-backup-server-vmware/vmware-choose-role.png)

  **[グローバル アクセス許可]** パネルの **[管理]** タブの一覧に新しいユーザー アカウントと、関連付けられているロールが表示されます。


## <a name="establish-vcenter-server-credentials-on-azure-backup-server"></a>Azure Backup Server での vCenter Server 資格情報の作成

VMware サーバーを Azure Backup Server に追加する前に、[Update 1 for Azure Backup Server](https://support.microsoft.com/help/3175529/update-1-for-microsoft-azure-backup-server) をインストールします。

1. Azure Backup Server を開くには、Azure Backup Server デスクトップでアイコンをダブルクリックします。

    ![Azure Backup Server アイコン](./media/backup-azure-backup-server-vmware/mabs-icon.png)

    デスクトップにアイコンが見つからない場合は、インストールされているアプリの一覧から Azure Backup Server を開きます。 Azure Backup Server アプリ名は、Microsoft Azure Backup です。

2. Azure Backup Server コンソールで、**[管理]**、**[運用サーバー]** の順にクリックし、ツール リボンの **[VMware の管理]** をクリックします。

    ![Azure Backup Server コンソール](./media/backup-azure-backup-server-vmware/add-vmware-credentials.png)

    **[資格情報の管理]** ダイアログ ボックスが表示されます。

    ![Azure Backup Server の [資格情報の管理] ダイアログ ボックス](./media/backup-azure-backup-server-vmware/mabs-manage-credentials-dialog.png)

3. **[資格情報の管理]** ダイアログ ボックスで、**[追加]** をクリックして、**[資格情報の追加]** ダイアログ ボックスを開きます。

4. **[資格情報の追加]** ダイアログ ボックスで、新しい資格情報の名前と説明を入力します。 その後ユーザー名とパスワードを指定します。 *Contoso Vcenter credential* という名前は、次の手順で資格情報を識別するために使用します。 vCenter Server で使用しているユーザー名とパスワードと同じものを使用してください。 vCenter Server と Azure Backup Server が同じドメイン内にない場合は、**[ユーザー名]** でドメインを指定します。

    ![Azure Backup Server の [資格情報の追加] ダイアログ ボックス](./media/backup-azure-backup-server-vmware/mabs-add-credential-dialog2.png)

    **[追加]** をクリックして、新しい資格情報を Azure Backup Server に追加します。 **[資格情報の管理]** ダイアログ ボックスの一覧に新しい資格情報が表示されます。
    
    ![Azure Backup Server の [資格情報の管理] ダイアログ ボックス](./media/backup-azure-backup-server-vmware/new-list-of-mabs-creds.png)

5. **[資格情報の管理]** ダイアログ ボックスを閉じるには、右上隅にある **[X]** をクリックします。


## <a name="add-the-vcenter-server-to-azure-backup-server"></a>Azure Backup Server への vCenter Server の追加

vCenter Server を Azure Backup Server に追加するには、運用サーバーの追加ウィザードを使用します。

運用サーバーの追加ウィザードを開くには、次の手順を行います。

1. Azure Backup Server コンソールで、**[管理]**、**[運用サーバー]** の順にクリックし、**[追加]** をクリックします。

    ![運用サーバーの追加ウィザードを開く](./media/backup-azure-backup-server-vmware/add-vcenter-to-mabs.png)

    **[運用サーバーの追加ウィザード]** ダイアログ ボックスが表示されます。

    ![運用サーバーの追加ウィザード](./media/backup-azure-backup-server-vmware/production-server-add-wizard.png)

2. **[運用サーバーの種類を選択]** ページで、**[VMware サーバー]** を選択し、**[次へ]** をクリックします。

3. **[サーバー名/IP アドレス]** で、VMware サーバーの完全修飾ドメイン名 (FQDN) または IP アドレスを指定します。 すべての ESXi サーバーが同じ vCenter で管理されている場合は、その vCenter の名前を使用できます。

    ![VMware サーバーの FQDN または IP アドレスを指定する](./media/backup-azure-backup-server-vmware/add-vmware-server-provide-server-name.png)

4. **[SSL ポート]** で、VMware サーバーと通信するために使用するポートを入力します。 別のポートが必要なことがわかっている場合を除き、既定のポートであるポート 443 を使用します。

5. **[資格情報の指定]** で、前に作成した資格情報を選択します。

    ![資格情報を指定する](./media/backup-azure-backup-server-vmware/identify-creds.png)

6. **[追加]** をクリックして、VMware サーバーを **[追加された VMware サーバー]** の一覧に追加し、**[次へ]** をクリックして、ウィザードの次のページに移動します。

    ![VMWare サーバーと資格情報を追加する](./media/backup-azure-backup-server-vmware/add-vmware-server-credentials.png)

7. **[概要]** ページで、**[追加]** をクリックして、指定した VMware サーバーを Azure Backup Server に追加します。

    ![Azure Backup Server への VMware サーバーの追加](./media/backup-azure-backup-server-vmware/tasks-screen.png)

  VMware サーバーのバックアップはエージェントレスのバックアップであるため、新しいサーバーが即座に追加されます。 結果は **[完了]** ページに表示されます。

  ![[完了] ページ](./media/backup-azure-backup-server-vmware/summary-screen.png)

  vCenter Server の複数のインスタンスを Azure Backup Server に追加するには、このセクションの前述の手順を繰り返します。

vCenter Server を Azure Backup Server に追加したら、次は保護グループを作成します。 保護グループでは、短期または長期的なリテンション期間のさまざまな詳細を指定し、バックアップ ポリシーを定義および適用します。 バックアップ ポリシーでは、バックアップが実行される時間とバックアップ対象を設定するためのスケジュールです。


## <a name="configure-a-protection-group"></a>保護グループの構成

以前に System Center Data Protection Manager または Azure Backup Server を使用したことがない場合は、「[ディスク バックアップの計画](https://technet.microsoft.com/library/hh758026.aspx)」を参照して、ハードウェア環境を準備してください。 適切なストレージがあることを確認したら、新しい保護グループの作成ウィザードを使用して、VMware 仮想マシンを追加します。

1. Azure Backup Server コンソールで **[保護]** をクリックし、ツール リボンの **[新規]** をクリックして、新しい保護グループの作成ウィザードを開きます。

    ![新しい保護グループの作成ウィザードを開く](./media/backup-azure-backup-server-vmware/open-protection-wizard.png)

    **[新しい保護グループの作成ウィザード]** ダイアログ ボックスが表示されます。

    ![[新しい保護グループの作成ウィザード] ダイアログ ボックス](./media/backup-azure-backup-server-vmware/protection-wizard.png)

    **[次へ]** をクリックして、**[保護グループの種類の選択]** ページに進みます。

2. **[保護グループの種類の選択]** ページで、**[サーバー]** を選択し、**[次へ]** をクリックします。 **[グループ メンバーの選択]** ページが表示されます。

3. **[グループ メンバーの選択]** ページに、利用可能なメンバーと選択したメンバーが表示されます。 保護するメンバーを選択し、**[次へ]** をクリックします。

    ![グループ メンバーの選択](./media/backup-azure-backup-server-vmware/server-add-selected-members.png)

    メンバーを選択する際に、他のフォルダーや VM を含むフォルダーを選択すると、それらのフォルダーと VM も選択されます。 親フォルダー内のフォルダーと VM を含めることは、フォルダー レベルの保護と呼ばれます。 フォルダーや VM を削除するには、チェック ボックスをオフにします。

    VM または VM を含むフォルダーが既に Azure で保護されている場合、その VM をもう一度選択することはできません。 つまり、Azure で保護されている VM は再度保護することができません。これは、1 つの VM に対して復旧ポイントが重複して作成されないようにするためです。 Azure Backup Server インスタンスで既に保護されているメンバーを確認する場合は、メンバーの上にマウス ポインターを移動します。そうすると、保護サーバーの名前が表示されます。

4. **[データの保護方法の選択]** ページで、保護グループの名前を入力します。 (ディスクへの) 短期的な保護とオンライン保護が選択されています。 (Azure への) オンライン保護を使用する場合は、ディスクへの短期的な保護を使用する必要があります。 **[次へ]** をクリックして、短期的な保護の範囲に進みます。

    ![データ保護方法の選択](./media/backup-azure-backup-server-vmware/name-protection-group.png)

5. **[短期的な目標値の指定]** ページの **[保有期間の範囲]** で、"*ディスクに保存された*" 復旧ポイントを保持する日数を指定します。 復旧ポイントを作成する日時を変更する場合は、**[変更]** をクリックします。 短期的な復旧ポイントは、完全バックアップです。 増分バックアップではありません。 短期的な目標値を設定したら、**[次へ]** をクリックします。

    ![短期的な目標の指定](./media/backup-azure-backup-server-vmware/short-term-goals.png)

6. **[ディスク割り当ての確認]** ページで、VM のディスク領域を確認し、必要に応じて変更します。 推奨されるディスク割り当ては、**[短期的な目標値の指定]** ページで指定した保有期間の範囲、ワークロードの種類、および (手順 3 で特定された) 保護データのサイズに基づきます。  

  - **データ サイズ:** 保護グループ内のデータのサイズ。
  - **ディスク領域:** 保護グループの推奨ディスク領域の量。 この設定を変更する場合は、どのデータ ソースも拡大するので、予想よりも少し大きめに合計領域を割り当てる必要があります。
  - **データを併置:** 併置を有効にすると、保護対象の複数のデータ ソースを 1 つのレプリカと復旧ポイントのボリュームにマップできます。 すべてのワークロードで併置がサポートされているわけではありません。
  - **自動的に拡大:** この設定を有効にした場合、保護グループ内のデータが最初に割り当てたサイズよりも大きくなると、System Center Data Protection Manager がディスク サイズを 25% 増やそうとします。
  - **記憶域プールの詳細:** 合計や残りのディスク サイズなど、記憶域プールの状態を表示します。

    ![ディスクの割り当てを確認する](./media/backup-azure-backup-server-vmware/review-disk-allocation.png)

    ディスク領域の割り当てが終了したら、**[次へ]** をクリックします。

7. **[レプリカの作成方法の選択]** ページで、Azure Backup Server で保護されたデータの最初のコピー (レプリカ) を生成する方法を指定します。

    既定値は、**[自動 (ネットワーク経由)]** と **[今すぐ]** です。 既定値を使用する場合は、ピーク時以外を指定することをお勧めします。 **[時間指定]** を選択し、日時を指定します。

    データが大量にある場合や、ネットワークの状態が最適でない場合は、リムーバブル メディアを使用してオフラインでデータをレプリケートすることを検討してください。

    選択が終わったら、**[次へ]** をクリックします。

    ![レプリカ作成方法の選択](./media/backup-azure-backup-server-vmware/replica-creation.png)

8. **[整合性チェック オプション]** ページで、整合性チェックを自動化する方法とタイミングを選択します。 整合性チェックは、レプリカ データが不整合になったときに実行することや、設定したスケジュールで実行することができます。

    自動整合性チェックを構成しない場合は、チェックを手動で実行できます。 Azure Backup Server コンソールの保護領域で保護グループを右クリックし、**[整合性チェックの実行]** を選択します。

    **[次へ]** をクリックして、次のページに進みます。

9. **[オンライン保護するデータの指定]** ページで、保護するデータ ソースを 1 つ以上選択します。 メンバーを個別に選択するか、**[すべて選択]** をクリックしてすべてのメンバーを選択することができます。 メンバーを選択したら、**[次へ]** をクリックします。

    ![オンライン保護データの指定](./media/backup-azure-backup-server-vmware/select-data-to-protect.png)

10. **[オンライン バックアップ スケジュールの指定]** ページで、ディスク バックアップから復旧ポイントを生成するためのスケジュールを指定します。 復旧ポイントは生成されると、Azure の Recovery Services コンテナーに転送されます。 オンライン バックアップ スケジュールを設定したら、**[次へ]** をクリックします。

    ![オンライン バックアップ スケジュールの指定](./media/backup-azure-backup-server-vmware/online-backup-schedule.png)

11. **[オンライン保持ポリシーの指定]** ページで、Azure にバックアップ データを保持する期間を指定します。 ポリシーを定義したら、**[次へ]** をクリックします。

    ![オンライン保持ポリシーの指定](./media/backup-azure-backup-server-vmware/retention-policy.png)

    Azure にデータを保持できる期間に制限はありません。 Azure に復旧ポイント データを格納する際の唯一の制限は、保護するインスタンスあたりの復旧ポイントの数が 9999 までであることです。 この例では、保護するインスタンスは VMware サーバーです。

12. **[概要]** ページで、指定の保護グループのメンバーと設定の詳細を確認し、**[グループの作成]** をクリックします。

    ![保護グループのメンバーと設定の概要](./media/backup-azure-backup-server-vmware/protection-group-summary.png)

## <a name="next-steps"></a>次のステップ
Azure Backup Server を使用して、VMware ワークロードを保護する場合は、Azure Backup Server を使用した [Microsoft Exchange Server](./backup-azure-exchange-mabs.md)、[Microsoft SharePoint ファーム](./backup-azure-backup-sharepoint-mabs.md)、または [SQL Server データベース](./backup-azure-sql-mabs.md)の保護も参考にしてください。

エージェントの登録、保護グループの構成、およびジョブのバックアップに関する問題については、「[Azure Backup Server のトラブルシューティング](./backup-azure-mabs-troubleshoot.md)」をご覧ください。
