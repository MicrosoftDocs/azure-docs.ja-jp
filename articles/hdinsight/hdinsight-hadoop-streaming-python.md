---
title: "HDInsight での Python MapReduce ジョブの開発 | Microsoft Docs"
description: "Linux ベースの HDInsight クラスターで Python MapReduce ジョブを作成、実行する方法を説明します。"
services: hdinsight
documentationcenter: 
author: Blackmist
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: 7631d8d9-98ae-42ec-b9ec-ee3cf7e57fb3
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 02/06/2017
ms.author: larryfr
translationtype: Human Translation
ms.sourcegitcommit: 4f2230ea0cc5b3e258a1a26a39e99433b04ffe18
ms.openlocfilehash: 6ce490fb903d4ed2177b95145bb98fb3eeb0654f
ms.lasthandoff: 03/25/2017


---
# <a name="develop-python-streaming-programs-for-hdinsight"></a>HDInsight 用 Python ストリーミング プログラムの開発

Hadoop には MapReduce に対するストリーミング API が用意されていて、Java 以外の言語の map 関数と reduce 関数を記述することができます。 この記事では、Python を使用して、MapReduce 操作を実行する方法を説明します。

この記事は、 [Python での Hadoop MapReduce プログラムの記述](http://www.michael-noll.com/tutorials/writing-an-hadoop-mapreduce-program-in-python/)に関するページの Michael Noll が公開する情報および例に基づいています。

## <a name="prerequisites"></a>前提条件

この記事の手順を完了するには、次のものが必要です。

* HDInsight クラスターでの Linux ベースの Hadoop

  > [!IMPORTANT]
  > このドキュメントの手順では、Linux を使用する HDInsight クラスターが必要です。 Linux は、バージョン 3.4 以上の HDInsight で使用できる唯一のオペレーティング システムです。 詳細については、[Window での HDInsight の廃止](hdinsight-component-versioning.md#hdi-version-32-and-33-nearing-deprecation-date)に関する記事を参照してください。

* テキスト エディター
  
  > [!IMPORTANT]
  > テキスト エディターでは、行の終わりとして LF を使用する必要があります。 CRLF を使用する場合、これは Linux ベースの HDInsight クラスターで MapReduce ジョブを実行するときにエラーが発生します。 不明な場合は、「 [MapReduce の実行](#run-mapreduce) 」セクションにある省略可能な手順を使用して、CRLF を LF に変換します。

* **SSH と SCP を熟知していること**。 詳細については、[HDInsight での SSH の使用](hdinsight-hadoop-linux-use-ssh-unix.md)に関するページを参照してください。

## <a name="word-count"></a>文字数

この例では、マッパーとレジューサを使用して、基本的な文字カウントを実装します。 マッパーは文をいくつかの単語に分割し、レジューサは単語数や回数を合計して出力を生成します。

次のフローチャートは、map および reduce のフェーズで実際に何が実行されるかを示しています。

![MapReduce の図](./media/hdinsight-hadoop-streaming-python/HDI.WordCountDiagram.png)

## <a name="why-python"></a>Python について

Python は、多くの他の言語より少ないコード行で概念を表現できる高度な汎用プログラム言語です。 迅速なアプリケーションに最適な構造、動的型付けのほか、その洗練された構文といった特徴のため、データ サイエンティストの間で、近年、プロトタイプ言語として一般的になりつつあります。

Python はすべての HDInsight クラスターにインストールされています。

## <a name="streaming-mapreduce"></a>MapReduce ストリーミング

Hadoop では、ジョブで使用される map および reduce のロジックを含むファイルを指定できます。 map および reduce ロジックに固有の要件は次のとおりです。

* **Input**: map および reduce コンポーネントは STDIN から入力データを読み取る必要があります。
* **Output**: map および reduce コンポーネントは STDOUT に出力データを書き込む必要があります。
* **Data format**:使用および生成されるデータは、タブ文字で区切られたキーと値のペアである必要があります。

Python では、STDIN からの読み取りに **sys** モジュールを、STDOUT への印刷に **print** を使用して、これらの要件を簡単に処理できます。 その他、キーと値の間にタブ (`\t`) 文字を使用してデータを簡単に書式設定できます。

## <a name="create-the-mapper-and-reducer"></a>マッパーとリデューサーの作成

マッパーとレジューサは単なるテキスト ファイルで、この場合 **mapper.py** と **reducer.py** であるため、各ファイルで何が実行されるかは明白です。 これらは、好みのエディターを使用して作成できます。

### <a name="mapperpy"></a>Mapper.py

**mapper.py** という名前の新しいファイルを作成し、内容として以下のコードを使用します。

```python
#!/usr/bin/env python

# Use the sys module
import sys

# 'file' in this case is STDIN
def read_input(file):
    # Split each line into words
    for line in file:
        yield line.split()

def main(separator='\t'):
    # Read the data using read_input
    data = read_input(sys.stdin)
    # Process each words returned from read_input
    for words in data:
        # Process each word
        for word in words:
            # Write to STDOUT
            print '%s%s%d' % (word, separator, 1)

if __name__ == "__main__":
    main()
```

コードが読み取られ、解読されるまでしばらく待ちます。

### <a name="reducerpy"></a>reducer.py

**reducer.py** という名前の新しいファイルを作成し、内容として以下のコードを使用します。

```python
#!/usr/bin/env python

# import modules
from itertools import groupby
from operator import itemgetter
import sys

# 'file' in this case is STDIN
def read_mapper_output(file, separator='\t'):
    # Go through each line
    for line in file:
        # Strip out the separator character
        yield line.rstrip().split(separator, 1)

def main(separator='\t'):
    # Read the data using read_mapper_output
    data = read_mapper_output(sys.stdin, separator=separator)
    # Group words and counts into 'group'
    #   Since MapReduce is a distributed process, each word
    #   may have multiple counts. 'group' will have all counts
    #   which can be retrieved using the word as the key.
    for current_word, group in groupby(data, itemgetter(0)):
        try:
            # For each word, pull the count(s) for the word
            #   from 'group' and create a total count
            total_count = sum(int(count) for current_word, count in group)
            # Write to stdout
            print "%s%s%d" % (current_word, separator, total_count)
        except ValueError:
            # Count was not a number, so do nothing
            pass

if __name__ == "__main__":
    main()
```

## <a name="upload-the-files"></a>ファイルのアップロード

**mapper.py** と **reducer.py** はその実行前に、いずれもクラスターのヘッド ノードにある必要があります。 このセクションでは、クラスターにファイルをアップロードするために使用できる `scp` コマンドと Azure PowerShell スクリプトの例を示します。

> [!IMPORTANT]
> `scp` を使用したファイルのアップロードと PowerShell を使用したファイルのアップロードには重要な違いがあります。
>
> * `scp` を使用すると、ファイルはクラスターのプライマリ ヘッド ノード上に配置されます。 これは、後でヘッド ノードに接続して SSH セッションからジョブを実行することを前提としています。
> * PowerShell スクリプトを使用すると、ファイルは、クラスターの既定のストレージに配置されます。 これは、後で PowerShell スクリプトを使用して、リモート クライアントからジョブを実行することを前提としています。

### <a name="upload-using-scp"></a>scp を使用してアップロードする

開発環境で、**mapper.py** と **reducer.py** と同じディレクトリで、次のコマンドを使用します。 **username** は、クラスターの SSH ユーザーに置き換えます。また、**clustername** はクラスターの名前に置き換えます。

`scp mapper.py reducer.py username@clustername-ssh.azurehdinsight.net:`

これにより、ファイルがローカル システムからヘッド ノードにコピーされます。

> [!NOTE]
> SSH アカウントのセキュリティ保護にパスワードを使用している場合は、パスワードの入力が求められます。 SSH キーを使用している場合は、`-i` パラメーターと、秘密キーのパスを使用する必要があることがあります。例`scp -i /path/to/private/key mapper.py reducer.py username@clustername-ssh.azurehdinsight.net:`:

### <a name="upload-using-powershell"></a>PowerShell を使用してアップロードする

1. 開発環境で、`Put-FilesInHDInsight.ps1` という名前の新しいファイルを作成し、このファイルの内容として次のコードを使用します。

    ```PowerShell
    param(
        [Parameter(Mandatory = $true)]
        [String]$clusterName,
        [Parameter(Mandatory = $true)]
        [String]$source,
        [Parameter(Mandatory = $true)]
        [String]$destination
    )
    # Login to your Azure subscription
    # Is there an active Azure subscription?
    $sub = Get-AzureRmSubscription -ErrorAction SilentlyContinue
    if(-not($sub))
    {
        Add-AzureRmAccount
    }

    # Get the cluster info
    $clusterInfo = Get-AzureRmHDInsightCluster -ClusterName $clusterName
    $storageInfo = $clusterInfo.DefaultStorageAccount.split('.')

    # What type of default storage are we using?
    switch ($storageInfo[1])
    {
        "blob" {
            # Get the blob storage information for the cluster
            $resourceGroup = $clusterInfo.ResourceGroup
            $storageAccountName=$storageInfo[0]
            $storageContainer=$clusterInfo.DefaultStorageContainer
            $storageAccountKey=(Get-AzureRmStorageAccountKey `
                -Name $storageAccountName `
                -ResourceGroupName $resourceGroup)[0].Value
            # Create a storage context and upload the file
            $context = New-AzureStorageContext `
                -StorageAccountName $storageAccountName `
                -StorageAccountKey $storageAccountKey
            # Upload the file
            Set-AzureStorageBlobContent `
                -File $source `
                -Blob $destination `
                -Container $storageContainer `
                -Context $context
        }
        "azuredatalakestore" {
            # Get the Data Lake Store name
            $dataLakeStoreName=$storageInfo[0]
            # Get the root of the HDInsight cluster azuredatalakestore
            $clusterRoot=$clusterInfo.DefaultStorageRootPath
            # Upload the file. Prepend the destination with the cluster root
            Import-AzureRmDataLakeStoreItem -AccountName $dataLakeStoreName -Path $source -Destination "$clusterRoot$destination"
        }
        default {
            Throw "Unknown storage type: $storageInfo[1]"
        }
    }
    ```

2. このスクリプトを使用してファイルをアップロードするには、Azure PowerShell プロンプトで次を使用します。

    `.\Put-FilesInHDInsight.ps1 -clusterName <your HDInsight cluster name> -source mapper.py -destination mapper.py`

    メッセージが表示されたら、クラスターの HTTP ログイン資格情報を入力します。

3. `mapper.py` を `reducer.py` に置き換えて、コマンドを繰り返します。 これで、マッパーとレジューサの両方のファイルが、クラスターの既定のストレージにアップロードされます。

## <a name="run-mapreduce-ssh"></a>MapReduce の実行 (SSH)

クラスターに接続し、SSH セッションからストリーミング MapReduce ジョブを実行するには、次の手順を使用します。

1. SSH を使用したクラスターへの接続:
   
   `ssh username@clustername-ssh.azurehdinsight.net`
   
   > [!NOTE]
   > SSH アカウントのセキュリティ保護にパスワードを使用している場合は、パスワードの入力が求められます。 SSH キーを使用している場合は、`-i` パラメーターと、秘密キーのパスを使用する必要があることがあります。例`ssh -i /path/to/private/key username@clustername-ssh.azurehdinsight.net`:

2. (省略可能) ファイルを作成するときに、行の終わりとして CRLF を使用するテキスト エディターを使用した場合、またはエディターが使用する行の終わりがわからない場合、次のコマンドを使用して、mapper.py と reducer.py の CRLF を LF に変換します。
   
    `perl -pi -e 's/\r\n/\n/g' mappery.py`
    `perl -pi -e 's/\r\n/\n/g' reducer.py`

3. MapReduce ジョブを開始するには次のコマンドを使用します。
   
    `yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-streaming.jar -files mapper.py,reducer.py -mapper mapper.py -reducer reducer.py -input /example/data/gutenberg/davinci.txt -output /example/wordcountout`
   
    このコマンドには次のようなものがあります。
   
   * **hadoop-streaming.jar**: MapReduce 操作のストリーミングを実行するときに使用します。 Hadoop と指定した外部 MapReduce コードとの橋渡しを務めます。

   * **-files**: 指定されたファイルがこの MapReduce ジョブに必要なこと、また、すべてのワーカー ノードにコピーする必要があることを Hadoop に伝えます。

   * **-mapper**: マッパーとして使用するファイルを Hadoop に伝えます。

   * **-reducer**: レジューサとして使用するファイルを Hadoop に伝えます。

   * **-input**: 文字数をカウントする入力ファイル

   * **-output**: 出力の書き込み先のディレクトリ
     
     > [!NOTE]
     > このディレクトリはジョブによって作成されます。

ジョブの開始時に一連の **INFO** ステートメントに続いて、**map** および **reduce** 操作がパーセンテージで表示されます。

    15/02/05 19:01:04 INFO mapreduce.Job:  map 0% reduce 0%
    15/02/05 19:01:16 INFO mapreduce.Job:  map 100% reduce 0%
    15/02/05 19:01:27 INFO mapreduce.Job:  map 100% reduce 100%

完了すると、ジョブに関するステータス情報が返されます。

## <a name="run-mapreduce-powershell"></a>MapReduce の実行 (PowerShell)

開発環境で、次の手順を使用して、ストリーミング MapReduce を PowerShell から実行します。 この PowerShell スクリプトは、WebHCat を使用して HDInsight クラスターに接続することでジョブを実行します。

1. `Start-HDInsightStreamingPythonJob` という名前の新しいファイルを作成し、このファイルの内容として次のコードを使用します。

    ```PowerShell
    param(
        [Parameter(Mandatory = $true)]
        [String]$clusterName,
        [Parameter(Mandatory = $true)]
        [String]$inputPath,
        [Parameter(Mandatory = $true)]
        [String]$outputPath
    )
    # Login to your Azure subscription
    # Is there an active Azure subscription?
    $sub = Get-AzureRmSubscription -ErrorAction SilentlyContinue
    if(-not($sub))
    {
        Add-AzureRmAccount
    }

    # Get the login (HTTPS) credentials for the cluster
    $creds=Get-Credential -Message "Enter the login for the cluster" -UserName "admin"

    # Create the streaming job definition
    # Note: This assumes that the mapper.py and reducer.py
    #       are in the root of default storage. If you put them in a 
    #       subdirectory, change the -Files parameter to the correct path.
    $jobDefinition = New-AzureRmHDInsightStreamingMapReduceJobDefinition `
        -Files "/mapper.py", "/reducer.py" `
        -Mapper "mapper.py" `
        -Reducer "reducer.py" `
        -InputPath $inputPath `
        -OutputPath $outputPath

    # Start the job
    $job = Start-AzureRmHDInsightJob `
        -ClusterName $clusterName `
        -JobDefinition $jobDefinition `
        -HttpCredential $creds

    # Wait for the job to complete
    Wait-AzureRmHDInsightJob `
        -JobId $job.JobId `
        -ClusterName $clusterName `
        -HttpCredential $creds
    ```

2. Azure PowerShell プロンプトで次を使用して、ジョブを実行します。

    `.\Start-HDInsightStreamingPythonJob.ps1 -clusterName <your HDInsight cluster name> -inputPath "/example/data/gutenberg/davinci.txt" -outputPath "/example/wordcountout"`

    これは、前にクラスターにアップロードした `mapper.py` ファイルと `reducer.py` ファイルを使用して、`davinci.txt` ファイル内の単語数をカウントします。 出力は、クラスターの既定のストレージの `/example/wordcount` フォルダーに格納されます。

    ジョブが完了すると、次のような情報が表示されます。

    ```
    Cluster         : myhdicluster
    HttpEndpoint    : myhdicluster.azurehdinsight.net
    State           : SUCCEEDED
    JobId           : job_1486046226168_0004
    ParentId        :
    PercentComplete : map 100% reduce 100%
    ExitValue       : 0
    User            : admin
    Callback        :
    Completed       : done
    StatusFolder    : 2017-02-06T19-14-28-a3dda3ca-f489-4287-afc9-b5e16e2e7c7a
    ```

## <a name="view-the-output"></a>出力を表示する

ジョブの出力は、`/example/wordcountout` ディレクトリに格納されます。 これは、SSH セッションで表示するか、ローカルにダウンロードして PowerShell で表示することができます。

クラスターへの SSH セッションでデータを表示するには、次のコマンドを使用します。

`hdfs dfs -text /example/wordcountout/part-00000`

これにより、文字の一覧と、その出現回数が表示されます。 出力データは次のようになります。

```
wrenching       1
wretched        6
wriggling       1
wrinkled,       1
wrinkles        2
wrinkling       2
```

開発環境から PowerShell を使用して出力を表示するには、次の手順を使用します。

1. `Get-FilesInHDInsight.ps1` という名前の新しいファイルを作成し、このファイルの内容として次のコードを使用します。

    ```PowerShell
    param(
        [Parameter(Mandatory = $true)]
        [String]$clusterName,
        [Parameter(Mandatory = $true)]
        [String]$source
    )
    # Login to your Azure subscription
    # Is there an active Azure subscription?
    $sub = Get-AzureRmSubscription -ErrorAction SilentlyContinue
    if(-not($sub))
    {
        Add-AzureRmAccount
    }

    # Get the cluster info
    $clusterInfo = Get-AzureRmHDInsightCluster -ClusterName $clusterName
    $storageInfo = $clusterInfo.DefaultStorageAccount.split('.')

    # What type of default storage are we using?
    switch ($storageInfo[1])
    {
        "blob" {
            # Get the blob storage information for the cluster
            $resourceGroup = $clusterInfo.ResourceGroup
            $storageAccountName=$storageInfo[0]
            $storageContainer=$clusterInfo.DefaultStorageContainer
            $storageAccountKey=(Get-AzureRmStorageAccountKey `
                -Name $storageAccountName `
                -ResourceGroupName $resourceGroup)[0].Value
            # Create a storage context and download the file
            $context = New-AzureStorageContext `
                -StorageAccountName $storageAccountName `
                -StorageAccountKey $storageAccountKey
            # Download the file
            Get-AzureStorageBlobContent `
                -Container $storageContainer `
                -Blob $source `
                -Context $context `
                -Destination "./output.txt"
            # Display the output
            Get-Content "./output.txt"
        }
        "azuredatalakestore" {
            # Get the Data Lake Store name
            $dataLakeStoreName=$storageInfo[0]
            # Get the root of the HDInsight cluster azuredatalakestore
            $clusterRoot=$clusterInfo.DefaultStorageRootPath
            # Download the file. Prepend the destination with the cluster root
            # NOTE: Unlike getting a blob, this just gets the content and no
            #       file is created locally.
            Get-AzureRmDataLakeStoreItemContent -Account $dataLakeStoreName -Path $clusterRoot$source -Confirm
        }
        default {
            Throw "Unknown storage type: $storageInfo[1]"
        }
    }
    ```

2. Azure PowerShell プロンプトで、次を使用してスクリプトを実行し、出力を表示します。

    `Get-FilesInHDInsight.ps1 -clusterName <your HDInsight cluster name> -source "example/wordcountout/part-00000"`

    これにより、文字の一覧と、その出現回数が表示されます。 出力データは次のようになります。

    ```
    wrenching       1
    wretched        6
    wriggling       1
    wrinkled,       1
    wrinkles        2
    wrinkling       2
    ```

## <a name="next-steps"></a>次のステップ

これで、HDInsight でストリーミング MapRedcue ジョブを使用する方法に関する説明は終わりです。次のリンクを使用して、Azure HDInsight を操作するその他の方法について調べることもできます。

* [HDInsight での Hive の使用](hdinsight-use-hive.md)
* [HDInsight の Hadoop での Pig の使用](hdinsight-use-pig.md)
* [HDInsight での MapReduce ジョブの使用](hdinsight-use-mapreduce.md)


