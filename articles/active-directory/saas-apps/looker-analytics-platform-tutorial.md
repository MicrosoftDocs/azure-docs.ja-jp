---
title: チュートリアル:Azure Active Directory シングル サインオン (SSO) と Looker Analytics Platform の統合 | Microsoft Docs
description: Azure Active Directory と Looker Analytics Platform の間でシングル サインオンを構成する方法について説明します。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 11/10/2020
ms.author: jeedes
ms.openlocfilehash: dbb6f6d278256730e77677e78f452615fe4b611e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "96180744"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-looker-analytics-platform"></a>チュートリアル:Azure Active Directory シングル サインオン (SSO) と Looker Analytics Platform の統合

このチュートリアルでは、Looker Analytics Platform と Azure Active Directory (Azure AD) を統合する方法について説明します。 Looker Analytics Platform と Azure AD を統合すると、次のことができます。

* Looker Analytics Platform にアクセスできるユーザーを Azure AD で制御する。
* ユーザーが自分の Azure AD アカウントを使用して Looker Analytics Platform に自動的にサインインできるように設定する。
* 1 つの中央サイト (Azure Portal) で自分のアカウントを管理します。

## <a name="prerequisites"></a>前提条件

開始するには、次が必要です。

* Azure AD サブスクリプション。 サブスクリプションがない場合は、[無料アカウント](https://azure.microsoft.com/free/)を取得できます。
* Looker Analytics Platform でのシングル サインオン (SSO) が有効なサブスクリプション。

## <a name="scenario-description"></a>シナリオの説明

このチュートリアルでは、テスト環境で Azure AD の SSO を構成してテストします。

* Looker Analytics Platform では、**SP および IDP** Initiated SSO がサポートされます
* Looker Analytics Platform では、**Just In Time** ユーザー プロビジョニングがサポートされます


## <a name="adding-looker-analytics-platform-from-the-gallery"></a>ギャラリーからの Looker Analytics Platform の追加

Azure AD への Looker Analytics Platform の統合を構成するには、ギャラリーからマネージド SaaS アプリの一覧に Looker Analytics Platform を追加する必要があります。

1. 職場または学校アカウントか、個人の Microsoft アカウントを使用して、Azure portal にサインインします。
1. 左のナビゲーション ウィンドウで **[Azure Active Directory]** サービスを選択します。
1. **[エンタープライズ アプリケーション]** に移動し、 **[すべてのアプリケーション]** を選択します。
1. 新しいアプリケーションを追加するには、 **[新しいアプリケーション]** を選択します。
1. **[ギャラリーから追加する]** セクションで、検索ボックスに「**Looker Analytics Platform**」と入力します。
1. 結果パネルから **[Looker Analytics Platform]** を選択し、そのアプリを追加します。 お使いのテナントにアプリが追加されるのを数秒待機します。


## <a name="configure-and-test-azure-ad-sso-for-looker-analytics-platform"></a>Looker Analytics Platform の Azure AD SSO の構成とテスト

**B.Simon** というテスト ユーザーを使用して、Looker Analytics Platform に対する Azure AD SSO を構成してテストします。 SSO が機能するためには、Azure AD ユーザーと Looker Analytics Platform の関連ユーザーとの間にリンク関係を確立する必要があります。

Looker Analytics Platform に対して Azure AD SSO を構成してテストするには、次の手順を行います。

1. **[Azure AD SSO の構成](#configure-azure-ad-sso)** - ユーザーがこの機能を使用できるようにします。
    1. **[Azure AD のテスト ユーザーの作成](#create-an-azure-ad-test-user)** - B.Simon で Azure AD のシングル サインオンをテストします。
    1. **[Azure AD テスト ユーザーの割り当て](#assign-the-azure-ad-test-user)** - B.Simon が Azure AD シングル サインオンを使用できるようにします。
1. **[Looker Analytics Platform の SSO の構成](#configure-looker-analytics-platform-sso)** - アプリケーション側でシングル サインオン設定を構成します。
    1. **[Looker Analytics Platform のテスト ユーザーの作成](#create-looker-analytics-platform-test-user)** - Looker Analytics Platform で B.Simon に対応するユーザーを作成し、Azure AD の B.Simon にリンクさせます。
1. **[SSO のテスト](#test-sso)** - 構成が機能するかどうかを確認します。

## <a name="configure-azure-ad-sso"></a>Azure AD SSO の構成

これらの手順に従って、Azure portal で Azure AD SSO を有効にします。

1. Azure portal の **Looker Analytics Platform** アプリケーション統合ページで、 **[管理]** セクションを探して、 **[シングル サインオン]** を選択します。
1. **[シングル サインオン方式の選択]** ページで、 **[SAML]** を選択します。
1. **[SAML でシングル サインオンをセットアップします]** ページで、 **[基本的な SAML 構成]** の編集 (ペン) アイコンをクリックして設定を編集します。

   ![基本的な SAML 構成を編集する](common/edit-urls.png)

1. **[基本的な SAML 構成]** セクションで、アプリケーションを **IDP** 開始モードで構成する場合は、次のフィールドの値を入力します。

    a. **[識別子]** ボックスに、`<SPN>_looker` の形式で URL を入力します。

    b. **[応答 URL]** ボックスに、`https://<SUBDOMAIN>.looker.com/samlcallback` のパターンを使用して URL を入力します

1. アプリケーションを **SP** 開始モードで構成する場合は、 **[追加の URL を設定します]** をクリックして次の手順を実行します。

    **[サインオン URL]** ボックスに、`https://<SUBDOMAIN>.looker.com` という形式で URL を入力します。

    > [!NOTE]
    > これらは実際の値ではありません。 実際の識別子、応答 URL、サインオン URL でこれらの値を更新します。 これらの値を取得するには、[Looker Analytics Platform クライアント サポート チーム](mailto:support@looker.com)に問い合わせてください。 Azure portal の **[基本的な SAML 構成]** セクションに示されているパターンを参照することもできます。

1. **[SAML でシングル サインオンをセットアップします]** ページの **[SAML 署名証明書]** セクションで、 **[フェデレーション メタデータ XML]** を探して **[ダウンロード]** を選択し、証明書をダウンロードして、お使いのコンピューターに保存します。

    ![証明書のダウンロードのリンク](common/metadataxml.png)

1. **[Looker Analytics Platform のセットアップ]** セクションで、要件に基づいて適切な URL をコピーします。

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

このセクションでは、Looker Analytics Platform へのアクセスを許可することで、B.Simon が Azure シングル サインオンを使用できるようにします。

1. Azure portal で **[エンタープライズ アプリケーション]** を選択し、 **[すべてのアプリケーション]** を選択します。
1. アプリケーションの一覧で **[Looker Analytics Platform]** を選択します。
1. アプリの概要ページで、 **[管理]** セクションを見つけて、 **[ユーザーとグループ]** を選択します。
1. **[ユーザーの追加]** を選択し、 **[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。
1. **[ユーザーとグループ]** ダイアログの [ユーザー] の一覧から **[B.Simon]** を選択し、画面の下部にある **[選択]** ボタンをクリックします。
1. ユーザーにロールが割り当てられることが想定される場合は、 **[ロールの選択]** ドロップダウンからそれを選択できます。 このアプリに対してロールが設定されていない場合は、[既定のアクセス] ロールが選択されていることを確認します。
1. **[割り当ての追加]** ダイアログで、 **[割り当て]** をクリックします。

## <a name="configure-looker-analytics-platform-sso"></a>Looker Analytics Platform の SSO の構成

1. 別の Web ブラウザー ウィンドウで、管理者として Looker Analytics Platform Web サイトにサインインします。

1. **[Admin]\(管理\)**  ->  **[Authentication]\(認証\)**  ->  **[SAML]** の順に移動します。

    ![SAML オプションのスクリーンショット](./media/looker-analytics-platform-tutorial/admin.png)

1. Azure portal からコピーした **フェデレーション メタデータ** 情報を **[IDP Metadata]\(IDP メタデータ\)** ボックスに貼り付けて、 **[Load]\(読み込み\)** をクリックします。

    ![メタデータ アップロードのスクリーンショット](./media/looker-analytics-platform-tutorial/metadata.png)

1. **[User Attribute Settings]\(ユーザー属性設定\)** セクションで次の手順を実行します。

    ![[User Attribute Settings]\(ユーザー属性設定\) のスクリーンショット](./media/looker-analytics-platform-tutorial/user-attribute-settings.png)

    a. [Email Attr]\(Email 属性\) フィールドに次の値を追加します: `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress`

    b. [Fname Attr]\(Fname 属性\) フィールドに次の値を追加します: `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname`

    c. [Lname Attr]\(Lname 属性\) フィールドに次の値を追加します: `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname`

    d. **[Test User Authentication]\(ユーザー認証のテスト\)** で **[Test SAML Authentication]\(SAML 認証のテスト\)** をクリックします。 読み込まれたページに "Server Response Successfully Validated (サーバーの応答が正しく検証されました)" と表示されている場合、SAML 統合のインスタンスは正しく設定されています。
    
    e. **[Save and Apply Settings]\(設定の保存と適用\)** で、 **[I have confirmed the configuration above and want to enable applying it globally]\(上記の構成を確認し、グローバルに適用して有効にすることを希望します\)** チェック ボックスをオンにします。

    f. **[Update Settings]\(設定の更新\)** ボタンをクリックします。

### <a name="create-looker-analytics-platform-test-user"></a>Looker Analytics Platform のテスト ユーザーの作成

このセクションでは、Britta Simon というユーザーを Looker Analytics Platform に作成します。 Looker Analytics Platform では、Just-In-Time ユーザー プロビジョニングがサポートされています。これは既定で有効になっています。 このセクションでは、ユーザー側で必要な操作はありません。 Looker Analytics Platform にユーザーがまだ存在していない場合は、認証後に新しく作成されます。

## <a name="test-sso"></a>SSO のテスト 

このセクションでは、次のオプションを使用して Azure AD のシングル サインオン構成をテストします。 

#### <a name="sp-initiated"></a>SP Initiated:

* Azure portal で **[このアプリケーションをテストします]** をクリックします。 これにより、ログイン フローを開始できる Looker Analytics Platform のサインオン URL にリダイレクトされます。  

* Looker Analytics Platform のサインオン URL に直接移動し、そこからログイン フローを開始します。

#### <a name="idp-initiated"></a>IDP Initiated:

* Azure portal で **[このアプリケーションをテストします]** をクリックすると、SSO を設定した Looker Analytics Platform に自動的にサインインされます 

また、Microsoft アクセス パネルを使用して、任意のモードでアプリケーションをテストすることもできます。 アクセス パネルで Looker Analytics Platform タイルをクリックすると、SP モードで構成されている場合は、ログイン フローを開始するためのアプリケーション サインオン ページにリダイレクトされます。IDP モードで構成されている場合は、SSO を設定した Looker Analytics Platform に自動的にサインインされます。 アクセス パネルの詳細については、[アクセス パネルの概要](../user-help/my-apps-portal-end-user-access.md)に関する記事を参照してください。

## <a name="next-steps"></a>次のステップ

Looker Analytics Platform を構成したら、組織の機密データを流出と侵入からリアルタイムで保護するセッション制御を適用することができます。 セッション制御は、条件付きアクセスを拡張したものです。 [Microsoft Cloud App Security でセッション制御を強制する方法](/cloud-app-security/proxy-deployment-any-app)をご覧ください。