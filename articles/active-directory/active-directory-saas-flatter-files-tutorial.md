---
title: "チュートリアル: Azure Active Directory と Flatter Files の統合 | Microsoft Docs"
description: "Azure Active Directory と Flatter Files の間でシングル サインオンを構成する方法について説明します。"
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.assetid: f86fe5e3-0e91-40d6-869c-3df6912d27ea
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/21/2017
ms.author: jeedes
ms.translationtype: Human Translation
ms.sourcegitcommit: 31ecec607c78da2253fcf16b3638cc716ba3ab89
ms.openlocfilehash: e02150cb27768d7b403bdca191bc1f189821def4
ms.contentlocale: ja-jp
ms.lasthandoff: 06/23/2017


---
# <a name="tutorial-azure-active-directory-integration-with-flatter-files"></a>チュートリアル: Azure Active Directory と Flatter Files の統合

このチュートリアルでは、Flatter Files と Azure Active Directory (Azure AD) を統合する方法について説明します。

Flatter Files と Azure AD の統合には、次の利点があります。

- Flatter Files にアクセスする Azure AD ユーザーを制御できます。
- ユーザーが自分の Azure AD アカウントで自動的に Flatter Files にサインオン (シングル サインオン) できるようにします。
- 1 つの中央サイト (Azure Portal) でアカウントを管理できます

SaaS アプリと Azure AD の統合の詳細については、「[Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](active-directory-appssoaccess-whatis.md)」をご覧ください。

## <a name="prerequisites"></a>前提条件

Flatter Files と Azure AD の統合を構成するには、次のものが必要です。

- Azure AD サブスクリプション
- Flatter Files でのシングル サインオンが有効なサブスクリプション

> [!NOTE]
> このチュートリアルの手順をテストする場合、運用環境を使用しないことをお勧めします。

このチュートリアルの手順をテストするには、次の推奨事項に従ってください。

- 必要な場合を除き、運用環境は使用しないでください。
- Azure AD の評価環境がない場合は、 [こちら](https://azure.microsoft.com/pricing/free-trial/)から 1 か月の評価版を入手できます。

## <a name="scenario-description"></a>シナリオの説明
このチュートリアルでは、テスト環境で Azure AD のシングル サインオンをテストします。 このチュートリアルで説明するシナリオは、主に次の 2 つの要素で構成されています。

1. ギャラリーからの Flatter Files の追加
2. Azure AD シングル サインオンの構成とテスト

## <a name="adding-flatter-files-from-the-gallery"></a>ギャラリーからの Flatter Files の追加
Azure AD への Flatter Files の統合を構成するには、ギャラリーから管理対象 SaaS アプリの一覧に Flatter Files を追加する必要があります。

**ギャラリーから Flatter Files を追加するには、次の手順に従います。**

1. **[Azure Portal](https://portal.azure.com)** の左側のナビゲーション ウィンドウで、**[Azure Active Directory]** アイコンをクリックします。 

    ![Active Directory][1]

2. **[エンタープライズ アプリケーション]** に移動します。 次に、**[すべてのアプリケーション]** に移動します。

    ![アプリケーション][2]
    
3. 新しいアプリケーションを追加するには、ダイアログの上部にある **[新しいアプリケーション]** をクリックします。

    ![アプリケーション][3]

4. 検索ボックスに、「 **Flatter Files**」と入力します。

    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-flatter-files-tutorial/tutorial_flatterfiles_search.png)

5. 結果ウィンドウで **Flatter Files** を選択し、**[追加]** をクリックして、アプリケーションを追加します。

    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-flatter-files-tutorial/tutorial_flatterfiles_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成とテスト
このセクションでは、"Britta Simon" というテスト ユーザーに基づいて、Flatter Files で Azure AD のシングル サインオンを構成し、テストします。

シングル サインオンを機能させるには、Azure AD ユーザーに対応する Flatter Files ユーザーが Azure AD で認識されている必要があります。 言い換えると、Azure AD ユーザーと Flatter Files の関連ユーザーの間で、リンク関係が確立されている必要があります。

Flatter Files、Azure AD の **[ユーザー名]** の値を **[Username]** の値として割り当ててリンク関係を確立します。

Flatter Files で Azure AD のシングル サインオンを構成してテストするには、次の構成要素を完了する必要があります。

1. **[Configuring Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)** - ユーザーがこの機能を使用できるようにします。
2. **[Azure AD のテスト ユーザーの作成](#creating-an-azure-ad-test-user)** - Britta Simon で Azure AD のシングル サインオンをテストします。
3. **[Flatter Files テスト ユーザーの作成](#creating-a-flatter-files-test-user)** - Flatter Files で Britta Simon に対応するユーザーを作成し、Azure AD の Britta Simon にリンクさせます。
4. **[Azure AD テスト ユーザーの割り当て](#assigning-the-azure-ad-test-user)** - Britta Simon が Azure AD のシングル サインオンを使用できるようにします。
5. **[Testing Single Sign-On](#testing-single-sign-on)** - 構成が機能するかどうかを確認します。

### <a name="configuring-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成

このセクションでは、Azure Portal で Azure AD のシングル サインオンを有効にして、Flatter Files アプリケーションでシングル サインオンを構成します。

**Flatter Files で Azure AD シングル サインオンを構成するには、次の手順に従います。**

1. Azure Portal の **Flatter Files** アプリケーション統合ページで、**[シングル サインオン]** をクリックします。

    ![[シングル サインオンの構成]][4]

2. **[シングル サインオン]** ダイアログで、**[モード]** として **[SAML ベースのサインオン]** を選択し、シングル サインオンを有効にします。
 
    ![[シングル サインオンの構成]](./media/active-directory-saas-flatter-files-tutorial/tutorial_flatterfiles_samlbase.png)

3. アプリは Azure と事前に統合済みであるため、**[Flatter Files のドメインと URL]** セクションで特に手順を実施する必要はありません。

    ![[シングル サインオンの構成]](./media/active-directory-saas-flatter-files-tutorial/tutorial_flatterfiles_url.png)
 
4. **[SAML 署名証明書]** セクションで、**[証明書 (Base64)]** をクリックし、コンピューターに証明書ファイルを保存します。

    ![[シングル サインオンの構成]](./media/active-directory-saas-flatter-files-tutorial/tutorial_flatterfiles_certificate.png) 

5. **[保存]** ボタンをクリックします。

    ![[シングル サインオンの構成]](./media/active-directory-saas-flatter-files-tutorial/tutorial_general_400.png)

6. **[Flatter Files 構成]** セクションで、**[Flatter Files の構成]** をクリックして、**[サインオンの構成]** ウィンドウを開きます。 **[クイック リファレンス]** セクションから **SAML シングル サインオン サービスの URL** をコピーします。

    ![[シングル サインオンの構成]](./media/active-directory-saas-flatter-files-tutorial/tutorial_flatterfiles_configure.png) 

7. 管理者として Flatter Files アプリケーションにサインオンします。

8. **[DASHBOARD]\(ダッシュボード\)** をクリックします。 
   
    ![[シングル サインオンの構成]](./media/active-directory-saas-flatter-files-tutorial/tutorial_flatter_files_05.png)  

9. **[設定]** をクリックし、**[会社]** タブで次の手順に従います。 
   
    ![Configure Single Sign-On](./media/active-directory-saas-flatter-files-tutorial/tutorial_flatter_files_06.png)  
    
    a. **[Use SAML 2.0 for Authentication]** を選択します。
    
    b. **[Configure SAML]** をクリックします。

8. **[SAML Configuration]** ダイアログ ボックスで、次の手順を実行します。 
   
    ![Configure Single Sign-On](./media/active-directory-saas-flatter-files-tutorial/tutorial_flatter_files_08.png)  
   
    a. **[ドメイン]** ボックスに、登録済みのドメインを入力します。
   
    >[!NOTE]
    >登録済みのドメインがない場合は、Flatter Files のサポート チーム ( [support@flatterfiles.com](mailto:support@flatterfiles.com)」を参照してください。 
    
    b. **[Identity Provider URL]\(ID プロバイダー URL\)** ボックスに、Azure Portal からコピーした **SAML シングル サインオン サービス URL** の値を貼り付けます。
   
    c.  base-64 でエンコードされた証明書をメモ帳で開き、その内容をクリップボードにコピーして **[ID プロバイダー証明書]** テキスト ボックスに貼り付けます。

    d. **[Update]**をクリックします。

> [!TIP]
> アプリのセットアップ中、[Azure Portal](https://portal.azure.com) 内で上記の手順の簡易版を確認できるようになりました。  **[Active Directory] の [エンタープライズ アプリケーション]** セクションからこのアプリを追加した後、**[シングル サインオン]** タブをクリックし、一番下の **[構成]** セクションから組み込みドキュメントにアクセスするだけです。 組み込みドキュメント機能の詳細については、[Azure AD の組み込みドキュメント]( https://go.microsoft.com/fwlink/?linkid=845985)に関する記事をご覧ください。


### <a name="creating-an-azure-ad-test-user"></a>Azure AD のテスト ユーザーの作成
このセクションの目的は、Azure Portal で Britta Simon というテスト ユーザーを作成することです。

![Azure AD ユーザーの作成][100]

**Azure AD でテスト ユーザーを作成するには、次の手順に従います。**

1. **Azure Portal** の左側のナビゲーション ウィンドウで、**[Azure Active Directory]** アイコンをクリックします。

    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-flatter-files-tutorial/create_aaduser_01.png) 

2. **[ユーザーとグループ]** に移動し、**[すべてのユーザー]** をクリックして、ユーザーの一覧を表示します。
    
    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-flatter-files-tutorial/create_aaduser_02.png) 

3. ダイアログの上部にある **[追加]** をクリックして、**[ユーザー]** ダイアログを開きます。
 
    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-flatter-files-tutorial/create_aaduser_03.png) 

4. **[ユーザー]** ダイアログ ページで、次の手順を実行します。
 
    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-flatter-files-tutorial/create_aaduser_04.png) 

    a. **[名前]** ボックスに「**BrittaSimon**」と入力します。

    b. **[ユーザー名]** ボックスに BrittaSimon の**電子メール アドレス**を入力します。

    c. **[パスワードを表示]** を選択し、**[パスワード]** の値をメモします。

    d. ページの下部にある **[Create]**」を参照してください。
 
### <a name="creating-a-flatter-files-test-user"></a>Flatter Files テスト ユーザーの作成

このセクションの目的は、Flatter Files で Britta Simon というユーザーを作成することです。

**Flatter Files で Britta Simon というユーザーを作成するには、次の手順に従います。**

1. **Flatter Files** 企業サイトに管理者としてサインオンします。

2. 左側のナビゲーション ウィンドウで、**[Settings]\(設定\)**、**[Users]\(ユーザー\)** タブの順にクリックします。
   
    ![Flatter Files ユーザーの作成](./media/active-directory-saas-flatter-files-tutorial/tutorial_flatter_files_09.png)

3. **[ユーザーの追加]**をクリックします。 

4. **[Add User]** ダイアログで、次の手順を実行します。
   
    ![Flatter Files ユーザーの作成](./media/active-directory-saas-flatter-files-tutorial/tutorial_flatter_files_10.png)

    a. **[名]** ボックスに「**Britta**」と入力します。
   
    b. **[姓]** ボックスに「**Simon**」と入力します。 
   
    c. **[Email Address]\(電子メール アドレス\)** ボックスに、Britta の Azure Portal の電子メール アドレスを入力します。
   
    d. **[送信]**をクリックします。   


### <a name="assigning-the-azure-ad-test-user"></a>Azure AD テスト ユーザーの割り当て

このセクションでは、Britta Simon に Flatter Files へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

![ユーザーの割り当て][200] 

**Flatter Files に Britta Simon を割り当てるには、次の手順に従います。**

1. Azure Portal でアプリケーション ビューを開き、ディレクトリ ビューに移動します。次に、**[エンタープライズ アプリケーション]** に移動し、**[すべてのアプリケーション]** をクリックします。

    ![ユーザーの割り当て][201] 

2. アプリケーションの一覧で **[Flatter Files]**を選択します。

    ![[シングル サインオンの構成]](./media/active-directory-saas-flatter-files-tutorial/tutorial_flatterfiles_app.png) 

3. 左側のメニューで **[ユーザーとグループ]** をクリックします。

    ![ユーザーの割り当て][202] 

4. **[追加]** ボタンをクリックします。 次に、**[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。

    ![ユーザーの割り当て][203]

5. **[ユーザーとグループ]** ダイアログで、ユーザーの一覧から **[Britta Simon]** を選択します。

6. **[ユーザーとグループ]** ダイアログで **[選択]** をクリックします。

7. **[割り当ての追加]** ダイアログで **[割り当て]** ボタンをクリックします。
    
### <a name="testing-single-sign-on"></a>シングル サインオンのテスト

このセクションでは、アクセス パネルを使用して Azure AD のシングル サインオン構成をテストします。

アクセス パネルで [Flatter Files] タイルをクリックすると、自動的に Flatter Files アプリケーションにサインオンします。
アクセス パネルの詳細については、[アクセス パネルの概要](active-directory-saas-access-panel-introduction.md)に関する記事を参照してください。

## <a name="additional-resources"></a>その他のリソース

* [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](active-directory-saas-tutorial-list.md)
* [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-flatter-files-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-flatter-files-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-flatter-files-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-flatter-files-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-flatter-files-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-flatter-files-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-flatter-files-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-flatter-files-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-flatter-files-tutorial/tutorial_general_203.png


