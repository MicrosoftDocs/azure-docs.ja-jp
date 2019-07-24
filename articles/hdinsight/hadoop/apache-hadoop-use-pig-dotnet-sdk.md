---
title: .NET SDK for Hadoop で Apache Pig ジョブを実行する - Azure HDInsight
description: .NET SDK for Hadoop を使用して HDInsight で Hadoop に Pig ジョブを送信する方法を説明します。
services: hdinsight
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 05/01/2018
ms.author: hrasheed
ms.openlocfilehash: 721bb93dc259bd2b4cc9f51d8d0d665c9b921128
ms.sourcegitcommit: c2e61b62f218830dd9076d9abc1bbcb42180b3a8
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/15/2018
ms.locfileid: "53436842"
---
# <a name="run-apache-pig-jobs-using-the-net-sdk-for-apache-hadoop-in-hdinsight"></a>.NET SDK for Apache Hadoop を使用して HDInsight 上で Apache Pig ジョブを実行する

[!INCLUDE [pig-selector](../../../includes/hdinsight-selector-use-pig.md)]

.NET SDK for Apache Hadoop を使用して Azure HDInsight 上の Hadoop に Apache Pig ジョブを送信する方法について説明します。

HDInsight .NET SDK は、.NET から HDInsight クラスターを簡単に操作できる .NET クライアント ライブラリを提供します。 Pig では、一連のデータ変換をモデル化することで MapReduce 操作を作成できます。 このドキュメントでは、基本的な C# アプリケーションを使用して、Pig ジョブを HDInsight クラスターに送信する方法を説明します。

## <a name="prerequisites"></a>前提条件

この記事の手順を完了するには、次のものが必要です。

* Azure HDInsight (HDInsight の Hadoop) クラスター (Windows または Linux ベース)

  > [!IMPORTANT]
  > Linux は、バージョン 3.4 以上の HDInsight で使用できる唯一のオペレーティング システムです。 詳細については、[Windows での HDInsight の提供終了](../hdinsight-component-versioning.md#hdinsight-windows-retirement)に関する記事を参照してください。

* Visual Studio 2012、2013、2015、または 2017

## <a name="create-the-application"></a>アプリケーションを作成する

HDInsight .NET SDK は、.NET から HDInsight クラスターを簡単に操作できる .NET クライアント ライブラリを提供します。

1. Visual Studio で **[ファイル]** メニューから **[新規作成]**、**[プロジェクト]** の順に選択します。

2. 新しいプロジェクトの場合は、次の値を入力または選択します。

   | プロパティ | 値 |
   | ------ | ------ |
   | Category | テンプレート/Visual C#/Windows |
   | テンプレート | コンソール アプリケーション |
   | Name | SubmitPigJob |

3. **[OK]** をクリックしてプロジェクトを作成します。

4. **[ツール]** メニューで **[ライブラリ パッケージ マネージャー]** または **[NuGet パッケージ マネージャー]** を選択し、**[パッケージ マネージャー コンソール]** を選択します。

5. .NET SDK パッケージをインストールするには、次のコマンドを使用します。

        Install-Package Microsoft.Azure.Management.HDInsight.Job

6. ソリューション エクスプローラーで **Program.cs** をダブルクリックして、このファイルを開きます。 既存のコードを次のコードに置き換えます。

    ```csharp
    using Microsoft.Azure.Management.HDInsight.Job;
    using Microsoft.Azure.Management.HDInsight.Job.Models;
    using Hyak.Common;

    namespace SubmitPigJob
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

                SubmitPigJob();

                System.Console.WriteLine("Press ENTER to continue ...");
                System.Console.ReadLine();
            }

            private static void SubmitPigJob()
            {
                var parameters = new PigJobSubmissionParameters
                {
                    Query = @"LOGS = LOAD '/example/data/sample.log';
                                LEVELS = foreach LOGS generate REGEX_EXTRACT($0, '(TRACE|DEBUG|INFO|WARN|ERROR|FATAL)', 1)  as LOGLEVEL;
                                FILTEREDLEVELS = FILTER LEVELS by LOGLEVEL is not null;
                                GROUPEDLEVELS = GROUP FILTEREDLEVELS by LOGLEVEL;
                                FREQUENCIES = foreach GROUPEDLEVELS generate group as LOGLEVEL, COUNT(FILTEREDLEVELS.LOGLEVEL) as COUNT;
                                RESULT = order FREQUENCIES by COUNT desc;
                                DUMP RESULT;"
                };

                System.Console.WriteLine("Submitting the Pig job to the cluster...");
                var response = _hdiJobManagementClient.JobManagement.SubmitPigJob(parameters);
                System.Console.WriteLine("Validating that the response is as expected...");
                System.Console.WriteLine("Response status code is " + response.StatusCode);
                System.Console.WriteLine("Validating the response object...");
                System.Console.WriteLine("JobId is " + response.JobSubmissionJsonResponse.Id);
            }
        }
    }
    ```

7. アプリケーションを起動するには、**F5** キーを押します。

8. アプリケーションを終了するには、**Enter** キーを押します。

## <a name="next-steps"></a>次の手順

HDInsight での Pig について詳しくは、「[HDInsight 上の Apache Pig で Apache Pig を使用する](hdinsight-use-pig.md)」をご覧ください。

HDInsight での Hadoop の使用について詳しくは、次のドキュメントを参照してください。

* [HDInsight 上の Apache Hadoop で Apache Hive を使用する](hdinsight-use-hive.md)
* [HDInsight 上の Apache Hadoop で MapReduce を使用する](hdinsight-use-mapreduce.md)

[preview-portal]: https://portal.azure.com/
