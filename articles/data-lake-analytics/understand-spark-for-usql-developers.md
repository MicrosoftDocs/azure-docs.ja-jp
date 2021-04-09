---
title: Azure Data Lake Analytics U-SQL 開発者向けの Apache Spark について理解する
description: この記事では、U-SQL 開発者が違いを理解するのに役立つ Apache Spark の概念について説明します。
ms.reviewer: jasonh
ms.service: data-lake-analytics
ms.topic: how-to
ms.custom: understand-apache-spark-for-usql-developers
ms.date: 10/15/2019
ms.openlocfilehash: a66a82a6d2a5bb1f534ed211091793b2ab4d2a28
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "92221096"
---
# <a name="understand-apache-spark-for-u-sql-developers"></a>U-SQL 開発者向けの Apache Spark について

Microsoft では、[Azure Databricks](/azure/databricks/scenarios/what-is-azure-databricks)、[Azure HDInsight](../hdinsight/hdinsight-overview.md)、Azure Data Lake Analytics など、いくつかの分析サービスがサポートされています。 開発者は、分析パイプラインを構築するときに、はっきりと好みのオープンソース ソリューションがあると言っています。 このガイダンスでは、U-SQL 開発者が Apache Spark を理解するのに役立つ情報と、U-SQL のスクリプトを Apache Spark に変換する方法について説明します。

これには、実行するいくつかのステップと、複数の代替手段が含まれます。

## <a name="steps-to-transform-u-sql-to-apache-spark"></a>U-SQL を Apache Spark に変換する手順

1. ジョブ オーケストレーション パイプラインを変換します。

   [Azure Data Factory](../data-factory/introduction.md) を使用して Azure Data Lake Analytics のスクリプトのオーケストレーションを行っている場合、新しい Spark プログラムのオーケストレーションを行うようにそれらを調整する必要があります。
2. U-SQL と Spark によるデータの管理方法の違いを理解します

   [Azure Data Lake Storage Gen1](../data-lake-store/data-lake-store-overview.md) から [Azure Data Lake Storage Gen2](../storage/blobs/data-lake-storage-introduction.md) にデータを移動する場合は、ファイル データとカタログに保持されたデータの両方をコピーする必要があります。 Azure Data Lake Analytics では、Azure Data Lake Storage Gen1 のみがサポートされることに注意してください。 [Spark のデータ形式](understand-spark-data-formats.md)に関する記事をご覧ください
3. U-SQL のスクリプトを Spark に変換します

   U-SQL のスクリプトを変換する前に、分析サービスを選択する必要があります。 利用可能なコンピューティング サービスの一部を次に示します。
      - [Azure Data Factory データフロー](../data-factory/concepts-data-flow-overview.md): マッピング データ フローは、視覚的に設計されたデータ変換であり、データ エンジニアはコードを記述せずにグラフィカルなデータ変換ロジックを開発できます。 複雑なユーザー コードの実行には適していませんが、従来の SQL に似たデータフローの変換を簡単に表すことができます
      - [Azure HDInsight Hive](../hdinsight/hadoop/apache-hadoop-using-apache-hive-as-an-etl-tool.md): HDInsight 上の Apache Hive は、抽出、変換、読み込み (ETL) 操作に適しています。 つまり、U-SQL のスクリプトを Apache Hive に変換します。
      - [Azure HDInsight Spark](../hdinsight/spark/apache-spark-overview.md) や [Azure Databricks](/azure/databricks/scenarios/what-is-azure-databricks) などの Apache Spark エンジン: U-SQL のスクリプトを Spark に変換します。 詳しくは、[Spark のデータ形式](understand-spark-data-formats.md)に関する記事をご覧ください

> [!CAUTION]
> [Azure Databricks](/azure/databricks/scenarios/what-is-azure-databricks) と [Azure HDInsight Spark](../hdinsight/spark/apache-spark-overview.md) はどちらもクラスター サービスであり、Azure Data Lake Analytics のようなサーバーレス ジョブではありません。 適切なコスト/パフォーマンス比を得るためのクラスターのプロビジョニング方法と、コストを最小限に抑えるための有効期間の管理方法を、検討する必要があります。 これらのサービスには、.NET で記述されたユーザー コードとは異なるパフォーマンス特性があるため、ラッパーを作成するか、サポートされている言語でコードを書き直す必要があります。 詳細については、[Spark のデータ形式](understand-spark-data-formats.md)、[U-SQL 開発者向けの Apache Spark コードの概念](understand-spark-code-concepts.md)、[.Net Apache Spark](https://dotnet.microsoft.com/apps/data/spark) に関する記事をご覧ください

## <a name="next-steps"></a>次のステップ

- [U-SQL 開発者向けの Spark データ形式について](understand-spark-data-formats.md)
- [U-SQL 開発者向けの Spark コードの概念について](understand-spark-code-concepts.md)
- [ビッグ データ分析ソリューションを Azure Data Lake Storage Gen1 から Azure Data Lake Storage Gen2 にアップグレードする](../storage/blobs/data-lake-storage-migrate-gen1-to-gen2.md)
- [.NET for Apache Spark](/dotnet/spark/what-is-apache-spark-dotnet)
- [Azure Data Factory で Hadoop Hive アクティビティを使用してデータを変換する](../data-factory/transform-data-using-hadoop-hive.md)
- [Azure Data Factory での Spark アクティビティを使用したデータの変換](../data-factory/transform-data-using-spark.md)
- [Apache Spark とは - Azure HDInsight](../hdinsight/spark/apache-spark-overview.md)