---
title: チュートリアル:Azure Active Directory と ExpenseIn の統合 | Microsoft Docs
description: Azure Active Directory と ExpenseIn の間でシングル サインオンを構成する方法について説明します。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 07/17/2020
ms.author: jeedes
ms.openlocfilehash: eb7062b745ae6eb97dc2cd4ba2051cc221031630
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "96179165"
---
# <a name="tutorial-integrate-expensein-with-azure-active-directory"></a>チュートリアル:Azure Active Directory と ExpenseIn の統合

このチュートリアルでは、ExpenseIn と Azure Active Directory (Azure AD) を統合する方法について説明します。 Azure AD と ExpenseIn を統合すると、次のことができます。

* ExpenseIn にアクセスする Azure AD ユーザーを制御する。
* ユーザーが自分の Azure AD アカウントを使用して ExpenseIn に自動的にサインインできるようにする。
* 1 つの中央サイト (Azure Portal) で自分のアカウントを管理します。

SaaS アプリと Azure AD の統合の詳細については、「[Azure Active Directory でのアプリケーションへのシングル サインオン](../manage-apps/what-is-single-sign-on.md)」を参照してください。

## <a name="prerequisites"></a>前提条件

開始するには、次が必要です。

* Azure AD サブスクリプション。 サブスクリプションがない場合は、[無料アカウント](https://azure.microsoft.com/free/)を取得できます。
* ExpenseIn でのシングル サインオン (SSO) が有効なサブスクリプション。

## <a name="scenario-description"></a>シナリオの説明

このチュートリアルでは、テスト環境で Azure AD の SSO を構成してテストします。 
* ExpenseIn では、**SP と IDP** によって開始される SSO がサポートされます。
* ExpenseIn を構成したら、組織の機密データを流出と侵入からリアルタイムで保護するセッション制御を適用することができます。 セッション制御は、条件付きアクセスを拡張したものです。 [Microsoft Cloud App Security でセッション制御を強制する方法](/cloud-app-security/proxy-deployment-any-app)をご覧ください。


## <a name="adding-expensein-from-the-gallery"></a>ギャラリーからの ExpenseIn の追加

Azure AD への ExpenseIn の統合を構成するには、ギャラリーから管理対象 SaaS アプリの一覧に ExpenseIn を追加する必要があります。

1. 職場または学校アカウントか、個人の Microsoft アカウントを使用して、[Azure portal](https://portal.azure.com) にサインインします。
1. 左のナビゲーション ウィンドウで **[Azure Active Directory]** サービスを選択します。
1. **[エンタープライズ アプリケーション]** に移動し、 **[すべてのアプリケーション]** を選択します。
1. 新しいアプリケーションを追加するには、 **[新しいアプリケーション]** を選択します。
1. **[ギャラリーから追加する]** セクションで、検索ボックスに、「**ExpenseIn**」と入力します。
1. 結果ウィンドウで **[ExpenseIn]** を選択し、アプリケーションを追加します。 お使いのテナントにアプリが追加されるのを数秒待機します。

## <a name="configure-and-test-azure-ad-sso-for-expensein"></a>ExpenseIn の Azure AD SSO の構成とテスト

**B.Simon** というテスト ユーザーを使用して、ExpenseIn に対する Azure AD SSO を構成してテストします。 SSO を機能させるために、Azure AD ユーザーと ExpenseIn の関連ユーザーとの間にリンク関係を確立する必要があります。

ExpenseIn で Azure AD SSO を構成してテストするには、次の構成要素を完了する必要があります。

1. **[Azure AD SSO の構成](#configure-azure-ad-sso)** - ユーザーがこの機能を使用できるようにします。
    1. **[Azure AD のテスト ユーザーの作成](#create-an-azure-ad-test-user)** - B.Simon で Azure AD のシングル サインオンをテストします。
    1. **[Azure AD テスト ユーザーの割り当て](#assign-the-azure-ad-test-user)** - B.Simon が Azure AD シングル サインオンを使用できるようにします。
1. **[ExpenseIn の SSO の構成](#configure-expensein-sso)** - アプリケーション側で SSO 設定を構成します。
    1. **[ExpenseIn のテスト ユーザーの作成](#create-expensein-test-user)** - ExpenseIn で B.Simon に対応するユーザーを作成し、Azure AD のこのユーザーにリンクさせます。
1. **[SSO のテスト](#test-sso)** - 構成が機能するかどうかを確認します。

## <a name="configure-azure-ad-sso"></a>Azure AD SSO の構成

これらの手順に従って、Azure portal で Azure AD SSO を有効にします。

1. [Azure portal](https://portal.azure.com/) の **ExpenseIn** アプリケーション統合ページで、 **[管理]** セクションを見つけて、 **[シングル サインオン]** を選択します。
1. **[シングル サインオン方式の選択]** ページで、 **[SAML]** を選択します。
1. **[SAML でシングル サインオンをセットアップします]** ページで、 **[基本的な SAML 構成]** の編集/ペン アイコンをクリックして設定を編集します。

   ![基本的な SAML 構成を編集する](common/edit-urls.png)

4. アプリは Azure と事前に統合済みであるため、 **[基本的な SAML 構成]** セクションで実行が必要な手順はありません。

5. アプリケーションを **SP** 開始モードで構成する場合は、 **[追加の URL を設定します]** をクリックして次の手順を実行します。

    **[サインオン URL]** テキスト ボックスに URL として「`https://app.expensein.com/saml`」と入力します。

1. **[SAML でシングル サインオンをセットアップします]** ページの **[SAML 署名証明書]** セクションで、コピー ボタンをクリックして **[アプリのフェデレーション メタデータ URL]** をコピーし、 **[ダウンロード]** をクリックして **[証明書 (Base64)]** をダウンロードし、お使いのコンピューター上に保存します。

   ![証明書のダウンロードのリンク](./media/expensein-tutorial/copy-metdataurl-certificate.png)

1. **[ExpenseIn のセットアップ]** セクションで、要件に基づいて適切な URL をコピーします。

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

このセクションでは、B.Simon に ExpenseIn へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

1. Azure portal で **[エンタープライズ アプリケーション]** を選択し、 **[すべてのアプリケーション]** を選択します。
1. アプリケーションの一覧で **[ExpenseIn]** を選択します。
1. アプリの概要ページで、 **[管理]** セクションを見つけて、 **[ユーザーとグループ]** を選択します。

   ![[ユーザーとグループ] リンク](common/users-groups-blade.png)

1. **[ユーザーの追加]** を選択し、 **[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。

    ![[ユーザーの追加] リンク](common/add-assign-user.png)

1. **[ユーザーとグループ]** ダイアログの [ユーザー] の一覧から **[B.Simon]** を選択し、画面の下部にある **[選択]** ボタンをクリックします。
1. SAML アサーション内に任意のロール値が必要な場合、 **[ロールの選択]** ダイアログでユーザーに適したロールを一覧から選択し、画面の下部にある **[選択]** をクリックします。
1. **[割り当ての追加]** ダイアログで、 **[割り当て]** をクリックします。


## <a name="configure-expensein-sso"></a>ExpenseIn の SSO の構成

1. ExpenseIn 内での構成を自動化するには、**[拡張機能のインストール]** をクリックして **My Apps Secure Sign-in ブラウザー拡張機能** をインストールする必要があります。

    ![マイ アプリの拡張機能](common/install-myappssecure-extension.png)

1. ブラウザーに拡張機能を追加した後、 **[ExpenseIn のセットアップ]** をクリックすると、ExpenseIn アプリケーションに移動します。 そこから、管理者の資格情報を入力して ExpenseIn にサインインします。 ブラウザー拡張機能によりアプリケーションが自動的に構成され、手順 3 から 5 が自動化されます。

    ![セットアップの構成](common/setup-sso.png)

1. ExpenseIn を手動で設定する場合は、ExpenseIn の企業サイトに管理者としてログインします。

1. ページ上部の **[管理者]** をクリックし、 **[シングル サインオン]** に移動して **[プロバイダーの追加]** をクリックします。

     ![[管理者] タブ、[シングル サインオン - プロバイダー] ページ、選択されている [プロバイダーの追加] を示すスクリーンショット。](./media/expenseIn-tutorial/config01.png)

1. **[New Identity Provider] (新しい ID プロバイダー)** ポップアップで、次の手順に従います。

    ![値が入力された [Edit Identity Provider]\(ID プロバイダーの編集\) ポップアップを示すスクリーンショット。](./media/expenseIn-tutorial/config02.png)

    a. **[プロバイダー名]** ボックスに「Azure」などの名前を入力します。

    b. **[Allow Provider Intitated Sign-On]\(プロバイダーによって開始されるサインオンを許可する\)** で **[Yes]\(はい\)** を選択します。

    c. **[ターゲット URL]** ボックスに、Azure portal からコピーした **ログイン URL** の値を貼り付けます。

    d. **[Issuer]\(発行者\)** ボックスに、Azure portal からコピーした **Azure AD 識別子** の値を貼り付けます。

    e. 証明書 (Base64) をメモ帳で開き、その内容をコピーして **[証明書]** ボックスに貼り付けます。

    f. **Create** をクリックしてください。

### <a name="create-expensein-test-user"></a>ExpenseIn のテスト ユーザーの作成

Azure AD ユーザーが ExpenseIn にサインインできるようにするには、そのユーザーを ExpenseIn にプロビジョニングする必要があります。 ExpenseIn では、プロビジョニングは手動のタスクです。

**ユーザー アカウントをプロビジョニングするには、次の手順に従います。**

1. 管理者として ExpenseIn にサインインします。

2. ページ上部の **[管理者]** をクリックし、 **[ユーザー]** に移動して **[新しいユーザー]** をクリックします。

     ![[管理者] タブと、[新しいユーザー] が選択されている [ユーザーの管理] ページを示すスクリーンショット。](./media/expenseIn-tutorial/config03.png)

3. **[詳細]** ポップアップで、次の手順に従います。

    ![ExpenseIn の構成](./media/expenseIn-tutorial/config04.png)

    a. **[名]** ボックスに、ユーザーの名を入力します (例: **B**)。

    b. **[Last Name]\(姓\)** ボックスに、ユーザーの姓を入力します (例: **Simon**)。

    c. **[電子メール]** ボックスに、ユーザーのメール アドレスを入力します (例: `B.Simon@contoso.com`)。

    d. **Create** をクリックしてください。

## <a name="test-sso"></a>SSO のテスト

アクセス パネル上で [ExpenseIn] タイルを選択すると、SSO を設定した ExpenseIn に自動的にサインインします。 アクセス パネルの詳細については、[アクセス パネルの概要](../user-help/my-apps-portal-end-user-access.md)に関する記事を参照してください。

## <a name="additional-resources"></a>その他のリソース

- [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](./tutorial-list.md)

- [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)

- [Azure Active Directory の条件付きアクセスとは](../conditional-access/overview.md)

- [Azure AD で ExpenseIn を試す](https://aad.portal.azure.com/)

- [Microsoft Cloud App Security におけるセッション制御とは](/cloud-app-security/proxy-intro-aad)

- [高度な可視性と制御によって ExpenseIn を保護する方法](/cloud-app-security/proxy-intro-aad)