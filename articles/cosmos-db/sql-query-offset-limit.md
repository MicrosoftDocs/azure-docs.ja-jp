---
title: Azure Cosmos DB での OFFSET LIMIT 句
description: Azure Cosmos DB の OFFSET LIMIT 句について説明します。
author: timsander1
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 06/10/2019
ms.author: mjbrown
ms.openlocfilehash: 60ac28c80e9f7cc72f4d6005c12cb5f68671341e
ms.sourcegitcommit: a12b2c2599134e32a910921861d4805e21320159
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/24/2019
ms.locfileid: "67343279"
---
# <a name="offset-limit-clause"></a>OFFSET LIMIT 句

OFFSET LIMIT 句は、スキップした後、クエリからいくつかの値を取得するオプションの句です。 OFFSET LIMIT 句には、OFFSET の数と LIMIT の数が必要です。

OFFSET LIMIT を ORDER BY 句と組み合わせて使用した場合、スキップが実行され、順序付けられた値を受け取る結果セットが生成されます。 ORDER BY 句を使用しない場合、決定論的順序の値になります。

## <a name="syntax"></a>構文
  
```sql  
OFFSET <offset_amount> LIMIT <limit_amount>
```  
  
## <a name="arguments"></a>引数

- `<offset_amount>`

   クエリの結果でスキップする項目の数を整数で指定します。

- `<limit_amount>`
  
   クエリ結果に含める項目の数を整数で指定します。

## <a name="remarks"></a>解説
  
  OFFSET LIMIT 句では、OFFSET の数と LIMIT の数の両方が必要です。 省略可能な `ORDER BY` 句を使用すると、順序付けられた値に対してスキップを実行することで結果セットが生成されます。 そうでない場合、クエリでは、決められた順序の値が返されます。 現在、この句は単一パーティション内のクエリのみでサポートされており、クロス パーティション クエリではまだサポートしていません。

## <a name="examples"></a>例

たとえば、このクエリは、最初の値をスキップし、2 番目の値を返します (居住都市名の順序で)。

```sql
    SELECT f.id, f.address.city
    FROM Families f
    ORDER BY f.address.city
    OFFSET 1 LIMIT 1
```

結果は次のようになります。

```json
    [
      {
        "id": "AndersenFamily",
        "city": "Seattle"
      }
    ]
```

このクエリは、最初の値をスキップし、2 番目の値を返します (順序付けなし)。

```sql
   SELECT f.id, f.address.city
    FROM Families f
    OFFSET 1 LIMIT 1
```

結果は次のようになります。

```json
    [
      {
        "id": "WakefieldFamily",
        "city": "Seattle"
      }
    ]
```

## <a name="next-steps"></a>次の手順

- [使用の開始](sql-query-getting-started.md)
- [SELECT 句](sql-query-select.md)
- [ORDER BY 句](sql-query-order-by.md)
