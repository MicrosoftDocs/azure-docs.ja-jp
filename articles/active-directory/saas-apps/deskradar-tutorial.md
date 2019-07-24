---
title: チュートリアル:Azure Active Directory と Deskradar の統合 | Microsoft Docs
description: Azure Active Directory と Deskradar の間でシングル サインオンを構成する方法について説明します。
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: 5ff3e014-b79f-4a6c-bb0b-38462bea5d10
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 01/04/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: ea17794a5ca57ae0d38d9a90857706cf380cab36
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2019
ms.locfileid: "57895670"
---
# <a name="tutorial-azure-active-directory-integration-with-deskradar"></a>チュートリアル:Azure Active Directory と Deskradar の統合

このチュートリアルでは、Deskradar と Azure Active Directory (Azure AD) を統合する方法について説明します。
Deskradar と Azure AD の統合には、次の利点があります。

* Deskradar にアクセスできる Azure AD ユーザーを制御できます。
* ユーザーが自分の Azure AD アカウントを使用して Deskradar に自動的にサインイン (シングル サインオン) できるようにすることができます。
* 1 つの中央サイト (Azure Portal) でアカウントを管理できます。

SaaS アプリと Azure AD の統合の詳細については、「 [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)」を参照してください。
Azure サブスクリプションをお持ちでない場合は、開始する前に[無料アカウントを作成](https://azure.microsoft.com/free/)してください。

## <a name="prerequisites"></a>前提条件

Deskradar と Azure AD の統合を構成するには、次のものが必要です。

* Azure AD サブスクリプション。 Azure AD の環境がない場合は、[こちら](https://azure.microsoft.com/pricing/free-trial/)から 1 か月の評価版を入手できます
* Deskradar でのシングル サインオンが有効なサブスクリプション

## <a name="scenario-description"></a>シナリオの説明

このチュートリアルでは、テスト環境で Azure AD のシングル サインオンを構成してテストします。

* Deskradar では、**SP と IDP** によって開始される SSO がサポートされます

## <a name="add-deskradar-from-the-gallery"></a>ギャラリーからの Deskradar の追加

Azure AD への Deskradar の統合を構成するには、ギャラリーからマネージド SaaS アプリの一覧に Deskradar を追加する必要があります。

**ギャラリーから Deskradar を追加するには、次の手順を実行します。**

1. **[Azure Portal](https://portal.azure.com)** の左側のナビゲーション ウィンドウで、**[Azure Active Directory]** アイコンをクリックします。

    ![Azure Active Directory のボタン](common/select-azuread.png)

2. **[エンタープライズ アプリケーション]** に移動し、**[すべてのアプリケーション]** オプションを選択します。

    ![[エンタープライズ アプリケーション] ブレード](common/enterprise-applications.png)

3. 新しいアプリケーションを追加するには、ダイアログの上部にある **[新しいアプリケーション]** をクリックします。

    ![[新しいアプリケーション] ボタン](common/add-new-app.png)

4. 検索ボックスに「**Deskradar**」と入力し、結果ウィンドウで **[Deskradar]** を選択してから、**[追加]** をクリックしてアプリケーションを追加します。

     ![結果一覧の Deskradar](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成とテスト

このセクションでは、**Britta Simon** というテスト ユーザーに基づいて、[アプリケーション名] を使用して Azure AD のシングル サインオンを構成し、テストします。
シングル サインオンを機能させるには、Azure AD ユーザーと [アプリケーション名] 内の関連ユーザー間にリンク関係が確立されている必要があります。

[アプリケーション名] を使用して Azure AD のシングル サインオンを構成し、テストするには、次の構成要素を完了する必要があります。

1. **[Azure AD シングル サインオンの構成](#configure-azure-ad-single-sign-on)** - ユーザーがこの機能を使用できるようにします。
2. **[Deskradar シングル サインオンの構成](#configure-deskradar-single-sign-on)** - アプリケーション側でシングル サインオン設定を構成します。
3. **[Azure AD のテスト ユーザーの作成](#create-an-azure-ad-test-user)** - Britta Simon で Azure AD のシングル サインオンをテストします。
4. **[Azure AD テスト ユーザーの割り当て](#assign-the-azure-ad-test-user)** - Britta Simon が Azure AD シングル サインオンを使用できるようにします。
5. **[Deskradar のテスト ユーザーの作成](#create-deskradar-test-user)** - Deskradar で Britta Simon に対応するユーザーを作成し、Azure AD の Britta Simon にリンクさせます。
6. **[シングル サインオンのテスト](#test-single-sign-on)** - 構成が機能するかどうかを確認します。

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成

このセクションでは、Azure portal 上で Azure AD のシングル サインオンを有効にします。

[アプリケーション名] を使用して Azure AD のシングル サインオンを構成するには、次の手順を実行します。

1. [Azure portal](https://portal.azure.com/) の **[Deskradar]** アプリケーション統合ページで、**[シングル サインオン]** を選択します。

    ![シングル サインオン構成のリンク](common/select-sso.png)

2. **[シングル サインオン方式の選択]** ダイアログで、**[SAML/WS-Fed]** モードを選択して、シングル サインオンを有効にします。

    ![シングル サインオン選択モード](common/select-saml-option.png)

3. **[SAML でシングル サインオンをセットアップします]** ページで、**[編集]** アイコンをクリックして **[基本的な SAML 構成]** ダイアログを開きます。

    ![基本的な SAML 構成を編集する](common/edit-urls.png)

4. **[基本的な SAML 構成]** セクションで、アプリケーションを **IDP** 開始モードで構成する場合は、次の手順を実行します。

    ![Deskradar のドメインと URL のシングル サインオン情報](common/idp-intiated.png)

    a. **[識別子]** ボックスに、`https://YOURDOMAIN.deskradar.cloud` の形式で URL を入力します。

    b. **[応答 URL]** ボックスに、`https://YOURDOMAIN.deskradar.cloud/auth/sso/saml/consume` のパターンを使用して URL を入力します

5. アプリケーションを **SP** 開始モードで構成する場合は、**[追加の URL を設定します]** をクリックして次の手順を実行します。

    **[サインオン URL]** ボックスに、`https://YOURDOMAIN.deskradar.cloud/auth/sso/saml/login` という形式で URL を入力します。

    ![Deskradar のドメインと URL のシングル サインオン情報](common/metadata-upload-additional-signon.png)

    > [!NOTE]
    > これらは実際の値ではありません。 実際の識別子、応答 URL、サインオン URL でこれらの値を更新します。 **YOURDOMAIN** を Deskradar インスタンス ドメインに置き換えます。 これらの値を取得するには、[Deskradar クライアント サポート チーム](mailto:support@deskradar.com)に連絡してください。 Azure portal の **[基本的な SAML 構成]** セクションに示されているパターンを参照することもできます。

6. Deskradar アプリケーションは、特定の形式で構成された SAML アサーションを受け入れます。 このアプリケーションには、次の要求を構成します。 これらの属性の値は、アプリケーション統合ページの **[ユーザー属性]** セクションで管理できます。 **[SAML でシングル サインオンをセットアップします]** ページで、**[編集]** ボタンをクリックして **[ユーザー属性]** ダイアログを開きます。

    ![image](common/edit-attribute.png)

7. **[ユーザー属性]** ダイアログの **[ユーザーの要求]** セクションで、上の図のように SAML トークン属性を構成し、次の手順を実行します。

    | Name | ソース属性|
    | ---------------| --------- |
    | FirstName | User.givenname |
    | LastName | User.surname |
    | 電子メール | user.userprincipalname |
    | | |

    a. **[新しい要求の追加]** をクリックして **[ユーザー要求の管理]** ダイアログを開きます。

    ![image](common/new-save-attribute.png)

    ![image](common/new-attribute-details.png)

    b. **[名前]** ボックスに、その行に対して表示される属性名を入力します。

    c. **[名前空間]** は空白のままにします。

    d. [ソース] として **[属性]** を選択します。

    e. **[ソース属性]** の一覧から、その行に表示される属性値を入力します。

    f. **[OK]** をクリックします。

    g. **[Save]** をクリックします。

8. **[SAML でシングル サインオンをセットアップします]** ページの **[SAML 署名証明書]** セクションで、**[ダウンロード]** をクリックして要件のとおりに指定したオプションからの**証明書 (Base64)** をダウンロードして、お使いのコンピューターに保存します。

    ![証明書のダウンロードのリンク](common/certificatebase64.png)

9. **[Deskradar のセットアップ]** セクションで、要件に従って適切な URL をコピーします。

    ![構成 URL のコピー](common/copy-configuration-urls.png)

    a. ログイン URL

    b. Azure AD 識別子

    c. ログアウト URL

### <a name="configure-deskradar-single-sign-on"></a>Deskradar シングル サインオンの構成

1. 招待のリンクを使用してサインアップしたときに定義した電子メール アドレスとパスワードを使用して、管理者アカウントで Deskradar インスタンスにサインインします。

2. サイドバーのアイコンをクリックして、**[チーム]** パネルを開きます。

3. **[認証]** アブに切り替えます。

4. **[SAML 2.0]** タブで、次の手順を実行します。

    ![Deskradar の構成](./media/deskradar-tutorial/14-paste-urls.jpg)

    a. **SAML** 認証方法を有効にします。

    b. **[SAML SSO URL]** テキストボックスに、Azure portal からコピーした**ログイン URL** の値を入力します。

    c. **[ID プロバイダーの発行者]** テキスト ボックスに、Azure portal からコピーした **Azure AD 識別子**の値を入力します。

5. ダウンロードした**証明書 (Base64)** ファイルをテキスト エディターで開き、その内容をコピーして、Deskradar の **[公開証明書]** フィールドに貼り付けます。

    ![Deskradar の構成](./media/deskradar-tutorial/15-paste-certificate.jpg)

### <a name="create-an-azure-ad-test-user"></a>Azure AD のテスト ユーザーの作成

このセクションの目的は、Azure Portal で Britta Simon というテスト ユーザーを作成することです。

1. Azure portal の左側のウィンドウで、**[Azure Active Directory]**、**[ユーザー]**、**[すべてのユーザー]** の順に選択します。

    ![[ユーザーとグループ] と [すべてのユーザー] リンク](common/users.png)

2. 画面の上部にある **[新しいユーザー]** を選択します。

    ![[新しいユーザー] ボタン](common/new-user.png)

3. [ユーザーのプロパティ] で、次の手順を実行します。

    ![[ユーザー] ダイアログ ボックス](common/user-properties.png)

    a. **[名前]** フィールドに「**BrittaSimon**」と入力します。
  
    b. **[ユーザー名]** フィールドに「**brittasimon\@yourcompanydomain.extension**」と入力します  
    たとえば、BrittaSimon@contoso.com のように指定します。

    c. **[パスワードを表示]** チェック ボックスをオンにし、[パスワード] ボックスに表示された値を書き留めます。

    d. **Create** をクリックしてください。

### <a name="assign-the-azure-ad-test-user"></a>Azure AD テスト ユーザーの割り当て

このセクションでは、Britta Simon に Deskradar へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

1. Azure portal で **[エンタープライズ アプリケーション]** を選択し、**[すべてのアプリケーション]** を選択してから、**[Deskradar]** を選択します。

    ![[エンタープライズ アプリケーション] ブレード](common/enterprise-applications.png)

2. アプリケーションの一覧で **[Deskradar]** を選択します。

    ![アプリケーションの一覧の Deskradar のリンク](common/all-applications.png)

3. 左側のメニューで **[ユーザーとグループ]** を選びます。

    ![[ユーザーとグループ] リンク](common/users-groups-blade.png)

4. **[ユーザーの追加]** をクリックし、**[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。

    ![[割り当ての追加] ウィンドウ](common/add-assign-user.png)

5. **[ユーザーとグループ]** ダイアログの [ユーザー] の一覧で **[Britta Simon]** を選択し、画面の下部にある **[選択]** ボタンをクリックします。

6. SAML アサーション内に任意のロール値が必要な場合、**[ロールの選択]** ダイアログでユーザーに適したロールを一覧から選択し、画面の下部にある **[選択]** をクリッします。

7. **[割り当ての追加]** ダイアログで、**[割り当て]** をクリックします。

### <a name="create-deskradar-test-user"></a>Deskradar のテスト ユーザーの作成

このセクションでは、Deskradar で Britta Simon というユーザーを作成します。  [Deskradar クライアント サポート チーム](mailto:support@deskradar.com) と連携し、Deskradar プラットフォームにユーザーを追加してください。 シングル サインオンを使用する前に、ユーザーを作成し、有効化する必要があります。

### <a name="test-single-sign-on"></a>シングル サインオンのテスト

このセクションでは、アクセス パネルを使用して Azure AD のシングル サインオン構成をテストします。

**SP 開始モードでアプリケーションを使用する場合は、次の手順に従います。**

1. ブラウザーで URL `https://YOURDOMAIN.deskradar.cloud` (`YOURDOMAIN` は Deskradar インスタンス ドメインに置き換えます) を開いて、Deskradar インスタンスを起動します。 
1. **[エンタープライズ シングル サインオン]** を選択します。

    ![Deskradar](./media/deskradar-tutorial/16-sso-button.jpg)

- Active Directory に既にサインインしている場合は、Deskradar への直接サインインが行われます。
- まだサインインしていない場合は、Active Directory のサインイン フォームにリダイレクトされます。 そこで会社のアカウントの資格情報を入力して、サインインできます。
- 会社のアカウントの資格情報によるサインインが成功すると、Deskradar に戻り、Deskradar へのサインインが行われます。

**IDP 開始モードでアプリケーションを使用する場合は、次の手順に従います。**

アクセス パネルで [Deskradar] タイルをクリックすると、SSO を設定した Deskradar に自動的にサインインします。 アクセス パネルの詳細については、[アクセス パネルの概要](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)に関する記事を参照してください。

## <a name="additional-resources"></a>その他のリソース

- [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Azure Active Directory の条件付きアクセスとは](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
