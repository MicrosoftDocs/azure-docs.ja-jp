---
title: チュートリアル:Azure Active Directory と Egnyte の統合 | Microsoft Docs
description: Azure Active Directory と Egnyte の間でシングル サインオンを構成する方法について説明します。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/31/2020
ms.author: jeedes
ms.openlocfilehash: 512b96b6b785c0694bb41fab657f0a45c4321f10
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/18/2020
ms.locfileid: "88544334"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-egnyte"></a>チュートリアル:Azure Active Directory シングル サインオン (SSO) と Egnyte の統合

このチュートリアルでは、Egnyte と Azure Active Directory (Azure AD) を統合する方法について説明します。 Azure AD と Egnyte を統合すると、次のことができます。

* Egnyte にアクセスできるユーザーを Azure AD で制御できます。
* ユーザーが自分の Azure AD アカウントを使用して Egnyte に自動的にサインインできるように設定できます。
* 1 つの中央サイト (Azure Portal) で自分のアカウントを管理します。

SaaS アプリと Azure AD の統合の詳細については、「[Azure Active Directory でのアプリケーションへのシングル サインオン](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)」を参照してください。

## <a name="prerequisites"></a>前提条件

開始するには、次が必要です。

* Azure AD サブスクリプション。 サブスクリプションがない場合は、[無料アカウント](https://azure.microsoft.com/free/)を取得できます。
* Egnyte でのシングル サインオン (SSO) が有効なサブスクリプション。

## <a name="scenario-description"></a>シナリオの説明

このチュートリアルでは、テスト環境で Azure AD のシングル サインオンを構成してテストします。

* Egnyte では、**SP** Initiated SSO がサポートされます
* Egnyte を構成したら、組織の機密データを流出と侵入からリアルタイムで保護するセッション制御を適用することができます。 セッション制御は、条件付きアクセスを拡張したものです。 [Microsoft Cloud App Security でセッション制御を適用する方法](https://docs.microsoft.com/cloud-app-security/proxy-deployment-aad)をご覧ください。

## <a name="adding-egnyte-from-the-gallery"></a>ギャラリーからの Egnyte の追加

Azure AD への Egnyte の統合を構成するには、ギャラリーから管理対象 SaaS アプリの一覧に Egnyte を追加する必要があります。

1. 職場または学校アカウントか、個人の Microsoft アカウントを使用して、[Azure portal](https://portal.azure.com) にサインインします。
1. 左のナビゲーション ウィンドウで **[Azure Active Directory]** サービスを選択します。
1. **[エンタープライズ アプリケーション]** に移動し、 **[すべてのアプリケーション]** を選択します。
1. 新しいアプリケーションを追加するには、 **[新しいアプリケーション]** を選択します。
1. **[ギャラリーから追加する]** セクションで、検索ボックスに「**Egnyte**」と入力します。
1. 結果のパネルから **[Egnyte]** を選択し、アプリを追加します。 お使いのテナントにアプリが追加されるのを数秒待機します。

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成とテスト

このセクションでは、**Britta Simon** というテスト ユーザーに基づいて、Egnyte で Azure AD のシングル サインオンを構成し、テストします。
シングル サインオンを機能させるには、Azure AD ユーザーと Egnyte 内の関連ユーザー間にリンク関係が確立されている必要があります。

Egnyte で Azure AD のシングル サインオンを構成してテストするには、次の構成要素を完了する必要があります。

Egnyte に対する Azure AD SSO を構成してテストするには、次の構成要素を完了します。

1. **[Azure AD SSO の構成](#configure-azure-ad-sso)** - ユーザーがこの機能を使用できるようにします。
    1. **[Azure AD のテスト ユーザーの作成](#create-an-azure-ad-test-user)** - B.Simon で Azure AD のシングル サインオンをテストします。
    1. **[Azure AD テスト ユーザーの割り当て](#assign-the-azure-ad-test-user)** - B.Simon が Azure AD シングル サインオンを使用できるようにします。
1. **[Egnyte の SSO の構成](#configure-egnyte-sso)** - アプリケーション側でシングル サインオン設定を構成します。
    1. **[Egnyte のテスト ユーザーの作成](#create-egnyte-test-user)** - Egnyte で B.Simon に対応するユーザーを作成し、Azure AD の B.Simon にリンクさせます。
1. **[SSO のテスト](#test-sso)** - 構成が機能するかどうかを確認します。

### <a name="configure-azure-ad-sso"></a>Azure AD SSO の構成

このセクションでは、Azure portal 上で Azure AD のシングル サインオンを有効にします。

Egnyte で Azure AD シングル サインオンを構成するには、次の手順に従います。

1. [Azure portal](https://portal.azure.com/) の **Egnyte** アプリケーション統合ページで、 **[シングル サインオン]** を選択します。

    ![シングル サインオン構成のリンク](common/select-sso.png)

2. **[シングル サインオン方式の選択]** ダイアログで、 **[SAML/WS-Fed]** モードを選択して、シングル サインオンを有効にします。

    ![シングル サインオン選択モード](common/select-saml-option.png)

3. **[SAML でシングル サインオンをセットアップします]** ページで、 **[編集]** アイコンをクリックして **[基本的な SAML 構成]** ダイアログを開きます。

    ![基本的な SAML 構成を編集する](common/edit-urls.png)

4. **[基本的な SAML 構成]** セクションで、次の手順を実行します。

    ![Egnyte のドメインおよび URL のシングル サインオン情報](common/sp-signonurl.png)

    **[サインオン URL]** ボックスに、`https://<companyname>.egnyte.com` という形式で URL を入力します。

    > [!NOTE]
    > この値は実際のものではありません。 実際のサインオン URL でこの値を更新してください。 この値を取得するには、[Egnyte クライアント サポート チーム](https://www.egnyte.com/corp/contact_egnyte.html)に問い合わせてください。 Azure portal の **[基本的な SAML 構成]** セクションに示されているパターンを参照することもできます。

4. **[SAML でシングル サインオンをセットアップします]** ページの **[SAML 署名証明書]** セクションで、 **[ダウンロード]** をクリックして要件のとおりに指定したオプションからの**証明書 (Base64)** をダウンロードして、お使いのコンピューターに保存します。

    ![証明書のダウンロードのリンク](common/certificatebase64.png)

5. **[Egnyte のセットアップ]** セクションで、要件に従って適切な URL をコピーします。

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

このセクションでは、Britta Simon に Egnyte へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

1. Azure portal 上で **[エンタープライズ アプリケーション]** を選択し、 **[すべてのアプリケーション]** を選択してから、 **[Egnyte]** を選択します。

    ![[エンタープライズ アプリケーション] ブレード](common/enterprise-applications.png)

2. アプリケーションの一覧で **[Egnyte]** を選択します。

    ![アプリケーションの一覧の Egnyte のリンク](common/all-applications.png)

3. 左側のメニューで **[ユーザーとグループ]** を選びます。

    ![[ユーザーとグループ] リンク](common/users-groups-blade.png)

4. **[ユーザーの追加]** をクリックし、 **[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。

    ![[割り当ての追加] ウィンドウ](common/add-assign-user.png)

5. **[ユーザーとグループ]** ダイアログの [ユーザー] の一覧で **[Britta Simon]** を選択し、画面の下部にある **[選択]** ボタンをクリックします。

6. SAML アサーション内に任意のロール値が必要な場合、 **[ロールの選択]** ダイアログでユーザーに適したロールを一覧から選択し、画面の下部にある **[選択]** をクリッします。

7. **[割り当ての追加]** ダイアログで、 **[割り当て]** ボタンをクリックします。

## <a name="configure-egnyte-sso"></a>Egnyte の SSO の構成

1. 別の Web ブラウザー ウィンドウで、Egnyte 企業サイトに管理者としてサインインします。

2. **[設定]** をクリックします。
   
    ![設定](./media/egnyte-tutorial/ic787819.png "設定")

3. メニューで **[設定]** をクリックします。

    ![設定](./media/egnyte-tutorial/ic787820.png "設定")

4. **[構成]** タブをクリックし、 **[セキュリティ]** をクリックします。

    ![Security](./media/egnyte-tutorial/ic787821.png "Security")

5. **[シングル サインオン認証]** セクションで、次の手順を実行します。

    ![シングル サインオン認証](./media/egnyte-tutorial/ic787822.png "シングル サインオン認証")   
    
    a. **[シングル サインオン認証]** として **[SAML 2.0]** を選択します。
   
    b. **[ID プロバイダー]** として **[AzureAD]** を選択します。
   
    c. Azure portal からコピーした**ログイン URL** を **[ID プロバイダーのログイン URL]** テキスト ボックス内に貼り付けます。
   
    d. Azure portal からコピーした **Azure AD 識別子**を **[Identity provider entity ID]\(ID プロバイダーのエンティティ ID\)** テキスト ボックスに貼り付けます。
      
    e. Azure Portal からダウンロードした base-64 でエンコードされた証明書をメモ帳で開き、その内容をクリップボードにコピーして **[Identity provider certificate]\(ID プロバイダー証明書\)** ボックスに貼り付けます。
   
    f. **[既定のユーザー マッピング]** として **[メール アドレス]** を選択します。
   
    g. **[ドメイン固有の発行者の値を使用]** として **[無効]** を選択します。
   
    h. **[保存]** をクリックします。

### <a name="create-egnyte-test-user"></a>Egnyte のテスト ユーザーの作成

Azure AD ユーザーが Egnyte にサインインできるようにするには、そのユーザーを Egnyte にプロビジョニングする必要があります。 Egnyte の場合、プロビジョニングは手動で行います。

**ユーザー アカウントをプロビジョニングするには、次の手順に従います。**

1. **Egnyte** 企業サイトに管理者としてサインインします。

2. **[設定] \> [ユーザーとグループ]** の順にクリックします。

3. **[新しいユーザーの追加]** をクリックし、追加するユーザーの種類を選択します。
   
    ![ユーザー](./media/egnyte-tutorial/ic787824.png "ユーザー")

4. **[New Power User]\(新規のパワー ユーザー\)** セクションで、次の手順を実行します。
    
    ![新しい標準ユーザー](./media/egnyte-tutorial/ic787825.png "[新しい標準ユーザー]")   

    a. **[Email]\(メール\)** ボックスに、ユーザーのメール アドレス (例: **Brittasimon\@contoso.com**) を入力します。

    b. **[ユーザー名]** テキスト ボックスにユーザー名 (**Brittasimon** など) を入力します。

    c. **[Authentication Type]\(認証の種類\)** として **[シングル サインオン]** を選択します。
   
    d. **[保存]** をクリックします。
    
    >[!NOTE]
    >Azure Active Directory アカウント保有者に通知メールが届きます。
    >

>[!NOTE]
>他の Egnyte ユーザー アカウント作成ツールや、Egnyte から提供されている API を使用して、Azure AD ユーザー アカウントをプロビジョニングできます。
>

### <a name="test-sso"></a>SSO のテスト

このセクションでは、アクセス パネルを使用して Azure AD のシングル サインオン構成をテストします。

アクセス パネル上で [Egnyte] タイルをクリックすると、SSO を設定した Egnyte に自動的にサインインします。 アクセス パネルの詳細については、[アクセス パネルの概要](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)に関する記事を参照してください。

## <a name="additional-resources"></a>その他のリソース

- [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Azure Active Directory の条件付きアクセスとは](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Microsoft Cloud App Security におけるセッション制御とは](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)
