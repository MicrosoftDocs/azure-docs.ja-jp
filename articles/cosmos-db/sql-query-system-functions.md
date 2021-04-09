---
title: Azure Cosmos DB クエリ言語のシステム関数
description: Azure Cosmos DB の組み込み SQL システム関数とユーザー定義 SQL システム関数について説明します。
author: ginamr
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 02/03/2021
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 36ebe80671c77cc83ddba770e3259f6542472e58
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "99526841"
---
# <a name="system-functions-azure-cosmos-db"></a>システム関数 (Azure Cosmos DB)
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

 Cosmos DB は、多くの組み込み SQL 関数を提供します。 組み込み関数のカテゴリは次のとおりです。  
  
|関数グループ|説明|操作|  
|--------------|-----------------|-----------------| 
|[配列関数](sql-query-array-functions.md)|配列関数は、配列入力値に対して演算を実行し、数値、ブール値、または配列値を返します。 | [ARRAY_CONCAT](sql-query-array-concat.md)、[ARRAY_CONTAINS](sql-query-array-contains.md)、[ARRAY_LENGTH](sql-query-array-length.md)、[ARRAY_SLICE](sql-query-array-slice.md) |
|[日付と時刻関数](sql-query-date-time-functions.md)|日付と時刻関数では、UTC での現在の日付と時刻を、数値のタイムスタンプ (値はミリ秒単位の Unix エポック) または ISO 8601 形式に準拠した文字列という 2 つの形式で取得できます。 | [GetCurrentDateTime](sql-query-getcurrentdatetime.md)、[GetCurrentTimestamp](sql-query-getcurrenttimestamp.md)、[GetCurrentTicks](sql-query-getcurrentticks.md) |
|[数学関数](sql-query-mathematical-functions.md)|一般に、各数学関数は、引数として提供された入力値に基づいて計算を実行し、数値を返します。 | [ABS](sql-query-abs.md)、[ACOS](sql-query-acos.md)、[ASIN](sql-query-asin.md)、[ATAN](sql-query-atan.md)、[ATN2](sql-query-atn2.md)、[CEILING](sql-query-ceiling.md)、[COS](sql-query-cos.md)、[COT](sql-query-cot.md)、[DEGREES](sql-query-degrees.md)、[EXP](sql-query-exp.md)、[FLOOR](sql-query-floor.md)、[LOG](sql-query-log.md)、[LOG10](sql-query-log10.md)、[PI](sql-query-pi.md)、[POWER](sql-query-power.md)、[RADIANS](sql-query-radians.md)、[RAND](sql-query-rand.md)、[ROUND](sql-query-round.md)、[SIGN](sql-query-sign.md)、[SIN](sql-query-sin.md)、[SQRT](sql-query-sqrt.md)、[SQUARE](sql-query-square.md)、[TAN](sql-query-tan.md)、[TRUNC](sql-query-trunc.md) |
|[空間関数](sql-query-spatial-functions.md)|空間関数は、空間オブジェクト入力値に対して演算を実行し、数値またはブール値を返します。 | [ST_DISTANCE](sql-query-st-distance.md)、[ST_INTERSECTS](sql-query-st-intersects.md)、[ST_ISVALID](sql-query-st-isvalid.md)、[ST_ISVALIDDETAILED](sql-query-st-isvaliddetailed.md)、[ST_WITHIN](sql-query-st-within.md) |
|[文字列関数](sql-query-string-functions.md)|文字列関数は、文字列入力値に対して演算を実行し、文字列、数値またはブール値を返します。 | [CONCAT](sql-query-concat.md)、[CONTAINS](sql-query-contains.md)、[ENDSWITH](sql-query-endswith.md)、[INDEX_OF](sql-query-index-of.md)、[LEFT](sql-query-left.md)、[LENGTH](sql-query-length.md)、[LOWER](sql-query-lower.md)、[LTRIM](sql-query-ltrim.md)、[REGEXMATCH](sql-query-regexmatch.md)[REPLACE](sql-query-replace.md)、[REPLICATE](sql-query-replicate.md)、[REVERSE](sql-query-reverse.md)、[RIGHT](sql-query-right.md)、[RTRIM](sql-query-rtrim.md)、[STARTSWITH](sql-query-startswith.md)、[StringToArray](sql-query-stringtoarray.md)、[StringToBoolean](sql-query-stringtoboolean.md)、[StringToNull](sql-query-stringtonull.md)、[StringToNumber](sql-query-stringtonumber.md)、[StringToObject](sql-query-stringtoobject.md)、[SUBSTRING](sql-query-substring.md)、[ToString](sql-query-tostring.md)、[TRIM](sql-query-trim.md)、[UPPER](sql-query-upper.md) |
|[型チェック関数](sql-query-type-checking-functions.md)|型チェック関数を使用すると、SQL クエリ内の式の型をチェックできます。 | [IS_ARRAY](sql-query-is-array.md)、[IS_BOOL](sql-query-is-bool.md)、[IS_DEFINED](sql-query-is-defined.md)、[IS_NULL](sql-query-is-null.md)、[IS_NUMBER](sql-query-is-number.md)、[IS_OBJECT](sql-query-is-object.md)、[IS_PRIMITIVE](sql-query-is-primitive.md)、[IS_STRING](sql-query-is-string.md) |

## <a name="built-in-versus-user-defined-functions-udfs"></a>組み込み関数とユーザー定義関数 (UDF)

現在ユーザー定義関数 (UDF) を使用している処理に対して、組み込み関数を利用できるようになった場合は、対応する組み込み関数の方が、高速かつ効率的に実行します。

## <a name="built-in-versus-ansi-sql-functions"></a>組み込み関数と ANSI SQL 関数

Cosmos DB 関数と ANSI SQL 関数の主な違いとして、Cosmos DB 関数はスキーマレス データやスキーマが混在するデータとうまく機能するように設計されています。 たとえば、プロパティがない場合や `undefined` のような数値以外の値を持つ場合、エラーを返す代わりに、項目がスキップされます。

## <a name="next-steps"></a>次のステップ

- [Azure Cosmos DB の概要](introduction.md)
- [配列関数](sql-query-array-functions.md)
- [日付と時刻関数](sql-query-date-time-functions.md)
- [数学関数](sql-query-mathematical-functions.md)
- [空間関数](sql-query-spatial-functions.md)
- [文字列関数](sql-query-string-functions.md)
- [型チェック関数](sql-query-type-checking-functions.md)
- [ユーザー定義関数](sql-query-udfs.md)
- [集計](sql-query-aggregate-functions.md)
