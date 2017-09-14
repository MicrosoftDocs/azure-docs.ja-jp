---
title: "チュートリアル: Azure Active Directory と Bonusly の統合 | Microsoft Docs"
description: "Azure Active Directory と Bonusly の間でシングル サインオンを構成する方法について説明します。"
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 29fea32a-fa20-47b2-9e24-26feb47b0ae6
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/19/2017
ms.author: jeedes
ms.translationtype: HT
ms.sourcegitcommit: 74b75232b4b1c14dbb81151cdab5856a1e4da28c
ms.openlocfilehash: 29a88b2efdb9f0f33f7933bc654a5a0fdf589c5a
ms.contentlocale: ja-jp
ms.lasthandoff: 07/26/2017

---
# <a name="tutorial-azure-active-directory-integration-with-bonusly"></a>チュートリアル: Azure Active Directory と Bonusly の統合

このチュートリアルでは、Bonusly と Azure Active Directory (Azure AD) を統合する方法について説明します。

Bonusly と Azure AD の統合には、次の利点があります。

- Bonusly にアクセスする Azure AD ユーザーを制御できます。
- ユーザーが自分の Azure AD アカウントで Bonusly に自動的にサインオン (シングル サインオン) できるように、設定が可能です。
- 1 つの中央サイト (Azure Portal) でアカウントを管理できます

SaaS アプリと Azure AD の統合の詳細については、「[Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](active-directory-appssoaccess-whatis.md)」をご覧ください。

## <a name="prerequisites"></a>前提条件

Bonusly と Azure AD の統合を構成するには、次のものが必要です。

- Azure AD サブスクリプション
- Bonusly でのシングル サインオンが有効なサブスクリプション

> [!NOTE]
> このチュートリアルの手順をテストする場合、運用環境を使用しないことをお勧めします。

このチュートリアルの手順をテストするには、次の推奨事項に従ってください。

- 必要な場合を除き、運用環境は使用しないでください。
- Azure AD の評価環境がない場合は、[1 か月の評価版を入手できます](https://azure.microsoft.com/pricing/free-trial/)。

## <a name="scenario-description"></a>シナリオの説明
このチュートリアルでは、テスト環境で Azure AD のシングル サインオンをテストします。 このチュートリアルで説明するシナリオは、主に次の 2 つの要素で構成されています。

1. ギャラリーからの Bonusly の追加
2. Azure AD シングル サインオンの構成とテスト

## <a name="adding-bonusly-from-the-gallery"></a>ギャラリーからの Bonusly の追加
Azure AD への Bonusly の統合を構成するには、ギャラリーから管理対象 SaaS アプリの一覧に Bonusly を追加する必要があります。

**ギャラリーから Bonusly を追加するには、次の手順を実行します。**

1. **[Azure Portal](https://portal.azure.com)** の左側のナビゲーション ウィンドウで、**[Azure Active Directory]** アイコンをクリックします。 

    ![Azure Active Directory のボタン][1]

2. **[エンタープライズ アプリケーション]** に移動します。 次に、**[すべてのアプリケーション]** に移動します。

    ![[エンタープライズ アプリケーション] ブレード][2]
    
3. 新しいアプリケーションを追加するには、ダイアログの上部にある **[新しいアプリケーション]** をクリックします。

    ![[新しいアプリケーション] ボタン][3]

4. 検索ボックスに「**Bonusly**」と入力し、結果ウィンドウで **Bonusly** を選び、**[追加]** をクリックして、アプリケーションを追加します。

    ![結果一覧の Bonusly](./media/active-directory-saas-bonus-tutorial/tutorial_bonusly_addfromgallery.png)

##  <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成とテスト
このセクションでは、"Britta Simon" というテスト ユーザーに基づいて、Bonusly で Azure AD のシングル サインオンを構成し、テストします。

シングル サインオンを機能させるには、Azure AD ユーザーに対応する Bonusly ユーザーが Azure AD で認識されている必要があります。 言い換えると、Azure AD ユーザーと Bonusly の関連ユーザーの間で、リンク関係が確立されている必要があります。

Bonusly で、Azure AD の **[ユーザー名]** の値を **[Username]\(ユーザー名\)** の値として割り当ててリンク関係を確立します。

Bonusly で Azure AD のシングル サインオンを構成してテストするには、次の手順を完了する必要があります。

1. **[Azure AD シングル サインオンの構成](#configure-azure-ad-single-sign-on)** - ユーザーがこの機能を使用できるようにします。
2. **[Azure AD のテスト ユーザーの作成](#create-an-azure-ad-test-user)** - Britta Simon で Azure AD のシングル サインオンをテストします。
3. **[Bonusly のテスト ユーザーの作成](#create-a-bonusly-test-user)** - Bonusly で Britta Simon に対応するユーザーを作成し、Azure AD の Britta Simon にリンクさせます。
4. **[Azure AD テスト ユーザーの割り当て](#assign-the-azure-ad-test-user)** - Britta Simon が Azure AD シングル サインオンを使用できるようにします。
5. **[シングル サインオンのテスト](#test-single-sign-on)** - 構成が機能するかどうかを確認します。

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成

このセクションでは、Azure Portal で Azure AD のシングル サインオンを有効にし、Bonusly アプリケーションでシングル サインオンを構成します。

**Bonusly で Azure AD シングル サインオンを構成するには、次の手順を実行します。**

1. Azure Portal の **Bonusly** アプリケーション統合ページで、**[シングル サインオン]** をクリックします。

    ![[シングル サインオンの構成]][4]

2. **[シングル サインオン]** ダイアログで、**[モード]** として **[SAML ベースのサインオン]** を選択し、シングル サインオンを有効にします。
 
    ![[シングル サインオン] ダイアログ ボックス](./media/active-directory-saas-bonus-tutorial/tutorial_bonusly_samlbase.png)

3. **[Bonusly のドメインと URL]** セクションで、次の手順に従います。

    ![[Bonusly のドメインと URL] のシングル サインオン情報](./media/active-directory-saas-bonus-tutorial/tutorial_bonusly_url.png)

    **[応答 URL]** ボックスに、`https://Bonus.ly/saml/<tenant-name>` のパターンを使用して URL を入力します。

    > [!NOTE] 
    > この値は実際のものではありません。 実際の応答 URL でこの値を更新します。 この値を取得するには、[Bonusly サポート チーム](https://Bonusly/contact)に連絡してください。
 
4. **[SAML 署名証明書]** セクションで、証明書の **[拇印]** の値をコピーします。

    ![証明書のダウンロードのリンク](./media/active-directory-saas-bonus-tutorial/tutorial_bonusly_certificate.png) 

5. **[保存]** ボタンをクリックします。

    ![[シングル サインオンの構成] の [保存] ボタン](./media/active-directory-saas-bonus-tutorial/tutorial_general_400.png)

6. **[Bonusly 構成]** セクションで、**[Bonusly の構成]** をクリックして、**[サインオンの構成]** ウィンドウを開きます。 **[クイック リファレンス] セクション**から、**SAML エンティティ ID と SAML Single シングル サインオン サービス URL** をコピーします。

    ![Bonusly の構成](./media/active-directory-saas-bonus-tutorial/tutorial_bonusly_configure.png) 

7. 別のブラウザー ウィンドウで、**Bonusly** テナントにログインします。

8. 上部のツール バーの **[Settings]** をクリックし、**[Integrations and apps]** を選択します。
   
    ![Bonusly ソーシャル セクション](./media/active-directory-saas-bonus-tutorial/ic773686.png "Bonusly")
9. **[Single Sign-On]** の **[SAML]** を選択します。

10. **[SAML]** ダイアログ ページで、次の手順を実行します。
   
    ![Bonusly SAML ダイアログ ページ](./media/active-directory-saas-bonus-tutorial/ic773687.png "Bonusly")
   
    a. **[IdP SSO target URL]\(IdP SSO ターゲット URL\)** ボックスに、Azure Portal からコピーした **SAML シングル サインオン サービス URL** の値を貼り付けます。
   
    b. **[IdP Issuer]\(IdP 発行者\)** テキストボックスに、Azure Portal からコピーした **SAML エンティティ ID** の値を貼り付けます。 

    c. **[IdP Login URL]\(IdP ログイン URL\)** ボックスに、Azure Portal からコピーした **SAML シングル サインオン サービス URL** の値を貼り付けます。

    d. Azure Portal からコピーした**拇印**の値を、**[Cert Fingerprint]\(証明書の指紋\)** ボックスに貼り付けます。
   
11. [ **Save**] をクリックします。

> [!TIP]
> アプリのセットアップ中、[Azure Portal](https://portal.azure.com) 内で上記の手順の簡易版を確認できるようになりました。  **[Active Directory] の [エンタープライズ アプリケーション]** セクションからこのアプリを追加した後、**[シングル サインオン]** タブをクリックし、一番下の **[構成]** セクションから組み込みドキュメントにアクセスするだけです。 組み込みドキュメント機能の詳細については、[Azure AD の組み込みドキュメント]( https://go.microsoft.com/fwlink/?linkid=845985)に関する記事をご覧ください。
> 

### <a name="create-an-azure-ad-test-user"></a>Azure AD のテスト ユーザーの作成
このセクションの目的は、Azure Portal で Britta Simon というテスト ユーザーを作成することです。

![Azure AD のテスト ユーザーの作成][100]

**Azure AD でテスト ユーザーを作成するには、次の手順に従います。**

1. **Azure Portal** の左側のナビゲーション ウィンドウで、**[Azure Active Directory]** アイコンをクリックします。

    ![Azure Active Directory のボタン](./media/active-directory-saas-bonus-tutorial/create_aaduser_01.png) 

2. **[ユーザーとグループ]** に移動し、**[すべてのユーザー]** をクリックして、ユーザーの一覧を表示します。
    
    ![[ユーザーとグループ] と [すべてのユーザー] リンク](./media/active-directory-saas-bonus-tutorial/create_aaduser_02.png) 

3. ダイアログの上部にある **[追加]** をクリックして、**[ユーザー]** ダイアログを開きます。
 
    ![[追加] ボタン](./media/active-directory-saas-bonus-tutorial/create_aaduser_03.png) 

4. **[ユーザー]** ダイアログ ページで、次の手順を実行します。
 
    ![[ユーザー] ダイアログ ボックス](./media/active-directory-saas-bonus-tutorial/create_aaduser_04.png) 

    a. **[名前]** ボックスに「**BrittaSimon**」と入力します。

    b. **[ユーザー名]** ボックスに BrittaSimon の**電子メール アドレス**を入力します。

    c. **[パスワードを表示]** を選択し、**[パスワード]** の値をメモします。

    d. **[作成]**をクリックします。
 
### <a name="create-a-bonusly-test-user"></a>Bonusly テスト ユーザーの作成

Azure AD ユーザーが Bonusly にログインできるようにするには、ユーザーを Bonusly にプロビジョニングする必要があります。 Bonusly の場合、プロビジョニングは手動で行います。

>[!NOTE]
>Bonusly から提供されている他の Bonusly ユーザー アカウント作成ツールまたは API を使用して、AAD ユーザー アカウントをプロビジョニングできます。
>  

**ユーザー プロビジョニングを構成するには、次の手順に従います。**

1. Web ブラウザー ウィンドウで、Bonusly テナントにログインします。

2. **[設定]**をクリックします。
 
    ![設定](./media/active-directory-saas-bonus-tutorial/ic781041.png "Settings")

3. **[Users and bonuses]** タブをクリックします。
   
    ![Users and bonuses](./media/active-directory-saas-bonus-tutorial/ic781042.png "Users and bonuses")

4. **[Manage Users]**をクリックします。
   
    ![Manage Users](./media/active-directory-saas-bonus-tutorial/ic781043.png "Manage Users")

5. **[ユーザーの追加]**をクリックします。
   
    ![ユーザーの追加](./media/active-directory-saas-bonus-tutorial/ic781044.png "Add User")

6. **[Add User]** ダイアログで、次の手順を実行します。
   
    ![ユーザーの追加](./media/active-directory-saas-bonus-tutorial/ic781045.png "Add User")  

    a. **[First name]\(名\)** ボックスに、ユーザーの名前を入力します (この例では **Britta**)。

    b. **[Last name]\(姓\)** ボックスに、ユーザーの姓を入力します (この例では **Simon**)。
 
    c. **[電子メール]** テキスト ボックスに、ユーザーの電子メール (**brittasimon@contoso.com** など) を入力します。

    d. [ **Save**] をクリックします。
   
     >[!NOTE]
     >アカウントがアクティブになる前に、Azure AD アカウント所有者に、アカウント確認用のリンクを含む電子メールが送信されます。
     >  

### <a name="assign-the-azure-ad-test-user"></a>Azure AD テスト ユーザーの割り当て

このセクションでは、Britta Simon に Bonusly へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようします。

![ユーザー ロールを割り当てる][200] 

**Britta Simon を Bonusly に割り当てるには、次の手順を実行します。**

1. Azure Portal でアプリケーション ビューを開き、ディレクトリ ビューに移動します。次に、**[エンタープライズ アプリケーション]** に移動し、**[すべてのアプリケーション]** をクリックします。

    ![ユーザーの割り当て][201] 

2. アプリケーションの一覧で **[Bonusly]** を選択します。

    ![アプリケーションの一覧の Bonusly のリンク](./media/active-directory-saas-bonus-tutorial/tutorial_bonusly_app.png) 

3. 左側のメニューで **[ユーザーとグループ]** をクリックします。

    ![[ユーザーとグループ] リンク][202] 

4. **[追加]** ボタンをクリックします。 次に、**[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。

    ![[割り当ての追加] ウィンドウ][203]

5. **[ユーザーとグループ]** ダイアログで、ユーザーの一覧から **[Britta Simon]** を選択します。

6. **[ユーザーとグループ]** ダイアログで **[選択]** をクリックします。

7. **[割り当ての追加]** ダイアログで **[割り当て]** ボタンをクリックします。
    
### <a name="test-single-sign-on"></a>シングル サインオンのテスト

このセクションの目的は、アクセス パネルを使用して Azure AD のシングル サインオン構成をテストすることです。

アクセス パネルで [Bonusly] タイルをクリックすると、Bonusly アプリケーションに自動的にサインオンします。

## <a name="additional-resources"></a>その他のリソース

* [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](active-directory-saas-tutorial-list.md)
* [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-bonus-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-bonus-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-bonus-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-bonus-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-bonus-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-bonus-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-bonus-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-bonus-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-bonus-tutorial/tutorial_general_203.png


