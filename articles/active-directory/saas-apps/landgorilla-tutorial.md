---
title: チュートリアル:Azure Active Directory と Land Gorilla Client の統合 | Microsoft Docs
description: Azure Active Directory と Land Gorilla の間でシングル サインオンを構成する方法について説明します。
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.assetid: 28acce3e-22a0-4a37-8b66-6e518d777350
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/13/2017
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: b2e1288885476aeeba01f966b615c23c5fe51081
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/13/2019
ms.locfileid: "56199512"
---
# <a name="tutorial-azure-active-directory-integration-with-land-gorilla-client"></a>チュートリアル:Azure Active Directory と Land Gorilla Client の統合

このチュートリアルでは、Land Gorilla Client と Azure Active Directory (Azure AD) を統合する方法について説明します。

Land Gorilla Client と Azure AD の統合には、次の利点があります。

- Land Gorilla Client にアクセスする Azure AD ユーザーを制御できます。
- ユーザーが自分の Azure AD アカウントで自動的に Land Gorilla Client にサインオン (シングル サインオン) できるようにします。
- 1 つの中央サイト (Microsoft Azure 管理ポータル) でアカウントを管理できます

SaaS アプリと Azure AD の統合の詳細については、「 [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)」を参照してください。


## <a name="prerequisites"></a>前提条件

Land Gorilla Client と Azure AD の統合を構成するには、次のものが必要です。

- Azure AD サブスクリプション
- Land Gorilla Client でのシングル サインオンが有効なサブスクリプション


> [!NOTE]
> このチュートリアルの手順をテストする場合、運用環境を使用しないことをお勧めします。


このチュートリアルの手順をテストするには、次の推奨事項に従ってください。

- 必要な場合を除き、運用環境は使用しないでください。
- Azure AD の評価環境がない場合は、 [こちら](https://azure.microsoft.com/pricing/free-trial/)から 1 か月の評価版を入手できます。


## <a name="scenario-description"></a>シナリオの説明
このチュートリアルでは、テスト環境で Azure AD のシングル サインオンをテストします。 このチュートリアルで説明するシナリオは、主に次の 2 つの要素で構成されています。

1. ギャラリーからの Land Gorilla Client の追加
1. Azure AD シングル サインオンの構成とテスト


## <a name="adding-land-gorilla-client-from-the-gallery"></a>ギャラリーからの Land Gorilla Client の追加
Azure AD への Land Gorilla Client の統合を構成するには、ギャラリーから管理対象 SaaS アプリの一覧に Land Gorilla Client を追加する必要があります。

**ギャラリーから Land Gorilla Client を追加するには、次の手順に従います。**

1. **[Microsoft Azure 管理ポータル](https://portal.azure.com)** の左側のナビゲーション ウィンドウで、**[Azure Active Directory]** アイコンをクリックします。 

    ![Active Directory][1]

1. **[エンタープライズ アプリケーション]** に移動します。 次に、**[すべてのアプリケーション]** に移動します。

    ![[アプリケーション]][2]
    
1. ダイアログの上部にある **[追加]** をクリックします。

    ![[アプリケーション]][3]

1. 検索ボックスに、「**Land Gorilla Client**」と入力します。

    ![Azure AD のテスト ユーザーの作成](./media/landgorilla-tutorial/tutorial_landgorilla_search.png)

1. 結果ウィンドウで **[Land Gorilla Client]** を選択し、**[追加]** をクリックして、アプリケーションを追加します。

    ![Azure AD のテスト ユーザーの作成](./media/landgorilla-tutorial/tutorial_landgorilla_addfromgallery.png)


##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成とテスト
このセクションでは、"Britta Simon" というテスト ユーザーに基づいて、Land Gorilla Client で Azure AD のシングル サインオンを構成し、テストします。

シングル サインオンを機能させるには、Azure AD ユーザーに対応する Land Gorilla Client ユーザーが Azure AD で認識されている必要があります。 言い換えると、Azure AD ユーザーと Land Gorilla Client の関連ユーザーの間で、リンク関係が確立されている必要があります。

このリンク関係を確立するには、Azure AD の **[ユーザー名]** の値を Land Gorilla Client の **[Username (ユーザー名)]** の値として割り当てます。

Land Gorilla Client で Azure AD のシングル サインオンを構成してテストするには、次の構成要素を完了する必要があります。

1. **[Azure AD シングル サインオンの構成](#configuring-azure-ad-single-sign-on)** - ユーザーがこの機能を使用できるようにします。
1. **[Azure AD のテスト ユーザーの作成](#creating-an-azure-ad-test-user)** - 制限されたグループで Azure AD のシングル サインオンをテストします。
1. **[Land Gorilla のテスト ユーザーの作成](#creating-a-land-gorilla-test-user)** - Britta Simon で Azure AD のシングル サインオンをテストします。
1. **[Azure AD テスト ユーザーの割り当て](#assigning-the-azure-ad-test-user)** - Britta Simon が Azure AD のシングル サインオンを使用できるようにします。
1. **[シングル サインオンのテスト](#testing-single-sign-on)** - 構成が機能するかどうかを確認します。

### <a name="configuring-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成

このセクションでは、Microsoft Azure 管理ポータルで Azure AD のシングル サインオンを有効にし、Land Gorilla Client アプリケーションでシングル サインオンを構成します。

**Land Gorilla Client で Azure AD シングル サインオンを構成するには、次の手順に従います。**

1. Microsoft Azure 管理ポータルの **Land Gorilla Client** アプリケーション統合ページで、**[シングル サインオン]** をクリックします。

    ![Configure single sign-on][4]

1. **[シングル サインオン]** ダイアログで、**[モード]** として **[SAML ベースのサインオン]** を選択し、シングル サインオンを有効にします。
 
    ![Configure single sign-on](./media/landgorilla-tutorial/tutorial_landgorilla_samlbase.png)

1. **[Land Gorilla Client のドメインと URL]** セクションで、次の手順を実行します。

    ![Configure single sign-on](./media/landgorilla-tutorial/tutorial_landgorilla_url_02.png)

    a. **[識別子]** ボックスに、次のいずれかの形式で値を入力します。 
    
    `https://<customer domain>.landgorilla.com/` 
    
    `https://www.<customer domain>.landgorilla.com`

    b. **[応答 URL]** ボックスに、次のいずれかの形式で URL を入力します。

    `https://<customer domain>.landgorilla.com/simplesaml/module.php/core/authenticate.php`

    `https://www.<customer domain>.landgorilla.com/simplesaml/module.php/core/authenticate.php`

    `https://<customer domain>.landgorilla.com/simplesaml/module.php/saml/sp/saml2-acs.php/default-sp`
    
    `https://www.<customer domain>.landgorilla.com/simplesaml/module.php/saml/sp/saml2-acs.php/default-sp`

    > [!NOTE] 
    > これは実際の値ではないので注意してください。 これらの値は、実際の識別子と応答 URL で更新する必要があります。 ここでは、識別子に一意の文字列値を使用することをお勧めします。 これらの値の取得については、[Land Gorilla Client チーム](https://www.landgorilla.com/support/)にお問い合わせください。 

1. **[SAML 署名証明書]** セクションで、**[メタデータ XML]** をクリックし、コンピューターに XML ファイルを保存します。

    ![Configure single sign-on](./media/landgorilla-tutorial/tutorial_landgorilla_certificate.png) 

1. **[保存]** ボタンをクリックします。

    ![Configure single sign-on](./media/landgorilla-tutorial/tutorial_general_400.png) 

1. アプリケーションの SSO 構成を Land Gorilla 側で完成させるには、[Land Gorilla Client サポート チーム](https://www.landgorilla.com/support/)に問い合わせてダウンロード済みの**メタデータ XML** ファイルを提供してください。


### <a name="creating-an-azure-ad-test-user"></a>Azure AD のテスト ユーザーの作成
このセクションの目的は、Microsoft Azure 管理ポータルで Britta Simon というテスト ユーザーを作成することです。

![Azure AD ユーザーの作成][100]

**Azure AD でテスト ユーザーを作成するには、次の手順に従います。**

1. **Microsoft Azure 管理ポータル**の左側のナビゲーション ウィンドウで、**[Azure Active Directory]** アイコンをクリックします。

    ![Azure AD のテスト ユーザーの作成](./media/landgorilla-tutorial/create_aaduser_01.png) 

1. **[ユーザーとグループ]** に移動し、**[すべてのユーザー]** をクリックして、ユーザーの一覧を表示します。
    
    ![Azure AD のテスト ユーザーの作成](./media/landgorilla-tutorial/create_aaduser_02.png) 

1. ダイアログの上部にある **[追加]** をクリックして **[ユーザー]** ダイアログを開きます。
 
    ![Azure AD のテスト ユーザーの作成](./media/landgorilla-tutorial/create_aaduser_03.png) 

1. **[ユーザー]** ダイアログ ページで、次の手順を実行します。
 
    ![Azure AD のテスト ユーザーの作成](./media/landgorilla-tutorial/create_aaduser_04.png) 

    a. **[名前]** ボックスに「**BrittaSimon**」と入力します。

    b. **[ユーザー名]** ボックスに BrittaSimon の**電子メール アドレス**を入力します。

    c. **[パスワードを表示]** を選択し、**[パスワード]** の値をメモします。

    d.[Tableau Server return URL]: Tableau Server ユーザーがアクセスする URL。 **Create** をクリックしてください。 

### <a name="creating-a-land-gorilla-test-user"></a>Land Gorilla テスト ユーザーの作成

[Land Gorilla サポート チーム](https://www.landgorilla.com/support/)と連携し、Land Gorilla プラットフォームにユーザーを追加してください。
    
### <a name="assigning-the-azure-ad-test-user"></a>Azure AD テスト ユーザーの割り当て

このセクションでは、Britta Simon に Land Gorilla Client へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

![ユーザーの割り当て][200] 

**Land Gorilla Client に Britta Simon を割り当てるには、次の手順に従います。**

1. Azure 管理ポータルでアプリケーション ビューを開き、ディレクトリ ビューに移動します。次に、**[エンタープライズ アプリケーション]** に移動し、**[すべてのアプリケーション]** をクリックします。

    ![ユーザーの割り当て][201] 

1. アプリケーションの一覧から、**Land Gorilla Client** を選択します。

    ![Configure single sign-on](./media/landgorilla-tutorial/tutorial_landgorilla_app.png) 

1. 左側のメニューで **[ユーザーとグループ]** をクリックします。

    ![ユーザーの割り当て][202] 

1. **[追加]** ボタンをクリックします。 次に、**[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。

    ![ユーザーの割り当て][203]

1. **[ユーザーとグループ]** ダイアログで、ユーザーの一覧から **[Britta Simon]** を選択します。

1. **[ユーザーとグループ]** ダイアログで **[選択]** をクリックします。

1. **[割り当ての追加]** ダイアログで **[割り当て]** ボタンをクリックします。
    


### <a name="testing-single-sign-on"></a>シングル サインオンのテスト

このセクションでは、アクセス パネルを使用して Azure AD のシングル サインオン構成をテストします。

アクセス パネルで [Land Gorilla Client] タイルをクリックすると、自動的に Land Gorilla Client アプリケーションにサインオンします。


## <a name="additional-resources"></a>その他のリソース

* [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](tutorial-list.md)
* [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/landgorilla-tutorial/tutorial_general_01.png
[2]: ./media/landgorilla-tutorial/tutorial_general_02.png
[3]: ./media/landgorilla-tutorial/tutorial_general_03.png
[4]: ./media/landgorilla-tutorial/tutorial_general_04.png

[100]: ./media/landgorilla-tutorial/tutorial_general_100.png
[200]: ./media/landgorilla-tutorial/tutorial_general_200.png
[201]: ./media/landgorilla-tutorial/tutorial_general_201.png
[202]: ./media/landgorilla-tutorial/tutorial_general_202.png
[203]: ./media/landgorilla-tutorial/tutorial_general_203.png
