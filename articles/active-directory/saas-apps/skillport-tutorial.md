---
title: チュートリアル:Azure Active Directory と Skillport の統合 | Microsoft Docs
description: Azure Active Directory と Skillport の間でシングル サインオンを構成する方法について説明します。
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.assetid: 4df349b2-a73f-4b88-a077-ec0fbfc26527
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/24/2017
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 936ad2e49aaf449144296dd941a3fbc42719fe4f
ms.sourcegitcommit: a60a55278f645f5d6cda95bcf9895441ade04629
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/03/2019
ms.locfileid: "58879031"
---
# <a name="tutorial-azure-active-directory-integration-with-skillport"></a>チュートリアル:Azure Active Directory と Skillport の統合

このチュートリアルでは、Skillport と Azure Active Directory (Azure AD) を統合する方法について説明します。

Skillport と Azure AD の統合には、次の利点があります。

- Skillport にアクセスする Azure AD ユーザーを制御できます。
- ユーザーが自分の Azure AD アカウントで自動的に Skillport にサインオン (シングル サインオン) できるように、設定が可能です。
- 1 つの中央サイト (Azure Portal) でアカウントを管理できます

SaaS アプリと Azure AD の統合の詳細については、「[Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)」をご覧ください。

## <a name="prerequisites"></a>前提条件

Azure AD と Skillport の統合を構成するには、次のものが必要です。

- Azure AD サブスクリプション
- Skillport でのシングル サインオンが有効なサブスクリプション

> [!NOTE]
> このチュートリアルの手順をテストする場合、運用環境を使用しないことをお勧めします。

このチュートリアルの手順をテストするには、次の推奨事項に従ってください。

- 必要な場合を除き、運用環境は使用しないでください。
- Azure AD の評価環境がない場合は、 [こちら](https://azure.microsoft.com/pricing/free-trial/)から 1 か月の評価版を入手できます。

## <a name="scenario-description"></a>シナリオの説明
このチュートリアルでは、テスト環境で Azure AD のシングル サインオンをテストします。 このチュートリアルで説明するシナリオは、主に次の 2 つの要素で構成されています。

1. ギャラリーからの Skillport の追加
1. Azure AD シングル サインオンの構成とテスト

## <a name="adding-skillport-from-the-gallery"></a>ギャラリーからの Skillport の追加
Azure AD への Skillport の統合を構成するには、ギャラリーから管理対象 SaaS アプリの一覧に Skillport を追加する必要があります。

**ギャラリーから Skillport を追加するには、次の手順に従います。**

1. **[Azure Portal](https://portal.azure.com)** の左側のナビゲーション ウィンドウで、**[Azure Active Directory]** アイコンをクリックします。 

    ![Active Directory][1]

1. **[エンタープライズ アプリケーション]** に移動します。 次に、**[すべてのアプリケーション]** に移動します。

    ![[アプリケーション]][2]
    
1. ダイアログの上部にある **[新しいアプリケーション]** ボタンをクリックします。

    ![[アプリケーション]][3]

1. 検索ボックスに、「**Skillport**」と入力します。

    ![Azure AD のテスト ユーザーの作成](./media/skillport-tutorial/tutorial_skillport_search.png)

1. 結果ウィンドウで **[Skillport]** を選択し、**[追加]** をクリックして、アプリケーションを追加します。

    ![Azure AD のテスト ユーザーの作成](./media/skillport-tutorial/tutorial_skillport_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成とテスト
このセクションでは、"Britta Simon" というテスト ユーザーに基づいて、Skillport で Azure AD のシングル サインオンを構成し、テストします。

シングル サインオンを機能させるには、Azure AD ユーザーに対応する Skillport ユーザーが Azure AD で認識されている必要があります。 言い換えると、Azure AD ユーザーと Skillport の関連ユーザーの間で、リンク関係が確立されている必要があります。

このリンク関係を確立するには、Azure AD の **[ユーザー名]** の値を Skillport の **[Username]\(ユーザー名\)** の値として割り当てます。

Skillport で Azure AD のシングル サインオンを構成してテストするには、次の構成要素を完了する必要があります。

1. **[Azure AD シングル サインオンの構成](#configuring-azure-ad-single-sign-on)** - ユーザーがこの機能を使用できるようにします。
1. **[Azure AD のテスト ユーザーの作成](#creating-an-azure-ad-test-user)** - Britta Simon で Azure AD のシングル サインオンをテストします。
1. **[Skillport テスト ユーザーの作成](#creating-a-skillport-test-user)** - Skillport で Britta Simon に対応するユーザーを作成し、Azure AD の Britta Simon にリンクさせます。
1. **[Azure AD テスト ユーザーの割り当て](#assigning-the-azure-ad-test-user)** - Britta Simon が Azure AD のシングル サインオンを使用できるようにします。
1. **[シングル サインオンのテスト](#testing-single-sign-on)** - 構成が機能するかどうかを確認します。

### <a name="configuring-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成

このセクションでは、Azure Portal で Azure AD のシングル サインオンを有効にして、Skillport アプリケーションでシングル サインオンを構成します。

**Skillport で Azure AD シングル サインオンを構成するには、次の手順に従います。**

1. Azure Portal の **Skillport** アプリケーション統合ページで、**[シングル サインオン]** をクリックします。

    ![Configure single sign-on][4]

1. **[シングル サインオン]** ダイアログで、**[モード]** として **[SAML ベースのサインオン]** を選択し、シングル サインオンを有効にします。
 
    ![Configure single sign-on](./media/skillport-tutorial/tutorial_skillport_samlbase.png)

1. **[Skillport のドメインと URL]** セクションで、次の手順を実行します。

    ![Configure single sign-on](./media/skillport-tutorial/tutorial_skillport_url.png)

    a. **[サインオン URL]** ボックスに、次の URL を入力します。
      
      EU のデータセンター:  `https://adfs.skillport.eu`
   
      米国のデータセンター:  `https://sso.skillport.com`

    b. **[識別子]** ボックスに次の URL を入力します。
      
      EU のデータセンター:  `http://adfs.skillport.eu/adfs/services/trust`
   
      米国のデータセンター:  `https://sso.skillport.com`
   
    c. **[応答 URL]** ボックスに次のURL を入力します。
    
      EU のデータセンター:  `https://adfs.skillport.eu/adfs/ls/`
    
      米国のデータセンター:  `https://sso.skillport.com/sp/ACS.saml2`
 
1. **[SAML 署名証明書]** セクションで、**[メタデータ XML]** をクリックし、コンピューターに XML ファイルを保存します。

    ![Configure single sign-on](./media/skillport-tutorial/tutorial_skillport_certificate.png) 

1. **[保存]** ボタンをクリックします。

    ![Configure single sign-on](./media/skillport-tutorial/tutorial_general_400.png)

1. **Skillport** 側にシングル サインオンを構成するには、ダウンロードした**メタデータ XML** を [Skillport サポート チーム](https://www.skillsoft.com/contact.asp)に送信する必要があります。 サポート チームはこれをセットアップして、SAML SSO 接続が両方の側で正しく設定されるようにします。

### <a name="creating-an-azure-ad-test-user"></a>Azure AD のテスト ユーザーの作成
このセクションの目的は、Azure Portal で Britta Simon というテスト ユーザーを作成することです。

![Azure AD ユーザーの作成][100]

**Azure AD でテスト ユーザーを作成するには、次の手順に従います。**

1. **Azure Portal** の左側のナビゲーション ウィンドウで、**[Azure Active Directory]** アイコンをクリックします。

    ![Azure AD のテスト ユーザーの作成](./media/skillport-tutorial/create_aaduser_01.png) 

1. **[ユーザーとグループ]** に移動し、**[すべてのユーザー]** をクリックして、ユーザーの一覧を表示します。
    
    ![Azure AD のテスト ユーザーの作成](./media/skillport-tutorial/create_aaduser_02.png) 

1. ダイアログの上部にある **[追加]** をクリックして **[ユーザー]** ダイアログを開きます。
 
    ![Azure AD のテスト ユーザーの作成](./media/skillport-tutorial/create_aaduser_03.png) 

1. **[ユーザー]** ダイアログ ページで、次の手順を実行します。
 
    ![Azure AD のテスト ユーザーの作成](./media/skillport-tutorial/create_aaduser_04.png) 

    a. **[名前]** ボックスに「**BrittaSimon**」と入力します。

    b. **[ユーザー名]** ボックスに BrittaSimon の**電子メール アドレス**を入力します。

    c. **[パスワードを表示]** を選択し、**[パスワード]** の値をメモします。

    d. **Create** をクリックしてください。
 
### <a name="creating-a-skillport-test-user"></a>Skillport テスト ユーザーの作成

エンド ユーザーの要件に応じた複数のビジネス シナリオがあるため、Skillport テスト ユーザーを作成するには、[Skillport サポート チーム](https://www.skillsoft.com/contact.asp)に連絡する必要があります。 ユーザーと話し合った後、Skillport サポート チームが構成します。  

### <a name="assigning-the-azure-ad-test-user"></a>Azure AD テスト ユーザーの割り当て

このセクションでは、Britta Simon に Skillport へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

![ユーザーの割り当て][200] 

**Skillport に Britta Simon を割り当てるには、次の手順に従います。**

1. Azure Portal でアプリケーション ビューを開き、ディレクトリ ビューに移動します。次に、**[エンタープライズ アプリケーション]** に移動し、**[すべてのアプリケーション]** をクリックします。

    ![ユーザーの割り当て][201] 

1. アプリケーションの一覧で **[Skillport]** を選択します。

    ![Configure single sign-on](./media/skillport-tutorial/tutorial_skillport_app.png) 

1. 左側のメニューで **[ユーザーとグループ]** をクリックします。

    ![ユーザーの割り当て][202] 

1. **[追加]** ボタンをクリックします。 次に、**[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。

    ![ユーザーの割り当て][203]

1. **[ユーザーとグループ]** ダイアログで、ユーザーの一覧から **[Britta Simon]** を選択します。

1. **[ユーザーとグループ]** ダイアログで **[選択]** をクリックします。

1. **[割り当ての追加]** ダイアログで **[割り当て]** ボタンをクリックします。
    
### <a name="testing-single-sign-on"></a>シングル サインオンのテスト

このセクションでは、アクセス パネルを使用して Azure AD のシングル サインオン構成をテストします。

アクセス パネルで [Skillport] タイルをクリックすると、自動的に Skillport アプリケーションにサインオンします。
アクセス パネルの詳細については、 [アクセス パネルの概要](../user-help/active-directory-saas-access-panel-introduction.md)に関する記事をご覧ください。 

## <a name="additional-resources"></a>その他のリソース

* [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](tutorial-list.md)
* [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/skillport-tutorial/tutorial_general_01.png
[2]: ./media/skillport-tutorial/tutorial_general_02.png
[3]: ./media/skillport-tutorial/tutorial_general_03.png
[4]: ./media/skillport-tutorial/tutorial_general_04.png

[100]: ./media/skillport-tutorial/tutorial_general_100.png

[200]: ./media/skillport-tutorial/tutorial_general_200.png
[201]: ./media/skillport-tutorial/tutorial_general_201.png
[202]: ./media/skillport-tutorial/tutorial_general_202.png
[203]: ./media/skillport-tutorial/tutorial_general_203.png

