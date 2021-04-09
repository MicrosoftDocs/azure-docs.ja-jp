---
title: チュートリアル:Azure Active Directory シングル サインオン (SSO) と Hub Planner の統合 | Microsoft Docs
description: Azure Active Directory と Hub Planner の間でシングル サインオンを構成する方法について説明します。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 06/12/2020
ms.author: jeedes
ms.openlocfilehash: 2d9dc483a9d60dc395c0aff52b721690d4fc701d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "92442441"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-hub-planner"></a>チュートリアル:Azure Active Directory シングル サインオン (SSO) と Hub Planner の統合

このチュートリアルでは、Hub Planner と Azure Active Directory (Azure AD) を統合する方法について説明します。 Azure AD と Hub Planner を統合すると、次のことができます。

* Hub Planner にアクセスできるユーザーを Azure AD で制御できます。
* ユーザーが自分の Azure AD アカウントを使用して Hub Planner に自動的にサインインできるように設定できます。
* 1 つの中央サイト (Azure Portal) で自分のアカウントを管理します。

SaaS アプリと Azure AD の統合の詳細については、「[Azure Active Directory でのアプリケーションへのシングル サインオン](../manage-apps/what-is-single-sign-on.md)」を参照してください。

## <a name="prerequisites"></a>前提条件

開始するには、次が必要です。

* Azure AD サブスクリプション。 サブスクリプションがない場合は、[無料アカウント](https://azure.microsoft.com/free/)を取得できます。
* Hub Planner でのシングル サインオン (SSO) が有効なサブスクリプション。

## <a name="scenario-description"></a>シナリオの説明

このチュートリアルでは、テスト環境で Azure AD の SSO を構成してテストします。

* Hub Planner では、**SP** Initiated SSO がサポートされます。
* Hub Planner を構成したら、組織の機密データを流出と侵入からリアルタイムで保護するセッション制御を適用することができます。 セッション制御は、条件付きアクセスを拡張したものです。 [Microsoft Cloud App Security でセッション制御を強制する方法](/cloud-app-security/proxy-deployment-any-app)をご覧ください。

## <a name="adding-hub-planner-from-the-gallery"></a>ギャラリーからの Hub Planner の追加

Azure AD への Hub Planner の統合を構成するには、ギャラリーからマネージド SaaS アプリの一覧に Hub Planner を追加する必要があります。

1. 職場または学校アカウントか、個人の Microsoft アカウントを使用して、[Azure portal](https://portal.azure.com) にサインインします。
1. 左のナビゲーション ウィンドウで **[Azure Active Directory]** サービスを選択します。
1. **[エンタープライズ アプリケーション]** に移動し、 **[すべてのアプリケーション]** を選択します。
1. 新しいアプリケーションを追加するには、 **[新しいアプリケーション]** を選択します。
1. **[ギャラリーから追加する]** セクションで、検索ボックスに「**Hub Planner**」と入力します。
1. 結果のパネルから **[Hub Planner]** を選択し、アプリを追加します。 お使いのテナントにアプリが追加されるのを数秒待機します。


## <a name="configure-and-test-azure-ad-single-sign-on-for-hub-planner"></a>Hub Planner の Azure AD シングル サインオンの構成とテスト

**B.Simon** というテスト ユーザーを使用して、Hub Planner に対する Azure AD SSO を構成してテストします。 SSO が機能するためには、Azure AD ユーザーと Hub Planner の関連ユーザーとの間にリンク関係を確立する必要があります。

Hub Planner で Azure AD SSO を構成してテストするには、次の構成要素を完了する必要があります。

1. **[Azure AD SSO の構成](#configure-azure-ad-sso)** - ユーザーがこの機能を使用できるようにします。
    1. **[Azure AD のテスト ユーザーの作成](#create-an-azure-ad-test-user)** - B.Simon で Azure AD のシングル サインオンをテストします。
    1. **[Azure AD テスト ユーザーの割り当て](#assign-the-azure-ad-test-user)** - B.Simon が Azure AD シングル サインオンを使用できるようにします。
1. **[Hub Planner の SSO の構成](#configure-hub-planner-sso)** - アプリケーション側でシングル サインオン設定を構成します。
    1. **[Hub Planner のテスト ユーザーの作成](#create-hub-planner-test-user)** - Hub Planner で B.Simon に対応するユーザーを作成し、Azure AD の B.Simon にリンクさせます。
1. **[SSO のテスト](#test-sso)** - 構成が機能するかどうかを確認します。

## <a name="configure-azure-ad-sso"></a>Azure AD SSO の構成

これらの手順に従って、Azure portal で Azure AD SSO を有効にします。

1. [Azure portal](https://portal.azure.com/) の **Hub Planner** アプリケーション統合ページで、 **[管理]** セクションを見つけて、 **[シングル サインオン]** を選択します。
1. **[シングル サインオン方式の選択]** ページで、 **[SAML]** を選択します。
1. **[SAML でシングル サインオンをセットアップします]** ページで、 **[基本的な SAML 構成]** の編集 (ペン) アイコンをクリックして設定を編集します。

   ![基本的な SAML 構成を編集する](common/edit-urls.png)

1. **[基本的な SAML 構成]** セクションで、次のフィールドの値を入力します。

    a. **[サインオン URL]** ボックスに、`https://<SUBDOMAIN>.hubplanner.com` という形式で URL を入力します。

    b. **[識別子]** ボックスに、`https://app.hubplanner.com/sso/metadata` という形式で URL を入力します。

    c. **[応答 URL]** ボックスに、`https://app.hubplanner.com/sso/callback` のパターンを使用して URL を入力します

    > [!NOTE]
    > これらの値は、後ほど使用します。 必要な変更点は、 **[サインオン URL]** の \<SUBDOMAIN\> を、Hub Planner にサインアップしたときに受け取ったサブドメインで置き換えることだけです。 Azure portal の **[基本的な SAML 構成]** セクションに示されているパターンを参照することもできます。

1. **[SAML でシングル サインオンをセットアップします]** ページの **[SAML 署名証明書]** セクションで、 **[証明書 (Base64)]** を見つけて、 **[ダウンロード]** を選択し、証明書をダウンロードして、お使いのコンピューターに保存します。

    ![証明書のダウンロードのリンク](common/certificatebase64.png)

1. **[Set up Hub Planner]\(Hub Planner の設定\)** セクションで、要件に基づいて適切な URL をコピーします。

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

このセクションでは、B.Simon に Hub Planner へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

1. Azure portal で **[エンタープライズ アプリケーション]** を選択し、 **[すべてのアプリケーション]** を選択します。
1. アプリケーションの一覧で **[Hub Planner]** を選択します。
1. アプリの概要ページで、 **[管理]** セクションを見つけて、 **[ユーザーとグループ]** を選択します。

   ![[ユーザーとグループ] リンク](common/users-groups-blade.png)

1. **[ユーザーの追加]** を選択し、 **[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。

    ![[ユーザーの追加] リンク](common/add-assign-user.png)

1. **[ユーザーとグループ]** ダイアログの [ユーザー] の一覧から **[B.Simon]** を選択し、画面の下部にある **[選択]** ボタンをクリックします。
1. SAML アサーション内に任意のロール値が必要な場合、 **[ロールの選択]** ダイアログでユーザーに適したロールを一覧から選択し、画面の下部にある **[選択]** をクリックします。
1. **[割り当ての追加]** ダイアログで、 **[割り当て]** をクリックします。

## <a name="configure-hub-planner-sso"></a>Hub Planner の SSO の構成

**Hub Planner** 側にシングル サインオンを構成するには、Hub Planner アカウントにサインインし、次のタスクを完了する必要があります。 

### <a name="install-the-extension-in-hub-planner"></a>Hub Planner に拡張機能をインストールする

SSO 機能を有効にするには、まず拡張機能を有効にする必要があります。 アカウント オーナーとして、または同等のアクセス許可があるアカウントで、次の手順を実行します。

1. **[設定]** に移動します。
1. サイド メニューで、 **[拡張機能の管理]**  >  **[Add/Remove Extensions]\(拡張機能の追加と削除\)** を選択します。
1. シングル サインオンの拡張機能を見つけて追加するか、無料で試します。
1. 確認を求められたら、利用条件に同意して **[今すぐ追加]** を選択します。

### <a name="enable-sso"></a>SSO を有効にする

拡張機能を有効にしたら、アカウントの SSO を有効にする必要があります。 

1. **[設定]** に移動します。
1. サイド メニューで、 **[認証]** を選択します。
1. **[SSO (Single Sign-On)]\(SSO (シングル サインオン)\)** を選択します。
1. 次の図のように詳しい認証情報を入力して、 **[保存]** を選択します。

![SSO の設定のスクリーンショット](media/hub-planner-tutorial/sso-settings.png)

### <a name="create-hub-planner-test-user"></a>Hub Planner のテスト ユーザーの作成

他のユーザーを追加したい場合は、 **[設定]**  >  **[リソースの管理]** に移動し、そこからユーザーを追加します。 必ずそれらのユーザーのメール アドレスを追加して招待してください。 招待されたユーザーはメールを受信したうえで、SSO を介してアクセスできるようになります。 

## <a name="test-sso"></a>SSO のテスト 

このセクションでは、アクセス パネルを使用して Azure AD のシングル サインオン構成をテストします。

アクセス パネル上で [Hub Planner] タイルをクリックすると、SSO を設定した Hub Planner に自動的にサインインします。 アクセス パネルの詳細については、[アクセス パネルの概要](../user-help/my-apps-portal-end-user-access.md)に関する記事を参照してください。

## <a name="additional-resources"></a>その他のリソース

- [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](./tutorial-list.md)

- [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)

- [Azure Active Directory の条件付きアクセスとは](../conditional-access/overview.md)

- [Azure AD で Hub Planner を試す](https://aad.portal.azure.com/)

- [Microsoft Cloud App Security におけるセッション制御とは](/cloud-app-security/proxy-intro-aad)

- [高度な可視性と制御によって Hub Planner を保護する方法](/cloud-app-security/proxy-intro-aad)