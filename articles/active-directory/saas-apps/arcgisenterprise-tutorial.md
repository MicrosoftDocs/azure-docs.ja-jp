---
title: チュートリアル:Azure Active Directory と ArcGIS Enterprise の統合 | Microsoft Docs
description: Azure Active Directory と ArcGIS Enterprise の間でシングル サインオンを構成する方法について説明します。
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: 24809e9d-a4aa-4504-95a9-e4fcf484f431
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 12/28/2018
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9642dc67a3707862009efa59d99f0cef54b9b878
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/13/2019
ms.locfileid: "67106687"
---
# <a name="tutorial-azure-active-directory-integration-with-arcgis-enterprise"></a>チュートリアル:Azure Active Directory と ArcGIS Enterprise の統合

このチュートリアルでは、ArcGIS Enterprise と Azure Active Directory (Azure AD) を統合する方法について説明します。
ArcGIS Enterprise と Azure AD の統合には、次の利点があります。

* Azure AD で誰が ArcGIS Enterprise にアクセスできるかを制御できます。
* ユーザーが自分の Azure AD アカウントを使用して ArcGIS Enterprise に自動的にサインイン (シングル サインオン) できるようにすることができます。
* 1 つの中央サイト (Azure Portal) でアカウントを管理できます。

SaaS アプリと Azure AD の統合の詳細については、「 [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)」を参照してください。
Azure サブスクリプションをお持ちでない場合は、開始する前に[無料アカウントを作成](https://azure.microsoft.com/free/)してください。

## <a name="prerequisites"></a>前提条件

ArcGIS Enterprise と Azure AD の統合を構成するには、次のものが必要です。

* Azure AD サブスクリプション。 Azure AD の環境がない場合は、[こちら](https://azure.microsoft.com/pricing/free-trial/)から 1 か月の評価版を入手できます
* ArcGIS Enterprise でのシングル サインオンが有効なサブスクリプション

## <a name="scenario-description"></a>シナリオの説明

このチュートリアルでは、テスト環境で Azure AD のシングル サインオンを構成してテストします。



* ArcGIS Enterprise では、**SP と IDP** によって開始される SSO がサポートされます
* ArcGIS Enterprise では、**Just In Time** ユーザー プロビジョニングがサポートされます


## <a name="adding-arcgis-enterprise-from-the-gallery"></a>ギャラリーからの ArcGIS Enterprise の追加

Azure AD への ArcGIS Enterprise の統合を構成するには、ギャラリーから管理対象 SaaS アプリの一覧に ArcGIS Enterprise を追加する必要があります。

**ギャラリーから ArcGIS Enterprise を追加するには、次の手順に従います。**

1. **[Azure Portal](https://portal.azure.com)** の左側のナビゲーション ウィンドウで、 **[Azure Active Directory]** アイコンをクリックします。

    ![Azure Active Directory のボタン](common/select-azuread.png)

2. **[エンタープライズ アプリケーション]** に移動し、 **[すべてのアプリケーション]** オプションを選択します。

    ![[エンタープライズ アプリケーション] ブレード](common/enterprise-applications.png)

3. 新しいアプリケーションを追加するには、ダイアログの上部にある **[新しいアプリケーション]** をクリックします。

    ![[新しいアプリケーション] ボタン](common/add-new-app.png)

4. 検索ボックスに「**ArcGIS Enterprise**」と入力し、結果パネルで **[ArcGIS Enterprise]** を選択し、 **[追加]** をクリックして、アプリケーションを追加します。

     ![結果一覧の ArcGIS Enterprise](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成とテスト

このセクションでは、**Britta Simon** というテスト ユーザーに基づいて、[アプリケーション名] を使用して Azure AD のシングル サインオンを構成し、テストします。
シングル サインオンを機能させるには、Azure AD ユーザーと [アプリケーション名] 内の関連ユーザー間にリンク関係が確立されている必要があります。

[アプリケーション名] を使用して Azure AD のシングル サインオンを構成し、テストするには、次の構成要素を完了する必要があります。

1. **[Azure AD シングル サインオンの構成](#configure-azure-ad-single-sign-on)** - ユーザーがこの機能を使用できるようにします。
2. **[ArcGIS Enterprise シングル サインオンの構成](#configure-arcgis-enterprise-single-sign-on)** - アプリケーション側でシングル サインオン設定を構成します。
3. **[Azure AD のテスト ユーザーの作成](#create-an-azure-ad-test-user)** - Britta Simon で Azure AD のシングル サインオンをテストします。
4. **[Azure AD テスト ユーザーの割り当て](#assign-the-azure-ad-test-user)** - Britta Simon が Azure AD シングル サインオンを使用できるようにします。
5. **[ArcGIS Enterprise のテスト ユーザーの作成](#create-arcgis-enterprise-test-user)** - ArcGIS Enterprise で Britta Simon に対応するユーザーを作成し、Azure AD の Britta Simon にリンクさせます。
6. **[シングル サインオンのテスト](#test-single-sign-on)** - 構成が機能するかどうかを確認します。

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成

このセクションでは、Azure portal 上で Azure AD のシングル サインオンを有効にします。

[アプリケーション名] を使用して Azure AD のシングル サインオンを構成するには、次の手順を実行します。

1. [Azure portal](https://portal.azure.com/) の **ArcGIS Enterprise** アプリケーション統合ページで、 **[シングル サインオン]** を選択します。

    ![シングル サインオン構成のリンク](common/select-sso.png)

2. **[シングル サインオン方式の選択]** ダイアログで、 **[SAML/WS-Fed]** モードを選択して、シングル サインオンを有効にします。

    ![シングル サインオン選択モード](common/select-saml-option.png)

3. **[SAML でシングル サインオンをセットアップします]** ページで、 **[編集]** アイコンをクリックして **[基本的な SAML 構成]** ダイアログを開きます。

    ![基本的な SAML 構成を編集する](common/edit-urls.png)

4. **[基本的な SAML 構成]** セクションで、**IDP** 開始モードでアプリケーションを構成する場合は、次の手順を実行します。

    ![[ArcGIS Enterprise Domain and URLs]\(ArcGIS Enterprise のドメインと URL\) のシングル サインオン情報](common/idp-intiated.png)

    a. **[識別子]** ボックスに、`<EXTERNAL_DNS_NAME>.portal` の形式で URL を入力します。

    b. **[応答 URL]** ボックスに、`https://<EXTERNAL_DNS_NAME>/portal/sharing/rest/oauth2/saml/signin2` のパターンを使用して URL を入力します

    c. アプリケーションを **SP** 開始モードで構成する場合は、 **[追加の URL を設定します]** をクリックして次の手順を実行します。

    ![[ArcGIS Enterprise Domain and URLs]\(ArcGIS Enterprise のドメインと URL\) のシングル サインオン情報](common/metadata-upload-additional-signon.png)

    **[サインオン URL]** ボックスに、`https://<EXTERNAL_DNS_NAME>/portal/sharing/rest/oauth2/saml/signin` という形式で URL を入力します。

    > [!NOTE]
    > これらは実際の値ではありません。 実際の識別子、応答 URL、サインオン URL でこれらの値を更新します。 これらの値を取得するには、[ArcGIS Enterprise クライアント サポート チーム](mailto:support@esri.com)に問い合わせてください。 識別子の値は、このチュートリアルで後から説明する **[Set Identity Provider]\(ID プロバイダーの設定\)** セクションで取得します。

5. **Set up Single Sign-On with SAML\(SAML でのシングルサインオンの設定** ページの **SAML 署名証明書** セクションで、コピー ボタンをクリックして **App Federation Metadata Url\(アプリのフェデレーション メタデータ URL)** をコピーして、コンピューターに保存します。

    ![証明書のダウンロードのリンク](common/copy-metadataurl.png)

### <a name="configure-arcgis-enterprise-single-sign-on"></a>ArcGIS Enterprise でシングル サインオンを構成する

1. 別の Web ブラウザーのウィンドウで、ArcGIS Enterprise の企業サイトに管理者としてログインします。

2. **[組織] > [設定の編集]** を選択します。

    ![ArcGIS Enterprise の構成](./media/arcgisenterprise-tutorial/configure1.png)

3. **[セキュリティ]** タブを選びます。

    ![ArcGIS Enterprise の構成](./media/arcgisenterprise-tutorial/configure2.png)

4. **[Enterprise Logins via SAML]\(SAML 経由でのエンタープライズ ログイン\)** セクションまで下へスクロールして **[SET ENTERPRISE LOGIN]\(エンタープライズ ログインの設定\)** を選択します。

    ![ArcGIS Enterprise の構成](./media/arcgisenterprise-tutorial/configure3.png)

5. **[Set Identity Provider]\(ID プロバイダーの設定\)** セクションで、次の手順に従います。

    ![ArcGIS Enterprise の構成](./media/arcgisenterprise-tutorial/configure4.png)

    a. **[名前]** テキスト ボックスに、**Azure Active Directory Test** のような名前を指定します。

    b. **[URL]** ボックスに、Azure Portal からコピーした **[アプリのフェデレーション メタデータ URL]** の値を貼り付けます。

    c. **[詳細設定の表示]** をクリックし、 **[エンティティ ID]** の値をコピーして、Azure Portal の **[ArcGIS Enterprise Domain and URLs]\(ArcGIS Enterprise のドメインと URL\)** セクションの **[識別子]** テキスト ボックスに貼り付けます。
    
    ![ArcGIS Enterprise の構成](./media/arcgisenterprise-tutorial/configure5.png)

    d. **[ID プロバイダーの更新]** をクリックします。

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

このセクションでは、ArcGIS Enterprise へのアクセス権を付与することによって、Britta Simon が Azure シングル サインオンを使用できるようにします。

1. Azure portal で **[エンタープライズ アプリケーション]** を選択し、 **[すべてのアプリケーション]** 、 **[ArcGIS Enterprise]** の順に選択します。

    ![[エンタープライズ アプリケーション] ブレード](common/enterprise-applications.png)

2. アプリケーションの一覧で「**ArcGIS Enterprise**」と入力して選択します。

    ![アプリケーションの一覧の ArcGIS Enterprise のリンク](common/all-applications.png)

3. 左側のメニューで **[ユーザーとグループ]** を選びます。

    ![[ユーザーとグループ] リンク](common/users-groups-blade.png)

4. **[ユーザーの追加]** をクリックし、 **[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。

    ![[割り当ての追加] ウィンドウ](common/add-assign-user.png)

5. **[ユーザーとグループ]** ダイアログの [ユーザー] の一覧で **[Britta Simon]** を選択し、画面の下部にある **[選択]** ボタンをクリックします。

6. SAML アサーション内に任意のロール値が必要な場合、 **[ロールの選択]** ダイアログでユーザーに適したロールを一覧から選択し、画面の下部にある **[選択]** をクリッします。

7. **[割り当ての追加]** ダイアログで、 **[割り当て]** ボタンをクリックします。

### <a name="create-arcgis-enterprise-test-user"></a>ArcGIS Enterprise テスト ユーザーを作成する

このセクションでは、Britta Simon というユーザーを ArcGIS Enterprise に作成します。 ArcGIS Enterprise では、Just-In-Time ユーザー プロビジョニングがサポートされています。この設定は既定で有効になっています。 このセクションでは、ユーザー側で必要な操作はありません。 ArcGIS Enterprise にユーザーがまだ存在していない場合は、認証後に新規に作成されます。

> [!Note]
> ユーザーを手動で作成する必要がある場合は、 [ArcGIS Enterprise のサポート チーム](mailto:support@esri.com)にお問い合わせください。

### <a name="test-single-sign-on"></a>シングル サインオンのテスト 

このセクションでは、アクセス パネルを使用して Azure AD のシングル サインオン構成をテストします。

アクセス パネルで [ArcGIS Enterprise] タイルをクリックすると、SSO を設定した ArcGIS Enterprise に自動的にサインインします。 アクセス パネルの詳細については、[アクセス パネルの概要](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)に関する記事を参照してください。

## <a name="additional-resources"></a>その他のリソース

- [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Azure Active Directory の条件付きアクセスとは](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

