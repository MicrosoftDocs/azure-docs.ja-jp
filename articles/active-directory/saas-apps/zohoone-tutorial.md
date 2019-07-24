---
title: チュートリアル:Azure Active Directory と Zoho One の統合 | Microsoft Docs
description: Azure Active Directory と Zoho One の間でシングル サインオンを構成する方法について説明します。
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: bbc3038c-0d8b-45dd-9645-368bd3d01a0f
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/25/2018
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: aadb3188969e099a709711ac18916b67c83b5da1
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2019
ms.locfileid: "57884440"
---
# <a name="tutorial-azure-active-directory-integration-with-zoho-one"></a>チュートリアル:Azure Active Directory と Zoho One の統合

このチュートリアルでは、Zoho One と Azure Active Directory (Azure AD) を統合する方法について説明します。

Zoho One と Azure AD の統合には、次の利点があります。

- Zoho One にアクセスできる Azure AD ユーザーを制御できます。
- ユーザーが自分の Azure AD アカウントで自動的に Zoho One にサインオン (シングル サインオン) できるようになります。
- 1 つの中央サイト (Azure Portal) でアカウントを管理できます。

SaaS アプリと Azure AD の統合の詳細については、「[Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)」をご覧ください。

## <a name="prerequisites"></a>前提条件

Zoho One と Azure AD の統合を構成するには、次のものが必要です。

- Azure AD サブスクリプション
- Zoho One でのシングル サインオンが有効なサブスクリプション

> [!NOTE]
> このチュートリアルの手順をテストする場合、運用環境を使用しないことをお勧めします。

このチュートリアルの手順をテストするには、次の推奨事項に従ってください。

- 必要な場合を除き、運用環境は使用しないでください。
- Azure AD の評価環境がない場合は、[1 か月の評価版を入手できます](https://azure.microsoft.com/pricing/free-trial/)。

## <a name="scenario-description"></a>シナリオの説明
このチュートリアルでは、テスト環境で Azure AD のシングル サインオンをテストします。 このチュートリアルで説明するシナリオは、主に次の 2 つの要素で構成されています。

1. ギャラリーからの Zoho One の追加
1. Azure AD シングル サインオンの構成とテスト

## <a name="adding-zoho-one-from-the-gallery"></a>ギャラリーからの Zoho One の追加
Azure AD への Zoho One の統合を構成するには、ギャラリーからマネージド SaaS アプリの一覧に Zoho One を追加する必要があります。

**ギャラリーから Zoho One を追加するには、次の手順に従います。**

1. **[Azure Portal](https://portal.azure.com)** の左側のナビゲーション ウィンドウで、**[Azure Active Directory]** アイコンをクリックします。 

    ![Azure Active Directory のボタン][1]

1. **[エンタープライズ アプリケーション]** に移動します。 次に、**[すべてのアプリケーション]** に移動します。

    ![[エンタープライズ アプリケーション] ブレード][2]
    
1. 新しいアプリケーションを追加するには、ダイアログの上部にある **[新しいアプリケーション]** をクリックします。

    ![[新しいアプリケーション] ボタン][3]

1. 検索ボックスに「**Zoho One**」と入力し、結果パネルで **[Zoho One]** を選び、**[追加]** をクリックして、アプリケーションを追加します。

    ![結果一覧の Zoho One](./media/zohoone-tutorial/tutorial_zohoone_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成とテスト

このセクションでは、"Britta Simon" というテスト ユーザーに基づいて、Zoho One で Azure AD のシングル サインオンを構成し、テストします。

シングル サインオンを機能させるには、Azure AD ユーザーに対応する Zoho One ユーザーが Azure AD で認識されている必要があります。 言い換えると、Azure AD ユーザーと Zoho One の関連ユーザーの間で、リンク関係が確立されている必要があります。

Zoho One で Azure AD のシングル サインオンを構成してテストするには、次の構成要素を完了する必要があります。

1. **[Azure AD シングル サインオンの構成](#configure-azure-ad-single-sign-on)** - ユーザーがこの機能を使用できるようにします。
1. **[Azure AD のテスト ユーザーの作成](#create-an-azure-ad-test-user)** - Britta Simon で Azure AD のシングル サインオンをテストします。
1. **[Zoho One テスト ユーザーの作成](#create-a-zoho-one-test-user)** - Azure AD の Britta Simon にリンクさせるために、対応するユーザーを Zoho One で作成します。
1. **[Azure AD テスト ユーザーの割り当て](#assign-the-azure-ad-test-user)** - Britta Simon が Azure AD シングル サインオンを使用できるようにします。
1. **[シングル サインオンのテスト](#test-single-sign-on)** - 構成が機能するかどうかを確認します。

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成

このセクションでは、Azure Portal で Azure AD のシングル サインオンを有効にし、Zoho One アプリケーションでシングル サインオンを構成します。

**Zoho One で Azure AD シングル サインオンを構成するには、次の手順に従います。**

1. Azure Portal の **Zoho One** アプリケーション統合ページで、**[シングル サインオン]** をクリックします。

    ![シングル サインオン構成のリンク][4]

1. **[シングル サインオン]** ダイアログで、**[モード]** として **[SAML ベースのサインオン]** を選択し、シングル サインオンを有効にします。
 
    ![[シングル サインオン] ダイアログ ボックス](./media/zohoone-tutorial/tutorial_zohoone_samlbase.png)

1. アプリケーションを **IDP** 開始モードで構成する場合は、**[Zoho One ドメインと URL]** セクションで次の手順を実行します。

    ![[Zoho One ドメインと URL] のシングル サインオン情報](./media/zohoone-tutorial/tutorial_zohoone_url.png)

    a. **[識別子 (エンティティ ID)]** ボックスに、URL を入力します。`one.zoho.com`

    b. **[応答 URL]** ボックスに、`https://accounts.zoho.com/samlresponse/<saml-identifier>` のパターンを使用して URL を入力します。

    c. **[詳細な URL 設定の表示]** をクリックします。

    d. **[リレー状態]** ボックスに、URL `https://one.zoho.com` を入力します。

1. **SP** 開始モードでアプリケーションを構成する場合は、次の手順を実行します。

    **[サインオン URL]** ボックスに、`https://accounts.zoho.com/samlauthrequest/<domain_name>?serviceurl=https://one.zoho.com` のパターンを使用して URL を入力します。
     
    > [!NOTE] 
    > 上記の **[応答 URL]** と **[サインオン URL]** の値は、実際の値ではありません。 実際の応答 URL とサインオン URL に値を置き換えます。実際の値については後で説明します。 

1. **[SAML 署名証明書]** セクションで、**[Certificate (Base64) (証明書 (Base64)) ]** をクリックし、コンピューターに証明書ファイルを保存します。

    ![証明書のダウンロードのリンク](./media/zohoone-tutorial/tutorial_zohoone_certificate.png) 

1. **[保存]** ボタンをクリックします。

    ![[シングル サインオンの構成] の [保存] ボタン](./media/zohoone-tutorial/tutorial_general_400.png)
    
1. **[Zoho One 構成]** セクションで、**[Zoho One の構成]** をクリックして、**[サインオンの構成]** ウィンドウを開きます。 **[クイック リファレンス]** セクションから**サインアウト URL と SAML シングル サインオン サービス URL** をコピーします。

    ![Zoho One 構成](./media/zohoone-tutorial/tutorial_zohoone_configure.png) 

1. 別の Web ブラウザー ウィンドウで、Zoho One 企業サイトに管理者としてログインします。

1. **[Organization]\(組織\)** タブで、**[SAML Authentication]\(SAML 認証\)** の **[Setup]\(設定\)** をクリックします。

    ![Zoho One、組織](./media/zohoone-tutorial/tutorial_zohoone_setup.png)

1. ポップアップ ページで、次の手順に従います。

    ![Zoho One、sig](./media/zohoone-tutorial/tutorial_zohoone_save.png)

    a. **[Sign-in URL]\(サインイン URL\)** ボックスに、Azure Portal からコピーした **SAML シングル サインオン サービス URL** の値を貼り付けます。

    b. **[Sign-out URL]\(サインアウト URL\)** ボックスに、Azure Portal からコピーした**サインアウト URL** の値を貼り付けます。

    c. **[Browse]\(参照\)** をクリックし、Azure Portal からダウンロードした**証明書 (Base64)** をアップロードします。

    d. **[Save]** をクリックします。

1. SAML 認証設定を保存した後、**[SAML-Identfier]\(SAML 識別子\)** の値をコピーし、この値を Azure Portal の **[Zoho One ドメインと URL]** セクションの **[応答 URL]** で使用します。

    ![Zoho One、SAML](./media/zohoone-tutorial/tutorial_zohoone_samlidenti.png)

1. **[Domains]\(ドメイン\)** タブに移動し、**[Add Domain]\(ドメインの追加\)** をクリックします。

    ![Zoho One、ドメイン](./media/zohoone-tutorial/tutorial_zohoone_domain.png)

1. **[Add Domain]\(ドメインの追加\)** ページで、次の手順に従います。

    ![Zoho One、ドメインの追加](./media/zohoone-tutorial/tutorial_zohoone_adddomain.png)

    a. **[Domain Name]\(ドメイン名\)** ボックスに、ドメインを入力します (例: **contoso.com**)。

    b. **[追加]** をクリックします。

    >[!Note]
    >ドメインに追加した後、[これら](https://www.zoho.com/one/help/admin-guide/domain-verification.html)の手順に従って、ドメインを確認します。 ドメインを確認したら、Azure Portal の **[Zoho One ドメインと URL]** セクションの **[サインオン URL]** でこのドメイン名を使用します。

### <a name="create-an-azure-ad-test-user"></a>Azure AD のテスト ユーザーの作成

このセクションの目的は、Azure Portal で Britta Simon というテスト ユーザーを作成することです。

   ![Azure AD のテスト ユーザーの作成][100]

**Azure AD でテスト ユーザーを作成するには、次の手順に従います。**

1. Azure Portal の左側のウィンドウで、**Azure Active Directory** のボタンをクリックします。

    ![Azure Active Directory のボタン](./media/zohoone-tutorial/create_aaduser_01.png)

1. ユーザーの一覧を表示するには、**[ユーザーとグループ]** に移動し、**[すべてのユーザー]** をクリックします。

    ![[ユーザーとグループ] と [すべてのユーザー] リンク](./media/zohoone-tutorial/create_aaduser_02.png)

1. **[ユーザー]** ダイアログ ボックスを開くには、**[すべてのユーザー]** ダイアログ ボックスの上部にある **[追加]** をクリックしてきます。

    ![[追加] ボタン](./media/zohoone-tutorial/create_aaduser_03.png)

1. **[ユーザー]** ダイアログ ボックスで、次の手順に従います。

    ![[ユーザー] ダイアログ ボックス](./media/zohoone-tutorial/create_aaduser_04.png)

    a. **[名前]** ボックスに「**BrittaSimon**」と入力します。

    b. **[ユーザー名]** ボックスに、ユーザーである Britta Simon の電子メール アドレスを入力します。

    c. **[パスワードを表示]** チェック ボックスをオンにし、**[パスワード]** ボックスに表示された値を書き留めます。

    d. **Create** をクリックしてください。
 
### <a name="create-a-zoho-one-test-user"></a>Zoho One テスト ユーザーの作成

Azure AD ユーザーが Zoho One にログインできるようにするには、そのユーザーを Zoho One にプロビジョニングする必要があります。 Zoho One では、プロビジョニングは手動で行います。

**ユーザー アカウントをプロビジョニングするには、次の手順に従います。**

1. セキュリティ管理者として Zoho One にログインします。

1. **[Users]\(ユーザー\)** タブで、**ユーザー ロゴ**をクリックします。

    ![Zoho One、ユーザー](./media/zohoone-tutorial/tutorial_zohoone_users.png)

1. **[Add User]** ページで、次の手順に従います。

    ![Zoho One、ユーザーの追加](./media/zohoone-tutorial/tutorial_zohoone_adduser.png)
    
    a. **[Name]\(名前\)** ボックスに、ユーザーの氏名を入力します (例: **Britta Simon**)。
    
    b. **[Email Address]\(メール アドレス\)** ボックスに、ユーザーのメール アドレス (**brittasimon\@contoso.com** など) を入力します。

    >[!Note]
    >ドメインの一覧から、確認済みドメインを選択します。

    c. **[追加]** をクリックします。

### <a name="assign-the-azure-ad-test-user"></a>Azure AD テスト ユーザーの割り当て

このセクションでは、Britta Simon に Zoho One へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

![ユーザー ロールを割り当てる][200] 

**Zoho One に Britta Simon を割り当てるには、次の手順に従います。**

1. Azure Portal でアプリケーション ビューを開き、ディレクトリ ビューに移動します。次に、**[エンタープライズ アプリケーション]** に移動し、**[すべてのアプリケーション]** をクリックします。

    ![ユーザーの割り当て][201] 

1. アプリケーションの一覧で **[Zoho One]** を選択します。

    ![アプリケーションの一覧の Zoho One のリンク](./media/zohoone-tutorial/tutorial_zohoone_app.png)  

1. 左側のメニューで **[ユーザーとグループ]** をクリックします。

    ![[ユーザーとグループ] リンク][202]

1. **[追加]** ボタンをクリックします。 次に、**[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。

    ![[割り当ての追加] ウィンドウ][203]

1. **[ユーザーとグループ]** ダイアログで、ユーザーの一覧から **[Britta Simon]** を選択します。

1. **[ユーザーとグループ]** ダイアログで **[選択]** をクリックします。

1. **[割り当ての追加]** ダイアログで **[割り当て]** ボタンをクリックします。
    
### <a name="test-single-sign-on"></a>シングル サインオンのテスト

このセクションでは、アクセス パネルを使用して Azure AD のシングル サインオン構成をテストします。

アクセス パネルで Zoho One のタイルをクリックすると、自動的に Zoho One アプリケーションにサインオンします。
アクセス パネルの詳細については、[アクセス パネルの概要](../user-help/active-directory-saas-access-panel-introduction.md)に関するページを参照してください。 

## <a name="additional-resources"></a>その他のリソース

* [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](tutorial-list.md)
* [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/zohoone-tutorial/tutorial_general_01.png
[2]: ./media/zohoone-tutorial/tutorial_general_02.png
[3]: ./media/zohoone-tutorial/tutorial_general_03.png
[4]: ./media/zohoone-tutorial/tutorial_general_04.png

[100]: ./media/zohoone-tutorial/tutorial_general_100.png

[200]: ./media/zohoone-tutorial/tutorial_general_200.png
[201]: ./media/zohoone-tutorial/tutorial_general_201.png
[202]: ./media/zohoone-tutorial/tutorial_general_202.png
[203]: ./media/zohoone-tutorial/tutorial_general_203.png

