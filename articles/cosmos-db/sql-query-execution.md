---
title: Azure Cosmos DB での SQL クエリの実行
description: Azure Cosmos DB での SQL クエリの実行について学習します
author: timsander1
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/31/2019
ms.author: tisande
ms.openlocfilehash: e4e26b658bd29e4589be40e4d29935059836c909
ms.sourcegitcommit: a12b2c2599134e32a910921861d4805e21320159
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/24/2019
ms.locfileid: "67343278"
---
# <a name="azure-cosmos-db-sql-query-execution"></a>Azure Cosmos DB SQL クエリの実行

HTTP/HTTPS 要求機能を持つ任意の言語で Cosmos DB REST API を呼び出すことができます。 さらに、.NET、Node.js、JavaScript、Python プログラミング言語用のプログラミング ライブラリも Cosmos DB に用意されています。 REST API とライブラリはすべて SQL 経由のクエリをサポートしており、NET SDK は [LINQ クエリ](sql-query-linq-to-sql.md)もサポートしています。

以下の例では、クエリを作成して Cosmos DB データベース アカウントに送信する方法について説明します。

## <a id="REST-API"></a>REST API

Cosmos DB は、HTTP を介したオープンな RESTful プログラミング モデルを提供します。 リソース モデルは、Azure サブスクリプションがプロビジョニングする、データベース アカウントに従属する一連のリソースから構成されます。 データベース アカウントは一連の*データベース*で構成され、各データベースには複数の*コンテナー*が含まれています。さらにそのそれぞれに、*項目*、UDF、その他のリソースの種類が含まれます。 各 Cosmos DB リソース モデルは、不変の論理 URI を使用してアドレス指定できます。 一連のリソースは、*フィード*と呼ばれます。 

これらのリソースとの基本的な対話モデルでは、HTTP の動詞である `GET`、`PUT`、`POST`、および `DELETE` が標準の解釈で使用されます。 `POST` を使用して、新しいリソースの作成、ストアド プロシージャの実行、または Cosmos DB クエリの発行を行います。 クエリは常に読み取り専用の操作で、副作用はありません。

以下の例は、サンプル項目に対する SQL API クエリの `POST` を示しています。 このクエリには、JSON の `name` プロパティに対するシンプルなフィルターがあります。 `x-ms-documentdb-isquery` と Content-Type: `application/query+json` ヘッダーは、クエリによる操作であることを示しています。 `mysqlapicosmosdb.documents.azure.com:443` を Cosmos DB アカウントの URI に置き換えます。

```json
    POST https://mysqlapicosmosdb.documents.azure.com:443/docs HTTP/1.1
    ...
    x-ms-documentdb-isquery: True
    Content-Type: application/query+json

    {
        "query": "SELECT * FROM Families f WHERE f.id = @familyId",
        "parameters": [
            {"name": "@familyId", "value": "AndersenFamily"}
        ]
    }
```

結果は次のようになります。

```json
    HTTP/1.1 200 Ok
    x-ms-activity-id: 8b4678fa-a947-47d3-8dd3-549a40da6eed
    x-ms-item-count: 1
    x-ms-request-charge: 0.32

    {  
       "_rid":"u1NXANcKogE=",
       "Documents":[  
          {  
             "id":"AndersenFamily",
             "lastName":"Andersen",
             "parents":[  
                {  
                   "firstName":"Thomas"
                },
                {  
                   "firstName":"Mary Kay"
                }
             ],
             "children":[  
                {  
                   "firstName":"Henriette Thaulow",
                   "gender":"female",
                   "grade":5,
                   "pets":[  
                      {  
                         "givenName":"Fluffy"
                      }
                   ]
                }
             ],
             "address":{  
                "state":"WA",
                "county":"King",
                "city":"Seattle"
             },
             "_rid":"u1NXANcKogEcAAAAAAAAAA==",
             "_ts":1407691744,
             "_self":"dbs\/u1NXAA==\/colls\/u1NXANcKogE=\/docs\/u1NXANcKogEcAAAAAAAAAA==\/",
             "_etag":"00002b00-0000-0000-0000-53e7abe00000",
             "_attachments":"_attachments\/"
          }
       ],
       "count":1
    }
```

次のもっと複雑なクエリは、結合から複数の結果を返します。

```json
    POST https://https://mysqlapicosmosdb.documents.azure.com:443/docs HTTP/1.1
    ...
    x-ms-documentdb-isquery: True
    Content-Type: application/query+json

    {
        "query": "SELECT
                     f.id AS familyName,
                     c.givenName AS childGivenName,
                     c.firstName AS childFirstName,
                     p.givenName AS petName
                  FROM Families f
                  JOIN c IN f.children
                  JOIN p in c.pets",
        "parameters": []
    }
```

結果は次のようになります。 

```json
    HTTP/1.1 200 Ok
    x-ms-activity-id: 568f34e3-5695-44d3-9b7d-62f8b83e509d
    x-ms-item-count: 1
    x-ms-request-charge: 7.84

    {  
       "_rid":"u1NXANcKogE=",
       "Documents":[  
          {  
             "familyName":"AndersenFamily",
             "childFirstName":"Henriette Thaulow",
             "petName":"Fluffy"
          },
          {  
             "familyName":"WakefieldFamily",
             "childGivenName":"Jesse",
             "petName":"Goofy"
          },
          {  
             "familyName":"WakefieldFamily",
             "childGivenName":"Jesse",
             "petName":"Shadow"
          }
       ],
       "count":3
    }
```

クエリ結果を 1 つのページに収めることができない場合、REST API は `x-ms-continuation-token` 応答ヘッダーを使って継続トークンを返します。 クライアントは、このヘッダーを後続の結果に含めることで、結果を改ページすることができます。 ページあたりの結果の数も、`x-ms-max-item-count` 数値ヘッダーによって制御できます。

クエリに COUNT などの集計関数が含まれる場合、クエリ ページは、結果の 1 ページのみに対する部分的な集計値を返すことがあります。 クライアントが、最終的な結果を得るためには、これらの結果に対して二次的な集計を実行する必要があります。 たとえば、個々のページで返された数を集計して合計数を返します。

クエリのデータ一貫性ポリシーを管理するには、すべての REST API 要求と同様に `x-ms-consistency-level` ヘッダーを使用します。 セッションの一貫性には、クエリ要求で最新の `x-ms-session-token` Cookie ヘッダーもエコーする必要があります。 クエリされたコンテナーのインデックス作成ポリシーは、クエリ結果の一貫性にも影響を与えます。 コンテナーの既定のインデックス作成ポリシーの設定では、インデックスは項目の内容に関して常に最新の状態になり、クエリ結果はデータ用に選択された一貫性と一致します。 詳細については、[Azure Cosmos DB の一貫性レベル][consistency-levels]に関する記事をご覧ください。

コンテナーで構成されたインデックス作成ポリシーが指定されたクエリをサポートできない場合、Azure Cosmos DB サーバーによって 400 "Bad Request" が返されます。 このエラー メッセージは、インデックス作成から明示的に除外されたパスが含まれているクエリに対して返されます。 `x-ms-documentdb-query-enable-scan` ヘッダーを指定して、インデックスを利用できない場合のクエリによるスキャン実行を許可することができます。

`x-ms-documentdb-populatequerymetrics` ヘッダーを `true` に設定することによって、クエリ実行についての詳細なメトリックを取得できます。 詳細については、[Azure Cosmos DB の SQL クエリ メトリック](sql-api-query-metrics.md)に関するページをご覧ください。

## <a name="c-net-sdk"></a>C# (.NET SDK)

.NET SDK は LINQ クエリと SQL クエリの両方をサポートしています。 次の例は、.NET で前述したフィルター クエリを実行する方法を示しています。

```csharp
    foreach (var family in client.CreateDocumentQuery(containerLink,
        "SELECT * FROM Families f WHERE f.id = \"AndersenFamily\""))
    {
        Console.WriteLine("\tRead {0} from SQL", family);
    }

    SqlQuerySpec query = new SqlQuerySpec("SELECT * FROM Families f WHERE f.id = @familyId");
    query.Parameters = new SqlParameterCollection();
    query.Parameters.Add(new SqlParameter("@familyId", "AndersenFamily"));

    foreach (var family in client.CreateDocumentQuery(containerLink, query))
    {
        Console.WriteLine("\tRead {0} from parameterized SQL", family);
    }

    foreach (var family in (
        from f in client.CreateDocumentQuery(containerLink)
        where f.Id == "AndersenFamily"
        select f))
    {
        Console.WriteLine("\tRead {0} from LINQ query", family);
    }

    foreach (var family in client.CreateDocumentQuery(containerLink)
        .Where(f => f.Id == "AndersenFamily")
        .Select(f => f))
    {
        Console.WriteLine("\tRead {0} from LINQ lambda", family);
    }
```

次の例では、2 つのプロパティの等値比較を各項目内で実行し、匿名プロジェクションを使用しています。

```csharp
    foreach (var family in client.CreateDocumentQuery(containerLink,
        @"SELECT {""Name"": f.id, ""City"":f.address.city} AS Family
        FROM Families f
        WHERE f.address.city = f.address.state"))
    {
        Console.WriteLine("\tRead {0} from SQL", family);
    }

    foreach (var family in (
        from f in client.CreateDocumentQuery<Family>(containerLink)
        where f.address.city == f.address.state
        select new { Name = f.Id, City = f.address.city }))
    {
        Console.WriteLine("\tRead {0} from LINQ query", family);
    }

    foreach (var family in
        client.CreateDocumentQuery<Family>(containerLink)
        .Where(f => f.address.city == f.address.state)
        .Select(f => new { Name = f.Id, City = f.address.city }))
    {
        Console.WriteLine("\tRead {0} from LINQ lambda", family);
    }
```

次の例は、LINQ `SelectMany` によって表された結合を示しています。

```csharp
    foreach (var pet in client.CreateDocumentQuery(containerLink,
          @"SELECT p
            FROM Families f
                 JOIN c IN f.children
                 JOIN p in c.pets
            WHERE p.givenName = ""Shadow"""))
    {
        Console.WriteLine("\tRead {0} from SQL", pet);
    }

    // Equivalent in Lambda expressions:
    foreach (var pet in
        client.CreateDocumentQuery<Family>(containerLink)
        .SelectMany(f => f.children)
        .SelectMany(c => c.pets)
        .Where(p => p.givenName == "Shadow"))
    {
        Console.WriteLine("\tRead {0} from LINQ lambda", pet);
    }
```

前の例に示したとおり、.NET クライアントは `foreach` ブロックのクエリ結果のすべてのページを自動で反復処理します。 [REST API](#REST-API) のセクションで説明したクエリ オプションは、.NET SDK でも利用可能です。これには、`FeedOptions` および `FeedResponse` クラスを `CreateDocumentQuery` メソッドで使用します。 ページの数は `MaxItemCount` 設定を使用して制御できます。

開発者は、`IDocumentQueryable` を作成することでページ設定を明示的に制御できます。これには `IQueryable` オブジェクトを使用し、次に `ResponseContinuationToken` の値を読み取り、これらを `RequestContinuationToken` として `FeedOptions` 内で渡します。 `EnableScanInQuery` を設定して、構成されたインデックス作成ポリシーでクエリがサポートされない場合に、スキャンを有効にすることができます。 パーティション分割コンテナーの場合は、`PartitionKey` を使用すると 1 つのパーティションに対してクエリを実行できますが、Azure Cosmos DB ではクエリ テキストからこれを自動的に抽出できます。 `EnableCrossPartitionQuery` を使用して、複数のパーティションに対してクエリを実行できます。

クエリを含む他の .NET サンプルについては、GitHub の [Azure Cosmos DB .NET サンプル](https://github.com/Azure/azure-cosmosdb-dotnet)を参照してください。

## <a id="JavaScript-server-side-API"></a>JavaScript のサーバー側 API

Azure Cosmos DB が提供するプログラミング モデルでは、ストアド プロシージャとトリガーを使用して、[JavaScript ベースのアプリケーション ロジックをコンテナーで直接実行する](stored-procedures-triggers-udfs.md)ことができます。 コンテナー レベルで登録された JavaScript ロジックは、アンビエント ACID トランザクションでラップされた特定のコンテナーの項目に対してデータベース操作を発行できます。

以下の例は、JavaScript サーバー API で `queryDocuments` を使用して、ストアド プロシージャとトリガー内からクエリを実行する方法を示しています。

```javascript
    function findName(givenName, familyName) {
        var context = getContext();
        var containerManager = context.getCollection();
        var containerLink = containerManager.getSelfLink()

        // create a new item.
        containerManager.createDocument(containerLink,
            { givenName: givenName, familyName: familyName },
            function (err, documentCreated) {
                if (err) throw new Error(err.message);

                // filter items by familyName
                var filterQuery = "SELECT * from root r WHERE r.familyName = 'Wakefield'";
                containerManager.queryDocuments(containerLink,
                    filterQuery,
                    function (err, matchingDocuments) {
                        if (err) throw new Error(err.message);
    context.getResponse().setBody(matchingDocuments.length);

                        // Replace the familyName for all items that satisfied the query.
                        for (var i = 0; i < matchingDocuments.length; i++) {
                            matchingDocuments[i].familyName = "Robin Wakefield";
                            // we don't need to execute a callback because they are in parallel
                            containerManager.replaceDocument(matchingDocuments[i]._self,
                                matchingDocuments[i]);
                        }
                    })
            });
    }
```

## <a name="next-steps"></a>次の手順

- [Azure Cosmos DB の概要](introduction.md)
- [Azure Cosmos DB .NET のサンプル](https://github.com/Azure/azure-cosmosdb-dotnet)
- [Azure Cosmos DB の一貫性レベル](consistency-levels.md)
