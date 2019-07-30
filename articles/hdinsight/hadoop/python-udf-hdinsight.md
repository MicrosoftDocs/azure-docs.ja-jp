---
title: Python UDF と Apache Hive と Apache Pig - Azure HDInsight
description: HDInsight (Azure の Apache Hadoop テクノロジ スタック) 上の Apache Hive と Apache Pig から Python ユーザー定義関数 (UDF) を使用する方法について説明します。
ms.service: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.topic: conceptual
ms.date: 03/15/2019
ms.custom: H1Hack27Feb2017,hdinsightactive
ms.openlocfilehash: 49fd69c124ff9053f3934aefd349e039b437df0d
ms.sourcegitcommit: 4b647be06d677151eb9db7dccc2bd7a8379e5871
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/19/2019
ms.locfileid: "68354965"
---
# <a name="use-python-user-defined-functions-udf-with-apache-hive-and-apache-pig-in-hdinsight"></a>HDInsight 上の Apache Hive と Apache Pig で Python ユーザー定義関数 (UDF) を使用する

Azure HDInsight 上の Apache Hadoop の Apache Hive と Apache Pig で Python ユーザー定義関数 (UDF) を使用する方法について説明します。

## <a name="python"></a>HDInsight の Python

Python2.7 は HDInsight 3.0 以降に既定でインストールされます。 Python のこのバージョンではストリーム処理に Apache Hive を使うことができます。 ストリーム処理では、Hive と UDF の間のデータの受け渡しに、STDOUT と STDIN を使います。

HDInsight には、Java で記述された Python 実装である Jython も付属しています。 Jython は、Java 仮想マシン上で直接実行し、ストリームを使いません。 Jython は、Pig で Python を使うときに推奨される Python インタープリターです。

## <a name="prerequisites"></a>前提条件

* **HDInsight 上の Hadoop クラスター**。 [Linux での HDInsight の概要](apache-hadoop-linux-tutorial-get-started.md)に関するページを参照してください。
* **SSH クライアント**。 詳細については、[SSH を使用して HDInsight (Apache Hadoop) に接続する方法](../hdinsight-hadoop-linux-use-ssh-unix.md)に関するページを参照してください。
* クラスターのプライマリ ストレージの [URI スキーム](../hdinsight-hadoop-linux-information.md#URI-and-scheme)。 Azure Storage では wasb://、Azure Data Lake Storage Gen2 では abfs://、Azure Data Lake Storage Gen1 では adl:// です。 Azure Storage または Data Lake Storage Gen2 で安全な転送が有効になっている場合、URI はそれぞれ wasbs:// または abfss:// になります。「[安全な転送](../../storage/common/storage-require-secure-transfer.md)」も参照してください。
* **ストレージ構成に対する変更の可能性。**  ストレージ アカウントの種類 `BlobStorage` を使用している場合は、「[ストレージの構成](#storage-configuration)」を参照してください。
* 省略可能。  PowerShell を使用する予定の場合は、[AZ モジュール](https://docs.microsoft.com/powershell/azure/new-azureps-module-az)がインストールされている必要があります。

> [!NOTE]  
> この記事で使用されたストレージ アカウントは[安全な転送](../../storage/common/storage-require-secure-transfer.md)が有効になっている Azure Storage なので、`wasbs` がこの記事全体で使用されています。

## <a name="storage-configuration"></a>ストレージの構成
使用しているストレージ アカウントの種類が `Storage (general purpose v1)` または `StorageV2 (general purpose v2)` の場合、アクションは必要ありません。  この記事のプロセスにより、少なくとも `/tezstaging` に対する出力が生成されます。  既定の Hadoop 構成では、サービス `HDFS` の `core-site.xml` 内の `fs.azure.page.blob.dir` 構成変数に `/tezstaging` が含まれます。  この構成により、ディレクトリに対する出力がページ BLOB となります。これは、ストレージ アカウントの種類 `BlobStorage` ではサポートされていません。  この記事で `BlobStorage` を使用するには、`/tezstaging` を `fs.azure.page.blob.dir` 構成変数から削除します。  構成は [Ambari UI](../hdinsight-hadoop-manage-ambari.md) からアクセスすることができます。  そうしないと、`Page blob is not supported for this account type.` というエラー メッセージが表示されます。

> [!WARNING]  
> このドキュメントの手順では、次のことが前提条件となっています。  
>
> * ローカル開発環境に Python スクリプトを作成します。
> * `scp` コマンドまたは提供された PowerShell スクリプトを使って、HDInsight にスクリプトをアップロードします。
>
> [Azure Cloud Shell (bash)](https://docs.microsoft.com/azure/cloud-shell/overview) を使って HDInsight を処理する場合は、以下のことが必要です。
>
> * クラウド シェル環境内でスクリプトを作成します。
> * `scp` を使って、クラウド シェルから HDInsight にファイルをアップロードします。
> * クラウド シェルから `ssh` を使って HDInsight に接続し、例を実行します。

## <a name="hivepython"></a>Apache Hive UDF

Python は、HiveQL の `TRANSFORM` ステートメントを通じて Hive から UDF として使うことができます。 たとえば、次の HiveQL は、クラスターの既定の Azure Storage アカウントに格納されている `hiveudf.py` ファイルを呼び出します。

```hiveql
add file wasbs:///hiveudf.py;

SELECT TRANSFORM (clientid, devicemake, devicemodel)
    USING 'python hiveudf.py' AS
    (clientid string, phoneLabel string, phoneHash string)
FROM hivesampletable
ORDER BY clientid LIMIT 50;
```

この例では以下のように処理されます。

1. ファイルの先頭の `add file` ステートメントで `hiveudf.py` ファイルが分散キャッシュに追加されるので、クラスター内のすべてのノードからアクセスできます。
2. `SELECT TRANSFORM ... USING` ステートメントは、`hivesampletable` からデータを選択します。 また、clientid、devicemake、devicemodel の各値を `hiveudf.py` スクリプトに渡します。
3. `AS` 句では、`hiveudf.py` から返されるフィールドを記述します。

<a name="streamingpy"></a>

### <a name="create-file"></a>ファイルを作成する

開発環境で、`hiveudf.py` という名前のテキスト ファイルを作成します。 このファイルの内容として、以下のコードを使用します。

```python
#!/usr/bin/env python
import sys
import string
import hashlib

while True:
    line = sys.stdin.readline()
    if not line:
        break

    line = string.strip(line, "\n ")
    clientid, devicemake, devicemodel = string.split(line, "\t")
    phone_label = devicemake + ' ' + devicemodel
    print "\t".join([clientid, phone_label, hashlib.md5(phone_label).hexdigest()])
```

このスクリプトは、次のアクションを実行します。

1. STDIN からデータ行を読み取ります。
2. `string.strip(line, "\n ")` を使用することで、末尾の改行文字が削除されます。
3. ストリームの処理中は、すべての値が 1 つの行に含まれ、値と値の間はタブ文字で区切られます。 それにより、 `string.split(line, "\t")` を使用してタブごとに入力を分割し、フィールドのみを返すことができます。
4. 処理の完了時には、フィールド間がタブで区切られた単一の行として、STDOUT に出力が書き出される必要があります。 たとえば、「 `print "\t".join([clientid, phone_label, hashlib.md5(phone_label).hexdigest()])` 」のように入力します。
5. `line` が読み込まれなくなるまで、`while` ループが繰り返されます。

スクリプトの出力は、`devicemake` と `devicemodel` の入力値を連結したものであり、連結後の値のハッシュです。

### <a name="upload-file-shell"></a>ファイルをアップロードする (シェル)
以下のコマンドでは、`sshuser` を実際のユーザー名と置き換えます (異なる場合)。  `mycluster` を実際のクラスター名に置き換えます。  作業ディレクトリにファイルがあることを確認します。

1. `scp` を使用して HDInsight クラスターにファイルをコピーします。 次のコマンドを編集して入力します。

    ```cmd
    scp hiveudf.py sshuser@mycluster-ssh.azurehdinsight.net:
    ```

2. SSH を使用してクラスターに接続します。  次のコマンドを編集して入力します。

    ```cmd
    ssh sshuser@mycluster-ssh.azurehdinsight.net
    ```

3. 以前アップロードした python のファイルを SSH セッションからクラスターのストレージに追加します。

    ```bash
    hdfs dfs -put hiveudf.py /hiveudf.py
    ```

### <a name="use-hive-udf-shell"></a>Hive UDF を使用する (シェル)

1. Hive に接続するには、開いている SSH セッションから次のコマンドを使用します。

    ```bash
    beeline -u 'jdbc:hive2://headnodehost:10001/;transportMode=http'
    ```

    このコマンドで、Beeline クライアントが開始されます。

2. `0: jdbc:hive2://headnodehost:10001/>` プロンプトで、次のクエリを入力します。

   ```hive
   add file wasbs:///hiveudf.py;
   SELECT TRANSFORM (clientid, devicemake, devicemodel)
       USING 'python hiveudf.py' AS
       (clientid string, phoneLabel string, phoneHash string)
   FROM hivesampletable
   ORDER BY clientid LIMIT 50;
   ```

3. 最後の行を入力すると、ジョブが開始されます。 ジョブが完了すると、次の例のような出力が返されます。

        100041    RIM 9650    d476f3687700442549a83fac4560c51c
        100041    RIM 9650    d476f3687700442549a83fac4560c51c
        100042    Apple iPhone 4.2.x    375ad9a0ddc4351536804f1d5d0ea9b9
        100042    Apple iPhone 4.2.x    375ad9a0ddc4351536804f1d5d0ea9b9
        100042    Apple iPhone 4.2.x    375ad9a0ddc4351536804f1d5d0ea9b9

4. Beeline を終了するには、次のコマンドを入力します。

    ```hive
    !q
    ```

### <a name="upload-file-powershell"></a>ファイルをアップロードする (PowerShell)

PowerShell を使って、Hive クエリをリモートで実行することもできます。 作業ディレクトリに `hiveudf.py` があることを確認します。  `hiveudf.py` スクリプトを使う Hive クエリを実行するには、次の PowerShell スクリプトを使います。

```PowerShell
# Login to your Azure subscription
# Is there an active Azure subscription?
$sub = Get-AzSubscription -ErrorAction SilentlyContinue
if(-not($sub))
{
    Connect-AzAccount
}

# Revise file path as needed
$pathToStreamingFile = ".\hiveudf.py"

# Get cluster info
$clusterName = Read-Host -Prompt "Enter the HDInsight cluster name"
$clusterInfo = Get-AzHDInsightCluster -ClusterName $clusterName
$resourceGroup = $clusterInfo.ResourceGroup
$storageAccountName=$clusterInfo.DefaultStorageAccount.split('.')[0]
$container=$clusterInfo.DefaultStorageContainer
$storageAccountKey=(Get-AzStorageAccountKey `
   -ResourceGroupName $resourceGroup `
   -Name $storageAccountName)[0].Value

# Create an Azure Storage context
$context = New-AzStorageContext `
    -StorageAccountName $storageAccountName `
    -StorageAccountKey $storageAccountKey

# Upload local files to an Azure Storage blob
Set-AzStorageBlobContent `
    -File $pathToStreamingFile `
    -Blob "hiveudf.py" `
    -Container $container `
    -Context $context
```

> [!NOTE]  
> ファイルをアップロードする方法の詳細については、「[HDInsight で Apache Hadoop ジョブのデータをアップロードする](../hdinsight-upload-data.md)」ドキュメントを参照してください。


#### <a name="use-hive-udf"></a>Hive UDF を使用する


```PowerShell
# Script should stop on failures
$ErrorActionPreference = "Stop"

# Login to your Azure subscription
# Is there an active Azure subscription?
$sub = Get-AzSubscription -ErrorAction SilentlyContinue
if(-not($sub))
{
    Connect-AzAccount
}

# Get cluster info
$clusterName = Read-Host -Prompt "Enter the HDInsight cluster name"
$creds=Get-Credential -UserName "admin" -Message "Enter the login for the cluster"

$HiveQuery = "add file wasbs:///hiveudf.py;" +
                "SELECT TRANSFORM (clientid, devicemake, devicemodel) " +
                "USING 'python hiveudf.py' AS " +
                "(clientid string, phoneLabel string, phoneHash string) " +
                "FROM hivesampletable " +
                "ORDER BY clientid LIMIT 50;"

# Create Hive job object
$jobDefinition = New-AzHDInsightHiveJobDefinition `
    -Query $HiveQuery

# For status bar updates
$activity="Hive query"

# Progress bar (optional)
Write-Progress -Activity $activity -Status "Starting query..."

# Start defined Azure HDInsight job on specified cluster.
$job = Start-AzHDInsightJob `
    -ClusterName $clusterName `
    -JobDefinition $jobDefinition `
    -HttpCredential $creds

# Progress bar (optional)
Write-Progress -Activity $activity -Status "Waiting on query to complete..."

# Wait for completion or failure of specified job
Wait-AzHDInsightJob `
    -JobId $job.JobId `
    -ClusterName $clusterName `
    -HttpCredential $creds

# Uncomment the following to see stderr output
<#
Get-AzHDInsightJobOutput `
   -Clustername $clusterName `
   -JobId $job.JobId `
   -HttpCredential $creds `
   -DisplayOutputType StandardError
#>

# Progress bar (optional)
Write-Progress -Activity $activity -Status "Retrieving output..."

# Gets the log output
Get-AzHDInsightJobOutput `
    -Clustername $clusterName `
    -JobId $job.JobId `
    -HttpCredential $creds
```

**Hive** ジョブの出力は、次の例のようになります。

    100041    RIM 9650    d476f3687700442549a83fac4560c51c
    100041    RIM 9650    d476f3687700442549a83fac4560c51c
    100042    Apple iPhone 4.2.x    375ad9a0ddc4351536804f1d5d0ea9b9
    100042    Apple iPhone 4.2.x    375ad9a0ddc4351536804f1d5d0ea9b9
    100042    Apple iPhone 4.2.x    375ad9a0ddc4351536804f1d5d0ea9b9


## <a name="pigpython"></a>Apache Pig UDF

`GENERATE` ステートメントを使って、Pig から Python スクリプトを UDF として使うことができます。 スクリプトは Jython または C Python を使用して実行できます。

* Jython は JVM 上で実行され、Pig からネイティブに呼び出すことができるのに対し、
* C Python は外部プロセスです。そのため、JVM 上の Pig からのデータが、Python のプロセスで実行されているスクリプトに送信されます。 Python スクリプトの出力が Pig に返送されます。

Python インタープリターを指定するには、Python スクリプトを参照するときに `register` を使います。 次の例では、スクリプトを `myfuncs` として Pig に登録します。

* **Jython を使用するには**: `register '/path/to/pigudf.py' using jython as myfuncs;`
* **C Python を使用するには**: `register '/path/to/pigudf.py' using streaming_python as myfuncs;`

> [!IMPORTANT]  
> Jython を使用する場合、pig_jython ファイルへのパスには、ローカル パスまたは WASBS:// パスを指定できます。 ただし、C Python を使用する場合は、Pig ジョブの送信に使用しているノードのローカル ファイル システム上のファイルを参照する必要があります。

一度登録したこの例では、Pig Latin は両方について同じです:

```pig
LOGS = LOAD 'wasbs:///example/data/sample.log' as (LINE:chararray);
LOG = FILTER LOGS by LINE is not null;
DETAILS = FOREACH LOG GENERATE myfuncs.create_structure(LINE);
DUMP DETAILS;
```

この例では以下のように処理されます。

1. 最初の行は、サンプルのデータ ファイル `sample.log` を `LOGS` に読み込みます。 また、各レコードを `chararray` として定義します。
2. 次の行はすべての null 値を除去し、操作の結果を `LOG`に格納します。
3. 次に、`LOG` のレコードを反復処理し、`GENERATE` を使って、`myfuncs` として読み込まれた Python/Jython スクリプトに含まれる `create_structure` メソッドを呼び出します。 `LINE` を使って、現在のレコードを関数に渡します。
4. 最後に、`DUMP` コマンドを使って出力が STDOUT にダンプされます。 このコマンドは、操作完了後に結果を表示します。

### <a name="create-file"></a>ファイルを作成する

開発環境で、`pigudf.py` という名前のテキスト ファイルを作成します。 このファイルの内容として、以下のコードを使用します。

<a name="streamingpy"></a>

```python
# Uncomment the following if using C Python
#from pig_util import outputSchema


@outputSchema("log: {(date:chararray, time:chararray, classname:chararray, level:chararray, detail:chararray)}")
def create_structure(input):
    if (input.startswith('java.lang.Exception')):
        input = input[21:len(input)] + ' - java.lang.Exception'
    date, time, classname, level, detail = input.split(' ', 4)
    return date, time, classname, level, detail
```

Pig Latin の例では、入力の一貫したスキーマがないため、`LINE` 入力は chararray と定義されています。 この Python スクリプトでは、出力用に、データを一貫したスキーマに変換します。

1. `@outputSchema` ステートメントは、Pig に返されるデータの形式を定義します。 この場合、Pig のデータ型である、 **data bag**になります。 この bag には以下のフィールドが含まれ、すべて chararray (文字列) です。

   * date - ログ エントリが作成された日付
   * time - ログ エントリが作成された時刻
   * classname - エントリが作成されたクラスの名前
   * level - ログ レベル
   * detail - ログ エントリの詳細説明

2. 次に、`def create_structure(input)` では、Pig が行項目を渡す関数が定義されています。

3. 例のデータ `sample.log` は、日付、時刻、クラス名、レベル、および詳細スキーマにほぼ従っています。 ただし、`*java.lang.Exception*` で始まる行がいくつか含まれています。 これらの行は、スキーマに合わせて変更する必要があります。 `if` ステートメントでそれらをチェックした後、想定される出力スキーマと一致するように、入力データの `*java.lang.Exception*` 文字列を末尾に移動します。

4. 次に、`split` コマンドを使って、最初の 4 つの空白文字でデータを分割します。 出力は、`date`、`time`、`classname`、`level`、および `detail` に割り当てられます。

5. 最後に、値が Pig に返されます。

Pig に返された時点のデータは、`@outputSchema` ステートメントで定義されている一貫したスキーマになっています。



### <a name="upload-file-shell"></a>ファイルをアップロードする (シェル)

以下のコマンドでは、`sshuser` を実際のユーザー名と置き換えます (異なる場合)。  `mycluster` を実際のクラスター名に置き換えます。  作業ディレクトリにファイルがあることを確認します。

1. `scp` を使用して HDInsight クラスターにファイルをコピーします。 次のコマンドを編集して入力します。

    ```cmd
    scp pigudf.py sshuser@mycluster-ssh.azurehdinsight.net:
    ```

2. SSH を使用してクラスターに接続します。  次のコマンドを編集して入力します。

    ```cmd
    ssh sshuser@mycluster-ssh.azurehdinsight.net
    ```

3. 以前アップロードした python のファイルを SSH セッションからクラスターのストレージに追加します。

    ```bash
    hdfs dfs -put pigudf.py /pigudf.py
    ```


### <a name="use-pig-udf-shell"></a>Pig UDF を使用する (シェル)

1. pig に接続するには、開いている SSH セッションから次のコマンドを使用します。

    ```bash
    pig
    ```

2. `grunt>` プロンプトで、次のステートメントを入力します。

   ```pig
   Register wasbs:///pigudf.py using jython as myfuncs;
   LOGS = LOAD 'wasb:///example/data/sample.log' as (LINE:chararray);
   LOG = FILTER LOGS by LINE is not null;
   DETAILS = foreach LOG generate myfuncs.create_structure(LINE);
   DUMP DETAILS;
   ```

3. 次の行を入力すると、ジョブが開始されます。 ジョブが完了すると、次のデータのような出力が返されます。

        ((2012-02-03,20:11:56,SampleClass5,[TRACE],verbose detail for id 990982084))
        ((2012-02-03,20:11:56,SampleClass7,[TRACE],verbose detail for id 1560323914))
        ((2012-02-03,20:11:56,SampleClass8,[DEBUG],detail for id 2083681507))
        ((2012-02-03,20:11:56,SampleClass3,[TRACE],verbose detail for id 1718828806))
        ((2012-02-03,20:11:56,SampleClass3,[INFO],everything normal for id 530537821))

4. `quit` を使って Grunt シェルを終了し、次のコマンドを使ってローカル ファイル システムで pigudf.py ファイルを編集します。

    ```bash
    nano pigudf.py
    ```

5. エディターを使い、次の各行で、行の先頭から`#`文字を削除してコメント解除します:

    ```bash
    #from pig_util import outputSchema
    ```

    この行は、Jython ではなく C Python を使用するように Python スクリプトを変更します。 変更を加えた後は、**Ctrl キーを押しながら X キー**を押してエディターを終了します。 **Y** キーを押し、**Enter** キーを押して変更を保存します。

6. `pig` コマンドを使用してシェルを再起動します。 `grunt>` プロンプトが出たら、次のステートメントを使って C Python インタープリターを使用する Python スクリプトを実行します。

   ```pig
   Register 'pigudf.py' using streaming_python as myfuncs;
   LOGS = LOAD 'wasbs:///example/data/sample.log' as (LINE:chararray);
   LOG = FILTER LOGS by LINE is not null;
   DETAILS = foreach LOG generate myfuncs.create_structure(LINE);
   DUMP DETAILS;
   ```

    このジョブが完了すると、以前に Jython を使用してスクリプトを実行したときと同じ出力が表示されます。


### <a name="upload-file-powershell"></a>ファイルをアップロードする (PowerShell)

PowerShell を使って、Hive クエリをリモートで実行することもできます。 作業ディレクトリに `pigudf.py` があることを確認します。  `pigudf.py` スクリプトを使う Hive クエリを実行するには、次の PowerShell スクリプトを使います。

```PowerShell
# Login to your Azure subscription
# Is there an active Azure subscription?
$sub = Get-AzSubscription -ErrorAction SilentlyContinue
if(-not($sub))
{
    Connect-AzAccount
}

# Revise file path as needed
$pathToJythonFile = ".\pigudf.py"


# Get cluster info
$clusterName = Read-Host -Prompt "Enter the HDInsight cluster name"
$clusterInfo = Get-AzHDInsightCluster -ClusterName $clusterName
$resourceGroup = $clusterInfo.ResourceGroup
$storageAccountName=$clusterInfo.DefaultStorageAccount.split('.')[0]
$container=$clusterInfo.DefaultStorageContainer
$storageAccountKey=(Get-AzStorageAccountKey `
   -ResourceGroupName $resourceGroup `
   -Name $storageAccountName)[0].Value

# Create an Azure Storage context
$context = New-AzStorageContext `
    -StorageAccountName $storageAccountName `
    -StorageAccountKey $storageAccountKey

# Upload local files to an Azure Storage blob
Set-AzStorageBlobContent `
    -File $pathToJythonFile `
    -Blob "pigudf.py" `
    -Container $container `
    -Context $context
```

### <a name="use-pig-udf-powershell"></a>Pig UDF を使用する (PowerShell)

> [!NOTE]  
> PowerShell を使用してジョブをリモートで送信するときは、C Python をインタープリターとして使用することはできません。

PowerShell を使って Pig Latin ジョブを実行することもできます。 `pigudf.py` スクリプトを使う Pig Latin ジョブを実行するには、次の PowerShell スクリプトを使います。

```PowerShell
# Script should stop on failures
$ErrorActionPreference = "Stop"

# Login to your Azure subscription
# Is there an active Azure subscription?
$sub = Get-AzSubscription -ErrorAction SilentlyContinue
if(-not($sub))
{
    Connect-AzAccount
}

# Get cluster info
$clusterName = Read-Host -Prompt "Enter the HDInsight cluster name"
$creds=Get-Credential -UserName "admin" -Message "Enter the login for the cluster"


$PigQuery = "Register wasbs:///pigudf.py using jython as myfuncs;" +
            "LOGS = LOAD 'wasbs:///example/data/sample.log' as (LINE:chararray);" +
            "LOG = FILTER LOGS by LINE is not null;" +
            "DETAILS = foreach LOG generate myfuncs.create_structure(LINE);" +
            "DUMP DETAILS;"

# Create Pig job object
$jobDefinition = New-AzHDInsightPigJobDefinition -Query $PigQuery

# For status bar updates
$activity="Pig job"

# Progress bar (optional)
Write-Progress -Activity $activity -Status "Starting job..."

# Start defined Azure HDInsight job on specified cluster.
$job = Start-AzHDInsightJob `
    -ClusterName $clusterName `
    -JobDefinition $jobDefinition `
    -HttpCredential $creds

# Progress bar (optional)
Write-Progress -Activity $activity -Status "Waiting for the Pig job to complete..."

# Wait for completion or failure of specified job
Wait-AzHDInsightJob `
    -Job $job.JobId `
    -ClusterName $clusterName `
    -HttpCredential $creds

# Uncomment the following to see stderr output
<#
Get-AzHDInsightJobOutput `
    -Clustername $clusterName `
    -JobId $job.JobId `
    -HttpCredential $creds `
    -DisplayOutputType StandardError
#>

# Progress bar (optional)
Write-Progress -Activity $activity "Retrieving output..."

# Gets the log output
Get-AzHDInsightJobOutput `
    -Clustername $clusterName `
    -JobId $job.JobId `
    -HttpCredential $creds
```

**Pig** ジョブの出力は、次のデータのように表示されます。

    ((2012-02-03,20:11:56,SampleClass5,[TRACE],verbose detail for id 990982084))
    ((2012-02-03,20:11:56,SampleClass7,[TRACE],verbose detail for id 1560323914))
    ((2012-02-03,20:11:56,SampleClass8,[DEBUG],detail for id 2083681507))
    ((2012-02-03,20:11:56,SampleClass3,[TRACE],verbose detail for id 1718828806))
    ((2012-02-03,20:11:56,SampleClass3,[INFO],everything normal for id 530537821))

## <a name="troubleshooting"></a>トラブルシューティング

### <a name="errors-when-running-jobs"></a>ジョブ実行時のエラー

Hive ジョブを実行しているときに、次のテキストようなエラーが発生する場合があります。

    Caused by: org.apache.hadoop.hive.ql.metadata.HiveException: [Error 20001]: An error occurred while reading or writing to your custom script. It may have crashed with an error.

この問題は、Python ファイルの行末が原因で発生する場合があります。 多くの Windows 版エディターでは行末に既定でCRLF が使用されていますが、Linux アプリケーションでは通常、行末は LF であることを前提としています。

ファイルを HDInsight にアップロードする前に、次の PowerShell ステートメントを使用して CR 文字を削除できます。

[!code-powershell[main](../../../powershell_scripts/hdinsight/run-python-udf/run-python-udf.ps1?range=148-150)]

### <a name="powershell-scripts"></a>PowerShell スクリプト

例の実行に使用した PowerShell スクリプトには、どちらもジョブのエラー出力を表示するコメント行が含まれています。 ジョブについて想定された出力が確認できない場合は、次の行をコメント解除し、エラー情報で問題が示されるかどうかを確認してください。

[!code-powershell[main](../../../powershell_scripts/hdinsight/run-python-udf/run-python-udf.ps1?range=135-139)]

エラー情報 (STDERR) とジョブの結果 (STDOUT) は、HDInsight ストレージにも記録されます。

| ジョブ | BLOB コンテナーで確認するファイル |
| --- | --- |
| Hive |/HivePython/stderr<p>/HivePython/stdout |
| Pig |/PigPython/stderr<p>/PigPython/stdout |

## <a name="next"></a>次のステップ

既定で提供されない Python モジュールを読み込む必要がある場合は、[モジュールを Azure HDInsight にデプロイする方法](https://blogs.msdn.com/b/benjguin/archive/2014/03/03/how-to-deploy-a-python-module-to-windows-azure-hdinsight.aspx)に関するブログ記事をご覧ください。

Pig と Hive を使用する他の方法と、MapReduce の使用方法については、次のドキュメントをご覧ください。

* [HDInsight での Apache Hive の使用](hdinsight-use-hive.md)
* [HDInsight での Apache Pig の使用](hdinsight-use-pig.md)
* [HDInsight での MapReduce の使用](hdinsight-use-mapreduce.md)
