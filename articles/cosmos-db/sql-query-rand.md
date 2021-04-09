---
title: Azure Cosmos DB クエリ言語の RAND
description: Azure Cosmos DB の SQL システム関数 RAND について説明します。
author: ginamr
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 09/16/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: fb3e310970fcc2146ee0d4b790a9744dcd566bad
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "93341656"
---
# <a name="rand-azure-cosmos-db"></a>RAND (Azure Cosmos DB)
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

 [0,1) からランダムに生成された数値を返します。
 
## <a name="syntax"></a>構文
  
```sql
RAND ()  
```  

## <a name="return-types"></a>戻り値の型

  数値式を返します。

## <a name="remarks"></a>解説

  `RAND` は非決定論的関数です。 `RAND` の繰り返し呼び出しでは、同じ結果が返されません。 このシステム関数では、インデックスは使用されません。


## <a name="examples"></a>例
  
  次の例では、ランダムに生成された数値を返します。
  
```sql
SELECT RAND() AS rand 
```  
  
 結果セットは次のようになります。  
  
```json
[{"rand": 0.87860053195618093}]  
``` 

## <a name="next-steps"></a>次のステップ

- [Azure Cosmos DB の数学関数](sql-query-mathematical-functions.md)
- [Azure Cosmos DB のシステム関数](sql-query-system-functions.md)
- [Azure Cosmos DB の概要](introduction.md)
