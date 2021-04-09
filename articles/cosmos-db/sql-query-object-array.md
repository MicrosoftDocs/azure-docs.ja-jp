---
title: Azure Cosmos DB での配列とオブジェクトの操作
description: Azure Cosmos DB で配列とオブジェクトを作成するための SQL 構文について説明します。 またこの記事では、配列オブジェクトに対して操作を実行するいくつかの例も示します
author: timsander1
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 02/02/2021
ms.author: tisande
ms.openlocfilehash: 1dccb8e51fbc578f8f218fe1582f95f7bcaf42d7
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "99493789"
---
# <a name="working-with-arrays-and-objects-in-azure-cosmos-db"></a>Azure Cosmos DB での配列とオブジェクトの操作
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

Azure Cosmos DB SQL API の重要な機能は、配列とオブジェクトの作成です。 このドキュメントでは、[Family データセット](sql-query-getting-started.md#upload-sample-data)を使用して再作成できる例を使用します。

このデータセットの項目の例を次に示します。

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

## <a name="arrays"></a>配列

次の例に示されているように配列を作成できます。

```sql
SELECT [f.address.city, f.address.state] AS CityState
FROM Families f
```

結果は次のようになります。

```json
[
  {
    "CityState": [
      "Seattle",
      "WA"
    ]
  },
  {
    "CityState": [
      "NY", 
      "NY"
    ]
  }
]
```

[ARRAY 式](sql-query-subquery.md#array-expression)を使用して、[サブクエリ](sql-query-subquery.md)の結果から配列を作成することもできます。 このクエリは、配列内の子の個別名をすべて取得します。

```sql
SELECT f.id, ARRAY(SELECT DISTINCT VALUE c.givenName FROM c IN f.children) as ChildNames
FROM f
```

結果は次のようになります。

```json
[
    {
        "id": "AndersenFamily",
        "ChildNames": []
    },
    {
        "id": "WakefieldFamily",
        "ChildNames": [
            "Jesse",
            "Lisa"
        ]
    }
]
```

## <a name="iteration"></a><a id="Iteration"></a>反復

SQL API では、FROM ソースの [IN キーワード](sql-query-keywords.md#in)によって、JSON 配列に対する反復がサポートされています。 次に例を示します。

```sql
SELECT *
FROM Families.children
```

結果は次のようになります。

```json
[
  [
    {
      "firstName": "Henriette Thaulow",
      "gender": "female",
      "grade": 5,
      "pets": [{ "givenName": "Fluffy"}]
    }
  ], 
  [
    {
        "familyName": "Merriam",
        "givenName": "Jesse",
        "gender": "female",
        "grade": 1
    }, 
    {
        "familyName": "Miller",
        "givenName": "Lisa",
        "gender": "female",
        "grade": 8
    }
  ]
]
```

次のクエリは、`Families`コンテナー内の `children` への反復を実行しています。 出力配列は、前のクエリと異なります。 この例では、 `children` を分割し、結果を 1 つの配列にフラット化しています。  

```sql
SELECT *
FROM c IN Families.children
```

結果は次のようになります。

```json
[
  {
      "firstName": "Henriette Thaulow",
      "gender": "female",
      "grade": 5,
      "pets": [{ "givenName": "Fluffy" }]
  },
  {
      "familyName": "Merriam",
      "givenName": "Jesse",
      "gender": "female",
      "grade": 1
  },
  {
      "familyName": "Miller",
      "givenName": "Lisa",
      "gender": "female",
      "grade": 8
  }
]
```

以下の例のように、配列の個々のエントリをさらにフィルターすることができます。

```sql
SELECT c.givenName
FROM c IN Families.children
WHERE c.grade = 8
```

結果は次のようになります。

```json
[{
  "givenName": "Lisa"
}]
```

配列の反復処理の結果に対して集計することもできます。 たとえば、次のクエリは、すべての家族の子どもの数を合計します。

```sql
SELECT COUNT(1) AS Count
FROM child IN Families.children
```

結果は次のようになります。

```json
[
  {
    "Count": 3
  }
]
```

> [!NOTE]
> IN キーワードを使用して反復処理を行う場合、配列の外部にあるプロパティをフィルター処理したり射影したりすることはできません。 代わりに、[JOIN](sql-query-join.md) を使用する必要があります。

その他の例については、[Azure Cosmos DB での配列の操作に関するブログ記事](https://devblogs.microsoft.com/cosmosdb/understanding-how-to-query-arrays-in-azure-cosmos-db/)を参照してください。

## <a name="next-steps"></a>次のステップ

- [作業の開始](sql-query-getting-started.md)
- [Azure Cosmos DB .NET のサンプル](https://github.com/Azure/azure-cosmos-dotnet-v3)
- [結合](sql-query-join.md)
