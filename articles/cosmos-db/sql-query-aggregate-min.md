---
title: Azure Cosmos DB クエリ言語の MIN
description: Azure Cosmos DB の Min (MIN) SQL システム関数について説明します。
author: timsander1
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 12/02/2020
ms.author: tisande
ms.custom: query-reference
ms.openlocfilehash: 679814822cca5a72bd261d3c8944863715e31f70
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "96551076"
---
# <a name="min-azure-cosmos-db"></a>MIN (Azure Cosmos DB)
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

この集計関数では、式内の値の最小値が返されます。
  
## <a name="syntax"></a>構文
  
```sql
MIN(<scalar_expr>)  
```  
  
## <a name="arguments"></a>引数
  
*scalar_expr*  
   スカラー式です。 
  
## <a name="return-types"></a>戻り値の型
  
スカラー式を返します。  
  
## <a name="examples"></a>例
  
次の例では、`propertyA` の最小値が返されます。
  
```sql
SELECT MIN(c.propertyA)
FROM c
```  

## <a name="remarks"></a>解説

このシステム関数は、[範囲インデックス](index-policy.md#includeexclude-strategy)の恩恵を受けます。 `MIN` の引数には、数値、文字列、ブール値、または null 値を指定できます。 未定義の値はすべて無視されます。

異なる種類のデータを比較する場合は、次の優先順位が使用されます (昇順)。

- null
- boolean
- number
- string

## <a name="next-steps"></a>次のステップ

- [Azure Cosmos DB の数学関数](sql-query-mathematical-functions.md)
- [Azure Cosmos DB のシステム関数](sql-query-system-functions.md)
- [Azure Cosmos DB の集計関数](sql-query-aggregate-functions.md)