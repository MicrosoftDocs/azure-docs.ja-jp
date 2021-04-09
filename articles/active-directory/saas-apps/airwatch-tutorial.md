---
title: チュートリアル:Azure Active Directory と AirWatch の統合 | Microsoft Docs
description: Azure Active Directory と AirWatch の間でシングル サインオンを構成する方法について説明します。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/20/2021
ms.author: jeedes
ms.openlocfilehash: 4955062e6f0d0c231d09964c985df12284e3733c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "101653339"
---
# <a name="tutorial-integrate-airwatch-with-azure-active-directory"></a>チュートリアル:AirWatch を Azure Active Directory と統合する

このチュートリアルでは、AirWatch と Azure Active Directory (Azure AD) を統合する方法について説明します。 AirWatch を Azure AD と統合すると、次のことが可能になります。

* AirWatch にアクセスする Azure AD ユーザーを制御できます。
* ユーザーが自分の Azure AD アカウントを使用して AirWatch に自動的にサインインするように設定できます。
* 1 つの中央サイト (Azure Portal) で自分のアカウントを管理します。

## <a name="prerequisites"></a>前提条件

開始するには、次が必要です。
 
* Azure AD サブスクリプション。 サブスクリプションがない場合は、[無料アカウント](https://azure.microsoft.com/free/)を取得できます。
* AirWatch でのシングル サインオン (SSO) が有効なサブスクリプション。

## <a name="scenario-description"></a>シナリオの説明

このチュートリアルでは、テスト環境で Azure AD の SSO を構成してテストします。 

* AirWatch では、**SP** によって開始される SSO がサポートされます。

## <a name="add-airwatch-from-the-gallery"></a>ギャラリーからの AirWatch の追加

Azure AD への AirWatch の統合を構成するには、ギャラリーから管理対象 SaaS アプリの一覧に AirWatch を追加する必要があります。

1. 職場または学校アカウントか、個人の Microsoft アカウントを使用して、Azure portal にサインインします。
1. 左のナビゲーション ウィンドウで **[Azure Active Directory]** サービスを選択します。
1. **[エンタープライズ アプリケーション]** に移動し、 **[すべてのアプリケーション]** を選択します。
1. 新しいアプリケーションを追加するには、 **[新しいアプリケーション]** を選択します。
1. **[ギャラリーから追加する]** セクションで、検索ボックスに、「**AirWatch**」と入力します。
1. 結果のパネルから **[AirWatch]** を選択し、アプリを追加します。 お使いのテナントにアプリが追加されるのを数秒待機します。

## <a name="configure-and-test-azure-ad-sso-for-airwatch"></a>AirWatch の Azure AD SSO の構成とテスト

**B.Simon** というテスト ユーザーを使用して、AirWatch に対する Azure AD SSO を構成してテストします。 SSO が機能するためには、Azure AD ユーザーと AirWatch の関連ユーザーとの間にリンク関係を確立する必要があります。

AirWatch に対して Azure AD SSO を構成してテストするには、次の手順を行います。

1. **[Azure AD SSO の構成](#configure-azure-ad-sso)** - ユーザーがこの機能を使用できるようにします。
    1. **[Azure AD のテスト ユーザーの作成](#create-an-azure-ad-test-user)** - B.Simon で Azure AD のシングル サインオンをテストします。
    1. **[Azure AD テスト ユーザーの割り当て](#assign-the-azure-ad-test-user)** - B.Simon が Azure AD シングル サインオンを使用できるようにします。
1. **[AirWatch SSO の構成](#configure-airwatch-sso)** - アプリケーション側でシングル サインオン設定を構成します。
    1. **[AirWatch のテスト ユーザーの作成](#create-airwatch-test-user)** - AirWatch で Britta Simon に対応するユーザーを作成し、Azure AD の B.Simon にリンクさせます。
1. **[SSO のテスト](#test-sso)** - 構成が機能するかどうかを確認します。

### <a name="configure-azure-ad-sso"></a>Azure AD SSO の構成

これらの手順に従って、Azure portal で Azure AD SSO を有効にします。

1. Azure portal の **AirWatch** アプリケーション統合ページで、 **[管理]** セクションを見つけて、 **[シングル サインオン]** を選択します。
1. **[シングル サインオン方式の選択]** ページで、 **[SAML]** を選択します。
1. **[SAML によるシングル サインオンのセットアップ]** ページで、 **[基本的な SAML 構成]** の鉛筆アイコンをクリックして設定を編集します。

   ![基本的な SAML 構成を編集する](common/edit-urls.png)

1. **[基本的な SAML 構成]** ページで、次のフィールドの値を入力します。

    1. **[サインオン URL]** ボックスに、次のパターンを使用して URL を入力します。`https://<subdomain>.awmdm.com/AirWatch/Login?gid=companycode`

    1. **[識別子 (エンティティ ID)]** ボックスに、`AirWatch` という値を入力します。

    > [!NOTE]
    > これは実際の値ではありません。 この値を実際のサインオン URL で更新してください。 この値を取得するには、[AirWatch Client サポート チーム](https://www.air-watch.com/company/contact-us/)に問い合わせてください。 Azure portal の **[基本的な SAML 構成]** セクションに示されているパターンを参照することもできます。

1. AirWatch アプリケーションは、特定の形式で構成された SAML アサーションを受け入れます。 このアプリケーションには、次の要求を構成します。 これらの属性の値は、アプリケーション統合ページの **[ユーザー属性]** セクションで管理できます。 **[SAML でシングル サインオンをセットアップします]** ページで、 **[編集]** ボタンをクリックして **[ユーザー属性]** ダイアログを開きます。

    ![image](common/edit-attribute.png)

1. **[ユーザー属性]** ダイアログの **[ユーザーの要求]** セクションで、**編集アイコン** を使用して要求を編集するか、 **[新しい要求の追加]** を使用して要求を追加することで、上の図のように SAML トークン属性を構成し、次の手順を実行します。

    | 名前 |  ソース属性|
    |---------------|----------------|
    | UID | user.userprincipalname |
    | | |

    a. **[新しい要求の追加]** をクリックして **[ユーザー要求の管理]** ダイアログを開きます。

    b. **[名前]** ボックスに、その行に対して表示される属性名を入力します。

    c. **[名前空間]** は空白のままにします。

    d. [ソース] として **[属性]** を選択します。

    e. **[ソース属性]** の一覧から、その行に表示される属性値を入力します。

    f. **[OK]** をクリックします。

    g. **[保存]** をクリックします。

1. **[SAML でシングル サインオンをセットアップします]** ページの **[SAML 署名証明書]** セクションで、 **[フェデレーション メタデータ XML]** を探して **[ダウンロード]** を選択し、メタデータ XML をダウンロードしてコンピューターに保存します。

   ![証明書のダウンロードのリンク](common/metadataxml.png)

1. **[AirWatch の設定]** セクションで、要件に基づいて適切な URL をコピーします。

   ![構成 URL のコピー](common/copy-configuration-urls.png)

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
1. **[ユーザーの追加]** を選択し、 **[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。
1. **[ユーザーとグループ]** ダイアログの [ユーザー] の一覧から **[B.Simon]** を選択し、画面の下部にある **[選択]** ボタンをクリックします。
1. ユーザーにロールが割り当てられることが想定される場合は、 **[ロールの選択]** ドロップダウンからそれを選択できます。 このアプリに対してロールが設定されていない場合は、[既定のアクセス] ロールが選択されていることを確認します。
1. **[割り当ての追加]** ダイアログで、 **[割り当て]** をクリックします。

### <a name="configure-airwatch-sso"></a>AirWatch SSO の構成

1. 別の Web ブラウザーのウィンドウで、管理者として AirWatch 企業サイトにサインインします。

1. 設定ページで、 **[Settings]\(設定\) > [Enterprise Integration]\(エンタープライズ統合\) > [Directory Services]\(ディレクトリ サービス\)** を選択します。

   ![設定](./media/airwatch-tutorial/services.png "設定")

1. **[ユーザー]** タブをクリックし、 **[ベース DN]** テキストボックスにドメイン名を入力してから **[保存]** をクリックします。

   ![[ベース DN] テキスト ボックスが強調表示されているスクリーンショット。](./media/airwatch-tutorial/user.png "User")

1. **[Server]** タブをクリックします。

   ![[サーバー]](./media/airwatch-tutorial/directory.png "サーバー")

1. **[LDAP]** セクションで次の手順を実行します。

    ![[LDAP] セクションに加える必要のある変更点を示すスクリーンショット。](./media/airwatch-tutorial/ldap.png "LDAP")   

    a. **[Directory Type]** として **[None]** を選択します。

    b. **[Use SAML For Authentication]** を選択します。

1. **[SAML 2.0]** セクションで、ダウンロードした証明書をアップロードするには、 **[Upload]\(アップロード\)** をクリックします。

    ![アップロード](./media/airwatch-tutorial/uploads.png "アップロード")

1. **[Request]** セクションで、次の手順に従います。

    ![[Request]\(要求\) セクション](./media/airwatch-tutorial/request.png "Request")  

    a. **[Request Binding Type]** として **[POST]** を選択します。

    b. Azure portal の **[AirWatch でのシングル サインオンの構成]** ダイアログ ページで、 **[ログイン URL]** の値をコピーし、 **[ID プロバイダー シングル サインオン URL]** テキスト ボックスに貼り付けます。

    c. **[NameID Format]** として **[Email Address]** を選択します。

    d. **[Authentication Request Security]\(認証要求のセキュリティ\)** として **[None]\(なし\)** を選択します。

    e. **[保存]** をクリックします。

1. **[User]** タブをもう一度クリックします。

    ![User](./media/airwatch-tutorial/users.png "User")

1. **[Attribute]** セクションで、次の手順に従います。

    ![属性](./media/airwatch-tutorial/attributes.png "属性")

    a. **[オブジェクト識別子]** ボックスに「`http://schemas.microsoft.com/identity/claims/objectidentifier`」と入力します。

    b. **[ユーザー名]** ボックスに「`http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress`」と入力します。

    c. **[表示名]** ボックスに「`http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname`」と入力します。

    d. **[名]** ボックスに「`http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname`」と入力します。

    e. **[姓]** ボックスに「`http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname`」と入力します。

    f. **[電子メール]** ボックスに「`http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress`」と入力します。

    g. **[保存]** をクリックします。

### <a name="create-airwatch-test-user"></a>AirWatch のテスト ユーザーを作成する

Azure AD ユーザーが AirWatch にサインインできるようにするには、そのユーザーを AirWatch にプロビジョニングする必要があります。 AirWatch の場合、プロビジョニングは手動で行います。

**ユーザー プロビジョニングを構成するには、次の手順に従います。**

1. **AirWatch** 企業サイトに管理者としてサインインします。

2. 左側のナビゲーション ウィンドウで、 **[Accounts]** 、 **[Users]** の順にクリックします。
  
   ![ユーザー](./media/airwatch-tutorial/accounts.png "ユーザー")

3. **Users\(ユーザー\)** メニューで、**List View\(リスト ビュー\)** 、**Add\(追加\) > Add User\(ユーザーの追加\)** の順にクリックします。
  
   ![[Add]\(追加\) および [Add User]\(ユーザーの追加\) ボタンが強調表示されているスクリーンショット。](./media/airwatch-tutorial/add.png "ユーザーの追加")

4. **[Add / Edit User]** ダイアログで、次の手順を実行します。

   ![ユーザーの追加](./media/airwatch-tutorial/save.png "ユーザーの追加")

   a. 関連するテキスト ボックスに、プロビジョニングする有効な Azure Active Directory アカウントの **[Username]** 、 **[Password]** 、 **[Confirm Password]** 、 **[First Name]** 、 **[Last Name]** 、 **[Email Address]** を入力します。

   b. **[保存]** をクリックします。

> [!NOTE]
> 他の AirWatch ユーザー アカウント作成ツールや、AirWatch から提供されている API を使用して、Azure AD ユーザー アカウントをプロビジョニングできます。

### <a name="test-sso"></a>SSO のテスト

このセクションでは、次のオプションを使用して Azure AD のシングル サインオン構成をテストします。 

* Azure portal で **[このアプリケーションをテストします]** をクリックします。 これにより、ログイン フローを開始できる AirWatch のサインオン URL にリダイレクトされます。 

* AirWatch のサインオン URL に直接移動し、そこからログイン フローを開始します。

* Microsoft マイ アプリを使用することができます。 マイ アプリで [AirWatch] タイルをクリックすると、AirWatch のサインオン URL にリダイレクトされます。 マイ アプリの詳細については、[マイ アプリの概要](../user-help/my-apps-portal-end-user-access.md)に関するページを参照してください。

## <a name="next-steps"></a>次のステップ

AirWatch を構成したら、組織の機密データを流出と侵入からリアルタイムで保護するセッション制御を適用することができます。 セッション制御は、条件付きアクセスを拡張したものです。 [Microsoft Cloud App Security でセッション制御を強制する方法](/cloud-app-security/proxy-deployment-any-app)をご覧ください。