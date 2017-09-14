---
title: "チュートリアル: Azure Active Directory と Autotask Workplace の統合 | Microsoft Docs"
description: "Azure Active Directory と Autotask Workplace の間でシングル サインオンを構成する方法について説明します。"
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: a9a7ff71-c389-4169-aafd-d7a505244797
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/19/2017
ms.author: jeedes
ms.translationtype: HT
ms.sourcegitcommit: 74b75232b4b1c14dbb81151cdab5856a1e4da28c
ms.openlocfilehash: 45130162271b20860607497ff93c6a668c415233
ms.contentlocale: ja-jp
ms.lasthandoff: 07/26/2017

---
# <a name="tutorial-azure-active-directory-integration-with-autotask-workplace"></a>チュートリアル: Azure Active Directory と Autotask Workplace の統合

このチュートリアルでは、Autotask Workplace と Azure Active Directory (Azure AD) を統合する方法について説明します。

Autotask Workplace と Azure AD の統合には、次の利点があります。

- Autotask Workplace にアクセスするユーザーを Azure AD で管理できます。
- ユーザーが自分の Azure AD アカウントで自動的に Autotask Workplace にサインオンされる (シングル サインオン) ようにできます。
- 1 つの中央サイト (Azure Portal) でアカウントを管理できます

SaaS アプリと Azure AD の統合の詳細については、「[Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](active-directory-appssoaccess-whatis.md)」をご覧ください。

## <a name="prerequisites"></a>前提条件

Azure AD と Autotask Workplace の統合を構成するには、次のものが必要です。

- Azure AD サブスクリプション
- Autotask Workplace でのシングル サインオンが有効なサブスクリプション
- Workplace の管理者またはスーパー管理者である必要があります。
- Azure AD に Administrator アカウントを持っている必要があります。
- この機能を利用するユーザーは、Workplace と Azure AD にアカウントを持ち、それぞれのメール アドレスが一致している必要があります。

> [!NOTE]
> このチュートリアルの手順をテストする場合、運用環境を使用しないことをお勧めします。

このチュートリアルの手順をテストするには、次の推奨事項に従ってください。

- 必要な場合を除き、運用環境は使用しないでください。
- Azure AD の評価環境がない場合は、[1 か月の評価版を入手できます](https://azure.microsoft.com/pricing/free-trial/)。

## <a name="scenario-description"></a>シナリオの説明
このチュートリアルでは、テスト環境で Azure AD のシングル サインオンをテストします。 このチュートリアルで説明するシナリオは、主に次の 2 つの要素で構成されています。

1. ギャラリーからの Autotask Workplace の追加
2. Azure AD シングル サインオンの構成とテスト

## <a name="adding-autotask-workplace-from-the-gallery"></a>ギャラリーからの Autotask Workplace の追加
Azure AD への Autotask Workplace の統合を構成するには、ギャラリーから管理対象 SaaS アプリの一覧に Autotask Workplace を追加する必要があります。

**ギャラリーから Autotask Workplace を追加するには、次の手順に従います。**

1. **[Azure Portal](https://portal.azure.com)** の左側のナビゲーション ウィンドウで、**[Azure Active Directory]** アイコンをクリックします。 

    ![Azure Active Directory のボタン][1]

2. **[エンタープライズ アプリケーション]** に移動します。 次に、**[すべてのアプリケーション]** に移動します。

    ![[エンタープライズ アプリケーション] ブレード][2]
    
3. 新しいアプリケーションを追加するには、ダイアログの上部にある **[新しいアプリケーション]** をクリックします。

    ![[新しいアプリケーション] ボタン][3]

4. 検索ボックスに「**Autotask Workplace**」と入力し、結果ウィンドウで **Autotask Workplace** を選び、**[追加]** をクリックして、アプリケーションを追加します。

    ![結果一覧の Autotask Workplace](./media/active-directory-saas-autotaskworkplace-tutorial/tutorial_autotaskworkplace_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成とテスト

このセクションでは、"Britta Simon" というテスト ユーザーに基づいて、Autotask Workplace で Azure AD のシングル サインオンを構成し、テストします。

シングル サインオンを機能させるには、Azure AD ユーザーに対応する Autotask Workplace ユーザーが Azure AD で認識されている必要があります。 言い換えると、Azure AD ユーザーと Autotask Workplace の関連ユーザーの間で、リンク関係が確立されている必要があります。

Autotask Workplace で、Azure AD の **[ユーザー名]** の値を **[Username] \(ユーザー名)** の値として割り当ててリンク関係を確立します。

Autotask Workplace で Azure AD のシングル サインオンを構成してテストするには、次の構成要素を完了する必要があります。

1. **[Azure AD シングル サインオンの構成](#configure-azure-ad-single-sign-on)** - ユーザーがこの機能を使用できるようにします。
2. **[Azure AD のテスト ユーザーの作成](#create-an-azure-ad-test-user)** - Britta Simon で Azure AD のシングル サインオンをテストします。
3. **[Autotask Workplace テスト ユーザーの作成](#create-an-autotask-workplace-test-user)** - Autotask Workplace で Britta Simon に対応するユーザーを作成し、Azure AD の Britta Simon にリンクさせます。
4. **[Azure AD テスト ユーザーの割り当て](#assign-the-azure-ad-test-user)** - Britta Simon が Azure AD シングル サインオンを使用できるようにします。
5. **[シングル サインオンのテスト](#test-single-sign-on)** - 構成が機能するかどうかを確認します。

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成

このセクションでは、クラシック ポータルで Azure AD のシングル サインオンを有効にして、Autotask Workplace アプリケーションでシングル サインオンを構成します。

**Autotask Workplace で Azure AD シングル サインオンを構成するには、次の手順に従います。**

1. Azure Portal の **Autotask Workplace** アプリケーション統合ページで、**[シングル サインオン]** をクリックします。

    ![シングル サインオン構成のリンク][4]

2. **[シングル サインオン]** ダイアログで、**[モード]** として **[SAML ベースのサインオン]** を選択し、シングル サインオンを有効にします。
 
    ![[シングル サインオン] ダイアログ ボックス](./media/active-directory-saas-autotaskworkplace-tutorial/tutorial_autotaskworkplace_samlbase.png)

3. **IDP** 開始モードでアプリケーションを構成する場合は、**[Autotask Workplace のドメインと URL]** セクションで次の手順を実行します。

    ![IDP の場合の [Autotask Workplace のドメインと URL] のシングル サインオン情報](./media/active-directory-saas-autotaskworkplace-tutorial/tutorial_autotaskworkplace_url.png)

    a. **[識別子]** ボックスに、`https://<subdomain>.awp.autotask.net/singlesignon/saml/metadata` の形式で URL を入力します。

    b. **[応答 URL]** ボックスに、`https://<subdomain>.awp.autotask.net/singlesignon/saml/SSO` のパターンを使用して URL を入力します。

4. アプリケーションを **SP** 開始モードで構成する場合は、**[詳細な URL 設定の表示]** チェックボックスをオンにします。

    ![SP の場合の [Autotask Workplace のドメインと URL] のシングル サインオン情報](./media/active-directory-saas-autotaskworkplace-tutorial/tutorial_autotaskworkplace_url1.png)

    **[サインオン URL]** ボックスに、`https://<subdomain>.awp.autotask.net/loginsso` のパターンを使用して URL を入力します。
     
    > [!NOTE] 
    > これらは実際の値ではありません。 実際の識別子、応答 URL、サインオン URL でこれらの値を更新します。 これらの値を取得するには、[Autotask Workplace クライアント サポート チーム](https://awp.autotask.net/help/Content/0_HOME/Support_for_End_Clients.htm)に問い合わせてください。 

5. **[SAML 署名証明書]** セクションで、**[Metadata XML (メタデータ XML)]** をクリックし、コンピューターにメタデータ ファイルを保存します。

    ![証明書のダウンロードのリンク](./media/active-directory-saas-autotaskworkplace-tutorial/tutorial_autotaskworkplace_certificate.png) 

6. **[保存]** ボタンをクリックします。

    ![[シングル サインオンの構成] の [保存] ボタン](./media/active-directory-saas-autotaskworkplace-tutorial/tutorial_general_400.png)

7. 別の Web ブラウザー ウィンドウで、管理者の資格情報を使って Workplace Online にログインします。

    >[!Note]
    >IdP を構成するときは、サブドメインを指定する必要があります。 正しいサブドメインを確認するには、Workplace Online にログインします。 ログインした後、URL のサブドメインを書き留めます。
    >サブドメインは "https://" と ".awp.autotask.net/" の間の部分であり、us、eu、ca、au のいずれかでなければなりません。

8. **[Configuration]\(構成\)** > **[Single Sign-On]\(シングル サインオン\)** に移動し、以下の手順を実行します。

    ![Autotask のシングル サインオンの構成](./media/active-directory-saas-autotaskworkplace-tutorial/tutorial_autotaskssoconfig1.png)
 
    a. **[XML Metadata File]\(XML メタデータ ファイル\)** オプションを選び、Azure Portal からダウンロードした**メタデータ XML** をアップロードします。

    b. **[Enable SSO]\(SSO を有効にする\)** をクリックします。
    
    ![Autotask シングル サインオン承認の構成](./media/active-directory-saas-autotaskworkplace-tutorial/tutorial_autotaskssoconfig2.png)

    c. **[I confirm this information is correct and I trust this IdP]\(この情報が正しいことを確認し、この IdP を信頼します\)** チェック ボックスをオンにします。

    d. **[Approve]\(承認\)** をクリックします。
     
>[!Note]
>Autotask Workplace の構成でサポートを必要とする場合は、[こちらのページ](https://awp.autotask.net/help/Content/0_HOME/Support_for_End_Clients.htm)を参照して、Workplace アカウントでサポートを受けてください。

> [!TIP]
> アプリのセットアップ中、[Azure Portal](https://portal.azure.com) 内で上記の手順の簡易版を確認できるようになりました。  **[Active Directory] の [エンタープライズ アプリケーション]** セクションからこのアプリを追加した後、**[シングル サインオン]** タブをクリックし、一番下の **[構成]** セクションから組み込みドキュメントにアクセスするだけです。 組み込みドキュメント機能の詳細については、[Azure AD の組み込みドキュメント]( https://go.microsoft.com/fwlink/?linkid=845985)に関する記事をご覧ください。

### <a name="create-an-azure-ad-test-user"></a>Azure AD のテスト ユーザーの作成

このセクションの目的は、Azure Portal で Britta Simon というテスト ユーザーを作成することです。

   ![Azure AD のテスト ユーザーの作成][100]

**Azure AD でテスト ユーザーを作成するには、次の手順に従います。**

1. Azure Portal の左側のウィンドウで、**Azure Active Directory** のボタンをクリックします。

    ![Azure Active Directory のボタン](./media/active-directory-saas-autotaskworkplace-tutorial/create_aaduser_01.png)

2. ユーザーの一覧を表示するには、**[ユーザーとグループ]** に移動し、**[すべてのユーザー]** をクリックします。

    ![[ユーザーとグループ] と [すべてのユーザー] リンク](./media/active-directory-saas-autotaskworkplace-tutorial/create_aaduser_02.png)

3. **[ユーザー]** ダイアログ ボックスを開くには、**[すべてのユーザー]** ダイアログ ボックスの上部にある **[追加]** をクリックしてきます。

    ![[追加] ボタン](./media/active-directory-saas-autotaskworkplace-tutorial/create_aaduser_03.png)

4. **[ユーザー]** ダイアログ ボックスで、次の手順に従います。

    ![[ユーザー] ダイアログ ボックス](./media/active-directory-saas-autotaskworkplace-tutorial/create_aaduser_04.png)

    a. **[名前]** ボックスに「**BrittaSimon**」と入力します。

    b. **[ユーザー名]** ボックスに、ユーザーである Britta Simon の電子メール アドレスを入力します。

    c. **[パスワードを表示]** チェック ボックスをオンにし、**[パスワード]** ボックスに表示された値を書き留めます。

    d. **[作成]**をクリックします。

### <a name="create-an-autotask-workplace-test-user"></a>Autotask Workplace テスト ユーザーの作成

このセクションでは、Autotask で Britta Simon というユーザーを作成します。 [Autotask Workplace サポート チーム](https://awp.autotask.net/help/Content/0_HOME/Support_for_End_Clients.htm)と連携し、Autotask Workplace プラットフォームにユーザーを追加してください。

### <a name="assign-the-azure-ad-test-user"></a>Azure AD テスト ユーザーの割り当て

このセクションでは、Autotask Workplace へのアクセスを許可することで、Britta Simon が Azure シングル サインオンを使用できるようにします。

![ユーザー ロールを割り当てる][200] 

**Britta Simon を Autotask Workplace に割り当てるには、次の手順に従います。**

1. Azure Portal でアプリケーション ビューを開き、ディレクトリ ビューに移動します。次に、**[エンタープライズ アプリケーション]** に移動し、**[すべてのアプリケーション]** をクリックします。

    ![ユーザーの割り当て][201] 

2. アプリケーションの一覧で **[Autotask Workplace]** を選択します。

    ![アプリケーション一覧での Autotask Workplace のリンク](./media/active-directory-saas-autotaskworkplace-tutorial/tutorial_autotaskworkplace_app.png) 

3. 左側のメニューで **[ユーザーとグループ]** をクリックします。

    ![[ユーザーとグループ] リンク][202]

4. **[追加]** ボタンをクリックします。 次に、**[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。

    ![[割り当ての追加] ウィンドウ][203]

5. **[ユーザーとグループ]** ダイアログで、ユーザーの一覧から **[Britta Simon]** を選択します。

6. **[ユーザーとグループ]** ダイアログで **[選択]** をクリックします。

7. **[割り当ての追加]** ダイアログで **[割り当て]** ボタンをクリックします。
    
### <a name="test-single-sign-on"></a>シングル サインオンのテスト

このセクションでは、アクセス パネルを使用して Azure AD のシングル サインオン構成をテストします。

アクセス パネルで [Autotask Workplace] タイルをクリックすると、自動的に Autotask Workplace アプリケーションにサインオンします。
アクセス パネルの詳細については、[アクセス パネルの概要](active-directory-saas-access-panel-introduction.md)に関する記事を参照してください。

## <a name="additional-resources"></a>その他のリソース

* [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](active-directory-saas-tutorial-list.md)
* [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](active-directory-appssoaccess-whatis.md)


<!--Image references-->

[1]: ./media/active-directory-saas-autotaskworkplace-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-autotaskworkplace-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-autotaskworkplace-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-autotaskworkplace-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-autotaskworkplace-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-autotaskworkplace-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-autotaskworkplace-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-autotaskworkplace-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-autotaskworkplace-tutorial/tutorial_general_203.png


