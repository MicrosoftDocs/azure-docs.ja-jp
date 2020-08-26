---
title: 'チュートリアル: Azure Active Directory と FreshDesk の統合 | Microsoft Docs'
description: Azure Active Directory と FreshDesk の間でシングル サインオンを構成する方法について説明します。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 07/16/2020
ms.author: jeedes
ms.openlocfilehash: 71c1bea58f17b457f417a5b050640d04d44019d5
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/18/2020
ms.locfileid: "88551061"
---
# <a name="tutorial-azure-active-directory-integration-with-freshdesk"></a>チュートリアル: Azure Active Directory と FreshDesk の統合

このチュートリアルでは、FreshDesk と Azure Active Directory (Azure AD) を統合する方法について説明します。
FreshDesk と Azure AD の統合には、次の利点があります。

* FreshDesk にアクセスする Azure AD ユーザーを制御できます。
* ユーザーが自分の Azure AD アカウントで FreshDesk に自動的にサインイン (シングル サインオン) するように設定できます。
* 1 つの中央サイト (Azure Portal) でアカウントを管理できます。

SaaS アプリと Azure AD の統合の詳細については、「 [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)」を参照してください。
Azure サブスクリプションをお持ちでない場合は、開始する前に[無料アカウントを作成](https://azure.microsoft.com/free/)してください。

## <a name="prerequisites"></a>前提条件

FreshDesk と Azure AD の統合を構成するには、次のものが必要です。

* Azure AD サブスクリプション。 Azure AD の環境がない場合は、[こちら](https://azure.microsoft.com/pricing/free-trial/)から 1 か月の評価版を入手できます
* FreshDesk でのシングル サインオンが有効なサブスクリプション

## <a name="scenario-description"></a>シナリオの説明

このチュートリアルでは、テスト環境で Azure AD のシングル サインオンを構成してテストします。

* FreshDesk では、**SP** によって開始される SSO がサポートされます
* FreshDesk を構成したら、組織の機密データを流出と侵入からリアルタイムで保護するセッション制御を適用することができます。 セッション制御は、条件付きアクセスを拡張したものです。 [Microsoft Cloud App Security でセッション制御を強制する方法](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app)をご覧ください。

## <a name="adding-freshdesk-from-the-gallery"></a>ギャラリーからの FreshDesk の追加

Azure AD への FreshDesk の統合を構成するには、ギャラリーから管理対象 SaaS アプリの一覧に FreshDesk を追加する必要があります。

1. 職場または学校アカウントか、個人の Microsoft アカウントを使用して、[Azure portal](https://portal.azure.com) にサインインします。
1. 左のナビゲーション ウィンドウで **[Azure Active Directory]** サービスを選択します。
1. **[エンタープライズ アプリケーション]** に移動し、 **[すべてのアプリケーション]** を選択します。
1. 新しいアプリケーションを追加するには、 **[新しいアプリケーション]** を選択します。
1. **[ギャラリーから追加する]** セクションで、検索ボックスに「**FreshDesk**」と入力します。
1. 結果のパネルから **FreshDesk** を選択し、アプリを追加します。 お使いのテナントにアプリが追加されるのを数秒待機します。

## <a name="configure-and-test-azure-ad-single-sign-on-for-freshdesk"></a>FreshDesk の Azure AD シングル サインオンの構成とテスト

**B.Simon** というテスト ユーザーを使用して、FreshDesk に対する Azure AD SSO を構成してテストします。 SSO が機能するためには、Azure AD ユーザーと FreshDesk の関連ユーザーとの間にリンク関係を確立する必要があります。

FreshDesk に対する Azure AD SSO を構成してテストするには、次の構成要素を完了します。

1. **[Azure AD シングル サインオンの構成](#configure-azure-ad-single-sign-on)** - ユーザーがこの機能を使用できるようにします。
    1. **[Azure AD のテスト ユーザーの作成](#create-an-azure-ad-test-user)** - Britta Simon で Azure AD のシングル サインオンをテストします。
    1. **[Azure AD テスト ユーザーの割り当て](#assign-the-azure-ad-test-user)** - Britta Simon が Azure AD シングル サインオンを使用できるようにします。
1. **[FreshDesk のシングル サインオンの構成](#configure-freshdesk-single-sign-on)** - アプリケーション側でシングル サインオン設定を構成します。
    1. **[FreshDesk テスト ユーザーの作成](#create-freshdesk-test-user)** - FreshDesk で Britta Simon に対応するユーザーを作成し、Azure AD の Britta Simon にリンクさせます。
1. **[シングル サインオンのテスト](#test-single-sign-on)** - 構成が機能するかどうかを確認します。

## <a name="configure-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成

1. [Azure portal](https://portal.azure.com/) の **FreshDesk** アプリケーション統合ページで、 **[管理]** セクションを探して、 **[シングル サインオン]** を選択します。
1. **[シングル サインオン方式の選択]** ページで、 **[SAML]** を選択します。
1. **[SAML でシングル サインオンをセットアップします]** ページで、 **[基本的な SAML 構成]** の編集 (ペン) アイコンをクリックして設定を編集します。

   ![基本的な SAML 構成を編集する](common/edit-urls.png)

1. **[基本的な SAML 構成]** セクションで、次のフィールドの値を入力します。

    a. **[サインオン URL]** ボックスに、`https://<tenant-name>.freshdesk.com` のパターン、または FreshDesk から示されたその他の値を使用して URL を入力します。

    b. **[Identifier (Entity ID)]\(ID (エンティティ ID)\)** ボックスに、`https://<tenant-name>.freshdesk.com` のパターン、または FreshDesk から示されたその他の値を使用して URL を入力します。

    > [!NOTE]
    > これらは実際の値ではありません。 実際のサインオン URL と識別子でこれらの値を更新します。 これらの値を取得するには、[FreshDesk サポート チーム](https://freshdesk.com/helpdesk-software?utm_source=Google-AdWords&utm_medium=Search-IND-Brand&utm_campaign=Search-IND-Brand&utm_term=freshdesk&device=c&gclid=COSH2_LH7NICFVUDvAodBPgBZg)に問い合わせてください。 Azure portal の **[基本的な SAML 構成]** セクションに示されているパターンを参照することもできます。

1. FreshDesk アプリケーションは、特定の形式の SAML アサーションを使用するため、カスタム属性のマッピングを SAML トークンの属性の構成に追加する必要があります。 次のスクリーンショットは、既定の属性の一覧を示したものです。 **[一意のユーザー ID]** は **user.userprincipalname** にマップされていますが、FreshDesk ではこの要求は **user.mail** にマップされている必要があるため、[編集] アイコンをクリックして属性マッピングを編集し、属性マッピングを変更する必要があります。

    ![image](common/edit-attribute.png)

1. **[SAML でシングル サインオンをセットアップします]** ページの **[SAML 署名証明書]** セクションで **[ダウンロード]** をクリックして、要件に適した特定のオプションの**証明書 (Base64)** をダウンロードし、コンピューターに保存します。

    ![証明書のダウンロードのリンク](common/certificatebase64.png)

1. **[FreshDesk の設定]** セクションで、要件に従って適切な URL をコピーします。

    ![構成 URL のコピー](common/copy-configuration-urls.png)

    a. ログイン URL

    b. Azure AD 識別子

    c. ログアウト URL

### <a name="create-an-azure-ad-test-user"></a>Azure AD のテスト ユーザーの作成 

このセクションの目的は、Azure Portal で Britta Simon というテスト ユーザーを作成することです。

1. Azure portal の左側のウィンドウで、 **[Azure Active Directory]** 、 **[ユーザー]** 、 **[すべてのユーザー]** の順に選択します。

    ![[ユーザーとグループ] と [すべてのユーザー] リンク](common/users.png)

2. 画面の上部にある **[新しいユーザー]** を選択します。

    ![[新しいユーザー] ボタン](common/new-user.png)

3. [ユーザーのプロパティ] で、次の手順を実行します。

    ![[ユーザー] ダイアログ ボックス](common/user-properties.png)

    a. **[名前]** フィールドに「**BrittaSimon**」と入力します。
  
    b. **[User name]\(ユーザー名\)** フィールドに「**brittasimon\@yourcompanydomain.extension**」と入力します。  
    たとえば、BrittaSimon@contoso.com のように指定します。

    c. **[パスワードを表示]** チェック ボックスをオンにし、[パスワード] ボックスに表示された値を書き留めます。

    d. **Create** をクリックしてください。


### <a name="assign-the-azure-ad-test-user"></a>Azure AD テスト ユーザーの割り当て

このセクションでは、Britta Simon に FreshDesk へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

1. Azure portal 上で **[エンタープライズ アプリケーション]** を選択し、 **[すべてのアプリケーション]** を選択してから、 **[FreshDesk]** を選択します。

    ![[エンタープライズ アプリケーション] ブレード](common/enterprise-applications.png)

2. アプリケーションの一覧で、「**FreshDesk**」と入力して選択します。

    ![アプリケーションの一覧の FreshDesk リンク](common/all-applications.png)

3. 左側のメニューで **[ユーザーとグループ]** を選びます。

    ![[ユーザーとグループ] リンク](common/users-groups-blade.png)

4. **[ユーザーの追加]** をクリックし、 **[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。

    ![[割り当ての追加] ウィンドウ](common/add-assign-user.png)

5. **[ユーザーとグループ]** ダイアログの [ユーザー] の一覧で **[Britta Simon]** を選択し、画面の下部にある **[選択]** ボタンをクリックします。

6. SAML アサーション内に任意のロール値が必要な場合、 **[ロールの選択]** ダイアログでユーザーに適したロールを一覧から選択し、画面の下部にある **[選択]** をクリッします。

7. **[割り当ての追加]** ダイアログで、 **[割り当て]** をクリックします。

## <a name="configure-freshdesk-single-sign-on"></a>FreshDesk のシングル サインオンの構成

1. 別の Web ブラウザー ウィンドウで、Freshdesk 企業サイトに管理者としてログインします。

2. **[Security]\(セキュリティ\)** アイコンを選択し、 **[Security]\(セキュリティ\)** セクションで次の手順のようにします。

    ![シングル サインオン](./media/freshdesk-tutorial/configure-1.png "シングル サインオン")
  
    a. **[Single Sign On]\(シングル サインオン\)** で **[On]\(オン\)** を選択します。

    b. **[Login Method]\(ログイン方法\)** で、 **[SAML SSO]** を選択します。

    c. **[Entity ID provided by the IdP]\(IdP によって提供されたエンティティ ID\)** テキスト ボックスに、Azure portal からコピーした**エンティティ ID** の値を貼り付けます。

    d. **[SAML SSO URL]** テキストボックスに、Azure portal からコピーした**ログイン URL** の値を貼り付けます。

    e. **[Signing Options]\(署名オプション\)** で、ドロップダウンから **[Only Signed Assertions]\(署名されたアサーションのみ\)** を選択します。

    f. **[ログアウト URL]** テキストボックスに、Azure portal からコピーした**ログアウト URL** の値を貼り付けます。

    g. **[Security Certificate]\(セキュリティ証明書\)** テキスト ボックスに、前に取得した**証明書 (Base64)** の値を貼り付けます。
  
    h. **[保存]** をクリックします。

## <a name="create-freshdesk-test-user"></a>FreshDesk のテスト ユーザーの作成

Azure AD ユーザーが FreshDesk にログインできるようにするには、そのユーザーを FreshDesk にプロビジョニングする必要があります。  
FreshDesk の場合、プロビジョニングは手動で行います。

**ユーザー アカウントをプロビジョニングするには、次の手順に従います。**

1. **Freshdesk** テナントにログインします。

1. 左側のメニューで **[Admin]\(管理者\)** をクリックし、 **[General Settings]\(全般設定\)** タブで **[Agents]\(エージェント\)** をクリックします。
  
    ![[エージェント]](./media/freshdesk-tutorial/create-user-1.png "エージェント")

1. **[新しいエージェント]** をクリックします。

    ![新しいエージェント](./media/freshdesk-tutorial/create-user-2.png "[新しいエージェント]")

1. [Agent Information]\(エージェント情報\) ダイアログで、必要なフィールドを入力し、 **[Create agent]\(エージェントの作成\)** をクリックします。

    ![エージェント情報](./media/freshdesk-tutorial/create-user-3.png "エージェント情報")

    >[!NOTE]
    >Azure AD のアカウント所有者には、そのアカウントがアクティブになる前に、アカウント確認用のリンクを含む電子メールが送信されます。
    >
    >[!NOTE]
    >その他の Freshdesk ユーザー アカウント作成ツールや、Freshdesk から提供されている API を使用して、Azure AD ユーザー アカウントを Freshdesk にプロビジョニングできます。

### <a name="test-single-sign-on"></a>シングル サインオンのテスト 

このセクションでは、アクセス パネルを使用して Azure AD のシングル サインオン構成をテストします。

アクセス パネル上で [FreshDesk] タイルをクリックすると、SSO を設定した FreshDesk に自動的にサインインします。 アクセス パネルの詳細については、[アクセス パネルの概要](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)に関する記事を参照してください。

## <a name="additional-resources"></a>その他のリソース

- [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Azure Active Directory の条件付きアクセスとは](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

