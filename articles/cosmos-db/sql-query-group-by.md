---
title: Azure Cosmos DB での GROUP BY 句
description: Azure Cosmos DB の GROUP BY 句について説明します。
author: timsander1
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 05/19/2020
ms.author: tisande
ms.openlocfilehash: d9cafc100ddd4b553577c447e82334e6ee7d1b6d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "96545453"
---
# <a name="group-by-clause-in-azure-cosmos-db"></a>Azure Cosmos DB での GROUP BY 句
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

GROUP BY 句では、指定した 1 つ以上のプロパティの値に従ってクエリの結果を分割します。

## <a name="syntax"></a>構文

```sql  
<group_by_clause> ::= GROUP BY <scalar_expression_list>

<scalar_expression_list> ::=
          <scalar_expression>
        | <scalar_expression_list>, <scalar_expression>
```  

## <a name="arguments"></a>引数

- `<scalar_expression_list>`

   クエリ結果を分割するために使用される式を指定します。

- `<scalar_expression>`
  
   スカラー サブクエリとスカラー集計を除き、任意のスカラー式を使用できます。 各スカラー式には、少なくとも 1 つのプロパティ参照が含まれている必要があります。 個々の式の数や各式のカーディナリティに制限はありません。

## <a name="remarks"></a>解説
  
  クエリで GROUP BY 句を使用する場合、SELECT 句には、GROUP BY 句に含まれるプロパティとシステム関数のサブセットのみを含めることができます。 1 つの例外として、GROUP BY 句の中に含めずに SELECT 句に指定できる[集計関数](sql-query-aggregate-functions.md)があります。 SELECT 句には、常にリテラル値も含めることができます。

  GROUP BY 句は、SELECT、FROM、および WHERE 句の後、OFFSET LIMIT 句の前に指定する必要があります。 現在は GROUP BY を ORDER BY 句と共に使用することはできませんが、これは計画されています。

  GROUP BY 句では、次のいずれも許可されていません。
  
- プロパティのエイリアス化またはシステム関数のエイリアス化 (なお、SELECT 句ではエイリアス化を使用できます)
- サブクエリ
- 集計システム関数 (SELECT 句でのみ使用できます)

集計システム関数を使用したクエリと `GROUP BY` を使用したサブクエリはサポートされていません。 たとえば、次のクエリはサポートされません。

```sql
SELECT COUNT(UniqueLastNames)
FROM (
SELECT AVG(f.age)
FROM f
GROUP BY f.lastName
) AS UniqueLastNames
```

## <a name="examples"></a>例

これらの例では、[Azure Cosmos DB Query Playground](https://www.documentdb.com/sql/demo) で使用可能な栄養データセットを使用します。

たとえば、次のクエリでは、各 foodGroup 内の項目の合計数が返されます。

```sql
SELECT TOP 4 COUNT(1) AS foodGroupCount, f.foodGroup
FROM Food f
GROUP BY f.foodGroup
```

結果の一部を次に示します (TOP キーワードは結果を制限するために使用されています)。

```json
[
    {
        "foodGroupCount": 183,
        "foodGroup": "Cereal Grains and Pasta"
    },
    {
        "foodGroupCount": 133,
        "foodGroup": "Nut and Seed Products"
    },
    {
        "foodGroupCount": 113,
        "foodGroup": "Meals, Entrees, and Side Dishes"
    },
    {
        "foodGroupCount": 64,
        "foodGroup": "Spices and Herbs"
    }
]
```

このクエリには、結果を分割するために使用される 2 つの式があります。

```sql
SELECT TOP 4 COUNT(1) AS foodGroupCount, f.foodGroup, f.version
FROM Food f
GROUP BY f.foodGroup, f.version
```

結果は次のようになります。

```json
[
    {
        "foodGroupCount": 183,
        "foodGroup": "Cereal Grains and Pasta",
        "version": 1
    },
    {
        "foodGroupCount": 133,
        "foodGroup": "Nut and Seed Products",
        "version": 1
    },
    {
        "foodGroupCount": 113,
        "foodGroup": "Meals, Entrees, and Side Dishes",
        "version": 1
    },
    {
        "foodGroupCount": 64,
        "foodGroup": "Spices and Herbs",
        "version": 1
    }
]
```

このクエリには、GROUP BY 句にシステム関数があります。

```sql
SELECT TOP 4 COUNT(1) AS foodGroupCount, UPPER(f.foodGroup) AS upperFoodGroup
FROM Food f
GROUP BY UPPER(f.foodGroup)
```

結果は次のようになります。

```json
[
    {
        "foodGroupCount": 183,
        "upperFoodGroup": "CEREAL GRAINS AND PASTA"
    },
    {
        "foodGroupCount": 133,
        "upperFoodGroup": "NUT AND SEED PRODUCTS"
    },
    {
        "foodGroupCount": 113,
        "upperFoodGroup": "MEALS, ENTREES, AND SIDE DISHES"
    },
    {
        "foodGroupCount": 64,
        "upperFoodGroup": "SPICES AND HERBS"
    }
]
```

このクエリでは、品目のプロパティ式でキーワードとシステム関数の両方を使用しています。

```sql
SELECT COUNT(1) AS foodGroupCount, ARRAY_CONTAINS(f.tags, {name: 'orange'}) AS containsOrangeTag,  f.version BETWEEN 0 AND 2 AS correctVersion
FROM Food f
GROUP BY ARRAY_CONTAINS(f.tags, {name: 'orange'}), f.version BETWEEN 0 AND 2
```

結果は次のようになります。

```json
[
    {
        "foodGroupCount": 10,
        "containsOrangeTag": true,
        "correctVersion": true
    },
    {
        "foodGroupCount": 8608,
        "containsOrangeTag": false,
        "correctVersion": true
    }
]
```

## <a name="next-steps"></a>次のステップ

- [作業の開始](sql-query-getting-started.md)
- [SELECT 句](sql-query-select.md)
- [集計関数](sql-query-aggregate-functions.md)
