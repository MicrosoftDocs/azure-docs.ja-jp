---
title: Azure Cosmos DB の SQL 定数
description: Azure Cosmos DB 内の SQL クエリ定数を使用して特定のデータ値を表す方法について説明します。
author: timsander1
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 05/31/2019
ms.author: tisande
ms.openlocfilehash: b0973dca980b65b85ac66cc1b3c9ed45f0f9c779
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "93339206"
---
# <a name="azure-cosmos-db-sql-query-constants"></a>Azure Cosmos DB の SQL クエリ定数  
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

 リテラル値またはスカラー値としても知られる定数は、特定のデータ値を表す記号です。 定数の形式は、その定数が表す値のデータ型に依存します。  
  
 **サポートされるスカラー データ型:**  
  
|**Type**|**値の順序**|  
|-|-|  
|**Undefined**|単一の値: **undefined**|  
|**Null**|単一の値: **Null**|  
|**Boolean**|値: **false**、**true**。|  
|**数値**|倍精度浮動小数点数、IEEE 754 標準。|  
|**String**|0 個以上の Unicode 文字のシーケンス。 文字列は、一重引用符または二重引用符で囲む必要があります。|  
|**配列**|0 個以上の要素のシーケンス。 各要素には、**Undefined** を除く、任意のスカラー データ型の値を指定できます。|  
|**オブジェクト**|0 個以上の名前/値ペアの順序なしのセット。 名前は Unicode 文字列で、値は **Undefined** 以外の任意のスカラー データ型を指定できます。|  
  
## <a name="syntax"></a><a name="bk_syntax"></a>構文
  
```sql  
<constant> ::=  
   <undefined_constant>  
     | <null_constant>   
     | <boolean_constant>   
     | <number_constant>   
     | <string_constant>   
     | <array_constant>   
     | <object_constant>   
  
<undefined_constant> ::= undefined  
  
<null_constant> ::= null  
  
<boolean_constant> ::= false | true  
  
<number_constant> ::= decimal_literal | hexadecimal_literal  
  
<string_constant> ::= string_literal  
  
<array_constant> ::=  
    '[' [<constant>][,...n] ']'  
  
<object_constant> ::=   
   '{' [{property_name | "property_name"} : <constant>][,...n] '}'  
  
```  
  
##  <a name="arguments"></a><a name="bk_arguments"></a> 引数
  
* `<undefined_constant>; Undefined`  
  
  Undefined 型の未定義値を表します。  
  
* `<null_constant>; null`  
  
  **Null** 型の値 **null** を表します。  
  
* `<boolean_constant>`  
  
  Boolean 型の定数を表します。  
  
* `false`  
  
  Boolean 型の **false** 値を表します。  
  
* `true`  
  
  Boolean 型の **true** 値を表します。  
  
* `<number_constant>`  
  
  定数を表します。  
  
* `decimal_literal`  
  
  10 進数のリテラルは、10 進表記または指数表記を使用して表現された数値です。  
  
* `hexadecimal_literal`  
  
  16 進数のリテラルは、プレフィックス '0 x' の後に 1 つまたは複数の 16 進数字を使用して表現された数値です。  
  
* `<string_constant>`  
  
  文字列型の定数を表します。  
  
* `string _literal`  
  
  文字列リテラルは、0 個以上の Unicode 文字のシーケンスまたはエスケープ シーケンスによって表される Unicode 文字列です。 文字列リテラルは、単一引用符 (アポストロフィ: ') または二重引用符 (引用符:") で囲みます。  
  
  次のエスケープ シーケンスを使用できます。  
  
|**エスケープ シーケンス**|**説明**|**Unicode 文字**|  
|-|-|-|  
|\\'|アポストロフィ (')|U+0027|  
|\\"|引用符 (")|U+0022|  
|\\\ |逆斜線 (\\)|U+005C|  
|\\/|斜線 (/)|U+002F|  
|\b|バックスペース|U+0008|  
|\f|フォーム フィード|U+000C|  
|\n|ライン フィード|U+000A|  
|\r|キャリッジ リターン|U+000D|  
|\t|タブ|U+0009|  
|\uXXXX|4 つの 16 進数字によって定義された Unicode 文字。|U+XXXX|  

## <a name="next-steps"></a>次のステップ

- [Azure Cosmos DB .NET のサンプル](https://github.com/Azure/azure-cosmos-dotnet-v3)
- [モデル ドキュメント データ](modeling-data.md)
