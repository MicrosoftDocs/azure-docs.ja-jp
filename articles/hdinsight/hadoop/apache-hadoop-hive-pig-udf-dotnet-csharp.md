---
title: Apache Hadoop 上での C#、Apache Hive、Apache Pig - Azure HDInsight
description: Azure HDInsight 上の Apache Hive と Apache Pig のストリーミングで C# のユーザー定義関数 (UDF) を使用する方法について説明します。
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive, devx-track-csharp
ms.date: 12/06/2019
ms.openlocfilehash: b5682ff58ad827f5a165342f11f03fb49bbe6d2d
ms.sourcegitcommit: 42e4f986ccd4090581a059969b74c461b70bcac0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/23/2021
ms.locfileid: "104867884"
---
# <a name="use-c-user-defined-functions-with-apache-hive-and-apache-pig-on-apache-hadoop-in-hdinsight"></a>HDInsight 上の Apache Hadoop の Apache Hive と Apache Pig で C# のユーザー定義関数を使用する

HDInsight 上の [Apache Hive](https://hive.apache.org) と [Apache Pig](https://pig.apache.org) で C# のユーザー定義関数 (UDF) を使用する方法について説明します。

> [!IMPORTANT]
> このドキュメントの手順は、Linux ベースの HDInsight クラスターに対してのみ有効です。 Linux は、バージョン 3.4 以上の HDInsight で使用できる唯一のオペレーティング システムです。 詳細については、「[HDInsight コンポーネントのバージョン管理](../hdinsight-component-versioning.md)」を参照してください。

Hive と Pig では、両方とも、外部のアプリケーションにデータを渡して処理できます。 このプロセスは _ストリーミング_ と呼ばれます。 .NET アプリケーションを使用する場合、データは STDIN 上のアプリケーションに渡され、そのアプリケーションから結果が STDOUT に返されます。 STDIN および STDOUT から読み取りと書き込みを実行する場合は、`Console.ReadLine()` と `Console.WriteLine()` をコンソール アプリケーションから使用できます。

## <a name="prerequisites"></a>前提条件

* .NET Framework 4.5 を対象にした C# コードの記述と構築に精通していること。

    必要なすべての IDE を使用します。 [Visual Studio](https://www.visualstudio.com/vs)または [Visual Studio Code](https://code.visualstudio.com/) をお勧めします。 このドキュメントの手順では、Visual Studio 2019 を使用します。

* .exe ファイルをクラスターにアップロードして Pig と Hive のジョブを実行する方法。 [Data Lake Tools for Visual Studio](../../data-lake-analytics/data-lake-analytics-data-lake-tools-install.md)、[Azure PowerShell](/powershell/azure)、[Azure CLI](/cli/azure/install-azure-cli) をお勧めします。 このドキュメントの手順では、Visual Studio の Data Lake ツールを使用して、ファイルをアップロードし、サンプルの Hive クエリを実行します。

    Hive クエリを実行する他の方法については、「[Azure HDInsight における Apache Hive と HiveQL](hdinsight-use-hive.md)」を参照してください。

* HDInsight クラスターでの Hadoop。 クラスターの作成の詳細については、「[HDInsight クラスターの作成](../hdinsight-hadoop-provision-linux-clusters.md)」を参照してください。

## <a name="net-on-hdinsight"></a>HDInsight の .NET

*Linux ベースの HDInsight* クラスターでは、[Mono (https://mono-project.com)](https://mono-project.com) を使用して .NET アプリケーションを実行します。 Mono バージョン 4.2.1 は HDInsight バージョン 3.6 に付属しています。

.NET Framework のバージョンと Mono の互換性の詳細については、「[Mono compatibility](https://www.mono-project.com/docs/about-mono/compatibility/)」 (Mono の互換性) を参照してください。

HDInsight バージョンに付属する Mono と .NET Framework のバージョンの詳細については、[HDInsight コンポーネントのバージョン](../hdinsight-component-versioning.md)に関するページを参照してください。

## <a name="create-the-c-projects"></a>C\# プロジェクトを作成する

以下のセクションでは、Apache Hive UDF と Apache Pig UDF 用に Visual Studio で C# プロジェクトを作成する方法について説明します。

### <a name="apache-hive-udf"></a>Apache Hive UDF

Apache Hive UDF の C# プロジェクトを作成するには、次のようにします。

1. Visual Studio を起動します。

2. **[新しいプロジェクトの作成]** を選択します。

3. **[新しいプロジェクトの作成]** ウィンドウで、**[コンソール アプリ (.NET Framework)]** テンプレート (C# バージョン) を選択します。 次に、**[次へ]** を選択します。

4. **[新しいプロジェクトを構成します]** ウィンドウで、*HiveCSharp* の **プロジェクト名** を入力し、新しいプロジェクトを保存する **場所** に移動または場所を作成します。 **[作成]** を選択します。

5. Visual Studio IDE で、*Program.cs* の内容を次のコードに置き換えます。

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

6. メニュー バーで **[ビルド]**  >  **[ソリューションのビルド]** の順に選択して、プロジェクトをビルドします。

7. ソリューションを閉じます。

### <a name="apache-pig-udf"></a>Apache Pig UDF

Apache Hive UDF の C# プロジェクトを作成するには、次のようにします。

1. Visual Studio を開きます。

2. **[開始]** ウィンドウで、 **[新しいプロジェクトの作成]** を選択します。

3. **[新しいプロジェクトの作成]** ウィンドウで、**[コンソール アプリ (.NET Framework)]** テンプレート (C# バージョン) を選択します。 次に、**[次へ]** を選択します。

4. **[新しいプロジェクトを構成します]** ウィンドウで、*PigUDF* の **プロジェクト名** を入力し、新しいプロジェクトを保存する **場所** に移動または場所を作成します。 **[作成]** を選択します。

5. Visual Studio IDE で、*Program.cs* の内容を次のコードに置き換えます。

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

6. メニュー バーから **[ビルド]**  >  **[ソリューションのビルド]** を選択してプロジェクトをビルドします。

7. ソリューションを開いたままにしておきます。

## <a name="upload-to-storage"></a>ストレージにアップロードする

次に、Hive と Pig の UDF アプリケーションを HDInsight クラスター上のストレージにアップロードします。

1. Visual Studio で、 **[表示]**  >  **[サーバー エクスプローラー]** の順に移動します。

1. **[サーバー エクスプローラー]** で、**[Azure]** を右クリックし、**[Microsoft Azure サブスクリプションへの接続]** を選択し、サインイン処理を完了します。

1. このアプリケーションをデプロイする HDInsight クラスターを展開します。 エントリとテキスト **(既定のストレージ アカウント)** が一覧表示されます。

    :::image type="content" source="./media/apache-hadoop-hive-pig-udf-dotnet-csharp/hdinsight-storage-account.png" alt-text="既定のストレージ アカウント、HDInsight クラスター、サーバー エクスプローラー" border="true":::

    * このエントリを展開できる場合は、クラスターの既定のストレージとして **Azure ストレージ アカウント** を使用します。 クラスターの既定のストレージにファイルを表示するには、エントリを展開し、**[(既定のコンテナー)]** をダブルクリックします。

    * このエントリを展開できない場合は、クラスターの既定のストレージとして **Azure Data Lake Storage** を使用します。 クラスターの既定のストレージにファイルを表示するには、 **(既定のストレージ アカウント)** エントリをダブルクリックします。

1. .exe ファイルをアップロードするには、次のいずれかの方法を使用します。

    * **Azure ストレージ アカウント** を使用している場合は、 **[BLOB のアップロード]** アイコンを選択します。

        :::image type="content" source="./media/apache-hadoop-hive-pig-udf-dotnet-csharp/hdinsight-upload-icon.png" alt-text="新しいプロジェクトの HDInsight アップロード アイコン" border="true":::

        **[新しいファイルのアップロード]** ダイアログ ボックスの **[ファイル名]** で、 **[参照]** を選択します。 **[BLOB のアップロード]** ダイアログ ボックスで、*HiveCSharp* プロジェクトの *bin\debug* フォルダーに移動し、*HiveCSharp.exe* ファイルを選択します。 最後に、 **[開く]** を選択し、 **[OK]** を選択してアップロードを完了します。

    * **Azure Data Lake Storage** を使用している場合は、ファイルの一覧の空の領域を右クリックし、**[アップロード]** を選択します。 最後に、*HiveCSharp.exe* ファイルを選び、**[OK]** を選択します。

    *HiveCSharp.exe* のアップロードが完了したら、*PigUDF.exe* ファイルのアップロード プロセスを繰り返します。

## <a name="run-an-apache-hive-query"></a>Apache Hive クエリを実行する

Hive UDF アプリケーションを使用する Hive クエリを実行できるようになりました。

1. Visual Studio で、 **[表示]**  >  **[サーバー エクスプローラー]** の順に移動します。

2. **[Azure]** を展開して、**[HDInsight]** を展開します。

3. *HiveCSharp* アプリケーションをデプロイしたクラスターを右クリックし、**[Write a Hive Query]** を選択します。

4. Hive クエリには、次のテキストを使用します。

    ```hiveql
    -- Uncomment the following if you are using Azure Storage
    -- add file wasbs:///HiveCSharp.exe;
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

    このクエリは、`hivesampletable` から `clientid`、`devicemake`、`devicemodel` の各フィールドを選択し、*HiveCSharp.exe* アプリケーションに選択したフィールドを渡します。 クエリはアプリケーションが 3 つのフィールドを返すことを想定し、これは `clientid`、`phoneLabel`、`phoneHash` として格納されます。 このクエリでは、既定のストレージ コンテナーのルートで *HiveCSharp.exe* が見つかることを想定しています。

5. 既定の **[対話型]** を **[バッチ]** に切り替えてから、**[送信]** を選択して、ジョブを HDInsight クラスターに送信します。 **[Hive ジョブの概要]** ウィンドウが開きます。

6. **[更新]** を選択し、**[ジョブの状態]** が **[完了]** に変わるまで概要を更新します。 ジョブの出力を表示するには、**[ジョブの出力]** を選択します。

## <a name="run-an-apache-pig-job"></a>Apache Pig ジョブを実行する

Pig UDF アプリケーションを使用する Pig ジョブを実行することもできます。

1. SSH を使用して、HDInsight クラスターに接続します。 (たとえば、コマンド `ssh sshuser@<clustername>-ssh.azurehdinsight.net` を実行します。) 詳細については、[HDInsight での SSH の使用](../hdinsight-hadoop-linux-use-ssh-unix.md)に関するページを参照してください。

2. 次のコマンドを使用して、Pig コマンド ラインを開始します。

    ```shell
    pig
    ```

    `grunt>` プロンプトが表示されます。

3. .NET Framework アプリケーションを使用する Pig ジョブを実行するには、次のように入力します。

    ```pig
    DEFINE streamer `PigUDF.exe` CACHE('/PigUDF.exe');
    LOGS = LOAD '/example/data/sample.log' as (LINE:chararray);
    LOG = FILTER LOGS by LINE is not null;
    DETAILS = STREAM LOG through streamer as (col1, col2, col3, col4, col5);
    DUMP DETAILS;
    ```

    `DEFINE` ステートメントで *PigUDF.exe* アプリケーションの `streamer` というエイリアスを作成し、それを `CACHE` でクラスターの既定のストレージから読み込みます。 その後、`streamer` が `STREAM` 演算子で使用され、`LOG` に含まれる単一行が処理されて、一連の列としてデータが返されます。

    > [!NOTE]
    > ストリーミングで使用されるアプリケーション名は、エイリアスを使用する場合は \` (アクサン グラーブ) 文字、`SHIP` で使用する場合は ' (一重引用符) 文字で囲む必要があります。

4. 最後の行を入力すると、ジョブが開始されます。 次のテキストのような出力が返されます。

    ```output
    (2019-07-15 16:43:25 SampleClass5 [WARN] problem finding id 1358451042 - java.lang.Exception)
    (2019-07-15 16:43:25 SampleClass5 [DEBUG] detail for id 1976092771)
    (2019-07-15 16:43:25 SampleClass5 [TRACE] verbose detail for id 1317358561)
    (2019-07-15 16:43:25 SampleClass5 [TRACE] verbose detail for id 1737534798)
    (2019-07-15 16:43:25 SampleClass7 [DEBUG] detail for id 1475865947)
    ```

5. `exit` を使用して、pig を終了します。

## <a name="next-steps"></a>次のステップ

このドキュメントでは、HDInsight の Hive と Pig から .NET Framework アプリケーションを使用する方法について説明しました。 Hive と Pig で Python を使用する方法について学習するには、[HDInsight における Apache Hive および Apache Pig での Python の使用](python-udf-hdinsight.md)に関するページを参照してください。

Hive を使用する他の方法と、MapReduce の使用方法については、以下の記事をご覧ください。

* [HDInsight での Apache Hive の使用](hdinsight-use-hive.md)
* [HDInsight での MapReduce の使用](hdinsight-use-mapreduce.md)
* [Pig Latin の基本](https://pig.apache.org/docs/latest/basic.html)
