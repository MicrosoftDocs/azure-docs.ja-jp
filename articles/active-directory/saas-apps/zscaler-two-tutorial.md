---
title: チュートリアル:Azure Active Directory と Zscaler Two の統合 | Microsoft Docs
description: Azure Active Directory と Zscaler Two の間でシングル サインオンを構成する方法について説明します。
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 1fd8a940-7320-47e0-a176-2dd4eeca6db2
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/10/2018
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: cdfd60cf91df013e097e7e09c04b589416d1f531
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2019
ms.locfileid: "57880672"
---
# <a name="tutorial-azure-active-directory-integration-with-zscaler-two"></a>チュートリアル:Azure Active Directory と Zscaler Two の統合

このチュートリアルでは、Zscaler Two と Azure Active Directory (Azure AD) を統合する方法について説明します。

Zscaler Two と Azure AD の統合には、次の利点があります。

- Zscaler Two にアクセスするユーザーを Azure AD で管理できます。
- ユーザーが Azure AD アカウントで Zscaler Two に自動的にサインオン (シングル サインオン) できるようにすることが可能です。
- 1 つの中央サイト (Azure Portal) でアカウントを管理できます。

SaaS アプリと Azure AD の統合の詳細については、「[Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)」をご覧ください。

## <a name="prerequisites"></a>前提条件

Zscaler Two と Azure AD の統合を構成するには、次の項目が必要です。

- Azure AD サブスクリプション
- Zscaler Two でのシングル サインオンが有効なサブスクリプション

> [!NOTE]
> このチュートリアルの手順をテストする場合、運用環境を使用しないことをお勧めします。

このチュートリアルの手順をテストするには、次の推奨事項に従ってください。

- 必要な場合を除き、運用環境は使用しないでください。
- Azure AD の評価環境がない場合は、[1 か月の評価版を入手できます](https://azure.microsoft.com/pricing/free-trial/)。

## <a name="scenario-description"></a>シナリオの説明

このチュートリアルでは、テスト環境で Azure AD のシングル サインオンをテストします。 このチュートリアルで説明するシナリオは、主に次の 2 つの要素で構成されています。

1. ギャラリーからの Zscaler Two の追加
2. Azure AD シングル サインオンの構成とテスト

## <a name="adding-zscaler-two-from-the-gallery"></a>ギャラリーからの Zscaler Two の追加

Azure AD への Zscaler Two の統合を構成するには、管理対象の SaaS アプリ一覧に Zscaler Two をギャラリーから追加する必要があります。

**ギャラリーから Zscaler Two を追加するには、次の手順に従います。**

1. **[Azure Portal](https://portal.azure.com)** の左側のナビゲーション ウィンドウで、**[Azure Active Directory]** アイコンをクリックします。 

    ![Azure Active Directory のボタン][1]

2. **[エンタープライズ アプリケーション]** に移動します。 次に、**[すべてのアプリケーション]** に移動します。

    ![[エンタープライズ アプリケーション] ブレード][2]

3. 新しいアプリケーションを追加するには、ダイアログの上部にある **[新しいアプリケーション]** をクリックします。

    ![[新しいアプリケーション] ボタン][3]

4. 検索ボックスに「**Zscaler Two**」と入力し、結果パネルで **[Zscaler Two]** を選び、**[追加]** をクリックして、アプリケーションを追加します。

    ![結果リストの Zscaler Two](./media/zscaler-two-tutorial/tutorial_zscalertwo_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成とテスト

このセクションでは、"Britta Simon" というテスト ユーザーに基づいて、Zscaler Two で Azure AD のシングル サインオンを構成し、テストします。

シングル サインオンを機能させるには、Azure AD ユーザーに対応する Zscaler Two ユーザーが Azure AD で認識されている必要があります。 言い換えると、Azure AD ユーザーと Zscaler Two の関連ユーザーの間で、リンク関係が確立されている必要があります。

Zscaler Two で Azure AD のシングル サインオンを構成してテストするには、次の構成要素を完了する必要があります。

1. **[Azure AD シングル サインオンの構成](#configuring-azure-ad-single-sign-on)** - ユーザーがこの機能を使用できるようにします。
2. **[Azure AD のテスト ユーザーの作成](#creating-an-azure-ad-test-user)** - Britta Simon で Azure AD のシングル サインオンをテストします。
3. **[Zscaler Two のテスト ユーザーの作成](#creating-a-zscaler-two-test-user)** - Zscaler Two で Britta Simon に対応するユーザーを作成し、Azure AD の Britta Simon にリンクさせます。
4. **[Azure AD テスト ユーザーの割り当て](#assigning-the-azure-ad-test-user)** - Britta Simon が Azure AD のシングル サインオンを使用できるようにします。
5. **[シングル サインオンのテスト](#testing-single-sign-on)** - 構成が機能するかどうかを確認します。

### <a name="configuring-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成

このセクションでは、Azure Portal で Azure AD のシングル サインオンを有効にして、Zscaler Two アプリケーションでシングル サインオンを構成します。

**Zscaler Two で Azure AD シングル サインオンを構成するには、次の手順に従います。**

1. Azure Portal の **Zscaler Two** アプリケーション統合ページで、**[シングル サインオン]** をクリックします。

    ![シングル サインオン構成のリンク][4]

2. **[シングル サインオン方式の選択]** ダイアログで、**[SAML]** モードの **[選択]** をクリックして、シングル サインオンを有効にします。

    ![Configure single sign-on](common/tutorial_general_301.png)

3. **[SAML でシングル サインオンをセットアップします]** ページで、**[編集]** アイコンをクリックして **[基本的な SAML 構成]** ダイアログを開きます。

    ![Configure single sign-on](common/editconfigure.png)

4. **[基本的な SAML 構成]** セクションで、次の手順を実行します。

    ![Zscaler Two のドメインと URL のシングル サインオン情報](./media/zscaler-two-tutorial/tutorial_zscalertwo_url.png)

    [サインオン URL] ボックスに、ユーザーが Zscaler Two アプリケーションへのサインオンに使用する URL を入力します。

    > [!NOTE] 
    > この値は実際のサインオン URL で更新する必要があります。 これらの値を取得するには、[Zscaler Two クライアント サポート チーム](https://www.zscaler.com/company/contact)に問い合わせてください。

5. Zscaler Two アプリケーションは、特定の形式で構成された SAML アサーションを受け入れます。 このアプリケーションには、次の要求を構成します。 これらの属性の値は、アプリケーション統合ページの **[ユーザー属性と要求]** セクションで管理できます。 **[SAML でシングル サインオンをセットアップします]** ページで、**[編集]** ボタンをクリックして **[ユーザー属性と要求]** ダイアログを開きます。

    ![属性リンク](./media/zscaler-two-tutorial/tutorial_zscalertwo_attribute.png)

6. **[ユーザー属性]** ダイアログの **[ユーザーの要求]** セクションで、上の図のように SAML トークン属性を構成し、次の手順を実行します。

    | Name  | ソース属性  |
    | ---------| ------------ |
    | memberOf     | user.assignedroles |

    a. **[新しい要求の追加]** をクリックして **[ユーザー要求の管理]** ダイアログを開きます。

    ![image](./common/new_save_attribute.png)
    
    ![image](./common/new_attribute_details.png)

    b. **[ソース属性]** の一覧から、属性値を選択します。

    c. **[OK]** をクリックします。

    d. **[Save]** をクリックします。

    > [!NOTE]
    > Azure AD で役割を構成する方法については、[ここ](https://docs.microsoft.com/azure/active-directory/active-directory-enterprise-app-role-management)をクリックしてください

7. **[SAML 署名証明書]** ページの **[SAML 署名証明書]** セクションで、**[ダウンロード]** をクリックして**証明書 (Base64)** をダウンロードし、証明書ファイルをコンピューターに保存します。

    ![証明書のダウンロードのリンク](./media/zscaler-two-tutorial/tutorial_zscalertwo_certificate.png) 

8. **[Set up Zscaler Two]\(Zscaler Two の設定\)** セクションで、要件どおりの適切な URL をコピーします。

    a. ログイン URL

    b. Azure AD 識別子

    c. ログアウト URL

    ![Zscaler Two 構成](common/configuresection.png)

9. 別の Web ブラウザー ウィンドウで、Zscaler Two 企業サイトに管理者としてログインします。

10. **[Administration]\(管理\) > [Authentication]\(認証\) > [Authentication Settings]\(認証の設定\)** に移動し、次の手順を実行します。
   
    ![Administration](./media/zscaler-two-tutorial/ic800206.png "Administration")

    a. [Authentication Type]\(認証の種類\)で **[SAML]** を選択します。

    b. **[Configure SAML]** をクリックします。

11. **[Edit SAML]\(SAML の編集)** ウィンドウで次の手順を実行して、[保存] をクリックします。  
            
    ![ユーザーと認証の管理](./media/zscaler-two-tutorial/ic800208.png "Manage Users & Authentication")
    
    a. **[SAML Portal URL]\(SAML ポータル URL\)** テキスト ボックスに、Azure portal からコピーした**ログイン URL** を貼り付けます。

    b. **[Login Name Attribute]\(ログイン名属性\)** テキスト ボックスに **NameID** の値を入力します。

    c. **アップロード** をクリックして、Azure portal からダウンロードした Azure SAML 署名証明書を **Public SSL Certificate\(パブリック SSL 証明書\)** にアップロードします。

    d. **[SAML 自動プロビジョニングを有効にする]** を選択します。

    e. displayName 属性に SAML 自動プロビジョニングを有効にするには、**[User Display Name Attribute]\(ユーザー表示名属性\)** テキスト ボックスに **displayName** の値を入力します。

    f. memberOf 属性に SAML 自動プロビジョニングを有効にするには、**[Group Name Attribute]\(グループ名属性\)** テキスト ボックスに **memberOf** の値を入力します。

    g. department 属性に SAML 自動プロビジョニングを有効にするには、**[Department Name Attribute]\(部署名属性\)** テキスト ボックスに **department** の値を入力します。

    i. **[Save]** をクリックします。

12. **[ユーザー認証の構成]** ダイアログ ページで、次の手順に従います。

    ![管理](./media/zscaler-two-tutorial/ic800207.png)

    a. 左下の **[Activation]\(アクティブ化\)** メニューにポインターを置きます。

    b. **[アクティブ化]** をクリックします。

## <a name="configuring-proxy-settings"></a>プロキシ設定の構成
### <a name="to-configure-the-proxy-settings-in-internet-explorer"></a>Internet Explorer でプロキシ設定を構成するには

1. **Internet Explorer**を開始します。

1. **[ツール]** メニューの **[インターネット オプション]** を選択し、**[インターネット オプション]** ダイアログを開きます。   
    
     ![インターネット オプション](./media/zscaler-two-tutorial/ic769492.png "Internet Options")

1. **[接続]** タブをクリックします。   
  
     ![接続](./media/zscaler-two-tutorial/ic769493.png "Connections")

1. **[LAN の設定]** をクリックして **[LAN の設定]** ダイアログを開きます。

1. [プロキシ サーバー] セクションで、次の手順を実行します。   
   
    ![プロキシ サーバー](./media/zscaler-two-tutorial/ic769494.png "Proxy server")

    a. **[LAN にプロキシ サーバーを使用する]** をオンにします。

    b. [アドレス] テキスト ボックスに「**gateway.Zscaler Two.net**」と入力します。

    c. [ポート] ボックスに「 **80**」と入力します。

    d. **[ローカル アドレスにはプロキシ サーバーを使用しない]** を選択します。

    e. **[OK]** をクリックして **[ローカル エリア ネットワーク (LAN) の設定]** ダイアログを閉じます。

1. **[OK]** をクリックして **[インターネット オプション]** ダイアログを閉じます。

### <a name="creating-an-azure-ad-test-user"></a>Azure AD のテスト ユーザーの作成

このセクションの目的は、Azure Portal で Britta Simon というテスト ユーザーを作成することです。

1. Azure portal の左側のウィンドウで、**[Azure Active Directory]**、**[ユーザー]**、**[すべてのユーザー]** の順に選択します。

    ![Azure AD ユーザーの作成][100]

2. 画面の上部にある **[新しいユーザー]** を選択します。

    ![Azure AD のテスト ユーザーの作成](common/create_aaduser_01.png) 

3. [ユーザーのプロパティ] で、次の手順を実行します。

    ![Azure AD のテスト ユーザーの作成](common/create_aaduser_02.png)

    a. **[名前]** フィールドに「**BrittaSimon**」と入力します。
  
    b. **[ユーザー名]** フィールドに「**brittasimon\@yourcompanydomain.extension**」と入力します。  
    たとえば、BrittaSimon@contoso.com のように指定します。

    c. **[プロパティ]** を選択し、**[パスワードを表示]** チェック ボックスをオンにして、[パスワード] ボックスに表示された値を書き留めます。

    d. **作成**を選択します。

### <a name="creating-a-zscaler-two-test-user"></a>Zscaler Two テスト ユーザーの作成

このセクションの目的は、Zscaler Two に Britta Simon というユーザーを作成することです。 Zscaler Two では、Just-In-Time プロビジョニングがサポートされています。この設定は、既定で有効になっています。 このセクションでは、ユーザー側で必要な操作はありません。 存在しない Zscaler Two にアクセスしようとすると、新しいユーザーが作成されます。
>[!Note]
>ユーザーを手動で作成する必要がある場合は、 [Zscaler Two サポート チーム](https://www.zscaler.com/company/contact)にお問い合わせください。

### <a name="assigning-the-azure-ad-test-user"></a>Azure AD テスト ユーザーの割り当て

このセクションでは、Britta Simon に Zscaler Two へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

1. Azure portal で **[エンタープライズ アプリケーション]** を選択し、**[すべてのアプリケーション]** を選択します。

    ![ユーザーの割り当て][201]

2. アプリケーションの一覧で、**[Zscaler Two]** を選択します。

    ![Configure single sign-on](./media/zscaler-two-tutorial/tutorial_zscalertwo_app.png)

3. 左側のメニューで **[ユーザーとグループ]** をクリックします。

    ![ユーザーの割り当て][202]

4. **[追加]** ボタンをクリックし、**[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。

    ![ユーザーの割り当て][203]

5. **[ユーザーとグループ]** ダイアログの一覧から **[Britta Simon]** などのユーザーを選択し、画面の下部にある **[選択]** ボタンをクリックします。

    ![image](./media/zscaler-two-tutorial/tutorial_zscalertwo_users.png)

6. **[ロールの選択]** ダイアログで、一覧から適切なユーザー ロールを選択し、画面の下部にある **[選択]** ボタンをクリックします。

    ![image](./media/zscaler-two-tutorial/tutorial_zscalertwo_roles.png)

7. **[割り当ての追加]** ダイアログで、**[割り当て]** ボタンを選択します。

    ![image](./media/zscaler-two-tutorial/tutorial_zscalertwo_assign.png)

### <a name="testing-single-sign-on"></a>シングル サインオンのテスト

このセクションでは、アクセス パネルを使用して Azure AD のシングル サインオン構成をテストします。

アクセス パネルで Zscaler Two のタイルをクリックすると、自動的に Zscaler Two アプリケーションにサインオンします。
アクセス パネルの詳細については、[アクセス パネルの概要](../user-help/active-directory-saas-access-panel-introduction.md)に関するページを参照してください。

## <a name="additional-resources"></a>その他のリソース

* [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](tutorial-list.md)
* [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: common/tutorial_general_01.png
[2]: common/tutorial_general_02.png
[3]: common/tutorial_general_03.png
[4]: common/tutorial_general_04.png

[100]: common/tutorial_general_100.png

[201]: common/tutorial_general_201.png
[202]: common/tutorial_general_202.png
[203]: common/tutorial_general_203.png
