---
title: Apache Hadoop のコンポーネントとバージョン - Azure HDInsight 4.0
description: Azure HDInsight 4.0 での Apache Hadoop のコンポーネントとバージョンについて説明します。
ms.service: hdinsight
ms.topic: conceptual
author: deshriva
ms.author: deshriva
ms.date: 02/08/2021
ms.openlocfilehash: 0283779e10ac3e6070e9fd73d186af35972cad22
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "104582886"
---
# <a name="hdinsight-40-component-versions"></a>HDInsight 4.0 のコンポーネントのバージョン

この記事では、Azure HDInsight 4.0 での Apache Hadoop 環境のコンポーネントとバージョンについて説明します。

## <a name="apache-components-available-with-hdinsight-version-40"></a>HDInsight バージョン 4.0 で使用できる Apache のコンポーネント

HDInsight 4.0 に関連付けられている OSS コンポーネントのバージョンを、次の表にまとめます。

| コンポーネント              | HDInsight 4.0 |
|------------------------|---------------|
| Apache Hadoop と YARN | 3.1.1         |
| Apache Tez             | 0.9.1         |
| Apache Pig             | 0.16.1        |
| Apache Hive            | 3.1.0         |
| Apache Ranger          | 1.1.0         |
| Apache HBase           | 2.1.6         |
| Apache Sqoop           | 1.5.0         |
| Apache Oozie           | 4.3.1         |
| Apache Zookeeper       | 3.4.6         |
| Apache Phoenix         | 5             |
| Apache Spark           | 2.4.4         |
| Apache Livy            | 0.5           |
| Apache Kafka           | 2.1.1         |
| Apache Ambari          | 2.7.0         |
| Apache Zeppelin        | 0.8.0         |
| Mono                   | 4.2.1         |


次の表は、廃止された HDInsight 4.0 クラスターの種類の一覧です。

| クラスターの種類                    | Framework のバージョン | サポート有効期限      | 提供終了日 |
|---------------------------------|-------------------|------------------------------|-----------------|
| HDInsight 4.0 Spark             | 2.3               | 2020 年 6 月 30 日                | 2020 年 6 月 30 日   |
| HDInsight 4.0 Kafka             | 1.1               | 2020 年 12 月 31 日                 | 2020 年 12 月 31 日    |

## <a name="next-steps"></a>次のステップ

- [HDInsight で Apache Hadoop、Spark、その他のクラスターをセットアップする](hdinsight-hadoop-provision-linux-clusters.md)
- [Enterprise セキュリティ パッケージ](./enterprise-security-package.md)
- [Windows PC から HDInsight の Apache Hadoop で作業する](hdinsight-hadoop-windows-tools.md)
