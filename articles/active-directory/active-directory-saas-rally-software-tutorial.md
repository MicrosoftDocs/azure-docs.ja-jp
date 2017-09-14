---
title: "チュートリアル: Azure Active Directory と Rally Software の統合 | Microsoft Docs"
description: "Azure Active Directory と Rally Software の間でシングル サインオンを構成する方法について説明します。"
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: ba25fade-e152-42dd-8377-a30bbc48c3ed
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/04/2017
ms.author: jeedes
ms.translationtype: HT
ms.sourcegitcommit: 1e6fb68d239ee3a66899f520a91702419461c02b
ms.openlocfilehash: 6481c9ef0ca71419ccfa6f7956f4702985743df3
ms.contentlocale: ja-jp
ms.lasthandoff: 08/16/2017

---
# <a name="tutorial-azure-active-directory-integration-with-rally-software"></a>チュートリアル: Azure Active Directory と Rally Software の統合

このチュートリアルでは、Rally Software と Azure Active Directory (Azure AD) を統合する方法について説明します。

Rally Software と Azure AD の統合には、次の利点があります。

- Rally Software にアクセスする Azure AD ユーザーを制御できます。
- ユーザーが自分の Azure AD アカウントで自動的に Rally Software にサインオン (シングル サインオン) できるようにします。
- 1 つの中央サイト (Azure Portal) でアカウントを管理できます。

SaaS アプリと Azure AD の統合の詳細については、「[Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](active-directory-appssoaccess-whatis.md)」をご覧ください。

## <a name="prerequisites"></a>前提条件

Rally Software と Azure AD の統合を構成するには、次のものが必要です。

- Azure AD サブスクリプション
- シングル サインオン対応の Rally Software サブスクリプション

> [!NOTE]
> このチュートリアルの手順をテストする場合、運用環境を使用しないことをお勧めします。

このチュートリアルの手順をテストするには、次の推奨事項に従ってください。

- 必要な場合を除き、運用環境は使用しないでください。
- Azure AD の評価環境がない場合は、[1 か月の評価版を入手できます](https://azure.microsoft.com/pricing/free-trial/)。

## <a name="scenario-description"></a>シナリオの説明
このチュートリアルでは、テスト環境で Azure AD のシングル サインオンをテストします。 このチュートリアルで説明するシナリオは、主に次の 2 つの要素で構成されています。

1. ギャラリーからの Rally Software の追加
2. Azure AD シングル サインオンの構成とテスト

## <a name="adding-rally-software-from-the-gallery"></a>ギャラリーからの Rally Software の追加
Azure AD への Rally Software の統合を構成するには、ギャラリーから管理対象 SaaS アプリの一覧に Rally Software を追加する必要があります。

**ギャラリーから Rally Software を追加するには、次の手順に従います。**

1. **[Azure Portal](https://portal.azure.com)** の左側のナビゲーション ウィンドウで、**[Azure Active Directory]** アイコンをクリックします。 

    ![Azure Active Directory のボタン][1]

2. **[エンタープライズ アプリケーション]** に移動します。 次に、**[すべてのアプリケーション]** に移動します。

    ![[エンタープライズ アプリケーション] ブレード][2]
    
3. 新しいアプリケーションを追加するには、ダイアログの上部にある **[新しいアプリケーション]** をクリックします。

    ![[新しいアプリケーション] ボタン][3]

4. 検索ボックスに「**Rally Software**」と入力し、結果ウィンドウで **Rally Software** を選び、**[追加]** をクリックして、アプリケーションを追加します。

    ![結果リストの Rally Software](./media/active-directory-saas-rally-software-tutorial/tutorial_rallysoftware_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成とテスト

このセクションでは、"Britta Simon" というテスト ユーザーに基づいて、Rally Software で Azure AD のシングル サインオンを構成し、テストします。

シングル サインオンを機能させるには、Azure AD ユーザーに対応する Rally Software ユーザーが Azure AD で認識されている必要があります。 言い換えると、Azure AD ユーザーと Rally Software の関連ユーザーの間で、リンクの関係が確立されている必要があります。

Rally Software で、Azure AD の **[ユーザー名]** の値を **[Username]\(ユーザー名\)** の値として割り当ててリンク関係を確立します。

Rally Software で Azure AD のシングル サインオンを構成し、テストするには、次の要素を完了する必要があります。

1. **[Azure AD シングル サインオンの構成](#configure-azure-ad-single-sign-on)** - ユーザーがこの機能を使用できるようにします。
2. **[Azure AD のテスト ユーザーの作成](#create-an-azure-ad-test-user)** - Britta Simon で Azure AD のシングル サインオンをテストします。
3. **[Rally Software のテスト ユーザーの作成](#create-a-rally-software-test-user)** - Rally Software で Britta Simon に対応するユーザーを作成し、Azure AD の Britta Simon にリンクさせます。
4. **[Azure AD テスト ユーザーの割り当て](#assign-the-azure-ad-test-user)** - Britta Simon が Azure AD シングル サインオンを使用できるようにします。
5. **[シングル サインオンのテスト](#test-single-sign-on)** - 構成が機能するかどうかを確認します。

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成

このセクションでは、Azure Portal で Azure AD のシングル サインオンを有効にして、Rally Software アプリケーションでシングル サインオンを構成します。

**Rally Software で Azure AD シングル サインオンを構成するには、次の手順に従います。**

1. Azure Portal の **Rally Software** アプリケーション統合ページで、**[シングル サインオン]** をクリックします。

    ![シングル サインオン構成のリンク][4]

2. **[シングル サインオン]** ダイアログで、**[モード]** として **[SAML ベースのサインオン]** を選択し、シングル サインオンを有効にします。
 
    ![[シングル サインオン] ダイアログ ボックス](./media/active-directory-saas-rally-software-tutorial/tutorial_rallysoftware_samlbase.png)

3. **[Rally Software のドメインと URL]** セクションで、次の手順を実行します。

    ![[Rally Software のドメインと URL] のシングル サインオン情報](./media/active-directory-saas-rally-software-tutorial/tutorial_rallysoftware_url.png)

    a. **[サインオン URL]** ボックスに、`https://<tenant-name>.rally.com` のパターンを使用して URL を入力します。

    b. **[識別子]** ボックスに、`https://<tenant-name>.rally.com` の形式で URL を入力します。

    > [!NOTE] 
    > これらは実際の値ではありません。 実際のサインオン URL と識別子でこれらの値を更新してください。 これらの値を取得するには、[Rally Software クライアント サポート チーム](https://help.rallydev.com/)に問い合わせてください。 
 


4. **[SAML 署名証明書]** セクションで、**[Metadata XML (メタデータ XML)]** をクリックし、コンピューターにメタデータ ファイルを保存します。

    ![証明書のダウンロードのリンク](./media/active-directory-saas-rally-software-tutorial/tutorial_rallysoftware_certificate.png) 

5. **[保存]** ボタンをクリックします。

    ![[シングル サインオンの構成] の [保存] ボタン](./media/active-directory-saas-rally-software-tutorial/tutorial_general_400.png)

6. **[Rally Software 構成]** セクションで **[Rally Software の構成]** をクリックして、**[サインオンの構成]** ウィンドウを開きます。 **[クイック リファレンス]** セクションから、**サインアウト URL と SAML エンティティ ID** をコピーします。

    ![Rally Software の構成](./media/active-directory-saas-rally-software-tutorial/tutorial_rallysoftware_configure.png) 

7. **Rally Software** テナントにログインします。

8. 上部にあるツールバーの **[Setup]** をクリックし、**[Subscription]** を選択します。
   
    ![Subscription](./media/active-directory-saas-rally-software-tutorial/ic769531.png "Subscription")

9. **[アクション]** ボタンをクリックします。 ツールバーの上部の右側にある **[サブスクリプションの編集]** を選択します。

10. **[Subscription]** ダイアログ ページで次の手順に従った後、**[Save & Close]** をクリックします。
   
    ![Authentication](./media/active-directory-saas-rally-software-tutorial/ic769542.png "Authentication")
   
    a. Authentication のドロップダウン リストから、**[Rally or SSO authentication]** を選択します。

    b. **[Identity Provider URL]\(ID プロバイダー URL\)** ボックスに、Azure Portal からコピーした **SAML エンティティ ID** の値を貼り付けます。 

    c. **[SSO ログアウト]** ボックスに、Azure Portal からコピーした **サインアウト URL** の値を貼り付けます。

> [!TIP]
> アプリのセットアップ中、[Azure Portal](https://portal.azure.com) 内で上記の手順の簡易版を確認できるようになりました。  **[Active Directory] の [エンタープライズ アプリケーション]** セクションからこのアプリを追加した後、**[シングル サインオン]** タブをクリックし、一番下の **[構成]** セクションから組み込みドキュメントにアクセスするだけです。 組み込みドキュメント機能の詳細については、[Azure AD の組み込みドキュメント]( https://go.microsoft.com/fwlink/?linkid=845985)に関する記事をご覧ください。
> 

### <a name="create-an-azure-ad-test-user"></a>Azure AD のテスト ユーザーの作成

このセクションの目的は、Azure Portal で Britta Simon というテスト ユーザーを作成することです。

   ![Azure AD のテスト ユーザーの作成][100]

**Azure AD でテスト ユーザーを作成するには、次の手順に従います。**

1. Azure Portal の左側のウィンドウで、**Azure Active Directory** のボタンをクリックします。

    ![Azure Active Directory のボタン](./media/active-directory-saas-rally-software-tutorial/create_aaduser_01.png)

2. ユーザーの一覧を表示するには、**[ユーザーとグループ]** に移動し、**[すべてのユーザー]** をクリックします。

    ![[ユーザーとグループ] と [すべてのユーザー] リンク](./media/active-directory-saas-rally-software-tutorial/create_aaduser_02.png)

3. **[ユーザー]** ダイアログ ボックスを開くには、**[すべてのユーザー]** ダイアログ ボックスの上部にある **[追加]** をクリックしてきます。

    ![[追加] ボタン](./media/active-directory-saas-rally-software-tutorial/create_aaduser_03.png)

4. **[ユーザー]** ダイアログ ボックスで、次の手順に従います。

    ![[ユーザー] ダイアログ ボックス](./media/active-directory-saas-rally-software-tutorial/create_aaduser_04.png)

    a. **[名前]** ボックスに「**BrittaSimon**」と入力します。

    b. **[ユーザー名]** ボックスに、ユーザーである Britta Simon の電子メール アドレスを入力します。

    c. **[パスワードを表示]** チェック ボックスをオンにし、**[パスワード]** ボックスに表示された値を書き留めます。

    d. **[作成]**をクリックします。
 
### <a name="create-a-rally-software-test-user"></a>Rally Software テスト ユーザーの作成

Azure AD ユーザーがサインインできるように、Azure Active Directory ユーザー名を使用して、Rally Software アプリケーションにユーザーをプロビジョニングする必要があります。

**ユーザー プロビジョニングを構成するには、次の手順に従います。**

1. Rally Software テナントにログインします。

2. **[Setup] \> [USERS]** に移動し、**[+ Add New]** をクリックします。
   
    ![ユーザー](./media/active-directory-saas-rally-software-tutorial/ic781039.png "Users")

3. New User テキストボックスに名前を入力し、 **[Add with Details]**をクリックします。

4. **[Create User]** セクションで、次の手順に従います。
   
    ![Create User](./media/active-directory-saas-rally-software-tutorial/ic781040.png "Create User")

    a. **[User Name]\(ユーザー名\)** ボックスに、ユーザーの氏名 (**BrittSimon** など) を入力します。
   
    b. **[E-mail address]\(電子メール アドレス\)** ボックスに、ユーザーの電子メール アドレスを入力します (この例では **brittasimon@contoso.com**)。

    c. **[First Name]\(名\)** ボックスに、ユーザーの名を入力します (例: **Britta**)。

    d. **[Last Name]\(姓\)** ボックスに、ユーザーの姓を入力します (例: **Simon**)。

    e. **[Save & Close]** をクリックします。

   >[!NOTE]
   >他の Rally Software ユーザー アカウントの作成ツールまたは Rally Software から提供されている API を使用して、Azure AD ユーザー アカウントをプロビジョニングできます。

### <a name="assign-the-azure-ad-test-user"></a>Azure AD テスト ユーザーの割り当て

このセクションでは、Britta Simon に Rally Software へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

![ユーザー ロールを割り当てる][200] 

**Rally Software に Britta Simon を割り当てるには、次の手順に従います。**

1. Azure Portal でアプリケーション ビューを開き、ディレクトリ ビューに移動します。次に、**[エンタープライズ アプリケーション]** に移動し、**[すべてのアプリケーション]** をクリックします。

    ![ユーザーの割り当て][201] 

2. アプリケーションの一覧で **[Rally Software]** を選択します。

    ![アプリケーションの一覧の Rally Software リンク](./media/active-directory-saas-rally-software-tutorial/tutorial_rallysoftware_app.png)  

3. 左側のメニューで **[ユーザーとグループ]** をクリックします。

    ![[ユーザーとグループ] リンク][202]

4. **[追加]** ボタンをクリックします。 次に、**[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。

    ![[割り当ての追加] ウィンドウ][203]

5. **[ユーザーとグループ]** ダイアログで、ユーザーの一覧から **[Britta Simon]** を選択します。

6. **[ユーザーとグループ]** ダイアログで **[選択]** をクリックします。

7. **[割り当ての追加]** ダイアログで **[割り当て]** ボタンをクリックします。
    
### <a name="test-single-sign-on"></a>シングル サインオンのテスト

このセクションの目的は、アクセス パネルを使用して Azure AD のシングル サインオン構成をテストすることです。

アクセス パネルで Rally Software のタイルをクリックすると、自動的に Rally Software アプリケーションにサインオンします。

## <a name="additional-resources"></a>その他のリソース

* [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](active-directory-saas-tutorial-list.md)
* [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-rally-software-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-rally-software-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-rally-software-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-rally-software-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-rally-software-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-rally-software-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-rally-software-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-rally-software-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-rally-software-tutorial/tutorial_general_203.png


