---
title: Azure HDInsight のクラスターの容量計画
description: Azure HDInsight クラスターの容量とパフォーマンスの計画に関する重要な質問を特定します。
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 05/07/2020
ms.openlocfilehash: 8e76f767470b9052b25cd2b2958f3f9e9780881b
ms.sourcegitcommit: 958f086136f10903c44c92463845b9f3a6a5275f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/20/2020
ms.locfileid: "83714748"
---
# <a name="capacity-planning-for-hdinsight-clusters"></a>HDInsight クラスターの容量計画

HDInsight クラスターをデプロイする前に、必要なパフォーマンスとスケールを判定することによって目的のクラスター容量を計画します。 この計画により、使いやすさとコストの両方を最適化できます。 クラスターの容量に関する決定事項の中には、デプロイ後に変更できないものもあります。 パフォーマンス パラメーターが変わった場合は、保存されているデータを失うことなく、クラスターを解体して再作成できます。

容量計画における重要な検討事項は次のとおりです。

* クラスターをどの地理的リージョンにデプロイすればよいか。
* どれくらいのストレージ容量が必要か。
* どのような種類のクラスターをデプロイすればよいか。
* クラスター ノードでは、どのサイズおよび種類の仮想マシン (VM) を使用すればよいか。
* クラスターにはワーカー ノードがいくつ必要か。

## <a name="choose-an-azure-region"></a>Azure リージョンの選択

Azure リージョンによって、クラスターを物理的にプロビジョニングする場所が決まります。 読み取りと書き込みの待機時間を最小限に抑えるには、クラスターをデータの近くに配置する必要があります。

HDInsight は多数の Azure リージョンで利用できます。 最も近いリージョンを確認するには、「[リージョン別の利用可能な製品](https://azure.microsoft.com/global-infrastructure/services/?products=hdinsight)」を参照してください。

## <a name="choose-storage-location-and-size"></a>ストレージの場所とサイズの選択

### <a name="location-of-default-storage"></a>既定のストレージの場所

既定のストレージ (Azure ストレージ アカウントまたは Azure Data Lake Storage) は、クラスターと同じ場所に存在する必要があります。 Azure Storage はあらゆる場所で利用できます。 Data Lake Storage Gen1 は一部のリージョンで利用できます。[Data Lake Storage の現在の提供状況](https://azure.microsoft.com/global-infrastructure/services/?products=storage)を参照してください。

### <a name="location-of-existing-data"></a>既存のデータの場所

既存のストレージ アカウントまたは Data Lake Storage をクラスターの既定のストレージとして使用する場合は、その同じ場所にクラスターをデプロイする必要があります。

### <a name="storage-size"></a>ストレージ サイズ

デプロイされたクラスターで、追加の Azure Storage アカウントを接続したり、他の Data Lake Storage にアクセスしたりできます。 すべてのストレージ アカウントがクラスターと同じ場所に存在する必要があります。 Data Lake Storage は別の場所にあってもかまいませんが、その距離が長いと、ある程度の待ち時間が発生する可能性があります。

Azure Storage には何らかの[容量制限](../azure-resource-manager/management/azure-subscription-service-limits.md#storage-limits)がありますが、Data Lake Storage Gen1 はほぼ無制限です。

クラスターは、さまざまなストレージ アカウントの組み合わせにアクセスできます。 一般的な例を次に示します。

* データ量が、1 つの Blob Storage コンテナーのストレージ容量を超える可能性がある場合。
* BLOB コンテナーへのアクセス レートがしきい値を超え、調整が発生する可能性がある場合。
* データを作成する必要があるときに、クラスターで利用できる BLOB コンテナーに既にアップロードしている場合。
* セキュリティ上の理由からストレージのさまざまな部分を分離する場合、または管理を簡素化する場合。

パフォーマンスを向上させるために、ストレージ アカウントごとにコンテナーを 1 つだけ使用します。

## <a name="choose-a-cluster-type"></a>クラスターの種類の選択

クラスターの種類によって、HDInsight クラスターが実行するように構成されるワークロードが決定されます。 種類には、[Apache Hadoop](./hadoop/apache-hadoop-introduction.md)、[Apache Storm](./storm/apache-storm-overview.md)、[Apache Kafka](./kafka/apache-kafka-introduction.md)、または [Apache Spark](./spark/apache-spark-overview.md) が含まれます。 使用できるクラスターの種類の詳細については、[Azure HDInsight の概要](hdinsight-overview.md#cluster-types-in-hdinsight)に関するページを参照してください。 クラスターの種類ごとに、サイズとノード数の要件を含む特定のデプロイ トポロジがあります。

## <a name="choose-the-vm-size-and-type"></a>VM のサイズと種類の選択

各クラスターの種類には一連のノード タイプがあり、各ノード タイプには VM のサイズと種類の固有のオプションがあります。

アプリケーションに最適なクラスター サイズを決定するために、クラスターの容量のベンチマークを実行し、その結果に従ってサイズを増やすことができます。 たとえば、シミュレートされたワークロードや "*カナリア クエリ*" をご利用いただけます。 シミュレートされたワークロードをさまざまなサイズのクラスターで実行します。 目的のパフォーマンスに到達するまで、サイズを徐々に増やします。 他の実稼働クエリの間にカナリア クエリを定期的に挿入することで、クラスターに十分なリソースがあるかどうかを示すことができます。

ワークロードのための適切な VM ファミリを選択する方法の詳細については、[クラスターの適切な VM サイズの選択](hdinsight-selecting-vm-size.md)に関するページを参照してください。

## <a name="choose-the-cluster-scale"></a>クラスターのスケールの選択

クラスターのスケールは、VM ノードの数によって決まります。 どのクラスターの種類にも、特定のスケールのノード タイプと、スケールアウトをサポートするノード タイプがあります。たとえば、クラスターで 3 つの [Apache ZooKeeper](https://zookeeper.apache.org/) ノードまたは 2 つのヘッド ノードが必要な場合があります。 分散方式でデータ処理を実行するワーカー ノードは、追加のワーカー ノードのメリットが得られます。

クラスターの種類によっては、ワーカー ノードの数を増やすと、コンピューティング容量が追加されます (コア数の増加など)。 ノードの数が増加すると、処理されるデータのインメモリ ストレージをサポートするためにクラスター全体に必要なメモリの合計が増えます。 VM のサイズと種類の選択と同様に、クラスターの適切なスケールの選択も、通常は経験的に実現されます。 シミュレートされたワークロードまたはカナリア クエリを使用します。

ピーク時の負荷要求を満たすようにクラスターをスケールアウトできます。 その後、これらの追加のノードが必要なくなったら、再びスケールダウンします。 [自動スケーリング機能](hdinsight-autoscale-clusters.md)を使用すると、事前に定義されたメトリックとタイミングに基づいて、クラスターを自動的にスケーリングできます。 クラスターの手動スケーリングについて詳しくは、「[HDInsight クラスターのスケーリング](hdinsight-scaling-best-practices.md)」をご覧ください。

### <a name="cluster-lifecycle"></a>クラスターのライフサイクル

ユーザーは、クラスターの有効期間に対して課金されます。 特定の時間だけクラスターが必要な場合は、[Azure Data Factory を使用してオンデマンド クラスターを作成](hdinsight-hadoop-create-linux-clusters-adf.md)します。 また、クラスターのプロビジョニングと削除を実行する PowerShell スクリプトを作成し、[Azure Automation](https://azure.microsoft.com/services/automation/) を使用してそれらのスクリプトのスケジュールを設定することもできます。

> [!NOTE]  
> クラスターが削除されると、既定の Hive metastore も削除されます。 クラスターを次回再作成するために metastore を保持するには、Azure Database や [Apache Oozie](https://oozie.apache.org/) などの外部メタデータ ストアを使用します。

### <a name="isolate-cluster-job-errors"></a>クラスターのジョブ エラーの分離

マルチノード クラスターでの複数の map および reduce コンポーネントの並列実行のためにエラーが発生する場合があります。 この問題を分離しやすくするために、分散テストを試してください。 1 つのワーカー ノード クラスターで複数の同時実行ジョブを実行します。 次に、このアプローチを拡張して、複数のノードを含むクラスターで複数のジョブを同時に実行します。 Azure で単一ノードの HDInsight クラスターを作成するには、 *`Custom(size, settings, apps)`* オプションを使用し、ポータルで新しいクラスターをプロビジョニングするときに **[クラスター サイズ]** セクションの *[Number of Worker nodes] (ワーカー ノードの数)* として 1 の値を使用します。

## <a name="quotas"></a>Quotas (クォータ)

サブスクリプション クォータの管理の詳細については、[クォータの増加要求](quota-increase-request.md)に関するページを参照してください。

## <a name="next-steps"></a>次のステップ

* [Apache Hadoop、Spark、Kafka などを使用して HDInsight でクラスターをセットアップする](hdinsight-hadoop-provision-linux-clusters.md): HDInsight でクラスターをセットアップして構成する方法について説明します。
* [クラスター パフォーマンスを監視する](hdinsight-key-scenarios-to-monitor.md): クラスターの容量に影響を及ぼす可能性のある HDInsight クラスターを監視するための主なシナリオについて説明します。
