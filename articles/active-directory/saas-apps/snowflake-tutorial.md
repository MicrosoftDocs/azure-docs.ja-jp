---
title: 'チュートリアル: Azure Active Directory と Snowflake の統合 | Microsoft Docs'
description: Azure Active Directory と Snowflake の間でシングル サインオンを構成する方法について説明します。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 12/27/2020
ms.author: jeedes
ms.openlocfilehash: 1af0209265ec4945950120e80a83e19c8ab2eb4b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "98726279"
---
# <a name="tutorial-azure-active-directory-integration-with-snowflake"></a>チュートリアル: Azure Active Directory と Snowflake の統合

このチュートリアルでは、Snowflake と Azure Active Directory (Azure AD) を統合する方法について説明します。 Azure AD と Snowflake を統合すると、次のことが可能になります。

* Snowflake にアクセスできるユーザーを Azure AD で制御できます。
* ユーザーが自分の Azure AD アカウントを使用して Snowflake に自動的にサインインできるように設定できます。
* 1 つの中央サイト (Azure Portal) で自分のアカウントを管理します。

## <a name="prerequisites"></a>前提条件

Snowflake と Azure AD の統合を構成するには、次のものが必要です。

* Azure AD サブスクリプション。 Azure AD の環境がない場合は、[こちら](https://azure.microsoft.com/pricing/free-trial/)から 1 か月の評価版を入手できます
* Snowflake でのシングル サインオンが有効なサブスクリプション

## <a name="scenario-description"></a>シナリオの説明

このチュートリアルでは、テスト環境で Azure AD のシングル サインオンを構成してテストします。

- Snowflake では、**SP と IDP** によって開始される SSO がサポートされます
- Snowflake では、[自動化されたユーザー プロビジョニングとプロビジョニング解除](snowflake-provisioning-tutorial.md) (推奨) がサポートされます

## <a name="adding-snowflake-from-the-gallery"></a>ギャラリーからの Snowflake の追加

Azure AD への Snowflake の統合を構成するには、ギャラリーからマネージド SaaS アプリの一覧に Snowflake を追加する必要があります。

1. 職場または学校アカウントか、個人の Microsoft アカウントを使用して、Azure portal にサインインします。
1. 左のナビゲーション ウィンドウで **[Azure Active Directory]** サービスを選択します。
1. **[エンタープライズ アプリケーション]** に移動し、 **[すべてのアプリケーション]** を選択します。
1. 新しいアプリケーションを追加するには、 **[新しいアプリケーション]** を選択します。
1. **[ギャラリーから追加する]** セクションで、検索ボックスに「**Snowflake**」と入力します。
1. 結果のパネルから **[Snowflake]** を選択し、アプリを追加します。 お使いのテナントにアプリが追加されるのを数秒待機します。

## <a name="configure-and-test-azure-ad-sso-for-snowflake"></a>Snowflake の Azure AD SSO の構成とテスト

**B.Simon** というテスト ユーザーを使用して、Snowflake に対する Azure AD SSO を構成してテストします。 SSO が機能するためには、Azure AD ユーザーと Snowflake の関連ユーザーとの間にリンク関係を確立する必要があります。

Snowflake に対する Azure AD SSO を構成してテストするには、次の構成要素を完了します。

1. **[Azure AD SSO の構成](#configure-azure-ad-sso)** - ユーザーがこの機能を使用できるようにします。
    1. **[Azure AD のテスト ユーザーの作成](#create-an-azure-ad-test-user)** - B.Simon で Azure AD のシングル サインオンをテストします。
    1. **[Azure AD テスト ユーザーの割り当て](#assign-the-azure-ad-test-user)** - B.Simon が Azure AD シングル サインオンを使用できるようにします。
1. **[Snowflake の SSO の構成](#configure-snowflake-sso)** - アプリケーション側でシングル サインオン設定を構成します。
    1. **[Snowflake のテスト ユーザーの作成](#create-snowflake-test-user)** - Snowflake で B.Simon に対応するユーザーを作成し、Azure AD の B.Simon にリンクさせます。
1. **[SSO のテスト](#test-sso)** - 構成が機能するかどうかを確認します。

### <a name="configure-azure-ad-sso"></a>Azure AD SSO の構成

これらの手順に従って、Azure portal で Azure AD SSO を有効にします。

1. Azure portal の **Snowflake** アプリケーション統合ページで、 **[管理]** セクションを見つけて、 **[シングル サインオン]** を選択します。
1. **[シングル サインオン方式の選択]** ページで、 **[SAML]** を選択します。
1. **[SAML によるシングル サインオンのセットアップ]** ページで、 **[基本的な SAML 構成]** の鉛筆アイコンをクリックして設定を編集します。

   ![基本的な SAML 構成を編集する](common/edit-urls.png)

4. **[基本的な SAML 構成]** セクションで、**IDP** 開始モードでアプリケーションを構成する場合は、次の手順を実行します。

    a. **[識別子]** ボックスに、`https://<SNOWFLAKE-URL>.snowflakecomputing.com` の形式で URL を入力します。

    b. **[応答 URL]** ボックスに、`https://<SNOWFLAKE-URL>.snowflakecomputing.com/fed/login` のパターンを使用して URL を入力します

1. アプリケーションを SP 開始モードで構成する場合は、 **[追加の URL を設定します]** をクリックして次の手順を実行します。

    a. **[サインオン URL]** ボックスに、`https://<SNOWFLAKE-URL>.snowflakecomputing.com` という形式で URL を入力します。
    
    b. **[ログアウト URL]** テキスト ボックスに、`https://<SNOWFLAKE-URL>.snowflakecomputing.com/fed/logout` のパターンを使用して URL を入力します。

    > [!NOTE]
    > これらは実際の値ではありません。 実際の識別子、応答 URL、サインオン URL でこれらの値を更新します。 これらの値を取得するには、[Snowflake クライアント サポート チーム](https://support.snowflake.net/s/)に問い合わせてください。 Azure portal の **[基本的な SAML 構成]** セクションに示されているパターンを参照することもできます。

4. **[SAML でシングル サインオンをセットアップします]** ページの **[SAML 署名証明書]** セクションで、 **[ダウンロード]** をクリックして要件のとおりに指定したオプションからの **証明書 (Base64)** をダウンロードして、お使いのコンピューターに保存します。

    ![証明書のダウンロードのリンク](common/certificatebase64.png)

6. **[Snowflake の設定]** セクションで、要件に従って適切な URL をコピーします。

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

このセクションでは、B.Simon に Snowflake へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

1. Azure portal で **[エンタープライズ アプリケーション]** を選択し、 **[すべてのアプリケーション]** を選択します。
1. アプリケーションの一覧で **[Snowflake]** を選択します。
1. アプリの概要ページで、 **[管理]** セクションを見つけて、 **[ユーザーとグループ]** を選択します。
1. **[ユーザーの追加]** を選択し、 **[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。
1. **[ユーザーとグループ]** ダイアログの [ユーザー] の一覧から **[B.Simon]** を選択し、画面の下部にある **[選択]** ボタンをクリックします。
1. ユーザーにロールが割り当てられることが想定される場合は、 **[ロールの選択]** ドロップダウンからそれを選択できます。 このアプリに対してロールが設定されていない場合は、[既定のアクセス] ロールが選択されていることを確認します。
1. **[割り当ての追加]** ダイアログで、 **[割り当て]** をクリックします。

## <a name="configure-snowflake-sso"></a>Snowflake の SSO の構成

1. 別の Web ブラウザー ウィンドウで、セキュリティ管理者として Snowflake にログインします。

1. ページの右上で **[プロファイル]** をクリックして、 **[Switch Role]\(ロールの切り替え\)** で **[ACCOUNTADMIN]** を選択します。

    > [!NOTE]
    > これは、右上の [ユーザー名] で選択したコンテキストとは別のものです。
    
    ![Snowflake 管理者](./media/snowflake-tutorial/tutorial_snowflake_accountadmin.png)

1. **ダウンロードした Base 64 証明書** をメモ帳で開きます。 “-----BEGIN CERTIFICATE-----” と “-----END CERTIFICATE-----" の間の値をコピーし、下の **certificate** の横の引用符の間に貼り付けます。 **[ssoUrl]** に、Azure portal からコピーした **ログイン URL** の値を貼り付けます。 **[All Queries]\(すべてのクエリ\)** を選択し、 **[実行]** をクリックします。

   ![Snowflake sql](./media/snowflake-tutorial/tutorial_snowflake_sql.png)

   ```
   use role accountadmin;
   alter account set saml_identity_provider = '{
   "certificate": "<Paste the content of downloaded certificate from Azure portal>",
   "ssoUrl":"<Login URL value which you have copied from the Azure portal>",
   "type":"custom",
   "label":"AzureAD"
   }';
   alter account set sso_login_page = TRUE;
   ```


### <a name="create-snowflake-test-user"></a>Snowflake のテスト ユーザーの作成

Azure AD ユーザーが Snowflake にログインできるようにするには、そのユーザーを Snowflake にプロビジョニングする必要があります。 Snowflake では、プロビジョニングは手動のタスクです。

**ユーザー アカウントをプロビジョニングするには、次の手順に従います。**

1. セキュリティ管理者として Snowflake にログインします。

2. ページの右上で **[プロファイル]** をクリックして、 **[Switch Role]\(ロールの切り替え\)** で **[ACCOUNTADMIN]** を選択します。  

    ![Snowflake 管理者](./media/snowflake-tutorial/tutorial_snowflake_accountadmin.png)

3. 次に示すように、"Login name" がワークシートの Azure AD ユーザー名に設定されていることを確認して、次の SQL クエリを実行してユーザーを作成します。

    ![Snowflake adminsql](./media/snowflake-tutorial/tutorial_snowflake_usersql.png)

    ```
    use role accountadmin;
    CREATE USER britta_simon PASSWORD = '' LOGIN_NAME = 'BrittaSimon@contoso.com' DISPLAY_NAME = 'Britta Simon';
    ```

### <a name="test-sso"></a>SSO のテスト 

このセクションでは、次のオプションを使用して Azure AD のシングル サインオン構成をテストします。 

#### <a name="sp-initiated"></a>SP Initiated:

* Azure portal で **[このアプリケーションをテストします]** をクリックします。 これにより、ログイン フローを開始できる Snowflake のサインオン URL にリダイレクトされます。  

* Snowflake のサインオン URL に直接移動し、そこからログイン フローを開始します。

#### <a name="idp-initiated"></a>IDP Initiated:

* Azure portal で **[このアプリケーションをテストします]** をクリックすると、SSO を設定した Snowflake に自動的にサインインされます 

また、Microsoft マイ アプリを使用して、任意のモードでアプリケーションをテストすることもできます。 マイ アプリで [Snowflake] タイルをクリックすると、SP モードで構成されている場合は、ログイン フローを開始するためのアプリケーション サインオン ページにリダイレクトされます。IDP モードで構成されている場合は、SSO を設定した Snowflake に自動的にサインインされます。 マイ アプリの詳細については、[マイ アプリの概要](../user-help/my-apps-portal-end-user-access.md)に関するページを参照してください。


## <a name="next-steps"></a>次のステップ

Snowflake を構成したら、組織の機密データを流出と侵入からリアルタイムで保護するセッション制御を適用することができます。 セッション制御は、条件付きアクセスを拡張したものです。 [Microsoft Cloud App Security でセッション制御を適用する方法](/cloud-app-security/proxy-deployment-aad)をご覧ください。