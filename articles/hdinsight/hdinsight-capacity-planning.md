---
title: Azure HDInsight のクラスターの容量計画
description: 容量とパフォーマンスのニーズを満たす HDInsight クラスターを指定する方法。
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 05/23/2019
ms.author: hrasheed
ms.openlocfilehash: bd2284211c2fdc5a346c6ffb113f89fe311a358c
ms.sourcegitcommit: 1572b615c8f863be4986c23ea2ff7642b02bc605
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/10/2019
ms.locfileid: "67786512"
---
# <a name="capacity-planning-for-hdinsight-clusters"></a>HDInsight クラスターの容量計画

HDInsight クラスターをデプロイする前に、必要なパフォーマンスとスケールを決定して、必要となるクラスターの容量を計画します。 この計画により、使いやすさとコストの両方を最適化できます。 クラスターの容量に関する決定事項の中には、デプロイ後に変更できないものもあります。 パフォーマンス パラメーターが変わった場合は、保存されているデータを失うことなく、クラスターを解体して再作成できます。

容量計画における重要な検討事項は次のとおりです。

* クラスターをどの地理的リージョンにデプロイすればよいか。
* どれくらいのストレージ容量が必要か。
* どのような種類のクラスターをデプロイすればよいか。
* クラスター ノードでは、どのサイズおよび種類の仮想マシン (VM) を使用すればよいか。
* クラスターにはワーカー ノードがいくつ必要か。

## <a name="choose-an-azure-region"></a>Azure リージョンの選択

Azure リージョンによって、クラスターを物理的にプロビジョニングする場所が決まります。 読み取りと書き込みの待機時間を最小限に抑えるには、クラスターをデータの近くに配置する必要があります。

HDInsight は多数の Azure リージョンで利用できます。 最も近いリージョンを確認するには、「[リージョン別の利用可能な製品](https://azure.microsoft.com/regions/services/)」の「*分析*」の「*HDInsight*」エントリを参照してください。

## <a name="choose-storage-location-and-size"></a>ストレージの場所とサイズの選択

### <a name="location-of-default-storage"></a>既定のストレージの場所

既定のストレージ (Azure ストレージ アカウントまたは Azure Data Lake Storage) は、クラスターと同じ場所に存在する必要があります。 Azure Storage はあらゆる場所で利用できます。 Data Lake Storage Gen1 は一部のリージョンで利用できます。Data Lake Storage の現在の提供状況については、「[リージョン別の利用可能な製品](https://azure.microsoft.com/regions/services/)」の「*Storage*」を参照してください。

### <a name="location-of-existing-data"></a>既存のデータの場所

データを格納しているストレージ アカウントまたは Data Lake Storage が既にあり、そのストレージをクラスターの既定のストレージとして使用する場合は、ストレージと同じ場所にクラスターをデプロイする必要があります。

### <a name="storage-size"></a>ストレージ サイズ

HDInsight クラスターのデプロイ後に、追加の Azure ストレージ アカウントを接続したり、他の Data Lake Storage にアクセスしたりできます。 すべてのストレージ アカウントがクラスターと同じ場所に存在する必要があります。 Data Lake Storage は別の場所にあっても構いませんが、その場合、データの読み取り/書き込みの待機時間が発生する可能性があります。

Azure Storage Gen1 には[容量制限](../azure-subscription-service-limits.md#storage-limits)がありますが、Data Lake Storage には実質的に制限はありません。

クラスターは、さまざまなストレージ アカウントの組み合わせにアクセスできます。 一般的な例を次に示します。

* データ量が、1 つの Blob Storage コンテナーのストレージ容量を超える可能性がある場合。
* BLOB コンテナーへのアクセス レートがしきい値を超え、調整が発生する可能性がある場合。
* データを作成する必要があるときに、クラスターで利用できる BLOB コンテナーに既にアップロードしている場合。
* セキュリティ上の理由からストレージのさまざまな部分を分離する場合、または管理を簡素化する場合。

48 ノードのクラスターの場合、4 から 8 個のストレージ アカウントを使用することをお勧めします。 既に十分な数のストレージが存在する場合もありますが、各ストレージ アカウントはコンピューティング ノードに追加のネットワーク帯域幅を提供します。 複数のストレージ アカウントがある場合は、各ストレージ アカウントにプレフィックスなしのランダムな名前を使用します。 ランダムな名前付けの目的は、すべてのアカウント間でストレージのボトルネック (調整) や共通モード エラーが発生する可能性を低減することです。 パフォーマンスを向上させるために、ストレージ アカウントごとにコンテナーを 1 つだけ使用します。

## <a name="choose-a-cluster-type"></a>クラスターの種類の選択

クラスターの種類によって、実行されるワークロード ([Apache Hadoop](https://hadoop.apache.org/)、[Apache Storm](https://storm.apache.org/)、[Apache Kafka](https://kafka.apache.org/)、[Apache Spark](https://spark.apache.org/) など) が決まります。HDInsight クラスターは、そのワークロードを実行するように構成されます。 使用できるクラスターの種類の詳細については、[Azure HDInsight の概要](hadoop/apache-hadoop-introduction.md#cluster-types-in-hdinsight)に関するページを参照してください。 クラスターの種類ごとに、サイズとノード数の要件を含む特定のデプロイ トポロジがあります。

## <a name="choose-the-vm-size-and-type"></a>VM のサイズと種類の選択

各クラスターの種類には一連のノード タイプがあり、各ノード タイプには VM のサイズと種類の固有のオプションがあります。

アプリケーションに最適なクラスター サイズを決定するために、クラスターの容量のベンチマークを実行し、その結果に従ってサイズを増やすことができます。 たとえば、シミュレートされたワークロードや "*カナリア クエリ*" をご利用いただけます。 シミュレートされたワークロードを使用して、さまざまなサイズのクラスターで予想されるワークロードを実行し、必要なパフォーマンスに到達するまでサイズを徐々に増やしていきます。 他の実稼働クエリの間にカナリア クエリを定期的に挿入することで、クラスターに十分なリソースがあるかどうかを示すことができます。

VM のサイズと種類は、CPU の処理能力、RAM サイズ、ネットワーク待ち時間によって決まります。

* CPU: VM サイズによってコア数が決まります。 コア数が増えるほど、各ノードで実現できる並列計算の度合いが上がります。 また、VM の一部の種類は高速コアを備えています。

* RAM: VM サイズによって、VM で使用可能な RAM の容量も決まります。 処理するデータをディスクから読み取るのではなく、メモリに格納するワークロードの場合、ワーカー ノードにデータを格納できる十分なメモリがあることを確認します。

* ネットワーク: クラスターのほとんどの種類では、クラスターで処理されるデータはローカル ディスク上に存在するのではなく、Data Lake Storage や Azure Storage などの外部ストレージ サービスに存在します。 ノードの VM とストレージ サービス間のネットワーク帯域幅とスループットを考慮します。 通常は、VM のサイズが大きいほど、VM が使用できるネットワーク帯域幅が増加します。 詳細については、[VM サイズの概要](https://docs.microsoft.com/azure/virtual-machines/linux/sizes)に関する記事をご覧ください。

## <a name="choose-the-cluster-scale"></a>クラスターのスケールの選択

クラスターのスケールは、VM ノードの数によって決まります。 どのクラスターの種類にも、特定のスケールのノード タイプと、スケールアウトをサポートするノード タイプがあります。たとえば、クラスターで 3 つの [Apache ZooKeeper](https://zookeeper.apache.org/) ノードまたは 2 つのヘッド ノードが必要な場合があります。 分散方式でデータ処理を実行するワーカー ノードは、ワーカー ノードを追加することで、スケールアウトのメリットが得られます。

クラスターの種類によっては、ワーカー ノードの数を増やすことでコンピューティング能力が高まりますが (コア数の増加など)、処理するデータのインメモリ ストレージをクラスター全体でサポートするために必要なメモリの総容量も増加する可能性があります。 VM のサイズと種類を選択する場合と同様に、通常、クラスタの適切なスケールの選択は、シミュレートされたワークロードやカナリア クエリを使用して経験的に行われます。

ピーク時の負荷要求に合わせてクラスターをスケールアウトし、それらの追加ノードが不要になったらスケールインすることができます。 [自動スケーリング機能](hdinsight-autoscale-clusters.md)を使用すると、あらかじめ決められているメトリックとタイミングに基づいて、クラスターを自動的にスケーリングできます。 クラスターの手動スケーリングについて詳しくは、「[HDInsight クラスターのスケーリング](hdinsight-scaling-best-practices.md)」をご覧ください。

### <a name="cluster-lifecycle"></a>クラスターのライフサイクル

クラスターの有効期間に課金が発生します。 特定の時間にのみクラスターを起動して実行する必要がある場合は、[Azure Data Factory を使用してオンデマンド クラスターを作成](hdinsight-hadoop-create-linux-clusters-adf.md)できます。 また、クラスターのプロビジョニングと削除を実行する PowerShell スクリプトを作成し、[Azure Automation](https://azure.microsoft.com/services/automation/) を使用してそれらのスクリプトのスケジュールを設定することもできます。

> [!NOTE]  
> クラスターが削除されると、既定の Hive metastore も削除されます。 クラスターを次回再作成するために metastore を保持するには、Azure Database や [Apache Oozie](https://oozie.apache.org/) などの外部メタデータ ストアを使用します。
<!-- see [Using external metadata stores](hdinsight-using-external-metadata-stores.md). -->

### <a name="isolate-cluster-job-errors"></a>クラスターのジョブ エラーの分離

マルチノード クラスターで、複数の map および reduce コンポーネントの同時実行が原因でエラーが発生する場合があります。 この問題を分離するために、単一ノード クラスターで複数の同時実行ジョブを実行して分散テストをしてみます。その後、この手法を拡大して、複数のノードを含むクラスターで複数のジョブを同時に実行します。 Azure で単一ノードの HDInsight クラスターを作成するには、"*詳細*" オプションを使用します。

また、ローカル コンピューターに単一ノード開発環境をインストールし、そこでソリューションをテストすることもできます。 Hortonworks では、初期開発、概念実証、テストに役立つ、Hadoop ベースのソリューションの単一ノード ローカル開発環境を提供しています。 詳細については、[Hortonworks Sandbox](https://hortonworks.com/products/hortonworks-sandbox/) を参照してください。

単一ノードのローカル クラスターで問題を特定するには、失敗したジョブを再実行し、入力データを調整するか、小規模のデータセットを使用します。 これらのジョブを実行する方法は、プラットフォームとアプリケーションの種類によって異なります。

## <a name="quotas"></a>Quotas (クォータ)

ターゲット クラスターの VM サイズ、スケール、種類を決定したら、サブスクリプションの現在のクォータの容量制限を確認します。 クォータの上限に達すると、新しいクラスターをデプロイしたり、ワーカー ノードを追加して既存のクラスターをスケールアウトしたりといったことができなくなる可能性があります。 唯一のクォータ制限は CPU コア クォータに関するものであり、サブスクリプションごとにリージョン レベルで存在します。 たとえば、ご利用のサブスクリプションは、米国東部リージョンにおいてコア数が 30 に制限されます。 クォータの増加を要求する必要がある場合は、次の手順を行います。

1. [Azure Portal](https://portal.azure.com/) にサインインします。
1. ページの左下にある **[ヘルプとサポート]** を選択します。
1. **[新しいサポート リクエスト]** を選択します。
1. **[新しいサポート要求]** ページの **[基本]** タブで、次のオプションを選択します。
   - **問題の種類**: **サービスとサブスクリプションの制限 (クォータ)**
   - **サブスクリプション**: 使用するサブスクリプション
   - **クォータの種類**: **HDInsight**
    
     ![HDInsight コア クォータを増やすためのサポート要求を作成します。](./media/hdinsight-capacity-planning/hdinsight-quota-support-request.png)

1. **[次へ:ソリューション >>]** を選択します。
1. **[詳細]** ページで、問題に関する説明を入力し、問題の重大度、希望する連絡方法、およびその他の必須フィールドを選択します。
1. **[次へ:確認と作成 >>]** を選択します。
1. **[確認および作成]** タブで、 **[作成]** を選択します。

> [!NOTE]  
> プライベート リージョンで HDInsight コア クォータを増やす必要がある場合は、[ホワイト リストの要求を送信](https://aka.ms/canaryintwhitelist)してください。

[サポートに連絡してクォータの引き上げを要求](https://docs.microsoft.com/azure/azure-supportability/resource-manager-core-quotas-request)できます。

ただし、固定のクォータ制限もいくつかあります。たとえば、1 つの Azure サブスクリプションで使用できるコア数は最大 10,000 コアです。 これらの制限の詳細については、「[Azure サブスクリプションとサービスの制限、クォータ、制約](https://docs.microsoft.com/azure/azure-subscription-service-limits)」をご覧ください。

## <a name="next-steps"></a>次の手順

* [Apache Hadoop、Spark、Kafka などを使用して HDInsight でクラスターをセットアップする](hdinsight-hadoop-provision-linux-clusters.md): Apache Hadoop、Spark、Kafka、Interactive Hive、HBase、ML Services、または Storm を使用して HDInsight でクラスターをセットアップして構成する方法について説明します。
* [クラスター パフォーマンスを監視する](hdinsight-key-scenarios-to-monitor.md): クラスターの容量に影響を及ぼす可能性のある HDInsight クラスターを監視するための主なシナリオについて説明します。
