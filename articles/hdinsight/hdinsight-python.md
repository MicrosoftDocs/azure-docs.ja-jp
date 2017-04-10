---
title: "HDInsight における Python と Hive および Pig の使用 | Microsoft Docs"
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
ms.date: 02/27/2017
ms.author: larryfr
ms.custom: H1Hack27Feb2017,hdinsightactive
translationtype: Human Translation
ms.sourcegitcommit: 4f2230ea0cc5b3e258a1a26a39e99433b04ffe18
ms.openlocfilehash: 88d54250c0ce8feff78e2bf122be1c69dd0d8008
ms.lasthandoff: 03/25/2017

---
# <a name="use-python-user-defined-functions-udf-with-hive-and-pig-in-hdinsight"></a>HDInsight における Hive および Pig での Python ユーザー定義関数 (UDF) の使用

Hive と Pig は HDInsight でのデータの操作に最適ですが、より汎用的な言語が必要になる場合もあります。 Hive と Pig では、さまざまなプログラミング言語を使用してユーザー定義関数 (UDF) を作成できます。 この記事では、Hive と Pig で Python UDF を使用する方法について説明します。

## <a name="requirements"></a>必要条件

* HDInsight クラスター

  > [!IMPORTANT]
  > Linux は、バージョン 3.4 以上の HDInsight で使用できる唯一のオペレーティング システムです。 詳細については、[Window での HDInsight の廃止](hdinsight-component-versioning.md#hdi-version-32-and-33-nearing-deprecation-date)に関する記事を参照してください。

* テキスト エディター

## <a name="python"></a>HDInsight の Python

Python2.7 は HDInsight 3.0 以降のクラスターに既定でインストール済みです。 このバージョンの Python と共に Hive を使用することで、ストリームを処理できます (Hive と Python の間のデータの受け渡しには STDOUT/STDIN を使用します)。

HDInsight には、Java で記述された Python 実装である Jython も付属しています。 Pig ではストリーミングを用いずに Jython と対話できるため、Pig を使用している場合に有効です。 また、標準 Python (C Python) を Pig で使用することもできます。

## <a name="hivepython"></a>Hive と Python

Python は、HiveQL の **TRANSFORM** ステートメントを通じて Hive から UDF として使用することができます。 たとえば、次の HiveQL は **streaming.py** ファイルに格納されている Python スクリプトを呼び出します。

**Linux ベースの HDInsight**

```hiveql
add file wasbs:///streaming.py;

SELECT TRANSFORM (clientid, devicemake, devicemodel)
    USING 'python streaming.py' AS
    (clientid string, phoneLable string, phoneHash string)
FROM hivesampletable
ORDER BY clientid LIMIT 50;
```

**Windows ベースの HDInsight**

```hiveql
add file wasbs:///streaming.py;

SELECT TRANSFORM (clientid, devicemake, devicemodel)
    USING 'D:\Python27\python.exe streaming.py' AS
    (clientid string, phoneLable string, phoneHash string)
FROM hivesampletable
ORDER BY clientid LIMIT 50;
```

> [!NOTE]
> Windows ベースの HDInsight クラスターでは、 **USING** 句で python.exe への完全パスを指定する必要があります。

この例では以下のように処理されます。

1. ファイルの先頭の **add file** ステートメントで **streaming.py** ファイルが分散キャッシュに追加されます。これによってクラスター内のすべてのノードからのアクセスが可能になります。
2. **SELECT TRANSFORM ...USING** ステートメントは、**hivesampletable** からデータを選択します。 また、clientid、devicemake、devicemodel の各値を **streaming.py** スクリプトに渡します。
3. **AS** 句は、**streaming.py** から返されるフィールドを記述します。

<a name="streamingpy"></a> 次に、HiveQL で使用される **streaming.py** ファイルの例を示します。

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
4. 処理の完了時には、フィールド間がタブで区切られた単一の行として、STDOUT に出力が書き出される必要があります。 この処理は `print "\t".join([clientid, phone_label, hashlib.md5(phone_label).hexdigest()])` を使用して実現します。
5. `line` が読み込まれなくなるまで、`while` ループが繰り返されます。

スクリプトの出力は、`devicemake` と `devicemodel` の入力値を連結したものであり、連結後の値のハッシュです。

HDInsight クラスターでこの例を実行する方法については「[例を実行する](#running)」を参照してください。

## <a name="pigpython"></a>Pig と Python

Python スクリプトは、 **GENERATE** ステートメントを通じて Pig から UDF として使用できます。 スクリプトは Jython または C Python を使用して実行できます。

違いは、Jython は JVM 上で実行され、Pig からネイティブに呼び出すことができるのに対し、 C Python は外部プロセスです。そのため、JVM 上の Pig からのデータが、Python のプロセスで実行されているスクリプトに送信されます。 Python スクリプトの出力が Pig に返送されます。

Pig がスクリプトの実行に Jython と C Python のどちらを使うかを指定するには、Pig Latin から Python スクリプトを参照するときに **[登録]** を使ってください。 次の例では、Pig を **myfuncs** としてスクリプトを登録します:

* **Jython を使用するには**: `register '/path/to/pig_python.py' using jython as myfuncs;`
* **C Python を使用するには**: `register '/path/to/pig_python.py' using streaming_python as myfuncs;`

> [!IMPORTANT]
> Jython を使用する場合、pig_jython ファイルへのパスには、ローカル パスまたは WASB:// パスが指定できます。 ただし、C Python を使用する場合は、Pig ジョブの送信に使用しているノードのローカル ファイル システム上のファイルを参照する必要があります。

一度登録したこの例では、Pig Latin は両方について同じです:

```pig
LOGS = LOAD 'wasbs:///example/data/sample.log' as (LINE:chararray);
LOG = FILTER LOGS by LINE is not null;
DETAILS = FOREACH LOG GENERATE myfuncs.create_structure(LINE);
DUMP DETAILS;
```

この例では以下のように処理されます。

1. 最初の行は、サンプルのデータ ファイルである、**sample.log** を **LOGS** に読み込みます。 また、各レコードを **chararray** として定義します。
2. 次の行はすべての null 値を除去し、操作の結果を **LOG**に格納します。
3. 次に、**LOG** のレコードを反復処理し、**GENERATE** を使用して、**myfuncs** として読み込まれた Python/Jython スクリプトに格納されている **create_structure** メソッドを呼び出します。  **LINE** は現在のレコードを関数に渡すために使用されます。
4. 最後に、 **DUMP** コマンドで出力が STDOUT にダンプされます。 これにより、操作完了後の結果が表示されます。

Python スクリプト ファイルは、C Python と Jython で似ています。唯一異なる点は、C Python を使用するときには **pig\_util** からインポートする必要があることです。 **pig\_python.py** スクリプトを次に示します。

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

> [!NOTE]
> "pig_util" は、インストールの必要はありません。スクリプトで自動的に使用可能です。

入力の一貫したスキーマがないため、先ほどは、**LINE** 入力を chararray として定義しました。 この Python スクリプトでは、出力用に、データを一貫したスキーマに変換します。

1. **@outputSchema** ステートメントは、Pig に返されるデータの形式を定義します。 この場合、Pig のデータ型である、 **data bag**になります。 この bag には以下のフィールドが含まれ、すべて chararray (文字列) です。
   
   * date - ログ エントリが作成された日付
   * time - ログ エントリが作成された時刻
   * classname - エントリが作成されたクラスの名前
   * level - ログ レベル
   * detail - ログ エントリの詳細説明

2. 次に、**def create_structure(input)** によって、Pig が行項目を渡す関数を定義します。

3. 例のデータである **sample.log**は通常、日付、時刻、クラス名、レベル、および詳細説明のデータを返すスキーマに従います。 しかし、ここにはスキーマとの一致を図るために、"*java.lang.Exception*" という文字列で始まるいくつかの行を変更する必要があります。 **if** ステートメントでそれらを確認した後、想定される出力スキーマと一致するように、入力データの "*java.lang.Exception*" 文字列を末尾に移動します。

4. 次に、 **split** コマンドを使用して、最初の 4 つの空白文字でデータを分割します。 出力は、**date**、**time**、**classname**、**level**、**detail** に割り当てられます。

5. 最後に、値が Pig に返されます。

データが Pig に返された時点で、**@outputSchema** ステートメントで定義された一貫したスキーマがデータに適用されます。

## <a name="running"></a>例を実行する
Linux ベースの HDInsight クラスターを使用している場合は、**SSH** の手順に従います。 Windows ベースの HDInsight クラスターと、Windows クライアントを使用している場合は、 **PowerShell** の手順を実行します。

### <a name="ssh"></a>SSH

SSH の使用方法の詳細については、[HDInsight での SSH の使用](hdinsight-hadoop-linux-use-ssh-unix.md)に関するページをご覧ください。

1. Python の例の [streaming.py](#streamingpy) と [pig_python.py](#jythonpy) を使用して、開発用コンピューターにファイルのローカル コピーを作成します。

2. `scp` を使用して HDInsight クラスターにファイルをコピーします。 たとえば、次のコマンドは **mycluster** という名前のクラスターにファイルをコピーします。
   
        scp streaming.py pig_python.py myuser@mycluster-ssh.azurehdinsight.net:

3. SSH を使用してクラスターに接続します。 以下の例では、**mycluster** という名前のクラスターにユーザー **myuser** として接続します。
   
        ssh myuser@mycluster-ssh.azurehdinsight.net
4. 以前アップロードした python のファイルを SSH セッションからクラスターの WASB ストレージに追加します。
   
        hdfs dfs -put streaming.py /streaming.py
        hdfs dfs -put pig_python.py /pig_python.py

ファイルをアップロードした後、次の手順に従って、Hive ジョブと Pig ジョブを実行します。

#### <a name="hive"></a>Hive

1. `hive` コマンドを使用して hive シェルを起動します。 シェルが読み込まれると、 `hive>` プロンプトが表示されます。
2. `hive>` プロンプトで、以下を入力します。
   
   ```hive
   add file wasbs:///streaming.py;
   SELECT TRANSFORM (clientid, devicemake, devicemodel)
       USING 'python streaming.py' AS
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

#### <a name="pig"></a>Pig

1. `pig` コマンドを使用してシェルを起動します。 シェルが読み込まれると、 `grunt>` プロンプトが表示されます。

2. `grunt>` プロンプトで、次のステートメントを入力します。
   
   ```pig
   Register wasbs:///pig_python.py using jython as myfuncs;
   LOGS = LOAD 'wasbs:///example/data/sample.log' as (LINE:chararray);
   LOG = FILTER LOGS by LINE is not null;
   DETAILS = foreach LOG generate myfuncs.create_structure(LINE);
   DUMP DETAILS;
   ```

3. 次の行を入力すると、ジョブが開始されます。 ジョブが完了すると、次のような出力が返されます。
   
        ((2012-02-03,20:11:56,SampleClass5,[TRACE],verbose detail for id 990982084))
        ((2012-02-03,20:11:56,SampleClass7,[TRACE],verbose detail for id 1560323914))
        ((2012-02-03,20:11:56,SampleClass8,[DEBUG],detail for id 2083681507))
        ((2012-02-03,20:11:56,SampleClass3,[TRACE],verbose detail for id 1718828806))
        ((2012-02-03,20:11:56,SampleClass3,[INFO],everything normal for id 530537821))

4. `quit` を使用して Grunt シェルを終了し、次のコマンドを使ってローカル ファイル システムで pig_python.py ファイルを編集します:
   
    nano pig_python.py

5. エディターを使い、次の各行で、行の先頭から`#`文字を削除してコメント解除します:
   
        #from pig_util import outputSchema
   
    変更を加えた後は、Ctrl キーを押しながら X キーを押してエディターを終了します。 Y を選択し、Enter を押して変更を保存します。

6. `pig` コマンドを使用してシェルを再起動します。 `grunt>` プロンプトが出たら、次のステートメントを使って C Python インタープリターを使用する Python スクリプトを実行します。
   
   ```pig
   Register 'pig_python.py' using streaming_python as myfuncs;
   LOGS = LOAD 'wasbs:///example/data/sample.log' as (LINE:chararray);
   LOG = FILTER LOGS by LINE is not null;
   DETAILS = foreach LOG generate myfuncs.create_structure(LINE);
   DUMP DETAILS;
   ```
   
    このジョブが完了すると、以前に Jython を使用してスクリプトを実行したときと同じ出力が表示されます。

### <a name="powershell"></a>PowerShell

次の手順では、Azure PowerShell を使用します。 Azure PowerShell の使用方法の詳細については、「[Azure PowerShell のインストールおよび構成方法](/powershell/azureps-cmdlets-docs)」をご覧ください。

1. Python の例の [streaming.py](#streamingpy) と [pig_python.py](#jythonpy) を使用して、開発用コンピューターにファイルのローカル コピーを作成します。
2. 次の PowerShell スクリプトを使用して、**streaming.py** ファイルと **pig\_python.py** ファイルをサーバーにアップロードします。 スクリプトの最初の 3 行に、Azure HDInsight クラスターの名前と、**streaming.py** ファイルおよび **pig\_python.py** ファイルへのパスを入力します。
   
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
    $pathToStreamingFile = "C:\path\to\streaming.py"
    $pathToJythonFile = "C:\path\to\pig_python.py"

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
        -Blob "streaming.py" `
        -Container $container `
        -Context $context

    Set-AzureStorageBlobContent `
        -File $pathToJythonFile `
        -Blob "pig_python.py" `
        -Container $container `
        -Context $context
   ```

    このスクリプトは、HDInsight クラスターの情報を取得してから、既定のストレージ アカウント用にアカウントとキーを抽出します。その後、ファイルをコンテナーのルートにアップロードします。
   
   > [!NOTE]
   > スクリプトをアップロードする他の方法については、ドキュメント「[HDInsight での Hadoop ジョブ用データのアップロード](hdinsight-upload-data.md)」で確認できます。

ファイルのアップロード後、次の PowerShell スクリプトを使用してジョブを開始します。 ジョブが完了すると、PowerShell コンソールに出力が書き込まれます。

#### <a name="hive"></a>Hive

次のスクリプトでは、**streaming.py** スクリプトを実行します。 実行前に、HDInsight クラスターの HTTPs/Admin アカウント情報の入力を求められます。

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
# "USING 'D:\Python27\python.exe streaming.py' AS " +
$HiveQuery = "add file wasbs:///streaming.py;" +
                "SELECT TRANSFORM (clientid, devicemake, devicemodel) " +
                "USING 'python streaming.py' AS " +
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

次のスクリプトでは、Jython インタープリターを使用して **pig_python.py** スクリプトを使用します。 実行前に、HDInsight クラスターの HTTPs/Admin 情報の入力を求められます。

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

$PigQuery = "Register wasbs:///pig_python.py using jython as myfuncs;" +
            "LOGS = LOAD 'wasbs:///example/data/sample.log' as (LINE:chararray);" +
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

**Pig** ジョブの出力は次のように表示されます。

    ((2012-02-03,20:11:56,SampleClass5,[TRACE],verbose detail for id 990982084))
    ((2012-02-03,20:11:56,SampleClass7,[TRACE],verbose detail for id 1560323914))
    ((2012-02-03,20:11:56,SampleClass8,[DEBUG],detail for id 2083681507))
    ((2012-02-03,20:11:56,SampleClass3,[TRACE],verbose detail for id 1718828806))
    ((2012-02-03,20:11:56,SampleClass3,[INFO],everything normal for id 530537821))

## <a name="troubleshooting"></a>トラブルシューティング

### <a name="errors-when-running-jobs"></a>ジョブ実行時のエラー

Hive ジョブを実行しているときに、次のようなエラーが発生する場合があります。

    Caused by: org.apache.hadoop.hive.ql.metadata.HiveException: [Error 20001]: An error occurred while reading or writing to your custom script. It may have crashed with an error.

この問題は、streaming.py ファイルの行末が原因で生じる場合があります。 多くの Windows 版エディターでは行末に既定でCRLF が使用されていますが、Linux アプリケーションでは通常、行末は LF であることを前提としています。

ファイルを HDInsight にアップロードする前に、次の PowerShell ステートメントを使用して CR 文字を削除できます。

```powershell
$original_file ='c:\path\to\streaming.py'
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


