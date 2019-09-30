---
title: Durable Functions の HTTP 機能 - Azure Functions
description: Azure Functions 用の Durable Functions 拡張機能の統合 HTTP 機能について説明します。
author: cgillum
manager: gwallace
keywords: ''
ms.service: azure-functions
ms.topic: conceptual
ms.date: 09/04/2019
ms.author: azfuncdf
ms.openlocfilehash: b909918ff4f9abc1dd64d4c7e5ccb35954b233f7
ms.sourcegitcommit: f3f4ec75b74124c2b4e827c29b49ae6b94adbbb7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/12/2019
ms.locfileid: "70935548"
---
# <a name="http-features"></a>HTTP 機能

Durable Functions には、持続的なオーケストレーションとエンティティを HTTP ワークフローに簡単に組み込むことができる機能がいくつかあります。 この記事では、これらの機能の一部について詳しく説明します。

## <a name="exposing-http-apis"></a>HTTP API の公開

オーケストレーションとエンティティは、HTTP 要求を使用して起動および管理できます。 Durable Functions 拡張機能は、組み込みの HTTP API を公開しています。また、HTTP によってトリガーされる関数内からオーケストレーションやエンティティと対話する API も用意されています。

### <a name="built-in-http-apis"></a>組み込みの HTTP API

Durable Functions 拡張機能によって、一連の HTTP API が Azure Functions ホストに自動的に追加されます。 これらの API を使用すると、コードを書くことなく、オーケストレーションやエンティティとの対話や管理を行うことができます。

次の組み込みの HTTP API がサポートされています。

* [新しいオーケストレーションを開始する](durable-functions-http-api.md#start-orchestration)
* [オーケストレーション インスタンスのクエリを実行する](durable-functions-http-api.md#get-instance-status)
* [オーケストレーション インスタンスを終了する](durable-functions-http-api.md#terminate-instance)
* [オーケストレーションに外部イベントを送信する](durable-functions-http-api.md#raise-event)
* [オーケストレーション履歴を消去する](durable-functions-http-api.md#purge-single-instance-history)
* [エンティティに操作イベントを送信する](durable-functions-http-api.md#signal-entity)
* [エンティティの状態のクエリを実行する](durable-functions-http-api.md#query-entity)

Durable Functions 拡張機能で公開されているすべての組み込みの HTTP API の詳細については、[HTTP API](durable-functions-http-api.md) の記事を参照してください。

### <a name="http-api-url-discovery"></a>HTTP API URL の検出

[オーケストレーション クライアント バインド](durable-functions-bindings.md#orchestration-client)では、便利な HTTP 応答ペイロードを生成するために使用できる API を公開しています。 たとえば、特定のオーケストレーション インスタンスの管理 API へのリンクを含む応答を作成できます。 この API を新しいオーケストレーション インスタンスに使用する方法を示す HTTP トリガー関数の例を次に示します。

#### <a name="precompiled-c"></a>プリコンパイル済み C#

[!code-csharp[Main](~/samples-durable-functions/samples/precompiled/HttpStart.cs)]

#### <a name="c-script"></a>C# スクリプト

[!code-csharp[Main](~/samples-durable-functions/samples/csx/HttpStart/run.csx)]

#### <a name="javascript-functions-2x-only"></a>JavaScript (Functions 2.x のみ)

[!code-javascript[Main](~/samples-durable-functions/samples/javascript/HttpStart/index.js)]

#### <a name="functionjson"></a>Function.json

[!code-javascript[Main](~/samples-durable-functions/samples/javascript/HttpStart/function.json)]

上記の HTTP トリガー関数を使用したオーケストレーター関数は、任意の HTTP クライアントを使用して開始できます。 次の cURL コマンドでは、`DoWork` という名前のオーケストレーター関数を開始します。

```bash
curl -X POST https://localhost:7071/orchestrators/DoWork -H "Content-Length: 0" -i
```

ID として `abc123` を使用するオーケストレーションに対する応答例を次に示します (わかりやすくするために、一部の詳細が削除されています)。

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

前の例の各 `~Uri` フィールドは、組み込みの HTTP API に対応しています。 これらの API を使用して、ターゲット オーケストレーション インスタンスを管理できます。

> [!NOTE]
> webhook URL の形式は、実行している Azure Functions ホストのバージョンによって異なる場合があります。 上記の例は、Azure Functions 2.0 ホスト用の形式です。

すべての組み込みの HTTP API の詳細については、[HTTP API リファレンス](durable-functions-http-api.md) ドキュメントを参照してください。

### <a name="async-operation-tracking"></a>非同期操作の追跡

上記の HTTP 応答は、Durable Functions で実行時間の長い HTTP 非同期 API を実装するのに役立つように設計されています。 このパターンは、*ポーリング コンシューマー パターン*と呼ばれる場合もあります。 クライアント/サーバー フローは次のように動作します。

1. クライアントが、オーケストレーター関数など、実行時間の長いプロセスを開始する HTTP 要求を発行します。
2. 対象の HTTP トリガーが、`statusQueryGetUri` 値の `Location` ヘッダーを含む HTTP 202 応答を返します。
3. クライアントが `Location` ヘッダー内の URL をポーリングします。 クライアントは引き続き、`Location` ヘッダーを含む HTTP 202 応答を参照します。
4. インスタンスが完了 (または失敗) すると、`Location` ヘッダー内のエンドポイントから HTTP 200 が返されます。

このプロトコルでは、HTTP エンドポイントのポーリングと `Location` ヘッダーのフォローをサポートする、外部のクライアントまたはサービスでの実行時間の長いプロセスを調整できます。 このパターンのクライアントとサーバーの両方の実装は、Durable Functions HTTP API に組み込まれています。

> [!NOTE]
> 既定では、[Azure Logic Apps](https://azure.microsoft.com/services/logic-apps/) によって提供される HTTP ベースのすべてのアクションで、標準的な非同期操作パターンがサポートされています。 この機能により、実行時間の長い Durable Functions を Logic Apps ワークフローの一部として組み込むことができます。 Logic Apps での非同期 HTTP パターンのサポートについて詳しくは、[Azure Logic Apps ワークフロー アクションおよびトリガーに関するドキュメント](../../logic-apps/logic-apps-workflow-actions-triggers.md)をご覧ください。

> [!NOTE]
> オーケストレーションとの対話は、HTTP によってトリガーされる関数だけでなく、任意の関数型から実行できます。

クライアント API を使用してオーケストレーションとエンティティを管理する方法の詳細については、[インスタンス管理](durable-functions-instance-management.md)に関する記事を参照してください。

## <a name="consuming-http-apis"></a>HTTP API の使用

[オーケストレーター関数のコードの制約](durable-functions-code-constraints.md)に関する記事で説明されているように、オーケストレーター関数で I/O を直接実行することは許可されていません。 代わりに、通常、オーケストレーター関数からは I/O 操作を実行する[アクティビティ関数](durable-functions-types-features-overview.md#activity-functions)を呼び出します。

Durable Functions 2.0 以降、オーケストレーションでは、[オーケストレーション トリガーのバインド](durable-functions-bindings.md#orchestration-trigger)を使用して HTTP API をネイティブで使用できます。

> [!NOTE]
> オーケストレーター関数から HTTP エンドポイントを直接呼び出す機能は、JavaScript ではまだ使用できません。

次のコード例は、`CallHttpAsync` .NET API を使用して送信 HTTP 要求を行う C# オーケストレーター関数を示しています。

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

"call HTTP" アクションを使用すると、オーケストレーター関数で次のことを行うことができます。

* オーケストレーション関数から HTTP API を直接呼び出す (後述するいくつかの制限があります)。
* クライアント側の HTTP 202 状態ポーリング パターンの自動サポート。
* [Azure マネージド ID](../../active-directory/managed-identities-azure-resources/overview.md) を使用して、他の Azure エンドポイントに対する承認済みの HTTP 呼び出しを行う。

オーケストレーター関数から HTTP API を直接使用する機能は、一般的なシナリオの特定のセットに合わせた利便性が意図されています。 これらの機能はいずれも、アクティビティ関数を使用して自分で実装することができます。 多くの場合、アクティビティ関数を使用すると、柔軟性が向上します。

### <a name="http-202-handling"></a>HTTP 202 の処理

"call HTTP" API では、*ポーリング コンシューマー パターン*のクライアント側を自動的に実装できます。 呼び出された API から `Location` ヘッダーを含む HTTP 202 応答が返される場合、202 以外の応答が返されるまで、オーケストレーター関数では `Location` リソースが自動的にポーリングされます。 202 以外の応答は、オーケストレーター関数コードに返される応答です。

> [!NOTE]
> オーケストレーター関数は、「[非同期操作の追跡](#async-operation-tracking)」で説明されているように、サーバー側の*ポーリング コンシューマー パターン*もネイティブでサポートします。 つまり、1 つの関数アプリ内のオーケストレーションで、他の関数アプリのオーケストレーター関数を簡単に調整できます。 これは、[サブオーケストレーション](durable-functions-sub-orchestrations.md)の概念と似ていますが、アプリ間通信がサポートされています。 これは、マイクロサービススタイルのアプリケーション開発に特に役立ちます。

### <a name="managed-identities"></a>マネージド ID

Durable Functions は、承認のために Azure AD トークンを受け入れる API の呼び出しをネイティブでサポートしています。 このサポートでは、[Azure マネージド ID](../../active-directory/managed-identities-azure-resources/overview.md) を使用してこれらのトークンを取得します。

次のコードは、Azure Resource Manager [Virtual Machines REST API](https://docs.microsoft.com/rest/api/compute/virtualmachines) を使用して仮想マシンを再起動する認証済み呼び出しを行う .NET オーケストレーター関数の例です。

```csharp
[FunctionName("RestartVm")]
public static async Task RunOrchestrator(
    [OrchestrationTrigger] IDurableOrchestrationContext context)
{
    string subscriptionId = "mySubId";
    string resourceGroup = "myRG";
    string vmName = "myVM";
    
    // Automatically fetches an Azure AD token for resource = https://management.core.windows.net
    // and attaches it to the outgoing Azure Resource Manager API call.
    var restartRequest = new DurableHttpRequest(
        HttpMethod.Post, 
        new Uri($"https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroup}/providers/Microsoft.Compute/virtualMachines/{vmName}/restart?api-version={apiVersion}"),
        tokenSource: new ManagedIdentityTokenSource("https://management.core.windows.net"));
    DurableHttpResponse restartResponse = await context.CallHttpAsync(restartRequest);
    if (restartResponse.StatusCode != HttpStatusCode.OK)
    {
        throw new ArgumentException($"Failed to restart VM: {restartResponse.StatusCode}: {restartResponse.Content}");
    }
}
```

前の例で、`tokenSource` パラメーターは ("リソース URI" `https://management.core.windows.net` によって識別される) [Azure Resource Manager](../../azure-resource-manager/resource-group-overview.md) の Azure AD トークンを取得するように構成されています。 この例は、現在の関数アプリがローカルで実行されているか、マネージド ID を使用する Azure Functions アプリとしてデプロイ済みであることを前提としています。 ローカル ID またはマネージド ID は、指定されたリソース グループ `myRG` 内の VM を管理できるアクセス許可を持っていると見なされます。

実行時に、構成済みのトークン ソースからは自動的に OAuth 2.0 アクセス トークンが返され、それがベアラー トークンとして送信要求の `Authorization` ヘッダーに追加されます。 このモデルは、次の理由により、承認ヘッダーを HTTP 要求に手動で追加するよりも優れています。

* トークンの更新は自動的に処理されます。 期限切れのトークンについて心配する必要はありません。
* トークンは、持続的なオーケストレーション状態には格納されません。
* トークンの取得を処理するコードを記述する必要はありません。

より完全な例については、[プリコンパイル済みの C# の "RestartVMs" サンプル](https://github.com/Azure/azure-functions-durable-extension/blob/v2/samples/v2/precompiled/RestartVMs.cs)を参照してください。

マネージド ID は、Azure リソース管理に限定されません。 マネージド ID を使用すると、ファーストパーティの Azure サービスやサードパーティの Web アプリケーションなど、Azure AD ベアラー トークンを受け入れる任意の API にアクセスできます。 サードパーティの Web アプリを別の関数アプリにすることもできます。 Azure AD での認証をサポートするファーストパーティの Azure サービスの一覧については、「[Azure AD 認証をサポートしている Azure サービス](../../active-directory/managed-identities-azure-resources/services-support-managed-identities.md#azure-services-that-support-azure-ad-authentication)」を参照してください。

### <a name="limitations"></a>制限事項

HTTP API を呼び出す組み込みのサポートは便利な機能であり、すべてのシナリオに適しているわけではありません。 オーケストレーター関数で送信される HTTP 要求とその応答はシリアル化され、キュー メッセージとして永続化されます。 このキュー動作によって、HTTP 呼び出しで[信頼性が高く、安全にオーケストレーションを再生する](durable-functions-orchestrations.md#reliability)ことが保証されます。 ただし、このキューの動作は次のことも意味します。

* 各 HTTP 要求では、ネイティブの HTTP クライアントと比較すると、追加の待機時間が発生します。
* キュー メッセージに収まりきらない大きな要求または応答メッセージがあると、オーケストレーションのパフォーマンスが大幅に低下する可能性があります。 パフォーマンスの低下は、BLOB ストレージへのメッセージ ペイロードのオフロードのオーバーヘッドが原因で発生する可能性があります。
* ストリーミング、チャンク、およびバイナリのペイロードはサポートされません。
* HTTP クライアントの動作をカスタマイズする機能は制限されています。

これらの制限事項のいずれかがお客様のユースケースに影響する可能性がある場合は、代わりにアクティビティ関数と言語固有の HTTP クライアント ライブラリを使用して送信 HTTP 呼び出しを行うことを検討してください。

> [!NOTE]
> .Net 開発者であれば、この機能が組み込みの .NET `HttpRequestMessage` および `HttpResponseMessage` ではなく `DurableHttpRequest` および `DurableHttpResponse` 型を使用する理由を疑問に思うかもしれません。 この設計の選択は意図的なものでした。 主な理由は、カスタム型を使用して、内部 HTTP クライアントのサポートされている動作についてユーザーが誤った想定を行わないようにすることです。 また、持続性に特化した型によって、API の設計を簡素化し、[マネージド ID の統合](#managed-identities)や[ポリシー コンシューマー パターン](#http-202-handling)などの特別な機能をより簡単に利用できるようになります。

## <a name="next-steps"></a>次の手順

> [!div class="nextstepaction"]
> [持続性のあるエンティティについて学習します](durable-functions-entities.md)