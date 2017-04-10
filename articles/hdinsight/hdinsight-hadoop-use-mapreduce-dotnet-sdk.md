---
title: "HDInsight .NET SDK を使用して MapReduce ジョブを送信する | Microsoft Docs"
description: "HDInsight .NET SDK を使用して、MapReduce ジョブを Azure HDInsight Hadoop に送信する方法について説明します。"
editor: cgronlun
manager: jhubbard
services: hdinsight
documentationcenter: 
tags: azure-portal
author: mumian
ms.assetid: c85e44b0-85fd-4185-ad1c-c34a9fe5ef44
ms.service: hdinsight
ms.custom: hdinsightactive
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/03/2017
ms.author: jgao
translationtype: Human Translation
ms.sourcegitcommit: 2f03ba60d81e97c7da9a9fe61ecd419096248763
ms.openlocfilehash: 03f3898182d405bced8ad41b281e2ce2037b8343
ms.lasthandoff: 03/04/2017


---
# <a name="run-mapreduce-jobs-using-hdinsight-net-sdk"></a>HDInsight .NET SDK を使用して MapReduce ジョブを実行する
[!INCLUDE [mapreduce-selector](../../includes/hdinsight-selector-use-mapreduce.md)]

この記事では、HDInsight .NET SDK を使用して MapReduce ジョブを送信する方法について説明します。 HDInsight クラスターには、MapReduce サンプルがいくつか含まれた jar ファイルが付属しています。 jar ファイルは */example/jars/hadoop-mapreduce-examples.jar* に格納されています。  そのサンプルの&1; つに、*wordcount* があります。 この記事では、wordcount ジョブを送信する C# コンソール アプリケーションを作成します。  このジョブでは、*/example/data/gutenberg/davinci.txt* ファイルを読み取り、結果を */example/data/davinciwordcount* に出力します。  アプリケーションを再実行する場合は、出力フォルダーをクリーンアップする必要があります。

> [!NOTE]
> この記事の手順は、Windows クライアントから実行する必要があります。 Linux、OS X、または Unix クライアントで Hive を使用する方法については、この記事の上部に表示されているタブ セレクターをクリックしてください。
> 
> 

## <a name="prerequisites"></a>前提条件
この記事を読み始める前に、次の項目を用意する必要があります。

* **HDInsight の Hadoop クラスター**。 詳細については、[HDInsight での Linux ベース Hadoop の使用](hdinsight-use-sqoop.md#create-cluster-and-sql-database)に関するページを参照してください。
* **Visual Studio 2013/2015/2017**。

## <a name="submit-mapreduce-jobs-using-hdinsight-net-sdk"></a>HDInsight .NET SDK を使用して MapReduce ジョブを送信する
HDInsight .NET SDK は、.NET から HDInsight クラスターを簡単に操作できる .NET クライアント ライブラリを提供します。 

**ジョブを送信するには**

1. Visual Studio で、C# コンソール アプリケーションを作成します。
2. NuGet パッケージ マネージャー コンソールから、次のコマンドを実行します。
   
        Install-Package Microsoft.Azure.Management.HDInsight.Job
3. 次のコードを使用します。
   
        using System.Collections.Generic;
        using System.IO;
        using System.Text;
        using System.Threading;
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
   
                private const string DefaultStorageAccountName = "<Default Storage Account Name>"; //<StorageAccountName>.blob.core.windows.net
                private const string DefaultStorageAccountKey = "<Default Storage Account Key>";
                private const string DefaultStorageContainerName = "<Default Blob Container Name>";
   
                static void Main(string[] args)
                {
                    System.Console.WriteLine("The application is running ...");
   
                    var clusterCredentials = new BasicAuthenticationCloudCredentials { Username = ExistingClusterUsername, Password = ExistingClusterPassword };
                    _hdiJobManagementClient = new HDInsightJobManagementClient(ExistingClusterUri, clusterCredentials);
   
                    SubmitMRJob();
   
                    System.Console.WriteLine("Press ENTER to continue ...");
                    System.Console.ReadLine();
                }
   
                private static void SubmitMRJob()
                {
                    List<string> args = new List<string> { { "/example/data/gutenberg/davinci.txt" }, { "/example/data/davinciwordcount" } };
   
                    var paras = new MapReduceJobSubmissionParameters
                    {
                        JarFile = @"/example/jars/hadoop-mapreduce-examples.jar",
                        JarClass = "wordcount",
                        Arguments = args
                    };
   
                    System.Console.WriteLine("Submitting the MR job to the cluster...");
                    var jobResponse = _hdiJobManagementClient.JobManagement.SubmitMapReduceJob(paras);
                    var jobId = jobResponse.JobSubmissionJsonResponse.Id;
                    System.Console.WriteLine("Response status code is " + jobResponse.StatusCode);
                    System.Console.WriteLine("JobId is " + jobId);
   
                    System.Console.WriteLine("Waiting for the job completion ...");
   
                    // Wait for job completion
                    var jobDetail = _hdiJobManagementClient.JobManagement.GetJob(jobId).JobDetail;
                    while (!jobDetail.Status.JobComplete)
                    {
                        Thread.Sleep(1000);
                        jobDetail = _hdiJobManagementClient.JobManagement.GetJob(jobId).JobDetail;
                    }
   
                    // Get job output
                    var storageAccess = new AzureStorageAccess(DefaultStorageAccountName, DefaultStorageAccountKey,
                        DefaultStorageContainerName);
                    var output = (jobDetail.ExitValue == 0)
                        ? _hdiJobManagementClient.JobManagement.GetJobOutput(jobId, storageAccess) // fetch stdout output in case of success
                        : _hdiJobManagementClient.JobManagement.GetJobErrorLogs(jobId, storageAccess); // fetch stderr output in case of failure
   
                    System.Console.WriteLine("Job output is: ");
   
                    using (var reader = new StreamReader(output, Encoding.UTF8))
                    {
                        string value = reader.ReadToEnd();
                        System.Console.WriteLine(value);
                    }
                }
            }
        }
4. **F5** キーを押してアプリケーションを実行します。

ジョブを再実行するには、ジョブの出力フォルダー名を変更する必要があります (サンプルでは "/example/data/davinciwordcount" になっています)。

ジョブが正常に完了すると、出力は空白になります。 MapReduce ジョブの結果を確認するには、Azure Portal で Blob ストレージの既定のストレージ コンテナーを調べます。  ファイル名は "part-r-00000" です。

## <a name="next-steps"></a>次のステップ
この記事では、HDInsight クラスターを作成する方法をいくつか説明しました。 詳細については、次の記事を参照してください。

* Hive ジョブの送信については、「[HDInsight .NET SDK を使用した Hive クエリの実行](hdinsight-hadoop-use-hive-dotnet-sdk.md)」を参照してください。
* HDInsight クラスターの作成については、「[HDInsight での Linux ベースの Hadoop クラスターの作成](hdinsight-hadoop-provision-linux-clusters.md)」を参照してください。
* HDInsight クラスターの管理については、[HDInsight での Hadoop クラスターの管理](hdinsight-administer-use-portal-linux.md)に関するページを参照してください。
* HDInsight .NET SDK の詳細については、[HDInsight .NET SDK リファレンス](https://msdn.microsoft.com/library/mt271028.aspx)を参照してください。
* 非対話型認証については、「[非対話型認証 .NET HDInsight アプリケーションを作成する](hdinsight-create-non-interactive-authentication-dotnet-applications.md)」を参照してください。


