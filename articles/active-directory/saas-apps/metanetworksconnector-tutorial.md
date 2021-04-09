---
title: 'チュートリアル: Azure Active Directory と Meta Networks Connector の統合 | Microsoft Docs'
description: Azure Active Directory と Meta Networks Connector の間でシングル サインオンを構成する方法について説明します。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 02/21/2019
ms.author: jeedes
ms.openlocfilehash: b14a75dba2860c9dee58e40673d3299fdde277e7
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "92516870"
---
# <a name="tutorial-azure-active-directory-integration-with-meta-networks-connector"></a>チュートリアル: Azure Active Directory と Meta Networks Connector の統合

このチュートリアルでは、Meta Networks Connector と Azure Active Directory (Azure AD) を統合する方法について説明します。
Meta Networks Connector と Azure AD の統合には、次の利点があります。

* Meta Networks Connector にアクセスする Azure AD ユーザーを制御できます。
* ユーザーが自分の Azure AD アカウントで自動的に Meta Networks Connector にサインイン (シングル サインオン) できるように設定できます。
* 1 つの中央サイト (Azure Portal) でアカウントを管理できます。

SaaS アプリと Azure AD の統合の詳細については、「 [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)」を参照してください。
Azure サブスクリプションをお持ちでない場合は、開始する前に[無料アカウントを作成](https://azure.microsoft.com/free/)してください。

## <a name="prerequisites"></a>前提条件

Meta Networks Connector と Azure AD の統合を構成するには、次のものが必要です。

* Azure AD サブスクリプション。 Azure AD の環境がない場合は、[こちら](https://azure.microsoft.com/pricing/free-trial/)から 1 か月の評価版を入手できます
* Meta Networks Connector でのシングル サインオンが有効なサブスクリプション

## <a name="scenario-description"></a>シナリオの説明

このチュートリアルでは、テスト環境で Azure AD のシングル サインオンを構成してテストします。

* Meta Networks Connector では、**SP** と **IDP** によって開始される SSO がサポートされます
 
* Meta Networks Connector では、**Just-In-Time** ユーザー プロビジョニングがサポートされます

## <a name="adding-meta-networks-connector-from-the-gallery"></a>ギャラリーからの Meta Networks Connector の追加

Azure AD への Meta Networks Connector の統合を構成するには、ギャラリーからマネージド SaaS アプリの一覧に Meta Networks Connector を追加する必要があります。

**ギャラリーから Meta Networks Connector を追加するには、次の手順を行います。**

1. **[Azure Portal](https://portal.azure.com)** の左側のナビゲーション ウィンドウで、 **[Azure Active Directory]** アイコンをクリックします。

    ![Azure Active Directory のボタン](common/select-azuread.png)

2. **[エンタープライズ アプリケーション]** に移動し、 **[すべてのアプリケーション]** オプションを選択します。

    ![[エンタープライズ アプリケーション] ブレード](common/enterprise-applications.png)

3. 新しいアプリケーションを追加するには、ダイアログの上部にある **[新しいアプリケーション]** をクリックします。

    ![[新しいアプリケーション] ボタン](common/add-new-app.png)

4. 検索ボックスに「**Meta Networks Connector**」と入力して、結果パネルから **Meta Networks Connector** を選択し、 **[追加]** ボタンをクリックしてアプリケーションを追加します。

     ![結果一覧の Meta Networks Connector](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成とテスト

このセクションでは、**Britta Simon** というテスト ユーザーに基づいて、Meta Networks Connector で Azure AD のシングル サインオンを構成し、テストします。
シングル サインオンが機能するには、Azure AD ユーザーと Meta Networks Connector の関連ユーザーの間で、リンク関係が確立されている必要があります。

Meta Networks Connector で Azure AD のシングル サインオンを構成してテストするには、次の構成要素を完了する必要があります。

1. **[Azure AD シングル サインオンの構成](#configure-azure-ad-single-sign-on)** - ユーザーがこの機能を使用できるようにします。
2. **[Meta Networks Connector のシングル サインオンの構成](#configure-meta-networks-connector-single-sign-on)** - アプリケーション側でシングル サインオン設定を構成します。
3. **[Azure AD のテスト ユーザーの作成](#create-an-azure-ad-test-user)** - Britta Simon で Azure AD のシングル サインオンをテストします。
4. **[Azure AD テスト ユーザーの割り当て](#assign-the-azure-ad-test-user)** - Britta Simon が Azure AD シングル サインオンを使用できるようにします。
5. **[Meta Networks Connector のテスト ユーザーの作成](#create-meta-networks-connector-test-user)** - Meta Networks Connector で Britta Simon に対応するユーザーを作成し、Azure AD の Britta Simon にリンクさせます。
6. **[シングル サインオンのテスト](#test-single-sign-on)** - 構成が機能するかどうかを確認します。

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成

このセクションでは、Azure portal 上で Azure AD のシングル サインオンを有効にします。

Meta Networks Connector で Azure AD シングル サインオンを構成するには、次の手順を行います。

1. [Azure portal](https://portal.azure.com/) の **Meta Networks Connector** アプリケーション統合ページで、 **[シングル サインオン]** を選択します。

    ![シングル サインオン構成のリンク](common/select-sso.png)

2. **[シングル サインオン方式の選択]** ダイアログで、 **[SAML/WS-Fed]** モードを選択して、シングル サインオンを有効にします。

    ![シングル サインオン選択モード](common/select-saml-option.png)

3. **[SAML でシングル サインオンをセットアップします]** ページで、 **[編集]** アイコンをクリックして **[基本的な SAML 構成]** ダイアログを開きます。

    ![基本的な SAML 構成を編集する](common/edit-urls.png)

4. **[基本的な SAML 構成]** セクションで、アプリケーションを **IDP** 開始モードで構成する場合は、次の手順を実行します。

    ![このスクリーンショットは、[基本的な SAML 構成] を示しています。ここで、識別子と応答 U R L を入力し、[保存] を選択できます。](common/idp-intiated.png)

    a. **[識別子]** ボックスに、`https://login.nsof.io/v1/<ORGANIZATION-SHORT-NAME>/saml/metadata` の形式で URL を入力します。

    b. **[応答 URL]** ボックスに、`https://login.nsof.io/v1/<ORGANIZATION-SHORT-NAME>/sso/saml` のパターンを使用して URL を入力します

5. アプリケーションを **SP** 開始モードで構成する場合は、 **[追加の URL を設定します]** をクリックして次の手順を実行します。

    ![このスクリーンショットは、[追加の U R L を設定します] を示しています。ここで、サインオン U R L を入力できます。](common/both-advanced-urls.png)

    a. **[サインオン URL]** ボックスに、`https://<ORGANIZATION-SHORT-NAME>.metanetworks.com/login` という形式で URL を入力します。

    b. **[リレー状態]** ボックスに、`https://<ORGANIZATION-SHORT-NAME>.metanetworks.com/#/` のパターンで URL を入力します。

    > [!NOTE]
    > これらは実際の値ではありません。 実際の識別子、応答 URL、サインオン URL に値を置き換えます。実際の値については後で説明します。

6. Meta Networks Connector アプリケーションでは、特定の形式の SAML アサーションが求められます。そのため、カスタム属性マッピングを SAML トークン属性構成に追加する必要があります。 次のスクリーンショットには、既定の属性一覧が示されています。 **[編集]** アイコンをクリックして、 **[ユーザー属性]** ダイアログを開きます。

    ![このスクリーンショットは、[編集] アイコンが選択された状態の [User Attributes]\(ユーザー属性\) を示しています。](common/edit-attribute.png)
    
7. その他に、Meta Networks Connector アプリケーションでは、いくつかの属性が SAML 応答で返されることが想定されています。 **[ユーザー属性]** ダイアログの **[ユーザー要求]** セクションで、以下の手順を実行して、以下の表のように SAML トークン属性を追加します。
    
    | 名前 | ソース属性 | 名前空間|
    | ---------------| --------------- | -------- |
    | firstname | User.givenname | |
    | lastname | User.surname | |
    | emailaddress| User.mail| `http://schemas.xmlsoap.org/ws/2005/05/identity/claims` |
    | name | user.userprincipalname| `http://schemas.xmlsoap.org/ws/2005/05/identity/claims` |
    | phone | user.telephonenumber | |

    a. **[新しい要求の追加]** をクリックして **[ユーザー要求の管理]** ダイアログを開きます。

    ![[新しい要求の追加] オプションが備わっている [ユーザー要求] のスクリーンショット。](common/new-save-attribute.png)

    ![スクリーンショットは、説明されている値を入力できる [ユーザー要求の管理] ダイアログ ボックスを示しています。](common/new-attribute-details.png)

    b. **[名前]** ボックスに、その行に対して表示される属性名を入力します。

    c. **[名前空間]** は空白のままにします。

    d. [ソース] として **[属性]** を選択します。

    e. **[ソース属性]** の一覧から、その行に表示される属性値を入力します。

    f. **[OK]** をクリックします。

    g. **[保存]** をクリックします。

8. **[SAML でシングル サインオンをセットアップします]** ページの **[SAML 署名証明書]** セクションで、 **[ダウンロード]** をクリックして要件のとおりに指定したオプションからの **証明書 (Base64)** をダウンロードして、お使いのコンピューターに保存します。

    ![証明書のダウンロードのリンク](common/certificatebase64.png)

9. **[Meta Networks Connector のセットアップ]** セクションで、要件に従って適切な URL をコピーします。

    ![構成 URL のコピー](common/copy-configuration-urls.png)

    a. ログイン URL

    b. Azure AD 識別子

    c. ログアウト URL

### <a name="configure-meta-networks-connector-single-sign-on"></a>Meta Networks Connector のシングル サインオンの構成

1. ブラウザーで新しいタブを開き、Meta Networks Connector の管理者アカウントにログインします。
    
    > [!NOTE]
    > Meta Networks Connector は、セキュリティで保護されたシステムです。 したがって、ポータルにアクセスする前に、接続先側でパブリック IP アドレスを許可リストに登録する必要があります。 パブリック IP アドレスを取得するには、[ここ](https://whatismyipaddress.com/)で指定されているリンクに従います。 IP アドレスを [Meta Networks Connector クライアント サポート チーム](mailto:support@metanetworks.com)に送信して、IP アドレスを許可リストに登録してもらいます。
    
2. **[管理者]** に移動して **[設定]** を選択します。
    
    ![[Administration]\(管理\) メニューの [Settings]\(設定\) が選択されているスクリーンショット。](./media/metanetworksconnector-tutorial/configure3.png)
    
3. **[Log Internet Traffic]\(インターネット トラフィックのログ記録\)** と **[Force VPN MFA]\(VPN MFA の強制\)** がオフに設定されていることを確認します。
    
    ![これらの設定がオフにされているスクリーンショット。](./media/metanetworksconnector-tutorial/configure1.png)
    
4. **[管理者]** に移動して **[SAML]** を選択します。
    
    ![[Administration]\(管理\) メニューの [SAML] が選択されているスクリーンショット。](./media/metanetworksconnector-tutorial/configure4.png)
    
5. **[DETAILS]\(詳細\)** タブで次の手順を実行します。
    
    ![説明されている値を入力できる [DETAILS]\(詳細\) ページを示すスクリーンショット。](./media/metanetworksconnector-tutorial/configure2.png)
    
    a. **[SSO URL]** の値をコピーし、 **[Meta Networks Connector ドメインと URL]** セクションの **[サインイン URL]** テキスト ボックスに貼り付けます。
    
    b. **[Recipient URL]\(受信者 URL\)** の値をコピーし、 **[Meta Networks Connector ドメインと URL]** セクションの **[応答 URL]** テキスト ボックスに貼り付けます。
    
    c. **[Audience URI (SP Entity ID)]\(オーディエンス URI (SP エンティティ ID)\)** の値をコピーし、 **[Meta Networks Connector ドメインと URL]** セクションの **[識別子 (エンティティ ID)]** テキスト ボックスに貼り付けます。
    
    d. SAML を有効にします
    
6. **[GENERAL]\(全般\)** タブで、次の手順を実行します。

    ![説明されている値を入力できる [GENERAL]\(全般\) ページを示すスクリーンショット。](./media/metanetworksconnector-tutorial/configure5.png)

    a. **[Identity Provider Single Sign-On URL]\(ID プロバイダーのシングル サインオン URL\)** に、Azure portal からコピーした **[ログイン URL]** の値を貼り付けます。

    b. **[Identity Provider Issuer]\(ID プロバイダーの発行者\)** に、Azure portal からコピーした **[Azure AD 識別子]** の値を貼り付けます。

    c. Azure Portal からダウンロードした証明書をメモ帳で開き、 **[X.509 Certificate]\(X.509 証明書\)** ボックスに貼り付けます。

    d. **[Just-in-Time Provisioning]\(ジャストイン タイム プロビジョニング\)** を有効にします。

### <a name="create-an-azure-ad-test-user"></a>Azure AD のテスト ユーザーの作成 

このセクションの目的は、Azure Portal で Britta Simon というテスト ユーザーを作成することです。

1. Azure portal の左側のウィンドウで、 **[Azure Active Directory]** 、 **[ユーザー]** 、 **[すべてのユーザー]** の順に選択します。

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

このセクションでは、Britta Simon に Meta Networks Connector へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

1. Azure portal 上で **[エンタープライズ アプリケーション]** を選択し、 **[すべてのアプリケーション]** を選択してから、 **[Meta Networks Connector]** を選択します。

    ![[エンタープライズ アプリケーション] ブレード](common/enterprise-applications.png)

2. アプリケーションの一覧で **[Meta Networks Connector]** を選択します。

    ![アプリケーションの一覧の Meta Networks Connector リンク](common/all-applications.png)

3. 左側のメニューで **[ユーザーとグループ]** を選びます。

    ![[ユーザーとグループ] リンク](common/users-groups-blade.png)

4. **[ユーザーの追加]** をクリックし、 **[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。

    ![[割り当ての追加] ウィンドウ](common/add-assign-user.png)

5. **[ユーザーとグループ]** ダイアログの [ユーザー] の一覧で **[Britta Simon]** を選択し、画面の下部にある **[選択]** ボタンをクリックします。

6. SAML アサーション内に任意のロール値が必要な場合、 **[ロールの選択]** ダイアログでユーザーに適したロールを一覧から選択し、画面の下部にある **[選択]** をクリッします。

7. **[割り当ての追加]** ダイアログで、 **[割り当て]** をクリックします。

### <a name="create-meta-networks-connector-test-user"></a>Meta Networks Connector のテスト ユーザーの作成

このセクションでは、Britta Simon というユーザーを Meta Networks Connector に作成します。 Meta Networks Connector では、Just-In-Time プロビジョニングがサポートされています。この設定は、既定で有効になっています。 このセクションでは、ユーザー側で必要な操作はありません。 ユーザーがまだ Meta Networks Connector に存在しない場合は、Meta Networks Connector にアクセスしようとしたときに新しいユーザーが作成されます。

>[!Note]
>ユーザーを手動で作成する必要がある場合は、[Meta Networks Connector クライアント サポート チーム](mailto:support@metanetworks.com)にお問い合わせください。

### <a name="test-single-sign-on"></a>シングル サインオンのテスト 

このセクションでは、アクセス パネルを使用して Azure AD のシングル サインオン構成をテストします。

アクセス パネルで [Meta Networks Connector] タイルをクリックすると、SSO を設定した Meta Networks Connector に自動的にサインインします。 アクセス パネルの詳細については、[アクセス パネルの概要](../user-help/my-apps-portal-end-user-access.md)に関する記事を参照してください。

## <a name="additional-resources"></a>その他のリソース

- [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](./tutorial-list.md)

- [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)

- [Azure Active Directory の条件付きアクセスとは](../conditional-access/overview.md)