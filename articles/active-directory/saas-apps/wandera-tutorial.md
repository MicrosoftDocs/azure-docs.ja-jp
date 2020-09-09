---
title: チュートリアル:Azure Active Directory と Wandera RADAR Admin の統合 | Microsoft Docs
description: Azure Active Directory と Wandera RADAR Admin の間でシングル サインオンを構成する方法について確認します。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 08/27/2020
ms.author: jeedes
ms.openlocfilehash: c8ec4b68dc774b52ad621ff3e965481de680b6ff
ms.sourcegitcommit: 656c0c38cf550327a9ee10cc936029378bc7b5a2
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/28/2020
ms.locfileid: "89080001"
---
# <a name="tutorial-integrate-wandera-radar-admin-with-azure-active-directory"></a>チュートリアル:Wandera RADAR Admin と Azure Active Directory の統合

このチュートリアルでは、Wandera RADAR Admin と Azure Active Directory (Azure AD) を統合する方法について説明します。 Azure AD と Wandera RADAR Admin を統合すると、次のことができます。

* Wandera RADAR Admin にアクセスできるユーザーを Azure AD で制御します。
* ユーザーが自分の Azure AD アカウントを使用して Wandera RADAR Admin に自動的にサインインできるようにします。
* 1 つの中央サイト (Azure Portal) で自分のアカウントを管理します。

SaaS アプリと Azure AD の統合の詳細については、「[Azure Active Directory でのアプリケーションへのシングル サインオン](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)」を参照してください。

## <a name="prerequisites"></a>前提条件

開始するには、次が必要です。

* Azure AD サブスクリプション。 サブスクリプションがない場合は、[無料アカウント](https://azure.microsoft.com/free/)を取得できます。
* Wandera RADAR Admin でのシングル サインオン (SSO) が有効なサブスクリプション。

## <a name="scenario-description"></a>シナリオの説明

このチュートリアルでは、テスト環境で Azure AD の SSO を構成してテストします。

* Wandera RADAR Admin では、**IDP** initiated SSO がサポートされます
* Wandera RADAR Admin を構成したら、組織の機密データを流出と侵入からリアルタイムで保護するセッション制御を適用することができます。 セッション制御は、条件付きアクセスを拡張したものです。 [Microsoft Cloud App Security でセッション制御を強制する方法](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app)をご覧ください。

## <a name="adding-wandera-radar-admin-from-the-gallery"></a>ギャラリーからの Wandera RADAR Admin の追加

Azure AD への Wandera RADAR Admin の統合を構成するには、ギャラリーから管理対象 SaaS アプリの一覧に Wandera RADAR Admin を追加する必要があります。

1. 職場または学校アカウントか、個人の Microsoft アカウントを使用して、[Azure portal](https://portal.azure.com) にサインインします。
1. 左のナビゲーション ウィンドウで **[Azure Active Directory]** サービスを選択します。
1. **[エンタープライズ アプリケーション]** に移動し、 **[すべてのアプリケーション]** を選択します。
1. 新しいアプリケーションを追加するには、 **[新しいアプリケーション]** を選択します。
1. **[ギャラリーから追加する]** セクションで、検索ボックスに「**Wandera RADAR Admin**」と入力します。
1. 結果パネルから **[Wandera RADAR Admin]** を選択し、アプリを追加します。 お使いのテナントにアプリが追加されるのを数秒待機します。


## <a name="configure-and-test-azure-ad-sso"></a>Azure AD SSO の構成とテスト

**B.Simon** というテスト ユーザーを使用して、Wandera RADAR Admin に対する Azure AD SSO を構成してテストします。 SSO が機能するためには、Azure AD ユーザーと Wandera RADAR Admin の関連ユーザーの間でリンク関係を確立する必要があります。

Wandera RADAR Admin に対する Azure AD SSO を構成してテストするには、次の構成要素を完了します。

1. **[Azure AD SSO の構成](#configure-azure-ad-sso)** - ユーザーがこの機能を使用できるようにします。
   * **[Azure AD のテスト ユーザーの作成](#create-an-azure-ad-test-user)** - B.Simon で Azure AD のシングル サインオンをテストします。
   * **[Azure AD テスト ユーザーの割り当て](#assign-the-azure-ad-test-user)** - B.Simon が Azure AD シングル サインオンを使用できるようにします。
1. **[Wandera RADAR Admin SSO の構成](#configure-wandera-radar-admin-sso)** - アプリケーション側でシングル サインオン設定を構成します。
   * **[Wandera RADAR Admin のテスト ユーザーの作成](#create-wandera-radar-admin-test-user)** - Wandera RADAR Admin で B.Simon に対応するユーザーを作成し、Azure AD のこのユーザーにリンクさせます。
1. **[SSO のテスト](#test-sso)** - 構成が機能するかどうかを確認します。

### <a name="configure-azure-ad-sso"></a>Azure AD SSO の構成

これらの手順に従って、Azure portal で Azure AD SSO を有効にします。

1. [Azure portal](https://portal.azure.com/) の **Wandera RADAR Admin** アプリケーション統合ページで、 **[管理]** セクションを探して、 **[シングル サインオン]** を選択します。
1. **[シングル サインオン方式の選択]** ページで、 **[SAML]** を選択します。
1. **[SAML でシングル サインオンをセットアップします]** ページで、 **[基本的な SAML 構成]** の編集/ペン アイコンをクリックして設定を編集します。

   ![基本的な SAML 構成を編集する](common/edit-urls.png)

1. **[基本的な SAML 構成]** セクションで、次のフィールドの値を入力します。

    **[応答 URL]** ボックスに、`https://radar.wandera.com/saml/acs/<tenant id>` のパターンを使用して URL を入力します

    > [!NOTE]
    > この値は実際のものではありません。 実際の応答 URL でこの値を更新します。 この値を取得するには、[Wandera RADAR Admin クライアント サポート チーム](https://www.wandera.com/about-wandera/contact/#supportsection)にお問い合わせください。 Azure portal の **[基本的な SAML 構成]** セクションに示されているパターンを参照することもできます。

1. **[SAML でシングル サインオンをセットアップします]** ページの **[SAML 署名証明書]** セクションで、 **[フェデレーション メタデータ XML]** を探して **[ダウンロード]** を選択し、証明書をダウンロードして、お使いのコンピューターに保存します。

    ![証明書のダウンロードのリンク](common/metadataxml.png)

1. **[SAML でシングル サインオンをセットアップします]** ページで、**[SAML 署名証明書]** の編集/ペン アイコンをクリックして設定を編集します。

    ![署名オプション](common/signing-option.png)

    1. **[証明書オプション]** で **[SAML 応答とアサーションへの署名]** を選択します。

    1. **[署名アルゴリズム]** で **[SHA-256]** を選択します。

1. **[Wandera RADAR Admin のセットアップ]** セクションで、要件に基づいて適切な URL をコピーします。

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

このセクションでは、Wandera RADAR Admin へのアクセスを許可することで、B. Simon が Azure シングル サインオンを使用できるようにします。

1. Azure portal で **[エンタープライズ アプリケーション]** を選択し、 **[すべてのアプリケーション]** を選択します。
1. アプリケーションの一覧で **[Wandera RADAR Admin]** を選択します。
1. アプリの概要ページで、 **[管理]** セクションを見つけて、 **[ユーザーとグループ]** を選択します。

   ![[ユーザーとグループ] リンク](common/users-groups-blade.png)

1. **[ユーザーの追加]** を選択し、 **[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。

    ![[ユーザーの追加] リンク](common/add-assign-user.png)

1. **[ユーザーとグループ]** ダイアログの [ユーザー] の一覧から **[B.Simon]** を選択し、画面の下部にある **[選択]** ボタンをクリックします。
1. SAML アサーション内に任意のロール値が必要な場合、 **[ロールの選択]** ダイアログでユーザーに適したロールを一覧から選択し、画面の下部にある **[選択]** をクリックします。
1. **[割り当ての追加]** ダイアログで、 **[割り当て]** をクリックします。

## <a name="configure-wandera-radar-admin-sso"></a>Wandera RADAR Admin SSO の構成

1. Wandera RADAR Admin 内での構成を自動化するには、 **[拡張機能のインストール]** をクリックして **My Apps Secure Sign-in ブラウザー拡張機能**をインストールする必要があります。

    ![マイ アプリの拡張機能](common/install-myappssecure-extension.png)

2. ブラウザーに拡張機能を追加した後、 **[Wandera RADAR Admin のセットアップ]** をクリックすると、Wandera RADAR Admin アプリケーションに移動します。 そこから、管理者の資格情報を入力して Wandera RADAR Admin にサインインします。ブラウザー拡張機能によりアプリケーションが自動的に構成され、手順 3 から 4 が自動化されます。

    ![セットアップの構成](common/setup-sso.png)

3. Wandera RADAR Admin を手動でセットアップする場合は、新しい Web ブラウザー ウィンドウを開き、管理者として Wandera RADAR Admin 企業サイトにサインインして、次の手順を実行します。

4. ページの右上隅で、 **[設定]**  >  **[管理]**  >  **[シングル サインオン]** の順にクリックし、 **[Enable SAML 2.0]\(SAML 2.0 の有効化\)** オプションをオンにして、次の手順を実行します。

    ![Wandera RADAR Admin の構成](./media/wandera-tutorial/config01.png)

    a. **[Or manually enter the required fields]\(または必須フィールドを手動で入力する\)** をクリックします。

    b. **[IdP EntityId]** テキストボックスに、Azure portal からコピーした **Azure AD ID** の値を貼り付けます。

    c. フェデレーション メタデータ XML をメモ帳で開き、その内容をコピーして **[IdP Public X.509 Certificate]\(IdP パブリック X.509 証明書\)** ボックスに貼り付けます。

    d. **[保存]** をクリックします。

### <a name="create-wandera-radar-admin-test-user"></a>Wandera RADAR Admin のテスト ユーザーの作成

このセクションでは、Wandera RADAR Admin で B.Simon というユーザーを作成します。 [Wandera RADAR Admin サポート チーム](https://www.wandera.com/about-wandera/contact/#supportsection)と連携して、Wandera RADAR Admin プラットフォームにユーザーを追加します。 シングル サインオンを使用する前に、ユーザーを作成し、有効化する必要があります。

## <a name="test-sso"></a>SSO のテスト

このセクションでは、アクセス パネルを使用して Azure AD のシングル サインオン構成をテストします。

アクセス パネル上で [Wandera RADAR Admin] タイルをクリックすると、SSO を設定した Wandera RADAR Admin に自動的にサインインします。 アクセス パネルの詳細については、[アクセス パネルの概要](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)に関する記事を参照してください。

## <a name="additional-resources"></a>その他のリソース

- [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Azure Active Directory の条件付きアクセスとは](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

