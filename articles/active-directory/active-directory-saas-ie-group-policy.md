---
title: "GPO を使用して IE 用 Azure アクセス パネル拡張機能をデプロイする | Microsoft Docs"
description: "グループ ポリシーを使用してマイ アプリ ポータル用の Internet Explorer アドオンをデプロイする方法。"
services: active-directory
documentationcenter: 
author: MarkusVi
manager: femila
ms.assetid: 7c2d49c8-5be0-4e7e-abac-332f9dfda736
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 02/27/2017
ms.author: markvi
ms.custom: H1Hack27Feb2017
translationtype: Human Translation
ms.sourcegitcommit: c06c089fb08c19b55246122201c378917a560e14
ms.openlocfilehash: af36f45e66b68e2e76651eb408682f36ee0cbb68
ms.lasthandoff: 03/01/2017


---
# <a name="how-to-deploy-the-access-panel-extension-for-internet-explorer-using-group-policy"></a>グループ ポリシーを使用して Internet Explorer 用アクセス パネル拡張機能をデプロイする方法
このチュートリアルでは、グループ ポリシーを使用して Internet Explorer 用のアクセス パネル拡張機能をユーザーのコンピューターにリモートでインストールする方法を示します。 この拡張機能は、 [パスワード ベースのシングル サインオン](active-directory-appssoaccess-whatis.md#password-based-single-sign-on)を使用するように構成されているアプリにサインインする必要がある Internet Explorer ユーザーに必要なものです。

管理者にはこの拡張機能のデプロイメントを自動化することをお勧めします。 自動化しないと、ユーザーは自分で拡張機能をダウンロードしてインストールする必要があるので、ユーザー エラーが発生しやすく、管理者のアクセス許可が必要になります。 このチュートリアルでは、グループ ポリシーを使用してソフトウェアのデプロイメントを自動化する方法について説明します。 [グループ ポリシーの詳細についてはこちらを参照してください。](https://technet.microsoft.com/windowsserver/bb310732.aspx)

アクセス パネルの拡張機能は [Chrome](https://go.microsoft.com/fwLink/?LinkID=311859) および [Firefox](https://go.microsoft.com/fwLink/?LinkID=626998) でも使用でき、どちらの場合もインストールに管理者のアクセス許可は必要ありません。

## <a name="prerequisites"></a>前提条件
* [Active Directory ドメイン サービス](https://msdn.microsoft.com/library/aa362244%28v=vs.85%29.aspx)を設定し、ユーザーのコンピューターをドメインに参加させている必要があります。
* グループ ポリシー オブジェクト (GPO) を編集するには、"設定の編集" アクセス許可が必要です。 既定では、Domain Administrators、Enterprise Administrators、Group Policy Creator Owners の各セキュリティ グループ メンバーにはこのアクセス許可があります。 [詳細情報。](https://technet.microsoft.com/library/cc781991%28v=ws.10%29.aspx)

## <a name="step-1-create-the-distribution-point"></a>手順 1: 配布ポイントを作成する
最初に、拡張機能をリモートでインストールするコンピューターがアクセスできるネットワークの場所に、インストーラー パッケージを置く必要があります。 そのためには、次の手順に従います。

1. サーバーに管理者としてログオンします。
2. **[サーバー マネージャー]** ウィンドウで、**[ファイルおよび記憶域サービス]** に移動します。
   
    ![[ファイル サービスおよびストレージ サービス] を開く](./media/active-directory-saas-ie-group-policy/files-services.png)
3. **[共有]** タブに移動します。 **[タスク]**  >  **[新しい共有...]** の順にクリックします
   
    ![[ファイル サービスおよびストレージ サービス] を開く](./media/active-directory-saas-ie-group-policy/shares.png)
4. **[新しい共有ウィザード]** を完了して、ユーザーのコンピューターからアクセスできるようにアクセス許可を設定します。 [共有の詳細についてはこちらを参照してください。](https://technet.microsoft.com/library/cc753175.aspx)
5. 次の Microsoft Windows インストーラー パッケージ (.msi ファイル) をダウンロードします。[[Access Panel Extension.msi]](https://account.activedirectory.windowsazure.com/Applications/Installers/x64/Access Panel Extension.msi)
6. インストーラー パッケージを共有上の目的の場所にコピーします。
   
    ![.msi ファイルを共有にコピーする。](./media/active-directory-saas-ie-group-policy/copy-package.png)
7. クライアント コンピューターが共有のインストーラー パッケージにアクセスできることを確認します。 

## <a name="step-2-create-the-group-policy-object"></a>手順 2: グループ ポリシー オブジェクトを作成する
1. Active Directory ドメイン サービス (AD DS) のインストールをホストしているサーバーにログオンします。
2. サーバー マネージャーで、**[ツール]** > **[グループ ポリシーの管理]** に移動します。
   
    ![[ツール] > [グループ ポリシーの管理] に移動する](./media/active-directory-saas-ie-group-policy/tools-gpm.png)
3. **[グループ ポリシーの管理** ] の左側のウィンドウで組織単位 (OU) 階層を表示し、グループ ポリシーを適用するスコープを決定します。 たとえば、テストのために数ユーザーにデプロイする場合は小さい OU を選択し、組織全体にデプロイする場合は最上位レベルの OU を選択します。
   
   > [!NOTE]
   > 組織単位 (OU) を作成または編集する場合は、サーバー マネージャーに戻り、**[ツール]** > **[Active Directory ユーザーとコンピューター]** に移動します。
   > 
   > 
4. OU を選択して右クリックし、**[このドメインに GPO を作成し、このコンテナーにリンクする...]** を選択します
   
    ![新しい GPO を作成する](./media/active-directory-saas-ie-group-policy/create-gpo.png)
5. **[新しい GPO]** プロンプトで、新しいグループ ポリシー オブジェクトの名前を入力します。
   
    ![新しい GPO の名前を設定する](./media/active-directory-saas-ie-group-policy/name-gpo.png)
6. 作成したグループ ポリシー オブジェクトを右クリックし、**[編集]** を選択します。
   
    ![新しい GPO を編集する](./media/active-directory-saas-ie-group-policy/edit-gpo.png)

## <a name="step-3-assign-the-installation-package"></a>手順 3: インストール パッケージを割り当てる
1. **[コンピューターの構成]** と **[ユーザーの構成]** のどちらを基にして拡張機能をデプロイするかを決めます。 [コンピューターの構成](https://technet.microsoft.com/library/cc736413%28v=ws.10%29.aspx)を使用すると、コンピューターにログオンしているユーザーに関係なく、拡張機能はコンピューターにインストールされます。 [ユーザーの構成](https://technet.microsoft.com/library/cc781953%28v=ws.10%29.aspx)では、ユーザーがログオンしているコンピューターに関係なく、拡張機能はユーザーに対してインストールされます。
2. **[グループ ポリシー管理エディター]** の左側のウィンドウで、選択した構成の種類に応じて、次のどちらかのフォルダー パスに移動します。
   
   * `Computer Configuration/Policies/Software Settings/`
   * `User Configuration/Policies/Software Settings/`
3. **[ソフトウェアのインストール]** を右クリックして、**[新規]**  >  **[パッケージ...]** の順に選択します
   
    ![新しいソフトウェア インストール パッケージを作成する](./media/active-directory-saas-ie-group-policy/new-package.png)
4. 「 [手順 1: 配布ポイントを作成する](#step-1-create-the-distribution-point)」でインストーラー パッケージをダウンロードした共有フォルダーに移動し、.msi ファイルを選択して、 **[開く]**をクリックします。
   
   > [!IMPORTANT]
   > 共有が同じサーバー上に存在する場合は、ローカル ファイル パスではなく、ネットワーク ファイル パスから .msi にアクセスしていることを確認します。
   > 
   > 
   
    ![共有フォルダーからインストール パッケージを選択する](./media/active-directory-saas-ie-group-policy/select-package.png)
5. **[ソフトウェアの展開]** プロンプトで、デプロイ方法として **[割り当て]** を選択します。 次に、 **[OK]**をクリックします
   
    ![[割り当て] を選択し、[OK] をクリックする](./media/active-directory-saas-ie-group-policy/deployment-method.png)

選択した OU に拡張機能がデプロイされます。 [グループ ポリシー ソフトウェアのインストールの詳細についてはこちらを参照してください。](https://technet.microsoft.com/library/cc738858%28v=ws.10%29.aspx)

## <a name="step-4-auto-enable-the-extension-for-internet-explorer"></a>手順 4: Internet Explorer の拡張機能を自動的に有効にする
Internet Explorer のすべての拡張機能は、インストーラーを実行するだけでなく、明示的に有効にしてからでないと使用できません。 グループ ポリシーを使用してアクセス パネル拡張機能を有効にするのには、次の手順に従います。

1. **[グループ ポリシー管理エディター]** ウィンドウで、「 [手順 3: インストール パッケージを割り当てる](#step-3-assign-the-installation-package)」で選んだ構成の種類に応じて、次のどちらかのパスに移動します。
   
   * `Computer Configuration/Policies/Administrative Templates/Windows Components/Internet Explorer/Security Features/Add-on Management`
   * `User Configuration/Policies/Administrative Templates/Windows Components/Internet Explorer/Security Features/Add-on Management`
2. **[アドオンの一覧]** を右クリックし、**[編集]** を選択します。
    ![アドオンの一覧を編集します。](./media/active-directory-saas-ie-group-policy/edit-add-on-list.png)
3. **[アドオンの一覧]** ウィンドウで、**[有効]** を選択します。 次に、**[オプション]** セクションで **[表示...]** をクリックします。
   
    ![[有効] をクリックし、[表示...] をクリックする](./media/active-directory-saas-ie-group-policy/edit-add-on-list-window.png)
4. **[表示するコンテンツ]** ウィンドウで、次の手順を実行します。
   
   1. 1 列目の **[値の名前]** フィールドには、次のクラス ID をコピーして貼り付けます。`{030E9A3F-7B18-4122-9A60-B87235E4F59E}`
   2. 2 列目の **[値]** フィールドには、次の値を入力します。`1`
   3. **[OK]** をクリックして **[表示するコンテンツ]** ウィンドウを閉じます。
      
      ![上で指定した値を入力する](./media/active-directory-saas-ie-group-policy/show-contents.png)
5. **[OK]** をクリックして変更を適用し、**[アドオンの一覧]** ウィンドウを閉じます。

選択した OU のコンピューターで拡張機能が有効になります。 [グループ ポリシーを使用して Internet Explorer のアドオンを有効または無効にする方法の詳細については、こちらを参照してください。](https://technet.microsoft.com/library/dn454941.aspx)

## <a name="step-5-optional-disable-remember-password-prompt"></a>手順 5 (省略可能): パスワードを保存するかどうかを確認するプロンプトを無効にする
ユーザーがアクセス パネル拡張機能を使用してサインインするときに、Internet Explorer でパスワードを保存するかどうかを確認する次のプロンプトが表示されることがあります。

![](./media/active-directory-saas-ie-group-policy/remember-password-prompt.png)

ユーザーに対してこのメッセージが表示されないようにする場合は、次の手順に従って、パスワードの保存によるオートコンプリートを禁止します。

1. **グループ ポリシー管理エディター** ウィンドウで、次のパスに移動します。 この構成設定は **[ユーザー構成]** でのみ利用できます。
   
   * `User Configuration/Policies/Administrative Templates/Windows Components/Internet Explorer/`
2. **[フォームのユーザー名とパスワードのオートコンプリート機能を有効にする]**という名前の設定を見つけます。
   
   > [!NOTE]
   > 以前のバージョンの Active Directory では、この設定は、 **[パスワードを保存するオートコンプリートを許可しない]**という名前で表示される場合があります。 その設定の構成は、このチュートリアルで説明する設定とは異なります。
   > 
   > 
   
    ![これは [ユーザー設定] から探すことに注意](./media/active-directory-saas-ie-group-policy/disable-auto-complete.png)
3. 上記の設定を右クリックし、**[編集]** を選択します。
4. **[フォームのユーザー名とパスワードのオートコンプリート機能を有効にする]** というタイトルのウィンドウで、**[無効]** を選択します。
   
    ![[無効] を選択](./media/active-directory-saas-ie-group-policy/disable-passwords.png)
5. **[OK]** をクリックしてこれらの変更を適用し、ウィンドウを閉じます。

これで、ユーザーは資格情報を保存することも、オートコンプリートを使用して前に保存された資格情報にアクセスすることもできなくなります。 ただし、このポリシーでは、他の種類のフォーム フィールド (検索フィールドなど) でのオートコンプリートは引き続き許可されます。

> [!WARNING]
> ユーザーがいくつかの資格情報を保存した後でこのポリシーが有効になった場合、既に保存されている資格情報がこのポリシーによって消去されることは*ありません*。
> 
> 

## <a name="step-6-testing-the-deployment"></a>手順 6: デプロイのテスト
拡張機能のデプロイメントが成功したかどうかを確認するには、次の手順に従います。

1. **[コンピューターの構成]**を使用してデプロイした場合は、「 [手順 2: グループ ポリシー オブジェクトを作成する](#step-2-create-the-group-policy-object)」で選択した OU に属しているクライアント コンピューターにサインインします。 **[ユーザーの構成]**を使用してデプロイした場合は、その OU に属しているユーザーとしてサインインします。
2. そのコンピューターでグループ ポリシーの変更が完全に更新されるには、2 回サインインする必要がある場合があります。 強制的に更新するには、**[コマンド プロンプト]** ウィンドウを開き、次のコマンドを実行します。`gpupdate /force`
3. インストールが行われるには、コンピューターを再起動する必要があります。 起動には、通常の拡張機能のインストールよりかなり長い時間がかかる可能性があります。
4. コンピューターが再起動したら、 **Internet Explorer**を開きます。 ウィンドウの右上隅の **[ツール]** (歯車アイコン) をクリックし、**[アドオンの管理]** をクリックします。
   
    ![[ツール] > [アドオンの管理] に移動する](./media/active-directory-saas-ie-group-policy/manage-add-ons.png)
5. **[アドオンの管理]** ウィンドウで、**[Access Panel Extension]** がインストールされていて、**[状態]** が **[有効]** に設定されていることを確認します。
   
    ![Access Panel Extension がインストールされ、有効になっていることを確認する](./media/active-directory-saas-ie-group-policy/verify-install.png)

## <a name="related-articles"></a>関連記事
* [Article Index for Application Management in Azure Active Directory](active-directory-apps-index.md)
* [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](active-directory-appssoaccess-whatis.md)
* [Internet Explorer 用アクセス パネル拡張機能のトラブルシューティング](active-directory-saas-ie-troubleshooting.md)


