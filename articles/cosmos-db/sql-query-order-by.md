---
title: Azure Cosmos DB での ORDER BY 句
description: Azure Cosmos DB の SQL の ORDER BY 句について説明します。 Azure Cosmos DB JSON クエリ言語として SQL を使用します。
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 06/10/2019
ms.author: mjbrown
ms.openlocfilehash: d0a1ed33d5848c3ed8d5f83af8b320d77fe0dc65
ms.sourcegitcommit: a12b2c2599134e32a910921861d4805e21320159
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/24/2019
ms.locfileid: "67343165"
---
# <a name="order-by-clause"></a>ORDER BY 句

省略可能な ORDER BY 句では、クエリによって返される結果の並べ替え順序を指定します。

## <a name="syntax"></a>構文
  
```sql  
ORDER BY <sort_specification>  
<sort_specification> ::= <sort_expression> [, <sort_expression>]  
<sort_expression> ::= {<scalar_expression> [ASC | DESC]} [ ,...n ]  
```  

## <a name="arguments"></a>引数
  
- `<sort_specification>`  
  
   クエリの結果セットの並べ替えの条件にするプロパティまたは式を指定します。 並べ替え列は、名前またはプロパティの別名として指定できます。  
  
   複数のプロパティを指定することができます。 プロパティ名は一意である必要があります。 ORDER BY 句の並べ替えプロパティの順序は、並べ替えられた結果セットの構成を定義します。 つまり、結果セットは最初のプロパティで並べ替えられ、その並べ替えられたリストが 2 番目のプロパティで並べ替えられ、以下同様に続きます。  
  
   ORDER BY 句で参照されるプロパティ名は、選択リスト内のプロパティに、またはあいまいさのない FROM 句で指定されたコレクションで定義されているプロパティに対応する必要があります。  
  
- `<sort_expression>`  
  
   クエリの結果セットの並べ替えの条件にする 1 つ以上のプロパティまたは式を指定します。  
  
- `<scalar_expression>`  
  
   詳細については、「[スカラー式](sql-query-scalar-expressions.md)」セクションを参照してください。  
  
- `ASC | DESC`  
  
   指定された列の値を昇順または降順で並べ替える必要があることを指定します。 ASC は、最小値から最大値に並べ替えられます。 DESC は、最大値から最小値に並べ替えられます。 既定の並べ替え順は ASC です。 NULL 値は、有効な最小値として扱われます。  
  
## <a name="remarks"></a>解説  
  
   ORDER BY 句では、並べ替えるフィールドのインデックスがインデックス作成ポリシーに含まれている必要があります。 Azure Cosmos DB のクエリ ランタイムでは、計算されたプロパティに対してではなく、プロパティ名に対する並べ替えがサポートされています。 Azure Cosmos DB では、複数の ORDER BY プロパティがサポートされています。 複数の ORDER BY プロパティを使ったクエリを実行するには、並べ替えるフィールドに対する[複合インデックス](index-policy.md#composite-indexes)を定義する必要があります。

## <a name="examples"></a>例

たとえば、居住都市名の昇順で家族を取得するクエリは次のようになります。

```sql
    SELECT f.id, f.address.city
    FROM Families f
    ORDER BY f.address.city
```

結果は次のようになります。

```json
    [
      {
        "id": "WakefieldFamily",
        "city": "NY"
      },
      {
        "id": "AndersenFamily",
        "city": "Seattle"
      }
    ]
```

次のクエリは、家族 `id` をその項目の作成日の順序で取得します。 項目 `creationDate` は、*エポック時間*、つまり、1970 年 1 月 1 日からの経過時間を秒で表す数字です。

```sql
    SELECT f.id, f.creationDate
    FROM Families f
    ORDER BY f.creationDate DESC
```

結果は次のようになります。

```json
    [
      {
        "id": "WakefieldFamily",
        "creationDate": 1431620462
      },
      {
        "id": "AndersenFamily",
        "creationDate": 1431620472
      }
    ]
```

さらに、複数のプロパティで並べ替えることができます。 複数のプロパティで並べ替えるクエリには、[複合インデックス](index-policy.md#composite-indexes)が必要です。 次のクエリについて考えてみましょう。

```sql
    SELECT f.id, f.creationDate
    FROM Families f
    ORDER BY f.address.city ASC, f.creationDate DESC
```

このクエリでは、都市名の昇順で家族の `id` が取得されます。 複数の項目に同じ都市名がある場合、クエリが `creationDate` の降順で並べ替えます。

## <a name="next-steps"></a>次の手順

- [使用の開始](sql-query-getting-started.md)
- [SELECT 句](sql-query-select.md)
- [OFFSET LIMIT 句](sql-query-offset-limit.md)
