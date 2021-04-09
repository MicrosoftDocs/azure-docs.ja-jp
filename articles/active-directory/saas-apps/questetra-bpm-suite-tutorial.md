---
title: 'チュートリアル: Azure Active Directory と Questetra BPM Suite の統合 | Microsoft Docs'
description: Azure Active Directory と Questetra BPM Suite の間でシングル サインオンを構成する方法について説明します。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/27/2019
ms.author: jeedes
ms.openlocfilehash: 13f05005529d3983e042398f5274fd7f981b8c8c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "92516596"
---
# <a name="tutorial-azure-active-directory-integration-with-questetra-bpm-suite"></a>チュートリアル: Azure Active Directory と Questetra BPM Suite の統合

このチュートリアルでは、Questetra BPM Suite と Azure Active Directory (Azure AD) を統合する方法について説明します。
Questetra BPM Suite と Azure AD の統合には、次の利点があります。

* Questetra BPM Suite にアクセスする Azure AD ユーザーを制御できます。
* ユーザーが自分の Azure AD アカウントを使用して自動的に Questetra BPM Suite にサインイン (シングル サインオン) するよう指定できます。
* 1 つの中央サイト (Azure Portal) でアカウントを管理できます。

SaaS アプリと Azure AD の統合の詳細については、「 [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)」を参照してください。
Azure サブスクリプションをお持ちでない場合は、開始する前に[無料アカウントを作成](https://azure.microsoft.com/free/)してください。

## <a name="prerequisites"></a>前提条件

Questetra BPM Suite と Azure AD の統合を構成するには、次のものが必要です。

* Azure AD サブスクリプション。 Azure AD の環境がない場合は、[こちら](https://azure.microsoft.com/pricing/free-trial/)から 1 か月の評価版を入手できます
* Questetra BPM Suite でのシングル サインオンが有効なサブスクリプション

## <a name="scenario-description"></a>シナリオの説明

このチュートリアルでは、テスト環境で Azure AD のシングル サインオンを構成してテストします。

* Questetra BPM Suite では、**SP** によって開始される SSO がサポートされます

## <a name="adding-questetra-bpm-suite-from-the-gallery"></a>ギャラリーからの Questetra BPM Suite の追加

Azure AD への Questetra BPM Suite の統合を構成するには、ギャラリーから管理対象 SaaS アプリの一覧に Questetra BPM Suite を追加する必要があります。

**ギャラリーから Questetra BPM Suite を追加するには、次の手順に従います。**

1. **[Azure Portal](https://portal.azure.com)** の左側のナビゲーション ウィンドウで、 **[Azure Active Directory]** アイコンをクリックします。

    ![Azure Active Directory のボタン](common/select-azuread.png)

2. **[エンタープライズ アプリケーション]** に移動し、 **[すべてのアプリケーション]** オプションを選択します。

    ![[エンタープライズ アプリケーション] ブレード](common/enterprise-applications.png)

3. 新しいアプリケーションを追加するには、ダイアログの上部にある **[新しいアプリケーション]** をクリックします。

    ![[新しいアプリケーション] ボタン](common/add-new-app.png)

4. 検索ボックスに「**Questetra BPM Suite**」と入力し、結果パネルで **[Questetra BPM Suite]** を選択し、 **[追加]** をクリックして、アプリケーションを追加します。

     ![結果の一覧の Questetra BPM Suite](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成とテスト

このセクションでは、**Britta Simon** というテスト ユーザーに基づいて、Questetra BPM Suite で Azure AD のシングル サインオンを構成し、テストします。
シングル サインオンを機能させるには、Azure AD ユーザーと Questetra BPM Suite 内の関連ユーザー間にリンク関係が確立されている必要があります。

Questetra BPM Suite で Azure AD のシングル サインオンを構成し、テストするには、次の要素を完了する必要があります。

1. **[Azure AD シングル サインオンの構成](#configure-azure-ad-single-sign-on)** - ユーザーがこの機能を使用できるようにします。
2. **[Questetra BPM Suite シングル サインオンの構成](#configure-questetra-bpm-suite-single-sign-on)** - アプリケーション側でシングル サインオン設定を構成します。
3. **[Azure AD のテスト ユーザーの作成](#create-an-azure-ad-test-user)** - Britta Simon で Azure AD のシングル サインオンをテストします。
4. **[Azure AD テスト ユーザーの割り当て](#assign-the-azure-ad-test-user)** - Britta Simon が Azure AD シングル サインオンを使用できるようにします。
5. **[Questetra BPM Suite のテスト ユーザーの作成](#create-questetra-bpm-suite-test-user)** - Questetra BPM Suite で Britta Simon に対応するユーザーを作成し、Azure AD の Britta Simon にリンクさせます。
6. **[シングル サインオンのテスト](#test-single-sign-on)** - 構成が機能するかどうかを確認します。

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成

このセクションでは、Azure portal 上で Azure AD のシングル サインオンを有効にします。

Questetra BPM Suite で Azure AD シングル サインオンを構成するには、次の手順に従います。

1. [Azure portal](https://portal.azure.com/) の **Questetra BPM Suite** アプリケーション統合ページで、 **[シングル サインオン]** を選択します。

    ![シングル サインオン構成のリンク](common/select-sso.png)

2. **[シングル サインオン方式の選択]** ダイアログで、 **[SAML/WS-Fed]** モードを選択して、シングル サインオンを有効にします。

    ![シングル サインオン選択モード](common/select-saml-option.png)

3. **[SAML でシングル サインオンをセットアップします]** ページで、 **[編集]** アイコンをクリックして **[基本的な SAML 構成]** ダイアログを開きます。

    ![基本的な SAML 構成を編集する](common/edit-urls.png)

4. **[基本的な SAML 構成]** セクションで、次の手順を実行します。

    ![[Questetra BPM Suite ドメインと URL] のシングル サインオン情報](common/sp-identifier.png)

    a. **[サインオン URL]** ボックスに、次のパターンを使用して URL を入力します。`https://<subdomain>.questetra.net/saml/SSO/alias/bpm`

    b. **[識別子 (エンティティ ID)]** ボックスに、次のパターンを使用して URL を入力します。`https://<subdomain>.questetra.net/`

    > [!NOTE]
    > これらは実際の値ではありません。 実際のサインオン URL と識別子でこれらの値を更新してください。 これらの値は、**Questetra BPM Suite** 企業サイトの **[SP Information]\(SP 情報\)** セクションから取得できます。これについては、チュートリアルの後半で説明します。または、[Questetra BPM Suite クライアント サポート チーム](https://www.questetra.com/contact/)にお問い合わせください。 Azure portal の **[基本的な SAML 構成]** セクションに示されているパターンを参照することもできます。

5. **[SAML でシングル サインオンをセットアップします]** ページの **[SAML 署名証明書]** セクションで、 **[ダウンロード]** をクリックして要件のとおりに指定したオプションからの **証明書 (Base64)** をダウンロードして、お使いのコンピューターに保存します。

    ![証明書のダウンロードのリンク](common/certificatebase64.png)

6. **[Questetra BPM Suite のセットアップ]** セクションで、要件のとおりに適切な URL をコピーします。

    ![構成 URL のコピー](common/copy-configuration-urls.png)

    a. ログイン URL

    b. Azure AD 識別子

    c. ログアウト URL

### <a name="configure-questetra-bpm-suite-single-sign-on"></a>Questetra BPM Suite のシングル サインオンを構成する

1. 別の Web ブラウザーのウィンドウで、管理者として **Questetra BPM Suite** 企業サイトにサインインします。

2. 上部のメニューで **[システム設定]** をクリックします。 
   
    ![Questetra BPM Suite 企業サイトから [システム設定] が選択された画面のスクリーンショット。][10]

3. **[シングルサインオン (SAML)]** ページを開くには、 **[SSO (SAML)]** をクリックします。 
   
    ![[S S O (SAML)] が選択された画面のスクリーンショット。][11]

4. **Questetra BPM Suite** 企業サイトの **[SP Information]\(SP 情報\)** セクションで、次の手順に従います。

    a. **[ACS URL]** をコピーし、Azure portal の **[基本的な SAML 構成]** セクションにある **[サインオン URL]** ボックスに貼り付けます。
    
    b. **[Entity ID]\(エンティティ ID\)** をコピーし、Azure portal の **[基本的な SAML 構成]** セクションにある **[識別子]** ボックスに貼り付けます。

5. **Questetra BPM Suite** 企業サイトで、次の手順に従います。 
   
    ![Configure single sign-on][15]
   
    a. **[シングル サインオンを有効にする]** を選択します。
   
    b. **[Entity ID]\(エンティティ ID\)** ボックスに、Azure portal からコピーした **Azure AD ID** の値を貼り付けます。
    
    c. **[Sign-in page URL]\(サインイン ページの URL\)** ボックスに、Azure portal からコピーした **ログイン URL** の値を貼り付けます。
    
    d. **[Sign-out page URL]\(サインアウト ページの URL\)** ボックスに、Azure portal からコピーした **ログアウト URL** の値を貼り付けます。
    
    e. **[NameID format]\(NameID の形式\)** ボックスに、「`urn:oasis:names:tc:SAML:1.1:nameid-format:emailAddress`」と入力します。

    f. Azure Portal からダウンロードした **Base-64** でエンコードされた証明書をメモ帳で開き、その内容をクリップボードにコピーしてから、それを **[Validation certificate]\(検証証明書\)** ボックスに貼り付けます。 

    g. **[保存]** をクリックします。

### <a name="create-an-azure-ad-test-user"></a>Azure AD のテスト ユーザーの作成 

このセクションの目的は、Azure Portal で Britta Simon というテスト ユーザーを作成することです。

1. Azure portal の左側のウィンドウで、 **[Azure Active Directory]** 、 **[ユーザー]** 、 **[すべてのユーザー]** の順に選択します。

    ![[ユーザーとグループ] と [すべてのユーザー] リンク](common/users.png)

2. 画面の上部にある **[新しいユーザー]** を選択します。

    ![[新しいユーザー] ボタン](common/new-user.png)

3. [ユーザーのプロパティ] で、次の手順を実行します。

    ![[ユーザー] ダイアログ ボックス](common/user-properties.png)

    a. **[名前]** フィールドに「**BrittaSimon**」と入力します。
  
    b. **[ユーザー名]** フィールドに「brittasimon@yourcompanydomain.extension」と入力します。 たとえば、BrittaSimon@contoso.com のように指定します。

    c. **[パスワードを表示]** チェック ボックスをオンにし、[パスワード] ボックスに表示された値を書き留めます。

    d. **Create** をクリックしてください。

### <a name="assign-the-azure-ad-test-user"></a>Azure AD テスト ユーザーの割り当て

このセクションでは、Britta Simon に Questetra BPM Suite へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

1. Azure portal 上で **[エンタープライズ アプリケーション]** を選択し、 **[すべてのアプリケーション]** を選択してから、 **[Questetra BPM Suite]** を選択します。

    ![[エンタープライズ アプリケーション] ブレード](common/enterprise-applications.png)

2. アプリケーションの一覧で **[Questetra BPM Suite]** を選択します。

    ![アプリケーションの一覧の Questetra BPM Suite のリンク](common/all-applications.png)

3. 左側のメニューで **[ユーザーとグループ]** を選びます。

    ![[ユーザーとグループ] リンク](common/users-groups-blade.png)

4. **[ユーザーの追加]** をクリックし、 **[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。

    ![[割り当ての追加] ウィンドウ](common/add-assign-user.png)

5. **[ユーザーとグループ]** ダイアログの [ユーザー] の一覧で **[Britta Simon]** を選択し、画面の下部にある **[選択]** ボタンをクリックします。

6. SAML アサーション内に任意のロール値が必要な場合、 **[ロールの選択]** ダイアログでユーザーに適したロールを一覧から選択し、画面の下部にある **[選択]** をクリッします。

7. **[割り当ての追加]** ダイアログで、 **[割り当て]** ボタンをクリックします。

### <a name="create-questetra-bpm-suite-test-user"></a>Questetra BPM Suite テスト ユーザーの作成

このセクションの目的は、Questetra BPM Suite で Britta Simon というユーザーを作成することです。

**Questetra BPM Suite で Britta Simon というユーザーを作成するには、次の手順に従います。**

1. Questetra BPM Suite 企業サイトに管理者としてサインインします。

2. **[システム設定]、[ユーザー一覧]、[新規ユーザー]** の順に移動します。
 
3. [新規ユーザー] ダイアログで、次の手順を実行します。 
   
    ![テスト ユーザーの作成][300] 
   
    a. **[Name]\(名前\)** ボックスに、ユーザーの **名前** を「britta.simon@contoso.com」と入力します。
   
    b. **[Email]\(電子メール\)** ボックスに、ユーザーの **電子メール** を「britta.simon@contoso.com」と入力します。
   
    c. **[Password]\(パスワード\)** ボックスに、そのユーザーの **パスワード** を入力します。
    
    d. **[新しいユーザーの追加]** をクリックします。

### <a name="test-single-sign-on"></a>シングル サインオンのテスト 

このセクションでは、アクセス パネルを使用して Azure AD のシングル サインオン構成をテストします。

アクセス パネル上で [Questetra BPM Suite] タイルをクリックすると、SSO を設定した Questetra BPM Suite に自動的にサインインします。 アクセス パネルの詳細については、[アクセス パネルの概要](../user-help/my-apps-portal-end-user-access.md)に関する記事を参照してください。

## <a name="additional-resources"></a>その他のリソース

- [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](./tutorial-list.md)

- [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)

- [Azure Active Directory の条件付きアクセスとは](../conditional-access/overview.md)

<!--Image references-->

[10]: ./media/questetra-bpm-suite-tutorial/questera_bpm_suite_03.png
[11]: ./media/questetra-bpm-suite-tutorial/questera_bpm_suite_04.png
[15]: ./media/questetra-bpm-suite-tutorial/questera_bpm_suite_08.png
[300]: ./media/questetra-bpm-suite-tutorial/questera_bpm_suite_11.png