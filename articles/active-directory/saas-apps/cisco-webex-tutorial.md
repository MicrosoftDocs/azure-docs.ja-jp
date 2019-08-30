---
title: チュートリアル:Azure Active Directory シングル サインオン (SSO) と Cisco Webex Meetings の統合 | Microsoft Docs
description: Azure Active Directory と Cisco Webex Meetings の間でシングル サインオンを構成する方法について説明します。
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 26704ca7-13ed-4261-bf24-fd6252e2072b
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 08/16/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: f6aab04826a3c06b595859c0f41f658b6e5d3432
ms.sourcegitcommit: 39d95a11d5937364ca0b01d8ba099752c4128827
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/16/2019
ms.locfileid: "69562305"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-cisco-webex-meetings"></a>チュートリアル:Azure Active Directory シングル サインオン (SSO) と Cisco Webex Meetings の統合

このチュートリアルでは、Cisco Webex Meetings と Azure Active Directory (Azure AD) を統合する方法について説明します。 Cisco Webex Meetings を Azure AD と統合すると、次のことができます。

* Cisco Webex Meetings にアクセスできるユーザーを Azure AD で制御できます。
* ユーザーが自分の Azure AD アカウントを使用して Cisco Webex Meetings に自動的にサインインするように設定できます。
* 1 つの中央サイト (Azure Portal) で自分のアカウントを管理できます。

SaaS アプリと Azure AD の統合の詳細については、「[Azure Active Directory でのアプリケーションへのシングル サインオン](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)」を参照してください。

## <a name="prerequisites"></a>前提条件

開始するには、次が必要です。

* Azure AD サブスクリプション。 サブスクリプションがない場合は、[無料アカウント](https://azure.microsoft.com/free/)を取得できます。
* Cisco Webex Meetings でのシングル サインオン (SSO) が有効なサブスクリプション。

## <a name="scenario-description"></a>シナリオの説明

このチュートリアルでは、テスト環境で Azure AD の SSO を構成してテストします。

* Cisco Webex Meetings では、**SP Initiated SSO と IDP Initiated SSO** がサポートされます

* Cisco Webex Meetings では、**Just-In-Time** ユーザー プロビジョニングがサポートされます

## <a name="adding-cisco-webex-meetings-from-the-gallery"></a>ギャラリーからの Cisco Webex Meetings の追加

Azure AD への Cisco Webex Meetings の統合を構成するには、ギャラリーから管理対象 SaaS アプリの一覧に Cisco Webex Meetings を追加する必要があります。

1. 職場または学校アカウントか、個人の Microsoft アカウントを使用して、[Azure portal](https://portal.azure.com) にサインインします。
1. 左のナビゲーション ウィンドウで **[Azure Active Directory]** サービスを選択します。
1. **[エンタープライズ アプリケーション]** に移動し、 **[すべてのアプリケーション]** を選択します。
1. 新しいアプリケーションを追加するには、 **[新しいアプリケーション]** を選択します。
1. **[ギャラリーから追加する]** セクションで、検索ボックスに、「**Cisco Webex Meetings**」と入力します。
1. 結果のパネルから **[Cisco Webex Meetings]** を選択し、アプリを追加します。 お使いのテナントにアプリが追加されるのを数秒待機します。

## <a name="configure-and-test-azure-ad-single-sign-on-for-cisco-webex-meetings"></a>Cisco Webex Meetings の Azure AD シングル サインオンの構成とテスト

**B.Simon** というテスト ユーザーを使用して、Cisco Webex Meetings に対する Azure AD SSO を構成してテストします。 SSO を機能させるために、Azure AD ユーザーと Cisco Webex Meetings の関連ユーザーとの間にリンク関係を確立する必要があります。

Cisco Webex Meetings に対する Azure AD SSO を構成してテストするには、次の構成要素を完了します。

1. **[Azure AD SSO の構成](#configure-azure-ad-sso)** - ユーザーがこの機能を使用できるようにします。
    1. **[Azure AD のテスト ユーザーの作成](#create-an-azure-ad-test-user)** - B.Simon で Azure AD のシングル サインオンをテストします。
    1. **[Azure AD テスト ユーザーの割り当て](#assign-the-azure-ad-test-user)** - B.Simon が Azure AD シングル サインオンを使用できるようにします。
2. **[Cisco Webex Meetings の SSO の構成](#configure-cisco-webex-meetings-sso)** - アプリケーション側でシングル サインオン設定を構成します。
    1. **[Cisco Webex Meetings のテスト ユーザーの作成](#create-cisco-webex-meetings-test-user)** - Cisco Webex Meetings で B.Simon に対応するユーザーを作成し、Azure AD の B.Simon にリンクさせます。
3. **[SSO のテスト](#test-sso)** - 構成が機能するかどうかを確認します。

## <a name="configure-azure-ad-sso"></a>Azure AD SSO の構成

これらの手順に従って、Azure portal で Azure AD SSO を有効にします。

1. [Azure portal](https://portal.azure.com/) の **Cisco Webex Meetings** アプリケーション統合ページで、 **[管理]** セクションを見つけて、 **[シングル サインオン]** を選択します。
1. **[シングル サインオン方式の選択]** ページで、 **[SAML]** を選択します。
1. **[SAML でシングル サインオンをセットアップします]** ページで、 **[基本的な SAML 構成]** の編集 (ペン) アイコンをクリックして設定を編集します。

   ![基本的な SAML 構成を編集する](common/edit-urls.png)

1. **[基本的な SAML 構成]** セクションで、ダウンロードした**サービス プロバイダー メタデータ ファイル**をアップロードし、次の手順を実行してアプリケーションを  **IDP**  開始モードで構成します。

    >[!Note]
    >チュートリアルの「**Cisco Webex Meetings の SSO の構成**」セクションで後述するサービス プロバイダー メタデータ ファイルを取得します。 

    a. **[メタデータ ファイルをアップロードします]** をクリックします。

    b. **フォルダー ロゴ**をクリックしてメタデータ ファイルを選択し、 **[アップロード]** をクリックします。

    c. サービス プロバイダー メタデータ ファイルのアップロードが正常に完了すると、次のように、**識別子**と**応答 URL** の値が **[基本的な SAML 構成]** セクションに自動的に入力されます。

5. **SP** 開始モードでアプリケーションを構成する場合は、次の手順を実行します。
    
    **[サインオン URL]** ボックスに、`https://<customername>.webex.com` という形式で URL を入力します。

5. Cisco Webex Meetings アプリケーションでは、特定の形式の SAML アサーションを使用するため、カスタム属性マッピングを SAML トークン属性の構成に追加する必要があります。 次のスクリーンショットには、既定の属性一覧が示されています。  **[編集]**   アイコンをクリックして、[ユーザー属性] ダイアログを開きます。

    ![image](common/edit-attribute.png)

6. その他に、Cisco Webex Meetings アプリケーションでは、いくつかの属性が SAML 応答で返されることが想定されています。 [ユーザー属性] ダイアログの [ユーザー要求] セクションで、以下の手順を実行して、以下の表のように SAML トークン属性を追加します。 

    | Name | ソース属性|
    | ---------------|  --------- |
    |   firstname    | User.givenname |
    |   lastname    | User.surname |
    |   email       | User.mail |
    |   uid    | User.mail |

    a. **[新しい要求の追加]** をクリックして **[ユーザー要求の管理]** ダイアログを開きます。

    b. **[名前]** ボックスに、その行に対して表示される属性名を入力します。

    c. **[名前空間]** は空白のままにします。

    d. [ソース] として **[属性]** を選択します。

    e. **[ソース属性]** の一覧から、その行に表示される属性値を入力します。

    f. **[Save]** をクリックします。

4. **[SAML でシングル サインオンをセットアップします]** ページの **[SAML 署名証明書]** セクションで、 **[フェデレーション メタデータ XML]** を探して **[ダウンロード]** を選択し、証明書をダウンロードして、お使いのコンピューターに保存します。

    ![証明書のダウンロードのリンク](common/metadataxml.png)

6. **[Cisco Webex Meetings のセットアップ]** セクションで、要件に基づいて適切な URL をコピーします。

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

このセクションでは、B.Simon に Cisco Webex Meetings へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

1. Azure portal で **[エンタープライズ アプリケーション]** を選択し、 **[すべてのアプリケーション]** を選択します。
1. アプリケーションの一覧で **[Cisco Webex Meetings]** を選択します。
1. アプリの概要ページで、 **[管理]** セクションを見つけて、 **[ユーザーとグループ]** を選択します。

    ![[ユーザーとグループ] リンク](common/users-groups-blade.png)

1. **[ユーザーの追加]** を選択し、 **[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。

    ![[ユーザーの追加] リンク](common/add-assign-user.png)

1. **[ユーザーとグループ]** ダイアログの [ユーザー] の一覧から **[B.Simon]** を選択し、画面の下部にある **[選択]** ボタンをクリックします。
1. SAML アサーション内に任意のロール値が必要な場合、 **[ロールの選択]** ダイアログでユーザーに適したロールを一覧から選択し、画面の下部にある **[選択]** をクリックします。
1. **[割り当ての追加]** ダイアログで、 **[割り当て]** をクリックします。

## <a name="configure-cisco-webex-meetings-sso"></a>Cisco Webex Meetings の SSO の構成

1. 管理資格情報で `https://<customername>.webex.com/admin` という URL に移動します。

2. **[Common Site Settings]\(共通サイト設定\)** に移動し、 **[SSO Configuration]\(SSO 構成\)** に移動します。
 
    ![Configure single sign-on](./media/cisco-webex-tutorial/tutorial-cisco-webex-11.png)

3. **[Webex Administration]\(Webex 管理\)** ページで、次の手順を実行します。

    ![Configure single sign-on](./media/cisco-webex-tutorial/tutorial-cisco-webex-10.png)

    a. **[Federation Protocol]\(フェデレーション プロトコル\)** として **[SAML 2.0]** を選択します。

    b. **[SAML メタデータのインポート]** リンクをクリックして、Azure Portal からダウンロードしたメタデータ ファイルをアップロードします。

    c. **[エクスポート]** ボタンをクリックしてサービス プロバイダー メタデータ ファイルをダウンロードし、Azure Portal の **[基本 SAML 構成]** セクションにアップロードします。

    d. **[AuthContextClassRef]** テキストボックスに `urn:oasis:names:tc:SAML:2.0:ac:classes:unspecified` と入力し、Azure AD を使用して MFA を有効にする場合は `urn:oasis:names:tc:SAML:2.0:ac:classes:PasswordProtectedTransport;urn:oasis:names:tc:SAML:2.0:ac:classes:X509` のように 2 つの値を入力します

    e. **[アカウントの自動作成]** を選択します。

    >[!NOTE]
    >**Just-In-Time** ユーザー プロビジョニングを有効にするには、 **[アカウントの自動作成]** をチェックする必要があります。 さらに、SAML トークン属性を、SAML 応答で渡す必要があります。

    f. **[Save]** をクリックします。

    >[!NOTE]
    >この構成は、メール形式の Webex UserID を使用するユーザー専用です。

### <a name="create-cisco-webex-meetings-test-user"></a>Cisco Webex Meetings のテスト ユーザーの作成

このセクションの目的は、Cisco Webex Meetings で B.Simon というユーザーを作成することです。 Cisco Webex Meetings では、**Just-In-Time** プロビジョニングがサポートされています。この設定は、既定で有効になっています。 このセクションでは、ユーザー側で必要な操作はありません。 ユーザーがまだ Cisco Webex Meetings に存在しない場合は、Cisco Webex Meetings にアクセスしようとしたときに新しいユーザーが作成されます。

## <a name="test-sso"></a>SSO のテスト 

このセクションでは、アクセス パネルを使用して Azure AD のシングル サインオン構成をテストします。

アクセス パネルで [Cisco Webex Meetings] タイルをクリックすると、SSO を設定した Cisco Webex Meetings に自動的にサインインします。 アクセス パネルの詳細については、[アクセス パネルの概要](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)に関する記事を参照してください。

## <a name="additional-resources"></a>その他のリソース

- [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Azure Active Directory でのアプリケーション アクセスとシングル サインオンとは](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Azure Active Directory の条件付きアクセスとは](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Azure AD で ServiceNow を試す](https://aad.portal.azure.com)