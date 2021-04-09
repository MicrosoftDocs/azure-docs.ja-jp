---
title: Azure Cosmos DB クエリ言語の EXP
description: 指定された数値式の指数値を返す、Azure Cosmos DB の指数 (EXP) SQL システム関数について説明します
author: ginamr
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: adf80d36edbe8f9a5535e8fa04501918a62ea7ca
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "93335634"
---
# <a name="exp-azure-cosmos-db"></a>EXP (Azure Cosmos DB)
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

 指定された数値式の指数値を返します。  
  
## <a name="syntax"></a>構文
  
```sql
EXP (<numeric_expr>)  
```  
  
## <a name="arguments"></a>引数
  
*numeric_expr*  
   数値式です。  
  
## <a name="return-types"></a>戻り値の型
  
  数値式を返します。  
  
## <a name="remarks"></a>解説
  
  定数 **e** (2.718281...) は、自然対数の底です。  
  
  数値の指数部は、定数 **e** を数値で累乗した値です。 たとえば、EXP(1.0) = e^1.0 = 2.71828182845905 および EXP(10) = e^10 = 22026.4657948067 になります。  
  
  数値の自然対数の指数は数自体です: EXP (LOG (n)) = n。 数値の指数の自然対数は数自体です: LOG (EXP (n)) = n。  
  
## <a name="examples"></a>例
  
  次の例では、変数を宣言し、指定した変数 (10) の指数値を返します。  
  
```sql
SELECT EXP(10) AS exp  
```  
  
 結果セットは次のようになります。  
  
```json
[{exp: 22026.465794806718}]  
```  
  
 次の例では、自然対数 20 の指数値、および指数 20 の自然対数を返します。 これらの関数は互いの逆関数であるため、どちらの場合も浮動小数点計算に丸めを適用した戻り値は 20 です。  
  
```sql
SELECT EXP(LOG(20)) AS exp1, LOG(EXP(20)) AS exp2  
```  
  
 結果セットは次のようになります。  
  
```json
[{exp1: 19.999999999999996, exp2: 20}]  
```  

## <a name="next-steps"></a>次のステップ

- [Azure Cosmos DB の数学関数](sql-query-mathematical-functions.md)
- [Azure Cosmos DB のシステム関数](sql-query-system-functions.md)
- [Azure Cosmos DB の概要](introduction.md)
