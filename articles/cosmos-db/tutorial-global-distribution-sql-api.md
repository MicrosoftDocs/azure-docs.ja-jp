---
title: SQL API 用の Azure Cosmos DB グローバル分散チュートリアル
description: SQL API を使用して Azure Cosmos DB グローバル分散をセットアップする方法について説明します。
author: rimman
ms.service: cosmos-db
ms.topic: tutorial
ms.date: 07/15/2019
ms.author: rimman
ms.reviewer: sngun
ms.openlocfilehash: a566094f88ba9ffd25eadd046ae7254e26b9c2cf
ms.sourcegitcommit: b2db98f55785ff920140f117bfc01f1177c7f7e2
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/16/2019
ms.locfileid: "68234596"
---
# <a name="set-up-azure-cosmos-db-global-distribution-using-the-sql-api"></a>SQL API を使用して Azure Cosmos DB グローバル分散をセットアップする

この記事では、Azure Portal を使用して Azure Cosmos DB グローバル分散をセットアップし、SQL API を使用して接続する方法を説明します。

この記事に含まれるタスクは次のとおりです。 

> [!div class="checklist"]
> * Azure Portal を使用してグローバル分散を構成する
> * [SQL API](sql-api-introduction.md) を使用してグローバル分散を構成する

<a id="portal"></a>
[!INCLUDE [cosmos-db-tutorial-global-distribution-portal](../../includes/cosmos-db-tutorial-global-distribution-portal.md)]


## <a name="connecting-to-a-preferred-region-using-the-sql-api"></a>SQL API を使用して優先リージョンに接続する

[グローバル配布](distribute-data-globally.md)を活用するために、クライアント アプリケーションでは、ドキュメントの操作の実行に使用するリージョンの順序付き優先リストを指定できます。 これは、接続ポリシーを設定して行います。 Azure Cosmos DB アカウント構成、現在のリージョンの可用性、指定された優先リストに基づいて、書き込み操作と読み取り操作を実行する SQL SDK によって最適なエンドポイントが選択されます。

この優先リストは、SQL SDK を使用して接続を初期化する際に指定されます。 SDK は、Azure リージョンの順序付きリストである省略可能なパラメーター "PreferredLocations" を受け取ります。

SDK は、すべての書き込みを現在の書き込みリージョンに自動的に送信します。

すべての読み取りは、PreferredLocations リストの最初の利用可能なリージョンに送信されます。 要求が失敗すると、クライアントはリストにある次のリージョンを試します。これが繰り返されます。

SDK は、PreferredLocations で指定されたリージョンからの読み取りのみを試みます。 このため、4 つのリージョンでデータベース アカウントが利用できるものの、クライアントが PreferredLocations の読み取り (非書き込み) リージョンを 2 つだけ指定している場合などには、PreferredLocations で指定されていない読み取りリージョンの外で読み取りが処理されません。 PreferredLocations で指定された読み取りリージョンが利用できない場合は、書き込みリージョンの外で読み取りが処理されます。

アプリケーションは、WriteEndpoint と ReadEndpoint の 2 つのプロパティをチェックすることで、SDK によって選択された現在の書き込みエンドポイントと読み取りエンドポイントを確認できます。これらのプロパティは SDK バージョン 1.8 以上で利用可能です。

PreferredLocations プロパティが設定されていない場合、すべての要求が現在の書き込みリージョンから処理されます。

## <a name="net-sdk"></a>.NET SDK
SDK はコードに変更を加えることなく使用できます。 この場合、SDK は読み取りと書き込みの両方を現在の書き込みリージョンに自動的に転送します。

.NET SDK のバージョン 1.8 以降では、DocumentClient コンストラクターの ConnectionPolicy パラメーターに Microsoft.Azure.Documents.ConnectionPolicy.PreferredLocations という名前のプロパティがあります。 このプロパティは、コレクション型 `<string>` であり、リージョン名のリストを含んでいる必要があります。 文字列値は、「[Azure のリージョン][regions]」ページのリージョン名の列に従って書式設定されます。先頭と末尾の文字のそれぞれ前後にはスペースはありません。

現在の書き込みエンドポイントと読み取りエンドポイントはそれぞれ、DocumentClient.WriteEndpoint と DocumentClient.ReadEndpoint で使用できます。

> [!NOTE]
> エンドポイントの URL は、有効期間が長い定数と見なさないでください。 これらは任意のタイミングでサービスによって更新される可能性があります。 SDK はこの変更を自動的に処理します。
>
>

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

## <a name="nodejsjavascript"></a>Node.js と JavaScript

SDK はコードに変更を加えることなく使用できます。 この場合、SDK は読み取りと書き込みの両方を現在の書き込みリージョンに自動的に転送します。

各 SDK のバージョン 1.8 以降では、DocumentClient コンストラクターの ConnectionPolicy パラメーターに DocumentClient.ConnectionPolicy.PreferredLocations という名前の新しいプロパティがあります。 このパラメーターは、リージョン名のリストを受け取る文字列の配列です。 この名前は「[Azure のリージョン][regions]」ページのリージョン名の列に従って書式設定されます。 便利なオブジェクト AzureDocuments.Regions で事前定義された定数を使用することもできます。

現在の書き込みエンドポイントと読み取りエンドポイントはそれぞれ、DocumentClient.getWriteEndpoint と DocumentClient.getReadEndpoint で使用できます。

> [!NOTE]
> エンドポイントの URL は、有効期間が長い定数と見なさないでください。 これらは任意のタイミングでサービスによって更新される可能性があります。 SDK はこの変更を自動的に処理します。
>
>

Node.js と Javascript のコード例を以下に示します。

```JavaScript
// Creating a ConnectionPolicy object
var connectionPolicy = new DocumentBase.ConnectionPolicy();

// Setting read region selection preference, in the following order -
// 1 - West US
// 2 - East US
// 3 - North Europe
connectionPolicy.PreferredLocations = ['West US', 'East US', 'North Europe'];

// initialize the connection
var client = new DocumentDBClient(host, { masterKey: masterKey }, connectionPolicy);
```

## <a name="python-sdk"></a>Python SDK

次のコードは、Python SDK を使用して優先される場所を設定する方法を示しています。

```python

connectionPolicy = documents.ConnectionPolicy()
connectionPolicy.PreferredLocations = ['West US', 'East US', 'North Europe']
client = cosmos_client.CosmosClient(ENDPOINT, {'masterKey': MASTER_KEY}, connectionPolicy)

```

## <a name="java-v2-sdk"></a>Java V2 SDK

次のコードは、Java SDK を使用して優先される場所を設定する方法を示しています。

```java
ConnectionPolicy policy = new ConnectionPolicy();
policy.setUsingMultipleWriteLocations(true);
policy.setPreferredLocations(Arrays.asList("East US", "West US", "Canada Central"));
AsyncDocumentClient client =
        new AsyncDocumentClient.Builder()
                .withMasterKeyOrResourceToken(this.accountKey)
                .withServiceEndpoint(this.accountEndpoint)
                .withConnectionPolicy(policy)
                .build();
```

## <a name="rest"></a>REST
データベース アカウントが複数リージョンで利用できるようになったら、クライアントは次の URI に対して GET 要求を実行してその可用性を照会できます。

    https://{databaseaccount}.documents.azure.com/

リージョンのほか、そのリージョンに対応するレプリカの Azure Cosmos DB エンドポイント URI がリストとしてサービスによって返されます。 現在の書き込みリージョンが応答に示されます。 クライアントはその後、次のようにそれ以降のすべての REST API 要求に対して適切なエンドポイントを選択できます。

応答の例

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


* PUT 要求、POST 要求、DELETE 要求はすべて、示された書き込み URI に送信されます。
* すべての GET 要求とその他の読み取り専用の要求 (クエリなど) は、クライアントによって選択された任意のエンドポイントに送信できます。

読み取り専用のリージョンへの書き込み要求は失敗し、HTTP エラー コード 403 ("許可されていません") が表示されます。

クライアントの最初の検出フェーズの後に書き込みリージョンが変更された場合、以前の書き込みリージョンへの以降の書き込みは失敗し、HTTP エラー コード 403 ("許可されていません") が表示されます。 クライアントはその後、更新された書き込みリージョンを把握するためにリージョンのリストをもう一度取得する必要があります。

このチュートリアルはこれで終わりです。 [Azure Cosmos DB の一貫性レベル](consistency-levels.md)に関する記事を読んで、グローバルにレプリケートされたアカウントの整合性を管理する方法について確認できます。 また、Azure Cosmos DB におけるグローバル データベース レプリケーションの動作の詳細については、[Azure Cosmos DB を使用したデータのグローバル分散](distribute-data-globally.md)に関する記事を参照してください。

## <a name="next-steps"></a>次の手順

このチュートリアルでは、次の手順を行いました。

> [!div class="checklist"]
> * Azure Portal を使用してグローバル分散を構成する
> * SQL API を使用してグローバル分散を構成する

これで次のチュートリアルに進むことができます。Azure Cosmos DB ローカル エミュレーターを使用してローカルで開発する方法について学びます。

> [!div class="nextstepaction"]
> [エミュレーターを使用したローカル開発](local-emulator.md)

[regions]: https://azure.microsoft.com/regions/

