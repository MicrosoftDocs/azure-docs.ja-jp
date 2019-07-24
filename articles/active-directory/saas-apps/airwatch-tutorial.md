---
title: チュートリアル:Azure Active Directory と AirWatch の統合 | Microsoft Docs
description: Azure Active Directory と AirWatch の間でシングル サインオンを構成する方法について説明します。
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 96a3bb1c-96c6-40dc-8ea0-060b0c2a62e5
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 02/07/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: e20a298c34a7b2723963396f8ccaafaa9472e19a
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2019
ms.locfileid: "57888333"
---
# <a name="tutorial-azure-active-directory-integration-with-airwatch"></a>チュートリアル:Azure Active Directory と AirWatch の統合

このチュートリアルでは、AirWatch と Azure Active Directory (Azure AD) を統合する方法について説明します。
AirWatch と Azure AD の統合には、次の利点があります。

* AirWatch にアクセスする Azure AD ユーザーを制御できます。
* ユーザーが自分の Azure AD アカウントを使用して AirWatch に自動的にサインイン (シングル サインオン) できるようにすることが可能です。
* 1 つの中央サイト (Azure Portal) でアカウントを管理できます。

SaaS アプリと Azure AD の統合の詳細については、「 [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)」を参照してください。
Azure サブスクリプションをお持ちでない場合は、開始する前に[無料アカウントを作成](https://azure.microsoft.com/free/)してください。

## <a name="prerequisites"></a>前提条件

AirWatch と Azure AD の統合を構成するには、次のものが必要です。

* Azure AD サブスクリプション。 Azure AD の環境がない場合は、[こちら](https://azure.microsoft.com/pricing/free-trial/)から 1 か月の評価版を入手できます
* AirWatch でのシングル サインオンが有効なサブスクリプション

## <a name="scenario-description"></a>シナリオの説明

このチュートリアルでは、テスト環境で Azure AD のシングル サインオンを構成してテストします。

* AirWatch では、**SP** によって開始される SSO がサポートされます

## <a name="adding-airwatch-from-the-gallery"></a>ギャラリーから AirWatch を追加する

Azure AD への AirWatch の統合を構成するには、ギャラリーから管理対象 SaaS アプリの一覧に AirWatch を追加する必要があります。

**ギャラリーから AirWatch を追加するには、次の手順を実行します。**

1. **[Azure Portal](https://portal.azure.com)** の左側のナビゲーション ウィンドウで、**[Azure Active Directory]** アイコンをクリックします。

    ![Azure Active Directory のボタン](common/select-azuread.png)

2. **[エンタープライズ アプリケーション]** に移動し、**[すべてのアプリケーション]** オプションを選択します。

    ![[エンタープライズ アプリケーション] ブレード](common/enterprise-applications.png)

3. 新しいアプリケーションを追加するには、ダイアログの上部にある **[新しいアプリケーション]** をクリックします。

    ![[新しいアプリケーション] ボタン](common/add-new-app.png)

4. 検索ボックスに「**AirWatch**」と入力し、結果パネルで **[AirWatch]** を選択し、**[追加]** をクリックしてアプリケーションを追加します。

     ![結果リストの AirWatch](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成とテスト

このセクションでは、**Britta Simon** というテスト ユーザーに基づいて、AirWatch で Azure AD のシングル サインオンを構成し、テストします。
シングル サインオンを機能させるには、Azure AD ユーザーと AirWatch 内の関連ユーザーとの間にリンク関係が確立されている必要があります。

AirWatch で Azure AD のシングル サインオンを構成してテストするには、次の構成要素を完了する必要があります。

1. **[Azure AD シングル サインオンの構成](#configure-azure-ad-single-sign-on)** - ユーザーがこの機能を使用できるようにします。
2. **[AirWatch シングル サインオンの構成](#configure-airwatch-single-sign-on)** - アプリケーション側でシングル サインオン設定を構成します。
3. **[Azure AD のテスト ユーザーの作成](#create-an-azure-ad-test-user)** - Britta Simon で Azure AD のシングル サインオンをテストします。
4. **[AirWatch のテスト ユーザーの作成](#create-airwatch-test-user)** - AirWatch で Britta Simon に対応するユーザーを作成し、Azure AD の Britta Simon にリンクさせます。
5. **[Azure AD テスト ユーザーの割り当て](#assign-the-azure-ad-test-user)** - Britta Simon が Azure AD シングル サインオンを使用できるようにします。
6. **[シングル サインオンのテスト](#test-single-sign-on)** - 構成が機能するかどうかを確認します。

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成

このセクションでは、Azure portal 上で Azure AD のシングル サインオンを有効にします。

AirWatch で Azure AD シングル サインオンを構成するには、次の手順に従います。

1. [Azure portal](https://portal.azure.com/) の **AirWatch** アプリケーション統合ページで、**[シングル サインオン]** を選択します。

    ![シングル サインオン構成のリンク](common/select-sso.png)

2. **[シングル サインオン方式の選択]** ダイアログで、**[SAML/WS-Fed]** モードを選択して、シングル サインオンを有効にします。

    ![シングル サインオン選択モード](common/select-saml-option.png)

3. **[SAML でシングル サインオンをセットアップします]** ページで、**[編集]** アイコンをクリックして **[基本的な SAML 構成]** ダイアログを開きます。

    ![基本的な SAML 構成を編集する](common/edit-urls.png)

4. **[基本的な SAML 構成]** セクションで、次の手順を実行します。

    ![[AirWatch のドメインと URL] のシングル サインオン情報](common/sp-identifier.png)

    a. **[サインオン URL]** ボックスに、次のパターンを使用して URL を入力します。`https://<subdomain>.awmdm.com/AirWatch/Login?gid=companycode`

    b. **[識別子 (エンティティ ID)]** ボックスに、`AirWatch` という値を入力します。

    > [!NOTE]
    > これは実際の値ではありません。 この値を実際のサインオン URL で更新してください。 この値を取得するには、[AirWatch Client サポート チーム](https://www.air-watch.com/company/contact-us/)に問い合わせてください。 Azure portal の **[基本的な SAML 構成]** セクションに示されているパターンを参照することもできます。

5. AirWatch アプリケーションは、特定の形式で構成された SAML アサーションを受け入れます。 このアプリケーションには、次の要求を構成します。 これらの属性の値は、アプリケーション統合ページの **[ユーザー属性]** セクションで管理できます。 **[SAML でシングル サインオンをセットアップします]** ページで、**[編集]** ボタンをクリックして **[ユーザー属性]** ダイアログを開きます。

    ![image](common/edit-attribute.png)

6. **[ユーザー属性]** ダイアログの **[ユーザーの要求]** セクションで、**編集アイコン**を使用して要求を編集するか、**[新しい要求の追加]** を使用して要求を追加することで、上の図のように SAML トークン属性を構成し、次の手順を実行します。

    | Name |  ソース属性|
    |---------------|----------------|
    | UID | user.userprincipalname |
    | | |

    a. **[新しい要求の追加]** をクリックして **[ユーザー要求の管理]** ダイアログを開きます。

    ![image](common/new-save-attribute.png)

    ![image](common/new-attribute-details.png)

    b. **[名前]** ボックスに、その行に対して表示される属性名を入力します。

    c. **[名前空間]** は空白のままにします。

    d. [ソース] として **[属性]** を選択します。

    e. **[ソース属性]** の一覧から、その行に表示される属性値を入力します。

    f. **[OK]** をクリックします。

    g. **[Save]** をクリックします。

7. **[SAML でシングル サインオンをセットアップします]** ページの **[SAML 署名証明書]** セクションで、**[ダウンロード]** をクリックして、要件のとおりに指定したオプションから**フェデレーション メタデータ XML** をダウンロードして、お使いのコンピューターに保存します。

    ![証明書のダウンロードのリンク](common/metadataxml.png)

8. **[AirWatch のセットアップ]** セクションで、要件どおりの適切な URL をコピーします。

    ![構成 URL のコピー](common/copy-configuration-urls.png)

    a. ログイン URL

    b. Azure AD 識別子

    c. ログアウト URL

### <a name="configure-airwatch-single-sign-on"></a>AirWatch シングル サインオンの構成

1. 別の Web ブラウザーのウィンドウで、管理者として AirWatch 企業サイトにログインします。

2. 左側のナビゲーション ウィンドウで、**[Accounts]**、**[Administrators]** の順にクリックします。

   ![管理者](./media/airwatch-tutorial/ic791920.png "Administrators")

3. **[Settings]** メニューを展開し、**[Directory Services]** をクリックします。

   ![設定](./media/airwatch-tutorial/ic791921.png "Settings")

4. **[ユーザー]** タブをクリックし、**[ベース DN]** テキストボックスにドメイン名を入力してから **[保存]** をクリックします。

   ![ユーザー](./media/airwatch-tutorial/ic791922.png "User")

5. **[Server]** タブをクリックします。

   ![サーバー](./media/airwatch-tutorial/ic791923.png "Server")

6. 次の手順に従います。

    ![アップロード](./media/airwatch-tutorial/ic791924.png "Upload")   

    a. **[Directory Type]** として **[None]** を選択します。

    b. **[Use SAML For Authentication]** を選択します。

    c. ダウンロードした証明書をアップロードするには、**[Upload]** をクリックします。

7. **[Request]** セクションで、次の手順に従います。

    ![要求](./media/airwatch-tutorial/ic791925.png "Request")  

    a. **[Request Binding Type]** として **[POST]** を選択します。

    b. Azure portal の **[Airwatch でのシングル サインオンの構成]** ダイアログ ページで、**[ログイン URL]** の値をコピーし、**[ID プロバイダー シングル サインオン URL]** テキスト ボックスに貼り付けます。

    c. **[NameID Format]** として **[Email Address]** を選択します。

    d. **[Save]** をクリックします。

8. **[User]** タブをもう一度クリックします。

    ![ユーザー](./media/airwatch-tutorial/ic791926.png "User")

9. **[Attribute]** セクションで、次の手順に従います。

    ![属性](./media/airwatch-tutorial/ic791927.png "Attribute")

    a. **[オブジェクト識別子]** ボックスに「`http://schemas.microsoft.com/identity/claims/objectidentifier`」と入力します。

    b. **[ユーザー名]** ボックスに「`http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress`」と入力します。

    c. **[表示名]** ボックスに「`http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname`」と入力します。

    d. **[名]** ボックスに「`http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname`」と入力します。

    e. **[姓]** ボックスに「`http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname`」と入力します。

    f. **[電子メール]** ボックスに「`http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress`」と入力します。

    g. **[Save]** をクリックします。

### <a name="create-an-azure-ad-test-user"></a>Azure AD のテスト ユーザーの作成 

このセクションの目的は、Azure Portal で Britta Simon というテスト ユーザーを作成することです。

1. Azure portal の左側のウィンドウで、**[Azure Active Directory]**、**[ユーザー]**、**[すべてのユーザー]** の順に選択します。

    ![[ユーザーとグループ] と [すべてのユーザー] リンク](common/users.png)

2. 画面の上部にある **[新しいユーザー]** を選択します。

    ![[新しいユーザー] ボタン](common/new-user.png)

3. [ユーザーのプロパティ] で、次の手順を実行します。

    ![[ユーザー] ダイアログ ボックス](common/user-properties.png)

    a. **[名前]** フィールドに「**BrittaSimon**」と入力します。
  
    b. **[ユーザー名]** フィールドに「**brittasimon\@yourcompanydomain.extension**」と入力します。  
    たとえば、BrittaSimon@contoso.com のように指定します。

    c. **[パスワードを表示]** チェック ボックスをオンにし、[パスワード] ボックスに表示された値を書き留めます。

    d. **Create** をクリックしてください。

### <a name="assign-the-azure-ad-test-user"></a>Azure AD テスト ユーザーの割り当て

このセクションでは、Britta Simon に AirWatch へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

1. Azure portal 上で **[エンタープライズ アプリケーション]** を選択し、**[すべてのアプリケーション]**、**[AirWatch]** の順に選択します。

    ![[エンタープライズ アプリケーション] ブレード](common/enterprise-applications.png)

2. アプリケーションの一覧で **[AirWatch]** を選択します。

    ![アプリケーション一覧の [AirWatch] リンク](common/all-applications.png)

3. 左側のメニューで **[ユーザーとグループ]** を選びます。

    ![[ユーザーとグループ] リンク](common/users-groups-blade.png)

4. **[ユーザーの追加]** をクリックし、**[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。

    ![[割り当ての追加] ウィンドウ](common/add-assign-user.png)

5. **[ユーザーとグループ]** ダイアログの [ユーザー] の一覧で **[Britta Simon]** を選択し、画面の下部にある **[選択]** ボタンをクリックします。

6. SAML アサーション内に任意のロール値が必要な場合、**[ロールの選択]** ダイアログでユーザーに適したロールを一覧から選択し、画面の下部にある **[選択]** をクリッします。

7. **[割り当ての追加]** ダイアログで、**[割り当て]** ボタンをクリックします。

### <a name="create-airwatch-test-user"></a>AirWatch のテスト ユーザーを作成する

Azure AD ユーザーが AirWatch にログインできるようにするには、そのユーザーを AirWatch にプロビジョニングする必要があります。 AirWatch の場合、プロビジョニングは手動で行います。

**ユーザー プロビジョニングを構成するには、次の手順に従います。**

1. **AirWatch** 企業サイトに管理者としてログインします。

2. 左側のナビゲーション ウィンドウで、**[Accounts]**、**[Users]** の順にクリックします。
  
   ![ユーザー](./media/airwatch-tutorial/ic791929.png "Users")

3. **[Users]** メニューで、**[List View]**、**[Add] \> [Add User]** の順にクリックします。
  
   ![ユーザーの追加](./media/airwatch-tutorial/ic791930.png "Add User")

4. **[Add / Edit User]** ダイアログで、次の手順を実行します。

   ![ユーザーの追加](./media/airwatch-tutorial/ic791931.png "Add User")

   a. 関連するテキスト ボックスに、プロビジョニングする有効な Azure Active Directory アカウントの **[Username]**、**[Password]**、**[Confirm Password]**、**[First Name]**、**[Last Name]**、**[Email Address]** を入力します。

   b. **[Save]** をクリックします。

> [!NOTE]
> 他の AirWatch ユーザー アカウントの作成ツールまたは AirWatch から提供されている API を使用して、AAD ユーザー アカウントをプロビジョニングできます。

### <a name="test-single-sign-on"></a>シングル サインオンのテスト

このセクションでは、アクセス パネルを使用して Azure AD のシングル サインオン構成をテストします。

アクセス パネル上で [AirWatch] タイルをクリックすると、SSO を設定した AirWatch に自動的にサインインします。 アクセス パネルの詳細については、[アクセス パネルの概要](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)に関する記事を参照してください。

## <a name="additional-resources"></a>その他のリソース

- [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Azure Active Directory の条件付きアクセスとは](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
