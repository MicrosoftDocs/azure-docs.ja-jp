---
title: マッピング データ フロー スクリプト
description: Data Factory のデータ フロー スクリプトのコードビハインド言語の概要
author: kromerm
ms.author: nimoolen
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 02/15/2021
ms.openlocfilehash: 7dd58a7d4a94b832e52930f8ac6507cdd8f7a20e
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "100534823"
---
# <a name="data-flow-script-dfs"></a>データ フロー スクリプト (DFS)

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

データ フロー スクリプト (DFS) とは、マッピング データ フローに含まれている変換を実行するために使用される、コーディング言語に似た基礎になっているメタデータです。 すべての変換は、ジョブを正しく実行するために必要な情報を提供する一連のプロパティによって表されます。 スクリプトは、ブラウザー UI の上部のリボンにある [スクリプト] ボタンをクリックして、ADF から表示および編集できます。

![[スクリプト] ボタン](media/data-flow/scriptbutton.png "[スクリプト] ボタン")

たとえば、ソース変換の `allowSchemaDrift: true,` は、それがスキーマ プロジェクションに含まれていない場合でも、データ フロー内のソース データセットのすべての列を含めるようにサービスに指示します。

## <a name="use-cases"></a>ユース ケース
DFS は、ユーザー インターフェイスによって自動的に生成されます。 スクリプトを表示してカスタマイズするには、[スクリプト] ボタンをクリックします。 ADF UI を使用せずにスクリプトを生成し、それを PowerShell コマンドレットに渡すこともできます。 複雑なデータ フローをデバッグする場合、ご自分のフローの UI グラフ表現をスキャンするのではなく、スクリプトのコードビハインドをスキャンした方が簡単な場合があります。

次にいくつかのユース ケースの例を示します。
- 非常に類似したデータ フローをプログラミングして多数作成する場合。つまり、データ フローを "スタンプアウト" する場合。
- UI で管理するのが困難な、または検証の問題が発生する可能性がある複雑な式の場合。
- 実行時に返されるさまざまなエラーをデバッグおよび理解したい場合。

PowerShell または API で使用するデータ フロー スクリプトを作成する場合は、書式設定されたテキストを 1 行に折りたたむ必要があります。 これらのタブと改行はエスケープ文字として保持できます。 ただし、テキストは JSON プロパティ内に収まるように書式設定する必要があります。 下部のスクリプト エディターの UI には、スクリプトを 1 行に書式設定するボタンがあります。

![コピー ボタン](media/data-flow/copybutton.png "コピー ボタン")

## <a name="how-to-add-transforms"></a>変換を追加する方法
変換を追加するには、主な変換データを追加し、入力ストリームを再ルーティングし、出力ストリームを再ルーティングするという 3 つの基本的な手順を行う必要があります。 これは、例で簡単に確認できます。
まず、次のようにデータ フローをシンクする単純なソースで開始します。

```
source(output(
        movieId as string,
        title as string,
        genres as string
    ),
    allowSchemaDrift: true,
    validateSchema: false) ~> source1
source1 sink(allowSchemaDrift: true,
    validateSchema: false) ~> sink1
```

派生変換を追加する場合は、まず、`upperCaseTitle` という新しい大文字の列を追加する主な変換テキストの単純な式を作成する必要があります。
```
derive(upperCaseTitle = upper(title)) ~> deriveTransformationName
```

次いで、この既存の DFS を使用して変換を追加します。
```
source(output(
        movieId as string,
        title as string,
        genres as string
    ),
    allowSchemaDrift: true,
    validateSchema: false) ~> source1
derive(upperCaseTitle = upper(title)) ~> deriveTransformationName
source1 sink(allowSchemaDrift: true,
    validateSchema: false) ~> sink1
```

次に、新しい変換をどの変換の後に入れるか (この場合は `source1`) を指定し、そのストリームの名前を新しい変換にコピーして、受信ストリームを再ルーティングします。
```
source(output(
        movieId as string,
        title as string,
        genres as string
    ),
    allowSchemaDrift: true,
    validateSchema: false) ~> source1
source1 derive(upperCaseTitle = upper(title)) ~> deriveTransformationName
source1 sink(allowSchemaDrift: true,
    validateSchema: false) ~> sink1
```

最後に、この新しい変換の後に入れる変換を指定し、その入力ストリーム (この例では `sink1`) を新しい変換の出力ストリーム名に置き換えます。
```
source(output(
        movieId as string,
        title as string,
        genres as string
    ),
    allowSchemaDrift: true,
    validateSchema: false) ~> source1
source1 derive(upperCaseTitle = upper(title)) ~> deriveTransformationName
deriveTransformationName sink(allowSchemaDrift: true,
    validateSchema: false) ~> sink1
```

## <a name="dfs-fundamentals"></a>DFS の基礎
DFS は、ソース、シンクなど、新しい列の追加、データのフィルター処理、データの結合など多数のことを実行できる一連の変換の組み合わせで構成されています。 通常スクリプトは、1 つ以上のソースで開始し、その後に多数の変換が続き、1 つ以上のシンクで終わります。

すべてのソースの基本構成は同じです。
```
source(
  source properties
) ~> source_name
```

たとえば、3 つの列 (movieId、title、genres) がある単純なソースは次のようになります。
```
source(output(
        movieId as string,
        title as string,
        genres as string
    ),
    allowSchemaDrift: true,
    validateSchema: false) ~> source1
```

ソース以外のすべての変換の基本構成は同じです。
```
name_of_incoming_stream transformation_type(
  properties
) ~> new_stream_name
```

たとえば、列 (title) を受け取り、それを大文字のバージョンで上書きする単純な派生変換は、次のようになります。
```
source1 derive(
  title = upper(title)
) ~> derive1
```

スキーマのないシンクは、単純に次のようになります。
```
derive1 sink(allowSchemaDrift: true,
    validateSchema: false) ~> sink1
```

## <a name="script-snippets"></a>スクリプト スニペット

スクリプト スニペットは、データ フロー間で共有するために使用できるデータ フロー スクリプトの共有可能なコードです。 次のビデオでは、スクリプト スニペットを使用し、データ フロー スクリプトを利用して、データ フロー グラフの背後にあるスクリプトの部分をコピーして貼り付ける方法について説明します。

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE4tA9b]


### <a name="aggregated-summary-stats"></a>集計要約統計量
「SummaryStats」という名前のデータフローに集計変換を追加し、スクリプト内の集計関数に次のコードを貼り付けて、既存の SummaryStats を置き換えます。 これにより、データ プロファイルの概要の統計情報の汎用パターンが提供されます。

```
aggregate(each(match(true()), $$+'_NotNull' = countIf(!isNull($$)), $$ + '_Null' = countIf(isNull($$))),
        each(match(type=='double'||type=='integer'||type=='short'||type=='decimal'), $$+'_stddev' = round(stddev($$),2), $$ + '_min' = min ($$), $$ + '_max' = max($$), $$ + '_average' = round(avg($$),2), $$ + '_variance' = round(variance($$),2)),
        each(match(type=='string'), $$+'_maxLength' = max(length($$)))) ~> SummaryStats
```
次のサンプルを使用して、一意の数とデータ内の個別行の数をカウントすることもできます。 次の例は、ValueDistAgg という集計変換を使用してデータ フローに貼り付けることができます。 この例では、「title」という名前の列を使用します。 値のカウントを取得するために使用するデータの文字列型の列に、「title」を必ず置き換えてください。

```
aggregate(groupBy(title),
    countunique = count()) ~> ValueDistAgg
ValueDistAgg aggregate(numofunique = countIf(countunique==1),
        numofdistinct = countDistinct(title)) ~> UniqDist
```

### <a name="include-all-columns-in-an-aggregate"></a>集計にすべての列を含める
これは、集計を構築するときに、出力メタデータの残りの列を保持する方法を示す一般的な集計パターンです。 この例では、```first()``` 関数を使用して、名前が「movie」ではないすべての列の最初の値を選択します。 これを使用するには、DistinctRows という名前の集計変換を作成し、これをスクリプト内で既存の DistinctRows 集計スクリプトの先頭に貼り付けます。

```
aggregate(groupBy(movie),
    each(match(name!='movie'), $$ = first($$))) ~> DistinctRows
```

### <a name="create-row-hash-fingerprint"></a>行ハッシュ フィンガープリントの作成 
データ フロー スクリプトでこのコードを使用して、3 つの列の ```sha1``` ハッシュを生成する ```DWhash``` という名前の新しい派生列を作成します。

```
derive(DWhash = sha1(Name,ProductNumber,Color)) ~> DWHash
```

また、次のスクリプトを使用すると、各列に名前を付けなくても、ストリームに存在するすべての列を使用して行ハッシュを生成できます。

```
derive(DWhash = sha1(columns())) ~> DWHash
```

### <a name="string_agg-equivalent"></a>String_agg の同等のもの
このコードは T-SQL ```string_agg()``` 関数のように動作し、文字列値を配列に集約します。 その後、SQL 変換先で使用するために、その配列を文字列にキャストできます。

```
source1 aggregate(groupBy(year),
    string_agg = collect(title)) ~> Aggregate1
Aggregate1 derive(string_agg = toString(string_agg)) ~> StringAgg
```

### <a name="count-number-of-updates-upserts-inserts-deletes"></a>更新、upsert、挿入、削除の数をカウントする
行の変更変換を使用する場合は、行の変更ポリシーによって発生した更新、upsert、挿入、削除の数をカウントしたい場合があります。 変更行の後に集計変換を追加し、そのデータ フロー スクリプトをこれらのカウントの集計の定義に貼り付けます。

```
aggregate(updates = countIf(isUpdate(), 1),
        inserts = countIf(isInsert(), 1),
        upserts = countIf(isUpsert(), 1),
        deletes = countIf(isDelete(),1)) ~> RowCount
```

### <a name="distinct-row-using-all-columns"></a>すべての列を使用する個別の行
このスニペットは、すべての入力列を受け取り、グループ化に使用されるハッシュを生成して重複を排除した後、各重複項目の最初の発生を出力として提供する新しい集計変換を、データ フローに追加します。 明示的に列の名前を指定する必要はなく、これらは、受信データ ストリームから自動的に生成されます。

```
aggregate(groupBy(mycols = sha2(256,columns())),
    each(match(true()), $$ = first($$))) ~> DistinctRows
```

### <a name="check-for-nulls-in-all-columns"></a>すべての列に Null があるかどうかを確認する
これは、データ フローに貼り付けて、NULL 値があるかどうかすべての列をまとめて確認することができるスニペットです。 この手法は、スキーマの誤差を利用してすべての行のすべての列を調べ、条件分割を使用して、Null 値を持つ行と Null 値を持たない行とを分離します。 

```
split(contains(array(columns()),isNull(#item)),
    disjoint: false) ~> LookForNULLs@(hasNULLs, noNULLs)
```

### <a name="automap-schema-drift-with-a-select"></a>Select を使用したスキーマの誤差の自動マッピング
不明または動的な入力列のセットから既存のデータベース スキーマを読み込む必要がある場合は、シンク変換で右側の列をマップする必要があります。 これは既存のテーブルを読み込む場合にのみ必要です。 このスニペットをシンクの前に追加して、列を自動マップする Select を作成します。 シンク マッピングは自動マップのままにしておきます。

```
select(mapColumn(
        each(match(true()))
    ),
    skipDuplicateMapInputs: true,
    skipDuplicateMapOutputs: true) ~> automap
```

### <a name="persist-column-data-types"></a>列のデータ型を保持する
このスクリプトを派生列の定義内に追加し、シンクを使用して、データ フローの列名とデータ型を永続ストアに格納します。

```
derive(each(match(type=='string'), $$ = 'string'),
    each(match(type=='integer'), $$ = 'integer'),
    each(match(type=='short'), $$ = 'short'),
    each(match(type=='complex'), $$ = 'complex'),
    each(match(type=='array'), $$ = 'array'),
    each(match(type=='float'), $$ = 'float'),
    each(match(type=='date'), $$ = 'date'),
    each(match(type=='timestamp'), $$ = 'timestamp'),
    each(match(type=='boolean'), $$ = 'boolean'),
    each(match(type=='long'), $$ = 'long'),
    each(match(type=='double'), $$ = 'double')) ~> DerivedColumn1
```

### <a name="fill-down"></a>フィル ダウン
ここでは、NULL 値をシーケンス内の NULL 以外の前の値に置換する場合に、データセットで一般的な "フィル ダウン" 問題を実装する方法について説明します。 この操作では、データセット全体に対して "dummy" カテゴリ値を使って合成ウィンドウを作成する必要があるため、パフォーマンスに悪影響がある可能性があることに注意してください。 また、NULL 以外の前の値を見つけるには、値で並べ替えて適切なデータ シーケンスを作成する必要があります。 次のスニペットでは、"dummy" として合成カテゴリが作成され、代理キーで並べ替えが行われます。 この代理キーを削除して、独自のデータ固有の並べ替えキーを使用できます。 このコード スニペットでは、```source1``` というソース変換が既に追加済みであることを前提としています。

```
source1 derive(dummy = 1) ~> DerivedColumn
DerivedColumn keyGenerate(output(sk as long),
    startAt: 1L) ~> SurrogateKey
SurrogateKey window(over(dummy),
    asc(sk, true),
    Rating2 = coalesce(Rating, last(Rating, true()))) ~> Window1
```

### <a name="moving-average"></a>移動平均
移動平均は、ウィンドウ変換を使用してデータ フロー内に非常に簡単に実装できます。 以下の例では、Microsoft の株価の 15 日間の移動平均を作成します。

```
window(over(stocksymbol),
    asc(Date, true),
    startRowOffset: -7L,
    endRowOffset: 7L,
    FifteenDayMovingAvg = round(avg(Close),2)) ~> Window1
```

## <a name="next-steps"></a>次のステップ

データ フローの詳細については、まず[データ フロー概要に関する記事](concepts-data-flow-overview.md)を参照してください。
