---
title: 信頼できるサービスの JavaScript
description: これは、Communication Services の信頼できるサービスを作成する JavaScript バージョンです。
author: dademath
manager: nimag
services: azure-communication-services
ms.author: ddematheu2
ms.date: 03/10/2021
ms.topic: include
ms.service: azure-communication-services
ms.openlocfilehash: 41d959468e3183af00d2ab514e7c1bf0a134a1f8
ms.sourcegitcommit: 4bda786435578ec7d6d94c72ca8642ce47ac628a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/16/2021
ms.locfileid: "103490478"
---
## <a name="download-code"></a>コードをダウンロードする

このクイックスタートの最終的なコードは [GitHub](https://github.com/Azure-Samples/communication-services-javascript-quickstarts/tree/main/trusted-authentication-service) にあります

## <a name="prerequisites"></a>前提条件

- アクティブなサブスクリプションが含まれる Azure アカウント。 詳細については、[アカウントの無料作成](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)に関するページを参照してください。
- [サポートされているプラットフォーム](https://code.visualstudio.com/docs/supporting/requirements#_platforms)のいずれかにインストールされた [Visual Studio Code](https://code.visualstudio.com/)。
- [Node.js](https://nodejs.org/)。アクティブ LTS およびメンテナンス LTS バージョン (10.14.1 を推奨)。 `node --version` コマンドを使用して、現在のバージョンを確認してください。
- Visual Studio Code 用 [Azure Functions 拡張機能](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions)。
- アクティブな Communication Services リソースと接続文字列。 [Communication Services リソースを作成します](../../quickstarts/create-communication-resource.md)。

## <a name="overview"></a>概要

:::image type="content" source="../media/trusted-service-architecture.png" alt-text="信頼できるサービスのアーキテクチャの図":::

このチュートリアルでは、信頼できるトークン プロビジョニング サービスとして機能する Azure Functions を作成します。 このチュートリアルを使用して、独自のトークン プロビジョニング サービスをブートストラップできます。

このサービスは、Azure Communication Services に対するユーザーの認証を担当します。 Communication Services アプリケーションのユーザーは、チャット スレッドおよび VoIP 通話に参加するために `Access Token` が必要になります。 Azure Functions は、ユーザーと Communication Services の間で信頼できる仲介者として機能します。 これにより、リソース接続文字列をユーザーに公開せずにアクセス トークンをプロビジョニングできます。

詳細については、[クライアント - サーバー アーキテクチャ](../../concepts/client-and-server-architecture.md)および[認証と認可](../../concepts/authentication.md)の概念に関するドキュメントを参照してください。

## <a name="setting-up"></a>設定

### <a name="azure-functions-set-up"></a>Azure Functions の設定

最初に Azure Functions の基本構造を設定します。 設定のステップ バイ ステップの手順については、以下を参照してください。[Visual Studio Code を使用して関数を作成する](../../../azure-functions/create-first-function-vs-code-csharp.md?pivots=programming-language-javascript)

Azure Functions には、次の構成が必要です。

- [言語]JavaScript
- テンプレート:HTTP トリガー
- 認可レベル:匿名 (別の認可モデルを使用する場合は、後で切り替えることができます)
- 関数名:ユーザー定義

上記の構成を使用して [Azure Functions の手順](../../../azure-functions/create-first-function-vs-code-csharp.md?pivots=programming-language-javascript)を実行すると、Visual Studio Code に Azure Functions のプロジェクトがあり、そこに関数自体を含む `index.js` ファイルがあるはずです。 このファイル内のコードは以下のようになっているはずです。

```javascript

module.exports = async function (context, req) {
    context.log('JavaScript HTTP trigger function processed a request.');

    const name = (req.query.name || (req.body && req.body.name));
    const responseMessage = name
        ? "Hello, " + name + ". This HTTP triggered function executed successfully."
        : "This HTTP triggered function executed successfully. Pass a name in the query string or in the request body for a personalized response.";

    context.res = {
        // status: 200, /* Defaults to 200 */
        body: responseMessage
    };
}

```

次に、Azure Communication Services ライブラリのインストールに進みます。

### <a name="install-communication-services-libraries"></a>Communication Services ライブラリをインストールする

`Identity` ライブラリを使用して `User Access Tokens` を生成します。

`npm install` コマンドを使用して、JavaScript 用の Azure Communication Services ID クライアント ライブラリをインストールします。

```console

npm install @azure/communication-identity --save

```

`--save` オプションを使用すると、**package.json** ファイル内の依存関係としてライブラリが表示されます。

`index.js` ファイルの先頭で、`CommunicationIdentityClient` のインターフェイスをインポートします。

```javascript
const { CommunicationIdentityClient } = require('@azure/communication-identity');
```

## <a name="access-token-generation"></a>アクセス トークンの生成

Azure Functions で `User Access Tokens` を生成できるようにするには、最初に Communication Services リソースの接続文字列を使用する必要があります。

接続文字列の取得の詳細については、[リソース プロビジョニングのクイックスタート](../../quickstarts/create-communication-resource.md)に関するページを参照してください。

``` javascript
const connectionString = 'INSERT YOUR RESOURCE CONNECTION STRING'
```

次に、元の関数を変更して `User Access Tokens` を生成します。

`User Access Tokens` は、`createUser` メソッドからユーザーを作成することによって生成されます。 ユーザーが作成されたら、`issueToken` メソッドを使用して、Azure Functions から返されるそのユーザーのトークンを生成できます。

この例では、トークンのスコープを `voip` に構成します。 ご使用のアプリケーションに他のスコープが必要な場合があります。 詳しくは、[スコープ](../../quickstarts/access-tokens.md)に関するページを参照してください。

```javascript
module.exports = async function (context, req) {
    let tokenClient = new CommunicationIdentityClient(connectionString);

    const user = await tokenClient.createUser();

    const userToken = await tokenClient.issueToken(user, ["voip"]);

    context.res = {
        body: userToken
    };
}
```

既存の Communication Services `CommunicationUser` では、作成手順をスキップして、アクセス トークンを生成するだけで済みます。 詳細については、[ユーザー アクセス トークン作成のクイックスタート](../../quickstarts/access-tokens.md)に関するページを参照してください。

## <a name="test-the-azure-function"></a>Azure Functions をテストする

`F5` を使用して Azure Functions をローカルで実行します。 これにより、Azure Functions がローカルで初期化され、`http://localhost:7071/api/FUNCTION_NAME` によってアクセスできるようになります。 [ローカルでの実行](../../../azure-functions/create-first-function-vs-code-csharp.md?pivots=programming-language-javascript#run-the-function-locally)に関する追加のドキュメントを確認してください

お使いのブラウザーで URL を開くと、Communication のユーザー ID、トークン、トークンの有効期限を含む応答本文が表示されます。

:::image type="content" source="../media/trusted-service-sample-response.png" alt-text="作成された Azure Functions の応答例を示すスクリーンショット。":::

## <a name="deploy-the-function-to-azure"></a>関数を Azure にデプロイする

Azure 関数をデプロイするには、[ステップ バイ ステップの手順](../../../azure-functions/create-first-function-vs-code-csharp.md?pivots=programming-language-javascript#sign-in-to-azure)に従います。

まとめると、次のことを行う必要があります。
1. Visual Studio から Azure にサインインする
2. プロジェクトを Azure アカウントに発行します。 ここでは、既存のサブスクリプションを選択する必要があります。
3. Visual Studio ウィザードを使用して新しい Azure Functions リソースを作成するか、既存のリソースを使用します。 新しいリソースの場合は、目的のリージョン、ランタイム、一意の識別子に構成する必要があります。
4. デプロイが完了するまで待機する
5. 関数 🎉 を実行する

## <a name="run-azure-function"></a>Azure 関数を実行する

URL `http://<function-appn-ame>.azurewebsites.net/api/<function-name>` を使用して Azure 関数を実行する

URL を見つけるには、Visual Studio Code で関数を右クリックし、関数の URL をコピーします。

詳細については、[Azure 関数の実行](../../../azure-functions/create-first-function-vs-code-csharp.md?pivots=programming-language-javascript#run-the-function-in-azure)に関するページを参照してください。
