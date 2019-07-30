---
title: チュートリアル:Azure Active Directory と Cisco Webex Meetings の統合 | Microsoft Docs
description: Azure Active Directory と Cisco Webex Meetings の間でシングル サインオンを構成する方法について説明します。
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: 26704ca7-13ed-4261-bf24-fd6252e2072b
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/01/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 498650ce8a22f4804773bb66db3d640cd63bab8c
ms.sourcegitcommit: cf438e4b4e351b64fd0320bf17cc02489e61406a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/08/2019
ms.locfileid: "67655834"
---
# <a name="tutorial-azure-active-directory-integration-with-cisco-webex-meetings"></a>チュートリアル:Azure Active Directory と Cisco Webex Meetings の統合

このチュートリアルでは、Cisco Webex Meetings と Azure Active Directory (Azure AD) を統合する方法について説明します。
Cisco Webex Meetings と Azure AD の統合には、次の利点があります。

* Cisco Webex Meetings にアクセスする Azure AD ユーザーを制御できます。
* ユーザーが自分の Azure AD アカウントで Cisco Webex Meetings に自動的にサインイン (シングル サインオン) するように設定できます。
* 1 つの中央サイト (Azure Portal) でアカウントを管理できます。

SaaS アプリと Azure AD の統合の詳細については、「 [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)」を参照してください。
Azure サブスクリプションをお持ちでない場合は、開始する前に[無料アカウントを作成](https://azure.microsoft.com/free/)してください。

## <a name="prerequisites"></a>前提条件

Cisco Webex Meetings と Azure AD の統合を構成するには、次のものが必要です。

* Azure AD サブスクリプション。 Azure AD の環境がない場合は、[こちら](https://azure.microsoft.com/pricing/free-trial/)から 1 か月の評価版を入手できます
* Cisco Webex Meetings でのシングル サインオンが有効なサブスクリプション

## <a name="scenario-description"></a>シナリオの説明

このチュートリアルでは、テスト環境で Azure AD のシングル サインオンを構成してテストします。

* Cisco Webex Meetings では、**SP** によって開始される SSO がサポートされます

* Cisco Webex Meetings では、**Just-In-Time** ユーザー プロビジョニングがサポートされます

## <a name="adding-cisco-webex-meetings-from-the-gallery"></a>ギャラリーからの Cisco Webex Meetings の追加

Azure AD への Cisco Webex Meetings の統合を構成するには、ギャラリーから管理対象 SaaS アプリの一覧に Cisco Webex Meetings を追加する必要があります。

**ギャラリーから Cisco Webex Meetings を追加するには、次の手順に従います。**

1. **[Azure Portal](https://portal.azure.com)** の左側のナビゲーション ウィンドウで、 **[Azure Active Directory]** アイコンをクリックします。

    ![Azure Active Directory のボタン](common/select-azuread.png)

2. **[エンタープライズ アプリケーション]** に移動し、 **[すべてのアプリケーション]** オプションを選択します。

    ![[エンタープライズ アプリケーション] ブレード](common/enterprise-applications.png)

3. 新しいアプリケーションを追加するには、ダイアログの上部にある **[新しいアプリケーション]** をクリックします。

    ![[新しいアプリケーション] ボタン](common/add-new-app.png)

4. 検索ボックスに「**Cisco Webex Meetings**」と入力して、結果パネルから **[Cisco Webex Meetings]** を選択し、 **[追加]** をクリックしてアプリケーションを追加します。

     ![結果リストの Cisco Webex Meetings](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成とテスト

このセクションでは、**Britta Simon** というテスト ユーザーに基づいて、Cisco Webex Meetings で Azure AD のシングル サインオンを構成し、テストします。
シングル サインオンを機能させるには、Azure AD ユーザーと Cisco Webex Meetings 内の関連ユーザーとの間にリンク関係が確立されている必要があります。

Cisco Webex Meetings で Azure AD のシングル サインオンを構成してテストするには、次の構成要素を完了する必要があります。

1. **[Azure AD シングル サインオンの構成](#configure-azure-ad-single-sign-on)** - ユーザーがこの機能を使用できるようにします。
2. **[Cisco Webex Meetings のシングル サインオンの構成](#configure-cisco-webex-meetings-single-sign-on)** - アプリケーション側でシングル サインオン設定を構成します。
3. **[Azure AD のテスト ユーザーの作成](#create-an-azure-ad-test-user)** - Britta Simon で Azure AD のシングル サインオンをテストします。
4. **[Azure AD テスト ユーザーの割り当て](#assign-the-azure-ad-test-user)** - Britta Simon が Azure AD シングル サインオンを使用できるようにします。
5. **[Cisco Webex Meetings のテスト ユーザーの作成](#create-cisco-webex-meetings-test-user)** - Cisco Webex Meetings で Britta Simon に対応するユーザーを作成し、Azure AD の Britta Simon にリンクさせます。
6. **[シングル サインオンのテスト](#test-single-sign-on)** - 構成が機能するかどうかを確認します。

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成

このセクションでは、Azure portal 上で Azure AD のシングル サインオンを有効にします。

Cisco Webex Meetings で Azure AD シングル サインオンを構成するには、次の手順に従います。

1. [Azure portal](https://portal.azure.com/) の **Cisco Webex Meetings** アプリケーション統合ページで、 **[シングル サインオン]** を選択します。

    ![シングル サインオン構成のリンク](common/select-sso.png)

2. **[シングル サインオン方式の選択]** ダイアログで、 **[SAML/WS-Fed]** モードを選択して、シングル サインオンを有効にします。

    ![シングル サインオン選択モード](common/select-saml-option.png)

3. Azure portal の **[SAML でシングル サインオンをセットアップします]** ページで、 **[編集]** アイコンをクリックして **[基本的な SAML 構成]** ダイアログを開きます。

    ![基本的な SAML 構成を編集する](common/edit-urls.png)

4. **[基本的な SAML 構成]** セクションで、ダウンロードした**サービス プロバイダー メタデータ ファイル**をアップロードし、次の手順を実行してアプリケーションを構成します。

    >[!Note]
    >チュートリアルの **[Cisco Webex Meetings シングル サインオンの構成]** セクションで後述するサービス プロバイダー メタデータ ファイルを取得します。 

    a. **[メタデータ ファイルをアップロードします]** をクリックします。

    ![メタデータ ファイルをアップロードする](common/upload-metadata.png)

    b. **フォルダー ロゴ**をクリックしてメタデータ ファイルを選択し、 **[アップロード]** をクリックします。

    ![メタデータ ファイルを選択する](common/browse-upload-metadata.png)

    c. サービス プロバイダー メタデータ ファイルのアップロードが正常に完了すると、次のように、**識別子**と**応答 URL** の値が **[基本的な SAML 構成]** セクションに自動的に入力されます。

    ![[Cisco Webex Meetings のドメインと URL] のシングル サインオン情報](common/sp-identifier-reply.png)

    **[サインオン URL]** テキストボックスに、SP メタデータ ファイルのアップロードによって自動入力される **[応答 URL]** の値を貼り付けます。

5. Cisco Webex Meetings アプリケーションでは、特定の形式の SAML アサーションを使用するため、カスタム属性マッピングを SAML トークン属性の構成に追加する必要があります。 次のスクリーンショットには、既定の属性一覧が示されています。 **[編集]** アイコンをクリックして属性を追加します。

    ![image](common/edit-attribute.png)

6. **[ユーザーの要求]** セクションからデフォルト属性を削除します。Cisco Webex Meetings アプリケーションでは、さらにいくつかの属性が SAML 応答で返されます。 **[ユーザー属性]** ダイアログの **[ユーザー要求]** セクションで、以下の手順を実行して、以下の表のように SAML トークン属性を追加します。
    
    | Name | ソース属性|
    | ---------------|  --------- |
    |   firstname    | User.givenname |
    |   lastname    | User.surname |
    |   email       | User.mail |
    |   uid    | User.mail |

    a. **[新しい要求の追加]** をクリックして **[ユーザー要求の管理]** ダイアログを開きます。

    ![image](./media/cisco-webex-tutorial/tutorial-cisco-webex-addnewclaim.png)

    ![image](common/new-attribute-details.png)

    b. **[名前]** ボックスに、その行に対して表示される属性名を入力します。

    c. **[名前空間]** は空白のままにします。

    d. [ソース] として **[属性]** を選択します。

    e. **[ソース属性]** の一覧から、その行に表示される属性値を入力します。

    f. **[OK]** をクリックします。

    g. **[Save]** をクリックします。

7. **[SAML でシングル サインオンをセットアップします]** ページの **[SAML 署名証明書]** セクションで、 **[ダウンロード]** をクリックして、要件のとおりに指定したオプションから**フェデレーション メタデータ XML** をダウンロードして、お使いのコンピューターに保存します。

    ![証明書のダウンロードのリンク](common/metadataxml.png)

8. **[Cisco Webex Meetings のセットアップ]** セクションで、要件に従って適切な URL をコピーします。

    ![構成 URL のコピー](common/copy-configuration-urls.png)

    a. ログイン URL

    b. Azure AD 識別子

    c. ログアウト URL

### <a name="configure-cisco-webex-meetings-single-sign-on"></a>Cisco Webex Meetings シングル サインオンの構成

1. 管理者権限を備えた資格情報を使って [Cisco Cloud Collaboration Management](https://www.webex.com/go/connectadmin) に移動します。

2. **[Security Settings]** に進み、 **[フェデレーション Web SSO 構成]** にナビゲートします。
 
    ![Configure single sign-on](./media/cisco-webex-tutorial/tutorial-cisco-webex-10.png)

3. **[Federated Web SSO Configuration]** で、次の手順を実行します。

    ![Configure single sign-on](./media/cisco-webex-tutorial/tutorial-cisco-webex-11.png)

    a. [フェデレーション プロトコル] ボックスに、プロトコルの名前を入力します。

    b. **[SAML メタデータのインポート]** リンクをクリックして、Azure Portal からダウンロードしたメタデータ ファイルをアップロードします。

    c. **[エクスポート]** ボタンをクリックしてサービス プロバイダー メタデータ ファイルをダウンロードし、Azure Portal の **[基本 SAML 構成]** セクションにアップロードします。

    d. **[AuthContextClassRef]** テキストボックスに `urn:oasis:names:tc:SAML:2.0:ac:classes:unspecified` と入力し、Azure AD を使用して MFA を有効にする場合は `urn:oasis:names:tc:SAML:2.0:ac:classes:PasswordProtectedTransport;urn:oasis:names:tc:SAML:2.0:ac:classes:X509` のように 2 つの値を入力します

    e. **[アカウントの自動作成]** を選択します。

    >[!NOTE]
    >**Just-In-Time** ユーザー プロビジョニングを有効にするには、 **[アカウントの自動作成]** をチェックする必要があります。 さらに、SAML トークン属性を、SAML 応答で渡す必要があります。

    f. **[Save]** をクリックします。 

### <a name="create-an-azure-ad-test-user"></a>Azure AD のテスト ユーザーの作成

このセクションの目的は、Azure Portal で Britta Simon というテスト ユーザーを作成することです。

1. Azure portal の左側のウィンドウで、 **[Azure Active Directory]** 、 **[ユーザー]** 、 **[すべてのユーザー]** の順に選択します。

    ![[ユーザーとグループ] と [すべてのユーザー] リンク](common/users.png)

2. 画面の上部にある **[新しいユーザー]** を選択します。

    ![[新しいユーザー] ボタン](common/new-user.png)

3. [ユーザーのプロパティ] で、次の手順を実行します。

    ![[ユーザー] ダイアログ ボックス](common/user-properties.png)

    a. **[名前]** フィールドに「**BrittaSimon**」と入力します。
  
    b. **[User name]\(ユーザー名\)** フィールドに「**brittasimon\@yourcompanydomain.extension**」と入力します。  
    たとえば、BrittaSimon@contoso.com のように指定します。

    c. **[パスワードを表示]** チェック ボックスをオンにし、[パスワード] ボックスに表示された値を書き留めます。

    d. **Create** をクリックしてください。

### <a name="assign-the-azure-ad-test-user"></a>Azure AD テスト ユーザーの割り当て

このセクションでは、Britta Simon に Cisco Webex Meetings へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

1. Azure portal で **[エンタープライズ アプリケーション]** を選択し、 **[すべてのアプリケーション]** を選択してから、 **[Cisco Webex Meetings]** を選択します。

    ![[エンタープライズ アプリケーション] ブレード](common/enterprise-applications.png)

2. アプリケーションの一覧で **[Cisco Webex Meetings]** を選択します。

    ![アプリケーションの一覧の [Cisco Webex Meetings] リンク](common/all-applications.png)

3. 左側のメニューで **[ユーザーとグループ]** を選びます。

    ![[ユーザーとグループ] リンク](common/users-groups-blade.png)

4. **[ユーザーの追加]** をクリックし、 **[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。

    ![[割り当ての追加] ウィンドウ](common/add-assign-user.png)

5. **[ユーザーとグループ]** ダイアログの [ユーザー] の一覧で **[Britta Simon]** を選択し、画面の下部にある **[選択]** ボタンをクリックします。

6. SAML アサーション内に任意のロール値が必要な場合、 **[ロールの選択]** ダイアログでユーザーに適したロールを一覧から選択し、画面の下部にある **[選択]** をクリッします。

7. **[割り当ての追加]** ダイアログで、 **[割り当て]** ボタンをクリックします。

### <a name="create-cisco-webex-meetings-test-user"></a>Cisco Webex Meetings のテスト ユーザーの作成

このセクションの目的は、Cisco Webex Meetings で Britta Simon というユーザーを作成することです。 Cisco Webex Meetings では、**Just-In-Time** プロビジョニングがサポートされています。この設定は、既定で有効になっています。 このセクションでは、ユーザー側で必要な操作はありません。 ユーザーがまだ Cisco Webex Meetings に存在しない場合は、Cisco Webex Meetings にアクセスしようとしたときに新しいユーザーが作成されます。

### <a name="test-single-sign-on"></a>シングル サインオンのテスト

このセクションでは、アクセス パネルを使用して Azure AD のシングル サインオン構成をテストします。

アクセス パネルで [Cisco Webex Meetings] タイルをクリックすると、SSO を設定した Cisco Webex Meetings に自動的にサインインします。 アクセス パネルの詳細については、[アクセス パネルの概要](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)に関する記事を参照してください。

## <a name="additional-resources"></a>その他のリソース

- [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Azure Active Directory の条件付きアクセスとは](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

