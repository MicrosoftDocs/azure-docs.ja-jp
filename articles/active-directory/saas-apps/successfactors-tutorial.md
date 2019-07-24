---
title: チュートリアル:Azure Active Directory と SuccessFactors の統合 | Microsoft Docs
description: Azure Active Directory と SuccessFactors の間でシングル サインオンを構成する方法について説明します。
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: 32bd8898-c2d2-4aa7-8c46-f1f5c2aa05f1
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 1/3/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2281fb046ca9b96aa5800150d630b9086236c5c5
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2019
ms.locfileid: "57846924"
---
# <a name="tutorial-azure-active-directory-integration-with-successfactors"></a>チュートリアル:Azure Active Directory と SuccessFactors の統合

このチュートリアルでは、SuccessFactors と Azure Active Directory (Azure AD) を統合する方法について説明します。
SuccessFactors と Azure AD の統合には、次の利点があります。

* SuccessFactors にアクセスする Azure AD ユーザーを制御できます。
* ユーザーが自分の Azure AD アカウントを使用して SuccessFactors に自動的にサインイン (シングル サインオン) できるようにすることができます。
* 1 つの中央サイト (Azure Portal) でアカウントを管理できます。

SaaS アプリと Azure AD の統合の詳細については、「 [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)」を参照してください。
Azure サブスクリプションをお持ちでない場合は、開始する前に[無料アカウントを作成](https://azure.microsoft.com/free/)してください。

## <a name="prerequisites"></a>前提条件

SuccessFactors と Azure AD の統合を構成するには、次のものが必要です。

* Azure AD サブスクリプション。 Azure AD の環境がない場合は、[こちら](https://azure.microsoft.com/pricing/free-trial/)から 1 か月の評価版を入手できます
* SuccessFactors でのシングル サインオンが有効なサブスクリプション

## <a name="scenario-description"></a>シナリオの説明

このチュートリアルでは、テスト環境で Azure AD のシングル サインオンを構成してテストします。

* SuccessFactors では、**SP** によって開始される SSO がサポートされます

## <a name="adding-successfactors-from-the-gallery"></a>ギャラリーからの SuccessFactors の追加

Azure AD への SuccessFactors の統合を構成するには、ギャラリーから管理対象 SaaS アプリの一覧に SuccessFactors を追加する必要があります。

**ギャラリーから SuccessFactors を追加するには、次の手順に従います。**

1. **[Azure Portal](https://portal.azure.com)** の左側のナビゲーション ウィンドウで、**[Azure Active Directory]** アイコンをクリックします。

    ![Azure Active Directory のボタン](common/select-azuread.png)

2. **[エンタープライズ アプリケーション]** に移動し、**[すべてのアプリケーション]** オプションを選択します。

    ![[エンタープライズ アプリケーション] ブレード](common/enterprise-applications.png)

3. 新しいアプリケーションを追加するには、ダイアログの上部にある **[新しいアプリケーション]** をクリックします。

    ![[新しいアプリケーション] ボタン](common/add-new-app.png)

4. 検索ボックスに「**SuccessFactors**」と入力し、結果ウィンドウで **[SuccessFactors]** を選び、**[追加]** をクリックして、アプリケーションを追加します。

     ![結果一覧の SuccessFactors](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成とテスト

このセクションでは、**Britta Simon** というテスト ユーザーに基づいて、SuccessFactors で Azure AD のシングル サインオンを構成し、テストします。
シングル サインオンを機能させるには、Azure AD ユーザーと SuccessFactors 内の関連ユーザーとの間にリンク関係が確立されている必要があります。

SuccessFactors で Azure AD のシングル サインオンを構成してテストするには、次の構成要素を完了する必要があります。

1. **[Azure AD シングル サインオンの構成](#configure-azure-ad-single-sign-on)** - ユーザーがこの機能を使用できるようにします。
2. **[SuccessFactors シングル サインオンの構成](#configure-successfactors-single-sign-on)** - アプリケーション側でシングル サインオン設定を構成します。
3. **[Azure AD のテスト ユーザーの作成](#create-an-azure-ad-test-user)** - Britta Simon で Azure AD のシングル サインオンをテストします。
4. **[Azure AD テスト ユーザーの割り当て](#assign-the-azure-ad-test-user)** - Britta Simon が Azure AD シングル サインオンを使用できるようにします。
5. **[SuccessFactors のテスト ユーザーの作成](#create-successfactors-test-user)** - SuccessFactors で Britta Simon に対応するユーザーを作成し、Azure AD の Britta Simon にリンクさせます。
6. **[シングル サインオンのテスト](#test-single-sign-on)** - 構成が機能するかどうかを確認します。

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成

このセクションでは、Azure portal 上で Azure AD のシングル サインオンを有効にします。

SuccessFactors で Azure AD シングル サインオンを構成するには、次の手順に従います。

1. [Azure portal](https://portal.azure.com/) の **SuccessFactors** アプリケーション統合ページで、**[シングル サインオン]** をクリックします。

    ![シングル サインオン構成のリンク](common/select-sso.png)

2. **[シングル サインオン方式の選択]** ダイアログで、**[SAML/WS-Fed]** モードを選択して、シングル サインオンを有効にします。

    ![シングル サインオン選択モード](common/select-saml-option.png)

3. **[SAML でシングル サインオンをセットアップします]** ページで、**[編集]** アイコンをクリックして **[基本的な SAML 構成]** ダイアログを開きます。

    ![基本的な SAML 構成を編集する](common/edit-urls.png)

4. **[基本的な SAML 構成]** セクションで、次の手順を実行します。

    ![[SuccessFactors のドメインと URL] のシングル サインオン情報](common/sp-identifier-reply.png)

    a. **[サインオン URL]** ボックスに、次の形式で URL を入力します。

    | |
    |--|
    | `https://<companyname>.successfactors.com/<companyname>`|
    | `https://<companyname>.sapsf.com/<companyname>`|
    | `https://<companyname>.successfactors.eu/<companyname>`|
    | `https://<companyname>.sapsf.eu`|

    b. **[識別子]** ボックスに、次のパターンを使用して URL を入力します。

    | |
    |--|
    | `https://www.successfactors.com/<companyname>`|
    | `https://www.successfactors.com`|
    | `https://<companyname>.successfactors.eu`|
    | `https://www.successfactors.eu/<companyname>`|
    | `https://<companyname>.sapsf.com`|
    | `https://hcm4preview.sapsf.com/<companyname>`|
    | `https://<companyname>.sapsf.eu`|
    | `https://www.successfactors.cn`|
    | `https://www.successfactors.cn/<companyname>`|

    c. **[応答 URL]** ボックスに、次のパターンを使用して URL を入力します。

    | |
    |--|
    | `https://<companyname>.successfactors.com/<companyname>`|
    | `https://<companyname>.successfactors.com`|
    | `https://<companyname>.sapsf.com/<companyname>`|
    | `https://<companyname>.sapsf.com`|
    | `https://<companyname>.successfactors.eu/<companyname>`|
    | `https://<companyname>.successfactors.eu`|
    | `https://<companyname>.sapsf.eu`|
    | `https://<companyname>.sapsf.eu/<companyname>`|
    | `https://<companyname>.sapsf.cn`|
    | `https://<companyname>.sapsf.cn/<companyname>`|

    > [!NOTE]
    > これらは実際の値ではありません。 実際のサインオン URL、識別子、および応答 URL で値を更新します。 これらの値を取得するには、[SuccessFactors クライアント サポート チーム](https://www.successfactors.com/content/ssf-site/en/support.html)に問い合わせてください。

5. **[SAML でシングル サインオンをセットアップします]** ページの **[SAML 署名証明書]** セクションで、**[ダウンロード]** をクリックして要件のとおりに指定したオプションからの**証明書 (Base64)** をダウンロードして、お使いのコンピューターに保存します。

    ![証明書のダウンロードのリンク](common/certificatebase64.png)

6. **[SuccessFactors のセットアップ]** セクションで、要件どおりの適切な URL をコピーします。

    ![構成 URL のコピー](common/copy-configuration-urls.png)

    a. ログイン URL

    b. Azure AD 識別子

    c. ログアウト URL

### <a name="configure-successfactors-single-sign-on"></a>SuccessFactors シングル サインオンの構成

1. 別の Web ブラウザー ウィンドウで、**SuccessFactors 管理者ポータル**に管理者としてログインします。

2. **[アプリケーション セキュリティ]** で **[Single Sign On Feature (シングル サインオン機能)]** に移動します。

3. **[Reset Token (リセット トークン)]** に任意の値を入力し、**[Save Token (トークンの保存)]** をクリックして、SAML SSO を有効にします。

    ![Configuring single sign-on on app side][11]

    > [!NOTE]
    > この値は、オン/オフのスイッチとして使用されます。 任意の値を保存すると、SAML SSO はオンになります。 値が空白のまま保存すると、SAML SSO はオフになります。

4. 次のスクリーンショットの画面に移動して、次の操作を実行します。

    ![Configuring single sign-on on app side][12]
  
    a. **[SAML v2 SSO]** オプションをクリックします。
  
    b. **[SAML Asserting Party Name]\(SAML アサーティング パーティ名\)** を設定します (例: SAML 発行者 + 会社名)。

    c. **[発行者の URL]** テキストボックスに、Azure portal からコピーした、**Azure AD ID** の値を貼り付けます。

    d. **[Require Mandatory Signature]\(必須の署名が必要\)** として **[アサーション]** を選択します。

    e. **[Enable SAML Flag (SAML フラグを有効にする)]** で **[Enabled (有効にする)]** を選択します。

    f. **[Login Request Signature(SF Generated/SP/RP) (ログイン要求署名 (SF 生成/SP/RP))]** で **[No (いいえ)]** を選択します。

    g. **[SAML Profile (SAML プロファイル)]** で **[Browser/Post Profile (Browser/Post プロファイル)]** を選択します。

    h. **[Enforce Certificate Valid Period (証明書の有効期間を適用する)]** で **[No (いいえ)]** を選択します。

    i. Azure Portal からダウンロードした証明書ファイルのコンテンツをコピーし、**[SAML Verifying Certificate]\(SAML で確認する証明書\)** ボックスに貼り付けます。

    > [!NOTE] 
    > この証明書には、証明書の開始タグおよび終了タグが必要です。

5. [SAML V2] に移動して、次の手順に従います。

    ![Configuring single sign-on on app side][13]

    a. **[Support SP-initiated Global Logout (SP によって開始されたグローバル ログアウトのサポート)]** で **[Yes (はい)]** を選択します。

    b. **[Global Logout Service URL (LogoutRequest destination)]\(グローバル ログアウト サービスの URL (LogoutRequest の送信先)\)** ボックスに、Azure Portal からコピーした**サインアウト URL** の値を貼り付けます。

    c. **[Require sp must encrypt all NameID element (すべての NameID 要素で SP での暗号化を要求)]** で **[No (いいえ)]** を選択します。

    d. **[NameID Format (NameID の形式)]** で **[unspecified (未指定)]** を選択します。

    e. **[Enable sp initiated login (AuthnRequest) (SP によって開始されたログインを有効にする (AuthnRequest))]** で **[Yes (はい)]** を選択します。

    f. Azure portal からコピーした **[ログイン URL]** の値を、**[Send request as Company-Wide issuer]\(会社全体の発行者として要求を送信する\)** ボックスに貼り付けます。

6. 大文字と小文字を区別しないログイン ユーザー名を作成する場合、次の手順を実行します。

    ![Configure single sign-on][29]

    a. 下部の **[Company Settings (会社設定)]** に移動します。

    b. **[Enable Non-Case-Sensitive Username]\(大文字と小文字を区別しないユーザー名\)** の横のチェック ボックスをオンにします。

    c. **[Save]** をクリックします。

    > [!NOTE]
    > これを有効にしようとすると、重複する SAML ログイン名が作成されるかどうかがシステムによって確認されます。 たとえば、顧客が User1 および user1 というユーザー名を持っている場合などです。 大文字と小文字の区別をしないと、これらの重複が発生します。 システムによってエラー メッセージが表示され、機能は有効になりません。 顧客はユーザー名の一方を、違うスペルになるように変更する必要があります。

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

このセクションでは、Britta Simon に SuccessFactors へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

1. Azure portal 上で **[エンタープライズ アプリケーション]** を選択し、**[すべてのアプリケーション]** を選択してから、**[SuccessFactors]** を選択します。

    ![[エンタープライズ アプリケーション] ブレード](common/enterprise-applications.png)

2. アプリケーションの一覧で **[SuccessFactors]** を選択します。

    ![アプリケーションの一覧の SuccessFactors のリンク](common/all-applications.png)

3. 左側のメニューで **[ユーザーとグループ]** を選びます。

    ![[ユーザーとグループ] リンク](common/users-groups-blade.png)

4. **[ユーザーの追加]** をクリックし、**[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。

    ![[割り当ての追加] ウィンドウ](common/add-assign-user.png)

5. **[ユーザーとグループ]** ダイアログの [ユーザー] の一覧で **[Britta Simon]** を選択し、画面の下部にある **[選択]** ボタンをクリックします。

6. SAML アサーション内に任意のロール値が必要な場合、**[ロールの選択]** ダイアログでユーザーに適したロールを一覧から選択し、画面の下部にある **[選択]** をクリッします。

7. **[割り当ての追加]** ダイアログで、**[割り当て]** ボタンをクリックします。

### <a name="create-successfactors-test-user"></a>SuccessFactors のテスト ユーザーの作成

Azure AD ユーザーが SuccessFactors にログインできるようにするには、ユーザーを SuccessFactors にプロビジョニングする必要があります。 SuccessFactors の場合、プロビジョニングは手動で行います。

SuccessFactors でユーザーを作成するには、 [SuccessFactors のサポート チーム](https://www.successfactors.com/content/ssf-site/en/support.html)に連絡する必要があります。

### <a name="test-single-sign-on"></a>シングル サインオンのテスト

このセクションでは、アクセス パネルを使用して Azure AD のシングル サインオン構成をテストします。

アクセス パネル上で [SuccessFactors] タイルをクリックすると、SSO を設定した SuccessFactors に自動的にサインインします。 アクセス パネルの詳細については、[アクセス パネルの概要](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)に関する記事を参照してください。

## <a name="additional-resources"></a>その他のリソース

- [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Azure Active Directory の条件付きアクセスとは](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

<!--Image references-->

[11]: ./media/successfactors-tutorial/tutorial_successfactors_07.png
[12]: ./media/successfactors-tutorial/tutorial_successfactors_08.png
[13]: ./media/successfactors-tutorial/tutorial_successfactors_09.png
[29]: ./media/successfactors-tutorial/tutorial_successfactors_10.png
