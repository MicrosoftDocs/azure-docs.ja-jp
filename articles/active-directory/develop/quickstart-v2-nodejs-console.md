---
title: 'クイックスタート: Node.js コンソール アプリから Microsoft Graph を呼び出す | Azure'
titleSuffix: Microsoft identity platform
description: このクイックスタートでは、Node.js コンソール アプリケーションでアクセス トークンを取得し、Microsoft ID プラットフォーム エンドポイントによって保護されている API を、アプリ自体の ID を使用して呼び出す方法を示すコード サンプルをダウンロードして実行します
services: active-directory
author: derisen
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: quickstart
ms.date: 02/17/2021
ms.author: v-doeris
ms.openlocfilehash: 4360810d460c5fc8598ce302ad8b82f65d2d819e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "101653747"
---
# <a name="quickstart-acquire-a-token-and-call-microsoft-graph-api-from-a-nodejs-console-app-using-apps-identity"></a>クイックスタート: トークンを取得し、Node.js コンソール アプリからアプリの ID を使用して Microsoft Graph API を呼び出す

このクイックスタートでは、Node.js コンソール アプリケーションでアプリの ID を使ってアクセス トークンを取得して、Microsoft Graph API を呼び出し、ディレクトリ内の[ユーザーの一覧](/graph/api/user-list)を表示する方法を示すコード サンプルをダウンロードして実行します。 このコード サンプルでは、ユーザーの ID ではなく、アプリケーション ID を使用して、無人のジョブまたは Windows サービスを実行する方法を示します。

このクイックスタートでは、[クライアント資格情報の付与](v2-oauth2-client-creds-grant-flow.md)で [Node.js 用 Microsoft Authentication Library (MSAL Node)](https://github.com/AzureAD/microsoft-authentication-library-for-js/tree/dev/lib/msal-node) を使用します。

## <a name="prerequisites"></a>前提条件

* [Node.js](https://nodejs.org/en/download/)
* [Visual Studio Code](https://code.visualstudio.com/download) または別のコード エディター

> [!div renderon="docs"]
> ## <a name="register-and-download-the-sample-application"></a>サンプル アプリケーションを登録してダウンロードする
>
> まず、以下の手順に従ってください。
>
> [!div renderon="docs"]
> #### <a name="step-1-register-the-application"></a>手順 1:アプリケーションを登録する
> アプリケーションを登録し、その登録情報をソリューションに手動で追加するには、次の手順を実行します。
>
> 1. <a href="https://portal.azure.com/" target="_blank">Azure portal</a> にサインインします。
> 1. 複数のテナントにアクセスできる場合は、トップ メニューの **[ディレクトリとサブスクリプション]** フィルター:::image type="icon" source="./media/common/portal-directory-subscription-filter.png" border="false":::を使用して、アプリケーションを登録するテナントを選択します。
> 1. **Azure Active Directory** を検索して選択します。
> 1. **[管理]** で **[アプリの登録]**  >  **[新規登録]** の順に選択します。
> 1. アプリケーションの **名前** を入力します (例: `msal-node-cli`)。 この名前は、アプリのユーザーに表示される場合があります。また、後で変更することができます。
> 1. **[登録]** を選択します。
> 1. **[管理]** で、 **[証明書とシークレット]** を選択します。
> 1. **[クライアント シークレット]** で、 **[新しいクライアント シークレット]** を選択し、名前を入力して、 **[追加]** を選択します。 後の手順で使用できるように、シークレットの値を安全な場所に記録します。
> 1. **[管理]** で、 **[API のアクセス許可]**  >  **[アクセス許可の追加]** の順に選択します。 **[Microsoft Graph]** を選択します。
> 1. **[アプリケーションのアクセス許可]** を選択します。
> 1. **[ユーザー]** ノードで、 **[User.Read.All]** を選択し、 **[アクセス許可の追加]** を選択します。

> [!div class="sxs-lookup" renderon="portal"]
> ### <a name="download-and-configure-the-sample-app"></a>サンプル アプリをダウンロードして構成する
>
> #### <a name="step-1-configure-the-application-in-azure-portal"></a>手順 1: Azure portal でのアプリケーションの構成
> このクイック スタート用サンプル コードを動作させるには、クライアント シークレットを作成し、Graph API の **User.Read.All** アプリケーションのアクセス許可を追加します。
> > [!div renderon="portal" id="makechanges" class="nextstepaction"]
> > [これらの変更を行います]()
>
> > [!div id="appconfigured" class="alert alert-info"]
> > ![構成済み](media/quickstart-v2-netcore-daemon/green-check.png) アプリケーションはこれらの属性で構成されています。

#### <a name="step-2-download-the-nodejs-sample-project"></a>手順 2: Node.js サンプル プロジェクトをダウンロードする

> [!div renderon="docs"]
> [コード サンプルをダウンロードします](https://github.com/azure-samples/ms-identity-javascript-nodejs-console/archive/main.zip)

> [!div renderon="portal" id="autoupdate" class="sxs-lookup nextstepaction"]
> [コード サンプルをダウンロードします](https://github.com/azure-samples/ms-identity-javascript-nodejs-console/archive/main.zip)

> [!div class="sxs-lookup" renderon="portal"]
> > [!NOTE]
> > `Enter_the_Supported_Account_Info_Here`

> [!div renderon="docs"]
> #### <a name="step-3-configure-the-nodejs-sample-project"></a>手順 3: Node.js サンプル プロジェクトを構成する
>
> 1. ディスクのルートに近いローカル フォルダー (例: *C:/Azure-Samples*) に ZIP ファイルを展開します。
> 1. *.env* を編集し、`TENANT_ID`、`CLIENT_ID`、`CLIENT_SECRET` の各フィールドの値を次のスニペットに置き換えます。
>
>    ```
>    "TENANT_ID": "Enter_the_Tenant_Id_Here",
>    "CLIENT_ID": "Enter_the_Application_Id_Here",
>    "CLIENT_SECRET": "Enter_the_Client_Secret_Here"
>    ```
>    各値の説明:
>    - `Enter_the_Application_Id_Here` - 前に登録したアプリケーションの **アプリケーション (クライアント) ID**。 この ID は、Azure portal からアプリの登録の **[概要]** ペインで確認できます。
>    - `Enter_the_Tenant_Id_Here` - この値を **テナント ID** または **テナント名** (例: contoso.microsoft.com) に置き換えます。  これらの値は、Azure portal からアプリの登録の **[概要]** ペインで確認できます。
>    - `Enter_the_Client_Secret_Here` - この値を、前に作成したクライアント シークレットに置き換えます。 新しいキーを生成するには、Azure portal でアプリの登録設定の **[証明書とシークレット]** を使用します。
>
> > [!WARNING]
> > ソース コードでシークレットがプレーンテキストになっていると、セキュリティ リスクが増大します。 この記事でプレーンテキストのクライアント シークレットを使用しているのは、あくまで簡潔にするためです。 機密性の高いクライアント アプリケーション、特に運用環境へのデプロイを予定しているアプリでは、クライアント シークレットではなく、[証明書の資格情報](active-directory-certificate-credentials.md)を使用してください。

> [!div class="sxs-lookup" renderon="portal"]
> #### <a name="step-3-admin-consent"></a>手順 3:管理者の同意

> [!div renderon="docs"]
> #### <a name="step-4-admin-consent"></a>手順 4:管理者の同意

この時点でアプリケーションを実行すると、*HTTP 403 - Forbidden* エラー "`Insufficient privileges to complete the operation`" が表示されます。 これは、すべての "*アプリ専用のアクセス許可*" には **管理者の同意** が必要であるために発生します。ディレクトリのグローバル管理者にお使いのアプリケーションに同意してもらう必要があります。 ご自身のロールに応じて、次のオプションのいずれかを選択します。

##### <a name="global-tenant-administrator"></a>グローバル テナント管理者

> [!div renderon="docs"]
> グローバル テナント管理者の場合は、Azure portal 上で [アプリケーションの登録] の **[API のアクセス許可]** ページに移動し、 **[<Tenant Name> に管理者の同意を与えます]** (<Tenant Name> はお使いのディレクトリの名前) を選択します。

> [!div renderon="portal" class="sxs-lookup"]
> グローバル管理者の場合は、 **[API のアクセス許可]** ページに移動し、 **[Enter_the_Tenant_Name_Here に管理者の同意を与えます]** を選択します。
> > [!div id="apipermissionspage"]
> > [[API のアクセス許可] ページに移動する]()

##### <a name="standard-user"></a>標準ユーザー

テナントの標準ユーザーの場合は、お使いのアプリケーションに **管理者の同意** を与えるようグローバル管理者に依頼する必要があります。 これを行うには、次の URL を管理者に知らせます。

```url
https://login.microsoftonline.com/Enter_the_Tenant_Id_Here/adminconsent?client_id=Enter_the_Application_Id_Here
```

> [!div renderon="docs"]
>> 各値の説明:
>> * `Enter_the_Tenant_Id_Here` - この値を **テナント ID** または **テナント名** (例: contoso.microsoft.com) に置き換えます。
>> * `Enter_the_Application_Id_Here` - 登録したアプリケーションの **アプリケーション (クライアント) ID**。

> [!div class="sxs-lookup" renderon="portal"]
> #### <a name="step-4-run-the-application"></a>手順 4:アプリケーションの実行

> [!div renderon="docs"]
> #### <a name="step-5-run-the-application"></a>手順 5:アプリケーションの実行

コマンド プロンプトまたはコンソールで、(`package.json` が存在する) サンプルのルート フォルダーを探します。 このサンプルの依存関係を 1 回インストールする必要があります。

```console
npm install
```

次に、コマンド プロンプトまたはコンソールを使用して、アプリケーションを実行します。

```console
node . --op getUsers
```

コンソール出力には、Azure AD ディレクトリ内のユーザーの一覧を表すいくつかの JSON フラグメントが表示されます。

## <a name="about-the-code"></a>コードについて

以降、サンプル アプリケーションのいくつかの重要な要素について説明します。

### <a name="msal-node"></a>MSAL Node

[MSAL Node](https://github.com/AzureAD/microsoft-authentication-library-for-js/tree/dev/lib/msal-node) はユーザーのサインインを処理し、Microsoft ID プラットフォームによって保護されている API にアクセスするトークンを要求するために使用するライブラリです。 説明したとおり、このクイックスタートでは、委任されたアクセス許可ではなく、アプリケーションのアクセス許可 (アプリケーション自体の ID) を使用してトークンを要求しています。 ここで使用される認証フローは、[OAuth 2.0 クライアント資格情報フロー](v2-oauth2-client-creds-grant-flow.md)と呼ばれます。 デーモン アプリでの MSAL Node の使用方法の詳細については、[デーモン アプリケーションのシナリオ](scenario-daemon-overview.md)を参照してください。

 MSAL Node は、次の npm コマンドを実行してインストールできます。

```console
npm install @azure/msal-node --save
```

### <a name="msal-initialization"></a>MSAL の初期化

MSAL への参照を追加するには、次のコードを追加します。

```javascript
const msal = require('@azure/msal-node');
```

続いて、次のコードを使用して MSAL を初期化します。

```javascript
const msalConfig = {
    auth: {
        clientId: "Enter_the_Application_Id_Here",
        authority: "https://login.microsoftonline.com/Enter_the_Tenant_Id_Here",
        clientSecret: "Enter_the_Client_Secret_Here",
   }
};
const cca = new msal.ConfidentialClientApplication(msalConfig);
```

> | 各値の説明: |説明 |
> |---------|---------|
> | `clientId` | Azure portal に登録されているアプリケーションの "**アプリケーション (クライアント) ID**"。 この値は、Azure portal のアプリの **[概要]** ページで確認できます。 |
> | `authority`    | ユーザーが認証するための STS エンドポイント。 通常、パブリック クラウド上では `https://login.microsoftonline.com/{tenant}` です。{tenant} はご自分のテナントの名前またはテナント ID です。|
> | `clientSecret` | Azure Portal 上でアプリケーションに対して作成されるクライアント シークレット。 |

詳細については、[`ConfidentialClientApplication` 用の参照ドキュメント](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-node/docs/initialize-confidential-client-application.md)をご覧ください。

### <a name="requesting-tokens"></a>トークンの要求

アプリの ID を使用してトークンを要求するには、`acquireTokenByClientCredential` メソッドを使用します。

```javascript
const tokenRequest = {
    scopes: [ 'https://graph.microsoft.com/.default' ],
};

const tokenResponse = await cca.acquireTokenByClientCredential(tokenRequest);
```

> |各値の説明:| 説明 |
> |---------|---------|
> | `tokenRequest` | 要求されるスコープが含まれています。 Confidential クライアントの場合は、`{Application ID URI}/.default` のような形式を使用して、要求されるスコープが Azure Portal 上で設定されるアプリ オブジェクト内に静的に定義されたものであることを示す必要があります (Microsoft Graph では、`{Application ID URI}` は `https://graph.microsoft.com` を指します)。 カスタム Web API の場合、`{Application ID URI}` は、Azure portal 上で [アプリケーションの登録] の **[API の公開]** セクションに定義されます。 |
> | `tokenResponse` | 応答には、要求したスコープのアクセストークンが含まれています。 |

[!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]

## <a name="next-steps"></a>次の手順

MSAL Node を使用したデーモン (コンソール) アプリ開発については、次のチュートリアルを参照してください。

> [!div class="nextstepaction"]
> [Web API を呼び出すデーモン アプリケーション](tutorial-v2-nodejs-console.md)