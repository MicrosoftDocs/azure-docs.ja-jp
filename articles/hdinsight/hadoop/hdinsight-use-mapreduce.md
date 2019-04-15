---
title: MapReduce と HDInsight 上の Apache Hadoop
description: HDInsight クラスター上の Apache Hadoop で MapReduce ジョブを実行する方法について説明します。
services: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 03/20/2019
ms.openlocfilehash: f2baaf598be8ede69fd6e1fa49a5f5a6b64c24ff
ms.sourcegitcommit: 6da4959d3a1ffcd8a781b709578668471ec6bf1b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/27/2019
ms.locfileid: "58521202"
---
# <a name="use-mapreduce-in-apache-hadoop-on-hdinsight"></a>HDInsight 上の Apache Hadoop で MapReduce を使用する

HDInsight クラスターで MapReduce ジョブを実行する方法を説明します。 

## <a id="whatis"></a>MapReduce とは

Apache Hadoop MapReduce は、膨大なデータを処理するジョブを記述するためのソフトウェア フレームワークです。 入力データは、独立したチャンクに分割されます。 各チャンクは、クラスター内のノード全体で並列に処理されます。 MapReduce ジョブは次の 2 つの関数で構成されます。

* **Mapper**: 入力データを使用して分析し (通常はフィルターと並べ替え操作を使用)、タプル (キーと値のペア) を出力します。

* **Reducer**: Mapper で出力されるタプルを使用して、Mapper データから、より小さい結合結果を作成する要約操作を実行します。

次の図では、基本的なワード カウント MapReduce ジョブの例を示します。

![HDI.WordCountDiagram][image-hdi-wordcountdiagram]

このジョブの出力は、テキストでの各単語の出現回数です。

* まず、Mapper が入力テキストから各行を読み込み、単語に分解して、 単語が出現するたびにキーと値のペア (キーは単語、値は 1) が出力されます。 出力は reducer に送信する前に並べ替えられます。
* reducer は、各単語の個々のカウントを合計し、単語とその後に続く合計出現回数から成る単一のキーと値のペアを出力します。

さまざまな言語で MapReduce を実装することができます。 Java は最も一般的な実装で、このドキュメントでのデモンストレーションのために使用されます。

## <a name="development-languages"></a>開発言語

Java や Java 仮想マシンに基づく言語またはフレームワークは、MapReduce ジョブとして直接実行できます。 このドキュメントで使用されている例は、Java MapReduce アプリケーションです。 C# や Python などの Java 以外の言語、またはスタンドアロンの実行可能ファイルでは **Hadoop ストリーミング**を使用する必要があります。

Hadoop ストリーミングは、STDIN と STDOUT 上で mapper や reducer と通信します。 mapper と reducer は、STDIN から一度に 1 行ずつデータを読み取り、STDOUT に出力を書き込みます。 mapper と reducer によって読み取りまたは出力が行われる各行は、以下のように、タブ文字で区切られたキーと値のペアの形式である必要があります。

    [key]/t[value]

詳細については、「 [Hadoop ストリーミング](https://hadoop.apache.org/docs/r1.2.1/streaming.html)」を参照してください。

HDInsight での Hadoop ストリーミングの使用例については、以下のドキュメントを参照してください。

* [C# MapReduce ジョブの開発](apache-hadoop-dotnet-csharp-mapreduce-streaming.md)

* [Python MapReduce ジョブの開発](apache-hadoop-streaming-python.md)

## <a id="data"></a>サンプル データ

HDInsight にはさまざまなサンプル データ セットが用意されていて、`/example/data` および `/HdiSamples` ディレクトリに格納されています。 これらのディレクトリは、クラスターの既定のストレージ内にあります。 このドキュメントでは、`/example/data/gutenberg/davinci.txt` ファイルを使用します。 このファイルには、レオナルド ダ ヴィンチの手記が含まれています。

## <a id="job"></a>MapReduce の例

サンプルの MapReduce ワード カウント アプリケーションは、HDInsight クラスターに付属しています。 このサンプルは、クラスターの既定のストレージの `/example/jars/hadoop-mapreduce-examples.jar` にあります。

次の Java コードは、`hadoop-mapreduce-examples.jar` ファイルに含まれている MapReduce アプリケーションのソースです。

```java
package org.apache.hadoop.examples;

import java.io.IOException;
import java.util.StringTokenizer;

import org.apache.hadoop.conf.Configuration;
import org.apache.hadoop.fs.Path;
import org.apache.hadoop.io.IntWritable;
import org.apache.hadoop.io.Text;
import org.apache.hadoop.mapreduce.Job;
import org.apache.hadoop.mapreduce.Mapper;
import org.apache.hadoop.mapreduce.Reducer;
import org.apache.hadoop.mapreduce.lib.input.FileInputFormat;
import org.apache.hadoop.mapreduce.lib.output.FileOutputFormat;
import org.apache.hadoop.util.GenericOptionsParser;

public class WordCount {

    public static class TokenizerMapper
        extends Mapper<Object, Text, Text, IntWritable>{

    private final static IntWritable one = new IntWritable(1);
    private Text word = new Text();

    public void map(Object key, Text value, Context context
                    ) throws IOException, InterruptedException {
        StringTokenizer itr = new StringTokenizer(value.toString());
        while (itr.hasMoreTokens()) {
        word.set(itr.nextToken());
        context.write(word, one);
        }
    }
    }

    public static class IntSumReducer
        extends Reducer<Text,IntWritable,Text,IntWritable> {
    private IntWritable result = new IntWritable();

    public void reduce(Text key, Iterable<IntWritable> values,
                        Context context
                        ) throws IOException, InterruptedException {
        int sum = 0;
        for (IntWritable val : values) {
        sum += val.get();
        }
        result.set(sum);
        context.write(key, result);
    }
    }

    public static void main(String[] args) throws Exception {
    Configuration conf = new Configuration();
    String[] otherArgs = new GenericOptionsParser(conf, args).getRemainingArgs();
    if (otherArgs.length != 2) {
        System.err.println("Usage: wordcount <in> <out>");
        System.exit(2);
    }
    Job job = new Job(conf, "word count");
    job.setJarByClass(WordCount.class);
    job.setMapperClass(TokenizerMapper.class);
    job.setCombinerClass(IntSumReducer.class);
    job.setReducerClass(IntSumReducer.class);
    job.setOutputKeyClass(Text.class);
    job.setOutputValueClass(IntWritable.class);
    FileInputFormat.addInputPath(job, new Path(otherArgs[0]));
    FileOutputFormat.setOutputPath(job, new Path(otherArgs[1]));
    System.exit(job.waitForCompletion(true) ? 0 : 1);
    }
}
```

独自の MapReduce アプリケーションを記述する手順については、次のドキュメントを参照してください。

* [HDInsight 用 Java MapReduce アプリケーションの開発](apache-hadoop-develop-deploy-java-mapreduce-linux.md)

* [HDInsight 用 Python MapReduce アプリケーションの開発](apache-hadoop-streaming-python.md)

## <a id="run"></a>MapReduce の実行

HDInsight では、さまざまな方法を使用して HiveQL ジョブを実行できます。 次の表を使用して、適切な方法を判別してから、該当するチュートリアルのリンクをクリックしてください。

| **使用する方法** | **目的** | 使用する **クラスターのオペレーティング システム** | 使用元の **クライアントのオペレーティング システム** |
|:--- |:--- |:--- |:--- |
| [SSH](apache-hadoop-use-mapreduce-ssh.md) |**SSH** |Linux |Linux、Unix、Mac OS X、または Windows |
| [Curl](apache-hadoop-use-mapreduce-curl.md) |**REST** |Linux または Windows |Linux、Unix、Mac OS X、または Windows |
| [Windows PowerShell](apache-hadoop-use-mapreduce-powershell.md) |**Windows PowerShell** |Linux または Windows | Windows |

## <a id="nextsteps"></a>次のステップ

HDInsight でのデータ操作の詳細については、次のドキュメントを参照してください。

* [HDInsight 用 Java MapReduce プログラムの開発](apache-hadoop-develop-deploy-java-mapreduce-linux.md)

* [HDInsight 用 Python ストリーミング MapReduce プログラムの開発](apache-hadoop-streaming-python.md)

* [HDInsight での Apache Hive の使用][hdinsight-use-hive]

* [HDInsight での Apache Pig の使用][hdinsight-use-pig]


[hdinsight-upload-data]: hdinsight-upload-data.md
[hdinsight-get-started]:apache-hadoop-linux-tutorial-get-started.md
[hdinsight-develop-mapreduce-jobs]: apache-hadoop-develop-deploy-java-mapreduce-linux.md
[hdinsight-use-hive]:../hdinsight-use-hive.md
[hdinsight-use-pig]:hdinsight-use-pig.md


[powershell-install-configure]: /powershell/azureps-cmdlets-docs

[image-hdi-wordcountdiagram]: ./media/hdinsight-use-mapreduce/HDI.WordCountDiagram.gif
