---
title: "チュートリアル: Azure Active Directory と Hightail の統合 | Microsoft Docs"
description: "Azure Active Directory と Hightail の間でシングル サインオンを構成する方法について説明します。"
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.assetid: e15206ac-74b0-46e4-9329-892c7d242ec0
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/21/2017
ms.author: jeedes
ms.openlocfilehash: ba55f9b62d274aa3eb91723c62b53f54de0891b5
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="tutorial-azure-active-directory-integration-with-hightail"></a>チュートリアル: Azure Active Directory と Hightail の統合

このチュートリアルでは、Hightail と Azure Active Directory (Azure AD) を統合する方法について説明します。

Hightail と Azure AD の統合には、次の利点があります。

- Hightail にアクセスする Azure AD ユーザーを制御できます。
- ユーザーが自分の Azure AD アカウントで自動的に Hightail にサインオン (シングル サインオン) できるようにします。
- 1 つの中央サイト (Azure Portal) でアカウントを管理できます

SaaS アプリと Azure AD の統合の詳細については、「[Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](active-directory-appssoaccess-whatis.md)」をご覧ください。

## <a name="prerequisites"></a>前提条件

Hightail と Azure AD の統合を構成するには、次のものが必要です。

- Azure AD サブスクリプション
- Hightail でのシングル サインオンが有効なサブスクリプション

> [!NOTE]
> このチュートリアルの手順をテストする場合、運用環境を使用しないことをお勧めします。

このチュートリアルの手順をテストするには、次の推奨事項に従ってください。

- 必要な場合を除き、運用環境は使用しないでください。
- Azure AD の評価環境がない場合は、 [こちら](https://azure.microsoft.com/pricing/free-trial/)から 1 か月の評価版を入手できます。

## <a name="scenario-description"></a>シナリオの説明
このチュートリアルでは、テスト環境で Azure AD のシングル サインオンをテストします。 このチュートリアルで説明するシナリオは、主に次の 2 つの要素で構成されています。

1. ギャラリーから Hightail を追加する
2. Azure AD シングル サインオンの構成とテスト

## <a name="adding-hightail-from-the-gallery"></a>ギャラリーから Hightail を追加する
Azure AD への Hightail の統合を構成するには、ギャラリーから管理対象 SaaS アプリの一覧に Hightail を追加する必要があります。

**ギャラリーから Hightail を追加するには、次の手順を実行します。**

1. **[Azure Portal](https://portal.azure.com)** の左側のナビゲーション ウィンドウで、**[Azure Active Directory]** アイコンをクリックします。 

    ![Active Directory][1]

2. **[エンタープライズ アプリケーション]** に移動します。 次に、**[すべてのアプリケーション]** に移動します。

    ![アプリケーション][2]
    
3. 新しいアプリケーションを追加するには、ダイアログの上部にある **[新しいアプリケーション]** をクリックします。

    ![アプリケーション][3]

4. 検索ボックスに、「 **Hightail**」と入力します。

    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-hightail-tutorial/tutorial_hightail_search.png)

5. 結果ウィンドウで **Hightail** を選択し、**[追加]** をクリックしてアプリケーションを追加します。

    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-hightail-tutorial/tutorial_hightail_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成とテスト
このセクションでは、"Britta Simon" というテスト ユーザーに基づいて、Hightail で Azure AD のシングル サインオンを構成し、テストします。

シングル サインオンを機能させるには、Azure AD ユーザーに対応する Hightail ユーザーが Azure AD で認識されている必要があります。 言い換えると、Azure AD ユーザーと Hightail の関連ユーザーの間で、リンク関係が確立されている必要があります。

Hightail で、Azure AD の **[ユーザー名]** の値を **[Username]** の値として割り当ててリンク関係を確立します。

Hightail で Azure AD のシングル サインオンを構成してテストするには、次の構成要素を完了する必要があります。

1. **[Azure AD シングル サインオンの構成](#configuring-azure-ad-single-sign-on)** - ユーザーがこの機能を使用できるようにします。
2. **[Azure AD のテスト ユーザーの作成](#creating-an-azure-ad-test-user)** - Britta Simon で Azure AD のシングル サインオンをテストします。
3. **[Hightail テスト ユーザーの作成](#creating-a-hightail-test-user)** - Hightail で Britta Simon に対応するユーザーを作成し、Azure AD の Britta Simon にリンクさせます。
4. **[Azure AD テスト ユーザーの割り当て](#assigning-the-azure-ad-test-user)** - Britta Simon が Azure AD のシングル サインオンを使用できるようにします。
5. **[Testing Single Sign-On](#testing-single-sign-on)** - 構成が機能するかどうかを確認します。

### <a name="configuring-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成

このセクションでは、Azure Portal で Azure AD のシングル サインオンを有効にして、Hightail アプリケーションでシングル サインオンを構成します。

**Hightail で Azure AD シングル サインオンを構成するには、次の手順に従います。**

1. Azure Portal の **Hightail** アプリケーション統合ページで、**[シングル サインオン]** をクリックします。

    ![[シングル サインオンの構成]][4]

2. **[シングル サインオン]** ダイアログで、**[モード]** として **[SAML ベースのサインオン]** を選択し、シングル サインオンを有効にします。
 
    ![[シングル サインオンの構成]](./media/active-directory-saas-hightail-tutorial/tutorial_hightail_samlbase.png)

3. **[Hightail のドメインと URL]** セクションで、次の手順に従います。

    ![[シングル サインオンの構成]](./media/active-directory-saas-hightail-tutorial/tutorial_hightail_url.png)

     **[応答 URL]** ボックスに、URL を「`https://www.hightail.com/samlLogin?phi_action=app/samlLogin&subAction=handleSamlResponse`」と入力します。

    > [!NOTE] 
    > 上記の値は、実際の値ではありません。 実際の応答 URL に値を置き換えます。実際の値については後で説明します。
 
4. **SP 開始モード**でアプリケーションを構成する場合は、**[Hightail のドメインと URL]** セクションで次の手順を実行します。
    
    ![[シングル サインオンの構成]](./media/active-directory-saas-hightail-tutorial/tutorial_hightail_url1.png)

    a. **[詳細な URL 設定の表示]** をクリックします。

    b. **[サインオン URL]** ボックスに、URL として「`https://www.hightail.com/loginSSO`」と入力します。

4. **[SAML 署名証明書]** セクションで、**[Certificate (Base64) (証明書 (Base64)) ]** をクリックし、コンピューターに証明書ファイルを保存します。

    ![[シングル サインオンの構成]](./media/active-directory-saas-hightail-tutorial/tutorial_hightail_certificate.png) 

5. Hightail アプリケーションは、特定の形式で構成された SAML アサーションを受け入れます。 このアプリケーションには、次の要求を構成してください。 この属性の値は、アプリケーションの **[属性]** タブから管理できます。 次のスクリーンショットはその例です。 

    ![[シングル サインオンの構成]](./media/active-directory-saas-hightail-tutorial/tutorial_hightail_attribute.png) 

6. **[シングル サインオン]** ダイアログの **[ユーザー属性]** セクションで、図に示すように SAML トークン属性を構成し、次の手順を実行します。
    
    | 属性名 | 属性値 |
    | ------------------- | -------------------- |
    | FirstName | User.givenname |
    | LastName | User.surname |
    | 電子メール | User.mail |    
    | UserIdentity | User.mail |
    
    a. **[属性の追加]** をクリックして **[属性の追加]** ダイアログを開きます。

    ![[シングル サインオンの構成]](./media/active-directory-saas-hightail-tutorial/tutorial_officespace_04.png)

    ![Configure Single Sign-On](./media/active-directory-saas-hightail-tutorial/tutorial_officespace_05.png)

    b. **[名前]** ボックスに、その行に対して表示される属性名を入力します。

    c. **[値]** 一覧から、その行に対して表示される値を入力します。

    d. **[名前空間]**は空白のままにします。
    
    e. **[OK]**をクリックします。

7. **[保存]** ボタンをクリックします。

    ![[シングル サインオンの構成]](./media/active-directory-saas-hightail-tutorial/tutorial_general_400.png)

8. **[Hightail 構成]** セクションで、**[Hightail の構成]** をクリックして、**[サインオンの構成]** ウィンドウを開きます。 **[クイック リファレンス]** セクションから **SAML シングル サインオン サービスの URL** をコピーします。

    ![[シングル サインオンの構成]](./media/active-directory-saas-hightail-tutorial/tutorial_hightail_configure.png) 

    >[!NOTE] 
    >Hightail アプリでシングル サインオンを構成する前に、電子メール ドメインを Hightail チームのホワイト リストに登録し、そのドメインを使用するすべてのユーザーがシングル サインオン機能を利用できるようにします。


9. アプリケーションに合わせて SSO を構成するには、管理者として Hightail テナントにサインオンする必要があります。
   
    a. 上部にあるメニューの **[アカウント]** タブをクリックし、**[Configure SAML]** を選択します。
 
    ![[シングル サインオンの構成]](./media/active-directory-saas-hightail-tutorial/tutorial_hightail_001.png) 

    b. **[Enable SAML Authentication]** チェックボックスをオンにします。

    ![[シングル サインオンの構成]](./media/active-directory-saas-hightail-tutorial/tutorial_hightail_002.png) 

    c. Azure Portal からダウンロードした base-64 でエンコードされた証明書をメモ帳で開き、その内容をクリップボードにコピーして **[SAML Token Signing Certificate]\(SAML トークン署名証明書\)** ボックスに貼り付けます。

    ![[シングル サインオンの構成]](./media/active-directory-saas-hightail-tutorial/tutorial_hightail_003.png) 

    d. **[SAML Authority (Identity Provider)]\(SAML 機関 (ID プロバイダー)\)** ボックスに、Azure Portal からコピーした **SAML シングル サインオン サービス URL** の値を貼り付けます。

    ![[シングル サインオンの構成]](./media/active-directory-saas-hightail-tutorial/tutorial_hightail_004.png)

    e. **IDP 開始モード**でアプリケーションを構成する場合は、**[Identity Provider (IdP) initiated log in]** を選択します。 **SP 開始モード**の場合は、**[Service Provider (SP) initiated log in]** を選択します。

    ![[シングル サインオンの構成]](./media/active-directory-saas-hightail-tutorial/tutorial_hightail_006.png)

    f. インスタンスの SAML コンシューマー URL をコピーし、Azure Portal の **[Hightail ドメインと URL]** セクションの **[応答 URL]** ボックスに貼り付けます。
    
    g. [ **Save**] をクリックします。

> [!TIP]
> アプリのセットアップ中、[Azure Portal](https://portal.azure.com) 内で上記の手順の簡易版を確認できるようになりました。  **[Active Directory] の [エンタープライズ アプリケーション]** セクションからこのアプリを追加した後、**[シングル サインオン]** タブをクリックし、一番下の **[構成]** セクションから組み込みドキュメントにアクセスするだけです。 組み込みドキュメント機能の詳細については、[Azure AD の組み込みドキュメント]( https://go.microsoft.com/fwlink/?linkid=845985)に関する記事をご覧ください。
> 

### <a name="creating-an-azure-ad-test-user"></a>Azure AD のテスト ユーザーの作成
このセクションの目的は、Azure Portal で Britta Simon というテスト ユーザーを作成することです。

![Azure AD ユーザーの作成][100]

**Azure AD でテスト ユーザーを作成するには、次の手順に従います。**

1. **Azure Portal** の左側のナビゲーション ウィンドウで、**[Azure Active Directory]** アイコンをクリックします。

    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-hightail-tutorial/create_aaduser_01.png) 

2. **[ユーザーとグループ]** に移動し、**[すべてのユーザー]** をクリックして、ユーザーの一覧を表示します。
    
    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-hightail-tutorial/create_aaduser_02.png) 

3. ダイアログの上部にある **[追加]** をクリックして、**[ユーザー]** ダイアログを開きます。
 
    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-hightail-tutorial/create_aaduser_03.png) 

4. **[ユーザー]** ダイアログ ページで、次の手順を実行します。
 
    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-hightail-tutorial/create_aaduser_04.png) 

    a. **[名前]** ボックスに「**BrittaSimon**」と入力します。

    b. **[ユーザー名]** ボックスに BrittaSimon の**電子メール アドレス**を入力します。

    c. **[パスワードを表示]** を選択し、**[パスワード]** の値をメモします。

    d. **Create** をクリックしてください。
 
### <a name="creating-a-hightail-test-user"></a>Hightail テスト ユーザーの作成

このセクションの目的は、Hightail で Britta Simon というユーザーを作成することです。 

このセクションでは、ユーザー側で必要な操作はありません。 Hightail は、カスタムの要求に基づく時間どおりのユーザー プロビジョニングをサポートしています。 前述のセクション「 **[Azure AD シングル サインオンの構成](#configuring-azure-ad-single-sign-on)** 」に従ってカスタムの要求を構成した場合、まだ存在していないアプリケーションにユーザーが自動的に作成されます。 

>[!NOTE]
>ユーザーを手動で作成する必要がある場合は、[Hightail サポート チーム](mailto:support@hightail.com)に問い合わせてください。 

### <a name="assigning-the-azure-ad-test-user"></a>Azure AD テスト ユーザーの割り当て

このセクションでは、Britta Simon に Hightail へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

![ユーザーの割り当て][200] 

**Hightail に Britta Simon を割り当てるには、次の手順を実行します。**

1. Azure Portal でアプリケーション ビューを開き、ディレクトリ ビューに移動します。次に、**[エンタープライズ アプリケーション]** に移動し、**[すべてのアプリケーション]** をクリックします。

    ![ユーザーの割り当て][201] 

2. アプリケーションの一覧で **[Hightail]**を選択します。

    ![[シングル サインオンの構成]](./media/active-directory-saas-hightail-tutorial/tutorial_hightail_app.png) 

3. 左側のメニューで **[ユーザーとグループ]** をクリックします。

    ![ユーザーの割り当て][202] 

4. **[追加]** ボタンをクリックします。 次に、**[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。

    ![ユーザーの割り当て][203]

5. **[ユーザーとグループ]** ダイアログで、ユーザーの一覧から **[Britta Simon]** を選択します。

6. **[ユーザーとグループ]** ダイアログで **[選択]** をクリックします。

7. **[割り当ての追加]** ダイアログで **[割り当て]** ボタンをクリックします。
    
### <a name="testing-single-sign-on"></a>シングル サインオンのテスト

このセクションの目的は、アクセス パネルを使用して Azure AD のシングル サインオン構成をテストすることです。

アクセス パネルで Hightail のタイルをクリックすると、自動的に Hightail アプリケーションにサインオンします。


## <a name="additional-resources"></a>その他のリソース

* [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](active-directory-saas-tutorial-list.md)
* [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-hightail-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-hightail-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-hightail-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-hightail-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-hightail-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-hightail-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-hightail-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-hightail-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-hightail-tutorial/tutorial_general_203.png

