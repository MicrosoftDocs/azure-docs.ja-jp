---
title: "チュートリアル: Azure Active Directory と EverBridge の統合 | Microsoft Docs"
description: "Azure Active Directory と EverBridge の間でシングル サインオンを構成する方法について説明します。"
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.assetid: 58d7cd22-98c0-4606-9ce5-8bdb22ee8b3e
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/16/2017
ms.author: jeedes
ms.translationtype: Human Translation
ms.sourcegitcommit: a1ba750d2be1969bfcd4085a24b0469f72a357ad
ms.openlocfilehash: f5a97fc8df978dd55a73ae53516a82f884c14bec
ms.contentlocale: ja-jp
ms.lasthandoff: 06/20/2017


---
# <a name="tutorial-azure-active-directory-integration-with-everbridge"></a>チュートリアル: Azure Active Directory と EverBridge の統合

このチュートリアルでは、EverBridge と Azure Active Directory (Azure AD) を統合する方法について説明します。

EverBridge と Azure AD の統合には、次の利点があります。

- EverBridge にアクセスする Azure AD ユーザーを制御できます
- ユーザーが自分の Azure AD アカウントで自動的に EverBridge にサインオン (シングル サインオン) できるようにします
- 1 つの中央サイト (Azure Portal) でアカウントを管理できます

SaaS アプリと Azure AD の統合の詳細については、「[Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](active-directory-appssoaccess-whatis.md)」をご覧ください。

## <a name="prerequisites"></a>前提条件

Azure AD と EverBridge の統合を構成するには、次のものが必要です。

- Azure AD サブスクリプション
- EverBridge でのシングル サインオンが有効なサブスクリプション

> [!NOTE]
> このチュートリアルの手順をテストする場合、運用環境を使用しないことをお勧めします。

このチュートリアルの手順をテストするには、次の推奨事項に従ってください。

- 必要な場合を除き、運用環境は使用しないでください。
- Azure AD の評価環境がない場合は、 [こちら](https://azure.microsoft.com/pricing/free-trial/)から 1 か月の評価版を入手できます。

## <a name="scenario-description"></a>シナリオの説明
このチュートリアルでは、テスト環境で Azure AD のシングル サインオンをテストします。 このチュートリアルで説明するシナリオは、主に次の 2 つの要素で構成されています。

1. ギャラリーからの EverBridge の追加
2. Azure AD シングル サインオンの構成とテスト

## <a name="adding-everbridge-from-the-gallery"></a>ギャラリーからの EverBridge の追加
Azure AD への EverBridge の統合を構成するには、ギャラリーから管理対象 SaaS アプリの一覧に EverBridge を追加する必要があります。

**ギャラリーから EverBridge を追加するには、次の手順に従います。**

1. **[Azure Portal](https://portal.azure.com)** の左側のナビゲーション ウィンドウで、**[Azure Active Directory]** アイコンをクリックします。 

    ![Active Directory][1]

2. **[エンタープライズ アプリケーション]** に移動します。 次に、**[すべてのアプリケーション]** に移動します。

    ![アプリケーション][2]
    
3. 新しいアプリケーションを追加するには、ダイアログの上部にある **[新しいアプリケーション]** をクリックします。

    ![アプリケーション][3]

4. 検索ボックスに、「**EverBridge**」と入力します。

    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-everbridge-tutorial/tutorial_everbridge_search.png)

5. 結果ウィンドウで **EverBridge** を選択し、**[追加]** をクリックして、アプリケーションを追加します。

    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-everbridge-tutorial/tutorial_everbridge_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成とテスト
このセクションでは、"Britta Simon" というテスト ユーザーに基づいて、EverBridge で Azure AD のシングル サインオンを構成し、テストします。

シングル サインオンを機能させるには、Azure AD ユーザーに対応する EverBridge ユーザーが Azure AD で認識されている必要があります。 言い換えると、Azure AD ユーザーと EverBridge の関連ユーザーの間で、リンク関係が確立されている必要があります。

EverBridge で、Azure AD の **[ユーザー名]** の値を **[Username]** の値として割り当ててリンク関係を確立します。

EverBridge で Azure AD のシングル サインオンを構成してテストするには、次の構成要素を完了する必要があります。

1. **[Azure AD シングル サインオンの構成](#configuring-azure-ad-single-sign-on)** - ユーザーがこの機能を使用できるようにします。
2. **[Azure AD のテスト ユーザーの作成](#creating-an-azure-ad-test-user)** - Britta Simon で Azure AD のシングル サインオンをテストします。
3. **[EverBridge テスト ユーザーの作成](#creating-an-everbridge-test-user)** - EverBridge で Britta Simon に対応するユーザーを作成し、Azure AD の Britta Simon にリンクさせます。
4. **[Azure AD テスト ユーザーの割り当て](#assigning-the-azure-ad-test-user)** - Britta Simon が Azure AD のシングル サインオンを使用できるようにします。
5. **[Testing Single Sign-On](#testing-single-sign-on)** - 構成が機能するかどうかを確認します。

### <a name="configuring-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成

このセクションでは、Azure Portal で Azure AD のシングル サインオンを有効にして、EverBridge アプリケーションでシングル サインオンを構成します。

**EverBridge で Azure AD シングル サインオンを構成するには、次の手順に従います。**

1. Azure Portal の **EverBridge** アプリケーション統合ページで、**[シングル サインオン]** をクリックします。

    ![[シングル サインオンの構成]][4]

2. **[シングル サインオン]** ダイアログで、**[モード]** として **[SAML ベースのサインオン]** を選択し、シングル サインオンを有効にします。
 
    ![[シングル サインオンの構成]](./media/active-directory-saas-everbridge-tutorial/tutorial_everbridge_samlbase.png)

3. **[EverBridge のドメインと URL]** セクションで、次の手順を実行します。

    ![[シングル サインオンの構成]](./media/active-directory-saas-everbridge-tutorial/tutorial_everbridge_url.png)

    a.[サインオン URL] ボックスに、次のパターンを使用して、ユーザーが Yardi eLearning アプリケーションへのサインオンに使用する URL を入力します。 **[識別子]** ボックスに、`https://sso.everbridge.net/<companyname>` の形式で URL を入力します。

    b. **[応答 URL]** ボックスに、`https://manager.everbridge.net/saml/SSO/<companyname>/alias/defaultAlias` のパターンを使用して URL を入力します。

    > [!NOTE] 
    > これらは実際の値ではありません。 実際の識別子と応答 URL でこれらの値を更新します。 これらの値を取得するには、[EverBridge サポート チーム](mailto:support@everbridge.com)に問い合わせてください。
 
4. **[SAML 署名証明書]** セクションで、**[Metadata XML (メタデータ XML)]** をクリックし、コンピューターにメタデータ ファイルを保存します。

    ![Configure Single Sign-On](./media/active-directory-saas-everbridge-tutorial/tutorial_everbridge_certificate.png) 

5. **[保存]** ボタンをクリックします。

    ![[シングル サインオンの構成]](./media/active-directory-saas-everbridge-tutorial/tutorial_general_400.png)

6. **[EverBridge 構成]** セクションで、**[EverBridge の構成]** をクリックして、**[サインオンの構成]** ウィンドウを開きます。 **[クイック リファレンス]** セクションから **SAML シングル サインオン サービスの URL** をコピーします。

    ![[シングル サインオンの構成]](./media/active-directory-saas-everbridge-tutorial/tutorial_everbridge_configure.png) 

6. アプリケーションに合わせて SSO を構成するには、管理者として Everbridge テナントにサインオンする必要があります。

7. 上部にあるメニューの **[Settings (設定)]** タブをクリックし、**[Security (セキュリティ)]** の **[Single Sign-On (シングル サインオン)]** を選択します。
   
    ![[シングル サインオンの構成]](./media/active-directory-saas-everbridge-tutorial/tutorial_everbridge_002.png)
   
    a. **[Name]\(名前\)** ボックスに、ID プロバイダーの名前 (会社名など) を入力します。
   
    b. **[API Name (API 名)]** ボックスに API の名前を入力します。
   
    c. **[Choose File]\(ファイルの選択\)** ボタンをクリックして、Azure Portal からダウンロードしたメタデータ ファイルをアップロードします。
   
    d. [SAML Identity Location]\(SAML ID の場所\) で、**[Identity is in the NameIdentifier element of the Subject statement]\(ID は Subject ステートメントの NameIdentifier 要素にあります\)** を選択します。
   
    e. **[Identity Provider Login URL]\(ID プロバイダー ログイン URL\)** ボックスに、Azure AD から取得した SAML SSO URL の値を貼り付けます。
   
    ![[シングル サインオンの構成]](./media/active-directory-saas-everbridge-tutorial/tutorial_everbridge_003.png)
   
    f.SAML 属性の属性名またはスキーマ リファレンスを入力します。 [Service Provider Initiated Request Binding]\(サービス プロバイダーが開始した要求のバインド\) で、**[HTTP Redirect]\(HTTP リダイレクト\)** を選択します。

    g. **[保存]**

> [!TIP]
> アプリのセットアップ中、[Azure Portal](https://portal.azure.com) 内で上記の手順の簡易版を確認できるようになりました。  **[Active Directory] の [エンタープライズ アプリケーション]** セクションからこのアプリを追加した後、**[シングル サインオン]** タブをクリックし、一番下の **[構成]** セクションから組み込みドキュメントにアクセスするだけです。 組み込みドキュメント機能の詳細については、[Azure AD の組み込みドキュメント]( https://go.microsoft.com/fwlink/?linkid=845985)に関する記事をご覧ください。
> 

### <a name="creating-an-azure-ad-test-user"></a>Azure AD のテスト ユーザーの作成
このセクションの目的は、Azure Portal で Britta Simon というテスト ユーザーを作成することです。

![Azure AD ユーザーの作成][100]

**Azure AD でテスト ユーザーを作成するには、次の手順に従います。**

1. **Azure Portal** の左側のナビゲーション ウィンドウで、**[Azure Active Directory]** アイコンをクリックします。

    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-everbridge-tutorial/create_aaduser_01.png) 

2. **[ユーザーとグループ]** に移動し、**[すべてのユーザー]** をクリックして、ユーザーの一覧を表示します。
    
    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-everbridge-tutorial/create_aaduser_02.png) 

3. ダイアログの上部にある **[追加]** をクリックして、**[ユーザー]** ダイアログを開きます。
 
    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-everbridge-tutorial/create_aaduser_03.png) 

4. **[ユーザー]** ダイアログ ページで、次の手順を実行します。
 
    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-everbridge-tutorial/create_aaduser_04.png) 

    a. **[名前]** ボックスに「**BrittaSimon**」と入力します。

    b. **[ユーザー名]** ボックスに BrittaSimon の**電子メール アドレス**を入力します。

    c. **[パスワードを表示]** を選択し、**[パスワード]** の値をメモします。

    d. ページの下部にある **[Create]**」を参照してください。
 
### <a name="creating-an-everbridge-test-user"></a>EverBridge テスト ユーザーの作成

このセクションでは、Everbridge で Britta Simon というユーザーを作成します。 [EverBridge サポート チーム](mailto:support@everbridge.com)と協力して、EverBridge プラットフォームにユーザーを追加します。

### <a name="assigning-the-azure-ad-test-user"></a>Azure AD テスト ユーザーの割り当て

このセクションでは、Britta Simon に EverBridge へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

![ユーザーの割り当て][200] 

**Britta Simon を EverBridge に割り当てるには、次の手順に従います。**

1. Azure Portal でアプリケーション ビューを開き、ディレクトリ ビューに移動します。次に、**[エンタープライズ アプリケーション]** に移動し、**[すべてのアプリケーション]** をクリックします。

    ![ユーザーの割り当て][201] 

2. アプリケーションの一覧で **[EverBridge]**を選択します。

    ![[シングル サインオンの構成]](./media/active-directory-saas-everbridge-tutorial/tutorial_everbridge_app.png) 

3. 左側のメニューで **[ユーザーとグループ]** をクリックします。

    ![ユーザーの割り当て][202] 

4. **[追加]** ボタンをクリックします。 次に、**[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。

    ![ユーザーの割り当て][203]

5. **[ユーザーとグループ]** ダイアログで、ユーザーの一覧から **[Britta Simon]** を選択します。

6. **[ユーザーとグループ]** ダイアログで **[選択]** をクリックします。

7. **[割り当ての追加]** ダイアログで **[割り当て]** ボタンをクリックします。
    
### <a name="testing-single-sign-on"></a>シングル サインオンのテスト

このセクションの目的は、アクセス パネルを使用して Azure AD のシングル サインオン構成をテストすることです。

アクセス パネルで [Everbridge] タイルをクリックすると、自動的に Everbridge アプリケーションにサインオンします。

## <a name="additional-resources"></a>その他のリソース

* [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](active-directory-saas-tutorial-list.md)
* [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-everbridge-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-everbridge-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-everbridge-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-everbridge-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-everbridge-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-everbridge-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-everbridge-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-everbridge-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-everbridge-tutorial/tutorial_general_203.png


