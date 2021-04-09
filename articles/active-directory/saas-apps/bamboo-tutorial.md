---
title: 'チュートリアル: Azure Active Directory と SAML SSO for Bamboo by resolution GmbH の統合 | Microsoft Docs'
description: Azure Active Directory と SAML SSO for Bamboo by resolution GmbH の間のシングル サインオンを構成する方法について説明します。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/12/2021
ms.author: jeedes
ms.openlocfilehash: 8ec834d76692f78d1d7bc60ddbd4c73fe4adaede
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "101646111"
---
# <a name="tutorial-azure-active-directory-integration-with-saml-sso-for-bamboo-by-resolution-gmbh"></a>チュートリアル: Azure Active Directory と SAML SSO for Bamboo by resolution GmbH の統合

このチュートリアルでは、SAML SSO for Bamboo by resolution GmbH と Azure Active Directory (Azure AD) を統合する方法について説明します。 Azure AD と SAML SSO for Bamboo by resolution GmbH を統合すると、次のことができます。

* SAML SSO for Bamboo by resolution GmbH にアクセスするユーザーを Azure AD で制御する。
* ユーザーが自分の Azure AD アカウントで SAML SSO for Bamboo by resolution GmbH に自動的にサインインするように設定する。
* 1 つの中央サイト (Azure Portal) でアカウントを管理できます。

## <a name="prerequisites"></a>前提条件

Azure AD と SAML SSO for Bamboo by resolution GmbH の統合を構成するには、次のものが必要です。

* Azure AD サブスクリプション。 Azure AD の環境がない場合は、[無料アカウント](https://azure.microsoft.com/free/)を取得できます
* SAML SSO for Bamboo by resolution GmbH でのシングル サインオンが有効なサブスクリプション

## <a name="scenario-description"></a>シナリオの説明

このチュートリアルでは、テスト環境で Azure AD のシングル サインオンを構成してテストします。

* SAML SSO for Bamboo by resolution GmbH では、**SP と IDP** によって開始される SSO がサポートされます
* SAML SSO for Bamboo by resolution GmbH では、**Just In Time** ユーザー プロビジョニングがサポートされます

## <a name="add-saml-sso-for-bamboo-by-resolution-gmbh-from-the-gallery"></a>ギャラリーからの SAML SSO for Bamboo by resolution GmbH の追加

Azure AD への SAML SSO for Bamboo by resolution GmbH の統合を構成するには、ギャラリーから管理対象 SaaS アプリの一覧に SAML SSO for Bamboo by resolution GmbH を追加する必要があります。

1. 職場または学校アカウントか、個人の Microsoft アカウントを使用して、Azure portal にサインインします。
1. 左のナビゲーション ウィンドウで **[Azure Active Directory]** サービスを選択します。
1. **[エンタープライズ アプリケーション]** に移動し、 **[すべてのアプリケーション]** を選択します。
1. 新しいアプリケーションを追加するには、 **[新しいアプリケーション]** を選択します。
1. **[ギャラリーから追加する]** セクションで、検索ボックスに「**SAML SSO for Bamboo by resolution GmbH**」と入力します。
1. 結果パネルから **[SAML SSO for Bamboo by resolution GmbH]** を選択し、アプリを追加します。 お使いのテナントにアプリが追加されるのを数秒待機します。

## <a name="configure-and-test-azure-ad-sso-with-saml-sso-for-bamboo-by-resolution-gmbh"></a>SAML SSO for Bamboo by resolution GmbH の Azure AD SSO の構成とテスト

**B.Simon** というテスト ユーザーを使用して、SAML SSO for Bamboo by resolution GmbH に対する Azure AD SSO を構成してテストします。 SSO を機能させるためには、Azure AD ユーザーと SAML SSO for Bamboo by resolution GmbH の関連ユーザーとの間にリンクされた関係を確立する必要があります。

SAML SSO for Bamboo by resolution GmbH で Azure AD SSO を構成してテストするには、次の手順に従います。

1. **[Azure AD SSO の構成](#configure-azure-ad-sso)** - ユーザーがこの機能を使用できるようにします。
     1. **[Azure AD のテスト ユーザーの作成](#create-an-azure-ad-test-user)** - Britta Simon で Azure AD のシングル サインオンをテストします。
     1. **[Azure AD テスト ユーザーの割り当て](#assign-the-azure-ad-test-user)** - Britta Simon が Azure AD シングル サインオンを使用できるようにします。
2. **[SAML SSO for Bamboo by resolution GmbH の SSO の構成](#configure-saml-sso-for-bamboo-by-resolution-gmbh-sso)** - アプリケーション側でシングル サインオン設定を構成します。
    1. **[SAML SSO for Bamboo by resolution GmbH のテスト ユーザーの作成](#create-saml-sso-for-bamboo-by-resolution-gmbh-test-user)** - SAML SSO for Bamboo by resolution GmbH で Britta Simon に対応するユーザーを作成し、Azure AD の Britta Simon にリンクさせます。
6. **[SSO のテスト](#test-sso)** - 構成が機能するかどうかを確認します。

### <a name="configure-azure-ad-sso"></a>Azure AD SSO の構成

このセクションでは、Azure portal で Azure AD の SSO を有効にします。
 
1. Azure portal の **SAML SSO for Bamboo by resolution GmbH** アプリケーション統合ページで、 **[管理]** セクションを見つけ、 **[シングル サインオン]** を選択します。
1. **[シングル サインオン方式の選択]** ページで、 **[SAML]** を選択します。
1. **[SAML によるシングル サインオンのセットアップ]** ページで、 **[基本的な SAML 構成]** の鉛筆アイコンを選択して設定を編集します。

    ![基本的な SAML 構成を編集する](common/edit-urls.png)
4. **[基本的な SAML 構成]** セクションで、アプリケーションを **IDP** 開始モードで構成する場合は、次の手順を実行します。

    a. **[識別子]** ボックスに、`https://<server-base-url>/plugins/servlet/samlsso` の形式で URL を入力します。

    b. **[応答 URL]** ボックスに、`https://<server-base-url>/plugins/servlet/samlsso` のパターンを使用して URL を入力します

5. アプリケーションを **SP** 開始モードで構成する場合は、 **[追加の URL を設定します]** をクリックして次の手順を実行します。

     **[サインオン URL]** ボックスに、`https://<server-base-url>/plugins/servlet/samlsso` という形式で URL を入力します。

    > [!NOTE]
    > これらは実際の値ではありません。 実際の識別子、応答 URL、サインオン URL でこれらの値を更新します。 これらの値を取得するには、[SAML SSO for Bamboo by resolution GmbH クライアント サポート チーム](https://marketplace.atlassian.com/plugins/com.resolution.atlasplugins.samlsso-bamboo/server/support)に問い合わせます。 Azure portal の **[基本的な SAML 構成]** セクションに示されているパターンを参照することもできます。

6. **[SAML でシングル サインオンをセットアップします]** ページの **[SAML 署名証明書]** セクションで、 **[ダウンロード]** をクリックして、要件のとおりに指定したオプションから **フェデレーション メタデータ XML** をダウンロードして、お使いのコンピューターに保存します。

    ![証明書のダウンロードのリンク](common/metadataxml.png)

7. **[SAML SSO for Bamboo by resolution GmbH のセットアップ]** セクションで、要件に従って適切な URL をコピーします。

    ![構成 URL のコピー](common/copy-configuration-urls.png)


### <a name="create-an-azure-ad-test-user"></a>Azure AD のテスト ユーザーの作成

このセクションでは、Azure portal 内で B.Simon というテスト ユーザーを作成します。

1. Azure portal の左側のウィンドウで、 **[Azure Active Directory]**  >  **[ユーザー]**  >  **[すべてのユーザー]** を選択します。
1. 画面の上部にある **[新しいユーザー]** を選択します。
1. **[ユーザー]** プロパティで、以下の手順を実行します。
   1. **[名前]** フィールドに「`B.Simon`」と入力します。  
   1. **[ユーザー名]** フィールドに「username@companydomain.extension」と入力します。 たとえば、「 `B.Simon@contoso.com` 」のように入力します。
   1. **[Show password]\(パスワードの表示\)** チェック ボックスをオンにし、パスワードを書き留めます。
   1. **［作成］** を選択します

### <a name="assign-the-azure-ad-test-user"></a>Azure AD テスト ユーザーの割り当て

このセクションでは、B.Simon に SAML SSO for bamboo by resolution GmbH へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

1. Azure portal で、 **[エンタープライズ アプリケーション]**  >  **[すべてのアプリケーション]** の順に選択します。
1. アプリケーションの一覧で **[SAML SSO for bamboo by resolution GmbH]** を選択します。
1. アプリの概要ページで、 **[管理]** セクションを見つけて、 **[ユーザーとグループ]** を選択します。
1. **[ユーザーの追加]** を選択します。 次に、 **[割り当ての追加]** ダイアログ ボックスで **[ユーザーとグループ]** を選択します。
1. **[ユーザーとグループ]** ダイアログ ボックスで、ユーザーの一覧から **[B.Simon]** を選択します。 次に、画面の下部にある **[選択]** を選択します。
1. ユーザーにロールが割り当てられることが想定される場合は、 **[ロールの選択]** ドロップダウンからそれを選択できます。 このアプリに対してロールが設定されていない場合は、[既定のアクセス] ロールが選択されていることを確認します。
1. **[割り当ての追加]** ダイアログ ボックスで **[割り当て]** を選びます。

### <a name="configure-saml-sso-for-bamboo-by-resolution-gmbh-sso"></a>SAML SSO for Bamboo by resolution GmbH の SSO の構成

1. SAML SSO for Bamboo by resolution GmbH 企業サイトに管理者としてサインオンします。

1. メイン ツールバーの右側で、 **[Settings]\(設定\)**  >  **[Add-ons]\(アドオン\)** の順にクリックします。

    ![設定](./media/bamboo-tutorial/tutorial_bamboo_setings.png)

1. [SECURITY]\(セキュリティ\) セクションに移動し、メニュー バーの **[SAML SingleSignOn]** をクリックします。

    ![Samlsingle](./media/bamboo-tutorial/tutorial_bamboo_samlsingle.png)

1. **[SAML SingleSignOn Plugin Configuration]\(SAML SingleSignOn プラグインの構成\)** ページで、 **[Add idp]\(IDP の追加\)** をクリックします。

    ![IDP の追加](./media/bamboo-tutorial/tutorial_bamboo_addidp.png)

1. **[Choose your SAML Identity Provider]\(SAML ID プロバイダーの選択\)** ページで、次の手順を実行します。

    ![ID プロバイダー](./media/bamboo-tutorial/tutorial_bamboo_identityprovider.png)

    a. **[Idp Type]\(IDP の種類\)** として **[AZURE AD]** を選択します。

    b. **[Name]\(名前\)** ボックスに、名前を入力します。

    c. **[Description]\(説明\)** ボックスに、説明を入力します。

    d. **[次へ]** をクリックします。

1. **[Identity provider configuration]\(ID プロバイダーの構成\)** ページで、 **[Next]\(次へ\)** をクリックします。

    ![ID の構成](./media/bamboo-tutorial/tutorial_bamboo_identityconfig.png)

1. **[Import SAML IdP Metadata]\(SAML IDP メタデータのインポート\)** ページで、 **[ファイルの読み込み]** をクリックして、Azure Portal からダウンロードした **メタデータ XML** ファイルをアップロードします。

    ![IDP メタデータ](./media/bamboo-tutorial/tutorial_bamboo_idpmetadata.png)

1. **[次へ]** をクリックします。

1. **[Save settings]\(設定の保存\)** をクリックします。

### <a name="create-saml-sso-for-bamboo-by-resolution-gmbh-test-user"></a>SAML SSO for Bamboo by resolution GmbH のテスト ユーザーの作成

このセクションの目的は、SAML SSO for Bamboo by resolution GmbH で Britta Simon というユーザーを作成することです。 SAML SSO for Bamboo by resolution GmbH では、Just-In-Time プロビジョニングがサポートされていますが、ユーザーを手動で作成することもできます。要件に応じて [SAML SSO for Bamboo by resolution GmbH クライアント サポート チーム](https://marketplace.atlassian.com/plugins/com.resolution.atlasplugins.samlsso-bamboo/server/support)にお問い合わせください。

### <a name="test-sso"></a>SSO のテスト

このセクションでは、次のオプションを使用して Azure AD のシングル サインオン構成をテストします。 

#### <a name="sp-initiated"></a>SP Initiated:

* Azure portal で **[このアプリケーションをテストします]** をクリックします。 これにより、ログイン フローを開始できる SAML SSO for Bamboo by resolution GmbH のサインオン URL にリダイレクトされます。  

* SAML SSO for Bamboo by resolution GmbH のサインオン URL に直接移動し、そこからログイン フローを開始します。

#### <a name="idp-initiated"></a>IDP Initiated:

* Azure portal で **[このアプリケーションをテストします]** をクリックすると、SSO を設定した SAML SSO for Bamboo by resolution GmbH に自動的にサインインされます。

また、Microsoft マイ アプリを使用して、任意のモードでアプリケーションをテストすることもできます。 マイ アプリで [SAML SSO for Bamboo by resolution GmbH] タイルをクリックすると、SP モードで構成されている場合は、ログイン フローを開始するためのアプリケーション サインオン ページにリダイレクトされます。IDP モードで構成されている場合は、SSO を設定した SAML SSO for Bamboo by resolution GmbH に自動的にサインインされます。 マイ アプリの詳細については、[マイ アプリの概要](../user-help/my-apps-portal-end-user-access.md)に関するページを参照してください。

## <a name="next-steps"></a>次のステップ

SAML SSO for Bamboo by resolution GmbH を構成したら、組織の機密データを流出と侵入からリアルタイムで保護するセッション制御を適用できます。 セッション制御は、条件付きアクセスを拡張したものです。 [Microsoft Cloud App Security でセッション制御を強制する方法](/cloud-app-security/proxy-deployment-any-app)をご覧ください。