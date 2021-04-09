---
title: チュートリアル:Azure Active Directory シングル サインオン (SSO) と Zscaler B2B ユーザー ポータルの統合 | Microsoft Docs
description: Azure Active Directory と Zscaler B2B ユーザー ポータルの間でシングル サインオンを構成する方法について説明します。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/24/2020
ms.author: jeedes
ms.openlocfilehash: f6b7fb79b5aab1ee3ea6b5b710c766c1a34b099c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "92519811"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-zscaler-b2b-user-portal"></a>チュートリアル:Azure Active Directory シングル サインオン (SSO) と Zscaler B2B ユーザー ポータルの統合

このチュートリアルでは、Zscaler B2B ユーザー ポータルと Azure Active Directory (Azure AD) を統合する方法について説明します。 Zscaler B2B ユーザー ポータルを Azure AD に統合すると、次のことを実行できます。

* Zscaler B2B ユーザー ポータルにアクセスするユーザーを Azure AD で管理します。
* ユーザーが自分の Azure AD アカウントを使用して Zscaler B2B ユーザー ポータルに自動的にサインインするようにできます。
* 1 つの中央サイト (Azure Portal) で自分のアカウントを管理します。

SaaS アプリと Azure AD の統合の詳細については、「[Azure Active Directory でのアプリケーションへのシングル サインオン](../manage-apps/what-is-single-sign-on.md)」を参照してください。

## <a name="prerequisites"></a>前提条件

開始するには、次が必要です。

* Azure AD サブスクリプション。 サブスクリプションがない場合は、[無料アカウント](https://azure.microsoft.com/free/)を取得できます。
* Zscaler B2B ユーザー ポータルでのシングル サインオン (SSO) が有効なサブスクリプション。

> [!NOTE]
> この統合は、Azure AD 米国政府クラウド環境から利用することもできます。 このアプリケーションは、Azure AD 米国政府クラウドのアプリケーション ギャラリーにあります。パブリック クラウドの場合と同じように構成してください。

## <a name="scenario-description"></a>シナリオの説明

このチュートリアルでは、テスト環境で Azure AD の SSO を構成してテストします。

* Zscaler B2B ユーザー ポータルでは、**IDP** Initiated SSO がサポートされます

* Zscaler B2B ユーザー ポータルでは、**Just-In-Time** ユーザー プロビジョニングがサポートされます

* Zscaler B2B ユーザー ポータルを構成したら、組織の機密データを流出と侵入からリアルタイムで保護するセッション制御を適用することができます。 セッション制御は、条件付きアクセスを拡張したものです。 [Microsoft Cloud App Security でセッション制御を適用する方法](/cloud-app-security/proxy-deployment-aad)をご覧ください。

## <a name="adding-zscaler-b2b-user-portal-from-the-gallery"></a>ギャラリーからの Zscaler B2B ユーザー ポータルの追加

Azure AD への Zscaler B2B ユーザー ポータルの統合を構成するには、管理対象 SaaS アプリ一覧に Zscaler B2B ユーザー ポータルをギャラリーから追加する必要があります。

1. 職場または学校アカウントか、個人の Microsoft アカウントを使用して、[Azure portal](https://portal.azure.com) にサインインします。
1. 左のナビゲーション ウィンドウで **[Azure Active Directory]** サービスを選択します。
1. **[エンタープライズ アプリケーション]** に移動し、 **[すべてのアプリケーション]** を選択します。
1. 新しいアプリケーションを追加するには、 **[新しいアプリケーション]** を選択します。
1. **[ギャラリーから追加する]** セクションで、検索ボックスに「**Zscaler B2B ユーザー ポータル**」と入力します。
1. 結果のパネルから **[Zscaler B2B ユーザー ポータル]** を選択し、アプリを追加します。 お使いのテナントにアプリが追加されるのを数秒待機します。

## <a name="configure-and-test-azure-ad-single-sign-on-for-zscaler-b2b-user-portal"></a>Zscaler B2B ユーザー ポータルの Azure AD シングル サインオンの構成とテスト

**B.Simon** というテスト ユーザーを使用して、Zscaler B2B ユーザー ポータルに対する Azure AD SSO を構成してテストします。 SSO を機能させるためには、Azure AD ユーザーと Zscaler B2B ユーザー ポータルの関連ユーザーとの間にリンク関係を確立する必要があります。

Zscaler B2B ユーザー ポータルで Azure AD SSO を構成してテストするには、次の構成要素を完了する必要があります。

1. **[Azure AD SSO の構成](#configure-azure-ad-sso)** - ユーザーがこの機能を使用できるようにします。
    1. **[Azure AD のテスト ユーザーの作成](#create-an-azure-ad-test-user)** - B.Simon で Azure AD のシングル サインオンをテストします。
    1. **[Azure AD テスト ユーザーの割り当て](#assign-the-azure-ad-test-user)** - B.Simon が Azure AD シングル サインオンを使用できるようにします。
1. **[Zscaler B2B ユーザー ポータル SSO の構成](#configure-zscaler-b2b-user-portal-sso)** - アプリケーション側でシングル サインオン設定を構成します。
    1. **[Zscaler B2B ユーザー ポータルのテスト ユーザーの作成](#create-zscaler-b2b-user-portal-test-user)** - Zscaler B2B ユーザー ポータルで B.Simon に対応するユーザーを作成し、Azure AD のこのユーザーにリンクさせます。
1. **[SSO のテスト](#test-sso)** - 構成が機能するかどうかを確認します。

## <a name="configure-azure-ad-sso"></a>Azure AD SSO の構成

これらの手順に従って、Azure portal で Azure AD SSO を有効にします。

1. [Azure portal](https://portal.azure.com/) の **Zscaler B2B ユーザー ポータル** アプリケーション統合ページで、 **[管理]** セクションを見つけて、 **[シングル サインオン]** を選択します。
1. **[シングル サインオン方式の選択]** ページで、 **[SAML]** を選択します。
1. **[SAML でシングル サインオンをセットアップします]** ページで、 **[基本的な SAML 構成]** の編集 (ペン) アイコンをクリックして設定を編集します。

   ![基本的な SAML 構成を編集する](common/edit-urls.png)

1. **[SAML でシングル サインオンをセットアップします]** ページで、次のフィールドの値を入力します。

    a. **[識別子]** ボックスに、`https://samlsp.private.zscaler.com/auth/metadata/<UniqueID>` の形式で URL を入力します。

    b. **[応答 URL]** ボックスに、`https://samlsp.private.zscaler.com/auth/login?domain=EXAMPLE` のパターンを使用して URL を入力します

    > [!NOTE]
    > これらは実際の値ではありません。 実際の識別子と応答 URL でこれらの値を更新します。 これらの値を取得するには、[Zscaler B2B ユーザー ポータル クライアント サポート チーム](https://help.zscaler.com/)に連絡してください。 Azure portal の **[基本的な SAML 構成]** セクションに示されているパターンを参照することもできます。

1. **[SAML でシングル サインオンをセットアップします]** ページの **[SAML 署名証明書]** セクションで、 **[フェデレーション メタデータ XML]** を探して **[ダウンロード]** を選択し、証明書をダウンロードして、お使いのコンピューターに保存します。

    ![証明書のダウンロードのリンク](common/metadataxml.png)

1. **[Zscaler B2B ユーザー ポータルのセットアップ]** セクションで、要件に基づいて適切な URL をコピーします。

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

このセクションでは、B.Simon に Zscaler B2B ユーザー ポータルへのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

1. Azure portal で **[エンタープライズ アプリケーション]** を選択し、 **[すべてのアプリケーション]** を選択します。
1. アプリケーションの一覧で、 **[Zscaler B2B ユーザー ポータル]** を選択します。
1. アプリの概要ページで、 **[管理]** セクションを見つけて、 **[ユーザーとグループ]** を選択します。

   ![[ユーザーとグループ] リンク](common/users-groups-blade.png)

1. **[ユーザーの追加]** を選択し、 **[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。

    ![[ユーザーの追加] リンク](common/add-assign-user.png)

1. **[ユーザーとグループ]** ダイアログの [ユーザー] の一覧から **[B.Simon]** を選択し、画面の下部にある **[選択]** ボタンをクリックします。
1. SAML アサーション内に任意のロール値が必要な場合、 **[ロールの選択]** ダイアログでユーザーに適したロールを一覧から選択し、画面の下部にある **[選択]** をクリックします。
1. **[割り当ての追加]** ダイアログで、 **[割り当て]** をクリックします。

## <a name="configure-zscaler-b2b-user-portal-sso"></a>Zscaler B2B ユーザー ポータル SSO の構成

1. 新しい Web ブラウザー ウィンドウを開き、Zscaler B2B ユーザー ポータルの企業サイトに管理者としてサインインして、次の手順を実行します。

1. メニュー左側の **[Administration]\(管理\)** をクリックして **[AUTHENTICATION]\(認証\)** セクションに移動し、 **[IdP Configuration]\(IdP 構成\)** をクリックします。

    ![Zscaler Private Access Administrator の [Administration]\(管理\)](./media/zscaler-b2b-user-tutorial/tutorial-zscaler-private-access-administration.png)

1. 右上隅の **[Add IdP Configuration]** をクリックします。 

    ![Zscaler Private Access Administrator の IdP](./media/zscaler-b2b-user-tutorial/tutorial-zscaler-private-access-idp.png)

1. **[Add IdP Configuration]** ページで、次の手順に従います。
 
    ![Zscaler Private Access Administrator での選択](./media/zscaler-b2b-user-tutorial/tutorial-zscaler-private-access-select.png)

    a. **[IdP Metadata File Upload]** フィールドの **[Select File]** をクリックして、Azure AD からダウンロードしたメタデータ ファイルをアップロードします。

    b. Azure AD から **IdP メタデータ** が読み取られ、以下に示すようにすべてのフィールド情報が設定されます。

    ![Zscaler Private Access Administrator での構成](./media/zscaler-b2b-user-tutorial/config.png)

    c. **[Domains]** フィールドから自分のドメインを選択します。
    
    d. **[保存]** をクリックします。

### <a name="create-zscaler-b2b-user-portal-test-user"></a>Zscaler B2B ユーザー ポータルのテスト ユーザーを作成する

このセクションでは、Britta Simon というユーザーを Zscaler B2B ユーザー ポータルに作成します。 Zscaler B2B ユーザー ポータルでは、Just-In-Time ユーザー プロビジョニングがサポートされています。この設定は既定で有効になっています。 このセクションでは、ユーザー側で必要な操作はありません。 Zscaler B2B ユーザー ポータルにユーザーがまだ存在していない場合は、認証後に新規に作成されます。

## <a name="test-sso"></a>SSO のテスト 

このセクションでは、アクセス パネルを使用して Azure AD のシングル サインオン構成をテストします。

アクセス パネルで [Zscaler B2B ユーザー ポータル] タイルをクリックすると、SSO を設定した Zscaler B2B ユーザー ポータルに自動的にサインインします。 アクセス パネルの詳細については、[アクセス パネルの概要](../user-help/my-apps-portal-end-user-access.md)に関する記事を参照してください。

## <a name="additional-resources"></a>その他のリソース

- [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](./tutorial-list.md)

- [Azure Active Directory でのアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)

- [Azure Active Directory の条件付きアクセスとは](../conditional-access/overview.md)

- [Azure AD で Zscaler B2B ユーザー ポータルを試す](https://aad.portal.azure.com/)

- [Microsoft Cloud App Security におけるセッション制御とは](/cloud-app-security/proxy-intro-aad)