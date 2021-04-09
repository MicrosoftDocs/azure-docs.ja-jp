---
title: 'チュートリアル: Azure Active Directory と Trisotech Digital Enterprise Server の統合 | Microsoft Docs'
description: Azure Active Directory と Trisotech Digital Enterprise Server の間でシングル サインオンを構成する方法について説明します。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/28/2019
ms.author: jeedes
ms.openlocfilehash: 05978b7300c401a0658d6c3316f37735fcb70c20
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "92521936"
---
# <a name="tutorial-azure-active-directory-integration-with-trisotech-digital-enterprise-server"></a>チュートリアル: Azure Active Directory と Trisotech Digital Enterprise Server の統合

このチュートリアルでは、Trisotech Digital Enterprise Server と Azure Active Directory (Azure AD) を統合する方法について説明します。
Trisotech Digital Enterprise Server と Azure AD の統合には、次の利点があります。

* Trisotech Digital Enterprise Server にアクセスする Azure AD ユーザーを制御できます。
* ユーザーが自分の Azure AD アカウントを使用して Trisotech Digital Enterprise Server に自動的にサインイン (シングル サインオン) するように設定できます。
* 1 つの中央サイト (Azure Portal) でアカウントを管理できます。

SaaS アプリと Azure AD の統合の詳細については、「 [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)」を参照してください。
Azure サブスクリプションをお持ちでない場合は、開始する前に[無料アカウントを作成](https://azure.microsoft.com/free/)してください。

## <a name="prerequisites"></a>前提条件

Azure AD と Trisotech Digital Enterprise Server の統合を構成するには、次のものが必要です。

* Azure AD サブスクリプション。 Azure AD の環境がない場合は、[こちら](https://azure.microsoft.com/pricing/free-trial/)から 1 か月の評価版を入手できます
* Trisotech Digital Enterprise Server でのシングル サインオンが有効なサブスクリプション

## <a name="scenario-description"></a>シナリオの説明

このチュートリアルでは、テスト環境で Azure AD のシングル サインオンを構成してテストします。

* Trisotech Digital Enterprise Server では、**SP** Initiated SSO がサポートされます

* Trisotech Digital Enterprise Server では、**Just In Time** ユーザー プロビジョニングがサポートされます

## <a name="adding-trisotech-digital-enterprise-server-from-the-gallery"></a>ギャラリーからの Trisotech Digital Enterprise Server の追加

Azure AD への Trisotech Digital Enterprise Server の統合を構成するには、ギャラリーから管理対象 SaaS アプリの一覧に Trisotech Digital Enterprise Server を追加する必要があります。

**ギャラリーから Trisotech Digital Enterprise Server を追加するには、次の手順に従います。**

1. **[Azure Portal](https://portal.azure.com)** の左側のナビゲーション ウィンドウで、 **[Azure Active Directory]** アイコンをクリックします。

    ![Azure Active Directory のボタン](common/select-azuread.png)

2. **[エンタープライズ アプリケーション]** に移動し、 **[すべてのアプリケーション]** オプションを選択します。

    ![[エンタープライズ アプリケーション] ブレード](common/enterprise-applications.png)

3. 新しいアプリケーションを追加するには、ダイアログの上部にある **[新しいアプリケーション]** をクリックします。

    ![[新しいアプリケーション] ボタン](common/add-new-app.png)

4. 検索ボックスに「**Trisotech Digital Enterprise Server**」と入力し、結果パネルで **[Trisotech Digital Enterprise Server]** を選択し、 **[追加]** をクリックして、アプリケーションを追加します。

     ![結果リスト内の Trisotech Digital Enterprise Server](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成とテスト

このセクションでは、**Britta Simon** というテスト ユーザーに基づいて、Trisotech Digital Enterprise Server で Azure AD のシングル サインオンを構成し、テストします。
シングル サインオンを機能させるには、Azure AD ユーザーと Trisotech Digital Enterprise Server 内の関連ユーザーとの間にリンク関係が確立されている必要があります。

Trisotech Digital Enterprise Server で Azure AD のシングル サインオンを構成してテストするには、次の手順を完了する必要があります。

1. **[Azure AD シングル サインオンの構成](#configure-azure-ad-single-sign-on)** - ユーザーがこの機能を使用できるようにします。
2. **[Trisotech Digital Enterprise Server シングル サインオンの構成](#configure-trisotech-digital-enterprise-server-single-sign-on)** - アプリケーション側でシングル サインオン設定を構成します。
3. **[Azure AD のテスト ユーザーの作成](#create-an-azure-ad-test-user)** - Britta Simon で Azure AD のシングル サインオンをテストします。
4. **[Azure AD テスト ユーザーの割り当て](#assign-the-azure-ad-test-user)** - Britta Simon が Azure AD シングル サインオンを使用できるようにします。
5. **[Trisotech Digital Enterprise Server のテスト ユーザーの作成](#create-trisotech-digital-enterprise-server-test-user)** - Trisotech Digital Enterprise Server で Britta Simon に対応するユーザーを作成し、Azure AD の Britta Simon にリンクさせます。
6. **[シングル サインオンのテスト](#test-single-sign-on)** - 構成が機能するかどうかを確認します。

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成

このセクションでは、Azure portal 上で Azure AD のシングル サインオンを有効にします。

Trisotech Digital Enterprise Server で Azure AD シングル サインオンを構成するには、次の手順を実行します。

1. [Azure portal](https://portal.azure.com/) の **Trisotech Digital Enterprise Server** アプリケーション統合ページで、 **[シングル サインオン]** を選択します。

    ![シングル サインオン構成のリンク](common/select-sso.png)

2. **[シングル サインオン方式の選択]** ダイアログで、 **[SAML/WS-Fed]** モードを選択して、シングル サインオンを有効にします。

    ![シングル サインオン選択モード](common/select-saml-option.png)

3. **[SAML でシングル サインオンをセットアップします]** ページで、 **[編集]** アイコンをクリックして **[基本的な SAML 構成]** ダイアログを開きます。

    ![基本的な SAML 構成を編集する](common/edit-urls.png)

4. **[基本的な SAML 構成]** セクションで、次の手順を実行します。

    ![[Trisotech Digital Enterprise Server のドメインと URL] のシングル サインオン情報](common/sp-identifier.png)

    a. **[サインオン URL]** ボックスに、次のパターンを使用して URL を入力します。`https://<companyname>.trisotech.com`

    b. **[識別子 (エンティティ ID)]** ボックスに、次のパターンを使用して URL を入力します。`https://<companyname>.trisotech.com`

    > [!NOTE]
    > これらは実際の値ではありません。 実際のサインオン URL と識別子でこれらの値を更新します。 これらの値を取得するには、[Trisotech Digital Enterprise Server サポート チーム](mailto:support@trisotech.com)にご連絡ください。 Azure portal の **[基本的な SAML 構成]** セクションに示されているパターンを参照することもできます。

4. **Set up Single Sign-On with SAML\(SAML でのシングルサインオンの設定** ページの **SAML 署名証明書** セクションで、コピー ボタンをクリックして **App Federation Metadata Url\(アプリのフェデレーション メタデータ URL)** をコピーして、コンピューターに保存します。

    ![証明書のダウンロードのリンク](common/copy-metadataurl.png)

### <a name="configure-trisotech-digital-enterprise-server-single-sign-on"></a>Trisotech Digital Enterprise Server シングル サインオンの構成

1. 別の Web ブラウザー ウィンドウで、Trisotech Digital Enterprise Server 構成企業サイトに管理者としてサインインします。

2. **メニュー アイコン** をクリックし、 **[管理]** を選択します。

    ![Microsoft Digital Enterprise Server の [管理] アイコンのスクリーンショット。](./media/trisotechdigitalenterpriseserver-tutorial/user1.png)

3. **[User Provider]\(ユーザー プロバイダー\)** を選択します。

    ![メニューから [User Provider]\(ユーザー プロバイダー\) が選択された画面のスクリーンショット。](./media/trisotechdigitalenterpriseserver-tutorial/user2.png)

4. **[User Provider Configurations]\(ユーザー プロバイダー構成\)** セクションで、次の手順を実行します。

    ![[User Provider Configurations]\(ユーザー プロバイダー構成\) 画面のスクリーンショット。ここで、説明されている値を入力できます。](./media/trisotechdigitalenterpriseserver-tutorial/user3.png)

    a. **[認証方法]** のドロップダウン リストから **[Secured Assertion Markup Language 2 (SAML 2)]** を選択します。

    b. **[メタデータ URL]** テキスト ボックスに、Azure Portal からコピーした **[アプリのフェデレーション メタデータ URL]** 値を貼り付けます。

    c. **[アプリケーション ID]** テキストボックスに、次のパターンを使って URL を入力します: `https://<companyname>.trisotech.com`。

    d. **[保存]**

    e. **[Allowed Domains (empty means everyone)]\(許可されているドメイン (空の場合はすべてのユーザー))** テキストボックスにドメイン名を入力します。許可されているドメインに一致するユーザーのライセンスが自動的に割り当てられます

    f. **[保存]**

### <a name="create-an-azure-ad-test-user"></a>Azure AD のテスト ユーザーの作成 

このセクションの目的は、Azure Portal で Britta Simon というテスト ユーザーを作成することです。

1. Azure portal の左側のウィンドウで、 **[Azure Active Directory]** 、 **[ユーザー]** 、 **[すべてのユーザー]** の順に選択します。

    ![[ユーザーとグループ] と [すべてのユーザー] リンク](common/users.png)

2. 画面の上部にある **[新しいユーザー]** を選択します。

    ![[新しいユーザー] ボタン](common/new-user.png)

3. [ユーザーのプロパティ] で、次の手順を実行します。

    ![[ユーザー] ダイアログ ボックス](common/user-properties.png)

    a. **[名前]** フィールドに「**BrittaSimon**」と入力します。
  
    b. **[ユーザー名]** フィールドに「brittasimon@yourcompanydomain.extension」と入力します。 たとえば、BrittaSimon@contoso.com のように指定します。

    c. **[パスワードを表示]** チェック ボックスをオンにし、[パスワード] ボックスに表示された値を書き留めます。

    d. **Create** をクリックしてください。

### <a name="assign-the-azure-ad-test-user"></a>Azure AD テスト ユーザーの割り当て

このセクションでは、Britta Simon に Trisotech Digital Enterprise Server へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

1. Azure portal で **[エンタープライズ アプリケーション]** を選択し、 **[すべてのアプリケーション]** 、 **[Trisotech Digital Enterprise Server]** の順に選択します。

    ![[エンタープライズ アプリケーション] ブレード](common/enterprise-applications.png)

2. アプリケーションの一覧で **[Trisotech Digital Enterprise Server]** を選択します。

    ![アプリケーションの一覧の Trisotech Digital Enterprise Server のリンク](common/all-applications.png)

3. 左側のメニューで **[ユーザーとグループ]** を選びます。

    ![[ユーザーとグループ] リンク](common/users-groups-blade.png)

4. **[ユーザーの追加]** をクリックし、 **[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。

    ![[割り当ての追加] ウィンドウ](common/add-assign-user.png)

5. **[ユーザーとグループ]** ダイアログの [ユーザー] の一覧で **[Britta Simon]** を選択し、画面の下部にある **[選択]** ボタンをクリックします。

6. SAML アサーション内に任意のロール値が必要な場合、 **[ロールの選択]** ダイアログでユーザーに適したロールを一覧から選択し、画面の下部にある **[選択]** をクリッします。

7. **[割り当ての追加]** ダイアログで、 **[割り当て]** ボタンをクリックします。

### <a name="create-trisotech-digital-enterprise-server-test-user"></a>Trisotech Digital Enterprise Server のテスト ユーザーの作成

このセクションでは、Britta Simon というユーザーを Trisotech Digital Enterprise Server に作成します。 Trisotech Digital Enterprise Server では、Just-In-Time ユーザー プロビジョニングがサポートされており、既定で有効になっています。 このセクションでは、ユーザー側で必要な操作はありません。 Trisotech Digital Enterprise Server にユーザーがまだ存在していない場合は、認証後に新しく作成されます。

>[!Note]
>ユーザーを手動で作成する必要がある場合は、[Trisotech Digital Enterprise Server サポート チーム](mailto:support@trisotech.com)にお問い合わせください。

### <a name="test-single-sign-on"></a>シングル サインオンのテスト 

このセクションでは、アクセス パネルを使用して Azure AD のシングル サインオン構成をテストします。

アクセス パネル上で [Trisotech Digital Enterprise Server] タイルをクリックすると、SSO を設定した Trisotech Digital Enterprise Server に自動的にサインインします。 アクセス パネルの詳細については、[アクセス パネルの概要](../user-help/my-apps-portal-end-user-access.md)に関する記事を参照してください。

## <a name="additional-resources"></a>その他のリソース

- [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](./tutorial-list.md)

- [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)

- [Azure Active Directory の条件付きアクセスとは](../conditional-access/overview.md)