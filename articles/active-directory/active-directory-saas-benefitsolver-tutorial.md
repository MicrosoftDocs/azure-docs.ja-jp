---
title: "チュートリアル: Azure Active Directory と Benefitsolver の統合 | Microsoft Docs"
description: "Azure Active Directory と Benefitsolver の間でシングル サインオンを構成する方法について説明します。"
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 333394c1-b5a7-489c-8f7b-d1a5b4e782ea
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/30/2017
ms.author: jeedes
ms.openlocfilehash: f8cf8ceabcb3fea7c22f08ec86155f60527297eb
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="tutorial-azure-active-directory-integration-with-benefitsolver"></a>チュートリアル: Azure Active Directory と Benefitsolver の統合

このチュートリアルでは、Benefitsolver と Azure Active Directory (Azure AD) を統合する方法について説明します。

Benefitsolver と Azure AD の統合には、次の利点があります。

- Benefitsolver にアクセスできる Azure AD ユーザーを制御できます。
- ユーザーが自分の Azure AD アカウントで自動的に Benefitsolver にサインオン (シングル サインオン) できるようにします。
- 1 つの中央サイト (Azure Portal) でアカウントを管理できます。

SaaS アプリと Azure AD の統合の詳細については、「[Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](active-directory-appssoaccess-whatis.md)」をご覧ください。

## <a name="prerequisites"></a>前提条件

Benefitsolver と Azure AD の統合を構成するには、次のものが必要です。

- Azure AD サブスクリプション
- Benefitsolver でのシングル サインオンが有効なサブスクリプション

> [!NOTE]
> このチュートリアルの手順をテストする場合、運用環境を使用しないことをお勧めします。

このチュートリアルの手順をテストするには、次の推奨事項に従ってください。

- 必要な場合を除き、運用環境は使用しないでください。
- Azure AD の評価環境がない場合は、[1 か月の評価版を入手できます](https://azure.microsoft.com/pricing/free-trial/)。

## <a name="scenario-description"></a>シナリオの説明
このチュートリアルでは、テスト環境で Azure AD のシングル サインオンをテストします。 このチュートリアルで説明するシナリオは、主に次の 2 つの要素で構成されています。

1. ギャラリーからの Benefitsolver の追加
2. Azure AD シングル サインオンの構成とテスト

## <a name="adding-benefitsolver-from-the-gallery"></a>ギャラリーからの Benefitsolver の追加
Azure AD への Benefitsolver の統合を構成するには、ギャラリーから管理対象 SaaS アプリの一覧に Benefitsolver を追加する必要があります。

**ギャラリーから Benefitsolver を追加するには、次の手順を実行します。**

1. **[Azure Portal](https://portal.azure.com)** の左側のナビゲーション ウィンドウで、**[Azure Active Directory]** アイコンをクリックします。 

    ![Azure Active Directory のボタン][1]

2. **[エンタープライズ アプリケーション]** に移動します。 次に、**[すべてのアプリケーション]** に移動します。

    ![[エンタープライズ アプリケーション] ブレード][2]
    
3. 新しいアプリケーションを追加するには、ダイアログの上部にある **[新しいアプリケーション]** をクリックします。

    ![[新しいアプリケーション] ボタン][3]

4. 検索ボックスに「**Benefitsolver**」と入力し、結果ウィンドウで **[Benefitsolver]** を選び、**[追加]** をクリックして、アプリケーションを追加します。

    ![結果一覧の Benefitsolver](./media/active-directory-saas-benefitsolver-tutorial/tutorial_benefitsolver_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成とテスト

このセクションでは、"Britta Simon" というテスト ユーザーに基づいて、Benefitsolver で Azure AD のシングル サインオンを構成し、テストします。

シングル サインオンを機能させるには、Azure AD ユーザーに対応する Benefitsolver ユーザーが Azure AD で認識されている必要があります。 言い換えると、Azure AD ユーザーと Benefitsolver の関連ユーザーの間で、リンク関係が確立されている必要があります。

Benefitsolver で、Azure AD の **[ユーザー名]** の値を **[Username]** の値として割り当ててリンク関係を確立します。

Benefitsolver で Azure AD のシングル サインオンを構成してテストするには、次の構成要素を完了する必要があります。

1. **[Azure AD シングル サインオンの構成](#configure-azure-ad-single-sign-on)** - ユーザーがこの機能を使用できるようにします。
2. **[Azure AD のテスト ユーザーの作成](#create-an-azure-ad-test-user)** - Britta Simon で Azure AD のシングル サインオンをテストします。
3. **[Benefitsolver のテスト ユーザーの作成](#create-a-benefitsolver-test-user)** - Benefitsolver で Britta Simon に対応するユーザーを作成し、Azure AD の Britta Simon にリンクさせます。
4. **[Azure AD テスト ユーザーの割り当て](#assign-the-azure-ad-test-user)** - Britta Simon が Azure AD シングル サインオンを使用できるようにします。
5. **[シングル サインオンのテスト](#test-single-sign-on)** - 構成が機能するかどうかを確認します。

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成

このセクションでは、Azure ポータルで Azure AD のシングル サインオンを有効にして、Benefitsolver アプリケーションでシングル サインオンを構成します。

**Benefitsolver で Azure AD シングル サインオンを構成するには、次の手順に従います。**

1. Azure ポータルの **Benefitsolver** アプリケーション統合ページで、**[シングル サインオン]** をクリックします。

    ![シングル サインオン構成のリンク][4]

2. **[シングル サインオン]** ダイアログで、**[モード]** として **[SAML ベースのサインオン]** を選択し、シングル サインオンを有効にします。
 
    ![[シングル サインオン] ダイアログ ボックス](./media/active-directory-saas-benefitsolver-tutorial/tutorial_benefitsolver_samlbase.png)

3. **[Benefitsolver のドメインと URL]** セクションで、次の手順に従います。

    ![[Benefitsolver のドメインと URL] のシングル サインオン情報](./media/active-directory-saas-benefitsolver-tutorial/tutorial_benefitsolver_url.png)

    a. **[サインオン URL]** ボックスに、`http://<companyname>.benefitsolver.com` のパターンを使用して URL を入力します。

    b. **[識別子]** ボックスに、`https://<companyname>.benefitsolver.com/saml20` の形式で URL を入力します。

    c. **[応答 URL]** ボックスに、URL として「`https://www.benefitsolver.com/benefits/BenefitSolverView?page_name=single_signon_saml`」と入力します。

    > [!NOTE] 
    > これらは実際の値ではありません。 実際のサインオン URL、識別子、および応答 URL で値を更新します。 これらの値を取得するには、[Benefitsolver クライアント サポート チーム](https://www.businessolver.com/contact)に問い合わせてください。

4. Benefitsolver アプリケーションでは、特定の形式の SAML アサーションが求められます。そのため、カスタム属性マッピングを **saml トークン属性**構成に追加する必要があります。

    ![Benefitsolver の属性セクション](./media/active-directory-saas-benefitsolver-tutorial/tutorial_attribute.png)

5. **[シングル サインオン]** ダイアログの **[ユーザー属性]** セクションで、図に示すように SAML トークン属性を構成し、次の手順を実行します。
    
    | 属性名| 属性値|
    |---------------|----------------|
    | ClientID | この値は、[Benefitsolver クライアントサポート チーム](https://www.businessolver.com/contact)から入手する必要があります。|
    | ClientKey | この値は、[Benefitsolver クライアントサポート チーム](https://www.businessolver.com/contact)から入手する必要があります。|
    | LogoutURL | この値は、[Benefitsolver クライアントサポート チーム](https://www.businessolver.com/contact)から入手する必要があります。|
    | EmployeeID | この値は、[Benefitsolver クライアントサポート チーム](https://www.businessolver.com/contact)から入手する必要があります。|

    a. [属性の追加] をクリックして [属性の追加] ダイアログを開きます。

    ![Benefitsolver の属性セクション](./media/active-directory-saas-benefitsolver-tutorial/tutorial_attribute_04.png)
    
    ![Benefitsolver の属性セクション](./media/active-directory-saas-benefitsolver-tutorial/tutorial_attribute_05.png)

    b. **[名前]** ボックスに、その行に対して表示される属性名を入力します。
    
    c. **[値]** 一覧から、その行に対して表示される値を入力します。
    
    d. **[OK]**をクリックします。

6. **[SAML 署名証明書]** セクションで、**[Metadata XML]\(メタデータ XML\)** をクリックし、コンピューターにメタデータ ファイルを保存します。

    ![証明書のダウンロードのリンク](./media/active-directory-saas-benefitsolver-tutorial/tutorial_benefitsolver_certificate.png) 

7. **[保存]** ボタンをクリックします。

    ![[シングル サインオンの構成] の [保存] ボタン](./media/active-directory-saas-benefitsolver-tutorial/tutorial_general_400.png)

8. **Benefitsolver** 側にシングル サインオンを構成するには、ダウンロードした**メタデータ XML** を [Benefitsolver サポート チーム](https://www.businessolver.com/contact)に送信する必要があります。

    > [!NOTE]
    > Benefitsolver サポート チームが、実際に SSO を構成する必要があります。 ご使用のサブスクリプションで SSO が有効になると通知が届きます。

> [!TIP]
> アプリのセットアップ中、[Azure Portal](https://portal.azure.com) 内で上記の手順の簡易版を確認できるようになりました。  **[Active Directory] の [エンタープライズ アプリケーション]** セクションからこのアプリを追加した後、**[シングル サインオン]** タブをクリックし、一番下の **[構成]** セクションから組み込みドキュメントにアクセスするだけです。 組み込みドキュメント機能の詳細については、[Azure AD の組み込みドキュメント]( https://go.microsoft.com/fwlink/?linkid=845985)に関する記事をご覧ください。
> 

### <a name="create-an-azure-ad-test-user"></a>Azure AD のテスト ユーザーの作成

このセクションの目的は、Azure Portal で Britta Simon というテスト ユーザーを作成することです。

   ![Azure AD のテスト ユーザーの作成][100]

**Azure AD でテスト ユーザーを作成するには、次の手順に従います。**

1. Azure Portal の左側のウィンドウで、**Azure Active Directory** のボタンをクリックします。

    ![Azure Active Directory のボタン](./media/active-directory-saas-benefitsolver-tutorial/create_aaduser_01.png)

2. ユーザーの一覧を表示するには、**[ユーザーとグループ]** に移動し、**[すべてのユーザー]** をクリックします。

    ![[ユーザーとグループ] と [すべてのユーザー] リンク](./media/active-directory-saas-benefitsolver-tutorial/create_aaduser_02.png)

3. **[ユーザー]** ダイアログ ボックスを開くには、**[すべてのユーザー]** ダイアログ ボックスの上部にある **[追加]** をクリックしてきます。

    ![[追加] ボタン](./media/active-directory-saas-benefitsolver-tutorial/create_aaduser_03.png)

4. **[ユーザー]** ダイアログ ボックスで、次の手順に従います。

    ![[ユーザー] ダイアログ ボックス](./media/active-directory-saas-benefitsolver-tutorial/create_aaduser_04.png)

    a. **[名前]** ボックスに「**BrittaSimon**」と入力します。

    b. **[ユーザー名]** ボックスに、ユーザーである Britta Simon の電子メール アドレスを入力します。

    c. **[パスワードを表示]** チェック ボックスをオンにし、**[パスワード]** ボックスに表示された値を書き留めます。

    d. **Create** をクリックしてください。
 
### <a name="create-a-benefitsolver-test-user"></a>Benefitsolver テスト ユーザーを作成する

Azure AD ユーザーが Benefitsolver にログインできるようにするには、ユーザーを Benefitsolver にプロビジョニングする必要があります。 Benefitsolver の場合、従業員のデータは、人事情報システムの国勢調査ファイルを介して (通常夜間に) アプリケーションに入力されます。

> [!NOTE]
> Benefitsolver から提供されている他の Benefitsolver ユーザー アカウント作成ツールまたは API を使用して、AAD ユーザー アカウントをプロビジョニングできます。

### <a name="assign-the-azure-ad-test-user"></a>Azure AD テスト ユーザーの割り当て

このセクションでは、Britta Simon に Benefitsolver へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

![ユーザー ロールを割り当てる][200] 

**Benefitsolver に Britta Simon を割り当てるには、次の手順に従います。**

1. Azure Portal でアプリケーション ビューを開き、ディレクトリ ビューに移動します。次に、**[エンタープライズ アプリケーション]** に移動し、**[すべてのアプリケーション]** をクリックします。

    ![ユーザーの割り当て][201] 

2. アプリケーションの一覧で **[Benefitsolver]** を選択します。

    ![アプリケーションの一覧の [Benefitsolver] リンク](./media/active-directory-saas-benefitsolver-tutorial/tutorial_benefitsolver_app.png)  

3. 左側のメニューで **[ユーザーとグループ]** をクリックします。

    ![[ユーザーとグループ] リンク][202]

4. **[追加]** ボタンをクリックします。 次に、**[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。

    ![[割り当ての追加] ウィンドウ][203]

5. **[ユーザーとグループ]** ダイアログで、ユーザーの一覧から **[Britta Simon]** を選択します。

6. **[ユーザーとグループ]** ダイアログで **[選択]** をクリックします。

7. **[割り当ての追加]** ダイアログで **[割り当て]** ボタンをクリックします。
    
### <a name="test-single-sign-on"></a>シングル サインオンのテスト

このセクションでは、アクセス パネルを使用して Azure AD のシングル サインオン構成をテストします。

アクセス パネルで [Benefitsolver] タイルをクリックすると、自動的に Benefitsolver アプリケーションにサインオンします。
アクセス パネルの詳細については、[アクセス パネルの概要](active-directory-saas-access-panel-introduction.md)に関する記事を参照してください。 

## <a name="additional-resources"></a>その他のリソース

* [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](active-directory-saas-tutorial-list.md)
* [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-benefitsolver-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-benefitsolver-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-benefitsolver-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-benefitsolver-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-benefitsolver-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-benefitsolver-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-benefitsolver-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-benefitsolver-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-benefitsolver-tutorial/tutorial_general_203.png

