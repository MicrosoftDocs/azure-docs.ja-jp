---
title: Azure Cosmos DB クエリ言語の REPLACE
description: Azure Cosmos DB の SQL システム関数 REPLACE について説明します。
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 2cb7d82efd010fd7c3395a4f6a9217370d9e5779
ms.sourcegitcommit: 7f6d986a60eff2c170172bd8bcb834302bb41f71
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/27/2019
ms.locfileid: "71349585"
---
# <a name="replace-azure-cosmos-db"></a>REPLACE (Azure Cosmos DB)
 指定された文字列値のすべての出現箇所をもう 1 つの文字列値に置き換えます。  
  
## <a name="syntax"></a>構文
  
```sql
REPLACE(<str_expr1>, <str_expr2>, <str_expr3>)  
```  
  
## <a name="arguments"></a>引数
  
*str_expr1*  
   検索対象となる文字列式です。  
  
*str_expr2*  
   検出する文字列式です。  
  
*str_expr3*  
   *str_expr1*内で見つかった *str_expr2* を置きかえる文字列式です。  
  
## <a name="return-types"></a>戻り値の型
  
  文字列式を返します。  
  
## <a name="examples"></a>例
  
  次の例は、クエリ内での `REPLACE` の使用方法を示しています。  
  
```sql
SELECT REPLACE("This is a Test", "Test", "desk") AS replace 
```  
  
 結果セットは次のようになります。  
  
```json
[{"replace": "This is a desk"}]  
```  

## <a name="next-steps"></a>次の手順

- [Azure Cosmos DB の文字列関数](sql-query-string-functions.md)
- [Azure Cosmos DB のシステム関数](sql-query-system-functions.md)
- [Azure Cosmos DB の概要](introduction.md)
