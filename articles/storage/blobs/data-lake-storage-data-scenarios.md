---
title: Azure Data Lake Storage Gen2 に関するデータ シナリオ | Microsoft Docs
description: Data Lake Storage Gen2 (旧称 Azure Data Lake Store) でのデータの取り込み、処理、ダウンロード、視覚化に使用するさまざまなシナリオとツールについて説明します
author: normesta
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: conceptual
ms.date: 02/14/2020
ms.author: normesta
ms.reviewer: stewu
ms.openlocfilehash: 4ed2458b09d200542ce8789d90250027a07a970d
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "101735473"
---
# <a name="using-azure-data-lake-storage-gen2-for-big-data-requirements"></a>Data Lake Storage Gen2 を使用してビッグ データの要件に対応する

ビッグ データの処理には主に 4 つの段階があります。

> [!div class="checklist"]
> * データ ストアへの大量のデータの取り込み (リアルタイムまたは一括)
> * データの処理
> * データのダウンロード
> * データの視覚化

この記事では、各処理フェーズのオプションとツールについて説明します。

Azure Data Lake Storage Gen2 で使用できる Azure サービスの完全な一覧については、「[Integrate Azure Data Lake Storage with Azure services](./data-lake-storage-supported-azure-services.md)」 (Azure Data Lake Storage と Azure サービスを統合する) を参照してください

## <a name="ingest-the-data-into-data-lake-storage-gen2"></a>Data Lake Storage Gen2 にデータを取り込む

このセクションでは、さまざまなデータ ソースと、そのデータを Data Lake Storage Gen2 アカウントに取り込む各種方法について説明します。

![Data Lake Storage Gen2 にデータを取り込む](./media/data-lake-storage-data-scenarios/ingest-data.png "Data Lake Storage Gen2 にデータを取り込む")

### <a name="ad-hoc-data"></a>アドホック データ

これは、ビッグ データ アプリケーションのプロトタイプ作成に使用される小規模なデータ セットを表します。 アドホック データの取り込み方法は、データ ソースに応じてさまざまです。 

アドホック データの取り込みに使用できるツールの一覧を次に示します。

| Data Source | 取り込みに使用するツール |
| --- | --- |
| ローカル コンピューター |[Azure PowerShell](data-lake-storage-directory-file-acl-powershell.md)<br><br>[Azure CLI](data-lake-storage-directory-file-acl-cli.md)<br><br>[Storage Explorer](https://azure.microsoft.com/features/storage-explorer/)<br><br>[AzCopy ツール](../common/storage-use-azcopy-v10.md)|
| Azure Storage BLOB |[Azure Data Factory](../../data-factory/connector-azure-data-lake-store.md)<br><br>[AzCopy ツール](../common/storage-use-azcopy-v10.md)<br><br>[HDInsight クラスター上で実行されている DistCp](data-lake-storage-use-distcp.md)|

### <a name="streamed-data"></a>ストリーミングされたデータ

これは、アプリケーション、デバイス、センサーなどのさまざまなソースによって生成されるデータを表します。Data Lake Storage Gen2 へのこのデータの取り込みには各種ツールを使用できます。 これらのツールでは通常、イベントごとにリアルタイムでデータを取り込み、処理します。その後、さらにデータを処理できるように、Data Lake Storage Gen2 にイベントを一括で書き込みます。

ストリーミングされたデータの取り込みに使用できるツールの一覧を次に示します。

|ツール | ガイダンス |
|---|--|
|Azure Stream Analytics|[クイック スタート: Azure Portal を使用して Stream Analytics ジョブを作成する](../../stream-analytics/stream-analytics-quick-create-portal.md) <br> [Azure Data Lake Gen2 に出力する](../../stream-analytics/stream-analytics-define-outputs.md)|
|Azure HDInsight Storm | [HDInsight 上の Apache Storm から Apache Hadoop HDFS に書き込む](../../hdinsight/storm/apache-storm-write-data-lake-store.md) |

### <a name="relational-data"></a>リレーショナル データ

リレーショナル データベースのデータもソースとして扱うことができます。 一定の期間を経て、リレーショナル データベースには大量のデータが収集されます。ビッグ データのパイプラインを介して処理すると、これらのデータから重要な知見が得られます。 このようなデータを Data Lake Storage Gen2 に移動する場合は、次のツールを使用できます。

リレーショナル データの取り込みに使用できるツールの一覧を次に示します。

|ツール | ガイダンス |
|---|--|
|Azure Data Factory | [Azure Data Factory の Copy アクティビティ](../../data-factory/copy-activity-overview.md) |

### <a name="web-server-log-data-upload-using-custom-applications"></a>Web サーバー ログ データ (カスタム アプリケーションを使用したアップロード)

Web サーバー ログ データの分析は、ビッグ データ アプリケーションの一般的な使用例で、大量のログ ファイルを Data Lake Storage Gen2 にアップロードすることを必要とするため、この種類のデータセットが特に呼び出されます。 このようなデータをアップロードするための独自のスクリプトまたはアプリケーションを記述するには、次のツールのいずれかを使用できます。

Web サーバー ログ データの取り込みに使用できるツールの一覧を次に示します。

|ツール | ガイダンス |
|---|--|
|Azure Data Factory | [Azure Data Factory の Copy アクティビティ](../../data-factory/copy-activity-overview.md)  |
|Azure CLI|[Azure CLI](data-lake-storage-directory-file-acl-cli.md)|
|Azure PowerShell|[Azure PowerShell](data-lake-storage-directory-file-acl-powershell.md)|

Web サーバー ログ データをアップロードする場合、または他の種類のデータ (ソーシャル センチメント データなど) をアップロードする場合には、独自のカスタム スクリプトやカスタム アプリケーションを記述することをお勧めします。 これにより、データをアップロードするコンポーネントをより大規模なビッグ データ アプリケーションの一部として含める柔軟性が得られるためです。 このコードは、スクリプトまたは単純なコマンド ライン ユーティリティの形をとる場合もあれば、ビッグ データの処理をビジネス アプリケーションまたはビジネス ソリューションに統合するために使用される場合もあります。

### <a name="data-associated-with-azure-hdinsight-clusters"></a>Azure HDInsight クラスターに関連付けられたデータ

ほとんどの種類の HDInsight クラスター (Hadoop、HBase、Storm) では、データ ストレージ リポジトリとして Data Lake Storage Gen2 がサポートされています。 HDInsight クラスターは Azure Storage Blob (WASB) のデータにアクセスします。 パフォーマンスを向上させるために、クラスターに関連付けられた Data Lake Storage Gen2 アカウントに WASB のデータをコピーできます。 データのコピーには次のツールを使用できます。

HDInsight クラスターに関連付けられたデータの取り込みに使用できるツールの一覧を次に示します。

|ツール | ガイダンス |
|---|--|
|Apache DistCp | [Distcp を使用して Azure Storage Blob と Azure Data Lake Storage Gen2 の間でデータをコピーする](./data-lake-storage-use-distcp.md) |
|AzCopy ツール | [AzCopy を使用してデータを転送する](../common/storage-use-azcopy-v10.md) |
|Azure Data Factory | [Azure Data Factory を使用して Azure Data Lake Storage Gen2 との間でデータをコピーする](../../data-factory/load-azure-data-lake-storage-gen2.md) |

### <a name="data-stored-in-on-premises-or-iaas-hadoop-clusters"></a>オンプレミスまたは IaaS Hadoop クラスターに格納されているデータ

大量のデータが既存の Hadoop クラスターの HDFS を使用するコンピューターにローカルに格納されている場合があります。 Hadoop クラスターは、オンプレミスのデプロイ内にある場合も、Azure の IaaS クラスター内にある場合もあります。 このようなデータを 1 回限りまたは定期的に Azure Data Lake Storage Gen2 にコピーする要件が存在します。 これを実現するために、さまざまな方法を使用できます。 以下の表に、それぞれの方法とそのトレードオフを示します。

| アプローチ | 詳細 | 長所 | 考慮事項 |
| --- | --- | --- | --- |
| Azure Data Factory (ADF) を使用して Hadoop クラスターから Azure Data Lake Storage Gen2 にデータを直接コピーする。 |[ADF ではデータ ソースとして HDFS をサポートしている。](../../data-factory/connector-hdfs.md) |ADF では、HDFS が最初からサポートされており、ファースト クラスのエンド ツー エンドの管理と監視が提供される。 |Data Management Gateway をオンプレミスまたは IaaS クラスターにデプロイする必要がある。 |
| Distcp を使用して、Hadoop から Azure Storage にデータをコピーする。 次に、適切なメカニズムを使用してデータを Azure Storage から Data Lake Storage Gen2 にコピーする。 |次のいずれかを使用してデータを Azure Storage から Data Lake Storage Gen2 にコピーできます。 <ul><li>[Azure Data Factory](../../data-factory/copy-activity-overview.md)</li><li>[AzCopy ツール](../common/storage-use-azcopy-v10.md)</li><li>[HDInsight クラスター上で実行されている Apache DistCp](data-lake-storage-use-distcp.md)</li></ul> |オープン ソースのツールを使用できる。 |複数のテクノロジを含む複数ステップのプロセス。 |

### <a name="really-large-datasets"></a>非常に大規模なデータセット

数 TB に及ぶデータセットをアップロードする場合、上記の方法では速度が遅く、コストがかかることがあります。 このような場合は、Azure ExpressRoute を使用できます。  

Azure ExpressRoute を使用すると、Azure データ センターとお客様のオンプレミスのインフラストラクチャとの間でプライベート接続を作成できます。 これにより、大量のデータを転送するための信頼性の高いオプションが提供されます。 詳しくは、[Azure ExpressRoute のドキュメント](../../expressroute/expressroute-introduction.md)をご覧ください。

## <a name="process-the-data"></a>データを処理する

Data Lake Storage Gen2 でデータが利用できるようになったら、サポートされているビッグ データ アプリケーションを使用して、そのデータの分析を実行できます。 

![Data Lake Storage Gen2 のデータを分析する](./media/data-lake-storage-data-scenarios/analyze-data.png "Data Lake Storage Gen2 のデータを分析する")

Data Lake Storage Gen2 に格納されているデータに対してデータ分析ジョブを実行するのに使用できるツールの一覧を次に示します。

|ツール | ガイダンス |
|---|--|
|Azure HDInsight | [Azure HDInsight クラスターで Azure Data Lake Storage Gen2 を使用する](../../hdinsight/hdinsight-hadoop-use-data-lake-storage-gen2.md) |
|Azure Databricks | [Azure Data Lake Storage Gen2](/azure/databricks/data/data-sources/azure/azure-datalake-gen2)<br><br>[クイック スタート: Azure Databricks を使用して Azure Data Lake Storage Gen2 のデータを分析する](./data-lake-storage-use-databricks-spark.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)<br><br>[チュートリアル:Azure Databricks を使用してデータの抽出、変換、読み込みを行う](/azure/databricks/scenarios/databricks-extract-load-sql-data-warehouse?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)|

## <a name="visualize-the-data"></a>データの視覚化

Data Lake Storage Gen2 に格納されたデータを視覚的に表現するには、Power BI コネクタを使用します。 「[Power BI を使用して Azure Data Lake Storage Gen2 のデータを分析する](/power-query/connectors/datalakestorage)」を参照してください。

## <a name="download-the-data"></a>データをダウンロードする

次のようなシナリオでは、Azure Data Lake Storage Gen2 からデータをダウンロードしたり、移動したりすることもできます。

* 既存のデータ処理パイプラインとのインターフェイスとなる他のリポジトリにデータを移動する。 たとえば、Data Lake Storage Gen2 から Azure SQL Database または SQL Server インスタンスにデータを移動できます。

* アプリケーション プロトタイプの作成中に IDE 環境で処理するために、ローカル コンピューターにデータをダウンロードする。

![Data Lake Storage Gen2 からデータを送信する](./media/data-lake-storage-data-scenarios/egress-data.png "Data Lake Storage Gen2 からデータを送信する")

Data Lake Storage Gen2 からのデータのダウンロードに使用できるツールの一覧を次に示します。

|ツール | ガイダンス |
|---|--|
|Azure Data Factory | [Azure Data Factory の Copy アクティビティ](../../data-factory/copy-activity-overview.md) |
|Apache DistCp | [Distcp を使用して Azure Storage Blob と Azure Data Lake Storage Gen2 の間でデータをコピーする](./data-lake-storage-use-distcp.md) |
|Azure ストレージ エクスプローラー|[Azure Storage Explorer を使用して Azure Data Lake Storage Gen2 のディレクトリ、ファイル、ACL を管理する](data-lake-storage-explorer.md)|
|AzCopy ツール|[AzCopy と Blob Storage でデータを転送する](../common/storage-use-azcopy-v10.md#transfer-data)|