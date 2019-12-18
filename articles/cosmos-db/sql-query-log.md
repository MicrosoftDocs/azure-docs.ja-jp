---
title: Azure Cosmos DB クエリ言語の LOG
description: 指定された数値式の自然対数を返す Azure Cosmos DB の LOG SQL システム関数について学ぶ
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: ae7812670da836efa326b9224547e4d1b64374c2
ms.sourcegitcommit: 9405aad7e39efbd8fef6d0a3c8988c6bf8de94eb
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/05/2019
ms.locfileid: "74873286"
---
# <a name="log-azure-cosmos-db"></a>LOG (Azure Cosmos DB)
 指定された数値式の自然対数を返します。  
  
## <a name="syntax"></a>構文
  
```sql
LOG (<numeric_expr> [, <base>])  
```  
  
## <a name="arguments"></a>引数
  
*numeric_expr*  
   数値式です。  
  
*base*  
   対数の底を設定する省略可能な数値引数。  
  
## <a name="return-types"></a>戻り値の型
  
  数値式を返します。  
  
## <a name="remarks"></a>解説
  
  既定では、LOG() は自然対数を返します。 省略可能な底パラメーターを使用して、対数の底を別の値に変更できます。  
  
  自然対数は、底 **e** に対する自然対数です。ここで、**e** は、2.718281828 にほぼ等しい無理定数です。  
  
  数値の指数の自然対数は数自体です:LOG( EXP( n ) ) = n。 数値の自然対数の指数は数自体です:EXP( LOG( n ) ) = n。  
  
## <a name="examples"></a>例
  
  次の例では、変数を宣言し、指定した変数 (10) の対数値を返します。  
  
```sql
SELECT LOG(10) AS log  
```  
  
 結果セットは次のようになります。  
  
```json
[{log: 2.3025850929940459}]  
```  
  
 次の例では、数値の指数の `LOG` を計算します。  
  
```sql
SELECT EXP(LOG(10)) AS expLog  
```  
  
 結果セットは次のようになります。  
  
```json
[{expLog: 10.000000000000002}]  
```  

## <a name="next-steps"></a>次の手順

- [Azure Cosmos DB の数学関数](sql-query-mathematical-functions.md)
- [Azure Cosmos DB のシステム関数](sql-query-system-functions.md)
- [Azure Cosmos DB の概要](introduction.md)
