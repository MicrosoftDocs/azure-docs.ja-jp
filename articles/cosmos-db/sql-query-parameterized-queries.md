---
title: Azure Cosmos DB のパラメーター化されたクエリ
description: SQL のパラメーター化されたクエリについて説明します
author: timsander1
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/30/2019
ms.author: tisande
ms.openlocfilehash: 2bfc22346c1dd43d7d3c2937ffc286e48ae774d0
ms.sourcegitcommit: a12b2c2599134e32a910921861d4805e21320159
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/24/2019
ms.locfileid: "67343144"
---
# <a name="parameterized-queries-in-azure-cosmos-db"></a>Azure Cosmos DB のパラメーター化されたクエリ

Cosmos DB では、使い慣れた @ 表記で表されたパラメーターを使用するクエリがサポートされます。 パラメーター化された SQL により、ユーザーの入力を堅牢に処理し、流用して、SQL インジェクションによってデータが誤って開示されるリスクを回避することができます。

## <a name="examples"></a>例

たとえば、パラメーターとして `lastName` と `address.state` を使用するクエリを記述し、ユーザーの入力に基づいて、`lastName` と `address.state` にさまざまな値を指定して実行できます。

```sql
    SELECT *
    FROM Families f
    WHERE f.lastName = @lastName AND f.address.state = @addressState
```

次に、この要求は、次のように、パラメーター化された JSON クエリとして Cosmos DB に送信できます。

```sql
    {
        "query": "SELECT * FROM Families f WHERE f.lastName = @lastName AND f.address.state = @addressState",
        "parameters": [
            {"name": "@lastName", "value": "Wakefield"},
            {"name": "@addressState", "value": "NY"},
        ]
    }
```

次の例では、パラメーター化されたクエリで TOP 引数を設定しています。 

```sql
    {
        "query": "SELECT TOP @n * FROM Families",
        "parameters": [
            {"name": "@n", "value": 10},
        ]
    }
```

パラメーターの値には、有効な任意の JSON (文字列、数値、ブール値、null、配列や入れ子になった JSON も含む) を指定できます。 Cosmos DB はスキーマレスであるため、パラメーターはどの型に対しても検証されません。


## <a name="next-steps"></a>次の手順

- [Azure Cosmos DB .NET のサンプル](https://github.com/Azure/azure-cosmosdb-dotnet)
- [モデル ドキュメント データ](modeling-data.md)
