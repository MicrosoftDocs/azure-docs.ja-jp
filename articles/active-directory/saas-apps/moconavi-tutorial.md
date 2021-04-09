---
title: 'チュートリアル: Azure Active Directory と moconavi の統合 | Microsoft Docs'
description: Azure Active Directory と moconavi の間のシングル サインオンを構成する方法について説明します。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 02/25/2019
ms.author: jeedes
ms.openlocfilehash: f4ddb41528357d2f1b5c9e671ec2dd1308f9bf11
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "92516835"
---
# <a name="tutorial-azure-active-directory-integration-with-moconavi"></a>チュートリアル: Azure Active Directory と moconavi の統合

このチュートリアルでは、moconavi と Azure Active Directory (Azure AD) を統合する方法について説明します。
moconavi と Azure AD の統合には、次の利点があります。

* moconavi にアクセスできる ユーザーを Azure AD 上でコントロールできます。
* ユーザーが自分の Azure AD アカウントを使用して自動的に moconavi にサインイン (シングル サインオン) するよう指定できます。
* 1 つの中央サイト (Azure Portal) でアカウントを管理できます。

SaaS アプリと Azure AD の統合の詳細については、「 [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)」を参照してください。
Azure サブスクリプションをお持ちでない場合は、開始する前に[無料アカウントを作成](https://azure.microsoft.com/free/)してください。

## <a name="prerequisites"></a>前提条件

moconavi と Azure AD の統合を構成するには、次のものが必要です。

* Azure AD サブスクリプション。 Azure AD の環境がない場合は、[こちら](https://azure.microsoft.com/pricing/free-trial/)から 1 か月の評価版を入手できます
* moconavi でのシングル サインオンが有効なサブスクリプション

## <a name="scenario-description"></a>シナリオの説明

このチュートリアルでは、テスト環境で Azure AD のシングル サインオンを構成してテストします。

* moconavi では、**SP** によって開始される SSO がサポートされます

## <a name="adding-moconavi-from-the-gallery"></a>ギャラリーからの moconavi の追加

Azure AD への moconavi の統合を構成するには、ギャラリーから管理対象 SaaS アプリの一覧に moconavi を追加する必要があります。

**ギャラリーから moconavi を追加するには、次の手順を実行します。**

1. **[Azure Portal](https://portal.azure.com)** の左側のナビゲーション ウィンドウで、 **[Azure Active Directory]** アイコンをクリックします。

    ![Azure Active Directory のボタン](common/select-azuread.png)

2. **[エンタープライズ アプリケーション]** に移動し、 **[すべてのアプリケーション]** オプションを選択します。

    ![[エンタープライズ アプリケーション] ブレード](common/enterprise-applications.png)

3. 新しいアプリケーションを追加するには、ダイアログの上部にある **[新しいアプリケーション]** をクリックします。

    ![[新しいアプリケーション] ボタン](common/add-new-app.png)

4. 検索ボックスに「**moconavi**」と入力し、結果パネルで **[moconavi]** を選び、 **[追加]** をクリックしてアプリケーションを追加します。

     ![結果リストの moconavi](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成とテスト

このセクションでは、**Britta Simon** というテスト ユーザーに基づいて、moconavi で Azure AD のシングル サインオンを構成し、テストします。
シングル サインオンを機能させるには、Azure AD ユーザーと moconavi 内の関連ユーザー間にリンク関係が確立されている必要があります。

moconavi で Azure AD のシングル サインオンを構成してテストするには、次の構成要素を完了する必要があります。

1. **[Azure AD シングル サインオンの構成](#configure-azure-ad-single-sign-on)** - ユーザーがこの機能を使用できるようにします。
2. **[moconavi シングル サインオンの構成](#configure-moconavi-single-sign-on)** - アプリケーション側でシングル サインオン設定を構成します。
3. **[Azure AD のテスト ユーザーの作成](#create-an-azure-ad-test-user)** - Britta Simon で Azure AD のシングル サインオンをテストします。
4. **[Azure AD テスト ユーザーの割り当て](#assign-the-azure-ad-test-user)** - Britta Simon が Azure AD シングル サインオンを使用できるようにします。
5. **[moconavi テスト ユーザーの作成](#create-moconavi-test-user)** - moconavi で Britta Simon に対応するユーザーを作成し、Azure AD の Britta Simon にリンクさせます。
6. **[シングル サインオンのテスト](#test-single-sign-on)** - 構成が機能するかどうかを確認します。

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成

このセクションでは、Azure portal 上で Azure AD のシングル サインオンを有効にします。

moconavi で Azure AD シングル サインオンを構成するには、次の手順を実行します。

1. [Azure portal](https://portal.azure.com/) の **moconavi** アプリケーション統合ページで、 **[シングル サインオン]** を選択します。

    ![シングル サインオン構成のリンク](common/select-sso.png)

2. **[シングル サインオン方式の選択]** ダイアログで、 **[SAML/WS-Fed]** モードを選択して、シングル サインオンを有効にします。

    ![シングル サインオン選択モード](common/select-saml-option.png)

3. **[SAML でシングル サインオンをセットアップします]** ページで、 **[編集]** アイコンをクリックして **[基本的な SAML 構成]** ダイアログを開きます。

    ![基本的な SAML 構成を編集する](common/edit-urls.png)

4. **[基本的な SAML 構成]** セクションで、次の手順を実行します。

    ![[moconavi のドメインと URL] のシングル サインオン情報](common/sp-identifier-reply.png)

    a. **[サインオン URL]** ボックスに、`https://<yourserverurl>/moconavi-saml2/saml/login` という形式で URL を入力します。

    b. **[識別子]** ボックスに、`https://<yourserverurl>/moconavi-saml2` という形式で URL を入力します。

    c. **[応答 URL]** ボックスに、`https://<yourserverurl>/moconavi-saml2/saml/SSO` のパターンを使用して URL を入力します

    > [!NOTE]
    > これらは実際の値ではありません。 実際のサインオン URL、識別子、および応答 URL で値を更新します。 これらの値を取得するには、[moconavi クライアント サポート チーム](mailto:support@recomot.co.jp)に連絡してください。 Azure portal の **[基本的な SAML 構成]** セクションに示されているパターンを参照することもできます。

5. **[SAML でシングル サインオンをセットアップします]** ページの **[SAML 署名証明書]** セクションで、 **[ダウンロード]** をクリックして、要件のとおりに指定したオプションから **フェデレーション メタデータ XML** をダウンロードして、お使いのコンピューターに保存します。

    ![証明書のダウンロードのリンク](common/metadataxml.png)

6. **[moconavi の設定]** セクションで、要件どおりの適切な URL をコピーします。

    ![構成 URL のコピー](common/copy-configuration-urls.png)

    a. ログイン URL

    b. Azure AD 識別子

    c. ログアウト URL

### <a name="configure-moconavi-single-sign-on"></a>moconavi でシングル サインオンを構成する

**moconavi** 側でシングル サインオンを構成するには、ダウンロードした **フェデレーション メタデータ XML** と Azure portal からコピーした適切な URL を [moconavi サポート チーム](mailto:support@recomot.co.jp)に送信する必要があります。 サポート チームはこれを設定して、SAML SSO 接続が両方の側で正しく設定されるようにします。

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

このセクションでは、Britta Simon に moconavi へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

1. Azure portal 上で **[エンタープライズ アプリケーション]** を選択し、 **[すべてのアプリケーション]** を選択してから、 **[moconavi]** を選択します。

    ![[エンタープライズ アプリケーション] ブレード](common/enterprise-applications.png)

2. アプリケーションの一覧で **[moconavi]** を選択します。

    ![アプリケーションの一覧の [moconavi] リンク](common/all-applications.png)

3. 左側のメニューで **[ユーザーとグループ]** を選びます。

    ![[ユーザーとグループ] リンク](common/users-groups-blade.png)

4. **[ユーザーの追加]** をクリックし、 **[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。

    ![[割り当ての追加] ウィンドウ](common/add-assign-user.png)

5. **[ユーザーとグループ]** ダイアログの [ユーザー] の一覧で **[Britta Simon]** を選択し、画面の下部にある **[選択]** ボタンをクリックします。

6. SAML アサーション内に任意のロール値が必要な場合、 **[ロールの選択]** ダイアログでユーザーに適したロールを一覧から選択し、画面の下部にある **[選択]** をクリッします。

7. **[割り当ての追加]** ダイアログで、 **[割り当て]** ボタンをクリックします。

### <a name="create-moconavi-test-user"></a>moconavi テスト ユーザーの作成

このセクションでは、moconavi で Britta Simon というユーザーを作成します。 [moconavi サポート チーム](mailto:support@recomot.co.jp)と連携して、moconavi プラットフォームにユーザーを追加してください。 シングル サインオンを使用する前に、ユーザーを作成し、有効化する必要があります。

### <a name="test-single-sign-on"></a>シングル サインオンのテスト 

1. Microsoft Store から moconavi をインストールします。

2. moconavi を起動します。

3. **[Connect setting]\(接続設定\)** をクリックします。

    ![moconavi を示すスクリーンショット。[接続設定] ボタンが表示されています。](./media/moconavi-tutorial/testing1.png)

4. **[Connect to URL]\(URL への接続\)** ボックスに「`https://mcs-admin.moconavi.biz/gateway`」と入力し、**[Done]\(完了\)** をクリックします。

    ![スクリーンショットは、[URL への接続] ボックスと [完了] ボタンを示すスクリーンショット。](./media/moconavi-tutorial/testing2.png)

5. 次のスクリーンショットで、次の手順を実行します。

    ![moconavi ページを示すスクリーンショット。ここでは、説明されている値を入力できます。](./media/moconavi-tutorial/testing3.png)

    a. **[Input Authentication Key]\(認証キーを入力\)**: **[Input Authentication Key]\(認証キーを入力\)** ボックスに「`azureAD`」と入力します。

    b. **[Input User ID]\(ユーザー ID を入力\)**: **[Input User ID]\(ユーザー ID を入力\)** ボックスに `your ad account` を入力します。

    c. **[LOGIN]\(ログイン\)** をクリックします。

6. **[Password]\(パスワード\)** ボックスに Azure AD のパスワードを入力し、 **[Login]\(ログイン\)** をクリックします。

    ![Azure AD パスワードを入力する場所を示すスクリーンショット。](./media/moconavi-tutorial/testing4.png)

7. メニューが表示されたら、Azure AD 認証は成功です。

    ![moconavi の [電話] アイコンを示すスクリーンショット。](./media/moconavi-tutorial/testing5.png)

## <a name="additional-resources"></a>その他のリソース

- [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](./tutorial-list.md)

- [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)

- [Azure Active Directory の条件付きアクセスとは](../conditional-access/overview.md)