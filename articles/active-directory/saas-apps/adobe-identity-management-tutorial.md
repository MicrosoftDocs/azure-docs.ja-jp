---
title: チュートリアル:Azure Active Directory シングル サインオン (SSO) と Adobe Identity Management の統合 | Microsoft Docs
description: Azure Active Directory と Adobe Identity Management の間でシングル サインオンを構成する方法について説明します。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/15/2021
ms.author: jeedes
ms.openlocfilehash: fdca04c645e1bb956c8e9f294c702b639c8e2f74
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "98726403"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-adobe-identity-management"></a>チュートリアル:Azure Active Directory シングル サインオン (SSO) と Adobe Identity Management の統合

このチュートリアルでは、Adobe Identity Management と Azure Active Directory (Azure AD) を統合する方法について説明します。 Adobe Identity Management と Azure AD を統合すると、次のことができます。

* Adobe Identity Management にアクセス権を持つユーザーを Azure AD で管理できます。
* ユーザーが自分の Azure AD アカウントで Adobe Identity Management に自動的にサインインできるようにすることができます。
* 1 つの中央サイト (Azure Portal) で自分のアカウントを管理します。

## <a name="prerequisites"></a>前提条件

開始するには、次が必要です。

* Azure AD サブスクリプション。 サブスクリプションがない場合は、[無料アカウント](https://azure.microsoft.com/free/)を取得できます。
* Adobe Identity Management のシングル サインオン (SSO) が有効なサブスクリプション。

## <a name="scenario-description"></a>シナリオの説明

このチュートリアルでは、テスト環境で Azure AD の SSO を構成してテストします。

* Adobe Identity Management は、**SP** によって開始される SSO がサポートされます。

## <a name="adding-adobe-identity-management-from-the-gallery"></a>ギャラリーから Adobe Identity Management を追加する

Adobe Identity Management の Azure AD への統合を構成するには、マネージド SaaS アプリの一覧にギャラリーから Adobe Identity Management を追加する必要があります。

1. 職場または学校アカウントか、個人の Microsoft アカウントを使用して、Azure portal にサインインします。
1. 左のナビゲーション ウィンドウで **[Azure Active Directory]** サービスを選択します。
1. **[エンタープライズ アプリケーション]** に移動し、 **[すべてのアプリケーション]** を選択します。
1. 新しいアプリケーションを追加するには、 **[新しいアプリケーション]** を選択します。
1. **[ギャラリーから追加する]** セクションで、検索ボックスに「**Adobe Identity Management**」と入力します。
1. 結果パネルから **[Adobe Identity Management]** を選択し、アプリを追加します。 お使いのテナントにアプリが追加されるのを数秒待機します。

## <a name="configure-and-test-azure-ad-sso-for-adobe-identity-management"></a>Adobe Identity Management の Azure AD SSO の構成とテスト

**B.Simon** というテスト ユーザーを使用して、Adobe Identity Management に対する Azure AD SSO を構成してテストします。 SSO を機能させるためには、Azure AD ユーザーと Adobe Identity Management の関連ユーザーとの間にリンク関係を確立する必要があります。

Adobe Identity Management に対する Azure AD SSO を構成してテストするには、次の手順を実行します。

1. **[Azure AD SSO の構成](#configure-azure-ad-sso)** - ユーザーがこの機能を使用できるようにします。
    1. **[Azure AD のテスト ユーザーの作成](#create-an-azure-ad-test-user)** - B.Simon で Azure AD のシングル サインオンをテストします。
    1. **[Azure AD テスト ユーザーの割り当て](#assign-the-azure-ad-test-user)** - B.Simon が Azure AD シングル サインオンを使用できるようにします。
1. **[Adobe Identity Management SSO の構成](#configure-adobe-identity-management-sso)** - アプリケーション側でシングル サインオン設定を構成します。
    1. **[Adobe Identity Management のテスト ユーザーの作成](#create-adobe-identity-management-test-user)** - Adobe Identity Management で B.Simon に対応するユーザーを作成し、Azure AD の B.Simon にリンクさせます。
1. **[SSO のテスト](#test-sso)** - 構成が機能するかどうかを確認します。

## <a name="configure-azure-ad-sso"></a>Azure AD SSO の構成

これらの手順に従って、Azure portal で Azure AD SSO を有効にします。

1. Azure portal の **Adobe Identity Management** アプリケーション統合ページで、 **[管理]** セクションを見つけて、 **[シングル サインオン]** を選択します。
1. **[シングル サインオン方式の選択]** ページで、 **[SAML]** を選択します。
1. **[SAML によるシングル サインオンのセットアップ]** ページで、 **[基本的な SAML 構成]** の鉛筆アイコンをクリックして設定を編集します。

   ![基本的な SAML 構成を編集する](common/edit-urls.png)

1. **[基本的な SAML 構成]** セクションで、次のフィールドの値を入力します。

    a. **[サインオン URL]** ボックスに、URL として「`https://adobe.com`」と入力します。

    b. **[識別子 (エンティティ ID)]** ボックスに、次のパターンを使用して URL を入力します。`https://federatedid-na1.services.adobe.com/federated/saml/metadata/alias/<CUSTOM_ID>`

    > [!NOTE]
    > この識別子の値は実際のものではありません。 この値を実際の識別子で更新してください。 この値を取得するには、[Adobe Identity Management クライアント サポート チーム](mailto:identity@adobe.com)に問い合わせてください。 Azure portal の **[基本的な SAML 構成]** セクションに示されているパターンを参照することもできます。

1. **[SAML でシングル サインオンをセットアップします]** ページの **[SAML 署名証明書]** セクションで、 **[フェデレーション メタデータ XML]** を探して **[ダウンロード]** を選択し、証明書をダウンロードして、お使いのコンピューターに保存します。

    ![証明書のダウンロードのリンク](common/metadataxml.png)

1. **[Set up Adobe Identity Management]\(Adobe Identity Management の設定\)** セクションで、要件に基づいて適切な URL をコピーします。

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

このセクションでは、B.Simon に Adobe Identity Management へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

1. Azure portal で **[エンタープライズ アプリケーション]** を選択し、 **[すべてのアプリケーション]** を選択します。
1. アプリケーションの一覧で **[Adobe Identity Management]** を選択します。
1. アプリの概要ページで、 **[管理]** セクションを見つけて、 **[ユーザーとグループ]** を選択します。
1. **[ユーザーの追加]** を選択し、 **[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。
1. **[ユーザーとグループ]** ダイアログの [ユーザー] の一覧から **[B.Simon]** を選択し、画面の下部にある **[選択]** ボタンをクリックします。
1. ユーザーにロールが割り当てられることが想定される場合は、 **[ロールの選択]** ドロップダウンからそれを選択できます。 このアプリに対してロールが設定されていない場合は、[既定のアクセス] ロールが選択されていることを確認します。
1. **[割り当ての追加]** ダイアログで、 **[割り当て]** をクリックします。

## <a name="configure-adobe-identity-management-sso"></a>Adobe Identity Management SSO の構成

1. Adobe Identity Management 内での構成を自動化するには、 **[拡張機能のインストール]** をクリックして、**My Apps Secure Sign-in ブラウザー拡張機能** をインストールする必要があります。

    ![マイ アプリの拡張機能](common/install-myappssecure-extension.png)

2. ブラウザーに拡張機能を追加した後、 **[Adobe Identity Management のセットアップ]** をクリックすると、Adobe Identity Management アプリケーションに移動します。 そこから、管理者の資格情報を入力して Adobe Identity Management にサインインします。 ブラウザー拡張機能によりアプリケーションが自動的に構成され、手順 3 ～ 8 が自動化されます。

    ![セットアップの構成](common/setup-sso.png)

3. Adobe Identity Management を手動でセットアップする場合は、別の Web ブラウザー ウィンドウで、Adobe Identity Management 企業サイトに管理者としてサインインします。

4. **[Settings]\(設定\)** タブに移動し、 **[Create Directory]\(ディレクトリの作成\)** をクリックします。

    ![Adobe Identity Management の設定](./media/adobe-identity-management-tutorial/settings.png)

5. テキスト ボックスにディレクトリ名を入力し、 **[Federated ID]\(フェデレーション ID\)** を選択して、 **[Next]\(次へ\)** をクリックします。

    ![Adobe Identity Management のディレクトリの作成](./media/adobe-identity-management-tutorial/create-directory.png)

6. **[Other SAML Providers]\(他の SAML プロバイダー\)** を選択し、 **[Next]\(次へ\)** をクリックします。
 
    ![Adobe Identity Management の SAML プロバイダー](./media/adobe-identity-management-tutorial/saml-providers.png)

7. **[select]\(選択\)** をクリックし、Azure portal からダウンロードした **メタデータ XML** ファイルを選択してアップロードします。

    ![Adobe Identity Management の SAML 構成](./media/adobe-identity-management-tutorial/saml-configuration.png)

8. **[Done]\(終了)** をクリックします。

### <a name="create-adobe-identity-management-test-user"></a>Adobe Identity Management テスト ユーザーの作成

1. **[Users]\(ユーザー\)** タブに移動し、 **[Add User]\(ユーザーの追加\)** をクリックします。

    ![Adobe Identity Management のユーザーの追加](./media/adobe-identity-management-tutorial/add-user.png)

2. **[Enter user’s email address]\(ユーザーのメール アドレスを入力してください)** ボックスに、**メール アドレス** を入力します。

    ![Adobe Identity Management のユーザーの保存](./media/adobe-identity-management-tutorial/save-user.png)

3. **[保存]** をクリックします。

## <a name="test-sso"></a>SSO のテスト

このセクションでは、次のオプションを使用して Azure AD のシングル サインオン構成をテストします。

* Azure portal で **[このアプリケーションをテストします]** をクリックします。 これにより、ログイン フローを開始できる Adobe Identity Management のサインオン URL にリダイレクトされます。

* Adobe Identity Management のサインオン URL に直接移動し、そこからログイン フローを開始します。

* Microsoft マイ アプリを使用することができます。 マイ アプリで [Adobe Identity Management] タイルをクリックすると、Adobe Identity Management のサインオン URL にリダイレクトされます。 マイ アプリの詳細については、[マイ アプリの概要](../user-help/my-apps-portal-end-user-access.md)に関するページを参照してください。

## <a name="next-steps"></a>次のステップ

Adobe Identity Management を構成したら、組織の機密データを流出と侵入からリアルタイムで保護するセッション制御を適用できます。 セッション制御は、条件付きアクセスを拡張したものです。 [Microsoft Cloud App Security でセッション制御を強制する方法](/cloud-app-security/proxy-deployment-any-app)をご覧ください。