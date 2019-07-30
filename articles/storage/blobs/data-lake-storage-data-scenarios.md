---
title: Azure Data Lake Storage Gen2 に関するデータ シナリオ | Microsoft Docs
description: Data Lake Storage Gen2 (旧称 Azure Data Lake Store) でのデータの取り込み、処理、ダウンロード、視覚化に使用するさまざまなシナリオとツールについて説明します
services: storage
author: normesta
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: conceptual
ms.date: 07/23/2019
ms.author: normesta
ms.openlocfilehash: 010b7bc38caf83c12dd0d8b8e731fdbad6e45256
ms.sourcegitcommit: 198c3a585dd2d6f6809a1a25b9a732c0ad4a704f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/23/2019
ms.locfileid: "68422868"
---
# <a name="using-azure-data-lake-storage-gen2-for-big-data-requirements"></a>Data Lake Storage Gen2 を使用してビッグ データの要件に対応する

ビッグ データの処理には主に 4 つの段階があります。

> [!div class="checklist"]
> * データ ストアへの大量のデータの取り込み (リアルタイムまたは一括)
> * データの処理
> * データのダウンロード
> * データの視覚化

最初に、ストレージ アカウントとファイル システムを作成します。 次に、データへのアクセス権を付与します。 この記事の最初の数セクションは、これらのタスクを実行するのに役立ちます。 残りのセクションでは、各処理フェーズのオプションとツールに注目します。

## <a name="create-a-data-lake-storage-gen2-account"></a>Data Lake Storage Gen2 アカウントを作成する

Data Lake Storage Gen2 アカウントは、階層型名前空間を持つストレージ アカウントです。 

作成方法については、「[クイック スタート: Azure Data Lake Storage Gen2 ストレージ アカウントを作成する](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-quickstart-create-account?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)」を参照してください。

## <a name="create-a-file-system"></a>ファイル システムを作成する

"*ファイル システム*" は、フォルダーとファイルのコンテナーです。 ストレージ アカウントへのデータの取り込みを始めるには、そのうちの少なくとも 1 つが必要です。  その作成に使用できるツールの一覧を次に示します。

|ツール | ガイダンス |
|---|--|
|Azure ストレージ エクスプローラー | [Storage Explorer を使用してファイル システムを作成する](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-explorer#create-a-file-system) |
|AzCopy | [AzCopyV10 を使用して BLOB コンテナーまたはファイル共有を作成する](https://docs.microsoft.com/azure/storage/common/storage-use-azcopy-v10#transfer-files)|
|Hadoop ファイル システム (HDFS) コマンド ライン インターフェイス (CLI) と HDInsight |[HDFS と HDInsight を使用してファイル システムを作成する](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-use-hdfs-data-lake-storage?toc=%2fazure%2fstorage%2fblobs%2ftoc.json#create-a-file-system) |
|Azure Databricks ノートブックでのコード|[ストレージ アカウント ファイル システムを作成する (Scala)](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-quickstart-create-databricks-account?toc=%2fazure%2fstorage%2fblobs%2ftoc.json#create-storage-account-file-system) <br><br> [ファイル システムを作成してマウントする (Python)](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-use-databricks-spark?toc=%2fazure%2fstorage%2fblobs%2ftoc.json#create-a-file-system-and-mount-it)|

Storage Explorer または AzCopy を使用してファイル システムを作成するのが最も簡単です。 HDInsight および Databricks を使用してファイル システムを作成する場合は、もう少し手間がかかります。 ただし、HDInsight クラスターまたは Databricks クラスターを使用してデータを処理することを計画している場合は、クラスターを最初に作成してから、HDFS CLI を使用してファイル システムを作成できます。  

## <a name="grant-access-to-the-data"></a>データへのアクセス権を付与する

データの取り込みを始める前に、アカウントおよびアカウント内のデータへの適切なアクセス許可を設定します。

アクセス権を付与するには 3 つの方法があります。

* ユーザー、グループ、ユーザー マネージド ID、またはサービス プリンシパルに、次のいずれかのロールを割り当てます。

  [ストレージ BLOB データ閲覧者](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#storage-blob-data-reader)

  [ストレージ BLOB データ共同作成者](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#storage-queue-data-contributor)

  [ストレージ BLOB データ所有者](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#storage-blob-data-owner)

* Shared Access Signature (SAS) トークンを使用します。

* ストレージ アカウント キーを使用します。

次の表では、各 Azure サービスまたはツールに対するアクセス権を付与する方法を示します。

|ツール | アクセス権を付与する方法 | ガイダンス |
|---|--|---|
|ストレージ エクスプローラー| ユーザーおよびグループにロールを割り当てる | [Azure Active Directory を使ってユーザーに管理者と管理者以外のロールを割り当てる](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-users-assign-role-azure-portal) |
|AzCopy| ユーザーおよびグループにロールを割り当てる <br>**or**<br> SAS トークンを使用する| [Azure Active Directory を使ってユーザーに管理者と管理者以外のロールを割り当てる](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-users-assign-role-azure-portal)<br><br>[SAS を簡単に作成して Azure Storage からファイルをダウンロードする – Azure Storage Explorer の使用](https://blogs.msdn.microsoft.com/jpsanders/2017/10/12/easily-create-a-sas-to-download-a-file-from-azure-storage-using-azure-storage-explorer/)|
|Apache DistCp | ユーザー割り当てマネージド ID にロールを割り当てる | [Data Lake Storage Gen2 を使用する HDInsight クラスターの作成](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-use-data-lake-storage-gen2) |
|Azure Data Factory| ユーザー割り当てマネージド ID にロールを割り当てる<br>**or**<br> サービス プリンシパルにロールを割り当てる<br>**or**<br> ストレージ アカウント キーを使用する | [リンクされたサービスのプロパティ](https://docs.microsoft.com/azure/data-factory/connector-azure-data-lake-storage#linked-service-properties) |
|Azure HDInsight| ユーザー割り当てマネージド ID にロールを割り当てる | [Data Lake Storage Gen2 を使用する HDInsight クラスターの作成](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-use-data-lake-storage-gen2)|
|Azure Databricks| サービス プリンシパルにロールを割り当てる | [方法: リソースにアクセスできる Azure AD アプリケーションとサービス プリンシパルをポータルで作成する](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal)|

特定のファイルやフォルダーへのアクセス権を付与するには、次の記事をご覧ください。

* [Azure Data Lake Storage Gen2 で Azure Storage Explorer を使用してファイルとディレクトリ レベルのアクセス許可を設定する](https://review.docs.microsoft.com/azure/storage/blobs/data-lake-storage-how-to-set-permissions-storage-explorer)

* [ファイルとディレクトリのアクセス制御リスト](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-access-control#access-control-lists-on-files-and-directories)

セキュリティの他の側面を設定する方法については、「[Azure Data Lake Storage Gen2 セキュリティ ガイド](https://docs.microsoft.com/azure/storage/common/storage-data-lake-storage-security-guide?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)」をご覧ください。

## <a name="ingest-the-data"></a>データを取り込む

このセクションでは、さまざまなデータ ソースと、そのデータを Data Lake Storage Gen2 アカウントに取り込む各種方法について説明します。

![Data Lake Storage Gen2 にデータを取り込む](./media/data-lake-storage-data-scenarios/ingest-data.png "Data Lake Storage Gen2 にデータを取り込む")

### <a name="ad-hoc-data"></a>アドホック データ

これは、ビッグ データ アプリケーションのプロトタイプ作成に使用される小規模なデータ セットを表します。 アドホック データの取り込み方法は、データ ソースに応じてさまざまです。 

アドホック データの取り込みに使用できるツールの一覧を次に示します。

| データ ソース | 取り込みに使用するツール |
| --- | --- |
| ローカル コンピューター |[Storage Explorer](https://azure.microsoft.com/features/storage-explorer/)<br><br>[AzCopy ツール](../common/storage-use-azcopy-v10.md)|
| Azure Storage BLOB |[Azure Data Factory](../../data-factory/connector-azure-data-lake-store.md)<br><br>[AzCopy ツール](../common/storage-use-azcopy-v10.md)<br><br>[HDInsight クラスター上で実行されている DistCp](data-lake-storage-use-distcp.md)|

### <a name="streamed-data"></a>ストリーミングされたデータ

これは、アプリケーション、デバイス、センサーなどのさまざまなソースによって生成されるデータを表します。Data Lake Storage Gen2 へのこのデータの取り込みには各種ツールを使用できます。 これらのツールでは通常、イベントごとにリアルタイムでデータを取り込み、処理します。その後、さらにデータを処理できるように、Data Lake Storage Gen2 にイベントを一括で書き込みます。

ストリーミングされたデータの取り込みに使用できるツールの一覧を次に示します。

|ツール | ガイダンス |
|---|--|
|Azure HDInsight Storm | [HDInsight 上の Apache Storm から Apache Hadoop HDFS に書き込む](https://docs.microsoft.com/azure/hdinsight/storm/apache-storm-write-data-lake-store) |

### <a name="relational-data"></a>リレーショナル データ

リレーショナル データベースのデータもソースとして扱うことができます。 一定の期間を経て、リレーショナル データベースには大量のデータが収集されます。ビッグ データのパイプラインを介して処理すると、これらのデータから重要な知見が得られます。 このようなデータを Data Lake Storage Gen2 に移動する場合は、次のツールを使用できます。

リレーショナル データの取り込みに使用できるツールの一覧を次に示します。

|ツール | ガイダンス |
|---|--|
|Azure Data Factory | [Azure Data Factory の Copy アクティビティ](https://docs.microsoft.com/azure/data-factory/copy-activity-overview) |

### <a name="web-server-log-data-upload-using-custom-applications"></a>Web サーバー ログ データ (カスタム アプリケーションを使用したアップロード)

Web サーバー ログ データの分析は、ビッグ データ アプリケーションの一般的な使用例で、大量のログ ファイルを Data Lake Storage Gen2 にアップロードすることを必要とするため、この種類のデータセットが特に呼び出されます。 このようなデータをアップロードするための独自のスクリプトまたはアプリケーションを記述するには、次のツールのいずれかを使用できます。

Web サーバー ログ データの取り込みに使用できるツールの一覧を次に示します。

|ツール | ガイダンス |
|---|--|
|Azure Data Factory | [Azure Data Factory の Copy アクティビティ](https://docs.microsoft.com/azure/data-factory/copy-activity-overview)  |

Web サーバー ログ データをアップロードする場合、または他の種類のデータ (ソーシャル センチメント データなど) をアップロードする場合には、独自のカスタム スクリプトやカスタム アプリケーションを記述することをお勧めします。 これにより、データをアップロードするコンポーネントをより大規模なビッグ データ アプリケーションの一部として含める柔軟性が得られるためです。 このコードは、スクリプトまたは単純なコマンド ライン ユーティリティの形をとる場合もあれば、ビッグ データの処理をビジネス アプリケーションまたはビジネス ソリューションに統合するために使用される場合もあります。

### <a name="data-associated-with-azure-hdinsight-clusters"></a>Azure HDInsight クラスターに関連付けられたデータ

ほとんどの種類の HDInsight クラスター (Hadoop、HBase、Storm) では、データ ストレージ リポジトリとして Data Lake Storage Gen2 がサポートされています。 HDInsight クラスターは Azure Storage Blob (WASB) のデータにアクセスします。 パフォーマンスを向上させるために、クラスターに関連付けられた Data Lake Storage Gen2 アカウントに WASB のデータをコピーできます。 データのコピーには次のツールを使用できます。

HDInsight クラスターに関連付けられたデータの取り込みに使用できるツールの一覧を次に示します。

|ツール | ガイダンス |
|---|--|
|Apache DistCp | [Distcp を使用して Azure Storage Blob と Azure Data Lake Storage Gen2 の間でデータをコピーする](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-use-distcp) |
|AzCopy ツール | [AzCopy を使用してデータを転送する](https://docs.microsoft.com/azure/storage/common/storage-use-azcopy-v10) |
|Azure Data Factory | [Azure Data Factory を使用して Azure Data Lake Storage Gen2 との間でデータをコピーする](https://docs.microsoft.com/azure/data-factory/load-azure-data-lake-storage-gen2) |

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
|Azure HDInsight | [Azure HDInsight クラスターで Azure Data Lake Storage Gen2 を使用する](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-use-data-lake-storage-gen2) |
|Azure Databricks | [Azure Data Lake Storage Gen2](https://docs.azuredatabricks.net/spark/latest/data-sources/azure/azure-datalake-gen2.html)<br><br>[クイック スタート:Azure Databricks を使用して Azure Data Lake Storage Gen2 のデータを分析する](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-quickstart-create-databricks-account?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)<br><br>[チュートリアル:Azure Databricks を使用してデータの抽出、変換、読み込みを行う](https://docs.microsoft.com/azure/azure-databricks/databricks-extract-load-sql-data-warehouse?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)|

## <a name="visualize-the-data"></a>データの視覚化

複数のサービスを組み合わせて使用することで、Data Lake Storage Gen2 に格納されたデータを視覚的に表現することができます。

![Data Lake Storage Gen2 のデータを視覚化する](./media/data-lake-storage-data-scenarios/visualize-data.png "Data Lake Storage Gen2 のデータを視覚化する")

* まず、[Azure Data Factory を使って、Data Lake Storage Gen2 から Azure SQL Data Warehouse にデータを移動](../../data-factory/copy-activity-overview.md)することができます。
* その後、 [Power BI を Azure SQL Data Warehouse と統合](../../sql-data-warehouse/sql-data-warehouse-get-started-visualize-with-power-bi.md) して、データを視覚的に表現することができます。

## <a name="download-the-data"></a>データをダウンロードする

次のようなシナリオでは、Azure Data Lake Storage Gen2 からデータをダウンロードしたり、移動したりすることもできます。

* 既存のデータ処理パイプラインとのインターフェイスとなる他のリポジトリにデータを移動する。 たとえば、Data Lake Storage Gen2 から Azure SQL Database またはオンプレミスの SQL Server にデータを移動できます。

* アプリケーション プロトタイプの作成中に IDE 環境で処理するために、ローカル コンピューターにデータをダウンロードする。

![Data Lake Storage Gen2 からデータを出力する](./media/data-lake-storage-data-scenarios/egress-data.png "Data Lake Storage Gen2 からデータを出力する")

Data Lake Storage Gen2 からのデータのダウンロードに使用できるツールの一覧を次に示します。

|ツール | ガイダンス |
|---|--|
|Azure Data Factory | [Azure Data Factory の Copy アクティビティ](https://docs.microsoft.com/azure/data-factory/copy-activity-overview) |
|Apache DistCp | [Distcp を使用して Azure Storage Blob と Azure Data Lake Storage Gen2 の間でデータをコピーする](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-use-distcp) |
