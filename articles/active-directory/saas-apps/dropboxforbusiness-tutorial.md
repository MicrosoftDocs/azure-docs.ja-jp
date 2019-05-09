---
title: チュートリアル:Azure Active Directory と Dropbox for Business の統合 | Microsoft Docs
description: Azure Active Directory と Dropbox for Business の間でシングル サインオンを構成する方法について説明します。
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: 63502412-758b-4b46-a580-0e8e130791a1
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 12/20/2018
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: d55ae4077b3ec14cb8dc2226714b094574ed9522
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/18/2019
ms.locfileid: "58905649"
---
# <a name="tutorial-azure-active-directory-integration-with-dropbox-for-business"></a>チュートリアル:Azure Active Directory と Dropbox for Business の統合

このチュートリアルでは、Dropbox for Business と Azure Active Directory (Azure AD) を統合する方法について説明します。
Dropbox for Business と Azure AD の統合には、次の利点があります。

* Dropbox for Business にアクセスする Azure AD ユーザーを制御できます。
* ユーザーが自分の Azure AD アカウントを使用して自動的に Dropbox for Business にサインイン (シングル サインオン) できるようにすることが可能です。
* 1 つの中央サイト (Azure Portal) でアカウントを管理できます。

SaaS アプリと Azure AD の統合の詳細については、「 [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)」を参照してください。
Azure サブスクリプションをお持ちでない場合は、開始する前に[無料アカウントを作成](https://azure.microsoft.com/free/)してください。

## <a name="prerequisites"></a>前提条件

Azure AD と Dropbox for Business の統合を構成するには、次のものが必要です。

* Azure AD サブスクリプション。 Azure AD の環境がない場合は、[こちら](https://azure.microsoft.com/pricing/free-trial/)から 1 か月の評価版を入手できます
* Dropbox for Business でのシングル サインオンが有効なサブスクリプション

## <a name="scenario-description"></a>シナリオの説明

このチュートリアルでは、テスト環境で Azure AD のシングル サインオンを構成してテストします。

* Dropbox for Business では、**SP** によって開始される SSO がサポートされます

* Dropbox for Business では、**Just-In-Time** ユーザー プロビジョニングがサポートされます

## <a name="adding-dropbox-for-business-from-the-gallery"></a>ギャラリーから Dropbox for Business を追加

Azure AD への Dropbox for Business の統合を構成するには、ギャラリーから管理対象 SaaS アプリの一覧に Dropbox for Business を追加する必要があります。

**ギャラリーから Dropbox for Business を追加するには、次の手順に従います。**

1. **[Azure Portal](https://portal.azure.com)** の左側のナビゲーション ウィンドウで、**[Azure Active Directory]** アイコンをクリックします。

    ![Azure Active Directory のボタン](common/select-azuread.png)

2. **[エンタープライズ アプリケーション]** に移動し、**[すべてのアプリケーション]** オプションを選択します。

    ![[エンタープライズ アプリケーション] ブレード](common/enterprise-applications.png)

3. 新しいアプリケーションを追加するには、ダイアログの上部にある **[新しいアプリケーション]** をクリックします。

    ![[新しいアプリケーション] ボタン](common/add-new-app.png)

4. 検索ボックスに「**Dropbox for Business**」と入力し、結果パネルで **[Dropbox for Business]** を選び、**[追加]** をクリックして、アプリケーションを追加します。

     ![結果リストの Dropbox for Business](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成とテスト

このセクションでは、**Britta Simon** というテスト ユーザーに基づいて、Dropbox for Business で Azure AD のシングル サインオンを構成し、テストします。
シングル サインオンを機能させるには、Azure AD ユーザーと Dropbox for Business 内の関連ユーザーとの間にリンク関係が確立されている必要があります。

Dropbox for Business で Azure AD のシングル サインオンを構成してテストするには、次の構成要素を完了する必要があります。

1. **[Azure AD シングル サインオンの構成](#configure-azure-ad-single-sign-on)** - ユーザーがこの機能を使用できるようにします。
2. **[Dropbox for Business シングル サインオンの構成](#configure-dropbox-for-business-single-sign-on)** - アプリケーション側でシングル サインオン設定を構成します。
3. **[Azure AD のテスト ユーザーの作成](#create-an-azure-ad-test-user)** - Britta Simon で Azure AD のシングル サインオンをテストします。
4. **[Azure AD テスト ユーザーの割り当て](#assign-the-azure-ad-test-user)** - Britta Simon が Azure AD シングル サインオンを使用できるようにします。
5. **[Dropbox for Business のテスト ユーザーの作成](#create-dropbox-for-business-test-user)** - Dropbox for Business で Britta Simon に対応するユーザーを作成し、Azure AD の Britta Simon にリンクさせます。
6. **[シングル サインオンのテスト](#test-single-sign-on)** - 構成が機能するかどうかを確認します。

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成

このセクションでは、Azure portal 上で Azure AD のシングル サインオンを有効にします。

Dropbox for Business で Azure AD シングル サインオンを構成するには、次の手順に従います。

1. [Azure portal](https://portal.azure.com/) の **Dropbox for Business** アプリケーション統合ページで、**[シングル サインオン]** を選択します。

    ![シングル サインオン構成のリンク](common/select-sso.png)

2. **[シングル サインオン方式の選択]** ダイアログで、**[SAML/WS-Fed]** モードを選択して、シングル サインオンを有効にします。

    ![シングル サインオン選択モード](common/select-saml-option.png)

3. **[SAML でシングル サインオンをセットアップします]** ページで、**[編集]** アイコンをクリックして **[基本的な SAML 構成]** ダイアログを開きます。

    ![基本的な SAML 構成を編集する](common/edit-urls.png)

4. **[基本的な SAML 構成]** セクションで、次の手順を実行します。

    ![[Dropbox for Business のドメインと URL] のシングル サインオン情報](common/sp-identifier.png)

    a. **[サインオン URL]** ボックスに、次のパターンを使用して URL を入力します。`https://www.dropbox.com/sso/<id>`

    b. **[識別子 (エンティティ ID)]** ボックスに、`Dropbox` という値を入力します。

    > [!NOTE]
    > 上記のサインオン URL の値は、実際の値ではありません。 実際のサインオン URL に値を置き換えます。実際の値については後で説明します。

4. **[SAML でシングル サインオンをセットアップします]** ページの **[SAML 署名証明書]** セクションで、**[ダウンロード]** をクリックして要件のとおりに指定したオプションからの**証明書 (Base64)** をダウンロードして、お使いのコンピューターに保存します。

    ![証明書のダウンロードのリンク](common/certificatebase64.png)

6. **[Dropbox for Business のセットアップ]** セクションで、要件どおりの適切な URL をコピーします。

    ![構成 URL のコピー](common/copy-configuration-urls.png)

    a. ログイン URL

    b. Azure AD 識別子

    c. ログアウト URL

### <a name="configure-dropbox-for-business-single-sign-on"></a>Dropbox for Business シングル サインオンの構成

1. **Dropbox for Business** 側のシングル サインオンを構成するには、Dropbox for Business テナントに移動し、Dropbox for Business テナントにサインインします。

    ![シングル サインオンの構成](./media/dropboxforbusiness-tutorial/ic769509.png "Configure single sign-on")

2. **ユーザー アイコン**をクリックし、**[Settings]\(設定\)** タブを選択します。

    ![シングル サインオンの構成](./media/dropboxforbusiness-tutorial/configure1.png "Configure single sign-on")

3. 左側のナビゲーション ウィンドウで、**[Admin console]\(管理コンソール\)** をクリックします。

    ![シングル サインオンの構成](./media/dropboxforbusiness-tutorial/configure2.png "Configure single sign-on")

4. **[Admin console]\(管理コンソール\)** の左側のナビゲーション ウィンドウで、**[Settings]\(設定\)** をクリックします。

    ![シングル サインオンの構成](./media/dropboxforbusiness-tutorial/configure3.png "Configure single sign-on")

5. **[Authentication]\(認証\)** セクションの **[Single sign-on]\(シングル サインオン\)** オプションを選択します。

    ![シングル サインオンの構成](./media/dropboxforbusiness-tutorial/configure4.png "Configure single sign-on")

6. **[Single sign-on]\(シングル サインオン\)** セクションで、次の手順を実行します。  

    ![シングル サインオンの構成](./media/dropboxforbusiness-tutorial/configure5.png "Configure single sign-on")

    a. **[Single sign-on]\(シングル サインオン\)** のドロップ ダウンからオプションとして **[Required]\(必須\)** を選択します。

    b. **[Add sign-in URL]\(サインイン URL の追加\)** をクリックし、**[Identity provider sign-in URL]\(ID プロバイダーのサインイン URL\)** ボックスに Azure portal からコピーした**ログイン URL** の値を貼り付け、**[完了]** を選択します。

    ![シングル サインオンの構成](./media/dropboxforbusiness-tutorial/configure6.png "Configure single sign-on")

    c. **[Upload certificate]\(証明書のアップロード\)** をクリックし、Azure portal からダウンロードした **Base64 でエンコードされた証明書ファイル**を参照します。

    d. **[Copy link]\(リンクのコピー\)** をクリックし、コピーした値を Azure portal の **[Dropbox for Business のドメインと URL]** セクションの **[サインオン URL]** テキストボックスに貼り付けます。

    e. **[Save]** をクリックします。

### <a name="create-an-azure-ad-test-user"></a>Azure AD のテスト ユーザーの作成 

このセクションの目的は、Azure Portal で Britta Simon というテスト ユーザーを作成することです。

1. Azure portal の左側のウィンドウで、**[Azure Active Directory]**、**[ユーザー]**、**[すべてのユーザー]** の順に選択します。

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

このセクションでは、Britta Simon に Dropbox for Business へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

1. Azure portal で **[エンタープライズ アプリケーション]** を選択し、**[すべてのアプリケーション]**、**[Dropbox for Business]** の順に選択します。

    ![[エンタープライズ アプリケーション] ブレード](common/enterprise-applications.png)

2. アプリケーションの一覧で、「**Dropbox for Business**」と入力して選択します。

    ![アプリケーション一覧の Dropbox for Business リンク](common/all-applications.png)

3. 左側のメニューで **[ユーザーとグループ]** を選びます。

    ![[ユーザーとグループ] リンク](common/users-groups-blade.png)

4. **[ユーザーの追加]** をクリックし、**[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。

    ![[割り当ての追加] ウィンドウ](common/add-assign-user.png)

5. **[ユーザーとグループ]** ダイアログの [ユーザー] の一覧で **[Britta Simon]** を選択し、画面の下部にある **[選択]** ボタンをクリックします。

6. SAML アサーション内に任意のロール値が必要な場合、**[ロールの選択]** ダイアログでユーザーに適したロールを一覧から選択し、画面の下部にある **[選択]** をクリッします。

7. **[割り当ての追加]** ダイアログで、**[割り当て]** ボタンをクリックします。

### <a name="create-dropbox-for-business-test-user"></a>Dropbox for Business のテスト ユーザーの作成

このセクションでは、Britta Simon というユーザーを Dropbox for Business に作成します。 Dropbox for Business では、Just-In-Time ユーザー プロビジョニングがサポートされています。この設定は既定で有効になっています。 このセクションでは、ユーザー側で必要な操作はありません。 Dropbox for Business にユーザーがまだ存在していない場合は、認証後に新規に作成されます。

>[!Note]
>ユーザーを手動で作成する必要がある場合は、[Dropbox for Business クライアント サポート チーム](https://www.dropbox.com/business/contact)にお問い合わせください。

### <a name="test-single-sign-on"></a>シングル サインオンのテスト 

このセクションでは、アクセス パネルを使用して Azure AD のシングル サインオン構成をテストします。

アクセス パネル上で [Dropbox for Business] タイルをクリックすると、SSO を設定した Dropbox for Business に自動的にサインインします。 アクセス パネルの詳細については、[アクセス パネルの概要](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)に関する記事を参照してください。

## <a name="additional-resources"></a>その他のリソース

- [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Azure Active Directory の条件付きアクセスとは](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

