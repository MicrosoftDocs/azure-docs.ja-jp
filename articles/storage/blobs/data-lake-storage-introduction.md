---
title: Azure Data Lake Storage Gen2 の概要
description: Azure Data Lake Storage Gen2 の概要について説明します
author: normesta
ms.service: storage
ms.topic: overview
ms.date: 12/06/2018
ms.author: normesta
ms.reviewer: jamesbak
ms.subservice: data-lake-storage-gen2
ms.openlocfilehash: de2dc5068dc454925744688a43f49a855aac42f3
ms.sourcegitcommit: 007ee4ac1c64810632754d9db2277663a138f9c4
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/23/2019
ms.locfileid: "69991850"
---
# <a name="introduction-to-azure-data-lake-storage-gen2"></a>Azure Data Lake Storage Gen2 の概要

‎Azure Data Lake Storage Gen2 は、ビッグ データ分析専用の機能セットであり、[Azure Blob Storage](storage-blobs-introduction.md) に基づいて構築されています。 Data Lake Storage Gen2 は、Azure Blob ストレージと Azure Data Lake Storage Gen1 という、既存の 2 つのストレージ サービスの機能を集約したものです。 ファイル システム セマンティクス、ディレクトリ、ファイル レベルのセキュリティおよびスケーリングなど、[Azure Data Lake Storage Gen1](https://docs.microsoft.com/azure/data-lake-store/index) に由来する機能が、[Azure Blob ストレージ](storage-blobs-introduction.md)の低コストの階層型記憶域、高可用性/ディザスター リカバリー機能と組み合わされています。

## <a name="designed-for-enterprise-big-data-analytics"></a>エンタープライズ ビッグ データ分析用に設計されている

Data Lake Storage Gen2 によって、Azure Storage は、Azure 上にエンタープライズ データ レイクを構築するための基盤となります。 Data Lake Storage Gen2 は、当初から、何百ものギガビット単位のスループットを維持しつつ、複数のペタバイト単位の情報を利用可能にする目的で設計されているため、大量のデータを簡単に管理することができます。

Data Lake Storage Gen2 の基礎部分は、BLOB ストレージに[階層型名前空間](data-lake-storage-namespace.md)を追加したものです。 階層型名前空間には、効率的なデータ アクセスのためにオブジェクトやファイルがディレクトリ階層に編成されています。 共通のオブジェクト ストアの名前規則では、名前にスラッシュを使用して階層型ディレクトリ構造を模倣しています。 この構造は、Data Lake Storage Gen2 を使って、実際のものになります。 ディレクトリの名前変更や削除などの操作は、ディレクトリ名のプレフィックスを共有するすべてのオブジェクトを列挙して処理するのではなく、ディレクトリ上の単一のアトミック メタデータの操作になります。

以前は、パフォーマンス、管理、およびセキュリティの領域では、クラウドベース分析は妥協する必要がありました。 Data Lake Storage Gen2 では、次の方法で各側面に対応しています。

-   **パフォーマンス**。分析の前提条件としてデータをコピーまたは変換する必要がないため、最適化されます。 階層型名前空間によってディレクトリ管理操作のパフォーマンスは大幅に向上し、その結果、全体的なジョブ パフォーマンスも向上します。

-   **管理**。ディレクトリおよびサブディレクトリを利用してファイルを編成および操作できるため、簡単になりました。

-   **セキュリティ**。ディレクトリや個別のファイルに対して POSIX アクセス許可を定義できるので、セキュリティを確保できます。

-   **コスト効率**。Data Lake Storage Gen2 が低コストの [Azure Blob ストレージ](storage-blobs-introduction.md)の上位にビルドされていることで、実現されました。 さらに、追加の機能により、Azure 上でビッグ データ分析を実行するための総保有コストが低下しました。

## <a name="key-features-of-data-lake-storage-gen2"></a>Data Lake Storage Gen2 の主な機能

-   **Hadoop と互換性のあるアクセス**:Data Lake Storage Gen2 を使用すると、[Hadoop 分散ファイル システム (HDFS)](https://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-hdfs/HdfsDesign.html) の場合と同様にデータの管理およびアクセスを行うことができます。 新しい [ABFS ドライバー](data-lake-storage-abfs-driver.md)は、[Azure HDInsight](https://docs.microsoft.com/azure/hdinsight/index) *、* [Azure Databricks](https://docs.microsoft.com/azure/azure-databricks/index)、[SQL Data Warehouse](https://docs.microsoft.com/azure/sql-data-warehouse/) など、すべての Apache Hadoop 環境内で、Data Lake Storage Gen2 に格納されているデータにアクセスするために使用できます。

-   **POSIX アクセス許可のスーパーセット**:Data Lake Gen2 のセキュリティ モデルは、ACL および POSIX のアクセス許可に加え、Data Lake Storage Gen2 固有の追加設定をサポートしています。 設定は、ストレージ エクスプローラーだけでなく、Hive や Spark などのフレームワークを使用して構成できます。

-   **コスト効率**:Data Lake Storage Gen2 は、低コストのストレージ容量とトランザクションを備えています。 ライフサイクル全体を通したデータ遷移として、課金レートは、[Azure Blob ストレージ ライフサイクル](storage-lifecycle-management-concepts.md)などの組み込みの機能を介して、最小限になるまで継続コストを変更します。

-   **最適化されたドライバー**:ABFS ドライバーは、ビッグ データ分析のために[特別に最適化](data-lake-storage-abfs-driver.md)されています。 該当する REST API は、`dfs.core.windows.net` エンドポイントを介して表示されます。

### <a name="scalability"></a>スケーラビリティ

Azure Storage では、Data Lake Storage Gen2 または Blob ストレージ インターフェイスのどちらを経由してアクセスするか、設計ごとにスケーラブルになっています。 また、*多数のエクサバイト データ*を格納および提供できます。 このストレージ容量は、秒単位での高レベルの入出力処理 (IOPS).で、ギガビット/秒 (Gbps) で計測されるスループットによって利用可能になります。 単なる永続性ではなく、サービス、アカウント、およびファイル レベルで計測される定数に近いリクエストごとの待機時間で、処理が実行されます。

### <a name="cost-effectiveness"></a>コスト効率

Azure Blob ストレージの上位に Data Lake Storage Gen2 をビルドする多くのメリットの 1 つが、低コストのストレージ容量とトランザクションです。 他のクラウド ストレージ サービスとは違って、Data Lake Storage Gen2 に格納されているデータは、分析を実行する前に移行または変換を行う必要がありません。 価格の詳細については、[Azure Storage の価格](https://azure.microsoft.com/pricing/details/storage)に関するページを参照してください。

さらに、[階層構造の名前空間](data-lake-storage-namespace.md)などの機能は、多数の分析ジョブのパフォーマンス全体を大幅に向上させます。 このパフォーマンスの向上は、同じデータ量を処理するうえで必要とするコンピューティング能力をより低減できることを意味し、結果として、エンド ツー エンドの分析ジョブの総保有コスト (TCO) を削減できます。

### <a name="one-service-multiple-concepts"></a>1 つのサービス、複数の概念

Data Lake Storage Gen2 は、Azure Blob ストレージに基づいて構築された、ビッグ データ分析用の追加機能です。 BLOB の既存のプラットフォーム コンポーネントを利用して分析用の Data Lake を作成および操作する場合に多くの利点がありますが、同じ共通することを説明する概念が複数生まれることになります。

複数の概念で説明される同等のエンティティを次に示します。 特記がない限り、これらのエンティティはそのままの同義語です。

| 概念                                | 最上位レベルの組織 | 下位レベルの組織                                            | データ コンテナー |
|----------------------------------------|------------------------|---------------------------------------------------------------------|----------------|
| BLOB - 汎用オブジェクト ストレージ | コンテナー              | 仮想ディレクトリ (SDK のみ - アトミック操作を提供しない) | BLOB           |
| ADLS Gen2 - Analytics ストレージ          | コンテナー            | Directory                                                           | ファイル           |

## <a name="supported-open-source-platforms"></a>サポートされるオープン ソース プラットフォーム

一部のオープン ソース プラットフォームは Data Lake Storage Gen2 をサポートしています。 これらのプラットフォームを次の表に示します。

> [!NOTE]
> この表に記載されているバージョンのみがサポートされています。

| プラットフォーム |  サポートされるバージョン | 詳細情報 |
| --- | --- | --- |
| [HDInsight](https://azure.microsoft.com/services/hdinsight/) | 3.6 以降 | [HDInsight で使用できる Apache Hadoop コンポーネントおよびバージョンとは](https://docs.microsoft.com/azure/hdinsight/hdinsight-component-versioning?toc=%2Fen-us%2Fazure%2Fhdinsight%2Fstorm%2FTOC.json&bc=%2Fen-us%2Fazure%2Fbread%2Ftoc.json)
| [Hadoop](https://hadoop.apache.org/) | 3.2 以降 | [Apache Hadoop リリース アーカイブ](https://hadoop.apache.org/release.html) |
| [Cloudera](https://www.cloudera.com/) | 6.1 以降 | [Cloudera Enterprise 6.x のリリース ノート](https://www.cloudera.com/documentation/enterprise/6/release-notes/topics/rg_cdh_6_release_notes.html) |
| [Azure Databricks](https://azure.microsoft.com/services/databricks/) | 5.1 以降 | [Databricks Runtime のバージョン](https://docs.databricks.com/release-notes/runtime/databricks-runtime-ver.html) |
|[Hortonworks](https://hortonworks.com/)| 3.1.x++ | [クラウド データ アクセスの構成](https://docs.hortonworks.com/HDPDocuments/Cloudbreak/Cloudbreak-2.9.0/cloud-data-access/content/cb_configuring-access-to-adls2.html) |

## <a name="next-steps"></a>次の手順

以下の記事では、Data Lake Storage Gen2 の主要な概念の一部と、データの分析情報を保管、アクセス、管理、および取得する方法の詳細を示しています。

-   [階層構造の名前空間](data-lake-storage-namespace.md)
-   [ストレージ アカウントの作成](data-lake-storage-quickstart-create-account.md)
-   [Azure Databricks での Data Lake Storage Gen2 アカウントの使用](data-lake-storage-quickstart-create-databricks-account.md)
