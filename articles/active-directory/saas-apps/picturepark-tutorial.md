---
title: チュートリアル:Azure Active Directory と Picturepark の統合 | Microsoft Docs
description: Azure Active Directory と Picturepark の間でシングル サインオンを構成する方法について説明します。
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.assetid: 31c21cd4-9c00-4cad-9538-a13996dc872f
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/06/2017
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7b458632ddb6e23ab1ddf642752aa0cbfc11f1ce
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2019
ms.locfileid: "57839070"
---
# <a name="tutorial-azure-active-directory-integration-with-picturepark"></a>チュートリアル:Azure Active Directory と Picturepark の統合

このチュートリアルでは、Picturepark と Azure Active Directory (Azure AD) を統合する方法について説明します。

Picturepark と Azure AD の統合には、次の利点があります。

- Picturepark にアクセスする Azure AD ユーザーを制御できます
- ユーザーが自分の Azure AD アカウントで自動的に Picturepark にサインオン (シングル サインオン) できるようにします
- 1 つの中央サイト (Azure Portal) でアカウントを管理できます

SaaS アプリと Azure AD の統合の詳細については、「[Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)」をご覧ください。

## <a name="prerequisites"></a>前提条件

Picturepark と Azure AD の統合を構成するには、次のものが必要です。

- Azure AD サブスクリプション
- Picturepark でのシングル サインオンが有効なサブスクリプション

> [!NOTE]
> このチュートリアルの手順をテストする場合、運用環境を使用しないことをお勧めします。

このチュートリアルの手順をテストするには、次の推奨事項に従ってください。

- 必要な場合を除き、運用環境は使用しないでください。
- Azure AD の評価環境がない場合は、 [こちら](https://azure.microsoft.com/pricing/free-trial/)から 1 か月の評価版を入手できます。

## <a name="scenario-description"></a>シナリオの説明
このチュートリアルでは、テスト環境で Azure AD のシングル サインオンをテストします。 このチュートリアルで説明するシナリオは、主に次の 2 つの要素で構成されています。

1. ギャラリーからの Picturepark の追加
1. Azure AD シングル サインオンの構成とテスト

## <a name="adding-picturepark-from-the-gallery"></a>ギャラリーからの Picturepark の追加
Azure AD への Picturepark の統合を構成するには、ギャラリーから管理対象 SaaS アプリの一覧に Picturepark を追加する必要があります。

**ギャラリーから Picturepark を追加するには、次の手順に従います。**

1. **[Azure Portal](https://portal.azure.com)** の左側のナビゲーション ウィンドウで、**[Azure Active Directory]** アイコンをクリックします。 

    ![Active Directory][1]

1. **[エンタープライズ アプリケーション]** に移動します。 次に、**[すべてのアプリケーション]** に移動します。

    ![[アプリケーション]][2]
    
1. 新しいアプリケーションを追加するには、ダイアログの上部にある **[新しいアプリケーション]** をクリックします。

    ![[アプリケーション]][3]

1. 検索ボックスに「**Picturepark**」と入力します。

    ![Azure AD のテスト ユーザーの作成](./media/picturepark-tutorial/tutorial_picturepark_search.png)

1. 結果ウィンドウで **Picturepark** を選択し、**[追加]** をクリックして、アプリケーションを追加します。

    ![Azure AD のテスト ユーザーの作成](./media/picturepark-tutorial/tutorial_picturepark_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成とテスト
このセクションでは、"Britta Simon" というテスト ユーザーに基づいて、Picturepark で Azure AD のシングル サインオンを構成し、テストします。

シングル サインオンを機能させるには、Azure AD ユーザーに対応する Picturepark ユーザーが Azure AD で認識されている必要があります。 言い換えると、Azure AD ユーザーと Picturepark の関連ユーザーの間で、リンク関係が確立されている必要があります。

Picturepark で、Azure AD の **[ユーザー名]** の値を **[Username]\(ユーザー名\)** の値として割り当ててリンク関係を確立します。

Picturepark で Azure AD のシングル サインオンを構成してテストするには、次の構成要素を完了する必要があります。

1. **[Azure AD シングル サインオンの構成](#configuring-azure-ad-single-sign-on)** - ユーザーがこの機能を使用できるようにします。
1. **[Azure AD のテスト ユーザーの作成](#creating-an-azure-ad-test-user)** - Britta Simon で Azure AD のシングル サインオンをテストします。
1. **[Picturepark テスト ユーザーの作成](#creating-a-picturepark-test-user)** - Picturepark で Britta Simon に対応するユーザーを作成し、Azure AD の Britta Simon にリンクさせます。
1. **[Azure AD テスト ユーザーの割り当て](#assigning-the-azure-ad-test-user)** - Britta Simon が Azure AD のシングル サインオンを使用できるようにします。
1. **[シングル サインオンのテスト](#testing-single-sign-on)** - 構成が機能するかどうかを確認します。

### <a name="configuring-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成

このセクションでは、Azure Portal で Azure AD のシングル サインオンを有効にして、Picturepark アプリケーションでシングル サインオンを構成します。

**Picturepark で Azure AD シングル サインオンを構成するには、次の手順に従います。**

1. Azure Portal の **Picturepark** アプリケーション統合ページで、**[シングル サインオン]** をクリックします。

    ![Configure single sign-on][4]

1. **[シングル サインオン]** ダイアログで、**[モード]** として **[SAML ベースのサインオン]** を選択し、シングル サインオンを有効にします。
 
    ![Configure single sign-on](./media/picturepark-tutorial/tutorial_picturepark_samlbase.png)

1. **[Picturepark のドメインと URL]** セクションで、次の手順を実行します。

    ![Configure single sign-on](./media/picturepark-tutorial/tutorial_picturepark_url.png)

    a. **[サインオン URL]** ボックスに、`https://<companyname>.picturepark.com` のパターンを使用して URL を入力します。

    b. **[識別子]** ボックスに、次の形式で URL を入力します。 
    
    |  |
    |--|
    | `https://<companyname>.current-picturepark.com`|
    | `https://<companyname>.picturepark.com`|
    | `https://<companyname>.next-picturepark.com`|
    | |

    > [!NOTE] 
    > これらは実際の値ではありません。 実際のサインオン URL と識別子でこれらの値を更新してください。 これらの値を取得するには、[Picturepark クライアント サポート チーム](https://picturepark.com/about/contact/)に問い合わせてください。 
 
1. **[SAML 署名証明書]** セクションで、証明書の **[拇印]** の値をコピーします。

    ![Configure single sign-on](./media/picturepark-tutorial/tutorial_picturepark_certificate.png) 

1. **[保存]** ボタンをクリックします。

    ![Configure single sign-on](./media/picturepark-tutorial/tutorial_general_400.png)

1. **[Picturepark 構成]** セクションで、**[Picturepark の構成]** をクリックして、**[サインオンの構成]** ウィンドウを開きます。 **[クイック リファレンス]** セクションから **SAML シングル サインオン サービスの URL** をコピーします。

    ![Configure single sign-on](./media/picturepark-tutorial/tutorial_picturepark_configure.png) 

1. 別の Web ブラウザーのウィンドウで、Picturepark 企業サイトに管理者としてログインします。

1. 上部にあるツールバーで **[Administrative tools]**、**[Management Console]** の順にクリックします。
   
    ![管理コンソール](./media/picturepark-tutorial/ic795062.png "管理コンソール")

1. **[Authentication]**、**[Identity providers]** の順にクリックします。
   
    ![Authentication](./media/picturepark-tutorial/ic795063.png "Authentication")

1. **[Identity provider configuration]** セクションで、次の手順に従います。
   
    ![ID プロバイダーの構成](./media/picturepark-tutorial/ic795064.png "ID プロバイダーの構成")
   
    a. **[追加]** をクリックします。
  
    b. 構成の名前を入力します。
   
    c. **[Set as default]** を選びます。
   
    d. **[Issuer URI]\(発行者 URI\)** ボックスに、Azure Portal からコピーした **SAML シングル サインオン サービス URL** の値を貼り付けます。
   
    e. **[Trusted Issuer Thumb Print]\(信頼された発行者の拇印\)** ボックスに、**[SAML 署名証明書]** セクションからコピーした**拇印**の値を貼り付けます。 

1. **[JoinDefaultUsersGroup]** をクリックします。

1. **[Claim]\(要求\)** ボックスで **[Emailaddress]\(電子メール アドレス\)** 属性を設定するには、「`http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress`」と入力して **[Save]\(保存\)** をクリックします。

      ![Configuration](./media/picturepark-tutorial/ic795065.png "Configuration")

> [!TIP]
> アプリのセットアップ中、[Azure Portal](https://portal.azure.com) 内で上記の手順の簡易版を確認できるようになりました。  **[Active Directory] の [エンタープライズ アプリケーション]** セクションからこのアプリを追加した後、**[シングル サインオン]** タブをクリックし、一番下の **[構成]** セクションから組み込みドキュメントにアクセスするだけです。 埋め込みドキュメント機能の詳細については、[Azure AD の埋め込みドキュメント]( https://go.microsoft.com/fwlink/?linkid=845985)に関するページを参照してください。
> 

### <a name="creating-an-azure-ad-test-user"></a>Azure AD のテスト ユーザーの作成
このセクションの目的は、Azure Portal で Britta Simon というテスト ユーザーを作成することです。

![Azure AD ユーザーの作成][100]

**Azure AD でテスト ユーザーを作成するには、次の手順に従います。**

1. **Azure Portal** の左側のナビゲーション ウィンドウで、**[Azure Active Directory]** アイコンをクリックします。

    ![Azure AD のテスト ユーザーの作成](./media/picturepark-tutorial/create_aaduser_01.png) 

1. **[ユーザーとグループ]** に移動し、**[すべてのユーザー]** をクリックして、ユーザーの一覧を表示します。
    
    ![Azure AD のテスト ユーザーの作成](./media/picturepark-tutorial/create_aaduser_02.png) 

1. ダイアログの上部にある **[追加]** をクリックして、**[ユーザー]** ダイアログを開きます。
 
    ![Azure AD のテスト ユーザーの作成](./media/picturepark-tutorial/create_aaduser_03.png) 

1. **[ユーザー]** ダイアログ ページで、次の手順を実行します。
 
    ![Azure AD のテスト ユーザーの作成](./media/picturepark-tutorial/create_aaduser_04.png) 

    a. **[名前]** ボックスに「**BrittaSimon**」と入力します。

    b. **[ユーザー名]** ボックスに BrittaSimon の**電子メール アドレス**を入力します。

    c. **[パスワードを表示]** を選択し、**[パスワード]** の値をメモします。

    d. **Create** をクリックしてください。
 
### <a name="creating-a-picturepark-test-user"></a>Picturepark テスト ユーザーの作成

Azure AD ユーザーが Picturepark にログインできるようにするには、ユーザーを Picturepark にプロビジョニングする必要があります。 Picturepark の場合、プロビジョニングは手動で行います。

**ユーザー アカウントをプロビジョニングするには、次の手順に従います。**

1. **Picturepark** テナントにログインします。

1. 上部にあるツールバーで **[Administrative tools]**、**[Users]** の順にクリックします。
   
    ![ユーザー](./media/picturepark-tutorial/ic795067.png "Users")

1. **[Users overview]** タブで、**[New]** をクリックします。
   
    ![ユーザー管理](./media/picturepark-tutorial/ic795068.png "ユーザー管理")

1. **[Create User]\(ユーザーの作成\)** ダイアログで、プロビジョニングする有効な Azure Active Directory ユーザーを次の手順で設定します。
   
    ![Create User](./media/picturepark-tutorial/ic795069.png "Create User")
   
    a. **[Email Address]\(メール アドレス\)** ボックスに、ユーザーの**メール アドレス** (**BrittaSimon\@contoso.com**) を入力します。  
   
    b. **[Password]\(パスワード\)** および **[Confirm Password]\(確認パスワード\)** ボックスに、BrittaSimon の**パスワード**を入力します。 
   
    c. **[First Name]\(名\)** ボックスに、ユーザーの**名**を「**Britta**」と入力します。 
   
    d. **[Last Name]\(姓\)** ボックスに、ユーザーの**姓**を「**Simon**」と入力します。
   
    e. **[Company]\(会社\)** ボックスに、ユーザーの**会社名**を入力します。 
   
    f. **[Country]\(国\)** ボックスに、ユーザーの**国**を入力します。
  
    g. **[ZIP]\(郵便番号\)** ボックスに、ユーザーの**郵便番号**を入力します。
   
    h. **[City]\(市区町村\)** ボックスに、ユーザーの**市区町村名**を入力します。

    i. **[Language]** を選択します。
   
    j. **Create** をクリックしてください。

>[!NOTE]
>Picturepark から提供されている他の Picturepark ユーザー アカウント作成ツールまたは API を使用して、Azure AD ユーザー アカウントをプロビジョニングできます。
> 

### <a name="assigning-the-azure-ad-test-user"></a>Azure AD テスト ユーザーの割り当て

このセクションでは、Britta Simon に Picturepark へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

![ユーザーの割り当て][200] 

**Picturepark に Britta Simon を割り当てるには、次の手順を実行します。**

1. Azure Portal でアプリケーション ビューを開き、ディレクトリ ビューに移動します。次に、**[エンタープライズ アプリケーション]** に移動し、**[すべてのアプリケーション]** をクリックします。

    ![ユーザーの割り当て][201] 

1. アプリケーションの一覧で **[Picturepark]** を選択します。

    ![Configure single sign-on](./media/picturepark-tutorial/tutorial_picturepark_app.png) 

1. 左側のメニューで **[ユーザーとグループ]** をクリックします。

    ![ユーザーの割り当て][202] 

1. **[追加]** ボタンをクリックします。 次に、**[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。

    ![ユーザーの割り当て][203]

1. **[ユーザーとグループ]** ダイアログで、ユーザーの一覧から **[Britta Simon]** を選択します。

1. **[ユーザーとグループ]** ダイアログで **[選択]** をクリックします。

1. **[割り当ての追加]** ダイアログで **[割り当て]** ボタンをクリックします。
    
### <a name="testing-single-sign-on"></a>シングル サインオンのテスト

このセクションでは、アクセス パネルを使用して Azure AD のシングル サインオン構成をテストします。

アクセス パネルで [Picturepark] タイルをクリックすると、自動的に Picturepark アプリケーションにサインオンします。 アクセス パネルの詳細については、[アクセス パネルの概要](../user-help/active-directory-saas-access-panel-introduction.md)に関する記事を参照してください。

## <a name="additional-resources"></a>その他のリソース

* [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](tutorial-list.md)
* [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/picturepark-tutorial/tutorial_general_01.png
[2]: ./media/picturepark-tutorial/tutorial_general_02.png
[3]: ./media/picturepark-tutorial/tutorial_general_03.png
[4]: ./media/picturepark-tutorial/tutorial_general_04.png

[100]: ./media/picturepark-tutorial/tutorial_general_100.png

[200]: ./media/picturepark-tutorial/tutorial_general_200.png
[201]: ./media/picturepark-tutorial/tutorial_general_201.png
[202]: ./media/picturepark-tutorial/tutorial_general_202.png
[203]: ./media/picturepark-tutorial/tutorial_general_203.png

