---
title: チュートリアル:Azure Active Directory と xMatters OnDemand の統合 | Microsoft Docs
description: Azure Active Directory と xMatters OnDemand の間でシングル サインオンを構成する方法について説明します。
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.assetid: ca0633db-4f95-432e-b3db-0168193b5ce9
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/16/2018
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8b5ec711f0e43d9d29d962d43ed8b1d86338db87
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2019
ms.locfileid: "57881216"
---
# <a name="tutorial-azure-active-directory-integration-with-xmatters-ondemand"></a>チュートリアル:Azure Active Directory と xMatters OnDemand の統合

このチュートリアルでは、xMatters OnDemand と Azure Active Directory (Azure AD) を統合する方法について説明します。

xMatters OnDemand と Azure AD の統合には、次の利点があります。

- xMatters OnDemand にアクセスするユーザーを Azure AD で管理できます
- ユーザーが自分の Azure AD アカウントで xMatters OnDemand に自動的にサインオン (シングル サインオン) できるように、設定が可能です
- 1 つの中央サイト (Azure Portal) でアカウントを管理できます

SaaS アプリと Azure AD の統合の詳細については、「[Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)」をご覧ください。

## <a name="prerequisites"></a>前提条件

xMatters OnDemand と Azure AD の統合を構成するには、次のものが必要です。

- Azure AD サブスクリプション
- xMatters OnDemand でのシングル サインオンが有効なサブスクリプション

> [!NOTE]
> このチュートリアルの手順をテストする場合、運用環境を使用しないことをお勧めします。

このチュートリアルの手順をテストするには、次の推奨事項に従ってください。

- 必要な場合を除き、運用環境は使用しないでください。
- Azure AD の評価環境がない場合は、 [こちら](https://azure.microsoft.com/pricing/free-trial/)から 1 か月の評価版を入手できます。

## <a name="scenario-description"></a>シナリオの説明
このチュートリアルでは、テスト環境で Azure AD のシングル サインオンをテストします。 このチュートリアルで説明するシナリオは、主に次の 2 つの要素で構成されています。

1. ギャラリーからの xMatters OnDemand の追加
1. Azure AD シングル サインオンの構成とテスト

## <a name="adding-xmatters-ondemand-from-the-gallery"></a>ギャラリーからの xMatters OnDemand の追加
Azure AD への xMatters OnDemand の統合を構成するには、ギャラリーから管理対象 SaaS アプリの一覧に xMatters OnDemand を追加する必要があります。

**ギャラリーから xMatters OnDemand を追加するには、次の手順を実行します。**

1. **[Azure Portal](https://portal.azure.com)** の左側のナビゲーション ウィンドウで、**[Azure Active Directory]** アイコンをクリックします。 

    ![Active Directory][1]

1. **[エンタープライズ アプリケーション]** に移動します。 次に、**[すべてのアプリケーション]** に移動します。

    ![[アプリケーション]][2]
    
1. 新しいアプリケーションを追加するには、ダイアログの上部にある **[新しいアプリケーション]** をクリックします。

    ![[アプリケーション]][3]

1. 検索ボックスに、「**xMatters OnDemand**」と入力します。

    ![Azure AD のテスト ユーザーの作成](./media/xmatters-ondemand-tutorial/tutorial_xmattersondemand_search.png)

1. 結果ウィンドウで **xMatters OnDemand** を選択し、**[追加]** をクリックしてアプリケーションを追加します。

    ![Azure AD のテスト ユーザーの作成](./media/xmatters-ondemand-tutorial/tutorial_xmattersondemand_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成とテスト
このセクションでは、"Britta Simon" というテスト ユーザーに基づいて、xMatters OnDemand で Azure AD のシングル サインオンを構成し、テストします。

シングル サインオンを機能させるには、Azure AD ユーザーに対応する xMatters OnDemand ユーザーが Azure AD で認識されている必要があります。 言い換えると、Azure AD ユーザーと xMatters OnDemand の関連ユーザーの間で、リンク関係が確立されている必要があります。

xMatters OnDemand で、Azure AD の **[ユーザー名]** の値を **[Username]\(ユーザー名\)** の値として割り当ててリンク関係を確立します。

xMatters OnDemand で Azure AD のシングル サインオンを構成してテストするには、次の構成要素を完了する必要があります。

1. **[Azure AD シングル サインオンの構成](#configuring-azure-ad-single-sign-on)** - ユーザーがこの機能を使用できるようにします。
1. **[Azure AD のテスト ユーザーの作成](#creating-an-azure-ad-test-user)** - Britta Simon で Azure AD のシングル サインオンをテストします。
1. **[xMatters OnDemand テスト ユーザーの作成](#creating-a-xmatters-ondemand-test-user)** - xMatters OnDemand で Britta Simon に対応するユーザーを作成し、Azure AD の Britta Simon にリンクさせます。
1. **[Azure AD テスト ユーザーの割り当て](#assigning-the-azure-ad-test-user)** - Britta Simon が Azure AD のシングル サインオンを使用できるようにします。
1. **[シングル サインオンのテスト](#testing-single-sign-on)** - 構成が機能するかどうかを確認します。

### <a name="configuring-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成

このセクションでは、Azure Portal で Azure AD のシングル サインオンを有効にして、xMatters OnDemand アプリケーションでシングル サインオンを構成します。

**xMatters OnDemand で Azure AD シングル サインオンを構成するには、次の手順を実行します。**

1. Azure Portal の **xMatters OnDemand** アプリケーション統合ページで、**[シングル サインオン]** をクリックします。

    ![Configure single sign-on][4]

1. **[シングル サインオン]** ダイアログで、**[モード]** として **[SAML ベースのサインオン]** を選択し、シングル サインオンを有効にします。

    ![Configure single sign-on](./media/xmatters-ondemand-tutorial/tutorial_xmattersondemand_samlbase.png)

1. **[xMatters OnDemand のドメインと URL]** セクションで、次の手順を実行します。

    ![Configure single sign-on](./media/xmatters-ondemand-tutorial/tutorial_xmattersondemand_url.png)
    
    a. **[識別子]** ボックスに、次のパターンで URL を入力します。

    | |
    |--|
    | `https://<companyname>.au1.xmatters.com.au/`|
    | `https://<companyname>.cs1.xmatters.com/`|
    | `https://<companyname>.xmatters.com/`|
    | `https://www.xmatters.com`|
    | `https://<companyname>.xmatters.com.au/`|

    b. **[応答 URL]** ボックスに、次のパターンを使用して URL を入力します。
    
    | |
    |--|
    | `https://<companyname>.au1.xmatters.com.au`|
    | `https://<companyname>.xmatters.com/sp/<instancename>`|
    | `https://<companyname>.cs1.xmatters.com/sp/<instancename>`|
    | `https://<companyname>.au1.xmatters.com.au/<instancename>`|

    > [!NOTE] 
    > これらは実際の値ではありません。 実際の識別子と応答 URL でこれらの値を更新します。 これらの値を取得するには、[xMatters OnDemand サポート チーム](https://www.xmatters.com/company/contact-us/)に問い合わせてください。

1. **[SAML 署名証明書]** セクションで、**[証明書 (Base64)]** をクリックし、証明書ファイルを **c:\\XMatters OnDemand.cer** としてローカルに保存します。

    ![Configure single sign-on](./media/xmatters-ondemand-tutorial/tutorial_xmattersondemand_certificate.png)

    > [!IMPORTANT]
    > [xMatters OnDemand サポート チーム](https://www.xmatters.com/company/contact-us/)に証明書を転送する必要があります。 シングル サインオンの構成を確定するには、その前に、xMatters サポート チームによって証明書がアップロードされる必要があります。 

1. **[保存]** ボタンをクリックします。

    ![Configure single sign-on](./media/xmatters-ondemand-tutorial/tutorial_general_400.png)

1. **[xMatters OnDemand 構成]** セクションで **[xMatters OnDemand の構成]** をクリックし、**[サインオンの構成]** ウィンドウを開きます。 **[クイック リファレンス]** セクションから、**サインアウト URL、SAML エンティティ ID、SAML シングル サインオン サービス URL** をコピーします。

    ![Configure single sign-on](./media/xmatters-ondemand-tutorial/tutorial_xmattersondemand_configure.png) 

1. 別の Web ブラウザーのウィンドウで、XMatters OnDemand の企業サイトに管理者としてログインします。

1. 上部のツールバーで、**[管理者]** をクリックし、左側にあるナビゲーション バーで **[会社の詳細]** をクリックします。

    ![管理](./media/xmatters-ondemand-tutorial/IC776795.png "Admin")

1. **[SAML 構成]** ページで、次の手順を実行します。

    ![SAML 構成](./media/xmatters-ondemand-tutorial/IC776796.png "SAML 構成")

    a. **[Enable SAML]** を選択します。

    b. **[Identity Provider ID]\(ID プロバイダー ID\)** テキストボックスに、Azure portal からコピーした **SAML エンティティ ID** の値を貼り付けます。

    c. **[Single Sign On URL]\(シングル サインオン URL\)** テキストボックスに、Azure portal からコピーした **SAML シングル サインオン サービス URL** の値を貼り付けます。

    d. Azure portal からコピーした**サインアウト URL** を **[Single Logout URL]\(シングル ログアウト URL\)** テキストボックスに貼り付けます。

    e. [会社の詳細] ページで、上部にある **[変更の保存]** をクリックします。

    ![会社の詳細](./media/xmatters-ondemand-tutorial/IC776797.png "Company details")

### <a name="creating-an-azure-ad-test-user"></a>Azure AD のテスト ユーザーの作成
このセクションの目的は、Azure Portal で Britta Simon というテスト ユーザーを作成することです。

![Azure AD ユーザーの作成][100]

**Azure AD でテスト ユーザーを作成するには、次の手順に従います。**

1. **Azure Portal** の左側のナビゲーション ウィンドウで、**[Azure Active Directory]** アイコンをクリックします。

    ![Azure AD のテスト ユーザーの作成](./media/xmatters-ondemand-tutorial/create_aaduser_01.png) 

1. **[ユーザーとグループ]** に移動し、**[すべてのユーザー]** をクリックして、ユーザーの一覧を表示します。

    ![Azure AD のテスト ユーザーの作成](./media/xmatters-ondemand-tutorial/create_aaduser_02.png) 

1. ダイアログの上部にある **[追加]** をクリックして、**[ユーザー]** ダイアログを開きます。

    ![Azure AD のテスト ユーザーの作成](./media/xmatters-ondemand-tutorial/create_aaduser_03.png) 

1. **[ユーザー]** ダイアログ ページで、次の手順を実行します。

    ![Azure AD のテスト ユーザーの作成](./media/xmatters-ondemand-tutorial/create_aaduser_04.png) 

    a. **[名前]** ボックスに「**BrittaSimon**」と入力します。

    b. **[ユーザー名]** ボックスに BrittaSimon の**電子メール アドレス**を入力します。

    c. **[パスワードを表示]** を選択し、**[パスワード]** の値をメモします。

    d. **Create** をクリックしてください。

### <a name="creating-a-xmatters-ondemand-test-user"></a>xMatters OnDemand テスト ユーザーの作成

このセクションの目的は、xMatters OnDemand で Britta Simon というユーザーを作成することです。

**ユーザーを手動で作成する必要がある場合は、次の手順を実行します:**

1. **XMatters OnDemand** テナントにログインします。

1. **[Users]\(ユーザー\)** タブをクリックし、**[Add User]\(ユーザーの追加\)** をクリックします。

   ![ユーザー](./media/xmatters-ondemand-tutorial/IC781048.png "Users")

1. **[ユーザーの追加]** セクションで、次の手順に従います。

    ![Add a User](./media/xmatters-ondemand-tutorial/IC781049.png "Add a User")

    a. **[アクティブ]** を選択します。

    b. **[User ID]\(ユーザー ID\)** ボックスに、ユーザーの ID を入力します (この例では Brittasimon@contoso.com)。

    c. **[First Name]\(名\)** ボックスに、ユーザーの名を入力します (この例では Britta)。

    d. **[Last Name]\(姓\)** ボックスに、ユーザーの姓を入力します (この例では Simon)。

    e. **[Site]\(サイト\)** ボックスに、プロビジョニングする有効な Azure AD アカウントの有効なサイトを入力します。

    f. **[Save]** をクリックします。

### <a name="assigning-the-azure-ad-test-user"></a>Azure AD テスト ユーザーの割り当て

このセクションでは、Britta Simon に xMatters OnDemand へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

![ユーザーの割り当て][200] 

**Britta Simon を xMatters OnDemand に割り当てるには、次の手順を実行します。**

1. Azure Portal でアプリケーション ビューを開き、ディレクトリ ビューに移動します。次に、**[エンタープライズ アプリケーション]** に移動し、**[すべてのアプリケーション]** をクリックします。

    ![ユーザーの割り当て][201] 

1. アプリケーション一覧で **[xMatters OnDemand]** を選択します。

    ![Configure single sign-on](./media/xmatters-ondemand-tutorial/tutorial_xmattersondemand_app.png) 

1. 左側のメニューで **[ユーザーとグループ]** をクリックします。

    ![ユーザーの割り当て][202] 

1. **[追加]** ボタンをクリックします。 次に、**[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。

    ![ユーザーの割り当て][203]

1. **[ユーザーとグループ]** ダイアログで、ユーザーの一覧から **[Britta Simon]** を選択します。

1. **[ユーザーとグループ]** ダイアログで **[選択]** をクリックします。

1. **[割り当ての追加]** ダイアログで **[割り当て]** ボタンをクリックします。
    
### <a name="testing-single-sign-on"></a>シングル サインオンのテスト

このセクションでは、アクセス パネルを使用して Azure AD のシングル サインオン構成をテストします。

アクセス パネルで [xMatters OnDemand] タイルをクリックすると、自動的に xMatters OnDemand アプリケーションにサインオンします。
アクセス パネルの詳細については、[アクセス パネルの概要](../user-help/active-directory-saas-access-panel-introduction.md)に関する記事を参照してください。

## <a name="additional-resources"></a>その他のリソース

* [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](tutorial-list.md)
* [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/xmatters-ondemand-tutorial/tutorial_general_01.png
[2]: ./media/xmatters-ondemand-tutorial/tutorial_general_02.png
[3]: ./media/xmatters-ondemand-tutorial/tutorial_general_03.png
[4]: ./media/xmatters-ondemand-tutorial/tutorial_general_04.png

[100]: ./media/xmatters-ondemand-tutorial/tutorial_general_100.png

[200]: ./media/xmatters-ondemand-tutorial/tutorial_general_200.png
[201]: ./media/xmatters-ondemand-tutorial/tutorial_general_201.png
[202]: ./media/xmatters-ondemand-tutorial/tutorial_general_202.png
[203]: ./media/xmatters-ondemand-tutorial/tutorial_general_203.png
