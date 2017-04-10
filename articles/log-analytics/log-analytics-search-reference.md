---
title: "Azure Log Analytics 検索リファレンス | Microsoft Docs"
description: "Log Analytics 検索リファレンスでは、検索言語について説明し、データを検索するときに使用できる一般的なクエリ構文のオプションと、検索を絞り込むために使用できるフィルター式の情報を提供します。"
services: log-analytics
documentationcenter: 
author: bandersmsft
manager: carmonm
editor: 
ms.assetid: 402615a2-bed0-4831-ba69-53be49059718
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/27/2017
ms.author: banders
ms.custom: H1Hack27Feb2017
translationtype: Human Translation
ms.sourcegitcommit: a0c8af30fbed064001c3fd393bf0440aa1cb2835
ms.openlocfilehash: a294ef357ea3c656e4e6392e89ad1d90b829e2cb
ms.lasthandoff: 02/28/2017


---
# <a name="log-analytics-search-reference"></a>Log Analytics 検索リファレンス
検索言語に関する次の参照セクションは、データを検索するときに使用できる一般的なクエリ構文のオプションと、検索を絞り込むために使用できるフィルター式について説明しています。 取得されたデータに対して何らかのアクションを実行するために使用できるコマンドについても説明しています。

検索で返されるフィールドと、類似するデータ カテゴリの詳細を確認するために役立つファセットについては、「 [検索フィールドとファセットのリファレンス](#search-field-and-facet-reference)」セクションを参照してください。

## <a name="general-query-syntax"></a>一般的なクエリ構文
構文:

```
filterExpression | command1 | command2 …
```

フィルター式 (`filterExpression`) は、クエリの "where" 条件を定義します。 コマンドは、クエリによって返された結果に適用されます。 複数のコマンドは、パイプ文字 (|) で区切る必要があります。

### <a name="general-syntax-examples"></a>一般的な構文の例
次に例を示します。

```
system
```

このクエリは、フル テキストまたは用語検索用のインデックスが設定されている任意のフィールドに "system" という単語を含む結果を返します。

> [!NOTE]
> すべてのフィールドがこのようにインデックス設定されているわけではありませんが、最も一般的なテキスト フィールド (説明や名前など) には通常は設定されています。
>
>

```
system error
```

このクエリは、*system* と *error* という単語を含む結果を返します。

```
system error | sort ManagementGroupName, TimeGenerated desc | top 10
```

このクエリは、*system* と *error* という単語を含む結果を返します。 結果は、*ManagementGroupName* フィールドによって (昇順に) 並べ替えられた後、*TimeGenerated* によって (降順に) 並べ替えられます。 最初の 10 個の結果だけが取得されます。

> [!IMPORTANT]
> すべてのフィールド名と、文字列フィールドとテキスト フィールドの値では、大文と字小文字が区別されます。
>
>

## <a name="filter-expression"></a>フィルター式
以降のサブセクションでは、フィルター式について説明します。

### <a name="string-literals"></a>文字列リテラル
文字列リテラルは、パーサーによってキーワードまたは定義済みのデータ型 (数値や日付など) として認識されないすべての文字列です。

次に例を示します。

```
These all are string literals
```

このクエリは、5 つの単語のすべてが出現する結果を検索します。 複雑な文字列検索を実行するには、リテラル文字列を引用符で囲みます。次に例を示します。

```
" Windows Server"
```

これは、"Windows Server" と完全に一致する結果のみを返します。

### <a name="numbers"></a>数値
パーサーは、数値フィールドで&10; 進数の整数と浮動小数点数をサポートします。

次に例を示します。

```
Type:Perf 0.5
```

```
HTTP 500
```

### <a name="datetime"></a>日付/時刻
システム内のすべてのデータは *TimeGenerated* プロパティを持っています。これはレコードの元の日付と時刻を表します。 データの種類によっては、さらに多くの日付/時刻フィールドを持つことができます (たとえば *LastModified*)。

Log Analytics のタイムライン グラフ/タイム セレクターは、 *TimeGenerated* フィールドに基づき (実行されている現在のクエリに従って)、時間の経過による結果の分布を表示します。 日付/時刻フィールドには、クエリを特定の期間に制限するためにクエリ内で使用できる文字列の形式があります。 相対的な期間 (たとえば "3 日前と 2 時間前の間") を参照する構文を使用することもできます。

構文:

```
yyyy-mm-ddThh:mm:ss.dddZ
```

```
yyyy-mm-ddThh:mm:ss.ddd
```

```
yyyy-mm-ddThh:mm:ss
```

```
yyyy-mm-ddThh:mm:ss
```

```
yyyy-mm-ddThh:mm
```

```
yyyy-mm-dd
```


例:

```
TimeGenerated:2013-10-01T12:20
```

上のコマンドは、 *TimeGenerated* の値が 2013 年 10 月 1 日 12 時 20 分であるレコードのみを返します。 このコマンドで結果が返る可能性はほとんどないのですが、考え方を理解してください。

パーサーは、ニーモニックな日付/時刻値である NOW もサポートします。

ここでも、データがシステムをそれほど高速で通過することはないため、結果を得られる可能性はほとんどありません。

これらの例は、相対的な日付と絶対的な日付を使用するための構成要素です。 次の&3; つのサブセクションでは、高度なフィルター内でのそれらの使用方法について、相対的な日付範囲を使用する例を用いて説明します。

### <a name="datetime-math"></a>日付/時刻の計算
日付/時刻算術演算子を使用して、単純な日付/時刻計算による日付/時刻値をオフセットするか丸めます。

構文:

```
datetime/unit
```

```
datetime[+|-]count unit
```

| 演算子 | 説明 |
| --- | --- |
| / |日付/時刻を指定された単位に丸めます。 例: NOW/DAY は、現在の日付/時刻を、当日の午前&0; 時に丸めます。 |
| + または - |日付/時刻を、指定された単位数でオフセットします。 例: NOW+1HOUR は、現在の日付/時刻を 1 時間後にオフセットします。2013-10-01T12:00-10DAYS は、日付の値を 10 日前にオフセットします。 |

日付/時刻算術演算子は連結できます。次に例を示します。

```
NOW+1HOUR-10MONTHS/MINUTE
```

次の表は、サポートされる日付/時刻の単位を一覧表示しています。

| 日付と時刻の単位 | 説明 |
| --- | --- |
| YEAR、YEARS |現在の年に丸めるか、指定された年数分オフセットします。 |
| MONTH、MONTHS |現在の月に丸めるか、指定された月数分オフセットします。 |
| DAY、DAYS、DATE |現在の日に丸めるか、指定された日数分オフセットします。 |
| HOUR、HOURS |現在の時に丸めるか、指定された時数分オフセットします。 |
| MINUTE、MINUTES |現在の分に丸めるか、指定された分数分オフセットします。 |
| SECOND、SECONDS |現在の秒に丸めるか、指定された秒数分オフセットします。 |
| MILLISECOND、MILLISECONDS、MILLI、MILLIS |現在のミリ秒に丸めるか、指定されたミリ秒数分オフセットします。 |

### <a name="field-facets"></a>フィールドのファセット
フィールドのファセットを使用することで、インデックスのさまざまな用語に対する "フリー テキスト" のクエリを記述するのではなく、特定のフィールドに対する検索条件と正確な値を指定できます。 この構文は、これまでの段落のさまざまな例の中で既に使用しています。 ここでは、さらに複雑な例を示します。

**構文**

```
field:value
```

```
field=value
```

**説明**

フィールドで特定の値を検索します。 値は、文字列リテラル、数値、または日付/時刻を指定できます。

例:

```
TimeGenerated:NOW
```

```
ObjectDisplayName:"server01.contoso.com"
```

```
SampleValue:0.3
```

**構文**

*field>value*

*field<value*

*field>=value*

*field<=value*

*field!=value*

**説明**

比較を指定します。

例:

```
TimeGenerated>NOW+2HOURS
```

**構文**

```
field:[from..to]
```

**説明**

範囲ファセットを指定します。

例:

```
TimeGenerated:[NOW..NOW+1DAY]
```

```
SampleValue:[0..2]
```

### <a name="regular-expressions"></a>正規表現
Regex キーワードを使用することで、正規表現でフィールドの検索条件を指定できます。  正規表現で使用できる構文の詳細な説明については、「[正規表現を使用した Log Analytics のログ検索のフィルター処理](log-analytics-log-searches-regex.md)」をご覧ください。

**構文**

```
field:Regex("Regular Expression")
```

```
field=Regex("Regular Expression")
```

**例**

```
Computer=Regex("C.*")
```

```
Computer=Regex("^C.*")
```

### <a name="logical-operators"></a>論理演算子
クエリ言語では、論理演算子 (*AND*、*OR*、*NOT*) と、C スタイルのエイリアス (*&&*、*||*、*!*) をサポートしています。 かっこを使用して、これらの演算子をグループ化できます。

次に例を示します。

```
system OR error

```

```
Type:Alert AND NOT(Severity:1 OR ObjectId:"8066bbc0-9ec8-ca83-1edc-6f30d4779bcb8066bbc0-9ec8-ca83-1edc-6f30d4779bcb")
```

最上位レベルのフィルター引数の論理演算子は省略できます。 この場合は、AND 演算子とみなされます。

| フィルター式 | 相当する構文 |
| --- | --- |
| system error |system AND error |
| system "Windows Server" OR Severity:1 |system AND ("Windows Server" OR Severity:1) |

### <a name="wildcarding"></a>ワイルドカード
クエリ言語では、(\*) 文字の使用をサポートしており、クエリ内の値として&1; つ以上の文字を表すことができます。

次に例を示します。

 "Redmond-SQL" など、名前に "SQL" が含まれるすべてのコンピューターを検索します。

```
Type=Event Computer=*SQL*
```

> [!NOTE]
> 現在、ワイルドカードを引用符で囲んで使用することはできません。 Message=`"*This text*"` では、(\*) をリテラル文字の (\*) として使用していると見なされます。
>
> ## <a name="commands"></a>コマンド
>

コマンドは、クエリによって返された結果に適用されます。 取得された結果にコマンドを適用するには、パイプ文字 (|) を使用します。 複数のコマンドは、パイプ文字で区切る必要があります。

> [!NOTE]
> コマンド名は、フィールド名とデータとは異なり、大文字でも小文字でも記述できます。
>
>

### <a name="sort"></a>並べ替え
構文:

    sort field1 asc|desc, field2 asc|desc, …

結果を特定のフィールドを基準に並べ替えます。 asc/desc プレフィックスは省略可能です。 これらを省略した場合、並べ替え順序は *asc* であると見なされます。 クエリで *Sort* コマンドが明示的に使用されない場合、既定の動作である Sort **TimeGenerated** desc によって常に最新の結果が最初に戻ります。

### <a name="toplimit"></a>Top/Limit
構文:

    top number


    limit number
応答を上位 N 個の結果に制限します。

例:

    Type:Alert errors detected | top 10

上位 10 個の一致する結果を返します。

### <a name="skip"></a>Skip
構文:

    skip number

表示される結果の数をスキップします。

例:

    Type:Alert errors detected | top 10 | skip 200

結果 200 から始まる上位 10 個の一致する結果を返します。

### <a name="select"></a>Select
構文:

    select field1, field2, ...

結果を選択したフィールドに制限します。

例:

    Type:Alert errors detected | select Name, Severity

返される結果のフィールドを *名前* と *Severity*」セクションを参照してください。

### <a name="measure"></a>Measure
*Measure* コマンドは、生の検索結果に統計関数を適用するために使用されます。 これは、データの *グループ別* の表示を取得するときに非常に役立ちます。 *Measure* コマンドを使用すると、Log Analytics 検索では、集計結果が表で示されます。

構文:

    measure aggregateFunction1([aggregatedField]) [as fieldAlias1] [, aggregateFunction2([aggregatedField2]) [as fieldAlias2] [, ...]] by groupField1 [, groupField2 [, groupField3]]  [interval interval]


    measure aggregateFunction1([aggregatedField]) [as fieldAlias1] [, aggregateFunction2([aggregatedField2]) [as fieldAlias2] [, ...]]  interval interval



結果を *groupField* 別に集計し、集計されたメジャー値を *aggregatedField* を使用して計算します。

| Measure 統計関数 | 説明 |
| --- | --- |
| *aggregateFunction* |集計関数の名前 (大文字と小文字は区別されません)。 次の集計関数がサポートされています: COUNT、MAX、MIN、SUM、AVG、STDDEV、COUNTDISTINCT、PERCENTILE##、または PCT## (## は 1 ～ 99 の任意の数) |
| *aggregatedField* |集計されるフィールド。 このフィールドは COUNT 集計関数では省略可能ですが、SUM、MAX、MIN、AVG、STDDEV、PERCENTILE##、または PCT## (## は 1 ～ 99 の任意の数) では既存の数値フィールドにする必要があります。 aggregatedField には、Extend がサポートされている関数のいずれかを指定することもできます。 |
| *fieldAlias* |計算される集計値のエイリアス (省略可能)。 指定されていない場合、フィールド名は AggregatedValue になります。 |
| *groupField* |結果セットのグループ化に使用するフィールドの名前。 |
| *間隔* |**nnnNAME** 形式の時間間隔。nnn は正の整数です。 **NAME** は間隔名です。 サポートされている間隔名 (大文字と小文字の区別あり): MILLISECOND[S]、SECOND[S]、MINUTE[S]、HOUR[S]、DAY[S]、MONTH[S]、YEAR[S] |

期間オプションは、日付/時刻グループ フィールド ( *TimeGenerated* と *TimeCreated*など) でのみ使用できます。 現時点では、これはサービスでは強制されていませんが、日付/時刻のないフィールドがバックエンドに渡されるとランタイム エラーが発生します。 スキーマの検証が実装されるときに、サービス API は、日付/時刻のないフィールドを使用して期間集計を行うクエリを拒否します。 現時点での *Measure* の実装では、任意の集計関数の期間によるグループ化のみをサポートしています。

BY 句は省略されたが (2 番目の構文として) 期間が指定された場合は、既定で *TimeGenerated* フィールドとみなされます。

次に例を示します。

**例 1**

    Type:Alert | measure count() as Count by ObjectId

*説明*

アラートを *ObjectID* 別にグループ化し、各グループのアラート数を計算します。 集計値が *Count* フィールド (別名) として返されます。

**例 2**

    Type:Alert | measure count() interval 1HOUR

*説明*

アラートを *TimeGenerated* フィールドを使用して 1 時間の期間でグループ化し、各期間のアラート数を返します。

**例 3**

    Type:Alert | measure count() as AlertsPerHour interval 1HOUR

*説明*

前の例と同じですが、 集計フィールドの別名 (*AlertsPerHour*) が使用されています。

**例 4**

    * | measure count() by TimeCreated interval 5DAYS

*説明*

結果を *TimeGenerated* フィールドを使用して 5 日間の期間でグループ化し、各期間の結果数を返します。

**例 5**

    Type:Alert | measure max(Severity) by WorkflowName

*説明*

アラートをワークロードの名前でグループ化し、各ワークフローでのアラートの重大度の最大値を返します。

**例 6**

    Type:Alert | measure min(Severity) by WorkflowName

*説明*

前の例と同じですが、 *Min* 集計関数が使用されています。

**例 7**

    Type:Perf | measure avg(CounterValue) by Computer

*説明*

パフォーマンスをコンピューター別にグループ化し、平均 (avg) を計算します。

**例 8**

    Type:Perf | measure sum(CounterValue) by Computer

*説明*

前の例と同じですが、 *Sum*が使用されています。

**例 9**

    Type:Perf | measure stddev(CounterValue) by Computer

*説明*

前の例と同じですが、 *stddev*が使用されています。

**例 10**

    Type:Perf | measure percentile70(CounterValue) by Computer

*説明*

前の例と同じですが、 *PERCENTILE70*が使用されています。

**例 11**

    Type:Perf | measure pct70(CounterValue) by Computer

*説明*

前の例と同じですが、 *PCT70*が使用されています。 *PCT##* は *PERCENTILE##* 関数のエイリアスです。

**例 12**

    Type:Perf | measure avg(CounterValue) by Computer, CounterName

*説明*

パフォーマンスを、まずコンピューター別に、次に CounterName 別にグループ化し、平均 (avg) を計算します。

**例 13**

    Type:Alert | measure count() as Count by WorkflowName | sort Count desc | top 5

*説明*

アラートの数が最大である上位&5; つのワークフローを取得します。

**例 14**

    * | measure countdistinct(Computer) by Type

*説明*

一意のコンピューターの数をカウントし、種類ごとにレポートします。

**例 15**

    * | measure countdistinct(Computer) Interval 1HOUR

*説明*

一意のコンピューターの数をカウントし、1 時間ごとにレポートします。

**例 16**

```
Type:Perf CounterName=”% Processor Time” InstanceName=”_Total” | measure avg(CounterValue) by Computer Interval 1HOUR
```

*説明*

% プロセッサ時間をコンピューター別にグループ化し、1 時間ごとの平均値を返します。

**例 17**

    Type:W3CIISLog | measure max(TimeTaken) by csMethod Interval 5MINUTES

*説明*

W3CIISLog をメソッド別にグループ化し、5 分ごとの最大値を返します。

**例 18**

```
Type:Perf CounterName=”% Processor Time” InstanceName=”_Total”  | measure min(CounterValue) as MIN, avg(CounterValue) as AVG, percentile75(CounterValue) as PCT75, max(CounterValue) as MAX by Computer Interval 1HOUR
```

*説明*

% プロセッサ時間をコンピューター別にグループ化し、1 時間ごとの最小値、平均値、75 パーセンタイル値、最大値を返します。

**例 19**

```
Type:Perf CounterName=”% Processor Time”  | measure min(CounterValue) as MIN, avg(CounterValue) as AVG, percentile75(CounterValue) as PCT75, max(CounterValue) as MAX by Computer, InstanceName Interval 1HOUR
```

*説明*

% プロセッサ時間を、まずコンピューター別に、次にインスタンス名別にグループ化し、1 時間ごとの最小値、平均値、75 パーセンタイル値、最大値を返します。

**例 20**

```
Type= Perf CounterName="Disk Writes/sec" Computer="BaconDC01.BaconLand.com" | measure max(product(CounterValue,60)) as MaxDWPerMin by InstanceName Interval 1HOUR
```

*説明*

コンピューターのすべてのディスクの&1; 分あたりの最大ディスク書き込み回数を計算します。

### <a name="where"></a>Where
構文:

```
**where** AggregatedValue>20
```

*Measure* 集計関数によって生成された集計結果をさらにフィルター処理するために *Measure* コマンドの後でのみ使用できます。

次に例を示します。

    Type:Perf CounterName:"% Total Run Time" | Measure max(CounterValue) as MAXCPU by Computer

    Type:Perf CounterName:"% Total Run Time" | Measure max(CounterValue) by Computer | where (AggregatedValue>50 and AggregatedValue<90)

### <a name="in"></a>IN
**IN** キーワードを使用すると、値のリストから選択することができます。  使用する構文によって、これは自分で指定する単純なリストにすることも、集計を基にした値のリストにすることもできます。

構文 1:

```
field IN {value1,value2,value3,...}
```

説明: この構文では、単純なリストにすべての値を含めることができます。



次に例を示します。

```
EventID IN {1201,1204,1210}
```

```
Computer IN {"srv01.contoso.com","srv02.contoso.com"}
```

構文 2:

```
(Outer Query) (Field to use with inner query results) IN {Inner query | measure count() by (Field to send to outer query)} (rest  of outer query)  
```

説明: この構文を使用すると、集計を作成し、その集計から別の外部 (プライマリ) 検索に値のリストをフィードすることで、それらの値を持つイベントを検索することができます。 これを実行するには、内部検索を中かっこで囲み、その結果を IN 演算子を使用して外部検索内のフィールドに指定できる値としてフィードします。

内部クエリの例: 次の集計クエリを使用して *現時点でセキュリティ更新プログラムが適用されていないコンピューター* を検索します。

```
Type:Update Classification="Security Updates"  UpdateState=needed TimeGenerated>NOW-25HOURS | measure count() by Computer
```    

*現時点でセキュリティ更新プログラムが適用されていないコンピューター向けのすべての Windows イベント* を検索する場合、最終的なクエリは次のようになります。

```
Type=Event Computer IN {Type:Update Classification="Security Updates"  UpdateState=needed TimeGenerated>NOW-25HOURS | measure count() by Computer}
```

### <a name="dedup"></a>Dedup
**構文**

    Dedup FieldName

**説明** 指定されたフィールドの一意の値すべてについて最初に見つかったドキュメントを返します。

**例**

    Type=Event | Dedup EventID | sort TimeGenerated DESC

上の例では、EventID ごとに&1; つのイベント (TimeGenerated で DESC を使用しているため最新のイベント) を返します。

### <a name="extend"></a>Extend
**説明** クエリ内に実行時フィールドを作成できます。 集計を実行する場合は、Extend の後に measure コマンドを使用することもできます。

**例 1**

    Type=SQLAssessmentRecommendation | Extend product(RecommendationScore, RecommendationWeight) AS RecommendationWeightedScore
SQL の評価に関する推奨事項の重み付けされた推奨スコアを表示します。

**例 2**

    Type=Perf CounterName="Private Bytes" | EXTEND div(CounterValue,1024) AS KBs | Select CounterValue,Computer,KBs
カウンター値をバイト単位ではなく KB 単位で表示します。

**例 3**

    Type=WireData | EXTEND scale(TotalBytes,0,100) AS ScaledTotalBytes | Select ScaledTotalBytes,TotalBytes | SORT TotalBytes DESC
すべての結果が 0 ～ 100 の範囲内に収まるように WireData TotalBytes の値をスケーリングします。

**例 4**

```
Type=Perf CounterName="% Processor Time" | EXTEND if(map(CounterValue,0,50,0,1),"HIGH","LOW") as UTILIZATION
```
Perf Counter 値に対して 50% 未満を LOW、それ以外を HIGH としてタグ付けします。

**例 5**

```
Type= Perf CounterName="Disk Writes/sec" Computer="BaconDC01.BaconLand.com" | Extend product(CounterValue,60) as DWPerMin| measure max(DWPerMin) by InstanceName Interval 1HOUR
```
コンピューターのすべてのディスクの&1; 分あたりの最大ディスク書き込み回数を計算します。

**サポートされている関数**

| 関数 | 説明 | 構文の例 |
| --- | --- | --- |
| abs |指定された値または関数の絶対値を返します。 |`abs(x)` <br> `abs(-5)` |
| acos |値または関数のアーク コサインを返します。 |`acos(x)` |
| と |すべてのオペランドが true と評価された場合にのみ値 true を返します。 |`and(not(exists(popularity)),exists(price))` |
| asin |値または関数のアーク サインを返します。 |`asin(x)` |
| atan |値または関数のアーク タンジェントを返します。 |`atan(x)` |
| atan2 |直交座標 (x、y) の極座標への変換から生じる角度を返します。 |`atan2(x,y)` |
| cbrt |立方根 |`cbrt(x)` |
| ceil |整数に切り上げます。 |`ceil(x)`  <br> `ceil(5.6)` - 6 を返します。 |
| cos |角度のコサインを返します。 |`cos(x)` |
| cosh |角度の双曲線コサインを返します。 |`cosh(x)` |
| def |def は default (既定) の略です。 "field" フィールドの値を返すか、このフィールドが存在しない場合は指定されている既定値を返し、 `exists()==true`になる最初の値を導出します。 |`def(rating,5)` - この def() 関数は評価を返します。ドキュメントで評価が指定されていない場合は 5 を返します。 <br> `def(myfield, 1.0)` は `if(exists(myfield),myfield,1.0)` と同値です。 |
| deg |ラジアンを角度に変換します。 |`deg(x)` |
| div |`div(x,y)` は、x を y で割ります。 |`div(1,y)` <br> `div(sum(x,100),max(y,1))` |
| dist |2 つのベクトル (ポイント) 間の距離を n 次元空間で返します。 指数と&2; つ以上の ValueSource インスタンスを受け取り、2 つのベクトル間の距離を計算します。 各 ValueSource は数値にする必要があります。 同じ数の ValueSource インスタンスを渡す必要があり、メソッドでは前半が最初のベクトル、後半が&2; 番目のベクトルを表すと見なされます。 |`dist(2, x, y, 0, 0)` - ドキュメントごとに (0,0) と (x,y) の間のユークリッド距離を計算します。 <br> `dist(1, x, y, 0, 0)` - ドキュメントごとに (0,0) と (x,y) の間のマンハッタン (タクシー) 距離を計算します。 <br> `dist(2,,x,y,z,0,0,0)` - ドキュメントごとに (0,0,0) と (x,y,z) の間のユークリッド距離を計算します。<br>`dist(1,x,y,z,e,f,g)` - (x,y,z) と (e,f,g) の間のマンハッタン距離であり、それぞれの文字はフィールド名です。 |
| exists |フィールドが&1; つでも存在する場合は TRUE を返します。 |`exists(author)` - いずれかのドキュメントに "author" フィールド値がある場合、TRUE を返します。<br>`exists(query(price:5.00))` - "price" が "5.00" と一致する場合、TRUE を返します。 |
| exp |x 乗されたオイラー数を返します。 |`exp(x)` |
| floor |整数に切り下げます。 |`floor(x)`  <br> `floor(5.6)` - 5 を返します。 |
| hypo |中間のオーバーフローやアンダーフローなしに sqrt(sum(pow(x,2),pow(y,2))) を返します。 |`hypo(x,y)`  <br> ` |
| if |条件付きの関数クエリを使用できます。 `if(test,value1,value2)` において、test は論理値または論理値 (TRUE か FALSE) を返す式を参照している or 関数です。  `value1` は、test から TRUE が導出された場合に関数によって返される値です。 `value2` は、test から FALSE が導出された場合に関数によって返される値です。 式は、ブール値を出力する任意の関数にも、数値または文字列を返す関数にもすることができます。数値を返す関数の場合、値 0 は false と解釈され、文字列を返す関数の場合、空の文字列は false と解釈されます。 |`if(termfreq(cat,'electronics'),popularity,42)` - この関数は、各ドキュメントをチェックして、cat フィールド内に "electronics" という語句が含まれているかどうかを確認します。 含まれている場合は popularity フィールドの値が返され、それ以外の場合は値 42 が返されます。 |
| linear |`m*x+c` を実装します。m と c は定数で、x は任意関数です。 これは `sum(product(m,x),c)` と等価ですが、単一の関数として実装されるため、少し効率的です。 |`linear(x,m,c) linear(x,2,4)` は `2*x+4` を返します。 |
| ln |指定された関数の自然対数を返します。 |`ln(x)` |
| log |指定された関数の 10 を底とする対数を返します。 |`log(x)   log(sum(x,100))` |
| map |min ～ max の範囲内にある入力関数 x の値を、指定された target にマップします。 引数の min と max は定数にする必要があります。 引数の target と default は定数または関数にすることができます。 x の値が min ～ max の範囲内に含まれない場合は、x の値が返されるか、既定値が 5 番目の引数として指定されていれば、その値が返されます。 |`map(x,min,max,target) map(x,0,0,1)` - 0 ～ 1 の任意の値を変更します。 これは既定値 0 の値の処理に役立ちます。<br> `map(x,min,max,target,default)    map(x,0,100,1,-1)` - 0 ～ 100 の任意の値を 1 に、およびその他のすべての値を -1 に変更します。<br>  `map(x,0,100,sum(x,599),docfreq(text,solr))` - 0 ～ 100 の任意の値を x+599 に、およびその他のすべての値をフィールド テキストの用語 'solr' の頻度に変更します。 |
| max |引数として `max(x,y,...)`のように指定された、複数の入れ子になった関数または定数の最大数値を返します。 max 関数は、別の関数またはフィールドを指定された定数に "ボトム アウト" する場合にも便利です。  単一の複数値フィールドの最大値を選択するために `field(myfield,max)` 構文を使用します。 |`max(myfield,myotherfield,0)` |
| Min |引数として `min(x,y,...)`のように指定された、複数の入れ子になった関数または定数の最小数値を返します。 min 関数は、定数を使用して関数の "上限" を設定する場合にも便利です。 単一の複数値フィールドの最小値を選択するために `field(myfield,min)` 構文を使用します。 |`min(myfield,myotherfield,0)` |
| mod |関数 x を関数 y で割った場合の剰余を計算します。 |`mod(1,x)` <br> `mod(sum(x,100), max(y,1))` |
| ms |引数間の差異をミリ秒単位で返します。 日付は Unix または POSIX タイム エポックである 1970 年 1 月 1 日午前 0 時 (UTC) からの相対値です。 引数には、インデックス付きの TrieDateField、日付の定数に基づく日付の計算、または NOW を指定できます。 `ms()` は、エポックからのミリ秒数である `ms(NOW)` と同値です。 `ms(a)` は、引数によって表されるエポックからのミリ秒数を返します。 `ms(a,b)` は、a の前に b が発生したミリ秒数である `a - b` を返します。 |`ms(NOW/DAY)`<br>`ms(2000-01-01T00:00:00Z)`<br>`ms(mydatefield)`<br>`ms(NOW,mydatefield)`<br>`ms(mydatefield,2000-01-01T00:00:00Z)`<br>`ms(datefield1,datefield2)` |
| not |ラップされた関数の論理的に否定された値。 |`not(exists(author))` - `exists(author)` が false の場合にのみ TRUE。 |
| または |論理和。 |`or(value1,value2)` - value1 または value2 が true の場合に TRUE。 |
| pow |指定された基数を指定された数値でべき乗します。 `pow(x,y)` は、x を y 乗します。 |`pow(x,y)`<br>`pow(x,log(y))`<br>`pow(x,0.5)` - sqrt と同じです。 |
| product |コンマ区切りのリストで指定された複数の値または関数の積を返します。 `mul(...)` は、この関数のエイリアスとしても使用できます。 |`product(x,y,...)`<br>`product(x,2)`<br>`product(x,y)`<br>`mul(x,y)` |
| recip |`recip(x,m,a,b)` による逆関数を実行し、`a/(m*x+b)` を実装します。m、a、b は定数で、x は任意の複素関数です。 a と b が等しく、x が 0 以上の場合、この関数の最大値は 1 となり、x が増加するとその値は減少します。 a と b の値を同時に増やすと、関数全体が曲線上のより平坦な部分に移動することになります。 このような特性により、x を "`rord(datefield)`" とした場合、この関数はより新しいドキュメントを検索するという点で最適な関数になります。 |`recip(myfield,m,a,b)`<br>`recip(rord(creationDate),1,1000,1000)` |
| rad |角度をラジアンに変換します。 |`rad(x)` |
| rint |最も近い整数値に丸めます。 |`rint(x)`  <br> `rint(5.6)` - 6 を返します。 |
| sin |角度のサインを返します。 |`sin(x)` |
| sinh |角度の双曲サインを返します。 |`sinh(x)` |
| scale |関数 x の値を、指定された minTarget ～ maxTarget の範囲内に収まるようにスケーリングします。 現在の実装では、正しいスケールを選択できるように、関数のすべての値を走査して最小値と最大値を取得しています。 現在の実装では、ドキュメントが削除されている場合、または値を持たない場合、それらを判別することができません。 このような場合には、値 0.0 が使用されます。 つまり、すべての値が通常どおりに 0.0 を越えている場合でも、最終的に 0.0 がマップ元の最小値になる可能性があります。 このような場合、適切な `map()` 関数を `scale(map(x,0,0,5),1,2)` のように対処法として使用し、0.0 を実際の範囲内の値に変更することができます。 |`scale(x,minTarget,maxTarget)`<br>`scale(x,1,2)` - すべての値が 1 以上 2 以下になるように x の値をスケーリングします。 |
| sqrt |指定された値または関数の平方根を返します。 |`sqrt(x)`<br>`sqrt(100)`<br>`sqrt(sum(x,100))` |
| strdist |2 つの文字列間の距離を計算します。 Lucene スペル チェッカーの StringDistance インターフェイスを使用し、そのパッケージに含まれるすべての実装をサポートしているほか、Solr のリソース読み込み機能を通じてアプリケーション独自の実装をプラグインとして使用できます。 strdist には `(string1, string2, distance measure)` (距離指標) を指定できます。 距離指標として指定できる値は、 <br>jw: Jaro-Winkler<br>edit: Levenstein または Edit distance<br>ngram: The NGramDistance です。NGramDistance を指定した場合、オプションとして ngram サイズも渡すことができます。 既定値は 2 です。<br>FQN: StringDistance インターフェイスの実装用の完全修飾クラス名。 引数なしのコンストラクターが必要です。 |`strdist("SOLR",id,edit)` |
| sub |`sub(x,y)`から x-y を返します。 |`sub(myfield,myfield2)`<br>`sub(100,sqrt(myfield))` |
| Sum |コンマ区切りのリストで指定された複数の値または関数の合計を返します。 `add(...)` をこの関数のエイリアスとして使用できます。 |`sum(x,y,...)`<br>`sum(x,1)`<br>`sum(x,y)`<br>`sum(sqrt(x),log(y),z,0.5)`<br>`add(x,y)` |
| termfreq |用語がドキュメントのフィールドに表示される回数を返します。 |termfreq(text,'memory') |
| tan |角度のタンジェントを返します。 |`tan(x)` |
| tanh |角度の双曲タンジェントを返します。 |`tanh(x)` |

## <a name="search-field-and-facet-reference"></a>検索フィールドとファセットのリファレンス
ログ検索を使用してデータを検索すると、結果にさまざまなフィールドとファセットが表示されます。 ただし、表示される一部の情報がわかりにくい場合があります。 次の情報は、結果を理解するために役立ちます。

| フィールド | 検索の種類 | 説明 |
| --- | --- | --- |
| TenantId |すべて |データをパーティション分割するために使用されます |
| TimeGenerated |すべて |(検索と他の画面で) タイムライン、タイムセレクターを動作させるために使用されます。 これは (通常はエージェント上で) データが生成された時間を表します。 時間は ISO 形式で表され、常に UTC です。 既存のインストルメンテーションに基づく "種類" (つまりログ内のイベント) の場合、通常これはログ エントリ/行/レコードが記録された実際の時間です。管理パック経由またはクラウド内で生成された一部の他の種類 (推奨事項/アラート/更新エージェントなど) の場合は、何らかの構成のスナップショットを使用して新しいデータが収集された時間、またはそれに基づいて推奨事項/アラートが生成された時間です。 |
| EventID |イベント |Windows イベント ログ内のイベント ID |
| EventLog |イベント |Windows によってイベントが記録されたイベント ログ |
| EventLevelName |イベント |重大/警告/情報/成功 |
| EventLevel |イベント |重大/警告/情報/成功を表す数値 (より簡単で読みやすいクエリにするには EventLevelName を代りに使用します) |
| SourceSystem |すべて |データ ソース (サービスへの "接続" モードの観点から)。サービスとは Operations Manager、OMS (データはクラウド内に生成されます)、Azure Storage (データ ソースは WAD) などです。 |
| ObjectName |PerfHourly |Windows パフォーマンス オブジェクト名 |
| InstanceName |PerfHourly |Windows パフォーマンス カウンターのインスタンス名 |
| CounteName |PerfHourly |Windows パフォーマンス カウンターの名前 |
| ObjectDisplayName |PerfHourly、ConfigurationAlert、ConfigurationObject、ConfigurationObjectProperty |Operations Manager でパフォーマンス収集ルールの対象となったオブジェクトの表示名、または Operational Insights によって検出またはアラートが生成されたオブジェクトの表示名。 |
| RootObjectName |PerfHourly、ConfigurationAlert、ConfigurationObject、ConfigurationObjectProperty |Operations Manager でパフォーマンス収集ルールの対象となったオブジェクトの親の親の表示名 (二重ホスティング関係。つまり、Windows コンピューターにホストされる SqlInstance によってホストされる SqlDatabase)、または Operational Insights によって検出またはアラートが生成されたオブジェクトの表示名。 |
| コンピューター |ほとんどの種類 |データが属しているコンピューターの名前 |
| DeviceName |ProtectionStatus |データが属しているコンピューターの名前 ('Computer' と同じ) |
| DetectionId |ProtectionStatus | |
| ThreatStatusRank |ProtectionStatus |脅威の状態のランクは脅威の状態の数値表現であり、HTTP 応答コードに似ています。数値の飛びは新しい状態を追加できる余地を残すためです (脅威なしを示す数値が 100 でも 0 でもなく 150 なのはこれが理由です)。 驚異の状態と保護の状態のロールアップでは、選択された期間内のコンピューターの状態で、最悪のものが表示されます。 数値を使用して異なる状態にランクを付けて、数値が最も高いレコードを検索できるようになっています。 |
| ThreatStatus |ProtectionStatus |ThreatStatus の説明。ThreatStatusRank と 1 対 1 で対応しています。 |
| TypeofProtection |ProtectionStatus |コンピューターで検出されたマルウェア対策製品。なし、Microsoft Malware Removal Tool、Forefront など |
| ScanDate |ProtectionStatus | |
| SourceHealthServiceId |ProtectionStatus、RequiredUpdate |このコンピューターのエージェント用の HealthService ID |
| HealthServiceId |ほとんどの種類 |このコンピューターのエージェント用の HealthService ID |
| ManagementGroupName |ほとんどの種類 |Operations Manager に接続されたエージェントの管理グループの名前。それ以外の場合は null/空白 |
| ObjectType |ConfigurationObject |Log Analytics の構成評価で検出されたこのオブジェクトの種類 (Operations Manager 管理パックの "型" /クラスに類似) |
| UpdateTitle |RequiredUpdate |インストールされていないことが検出された更新プログラムの名前 |
| PublishDate |RequiredUpdate |更新プログラムが Microsoft Update に公開された日時 |
| サーバー |RequiredUpdate |データが属しているコンピューターの名前 ('Computer' と同じ) |
| product |RequiredUpdate |更新プログラムが適用される製品 |
| UpdateClassification |RequiredUpdate |更新の種類 (更新プログラムのロールアップ、Service Pack など) |
| KBID |RequiredUpdate |このベスト プラクティスまたは更新プログラムについて説明しているサポート技術情報の ID |
| WorkflowName |ConfigurationAlert |アラートを生成したルールまたはモニターの名前 |
| Severity |ConfigurationAlert |アラートの重大度 |
| 優先順位 |ConfigurationAlert |アラートの優先度 |
| IsMonitorAlert |ConfigurationAlert |このアラートがモニターとルールのどちらによって生成されたか (モニターの場合は true、ルールの場合は fase) |
| AlertParameters |ConfigurationAlert |Log Analytics アラートのパラメーターを含む XML |
| Context |ConfigurationAlert |Log Analytics アラートの "コンテキスト" を含む XML |
| ワークロード |ConfigurationAlert |アラートが参照するテクノロジまたは 'ワークロード' |
| AdvisorWorkload |推奨 |推奨事項が参照するテクノロジまたは 'ワークロード' |
| 説明 |ConfigurationAlert |アラートの説明 (簡略版) |
| DaysSinceLastUpdate |UpdateAgent |(このレコードの 'TimeGenerated' の日付から) 何日前にこのエージェントが WSUS/Microsoft Update から更新プログラムをインストールしたかを示す日数 |
| DaysSinceLastUpdateBucket |UpdateAgent |WSUS/Microsoft Update の更新プログラムが何日前にコンピューターにインストールされたかを示す、DaysSinceLastUpdate に基づく 'タイム バケット' 分類 |
| AutomaticUpdateEnabled |UpdateAgent |このエージェントでの更新プログラムの自動チェックの設定 (有効/無効) |
| AutomaticUpdateValue |UpdateAgent |更新プログラムの自動チェックの設定内容 (自動的にダウンロードしてインストール、ダウンロードのみ、またはチェックのみ) |
| WindowsUpdateAgentVersion |UpdateAgent |Microsoft Update エージェントのバージョン番号 |
| WSUSServer |UpdateAgent |この更新エージェントのターゲット WSUS サーバー |
| OSVersion |UpdateAgent |この更新エージェントが実行されているオペレーティング システムのバージョン |
| 名前 |Recommendation、ConfigurationObjectProperty |推奨事項の名前/タイトル、または Log Analytics の構成評価でのプロパティの名前 |
| 値 |ConfigurationObjectProperty |Log Analytics の構成評価でのプロパティの値 |
| KBLink |推奨 |このベスト プラクティスまたは更新プログラムについて説明しているサポート技術情報の URL |
| RecommendationStatus |推奨 |推奨事項は、単に検索インデックスに追加されるのではなく、そのレコードが '更新される' 数少ない種類のデータです。 この状態は、推奨事項の状態 (アクティブ/オープン) や、それが解決されているかどうかの Log Analytics による検出の有無によって変化します。 |
| RenderedDescription |イベント |Windows イベントの表示の説明 (設定されたパラメーターを含む再使用テキスト) |
| ParameterXml |イベント |Windows イベントの 'データ' セクションのパラメーターを使用する XML (イベント ビューアーに表示) |
| EventData |イベント |Windows イベントの 'データ' セクション全体のパラメーターを使用する XML (イベント ビューアーに表示) |
| から |イベント |イベントを生成したイベント ログのソース |
| EventCategory |イベント |Windows イベント ログから直接取得されるイベントのカテゴリ |
| UserName |イベント |Windows イベントのユーザー名 (通常は NT AUTHORITY\LOCALSYSTEM) |
| SampleValue |PerfHourly |パフォーマンス カウンターの&1; 時間ごとの集計の平均値 |
| Min |PerfHourly |パフォーマンス カウンターの&1; 時間ごとの集計の最小値 |
| max |PerfHourly |パフォーマンス カウンターの&1; 時間ごとの集計の最大値 |
| Percentile95 |PerfHourly |パフォーマンス カウンターの 1 時間ごとの集計の 95 パーセンタイル値 |
| SampleCount |PerfHourly |この&1; 時間ごとの集計レコードを生成するために使用された "生" のパフォーマンス カウンターのサンプル数 |
| Threat |ProtectionStatus |検出されたマルウェアの名前 |
| StorageAccount |W3CIISLog |ログの読み取りが行われた Azure ストレージ アカウント |
| AzureDeploymentID |W3CIISLog |ログが属しているクラウド サービスの Azure デプロイ ID |
| ロール |W3CIISLog |ログが属している Azure クラウド サービスのロール |
| RoleInstance |W3CIISLog |ログが属している Azure ロールの RoleInstance |
| sSiteName |W3CIISLog |ログが属している IIS Web サイト (メタベース表記); 元のログの s-sitename フィールド |
| sComputerName |W3CIISLog |元のログの s computername フィールド |
| sIP |W3CIISLog |HTTP 要求が送信されたサーバーの IP アドレス。 元のログの s-ip フィールド |
| csMethod |W3CIISLog |HTTP 要求でクライアントによって使用される HTTP メソッド (GET/POST/その他)。 元のログの cs-method |
| cIP |W3CIISLog |HTTP 要求の送信元クライアントの IP アドレス。 元のログの c-ip フィールド |
| csUserAgent |W3CIISLog |クライアントによって宣言された HTTP ユーザー エージェント (ブラウザーまたはそれ以外)。 元のログの cs-user-agent |
| scStatus |W3CIISLog |サーバーによってクライアントに返された HTTP 状態コード (200/403/500/その他)。 元のログの cs-status |
| TimeTaken |W3CIISLog |要求が完了するまでにかかった時間 (ミリ秒単位)。 元のログの timetaken フィールド |
| csUriStem |W3CIISLog |要求された相対 URI (ホスト アドレスなし。つまり '/search') 。 元のログの cs-uristem フィールド |
| csUriQuery |W3CIISLog |URI クエリ。 URI クエリは ASP ページなどの動的なページに対してのみ必要であるため、このフィールドには通常は静的なページ用のハイフンが含まれます。 |
| sPort |W3CIISLog |HTTP 要求が送信された (およびIIS がピックアップしてリッスンする) サーバーのポート |
| csUserName |W3CIISLog |認証されたユーザーの名前 (要求が認証され、匿名でない場合) |
| csVersion |W3CIISLog |要求で使用された HTTP プロトコルのバージョン (つまり 'HTTP/1.1') |
| csCookie |W3CIISLog |Cookie 情報 |
| csReferer |W3CIISLog |ユーザーが最後にアクセスしたサイト。 現在のサイトへのリンクを提供したサイト |
| csHost |W3CIISLog |要求されたホスト ヘッダー (つまり 'www.mysite.com') |
| scSubStatus |W3CIISLog |Substatus エラー コード |
| scWin32Status |W3CIISLog |Windows 状態コード |
| csBytes |W3CIISLog |クライアントからサーバーへの要求で送信されたバイト数 |
| scBytes |W3CIISLog |サーバーからクライアントへの応答で返されたバイト数 |
| ConfigChangeType |ConfigurationChange |変更の種類 (WindowsServices/ソフトウェア/その他) |
| ChangeCategory |ConfigurationChange |変更のカテゴリ (変更/追加/削除) |
| SoftwareType |ConfigurationChange |ソフトウェアの種類 (更新プログラム/アプリケーション) |
| SoftwareName |ConfigurationChange |ソフトウェアの名前 (ソフトウェアの変更にのみ適用) |
| 発行元 |ConfigurationChange |ソフトウェアを公開しているベンダー (ソフトウェアの変更にのみ適用) |
| SvcChangeType |ConfigurationChange |Windows サービスに適用された変更の種類 (State / StartupType / Path / ServiceAccount)。Windows サービスの変更にのみ適用 |
| SvcDisplayName |ConfigurationChange |変更されたサービスの表示名 |
| SvcName |ConfigurationChange |変更されたサービスの名前 |
| SvcState |ConfigurationChange |サービスの新しい (現在の) 状態 |
| SvcPreviousState |ConfigurationChange |サービスの直前の既知の状態 (サービスの状態が変更された場合のみ適用) |
| SvcStartupType |ConfigurationChange |サービスのスタートアップの種類 |
| SvcPreviousStartupType |ConfigurationChange |直前のサービスのスタートアップの種類 (サービスのスタートアップの種類が変更された場合のみ適用) |
| SvcAccount |ConfigurationChange |サービス アカウント |
| SvcPreviousAccount |ConfigurationChange |直前のサービス アカウント (サービス アカウントが変更された場合のみ適用) |
| SvcPath |ConfigurationChange |Windows サービスの実行可能ファイルへのパス |
| SvcPreviousPath |ConfigurationChange |Windows サービスの実行可能ファイルの直前のパス (パスが変更された場合にのみ適用) |
| SvcDescription |ConfigurationChange |サービスの説明 |
| 前へ |ConfigurationChange |このソフトウェアの直前の状態 (インストールされている/インストールされていない/直前のバージョン) |
| Current |ConfigurationChange |このソフトウェアの最新の状態 (インストールされている/インストールされていない/現在のバージョン) |

## <a name="next-steps"></a>次のステップ
ログ検索の詳細については、以下を参照してください。

* [ログ検索](log-analytics-log-searches.md) について理解を深め、ソリューションによって収集された情報の詳細を確認します。
* ログの検索を拡張するには、 [Log Analytics でカスタム フィールド](log-analytics-custom-fields.md) を使用します。

