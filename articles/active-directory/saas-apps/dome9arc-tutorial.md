---
title: チュートリアル:Azure Active Directory シングル サインオン (SSO) と Check Point CloudGuard Dome9 Arc の統合 | Microsoft Docs
description: Azure Active Directory と Check Point CloudGuard Dome9 Arc の間でシングル サインオンを構成する方法について説明します。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 10/17/2019
ms.author: jeedes
ms.openlocfilehash: 9435a0eccff5a3be0dc9e0a2844bae175d753a5c
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/18/2020
ms.locfileid: "88555880"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-check-point-cloudguard-dome9-arc"></a>チュートリアル:Azure Active Directory シングル サインオン (SSO) と Check Point CloudGuard Dome9 Arc の統合

このチュートリアルでは、Check Point CloudGuard Dome9 Arc と Azure Active Directory (Azure AD) を統合する方法について説明します。 Check Point CloudGuard Dome9 Arc と Azure AD を統合すると、次のことが可能になります。

* Check Point CloudGuard Dome9 Arc にアクセスできるユーザーを Azure AD で制御できます。
* ユーザーが自分の Azure AD アカウントで Check Point CloudGuard Dome9 Arc に自動的にサインインするように設定できます。
* 1 つの中央サイト (Azure Portal) で自分のアカウントを管理します。

SaaS アプリと Azure AD の統合の詳細については、「[Azure Active Directory でのアプリケーションへのシングル サインオン](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)」を参照してください。

## <a name="prerequisites"></a>前提条件

開始するには、次が必要です。

* Azure AD サブスクリプション。 サブスクリプションがない場合は、[無料アカウント](https://azure.microsoft.com/free/)を取得できます。
* Check Point CloudGuard Dome9 Arc でのシングル サインオン (SSO) が有効なサブスクリプション。

## <a name="scenario-description"></a>シナリオの説明

このチュートリアルでは、テスト環境で Azure AD の SSO を構成してテストします。

* Check Point CloudGuard Dome9 Arc では、**SP Initiated SSO と IDP Initiated SSO** がサポートされます

> [!NOTE]
> このアプリケーションの識別子は固定文字列値であるため、1 つのテナントで構成できるインスタンスは 1 つだけです。

## <a name="adding-check-point-cloudguard-dome9-arc-from-the-gallery"></a>ギャラリーからの Check Point CloudGuard Dome9 Arc の追加

Azure AD への Check Point CloudGuard Dome9 Arc の統合を構成するには、マネージド SaaS アプリの一覧にギャラリーから Check Point CloudGuard Dome9 Arc を追加する必要があります。

1. 職場または学校アカウントか、個人の Microsoft アカウントを使用して、[Azure portal](https://portal.azure.com) にサインインします。
1. 左のナビゲーション ウィンドウで **[Azure Active Directory]** サービスを選択します。
1. **[エンタープライズ アプリケーション]** に移動し、 **[すべてのアプリケーション]** を選択します。
1. 新しいアプリケーションを追加するには、 **[新しいアプリケーション]** を選択します。
1. **[ギャラリーから追加する]** セクションで、検索ボックスに「**Check Point CloudGuard Dome9 Arc**」と入力します。
1. 結果パネルから **[Check Point CloudGuard Dome9 Arc]** を選択し、アプリを追加します。 お使いのテナントにアプリが追加されるのを数秒待機します。

## <a name="configure-and-test-azure-ad-single-sign-on-for-check-point-cloudguard-dome9-arc"></a>Check Point CloudGuard Dome9 Arc の Azure AD シングル サインオンの構成とテスト

**B.Simon** というテスト ユーザーを使用して、Check Point CloudGuard Dome9 Arc に対する Azure AD SSO を構成してテストします。 SSO を機能させるためには、Azure AD ユーザーと Check Point CloudGuard Dome9 Arc の関連ユーザーとの間にリンク関係を確立する必要があります。

Check Point CloudGuard Dome9 Arc で Azure AD の SSO を構成してテストするには、次の構成要素を完了します。

1. **[Azure AD SSO の構成](#configure-azure-ad-sso)** - ユーザーがこの機能を使用できるようにします。
    1. **[Azure AD のテスト ユーザーの作成](#create-an-azure-ad-test-user)** - B.Simon で Azure AD のシングル サインオンをテストします。
    1. **[Azure AD テスト ユーザーの割り当て](#assign-the-azure-ad-test-user)** - B.Simon が Azure AD シングル サインオンを使用できるようにします。
1. **[Check Point CloudGuard Dome9 Arc SSO の構成](#configure-check-point-cloudguard-dome9-arc-sso)** - アプリケーション側でシングル サインオン設定を構成します。
    1. **[Check Point CloudGuard Dome9 Arc テスト ユーザーの作成](#create-check-point-cloudguard-dome9-arc-test-user)** - Check Point CloudGuard Dome9 Arc で B.Simon に対応するユーザーを作成し、Azure AD の B.Simon にリンクさせます。
1. **[SSO のテスト](#test-sso)** - 構成が機能するかどうかを確認します。

## <a name="configure-azure-ad-sso"></a>Azure AD SSO の構成

これらの手順に従って、Azure portal で Azure AD SSO を有効にします。

1. [Azure portal](https://portal.azure.com/) の **Check Point CloudGuard Dome9 Arc** アプリケーション統合ページで、 **[管理]** セクションを見つけて、 **[シングル サインオン]** を選択します。
1. **[シングル サインオン方式の選択]** ページで、 **[SAML]** を選択します。
1. **[SAML でシングル サインオンをセットアップします]** ページで、 **[基本的な SAML 構成]** の編集 (ペン) アイコンをクリックして設定を編集します。

   ![基本的な SAML 構成を編集する](common/edit-urls.png)

1. **[基本的な SAML 構成]** セクションで、アプリケーションを **IDP** 開始モードで構成する場合は、次のフィールドの値を入力します。

    a. **[識別子]** テキスト ボックスに、`https://secure.dome9.com/` という URL を入力します。

    b. **[応答 URL]** ボックスに、`https://secure.dome9.com/sso/saml/<yourcompanyname>` のパターンを使用して URL を入力します

1. アプリケーションを **SP** 開始モードで構成する場合は、 **[追加の URL を設定します]** をクリックして次の手順を実行します。

    **[サインオン URL]** ボックスに、`https://secure.dome9.com/sso/saml/<yourcompanyname>` という形式で URL を入力します。

    > [!NOTE]
    > これらは実際の値ではありません。 実際の応答 URL とサインオン URL でこれらの値を更新します。 `<company name>` 値の値は、このチュートリアルで後述する「**Check Point CloudGuard Dome9 Arc SSO の構成**」セクションで取得します。 Azure portal の **[基本的な SAML 構成]** セクションに示されているパターンを参照することもできます。

1. Check Point CloudGuard Dome9 Arc アプリケーションでは、特定の形式の SAML アサーションを受け取るため、SAML トークン属性の構成にカスタム属性マッピングを追加する必要があります。 次のスクリーンショットには、既定の属性一覧が示されています。

    ![image](common/edit-attribute.png)

1. その他に、Check Point CloudGuard Dome9 Arc アプリケーションでは、以下に示したいくつかの属性が SAML 応答で返されることが想定されています。 これらの属性も値が事前に設定されますが、要件に従ってそれらの値を確認することができます。
    
    | 名前 |  ソース属性|
    | ---------------| --------------- |
    | memberof | user.assignedroles |

    >[!NOTE]
    >Azure AD でロールを作成する方法については、[ここ](https://docs.microsoft.com/azure/active-directory/saas-apps/apptio-tutorial)をクリックしてください。

1. **[SAML でシングル サインオンをセットアップします]** ページの **[SAML 署名証明書]** セクションで、 **[証明書 (Base64)]** を見つけて、 **[ダウンロード]** を選択し、証明書をダウンロードして、お使いのコンピューターに保存します。

    ![証明書のダウンロードのリンク](common/certificatebase64.png)

1. **[Set up Check Point CloudGuard Dome9 Arc]\(Check Point CloudGuard Dome9 Arc の設定\)** セクションで、要件に基づいて適切な URL をコピーします。

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

このセクションでは、B.Simon に Check Point CloudGuard Dome9 Arc へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

1. Azure portal で **[エンタープライズ アプリケーション]** を選択し、 **[すべてのアプリケーション]** を選択します。
1. アプリケーションの一覧で、 **[Check Point CloudGuard Dome9 Arc]** を選択します。
1. アプリの概要ページで、 **[管理]** セクションを見つけて、 **[ユーザーとグループ]** を選択します。

   ![[ユーザーとグループ] リンク](common/users-groups-blade.png)

1. **[ユーザーの追加]** を選択し、 **[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。

    ![[ユーザーの追加] リンク](common/add-assign-user.png)

1. **[ユーザーとグループ]** ダイアログの [ユーザー] の一覧から **[B.Simon]** を選択し、画面の下部にある **[選択]** ボタンをクリックします。
1. SAML アサーション内に任意のロール値が必要な場合、 **[ロールの選択]** ダイアログでユーザーに適したロールを一覧から選択し、画面の下部にある **[選択]** をクリックします。
1. **[割り当ての追加]** ダイアログで、 **[割り当て]** をクリックします。

## <a name="configure-check-point-cloudguard-dome9-arc-sso"></a>Check Point CloudGuard Dome9 Arc SSO の構成

1. Check Point CloudGuard Dome9 Arc 内での構成を自動化するには、 **[拡張機能のインストール]** をクリックして、**My Apps Secure Sign-in ブラウザー拡張機能**をインストールする必要があります。

    ![マイ アプリの拡張機能](common/install-myappssecure-extension.png)

2. ブラウザーに拡張機能を追加した後、 **[Check Point CloudGuard Dome9 Arc のセットアップ]** をクリックすると、Check Point CloudGuard Dome9 Arc アプリケーションが表示されます。 そこから、管理者の資格情報を入力して Check Point CloudGuard Dome9 Arc にサインインします。ブラウザー拡張機能によりアプリケーションが自動的に構成され、手順 3 ～ 6 が自動化されます。

    ![セットアップの構成](common/setup-sso.png)

3. Check Point CloudGuard Dome9 Arc を手動でセットアップする場合は、新しい Web ブラウザー ウィンドウを開き、管理者として Check Point CloudGuard Dome9 Arc 企業サイトにサインインして、次の手順を実行します。

2. 右上隅にある **[プロファイル設定]** をクリックしてから、 **[アカウント設定]** をクリックします。 

    ![Check Point CloudGuard Dome9 Arc の構成](./media/dome9arc-tutorial/configure1.png)

3. **[SSO]** に移動し、 **[有効にする]** をクリックします。

    ![Check Point CloudGuard Dome9 Arc の構成](./media/dome9arc-tutorial/configure2.png)

4. [SSO 構成] セクションで、次の手順を実行します。

    ![Check Point CloudGuard Dome9 Arc の構成](./media/dome9arc-tutorial/configure3.png)

    a. **[アカウント ID]** テキスト ボックスに会社名を入力します。 この値は、Azure portal の **[基本的な SAML 構成]** セクションに記載されている**応答** URL と**サインオン** URL に使用されます。

    b. **[Issuer]\(発行者\)** ボックスに、Azure portal からコピーした **Azure AD 識別子**の値を貼り付けます。

    c. **[Idp endpoint url]\(Idp エンドポイント URL\)** ボックスに、Azure portal からコピーした**ログイン URL** の値を貼り付けます。

    d. ダウンロードされた Base64 でエンコードされた証明書をメモ帳で開き、その内容をクリップボードにコピーしてから、それを **[X.509 証明書]** テキスト ボックスに貼り付けます。

    e. **[保存]** をクリックします。

### <a name="create-check-point-cloudguard-dome9-arc-test-user"></a>Check Point CloudGuard Dome9 Arc のテスト ユーザーの作成

Azure AD ユーザーが Check Point CloudGuard Dome9 Arc にサインインできるようにするには、そのユーザーをアプリケーションにプロビジョニングする必要があります。 Check Point CloudGuard Dome9 Arc はジャストインタイムのプロビジョニングをサポートしていますが、それが正しく機能するには、ユーザーが特定の**ロール**を選択し、同じものをそのユーザーに割り当てる必要があります。

   >[!Note]
   >**ロール**の作成やその他の詳細については、[Check Point CloudGuard Dome9 Arc クライアント サポート チーム](mailto:Dome9@checkpoint.com)に問い合わせてください。

**ユーザー アカウントを手動でプロビジョニングするには、次の手順を実行します。**

1. Check Point CloudGuard Dome9 Arc 企業サイトに管理者としてサインインします。

2. **[Users & Roles] (ユーザーとロール)** をクリックしてから、 **[ユーザー]** をクリックします。

    ![従業員の追加](./media/dome9arc-tutorial/user1.png)

3. **[ユーザーの追加]** をクリックします。

    ![従業員の追加](./media/dome9arc-tutorial/user2.png)

4. **[Create User]** セクションで、次の手順に従います。

    ![従業員の追加](./media/dome9arc-tutorial/user3.png)

    a. **[電子メール]** テキスト ボックスに、ユーザーの電子メール (B.Simon@contoso.com など) を入力します。

    b. **[First Name]\(名\)** ボックスに、ユーザーの名を入力します (この例では B)。

    c. **[Last Name]\(姓\)** ボックスに、ユーザーの姓を入力します (この例では Simon)。

    d. **[SSO User] (SSO ユーザー)** を **[オン]** にします。

    e. **[作成]** をクリックします。

## <a name="test-sso"></a>SSO のテスト 

このセクションでは、アクセス パネルを使用して Azure AD のシングル サインオン構成をテストします。

アクセス パネルで [Check Point CloudGuard Dome9 Arc] タイルをクリックすると、SSO を設定した Check Point CloudGuard Dome9 Arc に自動的にサインインします。 アクセス パネルの詳細については、[アクセス パネルの概要](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)に関する記事を参照してください。

## <a name="additional-resources"></a>その他のリソース

- [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Azure Active Directory でのアプリケーション アクセスとシングル サインオンとは](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Azure Active Directory の条件付きアクセスとは](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Azure AD で Check Point CloudGuard Dome9 Arc を試す](https://aad.portal.azure.com/)