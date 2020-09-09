---
title: チュートリアル:Azure Active Directory と OpsGenie の統合 | Microsoft Docs
description: Azure Active Directory と OpsGenie の間でシングル サインオンを構成する方法について説明します。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/19/2020
ms.author: jeedes
ms.openlocfilehash: 0ebcb746e10ae910c695e52fb053fc5d0b316e70
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/18/2020
ms.locfileid: "88543909"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-opsgenie"></a>チュートリアル:Azure Active Directory シングル サインオン (SSO) と OpsGenie の統合

このチュートリアルでは、OpsGenie と Azure Active Directory (Azure AD) を統合する方法について説明します。 Azure AD と OpsGenie を統合すると、次のことができます。

* OpsGenie にアクセスできるユーザーを Azure AD で制御できます。
* ユーザーが自分の Azure AD アカウントを使用して OpsGenie に自動的にサインインできるように設定できます。
* 1 つの中央サイト (Azure Portal) で自分のアカウントを管理します。

SaaS アプリと Azure AD の統合の詳細については、「[Azure Active Directory でのアプリケーションへのシングル サインオン](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on)」を参照してください。

## <a name="prerequisites"></a>前提条件

開始するには、次が必要です。

* Azure AD サブスクリプション。 サブスクリプションがない場合は、[無料アカウント](https://azure.microsoft.com/free/)を取得できます。
* OpsGenie でのシングル サインオン (SSO) が有効なサブスクリプション。

## <a name="scenario-description"></a>シナリオの説明

このチュートリアルでは、テスト環境で Azure AD の SSO を構成してテストします。

* OpsGenie では、**IDP** Initiated SSO がサポートされます
* OpsGenie を構成したら、組織の機密データを流出と侵入からリアルタイムで保護するセッション制御を適用することができます。 セッション制御は、条件付きアクセスを拡張したものです。 [Microsoft Cloud App Security でセッション制御を強制する方法](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app)をご覧ください。

## <a name="adding-opsgenie-from-the-gallery"></a>ギャラリーからの OpsGenie の追加

Azure AD への OpsGenie の統合を構成するには、ギャラリーから管理対象 SaaS アプリの一覧に OpsGenie を追加する必要があります。

1. 職場または学校アカウントか、個人の Microsoft アカウントを使用して、[Azure portal](https://portal.azure.com) にサインインします。
1. 左のナビゲーション ウィンドウで **[Azure Active Directory]** サービスを選択します。
1. **[エンタープライズ アプリケーション]** に移動し、 **[すべてのアプリケーション]** を選択します。
1. 新しいアプリケーションを追加するには、 **[新しいアプリケーション]** を選択します。
1. **[ギャラリーから追加する]** セクションで、検索ボックスに「**OpsGenie**」と入力します。
1. 結果のパネルから **[OpsGenie]** を選択し、アプリを追加します。 お使いのテナントにアプリが追加されるのを数秒待機します。

## <a name="configure-and-test-azure-ad-single-sign-on-for-opsgenie"></a>OpsGenie の Azure AD シングル サインオンの構成とテスト

**B.Simon** というテスト ユーザーを使用して、OpsGenie に対する Azure AD SSO を構成してテストします。 SSO が機能するためには、Azure AD ユーザーと OpsGenie の関連ユーザーとの間にリンク関係を確立する必要があります。

OpsGenie に対する Azure AD SSO を構成してテストするには、次の構成要素を完了します。

1. **[Azure AD SSO の構成](#configure-azure-ad-sso)** - ユーザーがこの機能を使用できるようにします。
    * **[Azure AD のテスト ユーザーの作成](#create-an-azure-ad-test-user)** - B.Simon で Azure AD のシングル サインオンをテストします。
    * **[Azure AD テスト ユーザーの割り当て](#assign-the-azure-ad-test-user)** - B.Simon が Azure AD シングル サインオンを使用できるようにします。
1. **[OpsGenie の SSO の構成](#configure-opsgenie-sso)** - アプリケーション側でシングル サインオン設定を構成します。
    * **[OpsGenie テスト ユーザーの作成](#create-opsgenie-test-user)** - OpsGenie で B. Simon に対応するユーザーを作成し、Azure AD の B.Simon にリンクさせます。
1. **[SSO のテスト](#test-sso)** - 構成が機能するかどうかを確認します。

## <a name="configure-azure-ad-sso"></a>Azure AD SSO の構成

これらの手順に従って、Azure portal で Azure AD SSO を有効にします。

1. [Azure portal](https://portal.azure.com/) の **OpsGenie** アプリケーション統合ページで、 **[管理]** セクションを探して、 **[シングル サインオン]** を選択します。
1. **[シングル サインオン方式の選択]** ページで、 **[SAML]** を選択します。
1. **[SAML でシングル サインオンをセットアップします]** ページで、 **[基本的な SAML 構成]** の編集 (ペン) アイコンをクリックして設定を編集します。

   ![基本的な SAML 構成を編集する](common/edit-urls.png)

1. **[基本的な SAML 構成]** セクションで、次の手順を実行します。

    a. **[識別子]** ボックスに、`https://app.opsginie.com/auth/saml/<UNIQUEID>` の形式で URL を入力します。

    b. **[応答 URL]** ボックスに、`https://app.opsginie.com/auth/saml?id=<UNIQUEID>` のパターンを使用して URL を入力します

    > [!NOTE]
    > これらは実際の値ではありません。 実際の識別子と応答 URL に値を置き換えます。実際の値については後で説明します。

5. **Set up Single Sign-On with SAML\(SAML でのシングルサインオンの設定** ページの **SAML 署名証明書** セクションで、コピー ボタンをクリックして **App Federation Metadata Url\(アプリのフェデレーション メタデータ URL)** をコピーして、コンピューターに保存します。

    ![証明書のダウンロードのリンク](common/copy-metadataurl.png)

1. **[OpsGenie のセットアップ]** セクションで、要件に基づいて適切な URL をコピーします。

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

このセクションでは、B.Simon に OpsGenie へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

1. Azure portal で **[エンタープライズ アプリケーション]** を選択し、 **[すべてのアプリケーション]** を選択します。
1. アプリケーションの一覧で **[OpsGenie]** を選択します。
1. アプリの概要ページで、 **[管理]** セクションを見つけて、 **[ユーザーとグループ]** を選択します。

   ![[ユーザーとグループ] リンク](common/users-groups-blade.png)

1. **[ユーザーの追加]** を選択し、 **[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。

    ![[ユーザーの追加] リンク](common/add-assign-user.png)

1. **[ユーザーとグループ]** ダイアログの [ユーザー] の一覧から **[B.Simon]** を選択し、画面の下部にある **[選択]** ボタンをクリックします。
1. SAML アサーション内に任意のロール値が必要な場合、 **[ロールの選択]** ダイアログでユーザーに適したロールを一覧から選択し、画面の下部にある **[選択]** をクリックします。
1. **[割り当ての追加]** ダイアログで、 **[割り当て]** をクリックします。

## <a name="configure-opsgenie-sso"></a>OpsGenie の SSO の構成

1. 別のブラウザー インスタンスを開き、管理者として OpsGenie にサインインします。

2. **[設定]** をクリックし、 **[シングル サインオン]** タブをクリックします。
   
    ![OpsGenie シングル サインオン](./media/opsgenie-tutorial/tutorial-opsgenie-06.png)

3. SSO を有効にするには、 **[有効]** を選択します。
   
    ![OpsGenie 設定](./media/opsgenie-tutorial/tutorial-opsgenie-07.png) 

4. **[プロバイダー]** セクションで、 **[Azure Active Directory]** タブをクリックします。
   
    ![OpsGenie 設定](./media/opsgenie-tutorial/tutorial-opsgenie-08.png) 

5. [Azure Active Directory] ダイアログ ページで、次の手順に従います。
   
    ![OpsGenie 設定](./media/opsgenie-tutorial/tutorial-opsgenie-09.png)
    
    a. **[App ID URI]\(アプリケーション ID/URI\)** の値をコピーして、Azure portal の **[基本的な SAML 構成]** セクションにある **[識別子 (エンティティ ID)]** ボックスに貼り付けます。

    a. **[Reply URL]\(応答 URL\)** の値をコピーして、Azure portal の **[基本的な SAML 構成]** セクションの **[応答 URL]** ボックスに貼り付けます。

    a. **[SAML 2.0 Endpoint]\(SAML 2.0 エンドポイント\)** ボックスに、Azure portal からコピーした**ログイン URL** の値を貼り付けます。
    
    b. **[メタデータ URL]** テキスト ボックスに、Azure Portal からコピーした **[アプリのフェデレーション メタデータ URL]** 値を貼り付けます。
    
    c. SSO を有効にするには、 **[シングル サインオンを有効にする]** をオンにします。

    d. **[Apply SSO settings]\(SSO 設定の適用\)** をクリックします。

### <a name="create-opsgenie-test-user"></a>OpsGenie テスト ユーザーの作成

このセクションの目的は、OpsGenie で B. Simon というユーザーを作成することです。 

1. Web ブラウザー ウィンドウで、OpsGenie テナントに管理者としてサインインします。

2. 左パネルの **[ユーザー]** をクリックし、ユーザー リストに移動します。
   
    ![OpsGenie 設定](./media/opsgenie-tutorial/tutorial-opsgenie-10.png) 

3. **[ユーザーの追加]** をクリックします。

4. **[Add User]** ダイアログで、次の手順を実行します。
   
    ![OpsGenie 設定](./media/opsgenie-tutorial/tutorial-opsgenie-11.png)
   
    a. **[電子メール]** ボックスに、Azure Active Directory の B.Simon のメール アドレスを入力します。
   
    b. **[フル ネーム]** ボックスに、「**B.Simon**」と入力します。
   
    c. **[保存]** をクリックします。 

> [!NOTE]
> B.Simon にプロファイルの設定方法が記載されたメールが届きます。

## <a name="test-sso"></a>SSO のテスト 

このセクションでは、アクセス パネルを使用して Azure AD のシングル サインオン構成をテストします。

アクセス パネル上で [OpsGenie] タイルをクリックすると、SSO を設定した OpsGenie に自動的にサインインします。 アクセス パネルの詳細については、[アクセス パネルの概要](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)に関する記事を参照してください。

## <a name="additional-resources"></a>その他のリソース

- [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Azure Active Directory でのアプリケーション アクセスとシングル サインオンとは](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on)

- [Azure Active Directory の条件付きアクセスとは](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Azure AD で OpsGenie を試す](https://aad.portal.azure.com/)

- [Microsoft Cloud App Security におけるセッション制御とは](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)