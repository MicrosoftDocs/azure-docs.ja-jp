---
title: チュートリアル:Azure Active Directory と Alibaba Cloud Service (ロールベースの SSO) の統合 | Microsoft Docs
description: Azure Active Directory と Alibaba Cloud Service (ロールベースの SSO) の間でシングル サインオンを構成する方法について説明します。
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 3667841e-acfc-4490-acf5-80d9ca3e71e8
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 05/02/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: bdfd19d9a0e928e26ad6f01ba4b9c3f493aacb0c
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/13/2019
ms.locfileid: "67107160"
---
# <a name="tutorial-azure-active-directory-integration-with-alibaba-cloud-service-role-based-sso"></a>チュートリアル:Azure Active Directory と Alibaba Cloud Service (ロールベースの SSO) の統合

このチュートリアルでは、Alibaba Cloud Service (ロールベースの SSO) と Azure Active Directory (Azure AD) を統合する方法について説明します。
Alibaba Cloud Service (ロールベースの SSO) と Azure AD の統合には、次の利点があります。

* Alibaba Cloud Service (ロールベースの SSO) にアクセスする Azure AD ユーザーを制御できます。
* ユーザーが自分の Azure AD アカウントで自動的に Alibaba Cloud Service (ロールベースの SSO) にサインイン (シングル サインオン) できるようにします。
* 1 つの中央サイト (Azure Portal) でアカウントを管理できます。

SaaS アプリと Azure AD の統合の詳細については、「 [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)」を参照してください。
Azure サブスクリプションをお持ちでない場合は、開始する前に[無料アカウントを作成](https://azure.microsoft.com/free/)してください。

## <a name="prerequisites"></a>前提条件

Alibaba Cloud Service (ロールベースの SSO) と Azure AD の統合を構成するには、次の項目が必要です。

* Azure AD サブスクリプション。 Azure AD の環境がない場合は、[無料アカウント](https://azure.microsoft.com/free/)を取得できます
* Alibaba Cloud Service (ロールベースの SSO) でのシングル サインオンが有効なサブスクリプション

## <a name="scenario-description"></a>シナリオの説明

このチュートリアルでは、テスト環境で Azure AD のシングル サインオンを構成してテストします。

* Alibaba Cloud Service (ロールベースの SSO) では、**IDP** Initiated SSO がサポートされます

## <a name="adding-alibaba-cloud-service-role-based-sso-from-the-gallery"></a>ギャラリーからの Alibaba Cloud Service (ロールベースの SSO) の追加

Azure AD への Alibaba Cloud Service (ロールベースの SSO) の統合を構成するには、ギャラリーからマネージド SaaS アプリの一覧に Alibaba Cloud Service (ロールベースの SSO) を追加する必要があります。

**ギャラリーから Alibaba Cloud Service (ロールベースの SSO) を追加するには、次の手順に従います。**

1. **[Azure Portal](https://portal.azure.com)** の左側のナビゲーション ウィンドウで、 **[Azure Active Directory]** アイコンをクリックします。

    ![Azure Active Directory のボタン](common/select-azuread.png)

2. **[エンタープライズ アプリケーション]** に移動し、 **[すべてのアプリケーション]** オプションを選択します。

    ![[エンタープライズ アプリケーション] ブレード](common/enterprise-applications.png)

3. 新しいアプリケーションを追加するには、ダイアログの上部にある **[新しいアプリケーション]** をクリックします。

    ![[新しいアプリケーション] ボタン](common/add-new-app.png)

4. 検索ボックスに「**Alibaba Cloud Service (ロールベースの SSO)** 」と入力して、結果パネルから **[Alibaba Cloud Service (ロールベースの SSO)]** を選択し、 **[追加]** をクリックしてアプリケーションを追加します。

    ![結果リストの Alibaba Cloud Service (ロールベースの SSO)](common/search-new-app.png)

5. **Alibaba Cloud Service (ロールベースの SSO)** ページで、左のナビゲーション ウィンドウにある **[プロパティ]** をクリックし、**オブジェクト ID** をコピーし、後で使用するためにそれをコンピューターに保存します。

    ![プロパティの構成](./media/alibaba-cloud-service-role-based-sso-tutorial/Properties.png)
    
## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成とテスト

このセクションでは、**Britta Simon** というテスト ユーザーに基づいて、Alibaba Cloud Service (ロールベースの SSO) で Azure AD のシングル サインオンを構成し、テストします。
シングル サインオンを機能させるには、Azure AD ユーザーと Alibaba Cloud Service (ロールベースの SSO) の関連ユーザーとの間にリンク関係が確立されている必要があります。

Alibaba Cloud Service (ロールベースの SSO) で Azure AD のシングル サインオンを構成してテストするには、次の構成要素を完了する必要があります。

1. **[Azure AD シングル サインオンの構成](#configure-azure-ad-single-sign-on)** - ユーザーがこの機能を使用できるようにします。
2. **[Alibaba Cloud Service でロールベースのシングル サインオンを構成する](#configure-role-based-single-sign-on-in-alibaba-cloud-service)** - ユーザーがこの機能を使用できるようにします。
2. **[Alibaba Cloud Service (ロールベースの SSO) シングル サインオンの構成](#configure-alibaba-cloud-service-role-based-sso-single-sign-on)** - アプリケーション側でシングル サインオン設定を構成します。
3. **[Azure AD のテスト ユーザーの作成](#create-an-azure-ad-test-user)** - Britta Simon で Azure AD のシングル サインオンをテストします。
4. **[Azure AD テスト ユーザーの割り当て](#assign-the-azure-ad-test-user)** - Britta Simon が Azure AD シングル サインオンを使用できるようにします。
5. **[Alibaba Cloud Service (ロールベースの SSO) のテスト ユーザーの作成](#create-alibaba-cloud-service-role-based-sso-test-user)** - Alibaba Cloud Service (ロールベースの SSO) で Britta Simon に対応するユーザーを作成し、Azure AD の Britta Simon にリンクさせます。
6. **[シングル サインオンのテスト](#test-single-sign-on)** - 構成が機能するかどうかを確認します。

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成

このセクションでは、Azure portal 上で Azure AD のシングル サインオンを有効にします。

Alibaba Cloud Service (ロールベースの SSO) で Azure AD シングル サインオンを構成するには、次の手順に従います。

1. [Azure portal](https://portal.azure.com/) の **Alibaba Cloud Service (ロールベースの SSO)** アプリケーション統合ページで、 **[シングル サインオン]** を選択します。

    ![シングル サインオン構成のリンク](common/select-sso.png)

2. **[シングル サインオン方式の選択]** ダイアログで、 **[SAML/WS-Fed]** モードを選択して、シングル サインオンを有効にします。

    ![シングル サインオン選択モード](common/select-saml-option.png)

3. **[SAML でシングル サインオンをセットアップします]** ページで、 **[編集]** アイコンをクリックして **[基本的な SAML 構成]** ダイアログを開きます。

    ![基本的な SAML 構成を編集する](common/edit-urls.png)

4. **[基本的な SAML 構成]** セクションで、**サービス プロバイダー メタデータ ファイル**がある場合は、次の手順に従います。

    >[!NOTE]
    >この [URL](https://signin.alibabacloud.com/saml-role/sp-metadata.xml) からサービス プロバイダー メタデータが届きます

    a. **[メタデータ ファイルをアップロードします]** をクリックします。

    ![image](common/upload-metadata.png)

    b. **フォルダー ロゴ**をクリックしてメタデータ ファイルを選択し、 **[アップロード]** をクリックします。

    ![image](common/browse-upload-metadata.png)

    c. メタデータ ファイルが正常にアップロードされると、**識別子**と**応答 URL** の値が、Alibaba Cloud Service (ロールベースの SSO) セクションのテキスト ボックスに自動的に設定されます。

    ![image](common/idp-intiated.png)

    > [!Note]
    > **識別子**と**応答 URL** の値が自動的に設定されない場合は、要件に応じて手動で値を入力してください。

5. Alibaba Cloud Service (ロールベースの SSO) アプリケーションは特定の形式の SAML アサーションを予測しているため、SAML トークン属性の構成にカスタム属性マッピングを追加する必要があります。 次のスクリーンショットには、既定の属性一覧が示されています。 **[編集]** アイコンをクリックして、 **[ユーザー属性]** ダイアログを開きます。

    ![image](common/edit-attribute.png)

6. その他に、Alibaba Cloud Service (ロールベースの SSO) アプリケーションでは、いくつかの属性が SAML 応答で返されることが想定されています。 **[ユーザー属性]** ダイアログの **[ユーザー要求]** セクションで、以下の手順を実行して、以下の表のように SAML トークン属性を追加します。

    | Name | 名前空間 | ソース属性|
    | ---------------| ------------| --------------- |
    | Role | https:\//www.aliyun.com/SAML-Role/Attribute | user.assignedroles |
    | RoleSessionName | https:\//www.aliyun.com/SAML-Role/Attribute | user.userprincipalname |

    > [!NOTE]
    > Azure AD で**役割**を構成する方法については、[ここ](https://docs.microsoft.com/azure/active-directory/develop/active-directory-enterprise-app-role-management)をクリックしてください

    a. **[新しい要求の追加]** をクリックして **[ユーザー要求の管理]** ダイアログを開きます。

    ![image](common/new-save-attribute.png)

    ![image](common/new-attribute-details.png)

    b. **[名前]** ボックスに、その行に対して表示される属性名を入力します。

    c. **[名前空間]** は空白のままにします。

    d. [ソース] として **[属性]** を選択します。

    e. **[ソース属性]** の一覧から、その行に表示される属性値を入力します。

    f. **[Save]** をクリックします。

7. **[SAML でシングル サインオンをセットアップします]** ページの **[SAML 署名証明書]** セクションで、 **[ダウンロード]** をクリックして、要件のとおりに指定したオプションから**フェデレーション メタデータ XML** をダウンロードして、お使いのコンピューターに保存します。

    ![証明書のダウンロードのリンク](common/metadataxml.png)

8. **[Alibaba Cloud Service (ロールベースの SSO) のセットアップ]** セクションで、要件に従って適切な URL をコピーします。

    ![構成 URL のコピー](common/copy-configuration-urls.png)

    a. ログイン URL

    b. Azure AD 識別子

    c. ログアウト URL

### <a name="configure-role-based-single-sign-on-in-alibaba-cloud-service"></a>Alibaba Cloud Service でロールベースのシングル サインオンを構成する

1. Account1 を使用し、Alibaba Cloud [RAM コンソール](https://account.alibabacloud.com/login/login.htm?oauth_callback=https%3A%2F%2Fram.console.aliyun.com%2F%3Fspm%3Da2c63.p38356.879954.8.7d904e167h6Yg9)にサインインします。

2. 左側のナビゲーション ウィンドウで **[SSO]** を選択します。

3. **[Role-based SSO]\(ロールベースの SSO\)** タブで **[Create IdP]\(IdP の作成\)** をクリックします。

4. 表示されたページで [IdP Name]\(IdP 名\) フィールドに「`AAD`」と入力し、 **[注]** フィールドに説明を入力し、 **[アップロード]** をクリックし、前にダウンロードしたフェデレーション メタデータ ファイルをアップロードし、 **[OK]** をクリックします。

5. IdP が正常に作成されたら、 **[Create RAM Role]\(RAM ロールの作成\)** をクリックします。

6. **[RAM Role Name]\(RAM ロール名\)** フィールドに「`AADrole`」と入力し、 **[Select IdP]\(IdP の選択\)** ドロップダウン リストから `AAD` を選択し、[OK] をクリックします。

    >[!NOTE]
    >必要に応じて、ロールにアクセス許可を付与できます。 IdP と該当するロールを作成したら、後で使用するために IdP とロールの ARN を保存することをお勧めします。 ARN は IdP 情報ページとロール情報ページで入手できます。

7. Alibaba Cloud RAM ロール (AADrole) と Azure AD ユーザー (u2) を関連付けます。RAM ロールと Azure AD ユーザーを関連付けるには、次の手順で Azure AD でロールを作成する必要があります。

    a. [Azure AD Graph Explorer](https://developer.microsoft.com/graph/graph-explorer?spm=a2c63.p38356.879954.9.7d904e167h6Yg9) にサインオンします。

    b. **[アクセス許可の変更]** をクリックし、ロールの作成に必要なアクセス許可を取得します。

    ![Graph の構成](./media/alibaba-cloud-service-role-based-sso-tutorial/graph01.png)

    c. 次の画像のように、一覧から次のアクセス許可を選択し、 **[アクセス許可の変更]** をクリックします。

    ![Graph の構成](./media/alibaba-cloud-service-role-based-sso-tutorial/graph02.png)

    >[!NOTE]
    >アクセス許可が付与されたら、Graph Explorer に再度ログオンします。

    d. Graph Explorer ページで、最初のドロップダウン リストから **[GET]** を選択し、2 つ目のドロップダウン リストから **[ベータ]** を選択します。 ドロップダウン リストの横にあるフィールドに「`https://graph.microsoft.com/beta/servicePrincipals`」と入力し、 **[クエリの実行]** を実行します。

    ![Graph の構成](./media/alibaba-cloud-service-role-based-sso-tutorial/graph03.png)

    >[!NOTE]
    >複数のディレクトリを使用している場合、クエリのフィールドに「`https://graph.microsoft.com/beta/contoso.com/servicePrincipals`」と入力できます。

    e. **[Response Preview]\(応答プレビュー\)** セクションで、後で使用するために "Service Principal" から appRoles プロパティを抽出します。

    ![Graph の構成](./media/alibaba-cloud-service-role-based-sso-tutorial/graph05.png)

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

### <a name="configure-alibaba-cloud-service-role-based-sso-single-sign-on"></a>Alibaba Cloud Service (ロールベースの SSO) シングル サインオンの構成

**Alibaba Cloud Service (ロールベースの SSO)** 側でシングル サインオンを構成するには、ダウンロードした**フェデレーション メタデータ XML** と Azure portal からコピーした適切な URL を [Alibaba Cloud Service (ロールベースの SSO) サポート チーム](https://www.aliyun.com/service/)に送信する必要があります。 サポート チームはこれを設定して、SAML SSO 接続が両方の側で正しく設定されるようにします。

### <a name="create-an-azure-ad-test-user"></a>Azure AD のテスト ユーザーの作成 

このセクションの目的は、Azure Portal で Britta Simon というテスト ユーザーを作成することです。

1. Azure portal の左側のウィンドウで、 **[Azure Active Directory]** 、 **[ユーザー]** 、 **[すべてのユーザー]** の順に選択します。

    ![[ユーザーとグループ] と [すべてのユーザー] リンク](common/users.png)

2. 画面の上部にある **[新しいユーザー]** を選択します。

    ![[新しいユーザー] ボタン](common/new-user.png)

3. [ユーザーのプロパティ] で、次の手順を実行します。

    ![[ユーザー] ダイアログ ボックス](common/user-properties.png)

    a. **[名前]** フィールドに「**BrittaSimon**」と入力します。
  
    b. **[ユーザー名]** フィールドに「`brittasimon@yourcompanydomain.extension`」と入力します。 たとえば、BrittaSimon@contoso.com のように指定します。

    c. **[パスワードを表示]** チェック ボックスをオンにし、[パスワード] ボックスに表示された値を書き留めます。

    d. **Create** をクリックしてください。

### <a name="assign-the-azure-ad-test-user"></a>Azure AD テスト ユーザーの割り当て

このセクションでは、Britta Simon に Alibaba Cloud Service (ロールベースの SSO) へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

1. Azure portal で **[エンタープライズ アプリケーション]** を選択し、 **[すべてのアプリケーション]** を選択してから、 **[Alibaba Cloud Service (ロールベースの SSO)]** を選択します。

    ![[エンタープライズ アプリケーション] ブレード](common/enterprise-applications.png)

2. アプリケーションの一覧で **[Alibaba Cloud Service (ロールベースの SSO)]** を選択します。

    ![アプリケーションの一覧の Alibaba Cloud Service (ロールベースの SSO) リンク](common/all-applications.png)

3. 左側のメニューで **[ユーザーとグループ]** を選びます。

    ![[ユーザーとグループ] リンク](common/users-groups-blade.png)

4. **[ユーザーの追加]** をクリックし、 **[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。

    ![[割り当ての追加] ウィンドウ](common/add-assign-user.png)

5. **[ユーザーとグループ]** タブでユーザーの一覧から u2 を選択し、 **[選択]** をクリックします。 次に、 **[割り当て]** をクリックします。

    ![テストの構成](./media/alibaba-cloud-service-role-based-sso-tutorial/test01.png)

6. 割り当てられたロールを表示し、Alibaba Cloud Service (ロールベースの SSO) をテストします。

    ![テストの構成](./media/alibaba-cloud-service-role-based-sso-tutorial/test02.png)

    >[!NOTE]
    >ユーザー (u2) を割り当てると、作成したロールがユーザーに自動的に関連付けられます。 複数のロールを作成した場合、必要に応じて、適切なロールをユーザーに関連付ける必要があります。 ロールベースの SSO を Azure AD から複数の Alibaba Cloud アカウントに実装する場合、前の手順を繰り返します。

### <a name="create-alibaba-cloud-service-role-based-sso-test-user"></a>Alibaba Cloud Service (ロールベースの SSO) のテスト ユーザーの作成

このセクションでは、Alibaba Cloud Service (ロールベースの SSO) で Britta Simon というユーザーを作成します。 [Alibaba Cloud Service (ロールベースの SSO) サポート チーム](https://www.aliyun.com/service/)と連携し、Alibaba Cloud Service (ロールベースの SSO) プラットフォームにユーザーを追加してください。 シングル サインオンを使用する前に、ユーザーを作成し、有効化する必要があります。

### <a name="test-single-sign-on"></a>シングル サインオンのテスト 

上記の構成が完了したら、次の手順を実行し、Alibaba Cloud Service (ロールベースの SSO) をテストします。

1. Azure portal で、 **[Alibaba Cloud Service (ロールベースの SSO)]** ページに移動し、 **[シングル サインオン]** を選択し、 **[テスト]** をクリックします。

    ![テストの構成](./media/alibaba-cloud-service-role-based-sso-tutorial/test03.png)

2. **[現在のユーザーとしてサインイン]** をクリックします。

    ![テストの構成](./media/alibaba-cloud-service-role-based-sso-tutorial/test04.png)

3. アカウント選択ページで、u2 を選択します。

    ![テストの構成](./media/alibaba-cloud-service-role-based-sso-tutorial/test05.png)

4. 次のページが表示され、ロールベースの SSO に成功したことが示されます。

    ![テストの構成](./media/alibaba-cloud-service-role-based-sso-tutorial/test06.png)

## <a name="additional-resources"></a>その他のリソース

- [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Azure Active Directory の条件付きアクセスとは](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

