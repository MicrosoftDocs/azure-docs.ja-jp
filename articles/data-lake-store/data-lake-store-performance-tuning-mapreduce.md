---
title: Azure Data Lake Storage Gen1 のパフォーマンス チューニング - MapReduce
description: パラメーター、ガイダンス、計算例、制限事項など、Azure Data Lake Storage Gen1 の MapReduce のパフォーマンス チューニングについて説明します。
author: twooley
ms.service: data-lake-store
ms.topic: how-to
ms.date: 12/19/2016
ms.author: twooley
ms.openlocfilehash: 2549413241e422fb1e0e5e1f079c287e0b7cf005
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "97723730"
---
# <a name="performance-tuning-guidance-for-mapreduce-on-hdinsight-and-azure-data-lake-storage-gen1"></a>HDInsight の MapReduce と Azure Data Lake Storage Gen1 のパフォーマンス チューニング ガイダンス

## <a name="prerequisites"></a>前提条件

* **Azure サブスクリプション**。 [Azure 無料試用版の取得](https://azure.microsoft.com/pricing/free-trial/)に関するページを参照してください。
* **Azure Data Lake Storage Gen1 アカウント**。 これを作成する手順については、[Azure Data Lake Storage Gen1 の使用開始](data-lake-store-get-started-portal.md)に関するページを参照してください。
* Data Lake Storage Gen1 アカウントにアクセスできる **Azure HDInsight クラスター**。 [Data Lake Storage Gen1 を使用する HDInsight クラスターの作成](data-lake-store-hdinsight-hadoop-use-portal.md)に関するページを参照してください。 クラスターのリモート デスクトップが有効になっていることを確認します。
* **HDInsight での MapReduce の使用**。 詳細については、「[HDInsight での MapReduce と Hadoop の使用](../hdinsight/hadoop/hdinsight-use-mapreduce.md)」を参照してください。
* **Data Lake Storage Gen1 のパフォーマンス チューニング ガイドラインを確認します**。 一般的なパフォーマンスの概念については、[Data Lake Storage Gen1 のパフォーマンス チューニング ガイダンス](./data-lake-store-performance-tuning-guidance.md)を参照してください。

## <a name="parameters"></a>パラメーター

MapReduce ジョブの実行時に、Data Lake Storage Gen1 のパフォーマンスを向上させるために構成できる最も重要なパラメーターは次のとおりです。

|パラメーター      | 説明  |
|---------|---------|
|`Mapreduce.map.memory.mb`  |  各 Mapper に割り当てるメモリの量。  |
|`Mapreduce.job.maps`     |  ジョブごとのマップ タスクの数。  |
|`Mapreduce.reduce.memory.mb`     |  各 Reducer に割り当てるメモリの量。  |
|`Mapreduce.job.reduces`    |   ジョブごとの削減タスクの数。  |

### <a name="mapreducemapmemory--mapreducereducememory"></a>mapreduce.map.memory / mapreduce.reduce.memory

この数値は、マップまたは削減タスク、あるいはその両方に必要なメモリ量に基づいて調整します。 Yarn 構成で、Ambari の `mapreduce.map.memory` や `mapreduce.reduce.memory` の既定値を表示できます。 Ambari で YARN に移動し、 **[Configs]\(構成\)** タブを表示します。YARN メモリが表示されます。

### <a name="mapreducejobmaps--mapreducejobreduces"></a>Mapreduce.job.maps / Mapreduce.job.reduces

これにより、作成する Mapper または Reducer の最大数が決まります。 分割の数によって MapReduce ジョブに作成される Mapper の数が決まります。 そのため、要求した Mapper の数よりも分割が少ないと、要求したよりも少ない数の Mapper を取得する場合があります。

## <a name="guidance"></a>ガイダンス

### <a name="step-1-determine-number-of-jobs-running"></a>手順 1:実行するジョブの数を決定する

既定では、MapReduce はジョブにクラスター全体を使用します。 クラスターの使用を抑えるには、使用可能なコンテナーの数よりも少ない Mapper を使用します。 このドキュメントのガイダンスでは、お使いのアプリケーションがクラスターで唯一実行されているアプリケーションであると想定しています。

### <a name="step-2-set-mapreducemapmemorymapreducereducememory"></a>手順 2:mapreduce.map.memory/mapreduce.reduce.memory を設定する

Map タスクと Reduce タスクのメモリ サイズは、特定のジョブに依存します。 コンカレンシーを向上させるには、メモリ サイズを小さくします。 同時に実行できるタスクの数は、コンテナー数によって異なります。 Mapper や Reducer ごとのメモリの量を減らすことで、より多くのコンテナーを作成できるため、より多くの Mapper や Reducer を同時に実行できます。 メモリの量を減らし過ぎると、一部のプロセスでメモリが不足する場合があります。 ジョブの実行時にヒープ エラーが発生する場合は、Mapper や Reducer ごとのメモリを増やします。 コンテナーをさらに追加すると、追加された各コンテナーに余分なオーバーヘッドが加わり、パフォーマンスを低下させる可能性がある点を考慮してください。 別の方法としては、メモリ容量が大きいクラスターを使用してメモリ量を増やすか、クラスター内のノード数を増やすことが挙げられます。 メモリの量が増えると使用できるコンテナーの数も増えて、コンカレンシーが向上されます。

### <a name="step-3-determine-total-yarn-memory"></a>手順 3:合計 YARN メモリを決定する

mapreduce.job.maps/mapreduce.job.reduces をチューニングするには、使用可能な合計 YARN メモリの量を考慮してください。 この情報は Ambari で利用できます。 YARN に移動し、 **[Configs]\(構成\)** タブを表示します。YARN メモリは、このウィンドウに表示されます。 合計 YARN メモリを得るには、YARN メモリと、クラスター内のノードの数を掛けます。

`Total YARN memory = nodes * YARN memory per node`

空のクラスターを使用している場合、メモリはクラスターの合計 YARN メモリになります。 ほかのアプリケーションでメモリを使用している場合は、Mapper や Reducer の数を使用するコンテナーの数に減らすことで、クラスターのメモリの一部のみを使用するように選択できます。

### <a name="step-4-calculate-number-of-yarn-containers"></a>手順 4:YARN コンテナーの数を計算する

YARN コンテナーによって、ジョブのコンカレンシーがどの程度可能かが決まります。 合計 YARN メモリを、mapreduce.map.memory で割ります。

`# of YARN containers = total YARN memory / mapreduce.map.memory`

### <a name="step-5-set-mapreducejobmapsmapreducejobreduces"></a>手順 5:mapreduce.job.maps/mapreduce.job.reduces を設定する

mapreduce.job.maps/mapreduce.job.reduces を、少なくとも、使用可能なコンテナーの数に設定します。 さらに、Mapper や Reducer の数を増やして、パフォーマンスが向上するかを確認することができます。 Mapper の数を増やすとオーバーヘッドが追加されるため、Mapper の数が多すぎるとパフォーマンスが低下する可能性がある点にご注意ください。

CPU スケジューリングと CPU の分離は既定ではオフになっているため、YARN コンテナーの数はメモリによって制約されます。

## <a name="example-calculation"></a>計算例

現在 8 つの D14 ノードから成るクラスターがあり、I/O 集約型ジョブを実行するとします。 次の計算を行う必要があります。

### <a name="step-1-determine-number-of-jobs-running"></a>手順 1:実行するジョブの数を決定する

この例では、実行されているジョブは 1 つだけであると想定しています。

### <a name="step-2-set-mapreducemapmemorymapreducereducememory"></a>手順 2:mapreduce.map.memory/mapreduce.reduce.memory を設定する

この例では、I/O 集約型ジョブを実行し、マップ タスクには 3 GB のメモリで十分であると判断しています。

`mapreduce.map.memory = 3GB`

### <a name="step-3-determine-total-yarn-memory"></a>手順 3:合計 YARN メモリを決定する

`total memory from the cluster is 8 nodes * 96GB of YARN memory for a D14 = 768GB`

### <a name="step-4-calculate--of-yarn-containers"></a>手順 4:YARN コンテナーの数を計算する

`# of YARN containers = 768 GB of available memory / 3 GB of memory = 256`

### <a name="step-5-set-mapreducejobmapsmapreducejobreduces"></a>手順 5:mapreduce.job.maps/mapreduce.job.reduces を設定する

`mapreduce.map.jobs = 256`

## <a name="limitations"></a>制限事項

**Data Lake Storage Gen1 の調整**

マルチテナント サービスとして、Data Lake Storage Gen1 ではアカウント レベルの帯域幅制限が設定されています。 帯域幅制限に達すると、タスク エラーが発生します。 エラーを特定するには、タスク ログの調整エラーを監視します。 ジョブにより広い帯域幅が必要な場合は、お問い合わせください。

調整されているかどうかを確認するには、クライアント側でデバッグ ログを有効にする必要があります。 その方法は次のとおりです。

1. [Ambari] > [YARN] > [Config] \(構成) > [Advanced yarn-log4j] \(詳細な yarn-log4j) の順に進み、log4j プロパティに log4j.logger.com.microsoft.azure.datalake.store=DEBUG を入力します。

2. すべてのノードとサービスを再起動し、構成を有効にします。

3. 調整されている場合は、YARN ログ ファイルに HTTP 429 のエラー コードが表示されます。 YARN ログ ファイルは /tmp/&lt;user&gt;/yarn.log に格納されています。

## <a name="examples-to-run"></a>実行例

Data Lake Storage Gen1 で MapReduce を実行する方法を示すために、次の設定のクラスターで実行されたサンプル コードをいくつかご紹介します。

* 16 ノード (D14 v2)
* HDI 3.6 を実行する Hadoop クラスター

最初に、MapReduce Teragen、Terasort、および Teravalidate を実行するコマンドの例を示します。 これらのコマンドは、お使いのリソースに基づいて調整できます。

### <a name="teragen"></a>Teragen

```
yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar teragen -Dmapreduce.job.maps=2048 -Dmapreduce.map.memory.mb=3072 10000000000 adl://example/data/1TB-sort-input
```

### <a name="terasort"></a>Terasort

```
yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar terasort -Dmapreduce.job.maps=2048 -Dmapreduce.map.memory.mb=3072 -Dmapreduce.job.reduces=512 -Dmapreduce.reduce.memory.mb=3072 adl://example/data/1TB-sort-input adl://example/data/1TB-sort-output
```

### <a name="teravalidate"></a>Teravalidate

```
yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar teravalidate -Dmapreduce.job.maps=512 -Dmapreduce.map.memory.mb=3072 adl://example/data/1TB-sort-output adl://example/data/1TB-sort-validate
```