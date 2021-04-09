---
title: Azure Files Active Directory Domain Services を使用した FSLogix プロファイル コンテナーの作成 - Azure
description: この記事では、Azure Files と Azure Active Directory Domain Services を使用して FSLogix プロファイル コンテナーを作成する方法について説明します。
author: Heidilohr
ms.topic: how-to
ms.date: 04/10/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 70a56b7efc34ba2fd3c06521c6e4cac6ea28778f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "96302469"
---
# <a name="create-a-profile-container-with-azure-files-and-azure-ad-ds"></a>Azure Files および Azure AD DS を使用してプロファイル コンテナーを作成する

この記事では、Azure Files と Azure Active Directory Domain Services (AD DS) を使用して FSLogix プロファイル コンテナーを作成する方法について説明します。

## <a name="prerequisites"></a>前提条件

この記事では、Azure AD DS インスタンスが既に設定されているものと想定されています。 まだ設定されていない場合は、まず「[基本的なマネージド ドメインを作成する](../active-directory-domain-services/tutorial-create-instance.md)」の手順に従ってから、ここに戻ってください。

## <a name="add-azure-ad-ds-admins"></a>Azure AD DS 管理者を追加する

管理者をさらに追加するには、新しいユーザーを作成してアクセス許可を付与します。

管理者を追加するには以下の手順に従ってください。

1. サイドバーから **[Azure Active Directory]** 、 **[すべてのユーザー]** の順に選択し、 **[新しいユーザー]** を選択します。

2.  フィールドにユーザーの詳細を入力します。

3. 画面左側の [Azure Active Directory] ウィンドウで **[グループ]** を選択します。

4. **[AAD DC 管理者]** グループを選択します。

5. 左側のウィンドウで **[メンバー]** を選択し、メイン ウィンドウで **[メンバーの追加]** を選択します。 これにより、Azure AD で使用できるユーザー全員の一覧が表示されます。 作成したばかりのユーザー プロファイルの名前を選択します。

## <a name="set-up-an-azure-storage-account"></a>Azure Storage アカウントを設定する

次に、サーバー メッセージ ブロック (SMB) で Azure AD DS 認証を有効にします。

認証を有効にするには以下の手順に従ってください。

1. 汎用 v2 Azure Storage アカウントをまだ設定してデプロイしていない場合は、「[Azure Storage アカウントを作成する](../storage/common/storage-account-create.md)」の手順に従ってください。

2. アカウントの設定が完了したら、 **[リソースに移動]** を選択します。

3. 画面の左側にあるウィンドウで **[構成]** を選択し、メイン ウィンドウで **[Azure Files の Azure Active Directory 認証]** を有効にします。 終了したら、 **[保存]** を選択します。

4. 画面の左側にあるウィンドウで **[概要]** を選択し、メイン ウィンドウで **[ファイル]** を選択します。

5. **[ファイル共有]** を選択し、画面の右側に表示されるフィールドに **[名前]** と **[クォータ]** を入力します。

## <a name="assign-access-permissions-to-an-identity"></a>ID にアクセス許可を割り当てる

他のユーザーは、ファイル共有にアクセスするためのアクセス許可が必要になります。 これを行うには、適切なアクセス許可のあるロールを各ユーザーに割り当てる必要があります。

ユーザーにアクセス許可を割り当てるには以下の手順に従ってください。

1. Azure portal から、[[Azure Storage アカウントの設定]](#set-up-an-azure-storage-account) で作成したファイル共有を開きます。

2. **[アクセス制御 (IAM)]** を選択します。

3. **[ロールの割り当てを追加する]** を選択します。

4. **[ロールの割り当てを追加する]** タブで、ロールのリストから適切な組み込みロールを選択します。 適切なアクセス許可を取得するには、少なくとも、アカウントの **[ストレージ ファイル　データ SMB 共有共同作成者]** を選択する必要があります。

5. **[アクセスの割り当て先]** で、 **[Azure Active Directory のユーザー、グループ、サービス プリンシパル]** を選択します。

6. 対象となる Azure Active Directory ID の名前または電子メール アドレスを選択します。

7. **[保存]** を選択します。

## <a name="get-the-storage-account-access-key"></a>ストレージ アカウントのアクセス キーを取得する

次にストレージ アカウントのアクセス キーを取得する必要があります。

ストレージ アカウントのアクセス キーを取得するには以下の手順に従ってください。

1. Azure portal のサイドバーで **[ストレージ アカウント]** を選択します。

2. ストレージ アカウントのリストから、Azure AD DS を有効にし、上記の手順でカスタム ロールを作成したアカウントを選択します。

3. **[設定]** で **[アクセス キー]** を選択し、**key1** からキーをコピーします。

4. **[仮想マシン]** タブに移動し、ホスト プールの一部になる VM を見つけます。

5. **[仮想マシン (adVM)]** で仮想マシン (VM) の名前を選択し、 **[接続]** を選択します

    これによって RDP ファイルがダウンロードされ、独自の資格情報を使用して VM にサインします。

    > [!div class="mx-imgBorder"]
    > ![[仮想マシンへの接続] ウィンドウの [RDP] タブのスクリーンショット。](media/rdp-tab.png)

6. VM にサインインしたら、管理者としてコマンド プロンプトを実行します。

7. 次のコマンドを実行します。

     ```cmd
     net use <desired-drive-letter>: \\<storage-account-name>.file.core.windows.net\<share-name> /user:Azure\<storage-account-name> <storage-account-key>
     ```

    - `<desired-drive-letter>` を、選択したドライブ文字 (`y:` など) に置き換えます。
    - `<storage-account-name>` のすべてのインスタンスを、以前に指定したストレージ アカウントの名前に置き換えます。
    - `<share-name>` を、以前に作成した共有の名前に置き換えます。
    - `<storage-account-key>` を、Azure からのストレージ アカウント キーに置き換えます。

    次に例を示します。

     ```cmd
     net use y: \\fsprofile.file.core.windows.net\share HDZQRoFP2BBmoYQ=(truncated)= /user:Azure\fsprofile)
     ```

8. Windows Virtual Desktop ユーザーが自分のプロファイル コンテナーを作成できるようにしつつ、作成者以外はそのプロファイル コンテナーにアクセスできないようにするために、次のコマンドを実行します。

     ```cmd
     icacls <mounted-drive-letter>: /grant <user-email>:(M)
     icacls <mounted-drive-letter>: /grant "Creator Owner":(OI)(CI)(IO)(M)
     icacls <mounted-drive-letter>: /remove "Authenticated Users"
     icacls <mounted-drive-letter>: /remove "Builtin\Users"
     ```

    - `<mounted-drive-letter>` の部分は、ドライブのマップに使用したドライブ文字に置き換えます。
    - `<user-email>` の部分は、共有にアクセスする必要があるユーザーか、そのユーザーが含まれる Active Directory グループの UPN に置き換えます。

    次に例を示します。

     ```cmd
     icacls <mounted-drive-letter>: /grant john.doe@contoso.com:(M)
     icacls <mounted-drive-letter>: /grant "Creator Owner":(OI)(CI)(IO)(M)
     icacls <mounted-drive-letter>: /remove "Authenticated Users"
     icacls <mounted-drive-letter>: /remove "Builtin\Users"
     ```

## <a name="create-a-profile-container"></a>プロファイル コンテナーを作成する

これでプロファイルの準備ができたので、FSLogix プロファイル コンテナーを作成しましょう。

FSLogix プロファイル コンテナーを構成するには以下の手順に従ってください。

1. この記事の冒頭で構成したセッション ホスト VM にサインインし、[FSLogix エージェントをダウンロードしてインストール](/fslogix/install-ht/)します。

2. ダウンロードした FSLogix エージェント ファイルを解凍し、 **[x64]**  >  **[リリース]** にアクセスして、**FSLogixAppsSetup.exe** を開きます。

3. インストーラーが起動したら、 **[ライセンス条項と条件に同意します]** を選択します。 適宜、新しいキーを指定します。

4. **[インストール]** を選択します。

5. **ドライブ C** を開き、 **[プログラム ファイル]**  >  **[FSLogix]**  >  **[アプリ]** に移動し、FSLogix エージェントが適切にインストールされていることを確認します。

     >[!NOTE]
     > ホスト プールに複数の VM がある場合は、VM ごとに手順 1 から手順 5 を繰り返す必要があります。

6. 管理者として **[レジストリ エディター]** (RegEdit) を実行します。

7. **[コンピューター]**  >  **[HKEY_LOCAL_MACHINE]**  >  **[ソフトウェア]**  >  **[FSLogix]** とナビゲートして **[FSLogix]** を右クリックし、 **[新規]** を選択してから **[キー]** を選びます。

8. 「**Profiles**」という名前の新しいキーを作成します。

9.  **[Profiles]** を右クリックし、 **[新規]** を選択してから **[DWORD (32 ビット) 値]** を選択します。 値に「**Enabled**」という名前を付け、 **[データ]** 値を「**1**」に設定します。

    > [!div class="mx-imgBorder"]
    > ![[Profiles] キーのスクリーンショット。 REG_DWORD ファイルが強調表示され、そのデータ値は「1」に設定されています。](media/dword-value.png)

10. **[Profiles]** を右クリックし、 **[新規]** を選択してから **[複数行文字列値]** を選択します。 値に「**VHDLocations**」という名前を付け、Azure Files 共有の URI (`\\fsprofile.file.core.windows.net\share`) をデータ値として設定し入力します。

    > [!div class="mx-imgBorder"]
    > ![VHDLocations ファイルを示す Profiles キーのスクリーンショット。 そのデータ値には、Azure Files 共有の URI が示されています。](media/multi-string-value.png)

## <a name="assign-users-to-a-session-host"></a>セッション ホストにユーザーを割り当てる

次にユーザーをセッション ホストに割り当てる必要があります。

ユーザーを割り当てるには以下の手順に従ってください。

1. 管理者として Windows PowerShell を実行した後、以下のコマンドレットを実行して PowerShell を使い、Windows Virtual Desktop にサインインします。

   ```powershell
   Import-Module Microsoft.RdInfra.RdPowershell

   #Optional
   Install-Module Microsoft.RdInfra.RdPowershell

   $brokerurl = "https://rdbroker.wvd.microsoft.com"

   Add-RdsAccount -DeploymentUrl $brokerurl
   ```

   資格情報を求められたら、Windows Virtual Desktop テナントのテナント作成者、RDS 所有者、または RDS 共同作成者のロールが与えられているユーザーを入力します。

2. 次のコマンドレットを実行して、リモート デスクトップ グループにユーザーを割り当てます。

     ```powershell
     $tenant = "<your-wvd-tenant>"

     $pool1 = "<wvd-pool>"

     $appgroup = "Desktop Application Group"

     $user1 = "<user-principal>"

     Add-RdsAppGroupUser $tenant $pool1 $appgroup $user1
     ```

    前のコマンドレットと同様に、`<your-wvd-tenant>`、`<wvd-pool>`、`<user-principal>` を関連値に置き換えてください。

    次に例を示します。

     ```powershell
     $pool1 = "contoso"

     $tenant = "contoso"

     $appgroup = "Desktop Application Group"

     $user1 = "jane.doe@contoso.com"

     Add-RdsAppGroupUser $tenant $pool1 $appgroup $user1
     ```

## <a name="make-sure-your-profile-works"></a>プロファイルが機能することを確認する

ここで必要なのは、作成したプロファイルが存在し、意図したとおりに動作することを確認することです。

プロファイルを確認するには以下の手順に従ってください。

1. ブラウザーを開き、[Windows Virtual Desktop の Web クライアント](https://rdweb.wvd.microsoft.com/arm/webclient)に移動します。

2. リモート デスクトップ グループに割り当てられたユーザー アカウントを使用してサインインします。

3. ユーザー セッションを確立したら、Azure portal を開き、管理者アカウントを使用してサインインします。

4. サイドバーで **[ストレージ　アカウント]** を選択します。

5. セッション ホスト プールのファイル共有として構成し、Azure AD DS を使用して有効にしたストレージ アカウントを選択します。

6. **[ファイル]** アイコンを選択し、共有を展開します。

    すべてが適正に設定されたら、`<user SID>-<username>` という形式の名前を含む **ディレクトリ** が表示されるはずです。

## <a name="next-steps"></a>次のステップ

FSLogix プロファイル コンテナーを作成する別の方法を探している場合は次の記事をご覧ください。

- [ファイル共有を使用してホスト プール用のプロファイル コンテナーを作成](create-host-pools-user-profile.md)します。
- [Azure NetApp Files を使用してホスト プール用の FSLogix プロファイル コンテナーを作成する](create-fslogix-profile-container.md)

Azure Files の FSlogix コンテナーに関連のある概念の詳細な情報については、「[FSLogix プロファイル コンテナーと Azure Files](fslogix-containers-azure-files.md)」をご覧ください。
