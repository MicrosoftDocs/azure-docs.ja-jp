---
title: "チュートリアル: Azure Active Directory と RFPIO の統合 | Microsoft Docs"
description: "Azure Active Directory と RFPIO の間でシングル サインオンを構成する方法について説明します。"
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.assetid: 87187076-7b50-4247-814f-f217b052703f
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/16/2017
ms.author: jeedes
ms.translationtype: Human Translation
ms.sourcegitcommit: 3716c7699732ad31970778fdfa116f8aee3da70b
ms.openlocfilehash: 26a8bb17dad5a01b401ce7f9b484f09822825cbf
ms.contentlocale: ja-jp
ms.lasthandoff: 06/30/2017


---
# <a name="tutorial-azure-active-directory-integration-with-rfpio"></a>チュートリアル: Azure Active Directory と RFPIO の統合

このチュートリアルでは、RFPIO と Azure Active Directory (Azure AD) を統合する方法について説明します。

RFPIO と Azure AD の統合には、次の利点があります。

- RFPIO にアクセスする Azure AD ユーザーを制御できます。
- ユーザーが自分の Azure AD アカウントで自動的に RFPIO にサインオン (シングル サインオン) できるようにすることが可能です。
- 1 つの中央サイト (Azure Portal) でアカウントを管理できます。

SaaS アプリと Azure AD の統合の詳細については、「 [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](active-directory-appssoaccess-whatis.md)」を参照してください。

## <a name="prerequisites"></a>前提条件

RFPIO と Azure AD の統合を構成するには、次のものが必要です。

- Azure AD サブスクリプション。
- RFPIO でのシングル サインオンが有効なサブスクリプション。

> [!NOTE]
> このチュートリアルの手順をテストする場合、運用環境を使用しないことをお勧めします。

このチュートリアルの手順をテストするには、次の推奨事項に従います。

- 必要な場合を除き、運用環境は使用しないでください。
- Azure AD の評価環境がない場合は、[1 か月の試用版](https://azure.microsoft.com/pricing/free-trial/)を入手できます。

## <a name="scenario-description"></a>シナリオの説明
このチュートリアルでは、テスト環境で Azure AD のシングル サインオンをテストします。 このチュートリアルで説明するシナリオは、主に次の 2 つの要素で構成されています。

1. ギャラリーからの RFPIO の追加。
2. Azure AD シングル サインオンの構成とテスト。

## <a name="add-rfpio-from-the-gallery"></a>ギャラリーからの RFPIO の追加
Azure AD への RFPIO の統合を構成するには、ギャラリーから管理対象 SaaS アプリの一覧に RFPIO を追加する必要があります。

### <a name="to-add-rfpio-from-the-gallery"></a>ギャラリーから RFPIO を追加する方法

1. **[Azure portal](https://portal.azure.com)** の左側のナビゲーション ウィンドウで、**[Azure Active Directory]** アイコンを選択します。 

    ![Active Directory][1]

2. **[エンタープライズ アプリケーション]** を選択し、**[すべてのアプリケーション]** を選択します。

    ![アプリケーション][2]
    
3. 新しいアプリケーションを追加するには、ダイアログ ボックスの上部にある **[新しいアプリケーション]** ボタンを選択します。

    ![アプリケーション][3]

4. 検索ボックスに、「**RFPIO**」と入力します。

    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-rfpio-tutorial/tutorial_rfpio_search.png)

5. 結果パネルで **[RFPIO]** を選択し、**[追加]** ボタンをクリックして、アプリケーションを追加します。

    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-rfpio-tutorial/tutorial_rfpio_addfromgallery.png)

##  <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成とテスト
このセクションでは、"Britta Simon" というテスト ユーザーに基づいて、RFPIO で Azure AD のシングル サインオンを構成し、テストします。

シングル サインオンを機能させるには、Azure AD ユーザーに対応する RFPIO ユーザーと Azure AD のユーザーの関係を Azure AD が認識している必要があります。 言い換えると、Azure AD ユーザーと RFPIO の関連ユーザーの間で、リンク関係が確立されている必要があります。

RFPIO で、Azure AD の **[ユーザー名]** の値を **[Username]** の値として割り当ててリンク関係を確立します。

RFPIO で Azure AD のシングル サインオンを構成してテストするには、次の構成要素を完了する必要があります。

1. **[Azure AD シングル サインオンの構成](#configuring-azure-ad-single-sign-on)** - ユーザーがこの機能を使用できるようにします。
2. **[Azure AD のテスト ユーザーの作成](#creating-an-azure-ad-test-user)** - Britta Simon で Azure AD のシングル サインオンをテストします。
3. **[RFPIO のテスト ユーザーの作成](#creating-a-rfpio-test-user)** - RFPIO で Britta Simon に対応するユーザーを作成し、Azure AD の Britta Simon にリンクさせます。
4. **[Azure AD テスト ユーザーの割り当て](#assigning-the-azure-ad-test-user)** - Britta Simon が Azure AD シングル サインオンを使用できるようにします。
5. **[シングル サインオンのテスト](#testing-single-sign-on)** - 構成が機能することを確認します。

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成

このセクションでは、Azure Portal で Azure AD のシングル サインオンを有効にし、RFPIO アプリケーションでシングル サインオンを構成します。

**RFPIO で Azure AD シングル サインオンを構成するには、次の手順に従います。**

1. Azure Portal の **RFPIO** アプリケーション統合ページで、**[シングル サインオン]** をクリックします。

    ![[シングル サインオンの構成]][4]

2. **[シングル サインオン]** ダイアログで、**[モード]** として **[SAML ベースのサインオン]** を選択し、シングル サインオンを有効にします。
 
    ![[シングル サインオンの構成]](./media/active-directory-saas-rfpio-tutorial/tutorial_rfpio_samlbase.png)

3. **[RFPIO のドメインと URL]** セクションで、**IDP** 開始モードでアプリケーションを構成する場合は、次の手順に従います。

    ![[シングル サインオンの構成]](./media/active-directory-saas-rfpio-tutorial/tutorial_rfpio_url.png)

    a. **[識別子]** テキストボックスに、URL として「`https://www.rfpio.com`」と入力します。

    ![[シングル サインオンの構成]](./media/active-directory-saas-rfpio-tutorial/tutorial_rfpio_url1.png)

    b. **[詳細な URL 設定の表示]** をクリックします。

    c. **[リレー状態]** テキストボックスに文字列値を入力します。 この値を取得するには、[RFPIO サポート チーム](https://www.rfpio.com/contact/)に問い合わせてください。 

4. **[詳細な URL 設定の表示]** をクリックします。 **SP** 開始モードでアプリケーションを構成する場合は、次の手順に従います。 

    ![[シングル サインオンの構成]](./media/active-directory-saas-rfpio-tutorial/tutorial_rfpio_url2.png)

    **[サインオン URL]** テキストボックスに、URL として「`https://www.app.rfpio.com`」と入力します。

5. **[SAML 署名証明書]** セクションで、**[Metadata XML (メタデータ XML)]** をクリックし、コンピューターにメタデータ ファイルを保存します。

    ![Configure Single Sign-On](./media/active-directory-saas-rfpio-tutorial/tutorial_rfpio_certificate.png) 

6. **[保存]** ボタンをクリックします。

    ![[シングル サインオンの構成]](./media/active-directory-saas-rfpio-tutorial/tutorial_general_400.png)

7. 別の Web ブラウザー ウィンドウで、**RFPIO** Web サイトに管理者としてログインします。

8. 左下隅にあるドロップダウンをクリックします。

    ![[シングル サインオンの構成]](./media/active-directory-saas-rfpio-tutorial/app1.png)

9. **[組織設定]** をクリックします。 

    ![[シングル サインオンの構成]](./media/active-directory-saas-rfpio-tutorial/app2.png)

10. **[FEATURES & INTEGRATION (機能と統合)]** をクリックします。

    ![[シングル サインオンの構成]](./media/active-directory-saas-rfpio-tutorial/app4.png)

11. **[SAML SSO 構成]** で**[編集]** をクリックします。

    ![[シングル サインオンの構成]](./media/active-directory-saas-rfpio-tutorial/app3.png)

12. このセクションでは、次のアクション実行します。

    ![[シングル サインオンの構成]](./media/active-directory-saas-rfpio-tutorial/app5.png)
    
    a. **ダウンロードしたメタデータ XML** の内容をコピーし、**[ID 構成]** フィールドに貼り付けます。

    > [!NOTE]
    >ダウンロードした**メタデータ XML** の内容をコピーするには、**Notepad++** または適切な **XML エディター**を使用します。 

    b. **[Validate (検証)]**をクリックします。

    c. **[検証]** をクリックした後、**[SAML(Enabled)(SAML (有効))]** をフリップしてオンにします。

    d. **[Submit]**をクリックします。

> [!TIP]
> アプリのセットアップ中、[Azure Portal](https://portal.azure.com) 内で上記の手順の簡易版を確認できるようになりました。  **[Active Directory] の [エンタープライズ アプリケーション]** セクションからこのアプリを追加した後、**[シングル サインオン]** タブをクリックし、一番下の **[構成]** セクションから組み込みドキュメントにアクセスするだけです。 組み込みドキュメント機能の詳細については、[Azure AD の組み込みドキュメント]( https://go.microsoft.com/fwlink/?linkid=845985)に関する記事をご覧ください。
> 

### <a name="create-an-azure-ad-test-user"></a>Azure AD のテスト ユーザーの作成
このセクションの目的は、Azure Portal で Britta Simon というテスト ユーザーを作成することです。

![Azure AD ユーザーの作成][100]

**Azure AD でテスト ユーザーを作成するには、次の手順に従います。**

1. **Azure Portal** の左側のナビゲーション ウィンドウで、**[Azure Active Directory]** アイコンをクリックします。

    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-rfpio-tutorial/create_aaduser_01.png) 

2. **[ユーザーとグループ]** に移動し、**[すべてのユーザー]** をクリックして、ユーザーの一覧を表示します。
    
    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-rfpio-tutorial/create_aaduser_02.png) 

3. ダイアログの上部にある **[追加]** をクリックして、**[ユーザー]** ダイアログを開きます。
 
    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-rfpio-tutorial/create_aaduser_03.png) 

4. **[ユーザー]** ダイアログ ページで、次の手順を実行します。
 
    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-rfpio-tutorial/create_aaduser_04.png) 

    a. **[名前]** ボックスに「**BrittaSimon**」と入力します。

    b. **[ユーザー名]** ボックスに BrittaSimon の**電子メール アドレス**を入力します。

    c. **[パスワードを表示]** を選択し、**[パスワード]** の値をメモします。

    d. ページの下部にある **[Create]**」を参照してください。
 
### <a name="create-a-rfpio-test-user"></a>RFPIO のテスト ユーザーの作成

Azure AD ユーザーが RFPIO にログインできるようにするには、ユーザーを RFPIO にプロビジョニングする必要があります。  
RFPIO の場合、プロビジョニングは手動で行います。

**ユーザー アカウントをプロビジョニングするには、次の手順に従います。**

1. RFPIO 企業サイトに管理者としてログインします。

2. 左下隅にあるドロップダウンをクリックします。

    ![[シングル サインオンの構成]](./media/active-directory-saas-rfpio-tutorial/app1.png)

3. **[組織設定]** をクリックします。 

    ![[シングル サインオンの構成]](./media/active-directory-saas-rfpio-tutorial/app2.png)

4. **[チーム メンバー]** をクリックします。

    ![[シングル サインオンの構成]](./media/active-directory-saas-rfpio-tutorial/app6.png)

5. **[メンバーの追加]** をクリックします。

    ![[シングル サインオンの構成]](./media/active-directory-saas-rfpio-tutorial/app7.png)

6. **[新しいメンバーの追加]** セクションで、 次の操作を実行します。

    ![[シングル サインオンの構成]](./media/active-directory-saas-rfpio-tutorial/app8.png)

    a. **[Enter one email per line(1 行につき 1 つの電子メール アドレスを入力する)]** フィールドに**電子メール アドレス**を入力します。

    b. 要件に応じて **[ロール]** を選択します。

    c. **[メンバーの追加]** をクリックします。
        
    > [!NOTE]
    > Azure Active Directory アカウント所有者がメールを受信し、リンクに従ってアカウントを確認するとそのアカウントがアクティブになります。

### <a name="assign-the-azure-ad-test-user"></a>Azure AD テスト ユーザーの割り当て

このセクションでは、Britta Simon に RFPIO へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

![ユーザーの割り当て][200] 

**RFPIO に Britta Simon を割り当てるには、次の手順を実行します。**

1. Azure Portal でアプリケーション ビューを開き、ディレクトリ ビューに移動します。次に、**[エンタープライズ アプリケーション]** に移動し、**[すべてのアプリケーション]** をクリックします。

    ![ユーザーの割り当て][201] 

2. アプリケーションの一覧で **[RFPIO]**を選択します。

    ![[シングル サインオンの構成]](./media/active-directory-saas-rfpio-tutorial/tutorial_rfpio_app.png) 

3. 左側のメニューで **[ユーザーとグループ]** をクリックします。

    ![ユーザーの割り当て][202] 

4. **[追加]** ボタンをクリックします。 次に、**[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。

    ![ユーザーの割り当て][203]

5. **[ユーザーとグループ]** ダイアログで、ユーザーの一覧から **[Britta Simon]** を選択します。

6. **[ユーザーとグループ]** ダイアログで **[選択]** をクリックします。

7. **[割り当ての追加]** ダイアログで **[割り当て]** ボタンをクリックします。
    
### <a name="test-single-sign-on"></a>シングル サインオンのテスト

このセクションでは、アクセス パネルを使用して Azure AD のシングル サインオン構成をテストします。

アクセス パネルで [RFPIO] タイルをクリックすると、自動的に RFPIO アプリケーションにサインオンします。
アクセス パネルの詳細については、[アクセス パネルの概要](active-directory-saas-access-panel-introduction.md)に関する記事を参照してください。

## <a name="additional-resources"></a>その他のリソース

* [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](active-directory-saas-tutorial-list.md)
* [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-rfpio-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-rfpio-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-rfpio-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-rfpio-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-rfpio-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-rfpio-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-rfpio-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-rfpio-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-rfpio-tutorial/tutorial_general_203.png


