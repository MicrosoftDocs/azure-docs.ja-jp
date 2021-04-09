---
title: チュートリアル:Azure Active Directory シングル サインオン (SSO) と NetSuite の統合 | Microsoft Docs
description: Azure Active Directory と NetSuite の間でシングル サインオンを構成する方法について説明します。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/20/2021
ms.author: jeedes
ms.openlocfilehash: d99a19efcef0cae518d8d21d3371adaf37d32ff7
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "98625483"
---
# <a name="tutorial-integrate-azure-ad-single-sign-on-sso-with-netsuite"></a>チュートリアル:Azure AD シングル サインオン (SSO) を NetSuite と統合する

このチュートリアルでは、NetSuite と Azure Active Directory (Azure AD) を統合する方法について説明します。 Azure AD と NetSuite を統合すると、次のことができます。

* NetSuite にアクセスできるユーザーを Azure AD で制御できます。
* ユーザーが自分の Azure AD アカウントを使用して NetSuite に自動的にサインインできるように設定できます。
* 1 つの中央サイト (Azure Portal) でアカウントを管理できます。

## <a name="prerequisites"></a>前提条件

開始するには、次が必要です。

* Azure AD サブスクリプション。 サブスクリプションがない場合は、[無料アカウント](https://azure.microsoft.com/free/)を取得できます。
* NetSuite でのシングル サインオン (SSO) が有効なサブスクリプション。

## <a name="scenario-description"></a>シナリオの説明

このチュートリアルでは、テスト環境で Azure AD の SSO を構成してテストします。 

NetSuite では、以下がサポートされます。

* IDP-Initiated SSO。
* JIT (Just-In-Time) ユーザー プロビジョニング。

> [!NOTE]
> このアプリケーションの識別子は固定文字列値であるため、1 つのテナントで構成できるインスタンスは 1 つだけです。

## <a name="add-netsuite-from-the-gallery"></a>ギャラリーからの NetSuite の追加

NetSuite の Azure AD への統合を構成するには、次の手順を実行して、NetSuite をギャラリーからマネージド SaaS アプリの一覧に追加します。

1. 職場または学校アカウントか、個人の Microsoft アカウントを使用して、Azure portal にサインインします。
1. 左側のウィンドウで、 **[Azure Active Directory]** サービスを選択します。
1. **[エンタープライズ アプリケーション]** に移動し、 **[すべてのアプリケーション]** を選択します。
1. 新しいアプリケーションを追加するには、 **[新しいアプリケーション]** を選択します。
1. **[ギャラリーから追加する]** セクションで、検索ボックスに「**NetSuite**」と入力します。
1. 結果ペインで、 **[NetSuite]** を選択し、アプリを追加します。 お使いのテナントにアプリが追加されるのを数秒待機します。

## <a name="configure-and-test-azure-ad-single-sign-on-for-netsuite"></a>NetSuite の Azure AD シングル サインオンの構成とテスト

**B.Simon** というテスト ユーザーを使用して、NetSuite に対する Azure AD SSO を構成してテストします。 SSO が機能するためには、Azure AD ユーザーと NetSuite の関連ユーザーとの間にリンク関係を確立する必要があります。

NetSuite での Azure AD SSO を構成してテストするには、次の構成要素を完了します。

1. [Azure AD SSO を構成](#configure-azure-ad-sso)して、ユーザーがこの機能を使用できるようにします。
    * [Azure AD のテスト ユーザーを作成](#create-an-azure-ad-test-user)して、ユーザー B.Simon を使って Azure AD のシングル サインオンをテストします。  
    * [Azure AD テスト ユーザーを割り当て](#assign-the-azure-ad-test-user)て、ユーザー B.Simon が Azure AD シングル サインオンを使用できるようにします。
1. [NetSuite SSO を構成](#configure-netsuite-sso)して、アプリケーション側でシングル サインオン設定を構成します。
    * [NetSuite のテスト ユーザーを作成](#create-the-netsuite-test-user)して、NetSuite でユーザー B.Simon に対応するユーザーを作成し、Azure AD の B.Simon にリンクさせます。
1. [SSO のテスト](#test-sso)。構成が機能することを確認します。

## <a name="configure-azure-ad-sso"></a>Azure AD SSO の構成

Azure portal で Azure AD SSO を有効にするには、以下を実行します。

1. Azure portal の **NetSuite** アプリケーション統合ページで、 **[管理]** セクションを見つけて、 **[シングル サインオン]** を選択します。
1. **[シングル サインオン方式の選択]** ウィンドウで、 **[SAML]** を選択します。
1. **[SAML によるシングル サインオンのセットアップ]** ウィンドウで、 **[基本的な SAML 構成]** の横にある **[編集]** ("鉛筆") アイコンを選択します。

   ![基本的な SAML 構成を編集する](common/edit-urls.png)

1. **[基本的な SAML 構成]** セクションの **[応答 URL]** ボックスに、次のいずれかの形式で URL を入力します。

    ```https
    https://<Instance ID>.NetSuite.com/saml2/acs
    https://<Instance ID>.na1.NetSuite.com/saml2/acs
    https://<Instance ID>.na2.NetSuite.com/saml2/acs
    https://<Instance ID>.sandbox.NetSuite.com/saml2/acs
    https://<Instance ID>.na1.sandbox.NetSuite.com/saml2/acs
    https://<Instance ID>.na2.sandbox.NetSuite.com/saml2/acs
    ```

    * **<`Instance ID`>** の値は、このチュートリアルの手順 8. で後述する [Netsuite Configuration]\(Netsuite の構成\) セクションで取得します。 厳密なドメイン (このケースでは system.na0.netsuite.com など) が見つかります。

        ![[SAML 設定] ページを示すスクリーンショット。ここでは、ドメインを取得できます。](./media/NetSuite-tutorial/domain-value.png)

        > [!NOTE]
        > 上記の URL の値は、実際の値ではありません。 実際の応答 URL でこの値を更新します。 この値を取得するには、[NetSuite クライアント サポート チーム](http://www.netsuite.com/portal/services/support-services/suitesupport.shtml)にお問い合わせください。 また、Azure portal の **[基本的な SAML 構成]** セクションに示されている形式を参照することもできます。

1. NetSuite アプリケーションは、特定の形式の SAML アサーションを使用するため、カスタム属性のマッピングを SAML トークンの属性の構成に追加する必要があります。 次のスクリーンショットには、既定の属性一覧が示されています。

    ![image](common/default-attributes.png)

1. 上記に加えて、NetSuite アプリケーションでは、いくつかの属性が SAML 応答で返されることが想定されています。それらの属性を次に示します。 これらの属性も値が事前に設定されますが、要件に従ってそれらの値を確認することができます。

    | 名前 | ソース属性 |
    | ---------------| --------------- |
    | account  | `account id` |

    > [!NOTE]
    > account 属性の値は、実際の値ではありません。 この値は、このチュートリアルで後述するように更新します。

1. [SAML によるシングル サインオンのセットアップ] ページの [SAML 署名証明書] セクションで、[フェデレーション メタデータ XML] を探して [ダウンロード] を選択し、証明書をダウンロードして、お使いのコンピューターに保存します。

    ![証明書のダウンロードのリンク](common/metadataxml.png)

1. **[NetSuite のセットアップ]** セクションで、実際の要件に応じて適切な URL をコピーします。

    ![構成 URL のコピー](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Azure AD のテスト ユーザーの作成

このセクションでは、Azure portal 内で B.Simon というテスト ユーザーを作成します。

1. Azure portal の左側のウィンドウで、 **[Azure Active Directory]**  >  **[ユーザー]**  >  **[すべてのユーザー]** を選択します。

1. 画面の上部にある **[新しいユーザー]** を選択します。

1. **[ユーザー]** プロパティ ウィンドウで、以下の手順に従います。

   a. **[名前]** ボックスに「**B.Simon**」と入力します。  
   b. **[ユーザー名]** ボックスに「username@companydomain.extension」と入力します (たとえば、B.Simon@contoso.com)。  
   c. **[パスワードを表示]** チェック ボックスをオンにし、 **[パスワード]** ボックスに表示された値を書き留めます。  
   d. **［作成］** を選択します

### <a name="assign-the-azure-ad-test-user"></a>Azure AD テスト ユーザーの割り当て

このセクションでは、ユーザー B.Simon に NetSuite へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

1. Azure portal で **[エンタープライズ アプリケーション]** を選択し、 **[すべてのアプリケーション]** を選択します。
1. アプリケーションの一覧で **[NetSuite]** を選択します。
1. [概要] ウィンドウで **[管理]** セクションを探し、 **[ユーザーとグループ]** リンクを選択します。
1. **[ユーザーの追加]** を選択し、 **[割り当ての追加]** ウィンドウで **[ユーザーとグループ]** を選択します。
1. **[ユーザーとグループ]** ウィンドウの **[ユーザー]** ボックスの一覧で **[B.Simon]** を選択し、画面の下部にある **[選択]** を選択します。
1. SAML アサーション内にロール値が必要な場合は、以下を実行します。

   a. **[ロールの選択]** ウィンドウにあるドロップダウン リストで、ユーザーに適したロールを選択します。  
   b. 画面の下部にある **[選択]** を選択します。
1. **[割り当ての追加]** ウィンドウで、 **[割り当て]** を選択します。

## <a name="configure-netsuite-sso"></a>NetSuite の SSO の構成

1. ブラウザーで新しいタブを開き、NetSuite の会社のサイトに管理者としてサインインします。

2. 上部のナビゲーション バーで、 **[Setup]\(セットアップ\)** を選択し、 **[Company]\(会社\)**  >  **[Enable Features]\(機能の有効化\)** を選択します。

    ![[会社] で選択した [機能を有効にする] を示すスクリーンショット。](./media/NetSuite-tutorial/ns-setupsaml.png)

3. ページの中央にあるツール バーで、 **[SuiteCloud]** を選択します。

    ![[SuiteCloud] が選択されていることを示すスクリーンショット。](./media/NetSuite-tutorial/ns-suitecloud.png)

4. **[Manage Authentication]\(認証の管理\)** で、 **[SAML Single Sign-on]\(SAML シングル サインオン\)** チェック ボックスをオンにして、NetSuite での SAML シングル サインオン オプションを有効にします。

    ![[認証を管理する] を示すスクリーンショット。ここでは、[SAML シングル サインオン] を選択できます。](./media/NetSuite-tutorial/ns-ticksaml.png)

5. 上部のナビゲーション バーで、 **[Setup]\(セットアップ\)** を選択します。

    ![NETSUITE ナビゲーション バーで [セットアップ] が選択されていることを示すスクリーンショット。](./media/NetSuite-tutorial/ns-setup.png)

6. **[Setup Tasks]\(セットアップ タスク\)** 一覧で、 **[Integration]\(統合\)** を選択します。

    ![[セットアップ タスク] で [統合] が選択されていることを示すスクリーンショット。](./media/NetSuite-tutorial/ns-integration.png)

7. **[Manage Authentication]\(認証の管理\)** で、 **[SAML Single Sign-on]\(SAML シングル サインオン\)** を選択します。

    ![[セットアップ タスク] の [統合] 項目で [SAML シングル サインオン] が選択されていることを示すスクリーンショット。](./media/NetSuite-tutorial/ns-saml.png)

8. **[SAML Setup]\(SAML セットアップ\)** ウィンドウの **[NetSuite Configuration]\(NetSuite の構成\)** で、以下を実行します。

    ![[SAML 設定] を示すスクリーンショット。ここでは、説明されている値を入力できます。](./media/NetSuite-tutorial/ns-saml-setup.png)
  
    a. **[Primary Authentication Method]\(プライマリ認証方法\)** チェック ボックスをオンにします。

    b. **[SAMLV2 Identity Provider Metadata]\(SAMLV2 ID プロバイダー メタデータ\)** で、 **[Upload IDP Metadata File]\(IDP メタデータ ファイルのアップロード\)** を選択し、次に **[Browse]\(参照\)** を選択して、Azure portal からダウンロードしたメタデータ ファイルをアップロードします。

    c. **[Submit]\(送信\)** をクリックします。

9. NetSuite の上部のナビゲーション バーで、 **[Setup]\(セットアップ\)** を選択し、 **[Company]\(会社\)**  >  **[Company Information]\(会社情報\)** を選択します。

    ![[会社] で [会社情報] が選択されていることを示すスクリーンショット。](./media/NetSuite-tutorial/ns-com.png)

    ![説明されている値を入力できるウィンドウを示すスクリーンショット。](./media/NetSuite-tutorial/ns-account-id.png)

    b. **[Company Information]\(会社情報\)** ウィンドウで、右側の列の **[Account ID]\(アカウント ID\)** の値をコピーします。

    c. NetSuite アカウントからコピーした **アカウント ID** を Azure AD の **[属性値]** ボックスに貼り付けます。

    ![アカウント ID の値を追加する画面のスクリーンショット](./media/netsuite-tutorial/attribute-value.png)

10. ユーザーは NetSuite にシングル サインオンする前に、まず、NetSuite で適切なアクセス許可が割り当てられている必要があります。 これらのアクセス許可を割り当てるには、以下を実行します。

    a. 上部のナビゲーション バーで、 **[Setup]\(セットアップ\)** を選択します。

    ![NETSUITE ナビゲーション バーで [セットアップ] が選択されていることを示すスクリーンショット。](./media/NetSuite-tutorial/ns-setup.png)

    b. 左側のウィンドウで、 **[Users/Roles]\(ユーザーとロール\)** を選択し、 **[Manage Roles]\(ロールの管理\)** を選択します。

    ![[ロールの管理] ウィンドウを示すスクリーンショット。ここでは、[新しいロール] を選択できます。](./media/NetSuite-tutorial/ns-manage-roles.png)

    c. **[New Role]\(新しいロール\)** を選択します。

    d. 新しいロールの **名前** を入力します。

    ![[セットアップ マネージャー] を示すスクリーンショット。ここでは、ロールの名前を入力できます。](./media/NetSuite-tutorial/ns-new-role.png)

    e. **[保存]** を選択します。

    f. 上部のナビゲーション バーで、 **[Permissions]\(アクセス許可\)** を選択します。 次に、 **[Setup]\(セットアップ\)** を選択します。

    ![[セットアップ] タブを示すスクリーンショット。ここでは、説明されている値を入力できます。](./media/NetSuite-tutorial/ns-sso.png)

    g. **[SAML Single Sign-on]\(SAML シングル サインオン\)** を選択し、 **[Add]\(追加\)** を選択します。

    h. **[保存]** を選択します。

    i. 上部のナビゲーション バーで、 **[Setup]\(セットアップ\)** を選択し、 **[Setup Manager]\(セットアップ マネージャー\)** を選択します。

    ![NETSUITE ナビゲーション バーで [セットアップ] が選択されていることを示すスクリーンショット。](./media/NetSuite-tutorial/ns-setup.png)

    j. 左側のウィンドウで、 **[Users/Roles]\(ユーザーとロール\)** を選択し、 **[Manage Users]\(ユーザーの管理\)** を選択します。

    ![[ユーザーの管理] ウィンドウを示すスクリーンショット。ここでは、[Suite Demo Team] を選択できます。](./media/NetSuite-tutorial/ns-manage-users.png)

    k. テスト ユーザーを選択します。 **[Edit]\(編集\)** を選択して、 **[Access]\(アクセス\)** タブを選択します。

    ![[ユーザーの管理] ウィンドウを示すスクリーンショット。ここでは、[編集] を選択できます。](./media/NetSuite-tutorial/ns-edit-user.png)

    l. **[Roles]\(ロール\)** ウィンドウで、作成した適切なロールを割り当てます。

    ![[従業員] で [管理者] が選択されていることを示すスクリーンショット。](./media/NetSuite-tutorial/ns-add-role.png)

    m. **[保存]** を選択します。

### <a name="create-the-netsuite-test-user"></a>NetSuite のテスト ユーザーの作成

このセクションでは、B.Simon というユーザーを NetSuite に作成します。 NetSuite では、Just-In-Time ユーザー プロビジョニングがサポートされています。この設定は既定で有効になっています。 このセクションでは、ユーザー側で必要な操作はありません。 NetSuite にユーザーがまだ存在していない場合は、認証後に新規に作成されます。

## <a name="test-sso"></a>SSO のテスト

このセクションでは、次のオプションを使用して Azure AD のシングル サインオン構成をテストします。

- Azure portal で [このアプリケーションをテストします] をクリックすると、SSO を設定した NetSuite に自動的にサインインされます

- Microsoft マイ アプリを使用することができます。 マイ アプリ上で [NetSuite] タイルをクリックすると、SSO を設定した NetSuite に自動的にサインインします。 マイ アプリの詳細については、[マイ アプリの概要](../user-help/my-apps-portal-end-user-access.md)に関するページを参照してください。


## <a name="next-steps"></a>次のステップ

NetSuite を構成したら、組織の機密データを流出と侵入からリアルタイムで保護するセッション制御を適用することができます。 セッション制御は、条件付きアクセスを拡張したものです。 [Microsoft Cloud App Security でセッション制御を適用する方法](/cloud-app-security/proxy-deployment-aad)をご覧ください。