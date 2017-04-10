---
title: "HDInsight での Hadoop MapReduce サンプルの実行 | Microsoft Docs"
description: "HDInsight で MapReduce サンプルの使用を開始します。 SSH を使用してクラスターに接続し、Hadoop コマンドを使用してサンプル ジョブを実行します。"
services: hdinsight
documentationcenter: 
author: Blackmist
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: e1d2a0b9-1659-4fab-921e-4a8990cbb30a
ms.service: hdinsight
ms.custom: hdinsightactive
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/12/2017
ms.author: larryfr
translationtype: Human Translation
ms.sourcegitcommit: 4f2230ea0cc5b3e258a1a26a39e99433b04ffe18
ms.openlocfilehash: d94e633273ef298079673c100c6edbf95dc3c96d
ms.lasthandoff: 03/25/2017


---
# <a name="run-the-hadoop-samples-in-hdinsight"></a>HDInsight での Hadoop のサンプルの実行
[!INCLUDE [samples-selector](../../includes/hdinsight-run-samples-selector.md)]

HDInsight クラスターには、Hadoop MapReduce ジョブの実行について理解するのに使用できる MapReduce のサンプル セットが用意されています。 このドキュメントでは、使用可能なサンプルと一部のサンプルの実行手順について説明します。

## <a name="prerequisites"></a>前提条件


* **Linux ベースの HDInsight クラスター**: [Linux 上の HDInsight で Hive と Hadoop を使用する](hdinsight-hadoop-linux-tutorial-get-started.md)

  > [!IMPORTANT]
  > Linux は、バージョン 3.4 以上の HDInsight で使用できる唯一のオペレーティング システムです。 詳細については、[Window での HDInsight の廃止](hdinsight-component-versioning.md#hdi-version-32-and-33-nearing-deprecation-date)に関する記事を参照してください。

* **An SSH クライアント**: 詳細については、[HDInsight での SSH の使用](hdinsight-hadoop-linux-use-ssh-unix.md)に関するページを参照してください。

## <a name="the-samples"></a>サンプル
**場所**: サンプルは HDInsight クラスター上の **/usr/hdp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar** にあります。

**内容**: 次のサンプルがこのアーカイブに含まれています。

* **aggregatewordcount**: 入力ファイル内の単語をカウントする集計ベースの Map / Reduce プログラム
* **aggregatewordhist**: 入力ファイル内の単語のヒストグラムを計算する集計ベースの Map / Reduce プログラム
* **bbp**:  Bailey-Borwein-Plouffe を使用して Pi の正確な数字を計算する Map / Reduce プログラム
* **dbcount**: データベースに保存されているページビューのログの数をカウントするサンプル ジョブ
* **distbbp**: BBP 公式を使用して Pi の正確なビットを計算する Map / Reduce プログラム
* **grep**: 入力内の正規表現の一致項目をカウントする Map / Reduce プログラム
* **join**: 並べ替えられ、均等に分割されたデータセット上の結合に影響を及ぼすジョブ
* **multifilewc**: 複数のファイルから単語をカウントするジョブ
* **pentomino**: pentomino 問題のソリューションを見つける Map / Reduce プログラム
* **pi**: 準モンテカルロ法を使用して Pi を推定する Map / Reduce プログラム
* **randomtextwriter**: ノードあたり 10 GB のランダムなテキスト データを書き込む Map / Reduce プログラム
* **randomwriter**: ノードあたり 10 GB のランダムなデータを書き込む Map / Reduce プログラム
* **secondarysort**:  Reduce プログラムに 2 番目の並べ替えを定義する例
* **sort**: ランダム ライターによって書き込まれたデータを並べ替える Map / Reduce プログラム
* **sudoku**: 数独問題を解くプログラム
* **teragen**: terasort のデータを生成する
* **terasort**: terasort を実行する
* **teravalidate**: terasort の結果を確認する
* **wordcount**: 入力ファイル内の単語をカウントする Map / Reduce プログラム
* **wordmean**: 入力ファイル内の単語の平均長をカウントする Map / Reduce プログラム
* **wordmedian**: 入力ファイル内の単語の中央の長さをカウントする Map / Reduce プログラム
* **wordmedian**: 入力ファイル内の単語の長さの標準偏差をカウントする Map / Reduce プログラム

**ソース コード**: これらのサンプルのソース コードは、HDInsight クラスター上の **/usr/hdp/2.2.4.9-1/hadoop/src/hadoop-mapreduce-project/hadoop-mapreduce-examples** にあります。

> [!NOTE]
> `2.2.4.9-1` は、HDInsight クラスターの Hortonworks Data Platform のバージョンであり、HDInsight が更新されると変更される場合があります。
> 
> 

## <a name="how-to-run-the-samples"></a>サンプルの実行方法
1. SSH を使用して HDInsight に接続する。 詳細については、[HDInsight での SSH の使用](hdinsight-hadoop-linux-use-ssh-unix.md)に関するページを参照してください。

2. `username@#######:~$` プロンプトで、次のコマンドを使用してサンプルの一覧を表示します。
   
        yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar
   
    これにより、このドキュメントの前のセクションのサンプル一覧が生成されます。
3. 次のコマンドを使用して、特定のサンプルのヘルプを表示します。 これは、 **wordcount** サンプルです。
   
        yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar wordcount
   
    次のメッセージが表示されます。
   
        Usage: wordcount <in> [<in>...] <out>
   
    これは、ソース ドキュメントに対して複数の入力パスを指定できることを示しています。 最後のパスは、出力 (ソース ドキュメント内の単語の数) の保存場所です。
4. 次のコマンドを使用して、クラスタでサンプルデータとして提供されているレオナルド·ダ·ヴィンチの手記の単語をすべてカウントします。
   
        yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar wordcount /example/data/gutenberg/davinci.txt /example/data/davinciwordcount
   
    このジョブの入力は **wasbs:///example/data/gutenberg/davinci.txt** から読み取られます。
   
    この例の出力は **wasbs:///example/data/davinciwordcount** に格納されます。
   
   > [!NOTE]
   > wordcount サンプルのヘルプで説明したように、複数の入力ファイルを指定することもできます。 たとえば、 `hadoop jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar wordcount /example/data/gutenberg/davinci.txt /example/data/gutenberg/ulysses.txt /example/data/twowordcount` は davinci.txt と ulysses.txt の両方の単語をカウントします。
   > 
   > 
5. ジョブが完了したら、次のコマンドを使用して出力を表示します。
   
        hdfs dfs -cat /example/data/davinciwordcount/*
   
    これにより、ジョブで生成されたすべての出力ファイルが連結して表示されます。 この基本的な例では、1 つのファイルがあるだけですが、複数のファイルがある場合、このコマンドはそのすべてを反復処理します。
   
    次のように出力されます。
   
        zum     1
        zur     1
        zwanzig 1
        zweite  1
   
    各行は、単語と入力データで発生した回数を表します。

## <a name="sudoku"></a>sudoku
数独の例には、あまり役に立たない使用法の説明 "コマンド ラインにパズルを含める" があります。

[数独](https://en.wikipedia.org/wiki/Sudoku) は、9 つの 3 x 3 グリッドで構成される論理パズルです。 いくつかのグリッドのセルには数字がありますが、他のセルは空白になっています。空白のセルを解決するとゴールです。 パズルの詳細については、上記のリンクにありますが、このサンプルの目的は空白のセルを解決することです。 そのため、入力は次の形式のファイルである必要があります。

* 9 つの行と 9 つの列
* 各列には、数字または `?` (空白のセルを示す) を含めることができる
* セルはスペースで区切られる

1 つの数字を 1 つの列または行で繰り返すことができないという、数独パズルを作成する特定の方法があります。 HDInsight クラスターに正しく作成された例があります。 これは、 **/usr/hdp/2.2.4.9-1/hadoop/src/hadoop-mapreduce-project/hadoop-mapreduce-examples/src/main/java/org/apache/hadoop/examples/dancing/puzzle1.dta** にあり、次の内容が含まれています。

    8 5 ? 3 9 ? ? ? ?
    ? ? 2 ? ? ? ? ? ?
    ? ? 6 ? 1 ? ? ? 2
    ? ? 4 ? ? 3 ? 5 9
    ? ? 8 9 ? 1 4 ? ?
    3 2 ? 4 ? ? 8 ? ?
    9 ? ? ? 8 ? 5 ? ?
    ? ? ? ? ? ? 2 ? ?
    ? ? ? ? 4 5 ? 7 8

> [!NOTE]
> `2.2.4.9-1` の部分は、HDInsight クラスターが更新されると変更される場合があります。
> 
> 

数独の例でこれを実行するには、次のコマンドを使用します。

    yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar sudoku /usr/hdp/2.2.9.1-1/hadoop/src/hadoop-mapreduce-project/hadoop-mapreduce-examples/src/main/java/org/apache/hadoop/examples/dancing/puzzle1.dta

結果は次のようになります。

    8 5 1 3 9 2 6 4 7
    4 3 2 6 7 8 1 9 5
    7 9 6 5 1 4 3 8 2
    6 1 4 8 2 3 7 5 9
    5 7 8 9 6 1 4 2 3
    3 2 9 4 5 7 8 1 6
    9 4 7 2 8 6 5 3 1
    1 8 5 7 3 9 2 6 4
    2 6 3 1 4 5 9 7 8

## <a name="pi-"></a>Pi (π)
pi サンプルでは統計的手法 (準モンテカルロ法) に基づいて Pi の値を計算します。 単位正方形の内部にランダムに配置された点は、その正方形に内接する円の内部にも円の面積に等しい確率 (Pi/4) で配置されます。 Pi の値は 4R という値で計算されます。ここで R は、正方形の内部にある点の総数と、円の内部にある点の数との比率です。 サンプルの点の数が大きくなるほど、推定値の精度が上がります。

このサンプルの mapper は、単位正方形の内部にランダムに多数の点を生成し、円の内部にある点の数を計算します。

reducer は、mapper が計算した点の数を累計して、4R という公式から Pi の値を推定します。ここで R は、正方形の内部にある点の総数と、円の内部にある点の数との比率です。

次のコマンドを使用して、このサンプルを実行します。 これは、10,000, 000 のサンプルごとに 16 のマップを使用して、pi の値を推定します。

    yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar pi 16 10000000

これによって返される値は、 **3.14159155000000000000**になります。 参考までに、Pi の小数点以下 10 桁までは 3.1415926535 です。

## <a name="10gb-greysort"></a>10GB Greysort
GraySort はベンチマーク ソートで、その評価尺度は、非常に大量のデータ、通常は最低でも 100 TB のデータをソートした際のソート速度 (TB/分) です。

このサンプルでは、比較的高速に実行できるように、中程度のサイズの 10 GB のデータを使用します。 使用する MapReduce アプリケーションは Owen O'Malley と Arun Murthy が開発したもので、2009 年にはテラバイト ソート ベンチマークの汎用目的 ("daytona") 部門で 0.578 TB/分 (173 分で 100 TB) という年間記録を樹立しました。 これも含めたソート ベンチマークの詳細については、 [Sortbenchmark](http://sortbenchmark.org/) サイトを参照してください。

このサンプルでは 3 組の MapReduce プログラムを使用します。

* **TeraGen**: データ行を生成してソートする MapReduce プログラム
* **TeraSort**: 入力データをサンプリングし、MapReduce を使用してデータを合計順にソートする
  
    TeraSort は MapReduce 関数の標準ソートです。 特に、sample[i-1] <= key < sample[i] となるキーはすべて reduce i に送られます。 このため、reduce i の出力がすべて reduce i+1 の出力より小さくなることが保証されます。
* **TeraValidate**: 出力がグローバルにソートされていることを検証する MapReduce プログラム
  
    出力ディレクトリ内のファイルごとにマップを 1 つ作成します。各マップは各キーが前のキー以下であることを保証します。 map 関数は、各ファイルの最初のキーと最後のキーの記録も生成し、reduce 関数は、ファイル i の最初のキーがファイル i-1 の最後のキーよりも大きいことを確認します。 問題が見つかった場合は、reduce の出力として範囲外のキーがレポートされます。

次の手順を使用してデータを生成、ソートし、出力を検証します。

1. 10 GB のデータを生成します。データは、HDInsight クラスターの既定の記憶域 **wasbs:///example/data/10GB-sort-input** に格納されます。
   
        yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar teragen -Dmapred.map.tasks=50 100000000 /example/data/10GB-sort-input
   
    `-Dmapred.map.tasks` は、このジョブに使用する map タスクの数を Hadoop に伝えます。 最後の 2 つのパラメーターは、10 GB 分のデータを作成し、それを **wasbs:///example/data/10GB-sort-input** に格納するようにジョブに指示します。
2. 次のコマンドを使用して、データをソートします。
   
        yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar terasort -Dmapred.map.tasks=50 -Dmapred.reduce.tasks=25 /example/data/10GB-sort-input /example/data/10GB-sort-output
   
    `-Dmapred.reduce.tasks` は、このジョブに使用する reduce タスクの数を Hadoop に伝えます。 最後の 2 つのパラメーターは、単なるデータの入力と出力の場所です。
3. 次のコマンドを使用して、ソートによって生成されたデータを検証します。
   
        yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar teravalidate -Dmapred.map.tasks=50 -Dmapred.reduce.tasks=25 /example/data/10GB-sort-output /example/data/10GB-sort-validate

## <a name="next-steps"></a>次のステップ
この記事では、Linux ベースの HDInsight クラスターに付属するサンプルを実行する方法を説明しました。 HDInsight で Pig、Hive、および MapReduce を使用する方法のチュートリアルについては、次のトピックをご覧ください。

* [HDInsight での Pig と Hadoop の使用][hdinsight-use-pig]
* [HDInsight での Hive と Hadoop の使用][hdinsight-use-hive]
* [HDInsight での MapReduce と Hadoop の使用][hdinsight-use-mapreduce]

[hdinsight-errors]: hdinsight-debug-jobs.md
[hdinsight-use-mapreduce]: hdinsight-use-mapreduce.md
[hdinsight-sdk-documentation]: https://msdn.microsoft.com/library/azure/dn479185.aspx

[hdinsight-submit-jobs]: hdinsight-submit-hadoop-jobs-programmatically.md
[hdinsight-introduction]: hdinsight-hadoop-introduction.md



[hdinsight-samples]: hdinsight-run-samples.md

[hdinsight-use-hive]: hdinsight-use-hive.md
[hdinsight-use-pig]: hdinsight-use-pig.md

