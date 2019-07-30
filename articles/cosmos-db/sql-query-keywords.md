---
title: Azure Cosmos DB の SQL キーワード
description: Azure Cosmos DB の SQL キーワードについて説明します。
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 06/20/2019
ms.author: mjbrown
ms.openlocfilehash: c9024f120e0a55162a1f6dba0cd9cbda97f5eebc
ms.sourcegitcommit: a12b2c2599134e32a910921861d4805e21320159
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/24/2019
ms.locfileid: "67343211"
---
# <a name="keywords-in-azure-cosmos-db"></a>Azure Cosmos DB でのキーワード
この記事では、Azure Cosmos DB の SQL クエリで使用できるキーワードについて説明します。

## <a name="between"></a>BETWEEN

ANSI SQL の場合と同様に、BETWEEN キーワードを使用して、文字列値や数値の範囲に対してクエリを表現することができます。 たとえば、次のクエリでは、最初の子の学年が 1 以上 5 以下であるすべての項目が返されます。

```sql
    SELECT *
    FROM Families.children[0] c
    WHERE c.grade BETWEEN 1 AND 5
```

ANSI SQL の場合と異なり、次の例のように、FROM 句内に BETWEEN 句を使用することもできます。

```sql
    SELECT (c.grade BETWEEN 0 AND 10)
    FROM Families.children[0] c
```

SQL API では、ANSI SQL と異なり、混合型のプロパティに対して範囲クエリを表すことができます。 たとえば、一部の項目の `grade` が `5` などの数値で、他の項目が `grade4` などの文字列である場合があります。 このような場合、JavaScript の場合と同様に、2 つの異なる種類を比較した結果は `Undefined` になるため、項目がスキップされます。

> [!TIP]
> クエリの実行速度を速めるには、BETWEEN 句でフィルター処理される数値プロパティやパスに対して範囲のインデックス型を使用するインデックス作成ポリシーを作成します。

## <a name="distinct"></a>DISTINCT

DISTINCT キーワードは、クエリのプロジェクションでの重複を除去します。

```sql
SELECT DISTINCT VALUE f.lastName
FROM Families f
```

この例では、クエリは各姓の値をプロジェクションしています。

結果は次のようになります。

```json
[
    "Andersen"
]
```

一意のオブジェクトをプロジェクションすることもできます。 この場合、lastName フィールドが 2 つのドキュメントのいずれかに存在しないため、クエリが空のオブジェクトを返します。

```sql
SELECT DISTINCT f.lastName
FROM Families f
```

結果は次のようになります。

```json
[
    {
        "lastName": "Andersen"
    },
    {}
]
```

DISTINCT は、サブクエリ内のプロジェクションでも使用できます。

```sql
SELECT f.id, ARRAY(SELECT DISTINCT VALUE c.givenName FROM c IN f.children) as ChildNames
FROM f
```

このクエリは、重複が削除された各子の givenName を格納する配列をプロジェクションします。 この配列は、ChildNames という別名が付けられ、外側のクエリでプロジェクションされます。

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
## <a name="in"></a> IN

IN キーワードは、指定した値がリスト内のいずれかの値と一致するかどうかをチェックするために使用します。 たとえば以下のクエリでは、`id` が `WakefieldFamily` または `AndersenFamily` であるすべての家族の項目が返されます。

```sql
    SELECT *
    FROM Families
    WHERE Families.id IN ('AndersenFamily', 'WakefieldFamily')
```

次の例では、状態が指定された値のいずれかであるすべての項目が返されます。

```sql
    SELECT *
    FROM Families
    WHERE Families.address.state IN ("NY", "WA", "CA", "PA", "OH", "OR", "MI", "WI", "MN", "FL")
```

SQL API では、FROM ソースの IN キーワードで追加される新しいコンストラクトによって、[JSON 配列に対する反復](sql-query-object-array.md#Iteration)がサポートされています。 

## <a name="top"></a>TOP

TOP キーワードは、任意の順序で最初の `N` 個のクエリ結果を返します。 ベスト プラクティスとして、ORDER BY 句で TOP を使用して、最初の `N` 個の順序付けされた値に結果を制限します。 これらの 2 つの句を組み合わせることが、TOP の影響を受ける行を予想どおりに指定する唯一の方法です。

TOP は、次の例のように定数で、またはパラメーター化されたクエリを使用した変数値で使用できます。

```sql
    SELECT TOP 1 *
    FROM Families f
```

結果は次のようになります。

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

## <a name="next-steps"></a>次の手順

- [使用の開始](sql-query-getting-started.md)
- [結合](sql-query-join.md)
- [サブクエリ](sql-query-subquery.md)