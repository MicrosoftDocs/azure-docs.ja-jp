---
title: Azure Data Factory の Mapping Data Flow 機能の式関数
description: Mapping Data Flow の式関数について説明します。
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.topic: conceptual
ms.date: 02/15/2019
ms.openlocfilehash: 12a97e9f355ca1c71e926433e5eafd8b960a87da
ms.sourcegitcommit: b7a44709a0f82974578126f25abee27399f0887f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/18/2019
ms.locfileid: "67203211"
---
# <a name="data-transformation-expressions-in-mapping-data-flow"></a>Mapping Data Flow のデータ変換式 

[!INCLUDE [notes](../../includes/data-factory-data-flow-preview.md)]

## <a name="expression-functions"></a>式関数

Data Factory では、Mapping Data Flow の機能の式言語を使用してデータ変換を構成します。

---
### <code>abs</code>
<code><b>abs(<i>&lt;value1&gt;</i> : number) => number</b></code><br/><br/>
数値のペアの正の剰余です。
* ``abs(-20) -> 20``
* ``abs(10) -> 10``
---
### <code>acos</code>
<code><b>acos(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
逆コサイン値を計算します * ``acos(1) -> 0.0``
---
### <code>add</code>
<code><b>add(<i>&lt;value1&gt;</i> : any, <i>&lt;value2&gt;</i> : any) => any</b></code><br/><br/>
文字列または数値のペアを追加します。 日付に日数を加算します。 別の配列に類似するタイプの配列を追加します。 \+ 演算子と同じです * ``add(10, 20) -> 30``
* ``10 + 20 -> 30``
* ``add('ice', 'cream') -> 'icecream'``
* ``'ice' + 'cream' + ' cone' -> 'icecream cone'``
* ``add(toDate('2012-12-12'), 3) -> 2012-12-15 (date value)``
* ``toDate('2012-12-12') + 3 -> 2012-12-15 (date value)``
* ``[10, 20] + [30, 40] => [10, 20, 30, 40]``
---
### <code>addDays</code>
<code><b>addDays(<i>&lt;date/timestamp&gt;</i> : datetime, <i>&lt;days to add&gt;</i> : integral) => datetime</b></code><br/><br/>
日付またはタイムスタンプに日数を加算します。 日付に対する + 演算子と同じです * ``addDays(toDate('2016-08-08'), 1) -> 2016-08-09``
---
### <code>addMonths</code>
<code><b>addMonths(<i>&lt;date/timestamp&gt;</i> : datetime, <i>&lt;months to add&gt;</i> : integral) => datetime</b></code><br/><br/>
日付またはタイムスタンプに月数を加算します * ``addMonths(toDate('2016-08-31'), 1) -> 2016-09-30``
* ``addMonths(toTimestamp('2016-09-30 10:10:10'), -1) -> 2016-08-31 10:10:10``
---
### <code>and</code>
<code><b>and(<i>&lt;value1&gt;</i> : boolean, <i>&lt;value2&gt;</i> : boolean) => boolean</b></code><br/><br/>
AND 論理演算子。 && と同じです * ``and(true, false) -> false``
* ``true && false -> false``
---
### <code>asin</code>
<code><b>asin(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
逆サイン値を計算します * ``asin(0) -> 0.0``
---
### <code>atan</code>
<code><b>atan(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
逆タンジェント値を計算します * ``atan(0) -> 0.0``
---
### <code>atan2</code>
<code><b>atan2(<i>&lt;value1&gt;</i> : number, <i>&lt;value2&gt;</i> : number) => double</b></code><br/><br/>
座標で指定された、平面の正の x 軸と点の間の角度をラジアンで返します * ``atan2(0, 0) -> 0.0``
---
### <code>avg</code>
<code><b>avg(<i>&lt;value1&gt;</i> : number) => number</b></code><br/><br/>
列の値の平均を取得します * ``avg(sales) -> 7523420.234``
---
### <code>avgIf</code>
<code><b>avgIf(<i>&lt;value1&gt;</i> : boolean, <i>&lt;value2&gt;</i> : number) => number</b></code><br/><br/>
条件に基づいて、列の値の平均を取得します * ``avgIf(region == 'West', sales) -> 7523420.234``
---
### <code>byName</code>
<code><b>byName(<i>&lt;column name&gt;</i> : string) => any</b></code><br/><br/>
ストリームでの名前で列の値を選択します。 複数の一致がある場合は、最初の一致が返されます。 一致がない場合は、NULL 値が返されます。 返された値は、いずれかの型変換関数 (TO_DATE、TO_STRING ...) で型変換する必要があります。設計時にわかっている列名は、その名前だけで処理する必要があります。 計算入力はサポートされていませんが、パラメーター置換を使用することができます * ``toString(byName('parent')) -> appa``
* ``toLong(byName('income')) -> 9000000000009``
* ``toBoolean(byName('foster')) -> false``
* ``toLong(byName($debtCol)) -> 123456890``
* ``birthDate -> 12/31/2050``
* ``toString(byName('Bogus Column')) -> NULL``
---
### <code>byPosition</code>
<code><b>byPosition(<i>&lt;position&gt;</i> : integer) => any</b></code><br/><br/>
ストリーム内の相対位置 (1 から始まる) で列の値を選択します。 位置が範囲外にある場合は、NULL 値が返されます。 返された値は、いずれかの型変換関数 (TO_DATE、TO_STRING ...) で型変換する必要があります。設計時にわかっている列名は、その名前だけで処理する必要があります * ``toString(byPosition(1)) -> amma``
* ``toDecimal(byPosition(2), 10, 2) -> 199990.99``
* ``toBoolean(byName(4)) -> false``
* ``toString(byName($colName)) -> family``
* ``toString(byPosition(1234)) -> NULL``
---
### <code>case</code>
<code><b>case(<i>&lt;condition&gt;</i> : boolean, <i>&lt;true_expression&gt;</i> : any, <i>&lt;false_expression&gt;</i> : any, ...) => any</b></code><br/><br/>
交互条件に基づいて、2 つの値のいずれかを適用します。 入力数が偶数の場合、最後の条件でその他は NULL になります * ``case(custType == 'Premium', 10, 4.5)``
* ``case(custType == 'Premium', price*0.95, custType == 'Elite',   price*0.9, price*2)``
* ``case(dayOfWeek(saleDate) == 1, 'Sunday', dayOfWeek(saleDate) == 6, 'Saturday')``
---
### <code>cbrt</code>
<code><b>cbrt(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
数値の立方根を計算します * ``cbrt(8) -> 2.0``
---
### <code>ceil</code>
<code><b>ceil(<i>&lt;value1&gt;</i> : number) => number</b></code><br/><br/>
特定の数値以上の最小の整数を返します * ``ceil(-0.1) -> 0``
---
### <code>concat</code>
<code><b>concat(<i>&lt;this&gt;</i> : string, <i>&lt;that&gt;</i> : string, ...) => string</b></code><br/><br/>
可変数の文字列を連結します。 文字列で使用する + 演算子と同じです * ``concat('Awesome', 'Cool', 'Product') -> 'AwesomeCoolProduct'``
* ``'Awesome' + 'Cool' + 'Product' -> 'AwesomeCoolProduct'``
* ``concat(addrLine1, ' ', addrLine2, ' ', city, ' ', state, ' ', zip)``
* ``addrLine1 + ' ' + addrLine2 + ' ' + city + ' ' + state + ' ' + zip``
---
### <code>concatWS</code>
<code><b>concatWS(<i>&lt;separator&gt;</i> : string, <i>&lt;this&gt;</i> : string, <i>&lt;that&gt;</i> : string, ...) => string</b></code><br/><br/>
可変数の文字列を区切り記号を使用して連結します。 最初のパラメーターは区切り記号です * ``concatWS(' ', 'Awesome', 'Cool', 'Product') -> 'Awesome Cool Product'``
* ``concatWS(' ' , addrLine1, addrLine2, city, state, zip) ->``
* ``concatWS(',' , toString(order_total), toString(order_discount))``
---
### <code>cos</code>
<code><b>cos(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
コサイン値を計算します * ``cos(10) -> -0.83907152907``
---
### <code>cosh</code>
<code><b>cosh(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
値の双曲線コサインを計算します * ``cosh(0) -> 1.0``
---
### <code>count</code>
<code><b>count([<i>&lt;value1&gt;</i> : any]) => long</b></code><br/><br/>
値の集計カウントを取得します。 省略可能な列を指定すると、カウントの NULL 値が無視されます * ``count(custId) -> 100``
* ``count(custId, custName) -> 50``
* ``count() -> 125``
* ``count(iif(isNull(custId), 1, NULL)) -> 5``
---
### <code>countDistinct</code>
<code><b>countDistinct(<i>&lt;value1&gt;</i> : any, [<i>&lt;value2&gt;</i> : any], ...) => long</b></code><br/><br/>
列セットの個別値の集計カウントを取得します * ``countDistinct(custId, custName) -> 60``
---
### <code>countIf</code>
<code><b>countIf(<i>&lt;value1&gt;</i> : boolean, [<i>&lt;value2&gt;</i> : any]) => long</b></code><br/><br/>
条件に基づいて、値の集計カウントを取得します。 省略可能な列を指定すると、カウントの NULL 値が無視されます * ``countIf(state == 'CA' && commission < 10000, name) -> 100``
---
### <code>covariancePopulation</code>
<code><b>covariancePopulation(<i>&lt;value1&gt;</i> : number, <i>&lt;value2&gt;</i> : number) => double</b></code><br/><br/>
2 つの列の間の母共分散を取得します * ``covariancePopulation(sales, profit) -> 122.12``
---
### <code>covariancePopulationIf</code>
<code><b>covariancePopulationIf(<i>&lt;value1&gt;</i> : boolean, <i>&lt;value2&gt;</i> : number, <i>&lt;value3&gt;</i> : number) => double</b></code><br/><br/>
条件に基づいて、2 つの列の間の母共分散を取得します * ``covariancePopulationIf(region == 'West', sales) -> 122.12``
---
### <code>covarianceSample</code>
<code><b>covarianceSample(<i>&lt;value1&gt;</i> : number, <i>&lt;value2&gt;</i> : number) => double</b></code><br/><br/>
2 つの列の標本共分散を取得します * ``covarianceSample(sales, profit) -> 122.12``
---
### <code>covarianceSampleIf</code>
<code><b>covarianceSampleIf(<i>&lt;value1&gt;</i> : boolean, <i>&lt;value2&gt;</i> : number, <i>&lt;value3&gt;</i> : number) => double</b></code><br/><br/>
条件に基づいて、2 つの列の標本共分散を取得します * ``covarianceSampleIf(region == 'West', sales, profit) -> 122.12``
---
### <code>crc32</code>
<code><b>crc32(<i>&lt;value1&gt;</i> : any, ...) => long</b></code><br/><br/>
さまざまなプリミティブ データ型の列セットの CRC32 ハッシュを指定のビット長で計算します。ビット長として指定できるのは、0(256)、224、256、384、512 の値のみです。 行のフィンガープリントを計算するために使用できます * ``crc32(256, 'gunchus', 8.2, 'bojjus', true, toDate('2010-4-4')) -> 3630253689``
---
### <code>cumeDist</code>
<code><b>cumeDist() => integer</b></code><br/><br/>
CumeDist 関数は、パーティション内のすべての値の相対値を計算します。 結果は、パーティションの順序内で現在行以前の行数を、ウィンドウ パーティション内の合計行数で除算した値です。 同順位の値は、同じ位置に評価されます。
* ``cumeDist() -> 1``
---
### <code>currentDate</code>
<code><b>currentDate([<i>&lt;value1&gt;</i> : string]) => date</b></code><br/><br/>
このジョブの実行を開始する現在の日付を取得します。 省略可能なタイムゾーンを 'GMT'、'PST'、'UTC'、'America/Cayman' の形式で渡せます。 ローカル タイムゾーンが既定値として使用されます。
* ``currentDate() -> 12-12-2030``
* ``currentDate('PST') -> 12-31-2050``
---
### <code>currentTimestamp</code>
<code><b>currentTimestamp() => timestamp</b></code><br/><br/>
ジョブの実行を開始する現在のタイムスタンプをローカル タイムゾーンを使用して取得します * ``currentTimestamp() -> 12-12-2030T12:12:12``
---
### <code>currentUTC</code>
<code><b>currentUTC([<i>&lt;value1&gt;</i> : string]) => timestamp</b></code><br/><br/>
現在のタイムスタンプを UTC で取得します。 省略可能なタイムゾーンを 'GMT'、'PST'、'UTC'、'America/Cayman' の形式で渡せます。 既定値は現在のタイムゾーンです * ``currentUTC() -> 12-12-2030T19:18:12``
* ``currentUTC('Asia/Seoul') -> 12-13-2030T11:18:12``
---
### <code>dayOfMonth</code>
<code><b>dayOfMonth(<i>&lt;value1&gt;</i> : datetime) => integer</b></code><br/><br/>
指定した日付から、その月の何日目かを取得します * ``dayOfMonth(toDate('2018-06-08')) -> 08``
---
### <code>dayOfWeek</code>
<code><b>dayOfWeek(<i>&lt;value1&gt;</i> : datetime) => integer</b></code><br/><br/>
指定した日付から、曜日を取得します。 1 - 日曜日、2 - 月曜日...、7 - 土曜日 * ``dayOfWeek(toDate('2018-06-08')) -> 7``
---
### <code>dayOfYear</code>
<code><b>dayOfYear(<i>&lt;value1&gt;</i> : datetime) => integer</b></code><br/><br/>
指定した日付から、その年の何日目かを取得します * ``dayOfYear(toDate('2016-04-09')) -> 100``
---
### <code>degrees</code>
<code><b>degrees(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
ラジアンを角度に変換します * ``degrees(3.141592653589793) -> 180``
---
### <code>denseRank</code>
<code><b>denseRank(<i>&lt;value1&gt;</i> : any, ...) => integer</b></code><br/><br/>
値のグループ内の順位値を計算します。 結果は、パーティション順位内の現在行以前の行数に 1 を加算した値です。 値では、シーケンス内にギャップは生じません。 Dense Rank は、データが並べ替えられていない場合や、値の変更が予測される場合でも機能します * ``denseRank(salesQtr, salesAmt) -> 1``
---
### <code>divide</code>
<code><b>divide(<i>&lt;value1&gt;</i> : any, <i>&lt;value2&gt;</i> : any) => any</b></code><br/><br/>
数値のペアを除算します。 / 演算子と同じです * ``divide(20, 10) -> 2``
* ``20 / 10 -> 2``
---
### <code>endsWith</code>
<code><b>endsWith(<i>&lt;string&gt;</i> : string, <i>&lt;substring to check&gt;</i> : string) => boolean</b></code><br/><br/>
文字列が指定した文字列で終了しているかをチェックします * ``endsWith('great', 'eat') -> true``
---
### <code>equals</code>
<code><b>equals(<i>&lt;value1&gt;</i> : any, <i>&lt;value2&gt;</i> : any) => boolean</b></code><br/><br/>
等価比較演算子。 == 演算子と同じです * ``equals(12, 24) -> false``
* ``12==24 -> false``
* ``'bad'=='bad' -> true``
* ``'good'== NULL -> false``
* ``NULL===NULL -> false``
---
### <code>equalsIgnoreCase</code>
<code><b>equalsIgnoreCase(<i>&lt;value1&gt;</i> : string, <i>&lt;value2&gt;</i> : string) => boolean</b></code><br/><br/>
大文字と小文字の区別を無視する等価比較演算子。 <=> 演算子と同じです * ``'abc'<==>'abc' -> true``
* ``equalsIgnoreCase('abc', 'Abc') -> true``
---
### <code>factorial</code>
<code><b>factorial(<i>&lt;value1&gt;</i> : number) => long</b></code><br/><br/>
数値の階乗を計算します * ``factorial(5) -> 120``
---
### <code>false</code>
<code><b>false() => boolean</b></code><br/><br/>
常に false 値を返します。 "false" という列名がある場合は、関数構文 (false()) を使用します * ``isDiscounted == false()``
* ``isDiscounted() == false``
---
### <code>first</code>
<code><b>first(<i>&lt;value1&gt;</i> : any, [<i>&lt;value2&gt;</i> : boolean]) => any</b></code><br/><br/>
列グループの最初の値を取得します。 2 番目のパラメーター ignoreNulls を省略すると、false であるとみなされます * ``first(sales) -> 12233.23``
* ``first(sales, false) -> NULL``
---
### <code>floor</code>
<code><b>floor(<i>&lt;value1&gt;</i> : number) => number</b></code><br/><br/>
特定の数値以下の最大の整数を返します * ``floor(-0.1) -> -1``
---
### <code>fromUTC</code>
<code><b>fromUTC(<i>&lt;value1&gt;</i> : timestamp, [<i>&lt;value2&gt;</i> : string]) => timestamp</b></code><br/><br/>
UTC からタイムスタンプに変換します。 必要に応じて、タイムゾーンを "GMT"、"PST"、"UTC"、"America/Cayman" の形式で渡すことができます。 既定値は現在のタイムゾーンです * ``fromUTC(currentTimeStamp()) -> 12-12-2030T19:18:12``
* ``fromUTC(currentTimeStamp(), 'Asia/Seoul') -> 12-13-2030T11:18:12``
---
### <code>greater</code>
<code><b>greater(<i>&lt;value1&gt;</i> : any, <i>&lt;value2&gt;</i> : any) => boolean</b></code><br/><br/>
比較超過演算子。 > 演算子と同じです * ``greater(12, 24) -> false``
* ``'abcd' > 'abc' -> true``
---
### <code>greaterOrEqual</code>
<code><b>greaterOrEqual(<i>&lt;value1&gt;</i> : any, <i>&lt;value2&gt;</i> : any) => boolean</b></code><br/><br/>
比較 (以上) 演算子。 >= 演算子と同じです * ``greaterOrEqual(12, 12) -> false``
* ``'abcd' >= 'abc' -> true``
---
### <code>greatest</code>
<code><b>greatest(<i>&lt;value1&gt;</i> : any, ...) => any</b></code><br/><br/>
入力値のリストの中の最大値を返します。 すべての入力が null の場合は null を返します * ``greatest(10, 30, 15, 20) -> 30``
* ``greatest(toDate('12/12/2010'), toDate('12/12/2011'), toDate('12/12/2000')) -> '12/12/2011'``
---
### <code>hour</code>
<code><b>hour(<i>&lt;value1&gt;</i> : timestamp, [<i>&lt;value2&gt;</i> : string]) => integer</b></code><br/><br/>
タイムスタンプから時間の値を取得します。 省略可能なタイムゾーンを 'GMT'、'PST'、'UTC'、'America/Cayman' の形式で渡せます。 ローカル タイムゾーンが既定値として使用されます。
* ``hour(toTimestamp('2009-07-30T12:58:59')) -> 12``
* ``hour(toTimestamp('2009-07-30T12:58:59'), 'PST') -> 12``
---
### <code>iif</code>
<code><b>iif(<i>&lt;condition&gt;</i> : boolean, <i>&lt;true_expression&gt;</i> : any, [<i>&lt;false_expression&gt;</i> : any]) => any</b></code><br/><br/>
条件に基づいて、2 つの値のいずれかを適用します。 その他が指定されていない場合は、NULL と見なされます。 両方の値は互換性がなければなりません (数値、文字列...) * ``iif(custType == 'Premium', 10, 4.5)``
* ``iif(amount > 100, 'High')``
* ``iif(dayOfWeek(saleDate) == 6, 'Weekend', 'Weekday')``
---
### <code>in</code>
<code><b>in(<i>&lt;array of items&gt;</i> : array, <i>&lt;item to find&gt;</i> : any) => boolean</b></code><br/><br/>
項目が配列内にあるかどうかをチェックします * ``in([10, 20, 30], 10) -> true``
* ``in(['good', 'kid'], 'bad') -> false``
---
### <code>initCap</code>
<code><b>initCap(<i>&lt;value1&gt;</i> : string) => string</b></code><br/><br/>
すべての単語の最初の文字を大文字に変換します。 単語は、空白文字で区切られているものとして識別されます * ``initCap('cool iceCREAM') -> 'Cool IceCREAM'``
---
### <code>instr</code>
<code><b>instr(<i>&lt;string&gt;</i> : string, <i>&lt;substring to find&gt;</i> : string) => integer</b></code><br/><br/>
文字列内の部分文字列の位置 (1 を基準とする) を見つけます。 見つからない場合は 0 が返されます * ``instr('great', 'eat') -> 3``
* ``instr('microsoft', 'o') -> 7``
* ``instr('good', 'bad') -> 0``
---
### <code>isDelete</code>
<code><b>isDelete([<i>&lt;value1&gt;</i> : integer]) => boolean</b></code><br/><br/>
行が削除用にマークされているかどうかをチェックします。 1 つ以上の入力ストリームを取る変換は、ストリームの (1 から始まる) インデックスを渡すことができます。 ストリーム インデックスの既定値は 1 です * ``isDelete() -> true``
* ``isDelete(1) -> false``
---
### <code>isError</code>
<code><b>isError([<i>&lt;value1&gt;</i> : integer]) => boolean</b></code><br/><br/>
行がエラーとしてマークされているかどうかをチェックします。 1 つ以上の入力ストリームを取る変換は、ストリームの (1 から始まる) インデックスを渡すことができます。 ストリーム インデックスの既定値は 1 です * ``isError() -> true``
* ``isError(1) -> false``
---
### <code>isIgnore</code>
<code><b>isIgnore([<i>&lt;value1&gt;</i> : integer]) => boolean</b></code><br/><br/>
行を無視するようにマークされているかどうかをチェックします。 1 つ以上の入力ストリームを取る変換は、ストリームの (1 から始まる) インデックスを渡すことができます。 ストリーム インデックスの既定値は 1 です * ``isIgnore() -> true``
* ``isIgnore(1) -> false``
---
### <code>isInsert</code>
<code><b>isInsert([<i>&lt;value1&gt;</i> : integer]) => boolean</b></code><br/><br/>
行が挿入用にマークされているかどうかをチェックします。 1 つ以上の入力ストリームを取る変換は、ストリームの (1 から始まる) インデックスを渡すことができます。 ストリーム インデックスの既定値は 1 です * ``isInsert() -> true``
* ``isInsert(1) -> false``
---
### <code>isMatch</code>
<code><b>isMatch([<i>&lt;value1&gt;</i> : integer]) => boolean</b></code><br/><br/>
行がルックアップで一致するかどうかをチェックします。 1 つ以上の入力ストリームを取る変換は、ストリームの (1 から始まる) インデックスを渡すことができます。 ストリーム インデックスの既定値は 1 です * ``isMatch() -> true``
* ``isMatch(1) -> false``
---
### <code>isNull</code>
<code><b>isNull(<i>&lt;value1&gt;</i> : any) => boolean</b></code><br/><br/>
値が NULL かどうかをチェックします * ``isNull(NULL()) -> true``
* ``isNull('') -> false'``
---
### <code>isUpdate</code>
<code><b>isUpdate([<i>&lt;value1&gt;</i> : integer]) => boolean</b></code><br/><br/>
行が更新用にマークされているかどうかをチェックします。 1 つ以上の入力ストリームを取る変換は、ストリームの (1 から始まる) インデックスを渡すことができます。 ストリーム インデックスの既定値は 1 です * ``isUpdate() -> true``
* ``isUpdate(1) -> false``
---
### <code>kurtosis</code>
<code><b>kurtosis(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
列の尖度を取得します * ``kurtosis(sales) -> 122.12``
---
### <code>kurtosisIf</code>
<code><b>kurtosisIf(<i>&lt;value1&gt;</i> : boolean, <i>&lt;value2&gt;</i> : number) => double</b></code><br/><br/>
条件に基づいて、列の尖度を取得します * ``kurtosisIf(region == 'West', sales) -> 122.12``
---
### <code>lag</code>
<code><b>lag(<i>&lt;value&gt;</i> : any, [<i>&lt;number of rows to look before&gt;</i> : number], [<i>&lt;default value&gt;</i> : any]) => any</b></code><br/><br/>
現在の行の n 行前を評価した最初のパラメーターの値を取得します。 2 番目のパラメーターは、戻る行の数です。既定値は 1 です。 指定した数の行がない場合、既定値が指定されていない限り、null 値が返されます * ``lag(amount, 2) -> 60``
* ``lag(amount, 2000, 100) -> 100``
---
### <code>last</code>
<code><b>last(<i>&lt;value1&gt;</i> : any, [<i>&lt;value2&gt;</i> : boolean]) => any</b></code><br/><br/>
列グループの最後の値を取得します。 2 番目のパラメーター ignoreNulls を省略すると、false であるとみなされます * ``last(sales) -> 523.12``
* ``last(sales, false) -> NULL``
---
### <code>lastDayOfMonth</code>
<code><b>lastDayOfMonth(<i>&lt;value1&gt;</i> : datetime) => date</b></code><br/><br/>
指定した日付から、その月の最後の日を取得します * ``lastDayOfMonth(toDate('2009-01-12')) -> 2009-01-31``
---
### <code>lead</code>
<code><b>lead(<i>&lt;value&gt;</i> : any, [<i>&lt;number of rows to look after&gt;</i> : number], [<i>&lt;default value&gt;</i> : any]) => any</b></code><br/><br/>
現在の行の n 行後を評価した最初のパラメーターの値を取得します。 2 番目のパラメーターは、進む行の数です。既定値は 1 です。 指定した数の行がない場合、既定値が指定されていない限り、null 値が返されます * ``lead(amount, 2) -> 60``
* ``lead(amount, 2000, 100) -> 100``
---
### <code>least</code>
<code><b>least(<i>&lt;value1&gt;</i> : any, ...) => any</b></code><br/><br/>
比較 (以下) 演算子。 <= 演算子と同じです * ``least(10, 30, 15, 20) -> 10``
* ``least(toDate('12/12/2010'), toDate('12/12/2011'), toDate('12/12/2000')) -> '12/12/2000'``
---
### <code>left</code>
<code><b>left(<i>&lt;string to subset&gt;</i> : string, <i>&lt;number of characters&gt;</i> : integral) => string</b></code><br/><br/>
インデックス 1 から開始して指定した文字数の部分文字列を抽出します。 SUBSTRING(str, 1, n) と同じです * ``left('bojjus', 2) -> 'bo'``
* ``left('bojjus', 20) -> 'bojjus'``
---
### <code>length</code>
<code><b>length(<i>&lt;value1&gt;</i> : string) => integer</b></code><br/><br/>
文字列の長さを返します * ``length('kiddo') -> 5``
---
### <code>lesser</code>
<code><b>lesser(<i>&lt;value1&gt;</i> : any, <i>&lt;value2&gt;</i> : any) => boolean</b></code><br/><br/>
比較未満演算子。 < 演算子と同じです * ``lesser(12 < 24) -> true``
* ``'abcd' < 'abc' -> false``
---
### <code>lesserOrEqual</code>
<code><b>lesserOrEqual(<i>&lt;value1&gt;</i> : any, <i>&lt;value2&gt;</i> : any) => boolean</b></code><br/><br/>
比較 (以下) 演算子。 <= 演算子と同じです * ``lesserOrEqual(12, 12) -> true``
* ``'abcd' <= 'abc' -> false``
---
### <code>levenshtein</code>
<code><b>levenshtein(<i>&lt;from string&gt;</i> : string, <i>&lt;to string&gt;</i> : string) => integer</b></code><br/><br/>
2 つの文字列の間のレーベンシュタイン距離を取得します * ``levenshtein('boys', 'girls') -> 4``
---
### <code>like</code>
<code><b>like(<i>&lt;string&gt;</i> : string, <i>&lt;pattern match&gt;</i> : string) => boolean</b></code><br/><br/>
パターンは文字通り一致する文字列です。 次の特殊文字は例外です。_ は入力内の任意の 1 文字と一致します (posix 正規表現の . に 類似)、% は入力内の 0 文字以上と一致します (posix 正規表現の * に類似)。
エスケープ文字は '' です。 エスケープ文字の前に特殊記号または別のエスケープ文字がある場合、その次の文字が文字通り照合されます。 その他の文字をエスケープするのは無効です。
* ``like('icecream', 'ice%') -> true``
---
### <code>locate</code>
<code><b>locate(<i>&lt;substring to find&gt;</i> : string, <i>&lt;string&gt;</i> : string, [<i>&lt;from index - 1-based&gt;</i> : integral]) => integer</b></code><br/><br/>
特定の位置から開始して、文字列内の部分文字列の位置 (1 を基準とする) を見つけます。 位置を省略すると、文字列の最初からとみなされます。 見つからない場合は 0 が返されます * ``locate('eat', 'great') -> 3``
* ``locate('o', 'microsoft', 6) -> 7``
* ``locate('bad', 'good') -> 0``
---
### <code>log</code>
<code><b>log(<i>&lt;value1&gt;</i> : number, [<i>&lt;value2&gt;</i> : number]) => double</b></code><br/><br/>
対数の値を計算します。 省略可能な底を指定できます。省略すると、オイラー数を返します (使用された場合) * ``log(100, 10) -> 2``
---
### <code>log10</code>
<code><b>log10(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
10 を底とする対数の値を計算します * ``log10(100) -> 2``
---
### <code>lower</code>
<code><b>lower(<i>&lt;value1&gt;</i> : string) => string</b></code><br/><br/>
文字列を小文字にします * ``lower('GunChus') -> 'gunchus'``
---
### <code>lpad</code>
<code><b>lpad(<i>&lt;string to pad&gt;</i> : string, <i>&lt;final padded length&gt;</i> : integral, <i>&lt;padding&gt;</i> : string) => string</b></code><br/><br/>
特定の長さになるまで、指定した埋め込み文字で文字列の左側を埋め込みます。 文字列が指定された長さ以上の場合は、何も実行しないと見なされます。* ``lpad('great', 10, '-') -> '-----great'``
* ``lpad('great', 4, '-') -> 'great'``
* ``lpad('great', 8, '<>') -> '<><great'``
---
### <code>ltrim</code>
<code><b>ltrim(<i>&lt;string to trim&gt;</i> : string, <i>&lt;trim characters&gt;</i> : string) => string</b></code><br/><br/>
文字列の先頭文字を左から削除します。 2 番目のパラメーターを指定しない場合、空白文字を削除します。 それ以外の場合は、2 番目のパラメーターに指定した任意の文字を削除します * ``ltrim('!--!wor!ld!', '-!') -> 'wor!ld!'``
---
### <code>max</code>
<code><b>max(<i>&lt;value1&gt;</i> : any) => any</b></code><br/><br/>
列の最大値を取得します * ``MAX(sales) -> 12312131.12``
---
### <code>maxIf</code>
<code><b>maxIf(<i>&lt;value1&gt;</i> : boolean, <i>&lt;value2&gt;</i> : any) => any</b></code><br/><br/>
条件に基づいて、列の値の最大値を取得します * ``maxIf(region == 'West', sales) -> 99999.56``
---
### <code>md5</code>
<code><b>md5(<i>&lt;value1&gt;</i> : any, ...) => string</b></code><br/><br/>
さまざまなプリミティブ データ型の列セットの MD5 ハッシュを計算し、32 文字の16 進数の文字列を返します。 行のフィンガープリントを計算するために使用できます * ``md5(5, 'gunchus', 8.2, 'bojjus', true, toDate('2010-4-4')) -> 'c1527622a922c83665e49835e46350fe'``
---
### <code>mean</code>
<code><b>mean(<i>&lt;value1&gt;</i> : number) => number</b></code><br/><br/>
列の値の平均を取得します。 AVG と同じです * ``mean(sales) -> 7523420.234``
---
### <code>meanIf</code>
<code><b>meanIf(<i>&lt;value1&gt;</i> : boolean, <i>&lt;value2&gt;</i> : number) => number</b></code><br/><br/>
条件に基づいて、列の値の平均を取得します。 avgIf と同じです * ``meanIf(region == 'West', sales) -> 7523420.234``
---
### <code>min</code>
<code><b>min(<i>&lt;value1&gt;</i> : any) => any</b></code><br/><br/>
列の最小値を取得します * ``min(sales) -> 00.01``
* ``min(orderDate) -> 12/12/2000``
---
### <code>minIf</code>
<code><b>minIf(<i>&lt;value1&gt;</i> : boolean, <i>&lt;value2&gt;</i> : any) => any</b></code><br/><br/>
条件に基づいて、列の値の最小値を取得します * ``minIf(region == 'West', sales) -> 00.01``
---
### <code>minus</code>
<code><b>minus(<i>&lt;value1&gt;</i> : any, <i>&lt;value2&gt;</i> : any) => any</b></code><br/><br/>
数値を減算します。 日付から日数を減算します。 \- 演算子と同じです * ``minus(20, 10) -> 10``
* ``20 - 10 -> 10``
* ``minus(toDate('2012-12-15'), 3) -> 2012-12-12 (date value)``
* ``toDate('2012-12-15') - 3 -> 2012-12-13 (date value)``
---
### <code>minute</code>
<code><b>minute(<i>&lt;value1&gt;</i> : timestamp, [<i>&lt;value2&gt;</i> : string]) => integer</b></code><br/><br/>
タイムスタンプから分の値を取得します。 省略可能なタイムゾーンを 'GMT'、'PST'、'UTC'、'America/Cayman' の形式で渡せます。 ローカル タイムゾーンが既定値として使用されます。
* ``minute(toTimestamp('2009-07-30T12:58:59')) -> 58``
* ``minute(toTimestamp('2009-07-30T12:58:59', 'PST')) -> 58``
---
### <code>mod</code>
<code><b>mod(<i>&lt;value1&gt;</i> : any, <i>&lt;value2&gt;</i> : any) => any</b></code><br/><br/>
数値のペアの剰余です。 % 演算子と同じです * ``mod(20, 8) -> 4``
* ``20 % 8 -> 4``
---
### <code>month</code>
<code><b>month(<i>&lt;value1&gt;</i> : datetime) => integer</b></code><br/><br/>
日付またはタイムスタンプから月の値を取得します * ``month(toDate('2012-8-8')) -> 8``
---
### <code>monthsBetween</code>
<code><b>monthsBetween(<i>&lt;from date/timestamp&gt;</i> : datetime, <i>&lt;to date/timestamp&gt;</i> : datetime, [<i>&lt;time zone&gt;</i> : boolean], [<i>&lt;value4&gt;</i> : string]) => double</b></code><br/><br/>
2 つの日付の間の月数を取得します。省略可能なタイムゾーンを 'GMT'、'PST'、'UTC'、'America/Cayman' の形式で渡せます。 ローカル タイムゾーンが既定値として使用されます。
* ``monthsBetween(toDate('1997-02-28 10:30:00'), toDate('1996-10-30')) -> 3.94959677``
---
### <code>multiply</code>
<code><b>multiply(<i>&lt;value1&gt;</i> : any, <i>&lt;value2&gt;</i> : any) => any</b></code><br/><br/>
数値のペアを乗算します。 \* 演算子と同じです * ``multiply(20, 10) -> 200``
* ``20 * 10 -> 200``
---
### <code>nTile</code>
<code><b>nTile([<i>&lt;value1&gt;</i> : integer]) => integer</b></code><br/><br/>
NTile 関数は、各ウィンドウ パーティションの行を `n` バケット (1 から最大 `n`) に分割します。 バケットの値の差は最大で 1 です。 パーティション内の行数がバケット数に対して均等に分割されない場合、残りの値は、最初のバケットから順番にバケットごとに 1 つずつ分配されます。 NTile 関数は、三分位数、四分位数、十分位数およびその他の一般的な集計統計情報を計算する場合に便利です。 この関数は、初期化中に次の 2 つの変数を計算します。通常のバケットのサイズには、さらに 1 行が追加されます。 どちらの変数も現在のパーティションのサイズに基づいています。 計算プロセス中に、この関数は現在の行番号、現在のバケット番号、およびバケットが変更される行番号 (bucketThreshold) を追跡します。 現在の行番号がバケットしきい値に達すると、バケットの値が 1 つ増加し、しきい値にはバケット サイズが追加されます (現在のバケットが埋め込まれている場合は、さらに 1 が追加されます)。
* ``nTile() -> 1``
* ``nTile(numOfBuckets) -> 1``
---
### <code>negate</code>
<code><b>negate(<i>&lt;value1&gt;</i> : number) => number</b></code><br/><br/>
数値の符号を反転します。 正の数値を負の数値 (または、その逆) に変換します * ``negate(13) -> -13``
---
### <code>nextSequence</code>
<code><b>nextSequence() => long</b></code><br/><br/>
次の固有なシーケンスを返します。 数値は、パーティション内でのみ連続し、プレフィックスとして partitionId が付加されます * ``nextSequence() -> 12313112``
---
### <code>normalize</code>
<code><b>normalize(<i>&lt;String to normalize&gt;</i> : string) => string</b></code><br/><br/>
文字列値を個別にアクセント記号が付いた Unicode 文字に正規化します * ``normalize('boys') -> 'boys'``
---
### <code>not</code>
<code><b>not(<i>&lt;value1&gt;</i> : boolean) => boolean</b></code><br/><br/>
論理否定演算子 * ``not(true) -> false``
* ``not(premium)``
---
### <code>notEquals</code>
<code><b>notEquals(<i>&lt;value1&gt;</i> : any, <i>&lt;value2&gt;</i> : any) => boolean</b></code><br/><br/>
比較不等価演算子。 != 演算子と同じです * ``12!=24 -> true``
* ``'abc'!='abc' -> false``
---
### <code>null</code>
<code><b>null() => null</b></code><br/><br/>
NULL 値を返します。 "null" という列がある場合は、関数構文 (null()) を使用します。 これを使用する操作はすべて NULL になります * ``custId = NULL (for derived field)``
* ``custId == NULL -> NULL``
* ``'nothing' + NULL -> NULL``
* ``10 * NULL -> NULL'``
* ``NULL == '' -> NULL'``
---
### <code>or</code>
<code><b>or(<i>&lt;value1&gt;</i> : boolean, <i>&lt;value2&gt;</i> : boolean) => boolean</b></code><br/><br/>
OR 論理演算子。 || と同じです * ``or(true, false) -> true``
* ``true || false -> true`` ---
### <code>pMod</code>
<code><b>pMod(<i>&lt;value1&gt;</i> : any, <i>&lt;value2&gt;</i> : any) => any</b></code><br/><br/>
数値のペアの正の剰余です。
* ``pmod(-20, 8) -> 4``
---
### <code>power</code>
<code><b>power(<i>&lt;value1&gt;</i> : number, <i>&lt;value2&gt;</i> : number) => double</b></code><br/><br/>
数値を別の数値のべき乗値に指定します * ``power(10, 2) -> 100``
---
### <code>rank</code>
<code><b>rank(<i>&lt;value1&gt;</i> : any, ...) => integer</b></code><br/><br/>
値のグループ内の順位値を計算します。 結果は、パーティション順位内の現在行以前の行数に 1 を加算した値です。 値では、シーケンス内にギャップが生じます。 Rank は、データが並べ替えられていない場合や、値の変更が予測される場合でも機能します * ``rank(salesQtr, salesAmt) -> 1``
---
### <code>regexExtract</code>
<code><b>regexExtract(<i>&lt;string&gt;</i> : string, <i>&lt;regex to find&gt;</i> : string, [<i>&lt;match group 1-based index&gt;</i> : integral]) => string</b></code><br/><br/>
指定された正規表現パターンに一致する部分文字列を抽出します。 最後のパラメーターは、一致グループを識別し、省略すると既定値として 1 が使用されます。 エスケープせずに文字列を照合するには、`<regex>` (バック クォート) を使用します * ``regexExtract('Cost is between 600 and 800 dollars', '(\\d+) and (\\d+)', 2) -> '800'``
* ``regexExtract('Cost is between 600 and 800 dollars', `(\d+) and (\d+)`, 2) -> '800'``
---
### <code>regexMatch</code>
<code><b>regexMatch(<i>&lt;string&gt;</i> : string, <i>&lt;regex to match&gt;</i> : string) => boolean</b></code><br/><br/>
指定された正規表現パターンに文字列が一致するかどうかをチェックします。 エスケープせずに文字列を照合するには、`<regex>` (バック クォート) を使用します * ``regexMatch('200.50', '(\\d+).(\\d+)') -> true``
* ``regexMatch('200.50', `(\d+).(\d+)`) -> true``
---
### <code>regexReplace</code>
<code><b>regexReplace(<i>&lt;string&gt;</i> : string, <i>&lt;regex to find&gt;</i> : string, <i>&lt;substring to replace&gt;</i> : string) => string</b></code><br/><br/>
指定の文字列内の正規表現パターンのすべての出現を別の部分文字列に置換します。エスケープせずに文字列を照合するには、`<regex>`(バック クォート) を使用します * ``regexReplace('100 and 200', '(\\d+)', 'bojjus') -> 'bojjus and bojjus'``
* ``regexReplace('100 and 200', `(\d+)`, 'gunchus') -> 'gunchus and gunchus'``
---
### <code>regexSplit</code>
<code><b>regexSplit(<i>&lt;string to split&gt;</i> : string, <i>&lt;regex expression&gt;</i> : string) => array</b></code><br/><br/>
文字列を正規表現に基づく区切り文字に基づいて分割し、文字列の配列を返します * ``regexSplit('oneAtwoBthreeC', '[CAB]') -> ['one', 'two', 'three']``
* ``regexSplit('oneAtwoBthreeC', '[CAB]')[1] -> 'one'``
* ``regexSplit('oneAtwoBthreeC', '[CAB]')[0] -> NULL``
* ``regexSplit('oneAtwoBthreeC', '[CAB]')[20] -> NULL``
---
### <code>replace</code>
<code><b>replace(<i>&lt;string&gt;</i> : string, <i>&lt;substring to find&gt;</i> : string, <i>&lt;substring to replace&gt;</i> : string) => string</b></code><br/><br/>
指定された文字列内の部分文字列のすべての出現を別の部分文字列に置換します * ``replace('doggie dog', 'dog', 'cat') -> 'catgie cat'``
* ``replace('doggie dog', 'dog', '') -> 'gie'``
---
### <code>reverse</code>
<code><b>reverse(<i>&lt;value1&gt;</i> : string) => string</b></code><br/><br/>
文字列を反転します * ``reverse('gunchus') -> 'suhcnug'``
---
### <code>right</code>
<code><b>right(<i>&lt;string to subset&gt;</i> : string, <i>&lt;number of characters&gt;</i> : integral) => string</b></code><br/><br/>
指定した文字数の部分文字列を右から抽出します。 SUBSTRING(str, LENGTH(str) - n, n) と同じです * ``right('bojjus', 2) -> 'us'``
* ``right('bojjus', 20) -> 'bojjus'``
---
### <code>rlike</code>
<code><b>rlike(<i>&lt;string&gt;</i> : string, <i>&lt;pattern match&gt;</i> : string) => boolean</b></code><br/><br/>
指定された正規表現パターンに文字列が一致するかどうかをチェックします * ``rlike('200.50', '(\d+).(\d+)') -> true``
---
### <code>round</code>
<code><b>round(<i>&lt;number&gt;</i> : number, [<i>&lt;scale to round&gt;</i> : number], [<i>&lt;rounding option&gt;</i> : integral]) => double</b></code><br/><br/>
省略可能な桁数と省略可能な丸めモードで数値を丸めます。 桁数を省略すると、既定値の 0 が使用されます。  モードを省略すると、既定値は ROUND_HALF_UP(5) になります。 丸めの値には、1 - ROUND_UP、2 - ROUND_DOWN、3 - ROUND_CEILING、4 - ROUND_FLOOR、5 - ROUND_HALF_UP、6 - ROUND_HALF_DOWN、7 - ROUND_HALF_EVEN、8 - ROUND_UNNECESSARY があります * ``round(100.123) -> 100.0``
* ``round(2.5, 0) -> 3.0``
* ``round(5.3999999999999995, 2, 7) -> 5.40``
---
### <code>rowNumber</code>
<code><b>rowNumber() => integer</b></code><br/><br/>
ウィンドウ内の行のシーケンシャル行番号を 1 から順番に割り当てます* ``rowNumber() -> 1``
---
### <code>rpad</code>
<code><b>rpad(<i>&lt;string to pad&gt;</i> : string, <i>&lt;final padded length&gt;</i> : integral, <i>&lt;padding&gt;</i> : string) => string</b></code><br/><br/>
特定の長さになるまで、指定した埋め込み文字で文字列の右側を埋め込みます。 文字列が指定された長さ以上の場合は、何も実行しないと見なされます。* ``rpad('great', 10, '-') -> 'great-----'``
* ``rpad('great', 4, '-') -> 'great'``
* ``rpad('great', 8, '<>') -> 'great<><'``
---
### <code>rtrim</code>rtrim</code>
<code><b>rtrim(<i>&lt;string to trim&gt;</i> : string, <i>&lt;trim characters&gt;</i> : string) => string</b></code><br/><br/>
文字列の先頭文字を右から削除します。 2 番目のパラメーターを指定しない場合、空白文字を削除します。 それ以外の場合は、2 番目のパラメーターに指定した任意の文字を削除します * ``rtrim('!--!wor!ld!', '-!') -> '!--!wor!ld'``
---
### <code>second</code>
<code><b>second(<i>&lt;value1&gt;</i> : timestamp, [<i>&lt;value2&gt;</i> : string]) => integer</b></code><br/><br/>
日付の秒の値を取得します。 省略可能なタイムゾーンを 'GMT'、'PST'、'UTC'、'America/Cayman' の形式で渡せます。 ローカル タイムゾーンが既定値として使用されます。
* ``second(toTimestamp('2009-07-30T12:58:59')) -> 59``
---
### <code>sha1</code>
<code><b>sha1(<i>&lt;value1&gt;</i> : any, ...) => string</b></code><br/><br/>
さまざまなプリミティブ データ型の列セットの SHA-1 ハッシュを計算し、40 文字の16 進数の文字列を返します。 行のフィンガープリントを計算するために使用できます * ``sha1(5, 'gunchus', 8.2, 'bojjus', true, toDate('2010-4-4')) -> '63849fd2abb65fbc626c60b1f827bd05573f0cea'``
---
### <code>sha2</code>
<code><b>sha2(<i>&lt;value1&gt;</i> : integer, <i>&lt;value2&gt;</i> : any, ...) => string</b></code><br/><br/>
さまざまなプリミティブ データ型の列セットの SHA-2 ハッシュを指定のビット長で計算します。ビット長として指定できるのは、0(256)、224、256、384、512 の値のみです。 行のフィンガープリントを計算するために使用できます * ``sha2(256, 'gunchus', 8.2, 'bojjus', true, toDate('2010-4-4')) -> 'd3b2bff62c3a00e9370b1ac85e428e661a7df73959fa1a96ae136599e9ee20fd'``
---
### <code>sin</code>
<code><b>sin(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
サイン値を計算します * ``sin(2) -> 0.90929742682``
---
### <code>sinh</code>
<code><b>sinh(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
双曲線サイン値を計算します * ``sinh(0) -> 0.0``
---
### <code>skewness</code>
<code><b>skewness(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
列の歪度を取得します * ``skewness(sales) -> 122.12``
---
### <code>skewnessIf</code>
<code><b>skewnessIf(<i>&lt;value1&gt;</i> : boolean, <i>&lt;value2&gt;</i> : number) => double</b></code><br/><br/>
条件に基づいて、列の歪度を取得します * ``skewnessIf(region == 'West', sales) -> 122.12``
---
### <code>slice</code>
<code><b>slice(<i>&lt;array to slice&gt;</i> : array, <i>&lt;from 1-based index&gt;</i> : integral, [<i>&lt;number of items&gt;</i> : integral]) => array</b></code><br/><br/>
ある位置から配列のサブセットを抽出します。 位置は 1 から始まります。 長さを省略すると、既定値は文字列の最後になります * ``slice([10, 20, 30, 40], 1, 2) -> [10, 20]``
* ``slice([10, 20, 30, 40], 2) -> [20, 30, 40]``
* ``slice([10, 20, 30, 40], 2)[1] -> 20``
* ``slice([10, 20, 30, 40], 2)[0] -> NULL``
* ``slice([10, 20, 30, 40], 2)[20] -> NULL``
* ``slice([10, 20, 30, 40], 8) -> []``
---
### <code>soundex</code>
<code><b>soundex(<i>&lt;value1&gt;</i> : string) => string</b></code><br/><br/>
文字列の soundex コードを取得します * ``soundex('genius') -> 'G520'``
---
### <code>split</code>
<code><b>split(<i>&lt;string to split&gt;</i> : string, <i>&lt;split characters&gt;</i> : string) => array</b></code><br/><br/>
文字列を区切り文字に基づいて分割し、文字列の配列を返します * ``split('100,200,300', ',') -> ['100', '200', '300']``
* ``split('100,200,300', '|') -> ['100,200,300']``
* ``split('100, 200, 300', ', ') -> ['100', '200', '300']``
* ``split('100, 200, 300', ', ')[1] -> '100'``
* ``split('100, 200, 300', ', ')[0] -> NULL``
* ``split('100, 200, 300', ', ')[20] -> NULL``
* ``split('100200300', ',') -> ['100200300']``
---
### <code>sqrt</code>
<code><b>sqrt(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
数値の平方根を計算します * ``sqrt(9) -> 3``
---
### <code>startsWith</code>
<code><b>startsWith(<i>&lt;string&gt;</i> : string, <i>&lt;substring to check&gt;</i> : string) => boolean</b></code><br/><br/>
文字列が指定した文字列で開始しているかをチェックします * ``startsWith('great', 'gr') -> true``
---
### <code>stddev</code>
<code><b>stddev(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
列の標準偏差を取得します * ``stdDev(sales) -> 122.12``
---
### <code>stddevIf</code>
<code><b>stddevIf(<i>&lt;value1&gt;</i> : boolean, <i>&lt;value2&gt;</i> : number) => double</b></code><br/><br/>
条件に基づいて、列の標準偏差を取得します * ``stddevIf(region == 'West', sales) -> 122.12``
---
### <code>stddevPopulation</code>
<code><b>stddevPopulation(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
列の母標準偏差を取得します * ``stddevPopulation(sales) -> 122.12``
---
### <code>stddevPopulationIf</code>
<code><b>stddevPopulationIf(<i>&lt;value1&gt;</i> : boolean, <i>&lt;value2&gt;</i> : number) => double</b></code><br/><br/>
条件に基づいて、列の母標準偏差を取得します * ``stddevPopulationIf(region == 'West', sales) -> 122.12``
---
### <code>stddevSample</code>
<code><b>stddevSample(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
列の標本標準偏差を取得します * ``stddevSample(sales) -> 122.12``
---
### <code>stddevSampleIf</code>
<code><b>stddevSampleIf(<i>&lt;value1&gt;</i> : boolean, <i>&lt;value2&gt;</i> : number) => double</b></code><br/><br/>
条件に基づいて、列の標本標準偏差を取得します * ``stddevSampleIf(region == 'West', sales) -> 122.12``
---
### <code>subDays</code>
<code><b>subDays(<i>&lt;date/timestamp&gt;</i> : datetime, <i>&lt;days to subtract&gt;</i> : integral) => datetime</b></code><br/><br/>
日付から月数を減算します。 日付に対する - 演算子と同じです * ``subDays(toDate('2016-08-08'), 1) -> 2016-08-09``
---
### <code>subMonths</code>
<code><b>subMonths(<i>&lt;date/timestamp&gt;</i> : datetime, <i>&lt;months to subtract&gt;</i> : integral) => datetime</b></code><br/><br/>
日付またはタイムスタンプから月数を減算します * ``subMonths(toDate('2016-09-30'), 1) -> 2016-08-31``
---
### <code>substring</code>
<code><b>substring(<i>&lt;string to subset&gt;</i> : string, <i>&lt;from 1-based index&gt;</i> : integral, [<i>&lt;number of characters&gt;</i> : integral]) => string</b></code><br/><br/>
ある位置から特定の長さの部分文字列を抽出します。 位置は 1 から始まります。 長さを省略すると、既定値は文字列の最後になります * ``substring('Cat in the hat', 5, 2) -> 'in'``
* ``substring('Cat in the hat', 5, 100) -> 'in the hat'``
* ``substring('Cat in the hat', 5) -> 'in the hat'``
* ``substring('Cat in the hat', 100, 100) -> ''``
---
### <code>sum</code>
<code><b>sum(<i>&lt;value1&gt;</i> : number) => number</b></code><br/><br/>
数値列の集計を取得します * ``sum(col) -> value``
---
### <code>sumDistinct</code>
<code><b>sumDistinct(<i>&lt;value1&gt;</i> : number) => number</b></code><br/><br/>
数値列の個別値の集計を取得します * ``sumDistinct(col) -> value``
---
### <code>sumDistinctIf</code>
<code><b>sumDistinctIf(<i>&lt;value1&gt;</i> : boolean, <i>&lt;value2&gt;</i> : number) => number</b></code><br/><br/>
条件に基づいて、数値列の集計を取得します。 条件は、任意の列に基づくことができます * ``sumDistinctIf(state == 'CA' && commission < 10000, sales) -> value``
* ``sumDistinctIf(true, sales) -> SUM(sales)``
*********************************
### <code>sumIf</code>
<code><b>sumIf(<i>&lt;value1&gt;</i> : boolean, <i>&lt;value2&gt;</i> : number) => number</b></code><br/><br/>
条件に基づいて、数値列の集計を取得します。 条件は、任意の列に基づくことができます * ``sumIf(state == 'CA' && commission < 10000, sales) -> value``
* ``sumIf(true, sales) -> SUM(sales)``
*********************************
### <code>tan</code>
<code><b>tan(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
タンジェント値を計算します * ``tan(0) -> 0.0``
---
### <code>tanh</code>
<code><b>tanh(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
双曲線タンジェント値を計算します * ``tanh(0) -> 0.0``
---
### <code>toBoolean</code>
<code><b>toBoolean(<i>&lt;value1&gt;</i> : string) => boolean</b></code><br/><br/>
('t'、'true'、'y'、'yes'、'1') の値を true に変換し、('f'、'false'、'n'、'no'、'0') の値を false に変換し、それ以外の値を NULL に変換します * ``toBoolean('true') -> true``
* ``toBoolean('n') -> false``
* ``toBoolean('truthy') -> NULL``
---
### <code>toDate</code>
<code><b>toDate(<i>&lt;string&gt;</i> : any, [<i>&lt;date format&gt;</i> : string]) => date</b></code><br/><br/>
省略可能な日付形式を指定して、文字列を日付に変換します。 使用できるすべての形式については、Java SimpleDateFormat を参照してください。 日付形式を省略すると、次の組み合わせを使用できます。 [ yyyy, yyyy-[M]M, yyyy-[M]M-[d]d, yyyy-[M]M-[d]d, yyyy-[M]M-[d]d, yyyy-[M]M-[d]dT* ] * ``toDate('2012-8-8') -> 2012-8-8``
* ``toDate('12/12/2012', 'MM/dd/yyyy') -> 2012-12-12``
---
### <code>toDecimal</code>
<code><b>toDecimal(<i>&lt;value&gt;</i> : any, [<i>&lt;precision&gt;</i> : integral], [<i>&lt;scale&gt;</i> : integral], [<i>&lt;format&gt;</i> : string], [<i>&lt;locale&gt;</i> : string]) => decimal(10,0)</b></code><br/><br/>
任意の数値または文字列を decimal 型の値に変換します。 有効桁数と小数点以下桁数を指定しない場合、既定値の (10,2) が使用されます。変換には、任意指定の Java 10 進数形式を使用できます。 en-US、de、zh-CN など、BCP47 言語の形式の省略可能なロケール形式 * ``toDecimal(123.45) -> 123.45``
* ``toDecimal('123.45', 8, 4) -> 123.4500``
* ``toDecimal('$123.45', 8, 4,'$###.00') -> 123.4500``
* ``toDecimal('Ç123,45', 10, 2, 'Ç###,##', 'de') -> 123.45``
---
### <code>toDouble</code>
<code><b>toDouble(<i>&lt;value&gt;</i> : any, [<i>&lt;format&gt;</i> : string], [<i>&lt;locale&gt;</i> : string]) => double</b></code><br/><br/>
任意の数値または文字列を double 型の値に変換します。 省略可能な Java の 10 進形式を変換に使用できます。 en-US、de、zh-CN など、BCP47 言語の形式の省略可能なロケール形式 * ``toDouble(123.45) -> 123.45``
* ``toDouble('123.45') -> 123.45``
* ``toDouble('$123.45', '$###.00') -> 123.45``
* ``toDouble('Ç123,45', 'Ç###,##', 'de') -> 123.45``
---
### <code>toFloat</code>
<code><b>toFloat(<i>&lt;value&gt;</i> : any, [<i>&lt;format&gt;</i> : string], [<i>&lt;locale&gt;</i> : string]) => float</b></code><br/><br/>
任意の数値または文字列を float 型の値に変換します。 省略可能な Java の 10 進形式を変換に使用できます。 double を切り捨てます * ``toFloat(123.45) -> 123.45``
* ``toFloat('123.45') -> 123.45``
* ``toFloat('$123.45', '$###.00') -> 123.45``
---
### <code>toInteger</code>
<code><b>toInteger(<i>&lt;value&gt;</i> : any, [<i>&lt;format&gt;</i> : string], [<i>&lt;locale&gt;</i> : string]) => integer</b></code><br/><br/>
任意の数値または文字列を integer 型の値に変換します。 省略可能な Java の 10 進形式を変換に使用できます。 long、float、double を切り捨てます * ``toInteger(123) -> 123``
* ``toInteger('123') -> 123``
* ``toInteger('$123', '$###') -> 123``
---
### <code>toLong</code>
<code><b>toLong(<i>&lt;value&gt;</i> : any, [<i>&lt;format&gt;</i> : string], [<i>&lt;locale&gt;</i> : string]) => long</b></code><br/><br/>
任意の数値または文字列を long 型の値に変換します。 省略可能な Java の 10 進形式を変換に使用できます。 float、double を切り捨てます * ``toLong(123) -> 123``
* ``toLong('123') -> 123``
* ``toLong('$123', '$###') -> 123``
---
### <code>toShort</code>
<code><b>toShort(<i>&lt;value&gt;</i> : any, [<i>&lt;format&gt;</i> : string], [<i>&lt;locale&gt;</i> : string]) => short</b></code><br/><br/>
任意の数値または文字列を short 型の値に変換します。 省略可能な Java の 10 進形式を変換に使用できます。 integer、long、float、double を切り捨てます * ``toShort(123) -> 123``
* ``toShort('123') -> 123``
* ``toShort('$123', '$###') -> 123``
---
### <code>toString</code>
<code><b>toString(<i>&lt;value&gt;</i> : any, [<i>&lt;number format/date format&gt;</i> : string]) => string</b></code><br/><br/>
プリミティブ データ型を文字列に変換します。 数値と日付の場合は、形式を指定できます。 指定しない場合、システムの既定値が選択されます。数値には、Java の 10 進数形式が使用されます。 使用できるすべての日付形式については、Java SimpleDateFormat を参照してください。既定の形式は yyyy-MM-dd です * ``toString(10) -> '10'``
* ``toString('engineer') -> 'engineer'``
* ``toString(123456.789, '##,###.##') -> '123,456.79'``
* ``toString(123.78, '000000.000') -> '000123.780'``
* ``toString(12345, '##0.#####E0') -> '12.345E3'``
* ``toString(toDate('2018-12-31')) -> '2018-12-31'``
* ``toString(toDate('2018-12-31'), 'MM/dd/yy') -> '12/31/18'``
* ``toString(4 == 20) -> 'false'``
---
### <code>toTimestamp</code>
<code><b>toTimestamp(<i>&lt;string&gt;</i> : any, [<i>&lt;timestamp format&gt;</i> : string], [<i>&lt;time zone&gt;</i> : string]) => timestamp</b></code><br/><br/>
省略可能なタイムスタンプ形式を指定して、文字列を日付に変換します。 使用できるすべての形式については、Java SimpleDateFormat を参照してください。 タイムスタンプを省略すると、既定のパターンの yyyy-[M]M-[d]d hh:mm:ss[.f...] が使用されます * ``toTimestamp('2016-12-31 00:12:00') -> 2012-8-8T00:12:00``
* ``toTimestamp('2016/12/31T00:12:00', 'MM/dd/yyyyThh:mm:ss') -> 2012-12-12T00:12:00``
---
### <code>toUTC</code>
<code><b>toUTC(<i>&lt;value1&gt;</i> : timestamp, [<i>&lt;value2&gt;</i> : string]) => timestamp</b></code><br/><br/>
タイムスタンプを UTC に変換します。 省略可能なタイムゾーンを 'GMT'、'PST'、'UTC'、'America/Cayman' の形式で渡せます。 既定値は現在のタイムゾーンです * ``toUTC(currentTimeStamp()) -> 12-12-2030T19:18:12``
* ``toUTC(currentTimeStamp(), 'Asia/Seoul') -> 12-13-2030T11:18:12``
---
### <code>translate</code>
<code><b>translate(<i>&lt;string to translate&gt;</i> : string, <i>&lt;lookup characters&gt;</i> : string, <i>&lt;replace characters&gt;</i> : string) => string</b></code><br/><br/>
文字列内のある文字セットを別の文字セットで置換します。 文字の置換は 1 対 1 で行われます * ``translate('(Hello)', '()', '[]') -> '[Hello]'``
* ``translate('(Hello)', '()', '[') -> '[Hello'``
---
### <code>trim</code>
<code><b>trim(<i>&lt;string to trim&gt;</i> : string, [<i>&lt;trim characters&gt;</i> : string]) => string</b></code><br/><br/>
文字列の先頭文字と末尾文字を削除します。 2 番目のパラメーターを指定しない場合、空白文字を削除します。 それ以外の場合は、2 番目のパラメーターに指定した任意の文字を削除します * ``trim('!--!wor!ld!', '-!') -> 'wor!ld'``
---
### <code>true</code>
<code><b>true() => boolean</b></code><br/><br/>
常に true 値を返します。 "true" という列名がある場合は、関数構文 (true()) を使用します * ``isDiscounted == true()``
* ``isDiscounted() == true``
---
### <code>typeMatch</code>
<code><b>typeMatch(<i>&lt;type&gt;</i> : string, <i>&lt;base type&gt;</i> : string) => boolean</b></code><br/><br/>
列の型を照合します。 パターン式でのみ使用できます。数値は short、integer、long、double、float、または decimal 型に一致し、整数は short、integer、long 型に一致し、小数は double、float、decimal 型に一致し、日時は date または timestamp 型に一致します * ``typeMatch(type, 'number') -> true``
* ``typeMatch('date', 'number') -> false``
---
### <code>upper</code>
<code><b>upper(<i>&lt;value1&gt;</i> : string) => string</b></code><br/><br/>
文字列を大文字にします * ``upper('bojjus') -> 'BOJJUS'``
---
### <code>variance</code>
<code><b>variance(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
列の分散を取得します * ``variance(sales) -> 122.12``
---
### <code>varianceIf</code>
<code><b>varianceIf(<i>&lt;value1&gt;</i> : boolean, <i>&lt;value2&gt;</i> : number) => double</b></code><br/><br/>
条件に基づいて、列の分散を取得します * ``varianceIf(region == 'West', sales) -> 122.12``
---
### <code>variancePopulation</code>
<code><b>variancePopulation(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
列の母分散を取得します * ``variancePopulation(sales) -> 122.12``
---
### <code>variancePopulationIf</code>
<code><b>variancePopulationIf(<i>&lt;value1&gt;</i> : boolean, <i>&lt;value2&gt;</i> : number) => double</b></code><br/><br/>
条件に基づいて、列の母分散を取得します * ``variancePopulationIf(region == 'West', sales) -> 122.12``
---
### <code>varianceSample</code>
<code><b>varianceSample(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
列の不偏分散を取得します * ``varianceSample(sales) -> 122.12``
---
### <code>varianceSampleIf</code>
<code><b>varianceSampleIf(<i>&lt;value1&gt;</i> : boolean, <i>&lt;value2&gt;</i> : number) => double</b></code><br/><br/>
条件に基づいて、列の不偏分散を取得します * ``varianceSampleIf(region == 'West', sales) -> 122.12``
---
### <code>weekOfYear</code>
<code><b>weekOfYear(<i>&lt;value1&gt;</i> : datetime) => integer</b></code><br/><br/>
指定した日付から、その年の何週目かを取得します * ``weekOfYear(toDate('2008-02-20')) -> 8``
---
### <code>xor</code>
<code><b>xor(<i>&lt;value1&gt;</i> : boolean, <i>&lt;value2&gt;</i> : boolean) => boolean</b></code><br/><br/>
XOR 論理演算子。 ^ 演算子と同じです * ``xor(true, false) -> true``
* ``xor(true, true) -> false``
* ``true ^ false -> true``
---
### <code>year</code>
<code><b>year(<i>&lt;value1&gt;</i> : datetime) => integer</b></code><br/><br/>
日付の年の値を取得します。* ``year(toDate('2012-8-8')) -> 2012``

## <a name="next-steps"></a>次の手順

[式ビルダーの使用方法を学習します](concepts-data-flow-expression-builder.md)。
