---
title: チュートリアル:Azure Active Directory と Adobe Experience Manager の統合 | Microsoft Docs
description: Azure Active Directory と Adobe Experience Manager の間でシングル サインオンを構成する方法について説明します。
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: 88a95bb5-c17c-474f-bb92-1f80f5344b5a
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 01/17/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 81032fbad21b18b0b7ca2e7662b0c4b4b6c10901
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/13/2019
ms.locfileid: "67107275"
---
# <a name="tutorial-azure-active-directory-integration-with-adobe-experience-manager"></a>チュートリアル:Azure Active Directory と Adobe Experience Manager の統合

このチュートリアルでは、Adobe Experience Manager と Azure Active Directory (Azure AD) を統合する方法について説明します。
Adobe Experience Manager と Azure AD を統合すると、次の利点があります。

* Adobe Experience Manager にどの Azure AD ユーザーがアクセスできるかを管理できます。
* ユーザーが自分の Azure AD アカウントで Adobe Experience Manager に自動的にサインイン (シングル サインオン) するように設定できます。
* 1 つの中央サイト (Azure Portal) でアカウントを管理できます。

SaaS アプリと Azure AD の統合の詳細については、「 [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)」を参照してください。
Azure サブスクリプションをお持ちでない場合は、開始する前に[無料アカウントを作成](https://azure.microsoft.com/free/)してください。

## <a name="prerequisites"></a>前提条件

Adobe Experience Manager と Azure AD の統合を構成するには、次のものが必要です。

* Azure AD サブスクリプション。 Azure AD の環境がない場合は、[こちら](https://azure.microsoft.com/pricing/free-trial/)から 1 か月の評価版を入手できます
* Adobe Experience Manager でのシングル サインオンが有効に設定されたサブスクリプション

## <a name="scenario-description"></a>シナリオの説明

このチュートリアルでは、テスト環境で Azure AD のシングル サインオンを構成してテストします。

* Adobe Experience Manager では、**SP と IDP** によって開始される SSO がサポートされます

* Adobe Experience Manager では、**Just-In-Time** ユーザー プロビジョニングがサポートされます

## <a name="adding-adobe-experience-manager-from-the-gallery"></a>ギャラリーから Adobe Experience Manager を追加する

Azure AD と Adobe Experience Manager の統合を構成するには、ギャラリーから管理対象 SaaS アプリの一覧に Adobe Experience Manager を追加する必要があります。

**ギャラリーから Adobe Experience Manager を追加するには、次の手順を実行します。**

1. **[Azure Portal](https://portal.azure.com)** の左側のナビゲーション ウィンドウで、 **[Azure Active Directory]** アイコンをクリックします。

    ![Azure Active Directory のボタン](common/select-azuread.png)

2. **[エンタープライズ アプリケーション]** に移動し、 **[すべてのアプリケーション]** オプションを選択します。

    ![[エンタープライズ アプリケーション] ブレード](common/enterprise-applications.png)

3. 新しいアプリケーションを追加するには、ダイアログの上部にある **[新しいアプリケーション]** をクリックします。

    ![[新しいアプリケーション] ボタン](common/add-new-app.png)

4. 検索ボックスに「**Adobe Experience Manager**」と入力し、結果ウィンドウで **[Adobe Experience Manager]** を選択し、 **[追加]** ボタンをクリックしてアプリケーションを追加します。

     ![結果一覧の Adobe Experience Manager](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成とテスト

このセクションでは、**Britta Simon** というテスト ユーザーに基づいて、[アプリケーション名] を使用して Azure AD のシングル サインオンを構成し、テストします。
シングル サインオンを機能させるには、Azure AD ユーザーと [アプリケーション名] 内の関連ユーザー間にリンク関係が確立されている必要があります。

[アプリケーション名] を使用して Azure AD のシングル サインオンを構成し、テストするには、次の構成要素を完了する必要があります。

1. **[Azure AD シングル サインオンの構成](#configure-azure-ad-single-sign-on)** - ユーザーがこの機能を使用できるようにします。
2. **[Adobe Experience Manager のシングル サインオンの構成](#configure-adobe-experience-manager-single-sign-on)** - アプリケーション側でシングル サインオン設定を構成します。
3. **[Azure AD のテスト ユーザーの作成](#create-an-azure-ad-test-user)** - Britta Simon で Azure AD のシングル サインオンをテストします。
4. **[Azure AD テスト ユーザーの割り当て](#assign-the-azure-ad-test-user)** - Britta Simon が Azure AD シングル サインオンを使用できるようにします。
5. **[Adobe Experience Manager のテスト ユーザーの作成](#create-adobe-experience-manager-test-user)** - Adobe Experience Manager で Britta Simon に対応するユーザーを作成し、Azure AD の Britta Simon にリンクします。
6. **[シングル サインオンのテスト](#test-single-sign-on)** - 構成が機能するかどうかを確認します。

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成

このセクションでは、Azure portal 上で Azure AD のシングル サインオンを有効にします。

[アプリケーション名] を使用して Azure AD のシングル サインオンを構成するには、次の手順を実行します。

1. [Azure portal](https://portal.azure.com/) の **Adobe Experience Manager** アプリケーション統合ページで、 **[シングル サインオン]** を選択します。

    ![シングル サインオン構成のリンク](common/select-sso.png)

2. **[シングル サインオン方式の選択]** ダイアログで、 **[SAML/WS-Fed]** モードを選択して、シングル サインオンを有効にします。

    ![シングル サインオン選択モード](common/select-saml-option.png)

3. **[SAML でシングル サインオンをセットアップします]** ページで、 **[編集]** アイコンをクリックして **[基本的な SAML 構成]** ダイアログを開きます。

    ![基本的な SAML 構成を編集する](common/edit-urls.png)

4. **[基本的な SAML 構成]** セクションで、アプリケーションを **IDP** 開始モードで構成する場合は、次の手順を実行します。

    ![Adobe Experience Manager のドメインと URL のシングル サインオン情報](common/idp-intiated.png)

    a. **[識別子]** ボックスに、AEM のサーバーにも定義する一意の値を入力します。

    b. **[応答 URL]** ボックスに、`https://<AEM Server Url>/saml_login` のパターンを使用して URL を入力します

    > [!NOTE]
    > 応答 URL 値は、実際の値ではありません。 応答 URL 値を実際の応答 URL で更新します。 この値を取得するには、[Adobe Experience Manager クライアント サポート チーム](https://helpx.adobe.com/support/experience-manager.html)に問い合わせてください。 Azure portal の **[基本的な SAML 構成]** セクションに示されているパターンを参照することもできます。

5. アプリケーションを **SP** 開始モードで構成する場合は、 **[追加の URL を設定します]** をクリックして次の手順を実行します。

    ![Adobe Experience Manager のドメインと URL のシングル サインオン情報](common/metadata-upload-additional-signon.png)

    **[サインオン URL]** ボックスに、Adobe Experience Manager サーバーの URL を入力します。

6. **[SAML でシングル サインオンをセットアップします]** ページの **[SAML 署名証明書]** セクションで、 **[ダウンロード]** をクリックして要件のとおりに指定したオプションからの**証明書 (Base64)** をダウンロードして、お使いのコンピューターに保存します。

    ![証明書のダウンロードのリンク](common/certificatebase64.png)

7. **[Adobe Experience Manager のセットアップ]** セクションで、要件に従って適切な URL をコピーします。

    ![構成 URL のコピー](common/copy-configuration-urls.png)

    a. ログイン URL

    b. Azure AD 識別子

    c. ログアウト URL

### <a name="configure-adobe-experience-manager-single-sign-on"></a>Adobe Experience Manager のシングル サインオンの構成

1. 別のブラウザー ウィンドウで **Adobe Experience Manager** の管理ポータルを開きます。

2. **[設定]**  >  **[セキュリティ]**  >  **[ユーザー]** を選択します。

    ![[シングル サインオンの構成] の [保存] ボタン](./media/adobeexperiencemanager-tutorial/tutorial_adobeexperiencemanager_user.png)

3. **[管理者]** またはその他の関連するユーザーを選択します。

    ![[シングル サインオンの構成] の [保存] ボタン](./media/adobeexperiencemanager-tutorial/tutorial_adobeexperiencemanager_admin6.png)

4. **[アカウント設定]**  >  **[Manage TrustStore]\(トラストストアの管理\)** を選択します。

    ![[シングル サインオンの構成] の [保存] ボタン](./media/adobeexperiencemanager-tutorial/tutorial_adobeexperiencemanager_managetrust.png)

5. **[Add Certificate from CER file]\(CER ファイルから証明書を追加\)** で、 **[証明書ファイルの選択]** をクリックします。 Azure Portal から既にダウンロードしている証明書ファイルを参照し選択します。

    ![[シングル サインオンの構成] の [保存] ボタン](./media/adobeexperiencemanager-tutorial/tutorial_adobeexperiencemanager_user2.png)

6. 証明書がトラストストアに追加されます。 証明書の別名に注意してください。

    ![[シングル サインオンの構成] の [保存] ボタン](./media/adobeexperiencemanager-tutorial/tutorial_adobeexperiencemanager_admin7.png)

7. **[ユーザー]** ページで、 **[認証サービス]** を選択します。

    ![[シングル サインオンの構成] の [保存] ボタン](./media/adobeexperiencemanager-tutorial/tutorial_adobeexperiencemanager_admin8.png)

8. **[アカウント設定]**  >  **[Create/Manage TrustStore]\(キーストアの作成および管理\)** を選択します。 パスワードを入力して、キーストアを作成します。

    ![[シングル サインオンの構成] の [保存] ボタン](./media/adobeexperiencemanager-tutorial/tutorial_adobeexperiencemanager_admin9.png)

9. 管理画面に戻ります。 **[設定]**  >  **[Operations]\(操作\)**  >  **[Web Console]\(Web コンソール\)** を選択します。

    ![[シングル サインオンの構成] の [保存] ボタン](./media/adobeexperiencemanager-tutorial/tutorial_adobeexperiencemanager_admin1.png)

    [構成] ページが開きます。

    ![[シングル サインオンの構成] の [保存] ボタン](./media/adobeexperiencemanager-tutorial/tutorial_adobeexperiencemanager_admin2.png)

10. 「**Adobe Granite SAML 2.0 Authentication Handler**」で検索します。 次に、 **[追加]** アイコンを選択します。

    ![[シングル サインオンの構成] の [保存] ボタン](./media/adobeexperiencemanager-tutorial/tutorial_adobeexperiencemanager_admin3.png)

11. このページで、次の操作を実行します。

    ![[シングル サインオンの構成] の [保存] ボタン](./media/adobeexperiencemanager-tutorial/tutorial_adobeexperiencemanager_admin4.png)

    a. **[パス]** ボックスに、「 **/** 」と入力します。

    b. **[IDP URL]** ボックスに、Azure portal からコピーした**ログイン URL** の値を入力します。

    c. **[IDP Certificate Alias]\(IDP 証明書の別名\)** ボックスに、トラストストアに追加した**証明書の別名**の値を入力します。

    d. **[Security Provided Entity ID]\(セキュリティ指定されたエンティティ ID\)** ボックスに、Azure portal で構成した **[Azure AD 識別子]** の一意の値を入力します。

    e. **[Assertion Consumer Service URL]** ボックスに、Azure Portal で構成した **[応答 URL]** の値を入力します。

    f. **[キーストアのパスワード]** ボックスに、キーストアに設定した **[パスワード]** を入力します。

    g. **[User Attribute ID]\(ユーザー属性 ID\)** ボックスに、 **[名前 ID]** または関連するその他のユーザー ID を入力します。

    h. **[Autocreate CRX Users]\(CRX ユーザーの自動作成\)** を選択します。

    i. **[Logout URL]\(ログアウト URL\)** ボックスに、Azure portal から取得した一意の**ログアウト URL** の値を入力します。

    j. **[保存]** を選択します。

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

このセクションでは、Britta Simon に Adobe Experience Manager へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

1. Azure portal で **[エンタープライズ アプリケーション]** を選択し、 **[すべてのアプリケーション]** 、 **[Adobe Experience Manager]** の順に選択します。

    ![[エンタープライズ アプリケーション] ブレード](common/enterprise-applications.png)

2. アプリケーションの一覧で **[Adobe Experience Manager]** を選択します。

    ![アプリケーションの一覧の [Adobe Experience Manager] リンク](common/all-applications.png)

3. 左側のメニューで **[ユーザーとグループ]** を選びます。

    ![[ユーザーとグループ] リンク](common/users-groups-blade.png)

4. **[ユーザーの追加]** をクリックし、 **[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。

    ![[割り当ての追加] ウィンドウ](common/add-assign-user.png)

5. **[ユーザーとグループ]** ダイアログの [ユーザー] の一覧で **[Britta Simon]** を選択し、画面の下部にある **[選択]** ボタンをクリックします。

6. SAML アサーション内に任意のロール値が必要な場合、 **[ロールの選択]** ダイアログでユーザーに適したロールを一覧から選択し、画面の下部にある **[選択]** をクリッします。

7. **[割り当ての追加]** ダイアログで、 **[割り当て]** ボタンをクリックします。

### <a name="create-adobe-experience-manager-test-user"></a>Adobe Experience Manager のテスト ユーザーの作成

このセクションでは、Adobe Experience Manager で Britta Simon というユーザーを作成します。 **[Autocreate CRX Users]\(CRX ユーザーの自動作成\)** オプションを選択した場合、認証成功後にユーザーが自動的に作成されます。

ユーザーを手動で作成する場合は、[Adobe Experience Manager サポート チーム](https://helpx.adobe.com/support/experience-manager.html) に連絡して、Adobe Experience Manager プラットフォームにユーザーを追加してください。

### <a name="test-single-sign-on"></a>シングル サインオンのテスト 

このセクションでは、アクセス パネルを使用して Azure AD のシングル サインオン構成をテストします。

アクセス パネルで [Adobe Experience Manager] タイルをクリックすると、SSO を設定した Adobe Experience Manager に自動的にサインインします。 アクセス パネルの詳細については、[アクセス パネルの概要](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)に関する記事を参照してください。

## <a name="additional-resources"></a>その他のリソース

- [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Azure Active Directory の条件付きアクセスとは](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
