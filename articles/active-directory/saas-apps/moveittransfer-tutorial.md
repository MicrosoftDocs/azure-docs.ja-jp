---
title: 'チュートリアル: Azure Active Directory と MOVEit Transfer - Azure AD integration の統合 | Microsoft Docs'
description: Azure Active Directory と MOVEit Transfer - Azure AD integration の間でシングル サインオンを構成する方法について確認します。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/27/2021
ms.author: jeedes
ms.openlocfilehash: 73f378bb0f71df4df3731a3ef2a1fdb7f8abb4aa
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "101653059"
---
# <a name="tutorial-azure-active-directory-integration-with-moveit-transfer---azure-ad-integration"></a>チュートリアル: Azure Active Directory と MOVEit Transfer - Azure AD integration の統合

このチュートリアルでは、MOVEit Transfer - Azure AD integration と Azure Active Directory (Azure AD) を統合する方法について説明します。 MOVEit Transfer - Azure AD integration と Azure AD を統合すると、次のことができます。

* MOVEit Transfer - Azure AD integration にアクセスする Azure AD ユーザーを制御できます。
* ユーザーが自分の Azure AD アカウントを使用して MOVEit Transfer - Azure AD integration に自動的にサインインできるように設定できます。
* 1 つの中央サイト (Azure Portal) で自分のアカウントを管理します。

## <a name="prerequisites"></a>前提条件

開始するには、次が必要です。

* Azure AD サブスクリプション。 サブスクリプションがない場合は、[無料アカウント](https://azure.microsoft.com/free/)を取得できます。
* MOVEit Transfer - Azure AD integration でのシングル サインオン (SSO) が有効なサブスクリプション。

## <a name="scenario-description"></a>シナリオの説明

このチュートリアルでは、テスト環境で Azure AD のシングル サインオンを構成してテストします。

* MOVEit Transfer - Azure AD integration では、**SP** によって開始される SSO がサポートされます

## <a name="add-moveit-transfer---azure-ad-integration-from-the-gallery"></a>ギャラリーからの MOVEit Transfer - Azure AD integration の追加

Azure AD への MOVEit Transfer - Azure AD integration の統合を構成するには、ギャラリーから管理対象 SaaS アプリの一覧に MOVEit Transfer - Azure AD integration を追加する必要があります。

1. 職場または学校アカウントか、個人の Microsoft アカウントを使用して、Azure portal にサインインします。
1. 左のナビゲーション ウィンドウで **[Azure Active Directory]** サービスを選択します。
1. **[エンタープライズ アプリケーション]** に移動し、 **[すべてのアプリケーション]** を選択します。
1. 新しいアプリケーションを追加するには、 **[新しいアプリケーション]** を選択します。
1. **[ギャラリーから追加する]** セクションで、検索ボックスに「**MOVEit Transfer - Azure AD integration**」と入力します。
1. 結果のパネルから **[MOVEit Transfer - Azure AD integration]** を選択し、アプリを追加します。 お使いのテナントにアプリが追加されるのを数秒待機します。

## <a name="configure-and-test-azure-ad-sso-for-moveit-transfer---azure-ad-integration"></a>MOVEit Transfer - Azure AD integration の Azure AD SSO の構成とテスト

**B.Simon** というテスト ユーザーを使用して、MOVEit Transfer - Azure AD integration に対する Azure AD SSO を構成してテストします。 SSO を機能させるためには、Azure AD ユーザーと MOVEit Transfer - Azure AD integration の関連ユーザーとの間にリンク関係を確立する必要があります。

MOVEit Transfer - Azure AD integration で Azure AD SSO を構成してテストするには、次の手順に従います。

1. **[Azure AD SSO の構成](#configure-azure-ad-sso)** - ユーザーがこの機能を使用できるようにします。
    1. **[Azure AD のテスト ユーザーの作成](#create-an-azure-ad-test-user)** - B.Simon で Azure AD のシングル サインオンをテストします。
    1. **[Azure AD テスト ユーザーの割り当て](#assign-the-azure-ad-test-user)** - B.Simon が Azure AD シングル サインオンを使用できるようにします。
1. **[MOVEit Transfer - Azure AD integration の SSO の構成](#configure-moveit-transfer---azure-ad-integration-sso)** - アプリケーション側でシングル サインオン設定を構成します。
    1. **[MOVEit Transfer - Azure AD integration のテスト ユーザーの作成](#create-moveit-transfer---azure-ad-integration-test-user)** - MOVEit Transfer - Azure AD integration で Britta Simon に対応するユーザーを作成し、Azure AD の B.Simon にリンクさせます。
1. **[SSO のテスト](#test-sso)** - 構成が機能するかどうかを確認します。

### <a name="configure-azure-ad-sso"></a>Azure AD SSO の構成

これらの手順に従って、Azure portal で Azure AD SSO を有効にします。

1. Azure portal の **MOVEit Transfer - Azure AD integration** アプリケーション統合ページで、 **[管理]** セクションを見つけて、 **[シングル サインオン]** を選択します。
1. **[シングル サインオン方式の選択]** ページで、 **[SAML]** を選択します。
1. **[SAML によるシングル サインオンのセットアップ]** ページで、 **[基本的な SAML 構成]** の鉛筆アイコンをクリックして設定を編集します。

   ![基本的な SAML 構成を編集する](common/edit-urls.png)

4. **[基本的な SAML 構成]** セクションで、**サービス プロバイダー メタデータ ファイル** がある場合は、次の手順に従います。

    a. **[メタデータ ファイルをアップロードします]** をクリックします。

    ![メタデータ ファイルをアップロードする](common/upload-metadata.png)

    b. **フォルダー ロゴ** をクリックしてメタデータ ファイルを選択し、 **[アップロード]** をクリックします。

    ![メタデータ ファイルを選択する](common/browse-upload-metadata.png)

    c. メタデータ ファイルが正常にアップロードされると、**識別子** と **[応答 URL]** の値が、**[基本的な SAML 構成]** セクションに自動的に設定されます。

    ![MOVEit Transfer - Azure AD integration のドメインと URL のシングル サインオン情報](common/sp-identifier-reply.png)

    **[サインオン URL]** テキスト ボックスに、URL として「`https://contoso.com`」と入力します。

    > [!NOTE]
    > **[サインオン URL]** は実際の値ではありません。 実際のサインオン URL でこの値を更新してください。 この値を取得するには、[MOVEit Transfer - Azure AD integration クライアント サポート](https://community.ipswitch.com/s/support) チームに問い合わせてください。 **サービス プロバイダー メタデータ ファイル** は、チュートリアルの「**MOVEit Transfer - Azure AD integration のシングル サインオンの構成**」セクションで後述する **サービス プロバイダー メタデータ URL** からダウンロードできます。 Azure portal の **[基本的な SAML 構成]** セクションに示されているパターンを参照することもできます。

4. **[SAML でシングル サインオンをセットアップします]** ページの **[SAML 署名証明書]** セクションで、 **[ダウンロード]** をクリックして、要件のとおりに指定したオプションから **フェデレーション メタデータ XML** をダウンロードして、お使いのコンピューターに保存します。

    ![証明書のダウンロードのリンク](common/metadataxml.png)

6. **[MOVEit Transfer - Azure AD integration のセットアップ]** セクションで、要件に従って適切な URL をコピーします。

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

このセクションでは、MOVEit Transfer - Azure AD integration へのアクセスを許可することで、B.Simon が Azure シングル サインオンを使用できるようにします。

1. Azure portal で **[エンタープライズ アプリケーション]** を選択し、 **[すべてのアプリケーション]** を選択します。
1. アプリケーションの一覧で **[MOVEit Transfer - Azure AD integration]** を選択します。
1. アプリの概要ページで、 **[管理]** セクションを見つけて、 **[ユーザーとグループ]** を選択します。
1. **[ユーザーの追加]** を選択し、 **[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。
1. **[ユーザーとグループ]** ダイアログの [ユーザー] の一覧から **[B.Simon]** を選択し、画面の下部にある **[選択]** ボタンをクリックします。
1. ユーザーにロールが割り当てられることが想定される場合は、 **[ロールの選択]** ドロップダウンからそれを選択できます。 このアプリに対してロールが設定されていない場合は、[既定のアクセス] ロールが選択されていることを確認します。
1. **[割り当ての追加]** ダイアログで、 **[割り当て]** をクリックします。

### <a name="configure-moveit-transfer---azure-ad-integration-sso"></a>MOVEit Transfer - Azure AD integration の SSO の構成

1. MOVEit Transfer テナントに管理者としてサインオンします。

2. 左側のナビゲーション ウィンドウで、 **[Settings (設定)]** をクリックします。

    ![アプリ側の [Settings]\(設定\) セクション](./media/moveittransfer-tutorial/settings.png)

3. **[Security Policies]\(セキュリティ ポリシー\) -> [User Auth]\(ユーザー認証\)** にある **[Single Signon]\(シングル サインオン\)** リンクをクリックします。

    ![アプリ側のセキュリティ ポリシー](./media/moveittransfer-tutorial/sso.png)

4. メタデータ URL リンクをクリックし、メタデータ ドキュメントをダウンロードします。

    ![サービス プロバイダー メタデータ URL](./media/moveittransfer-tutorial/metadata.png)
    
   * **[基本的な SAML 構成]** セクションで、**entityID** が **識別子** と一致することを確認します。
   * **AssertionConsumerService** の場所 URL が **[基本的な SAML 構成]** セクションの **[応答 URL]** と一致していることを確認します。
    
     ![アプリ側でのシングル サインオンの構成](./media/moveittransfer-tutorial/xml.png)

5. **[Add Identity Provider (ID プロバイダーの追加)]** ボタンをクリックして新しいフェデレーション ID プロバイダーを追加します。

    ![ID プロバイダーの追加](./media/moveittransfer-tutorial/idp.png)

6. **[Browse]\(参照\)** をクリックして Azure Portal からダウンロードしたメタデータ ファイルを選び、**[Add Identity Provider]\(ID プロバイダーの追加\)** をクリックしてダウンロードしたファイルをアップロードします。

    ![SAML ID プロバイダー](./media/moveittransfer-tutorial/saml.png)

7. **[Edit Federated Identity Provider Settings (フェデレーション ID プロバイダーの設定の編集)]** ページの **[Enabled (有効)]** で **[Yes (はい)]** を選択し、**[Save (保存)]** をクリックします。

    ![フェデレーション ID プロバイダーの設定](./media/moveittransfer-tutorial/save.png)

8. **[Edit Federated Identity Provider User Settings]\(フェデレーション ID プロバイダー ユーザーの設定の編集\)** ページで、次の操作を実行します。
    
    ![フェデレーション ID プロバイダーの設定の編集](./media/moveittransfer-tutorial/attributes.png)
    
    a. **[Login name (ログイン名)]** として **[SAML NameID]** を選択します。
    
    b. **[Full name]\(フル ネーム\)** として **[Other]\(その他\)** を選び、**[Attribute name]\(属性名\)** ボックスに値「`http://schemas.microsoft.com/identity/claims/displayname`」を入力します。
    
    c. **[Email]\(電子メール\)** として **[Other]\(その他\)** を選び、**[Attribute name]\(属性名\)** ボックスに値「`http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress`」を入力します。
    
    d. **[Auto-create account on signon (サインオン時にアカウントを自動作成する)]** で **[Yes (はい)]** を選択します。
    
    e. **[保存]** ボタンをクリックします。

### <a name="create-moveit-transfer---azure-ad-integration-test-user"></a>MOVEit Transfer - Azure AD integration のテスト ユーザーの作成

このセクションの目的は、MOVEit Transfer - Azure AD integration で Britta Simon というユーザーを作成することです。 MOVEit Transfer - Azure AD integration では、Just-In-Time プロビジョニングがサポートされています。この設定は有効になっています。 このセクションでは、ユーザー側で必要な操作はありません。 ユーザーが存在しない場合は、MOVEit Transfer - Azure AD integration へのアクセスを試みたときに、新しいユーザーが自動的に作成されます。

>[!NOTE]
>ユーザーを手動で作成する必要がある場合は、[MOVEit Transfer - Azure AD integration クライアント サポート チーム](https://community.ipswitch.com/s/support)にお問い合わせください。

### <a name="test-sso"></a>SSO のテスト 

このセクションでは、次のオプションを使用して Azure AD のシングル サインオン構成をテストします。 

* Azure portal で **[このアプリケーションをテストします]** をクリックします。 これにより、ログイン フローを開始できる MOVEit Transfer - Azure AD integration のサインオン URL にリダイレクトされます。 

* MOVEit Transfer - Azure AD integration のサインオン URL に直接移動し、そこからログイン フローを開始します。

* Microsoft マイ アプリを使用することができます。 マイ アプリで [MOVEit Transfer - Azure AD integration] タイルをクリックすると、SSO を設定した MOVEit Transfer - Azure AD integration に自動的にサインインします。 マイ アプリの詳細については、[マイ アプリの概要](../user-help/my-apps-portal-end-user-access.md)に関するページを参照してください。

## <a name="next-steps"></a>次のステップ

MOVEit Transfer - Azure AD integration を構成したら、組織の機密データを流出と侵入からリアルタイムで保護するセッション制御を適用することができます。 セッション制御は、条件付きアクセスを拡張したものです。 [Microsoft Cloud App Security でセッション制御を強制する方法](/cloud-app-security/proxy-deployment-any-app)をご覧ください。