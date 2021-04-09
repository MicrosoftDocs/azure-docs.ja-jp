---
title: Azure Cosmos DB クエリ言語の CONCAT
description: Azure Cosmos DB の CONCAT SQL システム関数で、2 つ以上の文字列値を連結した結果である文字列を返す方法について説明します。
author: ginamr
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 03/03/2020
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: f5fbbbdd9d29199f5b9bd173b6ab12d3d615943c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "93339195"
---
# <a name="concat-azure-cosmos-db"></a>CONCAT (Azure Cosmos DB)
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

 2 つ以上の文字列値を連結した結果である文字列を返します。  
  
## <a name="syntax"></a>構文
  
```sql
CONCAT(<str_expr1>, <str_expr2> [, <str_exprN>])  
```  
  
## <a name="arguments"></a>引数
  
*str_expr*  
   他の値に連結する文字列式です。 `CONCAT` 関数には、少なくとも 2 つの *str_expr* 引数が必要です。  
  
## <a name="return-types"></a>戻り値の型
  
  文字列式を返します。  
  
## <a name="examples"></a>例
  
  次の例では、指定した値の連結された文字列を返します。  
  
```sql
SELECT CONCAT("abc", "def") AS concat  
```  
  
 結果セットは次のようになります。  
  
```json
[{"concat": "abcdef"}]  
```  
  
## <a name="remarks"></a>解説

このシステム関数では、インデックスは使用されません。

## <a name="next-steps"></a>次のステップ

- [Azure Cosmos DB の文字列関数](sql-query-string-functions.md)
- [Azure Cosmos DB のシステム関数](sql-query-system-functions.md)
- [Azure Cosmos DB の概要](introduction.md)
