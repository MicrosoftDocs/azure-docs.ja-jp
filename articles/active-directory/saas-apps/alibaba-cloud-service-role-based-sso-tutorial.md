---
title: チュートリアル:Azure Active Directory シングル サインオン (SSO) と Alibaba Cloud Service (ロールベースの SSO) の統合 | Microsoft Docs
description: Azure Active Directory と Alibaba Cloud Service (ロールベースの SSO) の間でシングル サインオンを構成する方法について説明します。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 12/08/2020
ms.author: jeedes
ms.openlocfilehash: 6e4cb3c372b696b203d2441c74a6cafff175af47
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "98736146"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-alibaba-cloud-service-role-based-sso"></a>チュートリアル:Azure Active Directory シングル サインオン (SSO) と Alibaba Cloud Service (ロールベースの SSO) の統合

このチュートリアルでは、Alibaba Cloud Service (ロールベースの SSO) と Azure Active Directory (Azure AD) を統合する方法について説明します。 Alibaba Cloud Service (ロールベースの SSO) を Azure AD と統合すると、次のことができます。

* Alibaba Cloud Service (ロールベースの SSO) にアクセスできるユーザーを Azure AD で制御できます。
* ユーザーが自分の Azure AD アカウントで自動的に Alibaba Cloud Service (ロールベースの SSO) にサインインできるように設定できます。
* 1 つの中央サイト (Azure Portal) で自分のアカウントを管理します。

## <a name="prerequisites"></a>前提条件

開始するには、次が必要です。

* Azure AD サブスクリプション。 サブスクリプションがない場合は、[無料アカウント](https://azure.microsoft.com/free/)を取得できます。
* Alibaba Cloud Service (ロールベースの SSO) でのシングル サインオン (SSO) が有効なサブスクリプション。

## <a name="scenario-description"></a>シナリオの説明

このチュートリアルでは、テスト環境で Azure AD の SSO を構成してテストします。

* Alibaba Cloud Service (ロールベースの SSO) では、**IDP** Initiated SSO がサポートされます

## <a name="adding-alibaba-cloud-service-role-based-sso-from-the-gallery"></a>ギャラリーからの Alibaba Cloud Service (ロールベースの SSO) の追加

Azure AD への Alibaba Cloud Service (ロールベースの SSO) の統合を構成するには、ギャラリーからマネージド SaaS アプリの一覧に Alibaba Cloud Service (ロールベースの SSO) を追加する必要があります。

1. 職場または学校アカウントか、個人の Microsoft アカウントを使用して、Azure portal にサインインします。
1. 左のナビゲーション ウィンドウで **[Azure Active Directory]** サービスを選択します。
1. **[エンタープライズ アプリケーション]** に移動し、 **[すべてのアプリケーション]** を選択します。
1. 新しいアプリケーションを追加するには、 **[新しいアプリケーション]** を選択します。
1. **[ギャラリーから追加する]** セクションで、検索ボックスに「**Alibaba Cloud Service (ロールベースの SSO)** 」と入力します。
1. 結果のパネルから **[Alibaba Cloud Service (ロールベースの SSO)]** を選択し、アプリを追加します。 お使いのテナントにアプリが追加されるのを数秒待機します。
5. **Alibaba Cloud Service (ロールベースの SSO)** ページで、左のナビゲーション ウィンドウにある **[プロパティ]** をクリックし、**オブジェクト ID** をコピーし、後で使用するためにそれをコンピューターに保存します。

    ![プロパティの構成](./media/alibaba-cloud-service-role-based-sso-tutorial/Properties.png)


## <a name="configure-and-test-azure-ad-sso-for-alibaba-cloud-service-role-based-sso"></a>Alibaba Cloud Service (ロールベースの SSO) の Azure AD SSO の構成とテスト

**B.Simon** というテスト ユーザーを使用して、Alibaba Cloud Service (ロールベースの SSO) に対する Azure AD SSO を構成してテストします。 SSO が機能するためには、Azure AD ユーザーと Alibaba Cloud Service (ロールベースの SSO) の関連ユーザーとの間にリンク関係を確立する必要があります。

Alibaba Cloud Service (ロールベースの SSO) に対して Azure AD SSO を構成してテストするには、次の手順を実行します。

1. **[Azure AD SSO の構成](#configure-azure-ad-sso)** - ユーザーがこの機能を使用できるようにします。
    1. **[Azure AD のテスト ユーザーの作成](#create-an-azure-ad-test-user)** - Britta Simon で Azure AD のシングル サインオンをテストします。
    1. **[Azure AD テスト ユーザーの割り当て](#assign-the-azure-ad-test-user)** - Britta Simon が Azure AD シングル サインオンを使用できるようにします。
2. **[Alibaba Cloud Service でロールベースのシングル サインオンを構成する](#configure-role-based-single-sign-on-in-alibaba-cloud-service)** - ユーザーがこの機能を使用できるようにします。
    1. **[Alibaba Cloud Service (ロールベースの SSO) の SSO の構成](#configure-alibaba-cloud-service-role-based-sso-sso)** - アプリケーション側でシングル サインオン設定を構成します。
    1. **[Alibaba Cloud Service (ロールベースの SSO) のテスト ユーザーの作成](#create-alibaba-cloud-service-role-based-sso-test-user)** - Alibaba Cloud Service (ロールベースの SSO) で Britta Simon に対応するユーザーを作成し、Azure AD の Britta Simon にリンクさせます。
3. **[SSO のテスト](#test-sso)** - 構成が機能するかどうかを確認します。

## <a name="configure-azure-ad-sso"></a>Azure AD SSO の構成

これらの手順に従って、Azure portal で Azure AD SSO を有効にします。

1. Azure portal の **Alibaba Cloud Service (ロールベースの SSO)** アプリケーション統合ページで、 **[管理]** セクションを見つけて、 **[シングル サインオン]** を選択します。
1. **[シングル サインオン方式の選択]** ページで、 **[SAML]** を選択します。
1. **[SAML でシングル サインオンをセットアップします]** ページで、 **[基本的な SAML 構成]** の編集 (ペン) アイコンをクリックして設定を編集します。

    ![基本的な SAML 構成を編集する](common/edit-urls.png)

4. **[基本的な SAML 構成]** セクションで、**サービス プロバイダー メタデータ ファイル** がある場合は、次の手順に従います。

    a. **[メタデータ ファイルをアップロードします]** をクリックします。

    b. **フォルダー ロゴ** をクリックしてメタデータ ファイルを選択し、 **[アップロード]** をクリックします。


    >[!NOTE]
    >1. Alibaba Cloud International サイトについては、[こちら](https://signin.alibabacloud.com/saml-role/sp-metadata.xml)のリンクからサービス プロバイダーのメタデータをダウンロードしてください。
    > 1. Alibaba Cloud Service (CN) サイトについては、[こちら](https://signin.aliyun.com/saml-role/sp-metadata.xml)のリンクからサービス プロバイダーのメタデータをダウンロードしてください。

    c. メタデータ ファイルが正常にアップロードされると、**識別子** と **応答 URL** の値が、Alibaba Cloud Service (ロールベースの SSO) セクションのテキスト ボックスに自動的に設定されます。

    > [!Note]
    > **識別子** と **応答 URL** の値が自動的に設定されない場合は、要件に応じて手動で値を入力してください。

1. Alibaba Cloud Service (ロールベースの SSO) では、Azure AD にロールが構成されている必要があります。 ロール要求はあらかじめ構成されているため、自分で構成する必要はありませんが、それらを Azure AD に作成する必要があります。こちらの[記事](../develop/howto-add-app-roles-in-azure-ad-apps.md#app-roles-ui--preview)に従ってください。

1. **[SAML でシングル サインオンをセットアップします]** ページの **[SAML 署名証明書]** セクションで、 **[フェデレーション メタデータ XML]** を探して **[ダウンロード]** を選択し、証明書をダウンロードして、お使いのコンピューターに保存します。

    ![証明書のダウンロードのリンク](common/metadataxml.png)

1. **[Alibaba Cloud Service (ロールベースの SSO) のセットアップ]** セクションで、要件に基づいて適切な URL をコピーします。

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

このセクションでは、B.Simon に Alibaba Cloud Service (ロールベースの SSO) へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

1. Azure portal で **[エンタープライズ アプリケーション]** を選択し、 **[すべてのアプリケーション]** を選択します。
1. アプリケーションの一覧で **[Alibaba Cloud Service (ロールベースの SSO)]** を選択します。
1. アプリの概要ページで、 **[管理]** セクションを見つけて、 **[ユーザーとグループ]** を選択します。
1. **[ユーザーの追加]** を選択し、 **[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。
1. **[ユーザーとグループ]** タブでユーザーの一覧から u2 を選択し、 **[選択]** をクリックします。 次に、 **[割り当て]** をクリックします。

    ![Azure AD テスト ユーザーの割り当て 1](./media/alibaba-cloud-service-role-based-sso-tutorial/test01.png)

1. 割り当てられたロールを表示し、Alibaba Cloud Service (ロールベースの SSO) をテストします。

    ![Azure AD テスト ユーザーの割り当て 2](./media/alibaba-cloud-service-role-based-sso-tutorial/test02.png)

    >[!NOTE]
    >ユーザー (u2) を割り当てると、作成したロールがユーザーに自動的に関連付けられます。 複数のロールを作成した場合、必要に応じて、適切なロールをユーザーに関連付ける必要があります。 ロールベースの SSO を Azure AD から複数の Alibaba Cloud アカウントに実装する場合、前の手順を繰り返します。

## <a name="configure-role-based-single-sign-on-in-alibaba-cloud-service"></a>Alibaba Cloud Service でロールベースのシングル サインオンを構成する

1. Account1 を使用し、Alibaba Cloud [RAM コンソール](https://account.alibabacloud.com/login/login.htm?oauth_callback=https%3A%2F%2Fram.console.aliyun.com%2F%3Fspm%3Da2c63.p38356.879954.8.7d904e167h6Yg9)にサインインします。

2. 左側のナビゲーション ウィンドウで **[SSO]** を選択します。

3. **[Role-based SSO]\(ロールベースの SSO\)** タブで **[Create IdP]\(IdP の作成\)** をクリックします。

4. 表示されたページで [IdP Name]\(IdP 名\) フィールドに「`AAD`」と入力し、 **[注]** フィールドに説明を入力し、 **[アップロード]** をクリックし、前にダウンロードしたフェデレーション メタデータ ファイルをアップロードし、 **[OK]** をクリックします。

5. IdP が正常に作成されたら、 **[Create RAM Role]\(RAM ロールの作成\)** をクリックします。

6. **[RAM Role Name]\(RAM ロール名\)** フィールドに「`AADrole`」と入力し、 **[Select IdP]\(IdP の選択\)** ドロップダウン リストから `AAD` を選択し、[OK] をクリックします。

    >[!NOTE]
    >必要に応じて、ロールにアクセス許可を付与できます。 IdP と該当するロールを作成したら、後で使用するために IdP とロールの ARN を保存することをお勧めします。 ARN は IdP 情報ページとロール情報ページで入手できます。

7. Alibaba Cloud RAM ロール (AADrole) と Azure AD ユーザー (u2) を関連付けます。RAM ロールと Azure AD ユーザーを関連付けるには、次の手順で Azure AD でロールを作成する必要があります。

    a. [Microsoft Graph Explorer](https://developer.microsoft.com/graph/graph-explorer) にサインオンします。

    b. **[アクセス許可の変更]** をクリックし、ロールの作成に必要なアクセス許可を取得します。

    ![Graph の構成 1](./media/alibaba-cloud-service-role-based-sso-tutorial/graph01.png)

    c. 次の画像のように、一覧から次のアクセス許可を選択し、 **[アクセス許可の変更]** をクリックします。

    ![Graph の構成 2](./media/alibaba-cloud-service-role-based-sso-tutorial/graph02.png)

    >[!NOTE]
    >アクセス許可が付与されたら、Graph Explorer に再度ログオンします。

    d. Graph Explorer ページで、最初のドロップダウン リストから **[GET]** を選択し、2 つ目のドロップダウン リストから **[ベータ]** を選択します。 ドロップダウン リストの横にあるフィールドに「`https://graph.microsoft.com/beta/servicePrincipals`」と入力し、 **[クエリの実行]** を実行します。

    ![Graph の構成 3](./media/alibaba-cloud-service-role-based-sso-tutorial/graph03.png)

    >[!NOTE]
    >複数のディレクトリを使用している場合、クエリのフィールドに「`https://graph.microsoft.com/beta/contoso.com/servicePrincipals`」と入力できます。

    e. **[Response Preview]\(応答プレビュー\)** セクションで、後で使用するために "Service Principal" から appRoles プロパティを抽出します。

    ![Graph の構成 4](./media/alibaba-cloud-service-role-based-sso-tutorial/graph05.png)

    >[!NOTE]
    >クエリのフィールドに「`https://graph.microsoft.com/beta/servicePrincipals/<objectID>`」と入力することで appRoles プロパティを見つけることができます。 `objectID` は Azure AD **プロパティ** ページからコピーしたオブジェクト ID であることにご注目ください。

    f. Graph Explorer に戻り、メソッドを **GET** から **PATCH** に変更し、 **[要求本文]** セクションに次のコンテンツを貼り付け、 **[クエリの実行]** をクリックします。
    ```
    { 
      "appRoles": [
        { 
          "allowedMemberTypes":[
            "User"
          ],
          "description": "msiam_access",
          "displayName": "msiam_access",
          "id": "41be2db8-48d9-4277-8e86-f6d22d35****",
          "isEnabled": true,
          "origin": "Application",
          "value": null
        },
        { "allowedMemberTypes": [
            "User"
        ],
        "description": "Admin,AzureADProd",
        "displayName": "Admin,AzureADProd",
        "id": "68adae10-8b6b-47e6-9142-6476078cdbce",
        "isEnabled": true,
        "origin": "ServicePrincipal",
        "value": "acs:ram::187125022722****:role/aadrole,acs:ram::187125022722****:saml-provider/AAD"
        }
      ]
    }
    ```
    > [!NOTE]
    > `value` は、RAM コンソールで作成した IdP とロールの ARN です。 ここでは、必要に応じて、複数のロールを追加できます。 Azure AD では、SAML 応答の要求値として、これらのロールの値を送信します。 ただし、パッチ操作では、`msiam_access` 部分の後にのみ、新しいロールを追加できます。 作成過程を速やかに進めるため、GUID Generator など、ID ジェネレーターを使用してリアルタイムで ID を生成することをお勧めします。

    g. 必要なロールで "Service Principal" にパッチを適用したら、チュートリアルの「**Azure AD テスト ユーザーの割り当て**」セクションの手順に従い、Azure AD ユーザー (u2) とロールを関連付けます。

### <a name="configure-alibaba-cloud-service-role-based-sso-sso"></a>Alibaba Cloud Service (ロールベースの SSO) の SSO の構成

**Alibaba Cloud Service (ロールベースの SSO)** 側でシングル サインオンを構成するには、ダウンロードした **フェデレーション メタデータ XML** と Azure portal からコピーした適切な URL を [Alibaba Cloud Service (ロールベースの SSO) サポート チーム](https://www.aliyun.com/service/)に送信する必要があります。 サポート チームはこれを設定して、SAML SSO 接続が両方の側で正しく設定されるようにします。

### <a name="create-alibaba-cloud-service-role-based-sso-test-user"></a>Alibaba Cloud Service (ロールベースの SSO) のテスト ユーザーの作成

このセクションでは、Alibaba Cloud Service (ロールベースの SSO) で Britta Simon というユーザーを作成します。 [Alibaba Cloud Service (ロールベースの SSO) サポート チーム](https://www.aliyun.com/service/)と連携し、Alibaba Cloud Service (ロールベースの SSO) プラットフォームにユーザーを追加してください。 シングル サインオンを使用する前に、ユーザーを作成し、有効化する必要があります。

## <a name="test-sso"></a>SSO のテスト 

上記の構成が完了したら、次の手順を実行し、Alibaba Cloud Service (ロールベースの SSO) をテストします。

1. Azure portal で、 **[Alibaba Cloud Service (ロールベースの SSO)]** ページに移動し、 **[シングル サインオン]** を選択し、 **[テスト]** をクリックします。

    ![テストの構成 1](./media/alibaba-cloud-service-role-based-sso-tutorial/test03.png)

2. **[現在のユーザーとしてサインイン]** をクリックします。

    ![テストの構成 2](./media/alibaba-cloud-service-role-based-sso-tutorial/test04.png)

3. アカウント選択ページで、u2 を選択します。

    ![テストの構成 3](./media/alibaba-cloud-service-role-based-sso-tutorial/test05.png)

4. 次のページが表示され、ロールベースの SSO に成功したことが示されます。

    ![テストの構成 4](./media/alibaba-cloud-service-role-based-sso-tutorial/test06.png)

## <a name="next-steps"></a>次の手順

Alibaba Cloud Service (ロールベースの SSO) を構成したら、組織の機密データを流出と侵入からリアルタイムで保護するセッション制御を適用することができます。 セッション制御は、条件付きアクセスを拡張したものです。 [Microsoft Cloud App Security でセッション制御を強制する方法](/cloud-app-security/proxy-deployment-any-app)をご覧ください。
