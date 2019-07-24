---
title: チュートリアル:Azure Active Directory と dmarcian の統合 | Microsoft Docs
description: Azure Active Directory と dmarcian の間でシングル サインオンを構成する方法について説明します。
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: a04b9383-3a60-4d54-9412-123daaddff3b
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 01/30/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: c24cbf8ad21c7dd5875a71532a5278e313774e66
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2019
ms.locfileid: "57883969"
---
# <a name="tutorial-azure-active-directory-integration-with-dmarcian"></a>チュートリアル:Azure Active Directory と dmarcian の統合

このチュートリアルでは、dmarcian と Azure Active Directory (Azure AD) を統合する方法について説明します。
dmarcian と Azure AD の統合には、次の利点があります。

* dmarcian にアクセスできる Azure AD ユーザーを制御できます。
* ユーザーが自分の Azure AD アカウントを使用して dmarcian に自動的にサインイン (シングル サインオン) するように設定できます。
* 1 つの中央サイト (Azure Portal) でアカウントを管理できます。

SaaS アプリと Azure AD の統合の詳細については、「 [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)」を参照してください。
Azure サブスクリプションをお持ちでない場合は、開始する前に[無料アカウントを作成](https://azure.microsoft.com/free/)してください。

## <a name="prerequisites"></a>前提条件

dmarcian と Azure AD の統合を構成するには、次のものが必要です。

* Azure AD サブスクリプション。 Azure AD の環境がない場合は、[こちら](https://azure.microsoft.com/pricing/free-trial/)から 1 か月の評価版を入手できます
* dmarcian のシングル サインオンが有効なサブスクリプション

## <a name="scenario-description"></a>シナリオの説明

このチュートリアルでは、テスト環境で Azure AD のシングル サインオンを構成してテストします。

* dmarcian では、**SP** Initiated SSO と **IDP** Initiated SSO がサポートされます

## <a name="adding-dmarcian-from-the-gallery"></a>ギャラリーからの dmarcian の追加

Azure AD への dmarcian の統合を構成するに、ギャラリーから管理対象 SaaS アプリの一覧に dmarcian を追加する必要があります。

**ギャラリーから dmarcian を追加するには、次の手順を実行します。**

1. **[Azure Portal](https://portal.azure.com)** の左側のナビゲーション ウィンドウで、**[Azure Active Directory]** アイコンをクリックします。

    ![Azure Active Directory のボタン](common/select-azuread.png)

2. **[エンタープライズ アプリケーション]** に移動し、**[すべてのアプリケーション]** オプションを選択します。

    ![[エンタープライズ アプリケーション] ブレード](common/enterprise-applications.png)

3. 新しいアプリケーションを追加するには、ダイアログの上部にある **[新しいアプリケーション]** をクリックします。

    ![[新しいアプリケーション] ボタン](common/add-new-app.png)

4. 検索ボックスに「**dmarcian**」と入力し、結果パネルから **[dmarcian]** を選択してから、**[追加]** ボタンをクリックしてアプリケーションを追加します。

     ![結果一覧の dmarcian](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成とテスト

このセクションでは、**Britta Simon** というテスト ユーザーに基づいて、dmarcian で Azure AD のシングル サインオンを構成し、テストします。
シングル サインオンを機能させるには、Azure AD ユーザーと dmarcian 内の関連ユーザー間にリンク関係が確立されている必要があります。

dmarcian で Azure AD のシングル サインオンを構成してテストするには、次の構成要素を完了する必要があります。

1. **[Azure AD シングル サインオンの構成](#configure-azure-ad-single-sign-on)** - ユーザーがこの機能を使用できるようにします。
2. **[dmarcian シングル サインオンの構成](#configure-dmarcian-single-sign-on)** - アプリケーション側でシングル サインオン設定を構成します。
3. **[Azure AD のテスト ユーザーの作成](#create-an-azure-ad-test-user)** - Britta Simon で Azure AD のシングル サインオンをテストします。
4. **[Azure AD テスト ユーザーの割り当て](#assign-the-azure-ad-test-user)** - Britta Simon が Azure AD シングル サインオンを使用できるようにします。
5. **[dmarcian のテスト ユーザーの作成](#create-dmarcian-test-user)** - dmarcian で Britta Simon に対応するユーザーを作成し、Azure AD の Britta Simon にリンクさせます。
6. **[シングル サインオンのテスト](#test-single-sign-on)** - 構成が機能するかどうかを確認します。

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成

このセクションでは、Azure portal 上で Azure AD のシングル サインオンを有効にします。

dmarcian で Azure AD のシングル サインオンを構成するには、次の手順を実行します。

1. [Azure portal](https://portal.azure.com/) の **dmarcian** アプリケーション統合ページで、**[シングル サインオン]** を選択します。

    ![シングル サインオン構成のリンク](common/select-sso.png)

2. **[シングル サインオン方式の選択]** ダイアログで、**[SAML/WS-Fed]** モードを選択して、シングル サインオンを有効にします。

    ![シングル サインオン選択モード](common/select-saml-option.png)

3. **[SAML でシングル サインオンをセットアップします]** ページで、**[編集]** アイコンをクリックして **[基本的な SAML 構成]** ダイアログを開きます。

    ![基本的な SAML 構成を編集する](common/edit-urls.png)

4. **[基本的な SAML 構成]** セクションで、アプリケーションを **IDP** 開始モードで構成する場合は、次の手順を実行します。

    ![[dmarcian Domain and URLs] (dmarcian のドメインと URL) のシングル サインオン情報](common/idp-intiated.png)

    a. **[識別子]** ボックスに、次の形式で URL を入力します。

    | |
    | -- |
    | `https://us.dmarcian.com/sso/saml/<ACCOUNT_ID>/sp.xml` |
    | `https://dmarcian-eu.com/sso/saml/<ACCOUNT_ID>/sp.xml` |
    | `https://dmarcian-ap.com/sso/saml/<ACCOUNT_ID>/sp.xml` |

    b. **[応答 URL]** ボックスに、次のパターンを使用して URL を入力します。

    | |
    |--|
    | `https://us.dmarcian.com/login/<ACCOUNT_ID>/handle/` |
    | `https://dmarcian-eu.com/login/<ACCOUNT_ID>/handle/` |
    | `https://dmarcian-ap.com/login/<ACCOUNT_ID>/handle/` |

5. アプリケーションを **SP** 開始モードで構成する場合は、**[追加の URL を設定します]** をクリックして次の手順を実行します。

    ![[dmarcian Domain and URLs] (dmarcian のドメインと URL) のシングル サインオン情報](common/metadata-upload-additional-signon.png)

    **[サインオン URL]** ボックスに、次のパターンを使用して URL を入力します。
    
    | |
    |--|
    | `https://us.dmarcian.com/login/<ACCOUNT_ID>` |
    | `https://dmarcian-eu.com/login/<ACCOUNT_ID>` |
    | `https://dmarciam-ap.com/login/<ACCOUNT_ID>` |
     
    > [!NOTE] 
    > これらは実際の値ではありません。 これらの値は、実際の識別子、応答 URL、およびサインオン URL に更新します。実際の値については、このチュートリアルの後の方で説明します。 

6. **Set up Single Sign-On with SAML\(SAML でのシングルサインオンの設定** ページの **SAML 署名証明書** セクションで、コピー ボタンをクリックして **App Federation Metadata Url\(アプリのフェデレーション メタデータ URL)** をコピーして、コンピューターに保存します。

    ![証明書のダウンロードのリンク](common/copy-metadataurl.png)

### <a name="configure-dmarcian-single-sign-on"></a>dmarcian シングル サインオンの構成

1. 別の Web ブラウザー ウィンドウで、セキュリティ管理者として dmarcian にサインインします。

2. 右上隅にある **[プロファイル]** をクリックし、**[ユーザー設定]** に移動します。

    ![[ユーザー設定]](./media/dmarcian-tutorial/tutorial_dmarcian_pref.png)

3. 下へスクロールして **[シングル サインオン]** セクションをクリックしてから、**[構成]** をクリックします。

    ![シングル](./media/dmarcian-tutorial/tutorial_dmarcian_sso.png)

4. **[SAML シングル サインオン]** ページで、**[状態]** を **[有効]** に設定し、次の手順を実行します。

    ![認証](./media/dmarcian-tutorial/tutorial_dmarcian_auth.png)

    * **[Add dmarcian to your Identity Provider]\(ID プロバイダーへの dmarcian の追加\)** セクションで、**[コピー]** をクリックしてインスタンスの **[Assertion Consumer Service URL]** をコピーし、それを Azure portal の **[基本的な SAML 構成] セクション**にある **[応答 URL]** テキスト ボックスに貼り付けます。

    * **[Add dmarcian to your Identity Provider]\(ID プロバイダーへの dmarcian の追加\)** セクションで、**[コピー]** をクリックしてインスタンスの **[エンティティ ID]** をコピーし、それを Azure portal の **[基本的な SAML 構成] セクション**にある **[識別子]** テキスト ボックスに貼り付けます。

    * **[Set up Authentication] (認証の設定)** セクションの **[Identity Provider Metadata] (ID プロバイダーのメタデータ)** テキスト ボックスに、Azure Portal からコピーした **[アプリのフェデレーション メタデータ URL]** を貼り付けます。

    * **[Set up Authentication]\(認証の設定\)** セクションの **[Attribute Statements]\(属性ステートメント\)** テキスト ボックスに、URL `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress` を貼り付けます。

    * **[Set up Login URL]\(ログイン URL の設定\)** セクションで、インスタンスの **[ログイン URL]** をコピーし、それを Azure portal の **[基本的な SAML 構成] セクション**にある **[サインオン URL]** テキスト ボックスに貼り付けます。

        > [!Note]
        > **[ログイン URL]** は、組織に応じて変更できます。

    * **[Save]** をクリックします。

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

このセクションでは、Britta Simon に dmarcian へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

1. Azure portal 上で **[エンタープライズ アプリケーション]** を選択し、**[すべてのアプリケーション]** を選択してから、**[dmarcian]** を選択します。

    ![[エンタープライズ アプリケーション] ブレード](common/enterprise-applications.png)

2. アプリケーションの一覧で **[dmarcian]** を選択します。

    ![アプリケーションの一覧の [dmarcian] リンク](common/all-applications.png)

3. 左側のメニューで **[ユーザーとグループ]** を選びます。

    ![[ユーザーとグループ] リンク](common/users-groups-blade.png)

4. **[ユーザーの追加]** をクリックし、**[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。

    ![[割り当ての追加] ウィンドウ](common/add-assign-user.png)

5. **[ユーザーとグループ]** ダイアログの [ユーザー] の一覧から **[Britta Simon]** を選択し、画面の下部にある **[選択]** ボタンをクリックします。

6. SAML アサーション内に任意のロール値が必要な場合、**[ロールの選択]** ダイアログでユーザーに適したロールを一覧から選択し、画面の下部にある **[選択]** をクリッします。

7. **[割り当ての追加]** ダイアログで、**[割り当て]** をクリックします。

### <a name="create-dmarcian-test-user"></a>dmarcian のテスト ユーザーの作成

Azure AD ユーザーが dmarcian にサインインできるようにするには、ユーザーを dmarcian にプロビジョニングする必要があります。 dmarcian では、プロビジョニングは手動のタスクです。

**ユーザー アカウントをプロビジョニングするには、次の手順に従います。**

1. セキュリティ管理者として dmarcian にサインインします。

2. 右上隅にある **[プロファイル]** をクリックし、**[ユーザーの管理]** に移動します。

    ![ユーザー](./media/dmarcian-tutorial/tutorial_dmarcian_user.png)

3. **[SSO Users] (SSO ユーザー)** セクションの右側で、**[新しいユーザーの追加]** をクリックします。

    ![ユーザーの追加](./media/dmarcian-tutorial/tutorial_dmarcian_addnewuser.png)

4. **[新しいユーザーの追加]** ポップアップで、次の手順を実行します。

    ![新しいユーザー](./media/dmarcian-tutorial/tutorial_dmarcian_save.png)

    a. **[New User Email]\(新しいユーザーの電子メール\)** ボックスに、ユーザーのメール アドレス (**brittasimon\@contoso.com** など) を入力します。

    b. そのユーザーに管理者権限を付与する場合は、**[Make User an Admin] (ユーザーを管理者にする)** を選択します。

    c. **[ユーザーの追加]** をクリックします。

### <a name="test-single-sign-on"></a>シングル サインオンのテスト 

このセクションでは、アクセス パネルを使用して Azure AD のシングル サインオン構成をテストします。

アクセス パネルで [dmarcian] タイルをクリックすると、SSO を設定した dmarcian に自動的にサインインします。 アクセス パネルの詳細については、[アクセス パネルの概要](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)に関する記事を参照してください。

## <a name="additional-resources"></a>その他のリソース

- [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Azure Active Directory の条件付きアクセスとは](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

