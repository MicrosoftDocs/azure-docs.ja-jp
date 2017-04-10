---
title: "HDInsight での Apache Kafka の概要 | Microsoft Docs"
description: "HDInsight での Apache Kafka の概要を説明します。 Apache Kafka の機能と役割について説明し、例および概要情報の入手先を紹介します。"
services: hdinsight
documentationcenter: 
author: Blackmist
manager: jhubbard
editor: cgronlun
ms.assetid: f284b6e3-5f3b-4a50-b455-917e77588069
ms.service: hdinsight
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 02/09/2017
ms.author: larryfr
translationtype: Human Translation
ms.sourcegitcommit: 2d744e753224e4ce98680d3228914fd89e87eba4
ms.openlocfilehash: 28d213fa23d480635fd4376e22ff54a5e6374350

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

## <a name="use-cases"></a>ユース ケース

* **メッセージング**: Kafka は、発行-購読のメッセージ パターンをサポートしているため、メッセージ ブローカーとしてよく使用されます。

* **アクティビティの追跡**: Kafka ではレコードの受信順序のログ記録が提供されるため、 Web サイトやアプリケーション内でのユーザーの操作などのアクティビティの追跡と再現に使用することができます。

* **集計**: ストリーム処理を使用して異なるストリームからの情報を集計し、情報をまとめて運用データに一元化することができます。

* **変換**: ストリーム処理を使用して入力された複数のトピックからのデータを結合し、1 つまたは複数の出力トピックに変換することができます。

## <a name="where-do-i-start"></a>どこから始めるか

Kafka クラスターの作成と Kafka の使用 (プロデューサー、コンシューマー、およびストリーミング API の使用に関する Java ベースの例を含む) の手順については、「[Get started with Kafka on HDInsight (HDInsight での Kafka の使用)](hdinsight-apache-kafka-get-started.md)」を参照してください。

## <a name="next-steps"></a>次のステップ

次のリンクを使用することで、HDInsight で Apache Kafka を使用する方法を知ることができます。

* [HDInsight での Kafka の使用](hdinsight-apache-kafka-get-started.md)

* [MirrorMaker を使用した HDInsight での Kafka のレプリカの作成](hdinsight-apache-kafka-mirroring.md)

* [HDInsight での Kafka に Apache Storm を使用する](hdinsight-apache-storm-with-kafka.md)

* [HDInsight での Kafka に Apache Spark を使用する](hdinsight-apache-spark-with-kafka.md)




<!--HONumber=Nov16_HO3-->


