---
title: クエリ アクセラレーション SQL 言語リファレンス
titleSuffix: Azure Storage
description: クエリ アクセラレーション SQL 構文を使用する方法について説明します。
services: storage
author: normesta
ms.service: storage
ms.topic: conceptual
ms.date: 09/09/2020
ms.author: normesta
ms.subservice: data-lake-storage-gen2
ms.reviewer: ereilebr
ms.openlocfilehash: 2eda67e377a3b61e696e732b916d788c00a18eae
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "95908780"
---
# <a name="query-acceleration-sql-language-reference"></a>クエリ アクセラレーション SQL 言語リファレンス

クエリ アクセラレーションでは、BLOB コンテンツに対するクエリを表現するための ANSI SQL に似た言語をサポートしています。  クエリ アクセラレーション SQL 言語は ANSI SQL のサブセットであり、サポートされるデータ型、演算子などのセットは限定されていますが、JSON などの階層型の半構造化データ形式に対するクエリをサポートするために、ANSI SQL を拡張している部分もあります。 

## <a name="select-syntax"></a>SELECT 構文

クエリ アクセラレーションでサポートされている唯一の SQL ステートメントは、SELECT ステートメントです。 この例では、式で true が返されるすべての行を返します。

```sql
SELECT * FROM table [WHERE expression] [LIMIT limit]
```

CSV 形式のデータの場合、*table* が `BlobStorage` である必要があります。  つまり、REST 呼び出しで指定されたどの BLOB に対してもクエリが実行されます。
JSON 形式のデータの場合、*table* は "テーブル記述子" です。   この記事の「[テーブル記述子](#table-descriptors)」セクションを参照してください。

次の例では、WHERE *expression* によって true が返される行ごとに、このステートメントが各プロジェクション式の評価によって作成される新しい行を返します。


```sql
SELECT expression [, expression …] FROM table [WHERE expression] [LIMIT limit]
```

次の例では、*expression* によって true が返される行それぞれでの集計計算 (例: 特定の列の平均値) が返されます。 

```sql
SELECT aggregate_expression FROM table [WHERE expression] [LIMIT limit]
```

次の例では、CSV 形式の BLOB の分割に適切なオフセットが返されます。  この記事の「[Sys.Split](#sys-split)」セクションを参照してください。

```sql
SELECT sys.split(split_size)FROM BlobStorage
```

<a id="data-types"></a>

## <a name="data-types"></a>データ型

|データ型|説明|
|---------|-------------------------------------------|
|INT      |64 ビット符号付き整数                     |
|FLOAT    |64 ビット ("倍精度") 浮動小数点。|
|STRING   |可変長 Unicode 文字列。            |
|timestamp|特定の時点。                           |
|BOOLEAN  |true または false                             |

CSV 形式のデータから値を読み取ると、すべての値が文字列として読み取られます。  文字列値は、キャスト式を使用して他の型に変換される場合があります。  値は、コンテキストに応じて、暗黙的に他の型にキャストされる場合があります。 詳細については、「[データ型の優先順位 (Transact-SQL)](/sql/t-sql/data-types/data-type-precedence-transact-sql)」を参照してください。

## <a name="expressions"></a>式

### <a name="referencing-fields"></a>フィールドの参照

JSON 形式のデータ、またはヘッダー行を含む CSV 形式のデータの場合は、フィールドは名前で参照できます。  フィールド名は、引用符で囲んでも囲まなくてもかまいません。 フィールド名を引用符で囲む場合は、二重引用符 (") で囲み、スペースを含めることができます。また、大文字と小文字が区別されます。  フィールド名を引用符で囲まない場合は、大文字と小文字が区別されず、特殊文字を含めることもできません。

また、CSV 形式のデータでは、アンダースコア (_) 文字を先頭に付けた序数によってフィールドを参照することもできます。  たとえば、最初のフィールドは _1 として参照し、11 番目のフィールドは _11 として参照することができます。  序数によるフィールドの参照は、ヘッダー行を含まない CSV 形式のデータに役立ちます。この場合、特定のフィールドを参照する唯一の方法は、序数によるものです。

### <a name="operators"></a>オペレーター

次の標準の SQL 演算子がサポートされています。

|演算子|説明|
|--|--|
|[=](/sql/t-sql/language-elements/equals-transact-sql)    |2 つの式の等価性を比較します (比較演算子)。|
|[!=](/sql/t-sql/language-elements/not-equal-to-transact-sql-exclamation)    |一方の式がもう一方の式と等しくないかどうかをテストします (比較演算子)。|
|[<>](/sql/t-sql/language-elements/not-equal-to-transact-sql-traditional)    |2 つの式を比較して "等しくない" かどうかを判定します (比較演算子)。|
|[<](/sql/t-sql/language-elements/less-than-transact-sql)    |2 つの式を比較して "より小さい" かどうかを判定します (比較演算子)。|
|[<=](/sql/t-sql/language-elements/less-than-or-equal-to-transact-sql)    |2 つの式を比較して "以下" であるかどうかを判定します (比較演算子)。|
|[>](/sql/t-sql/language-elements/greater-than-transact-sql)    |2 つの式を比較して "より大きい" かどうかを判定します (比較演算子)。 |
|[>=](/sql/t-sql/language-elements/greater-than-or-equal-to-transact-sql)    |2 つの式を比較して "以上" であるかどうかを判定します (比較演算子)。|
|[+](/sql/t-sql/language-elements/add-transact-sql)    |2 つの値を加算します。 この加算算術演算子を使用して、日付に日数を加算することもできます。|
|[-](/sql/t-sql/language-elements/subtract-transact-sql)    |2 つの値で減算を行います (算術減算演算子)。 |
|[/](/sql/t-sql/language-elements/divide-transact-sql)    |1 つの値を別の値で除算します (算術除算演算子)。|
|[*](/sql/t-sql/language-elements/multiply-transact-sql)    |2 つの式を乗算します (算術乗算演算子)。|
|[%](/sql/t-sql/language-elements/modulo-transact-sql)    |ある値を別の値で除算した結果の余りを返します。|
|[AND](/sql/t-sql/language-elements/bitwise-and-transact-sql)    |2 つの整数値の間でビットごとの論理積演算を実行します。|
|[OR](/sql/t-sql/language-elements/bitwise-or-transact-sql)    |Transact-SQL ステートメントの中で、バイナリ式に変換された 2 つの指定される整数値に対して、ビットごとの論理和演算を実行します。|
|[NOT](/sql/t-sql/language-elements/not-transact-sql)    |ブール値を否定します。|
|[CAST](/sql/t-sql/functions/cast-and-convert-transact-sql)    |あるデータ型の式を別のデータ型に変換します。|
|[BETWEEN](/sql/t-sql/language-elements/between-transact-sql)    |テスト範囲を指定します。|
|[IN](/sql/t-sql/language-elements/in-transact-sql)    |指定された値が、サブクエリまたは一覧内の値と一致するかどうかを判断します。|
|[NULLIF](/sql/t-sql/language-elements/nullif-transact-sql)    |指定された 2 つの式が等しい場合に NULL 値を返します。|
|[COALESCE](/sql/t-sql/language-elements/coalesce-transact-sql)    |引数を順番に評価し、NULL と評価されない最初の式の現在の値を返します。|

演算子の左右のデータ型が異なる場合は、こちらで指定されている規則に従って自動変換が実行されます: 「[データ型の優先順位 (Transact-SQL)](/sql/t-sql/data-types/data-type-precedence-transact-sql)」。

クエリ アクセラレーション SQL 言語では、その記事で説明されているデータ型のごく一部のみがサポートされています。  この記事の「[データ型](#data-types)」セクションを参照してください。

### <a name="casts"></a>キャスト

クエリ アクセラレーション SQL 言語では、こちらの規則に従って CAST 演算子がサポートされています: 「[データ型の変換 (データベース エンジン)](/sql/t-sql/data-types/data-type-conversion-database-engine)」。  

クエリ アクセラレーション SQL 言語では、その記事で説明されているデータ型の一部のみがサポートされています。  この記事の「[データ型](#data-types)」セクションを参照してください。

### <a name="string-functions"></a>文字列関数

クエリ アクセラレーション SQL 言語では、以下の標準の SQL 文字列関数がサポートされています。

|機能|説明|
|--|--|
|CHAR_LENGTH    | 文字列式のデータ型が文字である場合は、文字列式の文字の長さを返します。それ以外の場合は、文字列式の長さをバイト単位 (ビット数を 8 で割った数より小さい最小の整数) で返します (この関数は CHARACTER_LENGTH 関数と同じです)。|
|CHARACTER_LENGTH    |文字列式のデータ型が文字である場合は、文字列式の文字の長さを返します。それ以外の場合は、文字列式の長さをバイト単位 (ビット数を 8 で割った数より小さい最小の整数) で返します (この関数は CHAR_LENGTH 関数と同じです)。|
|[LOWER](/sql/t-sql/functions/lower-transact-sql)    |大文字のデータが小文字に変換された状態の文字式を返します。|
|[UPPER](/sql/t-sql/functions/upper-transact-sql)    |小文字データを大文字に変換して文字式を返します。|
|[SUBSTRING](/sql/t-sql/functions/substring-transact-sql)    |SQL Server で、文字、バイナリ、テキスト、またはイメージ型の式の一部を返します。|
|[TRIM](/sql/t-sql/functions/trim-transact-sql)    |文字列の先頭と末尾にあるスペース文字 (32) または他の指定した文字を削除します。|
|LEADING    |説明|
|TRAILING    |説明|

以下にいくつか例を示します。

|機能|例|結果|
|---|---|---|
|CHARACTER_LENGTH|``SELECT CHARACTER_LENGTH('abcdefg') from BlobStorage`` |``7``|
|CHAR_LENGTH|``SELECT CHAR_LENGTH(_1) from BlobStorage``|``1``|
|LOWER|``SELECT LOWER('AbCdEfG') from BlobStorage``|``abcdefg``|
|UPPER|``SELECT UPPER('AbCdEfG') from BlobStorage``|``ABCDEFG``|
|SUBSTRING|``SUBSTRING('123456789', 1, 5)``|``23456``|
|TRIM|``TRIM(BOTH '123' FROM '1112211Microsoft22211122')``|``Microsoft``|

### <a name="date-functions"></a>データ関数

次の標準の SQL 日付関数がサポートされています。

``DATE_ADD``, ``DATE_DIFF``, ``EXTRACT``, ``TO_STRING``, ``TO_TIMESTAMP``.

現在、[標準 IS08601 の日付形式](https://www.w3.org/TR/NOTE-datetime)はすべて変換されます。 

#### <a name="date_add-function"></a>DATE_ADD 関数

クエリ アクセラレーション SQL 言語では、``DATE_ADD`` 関数で年、月、日、時間、分、秒をサポートしています。

例 :

``sql DATE_ADD(datepart, quantity, timestamp) DATE_ADD('minute', 1, CAST('2017-01-02T03:04:05.006Z' AS TIMESTAMP)
```

#### DATE_DIFF function

The query acceleration SQL language supports year, month, day, hour, minute, second for the ``DATE_DIFF`` function.

```sql
DATE_DIFF(datepart, timestamp, timestamp)
DATE_DIFF('hour','2018-11-09T00:00+05:30','2018-11-09T01:00:23-08:00') 
```

#### <a name="extract-function"></a>EXTRACT 関数

``DATE_ADD`` 関数でサポートされている日付部分以外の EXTRACT の場合、クエリ アクセラレーション SQL 言語では、日付部分として timezone_hour と timezone_minute をサポートしています。

例 :

```sql
EXTRACT(datepart FROM timestampstring)
EXTRACT(YEAR FROM '2010-01-01T')
```

#### <a name="to_string-function"></a>TO_STRING 関数

例 :

```sql
TO_STRING(TimeStamp , format)
TO_STRING(CAST('1969-07-20T20:18Z' AS TIMESTAMP),  'MMMM d, y')
```

次の表では、``TO_STRING`` 関数の出力形式を指定するために使用できる文字列について説明します。

|[書式設定文字列]    |出力                               |
|-----------------|-------------------------------------|
|yy               |2 桁形式の年 - 1999 であれば "99"|
|y                |4 桁形式の年               |
|yyyy             |4 桁形式の年               |
|M                |年の月 - 1                    |
|mm               |0 を埋め込んだ月 – 01               |
|MMM              |省略形。 年の月 - JAN            |
|MMMM             |正式な月名 - May                      |
|d                |月の日 (1 から 31)                  |
|dd               |0 を埋め込んだ月の日 (01 から 31)     |
|a                |AM または PM                             |
|h                |日の時間 (1 から 12)                   |
|hh               |0 を埋め込んだ日の時間 (01-12)     |
|H                |日の時間 (0 から 23)                   |
|HH               |0 を埋め込んだ日の時間 (00 から 23)      |
|m                |時間の分 (0-59)                |
|mm               |0 を埋め込んだ分 (00 から 59)           |
|s                |分の秒 (0 から 59)             |
|ss               |0 を埋め込んだ秒 (00 から 59)          |
|S                |秒の小数部分 (0.1 から 0.9)        |
|SS               |秒の小数部分 (0.01 から 0.99)      |
|SSS              |秒の小数部分 (0.001 から 0.999)    |
|X                |時間のオフセット                      |
|XX または XXXX       |時間と分のオフセット (+0430)  |
|XXX または XXXXX     |時間と分のオフセット (-07:00) |
|x                |時間のオフセット (7)                  |
|xx または xxxx       |時間と分のオフセット (+0530)    |
|Xxx または xxxxx     |時間と分のオフセット (+05:30)   |

#### <a name="to_timestamp-function"></a>TO_TIMESTAMP 関数

IS08601 形式のみがサポートされます。

例 :

```sql
TO_TIMESTAMP(string)
TO_TIMESTAMP('2007T')
```

> [!NOTE]
> ``UTCNOW`` 関数を使用して、システム時刻を取得することもできます。


## <a name="aggregate-expressions"></a>集計式

SELECT ステートメントには、1 つ以上のプロジェクション式または 1 つの集計式を含めることができます。  次の集計式がサポートされています。

|式|説明|
|--|--|
|[COUNT(\*)](https://docs.microsoft.com/sql/t-sql/functions/count-transact-sql)    |述語式と一致したレコードの数を返します。|
|[COUNT(expression)](https://docs.microsoft.com/sql/t-sql/functions/count-transact-sql)    |式が null 以外のレコードの数を返します。|
|[AVERAGE(expression)](https://docs.microsoft.com/sql/t-sql/functions/avg-transact-sql)    |式の null 以外の値の平均を返します。|
|[MIN(expression)](https://docs.microsoft.com/sql/t-sql/functions/min-transact-sql)    |式の null 以外の最小値を返します。|
|[MAX(expression)](https://docs.microsoft.com/sql/t-sql/functions/max-transact-sql)    |式の null 以外の最大値を返します。|
|[SUM(expression)](https://docs.microsoft.com/sql/t-sql/functions/sum-transact-sql)    |式の null 以外のすべての値の合計を返します。|

### <a name="missing"></a>MISSING

``IS MISSING`` 演算子は、クエリ アクセラレーション SQL 言語でサポートされる唯一の非標準の演算子です。  JSON データでは、特定の入力レコードにフィールドがない場合、``IS MISSING`` 式フィールドはブール値 true に評価されます。

<a id="table-descriptors"></a>

## <a name="table-descriptors"></a>テーブル記述子

CSV データの場合、テーブル名は常に `BlobStorage` です。  次に例を示します。

```sql
SELECT * FROM BlobStorage
```

JSON データの場合は、追加のオプションも使用できます。

```sql
SELECT * FROM BlobStorage[*].path
```

これにより、JSON データのサブセットに対するクエリが可能になります。

JSON クエリでは、FROM 句の中にパスを記述することができます。 これらのパスは、JSON データのサブセットを解析するのに役立ちます。 これらのパスは、JSON 配列およびオブジェクトの値を参照できます。

それでは、この詳細を理解するために例を見てみましょう。

これはサンプル データです。

```json
{
  "id": 1,
  "name": "mouse",
  "price": 12.5,
  "tags": [
    "wireless",
    "accessory"
  ],
  "dimensions": {
    "length": 3,
    "width": 2,
    "height": 2
  },
  "weight": 0.2,
  "warehouses": [
    {
      "latitude": 41.8,
      "longitude": -87.6
    }
  ]
}
```

興味があるのは、上のデータの `warehouses` JSON オブジェクトだけかもしれません。 `warehouses` オブジェクトは JSON 配列型であるため、FROM 句でこれを記述できます。 サンプル クエリは、次のようになります。

```sql
SELECT latitude FROM BlobStorage[*].warehouses[*]
```

このクエリでは、すべてのフィールドを取得しますが、選択するのは緯度のみです。

`dimensions` JSON オブジェクト値のみにアクセスしたい場合は、クエリでそのオブジェクトを参照することができます。 次に例を示します。

```sql
SELECT length FROM BlobStorage[*].dimensions
```

これにより、アクセスは `dimensions` オブジェクトのメンバーに制限されます。 JSON フィールドの他のメンバーや JSON オブジェクトの内部値にアクセスする場合は、次の例に示すようなクエリを使用します。

```sql
SELECT weight,warehouses[0].longitude,id,tags[1] FROM BlobStorage[*]
```

> [!NOTE]
> BlobStorage と BlobStorage[\*] はどちらも、オブジェクト全体を参照します。 ただし、FROM 句にパスを含めた場合は、BlobStorage[\*].path を使用する必要があります。

<a id="sys-split"></a>

## <a name="syssplit"></a>Sys.Split

これは特殊な形式の SELECT ステートメントであり、CSV 形式のデータに対してのみ使用できます。

```sql
SELECT sys.split(split_size)FROM BlobStorage
```

このステートメントを使用するのは、CSV データ レコードを数回に分けてダウンロードして処理する場合です。 そうすることで、一度にすべてのレコードをダウンロードするのではなく、レコードを並列処理できます。 このステートメントでは、CSV ファイルのレコードが返されません。 代わりに、バッチ サイズのコレクションが返されます。 その後、各バッチ サイズを使用して、データ レコードのバッチを取得できます。 

各バッチに含めるバイト数を指定するには、*split_size* パラメーターを使用します。 たとえば、一度に 10 MB のデータのみを処理する場合、ステートメントは `SELECT sys.split(10485760)FROM BlobStorage` のようになります。これは、10 MB が 10,485,760 バイトであるためです。 各バッチには、10 MB に収まる数だけのレコードが含まれます。 

ほとんどの場合、各バッチのサイズは、指定した数よりも若干大きくなります。 これは、バッチに部分的なレコードを含めることができないためです。 しきい値の終わりになる前にバッチ内の最後のレコードが開始されると、レコード全体を含めることができるようにバッチが大きくなります。 最後のバッチのサイズは、通常、指定したサイズよりも小さくなる可能性があります。

>[!NOTE]
> split_size は、少なくとも 10 MB (10,485,760) である必要があります。

## <a name="see-also"></a>関連項目

- [Azure Data Lake Storage のクエリ アクセラレーション](data-lake-storage-query-acceleration.md)
- [Azure Data Lake Storage のクエリ アクセラレーションを使用してデータをフィルター処理する](data-lake-storage-query-acceleration-how-to.md)