---
title: "チュートリアル: Azure Active Directory と Adobe Creative Cloud の統合 | Microsoft Docs"
description: "Azure Active Directory と Adobe Creative Cloud の間でシングル サインオンを構成する方法について説明します。"
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: c199073f-02ce-45c2-b515-8285d4bbbca2
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/15/2017
ms.author: jeedes
ms.openlocfilehash: ad2c3999ac8bfc8dfe0fc662ee37b41fff38444f
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="tutorial-azure-active-directory-integration-with-adobe-creative-cloud"></a>チュートリアル: Azure Active Directory と Adobe Creative Cloud の統合

このチュートリアルでは、Adobe Creative Cloud と Azure Active Directory (Azure AD) を統合する方法について説明します。

Adobe Creative Cloud と Azure AD の統合には、次の利点があります。

- Adobe Creative Cloud にアクセスする Azure AD ユーザーを制御できます。
- ユーザーが自分の Azure AD アカウントで自動的に Adobe Creative Cloud にサインオン (シングル サインオン) するよう指定できます。
- 1 つの中央サイト (Azure Portal) でアカウントを管理できます。

SaaS アプリと Azure AD の統合の詳細については、「[Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](active-directory-appssoaccess-whatis.md)」をご覧ください。

## <a name="prerequisites"></a>前提条件

Adobe Creative Cloud と Azure AD の統合を構成するには、次のものが必要です。

- Azure AD サブスクリプション
- Adobe Creative Cloud でのシングル サインオンが有効なサブスクリプション

> [!NOTE]
> このチュートリアルの手順をテストする場合、運用環境を使用しないことをお勧めします。

このチュートリアルの手順をテストするには、次の推奨事項に従ってください。

- 必要な場合を除き、運用環境は使用しないでください。
- Azure AD の評価環境がない場合は、[1 か月の評価版を入手できます](https://azure.microsoft.com/pricing/free-trial/)。

## <a name="scenario-description"></a>シナリオの説明
このチュートリアルでは、テスト環境で Azure AD のシングル サインオンをテストします。 このチュートリアルで説明するシナリオは、主に次の 2 つの要素で構成されています。

1. ギャラリーから Adobe Creative Cloud を追加する
2. Azure AD シングル サインオンの構成とテスト

## <a name="adding-adobe-creative-cloud-from-the-gallery"></a>ギャラリーから Adobe Creative Cloud を追加する
Azure AD への Adobe Creative Cloud の統合を構成するには、ギャラリーから管理対象 SaaS アプリの一覧に Adobe Creative Cloud を追加する必要があります。

**ギャラリーから Adobe Creative Cloud を追加するには、次の手順に従います。**

1. **[Azure Portal](https://portal.azure.com)** の左側のナビゲーション ウィンドウで、**[Azure Active Directory]** アイコンをクリックします。 

    ![Azure Active Directory のボタン][1]

2. **[エンタープライズ アプリケーション]** に移動します。 次に、**[すべてのアプリケーション]** に移動します。

    ![[エンタープライズ アプリケーション] ブレード][2]
    
3. 新しいアプリケーションを追加するには、ダイアログの上部にある **[新しいアプリケーション]** をクリックします。

    ![[新しいアプリケーション] ボタン][3]

4. 検索ボックスに「**Adobe Creative Cloud**」と入力し、結果パネルで **Adobe Creative Cloud** を選択し、**[追加]** をクリックして、アプリケーションを追加します。

    ![結果リストの Adobe Creative Cloud](./media/active-directory-saas-adobe-creative-cloud-tutorial/tutorial_adobecreativecloud_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成とテスト

このセクションでは、"Britta Simon" というテスト ユーザーに基づいて、Adobe Creative Cloud で Azure AD のシングル サインオンを構成し、テストします。

シングル サインオンを機能させるには、Azure AD ユーザーに対応する Adobe Creative Cloud ユーザーが Azure AD で認識されている必要があります。 言い換えると、Azure AD ユーザーと Adobe Creative Cloud の関連ユーザーの間で、リンク関係が確立されている必要があります。

Adobe Creative Cloud で、Azure AD の **[ユーザー名]** の値を **[Username]** の値として割り当てて、リンク関係を確立します。

Adobe Creative Cloud での Azure AD シングル サインオンを構成してテストするには、次の構成要素を完了する必要があります。

1. **[Azure AD シングル サインオンの構成](#configure-azure-ad-single-sign-on)** - ユーザーがこの機能を使用できるようにします。
2. **[Azure AD のテスト ユーザーの作成](#create-an-azure-ad-test-user)** - Britta Simon で Azure AD のシングル サインオンをテストします。
3. **[Adobe Creative Cloud のテスト ユーザーの作成](#create-an-adobe-creative-cloud-test-user)** - Adobe Creative Cloud で Britta Simon に対応するユーザーを作成し、Azure AD の Britta Simon にリンクさせます。
4. **[Azure AD テスト ユーザーの割り当て](#assign-the-azure-ad-test-user)** - Britta Simon が Azure AD シングル サインオンを使用できるようにします。
5. **[シングル サインオンのテスト](#test-single-sign-on)** - 構成が機能するかどうかを確認します。

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成

このセクションでは、Azure Portal で Azure AD のシングル サインオンを有効にして、Adobe Creative Cloud アプリケーションにシングル サインオンを構成します。

**Adobe Creative Cloud との Azure AD シングル サインオンを構成するには、次の手順を実行します。**

1. Azure Portal の **Adobe Creative Cloud** アプリケーション統合ページで、**[シングル サインオン]** をクリックします。

    ![シングル サインオン構成のリンク][4]

2. **[シングル サインオン]** ダイアログで、**[モード]** として **[SAML ベースのサインオン]** を選択し、シングル サインオンを有効にします。
 
    ![[シングル サインオン] ダイアログ ボックス](./media/active-directory-saas-adobe-creative-cloud-tutorial/tutorial_adobecreativecloud_samlbase.png)

3. **[Adobe Creative Cloud のドメインと URL]** セクションで、IDP 開始モードでアプリケーションを構成する場合は、次の手順を実行します。

    ![[Adobe Creative Cloud のドメインと URL] のシングル サインオン情報](./media/active-directory-saas-adobe-creative-cloud-tutorial/tutorial_adobecreativecloud_url.png)

    a. **[識別子]** ボックスに、`https://www.okta.com/saml2/service-provider/<token>` の形式で URL を入力します。

    b. **[応答 URL]** ボックスに、`https://<company name>.okta.com/auth/saml20/accauthlinktest` のパターンを使用して URL を入力します。

    > [!NOTE] 
    > これらは実際の値ではありません。 実際の識別子と応答 URL でこれらの値を更新します。 これらの値を取得するには、[Adobe Creative Cloud クライアント サポート チーム](https://helpx.adobe.com/in/contact/support.html)に問い合わせください。 

4. アプリケーションを **SP** 開始モードで構成する場合は、**[詳細な URL 設定の表示]** チェックボックスをオンにして次の手順を実行します。

    ![[Adobe Creative Cloud のドメインと URL] のシングル サインオン情報](./media/active-directory-saas-adobe-creative-cloud-tutorial/tutorial_adobecreativecloud_url2.png)

    **[サインオン URL]** テキストボックスに、「`https://adobe.com`」と入力します。

5. **[SAML 署名証明書]** セクションで、**[証明書 (Base64)]** をクリックし、コンピューターに証明書ファイルを保存します。

    ![証明書のダウンロードのリンク](./media/active-directory-saas-adobe-creative-cloud-tutorial/tutorial_adobecreativecloud_certificate.png)
     
6. Adobe Creative Cloud アプリケーションは、特定の形式で構成された SAML アサーションを受け入れます。 このアプリケーションには、次の要求を構成します。 これらの属性の値は、アプリケーションの **[ユーザー属性]** タブから管理できます。 次のスクリーンショットはその例です。

    ![[シングル サインオンの構成]](./media/active-directory-saas-adobe-creative-cloud-tutorial/tutorial_attribute.png)

7. **[シングル サインオン]** ダイアログの **[ユーザー属性]** セクションで、上の図に示すように SAML トークン属性を構成し、次の手順を実行します。

    | 属性名 | 属性値 |
    | ---------------| ----------------|
    | FirstName |User.givenname |
    | LastName |User.surname |
    | 電子メール |User.mail |

    a. **[属性の追加]** をクリックして **[属性の追加]** ダイアログを開きます。
    
    ![[シングル サインオンの構成]](./media/active-directory-saas-adobe-creative-cloud-tutorial/tutorial_attribute_04.png)
    
    ![Configure Single Sign-On](./media/active-directory-saas-adobe-creative-cloud-tutorial/tutorial_attribute_05.png)
    
    b. **[名前]** ボックスに、その行に対して表示される属性名を入力します。
    
    c. **[値]** 一覧から、その行に対して表示される値を入力します。
    
    d. **[OK]**をクリックします。
    
8. **[保存]** ボタンをクリックします。

    ![[シングル サインオンの構成] の [保存] ボタン](./media/active-directory-saas-adobe-creative-cloud-tutorial/tutorial_general_400.png)
    
9. **[Adobe Creative Cloud Configuration (Adobe Creative Cloud 構成)]** セクションで、**[Configure Adobe Creative Cloud (Adobe Creative Cloud の構成)]** をクリックして **[サインオンの構成]** ウィンドウを開きます。 **[クイック リファレンス] セクション**から、**SAML エンティティ ID と SAML Single シングル サインオン サービス URL** をコピーします。

    ![Adobe Creative Cloud の構成](./media/active-directory-saas-adobe-creative-cloud-tutorial/tutorial_adobecreativecloud_configure.png)
    
10. 別の Web ブラウザーのウィンドウで、管理者として Adobe Creative Cloud テナントにサインオンします。

11. 左のナビゲーション ウィンドウで **[ID]** に移動して、ドメインをクリックします。 その後、**[Single Sign On Configuration Required (シングル サインオンの構成が必要です)]** セクションで、次の手順を実行します。

    ![設定](./media/active-directory-saas-adobe-creative-cloud-tutorial/tutorial_adobe-creative-cloud_001.png "Settings")
    
    a. **[Browse (参照)]** をクリックして、Azure AD からダウンロードした証明書を **[IDP Certificate (IDP 証明書)]** にアップロードします。
    
    b. **[IDP issuer (IDP 発行者)]** ボックスで、Azure ポータルの **[サインオンの構成]** セクションからコピーした **SAML エンティティ ID** の値を入力します。
    
    c. **[IDP Login URL (IDP ログイン URL)]** ボックスで、Azure ポータルの **[サインオンの構成]** セクションからコピーした **SAML SSO サービスの URL** の値を入力します。
    
    d. **[IDP Binding (IDP バインディング)]** として **[HTTP - Redirect (HTTP - リダイレクト)]** を選択します。
    
    e. **[User Login Setting (ユーザー ログイン設定)]** として **[Email Address (電子メール アドレス])** を選択します。
    
    f.SAML 属性の属性名またはスキーマ リファレンスを入力します。 **[保存]** ボタンをクリックします。

12. ダッシュボードに、XML の **"メタデータのダウンロード"** ファイルが表示されます。 これには、アドビの EntityDescriptor URL と AssertionConsumerService URL が含まれています。 ファイルを開き、Azure AD アプリケーションでこれらを構成してください。

    ![アプリ側でのシングル サインオンの構成](./media/active-directory-saas-adobe-creative-cloud-tutorial/tutorial_adobe-creative-cloud_002.png)

    ![アプリ側でのシングル サインオンの構成](./media/active-directory-saas-adobe-creative-cloud-tutorial/tutorial_adobe-creative-cloud_003.png)

    a. **[アプリケーション設定の構成]** ダイアログの**識別子**として、アドビによって提供された EntityDescriptor 値を使用します。

    b. **[アプリケーション設定の構成]** ダイアログの **[応答 URL]** として、アドビによって提供された AssertionConsumerService 値を使用します。

> [!TIP]
> アプリのセットアップ中、[Azure Portal](https://portal.azure.com) 内で上記の手順の簡易版を確認できるようになりました。  **[Active Directory] の [エンタープライズ アプリケーション]** セクションからこのアプリを追加した後、**[シングル サインオン]** タブをクリックし、一番下の **[構成]** セクションから組み込みドキュメントにアクセスするだけです。 組み込みドキュメント機能の詳細については、[Azure AD の組み込みドキュメント]( https://go.microsoft.com/fwlink/?linkid=845985)に関する記事をご覧ください。

### <a name="create-an-azure-ad-test-user"></a>Azure AD のテスト ユーザーの作成

このセクションの目的は、Azure Portal で Britta Simon というテスト ユーザーを作成することです。

   ![Azure AD のテスト ユーザーの作成][100]

**Azure AD でテスト ユーザーを作成するには、次の手順に従います。**

1. Azure Portal の左側のウィンドウで、**Azure Active Directory** のボタンをクリックします。

    ![Azure Active Directory のボタン](./media/active-directory-saas-adobe-creative-cloud-tutorial/create_aaduser_01.png)

2. ユーザーの一覧を表示するには、**[ユーザーとグループ]** に移動し、**[すべてのユーザー]** をクリックします。

    ![[ユーザーとグループ] と [すべてのユーザー] リンク](./media/active-directory-saas-adobe-creative-cloud-tutorial/create_aaduser_02.png)

3. **[ユーザー]** ダイアログ ボックスを開くには、**[すべてのユーザー]** ダイアログ ボックスの上部にある **[追加]** をクリックしてきます。

    ![[追加] ボタン](./media/active-directory-saas-adobe-creative-cloud-tutorial/create_aaduser_03.png)

4. **[ユーザー]** ダイアログ ボックスで、次の手順に従います。

    ![[ユーザー] ダイアログ ボックス](./media/active-directory-saas-adobe-creative-cloud-tutorial/create_aaduser_04.png)

    a. **[名前]** ボックスに「**BrittaSimon**」と入力します。

    b. **[ユーザー名]** ボックスに、ユーザーである Britta Simon の電子メール アドレスを入力します。

    c. **[パスワードを表示]** チェック ボックスをオンにし、**[パスワード]** ボックスに表示された値を書き留めます。

    d. **Create** をクリックしてください。
 
### <a name="create-an-adobe-creative-cloud-test-user"></a>Adobe Creative Cloud テスト ユーザーの作成

Azure AD ユーザーが Adobe Creative Cloud にログインできるようにするには、そのユーザーを Adobe Creative Cloud にプロビジョニングする必要があります。 Adobe Creative Cloud の場合、プロビジョニングは手動で実行します。

### <a name="to-provision-a-user-accounts-perform-the-following-steps"></a>ユーザー アカウントをプロビジョニングするには、次の手順に従います。

1. Adobe Creative Cloud 企業サイトに管理者としてログインします。

2. Adobe のコンソール内でフェデレーション ID としてユーザーを追加し、権利用のグループに割り当てます

    ![アプリケーションの一覧の Adobe Creative Cloud リンク](./media/active-directory-saas-adobe-creative-cloud-tutorial/users.png)  

3. ここで、Adobe サインイン フォームにメール アドレス/UPN を入力し、Tab キーを押すと、Azure AD にフェデレーションされます。
    * Web アクセス: www.adobe.com > サインイン
    * デスクトップ アプリ ユーティリティ内 > サインイン
    * アプリケーション内 > ヘルプ > サインイン

### <a name="assign-the-azure-ad-test-user"></a>Azure AD テスト ユーザーの割り当て

このセクションでは、Britta Simon に Adobe Creative Cloud へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

![ユーザー ロールを割り当てる][200] 

**Adobe Creative Cloud に Britta Simon を割り当てるには、次の手順に従います。**

1. Azure Portal でアプリケーション ビューを開き、ディレクトリ ビューに移動します。次に、**[エンタープライズ アプリケーション]** に移動し、**[すべてのアプリケーション]** をクリックします。

    ![ユーザーの割り当て][201] 

2. アプリケーションの一覧で **[Adobe Creative Cloud]** を選択します。

    ![アプリケーションの一覧の Adobe Creative Cloud リンク](./media/active-directory-saas-adobe-creative-cloud-tutorial/tutorial_adobecreativecloud_app.png)  

3. 左側のメニューで **[ユーザーとグループ]** をクリックします。

    ![[ユーザーとグループ] リンク][202]

4. **[追加]** ボタンをクリックします。 次に、**[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。

    ![[割り当ての追加] ウィンドウ][203]

5. **[ユーザーとグループ]** ダイアログで、ユーザーの一覧から **[Britta Simon]** を選択します。

6. **[ユーザーとグループ]** ダイアログで **[選択]** をクリックします。

7. **[割り当ての追加]** ダイアログで **[割り当て]** ボタンをクリックします。
    
### <a name="test-single-sign-on"></a>シングル サインオンのテスト

このセクションでは、アクセス パネルを使用して Azure AD のシングル サインオン構成をテストします。

アクセス パネルで [Adobe Creative Cloud] タイルをクリックすると、自動的に Adobe Creative Cloud アプリケーションにサインオンします。
アクセス パネルの詳細については、[アクセス パネルの概要](active-directory-saas-access-panel-introduction.md)に関する記事を参照してください。 

## <a name="additional-resources"></a>その他のリソース

* [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](active-directory-saas-tutorial-list.md)
* [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-adobe-creative-cloud-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-adobe-creative-cloud-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-adobe-creative-cloud-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-adobe-creative-cloud-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-adobe-creative-cloud-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-adobe-creative-cloud-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-adobe-creative-cloud-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-adobe-creative-cloud-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-adobe-creative-cloud-tutorial/tutorial_general_203.png

