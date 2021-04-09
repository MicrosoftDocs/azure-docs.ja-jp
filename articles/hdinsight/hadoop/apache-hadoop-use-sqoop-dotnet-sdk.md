---
title: .NET と HDInsight を使用して Apache Sqoop ジョブを実行する - Azure
description: HDInsight .NET SDK を使用して、Apache Hadoop クラスターと Azure SQL Database 間で Apache Sqoop のインポートとエクスポートを実行する方法について説明します。
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive, hdiseo17may2017, devx-track-csharp
ms.date: 01/14/2020
ms.openlocfilehash: a06326f5b4cf34a06475c10e368c6dcbb49ca9e4
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "98928347"
---
# <a name="run-apache-sqoop-jobs-by-using-net-sdk-for-apache-hadoop-in-hdinsight"></a>HDInsight で .NET SDK for Apache Hadoop を使用して Apache Sqoop ジョブを実行する

[!INCLUDE [sqoop-selector](../../../includes/hdinsight-selector-use-sqoop.md)]

Azure HDInsight .NET SDK を使用して、HDInsight クラスターと Azure SQL Database または SQL Server データベース間でインポートとエクスポートを実行する Apache Sqoop ジョブを HDInsight で実行する方法について説明します。

## <a name="prerequisites"></a>前提条件

* 「[HDInsight の Hadoop での Apache Sqoop の使用](./hdinsight-use-sqoop.md)」の「[テスト環境のセットアップ](./hdinsight-use-sqoop.md#create-cluster-and-sql-database)」が完了していること。

* [Visual Studio](https://visualstudio.microsoft.com/vs/community/).

* Sqoop に関する知識。 詳細については、「[OpenFOAM ユーザーガイド](https://sqoop.apache.org/docs/1.4.7/SqoopUserGuide.html)」を参照してください。

## <a name="use-sqoop-on-hdinsight-clusters-with-the-net-sdk"></a>.NET SDK を使って HDInsight クラスターで Sqoop を使用する

HDInsight .NET SDK は、.NET から HDInsight クラスターを簡単に操作できる .NET クライアント ライブラリを提供します。 このセクションでは、C# コンソール アプリケーションを作成し、前提条件で作成した Azure SQL Database テーブルに `hivesampletable` をエクスポートします。

## <a name="set-up"></a>設定

1. Visual Studio を開始し、C# コンソール アプリケーションを作成します。

1. **[ツール]**  >  **[NuGet Package Manager]**  >  **[パッケージ マネージャー コンソール]** に移動し、次のコマンドを実行します。

    ```
    Install-Package Microsoft.Azure.Management.HDInsight.Job
    ```

## <a name="sqoop-export"></a>Sqoop のエクスポート

Hive から SQL Server へ  この例では、`hivesampletable` のテーブルから SQL Database の `mobiledata` テーブルにデータをエクスポートします。

1. Program.cs ファイルで次のコードを使用します。 このコードを編集して `ExistingClusterName` および `ExistingClusterPassword` の値を設定します。

    ```csharp
    using Microsoft.Azure.Management.HDInsight.Job;
    using Microsoft.Azure.Management.HDInsight.Job.Models;
    using Hyak.Common;
    
    namespace SubmitHDInsightJobDotNet
    {
        class Program
        {
            private static HDInsightJobManagementClient _hdiJobManagementClient;
    
            private const string ExistingClusterName = "<Your HDInsight Cluster Name>";
            private const string ExistingClusterPassword = "<Cluster User Password>";
            private const string ExistingClusterUri = ExistingClusterName + ".azurehdinsight.net";
            private const string ExistingClusterUsername = "admin";
    
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
                var sqlDatabaseServerName = ExistingClusterName + "dbserver";
                var sqlDatabaseLogin = "sqluser";
                var sqlDatabaseLoginPassword = ExistingClusterPassword;
                var sqlDatabaseDatabaseName = ExistingClusterName + "db";
    
                // Connection string for using Azure SQL Database; Comment if using SQL Server
                var connectionString = "jdbc:sqlserver://" + sqlDatabaseServerName + ".database.windows.net;user=" + sqlDatabaseLogin + "@" + sqlDatabaseServerName + ";password=" + sqlDatabaseLoginPassword + ";database=" + sqlDatabaseDatabaseName;
    
                // Connection string for using SQL Server; Uncomment if using SQL Server
                // var connectionString = "jdbc:sqlserver://" + sqlDatabaseServerName + ";user=" + sqlDatabaseLogin + ";password=" + sqlDatabaseLoginPassword + ";database=" + sqlDatabaseDatabaseName;
    
                //sqoop start
                var tableName = "mobiledata";
    
                var parameters = new SqoopJobSubmissionParameters
                {
                     Command = "export --connect " + connectionString + " --table " + tableName + " --hcatalog-table hivesampletable"
                };
                //sqoop end
    
                System.Console.WriteLine("Submitting the Sqoop job to the cluster...");
                var response = _hdiJobManagementClient.JobManagement.SubmitSqoopJob(parameters);
                System.Console.WriteLine("Validating that the response is as expected...");
                System.Console.WriteLine("Response status code is " + response.StatusCode);
                System.Console.WriteLine("Validating the response object...");
                System.Console.WriteLine("JobId is " + response.JobSubmissionJsonResponse.Id);
            }
        }
    }
    ```

1. プログラムを実行するには、**F5** キーを押します。

## <a name="sqoop-import"></a>Sqoop のインポート

SQL Server から Azure Storage へ。 この例は、上記のエクスポートが実行されたことを前提としています。  この例では、SQL Database の `mobiledata` テーブルからクラスターの既定のストレージ アカウントの `wasb:///tutorials/usesqoop/importeddata` ディレクトリにデータをインポートします。

1. `//sqoop start //sqoop end` ブロック内の上記のコードを次のコードに置き換えます。

    ```csharp
    var tableName = "mobiledata";
    var exportDir = "/tutorials/usesqoop/importeddata";
    
    var parameters = new SqoopJobSubmissionParameters
    {
        Command = "import --connect " + connectionString + " --table " + tableName + " --target-dir " +  exportDir + " --fields-terminated-by \\t --lines-terminated-by \\n -m 1"
    };
    ```

1. プログラムを実行するには、**F5** キーを押します。

## <a name="limitations"></a>制限事項

Linux ベースの HDInsight には次の制限事項があります。

* 一括エクスポート:Microsoft SQL Server または Azure SQL Database にデータをエクスポートするために使用する Sqoop コネクタでは、一括挿入は現在サポートされていません。

* バッチ処理: `-batch` スイッチを使用すると、Sqoop では挿入操作がバッチ処理されず、複数の挿入が実行されます。

## <a name="next-steps"></a>次のステップ

ここでは Sqoop の使用方法を学習しました。 詳細については、次を参照してください。

* [HDInsight での Apache Oozie の使用](../hdinsight-use-oozie-linux-mac.md):Oozie ワークフローで Sqoop アクションを使用します。
* [HDInsight へのデータのアップロード](../hdinsight-upload-data.md):HDInsight または Azure Blob Storage にデータをアップロードするその他の方法を説明します。
