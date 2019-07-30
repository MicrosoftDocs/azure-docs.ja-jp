---
title: Azure Cosmos DB の SQL クエリ演算子
description: Azure Cosmos DB の SQL 演算子について学習します。
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/31/2019
ms.author: mjbrown
ms.openlocfilehash: eecc1522f8c260286c7dd7fc4c2e58d5d8caa8fb
ms.sourcegitcommit: a12b2c2599134e32a910921861d4805e21320159
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/24/2019
ms.locfileid: "67343259"
---
# <a name="operators-in-azure-cosmos-db"></a>Azure Cosmos DB の演算子

この記事では、Azure Cosmos DB でサポートされているさまざまな演算子について詳しく説明します。

## <a name="equality-and-comparison-operators"></a>等値演算子と比較演算子

以下の表は、SQL API の 2 つの JSON 型で等値比較を実行した結果を示しています。

| **演算子** | **Undefined** | **Null** | **Boolean** | **Number** | **文字列** | **Object** | **Array** |
|---|---|---|---|---|---|---|---|
| **Undefined** | Undefined | Undefined | Undefined | Undefined | Undefined | Undefined | Undefined |
| **Null** | Undefined | **Ok** | Undefined | Undefined | Undefined | Undefined | Undefined |
| **Boolean** | Undefined | Undefined | **Ok** | Undefined | Undefined | Undefined | Undefined |
| **Number** | Undefined | Undefined | Undefined | **Ok** | Undefined | Undefined | Undefined |
| **文字列** | Undefined | Undefined | Undefined | Undefined | **Ok** | Undefined | Undefined |
| **Object** | Undefined | Undefined | Undefined | Undefined | Undefined | **Ok** | Undefined |
| **Array** | Undefined | Undefined | Undefined | Undefined | Undefined | Undefined | **Ok** |

`>`、`>=`、`!=`、`<`、および `<=` などの比較演算子では、種類全体または 2 つのオブジェクトや配列間の比較で、`Undefined` が生成されます。  

スカラー式の結果が `Undefined` である場合、`Undefined` は `true` に等しくないため、項目が結果に含まれません。

## <a name="logical-and-or-and-not-operators"></a>論理 (AND、OR、および NOT) 演算子

論理演算子は Boolean 値に対して使用されます。 次の表に、これらの演算子の真理値表を示します。

**OR 演算子**

| または | True | False | Undefined |
| --- | --- | --- | --- |
| True |True |True |True |
| False |True |False |Undefined |
| Undefined |True |Undefined |Undefined |

**AND 演算子**

| AND | True | False | Undefined |
| --- | --- | --- | --- |
| True |True |False |Undefined |
| False |False |False |False |
| Undefined |Undefined |False |Undefined |

**NOT 演算子**

| NOT |  |
| --- | --- |
| True |False |
| False |True |
| Undefined |Undefined |


## <a name="-operator"></a>* 演算子

特別な演算子 * によって、項目全体が現状のままプロジェクションされます。 使用する場合は、この演算子が唯一のプロジェクションされるフィールドである必要があります。 `SELECT * FROM Families f` のようなクエリは有効ですが、`SELECT VALUE * FROM Families f` および `SELECT *, f.id FROM Families f` は無効です。

## <a name="-and--operators"></a>? および ?? 演算子

3 項 (?) 演算子と合体 (??) 演算子は、C# や JavaScript などのプログラミング言語の場合と同様に、条件式の構築に使用することができます。 

? 演算子を 使用して、実行中に新しい JSON プロパティを構築できます。 たとえば、次のクエリは、学年を `elementary` または `other` に分類します。

```sql
     SELECT (c.grade < 5)? "elementary": "other" AS gradeLevel
     FROM Families.children[0] c
```

また、? 演算子の呼び出しを 次のクエリのように入れ子にすることもできます。 

```sql
    SELECT (c.grade < 5)? "elementary": ((c.grade < 9)? "junior": "high") AS gradeLevel
    FROM Families.children[0] c
```

他のクエリ演算子と同様に、? 演算子は 参照先のプロパティが見つからないか、比較対象の型が異なる場合、項目を除外します。

?? 演算子を 使用すると、半構造化されたデータや混合型のデータに対してクエリを実行するときに、項目内のプロパティが効率的にチェックされます。 たとえば、次のクエリは、`lastName` が存在する場合にそれを返し、`lastName` が存在しない場合は `surname` を返します。

```sql
    SELECT f.lastName ?? f.surname AS familyName
    FROM Families f
```

## <a name="next-steps"></a>次の手順

- [Azure Cosmos DB .NET のサンプル](https://github.com/Azure/azure-cosmosdb-dotnet)
- [キーワード](sql-query-keywords.md)
- [SELECT 句](sql-query-select.md)
