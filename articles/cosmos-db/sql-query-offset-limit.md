---
title: Azure Cosmos DB での OFFSET LIMIT 句
description: OFFSET LIMIT 句を使用して、 Azure Cosmos DB へのクエリを実行するときに特定の値をスキップして取得する方法について学習する
author: timsander1
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 07/29/2020
ms.author: tisande
ms.openlocfilehash: 459bd8511577067766cf488f53df57c1dc33fad1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "93338298"
---
# <a name="offset-limit-clause-in-azure-cosmos-db"></a>Azure Cosmos DB での OFFSET LIMIT 句
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

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
  
  `OFFSET LIMIT` 句には、`OFFSET` 数と `LIMIT` 数の両方が必要です。 省略可能な `ORDER BY` 句を使用すると、順序付けられた値に対してスキップを実行することで結果セットが生成されます。 そうでない場合、クエリでは、決められた順序の値が返されます。

  `OFFSET LIMIT` を使用するクエリの RU 料金は、オフセットされる用語の数が増えるにつれて増加します。 [複数の結果ページ](sql-query-pagination.md)があるクエリの場合は、通常、[継続トークン](sql-query-pagination.md#continuation-tokens)を使用することをお勧めします。 継続トークンとは、後でクエリを再開できる場所の "ブックマーク" です。 `OFFSET LIMIT` を使用する場合、"ブックマーク" はありません。 クエリの次のページを返す場合は、最初から開始する必要があります。
  
  項目を完全にスキップしてクライアント リソースを節約したい場合は、`OFFSET LIMIT` を使用してください。 たとえば、1000 番目のクエリ結果にスキップし、1 から 999 の結果は表示する必要がない場合は、`OFFSET LIMIT` を使用します。 バックエンドでは、`OFFSET LIMIT` は引き続き、スキップされた項目を含めて各項目を読み込みます。 パフォーマンス上の利点は、不要な項目の処理を回避することによって、クライアント リソースが節約されることです。

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

## <a name="next-steps"></a>次のステップ

- [作業の開始](sql-query-getting-started.md)
- [SELECT 句](sql-query-select.md)
- [ORDER BY 句](sql-query-order-by.md)
