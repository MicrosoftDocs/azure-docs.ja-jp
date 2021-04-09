---
title: HDInsight で Apache Hadoop ジョブのデータをアップロードする
description: HDInsight で Apache Hadoop ジョブのデータをアップロードしてアクセスする方法について説明します。 Azure クラシック CLI、Azure Storage Explorer、Azure PowerShell、Hadoop コマンド ライン、または Sqoop を使用します。
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdiseo17may2017,seoapr2020
ms.date: 04/27/2020
ms.openlocfilehash: a8d21b88298b6092c869b89d48fe4c259c2365b6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "98931350"
---
# <a name="upload-data-for-apache-hadoop-jobs-in-hdinsight"></a>HDInsight で Apache Hadoop ジョブのデータをアップロードする

HDInsight では、Azure Storage および Azure Data Lake Storage 上で Hadoop 分散ファイル システム (HDFS) を利用できます。 このストレージには、Gen1 と Gen2 が含まれています。 Azure Storage と Data Lake Storage Gen1 および Gen2 は、HDFS 拡張機能として設計されています。 Hadoop 環境のすべてのコンポーネントを使用し、管理対象のデータを直接操作できます。 Azure Storage、Data Lake Storage Gen1、Gen2 は個別のファイル システムです。 システムは、データを格納し、そのデータを計算するために最適化されています。 Azure Storage を使用するメリットの詳細については、[HDInsight での Azure Storage の使用](hdinsight-hadoop-use-blob-storage.md)に関する記事をご覧ください。 また、[HDInsight での Data Lake Storage Gen1 の使用](hdinsight-hadoop-use-data-lake-storage-gen1.md)および [HDInsight での Data Lake Storage Gen2 の使用](hdinsight-hadoop-use-data-lake-storage-gen2.md)に関する記事も参照してください。

## <a name="prerequisites"></a>前提条件

開始する前に、次の要件にご注意ください。

* Azure HDInsight クラスター。 手順については、[Azure HDInsight のクイックスタート](hadoop/apache-hadoop-linux-tutorial-get-started.md)に関するページをご覧ください。
* 次の記事に関する知識
    * [HDInsight での Azure Storage の使用](hdinsight-hadoop-use-blob-storage.md)
    * [HDInsight での Data Lake Storage Gen1 の使用](hdinsight-hadoop-use-data-lake-storage-gen1.md)
    * [HDInsight での Data Lake Storage Gen2 の使用](hdinsight-hadoop-use-data-lake-storage-gen2.md)  

## <a name="upload-data-to-azure-storage"></a>Azure Storage へのデータのアップロード

### <a name="utilities"></a>Utilities

Microsoft では、Azure Storage を操作する次のユーティリティを提供しています。

| ツール | Linux | OS X | Windows |
| --- |:---:|:---:|:---:|
| [Azure Portal](../storage/blobs/storage-quickstart-blobs-portal.md) |✔ |✔ |✔ |
| [Azure CLI](../storage/blobs/storage-quickstart-blobs-cli.md) |✔ |✔ |✔ |
| [Azure PowerShell](../storage/blobs/storage-quickstart-blobs-powershell.md) | | |✔ |
| [AzCopy](../storage/common/storage-use-azcopy-v10.md) |✔ | |✔ |
| [Hadoop コマンド](#hadoop-command-line) |✔ |✔ |✔ |

> [!NOTE]  
> Hadoop コマンドは、HDInsight クラスター上でのみ使用できます。 このコマンドでは、ローカル ファイル システムのデータを Azure Storage 内に読み込むことのみが可能です。  

### <a name="hadoop-command-line"></a>Hadoop コマンド ライン

Hadoop コマンド ラインは、クラスターのヘッド ノードに既にデータが存在している場合に、Azure Storage BLOB にデータを格納する際にのみ役立ちます。

Hadoop コマンドを使用するには、最初に [SSH または PuTTY](hdinsight-hadoop-linux-use-ssh-unix.md) を使用してヘッドノードに接続する必要があります。

接続したら、次の構文を使用してファイルをストレージにアップロードすることができます。

```bash
hadoop fs -copyFromLocal <localFilePath> <storageFilePath>
```

たとえば、`hadoop fs -copyFromLocal data.txt /example/data/data.txt` のように指定します。

HDInsight の既定のファイル システムは Azure Storage 内にあるため、/example/data/data.txt は実際には Azure Storage 内にあります。 このファイルは次のように表すこともできます。

`wasbs:///example/data/data.txt`

or

`wasbs://<ContainerName>@<StorageAccountName>.blob.core.windows.net/example/data/davinci.txt`

ファイルに使用するその他の Hadoop コマンドの一覧は、[https://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-common/FileSystemShell.html](https://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-common/FileSystemShell.html) をご覧ください。

> [!WARNING]  
> Apache HBase クラスターでは、データ書き込み時に使われる既定のブロック サイズは 256 KB です。 HBase API または REST API を使うときは問題なく動きますが、`hadoop` または `hdfs dfs` コマンドを使って 12 GB より大きいデータを書き込むとエラーになります。 詳しくは、[BLOB への書き込みに関するストレージ例外](hdinsight-troubleshoot-hdfs.md#storage-exception-for-write-on-blob)についての記事をご覧ください。

### <a name="graphical-clients"></a>グラフィカル クライアント

Azure Storage を操作するためのグラフィカル インターフェイスを提供するアプリケーションもいくつかあります。 これらのアプリケーションの一部を次の一覧表に示します。

| Client | Linux | OS X | Windows |
| --- |:---:|:---:|:---:|
| [Microsoft Visual Studio Tools for HDInsight](hadoop/apache-hadoop-visual-studio-tools-get-started.md#explore-linked-resources) |✔ |✔ |✔ |
| [Azure 記憶域エクスプローラー](../storage/blobs/storage-quickstart-blobs-storage-explorer.md) |✔ |✔ |✔ |
| [`Cerulea`](https://www.cerebrata.com/products/cerulean/features/azure-storage) | | |✔ |
| [CloudXplorer](https://clumsyleaf.com/products/cloudxplorer) | | |✔ |
| [Microsoft Azure 用の CloudBerry Explorer](https://www.cloudberrylab.com/free-microsoft-azure-explorer.aspx) | | |✔ |
| [Cyberduck](https://cyberduck.io/) | |✔ |✔ |

## <a name="mount-azure-storage-as-local-drive"></a>Azure Storage をローカル ドライブとしてマウントする

「[Mount Azure Storage as Local Drive (Azure Storage をローカル ドライブとしてマウントする)](/archive/blogs/bigdatasupport/mount-azure-blob-storage-as-local-drive)」を参照してください。

## <a name="upload-using-services"></a>サービスを使用してアップロードする

### <a name="azure-data-factory"></a>Azure Data Factory

Azure Data Factory はフル マネージドのサービスで、データの保存、処理、移動の各サービスを効率的で適用性のある信頼性の高いデータ生成パイプラインとして構成します。

|ストレージの種類|ドキュメント|
|----|----|
|Azure BLOB ストレージ|[Copy data to or from Azure Blob storage by using Azure Data Factory (Azure Data Factory を使用して、Azure Blob ストレージをコピー先、またはコピー元にして、データをコピーする)](../data-factory/connector-azure-blob-storage.md)|
|Azure Data Lake Storage Gen1|[Azure Data Factory を使用して Azure Data Lake Storage Gen1 との間でデータをコピーする](../data-factory/connector-azure-data-lake-store.md)|
|Azure Data Lake Storage Gen2 |[Load data into Azure Data Lake Storage Gen2 with Azure Data Factory (Azure Data Factory を使用して Azure Data Lake Storage Gen2 内にデータを読み込む)](../data-factory/load-azure-data-lake-storage-gen2.md)|

### <a name="apache-sqoop"></a>Apache Sqoop

Sqoop は、Hadoop とリレーショナル データベース間でデータを転送するためのツールです。 これを使用して、SQL Server、MySQL、Oracle などのリレーショナル データベース管理システム (RDBMS) からデータをインポートします。 その後、Hadoop 分散ファイル システム (HDFS) にデータをインポートし、 MapReduce または Hive を使用して Hadoop のデータを変換し、そのデータを RDBMS へ取り込んだりできます。

詳細については、[HDInsight での Sqoop の使用](hadoop/hdinsight-use-sqoop.md)に関するページを参照してください。

### <a name="development-sdks"></a>開発 SDK

Azure Storage には、次のプログラミング言語で Azure SDK を使用してアクセスすることもできます。

* .NET
* Java
* Node.js
* PHP
* Python
* Ruby

Azure SDK のインストールの詳細については、 [Azure のダウンロード](https://azure.microsoft.com/downloads/)

## <a name="next-steps"></a>次のステップ

ここでは、HDInsight にデータを取り込む方法を説明しました。次の記事でデータの分析について学習してください。

* [Azure HDInsight の概要](hadoop/apache-hadoop-linux-tutorial-get-started.md)
* [プログラムによる Apache Hadoop ジョブの送信](hadoop/submit-apache-hadoop-jobs-programmatically.md)
* [HDInsight での Apache Hive の使用](hadoop/hdinsight-use-hive.md)