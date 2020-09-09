---
title: Azure Cosmos DB での SELECT 句
description: Azure Cosmos DB の SQL SELECT 句について説明します。 Azure Cosmos DB JSON クエリ言語として SQL を使用します。
author: timsander1
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/08/2020
ms.author: tisande
ms.openlocfilehash: f33cf20b76655a893fe7eebd9e6e6569d35de98f
ms.sourcegitcommit: ac4a365a6c6ffa6b6a5fbca1b8f17fde87b4c05e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/10/2020
ms.locfileid: "83005948"
---
# <a name="select-clause-in-azure-cosmos-db"></a>Azure Cosmos DB での SELECT 句

すべてのクエリは ANSI SQL 標準に従って `SELECT` 句とオプションの [FROM](sql-query-from.md) および [WHERE](sql-query-where.md) 句で構成されます。 通常、`FROM` 句のソースが列挙され、JSON 項目のサブセットを取得するためにそのソースに `WHERE` 句のフィルターが適用されます。 `SELECT` 句は、要求された JSON 値を選択リストにプロジェクションします。

## <a name="syntax"></a>構文

```sql
SELECT <select_specification>  

<select_specification> ::=
      '*'
      | [DISTINCT] <object_property_list>
      | [DISTINCT] VALUE <scalar_expression> [[ AS ] value_alias]  
  
<object_property_list> ::=
{ <scalar_expression> [ [ AS ] property_alias ] } [ ,...n ]  
```  
  
## <a name="arguments"></a>引数
  
- `<select_specification>`  

  結果セットで選択されるプロパティまたは値。  
  
- `'*'`  

  変更を加えずに値を取得することを指定します。 具体的には、処理された値がオブジェクトである場合、すべてのプロパティが取得されます。  
  
- `<object_property_list>`  
  
  取得するプロパティの一覧を指定します。 返される各値は、指定されたプロパティを持つオブジェクトとなります。  
  
- `VALUE`  

  完全な JSON オブジェクトではなく JSON 値を取得することを指定します。 これは、`<property_list>` とは異なり、オブジェクト内の投影された値をラップしません。  

- `DISTINCT`
  
  投影されたプロパティの重複を削除するよう指定します。  

- `<scalar_expression>`  

  計算する値を表す式。 詳細については、「[スカラー式](sql-query-scalar-expressions.md)」セクションを参照してください。  

## <a name="remarks"></a>解説

`SELECT *` 構文は、FROM 句で正確に 1 つの別名が宣言された場合のみ有効です。 `SELECT *` は、プロジェクションが不要な場合に便利な、ID プロジェクションを提供します。 SELECT * は、FROM 句が指定され、1 つの入力ソースのみが導入されている場合にのみ有効です。  
  
`SELECT <select_list>` と `SELECT *` は両方とも「糖衣構文」であり、次に示すように単純な SELECT ステートメントを使用して、別の方法で表すことができます。  
  
1. `SELECT * FROM ... AS from_alias ...`  
  
   は以下に匹敵します。  
  
   `SELECT from_alias FROM ... AS from_alias ...`  
  
2. `SELECT <expr1> AS p1, <expr2> AS p2,..., <exprN> AS pN [other clauses...]`  
  
   は以下に匹敵します。  
  
   `SELECT VALUE { p1: <expr1>, p2: <expr2>, ..., pN: <exprN> }[other clauses...]`  
  
## <a name="examples"></a>例

次の SELECT クエリの例は、`id` が `AndersenFamily` と一致する `Families` から `address` を返します。

```sql
    SELECT f.address
    FROM Families f
    WHERE f.id = "AndersenFamily"
```

結果は次のようになります。

```json
    [{
      "address": {
        "state": "WA",
        "county": "King",
        "city": "Seattle"
      }
    }]
```

## <a name="next-steps"></a>次のステップ

- [作業の開始](sql-query-getting-started.md)
- [Azure Cosmos DB .NET のサンプル](https://github.com/Azure/azure-cosmos-dotnet-v3)
- [WHERE 句](sql-query-where.md)
