---
title: "チュートリアル: Azure Active Directory と Kintone の統合 | Microsoft Docs"
description: "Azure Active Directory と Kintone の間でシングル サインオンを構成する方法について説明します。"
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.assetid: c2b947dc-e1a8-4f5f-b40e-2c5180648e4f
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/20/2017
ms.author: jeedes
ms.translationtype: Human Translation
ms.sourcegitcommit: 7c69630688e4bcd68ab3b4ee6d9fdb0e0c46d04b
ms.openlocfilehash: e5e847c12cba3611ce7ea2c3e956dbd55b1e0cac
ms.contentlocale: ja-jp
ms.lasthandoff: 06/24/2017


---
# <a name="tutorial-azure-active-directory-integration-with-kintone"></a>チュートリアル: Azure Active Directory と Kintone の統合

このチュートリアルでは、Kintone と Azure Active Directory (Azure AD) を統合する方法について説明します。

Kintone と Azure AD の統合には、次の利点があります。

- Kintone にアクセスする Azure AD ユーザーを制御できます
- ユーザーが自分の Azure AD アカウントで自動的に Kintone にサインオン (シングル サインオン) できるようにします
- 1 つの中央サイト (Azure Portal) でアカウントを管理できます

SaaS アプリと Azure AD の統合の詳細については、「[Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](active-directory-appssoaccess-whatis.md)」をご覧ください。

## <a name="prerequisites"></a>前提条件

Kintone と Azure AD の統合を構成するには、次のものが必要です。

- Azure AD サブスクリプション
- Kintone でのシングル サインオンが有効なサブスクリプション

> [!NOTE]
> このチュートリアルの手順をテストする場合、運用環境を使用しないことをお勧めします。

このチュートリアルの手順をテストするには、次の推奨事項に従ってください。

- 必要な場合を除き、運用環境は使用しないでください。
- Azure AD の評価環境がない場合は、 [こちら](https://azure.microsoft.com/pricing/free-trial/)から 1 か月の評価版を入手できます。

## <a name="scenario-description"></a>シナリオの説明
このチュートリアルでは、テスト環境で Azure AD のシングル サインオンをテストします。 このチュートリアルで説明するシナリオは、主に次の 2 つの要素で構成されています。

1. ギャラリーからの Kintone の追加
2. Azure AD シングル サインオンの構成とテスト

## <a name="adding-kintone-from-the-gallery"></a>ギャラリーからの Kintone の追加
Azure AD への Kintone の統合を構成するには、ギャラリーから管理対象 SaaS アプリの一覧に Kintone を追加する必要があります。

**ギャラリーから Kintone を追加するには、次の手順に従います。**

1. **[Azure Portal](https://portal.azure.com)** の左側のナビゲーション ウィンドウで、**[Azure Active Directory]** アイコンをクリックします。 

    ![Active Directory][1]

2. **[エンタープライズ アプリケーション]** に移動します。 次に、**[すべてのアプリケーション]** に移動します。

    ![アプリケーション][2]
    
3. 新しいアプリケーションを追加するには、ダイアログの上部にある **[新しいアプリケーション]** をクリックします。

    ![アプリケーション][3]

4. 検索ボックスに、「**Kintone**」と入力します。

    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-kintone-tutorial/tutorial_kintone_search.png)

5. 結果ウィンドウで **Kintone** を選択し、**[追加]** をクリックして、アプリケーションを追加します。

    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-kintone-tutorial/tutorial_kintone_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成とテスト
このセクションでは、"Britta Simon" というテスト ユーザーに基づいて、Kintone で Azure AD のシングル サインオンを構成し、テストします。

シングル サインオンを機能させるには、Azure AD ユーザーに対応する Kintone ユーザーが Azure AD で認識されている必要があります。 言い換えると、Azure AD ユーザーと Kintone の関連ユーザーの間で、リンク関係が確立されている必要があります。

Kintone で、Azure AD の **[ユーザー名]** の値を **[Username]** の値として割り当ててリンク関係を確立します。

Kintone で Azure AD のシングル サインオンを構成してテストするには、次の構成要素を完了する必要があります。

1. **[Azure AD シングル サインオンの構成](#configuring-azure-ad-single-sign-on)** - ユーザーがこの機能を使用できるようにします。
2. **[Azure AD のテスト ユーザーの作成](#creating-an-azure-ad-test-user)** - Britta Simon で Azure AD のシングル サインオンをテストします。
3. **[Kintone テスト ユーザーの作成](#creating-a-kintone-test-user)** - Kintone で Britta Simon に対応するユーザーを作成し、Azure AD の Britta Simon にリンクさせます。
4. **[Azure AD テスト ユーザーの割り当て](#assigning-the-azure-ad-test-user)** - Britta Simon が Azure AD のシングル サインオンを使用できるようにします。
5. **[Testing Single Sign-On](#testing-single-sign-on)** - 構成が機能するかどうかを確認します。

### <a name="configuring-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成

このセクションでは、Azure Portal で Azure AD のシングル サインオンを有効にして、Kintone アプリケーションでシングル サインオンを構成します。

**Kintone で Azure AD シングル サインオンを構成するには、次の手順に従います。**

1. Azure Portal の **Kintone** アプリケーション統合ページで、**[シングル サインオン]** をクリックします。

    ![[シングル サインオンの構成]][4]

2. **[シングル サインオン]** ダイアログで、**[モード]** として **[SAML ベースのサインオン]** を選択し、シングル サインオンを有効にします。
 
    ![[シングル サインオンの構成]](./media/active-directory-saas-kintone-tutorial/tutorial_kintone_samlbase.png)

3. **[Kintone のドメインと URL]** セクションで、次の手順を実行します。

    ![[シングル サインオンの構成]](./media/active-directory-saas-kintone-tutorial/tutorial_kintone_url.png)

    a. **[サインオン URL]** ボックスに、`https://<companyname>.kintone.com` のパターンを使用して URL を入力します。

    b. **[識別子]** ボックスに、次のパターンで URL を入力します。
    | |
    |--|
    | `https://<companyname>.cybozu.com`|
    | `https://<companyname>.kintone.com`|

    > [!NOTE] 
    > これらは実際の値ではありません。 実際のサインオン URL と識別子でこれらの値を更新してください。 これらの値を取得するには、[Kintone クライアント サポート チーム](https://www.kintone.com/contact/)に問い合わせてください。 
 
4. **[SAML 署名証明書]** セクションで、**[Certificate (Base64) (証明書 (Base64)) ]** をクリックし、コンピューターに証明書ファイルを保存します。

    ![Configure Single Sign-On](./media/active-directory-saas-kintone-tutorial/tutorial_kintone_certificate.png) 

5. **[保存]** ボタンをクリックします。

    ![[シングル サインオンの構成]](./media/active-directory-saas-kintone-tutorial/tutorial_general_400.png)

6. **[Kintone 構成]** セクションで、**[Kintone の構成]** をクリックして、**[サインオンの構成]** ウィンドウを開きます。 **[クイック リファレンス]** セクションから、**サインアウト URL と SAML シングル サインオン サービス URL** をコピーします。

    ![[シングル サインオンの構成]](./media/active-directory-saas-kintone-tutorial/tutorial_kintone_configure.png) 

7. 別の Web ブラウザーのウィンドウで、 **Kintone** の企業サイトに管理者としてログインします。

8. **[設定]**をクリックします。
   
    ![設定](./media/active-directory-saas-kintone-tutorial/ic785879.png "Settings")

9. **[ユーザーとシステム管理]** をクリックします。
   
    ![Users & System Administration](./media/active-directory-saas-kintone-tutorial/ic785880.png "Users & System Administration")

10. **[システム管理] \> [セキュリティ]** で、**[ログイン]** をクリックします。
   
    ![Login](./media/active-directory-saas-kintone-tutorial/ic785881.png "Login")

11. **[SAML 認証を有効にする]**を選択します。
   
    ![SAML Authentication](./media/active-directory-saas-kintone-tutorial/ic785882.png "SAML Authentication")

12. [SAML 承認] セクションで、次の手順に従います。
    
    ![SAML Authentication](./media/active-directory-saas-kintone-tutorial/ic785883.png "SAML Authentication")
    
    a. **[Login URL]\(ログイン URL\)** ボックスに、Azure Portal からコピーした **SAML シングル サインオン サービス URL** の値を貼り付けます。
   
    b. **[Logout URL]\(ログアウト URL\)** ボックスに、Azure Portal からコピーした **サインアウト URL** の値を貼り付けます。
    
    c. **[参照]** をクリックして、ダウンロードした証明書をアップロードします。
    
    d. [ **Save**] をクリックします。

> [!TIP]
> アプリのセットアップ中、[Azure Portal](https://portal.azure.com) 内で上記の手順の簡易版を確認できるようになりました。  **[Active Directory] の [エンタープライズ アプリケーション]** セクションからこのアプリを追加した後、**[シングル サインオン]** タブをクリックし、一番下の **[構成]** セクションから組み込みドキュメントにアクセスするだけです。 組み込みドキュメント機能の詳細については、[Azure AD の組み込みドキュメント]( https://go.microsoft.com/fwlink/?linkid=845985)に関する記事をご覧ください。
> 

### <a name="creating-an-azure-ad-test-user"></a>Azure AD のテスト ユーザーの作成
このセクションの目的は、Azure Portal で Britta Simon というテスト ユーザーを作成することです。

![Azure AD ユーザーの作成][100]

**Azure AD でテスト ユーザーを作成するには、次の手順に従います。**

1. **Azure Portal** の左側のナビゲーション ウィンドウで、**[Azure Active Directory]** アイコンをクリックします。

    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-kintone-tutorial/create_aaduser_01.png) 

2. **[ユーザーとグループ]** に移動し、**[すべてのユーザー]** をクリックして、ユーザーの一覧を表示します。
    
    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-kintone-tutorial/create_aaduser_02.png) 

3. ダイアログの上部にある **[追加]** をクリックして、**[ユーザー]** ダイアログを開きます。
 
    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-kintone-tutorial/create_aaduser_03.png) 

4. **[ユーザー]** ダイアログ ページで、次の手順を実行します。
 
    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-kintone-tutorial/create_aaduser_04.png) 

    a. **[名前]** ボックスに「**BrittaSimon**」と入力します。

    b. **[ユーザー名]** ボックスに BrittaSimon の**電子メール アドレス**を入力します。

    c. **[パスワードを表示]** を選択し、**[パスワード]** の値をメモします。

    d. ページの下部にある **[Create]**」を参照してください。
 
### <a name="creating-a-kintone-test-user"></a>Kintone テスト ユーザーの作成

Azure AD ユーザーが Kintone にログインできるようにするには、そのユーザーを Kintone にプロビジョニングする必要があります。  
Kintone の場合、プロビジョニングは手動で行います。

### <a name="to-provision-a-user-account-perform-the-following-steps"></a>ユーザー アカウントをプロビジョニングするには、次の手順を実行します。

1. **Kintone** の企業サイトに管理者としてログインします。

2. **[設定]**をクリックします。
   
    ![設定](./media/active-directory-saas-kintone-tutorial/ic785879.png "Settings")

3. **[ユーザーとシステム管理]** をクリックします。
   
    ![User & System Administration](./media/active-directory-saas-kintone-tutorial/ic785880.png "User & System Administration")

4. **[ユーザー管理]** で、**[Departments & Users (部門とユーザー)]** をクリックします。
   
    ![Department & Users](./media/active-directory-saas-kintone-tutorial/ic785888.png "Department & Users")

5. **[新しいユーザー]**をクリックします。
   
    ![New Users](./media/active-directory-saas-kintone-tutorial/ic785889.png "New Users")

6. **[新しいユーザー]** セクションで、次の手順に従います。
   
    ![New Users](./media/active-directory-saas-kintone-tutorial/ic785890.png "New Users")
   
    a. プロビジョニングする有効な AAD アカウントの **[Display Name]\(表示名\)**、**[Login Name]\(ログイン名\)**、**[New Password]\(新しいパスワード\)**、**[Confirm Password]\(確認パスワード\)**、**[E-mail Address]\(メール アドレス\)**、その他の詳細を該当するボックスに入力します。
 
    b. [ **Save**] をクリックします。

> [!NOTE]
> 他の Kintone ユーザー アカウント作成ツールや Kintone から提供されている API を使用して、AAD ユーザー アカウントをプロビジョニングできます。

### <a name="assigning-the-azure-ad-test-user"></a>Azure AD テスト ユーザーの割り当て

このセクションでは、Britta Simon に Kintone へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

![ユーザーの割り当て][200] 

**Kintone に Britta Simon を割り当てるには、次の手順に従います。**

1. Azure Portal でアプリケーション ビューを開き、ディレクトリ ビューに移動します。次に、**[エンタープライズ アプリケーション]** に移動し、**[すべてのアプリケーション]** をクリックします。

    ![ユーザーの割り当て][201] 

2. アプリケーションの一覧で **[Kintone]** を選択します。

    ![[シングル サインオンの構成]](./media/active-directory-saas-kintone-tutorial/tutorial_kintone_app.png) 

3. 左側のメニューで **[ユーザーとグループ]** をクリックします。

    ![ユーザーの割り当て][202] 

4. **[追加]** ボタンをクリックします。 次に、**[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。

    ![ユーザーの割り当て][203]

5. **[ユーザーとグループ]** ダイアログで、ユーザーの一覧から **[Britta Simon]** を選択します。

6. **[ユーザーとグループ]** ダイアログで **[選択]** をクリックします。

7. **[割り当ての追加]** ダイアログで **[割り当て]** ボタンをクリックします。
    
### <a name="testing-single-sign-on"></a>シングル サインオンのテスト

このセクションの目的は、アクセス パネルを使用して Azure AD のシングル サインオン構成をテストすることです。

アクセス パネルで [Kintone] タイルをクリックすると、自動的に Kintone アプリケーションにサインオンします。

## <a name="additional-resources"></a>その他のリソース

* [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](active-directory-saas-tutorial-list.md)
* [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-kintone-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-kintone-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-kintone-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-kintone-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-kintone-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-kintone-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-kintone-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-kintone-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-kintone-tutorial/tutorial_general_203.png


