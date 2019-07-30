---
title: チュートリアル:Azure Active Directory と AnswerHub の統合 | Microsoft Docs
description: Azure Active Directory と AnswerHub の間でシングル サインオンを構成する方法について説明します。
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 818b91d7-01df-4b36-9706-f167c710a73c
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 01/17/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: ccf3894daf71be0defe4271a08ec5e8d963e0d34
ms.sourcegitcommit: 920ad23613a9504212aac2bfbd24a7c3de15d549
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/15/2019
ms.locfileid: "68227494"
---
# <a name="tutorial-azure-active-directory-integration-with-answerhub"></a>チュートリアル:Azure Active Directory と AnswerHub の統合

このチュートリアルでは、AnswerHub と Azure Active Directory (Azure AD) を統合する方法について説明します。
AnswerHub と Azure AD の統合には、以下の利点があります。

* Azure AD を使用して、AnswerHub にアクセスするユーザーを管理できます。
* ユーザーが自分の Azure AD アカウントを使用して AnswerHub に自動的にサインイン (シングル サインオン) できるようにできます。
* 中央の場所 (Azure portal) から自分のアカウントを管理できます。

SaaS アプリと Azure AD の統合の詳細については、「[Azure Active Directory でのアプリケーションへのシングル サインオン](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)」を参照してください。
Azure サブスクリプションをお持ちでない場合は、開始する前に[無料アカウントを作成](https://azure.microsoft.com/free/)してください。

## <a name="prerequisites"></a>前提条件

Azure AD と AnswerHub の統合を構成するには、以下が必要です。

* Azure AD サブスクリプション。 Azure AD の環境がない場合は、[1 か月の試用版](https://azure.microsoft.com/pricing/free-trial/)を開始できます。
* シングル サインオンが有効な AnswerHub サブスクリプション。

## <a name="scenario-description"></a>シナリオの説明

このチュートリアルでは、テスト環境で Azure AD のシングル サインオンを構成してテストします。

* AnswerHub では、SP Initiated SSO がサポートされます。

## <a name="add-answerhub-from-the-gallery"></a>ギャラリーからの AnswerHub の追加

AnswerHub の Azure AD への統合を設定するには、ギャラリーから AnswerHub をマネージド SaaS アプリの一覧に追加する必要があります。

**ギャラリーから AnswerHub を追加するには:**

1. [Azure Portal](https://portal.azure.com) の左側のウィンドウで、 **[Azure Active Directory]** を選択します。

    ![[Azure Active Directory] ボタン](common/select-azuread.png)

2. **[エンタープライズ アプリケーション]** に移動し、 **[すべてのアプリケーション]** を選択します。

    ![Enterprise Applications blade](common/enterprise-applications.png)

3. アプリケーションを追加するには、ウィンドウの上部の **[新しいアプリケーション]** を選択します。

    ![[新しいアプリケーション] ボタン](common/add-new-app.png)

4. 検索ボックスに「**AnswerHub**」と入力します。 結果リストで **[AnswerHub]** を選択し、 **[追加]** を選択します。

     ![結果一覧の AnswerHub](common/search-new-app.png)

## <a name="set-up-and-test-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの設定とテスト

このセクションでは、Britta Simon という名前のテスト ユーザーを使用して、AnswerHub で Azure AD のシングル サインオンを構成およびテストします。
シングル サインオンでは、Azure AD ユーザーと AnswerHub の関連ユーザーの間で、リンクを確立する必要があります。

AnswerHub で Azure AD のシングル サインオンを構成およびテストするには、以下のタスクを完了する必要があります。

1. [Azure AD シングル サインオンの構成](#configure-azure-ad-single-sign-on) - ユーザーがこの機能を使用できるようにします。
2. [AnswerHub シングル サインオンの構成](#configure-answerhub-single-sign-on) - アプリケーション側でシングル サインオンの設定を行います。
3. [Azure AD テスト ユーザーの作成](#create-an-azure-ad-test-user) - 名前は Britta Simon にします。
4. [Azure AD テスト ユーザーの割り当て](#assign-the-azure-ad-test-user) - Britta Simon が Azure AD シングル サインオンを使用できるようにします。
5. Azure AD テスト ユーザーに対応し、それにリンクする AnswerHub テスト ユーザーを作成します。
6. [シングル サインオンのテスト](#test-single-sign-on)。構成が機能することを確認します。

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成

このセクションでは、Azure Portal で Azure AD シングル サインオンを設定します。

**AnswerHub で Azure AD シングル サインオンを構成するには:**

1. [Azure portal](https://portal.azure.com/) の **[AnswerHub]** アプリケーション統合ページで、 **[シングル サインオン]** をクリックします。

    ![[シングル サインオン] ボタン](common/select-sso.png)

2. **[シングル サインオン方式の選択]** ダイアログ ボックスで、 **[SAML/WS-Fed]** モードを選択して、シングル サインオンを有効にします。

    ![[シングル サインオン方式の選択] ダイアログ ボックス](common/select-saml-option.png)

3. **[SAML でシングル サインオンをセットアップします]** ページで、編集アイコンを選択して **[基本的な SAML 構成]** ダイアログ ボックスを開きます。

    ![[SAML でシングル サインオンをセットアップします] ページ](common/edit-urls.png)

4. **[基本的な SAML 構成]** セクションで、次の手順を実行します。

    ![[基本的な SAML 構成] セクション](common/sp-identifier.png)

    a. **[サインオン URL]** ボックスに、次のパターンで URL を入力します。`https://<company>.answerhub.com`

    b. **[識別子 (エンティティ ID)]** ボックスに、次のパターンで URL を入力します。`https://<company>.answerhub.com`

    > [!NOTE]
    > これらは実際の値ではありません。 実際のサインオン URL と識別子でこれらの値を更新してください。 この値を取得するには、[AnswerHub サポート チーム](mailto:success@answerhub.com)に問い合わせてください。 Azure portal の **[基本的な SAML 構成]** セクションに示されているパターンを参照することもできます。

5. **[SAML でシングル サインオンをセットアップします]** ページの **[SAML 署名証明書]** セクションで、実際の要件に従って、 **[証明書 (Base64)]** の横にある **[ダウンロード]** リンクを選択し、証明書を自分のコンピューターに保存します。

    ![証明書のダウンロード リンク](common/certificatebase64.png)

6. **[AnswerHub のセットアップ]** セクションで、実際の要件に基づいて適切な URL をコピーします。

    ![構成 URL のコピー](common/copy-configuration-urls.png)

   これらの URL はコピーできます。
    - ログイン URL

    - Azure AD 識別子

    - ログアウト URL

### <a name="configure-answerhub-single-sign-on"></a>AnswerHub のシングル サインオンの構成

このセクションでは、AnswerHub のシングル サインオンを設定します。  

**AnswerHub のシングル サインオンを構成するには:**

1. 別の Web ブラウザー ウィンドウで、AnswerHub 企業サイトに管理者としてサインインします。

    > [!NOTE]
    > AnswerHub の構成について不明な点がある場合は、[AnswerHub サポート チーム](mailto:success@answerhub.com.)にお問い合わせください。

2. **[Administration]** に移動します。

3. **[User and Groups]\(ユーザーとグループ\)** タブの左側のウィンドウで、 **[Social Settings]\(ソーシャル設定\)** セクションの **[SAML Setup]\(SAML セットアップ\)** を選択します。

4. **[IDP Config]\(IDP 構成\)** タブで、以下の手順を実行します。

    ![[User & Groups]\(ユーザーとグループ\) タブ](./media/answerhub-tutorial/ic785172.png "SAML セットアップ")  
  
    a. **[IDP Login URL]\(IDP ログイン URL\)** ボックスに、Azure portal からコピーした **[ログイン URL]** を貼り付けます。
  
    b. **[IDP Logout URL]\(IDP ログアウト URL\)** ボックスに、Azure portal からコピーした **[ログアウト URL]** を貼り付けます。

    c. **[IDP Name Identifier Format]\(IDP 名前識別子形式\)** ボックスに、Azure portal の **[ユーザー属性]** セクションで選択した**識別子**の値を入力します。
  
    d. **[Keys and Certificates]\(キーと証明書\)** を選択します。

5. **[Keys and Certificates]\(キーと証明書\)** セクションで、以下の手順を実行します。

    ![[Keys and Certificates]\(キーと証明書\) セクション](./media/answerhub-tutorial/ic785173.png "キーと証明書")  

    a. Azure portal からダウンロードした Base64 でエンコードされた証明書をメモ帳で開き、その内容をコピーしてから、 **[IDP Public Key (x509 Format)]\(IDP 公開キー (x509 形式)\)** ボックスに貼り付けます。
  
    b. **[保存]** を選択します。

6. **[IDP Config]\(IDP 構成\)** タブで、もう一度 **[Save]\(保存\)** を選択します。

### <a name="create-an-azure-ad-test-user"></a>Azure AD のテスト ユーザーの作成

このセクションでは、Azure ポータルで Britta Simon というテスト ユーザーを作成します。

**Azure AD テスト ユーザーを作成するには:**

1. Azure portal の左側のウィンドウで、 **[Azure Active Directory]** 、 **[ユーザー]** 、 **[すべてのユーザー]** の順に選択します。

    ![[Azure Active Directory]、[ユーザー]、[すべてのユーザー] の選択](common/users.png)

2. 画面の上部にある **[新しいユーザー]** を選択します。

    ![[新しいユーザー] ボタン](common/new-user.png)

3. ユーザー プロパティで、以下の手順を実行します。

    ![ユーザー プロパティ](common/user-properties.png)

    a. **[名前]** ボックスに「**BrittaSimon**」と入力します。
  
    b. **[ユーザー名]** ボックスに、「**brittasimon\@<yourcompanydomain.extension>** 」と入力します。  
    たとえば、「 BrittaSimon@contoso.com 」のように入力します。

    c. **[パスワードを表示]** チェック ボックスをオンにし、 **[パスワード]** ボックスに表示された値を書き留めます。

    d. **作成** を選択します。

### <a name="assign-the-azure-ad-test-user"></a>Azure AD テスト ユーザーの割り当て

このセクションでは、Britta Simon というユーザーに AnswerHub へのアクセスを許可することで、このユーザーが Azure AD シングル サインオンを使用できるようにします。

**Azure AD テスト ユーザーを割り当てるには:**

1. Azure portal で **[エンタープライズ アプリケーション]** を選択し、 **[すべてのアプリケーション]** 、 **[AnswerHub]** の順に選択します。

    ![[エンタープライズ アプリケーション] ブレード](common/enterprise-applications.png)

2. アプリケーションの一覧で、 **[AnswerHub]** を選択します。

    ![アプリケーションの一覧](common/all-applications.png)

3. 左側のメニューで **[ユーザーとグループ]** を選びます。

    ![[ユーザーとグループ] の選択](common/users-groups-blade.png)

4. **[ユーザーの追加]** を選択し、 **[割り当ての追加]** ダイアログ ボックスで **[ユーザーとグループ]** を選択します。

    ![[割り当ての追加] ウィンドウ](common/add-assign-user.png)

5. **[ユーザーとグループ]** ダイアログ ボックスの **[ユーザー]** の一覧で **[Britta Simon]** を選択し、画面の下部にある **[選択]** ボタンを選択します。

6. SAML アサーション内にロール値が必要な場合、 **[ロールの選択]** ダイアログ ボックスで、一覧からユーザーに適したロールを選択します。 

7. 画面の下部にある **[選択]** ボタンを選択します。

8. **[割り当ての追加]** ダイアログ ボックスで **[割り当て]** を選びます。

### <a name="create-an-answerhub-test-user"></a>AnswerHub テスト ユーザーの作成

Azure AD ユーザーが AnswerHub にサインインできるようにするには、それらを AnswerHub に追加する必要があります。 AnswerHub では、このタスクを手動で実行します。

**ユーザー アカウントを設定するには:**

1. **AnswerHub** 企業サイトに管理者としてサインインします。

2. **[Administration]** に移動します。

3. **[User & Groups]\(ユーザーとグループ\)** タブを選択します。

4. 左側のウィンドウの **[Manage Users]\(ユーザーの管理\)** セクションで、 **[Create or import users]\(ユーザーの作成またはインポート\)** を選択し、 **[Users & Groups]\(ユーザーとグループ\)** を選択します。

   ![[User & Groups]\(ユーザーとグループ\) タブ](./media/answerhub-tutorial/ic785175.png "ユーザーとグループ")

5. 追加したい有効な Azure AD アカウントの**メール アドレス**、**ユーザー名**、**パスワード**を適切なボックスに入力してから、 **[Save]\(保存\)** を選択します。

> [!NOTE]
> AnswerHub から提供されている他のユーザー アカウント作成ツールまたは API を使用して、Azure AD ユーザー アカウントを設定できます。

### <a name="test-single-sign-on"></a>シングル サインオンのテスト

このセクションでは、アクセス パネルを使用して Azure AD のシングル サインオン構成をテストします。

アクセス パネルで [AnswerHub] タイルを選択すると、SSO を設定した AnswerHub に自動的にサインインします。 アクセス パネルの詳細については、[アクセス パネルの概要](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)に関する記事を参照してください。

## <a name="additional-resources"></a>その他のリソース

- [SaaS アプリと Azure Active Directory の統合に関するチュートリアル](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Azure Active Directory の条件付きアクセスとは](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

