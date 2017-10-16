---
title: "HDInsight での Apache Kafka の概要 - Azure | Microsoft Docs"
description: "HDInsight での Apache Kafka について説明します。その機能と役割について説明し、例と概要情報の入手先を紹介します。"
services: hdinsight
documentationcenter: 
author: Blackmist
manager: jhubbard
editor: cgronlun
ms.assetid: f284b6e3-5f3b-4a50-b455-917e77588069
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 09/07/2017
ms.author: larryfr
ms.openlocfilehash: 39234ca792983178cfd4304e001271ea30e28ae6
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="introducing-apache-kafka-on-hdinsight-preview"></a>HDInsight での Apache Kafka の概要 (プレビュー)

[Apache Kafka](https://kafka.apache.org) はオープン ソースの分散ストリーム プラットフォームで、リアルタイムのストリーミング データ パイプラインとアプリケーションの構築に使用できます。 Kafka は、名前付きデータ ストリームへの公開および購読ができる、メッセージ キューと同様のメッセージ ブローカー機能も提供しています。 HDInsight での Kafka の使用により、管理された、拡張性の高い、高可用性のサービスが Microsoft Azure クラウドで提供されます。

## <a name="why-use-kafka-on-hdinsight"></a>HDInsight で Apache Kafka を使用する理由

Kafka には、次の機能が用意されています。

* 発行-購読のメッセージング パターン: Kafka は、Kafka トピックにレコードを発行するためのプロデューサー API を提供します。 コンシューマー API は、トピックの購読に使用されます。

* ストリーム処理: リアルタイムのストリーム処理には、通常、Kafka と共に Apache Storm または Spark が使用されます。 Kafka 0.10.0.0 (HDInsight バージョン 3.5) では、Storm や Spark を必要とせずに、ストリーミング ソリューションを構築できるストリーミング API が導入されました。

* 水平スケール: Kafka は、HDInsight クラスター内のノードにわたってストリームを分割します。 コンシューマー プロセスを各パーティションと関連付けることにより、レコード使用時の負荷分散を実現することができます。

* 順番通りに処理: 各パーティション内で、レコードは、受信された順番にストリームに格納されます。 パーティションごとに 1 つのコンシューマー プロセスを関連付けることで、レコードが順番通りに確実に処理されるようになります。

* フォールト トレラント: パーティションをノード間でレプリケートできるため、フォールト トレランスを提供します。

* Azure Managed Disks との統合: Managed Disks は、HDInsight クラスターの仮想マシンで使われるディスクのスケーリングとスループットを高める機能を提供します。

    HDInsight 上の Kafka では Managed Disks が既定で有効になります。 ノードごとに使われるディスクの数は HDInsight の作成時に構成できます。 Managed Disks について詳しくは、「[Azure Managed Disks の概要](../virtual-machines/windows/managed-disks-overview.md)」をご覧ください。

    HDInsight 上の Kafka での Managed Disks の構成について詳しくは、「[HDInsight 上の Apache Kafka 用に記憶域とスケーラビリティを構成する](hdinsight-apache-kafka-scalability.md)」をご覧ください。

## <a name="use-cases"></a>ユース ケース

* **メッセージング**: Kafka は発行-購読のメッセージ パターンをサポートするため、メッセージ ブローカーとしてよく使用されます。

* **アクティビティの追跡**: Kafka ではレコードの受信順序のログ記録が提供されるため、アクティビティの追跡と再現に使用することができます。 たとえば、Web サイト上またはアプリケーション内のユーザー アクションです。

* **集計**: ストリーム処理を使用して異なるストリームからの情報を集計し、情報をまとめて運用データに一元化することができます。

* **変換**: ストリーム処理を使用して入力された複数のトピックからのデータを結合し、1 つまたは複数の出力トピックに変換することができます。

## <a name="architecture"></a>アーキテクチャ

![Kafka クラスターの構成](./media/hdinsight-apache-kafka-introduction/kafka-cluster.png)

この図は、コンシューマー グループ、パーティション分割、レプリケーションを使ってイベントの並列読み取りとフォールト トレランスを実現する標準的な Kafka の構成を示しています。 Apache ZooKeeper は、Kafka クラスターの状態管理におけるトランザクションの同時実行性、回復性、短い待ち時間の実現を目指して構築されています。 Kafka では、"*トピック*" 内にレコードが格納されます。 レコードは、*プロデューサー*によって生成され、*コンシューマー*によって消費されます。 プロデューサーは、Kafka の*ブローカー*からレコードを取得します。 HDInsight クラスターの各ワーカー ノードが、Kafka のブローカーです。 コンシューマーごとに 1 つのパーティションを作成することで、ストリーミング データの並列処理が可能となっています。 レプリケーションによって複数ノードにパーティションが分散されて、ノード (ブローカー) の機能不全に対する保護措置が講じられています。 *(L)* で示されたパーティションは、各パーティションのリーダーです。 プロデューサー トラフィックは、ZooKeeper によって管理された状態に基づいて、各ノードのリーダーにルーティングされます。

> [!IMPORTANT]
> 基になる Azure データ センターのハードウェア (ラック) は、Kafka では認識されません。 基になるハードウェア間でパーティションを正しく調整する方法については、[データの高可用性構成 (Kafka)](hdinsight-apache-kafka-high-availability.md) に関するページを参照してください。

## <a name="next-steps"></a>次のステップ

次のリンクを使用することで、HDInsight で Apache Kafka を使用する方法を知ることができます。

* [HDInsight での Kafka の使用](hdinsight-apache-kafka-get-started.md)

* [MirrorMaker を使用した HDInsight での Kafka のレプリカの作成](hdinsight-apache-kafka-mirroring.md)

* [HDInsight での Kafka に Apache Storm を使用する](hdinsight-apache-storm-with-kafka.md)

* [HDInsight での Kafka に Apache Spark を使用する](hdinsight-apache-spark-with-kafka.md)

* [Azure 仮想ネットワーク経由で Kafka に接続する](hdinsight-apache-kafka-connect-vpn-gateway.md)