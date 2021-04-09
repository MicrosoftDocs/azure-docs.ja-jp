---
title: チュートリアル:Azure Active Directory シングル サインオン (SSO) と Slack の統合 | Microsoft Docs
description: Azure Active Directory とSlack の間にシングル サインオンを構成する方法について説明します。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 12/28/2020
ms.author: jeedes
ms.openlocfilehash: 6e2428967b8e3b4c677752955ea743c5b7d144e5
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "98729638"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-slack"></a>チュートリアル:Azure Active Directory シングル サインオン (SSO) と Slack の統合

このチュートリアルでは、Slack と Azure Active Directory (Azure AD) を統合する方法について説明します。 Azure AD と Slack を統合すると、次のことができます。

* Slack にアクセスする Azure AD ユーザーを制御できます。
* ユーザーが自分の Azure AD アカウントを使用して Slack に自動的にサインインするように設定できます。
* 1 つの中央サイト (Azure Portal) で自分のアカウントを管理します。

## <a name="prerequisites"></a>前提条件

開始するには、次が必要です。

* Azure AD サブスクリプション。 サブスクリプションがない場合は、[無料アカウント](https://azure.microsoft.com/free/)を取得できます。
* Slack でのシングル サインオン (SSO) が有効なサブスクリプション。

> [!NOTE]
> 1 つのテナント内の複数の Slack インスタンスと統合する必要がある場合は、各アプリケーションの識別子を変数にすることができます。

> [!NOTE]
> この統合は、Azure AD 米国政府クラウド環境から利用することもできます。 このアプリケーションは、Azure AD 米国政府クラウドのアプリケーション ギャラリーにあります。パブリック クラウドの場合と同じように構成してください。

## <a name="scenario-description"></a>シナリオの説明

このチュートリアルでは、テスト環境で Azure AD の SSO を構成してテストします。

* Slack では、**SP** によって開始される SSO がサポートされます
* Slack では、**Just-In-Time** ユーザー プロビジョニングがサポートされます
* Slack では、[**自動化された** ユーザー プロビジョニング](./slack-provisioning-tutorial.md)がサポートされます

> [!NOTE]
> このアプリケーションの識別子は固定文字列値であるため、1 つのテナントで構成できるインスタンスは 1 つだけです。

## <a name="adding-slack-from-the-gallery"></a>ギャラリーからの Slack の追加

Azure AD への Slack の統合を構成するには、ギャラリーから管理対象 SaaS アプリの一覧に Slack を追加する必要があります。

1. 職場または学校アカウントか、個人の Microsoft アカウントを使用して、Azure portal にサインインします。
1. 左のナビゲーション ウィンドウで **[Azure Active Directory]** サービスを選択します。
1. **[エンタープライズ アプリケーション]** に移動し、 **[すべてのアプリケーション]** を選択します。
1. 新しいアプリケーションを追加するには、 **[新しいアプリケーション]** を選択します。
1. **[ギャラリーから追加する]** セクションで、検索ボックスに、「**Slack**」と入力します。
1. 結果のパネルから **[Slack]** を選択し、アプリを追加します。 お使いのテナントにアプリが追加されるのを数秒待機します。

## <a name="configure-and-test-azure-ad-sso-for-slack"></a>Slack の Azure AD SSO の構成とテスト

**B.Simon** というテスト ユーザーを使用して、Slack に対する Azure AD SSO を構成してテストします。 SSO を機能させるために、Azure AD ユーザーと Slack の関連ユーザーとの間にリンク関係を確立する必要があります。

Slack に対して Azure AD SSO を構成してテストするには、次の手順を行います。

1. **[Azure AD SSO の構成](#configure-azure-ad-sso)** - ユーザーがこの機能を使用できるようにします。
    1. **[Azure AD のテスト ユーザーの作成](#create-an-azure-ad-test-user)** - B.Simon で Azure AD のシングル サインオンをテストします。
    1. **[Azure AD テスト ユーザーの割り当て](#assign-the-azure-ad-test-user)** - B.Simon が Azure AD シングル サインオンを使用できるようにします。
1. **[Slack の SSO の構成](#configure-slack-sso)** - アプリケーション側でシングル サインオン設定を構成します。
    1. **[Slack のテスト ユーザーの作成](#create-slack-test-user)** - Slack で B.Simon に対応するユーザーを作成し、Azure AD の B.Simon にリンクします。
1. **[SSO のテスト](#test-sso)** - 構成が機能するかどうかを確認します。

### <a name="configure-azure-ad-sso"></a>Azure AD SSO の構成

これらの手順に従って、Azure portal で Azure AD SSO を有効にします。

1. Azure portal の **Slack** アプリケーション統合ページで、 **[管理]** セクションを見つけて、 **[シングル サインオン]** を選択します。
1. **[シングル サインオン方式の選択]** ページで、 **[SAML]** を選択します。
1. **[SAML でシングル サインオンをセットアップします]** ページで、 **[基本的な SAML 構成]** の編集 (ペン) アイコンをクリックして設定を編集します。

   ![基本的な SAML 構成を編集する](common/edit-urls.png)

1. **[基本的な SAML 構成]** セクションで、次のフィールドの値を入力します。

    a. **[サインオン URL]** ボックスに、次のパターンを使用して URL を入力します。`https://<DOMAIN NAME>.slack.com/sso/saml/start`

    b. **[識別子 (エンティティ ID)]** ボックスに `https://slack.com` という URL を入力します。
    
    c. **[応答 URL]** に、次の URL パターンのいずれかを入力します。
    
    | [応答 URL]|
    |----------|
    | `https://<DOMAIN NAME>.slack.com/sso/saml` |
    | `https://<DOMAIN NAME>.enterprise.slack.com/sso/saml` |

    > [!NOTE]
    > これらは実際の値ではありません。 実際のサインオン URL および応答 URL でこれらの値を更新する必要があります。 値を取得するには、[Slack クライアント サポート チーム](https://slack.com/help/contact)にお問い合わせください。 Azure portal の **[基本的な SAML 構成]** セクションに示されているパターンを参照することもできます。

    > [!NOTE]
    > テナントと統合する必要がある Slack インスタンスが複数ある場合は、 **[識別子 (エンティティ ID)]** の値を変数にすることができます。 `https://<DOMAIN NAME>.slack.com` というパターンを使用します。 このシナリオでは、同じ値を使用して、Slack の別の設定と組み合わせる必要もあります。

1. Slack アプリケーションでは、特定の形式の SAML アサーションを使用するため、カスタム属性マッピングを SAML トークン属性の構成に追加する必要があります。 次のスクリーンショットには、既定の属性一覧が示されています。

    ![image](common/edit-attribute.png)

1. その他に、Slack アプリケーションでは、いくつかの属性が SAML 応答で返されることが想定されています。それらの属性を次に示します。 これらの属性も値が事前に設定されますが、要件に従ってそれらの値を確認することができます。 また、`email` 属性も追加する必要があります。 ユーザーがメール アドレスを持っていない場合は、**emailaddress** を **user.userprincipalname** にマップし、**email** を **user.userprincipalname** にマップします。

    | 名前 | ソース属性 |
    | -----|---------|
    | emailaddress | user.userprincipalname |
    | email | user.userprincipalname |

   > [!NOTE]
   > サービス プロバイダー (SP) の構成を設定するには、SAML 構成ページの **[詳細オプション]** の横にある **[展開]** をクリックする必要があります。 **[Service Provider Issuer]\(サービス プロバイダーの発行者\)** ボックスに、ワークスペースの URL を入力します。 既定値は slack.com です。 

1. **[SAML でシングル サインオンをセットアップします]** ページの **[SAML 署名証明書]** セクションで、 **[証明書 (Base64)]** を見つけて、 **[ダウンロード]** を選択し、証明書をダウンロードして、お使いのコンピューターに保存します。

    ![証明書のダウンロードのリンク](common/certificatebase64.png)

1. **[Slack のセットアップ]** セクションで、要件に基づいて適切な URL をコピーします。

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

このセクションでは、B.Simon に Slack へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

1. Azure portal で **[エンタープライズ アプリケーション]** を選択し、 **[すべてのアプリケーション]** を選択します。
1. アプリケーションの一覧で **[Slack]** を選択します。
1. アプリの概要ページで、 **[管理]** セクションを見つけて、 **[ユーザーとグループ]** を選択します。

1. **[ユーザーの追加]** を選択し、 **[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。

1. **[ユーザーとグループ]** ダイアログの [ユーザー] の一覧から **[B.Simon]** を選択し、画面の下部にある **[選択]** ボタンをクリックします。
1. ユーザーにロールが割り当てられることが想定される場合は、 **[ロールの選択]** ドロップダウンからそれを選択できます。 このアプリに対してロールが設定されていない場合は、[既定のアクセス] ロールが選択されていることを確認します。
1. **[割り当ての追加]** ダイアログで、 **[割り当て]** をクリックします。

## <a name="configure-slack-sso"></a>Slack の SSO の構成

1. Slack 内での構成を自動化するには、 **[拡張機能のインストール]** をクリックして **My Apps Secure Sign-in ブラウザー拡張機能** をインストールする必要があります。

    ![マイ アプリの拡張機能](common/install-myappssecure-extension.png)

2. ブラウザーに拡張機能を追加した後、 **[Slack のセットアップ]** をクリックすると、Slack アプリケーションに移動します。 そこから、管理者の資格情報を入力して Slack にサインインします。 ブラウザー拡張機能によりアプリケーションが自動的に構成され、手順 3 ～ 6 が自動化されます。

    ![セットアップの構成](common/setup-sso.png)

3. Slack を手動でセットアップする場合は、別の Web ブラウザー ウィンドウで、Slack 企業サイトに管理者としてサインインします。

2. **[Microsoft Azure AD]** 、 **[Team Settings]** の順に選択します。

     ![[Microsoft Azure AD] でのシングル サインオンの構成](./media/slack-tutorial/tutorial-slack-team-settings.png)

3. **[Team Settings]** セクションで、 **[Authentication]** タブをクリックし、 **[Change Settings]** をクリックします。

    ![[Team Settings]\(チーム設定\) でのシングル サインオンの構成](./media/slack-tutorial/tutorial-slack-authentication.png)

4. **[SAML Authentication Settings]** ダイアログで、次の手順を実行します。

    ![[SAML Authentication Settings]\(SAML 認証設定\) でのシングル サインオンの構成](./media/slack-tutorial/tutorial-slack-save-authentication.png)

    a.  **[SAML 2.0 Endpoint (HTTP)]\(SAML 2.0 エンドポイント (HTTP)\)** テキスト ボックスに、Azure portal からコピーした **ログイン URL** の値を貼り付けます。

    b.  **[Identity Provider Issuer]\(ID プロバイダー発行者\)** テキスト ボックスに、Azure portal からコピーした **Azure AD 識別子** の値を貼り付けます。

    c.  ダウンロードした証明書ファイルをメモ帳で開き、その内容をクリップボードにコピーし、 **[公開証明書]** ボックスに貼り付けます。

    d. 必要に応じて、Slack チームに上記の 3 つの設定を構成します。 設定の詳細については、**Slack の SSO 構成ガイド** をこちらから参照してください。 `https://get.slack.help/hc/articles/220403548-Guide-to-single-sign-on-with-Slack%60`

    ![アプリ側でのシングル サインオンの構成](./media/slack-tutorial/tutorial-slack-expand.png)

    e. **[expand]\(展開\)** をクリックし、 **[Service provider issuer]\(サービス プロバイダー発行者\)** ボックスに「`https://slack.com`」と入力します。

    f.  **[Save Configuration]** をクリックします。
    
    > [!NOTE]
    > Azure AD と統合する必要がある複数の Slack インスタンスがある場合は、Azure アプリケーション **識別子** の設定と組み合わせることができるように、 **[Service provider issuer]\(サービス プロバイダー発行者\)** に `https://<DOMAIN NAME>.slack.com` を設定します。

### <a name="create-slack-test-user"></a>Slack のテスト ユーザーの作成

このセクションの目的は、Slack で B.Simon というユーザーを作成することです。 Slack では、Just-In-Time プロビジョニングがサポートされています。この設定は、既定で有効になっています。 このセクションでは、ユーザー側で必要な操作はありません。 存在しない Slack ユーザーにアクセスしようとすると、新しいユーザーが自動的に作成されます。 Slack はまた、 自動ユーザー プロビジョニングをサポートしています。自動ユーザー プロビジョニングの構成方法について詳しくは、[こちら](slack-provisioning-tutorial.md)をご覧ください。

> [!NOTE]
> ユーザーを手動で作成する必要がある場合は、[Slack のサポート チーム](https://slack.com/help/contact)にお問い合わせください。

> [!NOTE]
> Azure AD Connect はオンプレミスの Active Directory の ID を Azure AD と同期できる同期ツールであり、これらの同期済みユーザーもアプリケーションを他のクラウド ユーザーと同じように使用できます。

## <a name="test-sso"></a>SSO のテスト

このセクションでは、次のオプションを使用して Azure AD のシングル サインオン構成をテストします。

* Azure portal で **[このアプリケーションをテストします]** をクリックします。 これにより、ログイン フローを開始できる Slack のサインオン URL にリダイレクトされます。

* Slack のサインオン URL に直接移動し、そこからログイン フローを開始します。

* Microsoft マイ アプリを使用することができます。 マイ アプリで [Slack] タイルをクリックすると、Slack のサインオン URL にリダイレクトされます。 マイ アプリの詳細については、[マイ アプリの概要](../user-help/my-apps-portal-end-user-access.md)に関するページを参照してください。

## <a name="next-steps"></a>次のステップ

Slack を構成したら、組織の機密データを流出と侵入からリアルタイムで保護するセッション制御を適用することができます。 セッション制御は、条件付きアクセスを拡張したものです。 [Microsoft Cloud App Security でセッション制御を適用する方法](/cloud-app-security/proxy-deployment-aad)をご覧ください。