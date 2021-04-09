---
title: チュートリアル:Azure Active Directory と PagerDuty の統合 | Microsoft Docs
description: Azure Active Directory と PagerDuty の間でシングル サインオンを構成する方法について説明します。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/18/2021
ms.author: jeedes
ms.openlocfilehash: 9a3117b64c516120f8556b7b63b24e5ef906f973
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "101648562"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-pagerduty"></a>チュートリアル:Azure Active Directory シングル サインオン (SSO) と PagerDuty の統合

このチュートリアルでは、PagerDuty と Azure Active Directory (Azure AD) を統合する方法について説明します。 Azure AD と PagerDuty を統合すると、次のことができます。

* PagerDuty にアクセスできるユーザーを Azure AD で制御できます。
* ユーザーが自分の Azure AD アカウントを使用して PagerDuty に自動的にサインインできるように設定できます。
* 1 つの中央サイト (Azure Portal) で自分のアカウントを管理します。

## <a name="prerequisites"></a>前提条件

開始するには、次が必要です。

* Azure AD サブスクリプション。 サブスクリプションがない場合は、[無料アカウント](https://azure.microsoft.com/free/)を取得できます。
* PagerDuty でのシングル サインオン (SSO) が有効なサブスクリプション。

> [!NOTE]
> Azure AD で MFA またはパスワードレス認証を使用している場合は、SAML 要求で AuthnContext 値をオフにしてください。 そうしないと、Azure AD によって AuthnContext の不一致エラーがスローされ、トークンがアプリケーションに送り返されなくなります。

## <a name="scenario-description"></a>シナリオの説明

このチュートリアルでは、テスト環境で Azure AD の SSO を構成してテストします。

* PagerDuty では、**SP** によって開始される SSO がサポートされます

## <a name="add-pagerduty-from-the-gallery"></a>ギャラリーからの PagerDuty の追加

Azure AD への PagerDuty の統合を構成するには、ギャラリーから管理対象 SaaS アプリの一覧に PagerDuty を追加する必要があります。

1. 職場または学校アカウントか、個人の Microsoft アカウントを使用して、Azure portal にサインインします。
1. 左のナビゲーション ウィンドウで **[Azure Active Directory]** サービスを選択します。
1. **[エンタープライズ アプリケーション]** に移動し、 **[すべてのアプリケーション]** を選択します。
1. 新しいアプリケーションを追加するには、 **[新しいアプリケーション]** を選択します。
1. **[ギャラリーから追加する]** セクションで、検索ボックスに「**PagerDuty**」と入力します。
1. 結果のパネルから **[PagerDuty]** を選択し、アプリを追加します。 お使いのテナントにアプリが追加されるのを数秒待機します。

## <a name="configure-and-test-azure-ad-single-sign-on-for-pagerduty"></a>PagerDuty の Azure AD シングル サインオンの構成とテスト

**B.Simon** というテスト ユーザーを使用して、PagerDuty に対する Azure AD SSO を構成してテストします。 SSO が機能するためには、Azure AD ユーザーと PagerDuty の関連ユーザーとの間にリンク関係を確立する必要があります。

PagerDuty に対する Azure AD SSO を構成してテストするには、次の構成要素を完了します。

1. **[Azure AD SSO の構成](#configure-azure-ad-sso)** - ユーザーがこの機能を使用できるようにします。
    1. **[Azure AD のテスト ユーザーの作成](#create-an-azure-ad-test-user)** - B.Simon で Azure AD のシングル サインオンをテストします。
    1. **[Azure AD テスト ユーザーの割り当て](#assign-the-azure-ad-test-user)** - B.Simon が Azure AD シングル サインオンを使用できるようにします。
1. **[PagerDuty の SSO の構成](#configure-pagerduty-sso)** - アプリケーション側でシングル サインオン設定を構成します。
    1. **[PagerDuty のテスト ユーザーの作成](#create-pagerduty-test-user)** - PagerDuty で B.Simon に対応するユーザーを作成し、Azure AD の B.Simon にリンクさせます。
1. **[SSO のテスト](#test-sso)** - 構成が機能するかどうかを確認します。

## <a name="configure-azure-ad-sso"></a>Azure AD SSO の構成

これらの手順に従って、Azure portal で Azure AD SSO を有効にします。

1. Azure portal の **PagerDuty** アプリケーション統合ページで、 **[管理]** セクションを探して、 **[シングル サインオン]** を選択します。
1. **[シングル サインオン方式の選択]** ページで、 **[SAML]** を選択します。
1. **[SAML によるシングル サインオンのセットアップ]** ページで、 **[基本的な SAML 構成]** の鉛筆アイコンをクリックして設定を編集します。

   ![基本的な SAML 構成を編集する](common/edit-urls.png)

1. **[基本的な SAML 構成]** セクションで、次の手順を実行します。

    a. **[サインオン URL]** ボックスに、次のパターンを使用して URL を入力します。`https://<tenant-name>.pagerduty.com`

    b. **[識別子 (エンティティ ID)]** ボックスに、次のパターンを使用して URL を入力します。`https://<tenant-name>.pagerduty.com`

    c. **[応答 URL]** ボックスに、`https://<tenant-name>.pagerduty.com` のパターンを使用して URL を入力します

    > [!NOTE]
    > これらは実際の値ではありません。 これらの値を実際のサインオン URL、識別子、および応答 URL で更新してください。 これらの値を取得するには、[PagerDuty クライアント サポート チーム](https://www.pagerduty.com/support/)に問い合わせてください。 Azure portal の **[基本的な SAML 構成]** セクションに示されているパターンを参照することもできます。

1. **[SAML でシングル サインオンをセットアップします]** ページの **[SAML 署名証明書]** セクションで、 **[証明書 (Base64)]** を見つけて、 **[ダウンロード]** を選択し、証明書をダウンロードして、お使いのコンピューターに保存します。

    ![証明書のダウンロードのリンク](common/certificatebase64.png)

1. **[PagerDuty のセットアップ]** セクションで、要件に基づいて適切な URL をコピーします。

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

このセクションでは、B. Simon に PagerDuty へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

1. Azure portal で **[エンタープライズ アプリケーション]** を選択し、 **[すべてのアプリケーション]** を選択します。
1. アプリケーションの一覧で **[PagerDuty]** を選択します。
1. アプリの概要ページで、 **[管理]** セクションを見つけて、 **[ユーザーとグループ]** を選択します。
1. **[ユーザーの追加]** を選択し、 **[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。
1. **[ユーザーとグループ]** ダイアログの [ユーザー] の一覧から **[B.Simon]** を選択し、画面の下部にある **[選択]** ボタンをクリックします。
1. SAML アサーション内に任意のロール値が必要な場合、 **[ロールの選択]** ダイアログでユーザーに適したロールを一覧から選択し、画面の下部にある **[選択]** をクリックします。
1. **[割り当ての追加]** ダイアログで、 **[割り当て]** をクリックします。

## <a name=&quot;configure-pagerduty-sso&quot;></a>PagerDuty の SSO の構成

1. 別の Web ブラウザーのウィンドウで、PagerDuty 企業サイトに管理者としてサインインします。

2. 上部のメニューで **[アカウント設定]** をクリックします。

    ![アカウント設定](./media/pagerduty-tutorial/ic778535.png &quot;[Account Settings]")

3. **[Single Sign-on]** をクリックします。

    ![シングル サインオン](./media/pagerduty-tutorial/ic778536.png "シングル サインオン")

4. **[シングル サインオンの有効化 (SSO)]** ページで、次の手順に従います。

    ![シングル サインオンの有効化](./media/pagerduty-tutorial/ic778537.png "シングル サインオンの有効化")

    a. Azure Portal からダウンロードされた Base-64 でエンコードされた証明書をメモ帳で開き、その内容をクリップボードにコピーしてから、それを **[X.509 Certificate]** ボックスに貼り付けます
  
    b. **[Login URL]\(ログイン URL\)** ボックスに、Azure portal からコピーした **[ログイン URL]** を貼り付けます。
  
    c. **[Logout URL]\(ログアウト URL\)** ボックスに、Azure portal からコピーした **[ログアウト URL]** を貼り付けます。

    d. **[Allow username/password login]** (ユーザー名/パスワードによるログインを許可) を選択します。

    e. **[Require EXACT authentication context comparison]** (認証コンテキストの正確な比較を要求する) チェック ボックスを選択します。

    f. **[変更を保存]** をクリックします。

### <a name="create-pagerduty-test-user"></a>PagerDuty のテスト ユーザーの作成

Azure AD ユーザーが PagerDuty にサインインできるようにするには、ユーザーを PagerDuty にプロビジョニングする必要があります。 PagerDuty の場合、プロビジョニングは手動で行います。

> [!NOTE]
> PagerDuty から提供されている他の PagerDuty ユーザー アカウント作成ツールまたは API を使用して、Azure Active Directory ユーザー アカウントをプロビジョニングできます。

**ユーザー アカウントをプロビジョニングするには、次の手順に従います。**

1. **PagerDuty** テナントにサインインします。

2. 上部のメニューで **[ユーザー]** をクリックします。

3. **[ユーザーの追加]** をクリックします。
   
    ![ユーザーの追加](./media/pagerduty-tutorial/ic778539.png "ユーザーの追加")

4.  **[Invite your team]** ダイアログ ボックスで、次の手順を実行します。
   
    ![[Invite your team]\(チームの招待\)](./media/pagerduty-tutorial/ic778540.png "チームの招待")

    a. ユーザーの **氏名** (**B.Simon** など) を入力します。 
   
    b. ユーザーの **メール** アドレス (例: **b.simon\@contoso.com**) を入力します。
   
    c. **[Add]** をクリックし、 **[Send Invites]** をクリックします。
   
    > [!NOTE]
    > PagerDuty アカウントを作成すると、追加したすべてのユーザーが招待を受信します。

## <a name="test-sso"></a>SSO のテスト 

このセクションでは、次のオプションを使用して Azure AD のシングル サインオン構成をテストします。 

* Azure portal で **[このアプリケーションをテストします]** をクリックします。 これにより、ログイン フローを開始できる PagerDuty のサインオン URL にリダイレクトされます。 

* PagerDuty のサインオン URL に直接移動し、そこからログイン フローを開始します。

* Microsoft マイ アプリを使用することができます。 マイ アプリで [PagerDuty] タイルをクリックすると、PagerDuty のサインオン URL にリダイレクトされます。 マイ アプリの詳細については、[マイ アプリの概要](../user-help/my-apps-portal-end-user-access.md)に関するページを参照してください。

## <a name="next-steps"></a>次のステップ

PagerDuty を構成したら、組織の機密データを流出と侵入からリアルタイムで保護するセッション制御を強制することができます。 セッション制御は、条件付きアクセスを拡張したものです。 [Microsoft Cloud App Security でセッション制御を強制する方法](/cloud-app-security/proxy-deployment-any-app)をご覧ください。