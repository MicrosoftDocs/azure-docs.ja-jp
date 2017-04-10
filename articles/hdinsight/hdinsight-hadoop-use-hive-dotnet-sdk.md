---
title: "HDInsight .NET SDK を使用した Hive クエリの実行 | Microsoft Docs"
description: "HDInsight .NET SDK を使用して、Hadoop ジョブを Azure HDInsight Hadoop に送信する方法について説明します。"
editor: cgronlun
manager: jhubbard
services: hdinsight
documentationcenter: 
tags: azure-portal
author: mumian
ms.assetid: 4e291890-f8b4-426c-b5e8-d4fd512ff042
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
ms.openlocfilehash: 6e9626cf8da4e4106dfedc164af0f0ba52bf2f06
ms.lasthandoff: 03/04/2017


---
# <a name="run-hive-queries-using-hdinsight-net-sdk"></a>HDInsight .NET SDK を使用した Hive クエリの実行
[!INCLUDE [hive-selector](../../includes/hdinsight-selector-use-hive.md)]

HDInsight .NET SDK を使用して Hive クエリを送信する方法について説明します。

> [!NOTE]
> この記事の手順は、Windows クライアントから実行する必要があります。 Linux、OS X、または Unix クライアントで Hive を使用する方法については、この記事の上部に表示されているタブ セレクターをクリックしてください。
> 
> 

## <a name="prerequisites"></a>前提条件
この記事の操作を始める前に、以下を用意する必要があります。

* **HDInsight の Hadoop クラスター**。 詳細については、[HDInsight での Linux ベース Hadoop の使用](hdinsight-use-sqoop.md#create-cluster-and-sql-database)に関するページを参照してください。
* **Visual Studio 2013/2015/2017**。

## <a name="submit-hive-queries-using-hdinsight-net-sdk"></a>HDInsight .NET SDK を使用した Hive クエリの送信
HDInsight .NET SDK は、.NET から HDInsight クラスターを簡単に操作できる .NET クライアント ライブラリを提供します。 

**ジョブを送信するには**

1. Visual Studio で、C# コンソール アプリケーションを作成します。
2. NuGet パッケージ マネージャー コンソールから、次のコマンドを実行します。
   
        Install-Package Microsoft.Azure.Management.HDInsight.Job
3. 次のコードを使用します。

    ```csharp
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
   
                private const string DefaultStorageAccountName = "<Default Storage Account Name>";
                private const string DefaultStorageAccountKey = "<Default Storage Account Key>";
                private const string DefaultStorageContainerName = "<Default Blob Container Name>";
   
                static void Main(string[] args)
                {
                    System.Console.WriteLine("The application is running ...");
   
                    var clusterCredentials = new BasicAuthenticationCloudCredentials { Username = ExistingClusterUsername, Password = ExistingClusterPassword };
                    _hdiJobManagementClient = new HDInsightJobManagementClient(ExistingClusterUri, clusterCredentials);
   
                    SubmitHiveJob();
   
                    System.Console.WriteLine("Press ENTER to continue ...");
                    System.Console.ReadLine();
                }
   
                private static void SubmitHiveJob()
                {
                    Dictionary<string, string> defines = new Dictionary<string, string> { { "hive.execution.engine", "ravi" }, { "hive.exec.reducers.max", "1" } };
                    List<string> args = new List<string> { { "argA" }, { "argB" } };
                    var parameters = new HiveJobSubmissionParameters
                    {
                        Query = "SHOW TABLES",
                        Defines = defines,
                        Arguments = args
                    };
   
                    System.Console.WriteLine("Submitting the Hive job to the cluster...");
                    var jobResponse = _hdiJobManagementClient.JobManagement.SubmitHiveJob(parameters);
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
    ```
4. **F5** キーを押してアプリケーションを実行します。

アプリケーションの出力は、次のようになります。

![HDInsight Hadoop Hive ジョブの出力](./media/hdinsight-hadoop-use-hive-dotnet-sdk/hdinsight-hadoop-use-hive-net-sdk-output.png)

## <a name="next-steps"></a>次のステップ
この記事では、HDInsight クラスターを作成する方法をいくつか説明しました。 詳細については、次の記事を参照してください。

* [Azure HDInsight の概要][hdinsight-get-started]
* [HDInsight で Hadoop クラスターを作成する][hdinsight-provision]
* [Azure Portal を使用した HDInsight での Hadoop クラスターの管理](hdinsight-administer-use-management-portal.md)
* [HDInsight .NET SDK リファレンス](https://msdn.microsoft.com/library/mt271028.aspx)
* [HDInsight の Hadoop での Pig の使用](hdinsight-use-pig.md)
* [HDInsight での Sqoop の使用](hdinsight-use-sqoop-mac-linux.md)
* [非対話型認証 .NET HDInsight アプリケーションを作成する](hdinsight-create-non-interactive-authentication-dotnet-applications.md)

[hdinsight-provision]: hdinsight-provision-clusters.md
[hdinsight-get-started]: hdinsight-hadoop-linux-tutorial-get-started.md



