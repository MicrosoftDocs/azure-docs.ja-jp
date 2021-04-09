---
title: チュートリアル:Azure Active Directory シングル サインオン (SSO) と Synerise AI Growth Operating System の統合 | Microsoft Docs
description: Azure Active Directory と Synerise AI Growth Operating System の間にシングル サインオンを構成する方法について説明します。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/06/2021
ms.author: jeedes
ms.openlocfilehash: 0ffc6543a2597e39470c0d4aff86e7d5a1631d69
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "98736638"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-synerise-ai-growth-operating-system"></a>チュートリアル:Azure Active Directory シングル サインオン (SSO) と Synerise AI Growth Operating System の統合

このチュートリアルでは、Synerise と Azure Active Directory (Azure AD) を統合する方法について説明します。 Azure AD と Synerise を統合すると、次のことができます。

* Synerise にアクセスできるユーザーを Azure AD で制御できます。
* ユーザーが自分の Azure AD アカウントを使用して Synerise に自動的にサインインできるように設定できます。
* 1 つの中央サイト (Azure Portal) で自分のアカウントを管理します。

## <a name="prerequisites"></a>前提条件

開始するには、次が必要です。

* Azure AD サブスクリプション。 サブスクリプションがない場合は、[無料アカウント](https://azure.microsoft.com/free/)を取得できます。
* Synerise でのシングル サインオン (SSO) が有効なサブスクリプション。

## <a name="scenario-description"></a>シナリオの説明

このチュートリアルでは、テスト環境で Azure AD の SSO を構成してテストします。

* Synerise では、**SP Initiated SSO と IDP Initiated SSO** がサポートされます
* Synerise では、**Just-In-Time** ユーザー プロビジョニングがサポートされます

> [!NOTE]
> このアプリケーションの識別子は固定文字列値であるため、1 つのテナントで構成できるインスタンスは 1 つだけです。


## <a name="adding-synerise-ai-growth-operating-system-from-the-gallery"></a>ギャラリーからの Synerise AI Growth Operating System の追加

Azure AD への Synerise の統合を構成するには、ギャラリーからマネージド SaaS アプリの一覧に Synerise を追加する必要があります。

1. 職場または学校アカウントか、個人の Microsoft アカウントを使用して、Azure portal にサインインします。
1. 左のナビゲーション ウィンドウで **[Azure Active Directory]** サービスを選択します。
1. **[エンタープライズ アプリケーション]** に移動し、 **[すべてのアプリケーション]** を選択します。
1. 新しいアプリケーションを追加するには、 **[新しいアプリケーション]** を選択します。
1. **[ギャラリーから追加する]** セクションで、検索ボックスに「**Synerise AI Growth Operating System**」と入力します。
1. 結果のパネルから **Synerise AI Growth Operating System** を選択し、アプリを追加します。 お使いのテナントにアプリが追加されるのを数秒待機します。


## <a name="configure-and-test-azure-ad-sso-for-synerise-ai-growth-operating-system"></a>Synerise AI Growth Operating System の Azure AD SSO の構成とテスト

**B.Simon** というテスト ユーザーを使用して、Synerise に対する Azure AD SSO を構成してテストします。 SSO が機能するためには、Azure AD ユーザーと Synerise の関連ユーザーとの間にリンク関係を確立する必要があります。

Synerise に対する Azure AD SSO を構成してテストするには、次の手順を行います。

1. **[Azure AD SSO の構成](#configure-azure-ad-sso)** - ユーザーがこの機能を使用できるようにします。
    1. **[Azure AD のテスト ユーザーの作成](#create-an-azure-ad-test-user)** - B.Simon で Azure AD のシングル サインオンをテストします。
    1. **[Azure AD テスト ユーザーの割り当て](#assign-the-azure-ad-test-user)** - B.Simon が Azure AD シングル サインオンを使用できるようにします。
1. **[Synerise AI Growth Operating System の SSO の構成](#configure-synerise-ai-growth-operating-system-sso)** - アプリケーション側でシングル サインオン設定を構成します。
    1. **[Synerise AI Growth Operating System のテスト ユーザーの作成](#create-synerise-ai-growth-operating-system-test-user)** - Synerise で B.Simon に対応するユーザーを作成し、Azure AD の B.Simon にリンクさせます。
1. **[SSO のテスト](#test-sso)** - 構成が機能するかどうかを確認します。

## <a name="configure-azure-ad-sso"></a>Azure AD SSO の構成

これらの手順に従って、Azure portal で Azure AD SSO を有効にします。

1. Azure portal の **Synerise AI Growth Operating System** アプリケーション統合ページで、 **[管理]** セクションを見つけて、 **[シングル サインオン]** を選択します。
1. **[シングル サインオン方式の選択]** ページで、 **[SAML]** を選択します。
1. **[SAML によるシングル サインオンのセットアップ]** ページで、 **[基本的な SAML 構成]** の鉛筆アイコンをクリックして設定を編集します。

   ![基本的な SAML 構成を編集する](common/edit-urls.png)

1. **[基本的な SAML 構成]** セクションで、アプリケーションを **IDP** 開始モードで構成する場合は、次のフィールドの値を入力します。

    **[応答 URL]** ボックスに、`https://app.synerise.com/api-portal/uauth/saml/auth/<PROFILE_HASH>` のパターンを使用して URL を入力します

1. アプリケーションを **SP** 開始モードで構成する場合は、 **[追加の URL を設定します]** をクリックして次の手順を実行します。

    **[サインオン URL]** ボックスに、`https://app.synerise.com/api-portal/uauth/saml/auth/<PROFILE_HASH>` という形式で URL を入力します。

    > [!NOTE]
    > これらは実際の値ではありません。 実際の応答 URL とサインオン URL でこれらの値を更新します。 これらの値を取得するには、[Synerise サポート チーム](mailto:support@synerise.com)にお問い合わせください。 Azure portal の **[基本的な SAML 構成]** セクションに示されているパターンを参照することもできます。

1. **[SAML でシングル サインオンをセットアップします]** ページの **[SAML 署名証明書]** セクションで、 **[証明書 (Base64)]** を見つけて、 **[ダウンロード]** を選択し、証明書をダウンロードして、お使いのコンピューターに保存します。

    ![証明書のダウンロードのリンク](common/certificatebase64.png)

1. **[Synerise のセットアップ]** セクションで、要件に基づいて適切な URL をコピーします。

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

このセクションでは、B.Simon に Synerise へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

1. Azure portal で **[エンタープライズ アプリケーション]** を選択し、 **[すべてのアプリケーション]** を選択します。
1. アプリケーションの一覧で **[Synerise AI Growth Operating System]** を選択します。
1. アプリの概要ページで、 **[管理]** セクションを見つけて、 **[ユーザーとグループ]** を選択します。
1. **[ユーザーの追加]** を選択し、 **[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。
1. **[ユーザーとグループ]** ダイアログの [ユーザー] の一覧から **[B.Simon]** を選択し、画面の下部にある **[選択]** ボタンをクリックします。
1. ユーザーにロールが割り当てられることが想定される場合は、 **[ロールの選択]** ドロップダウンからそれを選択できます。 このアプリに対してロールが設定されていない場合は、[既定のアクセス] ロールが選択されていることを確認します。
1. **[割り当ての追加]** ダイアログで、 **[割り当て]** をクリックします。

## <a name="configure-synerise-ai-growth-operating-system-sso"></a>Synerise AI Growth Operating System の SSO の構成

1. 管理者として Synerise にログインします。

1. **[Settings]\(設定\) > [Access Control]\(アクセスの制御\)** に移動します。

    ![Synerise の設定](./media/synerise-ai-growth-ecosystem-tutorial/settings.png)

1. **[Access Control]\(アクセスの制御\)** ページの **[Single Sign-On]\(シングル サインオン\)** タブで、 **[Show]\(表示\)** ボタンをクリックします。

    ![Synerise のアクセスの制御](./media/synerise-ai-growth-ecosystem-tutorial/single-sign-on.png)

1. 次のページで以下の手順を実行します。

    ![Synerise の構成](./media/synerise-ai-growth-ecosystem-tutorial/configuration.png)

    a. **[Identifier Provider Entity ID]\(ID プロバイダー エンティティ ID\)** ボックスに、Azure portal からコピーした **Azure AD ID** の値を貼り付けます。

    b. **[SSO endpoint(https)]\(SSO エンドポイント (https)\)** ボックスに、Azure portal からコピーした **ログイン URL** の値を貼り付けます。

    c. **[Identity Provider application ID]\(ID プロバイダー アプリケーション ID\)** ボックスに、**アプリケーション ID** の値を貼り付けます。

    d. **[Service Provider redirect URI]\(サービス プロバイダーのリダイレクト URI\)** の値をコピーし、Azure portal の [基本的な SAML 構成] セクションにある **[応答 URL]** ボックスにその値を貼り付けます。

    e. **[Request binding]\(要求バインディング\)** の **[HTTP REDIRECT]\(HTTP リダイレクト\)** を選択します。

    f. **[Request signature]\(要求署名\)** をオンにします。

    g. ダウンロードした **証明書 (Base64)** ファイルを **[Identity Provider Signature Certificate]\(ID プロバイダー署名証明書\)** にアップロードします。

    i. **[適用]** をクリックします。

### <a name="create-synerise-ai-growth-operating-system-test-user"></a>Synerise AI Growth Operating System のテスト ユーザーの作成

このセクションでは、Britta Simon というユーザーを Synerise に作成します。 Synerise では、Just-In-Time ユーザー プロビジョニングがサポートされており、既定で有効になっています。 このセクションでは、ユーザー側で必要な操作はありません。 Synerise にユーザーがまだ存在していない場合は、認証後に新しく作成されます。

## <a name="test-sso"></a>SSO のテスト 

このセクションでは、次のオプションを使用して Azure AD のシングル サインオン構成をテストします。 

#### <a name="sp-initiated"></a>SP Initiated:

* Azure portal で **[このアプリケーションをテストします]** をクリックします。 これにより、ログイン フローを開始できる Synerise のサインオン URL にリダイレクトされます。  

* Synerise のサインオン URL に直接移動し、そこからログイン フローを開始します。

#### <a name="idp-initiated"></a>IDP Initiated:

* Azure portal で **[このアプリケーションをテストします]** をクリックすると、SSO を設定した Synerise に自動的にサインインされます 

また、Microsoft マイ アプリを使用して、任意のモードでアプリケーションをテストすることもできます。 マイ アプリで [Synerise] タイルをクリックすると、SP モードで構成されている場合は、ログイン フローを開始するためのアプリケーション サインオン ページにリダイレクトされます。IDP モードで構成されている場合は、SSO を設定した Synerise に自動的にサインインされます。 マイ アプリの詳細については、[マイ アプリの概要](../user-help/my-apps-portal-end-user-access.md)に関するページを参照してください。


## <a name="next-steps"></a>次のステップ

Synerise を構成したら、組織の機密データを流出と侵入からリアルタイムで保護するセッション制御を適用することができます。 セッション制御は、条件付きアクセスを拡張したものです。 [Microsoft Cloud App Security でセッション制御を強制する方法](/cloud-app-security/proxy-deployment-any-app)をご覧ください。