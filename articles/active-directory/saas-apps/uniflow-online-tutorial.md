---
title: チュートリアル:Azure Active Directory シングル サインオン (SSO) と uniFLOW Online の統合 | Microsoft Docs
description: Azure Active Directory と uniFLOW Online の間でシングル サインオンを構成する方法について説明します。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 02/04/2021
ms.author: jeedes
ms.openlocfilehash: 9fdcd8a82b901e00e28f0ddd89ba53d9a2e3fbae
ms.sourcegitcommit: ac035293291c3d2962cee270b33fca3628432fac
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/24/2021
ms.locfileid: "104952686"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-uniflow-online"></a>チュートリアル:Azure Active Directory シングル サインオン (SSO) と uniFLOW Online の統合

このチュートリアルでは、uniFLOW Online と Azure Active Directory (Azure AD) を統合する方法について説明します。 Azure AD と uniFLOW Online を統合すると、次のことができます。

* uniFLOW Online にアクセスできるユーザーを Azure AD で制御する。
* ユーザーが自分の Azure AD アカウントを使用して uniFLOW Online にサインインできるようにする。
* 1 つの中央サイト (Azure Portal) で自分のアカウントを管理します。

## <a name="prerequisites"></a>前提条件

開始するには、次が必要です。

* Azure AD サブスクリプション。 サブスクリプションがない場合は、[無料アカウント](https://azure.microsoft.com/free/)を取得できます。
* uniFLOW Online テナント。

## <a name="scenario-description"></a>シナリオの説明

このチュートリアルでは、テスト環境で Azure AD の SSO を構成してテストします。

* uniFLOW Online では、**SP** Initiated SSO がサポートされます

## <a name="add-uniflow-online-from-the-gallery"></a>ギャラリーからの uniFLOW Online の追加

Azure AD への uniFLOW Online の統合を構成するには、ギャラリーからマネージド SaaS アプリの一覧に uniFLOW Online を追加する必要があります。

1. 職場または学校アカウントか、個人の Microsoft アカウントを使用して、Azure portal にサインインします。
1. 左のナビゲーション ウィンドウで **[Azure Active Directory]** サービスを選択します。
1. **[エンタープライズ アプリケーション]** に移動し、 **[すべてのアプリケーション]** を選択します。
1. 新しいアプリケーションを追加するには、 **[新しいアプリケーション]** を選択します。
1. **[ギャラリーから追加する]** セクションで、検索ボックスに「**uniFLOW Online**」と入力します。
1. 結果のパネルから **[uniFLOW Online]** を選択し、アプリを追加します。 お使いのテナントにアプリが追加されるのを数秒待機します。

## <a name="configure-and-test-azure-ad-sso-for-uniflow-online"></a>uniFLOW Online の Azure AD SSO の構成とテスト

**B.Simon** というテスト ユーザーを使用して、uniFLOW Online に対する Azure AD SSO を構成してテストします。 SSO が機能するためには、Azure AD ユーザーと uniFLOW Online の関連ユーザーとの間にリンク関係を確立する必要があります。

uniFLOW Online に対して Azure AD SSO を構成してテストするには、次の手順を行います。

1. **[Azure AD SSO の構成](#configure-azure-ad-sso)** - ユーザーがこの機能を使用できるようにします。
   1. **[Azure AD のテスト ユーザーの作成](#create-an-azure-ad-test-user)** - B.Simon で Azure AD のシングル サインオンをテストします。
   1. **[Azure AD テスト ユーザーの割り当て](#assign-the-azure-ad-test-user)** - B.Simon が Azure AD シングル サインオンを使用できるようにします。
1. **[uniFLOW Online SSO の構成](#configure-uniflow-online-sso)** - アプリケーション側でシングル サインオン設定を構成します。
    1. **[作成済みのテスト ユーザーを使用した uniFLOW Online へのサインイン](#sign-in-to-uniflow-online-using-the-created-test-user)** - ユーザーのサインインをアプリケーション側でテストします。
1. **[SSO のテスト](#test-sso)** - 構成が機能するかどうかを確認します。

## <a name="configure-azure-ad-sso"></a>Azure AD SSO の構成

これらの手順に従って、Azure portal で Azure AD SSO を有効にします。

1. Azure portal の **uniFLOW Online** アプリケーション統合ページで、 **[管理]** セクションを見つけて、 **[シングル サインオン]** を選択します。
1. **[シングル サインオン方式の選択]** ページで、 **[SAML]** を選択します。
1. **[SAML によるシングル サインオンのセットアップ]** ページで、 **[基本的な SAML 構成]** の鉛筆アイコンをクリックして設定を編集します。

   ![基本的な SAML 構成を編集する](common/edit-urls.png)

1. **[基本的な SAML 構成]** セクションで、次のフィールドの値を入力します。

    a. **[サインオン URL]** テキスト ボックスに、次のいずれかのパターンを使用して URL を入力します。

    - `https://<tenant_domain_name>.eu.uniflowonline.com`
    - `https://<tenant_domain_name>.us.uniflowonline.com`
    - `https://<tenant_domain_name>.sg.uniflowonline.com`
    - `https://<tenant_domain_name>.jp.uniflowonline.com`
    - `https://<tenant_domain_name>.au.uniflowonline.com`

    b. **[識別子 (エンティティ ID)]** ボックスに、次のいずれかのパターンを使用して URL を入力します。

    - `https://<tenant_domain_name>.eu.uniflowonline.com`
    - `https://<tenant_domain_name>.us.uniflowonline.com`
    - `https://<tenant_domain_name>.sg.uniflowonline.com`
    - `https://<tenant_domain_name>.jp.uniflowonline.com`
    - `https://<tenant_domain_name>.au.uniflowonline.com`

    > [!NOTE]
    > これらは実際の値ではありません。 実際のサインオン URL と識別子でこれらの値を更新します。 これらの値を取得するには、[uniFLOW Online クライアント サポート チーム](mailto:support@nt-ware.com)に問い合わせてください。 Azure portal の **[基本的な SAML 構成]** セクションに示されているパターンを参照したり、uniFLOW Online テナントに表示されている応答 URL を参照したりすることもできます。

1. uniFLOW Online アプリケーションでは、特定の形式の SAML アサーションを使用するため、カスタム属性マッピングを SAML トークン属性の構成に追加する必要があります。 次のスクリーンショットには、既定の属性一覧が示されています。

    ![image](common/default-attributes.png)

1. その他に、uniFLOW Online アプリケーションでは、いくつかの属性が SAML 応答で返されることが想定されています。それらの属性を次に示します。 これらの属性も値が事前に設定されますが、要件に従ってそれらの値を確認することができます。

    | 名前 |  ソース属性|
    | -----------| --------------- |
    | displayName | user.displayname |
    | nickname | user.onpremisessamaccountname |

   > [!NOTE]
   > `user.onpremisessamaccountname` 属性に値が格納されるのは、Azure AD のユーザーがローカルの Windows Active Directory から同期されている場合のみです。

1. **[Set up single sign-on with SAML]\(SAML でシングル サインオンをセットアップします\)** ページの **[SAML 署名証明書]** セクションで、コピー ボタンをクリックして **[アプリのフェデレーション メタデータ URL]** をコピーして、お使いのコンピューターに保存します。

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

このセクションでは、B.Simon に uniFLOW Online へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

1. Azure portal で **[エンタープライズ アプリケーション]** を選択し、 **[すべてのアプリケーション]** を選択します。
1. アプリケーションの一覧で **[uniFLOW Online]** を選択します。
1. アプリの概要ページで、 **[管理]** セクションを見つけて、 **[ユーザーとグループ]** を選択します。
1. **[ユーザーの追加]** を選択し、 **[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。
1. **[ユーザーとグループ]** ダイアログの [ユーザー] の一覧から **[B.Simon]** を選択し、画面の下部にある **[選択]** ボタンをクリックします。
1. ユーザーにロールが割り当てられることが想定される場合は、 **[ロールの選択]** ドロップダウンからそれを選択できます。 このアプリに対してロールが設定されていない場合は、[既定のアクセス] ロールが選択されていることを確認します。
1. **[割り当ての追加]** ダイアログで、 **[割り当て]** をクリックします。

> [!NOTE]
> 割り当てを手動で行うことなく、すべてのユーザーにアプリケーションへのアクセスを許可するには、 **[管理]** セクションに移動し、 **[プロパティ]** を選択します。 次に、 **[ユーザーの割り当てが必要ですか]** パラメーターを **[いいえ]** に変更します。

## <a name="configure-uniflow-online-sso"></a>uniFLOW Online SSO の構成

1. 別の Web ブラウザー ウィンドウで、uniFLOW Online Web サイトに管理者としてサインインします。

1. 左側のナビゲーション パネルで **[User]\(ユーザー\)** タブを選択します。

    ![uniFLOW Online サイトで [User]\(ユーザー\) が選択されていることを示すスクリーンショット。](./media/uniflow-online-tutorial/configure-1.png)

1. **[ID プロバイダー]** をクリックします。

    ![[Identity Provider]\(ID プロバイダー\) が選択されていることを示すスクリーンショット。](./media/uniflow-online-tutorial/configure-2.png)

1. **[Add identity provider]\(ID プロバイダーの追加\)** をクリックします。

    ![[Add identity provider]\(ID プロバイダーの追加\) が選択されていることを示すスクリーンショット。](./media/uniflow-online-tutorial/configure-3.png)

1. **[ADD IDENTITY PROVIDER]\(ID プロバイダーの追加\)** セクションで、次の手順を実行します。

    ![[ADD IDENTITY PROVIDER]\(ID プロバイダーの追加\) セクションを示すスクリーンショット。ここで、説明されている値を入力できます。](./media/uniflow-online-tutorial/configure-4.png)

    a. 表示名を入力します (例: *AzureAD SSO*)。

    b. **[Provider type]\(プロバイダーの種類\)** で、ドロップダウンの **[WS-Fed]** オプションを選択します。

    c. **[WS-Fed type]\(WS-Fed の種類\)** で、ドロップダウンの **[Azure Active Directory]** オプションを選択します。

    d. **[保存]** をクリックします。

1. **[General]\(全般\)** タブで、次の手順を実行します。

    ![[General]\(全般\) タブを示すスクリーンショット。ここで説明されている値を入力できます。](./media/uniflow-online-tutorial/configure-5.png)

    a. 表示名を入力します (例: *AzureAD SSO*)。

    b. **[ADFS Federation Metadata]\(ADFS フェデレーション メタデータ\)** で **[From URL]\(URL から\)** オプションを選択します。

    c. **[Federation Metadata URl]\(フェデレーション メタデータ URI\)** ボックスに、Azure portal からコピーした **アプリのフェデレーション メタデータ URL** の値を貼り付けます。

    d. **[Identity provider]\(ID プロバイダー\)** で **[Enabled]\(有効化\)** を選択します。

    e. **[Automatic user registration]\(自動ユーザー登録\)** で **[Activated]\(アクティブ化\)** を選択します。

    f. **[保存]** をクリックします。

### <a name="sign-in-to-uniflow-online-using-the-created-test-user"></a>作成済みのテスト ユーザーを使用した uniFLOW Online へのサインイン

1. 別の Web ブラウザー ウィンドウで、テナントの uniFLOW Online URL に移動します。

1. 作成済みの ID プロバイダーを選択し、Azure AD インスタンス経由でサインインします。

1. テスト ユーザーを使用してサインインします。

## <a name="test-sso"></a>SSO のテスト

このセクションでは、次のオプションを使用して Azure AD のシングル サインオン構成をテストします。 

* Azure portal で **[このアプリケーションをテストします]** をクリックします。 これにより、ログイン フローを開始できる uniFLOW Online のサインオン URL にリダイレクトされます。 

* uniFLOW Online のサインオン URL に直接移動し、そこからログイン フローを開始します。

* Microsoft マイ アプリを使用することができます。 マイ アプリで [uniFLOW Online] タイルをクリックすると、uniFLOW Online のサインオン URL にリダイレクトされます。 マイ アプリの詳細については、[マイ アプリの概要](../user-help/my-apps-portal-end-user-access.md)に関するページを参照してください。

## <a name="next-steps"></a>次のステップ

uniFLOW Online を構成したら、組織の機密データを流出と侵入からリアルタイムで保護するセッション制御を適用することができます。 セッション制御は、条件付きアクセスを拡張したものです。 [Microsoft Cloud App Security でセッション制御を強制する方法](/cloud-app-security/proxy-deployment-any-app)をご覧ください。