---
title: チュートリアル:Azure Active Directory と Jamf Pro の統合 | Microsoft Docs
description: Azure Active Directory と Jamf Pro の間でシングル サインオンを構成する方法について説明します。
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: 35e86d08-c29e-49ca-8545-b0ff559c5faf
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 12/19/2018
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 00d03df38dee74f4767e8ff3b21d9462fd59c4c0
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/13/2019
ms.locfileid: "67099582"
---
# <a name="tutorial-azure-active-directory-integration-with-jamf-pro"></a>チュートリアル:Azure Active Directory と Jamf Pro の統合

このチュートリアルでは、Jamf Pro と Azure Active Directory (Azure AD) を統合する方法について説明します。
Jamf Pro と Azure AD の統合には、次の利点があります。

* Jamf Pro にアクセスできる Azure AD ユーザーを制御できます。
* ユーザーが自分の Azure AD アカウントを使用して Jamf Pro に自動的にサインイン (シングル サインオン) できるようにすることができます。
* 1 つの中央サイト (Azure Portal) でアカウントを管理できます。

SaaS アプリと Azure AD の統合の詳細については、「 [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)」を参照してください。
Azure サブスクリプションをお持ちでない場合は、開始する前に[無料アカウントを作成](https://azure.microsoft.com/free/)してください。

## <a name="prerequisites"></a>前提条件

Jamf Pro と Azure AD の統合を構成するには、次のものが必要です。

* Azure AD サブスクリプション。 Azure AD の環境がない場合は、[こちら](https://azure.microsoft.com/pricing/free-trial/)から 1 か月の評価版を入手できます
* Jamf Pro シングル サインオンが有効なサブスクリプション

## <a name="scenario-description"></a>シナリオの説明

このチュートリアルでは、テスト環境で Azure AD のシングル サインオンを構成してテストします。

* Jamf Pro では、**SP と IDP** によって開始される SSO がサポートされます

## <a name="adding-jamf-pro-from-the-gallery"></a>ギャラリーからの Jamf Pro の追加

Azure AD への Jamf Pro の統合を構成するには、ギャラリーからマネージド SaaS アプリの一覧に Jamf Pro を追加する必要があります。

**ギャラリーから Jamf Pro を追加するには、次の手順に従います。**

1. **[Azure Portal](https://portal.azure.com)** の左側のナビゲーション ウィンドウで、 **[Azure Active Directory]** アイコンをクリックします。

    ![Azure Active Directory のボタン](common/select-azuread.png)

2. **[エンタープライズ アプリケーション]** に移動し、 **[すべてのアプリケーション]** オプションを選択します。

    ![[エンタープライズ アプリケーション] ブレード](common/enterprise-applications.png)

3. 新しいアプリケーションを追加するには、ダイアログの上部にある **[新しいアプリケーション]** をクリックします。

    ![[新しいアプリケーション] ボタン](common/add-new-app.png)

4. 検索ボックスに「**Jamf Pro**」と入力し、結果パネルで **[Jamf Pro]** を選び、 **[追加]** をクリックして、アプリケーションを追加します。

     ![結果一覧の Jamf Pro](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成とテスト

このセクションでは、**Britta Simon** というテスト ユーザーに基づいて、Jamf Pro で Azure AD のシングル サインオンを構成し、テストします。
シングル サインオンを機能させるには、Azure AD ユーザーと Jamf Pro 内の関連ユーザー間にリンク関係が確立されている必要があります。

Jamf Pro で Azure AD のシングル サインオンを構成してテストするには、次の構成要素を完了する必要があります。

1. **[Azure AD シングル サインオンの構成](#configure-azure-ad-single-sign-on)** - ユーザーがこの機能を使用できるようにします。
2. **[Jamf Pro シングル サインオンの構成](#configure-jamf-pro-single-sign-on)** - アプリケーション側でシングル サインオン設定を構成します。
3. **[Azure AD のテスト ユーザーの作成](#create-an-azure-ad-test-user)** - Britta Simon で Azure AD のシングル サインオンをテストします。
4. **[Azure AD テスト ユーザーの割り当て](#assign-the-azure-ad-test-user)** - Britta Simon が Azure AD シングル サインオンを使用できるようにします。
5. **[Jamf Pro テスト ユーザーの作成](#create-jamf-pro-test-user)** - Azure AD の Britta Simon にリンクさせるために、対応するユーザーを Jamf Pro で作成します。
6. **[シングル サインオンのテスト](#test-single-sign-on)** - 構成が機能するかどうかを確認します。

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成

このセクションでは、Azure portal 上で Azure AD のシングル サインオンを有効にします。

Jamf Pro で Azure AD シングル サインオンを構成するには、次の手順に従います。

1. [Azure portal](https://portal.azure.com/) の **Jamf Pro** アプリケーション統合ページで、 **[シングル サインオン]** を選択します。

    ![シングル サインオン構成のリンク](common/select-sso.png)

2. **[シングル サインオン方式の選択]** ダイアログで、 **[SAML/WS-Fed]** モードを選択して、シングル サインオンを有効にします。

    ![シングル サインオン選択モード](common/select-saml-option.png)

3. **[SAML でシングル サインオンをセットアップします]** ページで、 **[編集]** アイコンをクリックして **[基本的な SAML 構成]** ダイアログを開きます。

    ![基本的な SAML 構成を編集する](common/edit-urls.png)

4. **[基本的な SAML 構成]** セクションで、**IDP** 開始モードで構成する場合は、次の手順を実行します。

    ![Jamf Pro のドメインと URL のシングル サインオン情報](common/idp-intiated.png)

    a. **[識別子]** ボックスに、`https://<subdomain>.jamfcloud.com/saml/metadata` の形式で URL を入力します。

    b. **[応答 URL]** ボックスに、`https://<subdomain>.jamfcloud.com/saml/SSO` のパターンを使用して URL を入力します

5. アプリケーションを **SP** 開始モードで構成する場合は、 **[追加の URL を設定します]** をクリックして次の手順を実行します。

    **[サインオン URL]** ボックスに、`https://<subdomain>.jamfcloud.com` という形式で URL を入力します。

    ![Jamf Pro のドメインと URL のシングル サインオン情報](common/metadata-upload-additional-signon.png)

    > [!NOTE]
    > これらは実際の値ではありません。 実際の識別子、応答 URL、サインオン URL でこれらの値を更新します。 実際の識別子の値を Jamf Pro ポータルの **[シングル サインオン]** セクションから取得します。これについては、このチュートリアルで後ほど説明します。 実際の**サブドメイン**の値を識別子の値から抽出し、サインオン URL と応答 URL 内のその**サブドメイン**情報を使用できます。 Azure portal の **[基本的な SAML 構成]** セクションに示されているパターンを参照することもできます。

6. **Set up Single Sign-On with SAML\(SAML でのシングルサインオンの設定** ページの **SAML 署名証明書** セクションで、コピー ボタンをクリックして **App Federation Metadata Url\(アプリのフェデレーション メタデータ URL)** をコピーして、コンピューターに保存します。

    ![証明書のダウンロードのリンク](common/copy-metadataurl.png)

### <a name="configure-jamf-pro-single-sign-on"></a>Jamf Pro のシングル サインオンを構成する

1. Jamf Pro 内での構成を自動化するには、 **[拡張機能のインストール]** をクリックして**マイアプリによるセキュリティで保護されたサインイン拡張機能**をインストールする必要があります。

    ![image](./media/jamfprosamlconnector-tutorial/install_extension.png)

2. ブラウザーに拡張機能を追加した後、 **[setup Jamf Pro]\(Jamf Pro の設定)** をクリックすると、Jamf Pro アプリケーションに移動します。 そこから、管理者資格情報を提供して Jamf Pro にサインインします。 ブラウザー拡張機能によりアプリケーションが自動的に構成され、手順 3 ～ 7 が自動化されます。

    ![image](./media/jamfprosamlconnector-tutorial/d1_saml.png)

3. Jamf Pro を手動でセットアップしたい場合は、新しい Web ブラウザー ウィンドウを開き、管理者として Jamf Pro 企業サイトにログインして、次の手順を実行します。

4. ページの右上隅の**設定アイコン**をクリックします。

    ![Jamf Pro の構成](./media/jamfprosamlconnector-tutorial/configure1.png)

5. **[シングル サインオン]** をクリックします。

    ![Jamf Pro の構成](./media/jamfprosamlconnector-tutorial/configure2.png)

6. **[Single sign-on]\(シングル サインオン\)** ページで、次の手順を実行します。

    ![Jamf Pro シングル](./media/jamfprosamlconnector-tutorial/tutorial_jamfprosamlconnector_single.png)

    a. **[Jamf Pro Server]\(Jamf Pro サーバー\)** を選択してシングル サインオン アクセスを有効にします。

    b. **[Allow bypass for all users]\(すべてのユーザーにバイパスを許可する\)** を選択すると、ユーザーは認証のために ID プロバイダーのログイン ページへとリダイレクトされなくなり、Jamf Pro に直接ログインできるようになります。 ユーザーが ID プロバイダーを経由して Jamf Pro にアクセスしようとした場合は、IdP によって開始される SSO 認証と承認が発生します。

    c. **[NameID]** オプションを **[USER MAPPING:SAML]\(ユーザー マッピング: SAML\)** で選択します。 既定では、この設定は **[NameID]** に設定されていますが、カスタム属性を定義することもできます。

    d. **[Email]\(電子メール\)** を **[USER MAPPING:JAMF PRO]\(ユーザー マッピング: JAMF PRO\)** で選択します。 Jamf Pro は、IdP によって送信された SAML 属性を次の方法でマップします: ユーザー別およびグループ別。 ユーザーが Jamf Pro にアクセスしようとすると、既定では、Jamf Pro がユーザーに関する情報を ID プロバイダーから取得し、それを Jamf Pro のユーザー アカウントと照合します。 受信したユーザー アカウントが Jamf Pro 内に存在しない場合は、グループ名のマッチングが発生します。

    e. 値 `http://schemas.microsoft.com/ws/2008/06/identity/claims/groups` を **[GROUP ATTRIBUTE NAME]\(グループ属性名\)** テキスト ボックスに貼り付けます。

7. 同じページで、 **[シングル サインオン]** セクションの下の **[ID プロバイダー]** までスクロールし、次の手順に従います。

    ![Jamf Pro の構成](./media/jamfprosamlconnector-tutorial/configure3.png)

    a. **[IDENTITY PROVIDER]\(ID プロバイダー\)** ドロップダウンから **[Other]\(その他\)** を選択します。

    b. **[OTHER PROVIDER]\(その他のプロバイダー\)** ボックスに、 **[Azure AD]** を入力します。

    c. **[IDENTITY PROVIDER METADATA SOURCE]\(ID プロバイダーのメタデータ ソース\)** ドロップダウンのオプションとして **[Metadata URL]\(メタデータ URL\)** を選択し、次のテキストボックスに、Azure portal からコピーした**アプリのフェデレーション メタデータ URL** の値を貼り付けます。

    d. **エンティティ ID** の値をコピーし、Azure portal の **[Jamf Pro Domain and URLs]\(Jamf Pro のドメインと URL\)** セクションの **[識別子 (エンティティ ID)]** テキストボックスに貼り付けます。

    > [!NOTE]
    > ここで、隠されている値はサブドメインの部分です。この値を使用して、Azure portal 上で、 **[Jamf Pro Domain and URLs]\(Jamf Pro ドメインおよび URL\)** セクションのサインオン URL と応答 URL を完了します。

    e. **[Save]** をクリックします。

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

このセクションでは、Britta Simon に Jamf Pro へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

1. Azure portal で **[エンタープライズ アプリケーション]** を選択し、 **[すべてのアプリケーション]** を選択してから、 **[Jamf Pro]** を選択します。

    ![[エンタープライズ アプリケーション] ブレード](common/enterprise-applications.png)

2. アプリケーションの一覧で **[Jamf Pro]** を選択します。

    ![アプリケーションの一覧の [Jamf Pro] リンク](common/all-applications.png)

3. 左側のメニューで **[ユーザーとグループ]** を選びます。

    ![[ユーザーとグループ] リンク](common/users-groups-blade.png)

4. **[ユーザーの追加]** をクリックし、 **[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。

    ![[割り当ての追加] ウィンドウ](common/add-assign-user.png)

5. **[ユーザーとグループ]** ダイアログの [ユーザー] の一覧で **[Britta Simon]** を選択し、画面の下部にある **[選択]** ボタンをクリックします。

6. SAML アサーション内に任意のロール値が必要な場合、 **[ロールの選択]** ダイアログでユーザーに適したロールを一覧から選択し、画面の下部にある **[選択]** をクリッします。

7. **[割り当ての追加]** ダイアログで、 **[割り当て]** ボタンをクリックします。

### <a name="create-jamf-pro-test-user"></a>Jamf Pro テスト ユーザーの作成

Azure AD ユーザーが Jamf Pro にログインできるようにするには、ユーザーを Jamf Pro にプロビジョニングする必要があります。 Jamf Pro の場合、プロビジョニングは手動で行います。

**ユーザー アカウントをプロビジョニングするには、次の手順に従います。**

1. Jamf Pro の企業サイトに管理者としてログインします。

2. ページの右上隅の**設定アイコン**をクリックします。

    ![従業員の追加](./media/jamfprosamlconnector-tutorial/configure1.png)

3. **[Jamf Pro User Accounts & Groups]\(Jamf Pro ユーザー アカウントとグループ\)** をクリックします。

    ![従業員の追加](./media/jamfprosamlconnector-tutorial/user1.png)

4. **[新規]** をクリックします。

    ![従業員の追加](./media/jamfprosamlconnector-tutorial/user2.png)

5. **[Create Standard Account]\(標準アカウントの作成\)** を選択します。

    ![従業員の追加](./media/jamfprosamlconnector-tutorial/user3.png)

6. **[新しいアカウント]** ダイアログで、次の手順に従います。

    ![従業員の追加](./media/jamfprosamlconnector-tutorial/user4.png)

    a. **[ユーザー名]** テキストボックスに、フル ネームの「BrittaSimon」を入力します。

    b. **[ACCESS LEVEL]\(アクセス レベル\)** 、 **[PRIVILEGE SET]\(特権セット\)** 、および **[ACCESS STATUS]\(アクセスの状態\)** に対して組織に従って適切なオプションを選択します。

    c. **[FULL NAME]\(フル ネーム\)** テキストボックスに、フル ネームの「Britta Simon」を入力します。

    d. **[メール アドレス]** テキストボックスに、Britta Simon アカウントのメール アドレスを入力します。

    e. **[パスワード]** テキストボックスに、そのユーザーのパスワードを入力します。

    f. **[VERIFY PASSWORD]\(パスワードの確認\)** テキストボックスに、そのユーザーのパスワードを入力します。

    g. **[Save]** をクリックします。

### <a name="test-single-sign-on"></a>シングル サインオンのテスト 

このセクションでは、アクセス パネルを使用して Azure AD のシングル サインオン構成をテストします。

アクセス パネル上で [Jamf Pro] タイルをクリックすると、SSO を設定した Jamf Pro に自動的にサインインします。 アクセス パネルの詳細については、[アクセス パネルの概要](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)に関する記事を参照してください。

## <a name="additional-resources"></a>その他のリソース

- [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Azure Active Directory の条件付きアクセスとは](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
