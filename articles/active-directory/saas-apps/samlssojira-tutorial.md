---
title: チュートリアル:Azure Active Directory と SAML SSO for Jira by resolution GmbH の統合 | Microsoft Docs
description: Azure Active Directory と SAML SSO for Jira by resolution GmbH の間でシングル サインオンを構成する方法について説明します。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 02/23/2021
ms.author: jeedes
ms.openlocfilehash: 827a05a8dfbf05b0dacb0bd812fb964567f39b3f
ms.sourcegitcommit: ac035293291c3d2962cee270b33fca3628432fac
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/24/2021
ms.locfileid: "104954209"
---
# <a name="tutorial-azure-active-directory-integration-with-saml-sso-for-jira-by-resolution-gmbh"></a>チュートリアル:Azure Active Directory と SAML SSO for Jira by resolution GmbH の統合

このチュートリアルでは、SAML SSO for Jira by resolution GmbH と Azure Active Directory (Azure AD) を統合する方法について説明します。 Azure AD と SAML SSO for Jira by resolution GmbH を統合すると、次のことができます。

* SAML SSO for Jira by resolution GmbH にアクセスするユーザーを Azure AD で制御する。
* ユーザーが自分の Azure AD アカウントで SAML SSO for Jira by resolution GmbH に自動的にサインインするように設定する。
* 1 つの中央サイト (Azure Portal) で自分のアカウントを管理します。

## <a name="prerequisites"></a>前提条件

開始するには、次が必要です。

* Azure AD サブスクリプション。 サブスクリプションがない場合は、[無料アカウント](https://azure.microsoft.com/free/)を取得できます。
* SAML SSO for Jira by resolution GmbH でのシングル サインオン (SSO) が有効なサブスクリプション。

## <a name="scenario-description"></a>シナリオの説明

このチュートリアルでは、テスト環境で Azure AD のシングル サインオンを構成してテストします。

* SAML SSO for Jira by resolution GmbH では、**SP** と **IDP** によって開始される SSO がサポートされます。

> [!NOTE]
> このアプリケーションの識別子は固定文字列値であるため、1 つのテナントで構成できるインスタンスは 1 つだけです。

## <a name="add-saml-sso-for-jira-by-resolution-gmbh-from-the-gallery"></a>ギャラリーから SAML SSO for Jira by resolution GmbH を追加する

Azure AD への SAML SSO for Jira by resolution GmbH の統合を構成するには、ギャラリーから管理対象 SaaS アプリの一覧に SAML SSO for Jira by resolution GmbH を追加する必要があります。

1. 職場または学校アカウントか、個人の Microsoft アカウントを使用して、Azure portal にサインインします。
1. 左のナビゲーション ウィンドウで **[Azure Active Directory]** サービスを選択します。
1. **[エンタープライズ アプリケーション]** に移動し、 **[すべてのアプリケーション]** を選択します。
1. 新しいアプリケーションを追加するには、 **[新しいアプリケーション]** を選択します。
1. **[ギャラリーから追加する]** セクションで、検索ボックスに「**SAML SSO for Jira by resolution GmbH**」と入力します。
1. 結果パネルから **[SAML SSO for Jira by resolution GmbH]** を選択し、アプリを追加します。 お使いのテナントにアプリが追加されるのを数秒待機します。

## <a name="configure-and-test-azure-ad-sso-for-saml-sso-for-jira-by-resolution-gmbh"></a>SAML SSO for Jira by resolution GmbH の Azure AD SSO の構成とテスト

**B.Simon** というテスト ユーザーを使用して、SAML SSO for Jira by resolution GmbH に対する Azure AD SSO を構成してテストします。 SSO を機能させるには、Azure AD ユーザーと SAML SSO for Jira by resolution GmbH の関連ユーザーとの間にリンク関係を確立する必要があります。

SAML SSO for Jira by resolution GmbH で Azure AD SSO を構成してテストするには、次の手順に従います。

1. **[Azure AD SSO の構成](#configure-azure-ad-sso)** - ユーザーがこの機能を使用できるようにします。
    1. **[Azure AD のテスト ユーザーの作成](#create-an-azure-ad-test-user)** - B.Simon で Azure AD のシングル サインオンをテストします。
    1. **[Azure AD テスト ユーザーの割り当て](#assign-the-azure-ad-test-user)** - B.Simon が Azure AD シングル サインオンを使用できるようにします。
1. **[SAML SSO for Jira by resolution GmbH の SSO の構成](#configure-saml-sso-for-jira-by-resolution-gmbh-sso)** - アプリケーション側でシングル サインオン設定を構成します。
    1. **[SAML SSO for Jira by resolution GmbH テスト ユーザーの作成](#create-saml-sso-for-jira-by-resolution-gmbh-test-user)** - SAML SSO for Jira by resolution GmbH で B.Simon に対応するユーザーを作成し、Azure AD の B.Simon にリンクさせます。
1. **[SSO のテスト](#test-sso)** - 構成が機能するかどうかを確認します。

## <a name="configure-azure-ad-sso"></a>Azure AD SSO の構成

これらの手順に従って、Azure portal で Azure AD SSO を有効にします。

1. Azure portal の **SAML SSO for Jira by resolution GmbH** アプリケーション統合ページで、 **[管理]** セクションを見つけ、 **[シングル サインオン]** を選択します。
1. **[シングル サインオン方式の選択]** ページで、 **[SAML]** を選択します。
1. **[SAML によるシングル サインオンのセットアップ]** ページで、 **[基本的な SAML 構成]** の鉛筆アイコンをクリックして設定を編集します。

   ![基本的な SAML 構成を編集する](common/edit-urls.png)

4. **[基本的な SAML 構成]** セクションで、アプリケーションを **IDP** 開始モードで構成する場合は、次の手順のようにします。

    a. **[識別子]** ボックスに、`https://<server-base-url>/plugins/servlet/samlsso` の形式で URL を入力します。

    b. **[応答 URL]** ボックスに、`https://<server-base-url>/plugins/servlet/samlsso` のパターンを使用して URL を入力します

    c. アプリケーションを **SP** 開始モードで構成する場合は、 **[追加の URL を設定します]** をクリックして、次の手順のようにします。

    **[サインオン URL]** ボックスに、`https://<server-base-url>/plugins/servlet/samlsso` という形式で URL を入力します。

    > [!NOTE]
    > 識別子、応答 URL、サインオン URL では、 **\<server-base-url>** を Jira インスタンスのベース URL に置き換えます。 Azure portal の **[基本的な SAML 構成]** セクションに示されているパターンを参照することもできます。 問題がある場合は、[SAML SSO for Jira by resolution GmbH クライアント サポート チーム](https://www.resolution.de/go/support)に問い合わせてください。

4. **[SAML でシングル サインオンをセットアップします]** ページの **[SAML 署名証明書]** セクションで、**フェデレーション メタデータ XML** をダウンロードし、コンピューターに保存します。

    ![証明書のダウンロードのリンク](common/metadataxml.png)

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

このセクションでは、B.Simon に SAML SSO for Jira by resolution GmbH へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

1. Azure portal で **[エンタープライズ アプリケーション]** を選択し、 **[すべてのアプリケーション]** を選択します。
1. アプリケーションの一覧で **[SAML SSO for Jira by resolution GmbH]** を選択します。
1. アプリの概要ページで、 **[管理]** セクションを見つけて、 **[ユーザーとグループ]** を選択します。
1. **[ユーザーの追加]** を選択し、 **[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。
1. **[ユーザーとグループ]** ダイアログの [ユーザー] の一覧から **[B.Simon]** を選択し、画面の下部にある **[選択]** ボタンをクリックします。
1. ユーザーにロールが割り当てられることが想定される場合は、 **[ロールの選択]** ドロップダウンからそれを選択できます。 このアプリに対してロールが設定されていない場合は、[既定のアクセス] ロールが選択されていることを確認します。
1. **[割り当ての追加]** ダイアログで、 **[割り当て]** をクリックします。

## <a name="configure-saml-sso-for-jira-by-resolution-gmbh-sso"></a>SAML SSO for Jira by resolution GmbH の SSO の構成 

1. 別の Web ブラウザー ウィンドウで、Jira インスタンスに管理者としてサインインします。

2. 右側にある歯車アイコンをポイントし、 **[Manage apps]\(アプリの管理\)** をクリックします。
    
    !["歯車" アイコンを指している矢印と、ドロップダウンから選択された [Manage apps]\(アプリの管理\) を示すスクリーンショット。](./media/samlssojira-tutorial/add-on-1.png)

3. [Administrator Access]\(管理者アクセス\) ページにリダイレクトされる場合は、 **[Password]\(パスワード\)** を入力し、 **[Confirm]\(確認\)** ボタンをクリックします。

    ![[Administrator Access]\(管理者アクセス\) ページを示すスクリーンショット。](./media/samlssojira-tutorial/add-on-2.png)

4. Jira では通常、Atlassian マーケットプレースにリダイレクトされます。 そうでない場合は、左側のパネルで **[Find new apps]\(新しいアプリの検索\)** をクリックします。 **[SAML Single Sign On (SSO) for JIRA]\(JIRA の SAML シングル サインオン \(SSO\)\)** を検索し、 **[Install]\(インストール\)** ボタンをクリックして、SAML プラグインをインストールします。

    ![[Atlassian Marketplace for JIRA]\(JIRA の Atlassian マーケットプレース\) を示すスクリーンショット。[S A M L Single Sign On (S S O) Jira, S A M L/S S O]\(S A M L シングル サインオン (S S O) Jira、S A M L/S S O\) アプリの [Install]\(インストール\) ボタンを指している矢印が表示されています。](./media/samlssojira-tutorial/store.png)

5. プラグインのインストールが開始されます。 終わったら、 **[Close]\(閉じる\)** ボタンをクリックします。

    ![[Installing]\(インストール中\) ダイアログを示すスクリーンショット。](./media/samlssojira-tutorial/store-2.png)

    !["Installed and ready to go!"\(インストールされ、使用できるようになりました\) と示すスクリーンショット。 [Close]\(閉じる\) ボタンが選択されているダイアログ。](./media/samlssojira-tutorial/store-3.png)

6. 次に、 **[Manage]\(管理\)** をクリックします。

    ![[S A M L Single Sign On (S S O) Jira, S A M L/S S O]\(S A M L シングル サインオン (S S O) Jira、S A M L/S S O\) アプリを示すスクリーンショット。[Manage]\(管理\) ボタンが選択されています。](./media/samlssojira-tutorial/store-4.png)
    
7. その後、 **[Configure]\(構成\)** をクリックして、インストールしたばかりのプラグインを構成します。

    ![[Manage apps]\(アプリの管理\) ページを示すスクリーンショット。[S A M L SingleSignOn for Jira]\(Jira の S A M L シングル サインオン\) アプリの [Configure]\(構成\) ボタンが選択されています。](./media/samlssojira-tutorial/store-5.png)

8. **[SAML SingleSignOn Plugin Configuration]\(SAML SingleSignOn プラグインの構成\)** ウィザードで **[Add new IdP]\(新しい IDP の追加\)** をクリックし、新しい ID プロバイダーとして Azure AD を構成します。

    ![[Welcome]\(ようこそ\) ページを示すスクリーンショット。[Add new I d P]\(新しい I D P の追加\) ボタンが選択されています。](./media/samlssojira-tutorial/add-on-4.png) 

9. **[Choose your SAML Identity Provider]\(SAML ID プロバイダーの選択\)** ページで、次の手順のようにします。

    ![[Choose your S A M L Identity Provider]\(S A M L I D プロバイダーの選択\) ページを示すスクリーンショット。[I d P Type]\(I d P の種類\) および [Name]\(名前\) テキスト ボックスが強調表示され、[Next]\(次へ\) ボタンが選択されています。](./media/samlssojira-tutorial/identity-provider.png)
 
    a. IDP の種類として **[Azure AD]** を設定します。
    
    b. ID プロバイダーの **[Name]\(名前\)** を追加します (例: Azure AD)。
    
    c. (省略可能) ID プロバイダーの **[Description]\(説明\)** を追加します (例: Azure AD)。
    
    d. **[次へ]** をクリックします。
    
10. **[Identity provider configuration]\(ID プロバイダーの構成\)** ページで、 **[次へ]** をクリックします。
 
    ![[Identity provider configuration]\(I D プロバイダーの構成\) ページを示すスクリーンショット。](./media/samlssojira-tutorial/configuration.png)

11. **[Import SAML IdP Metadata]\(SAML IDP メタデータのインポート\)** ページで、次の手順を実行します。

    ![[Import S A M L I d P Metadata]\(S A M L I D P メタデータのインポート\) ページを示すスクリーンショット。[Select Metadata X M L File]\(メタデータ X M L ファイルの選択\) アクションが選択されています。](./media/samlssojira-tutorial/metadata.png)

    a. **[Select Metadata XML File]\(メタデータ XML ファイルの選択\)** ボタンをクリックし、前にダウンロードした **フェデレーション メタデータ XML** ファイルを選択します。

    b. **[Import]\(インポート\)** ボタンをクリックします。
     
    c. インポートが成功するまでしばらく待ちます。  
     
    d. **[次へ]** をクリックします。
    
12. **[User ID attribute and transformation]\(ユーザーの ID 属性と変換\)** ページで、 **[Next]\(次へ\)** ボタンをクリックします。

    ![[User I D attribute and transformation]\(ユーザーの I D 属性と変換\) ページを示すスクリーンショット。[Next]\(次へ\) ボタンが選択されています。](./media/samlssojira-tutorial/transformation.png)
    
13. **[User creation and update]\(ユーザーの作成と更新\)** ページで、 **[Save & Next]\(保存して次へ\)** をクリックして設定を保存します。
    
    ![[User creation and update]\(ユーザーの作成と更新\) ページを示すスクリーンショット。[Next]\(次へ\) ボタンが選択されています。](./media/samlssojira-tutorial/update.png)
    
14. **[Test your settings]\(設定のテスト\)** ページで、 **[Skip test & configure manually]\(テストをスキップして手動で構成\)** をクリックして、今はユーザー テストをスキップします。 テストは次のセクションで実行し、Azure portal でいくつか設定する必要があります。
    
    ![[Test your settings]\(設定のテスト\) ページを示すスクリーンショット。[Skip test & configure manually]\(テストをスキップして手動で構成\) ボタンが選択されています。](./media/samlssojira-tutorial/test.png)
    
15. **[OK]** をクリックして警告をスキップします。
    
    ![[O K] ボタンが選択されている警告ダイアログを示すスクリーンショット。](./media/samlssojira-tutorial/warning.png)

### <a name="create-saml-sso-for-jira-by-resolution-gmbh-test-user"></a>SAML SSO for Jira by resolution GmbH のテスト ユーザーの作成

Azure AD ユーザーが SAML SSO for Jira by resolution GmbH にサインインできるようにするには、ユーザーを SAML SSO for Jira by resolution GmbH にプロビジョニングする必要があります。 このチュートリアルの場合、プロビジョニングは手作業で行う必要があります。 ただし、resolution による SAML SSO プラグインでは、他のプロビジョニング モデルも使用できます (**Just In Time** プロビジョニングなど)。 [SAML SSO by resolution GmbH](https://wiki.resolution.de/doc/saml-sso/latest/all) のドキュメントをご覧ください。 質問がある場合は、[resolution のサポート](https://www.resolution.de/go/support)に問い合わせてください。

**ユーザー アカウントを手作業でプロビジョニングするには、次の手順のようにします。**

1. 管理者として Jira インスタンスにサインインします。

2. 歯車アイコンをポイントし、 **[User management]\(ユーザー管理\)** を選択します。

   !["歯車" アイコンを指している矢印と、ドロップダウンから選択された [User management]\(ユーザー管理\) を示すスクリーンショット。](./media/samlssojira-tutorial/user-1.png)

3. [Administrator Access]\(管理者アクセス\) ページにリダイレクトされる場合は、 **[Password]\(パスワード\)** を入力し、 **[Confirm]\(確認\)** ボタンをクリックします。

    ![[Administrator Access]\(管理者アクセス\) ページを示すスクリーンショット。[Password]\(パスワード\) テキスト ボックスが強調表示されています。](./media/samlssojira-tutorial/user-2.png) 

4. **[User management]\(ユーザー管理\)** タブ セクションで、 **[create user]\(ユーザーの作成\)** をクリックします。

    ![[User management]\(ユーザー管理\) タブを示すスクリーンショット。[Create user]\(ユーザーの作成\) ボタンが選択されています。](./media/samlssojira-tutorial/user-3-new.png) 

5. **[Create new user]\(新しいユーザーの作成\)** ダイアログ ページで、次の手順のようにします。 Azure AD とまったく同じようにユーザーを作成する必要があります。

    ![従業員の追加](./media/samlssojira-tutorial/user-4-new.png) 

    a. **[Email address]\(電子メール アドレス\)** ボックスに、ユーザーのメール アドレス <b>BrittaSimon@contoso.com</b> を入力します。

    b. **[Full Name]\(フル ネーム\)** ボックスに、ユーザーの氏名を入力します: **Britta Simon**。

    c. **[Username]\(ユーザー名\)** ボックスに、ユーザーのメール アドレス <b>BrittaSimon@contoso.com</b> を入力します。 

    d. **[Password]\(パスワード\)** ボックスに、ユーザーのパスワードを入力します。

    e. **[Create user]\(ユーザーの作成\)** をクリックして、ユーザーの作成を完了します。

## <a name="test-sso"></a>SSO のテスト

このセクションでは、次のオプションを使用して Azure AD のシングル サインオン構成をテストします。 

#### <a name="sp-initiated"></a>SP Initiated:

* Azure portal で **[このアプリケーションをテストします]** をクリックします。 これにより、ログイン フローを開始できる SAML SSO for Jira by resolution GmbH のサインオン URL にリダイレクトされます。  

* SAML SSO for Jira by resolution GmbH のサインオン URL に直接移動し、そこからログイン フローを開始します。

#### <a name="idp-initiated"></a>IDP Initiated:

* Azure portal で **[このアプリケーションをテストします]** をクリックすると、SSO を設定した SAML SSO for Jira by resolution GmbH に自動的にサインインされます。 

また、Microsoft マイ アプリを使用して、任意のモードでアプリケーションをテストすることもできます。 マイ アプリで [SAML SSO for Jira by resolution GmbH] タイルをクリックすると、SP モードで構成されている場合は、ログイン フローを開始するためのアプリケーション サインオン ページにリダイレクトされます。IDP モードで構成されている場合は、SSO を設定した SAML SSO for Jira by resolution GmbH に自動的にサインインされます。 マイ アプリの詳細については、[マイ アプリの概要](../user-help/my-apps-portal-end-user-access.md)に関するページを参照してください。

## <a name="enable-sso-redirection-for-jira"></a>Jira の SSO リダイレクトを有効にする

前のセクションで説明したように、現在、シングル サインオンをトリガーするには 2 つの方法があります。 **Azure portal** を使用するか、または **Jira インスタンスへの特別なリンク** を使用します。 resolution GmbH による SAML SSO プラグインでは、単純に **Jira インスタンスを指している任意の URL にアクセスする** ことでシングル サインオンをトリガーすることもできます。

基本的に、Jira にアクセスするすべてのユーザーは、プラグインでオプションをアクティブ化した後、シングル サインオンにリダイレクトされます。

SSO リダイレクトを有効にするには、**お使いの Jira インスタンス** で次のようにします。

1. Jira で SAML SSO プラグインの構成ページにアクセスします。
1. 左側のパネルで **[Redirection]\(リダイレクト\)** をクリックします。

   ![Jira の SAML SingleSignOn プラグインの構成ページの断片的なスクリーンショット (左側のナビゲーションにある [Redirection]\(リダイレクト\) リンクを強調表示したところ)。](./media/samlssojira-tutorial/configure-1.png)

1. **[Enable SSO Redirect]\(SSO リダイレクトを有効にする\)** をオンにします。

   ![Jira の SAML SingleSignOn プラグインの構成ページの断片的なスクリーンショット ([Enable SSO Redirect]\(SSO リダイレクトを有効にする\) チェック ボックスのオン状態を強調表示したところ)。](./media/samlssojira-tutorial/configure-2.png) 

1. 右上隅の **[Save Settings]\(設定の保存\)** ボタンをクリックします。

`https://<server-base-url>/login.jsp?nosso` に移動して **[Enable nosso]\(nosso を有効にする\)** をオンにした場合は、このオプションを有効にした後でもユーザー名とパスワードの入力を求められることがあります。 やはり、 **\<server-base-url>** はベース URL に置き換えます。

## <a name="next-steps"></a>次のステップ

SAML SSO for Jira by resolution GmbH を構成したら、組織の機密データを流出と侵入からリアルタイムで保護するセッション制御を適用できます。 セッション制御は、条件付きアクセスを拡張したものです。 [Microsoft Cloud App Security でセッション制御を強制する方法](/cloud-app-security/proxy-deployment-any-app)をご覧ください。