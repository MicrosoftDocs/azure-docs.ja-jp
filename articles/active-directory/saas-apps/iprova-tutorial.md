---
title: チュートリアル:Azure Active Directory と iProva の統合 | Microsoft Docs
description: Azure Active Directory と iProva の間でシングル サインオンを構成する方法について説明します。
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: 1eaeef9b-4479-4a9f-b1b2-bc13b857c75c
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 12/24/2018
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 395d3887e35d6e9c043d4d947b605e71eb58bd6b
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2019
ms.locfileid: "57897335"
---
# <a name="tutorial-azure-active-directory-integration-with-iprova"></a>チュートリアル:Azure Active Directory と iProva の統合

このチュートリアルでは、iProva と Azure Active Directory (Azure AD) を統合する方法について説明します。
iProva と Azure AD の統合には、次の利点があります。

* iProva にアクセスできる Azure AD ユーザーを制御できます。
* ユーザーが自分の Azure AD アカウントを使用して iProva に自動的にサインイン (シングル サインオン) できるようにすることができます。
* 1 つの中央サイト (Azure Portal) でアカウントを管理できます。

サービスとしてのソフトウェア (SaaS) アプリと Azure AD との統合の詳細については、[Azure Active Directory によるアプリケーション アクセスとシングル サインオン](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)に関するページを参照してください。
Azure サブスクリプションをお持ちでない場合は、開始する前に[無料アカウントを作成](https://azure.microsoft.com/free/)してください。

## <a name="prerequisites"></a>前提条件

iProva と Azure AD の統合を構成するには、次のものが必要です。

* Azure AD サブスクリプション。 Azure AD 環境がない場合は、[Microsoft Azure](https://azure.microsoft.com/pricing/free-trial/) Web サイトで 1 か月の試用版を入手できます。
* iProva SSO が有効なサブスクリプション。

## <a name="scenario-description"></a>シナリオの説明

このチュートリアルでは、テスト環境で Azure AD のシングル サインオンを構成してテストします。

* iProva では、SP によって開始される SSO がサポートされます。

## <a name="add-iprova-from-the-gallery"></a>ギャラリーからの iProva の追加

Azure AD への iProva の統合を構成するには、ギャラリーからマネージド SaaS アプリの一覧に iProva を追加します。

ギャラリーから iProva を追加するには、次の手順を実行します。

1. [Azure Portal](https://portal.azure.com) の左側のウィンドウで、**[Azure Active Directory]** を選択します。

    ![Azure Active Directory のボタン](common/select-azuread.png)

2. **[エンタープライズ アプリケーション]** に移動し、**[すべてのアプリケーション]** を選択します。

    ![[エンタープライズ アプリケーション] ブレード](common/enterprise-applications.png)

3. 新しいアプリケーションを追加するには、ダイアログ ボックスの上部の **[新しいアプリケーション]** を選択します。

    ![[新しいアプリケーション] ボタン](common/add-new-app.png)

4. 検索ボックスに「**iProva**」と入力します。 結果パネルから **[iProva]** を選択し、**[追加]** を選択してアプリケーションを追加します。

     ![結果一覧の iProva](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成とテスト

このセクションでは、Britta Simon というテスト ユーザーに基づいて、iProva で Azure AD のシングル サインオンを構成し、テストします。
シングル サインオンを機能させるには、Azure AD ユーザーと iProva 内の関連ユーザーとの間にリンク関係を確立する必要があります。

iProva で Azure AD のシングル サインオンを構成してテストするには、次の手順を完了します。

- [iProva からの構成情報の取得](#retrieve-configuration-information-from-iprova) - 次の手順の準備として。
- [Azure AD シングル サインオンの構成](#configure-azure-ad-single-sign-on) - ユーザーがこの機能を使用できるようにします。
- [iProva シングル サインオンの構成](#configure-iprova-single-sign-on) - アプリケーション側でシングル サインオン設定を構成します。
- [Azure AD のテスト ユーザーの作成](#create-an-azure-ad-test-user) - Britta Simon で Azure AD のシングル サインオンをテストします。
- [Azure AD テスト ユーザーの割り当て](#assign-the-azure-ad-test-user) - Britta Simon が Azure AD シングル サインオンを使用できるようにします。
- [iProva のテスト ユーザーの作成](#create-an-iprova-test-user) - iProva で Britta Simon に対応するユーザーを用意し、Azure AD の Britta Simon にリンクさせます。
- [シングル サインオンのテスト](#test-single-sign-on) - 構成が機能するかどうかを確認します。

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

1. [Azure portal](https://portal.azure.com/) の **iProva** アプリケーション統合ページで、**[シングル サインオン]** を選択します。

    ![シングル サインオン構成のリンク](common/select-sso.png)

2. **[シングル サインオン方式の選択]** ダイアログ ボックスで、**[SAML/WS-Fed]** モードを選択して、シングル サインオンを有効にします。

    ![シングル サインオン選択モード](common/select-saml-option.png)

3. **[SAML でシングル サインオンをセットアップします]** ページで、**編集**アイコンを選択して **[基本的な SAML 構成]** ダイアログ ボックスを開きます。

    ![[基本的な SAML 構成] の編集アイコン](common/edit-urls.png)

4. **[基本的な SAML 構成]** セクションで、次の手順を実行します。

    a. **iProva の [SAML2 info]\(SAML2 の情報\)** ページで、**[EntityID]** というラベルの後にある **[Identifier]\(識別子\)** ボックスに値を入力します。 このページは他のブラウザー タブで開いたままです。

    b. **iProva の [SAML2 info]\(SAML2 の情報\)** ページで、**[Reply URL]\(応答 URL\)** というラベルの後にある **[Reply-URL]** ボックスに値を入力します。 このページは他のブラウザー タブで開いたままです。

    c. **iProva の [SAML2 info]\(SAML2 の情報\)** ページで、**[Sign-on URL]\(サインオン URL\)** というラベルの後にある **[Sign-on URL]\(サインオン URL\)** ボックスに値を入力します。 このページは他のブラウザー タブで開いたままです。

    ![[iProva のドメインと URL] のシングル サインオン情報](common/sp-identifier-reply.png)

5. iProva アプリケーションは、特定の形式の SAML アサーションを受け入れます。 このアプリケーションには、次の要求を構成します。 これらの属性の値は、アプリケーション統合ページの **[ユーザー属性]** セクションで管理できます。 **[SAML でシングル サインオンをセットアップします]** ページで、**編集**アイコンを選択して **[ユーザー属性]** ダイアログ ボックスを開きます。

    ![[ユーザー属性] ダイアログ ボックス](common/edit-attribute.png)

6. **[ユーザー属性]** ダイアログ ボックスの **[ユーザーの要求]** セクションで、前の画像で示されているように、SAML トークン属性を構成します。 次の手順に従います。

    | Name | ソース属性| 名前空間 |
    | ---------------| -------- | -----|
    | `samaccountname` | `user.onpremisessamaccountname`| `http://schemas.xmlsoap.org/ws/2005/05/identity/claims`|
    | | |

    a. **[新しい要求の追加]** を選択して **[ユーザー要求の管理]** ダイアログ ボックスを開きます。

    ![ユーザーの要求](common/new-save-attribute.png)

    ![[ユーザー要求の管理] ダイアログ ボックス](common/new-attribute-details.png)

    b. **[名前]** ボックスに、その行に表示される属性名を入力します。

    c. **[名前空間]** 一覧から、その行に表示される名前空間の値を入力します。

    d. **[ソース]** オプションで **[属性]** を選択します。

    e. **[ソース属性]** の一覧から、その行に表示される属性値を入力します。

    f. **[OK]** を選びます。

    g. **[保存]** を選択します。

7. **[SAML でシングル サインオンをセットアップします]** ページの **[SAML 署名証明書]** セクションで、**コピー** アイコンを選択して **[アプリのフェデレーション メタデータ URL]** をコピーし、コンピューターに保存します。

    ![証明書のダウンロードのリンク](common/copy-metadataurl.png)

### <a name="configure-iprova-single-sign-on"></a>iProva のシングル サインオンの構成

1. **Administrator** アカウントを使用して iProva にサインインします。

2. **[Go to]\(移動\)** メニューを開きます。

3. **[Application management]\(アプリケーション管理\)** を選択します。

4. **[System settings]\(システム設定\)** パネルの **[General]\(全般\)** を選択します。

5. **[編集]** を選択します。

6. **[Access control]\(アクセス制御\)** まで下にスクロールします。

    ![iProva のアクセス制御設定](media/iprova-tutorial/iprova-accesscontrol.png)

7. **[Users are automatically logged on with their network accounts]\(ユーザーのネットワーク アカウントを使用して自動的にログオンする\)** 設定を探し、**[Yes, authentication via SAML]\(はい、SAML 経由で認証します\)** に変更します。 これで、追加のオプションが表示されます。

8. **[Set up]\(セットアップ\)** を選択します。

9. **[次へ]** を選択します。

10. iProva によって、URL からフェデレーション データをダウンロードするか、それともファイルからアップロードするかを問われます。 **[from URL]\(URL から\)** オプションを選択します。

    ![Azure AD メタデータをダウンロードする](media/iprova-tutorial/iprova-download-metadata.png)

11. 「Azure AD シングル サインオンの構成」セクションの最後の手順で保存したメタデータ URL を貼り付けます。

12. 矢印形のボタンを選択して、Azure AD からメタデータをダウンロードします。

13. ダウンロードが完了したら、**Valid Federation Data file downloaded (有効なフェデレーション データ ファイルがダウンロードされました)** という確認メッセージが表示されます。

14. **[次へ]** を選択します。

15. ここでは **[Test login]\(テスト ログイン\)** オプションをスキップし、**[Next]\(次へ\)** を選択します。

16. **[Claim to use]\(使用する要求\)** ドロップダウン ボックスで **[windowsaccountname]** を選択します。

17. **[完了]** を選択します。

18. これで、**[Edit general settings]\(全般設定の編集\)** 画面に戻ります。 ページの下部までスクロールし、**[OK]** を選択して自分の構成を保存します。

### <a name="create-an-azure-ad-test-user"></a>Azure AD のテスト ユーザーの作成

このセクションでは、Azure portal で Britta Simon というテスト ユーザーを作成します。

1. Azure portal の左側のウィンドウで、**[Azure Active Directory]** > **[ユーザー]** > **[すべてのユーザー]** の順に選択します。

    ![[ユーザーとグループ] と [すべてのユーザー] リンク](common/users.png)

2. 画面の上部にある **[新しいユーザー]** を選択します。

    ![[新しいユーザー] ボタン](common/new-user.png)

3. **[ユーザー]** ダイアログ ボックスで、次の手順に従います。

    ![[ユーザー] ダイアログ ボックス](common/user-properties.png)

    a. **[名前]** ボックスに「**BrittaSimon**」などの名前を入力します。
  
    b. **[ユーザー名]** ボックスに、「*yourname\@yourcompanydomain.extension*」と入力します。 
    例: BrittaSimon@contoso.com。

    c. **[パスワードを表示]** チェック ボックスをオンにし、**[パスワード]** ボックスに表示された値を書き留めます。

    d. **作成**を選択します。

### <a name="assign-the-azure-ad-test-user"></a>Azure AD テスト ユーザーの割り当て

このセクションでは、Britta Simon に iProva へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

1. Azure portal で、**[エンタープライズ アプリケーション]** > **[すべてのアプリケーション]** > **[iProva]** の順に選択します。

    ![[エンタープライズ アプリケーション] ブレード](common/enterprise-applications.png)

2. アプリケーションの一覧で **[iProva]** を選択します。

    ![アプリケーションの一覧の iProva リンク](common/all-applications.png)

3. 左側のメニューで **[ユーザーとグループ]** を選びます。

    ![[ユーザーとグループ] リンク](common/users-groups-blade.png)

4. **[ユーザーの追加]** を選択し、**[割り当ての追加]** ダイアログ ボックスで **[ユーザーとグループ]** を選択します。

    ![[割り当ての追加] ダイアログ ボックス](common/add-assign-user.png)

5. **[ユーザーとグループ]** ダイアログ ボックスの **[ユーザー]** の一覧で **[Britta Simon]** を選択し、画面の下部にある **[選択]** を選択します。

6. SAML アサーション内に任意のロール値が必要な場合、**[ロールの選択]** ダイアログ ボックスで、一覧からユーザーに適したロールを選択します。 画面の下部にある **[選択]** を選択します。

7. **[割り当ての追加]** ダイアログ ボックスで **[割り当て]** を選びます。

### <a name="create-an-iprova-test-user"></a>iProva テスト ユーザーの作成

1. **Administrator** アカウントを使用して iProva にサインインします。

2. **[Go to]\(移動\)** メニューを開きます。

3. **[Application management]\(アプリケーション管理\)** を選択します。

4. **[Users and user groups]\(ユーザーとユーザー グループ\)** パネルの **[Users]\(ユーザー\)** を選択します。

5. **[追加]** を選択します。

6. **[ユーザー名]** ボックスに、「 *brittasimon\@yourcompanydomain.extension*」と入力します。 
    例: BrittaSimon@contoso.com。

7. **[Full name]\(フル ネーム\)** ボックスに「**BrittaSimon**」などのフル ネームを入力します。

8. **[No password (use single sign-on)]\(パスワードなし (シングル サインオンを使用する)\)** オプションを選択します。

9. **[電子メール アドレス]** ボックスに、「*yourname\@yourcompanydomain.extension*」と入力します。 
   例: BrittaSimon@contoso.com。

10. ページの一番下までスクロールし、**[Finish]\(完了\)** を選択します。

### <a name="test-single-sign-on"></a>シングル サインオンのテスト

このセクションでは、アクセス パネルを使用して Azure AD のシングル サインオン構成をテストします。

アクセス パネル上で iProva タイルを選択すると、SSO を設定した iProva に自動的にサインインします。 アクセス パネルの詳細については、[アクセス パネルの概要](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)に関する記事を参照してください。

## <a name="additional-resources"></a>その他のリソース

- [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)
- [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)
- [Azure Active Directory の条件付きアクセスとは](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
- [iProva - SAML2 シングル サインオンを構成する方法](https://webshare.iprova.nl/0wqwm45yn09f5poh/Document.aspx)
