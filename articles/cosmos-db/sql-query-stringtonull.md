---
title: Azure Cosmos DB クエリ言語の StringToNull
description: Azure Cosmos DB の SQL システム関数 StringToNull について説明します。
author: ginamr
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 03/03/2020
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: ee91ca3a32bacbf6590877f49bf74499fc00a8f9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "93339583"
---
# <a name="stringtonull-azure-cosmos-db"></a>StringToNull (Azure Cosmos DB)
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

 null 値に変換された式を返します。 式を変換できない場合は、undefined を返します。  
  
## <a name="syntax"></a>構文
  
```sql
StringToNull(<str_expr>)  
```  
  
## <a name="arguments"></a>引数
  
*str_expr*  
   null 式として解析される文字列式です。
  
## <a name="return-types"></a>戻り値の型
  
  null 式または undefined を返します。  
  
## <a name="examples"></a>例
  
  次の例は、異なる型間で `StringToNull` がどのように動作するかを示します。 

有効な入力を使用した例を次に示します。

 空白は "null" の前後のみで使用できます。

```sql
SELECT 
    StringToNull("null") AS n1, 
    StringToNull("  null ") AS n2,
    IS_NULL(StringToNull("null   ")) AS n3
```  
  
 結果セットは次のようになります。  
  
```json
[{"n1": null, "n2": null, "n3": true}]
```  

無効な入力を使用した例を次に示します。

null 値では大文字と小文字が区別されるので、すべて小文字で記述する必要があります (つまり、"null")。

```sql
SELECT    
    StringToNull("NULL"),
    StringToNull("Null")
```  
  
 結果セットは次のようになります。  
  
```json
[{}]
```  

渡された式は null 式として解析されます。これらの入力は null 型として評価されないため、undefined が返されます。

```sql
SELECT    
    StringToNull("true"), 
    StringToNull(false), 
    StringToNull(undefined),
    StringToNull(NaN) 
```  
  
 結果セットは次のようになります。  
  
```json
[{}]
```  

## <a name="remarks"></a>解説

このシステム関数では、インデックスは使用されません。

## <a name="next-steps"></a>次のステップ

- [Azure Cosmos DB の文字列関数](sql-query-string-functions.md)
- [Azure Cosmos DB のシステム関数](sql-query-system-functions.md)
- [Azure Cosmos DB の概要](introduction.md)
