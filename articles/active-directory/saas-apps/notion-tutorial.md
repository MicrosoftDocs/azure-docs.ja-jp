---
title: チュートリアル:Azure Active Directory シングル サインオン (SSO) と Notion の統合 | Microsoft Docs
description: Azure Active Directory と Notion の間でシングル サインオンを構成する方法について確認します。
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
ms.openlocfilehash: 520eb25bcb138c96b24166816d3374255fb7c3b2
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "99493991"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-notion"></a>チュートリアル:Azure Active Directory シングル サインオン (SSO) と Notion の統合

このチュートリアルでは、Notion と Azure Active Directory (Azure AD) を統合する方法について説明します。 Azure AD と Notion を統合すると、次のことができます。

* Notion にアクセスできるユーザーを Azure AD で制御できます。
* ユーザーが自分の Azure AD アカウントを使用して Notion に自動的にサインインできるように設定できます。
* 1 つの中央サイト (Azure Portal) で自分のアカウントを管理します。

## <a name="prerequisites"></a>前提条件

開始するには、次が必要です。

* Azure AD サブスクリプション。 サブスクリプションがない場合は、[無料アカウント](https://azure.microsoft.com/free/)を取得できます。
* Notion のシングル サインオン (SSO) が有効なサブスクリプション。

## <a name="scenario-description"></a>シナリオの説明

このチュートリアルでは、テスト環境で Azure AD の SSO を構成してテストします。

* Notion では、**SP Initiated SSO と IDP Initiated SSO** がサポートされます
* Notion では、**Just-In-Time** ユーザー プロビジョニングがサポートされます
> [!NOTE]
> このアプリケーションの識別子は固定文字列値であるため、1 つのテナントで構成できる Notion ワークスペースは 1 つだけです。


## <a name="adding-notion-from-the-gallery"></a>ギャラリーからの Notion の追加

Azure AD への Notion の統合を構成するには、ギャラリーからマネージド SaaS アプリの一覧に Notion を追加する必要があります。

1. 職場または学校アカウントか、個人の Microsoft アカウントを使用して、Azure portal にサインインします。
1. 左のナビゲーション ウィンドウで **[Azure Active Directory]** サービスを選択します。
1. **[エンタープライズ アプリケーション]** に移動し、 **[すべてのアプリケーション]** を選択します。
1. 新しいアプリケーションを追加するには、 **[新しいアプリケーション]** を選択します。
1. **[ギャラリーから追加する]** セクションで、検索ボックスに「**Notion**」と入力します。
1. 結果のパネルから **[Notion]** を選択し、アプリを追加します。 お使いのテナントにアプリが追加されるのを数秒待機します。


## <a name="configure-and-test-azure-ad-sso-for-notion"></a>Notion の Azure AD SSO の構成とテスト

**B.Simon** というテスト ユーザーを使用して、Notion に対する Azure AD SSO を構成してテストします。 SSO が機能するためには、Azure AD ユーザーと Notion の関連ユーザーとの間にリンク関係を確立する必要があります。

Notion に対する Azure AD SSO を構成してテストするには、次の手順を行います。

1. **[Azure AD SSO の構成](#configure-azure-ad-sso)** - ユーザーがこの機能を使用できるようにします。
    1. **[Azure AD のテスト ユーザーの作成](#create-an-azure-ad-test-user)** - B.Simon で Azure AD のシングル サインオンをテストします。
    1. **[Azure AD テスト ユーザーの割り当て](#assign-the-azure-ad-test-user)** - B.Simon が Azure AD シングル サインオンを使用できるようにします。
1. **[Notion の SSO の構成](#configure-notion-sso)** - アプリケーション側でシングル サインオン設定を構成します。
    1. **[Notion のテスト ユーザーの作成](#create-notion-test-user)** - Notion で B.Simon に対応するユーザーを作成し、Azure AD の B.Simon にリンクさせます。
1. **[SSO のテスト](#test-sso)** - 構成が機能するかどうかを確認します。

## <a name="configure-azure-ad-sso"></a>Azure AD SSO の構成

これらの手順に従って、Azure portal で Azure AD SSO を有効にします。

1. Azure portal の **Notion** アプリケーション統合ページで、 **[管理]** セクションを見つけて、 **[シングル サインオン]** を選択します。
1. **[シングル サインオン方式の選択]** ページで、 **[SAML]** を選択します。
1. **[SAML によるシングル サインオンのセットアップ]** ページで、 **[基本的な SAML 構成]** の鉛筆アイコンをクリックして設定を編集します。

   ![基本的な SAML 構成を編集する](common/edit-urls.png)

1. **[基本的な SAML 構成]** セクションで、アプリケーションを **IDP** 開始モードで構成する場合は、次のフィールドの値を入力します。

    **[応答 URL]** ボックスに、`https://www.notion.so/sso/saml/<CUSTOM_ID>` のパターンを使用して URL を入力します。URL は、Notion ワークスペースの **[Settings & Members]\(設定 & メンバー\)** > **[Security & identity]\(セキュリティ & ID\)** > **[Single sign-on URL]\(シングル サインオン URL\)** から取得できます。

1. アプリケーションを **SP** 開始モードで構成する場合は、 **[追加の URL を設定します]** をクリックして次の手順を実行します。

    **[サインオン URL]** ボックスに、URL (`https://www.notion.so/login`) を入力します。

    > [!NOTE]
    > これらは実際の値ではありません。 実際の応答 URLとサインオン URL でこれらの値を更新します。 Azure portal の **[基本的な SAML 構成]** セクションに示されているパターンを参照することもできます。

1. Notion アプリケーションは、特定の形式の SAML アサーションを使用するため、カスタム属性のマッピングを SAML トークンの属性の構成に追加する必要があります。 次のスクリーンショットには、既定の属性一覧が示されています。

    ![image](common/default-attributes.png)

1. その他に、Notion アプリケーションでは、いくつかの属性が SAML 応答で返されることが想定されています。それらの属性を次に示します。 これらの属性も値が事前に設定されますが、要件に従ってそれらの値を確認することができます。
    
    | 名前 |  ソース属性|
    | ----------- | --------- |
    | email | User.mail |
    | firstName | User.givenname |
    | lastName | User.surname |


1. **[SAML でシングル サインオンをセットアップします]** ページの **[SAML 署名証明書]** セクションで、コピー ボタンをクリックして **[アプリのフェデレーション メタデータ URL]** をコピーします。 **Notion** ワークスペースの **[Settings & Members]\(設定 & メンバー\)**  >  **[Security & identity]\(セキュリティ & ID\)** に移動し、コピーした値を **[IDP metadata URL]\(IDP メタデータ URL\)** フィールドに貼り付けます。

    ![証明書のダウンロードのリンク](common/copy-metadataurl.png)

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

このセクションでは、B.Simon に Notion へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

1. Azure portal で **[エンタープライズ アプリケーション]** を選択し、 **[すべてのアプリケーション]** を選択します。
1. アプリケーションの一覧で **[Notion]** を選択します。
1. アプリの概要ページで、 **[管理]** セクションを見つけて、 **[ユーザーとグループ]** を選択します。
1. **[ユーザーの追加]** を選択し、 **[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。
1. **[ユーザーとグループ]** ダイアログの [ユーザー] の一覧から **[B.Simon]** を選択し、画面の下部にある **[選択]** ボタンをクリックします。
1. ユーザーにロールが割り当てられることが想定される場合は、 **[ロールの選択]** ドロップダウンからそれを選択できます。 このアプリに対してロールが設定されていない場合は、[既定のアクセス] ロールが選択されていることを確認します。
1. **[割り当ての追加]** ダイアログで、 **[割り当て]** をクリックします。

## <a name="configure-notion-sso"></a>Notion の SSO の構成

**Notion** ワークスペースの **[Settings & Members]\(設定 & メンバー\)**  >  **[Security & identity]\(セキュリティ & ID\)** に移動し、 **[アプリのフェデレーション メタデータ URL]** でコピーした値を **[IDP metadata URL]\(IDP メタデータ URL\)** フィールドに貼り付けます。

同じ設定ページで、 **[Email domains]\(メール ドメイン\)** の **[Contact support]\(サポートへの問い合わせ\)** をクリックして、組織のメール ドメインを追加します。

メール ドメインが承認され、追加されたら、 **[Enable SAML]\(SAML を有効にする\)** トグルを使用して SAML SSO を有効にします。

テストが成功したら、 **[Enforce SAML]\(SAML を適用する\)** トグルを使用して SAML SSO を適用できます。 Notion ワークスペース管理者は電子メールでログインする機能を保持しますが、他のすべてのメンバーは、SAML SSO を使用して Notion にログインする必要があることに注意してください。

### <a name="create-notion-test-user"></a>Notion のテスト ユーザーの作成

このセクションでは、Britta Simon というユーザーを Notion に作成します。 Notion では、Just-In-Time ユーザー プロビジョニングがサポートされています。この設定は既定で有効になっています。 このセクションでは、ユーザー側で必要な操作はありません。 Notion にユーザーがまだ存在していない場合は、認証後に新規に作成されます。

## <a name="test-sso"></a>SSO のテスト 

このセクションでは、次のオプションを使用して Azure AD のシングル サインオン構成をテストします。 

#### <a name="sp-initiated"></a>SP Initiated:

* Azure portal で **[このアプリケーションをテストします]** をクリックします。 これにより、ログイン フローを開始できる Notion のサインオン URL にリダイレクトされます。  

* Notion のサインオン URL に直接移動し、そこからログイン フローを開始します。

#### <a name="idp-initiated"></a>IDP Initiated:

* Azure portal で **[このアプリケーションをテストします]** をクリックすると、SSO を設定した Notion に自動的にサインインされます 

また、Microsoft マイ アプリを使用して、任意のモードでアプリケーションをテストすることもできます。 マイ アプリで [Notion] タイルをクリックすると、SP モードで構成されている場合は、ログイン フローを開始するためのアプリケーション サインオン ページにリダイレクトされます。IDP モードで構成されている場合は、SSO を設定した Notion に自動的にサインインされます。 マイ アプリの詳細については、[マイ アプリの概要](../user-help/my-apps-portal-end-user-access.md)に関するページを参照してください。


## <a name="next-steps"></a>次のステップ

Notion を構成したら、組織の機密データを流出と侵入からリアルタイムで保護するセッション制御を適用することができます。 セッション制御は、条件付きアクセスを拡張したものです。 [Microsoft Cloud App Security でセッション制御を強制する方法](/cloud-app-security/proxy-deployment-any-app)をご覧ください。