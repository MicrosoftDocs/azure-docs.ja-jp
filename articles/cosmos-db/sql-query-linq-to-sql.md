---
title: Azure Cosmos DB での LINQ から SQL への変換
description: サポートされている LINQ 演算子と、Azure Cosmos DB の SQL クエリに LINQ クエリをマップする方法について説明します。
author: timsander1
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 11/11/2020
ms.author: tisande
ms.openlocfilehash: 122c95fe9ac017ad7a6957dcdb8323837be34f21
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "96545385"
---
# <a name="linq-to-sql-translation"></a>LINQ から SQL への変換
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

Azure Cosmos DB クエリ プロバイダーは、LINQ クエリから Cosmos DB SQL クエリへのマッピングをベスト エフォートで実行します。 LINQ から変換される SQL クエリを取得する場合、生成された `IQueryable` オブジェクトで `ToString()` メソッドを使用します。 以下の説明では、[LINQ](/dotnet/csharp/programming-guide/concepts/linq/introduction-to-linq-queries) の基本的知識を前提としています。 LINQ に加えて、Azure Cosmos DB では、SQL API と連携する [Entity Framework Core](/ef/core/providers/cosmos/?tabs=dotnet-core-cli) もサポートされます。

クエリ プロバイダーの型システムでは、JSON プリミティブ型 (数値型、ブール値、文字列、null) のみがサポートされます。

クエリ プロバイダーでは、以下のスカラー式がサポートされます。

- 定数値。クエリ評価時のプリミティブ データ型の定数値を含みます。
  
- プロパティ/配列インデックス式。オブジェクトまたは配列要素のプロパティを参照します。 次に例を示します。
  
  ```
    family.Id;
    family.children[0].familyName;
    family.children[0].grade;
    family.children[n].grade; //n is an int variable
  ```
  
- 算術式。数値およびブール値に対する共通の算術式を含みます。 完全な一覧については、[Azure Cosmos DB SQL の仕様](sql-query-aggregate-functions.md)に関するページを参照してください。
  
  ```
    2 * family.children[0].grade;
    x + y;
  ```
  
- 文字列比較式。これは、文字列値と定数文字列値との比較を含みます。  
  
  ```
    mother.familyName == "Wakefield";
    child.givenName == s; //s is a string variable
  ```
  
- オブジェクト/配列作成式。複合値の型または匿名型のオブジェクト、またはこうしたオブジェクトの配列を返します。 これらの値は入れ子にすることができます。
  
  ```
    new Parent { familyName = "Wakefield", givenName = "Robin" };
    new { first = 1, second = 2 }; //an anonymous type with two fields  
    new int[] { 3, child.grade, 5 };
  ```

## <a name="using-linq"></a>LINQ を使用する

`GetItemLinqQueryable` を使用して LINQ クエリを作成できます。 この例では、`FeedIterator` を使用した LINQ クエリの生成と非同期実行を示します。

```csharp
using (FeedIterator<Book> setIterator = container.GetItemLinqQueryable<Book>()
                      .Where(b => b.Title == "War and Peace")
                      .ToFeedIterator<Book>())
 {
     //Asynchronous query execution
     while (setIterator.HasMoreResults)
     {
         foreach(var item in await setIterator.ReadNextAsync()){
         {
             Console.WriteLine(item.cost);
         }
       }
     }
 }
```

## <a name="supported-linq-operators"></a><a id="SupportedLinqOperators"></a>サポートされている LINQ 演算子

SQL .NET SDK に含まれる LINQ プロバイダーでは、次の演算子がサポートされています。

- **Select**:オブジェクトの構築など、プロジェクションによって [SELECT](sql-query-select.md) に変換します。
- **Where**:フィルターによって [WHERE](sql-query-where.md) に変換します。また、`&&`、`||`、および `!` から SQL 演算子への変換をサポートしています
- **SelectMany**:[JOIN](sql-query-join.md) 句に対して配列をアンワインドできます。 配列要素に関してフィルターする式を連結または入れ子にするために使用します。
- **OrderBy** と **OrderByDescending**:ASC または DESC で [ORDER BY](sql-query-order-by.md) に変換します。
- [集計](sql-query-aggregate-functions.md)のための **Count**、**Sum**、**Min**、**Max**、**Average** 演算子と非同期でそれに相当する **CountAsync**、**SumAsync**、**MinAsync**、**MaxAsync**、**AverageAsync** 演算子。
- **CompareTo**:範囲比較に変換します。 .NET では比較できないので、一般的に文字列に使用されます。
- **Skip** と **Take**:クエリからの結果を制限して改ページ位置の自動修正を実行するために、[OFFSET および LIMIT](sql-query-offset-limit.md) に変換されます。
- **数学関数**:.NET `Abs`、`Acos`、`Asin`、`Atan`、`Ceiling`、`Cos`、`Exp`、`Floor`、`Log`、`Log10`、`Pow`、`Round`、`Sign`、`Sin`、`Sqrt`、`Tan`、および `Truncate` から同等の [組み込み数学関数](sql-query-mathematical-functions.md)への変換をサポートします。
- **文字列関数**:.NET `Concat`、`Contains`、`Count`、`EndsWith`,`IndexOf`、`Replace`、`Reverse`、`StartsWith`、`SubString`、`ToLower`、`ToUpper`、`TrimEnd`、および `TrimStart` から同等の [組み込み文字列関数](sql-query-string-functions.md)への変換をサポートします。
- **配列関数**:.NET `Concat`、`Contains`、および `Count` から同等の [組み込み配列関数](sql-query-array-functions.md)への変換をサポートします。
- **地理空間の拡張関数**:スタブ メソッド `Distance`、`IsValid`、`IsValidDetailed`、および `Within` から同等の [組み込み地理空間関数](sql-query-geospatial-query.md)への変換をサポートします。
- **ユーザー定義関数の拡張関数**:スタブ メソッド `UserDefinedFunctionProvider.Invoke` から、対応する [ユーザー定義関数](sql-query-udfs.md)への変換をサポートします。
- **その他**:`Coalesce` と条件 [演算子](sql-query-operators.md)の変換をサポートします。 コンテキストに応じて、`Contains` から、文字列 CONTAINS、ARRAY_CONTAINS、または IN に変換できます。

## <a name="examples"></a>例

一部の標準 LINQ クエリ演算子が Azure Cosmos DB のクエリにどのように変換されるかを以下の例で示します。

### <a name="select-operator"></a>Select 演算子

構文は `input.Select(x => f(x))` です。`f` はスカラー式です。 この場合、`input` は `IQueryable` オブジェクトになります。

**Select 演算子、例 1:**

- **LINQ ラムダ式**
  
  ```csharp
      input.Select(family => family.parents[0].familyName);
  ```
  
- **SQL**
  
  ```sql
      SELECT VALUE f.parents[0].familyName
      FROM Families f
    ```
  
**Select 演算子、例 2:**

- **LINQ ラムダ式**
  
  ```csharp
      input.Select(family => family.children[0].grade + c); // c is an int variable
  ```
  
- **SQL**
  
  ```sql
      SELECT VALUE f.children[0].grade + c
      FROM Families f
  ```
  
**Select 演算子、例 3:**

- **LINQ ラムダ式**
  
  ```csharp
    input.Select(family => new
    {
        name = family.children[0].familyName,
        grade = family.children[0].grade + 3
    });
  ```
  
- **SQL**
  
  ```sql
      SELECT VALUE {"name":f.children[0].familyName,
                    "grade": f.children[0].grade + 3 }
      FROM Families f
  ```

### <a name="selectmany-operator"></a>SelectMany 演算子

構文は `input.SelectMany(x => f(x))` です。`f` はコンテナー型を返すスカラー式です。

- **LINQ ラムダ式**
  
  ```csharp
      input.SelectMany(family => family.children);
  ```
  
- **SQL**

  ```sql
      SELECT VALUE child
      FROM child IN Families.children
  ```

### <a name="where-operator"></a>Where 演算子

構文は `input.Where(x => f(x))` です。`f` は Boolean 値を返すスカラー式です。

**Where 演算子、例 1:**

- **LINQ ラムダ式**
  
  ```csharp
      input.Where(family=> family.parents[0].familyName == "Wakefield");
  ```
  
- **SQL**
  
  ```sql
      SELECT *
      FROM Families f
      WHERE f.parents[0].familyName = "Wakefield"
  ```
  
**Where 演算子、例 2:**

- **LINQ ラムダ式**
  
  ```csharp
      input.Where(
          family => family.parents[0].familyName == "Wakefield" &&
          family.children[0].grade < 3);
  ```
  
- **SQL**
  
  ```sql
      SELECT *
      FROM Families f
      WHERE f.parents[0].familyName = "Wakefield"
      AND f.children[0].grade < 3
  ```

## <a name="composite-sql-queries"></a>複合 SQL クエリ

上記の演算子を組み合わせて、より強力なクエリを作成できます。 Cosmos DB では入れ子になったコンテナーがサポートされるため、複合を連結または入れ子にできます。

### <a name="concatenation"></a>連結

構文は `input(.|.SelectMany())(.Select()|.Where())*`です。 連結クエリは、オプションの `SelectMany` クエリで開始し、複数の `Select` または `Where` 演算子を追加できます。

**連結、例 1:**

- **LINQ ラムダ式**
  
  ```csharp
      input.Select(family => family.parents[0])
          .Where(parent => parent.familyName == "Wakefield");
  ```

- **SQL**
  
  ```sql
      SELECT *
      FROM Families f
      WHERE f.parents[0].familyName = "Wakefield"
  ```

**連結、例 2:**

- **LINQ ラムダ式**
  
  ```csharp
      input.Where(family => family.children[0].grade > 3)
          .Select(family => family.parents[0].familyName);
  ```

- **SQL**
  
  ```sql
      SELECT VALUE f.parents[0].familyName
      FROM Families f
      WHERE f.children[0].grade > 3
  ```

**連結、例 3:**

- **LINQ ラムダ式**
  
  ```csharp
      input.Select(family => new { grade=family.children[0].grade}).
          Where(anon=> anon.grade < 3);
  ```
  
- **SQL**
  
  ```sql
      SELECT *
      FROM Families f
      WHERE ({grade: f.children[0].grade}.grade > 3)
  ```

**連結、例 4:**

- **LINQ ラムダ式**
  
  ```csharp
      input.SelectMany(family => family.parents)
          .Where(parent => parents.familyName == "Wakefield");
  ```
  
- **SQL**
  
  ```sql
      SELECT *
      FROM p IN Families.parents
      WHERE p.familyName = "Wakefield"
  ```

### <a name="nesting"></a>入れ子

構文は `input.SelectMany(x=>x.Q())` です。`Q` は `Select`、`SelectMany`、または `Where` 演算子です。

入れ子になったクエリは、内側のクエリを外側のコンテナーの各要素に適用します。 1 つの重要な機能として、内側のクエリは外側のコンテナーの要素のフィールドを自己結合のように参照できます。

**入れ子、例 1:**

- **LINQ ラムダ式**
  
  ```csharp
      input.SelectMany(family=>
          family.parents.Select(p => p.familyName));
  ```

- **SQL**
  
  ```sql
      SELECT VALUE p.familyName
      FROM Families f
      JOIN p IN f.parents
  ```

**入れ子、例 2:**

- **LINQ ラムダ式**
  
  ```csharp
      input.SelectMany(family =>
          family.children.Where(child => child.familyName == "Jeff"));
  ```

- **SQL**
  
  ```sql
      SELECT *
      FROM Families f
      JOIN c IN f.children
      WHERE c.familyName = "Jeff"
  ```

**入れ子、例 3:**

- **LINQ ラムダ式**
  
  ```csharp
      input.SelectMany(family => family.children.Where(
          child => child.familyName == family.parents[0].familyName));
  ```

- **SQL**
  
  ```sql
      SELECT *
      FROM Families f
      JOIN c IN f.children
      WHERE c.familyName = f.parents[0].familyName
  ```

## <a name="next-steps"></a>次のステップ

- [Azure Cosmos DB .NET のサンプル](https://github.com/Azure/azure-cosmos-dotnet-v3)
- [モデル ドキュメント データ](modeling-data.md)