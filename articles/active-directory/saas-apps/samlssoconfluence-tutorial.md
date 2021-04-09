---
title: チュートリアル:Azure Active Directory と SAML SSO for Confluence by resolution GmbH の統合 | Microsoft Docs
description: Azure Active Directory と SAML SSO for Confluence by resolution GmbH の間でシングル サインオンを構成する方法について説明します。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 02/04/2021
ms.author: jeedes
ms.openlocfilehash: 9290272920bbb20144f4e0d957ba2d9ce60d06a9
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "101651282"
---
# <a name="tutorial-azure-active-directory-integration-with-saml-sso-for-confluence-by-resolution-gmbh"></a>チュートリアル:Azure Active Directory と SAML SSO for Confluence by resolution GmbH の統合

このチュートリアルでは、SAML SSO for Confluence by resolution GmbH と Azure Active Directory (Azure AD) を統合する方法について説明します。 SAML SSO for Confluence by resolution GmbH を Azure AD と統合すると、次のことが可能になります。

* SAML SSO for Confluence by resolution GmbH にアクセスできるユーザーを Azure AD で制御できます。
* ユーザーが自分の Azure AD アカウントを使用して SAML SSO for Confluence by resolution GmbH に自動的にサインインできるように設定できます。
* 1 つの中央サイト (Azure Portal) で自分のアカウントを管理します。

## <a name="prerequisites"></a>前提条件

開始するには、次が必要です。

* Azure AD サブスクリプション。 サブスクリプションがない場合は、[無料アカウント](https://azure.microsoft.com/free/)を取得できます。
* シングル サインオン (SSO) が有効な SAML SSO for Confluence by resolution GmbH サブスクリプション。

## <a name="scenario-description"></a>シナリオの説明

このチュートリアルでは、テスト環境で Azure AD の SSO を構成してテストします。

* SAML SSO for Confluence by resolution GmbH では、**SP と IDP** によって開始される SSO がサポートされます

## <a name="add-saml-sso-for-confluence-by-resolution-gmbh-from-the-gallery"></a>ギャラリーからの SAML SSO for Confluence by resolution GmbH の追加

Azure AD への SAML SSO for Confluence by resolution GmbH の統合を構成するには、ギャラリーから管理対象 SaaS アプリの一覧に SAML SSO for Confluence by resolution GmbH を追加する必要があります。

1. 職場または学校アカウントか、個人の Microsoft アカウントを使用して、Azure portal にサインインします。
1. 左のナビゲーション ウィンドウで **[Azure Active Directory]** サービスを選択します。
1. **[エンタープライズ アプリケーション]** に移動し、 **[すべてのアプリケーション]** を選択します。
1. 新しいアプリケーションを追加するには、 **[新しいアプリケーション]** を選択します。
1. **[ギャラリーから追加する]** セクションで、検索ボックスに「**SAML SSO for Confluence by resolution GmbH**」と入力します。
1. 結果パネルから **[SAML SSO for Confluence by resolution GmbH]** を選択し、アプリを追加します。 お使いのテナントにアプリが追加されるのを数秒待機します。

## <a name="configure-and-test-azure-ad-sso-for-saml-sso-for-confluence-by-resolution-gmbh"></a>SAML SSO for Confluence by resolution GmbH の Azure AD SSO の構成とテスト

**B.Simon** というテスト ユーザーを使用して、SAML SSO for Confluence by resolution GmbH に対する Azure AD SSO を構成してテストします。 SSO を機能させるには、Azure AD ユーザーと SAML SSO for Confluence by resolution GmbH の関連ユーザーとの間にリンク関係を確立する必要があります。

SAML SSO for Confluence by resolution GmbH に対する Azure AD SSO を構成してテストするには、次の手順を実行します。

1. **[Azure AD SSO の構成](#configure-azure-ad-sso)** - ユーザーがこの機能を使用できるようにします。
    1. **[Azure AD のテスト ユーザーの作成](#create-an-azure-ad-test-user)** - B.Simon で Azure AD のシングル サインオンをテストします。
    1. **[Azure AD テスト ユーザーの割り当て](#assign-the-azure-ad-test-user)** - B.Simon が Azure AD シングル サインオンを使用できるようにします。
2. **[SAML SSO for Confluence by resolution GmbH の SSO の構成](#configure-saml-sso-for-confluence-by-resolution-gmbh-sso)** - アプリケーション側でシングル サインオン設定を構成します。
    1. **[SAML SSO for Confluence by resolution GmbH テスト ユーザーの作成](#create-saml-sso-for-confluence-by-resolution-gmbh-test-user)** - SAML SSO for Confluence by resolution GmbH で Britta Simon に対応するユーザーを作成し、Azure AD の Britta Simon にリンクさせます。
6. **[SSO のテスト](#test-sso)** - 構成が機能するかどうかを確認します。

## <a name="configure-azure-ad-sso"></a>Azure AD SSO の構成

これらの手順に従って、Azure portal で Azure AD SSO を有効にします。

1. Azure portal の **SAML SSO for Confluence by resolution GmbH** アプリケーション統合ページで、 **[管理]** セクションを見つけて、 **[シングル サインオン]** を選択します。
1. **[シングル サインオン方式の選択]** ページで、 **[SAML]** を選択します。
1. **[SAML によるシングル サインオンのセットアップ]** ページで、 **[基本的な SAML 構成]** の鉛筆アイコンをクリックして設定を編集します。

   ![基本的な SAML 構成を編集する](common/edit-urls.png)

1. **[基本的な SAML 構成]** セクションで、アプリケーションを **IDP** 開始モードで構成する場合は、次のフィールドの値を入力します。

    a. **[識別子]** ボックスに、`https://<server-base-url>/plugins/servlet/samlsso` の形式で URL を入力します。

    b. **[応答 URL]** ボックスに、`https://<server-base-url>/plugins/servlet/samlsso` のパターンを使用して URL を入力します

1. アプリケーションを **SP** 開始モードで構成する場合は、 **[追加の URL を設定します]** をクリックして次の手順を実行します。

    **[サインオン URL]** ボックスに、`https://<server-base-url>/plugins/servlet/samlsso` という形式で URL を入力します。

    > [!NOTE]
    > これらは実際の値ではありません。 実際の識別子、応答 URL、サインオン URL でこれらの値を更新します。 これらの値を取得するには、[SAML SSO for Confluence by resolution GmbH クライアント サポート チーム](https://www.resolution.de/go/support)に問い合わせます。 Azure portal の **[基本的な SAML 構成]** セクションに示されているパターンを参照することもできます。

4. **[SAML でシングル サインオンをセットアップします]** ページの **[SAML 署名証明書]** セクションで、 **[ダウンロード]** をクリックして、要件のとおりに指定したオプションから **フェデレーション メタデータ XML** をダウンロードして、お使いのコンピューターに保存します。

    ![証明書のダウンロードのリンク](common/metadataxml.png)


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

このセクションでは、B.Simon に SAML SSO for Confluence by resolution GmbH へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

1. Azure portal で **[エンタープライズ アプリケーション]** を選択し、 **[すべてのアプリケーション]** を選択します。
1. アプリケーションの一覧で **[SAML SSO for Confluence by resolution GmbH]** を選択します。
1. アプリの概要ページで、 **[管理]** セクションを見つけて、 **[ユーザーとグループ]** を選択します。
1. **[ユーザーの追加]** を選択し、 **[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。
1. **[ユーザーとグループ]** ダイアログの [ユーザー] の一覧から **[B.Simon]** を選択し、画面の下部にある **[選択]** ボタンをクリックします。
1. ユーザーにロールが割り当てられることが想定される場合は、 **[ロールの選択]** ドロップダウンからそれを選択できます。 このアプリに対してロールが設定されていない場合は、[既定のアクセス] ロールが選択されていることを確認します。
1. **[割り当ての追加]** ダイアログで、 **[割り当て]** をクリックします。


## <a name="configure-saml-sso-for-confluence-by-resolution-gmbh-sso"></a>SAML SSO for Confluence by resolution GmbH の SSO の構成

1. 別の Web ブラウザー ウィンドウで、**SAML SSO for Confluence by resolution GmbH 管理者ポータル** に管理者としてログインします。

2. 歯車をポイントし、 **[Add-ons]\(アドオン\)** をクリックします。
    
    !["歯車" アイコンが選択され、ドロップダウンの [Add-ons]\(アドオン\) が選択されているスクリーンショット。](./media/saml-sso-confluence-tutorial/add-on-1.png)

3. [Administrator Access]\(管理者アクセス\) のページにリダイレクトされます。 パスワードを入力し、 **[Confirm]\(確認\)** ボタンをクリックします。

    ![[Administrator Access]\(管理者アクセス\) ページを示すスクリーンショット。[Confirm]\(確認\) ボタンが選択されています。](./media/saml-sso-confluence-tutorial/add-on-2.png)

4. **[ATLASSIAN MARKETPLACE]** タブで、 **[Find new add-ons]\(新しいアドオンの検索\)** をクリックします。 

    ![[ATTLASSIAN MARKETPLACE] タブを示すスクリーンショット。[Find new add-ons]\(新しいアドオンの検索\) が選択されています。](./media/saml-sso-confluence-tutorial/add-on.png)

5. **[SAML Single Sign On (SSO) for Confluence]\(Confluence の SAML シングル サインオン \(SSO\)\)** を検索し、 **[Install]\(インストール\)** ボタンをクリックして、新しい SAML プラグインをインストールします。

    ![[Find new add-ons]\(新しいアドオンの検索\) ページを示すスクリーンショット。検索ボックスに [S A M L Single Sign On (S S O) for Confluence]\(Confluence の S A M L シングル サインオン (S S O)\) と表示されており、[Install]\(インストール\) ボタンが選択されています。](./media/saml-sso-confluence-tutorial/add-on-7.png)

6. プラグインのインストールが開始されます。 **[閉じる]** をクリックします。

    ![[Installing]\(インストール中\) ダイアログを示すスクリーンショット。](./media/saml-sso-confluence-tutorial/add-on-8.png)

    !["Installed and ready to go!"\(インストールされ、使用できるようになりました\) と示すスクリーンショット。 [閉じる] アクションが選択されているダイアログ。](./media/saml-sso-confluence-tutorial/add-on-9.png)

7.  **Manage** をクリックします。

    ![[S A M L Single Sign On (S S O) for Confluence]\(Confluence の S A M L シングル サインオン (S S O)\) アプリ ページを示すスクリーンショット。[Manage]\(管理\) ボタンが選択されています。](./media/saml-sso-confluence-tutorial/add-on-10.png)
    
8. **[Configure]\(構成\)** をクリックして、新しいプラグインを構成します。

    ![[Manage]\(管理\) ページを示すスクリーンショット。[Configure]\(構成\) ボタンが選択されています。](./media/saml-sso-confluence-tutorial/add-on-11.png)

9. この新しいプラグインは、 **[USERS & SECURITY]\(ユーザーとセキュリティ\)** タブにも表示されます。

    ![[USERS & SECURITY]\(ユーザーとセキュリティ\) タブを示すスクリーンショット。[S A M L SingleSignOn]\(S A M L SingleSignOn\) が選択されています。](./media/saml-sso-confluence-tutorial/add-on-3.png)
    
10. **[SAML SingleSignOn Plugin Configuration]\(SAML SingleSignOn プラグインの構成\)** ページで **[Add new IdP]\(新しい IDP の追加\)** ボタンをクリックし、ID プロバイダーの設定を構成します。

    ![[S A M L SingleSignOn Plugin Configuration]\(S A M L SingleSignOn プラグインの構成\) ページを示すスクリーンショット。[Add new I d P]\(新しい I D P の追加\) ボタンが選択されています。](./media/saml-sso-confluence-tutorial/add-on-4.png)

11. **[Choose your SAML Identity Provider]\(SAML ID プロバイダーの選択\)** ページで、次の手順を実行します。

    ![[Choose your S A M L Identity Provider]\(S A M L I D プロバイダーの選択\) ページを示すスクリーンショット。[I d P Type]\(I d P の種類\)、[Name]\(名前\)、および [Description]\(説明\) テキスト ボックスが強調表示されています。](./media/saml-sso-confluence-tutorial/add-on-5-a.png)
 
    a. IDP の種類として **[Azure AD]** を設定します。
    
    b. ID プロバイダーの **名前** (たとえば Azure AD) を追加します。
    
    c. ID プロバイダーの **説明** (たとえば Azure AD) を追加します。
    
    d. **[次へ]** をクリックします。
    
12. **[Identity provider configuration]\(ID プロバイダーの構成\)** ページで、**[次へ]** ボタンをクリックします。

    ![[Identity provider configuration]\(I D プロバイダーの構成\) ページを示すスクリーンショット。[Next]\(次へ\) ボタンが選択されています。](./media/saml-sso-confluence-tutorial/add-on-5-b.png)

13. **[Import SAML IdP Metadata]\(SAML IDP メタデータのインポート\)** ページで、次の手順を実行します。

    ![[Import S A M L I d P Metadata]\(S A M L I D P メタデータのインポート\) ページを示すスクリーンショット。[Import]\(インポート\)、[Load File]\(ファイルの読み込み\)、および [Next]\(次へ\) ボタンが選択されています。](./media/saml-sso-confluence-tutorial/add-on-5-c.png)

    a. **[Load File]\(ファイルの読み込み\)** ボタンをクリックし、手順 5 でダウンロードしたメタデータ XML ファイルを選びます。

    b. **[インポート]** ボタンをクリックします。
    
    c. インポートが成功するまでしばらく待ちます。
    
    d. **[次へ]** ボタンをクリックします。
    
14. **[User ID attribute and transformation]\(ユーザーの ID 属性と変換\)** ページで、**[次へ]** ボタンをクリックします。

    ![[User ID attribute and transformation]\(ユーザーの I D 属性と変換\) ページを示すスクリーンショット。[Next]\(次へ\) ボタンが選択されています。](./media/saml-sso-confluence-tutorial/add-on-5-d.png)
    
15. **[User creation and update]\(ユーザーの作成と更新\)** ページで、**[Save & Next]\(保存して次へ\)** をクリックして設定を保存します。   
    
    ![[User creation and update]\(ユーザーの作成と更新\) ページを示すスクリーンショット。[Next]\(次へ\) ボタンが選択されています。](./media/saml-sso-confluence-tutorial/add-on-6-a.png)
    
16. **[Test your settings]\(設定のテスト\)** ページで、**[Skip test & configure manually]\(テストをスキップして手動で構成\)** をクリックしてここではユーザー テストをスキップします。 テストは次のセクションで実行し、Azure Portal でいくつか設定する必要があります。 
    
    ![[Test your settings]\(設定のテスト\) ページを示すスクリーンショット。[Skip test & configure manually]\(テストをスキップして手動で構成\) ボタンが選択されています。](./media/saml-sso-confluence-tutorial/add-on-6-b.png)
    
17. 表示される **[Skipping the test means...]\(テストをスキップすると...\)** ダイアログ ボックスで、**[OK]** をクリックします。
    
    ![Configure single sign-on](./media/saml-sso-confluence-tutorial/add-on-6-c.png)


### <a name="create-saml-sso-for-confluence-by-resolution-gmbh-test-user"></a>SAML SSO for Confluence by resolution GmbH のテスト ユーザーの作成

Azure AD ユーザーが SAML SSO for Confluence by resolution GmbH にログインできるようにするには、ユーザーを SAML SSO for Confluence by resolution GmbH にプロビジョニングする必要があります。  
SAML SSO for Confluence by resolution GmbH の場合、プロビジョニングは手動で行います。

**ユーザー アカウントをプロビジョニングするには、次の手順に従います。**

1. SAML SSO for Confluence by resolution GmbH 企業サイトに管理者としてログインします。

2. 歯車をポイントし、 **[User management]\(ユーザー管理\)** をクリックします。

    !["歯車" アイコンが選択され、メニューの [User management]\(ユーザー管理\) が強調表示されているスクリーンショット。](./media/saml-sso-confluence-tutorial/user-1.png) 

3. [Users]\(ユーザー\) セクションで、 **[Add users]\(ユーザーの追加\)** タブをクリックします。 **[Add a User]\(ユーザーの追加\)** ダイアログ ページで、次の手順に従います。

    ![従業員の追加](./media/saml-sso-confluence-tutorial/user-2.png) 

    a. **[Username]\(ユーザー名\)** ボックスに、ユーザーのメール (Britta Simon など) を入力します。

    b. **[Full Name]\(フル ネーム\)** ボックスに、ユーザーの氏名 (Britta Simon など) を入力します。

    c. **[Email]\(メール\)** ボックスに、ユーザーのメール アドレス (Brittasimon@contoso.com など) を入力します。

    d. **[Password]\(パスワード\)** ボックスに、Britta Simon のパスワードを入力します。

    e. **[Confirm Password]\(パスワードの確認\)** をクリックし、パスワードを再入力します。
    
    f. **[追加]** ボタンをクリックします。

## <a name="test-sso"></a>SSO のテスト 

このセクションでは、次のオプションを使用して Azure AD のシングル サインオン構成をテストします。 

#### <a name="sp-initiated"></a>SP Initiated:

* Azure portal で **[このアプリケーションをテストします]** をクリックします。 これにより、ログイン フローを開始できる SAML SSO for Confluence by resolution GmbH のサインオン URL にリダイレクトされます。  

* SAML SSO for Confluence by resolution GmbH のサインオン URL に直接移動し、そこからログイン フローを開始します。

#### <a name="idp-initiated"></a>IDP Initiated:

* Azure portal で **[このアプリケーションをテストします]** をクリックすると、SSO を設定した SAML SSO for Confluence by resolution GmbH に自動的にサインインされます 

また、Microsoft マイ アプリを使用して、任意のモードでアプリケーションをテストすることもできます。 マイ アプリで [SAML SSO for Confluence by resolution GmbH] タイルをクリックすると、SP モードで構成されている場合は、ログイン フローを開始するためのアプリケーション サインオン ページにリダイレクトされます。IDP モードで構成されている場合は、SSO を設定した SAML SSO for Confluence by resolution GmbH に自動的にサインインされます。 マイ アプリの詳細については、[マイ アプリの概要](../user-help/my-apps-portal-end-user-access.md)に関するページを参照してください。

## <a name="next-steps"></a>次のステップ

SAML SSO for Confluence by resolution GmbH を構成したら、組織の機密データを流出と侵入からリアルタイムで保護するセッション制御を適用できます。 セッション制御は、条件付きアクセスを拡張したものです。 [Microsoft Cloud App Security でセッション制御を強制する方法](/cloud-app-security/proxy-deployment-any-app)をご覧ください。