---
title: "Python UDF と Apache Hive および Pig - Azure HDInsight | Microsoft Docs"
description: "HDInsight (Azure の Hadoop テクノロジ スタック) での Hive および Pig の Python ユーザー定義関数 (UDF) の使用方法について説明します。"
services: hdinsight
documentationcenter: 
author: Blackmist
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: c44d6606-28cd-429b-b535-235e8f34a664
ms.service: hdinsight
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: python
ms.topic: article
ms.date: 07/17/2017
ms.author: larryfr
ms.custom: H1Hack27Feb2017,hdinsightactive
ms.translationtype: HT
ms.sourcegitcommit: bde1bc7e140f9eb7bb864c1c0a1387b9da5d4d22
ms.openlocfilehash: ad96b5dcb3bce98abc7ab776880dfec4f4a91620
ms.contentlocale: ja-jp
ms.lasthandoff: 07/21/2017

---
# <a name="use-python-user-defined-functions-udf-with-hive-and-pig-in-hdinsight"></a>HDInsight における Hive および Pig での Python ユーザー定義関数 (UDF) の使用

Azure HDInsight 上の Hadoop の Apache Hive と Pig で Python ユーザー定義関数 (UDF) を使う方法について説明します。

## <a name="python"></a>HDInsight の Python

Python2.7 は HDInsight 3.0 以降に既定でインストールされます。 Python のこのバージョンではストリーム処理に Apache Hive を使うことができます。 ストリーム処理では、Hive と UDF の間のデータの受け渡しに、STDOUT と STDIN を使います。

HDInsight には、Java で記述された Python 実装である Jython も付属しています。 Jython は、Java 仮想マシン上で直接実行し、ストリームを使いません。 Jython は、Pig で Python を使うときに推奨される Python インタープリターです。

> [!WARNING]
> このドキュメントの手順では、次のことが前提条件となっています。 
>
> * ローカル開発環境に Python スクリプトを作成します。
> * ローカル Bash セッションの `scp` コマンドまたは提供された PowerShell スクリプトを使って、HDInsight にスクリプトをアップロードします。
>
> [Azure Cloud Shell (bash)](https://docs.microsoft.com/azure/cloud-shell/overview) プレビューを使って HDInsight を処理する場合は、以下のことが必要です。
>
> * クラウド シェル環境内でスクリプトを作成します。
> * `scp` を使って、クラウド シェルから HDInsight にファイルをアップロードします。
> * クラウド シェルから `ssh` を使って HDInsight に接続し、例を実行します。

## <a name="hivepython"></a>Hive UDF

Python は、HiveQL の `TRANSFORM` ステートメントを通じて Hive から UDF として使うことができます。 たとえば、次の HiveQL は、クラスターの既定の Azure Storage アカウントに格納されている `hiveudf.py` ファイルを呼び出します。

**Linux ベースの HDInsight**

```hiveql
add file wasb:///hiveudf.py;

SELECT TRANSFORM (clientid, devicemake, devicemodel)
    USING 'python hiveudf.py' AS
    (clientid string, phoneLable string, phoneHash string)
FROM hivesampletable
ORDER BY clientid LIMIT 50;
```

**Windows ベースの HDInsight**

```hiveql
add file wasb:///hiveudf.py;

SELECT TRANSFORM (clientid, devicemake, devicemodel)
    USING 'D:\Python27\python.exe hiveudf.py' AS
    (clientid string, phoneLable string, phoneHash string)
FROM hivesampletable
ORDER BY clientid LIMIT 50;
```

> [!NOTE]
> Windows ベースの HDInsight クラスターでは、`USING` 句で python.exe の完全なパスを指定する必要があります。

この例では以下のように処理されます。

1. ファイルの先頭の `add file` ステートメントで `hiveudf.py` ファイルが分散キャッシュに追加されるので、クラスター内のすべてのノードからアクセスできます。
2. `SELECT TRANSFORM ... USING` ステートメントは、`hivesampletable` からデータを選択します。 また、clientid、devicemake、devicemodel の各値を `hiveudf.py` スクリプトに渡します。
3. `AS` 句では、`hiveudf.py` から返されるフィールドを記述します。

<a name="streamingpy"></a>

### <a name="create-the-hiveudfpy-file"></a>hiveudf.py ファイルを作成する


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
4. 処理の完了時には、フィールド間がタブで区切られた単一の行として、STDOUT に出力が書き出される必要があります。 たとえば、「 `print "\t".join([clientid, phone_label, hashlib.md5(phone_label).hexdigest()])`」のように入力します。
5. `line` が読み込まれなくなるまで、`while` ループが繰り返されます。

スクリプトの出力は、`devicemake` と `devicemodel` の入力値を連結したものであり、連結後の値のハッシュです。

HDInsight クラスターでこの例を実行する方法については「[例を実行する](#running)」を参照してください。

## <a name="pigpython"></a>Pig UDF

`GENERATE` ステートメントを使って、Pig から Python スクリプトを UDF として使うことができます。 スクリプトは Jython または C Python を使用して実行できます。

* Jython は JVM 上で実行され、Pig からネイティブに呼び出すことができるのに対し、
* C Python は外部プロセスです。そのため、JVM 上の Pig からのデータが、Python のプロセスで実行されているスクリプトに送信されます。 Python スクリプトの出力が Pig に返送されます。

Python インタープリターを指定するには、Python スクリプトを参照するときに `register` を使います。 次の例では、スクリプトを `myfuncs` として Pig に登録します。

* **Jython を使用するには**: `register '/path/to/pigudf.py' using jython as myfuncs;`
* **C Python を使用するには**: `register '/path/to/pigudf.py' using streaming_python as myfuncs;`

> [!IMPORTANT]
> Jython を使用する場合、pig_jython ファイルへのパスには、ローカル パスまたは WASB:// パスが指定できます。 ただし、C Python を使用する場合は、Pig ジョブの送信に使用しているノードのローカル ファイル システム上のファイルを参照する必要があります。

一度登録したこの例では、Pig Latin は両方について同じです:

```pig
LOGS = LOAD 'wasb:///example/data/sample.log' as (LINE:chararray);
LOG = FILTER LOGS by LINE is not null;
DETAILS = FOREACH LOG GENERATE myfuncs.create_structure(LINE);
DUMP DETAILS;
```

この例では以下のように処理されます。

1. 最初の行は、サンプルのデータ ファイル `sample.log` を `LOGS` に読み込みます。 また、各レコードを `chararray` として定義します。
2. 次の行はすべての null 値を除去し、操作の結果を `LOG`に格納します。
3. 次に、`LOG` のレコードを反復処理し、`GENERATE` を使って、`myfuncs` として読み込まれた Python/Jython スクリプトに含まれる `create_structure` メソッドを呼び出します。 `LINE` を使って、現在のレコードを関数に渡します。
4. 最後に、`DUMP` コマンドを使って出力が STDOUT にダンプされます。 このコマンドは、操作完了後に結果を表示します。

### <a name="create-the-pigudfpy-file"></a>pigudf.py ファイルを作成する

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

Pig Latin の例では、入力の一貫したスキーマがないため、`LINE` 入力を chararray として定義しました。 この Python スクリプトでは、出力用に、データを一貫したスキーマに変換します。

1. `@outputSchema` ステートメントは、Pig に返されるデータの形式を定義します。 この場合、Pig のデータ型である、 **data bag**になります。 この bag には以下のフィールドが含まれ、すべて chararray (文字列) です。

   * date - ログ エントリが作成された日付
   * time - ログ エントリが作成された時刻
   * classname - エントリが作成されたクラスの名前
   * level - ログ レベル
   * detail - ログ エントリの詳細説明

2. 次に、`def create_structure(input)` では、Pig が行項目を渡す関数が定義されています。

3. 例のデータ `sample.log` は、日付、時刻、クラス名、レベル、および詳細のデータを返すスキーマにほぼ従っています。 ただし、`*java.lang.Exception*` で始まる行がいくつか含まれています。 これらの行は、スキーマに合わせて変更する必要があります。 `if` ステートメントでそれらをチェックした後、想定される出力スキーマと一致するように、入力データの `*java.lang.Exception*` 文字列を末尾に移動します。

4. 次に、`split` コマンドを使って、最初の 4 つの空白文字でデータを分割します。 出力は、`date`、`time`、`classname`、`level`、および `detail` に割り当てられます。

5. 最後に、値が Pig に返されます。

Pig に返された時点のデータは、`@outputSchema` ステートメントで定義されている一貫したスキーマになっています。

## <a name="running"></a>例をアップロードして実行する

> [!IMPORTANT]
> **SSH** の手順は、Linux ベースの HDInsight クラスターでのみ実行できます。 **PowerShell** の手順は、Linux または Windows どちらの HDInsight クラスターでも実行できますが、Windows クライアントが必要です。

### <a name="ssh"></a>SSH

SSH の使用方法の詳細については、[HDInsight での SSH の使用](hdinsight-hadoop-linux-use-ssh-unix.md)に関するページをご覧ください。

1. `scp` を使用して HDInsight クラスターにファイルをコピーします。 たとえば、次のコマンドは **mycluster** という名前のクラスターにファイルをコピーします。

    ```bash
    scp hiveudf.py pigudf.py myuser@mycluster-ssh.azurehdinsight.net:
    ```

2. SSH を使用してクラスターに接続します。

    ```bash
    ssh myuser@mycluster-ssh.azurehdinsight.net
    ```

3. 以前アップロードした python のファイルを SSH セッションからクラスターの WASB ストレージに追加します。

    ```bash
    hdfs dfs -put hiveudf.py /hiveudf.py
    hdfs dfs -put pigudf.py /pigudf.py
    ```

ファイルをアップロードした後、次の手順に従って、Hive ジョブと Pig ジョブを実行します。

#### <a name="use-the-hive-udf"></a>Hive UDF を使用する

1. `hive` コマンドを使用して hive シェルを起動します。 シェルが読み込まれると、 `hive>` プロンプトが表示されます。

2. `hive>` プロンプトで、次のクエリを入力します。

   ```hive
   add file wasb:///hiveudf.py;
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

#### <a name="use-the-pig-udf"></a>Pig UDF を使用する

1. `pig` コマンドを使用してシェルを起動します。 シェルが読み込まれると、`grunt>` プロンプトが表示されます。

2. `grunt>` プロンプトで、次のステートメントを入力します。

   ```pig
   Register wasb:///pigudf.py using jython as myfuncs;
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

    変更を加えた後は、Ctrl キーを押しながら X キーを押してエディターを終了します。 Y を選択し、Enter を押して変更を保存します。

6. `pig` コマンドを使用してシェルを再起動します。 `grunt>` プロンプトが出たら、次のステートメントを使って C Python インタープリターを使用する Python スクリプトを実行します。

   ```pig
   Register 'pigudf.py' using streaming_python as myfuncs;
   LOGS = LOAD 'wasb:///example/data/sample.log' as (LINE:chararray);
   LOG = FILTER LOGS by LINE is not null;
   DETAILS = foreach LOG generate myfuncs.create_structure(LINE);
   DUMP DETAILS;
   ```

    このジョブが完了すると、以前に Jython を使用してスクリプトを実行したときと同じ出力が表示されます。

### <a name="powershell-upload-the-files"></a>PowerShell: ファイルをアップロードする

PowerShell を使って、HDInsight サーバーにファイルをアップロードできます。 Python ファイルをアップロードするには、次のスクリプトを使います。

> [!IMPORTANT] 
> このセクションの手順では、Azure PowerShell を使います。 Azure PowerShell の使用方法の詳細については、「[Azure PowerShell のインストールおよび構成方法](/powershell/azure/overview)」をご覧ください。

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
# Change the path to match the file location on your system
$pathToStreamingFile = "C:\path\to\hiveudf.py"
$pathToJythonFile = "C:\path\to\pigudf.py"

$clusterInfo = Get-AzureRmHDInsightCluster -ClusterName $clusterName
$resourceGroup = $clusterInfo.ResourceGroup
$storageAccountName=$clusterInfo.DefaultStorageAccount.split('.')[0]
$container=$clusterInfo.DefaultStorageContainer
$storageAccountKey=(Get-AzureRmStorageAccountKey `
    -Name $storageAccountName `
-ResourceGroupName $resourceGroup)[0].Value

#Create a storage content and upload the file
$context = New-AzureStorageContext `
    -StorageAccountName $storageAccountName `
    -StorageAccountKey $storageAccountKey

Set-AzureStorageBlobContent `
    -File $pathToStreamingFile `
    -Blob "hiveudf.py" `
    -Container $container `
    -Context $context

Set-AzureStorageBlobContent `
    -File $pathToJythonFile `
    -Blob "pigudf.py" `
    -Container $container `
    -Context $context
```
> [!IMPORTANT]
> `C:\path\to` の値を、開発環境でのファイルへのパスに変更します。

このスクリプトは、HDInsight クラスターの情報を取得してから、既定のストレージ アカウント用にアカウントとキーを抽出します。その後、ファイルをコンテナーのルートにアップロードします。

> [!NOTE]
> ファイルをアップロードする方法について詳しくは、「[HDInsight での Hadoop ジョブ用データのアップロード](hdinsight-upload-data.md)」ドキュメントをご覧ください。

#### <a name="powershell-use-the-hive-udf"></a>PowerShell: Hive UDF を使用する

PowerShell を使って、Hive クエリをリモートで実行することもできます。 **hiveudf.py** スクリプトを使う Hive クエリを実行するには、次の PowerShell スクリプトを使います。

> [!IMPORTANT]
> 実行前に、HDInsight クラスターの HTTPs/管理者アカウント情報の入力を求められます。

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

# If using a Windows-based HDInsight cluster, change the USING statement to:
# "USING 'D:\Python27\python.exe hiveudf.py' AS " +
$HiveQuery = "add file wasb:///hiveudf.py;" +
                "SELECT TRANSFORM (clientid, devicemake, devicemodel) " +
                "USING 'python hiveudf.py' AS " +
                "(clientid string, phoneLabel string, phoneHash string) " +
                "FROM hivesampletable " +
                "ORDER BY clientid LIMIT 50;"

$jobDefinition = New-AzureRmHDInsightHiveJobDefinition `
    -Query $HiveQuery

$job = Start-AzureRmHDInsightJob `
    -ClusterName $clusterName `
    -JobDefinition $jobDefinition `
    -HttpCredential $creds
Write-Host "Wait for the Hive job to complete ..." -ForegroundColor Green
Wait-AzureRmHDInsightJob `
    -JobId $job.JobId `
    -ClusterName $clusterName `
    -HttpCredential $creds
# Uncomment the following to see stderr output
# Get-AzureRmHDInsightJobOutput `
#   -Clustername $clusterName `
#   -JobId $job.JobId `
#   -HttpCredential $creds `
#   -DisplayOutputType StandardError
Write-Host "Display the standard output ..." -ForegroundColor Green
Get-AzureRmHDInsightJobOutput `
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

#### <a name="pig-jython"></a>Pig (Jython)

PowerShell を使って Pig Latin ジョブを実行することもできます。 **pigudf.py** スクリプトを使う Pig Latin ジョブを実行するには、次の PowerShell スクリプトを使います。

> [!NOTE]
> PowerShell を使用してジョブをリモートで送信するときは、C Python をインタープリターとして使用することはできません。

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

$PigQuery = "Register wasb:///pigudf.py using jython as myfuncs;" +
            "LOGS = LOAD 'wasb:///example/data/sample.log' as (LINE:chararray);" +
            "LOG = FILTER LOGS by LINE is not null;" +
            "DETAILS = foreach LOG generate myfuncs.create_structure(LINE);" +
            "DUMP DETAILS;"

$jobDefinition = New-AzureRmHDInsightPigJobDefinition -Query $PigQuery

$job = Start-AzureRmHDInsightJob `
    -ClusterName $clusterName `
    -JobDefinition $jobDefinition `
    -HttpCredential $creds

Write-Host "Wait for the Pig job to complete ..." -ForegroundColor Green
Wait-AzureRmHDInsightJob `
    -Job $job.JobId `
    -ClusterName $clusterName `
    -HttpCredential $creds
# Uncomment the following to see stderr output
# Get-AzureRmHDInsightJobOutput `
#    -Clustername $clusterName `
#    -JobId $job.JobId `
#    -HttpCredential $creds `
#    -DisplayOutputType StandardError
Write-Host "Display the standard output ..." -ForegroundColor Green
Get-AzureRmHDInsightJobOutput `
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

```powershell
$original_file ='c:\path\to\hiveudf.py'
$text = [IO.File]::ReadAllText($original_file) -replace "`r`n", "`n"
[IO.File]::WriteAllText($original_file, $text)
```

### <a name="powershell-scripts"></a>PowerShell スクリプト

例の実行に使用した PowerShell スクリプトには、どちらもジョブのエラー出力を表示するコメント行が含まれています。 ジョブについて想定された出力が確認できない場合は、次の行をコメント解除し、エラー情報で問題が示されるかどうかを確認してください。

```powershell
# Get-AzureRmHDInsightJobOutput `
        -Clustername $clusterName `
        -JobId $job.JobId `
        -HttpCredential $creds `
        -DisplayOutputType StandardError
```

エラー情報 (STDERR) とジョブの結果 (STDOUT) は、HDInsight ストレージにも記録されます。

| ジョブ | BLOB コンテナーで確認するファイル |
| --- | --- |
| Hive |/HivePython/stderr<p>/HivePython/stdout |
| Pig |/PigPython/stderr<p>/PigPython/stdout |

## <a name="next"></a>次のステップ

既定で提供されない Python モジュールを読み込む必要がある場合は、[モジュールを Azure HDInsight にデプロイする方法](http://blogs.msdn.com/b/benjguin/archive/2014/03/03/how-to-deploy-a-python-module-to-windows-azure-hdinsight.aspx)に関するブログ記事をご覧ください。

Pig と Hive を使用する他の方法と、MapReduce の使用方法については、次のドキュメントをご覧ください。

* [HDInsight での Hive の使用](hdinsight-use-hive.md)
* [HDInsight の Hadoop での Pig の使用](hdinsight-use-pig.md)
* [HDInsight での MapReduce の使用](hdinsight-use-mapreduce.md)

