---
title: チュートリアル:Azure Active Directory と Trello の統合 | Microsoft Docs
description: Azure Active Directory と Trello の間でシングル サインオンを構成する方法について説明します。
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: cd5ae365-9ed6-43a6-920b-f7814b993949
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 01/02/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 730ff5ff12f18d1f85b3ca53adb42fee41e19fb4
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/13/2019
ms.locfileid: "67088290"
---
# <a name="tutorial-azure-active-directory-integration-with-trello"></a>チュートリアル:Azure Active Directory と Trello の統合

このチュートリアルでは、Trello と Azure Active Directory (Azure AD) を統合する方法について説明します。
Trello と Azure AD の統合には、次の利点があります:

* Trello にアクセスする Azure AD ユーザーを制御できます。
* ユーザーが自分の Azure AD アカウントで Trello に自動的にサインイン (シングル サインオン) するように設定できます。
* 1 つの中央サイト (Azure ポータル) でアカウントを管理できます。

SaaS アプリと Azure AD の統合の詳細については、[Azure Active Directory を使用したアプリケーション アクセスとシングル サインオンの概要](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)に関する記事をご覧ください。
Azure サブスクリプションをお持ちでない場合は、開始する前に[無料アカウントを作成](https://azure.microsoft.com/free/)してください。

## <a name="prerequisites"></a>前提条件

Trello と Azure AD の統合を構成するには、次のものが必要です:

* Azure AD サブスクリプション。 Azure AD 環境がない場合は、[1 か月の試用版](https://azure.microsoft.com/pricing/free-trial/)を入手できます。
* Trello でのシングル サインオンが有効なサブスクリプション。

## <a name="scenario-description"></a>シナリオの説明

このチュートリアルでは、テスト環境で Azure AD のシングル サインオンを構成してテストします。

* Trello では、SP と IDP によって開始される SSO がサポートされます

* Trello では、Just In Time ユーザー プロビジョニングがサポートされます

## <a name="add-trello-from-the-gallery"></a>ギャラリーから Trello を追加する

Azure AD への Trello の統合を構成するには、まずギャラリーから管理対象 SaaS アプリの一覧に Trello を追加する必要があります。

ギャラリーから Trello を追加するには、次の手順に従います。

1. [Azure Portal](https://portal.azure.com) の左側のウィンドウで、**Azure Active Directory** アイコンを選択します。

    ![Azure Active Directory のボタン](common/select-azuread.png)

2. **[エンタープライズ アプリケーション]** を選択し、 **[すべてのアプリケーション]** を選択します。

    ![[エンタープライズ アプリケーション] ブレード](common/enterprise-applications.png)

3. 新しいアプリケーションを追加するには、ダイアログ ボックスの上部にある **[新しいアプリケーション]** ボタンを選びます。

    ![[新しいアプリケーション] ボタン](common/add-new-app.png)

4. 検索ボックスに「**Trello**」と入力し、結果ウィンドウから **[Trello]** を選択します。

5. **[追加]** をクリックしてアプリケーションを追加します。

     ![結果一覧の Trello](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成とテスト

このセクションでは、**Britta Simon** というテスト ユーザーに基づいて、Trello で Azure AD のシングル サインオンを構成し、テストします。

シングル サインオンを機能させるには、Azure AD ユーザーと Trello 内の関連ユーザーとの間にリンクを確立する必要があります。

Trello で Azure AD のシングル サインオンを構成してテストするには、次の構成要素を完了する必要があります:

1. [Azure AD シングル サインオンの構成](#configure-azure-ad-single-sign-on) - ユーザーがこの機能を使用できるようにします。
2. [Trello のシングル サインオンの構成](#configure-trello-single-sign-on) - アプリケーション側でシングル サインオン設定を構成します。
3. [Azure AD のテスト ユーザーの作成](#create-an-azure-ad-test-user) - Britta Simon で Azure AD のシングル サインオンをテストします。
4. [Azure AD テスト ユーザーの割り当て](#assign-the-azure-ad-test-user) - Britta Simon が Azure AD シングル サインオンを使用できるようにします。
5. [Trello テスト ユーザーの作成](#create-a-trello-test-user) - Trello で Britta Simon に対応するユーザーを作成し、Azure AD のこのユーザーにリンクします。
6. [シングル サインオンのテスト](#test-single-sign-on)。構成が機能することを確認します。

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成

このセクションでは、Azure portal 上で Azure AD のシングル サインオンを有効にします。

> [!NOTE]
> Trello から **\<enterprise\>** スラグを取得する必要があります。 スラグ値を取得していない場合は、[Trello サポート チーム](mailto:support@trello.com)に問い合わせて、自社のスラグを取得してください。

Trello で Azure AD シングル サインオンを構成するには、次の手順に従います。

1. [Azure portal](https://portal.azure.com/) の **Trello** アプリケーション統合ページで、 **[シングル サインオン]** を選択します。

    ![シングル サインオン構成のリンク](common/select-sso.png)

2. **[シングル サインオン方式の選択]** ダイアログ ボックスで、 **[SAML]** を選択して、シングル サインオンを有効にします。

    ![シングル サインオン選択モード](common/select-saml-option.png)

3. **[SAML でシングル サインオンをセットアップします]** ページで、**編集**アイコンを選択して **[基本的な SAML 構成]** ダイアログ ボックスを開きます。

    ![基本的な SAML 構成を編集する](common/edit-urls.png)

4. **[基本的な SAML 構成]** セクションで、アプリケーションを IDP 開始モードで構成する場合は、次の手順を実行します。

    ![[Trello のドメインと URL] のシングル サインオン情報](common/idp-intiated.png)

    a. **[識別子]** ボックスに、`https://trello.com/auth/saml/metadata` という形式で URL を入力します。

    b. **[応答 URL]** ボックスに、`https://trello.com/auth/saml/consume/<enterprise>` という形式で URL を入力します。

5. アプリケーションを SP 開始モードで構成する場合は、 **[追加の URL を設定します]** を選択して次の手順を実行します。

    ![[Trello のドメインと URL] のシングル サインオン情報](common/metadata-upload-additional-signon.png)

    **[サインオン URL]** ボックスに、`https://trello.com/auth/saml/login/<enterprise>` という形式で URL を入力します。

    > [!NOTE]
    > これらは実際の値ではありません。 実際の識別子、応答 URL、サインオン URL でこれらの値を更新します。 これらの値を取得するには、[Trello クライアント サポート チーム](mailto:support@trello.com)にお問い合わせください。 Azure portal の **[基本的な SAML 構成]** セクションのパターンを参照することもできます。

6. Trello アプリケーションは、特定の形式で構成された SAML アサーションを受け入れます。 このアプリケーションには、次の要求を構成します。 これらの属性の値は、アプリケーション統合ページの **[ユーザー属性]** セクションで管理できます。 **[SAML でシングル サインオンをセットアップします]** ページで、 **[編集]** ボタンを選択して **[ユーザー属性]** ダイアログ ボックスを開きます。

    ![[ユーザー属性] ダイアログ ボックス](common/edit-attribute.png)

7. **[ユーザー属性]** ダイアログ ボックスの **[ユーザーの要求]** セクションで、前の画像で示されているように、SAML トークン属性を構成します。 その後、次の手順を実行します。

    | Name |  ソース属性|
    | --- | --- |
    | User.Email | User.mail |
    | User.FirstName | User.givenname |
    | User.LastName | User.surname |

    a. **[新しい要求の追加]** を選択して **[ユーザー要求の管理]** ダイアログ ボックスを開きます。

    ![ユーザー要求のダイアログ ボックス](common/new-save-attribute.png)

    ![ユーザー要求の管理](common/new-attribute-details.png)

    b. **[名前]** ボックスに、その行に対して表示される属性名を入力します。

    c. **[名前空間]** は空白のままにします。

    d. **[ソース]** で **[属性]** を選択します。

    e. **[ソース属性]** の一覧で、その行に表示される属性値を入力します。

    f. **[OK]** を選びます。

    g. **[保存]** を選択します。

8. **[SAML でシングル サインオンをセットアップします]** ページの **[SAML 署名証明書]** セクションで **[ダウンロード]** を選択して、要件に適した特定のオプションの**証明書 (Base64)** をダウンロードします。 その後、ご自分のコンピューター上に保存します。

    ![証明書のダウンロードのリンク](common/certificatebase64.png)

9. **[Set up Trello]\(Trello の設定\)** セクションで、要件に従って適切な URL をコピーします。

    ![構成 URL のコピー](common/copy-configuration-urls.png)

    a. ログイン URL

    b. Azure AD 識別子

    c. ログアウト URL

### <a name="configure-trello-single-sign-on"></a>Trello のシングル サインオンの構成

Trello 側でシングル サインオンを構成するには、まず**ダウンロードした証明書 (Base64)** と Azure portal からコピーした URL を [Trello サポート チーム](mailto:support@trello.com)に送信します。 SAML シングル サインオン接続が両方の側で正しく設定されていることが確認されます。

### <a name="create-an-azure-ad-test-user"></a>Azure AD のテスト ユーザーの作成 

このセクションの目的は、Azure Portal で Britta Simon というテスト ユーザーを作成することです。

1. Azure portal の左側のウィンドウで、 **[Azure Active Directory]** 、 **[ユーザー]** 、 **[すべてのユーザー]** の順に選択します。

    ![[ユーザーとグループ] と [すべてのユーザー] リンク](common/users.png)

2. 画面の上部にある **[新しいユーザー]** を選択します。

    ![[新しいユーザー] ボタン](common/new-user.png)

3. **[ユーザー]** ダイアログ ボックスで、次の手順を実行します。

    ![[ユーザー] ダイアログ ボックス](common/user-properties.png)

    a. **[名前]** フィールドに「**BrittaSimon**」と入力します。
  
    b. **[ユーザー名]** フィールドに「brittasimon@yourcompanydomain.extension」と入力します。 たとえば、この場合は、「BrittaSimon@contoso.com」と入力できます。

    c. **[パスワードを表示]** チェック ボックスをオンにし、 **[パスワード]** ボックスに表示された値を書き留めます。

    d. **作成** を選択します。

### <a name="assign-the-azure-ad-test-user"></a>Azure AD テスト ユーザーの割り当て

このセクションでは、Britta Simon に Trello へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

1. Azure portal で **[エンタープライズ アプリケーション]** を選択し、 **[すべてのアプリケーション]** 、**Trello** の順に選択します。

    ![Enterprise Applications blade](common/enterprise-applications.png)

2. アプリケーションの一覧で **[Trello]** を選択します。

    ![アプリケーションの一覧の [Trello] リンク](common/all-applications.png)

3. 左側のメニューで **[ユーザーとグループ]** を選びます。

    ![[ユーザーとグループ] リンク](common/users-groups-blade.png)

4. **[ユーザーの追加]** ボタンを選択します。 次に、 **[割り当ての追加]** ダイアログ ボックスで **[ユーザーとグループ]** を選択します。

    ![[割り当ての追加] ウィンドウ](common/add-assign-user.png)

5. **[ユーザーとグループ]** ダイアログ ボックスで、ユーザーの一覧から **[Britta Simon]** を選択します。 次に、画面の下部にある **[選択]** ボタンをクリックします。

6. SAML アサーション内にロール値が必要な場合、 **[ロールの選択]** ダイアログ ボックスで、一覧からユーザーに適したロールを選択します。 次に、画面の下部にある **[選択]** ボタンをクリックします。

7. **[割り当ての追加]** ダイアログ ボックスで、 **[割り当て]** ボタンを選択します。

### <a name="create-a-trello-test-user"></a>Trello テスト ユーザーを作成する

このセクションでは、Trello で Britta Simon というユーザーを作成します。 Trello では、Just In Time ユーザー プロビジョニングがサポートされています。この設定は既定で有効になっています。 このセクションでは、ユーザー側で必要な操作はありません。 Trello にユーザーがまだ存在していない場合は、認証後に新規に作成されます。

> [!NOTE]
> ユーザーを手動で作成する必要がある場合は、 [Trello のサポート チーム](mailto:support@trello.com)にお問い合わせください。

### <a name="test-single-sign-on"></a>シングル サインオンのテスト 

このセクションでは、MyApps ポータルを使用してご自分の Azure AD のシングル サインオン構成をテストします。

MyApps ポータル内で [Trello] タイルを選択すると、Trello に自動的にサインインします。 MyApps ポータルの詳細については、[MyApps ポータルの概要](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)に関するページを参照してください。

## <a name="additional-resources"></a>その他のリソース

- [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Azure Active Directory の条件付きアクセスとは](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

