---
title: Apache Hadoop のコンポーネントとバージョン - Azure HDInsight 3.6
description: Azure HDInsight 3.6 での Apache Hadoop のコンポーネントとバージョンについて説明します。
ms.service: hdinsight
ms.topic: conceptual
author: deshriva
ms.author: deshriva
ms.date: 02/08/2021
ms.openlocfilehash: 0180f94109d01443390da363d9e09c5ad0b26d18
ms.sourcegitcommit: 4bda786435578ec7d6d94c72ca8642ce47ac628a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/16/2021
ms.locfileid: "103496118"
---
# <a name="hdinsight-36-component-versions"></a>HDInsight 3.6 のコンポーネントのバージョン

この記事では、Azure HDInsight 3.6 での Apache Hadoop 環境のコンポーネントとバージョンについて説明します。

## <a name="support-for-hdinsight-36"></a>HDInsight 3.6 のサポート

次の表は、HDInsight 3.6 クラスターの種類のサポート期間の一覧です。

| クラスターの種類                    | Framework のバージョン | 現在のサポート有効期限        | 新しいサポート有効期限 |
|---------------------------------|-------------------|-----------------------------------|-----------------------------|
| HDInsight 3.6 Hadoop            | 2.7.3             | 2020 年 12 月 31 日                      | 2021 年 6 月 30 日               |
| HDInsight 3.6 Spark             | 2.3               | 2020 年 12 月 31 日                      | 2021 年 6 月 30 日               |
| HDInsight 3.6 Spark             | 2.2               | 2020 年 6 月 30 日に廃止          |                             |
| HDInsight 3.6 Spark             | 2.1               | 2020 年 6 月 30 日に廃止          |                             |
| HDInsight 3.6 Kafka             | 1.1               | 2020 年 12 月 31 日                      | 2021 年 6 月 30 日               |
| HDInsight 3.6 Kafka             | 1.0               | 2020 年 6 月 30 日に廃止         |                             |
| HDInsight 3.6 HBase             | 1.1               | 2020 年 12 月 31 日                      | 2021 年 6 月 30 日               |
| HDInsight 3.6 Interactive Query | 2.1               | 2020 年 12 月 31 日                      | 2021 年 6 月 30 日               |
| HDInsight 3.6 Storm             | 1.1               | 2020 年 12 月 31 日                      | 2021 年 6 月 30 日               |
| HDInsight 3.6  ML Services      | 9.3               | 2020 年 12 月 31 日                      | 2020 年 12 月 31 日                |
## <a name="apache-components-available-with-hdinsight-version-36"></a>HDInsight バージョン 3.6 で使用できる Apache のコンポーネント

HDInsight 3.6 に関連付けられている OSS コンポーネントのバージョンを、次の表にまとめます。

| コンポーネント              | HDInsight 3.6 (既定)     |
|------------------------|-----------------------------|
| Apache Hadoop と YARN | 2.7.3                       |
| Apache Tez             | 0.7.0                       |
| Apache Pig             | 0.16.0                      |
| Apache Hive            | (ESP Interactive Query では 2.1.0) |
| Apache Tez Hive2       | 0.8.4                       |
| Apache Ranger          | 0.7.0                       |
| Apache HBase           | 1.1.2                       |
| Apache Sqoop           | 1.4.6                       |
| Apache Oozie           | 4.2.0                       |
| Apache Zookeeper       | 3.4.6                       |
| Apache Storm           | 1.1.0                       |
| Apache Mahout          | 0.9.0+                      |
| Apache Phoenix         | 4.7.0                       |
| Apache Spark           | 2.3.2.                      |
| Apache Livy            | 0.4.                        |
| Apache Kafka           | 1.1                         |
| Apache Ambari          | 2.6.0                       |
| Apache Zeppelin        | 0.7.3                       |
| Mono                   | 4.2.1                       |

## <a name="next-steps"></a>次のステップ

- [HDInsight で Apache Hadoop、Spark、その他のクラスターをセットアップする](hdinsight-hadoop-provision-linux-clusters.md)
- [Enterprise セキュリティ パッケージ](./enterprise-security-package.md)
- [Windows PC から HDInsight の Apache Hadoop で作業する](hdinsight-hadoop-windows-tools.md)