---
title: "チュートリアル: Azure Active Directory と Asana の統合 | Microsoft Docs"
description: "Azure Active Directory と Asana の間でシングル サインオンを構成する方法について説明します。"
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 837e38fe-8f55-475c-87f4-6394dc1fee2b
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/01/2017
ms.author: jeedes
ms.openlocfilehash: a2f0cecb93cc382bcfd710c44eb70f80ae67f9b6
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="tutorial-azure-active-directory-integration-with-asana"></a>チュートリアル: Azure Active Directory と Asana の統合

このチュートリアルでは、Asana と Azure Active Directory (Azure AD) を統合する方法について説明します。

Asana と Azure AD の統合には、次の利点があります。

- Asana にアクセスする Azure AD ユーザーを制御できます。
- ユーザーが自分の Azure AD アカウントで Asana に自動的にサインオン (シングル サインオン) できるように、設定が可能です。
- 1 つの中央サイト (Azure Portal) でアカウントを管理できます

SaaS アプリと Azure AD の統合の詳細については、「[Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](active-directory-appssoaccess-whatis.md)」をご覧ください。

## <a name="prerequisites"></a>前提条件

Asana と Azure AD の統合を構成するには、次のものが必要です。

- Azure AD サブスクリプション
- Asana でのシングル サインオンが有効なサブスクリプション

> [!NOTE]
> このチュートリアルの手順をテストする場合、運用環境を使用しないことをお勧めします。

このチュートリアルの手順をテストするには、次の推奨事項に従ってください。

- 必要な場合を除き、運用環境は使用しないでください。
- Azure AD の評価環境がない場合は、[1 か月の評価版を入手できます](https://azure.microsoft.com/pricing/free-trial/)。

## <a name="scenario-description"></a>シナリオの説明
このチュートリアルでは、テスト環境で Azure AD のシングル サインオンをテストします。 このチュートリアルで説明するシナリオは、主に次の 2 つの要素で構成されています。

1. ギャラリーからの Asana の追加
2. Azure AD シングル サインオンの構成とテスト

## <a name="adding-asana-from-the-gallery"></a>ギャラリーからの Asana の追加
Azure AD への Asana の統合を構成するには、ギャラリーから管理対象 SaaS アプリの一覧に Asana を追加する必要があります。

**ギャラリーから Asana を追加するには、次の手順を実行します。**

1. **[Azure Portal](https://portal.azure.com)** の左側のナビゲーション ウィンドウで、**[Azure Active Directory]** アイコンをクリックします。 

    ![Azure Active Directory のボタン][1]

2. **[エンタープライズ アプリケーション]** に移動します。 次に、**[すべてのアプリケーション]** に移動します。

    ![[エンタープライズ アプリケーション] ブレード][2]
    
3. 新しいアプリケーションを追加するには、ダイアログの上部にある **[新しいアプリケーション]** をクリックします。

    ![[新しいアプリケーション] ボタン][3]

4. 検索ボックスに「**Asana**」と入力し、結果ウィンドウで **[Asana]** を選択し、**[追加]** をクリックして、アプリケーションを追加します。

    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-asana-tutorial/tutorial_asana_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成とテスト

このセクションでは、"Britta Simon" というテスト ユーザーに基づいて、Asana で Azure AD のシングル サインオンを構成し、テストします。

シングル サインオンを機能させるには、Azure AD ユーザーに対応する Asana ユーザーが Azure AD で認識されている必要があります。 言い換えると、Azure AD ユーザーと Asana の関連ユーザーの間で、リンク関係が確立されている必要があります。

Asana で、Azure AD の **[ユーザー名]** の値を **[Username]** の値として割り当ててリンク関係を確立します。

Asana で Azure AD のシングル サインオンを構成してテストするには、次の構成要素を完了する必要があります。

1. **[Azure AD シングル サインオンの構成](#configure-azure-ad-single-sign-on)** - ユーザーがこの機能を使用できるようにします。
2. **[Azure AD のテスト ユーザーの作成](#create-an-azure-ad-test-user)** - Britta Simon で Azure AD のシングル サインオンをテストします。
3. **[Asana のテスト ユーザーの作成](#create-an-asana-test-user)** - Asana で Britta Simon に対応するユーザーを作成し、Azure AD の Britta Simon にリンクさせます。
4. **[Azure AD テスト ユーザーの割り当て](#assign-the-azure-ad-test-user)** - Britta Simon が Azure AD シングル サインオンを使用できるようにします。
5. **[シングル サインオンのテスト](#test-single-sign-on)** - 構成が機能するかどうかを確認します。

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成

このセクションでは、Azure Portal で Azure AD のシングル サインオンを有効にし、Asana アプリケーションでシングル サインオンを構成します。

**Asana で Azure AD シングル サインオンを構成するには、次の手順に従います。**

1. Azure Portal の **Asana** アプリケーション統合ページで、**[シングル サインオン]** をクリックします。

    ![[シングル サインオンの構成]][4]

2. **[シングル サインオン]** ダイアログで、**[モード]** として **[SAML ベースのサインオン]** を選択し、シングル サインオンを有効にします。
 
    ![[シングル サインオン] ダイアログ ボックス](./media/active-directory-saas-asana-tutorial/tutorial_asana_samlbase.png)

3. **[Asana のドメインと URL]** セクションで、次の手順を実行します。

    ![[Asana のドメインと URL] のシングル サインオン情報](./media/active-directory-saas-asana-tutorial/tutorial_asana_url.png)

    a. **[サインオン URL]** ボックスに、URL として「`https://app.asana.com/`」と入力します。

    b. **[識別子]** ボックスに、値として「`https://app.asana.com/`」と入力します。
 
4. **[SAML 署名証明書]** セクションで、**[証明書 (Base64)]** をクリックし、コンピューターに証明書ファイルを保存します。

    ![証明書のダウンロードのリンク](./media/active-directory-saas-asana-tutorial/tutorial_asana_certificate.png)
    
5. **[保存]** ボタンをクリックします。

    ![[シングル サインオンの構成] の [保存] ボタン](./media/active-directory-saas-asana-tutorial/tutorial_general_400.png)

6. **[Asana Configuration (Asana 構成)]** セクションで、**[Configure Asana (Asana を構成する)]** をクリックして、**[サインオンの構成]** ウィンドウを開きます。 **[クイック リファレンス]** セクションから **SAML シングル サインオン サービスの URL** をコピーします。

    ![Asana 構成](./media/active-directory-saas-asana-tutorial/tutorial_asana_configure.png) 

7. 別のブラウザー ウィンドウで、Asana アプリケーションにサインオンします。 Asana で SSO を構成するために、画面の右上隅にあるワークスペース名をクリックして、ワークスペースの設定にアクセスします。 その後、**[\<your workspace name\> Settings (<ワークスペース名> 設定)]** をクリックします。 
   
    ![Asana SSO 設定](./media/active-directory-saas-asana-tutorial/tutorial_asana_09.png)

8. **[Organization settings (組織の設定)]** ウィンドウで **[Administration (管理)]** をクリックします。 その後、 **[Members must log in via SAML (メンバーは SAML でログインする必要がある)]** をクリックして SSO 構成を有効にします。 次の手順に従います。
   
    ![[組織の設定] のシングル サインオン構成](./media/active-directory-saas-asana-tutorial/tutorial_asana_10.png)  

     a. **[Sign-in page URL (サインイン ページ URL)]** ボックスに、**SAML シングル サインオン サービス URL** を貼り付けます。

     b. Azure Portal からダウンロードした証明書を右クリックし、メモ帳またはお好きなテキスト エディターを使って証明書ファイルを開きます。 証明書タイトルの内容を最初から最後までコピーし、**[X.509 Certificate]** (X.509 証明書) ボックスに貼り付けます。

9. [ **Save**] をクリックします。 さらにサポートが必要な場合は、 [SSO の設定に関する Asana ガイド](https://asana.com/guide/help/premium/authentication#gl-saml) をご覧ください。

> [!TIP]
> アプリのセットアップ中、[Azure Portal](https://portal.azure.com) 内で上記の手順の簡易版を確認できるようになりました。  **[Active Directory] の [エンタープライズ アプリケーション]** セクションからこのアプリを追加した後、**[シングル サインオン]** タブをクリックし、一番下の **[構成]** セクションから組み込みドキュメントにアクセスするだけです。 組み込みドキュメント機能の詳細については、[Azure AD の組み込みドキュメント]( https://go.microsoft.com/fwlink/?linkid=845985)に関する記事をご覧ください。

### <a name="create-an-azure-ad-test-user"></a>Azure AD のテスト ユーザーの作成

このセクションの目的は、Azure Portal で Britta Simon というテスト ユーザーを作成することです。

![Azure AD のテスト ユーザーの作成][100]

**Azure AD でテスト ユーザーを作成するには、次の手順に従います。**

1. **Azure Portal** の左側のナビゲーション ウィンドウで、**[Azure Active Directory]** アイコンをクリックします。

    ![Azure Active Directory のボタン](./media/active-directory-saas-asana-tutorial/create_aaduser_01.png) 

2. **[ユーザーとグループ]** に移動し、**[すべてのユーザー]** をクリックして、ユーザーの一覧を表示します。
    
    ![[ユーザーとグループ] と [すべてのユーザー] リンク](./media/active-directory-saas-asana-tutorial/create_aaduser_02.png) 

3. ダイアログの上部にある **[追加]** をクリックして、**[ユーザー]** ダイアログを開きます。
 
    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-asana-tutorial/create_aaduser_03.png) 

4. **[ユーザー]** ダイアログ ページで、次の手順を実行します。
 
    ![[追加] ボタン](./media/active-directory-saas-asana-tutorial/create_aaduser_04.png) 

    a. **[名前]** ボックスに「**BrittaSimon**」と入力します。

    b. **[ユーザー名]** ボックスに BrittaSimon の**電子メール アドレス**を入力します。

    c. **[パスワードを表示]** を選択し、**[パスワード]** の値をメモします。

    d. **Create** をクリックしてください。
 
### <a name="create-an-asana-test-user"></a>Asana テスト ユーザーの作成

このセクションでは、Asana で Britta Simon というユーザーを作成します。

1. **Asana** の左側のパネルで、**[Teams (チーム)]** セクションに移動します。 プラス記号ボタンをクリックします。 
   
    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-asana-tutorial/tutorial_asana_12.png) 

2. 電子メール アドレス「 britta.simon@contoso.com 」をテキスト ボックスに入力し、 **[Invite (招待)]**を選択します。

3. **[Send Invite (招待の送信)]**をクリックします。 新しいユーザーの電子メール アカウントに電子メールが届きます。 新しいユーザーは、アカウントを作成、確認する必要があります。

### <a name="assign-the-azure-ad-test-user"></a>Azure AD テスト ユーザーの割り当て

このセクションでは、Asana へのアクセスを許可することで、Britta Simon が Azure シングル サインオンを使用できるようにします。

![ユーザー ロールを割り当てる][200]

**Asana に Britta Simon を割り当てるには、次の手順を実行します。**

1. Azure Portal でアプリケーション ビューを開き、ディレクトリ ビューに移動します。次に、**[エンタープライズ アプリケーション]** に移動し、**[すべてのアプリケーション]** をクリックします。

    ![ユーザーの割り当て][201] 

2. アプリケーションの一覧で **[Asana]**を選択します。

    ![アプリケーションの一覧の Asana のリンク](./media/active-directory-saas-asana-tutorial/tutorial_asana_app.png) 

3. 左側のメニューで **[ユーザーとグループ]** をクリックします。

    ![[ユーザーとグループ] リンク][202]

4. **[追加]** ボタンをクリックします。 次に、**[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。

    ![[割り当ての追加] ウィンドウ][203]

5. **[ユーザーとグループ]** ダイアログで、ユーザーの一覧から **[Britta Simon]** を選択します。

6. **[ユーザーとグループ]** ダイアログで **[選択]** をクリックします。

7. **[割り当ての追加]** ダイアログで **[割り当て]** ボタンをクリックします。
    
### <a name="test-single-sign-on"></a>シングル サインオンのテスト

このセクションの目的は、Azure AD のシングル サインオンをテストすることです。

Asana のログイン ページに移動します。 電子メール アドレスのテキスト ボックスに、電子メール アドレスとして「britta.simon@contoso.com」と入力します。パスワード ボックスは空白のままにして **[Log In (ログイン)]** をクリックします。 Azure AD ログイン ページにリダイレクトされます。 Azure AD 資格情報をすべて入力します。 これで、Asana へのログインが完了しました。

## <a name="additional-resources"></a>その他のリソース

* [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](active-directory-saas-tutorial-list.md)
* [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](active-directory-appssoaccess-whatis.md)


<!--Image references-->

[1]: ./media/active-directory-saas-asana-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-asana-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-asana-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-asana-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-asana-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-asana-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-asana-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-asana-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-asana-tutorial/tutorial_general_203.png
[10]: ./media/active-directory-saas-asana-tutorial/tutorial_general_060.png
[11]: ./media/active-directory-saas-asana-tutorial/tutorial_general_070.png
