---
title: チュートリアル:Azure Active Directory と CorpTax の統合 | Microsoft Docs
description: Azure Active Directory と CorpTax の間でシングル サインオンを構成する方法について説明します。
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: fdad2893-41f4-4056-b6d2-ec3d050eb350
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 12/17/2018
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: f150522c8ea4d3bcb69b7ffe274f1d40d831ab2b
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2019
ms.locfileid: "57864917"
---
# <a name="tutorial-azure-active-directory-integration-with-corptax"></a>チュートリアル:Azure Active Directory と CorpTax の統合

このチュートリアルでは、CorpTax と Azure Active Directory (Azure AD) を統合する方法について説明します。
CorpTax と Azure AD の統合には、次の利点があります。

* CorpTax にアクセスできる Azure AD ユーザーを制御できます。
* ユーザーが自分の Azure AD アカウントを使用して CorpTax に自動的にサインイン (シングル サインオン) できるようにすることができます。
* 1 つの中央サイト (Azure Portal) でアカウントを管理できます。

SaaS アプリと Azure AD の統合の詳細については、「 [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)」を参照してください。
Azure サブスクリプションをお持ちでない場合は、開始する前に[無料アカウントを作成](https://azure.microsoft.com/free/)してください。

## <a name="prerequisites"></a>前提条件

CorpTax と Azure AD の統合を構成するには、次のものが必要です。

* Azure AD サブスクリプション。 Azure AD の環境がない場合は、[こちら](https://azure.microsoft.com/pricing/free-trial/)から 1 か月の評価版を入手できます
* CorpTax でのシングル サインオンが有効なサブスクリプション

## <a name="scenario-description"></a>シナリオの説明

このチュートリアルでは、テスト環境で Azure AD のシングル サインオンを構成してテストします。

* CorpTax では、**SP** によって開始される SSO がサポートされます

## <a name="adding-corptax-from-the-gallery"></a>ギャラリーからの CorpTax の追加

Azure AD への CorpTax の統合を構成するには、ギャラリーから管理対象 SaaS アプリの一覧に CorpTax を追加する必要があります。

**ギャラリーから CorpTax を追加するには、次の手順に従います。**

1. **[Azure Portal](https://portal.azure.com)** の左側のナビゲーション ウィンドウで、**[Azure Active Directory]** アイコンをクリックします。

    ![Azure Active Directory のボタン](common/select_azuread.png)

2. **[エンタープライズ アプリケーション]** に移動し、**[すべてのアプリケーション]** オプションを選択します。

    ![[エンタープライズ アプリケーション] ブレード](common/enterprise_applications.png)

3. 新しいアプリケーションを追加するには、ダイアログの上部にある **[新しいアプリケーション]** をクリックします。

    ![[新しいアプリケーション] ボタン](common/add_new_app.png)

4. 検索ボックスに「**CorpTax**」と入力し、結果パネルで **[CorpTax]** を選択し、**[追加]** をクリックして、アプリケーションを追加します。

     ![結果一覧の CorpTax](common/search_new_app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成とテスト

このセクションでは、**Britta Simon** というテスト ユーザーに基づいて、CorpTax で Azure AD のシングル サインオンを構成し、テストします。
シングル サインオンを機能させるには、Azure AD ユーザーと CorpTax 内の関連ユーザー間にリンク関係が確立されている必要があります。

CorpTax で Azure AD のシングル サインオンを構成してテストするには、次の構成要素を完了する必要があります。

1. **[Azure AD シングル サインオンの構成](#configure-azure-ad-single-sign-on)** - ユーザーがこの機能を使用できるようにします。
2. **[CorpTax シングル サインオンの構成](#configure-corptax-single-sign-on)** - アプリケーション側でシングル サインオン設定を構成します。
3. **[Azure AD のテスト ユーザーの作成](#create-an-azure-ad-test-user)** - Britta Simon で Azure AD のシングル サインオンをテストします。
4. **[CorpTax のテスト ユーザーの作成](#create-corptax-test-user)** - CorpTax で Britta Simon に対応するユーザーを作成し、Azure AD の Britta Simon にリンクさせます。
5. **[Azure AD テスト ユーザーの割り当て](#assign-the-azure-ad-test-user)** - Britta Simon が Azure AD シングル サインオンを使用できるようにします。
6. **[シングル サインオンのテスト](#test-single-sign-on)** - 構成が機能するかどうかを確認します。

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成

このセクションでは、Azure portal 上で Azure AD のシングル サインオンを有効にします。

CorpTax で Azure AD シングル サインオンを構成するには、次の手順に従います。

1. [Azure portal](https://portal.azure.com/) の **CorpTax** アプリケーション統合ページで、**[シングル サインオン]** をクリックします。

    ![シングル サインオン構成のリンク](common/select_sso.png)

2. **[シングル サインオン方式の選択]** ダイアログで、**[SAML/WS-Fed]** モードを選択して、シングル サインオンを有効にします。

    ![シングル サインオン選択モード](common/select_saml_option.png)

3. **[SAML でシングル サインオンをセットアップします]** ページで、**[編集]** アイコンをクリックして **[基本的な SAML 構成]** ダイアログを開きます。

    ![基本的な SAML 構成を編集する](common/edit_urls.png)

4. **[基本的な SAML 構成]** セクションで、次の手順を実行します。

    ![[CorpTax のドメインと URL] のシングル サインオン情報](common/sp_intiated.png)

    **[サインオン URL]** テキスト ボックスに URL として「`https://asp.corptax.com`」と入力します。

8. **[SAML でシングル サインオンをセットアップします]** ページの **[SAML 署名証明書]** セクションで、**[ダウンロード]** をクリックして**フェデレーション メタデータ XML** をダウンロードし、お使いのコンピューターに保存します。

    ![証明書のダウンロードのリンク](common/metadataxml.png)

### <a name="configure-corptax-single-sign-on"></a>CorpTax のシングル サインオンを構成する

**CorpTax** 側でシングル サインオンを構成するには、ダウンロードした**フェデレーション メタデータ XML** を [CorpTax サポート チーム](mailto:bgraves@corptax.com)に送る必要があります。 サポート チームはこれを設定して、SAML SSO 接続が両方の側で正しく設定されるようにします。

### <a name="create-an-azure-ad-test-user"></a>Azure AD のテスト ユーザーの作成

このセクションの目的は、Azure Portal で Britta Simon というテスト ユーザーを作成することです。

1. Azure portal の左側のウィンドウで、**[Azure Active Directory]**、**[ユーザー]**、**[すべてのユーザー]** の順に選択します。

    ![[ユーザーとグループ] と [すべてのユーザー] リンク](common/users.png)

2. 画面の上部にある **[新しいユーザー]** を選択します。

    ![[新しいユーザー] ボタン](common/new_user.png)

3. [ユーザーのプロパティ] で、次の手順を実行します。

    ![[ユーザー] ダイアログ ボックス](common/user_properties.png)

    a. **[名前]** フィールドに「**BrittaSimon**」と入力します。

    b. **[ユーザー名]** フィールドに「**brittasimon\@yourcompanydomain.extension**」と入力します  
    たとえば、BrittaSimon@contoso.com のように指定します。

    c. **[パスワードを表示]** チェック ボックスをオンにし、[パスワード] ボックスに表示された値を書き留めます。

    d. **Create** をクリックしてください。

### <a name="assign-the-azure-ad-test-user"></a>Azure AD テスト ユーザーの割り当て

このセクションでは、Britta Simon に CorpTax へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

1. Azure portal 上で **[エンタープライズ アプリケーション]** を選択し、**[すべてのアプリケーション]** を選択してから、**[CorpTax]** を選択します。

    ![[エンタープライズ アプリケーション] ブレード](common/enterprise_applications.png)

2. アプリケーションの一覧で、**[CorpTax]** を入力および選択します。

    ![アプリケーションの一覧の CorpTax のリンク](common/all_applications.png)

3. 左側のメニューで **[ユーザーとグループ]** を選びます。

    ![[ユーザーとグループ] リンク](common/users_groups_blade.png)

4. **[ユーザーの追加]** をクリックし、**[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。

    ![[割り当ての追加] ウィンドウ](common/add_assign_user.png)

5. **[ユーザーとグループ]** ダイアログの [ユーザー] の一覧で **[Britta Simon]** を選択し、画面の下部にある **[選択]** ボタンをクリックします。

6. SAML アサーション内に任意のロール値が必要な場合、**[ロールの選択]** ダイアログでユーザーに適したロールを一覧から選択し、画面の下部にある **[選択]** をクリッします。

7. **[割り当ての追加]** ダイアログで、**[割り当て]** をクリックします。

### <a name="create-corptax-test-user"></a>CorpTax のテスト ユーザーを作成する

このセクションでは、CorpTax で Britta Simon というユーザーを作成します。  [CorpTax サポート チーム](mailto:bgraves@corptax.com)と連携し、CorpTax プラットフォームにユーザーを追加してください。 シングル サインオンを使用する前に、ユーザーを作成し、有効化する必要があります。

### <a name="test-single-sign-on"></a>シングル サインオンのテスト

このセクションでは、アクセス パネルを使用して Azure AD のシングル サインオン構成をテストします。
アクセス パネルで [CorpTax] タイルをクリックすると、次の CorpTax ページにリダイレクトされるはずです。 

![image](media/corptax-tutorial/corptaxlogin.png)

**[Environment]\(環境\)** テキスト ボックスに適切な環境を入力すると、SSO を設定した CorpTax に自動的にサインインされます。 アクセス パネルの詳細については、[アクセス パネルの概要](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)に関する記事を参照してください。

## <a name="additional-resources"></a>その他のリソース

- [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Azure Active Directory の条件付きアクセスとは](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
