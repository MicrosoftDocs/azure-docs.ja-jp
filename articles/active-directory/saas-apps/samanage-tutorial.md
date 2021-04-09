---
title: チュートリアル:Azure Active Directory と SolarWinds Service Desk (旧称 Samanage) の統合 | Microsoft Docs
description: Azure Active Directory と SolarWinds Service Desk (旧称 Samanage) の間でシングル サインオンを構成する方法について説明します。
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
ms.openlocfilehash: 96118a840c57932ae8e99b084766bf12493d9f7a
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/02/2021
ms.locfileid: "101652733"
---
# <a name="tutorial-azure-active-directory-integration-with-solarwinds-service-desk-previously-samanage"></a>チュートリアル:Azure Active Directory と SolarWinds Service Desk (旧称 Samanage) の統合

このチュートリアルでは、SolarWinds と Azure Active Directory (Azure AD) を統合する方法について説明します。 Azure AD と SolarWinds を統合すると、次のことができます。

* SolarWinds にアクセスできるユーザーを Azure AD で制御できます。
* ユーザーが自分の Azure AD アカウントを使用して SolarWinds に自動的にサインインできるように設定できます。
* 1 つの中央サイト (Azure Portal) で自分のアカウントを管理します。

## <a name="prerequisites"></a>前提条件

開始するには、次が必要です。

* Azure AD サブスクリプション。 サブスクリプションがない場合は、[無料アカウント](https://azure.microsoft.com/free/)を取得できます。
* SolarWinds でのシングル サインオン (SSO) が有効なサブスクリプション。

## <a name="scenario-description"></a>シナリオの説明

このチュートリアルでは、テスト環境で Azure AD のシングル サインオンを構成してテストします。

* SolarWinds では、**SP**-Initiated SSO がサポートされます。

## <a name="add-solarwinds-from-the-gallery"></a>ギャラリーからの SolarWinds の追加

Azure AD への SolarWinds の統合を構成するには、ギャラリーからマネージド SaaS アプリの一覧に SolarWinds を追加する必要があります。

1. 職場または学校アカウントか、個人の Microsoft アカウントを使用して、Azure portal にサインインします。
1. 左のナビゲーション ウィンドウで **[Azure Active Directory]** サービスを選択します。
1. **[エンタープライズ アプリケーション]** に移動し、 **[すべてのアプリケーション]** を選択します。
1. 新しいアプリケーションを追加するには、 **[新しいアプリケーション]** を選択します。
1. **[ギャラリーから追加する]** セクションで、検索ボックスに「**SolarWinds**」と入力します。
1. 結果のパネルから **[SolarWinds]** を選択し、アプリを追加します。 お使いのテナントにアプリが追加されるのを数秒待機します。

## <a name="configure-and-test-azure-ad-sso-for-solarwinds"></a>SolarWinds の Azure AD SSO の構成とテスト

**B.Simon** というテスト ユーザーを使用して、SolarWinds に対する Azure AD SSO を構成してテストします。 SSO が機能するためには、Azure AD ユーザーと SolarWinds の関連ユーザーとの間にリンク関係を確立する必要があります。

SolarWinds に対して Azure AD SSO を構成してテストするには、次の手順を行います。

1. **[Azure AD SSO の構成](#configure-azure-ad-sso)** - ユーザーがこの機能を使用できるようにします。
    1. **[Azure AD のテスト ユーザーの作成](#create-an-azure-ad-test-user)** - B.Simon で Azure AD のシングル サインオンをテストします。
    1. **[Azure AD テスト ユーザーの割り当て](#assign-the-azure-ad-test-user)** - B.Simon が Azure AD シングル サインオンを使用できるようにします。
1. **[SolarWinds の SSO の構成](#configure-solarwinds-sso)** - アプリケーション側でシングル サインオン設定を構成します。
    1. **[SolarWinds のテスト ユーザーの作成](#create-solarwinds-test-user)** - SolarWinds で B.Simon に対応するユーザーを作成し、Azure AD のこのユーザーにリンクさせます。
1. **[SSO のテスト](#test-sso)** - 構成が機能するかどうかを確認します。

## <a name="configure-azure-ad-sso"></a>Azure AD SSO の構成

これらの手順に従って、Azure portal で Azure AD SSO を有効にします。

1. Azure portal の **SolarWinds** アプリケーション統合ページで、 **[管理]** セクションを見つけて、 **[シングル サインオン]** を選択します。
1. **[シングル サインオン方式の選択]** ページで、 **[SAML]** を選択します。
1. **[SAML によるシングル サインオンのセットアップ]** ページで、 **[基本的な SAML 構成]** の鉛筆アイコンをクリックして設定を編集します。

   ![基本的な SAML 構成を編集する](common/edit-urls.png)

4. **[基本的な SAML 構成]** セクションで、次の手順を実行します。

    a. **[サインオン URL]** ボックスに、次のパターンを使用して URL を入力します。`https://<Company Name>.samanage.com/saml_login/<Company Name>`

    b. **[識別子 (エンティティ ID)]** ボックスに、次のパターンを使用して URL を入力します。`https://<Company Name>.samanage.com`

    > [!NOTE] 
    > これらは実際の値ではありません。 実際のサインオン URL と識別子に値を置き換えます。実際の値については後で説明します。 詳細については、[Samanage クライアント サポート チーム](https://www.samanage.com/support)に問い合わせてください。 Azure portal の **[基本的な SAML 構成]** セクションに示されているパターンを参照することもできます。

4. **[SAML でシングル サインオンをセットアップします]** ページの **[SAML 署名証明書]** セクションで、 **[ダウンロード]** をクリックして要件のとおりに指定したオプションからの **証明書 (Base64)** をダウンロードして、お使いのコンピューターに保存します。

    ![証明書のダウンロードのリンク](common/certificatebase64.png)

6. **[SolarWinds のセットアップ]** セクションで、要件に従って適切な URL をコピーします。

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

このセクションでは、B.Simon に SolarWinds へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

1. Azure portal で **[エンタープライズ アプリケーション]** を選択し、 **[すべてのアプリケーション]** を選択します。
1. アプリケーションの一覧で **[SolarWinds]** を選択します。
1. アプリの概要ページで、 **[管理]** セクションを見つけて、 **[ユーザーとグループ]** を選択します。
1. **[ユーザーの追加]** を選択し、 **[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。
1. **[ユーザーとグループ]** ダイアログの [ユーザー] の一覧から **[B.Simon]** を選択し、画面の下部にある **[選択]** ボタンをクリックします。
1. ユーザーにロールが割り当てられることが想定される場合は、 **[ロールの選択]** ドロップダウンからそれを選択できます。 このアプリに対してロールが設定されていない場合は、[既定のアクセス] ロールが選択されていることを確認します。
1. **[割り当ての追加]** ダイアログで、 **[割り当て]** をクリックします。

<a name="configure-solarwinds-single-sign-on"></a>

## <a name="configure-solarwinds-sso"></a>SolarWinds の SSO の構成

1. 別の Web ブラウザー ウィンドウで、SolarWinds 企業サイトに管理者としてログインします。

2. **[Dashboard]** をクリックして、左のナビゲーション ウィンドウで **[Setup]** を選択します。
   
    ![ダッシュボード](./media/samanage-tutorial/tutorial-samanage-1.png "ダッシュボード")

3. **[シングル サインオン]** をクリックします。
   
    ![シングル サインオン](./media/samanage-tutorial/tutorial-samanage-2.png "[Single Sign-On]")

4. **[Login using SAML (SAML でログイン)]** セクションで、次の手順を実行します。
   
    ![[Login using SAML]\(SAML でログイン\)](./media/samanage-tutorial/tutorial-samanage-3.png "[Login using SAML (SAML でログイン)]")
 
    a. **[Enable Single Sign-On with SAML (SAML でのシングル サインオンを有効にする)]** をクリックします。  
 
    b. **[Identity Provider URL]\(ID プロバイダー URL\)** ボックスに、Azure portal からコピーした **Azure AD 識別子** の値を貼り付けます。    
 
    c. **[Login URL]\(ログイン URL\)** が Azure portal の **[基本的な SAML 構成]** セクションの **[サインオン URL]** と一致することを確認します。
 
    d. **[Logout URL]\(ログアウト URL\)** ボックスに、Azure portal からコピーした **ログアウト URL** の値を入力します。
 
    e. **[SAML Issuer]** \(SAML 発行者\) ボックスに、ID プロバイダーに設定されたアプリ ID URI を入力します。
 
    f. Azure Portal からダウンロードした base-64 でエンコードされた証明書をメモ帳で開き、その内容をクリップボードにコピーして、 **[Paste your Identity Provider x.509 Certificate below]\(ID プロバイダー x.509 証明書を貼り付けてください\)** ボックスに貼り付けます。
 
    g. **[Create users if they do not exist in SolarWinds (SolarWinds に存在しない場合にユーザーを作成する)]** をクリックします。
 
    h. **[Update]** をクリックします。

### <a name="create-solarwinds-test-user"></a>SolarWinds のテスト ユーザーを作成する

Azure AD ユーザーが SolarWinds にログインできるようにするには、そのユーザーを SolarWinds にプロビジョニングする必要があります。  
SolarWinds の場合、プロビジョニングは手動で行います。

**ユーザー アカウントをプロビジョニングするには、次の手順に従います。**

1. SolarWinds 企業サイトに管理者としてログインします。

2. **[Dashboard (ダッシュボード)]** をクリックし、左のナビゲーション ウィンドウで **[Setup (セットアップ)]** を選択します。
   
    ![セットアップ](./media/samanage-tutorial/tutorial-samanage-1.png "セットアップ")

3. [**Users**] タブをクリックします。
   
    ![ユーザー](./media/samanage-tutorial/tutorial-samanage-6.png "ユーザー")

4. [**New User**] をクリックします。
   
    ![[New User]\(新しいユーザー\)](./media/samanage-tutorial/tutorial-samanage-7.png "[新しいユーザー]")

5. プロビジョニングする Azure Active Directory アカウントの **[Name]\(名前\)** と **[Email Address]\(電子メール アドレス\)** を入力し、**[Create user]\(ユーザーの作成\)** をクリックします。
   
    ![ユーザーの作成](./media/samanage-tutorial/tutorial-samanage-8.png "[Create User]")
   
   >[!NOTE]
   >Azure Active Directory アカウント所有者がメールを受信し、リンクに従ってアカウントを確認するとそのアカウントがアクティブになります。 SolarWinds から提供されている他の SolarWinds ユーザー アカウント作成ツールまたは API を使用して、Azure Active Directory ユーザー アカウントをプロビジョニングできます。

## <a name="test-sso"></a>SSO のテスト

このセクションでは、次のオプションを使用して Azure AD のシングル サインオン構成をテストします。 

* Azure portal で **[このアプリケーションをテストします]** をクリックします。 これにより、ログイン フローを開始できる SolarWinds のサインオン URL にリダイレクトされます。 

* SolarWinds のサインオン URL に直接移動し、そこからログイン フローを開始します。

* Microsoft マイ アプリを使用することができます。 マイ アプリで [SolarWinds] タイルをクリックすると、SolarWinds のサインオン URL にリダイレクトされます。 マイ アプリの詳細については、[マイ アプリの概要](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)に関するページを参照してください。

## <a name="next-steps"></a>次のステップ

SolarWinds を構成したら、組織の機密データを流出と侵入からリアルタイムで保護するセッション制御を適用することができます。 セッション制御は、条件付きアクセスを拡張したものです。 [Microsoft Cloud App Security でセッション制御を強制する方法](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app)をご覧ください。