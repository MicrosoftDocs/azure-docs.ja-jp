---
title: チュートリアル:Azure Active Directory と Mimecast Personal Portal の統合 | Microsoft Docs
description: Azure Active Directory と Mimecast Personal Portal の間でシングル サインオンを構成する方法について説明します。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 05/21/2020
ms.author: jeedes
ms.openlocfilehash: c14a519be112e61ed7055debe7a1a454e465bd9b
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/18/2020
ms.locfileid: "88549094"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-mimecast-personal-portal"></a>チュートリアル:Mimecast Personal Portal と Azure Active Directory のシングル サインオン (SSO) 統合

このチュートリアルでは、Mimecast Personal Portal を Azure Active Directory (Azure AD) と統合する方法について説明します。 Mimecast Personal Portal を Azure AD と統合すると、次のことを実行できます。

* Mimecast Personal Portal にアクセスするユーザーを Azure AD で制御できます。
* ユーザーが自分の Azure AD アカウントを使用して Mimecast Personal Portal に自動的にサインインできるようにします。
* 1 つの中央サイト (Azure Portal) で自分のアカウントを管理します。

SaaS アプリと Azure AD の統合の詳細については、「[Azure Active Directory でのアプリケーションへのシングル サインオン](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on)」を参照してください。

## <a name="prerequisites"></a>前提条件

開始するには、次が必要です。

* Azure AD サブスクリプション。 サブスクリプションがない場合は、[無料アカウント](https://azure.microsoft.com/free/)を取得できます。
* Mimecast Personal Portal のシングル サインオン (SSO) 対応のサブスクリプション。

## <a name="scenario-description"></a>シナリオの説明

このチュートリアルでは、テスト環境で Azure AD の SSO を構成してテストします。

* Mimecast Personal Portal では、**SP Initiated SSO と IDP Initiated SSO** がサポートされます
* Mimecast Personal Portal を構成すると、セッション制御を適用でき、組織の機密データの流出と侵入からリアルタイムで保護されます。 セッション制御は、条件付きアクセスを拡張したものです。 [Microsoft Cloud App Security でセッション制御を強制する方法](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app)をご覧ください。

## <a name="adding-mimecast-personal-portal-from-the-gallery"></a>ギャラリーからの Mimecast Personal Portal の追加

Azure AD への Mimecast Personal Portal の統合を構成するには、ギャラリーから管理対象 SaaS アプリの一覧に Mimecast Personal Portal を追加する必要があります。

1. 職場または学校アカウントか、個人の Microsoft アカウントを使用して、[Azure portal](https://portal.azure.com) にサインインします。
1. 左のナビゲーション ウィンドウで **[Azure Active Directory]** サービスを選択します。
1. **[エンタープライズ アプリケーション]** に移動し、 **[すべてのアプリケーション]** を選択します。
1. 新しいアプリケーションを追加するには、 **[新しいアプリケーション]** を選択します。
1. **[ギャラリーから追加する]** セクションで、検索ボックスに「**Mimecast Personal Portal**」と入力します。
1. 結果パネルから **Mimecast Personal Portal** を選択し、アプリを追加します。 お使いのテナントにアプリが追加されるのを数秒待機します。

## <a name="configure-and-test-azure-ad-single-sign-on-for-mimecast-personal-portal"></a>Mimecast Personal Portal に対して Azure AD シングル サインオンを構成してテストする

**B.Simon** というテスト ユーザーを使用して、Mimecast Personal Portal で Azure AD SSO を構成してテストします。 SSO を機能させるためには、Azure AD ユーザーと Mimecast Personal Portal の関連ユーザーとの間にリンク関係を確立する必要があります。

Mimecast Personal Portal で Azure AD SSO を構成してテストするには、次の構成要素を完了する必要があります。

1. **[Azure AD SSO の構成](#configure-azure-ad-sso)** - ユーザーがこの機能を使用できるようにします。
    1. **[Azure AD のテスト ユーザーの作成](#create-an-azure-ad-test-user)** - B.Simon で Azure AD のシングル サインオンをテストします。
    1. **[Azure AD テスト ユーザーの割り当て](#assign-the-azure-ad-test-user)** - B.Simon が Azure AD シングル サインオンを使用できるようにします。
1. **[Mimecast Personal Portal の SSO を構成する](#configure-mimecast-personal-portal-sso)** - アプリケーション側でシングル サインオン設定を構成します。
    1. **[Mimecast Personal Portal テスト ユーザーを作成する](#create-mimecast-personal-portal-test-user)** - Mimecast Personal Portal で B.Simon に対応するユーザーを作成し、Azure AD の B.Simon にリンクさせます。
1. **[SSO のテスト](#test-sso)** - 構成が機能するかどうかを確認します。

## <a name="configure-azure-ad-sso"></a>Azure AD SSO の構成

これらの手順に従って、Azure portal で Azure AD SSO を有効にします。

1. [Azure portal](https://portal.azure.com/) の **Mimecast Personal Portal** アプリケーション統合ページで、 **[管理]** セクションを見つけて、 **[シングル サインオン]** を選択します。
1. **[シングル サインオン方式の選択]** ページで、 **[SAML]** を選択します。
1. **[SAML でシングル サインオンをセットアップします]** ページで、 **[基本的な SAML 構成]** の編集 (ペン) アイコンをクリックして設定を編集します。

   ![基本的な SAML 構成を編集する](common/edit-urls.png)

1. **[基本的な SAML 構成]** セクションで、アプリケーションを IDP Initiated モードで構成する場合は、次の手順を行います。

    a. **[識別子]** ボックスに、次の形式で URL を入力します。

    | リージョン  |  値 | 
    | --------------- | --------------- |
    | ヨーロッパ          | `https://eu-api.mimecast.com/sso/<accountcode>`|
    | 米国   | `https://us-api.mimecast.com/sso/<accountcode>`|
    | 南アフリカ    | `https://za-api.mimecast.com/sso/<accountcode>`|
    | オーストラリア       | `https://au-api.mimecast.com/sso/<accountcode>`|
    | オフショア        | `https://jer-api.mimecast.com/sso/<accountcode>`|

    > [!NOTE]
    > `accountcode` の値は、Mimecast Personal Portal の **[アカウント]**  >  **[設定]**  >  **[アカウント コード]** に表示されます。 `accountcode` を識別子に追加します。

    b. **[応答 URL]** ボックスに、URL を入力します。

    | リージョン  |  値 |
    | --------------- | --------------- |
    | ヨーロッパ          | `https://eu-api.mimecast.com/login/saml`|
    | 米国   | `https://us-api.mimecast.com/login/saml`|
    | 南アフリカ    | `https://za-api.mimecast.com/login/saml`|
    | オーストラリア       | `https://au-api.mimecast.com/login/saml`|
    | オフショア        | `https://jer-api.mimecast.com/login/saml`|

1. **SP** 開始モードでアプリケーションを構成する場合は、次の手順に従います。

    **[サインオン URL]** ボックスに、次の URL を入力します。

    | リージョン  |  値 |
    | --------------- | --------------- |
    | ヨーロッパ          | `https://eu-api.mimecast.com/login/saml`|
    | United States   | `https://us-api.mimecast.com/login/saml`|
    | 南アフリカ    | `https://za-api.mimecast.com/login/saml`|
    | オーストラリア       | `https://au-api.mimecast.com/login/saml`|
    | オフショア        | `https://jer-api.mimecast.com/login/saml`|

1. **[保存]** をクリックします。

1. **[Set up single sign-on with SAML]\(SAML でシングル サインオンをセットアップします\)** ページの **[SAML 署名証明書]** セクションで、コピー ボタンをクリックして **[アプリのフェデレーション メタデータ URL]** をコピーして、お使いのコンピューターに保存します。

    ![証明書のダウンロードのリンク](common/copy-metadataurl.png)

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

このセクションでは、B.Simon に対して、Mimecast Personal Portal へのアクセスを許可して、Azure シングル サインオンを使用できるようにします。

1. Azure portal で **[エンタープライズ アプリケーション]** を選択し、 **[すべてのアプリケーション]** を選択します。
1. アプリケーションの一覧で **[Mimecast Personal Portal]** を選択します。
1. アプリの概要ページで、 **[管理]** セクションを見つけて、 **[ユーザーとグループ]** を選択します。

   ![[ユーザーとグループ] リンク](common/users-groups-blade.png)

1. **[ユーザーの追加]** を選択し、 **[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。

    ![[ユーザーの追加] リンク](common/add-assign-user.png)

1. **[ユーザーとグループ]** ダイアログの [ユーザー] の一覧から **[B.Simon]** を選択し、画面の下部にある **[選択]** ボタンをクリックします。
1. SAML アサーション内に任意のロール値が必要な場合、 **[ロールの選択]** ダイアログでユーザーに適したロールを一覧から選択し、画面の下部にある **[選択]** をクリックします。
1. **[割り当ての追加]** ダイアログで、 **[割り当て]** をクリックします。

## <a name="configure-mimecast-personal-portal-sso"></a>Mimecast Personal Portal の SSO を構成する

1. 別の Web ブラウザー ウィンドウで、Mimecast Administration Console にサインインします。

1. **[Administration]\(管理\)**  >  **[Services]\(サービス\)**  >  **[Applications]\(アプリケーション\)** に移動します。

    ![Mimecast Personal Portal の構成](./media/mimecast-personal-portal-tutorial/services.png)

1. **[Authentication Profiles]\(認証プロファイル\)** タブをクリックします。
    
    ![Mimecast Personal Portal の構成](./media/mimecast-personal-portal-tutorial/authentication-profiles.png)

1. **[New Authentication Profile]\(新しい認証プロファイル\)** タブをクリックします。

    ![Mimecast Personal Portal の構成](./media/mimecast-personal-portal-tutorial/new-authenticatio-profile.png)

1. **[Description]\(説明\)** テキストボックスに有効な説明を入力し、 **[Enforce SAML Authentication for Mimecast Personal Portal]\(Mimecast Personal Portal に SAML 認証を適用\)** チェックボックスをオンにします。

    ![Mimecast Personal Portal の構成](./media/mimecast-personal-portal-tutorial/selecting-personal-portal.png)

1. **[SAML Configuration for Mimecast Personal Portal]\(Mimecast Personal Portal の SAML 構成\)** ページで、次の手順に従います。

    ![Mimecast Personal Portal の構成](./media/mimecast-personal-portal-tutorial/sso-settings.png)

    a. **[Provider]\(プロバイダー\)** で、ドロップダウンから **[Azure Active Directory]\(Azure Active Directory\)** を選択します。

    b. **[Metadata URL]\(メタデータ URL\)** テキストボックスに、Azure portal からコピーした **[アプリのフェデレーション メタデータ URL]** の値を貼り付けます。

    c. **[インポート]** をクリックします。 メタデータ URL をインポートすると、フィールドは自動的に設定されます。これらのフィールドに対してアクションを実行する必要はありません。

    d. **[Use Password protected Context]\(パスワードで保護されたコンテキストを使用する\)** および **[Use Integrated Authentication Context]\(統合認証コンテキストを使用する\)** チェックボックスをオフにしてください。

    e. **[保存]** をクリックします。

### <a name="create-mimecast-personal-portal-test-user"></a>Mimecast Personal Portal テスト ユーザーの作成

1. 別の Web ブラウザー ウィンドウで、Mimecast Administration Console にサインインします。

1. **[Administration]\(管理\)**  >  **[Directories]\(ディレクトリ\)**  >  **[Internal Directories]\(内部ディレクトリ\)** に移動します。

    ![Mimecast Personal Portal の構成](./media/mimecast-personal-portal-tutorial/internal-directories.png)

1. お使いのドメインが下に記載されている場合は、そのドメインを選択します。記載されていない場合は、 **[New Domain]\(新しいドメイン\)** をクリックして新しいドメインを作成してください。

    ![Mimecast Personal Portal の構成](./media/mimecast-personal-portal-tutorial/domain-name.png)

1. **[New Address]\(新しいアドレス\)** タブをクリックします。

    ![Mimecast Personal Portal の構成](./media/mimecast-personal-portal-tutorial/new-address.png)

1. 次のページで、必要なユーザー情報を入力します。

    ![Mimecast Personal Portal の構成](./media/mimecast-personal-portal-tutorial/user-information.png)

    a. **[Email Address]\(メール アドレス\)** テキストボックスに、ユーザーのメール アドレス (`B.Simon@yourdomainname.com` など) を入力します。

    b. **[GLobal Name]\(グローバル名\)** テキストボックスに、ユーザーの**フル ネーム**を入力します。

    c. **[Password]\(パスワード\)** および **[Confirm Password]\(パスワードの確認\)** テキストボックスに、ユーザーのパスワードを入力します。

    d. **[Force Change at Login]\(ログイン時に変更を強制\)** チェックボックスをオンにします。

    e. **[保存]** をクリックします。

    f. ユーザーにロールを割り当てるには **[Role Edit]\(ロールの編集\)** をクリックし、組織の要件に従って、必要なロールをユーザーに割り当てます。

    ![Mimecast Personal Portal の構成](./media/mimecast-personal-portal-tutorial/assign-role.png)


## <a name="test-sso"></a>SSO のテスト 

このセクションでは、アクセス パネルを使用して Azure AD のシングル サインオン構成をテストします。

アクセス パネルで Mimecast Personal Portal のタイルをクリックすると、SSO を設定した Mimecast Personal Portal に自動的にサインインします。 アクセス パネルの詳細については、[アクセス パネルの概要](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)に関する記事を参照してください。

## <a name="additional-resources"></a>その他のリソース

- [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Azure Active Directory でのアプリケーション アクセスとシングル サインオンとは](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Azure Active Directory の条件付きアクセスとは](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Azure AD で Mimecast Personal Portal を試す](https://aad.portal.azure.com/)

- [Microsoft Cloud App Security におけるセッション制御とは](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)

- [高度な可視性と制御を使用して Mimecast Personal Portal を保護する方法](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)
