---
title: Apache Kafka による高可用性 - Azure HDInsight
description: Azure HDInsight 上の Apache Kafka で高可用性を確保する方法を説明します。 Kafka でパーティションのレプリカを再調整し、HDInsight が含まれている Azure リージョン内のさまざまな障害ドメインに配置されるようにする方法を説明します。
services: hdinsight
ms.service: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 05/01/2018
ms.openlocfilehash: 3d048618fef04b630366b3f575e420b329f102cb
ms.sourcegitcommit: 4eeeb520acf8b2419bcc73d8fcc81a075b81663a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/19/2018
ms.locfileid: "53597666"
---
# <a name="high-availability-of-your-data-with-apache-kafka-on-hdinsight"></a>HDInsight 上の Apache Kafka によるデータの高可用性

基になるハードウェア ラック構成を使用して Apache Kafka トピックのパーティションのレプリカを構成する方法を説明します。 この構成により、HDInsight 上の Apache Kafka に格納されているデータの高可用性が確保されます。

## <a name="fault-and-update-domains-with-apache-kafka"></a>Apache Kafka の障害ドメインおよび更新ドメイン

障害ドメインとは、Azure データ センター内にある基になるハードウェアの論理的なグループです。 各障害ドメインは、一般的な電源とネットワーク スイッチを共有します。 HDInsight クラスター内のノードを実装する仮想マシンと管理ディスクは、これらの障害ドメインに分散されます。 このアーキテクチャにより、物理的なハードウェア障害の潜在的な影響が制限されます。

各 Azure リージョンには、特定の数の障害ドメインがあります。 ドメインと、それに含まれる障害ドメインの数の一覧については、「[可用性セット](../../virtual-machines/windows/regions-and-availability.md#availability-sets)」を参照してください。

> [!IMPORTANT]  
> Kafka は、障害ドメインを認識しません。 Kafka でトピックを作成すると、パーティションのレプリカすべてが同じ障害ドメインに格納される可能性があります。 この問題を解決するために、HDInsight には [Kafka パーティション再調整ツール](https://github.com/hdinsight/hdinsight-kafka-tools)が用意されています。

## <a name="when-to-rebalance-partition-replicas"></a>パーティションのレプリカを再調整するタイミング

Kafka データの最高レベルの可用性を確保するには、次のタイミングでトピックのパーティションのレプリカを再調整する必要があります。

* 新しいトピックまたはパーティションが作成されたとき

* クラスターをスケールアップするとき

## <a name="replication-factor"></a>レプリケーション係数

> [!IMPORTANT]  
> 3 つの障害ドメインを含む Azure リージョンを使用することと、レプリケーション係数として 3 を使用することをお勧めします。

障害ドメインが 2 つだけのリージョンを使用する必要がある場合は、レプリケーション係数として 4 を使用して、レプリカを 2 つの障害ドメインに均等に分散します。

トピックの作成とレプリケーション係数の設定の例については、[Apache HDInsight 上での Kafka の開始](apache-kafka-get-started.md)に関するドキュメントを参照してください。

## <a name="how-to-rebalance-partition-replicas"></a>パーティションのレプリカを再調整する方法

[Apache Kafka パーティション再調整ツール](https://github.com/hdinsight/hdinsight-kafka-tools)を使用して、選択したトピックを再調整します。 このツールは、SSH セッションから Kafka クラスターのヘッド ノードに対して実行する必要があります。

SSH を使用した HDInsight への接続の詳細については、[HDInsight での SSH の使用](../hdinsight-hadoop-linux-use-ssh-unix.md)に関するドキュメントを参照してください。

## <a name="next-steps"></a>次の手順

* [HDInsight 上の Apache Kafka の拡張性](apache-kafka-scalability.md)
* [HDInsight 上の Apache Kafka でのミラーリング](apache-kafka-mirroring.md)
