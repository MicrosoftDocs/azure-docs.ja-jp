---
title: Azure Cosmos DB クエリ言語の StringToNumber
description: Azure Cosmos DB の SQL システム関数 StringToNumber について学習します。
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 8b9596738d9b02fa26f9c363287323b905654a1f
ms.sourcegitcommit: 7f6d986a60eff2c170172bd8bcb834302bb41f71
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/27/2019
ms.locfileid: "71349227"
---
# <a name="stringtonumber-azure-cosmos-db"></a>StringToNumber (Azure Cosmos DB)
 数値に変換された式を返します。 式を変換できない場合は、undefined を返します。  
  
## <a name="syntax"></a>構文
  
```sql
StringToNumber(<str_expr>)  
```  
  
## <a name="arguments"></a>引数
  
*str_expr*  
   JSON 数値式として解析される文字列式です。 JSON の数値は整数または浮動小数点にする必要があります。 JSON 形式の詳細については、「[json.org](https://json.org/)」をご覧ください。  
  
## <a name="return-types"></a>戻り値の型
  
  数値式または undefined を返します。  
  
## <a name="examples"></a>例
  
  次の例では、異なる型間で `StringToNumber` がどのように動作するかを示します。 

空白は数値の前後のみで使用できます。

```sql
SELECT 
    StringToNumber("1.000000") AS num1, 
    StringToNumber("3.14") AS num2,
    StringToNumber("   60   ") AS num3, 
    StringToNumber("-1.79769e+308") AS num4
```  
  
 結果セットは次のようになります。  
  
```json
{{"num1": 1, "num2": 3.14, "num3": 60, "num4": -1.79769e+308}}
```  

JSON で有効な数値は、整数または浮動小数点数である必要があります。

```sql
SELECT   
    StringToNumber("0xF")
```  
  
 結果セットは次のようになります。  
  
```json
{{}}
```  

渡された式は数値式として解析されます。これらの入力は数値型として評価されないため、undefined が返されます。 

```sql
SELECT 
    StringToNumber("99     54"),   
    StringToNumber(undefined),
    StringToNumber("false"),
    StringToNumber(false),
    StringToNumber(" "),
    StringToNumber(NaN)
```  
  
 結果セットは次のようになります。  
  
```json
{{}}
```  

## <a name="next-steps"></a>次の手順

- [Azure Cosmos DB の文字列関数](sql-query-string-functions.md)
- [Azure Cosmos DB のシステム関数](sql-query-system-functions.md)
- [Azure Cosmos DB の概要](introduction.md)
