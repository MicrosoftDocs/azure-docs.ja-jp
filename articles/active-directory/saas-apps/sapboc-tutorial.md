---
title: チュートリアル:Azure Active Directory と SAP Business Object Cloud の統合 | Microsoft Docs
description: Azure Active Directory と SAP Business Object Cloud の間でシングル サインオンを構成する方法について説明します。
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: 6c5e44f0-4e52-463f-b879-834d80a55cdf
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 12/31/2018
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8ad2ffddf96aa6ecc886ac5653d2d0b8dcfb0856
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/13/2019
ms.locfileid: "67091712"
---
# <a name="tutorial-azure-active-directory-integration-with-sap-business-object-cloud"></a>チュートリアル:Azure Active Directory と SAP Business Object Cloud の統合

このチュートリアルでは、SAP Business Object Cloud と Azure Active Directory (Azure AD) を統合する方法について説明します。
SAP Business Object Cloud と Azure AD の統合には、次の利点があります。

* SAP Business Object Cloud にアクセスできるユーザーを Azure AD で制御できます。
* ユーザーが自分の Azure AD アカウントで SAP Business Object Cloud に自動的にサインオン (シングル サインオン) するように設定できます。
* 1 つの中央サイト (Azure Portal) でアカウントを管理できます。

SaaS アプリと Azure AD の統合の詳細については、「 [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)」を参照してください。
Azure サブスクリプションをお持ちでない場合は、開始する前に[無料アカウントを作成](https://azure.microsoft.com/free/)してください。

## <a name="prerequisites"></a>前提条件

Azure AD と SAP Business Object Cloud の統合を構成するには、次のものが必要です。

* Azure AD サブスクリプション。 Azure AD の環境がない場合は、[こちら](https://azure.microsoft.com/pricing/free-trial/)から 1 か月の評価版を入手できます
* SAP Business Object Cloud でのシングル サインオンが有効なサブスクリプション

## <a name="scenario-description"></a>シナリオの説明

このチュートリアルでは、テスト環境で Azure AD のシングル サインオンを構成してテストします。

* SAP Business Object Cloud では、**SP** によって開始される SSO がサポートされます

## <a name="adding-sap-business-object-cloud-from-the-gallery"></a>ギャラリーから SAP Business Object Cloud を追加する

Azure AD への SAP Business Object Cloud の統合を構成するには、ギャラリーから管理対象 SaaS アプリの一覧に SAP Business Object Cloud を追加する必要があります。

**ギャラリーから SAP Business Object Cloud を追加するには、次の手順に従います。**

1. **[Azure Portal](https://portal.azure.com)** の左側のナビゲーション ウィンドウで、 **[Azure Active Directory]** アイコンをクリックします。

    ![Azure Active Directory のボタン](common/select-azuread.png)

2. **[エンタープライズ アプリケーション]** に移動し、 **[すべてのアプリケーション]** オプションを選択します。

    ![[エンタープライズ アプリケーション] ブレード](common/enterprise-applications.png)

3. 新しいアプリケーションを追加するには、ダイアログの上部にある **[新しいアプリケーション]** をクリックします。

    ![[新しいアプリケーション] ボタン](common/add-new-app.png)

4. 検索ボックスに「**SAP Business Object Cloud**」と入力し、結果パネルで **[SAP Business Object Cloud]** を選び、 **[追加]** をクリックして、アプリケーションを追加します。

     ![結果リストの SAP Business Object Cloud](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成とテスト

このセクションでは、**Britta Simon** というテスト ユーザーに基づいて、SAP Business Object Cloud で Azure AD のシングル サインオンを構成し、テストします。
シングル サインオンを機能させるには、Azure AD ユーザーと SAP Business Object Cloud 内の関連ユーザーとの間にリンク関係が確立されている必要があります。

SAP Business Object Cloud で Azure AD のシングル サインオンを構成してテストするには、次の手順を完了する必要があります。

1. **[Azure AD シングル サインオンの構成](#configure-azure-ad-single-sign-on)** - ユーザーがこの機能を使用できるようにします。
2. **[SAP Business Object Cloud のシングル サインオンの構成](#configure-sap-business-object-cloud-single-sign-on)** - アプリケーション側でシングル サインオン設定を構成します。
3. **[Azure AD のテスト ユーザーの作成](#create-an-azure-ad-test-user)** - Britta Simon で Azure AD のシングル サインオンをテストします。
4. **[Azure AD テスト ユーザーの割り当て](#assign-the-azure-ad-test-user)** - Britta Simon が Azure AD シングル サインオンを使用できるようにします。
5. **[SAP Business Object Cloud のテスト ユーザーの作成](#create-sap-business-object-cloud-test-user)** - SAP Business Object Cloud で Britta Simon に対応するユーザーを作成し、Azure AD の Britta Simon にリンクさせます。
6. **[シングル サインオンのテスト](#test-single-sign-on)** - 構成が機能するかどうかを確認します。

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成

このセクションでは、Azure portal 上で Azure AD のシングル サインオンを有効にします。

SAP Business Object Cloud で Azure AD シングル サインオンを構成するには、次の手順に従います。

1. [Azure portal](https://portal.azure.com/) の **SAP Business Object Cloud** アプリケーション統合ページで、 **[シングル サインオン]** を選択します。

    ![シングル サインオン構成のリンク](common/select-sso.png)

2. **[シングル サインオン方式の選択]** ダイアログで、 **[SAML/WS-Fed]** モードを選択して、シングル サインオンを有効にします。

    ![シングル サインオン選択モード](common/select-saml-option.png)

3. **[SAML でシングル サインオンをセットアップします]** ページで、 **[編集]** アイコンをクリックして **[基本的な SAML 構成]** ダイアログを開きます。

    ![基本的な SAML 構成を編集する](common/edit-urls.png)

4. **[基本的な SAML 構成]** セクションで、次の手順を実行します。

    ![[SAP Business Object Cloud のドメインと URL] のシングル サインオン情報](common/sp-identifier.png)

    a. **[サインオン URL]** ボックスに、次の形式で URL を入力します。

    | |
    |-|-|
    | `https://<sub-domain>.sapanalytics.cloud/` |
    | `https://<sub-domain>.sapbusinessobjects.cloud/` |

    b. **[識別子 (エンティティ ID)]** テキスト ボックスに、次のパターンで URL を入力します。
    
    | |
    |-|-|
    | `<sub-domain>.sapbusinessobjects.cloud` |
    | `<sub-domain>.sapanalytics.cloud` |

    > [!NOTE] 
    > これらの URL の値は、単なる例です。 実際のサインオン URL と識別子 URLでこれらの値を更新してください。 サインオン URL を取得するには、[SAP Business Object Cloud クライアント サポート チーム](https://help.sap.com/viewer/product/SAP_BusinessObjects_Cloud/release/)に問い合わせてください。 識別子 URL は、管理コンソールから SAP Business Object Cloud のメタデータをダウンロードすることで取得できます。 これについては、このチュートリアルの後半で説明します。

4. **[SAML でシングル サインオンをセットアップします]** ページの **[SAML 署名証明書]** セクションで、 **[ダウンロード]** をクリックして、要件のとおりに指定したオプションから**フェデレーション メタデータ XML** をダウンロードして、お使いのコンピューターに保存します。

    ![証明書のダウンロードのリンク](common/metadataxml.png)

### <a name="configure-sap-business-object-cloud-single-sign-on"></a>SAP Business Object Cloud のシングル サインオンの構成

1. Web ブラウザーの別のウィンドウで、SAP Business Object Cloud 企業サイトに管理者としてサインインします。

2. **[Menu]\(メニュー\)**  >  **[System]\(システム\)**  >  **[Administration]\(管理\)** を選択します。
    
    ![[Menu]\(メニュー\)、[System]\(システム\)、および [Administration]\(管理\) の選択](./media/sapboc-tutorial/config1.png)

3. **[Security]\(セキュリティ\)** タブで **[Edit]\(編集\)** (ペン) アイコンを選択します。
    
    ![[Security]\(セキュリティ\) タブで [Edit]\(編集\)アイコンを選択](./media/sapboc-tutorial/config2.png)  

4. **[Authentication Method]\(認証方法\)** として **[SAML Single Sign-On (SSO)]\(SAML シングル サインオン (SSO)\)** を選択します。

    ![認証方法として [SAML Single Sign-On (SSO)]\(SAML シングル サインオン (SSO)\) を選択](./media/sapboc-tutorial/config3.png)  

5. サービス プロバイダーのメタデータをダウンロードする (手順 1) には、 **[Download]\(ダウンロード\)** を選択します。 メタデータ ファイルで、**entityID** を見つけてその値をコピーします。 Azure portal で、 **[基本的な SAML 構成]** ダイアログの **[識別子]** ボックスに値を貼り付けます。

    ![EntityID 値をコピーして貼り付ける](./media/sapboc-tutorial/config4.png)  

6. Azure ポータルからダウンロードしたファイル内のサービス プロバイダーのメタデータをアップロードする (手順 2) には、 **[Upload Identity Provider metadata]\(ID プロバイダーのメタデータのアップロード\)** で、 **[アップロード]** を選択します。  

    ![[Upload Identity Provider metadata]\(ID プロバイダーのメタデータのアップロード\) で [アップロード] を選択](./media/sapboc-tutorial/config5.png)

7. **[User Attribute]\(ユーザー属性\)** の一覧で、実装で使用するユーザー属性を選択します (手順 3)。 このユーザー属性が ID プロバイダーにマップされます。 ユーザーのページでカスタム属性を入力するには、 **[Custom SAML Mapping]\(カスタム SAML マッピング\)** オプションを使用します。 または、ユーザー属性として **[Email]\(電子メール\)** または **[USER ID]\(ユーザー ID\)** のいずれかを選択できます。 この例では、Azure portal で **[ユーザー属性とクレーム]** セクションの **userprincipalname** 属性にユーザー識別子要求をマップしたため、 **[Email]\(電子メール\)** を選択しています。 これにより、一意のユーザーの電子メールが用意され、SAML 応答が成功するたびに SAP Business Object Cloud アプリケーションに送信されます。

    ![ユーザー属性の選択](./media/sapboc-tutorial/config6.png)

8. アカウントを ID プロバイダーで確認する (ステップ 4) には、 **[Login Credential (Email)]\(ログイン資格情報 (電子メール)\)** ボックスに、ユーザーの電子メール アドレスを入力します。 次に、 **[Verify Account]\(アカウントの確認\)** を選択します。 システムがサインイン資格情報をユーザー アカウントに追加します。

    ![電子メール アドレスを入力し、[Verify Account]\(アカウントの確認\) を選択](./media/sapboc-tutorial/config7.png)

9. **[Save]\(保存\)**  アイコンを選択します。

    ![[Save]\(保存\) アイコン](./media/sapboc-tutorial/save.png)

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

このセクションでは、Britta Simon に SAP Business Object Cloud へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

1. Azure portal 上で **[エンタープライズ アプリケーション]** を選択し、 **[すべてのアプリケーション]** を選択してから、 **[SAP Business Object Cloud]** を選択します。

    ![[エンタープライズ アプリケーション] ブレード](common/enterprise-applications.png)

2. アプリケーションの一覧で **[SAP Business Object Cloud]** を選択します。

    ![アプリケーションの一覧の SAP Business Object Cloud リンク](common/all-applications.png)

3. 左側のメニューで **[ユーザーとグループ]** を選びます。

    ![[ユーザーとグループ] リンク](common/users-groups-blade.png)

4. **[ユーザーの追加]** をクリックし、 **[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。

    ![[割り当ての追加] ウィンドウ](common/add-assign-user.png)

5. **[ユーザーとグループ]** ダイアログの [ユーザー] の一覧で **[Britta Simon]** を選択し、画面の下部にある **[選択]** ボタンをクリックします。

6. SAML アサーション内に任意のロール値が必要な場合、 **[ロールの選択]** ダイアログでユーザーに適したロールを一覧から選択し、画面の下部にある **[選択]** をクリッします。

7. **[割り当ての追加]** ダイアログで、 **[割り当て]** ボタンをクリックします。

### <a name="create-sap-business-object-cloud-test-user"></a>SAP Business Object Cloud のテスト ユーザーの作成

Azure AD ユーザーが SAP Business Object Cloud にサインインできるようにするには、そのユーザーを SAP Business Object Cloud にプロビジョニングしておく必要があります。 SAP Business Object Cloud では、プロビジョニングは手動のタスクです。

ユーザー アカウントをプロビジョニングするには:

1. SAP Business Object Cloud 企業サイトに管理者としてサインインします。

2. **[Menu]\(メニュー\)**  >  **[Security]\(セキュリティ\)**  >  **[Users]\(ユーザー\)** を選択します。

    ![従業員の追加](./media/sapboc-tutorial/user1.png)

3. **[Users]\(ユーザー\)** ページで、新しいユーザーの詳細を追加するには、 **+** を選択します。 

    ![[Add Users]\(ユーザーの追加\) ページ](./media/sapboc-tutorial/user4.png)

    その後、次の手順を完了します。

    a. **[USER ID]\(ユーザー ID\)** ボックスに、ユーザー ID を入力します (この例では **Britta**)。

    b. **[FIRST NAME]\(名\)** ボックスに、ユーザーの名を入力します (この例では **Britta**)。

    c. **[LAST NAME]\(姓\)** ボックスに、ユーザーの姓を入力します (この例では **Simon**)。

    d. **[DISPLAY NAME]\(表示名\)** ボックスに、ユーザーのフル ネームを入力します (この例では **Britta Simon**)。

    e. **[E-MAIL]\(電子メール\)** ボックスに、ユーザーのメール アドレスを入力します (この例では **brittasimon\@contoso.com**)。

    f. **[Select Roles]\(ロールの選択\)** ページで、ユーザーの適切なロールを選択し、 **[OK]** を選択します。

      ![Select role](./media/sapboc-tutorial/user3.png)

    g. **[Save]\(保存\)**  アイコンを選択します。    

### <a name="test-single-sign-on"></a>シングル サインオンのテスト 

このセクションでは、アクセス パネルを使用して Azure AD のシングル サインオン構成をテストします。

アクセス パネルで [SAP Business Object Cloud] タイルをクリックすると、SSO を設定した SAP Business Object Cloud に自動的にサインインします。 アクセス パネルの詳細については、[アクセス パネルの概要](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)に関する記事を参照してください。

## <a name="additional-resources"></a>その他のリソース

- [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Azure Active Directory の条件付きアクセスとは](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

