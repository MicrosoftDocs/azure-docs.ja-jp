---
title: チュートリアル:Azure Active Directory シングル サインオン (SSO) と Akamai の統合 | Microsoft Docs
description: Azure Active Directory と Akamai の間でシングル サインオンを構成する方法について説明します。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/03/2020
ms.author: jeedes
ms.openlocfilehash: fbec82e25424fd5220aa992cf2dd0e8449e6a0a4
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/18/2020
ms.locfileid: "88523135"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-akamai"></a>チュートリアル:Azure Active Directory シングル サインオン (SSO) と Akamai の統合

このチュートリアルでは、Akamai と Azure Active Directory (Azure AD) を統合する方法について説明します。 Azure AD と Akamai を統合すると、次のことができます。

* Akamai にアクセスできるユーザーを Azure AD で制御できます。
* ユーザーが自分の Azure AD アカウントを使用して Akamai に自動的にサインインできるように設定できます。
* 1 つの中央サイト (Azure Portal) で自分のアカウントを管理します。

SaaS アプリと Azure AD の統合の詳細については、「[Azure Active Directory でのアプリケーションへのシングル サインオン](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)」を参照してください。

Azure Active Directory と Akamai Enterprise Application Access の統合により、クラウドまたはオンプレミスでホストされているレガシ アプリケーションにシームレスにアクセスできます。 この統合ソリューションでは、レガシ アプリケーションにアクセスするために [Azure AD 条件付きアクセス](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-azure-portal)、[Azure AD Identity Protection](https://docs.microsoft.com/azure/active-directory/active-directory-identityprotection)、[Azure AD Identity Governance](https://docs.microsoft.com/azure/active-directory/governance/identity-governance-overview) など、Azure Active Directory の最新機能をすべて活用できます。その際、アプリの変更やエージェントのインストールは不要です。

次の図は、Akamai EAA が安全なハイブリッド アクセスの広範なシナリオに適しているようすを示しています。

![Akamai EAA は安全なハイブリッド アクセスの広範なシナリオに適しています](./media/header-akamai-tutorial/introduction01.png)

### <a name="key-authentication-scenarios"></a>キー認証のシナリオ

先進認証プロトコル (Open ID Connect、SAML、WS-Fed など) に対する Azure Active Directory のネイティブ統合のサポートとは別に、Akamai EAA は、Azure AD を使用することで、内部と外部の両方のアクセスに関してレガシベース認証アプリの安全なアクセスを拡張し、それらのアプリケーションへの最新のシナリオ (パスワードレス アクセスなど) を実現します。 これには次のものが含まれます

* ヘッダーベースの認証アプリ
* リモート デスクトップ
* SSH (Secure Shell)
* Kerberos 認証アプリ
* VNC (Virtual Network Computing)
* 匿名認証または非ビルトイン認証アプリ
* NTLM 認証アプリ (ユーザーに対する二重プロンプトでの保護)
* フォームベースのアプリケーション (ユーザーに対する二重プロンプトでの保護)

### <a name="integration-scenarios"></a>統合シナリオ

Microsoft と Akamai EAA のパートナーシップにより、ビジネス要件に基づく複数の統合シナリオがサポートされるため、柔軟にビジネス要件を満たすことができます。 これらを使用することで、すべてのアプリケーションにわたるカバレッジをゼロデイで実現し、適切なポリシー分類を段階的に分類および構成できます。

#### <a name="integration-scenario-1"></a>統合シナリオ 1

Akamai EAA が Azure AD 上で単一のアプリケーションとして構成されます。 管理者はそのアプリケーション上で CA ポリシーを構成することができ、条件が満たされると、ユーザーは Akamai EAA ポータルにアクセスできます。

**長所:** :

* IDP の構成が 1 回だけで済む

**短所**:

* ユーザーは最終的に 2 つのアプリケーション ポータルを持つことになる

* すべてのアプリケーションを対象とする、共通する 1 つの CA ポリシー。

![統合シナリオ 1](./media/header-akamai-tutorial/scenario1.png)

#### <a name="integration-scenario-2"></a>統合シナリオ 2

Akamai EAA アプリケーションが Azure AD ポータル上で個別に設定されます。 管理者はアプリケーションに対して個々の CA ポリシーを構成でき、条件が満たされると、ユーザーは特定のアプリケーションに直接リダイレクトされます。

**長所:** :

* 個々の CA ポリシーを定義できる

* すべてのアプリが O365 のワッフルと myApps.microsoft.com パネルに表示される。


**短所**:

* 複数の IDP を構成する必要がある。

![統合シナリオ 2](./media/header-akamai-tutorial/scenario2.png)

## <a name="prerequisites"></a>前提条件

開始するには、次が必要です。

* Azure AD サブスクリプション。 サブスクリプションがない場合は、[無料アカウント](https://azure.microsoft.com/free/)を取得できます。
* Akamai でのシングル サインオン (SSO) が有効なサブスクリプション。

## <a name="scenario-description"></a>シナリオの説明

このチュートリアルでは、テスト環境で Azure AD の SSO を構成してテストします。

- Akamai では、IDP Initiated SSO がサポートされます

#### <a name="important"></a>重要

以下に示す設定はすべて、**統合シナリオ 1** と**シナリオ 2** で同じです。 **統合シナリオ 2** では、Akamai EAA で個々の IDP を設定する必要があり、URL プロパティはアプリケーション URL を指すように変更する必要があります。

![重要](./media/header-akamai-tutorial/important.png)

## <a name="adding-akamai-from-the-gallery"></a>ギャラリーからの Akamai の追加

Azure AD への Akamai の統合を構成するには、ギャラリーからマネージド SaaS アプリの一覧に Akamai を追加する必要があります。

1. 職場または学校アカウントか、個人の Microsoft アカウントを使用して、[Azure portal](https://portal.azure.com) にサインインします。
1. 左のナビゲーション ウィンドウで **[Azure Active Directory]** サービスを選択します。
1. **[エンタープライズ アプリケーション]** に移動し、 **[すべてのアプリケーション]** を選択します。
1. 新しいアプリケーションを追加するには、 **[新しいアプリケーション]** を選択します。
1. **[ギャラリーから追加する]** セクションで、検索ボックスに「**Akamai**」と入力します。
1. 結果のパネルから **[Akamai]** を選択し、アプリを追加します。 お使いのテナントにアプリが追加されるのを数秒待機します。

## <a name="configure-and-test-azure-ad-single-sign-on-for-akamai"></a>Akamai の Azure AD シングル サインオンの構成とテスト

**B.Simon** というテスト ユーザーを使用して、Akamai に対する Azure AD SSO を構成してテストします。 SSO が機能するためには、Azure AD ユーザーと Akamai の関連ユーザーとの間にリンク関係を確立する必要があります。

Akamai に対する Azure AD SSO を構成してテストするには、次の構成要素を完了します。

1. **[Azure AD SSO の構成](#configure-azure-ad-sso)** - ユーザーがこの機能を使用できるようにします。
    * **[Azure AD のテスト ユーザーの作成](#create-an-azure-ad-test-user)** - B.Simon で Azure AD のシングル サインオンをテストします。
    * **[Azure AD テスト ユーザーの割り当て](#assign-the-azure-ad-test-user)** - B.Simon が Azure AD シングル サインオンを使用できるようにします。
1. **[Akamai の SSO の構成](#configure-akamai-sso)** - アプリケーション側でシングル サインオン設定を構成します。
    * **[IDP の設定](#setting-up-idp)**
    * **[ヘッダー ベースの認証](#header-based-authentication)**
    * **[リモート デスクトップ](#remote-desktop)**
    * **[SSH](#ssh)**
    * **[Kerberos 認証](#kerberos-authentication)**
    * **[Akamai のテスト ユーザーの作成](#create-akamai-test-user)** - Akamai で B.Simon に対応するユーザーを作成し、Azure AD の B.Simon にリンクさせます。
1. **[SSO のテスト](#test-sso)** - 構成が機能するかどうかを確認します。

## <a name="configure-azure-ad-sso"></a>Azure AD SSO の構成

これらの手順に従って、Azure portal で Azure AD SSO を有効にします。

1. [Azure portal](https://portal.azure.com/) の **Akamai** アプリケーション統合ページで、 **[管理]** セクションを探して、 **[シングル サインオン]** を選択します。
1. **[シングル サインオン方式の選択]** ページで、 **[SAML]** を選択します。
1. **[SAML でシングル サインオンをセットアップします]** ページで、 **[基本的な SAML 構成]** の編集 (ペン) アイコンをクリックして設定を編集します。

   ![基本的な SAML 構成を編集する](common/edit-urls.png)

1. **[基本的な SAML 構成]** セクションで、アプリケーションを **IDP** 開始モードで構成する場合は、次のフィールドの値を入力します。

    a. **[識別子]** ボックスに、`https://<Yourapp>.login.go.akamai-access.com/saml/sp/response` の形式で URL を入力します。

    b. **[応答 URL]** ボックスに、`https:// <Yourapp>.login.go.akamai-access.com/saml/sp/response` のパターンを使用して URL を入力します

    > [!NOTE]
    > これらは実際の値ではありません。 実際の識別子と応答 URL でこれらの値を更新します。 この値を取得するには、[Akamai クライアント サポート チーム](https://www.akamai.com/us/en/contact-us/)にお問い合わせください。 Azure portal の **[基本的な SAML 構成]** セクションに示されているパターンを参照することもできます。

1. **[SAML でシングル サインオンをセットアップします]** ページの **[SAML 署名証明書]** セクションで、 **[フェデレーション メタデータ XML]** を探して **[ダウンロード]** を選択し、証明書をダウンロードして、お使いのコンピューターに保存します。

    ![証明書のダウンロードのリンク](common/metadataxml.png)

1. **[Set up Akamai]\(Akamai の設定\)** セクションで、要件に基づいて適切な URL をコピーします。

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

このセクションでは、B.Simon に Akamai へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

1. Azure portal で **[エンタープライズ アプリケーション]** を選択し、 **[すべてのアプリケーション]** を選択します。
1. アプリケーションの一覧で **[Akamai]** を選択します。
1. アプリの概要ページで、 **[管理]** セクションを見つけて、 **[ユーザーとグループ]** を選択します。

   ![[ユーザーとグループ] リンク](common/users-groups-blade.png)

1. **[ユーザーの追加]** を選択し、 **[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。

    ![[ユーザーの追加] リンク](common/add-assign-user.png)

1. **[ユーザーとグループ]** ダイアログの [ユーザー] の一覧から **[B.Simon]** を選択し、画面の下部にある **[選択]** ボタンをクリックします。
1. SAML アサーション内に任意のロール値が必要な場合、 **[ロールの選択]** ダイアログでユーザーに適したロールを一覧から選択し、画面の下部にある **[選択]** をクリックします。
1. **[割り当ての追加]** ダイアログで、 **[割り当て]** をクリックします。

## <a name="configure-akamai-sso"></a>Akamai の SSO の構成

### <a name="setting-up-idp"></a>IDP の設定

**AKAMAI EAA IDP の構成**

1. **Akamai Enterprise Application Access** コンソールにサインインします。
1. **Akamai EAA コンソール**で、 **[Identity]\(ID\)**  >  **[Identity Providers]\(ID プロバイダー\)** の順に選択し、 **[Add Identity Provider]\(ID プロバイダーの追加\)** をクリックします。

    ![Akamai の構成](./media/header-akamai-tutorial/configure01.png)

1. **[Create New Identity Provider]\(新しい ID プロバイダーの作成\)** で、次の手順を実行します。

    ![Akamai の構成](./media/header-akamai-tutorial/configure02.png)

    a. **一意の名前**を指定します。

    b. **[Third Party SAML]\(サード パーティの SAML\)** を選択し、 **[Create Identity Provider and Configure]\(ID プロバイダーを作成して構成する\)** をクリックします。

### <a name="general-settings"></a>全般設定

1. **[Identity Intercept]\(ID のインターセプト\)** - SP ベース URL の名前を指定します (Azure AD 構成に使用されます)。

    > [!NOTE]
    > カスタム ドメインを使用することもできます (DNS エントリと証明書が必要です)。 この例では、Akamai ドメインを使用します。

1. **[Akamai Cloud Zone]\(Akamai クラウド ゾーン\)** - 適切なクラウド ゾーンを選択します。
1. **[Certificate Validation]\(証明書の検証\)** - Akamai ドキュメントをチェックします (省略可)。

    ![Akamai の構成](./media/header-akamai-tutorial/configure03.png)

### <a name="authentication-configuration"></a>認証の構成

1. [URL] - ID インターセプトと同じ URL を指定します (認証後、ユーザーはここにリダイレクトされます)。
2. [Logout URL]\(ログアウト URL\): ログアウト URL を更新します。
3. [Sign SAML Request]\(SAML 要求に署名する\): 既定ではオフです。
4. IDP メタデータ ファイルには、Azure AD コンソールでアプリケーションを追加します。

    ![Akamai の構成](./media/header-akamai-tutorial/configure04.png)

### <a name="session-settings"></a>セッションの設定

設定は既定値のままにします。

![Akamai の構成](./media/header-akamai-tutorial/sessionsettings.png)

### <a name="directories"></a>ディレクトリ

ディレクトリの構成はスキップします。

![Akamai の構成](./media/header-akamai-tutorial/directories.png)

### <a name="customization-ui"></a>カスタマイズ UI

IDP に対するカスタマイズを追加できます。

![Akamai の構成](./media/header-akamai-tutorial/customizationui.png)

### <a name="advanced-settings"></a>詳細設定

詳細設定をスキップするか、Akamai のドキュメントで詳細を確認してください。

![Akamai の構成](./media/header-akamai-tutorial/advancesettings.png)

### <a name="deployment"></a>デプロイ

1. [Deploy Identity Provider]\(ID プロバイダーのデプロイ\) をクリックします。

    ![Akamai の構成](./media/header-akamai-tutorial/deployment.png)

2. デプロイが成功したことを確認します。

### <a name="header-based-authentication"></a>ヘッダー ベースの認証

Akamai ヘッダー ベースの認証

1. アプリケーションの追加ウィザードから **[Custom HTTP]\(カスタム HTTP\)** を選択します。

    ![Akamai の構成](./media/header-akamai-tutorial/configure05.png)

2. **[Application Name]\(アプリケーション名\)** と **[Description]\(説明\)** を入力します。

    ![Akamai の構成](./media/header-akamai-tutorial/configure06.png)

    ![Akamai の構成](./media/header-akamai-tutorial/configure07.png)

    ![Akamai の構成](./media/header-akamai-tutorial/configure08.png)

#### <a name="authentication"></a>認証

1. **[Authentication]\(認証\)** タブを選択します。

    ![Akamai の構成](./media/header-akamai-tutorial/configure09.png)

2. **ID プロバイダー**を割り当てます。

    ![Akamai の構成](./media/header-akamai-tutorial/configure10.png)

#### <a name="services"></a>サービス

[Save and Go to Authentication]\(保存して認証に移動\) をクリックします。

![Akamai の構成](./media/header-akamai-tutorial/configure11.png)

#### <a name="advanced-settings"></a>詳細設定

1. **[Customer HTTP Headers]\(カスタマー HTTP ヘッダー\)** で、**カスタマー ヘッダー**と **SAML 属性**を指定します。

    ![Akamai の構成](./media/header-akamai-tutorial/configure12.png)

1. **[Save and go to Deployment]\(保存してデプロイに移動\)** ボタンをクリックします。

    ![Akamai の構成](./media/header-akamai-tutorial/configure13.png)

#### <a name="deploy-the-application"></a>アプリケーションのデプロイ

1. **[Deploy Application]\(アプリケーションのデプロイ\)** ボタンをクリックします。

    ![Akamai の構成](./media/header-akamai-tutorial/configure14.png)

1. アプリケーションが正しくデプロイされたことを確認します。

    ![Akamai の構成](./media/header-akamai-tutorial/configure15.png)

1. エンドユーザー エクスペリエンス。

    ![Akamai の構成](./media/header-akamai-tutorial/enduser01.png)

    ![Akamai の構成](./media/header-akamai-tutorial/enduser02.png)

1. 条件付きアクセス。

    ![Akamai の構成](./media/header-akamai-tutorial/conditionalaccess01.png)

    ![Akamai の構成](./media/header-akamai-tutorial/conditionalaccess02.png)

#### <a name="remote-desktop"></a>リモート デスクトップ

1. ADD Applications ウィザードから **[RDP]** を選択します。

    ![Akamai の構成](./media/header-akamai-tutorial/configure16.png)

1. **[Application Name]\(アプリケーション名\)** と **[Description]\(説明\)** を入力します。

    ![Akamai の構成](./media/header-akamai-tutorial/configure17.png)

    ![Akamai の構成](./media/header-akamai-tutorial/configure18.png)

1. このサービスを提供するコネクタを指定します。

    ![Akamai の構成](./media/header-akamai-tutorial/configure19.png)

#### <a name="authentication"></a>認証

**[Save and go to Services]\(保存してサービスに移動\)** をクリックします。

![Akamai の構成](./media/header-akamai-tutorial/configure20.png)

#### <a name="services"></a>サービス

**[Save and go to Advanced Settings]\(保存して詳細設定に移動\)** をクリックします。

![Akamai の構成](./media/header-akamai-tutorial/configure21.png)

#### <a name="advanced-settings"></a>詳細設定

1. **[Save and go to Deployment]\(保存してデプロイに移動\)** をクリックします。

    ![Akamai の構成](./media/header-akamai-tutorial/configure22.png)

    ![Akamai の構成](./media/header-akamai-tutorial/configure23.png)

    ![Akamai の構成](./media/header-akamai-tutorial/configure24.png)

1. エンドユーザー エクスペリエンス

    ![Akamai の構成](./media/header-akamai-tutorial/enduser03.png)

    ![Akamai の構成](./media/header-akamai-tutorial/enduser02.png)

1. 条件付きアクセス

    ![Akamai の構成](./media/header-akamai-tutorial/conditionalaccess04.png)

    ![Akamai の構成](./media/header-akamai-tutorial/conditionalaccess05.png)

    ![Akamai の構成](./media/header-akamai-tutorial/conditionalaccess06.png)

1. または、RDP アプリケーションの URL を直接入力することもできます。

#### <a name="ssh"></a>SSH

1. [Add Applications]\(アプリケーションの追加\) に移動し、 **[SSH]** を選択します。

    ![Akamai の構成](./media/header-akamai-tutorial/configure25.png)

1. **[Application Name]\(アプリケーション名\)** と **[Description]\(説明\)** を入力します。

    ![Akamai の構成](./media/header-akamai-tutorial/configure26.png)

1. アプリケーション ID を構成します。

    ![Akamai の構成](./media/header-akamai-tutorial/configure27.png)

    a. 名前と説明を指定します。

    b. アプリケーション サーバーの IP (または FQDN) と SSH のポートを指定します。

    c. SSH ユーザー名とパスフレーズを指定します (Akamai EAA を確認してください)。

    d. 外部ホスト名を指定します。

    e. コネクタの場所を指定し、コネクタを選択します。

#### <a name="authentication"></a>認証

**[Save and go to Services]\(保存してサービスに移動\)** をクリックします。

![Akamai の構成](./media/header-akamai-tutorial/configure28.png)

#### <a name="services"></a>サービス

**[Save and go to Advanced Settings]\(保存して詳細設定に移動\)** をクリックします。

![Akamai の構成](./media/header-akamai-tutorial/configure29.png)

#### <a name="advanced-settings"></a>詳細設定

[Save and go to Deployment]\(保存してデプロイに移動\) をクリックします。

![Akamai の構成](./media/header-akamai-tutorial/configure30.png)

![Akamai の構成](./media/header-akamai-tutorial/configure31.png)

#### <a name="deployment"></a>デプロイ

1. **[Deploy application]\(アプリケーションのデプロイ\)** をクリックします。

    ![Akamai の構成](./media/header-akamai-tutorial/configure32.png)

1. エンドユーザー エクスペリエンス

    ![Akamai の構成](./media/header-akamai-tutorial/enduser03.png)

    ![Akamai の構成](./media/header-akamai-tutorial/enduser04.png)

1. 条件付きアクセス

    ![Akamai の構成](./media/header-akamai-tutorial/conditionalaccess04.png)

    ![Akamai の構成](./media/header-akamai-tutorial/conditionalaccess07.png)

    ![Akamai の構成](./media/header-akamai-tutorial/conditionalaccess08.png)

    ![Akamai の構成](./media/header-akamai-tutorial/conditionalaccess09.png)

### <a name="kerberos-authentication"></a>Kerberos 認証

次の例では、内部 Web サーバー <code>http://frp-app1.superdemo.live</code> を発行し、KCD を使用して SSO を有効化します

#### <a name="general-tab"></a>全般タブ

![Akamai の構成](./media/header-akamai-tutorial/generaltab.png)

#### <a name="authentication-tab"></a>[認証] タブ

ID プロバイダーを割り当てます。

![Akamai の構成](./media/header-akamai-tutorial/authenticationtab.png)

#### <a name="services-tab"></a>[Services]\(サービス\) タブ

![Akamai の構成](./media/header-akamai-tutorial/servicestab.png)

#### <a name="advanced-settings"></a>詳細設定

![Akamai の構成](./media/header-akamai-tutorial/advancesettings02.png)

> [!NOTE]
> このデモでは、Web サーバーの SPN は SPN@Domain 形式 (例: `HTTP/frp-app1.superdemo.live@SUPERDEMO.LIVE`) にする必要があります。 残りの設定は既定値のままにします。

#### <a name="deployment-tab"></a>[Deployment]\(デプロイ\) タブ

![Akamai の構成](./media/header-akamai-tutorial/deploymenttab.png)

#### <a name="adding-directory"></a>ディレクトリの追加

1. ドロップダウン リストから **[AD]** を選択します。

    ![Akamai の構成](./media/header-akamai-tutorial/configure33.png)

1. 必要なデータを入力します。

    ![Akamai の構成](./media/header-akamai-tutorial/configure34.png)

1. ディレクトリの作成を確認します。

    ![Akamai の構成](./media/header-akamai-tutorial/directorydomain.png)

1. アクセスを必要とするグループまたは OU を追加します。

    ![Akamai の構成](./media/header-akamai-tutorial/addgroup.png)

1. 以下では、グループが EAAGroup と呼ばれ、1 名のメンバーが含まれています。

    ![Akamai の構成](./media/header-akamai-tutorial/eaagroup.png)

1. 使用する ID プロバイダーにディレクトリを追加するには、 **[Identity]\(ID\)**  >  **[Identity Providers]\(ID プロバイダー\)** の順にクリックし、 **[Directories]\(ディレクトリ\)** タブをクリックして、 **[Assign directory]\(ディレクトリの割り当て\)** をクリックします。

    ![Akamai の構成](./media/header-akamai-tutorial/assigndirectory.png)

### <a name="configure-kcd-delegation-for-eaa-walkthrough"></a>EAA チュートリアル用の KCD 委任の構成

#### <a name="step-1-create-an-account"></a>手順 1:アカウントの作成 

1. この例では **EAADelegation** というアカウントを使用します。 これを行うには、 **[Active Directory ユーザーとコンピューター]** スナップインを使用します。

    ![Akamai の構成](./media/header-akamai-tutorial/assigndirectory.png)

    > [!NOTE]
    > このユーザー名は **ID インターセプト名**に基づく特定の形式にする必要があります。 図 1 から、**corpapps.login.go.akamai-access.com** であることがわかります。

1. ユーザーログオン名は `HTTP/corpapps.login.go.akamai-access.com` になります。

    ![Akamai の構成](./media/header-akamai-tutorial/eaadelegation.png)

#### <a name="step-2-configure-the-spn-for-this-account"></a>手順 2:このアカウントの SPN の構成

1. このサンプルに基づき、SPN は次のようになります。

2. setspn -s **Http/corpapps.login.go.akamai-access.com eaadelegation**

    ![Akamai の構成](./media/header-akamai-tutorial/spn.png)

#### <a name="step-3-configure-delegation"></a>手順 3:委任の構成

1. EAADelegation アカウントについては、[委任] タブをクリックします。

    ![Akamai の構成](./media/header-akamai-tutorial/spn.png)

    * [任意の認証プロトコルを使う] を選択します。
    * [追加] をクリックし、Kerberos Web サイトのアプリケーション プール アカウントを追加します。 正しく構成されていれば、自動的に正しい SPN に解決されます。

#### <a name="step-4-create-a-keytab-file-for-akamai-eaa"></a>手順 4:AKAMAI EAA 用の keytab ファイルの作成

1. 一般的な構文を次に示します。

1. ktpass /out ActiveDirectorydomain.keytab  /princ `HTTP/yourloginportalurl@ADDomain.com`  /mapuser serviceaccount@ADdomain.com /pass +rdnPass  /crypto All /ptype KRB5_NT_PRINCIPAL

1. 例の説明は次のとおりです。

    | スニペット | 説明 |
    | - | - |
    | Ktpass /out EAADemo.keytab | // 出力 keytab ファイルの名前 |
    | /princ HTTP/corpapps.login.go.akamai-access.com@superdemo.live | // HTTP/yourIDPName@YourdomainName |
    | /mapuser eaadelegation@superdemo.live | // EAA 委任アカウント |
    | /pass RANDOMPASS | // EAA 委任アカウントのパスワード |
    | /crypto All ptype KRB5_NT_PRINCIPAL | // Akamai EAA のドキュメントを参照してください |
    | | |

1. Ktpass /out EAADemo.keytab  /princ HTTP/corpapps.login.go.akamai-access.com@superdemo.live /mapuser eaadelegation@superdemo.live /pass RANDOMPASS /crypto All ptype KRB5_NT_PRINCIPAL

    ![Akamai の構成](./media/header-akamai-tutorial/administrator.png)

#### <a name="step-5-import-keytab-in-the-akamai-eaa-console"></a>手順 5:Akamai EAA コンソールでの keytab のインポート

1. **[System]\(システム\)**  >  **[Keytabs]\(keytab\)** の順にクリックします。

    ![Akamai の構成](./media/header-akamai-tutorial/keytabs.png)

1. [Keytab Type]\(keytab の種類\) で **[Kerberos Delegation]\(Kerberos 委任\)** を選択します。

    ![Akamai の構成](./media/header-akamai-tutorial/keytabdelegation.png)

1. keytab がデプロイ済みおよび確認済みとして表示されていることを確認します。

    ![Akamai の構成](./media/header-akamai-tutorial/keytabs02.png)

1. ユーザー エクスペリエンス

    ![Akamai の構成](./media/header-akamai-tutorial/enduser03.png)

    ![Akamai の構成](./media/header-akamai-tutorial/enduser04.png)

1. 条件付きアクセス

    ![Akamai の構成](./media/header-akamai-tutorial/conditionalaccess04.png)

    ![Akamai の構成](./media/header-akamai-tutorial/conditionalaccess10.png)

    ![Akamai の構成](./media/header-akamai-tutorial/conditionalaccess11.png)

### <a name="create-akamai-test-user"></a>Akamai のテスト ユーザーの作成

このセクションでは、Akamai で B.Simon というユーザーを作成します。 [Akamai クライアント サポート チーム](https://www.akamai.com/us/en/contact-us/)と連携し、Akamai プラットフォームにユーザーを追加してください。 シングル サインオンを使用する前に、ユーザーを作成し、有効化する必要があります。 

## <a name="test-sso"></a>SSO のテスト

このセクションでは、アクセス パネルを使用して Azure AD のシングル サインオン構成をテストします。

アクセス パネル上で [Akamai] タイルをクリックすると、SSO を設定した Akamai に自動的にサインインします。 アクセス パネルの詳細については、[アクセス パネルの概要](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)に関する記事を参照してください。

## <a name="additional-resources"></a>その他のリソース

- [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Azure Active Directory でのアプリケーション アクセスとシングル サインオンとは](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Azure Active Directory の条件付きアクセスとは](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Azure AD で Akamai を試す](https://aad.portal.azure.com/)
