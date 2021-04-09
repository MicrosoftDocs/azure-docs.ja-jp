---
title: Date_Bucket (Transact-SQL) - Azure SQL Edge
description: Azure SQL Edge での Date_Bucket の使用について説明します
keywords: Date_Bucket, SQL Edge
services: sql-edge
ms.service: sql-edge
ms.topic: reference
author: SQLSourabh
ms.author: sourabha
ms.reviewer: sstein
ms.date: 09/03/2020
ms.openlocfilehash: 9d81419721e94a2e181f094c0e0e64b1b23544a8
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "93073521"
---
# <a name="date_bucket-transact-sql"></a>Date_Bucket (Transact-SQL)

基準のパラメーターが指定されていない場合は、この関数は、`origin` パラメーターまたは `1900-01-01 00:00:00.000` の既定の基準値で定義されたタイムスタンプから、各 datetime バケットの開始に対応する datetime 値を返します。 

Transact-SQL の日付と時刻のデータ型および関数の概要については、「[日付と時刻のデータ型および関数 &#40;Transact-SQL&#41;](/sql/t-sql/functions/date-and-time-data-types-and-functions-transact-sql/)」を参照してください。

[Transact-SQL 構文表記規則](/sql/t-sql/language-elements/transact-sql-syntax-conventions-transact-sql/)

## <a name="syntax"></a>構文

```sql
DATE_BUCKET (datePart, number, date, origin)
```

## <a name="arguments"></a>引数

*datePart*

"number" パラメーターで使用される "*日付*" の部分。 例: Year、month、minute、second など。

> [!NOTE]
> `DATE_BUCKET` では、*datepPart* 引数に対し、ユーザー定義変数に相当するものは受け付けられません。
  
|*datePart*|省略形|  
|---|---|
|**day**|**dd**、**d**|  
|**week**|**wk**、**ww**| 
|**month**|**mm**、**m**|
|**quarter**|**qq**、**q**|  
|**year**|**yy**、**yyyy**|  
|**hour**|**hh**|  
|**minute**|**mi**、**n**|  
|**second**|**ss**、**s**|  
|**millisecond**|**ms**|  

*number*

*datePart* 引数と組み合わされてバケットの幅を決定する整数値。 これは、基準日時からの dataPart バケットの幅を表します。 **`This argument cannot be a negative integer value`**. 

*date*

次のいずれかの値に解決できる式。

+ **date**
+ **datetime**
+ **datetimeoffset**
+ **datetime2**
+ **smalldatetime**
+ **time**

*date* については、上記のデータ型に解決される場合は、列式、式、またはユーザー定義変数が `DATE_BUCKET` で受け付けられます。

**元のドメイン** 

次のいずれかの値に解決できるオプションの式:

+ **date**
+ **datetime**
+ **datetimeoffset**
+ **datetime2**
+ **smalldatetime**
+ **time**

`Origin` のデータ型は、`Date` パラメーターのデータ型と一致する必要があります。 

`DATE_BUCKET` では、基準値が関数に指定されていない場合は、既定の日付の基準値として `1900-01-01 00:00:00.000`、つまり 1900 年 1 月 1 日 (月) 午前 0:00 が使用されます。

## <a name="return-type"></a>戻り値の型

このメソッドの戻り値のデータ型は動的です。 戻り値の型は、`date` に与えられた引数によって異なります。 有効な入力データ型が `date` に指定された場合、`DATE_BUCKET` からは同じデータ型が返されます。 `date` パラメーターに対して文字列リテラルを指定した場合は、`DATE_BUCKET` でエラーが発生します。

## <a name="return-values"></a>戻り値

### <a name="understanding-the-output-from-date_bucket"></a>`DATE_BUCKET` からの出力について

`Date_Bucket` からは、datePart および number パラメーターに対応する、最も新しい日付または時刻の値が返されます。 たとえば、次の式では、既定の基準日時 `1900-01-01 00:00:00.000` からの 1 週間のバケットに基づいて出力が計算されるため、`Date_Bucket` から返される出力値は `2020-04-13 00:00:00.0000000` になります。 値 `2020-04-13 00:00:00.0000000` は、基準値 `1900-01-01 00:00:00.000` から 6276 週です。 

```sql
declare @date datetime2 = '2020-04-15 21:22:11'
Select DATE_BUCKET(wk, 1, @date)
```

以下のすべての式に対して、同じ出力値 `2020-04-13 00:00:00.0000000` が返されます。 これは、`2020-04-13 00:00:00.0000000` は基準の日付から 6276 週であり、6276 は 2、3、4、6 で割り切れるためです。

```sql
declare @date datetime2 = '2020-04-15 21:22:11'
Select DATE_BUCKET(wk, 2, @date)
Select DATE_BUCKET(wk, 3, @date)
Select DATE_BUCKET(wk, 4, @date)
Select DATE_BUCKET(wk, 6, @date)
```

下の式の出力は、既定の基準の日時 `1900-01-01 00:00:00.000` から 6275 週である `2020-04-06 00:00:00.0000000` になります。

```sql
declare @date datetime2 = '2020-04-15 21:22:11'
Select DATE_BUCKET(wk, 5, @date)
```

下の式の出力は、指定された基準の日時 `2019-01-01 00:00:00` から 75 週である `2020-06-09 00:00:00.0000000` になります。

```sql
declare @date datetime2 = '2020-06-15 21:22:11'
declare @origin datetime2 = '2019-01-01 00:00:00'
Select DATE_BUCKET(wk, 5, @date, @origin)
```

## <a name="datepart-argument"></a>datepart 引数

**dayofyear**、**day**、および **weekday** は同じ値を返します。 *-各日付構成要素とその省略形は、同じ値を返します。*
  
## <a name="number-argument"></a>number 引数

*number* 引数は、正の **int** 値の範囲を超えることはできません。 次のステートメントでは、*number* の引数が **int** の範囲を 1 超えています。 以下のステートメントからは、次のメッセージが返されます: "`Msg 8115, Level 16, State 2, Line 2. Arithmetic overflow error converting expression to data type int."`
  
```sql
declare @date datetime2 = '2020-04-30 00:00:00'
Select DATE_BUCKET(dd, 2147483648, @date)
```  

`Date_Bucket` 関数に負の値の number を渡すと、次のエラーが返されます。 

```sql
Msg 9834, Level 16, State 1, Line 1
Invalid bucket width value passed to date_bucket function. Only positive values are allowed.
````

## <a name="date-argument"></a>date 引数  

`DATE_BUCKET` からは、`date` 引数のデータ型に対応するベース値が返されます。 次の例では、datetime2 データ型の出力値が返されます。 

```sql
Select DATE_BUCKET(dd, 10, SYSUTCDATETIME())
```

## <a name="origin-argument"></a>origin 引数  

`origin` と `date` の引数のデータ型は同じである必要があります。 異なるデータ型を使用すると、エラーが生成されます。

## <a name="remarks"></a>解説

次の句で `DATE_BUCKET` を使用します。

+ GROUP BY
+ HAVING
+ ORDER BY
+ SELECT \<list>
+ WHERE

## <a name="examples"></a>例

### <a name="a-calculating-date_bucket-with-a-bucket-width-of-1-from-the-origin-time"></a>A. バケットの幅を 1 にして基準日時からの Date_Bucket を計算する

以下の各ステートメントでは、基準日時からのバケット幅 1 で *date_bucket* がインクリメントされます。

```sql
declare @date datetime2 = '2020-04-30 21:21:21'
Select 'Week',  DATE_BUCKET(wk, 1, @date)
Union All
Select 'Day',  DATE_BUCKET(dd, 1, @date)
Union All
Select 'Hour',  DATE_BUCKET(hh, 1, @date)
Union All
Select 'Minutes',  DATE_BUCKET(mi, 1, @date)
Union All
Select 'Seconds',  DATE_BUCKET(ss, 1, @date)
```

結果セットは次のようになります。

```sql
Week    2020-04-27 00:00:00.0000000
Day     2020-04-30 00:00:00.0000000
Hour    2020-04-30 21:00:00.0000000
Minutes 2020-04-30 21:21:00.0000000
Seconds 2020-04-30 21:21:21.0000000
```

### <a name="b-using-expressions-as-arguments-for-the-number-and-date-parameters"></a>B. number パラメーターと date パラメーターの引数として式を使用する

次の例では、*number* パラメーターと *date* パラメーターの引数として、さまざまな種類の式を使用しています。 これらの例は、"AdventureWorksDW2017" データベースを使用して構築されています。
  
#### <a name="specifying-user-defined-variables-as-number-and-date"></a>number および date にユーザー定義変数を指定する  

次の例では、*number* と *date* の引数としてユーザー定義変数を指定しています。
  
```sql
DECLARE @days int = 365,
        @datetime datetime2 = '2000-01-01 01:01:01.1110000'; /* 2000 was a leap year */;  
SELECT Date_Bucket(day, @days, @datetime);
```

結果セットは次のようになります。

```sql
---------------------------
1999-12-08 00:00:00.0000000

(1 row affected)
```  

#### <a name="specifying-a-column-as-date"></a>列を date として指定する

次の例では、週単位の日付バケットでグループ化された OrderQuantity と UnitPrice の合計が計算されています。
  
```sql
SELECT
    Date_Bucket(week, 1 ,cast(Shipdate as datetime2)) AS ShippedDateBucket
    ,Sum(OrderQuantity)  As SumOrderQuantity
    ,Sum(UnitPrice) As SumUnitPrice
FROM dbo.FactInternetSales FIS
where Shipdate between '2011-01-03 00:00:00.000' and '2011-02-28 00:00:00.000'
Group by Date_Bucket(week, 1 ,cast(Shipdate as datetime2))
order by 1
```  

結果セットは次のようになります。
  
```sql
ShippedDateBucket           SumOrderQuantity SumUnitPrice
--------------------------- ---------------- ---------------------
2011-01-03 00:00:00.0000000 21               65589.7546
2011-01-10 00:00:00.0000000 27               89938.5464
2011-01-17 00:00:00.0000000 31               104404.9064
2011-01-24 00:00:00.0000000 36               118525.6846
2011-01-31 00:00:00.0000000 39               123555.431
2011-02-07 00:00:00.0000000 35               109342.351
2011-02-14 00:00:00.0000000 32               107804.8964
2011-02-21 00:00:00.0000000 37               119456.3428
2011-02-28 00:00:00.0000000 9                28968.6982
```  

#### <a name="specifying-scalar-system-function-as-date"></a>スカラー システム関数を date として指定する

この例では、*date* の `SYSDATETIME` を指定しています。 返される厳密な値は、ステートメント実行の日時によって変わります。
  
```sql
SELECT Date_Bucket(wk, 10, SYSDATETIME());  
```  

結果セットは次のようになります。

```sql
---------------------------
2020-03-02 00:00:00.0000000

(1 row affected)
```  

#### <a name="specifying-scalar-subqueries-and-scalar-functions-as-number-and-date"></a>number および date にスカラー サブクエリやスカラー関数を指定する

次の例では、*number* と *date* の引数として、スカラー サブクエリ (`MAX(OrderDate)`) を使用しています。 `(SELECT top 1 CustomerKey FROM dbo.DimCustomer where GeographyKey > 100)` は、値リストから *number* 引数を選択する方法を紹介するために用意した、数値パラメーターの架空の引数です。
  
```sql
SELECT DATE_BUCKET(week,(SELECT top 1 CustomerKey FROM dbo.DimCustomer where GeographyKey > 100),  
    (SELECT MAX(OrderDate) FROM dbo.FactInternetSales));  
```  
  
#### <a name="specifying-numeric-expressions-and-scalar-system-functions-as-number-and-date"></a>number および date として数値式やスカラー システム関数を指定する

この例では、数値式 ((10/2)) を使用し、number と date の引数としてスカラー システム関数 (SYSDATETIME) を使用しています。
  
```sql
SELECT Date_Bucket(week,(10/2), SYSDATETIME());
```

#### <a name="specifying-an-aggregate-window-function-as-number"></a>number として集計関数を指定する

次の例では、*number* の引数として集計関数を使用しています。
  
```sql
Select 
    DISTINCT DATE_BUCKET(day, 30, Cast([shipdate] as datetime2)) as DateBucket,
    First_Value([SalesOrderNumber]) OVER (Order by DATE_BUCKET(day, 30, Cast([shipdate] as datetime2))) as First_Value_In_Bucket,
    Last_Value([SalesOrderNumber]) OVER (Order by DATE_BUCKET(day, 30, Cast([shipdate] as datetime2))) as Last_Value_In_Bucket
    from [dbo].[FactInternetSales]
Where ShipDate between '2011-01-03 00:00:00.000' and '2011-02-28 00:00:00.000'
order by DateBucket
GO  
``` 
### <a name="c-using-a-non-default-origin-value"></a>C. 既定の基準値以外を使用する

この例では、既定の基準値以外を使用して、日付バケットを生成します。 

```sql
declare @date datetime2 = '2020-06-15 21:22:11'
declare @origin datetime2 = '2019-01-01 00:00:00'
Select DATE_BUCKET(hh, 2, @date, @origin)
```

## <a name="see-also"></a>関連項目

[CAST および CONVERT &#40;Transact-SQL&#41;](/sql/t-sql/functions/cast-and-convert-transact-sql/)
