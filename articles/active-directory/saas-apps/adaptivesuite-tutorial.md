---
title: チュートリアル:Azure Active Directory と Adaptive Insights の統合 | Microsoft Docs
description: Azure Active Directory と Adaptive Insights の間でシングル サインオンを構成する方法について説明します。
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
ms.openlocfilehash: 6372cd9d778210163c461c55119343e6c6911e4d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "101649084"
---
# <a name="tutorial-integrate-adaptive-insights-with-azure-active-directory"></a>チュートリアル:Adaptive Insights を Azure Active Directory と統合する

このチュートリアルでは、Adaptive Insights と Azure Active Directory (Azure AD) を統合する方法について説明します。 Adaptive Insights と Azure AD を統合すると、次のことができます。

* Adaptive Insights にアクセスするユーザーを Azure AD で管理できます。
* ユーザーが自分の Azure AD アカウントを使用して Adaptive Insights に自動的にサインインできます。
* 1 つの中央サイト (Azure Portal) で自分のアカウントを管理します。

## <a name="prerequisites"></a>前提条件

開始するには、次が必要です。

* Azure AD サブスクリプション。 サブスクリプションがない場合は、[無料アカウント](https://azure.microsoft.com/free/)を取得できます。
* Adaptive Insights でのシングル サインオン (SSO) が有効なサブスクリプション。

## <a name="scenario-description"></a>シナリオの説明

このチュートリアルでは、テスト環境で Azure AD の SSO を構成してテストします。

* Adaptive Insights では、**IDP** によって開始される SSO がサポートされます

## <a name="add-adaptive-insights-from-the-gallery"></a>ギャラリーからの Adaptive Insights の追加

Azure AD への Adaptive Insights の統合を構成するには、ギャラリーからマネージド SaaS アプリの一覧に Adaptive Insights を追加する必要があります。

1. 職場または学校アカウントか、個人の Microsoft アカウントを使用して、Azure portal にサインインします。
1. 左のナビゲーション ウィンドウで **[Azure Active Directory]** サービスを選択します。
1. **[エンタープライズ アプリケーション]** に移動し、 **[すべてのアプリケーション]** を選択します。
1. 新しいアプリケーションを追加するには、 **[新しいアプリケーション]** を選択します。
1. **[ギャラリーから追加する]** セクションで、検索ボックスに、「**Adaptive Insights**」と入力します。
1. 結果ウィンドウで **[Adaptive Insights]** を選択し、アプリケーションを追加します。 お使いのテナントにアプリが追加されるのを数秒待機します。

## <a name="configure-and-test-azure-ad-sso-for-adaptive-insights"></a>Adaptive Insights の Azure AD SSO の構成とテスト

**B.Simon** というテスト ユーザーを使用して、Adaptive Insights に対する Azure AD SSO を構成してテストします。 SSO を機能させるために、Azure AD ユーザーと Adaptive Insights の関連ユーザーとの間にリンク関係を確立する必要があります。

Adaptive Insights に対して Azure AD SSO を構成してテストするには、次の手順を行います。

1. **[Azure AD SSO の構成](#configure-azure-ad-sso)** - ユーザーがこの機能を使用できるようにします。
    1. **[Azure AD のテスト ユーザーの作成](#create-an-azure-ad-test-user)** - B.Simon で Azure AD のシングル サインオンをテストします。
    1. **[Azure AD テスト ユーザーの割り当て](#assign-the-azure-ad-test-user)** - B.Simon が Azure AD シングル サインオンを使用できるようにします。
1. **[Adaptive Insights の SSO の構成](#configure-adaptive-insights-sso)** - アプリケーション側でシングル サインオン設定を構成します。
    1. **[Adaptive Insights テスト ユーザーの作成](#create-adaptive-insights-test-user)** - Adaptive Insights で B.Simon に対応するユーザーを作成し、Azure AD のこのユーザーにリンクさせます。
1. **[SSO のテスト](#test-sso)** - 構成が機能するかどうかを確認します。

### <a name="configure-azure-ad-sso"></a>Azure AD SSO の構成

これらの手順に従って、Azure portal で Azure AD SSO を有効にします。

1. Azure portal の **Adaptive Insights** アプリケーション統合ページで、 **[管理]** セクションを見つけて、 **[シングル サインオン]** を選択します。
1. **[シングル サインオン方式の選択]** ページで、 **[SAML]** を選択します。
1. **[SAML によるシングル サインオンのセットアップ]** ページで、 **[基本的な SAML 構成]** の鉛筆アイコンをクリックして設定を編集します。

   ![基本的な SAML 構成を編集する](common/edit-urls.png)

1. **[基本的な SAML 構成]** セクションで、次の手順を実行します。

    a. **[識別子]** ボックスに、`https://login.adaptiveinsights.com:443/samlsso/<unique-id>` の形式で URL を入力します。

    b. **[応答 URL]** ボックスに、`https://login.adaptiveinsights.com:443/samlsso/<unique-id>` のパターンを使用して URL を入力します

    > [!NOTE]
    > [識別子 (エンティティ ID)] と [応答 URL] の値は、Adaptive Insights の **[SAML SSO 設定]** ページから取得できます。

4. **[SAML でシングル サインオンをセットアップします]** ページの **[SAML 署名証明書]** セクションで、 **[証明書 (Base64)]** を見つけて、 **[ダウンロード]** を選択し、証明書をダウンロードして、お使いのコンピューターに保存します。

    ![証明書のダウンロードのリンク](common/certificatebase64.png)

6. **[Adaptive Insights のセット アップ]** セクションで、要件に基づいて適切な URL をコピーします。

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

このセクションでは、B.Simon に Adaptive Insights へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

1. Azure portal で **[エンタープライズ アプリケーション]** を選択し、 **[すべてのアプリケーション]** を選択します。
1. アプリケーションの一覧で **[Adaptive Insights]** を選択します。
1. アプリの概要ページで、 **[管理]** セクションを見つけて、 **[ユーザーとグループ]** を選択します。
1. **[ユーザーの追加]** を選択し、 **[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。
1. **[ユーザーとグループ]** ダイアログの [ユーザー] の一覧から **[B.Simon]** を選択し、画面の下部にある **[選択]** ボタンをクリックします。
1. ユーザーにロールが割り当てられることが想定される場合は、 **[ロールの選択]** ドロップダウンからそれを選択できます。 このアプリに対してロールが設定されていない場合は、[既定のアクセス] ロールが選択されていることを確認します。
1. **[割り当ての追加]** ダイアログで、 **[割り当て]** をクリックします。

### <a name="configure-adaptive-insights-sso"></a>Adaptive Insights SSO の構成

1. 別の Web ブラウザー ウィンドウで、Adaptive Insights 企業サイトに管理者としてサインインします。

2. **[Administration]** に移動します。

    ![ナビゲーション パネルの [Administration]\(管理\) が強調表示されているスクリーンショット。](./media/adaptivesuite-tutorial/administration.png "[Admin]")

3. **[ユーザーとロール]** セクションで、 **[SAML SSO 設定]** をクリックします。

    ![SAML SSO 設定の管理](./media/adaptivesuite-tutorial/settings.png "[SAML SSO 設定の管理]")

4. **[SAML SSO 設定]** ページで、次の手順を実行します。

    ![SAML SSO 設定](./media/adaptivesuite-tutorial/saml.png "[SAML SSO 設定]")

    a. **[ID プロバイダー名]** テキスト ボックスに、構成の名前を入力します。

    b. Azure portal からコピーした **Azure AD 識別子** の値を **[Identity provider entity ID]\(ID プロバイダーのエンティティ ID\)** ボックスに貼り付けます。

    c. Azure portal からコピーした **ログイン URL** の値を **[Identity provider SSO URL]\(ID プロバイダーの SSO URL\)** ボックスに貼り付けます。

    d. Azure portal からコピーした **ログアウト URL** の値を **[Custom logout URL]\(カスタム ログアウト URL\)** ボックスに貼り付けます。

    e. ダウンロードした証明書をアップロードするには、 **[ファイルの選択]** をクリックします。

    f. 次のように選択します。

     * **[SAML ユーザー ID]** では、 **[ユーザーの Adaptive Insights ユーザー名]** を選択します。

     * **[SAML ユーザー ID の場所]** では、 **[サブジェクトの NameID 内のユーザー ID ]** を選択します。

     * **[SAML NameID 形式]** では、 **[電子メール アドレス]** を選択します。

     * **[SAML を有効にする]** では、 **[SAML SSO を許可して、Adaptive Insights に直接ログインする]** を選択します。

    g. **Adaptive Insights の SSO URL** をコピーし、Azure portal の **[基本的な SAML 構成]** セクションの **[識別子 (エンティティ ID)]** ボックスと **[応答 URL]** ボックスに貼り付けます。

    h. **[保存]** をクリックします。

### <a name="create-adaptive-insights-test-user"></a>Adaptive Insights のテスト ユーザーの作成

Azure AD ユーザーが Adaptive Insights にサインインできるようにするには、ユーザーを Adaptive Insights にプロビジョニングする必要があります。 Adaptive Insights の場合、プロビジョニングは手動で行います。

**ユーザー プロビジョニングを構成するには、次の手順に従います。**

1. **Adaptive Insights** 企業サイトに管理者としてサインインします。

2. **[Administration]** に移動します。

   ![管理者](./media/adaptivesuite-tutorial/administration.png "[Admin]")

3. **[ユーザーとロール]** セクションで **[ユーザー]** をクリックします。

   ![ユーザーの追加](./media/adaptivesuite-tutorial/users.png "ユーザーの追加")

4. **[新しいユーザー]** セクションで、次の手順に従います。

   ![[送信]](./media/adaptivesuite-tutorial/new.png "[送信]")

   a. 関連するテキスト ボックスに、プロビジョニングする有効な Azure Active Directory ユーザーの **[名前]** 、 **[ユーザー名]** 、 **[電子メール]** 、 **[パスワード]** を入力します。

   b. **[ロール]** を選択します。

   c. **[送信]** をクリックします。

> [!NOTE]
> 他の Adaptive Insights ユーザー アカウント作成ツールや、Adaptive Insights から提供されている API を使用して、Azure AD ユーザー アカウントをプロビジョニングできます。

### <a name="test-sso"></a>SSO のテスト 

このセクションでは、次のオプションを使用して Azure AD のシングル サインオン構成をテストします。

* Azure portal で [このアプリケーションをテストします] をクリックすると、SSO を設定した Adaptive Insights に自動的にサインインします。

* Microsoft マイ アプリを使用することができます。 マイ アプリで [Adaptive Insights] タイルをクリックすると、SSO を設定した Adaptive Insights に自動的にサインインします。 マイ アプリの詳細については、[マイ アプリの概要](../user-help/my-apps-portal-end-user-access.md)に関するページを参照してください。

## <a name="next-steps"></a>次のステップ

Adaptive Insights を構成したら、組織の機密データを流出と侵入からリアルタイムで保護するセッション制御を適用することができます。 セッション制御は、条件付きアクセスを拡張したものです。 [Microsoft Cloud App Security でセッション制御を強制する方法](/cloud-app-security/proxy-deployment-any-app)をご覧ください。