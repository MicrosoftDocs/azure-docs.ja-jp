---
title: Azure Cosmos DB クエリ言語の ACOS
description: Azure Cosmos DB の ACOS（arccosice）SQL システム関数が、コサインが指定された数値式である角度をラジアンで返す方法について学習する
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: fd5baa3ffefb7039c40706253e633661a51150f2
ms.sourcegitcommit: 9405aad7e39efbd8fef6d0a3c8988c6bf8de94eb
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/05/2019
ms.locfileid: "74873524"
---
# <a name="acos-azure-cosmos-db"></a>ACOS (Azure Cosmos DB)
 コサインが指定された数値式となる角度をラジアン単位で返します。アークコサインとも呼ばれます。  
  
## <a name="syntax"></a>構文
  
```sql
ACOS(<numeric_expr>)  
```  
  
## <a name="arguments"></a>引数
  
*numeric_expr*  
   数値式です。  
  
## <a name="return-types"></a>戻り値の型
  
  数値式を返します。  
  
## <a name="examples"></a>例
  
  次の例では、-1 の `ACOS` を返します。  
  
```sql
SELECT ACOS(-1) AS acos 
```  
  
 結果セットは次のようになります。  
  
```json
[{"acos": 3.1415926535897931}]  
```  

## <a name="next-steps"></a>次の手順

- [Azure Cosmos DB の数学関数](sql-query-mathematical-functions.md)
- [Azure Cosmos DB のシステム関数](sql-query-system-functions.md)
- [Azure Cosmos DB の概要](introduction.md)
