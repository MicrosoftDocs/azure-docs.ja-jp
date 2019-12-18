---
title: Data Factory - .NET API の変更ログ
description: Azure Data Factory の特定のバージョンの .NET API での重大な変更、機能の追加、バグ修正などについて説明します。
services: data-factory
documentationcenter: ''
author: djpmsft
ms.author: daperlov
manager: jroth
ms.reviewer: maghan
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
robots: noindex
ms.date: 01/22/2018
ms.openlocfilehash: dbbbdebdcf1db7afe485166f5744f2291b757d50
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/10/2019
ms.locfileid: "74979004"
---
# <a name="azure-data-factory---net-api-change-log"></a>Azure Data Factory - .NET API の変更ログ
> [!NOTE]
> この記事は、Data Factory のバージョン 1 に適用されます。 

この記事では、特定のバージョンの Azure Data Factory SDK の変更に関する情報を提供します。 Azure Data Factory の最新の NuGet パッケージについては、 [こちら](https://www.nuget.org/packages/Microsoft.Azure.Management.DataFactories)

## <a name="version-4110"></a>バージョン 4.11.0
機能の追加:

* 次のリンクされたサービスの種類が追加されています。
  * [OnPremisesMongoDbLinkedService](https://msdn.microsoft.com/library/mt765129.aspx)
  * [AmazonRedshiftLinkedService](https://msdn.microsoft.com/library/mt765121.aspx)
  * [AwsAccessKeyLinkedService](https://msdn.microsoft.com/library/mt765144.aspx)
* 次のデータセットの型が追加されています。
  * [MongoDbCollectionDataset](https://msdn.microsoft.com/library/mt765145.aspx)
  * [AmazonS3Dataset](https://msdn.microsoft.com/library/mt765112.aspx)
* 次のコピー ソースの種類が追加されています。
  * [MongoDbSource](https://msdn.microsoft.com/library/mt765123.aspx)

## <a name="version-4100"></a>バージョン 4.10.0
* 次の省略可能なプロパティが TextFormat に追加されました。
  * [SkipLineCount](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.models.textformat.skiplinecount.aspx)
  * [FirstRowAsHeader](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.models.textformat.firstrowasheader.aspx)
  * [TreatEmptyAsNull](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.models.textformat.treatemptyasnull.aspx)
* 次のリンクされたサービスの種類が追加されています。
  * [OnPremisesCassandraLinkedService](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.models.onpremisescassandralinkedservice.aspx)
  * [SalesforceLinkedService](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.models.salesforcelinkedservice.aspx)
* 次のデータセットの型が追加されています。
  * [OnPremisesCassandraTableDataset](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.models.onpremisescassandratabledataset.aspx)
* 次のコピー ソースの種類が追加されています。
  * [CassandraSource](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.models.cassandrasource.aspx)
* [WebServiceInputs](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.models.azuremlbatchexecutionactivity.webserviceinputs.aspx) プロパティを AzureMLBatchExecutionActivity に追加しました。
  * Azure Machine Learning の実験に複数の Web サービスの入力を渡すことができるようになりました。

## <a name="version-491"></a>バージョン 4.9.1
### <a name="bug-fix"></a>バグの修正
* [WebLinkedService](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.models.weblinkedservice.authenticationtype.aspx)の Web API ベースの認証が非推奨になりました。

## <a name="version-490"></a>バージョン 4.9.0
### <a name="feature-additions"></a>機能の追加
* [EnableStaging](https://msdn.microsoft.com/library/mt767916.aspx) プロパティと [StagingSettings](https://msdn.microsoft.com/library/mt767918.aspx) プロパティを CopyActivity に追加しました。 機能の詳細については、「 [ステージング コピー](data-factory-copy-activity-performance.md#staged-copy) 」を参照してください。

### <a name="bug-fix"></a>バグの修正
* [ActivityWindowsByActivityListParameters](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.activitywindowsbyactivitylistparameters.aspx) インスタンスを受け取る [ActivityWindowOperationExtensions.List](https://msdn.microsoft.com/library/mt767915.aspx) メソッドのオーバーロードを導入しました。
* CopySink で [WriteBatchSize](https://msdn.microsoft.com/library/dn884293.aspx) と [WriteBatchTimeout](https://msdn.microsoft.com/library/dn884245.aspx) が、省略可能としてマークされました。

## <a name="version-480"></a>バージョン 4.8.0
### <a name="feature-additions"></a>機能の追加
* 次の省略可能なプロパティがコピー アクティビティの種類に追加され、コピー パフォーマンスの調整が有効になりました。
  * [ParallelCopies](https://msdn.microsoft.com/library/mt767910.aspx)
  * [CloudDataMovementUnits](https://msdn.microsoft.com/library/mt767912.aspx)

## <a name="version-470"></a>Version 4.7.0
### <a name="feature-additions"></a>機能の追加
* Optimized Row Columnar (ORC) 形式でファイルをコピーするための新しい StorageFormat タイプ ( [OrcFormat](https://msdn.microsoft.com/library/mt723391.aspx) ) を追加しました。
* [AllowPolyBase](https://msdn.microsoft.com/library/mt723396.aspx) プロパティと PolyBaseSettings プロパティを SqlDWSink に追加しました。
  * PolyBase を使用して SQL Data Warehouse にデータをコピーすることができます。

## <a name="version-461"></a>Version 4.6.1
### <a name="bug-fixes"></a>バグの修正
* アクティビティ ウィンドウをリストするための HTTP 要求を修正します。
  * リソース グループの名前とデータ ファクトリの名前を要求のペイロードから削除します。

## <a name="version-460"></a>バージョン 4.6.0
### <a name="feature-additions"></a>機能の追加
* 次のプロパティが [PipelineProperties](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.pipelineproperties_properties.aspx)に追加されました。
  * [PipelineMode](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.pipelineproperties.pipelinemode.aspx)
  * [ExpirationTime](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.pipelineproperties.expirationtime.aspx)
  * [データセット](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.pipelineproperties.datasets.aspx)
* 次のプロパティが [PipelineRuntimeInfo](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.common.models.pipelineruntimeinfo.aspx)に追加されました。
  * [PipelineState](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.common.models.pipelineruntimeinfo.pipelinestate.aspx)
* JSON 形式のデータを持つデータセットを定義するために、新しい [StorageFormat](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.models.storageformat.aspx) 型の [JsonFormat](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.models.jsonformat.aspx) 型が追加されました。

## <a name="version-450"></a>バージョン 4.5.0
### <a name="feature-additions"></a>機能の追加
* [アクティビティ ウィンドウの一覧操作](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.activitywindowoperationsextensions.aspx)が追加されました。
  * エンティティ型 (つまり、データ ファクトリ、データセット、パイプライン、およびアクティビティ) に基づくフィルターを使用してアクティビティ ウィンドウを取得するメソッドが追加されました。
* 次のリンクされたサービスの種類が追加されています。
  * [ODataLinkedService](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.odatalinkedservice.aspx)、[WebLinkedService](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.weblinkedservice.aspx)
* 次のデータセットの型が追加されています。
  * [ODataResourceDataset](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.odataresourcedataset.aspx)、[WebTableDataset](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.webtabledataset.aspx)
* 次のコピー ソースの種類が追加されています。     
  * [WebSource](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.websource.aspx)

## <a name="version-440"></a>バージョン 4.4.0
### <a name="feature-additions"></a>機能の追加
* 次のリンクされたサービスの種類が、コピー アクティビティのデータ ソースおよびシンクとして追加されています。
  * [AzureStorageSasLinkedService](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.models.azurestoragesaslinkedservice.aspx)。 概念情報と例については、「 [Azure Storage SAS のリンクされたサービス](data-factory-azure-blob-connector.md#azure-storage-sas-linked-service) 」を参照してください。

## <a name="version-430"></a>バージョン 4.3.0
### <a name="feature-additions"></a>機能の追加
* 次のリンクされたサービスの種類が、コピー アクティビティのデータ ソースとして追加されています。
  * [HdfsLinkedService](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.models.hdfslinkedservice.aspx)。 概念情報と例については、 [Data Factory を使用した HDFS からのデータの移動](data-factory-hdfs-connector.md) に関する記事を参照してください。
  * [OnPremisesOdbcLinkedService](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.models.onpremisesodbclinkedservice.aspx)。 概念情報と例については、「 [Azure Data Factory を使用して ODBC データ ストアからデータを移動する](data-factory-odbc-connector.md) 」を参照してください。

## <a name="version-420"></a>バージョン 4.2.0
### <a name="feature-additions"></a>機能の追加
* 新しいアクティビティの種類 [AzureMLUpdateResourceActivity](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.azuremlupdateresourceactivity.aspx) が追加されました。 アクティビティの詳細については、「[更新リソース アクティビティを使用して Azure ML モデルを更新する](data-factory-azure-ml-batch-execution-activity.md)」を参照してください。
* 新しい省略可能なプロパティ [updateResourceEndpoint](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.azuremllinkedservice.updateresourceendpoint.aspx) が [AzureMLLinkedService クラス](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.azuremllinkedservice.aspx)に追加されました。
* [LongRunningOperationInitialTimeout](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.datafactorymanagementclient.longrunningoperationinitialtimeout.aspx) プロパティと [LongRunningOperationRetryTimeout](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.datafactorymanagementclient.longrunningoperationretrytimeout.aspx) プロパティが [DataFactoryManagementClient](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.datafactorymanagementclient.aspx) クラスに追加されました。
* Data Factory サービスへのクライアントの呼び出しのタイムアウトの構成が許可されました。

## <a name="version-410"></a>バージョン 4.1.0
### <a name="feature-additions"></a>機能の追加
* 次のリンクされたサービスの種類が追加されています。
  * [AzureDataLakeStoreLinkedService](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.azuredatalakestorelinkedservice.aspx)
  * [AzureDataLakeAnalyticsLinkedService](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.azuredatalakeanalyticslinkedservice.aspx)
* 次のアクティビティの種類が追加されています。
  * [DataLakeAnalyticsUSQLActivity](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.datalakeanalyticsusqlactivity.aspx)
* 次のデータセットの型が追加されています。
  * [AzureDataLakeStoreDataset](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.azuredatalakestoredataset.aspx)
* 次のコピー アクティビティ用のソースとシンクの種類が追加されています。
  * [AzureDataLakeStoreSource](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.azuredatalakestoresource.aspx)
  * [AzureDataLakeStoreSink](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.azuredatalakestoresink.aspx)

## <a name="version-401"></a>バージョン 4.0.1
### <a name="breaking-changes"></a>重大な変更
次のクラスの名前が変更されました。 新しい名前は、4.0.0 リリースより前の、クラスの元の名前です。

| 4\.0.0 での名前 | 4\.0.1 での名前 |
|:--- |:--- |
| AzureSqlDataWarehouseDataset |[AzureSqlDataWarehouseTableDataset](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.azuresqldatawarehousetabledataset.aspx) |
| AzureSqlDataset |[AzureSqlTableDataset](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.azuresqltabledataset.aspx) |
| AzureDataset |[AzureTableDataset](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.azuretabledataset.aspx) |
| OracleDataset |[OracleTableDataset](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.oracletabledataset.aspx) |
| RelationalDataset |[RelationalTableDataset](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.relationaltabledataset.aspx) |
| SqlServerDataset |[SqlServerTableDataset](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.sqlservertabledataset.aspx) |

## <a name="version-400"></a>バージョン 4.0.0
### <a name="breaking-changes"></a>重大な変更
* 次のクラスとインターフェイスの名前が変更されました。

| 以前の名前 | 新しい名前 |
|:--- |:--- |
| ITableOperations |[IDatasetOperations](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.idatasetoperations.aspx) |
| テーブル |[Dataset](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.dataset.aspx) |
| TableProperties |[DatasetProperties](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.datasetproperties.aspx) |
| TableTypeProprerties |[DatasetTypeProperties](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.datasettypeproperties.aspx) |
| TableCreateOrUpdateParameters |[DatasetCreateOrUpdateParameters](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.datasetcreateorupdateparameters.aspx) |
| TableCreateOrUpdateResponse |[DatasetCreateOrUpdateResponse](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.datasetcreateorupdateresponse.aspx) |
| TableGetResponse |[DatasetGetResponse](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.datasetgetresponse.aspx) |
| TableListResponse |[DatasetListResponse](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.datasetlistresponse.aspx) |
| CreateOrUpdateWithRawJsonContentParameters |[DatasetCreateOrUpdateWithRawJsonContentParameters](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.datasetcreateorupdatewithrawjsoncontentparameters.aspx) |

* **List** メソッドでページングされた結果が返されるようになりました。 応答に空でない **NextLink** プロパティが含まれている場合、クライアント アプリケーションは、すべてのページが返されるまで次のページを取得し続ける必要があります。  たとえば次のようになります。

    ```csharp
    PipelineListResponse response = client.Pipelines.List("ResourceGroupName", "DataFactoryName");
    var pipelines = new List<Pipeline>(response.Pipelines);

    string nextLink = response.NextLink;
    while (!string.IsNullOrEmpty(nextLink))
    {
        PipelineListResponse nextResponse = client.Pipelines.ListNext(nextLink);
        pipelines.AddRange(nextResponse.Pipelines);

        nextLink = nextResponse.NextLink;
    }
    ```
* **List** パイプライン API では、全詳細ではなく、パイプラインの概要だけが返されます。 たとえば、パイプライン概要のアクティビティには、名前と種類だけが含まれます。

### <a name="feature-additions"></a>機能の追加
* [SqlDWSink](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.models.sqldwsink.aspx) クラスでは、Azure SQL Data Warehouse へのべき等コピーをサポートするために、**SliceIdentifierColumnName** と **SqlWriterCleanupScript** の 2 つの新しいプロパティがサポートされるようになりました。 これらのプロパティの詳細については、[Azure SQL Data Warehouse](data-factory-azure-sql-data-warehouse-connector.md) に関する記事をご覧ください。
* コピー アクティビティの一環として、Azure SQL Database ソースと Azure SQL Data Warehouse ソースに対するストアド プロシージャの実行がサポートされるようになりました。 [SqlSource](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.models.sqlsource.aspx) および [SqlDWSource](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.models.sqldwsource.aspx) クラスには、プロパティ **SqlReaderStoredProcedureName** および **StoredProcedureParameters** があります。 これらのプロパティの詳細については、Azure.com の [Azure SQL Database](data-factory-azure-sql-connector.md#sqlsource) と [Azure SQL Data Warehouse](data-factory-azure-sql-data-warehouse-connector.md#sqldwsource) に関する記事をご覧ください。  
