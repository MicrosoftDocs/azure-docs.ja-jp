---
title: 'チュートリアル: Azure Active Directory シングル サインオン (SSO) と Supermood の統合 | Microsoft Docs'
description: Azure Active Directory と Supermood の間でシングル サインオンを構成する方法について説明します。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 12/31/2019
ms.author: jeedes
ms.openlocfilehash: b1f8fb4c6586281352b6da05978c6ca4dac23ce5
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "92504798"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-supermood"></a>チュートリアル: Azure Active Directory シングル サインオン (SSO) と Supermood の統合

このチュートリアルでは、Supermood と Azure Active Directory (Azure AD) を統合する方法について説明します。 Azure AD と Supermood を統合すると、次のことができます。

* Supermood にアクセスできるユーザーを Azure AD で制御できます。
* ユーザーが自分の Azure AD アカウントを使用して Supermood に自動的にサインインできるように設定できます。
* 1 つの中央サイト (Azure Portal) で自分のアカウントを管理します。

SaaS アプリと Azure AD の統合の詳細については、「[Azure Active Directory でのアプリケーションへのシングル サインオン](../manage-apps/what-is-single-sign-on.md)」を参照してください。

## <a name="prerequisites"></a>前提条件

開始するには、次が必要です。

* Azure AD サブスクリプション。 サブスクリプションがない場合は、[無料アカウント](https://azure.microsoft.com/free/)を取得できます。
* Supermood でのシングル サインオン (SSO) が有効なサブスクリプション。

## <a name="scenario-description"></a>シナリオの説明

このチュートリアルでは、テスト環境で Azure AD の SSO を構成してテストします。

* Supermood では、**SP と IDP** によって開始される SSO がサポートされます
* Supermood では、**Just-In-Time** ユーザー プロビジョニングがサポートされます

## <a name="adding-supermood-from-the-gallery"></a>ギャラリーからの Supermood の追加

Azure AD への Supermood の統合を構成するには、ギャラリーからマネージド SaaS アプリの一覧に Supermood を追加する必要があります。

1. 職場または学校アカウントか、個人の Microsoft アカウントを使用して、[Azure portal](https://portal.azure.com) にサインインします。
1. 左のナビゲーション ウィンドウで **[Azure Active Directory]** サービスを選択します。
1. **[エンタープライズ アプリケーション]** に移動し、 **[すべてのアプリケーション]** を選択します。
1. 新しいアプリケーションを追加するには、 **[新しいアプリケーション]** を選択します。
1. **[ギャラリーから追加する]** セクションで、検索ボックスに、「**Supermood**」と入力します。
1. 結果のパネルから **Supermood** を選択し、アプリを追加します。 お使いのテナントにアプリが追加されるのを数秒待機します。

## <a name="configure-and-test-azure-ad-single-sign-on-for-supermood"></a>Supermood の Azure AD シングル サインオンの構成とテスト

**B.Simon** というテスト ユーザーを使用して、Supermood に対する Azure AD SSO を構成してテストします。 SSO が機能するために、Azure AD ユーザーと Supermood の関連ユーザーとの間にリンク関係を確立する必要があります。

Supermood に対する Azure AD SSO を構成してテストするには、次の構成要素を完了する必要があります。

1. **[Azure AD SSO の構成](#configure-azure-ad-sso)** - ユーザーがこの機能を使用できるようにします。
    * **[Azure AD のテスト ユーザーの作成](#create-an-azure-ad-test-user)** - B.Simon で Azure AD のシングル サインオンをテストします。
    * **[Azure AD テスト ユーザーの割り当て](#assign-the-azure-ad-test-user)** - B.Simon が Azure AD シングル サインオンを使用できるようにします。
1. **[Supermood の SSO の構成](#configure-supermood-sso)** - アプリケーション側でシングル サインオン設定を構成します。
    * **[Supermood のテスト ユーザーの作成](#create-supermood-test-user)** - Supermood で B.Simon に対応するユーザーを作成し、Azure AD の B.Simon にリンクさせます。
1. **[SSO のテスト](#test-sso)** - 構成が機能するかどうかを確認します。

## <a name="configure-azure-ad-sso"></a>Azure AD SSO の構成

これらの手順に従って、Azure portal で Azure AD SSO を有効にします。

1. [Azure portal](https://portal.azure.com/) の **Supermood** アプリケーション統合ページで、**[管理]** セクションを見つけて、**[シングル サインオン]** を選択します。
1. **[シングル サインオン方式の選択]** ページで、 **[SAML]** を選択します。
1. **[SAML でシングル サインオンをセットアップします]** ページで、 **[基本的な SAML 構成]** の編集 (ペン) アイコンをクリックして設定を編集します。

   ![基本的な SAML 構成を編集する](common/edit-urls.png)

1. **[基本的な SAML 構成]** セクションで、アプリケーションを **IDP** 開始モードで構成する場合は、次の手順を実行します。

    a. **[追加の URL を設定します]** をクリックします。
    
    b. **[リレー状態]** ボックスに、URL `https://supermood.co/auth/sso/saml20` を入力します。

1. アプリケーションを **SP** 開始モードで構成する場合は、 **[追加の URL を設定します]** をクリックして次の手順を実行します。

    **[サインオン URL]** テキスト ボックスに、URL として「`https://supermood.co/app/#!/loginv2`」と入力します。

1. **[保存]** をクリックします。

1. Supermood アプリケーションでは、特定の形式の SAML アサーションを使用するため、カスタム属性マッピングを SAML トークン属性の構成に追加する必要があります。 次のスクリーンショットには、既定の属性一覧が示されています。

    ![image](common/default-attributes.png)

1. その他に、Supermood アプリケーションでは、いくつかの属性が SAML 応答で返されることが想定されています。それらの属性を次に示します。 これらの属性も値が事前に設定されますが、要件に従ってそれらの値を確認することができます。

    | 名前 | ソース属性|
    | ---------------| ------|
    | firstName | User.givenname |
    | lastName | User.surname |

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

このセクションでは、B.Simon に Supermood へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

1. Azure portal で **[エンタープライズ アプリケーション]** を選択し、 **[すべてのアプリケーション]** を選択します。
1. アプリケーションの一覧で **[Supermood]** を選択します。
1. アプリの概要ページで、 **[管理]** セクションを見つけて、 **[ユーザーとグループ]** を選択します。

   ![[ユーザーとグループ] リンク](common/users-groups-blade.png)

1. **[ユーザーの追加]** を選択し、 **[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。

    ![[ユーザーの追加] リンク](common/add-assign-user.png)

1. **[ユーザーとグループ]** ダイアログの [ユーザー] の一覧から **[B.Simon]** を選択し、画面の下部にある **[選択]** ボタンをクリックします。
1. SAML アサーション内に任意のロール値が必要な場合、 **[ロールの選択]** ダイアログでユーザーに適したロールを一覧から選択し、画面の下部にある **[選択]** をクリックします。
1. **[割り当ての追加]** ダイアログで、 **[割り当て]** をクリックします。

## <a name="configure-supermood-sso"></a>Supermood の SSO の構成

1. セキュリティ管理者として、Supermood.co 管理パネルに移動します。

1. **[My account]\(マイ アカウント\)** (左下) と **[Single Sign On (SSO)]\(シングル サインオン (SSO)\)** をクリックします。

    ![証明書シングル](./media/supermood-tutorial/tutorial_supermood_single.png)

1. **[Your SAML 2.0 configurations]\(SAML 2.0 構成)\\** で **[Add an SAML 2.0 configuration for an email domain]\(電子メール ドメインの SAML 2.0 構成の追加\)** をクリックします。

    ![証明書の追加](./media/supermood-tutorial/tutorial_supermood_add.png)

1. **[Add an SAML 2.0 configuration for an email domain]\(電子メール ドメインの SAML 2.0 構成の追加\)** セクションで、次の手順に従います。

    ![証明書 saml](./media/supermood-tutorial/tutorial_supermood_saml.png)

    a. **[email domain for this Identity provider]\(この ID プロバイダーの電子メール ドメイン\)** テキストボックスに、自分のドメインを入力します。

    b. **[Use a metadata URL]\(メタデータ URL の使用\)** テキストボックスに、Azure portal からコピーした **アプリのフェデレーション メタデータ URL** を貼り付けます。

    c. **[追加]** をクリックします。

### <a name="create-supermood-test-user"></a>Supermood のテスト ユーザーの作成

このセクションでは、Britta Simon というユーザーを Supermood に作成します。 Supermood では、Just-In-Time ユーザー プロビジョニングがサポートされています。この設定は既定で有効になっています。 このセクションでは、ユーザー側で必要な操作はありません。 Supermood にユーザーがまだ存在していない場合は、認証後に新しく作成されます。 ユーザーを手動で作成する必要がある場合は、[Supermood サポート チーム](mailto:hello@supermood.fr)にお問い合わせください。

## <a name="test-sso"></a>SSO のテスト 

このセクションでは、アクセス パネルを使用して Azure AD のシングル サインオン構成をテストします。

アクセス パネル上で [Supermood] タイルをクリックすると、SSO を設定した Supermood に自動的にサインインします。 アクセス パネルの詳細については、[アクセス パネルの概要](../user-help/my-apps-portal-end-user-access.md)に関する記事を参照してください。

## <a name="additional-resources"></a>その他のリソース

- [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](./tutorial-list.md)

- [Azure Active Directory でのアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)

- [Azure Active Directory の条件付きアクセスとは](../conditional-access/overview.md)

- [Azure AD で Supermood を試す](https://aad.portal.azure.com/)