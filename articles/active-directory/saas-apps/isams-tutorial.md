---
title: チュートリアル:Azure Active Directory シングル サインオン (SSO) と iSAMS の統合 | Microsoft Docs
description: Azure Active Directory と iSAMS の間でシングル サインオンを構成する方法について説明します。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 08/04/2020
ms.author: jeedes
ms.openlocfilehash: e994e39cc916c51e3ad6b00015d710bb422cccc9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "92459698"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-isams"></a>チュートリアル:Azure Active Directory シングル サインオン (SSO) と iSAMS の統合

このチュートリアルでは、iSAMS と Azure Active Directory (Azure AD) を統合する方法について説明します。 iSAMS と Azure AD を統合すると、次のことができます。

* iSAMS にアクセスできるユーザーを Azure AD で制御します。
* ユーザーが自分の Azure AD アカウントを使用して iSAMS に自動的にサインインできるようにします。
* 1 つの中央サイト (Azure Portal) で自分のアカウントを管理します。

SaaS アプリと Azure AD の統合の詳細については、「[Azure Active Directory でのアプリケーションへのシングル サインオン](../manage-apps/what-is-single-sign-on.md)」を参照してください。

## <a name="prerequisites"></a>前提条件

開始するには、次が必要です。

* Azure AD サブスクリプション。 サブスクリプションがない場合は、[無料アカウント](https://azure.microsoft.com/free/)を取得できます。
* iSAMS でのシングル サインオン (SSO) が有効なサブスクリプション。

## <a name="scenario-description"></a>シナリオの説明

このチュートリアルでは、テスト環境で Azure AD の SSO を構成してテストします。


* iSAMS では、**SP Initiated SSO と IDP Initiated SSO** がサポートされます
* iSAMS を構成したら、ご自分の組織の機密データの流出と侵入をリアルタイムで保護するセッション制御を適用することができます。 セッション制御は、条件付きアクセスを拡張したものです。 [Microsoft Cloud App Security でセッション制御を強制する方法](/cloud-app-security/proxy-deployment-any-app)をご覧ください。

## <a name="adding-isams-from-the-gallery"></a>ギャラリーからの iSAMS の追加

Azure AD への iSAMS の統合を構成するには、ギャラリーからマネージド SaaS アプリの一覧に iSAMS を追加する必要があります。

1. 職場または学校アカウントか、個人の Microsoft アカウントを使用して、[Azure portal](https://portal.azure.com) にサインインします。
1. 左のナビゲーション ウィンドウで **[Azure Active Directory]** サービスを選択します。
1. **[エンタープライズ アプリケーション]** に移動し、 **[すべてのアプリケーション]** を選択します。
1. 新しいアプリケーションを追加するには、 **[新しいアプリケーション]** を選択します。
1. **[ギャラリーから追加する]** セクションで、検索ボックスに「**iSAMS**」と入力します。
1. 結果のパネルから **[iSAMS]** を選択し、アプリを追加します。 お使いのテナントにアプリが追加されるのを数秒待機します。


## <a name="configure-and-test-azure-ad-sso-for-isams"></a>iSAMS の Azure AD SSO の構成とテスト

**B.Simon** というテスト ユーザーを使用して、iSAMS に対する Azure AD SSO を構成してテストします。 SSO が機能するためには、Azure AD ユーザーと iSAMS の関連ユーザーとの間にリンク関係を確立する必要があります。

iSAMS に対する Azure AD SSO を構成してテストするには、次の構成要素を完了します。

1. **[Azure AD SSO の構成](#configure-azure-ad-sso)** - ユーザーがこの機能を使用できるようにします。
    1. **[Azure AD のテスト ユーザーの作成](#create-an-azure-ad-test-user)** - B.Simon で Azure AD のシングル サインオンをテストします。
    1. **[Azure AD テスト ユーザーの割り当て](#assign-the-azure-ad-test-user)** - B.Simon が Azure AD シングル サインオンを使用できるようにします。
1. **[iSAMS の SSO の構成](#configure-isams-sso)** - アプリケーション側でシングル サインオン設定を構成します。
    1. **[iSAMS のテスト ユーザーの作成](#create-isams-test-user)** - iSAMS で B.Simon に対応するユーザーを作成し、Azure AD の B.Simon にリンクさせます。
1. **[SSO のテスト](#test-sso)** - 構成が機能するかどうかを確認します。

## <a name="configure-azure-ad-sso"></a>Azure AD SSO の構成

これらの手順に従って、Azure portal で Azure AD SSO を有効にします。

1. [Azure portal](https://portal.azure.com/) の **iSAMS** アプリケーション統合ページで、 **[管理]** セクションを見つけて、 **[シングル サインオン]** を選択します。
1. **[シングル サインオン方式の選択]** ページで、 **[SAML]** を選択します。
1. **[SAML でシングル サインオンをセットアップします]** ページで、 **[基本的な SAML 構成]** の編集 (ペン) アイコンをクリックして設定を編集します。

   ![基本的な SAML 構成を編集する](common/edit-urls.png)

1. **[基本的な SAML 構成]** セクションで、アプリケーションを **IDP** 開始モードで構成する場合は、次のフィールドの値を入力します。

    a. **[識別子]** ボックスに、`https://<SUBDOMAIN>.isams.cloud/main/sso/saml2` の形式で URL を入力します。

    b. **[応答 URL]** ボックスに、`https://<SUBDOMAIN>.isams.cloud/main/sso/saml2/acs` のパターンを使用して URL を入力します

1. アプリケーションを **SP** 開始モードで構成する場合は、 **[追加の URL を設定します]** をクリックして次の手順を実行します。

    **[サインオン URL]** ボックスに、`https://<SUBDOMAIN>.isams.cloud/` という形式で URL を入力します。

    > [!NOTE]
    > これらは実際の値ではありません。 実際の識別子、応答 URL、サインオン URL でこれらの値を更新します。 これらの値を取得するには、[iSAMS クライアント サポート チーム](mailto:support@isams.com)に問い合わせてください。 Azure portal の **[基本的な SAML 構成]** セクションに示されているパターンを参照することもできます。

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

このセクションでは、B.Simon に iSAMS へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

1. Azure portal で **[エンタープライズ アプリケーション]** を選択し、 **[すべてのアプリケーション]** を選択します。
1. アプリケーションの一覧で **[iSAMS]** を選択します。
1. アプリの概要ページで、 **[管理]** セクションを見つけて、 **[ユーザーとグループ]** を選択します。

   ![[ユーザーとグループ] リンク](common/users-groups-blade.png)

1. **[ユーザーの追加]** を選択し、 **[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。

    ![[ユーザーの追加] リンク](common/add-assign-user.png)

1. **[ユーザーとグループ]** ダイアログの [ユーザー] の一覧から **[B.Simon]** を選択し、画面の下部にある **[選択]** ボタンをクリックします。
1. SAML アサーション内に任意のロール値が必要な場合、 **[ロールの選択]** ダイアログでユーザーに適したロールを一覧から選択し、画面の下部にある **[選択]** をクリックします。
1. **[割り当ての追加]** ダイアログで、 **[割り当て]** をクリックします。

## <a name="configure-isams-sso"></a>iSAMS の SSO の構成

1. 管理者として iSAMS にログインします。

1. コントロール パネルに移動し、 **[Authentication]\(認証\)** モジュールを開きます。
1. 右側のメニューで、 **[Identity Providers]\(ID プロバイダー\)** を選択します

    ![[Identity Providers]\(ID プロバイダー\) が選択されている Active Directory 構成を示すスクリーンショット。](./media/isams-tutorial/click-identity-provider.png)

1. **[Add Provider]\(プロバイダーの追加\)** を選択します

    ![[Add Providers]\(プロバイダーの追加\) が選択されている [Identity Providers]\(ID プロバイダー\) を示すスクリーンショット。](./media/isams-tutorial/add-identity-provider.png)


1. 次のページで、以下の手順を実行します。

    ![説明されている手順を実行できる [Identity Providers]\(ID プロバイダー\) ウィザードのスクリーンショット。](./media/isams-tutorial/configure-isams.png)

    a. **[Name]\(名前\)** ボックスに、`Saml2 Azure` などの有効な名前を入力します。 これはログイン ページに表示される名前です。

    b. [Metadata URL]\(メタデータ URL\) ボックスに、Azure portal からコピーした **[アプリのフェデレーション メタデータ URL]** 値を入力します。
    
    c. **[Import]\(インポート\)** を押します。
    
    d. **[Enabled Client Applications]\(有効なクライアント アプリケーション\)** セクションの **[Applications]\(アプリケーション\)** ボックスで、プロバイダーによってログインページに表示されるすべての iSAMS アプリケーションを選択します。

    e. **[Save & Close]\(保存して閉じる\)** をクリックします。

### <a name="create-isams-test-user"></a>iSAMS のテスト ユーザーの作成

1. 管理者として iSAMS にログインします。

2.  **[Control Panel Home]\(コントロール パネル ホーム\)**  ->  **[Security & Permissions]\(セキュリティとアクセス許可\)**  ->  **[User Accounts]\(ユーザー アカウント\)**  ->  **[User Options & Tasks]\(ユーザー オプションとタスク\)**  ->  **[Modify User Properties]\(ユーザー プロパティの変更\)** に移動します

    ![[Modify User Properties]\(ユーザー プロパティの変更\) が選択されている [User Accounts]\(ユーザー アカウント\) ページを示すスクリーンショット。](./media/isams-tutorial/modify-user-properties.png)


3. 表示されるポップアップ ウィンドウで、 **[Account Details]\(アカウントの詳細\)** タブを選択し、 **[Authorization]\(承認\)** を、新しく作成した ID プロバイダーのものに変更します。

    ![[Authorization]\(承認\) の値が表示されている [Account Details]\(アカウントの詳細\) を示すスクリーンショット。](./media/isams-tutorial/account-details.png)

4. **[Save & Close]\(保存して閉じる\)** をクリックします。

## <a name="test-sso"></a>SSO のテスト 

このセクションでは、アクセス パネルを使用して Azure AD のシングル サインオン構成をテストします。

アクセス パネル上で [iSAMS] タイルをクリックすると、SSO を設定した iSAMS に自動的にサインインします。 アクセス パネルの詳細については、[アクセス パネルの概要](../user-help/my-apps-portal-end-user-access.md)に関する記事を参照してください。

## <a name="additional-resources"></a>その他のリソース

- [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](./tutorial-list.md)

- [Azure Active Directory でのアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)

- [Azure Active Directory の条件付きアクセスとは](../conditional-access/overview.md)

- [Azure AD で iSAMS を試す](https://aad.portal.azure.com/)

- [Microsoft Cloud App Security におけるセッション制御とは](/cloud-app-security/proxy-intro-aad)