---
title: チュートリアル:Azure Active Directory と Adobe Sign の統合 | Microsoft Docs
description: Azure Active Directory と Adobe Sign の間でシングル サインオンを構成する方法について説明します。
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: f9385723-8fe7-4340-8afb-1508dac3e92b
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 12/19/2018
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8942ebf3f006c2e1cc72b322dd243d46bf69f04d
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2019
ms.locfileid: "57888129"
---
# <a name="tutorial-azure-active-directory-integration-with-adobe-sign"></a>チュートリアル:Azure Active Directory と Adobe Sign の統合

このチュートリアルでは、Adobe Sign と Azure Active Directory (Azure AD) を統合する方法について説明します。
Adobe Sign と Azure AD の統合には、次の利点があります。

* Adobe Sign にアクセスする Azure AD ユーザーを制御できます。
* ユーザーが自分の Azure AD アカウントを使用して Adobe Sign に自動的にサインイン (シングル サインオン) するように設定できます。
* 1 つの中央サイト (Azure Portal) でアカウントを管理できます。

SaaS アプリと Azure AD の統合の詳細については、「 [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)」を参照してください。
Azure サブスクリプションをお持ちでない場合は、開始する前に[無料アカウントを作成](https://azure.microsoft.com/free/)してください。

## <a name="prerequisites"></a>前提条件

Adobe Sign と Azure AD の統合を構成するには、次のものが必要です。

* Azure AD サブスクリプション。 Azure AD の環境がない場合は、[こちら](https://azure.microsoft.com/pricing/free-trial/)から 1 か月の評価版を入手できます
* Adobe Sign でのシングル サインオンが有効なサブスクリプション

## <a name="scenario-description"></a>シナリオの説明

このチュートリアルでは、テスト環境で Azure AD のシングル サインオンを構成してテストします。

* Adobe Sign では、**SP** によって開始される SSO がサポートされます

## <a name="adding-adobe-sign-from-the-gallery"></a>ギャラリーからの Adobe Sign の追加

Azure AD への Adobe Sign の統合を構成するには、ギャラリーから管理対象 SaaS アプリの一覧に Adobe Sign を追加する必要があります。

**ギャラリーから Adobe Sign を追加するには、次の手順を実行します。**

1. **[Azure Portal](https://portal.azure.com)** の左側のナビゲーション ウィンドウで、**[Azure Active Directory]** アイコンをクリックします。

    ![Azure Active Directory のボタン](common/select-azuread.png)

2. **[エンタープライズ アプリケーション]** に移動し、**[すべてのアプリケーション]** オプションを選択します。

    ![[エンタープライズ アプリケーション] ブレード](common/enterprise-applications.png)

3. 新しいアプリケーションを追加するには、ダイアログの上部にある **[新しいアプリケーション]** をクリックします。

    ![[新しいアプリケーション] ボタン](common/add-new-app.png)

4. 検索ボックスに「**Adobe Sign**」と入力し、結果パネルで **Adobe Sign** を選び、**[追加]** をクリックして、アプリケーションを追加します。

     ![結果一覧の Adobe Sign](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成とテスト

このセクションでは、**Britta Simon** というテスト ユーザーに基づいて、Adobe Sign で Azure AD のシングル サインオンを構成し、テストします。
シングル サインオンを機能させるには、Azure AD ユーザーと Adobe Sign 内の関連ユーザー間にリンク関係が確立されている必要があります。

Adobe Sign で Azure AD のシングル サインオンを構成してテストするには、次の構成要素を完了する必要があります。

1. **[Azure AD シングル サインオンの構成](#configure-azure-ad-single-sign-on)** - ユーザーがこの機能を使用できるようにします。
2. **[Adobe Sign シングル サインオンの構成](#configure-adobe-sign-single-sign-on)** - アプリケーション側でシングル サインオン設定を構成します。
3. **[Azure AD のテスト ユーザーの作成](#create-an-azure-ad-test-user)** - Britta Simon で Azure AD のシングル サインオンをテストします。
4. **[Azure AD テスト ユーザーの割り当て](#assign-the-azure-ad-test-user)** - Britta Simon が Azure AD シングル サインオンを使用できるようにします。
5. **[Adobe Sign のテスト ユーザーの作成](#create-adobe-sign-test-user)** - Adobe Sign で Britta Simon に対応するユーザーを作成し、Azure AD の Britta Simon にリンクさせます。
6. **[シングル サインオンのテスト](#test-single-sign-on)** - 構成が機能するかどうかを確認します。

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成

このセクションでは、Azure portal 上で Azure AD のシングル サインオンを有効にします。

Adobe Sign で Azure AD シングル サインオンを構成するには、次の手順に従います。

1. [Azure portal](https://portal.azure.com/) の **Adobe Sign** アプリケーション統合ページで、**[シングル サインオン]** を選択します。

    ![シングル サインオン構成のリンク](common/select-sso.png)

2. **[シングル サインオン方式の選択]** ダイアログで、**[SAML/WS-Fed]** モードを選択して、シングル サインオンを有効にします。

    ![シングル サインオン選択モード](common/select-saml-option.png)

3. **[SAML でシングル サインオンをセットアップします]** ページで、**[編集]** アイコンをクリックして **[基本的な SAML 構成]** ダイアログを開きます。

    ![基本的な SAML 構成を編集する](common/edit-urls.png)

4. **[基本的な SAML 構成]** セクションで、次の手順を実行します。

    ![Adobe Sign のドメインと URL のシングル サインオン情報](common/sp-identifier.png)

    a. **[サインオン URL]** ボックスに、次のパターンを使用して URL を入力します。`https://<companyname>.echosign.com/`

    b. **[識別子 (エンティティ ID)]** ボックスに、次のパターンを使用して URL を入力します。`https://<companyname>.echosign.com`

    > [!NOTE]
    > これらは実際の値ではありません。 実際のサインオン URL と識別子でこれらの値を更新します。 これらの値を取得するには、[Adobe Sign クライアント サポート チーム](https://helpx.adobe.com/in/contact/support.html)に問い合わせください。 Azure portal の **[基本的な SAML 構成]** セクションに示されているパターンを参照することもできます。

4. **[SAML でシングル サインオンをセットアップします]** ページの **[SAML 署名証明書]** セクションで、**[ダウンロード]** をクリックして要件のとおりに指定したオプションからの**証明書 (Base64)** をダウンロードして、お使いのコンピューターに保存します。

    ![証明書のダウンロードのリンク](common/certificatebase64.png)

6. **[Adobe Sign の設定]** セクションで、要件どおりの適切な URL をコピーします。

    ![構成 URL のコピー](common/copy-configuration-urls.png)

    a. ログイン URL

    b. Azure AD 識別子

    c. ログアウト URL

### <a name="configure-adobe-sign-single-sign-on"></a>Adobe Sign シングル サインオンの構成

1. Adobe Sign で自分のドメインをホワイトリストに登録するには、構成する前に、[Adobe Sign クライアント サポート チーム](https://helpx.adobe.com/in/contact/support.html)に連絡してください。 ドメインの追加方法は次のとおりです。

    a. [Adobe Sign クライアント サポート チーム](https://helpx.adobe.com/in/contact/support.html)からランダムに生成されたトークンが送信されます。 ドメインの場合、トークンは **adobe-sign-verification= xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx** のようになります。

    b. DNS テキスト レコードで検証トークンを発行し、[Adobe Sign クライアント サポート チーム](https://helpx.adobe.com/in/contact/support.html)に通知します。
    
    > [!NOTE]
    > これには数日以上かかる場合があります。 DNS への反映の遅延は、DNS で公開された値が 1 時間以上表示されない可能性があることを意味することに注意してください。 DNS テキスト レコードでこのトークンを発行する方法については、通常、組織の IT 管理者が熟知しています。
    
    c. トークンが発行された後に、サポート チケットを通じて [Adobe Sign クライアント サポート チーム](https://helpx.adobe.com/in/contact/support.html)に通知すると、サポート チームがドメインを検証し、それをユーザーのアカウントに追加します。
    
    d. 通常、DNS レコードでトークンを発行する手順は次のとおりです。

    * ドメイン アカウントにサインインする
    * DNS レコードを更新するためのページを検索する。 このページは、DNS 管理、ネーム サーバー管理、または詳細設定と呼ばれる場合があります。
    * 自分のドメインの TXT レコードを検索する。
    * Adobe から提供された完全なトークン値を使用して TXT レコードを追加する。
    * 変更を保存します。

1. 別の Web ブラウザーのウィンドウで、管理者として Adobe Sign 企業サイトにサインインします。

1. [SAML] メニューで、**[アカウント設定]** > **[SAML 設定]** を選択します。
   
    ![Adobe Sign の [SAML 設定] ページ](./media/adobe-echosign-tutorial/ic789520.png "[アカウント]") のスクリーンショット

1. **[SAML 設定]** セクションで、次の手順に従います。
  
   ![[SAML 設定]](./media/adobe-echosign-tutorial/ic789521.png "[SAML 設定]") のスクリーンショット
   
   ![[SAML 設定]](./media/adobe-echosign-tutorial/ic789522.png "[SAML 設定]") のスクリーンショット

   a. **[SAML モード]** で **[SAML Mandatory]** を選択します。
   
   b. **[Echosign 資格情報を使用して、Echosign アカウント管理者のログインを許可する]** を選択します。
   
   c. **[ユーザーの作成]** で、**[SAML を使用して認証されたユーザーを自動的に追加]** を選択します。

   d. **[Idp Entity ID]\(IDP エンティティ ID\)** ボックスに、Azure portal からコピーした **Azure AD 識別子**を貼り付けます。
    
   e. **[Idp Login URL]\(IDP ログイン URL\)** ボックスに、Azure portal からコピーした**ログイン URL** を貼り付けます。
   
   f. **[Idp Logout URL]\(IDP ログアウト URL\)** ボックスに、Azure portal からコピーした**ログアウト URL** を貼り付けます。

   g. ダウンロードした**証明書 (Base64)** ファイルをメモ帳で開きます。 その内容をクリップボードにコピーし、**[IdP Certificate]\(IdP 証明書\)** ボックスに貼り付けます。

   h. **[変更の保存]** を選択します。

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

このセクションでは、Adobe Sign へのアクセスを許可することで、Britta Simon が Azure シングル サインオンを使用できるようにします。

1. Azure portal 上で **[エンタープライズ アプリケーション]** を選択し、**[すべてのアプリケーション]**、**[Adobe Sign]** の順に選択します。

    ![[エンタープライズ アプリケーション] ブレード](common/enterprise-applications.png)

2. アプリケーションの一覧で、「**Adobe Sign**」と入力して選択します。

    ![アプリケーションの一覧の Adobe Sign のリンク](common/all-applications.png)

3. 左側のメニューで **[ユーザーとグループ]** を選びます。

    ![[ユーザーとグループ] リンク](common/users-groups-blade.png)

4. **[ユーザーの追加]** をクリックし、**[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。

    ![[割り当ての追加] ウィンドウ](common/add-assign-user.png)

5. **[ユーザーとグループ]** ダイアログの [ユーザー] の一覧で **[Britta Simon]** を選択し、画面の下部にある **[選択]** ボタンをクリックします。

6. SAML アサーション内に任意のロール値が必要な場合、**[ロールの選択]** ダイアログでユーザーに適したロールを一覧から選択し、画面の下部にある **[選択]** をクリッします。

7. **[割り当ての追加]** ダイアログで、**[割り当て]** ボタンをクリックします。

### <a name="create-adobe-sign-test-user"></a>Adobe Sign のテスト ユーザーの作成

Azure AD ユーザーが Adobe Sign にサインインできるようにするには、ユーザーを Adobe Sign にプロビジョニングする必要があります。 この設定は手動で行います。

>[!NOTE]
>他の Adobe Sign ユーザー アカウント作成ツールまたは Adobe Sign から提供されている API を使用して、Azure AD ユーザー アカウントをプロビジョニングできます。 

1. **Adobe Sign** 企業サイトに管理者としてサインインします。

2. 上部のメニューで、**[アカウント]** を選択します。 次に、左側のウィンドウで **[ユーザーとグループ]** > **[新規ユーザーを作成]** を選択します。
   
    ![Adobe Sign 企業サイトのスクリーンショット ([アカウント]、[ユーザーとグループ]、[新規ユーザーを作成] が赤枠で示されています)](./media/adobe-echosign-tutorial/ic789524.png "アカウント")
   
3. **[新しいユーザーの作成]** セクションで、次の手順に従います。
   
    ![[新規ユーザーを作成] セクションのスクリーンショット](./media/adobe-echosign-tutorial/ic789525.png "ユーザーの作成")
   
    a. 関連するテキスト ボックスに、プロビジョニングする有効な Azure AD アカウントの **[メール アドレス]**、**[名]**、**[姓]** を入力します。
   
    b. **[ユーザーを作成]** を選択します。

>[!NOTE]
>Azure Active Directory のアカウント所有者には、アカウントがアクティブになる前に、アカウント確認用のリンクを含む電子メールが送信されます。 

### <a name="test-single-sign-on"></a>シングル サインオンのテスト 

このセクションでは、アクセス パネルを使用して Azure AD のシングル サインオン構成をテストします。

アクセス パネル上で [Adobe Sign] タイルをクリックすると、SSO を設定した Adobe Sign に自動的にサインインします。 アクセス パネルの詳細については、[アクセス パネルの概要](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)に関する記事を参照してください。

## <a name="additional-resources"></a>その他のリソース

- [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Azure Active Directory の条件付きアクセスとは](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

