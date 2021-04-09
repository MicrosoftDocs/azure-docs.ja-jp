---
title: チュートリアル:Node.js コンソール アプリで Microsoft Graph を呼び出す | Azure
titleSuffix: Microsoft identity platform
description: このチュートリアルでは、Node.js コンソール アプリで Microsoft Graph を呼び出すためのコンソール アプリを作成します。
services: active-directory
author: derisen
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: tutorial
ms.date: 02/17/2021
ms.author: v-doeris
ms.openlocfilehash: 33d3712e25a06419e0ccc5914cdddfae7d85a371
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "101645791"
---
# <a name="tutorial-call-the-microsoft-graph-api-in-a-nodejs-console-app"></a>チュートリアル:Node.js コンソール アプリで Microsoft Graph API を呼び出す

このチュートリアルでは、独自の ID を使用して Microsoft Graph API を呼び出すコンソール アプリを作成します。 作成するコンソール アプリでは、[Node.js 用の Microsoft Authentication Library (MSAL)](https://github.com/AzureAD/microsoft-authentication-library-for-js/tree/dev/lib/msal-node) を使用します。

このチュートリアルでは、次の手順に従います。

> [!div class="checklist"]
> - Azure portal でアプリケーションを登録する
> - Node.js コンソール アプリ プロジェクトを作成する
> - アプリに認証ロジックを追加する
> - アプリの登録の詳細を追加する
> - Web API を呼び出すメソッドを追加する
> - アプリケーションをテストする

## <a name="prerequisites"></a>前提条件

- [Node.js](https://nodejs.org/en/download/)
- [Visual Studio Code](https://code.visualstudio.com/download) または別のコード エディター

## <a name="register-the-application"></a>アプリケーションを登録する

まず、[Microsoft ID プラットフォームへのアプリケーションの登録](quickstart-register-app.md)に関するページの手順に従って、アプリを登録します。

アプリの登録には、次の設定を使用します。

- 名前: `NodeConsoleApp` (推奨)
- サポートされているアカウントの種類: **この組織のディレクトリ内のアカウントのみ**
- API のアクセス許可: **[Microsoft API]**  >  **[Microsoft Graph]**  >  **[アプリケーションのアクセス許可]**  > `User.Read.All`
- クライアント シークレット: `*********` (後の手順で使用するためにこの値を記録します。これは 1 回しか表示されません)

## <a name="create-the-project"></a>プロジェクトを作成する

アプリケーションをホストするフォルダーを作成します (例: *NodeConsoleApp*)。

1. まず、ターミナル内のプロジェクト ディレクトリに移動し、次の NPM コマンドを実行します。

```console
    npm init -y
    npm install --save dotenv yargs axios @azure/msal-node
```

2. 次に、*bin* という名前のフォルダーを作成します。 次に、このフォルダー内に *index.js* という名前のファイルを作成し、次のコードを追加します。

```JavaScript
#!/usr/bin/env node

// read in env settings
require('dotenv').config();

const yargs = require('yargs');

const fetch = require('./fetch');
const auth = require('./auth');

const options = yargs
    .usage('Usage: --op <operation_name>')
    .option('op', { alias: 'operation', describe: 'operation name', type: 'string', demandOption: true })
    .argv;

async function main() {
    console.log(`You have selected: ${options.op}`);

    switch (yargs.argv['op']) {
        case 'getUsers':

            try {
                // here we get an access token
                const authResponse = await auth.getToken(auth.tokenRequest);

                // call the web API with the access token
                const users = await fetch.callApi(auth.apiConfig.uri, authResponse.accessToken);

                // display result
                console.log(users);
            } catch (error) {
                console.log(error);
            }

            break;
        default:
            console.log('Select a Graph operation first');
            break;
    }
};

main();
```

このファイルは他の 2 つのノード モジュールを参照します。アクセス トークンを取得するための MSAL Node の実装が含まれている *auth.js* と、アクセス トークンを使用して Microsoft Graph API に対して HTTP 要求を行うためのメソッドが含まれている *fetch.js* です。 チュートリアルの残りの部分を完了すると、このプロジェクトのファイルとフォルダーの構造は次のようになります。

```
NodeConsoleApp/
├── bin
│   ├── auth.js
│   ├── fetch.js
│   ├── index.js
├── package.json
└── .env
```

## <a name="add-authentication-logic"></a>認証ロジックを追加する

*bin* フォルダー内に *auth.js* という名前の別のファイルを作成し、Microsoft Graph API の呼び出し時に提示するアクセス トークンを取得するための次のコードを追加します。

```JavaScript
const msal = require('@azure/msal-node');

/**
 * Configuration object to be passed to MSAL instance on creation.
 * For a full list of MSAL Node configuration parameters, visit:
 * https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-node/docs/configuration.md
 */
const msalConfig = {
    auth: {
        clientId: process.env.CLIENT_ID,
        authority: process.env.AAD_ENDPOINT + process.env.TENANT_ID,
        clientSecret: process.env.CLIENT_SECRET,
    }
};

/**
 * With client credentials flows permissions need to be granted in the portal by a tenant administrator.
 * The scope is always in the format '<resource>/.default'. For more, visit:
 * https://docs.microsoft.com/azure/active-directory/develop/v2-oauth2-client-creds-grant-flow
 */
const tokenRequest = {
    scopes: [process.env.GRAPH_ENDPOINT + '.default'],
};

const apiConfig = {
    uri: process.env.GRAPH_ENDPOINT + 'v1.0/users',
};

/**
 * Initialize a confidential client application. For more info, visit:
 * https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-node/docs/initialize-confidential-client-application.md
 */
const cca = new msal.ConfidentialClientApplication(msalConfig);

/**
 * Acquires token with client credentials.
 * @param {object} tokenRequest
 */
async function getToken(tokenRequest) {
    return await cca.acquireTokenByClientCredential(tokenRequest);
}

module.exports = {
    apiConfig: apiConfig,
    tokenRequest: tokenRequest,
    getToken: getToken
};
```

上記のコード スニペットでは、まず構成オブジェクト (*msalConfig*) を作成し、これを渡すことによって MSAL [ConfidentialClientApplication](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-node/docs/initialize-confidential-client-application.md) を初期化します。 次に、**クライアントの資格情報** を使用してトークンを取得するためのメソッドを作成し、最後に、*main.js* からアクセスできるようにこのモジュールを公開します。 このモジュールの構成パラメーターは、次の手順で作成する環境ファイルから取得されます。

## <a name="add-app-registration-details"></a>アプリの登録の詳細を追加する

トークンを取得するときに使用されるアプリ登録の詳細を格納するための環境ファイルを作成します。 これを行うには、サンプルのルート フォルダー (*NodeConsoleApp*) 内に *.env* という名前のファイルを作成し、次のコードを追加します。

```
# Credentials
TENANT_ID=Enter_the_Tenant_Id_Here
CLIENT_ID=Enter_the_Application_Id_Here
CLIENT_SECRET=Enter_the_Client_Secret_Here

# Endpoints
AAD_ENDPOINT=Enter_the_Cloud_Instance_Id_Here
GRAPH_ENDPOINT=Enter_the_Graph_Endpoint_Here
```

これらの詳細には、Azure アプリ登録ポータルから取得した値を入力します。

- `Enter_the_Tenant_Id_here` は、次のいずれかにする必要があります。
  - ご自分のアプリケーションで "*この組織のディレクトリ内のアカウント*" がサポートされる場合は、この値を **テナント ID** または **テナント名** に置き換えます。 たとえば、「 `contoso.microsoft.com` 」のように入力します。
  - アプリケーションで "*任意の組織のディレクトリ内のアカウント*" がサポートされる場合は、この値を `organizations` に置き換えます。
  - アプリケーションで "*任意の組織のディレクトリ内のアカウントと個人用の Microsoft アカウント*" がサポートされる場合は、この値を `common` に置き換えます。
  - "*個人用の Microsoft アカウントのみ*" にサポートを制限するには、この値を `consumers` に置き換えます。
- `Enter_the_Application_Id_Here`:登録したアプリケーションの **アプリケーション (クライアント) ID**。
- `Enter_the_Cloud_Instance_Id_Here`:アプリケーションが登録されている Azure クラウド インスタンス。
  - メイン ("*グローバル*") Azure クラウドの場合は、「`https://login.microsoftonline.com`」と入力します。
  - **各国** のクラウド (中国など) の場合は、「[各国のクラウド](authentication-national-cloud.md)」に適切な値が記載されています。
- `Enter_the_Graph_Endpoint_Here` は、アプリケーションが通信する必要がある、Microsoft Graph API のインスタンスです。
  - **グローバル** Microsoft Graph API エンドポイントの場合は、この文字列の両方のインスタンスを `https://graph.microsoft.com` に置き換えます。
  - **各国** のクラウドのデプロイにおけるエンドポイントの場合は、Microsoft Graph のドキュメントで「[各国のクラウドでのデプロイ](/graph/deployments)」を参照してください。

## <a name="add-a-method-to-call-a-web-api"></a>Web API を呼び出すメソッドを追加する

*bin* フォルダーに *fetch.js* という名前の別のファイルを作成し、Microsoft Graph API への REST 呼び出しを行うための次のコードを追加します。

```javascript
const axios = require('axios');

/**
 * Calls the endpoint with authorization bearer token.
 * @param {string} endpoint
 * @param {string} accessToken
 */
async function callApi(endpoint, accessToken) {

    const options = {
        headers: {
            Authorization: `Bearer ${accessToken}`
        }
    };

    console.log('request made to web API at: ' + new Date().toString());

    try {
        const response = await axios.default.get(endpoint, options);
        return response.data;
    } catch (error) {
        console.log(error)
        return error;
    }
};

module.exports = {
    callApi: callApi
};
```

ここでは、`callApi` メソッドを使用して、アクセス トークンを必要とする保護されたリソースに対して HTTP `GET` 要求を実行します。 その後、この要求からその内容が呼び出し元に返されます。 このメソッドは、取得したトークンを *HTTP Authorization ヘッダー* に追加します。 ここで保護されているリソースは、このアプリが登録されているテナント内のユーザーを表示する Microsoft Graph API [ユーザー エンドポイント](/graph/api/user-list)です。

## <a name="test-the-app"></a>アプリをテストする

これでアプリケーションの作成が完了し、アプリの機能をテストする準備ができました。

プロジェクト フォルダーのルート内から次のコマンドを実行して、Node.js コンソール アプリを起動します。

```console
node . --op getUsers
```

これにより Microsoft Graph API からの JSON 応答が生成され、コンソールにユーザー オブジェクトの配列が表示されます。

```console
You have selected: getUsers
request made to web API at: Fri Jan 22 2021 09:31:52 GMT-0800 (Pacific Standard Time)
{
    '@odata.context': 'https://graph.microsoft.com/v1.0/$metadata#users',
    value: [
        {
            displayName: 'Adele Vance'
            givenName: 'Adele',
            jobTitle: 'Retail Manager',
            mail: 'AdeleV@msaltestingjs.onmicrosoft.com',
            mobilePhone: null,
            officeLocation: '18/2111',
            preferredLanguage: 'en-US',
            surname: 'Vance',
            userPrincipalName: 'AdeleV@msaltestingjs.onmicrosoft.com',
            id: 'a6a218a5-f5ae-462a-acd3-581af4bcca00'
        }
    ]
}
```
:::image type="content" source="media/tutorial-v2-nodejs-console/screenshot.png" alt-text="Graph の応答が表示されたコマンド ライン インターフェイス":::

## <a name="how-the-application-works"></a>アプリケーションの動作

このアプリケーションでは、[OAuth 2.0 クライアント資格情報の認可](./v2-oauth2-client-creds-grant-flow.md)を使用します。 この種類の許可は、バックグラウンドでの実行が必要なサーバー間の相互作用に使用され、ユーザーとの即時の相互動作は必要ありません。 資格情報許可フローでは、Web サービス (機密クライアント) が別の Web サービスを呼び出すときに、ユーザーを偽装する代わりに、独自の資格情報を使用して認証を行うことができます。 この認証モデルでサポートされるアプリケーションの種類は、通常、**デーモン** または **サービス アカウント** です。

クライアントの資格情報フローのために要求するスコープは、後に `/.default` が続くリソースの名前です。 この表記は、アプリケーションの登録時に静的に宣言された "アプリケーション レベルのアクセス許可" を使用するように Azure Active Directory (Azure AD) に指示します。 また、これらの API アクセス許可は、**テナント管理者** が付与する必要があります。

## <a name="next-steps"></a>次の手順

Microsoft ID プラットフォームでの Node.js コンソール アプリケーションの開発についてさらに詳しく知りたい場合は、複数のパートで構成される次のシナリオ シリーズを参照してください。

> [!div class="nextstepaction"]
> [シナリオ: デーモン アプリケーション](scenario-daemon-overview.md)