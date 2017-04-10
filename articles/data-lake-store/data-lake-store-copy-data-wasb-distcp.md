---
title: "Distcp を使用して Data Lake Store と WASB の間でデータをコピーする | Microsoft Docs"
description: "Distcp ツールを使用して Azure Storage BLOB と Data Lake Store の間でデータをコピーする"
services: data-lake-store
documentationcenter: 
author: nitinme
manager: jhubbard
editor: cgronlun
ms.assetid: ae2e9506-69dd-4b95-8759-4dadca37ea70
ms.service: data-lake-store
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 03/06/2017
ms.author: nitinme
translationtype: Human Translation
ms.sourcegitcommit: 4f2230ea0cc5b3e258a1a26a39e99433b04ffe18
ms.openlocfilehash: 12aea210308636677ba2905887ddd24dc5c35238
ms.lasthandoff: 03/25/2017


---
# <a name="use-distcp-to-copy-data-between-azure-storage-blobs-and-data-lake-store"></a>Distcp を使用して Azure Storage BLOB と Data Lake Store の間でデータをコピーする
> [!div class="op_single_selector"]
> * [DistCp を使用](data-lake-store-copy-data-wasb-distcp.md)
> * [AdlCopy を使用](data-lake-store-copy-data-azure-storage-blob.md)
>
>

Data Lake Store アカウントにアクセスする HDInsight クラスターを作成した後、Distcp などの Hadoop エコシステム ツールを使用し、HDInsight クラスター ストレージ (WASB) と Data Lake Store アカウントの **間** でデータをコピーできます。 この記事では、これを実現する方法について説明します。

## <a name="prerequisites"></a>前提条件
この記事を読み始める前に、次の項目を用意する必要があります。

* **Azure サブスクリプション**。 [Azure 無料試用版の取得](https://azure.microsoft.com/pricing/free-trial/)に関するページを参照してください。
* **Azure Data Lake Store アカウント**。 このアカウントを作成する手順については、「 [Azure Data Lake Store の使用を開始する](data-lake-store-get-started-portal.md)
* Data Lake Store アカウントにアクセスできる **Azure HDInsight クラスター**。 [Data Lake Store を使用する HDInsight クラスターの作成](data-lake-store-hdinsight-hadoop-use-portal.md)に関するページを参照してください。 クラスターのリモート デスクトップが有効になっていることを確認します。

## <a name="do-you-learn-fast-with-videos"></a>ビデオで速習する
[こちらのビデオ](https://mix.office.com/watch/1liuojvdx6sie) をご覧ください。

## <a name="use-distcp-from-an-hdinsight-linux-cluster"></a>HDInsight Linux クラスターから Distcp を使用する

HDInsight クラスターには Distcp ユーティリティが付属しています。これを使用してさまざまなソースから HDInsight クラスターにデータをコピーできます。 追加の記憶域として Data Lake Store を使用する HDInsight クラスターを構成している場合、Distcp ユーティリティをすぐに使用し、Data Lake Store アカウント間でもデータをコピーできます。 このセクションでは、Distcp ユーティリティを使用する方法について説明します。

1. デスクトップから、SSH を使用してクラスターに接続します。 「 [Linux ベースの HDInsight クラスターへの接続](../hdinsight/hdinsight-hadoop-linux-use-ssh-unix.md)」を参照してください。 SSH プロンプトからコマンドを実行します。

2. Azure Storage BLOB (WASB) にアクセスできるかどうかを確認します。 次のコマンドを実行します。

        hdfs dfs –ls wasb://<container_name>@<storage_account_name>.blob.core.windows.net/

    これにより、ストレージ BLOB の内容の一覧が表示されます。
3. 同様に、クラスターから Data Lake Store アカウントにアクセスできるかどうかを確認します。 次のコマンドを実行します。

        hdfs dfs -ls adl://<data_lake_store_account>.azuredatalakestore.net:443/

    これにより、Data Lake Store アカウントのファイル/フォルダーの一覧が表示されます。
4. Distcp を使用し、WASB から Data Lake Store アカウントにデータをコピーします。

        hadoop distcp wasb://<container_name>@<storage_account_name>.blob.core.windows.net/example/data/gutenberg adl://<data_lake_store_account>.azuredatalakestore.net:443/myfolder

    これにより、WASB の **/example/data/gutenberg/** フォルダーの内容が Data Lake Store アカウントの **/myfolder** にコピーされます。
5. 同様に、Distcp を使用し、Data Lake Store アカウントと WASB の間でデータをコピーします。

        hadoop distcp adl://<data_lake_store_account>.azuredatalakestore.net:443/myfolder wasb://<container_name>@<storage_account_name>.blob.core.windows.net/example/data/gutenberg

    これにより、Data Lake Store アカウントの **/myfolder** の内容が WASB の **/example/data/gutenberg/** フォルダーにコピーされます。

## <a name="performance-considerations-while-using-distcp"></a>DistCp を使用するときのパフォーマンスに関する考慮事項

DistCp の最小粒度は 1 ファイルであるため、DistCp を Data Lake Store に対して最適化するうえで最も重要なのが、同時コピーの最大数を設定するパラメーターです。 これを制御するには、コマンド ラインでマッパー ("m") パラメーターの数を設定します。 このパラメーターで、データをコピーするときに使用されるマッパーの最大数を指定します。 既定値は 20 です。

**例**

    hadoop distcp wasb://<container_name>@<storage_account_name>.blob.core.windows.net/example/data/gutenberg adl://<data_lake_store_account>.azuredatalakestore.net:443/myfolder -m 100

### <a name="how-do-i-determine-the-number-of-mappers-to-use"></a>使用するマッパーの数を特定するにはどうすればよいですか。

使用できるガイダンスがいくつかあります。

* **手順 1: 合計 YARN メモリを確認する** - 最初に DistCp ジョブを実行するクラスターで利用できる YARN メモリを確認します。 この情報は、クラスターに関連付けられている Ambari ポータルで確認できます。 YARN に移動し、[Configs (構成)] タブで YARN メモリを表示します。 合計 YARN メモリを取得するには、ノードあたりの YARN メモリと、クラスター内にあるのノードの数を掛けます。

* **手順 2: マッパーの数を計算する** - **m** の値は、合計 YARN メモリを YARN コンテナーのサイズで割った値と等しくなります。 YARN コンテナー サイズの情報は、Ambari ポータルでも入手できます。 YARN に移動し、[Configs (構成)] タブを表示します。 YARN コンテナーのサイズは、このウィンドウに表示されます。 マッパーの数 (**m**) を求めるための式を次に示します

        m = (number of nodes * YARN memory for each node) / YARN container size

**例**

クラスター内に 4 D14v2s ノードがあり、10 個のフォルダーから 10 TB のデータを転送しようしているとします。 各フォルダーに含まれるデータ量はさまざまです。また、各フォルダー内のファイル サイズも異なります。

* 合計 YARN メモリ - Ambari ポータルから、D14 ノードの YARN メモリが 96 GB であることがわかります。 したがって、4 ノード クラスターの合計 YARN メモリは次のとおりです。 

        YARN memory = 4 * 96GB = 384GB

* マッパーの数 - Ambari ポータルから、D14 ノードの YARN コンテナー サイズが 3072 であることがわかります。 したがって、マッパーの数は次のとおりです。

        m = (4 nodes * 96GB) / 3072MB = 128 mappers

他のアプリケーションがメモリを使用している場合は、クラスターの YARN メモリで DistCp に対する部分のみを使用するように選択できます。

### <a name="copying-large-datasets"></a>大規模なデータセットのコピー

移動するデータセットのサイズが非常に大きい場合 (例: > 1 TB)、または、さまざまなフォルダーが多数ある場合は、複数の DistCp ジョブを使用することを検討します。 パフォーマンスの向上はないと思われますが、ジョブが分散されるため、あるジョブが失敗したときに、再開する必要があるのは、ジョブ全体ではなく特定のジョブのみになります。

### <a name="limitations"></a>制限事項

* DistCp では、パフォーマンスを最適化するために、同じようなサイズのマッパーを作成しようとします。 マッパー数を増やしても、必ずしもパフォーマンスが向上するとは限りません。

* DistCp では、ファイルあたりのマッパーの数が 1 つに制限されています。 したがって、ファイル数よりも多くのマッパーを持つことはできません。 DistCp がファイルに割り当てることができるマッパーの数は 1 つだけのため、大きなファイルのコピーに使用できる同時実行量は制限されます。

* 大きなファイルが少数だけ存在する場合は、潜在的な同時実行性を高めるために、そのファイルを 256 MB ファイル チャンクに分割します。 
 
* Azure Blob Storage アカウントからコピーする場合、コピー ジョブは、Blob Storage 側で調整されることがあります。 これにより、コピー ジョブのパフォーマンスが低下します。 Azure Blob Storage の制限の詳細については、[Azure サブスクリプションとサービスの制限事項](../azure-subscription-service-limits.md)に関するページをご覧ください。

## <a name="see-also"></a>関連項目
* [Azure Storage BLOB から Data Lake Store へのデータのコピー](data-lake-store-copy-data-azure-storage-blob.md)
* [Data Lake Store のデータをセキュリティで保護する](data-lake-store-secure-data.md)
* [Data Lake Store で Azure Data Lake Analytics を使用する](../data-lake-analytics/data-lake-analytics-get-started-portal.md)
* [Data Lake Store で Azure HDInsight を使用する](data-lake-store-hdinsight-hadoop-use-portal.md)

