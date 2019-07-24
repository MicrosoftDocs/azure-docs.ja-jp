---
title: チュートリアル:Azure Active Directory と Rightscale の統合 | Microsoft Docs
description: Azure Active Directory と Rightscale の間でシングル サインオンを構成する方法について説明します。
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.assetid: 3a8d376d-95fb-4dd7-832a-4fdd4dd7c87c
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/08/2017
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 416a98c5f9c5a2ec813206ea9ea7f311b23e86cb
ms.sourcegitcommit: 1c2cf60ff7da5e1e01952ed18ea9a85ba333774c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/12/2019
ms.locfileid: "59525581"
---
# <a name="tutorial-azure-active-directory-integration-with-rightscale"></a>チュートリアル:Azure Active Directory と Rightscale の統合

このチュートリアルでは、Rightscale と Azure Active Directory (Azure AD) を統合する方法について説明します。

Rightscale と Azure AD の統合には、次の利点があります。

- Rightscale にアクセスする Azure AD ユーザーを制御できます。
- ユーザーが自分の Azure AD アカウントで自動的に Rightscale にサインオン (シングル サインオン) できるようにします。
- 1 つの中央サイト (Azure Portal) でアカウントを管理できます

SaaS アプリと Azure AD の統合の詳細については、「[Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)」をご覧ください。

## <a name="prerequisites"></a>前提条件

Azure AD と Rightscale の統合を構成するには、次のものが必要です。

- Azure AD サブスクリプション
- Rightscale でのシングル サインオンが有効なサブスクリプション

> [!NOTE]
> このチュートリアルの手順をテストする場合、運用環境を使用しないことをお勧めします。

このチュートリアルの手順をテストするには、次の推奨事項に従ってください。

- 必要な場合を除き、運用環境は使用しないでください。
- Azure AD の評価環境がない場合は、 [こちら](https://azure.microsoft.com/pricing/free-trial/)から 1 か月の評価版を入手できます。

## <a name="scenario-description"></a>シナリオの説明
このチュートリアルでは、テスト環境で Azure AD のシングル サインオンをテストします。 このチュートリアルで説明するシナリオは、主に次の 2 つの要素で構成されています。

1. ギャラリーからの Rightscale の追加
1. Azure AD シングル サインオンの構成とテスト

## <a name="adding-rightscale-from-the-gallery"></a>ギャラリーからの Rightscale の追加
Azure AD への Rightscale の統合を構成するには、ギャラリーから管理対象 SaaS アプリの一覧に Rightscale を追加する必要があります。

**ギャラリーから Rightscale を追加するには、次の手順に従います。**

1. **[Azure Portal](https://portal.azure.com)** の左側のナビゲーション ウィンドウで、**[Azure Active Directory]** アイコンをクリックします。 

    ![Active Directory][1]

1. **[エンタープライズ アプリケーション]** に移動します。 次に、**[すべてのアプリケーション]** に移動します。

    ![[アプリケーション]][2]
    
1. 新しいアプリケーションを追加するには、ダイアログの上部にある **[新しいアプリケーション]** をクリックします。

    ![[アプリケーション]][3]

1. 検索ボックスに、「**Rightscale**」と入力します。

    ![Azure AD のテスト ユーザーの作成](./media/rightscale-tutorial/tutorial_rightscale_search.png)

1. 結果ウィンドウで **[Rightscale]** を選択し、**[追加]** をクリックして、アプリケーションを追加します。

    ![Azure AD のテスト ユーザーの作成](./media/rightscale-tutorial/tutorial_rightscale_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成とテスト
このセクションでは、"Britta Simon" というテスト ユーザーに基づいて、Rightscale で Azure AD のシングル サインオンを構成し、テストします。

シングル サインオンを機能させるには、Azure AD ユーザーに対応する Rightscale ユーザーが Azure AD で認識されている必要があります。 言い換えると、Azure AD ユーザーと Rightscale の関連ユーザーの間で、リンク関係が確立されている必要があります。

Rightscale で、Azure AD の **[ユーザー名]** の値を **[Username]** の値として割り当ててリンク関係を確立します。

Rightscale で Azure AD のシングル サインオンを構成してテストするには、次の構成要素を完了する必要があります。

1. **[Azure AD シングル サインオンの構成](#configuring-azure-ad-single-sign-on)** - ユーザーがこの機能を使用できるようにします。
1. **[Azure AD のテスト ユーザーの作成](#creating-an-azure-ad-test-user)** - Britta Simon で Azure AD のシングル サインオンをテストします。
1. **[Rightscale のテスト ユーザーの作成](#creating-a-rightscale-test-user)** - Rightscale で Britta Simon に対応するユーザーを作成し、Azure AD の Britta Simon にリンクさせます。
1. **[Azure AD テスト ユーザーの割り当て](#assigning-the-azure-ad-test-user)** - Britta Simon が Azure AD のシングル サインオンを使用できるようにします。
1. **[シングル サインオンのテスト](#testing-single-sign-on)** - 構成が機能するかどうかを確認します。

### <a name="configuring-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成

このセクションでは、Azure Portal で Azure AD のシングル サインオンを有効にして、Rightscale アプリケーションでシングル サインオンを構成します。

**Rightscale で Azure AD シングル サインオンを構成するには、次の手順に従います。**

1. Azure Portal の **Rightscale** アプリケーション統合ページで、**[シングル サインオン]** をクリックします。

    ![Configure single sign-on][4]

1. **[シングル サインオン]** ダイアログで、**[モード]** として **[SAML ベースのサインオン]** を選択し、シングル サインオンを有効にします。
 
    ![Configure single sign-on](./media/rightscale-tutorial/tutorial_rightscale_samlbase.png)

1. **IDP 開始モード**でアプリケーションを構成する場合は、Azure と既に統合されているため、**[Rightscale のドメインと URL]** セクションで手順を実行する必要はありません。

    ![Configure single sign-on](./media/rightscale-tutorial/tutorial_rightscale_url.png)

1. **SP 開始モード**でアプリケーションを構成する場合は、**[Rightscale のドメインと URL]** セクションで次の手順を実行します。
    
    ![Configure single sign-on](./media/rightscale-tutorial/tutorial_rightscale_url1.png)

    a. **[詳細な URL 設定の表示]** をクリックします。

    b. **[サインオン URL]** テキストボックスに、URL として「`https://login.rightscale.com/`」と入力します。

1. **[SAML 署名証明書]** セクションで、**[Certificate (Base64) (証明書 (Base64)) ]** をクリックし、コンピューターに証明書ファイルを保存します。

    ![Configure single sign-on](./media/rightscale-tutorial/tutorial_rightscale_certificate.png) 

1. **[保存]** ボタンをクリックします。

    ![Configure single sign-on](./media/rightscale-tutorial/tutorial_general_400.png)

1. **[Rightscale 構成]** セクションで、**[Rightscale の構成]** をクリックして、**[サインオンの構成]** ウィンドウを開きます。 **[クイック リファレンス]** セクションから **SAML エンティティ ID と SAML シングル サインオン サービス URL** をコピーします。

    ![Configure single sign-on](./media/rightscale-tutorial/tutorial_rightscale_configure.png) 

1. アプリケーションに合わせて SSO を構成するには、管理者として RightScale テナントにサインオンする必要があります。

    a. 上部にあるメニューの **[Settings]** タブをクリックし、**[Single Sign-On]** を選択します。
   
    ![Configure single sign-on](./media/rightscale-tutorial/tutorial_rightscale_001.png) 

    b. **[new]** ボタンをクリックして、**SAML ID プロバイダー**を追加します。
   
    ![Configure single sign-on](./media/rightscale-tutorial/tutorial_rightscale_002.png) 
 
    c. **[Display Name]** テキスト ボックスに会社名を入力します。
   
    ![Configure single sign-on](./media/rightscale-tutorial/tutorial_rightscale_003.png)
 
    d. **[Allow RightScale-initiated SSO using a discovery hint]** を選択して、下のテキストボックスに**ドメイン名**を入力します。
   
    ![Configure single sign-on](./media/rightscale-tutorial/tutorial_rightscale_004.png)

    e. RightScale の **[SAML SSO Endpoint]\(SAML SSO エンドポイント\)** ボックスに、Azure Portal からコピーした **SAML シングル サインオン サービス URL** の値を貼り付けます。
   
    ![Configure single sign-on](./media/rightscale-tutorial/tutorial_rightscale_006.png)

    f. RightScale の **[SAML EntityID]** ボックスに、Azure Portal からコピーした **SAML エンティティ ID** の値を貼り付けます。
   
    ![Configure single sign-on](./media/rightscale-tutorial/tutorial_rightscale_008.png)

    g. **[Browser]\(ブラウザー\)** ボタンをクリックして、Azure Portal からダウンロードした証明書をアップロードします。
   
    ![Configure single sign-on](./media/rightscale-tutorial/tutorial_rightscale_009.png)

    h. **[Save]** をクリックします。

   > [!TIP]
   > アプリのセットアップ中、[Azure Portal](https://portal.azure.com) 内で上記の手順の簡易版を確認できるようになりました。  **[Active Directory] の [エンタープライズ アプリケーション]** セクションからこのアプリを追加した後、**[シングル サインオン]** タブをクリックし、一番下の **[構成]** セクションから組み込みドキュメントにアクセスするだけです。 埋め込みドキュメント機能の詳細については、[Azure AD の埋め込みドキュメント]( https://go.microsoft.com/fwlink/?linkid=845985)に関するページを参照してください。
   > 

### <a name="creating-an-azure-ad-test-user"></a>Azure AD のテスト ユーザーの作成
このセクションの目的は、Azure Portal で Britta Simon というテスト ユーザーを作成することです。

![Azure AD ユーザーの作成][100]

**Azure AD でテスト ユーザーを作成するには、次の手順に従います。**

1. **Azure Portal** の左側のナビゲーション ウィンドウで、**[Azure Active Directory]** アイコンをクリックします。

    ![Azure AD のテスト ユーザーの作成](./media/rightscale-tutorial/create_aaduser_01.png) 

1. **[ユーザーとグループ]** に移動し、**[すべてのユーザー]** をクリックして、ユーザーの一覧を表示します。
    
    ![Azure AD のテスト ユーザーの作成](./media/rightscale-tutorial/create_aaduser_02.png) 

1. ダイアログの上部にある **[追加]** をクリックして、**[ユーザー]** ダイアログを開きます。
 
    ![Azure AD のテスト ユーザーの作成](./media/rightscale-tutorial/create_aaduser_03.png) 

1. **[ユーザー]** ダイアログ ページで、次の手順を実行します。
 
    ![Azure AD のテスト ユーザーの作成](./media/rightscale-tutorial/create_aaduser_04.png) 

    a. **[名前]** ボックスに「**BrittaSimon**」と入力します。

    b. **[ユーザー名]** ボックスに BrittaSimon の**電子メール アドレス**を入力します。

    c. **[パスワードを表示]** を選択し、**[パスワード]** の値をメモします。

    d. **Create** をクリックしてください。
 
### <a name="creating-a-rightscale-test-user"></a>Rightscale のテスト ユーザーの作成

このセクションでは、RightScale で Britta Simon というユーザーを作成します。 [Rightscale クライアント サポート チーム](mailto:support@rightscale.com)と連携し、RightScale プラットフォームにユーザーを追加してください。

### <a name="assigning-the-azure-ad-test-user"></a>Azure AD テスト ユーザーの割り当て

このセクションでは、Britta Simon に Rightscale へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

![ユーザーの割り当て][200] 

**Rightscale に Britta Simon を割り当てるには、次の手順に従います。**

1. Azure Portal でアプリケーション ビューを開き、ディレクトリ ビューに移動します。次に、**[エンタープライズ アプリケーション]** に移動し、**[すべてのアプリケーション]** をクリックします。

    ![ユーザーの割り当て][201] 

1. アプリケーションの一覧で **[Rightscale]** を選択します。

    ![Configure single sign-on](./media/rightscale-tutorial/tutorial_rightscale_app.png) 

1. 左側のメニューで **[ユーザーとグループ]** をクリックします。

    ![ユーザーの割り当て][202] 

1. **[追加]** ボタンをクリックします。 次に、**[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。

    ![ユーザーの割り当て][203]

1. **[ユーザーとグループ]** ダイアログで、ユーザーの一覧から **[Britta Simon]** を選択します。

1. **[ユーザーとグループ]** ダイアログで **[選択]** をクリックします。

1. **[割り当ての追加]** ダイアログで **[割り当て]** ボタンをクリックします。
    
### <a name="testing-single-sign-on"></a>シングル サインオンのテスト

このセクションの目的は、アクセス パネルを使用して Azure AD の SSO 構成をテストすることです。  

アクセス パネルで [RightScale] タイルをクリックすると、自動的に RightScale アプリケーションにサインオンします。

## <a name="additional-resources"></a>その他のリソース

* [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](tutorial-list.md)
* [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/rightscale-tutorial/tutorial_general_01.png
[2]: ./media/rightscale-tutorial/tutorial_general_02.png
[3]: ./media/rightscale-tutorial/tutorial_general_03.png
[4]: ./media/rightscale-tutorial/tutorial_general_04.png

[100]: ./media/rightscale-tutorial/tutorial_general_100.png

[200]: ./media/rightscale-tutorial/tutorial_general_200.png
[201]: ./media/rightscale-tutorial/tutorial_general_201.png
[202]: ./media/rightscale-tutorial/tutorial_general_202.png
[203]: ./media/rightscale-tutorial/tutorial_general_203.png

