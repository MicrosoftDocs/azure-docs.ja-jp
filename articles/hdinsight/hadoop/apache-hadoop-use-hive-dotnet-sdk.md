---
title: HDInsight .NET SDK を使用して Apache Hive クエリを実行する - Azure
description: HDInsight .NET SDK を使用して、Azure HDInsight 上の Apache Hadoop に Apache Hadoop ジョブを送信する方法について説明します。
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive, devx-track-csharp
ms.date: 12/24/2019
ms.openlocfilehash: 032c070a9485fc4720ae7966a0991bc0c5ae3921
ms.sourcegitcommit: 42e4f986ccd4090581a059969b74c461b70bcac0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/23/2021
ms.locfileid: "104867850"
---
# <a name="run-apache-hive-queries-using-hdinsight-net-sdk"></a>HDInsight .NET SDK を使用して Apache Hive クエリを実行する

[!INCLUDE [hive-selector](../../../includes/hdinsight-selector-use-hive.md)]

HDInsight .NET SDK を使用して Apache Hive クエリを送信する方法について説明します。 Hive テーブルを表示するための Hive クエリを送信し、結果を表示する C# プログラムを作成します。

> [!NOTE]  
> この記事の手順は、Windows クライアントから実行する必要があります。 Linux、OS X、または Unix クライアントで Hive を使用する方法については、この記事の上部に表示されているタブ セレクターをクリックしてください。

## <a name="prerequisites"></a>前提条件

この記事の操作を始める前に、以下を用意する必要があります。

* HDInsight 上の Apache Spark クラスター。 詳細については、[HDInsight での Linux ベース Hadoop の使用](apache-hadoop-linux-tutorial-get-started.md)に関するページを参照してください。

    > [!IMPORTANT]  
    > 2017 年 9 月 15 日の時点で、HDInsight .NET SDK でサポートされているのは、Azure ストレージ アカウントから Hive クエリの結果を返すことのみです。 プライマリ ストレージとして Azure Data Lake Storage を使用している HDInsight クラスターでこの例を使用する場合、.NET SDK を使用して検索結果を取得することはできません。

* [Visual Studio](https://visualstudio.microsoft.com/vs/community/) 2013 以降。 少なくとも **[.NET デスクトップ開発]** ワークロードがインストールされている必要があります。

## <a name="run-a-hive-query"></a>Hive クエリを実行する

HDInsight .NET SDK は、.NET から HDInsight クラスターを簡単に操作できる .NET クライアント ライブラリを提供します。

1. Visual Studio で、C# コンソール アプリケーションを作成します。

1. NuGet パッケージ マネージャー コンソールから、次のコマンドを実行します。

    ```console
    Install-Package Microsoft.Azure.Management.HDInsight.Job
    ```

1. 次のコードを編集して、変数 `ExistingClusterName, ExistingClusterUsername, ExistingClusterPassword,DefaultStorageAccountName,DefaultStorageAccountKey,DefaultStorageContainerName` の値を初期化します。 その後、変更されたコードを Visual Studio の **Program.cs** の内容全体として使用します。

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
            private const string ExistingClusterUsername = "<Cluster Username>";
            private const string ExistingClusterPassword = "<Cluster User Password>";

            // Only Azure Storage accounts are supported by the SDK
            private const string DefaultStorageAccountName = "<Default Storage Account Name>";
            private const string DefaultStorageAccountKey = "<Default Storage Account Key>";
            private const string DefaultStorageContainerName = "<Default Blob Container Name>";

            private const string ExistingClusterUri = ExistingClusterName + ".azurehdinsight.net";

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
                Dictionary<string, string> defines = new Dictionary<string, string> { { "hive.execution.engine", "tez" }, { "hive.exec.reducers.max", "1" } };
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

1. **F5** キーを押してアプリケーションを実行します。

アプリケーションの出力は次のようになります。

:::image type="content" source="./media/apache-hadoop-use-hive-dotnet-sdk/hdinsight-hadoop-use-hive-net-sdk-output.png" alt-text="HDInsight Hadoop Hive ジョブの出力" border="true":::

## <a name="next-steps"></a>次のステップ

この記事では、HDInsight .NET SDK を使用して Apache Hive クエリを送信する方法について説明しました。 詳細については、以下の記事をお読みください。

* [Azure HDInsight の概要](apache-hadoop-linux-tutorial-get-started.md)
* [HDInsight で Apache Hadoop クラスターを作成する](../hdinsight-hadoop-provision-linux-clusters.md)
* [HDInsight .NET SDK リファレンス](/dotnet/api/overview/azure/hdinsight)
* [HDInsight での Apache Sqoop の使用](apache-hadoop-use-sqoop-mac-linux.md)
* [非対話型認証 .NET HDInsight アプリケーションを作成する](../hdinsight-create-non-interactive-authentication-dotnet-applications.md)
