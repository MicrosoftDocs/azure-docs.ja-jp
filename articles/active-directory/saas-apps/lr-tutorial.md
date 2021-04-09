---
title: 'チュートリアル: Azure Active Directory と LoginRadius の統合 | Microsoft Docs'
description: Azure Active Directory と LoginRadius の間でシングル サインオンを構成する方法について説明します。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 04/14/2019
ms.author: jeedes
ms.openlocfilehash: 832c08123904b9fb889231faa86c1308704a2581
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "97606421"
---
# <a name="tutorial-azure-active-directory-integration-with-loginradius"></a>チュートリアル: Azure Active Directory と LoginRadius の統合

このチュートリアルでは、LoginRadius と Azure Active Directory (Azure AD) を統合する方法について説明します。

LoginRadius と Azure AD の統合には、次の利点があります。

* LoginRadius にアクセスする Azure AD ユーザーを制御できます。
* ユーザーが自分の Azure AD アカウントを使用して LoginRadius に自動的にサインイン (シングル サインオン) できるようにすることができます。
* 1 つの中央サイト (Azure Portal) でアカウントを管理できます。

SaaS アプリと Azure AD の統合の詳細については、「 [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)」を参照してください。

Azure サブスクリプションをお持ちでない場合は、開始する前に[無料アカウントを作成](https://azure.microsoft.com/free/)してください。

## <a name="prerequisites"></a>前提条件

LoginRadius と Azure AD の統合を構成するには、次のものが必要です。

* Azure AD サブスクリプション。 Azure AD の環境がない場合は、[無料アカウント](https://azure.microsoft.com/free/)を取得できます
* LoginRadius でのシングル サインオンが有効なサブスクリプション

## <a name="scenario-description"></a>シナリオの説明

このチュートリアルでは、テスト環境で Azure AD のシングル サインオンを構成してテストします。

* LoginRadius では、**SP** によって開始される SSO がサポートされます

## <a name="adding-loginradius-from-the-gallery"></a>ギャラリーからの LoginRadius の追加

Azure AD への LoginRadius の統合を構成するには、ギャラリーから管理対象 SaaS アプリの一覧に LoginRadius を追加する必要があります。

**ギャラリーから LoginRadius を追加するには、次の手順に従います。**

1. **[Azure portal](https://portal.azure.com)** の左側のナビゲーション パネルで、 **[Azure Active Directory]** アイコンを選択します。

    ![Azure Active Directory のボタン](common/select-azuread.png)

2. **[エンタープライズ アプリケーション]** に移動し、 **[すべてのアプリケーション]** オプションを選択します。

    ![[エンタープライズ アプリケーション] ブレード](common/enterprise-applications.png)

3. 新しいアプリケーションを追加するには、 **[新しいアプリケーション]** ボタンを選択します。

    ![[新しいアプリケーション] ボタン](common/add-new-app.png)

4. 検索ボックスに「**LoginRadius**」と入力し、結果パネルで **[LoginRadius]** を選択し、 **[追加]** ボタンをクリックして、アプリケーションを追加します。

    ![結果一覧の LoginRadius](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成とテスト

このセクションでは、**Britta Simon** というテスト ユーザーに基づいて、LoginRadius で Azure AD のシングル サインオンを構成し、テストします。
シングル サインオンを機能させるには、Azure AD ユーザーと LoginRadius 内の関連ユーザー間にリンク関係が確立されている必要があります。

LoginRadius で Azure AD のシングル サインオンを構成してテストするには、次の構成要素を完了する必要があります。

1. **[Azure AD シングル サインオンの構成](#configure-azure-ad-single-sign-on)** - ユーザーがこの機能を使用できるようにします。
2. **[LoginRadius シングル サインオンの構成](#configure-loginradius-single-sign-on)** - アプリケーション側でシングル サインオン設定を構成します。
3. **[Azure AD のテスト ユーザーの作成](#create-an-azure-ad-test-user)** - Britta Simon で Azure AD のシングル サインオンをテストします。
4. **[Azure AD テスト ユーザーの割り当て](#assign-the-azure-ad-test-user)** - Britta Simon が Azure AD シングル サインオンを使用できるようにします。
5. **[LoginRadius のテスト ユーザーの作成](#create-loginradius-test-user)** - LoginRadius で Britta Simon に対応するユーザーを作成し、Azure AD のユーザーの表現にリンクさせます。
6. **[シングル サインオンのテスト](#test-single-sign-on)** - 構成が機能するかどうかを確認します。

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成

このセクションでは、Azure portal 上で Azure AD のシングル サインオンを有効にします。

LoginRadius で Azure AD シングル サインオンを構成するには、次の手順に従います。

1. [Azure portal](https://portal.azure.com/) の **LoginRadius** アプリケーション統合ページで、 **[シングル サインオン]** を選択します。

    ![シングル サインオン構成のリンク](common/select-sso.png)

2. **[シングル サインオン方式の選択]** ペインで、 **[SAML/WS-Fed]** モードを選択して、シングル サインオンを有効にします。

    ![シングル サインオン選択モード](common/select-saml-option.png)

3. **[SAML でシングル サインオンをセットアップします]** ページで、**編集** アイコンを選択して **[基本的な SAML 構成]** ペインを開きます。

    ![基本的な SAML 構成を編集する](common/edit-urls.png)

4. **[基本的な SAML 構成]** セクションで、次の作業を行います。

   ![[LoginRadius Domain and URLs]\(LoginRadius のドメインと URL\) のシングル サインオン情報](common/sp-identifier.png)

   1. **[サインオン URL]** ボックスに URL (`https://secure.loginradius.com/login`) を入力します。

   1. **[識別子 (エンティティ ID)]** ボックスに URL (`https://lr.hub.loginradius.com/`) を入力します。

   1. **[応答 URL (Assertion Consumer Service URL)]** ボックスに、LoginRadius の ACS URL (`https://lr.hub.loginradius.com/saml/serviceprovider/AdfsACS.aspx`) を入力します。 

5. **[SAML でシングル サインオンをセットアップします]** ページの **[SAML 署名証明書]** セクションで、 **[ダウンロード]** を選択して、要件のとおりに指定したオプションから **フェデレーション メタデータ XML** をダウンロードして、お使いのコンピューターに保存します。

    ![証明書のダウンロードのリンク](common/metadataxml.png)

6. **[LoginRadius のセットアップ]** セクションで、要件に従って適切な URL をコピーします。

   ![構成 URL のコピー](common/copy-configuration-urls.png)

   - ログイン URL

   - Azure AD 識別子

   - ログアウト URL

## <a name="configure-loginradius-single-sign-on"></a>LoginRadius シングル サインオンの構成

このセクションでは、LoginRadius 管理コンソール上で Azure AD のシングル サインオンを有効にします。

1. LoginRadius [管理コンソール](https://adminconsole.loginradius.com/login) アカウントにログインします。

2. [LoginRadius 管理コンソール](https://secure.loginradius.com/account/team)の **[Team Management]\(チーム管理\)** セクションに移動します。

3. **[Single Sign-On]\(シングル サインオン\)** タブを選択し、 **[Azure AD]** を選択します。

   ![LoginRadius の [Team Management]\(チーム管理\) コンソールにあるシングル サインオン メニューを示すスクリーンショット](./media/loginradius-tutorial/azure-ad.png)
4. Azure AD のセットアップ ページで、次の手順を実行します。

   ![LoginRadius の [Team Management]\(チーム管理\) コンソールにある Azure Active Directory の構成を示すスクリーンショット](./media/loginradius-tutorial/single-sign-on.png)

    1. **[ID Provider Location]\(ID プロバイダーの場所\)** に、Azure AD アカウントから取得したサインオン エンドポイントを入力します。

    1. **[ID Provider Logout URL]\(ID プロバイダー ログアウト URL\)** に、Azure AD アカウントから取得したサインアウト エンドポイントを入力します。
 
    1. **[ID Provider Certificate]\(ID プロバイダー証明書\)** に、Azure AD アカウントから取得した Azure AD の証明書を入力します。 ヘッダーとフッターを付けて証明書の値を入力します。 例: `-----BEGIN CERTIFICATE-----<certifciate value>-----END CERTIFICATE-----`

    1. **[Service Provider Certificate]\(サービス プロバイダー証明書\)** と **[Server Provider Certificate Key]\(サーバー プロバイダー証明書キー\)** に、自分の証明書とキーを入力します。 

       自己署名証明書は、コマンド ラインから次のコマンドを実行して作成できます (Linux または Mac)。

       - SP の証明書キーを取得するコマンド: `openssl genrsa -out lr.hub.loginradius.com.key 2048`

       - SP の証明書を取得するコマンド: `openssl req -new -x509 -key lr.hub.loginradius.com.key -out lr.hub.loginradius.com.cert -days 3650 -subj /CN=lr.hub.loginradius.com`

       > [!NOTE]
       > 証明書と証明書キーの値は、必ずヘッダーとフッターを付けて入力してください。
       > - 証明書の値の形式 (入力例): `-----BEGIN CERTIFICATE-----<certifciate value>-----END CERTIFICATE-----`
       > - 証明書キーの値の形式 (入力例): `-----BEGIN RSA PRIVATE KEY-----<certifciate key value>-----END RSA PRIVATE KEY-----`

5. **[Data Mapping]\(データ マッピング\)** セクションでフィールド (SP フィールド) を選択し、対応する Azure AD フィールド (IdP フィールド) を入力します。

    次の表は、Azure AD に使用されるいくつかのフィールド名の一覧です。

    | フィールド    | プロファイル キー                                                          |
    | --------- | -------------------------------------------------------------------- |
    | Email     | `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress` |
    | FirstName | `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname`    |
    | LastName  | `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname`      |

    > [!NOTE]
    > **Email** フィールドのマッピングは必須です。 **FirstName** フィールドと **LastName** フィールドのマッピングは省略可能です。

### <a name="create-an-azure-ad-test-user"></a>Azure AD のテスト ユーザーの作成 

このセクションの目的は、Azure Portal で Britta Simon というテスト ユーザーを作成することです。

1. Azure portal の左側のウィンドウで、 **[Azure Active Directory]** 、 **[ユーザー]** 、 **[すべてのユーザー]** の順に選択します。

    ![[ユーザーとグループ] と [すべてのユーザー] リンク](common/users.png)

2. 画面の上部にある **[新しいユーザー]** を選択します。

    ![[新しいユーザー] ボタン](common/new-user.png)

3. **[ユーザー]** のプロパティで、次の手順を実行します。

   ![[ユーザー] ダイアログ ボックス](common/user-properties.png)

   1. **[名前]** フィールドに「**BrittaSimon**」と入力します。
  
   1. **[ユーザー名]** フィールドに「`brittasimon@yourcompanydomain.extension`」と入力します。 たとえば、「 BrittaSimon@contoso.com 」のように入力します。

   1. **[パスワードを表示]** チェック ボックスをオンにし、 **[パスワード]** ボックスに表示された値を書き留めます。

   1. **［作成］** を選択します

### <a name="assign-the-azure-ad-test-user"></a>Azure AD テスト ユーザーの割り当て

このセクションでは、Britta Simon に LoginRadius へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

1. Azure portal で **[エンタープライズ アプリケーション]** を選択し、**[すべてのアプリケーション]**、**[LoginRadius]** の順に選択します。

    ![[エンタープライズ アプリケーション] ブレード](common/enterprise-applications.png)

2. アプリケーションの一覧で **[LoginRadius]** を選択します。

    ![[アプリケーション] リストの [LoginRadius] リンク](common/all-applications.png)

3. 左側のメニューで **[ユーザーとグループ]** を選びます。

    ![[ユーザーとグループ] リンク](common/users-groups-blade.png)

4. **[ユーザーの追加]** をクリックし、 **[割り当ての追加]** ペインで **[ユーザーとグループ]** を選択します。

    ![[割り当ての追加] ウィンドウ](common/add-assign-user.png)

5. **[ユーザーとグループ]** ペインの **[ユーザー]** の一覧から **[Britta Simon]** を選択し、画面の下部にある **[選択]** ボタンを選択します。

6. SAML アサーションでロール値が必要な場合は、 **[ロールの選択]** ペインで、一覧からユーザーに適したロールを選択します。 次に、画面の下部にある **[選択]** ボタンを選択します。

7. **[割り当ての追加]** ウィンドウで、 **[割り当て]** を選択します。

### <a name="create-loginradius-test-user"></a>LoginRadius のテスト ユーザーの作成

1. LoginRadius [管理コンソール](https://adminconsole.loginradius.com/login) アカウントにログインします。

2. LoginRadius 管理コンソールのチーム管理セクションに移動します。

   ![LoginRadius 管理コンソールを示すスクリーンショット](./media/loginradius-tutorial/team-management.png)
3. サイド メニューの **[Add Team Member]\(チーム メンバーの追加\)** を選択してフォームを開きます。 

4. **[Add Team Member]\(チーム メンバーの追加\)** フォームで、LoginRadius サイトに Britta Simon というユーザーを作成します。このユーザーの詳細を入力し、必要なアクセス許可を割り当ててください。 ロールに基づくアクセス許可の詳細については、LoginRadius の「[チーム メンバーの管理](https://www.loginradius.com/docs/api/v2/admin-console/team-management/manage-team-members#roleaccesspermissions0)」ドキュメントの「[ロールのアクセス権](https://www.loginradius.com/docs/api/v2/admin-console/team-management/manage-team-members#roleaccesspermissions0)」セクションを参照してください。 シングル サインオンを使用する前に、ユーザーを作成し、有効化する必要があります。

### <a name="test-single-sign-on"></a>シングル サインオンのテスト 

このセクションでは、アクセス パネルを使用して Azure AD のシングル サインオン構成をテストします。

1. ブラウザーで https://accounts.loginradius.com/auth.aspx に移動し、 **[Fed SSO log in]\(フェデレーション SSO ログイン\)** を選択します。
2. ご利用の LoginRadius アプリの名前を入力し、 **[Login]\(ログイン\)** を選択します。
3. Azure AD アカウントへのサインインを求めるポップアップが表示されます。
4. 認証後、ポップアップが閉じて LoginRadius 管理コンソールにログインされます。

## <a name="additional-resources"></a>その他のリソース

- [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](./tutorial-list.md)

- [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)

- [Azure Active Directory の条件付きアクセスとは](../conditional-access/overview.md)
