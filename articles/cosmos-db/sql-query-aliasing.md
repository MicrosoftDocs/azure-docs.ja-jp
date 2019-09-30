---
title: Azure Cosmos DB でのエイリアス化
description: Azure Cosmos DB SQL クエリ内の値のエイリアス化について説明します
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 06/20/2019
ms.author: mjbrown
ms.openlocfilehash: 3b17cbc7710647b1e1875025a1db1849034ec1dc
ms.sourcegitcommit: e97a0b4ffcb529691942fc75e7de919bc02b06ff
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/15/2019
ms.locfileid: "71002085"
---
# <a name="aliasing-in-azure-cosmos-db"></a>Azure Cosmos DB でのエイリアス化

クエリ内の値を明示的にエイリアス化できます。 クエリに同じ名前を持つ 2 つのプロパティがある場合、エイリアス化を使ってプロパティのいずれかまたは両方の名前を変更することで、プロジェクションの結果でこれらを区別できます。

## <a name="examples"></a>例

2 つ目の値を `NameInfo` としてプロジェクションしている場合の次の例に示すように、エイリアス化に使用する AS キーワードはオプションです。

```sql
    SELECT 
           { "state": f.address.state, "city": f.address.city } AS AddressInfo,
           { "name": f.id } NameInfo
    FROM Families f
    WHERE f.id = "AndersenFamily"
```

結果は次のようになります。

```json
    [{
      "AddressInfo": {
        "state": "WA",
        "city": "Seattle"
      },
      "NameInfo": {
        "name": "AndersenFamily"
      }
    }]
```

## <a name="next-steps"></a>次の手順

- [Azure Cosmos DB .NET のサンプル](https://github.com/Azure/azure-cosmos-dotnet-v3)
- [SELECT 句](sql-query-select.md)
- [FROM 句](sql-query-from.md)
