---
title: チュートリアル:Azure Active Directory と TINFOIL SECURITY の統合 | Microsoft Docs
description: Azure Active Directory と TINFOIL SECURITY の間でシングル サインオンを構成する方法について説明します。
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: da02da92-e3b0-4c09-ad6c-180882b0f9f8
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/27/2019
ms.author: jeedes
ms.openlocfilehash: 135b5719422d1b28a82ac2eda06f76d6dd746800
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/18/2019
ms.locfileid: "59262382"
---
# <a name="tutorial-azure-active-directory-integration-with-tinfoil-security"></a>チュートリアル:Azure Active Directory と TINFOIL SECURITY の統合

このチュートリアルでは、TINFOIL SECURITY と Azure Active Directory (Azure AD) を統合する方法について説明します。
TINFOIL SECURITY と Azure AD の統合には、次の利点があります。

* TINFOIL SECURITY にアクセスできるユーザーを Azure AD 上で制御できます。
* ユーザーが自分の Azure AD アカウントを使用して TINFOIL SECURITY に自動的にサインイン (シングル サインオン) するように設定できます。
* 1 つの中央サイト (Azure Portal) でアカウントを管理できます。

SaaS アプリと Azure AD の統合の詳細については、「 [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)」を参照してください。
Azure サブスクリプションをお持ちでない場合は、開始する前に[無料アカウントを作成](https://azure.microsoft.com/free/)してください。

## <a name="prerequisites"></a>前提条件

TINFOIL SECURITY と Azure AD の統合を構成するには、次のものが必要です。

* Azure AD サブスクリプション。 Azure AD の環境がない場合は、[こちら](https://azure.microsoft.com/pricing/free-trial/)から 1 か月の評価版を入手できます
* TINFOIL SECURITY でのシングル サインオンが有効なサブスクリプション

## <a name="scenario-description"></a>シナリオの説明

このチュートリアルでは、テスト環境で Azure AD のシングル サインオンを構成してテストします。

* TINFOIL SECURITY では、**IDP** Initiated SSO がサポートされます

## <a name="adding-tinfoil-security-from-the-gallery"></a>ギャラリーからの TINFOIL SECURITY の追加

Azure AD への TINFOIL SECURITY の統合を構成するには、ギャラリーから管理対象 SaaS アプリの一覧に TINFOIL SECURITY を追加する必要があります。

**ギャラリーから TINFOIL SECURITY を追加するには、次の手順を実行します。**

1. **[Azure Portal](https://portal.azure.com)** の左側のナビゲーション ウィンドウで、**[Azure Active Directory]** アイコンをクリックします。

    ![Azure Active Directory のボタン](common/select-azuread.png)

2. **[エンタープライズ アプリケーション]** に移動し、**[すべてのアプリケーション]** オプションを選択します。

    ![[エンタープライズ アプリケーション] ブレード](common/enterprise-applications.png)

3. 新しいアプリケーションを追加するには、ダイアログの上部にある **[新しいアプリケーション]** をクリックします。

    ![[新しいアプリケーション] ボタン](common/add-new-app.png)

4. 検索ボックスに「**TINFOIL SECURITY**」と入力し、結果パネルで **[TINFOIL SECURITY]** を選択し、**[追加]** をクリックして、アプリケーションを追加します。

     ![結果一覧の TINFOIL SECURITY](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成とテスト

このセクションでは、**Britta Simon** というテスト ユーザーに基づいて、TINFOIL SECURITY で Azure AD のシングル サインオンを構成し、テストします。
シングル サインオンを機能させるには、Azure AD ユーザーと TINFOIL SECURITY 内の関連ユーザーとの間にリンク関係が確立されている必要があります。

TINFOIL SECURITY で Azure AD のシングル サインオンを構成してテストするには、次の手順を完了する必要があります。

1. **[Azure AD シングル サインオンの構成](#configure-azure-ad-single-sign-on)** - ユーザーがこの機能を使用できるようにします。
2. **[TINFOIL SECURITY シングル サインオンの構成](#configure-tinfoil-security-single-sign-on)** - アプリケーション側でシングル サインオン設定を構成します。
3. **[Azure AD のテスト ユーザーの作成](#create-an-azure-ad-test-user)** - Britta Simon で Azure AD のシングル サインオンをテストします。
4. **[Azure AD テスト ユーザーの割り当て](#assign-the-azure-ad-test-user)** - Britta Simon が Azure AD シングル サインオンを使用できるようにします。
5. **[TINFOIL SECURITY のテスト ユーザーの作成](#create-tinfoil-security-test-user)** - TINFOIL SECURITY で Britta Simon に対応するユーザーを作成し、Azure AD の Britta Simon にリンクさせます。
6. **[シングル サインオンのテスト](#test-single-sign-on)** - 構成が機能するかどうかを確認します。

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成

このセクションでは、Azure portal 上で Azure AD のシングル サインオンを有効にします。

TINFOIL SECURITY で Azure AD シングル サインオンを構成するには、次の手順を実行します。

1. [Azure portal](https://portal.azure.com/) の **TINFOIL SECURITY** アプリケーション統合ページで、**[シングル サインオン]** を選択します。

    ![シングル サインオン構成のリンク](common/select-sso.png)

2. **[シングル サインオン方式の選択]** ダイアログで、**[SAML/WS-Fed]** モードを選択して、シングル サインオンを有効にします。

    ![シングル サインオン選択モード](common/select-saml-option.png)

3. **[SAML でシングル サインオンをセットアップします]** ページで、**[編集]** アイコンをクリックして **[基本的な SAML 構成]** ダイアログを開きます。

    ![基本的な SAML 構成を編集する](common/edit-urls.png)

4. アプリは Azure と事前に統合済みであるため、**[基本的な SAML 構成]** セクションで実行が必要な手順はありません。

    ![[TINFOIL SECURITY ドメインと URL] のシングル サインオン情報](common/preintegrated.png)

5. TINFOIL SECURITY アプリケーションでは、特定の形式の SAML アサーションを使用するため、カスタム属性マッピングを SAML トークン属性の構成に追加する必要があります。 次のスクリーンショットには、既定の属性一覧が示されています。  **[編集]** アイコンをクリックして、 **[ユーザー属性]** ダイアログを開きます。

        ![image](common/edit-attribute.png)

6. その他に、TINFOIL SECURITY アプリケーションでは、いくつかの属性が SAML 応答で返されることが想定されています。 **[ユーザー属性]** ダイアログの **[ユーザー要求]** セクションで、以下の手順を実行して、以下の表のように SAML トークン属性を追加します。

    | Name | ソース属性 |
    | ------------------- | -------------|
    | accountid | UXXXXXXXXXXXXX |

    a. **[新しい要求の追加]** をクリックして **[ユーザー要求の管理]** ダイアログを開きます。

    ![image](common/new-save-attribute.png)

    ![image](common/new-attribute-details.png)

    b. **[名前]** ボックスに、その行に対して表示される属性名を入力します。

    c. **[名前空間]** は空白のままにします。

    d. [ソース] として **[属性]** を選択します。

    e. **[ソース属性]** ボックスに、アカウント ID の値を貼り付けます。この値はこのチュートリアルの後の手順で取得します。

    f. **[OK]** をクリックします。

    g. **[Save]** をクリックします。

7. **[SAML 署名証明書]** セクションで **[編集]** ボタンをクリックして、**[SAML 署名証明書]** ダイアログを開きます。

    ![SAML 署名証明書の編集](common/edit-certificate.png)

8. **[SAML 署名証明書]** セクションで **[Thumbprint]\(拇印\)** をコピーし、お使いのコンピューターに保存します。

    ![[Thumbprint]\(拇印\) の値をコピーする](common/copy-thumbprint.png)

9. **[TINFOIL SECURITY のセットアップ]** セクションで、要件に従って適切な URL をコピーします。

    ![構成 URL のコピー](common/copy-configuration-urls.png)

    a. ログイン URL

    b. Azure AD 識別子

    c. ログアウト URL

### <a name="configure-tinfoil-security-single-sign-on"></a>TINFOIL SECURITY シングル サインオンの構成

1. 別の Web ブラウザーのウィンドウで、TINFOIL SECURITY の企業サイトに管理者としてサインインします。

2. 上部のツール バーの **[My Account]** をクリックします。
   
    ![Dashboard](./media/tinfoil-security-tutorial/ic798971.png "Dashboard")

3. **[セキュリティ]** をクリックします。
   
    ![Security (セキュリティ)](./media/tinfoil-security-tutorial/ic798972.png "Security")

4. **[シングル サインオン]** 構成ページで、次の手順を実行します。
   
    ![シングル サインオン](./media/tinfoil-security-tutorial/ic798973.png "Single Sign-On")
   
    a. **[Enable SAML]** を選択します。
   
    b. **[Manual Configuration]** をクリックします。
   
    c. **[SAML Post URL]\(SAML POST の URL\)** ボックスに、Azure portal からコピーした **[ログイン URL]** の値を貼り付けます
   
    d. **[SAML Certificate Fingerprint]\(SAML 証明書フィンガープリント\)** ボックスに、**[SAML 署名証明書]** セクションからコピーした**拇印**の値を貼り付けます。
  
    e. **[Your Account ID]\(アカウント ID\)** の値をコピーし、Azure Portal の **[属性の追加]** セクションの **[属性値]** ボックスに値を貼り付けます。
   
    f. **[Save]** をクリックします。

### <a name="create-an-azure-ad-test-user"></a>Azure AD のテスト ユーザーの作成 

このセクションの目的は、Azure Portal で Britta Simon というテスト ユーザーを作成することです。

1. Azure portal の左側のウィンドウで、**[Azure Active Directory]**、**[ユーザー]**、**[すべてのユーザー]** の順に選択します。

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

このセクションでは、Britta Simon に TINFOIL SECURITY へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

1. Azure portal で **[エンタープライズ アプリケーション]** を選択し、**[すべてのアプリケーション]**、**[TINFOIL SECURITY]** の順に選択します。

    ![[エンタープライズ アプリケーション] ブレード](common/enterprise-applications.png)

2. アプリケーションの一覧で **[TINFOIL SECURITY]** を選択します。

    ![アプリケーションの一覧の TINFOIL SECURITY のリンク](common/all-applications.png)

3. 左側のメニューで **[ユーザーとグループ]** を選びます。

    ![[ユーザーとグループ] リンク](common/users-groups-blade.png)

4. **[ユーザーの追加]** をクリックし、**[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。

    ![[割り当ての追加] ウィンドウ](common/add-assign-user.png)

5. **[ユーザーとグループ]** ダイアログの [ユーザー] の一覧で **[Britta Simon]** を選択し、画面の下部にある **[選択]** ボタンをクリックします。

6. SAML アサーション内に任意のロール値が必要な場合、**[ロールの選択]** ダイアログでユーザーに適したロールを一覧から選択し、画面の下部にある **[選択]** をクリッします。

7. **[割り当ての追加]** ダイアログで、**[割り当て]** ボタンをクリックします。

### <a name="create-tinfoil-security-test-user"></a>TINFOIL SECURITY のテスト ユーザーの作成

Azure AD ユーザーが TINFOIL SECURITY にサインインできるようにするには、そのユーザーを TINFOIL SECURITY にプロビジョニングする必要があります。 TINFOIL SECURITY の場合、プロビジョニングは手動で行います。

**ユーザーをプロビジョニングするには、次の手順に従います。**

1. ユーザーがエンタープライズ アカウントに参加している場合は、[TINFOIL SECURITY サポート チームに連絡](https://www.tinfoilsecurity.com/contact)して、ユーザー アカウントを作成する必要があります。

1. 通常の TINFOIL SECURITY SaaS ユーザーは、グループ作業者をユーザーの任意のサイトに追加できます。 ここでは、新しい TINFOIL SECURITY ユーザー アカウントを作成するための招待状を指定した電子メール アドレスに送信するプロセスが起動します。

> [!NOTE]
> TINFOIL SECURITY から提供されている他の TINFOIL SECURITY ユーザー アカウント作成ツールまたは API を使用して、Azure AD ユーザー アカウントをプロビジョニングできます。
> 

### <a name="test-single-sign-on"></a>シングル サインオンのテスト 

このセクションでは、アクセス パネルを使用して Azure AD のシングル サインオン構成をテストします。

アクセス パネル上で [TINFOIL SECURITY] タイルをクリックすると、SSO を設定した TINFOIL SECURITY に自動的にサインインします。 アクセス パネルの詳細については、[アクセス パネルの概要](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)に関する記事を参照してください。

## <a name="additional-resources"></a>その他のリソース

- [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Azure Active Directory の条件付きアクセスとは](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

