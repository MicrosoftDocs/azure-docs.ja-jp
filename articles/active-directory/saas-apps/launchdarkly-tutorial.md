---
title: 'チュートリアル: Azure Active Directory と LaunchDarkly の統合 | Microsoft Docs'
description: Azure Active Directory と LaunchDarkly の間でシングル サインオンを構成する方法について説明します。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 02/17/2021
ms.author: jeedes
ms.openlocfilehash: d9db86e400d862dd67582ede0bf44b9e9fd1c893
ms.sourcegitcommit: ac035293291c3d2962cee270b33fca3628432fac
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/24/2021
ms.locfileid: "104954804"
---
# <a name="tutorial-azure-active-directory-integration-with-launchdarkly"></a>チュートリアル: Azure Active Directory と LaunchDarkly の統合

このチュートリアルでは、LaunchDarkly と Azure Active Directory (Azure AD) を統合する方法について説明します。 LaunchDarkly と Azure AD を統合すると、次のことができます。

* LaunchDarkly にアクセスできるユーザーを Azure AD で制御できます。
* ユーザーが自分の Azure AD アカウントを使用して LaunchDarkly に自動的にサインインできるように設定できます。
* 1 つの中央サイト (Azure Portal) で自分のアカウントを管理します。

    > [!NOTE]
    > LaunchDarkly と Azure Active Directory の統合は一方向方式です。 統合を構成した後、Azure AD を使用して LaunchDarkly のユーザー、SSO、およびアカウントを管理できますが、LaunchDarkly を使用して Azure のユーザー、SSO、およびアカウントを管理することは **できません**。

## <a name="prerequisites"></a>前提条件

開始するには、次が必要です。

* Azure AD サブスクリプション。 サブスクリプションがない場合は、[無料アカウント](https://azure.microsoft.com/free/)を取得できます。
* LaunchDarkly でのシングル サインオンが有効なサブスクリプション。

## <a name="scenario-description"></a>シナリオの説明

このチュートリアルでは、テスト環境で Azure AD のシングル サインオンを構成してテストします。

* LaunchDarkly では、**IDP** Initiated SSO がサポートされます。
* LaunchDarkly では、**Just-In-Time** ユーザー プロビジョニングがサポートされます。

> [!NOTE]
> このアプリケーションの識別子は固定文字列値であるため、1 つのテナントで構成できるインスタンスは 1 つだけです。

## <a name="add-launchdarkly-from-the-gallery"></a>ギャラリーからの LaunchDarkly の追加

Azure AD への LaunchDarkly の統合を構成するには、ギャラリーから管理対象 SaaS アプリの一覧に LaunchDarkly を追加する必要があります。

1. 職場または学校アカウントか、個人の Microsoft アカウントを使用して、Azure portal にサインインします。
1. 左のナビゲーション ウィンドウで **[Azure Active Directory]** サービスを選択します。
1. **[エンタープライズ アプリケーション]** に移動し、 **[すべてのアプリケーション]** を選択します。
1. 新しいアプリケーションを追加するには、 **[新しいアプリケーション]** を選択します。
1. **[ギャラリーから追加する]** セクションで、検索ボックスに「**LaunchDarkly**」と入力します。
1. 結果のパネルから **[LaunchDarkly]** を選択し、アプリを追加します。 お使いのテナントにアプリが追加されるのを数秒待機します。

## <a name="configure-and-test-azure-ad-sso-for-launchdarkly"></a>LaunchDarkly の Azure AD SSO の構成とテスト

**B.Simon** というテスト ユーザーを使用して、LaunchDarkly に対する Azure AD SSO を構成してテストします。 SSO が機能するためには、Azure AD ユーザーと LaunchDarkly の関連ユーザーとの間にリンク関係を確立する必要があります。

LaunchDarkly に対する Azure AD SSO を構成してテストするには、次の手順を行います。

1. **[Azure AD SSO の構成](#configure-azure-ad-sso)** - ユーザーがこの機能を使用できるようにします。
    1. **[Azure AD のテスト ユーザーの作成](#create-an-azure-ad-test-user)** - B.Simon で Azure AD のシングル サインオンをテストします。
    1. **[Azure AD テスト ユーザーの割り当て](#assign-the-azure-ad-test-user)** - B.Simon が Azure AD シングル サインオンを使用できるようにします。
1. **[LaunchDarkly の SSO の構成](#configure-launchdarkly-sso)** - アプリケーション側でシングル サインオン設定を構成します。
    1. **[LaunchDarkly のテスト ユーザーの作成](#create-launchdarkly-test-user)** - LaunchDarkly で B.Simon に対応するユーザーを作成し、Azure AD の B.Simon にリンクさせます。
1. **[SSO のテスト](#test-sso)** - 構成が機能するかどうかを確認します。

## <a name="configure-azure-ad-sso"></a>Azure AD SSO の構成

これらの手順に従って、Azure portal で Azure AD SSO を有効にします。

1. Azure portal の **LaunchDarkly** アプリケーション統合ページで、 **[管理]** セクションを見つけて、 **[シングル サインオン]** を選択します。
1. **[シングル サインオン方式の選択]** ページで、 **[SAML]** を選択します。
1. **[SAML によるシングル サインオンのセットアップ]** ページで、 **[基本的な SAML 構成]** の鉛筆アイコンをクリックして設定を編集します。

   ![基本的な SAML 構成を編集する](common/edit-urls.png)

4. **[基本的な SAML 構成]** セクションで、次の手順を実行します。

    a. **[識別子]** ボックスに、`app.launchdarkly.com` という URL を入力します。

    b. **[応答 URL]** ボックスに、`https://app.launchdarkly.com/trust/saml2/acs/<customers-unique-id>` のパターンを使用して URL を入力します

    > [!NOTE]
    > 応答 URL 値は、実際の値ではありません。 実際の応答 URL に値を置き換えます。実際の値については後で説明します。 アプリケーションを **IDP** モードで使用する場合は、 **[サインオン URL]** フィールドを空白のままにする必要があります。そうしないと、**IDP** からログインを開始できません。 Azure portal の **[基本的な SAML 構成]** セクションに示されているパターンを参照することもできます。

6. **[SAML でシングル サインオンをセットアップします]** ページの **[SAML 署名証明書]** セクションで、 **[ダウンロード]** をクリックして要件のとおりに指定したオプションからの **証明書 (Base64)** をダウンロードして、お使いのコンピューターに保存します。

    ![証明書のダウンロードのリンク](common/certificatebase64.png)

7. **[LaunchDarkly のセットアップ]** セクションで、要件どおりの適切な URL をコピーします。

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

このセクションでは、B.Simon に LaunchDarkly へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

1. Azure portal で **[エンタープライズ アプリケーション]** を選択し、 **[すべてのアプリケーション]** を選択します。
1. アプリケーションの一覧で **[LaunchDarkly]** を選択します。
1. アプリの概要ページで、 **[管理]** セクションを見つけて、 **[ユーザーとグループ]** を選択します。
1. **[ユーザーの追加]** を選択し、 **[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。
1. **[ユーザーとグループ]** ダイアログの [ユーザー] の一覧から **[B.Simon]** を選択し、画面の下部にある **[選択]** ボタンをクリックします。
1. ユーザーにロールが割り当てられることが想定される場合は、 **[ロールの選択]** ドロップダウンからそれを選択できます。 このアプリに対してロールが設定されていない場合は、[既定のアクセス] ロールが選択されていることを確認します。
1. **[割り当ての追加]** ダイアログで、 **[割り当て]** をクリックします。

## <a name="configure-launchdarkly-sso"></a>LaunchDarkly の SSO の構成

1. 別の Web ブラウザーのウィンドウで、LaunchDarkly 企業サイトに管理者としてログインします。

2. 左側のナビゲーション パネルから **[アカウント設定]** を選択します。

    ![[Production]\(運用\) の下で [Account Settings]\(アカウント設定\) 項目が選択されているスクリーンショット。](./media/launchdarkly-tutorial/configure-1.png)

3. **[セキュリティ]** タブをクリックします。

    ![[Account settings]\(アカウント設定\) の [Security]\(セキュリティ\) タブを示すスクリーンショット。](./media/launchdarkly-tutorial/configure-2.png)

4. **[ENABLE SSO]\(SSO を有効にする)** 、 **[EDIT SAML CONFIGURATION]\(SAML 構成の編集)** の順にクリックします。

    ![[ENABLE S S O]\(S S O を有効にする\) および [EDIT SAML CONFIGURATION]\(SAML 構成の編集\) を操作できる [Single sign-on]\(シングル サインオン\) ページを示すスクリーンショット。](./media/launchdarkly-tutorial/configure-3.png)

5. **[Edit your SAML configuration]\(SAML の構成の編集)** セクションで、次の手順を実行します。

    ![ここで説明されている変更を行うことができる [Edit your SAML configuration]\(SAML の構成の編集\) セクションを示すスクリーンショット。](./media/launchdarkly-tutorial/configure-4.png)

    a. インスタンスの **SAML コンシューマー サービス URL** をコピーし、Azure Portal で、 **[LaunchDarkly ドメインと URL]** セクションの [応答 URL] ボックスに貼り付けます。

    b. **[サインオン URL]** テキストボックスに、Azure portal からコピーした **ログイン URL** の値を貼り付けます。

    c. Azure Portal からダウンロードした証明書をメモ帳で開き、その内容をコピーして **[X.509 証明書]** ボックスに貼り付けます。または、 **[upload one]\(アップロードする)** をクリックして証明書を直接アップロードできます。

    d. **[保存]** をクリックします。

### <a name="create-launchdarkly-test-user"></a>LaunchDarkly テスト ユーザーを作成する

このセクションでは、B.Simon というユーザーを LaunchDarkly に作成します。 LaunchDarkly では、Just-In-Time ユーザー プロビジョニングがサポートされています。これは既定で有効になっています。 このセクションでは、ユーザー側で必要な操作はありません。 LaunchDarkly にユーザーがまだ存在していない場合は、認証後に新規に作成されます。

## <a name="test-sso"></a>SSO のテスト

このセクションでは、次のオプションを使用して Azure AD のシングル サインオン構成をテストします。

* Azure portal で [このアプリケーションをテストします] をクリックすると、SSO を設定した LaunchDarkly に自動的にサインインされます。

* Microsoft マイ アプリを使用することができます。 マイ アプリで [LaunchDarkly] タイルをクリックすると、SSO を設定した LaunchDarkly に自動的にサインインされます。 マイ アプリの詳細については、[マイ アプリの概要](../user-help/my-apps-portal-end-user-access.md)に関するページを参照してください。

## <a name="next-steps"></a>次のステップ

LaunchDarkly を構成したら、組織の機密データを流出と侵入からリアルタイムで保護するセッション制御を適用することができます。 セッション制御は、条件付きアクセスを拡張したものです。 [Microsoft Cloud App Security でセッション制御を強制する方法](/cloud-app-security/proxy-deployment-any-app)をご覧ください。