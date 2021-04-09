---
title: エンタープライズ Azure AD アプリ用のロール要求を構成する | Azure
titleSuffix: Microsoft identity platform
description: Azure Active Directory でエンタープライズ アプリケーション用の SAML トークン内に発行されるロール要求を構成する方法について説明します
services: active-directory
author: jeevansd
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.custom: aaddev
ms.workload: identity
ms.topic: how-to
ms.date: 02/15/2021
ms.author: jeedes
ms.openlocfilehash: 3c1b4b3d57bdce4a66adb35717f46d8989ee2453
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "101648817"
---
# <a name="how-to-configure-the-role-claim-issued-in-the-saml-token-for-enterprise-applications"></a>方法:エンタープライズ アプリケーション用の SAML トークン内に発行されるロール要求を構成する

Azure Active Directory (Azure AD) を使用して、アプリを承認した後に受信する応答トークン内のロール要求の要求の種類をカスタマイズできます。

## <a name="prerequisites"></a>前提条件

- ディレクトリのセットアップを済ませた Azure AD サブスクリプション。
- シングル サインオン (SSO) が有効なサブスクリプション。 アプリケーションで SSO を構成する必要があります。

> [!NOTE]
> この記事では、Azure AD の API を使用して、サービス プリンシパルでアプリケーション ロールを作成、更新、削除する方法について説明します。 アプリ ロールに新しいユーザー インターフェイスを使用する場合は、[こちら](./howto-add-app-roles-in-azure-ad-apps.md)で詳細を確認してください。

## <a name="when-to-use-this-feature"></a>この機能を使用する状況

Azure AD から返される SAML 応答でカスタム ロールを想定するアプリケーションの場合は、この機能を使用します。 ロールは、必要な数だけ作成できます。

## <a name="create-roles-for-an-application"></a>アプリケーションのロールを作成する

1. <a href="https://portal.azure.com/" target="_blank">Azure Portal</a> の左側のウィンドウで、**Azure Active Directory** アイコンを選択します。

    ![Azure Active Directory のアイコン][1]

2. **[エンタープライズ アプリケーション]** を選択します。 次に、 **[すべてのアプリケーション]** を選択します。

    ![[エンタープライズ アプリケーション] ウィンドウ][2]

3. 新しいアプリケーションを追加するには、ダイアログ ボックスの上部にある **[新しいアプリケーション]** ボタンを選びます。

    ![[新しいアプリケーション] ボタン][3]

4. 検索ボックスにアプリケーションの名前を入力し、結果ウィンドウからアプリケーションを選択します。 **[追加]** をクリックしてアプリケーションを追加します。

    ![結果リストのアプリケーション](./media/active-directory-enterprise-app-role-management/tutorial_app_addfromgallery.png)

5. アプリケーションが追加されたら、 **[プロパティ]** ページに移動し、オブジェクト ID をコピーします。

    ![[プロパティ] ページ](./media/active-directory-enterprise-app-role-management/tutorial_app_properties.png)

6. 別のウィンドウで [Microsoft Graph Explorer](https://developer.microsoft.com/graph/graph-explorer) を開き、以下の手順を実行します。

    1. テナントのグローバル管理者または共同管理者の資格情報を使用して、Graph Explorer サイトにサインインします。

    1. ロールを作成するための十分なアクセス許可が必要です。 **[アクセス許可の変更]** を選択して、必要なアクセス許可を取得します。

        ![[アクセス許可の変更] ボタン](./media/active-directory-enterprise-app-role-management/graph-explorer-new9.png)

        > [!NOTE]
        > クラウド アプリ管理者とアプリ管理者のロールは、このシナリオでは使用できません。ディレクトリの読み取りと書き込みのために全体管理者のアクセス許可が必要になるためです。

    1. 一覧から次のアクセス許可を選択し (まだ持っていない場合)、 **[アクセス許可の変更]** を選択します。

        ![アクセス許可の一覧と [アクセス許可の変更] ボタン](./media/active-directory-enterprise-app-role-management/graph-explorer-new10.png)

    1. 同意を受け入れます。 システムにもう一度ログインされます。

    1. バージョンを **ベータ版** に変更し、次のクエリを使用してテナントからサービス プリンシパルの一覧を取得します。

        `https://graph.microsoft.com/beta/servicePrincipals`

        複数のディレクトリを使用している場合は、次のパターンに従います。`https://graph.microsoft.com/beta/contoso.com/servicePrincipals`

        ![サービス プリンシパルをフェッチするためのクエリを表示している Graph Explorer ダイアログ ボックス](./media/active-directory-enterprise-app-role-management/graph-explorer-new1.png)

    1. フェッチしたサービス プリンシパルの一覧から、変更する必要があるものを取得します。 Ctrl + F を使用して、一覧に示されたすべてのサービス プリンシパルからアプリケーションを検索することもできます。 [プロパティ] ページからコピーした **オブジェクト ID** を検索し、次のクエリを使用してサービス プリンシパルを取得します。

        `https://graph.microsoft.com/beta/servicePrincipals/<objectID>`

        ![変更する必要があるサービス プリンシパルを取得するためのクエリ](./media/active-directory-enterprise-app-role-management/graph-explorer-new2.png)

    1. サービス プリンシパル オブジェクトから **appRoles** プロパティを抽出します。

        ![appRoles プロパティの詳細](./media/active-directory-enterprise-app-role-management/graph-explorer-new3.png)

        カスタム アプリ (Azure Marketplace 以外のアプリ) を使用している場合は、2 つの既定のロール (user と msiam_access) が表示されます。 Marketplace アプリの場合、既定のロールは msiam_access のみとなります。 既定のロールに変更を加える必要はありません。

    1. アプリケーションの新しいロールを生成します。

        次の JSON は、**appRoles** オブジェクトの例です。 同様のオブジェクトを作成して、アプリケーションで必要なロールを追加します。

        ```json
        {
          "appRoles": [
            {
               "allowedMemberTypes": [
                  "User"
                ],
                "description": "msiam_access",
                "displayName": "msiam_access",
                "id": "b9632174-c057-4f7e-951b-be3adc52bfe6",
                "isEnabled": true,
                "origin": "Application",
                "value": null
            },
            {
                "allowedMemberTypes": [
                    "User"
                ],
                "description": "Administrators Only",
                "displayName": "Admin",
                "id": "4f8f8640-f081-492d-97a0-caf24e9bc134",
                "isEnabled": true,
                "origin": "ServicePrincipal",
                "value": "Administrator"
            }
         ]
        }
        ```

        パッチ操作では、msiam_access の後にのみ、新しいロールを追加できます。 また、組織が必要とする数のロールを追加できます。 Azure AD では、SAML 応答の要求値として、これらのロールの値を送信します。 新しいロールの ID の GUID 値を生成するには、[こちら](https://www.guidgenerator.com/)のような Web ツールを使用します。

    1. Graph Explorer に戻り、メソッドを **GET** から **PATCH** に変更します。 前の例に示されているように **appRoles** プロパティを更新して、必要なロールを持つようにサービス プリンシパル オブジェクトを修正します。 **[クエリの実行]** をクリックして、パッチ操作を実行します。 成功メッセージにより、ロールの作成が確認されます。

        ![Patch 操作と成功メッセージ](./media/active-directory-enterprise-app-role-management/graph-explorer-new11.png)

1. より多くのロールでサービス プリンシパルを修正したら、対応するロールにユーザーを割り当てることができます。 ポータルに移動し、アプリケーションを参照することで、ユーザーを割り当てることができます。 **[ユーザーとグループ]** タブを選択します。このタブには、アプリに既に割り当てられているユーザーとグループがすべて表示されます。 新しいロールに新しいユーザーを追加できます。 また、既存のユーザーを選択し、 **[編集]** を選択してロールを変更することもできます。

    ![[ユーザーとグループ] タブ](./media/active-directory-enterprise-app-role-management/graph-explorer-new5.png)

    ユーザーにロールを割り当てるには、新しいロールを選択し、ページの下部にある **[割り当て]** ボタンを選択します。

    ![[割り当ての編集] ウィンドウと [ロールの選択] ウィンドウ](./media/active-directory-enterprise-app-role-management/graph-explorer-new6.png)

    
    新しいロールを表示するには、Azure portal でセッションを更新します。

1. **[属性]** テーブルを更新して、ロール要求のカスタマイズされたマッピングを定義します。

1. **[ユーザー属性]** ダイアログの **[ユーザー要求]** セクションで、以下の手順を実行して、以下の表のように SAML トークン属性を追加します。

    | 属性名 | 属性値 |
    | -------------- | ----------------|
    | ロール名  | user.assignedroles |

    ロール要求の値が null の場合、Azure AD はトークンでこの値を送信しません。これは、設計上の既定値です。

    1. **[編集]** アイコンをクリックして **[ユーザー属性と要求]** ダイアログを開きます。

        ![[ユーザー属性とクレーム] ダイアログ ボックスを開くために使用される [編集] アイコンが強調表示されているスクリーンショット。](./media/active-directory-enterprise-app-role-management/editattribute.png)

    1. **[ユーザー要求の管理]** ダイアログで、 **[新しい要求の追加]** をクリックして、SAML トークン属性を追加します。

        ![[属性の追加] ボタン](./media/active-directory-enterprise-app-role-management/tutorial_attribute_04.png)

        ![[属性の追加] ウィンドウ](./media/active-directory-enterprise-app-role-management/tutorial_attribute_05.png)

    1. **[名前]** ボックスに、必要に応じて属性名を入力します。 この例では、要求名として **ロールの名前** を使用します。

    1. **[名前空間]** ボックスは空白のままにします。

    1. **[ソース属性]** の一覧から、その行に表示される属性値を入力します。

    1. **[保存]** を選択します。

10. ID プロバイダーによって開始されるシングル サインオンでアプリケーションをテストするには、[アクセス パネル](https://myapps.microsoft.com)にサインインし、アプリケーション タイルを選択します。 SAML トークンには、指定した要求の名前を持つ、そのユーザーに割り当てられているすべてのロールが表示されるはずです。

## <a name="update-an-existing-role"></a>既存のロールを更新する

既存のロールを更新するには、以下の手順を実行します。

1. [Microsoft Graph Explorer](https://developer.microsoft.com/graph/graph-explorer) を開きます。

1. テナントのグローバル管理者または共同管理者の資格情報を使用して、Graph Explorer サイトにサインインします。

1. バージョンを **ベータ版** に変更し、次のクエリを使用してテナントからサービス プリンシパルの一覧を取得します。

    `https://graph.microsoft.com/beta/servicePrincipals`

    複数のディレクトリを使用している場合は、次のパターンに従います。`https://graph.microsoft.com/beta/contoso.com/servicePrincipals`

    ![サービス プリンシパルをフェッチするためのクエリを表示している Graph Explorer ダイアログ ボックス](./media/active-directory-enterprise-app-role-management/graph-explorer-new1.png)

1. フェッチしたサービス プリンシパルの一覧から、変更する必要があるものを取得します。 Ctrl + F を使用して、一覧に示されたすべてのサービス プリンシパルからアプリケーションを検索することもできます。 [プロパティ] ページからコピーした **オブジェクト ID** を検索し、次のクエリを使用してサービス プリンシパルを取得します。

    `https://graph.microsoft.com/beta/servicePrincipals/<objectID>`

    ![変更する必要があるサービス プリンシパルを取得するためのクエリ](./media/active-directory-enterprise-app-role-management/graph-explorer-new2.png)

1. サービス プリンシパル オブジェクトから **appRoles** プロパティを抽出します。

    ![appRoles プロパティの詳細](./media/active-directory-enterprise-app-role-management/graph-explorer-new3.png)

1. 既存のロールを更新するには、以下の手順を実行します。

    !["description" と "displayname" が強調されている "PATCH" の要求本文](./media/active-directory-enterprise-app-role-management/graph-explorer-patchupdate.png)

    1. メソッドを **GET** から **PATCH** に変更します。

    1. 既存のロールをコピーし、それらを **要求本文** に貼り付けます。

    1. 必要に応じて、ロールの説明、ロール値、またはロールの表示名を更新することで、ロールの値を更新します。

    1. 必要なすべてのロールを更新したら、 **[クエリの実行]** を選択します。

## <a name="delete-an-existing-role"></a>既存のロールを削除する

既存のロールを削除するには、以下の手順を実行します。

1. 別のウィンドウで [Microsoft Graph Explorer](https://developer.microsoft.com/graph/graph-explorer) を開きます。

1. テナントのグローバル管理者または共同管理者の資格情報を使用して、Graph Explorer サイトにサインインします。

1. バージョンを **ベータ版** に変更し、次のクエリを使用してテナントからサービス プリンシパルの一覧を取得します。

    `https://graph.microsoft.com/beta/servicePrincipals`

    複数のディレクトリを使用している場合は、次のパターンに従います。`https://graph.microsoft.com/beta/contoso.com/servicePrincipals`

    ![サービス プリンシパルの一覧をフェッチするためのクエリを表示している Graph Explorer ダイアログ ボックス](./media/active-directory-enterprise-app-role-management/graph-explorer-new1.png)

1. フェッチしたサービス プリンシパルの一覧から、変更する必要があるものを取得します。 Ctrl + F を使用して、一覧に示されたすべてのサービス プリンシパルからアプリケーションを検索することもできます。 [プロパティ] ページからコピーした **オブジェクト ID** を検索し、次のクエリを使用してサービス プリンシパルを取得します。

    `https://graph.microsoft.com/beta/servicePrincipals/<objectID>`

    ![変更する必要があるサービス プリンシパルを取得するためのクエリ](./media/active-directory-enterprise-app-role-management/graph-explorer-new2.png)

1. サービス プリンシパル オブジェクトから **appRoles** プロパティを抽出します。

    ![サービス プリンシパル オブジェクトの appRoles プロパティの詳細](./media/active-directory-enterprise-app-role-management/graph-explorer-new7.png)

1. 既存のロールを削除するには、以下の手順を実行します。

    ![IsEnabled が false に設定されている "PATCH" の要求本文](./media/active-directory-enterprise-app-role-management/graph-explorer-new8.png)

    1. メソッドを **GET** から **PATCH** に変更します。

    1. アプリケーションから既存のロールをコピーし、それらを **要求本文** に貼り付けます。

    1. 削除するロールの **IsEnabled** の値を **false** に設定します。

    1. **[クエリの実行]** を選択します。

    msiam_access ユーザー ロールを持っていること、および ID が生成されたロールと一致していることを確認してください。

1. ロールを無効にした後、そのロールのブロックを **appRoles** セクションから削除します。 メソッドを **PATCH** にしたまま、 **[クエリの実行]** を選択します。

1. クエリを実行すると、ロールが削除されます。

    ロールは、先に無効にしてから削除する必要があります。

## <a name="next-steps"></a>次のステップ

追加の手順については、[アプリのマニュアル](../saas-apps/tutorial-list.md)を参照してください。

<!--Image references-->

[1]: ./media/active-directory-enterprise-app-role-management/tutorial_general_01.png
[2]: ./media/active-directory-enterprise-app-role-management/tutorial_general_02.png
[3]: ./media/active-directory-enterprise-app-role-management/tutorial_general_03.png
[4]: ./media/active-directory-enterprise-app-role-management/tutorial_general_04.png