---
title: HDInsight の Hadoop において MapReduce で C# を使用する - Azure
description: Azure HDInsight 上の Apache Hadoop で C# を使用して MapReduce ソリューションを作成する方法について説明します。
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive, seoapr2020, devx-track-csharp
ms.date: 04/28/2020
ms.openlocfilehash: 8093db9c53d9c34014d1b315d53539b3f2cffb30
ms.sourcegitcommit: 42e4f986ccd4090581a059969b74c461b70bcac0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/23/2021
ms.locfileid: "104866711"
---
# <a name="use-c-with-mapreduce-streaming-on-apache-hadoop-in-hdinsight"></a>HDInsight 上の Apache Hadoop で C# と MapReduce ストリーミングを使用する

HDInsight で C# を使用して MapReduce ソリューションを作成する方法について説明します。

Apache Hadoop ストリーミングでは、スクリプトまたは実行可能ファイルを使用して MapReduce ジョブを実行できます。 ここでは、.NET を使用してマッパーとレジューサのワード カウント ソリューションを実装します。

## <a name="net-on-hdinsight"></a>HDInsight の .NET

HDInsight クラスターでは、[Mono (https://mono-project.com)](https://mono-project.com) を使用して .NET アプリケーションを実行します。 Mono バージョン 4.2.1 は HDInsight バージョン 3.6 に付属しています。 HDInsight に含まれる Mono のバージョンについて詳しくは、「[HDInsight の各バージョンで使用できる Apache Hadoop コンポーネント](../hdinsight-component-versioning.md)」を参照してください。

.NET Framework のバージョンと Mono の互換性の詳細については、「[Mono compatibility](https://www.mono-project.com/docs/about-mono/compatibility/)」 (Mono の互換性) を参照してください。

## <a name="how-hadoop-streaming-works"></a>Hadoop ストリーミングのしくみ

このドキュメントでストリーミングに使用する基本的なプロセスは、次のとおりです。

1. Hadoop は、STDIN のマッパー (この例では *mapper.exe*) にデータを渡します。
2. マッパーはデータを処理し、タブ区切りのキー/値ペアを STDOUT に出力します。
3. 出力は Hadoop によって読み取られ、STDIN のレジューサ (この例では *reducer.exe*) に渡されます。
4. レジューサは、タブ区切りのキー/値ペアを読み取り、データを処理した後、タブ区切りのキー/値ペアとして結果を STDOUT に出力します。
5. 出力は Hadoop によって読み取られ、出力ディレクトリに書き込まれます。

ストリーミングの詳細については、「[Hadoop Streaming](https://hadoop.apache.org/docs/r2.7.1/hadoop-streaming/HadoopStreaming.html)」(Hadoop ストリーミング) を参照してください。

## <a name="prerequisites"></a>前提条件

* 見ることができます。

* .NET Framework 4.5 を対象にした C# コードの記述と構築に精通していること。

* クラスターに .exe ファイルをアップロードする方法。 このドキュメントの手順では、Visual Studio の Data Lake ツールを使用して、クラスターのプライマリ ストレージにファイルをアップロードします。

* PowerShell を使用している場合は、[AZ モジュール](/powershell/azure/)が必要になります。

* HDInsight の Apache Hadoop クラスター。 [Linux での HDInsight の概要](../hadoop/apache-hadoop-linux-tutorial-get-started.md)に関するページを参照してください。

* クラスターのプライマリ ストレージの URI スキーム。 このスキームは、Azure Storage では `wasb://`、Azure Data Lake Storage Gen2 では `abfs://`、Azure Data Lake Storage Gen1 では `adl://` です。 Azure Storage または Data Lake Storage Gen2 で安全な転送が有効になっている場合、URI はそれぞれ `wasbs://` または `abfss://` になります。

## <a name="create-the-mapper"></a>マッパーの作成

Visual Studio で、"*マッパー*" と呼ばれる新しい .NET Framework コンソール アプリケーションを作成します。 アプリケーションには次のコードを使用します。

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

アプリケーションを作成した後、それをビルドしてプロジェクト ディレクトリに */bin/Debug/mapper.exe* ファイルを生成します。

## <a name="create-the-reducer"></a>レジューサの作成

Visual Studio で、"*レジューサ*" と呼ばれる新しい .NET Framework コンソール アプリケーションを作成します。 アプリケーションには次のコードを使用します。

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

アプリケーションを作成した後、それをビルドしてプロジェクト ディレクトリに */bin/Debug/reducer.exe* ファイルを生成します。

## <a name="upload-to-storage"></a>ストレージにアップロードする

次に、"*マッパー*" アプリケーションと "*レジューサ*" アプリケーションを HDInsight ストレージにアップロードする必要があります。

1. Visual Studio で、 **[表示]**  >  **[サーバー エクスプローラー]** を選択します。

1. **[Azure]** を右クリックし、 **[Microsoft Azure サブスクリプションへの接続]** を選択して、サインイン処理を完了します。

1. このアプリケーションをデプロイする HDInsight クラスターを展開します。 エントリとテキスト **(既定のストレージ アカウント)** が一覧表示されます。

   :::image type="content" source="./media/apache-hadoop-dotnet-csharp-mapreduce-streaming/hdinsight-storage-account.png" alt-text="ストレージ アカウント、HDInsight クラスター、サーバー エクスプローラー、Visual Studio" border="true":::

   * **[(既定のストレージ アカウント)]** エントリを展開できる場合は、クラスターの既定のストレージとして **Azure ストレージ アカウント** を使用しています。 クラスターの既定のストレージのファイルを表示するには、エントリを展開し、 **[(既定のコンテナー)]** をダブルクリックします。

   * **[(既定のストレージ アカウント)]** エントリを展開できない場合は、クラスターの既定のストレージとして **Azure Data Lake Storage** を使用しています。 クラスターの既定のストレージにファイルを表示するには、 **(既定のストレージ アカウント)** エントリをダブルクリックします。

1. .exe ファイルをアップロードするには、次のいずれかの方法を使用します。

    * **Azure ストレージ アカウント** を使用している場合は、 **[BLOB のアップロード]** アイコンを選択します。

        :::image type="content" source="./media/apache-hadoop-dotnet-csharp-mapreduce-streaming/hdinsight-upload-icon.png" alt-text="マッパーの HDInsight アップロード アイコン、Visual Studio" border="true":::

        **[新しいファイルのアップロード]** ダイアログ ボックスの **[ファイル名]** で、 **[参照]** を選択します。 **[BLOB のアップロード]** ダイアログ ボックスで、"*マッパー*" プロジェクトの *bin\debug* フォルダーに移動し、*mapper.exe* ファイルを選択します。 最後に、 **[開く]** を選択し、 **[OK]** を選択してアップロードを完了します。

    * **Azure Data Lake Storage** の場合は、ファイルの一覧の空の領域を右クリックし、 **[アップロード]** を選択します。 最後に、*mapper.exe* ファイルを選択し、 **[開く]** を選択します。

    *mapper.exe* のアップロードが完了したら、 *reducer.exe* ファイルのアップロード プロセスを繰り返します。

## <a name="run-a-job-using-an-ssh-session"></a>ジョブの実行: SSH セッションを使用

次の手順では、SSH セッションを使用して MapReduce ジョブを実行する方法について説明します。

1. [ssh コマンド](../hdinsight-hadoop-linux-use-ssh-unix.md)を使用してクラスターに接続します。 次のコマンドを編集して CLUSTERNAME をクラスターの名前に置き換えてから、そのコマンドを入力します。

    ```cmd
    ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
    ```

1. MapReduce ジョブを開始するには、次のいずれかのコマンドを使用します。

   * 既定のストレージが **Azure Storage** の場合:

        ```bash
        yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-streaming.jar \
            -files wasbs:///mapper.exe,wasbs:///reducer.exe \
            -mapper mapper.exe \
            -reducer reducer.exe \
            -input /example/data/gutenberg/davinci.txt \
            -output /example/wordcountout
        ```

    * 既定のストレージが **Data Lake Storage Gen1** の場合:

        ```bash
        yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-streaming.jar \
            -files adl:///mapper.exe,adl:///reducer.exe \
            -mapper mapper.exe \
            -reducer reducer.exe \
            -input /example/data/gutenberg/davinci.txt \
            -output /example/wordcountout
        ```

   * 既定のストレージが **Data Lake Storage Gen2** の場合:

        ```bash
        yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-streaming.jar \
            -files abfs:///mapper.exe,abfs:///reducer.exe \
            -mapper mapper.exe \
            -reducer reducer.exe \
            -input /example/data/gutenberg/davinci.txt \
            -output /example/wordcountout
        ```

   次の一覧では、各パラメーターおよびオプションによって表されているものについて説明します。

   |パラメーター | 説明 |
   |---|---|
   |hadoop-streaming.jar|ストリーミング MapReduce 機能が含まれる jar ファイルを指定します。|
   |-files|このジョブに対する *mapper.exe* ファイルと *reducer.exe* ファイルを指定します。 各ファイルの前の `wasbs:///`、`adl:///`、または `abfs:///` のプロトコル宣言は、クラスターの既定の記憶域のルートへのパスです。|
   |-mapper|マッパーが実装されているファイルを指定します。|
   |-reducer|レジューサが実装されているファイルを指定します。|
   |-input|入力データを指定します。|
   |-output|出力ディレクトリを指定します。|

1. MapReduce ジョブが完了したら、次のコマンドを使用して結果を表示します。

   ```bash
   hdfs dfs -text /example/wordcountout/part-00000
   ```

   次のテキストは、このコマンドによって返されるデータの例です。

   ```output
   you     1128
   young   38
   younger 1
   youngest        1
   your    338
   yours   4
   yourself        34
   yourselves      3
   youth   17
   ```

## <a name="run-a-job-using-powershell"></a>ジョブの実行: PowerShell の使用

次の PowerShell スクリプトを使用して、MapReduce ジョブを実行し、結果をダウンロードします。

[!code-powershell[main](../../../powershell_scripts/hdinsight/use-csharp-mapreduce/use-csharp-mapreduce.ps1?range=5-87)]

このスクリプトには、クラスター ログインのアカウント名とパスワードに加え、HDInsight のクラスター名が求められます。 ジョブが完了すると、出力が *output.txt* というファイルにダウンロードされます。 次のテキストは、`output.txt`ファイル内のデータの例です。

```output
you     1128
young   38
younger 1
youngest        1
your    338
yours   4
yourself        34
yourselves      3
youth   17
```

## <a name="next-steps"></a>次のステップ

* [HDInsight 上の Apache Hadoop で MapReduce を使用する](hdinsight-use-mapreduce.md)。
* [Apache Hive と Apache Pig で C# ユーザー定義関数を使用する](apache-hadoop-hive-pig-udf-dotnet-csharp.md)。
* [Java MapReduce プログラムを開発する](apache-hadoop-develop-deploy-java-mapreduce-linux.md)