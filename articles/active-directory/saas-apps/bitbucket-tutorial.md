---
title: 'チュートリアル: Azure Active Directory と SAML SSO for Bitbucket by resolution GmbH の統合 | Microsoft Docs'
description: Azure Active Directory と SAML SSO for Bitbucket by resolution GmbH の間でシングル サインオンを構成する方法について説明します。
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
ms.openlocfilehash: aa5e3e88ceb957728799f27482de7477ba6b7b48
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "98621249"
---
# <a name="tutorial-azure-active-directory-integration-with-saml-sso-for-bitbucket-by-resolution-gmbh"></a>チュートリアル: Azure Active Directory と SAML SSO for Bitbucket by resolution GmbH の統合

このチュートリアルでは、SAML SSO for Bitbucket by resolution GmbH と Azure Active Directory (Azure AD) を統合する方法について説明します。 Azure AD と SAML SSO for Bitbucket by resolution GmbH を統合すると、次のことができます。

* Azure AD で、SAML SSO for Bitbucket by resolution GmbH にアクセスするユーザーを制御する。
* ユーザーが自分の Azure AD アカウントで SAML SSO for Bitbucket by resolution GmbH に自動的にサインインするように設定する。
* 1 つの中央サイト (Azure Portal) でアカウントを管理できます。


## <a name="prerequisites"></a>前提条件

Azure AD と SAML SSO for Bitbucket by resolution GmbH の統合を構成するには、次のものが必要です。

* Azure AD サブスクリプション。 Azure AD の環境がない場合は、[こちら](https://azure.microsoft.com/pricing/free-trial/)から 1 か月の評価版を入手できます
* SAML SSO for Bitbucket by resolution GmbH でのシングル サインオンが有効なサブスクリプション

## <a name="scenario-description"></a>シナリオの説明

このチュートリアルでは、テスト環境で Azure AD のシングル サインオンを構成してテストします。

* SAML SSO for Bitbucket by resolution GmbH では、**SP と IDP** によって開始される SSO がサポートされます
* SAML SSO for Bitbucket by resolution GmbH では、**Just In Time** ユーザー プロビジョニングがサポートされます


## <a name="add-saml-sso-for-bitbucket-by-resolution-gmbh-from-the-gallery"></a>ギャラリーからの SAML SSO for Bitbucket by resolution GmbH の追加

Azure AD への SAML SSO for Bitbucket by resolution GmbH の統合を構成するには、ギャラリーから管理対象 SaaS アプリの一覧に SAML SSO for Bitbucket by resolution GmbH を追加する必要があります。

1. 職場または学校アカウントか、個人の Microsoft アカウントを使用して、Azure portal にサインインします。
1. 左側のペインで、 **[Azure Active Directory]** を選択します。
1. **[エンタープライズ アプリケーション]** に移動し、 **[すべてのアプリケーション]** を選択します。
1. 新しいアプリケーションを追加するには、 **[新しいアプリケーション]** を選択します。
1. **[ギャラリーから追加する]** セクションで、検索ボックスに「**SAML SSO for Bitbucket by resolution GmbH**」と入力します。
1. 結果から **[SAML SSO for Bitbucket by resolution GmbH]** を選択し、アプリを追加します。 お使いのテナントにアプリが追加されるのを数秒待機します。

## <a name="configure-and-test-azure-ad-sso-for-saml-sso-for-bitbucket-by-resolution-gmbh"></a>SAML SSO for Bitbucket by resolution GmbH の Azure AD SSO の構成とテスト

**B.Simon** というテスト ユーザーを使用して、SAML SSO for Bitbucket by resolution GmbH に対する Azure AD SSO を構成してテストします。 SSO を機能させるためには、Azure AD ユーザーと SAML SSO for Bitbucket by resolution GmbH の関連ユーザーとの間にリンクされた関係を確立する必要があります。

SAML SSO for Bitbucket by resolution GmbH で Azure AD SSO を構成してテストするには、次の手順に従います。


1. **[Azure AD SSO の構成](#configure-azure-ad-sso)** - ユーザーがこの機能を使用できるようにします。
    1. **[Azure AD のテスト ユーザーの作成](#create-an-azure-ad-test-user)** - Britta Simon で Azure AD のシングル サインオンをテストします。
    1. **[Azure AD テスト ユーザーの割り当て](#assign-the-azure-ad-test-user)** - Britta Simon が Azure AD シングル サインオンを使用できるようにします。
2. **[SAML SSO for Bitbucket by resolution GmbH の SSO の構成](#configure-saml-sso-for-bitbucket-by-resolution-gmbh-sso)** - アプリケーション側でシングル サインオン設定を構成します。
    1. **[SAML SSO for Bitbucket by resolution GmbH テスト ユーザーの作成](#create-saml-sso-for-bitbucket-by-resolution-gmbh-test-user)** - SAML SSO for Bitbucket by resolution GmbH で Britta Simon に対応するユーザーを作成し、Azure AD の Britta Simon にリンクさせます。
6. **[SSO のテスト](#test-sso)** - 構成が機能するかどうかを確認します。

## <a name="configure-azure-ad-sso"></a>Azure AD SSO の構成

このセクションでは、Azure portal で Azure AD の SSO を有効にします。
 
1. Azure portal の **SAML SSO for Bitbucket by resolution GmbH** アプリケーション統合ページで、 **[管理]** セクションを見つけ、 **[シングル サインオン]** を選択します。
1. **[シングル サインオン方式の選択]** ページで、 **[SAML]** を選択します。
1. **[SAML によるシングル サインオンのセットアップ]** ページで、 **[基本的な SAML 構成]** の鉛筆アイコンを選択して設定を編集します。

    ![基本的な SAML 構成を編集する](common/edit-urls.png)

4. **[基本的な SAML 構成]** セクションで、**IDP** 開始モードでアプリケーションを構成する場合は、次の手順に従います。


    a. **[識別子]** ボックスに、`https://<server-base-url>/plugins/servlet/samlsso` の形式で URL を入力します。

    b. **[応答 URL]** ボックスに、`https://<server-base-url>/plugins/servlet/samlsso` のパターンを使用して URL を入力します

    c. アプリケーションを **SP** 開始モードで構成する場合は、 **[追加の URL を設定します]** をクリックして次の手順を実行します。
    
    **[サインオン URL]** ボックスに、`https://<server-base-url>/plugins/servlet/samlsso` という形式で URL を入力します。

    > [!NOTE]
    > これらは実際の値ではありません。 実際の識別子、応答 URL、サインオン URL でこれらの値を更新します。 これらの値を取得するには、[SAML SSO for Bitbucket by resolution GmbH クライアント サポート チーム](https://marketplace.atlassian.com/apps/1217045/saml-single-sign-on-sso-bitbucket?hosting=server&tab=support)に問い合わせます。 Azure portal の **[基本的な SAML 構成]** セクションに示されているパターンを参照することもできます。

5. **[SAML でシングル サインオンをセットアップします]** ページの **[SAML 署名証明書]** セクションで、 **[ダウンロード]** をクリックして、要件のとおりに指定したオプションから **フェデレーション メタデータ XML** をダウンロードして、お使いのコンピューターに保存します。

    ![証明書のダウンロードのリンク](common/metadataxml.png)

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

このセクションでは、B.Simon に SAML SSO for Bitbucket by resolution GmbH へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

1. Azure portal で、 **[エンタープライズ アプリケーション]**  >  **[すべてのアプリケーション]** の順に選択します。
1. アプリケーションの一覧で **[SAML SSO for Bitbucket by resolution GmbH]** を選択します。
1. アプリの概要ページで、 **[管理]** セクションを見つけて、 **[ユーザーとグループ]** を選択します。
1. **[ユーザーの追加]** を選択します。 次に、 **[割り当ての追加]** ダイアログ ボックスで **[ユーザーとグループ]** を選択します。
1. **[ユーザーとグループ]** ダイアログ ボックスで、ユーザーの一覧から **[B.Simon]** を選択します。 次に、画面の下部にある **[選択]** を選択します。
1. ユーザーにロールが割り当てられることが想定される場合は、 **[ロールの選択]** ドロップダウンからそれを選択できます。 このアプリに対してロールが設定されていない場合は、[既定のアクセス] ロールが選択されていることを確認します。
1. **[割り当ての追加]** ダイアログ ボックスで **[割り当て]** を選びます。

## <a name="configure-saml-sso-for-bitbucket-by-resolution-gmbh-sso"></a>SAML SSO for Bitbucket by resolution GmbH の SSO の構成

1. SAML SSO for Bitbucket by resolution GmbH 企業サイトに管理者としてサインオンします。

2. メイン ツールバーの右側で、 **[設定]** をクリックします。

3. [ACCOUNTS]\(アカウント\) セクションに移動し、メニュー バーの **[SAML SingleSignOn]** をクリックします。

    ![Samlsingle](./media/bitbucket-tutorial/tutorial_bitbucket_samlsingle.png)

4. **[SAML SingleSignOn Plugin Configuration]\(SAML SingleSignOn プラグインの構成\)** ページで、 **[Add idp]\(IDP の追加\)** をクリックします。 

    ![IDP の追加](./media/bitbucket-tutorial/tutorial_bitbucket_addidp.png)

5. **[Choose your SAML Identity Provider]\(SAML ID プロバイダーの選択\)** ページで、次の手順を実行します。

    ![ID プロバイダー](./media/bitbucket-tutorial/tutorial_bitbucket_identityprovider.png)

    a. **[Idp Type]\(IDP の種類\)** として **[AZURE AD]** を選択します。

    b. **[Name]\(名前\)** ボックスに、名前を入力します。

    c. **[Description]\(説明\)** ボックスに、説明を入力します。

    d. **[次へ]** をクリックします。

6. **[Identity provider configuration]\(ID プロバイダーの構成\)** ページで、 **[次へ]** をクリックします。

    ![ID の構成](./media/bitbucket-tutorial/tutorial_bitbucket_identityconfig.png)

7.  **[Import SAML IdP Metadata]\(SAML IDP メタデータのインポート\)** ページで、 **[ファイルの読み込み]** をクリックして、Azure Portal からダウンロードした **メタデータ XML** ファイルをアップロードします。

    ![IDP メタデータ](./media/bitbucket-tutorial/tutorial_bitbucket_idpmetadata.png)

8. **[次へ]** をクリックします。

9. **[Save settings]\(設定の保存\)** をクリックします。

    ![保存](./media/bitbucket-tutorial/tutorial_bitbucket_save.png)


## <a name="create-saml-sso-for-bitbucket-by-resolution-gmbh-test-user"></a>SAML SSO for Bitbucket by resolution GmbH のテスト ユーザーの作成

このセクションの目的は、SAML SSO for Bitbucket by resolution GmbH で Britta Simon というユーザーを作成することです。 SAML SSO for Bitbucket by resolution GmbH では、Just-In-Time プロビジョニングがサポートされており、ユーザーを手動で作成することもできます。必要に応じて [SAML SSO for Bitbucket by resolution GmbH クライアント サポート チーム](https://marketplace.atlassian.com/plugins/com.resolution.atlasplugins.samlsso-bitbucket/server/support)にお問い合わせください。

## <a name="test-sso"></a>SSO のテスト 

このセクションでは、次のオプションを使用して Azure AD のシングル サインオン構成をテストします。 

#### <a name="sp-initiated"></a>SP Initiated:

* Azure portal で **[このアプリケーションをテストします]** をクリックします。 これにより、ログイン フローを開始できる SAML SSO for Bitbucket by resolution GmbH のサインオン URL にリダイレクトされます。  

* SAML SSO for Bitbucket by resolution GmbH のサインオン URL に直接移動し、そこからログイン フローを開始します。

#### <a name="idp-initiated"></a>IDP Initiated:

* Azure portal で **[このアプリケーションをテストします]** をクリックすると、SSO を設定した SAML SSO for Bitbucket by resolution GmbH に自動的にサインインされます。

また、Microsoft マイ アプリを使用して、任意のモードでアプリケーションをテストすることもできます。 マイ アプリで [SAML SSO for Bitbucket by resolution GmbH] タイルをクリックすると、SP モードで構成されている場合は、ログイン フローを開始するためのアプリケーション サインオン ページにリダイレクトされます。IDP モードで構成されている場合は、SSO を設定した SAML SSO for Bitbucket by resolution GmbH に自動的にサインインされます。 マイ アプリの詳細については、[マイ アプリの概要](../user-help/my-apps-portal-end-user-access.md)に関するページを参照してください。


## <a name="next-steps"></a>次のステップ

SAML SSO for Bitbucket by resolution GmbH を構成したら、組織の機密データを流出と侵入からリアルタイムで保護するセッション制御を適用できます。 セッション制御は、条件付きアクセスを拡張したものです。 [Microsoft Cloud App Security でセッション制御を強制する方法](/cloud-app-security/proxy-deployment-any-app)をご覧ください。