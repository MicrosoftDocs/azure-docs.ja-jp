---
title: チュートリアル:Netskope Administrator Console と Azure Active Directory のシングル サインオン (SSO) 統合 | Microsoft Docs
description: Azure Active Directory と Netskope Administrator Console の間でシングル サインオンを構成する方法について説明します。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 12/17/2020
ms.author: jeedes
ms.openlocfilehash: 8435cab1855e9df871d17ff7fa393b6ab2cf0cb1
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "98736345"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-netskope-administrator-console"></a>チュートリアル:Netskope Administrator Console と Azure Active Directory のシングル サインオン (SSO) 統合

このチュートリアルでは、Netskope Administrator Console と Azure Active Directory (Azure AD) を統合する方法について説明します。 Netskope Administrator Console と Azure AD を統合すると、次のことができます。

* Netskope Administrator Console にアクセスできる Azure AD ユーザーを制御する。
* ユーザーが自分の Azure AD アカウントを使用して自動的に Netskope Administrator Console にサインインできるようにする。
* 1 つの中央サイト (Azure Portal) で自分のアカウントを管理します。

## <a name="prerequisites"></a>前提条件

開始するには、次が必要です。

* Azure AD サブスクリプション。 サブスクリプションがない場合は、[無料アカウント](https://azure.microsoft.com/free/)を取得できます。
* Netskope Administrator Console でのシングル サインオン (SSO) が有効なサブスクリプション。

## <a name="scenario-description"></a>シナリオの説明

このチュートリアルでは、テスト環境で Azure AD の SSO を構成してテストします。

* Netskope Administrator Console では **SP と IDP** によって開始される SSO がサポートされます

> [!NOTE]
> このアプリケーションの識別子は固定文字列値であるため、1 つのテナントで構成できるインスタンスは 1 つだけです。


## <a name="adding-netskope-administrator-console-from-the-gallery"></a>ギャラリーからの Netskope Administrator Console の追加

Netskope Administrator Console の Azure AD への統合を構成するには、ギャラリーからマネージド SaaS アプリの一覧に Netskope Administrator Console を追加する必要があります。

1. 職場または学校アカウントか、個人の Microsoft アカウントを使用して、Azure portal にサインインします。
1. 左のナビゲーション ウィンドウで **[Azure Active Directory]** サービスを選択します。
1. **[エンタープライズ アプリケーション]** に移動し、 **[すべてのアプリケーション]** を選択します。
1. 新しいアプリケーションを追加するには、 **[新しいアプリケーション]** を選択します。
1. **[ギャラリーから追加する]** セクションで、検索ボックスに、「**Netskope Administrator Console**」と入力します。
1. 結果のパネルから **[Netskope Administrator Console]** を選択し、アプリを追加します。 お使いのテナントにアプリが追加されるのを数秒待機します。

## <a name="configure-and-test-azure-ad-sso-for-netskope-administrator-console"></a>Netskope Administrator Console の Azure AD SSO の構成とテスト

**B.Simon** というテスト ユーザーを使用して、Netskope Administrator Console に対する Azure AD SSO を構成してテストします。 SSO が機能するためには、Azure AD ユーザーと Netskope Administrator Console の関連ユーザーとの間にリンク関係を確立する必要があります。

Netskope Administrator Console に対して Azure AD SSO を構成してテストするには、次の手順を行います。

1. **[Azure AD SSO の構成](#configure-azure-ad-sso)** - ユーザーがこの機能を使用できるようにします。
    1. **[Azure AD のテスト ユーザーの作成](#create-an-azure-ad-test-user)** - B.Simon で Azure AD のシングル サインオンをテストします。
    1. **[Azure AD テスト ユーザーの割り当て](#assign-the-azure-ad-test-user)** - B.Simon が Azure AD シングル サインオンを使用できるようにします。
1. **[Netskope Administrator Console SSO の構成](#configure-netskope-administrator-console-sso)** - アプリケーション側でシングル サインオン設定を構成します。
    1. **[Netskope Administrator Console テスト ユーザーの作成](#create-netskope-administrator-console-test-user)** - Netskope Administrator Console で B.Simon に対応するユーザーを作成し、Azure AD のこのユーザーにリンクさせます。
1. **[SSO のテスト](#test-sso)** - 構成が機能するかどうかを確認します。

## <a name="configure-azure-ad-sso"></a>Azure AD SSO の構成

これらの手順に従って、Azure portal で Azure AD SSO を有効にします。

1. Azure portal の **Netskope Administrator Console** アプリケーション統合ページで、 **[管理]** セクションを見つけて、 **[シングル サインオン]** を選択します。
1. **[シングル サインオン方式の選択]** ページで、 **[SAML]** を選択します。
1. **[SAML によるシングル サインオンのセットアップ]** ページで、 **[基本的な SAML 構成]** の鉛筆アイコンをクリックして設定を編集します。

   ![基本的な SAML 構成を編集する](common/edit-urls.png)

1. **[基本的な SAML 構成]** セクションで、アプリケーションを **IDP** 開始モードで構成する場合は、次のフィールドの値を入力します。

    **[応答 URL]** ボックスに、`https://<tenant_host_name>/saml/acs` のパターンを使用して URL を入力します

    > [!NOTE]
    > この値は実際のものではありません。 実際の応答 URL でこの値を更新します。 この値については、このチュートリアルで後述します。

1. アプリケーションを **SP** 開始モードで構成する場合は、 **[追加の URL を設定します]** をクリックして次の手順を実行します。

    **[サインオン URL]** ボックスに、`https://<tenantname>.goskope.com` という形式で URL を入力します。

    > [!NOTE]
    > [サインオン URL] は実際の値ではありません。 実際のサインオン URL で [サインオン URL] の値を更新してください。 サインオン URL 値を取得するには、[Netskope Administrator Console クライアント サポート チーム](mailto:support@netskope.com)にお問い合わせください。 Azure portal の **[基本的な SAML 構成]** セクションに示されているパターンを参照することもできます。

1. Netskope Administrator Console アプリケーションでは、特定の形式の SAML アサーションを使用するため、カスタム属性マッピングを SAML トークン属性の構成に追加する必要があります。 次のスクリーンショットには、既定の属性一覧が示されています。

    ![image](common/default-attributes.png)

1. その他に、Netskope Administrator Console アプリケーションでは、いくつかの属性が SAML 応答で返されることが想定されています。それらの属性を次に示します。 これらの属性も値が事前に設定されますが、要件に従ってそれらの値を確認することができます。

    | 名前 |  ソース属性|
    | ---------| --------- |
    | admin-role | user.assignedroles |

    > [!NOTE]
    > Azure AD でロールを作成する方法については、[ここ](../develop/howto-add-app-roles-in-azure-ad-apps.md#app-roles-ui--preview)をクリックしてください。

1. **[SAML でシングル サインオンをセットアップします]** ページの **[SAML 署名証明書]** セクションで、 **[証明書 (Base64)]** を見つけて、 **[ダウンロード]** を選択し、証明書をダウンロードして、お使いのコンピューターに保存します。

    ![証明書のダウンロードのリンク](common/certificatebase64.png)

1. **[Set up Netskope Administrator Console]\(Netskope Administrator Console のセットアップ\)** セクションで、要件に基づいて適切な URL をコピーします。

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

このセクションでは、B.Simon に Netskope Administrator Console へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

1. Azure portal で **[エンタープライズ アプリケーション]** を選択し、 **[すべてのアプリケーション]** を選択します。
1. アプリケーションの一覧で **[Netskope Administrator Console]** を選択します。
1. アプリの概要ページで、 **[管理]** セクションを見つけて、 **[ユーザーとグループ]** を選択します。
1. **[ユーザーの追加]** を選択し、 **[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。
1. **[ユーザーとグループ]** ダイアログの [ユーザー] の一覧から **[B.Simon]** を選択し、画面の下部にある **[選択]** ボタンをクリックします。
1. 前述のロールを設定した場合、 **[ロールの選択]** ボックスの一覧からそれを選択できます。
1. **[割り当ての追加]** ダイアログで、 **[割り当て]** をクリックします。

## <a name="configure-netskope-administrator-console-sso"></a>Netskope Administrator Console SSO を構成する

1. ブラウザーで新しいタブを開き、Netskope Administrator Console の会社のサイトに管理者としてサインインします。

1. 左側のナビゲーション ペインの **[Settings]\(設定\)** タブをクリックします。

    ![ナビゲーション ウィンドウで [設定] が選択されていることを示すスクリーンショット。](./media/netskope-cloud-security-tutorial/config-settings.png)

1. **[管理]** タブをクリックします。

    ![[設定] で [管理] が選択されていることを示すスクリーンショット。](./media/netskope-cloud-security-tutorial/config-administration.png)

1. **[SSO]** タブをクリックします。

    ![[管理] で [SSO] が選択されていることを示すスクリーンショット。](./media/netskope-cloud-security-tutorial/config-sso.png)

1. **[ネットワーク設定]** セクションで、次の手順に従います。
    
    ![[ネットワーク設定] を示すスクリーンショット。ここでは、説明されている値を入力できます。](./media/netskope-cloud-security-tutorial/config-pasteurls.png)

    a. **[Assertion Consumer Service URL]** の値をコピーして、Azure portal の **[基本的な SAML 構成]** セクションにある **[返信 URL]** テキスト ボックスに貼り付けます。

    b. **[Service Provider Entity ID]\(サービス プロバイダー エンティティ ID\\)** の値をコピーして、Azure portal の **[基本的な SAML 構成]** セクションにある **[識別子]** テキスト ボックスに貼り付けます。

1. **[SSO/SLO Settings]\(SSO/SLO 設定\)** セクションの **[設定の編集]** をクリックします。

    ![[SSO/SLO Settings]\(SSO/SLO 設定\) を示すスクリーンショット。ここでは、[設定の編集] を選択できます。](./media/netskope-cloud-security-tutorial/config-editsettings.png)

1. **[設定]** ポップアップ ウィンドウで、次の手順を実行します。

    ![[設定] ダイアログ ボックスを示すスクリーンショット。ここでは、説明されている値を入力できます。](./media/netskope-cloud-security-tutorial/configuration.png)

    a. **[Enable SSO]\(SSO を有効にする\)** を選択します。

    b. **[IDP URL]** テキスト ボックスに、Azure portal からコピーした **ログイン URL** の値を貼り付けます。

    c. **[IDP ENTITY ID]\(IDP エンティティ ID\)** テキスト ボックスに、Azure portal からコピーした **Azure AD ID** の値を貼り付けます。

    d. ダウンロード済みの Base64 でエンコードされた証明書をメモ帳で開き、その内容をクリップボードにコピーしてから、それを **[IDP CERTIFICATE]\(IDP 証明書\)** テキスト ボックスに貼り付けます。

    e. **[Enable SSO]\(SSO を有効にする\)** を選択します。

    f. **[IDP SLO URL]** テキスト ボックスに、Azure portal からコピーした **ログアウト URL** の値を貼り付けます。

    g. **[SUBMIT]\(送信\)** をクリックします。

### <a name="create-netskope-administrator-console-test-user"></a>Netskope Administrator Console テスト ユーザーの作成

1. ブラウザーで新しいタブを開き、Netskope Administrator Console の会社のサイトに管理者としてサインインします。

1. 左側のナビゲーション ペインの **[Settings]\(設定\)** タブをクリックします。

    ![[設定] が選択されていることを示すスクリーンショット。](./media/netskope-cloud-security-tutorial/config-settings.png)

1. **[Active Platform]\(アクティブなプラットフォーム\)** タブをクリックします。

    ![[設定] で [アクティブなプラットフォーム] が選択されていることを示すスクリーンショット。](./media/netskope-cloud-security-tutorial/user1.png)

1. **[Users]** タブをクリックします。

    ![[アクティブなプラットフォーム] で [ユーザー] が選択されていることを示すスクリーンショット。](./media/netskope-cloud-security-tutorial/add-user.png)

1. **[ADD USERS]\(ユーザーの追加\)** をクリックします。

    ![[ユーザー] ダイアログ ボックスを示すスクリーンショット。ここでは、[ユーザーの追加] を選択できます。](./media/netskope-cloud-security-tutorial/user-add.png)

1. 追加するユーザーのメール アドレスを入力し、 **[ADD]\(追加\)** をクリックします。

    ![[ユーザーの追加] を示すスクリーンショット。ここでは、ユーザーの一覧を入力できます。](./media/netskope-cloud-security-tutorial/add-user-popup.png)

## <a name="test-sso"></a>SSO のテスト

このセクションでは、次のオプションを使用して Azure AD のシングル サインオン構成をテストします。 

#### <a name="sp-initiated"></a>SP Initiated:

* Azure portal で **[このアプリケーションをテストします]** をクリックします。 これにより、ログイン フローを開始できる Netskope Administrator Console のサインオン URL にリダイレクトされます。  

* Netskope Administrator Console のサインオン URL に直接移動し、そこからログイン フローを開始します。

#### <a name="idp-initiated"></a>IDP Initiated:

* Azure portal で **[このアプリケーションをテストします]** をクリックすると、SSO を設定した Netskope Administrator Console に自動的にサインインされます 

また、Microsoft マイ アプリを使用して、任意のモードでアプリケーションをテストすることもできます。 マイ アプリで [Netskope Administrator Console] タイルをクリックすると、SP モードで構成されている場合は、ログイン フローを開始するためのアプリケーション サインオン ページにリダイレクトされます。IDP モードで構成されている場合は、SSO を設定した Netskope Administrator Console に自動的にサインインされます。 マイ アプリの詳細については、[マイ アプリの概要](../user-help/my-apps-portal-end-user-access.md)に関するページを参照してください。

## <a name="next-steps"></a>次のステップ

Netskope Administrator Console を構成したら、組織の機密データを流出と侵入からリアルタイムで保護するセッション制御を適用できます。 セッション制御は、条件付きアクセスを拡張したものです。 [Microsoft Cloud App Security でセッション制御を強制する方法](/cloud-app-security/proxy-deployment-any-app)をご覧ください。
