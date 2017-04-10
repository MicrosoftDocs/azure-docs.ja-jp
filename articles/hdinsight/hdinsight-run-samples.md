---
title: "HDInsight での Hadoop のサンプルの実行 | Microsoft Docs"
description: "用意されたサンプルを利用して、Azure HDInsight サービスを使い始めます。 データ クラスター上で MapReduce プログラムを実行する PowerShell スクリプトを使用します。"
services: hdinsight
documentationcenter: 
tags: azure-portal
author: mumian
manager: jhubbard
editor: cgronlun
ms.assetid: bf76d452-abb4-4210-87bd-a2067778c6ed
ms.service: hdinsight
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/14/2017
ms.author: jgao
ROBOTS: NOINDEX
translationtype: Human Translation
ms.sourcegitcommit: d83bfd81768722592565fe924c4d00610b149999
ms.openlocfilehash: 16801860b78b40cc883393ca4db3ffa208b889fd
ms.lasthandoff: 02/15/2017


---
# <a name="run-hadoop-mapreduce-samples-in-windows-based-hdinsight"></a>Windows ベースの HDInsight での Hadoop MapReduce サンプルの実行
[!INCLUDE [samples-selector](../../includes/hdinsight-run-samples-selector.md)]

Azure HDInsight を使用して、Hadoop クラスターで MapReduce ジョブの実行を始めるときに役立つサンプルが用意されています。 これらのサンプルは、HDInsight によって管理されるクラスターを作成して、それぞれのクラスターで利用できます。 これらのサンプルを実行すると、Hadoop クラスターで Azure PowerShell コマンドレットを使用してジョブを実行するのに慣れることができます。

* [**ワード カウント**][hdinsight-sample-wordcount]: テキスト ファイル内の単語出現回数をカウントします。
* [**C# ストリーミング ワード カウント**][hdinsight-sample-csharp-streaming]: Hadoop ストリーミング インターフェイスを使用して、テキスト ファイル内の単語出現回数をカウントします。
* [**Pi 推定**][hdinsight-sample-pi-estimator]: 統計的手法 (準モンテカルロ法) を使用して、Pi の値を推定します。
* [**10 GB GraySort**][hdinsight-sample-10gb-graysort] : HDInsight を使用して、10 GB のファイルに対して汎用 GraySort を実行します。 実行するジョブは&3; つあります。データを生成する Teragen、データをソートする Terasort、データが適切にソートされているか確認する Teravalidate です。

> [!NOTE]
> ソース コードは「付録」にあります。

Hadoop 関連技術の追加情報は、Java ベースの MapReduce プログラミングやストリーミング、Windows PowerShell スクリプトで使用するコマンドレットのドキュメントなど、Web 上に多数存在しています。 これらのリソースの詳細については、以下を参照してください。

* [HDInsight での Hadoop 用 Java MapReduce プログラムの開発](hdinsight-develop-deploy-java-mapreduce-linux.md)
* [HDInsight での Hadoop ジョブの送信](hdinsight-submit-hadoop-jobs-programmatically.md)
* [Azure HDInsight 入門][hdinsight-introduction]

今日では、多くの人が MapReduce より Hive と Pig を選びます。  詳細については、次を参照してください。

* [HDInsight での Hive の使用](hdinsight-use-hive.md)
* [HDInsight での Pig の使用](hdinsight-use-pig.md)

**前提条件**:

* **Azure サブスクリプション**。 [Azure 無料試用版の取得](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/)に関するページを参照してください。
* **HDInsight クラスター**。 クラスターを作成できるさまざまな方法については、「 [HDInsight で Hadoop クラスターを作成する](hdinsight-provision-clusters.md)」を参照してください。
* **Azure PowerShell を実行できるワークステーション**。

    > [!IMPORTANT]
    > Azure サービス マネージャーを使用した HDInsight リソースの管理に関する Azure PowerShell のサポートは、2017 年 1 月 1 日までに**廃止**される予定です。 このドキュメントの手順では、Azure Resource Manager で機能する新しい HDInsight コマンドレットを使用します。
    >
    > [Azure PowerShell のインストールと構成](/powershell/azureps-cmdlets-docs)に関するページの手順に従い、Azure PowerShell の最新バージョンをインストールしてください。 Azure Resource Manager で機能する新しいコマンドレットを使用するようにスクリプトを変更する必要がある場合、詳しくは、「[HDInsight クラスター用の Azure Resource Manager ベースの開発ツールに移行する](hdinsight-hadoop-development-using-azure-resource-manager.md)」をご覧ください。

## <a name="hdinsight-sample-wordcount"></a>ワード カウント - Java
MapReduce プロジェクトを送信するには、まず、MapReduce ジョブ定義を作成します。 ジョブ定義で、MapReduce プログラムの jar ファイル、その jar ファイルの場所 (**wasbs:///example/jars/hadoop-mapreduce-examples.jar**)、クラス名、および引数を指定します。  ワード カウント MapReduce プログラムでは&2; つの引数 (ワードのカウントに使用されるソース ファイルと、出力の場所) を使用します。

ソース コードは「 [付録 A](#apendix-a---the-word-count-MapReduce-program-in-java)」にあります。

Java MapReduce プログラムの開発手順については、「 [HDInsight での Hadoop 用 Java MapReduce プログラムの開発](hdinsight-develop-deploy-java-mapreduce-linux.md)

**ワード カウント MapReduce ジョブを送信するには**

1. **Windows PowerShell ISE**を開きます。 手順については、[Azure PowerShell のインストールと構成に関するページ][powershell-install-configure]をご覧ください。
2. 次の PowerShell スクリプトを貼り付けます。

    ```powershell
    $subscriptionName = "<Azure Subscription Name>"
    $resourceGroupName = "<Resource Group Name>"
    $clusterName = "<HDInsight cluster name>"             # HDInsight cluster name

    Select-AzureRmSubscription -SubscriptionName $subscriptionName

    # Define the MapReduce job
    $mrJobDefinition = New-AzureRmHDInsightMapReduceJobDefinition `
                                -JarFile "wasbs:///example/jars/hadoop-mapreduce-examples.jar" `
                                -ClassName "wordcount" `
                                -Arguments "wasbs:///example/data/gutenberg/davinci.txt", "wasbs:///example/data/WordCountOutput1"

    # Submit the job and wait for job completion
    $cred = Get-Credential -Message "Enter the HDInsight cluster HTTP user credential:"
    $mrJob = Start-AzureRmHDInsightJob `
                        -ResourceGroupName $resourceGroupName `
                        -ClusterName $clusterName `
                        -HttpCredential $cred `
                        -JobDefinition $mrJobDefinition

    Wait-AzureRmHDInsightJob `
        -ResourceGroupName $resourceGroupName `
        -ClusterName $clusterName `
        -HttpCredential $cred `
        -JobId $mrJob.JobId

    # Get the job output
    $cluster = Get-AzureRmHDInsightCluster -ResourceGroupName $resourceGroupName -ClusterName $clusterName
    $defaultStorageAccount = $cluster.DefaultStorageAccount -replace '.blob.core.windows.net'
    $defaultStorageAccountKey = (Get-AzureRmStorageAccountKey -ResourceGroupName $resourceGroupName -Name $defaultStorageAccount)[0].Value
    $defaultStorageContainer = $cluster.DefaultStorageContainer

    Get-AzureRmHDInsightJobOutput `
        -ResourceGroupName $resourceGroupName `
        -ClusterName $clusterName `
        -HttpCredential $cred `
        -DefaultStorageAccountName $defaultStorageAccount `
        -DefaultStorageAccountKey $defaultStorageAccountKey `
        -DefaultContainer $defaultStorageContainer  `
        -JobId $mrJob.JobId `
        -DisplayOutputType StandardError

    # Download the job output to the workstation
    $storageContext = New-AzureStorageContext -StorageAccountName $defaultStorageAccount -StorageAccountKey $defaultStorageAccountKey
    Get-AzureStorageBlobContent -Container $defaultStorageContainer -Blob example/data/WordCountOutput/part-r-00000 -Context $storageContext -Force

    # Display the output file
    cat ./example/data/WordCountOutput/part-r-00000 | findstr "there"
    ```

    MapReduce ジョブは、単語と出現回数が記録された *part-r-00000*という名前のファイルを作成します。 スクリプトでは **findstr** コマンドを使用して、*"there"* を含む単語をすべて表示しています。
3. 最初の&3; つの変数を設定して、スクリプトを実行します。

## <a name="hdinsight-sample-csharp-streaming"></a>ワード カウント - C# ストリーミング
Hadoop には MapReduce に対するストリーミング API が用意されていて、Java 以外の言語 map 関数と reduce 関数を記述できます。

> [!NOTE]
> このチュートリアルの手順は、Windows ベースの HDInsight クラスターに対してのみ機能します。 Linux ベースの HDInsight クラスターのストリーミング例は、「 [HDInsight 用 Python ストリーミング プログラムの開発](hdinsight-hadoop-streaming-python.md)」をご覧ください。

この例では、mapper と reducer は [stdin][stdin-stdout-stderr] から入力を (1 行ずつ) 読み取り、出力を [stdout][stdin-stdout-stderr] に書き込む実行可能ファイルです。 プログラムはテキスト内の単語すべての出現数を計算します。

**mapper**用の実行可能ファイルが指定されると、各 mapper タスクは mapper 開始時に別のプロセスとしてその実行可能ファイルを起動します。 mapper タスクは実行されると、入力を行に変換して、その行をプロセスの [stdin][stdin-stdout-stderr] にフィードします。

一方、mapper はプロセスの stdout から行指向の出力を収集します。 各行はキーと値のペアに変換され、mapper の出力として収集されます。 既定では、行の最初のタブ文字までがキーであり、行の残り (タブ文字を除く) が値です。 行にタブ文字がない場合は、行全体がキーと見なされ、値は null になります。

**reducer**用の実行可能ファイルが指定されると、各 reducer タスクは reducer 開始時に別のプロセスとしてその実行可能ファイルを起動します。 reducer タスクは実行されると、入力のキーと値のペアを行に変換して、その行をプロセスの [stdin][stdin-stdout-stderr] にフィードします。

一方、reducer はプロセスの [stdout][stdin-stdout-stderr] から行指向の出力を収集します。 各行はキーと値のペアに変換され、reducer の出力として収集されます。 既定では、行の最初のタブ文字までがキーであり、行の残り (タブ文字を除く) が値です。

**C# ストリーミング ワード カウント ジョブを送信するには**

* 「[ワード カウント - Java](#word-count-java)」の手順に従って、ジョブ定義を次の行に置き換えます。

    ```powershell
    $mrJobDefinition = New-AzureRmHDInsightStreamingMapReduceJobDefinition `
                            -Files "/example/apps/cat.exe","/example/apps/wc.exe" `
                            -Mapper "cat.exe" `
                            -Reducer "wc.exe" `
                            -InputPath "/example/data/gutenberg/davinci.txt" `
                            -OutputPath "/example/data/StreamingOutput/wc.txt"
    ```

    出力ファイルは次のようになります。

        example/data/StreamingOutput/wc.txt/part-00000

## <a name="hdinsight-sample-pi-estimator"></a>Pi 推定
Pi 推定では、統計的手法 (準モンテカルロ法) を使用して、Pi の値を推定します。 単位正方形の内部にランダムに配置された点は、その正方形に内接する円の内部にも円の面積に等しい確率 (Pi/4) で配置されます。 Pi の値は 4R という値で計算されます。ここで R は、正方形の内部にある点の総数と、円の内部にある点の数との比率です。 サンプルの点の数が大きくなるほど、推定値の精度が上がります。

このサンプルで示したスクリプトでは、Hadoop jar ジョブを送信し、マップ数を 16 として実行し、それぞれがパラメーター値として指定された 1,000 万個のサンプル点を計算します。 このパラメーター値を変更すると、Pi の推定値の精度を高めることができます。 参考のために、Pi の小数点以下 10 桁までは 3.1415926535 です。

**Pi 推定ジョブを送信するには**

* 「[ワード カウント - Java](#word-count-java)」の手順に従って、ジョブ定義を次の行に置き換えます。

    ```powershell
    $mrJobJobDefinition = New-AzureRmHDInsightMapReduceJobDefinition `
                                -JarFile "wasbs:///example/jars/hadoop-mapreduce-examples.jar" `
                                -ClassName "pi" `
                                -Arguments "16", "10000000"
    ```

## <a name="hdinsight-sample-10gb-graysort"></a>10 GB GraySort
このサンプルでは、比較的高速に実行できるように、中程度のサイズの 10 GB のデータを使用します。 使用する MapReduce アプリケーションは Owen O'Malley と Arun Murthy が開発したもので、2009 年にはテラバイト ソート ベンチマークの汎用目的 ("daytona") 部門で 0.578 TB/分 (173 分で 100 TB) という年間記録を樹立しました。 これも含めたソート ベンチマークの詳細については、 [Sortbenchmark](http://sortbenchmark.org/) サイトを参照してください。

このサンプルでは&3; 組の MapReduce プログラムを使用します。

1. **TeraGen** は、ソートするデータ行を生成するのに使用できる MapReduce プログラムです。
2. **TeraSort** は入力データをサンプリングし、MapReduce を使用してデータを合計順にソートします。 TeraSort は MapReduce 関数の標準ソートです。 特に、sample[i-1] <= key < sample[i] となるキーはすべて reduce i に送られます。 このため、reduce i の出力がすべて reduce i+1 の出力より小さくなることが保証されます。
3. **TeraValidate** は、出力がグローバルにソートされているか検証する MapReduce プログラムです。 出力ディレクトリ内のファイルごとにマップを&1; つ作成します。各マップは各キーが前のキー以下であることを保証します。 map 関数は、各ファイルの最初のキーと最後のキーの記録も生成し、reduce 関数は、ファイル i の最初のキーがファイル i-1 の最後のキーよりも大きいことを確認します。 問題が見つかった場合は、reduce の出力として範囲外のキーがレポートされます。

3 つのアプリケーションすべてで使用される入力形式と出力形式は、適切な形式のテキスト ファイルを読み書きします。 ベンチマーク コンテストでは出力データを複数のノードにレプリケーションする必要がないため、reduce の出力ではレプリケーションが既定の 3 ではなく 1 に設定されます。

サンプルでは&3; つのタスクを実行する必要があります。各タスクが、先ほど説明した MapReduce プログラムに対応しています。

1. **TeraGen** MapReduce ジョブを実行して、ソート用のデータを生成します。
2. **TeraSort** MapReduce ジョブを実行して、データをソートします。
3. **TeraValidate** MapReduce ジョブを実行して、データが正しくソートされていることを確認します。

**ジョブを送信するには**

* 「[ワード カウント - Java](#word-count-java)」の手順に従って、以下のジョブ定義を使用します。

    ```powershell
    $teragen = New-AzureRmHDInsightMapReduceJobDefinition `
                                -JarFile "/example/jars/hadoop-mapreduce-examples.jar" `
                                -ClassName "teragen" `
                                -Arguments "-Dmapred.map.tasks=50", "100000000", "/example/data/10GB-sort-input"

    $terasort = New-AzureRmHDInsightMapReduceJobDefinition `
                                -JarFile "/example/jars/hadoop-mapreduce-examples.jar" `
                                -ClassName "terasort" `
                                -Arguments "-Dmapred.map.tasks=50", "-Dmapred.reduce.tasks=25", "/example/data/10GB-sort-input", "/example/data/10GB-sort-output"

    $teravalidate = New-AzureRmHDInsightMapReduceJobDefinition `
                                -JarFile "/example/jars/hadoop-mapreduce-examples.jar" `
                                -ClassName "teravalidate" `
                                -Arguments "-Dmapred.map.tasks=50", "-Dmapred.reduce.tasks=25", "/example/data/10GB-sort-output", "/example/data/10GB-sort-validate"
    ```

## <a name="next-steps"></a>次のステップ
この記事および各サンプルの記事では、Azure PowerShell を使用して HDInsight クラスターに付属するサンプルを実行する方法を説明しました。 HDInsight で Pig、Hive、および MapReduce を使用する方法のチュートリアルについては、次のトピックをご覧ください。

* [HDInsight で Hive と Hadoop を使用し、モバイル ハンドセットの使用状況を分析する][hdinsight-get-started]
* [HDInsight での Pig と Hadoop の使用][hdinsight-use-pig]
* [HDInsight での Hive と Hadoop の使用][hdinsight-use-hive]
* [HDInsight での Hadoop Job の送信][hdinsight-submit-jobs]
* [Azure HDInsight SDK のドキュメント][hdinsight-sdk-documentation]
* [HDInsight での Hadoop のデバッグ: エラー メッセージ][hdinsight-errors]

## <a name="appendix-a---the-word-count-source-code"></a>付録 A - ワード カウントのソース コード

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

## <a name="appendix-b---the-word-count-streaming-source-code"></a>付録 B - ワード カウントのストリーミング ソース コード
MapReduce プログラムでは、cat.exe アプリケーションを map インターフェイスとして使ってコンソールにテキストをストリーミングし、wc.exe アプリケーションを reduce インターフェイスとして使って、文書からストリーミングされた単語の数を計算します。 mapper と reducer はどちらも標準入力ストリーム (stdin) から&1; 行ずつ文字を読み取って、標準出力ストリーム (stdout) に書き込みます。

```csharp
// The source code for the cat.exe (Mapper).

using System;
using System.IO;

namespace cat
{
    class cat
    {
        static void Main(string[] args)
        {
            if (args.Length > 0)
            {
                Console.SetIn(new StreamReader(args[0]));
            }

            string line;
            while ((line = Console.ReadLine()) != null)
            {
                Console.WriteLine(line);
            }
        }
    }
}
```

cat.cs ファイルの mapper コードは、[StreamReader][streamreader] オブジェクトを使用して入力ストリームの文字をコンソールに読み取り、次にコンソールが静的な [Console.Writeline][console-writeline] メソッドを使用してストリームを標準出力ストリームに書き込みます。

```csharp
// The source code for wc.exe (Reducer) is:

using System;
using System.IO;
using System.Linq;

namespace wc
{
    class wc
    {
        static void Main(string[] args)
        {
            string line;
            var count = 0;

            if (args.Length > 0){
                Console.SetIn(new StreamReader(args[0]));
            }

            while ((line = Console.ReadLine()) != null) {
                count += line.Count(cr => (cr == ' ' || cr == '\n'));
            }
            Console.WriteLine(count);
        }
    }
}
```

wc.cs ファイルの reducer コードは、[StreamReader][streamreader] オブジェクトを使用して、mapper の cat.exe によって出力された標準入力ストリームから文字を読み取ります。 [Console.Writeline][console-writeline] メソッドを使用して文字を読み取ると、各単語の末尾にある空白および改行文字をカウントして、 [Console.Writeline][console-writeline] メソッドを使用して合計を標準出力ストリームに書き込みます。

## <a name="appendix-c---the-pi-estimator-source-code"></a>付録 C - Pi 推定のソース コード
検査に利用できる mapper 関数と reducer 関数を含む Pi 推定 Java コードを次に挙げます。 mapper プログラムは、指定された数の点を生成して単位正方形内にランダムに配置して、円の内部にある点の数を計算します。 reducer プログラムは、mapper が計算した点の数を累計して、4R という公式から Pi の値を推定します。ここで R は、正方形の内部にある点の総数と、円の内部にある点の数との比率です。

```java
/**
* Licensed to the Apache Software Foundation (ASF) under one
* or more contributor license agreements. See the NOTICE file
* distributed with this work for additional information
* regarding copyright ownership. The ASF licenses this file
* to you under the Apache License, Version 2.0 (the
* "License"); you may not use this file except in compliance
* with the License. You may obtain a copy of the License at
*
* http://www.apache.org/licenses/LICENSE-2.0
*
* Unless required by applicable law or agreed to in writing, software
* distributed under the License is distributed on an "AS IS" BASIS,
* WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or     implied.
* See the License for the specific language governing permissions and
* limitations under the License.
*/

package org.apache.hadoop.examples;

import java.io.IOException;
import java.math.BigDecimal;
import java.util.Iterator;

import org.apache.hadoop.conf.Configured;
import org.apache.hadoop.fs.FileSystem;
import org.apache.hadoop.fs.Path;
import org.apache.hadoop.io.BooleanWritable;
import org.apache.hadoop.io.LongWritable;
import org.apache.hadoop.io.SequenceFile;
import org.apache.hadoop.io.Writable;
import org.apache.hadoop.io.WritableComparable;
import org.apache.hadoop.io.SequenceFile.CompressionType;
import org.apache.hadoop.mapred.FileInputFormat;
import org.apache.hadoop.mapred.FileOutputFormat;
import org.apache.hadoop.mapred.JobClient;
import org.apache.hadoop.mapred.JobConf;
import org.apache.hadoop.mapred.MapReduceBase;
import org.apache.hadoop.mapred.Mapper;
import org.apache.hadoop.mapred.OutputCollector;
import org.apache.hadoop.mapred.Reducer;
import org.apache.hadoop.mapred.Reporter;
import org.apache.hadoop.mapred.SequenceFileInputFormat;
import org.apache.hadoop.mapred.SequenceFileOutputFormat;
import org.apache.hadoop.util.Tool;
import org.apache.hadoop.util.ToolRunner;

//A Map-reduce program to estimate the value of Pi
//using quasi-Monte Carlo method.
//
//Mapper:
//Generate points in a unit square
//and then count points inside/outside of the inscribed circle of the square.
//
//Reducer:
//Accumulate points inside/outside results from the mappers.
//Let numTotal = numInside + numOutside.
//The fraction numInside/numTotal is a rational approximation of
//the value (Area of the circle)/(Area of the square),
//where the area of the inscribed circle is Pi/4
//and the area of unit square is 1.
//Then, Pi is estimated value to be 4(numInside/numTotal).
//

public class PiEstimator extends Configured implements Tool {
//tmp directory for input/output
static private final Path TMP_DIR = new Path(
PiEstimator.class.getSimpleName() + "_TMP_3_141592654");

//2-dimensional Halton sequence {H(i)},
//where H(i) is a 2-dimensional point and i >= 1 is the index.
//Halton sequence is used to generate sample points for Pi estimation.
private static class HaltonSequence {
// Bases
static final int[] P = {2, 3};
//Maximum number of digits allowed
static final int[] K = {63, 40};

private long index;
private double[] x;
private double[][] q;
private int[][] d;

//Initialize to H(startindex),
//so the sequence begins with H(startindex+1).
HaltonSequence(long startindex) {
index = startindex;
x = new double[K.length];
q = new double[K.length][];
d = new int[K.length][];
for(int i = 0; i < K.length; i++) {
q[i] = new double[K[i]];
d[i] = new int[K[i]];
}

for(int i = 0; i < K.length; i++) {
long k = index;
x[i] = 0;

for(int j = 0; j < K[i]; j++) {
q[i][j] = (j == 0? 1.0: q[i][j-1])/P[i];
d[i][j] = (int)(k % P[i]);
k = (k - d[i][j])/P[i];
x[i] += d[i][j] * q[i][j];
}
}
}

//Compute next point.
//Assume the current point is H(index).
//Compute H(index+1).
//@return a 2-dimensional point with coordinates in [0,1)^2
double[] nextPoint() {
index++;
for(int i = 0; i < K.length; i++) {
for(int j = 0; j < K[i]; j++) {
d[i][j]++;
x[i] += q[i][j];
if (d[i][j] < P[i]) {
break;
}
d[i][j] = 0;
x[i] -= (j == 0? 1.0: q[i][j-1]);
}
}
return x;
}
}

//Mapper class for Pi estimation.
//Generate points in a unit square and then
//count points inside/outside of the inscribed circle of the square.
public static class PiMapper extends MapReduceBase
implements Mapper<LongWritable, LongWritable, BooleanWritable, LongWritable> {

//Map method.
//@param offset samples starting from the (offset+1)th sample.
//@param size the number of samples for this map
//@param out output {ture->numInside, false->numOutside}
//@param reporter
public void map(LongWritable offset,
LongWritable size,
OutputCollector<BooleanWritable, LongWritable> out,
Reporter reporter) throws IOException {

final HaltonSequence haltonsequence = new HaltonSequence(offset.get());
long numInside = 0L;
long numOutside = 0L;

for(long i = 0; i < size.get(); ) {
//generate points in a unit square
final double[] point = haltonsequence.nextPoint();

//count points inside/outside of the inscribed circle of the square
final double x = point[0] - 0.5;
final double y = point[1] - 0.5;
if (x*x + y*y > 0.25) {
numOutside++;
} else {
numInside++;
}

//report status
i++;
if (i % 1000 == 0) {
reporter.setStatus("Generated " + i + " samples.");
}
}

//output map results
out.collect(new BooleanWritable(true), new LongWritable(numInside));
out.collect(new BooleanWritable(false), new LongWritable(numOutside));
}
}

//Reducer class for Pi estimation.
//Accumulate points inside/outside results from the mappers.
public static class PiReducer extends MapReduceBase
implements Reducer<BooleanWritable, LongWritable, WritableComparable<?>, Writable> {

private long numInside = 0;
private long numOutside = 0;
private JobConf conf; //configuration for accessing the file system

//Store job configuration.
@Override
public void configure(JobConf job) {
conf = job;
}

// Accumulate number of points inside/outside results from the mappers.
// @param isInside Is the points inside?
// @param values An iterator to a list of point counts
// @param output dummy, not used here.
// @param reporter

public void reduce(BooleanWritable isInside,
Iterator<LongWritable> values,
OutputCollector<WritableComparable<?>, Writable> output,
Reporter reporter) throws IOException {
if (isInside.get()) {
for(; values.hasNext(); numInside += values.next().get());
} else {
for(; values.hasNext(); numOutside += values.next().get());
}
}

//Reduce task done, write output to a file.
@Override
public void close() throws IOException {
//write output to a file
Path outDir = new Path(TMP_DIR, "out");
Path outFile = new Path(outDir, "reduce-out");
FileSystem fileSys = FileSystem.get(conf);
SequenceFile.Writer writer = SequenceFile.createWriter(fileSys, conf,
outFile, LongWritable.class, LongWritable.class,
CompressionType.NONE);
writer.append(new LongWritable(numInside), new LongWritable(numOutside));
writer.close();
}
}

//Run a map/reduce job for estimating Pi.
//@return the estimated value of Pi.
public static BigDecimal estimate(int numMaps, long numPoints, JobConf jobConf
)
throws IOException {
//setup job conf
jobConf.setJobName(PiEstimator.class.getSimpleName());

jobConf.setInputFormat(SequenceFileInputFormat.class);

jobConf.setOutputKeyClass(BooleanWritable.class);
jobConf.setOutputValueClass(LongWritable.class);
jobConf.setOutputFormat(SequenceFileOutputFormat.class);

jobConf.setMapperClass(PiMapper.class);
jobConf.setNumMapTasks(numMaps);

jobConf.setReducerClass(PiReducer.class);
jobConf.setNumReduceTasks(1);

// turn off speculative execution, because DFS doesn't handle
// multiple writers to the same file.
jobConf.setSpeculativeExecution(false);

//setup input/output directories
final Path inDir = new Path(TMP_DIR, "in");
final Path outDir = new Path(TMP_DIR, "out");
FileInputFormat.setInputPaths(jobConf, inDir);
FileOutputFormat.setOutputPath(jobConf, outDir);

final FileSystem fs = FileSystem.get(jobConf);
if (fs.exists(TMP_DIR)) {
throw new IOException("Tmp directory " + fs.makeQualified(TMP_DIR)
+ " already exists. Remove it first.");
}
if (!fs.mkdirs(inDir)) {
throw new IOException("Cannot create input directory " + inDir);
}

//generate an input file for each map task
try {
for(int i=0; i < numMaps; ++i) {
final Path file = new Path(inDir, "part"+i);
final LongWritable offset = new LongWritable(i * numPoints);
final LongWritable size = new LongWritable(numPoints);
final SequenceFile.Writer writer = SequenceFile.createWriter(
fs, jobConf, file,
LongWritable.class, LongWritable.class, CompressionType.NONE);
try {
writer.append(offset, size);
} finally {
writer.close();
}
System.out.println("Wrote input for Map #"+i);
}

//start a map/reduce job
System.out.println("Starting Job");
final long startTime = System.currentTimeMillis();
JobClient.runJob(jobConf);
final double duration = (System.currentTimeMillis() - startTime)/1000.0;
System.out.println("Job Finished in " + duration + " seconds");

//read outputs
Path inFile = new Path(outDir, "reduce-out");
LongWritable numInside = new LongWritable();
LongWritable numOutside = new LongWritable();
SequenceFile.Reader reader = new SequenceFile.Reader(fs, inFile, jobConf);
try {
reader.next(numInside, numOutside);
} finally {
reader.close();
}

//compute estimated value
return BigDecimal.valueOf(4).setScale(20)
.multiply(BigDecimal.valueOf(numInside.get()))
.divide(BigDecimal.valueOf(numMaps))
.divide(BigDecimal.valueOf(numPoints));
} finally {
fs.delete(TMP_DIR, true);
}
}

//Parse arguments and then runs a map/reduce job.
//Print output in standard out.
//@return a non-zero if there is an error. Otherwise, return 0.
public int run(String[] args) throws Exception {
if (args.length != 2) {
System.err.println("Usage: "+getClass().getName()+" <nMaps> <nSamples>");
ToolRunner.printGenericCommandUsage(System.err);
return -1;
}

final int nMaps = Integer.parseInt(args[0]);
final long nSamples = Long.parseLong(args[1]);

System.out.println("Number of Maps = " + nMaps);
System.out.println("Samples per Map = " + nSamples);

final JobConf jobConf = new JobConf(getConf(), getClass());
System.out.println("Estimated value of Pi is "
+ estimate(nMaps, nSamples, jobConf));
return 0;
}

//main method for running it as a stand alone command.
public static void main(String[] argv) throws Exception {
System.exit(ToolRunner.run(null, new PiEstimator(), argv));
}
}
```

## <a name="appendix-d---the-10gb-graysort-source-code"></a>付録 D -10 GB GraySort のソース コード
このセクションでは、内容を確認するために TeraSort MapReduce プログラムのコードを示します。

```java
/**
    * Licensed to the Apache Software Foundation (ASF) under one
    * or more contributor license agreements.  See the NOTICE file
    * distributed with this work for additional information
    * regarding copyright ownership.  The ASF licenses this file
    * to you under the Apache License, Version 2.0 (the
    * "License"); you may not use this file except in compliance
    * with the License.  You may obtain a copy of the License at
    *
    *     http://www.apache.org/licenses/LICENSE-2.0
    *
    * Unless required by applicable law or agreed to in writing, software
    * distributed under the License is distributed on an "AS IS" BASIS,
    * WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
    * See the License for the specific language governing permissions and
    * limitations under the License.
    */

package org.apache.hadoop.examples.terasort;

import java.io.IOException;
import java.io.PrintStream;
import java.net.URI;
import java.util.ArrayList;
import java.util.List;

import org.apache.commons.logging.Log;
import org.apache.commons.logging.LogFactory;
import org.apache.hadoop.conf.Configured;
import org.apache.hadoop.filecache.DistributedCache;
import org.apache.hadoop.fs.FileSystem;
import org.apache.hadoop.fs.Path;
import org.apache.hadoop.io.NullWritable;
import org.apache.hadoop.io.SequenceFile;
import org.apache.hadoop.io.Text;
import org.apache.hadoop.mapred.FileOutputFormat;
import org.apache.hadoop.mapred.JobClient;
import org.apache.hadoop.mapred.JobConf;
import org.apache.hadoop.mapred.Partitioner;
import org.apache.hadoop.util.Tool;
import org.apache.hadoop.util.ToolRunner;

/**
    * Generates the sampled split points, launches the job,
    * and waits for it to finish.
    * <p>
    * To run the program:
    * <b>bin/hadoop jar hadoop-examples-*.jar terasort in-dir out-dir</b>
    */

public class TeraSort extends Configured implements Tool {
    private static final Log LOG = LogFactory.getLog(TeraSort.class);

    /**
    * A partitioner that splits text keys into roughly equal
    * partitions in a global sorted order.
    */

    static class TotalOrderPartitioner implements Partitioner<Text,Text>{
    private TrieNode trie;
    private Text[] splitPoints;

    /**
        * A generic trie node
        */
    static abstract class TrieNode {
        private int level;
        TrieNode(int level) {
        this.level = level;
        }
        abstract int findPartition(Text key);
        abstract void print(PrintStream strm) throws IOException;
        int getLevel() {
        return level;
        }
    }

    /**
        * An inner trie node that contains 256 children based on the next
        * character.
        */
    static class InnerTrieNode extends TrieNode {
        private TrieNode[] child = new TrieNode[256];

        InnerTrieNode(int level) {
        super(level);
        }
        int findPartition(Text key) {
        int level = getLevel();
        if (key.getLength() <= level) {
            return child[0].findPartition(key);
        }
        return child[key.getBytes()[level]].findPartition(key);
        }
        void setChild(int idx, TrieNode child) {
        this.child[idx] = child;
        }
        void print(PrintStream strm) throws IOException {
        for(int ch=0; ch < 255; ++ch) {
            for(int i = 0; i < 2*getLevel(); ++i) {
            strm.print(' ');
            }
            strm.print(ch);
            strm.println(" ->");
            if (child[ch] != null) {
            child[ch].print(strm);
            }
        }
        }
    }

    /**
        * A leaf trie node that does string compares to figure out where the given
        * key belongs between lower..upper.
        */
    static class LeafTrieNode extends TrieNode {
        int lower;
        int upper;
        Text[] splitPoints;
        LeafTrieNode(int level, Text[] splitPoints, int lower, int upper) {
        super(level);
        this.splitPoints = splitPoints;
        this.lower = lower;
        this.upper = upper;
        }
        int findPartition(Text key) {
        for(int i=lower; i<upper; ++i) {
            if (splitPoints[i].compareTo(key) >= 0) {
            return i;
            }
        }
        return upper;
        }
        void print(PrintStream strm) throws IOException {
        for(int i = 0; i < 2*getLevel(); ++i) {
            strm.print(' ');
        }
        strm.print(lower);
        strm.print(", ");
        strm.println(upper);
        }
    }

    /**
        * Read the cut points from the given sequence file.
        * @param fs the file system
        * @param p the path to read
        * @param job the job config
        * @return the strings to split the partitions on
        * @throws IOException
        */
    private static Text[] readPartitions(FileSystem fs, Path p,
                                            JobConf job) throws IOException {
        SequenceFile.Reader reader = new SequenceFile.Reader(fs, p, job);
        List<Text> parts = new ArrayList<Text>();
        Text key = new Text();
        NullWritable value = NullWritable.get();
        while (reader.next(key, value)) {
        parts.add(key);
        key = new Text();
        }
        reader.close();
        return parts.toArray(new Text[parts.size()]);
    }

    /**
        * Given a sorted set of cut points, build a trie that will find the correct
        * partition quickly.
        * @param splits the list of cut points
        * @param lower the lower bound of partitions 0..numPartitions-1
        * @param upper the upper bound of partitions 0..numPartitions-1
        * @param prefix the prefix that we have already checked against
        * @param maxDepth the maximum depth we will build a trie for
        * @return the trie node that will divide the splits correctly
        */
    private static TrieNode buildTrie(Text[] splits, int lower, int upper,
                                        Text prefix, int maxDepth) {
        int depth = prefix.getLength();
        if (depth >= maxDepth || lower == upper) {
        return new LeafTrieNode(depth, splits, lower, upper);
        }
        InnerTrieNode result = new InnerTrieNode(depth);
        Text trial = new Text(prefix);
        // append an extra byte on to the prefix
        trial.append(new byte[1], 0, 1);
        int currentBound = lower;
        for(int ch = 0; ch < 255; ++ch) {
        trial.getBytes()[depth] = (byte) (ch + 1);
        lower = currentBound;
        while (currentBound < upper) {
            if (splits[currentBound].compareTo(trial) >= 0) {
            break;
            }
            currentBound += 1;
        }
        trial.getBytes()[depth] = (byte) ch;
        result.child[ch] = buildTrie(splits, lower, currentBound, trial,
                                        maxDepth);
        }
        // pick up the rest
        trial.getBytes()[depth] = 127;
        result.child[255] = buildTrie(splits, currentBound, upper, trial,
                                    maxDepth);
        return result;
    }

    public void configure(JobConf job) {
        try {
        FileSystem fs = FileSystem.getLocal(job);
        Path partFile = new Path(TeraInputFormat.PARTITION_FILENAME);
        splitPoints = readPartitions(fs, partFile, job);
        trie = buildTrie(splitPoints, 0, splitPoints.length, new Text(), 2);
        } catch (IOException ie) {
        throw new IllegalArgumentException("can't read paritions file", ie);
        }
    }

    public TotalOrderPartitioner() {
    }

    public int getPartition(Text key, Text value, int numPartitions) {
        return trie.findPartition(key);
    }

    }

    public int run(String[] args) throws Exception {
    LOG.info("starting");
    JobConf job = (JobConf) getConf();
    Path inputDir = new Path(args[0]);
    inputDir = inputDir.makeQualified(inputDir.getFileSystem(job));
    Path partitionFile = new Path(inputDir, TeraInputFormat.PARTITION_FILENAME);
    URI partitionUri = new URI(partitionFile.toString() +
                                "#" + TeraInputFormat.PARTITION_FILENAME);
    TeraInputFormat.setInputPaths(job, new Path(args[0]));
    FileOutputFormat.setOutputPath(job, new Path(args[1]));
    job.setJobName("TeraSort");
    job.setJarByClass(TeraSort.class);
    job.setOutputKeyClass(Text.class);
    job.setOutputValueClass(Text.class);
    job.setInputFormat(TeraInputFormat.class);
    job.setOutputFormat(TeraOutputFormat.class);
    job.setPartitionerClass(TotalOrderPartitioner.class);
    TeraInputFormat.writePartitionFile(job, partitionFile);
    DistributedCache.addCacheFile(partitionUri, job);
    DistributedCache.createSymlink(job);
    job.setInt("dfs.replication", 1);
    TeraOutputFormat.setFinalSync(job, true);
    JobClient.runJob(job);
    LOG.info("done");
    return 0;
    }

    /**
    * @param args
    */

    public static void main(String[] args) throws Exception {
    int res = ToolRunner.run(new JobConf(), new TeraSort(), args);
    System.exit(res);
    }
}
```

[hdinsight-errors]: hdinsight-debug-jobs.md

[hdinsight-sdk-documentation]: https://msdn.microsoft.com/library/azure/dn479185.aspx

[hdinsight-submit-jobs]: hdinsight-submit-hadoop-jobs-programmatically.md
[hdinsight-introduction]: hdinsight-hadoop-introduction.md

[powershell-install-configure]: /powershell/azureps-cmdlets-docs

[hdinsight-get-started]: hdinsight-hadoop-linux-tutorial-get-started.md

[hdinsight-samples]: hdinsight-run-samples.md
[hdinsight-sample-10gb-graysort]: #hdinsight-sample-10gb-graysort
[hdinsight-sample-csharp-streaming]: #hdinsight-sample-csharp-streaming
[hdinsight-sample-pi-estimator]: #hdinsight-sample-pi-estimator
[hdinsight-sample-wordcount]: #hdinsight-sample-wordcount

[hdinsight-use-hive]: hdinsight-use-hive.md
[hdinsight-use-pig]: hdinsight-use-pig.md

[streamreader]: http://msdn.microsoft.com/library/system.io.streamreader.aspx
[console-writeline]: http://msdn.microsoft.com/library/system.console.writeline
[stdin-stdout-stderr]: https://msdn.microsoft.com/library/3x292kth.aspx

