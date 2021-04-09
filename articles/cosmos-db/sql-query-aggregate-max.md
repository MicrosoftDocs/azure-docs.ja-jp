---
title: Azure Cosmos DB クエリ言語の MAX
description: Azure Cosmos DB の Max (MAX) SQL システム関数について説明します。
author: timsander1
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 12/02/2020
ms.author: tisande
ms.custom: query-reference
ms.openlocfilehash: a3d8e3f2687a492461bdbbdd761e26cdb58f9e96
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "96550580"
---
# <a name="max-azure-cosmos-db"></a>MAX (Azure Cosmos DB)
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

この集計関数では、式内の値の最大値が返されます。
  
## <a name="syntax"></a>構文
  
```sql
MAX(<scalar_expr>)  
```  
  
## <a name="arguments"></a>引数

*scalar_expr*  
   スカラー式です。 
  
## <a name="return-types"></a>戻り値の型
  
スカラー式を返します。  
  
## <a name="examples"></a>例
  
次の例では、`propertyA` の最大値が返されます。
  
```sql
SELECT MAX(c.propertyA)
FROM c
```  

## <a name="remarks"></a>解説

このシステム関数は、[範囲インデックス](index-policy.md#includeexclude-strategy)の恩恵を受けます。 `MAX` の引数には、数値、文字列、ブール値、または null 値を指定できます。 未定義の値はすべて無視されます。

異なる種類のデータを比較する場合は、次の優先順位が使用されます (降順)。

- 文字列
- 数値
- boolean
- null

## <a name="next-steps"></a>次のステップ

- [Azure Cosmos DB の数学関数](sql-query-mathematical-functions.md)
- [Azure Cosmos DB のシステム関数](sql-query-system-functions.md)
- [Azure Cosmos DB の集計関数](sql-query-aggregate-functions.md)