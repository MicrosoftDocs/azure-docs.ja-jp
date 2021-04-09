---
title: マネージド ID を使用して、Cosmos DB アカウントへの接続を設定する
titleSuffix: Azure Cognitive Search
description: マネージド ID を使用して Cosmos DB アカウントへのインデクサー接続を設定する方法を学ぶ
manager: luisca
author: markheff
ms.author: maheff
ms.devlang: rest-api
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 09/22/2020
ms.openlocfilehash: 2a1744feedc3e0ffae6cf2cd45cd090a6c2f06d5
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "93422095"
---
# <a name="set-up-an-indexer-connection-to-a-cosmos-db-database-using-a-managed-identity"></a>マネージド ID を使用して Cosmos DB データベースへのインデクサー接続を設定する

このページでは、データ ソースのオブジェクト接続文字列で資格情報を指定する代わりに、マネージド ID を使用して Azure Cosmos DB データベースへのインデクサー接続を設定する方法を説明します。

この機能についてさらに学ぶ前に、インデクサーとは何かについて、およびデータ ソースに対してインデクサーを設定する方法について理解しておくことをお勧めします。 以下のリンクで詳しい情報を確認できます。

* [インデクサーの概要](search-indexer-overview.md)
* [Azure Cosmos DB インデクサー](search-howto-index-cosmosdb.md)

## <a name="set-up-a-connection-using-a-managed-identity"></a>マネージド ID を使用して接続を設定する

### <a name="1---turn-on-system-assigned-managed-identity"></a>1 - システム割り当てマネージド ID をオンにする

システム割り当てマネージド ID が有効になると、Azure で検索サービスのための ID が作成されます。これは、同じテナントとサブスクリプション内の他の Azure サービスに対する認証に使用することができます。 この ID はその後、インデックス作成中にデータへのアクセスを許可する Azure ロールベースのアクセス制御 (Azure RBAC) の割り当てで、使用することができます。

![システム割り当てマネージド ID をオンにする](./media/search-managed-identities/turn-on-system-assigned-identity.png "システム割り当てマネージド ID をオンにする")

**[保存]** を選択した後に、検索サービスに割り当てられたオブジェクト ID が表示されます。

![オブジェクト ID](./media/search-managed-identities/system-assigned-identity-object-id.png "Object ID")
 
### <a name="2---add-a-role-assignment"></a>2 - ロールの割り当てを追加する

このステップでは、お客様の Azure Cognitive Search サービスに、Cosmos DB データベースからデータを読み取るためのアクセス許可を付与します。

1. Azure portal で、インデックスを作成するデータが含まれている Cosmos DB アカウントに移動します。
2. **[アクセス制御 (IAM)]** を選択します
3. **[追加]** 、 **[ロールの割り当ての追加]** の順に選択します

    ![ロールの割り当てを追加する](./media/search-managed-identities/add-role-assignment-cosmos-db.png "ロールの割り当ての追加")

4. **[Cosmos DB アカウントの閲覧者ロール]** を選択します
5. **[アクセスの割り当て先]** を **[Azure AD のユーザー、グループ、サービス プリンシパル]** のままにしておきます
6. 検索サービスを検索し、それを選んでから、 **[保存]** を選択します

    ![閲覧者とデータ アクセスのロールの割り当てを追加する](./media/search-managed-identities/add-role-assignment-cosmos-db-account-reader-role.png "閲覧者とデータ アクセスのロールの割り当てを追加する")

### <a name="3---create-the-data-source"></a>3 - データ ソースを作成する

[REST API](/rest/api/searchservice/create-data-source)、Azure portal、および [.NET SDK](/dotnet/api/azure.search.documents.indexes.models.searchindexerdatasourcetype) では、マネージド ID 接続文字列がサポートされています。 次に、[REST API](/rest/api/searchservice/create-data-source) とマネージド ID 接続文字列を使用して Cosmos DB のデータにインデックスを付けるためのデータ ソースを作成する方法例を示します。 マネージド ID 接続文字列の形式は、REST API、.NET SDK、および Azure portal において同じです。

マネージド ID を使用して認証する場合、**資格情報** にはアカウント キーは含まれません。

```
POST https://[service name].search.windows.net/datasources?api-version=2020-06-30
Content-Type: application/json
api-key: [Search service admin key]

{
    "name": "cosmos-db-datasource",
    "type": "cosmosdb",
    "credentials": {
        "connectionString": "Database=sql-test-db;ResourceId=/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/cosmos-db-resource-group/providers/Microsoft.DocumentDB/databaseAccounts/my-cosmos-db-account/;"
    },
    "container": { "name": "myCollection", "query": null },
    "dataChangeDetectionPolicy": {
        "@odata.type": "#Microsoft.Azure.Search.HighWaterMarkChangeDetectionPolicy",
        "highWaterMarkColumnName": "_ts"
    }
}
```    

要求の本文には、次のフィールドを含むデータ ソースの定義が含まれている必要があります。

| フィールド   | 説明 |
|---------|-------------|
| **name** | 必須。 データ ソース オブジェクトを表す名前を選択します。 |
|**type**| 必須。 `cosmosdb`である必要があります。 |
|**credentials** | 必須。 <br/><br/>マネージド ID を使用して接続する場合、**credentials** の形式は次のようになります。*Database=[database-name];ResourceId=[resource-id-string];(ApiKind=[api-kind];)*<br/> <br/>ResourceId の形式:*ResourceId=/subscriptions/**your subscription ID**/resourceGroups/**your resource group name**/providers/Microsoft.DocumentDB/databaseAccounts/**your cosmos db account name**/;*<br/><br/>SQL コレクションの場合は、接続文字列に ApiKind は必要ありません。<br/><br/>MongoDB コレクションの場合は、**ApiKind=MongoDb** を接続文字列に追加します。 <br/><br/>Gremlin グラフと Cassandra テーブルの場合には、[インデクサーの限定プレビュー](https://aka.ms/azure-cognitive-search/indexer-preview)にサインアップして、プレビュー機能に対するアクセス権と、資格情報の形式に関する情報を入手してください。<br/>|
| **container** | 次の要素が含まれます。 <br/>**name**:必須。 インデックスを作成するデータベース コレクションの ID を指定します。<br/>**query**: 省略可能。 任意の JSON ドキュメントを、Azure Cognitive Search がインデックスを作成できるフラット スキーマにフラット化するクエリを指定できます。<br/>MongoDB API、Gremlin API、Cassandra API では現在、クエリがサポートされていません。 |
| **dataChangeDetectionPolicy** | 推奨 |
|**dataDeletionDetectionPolicy** | 省略可能 |

### <a name="4---create-the-index"></a>4 - インデックスを作成する

インデックスでは、検索に使用する、ドキュメント内のフィールド、属性、およびその他の構成要素を指定します。

検索可能な `booktitle` フィールドを使用してインデックスを作成する方法を次に示します。

```
POST https://[service name].search.windows.net/indexes?api-version=2020-06-30
Content-Type: application/json
api-key: [admin key]

{
    "name" : "my-target-index",
    "fields": [
    { "name": "id", "type": "Edm.String", "key": true, "searchable": false },
    { "name": "booktitle", "type": "Edm.String", "searchable": true, "filterable": false, "sortable": false, "facetable": false }
    ]
}
```

インデックスの作成の詳細については、[インデックスの作成](/rest/api/searchservice/create-index)に関する記事をご覧ください。

### <a name="5---create-the-indexer"></a>5 - インデクサーを作成する

インデクサーはデータ ソースをターゲットの検索インデックスに接続し、データ更新を自動化するスケジュールを提供します。

インデックスとデータ ソースを作成したら、インデクサーを作成できます。

インデクサー定義の例:

```http
    POST https://[service name].search.windows.net/indexers?api-version=2020-06-30
    Content-Type: application/json
    api-key: [admin key]

    {
      "name" : "cosmos-db-indexer",
      "dataSourceName" : "cosmos-db-datasource",
      "targetIndexName" : "my-target-index",
      "schedule" : { "interval" : "PT2H" }
    }
```

このインデクサーは 2 時間ごとに実行されます (スケジュールの間隔が "PT2H" に設定されています)。 インデクサーを 30 分ごとに実行するには、間隔を "PT30M" に設定します。 サポートされている最短の間隔は 5 分です。 スケジュールは省略可能です。省略した場合、インデクサーは作成時に一度だけ実行されます。 ただし、いつでもオンデマンドでインデクサーを実行できます。   

インデクサー作成 API の詳細については、「 [インデクサーの作成](/rest/api/searchservice/create-indexer)」をご覧ください。

インデクサーのスケジュールの定義の詳細については、[Azure Cognitive Search のインデクサーのスケジュールを設定する方法](search-howto-schedule-indexers.md)に関する記事を参照してください。

## <a name="troubleshooting"></a>トラブルシューティング

Cosmos DB からのデータにインデックスを付けることができない場合は、以下の点について検討してください。

1. Cosmos DB のアカウント キーを最近ローテーションした場合は、マネージド ID の接続文字列が機能するまでに最大で 15 分間待つ必要があります。

1. Cosmos DB アカウントで、特定のネットワークへのアクセスが制限されているかどうかを確認します。 そうされている場合は、[Azure ネットワーク セキュリティ機能を使用したデータ ソースへのインデクサーのアクセス](search-indexer-securing-resources.md)に関する記事を参照してください。

## <a name="next-steps"></a>次の手順

* [Azure Cosmos DB インデクサー](search-howto-index-cosmosdb.md)