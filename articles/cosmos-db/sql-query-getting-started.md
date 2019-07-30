---
title: Azure Cosmos DB で SQL クエリの使用を開始する
description: SQL クエリの概要
author: timsander1
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 06/21/2019
ms.author: tisande
ms.openlocfilehash: 87b275806c06443e37e9e92c35a38b4cde378322
ms.sourcegitcommit: a12b2c2599134e32a910921861d4805e21320159
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/24/2019
ms.locfileid: "67343261"
---
# <a name="getting-started-with-sql-queries"></a>SQL クエリの使用を開始する

Azure Cosmos DB SQL API アカウントでは、JSON クエリ言語として SQL (構造化照会言語) を使用する項目のクエリがサポートされています。 Azure Cosmos DB クエリ言語の設計目標は次のとおりです。

* 新しいクエリ言語を開発する代わりに、最もよく知られていて人気のあるクエリ言語の 1 つである SQL をサポートしています。 SQL は、JSON 項目に対するリッチ クエリ用の正式なプログラミング モデルを提供します。  

* クエリ言語の基盤として JavaScript のプログラミング モデルを使用します。 JavaScript の型システム、式評価、関数呼び出しは、SQL API の基盤です。 これらの基盤によって、リレーショナル プロジェクション、JSON 項目全体の階層型ナビゲーション、自己結合、空間クエリ、完全に JavaScript で記述されたユーザー定義関数 (UDF) の呼び出しなどの機能に対して、自然なプログラミング モデルが提供されます。

## <a name="upload-sample-data"></a>サンプル データのアップロード

SQL API Cosmos DB アカウントで、`Families` というコンテナーを作成します。 コンテナー内に 2 つの簡単な JSON 項目を作成します。 このデータ セットを使用して、Azure Cosmos DB クエリのドキュメントにあるほとんどのサンプル クエリを実行できます。

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

* Cosmos DB コンテナーは、スキーマがない JSON 項目のコレクションです。 コンテナー項目内および項目全体の関係は、含有関係によって暗黙的にキャプチャされ、主キーと外部キーの関係ではキャプチャされません。 この機能は、この記事で後述する項目間結合に重要です。

## <a name="next-steps"></a>次の手順

- [Azure Cosmos DB の概要](introduction.md)
- [Azure Cosmos DB .NET のサンプル](https://github.com/Azure/azure-cosmosdb-dotnet)
- [SELECT 句](sql-query-select.md)
