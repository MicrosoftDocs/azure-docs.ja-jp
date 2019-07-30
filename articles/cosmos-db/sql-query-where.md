---
title: Azure Cosmos DB での WHERE 句
description: Azure Cosmos DB の SQL WHERE 句について説明します
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 06/10/2019
ms.author: mjbrown
ms.openlocfilehash: 6a942e48ffea7785fe971cc2f8fa66e8569ed672
ms.sourcegitcommit: a12b2c2599134e32a910921861d4805e21320159
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/24/2019
ms.locfileid: "67343227"
---
# <a name="where-clause"></a>WHERE 句

オプションの WHERE 句 (`WHERE <filter_condition>`) は、ソース JSON 項目が、クエリでそれらを結果に含めるために、満たす必要がある条件を指定します。 結果の対象となるには、指定された条件を JSON 項目が `true` と評価する必要があります。 インデックス レイヤーは、WHERE 句を使用して、結果に含めることが可能なソース項目の最小のサブセットを判断します。
  
## <a name="syntax"></a>構文
  
```sql  
WHERE <filter_condition>  
<filter_condition> ::= <scalar_expression>  
  
```  
  
## <a name="arguments"></a>引数

- `<filter_condition>`  
  
   返されるドキュメントが満たす条件を指定します。  
  
- `<scalar_expression>`  
  
   計算する値を表す式。 詳細については、[スカラー式](sql-query-scalar-expressions.md)に関するページを参照してください。  
  

## <a name="remarks"></a>解説
  
  ドキュメントが返されるには、フィルター条件として指定された式が true に評価される必要があります。 ブール値 true のみがその条件を満たし、他の値 (undefined、null、false、数字、配列、またはオブジェクト) は条件を満たしません。 

## <a name="examples"></a>例

以下のクエリでは、`id` プロパティを含み、その値が `AndersenFamily` であるような項目を要求します。 `id` プロパティを備えていない、またはその値が `AndersenFamily` に一致しない項目はすべて除外されます。

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

### <a name="scalar-expressions-in-the-where-clause"></a>WHERE 句内のスカラー式

上記の例では単純な等値クエリを紹介しました。 SQL API はさまざまな[スカラー式](sql-query-scalar-expressions.md)もサポートしています。 最も多く使用されるのはバイナリ式と単項式です。 ソース JSON オブジェクトからのプロパティ参照も有効な式です。

次のサポートされているバイナリ演算子を使用できます。  

|**演算子の種類**  | **値** |
|---------|---------|
|算術 | +、-、*、/、% |
|ビット    | \|、&、^、<<、>>、>>> (0 埋め右シフト) |
|論理    | AND、OR、NOT      |
|比較 | =, !=, &lt;, &gt;, &lt;=, &gt;=, <> |
|string     |  \|\| (連結) |

次のクエリでは、2 項演算子を使用しています。

```sql
    SELECT *
    FROM Families.children[0] c
    WHERE c.grade % 2 = 1     -- matching grades == 5, 1

    SELECT *
    FROM Families.children[0] c
    WHERE c.grade ^ 4 = 1    -- matching grades == 5

    SELECT *
    FROM Families.children[0] c
    WHERE c.grade >= 5    -- matching grades == 5
```

次の例のように、クエリでは、単項演算子 +、-、~、および NOT も使用できます。

```sql
    SELECT *
    FROM Families.children[0] c
    WHERE NOT(c.grade = 5)  -- matching grades == 1

    SELECT *
    FROM Families.children[0] c
    WHERE (-c.grade = -5)  -- matching grades == 5
```

クエリでプロパティ参照を使用することもできます。 たとえば、`SELECT * FROM Families f WHERE f.isRegistered` は、値が `true` 値と等しい `isRegistered` プロパティを含む JSON 項目を返します。 `false`、`null`、`Undefined`、`<number>`、`<string>`、`<object>`、または `<array>` などの他の値は、項目を結果から除外します。 

## <a name="next-steps"></a>次の手順

- [使用の開始](sql-query-getting-started.md)
- [Azure Cosmos DB .NET のサンプル](https://github.com/Azure/azure-cosmosdb-dotnet)
- [FROM 句](sql-query-from.md)