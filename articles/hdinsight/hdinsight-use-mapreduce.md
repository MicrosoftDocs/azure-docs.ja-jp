---
title: "HDInsight での MapReduce と Hadoop | Microsoft Docs"
description: "HDInsight クラスターの Hadoop で MapReduce ジョブを実行する方法を説明します。 Java MapReduce ジョブとして実装されている基本的なワード カウントの操作を実行します。"
services: hdinsight
documentationcenter: 
author: Blackmist
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: 7f321501-d62c-4ffc-b5d6-102ecba6dd76
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 01/12/2017
ms.author: larryfr
translationtype: Human Translation
ms.sourcegitcommit: afe143848fae473d08dd33a3df4ab4ed92b731fa
ms.openlocfilehash: 8be0f2f30f815277c4953c223d91c2571c2521a5
ms.lasthandoff: 03/17/2017


---
# <a name="use-mapreduce-in-hadoop-on-hdinsight"></a>HDInsight での MapReduce と Hadoop の使用

[!INCLUDE [mapreduce-selector](../../includes/hdinsight-selector-use-mapreduce.md)]

この記事では、HDInsight クラスターの Hadoop で MapReduce ジョブを実行する方法を説明します。 Java MapReduce ジョブとして実装されている基本的なワード カウントの操作を実行します。

## <a id="whatis"></a>MapReduce とは

Hadoop MapReduce は、膨大なデータを処理するジョブを記述するためのソフトウェア フレームワークです。 入力データは独立したチャンクに分割され、クラスター内のノードで並列に処理されます。 MapReduce ジョブは次の&2; つの関数で構成されます。

* **Mapper**: 入力データを使用して分析し (通常はフィルターと並べ替え操作を使用)、タプル (キーと値のペア) を出力します。

* **Reducer**: Mapper で出力されるタプルを使用して、Mapper データから、より小さい結合結果を作成する要約操作を実行します。

次の図では、基本的なワード カウント MapReduce ジョブの例を示します。

![HDI.WordCountDiagram][image-hdi-wordcountdiagram]

このジョブの出力は、分析されたテキストでの各単語の出現回数です。

* まず、Mapper が入力テキストから各行を読み込み、単語に分解して、 単語が出現するたびにキーと値のペア (キーは単語、値は 1) が出力されます。 出力は reducer に送信する前に並べ替えられます。
* reducer は、各単語の個々のカウントを合計し、単語とその後に続く合計出現回数から成る単一のキーと値のペアを出力します。

さまざまな言語で MapReduce を実装することができます。 Java は最も一般的な実装で、このドキュメントでのデモンストレーションのために使用されます。

### <a name="hadoop-streaming"></a>Hadoop ストリーミング

Java や Java 仮想マシンに基づく言語またはフレームワーク (たとえば、Scalding や Cascading) は、Java アプリケーションと同様に、MapReduce ジョブとして直接実行できます。 C# や Python などの他の言語、またはスタンドアロンの実行可能ファイルでは Hadoop ストリーミングを使用する必要があります。

Hadoop ストリーミングは STDIN と STDOUT を介して mapper および reducer と通信します。mapper と reducer は STDIN から同時に&1; 行のデータを読み取り、出力を STDOUT に書き込みます。 mapper と reducer によって読み取りまたは出力される各行は、以下のように、タブ文字で区切られたキーと値のペアの形式である必要があります。

    [key]/t[value]

詳細については、「 [Hadoop ストリーミング](http://hadoop.apache.org/docs/r1.2.1/streaming.html)」を参照してください。

HDInsight での Hadoop ストリーミングの使用例については、以下を参照してください。

* [Python MapReduce ジョブの開発](hdinsight-hadoop-streaming-python.md)

## <a id="data"></a>サンプル データについて

この例では、サンプル データとして、HDInsight クラスターでテキスト ドキュメントとして提供される、レオナルド ダ ビンチの手記を使用します。

サンプル データは、HDInsight が Hadoop クラスターの既定のファイル システムとして使用する Azure BLOB ストレージに格納されています。 HDInsight では、 **wasb** プレフィックスを使用して、BLOB ストレージに格納されたファイルにアクセスすることができます。 たとえば、sample.log ファイルにアクセスするには、次の構文を使用します。

    wasbs:///example/data/gutenberg/davinci.txt

Azure BLOB ストレージが HDInsight の既定のストレージであるため、 **/example/data/gutenberg/davinci.txt**を使用してファイルにアクセスすることもできます。

> [!NOTE]
> 上の構文 **wasbs:///** は HDInsight クラスターの既定のストレージ コンテナーに格納されたファイルにアクセスするために使用します。 クラスターをプロビジョニングするときに追加のストレージ アカウントを指定し、そのアカウントに格納されたファイルにアクセスする必要がある場合、コンテナー名とストレージ アカウント アドレスを指定することによって、データにアクセスすることができます。 たとえば、**wasbs://mycontainer@mystorage.blob.core.windows.net/example/data/gutenberg/davinci.txt** です。


## <a id="job"></a>MapReduce 例について

この例で使用される MapReduce ジョブは **wasbs://example/jars/hadoop-mapreduce-examples.jar** にあり、HDInsight クラスターと共に提供されます。 これには、**davinci.txt** に対して実行するワード カウント例が含まれます。

> [!NOTE]
> HDInsight 2.1 クラスターでのファイルの場所は **wasbs:///example/jars/hadoop-examples.jar** です。

参考のために、ワード カウント MapReduce ジョブの Java コードを以下に示します。

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

独自の MapReduce ジョブの作成手順については、「 [HDInsight 用 Java MapReduce プログラムの開発](hdinsight-develop-deploy-java-mapreduce-linux.md)」を参照してください。

## <a id="run"></a>MapReduce の実行

HDInsight では、さまざまな方法を使用して HiveQL ジョブを実行できます。 次の表を使用して、適切な方法を判別してから、該当するチュートリアルのリンクをクリックしてください。

| **使用する方法** | **目的** | 使用する **クラスターのオペレーティング システム** | 使用元の **クライアントのオペレーティング システム** |
|:--- |:--- |:--- |:--- |
| [SSH](hdinsight-hadoop-use-mapreduce-ssh.md) |**SSH** |Linux |Linux、Unix、Mac OS X、または Windows |
| [Curl](hdinsight-hadoop-use-mapreduce-curl.md) |**REST** |Linux または Windows |Linux、Unix、Mac OS X、または Windows |
| [Windows PowerShell](hdinsight-hadoop-use-mapreduce-powershell.md) |**Windows PowerShell** |Linux または Windows |Windows |
| [リモート デスクトップ](hdinsight-hadoop-use-mapreduce-remote-desktop.md) |**リモート デスクトップ** |Windows |Windows |

> [!IMPORTANT]
> Linux は、バージョン 3.4 以上の HDInsight で使用できる唯一のオペレーティング システムです。 詳細については、[Window での HDInsight の廃止](hdinsight-component-versioning.md#hdi-version-32-and-33-nearing-deprecation-date)に関する記事を参照してください。

## <a id="nextsteps"></a>次のステップ

MapReduce は高度な診断機能を備えていますが、使いこなすのは少し難しいツールです。 HDInsight でデータを操作する簡単な方法を提供する、Pig や Hive などのテクノロジだけでなく、MapReduce アプリケーションを定義しやすくする Java ベースのフレームワークがいくつかあります。 詳細については、次の記事を参照してください。

* [HDInsight 用 Java MapReduce プログラムの開発](hdinsight-develop-deploy-java-mapreduce-linux.md)
* [HDInsight 用 Python ストリーミング MapReduce プログラムの開発](hdinsight-hadoop-streaming-python.md)
* [HDInsight での Apache Hadoop による Scalding MapReduce ジョブの開発](hdinsight-hadoop-mapreduce-scalding.md)
* [HDInsight での Hive の使用][hdinsight-use-hive]
* [HDInsight での Pig の使用][hdinsight-use-pig]


[hdinsight-upload-data]: hdinsight-upload-data.md
[hdinsight-get-started]: hdinsight-hadoop-linux-tutorial-get-started.md
[hdinsight-develop-mapreduce-jobs]: hdinsight-develop-deploy-java-mapreduce-linux.md
[hdinsight-use-hive]: hdinsight-use-hive.md
[hdinsight-use-pig]: hdinsight-use-pig.md


[powershell-install-configure]: /powershell/azureps-cmdlets-docs

[image-hdi-wordcountdiagram]: ./media/hdinsight-use-mapreduce/HDI.WordCountDiagram.gif

