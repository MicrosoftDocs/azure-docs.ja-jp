---
title: チュートリアル:Azure AD SSO と Change Process Management の統合
description: Azure Active Directory と Change Process Management の間でシングル サインオンを構成する方法について説明します。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 05/07/2020
ms.author: jeedes
ms.openlocfilehash: 03c78f05566876356e4f486368dc2a5b3a29de43
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "92456260"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-change-process-management"></a>チュートリアル:Azure Active Directory シングル サインオン (SSO) と Change Process Management の統合

このチュートリアルでは、Change Process Management と Azure Active Directory (Azure AD) を統合する方法について説明します。 Change Process Management と Azure AD を統合すると、次のことができます。

* Change Process Management にアクセスできるユーザーを Azure AD で制御できます。
* ユーザーが自分の Azure AD アカウントで Change Process Management に自動的にサインインできるように設定できます。
* 1 つの中央サイト (Azure Portal) でアカウントを管理できます。

SaaS アプリと Azure AD の統合の詳細については、「[Azure Active Directory でのアプリケーションへのシングル サインオン](../manage-apps/what-is-single-sign-on.md)」を参照してください。

## <a name="prerequisites"></a>前提条件

開始するには、次が必要です。

* Azure AD サブスクリプション。 サブスクリプションがない場合は、[無料アカウント](https://azure.microsoft.com/free/)を取得できます。
* シングル サインオン (SSO) が有効な Change Process Management サブスクリプション。

## <a name="tutorial-description"></a>チュートリアルの説明

このチュートリアルでは、テスト環境で Azure AD の SSO を構成してテストします。

Change Process Management では、IDP Initiated SSO がサポートされます。

Change Process Management を構成したら、組織の機密データを流出と侵入からリアルタイムで保護するセッション制御を適用することができます。 セッション制御は、条件付きアクセスを拡張したものです。 [Microsoft Cloud App Security でセッション制御を強制する方法](/cloud-app-security/proxy-deployment-any-app)をご覧ください。

## <a name="add-change-process-management-from-the-gallery"></a>ギャラリーからの Change Process Management の追加

Azure AD への Change Process Management の統合を構成するには、ギャラリーからマネージド SaaS アプリの一覧に Change Process Management を追加する必要があります。

1. 職場または学校アカウントか、個人の Microsoft アカウントを使用して、[Azure portal](https://portal.azure.com) にサインインします。
1. 左ウィンドウで、 **[Azure Active Directory]** を選択します。
1. **[エンタープライズ アプリケーション]** に移動し、 **[すべてのアプリケーション]** を選択します。
1. アプリケーションを追加するには、 **[新しいアプリケーション]** を選択します。
1. **[ギャラリーから追加する]** セクションで、検索ボックスに「**Change Process Management**」と入力します。
1. 結果パネルから **Change Process Management** を選択し、アプリを追加します。 お使いのテナントにアプリが追加されるのを数秒待機します。

## <a name="configure-and-test-azure-ad-sso-for-change-process-management"></a>Change Process Management の Azure AD SSO の構成とテスト

B.Simon というテスト ユーザーを使用して、Change Process Management に対する Azure AD SSO を構成してテストします。 SSO を機能させるためには、Azure AD ユーザーと Change Process Management 内の対応するユーザーとの間にリンク関係を確立する必要があります。

Change Process Management に対する Azure AD SSO を構成してテストするための大まかな手順は次のとおりです。

1. **[Azure AD SSO を構成](#configure-azure-ad-sso)** して、ユーザーがこの機能を使用できるようにします。
    1. Azure AD のシングル サインオンをテストするための **[Azure AD テスト ユーザーを作成](#create-an-azure-ad-test-user)** します。
    1. **[テスト ユーザーにアクセス権を付与](#grant-access-to-the-test-user)** して、Azure AD シングル サインオンをユーザーが使用できるようにします。
1. アプリケーション側で **[Change Process Management の SSO を構成](#configure-change-process-management-sso)** します。
    1. Azure AD のユーザーに対応するユーザーとして、 **[Change Process Management のテスト ユーザーを作成](#create-a-change-process-management-test-user)** します。
1. **[SSO をテスト](#test-sso)** して、構成が正しく機能することを確認します。

## <a name="configure-azure-ad-sso"></a>Azure AD SSO の構成

これらの手順に従って、Azure portal で Azure AD SSO を有効にします。

1. [Azure portal](https://portal.azure.com/) の **Change Process Management** アプリケーション統合ページで、 **[管理]** セクションの **[シングル サインオン]** を選択します。
1. **[シングル サインオン方式の選択]** ページで、 **[SAML]** を選択します。
1. **[SAML でシングル サインオンをセットアップします]** ページで、 **[基本的な SAML 構成]** の鉛筆ボタンを選択して設定を編集します。

   ![基本的な SAML 構成の鉛筆ボタン](common/edit-urls.png)

1. **[SAML でシングル サインオンをセットアップします]** ページで、次の手順を実行します。

    a. **[識別子]** ボックスに、`https://<hostname>:8443/` という形式で URL を入力します。

    b. **[応答 URL]** ボックスに、`https://<hostname>:8443/changepilot/saml/sso` という形式で URL を入力します。

    > [!NOTE]
    > 上記の **[識別子]** と **[応答 URL]** の値は、実際に使用すべき値ではありません。 実際の値を取得するには、[Change Process Management サポート チーム](mailto:support@realtech-us.com)にお問い合わせください。 Azure portal の **[基本的な SAML 構成]** セクションに示されているパターンを参照することもできます。

1. **[SAML でシングル サインオンをセットアップします]** ページの **[SAML 署名証明書]** セクションで、 **[証明書 (Base64)]** の **[ダウンロード]** リンクを選択し、証明書をダウンロードして、お使いのコンピューターに保存します。

    ![証明書のダウンロード リンク](common/certificatebase64.png)

1. **[Change Process Management のセットアップ]** セクションで、要件に基づいて適切な URL をコピーします。

    ![構成 URL のコピー](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Azure AD のテスト ユーザーの作成

このセクションでは、Azure portal で B. Simon というテスト ユーザーを作成します。

1. Azure portal の左ペインで、 **[Azure Active Directory]** を選択します。 **[ユーザー]** を選択し、 **[すべてのユーザー]** を選択します。
1. 画面の上部にある **[新しいユーザー]** を選択します。
1. **[ユーザー]** プロパティで、次の手順を実行します。
   1. **[名前]** ボックスに「**B.Simon**」と入力します。  
   1. **[ユーザー名]** ボックスに「\<username>@\<companydomain>.\<extension>」と入力します。 たとえば、「 `B.Simon@contoso.com` 」のように入力します。
   1. **[パスワードを表示]** をオンにし、 **[パスワード]** ボックスに表示された値を書き留めます。
   1. **［作成］** を選択します

### <a name="grant-access-to-the-test-user"></a>テスト ユーザーへのアクセス権の付与

このセクションでは、B.Simon に Change Process Management へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

1. Azure portal で **[エンタープライズ アプリケーション]** を選択し、 **[すべてのアプリケーション]** を選択します。
1. アプリケーションの一覧で **[Change Process Management]** を選択します。
1. アプリの概要ページの **[管理]** セクションで、 **[ユーザーとグループ]** を選択します。

   ![[ユーザーとグループ] の選択](common/users-groups-blade.png)

1. **[ユーザーの追加]** を選択し、 **[割り当ての追加]** ダイアログ ボックスで **[ユーザーとグループ]** を選択します。

    ![[ユーザーの追加] を選択する](common/add-assign-user.png)

1. **[ユーザーとグループ]** ダイアログ ボックスで、**ユーザー** の一覧で **[B.Simon]** を選択し、画面の下部にある **[選択]** ボタンをクリックします。
1. SAML アサーションにロール値が必要な場合は、 **[ロールの選択]** ダイアログ ボックスでユーザーに適したロールを一覧から選択し、画面の下部にある **[選択]** をクリックします。
1. **[割り当ての追加]** ダイアログ ボックスで **[割り当て]** を選びます。

## <a name="configure-change-process-management-sso"></a>Change Process Management の SSO の構成

Change Process Management 側でシングル サインオンを構成するには、ダウンロードした Base64 証明書と Azure portal からコピーした適切な URL を [Change Process Management サポート チーム](mailto:support@realtech-us.com)に送信する必要があります。 SAML SSO 接続が両側で正しく構成されます。

### <a name="create-a-change-process-management-test-user"></a>Change Process Management のテスト ユーザーの作成
 [Change Process Management サポート チーム](mailto:support@realtech-us.com)と連携して、B.Simon という名前のユーザーを Change Process Management に追加します。 シングル サインオンを使用する前に、ユーザーを作成し、有効化する必要があります。

## <a name="test-sso"></a>SSO のテスト 

このセクションでは、アクセス パネルを使用して Azure AD SSO の構成をテストします。

アクセス パネルで [Change Process Management] タイルを選択すると、SSO を設定した Change Process Management インスタンスに自動的にサインインします。 アクセス パネルの詳細については、[アクセス パネルの概要](../user-help/my-apps-portal-end-user-access.md)に関する記事を参照してください。

## <a name="additional-resources"></a>その他のリソース

- [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアル](./tutorial-list.md)

- [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)

- [Azure Active Directory の条件付きアクセスとは](../conditional-access/overview.md)

- [Azure AD で Change Process Management を試す](https://aad.portal.azure.com/)

- [Microsoft Cloud App Security におけるセッション制御とは](/cloud-app-security/proxy-intro-aad)

- [高度な可視性と制御によって Change Process Management を保護する方法](/cloud-app-security/proxy-intro-aad)