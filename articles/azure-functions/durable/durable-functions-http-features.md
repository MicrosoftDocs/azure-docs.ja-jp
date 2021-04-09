---
title: Durable Functions の HTTP 機能 - Azure Functions
description: Azure Functions 用の Durable Functions 拡張機能の統合 HTTP 機能について説明します。
author: cgillum
ms.topic: conceptual
ms.date: 07/14/2020
ms.author: azfuncdf
ms.openlocfilehash: 64d40de50f21811a56318971de1836abc8fbf8c9
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "93027263"
---
# <a name="http-features"></a>HTTP 機能

Durable Functions には、持続的なオーケストレーションとエンティティを HTTP ワークフローに簡単に組み込むことができる機能がいくつかあります。 この記事では、これらの機能の一部について詳しく説明します。

## <a name="exposing-http-apis"></a>HTTP API の公開

オーケストレーションとエンティティは、HTTP 要求を使用して起動および管理できます。 Durable Functions 拡張機能では、組み込みの HTTP API が公開されています。 また、HTTP によってトリガーされる関数内からオーケストレーションやエンティティと対話する API も用意されています。

### <a name="built-in-http-apis"></a>組み込みの HTTP API

Durable Functions 拡張機能によって、一連の HTTP API が Azure Functions ホストに自動的に追加されます。 これらの API を使用すると、コードを書くことなく、オーケストレーションやエンティティとの対話や管理を行うことができます。

次の組み込みの HTTP API がサポートされています。

* [新しいオーケストレーションを開始する](durable-functions-http-api.md#start-orchestration)
* [オーケストレーション インスタンスのクエリを実行する](durable-functions-http-api.md#get-instance-status)
* [オーケストレーション インスタンスを終了する](durable-functions-http-api.md#terminate-instance)
* [オーケストレーションに外部イベントを送信する](durable-functions-http-api.md#raise-event)
* [オーケストレーション履歴を消去する](durable-functions-http-api.md#purge-single-instance-history)
* [エンティティに操作イベントを送信する](durable-functions-http-api.md#signal-entity)
* [エンティティの状態を取得する](durable-functions-http-api.md#get-entity)
* [エンティティの一覧を問い合わせる](durable-functions-http-api.md#list-entities)

Durable Functions 拡張機能で公開されているすべての組み込みの HTTP API の詳細については、[HTTP API に関する記事](durable-functions-http-api.md)を参照してください。

### <a name="http-api-url-discovery"></a>HTTP API URL の検出

[オーケストレーション クライアントのバインド](durable-functions-bindings.md#orchestration-client)により、便利な HTTP 応答ペイロードを生成できる API が公開されます。 たとえば、特定のオーケストレーション インスタンスの管理 API へのリンクを含む応答を作成できます。 次の例では、この API を新しいオーケストレーション インスタンスに使用する方法を示す HTTP トリガー関数を示します。

# <a name="c"></a>[C#](#tab/csharp)

[!code-csharp[Main](~/samples-durable-functions/samples/precompiled/HttpStart.cs)]

# <a name="javascript"></a>[JavaScript](#tab/javascript)

**index.js**

[!code-javascript[Main](~/samples-durable-functions/samples/javascript/HttpStart/index.js)]

**function.json**

[!code-json[Main](~/samples-durable-functions/samples/javascript/HttpStart/function.json)]

# <a name="python"></a>[Python](#tab/python)

**__init__.py**

```python
import logging
import azure.functions as func
import azure.durable_functions as df

async def main(req: func.HttpRequest, starter: str) -> func.HttpResponse:
    client = df.DurableOrchestrationClient(starter)
    function_name = req.route_params['functionName']
    event_data = req.get_body()

    instance_id = await client.start_new(function_name, instance_id, event_data)
    
    logging.info(f"Started orchestration with ID = '{instance_id}'.")
    return client.create_check_status_response(req, instance_id)
```

**function.json**

```json
{
  "scriptFile": "__init__.py",
  "bindings": [
    {
      "authLevel": "function",
      "name": "req",
      "type": "httpTrigger",
      "direction": "in",
      "route": "orchestrators/{functionName}",
      "methods": [
        "post",
        "get"
      ]
    },
    {
      "name": "$return",
      "type": "http",
      "direction": "out"
    },
    {
      "name": "starter",
      "type": "orchestrationClient",
      "direction": "in"
    }
  ]
}
```

---

前述の HTTP トリガー関数を使用したオーケストレーター関数の開始は、任意の HTTP クライアントを使用して実行できます。 次の cURL コマンドでは、`DoWork` という名前のオーケストレーター関数を開始します。

```bash
curl -X POST https://localhost:7071/orchestrators/DoWork -H "Content-Length: 0" -i
```

次に、`abc123` という ID のオーケストレーションに対する応答の例を示します。 わかりやすくするために、細部をいくらか省略しています。

```http
HTTP/1.1 202 Accepted
Content-Type: application/json; charset=utf-8
Location: http://localhost:7071/runtime/webhooks/durabletask/instances/abc123?code=XXX
Retry-After: 10

{
    "id": "abc123",
    "purgeHistoryDeleteUri": "http://localhost:7071/runtime/webhooks/durabletask/instances/abc123?code=XXX",
    "sendEventPostUri": "http://localhost:7071/runtime/webhooks/durabletask/instances/abc123/raiseEvent/{eventName}?code=XXX",
    "statusQueryGetUri": "http://localhost:7071/runtime/webhooks/durabletask/instances/abc123?code=XXX",
    "terminatePostUri": "http://localhost:7071/runtime/webhooks/durabletask/instances/abc123/terminate?reason={text}&code=XXX"
}
```

前の例では、末尾に `Uri` が付く各フィールドは、組み込みの HTTP API に対応しています。 これらの API を使用して、ターゲット オーケストレーション インスタンスを管理できます。

> [!NOTE]
> webhook URL の形式は、実行している Azure Functions ホストのバージョンによって異なります。 前の例は、Azure Functions 2.0 ホスト用の形式です。

すべての組み込みの HTTP API の詳細については、「[HTTP API リファレンス](durable-functions-http-api.md)」を参照してください。

### <a name="async-operation-tracking"></a>非同期操作の追跡

上記の HTTP 応答は、Durable Functions で実行時間の長い HTTP 非同期 API を実装するのに役立つように設計されています。 このパターンは、*ポーリング コンシューマー パターン* と呼ばれる場合もあります。 クライアント/サーバー フローは次のように動作します。

1. クライアントが、オーケストレーター関数など、実行時間の長いプロセスを開始する HTTP 要求を発行します。
1. ターゲット HTTP トリガーは、"statusQueryGetUri" の値を持つ Location ヘッダーを含む HTTP 202 応答を返します。
1. クライアントが Location ヘッダー内の URL をポーリングします。 クライアントは引き続き、Location ヘッダーを含む HTTP 202 応答を参照します。
1. インスタンスが終了 (または失敗) すると、Location ヘッダー内のエンドポイントから HTTP 200 が返されます。

このプロトコルでは、HTTP エンドポイントのポーリングと Location ヘッダーのフォローをサポートする、外部のクライアントまたはサービスでの実行時間の長いプロセスを調整できます。 このパターンのクライアントとサーバーの両方の実装は、Durable Functions HTTP API に組み込まれています。

> [!NOTE]
> 既定では、[Azure Logic Apps](https://azure.microsoft.com/services/logic-apps/) によって提供される HTTP ベースのすべてのアクションで、標準的な非同期操作パターンがサポートされています。 この機能により、実行時間の長い Durable Functions を Logic Apps ワークフローの一部として組み込むことができます。 Logic Apps での非同期 HTTP パターンのサポートの詳細については、[Azure Logic Apps ワークフロー アクションおよびトリガーに関するドキュメント](../../logic-apps/logic-apps-workflow-actions-triggers.md)をご覧ください。

> [!NOTE]
> オーケストレーションとの対話は、HTTP によってトリガーされる関数だけでなく、任意の関数型から実行できます。

クライアント API を使用してオーケストレーションとエンティティを管理する方法の詳細については、[インスタンスの管理に関する記事](durable-functions-instance-management.md)を参照してください。

## <a name="consuming-http-apis"></a>HTTP API の使用

「[オーケストレーター関数コードの制約](durable-functions-code-constraints.md)」で説明されているように、オーケストレーター関数で I/O を直接実行することはできません。 通常は、代わりに、I/O 操作を実行する[アクティビティ関数](durable-functions-types-features-overview.md#activity-functions)を呼び出します。

Durable Functions 2.0 以降、オーケストレーションでは、[オーケストレーション トリガーのバインド](durable-functions-bindings.md#orchestration-trigger)を使用して HTTP API をネイティブで使用できます。

次のコード例は、送信 HTTP 要求を行うオーケストレーター関数を示しています。

# <a name="c"></a>[C#](#tab/csharp)

```csharp
[FunctionName("CheckSiteAvailable")]
public static async Task CheckSiteAvailable(
    [OrchestrationTrigger] IDurableOrchestrationContext context)
{
    Uri url = context.GetInput<Uri>();

    // Makes an HTTP GET request to the specified endpoint
    DurableHttpResponse response = 
        await context.CallHttpAsync(HttpMethod.Get, url);

    if (response.StatusCode >= 400)
    {
        // handling of error codes goes here
    }
}
```

# <a name="javascript"></a>[JavaScript](#tab/javascript)

```javascript
const df = require("durable-functions");

module.exports = df.orchestrator(function*(context){
    const url = context.df.getInput();
    const response = yield context.df.callHttp("GET", url)

    if (response.statusCode >= 400) {
        // handling of error codes goes here
    }
});
```
# <a name="python"></a>[Python](#tab/python)

```python
import azure.functions as func
import azure.durable_functions as df
from datetime import datetime, timedelta

def orchestrator_function(context: df.DurableOrchestrationContext):
    url = context.get_input()
    response = yield context.call_http('GET', url)
    
    if response["statusCode"] >= 400:
        # handling of error codes goes here

main = df.Orchestrator.create(orchestrator_function)
```

---

"call HTTP" アクションを使用して、オーケストレーター関数で次のアクションを実行できます。

* オーケストレーション関数から HTTP API を直接呼び出す (後述するいくつかの制限があります)。
* クライアント側の HTTP 202 状態ポーリング パターンを自動的にサポートする。
* [Azure マネージド ID](../../active-directory/managed-identities-azure-resources/overview.md) を使用して、他の Azure エンドポイントに対する承認済みの HTTP 呼び出しを行う。

オーケストレーター関数から HTTP API を直接使用する機能は、一般的なシナリオの特定のセットに合わせた利便性が意図されています。 これらの機能はいずれも、アクティビティ関数を使用して自分で実装できます。 多くの場合、アクティビティ関数を使用すると、柔軟性が向上します。

### <a name="http-202-handling"></a>HTTP 202 の処理

"call HTTP" API では、ポーリング コンシューマー パターンのクライアント側を自動的に実装できます。 呼び出された API から Location ヘッダーを含む HTTP 202 応答が返される場合、202 以外の応答が返されるまで、オーケストレーター関数では Location リソースが自動的にポーリングされます。 この応答は、オーケストレーター関数コードに返される応答です。

> [!NOTE]
> オーケストレーター関数は、「[非同期操作の追跡](#async-operation-tracking)」で説明されているように、サーバー側のポーリング コンシューマー パターンもネイティブでサポートします。 このサポートにより、1 つの関数アプリ内のオーケストレーションで、他の関数アプリのオーケストレーター関数を簡単に調整できます。 これは、[サブオーケストレーション](durable-functions-sub-orchestrations.md)の概念と似ていますが、アプリ間通信がサポートされています。 このサポートは、マイクロサービススタイルのアプリ開発に特に役立ちます。

### <a name="managed-identities"></a>マネージド ID

Durable Functions では、承認のために Azure Active Directory (Azure AD) トークンを受け入れる API の呼び出しがネイティブでサポートされています。 このサポートでは、[Azure マネージド ID](../../active-directory/managed-identities-azure-resources/overview.md) を使用してこれらのトークンを取得します。

次のコードは、.NET オーケストレーター関数の例です。 この関数では、Azure Resource Manager の [Virtual Machines REST API](/rest/api/compute/virtualmachines) を使用して、仮想マシンを再起動する認証済み呼び出しを行います。

# <a name="c"></a>[C#](#tab/csharp)

```csharp
[FunctionName("RestartVm")]
public static async Task RunOrchestrator(
    [OrchestrationTrigger] IDurableOrchestrationContext context)
{
    string subscriptionId = "mySubId";
    string resourceGroup = "myRG";
    string vmName = "myVM";
    string apiVersion = "2019-03-01";
    
    // Automatically fetches an Azure AD token for resource = https://management.core.windows.net/.default
    // and attaches it to the outgoing Azure Resource Manager API call.
    var restartRequest = new DurableHttpRequest(
        HttpMethod.Post, 
        new Uri($"https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroup}/providers/Microsoft.Compute/virtualMachines/{vmName}/restart?api-version={apiVersion}"),
        tokenSource: new ManagedIdentityTokenSource("https://management.core.windows.net/.default"));
    DurableHttpResponse restartResponse = await context.CallHttpAsync(restartRequest);
    if (restartResponse.StatusCode != HttpStatusCode.OK)
    {
        throw new ArgumentException($"Failed to restart VM: {restartResponse.StatusCode}: {restartResponse.Content}");
    }
}
```

# <a name="javascript"></a>[JavaScript](#tab/javascript)

```javascript
const df = require("durable-functions");

module.exports = df.orchestrator(function*(context) {
    const subscriptionId = "mySubId";
    const resourceGroup = "myRG";
    const vmName = "myVM";
    const apiVersion = "2019-03-01";
    const tokenSource = new df.ManagedIdentityTokenSource("https://management.core.windows.net/.default");

    // get a list of the Azure subscriptions that I have access to
    const restartResponse = yield context.df.callHttp(
        "POST",
        `https://management.azure.com/subscriptions/${subscriptionId}/resourceGroups/${resourceGroup}/providers/Microsoft.Compute/virtualMachines/${vmName}/restart?api-version=${apiVersion}`,
        undefined, // no request content
        undefined, // no request headers (besides auth which is handled by the token source)
        tokenSource);

    return restartResponse;
});
```

# <a name="python"></a>[Python](#tab/python)

```python
import azure.functions as func
import azure.durable_functions as df

def orchestrator_function(context: df.DurableOrchestrationContext):
    subscription_id = "mySubId"
    resource_group = "myRg"
    vm_name = "myVM"
    api_version = "2019-03-01"
    token_source = df.ManagedIdentityTokenSource("https://management.core.windows.net/.default")

    # get a list of the Azure subscriptions that I have access to
    restart_response = yield context.call_http("POST", 
        f"https://management.azure.com/subscriptions/{subscription_id}/resourceGroups/{resource_group}/providers/Microsoft.Compute/virtualMachines/{vm_name}/restart?api-version={api_version}",
        None,
        None,
        token_source)
    return restart_response

main = df.Orchestrator.create(orchestrator_function)
```

---

前の例で、`tokenSource` パラメーターは [Azure Resource Manager](../../azure-resource-manager/management/overview.md) の Azure AD トークンを取得するように構成されています。 このトークンは、リソース URI `https://management.core.windows.net/.default`によって識別されます。 この例では、現在の関数アプリがローカルで実行されているか、マネージド ID を使用する関数アプリとしてデプロイ済みであることを前提としています。 ローカル ID またはマネージド ID は、指定されたリソース グループ `myRG` 内の VM を管理できるアクセス許可を持っていると見なされます。

実行時に、構成されたトークン ソースにより OAuth 2.0 アクセス トークンが自動的に返されます。 次に、ソースによって、送信要求の Authorization ヘッダーにトークンがベアラー トークンとして追加されます。 このモデルは、次の理由により、Authorization ヘッダーを HTTP 要求に手動で追加するよりも優れています。

* トークンの更新は自動的に処理されます。 期限切れのトークンについて心配する必要はありません。
* トークンは、持続的なオーケストレーション状態には格納されません。
* トークンの取得を管理するコードを記述する必要はありません。

より詳細な例については、[プリコンパイル済みの C# の RestartVMs サンプル](https://github.com/Azure/azure-functions-durable-extension/blob/dev/samples/precompiled/RestartVMs.cs)を参照してください。

マネージド ID は、Azure リソース管理に限定されません。 マネージド ID を使用すると、Microsoft の Azure サービスやパートナーの Web アプリなど、Azure AD ベアラー トークンを受け入れるすべての API にアクセスできます。 パートナーの Web アプリを別の関数アプリにすることもできます。 Azure AD での認証をサポートする Microsoft の Azure サービスの一覧については、「[Azure AD 認証をサポートしている Azure サービス](../../active-directory/managed-identities-azure-resources/services-support-managed-identities.md#azure-services-that-support-azure-ad-authentication)」を参照してください。

### <a name="limitations"></a>制限事項

HTTP API を呼び出す組み込みのサポートは便利な機能です。 これは、すべてのシナリオに適しているわけではありません。

オーケストレーター関数で送信される HTTP 要求とその応答はキュー メッセージとしてシリアル化され、永続的です。 このキュー動作によって、HTTP 呼び出しで[信頼性が高く、安全にオーケストレーションを再生する](durable-functions-orchestrations.md#reliability)ことが保証されます。 ただし、キュー動作にも制限事項があります。

* 各 HTTP 要求では、ネイティブの HTTP クライアントと比較すると、追加の待機時間が発生します。
* キュー メッセージに収まりきらない大きな要求または応答メッセージがあると、オーケストレーションのパフォーマンスが大幅に低下する可能性があります。 BLOB ストレージへのメッセージ ペイロードのオフロードのオーバーヘッドにより、パフォーマンスが低下する可能性があります。
* ストリーミング、チャンク、およびバイナリのペイロードはサポートされません。
* HTTP クライアントの動作をカスタマイズする機能は制限されています。

これらの制限事項のいずれかがお客様のユースケースに影響する可能性がある場合は、代わりにアクティビティ関数と言語固有の HTTP クライアント ライブラリを使用して送信 HTTP 呼び出しを行うことを検討してください。

> [!NOTE]
> .NET 開発者であれば、この機能で、組み込みの .NET **HttpRequestMessage** および **HttpResponseMessage** 型ではなく、**DurableHttpRequest** および **DurableHttpResponse** 型が使用される理由を疑問に思うかもしれません。
>
> この設計の選択は意図的なものです。 主な理由は、カスタム型を使用することで、内部 HTTP クライアントのサポートされている動作についてユーザーが誤った想定を行うことを回避するためです。 Durable Functions に固有の型により、API の設計を簡素化することもできます。 また、[マネージド ID の統合](#managed-identities)や[ポーリング コンシューマー パターン](#http-202-handling)などの特別な機能を、より簡単に利用できるようになります。 

### <a name="extensibility-net-only"></a>拡張機能 (.NET のみ)

オーケストレーションの内部 HTTP クライアントの動作をカスタマイズするには、[Azure Functions の .NET 依存関係の挿入](../functions-dotnet-dependency-injection.md)を使用できます。 この機能は、小規模な動作変更を行う場合に役立ちます。 これは、モック オブジェクトを挿入することによる、HTTP クライアントの単体テストにも役立ちます。

次の例では、外部 HTTP エンドポイントを呼び出すオーケストレーター関数に対する TLS/SSL 証明書の検証を無効にするために、依存関係の挿入を使用する方法を示します。

```csharp
public class Startup : FunctionsStartup
{
    public override void Configure(IFunctionsHostBuilder builder)
    {
        // Register own factory
        builder.Services.AddSingleton<
            IDurableHttpMessageHandlerFactory,
            MyDurableHttpMessageHandlerFactory>();
    }
}

public class MyDurableHttpMessageHandlerFactory : IDurableHttpMessageHandlerFactory
{
    public HttpMessageHandler CreateHttpMessageHandler()
    {
        // Disable TLS/SSL certificate validation (not recommended in production!)
        return new HttpClientHandler
        {
            ServerCertificateCustomValidationCallback =
                HttpClientHandler.DangerousAcceptAnyServerCertificateValidator,
        };
    }
}
```

## <a name="next-steps"></a>次のステップ

> [!div class="nextstepaction"]
> [持続性のあるエンティティについて学習します](durable-functions-entities.md)
