---
title: 'チュートリアル: Azure Active Directory と Zoho の統合 | Microsoft Docs'
description: Azure Active Directory と Zoho の間でシングル サインオンを構成する方法について説明します。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/19/2021
ms.author: jeedes
ms.openlocfilehash: c5778f39a5091753a1658ec121379a4ed29a7542
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "101648375"
---
# <a name="tutorial-azure-active-directory-integration-with-zoho"></a>チュートリアル: Azure Active Directory と Zoho の統合

このチュートリアルでは、Zoho と Azure Active Directory (Azure AD) を統合する方法について説明します。 Azure AD と Zoho を統合すると、次のことができます。

* Zoho にアクセスする Azure AD ユーザーを制御する。
* ユーザーが自分の Azure AD アカウントを使用して Zoho に自動的にサインインできるように設定する。
* 1 つの中央サイト (Azure Portal) で自分のアカウントを管理します。

## <a name="prerequisites"></a>前提条件

Zoho One と Azure AD の統合を構成するには、次のものが必要です。

* Azure AD サブスクリプション。 Azure AD の環境がない場合は、[無料アカウント](https://azure.microsoft.com/free/)を取得できます。
* Zoho でのシングル サインオンが有効なサブスクリプション。

## <a name="scenario-description"></a>シナリオの説明

このチュートリアルでは、テスト環境で Azure AD のシングル サインオンを構成してテストします。

* Zoho では、**SP** によって開始される SSO がサポートされます

## <a name="add-zoho-from-the-gallery"></a>ギャラリーからの Zoho の追加

Azure AD への Zoho の統合を構成するには、ギャラリーから管理対象 SaaS アプリの一覧に Zoho を追加する必要があります。

1. 職場または学校アカウントか、個人の Microsoft アカウントを使用して、Azure portal にサインインします。
1. 左のナビゲーション ウィンドウで **[Azure Active Directory]** サービスを選択します。
1. **[エンタープライズ アプリケーション]** に移動し、 **[すべてのアプリケーション]** を選択します。
1. 新しいアプリケーションを追加するには、 **[新しいアプリケーション]** を選択します。
1. **[ギャラリーから追加する]** セクションで、検索ボックスに「**Zoho**」と入力します。
1. 結果のパネルから **[Zoho]** を選択し、アプリを追加します。 お使いのテナントにアプリが追加されるのを数秒待機します。

## <a name="configure-and-test-azure-ad-sso-for-zoho"></a>Zoho の Azure AD SSO の構成とテスト

**B.Simon** というテスト ユーザーを使用して、Zoho に対する Azure AD SSO を構成してテストします。 SSO を機能させるために、Azure AD ユーザーと Zoho の関連ユーザーとの間にリンク関係を確立する必要があります。

Zoho に対して Azure AD SSO を構成してテストするには、次の手順を実行します。

1. **[Azure AD SSO の構成](#configure-azure-ad-sso)** - ユーザーがこの機能を使用できるようにします。
    1. **[Azure AD のテスト ユーザーの作成](#create-an-azure-ad-test-user)** - B.Simon で Azure AD のシングル サインオンをテストします。
    1. **[Azure AD テスト ユーザーの割り当て](#assign-the-azure-ad-test-user)** - B.Simon が Azure AD シングル サインオンを使用できるようにします。
1. **[Zoho の SSO の構成](#configure-zoho-sso)** - アプリケーション側でシングル サインオン設定を構成します。
    1. **[Zoho テスト ユーザーの作成](#create-zoho-test-user)** - Azure AD の B.Simon にリンクさせるために、対応するユーザーを Zoho で作成します。
1. **[SSO のテスト](#test-sso)** - 構成が機能するかどうかを確認します。

### <a name="configure-azure-ad-sso"></a>Azure AD SSO の構成

これらの手順に従って、Azure portal で Azure AD SSO を有効にします。

1. Azure portal の **Zoho** アプリケーション統合ページで、 **[管理]** セクションを探して、 **[シングル サインオン]** を選択します。
1. **[シングル サインオン方式の選択]** ページで、 **[SAML]** を選択します。
1. **[SAML によるシングル サインオンのセットアップ]** ページで、 **[基本的な SAML 構成]** の鉛筆アイコンをクリックして設定を編集します。

   ![基本的な SAML 構成を編集する](common/edit-urls.png)

4. **[基本的な SAML 構成]** セクションで、次の手順を実行します。

    **[サインオン URL]** ボックスに、`https://<company name>.zohomail.com` という形式で URL を入力します。

    > [!NOTE]
    > この値は実際のものではありません。 実際のサインオン URL でこの値を更新してください。 この値を取得するには、[Zoho クライアント サポート チーム](https://www.zoho.com/mail/contact.html)にお問い合わせください。 Azure portal の **[基本的な SAML 構成]** セクションに示されているパターンを参照することもできます。

5. **[SAML でシングル サインオンをセットアップします]** ページの **[SAML 署名証明書]** セクションで、 **[ダウンロード]** をクリックして要件のとおりに指定したオプションからの **証明書 (Base64)** をダウンロードして、お使いのコンピューターに保存します。

    ![証明書のダウンロードのリンク](common/certificatebase64.png)

6. **[Set up Zoho]\(Zoho の設定\)** セクションで、要件どおりの適切な URL をコピーします。

    ![構成 URL のコピー](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user&quot;></a>Azure AD のテスト ユーザーの作成 

このセクションでは、Azure portal 内で B.Simon というテスト ユーザーを作成します。

1. Azure portal の左側のウィンドウから、 **[Azure Active Directory]** 、 **[ユーザー]** 、 **[すべてのユーザー]** の順に選択します。
1. 画面の上部にある **[新しいユーザー]** を選択します。
1. **[ユーザー]** プロパティで、以下の手順を実行します。
   1. **[名前]** フィールドに「`B.Simon`」と入力します。  
   1. **[ユーザー名]** フィールドに「username@companydomain.extension」と入力します。 たとえば、「 `B.Simon@contoso.com` 」のように入力します。
   1. **[パスワードを表示]** チェック ボックスをオンにし、 **[パスワード]** ボックスに表示された値を書き留めます。
   1. **Create** をクリックしてください。

### <a name=&quot;assign-the-azure-ad-test-user&quot;></a>Azure AD テスト ユーザーの割り当て

このセクションでは、B.Simon に Zoho へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

1. Azure portal で **[エンタープライズ アプリケーション]** を選択し、 **[すべてのアプリケーション]** を選択します。
1. アプリケーションの一覧で **[Zoho]** を選択します。
1. アプリの概要ページで、 **[管理]** セクションを見つけて、 **[ユーザーとグループ]** を選択します。
1. **[ユーザーの追加]** を選択し、 **[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。
1. **[ユーザーとグループ]** ダイアログの [ユーザー] の一覧から **[B.Simon]** を選択し、画面の下部にある **[選択]** ボタンをクリックします。
1. ユーザーにロールが割り当てられることが想定される場合は、 **[ロールの選択]** ドロップダウンからそれを選択できます。 このアプリに対してロールが設定されていない場合は、[既定のアクセス] ロールが選択されていることを確認します。
1. **[割り当ての追加]** ダイアログで、 **[割り当て]** をクリックします。

### <a name=&quot;configure-zoho-sso&quot;></a>Zoho SSO の構成

1. 別の Web ブラウザー ウィンドウで、Zoho Mail 企業サイトに管理者としてログインします。

2. **コントロール パネル** に移動します。
   
    ![コントロール パネル](./media/zoho-mail-tutorial/control-panel.png &quot;コントロール パネル")

3. **[SAML 認証]** タブをクリックします。
   
    ![SAML 認証](./media/zoho-mail-tutorial/saml-authentication.png "[SAML 認証]")

4. **[SAML 認証の詳細]** セクションで、次の手順に従います。
   
    ![SAML 認証の詳細](./media/zoho-mail-tutorial/details.png "[SAML 認証の詳細]")
   
    a. **[Login URL]\(ログイン URL\)** ボックスに、Azure portal からコピーした **[ログイン URL]** を貼り付けます。
   
    b. **[Logout URL]\(ログアウト URL\)** ボックスに、Azure portal からコピーした **[ログアウト URL]** を貼り付けます。
   
    c. **[Change Password URL]\(パスワードの変更 URL\)** ボックスに、Azure Portal からコピーした **パスワードの変更 URL** を貼り付けます。
       
    d. Azure Portal からダウンロードした Base-64 でエンコードされた証明書をメモ帳で開き、その内容をクリップボードにコピーして **[PublicKey]\(公開キー\)** ボックスに貼り付けます。
   
    e. **[アルゴリズム]** として **[RSA]** を選択します。
   
    f. **[OK]** をクリックします。

### <a name="create-zoho-test-user"></a>Zoho テスト ユーザーの作成

Azure AD ユーザーが Zoho Mail にログインできるようにするには、そのユーザーを Zoho Mail にプロビジョニングする必要があります。 Zoho Mail の場合、プロビジョニングは手動で行います。

> [!NOTE]
> 他の Zoho Mail ユーザー アカウント作成ツールや、Zoho Mail から提供されている API を使用して、Azure AD ユーザー アカウントをプロビジョニングできます。

### <a name="to-provision-a-user-account-perform-the-following-steps"></a>ユーザー アカウントをプロビジョニングするには、次の手順を実行します。

1. **Zoho Mail** 企業サイトに管理者としてログインします。

1. **[コントロール パネル] \> [メールとドキュメント]** に移動します。

1. **[ユーザーの詳細] \> [ユーザーの追加]** の順に移動します。
   
    ![[ユーザーの詳細] と [ユーザーの追加] が選択されている Zoho Mail サイトを示すスクリーンショット。](./media/zoho-mail-tutorial/add-user-1.png "ユーザーの追加")

1. **[ユーザーの追加]** ダイアログで、次の手順を実行します。
   
    ![[ユーザーの追加] ダイアログ ボックスを示すスクリーンショット。ここで、説明されている値を入力できます。](./media/zoho-mail-tutorial/add-user-2.png "ユーザーの追加")
   
    a. **[First Name]\(名\)** ボックスに、ユーザーの名を入力します (この例では **Britta**)。

    b. **[Last Name]\(姓\)** ボックスに、ユーザーの姓を入力します (この例では **Simon**)。

    c. **[Email ID]\(メール ID\)** ボックスに、ユーザーのメール ID (**brittasimon\@contoso.com** など) を入力します。

    d. **[Password]\(パスワード\)** ボックスに、ユーザーのパスワードを入力します。
   
    e. **[OK]** をクリックします。  
      
    > [!NOTE]
    > Azure Active Directory のアカウント所有者には、そのアカウントがアクティブになる前に、アカウント確認用のリンクを含む電子メールが送信されます。

### <a name="test-sso"></a>SSO のテスト 

このセクションでは、次のオプションを使用して Azure AD のシングル サインオン構成をテストします。 

* Azure portal で **[このアプリケーションをテストします]** をクリックします。 これにより、ログイン フローを開始できる Zoho のサインオン URL にリダイレクトされます。 

* Zoho のサインオン URL に直接移動し、そこからログイン フローを開始します。

* Microsoft マイ アプリを使用することができます。 マイ アプリで [Zoho] タイルをクリックすると、SSO を設定した Zoho に自動的にサインインされます。 マイ アプリの詳細については、[マイ アプリの概要](../user-help/my-apps-portal-end-user-access.md)に関するページを参照してください。

## <a name="next-steps"></a>次のステップ

Zoho を構成したら、組織の機密データを流出と侵入からリアルタイムで保護するセッション制御を適用することができます。 セッション制御は、条件付きアクセスを拡張したものです。 [Microsoft Cloud App Security でセッション制御を強制する方法](/cloud-app-security/proxy-deployment-any-app)をご覧ください。