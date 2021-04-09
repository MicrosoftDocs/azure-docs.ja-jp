---
title: チュートリアル:Azure Active Directory シングル サインオン (SSO) と InVision の統合 | Microsoft Docs
description: Azure Active Directory と InVision の間でシングル サインオンを構成する方法について説明します。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 12/24/2020
ms.author: jeedes
ms.openlocfilehash: ec35917ca18064d58279d8ed2b3fb1f0e83a88fc
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "98736936"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-invision"></a>チュートリアル:Azure Active Directory シングル サインオン (SSO) と InVision の統合

このチュートリアルでは、InVision と Azure Active Directory (Azure AD) を統合する方法について説明します。 Azure AD と InVision を統合すると、次のことができます。

* InVision にアクセスできるユーザーを Azure AD で制御する。
* ユーザーが自分の Azure AD アカウントを使用して InVision に自動的にサインインできるようにする。
* 1 つの中央サイト (Azure Portal) で自分のアカウントを管理します。

## <a name="prerequisites"></a>前提条件

開始するには、次が必要です。

* Azure AD サブスクリプション。 サブスクリプションがない場合は、[無料アカウント](https://azure.microsoft.com/free/)を取得できます。
* InVision でのシングル サインオン (SSO) が有効なサブスクリプション。

## <a name="scenario-description"></a>シナリオの説明

このチュートリアルでは、テスト環境で Azure AD の SSO を構成してテストします。

* InVision では、**SP Initiated SSO と IDP Initiated SSO** がサポートされます

## <a name="adding-invision-from-the-gallery"></a>ギャラリーからの InVision の追加

Azure AD への InVision の統合を構成するには、ギャラリーからマネージド SaaS アプリの一覧に InVision を追加する必要があります。

1. 職場または学校アカウントか、個人の Microsoft アカウントを使用して、Azure portal にサインインします。
1. 左のナビゲーション ウィンドウで **[Azure Active Directory]** サービスを選択します。
1. **[エンタープライズ アプリケーション]** に移動し、 **[すべてのアプリケーション]** を選択します。
1. 新しいアプリケーションを追加するには、 **[新しいアプリケーション]** を選択します。
1. **[ギャラリーから追加する]** セクションで、検索ボックスに「**InVision**」と入力します。
1. 結果のパネルから **[InVision]** を選択し、アプリを追加します。 お使いのテナントにアプリが追加されるのを数秒待機します。

## <a name="configure-and-test-azure-ad-sso-for-invision"></a>InVision の Azure AD SSO の構成とテスト

**B.Simon** というテスト ユーザーを使用して、InVision に対する Azure AD SSO を構成してテストします。 SSO が機能するためには、Azure AD ユーザーと InVision の関連ユーザーとの間にリンク関係を確立する必要があります。

InVision に対して Azure AD SSO を構成してテストするには、次の手順を行います。

1. **[Azure AD SSO の構成](#configure-azure-ad-sso)** - ユーザーがこの機能を使用できるようにします。
    1. **[Azure AD のテスト ユーザーの作成](#create-an-azure-ad-test-user)** - B.Simon で Azure AD のシングル サインオンをテストします。
    1. **[Azure AD テスト ユーザーの割り当て](#assign-the-azure-ad-test-user)** - B.Simon が Azure AD シングル サインオンを使用できるようにします。
1. **[InVision SSO の構成](#configure-invision-sso)** - アプリケーション側でシングル サインオン設定を構成します。
    1. **[InVision テスト ユーザーの作成](#create-invision-test-user)** - InVision で B.Simon に対応するユーザーを作成し、Azure AD の B.Simon にリンクさせます。
1. **[SSO のテスト](#test-sso)** - 構成が機能するかどうかを確認します。

## <a name="configure-azure-ad-sso"></a>Azure AD SSO の構成

これらの手順に従って、Azure portal で Azure AD SSO を有効にします。

1. Azure portal の **InVision** アプリケーション統合ページで、 **[管理]** セクションを見つけて、 **[シングル サインオン]** を選択します。
1. **[シングル サインオン方式の選択]** ページで、 **[SAML]** を選択します。
1. **[SAML でシングル サインオンをセットアップします]** ページで、 **[基本的な SAML 構成]** の編集 (ペン) アイコンをクリックして設定を編集します。

   ![基本的な SAML 構成を編集する](common/edit-urls.png)

1. **[基本的な SAML 構成]** セクションで、アプリケーションを **IDP** 開始モードで構成する場合は、次のフィールドの値を入力します。

    a. **[識別子]** ボックスに、`https://<SUBDOMAIN>.invisionapp.com` の形式で URL を入力します。

    b. **[応答 URL]** ボックスに、`https://<SUBDOMAIN>.invisionapp.com//sso/auth` のパターンを使用して URL を入力します

1. アプリケーションを **SP** 開始モードで構成する場合は、 **[追加の URL を設定します]** をクリックして次の手順を実行します。

    **[サインオン URL]** ボックスに、`https://<SUBDOMAIN>.invisionapp.com` という形式で URL を入力します。

    > [!NOTE]
    > これらは実際の値ではありません。 実際の識別子、応答 URL、サインオン URL でこれらの値を更新します。 これらの値を取得するには、[InVision クライアント サポート チーム](mailto:support@invisionapp.com)に連絡してください。 Azure portal の **[基本的な SAML 構成]** セクションに示されているパターンを参照することもできます。

1. **[SAML でシングル サインオンをセットアップします]** ページの **[SAML 署名証明書]** セクションで、 **[証明書 (Base64)]** を見つけて、 **[ダウンロード]** を選択し、証明書をダウンロードして、お使いのコンピューターに保存します。

    ![証明書のダウンロードのリンク](common/certificatebase64.png)

1. **[InVision のセットアップ]** セクションで、要件に基づいて適切な URL をコピーします。

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

このセクションでは、B.Simon に InVision へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

1. Azure portal で **[エンタープライズ アプリケーション]** を選択し、 **[すべてのアプリケーション]** を選択します。
1. アプリケーションの一覧で **[InVision]** を選択します。
1. アプリの概要ページで、 **[管理]** セクションを見つけて、 **[ユーザーとグループ]** を選択します。

1. **[ユーザーの追加]** を選択し、 **[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。

1. **[ユーザーとグループ]** ダイアログの [ユーザー] の一覧から **[B.Simon]** を選択し、画面の下部にある **[選択]** ボタンをクリックします。
1. ユーザーにロールが割り当てられることが想定される場合は、 **[ロールの選択]** ドロップダウンからそれを選択できます。 このアプリに対してロールが設定されていない場合は、[既定のアクセス] ロールが選択されていることを確認します。
1. **[割り当ての追加]** ダイアログで、 **[割り当て]** をクリックします。

## <a name="configure-invision-sso"></a>InVision SSO の構成

1. InVision 内での構成を自動化するには、 **[拡張機能のインストール]** をクリックして **My Apps Secure Sign-in ブラウザー拡張機能** をインストールする必要があります。

    ![マイ アプリの拡張機能](common/install-myappssecure-extension.png)

2. ブラウザーに拡張機能を追加した後、 **[InVision のセットアップ]** をクリックすると、InVision アプリケーションに移動します。 そこから、管理者の資格情報を入力して InVision にサインインします。 ブラウザー拡張機能によりアプリケーションが自動的に構成され、手順 3 ～ 6 が自動化されます。

    ![セットアップの構成](common/setup-sso.png)

3. InVision を手動でセットアップする場合は、別の Web ブラウザー ウィンドウで、InVision 企業サイトに管理者としてサインインします。

1. **[Team]\(チーム\)** をクリックし、 **[Settings]\(設定\)** を選択します。

    ![[Settings]\(設定\) が選択されている [Team]\(チーム\) タブを示すスクリーンショット。](./media/invision-tutorial/config1.png)

1. **[Single sign-on]\(シングル サインオン\)** までスクロールして **[Change]\(変更\)** をクリックします。

    ![[Single sign-on]\(シングル サインオン\) の [Change]\(変更\) ボタンを示すスクリーンショット。](./media/invision-tutorial/config3.png)

1. **[Single sign-on]\(シングル サインオン\)** ページで、以下の手順を実行します。

    ![この手順の値を入力する [Single sign-on]\(シングル サインオン\) ページを示すスクリーンショット。](./media/invision-tutorial/config4.png)

    a. **[Require SSO for every member of < account name >]\(<アカウント名> のすべてのメンバーに対して SSO を要求する\)** を **オン** に変更します。

    b. **[name]\(名前\)** ボックスに、名前 (`azureadsso` など) を入力します。

    c. **[Sign-in URL]\(サインイン URL\)** ボックスに、サインオン URL 値を入力します。

    d. **[Sign-out URL]\(サインアウト URL\)** ボックスに、Azure portal からコピーした **ログアウト** URL の値を貼り付けます。

    e. ダウンロードした **証明書 (Base64)** をメモ帳で開き、その内容をコピーして **[SAML Certificate]\(SAML 証明書\)** ボックスに貼り付けます。

    f. **[Name ID Format]\(名前 ID の形式\)** ボックスで、**名前 ID の形式** に `Unspecified` を使用します。

    g. **[HASH Algorithm]\(ハッシュ アルゴリズム\)** のドロップダウンで **[SHA-256]** を選択します。

    h. **[SSO Button Label]\(SSO ボタン ラベル\)** に適切な名前を入力します。

    i. **[Allow Just-in-Time provisioning]\(Just-in-Time プロビジョニングを許可する\)** をオンにします。

    j. **[Update]** をクリックします。

### <a name="create-invision-test-user"></a>InVision テスト ユーザーの作成

1. 別の Web ブラウザー ウィンドウで、管理者として InVision サイトにサインインします。

1. **[Team]\(チーム\)** をクリックし、 **[People]\(ユーザー\)** を選択します。

    ![[People]\(ユーザー\) が選択されている [Team]\(チーム\) タブを示すスクリーンショット。](./media/invision-tutorial/config2.png)

1. **+ アイコン** をクリックして、新しいユーザーを追加します。

    ![ユーザーを追加する [+] アイコンを示すスクリーンショット。](./media/invision-tutorial/user1.png)

1. ユーザーのメール アドレスを入力し、 **[Next]\(次へ\)** をクリックします。

    ![アドレスを入力できる [Invite to]\(招待先\) ダイアログ ボックスを示すスクリーンショット。](./media/invision-tutorial/user2.png)

1. メール アドレスを確認し、 **[Invite]\(招待\)** をクリックします。

    ![処理する招待を選択できる [Invite]\(招待\) ダイアログを示すスクリーンショット。](./media/invision-tutorial/user3.png)

## <a name="test-sso"></a>SSO のテスト

このセクションでは、次のオプションを使用して Azure AD のシングル サインオン構成をテストします。

#### <a name="sp-initiated"></a>SP Initiated:

* Azure portal で **[このアプリケーションをテストします]** をクリックします。 これにより、ログイン フローを開始できる InVision のサインオン URL にリダイレクトされます。

* InVision のサインオン URL に直接移動し、そこからログイン フローを開始します。

#### <a name="idp-initiated"></a>IDP Initiated:

* Azure portal で **[このアプリケーションをテストします]** をクリックすると、SSO を設定した InVision に自動的にサインインされます

また、Microsoft マイ アプリを使用して、任意のモードでアプリケーションをテストすることもできます。 マイ アプリで [InVision] タイルをクリックすると、SP モードで構成されている場合は、ログイン フローを開始するためのアプリケーション サインオン ページにリダイレクトされます。IDP モードで構成されている場合は、SSO を設定した InVision に自動的にサインインされます。 マイ アプリの詳細については、[マイ アプリの概要](../user-help/my-apps-portal-end-user-access.md)に関するページを参照してください。

## <a name="next-steps"></a>次のステップ

InVision を構成したら、組織の機密データを流出と侵入からリアルタイムで保護するセッション制御を適用することができます。 セッション制御は、条件付きアクセスを拡張したものです。 [Microsoft Cloud App Security でセッション制御を強制する方法](/cloud-app-security/proxy-deployment-any-app)をご覧ください。