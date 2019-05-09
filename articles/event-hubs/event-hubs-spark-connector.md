---
title: Apache Spark との統合 - Azure Event Hubs | Microsoft Docs
description: Apache Spark と統合して、Event Hubs での構造化ストリームを有効にします
services: event-hubs
documentationcenter: na
author: ShubhaVijayasarathy
manager: timlt
editor: ''
ms.service: event-hubs
ms.devlang: java
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.custom: seodec18
ms.date: 12/06/2018
ms.author: shvija
ms.openlocfilehash: 605669a740663040ab7a167bf266fe1940123afc
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/07/2019
ms.locfileid: "57551876"
---
# <a name="integrating-apache-spark-with-azure-event-hubs"></a>Apache Spark と Event Hubs の統合

Azure Event Hubs と [Apache Spark](https://spark.apache.org/) をシームレスに統合すると、分散ストリーミング のアプリケーションを構築できます。 この統合では、[Spark Core](https://spark.apache.org/docs/latest/rdd-programming-guide.html)、[Spark Streaming](https://spark.apache.org/docs/latest/streaming-programming-guide.html)、[構造化ストリーミング](https://spark.apache.org/docs/latest/structured-streaming-programming-guide.html) がサポートされています。 Apache Spark 用の Event Hubs コネクタは [GitHub](https://github.com/Azure/azure-event-hubs-spark) で入手できます。 このライブラリは、[Maven Central Repository](https://search.maven.org/#artifactdetails%7Ccom.microsoft.azure%7Cazure-eventhubs-spark_2.11%7C2.1.6%7C) の Maven プロジェクトで使用することもできます

この記事には、[Azure Databrick](https://azure.microsoft.com/services/databricks/) で継続的なアプリケーションを作成する方法について記載します。 この記事では Azure Databricks を使用しますが、Spark クラスターも [HDInsight](../hdinsight/spark/apache-spark-overview.md) で使用できます。

この記事の例では、2 つの Scala ノートブックを使用します。1 つはイベント ハブからのイベントのストリーミング、もう 1 つはイベント ハブへのイベントの返送に使用します。

## <a name="prerequisites"></a>前提条件

* Azure サブスクリプション。 ない場合は、[無料アカウントを作成](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) します。
* Event Hubs インスタンス。 ない場合は、[作成](event-hubs-create.md) します。
* [Azure Databricks](https://azure.microsoft.com/services/databricks/) インスタンス。 ない場合は、[作成](../azure-databricks/quickstart-create-databricks-workspace-portal.md) します。
* [Maven コーディネートを使用してライブラリを作成 ](https://docs.databricks.com/user-guide/libraries.html#upload-a-maven-package-or-spark-package): `com.microsoft.azure:azure‐eventhubs‐spark_2.11:2.3.1`。

次のコードを使用し、イベント ハブからイベントをストリーム配信します。

```scala
import org.apache.spark.eventhubs._

// To connect to an event hub, EntityPath is required as part of the connection string.
// Here, we assume that the connection string from the Azure portal does not have the EntityPath part.
val connectionString = ConnectionStringBuilder("{EVENT HUB CONNECTION STRING FROM AZURE PORTAL}")
  .setEventHubName("{EVENT HUB NAME}")
  .build 
val ehConf = EventHubsConf(connectionString)
  .setStartingPosition(EventPosition.fromEndOfStream)

// Create a stream that reads data from the specified Event Hub.
val reader = spark.readStream
  .format("eventhubs")
  .options(ehConf.toMap)
  .load()
val eventhubs = reader.select($"body" cast "string")

eventhubs.writeStream
  .format("console")
  .outputMode("append")
  .start()
  .awaitTermination()
```
次のサンプル コードは、Spark バッチ API を使用してイベントハブにイベントを送信します。 ストリーミング クエリを書き込んで、イベントをイベント ハブに送信することもできます。

```scala
import org.apache.spark.eventhubs._
import org.apache.spark.sql.functions._

// To connect to an event hub, EntityPath is required as part of the connection string.
// Here, we assume that the connection string from the Azure portal does not have the EntityPath part.
val connectionString = ConnectionStringBuilder("{EVENT HUB CONNECTION STRING FROM AZURE PORTAL}")
  .setEventHubName("{EVENT HUB NAME}")
  .build
val ehConf = EventHubsConf(connectionString)

// Create random strings as the body of the message.
val bodyColumn = concat(lit("random nunmber: "), rand()).as("body")

// Write 200 rows to the specified event hub.
val df = spark.range(200).select(bodyColumn)
df.write
  .format("eventhubs")
  .options(ehConf.toMap)
  .save() 
```

## <a name="next-steps"></a>次の手順

これで、Apache Spark の Event Hubs Connector を利用し、故障に耐え、拡張可能なストリームを設定する方法がわかりました。 構造化ストリームと Spark ストリームと共に Event Hubs を使用する方法については、次のリンクで学習できます。

* [構造化ストリーミングおよび Azure Event Hubs 統合ガイド](https://github.com/Azure/azure-event-hubs-spark/blob/master/docs/structured-streaming-eventhubs-integration.md)
* [Spark Streaming および Event Hubs 統合ガイド](https://github.com/Azure/azure-event-hubs-spark/blob/master/docs/spark-streaming-eventhubs-integration.md)
