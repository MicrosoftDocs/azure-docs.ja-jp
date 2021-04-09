---
title: チュートリアル:Azure Active Directory と Carbonite Endpoint Backup の統合 | Microsoft Docs
description: Azure Active Directory と Carbonite Endpoint Backup の間でシングル サインオンを構成する方法について説明します。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 08/06/2019
ms.author: jeedes
ms.openlocfilehash: ff19275270e5b6572fb7d637b88c4736a3aa6ea0
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "92456485"
---
# <a name="tutorial-integrate-carbonite-endpoint-backup-with-azure-active-directory"></a>チュートリアル:Carbonite Endpoint Backup と Azure Active Directory の統合

このチュートリアルでは、Carbonite Endpoint Backup と Azure Active Directory (Azure AD) を統合する方法について説明します。 Carbonite Endpoint Backup と Azure AD を統合すると、次のことができます。

* Carbonite Endpoint Backup にアクセスするユーザーを Azure AD で管理します。
* ユーザーが自分の Azure AD アカウントを使用して Carbonite Endpoint Backup に自動的にサインインできるようにします。
* 1 つの中央サイト (Azure Portal) で自分のアカウントを管理します。

SaaS アプリと Azure AD の統合の詳細については、「[Azure Active Directory でのアプリケーションへのシングル サインオン](../manage-apps/what-is-single-sign-on.md)」を参照してください。

## <a name="prerequisites"></a>前提条件

開始するには、次が必要です。

* Azure AD サブスクリプション。 サブスクリプションがない場合は、[無料アカウント](https://azure.microsoft.com/free/)を取得できます。
* Carbonite Endpoint Backup でのシングル サインオン (SSO) が有効なサブスクリプション。

## <a name="scenario-description"></a>シナリオの説明

このチュートリアルでは、テスト環境で Azure AD の SSO を構成してテストします。

* Carbonite Endpoint Backup では、**SP および IDP** で開始される SSO がサポートされます

## <a name="adding-carbonite-endpoint-backup-from-the-gallery"></a>ギャラリーからの Carbonite Endpoint Backup の追加

Azure AD への Carbonite Endpoint Backup の統合を構成するには、ギャラリーからマネージド SaaS アプリの一覧に Carbonite Endpoint Backup を追加する必要があります。

1. 職場または学校アカウントか、個人の Microsoft アカウントを使用して、[Azure portal](https://portal.azure.com) にサインインします。
1. 左のナビゲーション ウィンドウで **[Azure Active Directory]** サービスを選択します。
1. **[エンタープライズ アプリケーション]** に移動し、 **[すべてのアプリケーション]** を選択します。
1. 新しいアプリケーションを追加するには、 **[新しいアプリケーション]** を選択します。
1. **[ギャラリーから追加する]** セクションで、検索ボックスに、「**Carbonite Endpoint Backup**」と入力します。
1. 結果ウィンドウで **[Carbonite Endpoint Backup]** を選択し、アプリを追加します。 お使いのテナントにアプリが追加されるのを数秒待機します。

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成とテスト

**B.Simon** というテスト ユーザーを使用して、Carbonite Endpoint Backup に対する Azure AD SSO を構成してテストします。 SSO を機能させるには、Azure AD ユーザーと Carbonite Endpoint Backup の関連ユーザーの間で、リンク関係を確立する必要があります。

Carbonite Endpoint Backup で Azure AD SSO を構成してテストするには、次の構成要素を完了する必要があります。

1. **[Azure AD SSO の構成](#configure-azure-ad-sso)** - ユーザーがこの機能を使用できるようにします。
2. **[Carbonite Endpoint Backup SSO の構成](#configure-carbonite-endpoint-backup-sso)** - アプリケーション側でシングル サインオン設定を構成します。
3. **[Azure AD のテスト ユーザーの作成](#create-an-azure-ad-test-user)** - B.Simon で Azure AD のシングル サインオンをテストします。
4. **[Azure AD テスト ユーザーの割り当て](#assign-the-azure-ad-test-user)** - B.Simon が Azure AD シングル サインオンを使用できるようにします。
5. **[Carbonite Endpoint Backup テスト ユーザーの作成](#create-carbonite-endpoint-backup-test-user)** - Carbonite Endpoint Backup で B.Simon に対応するユーザーを作成し、Azure AD の B.Simon にリンクさせます。
6. **[SSO のテスト](#test-sso)** - 構成が機能するかどうかを確認します。

### <a name="configure-azure-ad-sso"></a>Azure AD SSO の構成

これらの手順に従って、Azure portal で Azure AD SSO を有効にします。

1. [Azure portal](https://portal.azure.com/) の **Carbonite Endpoint Backup** アプリケーション統合ページで、 **[管理]** セクションを見つけて、 **[シングル サインオン]** を選択します。
1. **[シングル サインオン方式の選択]** ページで、 **[SAML]** を選択します。
1. **[SAML でシングル サインオンをセットアップします]** ページで、 **[基本的な SAML 構成]** の編集/ペン アイコンをクリックして設定を編集します。

   ![基本的な SAML 構成を編集する](common/edit-urls.png)

1. **[基本的な SAML 構成]** セクションで、アプリケーションを **IDP** 開始モードで構成する場合は、次のフィールドの値を入力します。

    a. **[識別子]** ボックスに、次のいずれかの URL を入力します。

    ```http
    https://red-us.mysecuredatavault.com
    https://red-apac.mysecuredatavault.com
    https://red-fr.mysecuredatavault.com
    https://red-emea.mysecuredatavault.com
    https://kamino.mysecuredatavault.com
    ```

    b. **[応答 URL]** ボックスに、次のいずれかの URL を入力します。

    ```http
    https://red-us.mysecuredatavault.com/AssertionConsumerService.aspx
    https://red-apac.mysecuredatavault.com/AssertionConsumerService.aspx
    https://red-fr.mysecuredatavault.com/AssertionConsumerService.aspx
    https://red-emea.mysecuredatavault.com/AssertionConsumerService.aspx
    ```

1. アプリケーションを **SP** 開始モードで構成する場合は、 **[追加の URL を設定します]** をクリックして次の手順を実行します。

    **[サインオン URL]** ボックスに、次のいずれかの URL を入力します。

    ```http
    https://red-us.mysecuredatavault.com/
    https://red-apac.mysecuredatavault.com/
    https://red-fr.mysecuredatavault.com/
    https://red-emea.mysecuredatavault.com/
    ```

1. **[SAML でシングル サインオンをセットアップします]** ページの **[SAML 署名証明書]** セクションで、 **[証明書 (Base64)]** を見つけて、 **[ダウンロード]** を選択し、証明書をダウンロードして、お使いのコンピューターに保存します。

    ![証明書のダウンロードのリンク](common/certificatebase64.png)

1. **[Carbonite Endpoint Backup のセットアップ]** セクションで、要件に従って適切な URL をコピーします。

    ![構成 URL のコピー](common/copy-configuration-urls.png)

### <a name="configure-carbonite-endpoint-backup-sso"></a>Carbonite Endpoint Backup SSO の構成

1. Carbonite Endpoint Backup 内での構成を自動化するには、 **[拡張機能のインストール]** をクリックして、**My Apps Secure Sign-in ブラウザー拡張機能** をインストールする必要があります。

    ![マイ アプリの拡張機能](common/install-myappssecure-extension.png)

2. ブラウザーに拡張機能を追加した後、 **[Carbonite Endpoint Backup のセットアップ]** をクリックすると、Carbonite Endpoint Backup アプリケーションに移動します。 そこから、管理者の資格情報を入力して Carbonite Endpoint Backup にサインインします。 ブラウザー拡張機能によりアプリケーションが自動的に構成され、手順 3 ～ 7 が自動化されます。

    ![セットアップの構成](common/setup-sso.png)

3. Carbonite Endpoint Backup を手動でセットアップする場合は、新しい Web ブラウザー ウィンドウを開き、管理者としてご自分の Carbonite Endpoint Backup 企業サイトにサインインして、次の手順のようにします。

4. 左側のウィンドウの **[Company]\(会社\)** をクリックします。

    ![スクリーンショットは、[Company]\(会社\) が選択されている Carbonite Endpoint を示しています。](media/carbonite-endpoint-backup-tutorial/configure1.png)

5. **[Single sign-on]\(シングル サインオン\)** をクリックします。

    ![スクリーンショットは、[Single sign-on]\(シングル サインオン\) が選択されている [Company]\(会社\) を示しています。](media/carbonite-endpoint-backup-tutorial/configure2.png)

6. **[Enable]\(有効化\)** をクリックし、 **[Edit settings]\(設定の編集\)** をクリックして構成します。

    ![スクリーンショットは、[Enable]\(有効化\) と [Edit settings]\(設定の編集\) が選択されている [Single sign-on]\(シングル サインオン\) タブを示しています。](media/carbonite-endpoint-backup-tutorial/configure3.png)

7. **[Single sign-on]\(シングル サインオン\)** 設定ページで、次の手順を実行します。

    ![スクリーンショットは、この手順で説明されている情報を含む [Single sign-on]\(シングル サインオン\) タブを示しています。](media/carbonite-endpoint-backup-tutorial/configure4.png)

    1. **[Identity provider name]\(ID プロバイダー名\)** ボックスに、Azure portal からコピーした **Azure AD 識別子** の値を貼り付けます。

    1. **[Identity provider URL]\(ID プロバイダーの URL\)** ボックスに、Azure portal からコピーした **ログイン URL** の値を貼り付けます。

    1. **[Choose file]\(ファイルの選択\)** をクリックして、Azure portal からダウンロードした **証明書 (Base64)** をアップロードします。

    1. **[保存]** をクリックします。

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

このセクションでは、B.Simon に Carbonite Endpoint Backup へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

1. Azure portal で **[エンタープライズ アプリケーション]** を選択し、 **[すべてのアプリケーション]** を選択します。
1. アプリケーションの一覧で **[Carbonite Endpoint Backup]** を選択します。
1. アプリの概要ページで、 **[管理]** セクションを見つけて、 **[ユーザーとグループ]** を選択します。

   ![[ユーザーとグループ] リンク](common/users-groups-blade.png)

1. **[ユーザーの追加]** を選択し、 **[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。

    ![[ユーザーの追加] リンク](common/add-assign-user.png)

1. **[ユーザーとグループ]** ダイアログの [ユーザー] の一覧から **[B.Simon]** を選択し、画面の下部にある **[選択]** ボタンをクリックします。
1. SAML アサーション内に任意のロール値が必要な場合、 **[ロールの選択]** ダイアログでユーザーに適したロールを一覧から選択し、画面の下部にある **[選択]** をクリックします。
1. **[割り当ての追加]** ダイアログで、 **[割り当て]** をクリックします。

### <a name="create-carbonite-endpoint-backup-test-user"></a>Carbonite Endpoint Backup のテスト ユーザーの作成

1. 別の Web ブラウザー ウィンドウで、Carbonite Endpoint Backup 企業サイトに管理者としてサインインします。

1. 左側のウィンドウの **[Users]\(ユーザー\)** をクリックし、 **[Add user]\(ユーザーの追加\)** をクリックします。

    ![スクリーンショットは、[Users]\(ユーザー\) と [Add user]\(ユーザーの追加\) が選択されている [Carbonite Endpoint] ページを示しています。](media/carbonite-endpoint-backup-tutorial/adduser1.png)

1. **[Add user]\(ユーザーの追加\)** ページで、次の手順に従います。

    ![スクリーンショットは、ここで説明されている手順を実行できる [Add user]\(ユーザーの追加\) ページを示しています。](media/carbonite-endpoint-backup-tutorial/adduser2.png)

    1. ユーザーの **[Email]\(メール\)** 、 **[First name]\(名\)** 、 **[Last name]\(姓\)** を入力し、組織の要件に従って、必要なアクセス許可をユーザーに提供します。

    1. **[ユーザーの追加]** をクリックします。

### <a name="test-sso"></a>SSO のテスト

このセクションでは、アクセス パネルを使用して Azure AD のシングル サインオン構成をテストします。

アクセス パネルで [Carbonite Endpoint Backup] タイルをクリックすると、SSO を設定した Carbonite Endpoint Backup に自動的にサインインします。 アクセス パネルの詳細については、[アクセス パネルの概要](../user-help/my-apps-portal-end-user-access.md)に関する記事を参照してください。

## <a name="additional-resources"></a>その他のリソース

- [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](./tutorial-list.md)

- [Azure Active Directory でのアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)

- [Azure Active Directory の条件付きアクセスとは](../conditional-access/overview.md)