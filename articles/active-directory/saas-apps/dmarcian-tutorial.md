---
title: 'チュートリアル: Azure Active Directory と dmarcian の統合 | Microsoft Docs'
description: Azure Active Directory と dmarcian の間でシングル サインオンを構成する方法について説明します。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 08/01/2019
ms.author: jeedes
ms.openlocfilehash: 4a5ea47352bfbe86fe194821da0b6bf10818caf9
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "92454730"
---
# <a name="tutorial-integrate-dmarcian-with-azure-active-directory"></a>チュートリアル: dmarcian と Azure Active Directory の統合

このチュートリアルでは、dmarcian と Azure Active Directory (Azure AD) を統合する方法について説明します。 dmarcian と Azure AD を統合すると、次のことができます。

* dmarcian にアクセスできる Azure AD ユーザーを制御できます。
* ユーザーが自分の Azure AD アカウントを使用して dmarcian に自動的にサインインするように設定できます。
* 1 つの中央サイト (Azure Portal) で自分のアカウントを管理します。

SaaS アプリと Azure AD の統合の詳細については、「[Azure Active Directory でのアプリケーションへのシングル サインオン](../manage-apps/what-is-single-sign-on.md)」を参照してください。

## <a name="prerequisites"></a>前提条件

開始するには、次が必要です。

* Azure AD サブスクリプション。 サブスクリプションがない場合は、[無料アカウント](https://azure.microsoft.com/free/)を取得できます。
* dmarcian のシングル サインオン (SSO) が有効なサブスクリプション。

## <a name="scenario-description"></a>シナリオの説明

このチュートリアルでは、テスト環境で Azure AD の SSO を構成してテストします。

* dmarcian では、**SP と IDP** によって開始される SSO がサポートされます

## <a name="adding-dmarcian-from-the-gallery"></a>ギャラリーからの dmarcian の追加

Azure AD への dmarcian の統合を構成するに、ギャラリーから管理対象 SaaS アプリの一覧に dmarcian を追加する必要があります。

1. 職場または学校アカウントか、個人の Microsoft アカウントを使用して、[Azure portal](https://portal.azure.com) にサインインします。
1. 左のナビゲーション ウィンドウで **[Azure Active Directory]** サービスを選択します。
1. **[エンタープライズ アプリケーション]** に移動し、 **[すべてのアプリケーション]** を選択します。
1. 新しいアプリケーションを追加するには、 **[新しいアプリケーション]** を選択します。
1. **[ギャラリーから追加する]** セクションで、検索ボックスに、「**dmarcian**」と入力します。
1. 結果ウィンドウで **[dmarcian]** を選択し、アプリを追加します。 お使いのテナントにアプリが追加されるのを数秒待機します。


## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成とテスト

**B.Simon** というテスト ユーザーを使用して、dmarcian に対する Azure AD SSO を構成してテストします。 SSO が機能するためには、Azure AD ユーザーと dmarcian の関連ユーザーとの間にリンク関係を確立する必要があります。

dmarcian で Azure AD SSO を構成してテストするには、次の構成要素を完了する必要があります。

1. **[Azure AD SSO の構成](#configure-azure-ad-sso)** - ユーザーがこの機能を使用できるようにします。
2. **[dmarcian SSO の構成](#configure-dmarcian-sso)** - アプリケーション側でシングル サインオン設定を構成します。
3. **[Azure AD のテスト ユーザーの作成](#create-an-azure-ad-test-user)** - B.Simon で Azure AD のシングル サインオンをテストします。
4. **[Azure AD テスト ユーザーの割り当て](#assign-the-azure-ad-test-user)** - B.Simon が Azure AD シングル サインオンを使用できるようにします。
5. **[dmarcian のテスト ユーザーの作成](#create-dmarcian-test-user)** - dmarcian で B.Simon に対応するユーザーを作成し、Azure AD の Britta Simon にリンクさせます。
6. **[SSO のテスト](#test-sso)** - 構成が機能するかどうかを確認します。

### <a name="configure-azure-ad-sso"></a>Azure AD SSO の構成

これらの手順に従って、Azure portal で Azure AD SSO を有効にします。

1. [Azure portal](https://portal.azure.com/) の **dmarcian** アプリケーション統合ページで、**[管理]** セクションを見つけて、**[シングル サインオン]** を選択します。
1. **[シングル サインオン方式の選択]** ページで、 **[SAML]** を選択します。
1. **[SAML でシングル サインオンをセットアップします]** ページで、 **[基本的な SAML 構成]** の編集/ペン アイコンをクリックして設定を編集します。

   ![基本的な SAML 構成を編集する](common/edit-urls.png)

4. **[基本的な SAML 構成]** セクションで、アプリケーションを **IDP** 開始モードで構成する場合は、次の手順を実行します。

    a. **[識別子]** ボックスに、次の形式で URL を入力します。

    ```http
    https://us.dmarcian.com/sso/saml/<ACCOUNT_ID>/sp.xml
    https://dmarcian-eu.com/sso/saml/<ACCOUNT_ID>/sp.xml
    https://dmarcian-ap.com/sso/saml/<ACCOUNT_ID>/sp.xml
    ```

    b. **[応答 URL]** ボックスに、次のパターンを使用して URL を入力します。

    ```http
    https://us.dmarcian.com/login/<ACCOUNT_ID>/handle/
    https://dmarcian-eu.com/login/<ACCOUNT_ID>/handle/
    https://dmarcian-ap.com/login/<ACCOUNT_ID>/handle/
    ```

5. アプリケーションを **SP** 開始モードで構成する場合は、 **[追加の URL を設定します]** をクリックして次の手順を実行します。

    **[サインオン URL]** ボックスに、次のパターンを使用して URL を入力します。
    
    ```http
    https://us.dmarcian.com/login/<ACCOUNT_ID>
    https://dmarcian-eu.com/login/<ACCOUNT_ID>
    https://dmarciam-ap.com/login/<ACCOUNT_ID>
    ```
     
    > [!NOTE] 
    > これらは実際の値ではありません。 これらの値は、実際の識別子、応答 URL、およびサインオン URL に更新します。実際の値については、このチュートリアルの後の方で説明します。

4. **Set up Single Sign-On with SAML\(SAML でのシングルサインオンの設定** ページの **SAML 署名証明書** セクションで、コピー ボタンをクリックして **App Federation Metadata Url\(アプリのフェデレーション メタデータ URL)** をコピーして、コンピューターに保存します。

    ![証明書のダウンロードのリンク](common/copy-metadataurl.png)

### <a name="configure-dmarcian-sso"></a>dmarcian SSO の構成

1. dmarcian 内での構成を自動化するには、**[拡張機能のインストール]** をクリックして **My Apps Secure Sign-in ブラウザー拡張機能** をインストールする必要があります。

    ![マイ アプリの拡張機能](common/install-myappssecure-extension.png)

2. ブラウザーに拡張機能を追加した後、**[Setup dmarcian]\(dmarcian のセットアップ\)** をクリックすると、dmarcian アプリケーションに移動します。 そこから、管理者の資格情報を入力して dmarcian にサインインします。 ブラウザー拡張機能によりアプリケーションが自動的に構成され、手順 3 ～ 6 が自動化されます。

    ![セットアップの構成](common/setup-sso.png)

3. dmarcian を手動でセットアップする場合は、新しい Web ブラウザー ウィンドウを開き、管理者として dmarcian 企業サイトにサインインして、次の手順を実行します。

4. 右上隅にある **[プロファイル]** をクリックし、**[ユーザー設定]** に移動します。

    ![[ユーザー設定]](./media/dmarcian-tutorial/tutorial_dmarcian_pref.png)

5. 下へスクロールして **[シングル サインオン]** セクションをクリックしてから、**[構成]** をクリックします。

    ![シングル](./media/dmarcian-tutorial/tutorial_dmarcian_sso.png)

6. **[SAML シングル サインオン]** ページで、**[状態]** を **[有効]** に設定し、次の手順を実行します。

    ![認証](./media/dmarcian-tutorial/tutorial_dmarcian_auth.png)

    * **[Add dmarcian to your Identity Provider]\(ID プロバイダーへの dmarcian の追加\)** セクションで、**[コピー]** をクリックしてインスタンスの **[Assertion Consumer Service URL]** をコピーし、それを Azure portal の **[基本的な SAML 構成] セクション** にある **[応答 URL]** テキスト ボックスに貼り付けます。

    * **[Add dmarcian to your Identity Provider]\(ID プロバイダーへの dmarcian の追加\)** セクションで、**[コピー]** をクリックしてインスタンスの **[エンティティ ID]** をコピーし、それを Azure portal の **[基本的な SAML 構成] セクション** にある **[識別子]** テキスト ボックスに貼り付けます。

    * **[Set up Authentication] (認証の設定)** セクションの **[Identity Provider Metadata] (ID プロバイダーのメタデータ)** テキスト ボックスに、Azure Portal からコピーした **[アプリのフェデレーション メタデータ URL]** を貼り付けます。

    * **[Set up Authentication]\(認証の設定\)** セクションの **[Attribute Statements]\(属性ステートメント\)** テキスト ボックスに、URL `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress` を貼り付けます。

    * **[Set up Login URL]\(ログイン URL の設定\)** セクションで、インスタンスの **[ログイン URL]** をコピーし、それを Azure portal の **[基本的な SAML 構成] セクション** にある **[サインオン URL]** テキスト ボックスに貼り付けます。

        > [!Note]
        > **[ログイン URL]** は、組織に応じて変更できます。

    * **[保存]** をクリックします。

### <a name="create-an-azure-ad-test-user"></a>Azure AD のテスト ユーザーの作成

このセクションでは、Azure portal 内で B.Simon というテスト ユーザーを作成します。

1. Azure portal の左側のウィンドウから、 **[Azure Active Directory]** 、 **[ユーザー]** 、 **[すべてのユーザー]** の順に選択します。
1. 画面の上部にある **[新しいユーザー]** を選択します。
1. **[ユーザー]** プロパティで、以下の手順を実行します。
   1. **[名前]** フィールドに「`B.Simon`」と入力します。  
   1. **[ユーザー名]** フィールドに「username@companydomain.extension」と入力します。 たとえば、「 `B.Simon@contoso.com` 」のように入力します。
   1. **[パスワードを表示]** チェック ボックスをオンにし、 **[パスワード]** ボックスに表示された値を書き留めます。
   1. **Create** をクリックしてください。

### <a name="assign-the-azure-ad-test-user"></a>Azure AD テスト ユーザーの割り当て

このセクションでは、B.Simon に dmarcian へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

1. Azure portal で **[エンタープライズ アプリケーション]** を選択し、 **[すべてのアプリケーション]** を選択します。
1. アプリケーションの一覧で **[dmarcian]** を選択します。
1. アプリの概要ページで、 **[管理]** セクションを見つけて、 **[ユーザーとグループ]** を選択します。

   ![[ユーザーとグループ] リンク](common/users-groups-blade.png)

1. **[ユーザーの追加]** を選択し、 **[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。

    ![[ユーザーの追加] リンク](common/add-assign-user.png)

1. **[ユーザーとグループ]** ダイアログの [ユーザー] の一覧から **[B.Simon]** を選択し、画面の下部にある **[選択]** ボタンをクリックします。
1. SAML アサーション内に任意のロール値が必要な場合、 **[ロールの選択]** ダイアログでユーザーに適したロールを一覧から選択し、画面の下部にある **[選択]** をクリックします。
1. **[割り当ての追加]** ダイアログで、 **[割り当て]** をクリックします。

### <a name="create-dmarcian-test-user"></a>dmarcian のテスト ユーザーの作成

Azure AD ユーザーが dmarcian にサインインできるようにするには、ユーザーを dmarcian にプロビジョニングする必要があります。 dmarcian では、プロビジョニングは手動のタスクです。

**ユーザー アカウントをプロビジョニングするには、次の手順に従います。**

1. セキュリティ管理者として dmarcian にサインインします。

2. 右上隅にある **[プロファイル]** をクリックし、**[ユーザーの管理]** に移動します。

    ![ユーザー](./media/dmarcian-tutorial/tutorial_dmarcian_user.png)

3. **[SSO Users] (SSO ユーザー)** セクションの右側で、**[新しいユーザーの追加]** をクリックします。

    ![ユーザーの追加](./media/dmarcian-tutorial/tutorial_dmarcian_addnewuser.png)

4. **[新しいユーザーの追加]** ポップアップで、次の手順を実行します。

    ![新しいユーザー](./media/dmarcian-tutorial/tutorial_dmarcian_save.png)

    a. **[New User Email]\(新しいユーザーの電子メール\)** ボックスに、ユーザーのメール アドレス (**brittasimon\@contoso.com** など) を入力します。

    b. そのユーザーに管理者権限を付与する場合は、**[Make User an Admin] (ユーザーを管理者にする)** を選択します。

    c. **[ユーザーの追加]** をクリックします。

### <a name="test-sso"></a>SSO のテスト 

このセクションでは、アクセス パネルを使用して Azure AD のシングル サインオン構成をテストします。

アクセス パネルで [dmarcian] タイルをクリックすると、SSO を設定した dmarcian に自動的にサインインします。 アクセス パネルの詳細については、[アクセス パネルの概要](../user-help/my-apps-portal-end-user-access.md)に関する記事を参照してください。

## <a name="additional-resources"></a>その他のリソース

- [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](./tutorial-list.md)

- [Azure Active Directory でのアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)

- [Azure Active Directory の条件付きアクセスとは](../conditional-access/overview.md)