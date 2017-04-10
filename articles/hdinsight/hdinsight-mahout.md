---
title: "Mahout と HDInsight を使用した PowerShell からのリコメンデーションの生成 | Microsoft Docs"
description: "クライアント上で実行される PowerShell スクリプトから Apache Mahout 機械学習ライブラリを使用して HDInsight (Hadoop) で映画のリコメンデーションを生成する方法について説明します。"
services: hdinsight
documentationcenter: 
author: Blackmist
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: 07b57208-32aa-4e59-900a-6c934fa1b7a7
ms.service: hdinsight
ms.custom: hdinsightactive
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/19/2017
ms.author: larryfr
translationtype: Human Translation
ms.sourcegitcommit: 0d5b68d26d708a28edee13ff3d9a57588ce83e12
ms.openlocfilehash: fdca3ed832dc0895a04dc3fda2dcf863d8938d8a
ms.lasthandoff: 01/18/2017


---
# <a name="generate-movie-recommendations-by-using-apache-mahout-with-hadoop-in-hdinsight-powershell"></a>HDInsight で Apache Mahout と Hadoop を使用して映画のリコメンデーションを生成する (PowerShell)
[!INCLUDE [mahout-selector](../../includes/hdinsight-selector-mahout.md)]

[Apache Mahout](http://mahout.apache.org) 機械学習ライブラリを使用して Azure HDInsight で映画のリコメンデーションを生成する方法について説明します。 このドキュメントでは、Azure PowerShell を使用して Mahout をリモートで実行する方法について説明します。

Mahout は、Apache Hadoop の[機械学習][ml]ライブラリの&1; つです。 Mahout には、フィルター処理、分類、クラスタリングなどデータを処理するためのアルゴリズムが含まれています。 この記事では、リコメンデーション エンジンを使用し、友人たちが鑑賞した映画に基づいて映画のリコメンデーションを生成します。

## <a name="prerequisites"></a>前提条件

* Linux ベースの HDInsight クラスター。 作成の詳細については、「[Hadoop チュートリアル: HDInsight で Linux ベースの Hadoop を使用する][getstarted]」を参照してください。

> [!IMPORTANT]
> Linux は、バージョン 3.4 以上の HDInsight で使用できる唯一のオペレーティング システムです。 詳細については、[Window での HDInsight の廃止](hdinsight-component-versioning.md#hdi-version-32-and-33-nearing-deprecation-date)に関する記事を参照してください。

* **Azure PowerShell を実行できるワークステーション**。

    > [!IMPORTANT]
    > Azure サービス マネージャーを使用した HDInsight リソースの管理に関する Azure PowerShell のサポートは、2017 年 1 月 1 日までに**廃止**される予定です。 このドキュメントの手順では、Azure Resource Manager で機能する新しい HDInsight コマンドレットを使用します。
    >
    > [Azure PowerShell のインストールと構成](/powershell/azureps-cmdlets-docs) に関するページの手順に従い、Azure PowerShell の最新バージョンをインストールしてください。 Azure Resource Manager で機能する新しいコマンドレットを使用するようにスクリプトを変更する必要がある場合、詳細については、「[Migrating to Azure Resource Manager-based development tools for HDInsight clusters (HDInsight クラスターの Azure Resource Manager ベースの開発ツールへの移行)](hdinsight-hadoop-development-using-azure-resource-manager.md)」をご覧ください。

## <a name="recommendations"></a>Azure PowerShell を使用したリコメンデーションの生成

> [!NOTE]
> このセクションで使用されるジョブは Azure PowerShell で動作しますが、Mahout で提供されるクラスの多くは現在 Azure PowerShell で動作しないため、Hadoop コマンド ラインを使用して実行する必要があります。 Azure PowerShell で動作しないクラスの一覧については、「[トラブルシューティング](#troubleshooting)」セクションを参照してください。
>
> SSH を使用した HDInsight への接続とクラスターでの Mahout サンプルの直接的な実行の例については、[Mahout と HDInsight を使用した映画のリコメンデーションの生成 (SSH)](hdinsight-hadoop-mahout-linux-mac.md)に関するページを参照してください。

Mahout で提供される機能の&1; つが、リコメンデーション エンジンです。 データは、`userID`、`itemId`、`prefValue` (項目に対するユーザーの嗜好) の形式で受け付けられます。 Mahout では、共起分析を実行して、*ある項目を嗜好するユーザーが他の項目も嗜好する*ということを判断できます。 次に Mahout は、項目の嗜好が似ているユーザーを特定します。これはリコメンデーションの作成に使用できます。

映画を使用した非常にシンプルな例を次に示します。

* **共起**: Joe、Alice、Bob は全員、好きな映画として「*Star Wars (スター ウォーズ)*」、「*The Empire Strikes Back (帝国の逆襲)*」、「*Return of the Jedi (ジェダイの帰還)*」を挙げました。 Mahout では、これらの映画のいずれかを好きなユーザーが他の&2; 作品も好きであると判断します。

* **共起**: Bob と Alice は「*The Phantom Menace (ファントム メナス)*」、「*Attack of the Clones (クローンの攻撃)*」、「*Revenge of the Sith (シスの復讐)*」も好きな映画として選びました。 Mahout では、この例の最初の&3; 作品を好きなユーザーがこれらの&3; 作品も好きであると判断します。

* **類似性のリコメンデーション**: Joe は、この例の最初の&3; 作品を好きな映画として選びました。Mahout では、嗜好が似ている他のユーザーが好きな映画の中で、Joe がまだ観ていない映画を調べます (好み/順位)。 この場合、Mahout では、「*The Phantom Menace (ファントム メナス)*」、「*Attack of the Clones (クローンの攻撃)*」、「*Revenge of the Sith (シスの復讐)*」を推薦します。

### <a name="understanding-the-data"></a>データの説明

[GroupLens Research][movielens] では利便性を高めるために、Mahout と互換性のある形式で映画の評価データを提供します。 このデータは、クラスターの既定の記憶域 ( `/HdiSamples//HdiSamples/MahoutMovieData`) にあります。

2 つのファイル `moviedb.txt` (映画に関する情報) と `user-ratings.txt` があります。 user-ratings.txt ファイルは分析中に使用されます。moviedb.txt は、分析の結果を表示するときに、わかりやすいテキスト情報を提供するために使用されます。

user-ratings.txt に含まれているデータの構造は `userID`、`movieID`、`userRating`、および `timestamp` です。これは、各ユーザーによって映画に対してどれだけ高い評価が付けられているか示しています。 次にデータの例を示します。

    196    242    3    881250949
    186    302    3    891717742
    22    377    1    878887116
    244    51    2    880606923
    166    346    1    886397596

### <a name="run-the-job"></a>ジョブを実行する

次の Windows PowerShell スクリプトを使用して、映画データで Mahout リコメンデーション エンジンを使用するジョブを実行します。

> [!NOTE]
> このファイルは、HDInsight クラスターに接続してジョブを実行するために使用される情報の入力を求めます。 ジョブが完了し、output.txt ファイルがダウンロードされるまで、数分かかる場合があります。

```powershell
# Login to your Azure subscription
# Is there an active Azure subscription?
$sub = Get-AzureRmSubscription -ErrorAction SilentlyContinue
if(-not($sub))
{
    Add-AzureRmAccount
}

# Get cluster info
$clusterName = Read-Host -Prompt "Enter the HDInsight cluster name"
$creds=Get-Credential -Message "Enter the login for the cluster (the default name is usually 'admin')"

#Get the cluster info so we can get the resource group, storage, etc.
$clusterInfo = Get-AzureRmHDInsightCluster -ClusterName $clusterName
$resourceGroup = $clusterInfo.ResourceGroup
$storageAccountName = $clusterInfo.DefaultStorageAccount.split('.')[0]
$container = $clusterInfo.DefaultStorageContainer
$storageAccountKey = (Get-AzureRmStorageAccountKey `
    -Name $storageAccountName `
-ResourceGroupName $resourceGroup)[0].Value

#Create a storage context and upload the file
$context = New-AzureStorageContext `
    -StorageAccountName $storageAccountName `
    -StorageAccountKey $storageAccountKey

#Use Hive to figure out the path to the mahout examples
#Because the file name/path has a version number in it that changes
$queryString = "!ls /usr/hdp/current/mahout-client"
$hiveJobDefinition = New-AzureRmHDInsightHiveJobDefinition -Query $queryString
$hiveJob=Start-AzureRmHDInsightJob -ClusterName $clusterName -JobDefinition $hiveJobDefinition -HttpCredential $creds
$dummy = wait-azurermhdinsightjob -ClusterName $clusterName -JobId $hiveJob.JobId -HttpCredential $creds
#Get the files returned from Hive
$files=get-azurermhdinsightjoboutput -clustername $clusterName -JobId $hiveJob.JobId -DefaultContainer $container -DefaultStorageAccountName $storageAccountName -DefaultStorageAccountKey $storageAccountKey -HttpCredential $creds
#Find the file that starts with mahout-examples and ends in job.jar
$jarFile = $files | select-string "mahout-examples.+job\.jar" | % {$_.Matches.Value}
#Add the full path
$jarFile = "file:///usr/hdp/current/mahout-client/$jarFile"

# The arguments for the mahout job
# * input - the path to the data uploaded to HDInsight
# * output - the path to store output data
# * tempDir - the directory for temp files
$jobArguments = "-s", "SIMILARITY_COOCCURRENCE", `
                "--input", "/HdiSamples/HdiSamples/MahoutMovieData/user-ratings.txt",
                "--output", "/example/out",
                "--tempDir", "/example/temp"

# Create the job definition
$jobDefinition = New-AzureRmHDInsightMapReduceJobDefinition `
    -JarFile $jarFile `
    -ClassName "org.apache.mahout.cf.taste.hadoop.item.RecommenderJob" `
    -Arguments $jobArguments

# Start the job
$job = Start-AzureRmHDInsightJob `
    -ClusterName $clusterName `
    -JobDefinition $jobDefinition `
    -HttpCredential $creds

# Wait on the job to complete
Write-Host "Wait for the job to complete ..." -ForegroundColor Green
Wait-AzureRmHDInsightJob `
        -ClusterName $clusterName `
        -JobId $job.JobId `
        -HttpCredential $creds

# Write out any error information
Write-Host "STDERR"
Get-AzureRmHDInsightJobOutput `
        -Clustername $clusterName `
        -JobId $job.JobId `
        -HttpCredential $creds `
        -DisplayOutputType StandardError

# Download the output
Get-AzureStorageBlobContent `
        -Blob example/out/part-r-00000 `
        -Container $container `
        -Destination output.txt `
        -Context $context
#Download movie and user files for use in displaying results
Get-AzureStorageBlobContent -blob "HdiSamples/HdiSamples/MahoutMovieData/moviedb.txt" `
        -Container $container `
        -Destination moviedb.txt `
        -Context $context
Get-AzureStorageBlobContent -blob "HdiSamples/HdiSamples/MahoutMovieData/user-ratings.txt" `
        -Container $container `
        -Destination user-ratings.txt `
        -Context $context
```

> [!NOTE]
> Mahout ジョブは、ジョブの処理中に作成された一時データを削除しません。 このサンプル ジョブでは `--tempDir` パラメーターを指定し、一時ファイルを特定のディレクトリに分離します。

Mahout ジョブは出力を STDOUT に返しません。 代わりに、指定された出力ディレクトリに **part-r-00000**として格納します。 このファイルは、ワークステーションの現在のディレクトリ内の **output.txt** にダウンロードされます。

ファイルの内容の例を次に示します。

    1    [234:5.0,347:5.0,237:5.0,47:5.0,282:5.0,275:5.0,88:5.0,515:5.0,514:5.0,121:5.0]
    2    [282:5.0,210:5.0,237:5.0,234:5.0,347:5.0,121:5.0,258:5.0,515:5.0,462:5.0,79:5.0]
    3    [284:5.0,285:4.828125,508:4.7543354,845:4.75,319:4.705128,124:4.7045455,150:4.6938777,311:4.6769233,248:4.65625,272:4.649266]
    4    [690:5.0,12:5.0,234:5.0,275:5.0,121:5.0,255:5.0,237:5.0,895:5.0,282:5.0,117:5.0]

最初の列は `userID`です。 "[" と "]" に含まれる値は `movieId`:`recommendationScore` です。

このスクリプトは、出力を読みやすい形式にするために必要な `moviedb.txt` ファイルと `user-ratings.txt` ファイルのダウンロードも実行します。

### <a name="view-the-output"></a>出力を表示する

生成された出力はアプリケーションで使用できるものですが、人間が判読するのは困難です。 サーバーの `moviedb.txt` を使用して、`movieId`を映画名に解決できます。 次の PowerShell スクリプトを使用して、映画名とリコメンデーションを表示します。

```powershell
<#
.SYNOPSIS
    Displays recommendations for movies.
.DESCRIPTION
    Displays recommendations generated by Mahout
    with HDInsight example in a human readable format.
.EXAMPLE
    .\Show-Recommendation -userId 4
        -userDataFile "user-ratings.txt"
        -movieFile "moviedb.txt"
        -recommendationFile "output.txt"
#>

[CmdletBinding(SupportsShouldProcess = $true)]
param(
    #The user ID
    [Parameter(Mandatory = $true)]
    [String]$userId,

    [Parameter(Mandatory = $true)]
    [String]$userDataFile,

    [Parameter(Mandatory = $true)]
    [String]$movieFile,

    [Parameter(Mandatory = $true)]
    [String]$recommendationFile
)
# Read movie ID & description into hash table
Write-Host "Reading movies descriptions" -ForegroundColor Green
$movieById = @{}
foreach($line in Get-Content $movieFile)
{
    $tokens = $line.Split("|")
    $movieById[$tokens[0]] = $tokens[1]
}
# Load movies user has already seen (rated)
# into a hash table
Write-Host "Reading rated movies" -ForegroundColor Green
$ratedMovieIds = @{}
foreach($line in Get-Content $userDataFile)
{
    $tokens = $line.Split("`t")
    if($tokens[0] -eq $userId)
    {
        # Resolve the ID to the movie name
        $ratedMovieIds[$movieById[$tokens[1]]] = $tokens[2]
    }
}
# Read recommendations generated by Mahout
Write-Host "Reading recommendations" -ForegroundColor Green
$recommendations = @{}
foreach($line in get-content $recommendationFile)
{
    $tokens = $line.Split("`t")
    if($tokens[0] -eq $userId)
    {
        #Trim leading/treailing [] and split at ,
        $movieIdAndScores = $tokens[1].TrimStart("[").TrimEnd("]").Split(",")
        foreach($movieIdAndScore in $movieIdAndScores)
        {
            #Split at : and store title and score in a hash table
            $idAndScore = $movieIdAndScore.Split(":")
            $recommendations[$movieById[$idAndScore[0]]] = $idAndScore[1]
        }
        break
    }
}

Write-Host "Rated movies" -ForegroundColor Green
Write-Host "---------------------------" -ForegroundColor Green
$ratedFormat = @{Expression={$_.Name};Label="Movie";Width=40}, `
                @{Expression={$_.Value};Label="Rating"}
$ratedMovieIds | format-table $ratedFormat
Write-Host "---------------------------" -ForegroundColor Green

write-host "Recommended movies" -ForegroundColor Green
Write-Host "---------------------------" -ForegroundColor Green
$recommendationFormat = @{Expression={$_.Name};Label="Movie";Width=40}, `
                        @{Expression={$_.Value};Label="Score"}
$recommendations | format-table $recommendationFormat
```

スクリプトを実行する例を次に示します。

    PS C:\> show-recommendation.ps1 -userId 4 -userDataFile .\user-ratings.txt -movieFile .\moviedb.txt -recommendationFile .\output.txt

出力は次のようになります。

    Reading movies descriptions
    Reading rated movies
    Reading recommendations
    Rated movies
    ---------------------------
    Movie                                    Rating
    -----                                    ------
    Devil's Own, The (1997)                  1
    Alien: Resurrection (1997)               3
    187 (1997)                               2
    (lines ommitted)

    ---------------------------
    Recommended movies
    ---------------------------

    Movie                                    Score
    -----                                    -----
    Good Will Hunting (1997)                 4.6504064
    Swingers (1996)                          4.6862745
    Wings of the Dove, The (1997)            4.6666665
    People vs. Larry Flynt, The (1996)       4.834559
    Everyone Says I Love You (1996)          4.707071
    Secrets & Lies (1996)                    4.818182
    That Thing You Do! (1996)                4.75
    Grosse Pointe Blank (1997)               4.8235292
    Donnie Brasco (1997)                     4.6792455
    Lone Star (1996)                         4.7099237

## <a name="troubleshooting"></a>トラブルシューティング

### <a name="cannot-overwrite-files"></a>ファイルを上書きできない

Mahout ジョブは、処理中に作成された一時ファイルをクリーンアップしません。 さらに、このジョブは既存の出力ファイルを上書きしません。

Mahout ジョブの実行中のエラーを回避するには、実行と実行の間で一時ファイルと出力ファイルを削除するか、一時ディレクトリと出力ディレクトリに一意の名前を使用します。 次の PowerShell スクリプトを使用して、このドキュメントのここまでのスクリプトによって作成されたファイルを削除します。

```powershell
# Login to your Azure subscription
# Is there an active Azure subscription?
$sub = Get-AzureRmSubscription -ErrorAction SilentlyContinue
if(-not($sub))
{
    Add-AzureRmAccount
}

# Get cluster info
$clusterName = Read-Host -Prompt "Enter the HDInsight cluster name"
$creds=Get-Credential -Message "Enter the login for the cluster"

#Get the cluster info so we can get the resource group, storage, etc.
$clusterInfo = Get-AzureRmHDInsightCluster -ClusterName $clusterName
$resourceGroup = $clusterInfo.ResourceGroup
$storageAccountName = $clusterInfo.DefaultStorageAccount.split('.')[0]
$container = $clusterInfo.DefaultStorageContainer
$storageAccountKey = (Get-AzureRmStorageAccountKey `
    -Name $storageAccountName `
-ResourceGroupName $resourceGroup)[0].Value

#Create a storage context and upload the file
$context = New-AzureStorageContext `
    -StorageAccountName $storageAccountName `
    -StorageAccountKey $storageAccountKey

#Azure PowerShell can't delete blobs using wildcard, 
#so have to get a list and delete one at a time
# Start with the output
$blobs = Get-AzureStorageBlob -Container $container -Context $context -Prefix "example/out"
foreach($blob in $blobs)
{
    Remove-AzureStorageBlob -Blob $blob.Name -Container $container -context $context
}
# Next the temp files
$blobs = Get-AzureStorageBlob -Container $container -Context $context -Prefix "example/temp"
foreach($blob in $blobs)
{
    Remove-AzureStorageBlob -Blob $blob.Name -Container $container -context $context
}
```

### <a name="nopowershell"></a>Azure PowerShell で動作しないクラス

次のクラスを使用する Mahout ジョブを Windows PowerShell から使用すると、さまざまなエラー メッセージが返されます。

* org.apache.mahout.utils.clustering.ClusterDumper
* org.apache.mahout.utils.SequenceFileDumper
* org.apache.mahout.utils.vectors.lucene.Driver
* org.apache.mahout.utils.vectors.arff.Driver
* org.apache.mahout.text.WikipediaToSequenceFile
* org.apache.mahout.clustering.streaming.tools.ResplitSequenceFiles
* org.apache.mahout.clustering.streaming.tools.ClusterQualitySummarizer
* org.apache.mahout.classifier.sgd.TrainLogistic
* org.apache.mahout.classifier.sgd.RunLogistic
* org.apache.mahout.classifier.sgd.TrainAdaptiveLogistic
* org.apache.mahout.classifier.sgd.ValidateAdaptiveLogistic
* org.apache.mahout.classifier.sgd.RunAdaptiveLogistic
* org.apache.mahout.classifier.sequencelearning.hmm.BaumWelchTrainer
* org.apache.mahout.classifier.sequencelearning.hmm.ViterbiEvaluator
* org.apache.mahout.classifier.sequencelearning.hmm.RandomSequenceGenerator
* org.apache.mahout.classifier.df.tools.Describe

これらのクラスを使用するジョブを実行するには、SSH を使用して HDInsight クラスターに接続し、コマンド ラインからジョブを実行します。 SSH を使用した Mahout ジョブの実行例については、[Mahout と HDInsight を使用した映画のリコメンデーションの作成 (SSH)](hdinsight-hadoop-mahout-linux-mac.md)に関するページを参照してください。

## <a name="next-steps"></a>次のステップ

ここまで、Mahout の使用方法を学習し、HDInsight でデータを操作するその他の方法を確認してきました。

* [HDInsight での Hive の使用](hdinsight-use-hive.md)
* [HDInsight での Pig の使用](hdinsight-use-pig.md)
* [HDInsight での MapReduce の使用](hdinsight-use-mapreduce.md)

[build]: http://mahout.apache.org/developers/buildingmahout.html
[aps]: /powershell/azureps-cmdlets-docs
[movielens]: http://grouplens.org/datasets/movielens/
[100k]: http://files.grouplens.org/datasets/movielens/ml-100k.zip
[getstarted]: hdinsight-hadoop-linux-tutorial-get-started.md
[upload]: hdinsight-upload-data.md
[ml]: http://en.wikipedia.org/wiki/Machine_learning
[forest]: http://en.wikipedia.org/wiki/Random_forest
[management]: https://manage.windowsazure.com/
[enableremote]: ./media/hdinsight-mahout/enableremote.png
[connect]: ./media/hdinsight-mahout/connect.png
[hadoopcli]: ./media/hdinsight-mahout/hadoopcli.png
[tools]: https://github.com/Blackmist/hdinsight-tools

