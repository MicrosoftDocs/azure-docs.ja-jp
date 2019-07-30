---
title: チュートリアル:Azure Active Directory と Zscaler Private Access (ZPA) の統合 | Microsoft Docs
description: Azure Active Directory と Zscaler Private Access (ZPA) の間でシングル サインオンを構成する方法について説明します。
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: celested
ms.assetid: 83711115-1c4f-4dd7-907b-3da24b37c89e
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 05/23/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: e0a1538f640bb4722eca1d4f3a80125837593bab
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/13/2019
ms.locfileid: "67085723"
---
# <a name="tutorial-integrate-zscaler-private-access-zpa-with-azure-active-directory"></a>チュートリアル:Azure Active Directory と Zscaler Private Access (ZPA) の統合

このチュートリアルでは、Zscaler Private Access (ZPA) と Azure Active Directory (Azure AD) を統合する方法について説明します。 Zscaler Private Access (ZPA) を Azure AD に統合すると、次のことができます。

* Azure AD で、Zscaler Private Access (ZPA) にアクセスできるユーザーを制御できます。
* ユーザーが自分の Azure AD アカウントで Zscaler Private Access (ZPA) に自動的にサインインするように設定できます。
* 1 つの中央サイト (Azure Portal) で自分のアカウントを管理できます。

SaaS アプリと Azure AD の統合の詳細については、「[Azure Active Directory でのアプリケーションへのシングル サインオン](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)」を参照してください。

## <a name="prerequisites"></a>前提条件

開始するには、次が必要です。

* Azure AD サブスクリプション。 サブスクリプションがない場合は、[無料アカウント](https://azure.microsoft.com/free/)を取得できます。
* シングル サインオン (SSO) が有効な Zscaler Private Access (ZPA) サブスクリプション。

## <a name="scenario-description"></a>シナリオの説明

このチュートリアルでは、テスト環境で Azure AD の SSO を構成してテストします。 Zscaler Private Access (ZPA) では **SP** Initiated SSO がサポートされます。

## <a name="adding-zscaler-private-access-zpa-from-the-gallery"></a>ギャラリーからの Zscaler Private Access (ZPA) の追加

Azure AD への Zscaler Private Access (ZPA) の統合を構成するには、ギャラリーから管理対象 SaaS アプリの一覧に Zscaler Private Access (ZPA) を追加する必要があります。

1. 職場または学校アカウントか、個人の Microsoft アカウントを使用して、[Azure portal](https://portal.azure.com) にサインインします。
1. 左のナビゲーション ウィンドウで **[Azure Active Directory]** サービスを選択します。
1. **[エンタープライズ アプリケーション]** に移動し、 **[すべてのアプリケーション]** を選択します。
1. 新しいアプリケーションを追加するには、 **[新しいアプリケーション]** を選択します。
1. **[ギャラリーから追加する]** セクションで、検索ボックスに「**Zscaler Private Access (ZPA)** 」と入力します。
1. 結果のパネルから **[Zscaler Private Access (ZPA)]** を選択し、アプリを追加します。 お使いのテナントにアプリが追加されるのを数秒待機します。

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成とテスト

**Britta Simon** というテスト ユーザーを使用して、Zscaler Private Access (ZPA) に対する Azure AD SSO を構成し、テストします。 SSO が機能するために、Azure AD ユーザーと Zscaler Private Access (ZPA) の関連ユーザーの間で、リンク関係を確立する必要があります。

Zscaler Private Access (ZPA) に対する Azure AD SSO を構成してテストするには、次の構成要素を完了する必要があります。

1. **[Azure AD SSO の構成](#configure-azure-ad-sso)** - ユーザーがこの機能を使用できるようにします。
2. **[Zscaler Private Access (ZPA) の構成](#configure-zscaler-private-access-zpa)** - アプリケーション側で SSO 設定を構成します。
3. **[Azure AD のテスト ユーザーの作成](#create-an-azure-ad-test-user)** - Britta Simon で Azure AD のシングル サインオンをテストします。
4. **[Azure AD テスト ユーザーの割り当て](#assign-the-azure-ad-test-user)** - Britta Simon が Azure AD シングル サインオンを使用できるようにします。
5. **[Zscaler Private Access (ZPA) のテスト ユーザーの作成](#create-zscaler-private-access-zpa-test-user)** - Zscaler Private Access (ZPA) で Britta Simon に対応するユーザーを作成し、Azure AD の Britta Simon にリンクさせます。
6. **[SSO のテスト](#test-sso)** - 構成が機能するかどうかを確認します。

### <a name="configure-azure-ad-sso"></a>Azure AD SSO の構成

これらの手順に従って、Azure portal で Azure AD SSO を有効にします。

1. [Azure portal](https://portal.azure.com/) の **Zscaler Private Access (ZPA)** アプリケーション統合ページで、 **[管理]** セクションを探して、 **[シングル サインオン]** を選択します。
1. **[シングル サインオン方式の選択]** ページで、 **[SAML]** を選択します。
1. **[SAML でシングル サインオンをセットアップします]** ページで、 **[基本的な SAML 構成]** の編集/ペン アイコンをクリックして設定を編集します。

   ![基本的な SAML 構成を編集する](common/edit-urls.png)

1. **[基本的な SAML 構成]** ページで、次のフィールドの値を入力します。

    1. **[サインオン URL]** ボックスに、次のパターンを使用して URL を入力します。`https://samlsp.private.zscaler.com/auth/login?domain=<your-domain-name>`

    1. **[識別子 (エンティティ ID)]** ボックスに、`https://samlsp.private.zscaler.com/auth/metadata` という URL を入力します。

    > [!NOTE]
    > **サインオン URL** は実際の値ではありません。 実際のサインオン URL で値を更新する必要があります。 値を取得するには、[Zscaler Private Access (ZPA) サポート チーム](https://help.zscaler.com/zpa-submit-ticket)に問い合わせてください。 Azure portal の **[基本的な SAML 構成]** セクションに示されているパターンを参照することもできます。

1. **[SAML でシングル サインオンをセットアップします]** ページの **[SAML 署名証明書]** セクションで、 **[フェデレーション メタデータ XML]** を探して **[ダウンロード]** を選択し、証明書をダウンロードしてコンピューターに保存します。

   ![証明書のダウンロードのリンク](common/metadataxml.png)

1. **[Set up Zscaler Private Access (ZPA)]\(Zscaler Private Access (ZPA) の設定\)** セクションで、要件に基づいて適切な URL をコピーします。

   ![構成 URL のコピー](common/copy-configuration-urls.png)

### <a name="configure-zscaler-private-access-zpa"></a>Zscaler Private Access (ZPA) の構成

1. Zscaler Private Access (ZPA) 内での構成を自動化するには、 **[拡張機能のインストール]** をクリックして **My Apps Secure Sign-in ブラウザー拡張機能**をインストールする必要があります。

    ![マイ アプリの拡張機能](common/install-myappssecure-extension.png)

2. ブラウザーに拡張機能を追加した後、 **[Setup Zscaler Private Access (ZPA)]\(Zscaler Private Access (ZPA) のセットアップ\)** をクリックすると、Zscaler Private Access (ZPA) アプリケーションに移動します。 そこから、管理者資格情報を入力して Zscaler Private Access (ZPA) にサインインします。 ブラウザー拡張機能によりアプリケーションが自動的に構成され、手順 3 から 6 が自動化されます。

    ![セットアップの構成](common/setup-sso.png)

3. Zscaler Private Access (ZPA) を手動で設定する場合は、新しい Web ブラウザー ウィンドウを開き、管理者として Zscaler Private Access (ZPA) 企業サイトにサインインして、次の手順を実行します。

4. メニュー左側の **[Administration]\(管理\)** をクリックして **[AUTHENTICATION]\(認証\)** セクションに移動し、 **[IdP Configuration]\(IdP 構成\)** をクリックします。

    ![Zscaler Private Access Administrator の [Administration]\(管理\)](./media/zscalerprivateaccess-tutorial/tutorial-zscaler-private-access-administration.png)

5. 右上隅の **[Add IdP Configuration]** をクリックします。 

    ![Zscaler Private Access Administrator の IdP](./media/zscalerprivateaccess-tutorial/tutorial-zscaler-private-access-idp.png)

6. **[Add IdP Configuration]** ページで、次の手順に従います。
 
    ![Zscaler Private Access Administrator での選択](./media/zscalerprivateaccess-tutorial/tutorial-zscaler-private-access-select.png)

    a. **[IdP Metadata File Upload]** フィールドの **[Select File]** をクリックして、Azure AD からダウンロードしたメタデータ ファイルをアップロードします。

    b. Azure AD から **IdP メタデータ** が読み取られ、以下に示すようにすべてのフィールド情報が設定されます。

    ![Zscaler Private Access Administrator での構成](./media/zscalerprivateaccess-tutorial/config.png)

    c. **[Domains]** フィールドから自分のドメインを選択します。
    
    d. **[Save]** をクリックします。

### <a name="create-an-azure-ad-test-user"></a>Azure AD のテスト ユーザーの作成

このセクションでは、Azure portal で Britta Simon というテスト ユーザーを作成します。

1. Azure portal の左側のウィンドウから、 **[Azure Active Directory]** 、 **[ユーザー]** 、 **[すべてのユーザー]** の順に選択します。
1. 画面の上部にある **[新しいユーザー]** を選択します。
1. **[ユーザー]** プロパティで、以下の手順を実行します。
   1. **[名前]** フィールドに「`Britta Simon`」と入力します。  
   1. **[ユーザー名]** フィールドに「username@companydomain.extension」と入力します。 たとえば、「 `BrittaSimon@contoso.com` 」のように入力します。
   1. **[パスワードを表示]** チェック ボックスをオンにし、 **[パスワード]** ボックスに表示された値を書き留めます。
   1. **Create** をクリックしてください。

### <a name="assign-the-azure-ad-test-user"></a>Azure AD テスト ユーザーの割り当て

このセクションでは、Britta Simon に Zscaler Private Access (ZPA) へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

1. Azure portal で **[エンタープライズ アプリケーション]** を選択し、 **[すべてのアプリケーション]** を選択します。
1. アプリケーションの一覧で、 **[Zscaler Private Access (ZPA)]** を選択します。
1. アプリの概要ページで、 **[管理]** セクションを見つけて、 **[ユーザーとグループ]** を選択します。

   ![[ユーザーとグループ] リンク](common/users-groups-blade.png)

1. **[ユーザーの追加]** を選択し、 **[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。

    ![[ユーザーの追加] リンク](common/add-assign-user.png)

1. **[ユーザーとグループ]** ダイアログの [ユーザー] の一覧から **Britta Simon** を選択し、画面の下部にある **[選択]** ボタンをクリックします。
1. SAML アサーション内に任意のロール値が必要な場合、 **[ロールの選択]** ダイアログでユーザーに適したロールを一覧から選択し、画面の下部にある **[選択]** をクリックします。
1. **[割り当ての追加]** ダイアログで、 **[割り当て]** をクリックします。

### <a name="create-zscaler-private-access-zpa-test-user"></a>Zscaler Private Access (ZPA) テスト ユーザーの作成

このセクションでは、Zscaler Private Access (ZPA) で Britta Simon というユーザーを作成します。 [Zscaler Private Access (ZPA) のサポート チーム](https://help.zscaler.com/zpa-submit-ticket)に問い合わせて、Zscaler Private Access (ZPA) プラットフォームでユーザーを追加します。

### <a name="test-sso"></a>SSO のテスト

アクセス パネルで Zscaler Private Access (ZPA) タイルを選択すると、SSO を設定した Zscaler Private Access (ZPA) に自動的にサインインします。 アクセス パネルの詳細については、[アクセス パネルの概要](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)に関する記事を参照してください。

## <a name="additional-resources"></a>その他のリソース

- [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Azure Active Directory の条件付きアクセスとは](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)