---
title: チュートリアル:Azure Active Directory シングル サインオン (SSO) と EZOfficeInventory の統合 | Microsoft Docs
description: Azure Active Directory と EZOfficeInventory の間でシングル サインオンを構成する方法について説明します。
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: e8594f7c-dc2f-446f-9c25-676fe49ff3af
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 10/10/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: cc73d7ec81ea0105a5868ec698289bd27526a43b
ms.sourcegitcommit: 8bd85510aee664d40614655d0ff714f61e6cd328
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/06/2019
ms.locfileid: "74893503"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-ezofficeinventory"></a>チュートリアル:Azure Active Directory シングル サインオン (SSO) と EZOfficeInventory の統合

このチュートリアルでは、EZOfficeInventory と Azure Active Directory (Azure AD) を統合する方法について説明します。 Azure AD と EZOfficeInventory を統合すると、次のことができます。

* EZOfficeInventory にアクセスできるユーザーを Azure AD で制御できます。
* ユーザーが自分の Azure AD アカウントを使用して EZOfficeInventory に自動的にサインインできるように設定できます。
* 1 つの中央サイト (Azure Portal) で自分のアカウントを管理します。

SaaS アプリと Azure AD の統合の詳細については、「[Azure Active Directory でのアプリケーションへのシングル サインオン](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)」を参照してください。

## <a name="prerequisites"></a>前提条件

開始するには、次が必要です。

* Azure AD サブスクリプション。 サブスクリプションがない場合は、[無料アカウント](https://azure.microsoft.com/free/)を取得できます。
* EZOfficeInventory でのシングル サインオン (SSO) が有効なサブスクリプション。

## <a name="scenario-description"></a>シナリオの説明

このチュートリアルでは、テスト環境で Azure AD の SSO を構成してテストします。

* EZOfficeInventory では、**SP** Initiated SSO がサポートされます

* EZOfficeInventory では、**Just-In-Time** ユーザー プロビジョニングがサポートされます

> [!NOTE]
> このアプリケーションの識別子は固定文字列値であるため、1 つのテナントで構成できるインスタンスは 1 つだけです。

## <a name="adding-ezofficeinventory-from-the-gallery"></a>ギャラリーからの EZOfficeInventory の追加

Azure AD への EZOfficeInventory の統合を構成するには、ギャラリーからマネージド SaaS アプリの一覧に EZOfficeInventory を追加する必要があります。

1. 職場または学校アカウントか、個人の Microsoft アカウントを使用して、[Azure portal](https://portal.azure.com) にサインインします。
1. 左のナビゲーション ウィンドウで **[Azure Active Directory]** サービスを選択します。
1. **[エンタープライズ アプリケーション]** に移動し、 **[すべてのアプリケーション]** を選択します。
1. 新しいアプリケーションを追加するには、 **[新しいアプリケーション]** を選択します。
1. **[ギャラリーから追加する]** セクションで、検索ボックスに、「**EZOfficeInventory**」と入力します。
1. 結果のパネルから **EZOfficeInventory** を選択し、アプリを追加します。 お使いのテナントにアプリが追加されるのを数秒待機します。

## <a name="configure-and-test-azure-ad-single-sign-on-for-ezofficeinventory"></a>EZOfficeInventory の Azure AD シングル サインオンの構成とテスト

**B.Simon** というテスト ユーザーを使用して、EZOfficeInventory に対する Azure AD SSO を構成してテストします。 SSO が機能するためには、Azure AD ユーザーと EZOfficeInventory の関連ユーザーとの間にリンク関係を確立する必要があります。

EZOfficeInventory で Azure AD SSO を構成してテストするには、次の構成要素を完了する必要があります。

1. **[Azure AD SSO の構成](#configure-azure-ad-sso)** - ユーザーがこの機能を使用できるようにします。
    1. **[Azure AD のテスト ユーザーの作成](#create-an-azure-ad-test-user)** - B.Simon で Azure AD のシングル サインオンをテストします。
    1. **[Azure AD テスト ユーザーの割り当て](#assign-the-azure-ad-test-user)** - B.Simon が Azure AD シングル サインオンを使用できるようにします。
1. **[EZOfficeInventory の SSO の構成](#configure-ezofficeinventory-sso)** - アプリケーション側でシングル サインオン設定を構成します。
    1. **[EZOfficeInventory のテスト ユーザーの作成](#create-ezofficeinventory-test-user)** - EZOfficeInventory で B.Simon に対応するユーザーを作成し、Azure AD の B.Simon にリンクさせます。
1. **[SSO のテスト](#test-sso)** - 構成が機能するかどうかを確認します。

## <a name="configure-azure-ad-sso"></a>Azure AD SSO の構成

これらの手順に従って、Azure portal で Azure AD SSO を有効にします。

1. [Azure portal](https://portal.azure.com/) の **EZOfficeInventory** アプリケーション統合ページで、 **[管理]** セクションを見つけて、 **[シングル サインオン]** を選択します。
1. **[シングル サインオン方式の選択]** ページで、 **[SAML]** を選択します。
1. **[SAML でシングル サインオンをセットアップします]** ページで、 **[基本的な SAML 構成]** の編集 (ペン) アイコンをクリックして設定を編集します。

   ![基本的な SAML 構成を編集する](common/edit-urls.png)

1. **[基本的な SAML 構成]** セクションで、次のフィールドの値を入力します。

    **[サインオン URL]** ボックスに、`https://<SUBDOMAIN>.ezofficeinventory.com/users/sign_in` という形式で URL を入力します。

    > [!NOTE]
    > この値は実際のものではありません。 実際のサインオン URL でこの値を更新してください。 この値を取得するには、[EZOfficeInventory クライアント サポート チーム](mailto:support@ezofficeinventory.com)に問い合わせてください。 Azure portal の **[基本的な SAML 構成]** セクションに示されているパターンを参照することもできます。

1. EZOfficeInventory アプリケーションでは、特定の形式の SAML アサーションを使用するため、カスタム属性マッピングを SAML トークン属性の構成に追加する必要があります。 次のスクリーンショットには、既定の属性一覧が示されています。

    ![image](common/default-attributes.png)

1. その他に、EZOfficeInventory アプリケーションでは、いくつかの属性が SAML 応答で返されることが想定されています。それらの属性を次に示します。 これらの属性も値が事前に設定されますが、要件に従ってそれらの値を確認することができます。

    | 名前 | ソース属性|
    | ---------------| --------------- |
    | First_name | User.givenname |
    | Last_name | User.surname |
    | Email | User.mail |

1. **[SAML でシングル サインオンをセットアップします]** ページの **[SAML 署名証明書]** セクションで、 **[証明書 (Base64)]** を見つけて、 **[ダウンロード]** を選択し、証明書をダウンロードして、お使いのコンピューターに保存します。

    ![証明書のダウンロードのリンク](common/certificatebase64.png)

1. **[EZOfficeInventory のセットアップ]** セクションで、要件に基づいて適切な URL をコピーします。

    ![構成 URL のコピー](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Azure AD のテスト ユーザーの作成

このセクションでは、Azure portal 内で B.Simon というテスト ユーザーを作成します。

1. Azure portal の左側のウィンドウから、 **[Azure Active Directory]** 、 **[ユーザー]** 、 **[すべてのユーザー]** の順に選択します。
1. 画面の上部にある **[新しいユーザー]** を選択します。
1. **[ユーザー]** プロパティで、以下の手順を実行します。
   1. **[名前]** フィールドに「`B.Simon`」と入力します。  
   1. **[ユーザー名]** フィールドに「username@companydomain.extension」と入力します。 たとえば、「 `B.Simon@contoso.com` 」のように入力します。
   1. **[パスワードを表示]** チェック ボックスをオンにし、 **[パスワード]** ボックスに表示された値を書き留めます。
   1. **Create** をクリックしてください。

### <a name="assign-the-azure-ad-test-user"></a>Azure AD テスト ユーザーの割り当て

このセクションでは、B.Simon に EZOfficeInventory へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

1. Azure portal で **[エンタープライズ アプリケーション]** を選択し、 **[すべてのアプリケーション]** を選択します。
1. アプリケーションの一覧で **[EZOfficeInventory]** を選択します。
1. アプリの概要ページで、 **[管理]** セクションを見つけて、 **[ユーザーとグループ]** を選択します。

   ![[ユーザーとグループ] リンク](common/users-groups-blade.png)

1. **[ユーザーの追加]** を選択し、 **[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。

    ![[ユーザーの追加] リンク](common/add-assign-user.png)

1. **[ユーザーとグループ]** ダイアログの [ユーザー] の一覧から **[B.Simon]** を選択し、画面の下部にある **[選択]** ボタンをクリックします。
1. SAML アサーション内に任意のロール値が必要な場合、 **[ロールの選択]** ダイアログでユーザーに適したロールを一覧から選択し、画面の下部にある **[選択]** をクリックします。
1. **[割り当ての追加]** ダイアログで、 **[割り当て]** をクリックします。

## <a name="configure-ezofficeinventory-sso"></a>EZOfficeInventory の SSO の構成

1. EZOfficeInventory 内での構成を自動化するには、 **[拡張機能のインストール]** をクリックして **My Apps Secure Sign-in ブラウザー拡張機能**をインストールする必要があります。

    ![マイ アプリの拡張機能](common/install-myappssecure-extension.png)

1. ブラウザーに拡張機能を追加した後、 **[EZOfficeInventory のセットアップ]** をクリックすると、EZOfficeInventory アプリケーションに誘導されます。 そこから、管理者の資格情報を入力して EZOfficeInventory にサインインします。 ブラウザー拡張機能によりアプリケーションが自動的に構成され、手順 3 から 5 が自動化されます。

    ![セットアップの構成](common/setup-sso.png)

1. EZOfficeInventory を手動でセットアップする場合は、新しい Web ブラウザー ウィンドウを開き、管理者として EZOfficeInventory 企業サイトにサインインして、次の手順を実行します。

1. ページの右上隅の **[Profile]\(プロファイル\)** をクリックし、 **[Settings]\(設定\)**  >  **[Add Ons]\(アドオン\)** に移動します。

    ![EZOfficeInventory の構成](./media/ezofficeinventory-tutorial/configure01.png)

1. 下へスクロールして **[SAML Integration]\(SAML 統合\)** セクションに移動し、次の手順に従います。

    ![EZOfficeInventory の構成](./media/ezofficeinventory-tutorial/configure02.png)

    a. **[Enabled]\(有効\)** オプションをオンにします。

    b. **[Identity Provider URL]\(ID プロバイダーの URL\)** ボックスに、Azure portal からコピーした**ログイン URL** の値を貼り付けます。

    c. Base64 でエンコードされた証明書をメモ帳で開き、その内容をコピーして **[Identity Provider Certificate]\(ID プロバイダー証明書\)** ボックスに貼り付けます。

    d. **[Login Button Text]\(ログイン ボタン テキスト\)** ボックスに、ログイン ボタンのテキストを入力します。

    e. **[First Name]\(名\)** ボックスに「**first_name**」と入力します。

    f. **[Last Name]\(姓\)** ボックスに「**last_name**」と入力します。

    g. **[Email]\(メール\)** ボックスに「**email**」と入力します。

    h. **[EZOfficeInventory Role By default]\(既定の EZOfficeInventory ロール\)** オプションから要件に従って自分のロールを選択します。

    i. **[Update]** をクリックします。

### <a name="create-ezofficeinventory-test-user"></a>EZOfficeInventory のテスト ユーザーの作成

このセクションでは、Britta Simon というユーザーを EZOfficeInventory に作成します。 EZOfficeInventory では、Just-In-Time ユーザー プロビジョニングがサポートされています。この設定は既定で有効になっています。 このセクションでは、ユーザー側で必要な操作はありません。 EZOfficeInventory にユーザーがまだ存在していない場合は、認証後に新規に作成されます。

## <a name="test-sso"></a>SSO のテスト

このセクションでは、アクセス パネルを使用して Azure AD のシングル サインオン構成をテストします。

アクセス パネル上で [EZOfficeInventory] タイルをクリックすると、SSO を設定した EZOfficeInventory に自動的にサインインします。 アクセス パネルの詳細については、[アクセス パネルの概要](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)に関する記事を参照してください。

## <a name="additional-resources"></a>その他のリソース

- [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Azure Active Directory でのアプリケーション アクセスとシングル サインオンとは](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Azure Active Directory の条件付きアクセスとは](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Azure AD で EZOfficeInventory を試す](https://aad.portal.azure.com/)
