---
title: チュートリアル:Azure Active Directory と GitHub の統合 | Microsoft Docs
description: Azure Active Directory と GitHub の間でシングル サインオンを構成する方法について説明します。
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: 8761f5ca-c57c-4a7e-bf14-ac0421bd3b5e
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 01/31/2020
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: e812e1b03637a3ecd7a45f02664c4e3547f1aef1
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/24/2020
ms.locfileid: "79138979"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-github"></a>チュートリアル:Azure Active Directory シングル サインオン (SSO) と GitHub の統合

このチュートリアルでは、GitHub と Azure Active Directory (Azure AD) を統合する方法について説明します。 GitHub を Azure AD に統合すると、次のことができます。

* GitHub Enterprise Cloud Organization にアクセスできるユーザーを Azure AD で制御する。
* GitHub Enterprise Cloud Organization へのアクセスを 1 つの中央サイト (Azure portal) で管理する。

SaaS アプリと Azure AD の統合の詳細については、「[Azure Active Directory でのアプリケーションへのシングル サインオン](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)」を参照してください。

## <a name="prerequisites"></a>前提条件

GitHub と Azure AD の統合を構成するには、次のものが必要です。

* Azure AD サブスクリプション。 Azure AD の環境がない場合は、[こちら](https://azure.microsoft.com/pricing/free-trial/)から 1 か月の評価版を入手できます
* [GitHub Enterprise Cloud](https://help.github.com/articles/github-s-products/#github-enterprise) に作成された GitHub 組織 ([GitHub Enterprise 課金プラン](https://help.github.com/articles/github-s-billing-plans/#billing-plans-for-organizations)が必要)

## <a name="scenario-description"></a>シナリオの説明

このチュートリアルでは、テスト環境で Azure AD のシングル サインオンを構成してテストします。

* GitHub では、**SP** によって開始される SSO がサポートされます

* GitHub では、[**自動化された**ユーザー プロビジョニング (組織の招待)](github-provisioning-tutorial.md) がサポートされます
* GitHub を構成したら、組織の機密データを流出と侵入からリアルタイムで保護するセッション制御を適用することができます。 セッション制御は、条件付きアクセスを拡張したものです。 [Microsoft Cloud App Security でセッション制御を適用する方法](https://docs.microsoft.com/cloud-app-security/proxy-deployment-aad)をご覧ください。

## <a name="adding-github-from-the-gallery"></a>ギャラリーからの GitHub の追加

Azure AD への GitHub の統合を構成するには、ギャラリーから管理対象 SaaS アプリの一覧に GitHub を追加する必要があります。

1. 職場または学校アカウントか、個人の Microsoft アカウントを使用して、[Azure portal](https://portal.azure.com) にサインインします。
1. 左のナビゲーション ウィンドウで **[Azure Active Directory]** サービスを選択します。
1. **[エンタープライズ アプリケーション]** に移動し、 **[すべてのアプリケーション]** を選択します。
1. 新しいアプリケーションを追加するには、 **[新しいアプリケーション]** を選択します。
1. **[ギャラリーから追加する]** セクションで、検索ボックスに「**GitHub**」と入力します。
1. 結果のパネルから **[GitHub]** を選択し、アプリを追加します。 お使いのテナントにアプリが追加されるのを数秒待機します。


## <a name="configure-and-test-azure-ad-single-sign-on-for-github"></a>GitHub の Azure AD シングル サインオンの構成とテスト

**B.Simon** というテスト ユーザーを使用して、GitHub に対する Azure AD SSO を構成してテストします。 SSO を機能させるために、Azure AD ユーザーと GitHub の関連ユーザーとの間にリンク関係を確立する必要があります。

GitHub で Azure AD SSO を構成してテストするには、次の構成要素を完了します。

1. **[Azure AD SSO の構成](#configure-azure-ad-sso)** - ユーザーがこの機能を使用できるようにします。
    * **[Azure AD のテスト ユーザーの作成](#create-an-azure-ad-test-user)** - B.Simon で Azure AD のシングル サインオンをテストします。
    * **[Azure AD テスト ユーザーの割り当て](#assign-the-azure-ad-test-user)** - B.Simon が Azure AD シングル サインオンを使用できるようにします。
1. **[GitHub の SSO の構成](#configure-github-sso)** - アプリケーション側でシングル サインオン設定を構成します。
    * **[GitHub テスト ユーザーの作成](#create-github-test-user)** - GitHub で B.Simon に対応するユーザーを作成し、Azure AD の B.Simon にリンクさせます。
1. **[SSO のテスト](#test-sso)** - 構成が機能するかどうかを確認します。

## <a name="configure-azure-ad-sso"></a>Azure AD SSO の構成

これらの手順に従って、Azure portal で Azure AD SSO を有効にします。

1. [Azure portal](https://portal.azure.com/) の **GitHub** アプリケーション統合ページで、 **[管理]** セクションを見つけて、 **[シングル サインオン]** を選択します。
1. **[シングル サインオン方式の選択]** ページで、 **[SAML]** を選択します。
1. **[SAML でシングル サインオンをセットアップします]** ページで、 **[基本的な SAML 構成]** の編集 (ペン) アイコンをクリックして設定を編集します。

   ![基本的な SAML 構成を編集する](common/edit-urls.png)

1. **[基本的な SAML 構成]** セクションで、次のフィールドの値を入力します。

   a. **[サインオン URL]** ボックスに、次のパターンを使用して URL を入力します。`https://github.com/orgs/<entity-id>/sso`

    b. **[識別子 (エンティティ ID)]** ボックスに、次のパターンを使用して URL を入力します。`https://github.com/orgs/<entity-id>`

    > [!NOTE]
    > これは実際の値ではないので注意してください。 実際のサインオン URL と識別子で、これらの値を更新する必要があります。 ここでは、識別子に一意の文字列値を使用することをお勧めします。 これらの値を取得するには、GitHub 管理者セクションに移動します。

5. GitHub アプリケーションでは、特定の形式の SAML アサーションを使用するため、カスタム属性マッピングを SAML トークン属性の構成に追加する必要があります。 次のスクリーンショットは、既定の属性の一覧を示しています。ここで、**nameidentifier** は **user.userprincipalname** にマップされています。 GitHub アプリケーションでは、**nameidentifier** が **user.mail** にマップされると想定されているため、 **[編集]** アイコンをクリックして属性マッピングを編集し、属性マッピングを変更する必要があります。

    ![image](common/edit-attribute.png)

6. **[SAML でシングル サインオンをセットアップします]** ページの **[SAML 署名証明書]** セクションで、 **[ダウンロード]** をクリックして要件のとおりに指定したオプションからの**証明書 (Base64)** をダウンロードして、お使いのコンピューターに保存します。

    ![証明書のダウンロードのリンク](common/certificatebase64.png)

7. **[GitHub のセットアップ]** セクションで、要件どおりの適切な URL をコピーします。

    ![構成 URL のコピー](common/copy-configuration-urls.png)

    a. ログイン URL

    b. Azure AD 識別子

    c. ログアウト URL

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

このセクションでは、B.Simon に GitHub へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

1. Azure portal で **[エンタープライズ アプリケーション]** を選択し、 **[すべてのアプリケーション]** を選択します。
1. アプリケーションの一覧で **GitHub** を選択します。
1. アプリの概要ページで、 **[管理]** セクションを見つけて、 **[ユーザーとグループ]** を選択します。

   ![[ユーザーとグループ] リンク](common/users-groups-blade.png)

1. **[ユーザーの追加]** を選択し、 **[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。

    ![[ユーザーの追加] リンク](common/add-assign-user.png)

1. **[ユーザーとグループ]** ダイアログの [ユーザー] の一覧から **[B.Simon]** を選択し、画面の下部にある **[選択]** ボタンをクリックします。
1. SAML アサーション内に任意のロール値が必要な場合、 **[ロールの選択]** ダイアログでユーザーに適したロールを一覧から選択し、画面の下部にある **[選択]** をクリックします。
1. **[割り当ての追加]** ダイアログで、 **[割り当て]** をクリックします。

## <a name="configure-github-sso"></a>GitHub の SSO の構成

1. 別の Web ブラウザー ウィンドウで、GitHub 組織サイトに管理者としてログインします。

2. **[設定]** に移動し、 **[セキュリティ]** をクリックします。

    ![設定](./media/github-tutorial/tutorial_github_config_github_03.png)

3. **[Enable SAML authentication (SAML 認証を有効にする)]** チェック ボックスをオンにして、シングル サインオンの構成フィールドを表示します。 次に、シングル サインオン URL 値を使用して、Azure AD 構成でシングル サインオン URL を更新します。

    ![設定](./media/github-tutorial/tutorial_github_config_github_13.png)

4. 次のフィールドを構成します。

    ![設定](./media/github-tutorial/tutorial_github_config_github_051.png)

    a. **[シングル サインオン URL]** テキストボックスに、Azure portal からコピーした**ログイン URL** の値を貼り付けます。

    b. **[発行者]** テキストボックスに、Azure portal からコピーした、**Azure AD ID** の値を貼り付けます。

    c. Azure Portal からダウンロードした証明書 をメモ帳で開き、その内容を **[Public Certificate]\(公開証明書\)** ボックスに貼り付けます。

    d. **[Edit]\(編集\)** アイコンをクリックし、 **[Signature Method]\(署名方法\)** と **[Digest Method]\(ダイジェスト方法\)** を編集して、**RSA-SHA1** および **SHA1** から **RSA-SHA256** および **SHA256** に変更します (下図参照)。
    
    e. GitHub の URL が Azure アプリ登録の URL と一致するように、**Assertion Consumer Service URL** (応答 URL) を既定の URL から更新します。

    ![image](./media/github-tutorial/tutorial_github_sha.png)

5. **[Test SAML configuration (SAML 構成のテスト)]** をクリックして、SSO の際に検証の失敗やエラーがないことを確認します。

    ![設定](./media/github-tutorial/tutorial_github_config_github_06.png)

6. **[保存]**

> [!NOTE]
> GitHub でのシングル サインオンは GitHub で特定の組織を認証するものです。GitHub そのものの認証に取って代わることはできません。 つまり、ユーザーの github.com セッションの有効期限が切れた場合は、シングル サインオン プロセス中に GitHub の ID とパスワードで認証するように求められることがあります。

### <a name="create-github-test-user"></a>GitHub テスト ユーザーの作成

このセクションの目的は、GitHub で Britta Simon というユーザーを作成することです。 GitHub では、自動ユーザー プロビジョニングがサポートされています。この設定は、既定で有効になっています。 自動ユーザー プロビジョニングの構成方法について詳しくは、[こちら](github-provisioning-tutorial.md)をご覧ください。

**ユーザーを手動で作成する必要がある場合は、次の手順を実行します:**

1. GitHub 企業サイトに管理者としてログインします。

2. **[ユーザー]** をクリックします。

    ![ユーザー](./media/github-tutorial/tutorial_github_config_github_08.png "ユーザー")

3. **[メンバーの招待]** をクリックします。

    ![ユーザーの招待](./media/github-tutorial/tutorial_github_config_github_09.png "ユーザーの招待")

4. **[メンバーの招待]** ダイアログ ページで、次の手順を実行します。

    a. **[Email (電子メール)]** ボックスに、Britta Simon アカウントの電子メール アドレスを入力します。

    ![ユーザーの招待](./media/github-tutorial/tutorial_github_config_github_10.png "[ユーザーの招待]")

    b. **[Send Invitation (招待状の送信)]** をクリックします。

    ![ユーザーの招待](./media/github-tutorial/tutorial_github_config_github_11.png "[ユーザーの招待]")

    > [!NOTE]
    > Azure Active Directory アカウント所有者がメールを受信し、リンクに従ってアカウントを確認するとそのアカウントがアクティブになります。

## <a name="test-sso"></a>SSO のテスト 

このセクションでは、アクセス パネルを使用して Azure AD のシングル サインオン構成をテストします。

アクセス パネル上で [GitHub] タイルをクリックすると、SSO を設定した GitHub に自動的にサインインします。 アクセス パネルの詳細については、[アクセス パネルの概要](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)に関する記事を参照してください。

## <a name="additional-resources"></a>その他のリソース

- [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Azure Active Directory でのアプリケーション アクセスとシングル サインオンとは](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Azure Active Directory の条件付きアクセスとは](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Azure AD で GitHub を試す](https://aad.portal.azure.com/)

- [Microsoft Cloud App Security におけるセッション制御とは](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)
