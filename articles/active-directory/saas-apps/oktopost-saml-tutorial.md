---
title: チュートリアル:Azure Active Directory シングル サインオン (SSO) と Oktopost SAML の統合 | Microsoft Docs
description: Azure Active Directory と Oktopost SAML の間でシングル サインオンを構成する方法について説明します。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/05/2021
ms.author: jeedes
ms.openlocfilehash: e71f991037c0d725a2a03ecd8c1ff4ebbd4d7810
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "98735934"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-oktopost-saml"></a>チュートリアル:Azure Active Directory シングル サインオン (SSO) と Oktopost SAML の統合

このチュートリアルでは、Oktopost SAML と Azure Active Directory (Azure AD) を統合する方法について説明します。 Azure AD と Oktopost SAML を統合すると、次のことができます。

* Oktopost SAML にアクセスできるユーザーを Azure AD で制御できます。
* ユーザーが自分の Azure AD アカウントを使用して Oktopost SAML に自動的にサインインできるようになります。
* 1 つの中央サイト (Azure Portal) で自分のアカウントを管理します。

## <a name="prerequisites"></a>前提条件

開始するには、次が必要です。

* Azure AD サブスクリプション。 サブスクリプションがない場合は、[無料アカウント](https://azure.microsoft.com/free/)を取得できます。
* Oktopost SAML でのシングル サインオン (SSO) が有効なサブスクリプション。

## <a name="scenario-description"></a>シナリオの説明

このチュートリアルでは、テスト環境で Azure AD の SSO を構成してテストします。

* Oktopost SAML では、**SP Initiated SSO と IDP Initiated SSO** がサポートされます

> [!NOTE]
> このアプリケーションの識別子は固定文字列値であるため、1 つのテナントで構成できるインスタンスは 1 つだけです。


## <a name="adding-oktopost-saml-from-the-gallery"></a>ギャラリーからの Oktopost SAML の追加

Azure AD への Oktopost SAML の統合を構成するには、ギャラリーからマネージド SaaS アプリの一覧に Oktopost SAML を追加する必要があります。

1. 職場または学校アカウントか、個人の Microsoft アカウントを使用して、Azure portal にサインインします。
1. 左のナビゲーション ウィンドウで **[Azure Active Directory]** サービスを選択します。
1. **[エンタープライズ アプリケーション]** に移動し、 **[すべてのアプリケーション]** を選択します。
1. 新しいアプリケーションを追加するには、 **[新しいアプリケーション]** を選択します。
1. **[ギャラリーから追加する]** セクションで、検索ボックスに「**Oktopost SAML**」と入力します。
1. 結果のパネルから **Oktopost SAML** を選択し、アプリを追加します。 お使いのテナントにアプリが追加されるのを数秒待機します。


## <a name="configure-and-test-azure-ad-sso-for-oktopost-saml"></a>Oktopost SAML の Azure AD SSO の構成とテスト

**B.Simon** というテスト ユーザーを使用して、Oktopost SAML に対する Azure AD SSO を構成してテストします。 SSO が機能するためには、Azure AD ユーザーと Oktopost SAML の関連ユーザーとの間にリンク関係を確立する必要があります。

Oktopost SAML に対する Azure AD SSO を構成してテストするには、次の手順を行います。

1. **[Azure AD SSO の構成](#configure-azure-ad-sso)** - ユーザーがこの機能を使用できるようにします。
    1. **[Azure AD のテスト ユーザーの作成](#create-an-azure-ad-test-user)** - B.Simon で Azure AD のシングル サインオンをテストします。
    1. **[Azure AD テスト ユーザーの割り当て](#assign-the-azure-ad-test-user)** - B.Simon が Azure AD シングル サインオンを使用できるようにします。
1. **[Oktopost SAML の SSO の構成](#configure-oktopost-saml-sso)** - アプリケーション側でシングル サインオン設定を構成します。
    1. **[Oktopost SAML のテスト ユーザーの作成](#create-oktopost-saml-test-user)** - Oktopost SAML で B.Simon に対応するユーザーを作成し、Azure AD の B.Simon にリンクさせます。
1. **[SSO のテスト](#test-sso)** - 構成が機能するかどうかを確認します。

## <a name="configure-azure-ad-sso"></a>Azure AD SSO の構成

これらの手順に従って、Azure portal で Azure AD SSO を有効にします。

1. Azure portal の **Oktopost SAML** アプリケーション統合ページで、 **[管理]** セクションを見つけて、 **[シングル サインオン]** を選択します。
1. **[シングル サインオン方式の選択]** ページで、 **[SAML]** を選択します。
1. **[SAML によるシングル サインオンのセットアップ]** ページで、 **[基本的な SAML 構成]** の鉛筆アイコンをクリックして設定を編集します。

   ![基本的な SAML 構成を編集する](common/edit-urls.png)

1. アプリは Azure と事前に統合済みであるため、 **[基本的な SAML 構成]** セクションで実行が必要な手順はありません。

1. アプリケーションを **SP** 開始モードで構成する場合は、 **[追加の URL を設定します]** をクリックして次の手順を実行します。

    **[サインオン URL]** テキスト ボックスに、URL として「`https://app.oktopost.com/auth/login`」と入力します。


1. **[保存]** をクリックします。

1. **[SAML でシングル サインオンをセットアップします]** ページの **[SAML 署名証明書]** セクションで、 **[証明書 (Base64)]** を見つけて、 **[ダウンロード]** を選択し、証明書をダウンロードして、お使いのコンピューターに保存します。

    ![証明書のダウンロードのリンク](common/certificatebase64.png)

1. **[Oktopost SAML のセットアップ]** セクションで、要件に基づいて適切な URL をコピーします。

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

このセクションでは、B.Simon に Oktopost SAML へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

1. Azure portal で **[エンタープライズ アプリケーション]** を選択し、 **[すべてのアプリケーション]** を選択します。
1. アプリケーションの一覧で **[Oktopost SAML]** を選択します。
1. アプリの概要ページで、 **[管理]** セクションを見つけて、 **[ユーザーとグループ]** を選択します。
1. **[ユーザーの追加]** を選択し、 **[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。
1. **[ユーザーとグループ]** ダイアログの [ユーザー] の一覧から **[B.Simon]** を選択し、画面の下部にある **[選択]** ボタンをクリックします。
1. ユーザーにロールが割り当てられることが想定される場合は、 **[ロールの選択]** ドロップダウンからそれを選択できます。 このアプリに対してロールが設定されていない場合は、[既定のアクセス] ロールが選択されていることを確認します。
1. **[割り当ての追加]** ダイアログで、 **[割り当て]** をクリックします。

## <a name="configure-oktopost-saml-sso"></a>Oktopost SAML の SSO の構成

1. 管理者として Oktopost SAML にログインします。

1. **ユーザー アイコン > [Settings]\(設定\)** をクリックします。

    ![Oktopost SAML の [Settings]\(設定\)](./media/oktopost-saml-tutorial/settings.png)

1. **[Settings]\(設定\)** の **[Security]\(セキュリティ\) > [Single Sign-on]\(シングル サインオン\)** ページに移動し、次の手順を実行します。

    ![Oktopost SAML の構成](./media/oktopost-saml-tutorial/configure-sso.png)

    a. **[Enable Single Sign-on]\(シングル サインオンを有効にする\)** で **[Yes]\(はい\)** を選択します。

    b. **[SAML Endpoint]\(SAML エンドポイント\)** ボックスに、Azure portal からコピーした **ログイン URL** の値を貼り付けます。

    c. **[発行者]** ボックスに、Azure portal からコピーした **Azure AD ID** の値を貼り付けます。

    d. Azure portal からダウンロードした **証明書 (Base64)** をメモ帳で開き、その内容を **[X.509 Certificate]\(X.509 証明書\)** ボックスに貼り付けます。
    
    e. **[保存]** をクリックします。

### <a name="create-oktopost-saml-test-user"></a>Oktopost SAML のテスト ユーザーの作成

1. 管理者として Oktopost SAML にログインします。

1. **ユーザー アイコン > [Settings]\(設定\)** をクリックします。

    ![Oktopost SAML のテスト ユーザー 1](./media/oktopost-saml-tutorial/settings.png)

1.  **[User Management]\(ユーザー管理\) > [Users]\(ユーザー\) > [Add User]\(ユーザーの追加\)** に移動します。

    ![Oktopost SAML のテスト ユーザー 2](./media/oktopost-saml-tutorial/add-user-1.png)

1. ポップアップの必須フィールドに入力し、 **[Send]\(送信\)** をクリックします。

    ![Oktopost SAML のテスト ユーザー 3](./media/oktopost-saml-tutorial/add-user-2.png)

## <a name="test-sso"></a>SSO のテスト 

このセクションでは、次のオプションを使用して Azure AD のシングル サインオン構成をテストします。 

#### <a name="sp-initiated"></a>SP Initiated:

* Azure portal で **[このアプリケーションをテストします]** をクリックします。 これにより、ログイン フローを開始できる Oktopost SAML のサインオン URL にリダイレクトされます。  

* Oktopost SAML のサインオン URL に直接移動し、そこからログイン フローを開始します。

#### <a name="idp-initiated"></a>IDP Initiated:

* Azure portal で **[このアプリケーションをテストします]** をクリックすると、SSO を設定した Oktopost SAML に自動的にサインインされます 

また、Microsoft マイ アプリを使用して、任意のモードでアプリケーションをテストすることもできます。 マイ アプリで [Oktopost SAML] タイルをクリックすると、SP モードで構成されている場合は、ログイン フローを開始するためのアプリケーション サインオン ページにリダイレクトされます。IDP モードで構成されている場合は、SSO を設定した Oktopost SAML に自動的にサインインされます。 マイ アプリの詳細については、[マイ アプリの概要](../user-help/my-apps-portal-end-user-access.md)に関するページを参照してください。


## <a name="next-steps"></a>次のステップ

Oktopost SAML を構成したら、組織の機密データを流出と侵入からリアルタイムで保護するセッション制御を適用することができます。 セッション制御は、条件付きアクセスを拡張したものです。 [Microsoft Cloud App Security でセッション制御を強制する方法](/cloud-app-security/proxy-deployment-any-app)をご覧ください。