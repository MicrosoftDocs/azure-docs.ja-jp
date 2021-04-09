---
title: Azure Functions でプロキシを操作する
description: Azure Functions プロキシ の使用方法の概要
ms.topic: conceptual
ms.date: 01/22/2018
ms.openlocfilehash: fb263239f99bcb4ec4c893b700d5c1cce078659f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "96601375"
---
# <a name="work-with-azure-functions-proxies"></a>Azure Functions プロキシの操作

この記事では、Azure Functions プロキシ を構成および操作する方法について説明します。 この機能を使用すると、他のリソースによって実装される、関数アプリのエンドポイントを指定することができます。 これらのプロキシを使用して、大きな API を複数の関数アプリに分割できます (マイクロサービス アーキテクチャでのように)。その場合でも、クライアントには単一の API サーフェスとして表示されます。

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

> [!NOTE] 
> プロキシの実行には標準の Functions の料金が適用されます。 詳細については、[Azure Functions の価格](https://azure.microsoft.com/pricing/details/functions/)に関するページを参照してください。

## <a name="create-a-proxy"></a><a name="create"></a>プロキシを作成する

このセクションでは、Functions ポータルでプロキシを作成する方法を示します。

1. [Azure Portal] を開き、関数アプリに移動します。
2. 左側のウィンドウで、 **[新しいプロキシ]** を選択します。
3. プロキシの名前を指定します。
4. **ルート テンプレート** と **HTTP メソッド** を指定して、この関数アプリで公開されるエンドポイントを構成します。 これらのパラメーターは、[HTTP トリガー]の規則に従って動作します。
5. **バックエンド URL** を他のエンドポイントに設定します。 このエンドポイントは、別の関数アプリ内の関数にすることも、他の任意の API にすることもできます。 静的な値である必要はありません。[アプリケーション設定]や[元のクライアント要求のパラメーター]を参照することもできます。
6. **Create** をクリックしてください。

これで、プロキシが関数アプリの新しいエンドポイントになりました。 クライアントの観点からは、Azure Functions の HttpTrigger と同じです。 プロキシの URL をコピーし、任意の HTTP クライアントでテストすることで、新しいプロキシを試してみることができます。

## <a name="modify-requests-and-responses"></a><a name="modify-requests-responses"></a>要求と応答を変更する

バックエンドに対する要求やバックエンドからの応答には、Azure Functions プロキシを使って変更を加えることができます。 この変換時には、「[変数を使用する]」で定義している変数を使うことができます。

### <a name="modify-the-back-end-request"></a><a name="modify-backend-request"></a>バックエンドへの要求を変更する

既定では、バックエンドへの要求は、元の要求のコピーとして初期化されます。 バックエンドの URL を設定することに加え、HTTP メソッドやヘッダー、クエリ文字列のパラメーターに変更を加えることができます。 変更後の値から、[アプリケーション設定]や[元のクライアント要求のパラメーター]を参照することが可能です。

バックエンドへの要求は、ポータルで [proxy detail]\(プロキシの詳細\) ページの *[request override]\(要求のオーバーライド\)* セクションを展開して変更することができます。 

### <a name="modify-the-response"></a><a name="modify-response"></a>応答を変更する

既定では、クライアントへの応答は、バックエンドからの応答のコピーとして初期化されます。 応答の状態コード、理由の文字列、ヘッダー、本文には、変更を加えることができます。 変更後の値から、[アプリケーション設定]や[元のクライアント要求のパラメーター]、[バックエンドからの応答のパラメーター]を参照することが可能です。

バックエンドへの要求は、ポータルで [proxy detail]\(プロキシの詳細\) ページの *[response override]\(応答のオーバーライド\)* セクションを展開して変更することができます。 

## <a name="use-variables"></a><a name="using-variables"></a>変数を使用する

プロキシの構成は必ずしも静的である必要はありません。 条件に応じて元のクライアント要求やバックエンドからの応答、アプリケーション設定から得られる変数を使うことができます。

### <a name="reference-local-functions"></a><a name="reference-localhost"></a>ローカル関数を参照する
`localhost` を使用して、往復のプロキシ要求なしで、同じ関数アプリ内の関数を直接参照することができます。

`"backendUri": "https://localhost/api/httptriggerC#1"` は、ローカルの HTTP トリガーされた関数をルート `/api/httptriggerC#1` で参照します。

 
>[!Note]  
>関数が "*function、admin、または sys*" の承認レベルを使用している場合は、元の関数 URL に従って code と clientId を指定する必要があります。 この場合、参照は次のようになります: `"backendUri": "https://localhost/api/httptriggerC#1?code=<keyvalue>&clientId=<keyname>"`。これらのキーを[アプリケーション設定]に格納し、プロキシでそれらを参照することをお勧めします。 これにより、ソース コードにシークレットを格納しなくて済みます。 

### <a name="reference-request-parameters"></a><a name="request-parameters"></a>要求のパラメーターを参照する

要求のパラメーターは、バックエンド URL プロパティの入力として、または要求や応答に加える変更の一部として使うことができます。 パラメーターには、ベース プロキシの構成に指定されているルート テンプレートに由来するものもあれば、受信要求のプロパティに由来するものもあります。

#### <a name="route-template-parameters"></a>ルート テンプレートのパラメーター
ルート テンプレートに使われているパラメーターは、名前で参照することができます。 パラメーター名は中かっこ ({}) で囲みます。

たとえば、プロキシに `/pets/{petId}` のようなルート テンプレートがある場合、バックエンド URL には `https://<AnotherApp>.azurewebsites.net/api/pets/{petId}` のようにして、`{petId}` の値を含めることができます。 ルート テンプレートが `/api/{*restOfPath}` のようにワイルドカードで終了している場合、値 `{restOfPath}` は受信要求の残りのパス セグメントの文字列表現になります。

#### <a name="additional-request-parameters"></a>要求のパラメーター (その他)
構成の値には、ルート テンプレートのパラメーターに加え、次の値を使うことができます。

* **{request.method}** :元の要求に使われていた HTTP メソッド。
* **{request.headers.\<HeaderName\>}** :元の要求から読み取り可能なヘッダー。 *\<HeaderName\>* は、読み取るヘッダーの名前に置き換えます。 該当するヘッダーが要求に含まれていない場合、この値は空の文字列になります。
* **{request.querystring.\<ParameterName\>}** :元の要求から読み取り可能なクエリ文字列パラメーター。 *\<ParameterName\>* は、読み取るパラメーターの名前に置き換えます。 該当するパラメーターが要求に含まれていない場合、この値は空の文字列になります。

### <a name="reference-back-end-response-parameters"></a><a name="response-parameters"></a>バックエンドからの応答のパラメーターを参照する

応答のパラメーターは、クライアントへの応答に加える変更の一部として使うことができます。 構成の値には、次の値を使うことができます。

* **{backend.response.statusCode}** :バックエンドからの応答で返される HTTP 状態コード。
* **{backend.response.statusReason}** :バックエンドからの応答で返される HTTP 理由文字列。
* **{backend.response.headers.\<HeaderName\>}** :バックエンドの応答から読み取り可能なヘッダー。 *\<HeaderName\>* は、読み取るヘッダーの名前に置き換えます。 該当するヘッダーが応答に含まれていない場合、この値は空の文字列になります。

### <a name="reference-application-settings"></a><a name="use-appsettings"></a>アプリケーション設定を参照する

[関数アプリに対して定義されているアプリケーション設定](./functions-how-to-use-azure-function-app-settings.md)を参照することもできます。その場合は、設定名をパーセント記号 (%) で囲みます。

たとえば、 *https://%ORDER_PROCESSING_HOST%/api/orders* のバックエンド URL で、"%ORDER_PROCESSING_HOST%" は ORDER_PROCESSING_HOST 設定の値に置き換えられます。

> [!TIP] 
> 複数のデプロイまたはテスト環境がある場合は、バックエンド ホストのアプリケーション設定を使用してください。 そうすることで、常にその環境の適切なバックエンドと通信することができます。

## <a name="troubleshoot-proxies"></a><a name="debugProxies"></a>プロキシのトラブルシューティング

フラグ `"debug":true` を `proxies.json` 内の任意のプロキシに追加することで、デバッグ ログを有効にします。 ログは、`D:\home\LogFiles\Application\Proxies\DetailedTrace` に格納され、高度なツール (Kudu) を使用してアクセスできます。 すべての HTTP 応答には、`Proxy-Trace-Location` ヘッダーと、ログ ファイルにアクセスするための URL も含まれます。

`true` に設定した `Proxy-Trace-Enabled` ヘッダーを追加することによって、クライアント側からプロキシをデバッグできます。 これにより、ファイル システムのトレースもログに記録され、応答のヘッダーとしてトレースの URL が返されます。

### <a name="block-proxy-traces"></a>プロキシ トレースをブロックする

セキュリティ上の理由から、トレースを生成するためのサービスの呼び出しは許可しないことをお勧めします。 ログイン資格情報なしでトレース コンテンツにアクセスすることはできませんが、トレースを生成するとリソースが消費され、Functions プロキシを使用していることが公開されます。

`proxies.json` 内の特定のプロキシに `"debug":false` を追加することで、トレースを完全に無効にします。

## <a name="advanced-configuration"></a>詳細な構成

構成するプロキシは、関数アプリ ディレクトリのルートにある *proxies.json* ファイルに格納されます。 このファイルを手動で編集し、Functions でサポートされているいずれかの[デプロイ方法](./functions-continuous-deployment.md)を使用するときにアプリの一部としてデプロイできます。 

> [!TIP] 
> いずれのデプロイ方法もまだ設定していない場合は、ポータルで *proxies.json* ファイルを編集することもできます。 目的の関数アプリに移動して **[プラットフォーム機能]** を選択し、 **[App Service エディター]** を選択します。 これにより、関数アプリのファイル構造全体が表示され、変更を加えることができます。

*Proxies.json* は、名前付きプロキシとその定義から構成されるプロキシ オブジェクトによって定義されます。 エディターがサポートしていれば、必要に応じて [JSON スキーマ](http://json.schemastore.org/proxies)を参照してコード補完を行うことができます。 サンプル ファイルは、次のようになります。

```json
{
    "$schema": "http://json.schemastore.org/proxies",
    "proxies": {
        "proxy1": {
            "matchCondition": {
                "methods": [ "GET" ],
                "route": "/api/{test}"
            },
            "backendUri": "https://<AnotherApp>.azurewebsites.net/api/<FunctionName>"
        }
    }
}
```

各プロキシには、上の例の *proxy1* のようなフレンドリ名があります。 対応するプロキシ定義オブジェクトは、以下のプロパティによって定義されます。

* **matchCondition**:必須 - このプロキシの実行をトリガーする要求を定義するオブジェクト。 [HTTP トリガー]と共有される、次の 2 つのプロパティが含まれています。
    * _methods_:プロキシが応答する HTTP メソッドの配列。 指定しない場合、プロキシはルートのすべての HTTP メソッドに応答します。
    * _route_:必須 - プロキシがどの要求 URL に応答するかを制御するルート テンプレートを定義します。 HTTP トリガーとは異なり、既定値はありません。
* **backendUri**:要求のプロキシ先となる、バックエンド リソースの URL。 この値から、アプリケーション設定や元のクライアント要求のパラメーターを参照することが可能です。 このプロパティが含まれていない場合、Azure Functions は HTTP 200 OK で応答します。
* **requestOverrides**:バックエンドへの要求に対する変換を定義するオブジェクト。 「[requestOverrides オブジェクトの定義]」をご覧ください。
* **responseOverrides**:クライアントへの応答に対する変換を定義するオブジェクト。 「[responseOverrides オブジェクトの定義]」をご覧ください。

> [!NOTE] 
> Azure Functions プロキシの *route* プロパティ では、Function App ホスト構成の *routePrefix* プロパティは考慮されません。 `/api` のようなプレフィックスを含める場合は、*route* プロパティに含める必要があります。

### <a name="disable-individual-proxies"></a><a name="disableProxies"></a> 個々のプロキシを無効する

個々のプロキシを無効にするには、`proxies.json` ファイル内のプロキシに `"disabled": true` を追加します。 これにより、matchCondition を満たす要求はすべて 404 を返します。
```json
{
    "$schema": "http://json.schemastore.org/proxies",
    "proxies": {
        "Root": {
            "disabled":true,
            "matchCondition": {
                "route": "/example"
            },
            "backendUri": "https://<AnotherApp>.azurewebsites.net/api/<FunctionName>"
        }
    }
}
```

### <a name="application-settings"></a><a name="applicationSettings"></a> アプリケーションの設定

プロキシの動作は、いくつかのアプリ設定によって制御できます。 これらについては、[関数のアプリ設定のリファレンス](./functions-app-settings.md)で説明されています

* [AZURE_FUNCTION_PROXY_DISABLE_LOCAL_CALL](./functions-app-settings.md#azure_function_proxy_disable_local_call)
* [AZURE_FUNCTION_PROXY_BACKEND_URL_DECODE_SLASHES](./functions-app-settings.md#azure_function_proxy_backend_url_decode_slashes)

### <a name="reserved-characters-string-formatting"></a><a name="reservedChars"></a> 予約文字 (文字列形式)

プロキシで JSON ファイルのすべての文字列が読み取られるときは、エスケープ記号として \ が使用されます。 プロキシでは、中括弧も解釈されます。 以下の例の完全なセットを参照してください。

|文字|エスケープ文字|例|
|-|-|-|
|{ または }|{{ または }}|`{{ example }}` --> `{ example }`
| \ | \\\\ | `example.com\\text.html` --> `example.com\text.html`
|"|\\\"| `\"example\"` --> `"example"`

### <a name="define-a-requestoverrides-object"></a><a name="requestOverrides"></a>requestOverrides オブジェクトの定義

バックエンド リソースが呼び出されたときに要求に対して行う変更は、requestOverrides オブジェクトで定義します。 このオブジェクトは、次のプロパティによって定義されます。

* **backend.request.method**:バックエンドを呼び出す際に使用される HTTP メソッドです。
* **backend.request.querystring.\<ParameterName\>** :バックエンドの呼び出し時に設定できるクエリ文字列パラメーターです。 *\<ParameterName\>* は、設定するパラメーターの名前に置き換えます。 空の文字列を指定した場合、このパラメーターはバックエンドの要求に引き続き含まれることに注意してください。
* **backend.request.headers.\<HeaderName\>** :バックエンドの呼び出し時に設定できるヘッダーです。 *\<HeaderName\>* は、設定するヘッダーの名前に置き換えます。 空の文字列を指定した場合、このパラメーターはバックエンドの要求に引き続き含まれることに注意してください。

アプリケーション設定や元のクライアント要求のパラメーターを値から参照することができます。

実際の構成の例を次に示します。

```json
{
    "$schema": "http://json.schemastore.org/proxies",
    "proxies": {
        "proxy1": {
            "matchCondition": {
                "methods": [ "GET" ],
                "route": "/api/{test}"
            },
            "backendUri": "https://<AnotherApp>.azurewebsites.net/api/<FunctionName>",
            "requestOverrides": {
                "backend.request.headers.Accept": "application/xml",
                "backend.request.headers.x-functions-key": "%ANOTHERAPP_API_KEY%"
            }
        }
    }
}
```

### <a name="define-a-responseoverrides-object"></a><a name="responseOverrides"></a>responseOverrides オブジェクトの定義

クライアントに返される応答に対して行う変更は、requestOverrides オブジェクトで定義します。 このオブジェクトは、次のプロパティによって定義されます。

* **response.statusCode**:クライアントに返される HTTP 状態コード。
* **response.statusReason**:クライアントに返される HTTP 理由文字列。
* **response.body**:クライアントに返される本体の文字列表記。
* **response.headers.\<HeaderName\>** :クライアントへの応答時に設定可能なヘッダー。 *\<HeaderName\>* は、設定するヘッダーの名前に置き換えます。 空の文字列を指定した場合、このヘッダーは応答に反映されません。

アプリケーション設定や元のクライアント要求のパラメーター、バックエンドからの応答のパラメーターを値から参照することができます。

実際の構成の例を次に示します。

```json
{
    "$schema": "http://json.schemastore.org/proxies",
    "proxies": {
        "proxy1": {
            "matchCondition": {
                "methods": [ "GET" ],
                "route": "/api/{test}"
            },
            "responseOverrides": {
                "response.body": "Hello, {test}",
                "response.headers.Content-Type": "text/plain"
            }
        }
    }
}
```
> [!NOTE] 
> この例では、応答の本文が直接設定されているため、`backendUri` プロパティは必要ありません。 この例では、Azure Functions プロキシを使って API のモッキングを行う方法を説明しています。

[Azure Portal]: https://portal.azure.com
[HTTP トリガー]: ./functions-bindings-http-webhook.md
[Modify the back-end request]: #modify-backend-request
[Modify the response]: #modify-response
[requestOverrides オブジェクトの定義]: #requestOverrides
[responseOverrides オブジェクトの定義]: #responseOverrides
[アプリケーション設定]: #use-appsettings
[変数を使用する]: #using-variables
[元のクライアント要求のパラメーター]: #request-parameters
[バックエンドからの応答のパラメーター]: #response-parameters
