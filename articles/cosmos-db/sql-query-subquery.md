---
title: Azure Cosmos DB の SQL サブクエリ
description: Azure Cosmos DB の SQL サブクエリとその一般的なユース ケースについて説明します
author: timsander1
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/23/2019
ms.author: tisande
ms.openlocfilehash: 4181a44e87d59d35d424a51c8fedc89523223f90
ms.sourcegitcommit: a12b2c2599134e32a910921861d4805e21320159
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/24/2019
ms.locfileid: "67343169"
---
# <a name="sql-subquery-examples-for-azure-cosmos-db"></a>Azure Cosmos DB の SQL サブクエリの例

サブクエリは、別のクエリ内で入れ子になったクエリです。 サブクエリは、内部クエリまたは内部選択とも呼ばれます。 サブクエリを含むステートメントは通常、外部クエリと呼ばれます。

この記事では、Azure Cosmos DB の SQL サブクエリとその一般的なユース ケースについて説明します。 このドキュメントのすべてのサンプル クエリは、[Azure Cosmos DB Query Playground](https://www.documentdb.com/sql/demo) に事前に読み込まれている栄養摂取量データセットに対して実行できます。

## <a name="types-of-subqueries"></a>サブクエリの種類

サブクエリには、主に次の 2 種類があります。

* **相関**:外部クエリからの値を参照するサブクエリ。 サブクエリは、外部クエリが処理する行ごとに 1 回評価されます。
* **非相関**:外部クエリから独立しているサブクエリ。 外部クエリに依存せずに単独で実行できます。

> [!NOTE]
> Azure Cosmos DB は相関サブクエリのみをサポートします。

サブクエリは、返される行と列の数に基づいてさらに分類できます。 次の 3 種類があります。
* **テーブル**:複数の行と複数の列を返します。
* **複数値**:複数の行と 1 つの列を返します。
* **スカラー**:1 つの行と 1 つの列を返します。

Azure Cosmos DB の SQL クエリは常に 1 つの列 (単純な値または複合ドキュメントのどちらか) を返します。 したがって、Azure Cosmos DB では、複数値サブクエリとスカラー サブクエリのみを適用できます。 複数値サブクエリは、関係式として FROM 句でのみ使用できます。 スカラー サブクエリは、SELECT または WHERE 句でスカラー式として、あるいは FROM 句で関係式として使用できます。

## <a name="multi-value-subqueries"></a>複数値サブクエリ

複数値サブクエリはドキュメントのセットを返し、常に FROM 句内で使用されます。 次の目的に使用されます。

* JOIN 式を最適化する。 
* コストが高い式を 1 回評価して複数回参照する。

## <a name="optimize-join-expressions"></a>JOIN 式を最適化する

複数値サブクエリは、WHERE 句内のすべてのクロス結合の後ではなく、それぞれの select-many 式の後に述語をプッシュすることによって JOIN 式を最適化できます。

次のクエリについて考えてみましょう。

```sql
SELECT Count(1) AS Count
FROM c
JOIN t IN c.tags
JOIN n IN c.nutrients
JOIN s IN c.servings
WHERE t.name = 'infant formula' AND (n.nutritionValue > 0 
AND n.nutritionValue < 10) AND s.amount > 1
```

このクエリでは、インデックスは、"infant formula"(乳児用調製粉乳) という名前のタグを持つ任意のドキュメントに一致します。 それは 0 ～ 10 の値の nutrient (栄養素) 項目と、1 より大きい数量の serving (1 回分) 項目です。 ここでの JOIN 式は、一致するドキュメントごとに、タグ、栄養素、1 回分の各配列の全項目の外積を、フィルターが適用される前に実行します。 

WHERE 句はその後、<c, t, n, s> タプルごとにフィルター述語を適用します。 たとえば、一致するドキュメントで、3 つの配列のそれぞれに 10 個の項目があるとした場合、1 x 10 x 10 x 10 (つまり、1,000) タプルに展開されます。 ここでサブクエリを使用すると、次の式と結合する前に、結合された配列項目をフィルターで除外するために役立ちます。

このクエリは前のものと同等ですが、サブクエリを使用します。

```sql
SELECT Count(1) AS Count
FROM c
JOIN (SELECT VALUE t FROM t IN c.tags WHERE t.name = 'infant formula')
JOIN (SELECT VALUE n FROM n IN c.nutrients WHERE n.nutritionValue > 0 AND n.nutritionValue < 10)
JOIN (SELECT VALUE s FROM s IN c.servings WHERE s.amount > 1)
```

tags 配列の 1 つの項目のみがフィルターに一致し、nutrients 配列と servings 配列の両方に 5 つの項目があるとします。 JOIN 式は、最初のクエリの 1,000 項目とは異なり、1 x 1 x 5 x 5 = 25 項目に展開されます。

## <a name="evaluate-once-and-reference-many-times"></a>1 回評価して複数回参照する

サブクエリは、ユーザー定義関数 (UDF)、複雑な文字列、算術式などのコストが高い式でクエリを最適化するために役立ちます。 サブクエリを JOIN 式と一緒に使用することで、式を 1 回しか評価しないが複数回参照するという処理ができます。

次のクエリは UDF `GetMaxNutritionValue` を 2 回実行します。

```sql
SELECT c.id, udf.GetMaxNutritionValue(c.nutrients) AS MaxNutritionValue
FROM c
WHERE udf.GetMaxNutritionValue(c.nutrients) > 100
```

次に示すのは、UDF を 1 回しか実行しない同等のクエリです。

```sql
SELECT TOP 1000 c.id, MaxNutritionValue
FROM c
JOIN (SELECT VALUE udf.GetMaxNutritionValue(c.nutrients)) MaxNutritionValue
WHERE MaxNutritionValue > 100
``` 

> [!NOTE] 
> JOIN 式の外積の動作に留意してください。 UDF 式が未定義と評価される可能性がある場合、値を直接返すのではなくサブクエリからのオブジェクトを返すことによって、JOIN 式の結果が常に 1 行になることを保証してください。
>

次に示すのは、値ではなくオブジェクトを返す同様の例です。

```sql
SELECT TOP 1000 c.id, m.MaxNutritionValue
FROM c
JOIN (SELECT udf.GetMaxNutritionValue(c.nutrients) AS MaxNutritionValue) m
WHERE m.MaxNutritionValue > 100
```

このアプローチは UDF に限定されません。 潜在的にコストが高いあらゆる式に当てはまります。 たとえば、数学関数 `avg` に対して同じアプローチを採用できます。

```sql
SELECT TOP 1000 c.id, AvgNutritionValue
FROM c
JOIN (SELECT VALUE avg(n.nutritionValue) FROM n IN c.nutrients) AvgNutritionValue
WHERE AvgNutritionValue > 80
```

## <a name="mimic-join-with-external-reference-data"></a>外部参照データとの結合を模倣する

測定単位や国番号のように、ほとんど変更されない静的データを参照することが必要になる場合がよくあります。 望ましいのは、そのようなデータがドキュメントごとに重複しないことです。 この重複を回避してドキュメントのサイズを小さく保つことで、ストレージを節約し、書き込みパフォーマンスを高めることができます。 サブクエリを使用して、参照データのコレクションとの内部結合セマンティクスを模倣することができます。

たとえば、次のような参照データのセットを考えます。

| **単位** | **Name**            | **乗数** | **基本単位** |
| -------- | ------------------- | -------------- | ------------- |
| ng       | ナノグラム            | 1.00E-09       | グラム          |
| µg       | マイクログラム           | 1.00E-06       | グラム          |
| mg       | ミリグラム           | 1.00E-03       | グラム          |
| g        | グラム                | 1.00E+00       | グラム          |
| kg       | キログラム            | 1.00E+03       | グラム          |
| Mg       | メガグラム            | 1.00E+06       | グラム          |
| Gg       | ギガグラム            | 1.00E+09       | グラム          |
| nJ       | ナノジュール           | 1.00E-09       | ジュール         |
| µJ       | マイクロジュール          | 1.00E-06       | ジュール         |
| mJ       | ミリジュール          | 1.00E-03       | ジュール         |
| J        | ジュール               | 1.00E+00       | ジュール         |
| kJ       | キロジュール           | 1.00E+03       | ジュール         |
| MJ       | メガジュール           | 1.00E+06       | ジュール         |
| GJ       | ギガジュール           | 1.00E+09       | ジュール         |
| cal      | カロリー             | 1.00E+00       | カロリー       |
| kcal     | カロリー             | 1.00E+03       | カロリー       |
| IU       | 国際単位 |                |               |


次のクエリは、このデータとの結合を模倣し、単位の名前を出力に追加します。

```sql
SELECT TOP 10 n.id, n.description, n.nutritionValue, n.units, r.name
FROM food
JOIN n IN food.nutrients
JOIN r IN (
    SELECT VALUE [
        {unit: 'ng', name: 'nanogram', multiplier: 0.000000001, baseUnit: 'gram'},
        {unit: 'µg', name: 'microgram', multiplier: 0.000001, baseUnit: 'gram'},
        {unit: 'mg', name: 'milligram', multiplier: 0.001, baseUnit: 'gram'},
        {unit: 'g', name: 'gram', multiplier: 1, baseUnit: 'gram'},
        {unit: 'kg', name: 'kilogram', multiplier: 1000, baseUnit: 'gram'},
        {unit: 'Mg', name: 'megagram', multiplier: 1000000, baseUnit: 'gram'},
        {unit: 'Gg', name: 'gigagram', multiplier: 1000000000, baseUnit: 'gram'},
        {unit: 'nJ', name: 'nanojoule', multiplier: 0.000000001, baseUnit: 'joule'},
        {unit: 'µJ', name: 'microjoule', multiplier: 0.000001, baseUnit: 'joule'},
        {unit: 'mJ', name: 'millijoule', multiplier: 0.001, baseUnit: 'joule'},
        {unit: 'J', name: 'joule', multiplier: 1, baseUnit: 'joule'},
        {unit: 'kJ', name: 'kilojoule', multiplier: 1000, baseUnit: 'joule'},
        {unit: 'MJ', name: 'megajoule', multiplier: 1000000, baseUnit: 'joule'},
        {unit: 'GJ', name: 'gigajoule', multiplier: 1000000000, baseUnit: 'joule'},
        {unit: 'cal', name: 'calorie', multiplier: 1, baseUnit: 'calorie'},
        {unit: 'kcal', name: 'Calorie', multiplier: 1000, baseUnit: 'calorie'},
        {unit: 'IU', name: 'International units'}
    ]
)
WHERE n.units = r.unit
```

## <a name="scalar-subqueries"></a>スカラー サブクエリ

スカラー サブクエリ式は、1 つの値に評価されるサブクエリです。 スカラー サブクエリ式の値は、サブクエリのプロジェクション (SELECT 句) の値です。  スカラー式が有効な多くの場面で、スカラー サブクエリ式を使用できます。 たとえば、SELECT 句と WHERE 句両方の任意の式でスカラー サブクエリを使用できます。

ただし、スカラー サブクエリの使用が常に最適化に寄与するとは限りません。 たとえば、スカラー サブクエリをシステムまたはユーザー定義関数に引数として渡しても、リソース単位 (RU) 消費または待機時間の面でメリットはありません。

スカラー サブクエリは、さらに次のように分類できます。
* 単純式スカラー サブクエリ
* 集計スカラー サブクエリ

## <a name="simple-expression-scalar-subqueries"></a>単純式スカラー サブクエリ

単純式スカラー サブクエリは、集約式を含まない SELECT 句を持つ相関サブクエリです。 これらのサブクエリは、コンパイラによって 1 つのより大きな単純式に変換されるため、最適化のベネフィットをもたらしません。 内部クエリと外部クエリの間に相関コンテキストはありません。

次に例をいくつか示します。

**例 1**

```sql
SELECT 1 AS a, 2 AS b
```

単純式スカラー サブクエリを使用して、このクエリを次のように書き直すことができます。

```sql
SELECT (SELECT VALUE 1) AS a, (SELECT VALUE 2) AS b
```

どちらのクエリも次の出力を生成します。

```json
[
  { "a": 1, "b": 2 }
]
```

**例 2**

```sql
SELECT TOP 5 Concat('id_', f.id) AS id
FROM food f
```

単純式スカラー サブクエリを使用して、このクエリを次のように書き直すことができます。

```sql
SELECT TOP 5 (SELECT VALUE Concat('id_', f.id)) AS id
FROM food f
```

クエリ出力:

```json
[
  { "id": "id_03226" },
  { "id": "id_03227" },
  { "id": "id_03228" },
  { "id": "id_03229" },
  { "id": "id_03230" }
]
```

**例 3**

```sql
SELECT TOP 5 f.id, Contains(f.description, 'fruit') = true ? f.description : undefined
FROM food f
```

単純式スカラー サブクエリを使用して、このクエリを次のように書き直すことができます。

```sql
SELECT TOP 10 f.id, (SELECT f.description WHERE Contains(f.description, 'fruit')).description
FROM food f
```

クエリ出力:

```json
[
  { "id": "03230" },
  { "id": "03238", "description":"Babyfood, dessert, tropical fruit, junior" },
  { "id": "03229" },
  { "id": "03226", "description":"Babyfood, dessert, fruit pudding, orange, strained" },
  { "id": "03227" }
]
```

### <a name="aggregate-scalar-subqueries"></a>集計スカラー サブクエリ

集計スカラー サブクエリは、1 つの値に評価されるそのプロジェクションまたはフィルターに集計関数が含まれるサブクエリです。

**例 1:**

次に示すのは、1 つの集計関数式がそのプロジェクションに含まれるサブクエリです。

```sql
SELECT TOP 5 
    f.id, 
    (SELECT VALUE Count(1) FROM n IN f.nutrients WHERE n.units = 'mg'
) AS count_mg
FROM food f
```

クエリ出力:

```json
[
  { "id": "03230", "count_mg": 13 },
  { "id": "03238", "count_mg": 14 },
  { "id": "03229", "count_mg": 13 },
  { "id": "03226", "count_mg": 15 },
  { "id": "03227", "count_mg": 19 }
]
```

**例 2**

次に示すのは、複数の集計関数式が含まれるサブクエリです。

```sql
SELECT TOP 5 f.id, (
    SELECT Count(1) AS count, Sum(n.nutritionValue) AS sum 
    FROM n IN f.nutrients 
    WHERE n.units = 'mg'
) AS unit_mg
FROM food f
```

クエリ出力:

```json
[
  { "id": "03230","unit_mg": { "count": 13,"sum": 147.072 } },
  { "id": "03238","unit_mg": { "count": 14,"sum": 107.385 } },
  { "id": "03229","unit_mg": { "count": 13,"sum": 141.579 } },
  { "id": "03226","unit_mg": { "count": 15,"sum": 183.91399999999996 } },
  { "id": "03227","unit_mg": { "count": 19,"sum": 94.788999999999987 } }
]
```

**例 3**

次に示すのは、プロジェクションとフィルターの両方に集計サブクエリが含まれるクエリです。

```sql
SELECT TOP 5 
    f.id, 
    (SELECT VALUE Count(1) FROM n IN f.nutrients WHERE n.units = 'mg') AS count_mg
FROM food f
WHERE (SELECT VALUE Count(1) FROM n IN f.nutrients WHERE n.units = 'mg') > 20
```

クエリ出力:

```json
[
  { "id": "03235", "count_mg": 27 },
  { "id": "03246", "count_mg": 21 },
  { "id": "03267", "count_mg": 21 },
  { "id": "03269", "count_mg": 21 },
  { "id": "03274", "count_mg": 21 }
]
```

このクエリのより最適な記述方法は、サブクエリで結合し、SELECT 句と WHERE 句の両方でサブクエリの別名を参照することです。 このクエリのほうが効率的なのは、JOIN ステートメントの中でサブクエリを実行するだけでよく、プロジェクションとフィルターの両方で実行する必要がないからです。

```sql
SELECT TOP 5 f.id, count_mg
FROM food f
JOIN (SELECT VALUE Count(1) FROM n IN f.nutrients WHERE n.units = 'mg') AS count_mg
WHERE count_mg > 20
```

## <a name="exists-expression"></a>EXISTS 式

Azure Cosmos DB は EXISTS 式をサポートしています。 これは、Azure Cosmos DB SQL API に組み込まれている集計スカラー サブクエリです。 EXISTS は、サブクエリ式を取り、サブクエリが行を返す場合は true を返すブール式です。 それ以外の場合、false を返します。

Azure Cosmos DB SQL API はブール式と他のスカラー式を区別しないため、SELECT 句と WHERE 句の両方で EXISTS を使用できます。 これは、ブール式 (EXISTS、BETWEEN、IN など) がフィルターに制限されている T-SQL とは異なります。

EXISTS サブクエリが 1 つの値を返し、その値が未定義である場合、EXISTS は false と評価されます。 たとえば、false と評価される次のクエリを考えます。
```sql
SELECT EXISTS (SELECT VALUE undefined)
```   


前のサブクエリで VALUE キーワードを省略した場合、クエリは true と評価されます。
```sql
SELECT EXISTS (SELECT undefined) 
```

サブクエリは、選択したリスト内の値のリストをオブジェクトに封入します。 選択したリストに値がない場合、サブクエリは 1 つの値 "{}" を返します。 この値は定義されているため、EXISTS は true と評価されます。

### <a name="example-rewriting-arraycontains-and-join-as-exists"></a>例:ARRAY_CONTAINS と JOIN を EXISTS に書き直す

ARRAY_CONTAINS の一般的なユース ケースでは、配列内の項目の存在によってドキュメントをフィルター処理します。 この場合は、"orange" という名前の項目が tags 配列に含まれているかどうかチェックしています。

```sql
SELECT TOP 5 f.id, f.tags
FROM food f
WHERE ARRAY_CONTAINS(f.tags, {name: 'orange'})
```

EXISTS を使用するように同じクエリを書き直すことができます。

```sql
SELECT TOP 5 f.id, f.tags
FROM food f
WHERE EXISTS(SELECT VALUE t FROM t IN f.tags WHERE t.name = 'orange')
```

さらに、ARRAY_CONTAINS がチェックできるのは、配列内のいずれかの要素に値が等しいかどうかだけです。 さらに複雑なフィルターが配列プロパティに対して必要な場合は、JOIN を使用します。

配列内の units および `nutritionValue` プロパティに基づいてフィルター処理を行う次のクエリを考えます。 

```sql
SELECT VALUE c.description
FROM c
JOIN n IN c.nutrients
WHERE n.units= "mg" AND n.nutritionValue > 0
```

コレクション内のドキュメントごとに、その配列要素を使用して外積を実行します。 この JOIN 操作により、配列内のプロパティをフィルター処理できます。 ただし、このクエリの RU 消費は多大です。 たとえば、1,000 個のドキュメントの各配列に 100 個の項目があるとした場合、1,000 x 100 (= 100,000) タプルに展開されます。

EXISTS を使用すれば、このようにコストが高い外積を回避できます。

```sql
SELECT VALUE c.description
FROM c
WHERE EXISTS(
    SELECT VALUE n
    FROM n IN c.nutrients
    WHERE n.units = "mg" AND n.nutritionValue > 0
)
```

この場合、EXISTS サブクエリ内で配列要素をフィルター処理します。 配列要素がフィルターに一致した場合、それを射影し、EXISTS は true と評価されます。

EXISTS に別名を付けてプロジェクション内で参照することもできます。

```sql
SELECT TOP 1 c.description, EXISTS(
    SELECT VALUE n
    FROM n IN c.nutrients
    WHERE n.units = "mg" AND n.nutritionValue > 0) as a
FROM c
```

クエリ出力:

```json
[
    {
        "description": "Babyfood, dessert, fruit pudding, orange, strained",
        "a": true
    }
]
```

## <a name="array-expression"></a>ARRAY 式

ARRAY 式を使用して、クエリの結果を配列として射影することができます。 この式はクエリの SELECT 句内でのみ使用できます。

```sql
SELECT TOP 1   f.id, ARRAY(SELECT VALUE t.name FROM t in f.tags) AS tagNames
FROM  food f
```

クエリ出力:

```json
[
    {
        "id": "03238",
        "tagNames": [
            "babyfood",
            "dessert",
            "tropical fruit",
            "junior"
        ]
    }
]
```

他のサブクエリと同様、ARRAY 式によるフィルターが可能です。

```sql
SELECT TOP 1 c.id, ARRAY(SELECT VALUE t FROM t in c.tags WHERE t.name != 'infant formula') AS tagNames
FROM c
```

クエリ出力:

```json
[
    {
        "id": "03226",
        "tagNames": [
            {
                "name": "babyfood"
            },
            {
                "name": "dessert"
            },
            {
                "name": "fruit pudding"
            },
            {
                "name": "orange"
            },
            {
                "name": "strained"
            }
        ]
    }
]
```

サブクエリで配列式を FROM 句の後に置くこともできます。

```sql
SELECT TOP 1 c.id, ARRAY(SELECT VALUE t.name FROM t in c.tags) as tagNames
FROM c
JOIN n IN (SELECT VALUE ARRAY(SELECT t FROM t in c.tags WHERE t.name != 'infant formula'))
```

クエリ出力:

```json
[
    {
        "id": "03238",
        "tagNames": [
            "babyfood",
            "dessert",
            "tropical fruit",
            "junior"
        ]
    }
]
```

## <a name="next-steps"></a>次の手順

- [Azure Cosmos DB .NET のサンプル](https://github.com/Azure/azure-cosmosdb-dotnet)
- [モデル ドキュメント データ](modeling-data.md)
