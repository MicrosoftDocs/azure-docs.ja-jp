---
title: チュートリアル:Azure AD SSO と Smart Global Governance の統合
description: Azure Active Directory と Smart Global Governance の間でシングル サインオンを構成する方法について説明します。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 05/04/2020
ms.author: jeedes
ms.openlocfilehash: 5a2169db47ca5a6adcddbcc9558161370b896a62
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "95997071"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-smart-global-governance"></a>チュートリアル:Azure Active Directory シングル サインオン (SSO) と Smart Global Governance の統合

このチュートリアルでは、Smart Global Governance と Azure Active Directory (Azure AD) を統合する方法について説明します。 Smart Global Governance と Azure AD を統合すると、次のことができます。

* Smart Global Governance にアクセスできるユーザーを Azure AD で制御できます。
* ユーザーが自分の Azure AD アカウントを使用して Smart Global Governance に自動的にサインインできるように設定できます。
* 1 つの中央サイト (Azure Portal) でアカウントを管理できます。

SaaS アプリと Azure AD の統合の詳細については、「[Azure Active Directory でのアプリケーションへのシングル サインオン](../manage-apps/what-is-single-sign-on.md)」を参照してください。

## <a name="prerequisites"></a>前提条件

開始するには、次が必要です。

* Azure AD サブスクリプション。 サブスクリプションがない場合は、[無料アカウント](https://azure.microsoft.com/free/)を取得できます。
* シングル サインオン (SSO) が有効な Smart Global Governance サブスクリプション。

## <a name="tutorial-description"></a>チュートリアルの説明

このチュートリアルでは、テスト環境で Azure AD の SSO を構成してテストします。

Smart Global Governance では、SP Initiated SSO と IDP Initiated SSO がサポートされます

Smart Global Governance を構成したら、組織の機密データを流出と侵入からリアルタイムで保護するセッション制御を適用することができます。 セッション制御は、条件付きアクセスを拡張したものです。 [Microsoft Cloud App Security でセッション制御を強制する方法](/cloud-app-security/proxy-deployment-any-app)をご覧ください。

## <a name="add-smart-global-governance-from-the-gallery"></a>ギャラリーからの Smart Global Governance の追加

Azure AD への Smart Global Governance の統合を構成するには、ギャラリーからマネージド SaaS アプリの一覧に Smart Global Governance を追加する必要があります。

1. 職場または学校アカウントか、個人の Microsoft アカウントを使用して、[Azure portal](https://portal.azure.com) にサインインします。
1. 左ウィンドウで、 **[Azure Active Directory]** を選択します。
1. **[エンタープライズ アプリケーション]** に移動し、 **[すべてのアプリケーション]** を選択します。
1. アプリケーションを追加するには、 **[新しいアプリケーション]** を選択します。
1. **[ギャラリーから追加する]** セクションで、検索ボックスに、「**Smart Global Governance**」と入力します。
1. 結果のパネルから **[Smart Global Governance]** を選択し、アプリを追加します。 お使いのテナントにアプリが追加されるのを数秒待機します。

## <a name="configure-and-test-azure-ad-sso-for-smart-global-governance"></a>Smart Global Governance の Azure AD SSO の構成とテスト

B.Simon というテスト ユーザーを使用して、Smart Global Governance に対する Azure AD SSO を構成してテストします。 SSO を機能させるためには、Azure AD ユーザーと Smart Global Governance 内の対応するユーザーとの間にリンク関係を確立する必要があります。

Smart Global Governance に対する Azure AD SSO を構成してテストするための大まかな手順は次のとおりです。

1. **[Azure AD SSO を構成](#configure-azure-ad-sso)** して、ユーザーがこの機能を使用できるようにします。
    1. Azure AD のシングル サインオンをテストするための **[Azure AD テスト ユーザーを作成](#create-an-azure-ad-test-user)** します。
    1. **[テスト ユーザーにアクセス権を付与](#grant-access-to-the-test-user)** して、Azure AD シングル サインオンをユーザーが使用できるようにします。
1. アプリケーション側で **[Smart Global Governance の SSO を構成](#configure-smart-global-governance-sso)** します。
    1. Azure AD のユーザーに対応するユーザーとして、 **[Smart Global Governance のテスト ユーザーを作成](#create-a-smart-global-governance-test-user)** します。
1. **[SSO をテスト](#test-sso)** して、構成が正しく機能することを確認します。

## <a name="configure-azure-ad-sso"></a>Azure AD SSO の構成

これらの手順に従って、Azure portal で Azure AD SSO を有効にします。

1. [Azure portal](https://portal.azure.com/) の **Smart Global Governance** アプリケーション統合ページで、 **[管理]** セクションの **[シングル サインオン]** を選択します。
1. **[シングル サインオン方式の選択]** ページで、 **[SAML]** を選択します。
1. **[SAML でシングル サインオンをセットアップします]** ページで、 **[基本的な SAML 構成]** の鉛筆ボタンを選択して設定を編集します。

   ![基本的な SAML 構成の鉛筆ボタン](common/edit-urls.png)

1. **[基本的な SAML 構成]** セクションで、アプリケーションを IDP 開始モードで構成する場合は、次の手順を実行します。

    a. **[識別子]** ボックスに、これらの URL のいずれかを入力します。

    - `https://eu-fr-south.console.smartglobalprivacy.com/platform/authentication-saml2/metadata`
    - `https://eu-fr-south.console.smartglobalprivacy.com/dpo/authentication-saml2/metadata`

    b. **[応答 URL]** ボックスに、これらの URL のいずれかを入力します。

    - `https://eu-fr-south.console.smartglobalprivacy.com/platform/authentication-saml2/acs`
    - `https://eu-fr-south.console.smartglobalprivacy.com/dpo/authentication-saml2/acs`

1. アプリケーションを SP 開始モードで構成する場合は、 **[追加の URL を設定します]** を選択して次の手順に従います。

   - **[サインオン URL]** ボックスに、これらの URL のいずれかを入力します。

    - `https://eu-fr-south.console.smartglobalprivacy.com/dpo`
    - `https://eu-fr-south.console.smartglobalprivacy.com/platform`

1. **[SAML でシングル サインオンをセットアップします]** ページの **[SAML 署名証明書]** セクションで、 **[証明書 (未加工)]** の **[ダウンロード]** リンクを選択し、証明書をダウンロードして、お使いのコンピューターに保存します。

    ![証明書のダウンロード リンク](common/certificateraw.png)

1. **[Smart Global Governance の設定]** セクションで、要件に基づいて適切な URL をコピーします。

    ![構成 URL のコピー](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Azure AD のテスト ユーザーの作成

このセクションでは、Azure portal 内で B.Simon というテスト ユーザーを作成します。

1. Azure portal の左ペインで、 **[Azure Active Directory]** を選択します。 **[ユーザー]** を選択し、 **[すべてのユーザー]** を選択します。
1. 画面の上部にある **[新しいユーザー]** を選択します。
1. **[ユーザー]** プロパティで、次の手順を実行します。
   1. **[名前]** ボックスに「**B.Simon**」と入力します。  
   1. **[ユーザー名]** ボックスに「\<username>@\<companydomain>.\<extension>」と入力します。 たとえば、「 `B.Simon@contoso.com` 」のように入力します。
   1. **[パスワードを表示]** をオンにし、 **[パスワード]** ボックスに表示された値を書き留めます。
   1. **［作成］** を選択します

### <a name="grant-access-to-the-test-user"></a>テスト ユーザーへのアクセス権の付与

このセクションでは、B.Simon に Smart Global Governance へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

1. Azure portal で **[エンタープライズ アプリケーション]** を選択し、 **[すべてのアプリケーション]** を選択します。
1. アプリケーションの一覧で **[Smart Global Governance]** を選択します。
1. アプリの概要ページの **[管理]** セクションで、 **[ユーザーとグループ]** を選択します。

   ![[ユーザーとグループ] の選択](common/users-groups-blade.png)

1. **[ユーザーの追加]** を選択し、 **[割り当ての追加]** ダイアログ ボックスで **[ユーザーとグループ]** を選択します。

    ![[ユーザーの追加] を選択する](common/add-assign-user.png)

1. **[ユーザーとグループ]** ダイアログ ボックスで、**ユーザー** の一覧で **[B.Simon]** を選択し、画面の下部にある **[選択]** ボタンをクリックします。
1. SAML アサーションにロール値が必要な場合は、 **[ロールの選択]** ダイアログ ボックスでユーザーに適したロールを一覧から選択し、画面の下部にある **[選択]** をクリックします。
1. **[割り当ての追加]** ダイアログ ボックスで **[割り当て]** を選びます。

## <a name="configure-smart-global-governance-sso"></a>Smart Global Governance の SSO の構成

Smart Global Governance 側でシングル サインオンを構成するには、ダウンロードした未加工の証明書と Azure portal からコピーした適切な URL を [Smart Global Governance サポート チーム](mailto:support.tech@smartglobal.com)に送信する必要があります。 SAML SSO 接続が両側で正しく構成されます。

### <a name="create-a-smart-global-governance-test-user"></a>Smart Global Governance のテスト ユーザーの作成

[Smart Global Governance サポート チーム](mailto:support.tech@smartglobal.com)と連携して、B.Simon という名前のユーザーを Smart Global Governance に追加します。 シングル サインオンを使用する前に、ユーザーを作成し、有効化する必要があります。

## <a name="test-sso"></a>SSO のテスト 

このセクションでは、アクセス パネルを使用して Azure AD SSO の構成をテストします。

アクセス パネルで [Smart Global Governance] タイルを選択すると、SSO を設定した Smart Global Governance インスタンスに自動的にサインインします。 アクセス パネルの詳細については、[アクセス パネルの概要](../user-help/my-apps-portal-end-user-access.md)に関する記事を参照してください。

## <a name="additional-resources"></a>その他のリソース

- [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアル](./tutorial-list.md)

- [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)

- [Azure Active Directory の条件付きアクセスとは](../conditional-access/overview.md)

- [Azure AD で Smart Global Governance を試す](https://aad.portal.azure.com/)

- [Microsoft Cloud App Security におけるセッション制御とは](/cloud-app-security/proxy-intro-aad)

- [高度な可視性と制御によって Smart Global Governance を保護する方法](/cloud-app-security/proxy-intro-aad)