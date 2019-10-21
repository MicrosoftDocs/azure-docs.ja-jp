---
title: Azure Cosmos DB クエリ言語の ASIN
description: Azure Cosmos DB の SQL システム関数 ASIN について説明します。
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 5a64fc8d8f87d38f001bf2bc9dd581692c97fe64
ms.sourcegitcommit: 7f6d986a60eff2c170172bd8bcb834302bb41f71
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/27/2019
ms.locfileid: "71348493"
---
# <a name="asin-azure-cosmos-db"></a>ASIN (Azure Cosmos DB)
 サインが指定された数値式となる角度をラジアン単位で返します。 アークサインとも呼ばれます。  
  
## <a name="syntax"></a>構文
  
```sql
ASIN(<numeric_expr>)  
```  
  
## <a name="arguments"></a>引数
  
*numeric_expr*  
   数値式です。  
  
## <a name="return-types"></a>戻り値の型
  
  数値式を返します。  
  
## <a name="examples"></a>例
  
  次の例では、-1 の `ASIN` を返します。  
  
```sql
SELECT ASIN(-1) AS asin  
```  
  
 結果セットは次のようになります。  
  
```json
[{"asin": -1.5707963267948966}]  
```  

## <a name="next-steps"></a>次の手順

- [Azure Cosmos DB の数学関数](sql-query-mathematical-functions.md)
- [Azure Cosmos DB のシステム関数](sql-query-system-functions.md)
- [Azure Cosmos DB の概要](introduction.md)
