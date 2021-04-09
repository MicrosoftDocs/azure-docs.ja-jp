---
title: Data Lake Storage Gen1 とその他の Azure サービスの統合
description: Azure Data Lake Storage Gen1 を他の Azure サービスと統合する方法を理解します。
author: twooley
ms.service: data-lake-store
ms.topic: conceptual
ms.date: 05/29/2018
ms.author: twooley
ms.openlocfilehash: 929853f4dbedca7034c8e2a51e6231651a2dd08f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "96461654"
---
# <a name="integrating-azure-data-lake-storage-gen1-with-other-azure-services"></a>Azure Data Lake Storage Gen1 と他の Azure サービスを統合する
Azure Data Lake Storage Gen1 を他の Azure サービスと連携させれば、さまざまなシナリオが可能になります。 次の記事では、Data Lake Storage Gen1 と統合できるサービスについてまとめてあります。

## <a name="use-data-lake-storage-gen1-with-azure-hdinsight"></a>Data Lake Storage Gen1 を Azure HDInsight と共に使用する
HDFS に準拠しているストレージとして Data Lake Storage Gen1 を使用する [Azure HDInsight](https://azure.microsoft.com/documentation/learning-paths/hdinsight-self-guided-hadoop-training/) クラスターにプロビジョニングできます。 このリリースでは、Windows と Linux 上の Hadoop または Storm クラスターの場合、Data Lake Storage Gen1 は追加ストレージとしてのみ利用できます。 このようなクラスターでは、依然として Azure Storage (WASB) がデフォルト ストレージとして使用されます。 一方、Windows と Linux 上の HBase クラスターの場合、Data Lake Storage Gen1 を既定のストレージ、追加ストレージ、またはその両方として使用できます。

Data Lake Storage Gen1 を使用する HDInsight クラスターをプロビジョニングする方法については、次をご覧ください。

* [Azure portal を使用して、Data Lake Storage Gen1 を使用する HDInsight クラスターをプロビジョニングする](data-lake-store-hdinsight-hadoop-use-portal.md)
* [Azure PowerShell を使用して、Data Lake Storage Gen1 を既定のストレージとして使用する HDInsight クラスターをプロビジョニングする](data-lake-store-hdinsight-hadoop-use-powershell-for-default-storage.md)
* [Azure PowerShell を使用して、Data Lake Storage Gen1 を追加のストレージとして使用する HDInsight クラスターをプロビジョニングする](data-lake-store-hdinsight-hadoop-use-powershell.md)

## <a name="use-data-lake-storage-gen1-with-azure-data-lake-analytics"></a>Data Lake Storage Gen1 を Azure Data Lake Analytics と共に使用する
[Azure Data Lake Analytics](../data-lake-analytics/data-lake-analytics-overview.md) を利用すれば、クラウド スケールでビッグ データを操作できます。 リソースを動的にプロビジョニングし、サポートされているさまざまなデータ ソース (Data Lake Storage Gen1 を含む) に保存可能なテラバイト単位の、さらにはエクサバイト単位の分析を可能にします。 Data Lake Analytics は Data Lake Storage Gen1 と連動するように最適化されており、ビッグ データの作業負荷に対して最高レベルのパフォーマンス、スループット、並列化を提供します。

Data Lake Storage Gen1 を Data Lake Analytics と共に使用する方法については、[Data Lake Storage Gen1 を使用した Data Lake Analytics の開始](../data-lake-analytics/data-lake-analytics-get-started-portal.md)に関する記事をご覧ください。

## <a name="use-data-lake-storage-gen1-with-azure-data-factory"></a>Data Lake Storage Gen1 を Azure Data Factory と共に使用する
[Azure Data Factory](https://azure.microsoft.com/services/data-factory/) を使用し、Azure テーブル、Azure SQL Database、Azure SQL DataWarehouse、Azure Storage BLOB、オンプレミス データベースからデータを取り込むことができます。 Azure エコシステムの第一級オブジェクトである Azure Data Factory を使用し、これらのソースから Data Lake Storage Gen1 へのデータ取り込みを調整できます。

Azure Data Factory を Data Lake Storage Gen1 と共に使用する方法については、[Data Factory を使用した Data Lake Storage Gen1 との間のデータの移動](../data-factory/connector-azure-data-lake-store.md)に関する記事をご覧ください。

## <a name="copy-data-from-azure-storage-blobs-into-data-lake-storage-gen1"></a>Azure Storage Blob から Data Lake Storage Gen1 へのデータのコピー
Azure Data Lake Storage Gen1 には、AdlCopy というコマンド ライン ツールがあります。このツールを使用すると、Azure Blob Storage のデータを Data Lake Storage Gen1 アカウントにコピーできます。 詳しくは、[Azure Storage Blob から Data Lake Storage Gen1 へのデータのコピー](data-lake-store-copy-data-azure-storage-blob.md)に関する記事をご覧ください。

## <a name="copy-data-between-azure-sql-database-and-data-lake-storage-gen1"></a>Azure SQL Database と Data Lake Storage Gen1 の間でデータをコピーする
Apache Sqoop を使用して Azure SQL Database と Data Lake Storage Gen1 の間でデータのインポートおよびエクスポートを行うことができます。 詳細については、[Sqoop を使用した Data Lake Storage Gen1 と Azure SQL Database の間のデータのコピー](data-lake-store-data-transfer-sql-sqoop.md)に関する記事を参照してください。

## <a name="use-data-lake-storage-gen1-with-stream-analytics"></a>Data Lake Storage Gen1 を Stream Analytics と共に使用する
Azure Stream Analytics を使用してストリーミングされたデータを保存する場合は、出力の 1 つとして Data Lake Storage Gen1 を使用できます。 詳しくは、[Azure Stream Analytics を使用した Azure Storage Blob から Data Lake Storage Gen1 へのデータ ストリーム](data-lake-store-stream-analytics.md)に関する記事をご覧ください。

## <a name="use-data-lake-storage-gen1-with-power-bi"></a>Data Lake Storage Gen1 を Power BI と共に使用する
Power BI を使用して、Data Lake Storage Gen1 アカウントからデータをインポートし、そのデータを分析および視覚化することができます。 詳しくは、[Power BI を使用した Data Lake Storage Gen1 のデータの分析](data-lake-store-power-bi.md)に関する記事をご覧ください。

## <a name="use-data-lake-storage-gen1-with-data-catalog"></a>Data Lake Storage Gen1 を Data Catalog と共に使用する
組織全体でデータを検出できるように、Azure Data Catalog に Data Lake Storage Gen1 のデータを登録することができます。 詳しくは、[Azure Data Catalog への Data Lake Storage Gen1 のデータの登録](data-lake-store-with-data-catalog.md)に関する記事をご覧ください。

## <a name="use-data-lake-storage-gen1-with-sql-server-integration-services-ssis"></a>Data Lake Storage Gen1 を SQL Server Integration Services (SSIS) と共に使用する
SSIS で Data Lake Storage Gen1 接続マネージャーを使用して、SSIS パッケージを Data Lake Storage Gen1 と接続することができます。 詳しくは、[SSIS での Data Lake Storage Gen1 の使用](/sql/integration-services/connection-manager/azure-data-lake-store-connection-manager)に関するページをご覧ください。

## <a name="use-data-lake-storage-gen1-with-azure-synapse-analytics"></a>Data Lake Storage Gen1 を Azure Synapse Analytics と共に使用する
PolyBase を使用して、Data Lake Storage Gen1 から Azure Synapse Analytics にデータを読み込むことができます。 詳しくは、[Azure Synapse Analytics での Data Lake Storage Gen1 の使用](../synapse-analytics/sql-data-warehouse/sql-data-warehouse-load-from-azure-data-lake-store.md)に関する記事をご覧ください。

## <a name="use-data-lake-storage-gen1-with-azure-event-hubs"></a>Data Lake Storage Gen1 を Azure Event Hubs と共に使用する
Azure Data Lake Storage Gen1 を使用して、Azure Event Hubs によって受信されたデータをアーカイブしてキャプチャできます。 詳しくは、[Azure Event Hubs での Data Lake Storage Gen1 の使用](data-lake-store-archive-eventhub-capture.md)に関する記事をご覧ください。

## <a name="see-also"></a>関連項目
* [Azure Data Lake Storage Gen1 の概要](data-lake-store-overview.md)
* [ポータルを使用して Data Lake Storage Gen1 の使用を開始する](data-lake-store-get-started-portal.md)
* [PowerShell を使用して Data Lake Storage Gen1 の使用を開始する](data-lake-store-get-started-powershell.md)