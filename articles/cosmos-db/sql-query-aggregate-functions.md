---
title: Azure Cosmos DB の集計関数
description: SQL 集計関数の構文、Azure Cosmos DB でサポートされる集計関数の種類について説明します。
author: timsander1
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 12/02/2020
ms.author: tisande
ms.openlocfilehash: c0d953c8d99582f63744d51b505852b5c44bc409
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "96551077"
---
# <a name="aggregate-functions-in-azure-cosmos-db"></a>Azure Cosmos DB の集計関数
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

集計関数は、`SELECT` 句内の一連の値を計算して 1 つの値を返します。 たとえば、次のクエリでは、コンテナー内にある項目の数が返されます。

```sql
    SELECT COUNT(1)
    FROM c
```

## <a name="types-of-aggregate-functions"></a>集計関数の種類

SQL API は、次の集計関数をサポートしています。 `SUM` と `AVG` は数値に対して機能し、`COUNT`、`MIN`、および `MAX` は、数値、文字列、ブール値、および null 値に対して機能します。

| 関数 | 説明 |
|-------|-------------|
| [AVG](sql-query-aggregate-avg.md) | 式の値の平均を返します。 |
| [COUNT](sql-query-aggregate-count.md) | 式の項目の数を返します。 |
| [MAX](sql-query-aggregate-max.md) | 式内の最大値を返します。 |
| [MIN](sql-query-aggregate-min.md) | 式の最小値を返します。 |
| [SUM](sql-query-aggregate-sum.md) | 式のすべての値の合計を返します。 |


VALUE キーワードを使用して、集計のスカラー値のみを返すこともできます。 たとえば、次のクエリは、値の数を 1 つの数値として返します。

```sql
    SELECT VALUE COUNT(1)
    FROM Families f
```

結果は次のようになります。

```json
    [ 2 ]
```

フィルターによって集計を組み合わせることもできます。 たとえば、次のクエリでは、住所の州が `WA` である項目の数が返されます。

```sql
    SELECT VALUE COUNT(1)
    FROM Families f
    WHERE f.address.state = "WA"
```

結果は次のようになります。

```json
    [ 1 ]
```

## <a name="remarks"></a>解説

この集計システム関数は、[範囲インデックス](index-policy.md#includeexclude-strategy)の恩恵を受けます。 あるプロパティで `AVG`、`COUNT`、`MAX`、`MIN`、`SUM` を行うことが予想される場合、[インデックス作成ポリシーに関連パスを含めてください](index-policy.md#includeexclude-strategy)。

## <a name="next-steps"></a>次のステップ

- [Azure Cosmos DB の概要](introduction.md)
- [システム関数](sql-query-system-functions.md)
- [ユーザー定義関数](sql-query-udfs.md)