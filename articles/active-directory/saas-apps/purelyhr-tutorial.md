---
title: 'チュートリアル: Azure Active Directory シングル サインオン (SSO) と PurelyHR の統合 | Microsoft Docs'
description: Azure Active Directory と PurelyHR の間でシングル サインオンを構成する方法について説明します。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 10/14/2019
ms.author: jeedes
ms.openlocfilehash: 344e0c142b905558bffbf339c8e7a29c92113c23
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "92515187"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-purelyhr"></a>チュートリアル: Azure Active Directory シングル サインオン (SSO) と PurelyHR の統合

このチュートリアルでは、PurelyHR と Azure Active Directory (Azure AD) を統合する方法について説明します。 Azure AD と PurelyHR を統合すると、次のことができます。

* PurelyHR にアクセスできるユーザーを Azure AD で制御できます。
* ユーザーが自分の Azure AD アカウントを使用して PurelyHR に自動的にサインインできるように設定できます。
* 1 つの中央サイト (Azure Portal) で自分のアカウントを管理します。

SaaS アプリと Azure AD の統合の詳細については、「[Azure Active Directory でのアプリケーションへのシングル サインオン](../manage-apps/what-is-single-sign-on.md)」を参照してください。

## <a name="prerequisites"></a>前提条件

開始するには、次が必要です。

* Azure AD サブスクリプション。 サブスクリプションがない場合は、[無料アカウント](https://azure.microsoft.com/free/)を取得できます。
* PurelyHR でのシングル サインオン (SSO) が有効なサブスクリプション。

## <a name="scenario-description"></a>シナリオの説明

このチュートリアルでは、テスト環境で Azure AD の SSO を構成してテストします。

* PurelyHR では、**SP Initiated SSO と IDP Initiated SSO** がサポートされます
* PurelyHR では、**Just In Time** ユーザー プロビジョニングがサポートされます

## <a name="adding-purelyhr-from-the-gallery"></a>ギャラリーからの PurelyHR の追加

Azure AD への PurelyHR の統合を構成するには、ギャラリーから管理対象 SaaS アプリの一覧に PurelyHR を追加する必要があります。

1. 職場または学校アカウントか、個人の Microsoft アカウントを使用して、[Azure portal](https://portal.azure.com) にサインインします。
1. 左のナビゲーション ウィンドウで **[Azure Active Directory]** サービスを選択します。
1. **[エンタープライズ アプリケーション]** に移動し、 **[すべてのアプリケーション]** を選択します。
1. 新しいアプリケーションを追加するには、 **[新しいアプリケーション]** を選択します。
1. **[ギャラリーから追加する]** セクションで、検索ボックスに「**PurelyHR**」と入力します。
1. 結果のパネルから **[PurelyHR]** を選択し、アプリを追加します。 お使いのテナントにアプリが追加されるのを数秒待機します。

## <a name="configure-and-test-azure-ad-single-sign-on-for-purelyhr"></a>PurelyHR の Azure AD シングル サインオンの構成とテスト

**B.Simon** というテスト ユーザーを使用して、PurelyHR に対する Azure AD SSO を構成してテストします。 SSO が機能するためには、Azure AD ユーザーと PurelyHR の関連ユーザーとの間にリンク関係を確立する必要があります。

PurelyHR に対する Azure AD SSO を構成してテストするには、次の構成要素を完了します。

1. **[Azure AD SSO の構成](#configure-azure-ad-sso)** - ユーザーがこの機能を使用できるようにします。
    * **[Azure AD のテスト ユーザーの作成](#create-an-azure-ad-test-user)** - B.Simon で Azure AD のシングル サインオンをテストします。
    * **[Azure AD テスト ユーザーの割り当て](#assign-the-azure-ad-test-user)** - B.Simon が Azure AD シングル サインオンを使用できるようにします。
1. **[PurelyHR の SSO の構成](#configure-purelyhr-sso)** - アプリケーション側でシングル サインオン設定を構成します。
    * **[PurelyHR のテスト ユーザーの作成](#create-purelyhr-test-user)** - PurelyHR で B.Simon に対応するユーザーを作成し、Azure AD の B.Simon にリンクさせます。
1. **[SSO のテスト](#test-sso)** - 構成が機能するかどうかを確認します。

## <a name="configure-azure-ad-sso"></a>Azure AD SSO の構成

これらの手順に従って、Azure portal で Azure AD SSO を有効にします。

1. [Azure portal](https://portal.azure.com/) の **PurelyHR** アプリケーション統合ページで、**[管理]** セクションを探して、**[シングル サインオン]** を選択します。
1. **[シングル サインオン方式の選択]** ページで、 **[SAML]** を選択します。
1. **[SAML でシングル サインオンをセットアップします]** ページで、 **[基本的な SAML 構成]** の編集 (ペン) アイコンをクリックして設定を編集します。

   ![基本的な SAML 構成を編集する](common/edit-urls.png)

1. **[基本的な SAML 構成]** セクションで、アプリケーションを **IDP** 開始モードで構成する場合は、次のフィールドの値を入力します。

    **[応答 URL]** ボックスに、`https://<companyID>.purelyhr.com/sso-consume` のパターンを使用して URL を入力します

1. アプリケーションを **SP** 開始モードで構成する場合は、 **[追加の URL を設定します]** をクリックして次の手順を実行します。

    **[サインオン URL]** ボックスに、`https://<companyID>.purelyhr.com/sso-initiate` という形式で URL を入力します。

    > [!NOTE]
    > これらは実際の値ではありません。 実際の応答 URLとサインオン URL でこれらの値を更新します。 これらの値を取得するには、[PurelyHR クライアント サポート チーム](https://support.purelyhr.com/)に連絡してください。 Azure portal の **[基本的な SAML 構成]** セクションに示されているパターンを参照することもできます。

1. **[SAML でシングル サインオンをセットアップします]** ページの **[SAML 署名証明書]** セクションで、 **[証明書 (Base64)]** を見つけて、 **[ダウンロード]** を選択し、証明書をダウンロードして、お使いのコンピューターに保存します。

    ![証明書のダウンロードのリンク](common/certificatebase64.png)

1. **[PurelyHR のセットアップ]** セクションで、要件に基づいて適切な URL をコピーします。

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

このセクションでは、B.Simon に PurelyHR へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

1. Azure portal で **[エンタープライズ アプリケーション]** を選択し、 **[すべてのアプリケーション]** を選択します。
1. アプリケーションの一覧で **[PurelyHR]** を選択します。
1. アプリの概要ページで、 **[管理]** セクションを見つけて、 **[ユーザーとグループ]** を選択します。

   ![[ユーザーとグループ] リンク](common/users-groups-blade.png)

1. **[ユーザーの追加]** を選択し、 **[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。

    ![[ユーザーの追加] リンク](common/add-assign-user.png)

1. **[ユーザーとグループ]** ダイアログの [ユーザー] の一覧から **[B.Simon]** を選択し、画面の下部にある **[選択]** ボタンをクリックします。
1. SAML アサーション内に任意のロール値が必要な場合、 **[ロールの選択]** ダイアログでユーザーに適したロールを一覧から選択し、画面の下部にある **[選択]** をクリックします。
1. **[割り当ての追加]** ダイアログで、 **[割り当て]** をクリックします。

## <a name="configure-purelyhr-sso"></a>PurelyHR の SSO の構成

1. PurelyHR 内での構成を自動化するには、**[拡張機能のインストール]** をクリックして **My Apps Secure Sign-in ブラウザー拡張機能** をインストールする必要があります。

    ![マイ アプリの拡張機能](common/install-myappssecure-extension.png)

1. ブラウザーに拡張機能を追加した後、**[PurelyHR のセットアップ]** をクリックすると、PurelyHR アプリケーションに誘導されます。 そこから、管理者の資格情報を入力して PurelyHR にサインインします。 ブラウザー拡張機能によりアプリケーションが自動的に構成され、手順 3 から 5 が自動化されます。

    ![セットアップの構成](common/setup-sso.png)

1. PurelyHR を手動でセットアップする場合は、新しい Web ブラウザー ウィンドウを開き、管理者として PurelyHR の企業サイトにサインインして、次の手順を実行します。

1. ツール バーのオプションから **ダッシュボード** を開き、**[SSO Settings]** をクリックします。

1. 以下の説明に従って、各ボックスに値を貼り付けます。

    ![Configure single sign-on](./media/purelyhr-tutorial/purelyhr-dashboard-sso-settings.png)  

    a. Azure Portal からダウンロードした **Certificate(Bas64)** をメモ帳で開き、証明書の値をコピーします。 コピーした値を **[X.509 Certificate]** ボックスに貼り付けます。

    b. **[Idp Issuer URL]\(Idp 発行者 URL\)** ボックスに、Azure portal からコピーした **Azure AD 識別子** を貼り付けます。

    c. **[Idp Endpoint URL]\(IdP エンドポイント URL\)** ボックスに、Azure portal からコピーした **ログイン URL** を貼り付けます。 

    d. **[Auto-Create Users]** チェック ボックスをオンにして、PurelyHR への自動ユーザー プロビジョニングを有効にします。

    e. **[Save Changes]** をクリックして設定を保存します。

### <a name="create-purelyhr-test-user"></a>PurelyHR のテスト ユーザーの作成

アプリケーションは、ジャスト イン タイムのユーザー プロビジョニングをサポートしているため、通常、この手順は必要ありません。 自動ユーザー プロビジョニングが有効でない場合は、次の手順にしたがって手動でユーザーを作成することができます。

Velpic SAML 企業サイトに管理者としてサインインし、次の手順に従います。

1. [Manage] \(管理) タブをクリックして [Users] \(ユーザー) セクションに移動し、[New] \(新規) をクリックしてユーザーを追加します。

    ![add user](./media/velpicsaml-tutorial/velpic_7.png)

2. **[Create New User] \(新しいユーザーの作成)** ダイアログ ページで、次の手順を実行します。

    ![user](./media/velpicsaml-tutorial/velpic_8.png)

    a. **[First Name]\(名\)** ボックスに、ユーザーの名 B を入力します。

    b. **[Last Name]\(姓\)** ボックスに、ユーザーの姓 Simon を入力します。

    c. **[User Name]\(ユーザー名\)** テキストボックスに、B.Simon のユーザーを入力します。

    d. **[Email]\(電子メール\)** ボックスに、B.Simon@contoso.com アカウントのメール アドレスを入力します。

    e. その他の情報は省略可能です。必要に応じて入力してください。

    f. **[保存]** をクリックします。

## <a name="test-sso"></a>SSO のテスト 

このセクションでは、アクセス パネルを使用して Azure AD のシングル サインオン構成をテストします。

アクセス パネル上で [PurelyHR] タイルをクリックすると、SSO を設定した PurelyHR に自動的にサインインします。 アクセス パネルの詳細については、[アクセス パネルの概要](../user-help/my-apps-portal-end-user-access.md)に関する記事を参照してください。

## <a name="additional-resources"></a>その他のリソース

- [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](./tutorial-list.md)

- [Azure Active Directory でのアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)

- [Azure Active Directory の条件付きアクセスとは](../conditional-access/overview.md)

- [Azure AD で PurelyHR を試す](https://aad.portal.azure.com/)