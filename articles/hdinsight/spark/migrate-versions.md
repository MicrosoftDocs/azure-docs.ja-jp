---
title: Apache Spark 2.1 または 2.2 のワークロードを 2.3 または 2.4 に移行する - Azure HDInsight
description: Apache Spark 2.1 と 2.2 を 2.3 または 2.4 に移行する方法について説明します。
ms.service: hdinsight
ms.topic: conceptual
ms.date: 05/20/2020
ms.openlocfilehash: af1894d2f63357006e87fa8e4533f135ecc02f21
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "98944750"
---
# <a name="migrate-apache-spark-21-and-22-workloads-to-23-and-24"></a>Apache Spark 2.1 と2.2 のワークロードを 2.3 または 2.4 に移行する

このドキュメントでは、Apache Spark 2.1 および 2.2 の Spark ワークロードを、2.3 または 2.4 に移行する方法について説明します。

[リリース ノート](../hdinsight-release-notes.md#upcoming-changes)に記載されているように、2020 年 7 月 1 日以降は、次のクラスター構成がサポートされず、お客様はこれらの構成で新しいクラスターを作成できなくなります。
 - HDInsight 3.6 Spark クラスターの Spark 2.1 と 2.2
 - HDInsight 4.0 Spark クラスターの Spark 2.3

これらの構成の既存のクラスターはそのまま稼働しますが、Microsoft からのサポートはありません。 HDInsight 3.6 で Spark 2.1 または 2.2 を使用している場合は、システムやサポートが中断する可能性を回避するために、2020 年 6月 30 日までに HDInsight 3.6 で Spark 2.3 に移行してください。 HDInsight 4.0 クラスターで Spark 2.3 を使用している場合は、システムやサポートが中断する可能性を回避するために、2020 年 6月 30 日までに HDInsight 4.0 で Spark 2.4 に移行してください。

HDInsight クラスター 3.6 から 4.0 への移行に関する一般的な情報については、「[HDInsight クラスターを新しいバージョンに移行する](../hdinsight-upgrade-cluster.md)」を参照してください。 Apache Spark の新しいバージョンへの移行に関する一般的な情報については、[Apache Spark のバージョン管理ポリシー](https://spark.apache.org/versioning-policy.html)に関するページを参照してください。

## <a name="guidance-on-spark-version-upgrades-on-hdinsight"></a>HDInsight での Spark のバージョン アップグレードに関するガイダンス

| アップグレード シナリオ | メカニズム | 考慮事項 | Spark と Hive の統合 |
|------------------|-----------|--------------------|------------------------|
|HDInsight 3.6 Spark 2.1 から HDInsight 3.6 Spark 2.3 へ| HDInsight Spark 2.3 でクラスターを再作成する | 以下の記事を確認してください。 <br> [Apache Spark:Spark SQL 2.2 から 2.3 へのアップグレード](https://spark.apache.org/docs/latest/sql-migration-guide.html#upgrading-from-spark-sql-22-to-23) <br><br> [Apache Spark:Spark SQL 2.1 から 2.2 へのアップグレード](https://spark.apache.org/docs/latest/sql-migration-guide.html#upgrading-from-spark-sql-21-to-22) | 変更なし |
|HDInsight 3.6 Spark 2.2 から HDInsight 3.6 Spark 2.3 へ | HDInsight Spark 2.3 でクラスターを再作成する | 以下の記事を確認してください。 <br> [Apache Spark:Spark SQL 2.2 から 2.3 へのアップグレード](https://spark.apache.org/docs/latest/sql-migration-guide.html#upgrading-from-spark-sql-22-to-23) | 変更なし |
| HDInsight 3.6 Spark 2.1 から HDInsight 4.0 Spark 2.4 へ | HDInsight 4.0 Spark 2.4 でクラスターを再作成する | 以下の記事を確認してください。 <br> [Apache Spark:Spark SQL 2.3 から 2.4 へのアップグレード](https://spark.apache.org/docs/latest/sql-migration-guide.html#upgrading-from-spark-sql-23-to-24) <br><br> [Apache Spark:Spark SQL 2.2 から 2.3 へのアップグレード](https://spark.apache.org/docs/latest/sql-migration-guide.html#upgrading-from-spark-sql-22-to-23) <br><br> [Apache Spark:Spark SQL 2.1 から 2.2 へのアップグレード](https://spark.apache.org/docs/latest/sql-migration-guide.html#upgrading-from-spark-sql-21-to-22) | Spark と Hive の統合は HDInsight 4.0 で変更されました。 <br><br> HDInsight 4.0 では、Spark と Hive は、SparkSQL または Hive テーブルへのアクセスに、独立したカタログを使用します。 Spark によって作成されたテーブルは、Spark カタログ内に存在します。 Hive によって作成されたテーブルは、Hive カタログ内に存在します。 この動作は、Hive と Spark が共通のカタログを共有する HDInsight 3.6 とは異なります。 HDInsight 4.0 での Hive と Spark の統合は、Hive Warehouse Connector (HWC) に依存します。 HWC は、Spark と Hive 間の橋として動作します。 Hive Warehouse Connector について学習してください。 <br> HDInsight 4.0 で Hive と Spark の間でメタストアを共有する場合は、Spark クラスターで metastore.catalog.default プロパティを hive に変更します。 このプロパティは、Ambari Advanced spark2-hive-site-override にあります。 メタストアの共有が可能なのは外部のハイブ テーブルのみであることを理解しておくことが重要です。内部/管理対象のハイブ テーブルまたは ACID テーブルがある場合は、この共有を行えません。 <br><br>詳細については、「[Azure HDInsight 3.6 Hive ワークロードを Hive HDInsight 4.0 に移行する](../interactive-query/apache-hive-migrate-workloads.md)」を参照してください。<br><br> |
| HDInsight 3.6 Spark 2.2 から HDInsight 4.0 Spark 2.4 へ | HDInsight 4.0 Spark 2.4 でクラスターを再作成する | 以下の記事を確認してください。 <br> [Apache Spark:Spark SQL 2.3 から 2.4 へのアップグレード](https://spark.apache.org/docs/latest/sql-migration-guide.html#upgrading-from-spark-sql-23-to-24) <br><br> [Apache Spark:Spark SQL 2.2 から 2.3 へのアップグレード](https://spark.apache.org/docs/latest/sql-migration-guide.html#upgrading-from-spark-sql-22-to-23) | Spark と Hive の統合は HDInsight 4.0 で変更されました。 <br><br> HDInsight 4.0 では、Spark と Hive は、SparkSQL または Hive テーブルへのアクセスに、独立したカタログを使用します。 Spark によって作成されたテーブルは、Spark カタログ内に存在します。 Hive によって作成されたテーブルは、Hive カタログ内に存在します。 この動作は、Hive と Spark が共通のカタログを共有する HDInsight 3.6 とは異なります。 HDInsight 4.0 での Hive と Spark の統合は、Hive Warehouse Connector (HWC) に依存します。 HWC は、Spark と Hive 間の橋として動作します。 Hive Warehouse Connector について学習してください。 <br> HDInsight 4.0 で Hive と Spark の間でメタストアを共有する場合は、Spark クラスターで metastore.catalog.default プロパティを hive に変更します。 このプロパティは、Ambari Advanced spark2-hive-site-override にあります。 メタストアの共有が可能なのは外部のハイブ テーブルのみであることを理解しておくことが重要です。内部/管理対象のハイブ テーブルまたは ACID テーブルがある場合は、この共有を行えません。 <br><br>詳細については、「[Azure HDInsight 3.6 Hive ワークロードを Hive HDInsight 4.0 に移行する](../interactive-query/apache-hive-migrate-workloads.md)」を参照してください。|

## <a name="next-steps"></a>次のステップ

* [HDInsight クラスターを新しいバージョンに移行する](../hdinsight-upgrade-cluster.md)
* [Azure HDInsight 3.6 Hive ワークロードを Hive HDInsight 4.0 に移行する](../interactive-query/apache-hive-migrate-workloads.md)
