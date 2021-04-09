---
title: チュートリアル:Azure Active Directory と RunMyProcess の統合 | Microsoft Docs
description: Azure Active Directory と RunMyProcess の間でシングル サインオンを構成する方法について説明します。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 08/07/2019
ms.author: jeedes
ms.openlocfilehash: 011bbbda07806f1493ae27fbeef8509f0d12c44b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "92518451"
---
# <a name="tutorial-integrate-runmyprocess-with-azure-active-directory"></a>チュートリアル:RunMyProcess と Azure Active Directory の統合

このチュートリアルでは、RunMyProcess と Azure Active Directory (Azure AD) を統合する方法について説明します。 Azure AD と RunMyProcess を統合すると、次のことができます。

* RunMyProcess にアクセスする Azure AD ユーザーを制御できます。
* ユーザーが自分の Azure AD アカウントを使用して RunMyProcess に自動的にサインインできるようにすることができます。
* 1 つの中央サイト (Azure Portal) で自分のアカウントを管理します。

SaaS アプリと Azure AD の統合の詳細については、「[Azure Active Directory でのアプリケーションへのシングル サインオン](../manage-apps/what-is-single-sign-on.md)」を参照してください。

## <a name="prerequisites"></a>前提条件

開始するには、次が必要です。

* Azure AD サブスクリプション。 サブスクリプションがない場合は、[無料アカウント](https://azure.microsoft.com/free/)を取得できます。
* RunMyProcess でのシングル サインオン (SSO) が有効なサブスクリプション。

## <a name="scenario-description"></a>シナリオの説明

このチュートリアルでは、テスト環境で Azure AD の SSO を構成してテストします。

* RunMyProcess では、**SP** Initiated SSO がサポートされます

## <a name="adding-runmyprocess-from-the-gallery"></a>ギャラリーからの RunMyProcess の追加

Azure AD への RunMyProcess の統合を構成するには、ギャラリーから管理対象 SaaS アプリの一覧に RunMyProcess を追加する必要があります。

1. 職場または学校アカウントか、個人の Microsoft アカウントを使用して、[Azure portal](https://portal.azure.com) にサインインします。
1. 左のナビゲーション ウィンドウで **[Azure Active Directory]** サービスを選択します。
1. **[エンタープライズ アプリケーション]** に移動し、 **[すべてのアプリケーション]** を選択します。
1. 新しいアプリケーションを追加するには、 **[新しいアプリケーション]** を選択します。
1. **[ギャラリーから追加する]** セクションで、検索ボックスに「**RunMyProcess**」と入力します。
1. 結果ウィンドウで **[RunMyProcess]** を選択し、アプリケーションを追加します。 お使いのテナントにアプリが追加されるのを数秒待機します。

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成とテスト

**B.Simon** というテスト ユーザーを使用して、RunMyProcess に対する Azure AD SSO を構成してテストします。 SSO が機能するために、Azure AD ユーザーと RunMyProcess の関連ユーザーとの間にリンク関係を確立する必要があります。

RunMyProcess で Azure AD の SSO を構成してテストするには、次の構成要素を完了します。

1. **[Azure AD SSO の構成](#configure-azure-ad-sso)** - ユーザーがこの機能を使用できるようにします。
2. **[RunMyProcess SSO の構成](#configure-runmyprocess-sso)** - アプリケーション側でシングル サインオン設定を構成します。
3. **[Azure AD のテスト ユーザーの作成](#create-an-azure-ad-test-user)** - B.Simon で Azure AD のシングル サインオンをテストします。
4. **[Azure AD テスト ユーザーの割り当て](#assign-the-azure-ad-test-user)** - B.Simon が Azure AD シングル サインオンを使用できるようにします。
5. **[RunMyProcess のテスト ユーザーの作成](#create-runmyprocess-test-user)** - RunMyProcess で B.Simon に対応するユーザーを作成し、Azure AD の B.Simon にリンクさせます。
6. **[SSO のテスト](#test-sso)** - 構成が機能するかどうかを確認します。

### <a name="configure-azure-ad-sso"></a>Azure AD SSO の構成

これらの手順に従って、Azure portal で Azure AD SSO を有効にします。

1. [Azure portal](https://portal.azure.com/) の **RunMyProcess** アプリケーション統合ページで、 **[管理]** セクションを見つけて、 **[シングル サインオン]** を選択します。
1. **[シングル サインオン方式の選択]** ページで、 **[SAML]** を選択します。
1. **[SAML でシングル サインオンをセットアップします]** ページで、 **[基本的な SAML 構成]** の編集/ペン アイコンをクリックして設定を編集します。

   ![基本的な SAML 構成を編集する](common/edit-urls.png)

1. **[基本的な SAML 構成]** セクションで、次のフィールドの値を入力します。

    **[サインオン URL]** ボックスに、`https://live.runmyprocess.com/live/<tenant id>` という形式で URL を入力します。

    > [!NOTE]
    > この値は実際のものではありません。 実際のサインオン URL でこの値を更新してください。 この値を取得するには、[RunMyProcess クライアント サポート チーム](mailto:support@runmyprocess.com)に問い合わせてください。 Azure portal の **[基本的な SAML 構成]** セクションに示されているパターンを参照することもできます。

1. **[SAML でシングル サインオンをセットアップします]** ページの **[SAML 署名証明書]** セクションで、 **[証明書 (Base64)]** を見つけて、 **[ダウンロード]** を選択し、証明書をダウンロードして、お使いのコンピューターに保存します。

    ![証明書のダウンロードのリンク](common/certificatebase64.png)

1. **[RunMyProcess のセットアップ]** セクションで、ご自分の要件に基づいて適切な URL をコピーします。

    ![構成 URL のコピー](common/copy-configuration-urls.png)

### <a name="configure-runmyprocess-sso"></a>RunMyProcess SSO の構成

1. 別の Web ブラウザーのウィンドウで、管理者として RunMyProcess テナントにサインオンします。

1. 左側のナビゲーション パネルで、 **[Account (アカウント)]** 、 **[Configuration (構成)]** の順にクリックします。

    ![[Account]\(アカウント\) から選択された [Configuration]\(構成\) を示すスクリーンショット。](./media/runmyprocess-tutorial/tutorial_runmyprocess_001.png)

1. **[Authentication method (認証方法)]** セクションに移動し、次の手順に従います。

    ![スクリーンショットは、説明した値を入力できる [Authentication method]\(認証方法\) タブを示しています。](./media/runmyprocess-tutorial/tutorial_runmyprocess_002.png)

    a. **[Method]** として、 **[SSO with Samlv2]** を選択します。

    b. **[SSO redirect]\(SSO リダイレクト\)** ボックスに、Azure portal からコピーした **ログイン URL** の値を貼り付けます。

    c. **[Logout redirect]\(ログアウト リダイレクト\)** ボックスに、Azure portal からコピーした **ログアウト URL** の値を貼り付けます。

    d. **[Name ID Format]\(名前識別子形式\)** ボックスで、 **[名前識別子形式]** の値を「**urn:oasis:names:tc:SAML:1.1:nameid-format:emailAddress**」のように入力します。

    e. Azure portal からダウンロードした証明書ファイルをメモ帳で開き、その内容をコピーして **[Certificate]\(証明書\)** ボックスに貼り付けます。

    f. **[Save (保存)]** アイコンをクリックします。

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

このセクションでは、B.Simon に RunMyProcess へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

1. Azure portal で **[エンタープライズ アプリケーション]** を選択し、 **[すべてのアプリケーション]** を選択します。
1. アプリケーションの一覧で **[RunMyProcess]** を選択します。
1. アプリの概要ページで、 **[管理]** セクションを見つけて、 **[ユーザーとグループ]** を選択します。

   ![[ユーザーとグループ] リンク](common/users-groups-blade.png)

1. **[ユーザーの追加]** を選択し、 **[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。

    ![[ユーザーの追加] リンク](common/add-assign-user.png)

1. **[ユーザーとグループ]** ダイアログの [ユーザー] の一覧から **[B.Simon]** を選択し、画面の下部にある **[選択]** ボタンをクリックします。
1. SAML アサーション内に任意のロール値が必要な場合、 **[ロールの選択]** ダイアログでユーザーに適したロールを一覧から選択し、画面の下部にある **[選択]** をクリックします。
1. **[割り当ての追加]** ダイアログで、 **[割り当て]** をクリックします。

### <a name="create-runmyprocess-test-user"></a>RunMyProcess テスト ユーザーの作成

Azure AD ユーザーが RunMyProcess にサインインできるようにするには、ユーザーを RunMyProcess にプロビジョニングする必要があります。 RunMyProcess の場合、プロビジョニングは手動で行います。

**ユーザー アカウントをプロビジョニングするには、次の手順に従います。**

1. RunMyProcess 企業サイトに管理者としてサインインします。

1. 左側のナビゲーション パネルで **[Account (アカウント)]** 、 **[Users (ユーザー)]** の順にクリックし、 **[New User (新しいユーザー)]** をクリックします。

    ![[New User]\(新しいユーザー\)](./media/runmyprocess-tutorial/tutorial_runmyprocess_003.png "[新しいユーザー]")

1. **[ユーザーの設定]** セクションで、次の手順に従います。

    ![プロファイル](./media/runmyprocess-tutorial/tutorial_runmyprocess_004.png "プロファイル")
  
    a. プロビジョニングする有効な Azure AD アカウントの **名前** と **メール** を対応するボックスに入力します。

    b. **[IDE 言語]** 、 **[言語]** 、 **[プロファイル]** を選択します。

    c. **[アカウント作成の電子メールを自分に送信]** を選択します。

    d. **[保存]** をクリックします。

    > [!NOTE]
    > 他の RunMyProcess ユーザー アカウントの作成ツールまたは RunMyProcess から提供されている API を使用して、Azure Active Directory ユーザー アカウントをプロビジョニングできます。

### <a name="test-sso"></a>SSO のテスト 

このセクションでは、アクセス パネルを使用して Azure AD のシングル サインオン構成をテストします。

アクセス パネルで [RunMyProcess] タイルをクリックすると、SSO を設定した RunMyProcess に自動的にサインインします。 アクセス パネルの詳細については、[アクセス パネルの概要](../user-help/my-apps-portal-end-user-access.md)に関する記事を参照してください。

## <a name="additional-resources"></a>その他のリソース

- [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](./tutorial-list.md)

- [Azure Active Directory でのアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)

- [Azure Active Directory の条件付きアクセスとは](../conditional-access/overview.md)