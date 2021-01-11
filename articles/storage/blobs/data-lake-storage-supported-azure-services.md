---
title: Azure Data Lake Storage Gen2 がサポートされている Azure のサービス | Microsoft Docs
description: Azure Data Lake Storage Gen2 と統合される Azure のサービスについて説明します
author: normesta
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: conceptual
ms.date: 08/05/2020
ms.author: normesta
ms.reviewer: stewu
ms.openlocfilehash: b185b6e7706b2049f2821400db9d810588000419
ms.sourcegitcommit: 7fe8df79526a0067be4651ce6fa96fa9d4f21355
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/06/2020
ms.locfileid: "87848215"
---
# <a name="azure-services-that-support-azure-data-lake-storage-gen2"></a>Azure Data Lake Storage Gen2 がサポートされている Azure のサービス

Azure のサービスを使用して、データを取り込み、分析を実行し、ビジュアル表現を作成することができます。 この記事では、サポートされている Azure のサービスの一覧を示して、そのサポート レベルを開示し、これらのサービスを Azure Data Lake Storage Gen2 で使用するときに役立つ記事へのリンクを提供します。

## <a name="supported-azure-services"></a>サポート対象の Azure サービス

次の表で、Azure Data Lake Storage Gen2 で使用できる Azure のサービスを示します。 サポートは継続的に拡張されるため、この表に示されている項目は時間の経過と共に変化します。

> [!NOTE]
> サポート レベルは、サービスが Data Lake Storage Gen2 でどのようにサポートされるかのみを示します。

|Azure サービス |サポート レベル |Azure AD |共有キー| 関連記事 |
|---------------|-------------------|---|---|---|
|Azure Data Factory|一般公開|はい|はい|[Load data into Azure Data Lake Storage Gen2 with Azure Data Factory (Azure Data Factory を使用して Azure Data Lake Storage Gen2 内にデータを読み込む)](https://docs.microsoft.com/azure/data-factory/load-azure-data-lake-storage-gen2?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)|
|Azure Databricks|一般公開|はい|はい|[Azure Databricks で使用する](https://docs.azuredatabricks.net/data/data-sources/azure/azure-datalake-gen2.html) <br> [クイック スタート: Azure Databricks を使用して Azure Data Lake Storage Gen2 のデータを分析する](data-lake-storage-quickstart-create-databricks-account.md) <br>[チュートリアル:Azure Databricks を使用してデータの抽出、変換、読み込みを行う](https://docs.microsoft.com/azure/azure-databricks/databricks-extract-load-sql-data-warehouse) <br>[チュートリアル:Spark を使用して Azure Databricks で Data Lake Storage Gen2 のデータにアクセスする](data-lake-storage-use-databricks-spark.md)|
|Azure Event Hub|一般公開|いいえ|はい|[Azure Event Hubs で Azure Blob Storage または Azure Data Lake Storage にイベントをキャプチャする](https://docs.microsoft.com/azure/event-hubs/event-hubs-capture-overview)|
|Azure Event Grid|一般公開|はい|はい|[チュートリアル:Databricks Delta テーブルを更新する Data Lake キャプチャ パターンを実装する](data-lake-storage-events.md)|
|Azure Logic Apps|一般公開|いいえ|はい|[概要 - Azure Logic Apps とは](https://docs.microsoft.com/azure/logic-apps/logic-apps-overview)|
|Azure Machine Learning|一般公開|はい|はい|[Azure ストレージ サービスのデータにアクセスする](https://docs.microsoft.com/azure/machine-learning/how-to-access-data)|
|Azure Stream Analytics|一般公開|はい|はい|[クイック スタート: Azure Portal を使用して Stream Analytics ジョブを作成する](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-quick-create-portal) <br> [Azure Data Lake Gen2 に出力する](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-define-outputs#blob-storage-and-azure-data-lake-gen2)|
|Data Box|一般公開|いいえ|はい|[Azure Data Box を使用してオンプレミス HDFS ストアから Azure Storage にデータを移行する](data-lake-storage-migrate-on-premises-hdfs-cluster.md)|
|HDInsight |一般公開|はい|はい|[Azure HDInsight クラスターで Azure Data Lake Storage Gen2 を使用する](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-use-data-lake-storage-gen2?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)<br>[Data Lake Storage Gen2 で HDFS CLI を使用する](data-lake-storage-use-hdfs-data-lake-storage.md) <br>[チュートリアル:Azure HDInsight の Apache Hive を使用したデータの抽出、変換、および読み込み](data-lake-storage-tutorial-extract-transform-load-hive.md)|
|IoT Hub |一般公開|いいえ|はい|[IoT Hub メッセージ ルーティングを使用して device-to-cloud メッセージを別のエンドポイントに送信する](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-messages-d2c)|
|Power BI|一般公開|はい|はい|[Power BI を使用して Data Lake Storage Gen2 のデータを分析する](https://docs.microsoft.com/power-query/connectors/datalakestorage)|
|Azure Synapse Analytics (旧称 SQL Data Warehouse)|一般公開|はい|はい|[Azure Synapse Analytics (旧称 SQL Data Warehouse) と共に使用する](https://docs.microsoft.com/azure/sql-database/sql-database-vnet-service-endpoint-rule-overview?toc=%2fazure%2fstorage%2fblobs%2ftoc.json#azure-sql-data-warehouse-polybase)|
|SQL Server Integration Services (SSIS)|一般公開|はい|はい|[Azure Storage 接続マネージャー](https://docs.microsoft.com/sql/integration-services/connection-manager/azure-storage-connection-manager?view=sql-server-2017)|
|Azure Data Explorer|一般公開|はい|はい|[Azure Data Explorer を使用して Azure Data Lake でデータのクエリを実行する](https://docs.microsoft.com/azure/data-explorer/data-lake-query-data)|
|Azure Cognitive Search|プレビュー|はい|はい|[Azure Data Lake Storage Gen2 ドキュメントのインデックスと検索 (プレビュー)](https://docs.microsoft.com/azure/search/search-howto-index-azure-data-lake-storage)|
|Azure Content Delivery Network|まだサポートされていません|適用なし|適用なし|[Azure Data Lake Storage Gen2 ドキュメントのインデックスと検索 (プレビュー)](https://docs.microsoft.com/azure/cdn/cdn-overview)|


## <a name="see-also"></a>関連項目

- [Azure Data Lake Storage Gen2 に関する既知の問題](data-lake-storage-known-issues.md)
- [Azure Data Lake Storage Gen2 で使用できる BLOB ストレージ機能](data-lake-storage-supported-blob-storage-features.md)
- [Data Lake Storage Gen2 がサポートされているオープン ソース プラットフォーム](data-lake-storage-supported-open-source-platforms.md)
- [Azure Data Lake Storage のマルチプロトコル アクセス](data-lake-storage-multi-protocol-access.md)