---
title: "HDInsight の Hadoop において Hive および Pig で C# を使用する | Microsoft Docs"
description: "Azure HDInsight において Hive および Pig のストリーミングで C# ユーザー定義関数 (UDF) を使用する方法について説明します。"
services: hdinsight
documentationcenter: 
author: Blackmist
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: d83def76-12ad-4538-bb8e-3ba3542b7211
ms.service: hdinsight
ms.custom: hdinsightactive
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 03/02/2017
ms.author: larryfr
translationtype: Human Translation
ms.sourcegitcommit: 7c28fda22a08ea40b15cf69351e1b0aff6bd0a95
ms.openlocfilehash: 257138fddc75b39985ba974b1314e978a554b1e2
ms.lasthandoff: 03/07/2017


---
# <a name="use-c-user-defined-functions-with-hive-and-pig-streaming-on-hadoop-in-hdinsight"></a>HDInsight の Hadoop の Hive と Pig ストリーミングでの C# ユーザー定義関数 (UDF) の使用

Hive と Pig は Azure HDInsight でデータを処理する場合にきわめて有益ですが、より汎用的な言語が必要になる場合もあります。 Hive と Pig の両方を使用すると、ユーザー定義関数 (UDF) またはストリーミングから外部コードを呼び出すことができます。

このドキュメントでは、Hive と Pig で C# を使用する方法について説明します。

> [!IMPORTANT]
> このドキュメントの手順では、オペレーティング システムとして Windows を使用する HDInsight クラスターが必要です。 Linux は、バージョン 3.4 以上の HDInsight で使用できる唯一のオペレーティング システムです。 詳細については、[Window での HDInsight の廃止](hdinsight-component-versioning.md#hdi-version-32-and-33-nearing-deprecation-date)に関する記事を参照してください。

## <a name="prerequisites"></a>前提条件

* Windows 7 以降。

* 以下のバージョンの Visual Studio:

  * Visual Studio 2012 Professional/Premium/Ultimate の [アップデート 4](http://www.microsoft.com/download/details.aspx?id=39305)

  * Visual Studio 2013 Community/Professional/Premium/Ultimate の [アップデート 4](https://www.microsoft.com/download/details.aspx?id=44921)

  * Visual Studio 2015

  * Visual Studio 2017

* HDInsight クラスターの Hadoop - クラスターを作成する手順については、「[HDInsight クラスターのプロビジョニング](hdinsight-provision-clusters.md)」をご覧ください。

* HDInsight Tools for Visual Studio または Data Lake Tools for Visual Studio ツールをインストールして構成する手順については、「[HDInsight Tools for Visual Studio を使用して Hive クエリを実行する](hdinsight-hadoop-visual-studio-tools-get-started.md)」を参照してください。

    > [!NOTE]
    > Data Lake Tools は、インストール時に __Azure 開発__ワークロードを選択することによって Visual Studio 2017 にインストールされます。

## <a name="net-on-hdinsight"></a>HDInsight の .NET

Windows ベースの HDInsight クラスターには、.NET 共通言語ランタイム (CLR) とフレームワークが既定でインストールされています。 .NET CLR を使用すると、Hive と Pig のストリーミングで C# アプリケーションを使用できます (データは Hive/Pig と C# アプリケーション間で stdout/stdin を使用して渡されます)。

> [!NOTE]
> 現在、Linux ベースの HDInsight クラスターでは、.NET Framework UDF の実行はサポートされていません。


## <a name="net-and-streaming"></a>.NET とストリーミング

ストリーミングには Hive と Pig が含まれており、stdout を使用して外部のアプリケーションにデータを渡し、stdin を使用して結果を受け取ります。 C# アプリケーションの場合、`Console.ReadLine()` および `Console.WriteLine()` を使用します。

Hive と Pig は実行時にアプリケーションを起動する必要があるため、C# プロジェクトには、**Console App (.NET Framework)** テンプレートを使用する必要があります。

## <a name="hive-and-c35"></a>Hive と C#

### <a name="create-the-c-project"></a>C# プロジェクトを作成する

1. Visual Studio を開き、ソリューションを作成します。 プロジェクトの種類で、**[Console App (.NET Framework)]** を選択し、新しいプロジェクトに **HiveCSharp** という名前を付けます。

2. **Program.cs** の内容を次の内容に置き換えます。

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

### <a name="upload-to-storage"></a>ストレージにアップロードする

1. Visual Studio で、 **サーバー エクスプローラー**を開きます。

2. **[Azure]** を展開して、**[HDInsight]** を展開します。

3. 入力を求められた場合は、Azure サブスクリプションの資格情報を入力し、 **[サインイン]**をクリックします。

4. このアプリケーションをデプロイする HDInsight クラスターを展開し、 **[既定のストレージ アカウント]**を展開します。

    ![クラスターのストレージ アカウントを表示するサーバー エクスプローラー](./media/hdinsight-hadoop-hive-pig-udf-dotnet-csharp/storage.png)

5. クラスターの**既定のコンテナー**をダブルクリックして、既定のコンテナーの内容を表示します。
6. アップロード アイコンをクリックし、**HiveCSharp** プロジェクトの **bin\debug** フォルダーを参照します。 最後に、**HiveCSharp.exe** ファイルを選択し、**[OK]** をクリックします。

    ![アップロード アイコン](./media/hdinsight-hadoop-hive-pig-udf-dotnet-csharp/upload.png)

7. アップロードが完了すると、Hive クエリからアプリケーションを使用できるようになります。

### <a name="hive-query"></a>Hive クエリ

1. Visual Studio で、 **サーバー エクスプローラー**を開きます。

2. **[Azure]** を展開して、**[HDInsight]** を展開します。

3. **HiveCSharp** アプリケーションをデプロイしたクラスターを右クリックし、**[Write a Hive Query]** を選択します。

4. Hive クエリには、次のテキストを使用します。

    ```hiveql
    add file wasbs:///HiveCSharp.exe;

    SELECT TRANSFORM (clientid, devicemake, devicemodel)
    USING 'HiveCSharp.exe' AS
    (clientid string, phoneLabel string, phoneHash string)
    FROM hivesampletable
    ORDER BY clientid LIMIT 50;
    ```

    このクエリは、`hivesampletable` から `clientid`、`devicemake`、`devicemodel` フィールドを選択し、HiveCSharp.exe アプリケーションにそのフィールドを渡します。 クエリはアプリケーションが&3; つのフィールドを返すことを想定し、これは `clientid`、`phoneLabel`、`phoneHash` として格納されます。 また、このクエリは既定のストレージ コンテナー (`add file wasbs:///HiveCSharp.exe`) のルートで HiveCSharp.exe を見つけることを想定しています。

5. **[送信]** をクリックして、HDInsight クラスターにジョブを送信します。 **[Hive ジョブの概要]** ウィンドウが開きます。

6. **[更新]** をクリックし、**[ジョブの状態]** が **[完了]** に変わるまで概要を更新します。 ジョブの出力を表示するには、 **[ジョブの出力]**をクリックします。

## <a name="pig-and-c35"></a>Pig と C#

### <a name="create-the-c-project"></a>C# プロジェクトを作成する

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

    このアプリケーションは、Pig から送信された行を解析し、`java.lang.Exception` で始まる行を再フォーマットします。

3. **Program.cs**を保存し、プロジェクトをビルドします。

### <a name="upload-the-application"></a>アプリケーションをアップロードする

1. pig ストリーミングは、アプリケーションがクラスター ファイル システムでローカルであると想定しています。 「[RDP を使用した HDInsight クラスターへの接続](hdinsight-administer-use-management-portal.md#connect-to-clusters-using-rdp)」の手順に従って、HDInsight クラスターのリモート デスクトップを有効にしてからデスクトップに接続します。

2. 接続されたら、ローカル コンピューターの PigUDF プロジェクトの **bin/debug** ディレクトリから **PigUDF.exe** をコピーし、クラスターの **%PIG_HOME%** ディレクトリに貼り付けます。

### <a name="use-the-application-from-pig-latin"></a>Pig Latin のアプリケーションを使用する

1. リモート デスクトップ セッションから、デスクトップ上の **[Hadoop コマンド ライン]** アイコンを使用して Hadoop コマンド ラインを開始します。

2. Pig コマンド ラインを開始するには次のコマンドを使用します。

        cd %PIG_HOME%
        bin\pig

    `grunt>` プロンプトが表示されます。
    
3. .NET Framework アプリケーションを使用して Pig ジョブを実行するには、次のコマンドを入力します。

        DEFINE streamer `pigudf.exe` SHIP('pigudf.exe');
        LOGS = LOAD 'wasbs:///example/data/sample.log' as (LINE:chararray);
        LOG = FILTER LOGS by LINE is not null;
        DETAILS = STREAM LOG through streamer as (col1, col2, col3, col4, col5);
        DUMP DETAILS;

    `DEFINE` ステートメントは pigudf.exe アプリケーションに `streamer` のエイリアスを作成し、`SHIP` はこれをクラスター内のノードに配布します。 その後、`streamer` は `STREAM` 演算子で使用され、LOG に含まれる単一行を処理し、一連の列としてデータを返します。

    > [!NOTE]
    > ストリーミングで使用されるアプリケーション名は、エイリアスを使用する場合は \` (アクサン グラーブ) 文字、`SHIP` で使用する場合は ' (一重引用符) で囲む必要があります。

4. 最後の行を入力すると、ジョブが開始されます。 次のテキストのような出力が返されます。

        (2012-02-03 20:11:56 SampleClass5 [WARN] problem finding id 1358451042 - java.lang.Exception)
        (2012-02-03 20:11:56 SampleClass5 [DEBUG] detail for id 1976092771)
        (2012-02-03 20:11:56 SampleClass5 [TRACE] verbose detail for id 1317358561)
        (2012-02-03 20:11:56 SampleClass5 [TRACE] verbose detail for id 1737534798)
        (2012-02-03 20:11:56 SampleClass7 [DEBUG] detail for id 1475865947)

## <a name="summary"></a>概要

このドキュメントでは、HDInsight の Hive と Pig から .NET Framework アプリケーションを使用する方法について説明しました。 Hive と Pig で Python を使用する方法について学習するには、「[HDInsight における Python と Hive および Pig の使用](hdinsight-python.md)」をご覧ください。

Pig と Hive を使用する他の方法と、MapReduce の使用方法については、次のドキュメントをご覧ください。

* [HDInsight での Hive の使用](hdinsight-use-hive.md)
* [HDInsight の Hadoop での Pig の使用](hdinsight-use-pig.md)
* [HDInsight での MapReduce の使用](hdinsight-use-mapreduce.md)

