---
title: チュートリアル:Azure Active Directory シングル サインオン (SSO) と Salesforce の統合 | Microsoft Docs
description: Azure Active Directory と Salesforce の間でシングル サインオンを構成する方法について説明します。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 02/18/2021
ms.author: jeedes
ms.openlocfilehash: 0f800d2d42d0d8815021f1582b04750d87aa5abc
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/02/2021
ms.locfileid: "101651437"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-salesforce"></a>チュートリアル:Azure Active Directory シングル サインオン (SSO) と Salesforce の統合

このチュートリアルでは、Salesforce と Azure Active Directory (Azure AD) を統合する方法について説明します。 Salesforce と Azure AD を統合すると、次のことができます。

* Salesforce にアクセスできるユーザーを Azure AD で制御する。
* ユーザーが自分の Azure AD アカウントを使用して Salesforce に自動的にサインインできるようにする。
* 1 つの中央サイト (Azure Portal) で自分のアカウントを管理します。

## <a name="prerequisites"></a>前提条件

開始するには、次が必要です。

* Azure AD サブスクリプション。 サブスクリプションがない場合は、[無料アカウント](https://azure.microsoft.com/free/)を取得できます。
* シングル サインオン (SSO) が有効な Salesforce のサブスクリプション。

## <a name="scenario-description"></a>シナリオの説明

このチュートリアルでは、テスト環境で Azure AD の SSO を構成してテストします。

* Salesforce では、**SP** Initiated SSO がサポートされます。

* Salesforce では、[**自動化された** ユーザー プロビジョニングとプロビジョニング解除](salesforce-provisioning-tutorial.md) (推奨) がサポートされます。

* Salesforce では、**Just-In-Time** ユーザー プロビジョニングがサポートされます。

* Salesforce Mobile アプリケーションを Azure AD と共に構成して SSO を有効にできるようになりました。 このチュートリアルでは、テスト環境で Azure AD の SSO を構成してテストします。

## <a name="adding-salesforce-from-the-gallery"></a>ギャラリーからの Salesforce の追加

Azure AD への Salesforce の統合を構成するには、ギャラリーから管理対象 SaaS アプリの一覧に Salesforce を追加する必要があります。

1. 職場または学校アカウントか、個人の Microsoft アカウントを使用して、Azure portal にサインインします。
1. 左のナビゲーション ウィンドウで **[Azure Active Directory]** サービスを選択します。
1. **[エンタープライズ アプリケーション]** に移動し、 **[すべてのアプリケーション]** を選択します。
1. 新しいアプリケーションを追加するには、 **[新しいアプリケーション]** を選択します。
1. **[ギャラリーから追加する]** セクションで、検索ボックスに、「**Salesforce**」と入力します。
1. 結果パネルで **[Salesforce]** を選択し、アプリを追加します。 お使いのテナントにアプリが追加されるのを数秒待機します。

## <a name="configure-and-test-azure-ad-sso-for-salesforce"></a>Salesforce の Azure AD SSO の構成とテスト

**B.Simon** というテスト ユーザーを使用して、Salesforce に対する Azure AD SSO を構成してテストします。 SSO を機能させるために、Azure AD ユーザーと Salesforce の関連ユーザーとの間にリンク関係を確立する必要があります。

Salesforce に対して Azure AD SSO を構成してテストするには、次の手順を行います。

1. **[Azure AD SSO の構成](#configure-azure-ad-sso)** - ユーザーがこの機能を使用できるようにします。
    * **[Azure AD のテスト ユーザーの作成](#create-an-azure-ad-test-user)** - B.Simon で Azure AD のシングル サインオンをテストします。
    * **[Azure AD テスト ユーザーの割り当て](#assign-the-azure-ad-test-user)** - B.Simon が Azure AD シングル サインオンを使用できるようにします。
1. **[Salesforce の SSO の構成](#configure-salesforce-sso)** - アプリケーション側でシングル サインオン設定を構成します。
    * **[Salesforce テスト ユーザーの作成](#create-salesforce-test-user)** - Salesforce で B.Simon に対応するユーザーを作成し、Azure AD のこのユーザーにリンクさせます。
1. **[SSO のテスト](#test-sso)** - 構成が機能するかどうかを確認します。

## <a name="configure-azure-ad-sso"></a>Azure AD SSO の構成

これらの手順に従って、Azure portal で Azure AD SSO を有効にします。

1. Azure portal の **Salesforce** アプリケーション統合ページで、 **[管理]** セクションを見つけて、 **[シングル サインオン]** を選択します。
1. **[シングル サインオン方式の選択]** ページで、 **[SAML]** を選択します。
1. **[SAML でシングル サインオンをセットアップします]** ページで、 **[基本的な SAML 構成]** の編集 (ペン) アイコンをクリックして設定を編集します。

   ![基本的な SAML 構成を編集する](common/edit-urls.png)

1. **[基本的な SAML 構成]** セクションで、次のフィールドの値を入力します。
    
    a. **[識別子]** ボックスに、次の形式で値を入力します。

    エンタープライズ アカウント: `https://<subdomain>.my.salesforce.com`

    開発者アカウント: `https://<subdomain>-dev-ed.my.salesforce.com`

    b. **[応答 URL]** ボックスに、次のパターンを使用して値を入力します。

    エンタープライズ アカウント: `https://<subdomain>.my.salesforce.com`

    開発者アカウント: `https://<subdomain>-dev-ed.my.salesforce.com`

    c. **[サインオン URL]** ボックスに、次のパターンを使用して値を入力します。

    エンタープライズ アカウント: `https://<subdomain>.my.salesforce.com`

    開発者アカウント: `https://<subdomain>-dev-ed.my.salesforce.com`

    > [!NOTE]
    > これらは実際の値ではありません。 実際の識別子、応答 URL、サインオン URL でこれらの値を更新します。 これらの値を取得するには、[Salesforce クライアント サポート チーム](https://help.salesforce.com/support)に問い合わせてください。

1. **[SAML でシングル サインオンをセットアップします]** ページの **[SAML 署名証明書]** セクションで、 **[フェデレーション メタデータ XML]** を探して **[ダウンロード]** を選択し、証明書をダウンロードして、お使いのコンピューターに保存します。

    ![証明書のダウンロードのリンク](common/metadataxml.png)

1. **[Salesforce のセットアップ]** セクションで、要件に基づいて適切な URL をコピーします。

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

このセクションでは、B.Simon に Salesforce へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

1. Azure portal で **[エンタープライズ アプリケーション]** を選択し、 **[すべてのアプリケーション]** を選択します。
1. アプリケーションの一覧で **[Salesforce]** を選択します。
1. アプリの概要ページで、 **[管理]** セクションを見つけて、 **[ユーザーとグループ]** を選択します。
1. **[ユーザーの追加]** を選択し、 **[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。
1. **[ユーザーとグループ]** ダイアログの [ユーザー] の一覧から **[B.Simon]** を選択し、画面の下部にある **[選択]** ボタンをクリックします。
1. ユーザーにロールが割り当てられることが想定される場合は、 **[ロールの選択]** ドロップダウンからそれを選択できます。 このアプリに対してロールが設定されていない場合は、[既定のアクセス] ロールが選択されていることを確認します。
1. **[割り当ての追加]** ダイアログで、 **[割り当て]** をクリックします。

## <a name="configure-salesforce-sso"></a>Salesforce SSO の構成

1. Salesforce 内での構成を自動化するには、 **[拡張機能のインストール]** をクリックして **My Apps Secure Sign-in ブラウザー拡張機能** をインストールする必要があります。

    ![マイ アプリの拡張機能](common/install-myappssecure-extension.png)

1. ブラウザーに拡張機能を追加した後、 **[Salesforce のセットアップ]** をクリックすると、Salesforce Single Sign-On アプリケーションに誘導されます。 そこから、管理者の資格情報を入力して Salesforce Single Sign-On にサインインします。 ブラウザー拡張機能によりアプリケーションが自動的に構成され、ステップ 3 から 13 が自動化されます。

    ![セットアップの構成](common/setup-sso.png)

1. Salesforce を手動でセットアップする場合は、新しい Web ブラウザー ウィンドウを開き、管理者として Salesforce 企業サイトにサインインして、次の手順を実行します。

1. ページの右上隅の **設定アイコン** の下の **[Setup]\(セットアップ\)** をクリックします。

    ![シングル サインオンの構成 (設定アイコン)](./media/salesforce-tutorial/configure1.png)

1. ナビゲーション ウィンドウの **[SETTINGS]\(設定\)** まで下へスクロールし、 **[Identity]\(ID\)** をクリックして、関連セクションを展開します。 次に、 **[シングル サインオンの設定]** をクリックします。

    ![シングル サインオンの構成 (設定)](./media/salesforce-tutorial/sf-admin-sso.png)

1. **[シングル サインオンの設定]** ページで、 **[編集]** ボタンをクリックします。

    ![シングル サインオンの構成 (編集)](./media/salesforce-tutorial/sf-admin-sso-edit.png)

    > [!NOTE]
    > Salesforce アカウント用のシングル サインオン設定を有効にできない場合は、必要に応じて、[Salesforce クライアント サポート チーム](https://help.salesforce.com/support)にお問い合わせください。

1. **[SAML 有効]** を選択し、 **[保存]** をクリックします。

    ![シングル サインオンの構成 (SAML 有効)](./media/salesforce-tutorial/sf-enable-saml.png)

1. SAML のシングル サインオン設定を構成するには、 **[New from Metadata File]\(メタデータ ファイルから新規)** をクリックします。

    ![シングル サインオンの構成 (メタデータ ファイルから新規)](./media/salesforce-tutorial/sf-admin-sso-new.png)

1. **[ファイルの選択]** をクリックして、Azure portal からダウンロードしたメタデータ XML ファイルをアップロードし、 **[作成]** をクリックします。

    ![シングル サインオンの構成 (ファイルの選択)](./media/salesforce-tutorial/xmlchoose.png)

1. **[SAML Single Sign-On Settings]\(SAML シングル サインオンの設定\)** ページでは、各フィールドが自動的に設定されます。SAML JIT を使用する場合は、 **[User Provisioning Enabled]\(ユーザー プロビジョニングは有効です\)** を選択し、 **[SAML Identity Type]\(SAML ID の種類\)** として **[Assertion contains the Federation ID from the User object]\(アサーションにはユーザー オブジェクトからのフェデレーション ID が含まれます\)** を選択します。それ以外の場合は、 **[User Provisioning Enabled]\(ユーザー プロビジョニングは有効です\)** の選択を解除し、 **[SAML Identity Type]\(SAML ID の種類\)** として **[Assertion contains the User's Salesforce username]\(アサーションにユーザーの Salesforce ユーザー名が含まれています\)** を選択します。 **[保存]** をクリックします。

    ![シングル サインオンの構成 (ユーザー プロビジョニングは有効です)](./media/salesforce-tutorial/salesforcexml.png)

1. Salesforce の左側のナビゲーション ウィンドウで、 **[Company Settings]\(会社の設定\)** をクリックして関連するセクションを展開し、 **[My Domain]\(マイ ドメイン\)** をクリックします。

    ![シングル サインオンの構成 (マイ ドメイン)](./media/salesforce-tutorial/sf-my-domain.png)

1. **[認証の構成]** セクションまで下へスクロールし、 **[編集]** ボタンをクリックします。

    ![シングル サインオンの構成 (認証の構成)](./media/salesforce-tutorial/sf-edit-auth-config.png)

1. **[Authentication Configuration]\(認証の構成\)** セクションで、SAML SSO 構成の **[Authentication Service]\(認証サービス\)** として **[Login Page]\(ログインページ\)** と **[Azure AD SSO]** をオンにし、 **[Save]\(保存\)** をクリックします。

    ![シングル サインオンの構成 (認証サービス)](./media/salesforce-tutorial/authentication.png)

    > [!NOTE]
    > 複数の認証サービスを選択した場合、ユーザーが Salesforce 環境へのシングル サインオンを開始すると、サインインに使用する認証サービスを選択するよう要求されます。 このメッセージが表示されないようにするには、**その他すべての認証サービスをオフのままに** しておいてください。

### <a name="create-salesforce-test-user"></a>Salesforce テスト ユーザーの作成

このセクションでは、B.Simon というユーザーを Salesforce に作成します。 Salesforce では、Just-In-Time プロビジョニングがサポートされています。この設定は既定で有効になっています。 このセクションでは、ユーザー側で必要な操作はありません。 ユーザーがまだ Salesforce に存在しない場合は、Salesforce にアクセスしようとしたときに新しいユーザーが作成されます。 Salesforce は、自動ユーザー プロビジョニングもまたサポートしています。自動ユーザー プロビジョニングの構成方法について詳しくは、[こちら](salesforce-provisioning-tutorial.md)をご覧ください。

## <a name="test-sso"></a>SSO のテスト

このセクションでは、次のオプションを使用して Azure AD のシングル サインオン構成をテストします。 

* Azure portal で **[このアプリケーションをテストします]** をクリックします。 これにより、ログイン フローを開始できる Salesforce のサインオン URL にリダイレクトされます。 

* Salesforce のサインオン URL に直接移動し、そこからログイン フローを開始します。

* Microsoft マイ アプリを使用することができます。 マイ アプリで [Salesforce] タイルをクリックすると、SSO を設定した Salesforce に自動的にサインインします。 マイ アプリの詳細については、[マイ アプリの概要](../user-help/my-apps-portal-end-user-access.md)に関するページを参照してください。

## <a name="test-sso-for-salesforce-mobile"></a>Salesforce (モバイル) の SSO をテストする

1. Salesforce モバイル アプリケーションを開きます。 サインイン ページで、 **[カスタムドメインを使用]** をクリックします。

    ![Salesforce モバイル アプリの [カスタムドメインを使用]](media/salesforce-tutorial/mobile-app1.png)

1. **[カスタムドメイン]** テキスト ボックスに、登録済みのカスタム ドメイン名を入力し、 **[Continue]\(続行\)** をクリックします。

    ![Salesforce モバイル アプリの [カスタムドメイン]](media/salesforce-tutorial/mobile-app2.png)

1. Azure AD の資格情報を入力して Salesforce アプリケーションにサインインし、 **[Next]\(次へ\)** をクリックします。

    ![Salesforce モバイル アプリの Azure AD の資格情報](media/salesforce-tutorial/mobile-app3.png)

1. 次に示すように、 **[アクセスを許可しますか?]** ページで、 **[許可]** をクリックして Salesforce アプリケーションへのアクセスを許可します。

    ![Salesforce モバイル アプリの [アクセスを許可しますか?]](media/salesforce-tutorial/mobile-app4.png)

1. サインインに成功すると、アプリケーションのホーム ページが表示されます。

    ![Salesforce モバイル アプリのホームページ](media/salesforce-tutorial/mobile-app5.png) ![Salesforce モバイル アプリ](media/salesforce-tutorial/mobile-app6.png)

## <a name="next-steps"></a>次の手順

Salesforce を構成したら、組織の機密データを流出と侵入からリアルタイムで保護するセッション制御を適用することができます。 セッション制御は、条件付きアクセスを拡張したものです。 [Microsoft Cloud App Security でセッション制御を適用する方法](/cloud-app-security/proxy-deployment-aad)をご覧ください。