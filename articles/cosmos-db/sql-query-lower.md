---
title: Azure Cosmos DB クエリ言語の LOWER
description: Azure Cosmos DB での SQL システム関数 LOWER について学習します。
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 3a9c122ef65772458b832d3b1651e7e63e42985e
ms.sourcegitcommit: 7f6d986a60eff2c170172bd8bcb834302bb41f71
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/27/2019
ms.locfileid: "71349707"
---
# <a name="lower-azure-cosmos-db"></a>LOWER (Azure Cosmos DB)
 文字列式の大文字データを小文字に変換して返します。  
  
## <a name="syntax"></a>構文
  
```sql
LOWER(<str_expr>)  
```  
  
## <a name="arguments"></a>引数
  
*str_expr*  
   文字列式です。  
  
## <a name="return-types"></a>戻り値の型
  
  文字列式を返します。  
  
## <a name="examples"></a>例
  
  次の例は、クエリ内での `LOWER` の使用方法を示しています。  
  
```sql
SELECT LOWER("Abc") AS lower
```  
  
 結果セットは次のようになります。  
  
```json
[{"lower": "abc"}]  
  
```  

## <a name="next-steps"></a>次の手順

- [文字列関数 (Azure Cosmos DB)](sql-query-string-functions.md)
- [システム関数 (Azure Cosmos DB)](sql-query-system-functions.md)
- [Azure Cosmos DB の概要](introduction.md)
