---
title: チュートリアル:Azure Active Directory と Coralogix の統合 | Microsoft Docs
description: Azure Active Directory と Coralogix の間でシングル サインオンを構成する方法について説明します。
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: ba79bfc1-992e-4924-b76a-8eb0dfb97724
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 1/2/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: d0095a825f5582dc795f5bebdcf08be07a92946e
ms.sourcegitcommit: c3d1aa5a1d922c172654b50a6a5c8b2a6c71aa91
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/17/2019
ms.locfileid: "59678294"
---
# <a name="tutorial-azure-active-directory-integration-with-coralogix"></a>チュートリアル:Azure Active Directory と Coralogix の統合

このチュートリアルでは、Coralogix と Azure Active Directory (Azure AD) を統合する方法について説明します。
Coralogix と Azure AD の統合には、次の利点があります。

* Coralogix にアクセスできるユーザーを Azure AD で制御できます。
* ユーザーが自分の Azure AD アカウントで Coralogix に自動的にサインイン (シングル サインオン) するように設定できます。
* 1 つの中央サイト (Azure ポータル) でアカウントを管理できます。

SaaS アプリと Azure AD の統合の詳細については、[Azure Active Directory を使用したアプリケーション アクセスとシングル サインオンの概要](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)に関する記事をご覧ください。
Azure サブスクリプションをお持ちでない場合は、開始する前に[無料アカウントを作成](https://azure.microsoft.com/free/)してください。

## <a name="prerequisites"></a>前提条件

Coralogix と Azure AD の統合を構成するには、次のものが必要です。

- Azure AD サブスクリプション。 Azure AD 環境がない場合は、[1 か月の試用版](https://azure.microsoft.com/pricing/free-trial/)を入手できます。
- Coralogix でのシングル サインオンが有効なサブスクリプション。 

## <a name="scenario-description"></a>シナリオの説明

このチュートリアルでは、テスト環境で Azure AD のシングル サインオンを構成してテストします。

* Coralogix では、SP によって開始される SSO がサポートされます。

## <a name="add-coralogix-from-the-gallery"></a>ギャラリーからの Coralogix の追加

Azure AD への Coralogix の統合を構成するには、まずギャラリーから管理対象 SaaS アプリの一覧に Coralogix を追加します。

ギャラリーから Coralogix を追加するには、次の手順を実行します。

1. [Azure Portal](https://portal.azure.com) の左側のウィンドウで、**Azure Active Directory** アイコンを選択します。

    ![Azure Active Directory のボタン](common/select-azuread.png)

2. **[エンタープライズ アプリケーション]** に移動し、**[すべてのアプリケーション]** を選択します。

    ![[エンタープライズ アプリケーション] ブレード](common/enterprise-applications.png)

3. 新しいアプリケーションを追加するには、ダイアログ ボックスの上部にある **[新しいアプリケーション]** ボタンを選びます。

    ![[新しいアプリケーション] ボタン](common/add-new-app.png)

4. 検索ボックスに「**Coralogix**」と入力します。 結果ペインから **[Coralogix]** を選択し、**[追加]** ボタンを選択して、アプリケーションを追加します。

     ![結果リストの Coralogix](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成とテスト

このセクションでは、Britta Simon というテスト ユーザーに基づいて、Coralogix で Azure AD のシングル サインオンを構成し、テストします。
シングル サインオンを機能させるには、Azure AD ユーザーと Coralogix 内の関連ユーザーとの間にリンクを確立する必要があります。

Coralogix で Azure AD のシングル サインオンを構成してテストするには、まず次の構成要素を完了します。

1. [Azure AD シングル サインオンの構成](#configure-azure-ad-single-sign-on) - ユーザーがこの機能を使用できるようにします。
2. [Coralogix シングル サインオンの構成](#configure-coralogix-single-sign-on) - アプリケーション側でシングル サインオン設定を構成します。
3. [Azure AD のテスト ユーザーの作成](#create-an-azure-ad-test-user) - Britta Simon で Azure AD のシングル サインオンをテストします。
4. [Azure AD テスト ユーザーの割り当て](#assign-the-azure-ad-test-user) - Britta Simon が Azure AD シングル サインオンを使用できるようにします。
5. [Coralogix のテスト ユーザーの作成](#create-a-coralogix-test-user) - Coralogix で Britta Simon に対応するユーザーを作成し、Azure AD のこのユーザーにリンクさせます。
6. [シングル サインオンのテスト](#test-single-sign-on)。構成が機能することを確認します。

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成

このセクションでは、Azure portal 上で Azure AD のシングル サインオンを有効にします。

Coralogix で Azure AD シングル サインオンを構成するには、次の手順を実行します。

1. [Azure portal](https://portal.azure.com/) の **Coralogix** アプリケーション統合ページで、**[シングル サインオン]** を選択します。

    ![シングル サインオン構成のリンク](common/select-sso.png)

2. **[シングル サインオン方式の選択]** ダイアログ ボックスで、**[SAML]** を選択して、シングル サインオンを有効にします。

    ![シングル サインオン選択モード](common/select-saml-option.png)

3. **[SAML でシングル サインオンをセットアップします]** ページで、**編集**アイコンを選択して **[基本的な SAML 構成]** ダイアログ ボックスを開きます。

    ![基本的な SAML 構成を編集する](common/edit-urls.png)

4. **[基本的な SAML 構成]** ダイアログ ボックスで、次の手順を実行します。

    ![[Coralogix のドメインと URL] のシングル サインオン情報](common/sp-identifier.png)

    a. **[サインオン URL]** ボックスに、`https://<SUBDOMAIN>.coralogix.com` 形式で URL を入力します。

    b. **[識別子 (エンティティ ID)]** テキスト ボックスに、次のように URL を入力します。
    
    `https://api.coralogix.com/saml/metadata.xml`

    or

    `https://aws-client-prod.coralogix.com/saml/metadata.xml` 

    > [!NOTE]
    > サインオン URL は実際の値ではありません。 実際のサインオン URL でこの値を更新してください。 この値を取得するには、[Coralogix クライアント サポート チーム](mailto:info@coralogix.com)に問い合わせてください。 Azure portal の **[基本的な SAML 構成]** セクションのパターンを参照することもできます。

5. Coralogix アプリケーションは、特定の形式で構成された SAML アサーションを受け入れます。 このアプリケーションには、次の要求を構成します。 これらの属性の値は、アプリケーション統合ページの **[ユーザー属性]** セクションで管理できます。 **[SAML でシングル サインオンをセットアップします]** ページで、**[編集]** ボタンを選択して **[ユーザー属性]** ダイアログ ボックスを開きます。

    ![image](common/edit-attribute.png)

6. **[ユーザー属性]** ダイアログ ボックスの **[ユーザー要求]** セクションで、**[編集]** アイコンを使用して要求を編集します。 前の画像で示されているように、**[新しい要求の追加]** を使用して SAML トークン属性を構成することで、要求を追加することもできます。 その後、次の手順を実行します。
    
    a. **[編集]** アイコンを選択して、**[ユーザー要求の管理]** ダイアログ ボックスを開きます。

    ![画像](./media/coralogix-tutorial/tutorial_usermail.png) ![画像](./media/coralogix-tutorial/tutorial_usermailedit.png)

    b. **[名前識別子の形式の選択]** の一覧で、**[電子メール アドレス]** を選択します。

    c. **[ソース属性]** の一覧から、**[user.mail]** を選択します。

    d. **[保存]** を選択します。

7. **[SAML でシングル サインオンをセットアップします]** ページの **[SAML 署名証明書]** セクションで、**[ダウンロード]** を選択し、要件に応じて指定のオプションから**フェデレーション メタデータ XML** をダウンロードします。 その後、ご自分のコンピューター上に保存します。

    ![証明書のダウンロードのリンク](common/metadataxml.png)

8. **[Set up Coralogix]\(Coralogix の設定\)** セクションで、適切な URL をコピーします。

    ![構成 URL のコピー](common/copy-configuration-urls.png)

    a. ログイン URL

    b. Azure AD 識別子

    c. ログアウト URL

### <a name="configure-coralogix-single-sign-on"></a>Coralogix のシングル サインオンの構成

**Coralogix** 側でシングル サインオンを構成するには、ダウンロードした**フェデレーション メタデータ XML** と Azure portal からコピーした URL を [Coralogix サポート チーム](mailto:info@coralogix.com)に送信します。 サポート チームにより、SAML シングル サインオン接続が両方の側で確実に正しく設定されます。

### <a name="create-an-azure-ad-test-user"></a>Azure AD のテスト ユーザーの作成 

このセクションの目的は、Azure Portal で Britta Simon というテスト ユーザーを作成することです。

1. Azure portal の左側のウィンドウで、**[Azure Active Directory]**、**[ユーザー]**、**[すべてのユーザー]** の順に選択します。

    ![[ユーザーとグループ] と [すべてのユーザー] リンク](common/users.png)

2. 画面の上部にある **[新しいユーザー]** を選択します。

    ![[新しいユーザー] ボタン](common/new-user.png)

3. **[ユーザー]** ダイアログ ボックスで、次の手順を実行します。

    ![[ユーザー] ダイアログ ボックス](common/user-properties.png)

    a. **[名前]** フィールドに「**BrittaSimon**」と入力します。
  
    b. **[ユーザー名]** フィールドに「brittasimon@yourcompanydomain.extension」と入力します。 たとえば、この場合は、「brittasimon@contoso.com」と入力できます。

    c. **[パスワードを表示]** チェック ボックスをオンにし、**[パスワード]** ボックスに表示された値を書き留めます。

    d. **作成** を選択します。

### <a name="assign-the-azure-ad-test-user"></a>Azure AD テスト ユーザーの割り当て

このセクションでは、Britta Simon に Coralogix へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

1. Azure portal で **[エンタープライズ アプリケーション]** を選択し、**[すべてのアプリケーション]**、**[Coralogix]** の順に選択します。

    ![[エンタープライズ アプリケーション] ブレード](common/enterprise-applications.png)

2. アプリケーションの一覧で **[Coralogix]** を選択します。

    ![アプリケーションの一覧の Coralogix リンク](common/all-applications.png)

3. 左側のメニューで **[ユーザーとグループ]** を選びます。

    ![[ユーザーとグループ] リンク](common/users-groups-blade.png)

4. **[ユーザーの追加]** ボタンを選択します。 次に、**[割り当ての追加]** ダイアログ ボックスで **[ユーザーとグループ]** を選択します。

    ![[割り当ての追加] ウィンドウ](common/add-assign-user.png)

5. **[ユーザーとグループ]** ダイアログ ボックスで、ユーザーの一覧から **[Britta Simon]** を選択します。 次に、画面の下部にある **[選択]** ボタンをクリックします。

6. SAML アサーション内にロール値が必要な場合、**[ロールの選択]** ダイアログ ボックスで、一覧からユーザーに適したロールを選択します。 次に、画面の下部にある **[選択]** ボタンをクリックします。

7. **[割り当ての追加]** ダイアログ ボックスで、**[割り当て]** ボタンを選択します。

### <a name="create-a-coralogix-test-user"></a>Coralogix のテスト ユーザーの作成

このセクションでは、Coralogix で Britta Simon というユーザーを作成します。  [Coralogix サポート チーム](mailto:info@coralogix.com)と連携し、Coralogix プラットフォームにユーザーを追加してください。 シングル サインオンを使用する前に、ユーザーを作成し、アクティブにする必要があります。

### <a name="test-single-sign-on"></a>シングル サインオンのテスト 

このセクションでは、MyApps ポータルを使用してご自分の Azure AD のシングル サインオン構成をテストします。

MyApps ポータル内で [Coralogix] タイルを選択すると、Coralogix に自動的にサインインします。 MyApps ポータルの詳細については、[MyApps ポータルの概要](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)に関するページを参照してください。

## <a name="additional-resources"></a>その他のリソース

- [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Azure Active Directory の条件付きアクセスとは](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

