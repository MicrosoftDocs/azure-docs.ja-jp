---
title: チュートリアル:Azure Active Directory シングル サインオン (SSO) と Prezi の統合 | Microsoft Docs
description: Azure Active Directory と Prezi の間にシングル サインオンを構成する方法について説明します。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 08/20/2020
ms.author: jeedes
ms.openlocfilehash: 4ff8196ff4e30aa6ed036cabe00a72bfa8a5c51f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "92515323"
---
# <a name="tutorial-azure-active-directory-single-sign-on-integration-with-prezi"></a>チュートリアル:Azure Active Directory シングル サインオンと Prezi の統合

このチュートリアルでは、Prezi と Azure Active Directory (Azure AD) を統合する方法について説明します。 Azure AD と Prezi を統合すると、次のことができます。

* Prezi にアクセスできるユーザーを Azure AD で制御する。
* ユーザーが自分の Azure AD アカウントを使用して Prezi に自動的にサインインできるように設定する。
* Azure portal でアカウントを管理する。

サービスとしてのソフトウェア (SaaS) アプリと Azure AD の統合の詳細については、「[Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)」を参照してください。

## <a name="prerequisites"></a>前提条件

開始するには、次が必要です。

* Azure AD サブスクリプション。 サブスクリプションがない場合は、[無料アカウント](https://azure.microsoft.com/free/)を取得できます。
* シングル サインオン (SSO) が有効になっている Prezi サブスクリプション。

## <a name="scenario-description"></a>シナリオの説明

このチュートリアルでは、テスト環境で Azure AD の SSO を構成してテストします。

* Prezi では、SP Initiated SSO と IDP Initiated SSO がサポートされます。
* Prezi では、Just-In-Time ユーザー プロビジョニングがサポートされます。
* Prezi を構成したら、組織の機密データを流出と侵入からリアルタイムで保護するセッション制御を適用することができます。 セッション制御は、条件付きアクセスを拡張したものです。 詳細については、[Microsoft Cloud App Security でセッション制御を強制する方法](/cloud-app-security/proxy-deployment-any-app)に関するページを参照してください。

> [!NOTE]
> このアプリケーションの識別子は固定文字列値であるため、1 つのテナントで構成できるインスタンスは 1 つだけです。

## <a name="add-prezi-from-the-gallery"></a>ギャラリーからの Prezi の追加

Azure AD への Prezi の統合を構成するには、ギャラリーからマネージド SaaS アプリの一覧に Prezi を追加する必要があります。

1. 職場または学校アカウントか、個人の Microsoft アカウントを使用して、[Azure portal](https://portal.azure.com) にサインインします。
1. 左端のペインで、 **[Azure Active Directory]** を選択します。
1. **[エンタープライズ アプリケーション]** に移動し、 **[すべてのアプリケーション]** を選択します。
1. 新しいアプリケーションを追加するには、 **[新しいアプリケーション]** を選択します。
1. **[ギャラリーから追加する]** セクションで、検索ボックスに「**Prezi**」と入力します。
1. 結果パネルで **[Prezi]** を選択し、アプリを追加します。 お使いのテナントにアプリが追加されるのを数秒待機します。

## <a name="configure-and-test-azure-ad-sso-for-prezi"></a>Prezi の Azure AD SSO の構成とテスト

B.Simon というテスト ユーザーを使用して、Prezi に対する Azure AD SSO を構成してテストします。 SSO が機能するためには、Azure AD ユーザーと Prezi の関連ユーザーとの間にリンク関係を確立します。

Prezi で Azure AD SSO を構成してテストするには、以下の構成要素を完了します。

1. [Azure AD SSO を構成](#configure-azure-ad-sso)して、ユーザーがこの機能を使用できるようにします。
    1. [Azure AD のテスト ユーザーを作成](#create-an-azure-ad-test-user)し、B.Simon を使用して Azure AD SSO をテストします。
    1. [Azure AD テスト ユーザーを割り当て](#assign-the-azure-ad-test-user)、B.Simon が Azure AD SSO を使用できるようにします。
1. [Prezi SSO を構成](#configure-prezi-sso)して、アプリケーション側で SSO 設定を構成します。
    1. [Prezi テスト ユーザーを作成](#create-a-prezi-test-user)し、Prezi で B.Simon に対応するユーザーにして、Azure AD の B.Simon にリンクさせます。
1. [SSO をテスト](#test-sso)して、構成が機能するかどうかを確認します。

## <a name="configure-azure-ad-sso"></a>Azure AD SSO の構成

Azure portal で Azure AD SSO を有効にするには、次のようにします。

1. [Azure portal](https://portal.azure.com/) の **Prezi** アプリケーション統合ページで、 **[管理]** セクションを見つけて、 **[シングル サインオン]** を選択します。
1. **[シングル サインオン方式の選択]** ページで、 **[SAML]** を選択します。
1. **[SAML によるシングル サインオンのセットアップ]** ページで **[編集]** アイコンを選択して、 **[基本的な SAML 構成]** で設定を編集します。

   ![[基本的な SAML 構成] の設定を編集する](common/edit-urls.png)

1. アプリは Azure と事前に統合済みであるため、 **[基本的な SAML 構成]** セクションで実行が必要な手順はありません。

1. アプリケーションを **SP** Initiated モードで構成する場合は、 **[追加の URL を設定します]** を選択して次の手順を実行します。

    **[サインオン URL]** ボックスに、URL として「`https://prezi.com/login/sso/`」を入力します。

1. **[保存]** を選択します。

1. Prezi アプリケーションは、特定の形式の SAML アサーションを使用するため、カスタム属性のマッピングを SAML トークンの属性の構成に追加する必要があります。 次のスクリーンショットには、既定の属性一覧が示されています。

    ![ユーザー属性とクレーム](common/default-attributes.png)

1. Prezi アプリケーションでは、以下に示すように、さらにいくつかの属性も SAML 応答で返されることが想定されています。 これらの属性も値が事前に設定されますが、要件に基づいてそれらの値を確認することができます。
    
    | 名前 | ソース属性|
    | ---------------| --------------- |
    | given_name | User.givenname |
    | family_name | User.surname |

1. **[SAML によるシングル サインオンのセットアップ]** ページの **[SAML 署名証明書]** セクションで、 **[証明書 (Base64)]** を見つけます。 **[ダウンロード]** を選択して証明書をダウンロードし、コンピューターに保存します。

    ![証明書のダウンロードのリンク](common/certificatebase64.png)

1. **[Prezi のセットアップ]** セクションで、要件に基づいて適切な URL をコピーします。

    ![構成 URL のコピー](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Azure AD のテスト ユーザーの作成

このセクションでは、Azure portal 内で B.Simon というテスト ユーザーを作成します。

1. Azure portal の左端のペインで、 **[Azure Active Directory]** を選択します。 **[ユーザー]** に移動し、 **[すべてのユーザー]** を選択します。
1. 画面の上部にある **[新しいユーザー]** を選択します。
1. ユーザーのプロパティで、以下の手順を実行します。
   1. **[名前]** ボックスに「**B.Simon**」と入力します。
   1. **[ユーザー名]** ボックスに「`username@companydomain.extension`」と入力します (たとえば、`B.Simon@contoso.com`)。
   1. **[パスワードを表示]** チェック ボックスを選択します。 **[パスワード]** ボックスに表示された値を書き留めます。
   1. **［作成］** を選択します

### <a name="assign-the-azure-ad-test-user"></a>Azure AD テスト ユーザーの割り当て

このセクションでは、B.Simon に Prezi へのアクセスを許可することで、このユーザーが Azure SSO を使用できるようにします。

1. Azure portal 内で、 **[エンタープライズ アプリケーション]**  >  **[すべてのアプリケーション]** の順に選択します。
1. アプリケーションの一覧で **[Prezi]** を選択します。
1. アプリの概要ページで、 **[管理]** セクションを見つけて、 **[ユーザーとグループ]** を選択します。

   ![[ユーザーとグループ] リンク](common/users-groups-blade.png)

1. **[ユーザーの追加]** を選択し、 **[割り当ての追加]** ダイアログ ボックスで **[ユーザーとグループ]** を選択します。

    ![[ユーザーの追加] リンク](common/add-assign-user.png)

1. **[ユーザーとグループ]** ダイアログ ボックスのユーザーの一覧で **[B.Simon]** を選択し、画面の下部にある **[選択]** をクリックします。
1. SAML アサーションにロール値が必要な場合は、 **[ロールの選択]** ダイアログ ボックスでユーザーに適したロールを一覧から選択し、画面の下部にある **[選択]** をクリックします。
1. **[割り当ての追加]** ダイアログ ボックスで **[割り当て]** を選びます。

## <a name="configure-prezi-sso"></a>Prezi の SSO の構成

1. 別の Web ブラウザー ウィンドウで、チーム アカウントで Prezi にサインインし、[管理コンソール](https://prezi.com/organizations/manage)に移動します。

1. **管理コンソール** で **[Settings]\(設定\)** タブを選択します。

    ![Settings tab](./media/prezi-tutorial/settings-image.png)

1. **[Single Sign-On (SSO)]\(シングル サインオン (SSO)\)** セクションに移動し、SSO を有効にするようにトグルをオンにします。
    
    ![[Single Sign-On (SSO)]\(シングル サインオン (SSO)\) のトグル](./media/prezi-tutorial/single-signon.png)

1. **[Single sign-on (SSO)]\(シングル サインオン (SSO)\)** セクションで、以下の手順に従います。

    ![[Single sign-on (SSO)]\(シングル サインオン (SSO)\) セクション](./media/prezi-tutorial/configuration.png)

    1. **[Identifier or Issuer URL]\(ID または発行者 URL\)** ボックスに、Azure portal からコピーした **Azure AD 識別子** の値を貼り付けます。

    1. **[SAML 2.0 Endpoint(HTTP)]\(SAML 2.0 エンドポイント (HTTP)\)** ボックスに、Azure portal からコピーした **ログイン URL** の値を貼り付けます。

    1. Azure portal からダウンロードした **証明書 (Base64)** をメモ帳で開きます。 証明書の内容をコピーし、その内容を **[Certificate (X.509)]\(証明書 (X.509)\)** ボックスに貼り付けます。

    1. **[保存]** を選択します。

### <a name="create-a-prezi-test-user"></a>Prezi のテスト ユーザーの作成

このセクションでは、Britta Simon というユーザーを Prezi に作成します。 Prezi では、Just-In-Time ユーザー プロビジョニングがサポートされており、既定で有効になっています。 このセクションでは、ユーザー側で必要な操作はありません。 Prezi にユーザーがまだ存在していない場合は、認証後に新規に作成されます。

## <a name="test-sso"></a>SSO のテスト 

このセクションでは、アクセス パネルを使用して Azure AD SSO の構成をテストします。

アクセス パネルで [Prezi] タイルを選択すると、SSO を設定した Prezi アカウントに自動的にサインインします。 アクセス パネルの詳細については、[アクセス パネルの概要](../user-help/my-apps-portal-end-user-access.md)に関する記事を参照してください。

## <a name="additional-resources"></a>その他のリソース

- [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](./tutorial-list.md)
- [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)
- [Azure Active Directory の条件付きアクセスとは](../conditional-access/overview.md)
- [Azure AD で Prezi を試す](https://aad.portal.azure.com/)
- [Microsoft Cloud App Security におけるセッション制御とは](/cloud-app-security/proxy-intro-aad)
- [高度な可視性と制御によって Prezi を保護する方法](/cloud-app-security/proxy-intro-aad)