---
title: Azure Cosmos DB での JSON の使用
description: 入れ子になった JSON プロパティに対するクエリの実行とアクセス、および Azure Cosmos DB での特殊文字の使用について説明します。
author: timsander1
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/19/2020
ms.author: tisande
ms.openlocfilehash: a569b0122f9122b141b64ded21dbd9be1d766a41
ms.sourcegitcommit: 595cde417684e3672e36f09fd4691fb6aa739733
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/20/2020
ms.locfileid: "83699124"
---
# <a name="working-with-json-in-azure-cosmos-db"></a>Azure Cosmos DB での JSON の使用

Azure Cosmos DB の SQL (Core) API では、項目は JSON として保存されます。 型システムおよび式は、JSON 型のみを扱うように制限されます。 詳細については、[JSON の仕様](https://www.json.org/)に関する記事を参照してください。

JSON の使用で重要な点の一部を次にまとめます。

- JSON オブジェクトは、常に左中かっこ `{` で始まり、`}` 右中かっこで終わる
- JSON プロパティは、互いに[入れ子にする](#nested-properties)ことができる
- JSON プロパティ値には、配列を使用できる
- JSON プロパティ名では、大文字と小文字が区別される
- JSON プロパティ名には、任意の文字列値 (スペースや英文字ではない文字を含む) を使用できる

## <a name="nested-properties"></a>入れ子になったプロパティ

入れ子になった JSON には、ドット アクセサーを使用してアクセスできます。 入れ子になった JSON プロパティは、他のプロパティを使用する場合と同じ方法でクエリで使用できます。

入れ子になった JSON を使用したドキュメントを次に示します。

```JSON
{
  "id": "AndersenFamily",
  "lastName": "Andersen",
  "address": {
      "state": "WA",
      "county": "King",
      "city": "Seattle"
      },
  "creationDate": 1431620472,
  "isRegistered": true
}
```

この場合、`state`、`country`、`city` の各プロパティは、すべて `address` プロパティ内で入れ子になっています。

以下の例では、2 つの入れ子になったプロパティ `f.address.state` と `f.address.city` をプロジェクションしています。

```sql
    SELECT f.address.state, f.address.city
    FROM Families f
    WHERE f.id = "AndersenFamily"
```

結果は次のようになります。

```json
    [{
      "state": "WA",
      "city": "Seattle"
    }]
```

## <a name="working-with-arrays"></a>配列の操作

JSON では、入れ子になったプロパティに加えて、配列もサポートされています。

配列を使用したドキュメントの例を次に示します。

```json
{
  "id": "WakefieldFamily",
  "children": [
      {
        "familyName": "Merriam",
        "givenName": "Jesse",
        "gender": "female",
        "grade": 1,
      },
      {
        "familyName": "Miller",
         "givenName": "Lisa",
         "gender": "female",
         "grade": 8
      }
  ],
}
```

配列を操作する場合、配列内の特定の要素の位置を参照すれば、それにアクセスできます。

```sql
SELECT *
FROM Families f
WHERE f.children[0].givenName = "Jesse"
```

ただし、ほとんどの場合は、[サブクエリ](sql-query-subquery.md)または[自己結合](sql-query-join.md)を使用して配列を操作します。

例として、顧客の銀行口座の日次残高を示すドキュメントを次に示します。

```json
{
  "id": "Contoso-Checking-Account-2020",
  "balance": [
      {
        "checkingAccount": 1000,
        "savingsAccount": 5000
      },
      {
        "checkingAccount": 100,
        "savingsAccount": 5000
      },
      {
        "checkingAccount": -10,
        "savingsAccount": 5000,
      },
      {
        "checkingAccount": 5000,
        "savingsAccount": 5000,
      }
         ...
  ]
}
```

ある時点で残高が負の顧客をすべて表示するクエリを実行する場合は、サブクエリと共に [EXISTS](sql-query-subquery.md#exists-expression) を使用できます。

```sql
SELECT c.id
FROM c
WHERE EXISTS(
    SELECT VALUE n
    FROM n IN c.balance
    WHERE n.checkingAccount < 0
)
```

## <a name="reserved-keywords-and-special-characters-in-json"></a>JSON での予約キーワードと特殊文字

プロパティは、引用符で囲まれたプロパティの演算子 `[]` を使用してアクセスすることができます。 たとえば、 `SELECT c.grade` and `SELECT c["grade"]` は同等です。 この構文はスペース、特殊文字を含むプロパティや、SQL キーワードや予約語と同じ名前を持つプロパティをエスケープする場合に役立ちます。

たとえば、`order` という名前のプロパティと、特殊文字を含むプロパティ `price($)` を持つドキュメントを次に示します。

```json
{
  "id": "AndersenFamily",
  "order": {
         "orderId": "12345",
         "productId": "A17849",
         "price($)": 59.33
   },
  "creationDate": 1431620472,
  "isRegistered": true
}
```

`order` プロパティまたは `price($)` プロパティを含むクエリを実行すると、構文エラーが発生します。

```sql
SELECT * FROM c where c.order.orderid = "12345"
```

```sql
SELECT * FROM c where c.order.price($) > 50
```

結果は次のとおりです。

`
Syntax error, incorrect syntax near 'order'
`

同じクエリを次のように書き換える必要があります。

```sql
SELECT * FROM c WHERE c["order"].orderId = "12345"
```

```sql
SELECT * FROM c WHERE c["order"]["price($)"] > 50
```

## <a name="json-expressions"></a>JSON 式

プロジェクションは、以下の例のように JSON 式もサポートします。

```sql
    SELECT { "state": f.address.state, "city": f.address.city, "name": f.id }
    FROM Families f
    WHERE f.id = "AndersenFamily"
```

結果は次のようになります。

```json
    [{
      "$1": {
        "state": "WA",
        "city": "Seattle",
        "name": "AndersenFamily"
      }
    }]
```

前の例では、`SELECT` 句で JSON オブジェクトを作成する必要があり、サンプルはキーを提供していないため、句で暗黙的な引数変数名 `$1` を使用しています。 次のクエリは `$1` と `$2` の 2 つの暗黙的な引数の変数を返します。

```sql
    SELECT { "state": f.address.state, "city": f.address.city },
           { "name": f.id }
    FROM Families f
    WHERE f.id = "AndersenFamily"
```

結果は次のようになります。

```json
    [{
      "$1": {
        "state": "WA",
        "city": "Seattle"
      },
      "$2": {
        "name": "AndersenFamily"
      }
    }]
```

## <a name="aliasing"></a>エイリアス化

クエリ内の値を明示的にエイリアス化できます。 クエリに同じ名前を持つ 2 つのプロパティがある場合、エイリアス化を使ってプロパティのいずれかまたは両方の名前を変更することで、プロジェクションの結果でこれらを区別できます。

### <a name="examples"></a>例

2 つ目の値を `NameInfo` としてプロジェクションしている場合の次の例に示すように、エイリアス化に使用する `AS` キーワードはオプションです。

```sql
    SELECT
           { "state": f.address.state, "city": f.address.city } AS AddressInfo,
           { "name": f.id } NameInfo
    FROM Families f
    WHERE f.id = "AndersenFamily"
```

結果は次のようになります。

```json
    [{
      "AddressInfo": {
        "state": "WA",
        "city": "Seattle"
      },
      "NameInfo": {
        "name": "AndersenFamily"
      }
    }]
```

### <a name="aliasing-with-reserved-keywords-or-special-characters"></a>予約キーワードまたは特殊文字を使用したエイリアス化

スペース、特殊文字、または予約済みのワードが含まれるプロパティ名として値をプロジェクションするためにエイリアス化を使用することはできません。 値のプロジェクションを変更して、プロパティ名にスペースが含まれるようにする必要があるなどの場合は、[JSON 式](#json-expressions)を使用できます。

次に例を示します。

```sql
    SELECT
           {"JSON expression with a space": { "state": f.address.state, "city": f.address.city }},
           {"JSON expression with a special character!": { "name": f.id }}
    FROM Families f
    WHERE f.id = "AndersenFamily"
```

## <a name="next-steps"></a>次のステップ

- [作業の開始](sql-query-getting-started.md)
- [SELECT 句](sql-query-select.md)
- [WHERE 句](sql-query-where.md)
