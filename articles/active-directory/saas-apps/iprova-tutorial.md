---
title: チュートリアル:Azure Active Directory と iProva の統合 | Microsoft Docs
description: Azure Active Directory と iProva の間でシングル サインオンを構成する方法について説明します。
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 1eaeef9b-4479-4a9f-b1b2-bc13b857c75c
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 04/14/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: bf685919879a9ee82cbaa3863826c891422d3013
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/13/2019
ms.locfileid: "67099819"
---
# <a name="tutorial-azure-active-directory-integration-with-iprova"></a>チュートリアル:Azure Active Directory と iProva の統合

このチュートリアルでは、iProva と Azure Active Directory (Azure AD) を統合する方法について説明します。
iProva と Azure AD の統合には、次の利点があります。

* iProva にアクセスできる Azure AD ユーザーを制御できます。
* ユーザーが自分の Azure AD アカウントを使用して iProva に自動的にサインイン (シングル サインオン) できるようにすることができます。
* 1 つの中央サイト (Azure Portal) でアカウントを管理できます。

SaaS アプリと Azure AD の統合の詳細については、「 [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)」を参照してください。
Azure サブスクリプションをお持ちでない場合は、開始する前に[無料アカウントを作成](https://azure.microsoft.com/free/)してください。

## <a name="prerequisites"></a>前提条件

iProva と Azure AD の統合を構成するには、次のものが必要です。

* Azure AD サブスクリプション。 Azure AD の環境がない場合は、[無料アカウント](https://azure.microsoft.com/free/)を取得できます
* iProva のシングル サインオンが有効なサブスクリプション

## <a name="scenario-description"></a>シナリオの説明

このチュートリアルでは、テスト環境で Azure AD のシングル サインオンを構成してテストします。

* iProva では、**SP** によって開始される SSO がサポートされます

## <a name="adding-iprova-from-the-gallery"></a>ギャラリーからの iProva の追加

Azure AD への iProva の統合を構成するには、ギャラリーから管理対象 SaaS アプリの一覧に iProva を追加する必要があります。

**ギャラリーから iProva を追加するには、次の手順を実行します。**

1. **[Azure Portal](https://portal.azure.com)** の左側のナビゲーション ウィンドウで、 **[Azure Active Directory]** アイコンをクリックします。

    ![Azure Active Directory のボタン](common/select-azuread.png)

2. **[エンタープライズ アプリケーション]** に移動し、 **[すべてのアプリケーション]** オプションを選択します。

    ![[エンタープライズ アプリケーション] ブレード](common/enterprise-applications.png)

3. 新しいアプリケーションを追加するには、ダイアログの上部にある **[新しいアプリケーション]** をクリックします。

    ![[新しいアプリケーション] ボタン](common/add-new-app.png)

4. 検索ボックスに「**iProva**」と入力し、結果ウィンドウで **iProva** を選択し、 **[追加]** をクリックして、アプリケーションを追加します。

    ![結果一覧の iProva](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成とテスト

このセクションでは、**Britta Simon** というテスト ユーザーに基づいて、iProva で Azure AD のシングル サインオンを構成し、テストします。
シングル サインオンを機能させるには、Azure AD ユーザーと iProva 内の関連ユーザー間にリンク関係が確立されている必要があります。

iProva で Azure AD のシングル サインオンを構成してテストするには、次の構成要素を完了する必要があります。

1. **[iProva からの構成情報の取得](#retrieve-configuration-information-from-iprova)** - 次の手順の準備として。
2. **[Azure AD シングル サインオンの構成](#configure-azure-ad-single-sign-on)** - ユーザーがこの機能を使用できるようにします。
3. **[iProva シングル サインオンの構成](#configure-iprova-single-sign-on)** - アプリケーション側でシングル サインオン設定を構成します。
4. **[Azure AD のテスト ユーザーの作成](#create-an-azure-ad-test-user)** - Britta Simon で Azure AD のシングル サインオンをテストします。
5. **[Azure AD テスト ユーザーの割り当て](#assign-the-azure-ad-test-user)** - Britta Simon が Azure AD シングル サインオンを使用できるようにします。
6. **[iProva のテスト ユーザーの作成](#create-iprova-test-user)** - iProva で Britta Simon に対応するユーザーを作成し、Azure AD の Britta Simon にリンクさせます。
7. **[シングル サインオンのテスト](#test-single-sign-on)** - 構成が機能するかどうかを確認します。

### <a name="retrieve-configuration-information-from-iprova"></a>iProva からの構成情報の取得

このセクションでは、Azure AD のシングル サインオンを構成するための情報を iProva から取得します。

1. Web ブラウザーを開き、次の URL パターンを使用して iProva の **[SAML2 info]\(SAML2 の情報\)** ページに移動します。

    | | |
    |-|-|
    | `https://SUBDOMAIN.iprova.nl/saml2info`|
    | `https://SUBDOMAIN.iprova.be/saml2info`|
    | | |

    ![iProva の [SAML2 info]\(SAML2 の情報\) ページを表示する](media/iprova-tutorial/iprova-saml2-info.png)

2. そのブラウザー タブを開いたまま、別のブラウザー タブで次の手順に進みます。

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成

このセクションでは、Azure portal 上で Azure AD のシングル サインオンを有効にします。

iProva で Azure AD シングル サインオンを構成するには、次の手順に従います。

1. [Azure portal](https://portal.azure.com/) の **iProva** アプリケーション統合ページで、 **[シングル サインオン]** を選択します。

    ![シングル サインオン構成のリンク](common/select-sso.png)

2. **[シングル サインオン方式の選択]** ダイアログで、 **[SAML/WS-Fed]** モードを選択して、シングル サインオンを有効にします。

    ![シングル サインオン選択モード](common/select-saml-option.png)

3. **[SAML でシングル サインオンをセットアップします]** ページで、 **[編集]** アイコンをクリックして **[基本的な SAML 構成]** ダイアログを開きます。

    ![基本的な SAML 構成を編集する](common/edit-urls.png)

4. **[基本的な SAML 構成]** セクションで、次の手順を実行します。

    ![[iProva のドメインと URL] のシングル サインオン情報](common/sp-identifier-reply.png)

    a. **iProva の [SAML2 info]\(SAML2 の情報\)** ページで、 **[EntityID]** というラベルの後にある **[Identifier]\(識別子\)** ボックスに値を入力します。 このページは他のブラウザー タブで開いたままです。

    b. **iProva の [SAML2 info]\(SAML2 の情報\)** ページで、 **[Reply URL]\(応答 URL\)** というラベルの後にある **[Reply-URL]** ボックスに値を入力します。 このページは他のブラウザー タブで開いたままです。

    c. **iProva の [SAML2 info]\(SAML2 の情報\)** ページで、 **[Sign-on URL]\(サインオン URL\)** というラベルの後にある **[Sign-on URL]\(サインオン URL\)** ボックスに値を入力します。 このページは他のブラウザー タブで開いたままです。

5. iProva アプリケーションは、特定の形式で構成された SAML アサーションを受け入れます。 このアプリケーションには、次の要求を構成します。 これらの属性の値は、アプリケーション統合ページの **[ユーザー属性]** セクションで管理できます。 **[SAML でシングル サインオンをセットアップします]** ページで、 **[編集]** ボタンをクリックして **[ユーザー属性]** ダイアログを開きます。

    ![image](common/edit-attribute.png)

6. **[ユーザー属性]** ダイアログの **[ユーザーの要求]** セクションで、**編集アイコン**を使用して要求を編集するか、 **[新しい要求の追加]** を使用して要求を追加することで、上の図のように SAML トークン属性を構成し、次の手順を実行します。

    | Name | ソース属性| 名前空間  |
    | ---------------| -------- | -----|
    | `samaccountname` | `user.onpremisessamaccountname`| `http://schemas.xmlsoap.org/ws/2005/05/identity/claims`|

    a. **[新しい要求の追加]** をクリックして **[ユーザー要求の管理]** ダイアログを開きます。

    ![image](common/new-save-attribute.png)

    ![image](common/new-attribute-details.png)

    b. **[名前]** ボックスに、その行に対して表示される属性名を入力します。

    c. **[名前空間]** ボックスに、その行に表示される名前空間の値を入力します。

    d. [ソース] として **[属性]** を選択します。

    e. **[ソース属性]** の一覧から、その行に表示される属性値を入力します。

    f. **[OK]** をクリックします。

    g. **[Save]** をクリックします。

7. **Set up Single Sign-On with SAML\(SAML でのシングルサインオンの設定** ページの **SAML 署名証明書** セクションで、コピー ボタンをクリックして **App Federation Metadata Url\(アプリのフェデレーション メタデータ URL)** をコピーして、コンピューターに保存します。

    ![証明書のダウンロードのリンク](common/copy-metadataurl.png)

### <a name="configure-iprova-single-sign-on"></a>iProva のシングル サインオンの構成

1. **Administrator** アカウントを使用して iProva にサインインします。

2. **[Go to]\(移動\)** メニューを開きます。

3. **[Application management]\(アプリケーション管理\)** を選択します。

4. **[System settings]\(システム設定\)** パネルの **[General]\(全般\)** を選択します。

5. **[編集]** を選択します。

6. **[Access control]\(アクセス制御\)** まで下にスクロールします。

    ![iProva のアクセス制御設定](media/iprova-tutorial/iprova-accesscontrol.png)

7. **[Users are automatically logged on with their network accounts]\(ユーザーのネットワーク アカウントを使用して自動的にログオンする\)** 設定を探し、 **[Yes, authentication via SAML]\(はい、SAML 経由で認証します\)** に変更します。 これで、追加のオプションが表示されます。

8. **[Set up]\(セットアップ\)** を選択します。

9. **[次へ]** を選択します。

10. iProva によって、URL からフェデレーション データをダウンロードするか、それともファイルからアップロードするかを問われます。 **[from URL]\(URL から\)** オプションを選択します。

    ![Azure AD メタデータをダウンロードする](media/iprova-tutorial/iprova-download-metadata.png)

11. 「Azure AD シングル サインオンの構成」セクションの最後の手順で保存したメタデータ URL を貼り付けます。

12. 矢印形のボタンを選択して、Azure AD からメタデータをダウンロードします。

13. ダウンロードが完了したら、**Valid Federation Data file downloaded (有効なフェデレーション データ ファイルがダウンロードされました)** という確認メッセージが表示されます。

14. **[次へ]** を選択します。

15. ここでは **[Test login]\(テスト ログイン\)** オプションをスキップし、 **[Next]\(次へ\)** を選択します。

16. **[Claim to use]\(使用する要求\)** ドロップダウン ボックスで **[windowsaccountname]** を選択します。

17. **[完了]** を選択します。

18. これで、 **[Edit general settings]\(全般設定の編集\)** 画面に戻ります。 ページの下部までスクロールし、 **[OK]** を選択して自分の構成を保存します。

### <a name="create-an-azure-ad-test-user"></a>Azure AD のテスト ユーザーの作成 

このセクションの目的は、Azure Portal で Britta Simon というテスト ユーザーを作成することです。

1. Azure portal の左側のウィンドウで、 **[Azure Active Directory]** 、 **[ユーザー]** 、 **[すべてのユーザー]** の順に選択します。

    ![[ユーザーとグループ] と [すべてのユーザー] リンク](common/users.png)

2. 画面の上部にある **[新しいユーザー]** を選択します。

    ![[新しいユーザー] ボタン](common/new-user.png)

3. [ユーザーのプロパティ] で、次の手順を実行します。

    ![[ユーザー] ダイアログ ボックス](common/user-properties.png)

    a. **[名前]** フィールドに「**BrittaSimon**」と入力します。
  
    b. **[ユーザー名]** フィールドに「`brittasimon@yourcompanydomain.extension`」と入力します。 たとえば、BrittaSimon@contoso.com のように指定します。

    c. **[パスワードを表示]** チェック ボックスをオンにし、[パスワード] ボックスに表示された値を書き留めます。

    d. **Create** をクリックしてください。

### <a name="assign-the-azure-ad-test-user"></a>Azure AD テスト ユーザーの割り当て

このセクションでは、Britta Simon に iProva へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

1. Azure portal 上で **[エンタープライズ アプリケーション]** を選択し、 **[すべてのアプリケーション]** を選択してから、 **[iProva]** を選択します。

    ![[エンタープライズ アプリケーション] ブレード](common/enterprise-applications.png)

2. アプリケーションの一覧で **[iProva]** を選択します。

    ![アプリケーションの一覧の iProva のリンク](common/all-applications.png)

3. 左側のメニューで **[ユーザーとグループ]** を選びます。

    ![[ユーザーとグループ] リンク](common/users-groups-blade.png)

4. **[ユーザーの追加]** をクリックし、 **[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。

    ![[割り当ての追加] ウィンドウ](common/add-assign-user.png)

5. **[ユーザーとグループ]** ダイアログの [ユーザー] の一覧で **[Britta Simon]** を選択し、画面の下部にある **[選択]** ボタンをクリックします。

6. SAML アサーション内に任意のロール値が必要な場合、 **[ロールの選択]** ダイアログでユーザーに適したロールを一覧から選択し、画面の下部にある **[選択]** をクリッします。

7. **[割り当ての追加]** ダイアログで、 **[割り当て]** ボタンをクリックします。

### <a name="create-iprova-test-user"></a>iProva テスト ユーザーの作成

1. **Administrator** アカウントを使用して iProva にサインインします。

2. **[Go to]\(移動\)** メニューを開きます。

3. **[Application management]\(アプリケーション管理\)** を選択します。

4. **[Users and user groups]\(ユーザーとユーザー グループ\)** パネルの **[Users]\(ユーザー\)** を選択します。

5. **[追加]** を選択します。

6. **[ユーザー名]** ボックスにユーザー名 (`BrittaSimon@contoso.com` など) を入力します。

7. **[Full name]\(フル ネーム\)** ボックスにユーザーのフル ネームを入力します (「**BrittaSimon**」など)。

8. **[No password (use single sign-on)]\(パスワードなし (シングル サインオンを使用する)\)** オプションを選択します。

9. **[E-mail address]\(メール アドレス\)** ボックスに、ユーザーのメール アドレス (`BrittaSimon@contoso.com` など) を入力します。

10. ページの一番下までスクロールし、 **[Finish]\(完了\)** を選択します。

### <a name="test-single-sign-on"></a>シングル サインオンのテスト

このセクションでは、アクセス パネルを使用して Azure AD のシングル サインオン構成をテストします。

アクセス パネル上で [iProva] タイルをクリックすると、SSO を設定した iProva に自動的にサインインします。 アクセス パネルの詳細については、[アクセス パネルの概要](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)に関する記事を参照してください。

## <a name="additional-resources"></a>その他のリソース

- [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Azure Active Directory の条件付きアクセスとは](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)