---
title: Azure Functions での接続の管理
description: 静的接続クライアントを使用して、Azure Functions のパフォーマンスの問題を回避する方法について説明します。
ms.topic: conceptual
ms.custom: devx-track-csharp
ms.date: 02/25/2018
ms.openlocfilehash: ec16ce3e7f9793be2a012a029bcca31c9a7ea4cf
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "97936704"
---
# <a name="manage-connections-in-azure-functions"></a>Azure Functions での接続の管理

関数アプリ内の関数はリソースを共有します。 それらの共有リソースの中には、HTTP 接続、データベース接続、Azure Storage などのサービスへの接続があります。 多くの関数が同時に実行されている場合、利用可能な接続がなくなる可能性があります。 この記事では、必要以上に多くの接続を使用しないように関数をコーディングする方法について説明します。

## <a name="connection-limit"></a>接続の制限

使用できる接続の数が制限される理由の 1 つは、関数アプリが[サンドボックス環境](https://github.com/projectkudu/kudu/wiki/Azure-Web-App-sandbox)で実行されるためです。 サンドボックスがコードに課す制限の 1 つに、送信接続数の制限があります。現在は、インスタンスあたり 600 アクティブ (合計 1,200) 接続です。 この制限に達すると、関数ランタイムによって `Host thresholds exceeded: Connections` というメッセージがログに出力されます。 詳細については、[Functions のサービスの制限](functions-scale.md#service-limits)に関する記事を参照してください。

この制限はインスタンスごとに適用されます。 より多くの要求を処理するために、[スケール コントローラーによって関数アプリ インスタンスが追加](event-driven-scaling.md)されると、インスタンスごとに接続の制限が適用されます。 つまり、接続のグローバルな制限はないので、すべてのアクティブ インスタンスでアクティブな接続の数が 600 をはるかに超える可能性があります。

トラブルシューティングを行う際には、関数アプリに対して Application Insights を有効にしたことを確認します。 Application Insights では、実行など、関数アプリのメトリックを表示できます。 詳細については、「[Application Insights でテレメトリを表示する](analyze-telemetry-data.md#view-telemetry-in-application-insights)」を参照してください。  

## <a name="static-clients"></a>静的クライアント

必要以上に多くの接続を保持しないようにするには、関数の呼び出しごとに新しいインスタンスを作成するのではなく、クライアント インスタンスを再利用します。 関数の記述に使用するどの言語でも、クライアント接続を再利用することをお勧めします。 たとえば、単一の静的クライアントを使用すると、[HttpClient](/dotnet/api/system.net.http.httpclient?view=netcore-3.1&preserve-view=true)、[DocumentClient](/dotnet/api/microsoft.azure.documents.client.documentclient)、Azure Storage クライアントなどの .NET クライアントで接続を管理できます。

Azure Functions アプリケーションでサービス固有のクライアントを使用する場合のガイドラインを次に示します。

- 関数呼び出しごとに新しいクライアントを *作成しない*。
- すべての関数呼び出しで使用できる単一の静的クライアントを *作成する*。
- さまざまな関数が同じサービスを使用している場合は、共有ヘルパー クラスで単一の静的クライアントを作成することを *検討する*。

## <a name="client-code-examples"></a>クライアント コードの例

このセクションでは、関数のコードからクライアントを作成および使用するためのベスト プラクティスを示します。

### <a name="httpclient-example-c"></a>HttpClient の例 (C#)

静的 [HttpClient](/dotnet/api/system.net.http.httpclient?view=netcore-3.1&preserve-view=true) インスタンスを作成する C# 関数コードの例を次に示します。

```cs
// Create a single, static HttpClient
private static HttpClient httpClient = new HttpClient();

public static async Task Run(string input)
{
    var response = await httpClient.GetAsync("https://example.com");
    // Rest of function
}
```

.NET の [HttpClient](/dotnet/api/system.net.http.httpclient?view=netcore-3.1&preserve-view=true) について、"クライアントを破棄する方がよいですか" という質問がよく寄せられます。 一般に、`IDisposable` を実装したオブジェクトは、使用の終了後に破棄します。 ただし、関数の終了時に静的クライアントの使用は終了しないため、静的クライアントは破棄しません。 アプリケーションの起動中は、静的クライアントを存続することができます。

### <a name="http-agent-examples-javascript"></a>HTTP エージェントの例 (JavaScript)

優れた接続管理オプションが提供されることから、`node-fetch` モジュールなどの非ネイティブ メソッドではなくネイティブの [`http.agent`](https://nodejs.org/dist/latest-v6.x/docs/api/http.html#http_class_http_agent) クラスを使用する必要があります。 接続パラメーターは、`http.agent` クラスのオプションを使用して構成されます。 HTTP エージェントで利用できるオプションの詳細については、[new Agent(\[options\])](https://nodejs.org/dist/latest-v6.x/docs/api/http.html#http_new_agent_options) を参照してください。

`http.request()` で使用されるグローバル `http.globalAgent` クラスでは、これらのすべての値がそれぞれの既定値に設定されます。 関数の接続制限を構成するための推奨される方法は、グローバルに最大数を設定することです。 次の例は、関数アプリのソケットの最大数を設定します。

```js
http.globalAgent.maxSockets = 200;
```

 次の例では、その要求専用のカスタム HTTP エージェントを使用する新しい HTTP 要求を作成します。

```js
var http = require('http');
var httpAgent = new http.Agent();
httpAgent.maxSockets = 200;
options.agent = httpAgent;
http.request(options, onResponseCallback);
```

### <a name="documentclient-code-example-c"></a>DocumentClient のコード例 (C#)

[DocumentClient](/dotnet/api/microsoft.azure.documents.client.documentclient) は、Azure Cosmos DB のインスタンスに接続します。 Azure Cosmos DB のドキュメントでは、[アプリケーションの有効期間中はシングルトン Azure Cosmos DB クライアントを使用する](../cosmos-db/performance-tips.md#sdk-usage)ことが推奨されています。 次の例では、関数内でそれを行うパターンの 1 つを示します。

```cs
#r "Microsoft.Azure.Documents.Client"
using Microsoft.Azure.Documents.Client;

private static Lazy<DocumentClient> lazyClient = new Lazy<DocumentClient>(InitializeDocumentClient);
private static DocumentClient documentClient => lazyClient.Value;

private static DocumentClient InitializeDocumentClient()
{
    // Perform any initialization here
    var uri = new Uri("example");
    var authKey = "authKey";
    
    return new DocumentClient(uri, authKey);
}

public static async Task Run(string input)
{
    Uri collectionUri = UriFactory.CreateDocumentCollectionUri("database", "collection");
    object document = new { Data = "example" };
    await documentClient.UpsertDocumentAsync(collectionUri, document);
    
    // Rest of function
}
```
Functions v3.x を使用している場合は、Microsoft.Azure.DocumentDB.Core への参照が必要です。 コード内に参照を追加します。

```cs
#r "Microsoft.Azure.DocumentDB.Core"
```
また、トリガー用に "function.proj" という名前のファイルを作成し、次の内容を追加します。

```cs

<Project Sdk="Microsoft.NET.Sdk">
    <PropertyGroup>
        <TargetFramework>netcoreapp3.0</TargetFramework>
    </PropertyGroup>
    <ItemGroup>
        <PackageReference Include="Microsoft.Azure.DocumentDB.Core" Version="2.12.0" />
    </ItemGroup>
</Project>

```
### <a name="cosmosclient-code-example-javascript"></a>CosmosClient のコード例 (JavaScript)
[CosmosClient](/javascript/api/@azure/cosmos/cosmosclient) は、Azure Cosmos DB のインスタンスに接続します。 Azure Cosmos DB のドキュメントでは、[アプリケーションの有効期間中はシングルトン Azure Cosmos DB クライアントを使用する](../cosmos-db/performance-tips.md#sdk-usage)ことが推奨されています。 次の例では、関数内でそれを行うパターンの 1 つを示します。

```javascript
const cosmos = require('@azure/cosmos');
const endpoint = process.env.COSMOS_API_URL;
const key = process.env.COSMOS_API_KEY;
const { CosmosClient } = cosmos;

const client = new CosmosClient({ endpoint, key });
// All function invocations also reference the same database and container.
const container = client.database("MyDatabaseName").container("MyContainerName");

module.exports = async function (context) {
    const { resources: itemArray } = await container.items.readAll().fetchAll();
    context.log(itemArray);
}
```

## <a name="sqlclient-connections"></a>SqlClient の接続

関数コードでは、SQL リレーショナル データベースに接続するために、.NET Framework Data Provider for SQL Server ([SqlClient](/dotnet/api/system.data.sqlclient)) を使用できます。 これは、ADO.NET に依存するデータ フレームワーク ([Entity Framework](/ef/ef6/) など) の基になるプロバイダーでもあります。 [HttpClient](/dotnet/api/system.net.http.httpclient) や [DocumentClient](/dotnet/api/microsoft.azure.documents.client.documentclient) の接続とは異なり、ADO.NET は接続プールを既定で実装します。 ただし、それでも接続を使い果たす可能性があるため、データベースへの接続を最適化する必要があります。 詳しくは、「[SQL Server の接続プール (ADO.NET)](/dotnet/framework/data/adonet/sql-server-connection-pooling)」をご覧ください。

> [!TIP]
> Entity Framework などの一部のデータ フレームワークは、通常、構成ファイルの **ConnectionStrings** セクションから接続文字列を取得します。 その場合は、関数アプリの設定およびローカル プロジェクトの [local.settings.json ファイル](functions-run-local.md#local-settings-file)の **接続文字列** コレクションに、SQL データベースの接続文字列を明示的に追加する必要があります。 関数コードで [SqlConnection](/dotnet/api/system.data.sqlclient.sqlconnection) のインスタンスを作成する場合は、他の接続と共に、接続文字列の値を **アプリケーションの設定** に保存する必要があります。

## <a name="next-steps"></a>次の手順

静的クライアントが推奨される理由の詳細については、「[不適切なインスタンス化のアンチパターン](/azure/architecture/antipatterns/improper-instantiation/)」をご覧ください。

Azure Functions のパフォーマンスに関するその他のヒントについては、「[Azure Functions のパフォーマンスと信頼性を最適化する](functions-best-practices.md)」を参照してください。
