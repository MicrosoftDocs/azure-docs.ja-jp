---
title: "Apache Kafka による高可用性 - Azure HDInsight | Microsoft Docs"
description: "Azure HDInsight 上の Apache Kafka で高可用性を確保する方法を説明します。 Kafka でパーティションのレプリカを再調整し、HDInsight が含まれている Azure リージョン内のさまざまな障害ドメインに配置されるようにする方法を説明します。"
services: hdinsight
documentationcenter: 
author: Blackmist
manager: jhubbard
editor: cgronlun
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: 
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 06/26/2017
ms.author: larryfr
ms.translationtype: Human Translation
ms.sourcegitcommit: 138f04f8e9f0a9a4f71e43e73593b03386e7e5a9
ms.openlocfilehash: f8164d1c3483b28e5f2abcc8035da78880daec1e
ms.contentlocale: ja-jp
ms.lasthandoff: 06/29/2017

---
<a id="high-availability-of-your-data-with-apache-kafka-preview-on-hdinsight" class="xliff"></a>

# HDInsight 上の Apache Kafka (プレビュー) によるデータの高可用性

基になるハードウェア ラック構成を Kafka トピックで利用できるようにパーティションのレプリカを構成する方法を説明します。 この構成により、HDInsight 上の Apache Kafka に格納されているデータの高可用性が確保されます。

<a id="fault-and-update-domains-with-kafka" class="xliff"></a>

## 障害ドメインおよび更新ドメインと Kafka

障害ドメインとは、Azure データ センター内にある基になるハードウェアの論理的なグループです。 各障害ドメインは、一般的な電源とネットワーク スイッチを共有します。 HDInsight クラスター内のノードを実装する仮想マシンと管理ディスクは、これらの障害ドメインに分散されます。 このアーキテクチャにより、物理的なハードウェア障害の潜在的な影響が制限されます。

各 Azure リージョンには、特定の数の障害ドメインがあります。 ドメインと、それに含まれる障害ドメインの数の一覧については、「[可用性セット](../virtual-machines/linux/regions-and-availability.md#availability-sets)」を参照してください。

> [!IMPORTANT]
> Kafka は、障害ドメインを認識しません。 Kafka でトピックを作成すると、パーティションのレプリカすべてが同じ障害ドメインに格納される可能性があります。 この問題を解決するために、[Kafka パーティション再調整ツール](https://github.com/hdinsight/hdinsight-kafka-tools)が用意されています。

<a id="when-to-rebalance-partition-replicas" class="xliff"></a>

## パーティションのレプリカを再調整するタイミング

Kafka データの最高レベルの可用性を確保するには、次のタイミングでトピックのパーティションのレプリカを再調整する必要があります。

* 新しいトピックまたはパーティションが作成されたとき

* クラスターをスケールアップするとき

<a id="replication-factor" class="xliff"></a>

## レプリケーション係数

> [!IMPORTANT]
> 3 つの障害ドメインを含む Azure リージョンを使用することと、レプリケーション係数として 3 を使用することをお勧めします。

障害ドメインが 2 つだけのリージョンを使用する必要がある場合は、レプリケーション係数として 4 を使用して、レプリカを 2 つの障害ドメインに均等に分散します。

トピックの作成とレプリケーション係数の設定の例については、[HDInsight 上での Kafka の開始](hdinsight-apache-kafka-get-started.md)に関するドキュメントを参照してください。

<a id="how-to-rebalance-partition-replicas" class="xliff"></a>

## パーティションのレプリカを再調整する方法

[Kafka パーティション再調整ツール](https://github.com/hdinsight/hdinsight-kafka-tools)を使用して、選択したトピックを再調整します。 このツールは、SSH セッションから Kafka クラスターのヘッド ノードに対して実行する必要があります。

SSH を使用した HDInsight への接続の詳細については、[HDInsight での SSH の使用](hdinsight-hadoop-linux-use-ssh-unix.md)に関するドキュメントを参照してください。

<a id="next-steps" class="xliff"></a>

## 次のステップ

* [HDInsight 上の Kafka の拡張性](hdinsight-apache-kafka-scalability.md)
* [HDInsight 上の Kafka でのミラーリング](hdinsight-apache-kafka-mirroring.md)
