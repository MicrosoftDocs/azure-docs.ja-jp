---
title: チュートリアル:Azure AD B2C を使用して Node.js Web API を保護し、シングルページ アプリケーション (SPA) へのアクセスを許可する
titleSuffix: Azure AD B2C
description: このチュートリアルでは、Active Directory B2C を使用して Node.js Web API を保護し、シングルページ アプリケーションから呼び出す方法について説明します。
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.author: mimart
ms.date: 04/04/2020
ms.custom: mvc, devx-track-js
ms.topic: tutorial
ms.service: active-directory
ms.subservice: B2C
ms.openlocfilehash: 737810a7d07d0d97b2e42acffa17fdd32986c48b
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "93421092"
---
# <a name="tutorial-protect-and-grant-access-to-a-nodejs-web-api-from-a-single-page-application-with-azure-ad-b2c"></a>チュートリアル:Azure AD B2C を使用して、シングルページ アプリケーションから Node.js Web API へのアクセスを保護および許可する

このチュートリアルでは、Azure Active Directory B2C (Azure AD B2C) で保護された Node.js Web API をシングルページ アプリケーションから呼び出す方法について説明します。

2 部構成のシリーズの 2 番目の部分であるこのチュートリアルでは、次の作業を行います。

> [!div class="checklist"]
> * Azure AD B2C テナントに Web API アプリケーション登録を作成する
> * Web API のスコープを構成する
> * Web API に対するアクセス許可を付与する
> * テナントで動作するように Web API コード サンプルを変更する

このシリーズの[最初のチュートリアル](tutorial-single-page-app.md)では、コード サンプルをダウンロードし、Azure AD B2C テナントのユーザー フローでユーザーのサインインを行うように変更しました。

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>前提条件

* 「[チュートリアル: Azure AD B2C を使用してシングルページ アプリケーションで認証を有効にする](tutorial-single-page-app.md)」の手順を完了し、前提条件を満たしていること。
* [Visual Studio Code](https://code.visualstudio.com/) または別のコード エディター
* [Node.js](https://nodejs.org/en/download/)

## <a name="add-a-web-api-application"></a>Web API アプリケーションを追加する

[!INCLUDE [active-directory-b2c-appreg-webapi](../../includes/active-directory-b2c-appreg-webapi.md)]

## <a name="configure-scopes"></a>スコープを構成する

スコープを使用すると、保護されたリソースへのアクセスを統制できます。 スコープは、スコープベースのアクセス制御を実装するために Web API によって使用されます。 たとえば、読み取りアクセスと書き込みアクセス両方を持つユーザーもいれば、読み取り専用アクセス許可を持つユーザーもいます。 このチュートリアルでは、Web API の読み取りと書き込み両方のアクセス許可を定義します。

[!INCLUDE [active-directory-b2c-scopes](../../includes/active-directory-b2c-scopes.md)]

`demo.read` スコープの **[スコープ]** に示されている値を記録しておきます。これは、後の手順でシングルページ アプリケーションを構成するときに使用します。 完全なスコープ値は `https://contosob2c.onmicrosoft.com/api/demo.read` のようになります。

## <a name="grant-permissions"></a>[アクセス許可の付与]

保護された Web API を別のアプリケーションから呼び出すには、その Web API へのアクセス許可をそのアプリケーションに付与する必要があります。

前提条件のチュートリアルでは、*spaapp1* という名前のシングルページ アプリケーションを作成しました。 このチュートリアルでは、前のセクションで作成した Web API である *spaapp1* を呼び出すようにそのアプリケーションを構成します。

[!INCLUDE [active-directory-b2c-permissions-api](../../includes/active-directory-b2c-permissions-api.md)]

これで、シングルページ Web アプリケーションに、指定されたスコープの、保護された Web API へのアクセス許可が付与されました。 ユーザーは、シングルページ アプリケーションを使用するために Azure AD B2C で認証を行います。 シングルページ アプリは、保護された Web API にアクセスするために、Azure AD B2C からアクセス トークンを取得します。

## <a name="configure-the-sample"></a>サンプルの構成

Web API を登録し、スコープを定義したので、Azure AD B2C テナントで動作するように Web API コードを構成します。 このチュートリアルでは、GitHub からダウンロードするサンプル Node.js Web API を構成します。

[\*.zip アーカイブをダウンロード](https://github.com/Azure-Samples/active-directory-b2c-javascript-nodejs-webapi/archive/master.zip)するか、GitHub からサンプルの Web API プロジェクトを複製します。 また、GitHub で [Azure-Samples/active-directory-b2c-javascript-nodejs-webapi](https://github.com/Azure-Samples/active-directory-b2c-javascript-nodejs-webapi) プロジェクトを直接参照することもできます。

```console
git clone https://github.com/Azure-Samples/active-directory-b2c-javascript-nodejs-webapi.git
```

### <a name="configure-the-web-api"></a>Web API を構成する

1. コード エディターで *config.json* ファイルを開きます。
1. 変数の値を変更して、前に作成したアプリケーション登録のものを反映させます。 また、前提条件の一部として作成したユーザー フローを使用して、`policyName` を更新します。 たとえば、*B2C_1_signupsignin1* などです。
    
    ```json
    "credentials": {
        "tenantName": "<your-tenant-name>",
        "clientID": "<your-webapi-application-ID>"
    },
    "policies": {
        "policyName": "B2C_1_signupsignin1"
    },
    "resource": {
        "scope": ["demo.read"] 
    },
    ```

#### <a name="enable-cors"></a>CORS を有効にする

シングルページ アプリケーションに Node.js Web API の呼び出しを許可するには、Web API で [CORS](https://expressjs.com/en/resources/middleware/cors.html) を有効にする必要があります。 実稼働アプリケーションでは、どのドメインが要求を行っているかに注意する必要がありますが、このチュートリアルでは、すべてのドメインからの要求を許可します。

CORS を有効にするには、次のミドルウェアを使用します。 このチュートリアルの Node.js Web API コード サンプルでは、*index.js* ファイルに既に追加されています。

```javascript
app.use((req, res, next) => {
    res.header("Access-Control-Allow-Origin", "*");
    res.header("Access-Control-Allow-Headers", "Authorization, Origin, X-Requested-With, Content-Type, Accept");
    next();
});
```

### <a name="configure-the-single-page-application"></a>シングルページ アプリケーションを構成する

このシリーズの [前のチュートリアル](tutorial-single-page-app.md)のシングルページ アプリケーション (SPA) は、ユーザーのサインアップとサインインに Azure AD B2C を使用し、既定で、*frabrikamb2c* デモ テナントによって保護されている Node.js Web API を呼び出します。

このセクションでは、"*自分の*" Azure AD B2C テナントによって保護されている (そして、自分のローカル マシンで実行する) Node.js Web API を呼び出すようにシングルページ Web アプリケーションを更新します。

SPA 内の設定を変更するには:

1. 前のチュートリアルでダウンロードまたは複製した [active-directory-b2c-javascript-msal-singlepageapp][github-js-spa] プロジェクトで、*JavaScriptSPA* フォルダー内の *apiConfig.js* ファイルを開きます。
1. 先ほど作成した *demo.read* スコープの URI と Web API の URL を使用してサンプルを構成します。
    1. `apiConfig` 定義で、`b2cScopes` 値を、*demo.read* スコープの完全な URI (前に記録した **スコープ** 値) に置き換えます。
    1. `webApi` 値のドメインを、前の手順で Web API アプリケーションを登録したときに追加したリダイレクト URI に変更します。

    API には `/hello` エンドポイントでアクセス可能なため、URI の */hello* はそのままにします。

    `apiConfig` の定義は次のコード ブロックのようになります。ただし、`<your-tenant-name>` の場所には B2C テナントの名前が入ります。

    ```javascript
    // The current application coordinates were pre-registered in a B2C tenant.
    const apiConfig = {
      b2cScopes: ["https://<your-tenant-name>.onmicrosoft.com/api/demo.read"],
      webApi: "http://localhost:5000/hello" // '/hello' should remain in the URI
    };
    ```

## <a name="run-the-spa-and-web-api"></a>SPA と Web API を実行する

これで、シングルページ アプリケーションの API へのスコープ付きアクセスをテストする準備ができました。 ローカル マシンで Node.js Web API とサンプル JavaScript シングルページ アプリケーションの両方を実行します。 次に、シングルページ アプリケーションにサインインし、 **[Call API]\(API の呼び出し\)** ボタンを選択して、保護されている API に対する要求を開始します。

このチュートリアルでは、両方のアプリケーションはローカルで実行されていますが、それらは、Azure AD B2C を使用して、セキュリティで保護されたサインアップ/サインインを実行し、保護された Web API へのアクセスを許可するように構成されています。

### <a name="run-the-nodejs-web-api"></a>Node.js Web API を実行する

1. コンソール ウィンドウを開き、Node.js Web API サンプルを含むディレクトリに移動します。 次に例を示します。

    ```console
    cd active-directory-b2c-javascript-nodejs-webapi
    ```

1. 次のコマンドを実行します。

    ```console
    npm install && npm update
    node index.js
    ```

    コンソール ウィンドウには、アプリケーションがホストされている場所のポート番号が表示されます。

    ```console
    Listening on port 5000...
    ```

### <a name="run-the-single-page-app"></a>シングルページ アプリを実行する

1. 別のコンソール ウィンドウを開き、JavaScript SPA サンプルを含むディレクトリに移動します。 次に例を示します。

    ```console
    cd ms-identity-b2c-javascript-spa
    ```

1. 次のコマンドを実行します。

    ```console
    npm install && npm update
    npm start
    ```

    コンソール ウィンドウには、アプリケーションがホストされている場所のポート番号が表示されます。

    ```console
    Listening on port 6420...
    ```

1. アプリケーションを表示するには、ブラウザーで `http://localhost:6420` に移動します。

    ![ブラウザーに表示されたシングルページ アプリケーションのサンプル アプリ](./media/tutorial-single-page-app-webapi/tutorial-01-sample-app-browser.png)

1. [前のチュートリアル](tutorial-single-page-app.md)で使用したメール アドレスとパスワードを使用してサインインします。 ログインが成功すると、`User 'Your Username' logged-in` というメッセージが表示されます。
1. **[Call API]** ボタンを選択します。 SPA は Azure AD B2C から承認許可を取得し、保護された Web API にアクセスして、ログインしているユーザーの名前を表示します。

    ![API によって返されるユーザー名の JSON 結果を示す、ブラウザーのシングルページ アプリケーション](./media/tutorial-single-page-app-webapi/tutorial-02-call-api.png)

## <a name="next-steps"></a>次のステップ

このチュートリアルでは、次のことを行いました。

> [!div class="checklist"]
> * Azure AD B2C テナントに Web API アプリケーション登録を作成しました
> * Web API のスコープを構成しました
> * Web API に対するアクセス許可を付与しました
> * テナントで動作するように Web API コード サンプルを変更しました

これで、SPA が保護された Web API に対してリソースを要求したことを確認できました。次は、これらの種類のアプリケーションが相互にまたは Azure AD B2C との間でどのように作用するかについて、理解を深めます。

> [!div class="nextstepaction"]
> [Azure Active Directory B2C で使用できるアプリケーションの種類 >](application-types.md)

<!-- Links - EXTERNAL -->
[github-js-spa]: https://github.com/Azure-Samples/active-directory-b2c-javascript-msal-singlepageapp
