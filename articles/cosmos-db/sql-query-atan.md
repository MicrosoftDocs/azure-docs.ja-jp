---
title: Azure Cosmos DB クエリ言語の ATAN
description: Azure Cosmos DB の SQL システム関数 ATAN について説明します。
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 4ee3867f3a4938358e8d61aa8a98f747756ee3e8
ms.sourcegitcommit: 7f6d986a60eff2c170172bd8bcb834302bb41f71
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/27/2019
ms.locfileid: "71348564"
---
# <a name="atan-azure-cosmos-db"></a>ATAN (Azure Cosmos DB)
 タンジェントが指定された数値式となる角度をラジアン単位で返します。 アークタンジェントとも呼ばれます。  
  
## <a name="syntax"></a>構文
  
```sql
ATAN(<numeric_expr>)  
```  
  
## <a name="arguments"></a>引数
  
*numeric_expr*  
   数値式です。  
  
## <a name="return-types"></a>戻り値の型
  
  数値式を返します。  
  
## <a name="examples"></a>例
  
  次の例では、指定した値の `ATAN` を返します。  
  
```sql
SELECT ATAN(-45.01) AS atan  
```  
  
 結果セットは次のようになります。  
  
```json
[{"atan": -1.5485826962062663}]  
```  
  

## <a name="next-steps"></a>次の手順

- [Azure Cosmos DB の数学関数](sql-query-mathematical-functions.md)
- [Azure Cosmos DB のシステム関数](sql-query-system-functions.md)
- [Azure Cosmos DB の概要](introduction.md)
