---
title: Azure AD でエンタープライズ アプリケーション用の SAML トークン内に発行されるロール要求を構成する | Microsoft Docs
description: Azure Active Directory でエンタープライズ アプリケーション用の SAML トークン内に発行されるロール要求を構成する方法について説明します
services: active-directory
documentationcenter: ''
author: jeevansd
manager: CelesteDG
editor: ''
ms.assetid: eb2b3741-3cde-45c8-b639-a636f3df3b74
ms.service: active-directory
ms.subservice: develop
ms.custom: aaddev
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/22/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 148324f293c36b88657f50a7405d85210b62dcc4
ms.sourcegitcommit: 4b431e86e47b6feb8ac6b61487f910c17a55d121
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/18/2019
ms.locfileid: "68321238"
---
# <a name="how-to-configure-the-role-claim-issued-in-the-saml-token-for-enterprise-applications"></a>方法:エンタープライズ アプリケーション用の SAML トークン内に発行されるロール要求を構成する

Azure Active Directory (Azure AD) を使用して、アプリを承認した後に受信する応答トークン内のロール要求の要求の種類をカスタマイズできます。

## <a name="prerequisites"></a>前提条件

- ディレクトリのセットアップを済ませた Azure AD サブスクリプション。
- シングル サインオン (SSO) が有効なサブスクリプション。 アプリケーションで SSO を構成する必要があります。

## <a name="when-to-use-this-feature"></a>この機能を使用する状況

アプリケーションが、SAML 応答でカスタム ロールが渡されることを予期している場合は、この機能を使用する必要があります。 Azure AD からアプリケーションに渡すロールを必要な数だけ作成できます。

## <a name="create-roles-for-an-application"></a>アプリケーションのロールを作成する

1. [Azure Portal](https://portal.azure.com) の左側のウィンドウで、**Azure Active Directory** アイコンを選択します。

    ![Azure Active Directory のアイコン][1]

2. **[エンタープライズ アプリケーション]** を選択します。 次に、 **[すべてのアプリケーション]** を選択します。

    ![[エンタープライズ アプリケーション] ウィンドウ][2]

3. 新しいアプリケーションを追加するには、ダイアログ ボックスの上部にある **[新しいアプリケーション]** ボタンを選びます。

    ![[新しいアプリケーション] ボタン][3]

4. 検索ボックスにアプリケーションの名前を入力し、結果ウィンドウからアプリケーションを選択します。 **[追加]** をクリックしてアプリケーションを追加します。

    ![結果リストのアプリケーション](./media/active-directory-enterprise-app-role-management/tutorial_app_addfromgallery.png)

5. アプリケーションが追加されたら、 **[プロパティ]** ページに移動し、オブジェクト ID をコピーします。

    ![[プロパティ] ページ](./media/active-directory-enterprise-app-role-management/tutorial_app_properties.png)

6. 別のウィンドウで [Azure AD Graph Explorer](https://developer.microsoft.com/graph/graph-explorer) を開き、以下の手順を実行します。

    a. テナントのグローバル管理者または共同管理者の資格情報を使用して、Graph Explorer サイトにサインインします。

    b. ロールを作成するための十分なアクセス許可が必要です。 **[アクセス許可の変更]** を選択して、必要なアクセス許可を取得します。

      ![[アクセス許可の変更] ボタン](./media/active-directory-enterprise-app-role-management/graph-explorer-new9.png)

    c. 一覧から次のアクセス許可を選択し (まだ持っていない場合)、 **[アクセス許可の変更]** を選択します。

      ![アクセス許可の一覧と [アクセス許可の変更] ボタン](./media/active-directory-enterprise-app-role-management/graph-explorer-new10.png)

    > [!Note]
    > クラウド アプリ管理者とアプリ管理者のロールは、このシナリオでは使用できません。ディレクトリの読み取りと書き込みのために全体管理者のアクセス許可が必要になるためです。

    d. 同意を受け入れます。 システムにもう一度ログインされます。

    e. バージョンを**ベータ版**に変更し、次のクエリを使用してテナントからサービス プリンシパルの一覧を取得します。

     `https://graph.microsoft.com/beta/servicePrincipals`

      複数のディレクトリを使用している場合は、次のパターンに従います。`https://graph.microsoft.com/beta/contoso.com/servicePrincipals`

      ![サービス プリンシパルをフェッチするためのクエリを表示している Graph Explorer ダイアログ ボックス](./media/active-directory-enterprise-app-role-management/graph-explorer-new1.png)

      > [!Note]
      > Microsoft では、サービスの途絶を経験しているお客様のために、この API をアップグレードするプロセスを既に進めています。

    f. フェッチしたサービス プリンシパルの一覧から、変更する必要があるものを取得します。 Ctrl + F を使用して、一覧に示されたすべてのサービス プリンシパルからアプリケーションを検索することもできます。 [プロパティ] ページからコピーした **オブジェクト ID** を検索し、次のクエリを使用してサービス プリンシパルを取得します。

      `https://graph.microsoft.com/beta/servicePrincipals/<objectID>`

      ![変更する必要があるサービス プリンシパルを取得するためのクエリ](./media/active-directory-enterprise-app-role-management/graph-explorer-new2.png)

    g. サービス プリンシパル オブジェクトから **appRoles** プロパティを抽出します。

      ![appRoles プロパティの詳細](./media/active-directory-enterprise-app-role-management/graph-explorer-new3.png)

      > [!Note]
      > カスタム アプリ (Azure Marketplace 以外のアプリ) を使用している場合は、2 つの既定のロール (user と msiam_access) が表示されます。 Marketplace アプリの場合、既定のロールは msiam_access のみとなります。 既定のロールに変更を加える必要はありません。

    h. アプリケーションの新しいロールを生成します。

      次の JSON は、**appRoles** オブジェクトの例です。 同様のオブジェクトを作成して、アプリケーションで必要なロールを追加します。

      ```
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

      > [!Note]
      > パッチ操作では、msiam_access の後にのみ、新しいロールを追加できます。 また、組織が必要とする数のロールを追加できます。 Azure AD では、SAML 応答の要求値として、これらのロールの値を送信します。 新しいロールの ID の GUID 値を生成するには、[こちら](https://www.guidgenerator.com/)のような Web ツールを使用します。

    i. Graph Explorer に戻り、メソッドを **GET** から **PATCH** に変更します。 前の例に示されているように **appRoles** プロパティを更新して、必要なロールを持つようにサービス プリンシパル オブジェクトを修正します。 **[クエリの実行]** をクリックして、パッチ操作を実行します。 成功メッセージにより、ロールの作成が確認されます。

      ![Patch 操作と成功メッセージ](./media/active-directory-enterprise-app-role-management/graph-explorer-new11.png)

7. より多くのロールでサービス プリンシパルを修正したら、対応するロールにユーザーを割り当てることができます。 ポータルに移動し、アプリケーションを参照することで、ユーザーを割り当てることができます。 **[ユーザーとグループ]** タブを選択します。このタブには、アプリに既に割り当てられているユーザーとグループがすべて表示されます。 新しいロールに新しいユーザーを追加できます。 また、既存のユーザーを選択し、 **[編集]** を選択してロールを変更することもできます。

    ![[ユーザーとグループ] タブ](./media/active-directory-enterprise-app-role-management/graph-explorer-new5.png)

    ユーザーにロールを割り当てるには、新しいロールを選択し、ページの下部にある **[割り当て]** ボタンを選択します。

    ![[割り当ての編集] ウィンドウと [ロールの選択] ウィンドウ](./media/active-directory-enterprise-app-role-management/graph-explorer-new6.png)

    > [!Note]
    > 新しいロールを表示するには、Azure Portal でセッションを更新する必要があります。

8. **[属性]** テーブルを更新して、ロール要求のカスタマイズされたマッピングを定義します。

9. **[ユーザー属性]** ダイアログの **[ユーザー要求]** セクションで、以下の手順を実行して、以下の表のように SAML トークン属性を追加します。

    | 属性名 | 属性名 |
    | -------------- | ----------------|
    | ロール名  | user.assignedroles |

    >[!NOTE]
    >ロール要求の値が null の場合、Azure AD はトークンでこの値を送信しません。これは、設計上の既定値です。

    a. **[編集]** アイコンをクリックして、 **[ユーザー属性と要求]** ダイアログを開きます。

      ![[属性の追加] ボタン](./media/active-directory-enterprise-app-role-management/editattribute.png)

    b. **[ユーザー要求の管理]** ダイアログで、 **[新しい要求の追加]** をクリックして、SAML トークン属性を追加します。

      ![[属性の追加] ボタン](./media/active-directory-enterprise-app-role-management/tutorial_attribute_04.png)

      ![[属性の追加] ウィンドウ](./media/active-directory-enterprise-app-role-management/tutorial_attribute_05.png)

    c. **[名前]** ボックスに、必要に応じて属性名を入力します。 この例では、要求名として**ロールの名前**を使用します。

    d. **[名前空間]** ボックスは空白のままにします。

    e. **[ソース属性]** の一覧から、その行に表示される属性値を入力します。

    f. **[保存]** を選択します。

10. ID プロバイダーによって開始されるシングル サインオンでアプリケーションをテストするには、[アクセス パネル](https://myapps.microsoft.com)にサインインし、アプリケーション タイルを選択します。 SAML トークンには、指定した要求の名前を持つ、そのユーザーに割り当てられているすべてのロールが表示されるはずです。

## <a name="update-an-existing-role"></a>既存のロールを更新する

既存のロールを更新するには、以下の手順を実行します。

1. [Azure AD Graph Explorer](https://developer.microsoft.com/graph/graph-explorer) を開きます

2. テナントのグローバル管理者または共同管理者の資格情報を使用して、Graph Explorer サイトにサインインします。

3. バージョンを**ベータ版**に変更し、次のクエリを使用してテナントからサービス プリンシパルの一覧を取得します。

    `https://graph.microsoft.com/beta/servicePrincipals`

    複数のディレクトリを使用している場合は、次のパターンに従います。`https://graph.microsoft.com/beta/contoso.com/servicePrincipals`

    ![サービス プリンシパルをフェッチするためのクエリを表示している Graph Explorer ダイアログ ボックス](./media/active-directory-enterprise-app-role-management/graph-explorer-new1.png)

4. フェッチしたサービス プリンシパルの一覧から、変更する必要があるものを取得します。 Ctrl + F を使用して、一覧に示されたすべてのサービス プリンシパルからアプリケーションを検索することもできます。 [プロパティ] ページからコピーした **オブジェクト ID** を検索し、次のクエリを使用してサービス プリンシパルを取得します。

    `https://graph.microsoft.com/beta/servicePrincipals/<objectID>`

    ![変更する必要があるサービス プリンシパルを取得するためのクエリ](./media/active-directory-enterprise-app-role-management/graph-explorer-new2.png)

5. サービス プリンシパル オブジェクトから **appRoles** プロパティを抽出します。

    ![appRoles プロパティの詳細](./media/active-directory-enterprise-app-role-management/graph-explorer-new3.png)

6. 既存のロールを更新するには、以下の手順を実行します。

    !["description" と "displayname" が強調されている "PATCH" の要求本文](./media/active-directory-enterprise-app-role-management/graph-explorer-patchupdate.png)

    a. メソッドを **GET** から **PATCH** に変更します。

    b. 既存のロールをコピーし、それらを**要求本文**に貼り付けます。

    c. 必要に応じて、ロールの説明、ロール値、またはロールの表示名を更新することで、ロールの値を更新します。

    d. 必要なすべてのロールを更新したら、 **[クエリの実行]** を選択します。

## <a name="delete-an-existing-role"></a>既存のロールを削除する

既存のロールを削除するには、以下の手順を実行します。

1. 別のウィンドウで [Azure AD Graph Explorer](https://developer.microsoft.com/graph/graph-explorer) を開きます。

2. テナントのグローバル管理者または共同管理者の資格情報を使用して、Graph Explorer サイトにサインインします。

3. バージョンを**ベータ版**に変更し、次のクエリを使用してテナントからサービス プリンシパルの一覧を取得します。

    `https://graph.microsoft.com/beta/servicePrincipals`

    複数のディレクトリを使用している場合は、次のパターンに従います。`https://graph.microsoft.com/beta/contoso.com/servicePrincipals`

    ![サービス プリンシパルの一覧をフェッチするためのクエリを表示している Graph Explorer ダイアログ ボックス](./media/active-directory-enterprise-app-role-management/graph-explorer-new1.png)

4. フェッチしたサービス プリンシパルの一覧から、変更する必要があるものを取得します。 Ctrl + F を使用して、一覧に示されたすべてのサービス プリンシパルからアプリケーションを検索することもできます。 [プロパティ] ページからコピーした **オブジェクト ID** を検索し、次のクエリを使用してサービス プリンシパルを取得します。

    `https://graph.microsoft.com/beta/servicePrincipals/<objectID>`

    ![変更する必要があるサービス プリンシパルを取得するためのクエリ](./media/active-directory-enterprise-app-role-management/graph-explorer-new2.png)

5. サービス プリンシパル オブジェクトから **appRoles** プロパティを抽出します。

    ![サービス プリンシパル オブジェクトの appRoles プロパティの詳細](./media/active-directory-enterprise-app-role-management/graph-explorer-new7.png)

6. 既存のロールを削除するには、以下の手順を実行します。

    ![IsEnabled が false に設定されている "PATCH" の要求本文](./media/active-directory-enterprise-app-role-management/graph-explorer-new8.png)

    a. メソッドを **GET** から **PATCH** に変更します。

    b. アプリケーションから既存のロールをコピーし、それらを**要求本文**に貼り付けます。

    c. 削除するロールの **IsEnabled** の値を **false** に設定します。

    d. **[クエリの実行]** を選択します。

    > [!NOTE]
    > msiam_access ユーザー ロールを持っていること、および ID が生成されたロールと一致していることを確認してください。

7. ロールを無効にした後、そのロールのブロックを **appRoles** セクションから削除します。 メソッドを **PATCH** にしたまま、 **[クエリの実行]** を選択します。

8. クエリを実行すると、ロールが削除されます。

    > [!NOTE]
    > ロールは、先に無効にしてから削除する必要があります。

## <a name="next-steps"></a>次の手順

追加の手順については、[アプリのマニュアル](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)を参照してください。

<!--Image references-->
<!--Image references-->

[1]: ./media/active-directory-enterprise-app-role-management/tutorial_general_01.png
[2]: ./media/active-directory-enterprise-app-role-management/tutorial_general_02.png
[3]: ./media/active-directory-enterprise-app-role-management/tutorial_general_03.png
[4]: ./media/active-directory-enterprise-app-role-management/tutorial_general_04.png
