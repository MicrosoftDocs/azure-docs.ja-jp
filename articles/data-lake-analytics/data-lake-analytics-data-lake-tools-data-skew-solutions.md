---
title: データ スキューを解決する - Azure Data Lake Tools for Visual Studio
description: Azure Data Lake Tools for Visual Studio を使用して、データ スキュー問題の解決策をトラブルシューティングします。
ms.reviewer: jasonh
ms.service: data-lake-analytics
ms.topic: how-to
ms.date: 12/16/2016
ms.openlocfilehash: 0b82ce187c06afa69e54ea93931e1745f0d52674
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "92219192"
---
# <a name="resolve-data-skew-problems-by-using-azure-data-lake-tools-for-visual-studio"></a>Azure Data Lake Tools for Visual Studio を使用してデータ スキュー問題を解決する

## <a name="what-is-data-skew"></a>データ スキュー問題とは何ですか。

簡単に言うと、データ スキューとは過度に表現された値のことです。 たとえば、納税申告の監査で、50 人の税務調査官を米国の各州に 1 人ずつ割り当てたとします。 ワイオミング州は人口が少ないので、調査官の仕事はあまり多くありません。 しかしカリフォルニア州では人口が多いため、調査官は常にとても忙しい状態です。
    ![データ スキュー問題の例](./media/data-lake-analytics-data-lake-tools-data-skew-solutions/data-skew-problem.png)

このケースでは、データが調査官に均等に分散されていないので、調査官の作業量にばらつきが生じます。 ジョブの実行でも、こうした状況が頻繁に起きているでしょう。 技術的に言うと、1 つの頂点に与えられるデータが他の頂点よりはるかに多いので、その頂点は他よりも長く実行することになり、結果的にジョブ全体の進行が遅くなります。 そのうえ、いくつかの頂点に実行限度 5 時間やメモリ制限 6 GBといった制限があれば、ジョブが失敗する可能性があります。

## <a name="resolving-data-skew-problems"></a>データ スキュー問題を解決する

Azure Data Lake Tools for Visual Studio は、ジョブにおいてデータ スキューの問題があるかどうかを検出することができます。 問題がある場合は、ここに示した解決策を試みることで解決できます。

## <a name="solution-1-improve-table-partitioning"></a>解決策 1: テーブルのパーティションを改善する

### <a name="option-1-filter-the-skewed-key-value-in-advance"></a>オプション 1: 傾斜したキーの値を事前にフィルター処理する

ビジネス ロジックに影響を与えないなら、事前に頻度の高い値をフィルター処理できます。 たとえば、GUID 列に多数の 000 000-000 がある場合は、その値の集計は避けたいかもしれません。 集計前に「WHERE GUID != “000-000-000”」と入力して、頻度の高い値をフィルター処理することができます。

### <a name="option-2-pick-a-different-partition-or-distribution-key"></a>オプション 2:別のパーティションまたはディストリビューション キーを選択する

前述の例では、国や地域全体の税務監査状況だけを確認するのであれば、ID 番号をキーとして選択してデータ分散を改善することができます。 異なるパーティションやディストリビューション キーを選択することでデータをより均等に分散できる場合もありますが、それがビジネス ロジックに影響を与えないように注意する必要があります。 たとえば、各州の納税額の合計を計算するには、_州_ をパーティション キーとして指定します。 問題が改善されない場合は、オプション 3 を試してみます。

### <a name="option-3-add-more-partition-or-distribution-keys"></a>オプション 3:パーティション キーまたはディストリビューション キーを追加する

_州_ のみをパーティション キーとして使用する代わりに、パーティションに 2 つ以上のキーを使用することができます。 たとえば、_郵便番号_ を追加のパーティション キーとして加えて、データ パーティションのサイズを削減し、データをより均等に分散することを検討してもよいでしょう。

### <a name="option-4-use-round-robin-distribution"></a>オプション 4:ラウンドロビン分散を使用する

パーティションとディストリビューションに適切なキーが見つからない場合は、ラウンドロビン分散を使用することができます。 ラウンドロビン分散はすべての行を均等に処理し、対応するバケットにランダムに配置します。 データは均等に分散されますが、地域情報を失い、一部の操作においてジョブのパフォーマンスが削減されるという欠点もあります。 さらに、いずれにしても傾斜キーの集計を行うという場合は、データ スキュー問題はなくなりません。 ラウンドロビン分散の詳細については、「[CREATE TABLE (U-SQL): Creating a Table with Schema (CREATE TABLE (U-SQL): スキーマを使用してテーブルを作成する)](/u-sql/ddl/tables/create/managed/create-table-u-sql-creating-a-table-with-schema#dis_sch)」の「U-SQL Table Distributions (U-SQL テーブルの分散)」を参照してください。

## <a name="solution-2-improve-the-query-plan"></a>解決策 2: クエリ プランを改善する

### <a name="option-1-use-the-create-statistics-statement"></a>オプション 1: CREATE STATISTICS ステートメントを使用する

U-SQL では、CREATE STATISTICS ステートメントをテーブルで提供します。 このステートメントは、テーブルに格納された値分布などのデータの特性に関する詳細情報を、クエリ オプティマイザーに提供します。 ほとんどのクエリでは、クエリ オプティマイザーは高品質のクエリ プランに必要な統計情報を既に生成しています。 場合によっては、CREATE STATISTICS で追加の統計情報を作成したり、またはクエリ デザインを変更したりすることで、クエリのパフォーマンスを向上させる必要があるでしょう。 詳細については、「[CREATE STATISTICS (U-SQL)](/u-sql/ddl/statistics/create-statistics)」ページを参照してください。

コード例:

```usql
CREATE STATISTICS IF NOT EXISTS stats_SampleTable_date ON SampleDB.dbo.SampleTable(date) WITH FULLSCAN;
```

>[!NOTE]
>統計情報は自動的には更新されません。 テーブル内のデータを更新しても、統計を再作成しなければ、クエリ パフォーマンスが低下することがあります。

### <a name="option-2-use-skewfactor"></a>オプション 2:SKEWFACTOR を使用する

上記の例で各州の税を合計する場合、州ごとにグループ化する以外の選択肢はありません。これはデータ スキュー問題の影響を受けます。 ただし、クエリにデータ ヒントを指定してキーのデータ スキューを識別することにより、オプティマイザー実行プランを準備できます。

通常、パラメーターを 0.5 および 1 に設定できます。0.5 は微量の傾斜、1 は激しい傾斜を意味します。 ヒントは、現在のステートメント、およびすべてのダウンストリーム ステートメントの実行プランの最適化に影響を与えるため、傾斜キーの集計があり得る場合は、必ず事前にヒントを追加しておいてください。

```usql
SKEWFACTOR (columns) = x
```

特定の列の傾斜係数 x が 0 (傾斜なし) から 1 (非常に大きい傾斜) であることを示すヒントを提供します。

コード例:

```usql
//Add a SKEWFACTOR hint.
@Impressions =
    SELECT * FROM
    searchDM.SML.PageView(@start, @end) AS PageView
    OPTION(SKEWFACTOR(Query)=0.5)
    ;
//Query 1 for key: Query, ClientId
@Sessions =
    SELECT
        ClientId,
        Query,
        SUM(PageClicks) AS Clicks
    FROM
        @Impressions
    GROUP BY
        Query, ClientId
    ;
//Query 2 for Key: Query
@Display =
    SELECT * FROM @Sessions
        INNER JOIN @Campaigns
            ON @Sessions.Query == @Campaigns.Query
    ;
```

### <a name="option-3-use-rowcount"></a>オプション 3:ROWCOUNT を使用する
SKEWFACTOR に加えて、特定の傾斜キー結合については、結合された一方の行セットが小さいことがわかっている場合、JOIN の前に U-SQL ステートメントに ROWCOUNT のヒントを追加してオプティマイザーに認識させることができます。 これによりオプティマイザーは、ブロードキャストの結合方法を選択してパフォーマンスを改善することができます。 ただし、ROWCOUNT はデータ スキュー問題の対処にある程度役立ちますが、問題を解決するわけではありません。

```usql
OPTION(ROWCOUNT = n)
```

推定の整数行数を指定して、JOIN の前の小さな行セットを識別します。

コード例:

```usql
//Unstructured (24-hour daily log impressions)
@Huge   = EXTRACT ClientId int, ...
            FROM @"wasb://ads@wcentralus/2015/10/30/{*}.nif"
            ;
//Small subset (that is, ForgetMe opt out)
@Small  = SELECT * FROM @Huge
            WHERE Bing.ForgetMe(x,y,z)
            OPTION(ROWCOUNT=500)
            ;
//Result (not enough information to determine simple broadcast JOIN)
@Remove = SELECT * FROM Bing.Sessions
            INNER JOIN @Small ON Sessions.Client == @Small.Client
            ;
```

## <a name="solution-3-improve-the-user-defined-reducer-and-combiner"></a>解決策 3: ユーザー定義レジューサーおよびコンバイナーを改善する

複雑な処理ロジックに対応するユーザー定義演算子を構築することもあるでしょう。適切に構築されたレジューサーおよびコンバイナーは、一部のケースにおいてデータ スキュー問題を軽減する場合があります。

### <a name="option-1-use-a-recursive-reducer-if-possible"></a>オプション 1: 可能な限り再帰的なレジューサーを使用する

既定では、ユーザー定義レジューサーは非再帰モードで実行されます。これは、キーの作業負荷が軽減された場合、その負荷が 1 つの頂点に配布されることを意味します。 しかし、データがスキューされている場合、膨大なデータ セットが 1 つの頂点で処理され、長いあいだ実行されることがあります。

パフォーマンスを向上させるためには、属性をコードに追加して、再帰的なモードで実行するレジューサを定義することができます。 そして、膨大なデータ セットを複数の頂点に分散して並行処理し、ジョブを高速化することができます。

レジューサーを非再帰的から再帰的に変更する場合、アルゴリズムが結合法則であることを確認する必要があります。 たとえば、合計は結合法則ですが、中央値は違います。 また、レジューサーの入力および出力が同じスキーマを保持することを確認する必要があります。

再帰的なレジューサーの属性:

```usql
[SqlUserDefinedReducer(IsRecursive = true)]
```

コード例:

```usql
[SqlUserDefinedReducer(IsRecursive = true)]
public class TopNReducer : IReducer
{
    public override IEnumerable<IRow>
        Reduce(IRowset input, IUpdatableRow output)
    {
        //Your reducer code goes here.
    }
}
```

### <a name="option-2-use-row-level-combiner-mode-if-possible"></a>オプション 2:可能な限り行レベルのコンバイナー モードを使用する

特定の傾斜キーを結合する場合の ROWCOUNT ヒントと同様に、コンバイナー モードでは、膨大な量の傾斜キー値のセットを複数の頂点に分散するよう試み、作業を同時に実行できるようにします。 コンバイナー モードではデータ スキュー問題は解決できませんが、膨大な量の傾斜キー値のセットを扱うときに役立つことがあります。

既定ではコンバイナー モードは完全であり、左行セットと右行セットは分割できません。 モードを左/右/内部として設定すると、行レベルの結合が有効になります。 システムは対応する行セットを分離し、それらを並列で実行している複数の頂点に分散します。 ただし、コンバイナー モードを構成する前に、対応する行セットを分割できるかどうかを必ず確認してください。

次の例では、分割さた左の行セットを示します。 出力行はいずれも、左側の単一の入力行に依存し、また、キー値の同じ右側の行すべてにも依存する可能性があります。 コンバイナー モードを左として設定した場合、システムは巨大な左側の行セットをより小さな行セットに分割し、それらを複数の頂点に割り当てます。

![コンバイナー モードの図](./media/data-lake-analytics-data-lake-tools-data-skew-solutions/combiner-mode-illustration.png)

>[!NOTE]
>間違ったコンバイナー モードを設定すると、組み合わせは効率が低下し、正しくない結果となる可能性があります。

コンバイナー モードの属性:

- SqlUserDefinedCombiner(Mode=CombinerMode.Full): 出力行がいずれも、キー値が同じ左右の入力行すべてに依存する可能性があります。

- SqlUserDefinedCombiner(Mode=CombinerMode.Left): 出力行がいずれも、左側の単一の入力行に依存します (また、キー値が同じ右側の行すべてにも依存する可能性があります)。

- qlUserDefinedCombiner(Mode=CombinerMode.Right): 出力行がいずれも、右側の単一の入力行に依存します (また、キー値が同じ左側の行すべてにも依存する可能性があります)。

- SqlUserDefinedCombiner(Mode=CombinerMode.Inner): 出力行がいずれも、値が同じである左側および右側の単一の入力行に依存します。

コード例:

```usql
[SqlUserDefinedCombiner(Mode = CombinerMode.Right)]
public class WatsonDedupCombiner : ICombiner
{
    public override IEnumerable<IRow>
        Combine(IRowset left, IRowset right, IUpdatableRow output)
    {
    //Your combiner code goes here.
    }
}
```
