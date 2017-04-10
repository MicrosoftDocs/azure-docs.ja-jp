---
title: "HDInsight 上の Apache Storm を使用した車両センサー データの処理 | Microsoft Docs"
description: "HDInsight 上の Apache Storm を使用して Event Hubs からの車両センサー データを処理する方法について説明します。 DocumentDB からモデルのデータを追加し、ストレージに出力を格納します。"
services: hdinsight,documentdb,notification-hubs
documentationcenter: 
author: Blackmist
manager: jhubbard
editor: cgronlun
ms.assetid: 78980635-8bef-4c33-96c3-fae50e932e31
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: java
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 02/09/2017
ms.author: larryfr
translationtype: Human Translation
ms.sourcegitcommit: 46bc5b3b70120cd631523fd2b27ad8b9a47e3c6d
ms.openlocfilehash: 952480e71dac19c7772198516863b3e64be1a6b3
ms.lasthandoff: 11/17/2016


---
# <a name="process-vehicle-sensor-data-from-azure-event-hubs-using-apache-storm-on-hdinsight"></a>HDInsight 上の Apache Storm を使用した Azure Event Hubs からの車両センサー データの処理

HDInsight 上の Apache Storm を使用して Azure Event Hubs からの車両センサー データを処理する方法について説明します。 この例では Azure Event Hubs からセンサー データを読み取り、Azure DocumentDB に格納されたデータを参照してデータを強化し、最後に Hadoop ファイル システム (HDFS) を使用して Azure Storage にデータを格納します。

![HDInsight and the Internet of Things (IoT) architecture diagram](./media/hdinsight-storm-iot-eventhub-documentdb/iot.png)

## <a name="overview"></a>概要

車両にセンサーを追加すると、履歴データの傾向に基づいて機器の問題を予測できるだけでなく、使用状況のパターン分析に基づいてその後のバージョンを改良できます。 この分析には従来の MapReduce バッチ処理を使用できますが、MapReduce 処理を行うためには、その前にすべての車両のデータを Hadoop に迅速かつ効率的に読み込めなければなりません。 また、重大なエラー パス (エンジン温度、ブレーキなど) に関してリアルタイムで分析を行いたい場合もあります。

Azure Event Hubs はセンサーによって生成された大量のデータを処理するためにビルドされていて、HDInsight 上で Apache Storm を使用してそれらのデータを読み込んで処理してから、(Azure Storage によって戻された) データを HDFS に格納してさらに MapReduce 処理を行えます。

## <a name="solution"></a>解決策

エンジン温度、大気温度、車両速度のテレメトリ データがセンサーによって記録され、車両識別番号 (VIN) とタイムスタンプとともに Event Hubs に送信されます。 そこから、HDInsight クラスター上の Apache Storm で実行されている Storm トポロジによってデータが読み取られ、処理され、HDFS に格納されます。

処理の際、VIN を使用して Azure DocumentDB からモデル情報が取得されます。 この情報がデータに追加されてから、格納されます。

Storm トポロジでは、次のコンポーネントが使用されます。

* **EventHubSpout** - Azure Event Hubs からデータを読み取ります。
* **TypeConversionBolt** - Event Hubs の JSON 文字列を、エンジン温度、大気温度、速度、VIN、タイムスタンプそれぞれのデータ値が含まれるの個々のデータ値を含むタプルに変換します。
* **DataReferencBolt** - VIN を使用して DocumentDB から車両モデルを検索します。
* **WasbStoreBolt** - HDFS (Azure Storage) にデータを格納します。

このソリューションのダイアグラムを次に示します。

![Storm トポロジ](./media/hdinsight-storm-iot-eventhub-documentdb/iottopology.png)

> [!NOTE]
> これは簡略化したダイアグラムで、ソリューション内の各コンポーネントには複数のインスタンスを含めることができます。 たとえば、トポロジ内にある各コンポーネントの複数のインスタンスを、HDInsight クラスター上の Storm にある複数のノードに分散できます。
> 
> 

## <a name="implementation"></a>実装

このシナリオの完全に自動化されたソリューションは、GitHub の [HDInsight-Storm-Examples](https://github.com/hdinsight/hdinsight-storm-examples) リポジトリの一部として入手可能です。 この例を使用するには、 [IoTExample README.MD](https://github.com/hdinsight/hdinsight-storm-examples/blob/master/IotExample/README.md)に記されている手順に従ってください。

## <a name="next-steps"></a>次のステップ

Storm トポロジ例をさらにご覧になる場合、「 [HDInsight での Storm トポロジの例](hdinsight-storm-example-topology.md)」を参照してください。


