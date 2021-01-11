---
title: Azure Data Lake Storage Gen2 の概要
description: Azure Data Lake Storage Gen2 の概要を読みます。 主な機能について説明します。 サポートされている BLOB ストレージの機能、Azure サービスの統合、およびプラットフォームを確認します。
author: normesta
ms.service: storage
ms.topic: overview
ms.date: 02/25/2020
ms.author: normesta
ms.reviewer: jamesbak
ms.subservice: data-lake-storage-gen2
ms.openlocfilehash: d85b0cd2f9fa7eb81f5c39bd5d163188e3cd7106
ms.sourcegitcommit: 2ff0d073607bc746ffc638a84bb026d1705e543e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/06/2020
ms.locfileid: "87835767"
---
# <a name="introduction-to-azure-data-lake-storage-gen2"></a>Azure Data Lake Storage Gen2 の概要

‎Azure Data Lake Storage Gen2 は、ビッグ データ分析専用の機能セットであり、[Azure Blob Storage](storage-blobs-introduction.md) に基づいて構築されています。 Data Lake Storage Gen2 は、Azure Blob ストレージと Azure Data Lake Storage Gen1 という、既存の 2 つのストレージ サービスの機能を集約したものです。 ファイル システム セマンティクス、ディレクトリ、ファイル レベルのセキュリティおよびスケーリングなど、[Azure Data Lake Storage Gen1](https://docs.microsoft.com/azure/data-lake-store/index) に由来する機能が、[Azure Blob ストレージ](storage-blobs-introduction.md)の低コストの階層型記憶域、高可用性/ディザスター リカバリー機能と組み合わされています。

## <a name="designed-for-enterprise-big-data-analytics"></a>エンタープライズ ビッグ データ分析用に設計されている

Data Lake Storage Gen2 によって、Azure Storage は、Azure 上にエンタープライズ データ レイクを構築するための基盤となります。 Data Lake Storage Gen2 は、当初から、何百ものギガビット単位のスループットを維持しつつ、複数のペタバイト単位の情報を利用可能にする目的で設計されているため、大量のデータを簡単に管理することができます。

Data Lake Storage Gen2 の基礎部分は、BLOB ストレージに[階層型名前空間](data-lake-storage-namespace.md)を追加したものです。 階層型名前空間には、効率的なデータ アクセスのためにオブジェクトやファイルがディレクトリ階層に編成されています。 共通のオブジェクト ストアの名前規則では、名前にスラッシュを使用して階層型ディレクトリ構造を模倣しています。 この構造は、Data Lake Storage Gen2 を使って、実際のものになります。 ディレクトリの名前変更や削除などの操作は、ディレクトリ名のプレフィックスを共有するすべてのオブジェクトを列挙して処理するのではなく、ディレクトリ上の単一のアトミック メタデータの操作になります。

Data Lake Storage Gen2 は Blob Storage をベースに構築され、パフォーマンス、管理、セキュリティが次のように強化されています。

-   **パフォーマンス**。分析の前提条件としてデータをコピーまたは変換する必要がないため、最適化されます。 Blob Storage のフラット型名前空間と比べ、階層型名前空間ではディレクトリ管理操作のパフォーマンスが大幅に向上し、その結果、全体的なジョブ パフォーマンスも向上します。

-   **管理**。ディレクトリおよびサブディレクトリを利用してファイルを編成および操作できるため、簡単になりました。

-   **セキュリティ**。ディレクトリや個別のファイルに対して POSIX アクセス許可を定義できるので、セキュリティを確保できます。

Data Lake Storage Gen2 は、低コストの [Azure Blob Storage](storage-blobs-introduction.md) をベースに構築されているため、きわめて高いコスト効果が得られます。 さらに、追加の機能により、Azure 上でビッグ データ分析を実行するための総保有コストが低下しました。

## <a name="key-features-of-data-lake-storage-gen2"></a>Data Lake Storage Gen2 の主な機能

-   **Hadoop と互換性のあるアクセス**:Data Lake Storage Gen2 を使用すると、[Hadoop 分散ファイル システム (HDFS)](https://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-hdfs/HdfsDesign.html) の場合と同様にデータの管理およびアクセスを行うことができます。 新しい [ABFS ドライバー](data-lake-storage-abfs-driver.md)は、[Azure HDInsight](https://docs.microsoft.com/azure/hdinsight/index) *、* [Azure Databricks](https://docs.microsoft.com/azure/azure-databricks/index)、[Azure Synapse Analytics](https://docs.microsoft.com/azure/synapse-analytics) など、すべての Apache Hadoop 環境内で、Data Lake Storage Gen2 に格納されているデータにアクセスするために使用できます。

-   **POSIX アクセス許可のスーパーセット**:Data Lake Gen2 のセキュリティ モデルは、ACL および POSIX のアクセス許可に加え、Data Lake Storage Gen2 固有の追加設定をサポートしています。 設定は、Storage Explorer だけでなく、Hive や Spark などのフレームワークを使用して構成できます。

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
| Azure Data Lake Storage Gen2 - Analytics Storage          | コンテナー            | ディレクトリ                                                           | ファイル           |

## <a name="supported-blob-storage-features"></a>Blob Storage のサポートされている機能

階層型名前空間があるアカウントで、 [診断ログ](../common/storage-analytics-logging.md)、 [アクセス レベル](storage-blob-storage-tiers.md)、 [Blob Storage ライフサイクル管理ポリシー](storage-lifecycle-management-concepts.md)などの Blob Storage の機能が動作するようになりました。 そのため、これらの機能へのアクセスを失うことなく、Blob Storage アカウントで階層型名前空間を有効にできます。 

サポートされる Blob Storage 機能の一覧については、「[Azure Data Lake Storage Gen2 で使用できる Blob Storage 機能](data-lake-storage-supported-blob-storage-features.md)」を参照してください。

## <a name="supported-azure-service-integrations"></a>サポートされる Azure サービスの統合

Data Lake Storage gen2 は、データの取り込み、分析の実行、およびビジュアル表現の作成に使用できるいくつかの Azure サービスをサポートしています。 サポートされる Azure サービスの一覧については、「[Azure Data Lake Storage Gen2 がサポートされている Azure のサービス](data-lake-storage-supported-azure-services.md)」を参照してください。

## <a name="supported-open-source-platforms"></a>サポートされるオープン ソース プラットフォーム

一部のオープン ソース プラットフォームは Data Lake Storage Gen2 をサポートしています。 完全な一覧については、「[Data Lake Storage Gen2 がサポートされているオープン ソース プラットフォーム](data-lake-storage-supported-open-source-platforms.md)」を参照してください。

## <a name="see-also"></a>関連項目

- [Azure Data Lake Storage Gen2 に関する既知の問題](data-lake-storage-known-issues.md)
- [Azure Data Lake Storage のマルチプロトコル アクセス](data-lake-storage-multi-protocol-access.md)


