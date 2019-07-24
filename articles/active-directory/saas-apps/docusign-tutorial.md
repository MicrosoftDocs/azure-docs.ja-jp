---
title: チュートリアル:Azure Active Directory と DocuSign の統合 | Microsoft Docs
description: Azure Active Directory と DocuSign の間でシングル サインオンを構成する方法について説明します。
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: a691288b-84c1-40fb-84bd-5b06878865f0
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 04/01/2019
ms.author: jeedes
ms.openlocfilehash: 72505cfc0a86c00882de37c35dff61a12a9c3fbe
ms.sourcegitcommit: b8a8d29fdf199158d96736fbbb0c3773502a092d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/15/2019
ms.locfileid: "59565752"
---
# <a name="tutorial-azure-active-directory-integration-with-docusign"></a>チュートリアル:Azure Active Directory と DocuSign の統合

このチュートリアルでは、DocuSign と Azure Active Directory (Azure AD) を統合する方法について説明します。
DocuSign と Azure AD の統合には、次の利点があります。

* DocuSign にアクセスする Azure AD ユーザーを制御できます。
* ユーザーが自分の Azure AD アカウントで DocuSign に自動的にサインイン (シングル サインオン) するように設定できます。
* 1 つの中央サイト (Azure Portal) でアカウントを管理できます。

SaaS アプリと Azure AD の統合の詳細については、「 [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)」を参照してください。
Azure サブスクリプションをお持ちでない場合は、開始する前に[無料アカウントを作成](https://azure.microsoft.com/free/)してください。

## <a name="prerequisites"></a>前提条件

Azure AD と DocuSign の統合を構成するには、次のものが必要です。

* Azure AD サブスクリプション。 Azure AD の環境がない場合は、[無料アカウント](https://azure.microsoft.com/free/)を取得できます
* DocuSign でのシングル サインオンが有効なサブスクリプション

## <a name="scenario-description"></a>シナリオの説明

このチュートリアルでは、テスト環境で Azure AD のシングル サインオンを構成してテストします。

* DocuSign では、**SP** Initiated SSO がサポートされます

* DocuSign では、**Just-In-Time** ユーザー プロビジョニングがサポートされます

## <a name="adding-docusign-from-the-gallery"></a>ギャラリーからの DocuSign の追加

Azure AD への DocuSign の統合を構成するには、ギャラリーから管理対象 SaaS アプリの一覧に DocuSign を追加する必要があります。

**ギャラリーから DocuSign を追加するには、次の手順を実行します。**

1. **[Azure Portal](https://portal.azure.com)** の左側のナビゲーション ウィンドウで、**[Azure Active Directory]** アイコンをクリックします。

    ![Azure Active Directory のボタン](common/select-azuread.png)

2. **[エンタープライズ アプリケーション]** に移動し、**[すべてのアプリケーション]** オプションを選択します。

    ![[エンタープライズ アプリケーション] ブレード](common/enterprise-applications.png)

3. 新しいアプリケーションを追加するには、ダイアログの上部にある **[新しいアプリケーション]** をクリックします。

    ![[新しいアプリケーション] ボタン](common/add-new-app.png)

4. 検索ボックスに「**DocuSign**」と入力し、結果ウィンドウで **DocuSign** を選択し、**[追加]** をクリックして、アプリケーションを追加します。

     ![結果一覧の DocuSign](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成とテスト

このセクションでは、**Britta Simon** というテスト ユーザーに基づいて、DocuSign で Azure AD のシングル サインオンを構成し、テストします。
シングル サインオンを機能させるには、Azure AD ユーザーと DocuSign 内の関連ユーザー間にリンク関係が確立されている必要があります。

DocuSign で Azure AD のシングル サインオンを構成してテストするには、次の手順を完了する必要があります。

1. **[Azure AD シングル サインオンの構成](#configure-azure-ad-single-sign-on)** - ユーザーがこの機能を使用できるようにします。
2. **[DocuSign のシングル サインオンの構成](#configure-docusign-single-sign-on)** - アプリケーション側でシングル サインオン設定を構成します。
3. **[Azure AD のテスト ユーザーの作成](#create-an-azure-ad-test-user)** - Britta Simon で Azure AD のシングル サインオンをテストします。
4. **[Azure AD テスト ユーザーの割り当て](#assign-the-azure-ad-test-user)** - Britta Simon が Azure AD シングル サインオンを使用できるようにします。
5. **[DocuSign のテスト ユーザーの作成](#create-docusign-test-user)** - DocuSign で Britta Simon に対応するユーザーを作成し、Azure AD の Britta Simon にリンクさせます。
6. **[シングル サインオンのテスト](#test-single-sign-on)** - 構成が機能するかどうかを確認します。

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成

このセクションでは、Azure portal 上で Azure AD のシングル サインオンを有効にします。

DocuSign で Azure AD シングル サインオンを構成するには、次の手順に従います。

1. [Azure portal](https://portal.azure.com/) の **DocuSign** アプリケーション統合ページで、**[シングル サインオン]** を選択します。

    ![シングル サインオン構成のリンク](common/select-sso.png)

2. **[シングル サインオン方式の選択]** ダイアログで、**[SAML/WS-Fed]** モードを選択して、シングル サインオンを有効にします。

    ![シングル サインオン選択モード](common/select-saml-option.png)

3. **[SAML でシングル サインオンをセットアップします]** ページで、**[編集]** アイコンをクリックして **[基本的な SAML 構成]** ダイアログを開きます。

    ![基本的な SAML 構成を編集する](common/edit-urls.png)

4. **[基本的な SAML 構成]** セクションで、次の手順を実行します。

    ![[DocuSign のドメインと URL] のシングル サインオン情報](common/sp-identifier.png)

    a. **[サインオン URL]** ボックスに、次のパターンを使用して URL を入力します。`https://<subdomain>.docusign.com/organizations/<OrganizationID>/saml2/login/sp/<IDPID>`

    b. **[識別子 (エンティティ ID)]** ボックスに、次のパターンを使用して URL を入力します。`https://<subdomain>.docusign.com/organizations/<OrganizationID>/saml2`

    > [!NOTE]
    > これらは実際の値ではありません。 実際のサインオン URL と識別子に値を置き換えます。実際の値については、このチュートリアルの **[View SAML 2.0 Endpoints]\(SAML 2.0 エンドポイントの表示\)** に関するセクションで説明します。

5. **[SAML でシングル サインオンをセットアップします]** ページの **[SAML 署名証明書]** セクションで、**[ダウンロード]** をクリックして要件のとおりに指定したオプションからの**証明書 (Base64)** をダウンロードして、お使いのコンピューターに保存します。

    ![証明書のダウンロードのリンク](common/certificatebase64.png)

6. **[DocuSign のセットアップ]** セクションで、要件に従って適切な URL をコピーします。

    ![構成 URL のコピー](common/copy-configuration-urls.png)

    a. ログイン URL

    b. Azure AD 識別子

    c. ログアウト URL

### <a name="configure-docusign-single-sign-on"></a>DocuSign のシングル サインオンの構成

1. 別の Web ブラウザー ウィンドウで、**DocuSign 管理者ポータル**に管理者としてサインインします。

2. ページの右上にあるプロファイル **ロゴ**をクリックし、**[Go to Admin]\(管理に移動\)** をクリックします。
  
    ![シングル サインオンの構成][51]

3. ドメインのソリューション ページで **[Domains]\(ドメイン\)** をクリックします。

    ![シングル サインオンの構成][50]

4. **[Domains]\(ドメイン\)** セクションの **[CLAIM DOMAIN]\(ドメインの要求\)** をクリックします。

    ![シングル サインオンの構成][52]

5. **[Claim a domain]\(ドメインの要求\)** ダイアログの **[Domain Name]\(ドメイン名\)** ボックスに、所属する会社のドメインを入力してから、**[CLAIM]\(要求\)** をクリックします。 ドメインを確認し、状態がアクティブであることを確かめてください。

    ![シングル サインオンの構成][53]

6. ドメインのソリューション ページで **[Identity Providers]\(ID プロバイダー\)** をクリックします。
  
    ![シングル サインオンの構成][54]

7. **[Identity Provider]\(ID プロバイダー\)** セクションで、**[ADD IDENTITY PROVIDER]\(ID プロバイダー\)** をクリックします。 

    ![シングル サインオンの構成][55]

8. **[Identity Provider Settings (ID プロバイダーの設定)]** ページで、次の手順を実行します。

    ![シングル サインオンの構成][56]

    a. **[Name (名前)]** ボックスに、構成の一意の名前を入力します。 スペースは使用しないでください。

    b. **[ID プロバイダーの発行者]** ボックスに、Azure portal からコピーした **Azure AD 識別子**の値を貼り付けます。

    c. **[Identity Provider Login URL]\(ID プロバイダーのログイン URL\)** テキスト ボックスに、Azure portal からコピーした**ログイン URL** の値を貼り付けます。

    d. **[Identity Provider Logout URL]\(ID プロバイダーのログアウト URL\)** テキスト ボックスに、Azure portal からコピーした**ログアウト URL** の値を貼り付けます。

    e. **[Sign AuthN request]\(AuthN 要求に署名する\)** を選択します。

    f. **[Send AuthN request by (認証要求の送信方法)]** として、**[POST]** を選択します。

    g. **[Send logout request by]\(ログアウト要求の送信方法\)** として、**[GET]** を選択します。

    h. **[Custom Attribute Mapping]\(カスタム属性のマッピング\)** セクションの **[ADD NEW MAPPING]\(新しいマッピングの追加\)** をクリックします。

    ![シングル サインオンの構成][62]

    i. Azure AD の要求とマッピングするフィールドを選択します。 この例では、**emailaddress** 要求は **http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress** の値とマップされます。 これは、Azure AD の電子メール要求の既定の要求名です。**[SAVE]\(保存\)** をクリックします。

    ![シングル サインオンの構成][57]

    > [!NOTE]
    > Azure AD のユーザーを DocuSign のユーザー マッピングにマッピングする際は、適切な**ユーザー識別子**を使用してください。 適切なフィールドを選択し、組織の設定に基づく適切な値を入力してください。

    j. **[Identity Provider Certificate]\(ID プロバイダー証明書\)** セクションで **[ADD CERTIFICATE]\(証明書の追加\)** をクリックし、Azure AD ポータルからダウンロードした証明書をアップロードし、**[SAVE]\(保存\)** をクリックします。

    ![シングル サインオンの構成][58]

    k. **[Identity Providers]\(ID プロバイダー\)** セクションで、**[ACTIONS]\(アクション\)** をクリックし、**[Endpoints]\(エンドポイント\)** をクリックします。

    ![シングル サインオンの構成][59]

    l. **DocuSign 管理者ポータル**の **[View SAML 2.0 Endpoints]\(SAML 2.0 エンドポイントの表示\)** セクションで、次の手順を実行します。

    ![シングル サインオンの構成][60]

    * **[Service Provider Issuer URL]\(サービス プロバイダー発行者 URL\)** をコピーし、Azure portal の **[基本的な SAML 構成]** セクションの **[識別子]** ボックスに貼り付けます。

    * **[Service Provider Login URL (サービス プロバイダーのログイン URL)]** をコピーし、Azure portal の **[基本的な SAML 構成]** セクションの **[サインオン URL]** ボックスに貼り付けます。

    * **[閉じる]** をクリックします。

### <a name="create-an-azure-ad-test-user"></a>Azure AD のテスト ユーザーの作成 

このセクションの目的は、Azure Portal で Britta Simon というテスト ユーザーを作成することです。

1. Azure portal の左側のウィンドウで、**[Azure Active Directory]**、**[ユーザー]**、**[すべてのユーザー]** の順に選択します。

    ![[ユーザーとグループ] と [すべてのユーザー] リンク](common/users.png)

2. 画面の上部にある **[新しいユーザー]** を選択します。

    ![[新しいユーザー] ボタン](common/new-user.png)

3. [ユーザーのプロパティ] で、次の手順を実行します。

    ![[ユーザー] ダイアログ ボックス](common/user-properties.png)

    a. **[名前]** フィールドに「**BrittaSimon**」と入力します。
  
    b. **[ユーザー名]** フィールドに「brittasimon@yourcompanydomain.extension」と入力します。 たとえば、BrittaSimon@contoso.com のように指定します。

    c. **[パスワードを表示]** チェック ボックスをオンにし、[パスワード] ボックスに表示された値を書き留めます。

    d. **Create** をクリックしてください。

### <a name="assign-the-azure-ad-test-user"></a>Azure AD テスト ユーザーの割り当て

このセクションでは、Britta Simon に DocuSign へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

1. Azure portal 上で **[エンタープライズ アプリケーション]** を選択し、**[すべてのアプリケーション]** を選択してから、**[DocuSign]** を選択します。

    ![[エンタープライズ アプリケーション] ブレード](common/enterprise-applications.png)

2. アプリケーションの一覧で **[DocuSign]** を選択します。

    ![アプリケーションの一覧の DocuSign のリンク](common/all-applications.png)

3. 左側のメニューで **[ユーザーとグループ]** を選びます。

    ![[ユーザーとグループ] リンク](common/users-groups-blade.png)

4. **[ユーザーの追加]** をクリックし、**[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。

    ![[割り当ての追加] ウィンドウ](common/add-assign-user.png)

5. **[ユーザーとグループ]** ダイアログの [ユーザー] の一覧で **[Britta Simon]** を選択し、画面の下部にある **[選択]** ボタンをクリックします。

6. SAML アサーション内に任意のロール値が必要な場合、**[ロールの選択]** ダイアログでユーザーに適したロールを一覧から選択し、画面の下部にある **[選択]** をクリッします。

7. **[割り当ての追加]** ダイアログで、**[割り当て]** ボタンをクリックします。

### <a name="create-docusign-test-user"></a>DocuSign のテスト ユーザーの作成

このセクションでは、Britta Simon というユーザーを DocuSign に作成します。 DocuSign では、Just-In-Time ユーザー プロビジョニングがサポートされています。この設定は既定で有効になっています。 このセクションでは、ユーザー側で必要な操作はありません。 DocuSign にユーザーがまだ存在していない場合は、認証後に新規に作成されます。

>[!Note]
>ユーザーを手動で作成する必要がある場合は、[DocuSign のサポート チーム](https://support.docusign.com/)にお問い合わせください。

### <a name="test-single-sign-on"></a>シングル サインオンのテスト 

このセクションでは、アクセス パネルを使用して Azure AD のシングル サインオン構成をテストします。

アクセス パネル上で [DocuSign] タイルをクリックすると、SSO を設定した DocuSign に自動的にサインインします。 アクセス パネルの詳細については、[アクセス パネルの概要](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)に関する記事を参照してください。

## <a name="additional-resources"></a>その他のリソース

- [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Azure Active Directory の条件付きアクセスとは](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

<!--Image references-->

[50]: ./media/docusign-tutorial/tutorial_docusign_18.png
[51]: ./media/docusign-tutorial/tutorial_docusign_21.png
[52]: ./media/docusign-tutorial/tutorial_docusign_22.png
[53]: ./media/docusign-tutorial/tutorial_docusign_23.png
[54]: ./media/docusign-tutorial/tutorial_docusign_19.png
[55]: ./media/docusign-tutorial/tutorial_docusign_20.png
[56]: ./media/docusign-tutorial/tutorial_docusign_24.png
[57]: ./media/docusign-tutorial/tutorial_docusign_25.png
[58]: ./media/docusign-tutorial/tutorial_docusign_26.png
[59]: ./media/docusign-tutorial/tutorial_docusign_27.png
[60]: ./media/docusign-tutorial/tutorial_docusign_28.png
[61]: ./media/docusign-tutorial/tutorial_docusign_29.png
[62]: ./media/docusign-tutorial/tutorial_docusign_30.png
