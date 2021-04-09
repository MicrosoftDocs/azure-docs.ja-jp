---
title: チュートリアル:Azure Active Directory シングル サインオン (SSO) と Sonarqube の統合 | Microsoft Docs
description: Azure Active Directory と Sonarqube の間でシングル サインオンを構成する方法について説明します。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 09/29/2020
ms.author: jeedes
ms.openlocfilehash: f756e738989775bf9c06b44a03f002c14f42e3ac
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "96182345"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-sonarqube"></a>チュートリアル:Azure Active Directory シングル サインオン (SSO) と Sonarqube の統合

このチュートリアルでは、Sonarqube と Azure Active Directory (Azure AD) を統合する方法について説明します。 Azure AD と Sonarqube を統合すると、次のことができます。

* Sonarqube にアクセスする Azure AD ユーザーを制御する。
* ユーザーが自分の Azure AD アカウントを使用して Sonarqube に自動的にサインインできるようにする。
* 1 つの中央サイト (Azure Portal) で自分のアカウントを管理します。

## <a name="prerequisites"></a>前提条件

開始するには、次が必要です。

* Azure AD サブスクリプション。 サブスクリプションがない場合は、[無料アカウント](https://azure.microsoft.com/free/)を取得できます。
* Sonarqube でのシングル サインオン (SSO) が有効なサブスクリプション。

## <a name="scenario-description"></a>シナリオの説明

このチュートリアルでは、テスト環境で Azure AD の SSO を構成してテストします。

* Sonarqube では、**SP** Initiated SSO がサポートされます

> [!NOTE]
> このアプリケーションの識別子は固定文字列値であるため、1 つのテナントで構成できるインスタンスは 1 つだけです。

## <a name="adding-sonarqube-from-the-gallery"></a>ギャラリーからの Sonarqube の追加

Azure AD への Sonarqube の統合を構成するには、ギャラリーからマネージド SaaS アプリの一覧に Sonarqube を追加する必要があります。

1. 職場または学校アカウントか、個人の Microsoft アカウントを使用して、Azure portal にサインインします。
1. 左のナビゲーション ウィンドウで **[Azure Active Directory]** サービスを選択します。
1. **[エンタープライズ アプリケーション]** に移動し、 **[すべてのアプリケーション]** を選択します。
1. 新しいアプリケーションを追加するには、 **[新しいアプリケーション]** を選択します。
1. **[ギャラリーから追加する]** セクションで、検索ボックスに「**Sonarqube**」と入力します。
1. 結果のパネルから **Sonarqube** を選択し、アプリを追加します。 お使いのテナントにアプリが追加されるのを数秒待機します。

## <a name="configure-and-test-azure-ad-sso-for-sonarqube"></a>Sonarqube の Azure AD SSO の構成とテスト

**B.Simon** というテスト ユーザーを使用して、Sonarqube に対する Azure AD SSO を構成してテストします。 SSO が機能するために、Azure AD ユーザーと Sonarqube の関連ユーザーの間で、リンク関係が確立されている必要があります。

Sonarqube に対して Azure AD SSO を構成してテストするには、次の手順を行います。

1. **[Azure AD SSO の構成](#configure-azure-ad-sso)** - ユーザーがこの機能を使用できるようにします。
    1. **[Azure AD のテスト ユーザーの作成](#create-an-azure-ad-test-user)** - B.Simon で Azure AD のシングル サインオンをテストします。
    1. **[Azure AD テスト ユーザーの割り当て](#assign-the-azure-ad-test-user)** - B.Simon が Azure AD シングル サインオンを使用できるようにします。
1. **[Sonarqube SSO の構成](#configure-sonarqube-sso)** - アプリケーション側でシングル サインオン設定を構成します。
    1. **[Sonarqube テスト ユーザーの作成](#create-sonarqube-test-user)** - Sonarqube で B.Simon に対応するユーザーを作成し、Azure AD の B.Simon にリンクさせます。
1. **[SSO のテスト](#test-sso)** - 構成が機能するかどうかを確認します。

## <a name="configure-azure-ad-sso"></a>Azure AD SSO の構成

これらの手順に従って、Azure portal で Azure AD SSO を有効にします。

1. Azure portal の **Sonarqube** アプリケーション統合ページで、 **[管理]** セクションを見つけて、 **[シングル サインオン]** を選択します。
1. **[シングル サインオン方式の選択]** ページで、 **[SAML]** を選択します。
1. **[SAML でシングル サインオンをセットアップします]** ページで、 **[基本的な SAML 構成]** の編集 (ペン) アイコンをクリックして設定を編集します。

   ![基本的な SAML 構成を編集する](common/edit-urls.png)

1. **[基本的な SAML 構成]** セクションで、次のフィールドの値を入力します。

    **[サインオン URL]** ボックスに、次の URL を入力します:

    * **運用環境の場合**

        `https://servicessonar.corp.microsoft.com/`

    * **開発環境の場合**

        `https://servicescode-dev.westus.cloudapp.azure.com`

1. **[SAML でシングル サインオンをセットアップします]** ページの **[SAML 署名証明書]** セクションで、 **[証明書 (Base64)]** を見つけて、 **[ダウンロード]** を選択し、証明書をダウンロードして、お使いのコンピューターに保存します。

    ![証明書のダウンロードのリンク](common/certificatebase64.png)

1. **[Sonarqube のセットアップ]** セクションで、要件に基づいて適切な URL をコピーします。

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

このセクションでは、B.Simon に Sonarqube へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

1. Azure portal で **[エンタープライズ アプリケーション]** を選択し、 **[すべてのアプリケーション]** を選択します。
1. アプリケーションの一覧で **[Sonarqube]** を選択します。
1. アプリの概要ページで、 **[管理]** セクションを見つけて、 **[ユーザーとグループ]** を選択します。
1. **[ユーザーの追加]** を選択し、 **[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。
1. **[ユーザーとグループ]** ダイアログの [ユーザー] の一覧から **[B.Simon]** を選択し、画面の下部にある **[選択]** ボタンをクリックします。
1. ユーザーにロールが割り当てられることが想定される場合は、 **[ロールの選択]** ドロップダウンからそれを選択できます。 このアプリに対してロールが設定されていない場合は、[既定のアクセス] ロールが選択されていることを確認します。

1. **[割り当ての追加]** ダイアログで、 **[割り当て]** をクリックします。

## <a name="configure-sonarqube-sso"></a>Sonarqube の SSO 構成

1. 新しい Web ブラウザー ウィンドウを開き、Sonarqube 企業サイトに管理者としてサインインします。

1. **[Administration]\(管理\) > [Configuration]\(構成\) > [Security]\(セキュリティ\)** の順にクリックして **[SAML Plugin]\(SAML プラグイン\)** に移動し、次の手順を実行します。

1. 次の IdP メタデータの詳細をコピーし、SonarQube プラグインの対応するテキスト フィールドに貼り付けます。
    1. IdP エンティティ ID
    2. ログイン URL
    3. X.509 証明書 
1. すべての詳細を保存します。
    ![SAML プラグイン IDP](./media/sonarqube-tutorial/sso-idp-metadata.png)

1. **[SAML]** ページで、次の手順を実行します。

    ![Sonarqube の構成](./media/sonarqube-tutorial/config01.png)

    a. **[Enabled]\(有効化\)** オプションを **[yes]\(はい\)** に切り替えます。

    b. **[Application ID]\(アプリケーション ID\)** ボックスに、**sonarqube** のような名前を入力します。

    c. **[Provider Name]\(プロバイダー名\)** ボックスに、**SAML** のような名前を入力します。

    d. **[Provider ID]\(プロバイダー ID\)** ボックスに、Azure portal からコピーした **Azure AD 識別子** の値を貼り付けます。

    e. **[SAML login url]\(SAML ログイン URL\)** ボックスに、Azure portal からコピーした **ログイン URL** の値を貼り付けます。

    f. Base64 でエンコードされた証明書をメモ帳で開き、その内容をコピーして **[Provider certificate]\(プロバイダー証明書\)** ボックスに貼り付けます。

    g. **[SAML user login attribute]\(SAML ユーザー ログイン属性\)** ボックスに、値「`http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name`」を入力します。

    h. **[SAML user name attribute]\(SAML ユーザー名属性\)** ボックスに、値「`http://schemas.microsoft.com/identity/claims/displayname`」を入力します。

    i. **[SAML user email attribute]\(SAML ユーザー メール属性\)** ボックスに、値「`http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name`」を入力します。

    j. **[保存]** をクリックします。

### <a name="create-sonarqube-test-user"></a>Sonarqube テスト ユーザーの作成

このセクションでは、Sonarqube で B.Simon というユーザーを作成します。 [Sonarqube クライアント サポート チーム](https://www.sonarsource.com/support/)と連携して、Sonarqube プラットフォームにユーザーを追加してください。 シングル サインオンを使用する前に、ユーザーを作成し、有効化する必要があります。 

## <a name="test-sso"></a>SSO のテスト 

このセクションでは、次のオプションを使用して Azure AD のシングル サインオン構成をテストします。 

1. Azure portal で **[このアプリケーションをテストします]** をクリックします。 これにより、ログイン フローを開始できる Sonarqube のサインオン URL にリダイレクトされます。 

2. Sonarqube のサインオン URL に直接移動し、そこからログイン フローを開始します。

3. Microsoft アクセス パネルを使用することができます。 アクセス パネルで [Sonarqube] タイルをクリックすると、Sonarqube サインオン URL にリダイレクトされます。 アクセス パネルの詳細については、[アクセス パネルの概要](../user-help/my-apps-portal-end-user-access.md)に関する記事を参照してください。

## <a name="next-steps"></a>次のステップ

* Sonarqube を構成したら、組織の機密データを流出と侵入からリアルタイムで保護するセッション制御を適用できます。 セッション制御は、条件付きアクセスを拡張したものです。 [Microsoft Cloud App Security でセッション制御を強制する方法](/cloud-app-security/proxy-deployment-any-app)をご覧ください。