---
title: DistCp を使用して Azure Data Lake Storage Gen2 にデータをコピーする | Microsoft Docs
description: DistCp ツールを使用して Data Lake Storage Gen2 との間でデータをコピーする
services: storage
author: seguler
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: conceptual
ms.date: 12/06/2018
ms.author: seguler
ms.openlocfilehash: 3b58dc8dabc55ba428ce6e35091a6947e5f4a824
ms.sourcegitcommit: c3d1aa5a1d922c172654b50a6a5c8b2a6c71aa91
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/17/2019
ms.locfileid: "59678388"
---
# <a name="use-distcp-to-copy-data-between-azure-storage-blobs-and-azure-data-lake-storage-gen2"></a>Distcp を使用して Azure Storage Blob と Azure Data Lake Storage Gen2 の間でデータをコピーする

[DistCp](https://hadoop.apache.org/docs/stable/hadoop-distcp/DistCp.html) を使用して、汎用 V2 ストレージ アカウントと階層型名前空間が有効になっている汎用 V2 ストレージ アカウントとの間でデータをコピーすることができます。 この記事では、DistCp ツールの使用方法について説明します。

DistCp にはさまざまなコマンド ライン パラメーターが用意されており、その使用を最適化するため、この記事をお読みになることを強くお勧めします。 この記事では、階層型名前空間が有効になっているアカウントにデータをコピーする場合の使用方法に重点を置きつつ、基本的な機能を紹介します。

## <a name="prerequisites"></a>前提条件

* **Azure サブスクリプション**。 [Azure 無料試用版の取得](https://azure.microsoft.com/pricing/free-trial/)に関するページを参照してください。
* **Data Lake Storage Gen2 の機能 (階層型名前空間) が有効になっていない既存の Azure Storage アカウント**。
* **Data Lake Storage Gen2 機能が使用可能な Azure Storage アカウント**。 作成手順については、「[Azure Data Lake Storage Gen2 ストレージ アカウントを作成する](data-lake-storage-quickstart-create-account.md)」を参照してください
* 階層型名前空間が有効になっているストレージ アカウントで作成された**ファイルシステム**。
* Data Lake Storage Gen2 が使用可能なストレージ アカウントにアクセスできる **Azure HDInsight クラスター**。 「[Use Azure Data Lake Storage Gen2 with Azure HDInsight clusters](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-use-data-lake-storage-gen2?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)」 (Azure HDInsight クラスターで Azure Data Lake Storage Gen2 を使用する) を参照してください。 クラスターのリモート デスクトップが有効になっていることを確認します。

## <a name="use-distcp-from-an-hdinsight-linux-cluster"></a>HDInsight Linux クラスターから DistCp を使用する

HDInsight クラスターには DistCp ユーティリティが付属しています。これを使用してさまざまなソースから HDInsight クラスターにデータをコピーできます。 Azure Blob Storage と Azure Data Lake Storage を一緒に使用するように HDInsight クラスターが構成されている場合は、DistCp ユーティリティを使用して、両者の間で簡単にデータをコピーすることもできます。 このセクションでは、DistCp ユーティリティを使用する方法について説明します。

1. HDI クラスターに SSH セッションを作成します。 「 [Linux ベースの HDInsight クラスターへの接続](../../hdinsight/hdinsight-hadoop-linux-use-ssh-unix.md)」を参照してください。

2. (階層型名前空間が有効になっていない) 既存の汎用 V2 アカウントにアクセスできるかどうかを確認します。

        hdfs dfs –ls wasbs://<CONTAINER_NAME>@<STORAGE_ACCOUNT_NAME>.blob.core.windows.net/

    出力には、コンテナーの内容の一覧が表示されます。

3. 同様に、クラスターから階層型名前空間が有効になったストレージ アカウントにアクセスできるかどうかを確認します。 次のコマンドを実行します。

        hdfs dfs -ls abfss://<FILE_SYSTEM_NAME>@<STORAGE_ACCOUNT_NAME>.dfs.core.windows.net/

    出力には、Data Lake Storage アカウントのファイル/フォルダーの一覧が表示されます。

4. DistCp を使用し、WASB から Data Lake Storage アカウントにデータをコピーします。

        hadoop distcp wasbs://<CONTAINER_NAME>@<STORAGE_ACCOUNT_NAME>.blob.core.windows.net/example/data/gutenberg abfss://<FILE_SYSTEM_NAME>@<STORAGE_ACCOUNT_NAME>.dfs.core.windows.net/myfolder

    このコマンドを実行すると、Blob Storage の **/example/data/gutenberg/** フォルダーの内容が Data Lake Storage アカウントの **/myfolder** にコピーされます。

5. 同様に、DistCp を使用し、Data Lake Storage アカウントから Blob Storage (WASB) にデータをコピーします。

        hadoop distcp abfss://<FILE_SYSTEM_NAME>@<STORAGE_ACCOUNT_NAME>.dfs.core.windows.net/myfolder wasbs://<CONTAINER_NAME>@<STORAGE_ACCOUNT_NAME>.blob.core.windows.net/example/data/gutenberg

    このコマンドを実行すると、Data Lake Store アカウントの **/myfolder** の内容が WASB の **/example/data/gutenberg/** フォルダーにコピーされます。

## <a name="performance-considerations-while-using-distcp"></a>DistCp を使用するときのパフォーマンスに関する考慮事項

DistCp の最小粒度は 1 ファイルであるため、DistCp を Data Lake Storage に対して最適化するうえで最も重要なのが、同時コピーの最大数を設定するパラメーターです。 同時コピー数は、コマンド ラインのマッパー (**m**) パラメーターの数と等しくなります。 このパラメーターで、データをコピーするときに使用されるマッパーの最大数を指定します。 既定値は 20 です。

**例**

    hadoop distcp wasbs://<CONTAINER_NAME>@<STORAGE_ACCOUNT_NAME>.blob.core.windows.net/example/data/gutenberg abfss://<FILE_SYSTEM_NAME>@<STORAGE_ACCOUNT_NAME>.dfs.core.windows.net/myfolder -m 100

### <a name="how-do-i-determine-the-number-of-mappers-to-use"></a>使用するマッパーの数を特定するにはどうすればよいですか。

使用できるガイダンスがいくつかあります。

* **手順 1: '既定' の YARN アプリ キューに使用できる合計メモリを決定する** - 最初の手順では、'既定' の YARN アプリ キューに使用可能なメモリを決定します。 この情報は、クラスターに関連付けられている Ambari ポータルで確認できます。 YARN に移動し、[Configs]\(構成\) タブで '既定' の YARN アプリ キューに使用可能な YARN メモリを確認します。 これは、DistCp ジョブ (実際には MapReduce ジョブ) に使用可能なメモリの合計です。

* **手順 2: マッパーの数を計算する** - **m** の値は、合計 YARN メモリを YARN コンテナーのサイズで割った値と等しくなります。 YARN コンテナー サイズの情報は、Ambari ポータルでも入手できます。 YARN に移動し、[Configs] \(構成) タブを表示します。YARN コンテナーのサイズは、このウィンドウに表示されます。 マッパーの数 (**m**) を求めるための式を次に示します

        m = (number of nodes * YARN memory for each node) / YARN container size

**例**

4x D14v2s クラスターがあり、10 個のフォルダーから 10 TB のデータを転送しようとしているとします。 各フォルダーに含まれるデータ量はさまざまです。また、各フォルダー内のファイル サイズも異なります。

* **合計 YARN メモリ**:Ambari ポータルから、D14 ノードの YARN メモリが 96 GB であることがわかります。 したがって、4 ノード クラスターの合計 YARN メモリは次のとおりです。 

        YARN memory = 4 * 96GB = 384GB

* **マッパーの数**:Ambari ポータルから、D14 ノードの YARN コンテナー サイズが 3,072 MB であることがわかります。 したがって、マッパーの数は次のとおりです。

        m = (4 nodes * 96GB) / 3072MB = 128 mappers

他のアプリケーションがメモリを使用している場合は、クラスターの YARN メモリで DistCp に対する部分のみを使用するように選択できます。

### <a name="copying-large-datasets"></a>大規模なデータセットのコピー

移動するデータセットのサイズが大きい場合 (たとえば 1 TB を超える場合)、または、さまざまなフォルダーが多数ある場合は、複数の DistCp ジョブを使用することを検討します。 パフォーマンスの向上はないと思われますが、ジョブが分散されるため、あるジョブが失敗したときに、再開する必要があるのは、ジョブ全体ではなく特定のジョブのみになります。

### <a name="limitations"></a>制限事項

* DistCp では、パフォーマンスを最適化するために、同じようなサイズのマッパーを作成しようとします。 マッパー数を増やしても、必ずしもパフォーマンスが向上するとは限りません。

* DistCp では、ファイルあたりのマッパーの数が 1 つに制限されています。 したがって、ファイル数よりも多くのマッパーを持つことはできません。 DistCp がファイルに割り当てることができるマッパーの数は 1 つだけのため、大きなファイルのコピーに使用できるコンカレンシー量は制限されます。

* 大きなファイルが少数だけ存在する場合は、潜在的なコンカレンシーを高めるために、そのファイルを 256 MB ファイル チャンクに分割します。
