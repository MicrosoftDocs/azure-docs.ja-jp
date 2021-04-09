---
title: Azure Cosmos DB クエリ言語の StringToObject
description: Azure Cosmos DB の SQL システム関数 StringToObject について説明します。
author: ginamr
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 03/03/2020
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 369c86c8fc2ef4d125c3f13db4eb3af88b7e726e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "93337848"
---
# <a name="stringtoobject-azure-cosmos-db"></a>StringToObject (Azure Cosmos DB)
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

 オブジェクトに変換された式を返します。 式を変換できない場合は、undefined を返します。  
  
## <a name="syntax"></a>構文
  
```sql
StringToObject(<str_expr>)  
```  
  
## <a name="arguments"></a>引数
  
*str_expr*  
   JSON オブジェクト式として解析される文字列式です。 入れ子になった文字列値を有効にするには二重引用符で囲む必要があることにご注意ください。 JSON 形式の詳細については、「[json.org](https://json.org/)」をご覧ください。  
  
## <a name="return-types"></a>戻り値の型
  
  オブジェクト式または undefined を返します。  
  
## <a name="examples"></a>例
  
  次の例は、異なる型間で `StringToObject` がどのように動作するかを示します。 
  
 有効な入力を使用した例を次に示します。

```sql
SELECT 
    StringToObject("{}") AS obj1, 
    StringToObject('{"A":[1,2,3]}') AS obj2,
    StringToObject('{"B":[{"b1":[5,6,7]},{"b2":8},{"b3":9}]}') AS obj3, 
    StringToObject("{\"C\":[{\"c1\":[5,6,7]},{\"c2\":8},{\"c3\":9}]}") AS obj4
``` 

結果セットは次のようになります。

```json
[{"obj1": {}, 
  "obj2": {"A": [1,2,3]}, 
  "obj3": {"B":[{"b1":[5,6,7]},{"b2":8},{"b3":9}]},
  "obj4": {"C":[{"c1":[5,6,7]},{"c2":8},{"c3":9}]}}]
```

 無効な入力を使用した例を次に示します。
クエリ内では有効であっても、有効なオブジェクトとして解析されません。 オブジェクト文字列内の文字列は、"{\\"a\\":\\"str\\"}" のようにエスケープするか、'{"a": "str"}' のように一重引用符で囲む必要があります。

一重引用符で囲んだプロパティ名は有効な JSON ではありません。

```sql
SELECT 
    StringToObject("{'a':[1,2,3]}")
```

結果セットは次のようになります。

```json
[{}]
```  

引用符で囲まれていないプロパティ名は有効な JSON ではありません。

```sql
SELECT 
    StringToObject("{a:[1,2,3]}")
```

結果セットは次のようになります。

```json
[{}]
``` 

無効な入力を使用した例を次に示します。

 渡された式は JSON オブジェクトとして解析されます。これらの入力はオブジェクト型として評価されないため、undefined が返されます。

```sql
SELECT 
    StringToObject("}"),
    StringToObject("{"),
    StringToObject("1"),
    StringToObject(NaN), 
    StringToObject(false), 
    StringToObject(undefined)
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
