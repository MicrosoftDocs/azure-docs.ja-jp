---
title: HDInsight の Hadoop において MapReduce で C# を使用する - Azure
description: Azure HDInsight 上の Apache Hadoop で C# を使用して MapReduce ソリューションを作成する方法について説明します。
author: hrasheed-msft
ms.reviewer: jasonh
ms.custom: hdinsightactive
ms.service: hdinsight
ms.topic: conceptual
ms.date: 02/15/2019
ms.author: hrasheed
ms.openlocfilehash: 5784fb4f4ab0f46d2db7e5e8cfe9deeafabb4e90
ms.sourcegitcommit: f209d0dd13f533aadab8e15ac66389de802c581b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/17/2019
ms.locfileid: "71066957"
---
# <a name="use-c-with-mapreduce-streaming-on-apache-hadoop-in-hdinsight"></a>HDInsight 上の Apache Hadoop で C# と MapReduce ストリーミングを使用する

HDInsight で C# を使用して MapReduce ソリューションを作成する方法について説明します。

> [!IMPORTANT]
> Linux は、バージョン 3.4 以上の HDInsight で使用できる唯一のオペレーティング システムです。 詳細については、「[HDInsight コンポーネントのバージョン管理](../hdinsight-component-versioning.md)」を参照してください。

Apache Hadoop ストリーミングは、スクリプトまたは実行可能ファイルを使用して MapReduce ジョブを実行するためのユーティリティです。 この例では、.NET を使用してマッパーとレジューサのワード カウント ソリューションを実装します。

## <a name="net-on-hdinsight"></a>HDInsight の .NET

__Linux ベースの HDInsight__ クラスターでは、[Mono (https://mono-project.com)](https://mono-project.com) を使用して .NET アプリケーションを実行します。 Mono バージョン 4.2.1 は HDInsight バージョン 3.6 に付属しています。 HDInsight に付属する Mono のバージョンの詳細については、「[HDInsight コンポーネントのバージョン管理](../hdinsight-component-versioning.md)」を参照してください。 

.NET Framework のバージョンと Mono の互換性に関する詳細は、「[Mono の互換性](https://www.mono-project.com/docs/about-mono/compatibility/)」を参照してください。

## <a name="how-hadoop-streaming-works"></a>Hadoop ストリーミングのしくみ

このドキュメントでストリーミングに使用する基本的なプロセスは、次のとおりです。

1. Hadoop は、STDIN のマッパー (この例では mapper.exe) にデータを渡します。
2. マッパーはデータを処理し、タブ区切りのキー/値ペアを STDOUT に出力します。
3. 出力は Hadoop によって読み取られ、STDIN のレジューサ (この例では reducer.exe) に渡されます。
4. レジューサは、タブ区切りのキー/値ペアを読み取り、データを処理した後、タブ区切りのキー/値ペアとして結果を STDOUT に出力します。
5. 出力は Hadoop によって読み取られ、出力ディレクトリに書き込まれます。

ストリーミングの詳細については、「[Hadoop Streaming](https://hadoop.apache.org/docs/r2.7.1/hadoop-streaming/HadoopStreaming.html)」(Hadoop ストリーミング) を参照してください。

## <a name="prerequisites"></a>前提条件

* .NET Framework 4.5 を対象にした C# コードの記述と構築に精通していること。 このドキュメントの手順では、Visual Studio 2017 を使用します。

* クラスターに .exe ファイルをアップロードする方法。 このドキュメントの手順では、Visual Studio の Data Lake ツールを使用して、クラスターのプライマリ ストレージにファイルをアップロードします。

* Azure PowerShell または SSH クライアント。

* HDInsight クラスターでの Hadoop。 クラスター作成の詳細については、「[HDInsight クラスターの作成](../hdinsight-hadoop-provision-linux-clusters.md)」を参照してください。

## <a name="create-the-mapper"></a>マッパーの作成

Visual Studio で、__マッパー__ と呼ばれる新しい  __コンソール アプリケーション__ を作成します。 アプリケーションには次のコードを使用します。

```csharp
using System;
using System.Text.RegularExpressions;

namespace mapper
{
    class Program
    {
        static void Main(string[] args)
        {
            string line;
            //Hadoop passes data to the mapper on STDIN
            while((line = Console.ReadLine()) != null)
            {
                // We only want words, so strip out punctuation, numbers, etc.
                var onlyText = Regex.Replace(line, @"\.|;|:|,|[0-9]|'", "");
                // Split at whitespace.
                var words = Regex.Matches(onlyText, @"[\w]+");
                // Loop over the words
                foreach(var word in words)
                {
                    //Emit tab-delimited key/value pairs.
                    //In this case, a word and a count of 1.
                    Console.WriteLine("{0}\t1",word);
                }
            }
        }
    }
}
```

アプリケーションの作成後は、構築してプロジェクト ディレクトリに `/bin/Debug/mapper.exe` ファイルを生成します。

## <a name="create-the-reducer"></a>レジューサの作成

Visual Studio で、__レジューサ__ と呼ばれる新しい __コンソール アプリケーション__ を作成します。 アプリケーションには次のコードを使用します。

```csharp
using System;
using System.Collections.Generic;

namespace reducer
{
    class Program
    {
        static void Main(string[] args)
        {
            //Dictionary for holding a count of words
            Dictionary<string, int> words = new Dictionary<string, int>();

            string line;
            //Read from STDIN
            while ((line = Console.ReadLine()) != null)
            {
                // Data from Hadoop is tab-delimited key/value pairs
                var sArr = line.Split('\t');
                // Get the word
                string word = sArr[0];
                // Get the count
                int count = Convert.ToInt32(sArr[1]);

                //Do we already have a count for the word?
                if(words.ContainsKey(word))
                {
                    //If so, increment the count
                    words[word] += count;
                } else
                {
                    //Add the key to the collection
                    words.Add(word, count);
                }
            }
            //Finally, emit each word and count
            foreach (var word in words)
            {
                //Emit tab-delimited key/value pairs.
                //In this case, a word and a count of 1.
                Console.WriteLine("{0}\t{1}", word.Key, word.Value);
            }
        }
    }
}
```

アプリケーションの作成後は、構築してプロジェクト ディレクトリに `/bin/Debug/reducer.exe` ファイルを生成します。

## <a name="upload-to-storage"></a>ストレージにアップロードする

1. Visual Studio で、 **サーバー エクスプローラー**を開きます。

2. **[Azure]** を展開して、 **[HDInsight]** を展開します。

3. 入力を求められた場合は、Azure サブスクリプションの資格情報を入力し、 **[サインイン]** をクリックします。

4. このアプリケーションをデプロイする HDInsight クラスターを展開します。 エントリとテキスト __(既定のストレージ アカウント)__ が一覧表示されます。

    ![クラスターのストレージ アカウントを表示するサーバー エクスプローラー](./media/apache-hadoop-dotnet-csharp-mapreduce-streaming/hdinsight-storage-account.png)

    * このエントリを展開できる場合は、クラスターの既定のストレージとして __Azure Storage アカウント__ を使用します。 クラスターの既定のストレージにファイルを表示するには、エントリを展開し、 __[(既定のコンテナー)]__ をダブルクリックします。

    * このエントリを展開できない場合は、クラスターの既定のストレージとして __Azure Data Lake Storage__ を使用します。 クラスターの既定のストレージにファイルを表示するには、 __(既定のストレージ アカウント)__ エントリをダブルクリックします。

5. .exe file をアップロードするには、次のいずれかの手順に従ってください。

   * __Azure Storage アカウント__ を使用している場合は、アップロード アイコンをクリックし、**マッパー** プロジェクトの **bin\debug** フォルダーを参照します。 最後に、**mapper.exe** ファイルを選択し、 **[OK]** をクリックします。

        ![マッパーの HDInsight アップロード アイコン](./media/apache-hadoop-dotnet-csharp-mapreduce-streaming/hdinsight-upload-icon.png)
    
   * __Azure Data Lake Storage__ を使用している場合は、ファイルの一覧の空の領域を右クリックし、 __[アップロード]__ を選択します。 最後に、**mapper.exe** ファイルを選択し、 **[開く]** をクリックします。

     __mapper.exe__ のアップロードが完了したら、 __reducer.exe__ ファイルのアップロード プロセスを繰り返します。

## <a name="run-a-job-using-an-ssh-session"></a>ジョブの実行: SSH セッションを使用

1. SSH を使用して、HDInsight クラスターに接続します。 詳細については、[HDInsight での SSH の使用](../hdinsight-hadoop-linux-use-ssh-unix.md)に関するページを参照してください。

2. MapReduce ジョブを開始するには、次のいずれかのコマンドを使用します。

   * 既定のストレージとして __Data Lake Storage Gen2__ を使用している場合:

       ```bash
       yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-streaming.jar -files abfs:///mapper.exe,abfs:///reducer.exe -mapper mapper.exe -reducer reducer.exe -input /example/data/gutenberg/davinci.txt -output /example/wordcountout
       ```

   * 既定のストレージとして __Data Lake Storage Gen1__ を使用している場合:

       ```bash
       yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-streaming.jar -files adl:///mapper.exe,adl:///reducer.exe -mapper mapper.exe -reducer reducer.exe -input /example/data/gutenberg/davinci.txt -output /example/wordcountout
       ```
    
   * 既定のストレージとして __Azure Storage__ を使用している場合:

       ```bash
       yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-streaming.jar -files wasb:///mapper.exe,wasb:///reducer.exe -mapper mapper.exe -reducer reducer.exe -input /example/data/gutenberg/davinci.txt -output /example/wordcountout
       ```

     次の一覧に、各パラメーターの動作を示します。

   * `hadoop-streaming.jar`:ストリーミング MapReduce 機能を含む jar ファイル。
   * `-files`:このジョブに `mapper.exe` および `reducer.exe` ファイルを追加します。 各ファイルの前の `abfs:///`、`adl:///`、または `wasb:///` は、クラスターの既定の記憶域のルートへのパスです。
   * `-mapper`:マッパーを実装するファイルを指定します。
   * `-reducer`:レジューサを実装するファイルを指定します。
   * `-input`:入力データ。
   * `-output`:出力ディレクトリ。

3. MapReduce ジョブが完了したら、次のコマンドを使用して結果を表示します。

    ```bash
    hdfs dfs -text /example/wordcountout/part-00000
    ```

    次のテキストは、このコマンドによって返されるデータの例です。

        you     1128
        young   38
        younger 1
        youngest        1
        your    338
        yours   4
        yourself        34
        yourselves      3
        youth   17

## <a name="run-a-job-using-powershell"></a>ジョブの実行: PowerShell の使用

次の PowerShell スクリプトを使用して、MapReduce ジョブを実行し、結果をダウンロードします。

[!code-powershell[main](../../../powershell_scripts/hdinsight/use-csharp-mapreduce/use-csharp-mapreduce.ps1?range=5-87)]

このスクリプトには、クラスター ログインのアカウント名とパスワードに加え、HDInsight のクラスター名が求められます。 ジョブが完了すると、出力が `output.txt` というファイルにダウンロードされます。 次のテキストは、`output.txt`ファイル内のデータの例です。

    you     1128
    young   38
    younger 1
    youngest        1
    your    338
    yours   4
    yourself        34
    yourselves      3
    youth   17

## <a name="next-steps"></a>次の手順

HDInsight での MapReduce の使用方法の詳細については、[HDInsight での MapReduce の使用](hdinsight-use-mapreduce.md)に関するページを参照してください。

Hive および Pig での C# の使用については、[Apache Hive および Apache Pig での C# ユーザー定義関数の使用](apache-hadoop-hive-pig-udf-dotnet-csharp.md)に関するページを参照してください。

HDInsight における Storm での C# の使用については、[HDInsight での Apache Storm の C# トポロジ開発](../storm/apache-storm-develop-csharp-visual-studio-topology.md)を参照してください。
