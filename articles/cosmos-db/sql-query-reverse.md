---
title: Azure Cosmos DB クエリ言語の REVERSE
description: Azure Cosmos DB の SQL システム関数 REVERSE について説明します。
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 8237918232bd8ba8edb2b8f71440ffd73a913334
ms.sourcegitcommit: 7f6d986a60eff2c170172bd8bcb834302bb41f71
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/27/2019
ms.locfileid: "71349545"
---
# <a name="reverse-azure-cosmos-db"></a>REVERSE (Azure Cosmos DB)
 文字列値の順序を逆にして返します。  
  
## <a name="syntax"></a>構文
  
```sql
REVERSE(<str_expr>)  
```  
  
## <a name="arguments"></a>引数
  
*str_expr*  
   文字列式です。  
  
## <a name="return-types"></a>戻り値の型
  
  文字列式を返します。  
  
## <a name="examples"></a>例
  
  次の例は、クエリ内での `REVERSE` の使用方法を示しています。  
  
```sql
SELECT REVERSE("Abc") AS reverse  
```  
  
 結果セットは次のようになります。  
  
```json
[{"reverse": "cbA"}]  
```  

## <a name="next-steps"></a>次の手順

- [Azure Cosmos DB の文字列関数](sql-query-string-functions.md)
- [Azure Cosmos DB のシステム関数](sql-query-system-functions.md)
- [Azure Cosmos DB の概要](introduction.md)
