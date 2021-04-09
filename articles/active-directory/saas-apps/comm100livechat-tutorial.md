---
title: チュートリアル:Azure Active Directory シングル サインオン (SSO) と Comm100 Live Chat の統合 | Microsoft Docs
description: Azure Active Directory と Comm100 Live Chat の間でシングル サインオンを構成する方法について説明します。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 10/22/2019
ms.author: jeedes
ms.openlocfilehash: 9636c8cbb517c7aece450f53cfc37e4ddd9803b7
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "92455495"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-comm100-live-chat"></a>チュートリアル:Azure Active Directory シングル サインオン (SSO) と Comm100 Live Chat の統合

このチュートリアルでは、Comm100 Live Chat と Azure Active Directory (Azure AD) を統合する方法について説明します。 Azure AD と Comm100 Live Chat を統合すると、次のことができます。

* Comm100 Live Chat にアクセスできるユーザーを Azure AD で制御できます。
* ユーザーが自分の Azure AD アカウントを使用して Comm100 Live Chat に自動的にサインインできるように設定できます。
* 1 つの中央サイト (Azure Portal) で自分のアカウントを管理します。

SaaS アプリと Azure AD の統合の詳細については、「[Azure Active Directory でのアプリケーションへのシングル サインオン](../manage-apps/what-is-single-sign-on.md)」を参照してください。

## <a name="prerequisites"></a>前提条件

開始するには、次が必要です。

* Azure AD サブスクリプション。 サブスクリプションがない場合は、[無料アカウント](https://azure.microsoft.com/free/)を取得できます。
* Comm100 Live Chat でのシングル サインオン (SSO) が有効なサブスクリプション。

## <a name="scenario-description"></a>シナリオの説明

このチュートリアルでは、テスト環境で Azure AD の SSO を構成してテストします。

* Comm100 Live Chat では、**SP** によって開始される SSO がサポートされます

> [!NOTE]
> このアプリケーションの識別子は固定文字列値であるため、1 つのテナントで構成できるインスタンスは 1 つだけです。

## <a name="adding-comm100-live-chat-from-the-gallery"></a>ギャラリーから Comm100 Live Chat を追加する

Azure AD への Comm100 Live Chat の統合を構成するには、ギャラリーから管理対象 SaaS アプリの一覧に Comm100 Live Chat を追加する必要があります。

1. 職場または学校アカウントか、個人の Microsoft アカウントを使用して、[Azure portal](https://portal.azure.com) にサインインします。
1. 左のナビゲーション ウィンドウで **[Azure Active Directory]** サービスを選択します。
1. **[エンタープライズ アプリケーション]** に移動し、 **[すべてのアプリケーション]** を選択します。
1. 新しいアプリケーションを追加するには、 **[新しいアプリケーション]** を選択します。
1. **[ギャラリーから追加する]** セクションで、検索ボックスに「**Comm100 Live Chat**」と入力します。
1. 結果ウィンドウで **Comm100 Live Chat** を選択し、アプリを追加します。 お使いのテナントにアプリが追加されるのを数秒待機します。


## <a name="configure-and-test-azure-ad-single-sign-on-for-comm100-live-chat"></a>Comm100 Live Chat の Azure AD シングル サインオンの構成とテスト

**B.Simon** というテスト ユーザーを使用して、Comm100 Live Chat に対する Azure AD SSO を構成してテストします。 SSO が機能するために、Azure AD ユーザーと Comm100 Live Chat の関連ユーザーの間で、リンク関係を確立する必要があります。

Comm100 Live Chat で Azure AD SSO を構成してテストするには、次の構成要素を完了する必要があります。

1. **[Azure AD SSO の構成](#configure-azure-ad-sso)** - ユーザーがこの機能を使用できるようにします。
    1. **[Azure AD のテスト ユーザーの作成](#create-an-azure-ad-test-user)** - B.Simon で Azure AD のシングル サインオンをテストします。
    1. **[Azure AD テスト ユーザーの割り当て](#assign-the-azure-ad-test-user)** - B.Simon が Azure AD シングル サインオンを使用できるようにします。
1. **[Comm100 Live Chat の SSO の構成](#configure-comm100-live-chat-sso)** - アプリケーション側でシングル サインオン設定を構成します。
    1. **[Comm100 Live Chat のテスト ユーザーの作成](#create-comm100-live-chat-test-user)** - Azure AD の B.Simon にリンクさせるために、対応するユーザーを Comm100 Live Chat で作成します。
1. **[SSO のテスト](#test-sso)** - 構成が機能するかどうかを確認します。

## <a name="configure-azure-ad-sso"></a>Azure AD SSO の構成

これらの手順に従って、Azure portal で Azure AD SSO を有効にします。

1. [Azure portal](https://portal.azure.com/) の **Comm100 Live Chat** アプリケーション統合ページで、 **[管理]** セクションを探して、 **[シングル サインオン]** を選択します。
1. **[シングル サインオン方式の選択]** ページで、 **[SAML]** を選択します。
1. **[SAML でシングル サインオンをセットアップします]** ページで、 **[基本的な SAML 構成]** の編集 (ペン) アイコンをクリックして設定を編集します。

   ![基本的な SAML 構成を編集する](common/edit-urls.png)

1. **[基本的な SAML 構成]** セクションで、次のフィールドの値を入力します。

    **[サインオン URL]** ボックスに、`https://<SUBDOMAIN>.comm100.com/AdminManage/LoginSSO.aspx?siteId=<SITEID>` という形式で URL を入力します。

    > [!NOTE] 
    > サインオン URL は実際の値ではありません。 サインオン URL の値は、後で実際のサインオン URL に置き換えることになります。実際の値については後で説明します。

1. Comm100 Live Chat アプリケーションは、特定の形式の SAML アサーションを使用するため、カスタム属性のマッピングを SAML トークンの属性の構成に追加する必要があります。 次のスクリーンショットには、既定の属性一覧が示されています。

    ![image](common/edit-attribute.png)

1. その他に、Comm100 Live Chat アプリケーションでは、いくつかの属性が SAML 応答で返されることが想定されています。それらの属性を次に示します。 これらの属性も値が事前に設定されますが、要件に従ってそれらの値を確認することができます。

    | 名前 |  ソース属性|
    | ---------------| --------------- |
    |   email    | User.mail |

1. **[SAML でシングル サインオンをセットアップします]** ページの **[SAML 署名証明書]** セクションで、 **[証明書 (Base64)]** を見つけて、 **[ダウンロード]** を選択し、証明書をダウンロードして、お使いのコンピューターに保存します。

    ![証明書のダウンロードのリンク](common/certificatebase64.png)

1. **[Comm100 Live Chat のセットアップ]** セクションで、要件に基づいて適切な URL をコピーします。

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

このセクションでは、B.Simon に Comm100 Live Chat へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

1. Azure portal で **[エンタープライズ アプリケーション]** を選択し、 **[すべてのアプリケーション]** を選択します。
1. アプリケーションの一覧で **[Comm100 Live Chat]** を選択します。
1. アプリの概要ページで、 **[管理]** セクションを見つけて、 **[ユーザーとグループ]** を選択します。

   ![[ユーザーとグループ] リンク](common/users-groups-blade.png)

1. **[ユーザーの追加]** を選択し、 **[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。

    ![[ユーザーの追加] リンク](common/add-assign-user.png)

1. **[ユーザーとグループ]** ダイアログの [ユーザー] の一覧から **[B.Simon]** を選択し、画面の下部にある **[選択]** ボタンをクリックします。
1. SAML アサーション内に任意のロール値が必要な場合、 **[ロールの選択]** ダイアログでユーザーに適したロールを一覧から選択し、画面の下部にある **[選択]** をクリックします。
1. **[割り当ての追加]** ダイアログで、 **[割り当て]** をクリックします。

## <a name="configure-comm100-live-chat-sso"></a>Comm100 Live Chat の SSO の構成

1. 別の Web ブラウザー ウィンドウで、Comm100 Live Chat にセキュリティ管理者としてサインインします。

1. ページの右上の **[My Account]\(マイ アカウント\)** をクリックします。

   ![Comm100 Live Chat のマイ アカウント](./media/comm100livechat-tutorial/tutorial_comm100livechat_account.png)

1. メニューの左側にある **[セキュリティ]** をクリックし、 **[Agent Single Sign-On]\(エージェント シングル サインオン\)** をクリックします。

   ![[セキュリティ] と [Agent Single Sign-On]\(エージェント シングル サインオン\) が強調表示された左側のアカウント メニューを示すスクリーンショット。](./media/comm100livechat-tutorial/tutorial_comm100livechat_security.png)

1. **[Agent Single Sign-On]\(エージェント シングル サインオン\)** ページで、次の手順を実行します。

   ![Comm100 Live Chat のセキュリティ](./media/comm100livechat-tutorial/tutorial_comm100livechat_singlesignon.png)

   a. 強調表示されている最初のリンクをコピーして、Azure portal の **[基本的な SAML 構成]** セクションの **[サインオン URL]** ボックスに貼り付けます。

   b. **[SAML SSO URL]** ボックスに、Azure portal からコピーした **ログイン URL** の値を貼り付けます。

   c. **[リモート ログアウト URL]** ボックスに、Azure portal からコピーした **ログアウト URL** の値を貼り付けます。

   d. **[Choose a File]\(ファイルの選択\)** を選択して、Azure Portal からダウンロードした base 64 でエンコードされた証明書を **[Certificate]\(証明書\)** にアップロードします。

   e. **[変更を保存]** をクリックします。

### <a name="create-comm100-live-chat-test-user"></a>Comm100 Live Chat テスト ユーザーの作成

Azure AD ユーザーが Comm100 Live Chat にサインインできるようにするには、ユーザーを Comm100 Live Chat にプロビジョニングする必要があります。 Comm100 Live Chat では、プロビジョニングは手動で行います。

**ユーザー アカウントをプロビジョニングするには、次の手順に従います。**

1. Comm100 Live Chat にセキュリティ管理者としてサインインします。

2. ページの右上の **[My Account]\(マイ アカウント\)** をクリックします。

    ![Comm100 Live Chat のマイ アカウント](./media/comm100livechat-tutorial/tutorial_comm100livechat_account.png)

3. メニューの左側にある **[Agents]\(エージェント\)** をクリックし、 **[New Agent]\(新規エージェント\)** をクリックします。

    ![Comm100 Live Chat のエージェント](./media/comm100livechat-tutorial/tutorial_comm100livechat_agent.png)

4. **[New Agent]\(新規エージェント\)** ページで、次の手順を実行します。

    ![Comm100 Live Chat の新規エージェント](./media/comm100livechat-tutorial/tutorial_comm100livechat_newagent.png)

    a. a. **[Email]\(メール\)** ボックスに、ユーザーのメール アドレス (例: **B.simon\@contoso.com**) を入力します。

    b. **[名]** ボックスに、ユーザーの名を入力します (例: **B**)。

    c. **[Last Name]\(姓\)** ボックスに、ユーザーの姓を入力します (例: **simon**)。

    d. **[Display Name]\(表示名\)** ボックスに、ユーザーの表示名を入力します (例: **B.simon**)

    e. **[Password]\(パスワード\)** ボックスに、ユーザーのパスワードを入力します。

    f. **[保存]** をクリックします。

## <a name="test-sso"></a>SSO のテスト 

このセクションでは、アクセス パネルを使用して Azure AD のシングル サインオン構成をテストします。

アクセス パネル上で [Comm100 Live Chat] タイルをクリックすると、SSO を設定した Comm100 Live Chat に自動的にサインインします。 アクセス パネルの詳細については、[アクセス パネルの概要](../user-help/my-apps-portal-end-user-access.md)に関する記事を参照してください。

## <a name="additional-resources"></a>その他のリソース

- [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](./tutorial-list.md)

- [Azure Active Directory でのアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)

- [Azure Active Directory の条件付きアクセスとは](../conditional-access/overview.md)

- [Azure AD で Comm100 Live Chat を試す](https://aad.portal.azure.com/)