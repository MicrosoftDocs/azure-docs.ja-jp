---
title: NoClassDefFoundError - Azure HDInsight での Apache Kafka データを持つ Apache Spark
description: Apache Kafka クラスターからデータを読み取る Apache Spark ストリーミング ジョブが Azure HDInsight の NoClassDefFoundError で失敗する
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 07/29/2019
ms.openlocfilehash: 4d00cbcb0151da39feb0cb015660291af544d7f4
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "98946371"
---
# <a name="apache-spark-streaming-job-that-reads-apache-kafka-data-fails-with-noclassdeffounderror-in-hdinsight"></a>Apache Kafka データを読み取る Apache Spark ストリーミング ジョブが HDInsight の NoClassDefFoundError で失敗する

この記事では、Azure HDInsight クラスターで Apache Spark コンポーネントを使用するときのトラブルシューティングの手順と考えられる解決策について説明します。

## <a name="issue"></a>問題

Apache Spark クラスターでは、Apache Kafka クラスターからデータを読み取る Spark ストリーミング ジョブが実行されます。 Kafka ストリーム圧縮が有効になっている場合、Spark ストリーミング ジョブは失敗します。 この場合、次のエラーが原因で、Spark ストリーミング Yarn アプリ application_1525986016285_0193 が失敗します。

```
18/05/17 20:01:33 WARN YarnAllocator: Container marked as failed: container_e25_1525986016285_0193_01_000032 on host: wn87-Scaled.2ajnsmlgqdsutaqydyzfzii3le.cx.internal.cloudapp.net. Exit status: 50. Diagnostics: Exception from container-launch.
Container id: container_e25_1525986016285_0193_01_000032
Exit code: 50
Stack trace: ExitCodeException exitCode=50: 
 at org.apache.hadoop.util.Shell.runCommand(Shell.java:944)
```

## <a name="cause"></a>原因

このエラーは、実行している Kafka クラスターのバージョンとは異なる `spark-streaming-kafka` jar ファイルのバージョンを指定することによって、発生する場合があります。

たとえば、Kafka クラスター バージョン 0.10.1 を実行している場合、次のコマンドを実行するとエラーが発生します。

```
spark-submit \
--packages org.apache.spark:spark-streaming-kafka-0-8_2.11:2.2.0
--conf spark.executor.instances=16 \
...
~/Kafka_Spark_SQL.py <bootstrap server details>
```

## <a name="resolution"></a>解決方法

`–packages` オプションを指定して Spark-submit コマンドを使用し、spark-streaming-kafka jar ファイルのバージョンが、実行している Kafka クラスターのバージョンと同じであることを確認します。

## <a name="next-steps"></a>次のステップ

[!INCLUDE [troubleshooting next steps](../../../includes/hdinsight-troubleshooting-next-steps.md)]