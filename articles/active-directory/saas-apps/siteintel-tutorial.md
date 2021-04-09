---
title: チュートリアル:Azure Active Directory シングル サインオン (SSO) と SiteIntel の統合 | Microsoft Docs
description: Azure Active Directory と SiteIntel の間でシングル サインオンを構成する方法について説明します。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 06/02/2020
ms.author: jeedes
ms.openlocfilehash: 0c8224bf84a2235086d941df7d02ab6f458f3f16
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "92510000"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-siteintel"></a>チュートリアル:Azure Active Directory シングル サインオン (SSO) と SiteIntel の統合

このチュートリアルでは、SiteIntel と Azure Active Directory (Azure AD) を統合する方法について説明します。 Azure AD と SiteIntel を統合すると、次のことができます。

* SiteIntel にアクセスするユーザーを Azure AD で制御できます。
* ユーザーが自分の Azure AD アカウントを使用して SiteIntel に自動的にサインインできるように設定できます。
* 1 つの中央サイト (Azure Portal) でアカウントを管理できます。

サービスとしてのソフトウェア (SaaS) アプリと Azure AD の統合の詳細については、「[Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)」を参照してください。

## <a name="prerequisites"></a>前提条件

開始するには、次が必要です。

* Azure AD サブスクリプション。 サブスクリプションがない場合は、[無料アカウント](https://azure.microsoft.com/free/)を取得できます。
* SiteIntel のシングル サインオン (SSO) が有効なサブスクリプション。

## <a name="scenario-description"></a>シナリオの説明

このチュートリアルでは、テスト環境で Azure AD の SSO を構成してテストします。

* SiteIntel では、SP Initiated SSO と IdP Initiated SSO がサポートされます。
* SiteIntel を構成したら、組織の機密データを流出と侵入からリアルタイムで保護するセッション制御を適用することができます。 セッション制御は、条件付きアクセスを拡張したものです。 [Microsoft Cloud App Security でセッション制御を強制する方法](/cloud-app-security/proxy-deployment-any-app)をご覧ください。

## <a name="add-siteintel-from-the-gallery"></a>ギャラリーから SiteIntel を追加する

Azure AD への SiteIntel の統合を構成するには、ギャラリーからマネージド SaaS アプリの一覧に SiteIntel を追加する必要があります。

1. 職場または学校アカウントか、個人の Microsoft アカウントを使用して、[Azure portal](https://portal.azure.com) にサインインします。
1. 左側のペインで、 **[Azure Active Directory]** を選択します。
1. **[エンタープライズ アプリケーション]** に移動し、 **[すべてのアプリケーション]** を選択します。
1. 新しいアプリケーションを追加するには、 **[新しいアプリケーション]** を選択します。
1. **[ギャラリーから追加する]** ボックスに「**SiteIntel**」と入力します。
1. 結果リストで **[SiteIntel]** を選択し、アプリを追加します。 お使いのテナントにアプリが追加されるのを数秒待機します。

## <a name="configure-and-test-azure-ad-single-sign-on-for-siteintel"></a>SiteIntel の Azure AD シングル サインオンの構成とテスト

*B.Simon* というテスト ユーザーを使用して、SiteIntel に対する Azure AD SSO を構成してテストします。 SSO が機能するためには、Azure AD ユーザーと SiteIntel の関連ユーザーとの間にリンク関係を確立する必要があります。

SiteIntel に対する Azure AD SSO を構成してテストするには、次の構成要素を完了する必要があります。

1. **[Azure AD SSO の構成](#configure-azure-ad-sso)** - ユーザーがこの機能を使用できるようにします。  

    a. **[Azure AD のテスト ユーザーの作成](#create-an-azure-ad-test-user)** - ユーザー B.Simon で Azure AD のシングル サインオンをテストします。  

    b. **[Azure AD のテスト ユーザーの割り当て](#assign-the-azure-ad-test-user)** - ユーザー B.Simon が Azure AD シングル サインオンを使用できるようにします。

1. **[SiteIntel の SSO の構成](#configure-siteintel-sso)** - アプリケーション側でシングル サインオン設定を構成します。

    * **[SiteIntel のテスト ユーザーの作成](#create-a-siteintel-test-user)** - SiteIntel でユーザー B.Simon に対応するユーザーを作成し、Azure AD の B.Simon にリンクさせます。

1. **[SSO をテスト](#test-sso)** して、構成が正しく機能することを確認します。

## <a name="configure-azure-ad-sso"></a>Azure AD SSO の構成

Azure portal で Azure AD SSO を有効にするには、以下を実行します。

1. [Azure portal](https://portal.azure.com/) の **SiteIntel** アプリケーション統合ページで、 **[管理]** セクションに移動して、 **[シングル サインオン]** を選択します。
1. **[シングル サインオン方式の選択]** ページで、 **[SAML]** を選択します。
1. **[SAML でシングル サインオンをセットアップします]** ページで、 **[基本的な SAML 構成]** の横にある **[編集]** (ペン アイコン) を選択します。

   ![[SAML でシングル サインオンをセットアップします] ペインのスクリーンショット](common/edit-urls.png)

1. IdP-Initiated モードでアプリケーションを構成するには、 **[基本的な SAML 構成]** セクションで次の手順を実行します。

    a. **[識別子]** ボックスに、`urn:amazon:cognito:sp:<REGION>_<USERPOOLID>` 形式で URL を入力します。

    b. **[応答 URL]** ボックスに、`https://<CLIENT>.auth.siteintel.com/saml2/idpresponse` 形式で URL を入力します。

    c. **[リレー状態]** ボックスに、`https://<CLIENT>.siteintel.com` 形式で URL を入力します。

1. SP-Initiated モードでアプリケーションを構成するには、 **[追加の URL を設定します]** を選択して、次の手順を実行します。

   * **[サインオン URL]** ボックスに、`https://<CLIENT>.siteintel.com` 形式で URL を入力します。

    > [!NOTE]
    > これらは実際の値ではありません。 実際の識別子、応答 URL、サインオン URL、リレー状態でこれらの値を更新します。 これらの値を取得するには、[SiteIntel クライアント サポート チーム](mailto:support@intalytics.com)に連絡してください。 Azure portal の **[基本的な SAML 構成]** セクションに示されているパターンを参照することもできます。

1. **[SAML でシングル サインオンをセットアップします]** ページの **[SAML 署名証明書]** セクションで **[コピー]** ボタンを選択して、 **[アプリのフェデレーション メタデータ URL]** ボックスの URL をコピーします。

    ![[アプリのフェデレーション メタデータ URL] の [コピー] ボタンのスクリーンショット](common/copy-metadataurl.png)

### <a name="create-an-azure-ad-test-user"></a>Azure AD のテスト ユーザーの作成

このセクションでは、Azure portal 内で B.Simon というテスト ユーザーを作成します。

1. Azure portal の左ペインで、 **[Azure Active Directory]**  >  **[ユーザー]**  >  **[すべてのユーザー]** を選択します。
1. ペインの上部にある **[新しいユーザー]** を選択します。
1. **[ユーザー]** プロパティで、次の手順を実行します。

   a. **[名前]** ボックスに「**B.Simon**」と入力します。  

   b. **[ユーザー名]** ボックスに、`username@companydomain.extension` 形式でユーザー名を入力します (例: `B.Simon@contoso.com`)。

   c. **[パスワードを表示]** チェック ボックスをオンにし、 **[パスワード]** ボックスに表示された値を書き留めます。

   d. **［作成］** を選択します

### <a name="assign-the-azure-ad-test-user"></a>Azure AD テスト ユーザーの割り当て

このセクションでは、ユーザー B.Simon に SiteIntel へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

1. Azure portal で、 **[エンタープライズ アプリケーション]**  >  **[すべてのアプリケーション]** の順に選択します。
1. **[アプリケーション]** の一覧から **[SiteIntel]** を選択します。
1. アプリの概要ページの **[管理]** セクションで、 **[ユーザーとグループ]** を選択します。

   ![[ユーザーとグループ] リンクのスクリーンショット](common/users-groups-blade.png)

1. **[ユーザーの追加]** を選択し、 **[割り当ての追加]** ペインで **[ユーザーとグループ]** を選択します。

    ![[ユーザーを追加] ボタンのスクリーンショット](common/add-assign-user.png)

1. **[ユーザーとグループ]** ペインで **[B.Simon]** を選択し、画面の下部にある **[選択]** ボタンを選択します。
1. SAML アサーション内にロール値が必要な場合、 **[ロールの選択]** ペインで、一覧からユーザーに適したロールを選択し、 **[選択]** ボタンを選択します。
1. **[割り当ての追加]** ペインで、 **[割り当て]** を選択します。

## <a name="configure-siteintel-sso"></a>SiteIntel の SSO の構成

SiteIntel 側でシングル サインオンを構成するには、 **[アプリのフェデレーション メタデータ URL]** ボックスからコピーした URL を [SiteIntel サポート チーム](mailto:support@intalytics.com)に送信します。 サポート チームはこの値を設定して、SAML SSO 接続を両方の側で正しく確立します。

### <a name="create-a-siteintel-test-user"></a>SiteIntel のテスト ユーザーの作成

このセクションでは、SiteIntel で *Britta Simon* というユーザーを作成します。 [SiteIntel サポート チーム](mailto:support@intalytics.com)と連携して、SiteIntel プラットフォームにユーザーを追加してください。 シングル サインオンを使用する前に、ユーザーを作成し、有効化する必要があります。

## <a name="test-sso"></a>SSO のテスト

このセクションでは、アクセス パネルを使用して Azure AD のシングル サインオン構成をテストします。

アクセス パネル上で **[SiteIntel]** タイルを選択すると、SSO を設定した SiteIntel に自動的にサインインします。 アクセス パネルの詳細については、[アクセス パネルの概要](../user-help/my-apps-portal-end-user-access.md)に関する記事を参照してください。

## <a name="additional-resources"></a>その他のリソース

- [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](./tutorial-list.md)
- [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)
- [Azure Active Directory の条件付きアクセスとは](../conditional-access/overview.md)
- [Azure AD で SiteIntel を試す](https://aad.portal.azure.com/)
- [Microsoft Cloud App Security におけるセッション制御とは](/cloud-app-security/proxy-intro-aad)
- [高度な可視性と制御によって SiteIntel を保護する方法](/cloud-app-security/proxy-intro-aad)