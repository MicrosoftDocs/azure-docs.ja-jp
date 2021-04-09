---
title: Gremlin SDK で Azure Cosmos DB リソース トークンを使用する
description: リソース トークンを作成し、それらを使用して Graph データベースにアクセスする方法について説明します。
author: christopheranderson
ms.author: chrande
ms.service: cosmos-db
ms.subservice: cosmosdb-graph
ms.topic: how-to
ms.date: 09/06/2019
ms.custom: devx-track-csharp
ms.openlocfilehash: 22c048b748806404ccfa580e660552a1744f3781
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "93361695"
---
# <a name="use-azure-cosmos-db-resource-tokens-with-the-gremlin-sdk"></a>Gremlin SDK で Azure Cosmos DB リソース トークンを使用する
[!INCLUDE[appliesto-gremlin-api](includes/appliesto-gremlin-api.md)]

この記事では、[Azure Cosmos DB リソース トークン](secure-access-to-data.md)を使用して、Gremlin SDK 経由で Graph データベースにアクセスする方法について説明します。

## <a name="create-a-resource-token"></a>リソース グループを作成する

Apache TinkerPop Gremlin SDK には、リソース トークンを作成するための API がありません。 "*リソース トークン*" という用語は、Azure Cosmos DB の概念です。 リソース トークンを作成するには、[Azure Cosmos DB SDK](sql-api-sdk-dotnet.md) をダウンロードします。 ご利用のアプリケーションでリソース トークンを作成し、それらを使用して Graph データベースにアクセスする必要がある場合は、個別の SDK が 2 つ必要です。

リソース トークンの上位にあるオブジェクト モデル階層を次のアウトラインで示します。

- **Azure Cosmos DB アカウント** - DNS が関連付けられている最上位のエンティティ (たとえば、`contoso.gremlin.cosmos.azure.com`)。
  - **Azure Cosmos DB データベース**
    - **User**
      - **権限**
        - **トークン** - 許可または拒否するアクションを示すアクセス許可オブジェクト プロパティです。

リソース トークンに使用する形式は `"type=resource&ver=1&sig=<base64 string>;<base64 string>;"` です。 この文字列はクライアントに対して非透過的であり、変更または解釈を行わずにそのまま使用する必要があります。

```csharp
// Notice that document client is created against .NET SDK endpoint, rather than Gremlin.
DocumentClient client = new DocumentClient(
  new Uri("https://contoso.documents.azure.com:443/"), 
  "<primary key>", 
  new ConnectionPolicy 
  {
    EnableEndpointDiscovery = false, 
    ConnectionMode = ConnectionMode.Direct 
  });

  // Read specific permission to obtain a token.
  // The token isn't returned during the ReadPermissionReedAsync() call.
  // The call succeeds only if database id, user id, and permission id already exist. 
  // Note that <database id> is not a database name. It is a base64 string that represents the database identifier, for example "KalVAA==".
  // Similar comment applies to <user id> and <permission id>.
  Permission permission = await client.ReadPermissionAsync(UriFactory.CreatePermissionUri("<database id>", "<user id>", "<permission id>"));

  Console.WriteLine("Obtained token {0}", permission.Token);
}
```

## <a name="use-a-resource-token"></a>リソース トークンを使用する
リソース トークンは、GremlinServer クラスを作成するときに、"パスワード" プロパティとして直接使用できます。

```csharp
// The Gremlin application needs to be given a resource token. It can't discover the token on its own.
// You can obtain the token for a given permission by using the Azure Cosmos DB SDK, or you can pass it into the application as a command line argument or configuration value.
string resourceToken = GetResourceToken();

// Configure the Gremlin server to use a resource token rather than a primary key.
GremlinServer server = new GremlinServer(
  "contoso.gremlin.cosmosdb.azure.com",
  port: 443,
  enableSsl: true,
  username: "/dbs/<database name>/colls/<collection name>",

  // The format of the token is "type=resource&ver=1&sig=<base64 string>;<base64 string>;".
  password: resourceToken);

  using (GremlinClient gremlinClient = new GremlinClient(server, new GraphSON2Reader(), new GraphSON2Writer(), GremlinClient.GraphSON2MimeType))
  {
      await gremlinClient.SubmitAsync("g.V().limit(1)");
  }
```

同じアプローチを、すべての TinkerPop Gremlin SDK で使用できます。

```java
Cluster.Builder builder = Cluster.build();

AuthProperties authenticationProperties = new AuthProperties();
authenticationProperties.with(AuthProperties.Property.USERNAME,
    String.format("/dbs/%s/colls/%s", "<database name>", "<collection name>"));

// The format of the token is "type=resource&ver=1&sig=<base64 string>;<base64 string>;".
authenticationProperties.with(AuthProperties.Property.PASSWORD, resourceToken);

builder.authProperties(authenticationProperties);
```

## <a name="limit"></a>制限

1 つの Gremlin アカウントで発行できるトークンの数は無制限です。 ただし、1 時間以内に同時に使用できるトークンは最大 100 個のみです。 アプリケーションが 1 時間あたりのトークン制限を超えた場合、認証要求は拒否され、次のエラー メッセージが表示されます。"Exceeded allowed resource token limit of 100 that can be used concurrently." (同時に使用できるリソース トークン制限である 100 の許容値を超えました。) 新しいトークン用にスロットを解放するために、特定のトークンを使用しているアクティブな接続を閉じることは効果的ではありません。 Azure Cosmos DB Gremlin データベース エンジンによって、認証要求の直前の 1 時間にわたって、一意のトークンが追跡されます。

## <a name="permission"></a>権限

アプリケーションでリソース トークン使用中に発生する一般的なエラーは、"Insufficient permissions provided in the authorization header for the corresponding request. Please retry with another authorization header." (対応する要求の認証ヘッダーに与えられているアクセス許可が不十分です。別の認証ヘッダーを使用して再試行してください。) です。 Gremlin トラバーサルでエッジまたは頂点の書き込みが試みられたが、リソース トークンからは *読み取り* アクセス許可しか付与されていない場合、このエラーが返されます。 次のステップのいずれかが含まれているかどうかを確認するために、ご利用のトラバーサルを調べてください: *.addV()* 、 *.addE()* 、 *.drop()* 、 *.property()* 。

## <a name="next-steps"></a>次のステップ
* Azure Cosmos DB の [Azure ロールベースのアクセス制御 (Azure RBAC)](role-based-access-control.md)
* Azure Cosmos DB の[データへのアクセスをセキュリティで保護する方法](secure-access-to-data.md)
