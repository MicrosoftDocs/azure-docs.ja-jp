---
title: .NET と HDInsight を使用して Apache Sqoop ジョブを実行する - Azure
description: HDInsight .NET SDK を使用して、Apache Hadoop クラスターと Azure SQL データベース間で Apache Sqoop のインポートとエクスポートを実行する方法について説明します。
keywords: sqoop ジョブ
ms.reviewer: jasonh
author: hrasheed-msft
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.topic: conceptual
ms.date: 05/16/2018
ms.author: hrasheed
ms.openlocfilehash: 1bdf1318f93697cd7b479d404f44b7617ad875dc
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/28/2019
ms.locfileid: "67450151"
---
# <a name="run-apache-sqoop-jobs-by-using-net-sdk-for-apache-hadoop-in-hdinsight"></a>HDInsight で .NET SDK for Apache Hadoop を使用して Apache Sqoop ジョブを実行する
[!INCLUDE [sqoop-selector](../../../includes/hdinsight-selector-use-sqoop.md)]

Azure HDInsight .NET SDK を使用して、HDInsight クラスターと Azure SQL データベースまたは SQL Server データベース間でインポートとエクスポートを実行する Apache Sqoop ジョブを HDInsight で実行する方法について説明します。

> [!NOTE]
> この記事の手順は、Windows ベースまたは Linux ベースの HDInsight クラスターで使用できます。ただし、これらの手順は Windows クライアントでのみ機能します。 他の方法を選択するには、この記事の上部にあるタブ セレクターを使用してください。

## <a name="prerequisites"></a>前提条件
この記事の作業を始める前に、以下を用意する必要があります。

* HDInsight 上の Apache Spark クラスター。 詳細については、[クラスターと SQL データベースの作成](hdinsight-use-sqoop.md#create-cluster-and-sql-database)に関するページを参照してください。

## <a name="use-sqoop-on-hdinsight-clusters-with-the-net-sdk"></a>.NET SDK を使って HDInsight クラスターで Sqoop を使用する
HDInsight .NET SDK は、.NET から HDInsight クラスターを簡単に操作できる .NET クライアント ライブラリを提供します。 このセクションでは、C# コンソール アプリケーションを作成し、この記事で作成した Azure SQL Database テーブルに hivesampletable をエクスポートします。

## <a name="submit-a-sqoop-job"></a>Sqoop ジョブの送信

1. Visual Studio で、C# コンソール アプリケーションを作成します。

2. Visual Studio パッケージ マネージャー コンソールから、次の NuGet コマンドを実行し、パッケージをインポートします。
   
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

4. プログラムを実行するには、**F5** キーを押します。 

## <a name="limitations"></a>制限事項
Linux ベースの HDInsight には次の制限事項があります。

* 一括エクスポート:Microsoft SQL Server または Azure SQL Database にデータをエクスポートするために使用する Sqoop コネクタは、一括挿入を現在サポートしていません。

* バッチ処理:`-batch` スイッチを使用すると、Sqoop では挿入操作がバッチ処理されず、複数の挿入が実行されます。

## <a name="next-steps"></a>次の手順
ここでは Sqoop の使用方法を説明しました。 詳細については、次を参照してください。

* [HDInsight での Apache Oozie の使用](../hdinsight-use-oozie-linux-mac.md):Oozie ワークフローで Sqoop アクションを使用します。
* [HDInsight へのデータのアップロード](../hdinsight-upload-data.md):HDInsight または Azure Blob Storage にデータをアップロードするその他の方法を説明します。

