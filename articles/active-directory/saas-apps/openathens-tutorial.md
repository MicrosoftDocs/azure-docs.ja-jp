---
title: チュートリアル:Azure Active Directory シングル サインオン (SSO) と OpenAthens の統合 | Microsoft Docs
description: Azure Active Directory と OpenAthens の間でシングル サインオンを構成する方法について説明します。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 10/24/2019
ms.author: jeedes
ms.openlocfilehash: fc60552fadf10dde88c0e6d8c6f46bd25c68cf2f
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/18/2020
ms.locfileid: "88543943"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-openathens"></a>チュートリアル:Azure Active Directory シングル サインオン (SSO) と OpenAthens の統合

このチュートリアルでは、OpenAthens と Azure Active Directory (Azure AD) を統合する方法について説明します。 Azure AD と OpenAthens を統合すると、次のことができます。

* OpenAthens にアクセスするユーザーを Azure AD で制御する。
* ユーザーが自分の Azure AD アカウントを使用して OpenAthens に自動的にサインインできるようにする。
* 1 つの中央サイト (Azure Portal) で自分のアカウントを管理します。

SaaS アプリと Azure AD の統合の詳細については、「[Azure Active Directory でのアプリケーションへのシングル サインオン](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)」を参照してください。

## <a name="prerequisites"></a>前提条件

開始するには、次が必要です。

* Azure AD サブスクリプション。 サブスクリプションがない場合は、[無料アカウント](https://azure.microsoft.com/free/)を取得できます。
* OpenAthens でのシングル サインオン (SSO) が有効なサブスクリプション。

## <a name="scenario-description"></a>シナリオの説明

このチュートリアルでは、テスト環境で Azure AD の SSO を構成してテストします。

* OpenAthens では、**IDP** によって開始される SSO がサポートされます
* OpenAthens では、**Just-In-Time** ユーザー プロビジョニングがサポートされます

## <a name="adding-openathens-from-the-gallery"></a>ギャラリーからの OpenAthens の追加

Azure AD への OpenAthens の統合を構成するには、ギャラリーから管理対象 SaaS アプリの一覧に OpenAthens を追加する必要があります。

1. 職場または学校アカウントか、個人の Microsoft アカウントを使用して、[Azure portal](https://portal.azure.com) にサインインします。
1. 左のナビゲーション ウィンドウで **[Azure Active Directory]** サービスを選択します。
1. **[エンタープライズ アプリケーション]** に移動し、 **[すべてのアプリケーション]** を選択します。
1. 新しいアプリケーションを追加するには、 **[新しいアプリケーション]** を選択します。
1. **[ギャラリーから追加する]** セクションで、検索ボックスに「**OpenAthens**」と入力します。
1. 結果のパネルから **[OpenAthens]** を選択し、アプリを追加します。 お使いのテナントにアプリが追加されるのを数秒待機します。

## <a name="configure-and-test-azure-ad-single-sign-on-for-openathens"></a>OpenAthens の Azure AD シングル サインオンの構成とテスト

**B.Simon** というテスト ユーザーを使用して、OpenAthens に対する Azure AD SSO を構成してテストします。 SSO が機能するためには、Azure AD ユーザーと OpenAthens の関連ユーザーとの間にリンク関係を確立する必要があります。

OpenAthens で Azure AD SSO を構成してテストするには、次の構成要素を完了します。

1. **[Azure AD SSO の構成](#configure-azure-ad-sso)** - ユーザーがこの機能を使用できるようにします。
    * **[Azure AD のテスト ユーザーの作成](#create-an-azure-ad-test-user)** - B.Simon で Azure AD のシングル サインオンをテストします。
    * **[Azure AD テスト ユーザーの割り当て](#assign-the-azure-ad-test-user)** - B.Simon が Azure AD シングル サインオンを使用できるようにします。
1. **[OpenAthens SSO の構成](#configure-openathens-sso)** - アプリケーション側でシングル サインオン設定を構成します。
    * **[OpenAthens テスト ユーザーの作成](#create-openathens-test-user)** - OpenAthens で B.Simon に対応するユーザーを作成し、Azure AD の B.Simon にリンクさせます。
1. **[SSO のテスト](#test-sso)** - 構成が機能するかどうかを確認します。

## <a name="configure-azure-ad-sso"></a>Azure AD SSO の構成

これらの手順に従って、Azure portal で Azure AD SSO を有効にします。

1. [Azure portal](https://portal.azure.com/) の **OpenAthens** アプリケーション統合ページで、 **[管理]** セクションを見つけて、 **[シングル サインオン]** を選択します。
1. **[シングル サインオン方式の選択]** ページで、 **[SAML]** を選択します。
1. **[SAML でシングル サインオンをセットアップします]** ページで、 **[基本的な SAML 構成]** の編集 (ペン) アイコンをクリックして設定を編集します。

   ![基本的な SAML 構成を編集する](common/edit-urls.png)

1. **[基本的な SAML 構成]** セクションで、**サービス プロバイダー メタデータ ファイル**をアップロードします。この手順については、後からこのチュートリアルの中で説明します。

    a. **[メタデータ ファイルをアップロードします]** をクリックします。

    ![OpenAthens (メタデータのアップロード)](common/upload-metadata.png)

    b. **フォルダー ロゴ**をクリックしてメタデータ ファイルを選択し、 **[アップロード]** をクリックします。

    ![OpenAthens (アップロードするメタデータの参照)](common/browse-upload-metadata.png)

    c. メタデータ ファイルが正常にアップロードされると、**識別子**の値が、 **[基本的な SAML 構成]** セクションのテキスト ボックスに自動的に設定されます。

    ![[OpenAthens のドメインと URL] のシングル サインオン情報](common/idp-identifier.png)

1. **[SAML でシングル サインオンをセットアップします]** ページの **[SAML 署名証明書]** セクションで、 **[フェデレーション メタデータ XML]** を探して **[ダウンロード]** を選択し、証明書をダウンロードして、お使いのコンピューターに保存します。

    ![証明書のダウンロードのリンク](common/metadataxml.png)

1. **[OpenAthens のセットアップ]** セクションで、要件に基づいて適切な URL をコピーします。

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

このセクションでは、B.Simon に OpenAthens へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

1. Azure portal で **[エンタープライズ アプリケーション]** を選択し、 **[すべてのアプリケーション]** を選択します。
1. アプリケーションの一覧で **[OpenAthens]** を選択します。
1. アプリの概要ページで、 **[管理]** セクションを見つけて、 **[ユーザーとグループ]** を選択します。

   ![[ユーザーとグループ] リンク](common/users-groups-blade.png)

1. **[ユーザーの追加]** を選択し、 **[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。

    ![[ユーザーの追加] リンク](common/add-assign-user.png)

1. **[ユーザーとグループ]** ダイアログの [ユーザー] の一覧から **[B.Simon]** を選択し、画面の下部にある **[選択]** ボタンをクリックします。
1. SAML アサーション内に任意のロール値が必要な場合、 **[ロールの選択]** ダイアログでユーザーに適したロールを一覧から選択し、画面の下部にある **[選択]** をクリックします。
1. **[割り当ての追加]** ダイアログで、 **[割り当て]** をクリックします。

## <a name="configure-openathens-sso"></a>OpenAthens の SSO の構成

1. 別の Web ブラウザー ウィンドウで、OpenAthens 企業サイトに管理者としてサインインします。

1. **[管理]** タブの一覧から **[接続]** を選択します。

    ![Configure single sign-on](./media/openathens-tutorial/tutorial_openathens_application1.png)

1. **[SAML 1.1/2.0]** を選択し、 **[構成]** を選択します。

    ![Configure single sign-on](./media/openathens-tutorial/tutorial_openathens_application2.png)

1. 構成を追加するには、 **[参照]** を選択して Azure Portal からダウンロードしたメタデータ .xml ファイルをアップロードし、 **[追加]** を選択します。

    ![Configure single sign-on](./media/openathens-tutorial/tutorial_openathens_application3.png)

1. **[詳細]** タブで、次の手順を実行します。

    ![Configure single sign-on](./media/openathens-tutorial/tutorial_openathens_application4.png)

    a. **[Display name mapping]\(表示名マッピング\)** で、 **[Use attribute]\(属性の使用\)** を選択します。

    b. **[Display name attribute]\(表示名属性\)** ボックスに、値「`http://schema.microsoft.com/identity/claims/displayname`」を入力します。

    c. **[Unique user mapping]\(一意のユーザー マッピング\)** で、 **[Use attribute]\(属性の使用\)** を選択します。

    d. **[Display name attribute]\(一意のユーザ属性\)** ボックスに、値「`http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name`」を入力します。

    e. **[状態]** で、3 つのチェック ボックスすべてをオンにします。

    f. **[Create local accounts]\(ローカル アカウントの作成\)** で、 **[automatically]\(自動\)** を選択します。

    g. **[変更の保存]** を選択します。

    h. **[</> Relying Party]\(</> 証明書利用者\)** タブで、 **[Metadata URL]\(メタデータ URL\)** をコピーし、その URL をブラウザーで開いて **SP メタデータ XML** ファイルをダウンロードします。 Azure AD の **[基本的な SAML 構成]** セクションで、この SP メタデータ ファイルをアップロードします。

    ![Configure single sign-on](./media/openathens-tutorial/tutorial_openathens_application5.png)

### <a name="create-openathens-test-user"></a>OpenAthens のテスト ユーザーの作成

このセクションでは、Britta Simon というユーザーを OpenAthens に作成します。 OpenAthens では、**Just-In-Time ユーザー プロビジョニング**がサポートされています。この設定は既定で有効になっています。 このセクションでは、ユーザー側で必要な操作はありません。 OpenAthens にユーザーがまだ存在していない場合は、認証後に新規に作成されます。

## <a name="test-sso"></a>SSO のテスト

このセクションでは、アクセス パネルを使用して Azure AD のシングル サインオン構成をテストします。

アクセス パネル上で [OpenAthens] タイルをクリックすると、SSO を設定した OpenAthens に自動的にサインインします。 アクセス パネルの詳細については、[アクセス パネルの概要](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)に関する記事を参照してください。

## <a name="additional-resources"></a>その他のリソース

- [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Azure Active Directory でのアプリケーション アクセスとシングル サインオンとは](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Azure Active Directory の条件付きアクセスとは](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Azure AD で OpenAthens を試す](https://aad.portal.azure.com/)