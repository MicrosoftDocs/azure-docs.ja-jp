---
title: Azure Cosmos DB クエリ言語での StringToBoolean
description: Azure Cosmos DB の SQL システム関数 StringToBoolean について説明します。
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 8719857dca16585a045f8174dbac8df455f38f38
ms.sourcegitcommit: 7f6d986a60eff2c170172bd8bcb834302bb41f71
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/27/2019
ms.locfileid: "71349258"
---
# <a name="stringtoboolean-azure-cosmos-db"></a>StringToBoolean (Azure Cosmos DB)
 ブール値に変換された式を返します。 式を変換できない場合は、undefined を返します。  
  
## <a name="syntax"></a>構文
  
```sql
StringToBoolean(<str_expr>)  
```  
  
## <a name="arguments"></a>引数
  
*str_expr*  
   ブール式として解析される文字列式です。  
  
## <a name="return-types"></a>戻り値の型
  
  ブール式または undefined を返します。  
  
## <a name="examples"></a>例
  
  次の例は、異なる型間で `StringToBoolean` がどのように動作するかを示します。 
 
 有効な入力を使用した例を次に示します。

空白は "true" または "false" の前後のみで使用できます。

```sql
SELECT 
    StringToBoolean("true") AS b1, 
    StringToBoolean("    false") AS b2,
    StringToBoolean("false    ") AS b3
```  
  
 結果セットは次のようになります。  
  
```json
[{"b1": true, "b2": false, "b3": false}]
```  

無効な入力を使用した例を次に示します。

 ブール値では大文字と小文字が区別されるので、すべて小文字で記述する必要があります (つまり、"true" と "false")。

```sql
SELECT 
    StringToBoolean("TRUE"),
    StringToBoolean("False")
```  

結果セットは次のようになります。  
  
```json
[{}]
``` 

渡された式はブール式として解析されます。これらの入力はブール型として評価されないため、undefined が返されます。

```sql
SELECT 
    StringToBoolean("null"),
    StringToBoolean(undefined),
    StringToBoolean(NaN), 
    StringToBoolean(false), 
    StringToBoolean(true)
```  

結果セットは次のようになります。  
  
```json
[{}]
```  

## <a name="next-steps"></a>次の手順

- [Azure Cosmos DB の文字列関数](sql-query-string-functions.md)
- [Azure Cosmos DB のシステム関数](sql-query-system-functions.md)
- [Azure Cosmos DB の概要](introduction.md)
