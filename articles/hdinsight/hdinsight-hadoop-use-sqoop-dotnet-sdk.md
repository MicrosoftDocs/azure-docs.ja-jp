---
title: ".NET と HDInsight を使用して Sqoop ジョブを実行する - Azure | Microsoft Docs"
description: "HDInsight .NET SDK を使用して、Hadoop クラスターと Azure SQL Database 間で Sqoop のインポートおよびエクスポートを実行する方法について説明します。"
keywords: "sqoop ジョブ"
editor: cgronlun
manager: jhubbard
services: hdinsight
documentationcenter: 
tags: azure-portal
author: mumian
ms.assetid: 87bacd13-7775-4b71-91da-161cb6224a96
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/25/2017
ms.author: jgao
ms.translationtype: Human Translation
ms.sourcegitcommit: 245ce9261332a3d36a36968f7c9dbc4611a019b2
ms.openlocfilehash: c95641fc6d20e2911e007d1974b9e2c2398b3133
ms.contentlocale: ja-jp
ms.lasthandoff: 06/09/2017


---
# <a name="run-sqoop-jobs-using-net-sdk-for-hadoop-in-hdinsight"></a>HDInsight で .NET SDK for Hadoop を使用した Sqoop ジョブの実行
[!INCLUDE [sqoop-selector](../../includes/hdinsight-selector-use-sqoop.md)]

HDInsight .NET SDK を使用して、HDInsight クラスターと Azure SQL Database または SQL Server データベース間でインポートとエクスポートを実行する Sqoop ジョブを HDInsight で実行する方法について説明します。

> [!NOTE]
> この記事の手順は、Windows ベースまたは Linux ベースの HDInsight クラスターで使用できます。ただし、これらの手順は Windows クライアントでのみ機能します。 他の方法を選択するには、この記事の上部にあるタブ セレクターをクリックしてください。
> 
> 

### <a name="prerequisites"></a>前提条件
このチュートリアルを開始する前に、次の項目を用意する必要があります。

* **HDInsight の Hadoop クラスター**。 「 [クラスターと SQL Database を作成する](hdinsight-use-sqoop.md#create-cluster-and-sql-database)」を参照してください。

## <a name="use-sqoop-on-hdinsight-clusters-using-net-sdk"></a>.NET SDK を使って HDInsight クラスターで Sqoop を使用する
HDInsight .NET SDK は、.NET から HDInsight クラスターを簡単に操作できる .NET クライアント ライブラリを提供します。 このセクションでは、C# コンソール アプリケーションを作成し、このチュートリアルで作成した SQL Database テーブルに hivesampletable をエクスポートします。

## <a name="submit-a-sqoop-job"></a>Sqoop ジョブの送信

1. Visual Studio で、C# コンソール アプリケーションを作成します。
2. Visual Studio パッケージ マネージャー、コンソールから、次の Nuget コマンドを実行し、パッケージをインポートします。
   
        Install-Package Microsoft.Azure.Management.HDInsight.Job
3. Program.cs ファイルで次のコードを使用します。
   
        using System.Collections.Generic;
        using Microsoft.Azure.Management.HDInsight.Job;
        using Microsoft.Azure.Management.HDInsight.Job.Models;
        using Hyak.Common;
   
        namespace SubmitHDInsightJobDotNet
        {
            class Program
            {
                private static HDInsightJobManagementClient _hdiJobManagementClient;
   
                private const string ExistingClusterName = "<Your HDInsight Cluster Name>";
                private const string ExistingClusterUri = ExistingClusterName + ".azurehdinsight.net";
                private const string ExistingClusterUsername = "<Cluster Username>";
                private const string ExistingClusterPassword = "<Cluster User Password>";
   
                static void Main(string[] args)
                {
                    System.Console.WriteLine("The application is running ...");
   
                    var clusterCredentials = new BasicAuthenticationCloudCredentials { Username = ExistingClusterUsername, Password = ExistingClusterPassword };
                    _hdiJobManagementClient = new HDInsightJobManagementClient(ExistingClusterUri, clusterCredentials);
   
                    SubmitSqoopJob();
   
                    System.Console.WriteLine("Press ENTER to continue ...");
                    System.Console.ReadLine();
                }
   
                private static void SubmitSqoopJob()
                {
                    var sqlDatabaseServerName = "<SQLDatabaseServerName>";
                    var sqlDatabaseLogin = "<SQLDatabaseLogin>";
                    var sqlDatabaseLoginPassword = "<SQLDatabaseLoginPassword>";
                    var sqlDatabaseDatabaseName = "<DatabaseName>";
   
                    var tableName = "<TableName>";
                    var exportDir = "/tutorials/usesqoop/data";
   
                    // Connection string for using Azure SQL Database.
                    // Comment if using SQL Server
                    var connectionString = "jdbc:sqlserver://" + sqlDatabaseServerName + ".database.windows.net;user=" + sqlDatabaseLogin + "@" + sqlDatabaseServerName + ";password=" + sqlDatabaseLoginPassword + ";database=" + sqlDatabaseDatabaseName;
                    // Connection string for using SQL Server.
                    // Uncomment if using SQL Server
                    //var connectionString = "jdbc:sqlserver://" + sqlDatabaseServerName + ";user=" + sqlDatabaseLogin + ";password=" + sqlDatabaseLoginPassword + ";database=" + sqlDatabaseDatabaseName;
   
                    var parameters = new SqoopJobSubmissionParameters
                    {
                        Files = new List<string> { "/user/oozie/share/lib/sqoop/sqljdbc41.jar" }, // This line is required for Linux-based cluster.
                        Command = "export --connect " + connectionString + " --table " + tableName + "_mobile --export-dir " + exportDir + "_mobile --fields-terminated-by \\t -m 1"
                    };
   
                    System.Console.WriteLine("Submitting the Sqoop job to the cluster...");
                    var response = _hdiJobManagementClient.JobManagement.SubmitSqoopJob(parameters);
                    System.Console.WriteLine("Validating that the response is as expected...");
                    System.Console.WriteLine("Response status code is " + response.StatusCode);
                    System.Console.WriteLine("Validating the response object...");
                    System.Console.WriteLine("JobId is " + response.JobSubmissionJsonResponse.Id);
                }
            }
        }
4. **F5** キーを押して、プログラムを実行します。 

## <a name="limitations"></a>制限事項
* 一括エクスポート - Linux ベースの HDInsight では、Microsoft SQL Server または Azure SQL Database にデータをエクスポートするために使用する Sqoop コネクタは、一括挿入を現在サポートしていません。
* バッチ処理 - Linux ベースの HDInsight で、挿入処理実行時に `-batch` スイッチを使用すると、Sqoop は挿入操作をバッチ処理するのではなく、複数の挿入を実行します。

## <a name="next-steps"></a>次のステップ
ここでは Sqoop の使用方法を説明しました。 詳細については、次を参照してください。

* [HDInsight での Oozie の使用](hdinsight-use-oozie.md): Oozie ワークフローで Sqoop アクションを使用します。
* [HDInsight を使用したフライト遅延データの分析](hdinsight-analyze-flight-delay-data.md): Hive を使用してフライト遅延データを分析し、Sqoop を使用して Azure SQL データベースにデータをエクスポートします。
* [HDInsight へのデータのアップロード](hdinsight-upload-data.md): HDInsight/Azure BLOB ストレージにデータをアップロードするその他の方法を説明します。


