---
title: DistCp を使用して Azure Data Lake Storage Gen2 にデータをコピーする | Microsoft Docs
description: Apache Hadoop Distributed Copy (DistCp) ツールを使用し、Azure Data Lake Storage Gen2 との間でデータをコピーします。
author: normesta
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: how-to
ms.date: 12/06/2018
ms.author: normesta
ms.reviewer: stewu
ms.openlocfilehash: e69a97a86a357fb36dde572f292b5cac7963d14a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "95912486"
---
# <a name="use-distcp-to-copy-data-between-azure-storage-blobs-and-azure-data-lake-storage-gen2"></a>Distcp を使用して Azure Storage Blob と Azure Data Lake Storage Gen2 の間でデータをコピーする

[DistCp](https://hadoop.apache.org/docs/stable/hadoop-distcp/DistCp.html) を使用して、汎用 V2 ストレージ アカウントと階層型名前空間が有効になっている汎用 V2 ストレージ アカウントとの間でデータをコピーすることができます。 この記事では、DistCp ツールの使用方法について説明します。

DistCp にはさまざまなコマンド ライン パラメーターが用意されており、その使用を最適化するため、この記事をお読みになることを強くお勧めします。 この記事では、階層型名前空間が有効になっているアカウントにデータをコピーする場合の使用方法に重点を置きつつ、基本的な機能を紹介します。

## <a name="prerequisites"></a>前提条件

* Azure サブスクリプション。 [Azure 無料試用版の取得](https://azure.microsoft.com/pricing/free-trial/)に関するページを参照してください。
* Data Lake Storage Gen2 の機能 (階層型名前空間) が有効になっていない既存の Azure Storage アカウント。
* Data Lake Storage Gen2 の機能 (階層型名前空間) が有効になっている Azure Storage アカウント。 Azure Storage アカウントの作成方法については、「[Azure Storage アカウントの作成](../common/storage-account-create.md)」を参照してください
* 階層型名前空間が有効になっているストレージ アカウントで作成されたコンテナー。
* 階層型名前空間機能が有効になっているストレージ アカウントにアクセスできる Azure HDInsight クラスター。 「[Use Azure Data Lake Storage Gen2 with Azure HDInsight clusters](../../hdinsight/hdinsight-hadoop-use-data-lake-storage-gen2.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)」 (Azure HDInsight クラスターで Azure Data Lake Storage Gen2 を使用する) を参照してください。 クラスターのリモート デスクトップが有効になっていることを確認します。

## <a name="use-distcp-from-an-hdinsight-linux-cluster"></a>HDInsight Linux クラスターから DistCp を使用する

HDInsight クラスターには DistCp ユーティリティが付属しています。これを使用してさまざまなソースから HDInsight クラスターにデータをコピーできます。 Azure Blob Storage と Azure Data Lake Storage を一緒に使用するように HDInsight クラスターが構成されている場合は、DistCp ユーティリティを使用して、両者の間で簡単にデータをコピーすることもできます。 このセクションでは、DistCp ユーティリティを使用する方法について説明します。

1. HDI クラスターに SSH セッションを作成します。 「 [Linux ベースの HDInsight クラスターへの接続](../../hdinsight/hdinsight-hadoop-linux-use-ssh-unix.md)」を参照してください。

2. (階層型名前空間が有効になっていない) 既存の汎用 V2 アカウントにアクセスできるかどうかを確認します。

    ```bash
    hdfs dfs –ls wasbs://<container-name>@<storage-account-name>.blob.core.windows.net/
    ```

   出力には、コンテナーの内容の一覧が表示されます。

3. 同様に、クラスターから階層型名前空間が有効になったストレージ アカウントにアクセスできるかどうかを確認します。 次のコマンドを実行します。

    ```bash
    hdfs dfs -ls abfss://<container-name>@<storage-account-name>.dfs.core.windows.net/
    ```

    出力には、Data Lake Storage アカウントのファイル/フォルダーの一覧が表示されます。

4. DistCp を使用し、WASB から Data Lake Storage アカウントにデータをコピーします。

    ```bash
    hadoop distcp wasbs://<container-name>@<storage-account-name>.blob.core.windows.net/example/data/gutenberg abfss://<container-name>@<storage-account-name>.dfs.core.windows.net/myfolder
    ```

    このコマンドを実行すると、Blob Storage の **/example/data/gutenberg/** フォルダーの内容が Data Lake Storage アカウントの **/myfolder** にコピーされます。

5. 同様に、DistCp を使用し、Data Lake Storage アカウントから Blob Storage (WASB) にデータをコピーします。

    ```bash
    hadoop distcp abfss://<container-name>@<storage-account-name>.dfs.core.windows.net/myfolder wasbs://<container-name>@<storage-account-name>.blob.core.windows.net/example/data/gutenberg
    ```

    このコマンドを実行すると、Data Lake Store アカウントの **/myfolder** の内容が WASB の **/example/data/gutenberg/** フォルダーにコピーされます。

## <a name="performance-considerations-while-using-distcp"></a>DistCp を使用するときのパフォーマンスに関する考慮事項

DistCp の最小粒度は 1 ファイルであるため、DistCp を Data Lake Storage に対して最適化するうえで最も重要なのが、同時コピーの最大数を設定するパラメーターです。 同時コピー数は、コマンド ラインのマッパー (**m**) パラメーターの数と等しくなります。 このパラメーターで、データをコピーするときに使用されるマッパーの最大数を指定します。 既定値は 20 です。

**例**

```bash
hadoop distcp -m 100 wasbs://<container-name>@<storage-account-name>.blob.core.windows.net/example/data/gutenberg abfss://<container-name>@<storage-account-name>.dfs.core.windows.net/myfolder
```

### <a name="how-do-i-determine-the-number-of-mappers-to-use"></a>使用するマッパーの数を特定するにはどうすればよいですか。

使用できるガイダンスがいくつかあります。

* **ステップ 1:'既定' の YARN アプリ キューに使用できる合計メモリを決定する** - 最初の手順では、'既定' の YARN アプリ キューに使用可能なメモリを決定します。 この情報は、クラスターに関連付けられている Ambari ポータルで確認できます。 YARN に移動し、[Configs]\(構成\) タブで '既定' の YARN アプリ キューに使用可能な YARN メモリを確認します。 これは、DistCp ジョブ (実際には MapReduce ジョブ) に使用可能なメモリの合計です。

* **手順 2:マッパーの数を計算する** - **m** の値は、合計 YARN メモリを YARN コンテナーのサイズで割った値と等しくなります。 YARN コンテナー サイズの情報は、Ambari ポータルでも入手できます。 YARN に移動し、[Configs] \(構成) タブを表示します。YARN コンテナーのサイズは、このウィンドウに表示されます。 マッパーの数 (**m**) を求めるための式を次に示します

    m = (ノードの数 * YARN の各ノードのメモリ) / YARN コンテナーのサイズ

**例**

4x D14v2s クラスターがあり、10 個のフォルダーから 10 TB のデータを転送しようとしているとします。 各フォルダーに含まれるデータ量はさまざまです。また、各フォルダー内のファイル サイズも異なります。

* **合計 YARN メモリ**:Ambari ポータルから、D14 ノードの YARN メモリが 96 GB であることがわかります。 したがって、4 ノード クラスターの合計 YARN メモリは次のとおりです。 

    YARN メモリ = 4 * 96 GB = 384 GB

* **マッパーの数**:Ambari ポータルから、D14 ノードの YARN コンテナー サイズが 3,072 MB であることがわかります。 したがって、マッパーの数は次のとおりです。

    m = (4 ノード * 96 GB) / 3072 MB = 128 マッパー

他のアプリケーションがメモリを使用している場合は、クラスターの YARN メモリで DistCp に対する部分のみを使用するように選択できます。

### <a name="copying-large-datasets"></a>大規模なデータセットのコピー

移動するデータセットのサイズが大きい場合 (たとえば 1 TB を超える場合)、または、さまざまなフォルダーが多数ある場合は、複数の DistCp ジョブを使用することを検討します。 パフォーマンスの向上はないと思われますが、ジョブが分散されるため、あるジョブが失敗したときに、再開する必要があるのは、ジョブ全体ではなく特定のジョブのみになります。

### <a name="limitations"></a>制限事項

* DistCp では、パフォーマンスを最適化するために、同じようなサイズのマッパーを作成しようとします。 マッパー数を増やしても、必ずしもパフォーマンスが向上するとは限りません。

* DistCp では、ファイルあたりのマッパーの数が 1 つに制限されています。 したがって、ファイル数よりも多くのマッパーを持つことはできません。 DistCp がファイルに割り当てることができるマッパーの数は 1 つだけのため、大きなファイルのコピーに使用できるコンカレンシー量は制限されます。

* 大きなファイルが少数だけ存在する場合は、潜在的なコンカレンシーを高めるために、そのファイルを 256 MB ファイル チャンクに分割します。