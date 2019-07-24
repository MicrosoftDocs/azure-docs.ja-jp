---
title: チュートリアル:Azure Active Directory と KnowledgeOwl の統合 | Microsoft Docs
description: Azure Active Directory と KnowledgeOwl の間でシングル サインオンを構成する方法について説明します。
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 2ae30996-864d-4872-90bc-f770e1ea159a
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/27/2018
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: c8c86ad6e4b11f21c648083fac35e15eec7658c1
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2019
ms.locfileid: "57995507"
---
# <a name="tutorial-azure-active-directory-integration-with-knowledgeowl"></a>チュートリアル:Azure Active Directory と KnowledgeOwl の統合

このチュートリアルでは、KnowledgeOwl を Azure Active Directory (Azure AD) と統合する方法について説明します。

KnowledgeOwl と Azure AD の統合には、次の利点があります。

- KnowledgeOwl にアクセスする Azure AD ユーザーを制御できます。
- ユーザーが Azure AD アカウントで自動的に KnowledgeOwl にサインオン (シングル サインオン) できるようにします。
- 1 つの中央サイト (Azure Portal) でアカウントを管理できます。

SaaS アプリと Azure AD の統合の詳細については、「[Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)」をご覧ください。

## <a name="prerequisites"></a>前提条件

KnowledgeOwl と Azure AD の統合を構成するには、次のものが必要です。

- Azure AD サブスクリプション
- KnowledgeOwl でのシングル サインオンが有効なサブスクリプション

> [!NOTE]
> このチュートリアルの手順をテストする場合、運用環境を使用しないことをお勧めします。

このチュートリアルの手順をテストするには、次の推奨事項に従ってください。

- 必要な場合を除き、運用環境は使用しないでください。
- Azure AD の評価環境がない場合は、[1 か月の評価版を入手できます](https://azure.microsoft.com/pricing/free-trial/)。

## <a name="scenario-description"></a>シナリオの説明
このチュートリアルでは、テスト環境で Azure AD のシングル サインオンをテストします。 このチュートリアルで説明するシナリオは、主に次の 2 つの要素で構成されています。

1. ギャラリーからの KnowledgeOwl の追加
1. Azure AD シングル サインオンの構成とテスト

## <a name="adding-knowledgeowl-from-the-gallery"></a>ギャラリーからの KnowledgeOwl の追加
Azure AD への KnowledgeOwl の統合を構成するには、ギャラリーから管理対象 SaaS アプリの一覧に KnowledgeOwl を追加する必要があります。

**ギャラリーから KnowledgeOwl を追加するには、次の手順に従います。**

1. **[Azure Portal](https://portal.azure.com)** の左側のナビゲーション ウィンドウで、**[Azure Active Directory]** アイコンをクリックします。 

    ![Azure Active Directory のボタン][1]

1. **[エンタープライズ アプリケーション]** に移動します。 次に、**[すべてのアプリケーション]** に移動します。

    ![[エンタープライズ アプリケーション] ブレード][2]
    
1. 新しいアプリケーションを追加するには、ダイアログの上部にある **[新しいアプリケーション]** をクリックします。

    ![[新しいアプリケーション] ボタン][3]

1. 検索ボックスに「**KnowledgeOwl**」と入力し、結果パネルで **[KnowledgeOwl]** を選び、**[追加]** をクリックしてアプリケーションを追加します。

    ![結果リストの KnowledgeOwl](./media/knowledgeowl-tutorial/tutorial_knowledgeowl_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成とテスト

このセクションでは、"Britta Simon" というテスト ユーザーに基づいて、KnowledgeOwl で Azure AD のシングル サインオンを構成し、テストします。

シングル サインオンを機能させるには、Azure AD ユーザーに対応する KnowledgeOwl ユーザーが Azure AD で認識されている必要があります。 言い換えると、Azure AD ユーザーと KnowledgeOwl の関連ユーザーの間で、リンク関係が確立されている必要があります。

KnowledgeOwl で Azure AD のシングル サインオンを構成してテストするには、次の構成要素を完了する必要があります。

1. **[Azure AD シングル サインオンの構成](#configure-azure-ad-single-sign-on)** - ユーザーがこの機能を使用できるようにします。
1. **[Azure AD のテスト ユーザーの作成](#create-an-azure-ad-test-user)** - Britta Simon で Azure AD のシングル サインオンをテストします。
1. **[KnowledgeOwl のテスト ユーザーの作成](#create-a-knowledgeowl-test-user)** - KnowledgeOwl で Britta Simon に対応するユーザーを作成し、Azure AD の Britta Simon にリンクさせます。
1. **[Azure AD テスト ユーザーの割り当て](#assign-the-azure-ad-test-user)** - Britta Simon が Azure AD シングル サインオンを使用できるようにします。
1. **[シングル サインオンのテスト](#test-single-sign-on)** - 構成が機能するかどうかを確認します。

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成

このセクションでは、Azure portal で Azure AD のシングル サインオンを有効にし、KnowledgeOwl アプリケーションでシングル サインオンを構成します。

**KnowledgeOwl で Azure AD シングル サインオンを構成するには、次の手順に従います。**

1. Azure portal の **KnowledgeOwl** アプリケーション統合ページで、**[シングル サインオン]** をクリックします。

    ![シングル サインオン構成のリンク][4]

1. **[シングル サインオン]** ダイアログで、**[モード]** として **[SAML ベースのサインオン]** を選択し、シングル サインオンを有効にします。
 
    ![[シングル サインオン] ダイアログ ボックス](./media/knowledgeowl-tutorial/tutorial_knowledgeowl_samlbase.png)

1. **[KnowledgeOwl のドメインと URL]** セクションで、**IDP** 開始モードでアプリケーションを構成する場合は、次の手順に従います。

    ![[KnowledgeOwl のドメインと URL] のシングル サインオン情報](./media/knowledgeowl-tutorial/tutorial_knowledgeowl_url.png)

    a. **[識別子 (エンティティ ID)]** ボックスに、次のパターンで URL を入力します。

    |||
    |-|-|
    | `https://app.knowledgeowl.com/sp`|
    | `https://app.knowledgeowl.com/sp/id/<unique ID>`|
    |||

    b. **[応答 URL]** ボックスに、次のパターンを使用して URL を入力します。

    |||
    |-|-|
    | `https://subdomain.knowledgeowl.com/help/saml-login`|
    | `https://subdomain.knowledgeowl.com/docs/saml-login`|
    | `https://subdomain.knowledgeowl.com/home/saml-login`|
    | `https://privatedomain.com/help/saml-login`|
    | `https://privatedomain.com/docs/saml-login`|
    | `https://privatedomain.com/home/saml-login`|
    |||

1. アプリケーションを **SP** 開始モードで構成する場合は、**[詳細な URL 設定の表示]** チェックボックスをオンにして次の手順を実行します。

    ![[KnowledgeOwl のドメインと URL] のシングル サインオン情報](./media/knowledgeowl-tutorial/tutorial_knowledgeowl_url1.png)

    **[サインオン URL]** ボックスに、次の形式で URL を入力します。
    
    |||
    |-|-|
    | `https://subdomain.knowledgeowl.com/help/saml-login`|
    | `https://subdomain.knowledgeowl.com/docs/saml-login`|
    | `https://subdomain.knowledgeowl.com/home/saml-login`|
    | `https://privatedomain.com/help/saml-login`|
    | `https://privatedomain.com/docs/saml-login`|
    | `https://privatedomain.com/home/saml-login`|
    |||
     
    > [!NOTE]
    > これらは実際の値ではありません。 これらの値を、このチュートリアルの後半で説明する実際の識別子、応答 URL、サインオン URL に更新する必要があります。

1. KnowledgeOwl アプリケーションでは、特定の形式の SAML アサーションを使用するため、カスタム属性マッピングを SAML トークン属性の構成に追加する必要があります。 このアプリケーションには、次の要求を構成します。 この属性の値は、アプリケーション統合ページの **[User Attributer]** セクションで管理できます。

    ![Configure single sign-on](./media/knowledgeowl-tutorial/attribute.png)

1. **[Single sign-on]\(シングル サインオン\)** ダイアログの **[User Attributes]\(ユーザー属性\)** セクションで、上記の図に示すように SAML トークン属性を構成し、次の手順を実行します。
    
    | 属性名 | 属性値 | 名前空間|
    | ------------------- | -------------------- | -----|
    | ssoid | User.mail | `http://schemas.xmlsoap.org/ws/2005/05/identity/claims`|
    
    a. **[属性の追加]** をクリックして **[属性の追加]** ダイアログを開きます。
    
    ![Configure single sign-on](./media/knowledgeowl-tutorial/tutorial_attribute_04.png)

    ![Configure single sign-on](./media/knowledgeowl-tutorial/tutorial_attribute_05.png)

    b. **[名前]** ボックスに、その行に対して表示される属性名を入力します。
    
    c. **[値]** 一覧から、その行に対して表示される値を入力します。

    d. **[名前空間]** 一覧から、その行に表示される名前空間の値を入力します。
    
    e. **[OK]** をクリックします。

1. **[SAML 署名証明書]** セクションで、**[Certificate (Raw) (証明書 (Raw))]** をクリックし、コンピューターに証明書ファイルを保存します。

    ![証明書のダウンロードのリンク](./media/knowledgeowl-tutorial/tutorial_knowledgeowl_certificate.png) 

1. **[保存]** ボタンをクリックします。

    ![[シングル サインオンの構成] の [保存] ボタン](./media/knowledgeowl-tutorial/tutorial_general_400.png)
    
1. **[KnowledgeOwl 構成]** セクションで、**[KnowledgeOwl の構成]** をクリックして、**[サインオンの構成]** ウィンドウを開きます。 **[クイック リファレンス]** セクションから、**サインアウト URL、SAML エンティティ ID、SAML シングル サインオン サービス URL** をコピーします。

    ![KnowledgeOwl の構成](./media/knowledgeowl-tutorial/tutorial_knowledgeowl_configure.png)

1. 別の Web ブラウザー ウィンドウで、KnowledgeOwl 企業サイトに管理者としてログインします。

1. **[Settings]\(設定\)** をクリックし、**[Security]\(セキュリティ\)** を選択します。

     ![KnowledgeOwl の構成](./media/knowledgeowl-tutorial/configure1.png)

1. **[SAML SSO Integration]\(SAML SSO 統合\)** までスクロールして、次の手順に従います。
    
     ![KnowledgeOwl の構成](./media/knowledgeowl-tutorial/configure2.png)

     a. **[Enable SAML SSO]\(SAML SSO を有効にする\)** をオンにします。

     b. **[SP Entity ID]\(SP エンティティ ID\)** の値をコピーし、Azure portal の **[KnowledgeOwl のドメインと URL]** セクションの **[識別子 (エンティティ ID)]** に貼り付けます。

     c. **[SP Login URL]\(SP ログイン URL\)** の値をコピーし、Azure portal の **[KnowledgeOwl のドメインと URL]** セクションの **[サインオン URL] および [応答 URL]** ボックスに貼り付けます。

     d. **[IdP entityID]** ボックスに、Azure portal からコピーした **SAML エンティティ ID** の値を貼り付けます。

     e. **[IdP Login URL]\(IdP ログイン URL\)** ボックスに、Azure portal からコピーした **SAML シングル サインオン サービス URL** の値を貼り付けます。

     f. **[IdP Logout URL]\(IdP ログアウト URL\)** ボックスに、Azure portal からコピーした **サインアウト URL** の値を貼り付けます。

     g. **[Upload IdP Certificate]\(IdP 証明書のアップロード\)** をクリックして、Azure portal からダウンロードした証明書をアップロードします。

     h. **[Map SAML Attributes]\(SAML 属性のマッピング\)** をクリックして属性をマップし、次の手順に従います。
    
     ![KnowledgeOwl の構成](./media/knowledgeowl-tutorial/configure3.png)

    * **[SSO ID]** ボックスに、「`http://schemas.xmlsoap.org/ws/2005/05/identity/claims/ssoid`」と入力します。
    * **[Username/Email]\(ユーザー名/電子メール\)** ボックスに、「`http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress`」と入力します。
    * **[First Name]\(名\)** ボックスに、「`http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname`」と入力します。
    * **[Last Name]\(姓\)** ボックスに、「`http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname`」と入力します。
    * **[保存]**

      i. ページの下部にある **[保存]** をクリックします。

      ![KnowledgeOwl の構成](./media/knowledgeowl-tutorial/configure4.png)

### <a name="create-an-azure-ad-test-user"></a>Azure AD のテスト ユーザーの作成

このセクションの目的は、Azure Portal で Britta Simon というテスト ユーザーを作成することです。

   ![Azure AD のテスト ユーザーの作成][100]

**Azure AD でテスト ユーザーを作成するには、次の手順に従います。**

1. Azure Portal の左側のウィンドウで、**Azure Active Directory** のボタンをクリックします。

    ![Azure Active Directory のボタン](./media/knowledgeowl-tutorial/create_aaduser_01.png)

1. ユーザーの一覧を表示するには、**[ユーザーとグループ]** に移動し、**[すべてのユーザー]** をクリックします。

    ![[ユーザーとグループ] と [すべてのユーザー] リンク](./media/knowledgeowl-tutorial/create_aaduser_02.png)

1. **[ユーザー]** ダイアログ ボックスを開くには、**[すべてのユーザー]** ダイアログ ボックスの上部にある **[追加]** をクリックしてきます。

    ![[追加] ボタン](./media/knowledgeowl-tutorial/create_aaduser_03.png)

1. **[ユーザー]** ダイアログ ボックスで、次の手順に従います。

    ![[ユーザー] ダイアログ ボックス](./media/knowledgeowl-tutorial/create_aaduser_04.png)

    a. **[名前]** ボックスに「**BrittaSimon**」と入力します。

    b. **[ユーザー名]** ボックスに、ユーザーである Britta Simon の電子メール アドレスを入力します。

    c. **[パスワードを表示]** チェック ボックスをオンにし、**[パスワード]** ボックスに表示された値を書き留めます。

    d. **Create** をクリックしてください。
 
### <a name="create-a-knowledgeowl-test-user"></a>KnowledgeOwl テスト ユーザーの作成

このセクションの目的は、KnowledgeOwl で Britta Simon というユーザーを作成することです。 KnowledgeOwl では、Just-In-Time プロビジョニングがサポートされています。この設定は、既定で有効になっています。 このセクションでは、ユーザー側で必要な操作はありません。 ユーザーがまだ存在しない場合、KnowledgeOwl にアクセスしようとすると、新しいユーザーが作成されます。
>[!Note]
>ユーザーを手動で作成する必要がある場合は、 [KnowledgeOwl のサポート チーム](mailto:support@knowledgeowl.com)にお問い合わせください。

### <a name="assign-the-azure-ad-test-user"></a>Azure AD テスト ユーザーの割り当て

このセクションでは、Britta Simon に KnowledgeOwl へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

![ユーザー ロールを割り当てる][200] 

**KnowledgeOwl に Britta Simon を割り当てるには、次の手順に従います。**

1. Azure Portal でアプリケーション ビューを開き、ディレクトリ ビューに移動します。次に、**[エンタープライズ アプリケーション]** に移動し、**[すべてのアプリケーション]** をクリックします。

    ![ユーザーの割り当て][201] 

1. アプリケーションの一覧で **[KnowledgeOwl]** を選択します。

    ![アプリケーションの一覧の [KnowledgeOwl] リンク](./media/knowledgeowl-tutorial/tutorial_knowledgeowl_app.png)  

1. 左側のメニューで **[ユーザーとグループ]** をクリックします。

    ![[ユーザーとグループ] リンク][202]

1. **[追加]** ボタンをクリックします。 次に、**[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。

    ![[割り当ての追加] ウィンドウ][203]

1. **[ユーザーとグループ]** ダイアログで、ユーザーの一覧から **[Britta Simon]** を選択します。

1. **[ユーザーとグループ]** ダイアログで **[選択]** をクリックします。

1. **[割り当ての追加]** ダイアログで **[割り当て]** ボタンをクリックします。
    
### <a name="test-single-sign-on"></a>シングル サインオンのテスト

このセクションでは、アクセス パネルを使用して Azure AD のシングル サインオン構成をテストします。

アクセス パネルで [KnowledgeOwl] タイルをクリックすると、自動的に KnowledgeOwl アプリケーションにサインオンします。
アクセス パネルの詳細については、[アクセス パネルの概要](../user-help/active-directory-saas-access-panel-introduction.md)に関するページを参照してください。 

## <a name="additional-resources"></a>その他のリソース

* [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](tutorial-list.md)
* [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/knowledgeowl-tutorial/tutorial_general_01.png
[2]: ./media/knowledgeowl-tutorial/tutorial_general_02.png
[3]: ./media/knowledgeowl-tutorial/tutorial_general_03.png
[4]: ./media/knowledgeowl-tutorial/tutorial_general_04.png

[100]: ./media/knowledgeowl-tutorial/tutorial_general_100.png

[200]: ./media/knowledgeowl-tutorial/tutorial_general_200.png
[201]: ./media/knowledgeowl-tutorial/tutorial_general_201.png
[202]: ./media/knowledgeowl-tutorial/tutorial_general_202.png
[203]: ./media/knowledgeowl-tutorial/tutorial_general_203.png

