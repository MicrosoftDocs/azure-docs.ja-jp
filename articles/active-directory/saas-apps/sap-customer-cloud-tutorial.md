---
title: チュートリアル:Azure Active Directory と SAP Cloud for Customer の統合 | Microsoft Docs
description: Azure Active Directory と SAP Cloud for Customer の間でシングル サインオンを構成する方法について説明します。
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: 90154dab-eba2-4563-bcf0-f2acc797ea97
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 12/19/2018
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 669dfaa40cfe1bc65618d8706910e19d72c233ad
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/13/2019
ms.locfileid: "67092051"
---
# <a name="tutorial-azure-active-directory-integration-with-sap-cloud-for-customer"></a>チュートリアル:Azure Active Directory と SAP Cloud for Customer の統合

このチュートリアルでは、SAP Cloud for Customer と Azure Active Directory (Azure AD) を統合する方法について説明します。
SAP Cloud for Customer と Azure AD の統合には、次の利点があります。

* SAP Cloud for Customer にアクセスする Azure AD ユーザーを制御できます。
* ユーザーが自分の Azure AD アカウントで自動的に SAP Cloud for Customer にサインオン (シングル サインオン) できるように、設定が可能です。
* 1 つの中央サイト (Azure Portal) でアカウントを管理できます。

SaaS アプリと Azure AD の統合の詳細については、「 [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)」を参照してください。
Azure サブスクリプションをお持ちでない場合は、開始する前に[無料アカウントを作成](https://azure.microsoft.com/free/)してください。

## <a name="prerequisites"></a>前提条件

Azure AD と SAP Cloud for Customer の統合を構成するには、次のものが必要です。

* Azure AD サブスクリプション。 Azure AD の環境がない場合は、[こちら](https://azure.microsoft.com/pricing/free-trial/)から 1 か月の評価版を入手できます
* SAP Cloud for Customer でのシングル サインオンが有効なサブスクリプション

## <a name="scenario-description"></a>シナリオの説明

このチュートリアルでは、テスト環境で Azure AD のシングル サインオンを構成してテストします。

* SAP Cloud for Customer では、**SP** によって開始される SSO がサポートされます

## <a name="adding-sap-cloud-for-customer-from-the-gallery"></a>ギャラリーから SAP Cloud for Customer を追加する

Azure AD への SAP Cloud for Customer の統合を構成するには、ギャラリーから管理対象 SaaS アプリの一覧に SAP Cloud for Customer を追加する必要があります。

**ギャラリーから SAP Cloud for Customer を追加するには、次の手順に従います。**

1. **[Azure Portal](https://portal.azure.com)** の左側のナビゲーション ウィンドウで、 **[Azure Active Directory]** アイコンをクリックします。

    ![Azure Active Directory のボタン](common/select-azuread.png)

2. **[エンタープライズ アプリケーション]** に移動し、 **[すべてのアプリケーション]** オプションを選択します。

    ![[エンタープライズ アプリケーション] ブレード](common/enterprise-applications.png)

3. 新しいアプリケーションを追加するには、ダイアログの上部にある **[新しいアプリケーション]** をクリックします。

    ![[新しいアプリケーション] ボタン](common/add-new-app.png)

4. 検索ボックスに「**SAP Cloud for Customer**」と入力して、結果パネルで **[SAP Cloud for Customer]** を選択し、 **[追加]** をクリックして、アプリケーションを追加します。

     ![結果リストの SAP Cloud for Customer](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成とテスト

このセクションでは、**Britta Simon** というテスト ユーザーに基づいて、SAP Cloud for Customer で Azure AD のシングル サインオンを構成し、テストします。
シングル サインオンを機能させるには、Azure AD ユーザーと SAP Cloud for Customer 内の関連ユーザー間にリンク関係が確立されている必要があります。

SAP Cloud for Customer で Azure AD のシングル サインオンを構成してテストするには、次の構成要素を完了する必要があります。

1. **[Azure AD シングル サインオンの構成](#configure-azure-ad-single-sign-on)** - ユーザーがこの機能を使用できるようにします。
2. **[SAP Cloud for Customer シングル サインオンの構成](#configure-sap-cloud-for-customer-single-sign-on)** - アプリケーション側でシングル サインオン設定を構成します。
3. **[Azure AD のテスト ユーザーの作成](#create-an-azure-ad-test-user)** - Britta Simon で Azure AD のシングル サインオンをテストします。
4. **[Azure AD テスト ユーザーの割り当て](#assign-the-azure-ad-test-user)** - Britta Simon が Azure AD シングル サインオンを使用できるようにします。
5. **[SAP Cloud for Customer のテスト ユーザーの作成](#create-sap-cloud-for-customer-test-user)** - SAP Cloud for Customer で Britta Simon に対応するユーザーを作成し、Azure AD の Britta Simon にリンクさせます。
6. **[シングル サインオンのテスト](#test-single-sign-on)** - 構成が機能するかどうかを確認します。

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成

このセクションでは、Azure portal 上で Azure AD のシングル サインオンを有効にします。

SAP Cloud for Customer で Azure AD シングル サインオンを構成するには、次の手順に従います。

1. [Azure portal](https://portal.azure.com/) の **SAP Cloud for Customer** アプリケーション統合ページで、 **[シングル サインオン]** を選択します。

    ![シングル サインオン構成のリンク](common/select-sso.png)

2. **[シングル サインオン方式の選択]** ダイアログで、 **[SAML/WS-Fed]** モードを選択して、シングル サインオンを有効にします。

    ![シングル サインオン選択モード](common/select-saml-option.png)

3. **[SAML でシングル サインオンをセットアップします]** ページで、 **[編集]** アイコンをクリックして **[基本的な SAML 構成]** ダイアログを開きます。

    ![基本的な SAML 構成を編集する](common/edit-urls.png)

4. **[基本的な SAML 構成]** セクションで、次の手順を実行します。

    ![[SAP Cloud for Customer のドメインと URL] のシングル サインオン情報](common/sp-identifier.png)

    a. **[サインオン URL]** ボックスに、次のパターンを使用して URL を入力します。`https://<server name>.crm.ondemand.com`

    b. **[識別子 (エンティティ ID)]** ボックスに、次のパターンを使用して URL を入力します。`https://<server name>.crm.ondemand.com`

    > [!NOTE]
    > これらは実際の値ではありません。 実際のサインオン URL と識別子でこれらの値を更新します。 これらの値を取得するには、[SAP Cloud for Customer クライアント サポート チーム](https://www.sap.com/about/agreements.sap-cloud-services-customers.html)に連絡してください。 Azure portal の **[基本的な SAML 構成]** セクションに示されているパターンを参照することもできます。

5. SAP Cloud for Customer アプリケーションは、特定の形式で構成された SAML アサーションを受け入れます。 このアプリケーションには、次の要求を構成します。 これらの属性の値は、アプリケーション統合ページの **[ユーザー属性]** セクションで管理できます。 **[SAML でシングル サインオンをセットアップします]** ページで、 **[編集]** ボタンをクリックして **[ユーザー属性]** ダイアログを開きます。

    ![image](common/edit-attribute.png)

6. **[ユーザー属性とクレーム]** ダイアログの **[ユーザー属性]** セクションで、次の手順を実行します。

    a. **[編集]** アイコンをクリックして、 **[ユーザー要求の管理]** ダイアログを開きます。

    ![image](./media/sap-customer-cloud-tutorial/tutorial_usermail.png)

    ![image](./media/sap-customer-cloud-tutorial/tutorial_usermailedit.png)

    b. **ソース**として **[変換]** を選択します。

    c. **[変換]** の一覧で、**ExtractMailPrefix()** を選択します。

    d. **[パラメーター 1]** 一覧から、実装で使用するユーザー属性を選択します。
    たとえば、一意のユーザー識別子として EmployeeID を使用し、その属性値を ExtensionAttribute2 に保存している場合、[user.extensionattribute2] を選択します。

    e. **[Save]** をクリックします。

7. **[SAML でシングル サインオンをセットアップします]** ページの **[SAML 署名証明書]** セクションで、 **[ダウンロード]** をクリックして、要件のとおりに指定したオプションから**フェデレーション メタデータ XML** をダウンロードして、お使いのコンピューターに保存します。

    ![証明書のダウンロードのリンク](common/metadataxml.png)

8. **[SAP Cloud for Customer のセットアップ]** セクションで、要件のとおりに適切な URL をコピーします。

    ![構成 URL のコピー](common/copy-configuration-urls.png)

    a. ログイン URL

    b. Azure AD 識別子

    c. ログアウト URL

### <a name="configure-sap-cloud-for-customer-single-sign-on"></a>SAP Cloud for Customer でシングル サインオンを構成する
   
1. SAP Cloud for Customer ポータルに管理者権限でログインします。
   
2. **[Application and User Management Common Task (アプリケーションとユーザー管理の共通タスク)]** に移動し、 **[ID プロバイダー]** タブをクリックします。
   
3. **[New Identity Provider (新しい ID プロバイダー)]** をクリックし、Azure Portal からダウンロードしたメタデータの XML ファイルを選択します。 メタデータをインポートすることによって、必要な署名証明書と暗号化証明書が自動的にアップロードされます。
   
    ![Configure single sign-on](./media/sap-customer-cloud-tutorial/tutorial_sapcloudforcustomer_54.png)
   
4. Azure Active Directory は、SAML 要求で Assertion Consumer Service URL 要素を必要とするため、 **[Include Assertion Consumer Service URL (Assertion Consumer Service URL を含める)]** チェック ボックスをオンにします。
   
5. **[Activate Single Sign-On (シングル サインオンを有効にする)]** をクリックします。
   
6. 変更を保存します。
   
7. **[My System (自分のシステム)]** タブをクリックします。
   
    ![Configure single sign-on](./media/sap-customer-cloud-tutorial/tutorial_sapcloudforcustomer_52.png)
   
8. **[Azure AD Sign On URL]\(Azure AD サインオン URL\)** テキストボックスに、Azure portal からコピーした**ログイン URL** を貼り付けます。
   
    ![Configure single sign-on](./media/sap-customer-cloud-tutorial/tutorial_sapcloudforcustomer_53.png)
   
9. ユーザー ID とパスワードでログオンするか、SSO でログオンするかを従業員が手動で選択できるかどうかを、 **[Manual Identity Provider Selection (ID プロバイダーの手動選択)]** を選択して指定します。
   
10. **[SSO URL]** セクションには、従業員がシステムへのサインオンに使用する URL を指定します。 
    **[URL Sent to Employee (従業員に送信する URL)]** 一覧で、次のオプションを選択できます。
   
    **[Non-SSO URL (非 SSO URL)]**
   
    従業員に送信されるのは、システムの通常の URL のみです。 従業員のログオンに SSO は使用できず、パスワードまたは証明書を使用する必要があります。
   
    **[SSO URL (SSO の URL)]** 
   
    従業員に送信されるのは、SSO の URL のみです。 従業員は SSO を使用してログオンすることができます。 認証要求は IdP を介してリダイレクトされます。
   
    **[Automatic Selection (自動選択)]**
   
    SSO が有効ではない場合、システムの通常の URL が従業員に送信されます。 SSO が有効である場合は、従業員がパスワードを持っているかどうかがシステムによってチェックされます。 パスワードを持っていた場合は、SSO の URL と非 SSO の URL の両方が従業員に送信されます。 一方、従業員がパスワードを持っていない場合は、SSO の URL だけが従業員に送信されます。
   
11. 変更を保存します。

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

このセクションでは、Britta Simon に SAP Cloud for Customer へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

1. Azure portal 上で **[エンタープライズ アプリケーション]** を選択し、 **[すべてのアプリケーション]** を選択してから、 **[SAP Cloud for Customer]** を選択します。

    ![[エンタープライズ アプリケーション] ブレード](common/enterprise-applications.png)

2. アプリケーションの一覧で、「 **[SAP Cloud for Customer]** 」と入力して選択します。

    ![アプリケーション一覧の SAP Cloud for Customer のリンク](common/all-applications.png)

3. 左側のメニューで **[ユーザーとグループ]** を選びます。

    ![[ユーザーとグループ] リンク](common/users-groups-blade.png)

4. **[ユーザーの追加]** をクリックし、 **[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。

    ![[割り当ての追加] ウィンドウ](common/add-assign-user.png)

5. **[ユーザーとグループ]** ダイアログの [ユーザー] の一覧で **[Britta Simon]** を選択し、画面の下部にある **[選択]** ボタンをクリックします。

6. SAML アサーション内に任意のロール値が必要な場合、 **[ロールの選択]** ダイアログでユーザーに適したロールを一覧から選択し、画面の下部にある **[選択]** をクリッします。

7. **[割り当ての追加]** ダイアログで、 **[割り当て]** ボタンをクリックします。

### <a name="create-sap-cloud-for-customer-test-user"></a>SAP Cloud for Customer のテスト ユーザーの作成

このセクションでは、SAP Cloud for Customer で Britta Simon というユーザーを作成します。 SAP Cloud for Customer プラットフォームにユーザーを追加する方法については、 [SAP Cloud for Customer サポート チーム](https://www.sap.com/about/agreements.sap-cloud-services-customers.html)にお問い合わせください。 シングル サインオンを使用する前に、ユーザーを作成し、有効化する必要があります。

> [!NOTE]
> NameID 値は必ず、SAP Cloud for Customer プラットフォームのユーザー名フィールドと一致させてください。

### <a name="test-single-sign-on"></a>シングル サインオンのテスト 

このセクションでは、アクセス パネルを使用して Azure AD のシングル サインオン構成をテストします。

アクセス パネル上で [SAP Cloud for Customer] タイルをクリックすると、SSO を設定した SAP Cloud for Customer に自動的にサインインします。 アクセス パネルの詳細については、[アクセス パネルの概要](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)に関する記事を参照してください。

## <a name="additional-resources"></a>その他のリソース

- [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Azure Active Directory の条件付きアクセスとは](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

