---
title: Azure Data Lake Analytics U-SQL 開発者向けの Apache Spark データ形式について説明します。
description: この記事では、U-SQL 開発者が、U-SQL と Spark のデータ形式の違いを理解するのに役立つ Apache Spark の概念について説明します。
ms.reviewer: jasonh
ms.service: data-lake-analytics
ms.topic: how-to
ms.custom: understand-apache-spark-data-formats
ms.date: 01/31/2019
ms.openlocfilehash: 399914186ce9de62ef46b682c8d4a6e51426cc26
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "92221113"
---
# <a name="understand-differences-between-u-sql-and-spark-data-formats"></a>U-SQL と Spark のデータ形式の違いを理解する

[Azure Databricks](/azure/databricks/scenarios/what-is-azure-databricks) または [Azure HDInsight Spark](../hdinsight/spark/apache-spark-overview.md) を使用する場合は、[Azure Data Lake Storage Gen1](../data-lake-store/data-lake-store-overview.md) から [Azure Data Lake Storage Gen2](../storage/blobs/data-lake-storage-introduction.md) にデータを移行することをお勧めします。

ファイルを移動するだけでなく、U-SQL テーブルに格納されているデータを Spark からアクセスできるようにすることも可能です。

## <a name="move-data-stored-in-azure-data-lake-storage-gen1-files"></a>Azure Data Lake Storage Gen1 ファイルに格納されているデータを移動する

ファイルに格納されているデータは、さまざまな方法で移動できます。

- [Azure Data Lake Storage Gen1](../data-lake-store/data-lake-store-overview.md) アカウントから [Azure Data Lake Storage Gen2](../storage/blobs/data-lake-storage-introduction.md) アカウントにデータをコピーする [Azure Data Factory](../data-factory/introduction.md) パイプラインを作成します。
- [Azure Data Lake Storage Gen1](../data-lake-store/data-lake-store-overview.md) アカウントからデータを読み取り [Azure Data Lake Storage Gen2](../storage/blobs/data-lake-storage-introduction.md) アカウントに書き込む Spark ジョブを作成します。 元のファイル形式を保持する必要がない場合は、使用状況に応じて、Parquet などの別の形式で記述することをお勧めします。

「[ビッグ データ分析ソリューションを Azure Data Lake Storage Gen1 から Azure Data Lake Storage Gen2 にアップグレードする](../storage/blobs/data-lake-storage-migrate-gen1-to-gen2.md)」の記事を確認することをお勧めします。

## <a name="move-data-stored-in-u-sql-tables"></a>U-SQL テーブルに格納されているデータを移動する

U-SQL テーブルは Spark で認識されません。 データが U-SQL テーブルに格納されている場合は、テーブル データを抽出して Spark が認識できる形式で保存する U-SQL ジョブを実行します。 最も適切な形式は、Hive metastore のフォルダー レイアウトに従って、一連の Parquet ファイルを作成することです。

U-SQL では、組み込みの Parquet アウトプッターを使用し、ファイル セットを使用した動的出力パーティション分割を使用してパーティション フォルダーを作成することで、出力を実現できます。 [これまで以上に多くのファイルの処理し、Parquet を使用](/archive/blogs/azuredatalake/process-more-files-than-ever-and-use-parquet-with-azure-data-lake-analytics)することで、このような Spark で使用可能なデータを作成する方法の例を示します。

この変換の後、「[Azure Data Lake Storage Gen1 ファイルに格納されているデータを移動する](#move-data-stored-in-azure-data-lake-storage-gen1-files)」の章で説明されているようにデータをコピーします。

## <a name="caveats"></a>注意事項

- データのセマンティクス。ファイルをコピーする場合、コピーはバイト レベルで行われます。 そのため、同じデータが [Azure Data Lake Storage Gen2](../storage/blobs/data-lake-storage-introduction.md) アカウントに表示されるはずです。 ただし、Spark では一部の文字が異なる方法で解釈される場合があります。 たとえば、CSV ファイルの行の区切り記号に異なる既定値を使用する場合があります。
    さらに、(テーブルから) 型指定されたデータをコピーする場合、Parquet と Spark では、一部の型指定された値 (float など) の有効桁数と小数点以下桁数が異なる場合があり、null 値が異なる方法で扱われる場合があります。 たとえば、U-SQL には null 値の C# セマンティクスがあり、Spark には null 値に対する 3 値論理があります。

- データ編成 (パーティション分割)。U-SQL テーブルには 2 つのレベルのパーティション分割が用意されています。 外部レベル (`PARTITIONED BY`) は値で表され、ほとんどの場合、フォルダー階層を使用して Hive/Spark パーティション構成にマップされます。 Null 値が正しいフォルダーにマップされていることを確認する必要があります。 U-SQL の内部レベル (`DISTRIBUTED BY`) では、ラウンド ロビン、範囲、ハッシュ、および直接ハッシュの 4 つのディストリビューション スキームが提供されています。
    Hive/Spark テーブルでは、U-SQL とは異なるハッシュ関数を使用して、値のパーティション分割またはハッシュ パーティション分割のみをサポートしています。 U-SQL テーブル データを出力する場合は、Spark の値のパーティション分割にのみマップでき、最終的な Spark クエリに応じてデータ レイアウトをさらに調整することが必要になる場合があります。

## <a name="next-steps"></a>次のステップ

- [U-SQL 開発者向けの Spark コードの概念について](understand-spark-code-concepts.md)
- [ビッグ データ分析ソリューションを Azure Data Lake Storage Gen1 から Azure Data Lake Storage Gen2 にアップグレードする](../storage/blobs/data-lake-storage-migrate-gen1-to-gen2.md)
- [.NET for Apache Spark](/dotnet/spark/what-is-apache-spark-dotnet)
- [Azure Data Factory での Spark アクティビティを使用したデータの変換](../data-factory/transform-data-using-spark.md)
- [Azure Data Factory で Hadoop Hive アクティビティを使用してデータを変換する](../data-factory/transform-data-using-hadoop-hive.md)
- [Apache Spark とは - Azure HDInsight](../hdinsight/spark/apache-spark-overview.md)