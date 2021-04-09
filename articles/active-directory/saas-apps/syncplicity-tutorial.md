---
title: 'チュートリアル: Azure Active Directory と Syncplicity の統合 | Microsoft Docs'
description: Azure Active Directory と Syncplicity の間でシングル サインオンを構成する方法について説明します。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 06/10/2019
ms.author: jeedes
ms.openlocfilehash: 3c665795325ed3863583eb0f21f3e0d3f534154a
ms.sourcegitcommit: 5f32f03eeb892bf0d023b23bd709e642d1812696
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/12/2021
ms.locfileid: "103201495"
---
# <a name="tutorial-integrate-syncplicity-with-azure-active-directory"></a>チュートリアル: Azure Active Directory と Syncplicity の統合

このチュートリアルでは、Syncplicity と Azure Active Directory (Azure AD) を統合する方法について説明します。 Azure AD と Syncplicity を統合すると、次のことができます。

* Syncplicity にアクセスする Azure AD ユーザーを制御する。
* ユーザーが自分の Azure AD アカウントを使用して Syncplicity に自動的にサインインできるようにする。
* 1 つの中央サイト (Azure Portal) で自分のアカウントを管理します。

SaaS アプリと Azure AD の統合の詳細については、「[Azure Active Directory でのアプリケーションへのシングル サインオン](../manage-apps/what-is-single-sign-on.md)」を参照してください。

## <a name="prerequisites"></a>前提条件

開始するには、次が必要です。

* Azure AD サブスクリプション。 サブスクリプションをお持ちでない場合は、[こちら](https://azure.microsoft.com/free/)から 12 か月間の無料試用版を入手できます。
* Syncplicity でのシングル サインオン (SSO) が有効なサブスクリプション

## <a name="scenario-description"></a>シナリオの説明

このチュートリアルでは、テスト環境で Azure AD の SSO を構成してテストします。 Syncplicity では、**SP** によって開始される SSO がサポートされます。

## <a name="adding-syncplicity-from-the-gallery"></a>ギャラリーからの Syncplicity の追加

Azure AD への Syncplicity の統合を構成するには、ギャラリーから管理対象 SaaS アプリの一覧に Syncplicity を追加する必要があります。

1. 職場または学校アカウントか、個人の Microsoft アカウントを使用して、[Azure portal](https://portal.azure.com) にサインインします。
1. 左のナビゲーション ウィンドウで **[Azure Active Directory]** サービスを選択します。
1. **[作成]** の下にある **[エンタープライズ アプリケーション]** をクリックします。
1. **[Azure AD ギャラリーの参照]** セクションで、検索ボックスに、「**Syncplicity**」と入力します。
1. 結果のパネルから **[Syncplicity]** を選択し、 **[作成]** をクリックしてアプリを追加します。 お使いのテナントにアプリが追加されるのを数秒待機します。

## <a name="configure-and-test-azure-ad-sso"></a>Azure AD SSO の構成とテスト

**B.Simon** というテスト ユーザーを使用して、Syncplicity に対する Azure AD SSO を構成してテストします。 SSO が機能するために、Azure AD ユーザーと Syncplicity の関連ユーザーの間で、リンク関係が確立されている必要があります。

Syncplicity で Azure AD SSO を構成してテストするには、次の構成要素を完了する必要があります。

1. **[Azure AD SSO の構成](#configure-azure-ad-sso)** - ユーザーがこの機能を使用できるようにします。
2. **[Syncplicity の SSO の構成](#configure-syncplicity-sso)** - アプリケーション側でシングル サインオン設定を構成します。
3. **[Azure AD のテスト ユーザーの作成](#create-an-azure-ad-test-user)** - B.Simon で Azure AD のシングル サインオンをテストします。
4. **[Azure AD テスト ユーザーの割り当て](#assign-the-azure-ad-test-user)** - B.Simon が Azure AD シングル サインオンを使用できるようにします。
5. **[Syncplicity テスト ユーザーの作成](#create-syncplicity-test-user)** - Syncplicity で B.Simon に対応するユーザーを作成し、Azure AD のこのユーザーにリンクさせます。
6. **[SSO のテスト](#test-sso)** - 構成が機能するかどうかを確認します。
7. **[SSO の更新](#update-sso)** - Azure AD で SSO 設定を変更した場合は、Syncplicity で必要な変更を行います。

### <a name="configure-azure-ad-sso"></a>Azure AD SSO の構成

これらの手順に従って、Azure portal で Azure AD SSO を有効にします。

1. [Azure portal](https://portal.azure.com/) の **Syncplicity** アプリケーション統合ページで **[Getting Started]\(作業の開始\)** セクションを見つけて、 **[Set up single sign-on]\(シングル サインオンの設定\)** を選択します。
2. **[シングル サインオン方式の選択]** ページで、 **[SAML]** を選択します。
3. **[SAML でシングル サインオンをセットアップします]** ページで、 **[基本的な SAML 構成]** の編集/ペン アイコンをクリックして設定を編集します。

   ![基本的な SAML 構成を編集する](common/edit-urls.png)

4. **[基本的な SAML 構成]** セクションで、次のフィールドの値を入力します。

    a. **[識別子 (エンティティ ID)]** ボックスに、次のパターンを使用して URL を入力します。`https://<companyname>.syncplicity.com/sp`

    b. **[サインオン URL]** ボックスに、次のパターンを使用して URL を入力します。`https://<companyname>.syncplicity.com`
    
    c. **[応答 URL (Assertion Consumer Service URL)]** テキスト ボックスに、`https://<companyname>.syncplicity.com/Auth/AssertionConsumerService.aspx` というパターンを使用して URL を入力します。

    > [!NOTE]
    > これらは実際の値ではありません。 実際のサインオン URL と識別子でこれらの値を更新します。 これらの値を取得するには、[Syncplicity クライアント サポート チーム](https://www.syncplicity.com/contact-us)に問い合わせてください。 Azure portal の **[基本的な SAML 構成]** セクションに示されているパターンを参照することもできます。

5. **[SAML によるシングル サインオンのセットアップ]** ページの **[SAML 署名証明書]** セクションで、 **[編集]** をクリックします。 次に、ダイアログで、アクティブな証明書の横にある省略記号ボタンをクリックし、 **[PEM 証明書のダウンロード]** を選択します。

   ![証明書のダウンロードのリンク](common/certificatebase64.png)

    > [!NOTE]
    > Syncplicity では CER 形式の証明書は受け入れられないため、PEM 証明書が必要です。

6. **[Syncplicity の設定]** セクションで、要件に基づいて適切な URL をコピーします。

   ![構成 URL のコピー](common/copy-configuration-urls.png)

### <a name="configure-syncplicity-sso"></a>Syncplicity の SSO の構成

1. **Syncplicity** テナントにサインインします。

1. 上部のメニューで **[Admin]** をクリックし、 **[Settings]** を選択し、 **[Custom domain and single sign-on]** をクリックします。

    ![Syncplicity](./media/syncplicity-tutorial/ic769545.png "Syncplicity")

1. [**Single Sign-On (SSO)**] ページで、以下の手順を実行します。

    ![Single Sign-On \(SSO\)](./media/syncplicity-tutorial/ic769550.png "Single Sign-On \\\(SSO\\\)")

    a. [**Custom Domain**] ボックスに、ドメインの名前を入力します。
  
    b. [**Single Sign-On Status**] で [**Enabled**] を選択します。

    c. Azure portal の **[基本的な SAML 構成]** セクションで使用した **[識別子 (エンティティ ID)]** 値を **[Entity Id]\(エンティティ ID\)** テキストボックスに貼り付けます。

    d. **[サインイン ページの URL]** ボックスに、Azure portal からコピーした **サインオン URL** を貼り付けます。

    e. **[Logout page URL]\(ログアウト ページの URL\)** ボックスに、Azure portal からコピーした **ログアウト URL** を貼り付けます。

    f. **[Identity Provider Certificate]\(ID プロバイダー証明書\)** で **[Choose file]\(ファイルの選択\)** をクリックし、Azure Portal からダウンロードした証明書をアップロードします。

    g. **[SAVE CHANGES]\(変更の保存\)** をクリックします。

### <a name="create-an-azure-ad-test-user"></a>Azure AD のテスト ユーザーの作成

このセクションでは、Azure portal 内で B.Simon というテスト ユーザーを作成します。

1. Azure portal の左側のウィンドウから、 **[Azure Active Directory]** 、 **[ユーザー]** 、 **[すべてのユーザー]** の順に選択します。
2. 画面の上部にある **[新しいユーザー]** を選択します。
3. **[ユーザー]** プロパティで、以下の手順を実行します。

   a. **[ユーザー名]** フィールドに「username@companydomain.extension」と入力します。 たとえば、「 `B.Simon@contoso.com` 」のように入力します。

   b. **[名前]** フィールドに「`B.Simon`」と入力します。  
   
   c. **[パスワードを表示]** チェック ボックスをオンにし、 **[パスワード]** ボックスに表示された値を書き留めます。
   
   d. **Create** をクリックしてください。

### <a name="assign-the-azure-ad-test-user"></a>Azure AD テスト ユーザーの割り当て

このセクションでは、B.Simon に Syncplicity へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

1. Azure portal で **[エンタープライズ アプリケーション]** を選択し、 **[すべてのアプリケーション]** を選択します。
1. アプリケーションの一覧で **[Syncplicity]** を選択します。
1. アプリの概要ページで、 **[管理]** セクションを見つけて、 **[ユーザーとグループ]** を選択します。

   ![[ユーザーとグループ] リンク](common/users-groups-blade.png)

1. **[Add user/group]\(ユーザーまたはグループの追加\)** を選択します。

    ![[ユーザーの追加] リンク](common/add-assign-user.png)
1. **[割り当ての追加]** ページで **[ユーザー]** を選択します。 
1. **[ユーザー]** ダイアログの [ユーザー] 一覧から **[B.Simon]** を選択し、画面の下部にある **[選択]** ボタンをクリックします。
1. SAML アサーション内に任意のロール値が必要な場合、 **[ロールの選択]** ダイアログでユーザーに適したロールを一覧から選択し、画面の下部にある **[選択]** をクリックします。
1. **[割り当ての追加]** ページで、 **[割り当て]** ボタンをクリックします。

### <a name="create-syncplicity-test-user"></a>Syncplicity テスト ユーザーの作成

Azure AD ユーザーがサインインできるようにするには、ユーザーを Syncplicity アプリケーションにプロビジョニングする必要があります。 このセクションでは、Syncplicity で Azure AD ユーザー アカウントを作成する方法について説明します。

**ユーザー アカウントを Syncplicity にプロビジョニングするには、次の手順に従います。**

1. **Syncplicity** テナント (例: `https://company.Syncplicity.com`) にサインインします。

1. **[Admin]** をクリックし、 **[User Accounts]** を選択して、 **[Add a User]** をクリックします。

    ![[ユーザーを管理する]](./media/syncplicity-tutorial/ic769764.png "[Manage Users]")

1. プロビジョニングする Azure AD アカウントの **メール アドレス** を入力し、 **[Role]** として **[User]** を選択して、 **[Next]** をクリックします。

    ![アカウント情報](./media/syncplicity-tutorial/ic769765.png "アカウント情報")

    > [!NOTE]
    > Azure AD アカウントの所有者にアカウントの確認およびアクティブ化用のリンクを含む電子メールが送信されます。

1. この新しいユーザーを会社内のどのグループのメンバーにするかを選択して [**Next**] をクリックします。

    ![グループ メンバーシップ](./media/syncplicity-tutorial/ic769772.png "グループ メンバーシップ")

    > [!NOTE]
    > 表示されるグループがない場合は、 **[Next]** をクリックします。

1. このユーザーのコンピューター上の、どのフォルダーを Syncplicity の制御下に置くかを選択して [**Next**] をクリックします。

    ![Syncplicity フォルダー](./media/syncplicity-tutorial/ic769773.png "Syncplicity フォルダー")

> [!NOTE]
> 他の Syncplicity ユーザー アカウント作成ツールや、Syncplicity から提供されている API を使用して、Azure AD ユーザー アカウントをプロビジョニングできます。

### <a name="test-sso"></a>SSO のテスト

アクセス パネルで [Syncplicity] タイルを選択すると、SSO を設定した Syncplicity に自動的にサインインします。 アクセス パネルの詳細については、[アクセス パネルの概要](../user-help/my-apps-portal-end-user-access.md)に関する記事を参照してください。

### <a name="update-sso"></a>SSO の更新

SSO に変更を加える必要がある場合は常に、使用されている **SAML 署名証明書** を確認する必要があります。 証明書が変更された場合は、「 **[Syncplicity の SSOの構成](#configure-syncplicity-sso)** 」の説明に従って、新しいものを Syncplicity に必ずアップロードしてください。

Syncplicity のモバイル アプリを使用している場合は、Syncplicity カスタマー サポート (support@syncplicity.com) にお問い合わせください。

## <a name="additional-resources"></a>その他のリソース

- [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](./tutorial-list.md)

- [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)

- [Azure Active Directory の条件付きアクセスとは](../conditional-access/overview.md)
