---
title: 'チュートリアル: Azure Active Directory と Symantec Web Security Service (WSS) の統合 | Microsoft Docs'
description: Azure Active Directory と Symantec Web Security Service (WSS) の間でシングル サインオンを構成する方法について説明します。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 12/25/2018
ms.author: jeedes
ms.openlocfilehash: 230f7fd9c62f657ce8ab893db2256808dce9a7ba
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "92518366"
---
# <a name="tutorial-azure-active-directory-integration-with-symantec-web-security-service-wss"></a>チュートリアル: Azure Active Directory と Symantec Web Security Service (WSS) の統合

このチュートリアルでは、Symantec Web Security Service (WSS) アカウントを Azure Active Directory (Azure AD) アカウントに統合して、WSS で Azure AD にプロビジョニングされたエンド ユーザーを SAML 認証を使用して認証し、ユーザー レベルまたはグループ レベルのポリシー ルールを適用できるようにする方法について説明します。

Symantec Web Security Service (WSS) と Azure AD の統合には、次の利点があります。

- WSS アカウントによって使用されるすべてのエンド ユーザーとグループを Azure AD ポータルから管理できます。

- エンド ユーザーは、Azure AD 資格情報を使用して WSS で自己認証を行うことができます。

- WSS アカウントに定義されたユーザー レベルおよびグループ レベルのポリシー ルールを適用できます。

SaaS アプリと Azure AD の統合の詳細については、「 [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)」を参照してください。
Azure サブスクリプションをお持ちでない場合は、開始する前に[無料アカウントを作成](https://azure.microsoft.com/free/)してください。

## <a name="prerequisites"></a>前提条件

Symantec Web Security Service (WSS) と Azure AD の統合を構成するには、次のものが必要です。

* Azure AD サブスクリプション。 Azure AD の環境がない場合は、[こちら](https://azure.microsoft.com/pricing/free-trial/)から 1 か月の評価版を入手できます
* Symantec Web Security Service (WSS) シングル サインオンが有効なサブスクリプション

## <a name="scenario-description"></a>シナリオの説明

このチュートリアルでは、テスト環境で Azure AD のシングル サインオンを構成してテストします。

* Symantec Web Security Service (WSS) では、**IDP** によって開始される SSO がサポートされます

## <a name="adding-symantec-web-security-service-wss-from-the-gallery"></a>ギャラリーからの Symantec Web Security Service (WSS) (WSS) の追加

Azure AD への Symantec Web Security Service (WSS) の統合を構成するには、管理対象の SaaS アプリの一覧にギャラリーから Symantec Web Security Service (WSS) を追加する必要があります。

**ギャラリーから Symantec Web Security Service (WSS) を追加するには、次の手順を実行します。**

1. **[Azure Portal](https://portal.azure.com)** の左側のナビゲーション ウィンドウで、 **[Azure Active Directory]** アイコンをクリックします。

    ![Azure Active Directory のボタン](common/select-azuread.png)

2. **[エンタープライズ アプリケーション]** に移動し、 **[すべてのアプリケーション]** オプションを選択します。

    ![[エンタープライズ アプリケーション] ブレード](common/enterprise-applications.png)

3. 新しいアプリケーションを追加するには、ダイアログの上部にある **[新しいアプリケーション]** をクリックします。

    ![[新しいアプリケーション] ボタン](common/add-new-app.png)

4. 検索ボックスに「**Symantec Web Security Service (WSS)**」と入力し、結果ウィンドウで「**Symantec Web Security Service (WSS)**」を選び、**[追加]** をクリックして、アプリケーションを追加します。

     ![結果一覧の Symantec Web Security Service (WSS)](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成とテスト

このセクションでは、**Britta Simon** というテスト ユーザーに基づいて、Symantec Web Security Service (WSS) で Azure AD のシングル サインオンを構成し、テストします。
シングル サインオンを機能させるには、Azure AD ユーザーと Symantec Web Security Service (WSS) 内の関連ユーザーとの間にリンク関係が確立されている必要があります。

Symantec Web Security Service (WSS) での Azure AD シングル サインオンを構成してテストするには、次の構成要素を完了する必要があります。

1. **[Azure AD シングル サインオンの構成](#configure-azure-ad-single-sign-on)** - ユーザーがこの機能を使用できるようにします。
2. **Symantec Web Security Service (WSS) のシングル サインオンの構成** - アプリケーション側でシングル サインオン設定を構成します。
3. **[Azure AD のテスト ユーザーの作成](#create-an-azure-ad-test-user)** - Britta Simon で Azure AD のシングル サインオンをテストします。
4. **[Azure AD テスト ユーザーの割り当て](#assign-the-azure-ad-test-user)** - Britta Simon が Azure AD シングル サインオンを使用できるようにします。
5. **[Symantec Web Security Service (WSS) テスト ユーザーの作成](#create-symantec-web-security-service-wss-test-user)** - Symantec Web Security Service (WSS) で Britta Simon に対応するユーザーを作成し、Azure AD の Britta Simon にリンクさせます。
6. **[シングル サインオンのテスト](#test-single-sign-on)** - 構成が機能するかどうかを確認します。

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成

このセクションでは、Azure portal 上で Azure AD のシングル サインオンを有効にします。

Symantec Web Security Service (WSS) で Azure AD シングル サインオンを構成するには、次の手順を実行します。

1. [Azure portal](https://portal.azure.com/) の **Symantec Web Security Service (WSS)** アプリケーション統合ページで、**[シングル サインオン]** を選択します。

    ![シングル サインオン構成のリンク](common/select-sso.png)

2. **[シングル サインオン方式の選択]** ダイアログで、 **[SAML/WS-Fed]** モードを選択して、シングル サインオンを有効にします。

    ![シングル サインオン選択モード](common/select-saml-option.png)

3. **[SAML でシングル サインオンをセットアップします]** ページで、 **[編集]** アイコンをクリックして **[基本的な SAML 構成]** ダイアログを開きます。

    ![基本的な SAML 構成を編集する](common/edit-urls.png)

4. **[基本的な SAML 構成]** ダイアログ ボックスで、次の手順を実行します。

    ![Symantec Web Security Service (WSS) ドメインおよび URL シングル サインオン情報](common/idp-intiated.png)

    a. **[識別子]** テキスト ボックスに、`https://saml.threatpulse.net:8443/saml/saml_realm` という URL を入力します。

    b. **[応答 URL]** ボックスに、`https://saml.threatpulse.net:8443/saml/saml_realm/bcsamlpost` という URL を入力します。

    > [!NOTE]
    > **ID** と **応答 URL** の値がなんらかの理由で有効でない場合には、[Symantec Web Security Service (WSS) クライアント サポート チーム](https://www.symantec.com/contact-us)に問い合わせてください。 Azure portal の **[基本的な SAML 構成]** セクションに示されているパターンを参照することもできます。

5. **[SAML でシングル サインオンをセットアップします]** ページの **[SAML 署名証明書]** セクションで、 **[ダウンロード]** をクリックして、要件のとおりに指定したオプションから **フェデレーション メタデータ XML** をダウンロードして、お使いのコンピューターに保存します。

    ![証明書のダウンロードのリンク](common/metadataxml.png)

### <a name="configure-symantec-web-security-service-wss-single-sign-on"></a>Symantec Web Security Service (WSS) のシングル サインオンの構成

Symantec Web Security Service (WSS) 側にシングル サインオンを構成するには、WSS のオンライン ドキュメントを参照してください。 ダウンロードした **フェデレーション メタデータ XML** は、WSS ポータルにインポートする必要があります。 WSS ポータルの構成でサポートが必要な場合には、[Symantec Web Security Service (WSS) サポート チーム](https://www.symantec.com/contact-us)に問い合わせてください。

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

このセクションでは、Britta Simon に Symantec Web Security Service (WSS) へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

1. Azure portal で **[エンタープライズ アプリケーション]** を選択し、**[すべてのアプリケーション]** を選択してから、**[Symantec Web Security Service (WSS)]** を選択します。

    ![[エンタープライズ アプリケーション] ブレード](common/enterprise-applications.png)

2. アプリケーションの一覧で、「**Symantec Web Security Service (WSS)**」と入力して選択します。

    ![アプリケーションの一覧の Symantec Web Security Service (WSS) リンク](common/all-applications.png)

3. 左側のメニューで **[ユーザーとグループ]** を選びます。

    ![[ユーザーとグループ] リンク](common/users-groups-blade.png)

4. **[ユーザーの追加]** をクリックし、 **[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。

    ![[割り当ての追加] ウィンドウ](common/add-assign-user.png)

5. **[ユーザーとグループ]** ダイアログの [ユーザー] の一覧で **[Britta Simon]** を選択し、画面の下部にある **[選択]** ボタンをクリックします。

6. SAML アサーション内に任意のロール値が必要な場合、 **[ロールの選択]** ダイアログでユーザーに適したロールを一覧から選択し、画面の下部にある **[選択]** をクリッします。

7. **[割り当ての追加]** ダイアログで、 **[割り当て]** ボタンをクリックします。

### <a name="create-symantec-web-security-service-wss-test-user"></a>Symantec Web Security Service (WSS) のテスト ユーザーの作成

このセクションでは、Symantec Web Security Service (WSS) で Britta Simon というユーザーを作成します。 対応するエンド ユーザー名を WSS ポータルで手動で作成するか、または、Azure AD にプロビジョニングされたユーザーまたはグループが数分後 (最大 15 分) に WSS ポータルに同期されるのを待ちます。 シングル サインオンを使用する前に、ユーザーを作成し、有効化する必要があります。 また、Web サイトを参照するために使用する、エンド ユーザー マシンのパブリック IP アドレスを Symantec Web Security Service (WSS) ポータルにプロビジョニングする必要もあります。

> [!NOTE]
> [ここ](https://www.bing.com/search?q=my+ip+address&qs=AS&pq=my+ip+a&sc=8-7&cvid=29A720C95C78488CA3F9A6BA0B3F98C5&FORM=QBLH&sp=1)をクリックして、マシンのパブリック IP アドレスを取得してください。

### <a name="test-single-sign-on"></a>シングル サインオンのテスト 

これまでに、WSS アカウントで Azure AD の SAML 認証が使用されるように構成しましたので、このセクションでは、そのシングル サインオン機能をテストします。

WSS にトラフィックをプロキシするように Web ブラウザーを構成したら、ブラウザーを開いてサイトを参照しようとすると、Azure サインオン ページにリダイレクトされます。 Azure AD にプロビジョニングされたテスト エンド ユーザー (つまり、BrittaSimon) の資格情報と、関連するパスワードを入力します。 認証されると、選択した Web サイトを参照できるようになります。 BrittaSimon が特定のサイトを参照できないようにするポリシー ルールを WSS 側に作成した場合、ユーザー BrittaSimon としてそのサイトを参照しようとすると、WSS ブロック ページが表示されます。

## <a name="additional-resources"></a>その他のリソース

- [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](./tutorial-list.md)

- [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)

- [Azure Active Directory の条件付きアクセスとは](../conditional-access/overview.md)