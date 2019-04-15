---
title: Distcp を使用して Azure Data Lake Storage Gen1 と WASB の間でデータをコピーする | Microsoft Docs
description: Distcp ツールを使用して Azure Storage BLOB と Azure Data Lake Storage Gen1 の間でデータをコピーする
services: data-lake-store
documentationcenter: ''
author: twooley
manager: mtillman
editor: cgronlun
ms.assetid: ae2e9506-69dd-4b95-8759-4dadca37ea70
ms.service: data-lake-store
ms.devlang: na
ms.topic: conceptual
ms.date: 05/29/2018
ms.author: twooley
ms.openlocfilehash: fbefe233ce0d2477982faf0a9f38a73062e0c7a1
ms.sourcegitcommit: a60a55278f645f5d6cda95bcf9895441ade04629
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/03/2019
ms.locfileid: "58884467"
---
# <a name="use-distcp-to-copy-data-between-azure-storage-blobs-and-azure-data-lake-storage-gen1"></a>Distcp を使用して Azure Storage BLOB と Azure Data Lake Storage Gen1 の間でデータをコピーする
> [!div class="op_single_selector"]
> * [DistCp を使用](data-lake-store-copy-data-wasb-distcp.md)
> * [AdlCopy を使用](data-lake-store-copy-data-azure-storage-blob.md)
>
>

Azure Data Lake Storage Gen1 にアクセスできる HDInsight クラスターがある場合、Distcp などの Hadoop エコシステム ツールを使用し、HDInsight クラスター記憶域 (WASB) と Data Lake Storage Gen1 アカウント**との間**でデータをコピーできます。 この記事では、Distcp ツールの使用方法について説明します。

## <a name="prerequisites"></a>前提条件

* **Azure サブスクリプション**。 [Azure 無料試用版の取得](https://azure.microsoft.com/pricing/free-trial/)に関するページを参照してください。
* **Azure Data Lake Storage Gen1 アカウント**。 これを作成する手順については、[Azure Data Lake Storage Gen1 の使用開始](data-lake-store-get-started-portal.md)に関するページを参照してください。
* Data Lake Storage Gen1 アカウントにアクセスできる **Azure HDInsight クラスター**。 [Data Lake Storage Gen1 を使用する HDInsight クラスターの作成](data-lake-store-hdinsight-hadoop-use-portal.md)に関するページを参照してください。 クラスターのリモート デスクトップが有効になっていることを確認します。

## <a name="use-distcp-from-an-hdinsight-linux-cluster"></a>HDInsight Linux クラスターから Distcp を使用する

HDInsight クラスターには Distcp ユーティリティが付属しています。これを使用してさまざまなソースから HDInsight クラスターにデータをコピーできます。 追加の記憶域として Data Lake Storage Gen1 を使用する HDInsight クラスターを構成している場合、Distcp ユーティリティをすぐに使用し、Data Lake Storage Gen1 アカウント間でもデータをコピーできます。 このセクションでは、Distcp ユーティリティを使用する方法について説明します。

1. デスクトップから、SSH を使用してクラスターに接続します。 「 [Linux ベースの HDInsight クラスターへの接続](../hdinsight/hdinsight-hadoop-linux-use-ssh-unix.md)」を参照してください。 SSH プロンプトからコマンドを実行します。

2. Azure Storage BLOB (WASB) にアクセスできるかどうかを確認します。 次のコマンドを実行します。

        hdfs dfs –ls wasb://<container_name>@<storage_account_name>.blob.core.windows.net/

    出力には、ストレージ BLOB の内容の一覧が表示されます。

3. 同様に、クラスターから Data Lake Storage Gen1 アカウントにアクセスできるかどうかを確認します。 次のコマンドを実行します。

        hdfs dfs -ls adl://<data_lake_storage_gen1_account>.azuredatalakestore.net:443/

    出力には、Data Lake Storage Gen1 アカウントのファイル/フォルダーの一覧が表示されます。

4. Distcp を使用し、WASB から Data Lake Storage Gen1 アカウントにデータをコピーします。

        hadoop distcp wasb://<container_name>@<storage_account_name>.blob.core.windows.net/example/data/gutenberg adl://<data_lake_storage_gen1_account>.azuredatalakestore.net:443/myfolder

    このコマンドを実行すると、WASB の **/example/data/gutenberg/** フォルダーの内容が Data Lake Storage Gen1 アカウントの **/myfolder** にコピーされます。

5. 同様に、Distcp を使用し、Data Lake Storage Gen1 アカウントと WASB の間でデータをコピーします。

        hadoop distcp adl://<data_lake_storage_gen1_account>.azuredatalakestore.net:443/myfolder wasb://<container_name>@<storage_account_name>.blob.core.windows.net/example/data/gutenberg

    このコマンドを実行すると、Data Lake Storage Gen1 アカウントの **/myfolder** の内容が WASB の **/example/data/gutenberg/** フォルダーにコピーされます。

## <a name="performance-considerations-while-using-distcp"></a>DistCp を使用するときのパフォーマンスに関する考慮事項

DistCp の最小粒度は 1 ファイルであるため、DistCp を Data Lake Storage Gen1 に対して最適化するうえで最も重要なのが、同時コピーの最大数を設定するパラメーターです。 同時コピー数を制御するには、コマンド ラインでマッパー ("m") パラメーターの数を設定します。 このパラメーターで、データをコピーするときに使用されるマッパーの最大数を指定します。 既定値は 20 です。

**例**

    hadoop distcp wasb://<container_name>@<storage_account_name>.blob.core.windows.net/example/data/gutenberg adl://<data_lake_storage_gen1_account>.azuredatalakestore.net:443/myfolder -m 100

### <a name="how-do-i-determine-the-number-of-mappers-to-use"></a>使用するマッパーの数を特定するにはどうすればよいですか。

使用できるガイダンスがいくつかあります。

* **手順 1:合計 YARN メモリを確認する** - 最初に DistCp ジョブを実行するクラスターで利用できる YARN メモリを確認します。 この情報は、クラスターに関連付けられている Ambari ポータルで確認できます。 YARN に移動し、[Configs (構成)] タブで YARN メモリを表示します。 合計 YARN メモリを取得するには、ノードあたりの YARN メモリと、クラスター内にあるのノードの数を掛けます。

* **手順 2:マッパーの数を計算する** - **m** の値は、合計 YARN メモリを YARN コンテナーのサイズで割った値と等しくなります。 YARN コンテナー サイズの情報は、Ambari ポータルでも入手できます。 YARN に移動し、[Configs] \(構成) タブを表示します。YARN コンテナーのサイズは、このウィンドウに表示されます。 マッパーの数 (**m**) を求めるための式を次に示します

        m = (number of nodes * YARN memory for each node) / YARN container size

**例**

クラスター内に 4 D14v2s ノードがあり、10 個のフォルダーから 10 TB のデータを転送しようしているとします。 各フォルダーに含まれるデータ量はさまざまです。また、各フォルダー内のファイル サイズも異なります。

* 合計 YARN メモリ - Ambari ポータルから、D14 ノードの YARN メモリが 96 GB であることがわかります。 したがって、4 ノード クラスターの合計 YARN メモリは次のとおりです。 

        YARN memory = 4 * 96GB = 384GB

* マッパーの数 - Ambari ポータルから、D14 ノードの YARN コンテナー サイズが 3072 であることがわかります。 したがって、マッパーの数は次のとおりです。

        m = (4 nodes * 96GB) / 3072MB = 128 mappers

他のアプリケーションがメモリを使用している場合は、クラスターの YARN メモリで DistCp に対する部分のみを使用するように選択できます。

### <a name="copying-large-datasets"></a>大規模なデータセットのコピー

移動するデータセットのサイズが大きい場合 (たとえば 1 TB を超える場合)、または、さまざまなフォルダーが多数ある場合は、複数の DistCp ジョブを使用することを検討します。 パフォーマンスの向上はないと思われますが、ジョブが分散されるため、あるジョブが失敗したときに、再開する必要があるのは、ジョブ全体ではなく特定のジョブのみになります。

### <a name="limitations"></a>制限事項

* DistCp では、パフォーマンスを最適化するために、同じようなサイズのマッパーを作成しようとします。 マッパー数を増やしても、必ずしもパフォーマンスが向上するとは限りません。

* DistCp では、ファイルあたりのマッパーの数が 1 つに制限されています。 したがって、ファイル数よりも多くのマッパーを持つことはできません。 DistCp がファイルに割り当てることができるマッパーの数は 1 つだけのため、大きなファイルのコピーに使用できるコンカレンシー量は制限されます。

* 大きなファイルが少数だけ存在する場合は、潜在的なコンカレンシーを高めるために、そのファイルを 256 MB ファイル チャンクに分割します。 
 
* Azure Blob Storage アカウントからコピーする場合、コピー ジョブは、Blob Storage 側で調整されることがあります。 これにより、コピー ジョブのパフォーマンスが低下します。 Azure Blob Storage の制限の詳細については、[Azure サブスクリプションとサービスの制限事項](../azure-subscription-service-limits.md)に関するページをご覧ください。

## <a name="see-also"></a>関連項目
* [Azure Storage BLOB から Azure Data Lake Storage Gen1 にデータをコピーする](data-lake-store-copy-data-azure-storage-blob.md)
* [Data Lake Storage Gen1 のデータをセキュリティ保護する](data-lake-store-secure-data.md)
* [Data Lake Storage Gen1 で Azure Data Lake Analytics を使用する](../data-lake-analytics/data-lake-analytics-get-started-portal.md)
* [Data Lake Storage Gen1 で Azure HDInsight を使用する](data-lake-store-hdinsight-hadoop-use-portal.md)
