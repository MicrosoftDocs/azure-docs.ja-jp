---
title: チュートリアル:SQL API 用の Azure Cosmos DB グローバル分散チュートリアル
description: チュートリアル:.NET、Java、Python など各種 SDK で SQL API を使用して Azure Cosmos DB グローバル分散をセットアップする方法について説明します。
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: tutorial
ms.date: 11/05/2019
ms.reviewer: sngun
ms.custom: devx-track-python, devx-track-js, devx-track-csharp
ms.openlocfilehash: aacb8d4ffb98d553b17aa52e4c4b11a4837dc1c6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "93337882"
---
# <a name="tutorial-set-up-azure-cosmos-db-global-distribution-using-the-sql-api"></a>チュートリアル:SQL API を使用して Azure Cosmos DB グローバル分散をセットアップする
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

この記事では、Azure portal を使用して Azure Cosmos DB グローバル分散をセットアップし、SQL API を使用して接続する方法を説明します。

この記事に含まれるタスクは次のとおりです。 

> [!div class="checklist"]
> * Azure Portal を使用してグローバル分散を構成する
> * [SQL API](./introduction.md) を使用してグローバル分散を構成する

<a id="portal"></a>
[!INCLUDE [cosmos-db-tutorial-global-distribution-portal](../../includes/cosmos-db-tutorial-global-distribution-portal.md)]

## <a name="connecting-to-a-preferred-region-using-the-sql-api"></a><a id="preferred-locations"></a> SQL API を使用して優先リージョンに接続する

[グローバル分散](distribute-data-globally.md)を活用するために、クライアント アプリケーションでは、ドキュメントの操作の実行に使用するリージョンの順序付き優先リストを指定できます。 Azure Cosmos DB アカウント構成、現在のリージョンの可用性、指定された優先リストに基づいて、書き込み操作と読み取り操作を実行する SQL SDK によって最適なエンドポイントが選択されます。

この優先リストは、SQL SDK を使用して接続を初期化する際に指定されます。 SDK は、Azure リージョンの順序指定済みリストである省略可能なパラメーター `PreferredLocations` を受け取ります。

SDK は、すべての書き込みを現在の書き込みリージョンに自動的に送信します。 すべての読み取りは、優先される場所リストの最初の利用可能なリージョンに送信されます。 要求が失敗すると、クライアントはリストにある次のリージョンを試します。

SDK は、優先される場所で指定されたリージョンからの読み取りのみを試みます。 このため、4 つのリージョンで Azure Cosmos アカウントが利用できるものの、クライアントが `PreferredLocations` 内で読み取り (非書き込み) リージョンを 2 つだけ指定している場合には、`PreferredLocations` で指定されていない読み取りリージョンからは読み取りが行われません。 `PreferredLocations` リストで指定された読み取りリージョンが利用できない場合は、書き込みリージョンから読み取りが行われます。

アプリケーションは、`WriteEndpoint` と `ReadEndpoint` の 2 つのプロパティをチェックすることで、SDK によって選択された現在の書き込みエンドポイントと読み取りエンドポイントを確認できます。これらのプロパティは SDK バージョン 1.8 以上で利用可能です。 `PreferredLocations` プロパティが設定されていない場合、すべての要求が現在の書き込みリージョンから処理されます。

優先される場所を指定せずに `setCurrentLocation` メソッドを使用すると、SDK は、クライアントが実行されている現在のリージョンに基づいて、優先される場所を自動的に設定します。 SDK は、各リージョンを、現在のリージョンとの距離に基づいて並べ替えます。

## <a name="net-sdk"></a>.NET SDK

SDK はコードに変更を加えることなく使用できます。 この場合、SDK は読み取りと書き込みの両方を現在の書き込みリージョンに自動的に転送します。

.NET SDK のバージョン 1.8 以降では、DocumentClient コンストラクターの ConnectionPolicy パラメーターに Microsoft.Azure.Documents.ConnectionPolicy.PreferredLocations という名前のプロパティがあります。 このプロパティは、コレクション型 `<string>` であり、リージョン名のリストを含んでいる必要があります。 文字列値は、「[Azure のリージョン][regions]」ページのリージョン名の列に従って書式設定されます。先頭文字の前と末尾の文字の後ろにはスペースはありません。

現在の書き込みエンドポイントと読み取りエンドポイントはそれぞれ、DocumentClient.WriteEndpoint と DocumentClient.ReadEndpoint で確認することができます。

> [!NOTE]
> エンドポイントの URL は、有効期間が長い定数と見なさないでください。 これらは任意のタイミングでサービスによって更新される可能性があります。 SDK はこの変更を自動的に処理します。
>

# <a name="net-sdk-v2"></a>[.NET SDK V2](#tab/dotnetv2)

.NET V2 SDK を使用している場合は、`PreferredLocations` プロパティを使用して、優先リージョンを設定します。

```csharp
// Getting endpoints from application settings or other configuration location
Uri accountEndPoint = new Uri(Properties.Settings.Default.GlobalDatabaseUri);
string accountKey = Properties.Settings.Default.GlobalDatabaseKey;
  
ConnectionPolicy connectionPolicy = new ConnectionPolicy();

//Setting read region selection preference
connectionPolicy.PreferredLocations.Add(LocationNames.WestUS); // first preference
connectionPolicy.PreferredLocations.Add(LocationNames.EastUS); // second preference
connectionPolicy.PreferredLocations.Add(LocationNames.NorthEurope); // third preference

// initialize connection
DocumentClient docClient = new DocumentClient(
    accountEndPoint,
    accountKey,
    connectionPolicy);

// connect to DocDB
await docClient.OpenAsync().ConfigureAwait(false);
```

または、`SetCurrentLocation` プロパティを使用して、近接度に基づいて優先される場所が選択されるようにすることもできます。

```csharp
// Getting endpoints from application settings or other configuration location
Uri accountEndPoint = new Uri(Properties.Settings.Default.GlobalDatabaseUri);
string accountKey = Properties.Settings.Default.GlobalDatabaseKey;
  
ConnectionPolicy connectionPolicy = new ConnectionPolicy();

connectionPolicy.SetCurrentLocation("West US 2"); /

// initialize connection
DocumentClient docClient = new DocumentClient(
    accountEndPoint,
    accountKey,
    connectionPolicy);

// connect to DocDB
await docClient.OpenAsync().ConfigureAwait(false);
```

# <a name="net-sdk-v3"></a>[.NET SDK V3](#tab/dotnetv3)

.NET V3 SDK を使用している場合は、`ApplicationPreferredRegions` プロパティを使用して、優先リージョンを設定します。

```csharp

CosmosClientOptions options = new CosmosClientOptions();
options.ApplicationName = "MyApp";
options.ApplicationPreferredRegions = new List<string> {Regions.WestUS, Regions.WestUS2};

CosmosClient client = new CosmosClient(connectionString, options);

```

または、`ApplicationRegion` プロパティを使用して、近接度に基づいて優先される場所が選択されるようにすることもできます。

```csharp
CosmosClientOptions options = new CosmosClientOptions();
options.ApplicationName = "MyApp";
// If the application is running in West US
options.ApplicationRegion = Regions.WestUS;

CosmosClient client = new CosmosClient(connectionString, options);
```

---

## <a name="nodejsjavascript"></a>Node.js と JavaScript

> [!NOTE]
> エンドポイントの URL は、有効期間が長い定数と見なさないでください。 これらは任意のタイミングでサービスによって更新される可能性があります。 SDK はこの変更を自動的に処理します。
>
>

Node.js と Javascript のコード例を以下に示します。

```JavaScript
// Setting read region selection preference, in the following order -
// 1 - West US
// 2 - East US
// 3 - North Europe
const preferredLocations = ['West US', 'East US', 'North Europe'];

// initialize the connection
const client = new CosmosClient{ endpoint, key, connectionPolicy: { preferredLocations } });
```

## <a name="python-sdk"></a>Python SDK

次のコードは、Python SDK を使用して優先される場所を設定する方法を示しています。

```python
connectionPolicy = documents.ConnectionPolicy()
connectionPolicy.PreferredLocations = ['West US', 'East US', 'North Europe']
client = cosmos_client.CosmosClient(ENDPOINT, {'masterKey': MASTER_KEY}, connectionPolicy)

```

## <a name="java-v4-sdk"></a><a id="java4-preferred-locations"></a> Java V4 SDK

次のコードは、Java SDK を使用して優先される場所を設定する方法を示しています。

# <a name="async"></a>[非同期](#tab/api-async)

   [Java SDK V4](sql-api-sdk-java-v4.md) (Maven [com.azure::azure-cosmos](https://mvnrepository.com/artifact/com.azure/azure-cosmos)) 非同期 API

   [!code-java[](~/azure-cosmos-java-sql-api-samples/src/main/java/com/azure/cosmos/examples/documentationsnippets/async/SampleDocumentationSnippetsAsync.java?name=TutorialGlobalDistributionPreferredLocationAsync)]

# <a name="sync"></a>[同期](#tab/api-sync)

   [Java SDK V4](sql-api-sdk-java-v4.md) (Maven [com.azure::azure-cosmos](https://mvnrepository.com/artifact/com.azure/azure-cosmos)) 同期 API

   [!code-java[](~/azure-cosmos-java-sql-api-samples/src/main/java/com/azure/cosmos/examples/documentationsnippets/sync/SampleDocumentationSnippets.java?name=TutorialGlobalDistributionPreferredLocationSync)]

--- 

## <a name="rest"></a>REST

データベース アカウントが複数リージョンで利用できるようになったら、クライアントは URI `https://{databaseaccount}.documents.azure.com/` に対して GET 要求を実行してその可用性を照会できます。

リージョンのほか、そのリージョンに対応するレプリカの Azure Cosmos DB エンドポイント URI がリストとしてサービスによって返されます。 現在の書き込みリージョンが応答に示されます。 クライアントはその後、次のようにそれ以降のすべての REST API 要求に対して適切なエンドポイントを選択できます。

応答の例

```json
{
    "_dbs": "//dbs/",
    "media": "//media/",
    "writableLocations": [
        {
            "Name": "West US",
            "DatabaseAccountEndpoint": "https://globaldbexample-westus.documents.azure.com:443/"
        }
    ],
    "readableLocations": [
        {
            "Name": "East US",
            "DatabaseAccountEndpoint": "https://globaldbexample-eastus.documents.azure.com:443/"
        }
    ],
    "MaxMediaStorageUsageInMB": 2048,
    "MediaStorageUsageInMB": 0,
    "ConsistencyPolicy": {
        "defaultConsistencyLevel": "Session",
        "maxStalenessPrefix": 100,
        "maxIntervalInSeconds": 5
    },
    "addresses": "//addresses/",
    "id": "globaldbexample",
    "_rid": "globaldbexample.documents.azure.com",
    "_self": "",
    "_ts": 0,
    "_etag": null
}
```

* PUT 要求、POST 要求、DELETE 要求はすべて、示された書き込み URI に送信されます。
* すべての GET 要求とその他の読み取り専用の要求 (クエリなど) は、クライアントによって選択された任意のエンドポイントに送信できます。

読み取り専用のリージョンへの書き込み要求は失敗し、HTTP エラー コード 403 ("許可されていません") が表示されます。

クライアントの最初の検出フェーズの後に書き込みリージョンが変更された場合、以前の書き込みリージョンへの以降の書き込みは失敗し、HTTP エラー コード 403 ("許可されていません") が返されます。 クライアントはその後、更新された書き込みリージョンを把握するためにリージョンのリストをもう一度取得する必要があります。

このチュートリアルはこれで終わりです。 [Azure Cosmos DB の一貫性レベル](consistency-levels.md)に関する記事を読んで、グローバルにレプリケートされたアカウントの整合性を管理する方法について確認できます。 また、Azure Cosmos DB におけるグローバル データベース レプリケーションの動作の詳細については、[Azure Cosmos DB を使用したデータのグローバル分散](distribute-data-globally.md)に関する記事を参照してください。

## <a name="next-steps"></a>次のステップ

このチュートリアルでは、次の手順を行いました。

> [!div class="checklist"]
> * Azure Portal を使用してグローバル分散を構成する
> * SQL API を使用してグローバル分散を構成する

これで次のチュートリアルに進むことができます。Azure Cosmos DB ローカル エミュレーターを使用してローカルで開発する方法について学びます。

> [!div class="nextstepaction"]
> [エミュレーターを使用したローカル開発](local-emulator.md)

[regions]: https://azure.microsoft.com/regions/