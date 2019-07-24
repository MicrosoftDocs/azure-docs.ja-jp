---
title: HDInsight で Apache Hadoop ジョブのデータをアップロードする
description: Azure クラシック CLI、Azure Storage Explorer、Azure PowerShell、Hadoop コマンド ライン、または Sqoop を使用して、HDInsight で Apache Hadoop ジョブのデータをアップロードしてアクセスする方法について説明します。
keywords: ETL Hadoop, Hadoop へのデータの取得, Hadoop ロード データ
services: hdinsight
author: hrasheed-msft
ms.reviewer: jasonh
ms.author: hrasheed
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.topic: conceptual
ms.date: 02/08/2019
ms.openlocfilehash: a56f2546eec12d04dcc64665e60a9874b8e991b6
ms.sourcegitcommit: c3d1aa5a1d922c172654b50a6a5c8b2a6c71aa91
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/17/2019
ms.locfileid: "59684142"
---
# <a name="upload-data-for-apache-hadoop-jobs-in-hdinsight"></a>HDInsight で Apache Hadoop ジョブのデータをアップロードする

Azure HDInsight には、Azure Storage と Azure Data lake Storage (Gen1 および Gen2) を経由した、フル機能を備えた Hadoop 分散ファイル システム (HDFS) が用意されています。 Azure Storage と Data Lake Storage Gen1 および Gen2 は、顧客にシームレスなエクスペリエンスを提供する HDFS 拡張機能として設計されています。 Hadoop エコシステムのすべてのコンポーネントを使用し、管理対象のデータを直接操作できます。 Azure Storage、Data Lake Storage Gen1 および Gen2 は、そのデータに対する格納と計算のために最適化された個別のファイル システムです。 Azure Storage を使用するメリットについては、[HDInsight での Azure Storage の使用][hdinsight-storage]、[HDInsight での Data Lake Storage Gen1 の使用](hdinsight-hadoop-use-data-lake-store.md)、および [HDInsight での Data Lake Storage Gen2 の使用](hdinsight-hadoop-use-data-lake-storage-gen2.md)のそれぞれに関するページを参照してください。

## <a name="prerequisites"></a>前提条件

開始する前に、次の要件にご注意ください。

* Azure HDInsight クラスター。 手順については、[Azure HDInsight の概要][hdinsight-get-started]または [HDInsight クラスターの作成](hdinsight-hadoop-provision-linux-clusters.md)に関するページを参照してください。
* 次の記事に関する知識

    - [HDInsight での Azure Storage の使用][hdinsight-storage]
    - [HDInsight での Data Lake Storage Gen1 の使用](hdinsight-hadoop-use-data-lake-store.md)
    - [HDInsight での Data Lake Storage Gen2 の使用](hdinsight-hadoop-use-data-lake-storage-gen2.md)  

## <a name="upload-data-to-azure-storage"></a>Azure Storage へのデータのアップロード

## <a name="utilities"></a>Utilities
Microsoft では、Azure Storage を操作する次のユーティリティを提供しています。

| ツール | Linux | OS X | Windows |
| --- |:---:|:---:|:---:|
| [Azure Portal](../storage/blobs/storage-quickstart-blobs-portal.md) |✔ |✔ |✔ |
| [Azure CLI](../storage/blobs/storage-quickstart-blobs-cli.md) |✔ |✔ |✔ |
| [Azure PowerShell](../storage/blobs/storage-quickstart-blobs-powershell.md) | | |✔ |
| [AzCopy](../storage/common/storage-use-azcopy-v10.md) |✔ | |✔ |
| [Hadoop コマンド](#commandline) |✔ |✔ |✔ |


> [!NOTE]  
> Hadoop コマンドは、HDInsight クラスター上でのみ使用できます。 このコマンドでは、ローカル ファイル システムのデータを Azure Storage 内に読み込むことのみが可能です。  


## <a id="commandline"></a>Hadoop コマンド ライン
Hadoop コマンド ラインは、クラスターのヘッド ノードに既にデータが存在している場合に、Azure Storage BLOB にデータを格納する際にのみ役立ちます。

Hadoop コマンドを使用するには、最初に [SSH または PuTTY](hdinsight-hadoop-linux-use-ssh-unix.md) を使用してヘッドノードに接続する必要があります。

接続したら、次の構文を使用してファイルをストレージにアップロードすることができます。

```bash
hadoop -copyFromLocal <localFilePath> <storageFilePath>
```

たとえば、`hadoop fs -copyFromLocal data.txt /example/data/data.txt` のように指定します。

HDInsight の既定のファイル システムは Azure Storage にあるため、/example/data.txt は実際は Azure Storage 上にあります。 このファイルは次のように表すこともできます。

    wasb:///example/data/data.txt

or

    wasb://<ContainerName>@<StorageAccountName>.blob.core.windows.net/example/data/davinci.txt

ファイルに使用するその他の Hadoop コマンドの一覧は、[https://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-common/FileSystemShell.html](https://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-common/FileSystemShell.html) をご覧ください。

> [!WARNING]  
> Apache HBase クラスターでは、データ書き込み時に使われる既定のブロック サイズは 256 KB です。 HBase API または REST API を使うときは問題なく動きますが、`hadoop` または `hdfs dfs` コマンドを使って 12 GB より大きいデータを書き込むとエラーになります。 詳細については、この記事の「[BLOB への書き込みに関するストレージ例外](#storageexception)」セクションをご覧ください。

## <a name="graphical-clients"></a>グラフィカル クライアント
Azure Storage を操作するためのグラフィカル インターフェイスを提供するアプリケーションもいくつかあります。 これらのアプリケーションの一部を次の一覧表に示します。

| Client | Linux | OS X | Windows |
| --- |:---:|:---:|:---:|
| [Microsoft Visual Studio Tools for HDInsight](hadoop/apache-hadoop-visual-studio-tools-get-started.md#explore-linked-resources) |✔ |✔ |✔ |
| [Azure Storage Explorer](../storage/blobs/storage-quickstart-blobs-storage-explorer.md) |✔ |✔ |✔ |
| [Cerulea](https://www.cerebrata.com/products/cerulean/features/azure-storage) | | |✔ |
| [CloudXplorer](http://clumsyleaf.com/products/cloudxplorer) | | |✔ |
| [Microsoft Azure 用の CloudBerry Explorer](https://www.cloudberrylab.com/free-microsoft-azure-explorer.aspx) | | |✔ |
| [Cyberduck](https://cyberduck.io/) | |✔ |✔ |


## <a name="mount-azure-storage-as-local-drive"></a>Azure Storage をローカル ドライブとしてマウントする
「[Mount Azure Storage as Local Drive (Azure Storage をローカル ドライブとしてマウントする)](https://blogs.msdn.com/b/bigdatasupport/archive/2014/01/09/mount-azure-blob-storage-as-local-drive.aspx)」を参照してください。

## <a name="upload-using-services"></a>サービスを使用してアップロードする
### <a name="azure-data-factory"></a>Azure Data Factory
Azure Data Factory はフル マネージドのサービスで、データの保存、データの処理、データの移動の各サービスを効率的かつスケーラブルで信頼性の高いデータ生成パイプラインとして構成します。

|ストレージの種類|ドキュメント|
|----|----|
|Azure BLOB ストレージ|[Copy data to or from Azure Blob storage by using Azure Data Factory (Azure Data Factory を使用して、Azure Blob ストレージをコピー先、またはコピー元にして、データをコピーする)](../data-factory/connector-azure-blob-storage.md)|
|Azure Data Lake Storage Gen1|[Copy data to or from Azure Data Lake Storage Gen1 by using Azure Data Factory (Azure Data Factory を使用して、Azure Data Lake Storage Gen1 をコピー先、またはコピー元にして、データをコピーする)](../data-factory/connector-azure-data-lake-store.md)|
|Azure Data Lake Storage Gen2 |[Load data into Azure Data Lake Storage Gen2 with Azure Data Factory (Azure Data Factory を使用して Azure Data Lake Storage Gen2 内にデータを読み込む)](../data-factory/load-azure-data-lake-storage-gen2.md)|

### <a id="sqoop"></a>Apache Sqoop
Sqoop は、Hadoop とリレーショナル データベース間でデータを転送するためのツールです。 このツールを使用して、SQL、MySQL、Oracle などのリレーショナル データベース管理システム (RDBMS) から Hadoop 分散ファイル システム (HDFS) へデータをインポートしたり、MapReduce または Hive を使用して Hadoop のデータを変換し、そのデータを RDBMS へ取り込んだりできます。

詳細については、[HDInsight での Sqoop の使用][hdinsight-use-sqoop]に関するページを参照してください。

### <a name="development-sdks"></a>開発 SDK
Azure Storage には、次のプログラミング言語で Azure SDK を使用してアクセスすることもできます。

* .NET
* Java
* Node.js
* PHP
* Python
* Ruby

Azure SDK のインストールの詳細については、 [Azure のダウンロード](https://azure.microsoft.com/downloads/)

## <a name="troubleshooting"></a>トラブルシューティング
### <a id="storageexception"></a>BLOB への書き込みに関するストレージ例外
**現象**:`hadoop` または `hdfs dfs` コマンドを使って HBase クラスターで 12 GB 以上のファイルを書き込むと、次のエラーが発生する可能性があります。

    ERROR azure.NativeAzureFileSystem: Encountered Storage Exception for write on Blob : example/test_large_file.bin._COPYING_ Exception details: null Error Code : RequestBodyTooLarge
    copyFromLocal: java.io.IOException
            at com.microsoft.azure.storage.core.Utility.initIOException(Utility.java:661)
            at com.microsoft.azure.storage.blob.BlobOutputStream$1.call(BlobOutputStream.java:366)
            at com.microsoft.azure.storage.blob.BlobOutputStream$1.call(BlobOutputStream.java:350)
            at java.util.concurrent.FutureTask.run(FutureTask.java:262)
            at java.util.concurrent.Executors$RunnableAdapter.call(Executors.java:471)
            at java.util.concurrent.FutureTask.run(FutureTask.java:262)
            at java.util.concurrent.ThreadPoolExecutor.runWorker(ThreadPoolExecutor.java:1145)
            at java.util.concurrent.ThreadPoolExecutor$Worker.run(ThreadPoolExecutor.java:615)
            at java.lang.Thread.run(Thread.java:745)
    Caused by: com.microsoft.azure.storage.StorageException: The request body is too large and exceeds the maximum permissible limit.
            at com.microsoft.azure.storage.StorageException.translateException(StorageException.java:89)
            at com.microsoft.azure.storage.core.StorageRequest.materializeException(StorageRequest.java:307)
            at com.microsoft.azure.storage.core.ExecutionEngine.executeWithRetry(ExecutionEngine.java:182)
            at com.microsoft.azure.storage.blob.CloudBlockBlob.uploadBlockInternal(CloudBlockBlob.java:816)
            at com.microsoft.azure.storage.blob.CloudBlockBlob.uploadBlock(CloudBlockBlob.java:788)
            at com.microsoft.azure.storage.blob.BlobOutputStream$1.call(BlobOutputStream.java:354)
            ... 7 more

**原因**:HDInsight クラスター上の HBase では、Azure Storage に書き込むときに既定のブロック サイズは 256 KB です。 HBase API または REST API では問題ありませんが、`hadoop` または `hdfs dfs` コマンドライン ユーティリティを使うとエラーになります。

**解決方法**:`fs.azure.write.request.size` を使ってさらに大きいブロック サイズを指定します。 これは、`-D` パラメーターを使うことで、利用状況に応じて指定できます。 `hadoop` コマンドでこのパラメーターを使う例を次に示します。

```bash
hadoop -fs -D fs.azure.write.request.size=4194304 -copyFromLocal test_large_file.bin /example/data
```

Apache Ambari を使うことで、`fs.azure.write.request.size` の値をグローバルに増やすこともできます。 Ambari Web UI で値を変更するには、次の手順を使えます。

1. ブラウザーで、クラスターの Ambari Web UI に移動します。 これは https://CLUSTERNAME.azurehdinsight.net です。**CLUSTERNAME** はクラスターの名前です。

    プロンプトが表示されたら、クラスターの管理者名とパスワードを入力します。
2. 画面の左側にある **[HDFS]** を選び、**[Configs (構成)]** を選びます。
3. **[Filter... (フィルター...)]** フィールドに「`fs.azure.write.request.size`」と入力します。 ページの中央にフィールドと現在の値が表示されます。
4. 値を 262144 (256 KB) から新しい値に変更します。 たとえば、4194304 (4 MB) に変更します。

![Ambari Web UI で値を変更する画像](./media/hdinsight-upload-data/hbase-change-block-write-size.png)

Ambari の使用について詳しくは、「[Apache Ambari Web UI を使用した HDInsight クラスターの管理](hdinsight-hadoop-manage-ambari.md)」をご覧ください。

## <a name="next-steps"></a>次の手順
ここでは、HDInsight にデータを取り込む方法を説明しました。次の記事でデータの分析方法を学習してください。

* [Azure HDInsight の概要][hdinsight-get-started]
* [プログラムによる Apache Hadoop ジョブの送信][hdinsight-submit-jobs]
* [HDInsight での Apache Hive の使用][hdinsight-use-hive]
* [HDInsight での Apache Pig の使用][hdinsight-use-pig]

[hdinsight-use-sqoop]:hadoop/hdinsight-use-sqoop.md
[hdinsight-storage]: hdinsight-hadoop-use-blob-storage.md
[hdinsight-submit-jobs]:hadoop/submit-apache-hadoop-jobs-programmatically.md
[hdinsight-get-started]:hadoop/apache-hadoop-linux-tutorial-get-started.md
[hdinsight-use-hive]:hadoop/hdinsight-use-hive.md
[hdinsight-use-pig]:hadoop/hdinsight-use-pig.md
