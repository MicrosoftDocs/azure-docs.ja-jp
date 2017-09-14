---
title: "チュートリアル: Azure Active Directory と Cerner Central の統合 | Microsoft Docs"
description: "Azure Active Directory と Cerner Central の間でシングル サインオンを構成する方法について説明します。"
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.assetid: d2bc549d-d286-4679-854e-bb67c62b0475
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/22/2017
ms.author: jeedes
ms.translationtype: Human Translation
ms.sourcegitcommit: 6adaf7026d455210db4d7ce6e7111d13c2b75374
ms.openlocfilehash: 77b5fb94cdfa5722081198aabc59fbf86229c2b0
ms.contentlocale: ja-jp
ms.lasthandoff: 06/22/2017


---
# <a name="tutorial-azure-active-directory-integration-with-cerner-central"></a>チュートリアル: Azure Active Directory と Cerner Central の統合

このチュートリアルでは、Cerner Central と Azure Active Directory (Azure AD) を統合する方法について説明します。

Cerner Central と Azure AD の統合には、次の利点があります。

- Cerner Central にアクセスするユーザーを Azure AD で管理できます
- ユーザーが各自の Azure AD アカウントで Cerner Central に自動的にサインオン (シングル サインオン) するように、設定が可能です
- 1 つの中央サイト (Azure Portal) でアカウントを管理できます

SaaS アプリと Azure AD の統合の詳細については、「[Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](active-directory-appssoaccess-whatis.md)」をご覧ください。

## <a name="prerequisites"></a>前提条件

Azure AD と Cerner Central の統合を構成するには、次のものが必要です。

- Azure AD サブスクリプション
- 承認済みの Cerner Central システム アカウント

> [!NOTE]
> このチュートリアルの手順をテストする場合、運用環境を使用しないことをお勧めします。

このチュートリアルの手順をテストするには、次の推奨事項に従ってください。

- 必要な場合を除き、運用環境は使用しないでください。
- Azure AD の評価環境がない場合は、 [こちら](https://azure.microsoft.com/pricing/free-trial/)から 1 か月の評価版を入手できます。

## <a name="scenario-description"></a>シナリオの説明
このチュートリアルでは、テスト環境で Azure AD のシングル サインオンをテストします。 このチュートリアルで説明するシナリオは、主に次の 2 つの要素で構成されています。

1. ギャラリーからの Cerner Central の追加
2. Azure AD シングル サインオンの構成とテスト

## <a name="adding-cerner-central-from-the-gallery"></a>ギャラリーからの Cerner Central の追加
Azure AD への Cerner Central の統合を構成するには、ギャラリーから管理対象 SaaS アプリの一覧に Cerner Central を追加する必要があります。

**ギャラリーから Cerner Central を追加するには、次の手順に従います。**

1. **[Azure Portal](https://portal.azure.com)** の左側のナビゲーション ウィンドウで、**[Azure Active Directory]** アイコンをクリックします。 

    ![Active Directory][1]

2. **[エンタープライズ アプリケーション]** に移動します。 次に、**[すべてのアプリケーション]** に移動します。

    ![アプリケーション][2]
    
3. 新しいアプリケーションを追加するには、ダイアログの上部にある **[新しいアプリケーション]** ボタンをクリックします。

    ![アプリケーション][3]

4. 検索ボックスに、「**Cerner Central**」と入力します。

    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-cernercentral-tutorial/tutorial_cernercentral_search.png)

5. 結果ウィンドウで **[Cerner Central]** を選択し、**[追加]** をクリックして、アプリケーションを追加します。

    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-cernercentral-tutorial/tutorial_cernercentral_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成とテスト
このセクションでは、"Britta Simon" というテスト ユーザーに基づいて、Cerner Central で Azure AD のシングル サインオンを構成し、テストします。

シングル サインオンを機能させるには、Azure AD ユーザーに対応する Cerner Central ユーザーが Azure AD で認識されている必要があります。 言い換えると、Azure AD ユーザーと Cerner Central の関連ユーザーの間で、リンク関係が確立されている必要があります。

Cerner Central で Azure AD のシングル サインオンを構成してテストするには、次の構成要素を完了する必要があります。

1. **[Azure AD シングル サインオンの構成](#configuring-azure-ad-single-sign-on)** - ユーザーがこの機能を使用できるようにします。
2. **[Azure AD のテスト ユーザーの作成](#creating-an-azure-ad-test-user)** - Britta Simon で Azure AD のシングル サインオンをテストします。
3. **[Cerner Central テスト ユーザーの作成](#creating-a-cerner-central-test-user)** - Cerner Central で Britta Simon に対応するユーザーを作成し、Azure AD の Britta Simon にリンクさせます。
4. **[Azure AD テスト ユーザーの割り当て](#assigning-the-azure-ad-test-user)** - Britta Simon が Azure AD のシングル サインオンを使用できるようにします。
5. **[Testing Single Sign-On](#testing-single-sign-on)** - 構成が機能するかどうかを確認します。

### <a name="configuring-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成

このセクションでは、Azure Portal で Azure AD のシングル サインオンを有効にして、Cerner Central アプリケーションでシングル サインオンを構成します。

**Cerner Central で Azure AD シングル サインオンを構成するには、次の手順に従います。**

1. Azure Portal の **Cerner Central** アプリケーション統合ページで、**[シングル サインオン]** をクリックします。

    ![[シングル サインオンの構成]][4]

2. **[シングル サインオン]** ダイアログで、**[モード]** として **[SAML ベースのサインオン]** を選択し、シングル サインオンを有効にします。
 
    ![[シングル サインオンの構成]](./media/active-directory-saas-cernercentral-tutorial/tutorial_cernercentral_samlbase.png)

3. **[Cerner Central のドメインと URL]** セクションで、次の手順を実行します。

    ![[シングル サインオンの構成]](./media/active-directory-saas-cernercentral-tutorial/tutorial_cernercentral_url.png)

    a. **[識別子]** ボックスに、次の形式で値を入力します。
    
    | |
    |--|
    | `https://<instancename>.cernercentral.com/session-api/protocol/saml2/metadata` |
    | `https://<instancename>.sandboxcerner.com/session-api/protocol/saml2/metadata` |
    | `https://<instancename>.sandboxcernercentral.com/session-api/protocol/saml2/metadata` |
    | `https://sandboxcernercentral.com/session-api/protocol/saml2/metadata` |
    | `https://<instancename>.cernercentral.com/session-api/protocol/saml2/metadata` |

    b. **[応答 URL]** ボックスに、次の形式で URL を入力します。 
    | |
    |--|
    | `https://<instancename>.cernercentral.com/session-api/protocol/saml2/sso` |
    | `https://cernercentral.com/<instasncename>` |
    | `https://sandboxcernercentral.com/<instancename>` |
    | `https://sandboxcernercentral.com/<instancename>` |
    | `https://<subdomain>.sandboxcernercentral.com/<instancename>` |

    > [!NOTE] 
    > これらは実際の値ではありません。 実際の識別子と応答 URL でこれらの値を更新します。 これらの値を取得するには、[Cerner Central サポート チーム](https://wiki.ucern.com/display/CernerCentral/Contacting+Cloud+Operations)に連絡してください。
 
4. **[保存]** ボタンをクリックします。

    ![[シングル サインオンの構成]](./media/active-directory-saas-cernercentral-tutorial/tutorial_general_400.png)

5. **メタデータ** URL を生成するには、次の手順を実行します。

    a. **[アプリの登録]** をクリックします。
    
    ![[シングル サインオンの構成]](./media/active-directory-saas-cernercentral-tutorial/tutorial_cernercentral_appregistrations.png)
   
    b. **[エンドポイント]** をクリックして **[エンドポイント]** ダイアログ ボックスを開きます。  
    
    ![[シングル サインオンの構成]](./media/active-directory-saas-cernercentral-tutorial/tutorial_cernercentral_endpointicon.png)

    c. コピー ボタンをクリックして、**フェデレーション メタデータ ドキュメント**の URL をコピーしてノートパッドに貼り付けます。
    
    ![[シングル サインオンの構成]](./media/active-directory-saas-cernercentral-tutorial/tutorial_cernercentral_endpoint.png)
     
    d. 次に、**Cerner Central** のプロパティ ページに移動し、**コピー** ボタンで**アプリケーション ID** をコピーしてノートパッドに貼り付けます。
 
    ![[シングル サインオンの構成]](./media/active-directory-saas-cernercentral-tutorial/tutorial_cernercentral_appid.png)

    e. 次のパターンを使用して**メタデータ URL** を生成します。`<FEDERATION METADATA DOCUMENT url>?appid=<application id>`

6. **Cerner Central** 側でシングル サインオンを構成するには、**メタデータ URL** を [Cerner Central サポート](https://wiki.ucern.com/display/CernerCentral/Contacting+Cloud+Operations)に送信する必要があります。 アプリケーション側で SSO が構成され、統合が完了します。

> [!TIP]
> アプリのセットアップ中、[Azure Portal](https://portal.azure.com) 内で上記の手順の簡易版を確認できるようになりました。  **[Active Directory] の [エンタープライズ アプリケーション]** セクションからこのアプリを追加した後、**[シングル サインオン]** タブをクリックし、一番下の **[構成]** セクションから組み込みドキュメントにアクセスするだけです。 組み込みドキュメント機能の詳細については、[Azure AD の組み込みドキュメント]( https://go.microsoft.com/fwlink/?linkid=845985)に関する記事をご覧ください。
> 

### <a name="creating-an-azure-ad-test-user"></a>Azure AD のテスト ユーザーの作成
このセクションの目的は、Azure Portal で Britta Simon というテスト ユーザーを作成することです。 

![Azure AD ユーザーの作成][100]

**Azure AD でテスト ユーザーを作成するには、次の手順に従います。**

1. **Azure Portal** の左側のナビゲーション ウィンドウで、**[Azure Active Directory]** アイコンをクリックします。

    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-cernercentral-tutorial/create_aaduser_01.png) 

2. **[ユーザーとグループ]** に移動し、**[すべてのユーザー]** をクリックして、ユーザーの一覧を表示します。
    
    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-cernercentral-tutorial/create_aaduser_02.png) 

3. **[ユーザー]** ダイアログを開くために、**[追加]** をクリックします。
 
    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-cernercentral-tutorial/create_aaduser_03.png) 

4. **[ユーザー]** ダイアログ ページで、次の手順を実行します。
 
    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-cernercentral-tutorial/create_aaduser_04.png) 

    a. **[名前]** ボックスに「**BrittaSimon**」と入力します。

    b. **[ユーザー名]** ボックスに、Britta Simon の**電子メール アドレス**を入力します。

    c. **[パスワードを表示]** を選択し、**[パスワード]** の値をメモします。

    d. **[作成]**をクリックします。
 
### <a name="creating-a-cerner-central-test-user"></a>Cerner Central のテスト ユーザーの作成

**Cerner Central** アプリケーションは、すべてのフェデレーション ID プロバイダーからの認証を許可します。 ユーザーがアプリケーションのホーム ページにログインできる場合、それらはフェデレーションされて、手動でプロビジョニングを行う必要はありません。

### <a name="assigning-the-azure-ad-test-user"></a>Azure AD テスト ユーザーの割り当て

このセクションでは、Cerner Central へのアクセスを許可することで、Britta Simon が Azure シングル サインオンを使用できるようにします。

![ユーザーの割り当て][200] 

**Cerner Central に Britta Simon を割り当てるには、次の手順を実行します。**

1. Azure Portal でアプリケーション ビューを開き、ディレクトリ ビューに移動します。次に、**[エンタープライズ アプリケーション]** に移動し、**[すべてのアプリケーション]** をクリックします。

    ![ユーザーの割り当て][201] 

2. アプリケーションの一覧で **[Cerner Central]** を選択します。

    ![[シングル サインオンの構成]](./media/active-directory-saas-cernercentral-tutorial/tutorial_cernercentral_app.png) 

3. 左側のメニューで **[ユーザーとグループ]** をクリックします。

    ![ユーザーの割り当て][202] 

4. **[追加]** ボタンをクリックします。 次に、**[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。

    ![ユーザーの割り当て][203]

5. **[ユーザーとグループ]** ダイアログで、ユーザーの一覧から **[Britta Simon]** を選択します。

6. **[ユーザーとグループ]** ダイアログで **[選択]** をクリックします。

7. **[割り当ての追加]** ダイアログで **[割り当て]** ボタンをクリックします。
    
### <a name="testing-single-sign-on"></a>シングル サインオンのテスト

このセクションでは、アクセス パネルを使用して Azure AD のシングル サインオン構成をテストします。

アクセス パネルで [Cerner Central] タイルをクリックすると、自動的に Cerner Central アプリケーションにサインオンします。 アクセス パネルの詳細については、[アクセス パネルの概要](active-directory-saas-access-panel-introduction.md)に関する記事を参照してください。

## <a name="additional-resources"></a>その他のリソース

* [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](active-directory-saas-tutorial-list.md)
* [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-cernercentral-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-cernercentral-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-cernercentral-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-cernercentral-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-cernercentral-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-cernercentral-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-cernercentral-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-cernercentral-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-cernercentral-tutorial/tutorial_general_203.png


