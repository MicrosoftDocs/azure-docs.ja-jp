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
ms.date: 02/04/2021
ms.author: jeedes
ms.openlocfilehash: 8838e3c92a2c7ccc77794973b3cb8e67128e3c71
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/02/2021
ms.locfileid: "101654822"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-akamai"></a>チュートリアル:Azure Active Directory シングル サインオン (SSO) と Akamai の統合

このチュートリアルでは、Akamai と Azure Active Directory (Azure AD) を統合する方法について説明します。 Azure AD と Akamai を統合すると、次のことができます。

* Akamai にアクセスできるユーザーを Azure AD で制御できます。
* ユーザーが自分の Azure AD アカウントを使用して Akamai に自動的にサインインできるように設定できます。
* 1 つの中央サイト (Azure Portal) で自分のアカウントを管理します。

Azure Active Directory と Akamai Enterprise Application Access の統合により、クラウドまたはオンプレミスでホストされているレガシ アプリケーションにシームレスにアクセスできます。 この統合ソリューションでは、レガシ アプリケーションにアクセスするために [Azure AD 条件付きアクセス](../conditional-access/overview.md)、[Azure AD Identity Protection](../identity-protection/overview-identity-protection.md)、[Azure AD Identity Governance](../governance/identity-governance-overview.md) など、Azure Active Directory の最新機能をすべて活用できます。その際、アプリの変更やエージェントのインストールは不要です。

次の図は、Akamai EAA が安全なハイブリッド アクセスの広範なシナリオに適しているようすを示しています。

![Akamai EAA は安全なハイブリッド アクセスの広範なシナリオに適しています](./media/header-akamai-tutorial/introduction-1.png)

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

Akamai EAA が Azure AD 上で単一のアプリケーションとして構成されます。 管理者はそのアプリケーション上で条件付きアクセス ポリシーを構成することができ、条件が満たされると、ユーザーは Akamai EAA ポータルにアクセスできます。

**長所:** :

* IDP の構成が 1 回だけで済む。

**短所**:

* ユーザーは最終的に 2 つのアプリケーション ポータルを持つことになる。

* すべてのアプリケーションを対象とする、共通する 1 つの条件付きアクセス ポリシー。

![統合シナリオ 1](./media/header-akamai-tutorial/scenario-1.png)

#### <a name="integration-scenario-2"></a>統合シナリオ 2

Akamai EAA アプリケーションが Azure AD ポータル上で個別に設定されます。 管理者はアプリケーションに対して個々の条件付きアクセス ポリシーを構成でき、条件が満たされると、ユーザーは特定のアプリケーションに直接リダイレクトされます。

**長所:** :

* 個々の CA ポリシーを定義できる。

* すべてのアプリが O365 のワッフルと myApps.microsoft.com パネルに表示される。


**短所**:

* 複数の IDP を構成する必要がある。

![統合シナリオ 2](./media/header-akamai-tutorial/scenario-2.png)

## <a name="prerequisites"></a>前提条件

開始するには、次が必要です。

* Azure AD サブスクリプション。 サブスクリプションがない場合は、[無料アカウント](https://azure.microsoft.com/free/)を取得できます。
* Akamai でのシングル サインオン (SSO) が有効なサブスクリプション。

## <a name="scenario-description"></a>シナリオの説明

このチュートリアルでは、テスト環境で Azure AD の SSO を構成してテストします。

- Akamai では、IDP Initiated SSO がサポートされます。

#### <a name="important"></a>重要

以下に示す設定はすべて、**統合シナリオ 1** と **シナリオ 2** で同じです。 **統合シナリオ 2** では、Akamai EAA で個々の IDP を設定する必要があり、URL プロパティはアプリケーション URL を指すように変更する必要があります。

![Akamai Enterprise Application Access の [AZURESSO-SP] の [General]\(全般\) タブのスクリーンショット。 [Authentication configuration]\(認証構成\) の [URL] フィールドが強調表示されている。](./media/header-akamai-tutorial/important.png)

## <a name="add-akamai-from-the-gallery"></a>ギャラリーからの Akamai の追加

Azure AD への Akamai の統合を構成するには、ギャラリーからマネージド SaaS アプリの一覧に Akamai を追加する必要があります。

1. 職場または学校アカウントか、個人の Microsoft アカウントを使用して、Azure portal にサインインします。
1. 左のナビゲーション ウィンドウで **[Azure Active Directory]** サービスを選択します。
1. **[エンタープライズ アプリケーション]** に移動し、 **[すべてのアプリケーション]** を選択します。
1. 新しいアプリケーションを追加するには、 **[新しいアプリケーション]** を選択します。
1. **[ギャラリーから追加する]** セクションで、検索ボックスに「**Akamai**」と入力します。
1. 結果のパネルから **[Akamai]** を選択し、アプリを追加します。 お使いのテナントにアプリが追加されるのを数秒待機します。

## <a name="configure-and-test-azure-ad-sso-for-akamai"></a>Akamai の Azure AD SSO の構成とテスト

**B.Simon** というテスト ユーザーを使用して、Akamai に対する Azure AD SSO を構成してテストします。 SSO が機能するためには、Azure AD ユーザーと Akamai の関連ユーザーとの間にリンク関係を確立する必要があります。

Akamai に対して Azure AD SSO を構成してテストするには、次の手順を実行します。

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

1. Azure portal の **Akamai** アプリケーション統合ページで、 **[管理]** セクションを見つけて、 **[シングル サインオン]** を選択します。
1. **[シングル サインオン方式の選択]** ページで、 **[SAML]** を選択します。
1. **[SAML によるシングル サインオンのセットアップ]** ページで、 **[基本的な SAML 構成]** の鉛筆アイコンをクリックして設定を編集します。

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
1. **[ユーザーの追加]** を選択し、 **[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。
1. **[ユーザーとグループ]** ダイアログの [ユーザー] の一覧から **[B.Simon]** を選択し、画面の下部にある **[選択]** ボタンをクリックします。
1. ユーザーにロールが割り当てられることが想定される場合は、 **[ロールの選択]** ドロップダウンからそれを選択できます。 このアプリに対してロールが設定されていない場合は、[既定のアクセス] ロールが選択されていることを確認します。
1. **[割り当ての追加]** ダイアログで、 **[割り当て]** をクリックします。

## <a name="configure-akamai-sso"></a>Akamai の SSO の構成

### <a name="setting-up-idp"></a>IDP の設定

**AKAMAI EAA IDP の構成**

1. **Akamai Enterprise Application Access** コンソールにサインインします。
1. **Akamai EAA コンソール** で、 **[Identity]\(ID\)**  >  **[Identity Providers]\(ID プロバイダー\)** の順に選択し、 **[Add Identity Provider]\(ID プロバイダーの追加\)** をクリックします。

    ![Akamai EAA コンソールの [Identity Providers]\(ID プロバイダー\) ウィンドウのスクリーンショット。 [Identity]\(ID\) メニューの [Identity Providers]\(ID プロバイダー\) を選択し、[Add Identity Provider]\(ID プロバイダーの追加\) を選択する。](./media/header-akamai-tutorial/configure-1.png)

1. **[Create New Identity Provider]\(新しい ID プロバイダーの作成\)** で、次の手順を実行します。

    ![Akamai EAA コンソールの [Create New Identity Providers]\(新しい ID プロバイダーの作成\) ダイアログのスクリーンショット。](./media/header-akamai-tutorial/configure-2.png)

    a. **一意の名前** を指定します。

    b. **[Third Party SAML]\(サード パーティの SAML\)** を選択し、 **[Create Identity Provider and Configure]\(ID プロバイダーを作成して構成する\)** をクリックします。

### <a name="general-settings"></a>全般設定

1. **[Identity Intercept]\(ID のインターセプト\)** - SP ベース URL の名前を指定します (Azure AD 構成に使用されます)。

    > [!NOTE]
    > カスタム ドメインを使用することもできます (DNS エントリと証明書が必要です)。 この例では、Akamai ドメインを使用します。

1. **[Akamai Cloud Zone]\(Akamai クラウド ゾーン\)** - 適切なクラウド ゾーンを選択します。
1. **[Certificate Validation]\(証明書の検証\)** - Akamai ドキュメントをチェックします (省略可)。

    ![Akamai EAA コンソールの [General]\(全般\) タブのスクリーンショット。[Identity Intercept]\(ID インターセプト\)、[Akamai Cloud Zone]\(Akamai クラウド ゾーン\)、[Certificate Validation]\(証明書の検証\) の各設定が表示されている。](./media/header-akamai-tutorial/configure-3.png)

### <a name="authentication-configuration"></a>認証の構成

1. [URL] - ID インターセプトと同じ URL を指定します (認証後、ユーザーはここにリダイレクトされます)。
2. [Logout URL]\(ログアウト URL\): ログアウト URL を更新します。
3. [Sign SAML Request]\(SAML 要求に署名する\): 既定ではオフです。
4. IDP メタデータ ファイルには、Azure AD コンソールでアプリケーションを追加します。

    ![Akamai EAA コンソールの [Authentication configuration]\(認証構成\) のスクリーンショット。[URL]、[Logout URL]\(ログアウト URL\)、[Sign SAML Request]\(SAML 要求に署名する\)、[IDP Metadata File]\(IDP メタデータ ファイル\) の各設定が表示されている。](./media/header-akamai-tutorial/configure-4.png)

### <a name="session-settings"></a>セッションの設定

設定は既定値のままにします。

![Akamai EAA コンソールの [Session settings]\(セッション設定\) ダイアログのスクリーンショット。](./media/header-akamai-tutorial/session-settings.png)

### <a name="directories"></a>ディレクトリ

ディレクトリの構成はスキップします。

![Akamai EAA コンソールの [Directories]\(ディレクトリ\) タブのスクリーンショット。](./media/header-akamai-tutorial/directories.png)

### <a name="customization-ui"></a>カスタマイズ UI

IDP に対するカスタマイズを追加できます。

![Akamai EAA コンソールの [Customization]\(カスタマイズ\) タブのスクリーンショット。[Customize UI]\(UI のカスタマイズ\)、[Language settings]\(言語設定\)、[Themes]\(テーマ\) の各設定が表示されている。](./media/header-akamai-tutorial/customization.png)

### <a name="advanced-settings"></a>詳細設定

詳細設定をスキップするか、Akamai のドキュメントで詳細を確認してください。

![Akamai EAA コンソールの [Advanced Settings]\(詳細設定\) タブのスクリーンショット。[EAA Client]\(EAA クライアント\)、[Advanced]\(詳細\)、[OIDC to SAML bridging]\(OIDC から SAML へのブリッジ\) の各設定が表示されている。](./media/header-akamai-tutorial/advance-settings.png)

### <a name="deployment"></a>デプロイ

1. [Deploy Identity Provider]\(ID プロバイダーのデプロイ\) をクリックします。

    ![Akamai EAA コンソールの [Deployment]\(デプロイ\) タブのスクリーンショット。[Deploy Identity provider]\(ID プロバイダーのデプロイ\) ボタンが表示されている。](./media/header-akamai-tutorial/deployment.png)

2. デプロイが成功したことを確認します。

### <a name="header-based-authentication"></a>ヘッダー ベースの認証

Akamai ヘッダー ベースの認証

1. アプリケーションの追加ウィザードから **[Custom HTTP]\(カスタム HTTP\)** を選択します。

    ![Akamai EAA コンソールのアプリケーションの追加ウィザードのスクリーンショット。[Access Apps]\(アプリへのアクセス\) セクションに [Custom HTTP]\(カスタム HTTP\) が表示されている。](./media/header-akamai-tutorial/configure-5.png)

2. **[Application Name]\(アプリケーション名\)** と **[Description]\(説明\)** を入力します。

    ![[Custom HTTP App]\(カスタム HTTP アプリ\) ダイアログのスクリーンショット。[Application Name]\(アプリケーション名\) と [Description]\(説明\) の各設定が表示されている。](./media/header-akamai-tutorial/configure-6.png)

    ![Akamai EAA コンソールの [General]\(全般\) タブのスクリーンショット。MYHEADERAPP の全般設定が表示されている。](./media/header-akamai-tutorial/configure-7.png)

    ![Akamai EAA コンソールのスクリーンショット。[Certificate]\(証明書\) と [Location]\(場所\) の各設定が表示されている。](./media/header-akamai-tutorial/configure-8.png)

#### <a name="authentication"></a>認証

1. **[Authentication]\(認証\)** タブを選択します。

    ![Akamai EAA コンソールのスクリーンショット。[Authentication]\(認証\) タブが選択されている。](./media/header-akamai-tutorial/configure-9.png)

2. **ID プロバイダー** を割り当てます。

    ![Akamai EAA コンソールの MYHEADERAPP の [Authentication]\(認証\) タブのスクリーンショット。[Identity provider]\(ID プロバイダー\) が [Azure AD SSO]\(Azure AD SSO\) に設定されている。](./media/header-akamai-tutorial/configure-10.png)

#### <a name="services"></a>サービス

[Save and Go to Authentication]\(保存して認証に移動\) をクリックします。

![Akamai EAA コンソールの MYHEADERAPP の [Services]\(サービス\) タブのスクリーンショット。右下隅に [Save and go to Advanced Settings]\(保存して詳細設定に移動\) ボタンが表示されている。](./media/header-akamai-tutorial/configure-11.png)

#### <a name="advanced-settings"></a>詳細設定

1. **[Customer HTTP Headers]\(カスタマー HTTP ヘッダー\)** で、**カスタマー ヘッダー** と **SAML 属性** を指定します。

    ![Akamai EAA コンソールの [Advanced Settings]\(詳細設定\) タブのスクリーンショット。[Authentication]\(認証\) の [SSO Logged URL]\(SSO ログ URL\) フィールドが強調表示されている。](./media/header-akamai-tutorial/configure-12.png)

1. **[Save and go to Deployment]\(保存してデプロイに移動\)** ボタンをクリックします。

    ![Akamai EAA コンソールの [Advanced Settings]\(詳細設定\) タブのスクリーンショット。右下隅に [Save and go to Deployment]\(保存してデプロイに移動\) ボタンが表示されている。](./media/header-akamai-tutorial/configure-13.png)

#### <a name="deploy-the-application"></a>アプリケーションのデプロイ

1. **[Deploy Application]\(アプリケーションのデプロイ\)** ボタンをクリックします。

    ![Akamai EAA コンソールの [Deployment]\(デプロイ\) タブのスクリーンショット。[Deploy Application]\(アプリケーションのデプロイ\) ボタンが表示されている。](./media/header-akamai-tutorial/configure-14.png)

1. アプリケーションが正しくデプロイされたことを確認します。

    ![Akamai EAA コンソールの [Deployment]\(デプロイ\) タブのスクリーンショット。[Application status]\(アプリケーションの状態\) に、"Application Successfully Deployed (アプリケーションが正常にデプロイされました)" というメッセージが表示されている。](./media/header-akamai-tutorial/configure-15.png)

1. エンドユーザー エクスペリエンス。

    ![myapps.microsoft.com の開始画面のスクリーンショット。背景画像と [サインイン] ダイアログが表示されている。](./media/header-akamai-tutorial/end-user-1.png)

    ![[アプリ] ウィンドウの一部を表示するスクリーンショット。[アドイン]、[HRWEB]、[Akamai - CorpApps]、[経費]、[グループ]、[アクセス レビュー] のアイコンが表示されている。 ](./media/header-akamai-tutorial/end-user-2.png)

1. 条件付きアクセス。

    !["サインイン要求を承認。お使いのモバイル デバイスに通知を送信しました。続行するには、応答してください" というメッセージのスクリーンショット。 We've sent a notification to your mobile device. Please respond to continue.](./media/header-akamai-tutorial/conditional-access-1.png)

    ![MyHeaderApp のアイコンを表示する [アプリケーション] 画面のスクリーンショット。](./media/header-akamai-tutorial/conditional-access-2.png)

#### <a name="remote-desktop"></a>リモート デスクトップ

1. ADD Applications ウィザードから **[RDP]** を選択します。

    ![Akamai EAA コンソールのアプリケーションの追加ウィザードのスクリーンショット。[Access Apps]\(アプリへのアクセス\) セクションの一連のアプリの中に [RDP] が表示されている。](./media/header-akamai-tutorial/configure-16.png)

1. **[Application Name]\(アプリケーション名\)** と **[Description]\(説明\)** を入力します。

    ![[RDP App]\(RDP アプリ\) ダイアログのスクリーンショット。[Application Name]\(アプリケーション名\) と [Description]\(説明\) の各設定が表示されている。](./media/header-akamai-tutorial/configure-17.png)

    ![Akamai EAA コンソールの [General]\(全般\) タブのスクリーンショット。SECRETRDPAPP の [Application identity]\(アプリケーション ID\) 設定が表示されている。](./media/header-akamai-tutorial/configure-18.png)

1. このサービスを提供するコネクタを指定します。

    ![Akamai EAA コンソールのスクリーンショット。[Certificate]\(証明書\) と [Location]\(場所\) の各設定が表示されている。 関連するコネクタが USWST-CON1 に設定されている。](./media/header-akamai-tutorial/configure-19.png)

#### <a name="authentication"></a>認証

**[Save and go to Services]\(保存してサービスに移動\)** をクリックします。

![Akamai EAA コンソールの SECRETRDPAPP の [Authentication]\(認証\) タブのスクリーンショット。右下隅に [Save and go to Services]\(保存してサービスに移動\) ボタンが表示されている。](./media/header-akamai-tutorial/configure-20.png)

#### <a name="services"></a>サービス

**[Save and go to Advanced Settings]\(保存して詳細設定に移動\)** をクリックします。

![Akamai EAA コンソールの SECRETRDPAPP の [Services]\(サービス\) タブのスクリーンショット。右下隅に [Save and go to Advanced Settings]\(保存して詳細設定に移動\) ボタンが表示されている。](./media/header-akamai-tutorial/configure-21.png)

#### <a name="advanced-settings"></a>詳細設定

1. **[Save and go to Deployment]\(保存してデプロイに移動\)** をクリックします。

    ![Akamai EAA コンソールの SECRETRDPAPP の [Advanced Settings]\(詳細設定\) タブのスクリーンショット。[Remote desktop configuration]\(リモート デスクトップ構成\) の設定が表示されている。](./media/header-akamai-tutorial/configure-22.png)

    ![Akamai EAA コンソールの SECRETRDPAPP の [Advanced Settings]\(詳細設定\) タブのスクリーンショット。[Authentication]\(認証\) と [Health check configuration]\(正常性チェック構成\) の各設定が表示されている。](./media/header-akamai-tutorial/configure-23.png)

    ![Akamai EAA コンソールの SECRETRDPAPP の [Custom HTTP headers]\(カスタム HTTP ヘッダー\) 設定のスクリーンショット。右下隅に [Save and go to Deployment]\(保存してデプロイに移動\) ボタンが表示されている。](./media/header-akamai-tutorial/configure-24.png)

1. エンドユーザー エクスペリエンス

    ![[myapps.microsoft.com] ウィンドウのスクリーンショット。背景画像と [サインイン] ダイアログが表示されている。](./media/header-akamai-tutorial/end-user-3.png)

    ![myapps.microsoft.com の [アプリ] ウィンドウのスクリーンショット。[アドイン]、[HRWEB]、[Akamai - CorpApps]、[経費]、[グループ]、[アクセス レビュー] のアイコンが表示されている。](./media/header-akamai-tutorial/end-user-2.png)

1. 条件付きアクセス

    !["サインイン要求を承認。お使いのモバイル デバイスに通知を送信しました。続行するには、応答してください" という条件付きアクセス メッセージのスクリーンショット。 We've sent a notification to your mobile device. Please respond to continue.](./media/header-akamai-tutorial/conditional-access-4.png)

    ![MyHeaderApp と SecretRDPApp のアイコンを表示する [アプリケーション] 画面のスクリーンショット。](./media/header-akamai-tutorial/conditional-access-5.png)

    ![Windows Server 2012 RS 画面のスクリーンショット。汎用的なユーザー アイコンが表示されている。 administrator、user0、user1 がサインイン済みであることを示すそれらのアイコン。](./media/header-akamai-tutorial/conditional-access-6.png)

1. または、RDP アプリケーションの URL を直接入力することもできます。

#### <a name="ssh"></a>SSH

1. [Add Applications]\(アプリケーションの追加\) に移動し、 **[SSH]** を選択します。

    ![Akamai EAA コンソールのアプリケーションの追加ウィザードのスクリーンショット。[Access Apps]\(アプリへのアクセス\) セクションの一連のアプリの中に [SSH] が表示されている。](./media/header-akamai-tutorial/configure-25.png)

1. **[Application Name]\(アプリケーション名\)** と **[Description]\(説明\)** を入力します。

    ![[SSH App]\(SSH アプリ\) ダイアログのスクリーンショット。[Application Name]\(アプリケーション名\) と [Description]\(説明\) の各設定が表示されている。](./media/header-akamai-tutorial/configure-26.png)

1. アプリケーション ID を構成します。

    ![Akamai EAA コンソールの [General]\(全般\) タブのスクリーンショット。SSH-SECURE の [Application identity]\(アプリケーション ID\) 設定が表示されている。](./media/header-akamai-tutorial/configure-27.png)

    a. 名前と説明を指定します。

    b. アプリケーション サーバーの IP (または FQDN) と SSH のポートを指定します。

    c. SSH ユーザー名とパスフレーズを指定します (Akamai EAA を確認してください)。

    d. 外部ホスト名を指定します。

    e. コネクタの場所を指定し、コネクタを選択します。

#### <a name="authentication"></a>認証

**[Save and go to Services]\(保存してサービスに移動\)** をクリックします。

![Akamai EAA コンソールの SSH-SECURE の [Authentication]\(認証\) タブのスクリーンショット。右下隅に [Save and go to Services]\(保存してサービスに移動\) ボタンが表示されている。](./media/header-akamai-tutorial/configure-28.png)

#### <a name="services"></a>サービス

**[Save and go to Advanced Settings]\(保存して詳細設定に移動\)** をクリックします。

![Akamai EAA コンソールの SSH-SECURE の [Services]\(サービス\) タブのスクリーンショット。右下隅に [Save and go to Advanced Settings]\(保存して詳細設定に移動\) ボタンが表示されている。](./media/header-akamai-tutorial/configure-29.png)

#### <a name="advanced-settings"></a>詳細設定

[Save and go to Deployment]\(保存してデプロイに移動\) をクリックします。

![Akamai EAA コンソールの SSH-SECURE の [Advanced Settings]\(詳細設定\) タブのスクリーンショット。[Authentication]\(認証\) と [Health check configuration]\(正常性チェック構成\) の各設定が表示されている。](./media/header-akamai-tutorial/configure-30.png)

![Akamai EAA コンソールの SSH-SECURE の [Custom HTTP headers]\(カスタム HTTP ヘッダー\) のスクリーンショット。右下隅に [Save and go to Deployment]\(保存してデプロイに移動\) ボタンが表示されている。](./media/header-akamai-tutorial/configure-31.png)

#### <a name="deployment"></a>デプロイ

1. **[Deploy application]\(アプリケーションのデプロイ\)** をクリックします。

    ![Akamai EAA コンソールの SSH-SECURE の [Deployment]\(デプロイ\) タブのスクリーンショット。[Deploy Application]\(アプリケーションのデプロイ\) ボタンが表示されている。](./media/header-akamai-tutorial/configure-32.png)

1. エンドユーザー エクスペリエンス

    ![[myapps.microsoft.com] ウィンドウの [サインイン] ダイアログのスクリーンショット。](./media/header-akamai-tutorial/end-user-3.png)

    ![myapps.microsoft.com の [アプリ] ウィンドウのスクリーンショット。[アドイン]、[HRWEB]、[Akamai - CorpApps]、[経費]、[グループ]、[アクセス レビュー] のアイコンが表示されている。](./media/header-akamai-tutorial/end-user-4.png)

1. 条件付きアクセス

    !["サインイン要求を承認。お使いのモバイル デバイスに通知を送信しました。続行するには、応答してください" というメッセージのスクリーンショット。 We've sent a notification to your mobile device. Please respond to continue.](./media/header-akamai-tutorial/conditional-access-4.png)

    ![MyHeaderApp、SSH Secure、SecretRDPApp のアイコンを表示する [アプリケーション] 画面のスクリーンショット。](./media/header-akamai-tutorial/conditional-access-7.png)

    ![ssh-secure-go.akamai-access.com のコマンド ウィンドウのスクリーンショット。[Password]\(パスワード\) プロンプトが表示されている。](./media/header-akamai-tutorial/conditional-access-8.png)

    ![ssh-secure-go.akamai-access.com のコマンド ウィンドウのスクリーンショット。アプリケーションに関する情報のほか、コマンドのプロンプトが表示されている。](./media/header-akamai-tutorial/conditional-access-9.png)

### <a name="kerberos-authentication"></a>Kerberos 認証

次の例では、内部 Web サーバー <code>http://frp-app1.superdemo.live</code> を発行し、KCD を使用して SSO を有効化します。

#### <a name="general-tab"></a>全般タブ

![Akamai EAA コンソールの MYKERBOROSAPP の [General]\(全般\) タブのスクリーンショット。](./media/header-akamai-tutorial/general-tab.png)

#### <a name="authentication-tab"></a>[認証] タブ

ID プロバイダーを割り当てます。

![Akamai EAA コンソールの MYKERBOROSAPP の [Authentication]\(認証\) タブのスクリーンショット。[Identity provider]\(ID プロバイダー\) が [Azure AD SSO]\(Azure AD SSO\) に設定されている。](./media/header-akamai-tutorial/authentication-tab.png)

#### <a name="services-tab"></a>[Services]\(サービス\) タブ

![Akamai EAA コンソールの MYKERBOROSAPP の [Services]\(サービス\) タブのスクリーンショット。](./media/header-akamai-tutorial/services-tab.png)

#### <a name="advanced-settings"></a>詳細設定

![Akamai EAA コンソールの MYKERBOROSAPP の [Advanced Settings]\(詳細設定\) タブのスクリーンショット。[Related Applications]\(関連アプリケーション\) と [Authentication]\(認証\) の各設定が表示されている。](./media/header-akamai-tutorial/advance-settings-2.png)

> [!NOTE]
> このデモでは、Web サーバーの SPN は SPN@Domain 形式 (例: `HTTP/frp-app1.superdemo.live@SUPERDEMO.LIVE`) にする必要があります。 残りの設定は既定値のままにします。

#### <a name="deployment-tab"></a>[Deployment]\(デプロイ\) タブ

![Akamai EAA コンソールの MYKERBOROSAPP の [Deployment]\(デプロイ\) タブのスクリーンショット。[Deploy Application]\(アプリケーションのデプロイ\) ボタンが表示されている。](./media/header-akamai-tutorial/deployment-tab.png)

#### <a name="adding-directory"></a>ディレクトリの追加

1. ドロップダウン リストから **[AD]** を選択します。

    ![Akamai EAA コンソールの [Directories]\(ディレクトリ\) ウィンドウのスクリーンショット。[Create New Directory]\(新しいディレクトリの作成\) ダイアログが表示され、[Directory Type]\(ディレクトリ タイプ\) のボックスの一覧では [AD] が選択されている。](./media/header-akamai-tutorial/configure-33.png)

1. 必要なデータを入力します。

    ![Akamai EAA コンソールの [SUPERDEMOLIVE] ウィンドウのスクリーンショット。[DirectoryName]\(ディレクトリ名\)、[Directory Service]\(ディレクトリ サービス\)、[Connector]\(コネクタ\)、[Attribute mapping]\(属性マッピング\) の各設定が表示されている。](./media/header-akamai-tutorial/configure-34.png)

1. ディレクトリの作成を確認します。

    ![Akamai EAA コンソールの [Directories]\(ディレクトリ\) ウィンドウのスクリーンショット。ディレクトリ superdemo.live が追加されていることがわかる。](./media/header-akamai-tutorial/directory-domain.png)

1. アクセスを必要とするグループまたは OU を追加します。

    ![ディレクトリ superdemo.live の設定のスクリーンショット。 グループまたは OU を追加するときに選択するアイコンが強調表示されている。](./media/header-akamai-tutorial/add-group.png)

1. 以下では、グループが EAAGroup と呼ばれ、1 名のメンバーが含まれています。

    ![Akamai EAA コンソールの [GROUPS ON SUPERDEMOLIVE DIRECTORY]\(SUPERDEMOLIVE ディレクトリ上のグループ\) ウィンドウのスクリーンショット。 1 ユーザーを含んだ EAAGroup が [Groups]\(グループ\) に表示されている。](./media/header-akamai-tutorial/eaagroup.png)

1. 使用する ID プロバイダーにディレクトリを追加するには、 **[Identity]\(ID\)**  >  **[Identity Providers]\(ID プロバイダー\)** の順にクリックし、 **[Directories]\(ディレクトリ\)** タブをクリックして、 **[Assign directory]\(ディレクトリの割り当て\)** をクリックします。

    ![Akamai EAA コンソールの Azure AD SSO の [Directories]\(ディレクトリ\) タブのスクリーンショット。[Currently assigned directories]\(現在割り当てられているディレクトリ\) の一覧に superdemo.live が表示されている。](./media/header-akamai-tutorial/assign-directory.png)

### <a name="configure-kcd-delegation-for-eaa-walkthrough"></a>EAA チュートリアル用の KCD 委任の構成

#### <a name="step-1-create-an-account"></a>手順 1:アカウントの作成 

1. この例では **EAADelegation** というアカウントを使用します。 これを行うには、 **[Active Directory ユーザーとコンピューター]** スナップインを使用します。

    ![Akamai EAA コンソールの Azure AD SSO の [Directories]\(ディレクトリ\) タブのスクリーンショット。 [Currently assigned directories]\(現在割り当てられているディレクトリ\) の一覧に、ディレクトリ superdemo.live が表示されている。](./media/header-akamai-tutorial/assign-directory.png)

    > [!NOTE]
    > このユーザー名は **ID インターセプト名** に基づく特定の形式にする必要があります。 図 1 から、**corpapps.login.go.akamai-access.com** であることがわかります。

1. ユーザーログオン名は `HTTP/corpapps.login.go.akamai-access.com` になります。

    ![[EAADelegation Properties]\(EAADelegation のプロパティ\) を示すスクリーンショット。[名] が "EAADelegation" に、[ユーザー ログオン名] が "HTTP/corpapps.login.go.akamai-access.com" に設定されている。](./media/header-akamai-tutorial/eaadelegation.png)

#### <a name="step-2-configure-the-spn-for-this-account"></a>手順 2:このアカウントの SPN の構成

1. このサンプルに基づき、SPN は次のようになります。

2. setspn -s **Http/corpapps.login.go.akamai-access.com eaadelegation**

    ![管理者コマンド プロンプトのスクリーンショット。コマンド setspn -s Http/corpapps.login.go.akamai-access.com eaadelegation の結果が表示されている。](./media/header-akamai-tutorial/spn.png)

#### <a name="step-3-configure-delegation"></a>手順 3:委任の構成

1. EAADelegation アカウントについては、[委任] タブをクリックします。

    ![管理者コマンド プロンプトのスクリーンショット。SPN を構成するためのコマンドが表示されている。](./media/header-akamai-tutorial/spn.png)

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

    ![管理者コマンド プロンプトのスクリーンショット。AKAMAI EAA の Keytab ファイルを作成するためのコマンドの結果が表示されている。](./media/header-akamai-tutorial/administrator.png)

#### <a name="step-5-import-keytab-in-the-akamai-eaa-console"></a>手順 5:Akamai EAA コンソールでの keytab のインポート

1. **[System]\(システム\)**  >  **[Keytabs]\(keytab\)** の順にクリックします。

    ![Akamai EAA コンソールのスクリーンショット。[System]\(システム\) メニューから [Keytabs]\(keytab\) を選択したところ。](./media/header-akamai-tutorial/keytabs.png)

1. [Keytab Type]\(keytab の種類\) で **[Kerberos Delegation]\(Kerberos 委任\)** を選択します。

    ![Akamai EAA コンソールの [EAAKEYTAB] 画面のスクリーンショット。keytab の設定が表示されている。 [Keytab Type]\(keytab の種類\) が [Kerberos Delegation]\(Kerberos 委任\) に選択されている。](./media/header-akamai-tutorial/keytab-delegation.png)

1. keytab がデプロイ済みおよび確認済みとして表示されていることを確認します。

    ![Akamai EAA コンソールの [KEYTABS] 画面のスクリーンショット。[Keytab deployed and verified]\(keytab がデプロイ済みおよび確認済み\) として EAA keytab が表示されている。](./media/header-akamai-tutorial/keytabs-2.png)

1. ユーザー エクスペリエンス

    ![myapps.microsoft.com の [サインイン] ダイアログのスクリーンショット。 ](./media/header-akamai-tutorial/end-user-3.png)

    ![myapps.microsoft.com の [アプリ] ウィンドウのスクリーンショット。アプリのアイコンが表示されている。](./media/header-akamai-tutorial/end-user-4.png)

1. 条件付きアクセス

    !["サインイン要求を承認" メッセージのスクリーンショット。 メッセージ。](./media/header-akamai-tutorial/conditional-access-4.png)

    ![MyHeaderApp、SSH Secure、SecretRDPApp、myKerberosApp のアイコンを表示する [アプリケーション] 画面のスクリーンショット。](./media/header-akamai-tutorial/conditional-access-10.png)

    ![myKerberosApp のスプラッシュ スクリーンのスクリーンショット。 背景画像に "Welcome superdemo\user1 (ようこそ、superdemo\user1 さん)" というメッセージが表示されている。](./media/header-akamai-tutorial/conditional-access-11.png)

### <a name="create-akamai-test-user"></a>Akamai のテスト ユーザーの作成

このセクションでは、Akamai で B.Simon というユーザーを作成します。 [Akamai クライアント サポート チーム](https://www.akamai.com/us/en/contact-us/)と連携し、Akamai プラットフォームにユーザーを追加してください。 シングル サインオンを使用する前に、ユーザーを作成し、有効化する必要があります。 

## <a name="test-sso"></a>SSO のテスト

このセクションでは、次のオプションを使用して Azure AD のシングル サインオン構成をテストします。

* Azure portal で [このアプリケーションをテストします] をクリックすると、SSO を設定した Akamai に自動的にサインインされます。

* Microsoft マイ アプリを使用することができます。 マイ アプリで [Akamai] タイルをクリックすると、SSO を設定した Akamai に自動的にサインインされます。 マイ アプリの詳細については、[マイ アプリの概要](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)に関するページを参照してください。

## <a name="next-steps"></a>次のステップ

Akamai を構成したら、組織の機密データを流出と侵入からリアルタイムで保護するセッション制御を適用することができます。 セッション制御は、条件付きアクセスを拡張したものです。 [Microsoft Cloud App Security でセッション制御を強制する方法](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app)をご覧ください。