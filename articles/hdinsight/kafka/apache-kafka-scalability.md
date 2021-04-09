---
title: Apache Kafka のスケーラビリティの向上 - Azure HDInsight
description: スケーラビリティが向上するように Azure HDInsight 上の Apache Kafka クラスター用のマネージド ディスクを構成する方法を説明します。
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 12/09/2019
ms.openlocfilehash: 9aa11be42aca59458fea0462a90b6aeb70df893d
ms.sourcegitcommit: 42e4f986ccd4090581a059969b74c461b70bcac0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/23/2021
ms.locfileid: "104863141"
---
# <a name="configure-storage-and-scalability-for-apache-kafka-on-hdinsight"></a>HDInsight 上の Apache Kafka 用に記憶域とスケーラビリティを構成する

HDInsight 上の [Apache Kafka](https://kafka.apache.org/) によって使用されるマネージド ディスクの数を構成する方法について説明します。

HDInsight 上の Kafka は、HDInsight クラスターの仮想マシンのローカル ディスクを使います。 Kafka は I/O が非常に多いため、[Azure Managed Disks](../../virtual-machines/managed-disks-overview.md) を使ってノードごとに高いスループットと多くの記憶域を提供します。 従来の仮想ハード ドライブ (VHD) を Kafka 用に使う場合は、各ノードは 1 TB に制限されます。 マネージド ディスクの場合は、複数のディスクを使ってクラスターの各ノードを 16 TB にできます。

次の図は、マネージド ディスクを使う前と使った後の HDInsight 上の Kafka を比較したものです。

:::image type="content" source="./media/apache-kafka-scalability/kafka-with-managed-disks-architecture.png" alt-text="Kafka とマネージド ディスクのアーキテクチャ" border="false":::

## <a name="configure-managed-disks-azure-portal"></a>マネージド ディスクを構成する: Azure Portal

1. 「[HDInsight クラスターの作成](../hdinsight-hadoop-create-linux-clusters-portal.md)」の手順に従って、Portal を使ってクラスターを作成する一般的な手順を理解します。 ポータルの作成プロセスを完了しないでください。

2. [**構成と価格**] セクションで、__[ノード数]__ フィールドを使用して、ディスクの数を構成します。

    > [!NOTE]  
    > マネージド ディスクの種類は、__Standard__ (HDD) または __Premium__ (SSD) です。 Premium ディスクは、DS および GS シリーズの VM で使われます。 他の種類の VM はすべて Standard を使います。

    :::image type="content" source="./media/apache-kafka-scalability/azure-portal-cluster-configuration-pricing-kafka-disks.png" alt-text="ワーカー ノードごとのディスク数が強調表示されている [クラスター サイズ] セクション" border="true":::

## <a name="configure-managed-disks-resource-manager-template"></a>マネージド ディスクを構成する: Resource Manager テンプレート

Kafka クラスターのワーカー ノードによって使われるディスクの数を制御するには、テンプレートの次のセクションを使います。

```json
"dataDisksGroups": [
    {
        "disksPerNode": "[variables('disksPerWorkerNode')]"
    }
    ],
```

[https://hditutorialdata.blob.core.windows.net/armtemplates/create-linux-based-kafka-mirror-cluster-in-vnet-v2.1.json](https://hditutorialdata.blob.core.windows.net/armtemplates/create-linux-based-kafka-mirror-cluster-in-vnet-v2.1.json) に、マネージド ディスクを構成する方法を示す完全なテンプレートがあります。

## <a name="next-steps"></a>次のステップ

HDInsight 上の Apache Kafka の操作の詳細については、次のドキュメントを参照してください。

* [MirrorMaker を使用して HDInsight 上の Apache Kafka のレプリカを作成する](apache-kafka-mirroring.md)
* [HDInsight 上の Apache Kafka で Apache Storm を使用する](../hdinsight-apache-storm-with-kafka.md)
* [HDInsight 上の Apache Kafka で Apache Spark を使用する](../hdinsight-apache-spark-with-kafka.md)
* [Azure Virtual Network 経由で Apache Kafka に接続する](apache-kafka-connect-vpn-gateway.md)

* [Apache Kafka でのマネージド ディスクに関する HDInsight ブログ](https://azure.microsoft.com/blog/announcing-public-preview-of-apache-kafka-on-hdinsight-with-azure-managed-disks/)
