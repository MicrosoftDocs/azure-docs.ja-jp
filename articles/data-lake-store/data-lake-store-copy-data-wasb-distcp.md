---
title: DistCp を使用して Azure Data Lake Storage Gen1 と WASB の間でデータをコピーする
description: DistCp ツールを使用して Azure Storage Blob と Azure Data Lake Storage Gen1 の間でデータをコピーする
author: twooley
ms.service: data-lake-store
ms.topic: how-to
ms.date: 01/03/2020
ms.author: twooley
ms.openlocfilehash: c608f357eb1eff9fd36e583b98d26250a71cb923
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "85515679"
---
# <a name="use-distcp-to-copy-data-between-azure-storage-blobs-and-azure-data-lake-storage-gen1"></a>DistCp を使用して Azure Storage Blob と Azure Data Lake Storage Gen1 の間でデータをコピーする

> [!div class="op_single_selector"]
> * [DistCp を使用](data-lake-store-copy-data-wasb-distcp.md)
> * [AdlCopy を使用](data-lake-store-copy-data-azure-storage-blob.md)
>
>

Azure Data Lake Storage Gen1 にアクセスできる HDInsight クラスターがある場合は、DistCp などの Hadoop エコシステム ツールを使用して、HDInsight クラスター記憶域 (WASB) と Data Lake Storage Gen1 アカウントとの間でデータをコピーできます。 この記事では、DistCp ツールを使用する方法について説明します。

## <a name="prerequisites"></a>前提条件

* **Azure サブスクリプション**。 [Azure 無料試用版の取得](https://azure.microsoft.com/pricing/free-trial/)に関するページを参照してください。
* **Azure Data Lake Storage Gen1 アカウント**。 これを作成する手順については、[Azure Data Lake Storage Gen1 の使用開始](data-lake-store-get-started-portal.md)に関する記事をご覧ください。
* Data Lake Storage Gen1 アカウントにアクセスできる **Azure HDInsight クラスター**。 [Data Lake Storage Gen1 を使用する HDInsight クラスターの作成](data-lake-store-hdinsight-hadoop-use-portal.md)に関するページを参照してください。 クラスターのリモート デスクトップが有効になっていることを確認します。

## <a name="use-distcp-from-an-hdinsight-linux-cluster"></a>HDInsight Linux クラスターから DistCp を使用する

HDInsight クラスターには DistCp ツールが付属しています。これを使用すると、さまざまなソースから HDInsight クラスターにデータをコピーできます。 追加の記憶域として Data Lake Storage Gen1 を使用する HDInsight クラスターを構成している場合は、DistCp をそのまま使用すれば、Data Lake Storage Gen1 アカウントとの間でデータをコピーできます。 このセクションでは、DistCp ツールを使用する方法について説明します。

1. デスクトップから、SSH を使用してクラスターに接続します。 「 [Linux ベースの HDInsight クラスターへの接続](../hdinsight/hdinsight-hadoop-linux-use-ssh-unix.md)」を参照してください。 SSH プロンプトからコマンドを実行します。

1. Azure Storage Blob (WASB) にアクセスできるかどうかを確認します。 次のコマンドを実行します。

   ```
   hdfs dfs –ls wasb://<container_name>@<storage_account_name>.blob.core.windows.net/
   ```

   出力すると、Storage Blob の内容の一覧が表示されます。

1. 同様に、クラスターから Data Lake Storage Gen1 アカウントにアクセスできるかどうかを確認します。 次のコマンドを実行します。

   ```
   hdfs dfs -ls adl://<data_lake_storage_gen1_account>.azuredatalakestore.net:443/
   ```

    出力すると、Data Lake Storage Gen1 アカウントのファイルやフォルダーの一覧が表示されます。

1. DistCp を使用して、WASB から Data Lake Storage Gen1 アカウントにデータをコピーします。

   ```
   hadoop distcp wasb://<container_name>@<storage_account_name>.blob.core.windows.net/example/data/gutenberg adl://<data_lake_storage_gen1_account>.azuredatalakestore.net:443/myfolder
   ```

    このコマンドを実行すると、WASB の **/example/data/gutenberg/** フォルダーの内容が Data Lake Storage Gen1 アカウントの **/myfolder** にコピーされます。

1. 同様に、DistCp を使用して、Data Lake Storage Gen1 アカウントから WASB にデータをコピーします。

   ```
   hadoop distcp adl://<data_lake_storage_gen1_account>.azuredatalakestore.net:443/myfolder wasb://<container_name>@<storage_account_name>.blob.core.windows.net/example/data/gutenberg
   ```

    このコマンドを実行すると、Data Lake Storage Gen1 アカウントの **/myfolder** の内容が WASB の **/example/data/gutenberg/** フォルダーにコピーされます。

## <a name="performance-considerations-while-using-distcp"></a>DistCp を使用するときのパフォーマンスに関する考慮事項

DistCp ツールの最小粒度は 1 ファイルであるため、同時コピーできる最大数を設定することが、Data Lake Storage Gen1 に対して最適化を行う際の最も重要なパラメーターとなります。 コマンド ラインでマッパー (‘m’) パラメーターの数を設定すると、同時コピー数を制御できます。 このパラメーターで、データをコピーするときに使用されるマッパーの最大数を指定します。 既定値は 20 です。

例:

```
 hadoop distcp wasb://<container_name>@<storage_account_name>.blob.core.windows.net/example/data/gutenberg adl://<data_lake_storage_gen1_account>.azuredatalakestore.net:443/myfolder -m 100
```

### <a name="how-to-determine-the-number-of-mappers-to-use"></a>使用するマッパーの数を決定する方法

使用できるガイダンスがいくつかあります。

* **手順 1: 合計 YARN メモリを確認する** - 最初に DistCp ジョブを実行するクラスターで利用できる YARN メモリを確認します。 この情報は、クラスターに関連付けられている Ambari ポータルで確認できます。 YARN に移動して、**[構成]** タブで YARN メモリを表示します。 合計 YARN メモリを取得するには、ノードあたりの YARN メモリと、クラスター内にあるのノードの数を掛けます。

* **手順 2:マッパーの数を計算する** - **m** の値は、合計 YARN メモリを YARN コンテナーのサイズで割った値と等しくなります。 YARN コンテナーのサイズ情報は、Ambari ポータルでも入手できます。 YARN に移動し、 **[Configs]\(構成\)** タブを表示します。YARN コンテナーのサイズは、このウィンドウに表示されます。 マッパーの数 (**m**) を求めるための式を次に示します。

   `m = (number of nodes * YARN memory for each node) / YARN container size`

例:

クラスター内に 4 つの D14v2s ノードがあり、10 個のフォルダーから 10 TB のデータを転送しようしているとします。 各フォルダーに含まれるデータ量はさまざまです。また、各フォルダー内のファイル サイズも異なります。

* 合計 YARN メモリ - Ambari ポータルから、D14 ノードの YARN メモリが 96 GB であることがわかります。 したがって、4 ノード クラスターの合計 YARN メモリは次のとおりです。

   `YARN memory = 4 * 96GB = 384GB`

* マッパーの数 - Ambari ポータルから、D14 ノードの YARN コンテナー サイズが 3072 であることがわかります。 したがって、マッパーの数は次のとおりです。

   `m = (4 nodes * 96GB) / 3072MB = 128 mappers`

他のアプリケーションがメモリを使用している場合は、クラスターの YARN メモリで DistCp 用の部分のみを使用するように選択できます。

### <a name="copying-large-datasets"></a>大規模なデータセットのコピー

移動するデータセットのサイズが大きい場合 (1 TB を超える場合など)、またはさまざまなフォルダーが多数ある場合は、複数の DistCp ジョブの使用を検討してください。 パフォーマンスの向上は見込めませんが、ジョブが分散されるため、いずれかのジョブが失敗しても、再開する必要があるのは、ジョブ全体ではなく、特定のジョブのみとなります。

### <a name="limitations"></a>制限事項

* DistCp では、パフォーマンスを最適化するために、同じようなサイズのマッパーを作成しようとします。 マッパー数を増やしても、必ずしもパフォーマンスが向上するとは限りません。

* DistCp では、ファイルあたりのマッパーの数が 1 つに制限されています。 したがって、ファイル数よりも多くのマッパーを持つことはできません。 DistCp によってファイルに割り当てられるマッパーの数は 1 つのみであるため、大きなファイルのコピーに使用できるコンカレンシー量は制限されます。

* 大きなファイルの数が少ない場合は、256 MB のファイル チャンクに分割して、潜在的なコンカレンシーを高めます。

* Azure Blob Storage アカウントからコピーする場合は、Blob Storage 側でコピー ジョブが調整されることがあります。 これにより、コピー ジョブのパフォーマンスが低下します。 Azure Blob Storage の制限の詳細については、[Azure サブスクリプションとサービスの制限](../azure-resource-manager/management/azure-subscription-service-limits.md)に関する記事で Azure Storage の制限について参照してください。

## <a name="see-also"></a>関連項目

* [Azure Storage Blob から Azure Data Lake Storage Gen1 にデータをコピーする](data-lake-store-copy-data-azure-storage-blob.md)
* [Data Lake Storage Gen1 でのデータのセキュリティ保護](data-lake-store-secure-data.md)
* [Data Lake Storage Gen1 で Azure Data Lake Analytics を使用する](../data-lake-analytics/data-lake-analytics-get-started-portal.md)
* [Data Lake Storage Gen1 で Azure HDInsight を使用する](data-lake-store-hdinsight-hadoop-use-portal.md)
