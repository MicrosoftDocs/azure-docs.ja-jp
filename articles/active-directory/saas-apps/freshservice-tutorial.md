---
title: チュートリアル:Azure Active Directory と Freshservice の統合 | Microsoft Docs
description: Azure Active Directory と Freshservice の間でシングル サインオンを構成する方法について説明します。
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: 3dd22b1f-445d-45c6-8eda-30207eb9a1a8
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 05/20/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8fbf69ba814b99434e933ed700fb0c8c842c3312
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/13/2019
ms.locfileid: "67101838"
---
# <a name="tutorial-integrate-freshservice-with-azure-active-directory"></a>チュートリアル:Freshservice と Azure Active Directory の統合

このチュートリアルでは、Freshservice と Azure Active Directory (Azure AD) を統合する方法について説明します。 Azure AD と Freshservice を統合すると、次のことができます。

* Freshservice にアクセスできるユーザーを Azure AD で制御する。
* ユーザーが自分の Azure AD アカウントを使用して Freshservice に自動的にサインインできるようにする。
* 1 つの中央サイト (Azure portal) でアカウントを管理する。

SaaS アプリと Azure AD の統合の詳細については、「[Azure Active Directory でのアプリケーションへのシングル サインオン](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)」を参照してください。

## <a name="prerequisites"></a>前提条件

開始するには、次が必要です。

* Azure AD サブスクリプション。 サブスクリプションをお持ちでない場合は、[ここ](https://azure.microsoft.com/pricing/free-trial/)から 1 か月間の無料試用版を入手できます。
* FreshService でのシングル サインオン (SSO) が有効なサブスクリプション。

## <a name="scenario-description"></a>シナリオの説明

このチュートリアルでは、テスト環境で Azure AD の SSO を構成してテストします。 Freshservice では、**SP** によって開始される SSO がサポートされます

## <a name="adding-freshservice-from-the-gallery"></a>ギャラリーからの Freshservice の追加

Azure AD への Freshservice の統合を構成するには、ギャラリーから管理対象 SaaS アプリの一覧に Freshservice を追加する必要があります。

1. 職場または学校アカウントか、個人の Microsoft アカウントを使用して、[Azure portal](https://portal.azure.com) にサインインします。
1. 左のナビゲーション ウィンドウで **[Azure Active Directory]** サービスを選択します。
1. **[エンタープライズ アプリケーション]** に移動し、 **[すべてのアプリケーション]** を選択します。
1. 新しいアプリケーションを追加するには、 **[新しいアプリケーション]** を選択します。
1. **[ギャラリーから追加する]** セクションで、検索ボックスに「**Freshservice**」と入力します。
1. 結果ウィンドウで **[Freshservice]** を選択し、アプリを追加します。 アプリがテナントに追加される間、数秒待ちます。

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成とテスト

**Britta Simon** というテスト ユーザーを使用して、Freshservice で Azure AD の SSO を構成し、テストします。 SSO が機能するために、Azure AD ユーザーと Freshservice の関連ユーザーの間で、リンク関係を確立する必要があります。

Freshservice で Azure AD SSO を構成してテストするには、次の構成要素を完了します。

1. **[Azure AD SSO の構成](#configure-azure-ad-sso)** - ユーザーがこの機能を使用できるようにします。
2. **[Freshservice のシングル サインオンの構成](#configure-freshservice-sso)** - アプリケーション側でシングル サインオン設定を構成します。
3. **[Azure AD のテスト ユーザーの作成](#create-an-azure-ad-test-user)** - Britta Simon で Azure AD のシングル サインオンをテストします。
4. **[Azure AD テスト ユーザーの割り当て](#assign-the-azure-ad-test-user)** - Britta Simon が Azure AD シングル サインオンを使用できるようにします。
5. **[Freshservice テスト ユーザーの作成](#create-freshservice-test-user)** - Freshservice で Britta Simon に対応するユーザーを作成し、Azure AD の Britta Simon にリンクさせます。
6. **[SSO のテスト](#test-sso)** - 構成が機能するかどうか確認します。

### <a name="configure-azure-ad-sso"></a>Azure AD SSO の構成

Azure portal で Azure AD SSO を有効にするには、次の手順に従います。

1. [Azure portal](https://portal.azure.com/) の **Freshservice** アプリケーション統合ページで、 **[管理]** セクションを見つけて、 **[シングル サインオン]** を選択します。
1. **[シングル サインオン方式の選択]** ページで、 **[SAML]** を選択します。
1. **[SAML でシングル サインオンをセットアップします]** ページで、 **[基本的な SAML 構成]** の編集/ペン アイコンをクリックして設定を編集します。

   ![基本的な SAML 構成を編集する](common/edit-urls.png)

1. **[基本的な SAML 構成]** ページで、次のフィールドの値を入力します。

    a. **[サインオン URL]** ボックスに、次のパターンを使用して URL を入力します。`https://<democompany>.freshservice.com`

    b. **[識別子 (エンティティ ID)]** ボックスに、次のパターンを使用して URL を入力します。`https://<democompany>.freshservice.com`

    > [!NOTE]
    > これらは実際の値ではありません。 実際のサインオン URL と識別子でこれらの値を更新します。 これらの値を取得するには、[Freshservice クライアント サポート チーム](https://support.freshservice.com/)に問い合わせてください。 Azure portal の **[基本的な SAML 構成]** セクションに示されているパターンを参照することもできます。

1. **[SAML 署名証明書]** セクションで **[編集]** ボタンをクリックして、 **[SAML 署名証明書]** ダイアログを開きます。

    ![SAML 署名証明書の編集](common/edit-certificate.png)

1. **[SAML 署名証明書]** セクションで **[Thumbprint]\(拇印\)** をコピーし、お使いのコンピューターに保存します。

    ![[Thumbprint]\(拇印\) の値をコピーする](common/copy-thumbprint.png)

1. **[Freshservice のセットアップ]** セクションで、要件に従って適切な URL をコピーします。

    ![構成 URL のコピー](common/copy-configuration-urls.png)

    a. ログイン URL

    b. Azure AD 識別子

    c. ログアウト URL

### <a name="configure-freshservice-sso"></a>Freshservice の SSO の構成

1. Freshservice 内での構成を自動化するには、 **[拡張機能のインストール]** をクリックして**マイ アプリによるセキュリティで保護されたサインイン拡張機能**をインストールする必要があります。

    ![マイ アプリの拡張機能](common/install-myappssecure-extension.png)

2. ブラウザーに拡張機能を追加した後、 **[Freshservice のセットアップ]** をクリックすると、Freshservice アプリケーションに移動します。 そこから、管理者資格情報を提供して Freshservice にサインインします。 ブラウザー拡張機能によりアプリケーションが自動的に構成され、手順 3 から 6 が自動化されます。

    ![セットアップの構成](common/setup-sso.png)

3. Freshservice を手動でセットアップする場合は、新しい Web ブラウザー ウィンドウを開き、管理者として Freshservice 企業サイトにサインインして、次の手順のようにします。

4. 上部のメニューで **[Admin]** をクリックします。

    ![管理](./media/freshservice-tutorial/ic790814.png "Admin")

5. **[カスタマー ポータル]** で **[セキュリティ]** をクリックします。

    ![Security (セキュリティ)](./media/freshservice-tutorial/ic790815.png "Security")

6. **[セキュリティ]** セクションで、次の手順を実行します。

    ![Single Sign On](./media/freshservice-tutorial/ic790816.png "Single Sign On")

    a. **[シングル サインオン]** を切り替えます。

    b. **[SAML SSO]** を選択します。

    c. **[SAML Login URL]\(SAML ログイン URL\)** ボックスに、Azure portal からコピーした**ログイン URL** の値を貼り付けます。

    d. **[ログアウト URL]** ボックスに、Azure portal からコピーした**ログアウト URL** の値を貼り付けます。

    e. **[Security Certificate Fingerprint]\(セキュリティ証明書フィンガープリント\)** ボックスに、Azure portal からコピーした証明書の**拇印**の値を貼り付けます。

    f. **[保存]**

### <a name="create-an-azure-ad-test-user"></a>Azure AD のテスト ユーザーの作成

このセクションでは、Azure portal で Britta Simon というテスト ユーザーを作成します。

1. Azure portal の左側のウィンドウから、 **[Azure Active Directory]** 、 **[ユーザー]** 、 **[すべてのユーザー]** の順に選択します。
1. 画面の上部にある **[新しいユーザー]** を選択します。
1. **[ユーザー]** プロパティで、以下の手順を実行します。
   1. **[名前]** フィールドに「`Britta Simon`」と入力します。  
   1. **[ユーザー名]** フィールドに「username@companydomain.extension」と入力します。 たとえば、「 `BrittaSimon@contoso.com` 」のように入力します。
   1. **[パスワードを表示]** チェック ボックスをオンにし、 **[パスワード]** ボックスに表示された値を書き留めます。
   1. **Create** をクリックしてください。

### <a name="assign-the-azure-ad-test-user"></a>Azure AD テスト ユーザーの割り当て

このセクションでは、Britta Simon に Freshservice へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

1. Azure portal で **[エンタープライズ アプリケーション]** を選択し、 **[すべてのアプリケーション]** を選択します。
1. アプリケーションの一覧で **[Freshservice]** を選択します。
1. アプリの概要ページで、 **[管理]** セクションを見つけて、 **[ユーザーとグループ]** を選択します。

   ![[ユーザーとグループ] リンク](common/users-groups-blade.png)

1. **[ユーザーの追加]** を選択し、 **[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。

    ![[ユーザーの追加] リンク](common/add-assign-user.png)

1. **[ユーザーとグループ]** ダイアログの [ユーザー] の一覧から **Britta Simon** を選択し、画面の下部にある **[選択]** ボタンをクリックします。
1. SAML アサーション内に任意のロール値が必要な場合、 **[ロールの選択]** ダイアログでユーザーに適したロールを一覧から選択し、画面の下部にある **[選択]** をクリックします。
1. **[割り当ての追加]** ダイアログで、 **[割り当て]** をクリックします。

### <a name="create-freshservice-test-user"></a>Freshservice のテスト ユーザーの作成

Azure AD ユーザーが Freshservice にサインインできるようにするには、そのユーザーを Freshservice にプロビジョニングする必要があります。 FreshService の場合、プロビジョニングは手動で行います。

**ユーザー アカウントをプロビジョニングするには、次の手順に従います。**

1. **FreshService** 企業サイトに管理者としてサインインします。

2. 上部のメニューで **[Admin]** をクリックします。

    ![管理](./media/freshservice-tutorial/ic790814.png "Admin")

3. **[ユーザー管理]** セクションで、 **[要求者]** をクリックします。

    ![Requesters](./media/freshservice-tutorial/ic790818.png "Requesters")

4. **[新しい要求者]** をクリックします。

    ![New Requesters](./media/freshservice-tutorial/ic790819.png "New Requesters")

5. **[新しい要求者]** セクションで、次の手順を実行します。

    ![New Requester](./media/freshservice-tutorial/ic790820.png "New Requester")  

    a. 関連するテキスト ボックスに、プロビジョニングする有効な Azure Active Directory アカウントの**名**および**メール**の属性を入力します。

    b. **[Save]** をクリックします。

    > [!NOTE]
    > Azure Active Directory のアカウント所有者は、そのアカウントがアクティブになる前に、アカウント確認用のリンクを含む電子メールを受け取ります。
    >  

> [!NOTE]
> FreshService から提供されている他の FreshService ユーザー アカウント作成ツールまたは API を使用して、AAD ユーザー アカウントをプロビジョニングできます。

### <a name="test-sso"></a>SSO のテスト

アクセス パネルで [Freshservice] タイルを選択すると、SSO を設定した Freshservice に自動的にサインインします。 アクセス パネルの詳細については、[アクセス パネルの概要](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)に関する記事を参照してください。

## <a name="additional-resources"></a>その他のリソース

- [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Azure Active Directory の条件付きアクセスとは](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
