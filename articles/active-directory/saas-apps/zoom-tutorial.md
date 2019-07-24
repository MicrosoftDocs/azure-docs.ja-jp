---
title: チュートリアル:Azure Active Directory と Zoom の統合 | Microsoft Docs
description: Azure Active Directory と Zoom 間でシングル サインオンを構成する方法について説明します。
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: 0ebdab6c-83a8-4737-a86a-974f37269c31
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/05/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 641fe5439e320208d41969b9563293257648d488
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2019
ms.locfileid: "57842092"
---
# <a name="tutorial-azure-active-directory-integration-with-zoom"></a>チュートリアル:Azure Active Directory と Zoom の統合

このチュートリアルでは、Zoom と Azure Active Directory (Azure AD) を統合する方法について説明します。
Zoom と Azure AD の統合には、次の利点があります。

* Zoom にアクセスする Azure AD ユーザーを制御できます。
* ユーザーが自分の Azure AD アカウントを使用して Zoom に自動的にサインイン (シングル サインオン) できるようにすることができます。
* 1 つの中央サイト (Azure Portal) でアカウントを管理できます。

SaaS アプリと Azure AD の統合の詳細については、「 [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)」を参照してください。
Azure サブスクリプションをお持ちでない場合は、開始する前に[無料アカウントを作成](https://azure.microsoft.com/free/)してください。

## <a name="prerequisites"></a>前提条件

Zoom と Azure AD の統合を構成するには、次のものが必要です。

* Azure AD サブスクリプション。 Azure AD の環境がない場合は、[こちら](https://azure.microsoft.com/pricing/free-trial/)から 1 か月の評価版を入手できます
* Zoom でのシングル サインオンが有効なサブスクリプション

## <a name="scenario-description"></a>シナリオの説明

このチュートリアルでは、テスト環境で Azure AD のシングル サインオンを構成してテストします。

* Zoom では、**SP** によって開始される SSO がサポートされます

## <a name="adding-zoom-from-the-gallery"></a>ギャラリーからの Zoom の追加

Azure AD への Zoom の統合を構成するには、ギャラリーから管理対象 SaaS アプリの一覧に Zoom を追加する必要があります。

**ギャラリーから Zoom を追加するには、次の手順に従います。**

1. **[Azure Portal](https://portal.azure.com)** の左側のナビゲーション ウィンドウで、**[Azure Active Directory]** アイコンをクリックします。

    ![Azure Active Directory のボタン](common/select-azuread.png)

2. **[エンタープライズ アプリケーション]** に移動し、**[すべてのアプリケーション]** オプションを選択します。

    ![[エンタープライズ アプリケーション] ブレード](common/enterprise-applications.png)

3. 新しいアプリケーションを追加するには、ダイアログの上部にある **[新しいアプリケーション]** をクリックします。

    ![[新しいアプリケーション] ボタン](common/add-new-app.png)

4. 検索ボックスに「**Zoom**」と入力し、結果パネルで **[Zoom]** を選び、**[追加]** をクリックして、アプリケーションを追加します。

     ![結果一覧の Zoom](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成とテスト

このセクションでは、**Britta Simon** というテスト ユーザーに基づいて、Zoom で Azure AD のシングル サインオンを構成し、テストします。
シングル サインオンを機能させるには、Azure AD ユーザーと Zoom 内の関連ユーザー間にリンク関係が確立されている必要があります。

Zoom で Azure AD のシングル サインオンを構成してテストするには、次の構成要素を完了する必要があります。

1. **[Azure AD シングル サインオンの構成](#configure-azure-ad-single-sign-on)** - ユーザーがこの機能を使用できるようにします。
2. **[Zoom シングル サインオンの構成](#configure-zoom-single-sign-on)** - アプリケーション側でシングル サインオン設定を構成します。
3. **[Azure AD のテスト ユーザーの作成](#create-an-azure-ad-test-user)** - Britta Simon で Azure AD のシングル サインオンをテストします。
4. **[Azure AD テスト ユーザーの割り当て](#assign-the-azure-ad-test-user)** - Britta Simon が Azure AD シングル サインオンを使用できるようにします。
5. **[Zoom テスト ユーザーの作成](#create-zoom-test-user)** - Azure AD の Britta Simon にリンクさせるために、対応するユーザーを Zoom で作成します。
6. **[シングル サインオンのテスト](#test-single-sign-on)** - 構成が機能するかどうかを確認します。

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成

このセクションでは、Azure portal 上で Azure AD のシングル サインオンを有効にします。

Zoom で Azure AD シングル サインオンを構成するには、次の手順に従います。

1. [Azure portal](https://portal.azure.com/) の **Zoom** アプリケーション統合ページで、**[シングル サインオン]** を選択します。

    ![シングル サインオン構成のリンク](common/select-sso.png)

2. **[シングル サインオン方式の選択]** ダイアログで、**[SAML/WS-Fed]** モードを選択して、シングル サインオンを有効にします。

    ![シングル サインオン選択モード](common/select-saml-option.png)

3. **[SAML でシングル サインオンをセットアップします]** ページで、**[編集]** アイコンをクリックして **[基本的な SAML 構成]** ダイアログを開きます。

    ![基本的な SAML 構成を編集する](common/edit-urls.png)

4. **[基本的な SAML 構成]** セクションで、次の手順を実行します。

    ![[Zoom のドメインと URL] のシングル サインオン情報](common/sp-identifier.png)

    a. **[サインオン URL]** ボックスに、次のパターンを使用して URL を入力します。`https://<companyname>.zoom.us`

    b. **[識別子 (エンティティ ID)]** ボックスに、次のパターンを使用して URL を入力します。`<companyname>.zoom.us`

    > [!NOTE]
    > これらは実際の値ではありません。 実際のサインオン URL と識別子でこれらの値を更新します。 この値を取得するには、[Zoom クライアント サポート チーム](https://support.zoom.us/hc/en-us)にお問い合わせください。 Azure portal の **[基本的な SAML 構成]** セクションに示されているパターンを参照することもできます。

5. Zoom アプリケーションは、特定の形式の SAML アサーションを使用するため、カスタム属性のマッピングを SAML トークンの属性の構成に追加する必要があります。 次のスクリーンショットには、既定の属性一覧が示されています。  **[編集]** アイコンをクリックして、 **[ユーザー属性]** ダイアログを開きます。

    ![image](common/edit-attribute.png)

6. その他に、Zoom アプリケーションでは、いくつかの属性が SAML 応答で返されることが想定されています。 **[ユーザー属性]** ダイアログの **[ユーザー要求]** セクションで、以下の手順を実行して、以下の表のように SAML トークン属性を追加します。
    
    | Name | 名前空間  |  ソース属性|
    | ---------------| --------------- | --------- |
    | 電子メール アドレス  | User.mail  | `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/mail` |
    | 名  | User.givenname  | `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname` |
    | 姓  | User.surname  | `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname` |
    | 電話番号  | user.telephonenumber  | `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/phone` |
    | 部署  | user.department  | `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/department` |
    | role |    user.assignedrole |`http://schemas.xmlsoap.org/ws/2005/05/identity/claims/role` |

    > [!NOTE]
    > Azure AD で役割を構成する方法については、[ここ](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal)をクリックしてください

    a. **[新しい要求の追加]** をクリックして **[ユーザー要求の管理]** ダイアログを開きます。

    ![image](common/new-save-attribute.png)

    ![image](common/new-attribute-details.png)

    b. **[名前]** ボックスに、その行に対して表示される属性名を入力します。

    c. [ソース] として **[属性]** を選択します。

    d. **[ソース属性]** の一覧から、その行に表示される属性値を入力します。

    e. **[OK]** をクリックします。

    f. **[Save]** をクリックします。

    > [!NOTE]
    > Zoom では、グループ要求が SAML ペイロードに含まれている必要があります。グループを作成した場合は、[Zoom クライアント サポート チーム](https://support.zoom.us/hc/en-us)にグループ情報を連絡し、Zoom クライアント サポート チーム側でも、そのグループ情報を構成できるようにしてください。 また、Zoom クライアント サポート チーム側で構成を行えるよう、[Zoom クライアント サポート チーム](https://support.zoom.us/hc/en-us)にオブジェクト ID を提供する必要もあります。 [ドキュメント](https://support.zoom.us/hc/en-us/articles/115005887566)に従ってオブジェクト ID を入手してください。

7. **[SAML でシングル サインオンをセットアップします]** ページの **[SAML 署名証明書]** セクションで、**[ダウンロード]** をクリックして要件のとおりに指定したオプションからの**証明書 (Base64)** をダウンロードして、お使いのコンピューターに保存します。

    ![証明書のダウンロードのリンク](common/certificatebase64.png)

8. **[Zoom の設定]** セクションで、要件どおりの適切な URL をコピーします。

    ![構成 URL のコピー](common/copy-configuration-urls.png)

    a. ログイン URL

    b. Azure AD 識別子

    c. ログアウト URL

### <a name="configure-zoom-single-sign-on"></a>Zoom のシングル サインオンを構成する

1. 別の Web ブラウザー ウィンドウで、Zoom 企業サイトに管理者としてログインします。

2. **[シングル サインオン]** タブをクリックします。

    ![[Single sign-on] \(シングル サインオン\) タブ](./media/zoom-tutorial/ic784700.png "シングル サインオン")

3. **[セキュリティ制御]** タブをクリックし、**[シングル サインオン]** に移動します。

4. [Single Sign-On] セクションで、次の手順に従います。

    ![[Single sign-on] \(シングル サインオン\) セクション](./media/zoom-tutorial/ic784701.png "シングル サインオン")

    a. **[Sign-in page URL]\(サインイン ページの URL\)** テキスト ボックスに、Azure portal からコピーした**ログイン URL** の値を貼り付けます。

    b. **[サインアウト ページの URL]** 値については、Azure portal に移動し、左側の **[Azure Active Directory]** をクリックしてから **[アプリの登録]** に移動する必要があります。

    ![Azure Active Directory のボタン](./media/zoom-tutorial/appreg.png)

    c. **[エンドポイント]** をクリックします

    ![[エンドポイント] ボタン](./media/zoom-tutorial/endpoint.png)

    d. **[SAML-P サインアウト エンドポイント]** をコピーして **[サインアウト ページの URL]** テキストボックスに貼り付けます。

    ![エンドポイントのコピー ボタン](./media/zoom-tutorial/endpoint1.png)

    e. base-64 でエンコードされた証明書をメモ帳で開き、その内容をクリップボードにコピーして、 **[ID プロバイダー証明書]** ボックスに貼り付けます。

    f. **[Issuer]\(発行者\)** テキストボックスに、Azure portal からコピーした **Azure AD 識別子**の値を貼り付けます。 

    g. **[Save]** をクリックします。

    > [!NOTE]
    > 詳しくは、Zoom のドキュメント [https://zoomus.zendesk.com/hc/articles/115005887566](https://zoomus.zendesk.com/hc/articles/115005887566) をご覧ください。

### <a name="create-an-azure-ad-test-user"></a>Azure AD のテスト ユーザーの作成

このセクションの目的は、Azure Portal で Britta Simon というテスト ユーザーを作成することです。

1. Azure portal の左側のウィンドウで、**[Azure Active Directory]**、**[ユーザー]**、**[すべてのユーザー]** の順に選択します。

    ![[ユーザーとグループ] と [すべてのユーザー] リンク](common/users.png)

2. 画面の上部にある **[新しいユーザー]** を選択します。

    ![[新しいユーザー] ボタン](common/new-user.png)

3. [ユーザーのプロパティ] で、次の手順を実行します。

    ![[ユーザー] ダイアログ ボックス](common/user-properties.png)

    a. **[名前]** フィールドに「**BrittaSimon**」と入力します。
  
    b. **[ユーザー名]** フィールドに「**brittasimon\@yourcompanydomain.extension**」と入力します  
    たとえば、BrittaSimon@contoso.com のように指定します。

    c. **[パスワードを表示]** チェック ボックスをオンにし、[パスワード] ボックスに表示された値を書き留めます。

    d. **Create** をクリックしてください。

### <a name="assign-the-azure-ad-test-user"></a>Azure AD テスト ユーザーの割り当て

このセクションでは、Britta Simon に Zoom へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

1. Azure portal 上で **[エンタープライズ アプリケーション]** を選択し、**[すべてのアプリケーション]** を選択してから、**[Zoom]** を選択します。

    ![[エンタープライズ アプリケーション] ブレード](common/enterprise-applications.png)

2. アプリケーションの一覧で、「**Zoom**」と入力して選択します。

    ![アプリケーションの一覧の [Zoom] リンク](common/all-applications.png)

3. 左側のメニューで **[ユーザーとグループ]** を選びます。

    ![[ユーザーとグループ] リンク](common/users-groups-blade.png)

4. **[ユーザーの追加]** をクリックし、**[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。

    ![[割り当ての追加] ウィンドウ](common/add-assign-user.png)

5. **[ユーザーとグループ]** ダイアログの [ユーザー] の一覧で **[Britta Simon]** を選択し、画面の下部にある **[選択]** ボタンをクリックします。

6. SAML アサーション内に任意のロール値が必要な場合、**[ロールの選択]** ダイアログでユーザーに適したロールを一覧から選択し、画面の下部にある **[選択]** をクリッします。

7. **[割り当ての追加]** ダイアログで、**[割り当て]** ボタンをクリックします。

### <a name="create-zoom-test-user"></a>Zoom テスト ユーザーの作成

Azure AD ユーザーが Zoom にログインできるようにするには、ユーザーを Zoom にプロビジョニングする必要があります。 Zoom の場合、プロビジョニングは手動で行います。

### <a name="to-provision-a-user-account-perform-the-following-steps"></a>ユーザー アカウントをプロビジョニングするには、次の手順を実行します。

1. **Zoom** 企業サイトに管理者としてログインします。

2. **[アカウント管理]** タブをクリックし、**[ユーザー管理]** をクリックします。

3. [ユーザー管理] セクションで、 **[ユーザーの追加]** をクリックします。

    ![ユーザー管理](./media/zoom-tutorial/ic784703.png "ユーザー管理")

4. **[ユーザーの追加]** ページで、次の手順を実行します。

    ![Add users](./media/zoom-tutorial/ic784704.png "Add users")

    a. **[ユーザー タイプ]** として、**[基本]** を選択します。

    b. **[Emails]\(電子メール\)** ボックスに、プロビジョニングする有効な Azure AD アカウントの電子メール アドレスを入力します。

    c. **[追加]** をクリックします。

> [!NOTE]
> Zoom から提供されている他の Zoom ユーザー アカウント作成ツールまたは API を使用して、Azure Active Directory ユーザー アカウントをプロビジョニングできます。

### <a name="test-single-sign-on"></a>シングル サインオンのテスト

このセクションでは、アクセス パネルを使用して Azure AD のシングル サインオン構成をテストします。

アクセス パネル上で [Zoom] タイルをクリックすると、SSO を設定した Zoom に自動的にサインインします。 アクセス パネルの詳細については、[アクセス パネルの概要](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)に関する記事を参照してください。

## <a name="additional-resources"></a>その他のリソース

- [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Azure Active Directory の条件付きアクセスとは](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
