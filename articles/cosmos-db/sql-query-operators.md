---
title: Azure Cosmos DB の SQL クエリ演算子
description: Azure Cosmos DB でサポートされている等値演算子、比較演算子、論理演算子などの SQL 演算子について説明します。
author: timsander1
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 07/29/2020
ms.author: tisande
ms.openlocfilehash: c1409bd7f098c24efbb4196d78c6dffb6048119b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "93335443"
---
# <a name="operators-in-azure-cosmos-db"></a>Azure Cosmos DB の演算子
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

この記事では、Azure Cosmos DB でサポートされているさまざまな演算子について詳しく説明します。

## <a name="equality-and-comparison-operators"></a>等値演算子と比較演算子

以下の表は、SQL API の 2 つの JSON 型で等値比較を実行した結果を示しています。

| **演算子** | **Undefined** | **Null** | **Boolean** | **Number** | **String** | **Object** | **Array** |
|---|---|---|---|---|---|---|---|
| **Undefined** | 未定義。 | 未定義。 | 未定義。 | 未定義。 | 未定義。 | 未定義。 | 未定義。 |
| **Null** | 未定義。 | **Ok** | 未定義。 | 未定義。 | 未定義。 | 未定義。 | 未定義。 |
| **Boolean** | 未定義。 | 未定義。 | **Ok** | 未定義。 | 未定義。 | 未定義。 | 未定義。 |
| **Number** | 未定義。 | 未定義。 | 未定義。 | **Ok** | 未定義。 | 未定義。 | 未定義。 |
| **String** | 未定義。 | 未定義。 | 未定義。 | 未定義。 | **Ok** | 未定義。 | 未定義。 |
| **Object** | 未定義。 | 未定義。 | 未定義。 | 未定義。 | 未定義。 | **Ok** | 未定義。 |
| **Array** | 未定義。 | 未定義。 | 未定義。 | 未定義。 | 未定義。 | 未定義。 | **Ok** |

`>`、`>=`、`!=`、`<`、および `<=` などの比較演算子では、種類全体または 2 つのオブジェクトや配列間の比較で、`Undefined` が生成されます。  

スカラー式の結果が `Undefined` である場合、`Undefined` は `true` に等しくないため、項目が結果に含まれません。

たとえば、次のクエリの数値と文字列値の比較では、`Undefined` が生成されます。 したがって、フィルターには結果が含まれません。

```sql
SELECT *
FROM c
WHERE 7 = 'a'
```

## <a name="logical-and-or-and-not-operators"></a>論理 (AND、OR、および NOT) 演算子

論理演算子は Boolean 値に対して使用されます。 次の表に、これらの演算子の真理値表を示します。

**OR 演算子**

いずれかの条件が `true` の場合に `true` を返します。

|  | **True** | **False** | **Undefined** |
| --- | --- | --- | --- |
| **True** |True |True |True |
| **False** |True |False |未定義。 |
| **Undefined** |True |未定義 |未定義 |

**AND 演算子**

両方の式が `true` の場合に `true` を返します。

|  | **True** | **False** | **Undefined** |
| --- | --- | --- | --- |
| **True** |True |False |未定義。 |
| **False** |False |False |False |
| **Undefined** |未定義。 |False |未定義。 |

**NOT 演算子**

任意のブール式の値を反転します。

|  | **NOT** |
| --- | --- |
| **True** |False |
| **False** |True |
| **Undefined** |未定義。 |

**演算子の優先順位**

論理演算子 `OR`、`AND`、`NOT` には、次に示す優先順位レベルがあります。

| **[オペレーター]** | **優先順位** |
| --- | --- |
| **NOT** |1 |
| **AND** |2 |
| **OR** |3 |

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

## <a name="next-steps"></a>次のステップ

- [Azure Cosmos DB .NET のサンプル](https://github.com/Azure/azure-cosmos-dotnet-v3)
- [キーワード](sql-query-keywords.md)
- [SELECT 句](sql-query-select.md)
