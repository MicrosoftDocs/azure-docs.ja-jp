---
title: チュートリアル:Azure Active Directory シングル サインオン (SSO) と Freshworks の統合 | Microsoft Docs
description: Azure Active Directory と Freshworks の間でシングル サインオンを構成する方法について説明します。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 02/11/2021
ms.author: jeedes
ms.openlocfilehash: 64a8907ec359235c2856b1f92388d9daa38cd190
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "101651724"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-freshworks"></a>チュートリアル:Azure Active Directory シングル サインオン (SSO) と Freshworks の統合

このチュートリアルでは、Freshworks と Azure Active Directory (Azure AD) を統合する方法について説明します。 Azure AD と Freshworks を統合すると、次のことができます。

* Freshworks にアクセスできるユーザーを Azure AD で制御できます。
* ユーザーが自分の Azure AD アカウントを使用して Freshworks に自動的にサインインできるように設定できます。
* 1 つの中央サイト (Azure Portal) で自分のアカウントを管理します。

## <a name="prerequisites"></a>前提条件

開始するには、次が必要です。

* Azure AD サブスクリプション。 サブスクリプションがない場合は、[無料アカウント](https://azure.microsoft.com/free/)を取得できます。
* Freshworks でのシングル サインオン (SSO) が有効なサブスクリプション

> [!NOTE]
> この統合は、Azure AD 米国政府クラウド環境から利用することもできます。 このアプリケーションは、Azure AD 米国政府クラウドのアプリケーション ギャラリーにあります。パブリック クラウドの場合と同じように構成してください。

## <a name="scenario-description"></a>シナリオの説明

このチュートリアルでは、テスト環境で Azure AD の SSO を構成してテストします。

* Freshworks では、**SP Initiated SSO と IDP Initiated SSO** がサポートされます

## <a name="add-freshworks-from-the-gallery"></a>ギャラリーからの Freshworks の追加

Azure AD への Freshworks の統合を構成するには、ギャラリーからマネージド SaaS アプリの一覧に Freshworks を追加する必要があります。

1. 職場または学校アカウントか、個人の Microsoft アカウントを使用して、Azure portal にサインインします。
1. 左のナビゲーション ウィンドウで **[Azure Active Directory]** サービスを選択します。
1. **[エンタープライズ アプリケーション]** に移動し、 **[すべてのアプリケーション]** を選択します。
1. 新しいアプリケーションを追加するには、 **[新しいアプリケーション]** を選択します。
1. **[ギャラリーから追加する]** セクションで、検索ボックスに「**Freshworks**」と入力します。
1. 結果パネルから「**Freshworks**」を選択し、アプリを追加します。 お使いのテナントにアプリが追加されるのを数秒待機します。

## <a name="configure-and-test-azure-ad-sso-for-freshworks"></a>Freshworks の Azure AD SSO の構成とテスト

**B.Simon** というテスト ユーザーを使用して、Freshworks に対する Azure AD SSO を構成してテストします。 SSO が機能するには、Azure AD ユーザーと Freshworks の関連ユーザーの間で、リンク関係を確立する必要があります。

Freshworks に対して Azure AD SSO を構成してテストするには、次の手順を行います。

1. **[Azure AD SSO の構成](#configure-azure-ad-sso)** - ユーザーがこの機能を使用できるようにします。
    1. **[Azure AD のテスト ユーザーの作成](#create-an-azure-ad-test-user)** - B.Simon で Azure AD のシングル サインオンをテストします。
    1. **[Azure AD テスト ユーザーの割り当て](#assign-the-azure-ad-test-user)** - B.Simon が Azure AD シングル サインオンを使用できるようにします。
1. **[Freshworks の SSO の構成](#configure-freshworks-sso)** - アプリケーション側でシングル サインオン設定を構成します。
    1. **[Freshworks のテスト ユーザーの作成](#create-freshworks-test-user)** - Freshworks で B.Simon に対応するユーザーを作成し、Azure AD の B.Simon にリンクさせます。
1. **[SSO のテスト](#test-sso)** - 構成が機能するかどうかを確認します。

## <a name="configure-azure-ad-sso"></a>Azure AD SSO の構成

これらの手順に従って、Azure portal で Azure AD SSO を有効にします。

1. Azure portal の **Freshworks** アプリケーション統合ページで、 **[管理]** セクションを見つけて、 **[シングル サインオン]** を選択します。
1. **[シングル サインオン方式の選択]** ページで、 **[SAML]** を選択します。
1. **[SAML によるシングル サインオンのセットアップ]** ページで、 **[基本的な SAML 構成]** の鉛筆アイコンをクリックして設定を編集します。

   ![基本的な SAML 構成を編集する](common/edit-urls.png)

1. **[基本的な SAML 構成]** セクションで、アプリケーションを **IDP** 開始モードで構成する場合は、次のフィールドの値を入力します。

    a. **[識別子 (エンティティ ID)]** ボックスに、次のパターンを使用して URL を入力します。`https://<SUBDOMAIN>.freshworks.com/sp/SAML/<MODULE_ID>/metadata`

    b. **[応答 URL]** ボックスに、`https://<SUBDOMAIN>.freshworks.com/sp/SAML/CUSTOM_URL` のパターンを使用して URL を入力します

1. アプリケーションを **SP** 開始モードで構成する場合は、 **[追加の URL を設定します]** をクリックして次の手順を実行します。

    **[サインオン URL]** ボックスに、`https://<SUBDOMAIN>.freshworks.com/login` という形式で URL を入力します。

    > [!NOTE]
    > これらは実際の値ではありません。 これらの値は、実際の識別子、応答 URL、サインオン URL で更新してください。 これらの値を取得するには、[Freshworks クライアント サポート チーム](mailto:support@freshworks.com)にお問い合わせください。 Azure portal の **[基本的な SAML 構成]** セクションに示されているパターンを参照することもできます。

1. **[SAML でシングル サインオンをセットアップします]** ページの **[SAML 署名証明書]** セクションで、 **[証明書 (Base64)]** を見つけて、 **[ダウンロード]** を選択し、証明書をダウンロードして、お使いのコンピューターに保存します。

    ![証明書のダウンロードのリンク](common/certificatebase64.png)

1. **[署名]** オプションを要件に応じて変更するには、**[編集]** ボタンをクリックして **[SAML 署名証明書]** ダイアログを開きます。

     ![image](common/edit-certificate.png)

     ![[Edit]\(編集\) ボタンが選択されている [S A M L Signing Certificate]\(S A M L 署名証明書\) ダイアログを示すスクリーンショット。](./media/freshworks-tutorial/response.png)

    a. **[署名オプション]** として **[SAML 応答への署名]** を選択します。

    b. **[保存]** をクリックします。

1. **[Freshworks のセットアップ]** セクションで、要件に基づいて適切な URL をコピーします。

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

このセクションでは、B.Simon に Freshworks へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

1. Azure portal で **[エンタープライズ アプリケーション]** を選択し、 **[すべてのアプリケーション]** を選択します。
1. アプリケーションの一覧で **[Freshworks]** を選択します。
1. アプリの概要ページで、 **[管理]** セクションを見つけて、 **[ユーザーとグループ]** を選択します。
1. **[ユーザーの追加]** を選択し、 **[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。
1. **[ユーザーとグループ]** ダイアログの [ユーザー] の一覧から **[B.Simon]** を選択し、画面の下部にある **[選択]** ボタンをクリックします。
1. ユーザーにロールが割り当てられることが想定される場合は、 **[ロールの選択]** ドロップダウンからそれを選択できます。 このアプリに対してロールが設定されていない場合は、[既定のアクセス] ロールが選択されていることを確認します。
1. **[割り当ての追加]** ダイアログで、 **[割り当て]** をクリックします。

## <a name="configure-freshworks-sso"></a>Freshworks の SSO の構成

1. 新しい Web ブラウザー ウィンドウを開き、Freshworks 企業サイトに管理者としてサインインして、次の手順を実行します。

2. 左側のメニューから **[Security]\(セキュリティ\)** アイコンをクリックし、 **[Single sign-on]\(シングル サインオン\)** オプションをオンにして、 **[Authentication Methods]\(認証方法\)** の **[SAML SSO]** を選択します。

    ![[Single sign-on]\(シングル サインオン\) オプションがオンになり、[S A M L S S O] が選択されている [Security - Authentication Methods]\(セキュリティ - 認証方法\) セクションを示すスクリーンショット。](./media/freshworks-tutorial/configure01.png)

3. **[Single sign-on]\(シングル サインオン\)** セクションで、次の手順に従います。

    ![Freshworks の構成](./media/freshworks-tutorial/configure02.png)

    a. **[Copy]\(コピー\)** をクリックして、ご利用のインスタンスの **[Service Provider(SP) Entity ID]\(サービス プロバイダー (SP) のエンティティ ID\)** をコピーし、Azure portal の **[基本的な SAML 構成]** セクションにある **[識別子 (エンティティ ID)]** ボックスに貼り付けます。

    b. **[Entity ID provided by the IdP]\(IdP から提供されたエンティティ ID\)** ボックスに、Azure portal からコピーした **[Azure AD 識別子]** の値を貼り付けます。

    c. **[SAML SSO URL]** ボックスに、Azure portal からコピーした **ログイン URL** の値を貼り付けます。

    d. Base64 でエンコードされた証明書をメモ帳で開き、その内容をコピーして **[セキュリティ証明書]** ボックスに貼り付けます。

    e. **[保存]** をクリックします。

### <a name="create-freshworks-test-user"></a>Freshworks のテスト ユーザーの作成

このセクションでは、Freshworks で B.Simon というユーザーを作成します。 [Freshworks クライアント サポート チーム](mailto:support@freshworks.com)と連携して、Freshworks プラットフォームにユーザーを追加してください。 シングル サインオンを使用する前に、ユーザーを作成し、有効化する必要があります。 

## <a name="test-sso"></a>SSO のテスト 

このセクションでは、次のオプションを使用して Azure AD のシングル サインオン構成をテストします。 

#### <a name="sp-initiated"></a>SP Initiated:

* Azure portal で **[このアプリケーションをテストします]** をクリックします。 これにより、ログイン フローを開始できる Freshworks のサインオン URL にリダイレクトされます。  

* Freshworks のサインオン URL に直接移動し、そこからログイン フローを開始します。

#### <a name="idp-initiated"></a>IDP Initiated:

* Azure portal で **[このアプリケーションをテストします]** をクリックすると、SSO を設定した Freshworks に自動的にサインインされます 

また、Microsoft マイ アプリを使用して、任意のモードでアプリケーションをテストすることもできます。 マイ アプリで [Freshworks] タイルをクリックすると、SP モードで構成されている場合は、ログイン フローを開始するためのアプリケーション サインオン ページにリダイレクトされます。IDP モードで構成されている場合は、SSO を設定した Freshworks に自動的にサインインされます。 マイ アプリの詳細については、[マイ アプリの概要](../user-help/my-apps-portal-end-user-access.md)に関するページを参照してください。


## <a name="next-steps"></a>次のステップ

Freshworks を構成したら、ご自分の組織の機密データの流出と侵入をリアルタイムで保護するセッション制御を適用することができます。 セッション制御は、条件付きアクセスを拡張したものです。 [Microsoft Cloud App Security でセッション制御を強制する方法](/cloud-app-security/proxy-deployment-any-app)をご覧ください。