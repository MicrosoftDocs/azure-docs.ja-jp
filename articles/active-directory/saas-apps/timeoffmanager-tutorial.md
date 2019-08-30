---
title: チュートリアル:Azure Active Directory と TimeOffManager の統合 | Microsoft Docs
description: Azure Active Directory と TimeOffManager の間でシングル サインオンを構成する方法について説明します。
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 3685912f-d5aa-4730-ab58-35a088fc1cc3
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/27/2019
ms.author: jeedes
ms.openlocfilehash: 69c5d30632e187efe36655a17a91c9e373062955
ms.sourcegitcommit: 124c3112b94c951535e0be20a751150b79289594
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/10/2019
ms.locfileid: "68943187"
---
# <a name="tutorial-azure-active-directory-integration-with-timeoffmanager"></a>チュートリアル:Azure Active Directory と TimeOffManager の統合

このチュートリアルでは、TimeOffManager と Azure Active Directory (Azure AD) を統合する方法について説明します。
TimeOffManager と Azure AD の統合には、次の利点があります。

* TimeOffManager にアクセスできるユーザーを Azure AD 上で制御できます。
* ユーザーが自分の Azure AD アカウントを使用して TimeOffManager に自動的にサインイン (シングル サインオン) するように設定できます。
* 1 つの中央サイト (Azure Portal) でアカウントを管理できます。

SaaS アプリと Azure AD の統合の詳細については、「 [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)」を参照してください。
Azure サブスクリプションをお持ちでない場合は、開始する前に[無料アカウントを作成](https://azure.microsoft.com/free/)してください。

## <a name="prerequisites"></a>前提条件

TimeOffManager と Azure AD の統合を構成するには、次のものが必要です。

* Azure AD サブスクリプション。 Azure AD の環境がない場合は、[こちら](https://azure.microsoft.com/pricing/free-trial/)から 1 か月の評価版を入手できます
* TimeOffManager でのシングル サインオンが有効なサブスクリプション

## <a name="scenario-description"></a>シナリオの説明

このチュートリアルでは、テスト環境で Azure AD のシングル サインオンを構成してテストします。

* TimeOffManager では、**IDP** Initiated SSO がサポートされます

* TimeOffManager では、**Just In Time** ユーザー プロビジョニングがサポートされます

## <a name="adding-timeoffmanager-from-the-gallery"></a>ギャラリーからの TimeOffManager の追加

Azure AD への TimeOffManager の統合を構成するには、ギャラリーから管理対象 SaaS アプリの一覧に TimeOffManager を追加する必要があります。

**ギャラリーから TimeOffManager を追加するには、次の手順を実行します。**

1. **[Azure Portal](https://portal.azure.com)** の左側のナビゲーション ウィンドウで、 **[Azure Active Directory]** アイコンをクリックします。

    ![Azure Active Directory のボタン](common/select-azuread.png)

2. **[エンタープライズ アプリケーション]** に移動し、 **[すべてのアプリケーション]** オプションを選択します。

    ![[エンタープライズ アプリケーション] ブレード](common/enterprise-applications.png)

3. 新しいアプリケーションを追加するには、ダイアログの上部にある **[新しいアプリケーション]** をクリックします。

    ![[新しいアプリケーション] ボタン](common/add-new-app.png)

4. 検索ボックスに「**TimeOffManager**」と入力し、結果パネルで **[TimeOffManager]** を選択し、 **[追加]** をクリックして、アプリケーションを追加します。

     ![結果一覧の TimeOffManager](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成とテスト

このセクションでは、**Britta Simon** というテスト ユーザーに基づいて、TimeOffManager で Azure AD のシングル サインオンを構成し、テストします。
シングル サインオンを機能させるには、Azure AD ユーザーと TimeOffManager 内の関連ユーザーとの間にリンク関係が確立されている必要があります。

TimeOffManager で Azure AD のシングル サインオンを構成してテストするには、次の構成要素を完了する必要があります。

1. **[Azure AD シングル サインオンの構成](#configure-azure-ad-single-sign-on)** - ユーザーがこの機能を使用できるようにします。
2. **[TimeOffManager シングル サインオンの構成](#configure-timeoffmanager-single-sign-on)** - アプリケーション側でシングル サインオン設定を構成します。
3. **[Azure AD のテスト ユーザーの作成](#create-an-azure-ad-test-user)** - Britta Simon で Azure AD のシングル サインオンをテストします。
4. **[Azure AD テスト ユーザーの割り当て](#assign-the-azure-ad-test-user)** - Britta Simon が Azure AD シングル サインオンを使用できるようにします。
5. **[TimeOffManager のテスト ユーザーの作成](#create-timeoffmanager-test-user)** - TimeOffManager で Britta Simon に対応するユーザーを作成し、Azure AD の Britta Simon にリンクさせます。
6. **[シングル サインオンのテスト](#test-single-sign-on)** - 構成が機能するかどうかを確認します。

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成

このセクションでは、Azure portal 上で Azure AD のシングル サインオンを有効にします。

TimeOffManager で Azure AD シングル サインオンを構成するには、次の手順を実行します。

1. [Azure portal](https://portal.azure.com/) の **TimeOffManager** アプリケーション統合ページで、 **[シングル サインオン]** を選択します。

    ![シングル サインオン構成のリンク](common/select-sso.png)

2. **[シングル サインオン方式の選択]** ダイアログで、 **[SAML/WS-Fed]** モードを選択して、シングル サインオンを有効にします。

    ![シングル サインオン選択モード](common/select-saml-option.png)

3. **[SAML でシングル サインオンをセットアップします]** ページで、 **[編集]** アイコンをクリックして **[基本的な SAML 構成]** ダイアログを開きます。

    ![基本的な SAML 構成を編集する](common/edit-urls.png)

4. **[基本的な SAML 構成]** セクションで、次の手順を実行します。

    ![[TimeOffManager ドメインと URL] のシングル サインオン情報](common/idp-reply.png)

    **[応答 URL]** ボックスに、`https://www.timeoffmanager.com/cpanel/sso/consume.aspx?company_id=<companyid>` のパターンを使用して URL を入力します

    > [!NOTE]
    > これは実際の値ではありません。 実際の応答 URL でこの値を更新します。 チュートリアルの後半で説明する**シングル サインオン設定ページ**からこの値を取得するか、[TimeOffManager サポート チーム](https://www.purelyhr.com/contact-us)に連絡してください。 Azure portal の **[基本的な SAML 構成]** セクションに示されているパターンを参照することもできます。

5. TimeOffManager アプリケーションでは、特定の形式の SAML アサーションを使用するため、カスタム属性マッピングを SAML トークン属性の構成に追加する必要があります。 次のスクリーンショットには、既定の属性一覧が示されています。 **[編集]** アイコンをクリックして、 **[ユーザー属性]** ダイアログを開きます。

    ![image](common/edit-attribute.png)

6. その他に、TimeOffManager アプリケーションでは、いくつかの属性が SAML 応答で返されることが想定されています。 **[ユーザー属性]** ダイアログの **[ユーザー要求]** セクションで、以下の手順を実行して、以下の表のように SAML トークン属性を追加します。 

    | Name | ソース属性|
    | --- | --- |
    | Firstname |User.givenname |
    | Lastname |User.surname |
    | Email |User.mail |

    a. **[新しい要求の追加]** をクリックして **[ユーザー要求の管理]** ダイアログを開きます。

    ![image](common/new-save-attribute.png)

    ![image](common/new-attribute-details.png)

    b. **[名前]** ボックスに、その行に対して表示される属性名を入力します。

    c. **[名前空間]** は空白のままにします。

    d. [ソース] として **[属性]** を選択します。

    e. **[ソース属性]** の一覧から、その行に表示される属性値を入力します。

    f. **[OK]** をクリックします。

    g. **[Save]** をクリックします。

7. **[SAML でシングル サインオンをセットアップします]** ページの **[SAML 署名証明書]** セクションで、 **[ダウンロード]** をクリックして要件のとおりに指定したオプションからの**証明書 (Base64)** をダウンロードして、お使いのコンピューターに保存します。

    ![証明書のダウンロードのリンク](common/certificatebase64.png)

8. **[TimeOffManager のセットアップ]** セクションで、要件に従って適切な URL をコピーします。

    ![構成 URL のコピー](common/copy-configuration-urls.png)

    a. ログイン URL

    b. Azure AD 識別子

    c. ログアウト URL

### <a name="configure-timeoffmanager-single-sign-on"></a>TimeOffManager シングル サインオンの構成

1. 別の Web ブラウザーのウィンドウで、管理者として TimeOffManager 企業サイトにサインインします。

2. **[Account] \> [Account Options] \> [Single Sign-On Settings]** に移動します。
   
    ![Single Sign-On Settings](./media/timeoffmanager-tutorial/ic795917.png "Single Sign-On Settings")

3. **[シングル サインオンの設定]** セクションで、次の手順に従います。
   
    ![Single Sign-On Settings](./media/timeoffmanager-tutorial/ic795918.png "Single Sign-On Settings")
   
    a. base-64 でエンコードされた証明書をメモ帳で開き、その内容をクリップボードにコピーして、証明書全体を **[X.509 Certificate]** テキスト ボックスに貼り付けます。
   
    b. **[Idp Issuer]\(Idp 発行者\)** ボックスに、Azure portal からコピーした **[Azure AD 識別子]** の値を貼り付けます。
   
    c. **[Idp Endpoint URL]\(IdP エンドポイント URL\)** ボックスに、Azure portal からコピーした **[ログイン URL]** の値を貼り付けます。
   
    d. **[Enforce SAML]** で **[No]** を選択します。
   
    e. **[Auto-Create Users]** で **[Yes]** を選択します。
   
    f. **[Logout URL]\(ログアウト URL\)** ボックスに、Azure portal からコピーした **[ログアウト URL]** の値を貼り付けます。
   
    g. **[変更を保存]** をクリックします。

4. **[Single Sign on settings]\(シングル サインオンの設定\)** ページで **[Assertion Consumer Service URL]\(アサーション コンシューマー サービス URL\)** の値をコピーして、それを Azure portal の **[基本的な SAML 構成]** セクションの **[応答 URL]** ボックスに貼り付けます。 

      ![Single Sign-On Settings](./media/timeoffmanager-tutorial/ic795915.png "Single Sign-On Settings")

### <a name="create-an-azure-ad-test-user"></a>Azure AD のテスト ユーザーの作成 

このセクションの目的は、Azure Portal で Britta Simon というテスト ユーザーを作成することです。

1. Azure portal の左側のウィンドウで、 **[Azure Active Directory]** 、 **[ユーザー]** 、 **[すべてのユーザー]** の順に選択します。

    ![[ユーザーとグループ] と [すべてのユーザー] リンク](common/users.png)

2. 画面の上部にある **[新しいユーザー]** を選択します。

    ![[新しいユーザー] ボタン](common/new-user.png)

3. [ユーザーのプロパティ] で、次の手順を実行します。

    ![[ユーザー] ダイアログ ボックス](common/user-properties.png)

    a. **[名前]** フィールドに「**BrittaSimon**」と入力します。
  
    b. **[ユーザー名]** フィールドに「brittasimon@yourcompanydomain.extension」と入力します。 たとえば、BrittaSimon@contoso.com のように指定します。

    c. **[パスワードを表示]** チェック ボックスをオンにし、[パスワード] ボックスに表示された値を書き留めます。

    d. **Create** をクリックしてください。

### <a name="assign-the-azure-ad-test-user"></a>Azure AD テスト ユーザーの割り当て

このセクションでは、Britta Simon に TimeOffManager へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

1. Azure portal で **[エンタープライズ アプリケーション]** を選択し、 **[すべてのアプリケーション]** 、 **[TimeOffManager]** の順に選択します。

    ![[エンタープライズ アプリケーション] ブレード](common/enterprise-applications.png)

2. アプリケーションの一覧で **TimeOffManager** を選択します。

    ![アプリケーションの一覧の TimeOffManager のリンク](common/all-applications.png)

3. 左側のメニューで **[ユーザーとグループ]** を選びます。

    ![[ユーザーとグループ] リンク](common/users-groups-blade.png)

4. **[ユーザーの追加]** をクリックし、 **[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。

    ![[割り当ての追加] ウィンドウ](common/add-assign-user.png)

5. **[ユーザーとグループ]** ダイアログの [ユーザー] の一覧で **[Britta Simon]** を選択し、画面の下部にある **[選択]** ボタンをクリックします。

6. SAML アサーション内に任意のロール値が必要な場合、 **[ロールの選択]** ダイアログでユーザーに適したロールを一覧から選択し、画面の下部にある **[選択]** をクリッします。

7. **[割り当ての追加]** ダイアログで、 **[割り当て]** ボタンをクリックします。

### <a name="create-timeoffmanager-test-user"></a>TimeOffManager のテスト ユーザーの作成

このセクションでは、Britta Simon というユーザーを TimeOffManager に作成します。 TimeOffManager では、Just-In-Time ユーザー プロビジョニングがサポートされており、既定で有効になっています。 このセクションでは、ユーザー側で必要な操作はありません。 TimeOffManager にユーザーがまだ存在していない場合は、認証後に新しく作成されます。

>[!NOTE]
>TimeOffManager から提供されている他の TimeOffManager ユーザー アカウント作成ツールまたは API を使用して、Azure AD ユーザー アカウントをプロビジョニングできます。
> 

### <a name="test-single-sign-on"></a>シングル サインオンのテスト 

このセクションでは、アクセス パネルを使用して Azure AD のシングル サインオン構成をテストします。

アクセス パネル上で [TimeOffManager] タイルをクリックすると、SSO を設定した TimeOffManager に自動的にサインインします。 アクセス パネルの詳細については、[アクセス パネルの概要](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)に関する記事を参照してください。

## <a name="additional-resources"></a>その他のリソース

- [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Azure Active Directory の条件付きアクセスとは](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

