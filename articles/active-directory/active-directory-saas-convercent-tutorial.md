---
title: "チュートリアル: Azure Active Directory と Convercent の統合 | Microsoft Docs"
description: "Azure Active Directory と Convercent の間でシングル サインオンを構成する方法について説明します。"
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.assetid: f9c9d290-0e13-490b-b559-0be772d6a690
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 3/10/2017
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 07635b0eb4650f0c30898ea1600697dacb33477c
ms.openlocfilehash: 187fe8af432d2521e3b9efa59b788280c32692a9
ms.lasthandoff: 03/28/2017


---
# <a name="tutorial-azure-active-directory-integration-with-convercent"></a>チュートリアル: Azure Active Directory と Convercent の統合

このチュートリアルでは、Convercent と Azure Active Directory (Azure AD) を統合する方法について説明します。

Convercent と Azure AD の統合には、次の利点があります。

- Convercent にアクセスする Azure AD ユーザーを制御できます。
- ユーザーが自分の Azure AD アカウントで自動的に Convercent にシングル サインオン (SSO) できるようにします。
- 1 つの中央サイト (Azure の新しいポータル) でアカウントを管理できます。

SaaS アプリと Azure AD の統合の詳細については、「 [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](active-directory-appssoaccess-whatis.md)」を参照してください。

## <a name="prerequisites"></a>前提条件

Convercent と Azure AD の統合を構成するには、次のものが必要です。

- Azure AD サブスクリプション
- Convercent での SSO が有効なサブスクリプション

>[!NOTE]
>このチュートリアルの手順をテストする場合、運用環境を使用しないことをお勧めします。
>

このチュートリアルの手順をテストするには、次の推奨事項に従ってください。

- 必要な場合を除き、運用環境は使用しないでください。
- Azure AD の評価環境がない場合は、[1 か月の試用版](https://azure.microsoft.com/pricing/free-trial/)を入手できます。


## <a name="scenario-description"></a>シナリオの説明
このチュートリアルでは、テスト環境で Azure AD SSO をテストします。 

このチュートリアルで説明するシナリオは、主に次の 2 つの要素で構成されています。

1. ギャラリーからの Convercent の追加
2. Azure AD SSO の構成とテスト

## <a name="add-convercent-from-the-gallery"></a>ギャラリーからの Convercent の追加
Azure AD への Convercent の統合を構成するには、ギャラリーから管理対象 SaaS アプリの一覧に Convercent を追加する必要があります。

**ギャラリーから Convercent を追加するには、次の手順に従います。**

1. **Azure 管理ポータル**の左側のナビゲーション ウィンドウで、**[Azure Active Directory]** アイコンをクリックします。 

    ![Active Directory][1]

2. **[エンタープライズ アプリケーション]** に移動します。 次に、**[すべてのアプリケーション]** に移動します。

    ![アプリケーション][2]
    
3. ダイアログの上部にある **[追加]** をクリックします。

    ![アプリケーション][3]

4. 検索ボックスに、「**Convercent**」と入力します。

    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-convercent-tutorial/tutorial_convercent_001.png)

5. 結果ウィンドウで **[Convercent]** を選択し、**[追加]** をクリックして、アプリケーションを追加します。

    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-convercent-tutorial/tutorial_convercent_0001.png)

##  <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成とテスト
このセクションでは、"Britta Simon" というテスト ユーザーに基づいて、Convercent で Azure AD の SSO を構成し、テストします。

SSO を機能させるには、Azure AD ユーザーに対応する Convercent ユーザーが Azure AD で認識されている必要があります。 言い換えると、Azure AD ユーザーと Convercent の関連ユーザーの間で、リンク関係が確立されている必要があります。

このリンク関係を確立するには、Azure AD の **[ユーザー名]** の値を Convercent の **[Username (ユーザー名)]** の値として割り当てます。

Convercent で Azure AD の SSO を構成してテストするには、次の構成要素を完了する必要があります。

1. **[Azure AD シングル サインオンの構成](#configuring-azure-ad-single-sign-on)** - ユーザーがこの機能を使用できるようにします。
2. **[Azure AD のテスト ユーザーの作成](#creating-an-azure-ad-test-user)** - Britta Simon で Azure AD のシングル サインオンをテストします。
3. **[Convercent テスト ユーザーの作成](#creating-a-works-mobile-test-user)** - Convercent で Britta Simon に対応するユーザーを作成し、Azure AD の Britta Simon にリンクさせます。
4. **[Azure AD テスト ユーザーの割り当て](#assigning-the-azure-ad-test-user)** - Britta Simon が Azure AD のシングル サインオンを使用できるようにします。
5. **[シングル サインオンのテスト](#testing-single-sign-on)** - 構成が機能するかどうかを確認します。

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成

このセクションでは、Azure の新しいポータルで Azure AD の SSO を有効にして、Convercent アプリケーションでシングル サインオンを構成します。

**Convercent で Azure AD シングル サインオンを構成するには、次の手順に従います。**

1. Azure の新しいポータルの **Convercent** アプリケーション統合ページで、**[シングル サインオン]** をクリックします。

    ![[シングル サインオンの構成]][4]

2. **[シングル サインオン]** ダイアログ ページで、**[モード]** として **[SAML ベースのサインオン]** を選択し、シングル サインオンを有効にします。
 
    ![[シングル サインオンの構成]](./media/active-directory-saas-convercent-tutorial/tutorial_convercent_01.png)

3. **[Convercent のドメインと URL]** セクションで、**IDP 開始モード**でアプリケーションを構成する場合は、次の手順を実行します。

    ![Configure Single Sign-On](./media/active-directory-saas-convercent-tutorial/tutorial_convercent_02.png)
  1. **[識別子]** ボックスに、「`https://sts.convercent.com/`」と入力します。
  2. **[詳細な URL 設定の表示]** をクリックします。
  3. **[リレー状態]** ボックスに、「`https://app.convercent.com/`」と入力します。
    
4. **SP 開始モード**でアプリケーションを構成する場合は、**[Convercent のドメインと URL]** セクションで次の手順を実行します。
    
    ![[シングル サインオンの構成]](./media/active-directory-saas-convercent-tutorial/tutorial_convercent_03.png)
  * **[サインオン URL]** ボックスに、「`https://app.convercent.com/`」と入力します。
    >[!NOTE] 
    >ここでは、指定された一意の識別子を使用することをお勧めします。 この値を取得するには、[Convercent サポート チーム](mailTo:support@convercent.com)に問い合わせてください。
    >

5. **[Convercent Configuration (Convercent 構成)]** セクションで、**[Configure Convercent (Convercent を構成)]** をクリックして、**[サインオンの構成]** ウィンドウを開きます。 **[SAML XML Metadata (SAML XML メタデータ)]**をクリックし、メタデータ ファイルをコンピューターに保存します。

    ![[シングル サインオンの構成]](./media/active-directory-saas-convercent-tutorial/tutorial_convercent_04.png) 

    ![Configure Single Sign-On](./media/active-directory-saas-convercent-tutorial/tutorial_convercent_05.png)

6. アプリケーション用に構成された SSO を入手するには、[Convercent サポート チーム](mailTo:support@convercent.com)に連絡して、ダウンロードした**メタデータ**を提供してください。

7. Azure の新しいポータルで、**[保存]** ボタンをクリックします。  
  
### <a name="create-an-azure-ad-test-user"></a>Azure AD のテスト ユーザーの作成
このセクションの目的は、新しいポータルで Britta Simon というテスト ユーザーを作成することです。

![Azure AD ユーザーの作成][100]

**Azure AD でテスト ユーザーを作成するには、次の手順に従います。**

1. **Microsoft Azure 管理ポータル**の左側のナビゲーション ウィンドウで、**[Azure Active Directory]** アイコンをクリックします。

    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-convercent-tutorial/create_aaduser_01.png) 

2. **[ユーザーとグループ]** に移動し、**[すべてのユーザー]** をクリックして、ユーザーの一覧を表示します。
    
    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-convercent-tutorial/create_aaduser_02.png) 

3. ダイアログの上部にある **[追加]** をクリックして **[ユーザー]** ダイアログを開きます。
 
    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-convercent-tutorial/create_aaduser_03.png) 

4. **[ユーザー]** ダイアログ ページで、次の手順を実行します。
 
    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-convercent-tutorial/create_aaduser_04.png) 
 1. **[名前]** ボックスに「**BrittaSimon**」と入力します。
 2. **[ユーザー名]** ボックスに BrittaSimon の**電子メール アドレス**を入力します。
 3. **[パスワードを表示]** を選択し、**[パスワード]** の値をメモします。
 4. **[作成]**をクリックします。 

### <a name="create-a-convercent-test-user"></a>Convercent テスト ユーザーの作成

このセクションでは、Convercent で Britta Simon というユーザーを作成します。 Convercent [サポート チーム](emailto:support@convercent.com)と連携し、Convercent プラットフォームにユーザーを追加してください。

### <a name="assign-the-azure-ad-test-user"></a>Azure AD テスト ユーザーの割り当て

このセクションでは、Britta Simon に Convercent へのアクセスを許可することで、このユーザーが Azure SSO を使用できるようにします。

![ユーザーの割り当て][200] 

**Convercent に Britta Simon を割り当てるには、次の手順に従います。**

1. Azure 管理ポータルでアプリケーション ビューを開き、ディレクトリ ビューに移動します。次に、**[エンタープライズ アプリケーション]** に移動し、**[すべてのアプリケーション]** をクリックします。

    ![ユーザーの割り当て][201] 

2. アプリケーションの一覧で **[Convercent]** を選択します。

    ![[シングル サインオンの構成]](./media/active-directory-saas-convercent-tutorial/tutorial_convercent_50.png) 

3. 左側のメニューで **[ユーザーとグループ]** をクリックします。

    ![ユーザーの割り当て][202] 

4. **[追加]** ボタンをクリックします。 次に、**[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。

    ![ユーザーの割り当て][203]

5. **[ユーザーとグループ]** ダイアログで、ユーザーの一覧から **[Britta Simon]** を選択します。

6. **[ユーザーとグループ]** ダイアログで **[選択]** をクリックします。

7. **[割り当ての追加]** ダイアログで **[割り当て]** ボタンをクリックします。
    
### <a name="test-single-sign-on"></a>シングル サインオンのテスト

このセクションでは、アクセス パネルを使用して Azure AD の SSO 構成をテストします。

アクセス パネルで [Convercent] タイルをクリックすると、自動的に Convercent アプリケーションにサインオンします。


## <a name="additional-resources"></a>その他のリソース

* [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](active-directory-saas-tutorial-list.md)
* [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-convercent-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-convercent-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-convercent-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-convercent-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-convercent-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-convercent-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-convercent-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-convercent-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-convercent-tutorial/tutorial_general_203.png
