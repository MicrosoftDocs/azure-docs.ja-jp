---
title: "Azure HDInsight での Hive クエリの最適化 |Microsoft ドキュメント"
description: "HDInsight で Hive クエリを最適化する方法について説明します。"
services: hdinsight
documentationcenter: 
author: mumian
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: d6174c08-06aa-42ac-8e9b-8b8718d9978e
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 04/26/2016
ms.author: jgao
ms.translationtype: Human Translation
ms.sourcegitcommit: 54b5b8d0040dc30651a98b3f0d02f5374bf2f873
ms.openlocfilehash: 7d269a5805da405e4e5f7a3caf5a58fa454b9abb
ms.contentlocale: ja-jp
ms.lasthandoff: 04/28/2017

---
# <a name="optimize-hive-queries-in-azure-hdinsight"></a>Azure HDInsight での Hive クエリの最適化

既定では、Hadoop クラスターのパフォーマンスは最適化されていません。 この記事では、クエリに適用できる最も一般的な Hive パフォーマンスの最適化方法について説明します。

## <a name="scale-out-worker-nodes"></a>ワーカー ノードのスケール アウト

クラスター内のノードのワーカーの数を増やすことで、より多くの mapper と reducer を同時に実行できるようになります。 HDInsight でのスケール アウトを向上させる方法が 2 つあります。

* プロビジョニング時に、Azure Portal、Azure PowerShell またはクロス プラットフォームのコマンド ライン インターフェイスを使用してワーカー ノードの数を指定できます。  詳細については、[HDInsight クラスターの作成](hdinsight-hadoop-provision-linux-clusters.md)に関するページを参照してください。 次のスクリーンショットは、Azure Portal 上に表示されたワーカー ノード構成を示しています。
  
    ![scaleout_1][image-hdi-optimize-hive-scaleout_1]
* 実行時に、クラスターを再作成せずにスケールアウトすることもできます。

    ![scaleout_1][image-hdi-optimize-hive-scaleout_2]

HDInsight によってサポートされているさまざまな仮想マシンの詳細については、「[HDInsight の価格](https://azure.microsoft.com/pricing/details/hdinsight/)」を参照してください。

## <a name="enable-tez"></a>Tez を有効にする

[Apache Tez](http://hortonworks.com/hadoop/tez/) は、MapReduce エンジンに代わる実行エンジンです。

![tez_1][image-hdi-optimize-hive-tez_1]

Tez はより高速です。それは次の理由によります。

* **MapReduce エンジンで、有向非巡回グラフ (DAG) を 1 つのジョブとして実行します**。 DAG では、mapper の各セットの後に 1 セットの reducer が続く必要があります。 これにより、複数の MapReduce ジョブが各 Hive クエリでスピンオフされます。 Tez にはこのような制約はありません。複雑な DAG を 1 つのジョブとして処理することができるため、ジョブのスタートアップのオーバーヘッドが最小限に抑えられます。
* **不要な書き込みを回避できます**。 MapReduce エンジンでは同じ Hive クエリに対して複数のジョブがスピンオフされるため、各ジョブの出力は中間データとして HDFS に書き込まれます。 Tez は各 Hive クエリのジョブの数を最小限に抑えるので、不要な書き込みを回避することができます。
* **起動時の遅延を最小限に抑えられます**。 Tez は、開始に必要な mapper の数を削減することによって、また全体的な最適化を向上させることによって、起動時の遅延を最小限に抑えることができます。
* **コンテナーを再利用できます**。 コンテナーの起動による待ち時間を軽減するため、可能なときは常にTez はコンテナーを再利用できます。
* **継続的な最適化手法を使用します**。 これまで、最適化はコンパイル フェーズで行われていました。 しかし最適化を向上させるための入力に関する詳細は、実行時に入手できます。 Tez は、実行時フェーズでプランをさらに最適化する継続的な最適化手法を使用します。

この概念の詳細については、[Apache TEZ](http://hortonworks.com/hadoop/tez/) のサイトを参照してください。

次の設定でクエリにプレフィックスを付けることで、Hive クエリで Tez を有効にできます。

    set hive.execution.engine=tez;

Linux ベースの HDInsight クラスターでは、Tez は既定で有効になっています。


## <a name="hive-partitioning"></a>Hive パーティション分割

I/O 操作は、Hive クエリを実行するための主なパフォーマンスのボトルネックです。 読み取る必要があるデータの量を削減することで、パフォーマンスを向上することができます。 既定では、Hive クエリは、全 Hive テーブルをスキャンします。 これはテーブル スキャンのようなクエリでは有効な方法ですが、 少量のデータのみのスキャンが必要なクエリ (フィルターを使用するクエリなど) では、不要なオーバーヘッドが発生します。 Hive パーティション分割では、Hive クエリは Hive テーブルの必要な量のデータだけにアクセスします。

Hive パーティション分割は、生データを新しいディレクトリに再構成することにより実装されます。その場合は、各パーティションに独自のディレクトリを用意します (パーティションは、ユーザーによって定義されます)。 次の図は、列 *Year* による Hive テーブルのパーティション分割を示しています。 年ごとに新しいディレクトリが作成されます。

![パーティション分割][image-hdi-optimize-hive-partitioning_1]

パーティション分割に関するいくつかの考慮事項:

* **パーティションの数を少なくしすぎない** - パーティション分割する列の値の種類が少ないと、パーティションの数が少なくなる場合があります。 たとえば、性別に基づいてパーティションを分割する場合、2 つのパーティション (男性と女性) しか作成されません。したがって、待ち時間の短縮は、最大でも半分にしかなりません。
* **パーティションの数を多くしすぎない**- その反対に、一意の値 (userid など) で列のパーティションを作成すると、複数のパーティションが作成されます。 これでは、多数のディレクトリを処理する必要があるため、クラスター namenode に多大なストレスを与えることになります。
* **データのスキューを回避する** - すべてのパーティションのサイズが均等になるよう、注意深くパーティショニング キーを選択します。 たとえば、*州*によってパーティションを分割すると、カリフォルニアの下のレコードの数がバーモントの約 30 倍になる可能性があります。これは人口が違うことによって生じます。

パーティション テーブルを作成するには、 *Partitioned By* 句を使用します。

    CREATE TABLE lineitem_part
        (L_ORDERKEY INT, L_PARTKEY INT, L_SUPPKEY INT,L_LINENUMBER INT,
         L_QUANTITY DOUBLE, L_EXTENDEDPRICE DOUBLE, L_DISCOUNT DOUBLE,
         L_TAX DOUBLE, L_RETURNFLAG STRING, L_LINESTATUS STRING,
         L_SHIPDATE_PS STRING, L_COMMITDATE STRING, L_RECEIPTDATE            STRING, L_SHIPINSTRUCT STRING, L_SHIPMODE STRING,
         L_COMMENT STRING)
    PARTITIONED BY(L_SHIPDATE STRING)
    ROW FORMAT DELIMITED FIELDS TERMINATED BY '\t'
    STORED AS TEXTFILE;

パーティション テーブルが作成されれば、静的パーティションまたは動的パーティションのいずれかを作成できるようになります。

* **静的パーティション分割** では、適切なディレクトリにシャード化データが既に存在し、ディレクトリの場所に基づいて Hive パーティションに手動で問い合わせができます。 次のコード スニペットに例を示します。
  
        INSERT OVERWRITE TABLE lineitem_part
        PARTITION (L_SHIPDATE = ‘5/23/1996 12:00:00 AM’)
        SELECT * FROM lineitem 
        WHERE lineitem.L_SHIPDATE = ‘5/23/1996 12:00:00 AM’
  
        ALTER TABLE lineitem_part ADD PARTITION (L_SHIPDATE = ‘5/23/1996 12:00:00 AM’))
        LOCATION ‘wasb://sampledata@ignitedemo.blob.core.windows.net/partitions/5_23_1996/'
* **動的パーティション分割** では、Hive に自動的にパーティションを作成させます。 ステージング テーブルからパーティショニング テーブルを作成したので、あとはパーティション テーブルにデータを挿入するだけです。
  
        SET hive.exec.dynamic.partition = true;
        SET hive.exec.dynamic.partition.mode = nonstrict;
        INSERT INTO TABLE lineitem_part
        PARTITION (L_SHIPDATE)
        SELECT L_ORDERKEY as L_ORDERKEY, L_PARTKEY as L_PARTKEY , 
             L_SUPPKEY as L_SUPPKEY, L_LINENUMBER as L_LINENUMBER,
              L_QUANTITY as L_QUANTITY, L_EXTENDEDPRICE as L_EXTENDEDPRICE,
             L_DISCOUNT as L_DISCOUNT, L_TAX as L_TAX, L_RETURNFLAG as           L_RETURNFLAG, L_LINESTATUS as L_LINESTATUS, L_SHIPDATE as           L_SHIPDATE_PS, L_COMMITDATE as L_COMMITDATE, L_RECEIPTDATE as      L_RECEIPTDATE, L_SHIPINSTRUCT as L_SHIPINSTRUCT, L_SHIPMODE as      L_SHIPMODE, L_COMMENT as L_COMMENT, L_SHIPDATE as L_SHIPDATE FROM lineitem;

詳細については、「[パーティション テーブル](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+DDL#LanguageManualDDL-PartitionedTables)」を参照してください。

## <a name="use-the-orcfile-format"></a>ORCFile 形式の使用
Hive は、さまざまなファイル形式をサポートしています。 次に例を示します。

* **テキスト**: これは既定のファイル形式で、ほとんどのシナリオで使用できます
* **Avro**: 相互運用性シナリオで使用できます
* **ORC/Parquet**: 最も高いパフォーマンスを発揮します

ORC (最適化行多桁式) 形式は、Hive データを格納する非常に効率的な方法です。 他の形式と比べて、ORC には次の利点があります。

* 複合型 (DateTime、複合構造化型、および半構造化型を含む) のサポート
* 最大で 70% の圧縮
* 10,000 行ごとのインデックス作成 (これにより行のスキップが可能)
* 実行時の実行の大幅な削除

ORC 形式を有効にするにはまず、 *Stored as ORC*句でテーブルを作成します。

    CREATE TABLE lineitem_orc_part
        (L_ORDERKEY INT, L_PARTKEY INT,L_SUPPKEY INT, L_LINENUMBER INT,
         L_QUANTITY DOUBLE, L_EXTENDEDPRICE DOUBLE, L_DISCOUNT DOUBLE,
         L_TAX DOUBLE, L_RETURNFLAG STRING, L_LINESTATUS STRING,
         L_SHIPDATE_PS STRING, L_COMMITDATE STRING, L_RECEIPTDATE STRING,
         L_SHIPINSTRUCT STRING, L_SHIPMODE STRING, L_COMMENT      STRING)
    PARTITIONED BY(L_SHIPDATE STRING)
    STORED AS ORC;

次に、データをステージング テーブルから ORC テーブルに挿入します。 次に例を示します。

    INSERT INTO TABLE lineitem_orc
    SELECT L_ORDERKEY as L_ORDERKEY, 
           L_PARTKEY as L_PARTKEY , 
           L_SUPPKEY as L_SUPPKEY,
           L_LINENUMBER as L_LINENUMBER,
            L_QUANTITY as L_QUANTITY, 
           L_EXTENDEDPRICE as L_EXTENDEDPRICE,
           L_DISCOUNT as L_DISCOUNT,
           L_TAX as L_TAX,
           L_RETURNFLAG as L_RETURNFLAG,
           L_LINESTATUS as L_LINESTATUS,
           L_SHIPDATE as L_SHIPDATE,
           L_COMMITDATE as L_COMMITDATE,
           L_RECEIPTDATE as L_RECEIPTDATE, 
           L_SHIPINSTRUCT as L_SHIPINSTRUCT,
           L_SHIPMODE as L_SHIPMODE,
           L_COMMENT as L_COMMENT
    FROM lineitem;

ORC 形式については、 [ここ](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+ORC)を参照してください。

## <a name="vectorization"></a>ベクター化

ベクター化により、Hive は、一度に 1 行を処理する代わりに、1024 行を一括処理することができます。 つまり、単純な操作では、実行に必要な内部コードが少なくなるため、処理が速くなります。

Hive クエリのベクター化プレフィックスを有効にするには、次の設定を使用します。

    set hive.vectorized.execution.enabled = true;

詳細については、「[Vectorized query execution (ベクター化されたクエリ実行)](https://cwiki.apache.org/confluence/display/Hive/Vectorized+Query+Execution)」を参照してください。

## <a name="other-optimization-methods"></a>その他の最適化の方法
その他にも考慮できる最適化の方法がいくつかあります。たとえば、次のような方法です。

* **Hive のバケット:** 大きなデータ セットをクラスター化またはセグメント化してクエリのパフォーマンスを最適化するための手法です。
* **結合の最適化:** 結合の効率を向上させユーザー ヒントの必要性を少なくするための Hive のクエリ実行プランの最適化です。 詳しくは、「[Join optimization (結合の最適化)](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+JoinOptimization#LanguageManualJoinOptimization-JoinOptimization)」を参照してください。
* **Reducer の増加**。

## <a name="next-steps"></a>次のステップ
この記事ではいくつかの一般的な Hive クエリの最適化方法を説明しました。 詳細については、次の記事を参照してください。

* [HDInsight での Apache Hive の使用](hdinsight-use-hive.md)
* [HDInsight での Hive を使用したフライトの遅延データの分析](hdinsight-analyze-flight-delay-data.md)
* [HDInsight での Hive を使用した Twitter データの分析](hdinsight-analyze-twitter-data.md)
* [HDInsight での Hadoop で Hive クエリ コンソールを使用したセンサー データの分析](hdinsight-hive-analyze-sensor-data.md)
* [Web サイトのログを分析するための HDInsight での Hive の使用](hdinsight-hive-analyze-website-log.md)

[image-hdi-optimize-hive-scaleout_1]: ./media/hdinsight-hadoop-optimize-hive-query/scaleout_1.png
[image-hdi-optimize-hive-scaleout_2]: ./media/hdinsight-hadoop-optimize-hive-query/scaleout_2.png
[image-hdi-optimize-hive-tez_1]: ./media/hdinsight-hadoop-optimize-hive-query/tez_1.png
[image-hdi-optimize-hive-partitioning_1]: ./media/hdinsight-hadoop-optimize-hive-query/partitioning_1.png

