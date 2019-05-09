---
title: チュートリアル:Azure Active Directory と Way We Do の統合 | Microsoft Docs
description: Azure Active Directory と Way We Do の間でシングル サインオンを構成する方法について説明します。
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 84fc4f36-ecd1-42c6-8a70-cb0f3dc15655
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 04/03/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 541be5466b65705daa0485976eab3df8eb3d707f
ms.sourcegitcommit: b8a8d29fdf199158d96736fbbb0c3773502a092d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/15/2019
ms.locfileid: "59565534"
---
# <a name="tutorial-azure-active-directory-integration-with-way-we-do"></a>チュートリアル:Azure Active Directory と Way We Do の統合

このチュートリアルでは、Way We Do と Azure Active Directory (Azure AD) を統合する方法について説明します。
Way We Do と Azure AD の統合には、次の利点があります。

* Way We Do にアクセスできる Azure AD ユーザーを制御できます。
* ユーザーが自分の Azure AD アカウントを使用して Way We Do に自動的にサインイン (シングル サインオン) できるようにすることができます。
* 1 つの中央サイト (Azure Portal) でアカウントを管理できます。

SaaS アプリと Azure AD の統合の詳細については、「 [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)」を参照してください。
Azure サブスクリプションをお持ちでない場合は、開始する前に[無料アカウントを作成](https://azure.microsoft.com/free/)してください。

## <a name="prerequisites"></a>前提条件

Way We Do と Azure AD の統合を構成するには、次のものが必要です。

* Azure AD サブスクリプション。 Azure AD の環境がない場合は、[無料アカウント](https://azure.microsoft.com/free/)を取得できます
* Way We Do でのシングル サインオンが有効なサブスクリプション

## <a name="scenario-description"></a>シナリオの説明

このチュートリアルでは、テスト環境で Azure AD のシングル サインオンを構成してテストします。

* Way We Do では、**SP** によって開始される SSO がサポートされます

* Way We Do では、**Just-In-Time** ユーザー プロビジョニングがサポートされます

## <a name="adding-way-we-do-from-the-gallery"></a>ギャラリーからの Way We Do の追加

Azure AD への Way We Do の統合を構成するには、ギャラリーからマネージド SaaS アプリの一覧に Way We Do を追加する必要があります。

**ギャラリーから Way We Do を追加するには、次の手順を行います。**

1. **[Azure Portal](https://portal.azure.com)** の左側のナビゲーション ウィンドウで、**[Azure Active Directory]** アイコンをクリックします。

    ![Azure Active Directory のボタン](common/select-azuread.png)

2. **[エンタープライズ アプリケーション]** に移動し、**[すべてのアプリケーション]** オプションを選択します。

    ![[エンタープライズ アプリケーション] ブレード](common/enterprise-applications.png)

3. 新しいアプリケーションを追加するには、ダイアログの上部にある **[新しいアプリケーション]** をクリックします。

    ![[新しいアプリケーション] ボタン](common/add-new-app.png)

4. 検索ボックスに「**Way We Do**」と入力し、結果パネルで **[Way We Do]** を選び、**[追加]** をクリックして、アプリケーションを追加します。

    ![結果一覧の Way We Do](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成とテスト

このセクションでは、**Britta Simon** というテスト ユーザーに基づいて、Way We Do で Azure AD のシングル サインオンを構成し、テストします。
シングル サインオンを機能させるには、Azure AD ユーザーと Way We Do 内の関連ユーザー間にリンク関係が確立されている必要があります。

Way We Do で Azure AD のシングル サインオンを構成してテストするには、次の構成要素を完了する必要があります。

1. **[Azure AD シングル サインオンの構成](#configure-azure-ad-single-sign-on)** - ユーザーがこの機能を使用できるようにします。
2. **[Way We Do シングル サインオンの構成](#configure-way-we-do-single-sign-on)** - アプリケーション側でシングル サインオン設定を構成します。
3. **[Azure AD のテスト ユーザーの作成](#create-an-azure-ad-test-user)** - Britta Simon で Azure AD のシングル サインオンをテストします。
4. **[Azure AD テスト ユーザーの割り当て](#assign-the-azure-ad-test-user)** - Britta Simon が Azure AD シングル サインオンを使用できるようにします。
5. **[Way We Do テスト ユーザーの作成](#create-way-we-do-test-user)** - Azure AD の Britta Simon にリンクさせるために、対応するユーザーを Way We Do で作成します。
6. **[シングル サインオンのテスト](#test-single-sign-on)** - 構成が機能するかどうかを確認します。

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成

このセクションでは、Azure portal 上で Azure AD のシングル サインオンを有効にします。

Way We Do で Azure AD シングル サインオンを構成するには、次の手順に従います。

1. [Azure portal](https://portal.azure.com/) の **Way We Do** アプリケーション統合ページで、**[シングル サインオン]** を選択します。

    ![シングル サインオン構成のリンク](common/select-sso.png)

2. **[シングル サインオン方式の選択]** ダイアログで、**[SAML/WS-Fed]** モードを選択して、シングル サインオンを有効にします。

    ![シングル サインオン選択モード](common/select-saml-option.png)

3. **[SAML でシングル サインオンをセットアップします]** ページで、**[編集]** アイコンをクリックして **[基本的な SAML 構成]** ダイアログを開きます。

    ![基本的な SAML 構成を編集する](common/edit-urls.png)

4. **[基本的な SAML 構成]** セクションで、次の手順を実行します。

    ![[Way We Do のドメインと URL] のシングル サインオン情報](common/sp-identifier.png)

    a. **[サインオン URL]** ボックスに、次のパターンを使用して URL を入力します。`https://<SUBDOMAIN>.waywedo.com/Authentication/ExternalSignIn`

    b. **[識別子 (エンティティ ID)]** ボックスに、次のパターンを使用して URL を入力します。`https://<SUBDOMAIN>.waywedo.com`

    > [!NOTE]
    > これらは実際の値ではありません。 実際のサインオン URL と識別子でこれらの値を更新します。 この値を取得するには、[Way We Do クライアント サポート チーム](mailto:support@waywedo.com)にお問い合わせください。 Azure portal の **[基本的な SAML 構成]** セクションに示されているパターンを参照することもできます。

5. **[SAML でシングル サインオンをセットアップします]** ページの **[SAML 署名証明書]** セクションで、**[ダウンロード]** をクリックして要件のとおりに指定したオプションからの**証明書 (未加工)** をダウンロードして、お使いのコンピューターに保存します。

    ![証明書のダウンロードのリンク](common/certificateraw.png)

6. **[Way We Do のセットアップ]** セクションで、要件に従って適切な URL をコピーします。

    ![構成 URL のコピー](common/copy-configuration-urls.png)

    a. ログイン URL

    b. Azure AD 識別子

    c. ログアウト URL

### <a name="configure-way-we-do-single-sign-on"></a>Way We Do シングル サインオンの構成

1. 異なる Web ブラウザー ウィンドウで、セキュリティ管理者として Way We Do にサインインします。

2. Way We Do の任意のページで右上隅にある**人アイコン**をクリックし、ドロップダウン メニューで **[Account]\(アカウント\)** をクリックします。

    ![Way We Do アカウント](./media/waywedo-tutorial/tutorial_waywedo_account.png) 

3. **メニュー アイコン**をクリックしてプッシュ ナビゲーション メニューを開き、**[Single Sign On]\(シングル サイン オン\)** をクリックします。

    ![Way We Do シングル](./media/waywedo-tutorial/tutorial_waywedo_single.png)

4. **[Single sign-on setup]\(シングル サイン オンの設定\)** ページで、次の手順を行います。

    ![Way We Do 保存](./media/waywedo-tutorial/tutorial_waywedo_save.png)

    a. **[Turn on single sign-on]\(シングル サイン オンの有効化\)** トグルをクリックして、**[Yes]\(はい\)** に切り替えて、シングル サインオンを有効にします。

    b. **[Single sign-on name]\(シングル サイン オン名\)** テキストボックスに、自分の名前を入力します。

    c. **[Entity ID]\(エンティティ ID\)** ボックスに、Azure portal からコピーした **Azure AD 識別子**の値を貼り付けます。

    d. **[SAML SSO URL]** ボックスに、Azure portal からコピーした**ログイン URL** の値を貼り付けます。

    e. **[Certificate]\(証明書\)** の横にある**選択**ボタンをクリックして、証明書をアップロードします。

    f. **オプション設定** -
    
    * Enable Passwords (パスワードを有効にする) - このオプションを有効にすると、ユーザーがシングル サインオンのみを使用できるように通常のパスワードが Way We Do に対して機能します。

    * Enable Auto-provisioning (自動プロビジョニングを有効にする) - このオプションを有効にすると、サインオンに使用されている電子メール アドレスが Way We Do 内のユーザー リストと自動的に比較されます。 電子メール アドレスが Way We Do 内のアクティブ ユーザーと一致しない場合は、サインインするユーザーに対して新しいユーザー アカウントが自動的に追加され、不足している情報があれば要求されます。

      > [!NOTE]
      > シングル サインオンを介して追加されたユーザーは、一般ユーザーとして追加され、システムでのロールは割り当てられません。 管理者は編集者または管理者としてセキュリティ ロールにアクセスし、それを変更することができると共に、1 つまたは複数の Org Chart ロールを割り当てることもできます。 

    g. **[Save]\(保存\)** をクリックして設定を保持します。

### <a name="create-an-azure-ad-test-user"></a>Azure AD のテスト ユーザーの作成 

このセクションの目的は、Azure Portal で Britta Simon というテスト ユーザーを作成することです。

1. Azure portal の左側のウィンドウで、**[Azure Active Directory]**、**[ユーザー]**、**[すべてのユーザー]** の順に選択します。

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

このセクションでは、Britta Simon に Way We Do へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

1. Azure portal で **[エンタープライズ アプリケーション]** を選択し、**[すべてのアプリケーション]** を選択してから、**[Way We Do]** を選択します。

    ![[エンタープライズ アプリケーション] ブレード](common/enterprise-applications.png)

2. アプリケーションの一覧で **[Way We Do]** を選択します。

    ![アプリケーションの一覧の [Way We Do] リンク](common/all-applications.png)

3. 左側のメニューで **[ユーザーとグループ]** を選びます。

    ![[ユーザーとグループ] リンク](common/users-groups-blade.png)

4. **[ユーザーの追加]** をクリックし、**[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。

    ![[割り当ての追加] ウィンドウ](common/add-assign-user.png)

5. **[ユーザーとグループ]** ダイアログの [ユーザー] の一覧で **[Britta Simon]** を選択し、画面の下部にある **[選択]** ボタンをクリックします。

6. SAML アサーション内に任意のロール値が必要な場合、**[ロールの選択]** ダイアログでユーザーに適したロールを一覧から選択し、画面の下部にある **[選択]** をクリッします。

7. **[割り当ての追加]** ダイアログで、**[割り当て]** ボタンをクリックします。

### <a name="create-way-we-do-test-user"></a>Way We Do テスト ユーザーの作成

このセクションでは、Britta Simon というユーザーを Way We Do に作成します。 Way We Do では、Just-In-Time ユーザー プロビジョニングがサポートされています。この設定は既定で有効になっています。 このセクションでは、ユーザー側で必要な操作はありません。 Way We Do にユーザーがまだ存在していない場合は、認証後に新規に作成されます。

> [!Note]
> ユーザーを手動で作成する必要がある場合は、[Way We Do クライアント サポート チーム](mailto:support@waywedo.com)にお問い合わせください。

### <a name="test-single-sign-on"></a>シングル サインオンのテスト 

このセクションでは、アクセス パネルを使用して Azure AD のシングル サインオン構成をテストします。

アクセス パネルで [Way We Do] タイルをクリックすると、SSO を設定した Way We Do に自動的にサインインします。 アクセス パネルの詳細については、[アクセス パネルの概要](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)に関する記事を参照してください。

## <a name="additional-resources"></a>その他のリソース

- [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Azure Active Directory の条件付きアクセスとは](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

