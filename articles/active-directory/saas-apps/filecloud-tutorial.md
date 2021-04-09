---
title: 'チュートリアル: Azure Active Directory と FileCloud の統合 | Microsoft Docs'
description: Azure Active Directory と FileCloud の間でシングル サインオンを構成する方法について説明します。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 02/12/2019
ms.author: jeedes
ms.openlocfilehash: 34a63ce51870e32ec6e0d8398eda7114b5fcd42b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "92453503"
---
# <a name="tutorial-azure-active-directory-integration-with-filecloud"></a>チュートリアル: Azure Active Directory と FileCloud の統合

このチュートリアルでは、FileCloud と Azure Active Directory (Azure AD) を統合する方法について説明します。
FileCloud と Azure AD の統合には、次の利点があります。

* FileCloud にアクセスする Azure AD ユーザーを制御できます。
* ユーザーが自分の Azure AD アカウントで FileCloud に自動的にサインイン (シングル サインオン) するように設定できます。
* 1 つの中央サイト (Azure Portal) でアカウントを管理できます。

SaaS アプリと Azure AD の統合の詳細については、「 [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)」を参照してください。
Azure サブスクリプションをお持ちでない場合は、開始する前に[無料アカウントを作成](https://azure.microsoft.com/free/)してください。

## <a name="prerequisites"></a>前提条件

FileCloud と Azure AD の統合を構成するには、次のものが必要です。

* Azure AD サブスクリプション。 Azure AD の環境がない場合は、[こちら](https://azure.microsoft.com/pricing/free-trial/)から 1 か月の評価版を入手できます
* FileCloud でのシングル サインオンが有効なサブスクリプション

## <a name="scenario-description"></a>シナリオの説明

このチュートリアルでは、テスト環境で Azure AD のシングル サインオンを構成してテストします。

* FileCloud では、**SP** Initiated SSO がサポートされます

* FileCloud では、**Just-In-Time** ユーザー プロビジョニングがサポーされます

## <a name="adding-filecloud-from-the-gallery"></a>ギャラリーからの FileCloud の追加

Azure AD への FileCloud の統合を構成するには、ギャラリーから管理対象 SaaS アプリの一覧に FileCloud を追加する必要があります。

**ギャラリーから FileCloud を追加するには、次の手順を実行します。**

1. **[Azure Portal](https://portal.azure.com)** の左側のナビゲーション ウィンドウで、 **[Azure Active Directory]** アイコンをクリックします。

    ![Azure Active Directory のボタン](common/select-azuread.png)

2. **[エンタープライズ アプリケーション]** に移動し、 **[すべてのアプリケーション]** オプションを選択します。

    ![[エンタープライズ アプリケーション] ブレード](common/enterprise-applications.png)

3. 新しいアプリケーションを追加するには、ダイアログの上部にある **[新しいアプリケーション]** をクリックします。

    ![[新しいアプリケーション] ボタン](common/add-new-app.png)

4. 検索ボックスに「**FileCloud**」と入力し、結果ウィンドウで **[FileCloud]** を選択し、 **[追加]** をクリックして、アプリケーションを追加します。

     ![結果一覧の FileCloud](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成とテスト

このセクションでは、**Britta Simon** というテスト ユーザーに基づいて、FileCloud で Azure AD のシングル サインオンを構成し、テストします。
シングル サインオンを機能させるには、Azure AD ユーザーと FileCloud 内の関連ユーザーとの間にリンク関係が確立されている必要があります。

FileCloud で Azure AD のシングル サインオンを構成してテストするには、次の構成要素を完了する必要があります。

1. **[Azure AD シングル サインオンの構成](#configure-azure-ad-single-sign-on)** - ユーザーがこの機能を使用できるようにします。
2. **[FileCloud シングル サインオンの構成](#configure-filecloud-single-sign-on)** - アプリケーション側でシングル サインオン設定を構成します。
3. **[Azure AD のテスト ユーザーの作成](#create-an-azure-ad-test-user)** - Britta Simon で Azure AD のシングル サインオンをテストします。
4. **[Azure AD テスト ユーザーの割り当て](#assign-the-azure-ad-test-user)** - Britta Simon が Azure AD シングル サインオンを使用できるようにします。
5. **[FileCloud のテスト ユーザーの作成](#create-filecloud-test-user)** - FileCloud で Britta Simon に対応するユーザーを作成し、Azure AD の Britta Simon にリンクさせます。
6. **[シングル サインオンのテスト](#test-single-sign-on)** - 構成が機能するかどうかを確認します。

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成

このセクションでは、Azure portal 上で Azure AD のシングル サインオンを有効にします。

FileCloud で Azure AD シングル サインオンを構成するには、次の手順に従います。

1. [Azure portal](https://portal.azure.com/) の **FileCloud** アプリケーション統合ページで、 **[シングル サインオン]** を選択します。

    ![シングル サインオン構成のリンク](common/select-sso.png)

2. **[シングル サインオン方式の選択]** ダイアログで、 **[SAML/WS-Fed]** モードを選択して、シングル サインオンを有効にします。

    ![シングル サインオン選択モード](common/select-saml-option.png)

3. **[SAML でシングル サインオンをセットアップします]** ページで、 **[編集]** アイコンをクリックして **[基本的な SAML 構成]** ダイアログを開きます。

    ![基本的な SAML 構成を編集する](common/edit-urls.png)

4. **[基本的な SAML 構成]** セクションで、次の手順を実行します。

    ![[FileCloud のドメインと URL] のシングル サインオン情報](common/sp-identifier.png)

    a. **[サインオン URL]** ボックスに、次のパターンを使用して URL を入力します。`https://<subdomain>.filecloudonline.com`

    b. **[識別子 (エンティティ ID)]** ボックスに、次のパターンを使用して URL を入力します。`https://<subdomain>.filecloudonline.com/simplesaml/module.php/saml/sp/metadata.php/default-sp`

    > [!NOTE]
    > これらは実際の値ではありません。 実際のサインオン URL と識別子でこれらの値を更新します。 これらの値を取得するには、[FileCloud クライアント サポート チーム](mailto:support@codelathe.com)に連絡してください。 Azure portal の **[基本的な SAML 構成]** セクションに示されているパターンを参照することもできます。

5. **[SAML でシングル サインオンをセットアップします]** ページの **[SAML 署名証明書]** セクションで、 **[ダウンロード]** をクリックして、要件のとおりに指定したオプションから **フェデレーション メタデータ XML** をダウンロードして、お使いのコンピューターに保存します。

    ![証明書のダウンロードのリンク](common/metadataxml.png)

6. **[FileCloud のセットアップ]** セクションで、要件に従って適切な URL をコピーします。

    ![構成 URL のコピー](common/copy-configuration-urls.png)

    a. ログイン URL

    b. Azure AD 識別子

    c. ログアウト URL

### <a name="configure-filecloud-single-sign-on"></a>FileCloud のシングル サインオンの構成

1. 別の Web ブラウザーのウィンドウで、管理者として FileCloud テナントにサインオンします。

2. 左側のナビゲーション ウィンドウで、 **[Settings (設定)]** をクリックします。 
   
    ![左側のナビゲーション ペインで強調表示されている [Settings]\(設定\) を示すスクリーンショット。](./media/filecloud-tutorial/tutorial_filecloud_000.png)

3. [Settings (設定)] セクションの **[SSO]** タブをクリックします。 
   
    ![[S S O] タブが選択されている [Settings]\(設定\) セクションを示すスクリーンショット。](./media/filecloud-tutorial/tutorial_filecloud_001.png)

4. **[Single Sign On (SSO) Settings (シングル サインオン (SSO) 設定)]** パネルで、 **[Default SSO Type (既定の SSO タイプ)]** として **[SAML]** を選択します。
   
    ![[S A M L] が選択されている [Single Sign On (S S O) Settings]\(シングル サインオン (S S O) 設定\) パネルを示すスクリーンショット。](./media/filecloud-tutorial/tutorial_filecloud_002.png)

5. **[IdP End Point URL]\(IdP のエンド ポイント URL\)** ボックスに、Azure portal からコピーした **Azure AD 識別子** の値を貼り付けます。

    ![[I d P End Point U R L]\(I d P のエンド ポイント U R L\) が強調表示されている [S A M L Settings]\(S A M L 設定\) セクションを示すスクリーンショット。](./media/filecloud-tutorial/tutorial_filecloud_003.png)

6. ダウンロードしたメタデータ ファイルをメモ帳で開き、その内容をクリップボードにコピーし、 **[SAML Settings (SAML 設定)]** パネルの **[IdP Meta Data (IdP メタ データ)]** ボックスに貼りつけます。

    ![アプリ側でのシングル サインオンの構成](./media/filecloud-tutorial/tutorial_filecloud_004.png)

7. **[保存]** ボタンをクリックします。

### <a name="create-an-azure-ad-test-user"></a>Azure AD のテスト ユーザーの作成 

このセクションの目的は、Azure Portal で Britta Simon というテスト ユーザーを作成することです。

1. Azure portal の左側のウィンドウで、 **[Azure Active Directory]** 、 **[ユーザー]** 、 **[すべてのユーザー]** の順に選択します。

    ![[ユーザーとグループ] と [すべてのユーザー] リンク](common/users.png)

2. 画面の上部にある **[新しいユーザー]** を選択します。

    ![[新しいユーザー] ボタン](common/new-user.png)

3. [ユーザーのプロパティ] で、次の手順を実行します。

    ![[ユーザー] ダイアログ ボックス](common/user-properties.png)

    a. **[名前]** フィールドに「**BrittaSimon**」と入力します。
  
    b. **[User name]\(ユーザー名\)** フィールドに「**brittasimon\@yourcompanydomain.extension**」と入力します。  
    たとえば、BrittaSimon@contoso.com のように指定します。

    c. **[パスワードを表示]** チェック ボックスをオンにし、[パスワード] ボックスに表示された値を書き留めます。

    d. **Create** をクリックしてください。

### <a name="assign-the-azure-ad-test-user"></a>Azure AD テスト ユーザーの割り当て

このセクションでは、Britta Simon に FileCloud へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

1. Azure portal 上で **[エンタープライズ アプリケーション]** を選択し、 **[すべてのアプリケーション]** を選択してから、 **[FileCloud]** を選択します。

    ![[エンタープライズ アプリケーション] ブレード](common/enterprise-applications.png)

2. アプリケーションの一覧で **[FileCloud]** を選択します。

    ![アプリケーションの一覧の FileCloud のリンク](common/all-applications.png)

3. 左側のメニューで **[ユーザーとグループ]** を選びます。

    ![[ユーザーとグループ] リンク](common/users-groups-blade.png)

4. **[ユーザーの追加]** をクリックし、 **[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。

    ![[割り当ての追加] ウィンドウ](common/add-assign-user.png)

5. **[ユーザーとグループ]** ダイアログの [ユーザー] の一覧で **[Britta Simon]** を選択し、画面の下部にある **[選択]** ボタンをクリックします。

6. SAML アサーション内に任意のロール値が必要な場合、 **[ロールの選択]** ダイアログでユーザーに適したロールを一覧から選択し、画面の下部にある **[選択]** をクリッします。

7. **[割り当ての追加]** ダイアログで、 **[割り当て]** ボタンをクリックします。

### <a name="create-filecloud-test-user"></a>FileCloud のテスト ユーザーの作成

このセクションでは、Britta Simon というユーザーを FileCloud に作成します。 FileCloud では、Just-In-Time ユーザー プロビジョニングがサポートされています。この設定は既定で有効になっています。 このセクションでは、ユーザー側で必要な操作はありません。 FileCloud にユーザーがまだ存在していない場合は、認証後に新規に作成されます。

>[!NOTE]
>ユーザーを手動で作成する必要がある場合は、[FileCloud クライアント サポート チーム](mailto:support@codelathe.com)に問い合わせてください。

### <a name="test-single-sign-on"></a>シングル サインオンのテスト 

このセクションでは、アクセス パネルを使用して Azure AD のシングル サインオン構成をテストします。

アクセス パネル上で [FileCloud] タイルをクリックすると、SSO を設定した FileCloud に自動的にサインインします。 アクセス パネルの詳細については、[アクセス パネルの概要](../user-help/my-apps-portal-end-user-access.md)に関する記事を参照してください。

## <a name="additional-resources"></a>その他のリソース

- [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](./tutorial-list.md)

- [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)

- [Azure Active Directory の条件付きアクセスとは](../conditional-access/overview.md)