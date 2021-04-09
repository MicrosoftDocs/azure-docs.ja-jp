---
title: Azure Cosmos DB のパラメーター化されたクエリ
description: パラメーター化された SQL クエリによってユーザー入力の堅牢な処理とエスケープ処理が提供され、SQL インジェクションによってデータが誤って開示されるリスクを回避する方法について説明します。
author: timsander1
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 07/29/2020
ms.author: tisande
ms.openlocfilehash: dc32aab89e50b500001fd2267f62e3031154be62
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "96549159"
---
# <a name="parameterized-queries-in-azure-cosmos-db"></a>Azure Cosmos DB のパラメーター化されたクエリ
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

Azure Cosmos DB では、使い慣れた @ 表記で表されたパラメーターを使用するクエリがサポートされます。 パラメーター化された SQL により、ユーザーの入力を堅牢に処理し、流用して、SQL インジェクションによってデータが誤って開示されるリスクを回避することができます。

## <a name="examples"></a>例

たとえば、パラメーターとして `lastName` と `address.state` を使用するクエリを記述し、ユーザーの入力に基づいて、`lastName` と `address.state` にさまざまな値を指定して実行できます。

```sql
    SELECT *
    FROM Families f
    WHERE f.lastName = @lastName AND f.address.state = @addressState
```

さらに、次のように、パラメーター化された JSON クエリとしてこの要求を Azure Cosmos DB に送信できます。

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

パラメーターの値には、有効な任意の JSON (文字列、数値、ブール値、null、配列や入れ子になった JSON も含む) を指定できます。 Azure Cosmos DB はスキーマレスであるため、パラメーターはどの型に対しても検証されません。

各 Azure Cosmos DB SDK のパラメーター化されたクエリの例を次に示します。

- [.NET SDK](https://github.com/Azure/azure-cosmos-dotnet-v3/blob/master/Microsoft.Azure.Cosmos.Samples/Usage/Queries/Program.cs#L195)
- [Java](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/main/src/main/java/com/azure/cosmos/examples/queries/sync/QueriesQuickstart.java#L392-L421)
- [Node.js](https://github.com/Azure/azure-cosmos-js/blob/master/samples/ItemManagement.ts#L58-L79)
- [Python](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/cosmos/azure-cosmos/samples/document_management.py#L66-L78)

## <a name="next-steps"></a>次のステップ

- [Azure Cosmos DB .NET のサンプル](https://github.com/Azure/azure-cosmos-dotnet-v3)
- [モデル ドキュメント データ](modeling-data.md)
