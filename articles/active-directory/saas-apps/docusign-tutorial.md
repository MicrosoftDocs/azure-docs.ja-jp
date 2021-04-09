---
title: チュートリアル:Azure Active Directory シングル サインオン (SSO) と DocuSign の統合 | Microsoft Docs
description: Azure Active Directory と DocuSign の間でシングル サインオン (SSO) を構成する方法について説明します。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 09/09/2020
ms.author: jeedes
ms.openlocfilehash: 00d4381c7af7fdf82ee1e895072d92d1e641f8c4
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "92454696"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-docusign"></a>チュートリアル:Azure Active Directory シングル サインオン (SSO) と DocuSign の統合

このチュートリアルでは、DocuSign と Microsoft Azure Active Directory (Azure AD) を統合する方法について説明します。 Azure AD と DocuSign を統合すると、次のことができます。

* DocuSign にアクセスできるユーザーを Azure AD を使用して管理できます。
* ユーザーが自分の Azure AD アカウントを使用して DocuSign に自動的にサインインできるように設定できます。
* 1 つの中央サイト (Azure Portal) でアカウントを管理できます。

## <a name="prerequisites"></a>前提条件

開始するには、次が必要です。

* Azure AD サブスクリプション。 サブスクリプションがない場合は、[無料アカウント](https://azure.microsoft.com/free/)を取得できます。
* シングル サインオン (SSO) が有効な DocuSign サブスクリプション。

> [!NOTE]
> この統合は、Azure AD 米国政府クラウド環境から利用することもできます。 このアプリケーションは、Azure AD 米国政府クラウドのアプリケーション ギャラリーにあります。パブリック クラウドの場合と同じように構成してください。

## <a name="scenario-description"></a>シナリオの説明

このチュートリアルでは、テスト環境で Azure AD の SSO を構成、テストして、次のことを確認します。

* DocuSign では、**SP** (Service Provider) Initiated SSO がサポートされます。

* DocuSign では、**Just-In-Time** ユーザー プロビジョニングがサポートされます。

* DocuSign では、[自動ユーザー プロビジョニング](./docusign-provisioning-tutorial.md)がサポートされます。

* DocuSign を構成したら、組織の機密データを流出と侵入からリアルタイムで保護するセッション制御を適用することができます。 セッション制御は、条件付きアクセスを拡張したものです。 [Microsoft Cloud App Security でセッション制御を適用する方法](/cloud-app-security/proxy-deployment-aad)をご覧ください。

## <a name="adding-docusign-from-the-gallery"></a>ギャラリーからの DocuSign の追加

Azure AD への DocuSign の統合を構成するには、ギャラリーからマネージド SaaS アプリの一覧に DocuSign を追加する必要があります。

1. 職場または学校アカウントを使用するか、個人用 Microsoft アカウントを使用して、Azure portal にサインインします。
1. 左側のナビゲーション ウィンドウで **[Azure Active Directory]** サービスを選択します。
1. **[エンタープライズ アプリケーション]** に移動し、 **[すべてのアプリケーション]** を選択します。
1. 新しいアプリケーションを追加するには、 **[新しいアプリケーション]** を選択します。
1. **[ギャラリーから追加する]** セクションで、検索ボックスに「**DocuSign**」と入力します。
1. 結果のパネルから **[DocuSign]** を選択し、アプリを追加します。 お使いのテナントにアプリが追加されるのを数秒待機します。


## <a name="configure-and-test-azure-ad-sso-for-docusign"></a>Azure AD SSO for DocuSign を構成し、テストする

**B.Simon** というテスト ユーザーを使用して、DocuSign に対する Azure AD SSO を構成してテストします。 SSO を機能させるためには、Azure AD ユーザーと DocuSign 内の対応するユーザーとの間にリンク関係を確立する必要があります。

DocuSign に対して Azure AD SSO を構成してテストするには、次の手順を行います。

1. [Azure AD SSO を構成](#configure-azure-ad-sso)して、その機能をユーザーが使用できるようにします。
    1. [Azure AD のテスト ユーザーを作成](#create-an-azure-ad-test-user)して、B.Simon を使って Azure AD のシングル サインオンをテストします。
    1. [Azure AD テスト ユーザーを割り当て](#assign-the-azure-ad-test-user)て、B.Simon が Azure AD シングル サインオンを使用できるようにします。
1. [DocuSign の SSO を構成](#configure-docusign-sso)して、アプリケーション側でシングル サインオン設定を構成します。
    1. [DocuSign のテスト ユーザーを作成](#create-docusign-test-user)して、DocuSign で B.Simon に対応するユーザーを生成し、Azure AD の B.Simon にリンクさせます。
1. [SSO のテスト](#test-sso)。構成が機能することを確認します。

## <a name="configure-azure-ad-sso"></a>Azure AD SSO の構成

Azure portal で Azure AD SSO を有効にするには、これらの手順を実行します。

1. Azure portal の **DocuSign** アプリケーション統合ページで、 **[管理]** セクションを探して、 **[シングル サインオン]** を選択します。
1. **[シングル サインオン方式の選択]** ページで、 **[SAML]** を選択します。
1. **[SAML でシングル サインオンをセットアップします]** ページで、 **[基本的な SAML 構成]** のペン アイコンを選択して設定を編集します。

   ![基本的な SAML 構成を編集する](common/edit-urls.png)

1. **[基本的な SAML 構成]** セクションで、次の手順を実行します。

    a. **[サインオン URL]** ボックスに、次のパターンを使用して URL を入力します:

    `https://<subdomain>.docusign.com/organizations/<OrganizationID>/saml2/login/sp/<IDPID>`

    b. **[識別子 (エンティティ ID)]** ボックスに、次のパターンを使用して URL を入力します:

    `https://<subdomain>.docusign.com/organizations/<OrganizationID>/saml2`

    c. **[応答 URL]** ボックスでは、次の URL パターンのいずれかを入力します。
    
    | [応答 URL] |
    |-------------|
    |`https://<subdomain>.docusign.com/organizations/<OrganizationID>/saml2/login/<IDPID>`|
    |`https://<subdomain>.docusign.net/SAML/`|

    > [!NOTE]
    > かっこで囲まれたこれらの値はプレースホルダーです。 これらを実際のサインオン URL、識別子、および返信 URL の値に置き換えてください。 これらの詳細は、このチュートリアルの後ろにある [View SAML 2.0 Endpoints]\(SAML 2.0 エンドポイントの表示\) に関するセクションで説明されています。

1. **[SAML でシングル サインオンをセットアップします]** ページの **[SAML 署名証明書]** セクションで、 **[証明書 (Base64)]** を見つけます。 **[ダウンロード]** を選択して証明書をダウンロードし、コンピューターに保存します。

    ![証明書のダウンロードのリンク](common/certificatebase64.png)

1. **[DocuSign のセットアップ]** セクションで、実際の要件に基づいて適切な URL をコピーします。

    ![構成 URL のコピー](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Azure AD のテスト ユーザーの作成

このセクションでは、Azure portal で B. Simon というテスト ユーザーを作成します。

1. Azure portal の左側のウィンドウで、 **[Azure Active Directory]** 、 **[ユーザー]** 、 **[すべてのユーザー]** の順に選択します。
1. 画面の上部にある **[新しいユーザー]** を選択します。
1. **[ユーザー]** プロパティで、以下の手順を実行します。
   1. **[名前]** フィールドに「**B.Simon**」と入力します。  
   1. **[ユーザー名]** フィールドに「`<username>@<companydomain>.<extension>`」と入力します。 (例: `B.Simon@contoso.com`)。
   1. **[パスワードを表示]** チェック ボックスをオンにし、 **[パスワード]** ボックスに表示された値を書き留めます。
   1. **［作成］** を選択します

### <a name="assign-the-azure-ad-test-user"></a>Azure AD テスト ユーザーの割り当て

このセクションでは、B.Simon が Azure シングル サインオンを使用できるよう、このユーザーに DocuSign へのアクセスを許可します。

1. Azure portal で **[エンタープライズ アプリケーション]** を選択し、 **[すべてのアプリケーション]** を選択します。
1. アプリケーションの一覧で **[DocuSign]** を選択します。
1. アプリの概要ページで、 **[管理]** セクションを見つけて、 **[ユーザーとグループ]** を選択します。
1. **[ユーザーの追加]** を選択し、 **[割り当ての追加]** ダイアログ ボックスで **[ユーザーとグループ]** を選択します。
1. **[ユーザーとグループ]** ダイアログ ボックスの **[ユーザー]** の一覧で **[B.Simon]** を選択し、画面の下部にある **[選択]** ボタンを押します。
1. ユーザーにロールが割り当てられることが想定される場合は、 **[ロールの選択]** ドロップダウンからそれを選択できます。 このアプリに対してロールが設定されていない場合は、[既定のアクセス] ロールが選択されていることを確認します。
1. **[割り当ての追加]** ダイアログ ボックスで、 **[割り当て]** ボタンを選択します。

## <a name="configure-docusign-sso"></a>DocuSign の SSO の構成

1. DocuSign 内での構成を自動化するには、 **[拡張機能のインストール]** を選択して My Apps Secure Sign-in ブラウザー拡張機能をインストールする必要があります。

    ![マイ アプリの拡張機能](common/install-myappssecure-extension.png)

2. ブラウザーに拡張機能を追加したら、 **[Setup DocuSign]\(DocuSign の設定\)** を選択します。 DocuSign アプリケーションに誘導されます。 そこから、管理者の資格情報を入力して DocuSign にサインインします。 ブラウザー拡張機能によりアプリケーションが自動的に構成され、手順 3. から 5. が自動化されます。

    ![セットアップの構成](common/setup-sso.png)

3. DocuSign を手動でセットアップしたい場合は、新しい Web ブラウザー ウィンドウを開き、管理者として DocuSign 企業サイトにサインインします。

4. ページの右上隅にあるプロフィールのロゴを選択し、 **[Go to Admin]\(管理に移動\)** を選択します。
  
    ![プロフィールの [Go to Admin]\(管理に移動\)][51]

5. ドメインのソリューション ページで **[Domains]\(ドメイン\)** を選択します。

    ![ドメインのソリューションの [Domains]\(ドメイン\)][50]

6. **[Domains]\(ドメイン\)** セクションの **[CLAIM DOMAIN]\(ドメインの要求\)** を選択します。

    ![[Claim Domain]\(ドメインの要求\) オプション][52]

7. **[Claim a Domain]\(ドメインの要求\)** ダイアログ ボックスの **[Domain Name]\(ドメイン名\)** ボックスに、所属する会社のドメインを入力してから、 **[CLAIM]\(要求\)** を選択します。 ドメインを確認し、その状態がアクティブであることを確かめてください。

    ![[Claim a Domain]\(ドメインの要求\) の [Domain Name]\(ドメイン名\) ダイアログ][53]

8. ドメインのソリューション ページで **[Identity Providers]\(ID プロバイダー\)** を選択します。
  
    ![[Identity Providers]\(ID プロバイダー\) オプション][54]

9. **[Identity Provider]\(ID プロバイダー\)** セクションで、 **[ADD IDENTITY PROVIDER]\(ID プロバイダー\)** を選択します。

    ![[Add Identity Provider]\(ID プロバイダー\) オプション][55]

10. **[Identity Provider Settings]\(ID プロバイダーの設定\)** ページで、次の手順を実行します。

    ![[Identity Provider Settings]\(ID プロバイダーの設定\) のフィールド][56]

    a. **[Name]\(名前\)** ボックスに、構成の一意の名前を入力します。 スペースは使用しないでください。

    b. **[Identity Provider Issuer]\(ID プロバイダーの発行者\) ボックス** に、Azure portal からコピーした **[Azure AD 識別子]** の値を貼り付けます。

    c. **[Identity Provider Login URL]\(ID プロバイダーのログイン URL\)** ボックスに、Azure portal からコピーした **ログイン URL** の値を貼り付けます。

    d. **[Identity Provider Logout URL]\(ID プロバイダーのログアウト URL\)** ボックスに、Azure portal からコピーした **ログアウト URL** の値を貼り付けます。

    e. **[Sign AuthN request]\(AuthN 要求に署名する\)** を選択します。

    f. **[Send AuthN request by]\(認証要求の送信方法\)** に **[POST]** を選択します。

    g. **[Send logout request by]\(ログアウト要求の送信方法\)** に **[GET]** を選択します。

    h. **[Custom Attribute Mapping]\(カスタム属性のマッピング\)** セクションの **[ADD NEW MAPPING]\(新しいマッピングの追加\)** を選択します。

       ![[Custom Attribute Mapping]\(カスタム属性のマッピング\) UI][62]

    i. Azure AD の要求にマッピングするフィールドを選択します。 この例では、**emailaddress** 要求は `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress` の値とマップされます。 これは、Azure AD の電子メール要求の既定の要求名です。 **[SAVE]\(保存\)** を選択します。

       ![[Custom Attribute Mapping]\(カスタム属性のマッピング\) のフィールド][57]

       > [!NOTE]
       > Azure AD のユーザーを DocuSign のユーザー マッピングにマッピングする際は、適切な **ユーザー識別子** を使用してください。 適切なフィールドを選択し、組織の設定に基づく適切な値を入力してください。

    j. **[Identity Provider Certificate]\(ID プロバイダー証明書\)** セクションで **[ADD CERTIFICATE]\(証明書の追加\)** を選択し、Azure AD ポータルからダウンロードした証明書をアップロードし、 **[SAVE]\(保存\)** を選択します。

       ![[Identity Provider Certificate]\(ID プロバイダー証明書\) の [Add Certificate]\(証明書の追加\)][58]

    k. **[Identity Providers]\(ID プロバイダー\)** セクションで、 **[ACTIONS]\(アクション\)** を選択し、 **[Endpoints]\(エンドポイント\)** を選択します。

       ![[Identity Providers]\(ID プロバイダー\) の [Endpoints]\(エンドポイント\)][59]

    l. DocuSign 管理ポータルの **[View SAML 2.0 Endpoints]\(SAML 2.0 エンドポイントの表示\)** セクションで、これらの手順を実行します。

       ![[View SAML 2.0 Endpoints]\(SAML 2.0 エンドポイントの表示\)][60]
       
       1. **[Service Provider Issuer URL]\(サービス プロバイダー発行者 URL\)** をコピーし、Azure portal の **[基本的な SAML 構成]** セクションの **[識別子]** ボックスに貼り付けます。
       
       1. **[Service Provider Assertion Consumer Service URL]\(サービス プロバイダー アサーション コンシューマー サービス URL\)** 値をコピーし、Azure portal の **[基本的な SAML 構成]** セクションの **[応答 URL]** ボックスに貼り付けます。
       
       1. **[Service Provider Login URL]\(サービス プロバイダーのログイン URL\)** をコピーし、Azure portal の **[基本的な SAML 構成]** セクションの **[サインオン URL]** ボックスに貼り付けます。 **[Service Provider Login URL]\(サービス プロバイダーのログイン URL\)** 値の末尾に IDPID 値があります。

       1. **[閉じる]** を選択します。

### <a name="create-docusign-test-user"></a>DocuSign のテスト ユーザーの作成

このセクションでは、B. Simon というユーザーを DocuSign に作成します。 DocuSign では、Just-In-Time ユーザー プロビジョニングがサポートされています。この設定は既定で有効になっています。 このセクションでは、ユーザー側で必要な操作はありません。 DocuSign にユーザーがまだ存在していない場合は、認証後に新規に作成されます。

> [!Note]
> ユーザーを手動で作成する必要がある場合は、[DocuSign のサポート チーム](https://support.docusign.com/)にお問い合わせください。

## <a name="test-sso"></a>SSO のテスト 

このセクションでは、次のオプションを使用して Azure AD のシングル サインオン構成をテストします。 

1. Azure portal で **[このアプリケーションをテストします]** をクリックします。 これにより、ログイン フローを開始できる DocuSign のサインオン URL にリダイレクトされます。 

2. DocuSign のサインオン URL に直接移動し、そこからログイン フローを開始します。

3. Microsoft アクセス パネルを使用することができます。 アクセス パネル上で [DocuSign] タイルをクリックすると、SSO を設定した DocuSign に自動的にサインインします。 アクセス パネルの詳細については、[アクセス パネルの概要](../user-help/my-apps-portal-end-user-access.md)に関する記事を参照してください。


## <a name="next-steps"></a>次の手順

DocuSign を構成したら、組織の機密データを流出と侵入からリアルタイムで保護するセッション制御を適用することができます。 セッション制御は、条件付きアクセスを拡張したものです。 [Microsoft Cloud App Security でセッション制御を適用する方法](/cloud-app-security/proxy-deployment-aad)をご覧ください。

<!--Image references-->

[50]: ./media/docusign-tutorial/tutorial_docusign_18.png
[51]: ./media/docusign-tutorial/tutorial_docusign_21.png
[52]: ./media/docusign-tutorial/tutorial_docusign_22.png
[53]: ./media/docusign-tutorial/tutorial_docusign_23.png
[54]: ./media/docusign-tutorial/tutorial_docusign_19.png
[55]: ./media/docusign-tutorial/tutorial_docusign_20.png
[56]: ./media/docusign-tutorial/tutorial_docusign_24.png
[57]: ./media/docusign-tutorial/tutorial_docusign_25.png
[58]: ./media/docusign-tutorial/tutorial_docusign_26.png
[59]: ./media/docusign-tutorial/tutorial_docusign_27.png
[60]: ./media/docusign-tutorial/tutorial_docusign_28.png
[61]: ./media/docusign-tutorial/tutorial_docusign_29.png
[62]: ./media/docusign-tutorial/tutorial_docusign_30.png