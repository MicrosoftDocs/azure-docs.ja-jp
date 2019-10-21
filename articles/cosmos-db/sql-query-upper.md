---
title: Azure Cosmos DB クエリ言語の UPPER
description: Azure Cosmos DB での SQL システム関数 UPPER について説明します。
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 6b0f025948803a23c5b3c8bb6415c0e111b946b2
ms.sourcegitcommit: 7f6d986a60eff2c170172bd8bcb834302bb41f71
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/27/2019
ms.locfileid: "71349043"
---
# <a name="upper-azure-cosmos-db"></a>UPPER (Azure Cosmos DB)
 文字列式の小文字データを大文字に変換して返します。  
  
## <a name="syntax"></a>構文
  
```sql
UPPER(<str_expr>)  
```  
  
## <a name="arguments"></a>引数
  
*str_expr*  
   文字列式です。  
  
## <a name="return-types"></a>戻り値の型
  
  文字列式を返します。  
  
## <a name="examples"></a>例
  
  次の例は、クエリ内での `UPPER` の使用方法を示しています  
  
```sql
SELECT UPPER("Abc") AS upper  
```  
  
 結果セットは次のようになります。  
  
```json
[{"upper": "ABC"}]  
```

## <a name="next-steps"></a>次の手順

- [Azure Cosmos DB の文字列関数](sql-query-string-functions.md)
- [Azure Cosmos DB のシステム関数](sql-query-system-functions.md)
- [Azure Cosmos DB の概要](introduction.md)
