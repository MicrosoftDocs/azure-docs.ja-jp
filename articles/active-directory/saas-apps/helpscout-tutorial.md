---
title: 'チュートリアル: Azure Active Directory と Help Scout の統合 | Microsoft Docs'
description: Azure Active Directory と Help Scout の間でシングル サインオンを構成する方法について説明します。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 10/24/2019
ms.author: jeedes
ms.openlocfilehash: 7c5e8210bc8b805d72149fd2ef3335c1d637a58f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "92445241"
---
# <a name="tutorial-azure-active-directory-integration-with-help-scout"></a>チュートリアル: Azure Active Directory と Help Scout の統合

このチュートリアルでは、Help Scout と Azure Active Directory (Azure AD) を統合する方法について説明します。
Help Scout と Azure AD の統合には、次の利点があります。

* Help Scout にアクセスする Azure AD ユーザーを制御できます。
* ユーザーが自分の Azure AD アカウントを使用して Help Scout に自動的にサインイン (シングル サインオン) できるようにすることができます。
* 1 つの中央サイト (Azure Portal) でアカウントを管理できます。

SaaS アプリと Azure AD の統合の詳細については、「 [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)」を参照してください。
Azure サブスクリプションをお持ちでない場合は、開始する前に[無料アカウントを作成](https://azure.microsoft.com/free/)してください。

## <a name="prerequisites"></a>前提条件

Help Scout と Azure AD の統合を構成するには、次のものが必要です。

* Azure AD サブスクリプション。 サブスクリプションがない場合は、[無料アカウント](https://azure.microsoft.com/free/)を取得できます。
* Help Scout でのシングル サインオンが有効なサブスクリプション

## <a name="scenario-description"></a>シナリオの説明

このチュートリアルでは、テスト環境で Azure AD のシングル サインオンを構成してテストします。

* Help Scout では、**SP と IDP** によって開始される SSO がサポートされます
* Help Scout では、**Just-In-Time** ユーザー プロビジョニングがサポートされます

## <a name="adding-help-scout-from-the-gallery"></a>ギャラリーからの Help Scout の追加

Azure AD への Help Scout の統合を構成するには、ギャラリーから管理対象 SaaS アプリの一覧に Help Scout を追加する必要があります。

1. 職場または学校アカウントか、個人の Microsoft アカウントを使用して、[Azure portal](https://portal.azure.com) にサインインします。
1. 左のナビゲーション ウィンドウで **[Azure Active Directory]** サービスを選択します。
1. **[エンタープライズ アプリケーション]** に移動し、 **[すべてのアプリケーション]** を選択します。
1. 新しいアプリケーションを追加するには、 **[新しいアプリケーション]** を選択します。
1. **[ギャラリーから追加する]** セクションで、検索ボックスに「**Help Scout**」と入力します。
1. 結果ウィンドウで **[Help Scout]** を選択し、アプリを追加します。 お使いのテナントにアプリが追加されるのを数秒待機します。

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成とテスト

このセクションでは、**B.Simon** というテスト ユーザーに基づいて、Help Scout で Azure AD のシングル サインオンを構成し、テストします。
シングル サインオンを機能させるには、Azure AD ユーザーと Help Scout 内の関連ユーザー間にリンク関係が確立されている必要があります。

Help Scout で Azure AD のシングル サインオンを構成してテストするには、次の構成要素を完了する必要があります。

1. **[Azure AD SSO の構成](#configure-azure-ad-sso)** - ユーザーがこの機能を使用できるようにします。
    * **[Azure AD のテスト ユーザーの作成](#create-an-azure-ad-test-user)** - B.Simon で Azure AD のシングル サインオンをテストします。
    * **[Azure AD テスト ユーザーの割り当て](#assign-the-azure-ad-test-user)** - B.Simon が Azure AD シングル サインオンを使用できるようにします。
1. **[Help Scout SSO の構成](#configure-help-scout-sso)** - アプリケーション側でシングル サインオン設定を構成します。
    * **[Help Scout テスト ユーザーの作成](#create-help-scout-test-user)** - Help Scout で B.Simon に対応するユーザーを作成し、Azure AD の B.Simon にリンクさせます。
1. **[SSO のテスト](#test-sso)** - 構成が機能するかどうかを確認します。

### <a name="configure-azure-ad-sso"></a>Azure AD SSO の構成

このセクションでは、Azure portal 上で Azure AD のシングル サインオンを有効にします。

Help Scout で Azure AD シングル サインオンを構成するには、次の手順に従います。

1. [Azure portal](https://portal.azure.com/) の **Help Scout** アプリケーション統合ページで、 **[シングル サインオン]** を選択します。

    ![シングル サインオン構成のリンク](common/select-sso.png)

1. **[シングル サインオン方式の選択]** ダイアログで、 **[SAML/WS-Fed]** モードを選択して、シングル サインオンを有効にします。

    ![シングル サインオン選択モード](common/select-saml-option.png)

1. **[SAML でシングル サインオンをセットアップします]** ページで、 **[編集]** アイコンをクリックして **[基本的な SAML 構成]** ダイアログを開きます。

    ![基本的な SAML 構成を編集する](common/edit-urls.png)

1. **[基本的な SAML 構成]** セクションで、アプリケーションを **IDP** 開始モードで構成する場合は、次の手順を実行します。

    ![このスクリーンショットは、[基本的な SAML 構成] を示しています。ここで、識別子と応答 U R L を入力し、[保存] を選択できます。](common/idp-intiated.png)

    a. **識別子** は、Help Scout の **対象ユーザー URI (サービス プロバイダーのエンティティ ID)** で、先頭は `urn:` です

    b. **応答 URL** は、Help Scout の **ポスト バック URL (Assertion Consumer Service URL)** で、先頭は `https://` です 

    > [!NOTE]
    > これらの URL の値は、単なる例です。 これらの値は、実際の応答 URL と識別子で更新する必要があります。 この値は、[認証] セクションの **[シングル サインオン]** タブから取得します。これについては後で説明します。

1. アプリケーションを **SP** 開始モードで構成する場合は、**[追加の URL を設定します]** をクリックして次の手順を実行します。

    ![このスクリーンショットは、[追加の U R L を設定します] を示しています。ここで、サインオン U R L を入力できます。](common/metadata-upload-additional-signon.png)

    **[サインオン URL]** ボックスに、「`https://secure.helpscout.net/members/login/`」と入力します。

1. **[SAML でシングル サインオンをセットアップします]** ページの **[SAML 署名証明書]** セクションで、 **[ダウンロード]** をクリックして要件のとおりに指定したオプションからの **証明書 (Base64)** をダウンロードして、お使いのコンピューターに保存します。

    ![証明書のダウンロードのリンク](common/certificatebase64.png)

1. **[Help Scout のセットアップ]** セクションで、要件に従って適切な URL をコピーします。

    ![構成 URL のコピー](common/copy-configuration-urls.png)

    a. ログイン URL

    b. Azure AD 識別子

    c. ログアウト URL

### <a name="create-an-azure-ad-test-user"></a>Azure AD のテスト ユーザーの作成

このセクションの目的は、Azure portal で B.Simon というテスト ユーザーを作成することです。

1. Azure portal の左側のウィンドウで、 **[Azure Active Directory]** 、 **[ユーザー]** 、 **[すべてのユーザー]** の順に選択します。

    ![[ユーザーとグループ] と [すべてのユーザー] リンク](common/users.png)

2. 画面の上部にある **[新しいユーザー]** を選択します。

    ![[新しいユーザー] ボタン](common/new-user.png)

3. [ユーザーのプロパティ] で、次の手順を実行します。

    ![[ユーザー] ダイアログ ボックス](common/user-properties.png)

    a. **[名前]** フィールドに「**B.Simon**」と入力します。
  
    b. **[ユーザー名]** フィールドに「**B.Simon\@yourcompanydomain.extension**」と入力します。  
    たとえば、B.Simon@contoso.com のように指定します。

    c. **[パスワードを表示]** チェック ボックスをオンにし、[パスワード] ボックスに表示された値を書き留めます。

    d. **Create** をクリックしてください。

### <a name="assign-the-azure-ad-test-user"></a>Azure AD テスト ユーザーの割り当て

このセクションでは、B.Simon に Help Scout へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

1. Azure portal で **[エンタープライズ アプリケーション]** を選択し、 **[すべてのアプリケーション]** を選択してから、 **[Help Scout]** を選択します。

    ![[エンタープライズ アプリケーション] ブレード](common/enterprise-applications.png)

2. アプリケーションの一覧で **[Help Scout]** を選択します。

    ![アプリケーションの一覧の Help Scout のリンク](common/all-applications.png)

3. 左側のメニューで **[ユーザーとグループ]** を選びます。

    ![[ユーザーとグループ] リンク](common/users-groups-blade.png)

4. **[ユーザーの追加]** をクリックし、 **[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。

    ![[割り当ての追加] ウィンドウ](common/add-assign-user.png)

5. **[ユーザーとグループ]** ダイアログの [ユーザー] リストで **[B.Simon]** を選択し、画面の下部にある **[選択]** ボタンをクリックします。

6. SAML アサーション内に任意のロール値が必要な場合、 **[ロールの選択]** ダイアログでユーザーに適したロールを一覧から選択し、画面の下部にある **[選択]** をクリッします。

7. **[割り当ての追加]** ダイアログで、 **[割り当て]** ボタンをクリックします。

## <a name="configure-help-scout-sso"></a>Help Scout の SSO の構成

1. Help Scout 内での構成を自動化するには、**[拡張機能のインストール]** をクリックして **My Apps Secure Sign-in ブラウザー拡張機能** をインストールする必要があります。

    ![マイ アプリの拡張機能](common/install-myappssecure-extension.png)

1. ブラウザーに拡張機能を追加した後、 **[Help Scout のセットアップ]** をクリックすると、Help Scout アプリケーションに移動します。 そこから、管理者の資格情報を入力して Help Scout にサインインします。 ブラウザー拡張機能によりアプリケーションが自動的に構成され、手順 3 ～ 7 が自動化されます。

    ![セットアップの構成](common/setup-sso.png)

1. Help Scout を手動でセットアップする場合は、新しい Web ブラウザー ウィンドウを開き、管理者としてご自分の Help Scout 企業サイトにサインインして、次の手順のようにします。

1. 上部のメニューで **[Manage]\(管理\)** をクリックし、ドロップダウン メニューの **[Company]\(会社\)** を選択します。

    ![[Company]\(会社\) が選択された [Manage]\(管理\) メニューのスクリーンショット。](./media/helpscout-tutorial/settings1.png)

1. 左側のナビゲーション ウィンドウで **[Authentication]\(認証\)** を選択します。

    ![選択された [Authentication]\(認証\) を示すスクリーンショット。](./media/helpscout-tutorial/settings2.png)

1. [SAML 設定] セクションが表示されます。ここで次の手順に従います。

    ![指定された情報を入力できる [Single Sign-On]\(シングル サインオン\) タブを示すスクリーンショット。](./media/helpscout-tutorial/settings3.png)

    a. **[Post-back URL (Assertion Consumer Service URL)]\(ポスト バック URL (Assertion Consumer Service URL)\)** の値をコピーし、Azure portal の **[基本的な SAML 構成]** セクションの **[応答 URL]** ボックスに貼り付けます。

    b. **[Audience URI (Service Provider Entity ID)]\(対象ユーザー URI (サービス プロバイダー エンティティ ID)\)** の値をコピーし、Azure portal の **[基本的な SAML 構成]** セクションの **[識別子]** ボックスに貼り付けます。

1. **[SAML を有効にする]** をオンにして、次の手順を実行します。

    ![[Single Sign-On]\(シングル サインオン\) タブのスクリーンショット。ここで、SAML を有効にしたり他の情報を追加したりします。](./media/helpscout-tutorial/settings4.png)

    a. **[Single Sign-On URL]\(シングル サインオン URL\)** ボックスに、Azure portal からコピーした **[ログイン URL]** の値を貼り付けます。

    b. **[証明書のアップロード]** をクリックして、Azure Portal からダウンロードした **証明書 (Base64)** をアップロードします。

    c. **[メール ドメイン]** ボックスに、組織のメール ドメイン (例: `contoso.com`) を入力します。 複数のドメインを指定する場合は、コンマで区切ります。 Help Scout ユーザーまたは管理者が [Help Scout ログイン ページ](https://secure.helpscout.net/members/login/)で特定のドメインを入力すると必ず、その資格情報で認証するために ID プロバイダーにルーティングされます。

    d. 最後に、ユーザーがこの方法以外で Help Scout にログオンできないようにする場合は、 **[Force SAML Sign-on]\(強制 SAML サインオン\)** の設定を切り替えてオンにします。 Help Scout 資格情報でも引き続きサインインできるようにする場合は、この設定をオフのままにします。 これを有効にしても、アカウント所有者は、いつでも自身のアカウント パスワードで Help Scout にログインにします。

    e. **[保存]** をクリックします。

### <a name="create-help-scout-test-user"></a>Help Scout テスト ユーザーの作成

このセクションでは、B.Simon というユーザーを Help Scout に作成します。 Help Scout では、Just-In-Time ユーザー プロビジョニングがサポートされています。この設定は既定で有効になっています。 このセクションでは、ユーザー側で必要な操作はありません。 Help Scout にユーザーがまだ存在していない場合は、認証後に新規に作成されます。

### <a name="test-sso"></a>SSO のテスト

このセクションでは、アクセス パネルを使用して Azure AD のシングル サインオン構成をテストします。

アクセス パネルで [Help Scout] タイルをクリックすると、SSO を設定した Help Scout に自動的にサインインします。 アクセス パネルの詳細については、[アクセス パネルの概要](../user-help/my-apps-portal-end-user-access.md)に関する記事を参照してください。

## <a name="additional-resources"></a>その他のリソース

- [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](./tutorial-list.md)

- [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)

- [Azure Active Directory の条件付きアクセスとは](../conditional-access/overview.md)

- [Azure AD で Help Scout を試す](https://aad.portal.azure.com/)