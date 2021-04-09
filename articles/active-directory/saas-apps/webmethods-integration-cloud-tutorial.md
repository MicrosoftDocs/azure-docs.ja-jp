---
title: 'チュートリアル: Azure Active Directory と webMethods Integration Suite の統合 | Microsoft Docs'
description: Azure Active Directory と webMethods Integration Suite の間でシングル サインオンを構成する方法について説明します。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 02/15/2019
ms.author: jeedes
ms.openlocfilehash: 52ad0d0356f4d309db89db6527d7fd2d7dec253d
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "92636818"
---
# <a name="tutorial-azure-active-directory-integration-with-webmethods-integration-suite"></a>チュートリアル: Azure Active Directory と webMethods Integration Suite の統合

このチュートリアルでは、webMethods Integration Suite と Azure Active Directory (Azure AD) を統合する方法について説明します。
webMethods Integration Suite と Azure AD の統合には、次の利点があります。

* webMethods Integration Suite にアクセスできる Azure AD ユーザーを制御できます。
* ユーザーが自分の Azure AD アカウントを使用して webMethods Integration Suite に自動的にサインオン (シングル サインオン) できるようにすることが可能です。
* 1 つの中央サイト (Azure Portal) でアカウントを管理できます。

SaaS アプリと Azure AD の統合の詳細については、「 [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)」を参照してください。
Azure サブスクリプションをお持ちでない場合は、開始する前に[無料アカウントを作成](https://azure.microsoft.com/free/)してください。

## <a name="prerequisites"></a>前提条件

webMethods Integration Suite と Azure AD の統合を構成するには、次のものが必要です。

* Azure AD サブスクリプション。 Azure AD の環境がない場合は、[こちら](https://azure.microsoft.com/pricing/free-trial/)から 1 か月の試用版を入手できます。
* webMethods Integration Suite でのシングル サインオンが有効なサブスクリプション

## <a name="scenario-description"></a>シナリオの説明

このチュートリアルでは、テスト環境で Azure AD のシングル サインオンを構成してテストします。

* webMethods Integration Suite では、**SP** と **IDP** によって開始される SSO がサポートされます。

* webMethods Integration Suite では、**Just-In-Time** ユーザー プロビジョニングがサポートされます。

## <a name="adding-webmethods-integration-suite-from-the-gallery"></a>ギャラリーからの webMethods Integration Suite の追加

Azure AD への webMethods Integration Suite の統合を構成するには、ギャラリーからマネージド SaaS アプリの一覧に webMethods Integration Suite を追加する必要があります。

**ギャラリーから webMethods Integration Suite を追加するには、次の手順に従います。**

1. **[Azure Portal](https://portal.azure.com)** の左側のナビゲーション ウィンドウで、 **[Azure Active Directory]** アイコンをクリックします。

    ![Azure Active Directory のボタン](common/select-azuread.png)

2. **[エンタープライズ アプリケーション]** に移動し、 **[すべてのアプリケーション]** オプションを選択します。

    ![[エンタープライズ アプリケーション] ブレード](common/enterprise-applications.png)

3. 新しいアプリケーションを追加するには、ダイアログの上部にある **[新しいアプリケーション]** をクリックします。

    ![[新しいアプリケーション] ボタン](common/add-new-app.png)

4. 検索ボックスに「**webMethods Integration Suite**」と入力し、結果パネルで **webMethods Integration Suite** を選択し、**[追加]** ボタンをクリックしてアプリケーションを追加します。

     ![結果一覧の webMethods Integration Suite](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成とテスト

このセクションでは、**Britta Simon** というテスト ユーザーに基づいて、webMethods Integration Suite で Azure AD のシングル サインオンを構成し、テストします。
シングル サインオンを機能させるには、Azure AD ユーザーと webMethods Integration Suite 内の関連ユーザーとの間にリンク関係が確立されている必要があります。

webMethods Integration Suite で Azure AD シングル サインオンを構成してテストするには、次の構成要素を完了する必要があります。

1. **[Azure AD シングル サインオンの構成](#configure-azure-ad-single-sign-on)** - ユーザーがこの機能を使用できるようにします。
2. **[webMethods Integration Suite シングル サインオンの構成](#configure-webmethods-integration-suite-single-sign-on)** - アプリケーション側でシングル サインオン設定を構成します。
3. **[Azure AD のテスト ユーザーの作成](#create-an-azure-ad-test-user)** - Britta Simon で Azure AD のシングル サインオンをテストします。
4. **[Azure AD テスト ユーザーの割り当て](#assign-the-azure-ad-test-user)** - Britta Simon が Azure AD シングル サインオンを使用できるようにします。
5. **[webMethods Integration Suite テスト ユーザーの作成](#create-webmethods-integration-suite-test-user)** - webMethods Integration Suite で Britta Simon に対応するユーザーを作成し、Azure AD のこのユーザーにリンクさせます。
6. **[シングル サインオンのテスト](#test-single-sign-on)** - 構成が機能するかどうかを確認します。

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成

このセクションでは、Azure portal 上で Azure AD のシングル サインオンを有効にします。

webMethods Integration Suite で Azure AD シングル サインオンを構成するには、次の手順に従います。

1. [Azure portal](https://portal.azure.com/) の **webMethods Integration Suite** アプリケーション統合ページで、**[シングル サインオン]** を選択します。

    ![シングル サインオン構成のリンク](common/select-sso.png)

2. **[シングル サインオン方式の選択]** ダイアログで、 **[SAML/WS-Fed]** モードを選択して、シングル サインオンを有効にします。

    ![シングル サインオン選択モード](common/select-saml-option.png)

3. **[SAML でシングル サインオンをセットアップします]** ページで、 **[編集]** アイコンをクリックして **[基本的な SAML 構成]** ダイアログを開きます。

    ![基本的な SAML 構成を編集する](common/edit-urls.png)

4. **webMethods Integration Suite** を構成する際に、**[基本的な SAML 構成]** セクションでアプリケーションを **IDP** 開始モードで構成する場合は、次の手順を実行します。

    ![このスクリーンショットは、[基本的な SAML 構成] を示しています。ここで、識別子と応答 U R L を入力し、[保存] を選択できます。](common/idp-intiated.png)

    a. **[識別子]** ボックスに、次の形式で URL を入力します。

    - `<SUBDOMAIN>.webmethodscloud.com`
    - `<SUBDOMAIN>.webmethodscloud.eu`
    - `<SUBDOMAIN>.webmethodscloud.de`

    b. **[応答 URL]** ボックスに、次のパターンを使用して URL を入力します。


    - `https://<SUBDOMAIN>.webmethodscloud.com/integration/live/saml/ssoResponse`
    - `https://<SUBDOMAIN>.webmethodscloud.eu/integration/live/saml/ssoResponse`
    - `https://<SUBDOMAIN>.webmethodscloud.de/integration/live/saml/ssoResponse`

    c. アプリケーションを **SP** 開始モードで構成する場合は、 **[追加の URL を設定します]** をクリックして次の手順を実行します。

    ![このスクリーンショットは、[追加の U R L を設定します] を示しています。ここで、サインオン U R L を入力できます。](common/metadata-upload-additional-signon.png)

    d. **[サインオン URL]** ボックスに、次のパターンを使用して URL を入力します。

    - `https://<SUBDOMAIN>.webmethodscloud.com/integration/live/saml/ssoRequest`
    - `https://<SUBDOMAIN>.webmethodscloud.eu/integration/live/saml/ssoRequest`
    - `https://<SUBDOMAIN>.webmethodscloud.de/integration/live/saml/ssoRequest`

    > [!NOTE]
    > これらは実際の値ではありません。 実際の識別子、応答 URL、サインオン URL でこれらの値を更新します。 これらの値を取得するには、[webMethods Integration Suite クライアント サポート チーム](https://empower.softwareag.com/)にお問い合わせください。 Azure portal の **[基本的な SAML 構成]** セクションに示されているパターンを参照することもできます。

5. **webMethods API Cloud** を構成するときに、**[基本的な SAML 構成]** セクションでアプリケーションを **IDP** 開始モードで構成する場合は、次の手順を実行します。

    ![このスクリーンショットは、[基本的な SAML 構成] を示しています。ここで、識別子と応答 U R L を入力し、[保存] を選択できます。](common/idp-intiated.png)

    a. **[識別子]** ボックスに、次の形式で URL を入力します。

    - `<SUBDOMAIN>.webmethodscloud.com`
    - `<SUBDOMAIN>.webmethodscloud.eu`
    - `<SUBDOMAIN>.webmethodscloud.de`

    b. **[応答 URL]** ボックスに、次のパターンを使用して URL を入力します。

    - `https://<SUBDOMAIN>.webmethodscloud.com/umc/rest/saml/initsso`
    - `https://<SUBDOMAIN>.webmethodscloud.eu/umc/rest/saml/initsso`
    - `https://<SUBDOMAIN>.webmethodscloud.de/umc/rest/saml/initsso`

    c. アプリケーションを **SP** 開始モードで構成する場合は、 **[追加の URL を設定します]** をクリックして次の手順を実行します。

    ![このスクリーンショットは、[追加の U R L を設定します] を示しています。ここで、サインオン U R L を入力できます。](common/metadata-upload-additional-signon.png)

    d. **[サインオン URL]** ボックスに、次のパターンを使用して URL を入力します。

    - `https://api.webmethodscloud.com/umc/rest/saml/initsso/?tenant=<TENANTID>`
    - `https://api.webmethodscloud.eu/umc/rest/saml/initsso/?tenant=<TENANTID>`
    - `https://api.webmethodscloud.de/umc/rest/saml/initsso/?tenant=<TENANTID>`

    > [!NOTE]
    > これらは実際の値ではありません。 実際の識別子、応答 URL、サインオン URL でこれらの値を更新します。 これらの値を取得するには、[webMethods Integration Suite クライアント サポート チーム](https://empower.softwareag.com/)にお問い合わせください。 Azure portal の **[基本的な SAML 構成]** セクションに示されているパターンを参照することもできます。

6. **[SAML でシングル サインオンをセットアップします]** ページの **[SAML 署名証明書]** セクションで、 **[ダウンロード]** をクリックして、要件のとおりに指定したオプションから **フェデレーション メタデータ XML** をダウンロードして、お使いのコンピューターに保存します。

    ![証明書のダウンロードのリンク](common/metadataxml.png)

7. **[webMethods Integration Suite のセットアップ]** セクションで、要件に従って適切な URL をコピーします。

    ![構成 URL のコピー](common/copy-configuration-urls.png)

    a. ログイン URL

    b. Azure AD 識別子

    c. ログアウト URL

### <a name="configure-webmethods-integration-suite-single-sign-on"></a>webMethods Integration Suite のシングル サインオンの構成

**webMethods Integration Suite** 側でシングル サインオンを構成するには、ダウンロードした **フェデレーション メタデータ XML** と Azure portal からコピーした適切な URL を [webMethods Integration Suite サポート チーム](https://empower.softwareag.com/)に送信する必要があります。 サポート チームはこれを設定して、SAML SSO 接続が両方の側で正しく設定されるようにします。

### <a name="create-an-azure-ad-test-user"></a>Azure AD のテスト ユーザーの作成

このセクションの目的は、Azure Portal で Britta Simon というテスト ユーザーを作成することです。

1. Azure portal の左側のウィンドウで、 **[Azure Active Directory]** 、 **[ユーザー]** 、 **[すべてのユーザー]** の順に選択します。

    ![[ユーザーとグループ] と [すべてのユーザー] リンク](common/users.png)

2. 画面の上部にある **[新しいユーザー]** を選択します。

    ![[新しいユーザー] ボタン](common/new-user.png)

3. [ユーザーのプロパティ] で、次の手順を実行します。

    ![[ユーザー] ダイアログ ボックス](common/user-properties.png)

    a. **[名前]** フィールドに「**BrittaSimon**」と入力します。
  
    b. **[ユーザー名]** フィールドに「**brittasimon\@yourcompanydomain.extension**」と入力します。  
    たとえば、BrittaSimon@contoso.com のように指定します。

    c. **[パスワードを表示]** チェック ボックスをオンにし、[パスワード] ボックスに表示された値を書き留めます。

    d. **Create** をクリックしてください。

### <a name="assign-the-azure-ad-test-user"></a>Azure AD テスト ユーザーの割り当て

このセクションでは、Britta Simon に webMethods Integration Suite へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

1. Azure portal で **[エンタープライズ アプリケーション]** を選択し、**[すべてのアプリケーション]** を選択してから、**[webMethods Integration Suite]** を選択します。

    ![[エンタープライズ アプリケーション] ブレード](common/enterprise-applications.png)

2. アプリケーションの一覧で **[webMethods Integration Suite]** を選択します。

    ![アプリケーションの一覧の webMethods Integration Suite のリンク](common/all-applications.png)

3. 左側のメニューで **[ユーザーとグループ]** を選びます。

    ![[ユーザーとグループ] リンク](common/users-groups-blade.png)

4. **[ユーザーの追加]** をクリックし、 **[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。

    ![[割り当ての追加] ウィンドウ](common/add-assign-user.png)

5. **[ユーザーとグループ]** ダイアログの [ユーザー] の一覧から **[Britta Simon]** を選択し、画面の下部にある **[選択]** ボタンをクリックします。

6. SAML アサーション内に任意のロール値が必要な場合、 **[ロールの選択]** ダイアログでユーザーに適したロールを一覧から選択し、画面の下部にある **[選択]** をクリッします。

7. **[割り当ての追加]** ダイアログで、 **[割り当て]** ボタンをクリックします。

### <a name="create-webmethods-integration-suite-test-user"></a>webMethods Integration Suite テスト ユーザーの作成

このセクションでは、Britta Simon というユーザーを webMethods Integration Suite に作成します。 webMethods Integration Suite では、Just-In-Time ユーザー プロビジョニングがサポートされています。この設定は既定で有効になっています。 このセクションでは、ユーザー側で必要な操作はありません。 webMethods Integration Suite にユーザーがまだ存在していない場合は、認証後に新規に作成されます。

### <a name="test-single-sign-on"></a>シングル サインオンのテスト 

このセクションでは、アクセス パネルを使用して Azure AD のシングル サインオン構成をテストします。

アクセス パネルで [webMethods Integration Suite] タイルをクリックすると、SSO を設定した webMethods Integration Suite に自動的にサインインします。 アクセス パネルの詳細については、[アクセス パネルの概要](../user-help/my-apps-portal-end-user-access.md)に関する記事を参照してください。

## <a name="additional-resources"></a>その他のリソース

- [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](./tutorial-list.md)

- [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)

- [Azure Active Directory の条件付きアクセスとは](../conditional-access/overview.md)