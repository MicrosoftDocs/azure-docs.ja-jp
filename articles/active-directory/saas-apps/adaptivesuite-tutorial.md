---
title: チュートリアル:Azure Active Directory と Adaptive Insights の統合 | Microsoft Docs
description: Azure Active Directory と Adaptive Insights の間でシングル サインオンを構成する方法について説明します。
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 13af9d00-116a-41b8-8ca0-4870b31e224c
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 07/19/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: ad059e6047dd883c0a5aab0d714d999840050584
ms.sourcegitcommit: ac29357a47cc05afdf0f84834de5277598f4d87c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/03/2019
ms.locfileid: "70213620"
---
# <a name="tutorial-integrate-adaptive-insights-with-azure-active-directory"></a>チュートリアル:Adaptive Insights を Azure Active Directory と統合する

このチュートリアルでは、Adaptive Insights と Azure Active Directory (Azure AD) を統合する方法について説明します。 Adaptive Insights と Azure AD を統合すると、次のことができます。

* Adaptive Insights にアクセスするユーザーを Azure AD で管理できます。
* ユーザーが自分の Azure AD アカウントを使用して Adaptive Insights に自動的にサインインできます。
* 1 つの中央サイト (Azure Portal) で自分のアカウントを管理できます。

SaaS アプリと Azure AD の統合の詳細については、「[Azure Active Directory でのアプリケーションへのシングル サインオン](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)」を参照してください。

## <a name="prerequisites"></a>前提条件

開始するには、次が必要です。

* Azure AD サブスクリプション。 サブスクリプションがない場合は、[無料アカウント](https://azure.microsoft.com/free/)を取得できます。
* Adaptive Insights でのシングル サインオン (SSO) が有効なサブスクリプション。

## <a name="scenario-description"></a>シナリオの説明

このチュートリアルでは、テスト環境で Azure AD の SSO を構成してテストします。

* Adaptive Insights では、**IDP** によって開始される SSO がサポートされます

## <a name="adding-adaptive-insights-from-the-gallery"></a>ギャラリーからの Adaptive Insights の追加

Azure AD への Adaptive Insights の統合を構成するには、ギャラリーからマネージド SaaS アプリの一覧に Adaptive Insights を追加する必要があります。

1. 職場または学校アカウントか、個人の Microsoft アカウントを使用して、[Azure portal](https://portal.azure.com) にサインインします。
1. 左のナビゲーション ウィンドウで **[Azure Active Directory]** サービスを選択します。
1. **[エンタープライズ アプリケーション]** に移動し、 **[すべてのアプリケーション]** を選択します。
1. 新しいアプリケーションを追加するには、 **[新しいアプリケーション]** を選択します。
1. **[ギャラリーから追加する]** セクションで、検索ボックスに、「**Adaptive Insights**」と入力します。
1. 結果ウィンドウで **[Adaptive Insights]** を選択し、アプリケーションを追加します。 お使いのテナントにアプリが追加されるのを数秒待機します。


## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成とテスト

**B.Simon** というテスト ユーザーを使用して、Adaptive Insights に対する Azure AD SSO を構成してテストします。 SSO を機能させるために、Azure AD ユーザーと Adaptive Insights の関連ユーザーとの間にリンク関係を確立する必要があります。

Adaptive Insights で Azure AD SSO を構成してテストするには、次の構成要素を完了する必要があります。

1. **[Azure AD SSO の構成](#configure-azure-ad-sso)** - ユーザーがこの機能を使用できるようにします。
2. **[Adaptive Insights の SSO の構成](#configure-adaptive-insights-sso)** - アプリケーション側でシングル サインオン設定を構成します。
3. **[Azure AD のテスト ユーザーの作成](#create-an-azure-ad-test-user)** - B.Simon で Azure AD のシングル サインオンをテストします。
4. **[Azure AD テスト ユーザーの割り当て](#assign-the-azure-ad-test-user)** - B.Simon が Azure AD シングル サインオンを使用できるようにします。
5. **[Adaptive Insights テスト ユーザーの作成](#create-adaptive-insights-test-user)** - Adaptive Insights で B.Simon に対応するユーザーを作成し、Azure AD のこのユーザーにリンクさせます。
6. **[SSO のテスト](#test-sso)** - 構成が機能するかどうかを確認します。

### <a name="configure-azure-ad-sso"></a>Azure AD SSO の構成

これらの手順に従って、Azure portal で Azure AD SSO を有効にします。

1. [Azure portal](https://portal.azure.com/) の **Adaptive Insights** アプリケーション統合ページで、 **[管理]** セクションを見つけて、 **[シングル サインオン]** を選択します。
1. **[シングル サインオン方式の選択]** ページで、 **[SAML]** を選択します。
1. **[SAML でシングル サインオンをセットアップします]** ページで、 **[基本的な SAML 構成]** の編集/ペン アイコンをクリックして設定を編集します。

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

### <a name="configure-adaptive-insights-sso"></a>Adaptive Insights SSO の構成

1. 別の Web ブラウザー ウィンドウで、Adaptive Insights 企業サイトに管理者としてサインインします。

2. **[Administration]** に移動します。

    ![管理](./media/adaptivesuite-tutorial/ic805644.png "Admin")

3. **[ユーザーとロール]** セクションで、 **[SAML SSO 設定]** をクリックします。

    ![SAML SSO 設定の管理](./media/adaptivesuite-tutorial/ic805645.png "Manage SAML SSO Settings")

4. **[SAML SSO 設定]** ページで、次の手順を実行します。

    ![SAML SSO 設定](./media/adaptivesuite-tutorial/ic805646.png "SAML SSO Settings")

    a. **[ID プロバイダー名]** テキスト ボックスに、構成の名前を入力します。

    b. Azure portal からコピーした **Azure AD 識別子**の値を **[Identity provider entity ID]\(ID プロバイダーのエンティティ ID\)** ボックスに貼り付けます。

    c. Azure portal からコピーした**ログイン URL** の値を **[Identity provider SSO URL]\(ID プロバイダーの SSO URL\)** ボックスに貼り付けます。

    d. Azure portal からコピーした**ログアウト URL** の値を **[Custom logout URL]\(カスタム ログアウト URL\)** ボックスに貼り付けます。

    e. ダウンロードした証明書をアップロードするには、 **[ファイルの選択]** をクリックします。

    f. 次のように選択します。

     * **[SAML ユーザー ID]** では、 **[ユーザーの Adaptive Insights ユーザー名]** を選択します。

     * **[SAML ユーザー ID の場所]** では、 **[サブジェクトの NameID 内のユーザー ID ]** を選択します。

     * **[SAML NameID 形式]** では、 **[電子メール アドレス]** を選択します。

     * **[SAML を有効にする]** では、 **[SAML SSO を許可して、Adaptive Insights に直接ログインする]** を選択します。

    g. **Adaptive Insights の SSO URL** をコピーし、Azure portal の **[基本的な SAML 構成]** セクションの **[識別子 (エンティティ ID)]** ボックスと **[応答 URL]** ボックスに貼り付けます。

    h. **[Save]** をクリックします。

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

   ![[ユーザーとグループ] リンク](common/users-groups-blade.png)

1. **[ユーザーの追加]** を選択し、 **[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。

    ![[ユーザーの追加] リンク](common/add-assign-user.png)

1. **[ユーザーとグループ]** ダイアログの [ユーザー] の一覧から **[B.Simon]** を選択し、画面の下部にある **[選択]** ボタンをクリックします。
1. SAML アサーション内に任意のロール値が必要な場合、 **[ロールの選択]** ダイアログでユーザーに適したロールを一覧から選択し、画面の下部にある **[選択]** をクリックします。
1. **[割り当ての追加]** ダイアログで、 **[割り当て]** をクリックします。

### <a name="create-adaptive-insights-test-user"></a>Adaptive Insights のテスト ユーザーの作成

Azure AD ユーザーが Adaptive Insights にサインインできるようにするには、ユーザーを Adaptive Insights にプロビジョニングする必要があります。 Adaptive Insights の場合、プロビジョニングは手動で行います。

**ユーザー プロビジョニングを構成するには、次の手順に従います。**

1. **Adaptive Insights** 企業サイトに管理者としてサインインします。

2. **[Administration]** に移動します。

   ![管理](./media/adaptivesuite-tutorial/IC805644.png "Admin")

3. **[ユーザーとロール]** セクションで **[ユーザー]** をクリックします。

   ![ユーザーの追加](./media/adaptivesuite-tutorial/IC805648.png "Add User")

4. **[新しいユーザー]** セクションで、次の手順に従います。

   ![送信](./media/adaptivesuite-tutorial/IC805649.png "Submit")

   a. 関連するテキスト ボックスに、プロビジョニングする有効な Azure Active Directory ユーザーの **[名前]** 、 **[ユーザー名]** 、 **[電子メール]** 、 **[パスワード]** を入力します。

   b. **[ロール]** を選択します。

   c. **[送信]** をクリックします。

> [!NOTE]
> 他の Adaptive Insights ユーザー アカウント作成ツールまたは Adaptive Insights から提供されている API を使用して、AAD ユーザー アカウントをプロビジョニングできます。

### <a name="test-sso"></a>SSO のテスト 

このセクションでは、アクセス パネルを使用して Azure AD のシングル サインオン構成をテストします。

アクセス パネル上で [Adaptive Insights] タイルをクリックすると、SSO を設定した Adaptive Insights に自動的にサインインします。 アクセス パネルの詳細については、[アクセス パネルの概要](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)に関する記事を参照してください。

## <a name="additional-resources"></a>その他のリソース

- [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Azure Active Directory でのアプリケーション アクセスとシングル サインオンとは](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Azure Active Directory の条件付きアクセスとは](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

