---
title: Azure Functions で HTTP エンドポイントをカスタマイズする
description: Azure Functions で HTTP トリガー エンドポイントをカスタマイズする方法について説明します。
author: mattchenderson
ms.topic: conceptual
ms.date: 04/27/2020
ms.author: mahender
ms.custom: mvc
ms.openlocfilehash: 7375a46245fbe523ddf0512bb5a55371adff64e9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "98683744"
---
# <a name="customize-an-http-endpoint-in-azure-functions"></a>Azure Functions で HTTP エンドポイントをカスタマイズする

この記事では、Azure Functions を使用して高度にスケーラブルな API を作成する方法について説明します。 Azure Functions には、組み込みの HTTP トリガーとバインドのコレクションが含まれており、作成者は Node.js、C# などのさまざまな言語で簡単にエンドポイントを作成できます。 この記事では、HTTP トリガーをカスタマイズして API の設計で特定の操作を処理します。 また、Azure Functions プロキシと統合してモック API をセットアップすることで、お使いの API を拡張する準備を行います。 これらのタスクは Functions のサーバーレス コンピューティング環境上で行われるため、リソースのスケーリングを考慮する必要はなく、API のロジックに集中できます。

## <a name="prerequisites"></a>前提条件 

[!INCLUDE [Previous quickstart note](../../includes/functions-quickstart-previous-topics.md)]

ここで作成する関数は、この記事の残りの箇所で使用します。

## <a name="sign-in-to-azure"></a>Azure へのサインイン

Azure アカウントで [Azure Portal](https://portal.azure.com) にサインインします。

## <a name="customize-your-http-function"></a>HTTP 関数のカスタマイズ

既定では、HTTP トリガー関数は任意の HTTP メソッドを受け入れるように構成されます。 既定の URL `http://<yourapp>.azurewebsites.net/api/<funcname>?code=<functionkey>` を使用することもできます。 このセクションでは、`/api/hello` の GET 要求にのみ応答するように関数を変更します。 

1. Azure Portal で関数に移動します。 左側のメニューで **[統合]** を選択し、 **[トリガー]** で **[HTTP (req)]** を選択します。

    :::image type="content" source="./media/functions-create-serverless-api/customizing-http.png" alt-text="HTTP 関数のカスタマイズ":::

1. 次の表で指定されている HTTP トリガーの設定を使用します。

    | フィールド | 値の例 | 説明 |
    |---|---|---|
    | [ルート テンプレート] | /hello | この関数の呼び出しに使用するルートを決定します |
    | 承認レベル | Anonymous | 省略可能:API キーを使用せずに関数にアクセスできるようにします |
    | [選択した HTTP メソッド] | GET | この関数の呼び出しには、選択した HTTP メソッドのみが使用できます |

    ルート テンプレートには `/api` ベース パス プレフィックスを含めませんでした。これはグローバル設定で処理されるためです。

1. **[保存]** を選択します。

HTTP 関数のカスタマイズの詳細については、[Azure Functions の HTTP バインド](./functions-bindings-http-webhook.md)に関する記事をご覧ください。

### <a name="test-your-api"></a>API のテスト

次に、関数のテストを行い、新しい API サーフェスでどのように機能するかを確認します。
1. 関数のページで、左側のメニューから **[コードとテスト]** を選択します。

1. 上部のメニューから **[関数の URL の取得]** を選択し、URL をコピーします。 `/api/hello` パスが使用されていることを確認します。
 
1. URL をブラウザーの新しいタブまたはお使いの REST クライアントにコピーします。 

   ブラウザーでは既定で GET が使用されます。
 
1. URL のクエリ文字列にパラメーターを追加します。 

   たとえば、「 `/api/hello/?name=John` 」のように入力します。
 
1. Enter を押して動作していることを確認します。 "*Hello John*" という応答が表示されます。

1. また、別の HTTP メソッドを使用してエンドポイントを呼び出し、関数が実行されていないことを確認することもできます。 そのためには、cURL、Postman、Fiddler などの REST クライアントを使用します。

## <a name="proxies-overview"></a>Proxies の概要

次のセクションでは、プロキシを経由して API サーフェスを使用します。 Azure Functions プロキシを使うと、他のリソースに要求を転送できます。 HTTP トリガーと同様に、HTTP エンドポイントを定義します。 ただし、エンドポイントの呼び出しの際に実行するコードを記述する代わりに、リモート実装への URL を指定します。 これにより、クライアントが簡単に使用できる単一の API サーフェスに複数の API ソースをまとめることができ、これは API をマイクロサービスとしてビルドする場合に便利です。

プロキシは、以下のような任意の HTTP リソースを指定できます。
- Azure Functions 
- [Azure App Service](../app-service/overview.md) 内の API アプリ
- [App Service on Linux](../app-service/overview.md#app-service-on-linux) 内の Docker コンテナー
- その他のホストされている API

プロキシについて詳しくは、「[Azure Functions プロキシの操作]」をご覧ください。

## <a name="create-your-first-proxy"></a>最初のプロキシの作成

このセクションでは、API 全体に対してフロントエンドとして機能する新しいプロキシを作成します。 

### <a name="setting-up-the-frontend-environment"></a>フロントエンド環境のセットアップ

「[Function App を作成する](./functions-get-started.md)」の手順を繰り返し、プロキシを作成する新しい Function App を作成します。 この新しいアプリの URL は、API のフロントエンドとして機能し、以前編集した関数アプリはバックエンドとして機能します。

1. ポータルで新しいフロントエンドの Function App に移動します。
1. **[プラットフォーム機能]** 、 **[アプリケーションの設定]** の順に選択します。
1. 下にスクロールして、キーと値のペアが格納されている **[アプリケーションの設定]** に移動し、キー `HELLO_HOST` を使用して新しい設定を作成します。 バックエンドの Function App のホストに `<YourBackendApp>.azurewebsites.net` などの値を設定します。 この値は以前に HTTP 関数をテストするときにコピーした URL の一部です。 後で構成の際にこの設定を参照します。

    > [!NOTE] 
    > ハード コーディングよるプロキシの環境依存を防ぐために、ホストの構成に対してアプリ設定を使用することをお勧めします。 アプリ設定を使用すると、プロキシの構成を環境間で移動でき、その環境に合わせたアプリ設定が適用されます。

1. **[保存]** を選択します。

### <a name="creating-a-proxy-on-the-frontend"></a>フロントエンドのプロキシの作成

1. ポータルでフロントエンドの関数アプリに戻ります。

1. 左側のメニューで、 **[プロキシ]** を選択し、 **[追加]** を選択します。 

1. **[新しいプロキシ]** ページで、次の表の設定を使用してから、 **[作成]** を選択します。

    | フィールド | 値の例 | 説明 |
    |---|---|---|
    | 名前 | HelloProxy | 管理にのみ使用するフレンドリ名です |
    | [ルート テンプレート] | /api/remotehello | このプロキシの呼び出しに使用するルートを決定します |
    | [バックエンド URL] | https://%HELLO_HOST%/api/hello | 要求の送信先となるエンドポイントを指定します |

    
    :::image type="content" source="./media/functions-create-serverless-api/creating-proxy.png" alt-text="プロキシの作成":::

    Azure Functions プロキシでは `/api` ベース パス プレフィックスは提供されません。これはルート テンプレートに含める必要があります。 `%HELLO_HOST%` 構文では、以前作成したアプリ設定が参照されます。 解決済みの URL は元の関数を指定します。

1. プロキシの URL をコピーし、ブラウザー内、または任意の HTTP クライアントでテストすることで、新しいプロキシを試します。
    - 匿名関数の場合は、`https://YOURPROXYAPP.azurewebsites.net/api/remotehello?name="Proxies"` を使用します。
    - 承認がある関数の場合は、`https://YOURPROXYAPP.azurewebsites.net/api/remotehello?code=YOURCODE&name="Proxies"` を使用します。

## <a name="create-a-mock-api"></a>モック API の作成

次に、プロキシを使用してソリューションのモック API を作成します。 このプロキシにより、バックエンドを完全に実装する必要なく、クライアント開発を進めることができます。 開発の後行程で、このロジックをサポートしプロキシをリダイレクトする新しい関数アプリを作成できます。

このモック API を作成するには、新しいプロキシを作成します (今回は [App Service Editor](https://github.com/projectkudu/kudu/wiki/App-Service-Editor) を使用します)。 作成を開始するには、Azure Portal で Function App に移動します。 **[プラットフォーム機能]** を選択し、 **[開発ツール]** で **[App Service Editor]** を見つけます。 App Service Editor が新しいタブで開きます。

左側のナビゲーションで、`proxies.json` を選択します。 このファイルには、すべてのプロキシの構成が格納されます。 [Functions のデプロイ方法](./functions-continuous-deployment.md)のいずれかを使用する場合、このファイルはソース管理で保持します。 このファイルの詳細については、[Proxies の詳細な構成](./functions-proxies.md#advanced-configuration)に関するページをご覧ください。

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

次に、モック API を追加します。 proxies.json ファイルを次のコードで置き換えます。

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

このコードにより、`backendUri` プロパティを指定せずに、新しいプロキシ `GetUserByName` が追加されます。 別のリソースを呼び出す代わりに、応答のオーバーライドによって Proxies からの既定の応答を変更します。 要求と応答のオーバーライドは、バックエンド URL と併用することもできます。 この手法は、ヘッダー、クエリ パラメーターなどを変更する必要がある場合がある、レガシ システムにプロキシする際に特に役立ちます。 要求と応答のオーバーライドの詳細については、[Proxies での要求と応答の変更](./functions-proxies.md)に関する記事をご覧ください。

ブラウザーまたはお使いの REST クライアントを使用して `<YourProxyApp>.azurewebsites.net/api/users/{username}` エンドポイントを呼び出し、モック API をテストします。 _{username}_ をユーザー名を表す文字列値に必ず置き換えてください。

## <a name="next-steps"></a>次のステップ

この記事では、Azure Functions の API を作成しカスタマイズする方法について説明します。 また、モックなどの複数の API をまとめて 1 つの API サーフェスにする方法についても説明します。 これらの手法を使用することで、Azure Functions のサーバーレス コンピューティング モデルで API を実行しながら、複雑な API も構築できます。

次のリファレンスは、API の開発をさらに進める際に役立ちます。

- [Azure Functions での HTTP のバインド](./functions-bindings-http-webhook.md)
- [Azure Functions プロキシの操作]
- [Azure Functions API (プレビュー) のドキュメント](./functions-openapi-definition.md)


[Create your first function]: ./functions-get-started.md
[Azure Functions プロキシの操作]: ./functions-proxies.md
