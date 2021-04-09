---
title: MapReduce と HDInsight 上の Apache Hadoop
description: HDInsight クラスターの Apache Hadoop 上で Apache MapReduce ジョブを実行する方法について説明します。
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive
ms.date: 12/06/2019
ms.openlocfilehash: 33d96262ca2e2ff3003fbf2b40ce9ceb496337ae
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "98944263"
---
# <a name="use-mapreduce-in-apache-hadoop-on-hdinsight"></a>HDInsight 上の Apache Hadoop で MapReduce を使用する

HDInsight クラスターで MapReduce ジョブを実行する方法を説明します。

## <a name="example-data"></a>サンプル データ

HDInsight にはさまざまなサンプル データ セットが用意されていて、`/example/data` および `/HdiSamples` ディレクトリに格納されています。 これらのディレクトリは、クラスターの既定のストレージ内にあります。 このドキュメントでは、`/example/data/gutenberg/davinci.txt` ファイルを使用します。 このファイルには、レオナルド ダ ヴィンチの手記が含まれています。

## <a name="example-mapreduce"></a>MapReduce の例

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

独自の MapReduce アプリケーションの作成手順については、[HDInsight 用の Java MapReduce アプリケーションの開発](apache-hadoop-develop-deploy-java-mapreduce-linux.md)に関する記事を参照してください。

## <a name="run-the-mapreduce"></a>MapReduce の実行

HDInsight では、さまざまな方法を使用して HiveQL ジョブを実行できます。 次の表を使用して、適切な方法を判別してから、該当するチュートリアルのリンクをクリックしてください。

| **使用する方法** | **目的** |  使用元の **クライアントのオペレーティング システム** |
|:--- |:--- |:--- |:--- |
| [SSH](apache-hadoop-use-mapreduce-ssh.md) |**SSH** |Linux、Unix、Mac OS X、または Windows |
| [Curl](apache-hadoop-use-mapreduce-curl.md) |**REST** |Linux、Unix、Mac OS X、または Windows |
| [Windows PowerShell](apache-hadoop-use-mapreduce-powershell.md) |**Windows PowerShell**  |Windows |

## <a name="next-steps"></a>次のステップ

HDInsight でのデータ操作の詳細については、次のドキュメントを参照してください。

* [HDInsight 用 Java MapReduce プログラムの開発](apache-hadoop-develop-deploy-java-mapreduce-linux.md)

* [HDInsight での Apache Hive の使用](./hdinsight-use-hive.md)
