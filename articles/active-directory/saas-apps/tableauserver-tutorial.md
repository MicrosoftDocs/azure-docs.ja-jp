---
title: チュートリアル:Azure Active Directory シングル サインオン (SSO) と Tableau Server の統合 | Microsoft Docs
description: Azure Active Directory と Tableau Server の間でシングル サインオンを構成する方法について説明します。
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: c1917375-08aa-445c-a444-e22e23fa19e0
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 09/09/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: b07c6d6317d8ca284008d271f507b965414bfbc1
ms.sourcegitcommit: 55f7fc8fe5f6d874d5e886cb014e2070f49f3b94
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/25/2019
ms.locfileid: "71257498"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-tableau-server"></a>チュートリアル:Azure Active Directory シングル サインオン (SSO) と Tableau Server の統合

このチュートリアルでは、Tableau Server と Azure Active Directory (Azure AD) を統合する方法について説明します。 Azure AD と Tableau Server を統合すると、次のことができます。

* Tableau Server にアクセスできるユーザーを Azure AD で制御できます。
* ユーザーが自分の Azure AD アカウントを使用して Tableau Server に自動的にサインインできるように設定できます。
* 1 つの中央サイト (Azure Portal) で自分のアカウントを管理できます。

SaaS アプリと Azure AD の統合の詳細については、「[Azure Active Directory でのアプリケーションへのシングル サインオン](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)」を参照してください。

## <a name="prerequisites"></a>前提条件

開始するには、次が必要です。

* Azure AD サブスクリプション。 サブスクリプションがない場合は、[無料アカウント](https://azure.microsoft.com/free/)を取得できます。
* Tableau Server でのシングル サインオン (SSO) が有効なサブスクリプション。

## <a name="scenario-description"></a>シナリオの説明

このチュートリアルでは、テスト環境で Azure AD の SSO を構成してテストします。

* Tableau Server では、**SP** によって開始される SSO がサポートされます

## <a name="adding-tableau-server-from-the-gallery"></a>ギャラリーから Tableau Server を追加する

Azure AD への Tableau Server の統合を構成するには、ギャラリーから管理対象 SaaS アプリの一覧に Tableau Server を追加する必要があります。

1. 職場または学校アカウントか、個人の Microsoft アカウントを使用して、[Azure portal](https://portal.azure.com) にサインインします。
1. 左のナビゲーション ウィンドウで **[Azure Active Directory]** サービスを選択します。
1. **[エンタープライズ アプリケーション]** に移動し、 **[すべてのアプリケーション]** を選択します。
1. 新しいアプリケーションを追加するには、 **[新しいアプリケーション]** を選択します。
1. **[ギャラリーから追加する]** セクションで、検索ボックスに、「**Tableau Server**」と入力します。
1. 結果パネルで **[Tableau Server]** を選択し、アプリを追加します。 お使いのテナントにアプリが追加されるのを数秒待機します。

## <a name="configure-and-test-azure-ad-single-sign-on-for-tableau-server"></a>Tableau Server の Azure AD シングル サインオンの構成とテスト

**B.Simon** というテスト ユーザーを使用して、Tableau Server に対する Azure AD SSO を構成してテストします。 SSO が機能するためには、Azure AD ユーザーと Tableau Server の関連ユーザーとの間にリンク関係を確立する必要があります。

Tableau Server に対する Azure AD SSO を構成してテストするには、次の構成要素を完了します。

1. **[Azure AD SSO の構成](#configure-azure-ad-sso)** - ユーザーがこの機能を使用できるようにします。
    1. **[Azure AD のテスト ユーザーの作成](#create-an-azure-ad-test-user)** - B.Simon で Azure AD のシングル サインオンをテストします。
    1. **[Azure AD テスト ユーザーの割り当て](#assign-the-azure-ad-test-user)** - B.Simon が Azure AD シングル サインオンを使用できるようにします。
1. **[Tableau Server SSO の構成](#configure-tableau-server-sso)** - アプリケーション側でシングル サインオン設定を構成します。
    1. **[Tableau Server のテスト ユーザーの作成](#create-tableau-server-test-user)** - Tableau Server で B.Simon に対応するユーザーを作成し、Azure AD のこのユーザーにリンクさせます。
1. **[SSO のテスト](#test-sso)** - 構成が機能するかどうかを確認します。

## <a name="configure-azure-ad-sso"></a>Azure AD SSO の構成

これらの手順に従って、Azure portal で Azure AD SSO を有効にします。

1. [Azure portal](https://portal.azure.com/) の **Tableau Server** アプリケーション統合ページで、 **[管理]** セクションを見つけて、 **[シングル サインオン]** を選択します。
1. **[シングル サインオン方式の選択]** ページで、 **[SAML]** を選択します。
1. **[SAML でシングル サインオンをセットアップします]** ページで、 **[基本的な SAML 構成]** の編集 (ペン) アイコンをクリックして設定を編集します。

   ![基本的な SAML 構成を編集する](common/edit-urls.png)

1. **[基本的な SAML 構成]** セクションで、次のフィールドの値を入力します。

    a. **[サインオン URL]** ボックスに、`https://azure.<domain name>.link` という形式で URL を入力します。

    b. **[識別子]** ボックスに、`https://azure.<domain name>.link` という形式で URL を入力します。

    c. **[応答 URL]** ボックスに、`https://azure.<domain name>.link/wg/saml/SSO/index.html` のパターンを使用して URL を入力します

    > [!NOTE]
    > 上記の値は、実際の値ではありません。 [Tableau Server Configiuration]\(Tableau Server の構成) ページから入手した実際の URL と識別子で値を更新します。これについてはこのチュートリアルで後ほど説明します。

1. **[SAML でシングル サインオンをセットアップします]** ページの **[SAML 署名証明書]** セクションで、 **[フェデレーション メタデータ XML]** を探して **[ダウンロード]** を選択し、証明書をダウンロードして、お使いのコンピューターに保存します。

    ![証明書のダウンロードのリンク](common/metadataxml.png)

1. **[Tableau Server のセットアップ]** セクションで、要件に基づいて適切な URL をコピーします。

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

このセクションでは、B.Simon に Tableau Server へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

1. Azure portal で **[エンタープライズ アプリケーション]** を選択し、 **[すべてのアプリケーション]** を選択します。
1. アプリケーションの一覧で **[Tableau Server]** を選択します。
1. アプリの概要ページで、 **[管理]** セクションを見つけて、 **[ユーザーとグループ]** を選択します。

   ![[ユーザーとグループ] リンク](common/users-groups-blade.png)

1. **[ユーザーの追加]** を選択し、 **[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。

    ![[ユーザーの追加] リンク](common/add-assign-user.png)

1. **[ユーザーとグループ]** ダイアログの [ユーザー] の一覧から **[B.Simon]** を選択し、画面の下部にある **[選択]** ボタンをクリックします。
1. SAML アサーション内に任意のロール値が必要な場合、 **[ロールの選択]** ダイアログでユーザーに適したロールを一覧から選択し、画面の下部にある **[選択]** をクリックします。
1. **[割り当ての追加]** ダイアログで、 **[割り当て]** をクリックします。

## <a name="configure-tableau-server-sso"></a>Tableau Server SSO の構成

1. アプリケーションに合わせて SSO を構成するには、管理者として Tableau Server テナントにサインインする必要があります。

2. **[構成]** タブで、 **[User Identity & Access]\(ユーザー ID とアクセス\)** を選択し、 **[Authentication Method]\(認証方法\)** タブを選択します。

    ![Configure single sign-on](./media/tableauserver-tutorial/tutorial-tableauserver-auth.png)

3. **[CONFIGURATION]\(構成\)** ページで、次の手順を実行します。

    ![Configure single sign-on](./media/tableauserver-tutorial/tutorial-tableauserver-config.png)

    a. **[Authentication Method]\(認証方法\)** として SAML を選択します。

    b. **[Enable SAML Authentication for the server]\(サーバーの SAML 認証を有効にする\)** のチェック ボックスをオンにします。

    c. [Tableau Server return URL]\(Tableau Server の戻り先 URL\): Tableau Server ユーザーがアクセスする URL (<http://tableau_server> など)。 `http://localhost` の使用は推奨されません。 末尾にスラッシュが付いている URL (例: `http://tableau_server/`) はサポートされていません。 **Tableau Server の戻り先 URL** をコピーし、Azure portal の **[基本的な SAML 構成]** セクションの **[サインオン URL]** ボックスに貼り付けます

    d. [SAML entity ID]: IdP に対して Tableau Server のインストールを一意に識別するエンティティ ID。 必要に応じてこの欄にも Tableau Server URL を入力できますが、使用する Tableau Server URL にする必要はありません。 **SAML エンティティ ID** をコピーし、Azure portal の **[基本的な SAML 構成]** セクションの **[識別子]** ボックスに貼り付けます

    e. **[Download XML Metadata File]\(XML メタデータ ファイルのダウンロード\)** をクリックし、テキスト エディター アプリケーションで開きます。 Http Post で Index 0 の [Assertion Consumer Service URL] を探し、URL をコピーします。 これを、Azure portal の **[基本的な SAML 構成]** セクションの **[応答 URL]** ボックスに貼り付けます

    f. Azure Portal からダウンロードしたフェデレーション メタデータ ファイルを検索し、 **[SAML Idp metadata file]\(SAML Idp メタデータ ファイル\)** でアップロードします。

    g. ユーザー名、表示名、メール アドレスを IdP が保持するために使用する属性の名前を入力します。

    h. **[保存]**

    > [!NOTE]
    > 顧客は任意の証明書を Tableau Server の SAML SSO 構成でアップロードする必要があります。SSO フローではその証明書は無視されます。 Tableau Server で SAML を構成する方法について不明な点がある場合は、[SAML の構成](https://help.tableau.com/current/online/en-us/saml_config_azure_ad.htm)に関する記事を参照してください。

### <a name="create-tableau-server-test-user"></a>Tableau Server のテスト ユーザーの作成

このセクションの目的は、Tableau Server で B.Simon というユーザーを作成することです。 Tableau Server 内のすべてのユーザーをプロビジョニングする必要があります。

また、ユーザーのユーザー名は、Azure AD のカスタム属性 **username** で構成した値と一致する必要があります。 正しい対応付けがあれば、統合で Azure AD シングル サインオンの構成が機能します。

> [!NOTE]
> ユーザーを手動で作成する必要がある場合は、組織の Tableau Server 管理者に問い合わせてください。

## <a name="test-sso"></a>SSO のテスト 

このセクションでは、アクセス パネルを使用して Azure AD のシングル サインオン構成をテストします。

アクセス パネル上で [Tableau Server] タイルをクリックすると、SSO を設定した Tableau Server に自動的にサインインします。 アクセス パネルの詳細については、[アクセス パネルの概要](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)に関する記事を参照してください。

## <a name="additional-resources"></a>その他のリソース

- [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Azure Active Directory でのアプリケーション アクセスとシングル サインオンとは](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Azure Active Directory の条件付きアクセスとは](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Azure AD を使用して Tableau Server を試す](https://aad.portal.azure.com/)