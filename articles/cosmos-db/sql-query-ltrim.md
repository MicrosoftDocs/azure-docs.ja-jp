---
title: Azure Cosmos DB クエリ言語の LTRIM
description: Azure Cosmos DB での SQL システム関数 LTRIM について学習します。
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: bbc9c09bd97b02a323e01c0b0d72ca1df3d13b15
ms.sourcegitcommit: 7f6d986a60eff2c170172bd8bcb834302bb41f71
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/27/2019
ms.locfileid: "71349689"
---
# <a name="ltrim-azure-cosmos-db"></a>LTRIM (Azure Cosmos DB)
 文字列式の先頭の空白を削除して返します。  
  
## <a name="syntax"></a>構文
  
```sql
LTRIM(<str_expr>)  
```  
  
## <a name="arguments"></a>引数
  
*str_expr*  
   文字列式です。  
  
## <a name="return-types"></a>戻り値の型
  
  文字列式を返します。  
  
## <a name="examples"></a>例
  
  次の例は、クエリ内での `LTRIM` の使用方法を示しています。  
  
```sql
SELECT LTRIM("  abc") AS l1, LTRIM("abc") AS l2, LTRIM("abc   ") AS l3 
```  
  
 結果セットは次のようになります。  
  
```json
[{"l1": "abc", "l2": "abc", "l3": "abc   "}]  
```  

## <a name="next-steps"></a>次の手順

- [文字列関数 (Azure Cosmos DB)](sql-query-string-functions.md)
- [システム関数 (Azure Cosmos DB)](sql-query-system-functions.md)
- [Azure Cosmos DB の概要](introduction.md)
