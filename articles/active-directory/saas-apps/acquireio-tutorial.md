---
title: チュートリアル:Azure Active Directory シングル サインオン (SSO) と AcquireIO の統合 | Microsoft Docs
description: Azure Active Directory と AcquireIO の間でシングル サインオンを構成する方法について説明します。
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
ms.openlocfilehash: 5fe070bc1abe0592b3082c597c1812781335448a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "97673188"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-acquireio"></a>チュートリアル:Azure Active Directory シングル サインオン (SSO) と AcquireIO の統合

このチュートリアルでは、AcquireIO と Azure Active Directory (Azure AD) を統合する方法について説明します。 Azure AD と AcquireIO を統合すると、次のことができます。

* AcquireIO にアクセスできるユーザーを Azure AD で制御できます。
* ユーザーが自分の Azure AD アカウントを使用して AcquireIO に自動的にサインインするように設定できます。
* 1 つの中央サイト (Azure Portal) で自分のアカウントを管理します。

SaaS アプリと Azure AD の統合の詳細については、「[Azure Active Directory でのアプリケーションへのシングル サインオン](../manage-apps/what-is-single-sign-on.md)」を参照してください。

## <a name="prerequisites"></a>前提条件

開始するには、次が必要です。

* Azure AD サブスクリプション。 サブスクリプションがない場合は、[無料アカウント](https://azure.microsoft.com/free/)を取得できます。
* AcquireIO でのシングル サインオン (SSO) が有効なサブスクリプション。

## <a name="scenario-description"></a>シナリオの説明

このチュートリアルでは、テスト環境で Azure AD の SSO を構成してテストします。

* AcquireIO では、**IDP** Initiated SSO がサポートされます

## <a name="adding-acquireio-from-the-gallery"></a>ギャラリーからの AcquireIO の追加

Azure AD への AcquireIO の統合を構成するには、ギャラリーから管理対象 SaaS アプリの一覧に AcquireIO を追加する必要があります。

1. 職場または学校アカウントか、個人の Microsoft アカウントを使用して、[Azure portal](https://portal.azure.com) にサインインします。
1. 左のナビゲーション ウィンドウで **[Azure Active Directory]** サービスを選択します。
1. **[エンタープライズ アプリケーション]** に移動し、 **[すべてのアプリケーション]** を選択します。
1. 新しいアプリケーションを追加するには、 **[新しいアプリケーション]** を選択します。
1. **[ギャラリーから追加する]** セクションで、検索ボックスに「**AcquireIO**」と入力します。
1. 結果のパネルから **[AcquireIO]** を選択し、アプリを追加します。 お使いのテナントにアプリが追加されるのを数秒待機します。

## <a name="configure-and-test-azure-ad-single-sign-on-for-acquireio"></a>AcquireIO の Azure AD シングル サインオンの構成とテスト

**B.Simon** というテスト ユーザーを使用して、AcquireIO に対する Azure AD SSO を構成してテストします。 SSO を機能させるために、Azure AD ユーザーと AcquireIO の関連ユーザーとの間にリンク関係を確立する必要があります。

AcquireIO に対する Azure AD SSO を構成してテストするには、次の構成要素を完了します。

1. **[Azure AD SSO の構成](#configure-azure-ad-sso)** - ユーザーがこの機能を使用できるようにします。
    * **[Azure AD のテスト ユーザーの作成](#create-an-azure-ad-test-user)** - B.Simon で Azure AD のシングル サインオンをテストします。
    * **[Azure AD テスト ユーザーの割り当て](#assign-the-azure-ad-test-user)** - B.Simon が Azure AD シングル サインオンを使用できるようにします。
1. **[AcquireIO SSO の構成](#configure-acquireio-sso)** - アプリケーション側でシングル サインオン設定を構成します。
    * **[AcquireIO テスト ユーザーの作成](#create-acquireio-test-user)** - AcquireIO で B.Simon に対応するユーザーを作成し、Azure AD のこのユーザーにリンクさせます。
1. **[SSO のテスト](#test-sso)** - 構成が機能するかどうかを確認します。

## <a name="configure-azure-ad-sso"></a>Azure AD SSO の構成

これらの手順に従って、Azure portal で Azure AD SSO を有効にします。

1. [Azure portal](https://portal.azure.com/) の **AcquireIO** アプリケーション統合ページで、 **[管理]** セクションを探して、 **[シングル サインオン]** を選択します。
1. **[シングル サインオン方式の選択]** ページで、 **[SAML]** を選択します。
1. **[SAML でシングル サインオンをセットアップします]** ページで、 **[基本的な SAML 構成]** の編集 (ペン) アイコンをクリックして設定を編集します。

    ![基本的な SAML 構成を編集する](common/edit-urls.png)

1. **[基本的な SAML 構成]** セクションで、次のフィールドの値を入力します。

    **[応答 URL]** ボックスに、`https://app.acquire.io/ad/<acquire_account_uid>` のパターンを使用して URL を入力します

    > [!NOTE]
    > この値は実際のものではありません。 実際の応答 URL を取得します。実際の値については、このチュートリアルの「**AcquireIO の構成**」セクションで説明します。 Azure portal の **[基本的な SAML 構成]** セクションに示されているパターンを参照することもできます。

1. **[SAML でシングル サインオンをセットアップします]** ページの **[SAML 署名証明書]** セクションで、 **[証明書 (Base64)]** を見つけて、 **[ダウンロード]** を選択し、証明書をダウンロードして、お使いのコンピューターに保存します。

    ![証明書のダウンロードのリンク](common/certificatebase64.png)

1. **[Set up AcquireIO]\(AcquireIO の設定\)** セクションで、要件に基づいて適切な URL をコピーします。

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

このセクションでは、B. Simon に AcquireIO へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

1. Azure portal で **[エンタープライズ アプリケーション]** を選択し、 **[すべてのアプリケーション]** を選択します。
1. アプリケーションの一覧で **[AcquireIO]** を選択します。
1. アプリの概要ページで、 **[管理]** セクションを見つけて、 **[ユーザーとグループ]** を選択します。

    ![[ユーザーとグループ] リンク](common/users-groups-blade.png)

1. **[ユーザーの追加]** を選択し、 **[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。

    ![[ユーザーの追加] リンク](common/add-assign-user.png)

1. **[ユーザーとグループ]** ダイアログの [ユーザー] の一覧から **[B.Simon]** を選択し、画面の下部にある **[選択]** ボタンをクリックします。
1. SAML アサーション内に任意のロール値が必要な場合、 **[ロールの選択]** ダイアログでユーザーに適したロールを一覧から選択し、画面の下部にある **[選択]** をクリックします。
1. **[割り当ての追加]** ダイアログで、 **[割り当て]** をクリックします。

## <a name="configure-acquireio-sso"></a>AcquireIO SSO の構成

1. AcquireIO 内での構成を自動化するには、 **[拡張機能のインストール]** をクリックして **My Apps Secure Sign-in ブラウザー拡張機能** をインストールする必要があります。

    ![マイ アプリの拡張機能](common/install-myappssecure-extension.png)

1. ブラウザーに拡張機能を追加した後、 **[AcquireIO のセットアップ]** をクリックすると、AcquireIO アプリケーションに移動します。 そこから、管理者の資格情報を入力して AcquireIO にサインインします。 ブラウザー拡張機能によりアプリケーションが自動的に構成され、手順 3 ～ 6 が自動化されます。

    ![セットアップの構成](common/setup-sso.png)

1. AcquireIO を手動で設定するには、別の Web ブラウザー ウィンドウで、管理者として AcquireIO にサインインします。

1. メニューの左側から **[App Store]\(アプリ ストア\)** をクリックします。

    ![[App Store]\(アプリ ストア\) が強調表示されているスクリーンショット。](./media/acquireio-tutorial/config01.png)

1. **[Active Directory]** まで下にスクロールして、 **[インストール]** をクリックします。

    ![[Active Directory] セクションと [インストール] ボタンが強調表示されているスクリーンショット。](./media/acquireio-tutorial/config02.png)

1. [Active Directory] ポップアップで、次の手順に従います。

    ![[Active Directory] 画面が表示されているスクリーンショット。](./media/acquireio-tutorial/config03.png)

    a. **[コピー]** をクリックして、インスタンスの応答 URL をコピーし、Azure portal の **[基本的な SAML 構成]** セクションの **[応答 URL]** ボックスに貼り付けます。

    b. **[ログイン URL]** ボックスに、Azure portal からコピーした **ログイン URL** の値を貼り付けます。

    c. Base64 でエンコードされた証明書をメモ帳で開き、その内容をコピーして **[X.509 証明書]** ボックスに貼り付けます。

    d. **[Connect Now]\(今すぐ接続\)** をクリックします。

### <a name="create-acquireio-test-user"></a>AcquireIO のテスト ユーザーの作成

Azure AD ユーザーが AcquireIO にサインインできるようにするには、そのユーザーを AcquireIO にプロビジョニングする必要があります。 AcquireIO では、プロビジョニングは手動のタスクです。

**ユーザー アカウントをプロビジョニングするには、次の手順に従います。**

1. 別の Web ブラウザー ウィンドウで、管理者として AcquireIO にサインインします。

1. メニューの左側で **[プロファイル]** をクリックして、 **[プロファイルの追加]** をクリックします。

    ![画面の左側にあるメニューの [プロファイル] と [プロファイルの追加] オプションが強調表示されているスクリーンショット。](./media/acquireio-tutorial/config04.png)

1. **[顧客の追加]** ポップアップで、次の手順に従います。

    ![AcquireIO の構成](./media/acquireio-tutorial/config05.png)

    a. **[Name]\(名前\)** ボックスに、ユーザーの氏名を入力します (例: **B.simon**)。

    b. **[Email]\(電子メール\)** ボックスに、ユーザーのメール アドレスを入力します (例: **B.simon@contoso.com** )。

    c. **[送信]** をクリックします。

## <a name="test-sso"></a>SSO のテスト 

このセクションでは、アクセス パネルを使用して Azure AD のシングル サインオン構成をテストします。

アクセス パネルで AcquireIO タイルをクリックすると、SSO を設定した AcquireIO に自動的にサインインします。 アクセス パネルの詳細については、[アクセス パネルの概要](../user-help/my-apps-portal-end-user-access.md)に関する記事を参照してください。

## <a name="additional-resources"></a>その他のリソース

- [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](./tutorial-list.md)

- [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)

- [Azure Active Directory の条件付きアクセスとは](../conditional-access/overview.md)

- [Azure AD で AcquireIO を試す](https://aad.portal.azure.com/)