---
title: "データベース互換性レベル 130 - Azure SQL Database | Microsoft Docs"
description: "この記事では、Azure SQL Database を互換性レベル 130 で実行して、新機能であるクエリ オプティマイザーとクエリ プロセッサを活用するメリットについて説明します。 また、既存の SQL アプリケーションのクエリ パフォーマンスに対して生じ得る副作用についても説明します。"
services: sql-database
documentationcenter: 
author: alainlissoir
manager: jhubbard
editor: 
ms.assetid: 8619f90b-7516-46dc-9885-98429add0053
ms.service: sql-database
ms.custom: monitor and tune
ms.workload: data-management
ms.devlang: NA
ms.tgt_pltfrm: NA
ms.topic: article
ms.date: 03/03/2017
ms.author: alainl
translationtype: Human Translation
ms.sourcegitcommit: 2f03ba60d81e97c7da9a9fe61ecd419096248763
ms.openlocfilehash: 5649895d1ae39d9a7fa863407b5341f1cdf567ee
ms.lasthandoff: 03/04/2017


---
# <a name="improved-query-performance-with-compatibility-level-130-in-azure-sql-database"></a>Azure SQL Database の互換性レベル 130 によるクエリ パフォーマンスの向上
Azure SQL Database は、数十万のデータベースをさまざまな互換性レベルで透過的に実行し、すべてのお客様に対して Microsoft SQL Server の該当バージョンに対する下位互換性の保持と保証しています。

この記事では、Azure SQL Database を互換性レベル 130 で実行して、新機能であるクエリ オプティマイザーとクエリ プロセッサを活用するメリットについて説明します。 また、既存の SQL アプリケーションのクエリ パフォーマンスに対して生じ得る副作用についても説明します。

これまでの SQL のバージョンと互換性レベルの既定の設定は次のようになっています。

* 100: SQL Server 2008 と Azure SQL Database V11。
* 110: SQL Server 2012 と Azure SQL Database V11。
* 120: SQL Server 2014 と Azure SQL Database V12。
* 130: SQL Server 2016 と Azure SQL Database V12。

> [!IMPORTANT]
> **新しく作成された**データベースの既定の互換性レベルは 130 です。
> 
> 2016年 6 月中旬より前に作成されたデータベースには、作成時のレベルが適用されます。 Azure SQL Database の以前のバージョンから移行されたデータベースの互換性レベルは、100 または 110 のいずれかになります。 2016年 6 月中旬より前に作成されたサーバーのマスター データベースには、サーバーの作成時のレベルが適用されます。このレベルを変更することはできません。 マスター データベースとユーザー データベースの間で互換性レベルが違っていても、ユーザーのデータベースの機能やパフォーマンスへの影響はありません。
> 

## <a name="about-compatibility-level-130"></a>互換性 130 について
まず、データベースの現在の互換性レベルを把握する場合は、次の Transact-SQL ステートメントを実行します。

```
SELECT compatibility_level
    FROM sys.databases
    WHERE name = '<YOUR DATABASE_NAME>’;
```


この**新しく作成される**データベースでのレベル 130 への変更が実施される前に、いくつかの基本的なクエリの例を通して、これがどのような変更であるかを確認し、その変更にどのようなメリットがあるかについて見ていきましょう。

リレーショナル データベースにおけるクエリ処理は非常に複雑になる可能性があり、設計に固有の選択肢と動作を理解するには、コンピューター サイエンスと数学の深い知識が必要になる可能性があります。 このドキュメントでは、多くの専門知識を持っていない人でも、互換性レベルの変更の影響を理解し、アプリケーションにもたらすメリットを判断できるように、その内容を意図的に簡略化しています。

互換性レベル 130 が提供するメリットをざっと見てみましょう。  「 [ALTER DATABASE 互換性レベル (Transact-SQL)](https://msdn.microsoft.com/library/bb510680.aspx)」で詳細を確認できますが、次のように要約できます。

* Insert-select ステートメントの挿入操作は、マルチ スレッドにするか並列プランを使用できます。この操作は、今まではシングル スレッドでした。
* メモリ最適化テーブルとテーブル変数のクエリで、並列プランを使用できるようになりました。この操作も、今まではシングル スレッドでした。
* メモリ最適化テーブルの統計で、サンプリングと自動更新が行われるようになりました。 「 [新機能 (データベース エンジン): インメモリ OLTP](https://msdn.microsoft.com/library/bb510411.aspx#InMemory) 」を参照してください。
* 列ストア インデックスでのバッチ モードと行モードの変更
  * 列ストア インデックス付きのテーブルでの並べ替えは、バッチ モードで実行されるようになりました。
  * ウィンドウ集計は、TSQL LAG/LEAD ステートメントなどのバッチ モードで実行されるようになりました。
  * 重複しない句が複数ある列ストア テーブルでのクエリは、Batch モードで動作します。
  * DOP =&1; または直列プランで実行されるクエリも、Batch モードで実行されます。
* 最後に、基数推定の機能強化は互換性レベル 120 で行われていますが、それより低い互換性レベル (100 または 110) を実行中のユーザーは、互換性レベル 130 に移行することでその機能強化を利用できるようになり、アプリケーションのクエリ パフォーマンスを向上させることができます。

## <a name="practicing-compatibility-level-130"></a>互換性レベル 130 の検証
いくつかの新しい機能を検証するために、複数のテーブル、インデックス、およびランダム データを作成することから始めましょう。 ここで示す TSQL スクリプトの例は、SQL Server 2016 または Azure SQL Database で実行できます。 ただし、Azure SQL Database を作成するときは、最低でも P2 データベースを必ず選択してください。これは、マルチスレッド化を可能にしてそれらの機能からメリットを得るには、少なくとも&2; つのコアが必要であるためです。

```
-- Create a Premium P2 Database in Azure SQL Database

CREATE DATABASE MyTestDB
    (EDITION=’Premium’, SERVICE_OBJECTIVE=’P2′);
GO

-- Create 2 tables with a column store index on
-- the second one (only available on Premium databases)

CREATE TABLE T_source
    (Color varchar(10), c1 bigint, c2 bigint);

CREATE TABLE T_target
    (c1 bigint, c2 bigint);

CREATE CLUSTERED COLUMNSTORE INDEX CCI ON T_target;
GO

-- Insert few rows.

INSERT T_source VALUES
    (‘Blue’, RAND() * 100000, RAND() * 100000),
    (‘Yellow’, RAND() * 100000, RAND() * 100000),
    (‘Red’, RAND() * 100000, RAND() * 100000),
    (‘Green’, RAND() * 100000, RAND() * 100000),
    (‘Black’, RAND() * 100000, RAND() * 100000);

GO 200

INSERT T_source SELECT * FROM T_source;

GO 10
```


それでは、互換性レベル 130 に付随するクエリ処理機能をいくつか見ていきましょう。

## <a name="parallel-insert"></a>並列 INSERT
次の TSQL ステートメントを実行すると、互換性レベル 120 と 130 で INSERT 操作が実行されます。それぞれの INSERT 操作は、シングル スレッド モデル (120) とマルチスレッド モデル (130) で実行されます。

```
-- Parallel INSERT … SELECT … in heap or CCI
-- is available under 130 only

SET STATISTICS XML ON;

ALTER DATABASE MyTestDB
    SET COMPATIBILITY_LEVEL = 120;
GO 

-- The INSERT part is in serial

INSERT t_target WITH (tablock)
    SELECT C1, COUNT(C2) * 10 * RAND()
        FROM T_source
        GROUP BY C1
    OPTION (RECOMPILE);

ALTER DATABASE MyTestDB
    SET COMPATIBILITY_LEVEL = 130
GO

-- The INSERT part is in parallel

INSERT t_target WITH (tablock)
    SELECT C1, COUNT(C2) * 10 * RAND()
        FROM T_source
        GROUP BY C1
    OPTION (RECOMPILE);

SET STATISTICS XML OFF;
```


実際のクエリ プランを要求し、そのグラフィカル表現または XML コンテンツを調べることで、どの基数推定機能が使用されているかを判断できます。 図 1 に示されているプランを調べると、列ストアでの INSERT の実行は、120 ではシリアルで実行され、130 ではパラレルで実行されることがはっきりとわかります。 また、130 のプランでは、反復子アイコンが 2 つの平行矢印に変更されていることに注意してください。これは、反復子の実行が本当に並列になっているという事実を示しています。 大量の INSERT 操作を完了する場合、並列実行のパフォーマンスは、データベースのために使用できるコアの数に応じて向上し、状況によっては最大で 100 倍速くなります。

*図 1: INSERT 操作の互換性レベル 130 でのシリアルからパラレルへの変更。*

![図 1](./media/sql-database-compatibility-level-query-performance-130/figure-1.jpg)

## <a name="serial-batch-mode"></a>シリアル バッチ モード
同様に、互換性レベル 130 に移行すると、データ行を処理するときにバッチ モード処理が可能になります。 まず、バッチ モード操作は、列ストア インデックスが存在する場合にのみ使用できます。 次に、バッチは、通常は&900; 未満の行に相当し、マルチコア CPU 用に最適化されたコード ロジックを使用するため、メモリのスループットが高くなります。そして、可能な場合は常に列ストアの圧縮データを直接利用します。 これらの条件下で、SQL Server 2016 は、1 度に 1 行ではなく 900 未満の行をまとめて処理でき、その結果、操作の全体的なオーバーヘッド コストがバッチ全体で共有され、行単位の全体的なコストが削減されます。 この共有操作と列ストアの圧縮の組合せによって、SELECT バッチ モード操作に関係する待機時間が根本的に短縮されます。 列ストアとバッチ モードの詳細については、「[列ストア インデックスの説明](https://msdn.microsoft.com/library/gg492088.aspx)」をご覧ください。

```
-- Serial batch mode execution

SET STATISTICS XML ON;

ALTER DATABASE MyTestDB
    SET COMPATIBILITY_LEVEL = 120;
GO

-- The scan and aggregate are in row mode

SELECT C1, COUNT (C2)
    FROM T_target
    GROUP BY C1
    OPTION (MAXDOP 1, RECOMPILE);
GO

ALTER DATABASE MyTestDB
    SET COMPATIBILITY_LEVEL = 130;
GO 

-- The scan and aggregate are in batch mode,
-- and force MAXDOP to 1 to show that batch mode
-- also now works in serial mode.

SELECT C1, COUNT(C2)
    FROM T_target
    GROUP BY C1
    OPTION (MAXDOP 1, RECOMPILE);
GO

SET STATISTICS XML OFF;
```


図 2 のクエリ プランを調べると、処理モードが互換性レベルで変更されていることがわかり、その結果、両方の互換性レベルでクエリを実行すると、処理時間の大部分が行モード (86%) で実行され、バッチ モード (14%) で 2 つのバッチが処理されていることがわかります。 データセットを増やすと、メリットが大きくなります。

*図 2: SELECT 操作の互換性レベル 130 でのシリアル モードからバッチ モードへの変更*

![図 2](./media/sql-database-compatibility-level-query-performance-130/figure-2.jpg)

## <a name="batch-mode-on-sort-execution"></a>並べ替え実行時のバッチ モード
上記の例に似ていますが、並べ替え操作に適用される行モード (互換性レベル 120) からバッチ モード (互換性レベル 130) への切り替えによって、SORT 操作のパフォーマンスも同じ理由で向上します。

```
-- Batch mode on sort execution

SET STATISTICS XML ON;

ALTER DATABASE MyTestDB
    SET COMPATIBILITY_LEVEL = 120;
GO

-- The scan and aggregate are in row mode

SELECT C1, COUNT(C2)
    FROM T_target
    GROUP BY C1
    ORDER BY C1
    OPTION (MAXDOP 1, RECOMPILE);
GO

ALTER DATABASE MyTestDB
    SET COMPATIBILITY_LEVEL = 130;
GO

-- The scan and aggregate are in batch mode,
-- and force MAXDOP to 1 to show that batch mode
-- also now works in serial mode.

SELECT C1, COUNT(C2)
    FROM T_target
    GROUP BY C1
    ORDER BY C1
    OPTION (MAXDOP 1, RECOMPILE);
GO

SET STATISTICS XML OFF;
```


図 3 に示されているように、行モードでの並べ替え操作はコストの 81% に相当していますが、バッチ モードではコストの 19% のみに相当していることがわかります (並べ替え自体は、それぞれ 81% と 56%)。

*図 3: SORT 操作の互換性レベル 130 での行モードからバッチ モードへの変更*

![図 3](./media/sql-database-compatibility-level-query-performance-130/figure-3.png)

言うまでもなく、これらのサンプルで扱っているのはほんの数万行であり、これは、最近のほとんどの SQL Server で使用できるデータの量を考えた場合、それほど大きな数値ではありません。 ただし、これらの結果を数百万行に投影すれば、毎日、実行の数分間がワークロードを保留にするために費やされていると解釈できます。

## <a name="cardinality-estimation-ce-improvements"></a>基数推定 (CE) の機能強化
互換性レベル 120 以上で実行されるすべてのデータベースは、SQL Server 2014 で導入された新しい基数推定機能を活用します。 基本的に、基数推定は、SQL Server がその推定コストに基づいてクエリを実行する方法を決定するためのロジックです。 この推定は、クエリに含まれるオブジェクトに関連付けられた統計の入力を使用して計算されます。 実際には、おおざっぱに言えば、基数推定機能は、行数の推定と、値の分布、重複しない値の数、およびクエリで参照されるテーブルとオブジェクトに含まれている重複している値の数に関する情報です。 これらの推定値の取得が間違っていると、不十分なメモリ許可 (TempDB 溢れ) に起因する不要なディスク I/O や、並列プランよりもシリアル プランの実行が優先的に選択される可能性があります。 結論として、不正確な推定は、クエリの実行の全体的なパフォーマンスの低下に至る可能性があります。 一方、正確な推定は、優れたクエリの実行につながります。

前述のように、クエリの最適化と推定は複雑な問題ですが、クエリ プランと基数推定機能の詳細を知りたいのであれば、ドキュメント「 [Optimizing Your Query Plans with the SQL Server 2014 Cardinality Estimator](https://msdn.microsoft.com/library/dn673537.aspx) 」(SQL Server 2014 の基数推定機能によるクエリ プランの最適化) を参照してください。

## <a name="which-cardinality-estimation-do-you-currently-use"></a>現在どの基数推定が使用されているか
クエリがどの基数推定で実行されているかを調べるために、次のクエリ サンプルを見てみましょう。 この最初の例は、互換性レベル 110 で実行されます (前の基数推定機能が使用されます)。

```
-- Old CE

ALTER DATABASE MyTestDB
    SET COMPATIBILITY_LEVEL = 110;
GO

SET STATISTICS XML ON;

SELECT [c1]
    FROM [dbo].[T_target]
    WHERE [c1] > 20000;
GO

SET STATISTICS XML OFF;
```


実行が完了したら、XML リンクをクリックし、最初の反復子のプロパティを確認します。 CardinalityEstimationModelVersion という名前のプロパティが、現在 70 に設定されています。 これは、データベースの互換性レベルが SQL Server 7.0 バージョンに設定されていることを意味するものではありません (互換性レベルは、前述の TSQL ステートメントでわかるように 110 に設定されます)。この値 70 は、SQL Server 7.0 から使用可能になり、SQL Server 2014 まで大幅な改訂が行われていなかった従来の基数推定機能を表しています (互換性レベルは SQL Server 2014 で120 になっています)。

*図 4: 互換性レベル 110 以下が使用される場合、CardinalityEstimationModelVersion は 70 に設定される。*

![図 4](./media/sql-database-compatibility-level-query-performance-130/figure-4.png)

互換性レベル 130 に変更し、[ALTER DATABASE SCOPED CONFIGURATION](https://msdn.microsoft.com/library/mt629158.aspx) で LEGACY_CARDINALITY_ESTIMATION を ON に設定することで、新しい基数推定機能の使用を無効にすることもできます。 これは、基数推定機能の観点から見ると 110 を使用することと同じですが、クエリ処理では最新の互換性レベルが使用されます。 このようにすると、最新の互換性レベルが備えている新しいクエリ処理機能 (バッチ モード) を活用することができますが、必要に応じて、前の基数推定機能も引き続き使用できます。

```
-- Old CE

ALTER DATABASE MyTestDB
    SET COMPATIBILITY_LEVEL = 130;
GO

ALTER DATABASE
    SCOPED CONFIGURATION
    SET LEGACY_CARDINALITY_ESTIMATION = ON;
GO

SET STATISTICS XML ON;

SELECT [c1]
    FROM [dbo].[T_target]
    WHERE [c1] > 20000;
GO

SET STATISTICS XML OFF;
```


新しい基数推定機能は、互換性レベルを 120 または 130 に移行すると有効になります。 このような場合、CardinalityEstimationModelVersion の既定値は 120 または 130 に設定されます。

```
-- New CE

ALTER DATABASE MyTestDB
    SET COMPATIBILITY_LEVEL = 130;
GO

ALTER DATABASE
    SCOPED CONFIGURATION
    SET LEGACY_CARDINALITY_ESTIMATION = OFF;
GO

SET STATISTICS XML ON;

SELECT [c1]
    FROM [dbo].[T_target]
    WHERE [c1] > 20000;
GO

SET STATISTICS XML OFF;
```


*図 5: 互換性レベル 130 が使用される場合、CardinalityEstimationModelVersion は 130 に設定される。*

![図 5](./media/sql-database-compatibility-level-query-performance-130/figure-5.jpg)

## <a name="witnessing-the-cardinality-estimation-differences"></a>基数推定の相違点の確認
次に、いくつかの述語を使用する WHERE 句付きの INNER JOIN を含む少し複雑なクエリを実行してみましょう。最初に、前の基数推定機能から得られる推定行数を調べます。

```
-- Old CE row estimate with INNER JOIN and WHERE clause

ALTER DATABASE MyTestDB
    SET COMPATIBILITY_LEVEL = 130;
GO

ALTER DATABASE
    SCOPED CONFIGURATION
    SET LEGACY_CARDINALITY_ESTIMATION = ON;
GO

SET STATISTICS XML ON;

SELECT T.[c2]
    FROM
                   [dbo].[T_source] S
        INNER JOIN [dbo].[T_target] T  ON T.c1=S.c1
    WHERE
        S.[Color] = ‘Red’  AND
        S.[c2] > 2000  AND
        T.[c2] > 2000
    OPTION (RECOMPILE);
GO

SET STATISTICS XML OFF;
```


このクエリを実行すると、実際には 200,704 行が返されますが、前の基数推定機能による行の推定値は 194,284 行でした。 既に説明したように、これらの行数の結果も、上のサンプルを何回実行したかによって異なります。サンプル テーブルのデータは、実行するたびに書き換えられます。 言うまでもなく、テーブルの形、データの内容、およびデータ間の相関関係に加え、クエリに含まれる述語も実際の推定値に影響を与えます。

*図 6: 推定行数は 194,284 (200,704 行に対して 6,000 行の差)。*

![図 6](./media/sql-database-compatibility-level-query-performance-130/figure-6.jpg)

同じ方法で、新しい基数推定機能を使用して、同じクエリを実行してみましょう。

```
-- New CE row estimate with INNER JOIN and WHERE clause

ALTER DATABASE MyTestDB
    SET COMPATIBILITY_LEVEL = 130;
GO

ALTER DATABASE
    SCOPED CONFIGURATION
    SET LEGACY_CARDINALITY_ESTIMATION = OFF;
GO

SET STATISTICS XML ON;

SELECT T.[c2]
    FROM
                   [dbo].[T_source] S
        INNER JOIN [dbo].[T_target] T  ON T.c1=S.c1
    WHERE
        S.[Color] = ‘Red’  AND
        S.[c2] > 2000  AND
        T.[c2] > 2000
    OPTION (RECOMPILE);
GO

SET STATISTICS XML OFF;
```


図を見ると、行の推定値が 202,877 であり、前の基数推定よりも高くなっていることがわかります。

*図 7: 推定行数は 194,284 ではなく 202,877 になっている。*

![図 7](./media/sql-database-compatibility-level-query-performance-130/figure-7.jpg)

実際の結果セットは 200,704 行です (ただし、すべての数値は、上のサンプル クエリを何回実行したかによって異なりますが、それよりも重要なのは、TSQL では RAND() ステートメントを使用するため、返される実際の値は実行するたびに変化するということです)。 したがって、この特定の例では、202,877 のほうが 194,284 よりも 200,704 に近いため、新しい基数推定のほうが行数の推定を的確に実行しています。 最後に、WHERE 句の述語を (たとえば ">" ではなく) 等価に変更した場合、新旧の基数推定機能による推定の差は、取得できる一致の数によってはさらに広がる可能性があります。

もちろん、この例では、実際の行数との&6000; 未満の差は、状況によっては大量のデータを表すものではありません。 ここで、この差を複数のテーブルとより複雑なクエリに当てはめて考えると、推定の差が数百万行になる可能性があり、それによって間違った実行プランの選択や、不十分なメモリ許可の要求による TempDB 溢れと大量の I/O が発生するリスクが非常に高くなります。

機会があれば、この比較を、ご自身の最も一般的なクエリとデータセットを使用して実際に行って、新旧の推定の差をご自身で検証してください。実際と推定の差が大きいもの、結果セットに返された実際の行数に近いものなどが混在している可能性があります。 すべては、クエリの形、Azure SQL Database の特性、データセットの性質とサイズ、およびそれらに関する統計によって決まります。 Azure SQL Database インスタンスを作成したばかりであれば、クエリ オプティマイザーは、これまでのクエリの実行によって作成された統計を再利用するのではなく、白紙の状態から知識を構築する必要があります。 このように、推定はそれぞれのサーバーとアプリケーションに非常に依存しており、同じものはほとんどないと考えることができます。 これは、常に留意する必要がある重要な点です。

## <a name="some-considerations-to-take-into-account"></a>考慮すべきいくつかの事柄
ほとんどのワークロードでは互換性レベル 130 からメリットを得ることができますが、この互換性レベルを運用環境に導入する前に実行できることには、基本的に次の 3 つのオプションがあります。

1. 互換性レベル 130 に移行し、処理状態を観察します。 後退が見受けられる場合は、互換性レベルを元のレベルに戻すか、互換性レベルは 130 にしたままで基数推定のみをレガシ モードに戻します (前述のように、これだけで問題が解決する可能性があります)。
2. 運用環境に導入する前に、運用環境と同じような負荷をかけて既存のアプリケーションを徹底的にテストし、微調整を行ってパフォーマンスを検証します。 問題が発生した場合、上記と同じように、いつでも元の互換性レベルに戻すか、基数推定をレガシ モードに戻すことができます。
3. 最後のオプションである、これらの疑問に対応するための最新の方法は、クエリ ストアを活用することです。 それが、今日の推奨されるオプションです。 120 以下の互換レベルでは、130 で推奨されるクエリ ストアを使用したクエリ分析の支援はお勧めできません。 クエリ ストアは、最新バージョンである Azure SQL Database V12 で使用可能であり、クエリ パフォーマンスのトラブルシューティングに役立つように設計されています。 クエリ ストアは、すべてのクエリに関する詳細な履歴情報の収集と提示を行う、データベースのフライト データ レコーダーと考えてください。 これは、問題を診断して解決するための時間を短縮することで、パフォーマンスのフォレンジクスを大幅に簡略化します。 詳細については、「 [Query Store: A flight data recorder for your database](https://azure.microsoft.com/blog/query-store-a-flight-data-recorder-for-your-database/)」(クエリ ストア: データベース用のフライト データ レコーダー) を参照してください。

大まかに言って、互換性レベル 120 以下で実行中の一連のデータベースが既に存在するときに、その一部を 130 に移行する予定であるか、既定で 130 に設定される新しいデータベースがワークロードによって自動的にプロビジョニングされる場合は、以下を検討してください。

* 運用環境で新しい互換性レベルに変更する前に、クエリ ストアを有効にします。 [データベースの互換性モードの変更とクエリ ストアの使用に関するページ](https://msdn.microsoft.com/library/bb895281.aspx) を参照できます。
* 次に、運用環境に似た環境で、代表的なデータとクエリを使用して、すべての重要なワークロードをテストし、パフォーマンスを比較し、クエリ ストアによる報告を確認します。 何らかの後退が起こった場合は、クエリ ストアを使用して後退したクエリを識別し、クエリ ストアからプラン強制オプション(プランの固定) を使用できます。 このような場合は、互換性レベル 130 は変更せずに、クエリ ストアによって提案された前のクエリ プランを使用します。
* SQL Server 2016 で実行されるAzure SQL Database の新しい機能や能力を活用したいが、互換性レベル 130 で行われる変更が心配な場合は、最後の手段として、ALTER DATABASE ステートメントを使用して、ワークロードに適した互換性レベルに強制的に戻すことを検討できる可能性があります。 ただし、130 を使用しないことは、基本的には SQL Server の前のバージョンの機能レベルに留まることになるため、クエリ ストアのプラン固定オプションが最善のオプションになります。
* 複数のデータベースにまたがるマルチテナント アプリケーションがある場合は、すべてのデータベース (前のデータベースと新しくプロビジョニングされるデータベース) で一貫性のある互換性レベルが確保されるように、データベースのプロビジョニング ロジックの更新が必要になる可能性があります。 一部のデータベースが異なる互換性レベルで実行されているときに、常に同じエクスペリエンスを顧客に提供するためには、すべてのデータベースで一貫性のある互換性レベルが要求される可能性があるという事実によって、アプリケーションのワークロードのパフォーマンスが影響を受ける可能性があります。 これは常に当てはまることではなく、アプリケーションが互換性レベルによってどのような影響を受けるかに完全に依存しています。
* 最後に、基数推定については、運用環境でのワークロードを新しい条件下でテストして、アプリケーションが基数推定の機能強化によるメリットを得られるかどうかを検証することをお勧めします。

## <a name="conclusion"></a>まとめ
Azure SQL Database を使用して、すべての SQL Server 2016 の機能強化のメリットを得ることで、クエリ実行のパフォーマンスは明らかに向上します。 もちろん、すべての新しい機能と同じように、ご自身のデータベースのワークロードが正確にどのような条件下で最善の動作を行うかを判断するには、正しい評価を行う必要があります。 経験上、ほとんどのワークロードは、互換性レベル 130 では、新しいクエリ処理機能と新しい基数推定を活用しながら、少なくとも透過的に実行されることが期待されます。 ただし、現実的には、いくつかの例外が常に存在するため、適正な詳細調査を実施して、これらの機能強化からどの程度のメリットを得られるかを判断することが重要です。 その作業を実施するにあたって、やはりクエリ ストアが非常に役に立つ可能性があります。

## <a name="references"></a>参照
* [新機能 (データベース エンジン)](https://msdn.microsoft.com/library/bb510411.aspx#InMemory)
* [Blog: Query Store: A flight data recorder for your database, by Borko Novakovic, June 8 2016](https://azure.microsoft.com/blog/query-store-a-flight-data-recorder-for-your-database/)
* [ALTER TABLE 互換性レベル (Transact-SQL)](https://msdn.microsoft.com/library/bb510680.aspx)
* [ALTER DATABASE SCOPED CONFIGURATION](https://msdn.microsoft.com/library/mt629158.aspx)
* [Compatibility Level 130 for Azure SQL Database V12](https://azure.microsoft.com/updates/compatibility-level-130-for-azure-sql-database-v12/)
* [Optimizing Your Query Plans with the SQL Server 2014 Cardinality Estimator](https://msdn.microsoft.com/library/dn673537.aspx)
* [列ストア インデックスの説明](https://msdn.microsoft.com/library/gg492088.aspx)
* [Blog: Improved Query Performance with Compatibility Level 130 in Azure SQL Database, by Alain Lissoir, May 6 2016](https://blogs.msdn.microsoft.com/sqlserverstorageengine/2016/05/06/improved-query-performance-with-compatibility-level-130-in-azure-sql-database/)


