---
title: チュートリアル:Azure Active Directory と Soloinsight-CloudGate SSO の統合 | Microsoft Docs
description: Azure Active Directory と Soloinsight-CloudGate SSO の間でシングル サインオンを構成する方法について説明します。
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 9263c241-85a4-4724-afac-0351d6275958
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 02/07/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6e8b2b4d1a660fe2f1289bba6fa596d08ec824b8
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2019
ms.locfileid: "57847252"
---
# <a name="tutorial-azure-active-directory-integration-with-soloinsight-cloudgate-sso"></a>チュートリアル:Azure Active Directory と Soloinsight-CloudGate SSO の統合

このチュートリアルでは、Soloinsight-CloudGate SSO と Azure Active Directory (Azure AD) を統合する方法について説明します。
Soloinsight-CloudGate SSO と Azure AD の統合には、次の利点があります。

* Soloinsight-CloudGate SSO にアクセスできるユーザーを Azure AD で制御できます。
* ユーザーが自分の Azure AD アカウントを使用して Soloinsight-CloudGate SSO に自動的にサインイン (シングル サインオン) できるようにします。
* 1 つの中央サイト (Azure Portal) でアカウントを管理できます。

SaaS アプリと Azure AD の統合の詳細については、「 [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)」を参照してください。
Azure サブスクリプションをお持ちでない場合は、開始する前に[無料アカウントを作成](https://azure.microsoft.com/free/)してください。

## <a name="prerequisites"></a>前提条件

Soloinsight-CloudGate SSO と Azure AD の統合を構成するには、次のものが必要です。

* Azure AD サブスクリプション。 Azure AD の環境がない場合は、[こちら](https://azure.microsoft.com/pricing/free-trial/)から 1 か月の評価版を入手できます
* Soloinsight-CloudGate SSO でのシングル サインオンが有効なサブスクリプション

## <a name="scenario-description"></a>シナリオの説明

このチュートリアルでは、テスト環境で Azure AD のシングル サインオンを構成してテストします。

* Soloinsight-CloudGate SSO では、**SP** initiated SSO がサポートされます

## <a name="adding-soloinsight-cloudgate-sso-from-the-gallery"></a>ギャラリーからの Soloinsight-CloudGate SSO の追加

Soloinsight-CloudGate SSO の Azure AD への統合を構成するには、ギャラリーからマネージド SaaS アプリの一覧に Soloinsight-CloudGate SSO を追加する必要があります。

**ギャラリーから Soloinsight-CloudGate SSO を追加するには、次の手順を実行します。**

1. **[Azure Portal](https://portal.azure.com)** の左側のナビゲーション ウィンドウで、**[Azure Active Directory]** アイコンをクリックします。

    ![Azure Active Directory のボタン](common/select-azuread.png)

2. **[エンタープライズ アプリケーション]** に移動し、**[すべてのアプリケーション]** オプションを選択します。

    ![[エンタープライズ アプリケーション] ブレード](common/enterprise-applications.png)

3. 新しいアプリケーションを追加するには、ダイアログの上部にある **[新しいアプリケーション]** をクリックします。

    ![[新しいアプリケーション] ボタン](common/add-new-app.png)

4. 検索ボックスに「**Soloinsight-CloudGate SSO**」と入力し、結果パネルで **[Soloinsight-CloudGate SSO]** を選択してから、**[追加]** をクリックしてアプリケーションを追加します。

     ![結果一覧の Soloinsight-CloudGate SSO](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成とテスト

このセクションでは、**Britta Simon** というテスト ユーザーを基に、Soloinsight-CloudGate SSO で Azure AD のシングル サインオンを構成し、テストします。
シングル サインオンを機能させるには、Azure AD ユーザーと Soloinsight-CloudGate SSO 内の関連ユーザーの間にリンク関係が確立されている必要があります。

Soloinsight-CloudGate SSO で Azure AD のシングル サインオンを構成してテストするには、次の構成要素を完了する必要があります。

1. **[Azure AD シングル サインオンの構成](#configure-azure-ad-single-sign-on)** - ユーザーがこの機能を使用できるようにします。
2. **[Soloinsight-CloudGate SSO のシングル サインオンの構成](#configure-soloinsight-cloudgate-sso-single-sign-on)** - アプリケーション側でシングル サインオン設定を構成します。
3. **[Azure AD のテスト ユーザーの作成](#create-an-azure-ad-test-user)** - Britta Simon で Azure AD のシングル サインオンをテストします。
4. **[Azure AD テスト ユーザーの割り当て](#assign-the-azure-ad-test-user)** - Britta Simon が Azure AD シングル サインオンを使用できるようにします。
5. **[Soloinsight-CloudGate SSO のテスト ユーザーの作成](#create-soloinsight-cloudgate-sso-test-user)** - Soloinsight-CloudGate SSO で Britta Simon に対応するユーザーを作成し、Azure AD の Britta Simon にリンクさせます。
6. **[シングル サインオンのテスト](#test-single-sign-on)** - 構成が機能するかどうかを確認します。

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成

このセクションでは、Azure portal 上で Azure AD のシングル サインオンを有効にします。

Soloinsight-CloudGate SSO で Azure AD のシングル サインオンを構成するには、次の手順を実行します。

1. [Azure portal](https://portal.azure.com/) の **Soloinsight-CloudGate SSO** アプリケーション統合ページで **[シングル サインオン]** を選択します。

    ![シングル サインオン構成のリンク](common/select-sso.png)

2. **[シングル サインオン方式の選択]** ダイアログで、**[SAML/WS-Fed]** モードを選択して、シングル サインオンを有効にします。

    ![シングル サインオン選択モード](common/select-saml-option.png)

3. **[SAML でシングル サインオンをセットアップします]** ページで、**[編集]** アイコンをクリックして **[基本的な SAML 構成]** ダイアログを開きます。

    ![基本的な SAML 構成を編集する](common/edit-urls.png)

4. **[基本的な SAML 構成]** セクションで、次の手順を実行します。

    ![[Soloinsight-CloudGate SSO Domain and URLs]\(Soloinsight-CloudGate SSO のドメインと URL\) のシングル サインオン情報](common/sp-identifier.png)

    a. **[サインオン URL]** ボックスに、次のパターンを使用して URL を入力します。`https://<SUBDOMAIN>.sigateway.com/login`

    b. **[識別子 (エンティティ ID)]** ボックスに、次のパターンを使用して URL を入力します。`https://<SUBDOMAIN>.sigateway.com/process/sso`

    > [!NOTE]
    > これらは実際の値ではありません。 これらの値を実際のサインオン URL と識別子に更新します。これについては、このチュートリアルの「**Soloinsight-CloudGate SSO のシングル サインオンの構成**」セクションで後述します。

4. **[SAML でシングル サインオンをセットアップします]** ページの **[SAML 署名証明書]** セクションで、**[ダウンロード]** をクリックして要件のとおりに指定したオプションからの**証明書 (Base64)** をダウンロードして、お使いのコンピューターに保存します。

    ![証明書のダウンロードのリンク](common/certificatebase64.png)

6. **[Set up Soloinsight-CloudGate SSO]\(Soloinsight-CloudGate SSO のセットアップ\)** セクションで、要件に従って適切な URL をコピーします。

    ![構成 URL のコピー](common/copy-configuration-urls.png)

    a. ログイン URL

    b. Azure AD 識別子

    c. ログアウト URL

### <a name="configure-soloinsight-cloudgate-sso-single-sign-on"></a>Soloinsight-CloudGate SSO のシングル サインオンの構成

1. 基本的な SAML の構成中に Azure portal で貼り付ける値を取得するには、ご自分の資格情報を使用して CloudGate Web ポータルにログインし、SSO 設定にアクセスします。この設定には、**[Home]\(ホーム\) > [Administration]\(管理\) > [Administration]\(システム設定\) > [General]\(全般\)** でアクセスできます。

    ![CloudGate SSO 設定](./media/soloinsight-cloudgate-sso-tutorial/sso-main-settings.png)

2. **SAML コンシューマー URL**

    * **[Saml Consumer URL]\(SAML コンシューマー URL\)** と **[Redirect URL]\(リダイレクト URL\)** の各フィールドに対して使用可能なリンクをコピーし、Azure portal の **[基本的な SAML 構成]** セクションの **[識別子 (エンティティ ID)]** フィールドと **[応答 URL]** フィールドにそれぞれ貼り付けます。

        ![SAMLIdentifier](./media/soloinsight-cloudgate-sso-tutorial/saml-identifier.png)

3. **SAML 署名証明書**

    * Azure portal の SAML 署名証明書の一覧からダウンロードされた 証明書 (Base64) ファイルのソースに移動して、右クリックします。 一覧から、**[Edit with Notepad++]\(Notepad++ で編集\)** オプションを選択します。 

        ![SAMLcertificate](./media/soloinsight-cloudgate-sso-tutorial/certificate-file.png)

    * 証明書 (Base64) Notepad++ ファイルの内容をコピーします。

        ![証明書のコピー](./media/soloinsight-cloudgate-sso-tutorial/certificate-copy.png)

    * その内容を CloudGate Web ポータルの SSO 設定の **[Certificate]\(証明書\)** フィールドに貼り付け、保存ボタンをクリックします。

        ![証明書ポータル](./media/soloinsight-cloudgate-sso-tutorial/certificate-portal.png)

4. **既定のグループ**

    * CloudGate Web ポータルの **[Default Group]\(既定のグループ\)** オプションのドロップダウン リストから **[Business Admin]\(ビジネス管理\)** を選択します。

        ![既定のグループ](./media/soloinsight-cloudgate-sso-tutorial/default-group.png)

5. **AD 識別子とログイン URL**

    * Azure portal の **[Set up Soloinsight-CloudGate SSO]\(Soloinsight-CloudGate SSO のセットアップ\)** からコピーした**ログイン URL** を CloudGate Web ポータルの SSO 設定セクションに入力します。 

    * Azure portal の**ログイン URL** リンクを CloudGate Web ポータルの **[AD Login URL]\(AD ログイン URL\)** フィールドに貼り付けます。
     
    * Azure portal の**Azure AD 識別子**リンクを CloudGate Web ポータルの **[AD Identifier]\(AD 識別子\)** フィールドに貼り付けます。

        ![AD ログイン](./media/soloinsight-cloudgate-sso-tutorial/ad-login.png)

### <a name="create-an-azure-ad-test-user"></a>Azure AD のテスト ユーザーの作成 

このセクションの目的は、Azure Portal で Britta Simon というテスト ユーザーを作成することです。

1. Azure portal の左側のウィンドウで、**[Azure Active Directory]**、**[ユーザー]**、**[すべてのユーザー]** の順に選択します。

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

このセクションでは、Britta Simon に Soloinsight-CloudGate SSO へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

1. Azure portal で **[エンタープライズ アプリケーション]**、**[すべてのアプリケーション]**、**[Soloinsight-CloudGate SSO]** の順に選択します。

    ![[エンタープライズ アプリケーション] ブレード](common/enterprise-applications.png)

2. アプリケーションの一覧で **[Soloinsight-CloudGate SSO]** を選択します。

    ![アプリケーションの一覧の [Soloinsight-CloudGate SSO] リンク](common/all-applications.png)

3. 左側のメニューで **[ユーザーとグループ]** を選びます。

    ![[ユーザーとグループ] リンク](common/users-groups-blade.png)

4. **[ユーザーの追加]** をクリックし、**[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。

    ![[割り当ての追加] ウィンドウ](common/add-assign-user.png)

5. **[ユーザーとグループ]** ダイアログの [ユーザー] の一覧で **[Britta Simon]** を選択し、画面の下部にある **[選択]** ボタンをクリックします。

6. SAML アサーション内に任意のロール値が必要な場合、**[ロールの選択]** ダイアログでユーザーに適したロールを一覧から選択し、画面の下部にある **[選択]** をクリックします。

7. **[割り当ての追加]** ダイアログで、**[割り当て]** をクリックします。

### <a name="create-soloinsight-cloudgate-sso-test-user"></a>Soloinsight CloudGate SSO テスト ユーザーを作成する

テスト ユーザーを作成するには、CloudGate Web ポータルのメイン メニューで **[Employees]\(従業員\)** を選択し、[Add New employee]\(新しい従業員の追加\) フォームに入力します。 テスト ユーザーに割り当てられる権限のレベルは **[Business Admin]\(ビジネス管理\)** です。**[Create]\(作成\)** をクリックすると、必要なフィールドがすべて入力されます。

![テスト用の従業員](./media/soloinsight-cloudgate-sso-tutorial/employee-test.png)

### <a name="test-single-sign-on"></a>シングル サインオンのテスト 

このセクションでは、アクセス パネルを使用して Azure AD のシングル サインオン構成をテストします。

アクセス パネル上で [Soloinsight-CloudGate SSO] タイルをクリックすると、SSO を設定した Soloinsight-CloudGate SSO に自動的にサインインします。 アクセス パネルの詳細については、[アクセス パネルの概要](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)に関する記事を参照してください。

## <a name="additional-resources"></a>その他のリソース

- [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Azure Active Directory の条件付きアクセスとは](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

