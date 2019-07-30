---
title: チュートリアル:Azure Active Directory と TigerText Secure Messenger の統合 | Microsoft Docs
description: Azure Active Directory と TigerText Secure Messenger の間でシングル サインオンを構成する方法について説明します。
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 03f1e128-5bcb-4e49-b6a3-fe22eedc6d5e
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/29/2019
ms.author: jeedes
ms.openlocfilehash: ea3bda1dd51a7c3a2e5e3f8b669d7138898f1595
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/13/2019
ms.locfileid: "67088671"
---
# <a name="tutorial-azure-active-directory-integration-with-tigertext-secure-messenger"></a>チュートリアル:Azure Active Directory と TigerText Secure Messenger の統合

このチュートリアルでは、TigerText Secure Messenger と Azure Active Directory (Azure AD) を統合する方法について説明します。

TigerText Secure Messenger と Azure AD の統合には、次の利点があります。

* TigerText Secure Messenger にアクセスする Azure AD ユーザーを制御できます。
* ユーザーが自分の Azure AD アカウントで TigerText Secure Messenger に自動的にサインイン (シングル サインオン) できるようにすることができます。
* 1 つの中央サイト (Azure ポータル) でアカウントを管理できます。

サービスとしてのソフトウェア (SaaS) アプリと Azure AD の統合の詳細については、「[Azure Active Directory でのアプリケーションへのシングル サインオン](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)」を参照してください。

## <a name="prerequisites"></a>前提条件

TigerText Secure Messenger と Azure AD の統合を構成するには、次のものが必要です。

* Azure AD サブスクリプション。 Azure サブスクリプションをお持ちでない場合は、開始する前に[無料アカウントを作成](https://azure.microsoft.com/free/)してください。
* TigerText Secure Messenger のシングル サインオンが有効なサブスクリプション。

## <a name="scenario-description"></a>シナリオの説明

このチュートリアルでは、テスト環境で Azure AD のシングル サインオンを構成してテストし、TigerText Secure Messenger を Azure AD と統合します。

TigerText Secure Messenger では、SP によって開始されるシングル サインオン (SSO) がサポートされます。

## <a name="add-tigertext-secure-messenger-from-the-azure-marketplace"></a>Azure Marketplace からの TigerText Secure Messenger の追加

Azure AD への TigerText Secure Messenger の統合を構成するには、Azure Marketplace からマネージド SaaS アプリの一覧に TigerText Secure Messenger を追加する必要があります。

1. [Azure Portal](https://portal.azure.com?azure-portal=true) にサインインします。
1. 左ウィンドウで、 **[Azure Active Directory]** を選択します。

    ![[Azure Active Directory] オプション](common/select-azuread.png)

1. **[エンタープライズ アプリケーション]** に移動し、 **[すべてのアプリケーション]** を選択します。

    ![[エンタープライズ アプリケーション] ウィンドウ](common/enterprise-applications.png)

1. 新しいアプリケーションを追加するには、ウィンドウの上部の **[+ 新しいアプリケーション]** を選択します。

    ![[新しいアプリケーション] オプション](common/add-new-app.png)

1. 検索ボックスに「**TigerText Secure Messenger**」と入力します。 検索結果で **[TigerText Secure Messenger]** を選択し、 **[追加]** を選択してアプリケーションを追加します。

    ![結果一覧の TigerText Secure Messenger](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成とテスト

このセクションでは、**Britta Simon** というテスト ユーザーに基づいて、TigerText Secure Messenger で Azure AD のシングル サインオンを構成し、テストします。 シングル サインオンを機能させるには、Azure AD ユーザーと TigerText Secure Messenger 内の関連ユーザーとの間にリンク関係を確立する必要があります。

TigerText Secure Messenger で Azure AD のシングル サインオンを構成してテストするには、次の構成要素を完了する必要があります。

1. **[Azure AD シングル サインオンの構成](#configure-azure-ad-single-sign-on)** - ユーザーがこの機能を使用できるようにします。
1. **[TigerText Secure Messenger のシングル サインオンの構成](#configure-tigertext-secure-messenger-single-sign-on)** - アプリケーション側でシングル サインオン設定を構成します。
1. **[Azure AD のテスト ユーザーの作成](#create-an-azure-ad-test-user)** - Britta Simon で Azure AD のシングル サインオンをテストします。
1. **[Azure AD テスト ユーザーの割り当て](#assign-the-azure-ad-test-user)** - Britta Simon が Azure AD シングル サインオンを使用できるようにします。
1. **[TigerText Secure Messenger のテスト ユーザーの作成](#create-a-tigertext-secure-messenger-test-user)** - Britta Simon という Azure AD ユーザーにリンクされている Britta Simon というユーザーが TigerText Secure Messenger に存在するようにします。
1. **[シングル サインオンのテスト](#test-single-sign-on)** - 構成が機能するかどうかを確認します。

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成

このセクションでは、Azure portal 上で Azure AD のシングル サインオンを有効にします。

TigerText Secure Messenger で Azure AD シングル サインオンを構成するには、次の手順に従います。

1. [Azure portal](https://portal.azure.com/) の **TigerText Secure Messenger** アプリケーション統合ページで、 **[シングル サインオン]** を選択します。

    ![シングル サインオン オプションを構成する](common/select-sso.png)

1. **[シングル サインオン方式の選択]** ウィンドウで、 **[SAML/WS-Fed]** モードを選択して、シングル サインオンを有効にします。

    ![シングル サインオン選択モード](common/select-saml-option.png)

1. **[SAML でシングル サインオンをセットアップします]** ウィンドウで、**編集** (鉛筆アイコン) を選択して **[基本的な SAML 構成]** ウィンドウを開きます。

    ![基本的な SAML 構成を編集する](common/edit-urls.png)

1. **[基本的な SAML 構成]** ウィンドウで、次の手順に従います。

    ![[TigerText Secure Messenger のドメインと URL] のシングル サインオン情報](common/sp-identifier.png)

    1. **[サインオン URL]** ボックスに、URL を入力します。

       `https://home.tigertext.com`

    1. **[識別子 (エンティティ ID)]** ボックスに、次のパターンを使用して URL を入力します。

       `https://saml-lb.tigertext.me/v1/organization/<instance ID>`

    > [!NOTE]
    > **[識別子 (エンティティ ID)]** の値は実際の値ではありません。 実際の識別子でこの値を更新します。 この値を取得するには、[TigerText Secure Messenger サポート チーム](mailto:prosupport@tigertext.com)に問い合わせてください。 Azure portal の **[基本的な SAML 構成]** ウィンドウに示されているパターンを参照することもできます。

1. **[SAML でシングル サインオンをセットアップします]** ウィンドウの **[SAML 署名証明書]** セクションで、 **[ダウンロード]** を選択し、特定のオプションの**フェデレーション メタデータ XML** をダウンロードします。

    ![フェデレーション メタデータ XML のダウンロード オプション](common/metadataxml.png)

1. **[TigerText Secure Messenger のセットアップ]** セクションに、必要な URL (複数可) をコピーします。

   * **ログイン URL**
   * **Azure AD 識別子**
   * **ログアウト URL**

    ![構成 URL のコピー](common/copy-configuration-urls.png)

### <a name="configure-tigertext-secure-messenger-single-sign-on"></a>TigerText Secure Messenger シングル サインオンの構成

TigerText Secure Messenger 側でシングル サインオンを構成するには、ダウンロードしたフェデレーション メタデータ XML と Azure portal からコピーした適切な URL を [TigerText Secure Messenger サポート チーム](mailto:prosupport@tigertext.com)に送信する必要があります。 TigerText Secure Messenger チームは、SAML SSO 接続が両方の側で正しく設定されていることを確認します。

### <a name="create-an-azure-ad-test-user"></a>Azure AD のテスト ユーザーの作成

このセクションでは、Azure ポータルで Britta Simon というテスト ユーザーを作成します。

1. Azure portal の左側のウィンドウで、 **[Azure Active Directory]**    >  **[ユーザー]**  >  **[すべてのユーザー]** の順に選択します。

    ![[ユーザー] と [すべてのユーザー] オプション](common/users.png)

1. 画面の上部にある **[+ 新しいユーザー]** を選択します。

    ![[新しいユーザー] オプション](common/new-user.png)

1. **[ユーザー]** ウィンドウで、次の手順を実行します。

    ![[ユーザー] ウィンドウ](common/user-properties.png)

    1. **[名前]** ボックスに「**BrittaSimon**」と入力します。
  
    1. **[ユーザー名]** ボックスに、「**BrittaSimon\@\<yourcompanydomain>.\<extension>** 」と入力します。 たとえば、「**BrittaSimon\@contoso.com**」です。

    1. **[パスワードを表示]** チェック ボックスをオンにし、 **[パスワード]** ボックスに表示された値を書き留めます。

    1. **作成** を選択します。

### <a name="assign-the-azure-ad-test-user"></a>Azure AD テスト ユーザーの割り当て

このセクションでは、Britta Simon に TigerText Secure Messenger へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

1. Azure portal で、 **[エンタープライズ アプリケーション]**  >  **[すべてのアプリケーション]**  >  **[TigerText Secure Messenger]** の順に選択します。

    ![[エンタープライズ アプリケーション] ウィンドウ](common/enterprise-applications.png)

1. アプリケーションの一覧で **[TigerText Secure Messenger]** を選択します。

    ![アプリケーションの一覧の TigerText Secure Messenger](common/all-applications.png)

1. 左側のウィンドウの **[管理]** で、 **[ユーザーとグループ]** を選択します。

    ![[ユーザーとグループ] オプション](common/users-groups-blade.png)

1. **[+ ユーザーの追加]** を選択し、 **[割り当ての追加]** ウィンドウで **[ユーザーとグループ]** を選択します。

    ![[割り当ての追加] ウィンドウ](common/add-assign-user.png)

1. **[ユーザーとグループ]** ウィンドウの **[ユーザー]** の一覧で **[Britta Simon]** を選択し、ウィンドウの下部にある **[選択]** を選択します。

1. SAML アサーションでロール値が必要な場合は、 **[ロールの選択]** ウィンドウで、一覧からユーザーに適したロールを選択します。 ウィンドウの下部で、 **[選択]** を選択します。

1. **[割り当ての追加]** ウィンドウで **[割り当て]** を選択します。

### <a name="create-a-tigertext-secure-messenger-test-user"></a>TigerText Secure Messenger のテストユーザーの作成

このセクションでは、TigerText Secure Messenger で Britta Simon というユーザーを作成します。 [TigerText Secure Messenger サポート チーム](mailto:prosupport@tigertext.com)と協力して、TigerText Secure Messenger にユーザーとして Britta Simon を追加します。 シングル サインオンを使用する前に、ユーザーを作成し、有効化する必要があります。

### <a name="test-single-sign-on"></a>シングル サインオンのテスト

このセクションでは、マイ アプリ ポータルを使用して自分の Azure AD のシングル サインオン構成をテストします。

マイ アプリ ポータルで **[TigerText Secure Messenger]** を選択すると、シングル サインオンを設定した TigerText Secure Messenger サブスクリプションに自動的にサインインします。 マイアプリ ポータルの詳細については、「[マイ アプリ ポータルでアプリにアクセスして使用する](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)」を参照してください。

## <a name="additional-resources"></a>その他のリソース

* [SaaS アプリと Azure Active Directory の統合に関するチュートリアルの一覧](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

* [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

* [Azure Active Directory の条件付きアクセスとは](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
