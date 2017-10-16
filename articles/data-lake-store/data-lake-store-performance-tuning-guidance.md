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
ms.date: 06/30/2017
ms.author: stewu
ms.openlocfilehash: 900447ab931f15e4d27aedd525eba7881ba813b2
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="tuning-azure-data-lake-store-for-performance"></a>Azure Data Lake Store のパフォーマンス チューニング

Data Lake Store は、I/O 集中型分析とデータ移動での高スループットをサポートします。  Azure Data Lake Store では、1 秒あたりに読み取むまたは書き込むデータ量など、利用可能なすべてのスループットを利用することが、最適なパフォーマンスを得るために重要となります。  並列での読み取り数と書き込み数をできるだけ多くすることで、これを実現します。

![Data Lake Store のパフォーマンス](./media/data-lake-store-performance-tuning-guidance/throughput.png)

Azure Data Lake Store は、あらゆる分析シナリオで必要とされるスループットを提供するようにスケーリングできます。 既定では、Azure Data Lake Store アカウントは、広範なカテゴリのユース ケースのニーズを満たすのに十分なスループットを自動的に提供します。 お客様が既定の制限に達した場合、Microsoft サポートに連絡して、さらに高いスループットを提供するように ADLS アカウントを構成することができます。

## <a name="data-ingestion"></a>データの取り込み

ソース システムから ADLS にデータを取り込む場合には、ソース ハードウェア、ソース ネットワーク ハードウェア、および ADLS へのネットワークの接続性がボトルネックとなる可能性があることを考慮することが重要です。  

![Data Lake Store のパフォーマンス](./media/data-lake-store-performance-tuning-guidance/bottleneck.png)

これらの要因がデータの移動に影響を与えないようにすることが重要です。

### <a name="source-hardware"></a>ソース ハードウェア

オンプレミス マシンを使用しているか、または Azure の VM を使用しているかに関わらず、適切なハードウェアを慎重に選択する必要があります。 ソース ディスク ハードウェアには、HDD よりも SSD を選び、高速スピンドルを備えたディスク ハードウェアをお選びください。 ソース ネットワーク ハードウェアには、考えられる最速の NIC を使用してください。  Azure に関しては、適度に強力なディスクとネットワーク ハードウェアを備えた Azure D14 VM をお勧めします。

### <a name="network-connectivity-to-azure-data-lake-store"></a>Azure Data Lake Store へのネットワーク接続

ソース データと Azure Data Lake Store との間のネットワーク接続はボトルネックになることがあります。 ソース データがオンプレミスの場合、[Azure ExpressRoute](https://azure.microsoft.com/en-us/services/expressroute/) で専用のリンクを使用することを検討してください。 ソース データが Azure にある場合、データが Data Lake Store と同じ Azure リージョンにあると、パフォーマンスは最適となります。

### <a name="configure-data-ingestion-tools-for-maximum-parallelization"></a>データ インジェスト ツールの最大並列化処理の構成

上記のソース ハードウェアとネットワーク接続性のボトルネックに対処したら、次はデータ インジェスト ツールを構成します。 次の表に、一般的なインジェスト ツールの主要な設定の概要と、それらのパフォーマンス チューニングに関する詳細な記事を示します。  ご自身のシナリオで使用すべきツールの詳細については、この[記事](https://docs.microsoft.com/en-us/azure/data-lake-store/data-lake-store-data-scenarios)をご覧ください。

| ツール               | 設定     | 詳細                                                                 |
|--------------------|------------------------------------------------------|------------------------------|
| Powershell       | PerFileThreadCount、ConcurrentFileCount |  [リンク](https://docs.microsoft.com/en-us/azure/data-lake-store/data-lake-store-get-started-powershell#performance-guidance-while-using-powershell)   |
| AdlCopy    | Azure Data Lake Analytics ユニット  |   [リンク](https://docs.microsoft.com/en-us/azure/data-lake-store/data-lake-store-copy-data-azure-storage-blob#performance-considerations-for-using-adlcopy)         |
| DistCp            | -m (マッパー)   | [リンク](https://docs.microsoft.com/en-us/azure/data-lake-store/data-lake-store-copy-data-wasb-distcp#performance-considerations-while-using-distcp)                             |
| Azure Data Factory| parallelCopies    | [リンク](../data-factory/copy-activity-performance.md)                          |
| Sqoop           | fs.azure.block.size、-m (マッパー)    |   [リンク](https://blogs.msdn.microsoft.com/bigdatasupport/2015/02/17/sqoop-job-performance-tuning-in-hdinsight-hadoop/)        |

## <a name="structure-your-data-set"></a>データ セットの構成

データが Data Lake Store に保存されると、ファイル サイズ、ファイル数、およびフォルダー構造がパフォーマンスに影響を与えるようになります。  次のセクションでは、これらの領域に関するベスト プラクティスについて説明します。  

### <a name="file-size"></a>ファイル サイズ

通常、HDInsight や Azure Data Lake Analytics などの分析エンジンでは、ファイルごとのオーバーヘッドがあります。  データを多数の小さなファイルとして保存すると、パフォーマンスに悪影響を及ぼすことがあります。  

一般に、パフォーマンスを向上させるには、データをより大きなサイズのファイルにまとめます。  データ セットを 256 MB か、それ以上のサイズのファイルにまとめることが多いでしょう。 画像やバイナリ データなどの場合、並行して処理することはできません。  このような場合は、個々のファイルを 2 GB 未満に維持することをお勧めします。

場合によっては、データ パイプラインで小さなファイルを多数含む生データの制御が制限されることがあります。  ダウンストリーム アプリケーションでは、“調理“ のプロセスを設けて、より大きいファイルを生成することをお勧めします。  

### <a name="organizing-time-series-data-in-folders"></a>フォルダー内の時系列データの整理

Hive および ADLA のワークロードでは、時系列データのパーティションを削除すると、一部のクエリがデータのサブセットのみを読み取るようにできるため、パフォーマンスを向上させることができます。    

時系列データを取り込むこれらのパイプラインでは、多くの場合、ファイルにかなり構造化されたファイル名やフォルダー名が付けられます。 以下に、日付によって構成されたデータの一般的な例を示します。

    \DataSet\YYYY\MM\DD\datafile_YYYY_MM_DD.tsv

フォルダーとファイル名の両方に、日時の情報が示されていることに注意してください。

日付と時刻については、以下が一般的なパターンです。

    \DataSet\YYYY\MM\DD\HH\mm\datafile_YYYY_MM_DD_HH_mm.tsv

繰り返しになりますが、フォルダーとファイルの整理については、より大きなファイルサイズに最適化され、各フォルダーに妥当な数のファイルが配置されるような選択を行ってください。

## <a name="optimizing-io-intensive-jobs-on-hadoop-and-spark-workloads-on-hdinsight"></a>HDInsight の Hadoop および Spark ワークロードでの I/O 集中型ジョブの最適化

ジョブは、大きく次の 3 つのカテゴリに分けられます。

* **CPU 集中型**  これらのジョブは、計算時間は長くかかりますが、I/O 時間は最小限で済みます。  機械学習や自然言語処理のジョブなどが、その例です。  
* **メモリ集中型**  これらのジョブは、大量のメモリを使用します。  PageRank やリアルタイム分析のジョブなどが、その例です。  
* **I/O 集中型**  これらのジョブは、大半の時間を I/O 処理に費やします。  読み取りおよび書き込み操作のみを行うコピー ジョブなどが一般的な例です。  その他の例には、大量のデータを読み取ってデータ変換を行った後、保存場所に書き戻すデータ準備ジョブなどがあります。  

次のガイダンスは、I/O 集中型ジョブにのみ適用されます。

### <a name="general-considerations-for-an-hdinsight-cluster"></a>HDInsight クラスターの一般的な考慮事項

* **HDInsight のバージョン** 最適なパフォーマンスを得るには、HDInsight の最新リリースを使用してください。
* **リージョン** Data Lake Store は HDInsight クラスターと同じリージョンに配置します。  

HDInsight クラスターは、2 つのヘッド ノードと複数のワーカー ノードで構成されます。 各ワーカー ノードは、特定数のコアとメモリを提供します。これは VM の種類によって決まります。  ジョブの実行時には、YARN がリソース ネゴシエーターとなり、コンテナー作成で使用可能なメモリとコアを割り当てます。  各コンテナーは、ジョブを完了するために必要なタスクを実行します。  コンテナーは、タスクを迅速に処理するために並列で実行されます。 そのため、できるだけ多くの並列コンテナーを実行することでパフォーマンスが向上します。

HDInsight クラスター内には 3 つのレイヤーがあります。これらは、コンテナー数を増やし、使用可能なすべてのスループットを使用するようにチューニングできます。  

* **物理レイヤー**
* **YARN レイヤー**
* **ワークロード レイヤー**

### <a name="physical-layer"></a>物理レイヤー

**より多くのノードと大きなサイズの VM、またはそのどちらかで、クラスターを実行します。**  次の図に示すように、クラスターを大きくすると、より多くの YARN コンテナーを実行できます。

![Data Lake Store のパフォーマンス](./media/data-lake-store-performance-tuning-guidance/VM.png)

**より大きなネットワーク帯域幅を備えた VM を使用します。**  ネットワーク帯域幅が Data Lake Store のスループットよりも小さい場合、ネットワーク帯域幅の容量がボトルネックとなる可能性があります。  VM によって、ネットワーク帯域幅のサイズは異なります。  できる限り大きなネットワーク帯域幅を備えた VM タイプを選択してください。

### <a name="yarn-layer"></a>YARN レイヤー

**より小さい YARN コンテナーを使用します。**  各 YARN コンテナーのサイズを小さくして、同じ容量のリソースを備えたコンテナーの数を増やします。

![Data Lake Store のパフォーマンス](./media/data-lake-store-performance-tuning-guidance/small-containers.png)

ワークロードによっては、YARN コンテナーが常に必要とする最小サイズが存在します。 選択したコンテナーが小さすぎる場合、ジョブでメモリ不足の問題が発生します。 通常、YARN コンテナーは 1 GB 以上にする必要があります。 3 GB の YARN コンテナーを検討するのが一般的です。 一部のワークロードでは、それよりも大きい YARN コンテナーが必要な場合もあります。  

**YARN コンテナーあたりのコア数を増やします。**  各コンテナーで実行される並列タスクの数を増やすには、各コンテナーに割り当てるコアの数を増やします。  これは、コンテナーごとに複数のタスクを実行する Spark のようなアプリケーションで有効です。  各コンテナーで単一スレッドを実行する Hive のようなアプリケーションでは、コンテナーごとのコア数を増やすよりもコンテナー数を増やすことをお勧めします。   

### <a name="workload-layer"></a>ワークロード レイヤー

**使用可能なすべてのコンテナーを使用します。**  すべてのリソースが使用されるように、タスク数を使用可能なコンテナー数と同じ数、またはそれ以上に設定します。

![Data Lake Store のパフォーマンス](./media/data-lake-store-performance-tuning-guidance/use-containers.png)

**タスクでエラーが発生するとコストがかかります。** 各タスクで大量のデータを処理すると、タスクでエラーが発生した場合、再試行に高いコストがかかることになります。  そのため、多数のタスクを作成し、各タスクで少量のデータを処理するようにすることをお勧めします。

上記の一般的なガイドラインに加え、各アプリケーションには、チューニングで使用できるアプリケーション固有のさまざまなパラメーターがあります。 次の表に、パラメーターの一部と、各アプリケーションでパフォーマンス チューニングを開始するためのリンクを示します。

| ワークロード               | タスク数を設定するパラメーター                                                         |
|--------------------|-------------------------------------------------------------------------------------|
| [HDInsight の Spark](data-lake-store-performance-tuning-spark.md)       | <ul><li>Num-executors</li><li>Executor-memory</li><li>Executor-cores</li></ul> |
| [HDInsight の Hive](data-lake-store-performance-tuning-hive.md)    | <ul><li>hive.tez.container.size</li></ul>         |
| [HDInsight の MapReduce](data-lake-store-performance-tuning-mapreduce.md)            | <ul><li>Mapreduce.map.memory</li><li>Mapreduce.job.maps</li><li>Mapreduce.reduce.memory</li><li>Mapreduce.job.reduces</li></ul> |
| [HDInsight の Storm](data-lake-store-performance-tuning-storm.md)| <ul><li>ワーカー プロセスの数</li><li>スパウトの Executor インスタンスの数</li><li>ボルトの Executor インスタンスの数 </li><li>スパウトのタスクの数</li><li>ボルトのタスクの数</li></ul>|

## <a name="see-also"></a>関連項目
* [Azure Data Lake Store の概要](data-lake-store-overview.md)
* [Azure Data Lake Analytics の使用を開始する](../data-lake-analytics/data-lake-analytics-get-started-portal.md)
