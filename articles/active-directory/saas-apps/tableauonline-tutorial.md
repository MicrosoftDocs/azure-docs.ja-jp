---
title: チュートリアル:Azure Active Directory と Tableau Online の統合 | Microsoft Docs
description: Azure Active Directory と Tableau Online の間でシングル サインオンを構成する方法について説明します。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/31/2020
ms.author: jeedes
ms.openlocfilehash: 035bc13ee44a8a003ae860eb2bdd67432fa91f14
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/18/2020
ms.locfileid: "88542521"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-tableau-online"></a>チュートリアル:Azure Active Directory シングル サインオン (SSO) と Tableau Online の統合

このチュートリアルでは、Tableau Online と Azure Active Directory (Azure AD) を統合する方法について説明します。 Azure AD と Tableau Online を統合すると、次のことができます。

* Tableau Online にアクセスできるユーザーを Azure AD で制御できます。
* ユーザーが自分の Azure AD アカウントを使用して Tableau Online に自動的にサインインできるように設定できます。
* 1 つの中央サイト (Azure Portal) で自分のアカウントを管理します。

SaaS アプリと Azure AD の統合の詳細については、「[Azure Active Directory でのアプリケーションへのシングル サインオン](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)」を参照してください。

## <a name="prerequisites"></a>前提条件

開始するには、次が必要です。

* Azure AD サブスクリプション。 サブスクリプションがない場合は、[無料アカウント](https://azure.microsoft.com/free/)を取得できます。
* Tableau Online でのシングル サインオン (SSO) が有効なサブスクリプション。

## <a name="scenario-description"></a>シナリオの説明

このチュートリアルでは、テスト環境で Azure AD のシングル サインオンを構成してテストします。

* Tableau Online では、**SP** Initiated SSO がサポートされます
* Tableau Online を構成したら、組織の機密データを流出と侵入からリアルタイムで保護するセッション制御を適用することができます。 セッション制御は、条件付きアクセスを拡張したものです。 [Microsoft Cloud App Security でセッション制御を適用する方法](https://docs.microsoft.com/cloud-app-security/proxy-deployment-aad)をご覧ください。

## <a name="adding-tableau-online-from-the-gallery"></a>ギャラリーからの Tableau Online の追加

Azure AD への Tableau Online の統合を構成するには、ギャラリーから管理対象 SaaS アプリの一覧に Tableau Online を追加する必要があります。

1. 職場または学校アカウントか、個人の Microsoft アカウントを使用して、[Azure portal](https://portal.azure.com) にサインインします。
1. 左のナビゲーション ウィンドウで **[Azure Active Directory]** サービスを選択します。
1. **[エンタープライズ アプリケーション]** に移動し、 **[すべてのアプリケーション]** を選択します。
1. 新しいアプリケーションを追加するには、 **[新しいアプリケーション]** を選択します。
1. **[ギャラリーから追加する]** セクションで、検索ボックスに、「**Tableau Online**」と入力します。
1. 結果パネルで **[Tableau Online]** を選択し、アプリを追加します。 お使いのテナントにアプリが追加されるのを数秒待機します。

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成とテスト

このセクションでは、**Britta Simon** というテスト ユーザーに基づいて、Tableau Online で Azure AD のシングル サインオンを構成し、テストします。
シングル サインオンを機能させるには、Azure AD ユーザーと Tableau Online 内の関連ユーザーとの間にリンク関係が確立されている必要があります。

Tableau Online に対する Azure AD SSO を構成してテストするには、次の構成要素を完了します。

1. **[Azure AD SSO の構成](#configure-azure-ad-sso)** - ユーザーがこの機能を使用できるようにします。
    1. **[Azure AD のテスト ユーザーの作成](#create-an-azure-ad-test-user)** - B.Simon で Azure AD のシングル サインオンをテストします。
    1. **[Azure AD テスト ユーザーの割り当て](#assign-the-azure-ad-test-user)** - B.Simon が Azure AD シングル サインオンを使用できるようにします。
1. **[Tableau Online の SSO の構成](#configure-tableau-online-sso)** - アプリケーション側でシングル サインオン設定を構成します。
    1. **[Tableau Online のテスト ユーザーの作成](#create-tableau-online-test-user)** - Tableau Online で B.Simon に対応するユーザーを作成し、Azure AD の B.Simon にリンクさせます。
1. **[SSO のテスト](#test-sso)** - 構成が機能するかどうかを確認します。

### <a name="configure-azure-ad-sso"></a>Azure AD SSO の構成

このセクションでは、Azure portal 上で Azure AD のシングル サインオンを有効にします。

Tableau Online で Azure AD シングル サインオンを構成するには、次の手順に従います。

1. [Azure portal](https://portal.azure.com/) の **Tableau Online** アプリケーション統合ページで、 **[シングル サインオン]** を選択します。

    ![シングル サインオン構成のリンク](common/select-sso.png)

2. **[シングル サインオン方式の選択]** ダイアログで、 **[SAML/WS-Fed]** モードを選択して、シングル サインオンを有効にします。

    ![シングル サインオン選択モード](common/select-saml-option.png)

3. **[SAML でシングル サインオンをセットアップします]** ページで、 **[編集]** アイコンをクリックして **[基本的な SAML 構成]** ダイアログを開きます。

    ![基本的な SAML 構成を編集する](common/edit-urls.png)

4. **[基本的な SAML 構成]** セクションで、次の手順を実行します。

    ![[Tableau Online のドメインと URL] のシングル サインオン情報](common/sp-identifier.png)

    a. **[サインオン URL]** ボックスに、URL として「`https://sso.online.tableau.com/public/sp/login?alias=<entityid>`」と入力します。

    b. **[識別子 (エンティティ ID)]** ボックスに `https://sso.online.tableau.com/public/sp/metadata?alias=<entityid>` という URL を入力します。

    > [!NOTE]
    > `<entityid>` 値は、このチュートリアルの **[Tableau Online のセットアップ]** セクションで取得します。 エンティティ ID 値は、 **[Tableau Online のセットアップ]** セクションの **[Azure AD 識別子]** 値になります。

5. **[SAML でシングル サインオンをセットアップします]** ページの **[SAML 署名証明書]** セクションで、 **[ダウンロード]** をクリックして、要件のとおりに指定したオプションから**フェデレーション メタデータ XML** をダウンロードして、お使いのコンピューターに保存します。

    ![証明書のダウンロードのリンク](common/metadataxml.png)

6. **[Tableau Online のセットアップ]** セクションで、要件に従って適切な URL をコピーします。

    ![構成 URL のコピー](common/copy-configuration-urls.png)

    a. ログイン URL

    b. Azure AD 識別子

    c. ログアウト URL

### <a name="create-an-azure-ad-test-user"></a>Azure AD のテスト ユーザーの作成

このセクションの目的は、Azure Portal で Britta Simon というテスト ユーザーを作成することです。

1. Azure portal の左側のウィンドウで、 **[Azure Active Directory]** 、 **[ユーザー]** 、 **[すべてのユーザー]** の順に選択します。

    ![[ユーザーとグループ] と [すべてのユーザー] リンク](common/users.png)

2. 画面の上部にある **[新しいユーザー]** を選択します。

    ![[新しいユーザー] ボタン](common/new-user.png)

3. [ユーザーのプロパティ] で、次の手順を実行します。

    ![[ユーザー] ダイアログ ボックス](common/user-properties.png)

    a. **[名前]** フィールドに「**BrittaSimon**」と入力します。
  
    b. **[User name]\(ユーザー名\)** フィールドに「**brittasimon\@yourcompanydomain.extension**」と入力します。  
    たとえば、「BrittaSimon\@contoso.com」です。

    c. **[パスワードを表示]** チェック ボックスをオンにし、[パスワード] ボックスに表示された値を書き留めます。

    d. **Create** をクリックしてください。

### <a name="assign-the-azure-ad-test-user"></a>Azure AD テスト ユーザーの割り当て

このセクションでは、Britta Simon に Tableau Online へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

1. Azure portal で **[エンタープライズ アプリケーション]** を選択し、 **[すべてのアプリケーション]** を選択してから、 **[Tableau Online]** を選択します。

    ![[エンタープライズ アプリケーション] ブレード](common/enterprise-applications.png)

2. アプリケーションの一覧で **[Tableau Online]** を選択します。

    ![アプリケーションの一覧の Tableau Online のリンク](common/all-applications.png)

3. 左側のメニューで **[ユーザーとグループ]** を選びます。

    ![[ユーザーとグループ] リンク](common/users-groups-blade.png)

4. **[ユーザーの追加]** をクリックし、 **[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。

    ![[割り当ての追加] ウィンドウ](common/add-assign-user.png)

5. **[ユーザーとグループ]** ダイアログの [ユーザー] の一覧で **[Britta Simon]** を選択し、画面の下部にある **[選択]** ボタンをクリックします。

6. SAML アサーション内に任意のロール値が必要な場合、 **[ロールの選択]** ダイアログでユーザーに適したロールを一覧から選択し、画面の下部にある **[選択]** をクリッします。

7. **[割り当ての追加]** ダイアログで、 **[割り当て]** ボタンをクリックします。

## <a name="configure-tableau-online-sso"></a>Tableau Online の SSO の構成

1. 別のブラウザー ウィンドウで、Tableau Online アプリケーションにサインオンします。 **[設定]** 、 **[認証]** の順にクリックします。

    ![Configure single sign-on](./media/tableauonline-tutorial/tutorial_tableauonline_09.png)

2. SAML を有効にするには、 **[Authentication types]\(認証の種類\)** セクションで、 **[Enable an additional authentication method]\(追加の認証方法を有効にする\)** をオンにし、 **[SAML]** チェック ボックスをオンにします。

    ![Configure single sign-on](./media/tableauonline-tutorial/tutorial_tableauonline_12.png)

3. 下へスクロールして、 **[Import metadata file into Tableau Online (Tableau Online にメタデータ ファイルをインポートする)]** セクションを表示します。  [Browse]\(参照\) をクリックし、Azure AD からダウンロードしたメタデータ ファイルをインポートします。 次に、 **[Apply (適用)]** をクリックします。

   ![Configure single sign-on](./media/tableauonline-tutorial/tutorial_tableauonline_13.png)

4. **[Match assertions (アサーションの一致)]** セクションで、**メール アドレス**、**名**、**姓**に対応する ID プロバイダーのアサーション名を挿入します。 Azure AD から情報を取得するには、以下の手順に従います。 
  
    a. Azure Portal で **Tableau Online** アプリケーション統合ページに移動します。

    b. **[User Attributes & Claims]\(ユーザー属性とクレーム\)** セクションで、編集アイコンをクリックします。

   ![Configure single sign-on](./media/tableauonline-tutorial/attributesection.png)

    c. 以下の手順で、属性 givenname、email、surname の名前空間の値をコピーします。

   ![Azure AD Single Sign-On](./media/tableauonline-tutorial/tutorial_tableauonline_10.png)

    d. **user.givenname** 値をクリックします

    e. **[名前空間]** ボックスの値をコピーします。

    ![Configure single sign-on](./media/tableauonline-tutorial/attributesection2.png)

    f. email、surname の名前空間の値をコピーするには、上の手順を繰り返します。

    g. Tableau Online アプリケーションに切り替えて、 **[User Attributes & Claims]\(ユーザー属性とクレーム\)** セクションを次のように設定します。

    * Email (電子メール): **mail** または **userprincipalname**

    * First name (名): **givenname**

    * Last name (姓): **surname**

    ![Configure single sign-on](./media/tableauonline-tutorial/tutorial_tableauonline_14.png)

### <a name="create-tableau-online-test-user"></a>Tableau Online テスト ユーザーの作成

このセクションでは、Tableau Online で Britta Simon というユーザーを作成します。

1. **Tableau Online** で、 **[設定]** 、 **[認証]** セクションの順にクリックします。 下へスクロールして、 **[Manage Users]\(ユーザーの管理\)** セクションを表示します。 **[Add Users]\(ユーザーの追加\)** をクリックし、 **[Enter email addresses]\(メール アドレスを入力\)** をクリックします。
  
    ![Azure AD のテスト ユーザーの作成](./media/tableauonline-tutorial/tutorial_tableauonline_15.png)

2. **[Add users for (SAML) authentication]\((SAML) 認証用にユーザーを追加する\)** を選択します。 **[Enter email addresses]\(メール アドレスを入力\)** テキスト ボックスに「britta.simon\@contoso.com」と入力します
  
    ![Azure AD のテスト ユーザーの作成](./media/tableauonline-tutorial/tutorial_tableauonline_11.png)

3. **[ユーザーの追加]** をクリックします。

### <a name="test-sso"></a>SSO のテスト

このセクションでは、アクセス パネルを使用して Azure AD のシングル サインオン構成をテストします。

アクセス パネルで [Tableau Online] タイルをクリックすると、SSO を設定した Tableau Online に自動的にサインインします。 アクセス パネルの詳細については、[アクセス パネルの概要](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)に関する記事を参照してください。

## <a name="additional-resources"></a>その他のリソース

- [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Azure Active Directory の条件付きアクセスとは](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Microsoft Cloud App Security におけるセッション制御とは](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)