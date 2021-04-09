---
title: チュートリアル:Azure Active Directory シングル サインオン (SSO) と AskYourTeam の統合 | Microsoft Docs
description: Azure Active Directory と AskYourTeam の間でシングル サインオンを構成する方法について説明します。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 12/18/2020
ms.author: jeedes
ms.openlocfilehash: b239811e9fe49f420b5e9d15afafdcf26832dbf8
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "98735405"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-askyourteam"></a>チュートリアル:Azure Active Directory シングル サインオン (SSO) と AskYourTeam の統合

このチュートリアルでは、AskYourTeam と Azure Active Directory (Azure AD) を統合する方法について説明します。 Azure AD と AskYourTeam を統合すると、次のことができます。

* AskYourTeam にアクセスできるユーザーを Azure AD で制御できます。
* ユーザーが自分の Azure AD アカウントを使用して AskYourTeam に自動的にサインインできるようになります。
* 1 つの中央サイト (Azure Portal) で自分のアカウントを管理します。

## <a name="prerequisites"></a>前提条件

開始するには、次が必要です。

* Azure AD サブスクリプション。 サブスクリプションがない場合は、[無料アカウント](https://azure.microsoft.com/free/)を取得できます。
* AskYourTeam でのシングル サインオン (SSO) が有効なサブスクリプション。

## <a name="scenario-description"></a>シナリオの説明

このチュートリアルでは、テスト環境で Azure AD の SSO を構成してテストします。

* AskYourTeam では、**SP Initiated SSO と IDP Initiated SSO** がサポートされます。

## <a name="adding-askyourteam-from-the-gallery"></a>ギャラリーからの AskYourTeam の追加

Azure AD への AskYourTeam の統合を構成するには、ギャラリーからマネージド SaaS アプリの一覧に AskYourTeam を追加する必要があります。

1. 職場または学校アカウントか、個人の Microsoft アカウントを使用して、Azure portal にサインインします。
1. 左のナビゲーション ウィンドウで **[Azure Active Directory]** サービスを選択します。
1. **[エンタープライズ アプリケーション]** に移動し、 **[すべてのアプリケーション]** を選択します。
1. 新しいアプリケーションを追加するには、 **[新しいアプリケーション]** を選択します。
1. **[ギャラリーから追加する]** セクションで、検索ボックスに「**AskYourTeam**」と入力します。
1. 結果のパネルから **[AskYourTeam]** を選択し、アプリを追加します。 お使いのテナントにアプリが追加されるのを数秒待機します。

## <a name="configure-and-test-azure-ad-sso-for-askyourteam"></a>AskYourTeam の Azure AD SSO の構成とテスト

**B.Simon** というテスト ユーザーを使用して、AskYourTeam に対する Azure AD SSO を構成してテストします。 SSO が機能するためには、Azure AD ユーザーと AskYourTeam の関連ユーザーとの間にリンク関係を確立する必要があります。

AskYourTeam で Azure AD SSO を構成してテストするには、次の手順を実行します。

1. **[Azure AD SSO の構成](#configure-azure-ad-sso)** - ユーザーがこの機能を使用できるようにします。
    1. **[Azure AD のテスト ユーザーの作成](#create-an-azure-ad-test-user)** - B.Simon で Azure AD のシングル サインオンをテストします。
    1. **[Azure AD テスト ユーザーの割り当て](#assign-the-azure-ad-test-user)** - B.Simon が Azure AD シングル サインオンを使用できるようにします。
1. **[AskYourTeam の SSO の構成](#configure-askyourteam-sso)** - アプリケーション側でシングル サインオン設定を構成します。
    1. **[AskYourTeam のテスト ユーザーの作成](#create-askyourteam-test-user)** - AskYourTeam で B.Simon に対応するユーザーを作成し、Azure AD の B.Simon にリンクさせます。
1. **[SSO のテスト](#test-sso)** - 構成が機能するかどうかを確認します。

## <a name="configure-azure-ad-sso"></a>Azure AD SSO の構成

これらの手順に従って、Azure portal で Azure AD SSO を有効にします。

1. Azure portal の **AskYourTeam** アプリケーション統合ページで、 **[管理]** セクションを見つけて、 **[シングル サインオン]** を選択します。
1. **[シングル サインオン方式の選択]** ページで、 **[SAML]** を選択します。
1. **[SAML でシングル サインオンをセットアップします]** ページで、 **[基本的な SAML 構成]** の編集 (ペン) アイコンをクリックして設定を編集します。

   ![基本的な SAML 構成を編集する](common/edit-urls.png)

1. **[基本的な SAML 構成]** セクションで、アプリケーションを **IDP** 開始モードで構成する場合は、次のフィールドの値を入力します。

    **[応答 URL]** ボックスに、`https://<COMPANY>.app.askyourteam.com/users/auth/saml/callback` のパターンを使用して URL を入力します

1. アプリケーションを **SP** 開始モードで構成する場合は、 **[追加の URL を設定します]** をクリックして次の手順を実行します。

    **[サインオン URL]** ボックスに、`https://<COMPANY>.app.askyourteam.com/login` という形式で URL を入力します。

    > [!NOTE]
    > これらは実際の値ではありません。 これらの値は、実際の応答 URL およびサインオン URL の値に更新します。実際の値については、このチュートリアルの後の方で説明します。

1. **[SAML でシングル サインオンをセットアップします]** ページの **[SAML 署名証明書]** セクションで、 **[証明書 (Base64)]** を見つけて、 **[ダウンロード]** を選択し、証明書をダウンロードして、お使いのコンピューターに保存します。

    ![証明書のダウンロードのリンク](common/certificatebase64.png)

1. **[AskYourTeam のセットアップ]** セクションで、要件に基づいて適切な URL をコピーします。

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

このセクションでは、B.Simon に AskYourTeam へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

1. Azure portal で **[エンタープライズ アプリケーション]** を選択し、 **[すべてのアプリケーション]** を選択します。
1. アプリケーションの一覧で **[AskYourTeam]** を選択します。
1. アプリの概要ページで、 **[管理]** セクションを見つけて、 **[ユーザーとグループ]** を選択します。

1. **[ユーザーの追加]** を選択し、 **[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。

1. **[ユーザーとグループ]** ダイアログの [ユーザー] の一覧から **[B.Simon]** を選択し、画面の下部にある **[選択]** ボタンをクリックします。
1. ユーザーにロールが割り当てられることが想定される場合は、 **[ロールの選択]** ドロップダウンからそれを選択できます。 このアプリに対してロールが設定されていない場合は、[既定のアクセス] ロールが選択されていることを確認します。
1. **[割り当ての追加]** ダイアログで、 **[割り当て]** をクリックします。

## <a name="configure-askyourteam-sso"></a>AskYourTeam の SSO の構成

1. AskYourTeam 内での構成を自動化するには、 **[拡張機能のインストール]** をクリックして **My Apps Secure Sign-in ブラウザー拡張機能** をインストールする必要があります。

    ![マイ アプリの拡張機能](common/install-myappssecure-extension.png)

2. ブラウザーに拡張機能を追加した後、 **[AskYourTeam のセットアップ]** をクリックすると、AskYourTeam アプリケーションに移動します。 そこから、管理者の資格情報を入力して AskYourTeam にサインインします。 ブラウザー拡張機能によりアプリケーションが自動的に構成され、手順 3 ～ 7 が自動化されます。

    ![セットアップの構成](common/setup-sso.png)

3. AskYourTeam を手動でセットアップする場合は、別の Web ブラウザー ウィンドウで、AskYourTeam 企業サイトに管理者としてサインインします。

1. **[My Organization]\(マイ組織\)** をクリックします。

    ![[My Organization]\(マイ組織\) リンクを示すスクリーンショット。](./media/askyourteam-tutorial/user1.png)

1. **[統合]** をクリックします。

    ![このスクリーンショットは、[Integrations]\(統合\) リンクを示しています。](./media/askyourteam-tutorial/configure1.png)

1. **[Edit Settings]\(設定の編集\)** をクリックします。

    ![このスクリーンショットは、[Edit Settings]\(設定の編集\) ボタンを備えた [Single Sign-On]\(シングル サインオン\) メッセージを示しています。](./media/askyourteam-tutorial/configure2.png)

1. **[Edit Single Sign-On Integration]\(シングル サインオンの統合の編集\)** ページで、次の手順を実行します。 

    ![このスクリーンショットは、[Edit Single Sign-On Integration]\(シングル サインオンの統合の編集\) を示しています。ここで、この手順の値を入力できます。](./media/askyourteam-tutorial/configure3.png)

    a. **[SAML Single Sign-On Service URL]\(SAML シングル サインオンサービス URL\)** テキスト ボックスに、Azure portal からコピーした **ログインURL** の値を貼り付けます。

    b. **[SAML Entity ID]\(SAML エンティティ ID\)** テキスト ボックスに、Azure portal からコピーした **Azure AD ID** の値を貼り付けます。

    c. **[Sign-Out URL]\(サインアウト URL\)** ボックスに、Azure portal からコピーした **ログアウト URL** の値を貼り付けます。

    d. Azure portal からダウンロードした **証明書 (Base64)** をメモ帳で開き、その内容を **[SAML Signing Certificate - Base64]\(SAML 署名証明書 - Base64\)** テキスト ボックスに貼り付けます。

    > [!NOTE]
    > または、 **[Choose File]\(ファイルの選択\)** オプションをクリックして、**フェデレーション メタデータ XML** ファイルをアップロードすることもできます。

    e. **[Reply URL (Assertion Consumer Service URL)]\(応答 URL (アサーション コンシューマー サービス URL)\)** の値をコピーし、Azure portal の **[基本的な SAML 構成]** セクションの **[応答 URL]** テキスト ボックスに貼り付けます。

    f. **[Sign on URL]\(サインオン URL\)** の値をコピーして、Azure portal の **[基本的な SAML 構成]** セクションの **[サインオン URL]** テキスト ボックスに貼り付けます。

    g. **[保存]** をクリックします。

### <a name="create-askyourteam-test-user"></a>AskYourTeam のテスト ユーザーの作成

1. 別の Web ブラウザー ウィンドウで、管理者として AskYourTeam Web サイトにサインインします。

1. **[My Organization]\(マイ組織\)** をクリックします。

    ![このタスクを開始するための [My Organization]\(マイ組織\) リンクを示すスクリーンショット。](./media/askyourteam-tutorial/user1.png)

1. **[Users]\(ユーザー\)** をクリックし、 **[New User]\(新しいユーザー\)** を選択します。

    ![このスクリーンショットは、[New User]\(新しいユーザー\) が選択された状態の [Users]\(ユーザー\) リンクを示しています。](./media/askyourteam-tutorial/user2.png)

1. **[New user]\(新しいユーザー\)** セクションで、次の手順を実行します。

    ![このスクリーンショットは、[New user]\(新しいユーザー\) セクションを示しています。ここで、ユーザー情報を入力します。](./media/askyourteam-tutorial/user3.png)

    1. **[First name]\(名\)** テキスト ボックスに、ユーザーの名前を入力します。

    1. **[Last name]\(姓\)** テキスト ボックスに、ユーザーの姓を入力します。

    1. **[Email]\(電子メール\)** テキスト ボックスに、ユーザーのメール アドレス (B.Simon@contoso.com など) を入力します。

    1. 組織の要件に従って、ユーザーの **ロール** を選択します。

    1. **[保存]** をクリックします。

## <a name="test-sso"></a>SSO のテスト 

このセクションでは、次のオプションを使用して Azure AD のシングル サインオン構成をテストします。

#### <a name="sp-initiated"></a>SP Initiated:

* Azure portal で **[このアプリケーションをテストします]** をクリックします。 これにより、ログイン フローを開始できる AskYourTeam のサインオン URL にリダイレクトされます。

* AskYourTeam のサインオン URL に直接移動し、そこからログイン フローを開始します。

#### <a name="idp-initiated"></a>IDP Initiated:

* Azure portal で **[このアプリケーションをテストします]** をクリックすると、SSO を設定した AskYourTeam に自動的にサインインされます

また、Microsoft マイ アプリを使用して、任意のモードでアプリケーションをテストすることもできます。 マイ アプリで [AskYourTeam] タイルをクリックすると、SP モードで構成されている場合は、ログイン フローを開始するためのアプリケーション サインオン ページにリダイレクトされます。IDP モードで構成されている場合は、SSO を設定した AskYourTeam に自動的にサインインされます。 マイ アプリの詳細については、[マイ アプリの概要](../user-help/my-apps-portal-end-user-access.md)に関するページを参照してください。

## <a name="next-steps"></a>次のステップ

AskYourTeam を構成したら、組織の機密データを流出と侵入からリアルタイムで保護するセッション制御を適用することができます。 セッション制御は、条件付きアクセスを拡張したものです。 [Microsoft Cloud App Security でセッション制御を強制する方法](/cloud-app-security/proxy-deployment-any-app)をご覧ください。