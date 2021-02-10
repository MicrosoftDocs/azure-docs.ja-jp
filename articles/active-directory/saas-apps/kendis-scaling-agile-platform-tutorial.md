---
title: チュートリアル:Azure Active Directory シングル サインオン (SSO) と Kendis - Scaling Agile Platform の統合 | Microsoft Docs
description: Azure Active Directory と Kendis - Scaling Agile Platform の間でシングル サインオンを構成する方法について説明します。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/28/2021
ms.author: jeedes
ms.openlocfilehash: e02ff4926897fafc72e1a5081366faad5d2f03ba
ms.sourcegitcommit: b85ce02785edc13d7fb8eba29ea8027e614c52a2
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/03/2021
ms.locfileid: "99509621"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-kendis-scaling-agile-platform"></a>チュートリアル:Azure Active Directory シングル サインオン (SSO) と Kendis - Scaling Agile Platform の統合

このチュートリアルでは、Kendis - Scaling Agile Platform と Azure Active Directory (Azure AD) を統合する方法について説明します。 Azure AD と Kendis - Scaling Agile Platform を統合すると、次のことができます。

* Kendis - Scaling Agile Platform にアクセスできるユーザーを Azure AD で制御できます。
* ユーザーが自分の Azure AD アカウントを使用して Kendis - Scaling Agile Platform に自動的にサインインできるように設定できます。
* 1 つの中央サイト (Azure Portal) で自分のアカウントを管理します。

## <a name="prerequisites"></a>前提条件

開始するには、次が必要です。

* Azure AD サブスクリプション。 サブスクリプションがない場合は、[無料アカウント](https://azure.microsoft.com/free/)を取得できます。
* Kendis - Scaling Agile Platform でのシングル サインオン (SSO) が有効なサブスクリプション。

## <a name="scenario-description"></a>シナリオの説明

このチュートリアルでは、テスト環境で Azure AD の SSO を構成してテストします。

* Kendis - Scaling Agile Platform では、**SP および IDP** Initiated SSO がサポートされます
* Kendis - Scaling Agile Platform では、**Just In Time** ユーザー プロビジョニングがサポートされます


## <a name="adding-kendis-scaling-agile-platform-from-the-gallery"></a>ギャラリーからの Kendis - Scaling Agile Platform の追加

Azure AD への Kendis - Scaling Agile Platform の統合を構成するには、ギャラリーからマネージド SaaS アプリの一覧に Kendis - Scaling Agile Platform を追加する必要があります。

1. 職場または学校アカウントか、個人の Microsoft アカウントを使用して、Azure portal にサインインします。
1. 左のナビゲーション ウィンドウで **[Azure Active Directory]** サービスを選択します。
1. **[エンタープライズ アプリケーション]** に移動し、 **[すべてのアプリケーション]** を選択します。
1. 新しいアプリケーションを追加するには、 **[新しいアプリケーション]** を選択します。
1. **[ギャラリーから追加する]** セクションで、検索ボックスに、「**Kendis - Scaling Agile Platform**」と入力します。
1. 結果のパネルから **[Kendis - Scaling Agile Platform]** を選択し、アプリを追加します。 お使いのテナントにアプリが追加されるのを数秒待機します。


## <a name="configure-and-test-azure-ad-sso-for-kendis-scaling-agile-platform"></a>Kendis - Scaling Agile Platform の Azure AD SSO の構成とテスト

**B.Simon** というテスト ユーザーを使用して、Kendis - Scaling Agile Platform に対する Azure AD SSO を構成してテストします。 SSO が機能するために、Azure AD ユーザーと Kendis - Scaling Agile Platform の関連ユーザーの間で、リンク関係を確立する必要があります。

Kendis - Scaling Agile Platform に対する Azure AD SSO を構成してテストするには、次の手順を行います。

1. **[Azure AD SSO の構成](#configure-azure-ad-sso)** - ユーザーがこの機能を使用できるようにします。
    1. **[Azure AD のテスト ユーザーの作成](#create-an-azure-ad-test-user)** - B.Simon で Azure AD のシングル サインオンをテストします。
    1. **[Azure AD テスト ユーザーの割り当て](#assign-the-azure-ad-test-user)** - B.Simon が Azure AD シングル サインオンを使用できるようにします。
1. **[Kendis - Scaling Agile Platform の SSO の構成](#configure-kendis-scaling-agile-platform-sso)** - アプリケーション側でシングル サインオン設定を構成します。
    1. **[Kendis - Scaling Agile Platform のテスト ユーザーの作成](#create-kendis-scaling-agile-platform-test-user)** - Kendis - Scaling Agile Platform で B.Simon に対応するユーザーを作成し、Azure AD の B.Simon にリンクさせます。
1. **[SSO のテスト](#test-sso)** - 構成が機能するかどうかを確認します。

## <a name="configure-azure-ad-sso"></a>Azure AD SSO の構成

これらの手順に従って、Azure portal で Azure AD SSO を有効にします。

1. Azure portal の **Kendis - Scaling Agile Platform** アプリケーション統合ページで、 **[管理]** セクションを探して、 **[シングル サインオン]** を選択します。
1. **[シングル サインオン方式の選択]** ページで、 **[SAML]** を選択します。
1. **[SAML によるシングル サインオンのセットアップ]** ページで、 **[基本的な SAML 構成]** の鉛筆アイコンをクリックして設定を編集します。

   ![基本的な SAML 構成を編集する](common/edit-urls.png)

1. **[基本的な SAML 構成]** セクションで、アプリケーションを **IDP** 開始モードで構成する場合は、次のフィールドの値を入力します。

    a. **[識別子]** ボックスに、`https://<SUBDOMAIN>.kendis.io` の形式で URL を入力します。

    b. **[応答 URL]** ボックスに、`https://<SUBDOMAIN>.kendis.io/login/saml` のパターンを使用して URL を入力します

1. アプリケーションを **SP** 開始モードで構成する場合は、 **[追加の URL を設定します]** をクリックして次の手順を実行します。

    **[サインオン URL]** ボックスに、`https://<SUBDOMAIN>.kendis.io/login` という形式で URL を入力します。

    > [!NOTE]
    > これらは実際の値ではありません。 実際の識別子、応答 URL、サインオン URL でこれらの値を更新します。 これらの値を取得するには、[Kendis - Scaling Agile Platform クライアント サポート チーム](mailto:support@kendis.io)に問い合わせてください。 Azure portal の **[基本的な SAML 構成]** セクションに示されているパターンを参照することもできます。

1. **[SAML でシングル サインオンをセットアップします]** ページの **[SAML 署名証明書]** セクションで、 **[証明書 (Base64)]** を見つけて、 **[ダウンロード]** を選択し、証明書をダウンロードして、お使いのコンピューターに保存します。

    ![証明書のダウンロードのリンク](common/certificatebase64.png)

1. **[Kendis - Scaling Agile Platform のセットアップ]** セクションで、要件に基づいて適切な URL をコピーします。

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

このセクションでは、B.Simon に Kendis - Scaling Agile Platform へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

1. Azure portal で **[エンタープライズ アプリケーション]** を選択し、 **[すべてのアプリケーション]** を選択します。
1. アプリケーションの一覧で **[Kendis - Scaling Agile Platform]** を選択します。
1. アプリの概要ページで、 **[管理]** セクションを見つけて、 **[ユーザーとグループ]** を選択します。
1. **[ユーザーの追加]** を選択し、 **[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。
1. **[ユーザーとグループ]** ダイアログの [ユーザー] の一覧から **[B.Simon]** を選択し、画面の下部にある **[選択]** ボタンをクリックします。
1. ユーザーにロールが割り当てられることが想定される場合は、 **[ロールの選択]** ドロップダウンからそれを選択できます。 このアプリに対してロールが設定されていない場合は、[既定のアクセス] ロールが選択されていることを確認します。
1. **[割り当ての追加]** ダイアログで、 **[割り当て]** をクリックします。

## <a name="configure-kendis-scaling-agile-platform-sso"></a>Kendis - Scaling Agile Platform SSO の構成

1. 別の Web ブラウザー ウィンドウで、Kendis - Scaling Agile Platform 企業サイトに管理者としてサインインします。

1. **[Settings]\(設定)\、[SAML Configurations]\(SAML 構成\)** に移動します。

    ![SAML 構成の設定](./media/kendis-scaling-agile-platform-tutorial/settings.png)

1. ページの下部にある **[Edit]\(編集\)** ボタンをクリックして、次の手順を実行します。

    ![SAML 構成](./media/kendis-scaling-agile-platform-tutorial/saml-configuration-settings.png)

    a. **[Callback URL]\(コールバック URL\)** の値をコピーし、その値を Azure portal の [基本的な SAML 構成] セクションの **[応答 URL]** ボックスに貼り付けます。

    b. **[Identity Provider Single Sign On URL]\(ID プロバイダーのシングル サインオン URL\)** ボックスに、Azure portal からコピーした **ログイン URL** の値を貼り付けます。

    c.  **[Identity Provider Issuer]\(ID プロバイダーの発行者\)** ボックスに、Azure portal からコピーした **Azure AD 識別子 (エンティティ ID)** の値を貼り付けます。

    d. Azure portal からダウンロードした **証明書 (Base64)** をメモ帳で開き、その内容を **[X.509 Certificate]\(X.509 証明書\)** ボックスに貼り付けます。

    e. オプションのリストから **[Default Group]\(既定のグループ\) を選択** します。 

    f. **[保存]** をクリックします。

### <a name="create-kendis-scaling-agile-platform-test-user"></a>Kendis - Scaling Agile Platform テスト ユーザーの作成

このセクションでは、Britta Simon というユーザーを Kendis - Scaling Agile Platform に作成します。 Kendis - Scaling Agile Platform では、Just-In-Time ユーザー プロビジョニングがサポートされています。これは既定で有効になっています。 このセクションでは、ユーザー側で必要な操作はありません。 Kendis - Scaling Agile Platform にユーザーがまだ存在していない場合は、認証後に新しく作成されます。

## <a name="test-sso"></a>SSO のテスト

このセクションでは、次のオプションを使用して Azure AD のシングル サインオン構成をテストします。 

#### <a name="sp-initiated"></a>SP Initiated:

* Azure portal で **[このアプリケーションをテストします]** をクリックします。 これにより、ログイン フローを開始できる Kendis - Scaling Agile Platform のサインオン URL にリダイレクトされます。  

* Kendis - Scaling Agile Platform のサインオン URL に直接移動し、そこからログイン フローを開始します。

#### <a name="idp-initiated"></a>IDP Initiated:

* Azure portal で **[このアプリケーションをテストします]** をクリックすると、SSO を設定した Kendis - Scaling Agile Platform に自動的にサインインされます 

また、Microsoft マイ アプリを使用して、任意のモードでアプリケーションをテストすることもできます。 マイ アプリで [Kendis - Scaling Agile Platform] タイルをクリックすると、SP モードで構成されている場合は、ログイン フローを開始するためのアプリケーション サインオン ページにリダイレクトされます。IDP モードで構成されている場合は、SSO を設定した Kendis - Scaling Agile Platform に自動的にサインインされます。 マイ アプリの詳細については、[マイ アプリの概要](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)に関するページを参照してください。


## <a name="next-steps"></a>次のステップ

Kendis - Scaling Agile Platform を構成したら、組織の機密データを流出と侵入からリアルタイムで保護するセッション制御を適用できます。 セッション制御は、条件付きアクセスを拡張したものです。 [Microsoft Cloud App Security でセッション制御を強制する方法](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app)をご覧ください。


