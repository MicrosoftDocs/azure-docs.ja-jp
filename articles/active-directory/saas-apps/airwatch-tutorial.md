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
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 07/11/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9f0a684c6a38ba3a95438941f668b36b23d278df
ms.sourcegitcommit: 920ad23613a9504212aac2bfbd24a7c3de15d549
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/15/2019
ms.locfileid: "68227731"
---
# <a name="tutorial-integrate-airwatch-with-azure-active-directory"></a>チュートリアル:AirWatch を Azure Active Directory と統合する

このチュートリアルでは、AirWatch と Azure Active Directory (Azure AD) を統合する方法について説明します。 AirWatch を Azure AD と統合すると、次のことが可能になります。

* AirWatch にアクセスする Azure AD ユーザーを制御できます。
* ユーザーが自分の Azure AD アカウントを使用して AirWatch に自動的にサインインするように設定できます。
* 1 つの中央サイト (Azure Portal) で自分のアカウントを管理できます。

SaaS アプリと Azure AD の統合の詳細については、「[Azure Active Directory でのアプリケーションへのシングル サインオン](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)」を参照してください。

## <a name="prerequisites"></a>前提条件

開始するには、次が必要です。

* Azure AD サブスクリプション。 サブスクリプションをお持ちでない場合は、[ここ](https://azure.microsoft.com/pricing/free-trial/)から 1 か月間の無料試用版を入手できます。
* AirWatch でのシングル サインオン (SSO) が有効なサブスクリプション

## <a name="scenario-description"></a>シナリオの説明

このチュートリアルでは、テスト環境で Azure AD の SSO を構成してテストします。 AirWatch では、**SP** によって開始される SSO がサポートされます。

## <a name="adding-airwatch-from-the-gallery"></a>ギャラリーから AirWatch を追加する

Azure AD への AirWatch の統合を構成するには、ギャラリーから管理対象 SaaS アプリの一覧に AirWatch を追加する必要があります。

1. 職場または学校アカウントか、個人の Microsoft アカウントを使用して、[Azure portal](https://portal.azure.com) にサインインします。
1. 左のナビゲーション ウィンドウで **[Azure Active Directory]** サービスを選択します。
1. **[エンタープライズ アプリケーション]** に移動し、 **[すべてのアプリケーション]** を選択します。
1. 新しいアプリケーションを追加するには、 **[新しいアプリケーション]** を選択します。
1. **[ギャラリーから追加する]** セクションで、検索ボックスに、「**AirWatch**」と入力します。
1. 結果のパネルから **[AirWatch]** を選択し、アプリを追加します。 お使いのテナントにアプリが追加されるのを数秒待機します。

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成とテスト

**B.Simon** というテスト ユーザーを使用して、AirWatch に対する Azure AD SSO を構成してテストします。 SSO が機能するためには、Azure AD ユーザーと AirWatch の関連ユーザーとの間にリンク関係を確立する必要があります。

AirWatch に対する Azure AD SSO を構成してテストするには、次の構成要素を完了します。

1. **[Azure AD SSO の構成](#configure-azure-ad-sso)** - ユーザーがこの機能を使用できるようにします。
2. **[AirWatch SSO の構成](#configure-airwatch-sso)** - アプリケーション側でシングル サインオン設定を構成します。
3. **[Azure AD のテスト ユーザーの作成](#create-an-azure-ad-test-user)** - Britta Simon で Azure AD のシングル サインオンをテストします。
4. **[AirWatch のテスト ユーザーの作成](#create-airwatch-test-user)** - AirWatch で Britta Simon に対応するユーザーを作成し、Azure AD の Britta Simon にリンクさせます。
5. **[Azure AD テスト ユーザーの割り当て](#assign-the-azure-ad-test-user)** - Britta Simon が Azure AD シングル サインオンを使用できるようにします。
6. **[SSO のテスト](#test-sso)** - 構成が機能するかどうかを確認します。

### <a name="configure-azure-ad-sso"></a>Azure AD SSO の構成

これらの手順に従って、Azure portal で Azure AD SSO を有効にします。

1. [Azure portal](https://portal.azure.com/) の **AirWatch** アプリケーション統合ページで、 **[管理]** セクションを探して、 **[シングル サインオン]** を選択します。
1. **[シングル サインオン方式の選択]** ページで、 **[SAML]** を選択します。
1. **[SAML でシングル サインオンをセットアップします]** ページで、 **[基本的な SAML 構成]** の編集/ペン アイコンをクリックして設定を編集します。

   ![基本的な SAML 構成を編集する](common/edit-urls.png)

1. **[基本的な SAML 構成]** ページで、次のフィールドの値を入力します。

    1. **[サインオン URL]** ボックスに、次のパターンを使用して URL を入力します。`https://<subdomain>.awmdm.com/AirWatch/Login?gid=companycode`

    1. **[識別子 (エンティティ ID)]** ボックスに、`AirWatch` という値を入力します。

    > [!NOTE]
    > これは実際の値ではありません。 この値を実際のサインオン URL で更新してください。 この値を取得するには、[AirWatch Client サポート チーム](https://www.air-watch.com/company/contact-us/)に問い合わせてください。 Azure portal の **[基本的な SAML 構成]** セクションに示されているパターンを参照することもできます。

1. AirWatch アプリケーションは、特定の形式で構成された SAML アサーションを受け入れます。 このアプリケーションには、次の要求を構成します。 これらの属性の値は、アプリケーション統合ページの **[ユーザー属性]** セクションで管理できます。 **[SAML でシングル サインオンをセットアップします]** ページで、 **[編集]** ボタンをクリックして **[ユーザー属性]** ダイアログを開きます。

    ![image](common/edit-attribute.png)

1. **[ユーザー属性]** ダイアログの **[ユーザーの要求]** セクションで、**編集アイコン**を使用して要求を編集するか、 **[新しい要求の追加]** を使用して要求を追加することで、上の図のように SAML トークン属性を構成し、次の手順を実行します。

    | EnableAdfsAuthentication |  ソース属性|
    |---------------|----------------|
    | UID | user.userprincipalname |
    | | |

    a. **[新しい要求の追加]** をクリックして **[ユーザー要求の管理]** ダイアログを開きます。

    b. **[名前]** ボックスに、その行に対して表示される属性名を入力します。

    c. **[名前空間]** は空白のままにします。

    d. [ソース] として **[属性]** を選択します。

    e. **[ソース属性]** の一覧から、その行に表示される属性値を入力します。

    f. **[OK]** をクリックします。

    g. **[Save]** をクリックします。

1. **[SAML でシングル サインオンをセットアップします]** ページの **[SAML 署名証明書]** セクションで、 **[フェデレーション メタデータ XML]** を探して **[ダウンロード]** を選択し、メタデータ XML をダウンロードしてコンピューターに保存します。

   ![証明書のダウンロードのリンク](common/metadataxml.png)

1. **[AirWatch の設定]** セクションで、要件に基づいて適切な URL をコピーします。

   ![構成 URL のコピー](common/copy-configuration-urls.png)

### <a name="configure-airwatch-sso"></a>AirWatch SSO の構成

1. 別の Web ブラウザーのウィンドウで、管理者として AirWatch 企業サイトにサインインします。

1. 設定ページで、 **[Settings]\(設定\) > [Enterprise Integration]\(エンタープライズ統合\) > [Directory Services]\(ディレクトリ サービス\)** を選択します。

   ![設定](./media/airwatch-tutorial/ic791921.png "Settings")

1. **[ユーザー]** タブをクリックし、 **[ベース DN]** テキストボックスにドメイン名を入力してから **[保存]** をクリックします。

   ![ユーザー](./media/airwatch-tutorial/ic791922.png "User")

1. **[Server]** タブをクリックします。

   ![サーバー](./media/airwatch-tutorial/ic791923.png "Server")

1. **[LDAP]** セクションで次の手順を実行します。

    ![アップロード](./media/airwatch-tutorial/ic791924.png "LDAP")   

    a. **[Directory Type]** として **[None]** を選択します。

    b. **[Use SAML For Authentication]** を選択します。

1. **[SAML 2.0]** セクションで、ダウンロードした証明書をアップロードするには、 **[Upload]\(アップロード\)** をクリックします。

    ![アップロード](./media/airwatch-tutorial/ic791932.png "Upload")

1. **[Request]** セクションで、次の手順に従います。

    ![要求](./media/airwatch-tutorial/ic791925.png "Request")  

    a. **[Request Binding Type]** として **[POST]** を選択します。

    b. Azure portal の **[AirWatch でのシングル サインオンの構成]** ダイアログ ページで、 **[ログイン URL]** の値をコピーし、 **[ID プロバイダー シングル サインオン URL]** テキスト ボックスに貼り付けます。

    c. **[NameID Format]** として **[Email Address]** を選択します。

    d. **[Authentication Request Security]\(認証要求のセキュリティ\)** として **[None]\(なし\)** を選択します。

    e. **[Save]** をクリックします。

1. **[User]** タブをもう一度クリックします。

    ![ユーザー](./media/airwatch-tutorial/ic791926.png "User")

1. **[Attribute]** セクションで、次の手順に従います。

    ![属性](./media/airwatch-tutorial/ic791927.png "Attribute")

    a. **[オブジェクト識別子]** ボックスに「`http://schemas.microsoft.com/identity/claims/objectidentifier`」と入力します。

    b. **[ユーザー名]** ボックスに「`http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress`」と入力します。

    c. **[表示名]** ボックスに「`http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname`」と入力します。

    d. **[名]** ボックスに「`http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname`」と入力します。

    e. **[姓]** ボックスに「`http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname`」と入力します。

    f. **[電子メール]** ボックスに「`http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress`」と入力します。

    g. **[Save]** をクリックします。

### <a name="create-an-azure-ad-test-user"></a>Azure AD のテスト ユーザーの作成

このセクションでは、Azure portal 内で B.Simon というテスト ユーザーを作成します。

1. Azure portal の左側のウィンドウから、 **[Azure Active Directory]** 、 **[ユーザー]** 、 **[すべてのユーザー]** の順に選択します。
1. 画面の上部にある **[新しいユーザー]** を選択します。
1. **[ユーザー]** プロパティで、以下の手順を実行します。
   1. **[名前]** フィールドに「`B.Simon`」と入力します。  
   1. **[ユーザー名]** フィールドに「username@companydomain.extension」と入力します。 たとえば、「 `B.Simon@contoso.com` 」のように入力します。
   1. **[パスワードを表示]** チェック ボックスをオンにし、 **[パスワード]** ボックスに表示された値を書き留めます。
   1. **Create** をクリックしてください。

### <a name="assign-the-azure-ad-test-user"></a>Azure AD テスト ユーザーの割り当て

このセクションでは、B.Simon に AirWatch へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

1. Azure portal で **[エンタープライズ アプリケーション]** を選択し、 **[すべてのアプリケーション]** を選択します。
1. アプリケーションの一覧で **[AirWatch]** を選択します。
1. アプリの概要ページで、 **[管理]** セクションを見つけて、 **[ユーザーとグループ]** を選択します。

   ![[ユーザーとグループ] リンク](common/users-groups-blade.png)

1. **[ユーザーの追加]** を選択し、 **[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。

    ![[ユーザーの追加] リンク](common/add-assign-user.png)

1. **[ユーザーとグループ]** ダイアログの [ユーザー] の一覧から **[B.Simon]** を選択し、画面の下部にある **[選択]** ボタンをクリックします。
1. SAML アサーション内に任意のロール値が必要な場合、 **[ロールの選択]** ダイアログでユーザーに適したロールを一覧から選択し、画面の下部にある **[選択]** をクリックします。
1. **[割り当ての追加]** ダイアログで、 **[割り当て]** をクリックします。

### <a name="create-airwatch-test-user"></a>AirWatch のテスト ユーザーを作成する

Azure AD ユーザーが AirWatch にサインインできるようにするには、そのユーザーを AirWatch にプロビジョニングする必要があります。 AirWatch の場合、プロビジョニングは手動で行います。

**ユーザー プロビジョニングを構成するには、次の手順に従います。**

1. **AirWatch** 企業サイトに管理者としてサインインします。

2. 左側のナビゲーション ウィンドウで、 **[Accounts]** 、 **[Users]** の順にクリックします。
  
   ![ユーザー](./media/airwatch-tutorial/ic791929.png "Users")

3. **Users\(ユーザー\)** メニューで、**List View\(リスト ビュー\)** 、**Add\(追加\) > Add User\(ユーザーの追加\)** の順にクリックします。
  
   ![ユーザーの追加](./media/airwatch-tutorial/ic791930.png "Add User")

4. **[Add / Edit User]** ダイアログで、次の手順を実行します。

   ![ユーザーの追加](./media/airwatch-tutorial/ic791931.png "Add User")

   a. 関連するテキスト ボックスに、プロビジョニングする有効な Azure Active Directory アカウントの **[Username]** 、 **[Password]** 、 **[Confirm Password]** 、 **[First Name]** 、 **[Last Name]** 、 **[Email Address]** を入力します。

   b. **[Save]** をクリックします。

> [!NOTE]
> 他の AirWatch ユーザー アカウントの作成ツールまたは AirWatch から提供されている API を使用して、AAD ユーザー アカウントをプロビジョニングできます。

### <a name="test-sso"></a>SSO のテスト

アクセス パネル上で [AirWatch] タイルを選択すると、SSO を設定した AirWatch に自動的にサインインします。 アクセス パネルの詳細については、[アクセス パネルの概要](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)に関する記事を参照してください。

## <a name="additional-resources"></a>その他のリソース

- [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Azure Active Directory の条件付きアクセスとは](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
