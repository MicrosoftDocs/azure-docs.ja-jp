---
title: 'チュートリアル: Azure Active Directory と ThirdLight の統合 | Microsoft Docs'
description: このチュートリアルでは、Azure Active Directory と ThirdLight の間でシングル サインオンを構成する方法について説明します。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/25/2019
ms.author: jeedes
ms.openlocfilehash: f15d00f1050177c6255fb5528f03314153c3ed9a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "92514664"
---
# <a name="tutorial-azure-active-directory-integration-with-thirdlight"></a>チュートリアル: Azure Active Directory と ThirdLight の統合

このチュートリアルでは、ThirdLight と Azure Active Directory (Azure AD) を統合する方法について説明します。 この統合には、次の利点があります。

* Azure AD を使用して、ThirdLight にアクセスできるユーザーを管理できます。
* ユーザーが自分の Azure AD アカウントで ThirdLight に自動的にサインイン (シングル サインオン) するように設定できます。
* 1 つの中央サイト (Azure ポータル) でアカウントを管理できます。

SaaS アプリと Azure AD の統合の詳細については、「[Azure Active Directory でのアプリケーションへのシングル サインオン](../manage-apps/what-is-single-sign-on.md)」を参照してください。

Azure サブスクリプションをお持ちでない場合は、開始する前に[無料アカウントを作成](https://azure.microsoft.com/free/)してください。

## <a name="prerequisites"></a>前提条件

ThirdLight と Azure AD の統合を構成するには、以下が必要です。

* Azure AD サブスクリプション。 Azure AD の環境がない場合は、[無料アカウント](https://azure.microsoft.com/free/)を取得できます。
* シングル サインオンが有効な ThirdLight サブスクリプション。

## <a name="scenario-description"></a>シナリオの説明

このチュートリアルでは、テスト環境で Azure AD のシングル サインオンを構成してテストします。

* ThirdLight では、SP Initiated SSO がサポートされます。

## <a name="add-thirdlight-from-the-gallery"></a>ギャラリーからの ThirdLight の追加

Azure AD への ThirdLight の統合を設定するには、ギャラリーからマネージド SaaS アプリの一覧に ThirdLight を追加する必要があります。

1. [Azure portal](https://portal.azure.com) の左側のウィンドウで、 **[Azure Active Directory]** を選択します。

    ![[Azure Active Directory] を選択します。](common/select-azuread.png)

2. **[エンタープライズ アプリケーション]**  >  **[すべてのアプリケーション]** の順に移動します。

    ![[エンタープライズ アプリケーション] ブレード](common/enterprise-applications.png)

3. アプリケーションを追加するには、ウィンドウの上部の **[新しいアプリケーション]** を選択します。

    ![[新しいアプリケーション] を選択する](common/add-new-app.png)

4. 検索ボックスに「**ThirdLight**」と入力します。 検索結果で **[ThirdLight]** を選択し、**[追加]** を選択します。

     ![[検索結果]](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成とテスト

このセクションでは、Britta Simon という名前のテスト ユーザーを使用して、ThirdLight に対する Azure AD シングル サインオンを構成およびテストします。
シングル サインオンを有効にするには、Azure AD ユーザーと ThirdLight の対応するユーザーの間で、関係を確立する必要があります。

ThirdLight に対する Azure AD シングル サインオンを構成およびテストするには、以下の手順を完了する必要があります。

1. **[Azure AD のシングル サインオンを構成](#configure-azure-ad-single-sign-on)** して、この機能をユーザーに対して有効にします。
2. アプリケーション側で **[ThirdLight シングル サインオンを構成](#configure-thirdlight-single-sign-on)** します。
3. Azure AD のシングル サインオンをテストするための **[Azure AD テスト ユーザーを作成](#create-an-azure-ad-test-user)** します。
4. **[Azure AD テスト ユーザーを割り当て](#assign-the-azure-ad-test-user)** て、Azure AD のシングル サインオンをそのユーザーに対して有効にします。
5. **[ThirdLight テスト ユーザーを作成](#create-a-thirdlight-test-user)** して、Azure AD の対応するユーザーにリンクさせます。
6. **[シングル サインオンをテスト](#test-single-sign-on)** して、この構成が機能することを確認します。

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成

このセクションでは、Azure portal で Azure AD のシングル サインオンを有効にします。

ThirdLight に対する Azure AD シングル サインオンを構成するには、次の手順を実行します。

1. [Azure portal](https://portal.azure.com/) の ThirdLight アプリケーション統合ページで、 **[シングル サインオン]** を選択します。

    ![[シングル サインオン] の選択](common/select-sso.png)

2. **[シングル サインオン方式の選択]** ダイアログ ボックスで、 **[SAML/WS-Fed]** モードを選択して、シングル サインオンを有効にします。

    ![シングル サインオン方式の選択](common/select-saml-option.png)

3. **[SAML でシングル サインオンをセットアップします]** ページで、**編集** アイコンを選択して **[基本的な SAML 構成]** ダイアログ ボックスを開きます。

    ![[編集] アイコン](common/edit-urls.png)

4. **[基本的な SAML 構成]** ダイアログ ボックスで、次の手順を完了します。

    ![[基本的な SAML 構成] ダイアログ ボックス](common/sp-identifier.png)

    1. **[サイン オン URL]** ボックスに、次のパターンの URL を入力します。
    
          `https://<subdomain>.thirdlight.com/`

    1. **[識別子 (エンティティ ID)]** ボックスに、次のパターンの URL を入力します。

       `https://<subdomain>.thirdlight.com/saml/sp`

       > [!NOTE]
       > これらの値はプレースホルダーです。 実際のサインオン URL と識別子を使用する必要があります。 この値を取得するには、[ThirdLight サポート チーム](https://www.thirdlight.com/support)に問い合わせてください。 また、Azure portal の **[基本的な SAML 構成]** ダイアログ ボックスに示されているパターンを参照することもできます。

5. **[SAML でシングル サインオンをセットアップします]** ページの **[SAML 署名証明書]** セクションで、実際の要件に従って、 **[フェデレーション メタデータ XML]** の横にある **[ダウンロード]** リンクを選択し、ファイルを自分のコンピューターに保存します。

    ![証明書のダウンロード リンク](common/metadataxml.png)

6. **[Set up ThirdLight]\(ThirdLight の設定\)** セクションで、要件に基づいて適切な URL をコピーします。

    ![構成 URL をコピーする](common/copy-configuration-urls.png)

    1. **[ログイン URL]** 。

    1. **[Azure AD 識別子]** 。

    1. **[ログアウト URL]** 。

### <a name="configure-thirdlight-single-sign-on"></a>ThirdLight のシングル サインオンの構成

1. 新しい Web ブラウザー ウィンドウで、ThirdLight 企業サイトに管理者としてサインインします。

1. **[Configuration]\(構成\)**  >  **[System Administration]\(システム管理\)**  >  **[SAML2]** の順に移動します。

    ![システム管理](./media/thirdlight-tutorial/ic805843.png "システム管理")

1. SAML2 の構成セクションで、次の手順を実行します。
  
    ![SAML2 の構成セクション](./media/thirdlight-tutorial/ic805844.png "SAML2 の構成セクション")

    1. **[Enable SAML2 Single Sign-On]** を選択します。

    1. **[Source for IdP Metadata]\(IdP メタデータのソース\)** で、**[Load IdP Metadata from XML]\(XML から IdP メタデータをダウンロードする\)** を選択します。

    1. 前のセクションで Azure portal からダウンロードしたメタデータ ファイルを開きます。 **[IdP Metadata XML]\(IdP メタデータ XML\)** ボックスにファイルの内容をコピーして貼り付けます。

    1. **[Save SAML2 settings]\(SAML2 設定の保存\)** を選択します。

### <a name="create-an-azure-ad-test-user"></a>Azure AD のテスト ユーザーの作成

このセクションでは、Azure portal で Britta Simon という名前のテスト ユーザーを作成します。

1. Azure portal で、左側のウィンドウの **[Azure Active Directory]** を選択し、 **[ユーザー]** 、 **[すべてのユーザー]** の順に選択します。

    ![[すべてのユーザー] を選択する](common/users.png)

2. ウィンドウの上部にある **[新しいユーザー]** を選択します。

    ![[新しいユーザー] を選択する](common/new-user.png)

3. **[ユーザー]** ダイアログ ボックスで、次の手順を実行します。

    ![[ユーザー] ダイアログ ボックス](common/user-properties.png)

    1. **[名前]** ボックスに「**BrittaSimon**」と入力します。
  
    1. **[ユーザー名]** ボックスに「**BrittaSimon@\<yourcompanydomain>.\<extension>** 」と入力します (例: BrittaSimon@contoso.com)。

    1. **[パスワードを表示]** を選択し、 **[パスワード]** ボックス内の値を書き留めます。

    1. **［作成］** を選択します

### <a name="assign-the-azure-ad-test-user"></a>Azure AD テスト ユーザーの割り当て

このセクションでは、Britta Simon に ThirdLight へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

1. Azure portal で **[エンタープライズ アプリケーション]** を選択し、**[すべてのアプリケーション]**、**[ThirdLight]** の順に選択します。

    ![[エンタープライズ アプリケーション] ブレード](common/enterprise-applications.png)

2. アプリケーションの一覧で、**[ThirdLight]** を選択します。

    ![アプリケーションの一覧](common/all-applications.png)

3. 左側のウィンドウで **[ユーザーとグループ]** を選択します。

    ![[ユーザーとグループ] の選択](common/users-groups-blade.png)

4. **[ユーザーの追加]** を選択し、 **[割り当ての追加]** ダイアログ ボックスで **[ユーザーとグループ]** を選択します。

    ![[ユーザーの追加] を選択する](common/add-assign-user.png)

5. **[ユーザーとグループ]** ダイアログ ボックスで、ユーザーの一覧で **[Britta Simon]** を選択し、ウィンドウの下部にある **[選択]** ボタンをクリックします。

6. SAML アサーション内にロール値が必要な場合、 **[ロールの選択]** ダイアログ ボックスで、一覧からユーザーに適したロールを選択します。 ウィンドウの下部にある **[選択]** ボタンをクリックします。

7. **[割り当ての追加]** ダイアログ ボックスで **[割り当て]** を選びます。

### <a name="create-a-thirdlight-test-user"></a>ThirdLight のテスト ユーザーの作成

Azure AD ユーザーが ThirdLight にサインインできるようにするには、そのユーザーを ThirdLight に追加する必要があります。 手動で追加する必要があります。

ユーザー アカウントを作成するには、以下の手順に従います。

1. ThirdLight 企業サイトに管理者としてサインインします。

1. **[Users]\(ユーザー\)** タブに移動します。

1. **[Users and Groups]** を選択します。

1. **[Add new User]\(新しいユーザーの追加\)** を選択します。

1. プロビジョニングする有効な Azure AD アカウントのユーザー名、名前または説明、メール アドレスを入力します。 新しいメンバーのプリセットまたはグループを選択します。

1. **［作成］** を選択します

> [!NOTE]
> ThirdLight から提供されているユーザー アカウント作成ツールまたは API を使用して、Azure AD ユーザー アカウントをプロビジョニングできます。

### <a name="test-single-sign-on"></a>シングル サインオンのテスト

ここで、アクセス パネルを使用して Azure AD のシングル サインオン構成をテストする必要があります。

アクセス パネル上で [ThirdLight] タイルを選択すると、SSO を設定した ThirdLight インスタンスに自動的にサインインします。 アクセス パネルの詳細については、「[マイ アプリ ポータルでアプリにアクセスして使用する](../user-help/my-apps-portal-end-user-access.md)」を参照してください。

## <a name="additional-resources"></a>その他のリソース

- [SaaS アプリケーションと Azure Active Directory との統合に関するチュートリアル](./tutorial-list.md)

- [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)

- [Azure Active Directory の条件付きアクセスとは](../conditional-access/overview.md)