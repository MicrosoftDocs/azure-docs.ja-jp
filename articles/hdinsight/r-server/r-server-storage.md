---
title: HDInsight の ML Services 向けの Azure storage ソリューション - Azure
description: HDInsight の ML Services で利用できるさまざまなストレージ オプションについて説明します
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: how-to
ms.date: 01/02/2020
ms.openlocfilehash: ddc48025de164ff68fb539a293e06bae09171742
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "98943908"
---
# <a name="azure-storage-solutions-for-ml-services-on-azure-hdinsight"></a>Azure HDInsight の ML Services 向けの Azure storage ソリューション

HDInsight 上の ML Services には、分析結果を含んだオブジェクトや、データ、コードを永続化するための異なるストレージ ソリューションがあります。 そのソリューションの例を次に示します。

- [Azure BLOB Storage](https://azure.microsoft.com/services/storage/blobs/)
- [Azure Data Lake Storage Gen1](https://azure.microsoft.com/services/storage/data-lake-storage/)
- [Azure File Storage](https://azure.microsoft.com/services/storage/files/)

ご自身の HDInsight クラスターで、複数の Azure ストレージ アカウント (またはコンテナー) にアクセスすることもできます。 Azure File Storage は、エッジ ノード用としての利便性に優れたデータ ストレージ オプションです。このストレージを選択した場合、Azure Storage ファイル共有を Linux ファイル システムなどにマウントすることができます。 ただし Azure ファイル共有は、サポートされている オペレーティング システム (Windows や Linux など) を使用している任意のシステムでマウントし、使用することができます。

HDInsight で Apache Hadoop クラスターを作成するときに、**Azure Blob Storage** アカウントまたは **Data Lake Storage Gen1** のどちらかを指定します。 そのアカウントの特定のストレージ コンテナーが、作成したクラスターのファイル システム (Hadoop 分散ファイル システムなど) を保持します。 詳しい情報とガイダンスについては、次のドキュメントを参照してください。

- [HDInsight での Azure Blob Storage の使用](../hdinsight-hadoop-use-blob-storage.md)
- [Azure HDInsight クラスターで Data Lake Storage Gen1 を使用する](../hdinsight-hadoop-use-data-lake-storage-gen1.md)

## <a name="use-azure-blob-storage-accounts-with-ml-services-cluster"></a>ML Services クラスターで Azure BLOB ストレージ アカウントを使用する

次の手順は、ML Services クラスターを作成するときに複数のストレージ アカウントを指定した場合に、ML Services クラスターでのデータ アクセスと操作に対して、どのようにセカンダリ アカウントを使用するかを説明します。 ここでは、ストレージ アカウント **storage1** と、**container1** と呼ばれる既定のコンテナー、および **storage2** と **container2** を想定しています。

> [!WARNING]  
> パフォーマンス上の理由から、HDInsight クラスターは、指定したプライマリ ストレージ アカウントと同じデータ センターに作成されます。 HDInsight クラスター以外の場所でストレージ アカウントを使用することはできません。

### <a name="use-the-default-storage-with-ml-services-on-hdinsight"></a>HDInsight で ML Services を使って既定のストレージを使用する

1. SSH クライアントを使用して、使用しているクラスターのエッジ ノードに接続します。 SSH と HDInsight クラスターの使用については、[HDInsight での SSH の使用](../hdinsight-hadoop-linux-use-ssh-unix.md)に関するページをご覧ください。
  
2. サンプル ファイル mysamplefile.csv を /share ディレクトリにコピーします。

    ```bash
    hadoop fs –mkdir /share
    hadoop fs –copyFromLocal mycsv.scv /share
    ```

3. R Studio または別の R コンソールに切り替えて、R コードを記述して、名前ノードを **既定** に設定し、アクセスするファイルの場所を設定します。  

    ```R
    myNameNode <- "default"
    myPort <- 0

    #Location of the data:  
    bigDataDirRoot <- "/share"  

    #Define Spark compute context:
    mySparkCluster <- RxSpark(nameNode=myNameNode, consoleOutput=TRUE)

    #Set compute context:
    rxSetComputeContext(mySparkCluster)

    #Define the Hadoop Distributed File System (HDFS) file system:
    hdfsFS <- RxHdfsFileSystem(hostName=myNameNode, port=myPort)

    #Specify the input file to analyze in HDFS:
    inputFile <-file.path(bigDataDirRoot,"mysamplefile.csv")
    ```

ディレクトリとファイルの参照はすべて、ストレージ アカウント `wasbs://container1@storage1.blob.core.windows.net` を指しています。 これは、HDInsight クラスターに関連付けられる **既定のストレージ アカウント** です。

### <a name="use-the-additional-storage-with-ml-services-on-hdinsight"></a>HDInsight で ML Services を使って追加のストレージを使用する

ここで、**storage2** の **container2** の /private ディレクトリにある mysamplefile1.csv というファイルを処理するとします。

R コードで、名前ノード参照が **storage2** ストレージ アカウントを指すようにします。

```R
myNameNode <- "wasbs://container2@storage2.blob.core.windows.net"
myPort <- 0

#Location of the data:
bigDataDirRoot <- "/private"

#Define Spark compute context:
mySparkCluster <- RxSpark(consoleOutput=TRUE, nameNode=myNameNode, port=myPort)

#Set compute context:
rxSetComputeContext(mySparkCluster)

#Define HDFS file system:
hdfsFS <- RxHdfsFileSystem(hostName=myNameNode, port=myPort)

#Specify the input file to analyze in HDFS:
inputFile <-file.path(bigDataDirRoot,"mysamplefile1.csv")
```

ここでは、ディレクトリとファイルの参照はすべて、ストレージ アカウント `wasbs://container2@storage2.blob.core.windows.net` を指しています。 これは、先ほど指定した **名前ノード** です。

次のように、**storage2** で `/user/RevoShare/<SSH username>` ディレクトリを構成します。

```bash
hadoop fs -mkdir wasbs://container2@storage2.blob.core.windows.net/user
hadoop fs -mkdir wasbs://container2@storage2.blob.core.windows.net/user/RevoShare
hadoop fs -mkdir wasbs://container2@storage2.blob.core.windows.net/user/RevoShare/<RDP username>
```

## <a name="use-azure-data-lake-storage-gen1-with-ml-services-cluster"></a>ML Services クラスターで Azure Data Lake Storage Gen1 を使用する

ご自身の HDInsight クラスターで Data Lake Storage Gen1 を使用するには、使用する各 Azure Data Lake Storage Gen1 にご自身のクラスター アクセスを付与する必要があります。 既定のストレージ (または追加のストレージ) として Azure Data Lake Storage Gen1 を使用する HDInsight クラスターを Azure portal で作成する方法については、[Azure portal を使用して、Data Lake Storage Gen1 を使用する HDInsight クラスターを作成する方法](../../data-lake-store/data-lake-store-hdinsight-hadoop-use-portal.md)に関するページを参照してください。

その後、(前の手順で説明した) セカンダリ Azure ストレージ アカウントと同じように、このストレージを R スクリプトで使用します。

### <a name="add-cluster-access-to-your-azure-data-lake-storage-gen1"></a>Azure Data Lake Storage Gen1 へのクラスター アクセスの追加

HDInsight クラスターに関連付けられている Azure Active Directory (Azure AD) サービス プリンシパルを使用して、Data Lake Storage Gen1 にアクセスします。

1. HDInsight クラスターを作成するときに、 **[データ ソース]** タブの **[クラスター Azure AD ID]** を選択します。

2. **[クラスター Azure AD ID]** ダイアログ ボックスの **[AD サービス プリンシパルの選択]** で、 **[新規作成]** を選択します。

サービス プリンシパルに名前を付けてパスワードを設定したら、 **[ADLS アクセスを管理する]** をクリックして、サービス プリンシパルをご自身の Data Lake Storage と関連付けます。

また、クラスター作成後に 1 つ以上の Data Lake Storage Gen1 アカウントへのクラスター アクセスを追加することもできます。 そのためには、Data Lake Storage Gen1 の Azure portal エントリを開き、 **[データ エクスプローラー]、[アクセス]、[追加]** の順に移動します。

### <a name="how-to-access-data-lake-storage-gen1-from-ml-services-on-hdinsight"></a>HDInsight で ML Services から Data Lake Storage Gen1 にアクセスする方法

Data Lake Storage Gen1 へのアクセスを付与したら、Azure セカンダリ ストレージ アカウントと同様に、HDInsight の ML Services クラスターでストレージを使用できます。 唯一の違いは、プレフィックスが次のように **wasbs://** から **adl://** に変わることです。

```R
# Point to the ADL Storage (e.g. ADLtest)
myNameNode <- "adl://rkadl1.azuredatalakestore.net"
myPort <- 0

# Location of the data (assumes a /share directory on the ADL account)
bigDataDirRoot <- "/share"  

# Define Spark compute context
mySparkCluster <- RxSpark(consoleOutput=TRUE, nameNode=myNameNode, port=myPort)

# Set compute context
rxSetComputeContext(mySparkCluster)

# Define HDFS file system
hdfsFS <- RxHdfsFileSystem(hostName=myNameNode, port=myPort)

# Specify the input file in HDFS to analyze
inputFile <-file.path(bigDataDirRoot,"mysamplefile.csv")
```

次に示すコマンドは、RevoShare ディレクトリを含む Data Lake Storage Gen1 を構成し、前の例のサンプル .csv ファイルを追加します。

```bash
hadoop fs -mkdir adl://rkadl1.azuredatalakestore.net/user
hadoop fs -mkdir adl://rkadl1.azuredatalakestore.net/user/RevoShare
hadoop fs -mkdir adl://rkadl1.azuredatalakestore.net/user/RevoShare/<user>

hadoop fs -mkdir adl://rkadl1.azuredatalakestore.net/share

hadoop fs -copyFromLocal /usr/lib64/R Server-7.4.1/library/RevoScaleR/SampleData/mysamplefile.csv adl://rkadl1.azuredatalakestore.net/share

hadoop fs –ls adl://rkadl1.azuredatalakestore.net/share
```

## <a name="use-azure-file-storage-with-ml-services-on-hdinsight"></a>HDInsight で ML Services を使って Azure File Storage を使用する

[Azure Files](https://azure.microsoft.com/services/storage/files/) という、エッジ ノード上で使用できる便利なデータ ストレージを選択することも可能です。 Azure Files を使用すると、Azure Storage ファイル共有を Linux ファイル システムにマウントできます。 このストレージは特に、HDFS ではなくエッジ ノード上でネイティブ ファイル システムを使用する方がよい場合に、後で必要となる可能性があるデータ ファイル、R スクリプト、結果オブジェクトを格納するのに便利です。

Azure Files の大きな利点は、サポートされている OS (Windows や Linux など) を使用している任意のシステムで、ファイル共有をマウントして使用できることです。 たとえば、チーム内のメンバーが所有する他の HDInsight クラスターや、Azure VM、オンプレミスのシステムでも使用できます。 詳細については、次を参照してください。

- [Linux で Azure File Storage を使用する方法](../../storage/files/storage-how-to-use-files-linux.md)
- [Windows で Azure File Storage を使用する方法](../../storage/files/storage-dotnet-how-to-use-files.md)

## <a name="next-steps"></a>次のステップ

- [HDInsight 上の ML サービス クラスターの概要](r-server-overview.md)
- [HDInsight 上の ML サービス クラスター向けのコンピューティング コンテキスト オプション](r-server-compute-contexts.md)
- [Azure HDInsight クラスターで Azure Data Lake Storage Gen2 を使用する](../hdinsight-hadoop-use-data-lake-storage-gen2.md)
