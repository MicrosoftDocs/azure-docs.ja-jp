---
title: マッピング データ フローでの式関数
description: マッピング データ フローでの式関数について説明します。
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 03/10/2021
ms.openlocfilehash: 0e60ac6da55c11d45e8b691b4883b0f5f93a2498
ms.sourcegitcommit: 18a91f7fe1432ee09efafd5bd29a181e038cee05
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/16/2021
ms.locfileid: "103563934"
---
# <a name="data-transformation-expressions-in-mapping-data-flow"></a>マッピング データ フローでのデータ変換式

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

## <a name="expression-functions"></a>式関数

Data Factory では、マッピング データ フロー機能の式言語を使用して、データ変換が構成されます。
___
### <code>abs</code>
<code><b>abs(<i>&lt;value1&gt;</i> : number) => number</b></code><br/><br/>
数値の絶対値。  
* ``abs(-20) -> 20``  
* ``abs(10) -> 10``  
___   
### <code>acos</code>
<code><b>acos(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
逆コサイン値を計算します。  
* ``acos(1) -> 0.0``  
___
### <code>add</code>
<code><b>add(<i>&lt;value1&gt;</i> : any, <i>&lt;value2&gt;</i> : any) => any</b></code><br/><br/>
文字列または数値のペアを追加します。 日付に日数を加算します。 タイムスタンプに期間を追加します。 別の配列に類似するタイプの配列を追加します。 \+ 演算子と同じです。  
* ``add(10, 20) -> 30``  
* ``10 + 20 -> 30``  
* ``add('ice', 'cream') -> 'icecream'``  
* ``'ice' + 'cream' + ' cone' -> 'icecream cone'``  
* ``add(toDate('2012-12-12'), 3) -> toDate('2012-12-15')``  
* ``toDate('2012-12-12') + 3 -> toDate('2012-12-15')``  
* ``[10, 20] + [30, 40] -> [10, 20, 30, 40]``  
* ``toTimestamp('2019-02-03 05:19:28.871', 'yyyy-MM-dd HH:mm:ss.SSS') + (days(1) + hours(2) - seconds(10)) -> toTimestamp('2019-02-04 07:19:18.871', 'yyyy-MM-dd HH:mm:ss.SSS')``  
___
### <code>addDays</code>
<code><b>addDays(<i>&lt;date/timestamp&gt;</i> : datetime, <i>&lt;days to add&gt;</i> : integral) => datetime</b></code><br/><br/>
日付またはタイムスタンプに日数を加算します。 日付に対する + 演算子と同じです。  
* ``addDays(toDate('2016-08-08'), 1) -> toDate('2016-08-09')``  
___
### <code>addMonths</code>
<code><b>addMonths(<i>&lt;date/timestamp&gt;</i> : datetime, <i>&lt;months to add&gt;</i> : integral, [<i>&lt;value3&gt;</i> : string]) => datetime</b></code><br/><br/>
日付またはタイムスタンプに月数を加算します。 必要に応じて、タイムゾーンを渡すことができます。  
* ``addMonths(toDate('2016-08-31'), 1) -> toDate('2016-09-30')``  
* ``addMonths(toTimestamp('2016-09-30 10:10:10'), -1) -> toTimestamp('2016-08-31 10:10:10')``  
___
### <code>and</code>
<code><b>and(<i>&lt;value1&gt;</i> : boolean, <i>&lt;value2&gt;</i> : boolean) => boolean</b></code><br/><br/>
論理 AND 演算子です。 && と同じです。  
* ``and(true, false) -> false``  
* ``true && false -> false``  
___
### <code>asin</code>
<code><b>asin(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
逆サイン値を計算します。  
* ``asin(0) -> 0.0``  
___
### <code>atan</code>
<code><b>atan(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
逆タンジェント値を計算します。  
* ``atan(0) -> 0.0``  
___
### <code>atan2</code>
<code><b>atan2(<i>&lt;value1&gt;</i> : number, <i>&lt;value2&gt;</i> : number) => double</b></code><br/><br/>
座標で指定された、平面の正の x 軸と点の間の角度をラジアンで返します。  
* ``atan2(0, 0) -> 0.0``  
___
### <code>between</code>
<code><b>between(<i>&lt;value1&gt;</i> : any, <i>&lt;value2&gt;</i> : any, <i>&lt;value3&gt;</i> : any) => boolean</b></code><br/><br/>
最初の値が他の 2 つの値の範囲内にあるかどうかを確認します。 数値、文字列値、datetime 値を比較できます * ``between(10, 5, 24)``
* ``true``
* ``between(currentDate(), currentDate() + 10, currentDate() + 20)``
* ``false``
___
### <code>bitwiseAnd</code>
<code><b>bitwiseAnd(<i>&lt;value1&gt;</i> : integral, <i>&lt;value2&gt;</i> : integral) => integral</b></code><br/><br/>
整数型間でのビット AND 演算子です。 & 演算子と同じです * ``bitwiseAnd(0xf4, 0xef)``
* ``0xe4``
* ``(0xf4 & 0xef)``
* ``0xe4``
___
### <code>bitwiseOr</code>
<code><b>bitwiseOr(<i>&lt;value1&gt;</i> : integral, <i>&lt;value2&gt;</i> : integral) => integral</b></code><br/><br/>
整数型間でのビット OR 演算子です。 | 演算子と同じです * ``bitwiseOr(0xf4, 0xef)``
* ``0xff``
* ``(0xf4 | 0xef)``
* ``0xff``
___
### <code>bitwiseXor</code>
<code><b>bitwiseXor(<i>&lt;value1&gt;</i> : any, <i>&lt;value2&gt;</i> : any) => any</b></code><br/><br/>
整数型間でのビット OR 演算子です。 | 演算子と同じです * ``bitwiseXor(0xf4, 0xef)``
* ``0x1b``
* ``(0xf4 ^ 0xef)``
* ``0x1b``
* ``(true ^ false)``
* ``true``
* ``(true ^ true)``
* ``false``
___
### <code>blake2b</code>
<code><b>blake2b(<i>&lt;value1&gt;</i> : integer, <i>&lt;value2&gt;</i> : any, ...) => string</b></code><br/><br/>
さまざまなプリミティブ データ型の列セットの Blake2 ハッシュを計算します。ビット長として指定できるのは、8 から 512 の範囲内の 8 の倍数のみです。 行のフィンガープリントを計算するために使用できます * ``blake2b(256, 'gunchus', 8.2, 'bojjus', true, toDate('2010-4-4'))``
* ``'c9521a5080d8da30dffb430c50ce253c345cc4c4effc315dab2162dac974711d'``
___
### <code>blake2bBinary</code>
<code><b>blake2bBinary(<i>&lt;value1&gt;</i> : integer, <i>&lt;value2&gt;</i> : any, ...) => binary</b></code><br/><br/>
さまざまなプリミティブ データ型の列セットの Blake2 ハッシュを計算します。ビット長として指定できるのは、8 から 512 の範囲内の 8 の倍数のみです。 行のフィンガープリントを計算するために使用できます * ``blake2bBinary(256, 'gunchus', 8.2, 'bojjus', true, toDate('2010-4-4'))``
* ``unHex('c9521a5080d8da30dffb430c50ce253c345cc4c4effc315dab2162dac974711d')``
___
### <code>case</code>
<code><b>case(<i>&lt;condition&gt;</i> : boolean, <i>&lt;true_expression&gt;</i> : any, <i>&lt;false_expression&gt;</i> : any, ...) => any</b></code><br/><br/>
交互条件に基づいて、2 つの値のいずれかを適用します。 入力数が偶数の場合、最後の条件でその他は既定で NULL になります。  
* ``case(10 + 20 == 30, 'dumbo', 'gumbo') -> 'dumbo'``  
* ``case(10 + 20 == 25, 'bojjus', 'do' < 'go', 'gunchus') -> 'gunchus'``  
* ``isNull(case(10 + 20 == 25, 'bojjus', 'do' > 'go', 'gunchus')) -> true``  
* ``case(10 + 20 == 25, 'bojjus', 'do' > 'go', 'gunchus', 'dumbo') -> 'dumbo'``  
___
### <code>cbrt</code>
<code><b>cbrt(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
数値の立方根を計算します。  
* ``cbrt(8) -> 2.0``  
___
### <code>ceil</code>
<code><b>ceil(<i>&lt;value1&gt;</i> : number) => number</b></code><br/><br/>
特定の数値以上の最小の整数を返します。  
* ``ceil(-0.1) -> 0``  
___
### <code>coalesce</code>
<code><b>coalesce(<i>&lt;value1&gt;</i> : any, ...) => any</b></code><br/><br/>
一連の入力から、最初の null でない値を返します。 すべての入力は同じ型である必要があります。  
* ``coalesce(10, 20) -> 10``  
* ``coalesce(toString(null), toString(null), 'dumbo', 'bo', 'go') -> 'dumbo'``  
___
### <code>collect</code>
<code><b>collect(<i>&lt;value1&gt;</i> : any) => array</b></code><br/><br/>
集計されたグループ内の式のすべての値を配列に収集します。 この処理中に構造体を収集し、別の構造体に変換できます。 項目の数はそのグループの行数と等しくなります。また、null 値を含めることができます。 収集する項目の数は少なくするようにします。  
* ``collect(salesPerson)``
* ``collect(firstName + lastName))``
* ``collect(@(name = salesPerson, sales = salesAmount) )``
___
### <code>columnNames</code>
<code><b>columnNames(<i>&lt;value1&gt;</i> : string) => array</b></code><br/><br/>
ストリームのすべての出力列の名前を取得します。 省略可能なストリーム名を 2 番目の引数として渡すことができます。  
* ``columnNames()``
* ``columnNames('DeriveStream')``
___
### <code>columns</code>
<code><b>columns([<i>&lt;stream name&gt;</i> : string]) => any</b></code><br/><br/>
ストリームのすべての出力列の値を取得します。 省略可能なストリーム名を 2 番目の引数として渡すことができます。   
* ``columns()``
* ``columns('DeriveStream')``
___
### <code>compare</code>
<code><b>compare(<i>&lt;value1&gt;</i> : any, <i>&lt;value2&gt;</i> : any) => integer</b></code><br/><br/>
同じ型の 2 つの値を比較します。 value1 < value2 の場合は負の整数、value1 == value2 の場合は 0、value1 > value2 の場合は正の値を返します。  
* ``(compare(12, 24) < 1) -> true``  
* ``(compare('dumbo', 'dum') > 0) -> true``  
___
### <code>concat</code>
<code><b>concat(<i>&lt;this&gt;</i> : string, <i>&lt;that&gt;</i> : string, ...) => string</b></code><br/><br/>
可変数の文字列を連結します。 文字列で使用する + 演算子と同じです。  
* ``concat('dataflow', 'is', 'awesome') -> 'dataflowisawesome'``  
* ``'dataflow' + 'is' + 'awesome' -> 'dataflowisawesome'``  
* ``isNull('sql' + null) -> true``  
___
### <code>concatWS</code>
<code><b>concatWS(<i>&lt;separator&gt;</i> : string, <i>&lt;this&gt;</i> : string, <i>&lt;that&gt;</i> : string, ...) => string</b></code><br/><br/>
可変数の文字列を区切り記号を使用して連結します。 最初のパラメーターは区切り記号です。  
* ``concatWS(' ', 'dataflow', 'is', 'awesome') -> 'dataflow is awesome'``  
* ``isNull(concatWS(null, 'dataflow', 'is', 'awesome')) -> true``  
* ``concatWS(' is ', 'dataflow', 'awesome') -> 'dataflow is awesome'``  
___
### <code>contains</code>
<code><b>contains(<i>&lt;value1&gt;</i> : array, <i>&lt;value2&gt;</i> : unaryfunction) => boolean</b></code><br/><br/>
指定された配列内のいずれかの要素が、指定された述語で true と評価される場合に true を返します。 contains は、述語関数の 1 つの要素への参照を #item として予期します。  
* ``contains([1, 2, 3, 4], #item == 3) -> true``  
* ``contains([1, 2, 3, 4], #item > 5) -> false``  
___
### <code>cos</code>
<code><b>cos(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
コサイン値を計算します。  
* ``cos(10) -> -0.8390715290764524``  
___
### <code>cosh</code>
<code><b>cosh(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
値の双曲線コサインを計算します。  
* ``cosh(0) -> 1.0``  
___
### <code>crc32</code>
<code><b>crc32(<i>&lt;value1&gt;</i> : any, ...) => long</b></code><br/><br/>
さまざまなプリミティブ データ型の列セットの CRC32 ハッシュを指定のビット長で計算します。ビット長として指定できるのは、0(256)、224、256、384、512 の値のみです。 行のフィンガープリントを計算するために使用できます。  
* ``crc32(256, 'gunchus', 8.2, 'bojjus', true, toDate('2010-4-4')) -> 3630253689L``  
___
### <code>currentDate</code>
<code><b>currentDate([<i>&lt;value1&gt;</i> : string]) => date</b></code><br/><br/>
このジョブの実行を開始する現在の日付を取得します。 省略可能なタイムゾーンを 'GMT'、'PST'、'UTC'、'America/Cayman' の形式で渡せます。 ローカル タイムゾーンが既定値として使用されます。 使用可能な形式については、Java の `SimpleDateFormat` クラスを参照してください。 [https://docs.oracle.com/javase/8/docs/api/java/text/SimpleDateFormat.html](https://docs.oracle.com/javase/8/docs/api/java/text/SimpleDateFormat.html). 
* ``currentDate() == toDate('2250-12-31') -> false``  
* ``currentDate('PST')  == toDate('2250-12-31') -> false``  
* ``currentDate('America/New_York')  == toDate('2250-12-31') -> false``  
___
### <code>currentTimestamp</code>
<code><b>currentTimestamp() => timestamp</b></code><br/><br/>
ジョブの実行を開始する現在のタイムスタンプをローカル タイムゾーンを使用して取得します。  
* ``currentTimestamp() == toTimestamp('2250-12-31 12:12:12') -> false``  
___
### <code>currentUTC</code>
<code><b>currentUTC([<i>&lt;value1&gt;</i> : string]) => timestamp</b></code><br/><br/>
現在のタイムスタンプを UTC で取得します。 現在の時刻がクラスターのタイム ゾーンとは異なるタイムゾーンで解釈されるようにする場合は、省略可能なタイム ゾーンを 'GMT'、'PST'、'UTC'、'America/Cayman' の形式で渡すことができます。 既定値は現在のタイムゾーンです。 使用可能な形式については、Java の `SimpleDateFormat` クラスを参照してください。 [https://docs.oracle.com/javase/8/docs/api/java/text/SimpleDateFormat.html](https://docs.oracle.com/javase/8/docs/api/java/text/SimpleDateFormat.html). UTC 時刻を別のタイムゾーンに変換するには、`fromUTC()` を使用します。  
* ``currentUTC() == toTimestamp('2050-12-12 19:18:12') -> false``  
* ``currentUTC() != toTimestamp('2050-12-12 19:18:12') -> true``  
* ``fromUTC(currentUTC(), 'Asia/Seoul') != toTimestamp('2050-12-12 19:18:12') -> true``  
___
### <code>dayOfMonth</code>
<code><b>dayOfMonth(<i>&lt;value1&gt;</i> : datetime) => integer</b></code><br/><br/>
指定した日付から、その月の何日目かを取得します。  
* ``dayOfMonth(toDate('2018-06-08')) -> 8``  
___
### <code>dayOfWeek</code>
<code><b>dayOfWeek(<i>&lt;value1&gt;</i> : datetime) => integer</b></code><br/><br/>
指定した日付から、曜日を取得します。 1 - 日曜日、2 - 月曜日...、7 - 土曜日。  
* ``dayOfWeek(toDate('2018-06-08')) -> 6``  
___
### <code>dayOfYear</code>
<code><b>dayOfYear(<i>&lt;value1&gt;</i> : datetime) => integer</b></code><br/><br/>
指定した日付から、その年の何日目かを取得します。  
* ``dayOfYear(toDate('2016-04-09')) -> 100``  
___
### <code>days</code>
<code><b>days(<i>&lt;value1&gt;</i> : integer) => long</b></code><br/><br/>
ミリ秒単位での日数の期間。  
* ``days(2) -> 172800000L``  
___
### <code>degrees</code>
<code><b>degrees(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
ラジアンを角度に変換します。  
* ``degrees(3.141592653589793) -> 180``  
___
### <code>divide</code>
<code><b>divide(<i>&lt;value1&gt;</i> : any, <i>&lt;value2&gt;</i> : any) => any</b></code><br/><br/>
数値のペアを除算します。 `/` 演算子と同じです。  
* ``divide(20, 10) -> 2``  
* ``20 / 10 -> 2``  
___
### <code>endsWith</code>
<code><b>endsWith(<i>&lt;string&gt;</i> : string, <i>&lt;substring to check&gt;</i> : string) => boolean</b></code><br/><br/>
文字列が指定した文字列で終了しているかをチェックします。  
* ``endsWith('dumbo', 'mbo') -> true``  
___
### <code>equals</code>
<code><b>equals(<i>&lt;value1&gt;</i> : any, <i>&lt;value2&gt;</i> : any) => boolean</b></code><br/><br/>
等価比較演算子。 == 演算子と同じです。  
* ``equals(12, 24) -> false``  
* ``12 == 24 -> false``  
* ``'bad' == 'bad' -> true``  
* ``isNull('good' == toString(null)) -> true``  
* ``isNull(null == null) -> true``  
___
### <code>equalsIgnoreCase</code>
<code><b>equalsIgnoreCase(<i>&lt;value1&gt;</i> : string, <i>&lt;value2&gt;</i> : string) => boolean</b></code><br/><br/>
大文字と小文字の区別を無視する等価比較演算子。 <=> 演算子と同じです。  
* ``'abc'<=>'Abc' -> true``  
* ``equalsIgnoreCase('abc', 'Abc') -> true``  
___
### <code>escape</code>
<code><b>escape(<i>&lt;string_to_escape&gt;</i> : string, <i>&lt;format&gt;</i> : string) => string</b></code><br/><br/>
形式に従って、文字列をエスケープします。 使用できる形式のリテラル値は、'json'、'xml'、'ecmascript'、'html'、'java' です。
___
### <code>factorial</code>
<code><b>factorial(<i>&lt;value1&gt;</i> : number) => long</b></code><br/><br/>
数値の階乗を計算します。  
* ``factorial(5) -> 120``  
___
### <code>false</code>
<code><b>false() => boolean</b></code><br/><br/>
常に false 値を返します。 'false' という名前の列がある場合は、関数 `syntax(false())` を使用します。  
* ``(10 + 20 > 30) -> false``  
* ``(10 + 20 > 30) -> false()``
___
### <code>floor</code>
<code><b>floor(<i>&lt;value1&gt;</i> : number) => number</b></code><br/><br/>
特定の数値以下の最大の整数を返します。  
* ``floor(-0.1) -> -1``  
___
### <code>fromBase64</code>
<code><b>fromBase64(<i>&lt;value1&gt;</i> : string) => string</b></code><br/><br/>
指定された文字列を base64 でエンコードします。  
* ``fromBase64('Z3VuY2h1cw==') -> 'gunchus'``  
___
### <code>fromUTC</code>
<code><b>fromUTC(<i>&lt;value1&gt;</i> : timestamp, [<i>&lt;value2&gt;</i> : string]) => timestamp</b></code><br/><br/>
UTC からタイムスタンプに変換します。 必要に応じて、タイムゾーンを "GMT"、"PST"、"UTC"、"America/Cayman" の形式で渡すことができます。 既定値は現在のタイムゾーンです。 使用可能な形式については、Java の `SimpleDateFormat` クラスを参照してください。 https://docs.oracle.com/javase/8/docs/api/java/text/SimpleDateFormat.html.  
* ``fromUTC(currentTimestamp()) == toTimestamp('2050-12-12 19:18:12') -> false``  
* ``fromUTC(currentTimestamp(), 'Asia/Seoul') != toTimestamp('2050-12-12 19:18:12') -> true``  
___
### <code>greater</code>
<code><b>greater(<i>&lt;value1&gt;</i> : any, <i>&lt;value2&gt;</i> : any) => boolean</b></code><br/><br/>
比較超過演算子。 > 演算子と同じです。  
* ``greater(12, 24) -> false``  
* ``('dumbo' > 'dum') -> true``  
* ``(toTimestamp('2019-02-05 08:21:34.890', 'yyyy-MM-dd HH:mm:ss.SSS') > toTimestamp('2019-02-03 05:19:28.871', 'yyyy-MM-dd HH:mm:ss.SSS')) -> true``  
___
### <code>greaterOrEqual</code>
<code><b>greaterOrEqual(<i>&lt;value1&gt;</i> : any, <i>&lt;value2&gt;</i> : any) => boolean</b></code><br/><br/>
比較 (以上) 演算子。 >= 演算子と同じです。  
* ``greaterOrEqual(12, 12) -> true``  
* ``('dumbo' >= 'dum') -> true``  
___
### <code>greatest</code>
<code><b>greatest(<i>&lt;value1&gt;</i> : any, ...) => any</b></code><br/><br/>
null 値をスキップした入力値のリストの中の最大値を返します。 すべての入力が null の場合は null を返します。  
* ``greatest(10, 30, 15, 20) -> 30``  
* ``greatest(10, toInteger(null), 20) -> 20``  
* ``greatest(toDate('2010-12-12'), toDate('2011-12-12'), toDate('2000-12-12')) -> toDate('2011-12-12')``  
* ``greatest(toTimestamp('2019-02-03 05:19:28.871', 'yyyy-MM-dd HH:mm:ss.SSS'), toTimestamp('2019-02-05 08:21:34.890', 'yyyy-MM-dd HH:mm:ss.SSS')) -> toTimestamp('2019-02-05 08:21:34.890', 'yyyy-MM-dd HH:mm:ss.SSS')``  
___
### <code>hasColumn</code>
<code><b>hasColumn(<i>&lt;column name&gt;</i> : string, [<i>&lt;stream name&gt;</i> : string]) => boolean</b></code><br/><br/>
ストリームでの名前で列の値をチェックします。 省略可能なストリーム名を 2 番目の引数として渡すことができます。 設計時にわかっている列名は、その名前だけで処理する必要があります。 計算入力はサポートされていませんが、パラメーター置換を使用することができます。  
* ``hasColumn('parent')``  
___
### <code>hour</code>
<code><b>hour(<i>&lt;value1&gt;</i> : timestamp, [<i>&lt;value2&gt;</i> : string]) => integer</b></code><br/><br/>
タイムスタンプから時間の値を取得します。 省略可能なタイムゾーンを 'GMT'、'PST'、'UTC'、'America/Cayman' の形式で渡せます。 ローカル タイムゾーンが既定値として使用されます。 使用可能な形式については、Java の `SimpleDateFormat` クラスを参照してください。 https://docs.oracle.com/javase/8/docs/api/java/text/SimpleDateFormat.html.  
* ``hour(toTimestamp('2009-07-30 12:58:59')) -> 12``  
* ``hour(toTimestamp('2009-07-30 12:58:59'), 'PST') -> 12``  
___
### <code>hours</code>
<code><b>hours(<i>&lt;value1&gt;</i> : integer) => long</b></code><br/><br/>
ミリ秒単位での時間数の期間。  
* ``hours(2) -> 7200000L``  
___
### <code>iif</code>
<code><b>iif(<i>&lt;condition&gt;</i> : boolean, <i>&lt;true_expression&gt;</i> : any, [<i>&lt;false_expression&gt;</i> : any]) => any</b></code><br/><br/>
条件に基づいて、2 つの値のいずれかを適用します。 その他が指定されていない場合は、NULL と見なされます。 両方の値は互換性がなければなりません (数値、文字列...)。* ``iif(10 + 20 == 30, 'dumbo', 'gumbo') -> 'dumbo'``  
* ``iif(10 > 30, 'dumbo', 'gumbo') -> 'gumbo'``  
* ``iif(month(toDate('2018-12-01')) == 12, 345.12, 102.67) -> 345.12``  
___
### <code>iifNull</code>
<code><b>iifNull(<i>&lt;value1&gt;</i> : any, [<i>&lt;value2&gt;</i> : any], ...) => any</b></code><br/><br/>
最初のパラメーターが null かどうかを確認します。 null 以外の場合は、最初のパラメーターが返されます。 null の場合は、2 番目のパラメーターが返されます。 3 つのパラメーターが指定されている場合、動作は iif(isNull(value1), value2, value3) と同じであり、最初の値が null でない場合は 3 番目のパラメーターが返されます。  
* ``iifNull(10, 20) -> 10``  
* ``iifNull(null, 20, 40) -> 20``  
* ``iifNull('azure', 'data', 'factory') -> 'factory'``  
* ``iifNull(null, 'data', 'factory') -> 'data'``  
___
### <code>in</code>
<code><b>in(<i>&lt;array of items&gt;</i> : array, <i>&lt;item to find&gt;</i> : any) => boolean</b></code><br/><br/>
項目が配列内にあるかどうかをチェックします。  
* ``in([10, 20, 30], 10) -> true``  
* ``in(['good', 'kid'], 'bad') -> false``  
___
### <code>initCap</code>
<code><b>initCap(<i>&lt;value1&gt;</i> : string) => string</b></code><br/><br/>
すべての単語の最初の文字を大文字に変換します。 単語は、空白文字で区切られているものとして識別されます。  
* ``initCap('cool iceCREAM') -> 'Cool Icecream'``  
___
### <code>instr</code>
<code><b>instr(<i>&lt;string&gt;</i> : string, <i>&lt;substring to find&gt;</i> : string) => integer</b></code><br/><br/>
文字列内の部分文字列の位置 (1 を基準とする) を見つけます。 見つからない場合は 0 が返されます。  
* ``instr('dumbo', 'mbo') -> 3``  
* ``instr('microsoft', 'o') -> 5``  
* ``instr('good', 'bad') -> 0``  
___
### <code>isDelete</code>
<code><b>isDelete([<i>&lt;value1&gt;</i> : integer]) => boolean</b></code><br/><br/>
行が削除用にマークされているかどうかをチェックします。 1 つ以上の入力ストリームを取る変換は、ストリームの (1 から始まる) インデックスを渡すことができます。 ストリーム インデックスは 1 または 2 のいずれかである必要があり、既定値は 1 です。  
* ``isDelete()``  
* ``isDelete(1)``  
___
### <code>isError</code>
<code><b>isError([<i>&lt;value1&gt;</i> : integer]) => boolean</b></code><br/><br/>
行がエラーとしてマークされているかどうかをチェックします。 1 つ以上の入力ストリームを取る変換は、ストリームの (1 から始まる) インデックスを渡すことができます。 ストリーム インデックスは 1 または 2 のいずれかである必要があり、既定値は 1 です。  
* ``isError()``  
* ``isError(1)``  
___
### <code>isIgnore</code>
<code><b>isIgnore([<i>&lt;value1&gt;</i> : integer]) => boolean</b></code><br/><br/>
行を無視するようにマークされているかどうかをチェックします。 1 つ以上の入力ストリームを取る変換は、ストリームの (1 から始まる) インデックスを渡すことができます。 ストリーム インデックスは 1 または 2 のいずれかである必要があり、既定値は 1 です。  
* ``isIgnore()``  
* ``isIgnore(1)``  
___
### <code>isInsert</code>
<code><b>isInsert([<i>&lt;value1&gt;</i> : integer]) => boolean</b></code><br/><br/>
行が挿入用にマークされているかどうかをチェックします。 1 つ以上の入力ストリームを取る変換は、ストリームの (1 から始まる) インデックスを渡すことができます。 ストリーム インデックスは 1 または 2 のいずれかである必要があり、既定値は 1 です。  
* ``isInsert()``  
* ``isInsert(1)``  
___
### <code>isMatch</code>
<code><b>isMatch([<i>&lt;value1&gt;</i> : integer]) => boolean</b></code><br/><br/>
行がルックアップで一致するかどうかをチェックします。 1 つ以上の入力ストリームを取る変換は、ストリームの (1 から始まる) インデックスを渡すことができます。 ストリーム インデックスは 1 または 2 のいずれかである必要があり、既定値は 1 です。  
* ``isMatch()``  
* ``isMatch(1)``  
___
### <code>isNull</code>
<code><b>isNull(<i>&lt;value1&gt;</i> : any) => boolean</b></code><br/><br/>
値が NULL かどうかをチェックします。  
* ``isNull(NULL()) -> true``  
* ``isNull('') -> false``  
___
### <code>isUpdate</code>
<code><b>isUpdate([<i>&lt;value1&gt;</i> : integer]) => boolean</b></code><br/><br/>
行が更新用にマークされているかどうかをチェックします。 1 つ以上の入力ストリームを取る変換は、ストリームの (1 から始まる) インデックスを渡すことができます。 ストリーム インデックスは 1 または 2 のいずれかである必要があり、既定値は 1 です。  
* ``isUpdate()``  
* ``isUpdate(1)``  
___
### <code>isUpsert</code>
<code><b>isUpsert([<i>&lt;value1&gt;</i> : integer]) => boolean</b></code><br/><br/>
行が挿入用にマークされているかどうかをチェックします。 1 つ以上の入力ストリームを取る変換は、ストリームの (1 から始まる) インデックスを渡すことができます。 ストリーム インデックスは 1 または 2 のいずれかである必要があり、既定値は 1 です。  
* ``isUpsert()``  
* ``isUpsert(1)``  
___
### <code>lastDayOfMonth</code>
<code><b>lastDayOfMonth(<i>&lt;value1&gt;</i> : datetime) => date</b></code><br/><br/>
指定した日付から、その月の最後の日を取得します。  
* ``lastDayOfMonth(toDate('2009-01-12')) -> toDate('2009-01-31')``  
___
### <code>least</code>
<code><b>least(<i>&lt;value1&gt;</i> : any, ...) => any</b></code><br/><br/>
比較 (以下) 演算子。 <= 演算子と同じです。  
* ``least(10, 30, 15, 20) -> 10``  
* ``least(toDate('2010-12-12'), toDate('2011-12-12'), toDate('2000-12-12')) -> toDate('2000-12-12')``  
___
### <code>left</code>
<code><b>left(<i>&lt;string to subset&gt;</i> : string, <i>&lt;number of characters&gt;</i> : integral) => string</b></code><br/><br/>
インデックス 1 から開始して指定した文字数の部分文字列を抽出します。 SUBSTRING(str, 1, n) と同じです。  
* ``left('bojjus', 2) -> 'bo'``  
* ``left('bojjus', 20) -> 'bojjus'``  
___
### <code>length</code>
<code><b>length(<i>&lt;value1&gt;</i> : string) => integer</b></code><br/><br/>
文字列の長さを返します。  
* ``length('dumbo') -> 5``  
___
### <code>lesser</code>
<code><b>lesser(<i>&lt;value1&gt;</i> : any, <i>&lt;value2&gt;</i> : any) => boolean</b></code><br/><br/>
比較未満演算子。 < 演算子と同じです。  
* ``lesser(12, 24) -> true``  
* ``('abcd' < 'abc') -> false``  
* ``(toTimestamp('2019-02-03 05:19:28.871', 'yyyy-MM-dd HH:mm:ss.SSS') < toTimestamp('2019-02-05 08:21:34.890', 'yyyy-MM-dd HH:mm:ss.SSS')) -> true``  
___
### <code>lesserOrEqual</code>
<code><b>lesserOrEqual(<i>&lt;value1&gt;</i> : any, <i>&lt;value2&gt;</i> : any) => boolean</b></code><br/><br/>
比較 (以下) 演算子。 <= 演算子と同じです。  
* ``lesserOrEqual(12, 12) -> true``  
* ``('dumbo' <= 'dum') -> false``  
___
### <code>levenshtein</code>
<code><b>levenshtein(<i>&lt;from string&gt;</i> : string, <i>&lt;to string&gt;</i> : string) => integer</b></code><br/><br/>
2 つの文字列の間のレーベンシュタイン距離を取得します。  
* ``levenshtein('boys', 'girls') -> 4``  
___
### <code>like</code>
<code><b>like(<i>&lt;string&gt;</i> : string, <i>&lt;pattern match&gt;</i> : string) => boolean</b></code><br/><br/>
パターンは文字通り一致する文字列です。 次の特殊文字は例外です。_ は入力内の任意の 1 文字と一致します (posix 正規表現の . に 類似 (```posix```))、% は入力内の 0 文字以上と一致します (```posix``` 正規表現の * に類似)。
エスケープ文字は '' です。 エスケープ文字の前に特殊記号または別のエスケープ文字がある場合、その次の文字が文字通り照合されます。 その他の文字をエスケープするのは無効です。  
* ``like('icecream', 'ice%') -> true``  
___
### <code>locate</code>
<code><b>locate(<i>&lt;substring to find&gt;</i> : string, <i>&lt;string&gt;</i> : string, [<i>&lt;from index - 1-based&gt;</i> : integral]) => integer</b></code><br/><br/>
特定の位置から開始して、文字列内の部分文字列の位置 (1 を基準とする) を見つけます。 位置を省略すると、文字列の最初からとみなされます。 見つからない場合は 0 が返されます。  
* ``locate('mbo', 'dumbo') -> 3``  
* ``locate('o', 'microsoft', 6) -> 7``  
* ``locate('bad', 'good') -> 0``  
___
### <code>log</code>
<code><b>log(<i>&lt;value1&gt;</i> : number, [<i>&lt;value2&gt;</i> : number]) => double</b></code><br/><br/>
対数の値を計算します。 省略可能な底を指定できます。省略すると、オイラー数を返します (使用される場合)。  
* ``log(100, 10) -> 2``  
___
### <code>log10</code>
<code><b>log10(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
10 を底とする対数の値を計算します。  
* ``log10(100) -> 2``  
___
### <code>lower</code>
<code><b>lower(<i>&lt;value1&gt;</i> : string) => string</b></code><br/><br/>
文字列を小文字にします。  
* ``lower('GunChus') -> 'gunchus'``  
___
### <code>lpad</code>
<code><b>lpad(<i>&lt;string to pad&gt;</i> : string, <i>&lt;final padded length&gt;</i> : integral, <i>&lt;padding&gt;</i> : string) => string</b></code><br/><br/>
特定の長さになるまで、指定した埋め込み文字で文字列の左側を埋め込みます。 文字列が指定された長さ以上の場合は、その長さまで削除されます。  
* ``lpad('dumbo', 10, '-') -> '-----dumbo'``  
* ``lpad('dumbo', 4, '-') -> 'dumb'``  
* ``lpad('dumbo', 8, '<>') -> '<><dumbo'``  
___
### <code>ltrim</code>
<code><b>ltrim(<i>&lt;string to trim&gt;</i> : string, [<i>&lt;trim characters&gt;</i> : string]) => string</b></code><br/><br/>
文字列の先頭文字を左から削除します。 2 番目のパラメーターを指定しない場合、空白文字を削除します。 それ以外の場合は、2 番目のパラメーターに指定した任意の文字を削除します。  
* ``ltrim('  dumbo  ') -> 'dumbo  '``  
* ``ltrim('!--!du!mbo!', '-!') -> 'du!mbo!'``  
___
### <code>md5</code>
<code><b>md5(<i>&lt;value1&gt;</i> : any, ...) => string</b></code><br/><br/>
さまざまなプリミティブ データ型の列セットの MD5 ハッシュを計算し、32 文字の16 進数の文字列を返します。 行のフィンガープリントを計算するために使用できます。  
* ``md5(5, 'gunchus', 8.2, 'bojjus', true, toDate('2010-4-4')) -> '4ce8a880bd621a1ffad0bca905e1bc5a'``  
___
### <code>millisecond</code>
<code><b>millisecond(<i>&lt;value1&gt;</i> : timestamp, [<i>&lt;value2&gt;</i> : string]) => integer</b></code><br/><br/>
日付のミリ秒の値を取得します。 省略可能なタイムゾーンを 'GMT'、'PST'、'UTC'、'America/Cayman' の形式で渡せます。 ローカル タイムゾーンが既定値として使用されます。 使用可能な形式については、Java の `SimpleDateFormat` クラスを参照してください。 https://docs.oracle.com/javase/8/docs/api/java/text/SimpleDateFormat.html.  
* ``millisecond(toTimestamp('2009-07-30 12:58:59.871', 'yyyy-MM-dd HH:mm:ss.SSS')) -> 871``  
___
### <code>milliseconds</code>
<code><b>milliseconds(<i>&lt;value1&gt;</i> : integer) => long</b></code><br/><br/>
ミリ秒単位でのミリ秒数の期間。  
* ``milliseconds(2) -> 2L``  
___
### <code>minus</code>
<code><b>minus(<i>&lt;value1&gt;</i> : any, <i>&lt;value2&gt;</i> : any) => any</b></code><br/><br/>
数値を減算します。 日付から日数を減算します。 タイムスタンプから期間を減算します。 2 つのタイムスタンプを減算して、ミリ秒単位での差を取得します。 \- 演算子と同じです。  
* ``minus(20, 10) -> 10``  
* ``20 - 10 -> 10``  
* ``minus(toDate('2012-12-15'), 3) -> toDate('2012-12-12')``  
* ``toDate('2012-12-15') - 3 -> toDate('2012-12-12')``  
* ``toTimestamp('2019-02-03 05:19:28.871', 'yyyy-MM-dd HH:mm:ss.SSS') + (days(1) + hours(2) - seconds(10)) -> toTimestamp('2019-02-04 07:19:18.871', 'yyyy-MM-dd HH:mm:ss.SSS')``  
* ``toTimestamp('2019-02-03 05:21:34.851', 'yyyy-MM-dd HH:mm:ss.SSS') - toTimestamp('2019-02-03 05:21:36.923', 'yyyy-MM-dd HH:mm:ss.SSS') -> -2072``  
___
### <code>minute</code>
<code><b>minute(<i>&lt;value1&gt;</i> : timestamp, [<i>&lt;value2&gt;</i> : string]) => integer</b></code><br/><br/>
タイムスタンプから分の値を取得します。 省略可能なタイムゾーンを 'GMT'、'PST'、'UTC'、'America/Cayman' の形式で渡せます。 ローカル タイムゾーンが既定値として使用されます。 使用可能な形式については、Java の `SimpleDateFormat` クラスを参照してください。 https://docs.oracle.com/javase/8/docs/api/java/text/SimpleDateFormat.html.  
* ``minute(toTimestamp('2009-07-30 12:58:59')) -> 58``  
* ``minute(toTimestamp('2009-07-30 12:58:59'), 'PST') -> 58``  
___
### <code>minutes</code>
<code><b>minutes(<i>&lt;value1&gt;</i> : integer) => long</b></code><br/><br/>
ミリ秒単位での分数の期間。  
* ``minutes(2) -> 120000L``  
___
### <code>mod</code>
<code><b>mod(<i>&lt;value1&gt;</i> : any, <i>&lt;value2&gt;</i> : any) => any</b></code><br/><br/>
数値のペアの剰余です。 % 演算子と同じです。  
* ``mod(20, 8) -> 4``  
* ``20 % 8 -> 4``  
___
### <code>month</code>
<code><b>month(<i>&lt;value1&gt;</i> : datetime) => integer</b></code><br/><br/>
日付またはタイムスタンプから月の値を取得します。  
* ``month(toDate('2012-8-8')) -> 8``  
___
### <code>monthsBetween</code>
<code><b>monthsBetween(<i>&lt;from date/timestamp&gt;</i> : datetime, <i>&lt;to date/timestamp&gt;</i> : datetime, [<i>&lt;roundoff&gt;</i> : boolean], [<i>&lt;time zone&gt;</i> : string]) => double</b></code><br/><br/>
2 つの日付の間の月数を取得します。 計算を丸めることができいます。省略可能なタイムゾーンを 'GMT'、'PST'、'UTC'、'America/Cayman' の形式で渡せます。 ローカル タイムゾーンが既定値として使用されます。 使用可能な形式については、Java の `SimpleDateFormat` クラスを参照してください。 https://docs.oracle.com/javase/8/docs/api/java/text/SimpleDateFormat.html.  
* ``monthsBetween(toTimestamp('1997-02-28 10:30:00'), toDate('1996-10-30')) -> 3.94959677``  
___
### <code>multiply</code>
<code><b>multiply(<i>&lt;value1&gt;</i> : any, <i>&lt;value2&gt;</i> : any) => any</b></code><br/><br/>
数値のペアを乗算します。 \* 演算子と同じです。  
* ``multiply(20, 10) -> 200``  
* ``20 * 10 -> 200``  
___
### <code>negate</code>
<code><b>negate(<i>&lt;value1&gt;</i> : number) => number</b></code><br/><br/>
数値の符号を反転します。 正の数値を負の数値 (または、その逆) に変換します。  
* ``negate(13) -> -13``  
___
### <code>nextSequence</code>
<code><b>nextSequence() => long</b></code><br/><br/>
次の固有なシーケンスを返します。 数値は、パーティション内でのみ連続し、プレフィックスとして partitionId が付加されます。  
* ``nextSequence() == 12313112 -> false``  
___
### <code>normalize</code>
<code><b>normalize(<i>&lt;String to normalize&gt;</i> : string) => string</b></code><br/><br/>
文字列値を個別にアクセント記号が付いた Unicode 文字に正規化します。  
* ``regexReplace(normalize('bo²s'), `\p{M}`, '') -> 'boys'``  
___
### <code>not</code>
<code><b>not(<i>&lt;value1&gt;</i> : boolean) => boolean</b></code><br/><br/>
論理否定演算子。  
* ``not(true) -> false``  
* ``not(10 == 20) -> true``  
___
### <code>notEquals</code>
<code><b>notEquals(<i>&lt;value1&gt;</i> : any, <i>&lt;value2&gt;</i> : any) => boolean</b></code><br/><br/>
比較不等価演算子。 != 演算子と同じです。  
* ``12 != 24 -> true``  
* ``'bojjus' != 'bo' + 'jjus' -> false``  
___
### <code>notNull</code>
<code><b>notNull(<i>&lt;value1&gt;</i> : any) => boolean</b></code><br/><br/>
値が NULL 以外であるかどうかをチェックします。  
* ``notNull(NULL()) -> false``  
* ``notNull('') -> true``  
___
### <code>null</code>
<code><b>null() => null</b></code><br/><br/>
NULL 値を返します。 "null" という名前の列がある場合は、関数 `syntax(null())` を使用します。 これを使用する操作はすべて NULL になります。  
* ``isNull('dumbo' + null) -> true``  
* ``isNull(10 * null) -> true``  
* ``isNull('') -> false``  
* ``isNull(10 + 20) -> false``  
* ``isNull(10/0) -> true``  
___
### <code>or</code>
<code><b>or(<i>&lt;value1&gt;</i> : boolean, <i>&lt;value2&gt;</i> : boolean) => boolean</b></code><br/><br/>
論理 OR 演算子です。 || と同じです。  
* ``or(true, false) -> true``  
* ``true || false -> true``  
___
### <code>pMod</code>
<code><b>pMod(<i>&lt;value1&gt;</i> : any, <i>&lt;value2&gt;</i> : any) => any</b></code><br/><br/>
数値のペアの正の剰余です。  
* ``pmod(-20, 8) -> 4``  
___
### <code>partitionId</code>
<code><b>partitionId() => integer</b></code><br/><br/>
入力行が含まれている現在のパーティション ID を返します。  
* ``partitionId()``  
___
### <code>power</code>
<code><b>power(<i>&lt;value1&gt;</i> : number, <i>&lt;value2&gt;</i> : number) => double</b></code><br/><br/>
数値を別の数値でべき乗します。  
* ``power(10, 2) -> 100``  
___
### <code>random</code>
<code><b>random(<i>&lt;value1&gt;</i> : integral) => long</b></code><br/><br/>
パーティション内に省略可能なシード値を指定すると、乱数を返します。 シード値は固定値である必要があり、乱数値を生成するために partitionId と組み合わせて使用されます。* ``random(1) == 1 -> false``
___
### <code>regexExtract</code>
<code><b>regexExtract(<i>&lt;string&gt;</i> : string, <i>&lt;regex to find&gt;</i> : string, [<i>&lt;match group 1-based index&gt;</i> : integral]) => string</b></code><br/><br/>
指定された正規表現パターンに一致する部分文字列を抽出します。 最後のパラメーターは、一致グループを識別し、省略すると既定値として 1 が使用されます。 エスケープせずに文字列を照合するには、`<regex>` (バック クォート) を使用します。  
* ``regexExtract('Cost is between 600 and 800 dollars', '(\\d+) and (\\d+)', 2) -> '800'``  
* ``regexExtract('Cost is between 600 and 800 dollars', `(\d+) and (\d+)`, 2) -> '800'``  
___
### <code>regexMatch</code>
<code><b>regexMatch(<i>&lt;string&gt;</i> : string, <i>&lt;regex to match&gt;</i> : string) => boolean</b></code><br/><br/>
指定された正規表現パターンに文字列が一致するかどうかをチェックします。 エスケープせずに文字列を照合するには、`<regex>` (バック クォート) を使用します。  
* ``regexMatch('200.50', '(\\d+).(\\d+)') -> true``  
* ``regexMatch('200.50', `(\d+).(\d+)`) -> true``  
___
### <code>regexReplace</code>
<code><b>regexReplace(<i>&lt;string&gt;</i> : string, <i>&lt;regex to find&gt;</i> : string, <i>&lt;substring to replace&gt;</i> : string) => string</b></code><br/><br/>
指定の文字列内の正規表現パターンのすべての出現を別の部分文字列に置換します。エスケープせずに文字列を照合するには、`<regex>` (バック クォート) を使用します。  
* ``regexReplace('100 and 200', '(\\d+)', 'bojjus') -> 'bojjus and bojjus'``  
* ``regexReplace('100 and 200', `(\d+)`, 'gunchus') -> 'gunchus and gunchus'``  
___
### <code>regexSplit</code>
<code><b>regexSplit(<i>&lt;string to split&gt;</i> : string, <i>&lt;regex expression&gt;</i> : string) => array</b></code><br/><br/>
文字列を正規表現に基づく区切り文字に基づいて分割し、文字列の配列を返します。  
* ``regexSplit('bojjusAgunchusBdumbo', `[CAB]`) -> ['bojjus', 'gunchus', 'dumbo']``  
* ``regexSplit('bojjusAgunchusBdumboC', `[CAB]`) -> ['bojjus', 'gunchus', 'dumbo', '']``  
* ``(regexSplit('bojjusAgunchusBdumboC', `[CAB]`)[1]) -> 'bojjus'``  
* ``isNull(regexSplit('bojjusAgunchusBdumboC', `[CAB]`)[20]) -> true``  
___
### <code>replace</code>
<code><b>replace(<i>&lt;string&gt;</i> : string, <i>&lt;substring to find&gt;</i> : string, [<i>&lt;substring to replace&gt;</i> : string]) => string</b></code><br/><br/>
指定された文字列内の substring のすべての出現を別の substring に置換します。 最後のパラメーターを省略すると、既定値は空の文字列になります。  
* ``replace('doggie dog', 'dog', 'cat') -> 'catgie cat'``  
* ``replace('doggie dog', 'dog', '') -> 'gie '``  
* ``replace('doggie dog', 'dog') -> 'gie '``  
___
### <code>reverse</code>
<code><b>reverse(<i>&lt;value1&gt;</i> : string) => string</b></code><br/><br/>
文字列を反転します。  
* ``reverse('gunchus') -> 'suhcnug'``  
___
### <code>right</code>
<code><b>right(<i>&lt;string to subset&gt;</i> : string, <i>&lt;number of characters&gt;</i> : integral) => string</b></code><br/><br/>
指定した文字数の部分文字列を右から抽出します。 SUBSTRING(str, LENGTH(str) - n, n) と同じです。  
* ``right('bojjus', 2) -> 'us'``  
* ``right('bojjus', 20) -> 'bojjus'``  
___
### <code>rlike</code>
<code><b>rlike(<i>&lt;string&gt;</i> : string, <i>&lt;pattern match&gt;</i> : string) => boolean</b></code><br/><br/>
指定された正規表現パターンに文字列が一致するかどうかをチェックします。  
* ``rlike('200.50', `(\d+).(\d+)`) -> true``  
* ``rlike('bogus', `M[0-9]+.*`) -> false``  
___
### <code>round</code>
<code><b>round(<i>&lt;number&gt;</i> : number, [<i>&lt;scale to round&gt;</i> : number], [<i>&lt;rounding option&gt;</i> : integral]) => double</b></code><br/><br/>
省略可能な桁数と省略可能な丸めモードで数値を丸めます。 桁数を省略すると、既定値の 0 が使用されます。 モードを省略すると、既定値は ROUND_HALF_UP(5) になります。 丸めの値には、1 - ROUND_UP、2 - ROUND_DOWN、3 - ROUND_CEILING、4 - ROUND_FLOOR、5 - ROUND_HALF_UP、6 - ROUND_HALF_DOWN、7 - ROUND_HALF_EVEN、8 - ROUND_UNNECESSARY があります。  
* ``round(100.123) -> 100.0``  
* ``round(2.5, 0) -> 3.0``  
* ``round(5.3999999999999995, 2, 7) -> 5.40``  
___
### <code>rpad</code>
<code><b>rpad(<i>&lt;string to pad&gt;</i> : string, <i>&lt;final padded length&gt;</i> : integral, <i>&lt;padding&gt;</i> : string) => string</b></code><br/><br/>
特定の長さになるまで、指定した埋め込み文字で文字列の右側を埋め込みます。 文字列が指定された長さ以上の場合は、その長さまで削除されます。  
* ``rpad('dumbo', 10, '-') -> 'dumbo-----'``  
* ``rpad('dumbo', 4, '-') -> 'dumb'``  
* ``rpad('dumbo', 8, '<>') -> 'dumbo<><'``  
___
### <code>rtrim</code>rtrim</code>
<code><b>rtrim(<i>&lt;string to trim&gt;</i> : string, [<i>&lt;trim characters&gt;</i> : string]) => string</b></code><br/><br/>
文字列の末尾文字を右から削除します。 2 番目のパラメーターを指定しない場合、空白文字を削除します。 それ以外の場合は、2 番目のパラメーターに指定した任意の文字を削除します。  
* ``rtrim('  dumbo  ') -> '  dumbo'``  
* ``rtrim('!--!du!mbo!', '-!') -> '!--!du!mbo'``  
___
### <code>second</code>
<code><b>second(<i>&lt;value1&gt;</i> : timestamp, [<i>&lt;value2&gt;</i> : string]) => integer</b></code><br/><br/>
日付の秒の値を取得します。 省略可能なタイムゾーンを 'GMT'、'PST'、'UTC'、'America/Cayman' の形式で渡せます。 ローカル タイムゾーンが既定値として使用されます。 使用可能な形式については、Java の `SimpleDateFormat` クラスを参照してください。 https://docs.oracle.com/javase/8/docs/api/java/text/SimpleDateFormat.html.  
* ``second(toTimestamp('2009-07-30 12:58:59')) -> 59``  
___
### <code>seconds</code>
<code><b>seconds(<i>&lt;value1&gt;</i> : integer) => long</b></code><br/><br/>
ミリ秒単位での秒数の期間。  
* ``seconds(2) -> 2000L``  
___
### <code>sha1</code>
<code><b>sha1(<i>&lt;value1&gt;</i> : any, ...) => string</b></code><br/><br/>
さまざまなプリミティブ データ型の列セットの SHA-1 ハッシュを計算し、40 文字の16 進数の文字列を返します。 行のフィンガープリントを計算するために使用できます。  
* ``sha1(5, 'gunchus', 8.2, 'bojjus', true, toDate('2010-4-4')) -> '46d3b478e8ec4e1f3b453ac3d8e59d5854e282bb'``  
___
### <code>sha2</code>
<code><b>sha2(<i>&lt;value1&gt;</i> : integer, <i>&lt;value2&gt;</i> : any, ...) => string</b></code><br/><br/>
さまざまなプリミティブ データ型の列セットの SHA-2 ハッシュを指定のビット長で計算します。ビット長として指定できるのは、0(256)、224、256、384、512 の値のみです。 行のフィンガープリントを計算するために使用できます。  
* ``sha2(256, 'gunchus', 8.2, 'bojjus', true, toDate('2010-4-4')) -> 'afe8a553b1761c67d76f8c31ceef7f71b66a1ee6f4e6d3b5478bf68b47d06bd3'``  
___
### <code>sin</code>
<code><b>sin(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
サイン値を計算します。  
* ``sin(2) -> 0.9092974268256817``  
___
### <code>sinh</code>
<code><b>sinh(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
双曲線サイン値を計算します。  
* ``sinh(0) -> 0.0``  
___
### <code>soundex</code>
<code><b>soundex(<i>&lt;value1&gt;</i> : string) => string</b></code><br/><br/>
文字列の ```soundex``` コードを取得します。  
* ``soundex('genius') -> 'G520'``  
___
### <code>split</code>
<code><b>split(<i>&lt;string to split&gt;</i> : string, <i>&lt;split characters&gt;</i> : string) => array</b></code><br/><br/>
文字列を区切り文字に基づいて分割し、文字列の配列を返します。  
* ``split('bojjus,guchus,dumbo', ',') -> ['bojjus', 'guchus', 'dumbo']``  
* ``split('bojjus,guchus,dumbo', '|') -> ['bojjus,guchus,dumbo']``  
* ``split('bojjus, guchus, dumbo', ', ') -> ['bojjus', 'guchus', 'dumbo']``  
* ``split('bojjus, guchus, dumbo', ', ')[1] -> 'bojjus'``  
* ``isNull(split('bojjus, guchus, dumbo', ', ')[0]) -> true``  
* ``isNull(split('bojjus, guchus, dumbo', ', ')[20]) -> true``  
* ``split('bojjusguchusdumbo', ',') -> ['bojjusguchusdumbo']``  
___
### <code>sqrt</code>
<code><b>sqrt(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
数値の平方根を計算します。  
* ``sqrt(9) -> 3``  
___
### <code>startsWith</code>
<code><b>startsWith(<i>&lt;string&gt;</i> : string, <i>&lt;substring to check&gt;</i> : string) => boolean</b></code><br/><br/>
文字列が指定した文字列で開始しているかどうかをチェックします。  
* ``startsWith('dumbo', 'du') -> true``  
___
### <code>subDays</code>
<code><b>subDays(<i>&lt;date/timestamp&gt;</i> : datetime, <i>&lt;days to subtract&gt;</i> : integral) => datetime</b></code><br/><br/>
日付またはタイムスタンプから日数を減算します。 日付に対する - 演算子と同じです。  
* ``subDays(toDate('2016-08-08'), 1) -> toDate('2016-08-07')``  
___
### <code>subMonths</code>
<code><b>subMonths(<i>&lt;date/timestamp&gt;</i> : datetime, <i>&lt;months to subtract&gt;</i> : integral) => datetime</b></code><br/><br/>
日付またはタイムスタンプから月数を減算します。  
* ``subMonths(toDate('2016-09-30'), 1) -> toDate('2016-08-31')``  
___
### <code>substring</code>
<code><b>substring(<i>&lt;string to subset&gt;</i> : string, <i>&lt;from 1-based index&gt;</i> : integral, [<i>&lt;number of characters&gt;</i> : integral]) => string</b></code><br/><br/>
ある位置から特定の長さの部分文字列を抽出します。 位置は 1 から始まります。 長さを省略すると、既定値は文字列の最後になります。  
* ``substring('Cat in the hat', 5, 2) -> 'in'``  
* ``substring('Cat in the hat', 5, 100) -> 'in the hat'``  
* ``substring('Cat in the hat', 5) -> 'in the hat'``  
* ``substring('Cat in the hat', 100, 100) -> ''``  
___
### <code>tan</code>
<code><b>tan(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
タンジェント値を計算します。  
* ``tan(0) -> 0.0``  
___
### <code>tanh</code>
<code><b>tanh(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
双曲線タンジェント値を計算します。  
* ``tanh(0) -> 0.0``  
___
### <code>translate</code>
<code><b>translate(<i>&lt;string to translate&gt;</i> : string, <i>&lt;lookup characters&gt;</i> : string, <i>&lt;replace characters&gt;</i> : string) => string</b></code><br/><br/>
文字列内のある文字セットを別の文字セットで置換します。 文字の置換は 1 対 1 で行われます。  
* ``translate('(bojjus)', '()', '[]') -> '[bojjus]'``  
* ``translate('(gunchus)', '()', '[') -> '[gunchus'``  
___
### <code>trim</code>
<code><b>trim(<i>&lt;string to trim&gt;</i> : string, [<i>&lt;trim characters&gt;</i> : string]) => string</b></code><br/><br/>
文字列の先頭文字と末尾文字を削除します。 2 番目のパラメーターを指定しない場合、空白文字を削除します。 それ以外の場合は、2 番目のパラメーターに指定した任意の文字を削除します。  
* ``trim('  dumbo  ') -> 'dumbo'``  
* ``trim('!--!du!mbo!', '-!') -> 'du!mbo'``  
___
### <code>true</code>
<code><b>true() => boolean</b></code><br/><br/>
常に true 値を返します。 "true" という名前の列がある場合は、関数 `syntax(true())` を使用します。  
* ``(10 + 20 == 30) -> true``  
* ``(10 + 20 == 30) -> true()``  
___
### <code>typeMatch</code>
<code><b>typeMatch(<i>&lt;type&gt;</i> : string, <i>&lt;base type&gt;</i> : string) => boolean</b></code><br/><br/>
列の型を照合します。 パターン式でのみ使用できます。数値は short、integer、long、double、float、または decimal 型に一致し、整数は short、integer、long 型に一致し、小数は double、float、decimal 型に一致し、日時は date または timestamp 型に一致します。  
* ``typeMatch(type, 'number')``  
* ``typeMatch('date', 'datetime')``  
___
### <code>unescape</code>
<code><b>unescape(<i>&lt;string_to_escape&gt;</i> : string, <i>&lt;format&gt;</i> : string) => string</b></code><br/><br/>
形式に従って、文字列を非エスケープします。 使用できる形式のリテラル値は、'json'、'xml'、'ecmascript'、'html'、'java' です。
* ```unescape('{\\\\\"value\\\\\": 10}', 'json')```
* ```'{\\\"value\\\": 10}'```
___
### <code>upper</code>
<code><b>upper(<i>&lt;value1&gt;</i> : string) => string</b></code><br/><br/>
文字列を大文字にします。  
* ``upper('bojjus') -> 'BOJJUS'``  
___
### <code>uuid</code>
<code><b>uuid() => string</b></code><br/><br/>
生成された UUID を返します。  
* ``uuid()``  
___
### <code>weekOfYear</code>
<code><b>weekOfYear(<i>&lt;value1&gt;</i> : datetime) => integer</b></code><br/><br/>
指定した日付から、その年の何週目かを取得します。  
* ``weekOfYear(toDate('2008-02-20')) -> 8``  
___
### <code>weeks</code>
<code><b>weeks(<i>&lt;value1&gt;</i> : integer) => long</b></code><br/><br/>
ミリ秒単位での週数の期間。  
* ``weeks(2) -> 1209600000L``  
___
### <code>xor</code>
<code><b>xor(<i>&lt;value1&gt;</i> : boolean, <i>&lt;value2&gt;</i> : boolean) => boolean</b></code><br/><br/>
XOR 論理演算子。 ^ 演算子と同じです。  
* ``xor(true, false) -> true``  
* ``xor(true, true) -> false``  
* ``true ^ false -> true``  
___
### <code>year</code>
<code><b>year(<i>&lt;value1&gt;</i> : datetime) => integer</b></code><br/><br/>
日付の年の値を取得します。  
* ``year(toDate('2012-8-8')) -> 2012``  

## <a name="aggregate-functions"></a>集計関数
次の関数は、集計、ピボット、ピボット解除、ウィンドウ変換でのみ使用できます。
___
### <code>avg</code>
<code><b>avg(<i>&lt;value1&gt;</i> : number) => number</b></code><br/><br/>
列の値の平均を取得します。  
* ``avg(sales)``  
___
### <code>avgIf</code>
<code><b>avgIf(<i>&lt;value1&gt;</i> : boolean, <i>&lt;value2&gt;</i> : number) => number</b></code><br/><br/>
条件に基づいて、列の値の平均を取得します。  
* ``avgIf(region == 'West', sales)``  
___
### <code>count</code>
<code><b>count([<i>&lt;value1&gt;</i> : any]) => long</b></code><br/><br/>
値の集計カウントを取得します。 省略可能な列を指定すると、カウントの NULL 値が無視されます。  
* ``count(custId)``  
* ``count(custId, custName)``  
* ``count()``  
* ``count(iif(isNull(custId), 1, NULL))``  
___
### <code>countDistinct</code>
<code><b>countDistinct(<i>&lt;value1&gt;</i> : any, [<i>&lt;value2&gt;</i> : any], ...) => long</b></code><br/><br/>
列セットの個別値の集計カウントを取得します。  
* ``countDistinct(custId, custName)``  
___
### <code>countIf</code>
<code><b>countIf(<i>&lt;value1&gt;</i> : boolean, [<i>&lt;value2&gt;</i> : any]) => long</b></code><br/><br/>
条件に基づいて、値の集計カウントを取得します。 省略可能な列を指定すると、カウントの NULL 値が無視されます。  
* ``countIf(state == 'CA' && commission < 10000, name)``  
___
### <code>covariancePopulation</code>
<code><b>covariancePopulation(<i>&lt;value1&gt;</i> : number, <i>&lt;value2&gt;</i> : number) => double</b></code><br/><br/>
2 つの列の間の母共分散を取得します。  
* ``covariancePopulation(sales, profit)``  
___
### <code>covariancePopulationIf</code>
<code><b>covariancePopulationIf(<i>&lt;value1&gt;</i> : boolean, <i>&lt;value2&gt;</i> : number, <i>&lt;value3&gt;</i> : number) => double</b></code><br/><br/>
条件に基づいて、2 つの列の間の母共分散を取得します。  
* ``covariancePopulationIf(region == 'West', sales)``  
___
### <code>covarianceSample</code>
<code><b>covarianceSample(<i>&lt;value1&gt;</i> : number, <i>&lt;value2&gt;</i> : number) => double</b></code><br/><br/>
2 つの列の標本共分散を取得します。  
* ``covarianceSample(sales, profit)``  
___
### <code>covarianceSampleIf</code>
<code><b>covarianceSampleIf(<i>&lt;value1&gt;</i> : boolean, <i>&lt;value2&gt;</i> : number, <i>&lt;value3&gt;</i> : number) => double</b></code><br/><br/>
条件に基づいて、2 つの列の標本共分散を取得します。  
* ``covarianceSampleIf(region == 'West', sales, profit)``  
___
### <code>first</code>
<code><b>first(<i>&lt;value1&gt;</i> : any, [<i>&lt;value2&gt;</i> : boolean]) => any</b></code><br/><br/>
列グループの最初の値を取得します。 2 番目のパラメーター ignoreNulls を省略すると、false であると見なされます。  
* ``first(sales)``  
* ``first(sales, false)``  
___
### <code>kurtosis</code>
<code><b>kurtosis(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
列の尖度を取得します。  
* ``kurtosis(sales)``  
___
### <code>kurtosisIf</code>
<code><b>kurtosisIf(<i>&lt;value1&gt;</i> : boolean, <i>&lt;value2&gt;</i> : number) => double</b></code><br/><br/>
条件に基づいて、列の尖度を取得します。  
* ``kurtosisIf(region == 'West', sales)``  
___
### <code>last</code>
<code><b>last(<i>&lt;value1&gt;</i> : any, [<i>&lt;value2&gt;</i> : boolean]) => any</b></code><br/><br/>
列グループの最後の値を取得します。 2 番目のパラメーター ignoreNulls を省略すると、false であると見なされます。  
* ``last(sales)``  
* ``last(sales, false)``  
___
### <code>max</code>
<code><b>max(<i>&lt;value1&gt;</i> : any) => any</b></code><br/><br/>
列の最大値を取得します。  
* ``max(sales)``  
___
### <code>maxIf</code>
<code><b>maxIf(<i>&lt;value1&gt;</i> : boolean, <i>&lt;value2&gt;</i> : any) => any</b></code><br/><br/>
条件に基づいて、列の値の最大値を取得します。  
* ``maxIf(region == 'West', sales)``  
___
### <code>mean</code>
<code><b>mean(<i>&lt;value1&gt;</i> : number) => number</b></code><br/><br/>
列の値の平均を取得します。 AVG と同じです。  
* ``mean(sales)``  
___
### <code>meanIf</code>
<code><b>meanIf(<i>&lt;value1&gt;</i> : boolean, <i>&lt;value2&gt;</i> : number) => number</b></code><br/><br/>
条件に基づいて、列の値の平均を取得します。 avgIf と同じです。  
* ``meanIf(region == 'West', sales)``  
___
### <code>min</code>
<code><b>min(<i>&lt;value1&gt;</i> : any) => any</b></code><br/><br/>
列の最小値を取得します。  
* ``min(sales)``  
___
### <code>minIf</code>
<code><b>minIf(<i>&lt;value1&gt;</i> : boolean, <i>&lt;value2&gt;</i> : any) => any</b></code><br/><br/>
条件に基づいて、列の値の最小値を取得します。  
* ``minIf(region == 'West', sales)``  
___
### <code>skewness</code>
<code><b>skewness(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
列の歪度を取得します。  
* ``skewness(sales)``  
___
### <code>skewnessIf</code>
<code><b>skewnessIf(<i>&lt;value1&gt;</i> : boolean, <i>&lt;value2&gt;</i> : number) => double</b></code><br/><br/>
条件に基づいて、列の歪度を取得します。  
* ``skewnessIf(region == 'West', sales)``  
___
### <code>stddev</code>
<code><b>stddev(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
列の標準偏差を取得します。  
* ``stdDev(sales)``  
___
### <code>stddevIf</code>
<code><b>stddevIf(<i>&lt;value1&gt;</i> : boolean, <i>&lt;value2&gt;</i> : number) => double</b></code><br/><br/>
条件に基づいて、列の標準偏差を取得します。  
* ``stddevIf(region == 'West', sales)``  
___
### <code>stddevPopulation</code>
<code><b>stddevPopulation(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
列の母標準偏差を取得します。  
* ``stddevPopulation(sales)``  
___
### <code>stddevPopulationIf</code>
<code><b>stddevPopulationIf(<i>&lt;value1&gt;</i> : boolean, <i>&lt;value2&gt;</i> : number) => double</b></code><br/><br/>
条件に基づいて、列の母標準偏差を取得します。  
* ``stddevPopulationIf(region == 'West', sales)``  
___
### <code>stddevSample</code>
<code><b>stddevSample(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
列の標本標準偏差を取得します。  
* ``stddevSample(sales)``  
___
### <code>stddevSampleIf</code>
<code><b>stddevSampleIf(<i>&lt;value1&gt;</i> : boolean, <i>&lt;value2&gt;</i> : number) => double</b></code><br/><br/>
条件に基づいて、列の標本標準偏差を取得します。  
* ``stddevSampleIf(region == 'West', sales)``  
___
### <code>sum</code>
<code><b>sum(<i>&lt;value1&gt;</i> : number) => number</b></code><br/><br/>
数値列の集計を取得します。  
* ``sum(col)``  
___
### <code>sumDistinct</code>
<code><b>sumDistinct(<i>&lt;value1&gt;</i> : number) => number</b></code><br/><br/>
数値列の個別値の集計を取得します。  
* ``sumDistinct(col)``  
___
### <code>sumDistinctIf</code>
<code><b>sumDistinctIf(<i>&lt;value1&gt;</i> : boolean, <i>&lt;value2&gt;</i> : number) => number</b></code><br/><br/>
条件に基づいて、数値列の集計を取得します。 条件は、任意の列に基づくことができます。  
* ``sumDistinctIf(state == 'CA' && commission < 10000, sales)``  
* ``sumDistinctIf(true, sales)``  
___
### <code>sumIf</code>
<code><b>sumIf(<i>&lt;value1&gt;</i> : boolean, <i>&lt;value2&gt;</i> : number) => number</b></code><br/><br/>
条件に基づいて、数値列の集計を取得します。 条件は、任意の列に基づくことができます。  
* ``sumIf(state == 'CA' && commission < 10000, sales)``  
* ``sumIf(true, sales)``  
___
### <code>variance</code>
<code><b>variance(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
列の分散を取得します。  
* ``variance(sales)``  
___
### <code>varianceIf</code>
<code><b>varianceIf(<i>&lt;value1&gt;</i> : boolean, <i>&lt;value2&gt;</i> : number) => double</b></code><br/><br/>
条件に基づいて、列の分散を取得します。  
* ``varianceIf(region == 'West', sales)``  
___
### <code>variancePopulation</code>
<code><b>variancePopulation(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
列の母分散を取得します。  
* ``variancePopulation(sales)``  
___
### <code>variancePopulationIf</code>
<code><b>variancePopulationIf(<i>&lt;value1&gt;</i> : boolean, <i>&lt;value2&gt;</i> : number) => double</b></code><br/><br/>
条件に基づいて、列の母分散を取得します。  
* ``variancePopulationIf(region == 'West', sales)``  
___
### <code>varianceSample</code>
<code><b>varianceSample(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
列の不偏分散を取得します。  
* ``varianceSample(sales)``  
___
### <code>varianceSampleIf</code>
<code><b>varianceSampleIf(<i>&lt;value1&gt;</i> : boolean, <i>&lt;value2&gt;</i> : number) => double</b></code><br/><br/>
条件に基づいて、列の不偏分散を取得します。  
* ``varianceSampleIf(region == 'West', sales)``  

## <a name="array-functions"></a>配列関数
配列関数は、配列であるデータ構造体に対して変換を実行します。 これらには、配列の要素とインデックスをアドレス指定するための特殊なキーワードが含まれます。

* ```#acc``` は、配列を縮小するときに 1 つの出力に含める値を表します。
* ```#index``` は、配列のインデックス番号 ```#index2, #index3 ...``` と共に、現在の配列のインデックスを表します。
* ```#item``` は、配列内の現在の要素の値を表します。

### <code>array</code>
<code><b>array([<i>&lt;value1&gt;</i> : any], ...) => array</b></code><br/><br/>
項目の配列を作成します。 すべての項目は同じ型になっている必要があります。 項目が指定されていない場合、既定値は空の文字列配列です。 [] 作成演算子と同じです。  
* ``array('Seattle', 'Washington')``
* ``['Seattle', 'Washington']``
* ``['Seattle', 'Washington'][1]``
* ``'Washington'``
___
### <code>filter</code>
<code><b>filter(<i>&lt;value1&gt;</i> : array, <i>&lt;value2&gt;</i> : unaryfunction) => array</b></code><br/><br/>
指定された述語を満たさない要素を配列から除外します。 filter は、述語関数の 1 つの要素への参照を #item として予期します。  
* ``filter([1, 2, 3, 4], #item > 2) -> [3, 4]``  
* ``filter(['a', 'b', 'c', 'd'], #item == 'a' || #item == 'b') -> ['a', 'b']``  
___
### <code>find</code>
<code><b>find(<i>&lt;value1&gt;</i> : array, <i>&lt;value2&gt;</i> : unaryfunction) => any</b></code><br/><br/>
配列から条件に一致する最初の項目を検索します。 フィルター関数が使用されます。この関数では、配列内の項目を #item としてアドレス指定できます。 入れ子になった深いマップの場合は、#item_n (#item_1, #item_2...) 表記を使用して親マップを参照できます。  
* ``find([10, 20, 30], #item > 10) -> 20``
* ``find(['azure', 'data', 'factory'], length(#item) > 4) -> 'azure'``
* ``find([
      @(
         name = 'Daniel',
         types = [
                   @(mood = 'jovial', behavior = 'terrific'),
                   @(mood = 'grumpy', behavior = 'bad')
                 ]
        ),
      @(
         name = 'Mark',
         types = [
                   @(mood = 'happy', behavior = 'awesome'),
                   @(mood = 'calm', behavior = 'reclusive')
                 ]
        )
      ],
      contains(#item.types, #item.mood=='happy')  /*Filter out the happy kid*/
    )``
* ``
     @(
           name = 'Mark',
           types = [
                     @(mood = 'happy', behavior = 'awesome'),
                     @(mood = 'calm', behavior = 'reclusive')
                   ]
      )
    ``  
___
### <code>map</code>
<code><b>map(<i>&lt;value1&gt;</i> : array, <i>&lt;value2&gt;</i> : unaryfunction) => any</b></code><br/><br/> 指定された式を使用して、配列の各要素を新しい要素にマップします。 map は、式関数の 1 つの要素への参照を #item として予期します。nction as #item.  
* ``map([1, 2, 3, 4], #item + 2) -> [3, 4, 5, 6]``  
* ``map(['a', 'b', 'c', 'd'], #item + '_processed') -> ['a_processed', 'b_processed', 'c_processed', 'd_processed']``  
___
### <code>mapIf</code>
<code><b>mapIf (<value1> : array, <value2> : binaryfunction, <value3>: binaryFunction) => any</b></code><br/><br/> 配列を、同じ長さまたはそれより短い長さの別の配列に条件付きでマップします。 値は、structTypes など、任意のデータ型とすることができます。 配列内のアイテムを #item として、現在のインデックスを #index としてアドレス指定できるマッピング関数を取ります。 深く入れ子になったマップの場合、#item_[n](#item_1, #index_1...) という表記を使用して親マップを参照できます。t maps using the ``#item_[n](#item_1,...)`` notation.
*   ``mapIf([10, 20, 30], #item > 10, #item + 5) -> [25, 35]``
* ``mapIf(['icecream', 'cake', 'soda'], length(#item) > 4, upper(#item)) -> ['ICECREAM', 'CAKE']``
___
### <code>mapIndex</code>
<code><b>mapIndex(<i>&lt;value1&gt;</i> : array, <i>&lt;value2&gt;</i> : binaryfunction) => any</b></code><br/><br/> 指定された式を使用して、配列の各要素を新しい要素にマップします。 map は、式関数の 1 つの要素への参照を #item として予期し、要素インデックスへの参照を #index として予期します。index as #index.  
* ``mapIndex([1, 2, 3, 4], #item + 2 + #index) -> [4, 6, 8, 10]``  
___
### <code>mapLoop</code>
<code><b>mapLoop(<value1> : integer, <value2> : unaryfunction) => any</b></code><br/><br/> 1 から length までループさせてその長さの配列を作成します。 これは、配列内のインデックスを #index としてアドレス指定できるマッピング関数を取ります。 深く入れ子になったマップの場合、#index_n(#index_1, #index_2...) という表記を使用して親マップを参照できます。_2...) notation.
*   ``mapLoop(3, #index * 10) -> [10, 20, 30]``
___
### <code>reduce</code>
<code><b>reduce(<i>&lt;value1&gt;</i> : array, <i>&lt;value2&gt;</i> : any, <i>&lt;value3&gt;</i> : binaryfunction, <i>&lt;value4&gt;</i> : unaryfunction) => any</b></code><br/><br/> 配列内の要素を累積します。 reduce は、最初の式関数内のアキュムレータと 1 つの要素への参照を #acc および #item として予期し、結果の値を 2 番目の式関数に使用される #result として予期します。ession function.  
* ``toString(reduce(['1', '2', '3', '4'], '0', #acc + #item, #result)) -> '01234'``  
___
### <code>size</code>
<code><b>size(<i>&lt;value1&gt;</i> : any) => integer</b></code><br/><br/> 配列型またはマップ型のサイズを検索します。rray or map type  
* ``size(['element1', 'element2']) -> 2``
* ``size([1,2,3]) -> 3``
___
### <code>slice</code>
<code><b>slice(<i>&lt;array to slice&gt;</i> : array, <i>&lt;from 1-based index&gt;</i> : integral, [<i>&lt;number of items&gt;</i> : integral]) => array</b></code><br/><br/> ある位置から配列のサブセットを抽出します。 位置は 1 から始まります。 長さを省略すると、既定値は文字列の最後になります。d of the string.  
* ``slice([10, 20, 30, 40], 1, 2) -> [10, 20]``  
* ``slice([10, 20, 30, 40], 2) -> [20, 30, 40]``  
* ``slice([10, 20, 30, 40], 2)[1] -> 20``  
* ``isNull(slice([10, 20, 30, 40], 2)[0]) -> true``  
* ``isNull(slice([10, 20, 30, 40], 2)[20]) -> true``  
* ``slice(['a', 'b', 'c', 'd'], 8) -> []``  
___
### <code>sort</code>
<code><b>sort(<i>&lt;value1&gt;</i> : array, <i>&lt;value2&gt;</i> : binaryfunction) => array</b></code><br/><br/> 指定された述語関数を使用して配列を並べ替えます。 sort は、式関数の 2 つの連続する要素への参照を #item1 および #item2 として予期します。tem1 and #item2.  
* ``sort([4, 8, 2, 3], compare(#item1, #item2)) -> [2, 3, 4, 8]``  
* ``sort(['a3', 'b2', 'c1'], iif(right(#item1, 1) >= right(#item2, 1), 1, -1)) -> ['c1', 'b2', 'a3']``* ``
 @(
       name = 'Mark',
       types = [
                 @(mood = 'happy', behavior = 'awesome'),
                 @(mood = 'calm', behavior = 'reclusive')
               ]
  )
``  
___
### <code>map</code>
<code><b>map(<i>&lt;value1&gt;</i> : array, <i>&lt;value2&gt;</i> : unaryfunction) => any</b></code><br/><br/>
Maps each element of the array to a new element using the provided expression. Map expects a reference to one element in the expression function as #item.  
* ``map([1, 2, 3, 4], #item + 2) -> [3, 4, 5, 6]``  
* ``map(['a', 'b', 'c', 'd'], #item + '_processed') -> ['a_processed', 'b_processed', 'c_processed', 'd_processed']``  
___
### <code>mapIf</code>
<code><b>mapIf (<value1> : array, <value2> : binaryfunction, <value3>: binaryFunction) => any</b></code><br/><br/>
Conditionally maps an array to another array of same or smaller length. The values can be of any datatype including structTypes. It takes a mapping function where you can address the item in the array as #item and current index as #index. For deeply nested maps you can refer to the parent maps using the ``#item_[n](#item_1, #index_1...)`` notation.
*   ``mapIf([10, 20, 30], #item > 10, #item + 5) -> [25, 35]``
* ``mapIf(['icecream', 'cake', 'soda'], length(#item) > 4, upper(#item)) -> ['ICECREAM', 'CAKE']``
___
### <code>mapIndex</code>
<code><b>mapIndex(<i>&lt;value1&gt;</i> : array, <i>&lt;value2&gt;</i> : binaryfunction) => any</b></code><br/><br/>
Maps each element of the array to a new element using the provided expression. Map expects a reference to one element in the expression function as #item and a reference to the element index as #index.  
* ``mapIndex([1, 2, 3, 4], #item + 2 + #index) -> [4, 6, 8, 10]``  
___
### <code>mapLoop</code>
<code><b>mapLoop(<value1> : integer, <value2> : unaryfunction) => any</b></code><br/><br/>
Loops through from 1 to length to create an array of that length. It takes a mapping function where you can address the index in the array as #index. For deeply nested maps you can refer to the parent maps using the #index_n(#index_1, #index_2...) notation.
*   ``mapLoop(3, #index * 10) -> [10, 20, 30]``
___
### <code>reduce</code>
<code><b>reduce(<i>&lt;value1&gt;</i> : array, <i>&lt;value2&gt;</i> : any, <i>&lt;value3&gt;</i> : binaryfunction, <i>&lt;value4&gt;</i> : unaryfunction) => any</b></code><br/><br/>
Accumulates elements in an array. Reduce expects a reference to an accumulator and one element in the first expression function as #acc and #item and it expects the resulting value as #result to be used in the second expression function.  
* ``toString(reduce(['1', '2', '3', '4'], '0', #acc + #item, #result)) -> '01234'``  
___
### <code>size</code>
<code><b>size(<i>&lt;value1&gt;</i> : any) => integer</b></code><br/><br/>
Finds the size of an array or map type  
* ``size(['element1', 'element2']) -> 2``
* ``size([1,2,3]) -> 3``
___
### <code>slice</code>
<code><b>slice(<i>&lt;array to slice&gt;</i> : array, <i>&lt;from 1-based index&gt;</i> : integral, [<i>&lt;number of items&gt;</i> : integral]) => array</b></code><br/><br/>
Extracts a subset of an array from a position. Position is 1 based. If the length is omitted, it is defaulted to end of the string.  
* ``slice([10, 20, 30, 40], 1, 2) -> [10, 20]``  
* ``slice([10, 20, 30, 40], 2) -> [20, 30, 40]``  
* ``slice([10, 20, 30, 40], 2)[1] -> 20``  
* ``isNull(slice([10, 20, 30, 40], 2)[0]) -> true``  
* ``isNull(slice([10, 20, 30, 40], 2)[20]) -> true``  
* ``slice(['a', 'b', 'c', 'd'], 8) -> []``  
___
### <code>sort</code>
<code><b>sort(<i>&lt;value1&gt;</i> : array, <i>&lt;value2&gt;</i> : binaryfunction) => array</b></code><br/><br/>
Sorts the array using the provided predicate function. Sort expects a reference to two consecutive elements in the expression function as #item1 and #item2.  
* ``sort([4, 8, 2, 3], compare(#item1, #item2)) -> [2, 3, 4, 8]``  
* ``sort(['a3', 'b2', 'c1'], iif(right(#item1, 1) >= right(#item2, 1), 1, -1)) -> ['c1', 'b2', 'a3']``  

## <a name="cached-lookup-functions"></a>キャッシュされた Lookup 関数
次の関数は、キャッシュされたシンクを含めているときに、キャッシュされた検索を使用する場合にのみ使用できます。
___
### <code>lookup</code>
<code><b>lookup(key, key2, ...) => complex[]</b></code><br/><br/>
キャッシュされたシンクのキーと一致する特定のキーを使用して、キャッシュされたシンクの最初の行が検索されます。
* ``cacheSink#lookup(movieId)``  
___
### <code>mlookup</code>
<code><b>mlookup(key, key2, ...) => complex[]</b></code><br/><br/>
キャッシュされたシンクのキーと一致する特定のキーを使用して、キャッシュされたシンクの一致するすべての行が検索されます。
* ``cacheSink#mlookup(movieId)``  
___
### <code>output</code>
<code><b>output() => any</b></code><br/><br/>
キャッシュ シンクの結果の最初の行を返します。* ``cacheSink#output()``  
___
### <code>outputs</code>
<code><b>output() => any</b></code><br/><br/>
キャッシュ シンクの結果の出力行セット全体を返します。* ``cacheSink#outputs()``
___


## <a name="conversion-functions"></a>変換関数

変換関数は、データの変換とデータ型のテストに使用されます

### <code>isBoolean</code>
<code><b>isBoolean(<value1> : string) => boolean</b></code><br/><br/>
文字列値が ``toBoolean()`` の規則に従ったブール値であるかどうかを確認します 
* ``isBoolean('true') -> true``
* ``isBoolean('no') -> true``
* ``isBoolean('microsoft') -> false``
___
### <code>isByte</code>
<code><b>isByte(<value1> : string) => boolean</b></code> <br/><br/>
文字列値が ``toByte()`` の規則に従った省略可能な形式が指定されたバイト値であるかどうかを確認します 
* ``isByte('123') -> true``
* ``isByte('chocolate') -> false``
___
### <code>isDate</code>
<code><b>isDate (<value1> : string, [<format>: string]) => boolean</b></code><br/><br/>
入力日付文字列が、省略可能な入力日付形式を使用した日付であるかどうかを確認します。 使用可能な形式については、Java の SimpleDateFormat を参照してください。 入力日付形式が省略されている場合、既定の形式は ``yyyy-[M]M-[d]d`` になります。 許容される形式は ``[ yyyy, yyyy-[M]M, yyyy-[M]M-[d]d, yyyy-[M]M-[d]dT* ]`` です 
* ``isDate('2012-8-18') -> true``
* ``isDate('12/18--234234' -> 'MM/dd/yyyy') -> false``
___
### <code>isShort</code>
<code><b>isShort (<value1> : string, [<format>: string]) => boolean</b></code><br/><br/>
文字列値が ``toShort()`` の規則に従った省略可能な形式が指定された 短整数型値であることを確認します 
* ``isShort('123') -> true``
* ``isShort('$123' -> '$###') -> true``
* ``isShort('microsoft') -> false``
___
### <code>isInteger</code>
<code><b>isInteger (<value1> : string, [<format>: string]) => boolean</b></code><br/><br/>
文字列値が ``toInteger()`` の規則に従った省略可能な形式が指定された整数値であることを確認します 
* ``isInteger('123') -> true``
* ``isInteger('$123' -> '$###') -> true``
* ``isInteger('microsoft') -> false``
___
### <code>isLong</code>
<code><b>isLong (<value1> : string, [<format>: string]) => boolean</b></code><br/><br/>
文字列値が ``toLong()`` の規則に従った省略可能な形式が指定された 長整数型値であることを確認します 
* ``isLong('123') -> true``
* ``isLong('$123' -> '$###') -> true``
* ``isLong('gunchus') -> false``
___
### <code>isFloat</code>
<code><b>isFloat (<value1> : string, [<format>: string]) => boolean</b></code><br/><br/>
文字列値が ``toFloat()`` の規則に従った省略可能な形式が指定された浮動小数点値であることを確認します 
* ``isFloat('123') -> true``
* ``isFloat('$123.45' -> '$###.00') -> true``
* ``isFloat('icecream') -> false``
___
### <code>isDouble</code>
<code><b>isDouble (<value1> : string, [<format>: string]) => boolean</b></code><br/><br/>
文字列値が ``toDouble()`` の規則に従った省略可能な形式が指定された倍精度値であることを確認します 
* ``isDouble('123') -> true``
* ``isDouble('$123.45' -> '$###.00') -> true``
* ``isDouble('icecream') -> false``
___
### <code>isDecimal</code>
<code><b>isDecimal (<value1> : string) => boolean</b></code><br/><br/>
文字列値が ``toDecimal()`` の規則に従った省略可能な形式が指定された 10 進数値であることを確認します 
* ``isDecimal('123.45') -> true``
* ``isDecimal('12/12/2000') -> false``
___
### <code>isTimestamp</code>
<code><b>isTimestamp (<value1> : string, [<format>: string]) => boolean</b></code><br/><br/>
入力日付文字列が、省略可能な入力タイムスタンプ形式を使用したタイムスタンプであるかどうかを確認します。 使用可能な形式については、Java の SimpleDateFormat を参照してください。 タイムスタンプを省略すると、既定のパターンの ``yyyy-[M]M-[d]d hh:mm:ss[.f...]`` が使用されます。 省略可能なタイムゾーンを 'GMT'、'PST'、'UTC'、'America/Cayman' の形式で渡せます。 タイムスタンプはミリ秒の精度で 999 の値までサポートされます。使用可能な形式については、Java の SimpleDateFormat を参照してください。
* ``isTimestamp('2016-12-31 00:12:00') -> true``
* ``isTimestamp('2016-12-31T00:12:00' -> 'yyyy-MM-dd\\'T\\'HH:mm:ss' -> 'PST') -> true``
* ``isTimestamp('2012-8222.18') -> false``
___
### <code>toBase64</code>
<code><b>toBase64(<i>&lt;value1&gt;</i> : string) => string</b></code><br/><br/>
指定された文字列を base64 でエンコードします。  
* ``toBase64('bojjus') -> 'Ym9qanVz'``  
___
### <code>toBinary</code>
<code><b>toBinary(<i>&lt;value1&gt;</i> : any) => binary</b></code><br/><br/>
任意の数値、日付、タイムスタンプ、文字列をバイナリ表現に変換します。  
* ``toBinary(3) -> [0x11]``  
___
### <code>toBoolean</code>
<code><b>toBoolean(<i>&lt;value1&gt;</i> : string) => boolean</b></code><br/><br/>
('t'、'true'、'y'、'yes'、'1') の値を true に変換し、('f'、'false'、'n'、'no'、'0') の値を false に変換し、それ以外の値を NULL に変換します。  
* ``toBoolean('true') -> true``  
* ``toBoolean('n') -> false``  
* ``isNull(toBoolean('truthy')) -> true``  
___
### <code>toByte</code>
<code><b>toByte(<i>&lt;value&gt;</i> : any, [<i>&lt;format&gt;</i> : string], [<i>&lt;locale&gt;</i> : string]) => byte</b></code><br/><br/>
任意の数値または文字列をバイト値に変換します。 省略可能な Java の 10 進形式を変換に使用できます。  
* ``toByte(123)``
* ``123``
* ``toByte(0xFF)``
* ``-1``
* ``toByte('123')``
* ``123``
___
### <code>toDate</code>
<code><b>toDate(<i>&lt;string&gt;</i> : any, [<i>&lt;date format&gt;</i> : string]) => date</b></code><br/><br/>
省略可能な入力日付形式を使用して、入力日付文字列を日付に変換します。 使用可能な形式については、Java の `SimpleDateFormat` クラスを参照してください。 入力日付形式が省略されている場合、既定の形式は yyyy-[M]M-[d]d になります。 許容される形式は [ yyyy, yyyy-[M]M, yyyy-[M]M-[d]d, yyyy-[M]M-[d]dT* ] です。  
* ``toDate('2012-8-18') -> toDate('2012-08-18')``  
* ``toDate('12/18/2012', 'MM/dd/yyyy') -> toDate('2012-12-18')``  
___
### <code>toDecimal</code>
<code><b>toDecimal(<i>&lt;value&gt;</i> : any, [<i>&lt;precision&gt;</i> : integral], [<i>&lt;scale&gt;</i> : integral], [<i>&lt;format&gt;</i> : string], [<i>&lt;locale&gt;</i> : string]) => decimal(10,0)</b></code><br/><br/>
任意の数値または文字列を decimal 型の値に変換します。 有効桁数と小数点以下桁数を指定しない場合、既定値の (10,2) が使用されます。変換には、任意指定の Java 10 進数形式を使用できます。 en-US、de、zh-CN など、BCP47 言語形式に属する省略可能なロケール形式。  
* ``toDecimal(123.45) -> 123.45``  
* ``toDecimal('123.45', 8, 4) -> 123.4500``  
* ``toDecimal('$123.45', 8, 4,'$###.00') -> 123.4500``  
* ``toDecimal('Ç123,45', 10, 2, 'Ç###,##', 'de') -> 123.45``  
___
### <code>toDouble</code>
<code><b>toDouble(<i>&lt;value&gt;</i> : any, [<i>&lt;format&gt;</i> : string], [<i>&lt;locale&gt;</i> : string]) => double</b></code><br/><br/>
任意の数値または文字列を double 型の値に変換します。 省略可能な Java の 10 進形式を変換に使用できます。 en-US、de、zh-CN など、BCP47 言語形式に属する省略可能なロケール形式。  
* ``toDouble(123.45) -> 123.45``  
* ``toDouble('123.45') -> 123.45``  
* ``toDouble('$123.45', '$###.00') -> 123.45``  
* ``toDouble('Ç123,45', 'Ç###,##', 'de') -> 123.45``  
___
### <code>toFloat</code>
<code><b>toFloat(<i>&lt;value&gt;</i> : any, [<i>&lt;format&gt;</i> : string], [<i>&lt;locale&gt;</i> : string]) => float</b></code><br/><br/>
任意の数値または文字列を float 型の値に変換します。 省略可能な Java の 10 進形式を変換に使用できます。 double を切り捨てます。  
* ``toFloat(123.45) -> 123.45f``  
* ``toFloat('123.45') -> 123.45f``  
* ``toFloat('$123.45', '$###.00') -> 123.45f``  
___
### <code>toInteger</code>
<code><b>toInteger(<i>&lt;value&gt;</i> : any, [<i>&lt;format&gt;</i> : string], [<i>&lt;locale&gt;</i> : string]) => integer</b></code><br/><br/>
任意の数値または文字列を integer 型の値に変換します。 省略可能な Java の 10 進形式を変換に使用できます。 long、float、double を切り捨てます。  
* ``toInteger(123) -> 123``  
* ``toInteger('123') -> 123``  
* ``toInteger('$123', '$###') -> 123``  
___
### <code>toLong</code>
<code><b>toLong(<i>&lt;value&gt;</i> : any, [<i>&lt;format&gt;</i> : string], [<i>&lt;locale&gt;</i> : string]) => long</b></code><br/><br/>
任意の数値または文字列を long 型の値に変換します。 省略可能な Java の 10 進形式を変換に使用できます。 float、double を切り捨てます。  
* ``toLong(123) -> 123``  
* ``toLong('123') -> 123``  
* ``toLong('$123', '$###') -> 123``  
___
### <code>toShort</code>
<code><b>toShort(<i>&lt;value&gt;</i> : any, [<i>&lt;format&gt;</i> : string], [<i>&lt;locale&gt;</i> : string]) => short</b></code><br/><br/>
任意の数値または文字列を short 型の値に変換します。 省略可能な Java の 10 進形式を変換に使用できます。 integer、long、float、double を切り捨てます。  
* ``toShort(123) -> 123``  
* ``toShort('123') -> 123``  
* ``toShort('$123', '$###') -> 123``  
___
### <code>toString</code>
<code><b>toString(<i>&lt;value&gt;</i> : any, [<i>&lt;number format/date format&gt;</i> : string]) => string</b></code><br/><br/>
プリミティブ データ型を文字列に変換します。 数値と日付の場合は、形式を指定できます。 指定しない場合、システムの既定値が選択されます。数値には、Java の 10 進数形式が使用されます。 使用できるすべての日付形式については、Java SimpleDateFormat を参照してください。既定の形式は yyyy-MM-dd です。  
* ``toString(10) -> '10'``  
* ``toString('engineer') -> 'engineer'``  
* ``toString(123456.789, '##,###.##') -> '123,456.79'``  
* ``toString(123.78, '000000.000') -> '000123.780'``  
* ``toString(12345, '##0.#####E0') -> '12.345E3'``  
* ``toString(toDate('2018-12-31')) -> '2018-12-31'``  
* ``isNull(toString(toDate('2018-12-31', 'MM/dd/yy'))) -> true``  
* ``toString(4 == 20) -> 'false'``  
___
### <code>toTimestamp</code>
<code><b>toTimestamp(<i>&lt;string&gt;</i> : any, [<i>&lt;timestamp format&gt;</i> : string], [<i>&lt;time zone&gt;</i> : string]) => timestamp</b></code><br/><br/>
省略可能なタイムスタンプ形式を指定して、文字列をタイムスタンプに変換します。 タイムスタンプを省略すると、既定のパターンの yyyy-[M]M-[d]d hh:mm:ss[.f...] が使用されます。 省略可能なタイムゾーンを 'GMT'、'PST'、'UTC'、'America/Cayman' の形式で渡せます。 タイムスタンプでサポートされる最大精度は、ミリ秒 (値 999) です。 使用可能な形式については、Java の `SimpleDateFormat` クラスを参照してください。 https://docs.oracle.com/javase/8/docs/api/java/text/SimpleDateFormat.html.  
* ``toTimestamp('2016-12-31 00:12:00') -> toTimestamp('2016-12-31 00:12:00')``  
* ``toTimestamp('2016-12-31T00:12:00', 'yyyy-MM-dd\'T\'HH:mm:ss', 'PST') -> toTimestamp('2016-12-31 00:12:00')``  
* ``toTimestamp('12/31/2016T00:12:00', 'MM/dd/yyyy\'T\'HH:mm:ss') -> toTimestamp('2016-12-31 00:12:00')``  
* ``millisecond(toTimestamp('2019-02-03 05:19:28.871', 'yyyy-MM-dd HH:mm:ss.SSS')) -> 871``  
___
### <code>toUTC</code>
<code><b>toUTC(<i>&lt;value1&gt;</i> : timestamp, [<i>&lt;value2&gt;</i> : string]) => timestamp</b></code><br/><br/>
タイムスタンプを UTC に変換します。 省略可能なタイムゾーンを 'GMT'、'PST'、'UTC'、'America/Cayman' の形式で渡せます。 既定値は現在のタイムゾーンです。 使用可能な形式については、Java の `SimpleDateFormat` クラスを参照してください。 https://docs.oracle.com/javase/8/docs/api/java/text/SimpleDateFormat.html.  
* ``toUTC(currentTimestamp()) == toTimestamp('2050-12-12 19:18:12') -> false``  
* ``toUTC(currentTimestamp(), 'Asia/Seoul') != toTimestamp('2050-12-12 19:18:12') -> true``  

## <a name="metafunctions"></a>メタ関数

メタ関数は、主にデータ フロー内のメタデータに対して機能します。

### <code>byItem</code>
<code><b>byItem(<i>&lt;parent column&gt;</i> : any, <i>&lt;column name&gt;</i> : string) => any</b></code><br/><br/>
構造体または構造体の配列内のサブ項目を検索します。複数の一致がある場合は、最初の一致が返されます。 一致がない場合は、NULL 値が返されます。 返された値は、いずれかの型変換アクション (? date、? string など) で型変換する必要があります。設計時にわかっている列名は、その名前だけで処理する必要があります。 計算入力はサポートされていませんが、パラメーター置換を使用することができます * ``byItem( byName('customer'), 'orderItems') ? (itemName as string, itemQty as integer)``
* ````
* ``byItem( byItem( byName('customer'), 'orderItems'), 'itemName') ? string``
* ````
___
### <code>byOrigin</code>
<code><b>byOrigin(<i>&lt;column name&gt;</i> : string, [<i>&lt;origin stream name&gt;</i> : string]) => any</b></code><br/><br/>
元のストリームでの名前で列の値を選択します。 2 番目の引数は元のストリームの名前です。 複数の一致がある場合は、最初の一致が返されます。 一致がない場合は、NULL 値が返されます。 返された値は、いずれかの型変換関数 (TO_DATE、TO_STRING ...) で型変換する必要があります。設計時にわかっている列名は、その名前だけで処理する必要があります。 計算入力はサポートされていませんが、パラメーター置換を使用することができます。  
* ``toString(byOrigin('ancestor', 'ancestorStream'))``
___
### <code>byOrigins</code>
<code><b>byOrigins(<i>&lt;column names&gt;</i> : array, [<i>&lt;origin stream name&gt;</i> : string]) => any</b></code><br/><br/>
ストリーム内の名前を指定して列の配列を選択します。 2 番目の引数は発生したストリームです。 複数の一致がある場合は、最初の一致が返されます。 一致がない場合は、NULL 値が返されます。 返された値は、いずれかの型変換関数 (TO_DATE、TO_STRING ...) で型変換する必要があります設計時にわかっている列名は、その名前だけで処理する必要があります。 計算入力はサポートされていませんが、パラメーター置換を使用することができます。
* ``toString(byOrigins(['ancestor1', 'ancestor2'], 'ancestorStream'))``
___
### <code>byName</code>
<code><b>byName(<i>&lt;column name&gt;</i> : string, [<i>&lt;stream name&gt;</i> : string]) => any</b></code><br/><br/>
ストリームでの名前で列の値を選択します。 省略可能なストリーム名を 2 番目の引数として渡すことができます。 複数の一致がある場合は、最初の一致が返されます。 一致がない場合は、NULL 値が返されます。 返された値は、いずれかの型変換関数 (TO_DATE、TO_STRING ...) で型変換する必要があります。設計時にわかっている列名は、その名前だけで処理する必要があります。 計算入力はサポートされていませんが、パラメーター置換を使用することができます。  
* ``toString(byName('parent'))``  
* ``toLong(byName('income'))``  
* ``toBoolean(byName('foster'))``  
* ``toLong(byName($debtCol))``  
* ``toString(byName('Bogus Column'))``  
* ``toString(byName('Bogus Column', 'DeriveStream'))``  
___
### <code>byNames</code>
<code><b>byNames(<i>&lt;column names&gt;</i> : array, [<i>&lt;stream name&gt;</i> : string]) => any</b></code><br/><br/>
ストリーム内の名前を指定して列の配列を選択します。 省略可能なストリーム名を 2 番目の引数として渡すことができます。 複数の一致がある場合は、最初の一致が返されます。 列に一致するものがない場合は、出力全体が NULL 値になります。 戻り値には、型変換関数 (toDate、toString、...) が必要です。設計時にわかっている列名は、その名前だけで処理する必要があります。 計算入力はサポートされていませんが、パラメーター置換を使用することができます。
* ``toString(byNames(['parent', 'child']))``
* ``byNames(['parent']) ? string``
* ``toLong(byNames(['income']))``
* ``byNames(['income']) ? long``
* ``toBoolean(byNames(['foster']))``
* ``toLong(byNames($debtCols))``
* ``toString(byNames(['a Column']))``
* ``toString(byNames(['a Column'], 'DeriveStream'))``
* ``byNames(['orderItem']) ? (itemName as string, itemQty as integer)``
___
### <code>byPosition</code>
<code><b>byPosition(<i>&lt;position&gt;</i> : integer) => any</b></code><br/><br/>
ストリーム内の相対位置 (1 から始まる) で列の値を選択します。 位置が範囲外にある場合は、NULL 値が返されます。 返された値は、いずれかの型変換関数 (TO_DATE、TO_STRING ...) で型変換する必要があります計算入力はサポートされていませんが、パラメーター置換を使用することができます。  
* ``toString(byPosition(1))``  
* ``toDecimal(byPosition(2), 10, 2)``  
* ``toBoolean(byName(4))``  
* ``toString(byName($colName))``  
* ``toString(byPosition(1234))``  
___
### <code>hex</code>
<code><b>hex(<value1>: binary) => string</b></code><br/><br/>
バイナリ値の 16 進数文字列表記を返します * ``hex(toBinary([toByte(0x1f), toByte(0xad), toByte(0xbe)])) -> '1fadbe'``
___
### <code>unhex</code>
<code><b>unhex(<value1>: string) => binary</b></code><br/><br/>
バイナリ値を 16 進数文字列表記から数値に変換します。 これは、文字列からバイナリ表記に変換するために、sha2、md5 と組み合わせて使用できます *   ``unhex('1fadbe') -> toBinary([toByte(0x1f), toByte(0xad), toByte(0xbe)])``
*   ``unhex(md5(5, 'gunchus', 8.2, 'bojjus', true, toDate('2010-4-4'))) -> toBinary([toByte(0x4c),toByte(0xe8),toByte(0xa8),toByte(0x80),toByte(0xbd),toByte(0x62),toByte(0x1a),toByte(0x1f),toByte(0xfa),toByte(0xd0),toByte(0xbc),toByte(0xa9),toByte(0x05),toByte(0xe1),toByte(0xbc),toByte(0x5a)])``

## <a name="window-functions"></a>ウィンドウ関数
次の関数は、ウィンドウ変換でのみ使用できます。
___
### <code>cumeDist</code>
<code><b>cumeDist() => integer</b></code><br/><br/>
CumeDist 関数は、パーティション内のすべての値の相対値を計算します。 結果は、パーティションの順序内で現在行以前の行数を、ウィンドウ パーティション内の合計行数で除算した値です。 同順位の値は、同じ位置に評価されます。  
* ``cumeDist()``  
___
### <code>denseRank</code>
<code><b>denseRank() => integer</b></code><br/><br/>
ウィンドウの order by 句で指定した値のグループ内の値の順位を計算します。 結果は、パーティション順位内の現在行以前の行数に 1 を加算した値です。 値では、シーケンス内にギャップは生じません。 Dense Rank は、データが並べ替えられていない場合や、値の変更が予測される場合であっても機能します。  
* ``denseRank()``  
___
### <code>lag</code>
<code><b>lag(<i>&lt;value&gt;</i> : any, [<i>&lt;number of rows to look before&gt;</i> : number], [<i>&lt;default value&gt;</i> : any]) => any</b></code><br/><br/>
現在の行の n 行前を評価した最初のパラメーターの値を取得します。 2 番目のパラメーターは、戻る行の数です。既定値は 1 です。 指定した数の行がない場合、既定値が指定されていない限り、null 値が返されます。  
* ``lag(amount, 2)``  
* ``lag(amount, 2000, 100)``  
___
### <code>lead</code>
<code><b>lead(<i>&lt;value&gt;</i> : any, [<i>&lt;number of rows to look after&gt;</i> : number], [<i>&lt;default value&gt;</i> : any]) => any</b></code><br/><br/>
現在の行の n 行後を評価した最初のパラメーターの値を取得します。 2 番目のパラメーターは、進む行の数です。既定値は 1 です。 指定した数の行がない場合、既定値が指定されていない限り、null 値が返されます。  
* ``lead(amount, 2)``  
* ``lead(amount, 2000, 100)``  
___
### <code>nTile</code>
<code><b>nTile([<i>&lt;value1&gt;</i> : integer]) => integer</b></code><br/><br/>
```NTile``` 関数は、各ウィンドウ パーティションの行を `n` バケット (1 から最大 `n`) に分割します。 バケットの値の差は最大で 1 です。 パーティション内の行数がバケット数に対して均等に分割されない場合、残りの値は、最初のバケットから順番にバケットごとに 1 つずつ分配されます。 ```NTile``` 関数は、```tertiles```、四分位数、十分位数およびその他の一般的な集計統計情報を計算する場合に便利です。 この関数は、初期化中に次の 2 つの変数を計算します。通常のバケットのサイズには、さらに 1 行が追加されます。 どちらの変数も現在のパーティションのサイズに基づいています。 計算プロセス中に、この関数は現在の行番号、現在のバケット番号、およびバケットが変更される行番号 (bucketThreshold) を追跡します。 現在の行番号がバケットしきい値に達すると、バケットの値が 1 つ増加し、しきい値にはバケット サイズが追加されます (現在のバケットが埋め込まれている場合は、さらに 1 が追加されます)。  
* ``nTile()``  
* ``nTile(numOfBuckets)``  
___
### <code>rank</code>
<code><b>rank() => integer</b></code><br/><br/>
ウィンドウの order by 句で指定した値のグループ内の値の順位を計算します。 結果は、パーティション順位内の現在行以前の行数に 1 を加算した値です。 値では、シーケンス内にギャップが生じます。 Rank は、データが並べ替えられていない場合や、値の変更が予測される場合でも機能します。  
* ``rank()``  
___
### <code>rowNumber</code>
<code><b>rowNumber() => integer</b></code><br/><br/>
ウィンドウ内の行のシーケンシャル行番号を 1 から順番に割り当てます。  
* ``rowNumber()``  

## <a name="next-steps"></a>次のステップ

[式ビルダーの使用方法を学習します](concepts-data-flow-expression-builder.md)。
