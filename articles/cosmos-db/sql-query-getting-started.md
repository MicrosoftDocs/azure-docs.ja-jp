---
title: Azure Cosmos DB で SQL クエリの使用を開始する
description: SQL クエリを使用して Azure Cosmos DB からデータのクエリを実行する方法について説明します。 Azure Cosmos DB のコンテナーにサンプル データをアップロードしてクエリを実行できます。
author: timsander1
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 07/24/2020
ms.author: tisande
ms.openlocfilehash: d292b7cfcda73cb4cd6ac2535c7e27fc675e1030
ms.sourcegitcommit: a76ff927bd57d2fcc122fa36f7cb21eb22154cfa
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/28/2020
ms.locfileid: "87308187"
---
# <a name="getting-started-with-sql-queries"></a>SQL クエリの使用を開始する

Azure Cosmos DB SQL API アカウントでは、次の 2 つの方法でデータを読み取ることができます。

**ポイントの読み取り** - 1 つの*項目 ID* およびパーティション キーにおいてキーと値の参照を行うことができます。 *項目 ID* とパーティション キーの組み合わせがキーであり、項目自体は値です。 1 KB のドキュメントの場合、ポイントの読み取りには通常 1 個の[要求ユニット](request-units.md)が必要で、待機時間は 10 ミリ秒未満です。 ポイントの読み取りでは、1 つの項目が返されます。

**SQL クエリ** - 構造化照会言語 (SQL) (Structured Query Language) を JSON クエリ言語として使用してクエリを記述することで、データを照会できます。 照会には常に 2.3 個以上の要求ユニットが必要で、通常はポイントの読み取りよりも待機時間が長く、その変動も大きくなります。 クエリを使用すると、多くの項目を返すことができます。

Azure Cosmos DB の読み取り負荷の高いワークロードのほとんどは、ポイント読み取りと SQL クエリの両方を組み合わせて使用します。 1 つの項目のみを読み取る必要がある場合、クエリを使用するよりもポイントの読み取りを実行するほうが、少ないユニット数で高速に読み取ることができます。 ポイントの読み取りでは、クエリ エンジンを使用してデータにアクセスする必要がなく、データを直接読み取ることができます。 もちろん、すべてのワークロードでポイントの読み取りを使用してデータを排他的に読み取ることはできません。この場合、SQL をクエリ言語として使用し、[スキーマに依存しないインデックス設定](index-overview.md)を行うことで、データに柔軟にアクセスできます。

以下に、各 SDK でポイントの読み取りを実行する方法の例を示します。

- [.NET SDK](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmos.container.readitemasync?view=azure-dotnet)
- [Java SDK](https://docs.microsoft.com/java/api/com.azure.cosmos.cosmoscontainer.readitem?view=azure-java-stable#com_azure_cosmos_CosmosContainer__T_readItem_java_lang_String_com_azure_cosmos_models_PartitionKey_com_azure_cosmos_models_CosmosItemRequestOptions_java_lang_Class_T__)
- [Node.js SDK](https://docs.microsoft.com/javascript/api/@azure/cosmos/item?view=azure-node-latest#read-requestoptions-)
- [Python SDK](https://docs.microsoft.com/python/api/azure-cosmos/azure.cosmos.containerproxy?view=azure-python#read-item-item--partition-key--populate-query-metrics-none--post-trigger-include-none----kwargs-)

このドキュメントの残りの部分では、Azure Cosmos DB で SQL クエリの記述を開始する方法について説明します。 SQL クエリは、SDK または Azure portal を使用して実行できます。

## <a name="upload-sample-data"></a>サンプル データのアップロード

SQL API Cosmos DB アカウントで、`Families` というコンテナーを作成します。 コンテナー内に 2 つの簡単な JSON 項目を作成します。 このデータ セットを使用して、Azure Cosmos DB クエリのドキュメントにあるサンプル クエリのほとんどを実行できます。

### <a name="create-json-items"></a>JSON 項目を作成する

次のコードは、家族に関する 2 つの簡単な JSON 項目を作成します。 Andersen 一家と Wakefield 一家に関する簡単な JSON 項目に、両親、子どもと子どものペット、住所、登録に関する情報が記載されています。 最初の項目には、文字列、数値、ブール値、配列、入れ子になったプロパティがあります。

```json
{
  "id": "AndersenFamily",
  "lastName": "Andersen",
  "parents": [
     { "firstName": "Thomas" },
     { "firstName": "Mary Kay"}
  ],
  "children": [
     {
         "firstName": "Henriette Thaulow",
         "gender": "female",
         "grade": 5,
         "pets": [{ "givenName": "Fluffy" }]
     }
  ],
  "address": { "state": "WA", "county": "King", "city": "Seattle" },
  "creationDate": 1431620472,
  "isRegistered": true
}
```

2 つ目の項目は、`firstName` と `lastName` の代わりに `givenName` と `familyName` を使用します。

```json
{
  "id": "WakefieldFamily",
  "parents": [
      { "familyName": "Wakefield", "givenName": "Robin" },
      { "familyName": "Miller", "givenName": "Ben" }
  ],
  "children": [
      {
        "familyName": "Merriam",
        "givenName": "Jesse",
        "gender": "female",
        "grade": 1,
        "pets": [
            { "givenName": "Goofy" },
            { "givenName": "Shadow" }
        ]
      },
      {
        "familyName": "Miller",
         "givenName": "Lisa",
         "gender": "female",
         "grade": 8 }
  ],
  "address": { "state": "NY", "county": "Manhattan", "city": "NY" },
  "creationDate": 1431620462,
  "isRegistered": false
}
```

### <a name="query-the-json-items"></a>JSON 項目のクエリを実行する

Azure Cosmos DB の SQL クエリ言語の重要な側面について理解するため、JSON データに対していくつかのクエリを実行してみます。

以下のクエリを実行すると、`id` フィールドが `AndersenFamily` に一致する項目が返されます。 `SELECT *` クエリであるため、クエリの出力は完全な JSON 項目になります。 SELECT 構文の詳細については、[SELECT ステートメント](sql-query-select.md)に関するページを参照してください。

```sql
    SELECT *
    FROM Families f
    WHERE f.id = "AndersenFamily"
```

クエリ結果:

```json
    [{
        "id": "AndersenFamily",
        "lastName": "Andersen",
        "parents": [
           { "firstName": "Thomas" },
           { "firstName": "Mary Kay"}
        ],
        "children": [
           {
               "firstName": "Henriette Thaulow", "gender": "female", "grade": 5,
               "pets": [{ "givenName": "Fluffy" }]
           }
        ],
        "address": { "state": "WA", "county": "King", "city": "Seattle" },
        "creationDate": 1431620472,
        "isRegistered": true
    }]
```

次のクエリは、JSON 出力を異なる形式に変更します。 このクエリは、住所の都市が州と同じ場合に、2 つの選択したフィールド (`Name` と `City`) を持つ JSON `Family` オブジェクトをプロジェクションします。 "NY, NY" はこのケースに一致します。

```sql
    SELECT {"Name":f.id, "City":f.address.city} AS Family
    FROM Families f
    WHERE f.address.city = f.address.state
```

クエリ結果:

```json
    [{
        "Family": {
            "Name": "WakefieldFamily",
            "City": "NY"
        }
    }]
```

次のクエリでは、`id` が `WakefieldFamily` と一致する家族の子どもの名がすべて都市順に返されます。

```sql
    SELECT c.givenName
    FROM Families f
    JOIN c IN f.children
    WHERE f.id = 'WakefieldFamily'
    ORDER BY f.address.city ASC
```

結果は次のようになります。

```json
    [
      { "givenName": "Jesse" },
      { "givenName": "Lisa"}
    ]
```

## <a name="remarks"></a>解説

上記の例は、Cosmos DB クエリ言語のいくつかの側面を示しています。  

* SQL API は JSON 値に対して機能するので、行と列ではなくツリー形式のエンティティを処理します。 `Node1.Node2.Node3…..Nodem` のように任意の深さのツリーのノードを参照でき、ANSI SQL での `<table>.<column>` の 2 項目参照に似ています。

* クエリ言語はスキーマレス データを操作するため、型システムを動的にバインドする必要があります。 同じ式でも、項目が異なれば異なる型が導出される場合があります。 クエリ結果は有効な JSON 値ですが、固定スキーマの場合でも有効とは限りません。  

* Azure Cosmos DB は厳密な JSON 項目だけをサポートします。 型システムおよび式は、JSON 型のみを扱うように制限されます。 詳細については、[JSON の仕様](https://www.json.org/)に関する記事を参照してください。  

* Cosmos コンテナーは、JSON 項目のスキーマなしのコレクションです。 コンテナー項目内および項目全体の関係は、含有関係によって暗黙的にキャプチャされ、主キーと外部キーの関係ではキャプチャされません。 この機能は、この記事で後述する項目間結合に重要です。

## <a name="next-steps"></a>次のステップ

- [Azure Cosmos DB の概要](introduction.md)
- [Azure Cosmos DB .NET のサンプル](https://github.com/Azure/azure-cosmos-dotnet-v3)
- [SELECT 句](sql-query-select.md)
