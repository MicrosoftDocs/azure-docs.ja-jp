---
title: HDInsight .NET SDK を使用して MapReduce ジョブを送信する - Azure
description: HDInsight .NET SDK を使用して、MapReduce ジョブを Azure HDInsight 上の Apache Hadoop に送信する方法について説明します。
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive, devx-track-csharp
ms.date: 01/15/2020
ms.openlocfilehash: 8fbcb66be11c7c77a9bfaf0e6ec790622dcbbda7
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "98932190"
---
# <a name="run-mapreduce-jobs-using-hdinsight-net-sdk"></a>HDInsight .NET SDK を使用して MapReduce ジョブを実行する

[!INCLUDE [mapreduce-selector](../../../includes/hdinsight-selector-use-mapreduce.md)]

この記事では、HDInsight .NET SDK を使用して MapReduce ジョブを送信する方法について説明します。 HDInsight クラスターには、MapReduce サンプルがいくつか含まれた jar ファイルが付属しています。 jar ファイルは `/example/jars/hadoop-mapreduce-examples.jar` です。  そのサンプルの 1 つに、**wordcount** があります。 この記事では、wordcount ジョブを送信する C# コンソール アプリケーションを作成します。  このジョブは `/example/data/gutenberg/davinci.txt` ファイルを読み取り、結果を `/example/data/davinciwordcount` に出力します。  アプリケーションを再実行する場合は、出力フォルダーをクリーンアップする必要があります。

> [!NOTE]  
> この記事の手順は、Windows クライアントから実行する必要があります。 Linux、OS X、または Unix クライアントで Hive を使用する方法については、この記事の上部に表示されているタブ セレクターをクリックしてください。

## <a name="prerequisites"></a>前提条件

* HDInsight の Apache Hadoop クラスター。 [Azure portal を使用した Apache Hadoop クラスターの作成](../hdinsight-hadoop-create-linux-clusters-portal.md)に関するページを参照してください。

* [Visual Studio](https://visualstudio.microsoft.com/vs/community/).

## <a name="submit-mapreduce-jobs-using-hdinsight-net-sdk"></a>HDInsight .NET SDK を使用して MapReduce ジョブを送信する

HDInsight .NET SDK は、.NET から HDInsight クラスターを簡単に操作できる .NET クライアント ライブラリを提供します。

1. Visual Studio を開始し、C# コンソール アプリケーションを作成します。

1. **[ツール]**  >  **[NuGet Package Manager]**  >  **[パッケージ マネージャー コンソール]** に移動し、次のコマンドを入力します。

    ```   
    Install-Package Microsoft.Azure.Management.HDInsight.Job
    ```

1. 以下のコードを **Program.cs** にコピーします。 次に、`existingClusterName`、`existingClusterPassword`、`defaultStorageAccountName`、`defaultStorageAccountKey`、および `defaultStorageContainerName` の値を設定して、コードを編集します。

    ```csharp
    using System.Collections.Generic;
    using System.IO;
    using System.Text;
    using System.Threading;
    using Microsoft.Azure.Management.HDInsight.Job;
    using Microsoft.Azure.Management.HDInsight.Job.Models;
    using Hyak.Common;
    using Microsoft.WindowsAzure.Storage;
    using Microsoft.WindowsAzure.Storage.Blob;
    
    namespace SubmitHDInsightJobDotNet
    {
        class Program
        {
            private static HDInsightJobManagementClient _hdiJobManagementClient;
    
            private const string existingClusterName = "<Your HDInsight Cluster Name>";
            private const string existingClusterPassword = "<Cluster User Password>";
            private const string defaultStorageAccountName = "<Default Storage Account Name>"; 
            private const string defaultStorageAccountKey = "<Default Storage Account Key>";
            private const string defaultStorageContainerName = "<Default Blob Container Name>";
    
            private const string existingClusterUsername = "admin";
            private const string existingClusterUri = existingClusterName + ".azurehdinsight.net";
            private const string sourceFile = "/example/data/gutenberg/davinci.txt";
            private const string outputFolder = "/example/data/davinciwordcount";
    
            static void Main(string[] args)
            {
                System.Console.WriteLine("The application is running ...");
    
                var clusterCredentials = new BasicAuthenticationCloudCredentials { Username = existingClusterUsername, Password = existingClusterPassword };
                _hdiJobManagementClient = new HDInsightJobManagementClient(existingClusterUri, clusterCredentials);
    
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
                System.Console.WriteLine("Job output is: ");
                var storageAccess = new AzureStorageAccess(defaultStorageAccountName, defaultStorageAccountKey,
                    defaultStorageContainerName);
    
                if (jobDetail.ExitValue == 0)
                {
                    // Create the storage account object
                    CloudStorageAccount storageAccount = CloudStorageAccount.Parse("DefaultEndpointsProtocol=https;AccountName=" +
                        defaultStorageAccountName +
                        ";AccountKey=" + defaultStorageAccountKey);
    
                    // Create the blob client.
                    CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();
    
                    // Retrieve reference to a previously created container.
                    CloudBlobContainer container = blobClient.GetContainerReference(defaultStorageContainerName);
    
                    CloudBlockBlob blockBlob = container.GetBlockBlobReference(outputFolder.Substring(1) + "/part-r-00000");
    
                    using (var stream = blockBlob.OpenRead())
                    {
                        using (StreamReader reader = new StreamReader(stream))
                        {
                            while (!reader.EndOfStream)
                            {
                                System.Console.WriteLine(reader.ReadLine());
                            }
                        }
                    }
                }
                else
                {
                    // fetch stderr output in case of failure
                    var output = _hdiJobManagementClient.JobManagement.GetJobErrorLogs(jobId, storageAccess);
    
                    using (var reader = new StreamReader(output, Encoding.UTF8))
                    {
                        string value = reader.ReadToEnd();
                        System.Console.WriteLine(value);
                    }
    
                }
            }
        }
    }

    ```

1. **F5** キーを押してアプリケーションを実行します。

ジョブを再実行するには、ジョブの出力フォルダー名を変更する必要があります (サンプルでは `/example/data/davinciwordcount` になっています)。

ジョブが正常に完了すると、アプリケーションは出力ファイル `part-r-00000` の内容を出力します。

## <a name="next-steps"></a>次のステップ

この記事では、HDInsight クラスターを作成する方法をいくつか説明しました。 詳細については、以下の記事をお読みください。

* Hive ジョブの送信については、「[HDInsight .NET SDK を使用した Apache Hive クエリの実行](apache-hadoop-use-hive-dotnet-sdk.md)」を参照してください。
* HDInsight クラスターの作成については、「[HDInsight での Linux ベースの Apache Hadoop クラスターの作成](../hdinsight-hadoop-provision-linux-clusters.md)」を参照してください。
* HDInsight クラスターの管理については、[HDInsight での Apache Hadoop クラスターの管理](../hdinsight-administer-use-portal-linux.md)に関するページを参照してください。
* HDInsight .NET SDK の詳細については、[HDInsight .NET SDK リファレンス](/dotnet/api/overview/azure/hdinsight)を参照してください。
* 非対話型認証については、「[非対話型認証 .NET HDInsight アプリケーションを作成する](../hdinsight-create-non-interactive-authentication-dotnet-applications.md)」を参照してください。
