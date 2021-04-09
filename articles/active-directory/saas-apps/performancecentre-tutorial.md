---
title: 'チュートリアル: Azure Active Directory と PerformanceCentre の統合 | Microsoft Docs'
description: Azure Active Directory と PerformanceCentre の間でシングル サインオンを構成する方法について説明します。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/14/2019
ms.author: jeedes
ms.openlocfilehash: 79255b98312b16fa21e07abad701e08578c6ca89
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "92515510"
---
# <a name="tutorial-azure-active-directory-integration-with-performancecentre"></a>チュートリアル: Azure Active Directory と PerformanceCentre の統合

このチュートリアルでは、PerformanceCentre と Azure Active Directory (Azure AD) を統合する方法について説明します。
PerformanceCentre と Azure AD の統合には、次の利点があります。

* PerformanceCentre にアクセスする Azure AD ユーザーを制御できます。
* ユーザーが自分の Azure AD アカウントを使用して PerformanceCentre に自動的にサインイン (シングル サインオン) できるようにすることができます。
* 1 つの中央サイト (Azure Portal) でアカウントを管理できます。

SaaS アプリと Azure AD の統合の詳細については、「 [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)」を参照してください。
Azure サブスクリプションをお持ちでない場合は、開始する前に[無料アカウントを作成](https://azure.microsoft.com/free/)してください。

## <a name="prerequisites"></a>前提条件

PerformanceCentre と Azure AD の統合を構成するには、次のものが必要です。

* Azure AD サブスクリプション。 Azure AD の環境がない場合は、[こちら](https://azure.microsoft.com/pricing/free-trial/)から 1 か月の評価版を入手できます
* PerformanceCentre でのシングル サインオンが有効なサブスクリプション

## <a name="scenario-description"></a>シナリオの説明

このチュートリアルでは、テスト環境で Azure AD のシングル サインオンを構成してテストします。

* PerformanceCentre では、**SP** Initiated SSO がサポートされます

## <a name="adding-performancecentre-from-the-gallery"></a>ギャラリーからの PerformanceCentre の追加

Azure AD への PerformanceCentre の統合を構成するには、ギャラリーから管理対象 SaaS アプリの一覧に PerformanceCentre を追加する必要があります。

**ギャラリーから PerformanceCentre を追加するには、次の手順に従います。**

1. **[Azure Portal](https://portal.azure.com)** の左側のナビゲーション ウィンドウで、 **[Azure Active Directory]** アイコンをクリックします。

    ![Azure Active Directory のボタン](common/select-azuread.png)

2. **[エンタープライズ アプリケーション]** に移動し、 **[すべてのアプリケーション]** オプションを選択します。

    ![[エンタープライズ アプリケーション] ブレード](common/enterprise-applications.png)

3. 新しいアプリケーションを追加するには、ダイアログの上部にある **[新しいアプリケーション]** をクリックします。

    ![[新しいアプリケーション] ボタン](common/add-new-app.png)

4. 検索ボックスに「**PerformanceCentre**」と入力し、結果パネルで **PerformanceCentre** を選び、 **[追加]** をクリックして、アプリケーションを追加します。

     ![結果リストの PerformanceCentre](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成とテスト

このセクションでは、**Britta Simon** というテスト ユーザーに基づいて、PerformanceCentre で Azure AD のシングル サインオンを構成し、テストします。
シングル サインオンを機能させるには、Azure AD ユーザーと PerformanceCentre 内の関連ユーザーとの間にリンク関係が確立されている必要があります。

PerformanceCentre で Azure AD のシングル サインオンを構成し、テストするには、次の要素を完了する必要があります。

1. **[Azure AD シングル サインオンの構成](#configure-azure-ad-single-sign-on)** - ユーザーがこの機能を使用できるようにします。
2. **[PerformanceCentre シングル サインオンの構成](#configure-performancecentre-single-sign-on)** - アプリケーション側でシングル サインオン設定を構成します。
3. **[Azure AD のテスト ユーザーの作成](#create-an-azure-ad-test-user)** - Britta Simon で Azure AD のシングル サインオンをテストします。
4. **[Azure AD テスト ユーザーの割り当て](#assign-the-azure-ad-test-user)** - Britta Simon が Azure AD シングル サインオンを使用できるようにします。
5. **[PerformanceCentre のテスト ユーザーの作成](#create-performancecentre-test-user)** - PerformanceCentre で Britta Simon に対応するユーザーを作成し、Azure AD の Britta Simon にリンクさせます。
6. **[シングル サインオンのテスト](#test-single-sign-on)** - 構成が機能するかどうかを確認します。

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成

このセクションでは、Azure portal 上で Azure AD のシングル サインオンを有効にします。

PerformanceCentre で Azure AD シングル サインオンを構成するには、次の手順に従います。

1. [Azure portal](https://portal.azure.com/) の **PerformanceCentre** アプリケーション統合ページで、 **[シングル サインオン]** を選択します。

    ![シングル サインオン構成のリンク](common/select-sso.png)

2. **[シングル サインオン方式の選択]** ダイアログで、 **[SAML/WS-Fed]** モードを選択して、シングル サインオンを有効にします。

    ![シングル サインオン選択モード](common/select-saml-option.png)

3. **[SAML でシングル サインオンをセットアップします]** ページで、 **[編集]** アイコンをクリックして **[基本的な SAML 構成]** ダイアログを開きます。

    ![基本的な SAML 構成を編集する](common/edit-urls.png)

4. **[基本的な SAML 構成]** セクションで、次の手順を実行します。

    ![[PerformanceCentre のドメインと URL] のシングル サインオン情報](common/sp-identifier.png)

    a. **[サインオン URL]** ボックスに、次のパターンを使用して URL を入力します。`http://<companyname>.performancecentre.com/saml/SSO`

    b. **[識別子 (エンティティ ID)]** ボックスに、次のパターンを使用して URL を入力します。`http://<companyname>.performancecentre.com`

    > [!NOTE]
    > これらは実際の値ではありません。 実際のサインオン URL と識別子でこれらの値を更新します。 これらの値を取得するには、[PerformanceCentre クライアント サポート チーム](https://www.performio.co/contact-us)に問い合わせてください。 Azure portal の **[基本的な SAML 構成]** セクションに示されているパターンを参照することもできます。

4. **[SAML でシングル サインオンをセットアップします]** ページの **[SAML 署名証明書]** セクションで、 **[ダウンロード]** をクリックして、要件のとおりに指定したオプションから **フェデレーション メタデータ XML** をダウンロードして、お使いのコンピューターに保存します。

    ![証明書のダウンロードのリンク](common/metadataxml.png)

6. **[PerformanceCentre のセットアップ]** セクションで、要件どおりの適切な URL をコピーします。

    ![構成 URL のコピー](common/copy-configuration-urls.png)

    a. ログイン URL

    b. Azure AD 識別子

    c. ログアウト URL

### <a name="configure-performancecentre-single-sign-on"></a>PerformanceCentre シングル サインオンの構成

1. **PerformanceCentre** 企業サイトに管理者としてサインオンします。

2. 左側にあるタブで、 **[Configure]** をクリックします。
   
    ![[PerformanceCenter] メニューのスクリーンショット。[Configure]\(構成\) が選択されています。][10]

3. 左側にあるタブで、 **[Miscellaneous]** 、 **[Single Sign On]** の順にクリックします。
   
    ![[Configure]\(構成\) タブのスクリーンショット。[Miscellaneous]\(その他\) メニューから [Single Sign On]\(シングル サインオン\) が選択されています。][11]

4. **[Protocol]** で **[SAML]** を選択します。
   
    ![[Single Sign On]\(シングル サインオン\) セクションのスクリーンショット。[Protocol]\(プロトコル\) メニューから [SAML] が選択されています。][12]

5. ダウンロードしたメタデータ ファイルをメモ帳で開き、ファイルの内容をコピーして、 **[Identity Provider Metadata]** ボックスに貼り付け、 **[Save]** をクリックします。
   
    ![ID プロバイダー メタデータのテキストボックスのスクリーンショット。][13]

6. **[Entity Base URL]** と **[Entity ID URL]** の値が正しいことを確認します。
    
     ![Azure AD Single Sign-On][14]

### <a name="create-an-azure-ad-test-user"></a>Azure AD のテスト ユーザーの作成 

このセクションの目的は、Azure Portal で Britta Simon というテスト ユーザーを作成することです。

1. Azure portal の左側のウィンドウで、 **[Azure Active Directory]** 、 **[ユーザー]** 、 **[すべてのユーザー]** の順に選択します。

    ![[ユーザーとグループ] と [すべてのユーザー] リンク](common/users.png)

2. 画面の上部にある **[新しいユーザー]** を選択します。

    ![[新しいユーザー] ボタン](common/new-user.png)

3. [ユーザーのプロパティ] で、次の手順を実行します。

    ![[ユーザー] ダイアログ ボックス](common/user-properties.png)

    a. **[名前]** フィールドに「**BrittaSimon**」と入力します。
  
    b. **[ユーザー名]** フィールドに **brittasimon@yourcompanydomain.extension** と入力します。  
    たとえば、BrittaSimon@contoso.com のように指定します。

    c. **[パスワードを表示]** チェック ボックスをオンにし、[パスワード] ボックスに表示された値を書き留めます。

    d. **Create** をクリックしてください。

### <a name="assign-the-azure-ad-test-user"></a>Azure AD テスト ユーザーの割り当て

このセクションでは、Britta Simon に PerformanceCentre へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

1. Azure portal で **[エンタープライズ アプリケーション]** を選択し、 **[すべてのアプリケーション]** 、 **[PerformanceCentre]** の順に選択します。

    ![[エンタープライズ アプリケーション] ブレード](common/enterprise-applications.png)

2. アプリケーションの一覧で **[PerformanceCentre]** を選択します。

    ![アプリケーションの一覧の PerformanceCentre のリンク](common/all-applications.png)

3. 左側のメニューで **[ユーザーとグループ]** を選びます。

    ![[ユーザーとグループ] リンク](common/users-groups-blade.png)

4. **[ユーザーの追加]** をクリックし、 **[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。

    ![[割り当ての追加] ウィンドウ](common/add-assign-user.png)

5. **[ユーザーとグループ]** ダイアログの [ユーザー] の一覧で **[Britta Simon]** を選択し、画面の下部にある **[選択]** ボタンをクリックします。

6. SAML アサーション内に任意のロール値が必要な場合、 **[ロールの選択]** ダイアログでユーザーに適したロールを一覧から選択し、画面の下部にある **[選択]** をクリッします。

7. **[割り当ての追加]** ダイアログで、 **[割り当て]** ボタンをクリックします。

### <a name="create-performancecentre-test-user"></a>PerformanceCentre のテスト ユーザーの作成

このセクションの目的は、PerformanceCentre で Britta Simon というユーザーを作成することです。

**PerformanceCentre で Britta Simon というユーザーを作成するには、次の手順に従います。**

1. PerformanceCentre 企業サイトに管理者としてサインオンします。

2. 左側のメニューで、 **[Interrelate]** 、 **[Create Participant]** の順にクリックします。
   
    ![[PerformanceCenter] 企業サイトの [Interrelate -Participants]\(相互関連 - 参加者\) ページのスクリーンショット。[Create Participant]\(参加者の作成\) ボタンが選択されています。][400]

3. **[Interrelate - Create Participant]** ダイアログ ボックスで、次の手順を実行します。
   
    ![[Create User]][401]
    
    a. 関連するテキスト ボックスに Britta Simon の必要な属性を入力します。
    
    >[!IMPORTANT]
    >PerformanceCentre での Britta の User Name 属性は、Azure AD でのユーザー名と同じにする必要があります。
    
    b. **[ロールの選択]** で **[クライアント管理者]** を選択します。
    
    c. **[保存]** をクリックします。 

### <a name="test-single-sign-on"></a>シングル サインオンのテスト 

このセクションでは、アクセス パネルを使用して Azure AD のシングル サインオン構成をテストします。

アクセス パネル上で [PerformanceCentre] タイルをクリックすると、SSO を設定した PerformanceCentre に自動的にサインインします。 アクセス パネルの詳細については、[アクセス パネルの概要](../user-help/my-apps-portal-end-user-access.md)に関する記事を参照してください。

## <a name="additional-resources"></a>その他のリソース

- [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](./tutorial-list.md)

- [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)

- [Azure Active Directory の条件付きアクセスとは](../conditional-access/overview.md)

<!--Image references-->

[10]: ./media/performancecentre-tutorial/tutorial_performancecentre_06.png
[11]: ./media/performancecentre-tutorial/tutorial_performancecentre_07.png
[12]: ./media/performancecentre-tutorial/tutorial_performancecentre_08.png
[13]: ./media/performancecentre-tutorial/tutorial_performancecentre_09.png
[14]: ./media/performancecentre-tutorial/tutorial_performancecentre_10.png
[400]: ./media/performancecentre-tutorial/tutorial_performancecentre_11.png
[401]: ./media/performancecentre-tutorial/tutorial_performancecentre_12.png