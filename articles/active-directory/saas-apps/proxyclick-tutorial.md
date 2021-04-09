---
title: 'チュートリアル: Azure Active Directory と Proxyclick の統合 | Microsoft Docs'
description: このチュートリアルでは、Azure Active Directory と Proxyclick の間でシングル サインオンを構成する方法について説明します。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/27/2019
ms.author: jeedes
ms.openlocfilehash: 23ae1a2c1371cda9435ea76f02cebc79c141c904
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "92522242"
---
# <a name="tutorial-azure-active-directory-integration-with-proxyclick"></a>チュートリアル: Azure Active Directory と Proxyclick の統合

このチュートリアルでは、Proxyclick と Azure Active Directory (Azure AD) を統合する方法について説明します。
この統合には、次の利点があります。

* Azure AD を使用して、Proxyclick にアクセスするユーザーを管理できます。
* ユーザーが自分の Azure AD アカウントを使用して自動的に Proxyclick にサインイン (シングル サインオン) するよう指定できます。
* 1 つの中央サイト (Azure ポータル) でアカウントを管理できます。

SaaS アプリと Azure AD の統合の詳細については、「[Azure Active Directory でのアプリケーションへのシングル サインオン](../manage-apps/what-is-single-sign-on.md)」を参照してください。

Azure サブスクリプションをお持ちでない場合は、開始する前に[無料アカウントを作成](https://azure.microsoft.com/free/)してください。

## <a name="prerequisites"></a>前提条件

Proxyclick と Azure AD の統合を構成するには、次のものが必要です。

* Azure AD サブスクリプション。 Azure AD 環境がない場合は、[1 か月の試用版](https://azure.microsoft.com/pricing/free-trial/)にサインアップできます。
* シングル サインオンが有効な Proxyclick サブスクリプション。

## <a name="scenario-description"></a>シナリオの説明

このチュートリアルでは、テスト環境で Azure AD のシングル サインオンを構成してテストします。

* Proxyclick では、SP-initiated SSO と IdP-initiated SSO がサポートされます。

## <a name="add-proxyclick-from-the-gallery"></a>ギャラリーから Proxyclick を追加する

Azure AD への Proxyclick の統合を設定するには、ギャラリーからマネージド SaaS アプリの一覧に Proxyclick を追加する必要があります。

1. [Azure portal](https://portal.azure.com) の左側のウィンドウで、 **[Azure Active Directory]** を選択します。

    ![[Azure Active Directory] を選択します。](common/select-azuread.png)

2. **[エンタープライズ アプリケーション]**  >  **[すべてのアプリケーション]** の順に移動します。

    ![[エンタープライズ アプリケーション] ブレード](common/enterprise-applications.png)

3. アプリケーションを追加するには、ウィンドウの上部の **[新しいアプリケーション]** を選択します。

    ![[新しいアプリケーション] を選択する](common/add-new-app.png)

4. 検索ボックスに、「**Proxyclick**」と入力します。 検索結果で **[Proxyclick]** を選択し、**[追加]** を選択します。

     ![[検索結果]](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成とテスト

このセクションでは、Britta Simon という名前のテスト ユーザーを使用して、Proxyclick で Azure AD のシングル サインオンを構成およびテストします。
シングル サインオンを有効にするには、Azure AD ユーザーと Proxyclick の対応するユーザーの間で、リレーションシップを確立する必要があります。

Proxyclick で Azure AD のシングル サインオンを構成およびテストするには、以下の手順を完了する必要があります。

1. **[Azure AD のシングル サインオンを構成](#configure-azure-ad-single-sign-on)** して、この機能をユーザーに対して有効にします。
2. アプリケーション側で **[Proxyclick シングル サインオンを構成](#configure-proxyclick-single-sign-on)** します。
3. Azure AD のシングル サインオンをテストするための **[Azure AD テスト ユーザーを作成](#create-an-azure-ad-test-user)** します。
4. **[Azure AD テスト ユーザーを割り当て](#assign-the-azure-ad-test-user)** て、Azure AD のシングル サインオンをそのユーザーに対して有効にします。
5. ユーザーの Azure AD 表現にリンクされた **[Proxyclick テスト ユーザーを作成](#create-a-proxyclick-test-user)** します。
6. **[シングル サインオンをテスト](#test-single-sign-on)** して、この構成が機能することを確認します。

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成

このセクションでは、Azure portal で Azure AD のシングル サインオンを有効にします。

Proxyclick で Azure AD シングル サインオンを構成するには、次の手順に従います。

1. [Azure portal](https://portal.azure.com/) の Proxyclick アプリケーション統合ページで、**[シングル サインオン]** を選択します。

    ![[シングル サインオン] の選択](common/select-sso.png)

2. **[シングル サインオン方式の選択]** ダイアログ ボックスで、 **[SAML/WS-Fed]** モードを選択して、シングル サインオンを有効にします。

    ![シングル サインオン方式の選択](common/select-saml-option.png)

3. **[SAML でシングル サインオンをセットアップします]** ページで、**編集** アイコンを選択して **[基本的な SAML 構成]** ダイアログ ボックスを開きます。

    ![[編集] アイコン](common/edit-urls.png)

4. **[基本的な SAML 構成]** ダイアログ ボックスで、アプリケーションを IDP 開始モードで構成する場合は、次の手順に従います。

    ![[基本的な SAML 構成] ダイアログ ボックス](common/idp-intiated.png)

    1. **[識別子]** ボックスに、次のパターンで URL を入力します。
   
       `https://saml.proxyclick.com/init/<companyId>`

    1. **[応答 URL]** ボックスに、次のパターンで URL を入力します。

       `https://saml.proxyclick.com/consume/<companyId>`

5. アプリケーションを SP-initiated モードで構成する場合は、 **[追加の URL を設定します]** を選択します。 **[サイン オン URL]** ボックスに、次のパターンの URL を入力します。
   
   `https://saml.proxyclick.com/init/<companyId>`

    ![[Proxyclick のドメインと URL] のシングル サインオン情報](common/metadata-upload-additional-signon.png)

    

    > [!NOTE]
    > これらの値はプレースホルダーです。 実際の識別子、応答 URL、サインオン URL を使用する必要があります。 これらの値を取得する手順については、このチュートリアルの後半で説明します。

6. **[SAML でシングル サインオンをセットアップします]** ページの **[SAML 署名証明書]** セクションで、要件に従って **[証明書 (Base64)]** の横にある **[ダウンロード]** リンクを選択し、証明書をコンピューターに保存します。

    ![証明書のダウンロード リンク](common/certificatebase64.png)

7. **[Set up Proxyclick]\(Proxyclick の設定\)** セクションで、要件に基づいて適切な URL をコピーします。

    ![構成 URL をコピーする](common/copy-configuration-urls.png)

    1. **[ログイン URL]** 。

    1. **[Azure AD 識別子]** 。

    1. **[ログアウト URL]** 。

### <a name="configure-proxyclick-single-sign-on"></a>Proxyclick のシングル サインオンを構成する

1. 新しい Web ブラウザー ウィンドウで、Proxyclick 企業サイトに管理者としてサインインします。

2. **[Account & Settings]\(アカウントと設定\)** を選択します。

    ![[Account & Settings]\(アカウントと設定\) を選択する](./media/proxyclick-tutorial/configure1.png)

3. 下のまでスクロールし **[Integrations]\(統合\)** セクションで、**[SAML]** を選択します。

    ![[SAML] を選択する](./media/proxyclick-tutorial/configure2.png)

4. **[SAML]** セクションで、次の手順に従います。

    ![[SAML] セクション](./media/proxyclick-tutorial/configure3.png)

    1. **[SAML Consumer URL]\(SAML コンシューマー URL\)** の値をコピーし、Azure portal の **[基本的な SAML 構成]** ダイアログ ボックスにある **[応答 URL]** ボックスに貼り付けます。

    1. **[SAML SSO Redirect URL]\(SAML SSO リダイレクト URL\)** の値をコピーし、Azure portal の **[基本的な SAML 構成]** ダイアログ ボックスにある **[サインオン URL]** ボックスと **[識別子]** ボックスに貼り付けます。

    1. **[SAML Request Method]\(SAML 要求メソッド\)** 一覧で、**[HTTP Redirect]\(HTTP リダイレクト\)** を選択します。

    1. **[Issuer]/(発行者/)** ボックスに、Azure portal からコピーした **[Azure AD 識別子]** の値を貼り付けます。

    1. **[SAML 2.0 Endpoint URL]\(SAML 2.0 エンドポイント URL\)** ボックスに、Azure portal からコピーした **[ログイン URL]** の値を貼り付けます。

    1. メモ帳で、Azure portal からダウンロードした証明書ファイルを開きます。 **[証明書]** ボックスにこのファイルの内容を貼り付けます。

    1. **[変更の保存]** を選択します。

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

このセクションでは、Britta Simon に Proxyclick へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

1. Azure portal 上で **[エンタープライズ アプリケーション]** を選択し、**[すべてのアプリケーション]** を選択してから、**[Proxyclick]** を選択します。

    ![[エンタープライズ アプリケーション] ブレード](common/enterprise-applications.png)

2. アプリケーションの一覧で、**[Proxyclick]** を選択します。

    ![アプリケーションの一覧](common/all-applications.png)

3. 左側のウィンドウで **[ユーザーとグループ]** を選択します。

    ![[ユーザーとグループ] の選択](common/users-groups-blade.png)

4. **[ユーザーの追加]** を選択し、 **[割り当ての追加]** ダイアログ ボックスで **[ユーザーとグループ]** を選択します。

    ![[ユーザーの追加] を選択する](common/add-assign-user.png)

5. **[ユーザーとグループ]** ダイアログ ボックスで、ユーザーの一覧で **[Britta Simon]** を選択し、ウィンドウの下部にある **[選択]** ボタンをクリックします。

6. SAML アサーション内にロール値が必要な場合、 **[ロールの選択]** ダイアログ ボックスで、一覧からユーザーに適したロールを選択します。 ウィンドウの下部にある **[選択]** ボタンをクリックします。

7. **[割り当ての追加]** ダイアログ ボックスで **[割り当て]** を選びます。

### <a name="create-a-proxyclick-test-user"></a>Proxyclick テスト ユーザーの作成

Azure AD ユーザーが Proxyclick にサインインできるようにするには、それらを Proxyclick に追加する必要があります。 手動で追加する必要があります。

ユーザー アカウントを作成するには、以下の手順に従います。

1. Proxyclick 企業サイトに管理者としてサインインします。

1. ウィンドウの上部にある **[仕事仲間]** を選択します。

    ![仕事仲間を選択する](./media/proxyclick-tutorial/user1.png)

1. **[Add Colleague]\(仕事仲間の追加\)** を選択します。

    ![[Add Colleague]\(仕事仲間の追加\) を選択する](./media/proxyclick-tutorial/user2.png)

1. **[Add a colleague]\(仕事仲間の追加\)** セクションで、次の手順に従います。

    ![[Add a colleague]\(仕事仲間の追加\) セクション](./media/proxyclick-tutorial/user3.png)

    1. **[電子メール]** ボックスに、ユーザーの電子メール アドレスを入力します。 この場合は **brittasimon\@contoso.com** です。

    1. **[First Name]\(名\)** ボックスに、ユーザーの名を入力します。 この場合は **Britta** です。

    1. **[Last Name]\(姓\)** ボックスに、ユーザーの姓を入力します。 この場合は **Simon** です。

    1. **[Add User]\(ユーザーの追加\)** を選択します。

### <a name="test-single-sign-on"></a>シングル サインオンのテスト

ここで、アクセス パネルを使用して Azure AD のシングル サインオン構成をテストする必要があります。

アクセス パネル上で [Proxyclick] タイルを選択すると、SSO を設定した Proxyclick インスタンスに自動的にサインインするはずです。 アクセス パネルの詳細については、「[マイ アプリ ポータルでアプリにアクセスして使用する](../user-help/my-apps-portal-end-user-access.md)」を参照してください。

## <a name="additional-resources"></a>その他のリソース

- [SaaS アプリケーションと Azure Active Directory との統合に関するチュートリアル](./tutorial-list.md)

- [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)

- [Azure Active Directory の条件付きアクセスとは](../conditional-access/overview.md)