---
title: システム関数
description: Azure Cosmos DB での SQL システム関数について説明します。
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/31/2019
ms.author: mjbrown
ms.openlocfilehash: 11a6fdad187670bcb5af4c56198fd7343680690d
ms.sourcegitcommit: a12b2c2599134e32a910921861d4805e21320159
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/24/2019
ms.locfileid: "67343176"
---
# <a name="system-functions"></a>システム関数

 Cosmos DB は、多くの組み込み SQL 関数を提供します。 組み込み関数のカテゴリは次のとおりです。  
  
|Function|説明|  
|--------------|-----------------|  
|[数学関数](#mathematical-functions)|一般に、各数学関数は、引数として提供された入力値に基づいて計算を実行し、数値を返します。|  
|[型チェック関数](#type-checking-functions)|型チェック関数を使用すると、SQL クエリ内の式の型をチェックできます。|  
|[文字列関数](#string-functions)|文字列関数は、文字列入力値に対して演算を実行し、文字列、数値またはブール値を返します。|  
|[配列関数](#array-functions)|配列関数は、配列入力値に対して演算を実行し、数値、ブール値、または配列値を返します。|
|[日付と時刻関数](#date-time-functions)|日付と時刻関数では、UTC での現在の日付と時刻を、数値のタイムスタンプ (値はミリ秒単位の Unix エポック) または ISO 8601 形式に準拠した文字列という 2 つの形式で取得できます。|
|[空間関数](#spatial-functions)|空間関数は、空間オブジェクト入力値に対して演算を実行し、数値またはブール値を返します。|  

各カテゴリの関数の一覧を次に示します。

| 関数グループ | Operations |
|---------|----------|
| 数学関数 | ABS、CEILING、EXP、FLOOR、LOG、LOG10、POWER、ROUND、SIGN、SQRT、SQUARE、TRUNC、ACOS、ASIN、ATAN、ATN2、COS、COT、DEGREES、PI、RADIANS、SIN、TAN |
| 型チェック関数 | IS_ARRAY、IS_BOOL、IS_NULL、IS_NUMBER、IS_OBJECT、IS_STRING、IS_DEFINED、IS_PRIMITIVE |
| 文字列関数 | CONCAT、CONTAINS、ENDSWITH、INDEX_OF、LEFT、LENGTH、LOWER、LTRIM、REPLACE、REPLICATE、REVERSE、RIGHT、RTRIM、STARTSWITH、SUBSTRING、UPPER |
| 配列関数 | ARRAY_CONCAT、ARRAY_CONTAINS、ARRAY_LENGTH、ARRAY_SLICE |
| 日付と時刻関数 | GETCURRENTDATETIME、GETCURRENTTIMESTAMP  |
| 空間関数 | ST_DISTANCE、ST_WITHIN、ST_INTERSECTS、ST_ISVALID、ST_ISVALIDDETAILED |

現在ユーザー定義関数 (UDF) を使用している処理に対して、組み込み関数を利用できるようになった場合は、対応する組み込み関数の方が、高速かつ効率的に実行します。

Cosmos DB 関数と ANSI SQL 関数の主な違いとして、Cosmos DB 関数はスキーマレス データやスキーマが混在するデータとうまく機能するように設計されています。 たとえば、プロパティがない場合や `unknown` のような数値以外の値を持つ場合、エラーを返す代わりに、項目がスキップされます。

##  <a name="mathematical-functions"></a>数学関数  

各数学関数は、引数として提供された入力値に基づいて計算を実行し、数値を返します。

次の例のようなクエリを実行できます。

```sql
    SELECT VALUE ABS(-4)
```

結果は次のとおりです。

```json
    [4]
```

次の表に、サポートされている組み込みの数学関数を示します。
  
||||  
|-|-|-|  
|[ABS](#bk_abs)|[ACOS](#bk_acos)|[ASIN](#bk_asin)|  
|[ATAN](#bk_atan)|[ATN2](#bk_atn2)|[CEILING](#bk_ceiling)|  
|[COS](#bk_cos)|[COT](#bk_cot)|[DEGREES](#bk_degrees)|  
|[EXP](#bk_exp)|[FLOOR](#bk_floor)|[LOG](#bk_log)|  
|[LOG10](#bk_log10)|[PI](#bk_pi)|[POWER](#bk_power)|  
|[RADIANS](#bk_radians)|[ROUND](#bk_round)|[SIN](#bk_sin)|  
|[SQRT](#bk_sqrt)|[SQUARE](#bk_square)|[SIGN](#bk_sign)|  
|[TAN](#bk_tan)|[TRUNC](#bk_trunc)||  
  
####  <a name="bk_abs"></a> ABS  
 指定された数値式の絶対値 (正の値) を返します。  
  
 **構文**  
  
```  
ABS (<numeric_expression>)  
```  
  
 **引数**  
  
- `numeric_expression`  
  
   数値式です。  
  
  **戻り値の型**  
  
  数値式を返します。  
  
  **例**  
  
  次の例は、次の 3 つの異なる数値に対して ABS 関数を使用した結果を示しています。  
  
```  
SELECT ABS(-1) AS abs1, ABS(0) AS abs2, ABS(1) AS abs3 
```  
  
 結果セットは次のようになります。  
  
```  
[{abs1: 1, abs2: 0, abs3: 1}]  
```  
  
####  <a name="bk_acos"></a> ACOS  
 コサインが指定された数値式となる角度をラジアン単位で返します。アークコサインとも呼ばれます。  
  
 **構文**  
  
```  
ACOS(<numeric_expression>)  
```  
  
 **引数**  
  
- `numeric_expression`  
  
   数値式です。  
  
  **戻り値の型**  
  
  数値式を返します。  
  
  **例**  
  
  次の例は、-1 の ACOS を返します。  
  
```  
SELECT ACOS(-1) AS acos 
```  
  
 結果セットは次のようになります。  
  
```  
[{"acos": 3.1415926535897931}]  
```  
  
####  <a name="bk_asin"></a> ASIN  
 サインが指定された数値式となる角度をラジアン単位で返します。 アークサインとも呼ばれます。  
  
 **構文**  
  
```  
ASIN(<numeric_expression>)  
```  
  
 **引数**  
  
- `numeric_expression`  
  
   数値式です。  
  
  **戻り値の型**  
  
  数値式を返します。  
  
  **例**  
  
  次の例は、-1 の ASIN を返します。  
  
```  
SELECT ASIN(-1) AS asin  
```  
  
 結果セットは次のようになります。  
  
```  
[{"asin": -1.5707963267948966}]  
```  
  
####  <a name="bk_atan"></a> ATAN  
 タンジェントが指定された数値式となる角度をラジアン単位で返します。 アークタンジェントとも呼ばれます。  
  
 **構文**  
  
```  
ATAN(<numeric_expression>)  
```  
  
 **引数**  
  
- `numeric_expression`  
  
   数値式です。  
  
  **戻り値の型**  
  
  数値式を返します。  
  
  **例**  
  
  次の例では、指定した値の ATAN を返します。  
  
```  
SELECT ATAN(-45.01) AS atan  
```  
  
 結果セットは次のようになります。  
  
```  
[{"atan": -1.5485826962062663}]  
```  
  
####  <a name="bk_atn2"></a> ATN2  
 ラジアン単位で表される y/x のアーク タンジェンの主値を返します。  
  
 **構文**  
  
```  
ATN2(<numeric_expression>, <numeric_expression>)  
```  
  
 **引数**  
  
- `numeric_expression`  
  
   数値式です。  
  
  **戻り値の型**  
  
  数値式を返します。  
  
  **例**  
  
  次の例は、指定された x と y コンポーネントの ATN2 を計算します。  
  
```  
SELECT ATN2(35.175643, 129.44) AS atn2  
```  
  
 結果セットは次のようになります。  
  
```  
[{"atn2": 1.3054517947300646}]  
```  
  
####  <a name="bk_ceiling"></a> CEILING  
 指定された数値式以上の最小の整数値を返します。  
  
 **構文**  
  
```  
CEILING (<numeric_expression>)  
```  
  
 **引数**  
  
- `numeric_expression`  
  
   数値式です。  
  
  **戻り値の型**  
  
  数値式を返します。  
  
  **例**  
  
  次の例では、CEILING 関数を使用して、正の数値、負の数値、およびゼロ値を示します。  
  
```  
SELECT CEILING(123.45) AS c1, CEILING(-123.45) AS c2, CEILING(0.0) AS c3  
```  
  
 結果セットは次のようになります。  
  
```  
[{c1: 124, c2: -123, c3: 0}]  
```  
  
####  <a name="bk_cos"></a> COS  
 式で指定されたラジアン単位の角度の三角関数コサインを返します。  
  
 **構文**  
  
```  
COS(<numeric_expression>)  
```  
  
 **引数**  
  
- `numeric_expression`  
  
   数値式です。  
  
  **戻り値の型**  
  
  数値式を返します。  
  
  **例**  
  
  次の例では、指定された角度の COS を計算します。  
  
```  
SELECT COS(14.78) AS cos  
```  
  
 結果セットは次のようになります。  
  
```  
[{"cos": -0.59946542619465426}]  
```  
  
####  <a name="bk_cot"></a> COT  
 数値式で指定されたラジアン単位の角度の三角関数コタンジェントを返します。  
  
 **構文**  
  
```  
COT(<numeric_expression>)  
```  
  
 **引数**  
  
- `numeric_expression`  
  
   数値式です。  
  
  **戻り値の型**  
  
  数値式を返します。  
  
  **例**  
  
  次の例では、指定された角度の COT を計算します。  
  
```  
SELECT COT(124.1332) AS cot  
```  
  
 結果セットは次のようになります。  
  
```  
[{"cot": -0.040311998371148884}]  
```  
  
####  <a name="bk_degrees"></a> DEGREES  
 ラジアン単位で指定された角度に対応する度数単位の角度を返します。  
  
 **構文**  
  
```  
DEGREES (<numeric_expression>)  
```  
  
 **引数**  
  
- `numeric_expression`  
  
   数値式です。  
  
  **戻り値の型**  
  
  数値式を返します。  
  
  **例**  
  
  次の例では、PI/2 ラジアンの角度で度数を返します。  
  
```  
SELECT DEGREES(PI()/2) AS degrees  
```  
  
 結果セットは次のようになります。  
  
```  
[{"degrees": 90}]  
```  
  
####  <a name="bk_floor"></a> FLOOR  
 指定された数値式未満の最大の整数を返します。  
  
 **構文**  
  
```  
FLOOR (<numeric_expression>)  
```  
  
 **引数**  
  
- `numeric_expression`  
  
   数値式です。  
  
  **戻り値の型**  
  
  数値式を返します。  
  
  **例**  
  
  次の例では、FLOOR 関数を使用して、正の数値、負の数値、およびゼロ値を示します。  
  
```  
SELECT FLOOR(123.45) AS fl1, FLOOR(-123.45) AS fl2, FLOOR(0.0) AS fl3  
```  
  
 結果セットは次のようになります。  
  
```  
[{fl1: 123, fl2: -124, fl3: 0}]  
```  
  
####  <a name="bk_exp"></a> EXP  
 指定された数値式の指数値を返します。  
  
 **構文**  
  
```  
EXP (<numeric_expression>)  
```  
  
 **引数**  
  
- `numeric_expression`  
  
   数値式です。  
  
  **戻り値の型**  
  
  数値式を返します。  
  
  **解説**  
  
  定数 **e** (2.718281...) は、自然対数の底です。  
  
  数値の指数は、定数 **e** のべき乗された数値です。 たとえば、EXP(1.0) = e^1.0 = 2.71828182845905 および EXP(10) = e^10 = 22026.4657948067 になります。  
  
  数値の自然対数の指数は数自体です:EXP (LOG (n)) = n。 数値の指数の自然対数は数自体です:LOG (EXP (n)) = n。  
  
  **例**  
  
  次の例では、変数を宣言し、指定した変数 (10) の指数値を返します。  
  
```  
SELECT EXP(10) AS exp  
```  
  
 結果セットは次のようになります。  
  
```  
[{exp: 22026.465794806718}]  
```  
  
 次の例では、自然対数 20 の指数値、および指数 20 の自然対数を返します。 これらの関数は互いの逆関数であるため、どちらの場合も浮動小数点計算に丸めを適用した戻り値は 20 です。  
  
```  
SELECT EXP(LOG(20)) AS exp1, LOG(EXP(20)) AS exp2  
```  
  
 結果セットは次のようになります。  
  
```  
[{exp1: 19.999999999999996, exp2: 20}]  
```  
  
####  <a name="bk_log"></a> LOG  
 指定された数値式の自然対数を返します。  
  
 **構文**  
  
```  
LOG (<numeric_expression> [, <base>])  
```  
  
 **引数**  
  
- `numeric_expression`  
  
   数値式です。  
  
- `base`  
  
   対数の底を設定する省略可能な数値引数。  
  
  **戻り値の型**  
  
  数値式を返します。  
  
  **解説**  
  
  既定では、LOG() は自然対数を返します。 省略可能な底パラメーターを使用して、対数の底を別の値に変更できます。  
  
  自然対数は、底 **e** に対する自然対数です。ここで、**e** は、2.718281828 にほぼ等しい無理定数です。  
  
  数値の指数の自然対数は数自体です:LOG( EXP( n ) ) = n。 数値の自然対数の指数は数自体です:EXP( LOG( n ) ) = n。  
  
  **例**  
  
  次の例では、変数を宣言し、指定した変数 (10) の対数値を返します。  
  
```  
SELECT LOG(10) AS log  
```  
  
 結果セットは次のようになります。  
  
```  
[{log: 2.3025850929940459}]  
```  
  
 次の例では、数値の指数の LOG を計算します。  
  
```  
SELECT EXP(LOG(10)) AS expLog  
```  
  
 結果セットは次のようになります。  
  
```  
[{expLog: 10.000000000000002}]  
```  
  
####  <a name="bk_log10"></a> LOG10  
 指定された数値式の底 10 の対数を返します。  
  
 **構文**  
  
```  
LOG10 (<numeric_expression>)  
```  
  
 **引数**  
  
- `numeric_expression`  
  
   数値式です。  
  
  **戻り値の型**  
  
  数値式を返します。  
  
  **解説**  
  
  LOG10 関数と POWER 関数は、互いに逆の関係になります。 たとえば、10 ^ log10 (n) = n になります。  
  
  **例**  
  
  次の例では、変数を宣言し、指定した変数 (100) の LOG10 値を返します。  
  
```  
SELECT LOG10(100) AS log10 
```  
  
 結果セットは次のようになります。  
  
```  
[{log10: 2}]  
```  
  
####  <a name="bk_pi"></a> PI  
 円周率の定数値を返します。  
  
 **構文**  
  
```  
PI ()  
```  
  
 **引数**  
  
- `numeric_expression`  
  
   数値式です。  
  
  **戻り値の型**  
  
  数値式を返します。  
  
  **例**  
  
  次の例は、PI の値を返します。  
  
```  
SELECT PI() AS pi 
```  
  
 結果セットは次のようになります。  
  
```  
[{"pi": 3.1415926535897931}]  
```  
  
####  <a name="bk_power"></a> POWER  
 指定されたべき乗の指定された式の値を返します。  
  
 **構文**  
  
```  
POWER (<numeric_expression>, <y>)  
```  
  
 **引数**  
  
- `numeric_expression`  
  
   数値式です。  
  
- `y`  
  
   `numeric_expression` のべき乗です。  
  
  **戻り値の型**  
  
  数値式を返します。  
  
  **例**  
  
  次の例では、数値の 3 (数のキューブ) のべき乗数を示します。  
  
```  
SELECT POWER(2, 3) AS pow1, POWER(2.5, 3) AS pow2  
```  
  
 結果セットは次のようになります。  
  
```  
[{pow1: 8, pow2: 15.625}]  
```  
  
####  <a name="bk_radians"></a> RADIANS  
 数値式が度数単位で入力されると、ラジアンを返します。  
  
 **構文**  
  
```  
RADIANS (<numeric_expression>)  
```  
  
 **引数**  
  
- `numeric_expression`  
  
   数値式です。  
  
  **戻り値の型**  
  
  数値式を返します。  
  
  **例**  
  
  次の例では、入力としていくつかの角度を取得し、対応するラジアン値を返します。  
  
```  
SELECT RADIANS(-45.01) AS r1, RADIANS(-181.01) AS r2, RADIANS(0) AS r3, RADIANS(0.1472738) AS r4, RADIANS(197.1099392) AS r5  
```  
  
  結果セットは次のようになります。  
  
```  
[{  
       "r1": -0.7855726963226477,  
       "r2": -3.1592204790349356,  
       "r3": 0,  
       "r4": 0.0025704127119236249,  
       "r5": 3.4402174274458375  
   }]  
```  
  
####  <a name="bk_round"></a> ROUND  
 最も近い整数値に丸められた数値を返します。  
  
 **構文**  
  
```  
ROUND(<numeric_expression>)  
```  
  
 **引数**  
  
- `numeric_expression`  
  
   数値式です。  
  
  **戻り値の型**  
  
  数値式を返します。  
  
  **解説**
  
  実行される丸め操作では、中点はゼロから離れる方向に丸められます。 入力が 2 つの整数のちょうど真ん中になる数値式の場合、結果はゼロより遠い側で最も近い整数値になります。  
  
  |<numeric_expression>|丸めの結果|
  |-|-|
  |-6.5000|-7|
  |-0.5|-1|
  |0.5|1|
  |6.5000|7||
  
  **例**  
  
  次の例では、最も近い整数に次の正と負の数値を丸めます。  
  
```  
SELECT ROUND(2.4) AS r1, ROUND(2.6) AS r2, ROUND(2.5) AS r3, ROUND(-2.4) AS r4, ROUND(-2.6) AS r5  
```  
  
  結果セットは次のようになります。  
  
```  
[{r1: 2, r2: 3, r3: 3, r4: -2, r5: -3}]  
```  
  
####  <a name="bk_sign"></a> SIGN  
 指定された数値式の正 (+1)、ゼロ (0)、または負 (-1) の符号を返します。  
  
 **構文**  
  
```  
SIGN(<numeric_expression>)  
```  
  
 **引数**  
  
- `numeric_expression`  
  
   数値式です。  
  
  **戻り値の型**  
  
  数値式を返します。  
  
  **例**  
  
  次の例では、-2 ～ 2 の数値の SIGN 値を返します。  
  
```  
SELECT SIGN(-2) AS s1, SIGN(-1) AS s2, SIGN(0) AS s3, SIGN(1) AS s4, SIGN(2) AS s5  
```  
  
 結果セットは次のようになります。  
  
```  
[{s1: -1, s2: -1, s3: 0, s4: 1, s5: 1}]  
```  
  
####  <a name="bk_sin"></a> SIN  
 式で指定されたラジアン単位の角度の三角関数サインを返します。  
  
 **構文**  
  
```  
SIN(<numeric_expression>)  
```  
  
 **引数**  
  
- `numeric_expression`  
  
   数値式です。  
  
  **戻り値の型**  
  
  数値式を返します。  
  
  **例**  
  
  次の例では、指定された角度の SIN を計算します。  
  
```  
SELECT SIN(45.175643) AS sin  
```  
  
 結果セットは次のようになります。  
  
```  
[{"sin": 0.929607286611012}]  
```  
  
####  <a name="bk_sqrt"></a> SQRT  
 指定された数値の平方根を返します。  
  
 **構文**  
  
```  
SQRT(<numeric_expression>)  
```  
  
 **引数**  
  
- `numeric_expression`  
  
   数値式です。  
  
  **戻り値の型**  
  
  数値式を返します。  
  
  **例**  
  
  次の例では、数値 1 ～ 3 の平方根を返します。  
  
```  
SELECT SQRT(1) AS s1, SQRT(2.0) AS s2, SQRT(3) AS s3  
```  
  
 結果セットは次のようになります。  
  
```  
[{s1: 1, s2: 1.4142135623730952, s3: 1.7320508075688772}]  
```  
  
####  <a name="bk_square"></a> SQUARE  
 指定された数値の 2 乗を返します。  
  
 **構文**  
  
```  
SQUARE(<numeric_expression>)  
```  
  
 **引数**  
  
- `numeric_expression`  
  
   数値式です。  
  
  **戻り値の型**  
  
  数値式を返します。  
  
  **例**  
  
  次の例では、数値 1 ～ 3 の 2 乗を返します。  
  
```  
SELECT SQUARE(1) AS s1, SQUARE(2.0) AS s2, SQUARE(3) AS s3  
```  
  
 結果セットは次のようになります。  
  
```  
[{s1: 1, s2: 4, s3: 9}]  
```  
  
####  <a name="bk_tan"></a> TAN  
 式で指定されたラジアン単位の角度のタンジェントを返します。  
  
 **構文**  
  
```  
TAN (<numeric_expression>)  
```  
  
 **引数**  
  
- `numeric_expression`  
  
   数値式です。  
  
  **戻り値の型**  
  
  数値式を返します。  
  
  **例**  
  
  次の例は、PI()/2 のタンジェントを計算します。  
  
```  
SELECT TAN(PI()/2) AS tan 
```  
  
 結果セットは次のようになります。  
  
```  
[{"tan": 16331239353195370 }]  
```  
  
####  <a name="bk_trunc"></a> TRUNC  
 最も近い整数値に切り捨てられた数値を返します。  
  
 **構文**  
  
```  
TRUNC(<numeric_expression>)  
```  
  
 **引数**  
  
- `numeric_expression`  
  
   数値式です。  
  
  **戻り値の型**  
  
  数値式を返します。  
  
  **例**  
  
  次の例では、最も近い整数値に次の正と負の数値を丸めます。  
  
```  
SELECT TRUNC(2.4) AS t1, TRUNC(2.6) AS t2, TRUNC(2.5) AS t3, TRUNC(-2.4) AS t4, TRUNC(-2.6) AS t5  
```  
  
 結果セットは次のようになります。  
  
```  
[{t1: 2, t2: 2, t3: 2, t4: -2, t5: -2}]  
```

## <a id="type-checking-functions"></a>型チェック関数

型チェック関数を使用すると、SQL クエリ内の式の型をチェックできます。 型チェック関数を使用して、項目内のプロパティの型が変数または不明の場合に型をその場で判定できます。 次の表に、サポートされている組み込みの型チェック関数を示します。

次の関数は、入力値に対する型チェックをサポートし、それぞれがブール値を返します。  
  
||||  
|-|-|-|  
|[IS_ARRAY](#bk_is_array)|[IS_BOOL](#bk_is_bool)|[IS_DEFINED](#bk_is_defined)|  
|[IS_NULL](#bk_is_null)|[IS_NUMBER](#bk_is_number)|[IS_OBJECT](#bk_is_object)|  
|[IS_PRIMITIVE](#bk_is_primitive)|[IS_STRING](#bk_is_string)||  
  
####  <a name="bk_is_array"></a> IS_ARRAY  
 指定した式の型が配列であるかどうかを示すブール値を返します。  
  
 **構文**  
  
```  
IS_ARRAY(<expression>)  
```  
  
 **引数**  
  
- `expression`  
  
   任意の有効な式です。  
  
  **戻り値の型**  
  
  ブール式を返します。  
  
  **例**  
  
  次の例では、IS_ARRAY 関数を使用して、JSON ブール値のオブジェクト、数値、文字列、null、オブジェクト、配列、および未定義の型をチェックします。  
  
```  
SELECT   
 IS_ARRAY(true) AS isArray1,   
 IS_ARRAY(1) AS isArray2,  
 IS_ARRAY("value") AS isArray3,  
 IS_ARRAY(null) AS isArray4,  
 IS_ARRAY({prop: "value"}) AS isArray5,   
 IS_ARRAY([1, 2, 3]) AS isArray6,  
 IS_ARRAY({prop: "value"}.prop2) AS isArray7  
```  
  
 結果セットは次のようになります。  
  
```  
[{"isArray1":false,"isArray2":false,"isArray3":false,"isArray4":false,"isArray5":false,"isArray6":true,"isArray7":false}]
```  
  
####  <a name="bk_is_bool"></a> IS_BOOL  
 指定した式の型がブール値であるかどうかを示すブール値を返します。  
  
 **構文**  
  
```  
IS_BOOL(<expression>)  
```  
  
 **引数**  
  
- `expression`  
  
   任意の有効な式です。  
  
  **戻り値の型**  
  
  ブール式を返します。  
  
  **例**  
  
  次の例では、IS_BOOL 関数を使用して、JSON ブール値のオブジェクト、数値、文字列、null、オブジェクト、配列、および未定義の型をチェックします。  
  
```  
SELECT   
    IS_BOOL(true) AS isBool1,   
    IS_BOOL(1) AS isBool2,  
    IS_BOOL("value") AS isBool3,   
    IS_BOOL(null) AS isBool4,  
    IS_BOOL({prop: "value"}) AS isBool5,   
    IS_BOOL([1, 2, 3]) AS isBool6,  
    IS_BOOL({prop: "value"}.prop2) AS isBool7  
```  
  
 結果セットは次のようになります。  
  
```  
[{"isBool1":true,"isBool2":false,"isBool3":false,"isBool4":false,"isBool5":false,"isBool6":false,"isBool7":false}]
```  
  
####  <a name="bk_is_defined"></a> IS_DEFINED  
 プロパティに値が代入されているかどうかを示すブール値を返します。  
  
 **構文**  
  
```  
IS_DEFINED(<expression>)  
```  
  
 **引数**  
  
- `expression`  
  
   任意の有効な式です。  
  
  **戻り値の型**  
  
  ブール式を返します。  
  
  **例**  
  
  次の例では、指定された JSON ドキュメント内のプロパティの存在を確認します。 1 つ目は、"a" が存在するので true を返しますが、2 つ目は "b" が存在しないので false を返します。  
  
```  
SELECT IS_DEFINED({ "a" : 5 }.a) AS isDefined1, IS_DEFINED({ "a" : 5 }.b) AS isDefined2 
```  
  
 結果セットは次のようになります。  
  
```  
[{"isDefined1":true,"isDefined2":false}]  
```  
  
####  <a name="bk_is_null"></a> IS_NULL  
 指定した式の型が null であるかどうかを示すブール値を返します。  
  
 **構文**  
  
```  
IS_NULL(<expression>)  
```  
  
 **引数**  
  
- `expression`  
  
   任意の有効な式です。  
  
  **戻り値の型**  
  
  ブール式を返します。  
  
  **例**  
  
  次の例では、IS_NULL 関数を使用して、JSON ブール値のオブジェクト、数値、文字列、null、オブジェクト、配列、および未定義の型をチェックします。  
  
```  
SELECT   
    IS_NULL(true) AS isNull1,   
    IS_NULL(1) AS isNull2,  
    IS_NULL("value") AS isNull3,   
    IS_NULL(null) AS isNull4,  
    IS_NULL({prop: "value"}) AS isNull5,   
    IS_NULL([1, 2, 3]) AS isNull6,  
    IS_NULL({prop: "value"}.prop2) AS isNull7  
```  
  
 結果セットは次のようになります。  
  
```  
[{"isNull1":false,"isNull2":false,"isNull3":false,"isNull4":true,"isNull5":false,"isNull6":false,"isNull7":false}]
```  
  
####  <a name="bk_is_number"></a> IS_NUMBER  
 指定した式の型が数値であるかどうかを示すブール値を返します。  
  
 **構文**  
  
```  
IS_NUMBER(<expression>)  
```  
  
 **引数**  
  
- `expression`  
  
   任意の有効な式です。  
  
  **戻り値の型**  
  
  ブール式を返します。  
  
  **例**  
  
  次の例では、IS_NULL 関数を使用して、JSON ブール値のオブジェクト、数値、文字列、null、オブジェクト、配列、および未定義の型をチェックします。  
  
```  
SELECT   
    IS_NUMBER(true) AS isNum1,   
    IS_NUMBER(1) AS isNum2,  
    IS_NUMBER("value") AS isNum3,   
    IS_NUMBER(null) AS isNum4,  
    IS_NUMBER({prop: "value"}) AS isNum5,   
    IS_NUMBER([1, 2, 3]) AS isNum6,  
    IS_NUMBER({prop: "value"}.prop2) AS isNum7  
```  
  
 結果セットは次のようになります。  
  
```  
[{"isNum1":false,"isNum2":true,"isNum3":false,"isNum4":false,"isNum5":false,"isNum6":false,"isNum7":false}]  
```  
  
####  <a name="bk_is_object"></a> IS_OBJECT  
 指定した式の型が JSON オブジェクトであるかどうかを示すブール値を返します。  
  
 **構文**  
  
```  
IS_OBJECT(<expression>)  
```  
  
 **引数**  
  
- `expression`  
  
   任意の有効な式です。  
  
  **戻り値の型**  
  
  ブール式を返します。  
  
  **例**  
  
  次の例では、IS_OBJECT 関数を使用して、JSON ブール値のオブジェクト、数値、文字列、null、オブジェクト、配列、および未定義の型をチェックします。  
  
```  
SELECT   
    IS_OBJECT(true) AS isObj1,   
    IS_OBJECT(1) AS isObj2,  
    IS_OBJECT("value") AS isObj3,   
    IS_OBJECT(null) AS isObj4,  
    IS_OBJECT({prop: "value"}) AS isObj5,   
    IS_OBJECT([1, 2, 3]) AS isObj6,  
    IS_OBJECT({prop: "value"}.prop2) AS isObj7  
```  
  
 結果セットは次のようになります。  
  
```  
[{"isObj1":false,"isObj2":false,"isObj3":false,"isObj4":false,"isObj5":true,"isObj6":false,"isObj7":false}]
```  
  
####  <a name="bk_is_primitive"></a> IS_PRIMITIVE  
 指定した式の型がプリミティブ (文字列、ブール値、数値、または null) であるかどうかを示すブール値を返します。  
  
 **構文**  
  
```  
IS_PRIMITIVE(<expression>)  
```  
  
 **引数**  
  
- `expression`  
  
   任意の有効な式です。  
  
  **戻り値の型**  
  
  ブール式を返します。  
  
  **例**  
  
  次の例では、IS_PRIMITIVE 関数を使用して、JSON ブール値のオブジェクト、数値、文字列、null、オブジェクト、配列、および未定義の型をチェックします。  
  
```  
SELECT   
           IS_PRIMITIVE(true) AS isPrim1,   
           IS_PRIMITIVE(1) AS isPrim2,  
           IS_PRIMITIVE("value") AS isPrim3,   
           IS_PRIMITIVE(null) AS isPrim4,  
           IS_PRIMITIVE({prop: "value"}) AS isPrim5,   
           IS_PRIMITIVE([1, 2, 3]) AS isPrim6,  
           IS_PRIMITIVE({prop: "value"}.prop2) AS isPrim7  
```  
  
 結果セットは次のようになります。  
  
```  
[{"isPrim1": true, "isPrim2": true, "isPrim3": true, "isPrim4": true, "isPrim5": false, "isPrim6": false, "isPrim7": false}]  
```  
  
####  <a name="bk_is_string"></a> IS_STRING  
 指定した式の型が文字列であるかどうかを示すブール値を返します。  
  
 **構文**  
  
```  
IS_STRING(<expression>)  
```  
  
 **引数**  
  
- `expression`  
  
   任意の有効な式です。  
  
  **戻り値の型**  
  
  ブール式を返します。  
  
  **例**  
  
  次の例では、IS_STRING 関数を使用して、JSON ブール値のオブジェクト、数値、文字列、null、オブジェクト、配列、および未定義の型をチェックします。  
  
```  
SELECT   
       IS_STRING(true) AS isStr1,   
       IS_STRING(1) AS isStr2,  
       IS_STRING("value") AS isStr3,   
       IS_STRING(null) AS isStr4,  
       IS_STRING({prop: "value"}) AS isStr5,   
       IS_STRING([1, 2, 3]) AS isStr6,  
       IS_STRING({prop: "value"}.prop2) AS isStr7  
```  
  
 結果セットは次のようになります。  
  
```  
[{"isStr1":false,"isStr2":false,"isStr3":true,"isStr4":false,"isStr5":false,"isStr6":false,"isStr7":false}] 
```  

## <a id="string-functions"></a>文字列関数

次のスカラー関数は、文字列入力値に対して演算を実行し、文字列、数値またはブール値を返します。
  
||||  
|-|-|-|  
|[CONCAT](#bk_concat)|[CONTAINS](#bk_contains)|[ENDSWITH](#bk_endswith)|  
|[INDEX_OF](#bk_index_of)|[LEFT](#bk_left)|[LENGTH](#bk_length)|  
|[LOWER](#bk_lower)|[LTRIM](#bk_ltrim)|[REPLACE](#bk_replace)|  
|[REPLICATE](#bk_replicate)|[REVERSE](#bk_reverse)|[RIGHT](#bk_right)|  
|[RTRIM](#bk_rtrim)|[STARTSWITH](#bk_startswith)|[StringToArray](#bk_stringtoarray)|
|[StringToBoolean](#bk_stringtoboolean)|[StringToNull](#bk_stringtonull)|[StringToNumber](#bk_stringtonumber)|
|[StringToObject](#bk_stringtoobject)|[SUBSTRING](#bk_substring)|[ToString](#bk_tostring)|
|[TRIM](#bk_trim)|[UPPER](#bk_upper)||
  
####  <a name="bk_concat"></a> CONCAT  
 2 つ以上の文字列値を連結した結果である文字列を返します。  
  
 **構文**  
  
```  
CONCAT(<str_expr>, <str_expr> [, <str_expr>])  
```  
  
 **引数**  
  
- `str_expr`  
  
   任意の有効な文字列式です。  
  
  **戻り値の型**  
  
  文字列式を返します。  
  
  **例**  
  
  次の例では、指定した値の連結された文字列を返します。  
  
```  
SELECT CONCAT("abc", "def") AS concat  
```  
  
 結果セットは次のようになります。  
  
```  
[{"concat": "abcdef"}  
```  
  
####  <a name="bk_contains"></a> CONTAINS  
 1 つ目の文字列式に 2 つ目の文字列式が含まれているかどうかを示すブール値を返します。  
  
 **構文**  
  
```  
CONTAINS(<str_expr>, <str_expr>)  
```  
  
 **引数**  
  
- `str_expr`  
  
   任意の有効な文字列式です。  
  
  **戻り値の型**  
  
  ブール式を返します。  
  
  **例**  
  
  次の例では、"abc" に "ab" と "d" が含まれるかどうかを確認します。  
  
```  
SELECT CONTAINS("abc", "ab") AS c1, CONTAINS("abc", "d") AS c2 
```  
  
 結果セットは次のようになります。  
  
```  
[{"c1": true, "c2": false}]  
```  
  
####  <a name="bk_endswith"></a> ENDSWITH  
 1 つ目の文字列式が 2 つ目の文字列で終了しているかどうかを示すブール値を返します。  
  
 **構文**  
  
```  
ENDSWITH(<str_expr>, <str_expr>)  
```  
  
 **引数**  
  
- `str_expr`  
  
   任意の有効な文字列式です。  
  
  **戻り値の型**  
  
  ブール式を返します。  
  
  **例**  
  
  次の例では、"b" と "bc" で終わる "abc" を返します。  
  
```  
SELECT ENDSWITH("abc", "b") AS e1, ENDSWITH("abc", "bc") AS e2 
```  
  
 結果セットは次のようになります。  
  
```  
[{"e1": false, "e2": true}]  
```  
  
####  <a name="bk_index_of"></a> INDEX_OF  
 1 つ目に指定された文字列式内で 2 つ目の文字列式が最初に出現する箇所の開始位置を返します。文字列が見つからない場合は -1 を返します。  
  
 **構文**  
  
```  
INDEX_OF(<str_expr>, <str_expr>)  
```  
  
 **引数**  
  
- `str_expr`  
  
   任意の有効な文字列式です。  
  
  **戻り値の型**  
  
  数値式を返します。  
  
  **例**  
  
  次の例では、"abc" 内のさまざまな部分文字列のインデックスを返します。  
  
```  
SELECT INDEX_OF("abc", "ab") AS i1, INDEX_OF("abc", "b") AS i2, INDEX_OF("abc", "c") AS i3 
```  
  
 結果セットは次のようになります。  
  
```  
[{"i1": 0, "i2": 1, "i3": -1}]  
```  
  
####  <a name="bk_left"></a> LEFT  
 指定された文字数分、文字列の左側の部分を返します。  
  
 **構文**  
  
```  
LEFT(<str_expr>, <num_expr>)  
```  
  
 **引数**  
  
- `str_expr`  
  
   任意の有効な文字列式です。  
  
- `num_expr`  
  
   任意の有効な数値式です。  
  
  **戻り値の型**  
  
  文字列式を返します。  
  
  **例**  
  
  次の例では、さまざまな長さの値の "abc" の左側の部分を返します。  
  
```  
SELECT LEFT("abc", 1) AS l1, LEFT("abc", 2) AS l2 
```  
  
 結果セットは次のようになります。  
  
```  
[{"l1": "a", "l2": "ab"}]  
```  
  
####  <a name="bk_length"></a> LENGTH  
 指定された文字列式の文字数を返します。  
  
 **構文**  
  
```  
LENGTH(<str_expr>)  
```  
  
 **引数**  
  
- `str_expr`  
  
   任意の有効な文字列式です。  
  
  **戻り値の型**  
  
  文字列式を返します。  
  
  **例**  
  
  次の例では、文字列の長さを返します。  
  
```  
SELECT LENGTH("abc") AS len 
```  
  
 結果セットは次のようになります。  
  
```  
[{"len": 3}]  
```  
  
####  <a name="bk_lower"></a> LOWER  
 文字列式の大文字データを小文字に変換して返します。  
  
 **構文**  
  
```  
LOWER(<str_expr>)  
```  
  
 **引数**  
  
- `str_expr`  
  
   任意の有効な文字列式です。  
  
  **戻り値の型**  
  
  文字列式を返します。  
  
  **例**  
  
  次の例は、クエリ内での LOWER の使用方法を示しています。  
  
```  
SELECT LOWER("Abc") AS lower
```  
  
 結果セットは次のようになります。  
  
```  
[{"lower": "abc"}]  
  
```  
  
####  <a name="bk_ltrim"></a> LTRIM  
 文字列式の先頭の空白を削除して返します。  
  
 **構文**  
  
```  
LTRIM(<str_expr>)  
```  
  
 **引数**  
  
- `str_expr`  
  
   任意の有効な文字列式です。  
  
  **戻り値の型**  
  
  文字列式を返します。  
  
  **例**  
  
  次の例は、クエリ内での LTRIM の使用方法を示しています。  
  
```  
SELECT LTRIM("  abc") AS l1, LTRIM("abc") AS l2, LTRIM("abc   ") AS l3 
```  
  
 結果セットは次のようになります。  
  
```  
[{"l1": "abc", "l2": "abc", "l3": "abc   "}]  
```  
  
####  <a name="bk_replace"></a> REPLACE  
 指定された文字列値のすべての出現箇所をもう 1 つの文字列値に置き換えます。  
  
 **構文**  
  
```  
REPLACE(<str_expr>, <str_expr>, <str_expr>)  
```  
  
 **引数**  
  
- `str_expr`  
  
   任意の有効な文字列式です。  
  
  **戻り値の型**  
  
  文字列式を返します。  
  
  **例**  
  
  次の例は、クエリ内での REPLACE の使用方法を示しています。  
  
```  
SELECT REPLACE("This is a Test", "Test", "desk") AS replace 
```  
  
 結果セットは次のようになります。  
  
```  
[{"replace": "This is a desk"}]  
```  
  
####  <a name="bk_replicate"></a> REPLICATE  
 文字列値を指定された回数だけ繰り返します。  
  
 **構文**  
  
```  
REPLICATE(<str_expr>, <num_expr>)  
```  
  
 **引数**  
  
- `str_expr`  
  
   任意の有効な文字列式です。  
  
- `num_expr`  
  
   任意の有効な数値式です。 num_expr が負の数値である場合、または有限の数値でない場合、結果は未定義になります。

  > [!NOTE]
  > 結果の最大長は 10,000 文字、つまり (length(str_expr) * num_expr) <= 10,000 です。
  
  **戻り値の型**  
  
  文字列式を返します。  
  
  **例**  
  
  次の例は、クエリ内での REPLICATE の使用方法を示しています。  
  
```  
SELECT REPLICATE("a", 3) AS replicate  
```  
  
 結果セットは次のようになります。  
  
```  
[{"replicate": "aaa"}]  
```  
  
####  <a name="bk_reverse"></a> REVERSE  
 文字列値の順序を逆にして返します。  
  
 **構文**  
  
```  
REVERSE(<str_expr>)  
```  
  
 **引数**  
  
- `str_expr`  
  
   任意の有効な文字列式です。  
  
  **戻り値の型**  
  
  文字列式を返します。  
  
  **例**  
  
  次の例は、クエリ内での REVERSE の使用方法を示しています。  
  
```  
SELECT REVERSE("Abc") AS reverse  
```  
  
 結果セットは次のようになります。  
  
```  
[{"reverse": "cbA"}]  
```  
  
####  <a name="bk_right"></a> RIGHT  
 指定された文字数分、文字列の右側の部分を返します。  
  
 **構文**  
  
```  
RIGHT(<str_expr>, <num_expr>)  
```  
  
 **引数**  
  
- `str_expr`  
  
   任意の有効な文字列式です。  
  
- `num_expr`  
  
   任意の有効な数値式です。  
  
  **戻り値の型**  
  
  文字列式を返します。  
  
  **例**  
  
  次の例では、さまざまな長さの値の "abc" の右側の部分を返します。  
  
```  
SELECT RIGHT("abc", 1) AS r1, RIGHT("abc", 2) AS r2 
```  
  
 結果セットは次のようになります。  
  
```  
[{"r1": "c", "r2": "bc"}]  
```  
  
####  <a name="bk_rtrim"></a> RTRIM  
 文字列式の末尾の空白を削除して返します。  
  
 **構文**  
  
```  
RTRIM(<str_expr>)  
```  
  
 **引数**  
  
- `str_expr`  
  
   任意の有効な文字列式です。  
  
  **戻り値の型**  
  
  文字列式を返します。  
  
  **例**  
  
  次の例は、クエリ内での RTRIM の使用方法を示しています。  
  
```  
SELECT RTRIM("  abc") AS r1, RTRIM("abc") AS r2, RTRIM("abc   ") AS r3  
```  
  
 結果セットは次のようになります。  
  
```  
[{"r1": "   abc", "r2": "abc", "r3": "abc"}]  
```  
  
####  <a name="bk_startswith"></a> STARTSWITH  
 1 つ目の文字列式が 2 つ目の文字列で始まっているかどうかを示すブール値を返します。  
  
 **構文**  
  
```  
STARTSWITH(<str_expr>, <str_expr>)  
```  
  
 **引数**  
  
- `str_expr`  
  
   任意の有効な文字列式です。  
  
  **戻り値の型**  
  
  ブール式を返します。  
  
  **例**  
  
  次の例は、文字列 "abc" が "b" および "a" で始まるかどうかを確認します。  
  
```  
SELECT STARTSWITH("abc", "b") AS s1, STARTSWITH("abc", "a") AS s2  
```  
  
 結果セットは次のようになります。  
  
```  
[{"s1": false, "s2": true}]  
```  

  ####  <a name="bk_stringtoarray"></a> StringToArray  
 配列に変換された式を返します。 式を変換できない場合は、undefined を返します。  
  
 **構文**  
  
```  
StringToArray(<expr>)  
```  
  
 **引数**  
  
- `expr`  
  
   これは、JSON 配列式として評価される有効なスカラー式です。 入れ子になった文字列値を有効にするには二重引用符で囲む必要があることにご注意ください。 JSON 形式の詳細については、「[json.org](https://json.org/)」をご覧ください。
  
  **戻り値の型**  
  
  配列式または undefined を返します。  
  
  **例**  
  
  次の例では、異なる型間で StringToArray がどのように動作するかを示します。 
  
 有効な入力を使用した例を次に示します。

```
SELECT 
    StringToArray('[]') AS a1, 
    StringToArray("[1,2,3]") AS a2,
    StringToArray("[\"str\",2,3]") AS a3,
    StringToArray('[["5","6","7"],["8"],["9"]]') AS a4,
    StringToArray('[1,2,3, "[4,5,6]",[7,8]]') AS a5
```

結果セットは次のようになります。

```
[{"a1": [], "a2": [1,2,3], "a3": ["str",2,3], "a4": [["5","6","7"],["8"],["9"]], "a5": [1,2,3,"[4,5,6]",[7,8]]}]
```

無効な入力を使用した例を次に示します。 
   
 配列内に一重引用符を使用した場合は、有効な JSON ではありません。
クエリ内で有効であっても、有効な配列として解析されません。 配列文字列内の文字列は、"[\\"\\"]" のようにエスケープするか、'[""]' のように一重引用符で囲む必要があります。

```
SELECT
    StringToArray("['5','6','7']")
```

結果セットは次のようになります。

```
[{}]
```

無効な入力の例を次に示します。
   
 渡された式は JSON 配列として解析されます。次の場合は、配列型として評価されないため、undefined が返されます。
   
```
SELECT
    StringToArray("["),
    StringToArray("1"),
    StringToArray(NaN),
    StringToArray(false),
    StringToArray(undefined)
```

結果セットは次のようになります。

```
[{}]
```

####  <a name="bk_stringtoboolean"></a> StringToBoolean  
 ブール値に変換された式を返します。 式を変換できない場合は、undefined を返します。  
  
 **構文**  
  
```  
StringToBoolean(<expr>)  
```  
  
 **引数**  
  
- `expr`  
  
   ブール式として評価される有効なスカラー式です。  
  
  **戻り値の型**  
  
  ブール式または undefined を返します。  
  
  **例**  
  
  次の例では、異なる型間で StringToBoolean がどのように動作するかを示します。 
 
 有効な入力を使用した例を次に示します。

空白は "true" または "false" の前後のみで使用できます。

```  
SELECT 
    StringToBoolean("true") AS b1, 
    StringToBoolean("    false") AS b2,
    StringToBoolean("false    ") AS b3
```  
  
 結果セットは次のようになります。  
  
```  
[{"b1": true, "b2": false, "b3": false}]
```  

無効な入力を使用した例を次に示します。

 ブール値では大文字と小文字が区別されるので、すべて小文字で記述する必要があります (つまり、"true" と "false")。

```  
SELECT 
    StringToBoolean("TRUE"),
    StringToBoolean("False")
```  

結果セットは次のようになります。  
  
```  
[{}]
``` 

渡された式はブール式として解析されます。これらの入力はブール型として評価されないため、undefined が返されます。

```  
SELECT 
    StringToBoolean("null"),
    StringToBoolean(undefined),
    StringToBoolean(NaN), 
    StringToBoolean(false), 
    StringToBoolean(true)
```  

結果セットは次のようになります。  
  
```  
[{}]
```  

####  <a name="bk_stringtonull"></a> StringToNull  
 null 値に変換された式を返します。 式を変換できない場合は、undefined を返します。  
  
 **構文**  
  
```  
StringToNull(<expr>)  
```  
  
 **引数**  
  
- `expr`  
  
   null 式として評価される有効なスカラー式です。
  
  **戻り値の型**  
  
  null 式または undefined を返します。  
  
  **例**  
  
  次の例では、異なる型間で StringToNull がどのように動作するかを示します。 

有効な入力を使用した例を次に示します。

 空白は "null" の前後のみで使用できます。

```  
SELECT 
    StringToNull("null") AS n1, 
    StringToNull("  null ") AS n2,
    IS_NULL(StringToNull("null   ")) AS n3
```  
  
 結果セットは次のようになります。  
  
```  
[{"n1": null, "n2": null, "n3": true}]
```  

無効な入力を使用した例を次に示します。

null 値では大文字と小文字が区別されるので、すべて小文字で記述する必要があります (つまり、"null")。

```  
SELECT    
    StringToNull("NULL"),
    StringToNull("Null")
```  
  
 結果セットは次のようになります。  
  
```  
[{}]
```  

渡された式は null 式として解析されます。これらの入力は null 型として評価されないため、undefined が返されます。

```  
SELECT    
    StringToNull("true"), 
    StringToNull(false), 
    StringToNull(undefined),
    StringToNull(NaN) 
```  
  
 結果セットは次のようになります。  
  
```  
[{}]
```  

####  <a name="bk_stringtonumber"></a> StringToNumber  
 数値に変換された式を返します。 式を変換できない場合は、undefined を返します。  
  
 **構文**  
  
```  
StringToNumber(<expr>)  
```  
  
 **引数**  
  
- `expr`  
  
   JSON 数値式として評価される有効なスカラー式です。 JSON の数値は整数または浮動小数点にする必要があります。 JSON 形式の詳細については、「[json.org](https://json.org/)」をご覧ください。  
  
  **戻り値の型**  
  
  数値式または undefined を返します。  
  
  **例**  
  
  次の例では、異なる型間で StringToNumber がどのように動作するかを示します。 

空白は数値の前後のみで使用できます。

```  
SELECT 
    StringToNumber("1.000000") AS num1, 
    StringToNumber("3.14") AS num2,
    StringToNumber("   60   ") AS num3, 
    StringToNumber("-1.79769e+308") AS num4
```  
  
 結果セットは次のようになります。  
  
```  
{{"num1": 1, "num2": 3.14, "num3": 60, "num4": -1.79769e+308}}
```  

JSON で有効な数値は、整数または浮動小数点数である必要があります。

```  
SELECT   
    StringToNumber("0xF")
```  
  
 結果セットは次のようになります。  
  
```  
{{}}
```  

渡された式は数値式として解析されます。これらの入力は数値型として評価されないため、undefined が返されます。 

```  
SELECT 
    StringToNumber("99     54"),   
    StringToNumber(undefined),
    StringToNumber("false"),
    StringToNumber(false),
    StringToNumber(" "),
    StringToNumber(NaN)
```  
  
 結果セットは次のようになります。  
  
```  
{{}}
```  

####  <a name="bk_stringtoobject"></a> StringToObject  
 オブジェクトに変換された式を返します。 式を変換できない場合は、undefined を返します。  
  
 **構文**  
  
```  
StringToObject(<expr>)  
```  
  
 **引数**  
  
- `expr`  
  
   JSON オブジェクト式として評価される有効なスカラー式です。 入れ子になった文字列値を有効にするには二重引用符で囲む必要があることにご注意ください。 JSON 形式の詳細については、「[json.org](https://json.org/)」をご覧ください。  
  
  **戻り値の型**  
  
  オブジェクト式または undefined を返します。  
  
  **例**  
  
  次の例では、異なる型間で StringToObject がどのように動作するかを示します。 
  
 有効な入力を使用した例を次に示します。

``` 
SELECT 
    StringToObject("{}") AS obj1, 
    StringToObject('{"A":[1,2,3]}') AS obj2,
    StringToObject('{"B":[{"b1":[5,6,7]},{"b2":8},{"b3":9}]}') AS obj3, 
    StringToObject("{\"C\":[{\"c1\":[5,6,7]},{\"c2\":8},{\"c3\":9}]}") AS obj4
``` 

結果セットは次のようになります。

```
[{"obj1": {}, 
  "obj2": {"A": [1,2,3]}, 
  "obj3": {"B":[{"b1":[5,6,7]},{"b2":8},{"b3":9}]},
  "obj4": {"C":[{"c1":[5,6,7]},{"c2":8},{"c3":9}]}}]
```

 無効な入力を使用した例を次に示します。
クエリ内では有効であっても、有効なオブジェクトとして解析されません。 オブジェクト文字列内の文字列は、"{\\"a\\":\\"str\\"}" のようにエスケープするか、'{"a": "str"}' のように一重引用符で囲む必要があります。

一重引用符で囲んだプロパティ名は有効な JSON ではありません。

``` 
SELECT 
    StringToObject("{'a':[1,2,3]}")
```

結果セットは次のようになります。

```  
[{}]
```  

引用符で囲まれていないプロパティ名は有効な JSON ではありません。

``` 
SELECT 
    StringToObject("{a:[1,2,3]}")
```

結果セットは次のようになります。

```  
[{}]
``` 

無効な入力を使用した例を次に示します。

 渡された式は JSON オブジェクトとして解析されます。これらの入力はオブジェクト型として評価されないため、undefined が返されます。

``` 
SELECT 
    StringToObject("}"),
    StringToObject("{"),
    StringToObject("1"),
    StringToObject(NaN), 
    StringToObject(false), 
    StringToObject(undefined)
``` 
 
 結果セットは次のようになります。

```
[{}]
```

####  <a name="bk_substring"></a> SUBSTRING  
 指定された文字のゼロベースの位置で始まる文字列式の一部を返し、指定された長さまたは文字列の末尾まで続きます。  
  
 **構文**  
  
```  
SUBSTRING(<str_expr>, <num_expr>, <num_expr>)  
```  
  
 **引数**  
  
- `str_expr`  
  
   任意の有効な文字列式です。  
  
- `num_expr`  
  
   開始および終了文字を示す任意の有効な数値式です。    
  
  **戻り値の型**  
  
  文字列式を返します。  
  
  **例**  
  
  次の例では、1 から始まる 1 文字の長さの "abc" の部分文字列を返します。  
  
```  
SELECT SUBSTRING("abc", 1, 1) AS substring  
```  
  
 結果セットは次のようになります。  
  
```  
[{"substring": "b"}]  
```  
####  <a name="bk_tostring"></a> ToString  
 スカラー式の文字列表現を返します。 
  
 **構文**  
  
```  
ToString(<expr>)
```  
  
 **引数**  
  
- `expr`  
  
   任意の有効なスカラー式です。  
  
  **戻り値の型**  
  
  文字列式を返します。  
  
  **例**  
  
  次の例では、異なる型間で ToString がどのように動作するかを示します。   
  
```  
SELECT 
    ToString(1.0000) AS str1, 
    ToString("Hello World") AS str2, 
    ToString(NaN) AS str3, 
    ToString(Infinity) AS str4,
    ToString(IS_STRING(ToString(undefined))) AS str5, 
    ToString(0.1234) AS str6, 
    ToString(false) AS str7, 
    ToString(undefined) AS str8
```  
  
 結果セットは次のようになります。  
  
```  
[{"str1": "1", "str2": "Hello World", "str3": "NaN", "str4": "Infinity", "str5": "false", "str6": "0.1234", "str7": "false"}]  
```  
 次のような入力があるものとします。
```  
{"Products":[{"ProductID":1,"Weight":4,"WeightUnits":"lb"},{"ProductID":2,"Weight":32,"WeightUnits":"kg"},{"ProductID":3,"Weight":400,"WeightUnits":"g"},{"ProductID":4,"Weight":8999,"WeightUnits":"mg"}]}
```    
 次の例は、CONCAT などの他の文字列関数を ToString とどのように一緒に使用できるかを示しています。   
 
```  
SELECT 
CONCAT(ToString(p.Weight), p.WeightUnits) 
FROM p in c.Products 
```  

結果セットは次のようになります。  
  
```  
[{"$1":"4lb" },
{"$1":"32kg"},
{"$1":"400g" },
{"$1":"8999mg" }]

```  
次のような入力があるものとします。
```
{"id":"08259","description":"Cereals ready-to-eat, KELLOGG, KELLOGG'S CRISPIX","nutrients":[{"id":"305","description":"Caffeine","units":"mg"},{"id":"306","description":"Cholesterol, HDL","nutritionValue":30,"units":"mg"},{"id":"307","description":"Sodium, NA","nutritionValue":612,"units":"mg"},{"id":"308","description":"Protein, ABP","nutritionValue":60,"units":"mg"},{"id":"309","description":"Zinc, ZN","nutritionValue":null,"units":"mg"}]}
```
次の例は、REPLACE などの他の文字列関数を ToString とどのように一緒に使用できるかを示しています。   
```
SELECT 
    n.id AS nutrientID,
    REPLACE(ToString(n.nutritionValue), "6", "9") AS nutritionVal
FROM food 
JOIN n IN food.nutrients
```
結果セットは次のようになります。  
 ```
[{"nutrientID":"305"},
{"nutrientID":"306","nutritionVal":"30"},
{"nutrientID":"307","nutritionVal":"912"},
{"nutrientID":"308","nutritionVal":"90"},
{"nutrientID":"309","nutritionVal":"null"}]
``` 
 
####  <a name="bk_trim"></a> TRIM  
 文字列式の先頭と末尾の空白を削除して返します。  
  
 **構文**  
  
```  
TRIM(<str_expr>)  
```  
  
 **引数**  
  
- `str_expr`  
  
   任意の有効な文字列式です。  
  
  **戻り値の型**  
  
  文字列式を返します。  
  
  **例**  
  
  次の例は、クエリ内での TRIM の使用方法を示しています。  
  
```  
SELECT TRIM("   abc") AS t1, TRIM("   abc   ") AS t2, TRIM("abc   ") AS t3, TRIM("abc") AS t4
```  
  
 結果セットは次のようになります。  
  
```  
[{"t1": "abc", "t2": "abc", "t3": "abc", "t4": "abc"}]  
``` 
####  <a name="bk_upper"></a> UPPER  
 文字列式の小文字データを大文字に変換して返します。  
  
 **構文**  
  
```  
UPPER(<str_expr>)  
```  
  
 **引数**  
  
- `str_expr`  
  
   任意の有効な文字列式です。  
  
  **戻り値の型**  
  
  文字列式を返します。  
  
  **例**  
  
  次の例は、クエリ内での UPPER の使用方法を示しています。  
  
```  
SELECT UPPER("Abc") AS upper  
```  
  
 結果セットは次のようになります。  
  
```  
[{"upper": "ABC"}]  
```

## <a id="array-functions"></a>配列関数

次のスカラー関数では、配列入力値に対して演算が実行され、数値、ブール値、または配列値が返されます。
  
||||  
|-|-|-|  
|[ARRAY_CONCAT](#bk_array_concat)|[ARRAY_CONTAINS](#bk_array_contains)|[ARRAY_LENGTH](#bk_array_length)|  
|[ARRAY_SLICE](#bk_array_slice)|||  
  
####  <a name="bk_array_concat"></a> ARRAY_CONCAT  
 2 つ以上の配列値を連結した結果である配列を返します。  
  
 **構文**  
  
```  
ARRAY_CONCAT (<arr_expr>, <arr_expr> [, <arr_expr>])  
```  
  
 **引数**  
  
- `arr_expr`  
  
   任意の有効な配列式です。  
  
  **戻り値の型**  
  
  配列式を返します。  
  
  **例**  
  
  次の例では、2 つの配列を結合する方法を示します。  
  
```  
SELECT ARRAY_CONCAT(["apples", "strawberries"], ["bananas"]) AS arrayConcat 
```  
  
 結果セットは次のようになります。  
  
```  
[{"arrayConcat": ["apples", "strawberries", "bananas"]}]  
```  
  
####  <a name="bk_array_contains"></a> ARRAY_CONTAINS  
配列に指定された値が含まれているかどうかを示すブール値を返します。 コマンド内でブール式を使用して、オブジェクトの部分一致または完全一致を確認できます。 

**構文**  
  
```  
ARRAY_CONTAINS (<arr_expr>, <expr> [, bool_expr])  
```  
  
 **引数**  
  
- `arr_expr`  
  
   任意の有効な配列式です。  
  
- `expr`  
  
   任意の有効な式です。  

- `bool_expr`  
  
   任意のブール式です。 'true' に設定されていて、指定された検索値がオブジェクトである場合、このコマンドで部分一致が確認されます (検索オブジェクトは、いずれかのオブジェクトのサブセットです)。 'false' に設定されている場合、このコマンドで配列内のすべてのオブジェクトの完全一致が確認されます。 指定しない場合の既定値は false です。 
  
  **戻り値の型**  
  
  ブール値を返します。  
  
  **例**  
  
  次の例では、ARRAY_CONTAINS を使用して、配列内のメンバーシップを確認する方法を示します。  
  
```  
SELECT   
           ARRAY_CONTAINS(["apples", "strawberries", "bananas"], "apples") AS b1,  
           ARRAY_CONTAINS(["apples", "strawberries", "bananas"], "mangoes") AS b2  
```  
  
 結果セットは次のようになります。  
  
```  
[{"b1": true, "b2": false}]  
```  

次の例では、ARRAY_CONTAINS を使用して、配列内 JSON の部分一致を確認する方法を示します。  
  
```  
SELECT  
    ARRAY_CONTAINS([{"name": "apples", "fresh": true}, {"name": "strawberries", "fresh": true}], {"name": "apples"}, true) AS b1, 
    ARRAY_CONTAINS([{"name": "apples", "fresh": true}, {"name": "strawberries", "fresh": true}], {"name": "apples"}) AS b2,
    ARRAY_CONTAINS([{"name": "apples", "fresh": true}, {"name": "strawberries", "fresh": true}], {"name": "mangoes"}, true) AS b3 
```  
  
 結果セットは次のようになります。  
  
```  
[{
  "b1": true,
  "b2": false,
  "b3": false
}] 
```  
  
####  <a name="bk_array_length"></a> ARRAY_LENGTH  
 指定された配列式の要素の数を返します。  
  
 **構文**  
  
```  
ARRAY_LENGTH(<arr_expr>)  
```  
  
 **引数**  
  
- `arr_expr`  
  
   任意の有効な配列式です。  
  
  **戻り値の型**  
  
  数値式を返します。  
  
  **例**  
  
  次の例では、ARRAY_LENGTH を使用して、配列の長さを取得する方法を示します。  
  
```  
SELECT ARRAY_LENGTH(["apples", "strawberries", "bananas"]) AS len  
```  
  
 結果セットは次のようになります。  
  
```  
[{"len": 3}]  
```  
  
####  <a name="bk_array_slice"></a> ARRAY_SLICE  
 配列式の一部を返します。
  
 **構文**  
  
```  
ARRAY_SLICE (<arr_expr>, <num_expr> [, <num_expr>])  
```  
  
 **引数**  
  
- `arr_expr`  
  
   任意の有効な配列式です。  
  
- `num_expr`  
  
   配列を開始する位置を示す 0 から始まる数値インデックス。 配列内の最後の要素を基準とした開始インデックスを指定するために負の値が使用されることがあり、つまり、-1 は配列の最後の要素を参照します。  

- `num_expr`  

   結果の配列内の要素の最大数。    

  **戻り値の型**  
  
  配列式を返します。  
  
  **例**  
  
  次の例では、ARRAY_SLICE を使用して、配列の別のスライスを取得する方法を示します。  
  
```  
SELECT
           ARRAY_SLICE(["apples", "strawberries", "bananas"], 1) AS s1,  
           ARRAY_SLICE(["apples", "strawberries", "bananas"], 1, 1) AS s2,
           ARRAY_SLICE(["apples", "strawberries", "bananas"], -2, 1) AS s3,
           ARRAY_SLICE(["apples", "strawberries", "bananas"], -2, 2) AS s4,
           ARRAY_SLICE(["apples", "strawberries", "bananas"], 1, 0) AS s5,
           ARRAY_SLICE(["apples", "strawberries", "bananas"], 1, 1000) AS s6,
           ARRAY_SLICE(["apples", "strawberries", "bananas"], 1, -100) AS s7      
  
```  
  
 結果セットは次のようになります。  
  
```  
[{  
           "s1": ["strawberries", "bananas"],   
           "s2": ["strawberries"],
           "s3": ["strawberries"],  
           "s4": ["strawberries", "bananas"], 
           "s5": [],
           "s6": ["strawberries", "bananas"],
           "s7": [] 
}]  
```  
## <a id="date-time-functions"></a>日付と時刻関数

次のスカラー関数では、UTC での現在の日付と時刻を、数値のタイムスタンプ (値はミリ秒単位の Unix エポック) または ISO 8601 形式に準拠した文字列という 2 つの形式で取得できます。 

|||
|-|-|
|[GETCURRENTDATETIME](#bk_get_current_date_time)|[GETCURRENTTIMESTAMP](#bk_get_current_timestamp)||

####  <a name="bk_get_current_date_time"></a> GETCURRENTDATETIME
 UTC での現在の日付と時刻を ISO 8601 文字列として返します。
  
 **構文**
  
```
GETCURRENTDATETIME ()
```
  
  **戻り値の型**
  
  UTC での現在の日付と時刻を ISO 8601 文字列値として返します。 

  これは YYYY-MM-DDThh:mm:ss.sssZ 形式で表されます。それぞれの意味は次のとおりです。
  
  |||
  |-|-|
  |YYYY|4 桁の年|
  |MM|2 桁の月 (例: 01 = 1 月)|
  |DD|2 桁の日付 (01 から 31)|
  |T|時間要素の開始を表します|
  |hh|2 桁の時間 (00 から 23)|
  |MM|2 桁の分 (00 から 59)|
  |ss|2 桁の秒 (00 から 59)|
  |.sss|3 桁の秒の小数部|
  |Z|UTC (協定世界時) 指定子||
  
  ISO 8601 形式の詳細については、「[ISO_8601](https://en.wikipedia.org/wiki/ISO_8601)」を参照してください。

  **解説**

  GETCURRENTDATETIME は非決定論的関数です。 
  
  返される結果は UTC (協定世界時) です。

  **例**  
  
  次の例は、GetCurrentDateTime 組み込み関数を使用して、UTC での現在の日付と時刻を取得する方法を示しています。
  
```  
SELECT GETCURRENTDATETIME() AS currentUtcDateTime
```  
  
 結果セットは次のようになります。
  
```  
[{
  "currentUtcDateTime": "2019-05-03T20:36:17.784Z"
}]  
```  

####  <a name="bk_get_current_timestamp"></a> GETCURRENTTIMESTAMP
 1970 年 1 月 1 日木曜日 00 時 00 分 00 秒から経過したミリ秒数を返します。 
  
 **構文**  
  
```  
GETCURRENTTIMESTAMP ()  
```  
  
  **戻り値の型**  
  
  Unix エポックから現在までに経過したミリ秒数を表す数値、つまり 1970 年 1 月 1 日木曜日 00 時 00 分 00 秒から経過したミリ秒数を返します。

  **解説**

  GETCURRENTTIMESTAMP は非決定論的関数です。
  
  返される結果は UTC (協定世界時) です。

  **例**  
  
  次の例は、GetCurrentTimestamp 組み込み関数を使用して、現在のタイムスタンプを取得する方法を示しています。
  
```  
SELECT GETCURRENTTIMESTAMP() AS currentUtcTimestamp
```  
  
 結果セットは次のようになります。
  
```  
[{
  "currentUtcTimestamp": 1556916469065
}]  
```

## <a id="spatial-functions"></a>空間関数

Cosmos DB は、以下の Open Geospatial Consortium (OGC) 組み込み関数を使った地理空間検索をサポートしています。 次のスカラー関数は、空間オブジェクト入力値に対して演算を実行し、数値またはブール値を返します。  
  
|||||
|-|-|-|-|
|[ST_DISTANCE](#bk_st_distance)|[ST_WITHIN](#bk_st_within)|[ST_INTERSECTS](#bk_st_intersects)|[ST_ISVALID](#bk_st_isvalid)|
|[ST_ISVALIDDETAILED](#bk_st_isvaliddetailed)||||
  
####  <a name="bk_st_distance"></a> ST_DISTANCE  
 2 つの GeoJSON Point、Polygon、または LineString 式間の距離を返します。  
  
 **構文**  
  
```  
ST_DISTANCE (<spatial_expr>, <spatial_expr>)  
```  
  
 **引数**  
  
- `spatial_expr`  
  
   有効な GeoJSON ポイント、多角形、または LineString オブジェクト式です。  
  
  **戻り値の型**  
  
  距離を含む数値式を返します。 これは、既定の参照システムのメートル単位で表されます。  
  
  **例**  
  
  次の例では、指定された場所の 30 km 圏内に存在するすべての世帯ドキュメントを ST_DISTANCE 組み込み関数で取得する方法を示します。 。  
  
```  
SELECT f.id   
FROM Families f   
WHERE ST_DISTANCE(f.location, {'type': 'Point', 'coordinates':[31.9, -4.8]}) < 30000  
```  
  
 結果セットは次のようになります。  
  
```  
[{  
  "id": "WakefieldFamily"  
}]  
```  
  
####  <a name="bk_st_within"></a> ST_WITHIN  
 最初の引数で指定された GeoJSON オブジェクト (Point、Polygon、または LineString) が 2 つ目の引数の GeoJSON (Point、Polygon、または LineString) 内に存在するかどうかを示すブール式を返します。  
  
 **構文**  
  
```  
ST_WITHIN (<spatial_expr>, <spatial_expr>)  
```  
  
 **引数**  
  
- `spatial_expr`  
  
   有効な GeoJSON ポイント、多角形、または LineString オブジェクト式です。  
 
- `spatial_expr`  
  
   有効な GeoJSON ポイント、多角形、または LineString オブジェクト式です。  
  
  **戻り値の型**  
  
  ブール値を返します。  
  
  **例**  
  
  次の例では、ST_WITHIN を使用して多角形内のすべての世帯ドキュメントを検索する方法を示します。  
  
```  
SELECT f.id   
FROM Families f   
WHERE ST_WITHIN(f.location, {  
    'type':'Polygon',   
    'coordinates': [[[31.8, -5], [32, -5], [32, -4.7], [31.8, -4.7], [31.8, -5]]]  
})  
```  
  
 結果セットは次のようになります。  
  
```  
[{ "id": "WakefieldFamily" }]  
```  

####  <a name="bk_st_intersects"></a> ST_INTERSECTS  
 最初の引数で指定された GeoJSON オブジェクト (Point、Polygon、または LineString) が 2 つ目の引数の GeoJSON (Point、Polygon、または LineString) と交差するかどうかを示すブール式を返します。  
  
 **構文**  
  
```  
ST_INTERSECTS (<spatial_expr>, <spatial_expr>)  
```  
  
 **引数**  
  
- `spatial_expr`  
  
   有効な GeoJSON ポイント、多角形、または LineString オブジェクト式です。  
 
- `spatial_expr`  
  
   有効な GeoJSON ポイント、多角形、または LineString オブジェクト式です。  
  
  **戻り値の型**  
  
  ブール値を返します。  
  
  **例**  
  
  次の例では、特定の多角形と交差するすべての領域を見つける方法を示します。  
  
```  
SELECT a.id
FROM Areas a
WHERE ST_INTERSECTS(a.location, {  
    'type':'Polygon',
    'coordinates': [[[31.8, -5], [32, -5], [32, -4.7], [31.8, -4.7], [31.8, -5]]]  
})  
```  
  
 結果セットは次のようになります。  
  
```  
[{ "id": "IntersectingPolygon" }]  
```  
  
####  <a name="bk_st_isvalid"></a> ST_ISVALID  
 指定された GeoJSON Point、Polygon、または LineString 式が有効かどうかを示すブール値を返します。  
  
 **構文**  
  
```  
ST_ISVALID(<spatial_expr>)  
```  
  
 **引数**
  
- `spatial_expr`  
  
   有効な GeoJSON ポイント、多角形、または LineString 式です。  
  
  **戻り値の型**  
  
  ブール式を返します。  
  
  **例**  
  
  次の例では、ST_VALID を使用してポイントが有効かどうかを確認する方法を示します。  
  
  たとえば、このポイントの緯度の値は有効な値の範囲 [-90, 90] に含まれていないので、クエリは false を返します。  
  
  GeoJSON 仕様では、最後に指定された座標ペアが、最初に指定された座標ペアとちょうど重なり、閉じた形状になっていることが、ポリゴンの条件となります。 ポリゴン内のポイントは、反時計回りに指定する必要があります。 時計回りに指定されたポリゴンは、その中の領域を逆にしたものを表します。  
  
```  
SELECT ST_ISVALID({ "type": "Point", "coordinates": [31.9, -132.8] }) AS b 
```  
  
 結果セットは次のようになります。  
  
```  
[{ "b": false }]  
```  
  
####  <a name="bk_st_isvaliddetailed"></a> ST_ISVALIDDETAILED  
 指定された GeoJSON Point、Polygon、または LineString 式が有効であるかどうかのブール値を含んだ JSON 値を返します。無効である場合はさらに、その理由が文字列値として返されます。  
  
 **構文**  
  
```  
ST_ISVALIDDETAILED(<spatial_expr>)  
```  
  
 **引数**  
  
- `spatial_expr`  
  
   有効な GeoJSON ポイントまたはポリゴン式です。  
  
  **戻り値の型**  
  
  指定された GeoJSON ポイントまたはポリゴン式が有効であるかどうかのブール値を含んだ JSON 値を返します。無効である場合はさらに、その理由が文字列値として返されます。  
  
  **例**  
  
  次の例では、ST_ISVALIDDETAILED を使用して有効性 (詳細) を確認する方法を示します。  
  
```  
SELECT ST_ISVALIDDETAILED({   
  "type": "Polygon",   
  "coordinates": [[ [ 31.8, -5 ], [ 31.8, -4.7 ], [ 32, -4.7 ], [ 32, -5 ] ]]  
}) AS b 
```  
  
 結果セットは次のようになります。  
  
```  
[{  
  "b": {
    "valid": false,
    "reason": "The Polygon input is not valid because the start and end points of the ring number 1 are not the same. Each ring of a polygon must have the same start and end points."   
  }  
}]  
```  

## <a name="next-steps"></a>次の手順

- [Azure Cosmos DB の概要](introduction.md)
- [UDF](sql-query-udfs.md)
- [集計](sql-query-aggregates.md)