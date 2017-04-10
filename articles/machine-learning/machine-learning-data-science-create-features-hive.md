---
title: "Hive クエリを使用して Hadoop クラスターのデータの特徴を作成する | Microsoft Docs"
description: "Azure HDInsight Hadoop クラスターに格納されているデータの特徴を生成する Hive クエリの例を紹介します。"
services: machine-learning
documentationcenter: 
author: bradsev
manager: jhubbard
editor: cgronlun
ms.assetid: e8a94c71-979b-4707-b8fd-85b47d309a30
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/24/2017
ms.author: hangzh;bradsev
translationtype: Human Translation
ms.sourcegitcommit: 503f5151047870aaf87e9bb7ebf2c7e4afa27b83
ms.openlocfilehash: 5a4ca11079ac2a3962d92c7688e8d7337c31389d
ms.lasthandoff: 03/29/2017


---
# <a name="create-features-for-data-in-an-hadoop-cluster-using-hive-queries"></a>Hive クエリを使用して Hadoop クラスターのデータの特徴を作成する
このドキュメントでは、Hive クエリを使用して、Azure HDInsight Hadoop クラスターに格納されているデータの特徴を作成する方法について説明します。 これらの Hive クエリでは、埋め込みの Hive のユーザー定義関数 (UDF) を使用します。また、そのスクリプトも用意されています。

特徴を作成するために必要な操作は、メモリの消費が激しい場合があります。 そのようなケースでは、Hive クエリのパフォーマンスが特に重要となります。Hive クエリのパフォーマンスは、特定のパラメーターをチューニングすることで高めることが可能です。 これらのパラメーターのチューニングについては最後のセクションで取り上げます。

また、[GitHub リポジトリ](https://github.com/Azure/Azure-MachineLearning-DataScience/tree/master/Misc/DataScienceProcess/DataScienceScripts)にも、[NYC タクシー乗車データ](http://chriswhong.com/open-data/foil_nyc_taxi/)のシナリオに固有のクエリの例が用意されています。 これらのクエリには、指定されたデータ スキーマが既にあり、すぐに送信して実行できる状態になっています。 最後のセクションでは、Hive クエリのパフォーマンスを向上させるためにユーザーが調整できるパラメーターについても説明します。

[!INCLUDE [cap-create-features-data-selector](../../includes/cap-create-features-selector.md)]

この **メニュー** は、多様な環境のデータの特徴を作成する方法が説明されたトピックにリンクされています。 このタスクは、 [Team Data Science Process (TDSP)](https://azure.microsoft.com/documentation/learning-paths/cortana-analytics-process/)の 1 ステップです。

## <a name="prerequisites"></a>前提条件
この記事では、以下のことを前提としています。

* Azure のストレージ アカウントが作成されている。 手順については、「[Azure ストレージ アカウントの作成](../storage/storage-create-storage-account.md#create-a-storage-account)」をご覧ください。
* HDInsight サービスでカスタマイズされた Hadoop クラスターがプロビジョニングされている。  手順については、「 [Advanced Analytics Process and Technology 向けに Azure HDInsight Hadoop クラスターをカスタマイズする](machine-learning-data-science-customize-hadoop-cluster.md)」をご覧ください。
* データが Azure HDInsight Hadoop クラスターの Hive テーブルにアップロードされている。 アップロードされていない場合は、まず「 [データを作成して Hive テーブルに読み込む](machine-learning-data-science-move-hive-tables.md) 」に従って Hive テーブルにデータをアップロードします。
* クラスターへのリモート アクセスが有効になっている。 手順については、「 [Hadoop クラスターのヘッド ノードへのアクセス](machine-learning-data-science-customize-hadoop-cluster.md#headnode)」をご覧ください。

## <a name="hive-featureengineering"></a>特徴の生成
このセクションでは、Hive クエリを使用して特徴を生成する方法について、いくつかの例を挙げて説明します。 追加の特徴を生成すると、既存のテーブルに列として追加するか、追加の特徴と主キーを持つ新しいテーブルを作成して元のテーブルと結合することができます。 次の例について説明します。

1. [頻度ベースの特徴の生成](#hive-frequencyfeature)
2. [二項分類におけるカテゴリ変数のリスク](#hive-riskfeature)
3. [[Datetime] フィールドからの特徴の抽出](#hive-datefeatures)
4. [[Text] フィールドからの特徴の抽出](#hive-textfeatures)
5. [GPS 座標間の距離の計算](#hive-gpsdistance)

### <a name="hive-frequencyfeature"></a>頻度ベースの特徴の生成
カテゴリ変数のレベルの出現頻度、または複数のカテゴリ変数のレベルの特定の組み合わせの出現頻度を計算することが役立つことがよくあります。 これらの頻度を計算するには、次のスクリプトを使用できます。

        select
            a.<column_name1>, a.<column_name2>, a.sub_count/sum(a.sub_count) over () as frequency
        from
        (
            select
                <column_name1>,<column_name2>, count(*) as sub_count
            from <databasename>.<tablename> group by <column_name1>, <column_name2>
        )a
        order by frequency desc;


### <a name="hive-riskfeature"></a>二項分類におけるカテゴリ変数のリスク
二項分類では、使用中のモデルが数値の特徴のみを処理する場合、数値以外の分類変数を数値の特徴に変換する必要があります。 これを行うには、各数値以外のレベルを数値のリスクに置き換えます。 このセクションでは、カテゴリ変数のリスクの値 (対数オッズ) を計算するいくつかの汎用 Hive クエリについて説明します。

        set smooth_param1=1;
        set smooth_param2=20;
        select
            <column_name1>,<column_name2>,
            ln((sum_target+${hiveconf:smooth_param1})/(record_count-sum_target+${hiveconf:smooth_param2}-${hiveconf:smooth_param1})) as risk
        from
            (
            select
                <column_nam1>, <column_name2>, sum(binary_target) as sum_target, sum(1) as record_count
            from
                (
                select
                    <column_name1>, <column_name2>, if(target_column>0,1,0) as binary_target
                from <databasename>.<tablename>
                )a
            group by <column_name1>, <column_name2>
            )b

この例では、データから計算されたリスク値をスムースにするため、変数 `smooth_param1` と `smooth_param2` が設定されています。 リスクの範囲は -Inf から Inf までです。 リスク > 0 は、ターゲット = 1 である確率が 0.5 よりも大きいことを示します。

リスクのテーブルが計算されると、リスクの値をリスクのテーブルと結合して、リスクの値をテーブルに割り当てることができます。 Hive 結合クエリは、前のセクションで用意されました。

### <a name="hive-datefeatures"></a>[Datetime] フィールドからの特徴の抽出
Hive には、[datetime] フィールドを処理するための UDF のセットが付属します。 Hive では、既定の datetime 形式は 'yyyy-MM-dd 00:00:00' (例: '1970-01-01 12:21:32') です。 このセクションでは、[datetime] フィールドから日と月を抽出する例、および既定の形式以外の datetime 文字列を既定の形式の datetime 文字列に変換する例を示します。

        select day(<datetime field>), month(<datetime field>)
        from <databasename>.<tablename>;

この Hive クエリは、*&#60;datetime フィールド>* が既定の datetime 形式であることを前提としています。

[datetime] フィールドが既定の形式でない場合、まず [datetime] フィールドを Unix のタイムスタンプに変換してから、Unix のタイムスタンプを既定の形式の datetime 文字列に変換する必要があります。 datetime が既定の形式の場合は、特徴を抽出するために組み込みの datetime ユーザー定義関数を適用することができます。

        select from_unixtime(unix_timestamp(<datetime field>,'<pattern of the datetime field>'))
        from <databasename>.<tablename>;

このクエリでは、*&#60;datetime フィールド>* が *03/26/2015 12:04:39* のようなパターンであった場合、*'&#60;datetime フィールドのパターン>'* は `'MM/dd/yyyy HH:mm:ss'` である必要があります。 これをテストするために、ユーザーは次を実行できます。

        select from_unixtime(unix_timestamp('05/15/2015 09:32:10','MM/dd/yyyy HH:mm:ss'))
        from hivesampletable limit 1;

このクエリの *hivesampletable* は、クラスターがプロビジョニングされるときに、既定ですべての Azure HDInsight Hadoop クラスターにプレインストールされます。

### <a name="hive-textfeatures"></a>[Text] フィールドからの特徴の抽出
Hive テーブルに、スペースで区切られた単語から成る文字列を含む [Text] フィールドがある場合、次のクエリは、文字列の長さと文字列内の単語数を抽出します。

        select length(<text field>) as str_len, size(split(<text field>,' ')) as word_num
        from <databasename>.<tablename>;

### <a name="hive-gpsdistance"></a>GPS 座標のセット間の距離の計算
このセクションで指定されたクエリは、NYC タクシー乗車データに直接適用できます。 このクエリの目的は、特徴を生成する Hive の組み込みの数学関数を適用する方法を示すことです。

このクエリで使用されているフィールドは、*pickup\_longitude*、*pickup\_latitude*、*dropoff\_longitude*、*dropoff\_latitude* という名前の乗車 (pickup) と降車 (dropoff) の位置を示す GPS 座標です。 pickup 座標と dropoff 座標間の直線距離を計算するクエリは次のとおりです。

        set R=3959;
        set pi=radians(180);
        select pickup_longitude, pickup_latitude, dropoff_longitude, dropoff_latitude,
            ${hiveconf:R}*2*2*atan((1-sqrt(1-pow(sin((dropoff_latitude-pickup_latitude)
            *${hiveconf:pi}/180/2),2)-cos(pickup_latitude*${hiveconf:pi}/180)
            *cos(dropoff_latitude*${hiveconf:pi}/180)*pow(sin((dropoff_longitude-pickup_longitude)*${hiveconf:pi}/180/2),2)))
            /sqrt(pow(sin((dropoff_latitude-pickup_latitude)*${hiveconf:pi}/180/2),2)
            +cos(pickup_latitude*${hiveconf:pi}/180)*cos(dropoff_latitude*${hiveconf:pi}/180)*
            pow(sin((dropoff_longitude-pickup_longitude)*${hiveconf:pi}/180/2),2))) as direct_distance
        from nyctaxi.trip
        where pickup_longitude between -90 and 0
        and pickup_latitude between 30 and 90
        and dropoff_longitude between -90 and 0
        and dropoff_latitude between 30 and 90
        limit 10;

2 つの GPS 座標の距離を計算する方程式は、Peter Lapisu による <a href="http://www.movable-type.co.uk/scripts/latlong.html" target="_blank">Movable Type Scripts</a> サイトにあります。 彼の Javascript で、関数 `toRad()` は単に *lat_or_lon*pi/180* であり、これは、角度をラジアンに変換します。 ここで、*lat_or_lon* は緯度または経度です。 Hive には関数 `atan2` はありませんが関数 `atan` はあるので、上記の Hive クエリでは、`atan2` 関数は <a href="http://en.wikipedia.org/wiki/Atan2" target="_blank">Wikipedia</a> に記載された定義を使用して、`atan` 関数により実装されています。

![ワークスペースの作成](./media/machine-learning-data-science-create-features-hive/atan2new.png)

Hive の組み込み UDF のリストは、<a href="https://cwiki.apache.org/confluence/display/Hive/LanguageManual+UDF#LanguageManualUDF-MathematicalFunctions" target="_blank">Apache Hive Wiki</a> の**組み込み関数**のセクションにあります。  

## <a name="tuning"></a> 高度なトピック: Hive パラメーターを調整してクエリ速度を向上させる
Hive クラスターの既定のパラメーター設定は、Hive クエリおよびクエリが処理するデータに適していないことがあります。 このセクションでは、Hive クエリのパフォーマンスを向上させるためにユーザーが調整できるパラメーターのいくつかについて説明します。 ユーザーは、データ処理のクエリの前に、パラメーター調整クエリを追加する必要があります。

1. **Java ヒープ スペース**: 大規模なデータセットの結合または長いレコードの処理を伴うクエリの場合、一般的なエラーの 1 つに、**ヒープ領域の不足**があります。 これは、パラメーター *mapreduce.map.java.opts* と *mapreduce.task.io.sort.mb* を必要な値に設定して調整できます。 たとえば次のようになります。
   
        set mapreduce.map.java.opts=-Xmx4096m;
        set mapreduce.task.io.sort.mb=-Xmx1024m;

    このパラメーターでは、Java ヒープ スペースに 4 GB のメモリが割り当てられ、より多くのメモリを割り当てることで並べ替えも効率化しています。 ヒープ スペース関連のジョブ失敗のエラーが発生する場合、これらの割り当てを試してみるとよいでしょう。

1. **DFS ブロック サイズ**: このパラメーターでは、ファイル システムに保存されるデータの最小単位を設定します。 たとえば、DFS ブロック サイズが 128 MB の場合、サイズが 128 MB 未満のデータは 1 つのブロックに保存されますが、128 MB を超えるデータでは追加のブロックが割り当てられます。 非常に小さいブロック サイズを選ぶと、 Hadoop に大きいオーバーヘッドが生じます。これは、NameNode がファイルに付随する関連ブロックを検索するためにさらに多くの要求を処理する必要があるためです。 ギガバイト (またはそれ以上) のデータを処理する場合に推奨される設定は、次のとおりです。
   
        set dfs.block.size=128m;
2. **Hive で結合操作を最適化する** : マップ/縮小フレームワークでの結合操作は通常縮小フェーズで発生しますが、マップ フェーズ ("mapjoins" と呼ばれることもあります) で結合をスケジュールすることで大幅な向上を実現できる場合があります。 これをいつでも実行できる場合にハイブを設定するには、次のように設定できます。
   
        set hive.auto.convert.join=true;
3. **Hive にマッパーの数を指定する**: Hadoop ではユーザーがレジューサの数を設定できますが、マッパーの数をユーザーが設定することは、通常はできません。 Hadoop 変数 (*mapred.min.split.size* と *mapred.max.split.size*) を選択すると、この数をある程度制御できます。これは、各マップ タスクのサイズが次の式によって決定されるためです。
   
        num_maps = max(mapred.min.split.size, min(mapred.max.split.size, dfs.block.size))
   
    通常、*mapred.min.split.size* の既定値は 0 で、*mapred.max.split.size* の既定値は **Long.MAX** で、*dfs.block.size* の既定値は 64 MB です。 お分かりのとおり、指定されたデータのサイズでは、これらのパラメーターを「設定」して調整すると、使用するマッパーの数を調整できるようになります。
4. Hive のパフォーマンスを最適化する他の **高度なオプション** を次に示します。 これらのオプションにより、タスクをマップおよび削減するためのメモリの割り当てを設定でき、パフォーマンスの調整に役立ちます。 *mapreduce.reduce.memory.mb* は、Hadoop クラスターの各 worker ノードの物理メモリのサイズを超えることはできないことに注意してください。
   
        set mapreduce.map.memory.mb = 2048;
        set mapreduce.reduce.memory.mb=6144;
        set mapreduce.reduce.java.opts=-Xmx8192m;
        set mapred.reduce.tasks=128;
        set mapred.tasktracker.reduce.tasks.maximum=128;


