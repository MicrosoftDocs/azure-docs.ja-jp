---
title: HDInsight 上の Apache Hadoop の Apache Hive と Apache Pig で C# を使用する - Azure
description: Azure HDInsight 上の Apache Hive と Apache Pig のストリーミングで C# のユーザー定義関数 (UDF) を使用する方法について説明します。
services: hdinsight
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 02/15/2019
ms.author: hrasheed
ms.openlocfilehash: ac2edb4c12e95a915790c1fadfb2dcdcce554aad
ms.sourcegitcommit: 031e4165a1767c00bb5365ce9b2a189c8b69d4c0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/13/2019
ms.locfileid: "59545575"
---
# <a name="use-c-user-defined-functions-with-apache-hive-and-apache-pig-streaming-on-apache-hadoop-in-hdinsight"></a>HDInsight 上の Apache Hadoop の Apache Hive と Apache Pig ストリーミングで C# のユーザー定義関数 (UDF) を使用する

HDInsight 上の Apache Hive と Apache Pig で C# のユーザー定義関数 (UDF) を使用する方法について説明します。

> [!IMPORTANT]
> このドキュメントの手順は、Linux および Windows ベースの HDInsight クラスターに対してのみ有効です。 Linux は、バージョン 3.4 以上の HDInsight で使用できる唯一のオペレーティング システムです。 詳細については、「[HDInsight コンポーネントのバージョン管理](../hdinsight-component-versioning.md)」を参照してください。

Hive と Pig では、両方とも、外部のアプリケーションにデータを渡して処理できます。 このプロセスは _ストリーミング_ と呼ばれます。 .NET アプリケーションを使用する場合、データは STDIN 上のアプリケーションに渡され、そのアプリケーションから結果が STDOUT に返されます。 STDIN および STDOUT から読み取りと書き込みを実行する場合は、`Console.ReadLine()` と `Console.WriteLine()` をコンソール アプリケーションから使用できます。

## <a name="prerequisites"></a>前提条件

* .NET Framework 4.5 を対象にした C# コードの記述と構築に精通していること。

    * 必要なすべての IDE を使用します。 [Visual Studio](https://www.visualstudio.com/vs) 2015、2017、または [Visual Studio Code](https://code.visualstudio.com/) をお勧めします。 このドキュメントの手順では、Visual Studio 2017 を使用します。

* .exe ファイルをクラスターにアップロードして Pig と Hive のジョブを実行する方法。 Visual Studio、Azure PowerShell、Azure Classic CLI の Data Lake ツールをお勧めします。 このドキュメントの手順では、Visual Studio の Data Lake ツールを使用して、ファイルをアップロードし、サンプルの Hive クエリを実行します。

    Hive クエリと Pig ジョブを実行する他の方法については、次のドキュメントを参照してください。

    * [HDInsight での Apache Hive の使用](hdinsight-use-hive.md)

    * [HDInsight での Apache Pig の使用](hdinsight-use-pig.md)

* HDInsight クラスターでの Hadoop。 クラスターの作成の詳細については、「[HDInsight クラスターの作成](../hdinsight-hadoop-provision-linux-clusters.md)」を参照してください。

## <a name="net-on-hdinsight"></a>HDInsight の .NET

* __Linux ベースの HDInsight__ クラスターでは、[Mono (https://mono-project.com)](https://mono-project.com) を使用して .NET アプリケーションを実行します。 Mono バージョン 4.2.1 は HDInsight バージョン 3.6 に付属しています。

    .NET Framework のバージョンと Mono の互換性の詳細については、「[Mono compatibility](https://www.mono-project.com/docs/about-mono/compatibility/)」 (Mono の互換性) を参照してください。

* __Windows ベースの HDInsight__ クラスターでは、Microsoft .NET CLR を使用して、.NET アプリケーションを実行します。

HDInsight バージョンに付属する Mono と .NET framework のバージョンの詳細については、「[HDInsight コンポーネントのバージョン管理](../hdinsight-component-versioning.md)」を参照してください。

## <a name="create-the-c-projects"></a>C\# プロジェクトを作成する

### <a name="apache-hive-udf"></a>Apache Hive UDF

1. Visual Studio を開き、ソリューションを作成します。 プロジェクトの種類で、**[Console App (.NET Framework)]** を選択し、新しいプロジェクトに **HiveCSharp** という名前を付けます。

    > [!IMPORTANT]
    > Linux ベースの HDInsight クラスターを使用している場合は、[__.NET Framework 4.5__] を選択します。 .NET Framework のバージョンと Mono の互換性の詳細については、「[Mono compatibility](https://www.mono-project.com/docs/about-mono/compatibility/)」 (Mono の互換性) を参照してください。

2. **Program.cs** の内容を次のコードに置き換えます。

    ```csharp
    using System;
    using System.Security.Cryptography;
    using System.Text;
    using System.Threading.Tasks;

    namespace HiveCSharp
    {
        class Program
        {
            static void Main(string[] args)
            {
                string line;
                // Read stdin in a loop
                while ((line = Console.ReadLine()) != null)
                {
                    // Parse the string, trimming line feeds
                    // and splitting fields at tabs
                    line = line.TrimEnd('\n');
                    string[] field = line.Split('\t');
                    string phoneLabel = field[1] + ' ' + field[2];
                    // Emit new data to stdout, delimited by tabs
                    Console.WriteLine("{0}\t{1}\t{2}", field[0], phoneLabel, GetMD5Hash(phoneLabel));
                }
            }
            /// <summary>
            /// Returns an MD5 hash for the given string
            /// </summary>
            /// <param name="input">string value</param>
            /// <returns>an MD5 hash</returns>
            static string GetMD5Hash(string input)
            {
                // Step 1, calculate MD5 hash from input
                MD5 md5 = System.Security.Cryptography.MD5.Create();
                byte[] inputBytes = System.Text.Encoding.ASCII.GetBytes(input);
                byte[] hash = md5.ComputeHash(inputBytes);

                // Step 2, convert byte array to hex string
                StringBuilder sb = new StringBuilder();
                for (int i = 0; i < hash.Length; i++)
                {
                    sb.Append(hash[i].ToString("x2"));
                }
                return sb.ToString();
            }
        }
    }
    ```

3. プロジェクトをビルドします。

### <a name="apache-pig-udf"></a>Apache Pig UDF

1. Visual Studio を開き、ソリューションを作成します。 プロジェクトの種類で、**[コンソール アプリケーション]** を選択し、新しいプロジェクトに **PigUDF** という名前を付けます。

2. **Program.cs** ファイルの内容を次のコードに置き換えます。

    ```csharp
    using System;

    namespace PigUDF
    {
        class Program
        {
            static void Main(string[] args)
            {
                string line;
                // Read stdin in a loop
                while ((line = Console.ReadLine()) != null)
                {
                    // Fix formatting on lines that begin with an exception
                    if(line.StartsWith("java.lang.Exception"))
                    {
                        // Trim the error info off the beginning and add a note to the end of the line
                        line = line.Remove(0, 21) + " - java.lang.Exception";
                    }
                    // Split the fields apart at tab characters
                    string[] field = line.Split('\t');
                    // Put fields back together for writing
                    Console.WriteLine(String.Join("\t",field));
                }
            }
        }
    }
    ```

    このコードは、Pig から送信された行を解析し、`java.lang.Exception` で始まる行を再フォーマットします。

3. **Program.cs**を保存し、プロジェクトをビルドします。

## <a name="upload-to-storage"></a>ストレージにアップロードする

1. Visual Studio で、 **サーバー エクスプローラー**を開きます。

2. **[Azure]** を展開して、**[HDInsight]** を展開します。

3. 入力を求められた場合は、Azure サブスクリプションの資格情報を入力し、 **[サインイン]** をクリックします。

4. このアプリケーションをデプロイする HDInsight クラスターを展開します。 エントリとテキスト __(既定のストレージ アカウント)__ が一覧表示されます。

    ![クラスターのストレージ アカウントを表示するサーバー エクスプローラー](./media/apache-hadoop-hive-pig-udf-dotnet-csharp/storage.png)

    * このエントリを展開できる場合は、クラスターの既定のストレージとして __Azure Storage アカウント__ を使用します。 クラスターの既定のストレージにファイルを表示するには、エントリを展開し、__[(既定のコンテナー)]__ をダブルクリックします。

    * このエントリを展開できない場合は、クラスターの既定のストレージとして __Azure Data Lake Storage__ を使用します。 クラスターの既定のストレージにファイルを表示するには、__(既定のストレージ アカウント)__ エントリをダブルクリックします。

6. .exe ファイルをアップロードするには、次のいずれかの方法を使用します。

   * __Azure ストレージ アカウント__ を使用している場合は、アップロード アイコンをクリックし、**HiveCSharp** プロジェクトの **bin\debug** フォルダーを参照します。 最後に、**HiveCSharp.exe** ファイルを選択し、**[OK]** をクリックします。

       ![アップロード アイコン](./media/apache-hadoop-hive-pig-udf-dotnet-csharp/upload.png)
    
   * __Azure Data Lake Storage__ を使用している場合は、ファイルの一覧の空の領域を右クリックし、__[アップロード]__ を選択します。 最後に、**HiveCSharp.exe** ファイルを選択し、**[OK]** をクリックします。

     __HiveCSharp.exe__ のアップロードが完了したら、__PigUDF.exe__ ファイルのアップロード プロセスを繰り返します。

## <a name="run-an-apache-hive-query"></a>Apache Hive クエリを実行する

1. Visual Studio で、 **サーバー エクスプローラー**を開きます。

2. **[Azure]** を展開して、**[HDInsight]** を展開します。

3. **HiveCSharp** アプリケーションをデプロイしたクラスターを右クリックし、**[Write a Hive Query]** を選択します。

4. Hive クエリには、次のテキストを使用します。

    ```hiveql
    -- Uncomment the following if you are using Azure Storage
    -- add file wasb:///HiveCSharp.exe;
    -- Uncomment the following if you are using Azure Data Lake Storage Gen1
    -- add file adl:///HiveCSharp.exe;
    -- Uncomment the following if you are using Azure Data Lake Storage Gen2
    -- add file abfs:///HiveCSharp.exe;

    SELECT TRANSFORM (clientid, devicemake, devicemodel)
    USING 'HiveCSharp.exe' AS
    (clientid string, phoneLabel string, phoneHash string)
    FROM hivesampletable
    ORDER BY clientid LIMIT 50;
    ```

    > [!IMPORTANT]
    > クラスターに使用した既定のストレージの種類に一致する `add file` ステートメントのコメントを解除します。

    このクエリは、`hivesampletable` から `clientid`、`devicemake`、`devicemodel` フィールドを選択し、HiveCSharp.exe アプリケーションにそのフィールドを渡します。 クエリはアプリケーションが 3 つのフィールドを返すことを想定し、これは `clientid`、`phoneLabel`、`phoneHash` として格納されます。 このクエリでは、既定のストレージ コンテナーのルートで HiveCSharp.exe を見つけることを想定しています。

5. **[送信]** をクリックして、HDInsight クラスターにジョブを送信します。 **[Hive ジョブの概要]** ウィンドウが開きます。

6. **[更新]** をクリックし、**[ジョブの状態]** が **[完了]** に変わるまで概要を更新します。 ジョブの出力を表示するには、 **[ジョブの出力]** をクリックします。

## <a name="run-an-apache-pig-job"></a>Apache Pig ジョブを実行する

1. SSH を使用して、HDInsight クラスターに接続します。 たとえば、「 `ssh sshuser@mycluster-ssh.azurehdinsight.net` 」のように入力します。 詳細については、「[HDInsight での SSH の使用](../hdinsight-hadoop-linux-use-ssh-unix.md)」を参照してください。

2. 次のいずれかのコマンドを使用して、Pig コマンド ラインを開始します。

        pig

    > [!IMPORTANT]
    > Windows ベースのクラスターを使用している場合は、代わりに、次のコマンドを使用します。
    > ```
    > cd %PIG_HOME%
    > bin\pig
    > ```

    `grunt>` プロンプトが表示されます。

3. .NET Framework アプリケーションを使用する Pig ジョブを実行するには、次のように入力します。

        DEFINE streamer `PigUDF.exe` CACHE('/PigUDF.exe');
        LOGS = LOAD '/example/data/sample.log' as (LINE:chararray);
        LOG = FILTER LOGS by LINE is not null;
        DETAILS = STREAM LOG through streamer as (col1, col2, col3, col4, col5);
        DUMP DETAILS;

    `DEFINE`ステートメントは pigudf.exe アプリケーションに `streamer` のエイリアスを作成し、`CACHE` がクラスターの既定のストレージから読み込みます。 その後、`streamer` は `STREAM` 演算子で使用され、LOG に含まれる単一行を処理し、一連の列としてデータを返します。

    > [!NOTE]
    > ストリーミングで使用されるアプリケーション名は、エイリアスを使用する場合は \` (アクサン グラーブ) 文字、`SHIP` で使用する場合は ' (一重引用符) で囲む必要があります。

4. 最後の行を入力すると、ジョブが開始されます。 次のテキストのような出力が返されます。

        (2012-02-03 20:11:56 SampleClass5 [WARN] problem finding id 1358451042 - java.lang.Exception)
        (2012-02-03 20:11:56 SampleClass5 [DEBUG] detail for id 1976092771)
        (2012-02-03 20:11:56 SampleClass5 [TRACE] verbose detail for id 1317358561)
        (2012-02-03 20:11:56 SampleClass5 [TRACE] verbose detail for id 1737534798)
        (2012-02-03 20:11:56 SampleClass7 [DEBUG] detail for id 1475865947)

## <a name="next-steps"></a>次の手順

このドキュメントでは、HDInsight の Hive と Pig から .NET Framework アプリケーションを使用する方法について説明しました。 Hive と Pig で Python を使用する方法について学習するには、[HDInsight における Apache Hive および Apache Pig での Python の使用](python-udf-hdinsight.md)に関するページを参照してください。

Pig と Hive を使用する他の方法と、MapReduce の使用方法については、次のドキュメントをご覧ください。

* [HDInsight での Apache Hive の使用](hdinsight-use-hive.md)
* [HDInsight での Apache Pig の使用](hdinsight-use-pig.md)
* [HDInsight での MapReduce の使用](hdinsight-use-mapreduce.md)
