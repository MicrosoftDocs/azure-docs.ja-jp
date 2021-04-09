---
title: ドメイン コントローラーを使用して Azure Files のファイル共有を作成する - Azure
description: Active Directory ドメインが設定された既存の Windows Virtual Desktop ホスト プールにある Azure ファイル共有上に、FSLogix プロファイル コンテナーを設定します。
author: Heidilohr
ms.topic: how-to
ms.date: 06/05/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 8f8086aced26fc46fb1430df074082e8c3365baa
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "92746814"
---
# <a name="create-a-profile-container-with-azure-files-and-ad-ds"></a>Azure Files および AD DS を使用してプロファイル コンテナーを作成する

この記事では、既存の Windows Virtual Desktop ホスト プール上のドメイン コントローラーによって認証された Azure ファイル共有を作成する方法について説明します。 このファイル共有は、ストレージのプロファイルの格納に利用できます。

このプロセスでは、オンプレミスのディレクトリ サービスである Active Directory Domain Services (AD DS) を使用します。 Azure AD DS を使った FSLogix プロファイル コンテナーの作成方法の詳細については、[Azure Files を使った FSLogix プロファイル コンテナーの作成](create-profile-container-adds.md)に関するページを参照してください。

## <a name="prerequisites"></a>前提条件

作業を開始する前に、ドメイン コントローラーが Azure に同期されており、かつ、セッション ホストを接続している Azure 仮想ネットワーク (VNET) から解決できることを確認してください。

## <a name="set-up-a-storage-account"></a>ストレージ アカウントを設定する

最初に、Azure Files のストレージ アカウントを設定する必要があります。

ストレージ アカウントを設定するには:

1. Azure portal にサインインします。

2. 検索バーで「**ストレージ アカウント**」を検索します。

3. **[+追加]** を選択します。

4. **[ストレージ アカウントの作成]** ページで、次の情報を入力します。

    - 新しいリソース グループを作成します。
    - ストレージ アカウント用に一意の名前を入力します。
    - **[場所]** については、Windows Virtual Desktop ホスト プールと同じ場所を選択することをお勧めします。
    - **[パフォーマンス]** には **[Standard]** を選択します (IOPS の要件によって異なります。 詳細については、「[Windows Virtual Desktop の FSLogix プロファイル コンテナーのストレージ オプション](store-fslogix-profile.md)」を参照してください)。
    - **[アカウントの種類]** については、 **[StorageV2]** または **[FileStorage]** を選択します (パフォーマンス レベルが Premium の場合のみ有効です)。
    - **[レプリケーション]** には **[ローカル冗長ストレージ (LRS)]** を選択します。

5. 完了したら、 **[確認と作成]** 、 **[作成]** の順に選択します。

さらに詳しい構成手順が必要な場合には、「[利用可能なリージョン](../storage/files/storage-files-identity-auth-active-directory-enable.md#regional-availability)」を参照してください。

## <a name="create-an-azure-file-share"></a>Azure ファイル共有を作成する

次は、Azure ファイル共有を作成する必要があります。

ファイル共有を作成するには:

1. **[リソースに移動]** を選択します。

2. [概要] ページで **[ファイル共有]** を選択します。

3. **[+ ファイル共有]** を選択し、**profiles** という名前の新しいファイル共有を作成したら、適切なクォータを入力するか、フィールドを空のまま (クォータなし) にしておきます。

4. **［作成］** を選択します

## <a name="enable-active-directory-authentication"></a>Active Directory 認証を有効にする

次は、Active Directory (AD) 認証を有効にする必要があります。 このポリシーを有効にするには、既にドメインに参加済みのマシン上で、このセクションの手順に従う必要があります。 認証を有効にするには、ドメイン コントローラーが稼働している VM で次の手順に従います。

1. ドメイン参加済みの VM に、リモート デスクトップ プロトコルを使って接続します。

2. [Azure ファイル共有に AD DS 認証を有効にする](../storage/files/storage-files-identity-ad-ds-enable.md)方法を説明したページの手順に従い、AzFilesHybrid モジュールをインストールして認証を有効にします。

3.  Azure portal を開き、ストレージ アカウントを開いて **[構成]** を選択したら、 **[Active Directory (AD)]** が **[有効]** に設定されていることを確認します。

     > [!div class="mx-imgBorder"]
     > ![[構成] ページで Azure Active Directory (AD) が有効になった状態のスクリーンショット。](media/active-directory-enabled.png)

## <a name="assign-azure-rbac-permissions-to-windows-virtual-desktop-users"></a>Windows Virtual Desktop のユーザーに Azure RBAC のアクセス許可を割り当てる

FSLogix プロファイルをストレージ アカウントに保存する必要があるユーザー全員に、記憶域ファイル データの SMB 共有の共同作成者ロールを割り当てる必要があります。

Windows Virtual Desktop セッション ホストにサインインするユーザーには、ファイル共有にアクセスするためのアクセス許可が必要です。 Azure ファイル共有に対するアクセス権を付与する際には、従来の Windows 共有の場合と同じように、共有レベルと NTFS レベルの両方でアクセス許可を構成する必要があります。

共有レベルのアクセス許可を構成するには、適切なアクセス許可が設定されたロールを各ユーザーに割り当てます。 アクセス許可は、個々のユーザーまたは Azure AD グループに割り当てることができます。 詳細については、[ID にアクセス許可を割り当てる](../storage/files/storage-files-identity-ad-ds-assign-permissions.md)方法に関するページを参照してください。

>[!NOTE]
>アクセス許可を割り当てるアカウントまたはグループは、ドメイン内に作成したもので、かつ、Azure AD と同期している必要があります。 Azure AD に作成したアカウントは利用できません。

Azure ロールベースのアクセス制御 (Azure RBAC) のアクセス許可を割り当てるには:

1. Azure portal を開きます。

2. 「[ストレージ アカウントを設定する](#set-up-a-storage-account)」で作成したストレージ アカウントを開きます。

3. **[ファイル共有]** を選択し、使用する予定のファイル共有の名前を選択します。

4. **[アクセス制御 (IAM)]** を選択します。

5. **[ロールの割り当てを追加する]** を選択します。

6. **[ロールの割り当ての追加]** タブで、管理者アカウントについて **[ストレージ ファイル データの SMB 共有の管理者特権の共同作成者]** を選択します。

     ユーザーに FSLogix プロファイルに対するアクセス許可を割り当てる場合も、同じ手順を使用します。 ただし、手順 5 では代わりに **[記憶域ファイル データの SMB 共有の共同作成者]** を選択してください。

7. **[保存]** を選択します。

## <a name="assign-users-permissions-on-the-azure-file-share"></a>ユーザーに Azure ファイル共有に対するアクセス許可を割り当てる

ユーザーに Azure RBAC のアクセス許可を割り当てたら、次は NTFS アクセス許可を構成する必要があります。

作業を開始するには、Azure portal で次の 2 つを把握する必要があります。

- UNC パス。
- ストレージ アカウント キー。

### <a name="get-the-unc-path"></a>UNC パスを取得する

UNC パスの取得方法は次のとおりです。

1. Azure portal を開きます。

2. 「[ストレージ アカウントを設定する](#set-up-a-storage-account)」で作成したストレージ アカウントを開きます。

3. **[設定]** 、 **[プロパティ]** の順に選択します。

4. **[プライマリ ファイル サービス エンドポイント]** の URI を、任意のテキスト エディターにコピーします。

5. URI をコピーしたら、次の操作を行って UNC に変更します。

    - `https://` を削除し、`\\` に置き換えます。
    - スラッシュ `/` をバック スラッシュ `\` で置き換えます。
    - 「[Azure ファイル共有を作成する](#create-an-azure-file-share)」で作成したファイル共有の名前を、UNC の末尾に追加します。

        例: `\\customdomain.file.core.windows.net\<fileshare-name>`

### <a name="get-the-storage-account-key"></a>ストレージ アカウント キーを取得する

ストレージ アカウント キーを取得するには:

1. Azure portal を開きます。

2. 「[ストレージ アカウントを設定する](#set-up-a-storage-account)」で作成したストレージ アカウントを開きます。

3. **[ストレージ アカウント]** タブで **[アクセス キー]** を選択します。

4. **[key1]** または **[key2]** を、ローカル マシンのファイルにコピーします。

### <a name="configure-ntfs-permissions"></a>NTFS のアクセス許可を構成する

NTFS のアクセス許可を構成するには:

1. ドメイン参加済みの VM 上で、コマンド プロンプトを開きます。

2. Azure ファイル共有をマウントし、ドライブ文字を割り当てるために、次のコマンドを実行します。

     ```cmd
     net use <desired-drive-letter>: <UNC-path> <SA-key> /user:Azure\<SA-name>
     ```

3. 次のコマンドを実行し、Azure ファイル共有へのアクセス許可を確認します。

    ```cmd
    icacls <mounted-drive-letter>:
    ```

    `<mounted-drive-letter>` は、自分が割り当てたドライブ文字に置き換えます。

    *NT Authority\Authenticated Users* と *BUILTIN\Users* はどちらも、既定で一定のアクセス許可が割り当てられています。 これらのユーザーはその既定のアクセス許可により、他のユーザーのプロファイル コンテナーを読み取ることができます。 これに対して、「[プロファイル コンテナーと Office コンテナーで使用するストレージ アクセス許可を構成する](/fslogix/fslogix-storage-config-ht)」に記載されているアクセス許可では、ユーザーが他のユーザーのプロファイル コンテナーの中を見ることができません。

4. Windows Virtual Desktop ユーザーが自分のプロファイル コンテナーを作成できるようにしつつ、作成者以外はそのプロファイル コンテナーにアクセスできないようにするために、次のコマンドを実行します。

     ```cmd
     icacls <mounted-drive-letter>: /grant <user-email>:(M)
     icacls <mounted-drive-letter>: /grant "Creator Owner":(OI)(CI)(IO)(M)
     icacls <mounted-drive-letter>: /remove "Authenticated Users"
     icacls <mounted-drive-letter>: /remove "Builtin\Users"
     ```

     - <mounted-drive-letter> の部分は、ドライブのマップに使用したドライブ文字に置き換えます。
     - <user-email> の部分は、共有にアクセスする必要があるユーザーか、そのユーザーが含まれる Active Directory グループの UPN に置き換えます。

     次に例を示します。

     ```cmd
     icacls <mounted-drive-letter>: /grant john.doe@contoso.com:(M)
     icacls <mounted-drive-letter>: /grant "Creator Owner":(OI)(CI)(IO)(M)
     icacls <mounted-drive-letter>: /remove "Authenticated Users"
     icacls <mounted-drive-letter>: /remove "Builtin\Users"
     ```

## <a name="configure-fslogix-on-session-host-vms"></a>セッション ホスト VM に FSLogix を構成する

このセクションでは、VM に FSLogix を構成する方法を説明します。 ここに挙げた手順は、セッション ホストを構成するたびに必要になります。 構成を始める前に、「[FSLogix のダウンロードとインストール](/fslogix/install-ht)」の手順に従ってください。 すべてのセッション ホストでレジストリ キーが設定されるようにするために、いくつかのオプションが用意されています。 イメージ内でそれらのオプションを設定するか、グループ ポリシーを構成することができます。

セッション ホスト VM に FSLogix を構成するには:

1. RDP を使用して、Windows Virtual Desktop ホスト プールのセッション ホスト VM に接続します。

2. [FSLogix をダウンロードし、インストールします](/fslogix/install-ht)。

5. 「[プロファイル コンテナーのレジストリ設定を構成する](/fslogix/configure-profile-container-tutorial#configure-profile-container-registry-settings)」の手順に従います。

    - **[コンピューター]**  > **HKEY_LOCAL_MACHINE** > **SOFTWARE** > **FSLogix** の順に移動します。

    - **[Profiles]** キーを作成します。

    - **[Enabled]** (DWORD、値は 1) を作成します。

    - **[VHDLocations]** (MULTI_SZ) を作成します。

    - **[VHDLocations]** の値は、「[UNC パスを取得する](#get-the-unc-path)」で生成した UNC パスに設定します。

6. VM を再起動します。

## <a name="testing"></a>テスト

FSLogix のインストールと構成が完了したら、ホスト プール上のアプリ グループまたはデスクトップが割り当てられているユーザー アカウントにサインインすることによって、デプロイをテストできます。 サインインするユーザー アカウントに、ファイル共有に対するアクセス許可が付与されていることを確認してください。

ユーザーが以前にサインインしたことがある場合には、このセッション中に使用するローカル プロファイルが既に存在します。 ローカル プロファイルを作成しないようにするには、テスト用に新しいユーザー アカウントを作成するか、「[チュートリアル: ユーザー プロファイルをリダイレクトするようにプロファイル コンテナーを構成する](/fslogix/configure-profile-container-tutorial/)」に記載されている構成方法を使用してください。

セッションに対するアクセス許可を確認するには:

1. Windows Virtual Desktop 上でセッションを開始します。

2. Azure portal を開きます。

3. 「[ストレージ アカウントを設定する](#set-up-a-storage-account)」で作成したストレージ アカウントを開きます。

4. [Create an Azure file share]\(Azure ファイル共有の作成\) ページで **[共有の作成]** を選択します。

5. ユーザー プロファイルが入ったフォルダーがファイルに存在することを確認します。

追加のテストについては、「[プロファイルが機能することを確認する](create-profile-container-adds.md#make-sure-your-profile-works)」の手順に従ってください。

## <a name="next-steps"></a>次のステップ

FSLogix のトラブルシューティングについては、[こちらのトラブルシューティング ガイド](/fslogix/fslogix-trouble-shooting-ht)を参照してください。
