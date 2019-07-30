---
title: SharePoint ファイル - QnA Maker
titleSuffix: Azure Cognitive Services
description: ご自分のサポート情報にセキュリティで保護された SharePoint データ ソースを追加して、Active Directory によりセキュリティで保護されている可能性がある質問と回答でサポート情報を補強できます。
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: article
ms.date: 06/24/2019
ms.author: diberry
ms.openlocfilehash: ecb9777643296685d0dcc7cd5a177f2fe00d2580
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/09/2019
ms.locfileid: "67704624"
---
# <a name="add-a-secured-sharepoint-data-source-to-your-knowledge-base"></a>ご自分のサポート情報にセキュリティで保護された SharePoint データ ソースを追加する

ご自分のサポート情報にセキュリティで保護された SharePoint データ ソースを追加して、Active Directory によりセキュリティで保護されている可能性がある質問と回答でサポート情報を補強できます。 

ご自分のサポート情報にセキュリティで保護された SharePoint ドキュメントを追加する場合、QnA Maker マネージャーとして、QnA Maker 用の Active Directory に対するアクセス許可を要求する必要があります。 この、SharePoint にアクセスするアクセス許可が Active Directory マネージャーから QnA Maker に付与されると、再度それを取得する必要はありません。 それ以降サポート情報にドキュメントを追加する場合、それが同じ SharePoint リソース内にある場合、承認は不要です。 

QnA Maker サポート情報マネージャーが Active Directory マネージャーでない場合、Active Directory マネージャーにこの手順を完了するよう伝える必要があります。

## <a name="add-supported-file-types-to-knowledge-base"></a>サポート情報へのサポート対象ファイルの種類の追加

SharePoint サイトからご自分のサポート情報に、QnA Maker でサポートされているすべての[ファイルの種類](../Concepts/data-sources-supported.md)を追加できます。 ファイルのリソースがセキュリティで保護されている場合、[アクセス許可](#permissions)を付与する必要がある場合があります。

1. SharePoint サイトのあるライブラリから、ファイルの省略記号メニュー (`...`) を選択します。
1. ファイルの URL をコピーします。

   ![ファイルの省略記号メニューを選択してから URL をコピーして、SharePoint ファイルの URL を取得します。](../media/add-sharepoint-datasources/get-sharepoint-file-url.png)

1. QnA Maker ポータルの **[設定]** ページでサポート情報に [URL を追加](edit-knowledge-base.md#add-datasource)します。 

### <a name="images-with-sharepoint-files"></a>SharePoint ファイルでのイメージ

ファイルにイメージが含まれる場合、それらは抽出されません。 ファイルが QnA ペアに抽出された後、QnA Maker ポータルからイメージを追加できます。

次のマークダウン構文を使用してイメージを追加します。 

```markdown
![Explanation or description of image](URL of public image)
```

角かっこ `[]` 内のテキストは、イメージについての説明です。 かっこ `()` 内の URL は、イメージへの直接リンクです。 

QnA Maker ポータルのインタラクティブ テスト パネルで QnA ペアをテストすると、マークダウン テキストではなくイメージが表示されます。 これにより、クライアント アプリケーションからイメージをパブリックに取得できることが検証されます。

## <a name="permissions"></a>アクセス許可

SharePoint サーバーからセキュリティで保護されたファイルがサポート情報に追加されると、アクセス許可が付与されます。 SharePoint の設定方法およびファイルを追加するユーザーのアクセス許可に応じ、以下が必要になる場合があります。

* 追加の手順は不要です。そのファイルを追加するユーザーに必要なすべての権限があります。
* [サポート情報マネージャー](#knowledge-base-manager-add-sharepoint-data-source-in-qna-maker-portal)と [Active Directory マネージャー](#active-directory-manager-grant-file-read-access-to-qna-maker)の両者による手順。

以下の手順を参照してください。 

### <a name="knowledge-base-manager-add-sharepoint-data-source-in-qna-maker-portal"></a>サポート情報マネージャー: SharePoint データ ソースを QnA Maker ポータルに追加する

**QnA Maker マネージャー**がセキュリティで保護された SharePoint ドキュメントをサポート情報に追加すると、サポート情報マネージャーによって Active Directory マネージャーが完了する必要のあるアクセス許可の要求が開始されます。

この要求は、Active Directory アカウントの認証のポップアップで開始されます。 

![ユーザー アカウントの認証](../media/add-sharepoint-datasources/authenticate-user-account.png)

QnA Maker マネージャーがアカウントを選択すると、Azure Active Directory 管理者は (QnA Maker マネージャーではなく) QnA Maker アプリが SharePoint リソースへのアクセスを許可される必要があるという通知を受け取ります。 Azure Active Directory マネージャーはこれをすべての SharePoint リソースに対して行う必要があります。そのリソース内のすべてのドキュメントに対しては行う必要はありません。 

### <a name="active-directory-manager-grant-file-read-access-to-qna-maker"></a>Active Directory マネージャー: QnA Maker へのファイルの読み取りアクセスの付与

(QnA Maker マネージャーではなく) Active Directory マネージャーは SharePoint リソースにアクセスできるよう、[こちらのリンク](https://login.microsoftonline.com/common/oauth2/v2.0/authorize?response_type=id_token&scope=Files.Read%20Files.Read.All%20Sites.Read.All%20User.Read%20User.ReadBasic.All%20profile%20openid%20email&client_id=c2c11949-e9bb-4035-bda8-59542eb907a6&redirect_uri=https%3A%2F%2Fwww.qnamaker.ai%3A%2FCreate&state=68)を選択して QnA Maker Portal SharePoint エンタープライズ アプリがファイルの読み取りアクセス許可を持つことができるよう、QnA Maker にアクセスを付与する必要があります。 

![対話形式でアクセス許可を付与する Azure Active Directory マネージャー](../media/add-sharepoint-datasources/aad-manager-grants-permission-interactively.png)

<!--
The Active Directory manager must grant QnA Maker access either by application name, `QnAMakerPortalSharePoint`, or by application ID, `c2c11949-e9bb-4035-bda8-59542eb907a6`. 
-->
<!--
### Grant access from the interactive pop-up window 

The Active Directory manager will get a pop-up window requesting permissions to the `QnAMakerPortalSharePoint` app. The pop-up window includes the QnA Maker Manager email address that initiated the request, an `App Info` link to learn more about **QnAMakerPortalSharePoint**, and a list of permissions requested. Select **Accept** to provide those permissions. 

![Azure Active Directory manager grants permission interactively](../media/add-sharepoint-datasources/aad-manager-grants-permission-interactively.png)
-->
<!--

### Grant access from the App Registrations list

1. The Active Directory manager signs in to the Azure portal and opens **[App registrations list](https://ms.portal.azure.com/#blade/Microsoft_AAD_IAM/ApplicationsListBlade)**. 

1. Search for and select the **QnAMakerPortalSharePoint** app. Change the second filter box from **My apps** to **All apps**. The app information will open on the right side.

    ![Select QnA Maker app in App registrations list](../media/add-sharepoint-datasources/select-qna-maker-app-in-app-registrations.png)

1. Select **Settings**.

    [![Select Settings in the right-side blade](../media/add-sharepoint-datasources/select-settings-for-qna-maker-app-registration.png)](../media/add-sharepoint-datasources/select-settings-for-qna-maker-app-registration.png#lightbox)

1. Under **API access**, select **Required permissions**. 

    ![Select 'Settings', then under 'API access', select 'Required permission'](../media/add-sharepoint-datasources/select-required-permissions-in-settings-blade.png)

1. Do not change any settings in the **Enable Access** window. Select **Grant Permission**. 

    [![Under 'Grant Permission', select 'Yes'](../media/add-sharepoint-datasources/grant-app-required-permissions.png)](../media/add-sharepoint-datasources/grant-app-required-permissions.png#lightbox)

1. Select **YES** in the pop-up confirmation windows. 

    ![Grant required permissions](../media/add-sharepoint-datasources/grant-required-permissions.png)
-->
### <a name="grant-access-from-the-azure-active-directory-admin-center"></a>Azure Active Directory 管理センターからのアクセスを許可する

1. Active Directory マネージャーは Azure portal にサインインし、 **[Enterprise アプリケーション](https://aad.portal.azure.com/#blade/Microsoft_AAD_IAM/StartboardApplicationsMenuBlade/AllApps)** を開きます。 

1. `QnAMakerPortalSharePoint` を検索して、QnA Maker アプリを選択します。 

    [![Enterprise アプリの一覧で QnAMakerPortalSharePoint を検索する](../media/add-sharepoint-datasources/search-enterprise-apps-for-qna-maker.png)](../media/add-sharepoint-datasources/search-enterprise-apps-for-qna-maker.png#lightbox)

1. **[セキュリティ]** の下で **[アクセス許可]** に移動します。 **[組織に管理者の同意を与えます]** を選択します。 

    [![Active Directory 管理者に対して認証されたユーザーを選択する](../media/add-sharepoint-datasources/grant-aad-permissions-to-enterprise-app.png)](../media/add-sharepoint-datasources/grant-aad-permissions-to-enterprise-app.png#lightbox)

1. Active Directory へのアクセス許可を付与するアクセス許可を持つサインオン アカウントを選択します。 


  
<!--

## Add SharePoint data source with APIs

You need to get the SharePoint file's URI before adding it to QnA Maker. 

## Get SharePoint File URI

Use the following steps to transform the SharePoint URL into a sharing token.

1. Encode the URL using [base64](https://en.wikipedia.org/wiki/Base64). 

1. Convert the base64-encoded result to an unpadded base64url format with the following character changes. 

    * Remove the equal character, `=` from the end of the value. 
    * Replace `/` with `_`. 
    * Replace `+` with `-`. 
    * Append `u!` to be beginning of the string. 

1. Sign in to Graph explorer and run the following query, where `sharedURL` is ...:

    ```
    https://graph.microsoft.com/v1.0/shares/<sharedURL>/driveitem
    ```

    Get the **@microsoft.graph.downloadUrl** and use this as `fileuri` in the QnA Maker APIs.

### Add or update a SharePoint File URI to your knowledge base

Use the **@microsoft.graph.downloadUrl** from the previous section as the `fileuri` in the QnA Maker API for [adding a knowledge base](https://go.microsoft.com/fwlink/?linkid=2092179) or [updating a knowledge base](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/knowledgebase/update). The following fields are mandatory: name, fileuri, filename, source.

```
{
    "name": "Knowledge base name",
    "files": [
        {
            "fileUri": "<@microsoft.graph.downloadURL>",
            "fileName": "filename.xlsx",
            "source": "<SharePoint link>"
        }
    ],
    "urls": [],
    "users": [],
    "hostUrl": "",
    "qnaList": []
}
```



## Remove QnA Maker app from SharePoint authorization

1. Use the steps in the previous section to find the Qna Maker app in the Active Directory admin center. 
1. When you select the **QnAMakerPortalSharePoint**, select **Overview**. 
1. Select **Delete** to remove permissions. 

-->

## <a name="next-steps"></a>次の手順

> [!div class="nextstepaction"]
> [自分のサポート情報の共同利用](collaborate-knowledge-base.md)
