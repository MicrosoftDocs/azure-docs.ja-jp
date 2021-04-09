---
title: チュートリアル:Azure Active Directory と Percolate の統合 | Microsoft Docs
description: このチュートリアルでは、Azure Active Directory と Percolate の間でシングル サインオンを構成する方法について説明します。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 04/01/2019
ms.author: jeedes
ms.openlocfilehash: 913ffc0670e40e749bd28382d492a16891fdc5c9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "92522268"
---
# <a name="tutorial-azure-active-directory-integration-with-percolate"></a>チュートリアル:Azure Active Directory と Percolate との統合

このチュートリアルでは、Percolate と Azure Active Directory (Azure AD) を統合する方法について説明します。

この統合には、次の利点があります。

* Azure AD を使用して、Percolate にアクセスするユーザーを管理できます。
* ユーザーが自分の Azure AD アカウントを使用して自動的に Percolate にサインイン (シングル サインオン) できるようにすることができます。
* 1 つの中央サイト (Azure ポータル) でアカウントを管理できます。

SaaS アプリと Azure AD の統合の詳細については、「[Azure Active Directory でのアプリケーションへのシングル サインオン](../manage-apps/what-is-single-sign-on.md)」を参照してください。

Azure サブスクリプションをお持ちでない場合は、開始する前に[無料アカウントを作成](https://azure.microsoft.com/free/)してください。

## <a name="prerequisites"></a>前提条件

Percolate と Azure AD の統合を構成するには、次のものが必要です。

* Azure AD サブスクリプション。 Azure AD の環境がない場合は、[無料アカウント](https://azure.microsoft.com/free/)を取得できます。
* シングル サインオンが有効な Percolate サブスクリプション。

## <a name="scenario-description"></a>シナリオの説明

このチュートリアルでは、テスト環境で Azure AD のシングル サインオンを構成してテストします。

* Percolate では、SP-initiated SSO と IdP-initiated SSO がサポートされます。

## <a name="add-percolate-from-the-gallery"></a>ギャラリーから Percolate を追加する

Azure AD への Percolate の統合を構成するには、ギャラリーからマネージド SaaS アプリの一覧に Percolate を追加する必要があります。

1. [Azure portal](https://portal.azure.com) の左側のウィンドウで、 **[Azure Active Directory]** を選択します。

    ![[Azure Active Directory] を選択します。](common/select-azuread.png)

2. **[エンタープライズ アプリケーション]**  >  **[すべてのアプリケーション]** の順に移動します。

    ![[エンタープライズ アプリケーション] ブレード](common/enterprise-applications.png)

3. アプリケーションを追加するには、ウィンドウの上部の **[新しいアプリケーション]** を選択します。

    ![[新しいアプリケーション] を選択する](common/add-new-app.png)

4. 検索ボックスに「**Percolate**」と入力します。 検索結果で **[Percolate]** を選択し、 **[追加]** を選択します。

     ![[検索結果]](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成とテスト

このセクションでは、Britta Simon という名前のテスト ユーザーを使用して、Percolate で Azure AD のシングル サインオンを構成およびテストします。
シングル サインオンを有効にするには、Azure AD ユーザーと Percolate の対応するユーザーの間で、関係を確立する必要があります。

Percolate で Azure AD のシングル サインオンを構成およびテストするには、以下の手順を完了する必要があります。

1. **[Azure AD のシングル サインオンを構成](#configure-azure-ad-single-sign-on)** して、この機能をユーザーに対して有効にします。
2. アプリケーション側で **[Percolate シングル サインオンを構成](#configure-percolate-single-sign-on)** します。
3. Azure AD のシングル サインオンをテストするための **[Azure AD テスト ユーザーを作成](#create-an-azure-ad-test-user)** します。
4. **[Azure AD テスト ユーザーを割り当て](#assign-the-azure-ad-test-user)** て、Azure AD のシングル サインオンをそのユーザーに対して有効にします。
5. ユーザーの Azure AD 表現にリンクされた **[Percolate テスト ユーザーを作成](#create-a-percolate-test-user)** します。
6. **[シングル サインオンをテスト](#test-single-sign-on)** して、この構成が機能することを確認します。

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成

このセクションでは、Azure portal で Azure AD のシングル サインオンを有効にします。

Percolate で Azure AD シングル サインオンを構成するには、次の手順を実行します。

1. [Azure portal](https://portal.azure.com/) の **Percolate** アプリケーション統合ページで、 **[シングル サインオン]** を選択します。

    ![[シングル サインオン] の選択](common/select-sso.png)

2. **[シングル サインオン方式の選択]** ダイアログ ボックスで、 **[SAML/WS-Fed]** モードを選択して、シングル サインオンを有効にします。

    ![シングル サインオン方式の選択](common/select-saml-option.png)

3. **[SAML でシングル サインオンをセットアップします]** ページで、**編集** アイコンを選択して **[基本的な SAML 構成]** ダイアログ ボックスを開きます。

    ![[編集] アイコン](common/edit-urls.png)

4. **[基本的な SAML 構成]** ダイアログ ボックスでは、IdP-initiated モードでアプリケーションを構成するためのアクションは必要ありません。 アプリは既に Azure と統合されています。

    ![[Percolate のドメインと URL] のシングル サインオン情報](common/preintegrated.png)

5. SP-initiated モードでアプリケーションを構成する場合は、 **[追加の URL を設定します]** を選択し、 **[サインオン URL]** ボックスに **https://percolate.com/app/login** と入力します。

   ![[追加の URL を設定します] のスクリーンショット。[サインオン URL] というテキスト ボックスが強調表示されています。](common/metadata-upload-additional-signon.png)
6. **[SAML でシングル サインオンをセットアップします]** ページの **[SAML 署名証明書]** セクションで、**コピー** アイコンを選択して **[アプリのフェデレーション メタデータ URL]** をコピーします。 この URL を保存します。

    ![アプリのフェデレーション メタデータ URL のコピー](common/copy-metadataurl.png)

7. **[Percolate のセットアップ]** セクションで、要件に基づいて適切な URL をコピーします。

    ![構成 URL をコピーする](common/copy-configuration-urls.png)

    1. **[ログイン URL]** 。

    1. **[Azure AD 識別子]** 。

    1. **[ログアウト URL]** 。

### <a name="configure-percolate-single-sign-on"></a>Percolate シングル サインオンの構成

1. 新しい Web ブラウザー ウィンドウで、管理者として Percolate にサインインします。

2. ホーム ページの左側で、 **[設定]** を選択します。
    
    ![[設定] を選択する](./media/percolate-tutorial/configure01.png)

3. 左側のウィンドウで、 **[組織]** の下にある **[SSO]** を選択します。

    ![[組織] の下にある [SSO] の選択](./media/percolate-tutorial/configure02.png)

    1. **[ログイン URL]** ボックスに、Azure portal からコピーした **[ログイン URL]** の値を貼り付けます。

    1. **[エンティティ ID]** ボックスに、Azure portal からコピーした **[Azure AD 識別子]** の値を貼り付けます。

    1. メモ帳で、Azure portal からダウンロードした base 64 エンコード証明書を開きます。 その内容をコピーして、 **[x509 certificates]\(x509 証明書\)** ボックスに貼り付けます。

    1. **[Email attribute]\(電子メール属性\)** ボックスに、「**emailaddress**」と入力します。

    1. **[Identity provider metadata URL]\(ID プロバイダー メタデータ URL\)** ボックスはオプションのフィールドです。 **[アプリのフェデレーション メタデータ URL]** を Azure portal からコピーした場合、それをこのボックスに貼り付けることができます。

    1. **[Should AuthNRequests be signed?]\(AuthNRequests には署名が必要ですか?\)** リストで、 **[いいえ]** を選択します。

    1. **[Enable SSO auto-provisioning]\(SSO の自動プロビジョニングを有効にする\)** リストで、 **[いいえ]** を選択します。

    1. **[保存]** を選択します。

### <a name="create-an-azure-ad-test-user"></a>Azure AD のテスト ユーザーの作成

このセクションでは、Azure portal で Britta Simon という名前のテスト ユーザーを作成します。

1. Azure portal で、左側のウィンドウの **[Azure Active Directory]** を選択し、 **[ユーザー]** 、 **[すべてのユーザー]** の順に選択します。

    ![[すべてのユーザー] を選択する](common/users.png)

2. 画面の上部にある **[新しいユーザー]** を選択します。

    ![[新しいユーザー] を選択する](common/new-user.png)

3. **[ユーザー]** ダイアログ ボックスで、次の手順を実行します。

    ![[ユーザー] ダイアログ ボックス](common/user-properties.png)

    1. **[名前]** ボックスに「**BrittaSimon**」と入力します。
  
    1. **[ユーザー名]** ボックスに「**BrittaSimon@\<yourcompanydomain>.\<extension>** 」と入力します (例: BrittaSimon@contoso.com)。

    1. **[パスワードを表示]** を選択し、 **[パスワード]** ボックス内の値を書き留めます。

    1. **［作成］** を選択します

### <a name="assign-the-azure-ad-test-user"></a>Azure AD テスト ユーザーの割り当て

このセクションでは、Britta Simon に Percolate へのアクセスを許可することで、このユーザーが Azure AD シングル サインオンを使用できるようにします。

1. Azure portal で **[エンタープライズ アプリケーション]** を選択し、 **[すべてのアプリケーション]** を選択してから、 **[Percolate]** を選択します。

    ![[エンタープライズ アプリケーション] ブレード](common/enterprise-applications.png)

2. アプリケーションの一覧で、 **[Percolate]** を選択します。

    ![アプリケーションの一覧](common/all-applications.png)

3. 左側のウィンドウで **[ユーザーとグループ]** を選択します。

    ![[ユーザーとグループ] が左のウィンドウで選択されている画面のスクリーンショット。](common/users-groups-blade.png)

4. **[ユーザーの追加]** を選択し、 **[割り当ての追加]** ダイアログ ボックスで **[ユーザーとグループ]** を選択します。

    ![[ユーザーとグループ] の選択](common/add-assign-user.png)

5. **[ユーザーとグループ]** ダイアログ ボックスで、ユーザーの一覧で **[Britta Simon]** を選択し、画面の下部にある **[選択]** ボタンをクリックします。

6. SAML アサーション内にロール値が必要な場合、 **[ロールの選択]** ダイアログ ボックスで、一覧からユーザーに適したロールを選択します。 画面の下部にある **[選択]** ボタンをクリックします。

7. **[割り当ての追加]** ダイアログ ボックスで **[割り当て]** を選びます。

### <a name="create-a-percolate-test-user"></a>Percolate テスト ユーザーの作成

Azure AD ユーザーが Percolate にサインインできるようにするには、それらを Percolate に追加する必要があります。 手動で追加する必要があります。

ユーザー アカウントを作成するには、以下の手順に従います。

1. 管理者として Percolate にサインインします。

2. 左側のウィンドウで、 **[組織]** の下にある **[ユーザー]** を選択します。 **[新しいユーザー]** を選択します。

    ![[新しいユーザー] の選択](./media/percolate-tutorial/configure03.png)

3. **[ユーザーの作成]** ページで、以下の手順を実行します。

    ![[ユーザーの作成] ページ](./media/percolate-tutorial/configure04.png)

    1. **[電子メール]** ボックスに、ユーザーの電子メール アドレスを入力します。 たとえば、「 brittasimon@contoso.com 」のように入力します。

    1. **[フル ネーム]** ボックスに、ユーザーの名前を入力します。 たとえば、「**Brittasimon**」と入力します。

    1. **[ユーザーの作成]** を選択します。

### <a name="test-single-sign-on"></a>シングル サインオンのテスト

ここで、アクセス パネルを使用して Azure AD のシングル サインオン構成をテストする必要があります。

アクセス パネルで [Percolate] タイルを選択すると、SSO を設定した Percolate インスタンスに自動的にサインインします。 詳細については、「[マイ アプリ ポータルでアプリにアクセスして使用する](../user-help/my-apps-portal-end-user-access.md)」を参照してください。

## <a name="additional-resources"></a>その他のリソース

- [SaaS アプリケーションと Azure Active Directory との統合に関するチュートリアル](./tutorial-list.md)

- [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)

- [Azure Active Directory の条件付きアクセスとは](../conditional-access/overview.md)