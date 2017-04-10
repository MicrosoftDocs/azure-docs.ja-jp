---
title: "Azure Data Lake Store のパフォーマンス チューニング ガイドライン | Microsoft Docs"
description: "Azure Data Lake Store のパフォーマンス チューニング ガイドライン"
services: data-lake-store
documentationcenter: 
author: stewu
manager: amitkul
editor: cgronlun
ms.assetid: ebde7b9f-2e51-4d43-b7ab-566417221335
ms.service: data-lake-store
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 03/06/2017
ms.author: stewu
translationtype: Human Translation
ms.sourcegitcommit: af11866fc812cd8a375557b7bf9df5cdc9bba610
ms.openlocfilehash: f0d0c05c08ce198e2702c76ad35b348107c664c7
ms.lasthandoff: 01/18/2017


---
# <a name="performance-tuning-guidance-for-azure-data-lake-store"></a>Azure Data Lake Store のパフォーマンス チューニング ガイダンス

この記事では、Azure Data Lake Store のデータの書き込みと読み取り時のパフォーマンスを最適化する方法について説明します。 この記事は、よく使用されるデータ アップロード/ダウンロード ツールとデータ分析ワークロードのために構成できるパラメーターを把握するのに役立ちます。 このガイドのチューニングは、特に、Data Lake Store に対して大量のデータを読み書きする、リソースを集中的に使用するワークロードを対象としています。

## <a name="prerequisites"></a>前提条件

* **Azure サブスクリプション**。 [Azure 無料試用版の取得](https://azure.microsoft.com/pricing/free-trial/)に関するページを参照してください。
* **Azure Data Lake Store アカウント**。 このアカウントを作成する手順については、「 [Azure Data Lake Store の使用を開始する](data-lake-store-get-started-portal.md)
* Data Lake Store アカウントにアクセスできる **Azure HDInsight クラスター**。 [Data Lake Store を使用する HDInsight クラスターの作成](data-lake-store-hdinsight-hadoop-use-portal.md)に関するページを参照してください。 クラスターのリモート デスクトップが有効になっていることを確認します。


## <a name="guidelines-for-data-ingestion-tools"></a>データ取り込みツールのガイドライン

このセクションでは、Data Lake Store に対してデータのコピーまたは移動を行う際のパフォーマンスを向上させるための一般的なガイダンスを示します。 このセクションでは、パフォーマンスを制限する要因とそれらの制限を克服する方法について説明します。 注意すべき考慮事項をいくつか次に示します。

* **ソース データ** - ソース データの取得元に起因する制約がたくさんあります。 ソース データが低速なスピンドルやスループット能力の低いリモート ストレージ上にある場合、スループットがボトルネックになることがあります。 (可能であればローカル ディスクで) SSD を使用すると、ディスク スループットが向上するため、最高のパフォーマンスが得られます。

* **ネットワーク** - ソース データが VM 上にある場合は、VM と Data Lake Store 間のネットワーク接続が重要です。 使用可能な最大の NIC を備えた VM を使用すると、ネットワーク帯域幅が向上します。

* **リージョン間コピー** - 米国東部 2 の VM でデータ取り込みツールを実行して米国中部の Data Lake Store アカウントにデータを書き込むなど、リージョン間のデータ I/O には膨大なネットワーク コストがかかります。 リージョン間でデータをコピーしていると、パフォーマンスが低下する場合があります。 ネットワーク スループットを最大化するために、コピー先の Data Lake Store アカウントと同じリージョンの VM でデータ取り込みジョブを使用することをお勧めします。                                                                                                                                        

* **クラスター** - HDInsight クラスター (DistCp など) を使用してデータ取り込みジョブを実行する場合は、D シリーズ VM をクラスターに使用することをお勧めします。これは、D シリーズ VM はメモリが多いからです。 また、コア数の増加もスループットの向上に貢献します。                                                                                                                                                                                                                                                                                                            

* **スレッドの同時実行性** - HDInsight クラスターを使用して、ストレージ コンテナーからデータをコピーする場合は、クラスター サイズ、コンテナー サイズ、およびスレッドの設定に応じて使用できる並列スレッドの数に制限があります。 Data Lake Store でのパフォーマンスを向上させるための最も重要な方法の&1; つは、同時実行性を向上させることです。 スループットを向上させるには、設定を調整して、同時実行性を最大化する必要があります。 次の表には、同時実行性を向上させるために構成できる設定を、取り込み方法ごとに示しています。 表内のリンクをクリックすると、ツールを使用して Data Lake Store にデータを取り込む方法のほかに、ツールのパフォーマンスを調整してスループットを最大化する方法についても説明している記事に移動します。

    | ツール               | 同時実行性の設定                                                                |
    |--------------------|------------------------------------------------------------------------------------|
    | [Powershell](data-lake-store-get-started-powershell.md)       | PerFileThreadCount、ConcurrentFileCount |
    | [AdlCopy](data-lake-store-copy-data-azure-storage-blob.md)    | Azure Data Lake Analytics ユニット         |
    | [DistCp](data-lake-store-copy-data-wasb-distcp.md)            | -m (マッパー)                             |
    | [Azure Data Factory](../data-factory/data-factory-azure-datalake-connector.md)| parallelCopies                          |
    | [Sqoop](data-lake-store-data-transfer-sql-sqoop.md)           | fs.azure.block.size、-m (マッパー)        |


## <a name="guidelines-while-working-with-hdinsight-workloads"></a>HDInsight ワークロードを使用する場合のガイドライン

Data Lake Store 内のデータを操作する分析ワークロードを実行する場合は、Data Lake Store のパフォーマンスを最大限に引き出すために、HDInsight 3.5 クラスター バージョンを使用することをお勧めします。 ジョブが I/O 集中型の場合、パフォーマンス上の理由から特定のパラメーターを構成できます。 たとえば、ジョブが主に読み取りまたは書き込みで構成されている場合は、Azure Data Lake Store との間の I/O の同時実行性を向上させると、パフォーマンスが向上する可能性があります。

Azure Data Lake Store は、同時実行性が高い場合に最高のパフォーマンスを発揮するよう最適化されています。 I/O 集中型のジョブの同時実行性を向上させる一般的な方法はいくつかあります。

1. **少数の大規模な YARN コンテナーではなく、多数のコンピューティング YARN コンテナーを実行する** – コンテナーが増えると、入出力操作の同時実行性が向上し、Data Lake Store の能力を活用できます。

    たとえば、HDInsight クラスターに D3v2 ノードが 2 つあるとします。 各 D3v2 ノードは 12 GB の YARN メモリを備えているため、2 台の D3v2 マシンで YARN メモリは 24 GB になります。 また、YARN コンテナーのサイズを 6 GB に設定しています。 そのため、各 6 GB のコンテナーが 4 つ作成されます。 したがって、4 つの同時実行タスクを並列に実行できます。 同時実行性を向上させるには、たとえば、コンテナーのサイズを 3 GB に減らします。そうすると、各 3 GB のコンテナーが 8 つ作成されます。 この場合、8 つの同時実行タスクを並列に実行できます。 以下に図を示します。

    ![Data Lake Store のパフォーマンス](./media/data-lake-store-performance-tuning-guidance/image-1.png)

    よく寄せられる質問に、"コンテナー サイズを 1 GB のメモリに減らせば、コンテナーが 24 個になり、同時実行性がさらに向上するのに、そうしてはいけないのはなぜですか" というものがあります。 これは、タスクにメモリが 3 GB 必要か、1 GB で十分かによります。  コンテナー内でメモリが 1 GB しか必要ない単純な操作を実行する場合や、メモリが 3 GB 必要になる可能性がある複雑な操作を実行する場合があります。  コンテナーのサイズを小さくしすぎると、メモリ不足例外が発生する可能性があります。  その場合は、コンテナーのサイズを増やす必要があります。  メモリ以外に、仮想コアの数も並列処理に影響します。

    ![Data Lake Store のパフォーマンス](./media/data-lake-store-performance-tuning-guidance/image-2.png)

2. **クラスターのメモリを増やして、同時実行性を向上させる** – クラスターのメモリを増やすには、クラスターのサイズを増やすか、メモリが多い VM タイプを選択します。 そうすると、使用可能な YARN メモリの量が増加するため、コンテナーをもっと作成して、同時実行性を向上させることができます。  

    たとえば、HDInsight クラスターに D3v2 ノードが 1 つあり、YARN メモリが 12 GB で、コンテナーが 3 GB とします。  クラスターを D3v2 2 台に拡張すると、YARN メモリが 24 GB に増加します。  その結果、同時実行数が 4 から 8 に増えます。

    ![Data Lake Store のパフォーマンス](./media/data-lake-store-performance-tuning-guidance/image-3.png)

3. **タスクの数を現在の同時実行数に設定する** – ここまでで、既にコンテナー サイズを適切に設定して、同時実行数を最大化しました。 今度は、これらのコンテナーをすべて使用するように、タスクの数を設定する必要があります。 各ワークロード内のタスクにはさまざまな名前が付いています。

    また、ジョブのサイズを考慮することが必要な場合もあります。 ジョブのサイズが大きい場合は、各タスクで大量のデータを処理する可能性があります。 タスク数を増やすと、各タスクで処理するデータの量を抑えることができます。

    たとえば、6 つのコンテナーがあるとします。 開始点として、タスク数を 6 に設定します。 タスク数を増やして、パフォーマンスが向上するかどうか実験してみます。 タスク数を大きく設定しても、同時実行性は向上しません。 タスク数を 6 より大きく設定した場合、次の波が来るまで、タスクは実行されません。 タスク数を 6 より小さく設定した場合、同時実行性がフル活用されません。

    タスク数の設定に使用できるパラメーターは、ワークロードごとに異なります。 次の表には、その一部を示しています。

    | ワークロード               | タスク数を設定するパラメーター                                                         |
    |--------------------|------------------------------------------------------------------------------------|
    | [HDInsight の Spark](data-lake-store-performance-tuning-spark.md)       | <ul><li>Num-executors</li><li>Executor-memory</li><li>Executor-cores</li></ul> |
    | [HDInsight の Hive](data-lake-store-performance-tuning-hive.md)    | hive.tez.container.size         |
    | [HDInsight の MapReduce](data-lake-store-performance-tuning-mapreduce.md)            | <ul><li>Mapreduce.map.memory</li><li>Mapreduce.job.maps</li><li>Mapreduce.reduce.memory</li><li>Mapreduce.job.reduces</li></ul> |
    | [HDInsight の Storm](data-lake-store-performance-tuning-storm.md)| <ul><li>ワーカー プロセスの数</li><li>スパウトの Executor インスタンスの数</li><li>ボルトの Executor インスタンスの数 </li><li>スパウトのタスクの数</li><li>ボルトのタスクの数</li></ul>|

## <a name="see-also"></a>関連項目
* [Azure Data Lake Store の概要](data-lake-store-overview.md)
* [Azure Data Lake Analytics の使用を開始する](../data-lake-analytics/data-lake-analytics-get-started-portal.md)

