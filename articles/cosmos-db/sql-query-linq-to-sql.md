---
title: Azure Cosmos DB での LINQ から SQL への変換
description: LINQ クエリを Azure Cosmos DB SQL クエリにマッピングします。
author: timsander1
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/30/2019
ms.author: tisande
ms.openlocfilehash: 057614da8fd29e1208c2788049c5d6d1a985eed5
ms.sourcegitcommit: a12b2c2599134e32a910921861d4805e21320159
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/24/2019
ms.locfileid: "67343277"
---
# <a name="linq-to-sql-translation"></a>LINQ から SQL への変換

Azure Cosmos DB クエリ プロバイダーは、LINQ クエリから Cosmos DB SQL クエリへのマッピングをベスト エフォートで実行します。 以下の説明では、LINQ の基本的知識を前提としています。

クエリ プロバイダーの型システムでは、JSON プリミティブ型 (数値型、ブール値、文字列、null) のみがサポートされます。

クエリ プロバイダーでは、以下のスカラー式がサポートされます。

- 定数値。クエリ評価時のプリミティブ データ型の定数値を含みます。
  
- プロパティ/配列インデックス式。オブジェクトまたは配列要素のプロパティを参照します。 例:
  
  ```
    family.Id;
    family.children[0].familyName;
    family.children[0].grade;
    family.children[n].grade; //n is an int variable
  ```
  
- 算術式。数値およびブール値に対する共通の算術式を含みます。 完全な一覧については、[Azure Cosmos DB SQL の仕様](https://go.microsoft.com/fwlink/p/?LinkID=510612)に関するページを参照してください。
  
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

## <a id="SupportedLinqOperators"></a>サポートされている LINQ 演算子

SQL .NET SDK に含まれる LINQ プロバイダーでは、次の演算子がサポートされています。

- **Select**:オブジェクトの構築など、プロジェクションによって SQL SELECT に変換します。
- **Where**:フィルターによって SQL WHERE に変換します。また、`&&`、`||`、および `!` から SQL 演算子への変換をサポートしています
- **SelectMany**:SQL JOIN 句に対して配列をアンワインドできます。 配列要素に関してフィルターする式を連結または入れ子にするために使用します。
- **OrderBy** と **OrderByDescending**:ASC または DESC で ORDER BY に変換します。
- 集計のための **Count**、**Sum**、**Min**、**Max**、**Average** 演算子と非同期でそれに相当する **CountAsync**、**SumAsync**、**MinAsync**、**MaxAsync**、**AverageAsync** 演算子。
- **CompareTo**:範囲比較に変換します。 .NET では文字列を比較できないので、一般的に文字列に使用されます。
- **Take**:クエリからの結果を制限するために SQL TOP に変換します。
- **数学関数**:.NET `Abs`、`Acos`、`Asin`、`Atan`、`Ceiling`、`Cos`、`Exp`、`Floor`、`Log`、`Log10`、`Pow`、`Round`、`Sign`、`Sin`、`Sqrt`、`Tan`、および `Truncate` から同等の SQL 組み込み関数への変換をサポートします。
- **文字列関数**:.NET `Concat`、`Contains`、`Count`、`EndsWith`、`IndexOf`、`Replace`、`Reverse`、`StartsWith`、`SubString`、`ToLower`、`ToUpper`、`TrimEnd`、および `TrimStart` から同等の SQL 組み込み関数への変換をサポートします。
- **配列関数**:.NET `Concat`、`Contains`、および `Count` から同等の SQL 組み込み関数への変換をサポートします。
- **地理空間の拡張関数**:スタブ メソッド `Distance`、`IsValid`、`IsValidDetailed`、および `Within` から同等の SQL 組み込み関数への変換をサポートします。
- **ユーザー定義関数の拡張関数**:スタブ メソッドの `UserDefinedFunctionProvider.Invoke` から、対応するユーザー定義関数への変換をサポートします。
- **その他**:`Coalesce` 演算子と条件演算子の変換をサポートします。 コンテキストに応じて、`Contains` から、文字列 CONTAINS、ARRAY_CONTAINS、または SQL IN に変換できます。

## <a name="examples"></a>例

一部の標準 LINQ クエリ演算子が Cosmos DB クエリにどのように変換されるかを以下の例で示します。

### <a name="select-operator"></a>Select 演算子

構文は `input.Select(x => f(x))` です。`f` はスカラー式です。

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
      input.Select(family=>family.parents[0])
          .Where(familyName == "Wakefield");
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


## <a name="next-steps"></a>次の手順

- [Azure Cosmos DB .NET のサンプル](https://github.com/Azure/azure-cosmosdb-dotnet)
- [モデル ドキュメント データ](modeling-data.md)
