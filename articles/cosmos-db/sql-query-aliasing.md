---
title: Azure Cosmos DB でのエイリアス化
description: Azure Cosmos DB SQL クエリ内の値のエイリアス化について説明します
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 06/20/2019
ms.author: mjbrown
ms.openlocfilehash: e532fb7180af8a21de6ae9a2e4d798abd9e93e7b
ms.sourcegitcommit: a12b2c2599134e32a910921861d4805e21320159
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/24/2019
ms.locfileid: "67343250"
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

- [Azure Cosmos DB .NET のサンプル](https://github.com/Azure/azure-cosmosdb-dotnet)
- [SELECT 句](sql-query-select.md)
- [FROM 句](sql-query-from.md)
