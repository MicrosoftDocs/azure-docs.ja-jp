---
title: チュートリアル:Azure Active Directory と Cisco Umbrella Admin SSO の統合 | Microsoft Docs
description: Azure Active Directory と Cisco Umbrella Admin SSO の間でシングル サインオンを構成する方法について説明します。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/16/2021
ms.author: jeedes
ms.openlocfilehash: 6073142b21a28f1242e0e6ec65ee2945a354b60b
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "104592508"
---
# <a name="tutorial-azure-active-directory-integration-with-cisco-umbrella-admin-sso"></a>チュートリアル: Azure Active Directory と Cisco Umbrella Admin SSO の統合

このチュートリアルでは、Cisco Umbrella Admin SSO と Azure Active Directory (Azure AD) を統合する方法について説明します。 Cisco Umbrella Admin SSO を Azure AD と統合すると、次のことが可能になります。

* Cisco Umbrella Admin SSO にアクセスできるユーザーを Azure AD で制御できます。
* ユーザーが自分の Azure AD アカウントを使用して Cisco Umbrella Admin SSO に自動的にサインインできるように設定できます。
* 1 つの中央サイト (Azure Portal) で自分のアカウントを管理します。

## <a name="prerequisites"></a>前提条件

開始するには、次が必要です。

* Azure AD サブスクリプション。 サブスクリプションがない場合は、[無料アカウント](https://azure.microsoft.com/free/)を取得できます。
* シングル サインオン (SSO) が有効な Cisco Umbrella Admin SSO サブスクリプション。

## <a name="scenario-description"></a>シナリオの説明

このチュートリアルでは、テスト環境で Azure AD のシングル サインオンを構成してテストします。

* Cisco Umbrella Admin SSO では、**SP Initiated SSO と IDP Initiated SSO** がサポートされます。

## <a name="add-cisco-umbrella-admin-sso-from-the-gallery"></a>ギャラリーからの Cisco Umbrella Admin SSO の追加

Azure AD への Cisco Umbrella Admin SSO の統合を構成するには、ギャラリーからマネージド SaaS アプリの一覧に Cisco Umbrella Admin SSO を追加する必要があります。

1. 職場または学校アカウントか、個人の Microsoft アカウントを使用して、Azure portal にサインインします。
1. 左のナビゲーション ウィンドウで **[Azure Active Directory]** サービスを選択します。
1. **[エンタープライズ アプリケーション]** に移動し、 **[すべてのアプリケーション]** を選択します。
1. 新しいアプリケーションを追加するには、 **[新しいアプリケーション]** を選択します。
1. **[ギャラリーから追加する]** セクションで、検索ボックスに、「**Cisco Umbrella Admin SSO**」と入力します。
1. 結果パネルで **[Cisco Umbrella Admin SSO]** を選択し、アプリを追加します。 お使いのテナントにアプリが追加されるのを数秒待機します。

## <a name="configure-and-test-azure-ad-sso-for-cisco-umbrella-admin-sso"></a>Cisco Umbrella Admin SSO の Azure AD SSO の構成とテスト

**B.Simon** というテスト ユーザーを使用して、Cisco Umbrella Admin SSO に対する Azure AD SSO を構成してテストします。 SSO を機能させるには、Azure AD ユーザーと Cisco Umbrella Admin SSO の関連ユーザーとの間にリンク関係を確立する必要があります。

Cisco Umbrella Admin SSO に対する Azure AD SSO を構成してテストするには、次の手順を実行します。

1. **[Azure AD SSO の構成](#configure-azure-ad-sso)** - ユーザーがこの機能を使用できるようにします。
    1. **[Azure AD のテスト ユーザーの作成](#create-an-azure-ad-test-user)** - B.Simon で Azure AD のシングル サインオンをテストします。
    1. **[Azure AD テスト ユーザーの割り当て](#assign-the-azure-ad-test-user)** - B.Simon が Azure AD シングル サインオンを使用できるようにします。
1. **[Cisco Umbrella Admin SSO の SSO の構成](#configure-cisco-umbrella-admin-sso-sso)** - アプリケーション側でシングル サインオン設定を構成します。
    1. **[Cisco Umbrella Admin SSO のテスト ユーザーの作成](#create-cisco-umbrella-admin-sso-test-user)** - Cisco Umbrella Admin SSO で B.Simon に対応するユーザーを作成し、Azure AD の B.Simon にリンクさせます。
1. **[SSO のテスト](#test-sso)** - 構成が機能するかどうかを確認します。

## <a name="configure-azure-ad-sso"></a>Azure AD SSO の構成

これらの手順に従って、Azure portal で Azure AD SSO を有効にします。

1. Azure portal の **Cisco Umbrella Admin SSO** アプリケーション統合ページで、 **[管理]** セクションを見つけて、 **[シングル サインオン]** を選択します。
1. **[シングル サインオン方式の選択]** ページで、 **[SAML]** を選択します。
1. **[SAML によるシングル サインオンのセットアップ]** ページで、 **[基本的な SAML 構成]** の鉛筆アイコンをクリックして設定を編集します。

   ![基本的な SAML 構成を編集する](common/edit-urls.png)

4. アプリは Azure と事前に統合済みであるため、 **[基本的な SAML 構成]** セクションで実行が必要な手順はありません。

    a. **SP** 開始モードでアプリケーションを構成する場合は、次の手順を実行します。

    b. **[追加の URL を設定します]** をクリックします。

    c. **[サインオン URL]** ボックスに、URL として「`https://login.umbrella.com/sso`」を入力します。

5. **[Set up Single Sign-On with SAML]\(SAML でシングル サインオンをセットアップします\)**  ページの **[SAML Signing Certificate]\(SAML 署名証明書\)** セクションで、 **[ダウンロード]** をクリックして要件のとおりに指定したオプションから **メタデータ XML** をダウンロードして、お使いのコンピューターに保存します。

    ![証明書のダウンロードのリンク](common/metadataxml.png)

6. **[Cisco Umbrella Admin SSO のセットアップ]** セクションで、要件のとおりに適切な URL をコピーします。

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

このセクションでは、B.Simon に Cisco Umbrella Admin SSO へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

1. Azure portal で **[エンタープライズ アプリケーション]** を選択し、 **[すべてのアプリケーション]** を選択します。
1. アプリケーションの一覧で **[Cisco Umbrella Admin SSO]** を選択します。
1. アプリの概要ページで、 **[管理]** セクションを見つけて、 **[ユーザーとグループ]** を選択します。
1. **[ユーザーの追加]** を選択し、 **[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。
1. **[ユーザーとグループ]** ダイアログの [ユーザー] の一覧から **[B.Simon]** を選択し、画面の下部にある **[選択]** ボタンをクリックします。
1. ユーザーにロールが割り当てられることが想定される場合は、 **[ロールの選択]** ドロップダウンからそれを選択できます。 このアプリに対してロールが設定されていない場合は、[既定のアクセス] ロールが選択されていることを確認します。
1. **[割り当ての追加]** ダイアログで、 **[割り当て]** をクリックします。

## <a name="configure-cisco-umbrella-admin-sso-sso"></a>Cisco Umbrella Admin SSO の SSO の構成

1. 別のブラウザーのウィンドウで、管理者として Cisco Umbrella Admin SSO 企業サイトにサインオンします。

2. メニューの左側で **[Admin]\(管理者\)** をクリックして、**[Authentication]\(認証\)** に移動し、**[SAML]** をクリックします。

    ![管理](./media/cisco-umbrella-tutorial/admin.png)

3. **[Other]\(その他\)** を選択して、**[NEXT]\(次\)** をクリックします。

    ![その他](./media/cisco-umbrella-tutorial/other.png)

4. **[Cisco Umbrella Admin SSO Metadata]\(Cisco Umbrella Admin SSO メタデータ\)** ページで、 **[NEXT]\(次\)** をクリックします。

    ![メタデータ](./media/cisco-umbrella-tutorial/metadata.png)

5. **[Upload Metadata]\(メタデータのアップロード\)** タブで、SAML を事前構成した場合には、**[Click here to change them]\(ここをクリックしてそれらを変更する\)** オプションを選択し、次の手順に従います。

    ![次](./media/cisco-umbrella-tutorial/next.png)

6. **[Option A:Upload XML file]\(オプション A: XML ファイルのアップロード\)** で、Azure portal からダウンロードした **フェデレーション メタデータ XML** ファイルをアップロードします。アップロードすると、次の値が自動的に入力されます。 **[NEXT]\(次\)** をクリックします。

    ![Choosefile](./media/cisco-umbrella-tutorial/choose-file.png)

7. **[Validate SAML Configuration]\(SAML 構成の検証\)** セクションで、**[TEST YOUR SAML CONFIGURATION]\(SAML 構成のテスト\)** をクリックします。

    ![テスト](./media/cisco-umbrella-tutorial/test.png)

8. **[保存]** をクリックします。

### <a name="create-cisco-umbrella-admin-sso-test-user"></a>Cisco Umbrella Admin SSO テスト ユーザーの作成

Azure AD ユーザーが Cisco Umbrella Admin SSO にログインできるようにするには、ユーザーを Cisco Umbrella Admin SSO にプロビジョニングする必要があります。  
Cisco Umbrella Admin SSO の場合、プロビジョニングは手動で行います。

**ユーザー アカウントをプロビジョニングするには、次の手順に従います。**

1. 別のブラウザーのウィンドウで、管理者として Cisco Umbrella Admin SSO 企業サイトにサインオンします。

2. メニューの左側で **[Admin]\(管理者\)** をクリックして、**[Accounts]\(アカウント\)** をクリックします。

    ![アカウント](./media/cisco-umbrella-tutorial/account.png)

3. **[Accounts]\(アカウント\)** ページの右側の一番上にある **[Add]\(追加\)** をクリックして、次の手順を実行します。

    ![ユーザー](./media/cisco-umbrella-tutorial/create-user.png)

    a. **[First Name]\(名\)** フィールドに、名前 (**Britta** など) を入力します。

    b. **[Last Name]\(姓\)** フィールドに、姓 (**Simon** など) を入力します。

    c. **[Choose Delegated Admin Role]\(代理管理者ロールの選択\)** から自分のロールを選択します。

    d. **[Email address]\(メール アドレス\)** フィールドに、ユーザーのメール アドレス (**brittasimon\@contoso.com** など) を入力します。

    e. **[Password]\(パスワード\)** フィールドにパスワードを入力します。

    f. **[Confirm Password]\(パスワードの確認\)** フィールドにパスワードをもう一度入力します。

    g. **[作成]** をクリックします。

## <a name="test-sso"></a>SSO のテスト

このセクションでは、次のオプションを使用して Azure AD のシングル サインオン構成をテストします。 

#### <a name="sp-initiated"></a>SP Initiated:

* Azure portal で **[このアプリケーションをテストします]** をクリックします。 これにより、ログイン フローを開始できる Cisco Umbrella Admin SSO のサインオン URL にリダイレクトされます。  

* Cisco Umbrella Admin SSO のサインオン URL に直接移動し、そこからログイン フローを開始します。

#### <a name="idp-initiated"></a>IDP Initiated:

* Azure portal で **[このアプリケーションをテストします]** をクリックすると、SSO を設定した Cisco Umbrella Admin SSO に自動的にサインインされます。 

また、Microsoft マイ アプリを使用して、任意のモードでアプリケーションをテストすることもできます。 マイ アプリで [Cisco Umbrella Admin SSO] タイルをクリックすると、SP モードで構成されている場合は、ログイン フローを開始するためのアプリケーション サインオン ページにリダイレクトされます。IDP モードで構成されている場合は、SSO を設定した Cisco Umbrella Admin SSO に自動的にサインインされます。 マイ アプリの詳細については、[マイ アプリの概要](../user-help/my-apps-portal-end-user-access.md)に関するページを参照してください。

## <a name="next-steps"></a>次のステップ

Cisco Umbrella Admin SSO を構成したら、組織の機密データを流出と侵入からリアルタイムで保護するセッション制御を適用できます。 セッション制御は、条件付きアクセスを拡張したものです。 [Microsoft Cloud App Security でセッション制御を強制する方法](/cloud-app-security/proxy-deployment-any-app)をご覧ください。