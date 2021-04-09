---
title: HDInsight 上で Apache Hadoop の MapReduce サンプルを実行する - Azure
description: HDInsight に含まれる jar ファイルの MapReduce のサンプルを使用します。 SSH を使用してクラスターに接続し、Hadoop コマンドを使用してサンプル ジョブを実行します。
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive,hdiseo17may2017
ms.date: 12/12/2019
ms.openlocfilehash: 5e75a39b1f9e8503097914b0c9e735915f9ae667
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "98943211"
---
# <a name="run-the-mapreduce-examples-included-in-hdinsight"></a>HDInsight に含まれる MapReduce サンプルを実行する

[!INCLUDE [samples-selector](../../../includes/hdinsight-run-samples-selector.md)]

HDInsight 上の Apache Hadoop に含まれている MapReduce サンプルを実行する方法を説明します。

## <a name="prerequisites"></a>前提条件

* HDInsight の Apache Hadoop クラスター。 [Linux での HDInsight の概要](./apache-hadoop-linux-tutorial-get-started.md)に関するページを参照してください。

* SSH クライアント 詳細については、[SSH を使用して HDInsight (Apache Hadoop) に接続する方法](../hdinsight-hadoop-linux-use-ssh-unix.md)に関するページを参照してください。

## <a name="the-mapreduce-examples"></a>MapReduce サンプル

サンプルは次の HDInsight クラスターに置かれています。`/usr/hdp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar` これらのサンプルのソース コードは、次の HDInsight クラスターに含まれています。`/usr/hdp/current/hadoop-client/src/hadoop-mapreduce-project/hadoop-mapreduce-examples`

次のサンプルがこのアーカイブに含まれています。

|サンプル |説明 |
|---|---|
|aggregatewordcount|入力ファイル内の単語をカウントします。|
|aggregatewordhist|入力ファイル内の単語のヒストグラムを計算します。|
|bbp|Bailey-Borwein-Plouffe を使用して Pi の正確な数値を計算します。|
|dbcount|データベースに保存されているページビューのログをカウントします。|
|distbbp|BBP 公式を使用して Pi の正確なビットを計算します。|
|grep|入力内の正規表現の一致をカウントします。|
|join|並べ替えられ、均等に分割されたデータセットの結合を実行します。|
|multifilewc|複数のファイルからの単語をカウントします。|
|pentomino|pentomino 問題のソリューションを見つける、タイル並べのプログラム。|
|pi|準モンテカルロ法を使用して Pi を推定します。|
|randomtextwriter|ノードあたり 10 GB のランダムなテキスト データを書き込みます。|
|randomwriter|ノードあたり 10 GB のランダムなデータを書き込みます。|
|secondarysort|Reduce フェーズに対する 2 番目の並べ替えを定義します。|
|sort|ランダム ライターによって書き込まれたデータを並べ替えます。|
|sudoku|数独問題を解くプログラム。|
|teragen|terasort のデータを生成します。|
|terasort|terasort を実行します。|
|teravalidate|terasort の結果をチェックします。|
|wordcount|入力ファイル内の単語をカウントします。|
|wordmean|入力ファイル内の単語の長さの平均をカウントします。|
|wordmedian|入力ファイル内の単語の長さの中央値をカウントします。|
|wordstandarddeviation|入力ファイル内の単語の長さの標準偏差をカウントします。|

## <a name="run-the-wordcount-example"></a>Wordcount 例の実行

1. SSH を使用して HDInsight に接続する。 `CLUSTER` をクラスターの名前に置き換えてから、次のコマンドを入力します。

    ```cmd
    ssh sshuser@CLUSTER-ssh.azurehdinsight.net
    ```

2. SSH セッションから、次のコマンドを使用してサンプルを一覧表示します。

    ```bash
    yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar
    ```

    このコマンドは、このドキュメントの前のセクションに示したサンプルの一覧を生成します。

3. 次のコマンドを使用して、特定のサンプルのヘルプを表示します。 これは、 **wordcount** サンプルです。

    ```bash
    yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar wordcount
    ```

    次のメッセージが表示されます。

    ```output
    Usage: wordcount <in> [<in>...] <out>
    ```

    このメッセージは、ソース ドキュメントの複数の入力パスを指定できることを示しています。 最後のパスは、出力 (ソース ドキュメント内の単語の数) の保存場所です。

4. 次のコマンドを使用して、レオナルド ダ ヴィンチの手記 (クラスターで提供されているサンプル データ) に含まれるすべての単語をカウントします。

    ```bash
    yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar wordcount /example/data/gutenberg/davinci.txt /example/data/davinciwordcount
    ```

    このジョブに対する入力は、`/example/data/gutenberg/davinci.txt` から読み取られます。 この例の出力は、`/example/data/davinciwordcount` に格納されます。 両方のパスは、ローカル ファイル システムではなく、クラスターの既定のストレージに配置されます。

   > [!NOTE]  
   > wordcount サンプルのヘルプで説明したように、複数の入力ファイルを指定することもできます。 たとえば、 `hadoop jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar wordcount /example/data/gutenberg/davinci.txt /example/data/gutenberg/ulysses.txt /example/data/twowordcount` は davinci.txt と ulysses.txt の両方の単語をカウントします。

5. ジョブが完了したら、次のコマンドを使用して出力を表示します。

    ```bash
    hdfs dfs -cat /example/data/davinciwordcount/*
    ```

    このコマンドは、ジョブによって生成されたすべての出力ファイルを連結します。 コンソールに出力を表示します。 出力は次のテキストのようになります。

    ```output
    zum     1
    zur     1
    zwanzig 1
    zweite  1
    ```

    各行は、単語と入力データで発生した回数を表します。

## <a name="the-sudoku-example"></a>数独の例

[数独](https://en.wikipedia.org/wiki/Sudoku) は、9 つの 3 x 3 グリッドで構成される論理パズルです。 いくつかのグリッドのセルには数字がありますが、他のセルは空白になっています。空白のセルを解決するとゴールです。 パズルの詳細については前のリンクで確認できますが、このサンプルの目的は空白のセルを解決することです。 そのため、入力は次の形式のファイルである必要があります。

* 9 つの行と 9 つの列
* 各列には、数字または `?` (空白のセルを示す) を含めることができる
* セルはスペースで区切られる

1 つの数字を 1 つの列または行で繰り返すことができないという、数独パズルを作成する特定の方法があります。 HDInsight クラスターに正しく作成された例があります。 それは `/usr/hdp/*/hadoop/src/hadoop-mapreduce-project/hadoop-mapreduce-examples/src/main/java/org/apache/hadoop/examples/dancing/puzzle1.dta` に配置され、次のテキストを含んでいます。

```output
8 5 ? 3 9 ? ? ? ?
? ? 2 ? ? ? ? ? ?
? ? 6 ? 1 ? ? ? 2
? ? 4 ? ? 3 ? 5 9
? ? 8 9 ? 1 4 ? ?
3 2 ? 4 ? ? 8 ? ?
9 ? ? ? 8 ? 5 ? ?
? ? ? ? ? ? 2 ? ?
? ? ? ? 4 5 ? 7 8
```

この例の問題を数独の例で実行するには、次のコマンドを使用します。

```bash
yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar sudoku /usr/hdp/*/hadoop/src/hadoop-mapreduce-project/hadoop-mapreduce-examples/src/main/java/org/apache/hadoop/examples/dancing/puzzle1.dta
```

結果は次のテキストのようになります。

```output
8 5 1 3 9 2 6 4 7
4 3 2 6 7 8 1 9 5
7 9 6 5 1 4 3 8 2
6 1 4 8 2 3 7 5 9
5 7 8 9 6 1 4 2 3
3 2 9 4 5 7 8 1 6
9 4 7 2 8 6 5 3 1
1 8 5 7 3 9 2 6 4
2 6 3 1 4 5 9 7 8
```

## <a name="pi--example"></a>Pi (π) の例

pi サンプルでは統計的手法 (準モンテカルロ法) に基づいて Pi の値を計算します。 単位正方形の中に点がランダムに配置されます。 正方形には円も含まれています。 点が円の内部にある確率は、円の面積に等しい pi/4 です。 pi の値は 4R という値から推定されます。 R は、正方形の内部にある点の総数と、円の内部にある点の数との比率です。 サンプルの点の数が大きくなるほど、推定値の精度が上がります。

次のコマンドを使用して、このサンプルを実行します。 このコマンドは、それぞれに 10,000, 000 のサンプルがある 16 のマップを使用して、pi の値を推定します。

```bash
yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar pi 16 10000000
```

このコマンドによって返される値は、**3.14159155000000000000** です。 参考までに、Pi の小数点以下 10 桁までは 3.1415926535 です。

## <a name="10-gb-graysort-example"></a>10 GB GraySort の例

GraySort はベンチマーク ソートです。 その評価尺度は、大量のデータ (通常は最低でも 100 TB のデータ) をソートした際のソート速度 (TB/分) です。

このサンプルでは、比較的高速に実行できるように、中程度のサイズの 10 GB のデータを使用します。 使用する MapReduce アプリケーションは、Owen O'Malley と Arun Murthy によって開発されたものです。 これらのアプリケーションは、2009 年にテラバイト ソート ベンチマークの汎用目的 ("Daytona") 部門で 0.578 TB/分 (173 分で 100 TB) という年間記録を樹立しました。 これも含めたソート ベンチマークの詳細については、 [Sort Benchmark](https://sortbenchmark.org/) サイトを参照してください。

このサンプルでは 3 組の MapReduce プログラムを使用します。

* **TeraGen**: データ行を生成してソートする MapReduce プログラム

* **TeraSort**: 入力データをサンプリングし、MapReduce を使用してデータを合計順にソートする

    TeraSort は、カスタム パーティショナーを除けば、標準的な MapReduce ソートです。 このパーティショナーは、各 reduce のキー範囲を定義する N-1 サンプル キーの並べ替えられた一覧を使用します。 特に、sample[i-1] <= key < sample[i] となるキーはすべて reduce i に送られます。 このパーティショナーでは、reduce i の出力がすべて reduce i+1 の出力より小さくなることが保証されます。

* **TeraValidate**: 出力がグローバルにソートされていることを検証する MapReduce プログラム

    出力ディレクトリ内のファイルごとにマップを 1 つ作成します。各マップは各キーが前のキー以下であることを保証します。 Map 関数は、各ファイルの最初と最後のキーのレコードを生成します。 Reduce 関数は、ファイル i の最初のキーがファイル i-1 の最後のキーよりも大きいことを保証します。 問題が見つかった場合は、Reduce フェーズの出力として報告され、順序が間違っているキーが報告されます。

次の手順を使用してデータを生成、ソートし、出力を検証します。

1. 10 GB のデータを生成します。データは、HDInsight クラスターの既定のストレージ (`/example/data/10GB-sort-input`) に格納されます。

    ```bash
    yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar teragen -Dmapred.map.tasks=50 100000000 /example/data/10GB-sort-input
    ```

    `-Dmapred.map.tasks` は、このジョブに使用する map タスクの数を Hadoop に伝えます。 最後の 2 つのパラメーターは、10 GB のデータを作成し、それを `/example/data/10GB-sort-input` に格納するようにジョブに指示します。

2. 次のコマンドを使用して、データをソートします。

    ```bash
    yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar terasort -Dmapred.map.tasks=50 -Dmapred.reduce.tasks=25 /example/data/10GB-sort-input /example/data/10GB-sort-output
    ```

    `-Dmapred.reduce.tasks` は、このジョブに使用する reduce タスクの数を Hadoop に伝えます。 最後の 2 つのパラメーターは、単なるデータの入力と出力の場所です。

3. 次のコマンドを使用して、ソートによって生成されたデータを検証します。

    ```bash
    yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar teravalidate -Dmapred.map.tasks=50 -Dmapred.reduce.tasks=25 /example/data/10GB-sort-output /example/data/10GB-sort-validate
    ```

## <a name="next-steps"></a>次のステップ

この記事では、Linux ベースの HDInsight クラスターに付属するサンプルを実行する方法を説明しました。 HDInsight で Pig、Hive、および MapReduce を使用する方法のチュートリアルについては、次のトピックをご覧ください。

* [HDInsight 上の Apache Hadoop で Apache Hive を使用する](hdinsight-use-hive.md)
* [HDInsight 上の Apache Hadoop で MapReduce を使用する](hdinsight-use-mapreduce.md)
