---
title: Azure Functions を使用してサーバーレス API を作成する | Microsoft Docs
description: Azure Functions を使用してサーバーレス API を作成する方法
services: functions
author: mattchenderson
manager: jeconnoc
ms.service: azure-functions
ms.devlang: multiple
ms.topic: tutorial
ms.date: 05/04/2017
ms.author: mahender
ms.custom: mvc
ms.openlocfilehash: f6a678e03818f1e1f2182b3b0dfab221d415dc72
ms.sourcegitcommit: a65b424bdfa019a42f36f1ce7eee9844e493f293
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/04/2019
ms.locfileid: "55698248"
---
# <a name="create-a-serverless-api-using-azure-functions"></a>Azure Functions を使用したサーバーレス API の作成

このチュートリアルでは、Azure Functions を使用して高度にスケーラブルな API を作成する方法について説明します。 Azure Functions には、組み込みの HTTP トリガーとバインドのコレクションが含まれており、作成者は Node.JS、C# などのさまざまな言語で簡単にエンドポイントを作成できます。 このチュートリアルでは、HTTP トリガーをカスタマイズして API の設計で特定の操作を処理します。 また、Azure Functions Proxies と統合してモック API をセットアップすることで、お使いの API を拡張する準備を行います。 これらの処理はすべて Functions のサーバーレス コンピューティング環境上で行われるため、リソースのスケーリングを考慮する必要はなく、API のロジックに集中できます。

## <a name="prerequisites"></a>前提条件 

[!INCLUDE [Previous quickstart note](../../includes/functions-quickstart-previous-topics.md)]

ここで作成する関数は、このチュートリアルの残りの箇所で使用します。

### <a name="sign-in-to-azure"></a>Azure へのサインイン

Azure Portal を開きます。 これを行うには、Azure アカウントで [https://portal.azure.com](https://portal.azure.com) にサインインします。

## <a name="customize-your-http-function"></a>HTTP 関数のカスタマイズ

既定では、HTTP によってトリガーされる関数は任意の HTTP メソッドを受け入れるように構成されます。 また、フォームの既定の URL は次のとおりです: `http://<yourapp>.azurewebsites.net/api/<funcname>?code=<functionkey>`。 クイックスタートの手順に従っている場合、`<funcname>` にはおそらく "HttpTriggerJS1" のように表示されています。 このセクションでは、`/api/hello` ルートに対する GET 要求にのみ応答するように関数を変更します。 

1. Azure Portal で関数に移動します。 左側のナビゲーションで、**[統合]** を選択します。

    ![HTTP 関数のカスタマイズ](./media/functions-create-serverless-api/customizing-http.png)

1. 次の表で指定されている HTTP トリガーの設定を使用します。

    | フィールド | 値の例 | 説明 |
    |---|---|---|
    | [許可されている HTTP メソッド] | 選択したメソッド | この関数の呼び出しに使用する HTTP メソッドを決定します |
    | [選択した HTTP メソッド] | GET | この関数の呼び出しには、選択した HTTP メソッドのみが使用できます |
    | [ルート テンプレート] | /hello | この関数の呼び出しに使用するルートを決定します |
    | 承認レベル | Anonymous | 省略可能:API キーを使用せずに関数にアクセスできるようにします |

    > [!NOTE] 
    > ルート テンプレートには `/api` ベース パス プレフィックスを含めないよう注意してください。このパス プレフィックスはグローバル設定で処理します。

1. **[Save]** をクリックします。

HTTP 関数をカスタマイズする方法の詳細については、「[Azure Functions における HTTP と Webhook のバインド](https://docs.microsoft.com/azure/azure-functions/functions-bindings-http-webhook)」をご覧ください。

### <a name="test-your-api"></a>API のテスト

次に、関数のテストを行い、新しい API サーフェスで機能していることを確認します。
1. 左側のナビゲーションに表示される関数名をクリックして、開発ページに戻ります。
1. **[関数の URL の取得]** をクリックし、URL をコピーします。 現在、`/api/hello` ルートが使用されていることを確認してください。
1. URL をブラウザーの新しいタブまたはお使いの REST クライアントにコピーします。 ブラウザーは既定で GET を使用します。
1. URL のクエリ文字列にパラメーターを追加します (例: `/api/hello/?name=John`)。
1. 動作していることを確認するには、Enter キーを押します。 "*Hello John*" という応答が表示されます。
1. また、別の HTTP メソッドを使用してエンドポイントを呼び出し、関数が実行されていないことを確認することもできます。 このためには、cURL、Postman、Fiddler などの REST クライアントを使用する必要があります。

## <a name="proxies-overview"></a>Proxies の概要

次のセクションでは、プロキシを経由して API サーフェスを使用します。 Azure Functions プロキシを使うと、他のリソースに要求を転送できます。 HTTP トリガーの場合と同様に HTTP エンドポイントを定義しますが、エンドポイントの呼び出しの際に実行するコードを記述する代わりに、リモートでの実装のために URL を提供します。 これにより、クライアントが簡単に使用できる単一の API サーフェスに複数の API ソースをまとめることができます。 これは、API をマイクロサービスとして構築する場合に特に役立ちます。

プロキシは、以下のような任意の HTTP リソースを指定できます。
- Azure Functions 
- [Azure App Service](https://docs.microsoft.com/azure/app-service/overview) 内の API アプリ
- [App Service on Linux](https://docs.microsoft.com/azure/app-service/containers/app-service-linux-intro) 内の Docker コンテナー
- その他のホストされている API

プロキシについて詳しくは、「[Azure Functions プロキシの操作]」をご覧ください。

## <a name="create-your-first-proxy"></a>最初のプロキシの作成

このセクションでは、API 全体に対してフロントエンドとして機能する新しいプロキシを作成します。 

### <a name="setting-up-the-frontend-environment"></a>フロントエンド環境のセットアップ

「[Function App を作成する](https://docs.microsoft.com/azure/azure-functions/functions-create-first-azure-function#create-a-function-app)」の手順を繰り返し、プロキシを作成する新しい Function App を作成します。 この新しいアプリの URL は、API のフロントエンドとして機能し、以前編集した Function App はバックエンドとして機能します。

1. ポータルで新しいフロントエンドの Function App に移動します。
1. **[プラットフォーム機能]**、**[アプリケーションの設定]** の順に選択します。
1. 下にスクロールして、キーと値のペアが格納されている **[アプリケーションの設定]** に移動し、キー "HELLO_HOST" を使用して新しい設定を作成します。 バックエンドの Function App のホストに `<YourBackendApp>.azurewebsites.net` などの値を設定します。 これは以前 HTTP 関数をテストするときにコピーした URL の一部です。 後で構成の際にこの設定を参照します。

    > [!NOTE] 
    > ハード コーディングよるプロキシの環境依存を防ぐために、ホストの構成に対してアプリ設定を使用することをお勧めします。 アプリ設定を使用すると、プロキシの構成を環境間で移動でき、その環境に合わせたアプリ設定が適用されます。

1. **[保存]** をクリックします。

### <a name="creating-a-proxy-on-the-frontend"></a>フロントエンドのプロキシの作成

1. ポータルでフロントエンドの Function App に移動します。
1. 左側のナビゲーションで、[プロキシ] の横にあるプラス記号 [+] をクリックします。
    ![プロキシの作成](./media/functions-create-serverless-api/creating-proxy.png)
1. 次の表で指定されているプロキシの設定を使用します。 

    | フィールド | 値の例 | 説明 |
    |---|---|---|
    | Name | HelloProxy | 管理にのみ使用するフレンドリ名です |
    | [ルート テンプレート] | /api/remotehello | このプロキシの呼び出しに使用するルートを決定します |
    | [バックエンド URL] | https://%HELLO_HOST%/api/hello | 要求の送信先となるエンドポイントを指定します |
    
1. Proxies は `/api` ベース パス プレフィックスを提供しないことに注意してください。パス プレフィックスはルート テンプレートで指定する必要があります。
1. `%HELLO_HOST%` 構文は、以前作成したアプリ設定を参照します。 解決済みの URL は元の関数を指定します。
1. **Create** をクリックしてください。
1. プロキシの URL をコピーし、ブラウザー内、または任意の HTTP クライアントでテストすることで、新しいプロキシを試すことができます。
    1. 匿名関数の場合は、以下を使用します。
        1. `https://YOURPROXYAPP.azurewebsites.net/api/remotehello?name="Proxies"`
    1. 承認がある関数の場合は、以下を使用します。
        1. `https://YOURPROXYAPP.azurewebsites.net/api/remotehello?code=YOURCODE&name="Proxies"`

## <a name="create-a-mock-api"></a>モック API の作成

次に、プロキシを使用してソリューションのモック API を作成します。 これにより、バックエンドを完全に実装する必要なく、クライアント開発を進めることができます。 開発の後行程で、このロジックをサポートしプロキシをリダイレクトする新しい Function App を作成できます。

このモック API を作成するには、新しいプロキシを作成します (今回は [App Service Editor](https://github.com/projectkudu/kudu/wiki/App-Service-Editor) を使用します)。 作成を開始するには、Azure Portal で Function App に移動します。 **[プラットフォーム機能]** を選択し、**[開発ツール]** で **[App Service Editor]** を見つけます。 これをクリックすると、新しいタブで App Service Editor が開きます。

左側のナビゲーションで、`proxies.json` を選択します。 これは、すべてのプロキシの構成が格納されるファイルです。 [Functions のデプロイ方法](https://docs.microsoft.com/azure/azure-functions/functions-continuous-deployment)で記載されている方法のいずれかを使用する場合、このファイルはソース管理で保持することになります。 このファイルの詳細については、[Proxies の詳細な構成](https://docs.microsoft.com/azure/azure-functions/functions-proxies#advanced-configuration)に関するページをご覧ください。

ここまでの手順に従っていれば、proxies.json ファイルは次のようになっているはずです。

```json
{
    "$schema": "http://json.schemastore.org/proxies",
    "proxies": {
        "HelloProxy": {
            "matchCondition": {
                "route": "/api/remotehello"
            },
            "backendUri": "https://%HELLO_HOST%/api/hello"
        }
    }
}
```

次に、モック API を追加します。 proxies.json ファイルを次のように書き換えてください。

```json
{
    "$schema": "http://json.schemastore.org/proxies",
    "proxies": {
        "HelloProxy": {
            "matchCondition": {
                "route": "/api/remotehello"
            },
            "backendUri": "https://%HELLO_HOST%/api/hello"
        },
        "GetUserByName" : {
            "matchCondition": {
                "methods": [ "GET" ],
                "route": "/api/users/{username}"
            },
            "responseOverrides": {
                "response.statusCode": "200",
                "response.headers.Content-Type" : "application/json",
                "response.body": {
                    "name": "{username}",
                    "description": "Awesome developer and master of serverless APIs",
                    "skills": [
                        "Serverless",
                        "APIs",
                        "Azure",
                        "Cloud"
                    ]
                }
            }
        }
    }
}
```

backendUri プロパティを変更することなく、"GetUserByName" という新しいプロキシを追加しています。 別のリソースを呼び出す代わりに、応答のオーバーライドによって Proxies からの既定の応答を変更します。 要求と応答のオーバーライドは、バックエンド URL と併用することもできます。 これは、レガシ システムにプロキシする際に特に役立ちます。その際は、ヘッダー、クエリ パラメーターなどを変更する必要がある場合があります。要求と応答のオーバーライドの詳細については、[Proxies での要求と応答の変更](https://docs.microsoft.com/azure/azure-functions/functions-proxies)に関する記事をご覧ください。

ブラウザーまたはお使いの REST クライアントを使用して `<YourProxyApp>.azurewebsites.net/api/users/{username}` エンドポイントを呼び出し、モック API をテストします。 _{username}_ をユーザー名を表す文字列値に必ず置き換えてください。

## <a name="next-steps"></a>次の手順

このチュートリアルでは、Azure Functions の API を作成しカスタマイズする方法について説明します。 また、モックなどの複数の API をまとめて 1 つの API サーフェスにする方法についても説明します。 これらの手法を使用することで、Azure Functions のサーバーレス コンピューティング モデルで API を実行しながら、複雑な API も構築できます。

次のリファレンスは、API の開発をさらに進める際に役立ちます。

- [Azure Functions での HTTP のバインド](https://docs.microsoft.com/azure/azure-functions/functions-bindings-http-webhook)
- [Azure Functions プロキシの操作]
- [Azure Functions API (プレビュー) のドキュメント](https://docs.microsoft.com/azure/azure-functions/functions-api-definition-getting-started)


[Create your first function]: https://docs.microsoft.com/azure/azure-functions/functions-create-first-azure-function
[Azure Functions プロキシの操作]: https://docs.microsoft.com/azure/azure-functions/functions-proxies
