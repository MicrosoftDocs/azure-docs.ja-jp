---
title: Data Factory - .NET API の変更ログ
description: Azure Data Factory の特定のバージョンの .NET API での重大な変更、機能の追加、バグ修正などについて説明します。
author: dcstwh
ms.author: weetok
ms.reviewer: jonburchel
ms.service: data-factory
ms.topic: conceptual
robots: noindex
ms.date: 01/22/2018
ms.openlocfilehash: 2d292bbc820574d4360be51e93588f8f908f468d
ms.sourcegitcommit: f611b3f57027a21f7b229edf8a5b4f4c75f76331
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/22/2021
ms.locfileid: "104785599"
---
# <a name="azure-data-factory---net-api-change-log"></a>Azure Data Factory - .NET API の変更ログ
> [!NOTE]
> この記事は、Data Factory のバージョン 1 に適用されます。 

この記事では、特定のバージョンの Azure Data Factory SDK の変更に関する情報を提供します。 Azure Data Factory の最新の NuGet パッケージについては、 [こちら](https://www.nuget.org/packages/Microsoft.Azure.Management.DataFactories)

## <a name="version-4110"></a>バージョン 4.11.0
機能の追加:

* 次のリンクされたサービスの種類が追加されています。
  * [OnPremisesMongoDbLinkedService](/dotnet/api/microsoft.azure.management.datafactories.models.onpremisesmongodblinkedservice)
  * [AmazonRedshiftLinkedService](/dotnet/api/microsoft.azure.management.datafactories.models.amazonredshiftlinkedservice)
  * [AwsAccessKeyLinkedService](/dotnet/api/microsoft.azure.management.datafactories.models.awsaccesskeylinkedservice)
* 次のデータセットの型が追加されています。
  * [MongoDbCollectionDataset](/dotnet/api/microsoft.azure.management.datafactories.models.mongodbcollectiondataset)
  * [AmazonS3Dataset](/dotnet/api/microsoft.azure.management.datafactories.models.amazons3dataset)
* 次のコピー ソースの種類が追加されています。
  * [MongoDbSource](/dotnet/api/microsoft.azure.management.datafactories.models.mongodbsource)

## <a name="version-4100"></a>バージョン 4.10.0
* 次の省略可能なプロパティが TextFormat に追加されました。
  * [SkipLineCount](/dotnet/api/microsoft.azure.management.datafactories.models.textformat)
  * [FirstRowAsHeader](/dotnet/api/microsoft.azure.management.datafactories.models.textformat)
  * [TreatEmptyAsNull](/dotnet/api/microsoft.azure.management.datafactories.models.textformat)
* 次のリンクされたサービスの種類が追加されています。
  * [OnPremisesCassandraLinkedService](/dotnet/api/microsoft.azure.management.datafactories.models.onpremisescassandralinkedservice)
  * [SalesforceLinkedService](/dotnet/api/microsoft.azure.management.datafactories.models.salesforcelinkedservice)
* 次のデータセットの型が追加されています。
  * [OnPremisesCassandraTableDataset](/dotnet/api/microsoft.azure.management.datafactories.models.onpremisescassandratabledataset)
* 次のコピー ソースの種類が追加されています。
  * [CassandraSource](/dotnet/api/microsoft.azure.management.datafactories.models.cassandrasource)
* [WebServiceInputs](/dotnet/api/microsoft.azure.management.datafactories.models.azuremlbatchexecutionactivity) プロパティを AzureMLBatchExecutionActivity に追加しました。
  * Azure Machine Learning の実験に複数の Web サービスの入力を渡すことができるようになりました。

## <a name="version-491"></a>バージョン 4.9.1
### <a name="bug-fix"></a>バグの修正
* [WebLinkedService](/dotnet/api/microsoft.azure.management.datafactories.models.weblinkedservice)の Web API ベースの認証が非推奨になりました。

## <a name="version-490"></a>バージョン 4.9.0
### <a name="feature-additions"></a>機能の追加
* [EnableStaging](/dotnet/api/microsoft.azure.management.datafactories.models.copyactivity) プロパティと [StagingSettings](/dotnet/api/microsoft.azure.management.datafactories.models.stagingsettings) プロパティを CopyActivity に追加しました。 機能の詳細については、「 [ステージング コピー](data-factory-copy-activity-performance.md#staged-copy) 」を参照してください。

### <a name="bug-fix"></a>バグの修正
* [ActivityWindowsByActivityListParameters](/dotnet/api/microsoft.azure.management.datafactories.models.activitywindowsbyactivitylistparameters) インスタンスを受け取る [ActivityWindowOperationExtensions.List](/dotnet/api/microsoft.azure.management.datafactories.activitywindowoperationsextensions) メソッドのオーバーロードを導入しました。
* CopySink で [WriteBatchSize](/dotnet/api/microsoft.azure.management.datafactories.models.copysink) と [WriteBatchTimeout](/dotnet/api/microsoft.azure.management.datafactories.models.copysink) が、省略可能としてマークされました。

## <a name="version-480"></a>バージョン 4.8.0
### <a name="feature-additions"></a>機能の追加
* 次の省略可能なプロパティがコピー アクティビティの種類に追加され、コピー パフォーマンスの調整が有効になりました。
  * [ParallelCopies](/dotnet/api/microsoft.azure.management.datafactories.models.copyactivity)
  * [CloudDataMovementUnits](/dotnet/api/microsoft.azure.management.datafactories.models.copyactivity)

## <a name="version-470"></a>Version 4.7.0
### <a name="feature-additions"></a>機能の追加
* Optimized Row Columnar (ORC) 形式でファイルをコピーするための新しい StorageFormat タイプ ( [OrcFormat](/dotnet/api/microsoft.azure.management.datafactories.models.orcformat) ) を追加しました。
* [AllowPolyBase](/dotnet/api/microsoft.azure.management.datafactories.models.sqldwsink) プロパティと PolyBaseSettings プロパティを SqlDWSink に追加しました。
  * PolyBase を使用して Azure Synapse Analytics にデータをコピーすることができます。

## <a name="version-461"></a>Version 4.6.1
### <a name="bug-fixes"></a>バグの修正
* アクティビティ ウィンドウをリストするための HTTP 要求を修正します。
  * リソース グループの名前とデータ ファクトリの名前を要求のペイロードから削除します。

## <a name="version-460"></a>バージョン 4.6.0
### <a name="feature-additions"></a>機能の追加
* 次のプロパティが [PipelineProperties](/dotnet/api/microsoft.azure.management.datafactories.models.pipelineproperties)に追加されました。
  * [PipelineMode](/dotnet/api/microsoft.azure.management.datafactories.models.pipelineproperties)
  * [ExpirationTime](/dotnet/api/microsoft.azure.management.datafactories.models.pipelineproperties)
  * [データセット](/dotnet/api/microsoft.azure.management.datafactories.models.pipelineproperties)
* 次のプロパティが [PipelineRuntimeInfo](/dotnet/api/microsoft.azure.management.datafactories.common.models.pipelineruntimeinfo)に追加されました。
  * [PipelineState](/dotnet/api/microsoft.azure.management.datafactories.common.models.pipelineruntimeinfo)
* JSON 形式のデータを持つデータセットを定義するために、新しい [StorageFormat](/dotnet/api/microsoft.azure.management.datafactories.models.storageformat) 型の [JsonFormat](/dotnet/api/microsoft.azure.management.datafactories.models.jsonformat) 型が追加されました。

## <a name="version-450"></a>バージョン 4.5.0
### <a name="feature-additions"></a>機能の追加
* [アクティビティ ウィンドウの一覧操作](/dotnet/api/microsoft.azure.management.datafactories.activitywindowoperationsextensions)が追加されました。
  * エンティティ型 (つまり、データ ファクトリ、データセット、パイプライン、およびアクティビティ) に基づくフィルターを使用してアクティビティ ウィンドウを取得するメソッドが追加されました。
* 次のリンクされたサービスの種類が追加されています。
  * [ODataLinkedService](/dotnet/api/microsoft.azure.management.datafactories.models.odatalinkedservice)、[WebLinkedService](/dotnet/api/microsoft.azure.management.datafactories.models.weblinkedservice)
* 次のデータセットの型が追加されています。
  * [ODataResourceDataset](/dotnet/api/microsoft.azure.management.datafactories.models.odataresourcedataset)、[WebTableDataset](/dotnet/api/microsoft.azure.management.datafactories.models.webtabledataset)
* 次のコピー ソースの種類が追加されています。     
  * [WebSource](/dotnet/api/microsoft.azure.management.datafactories.models.websource)

## <a name="version-440"></a>バージョン 4.4.0
### <a name="feature-additions"></a>機能の追加
* 次のリンクされたサービスの種類が、コピー アクティビティのデータ ソースおよびシンクとして追加されています。
  * [AzureStorageSasLinkedService](/dotnet/api/microsoft.azure.management.datafactories.models.azurestoragesaslinkedservice)。 概念情報と例については、「 [Azure Storage SAS のリンクされたサービス](data-factory-azure-blob-connector.md#azure-storage-sas-linked-service) 」を参照してください。

## <a name="version-430"></a>バージョン 4.3.0
### <a name="feature-additions"></a>機能の追加
* 次のリンクされたサービスの種類が、コピー アクティビティのデータ ソースとして追加されています。
  * [HdfsLinkedService](/dotnet/api/microsoft.azure.management.datafactories.models.hdfslinkedservice)。 概念情報と例については、 [Data Factory を使用した HDFS からのデータの移動](data-factory-hdfs-connector.md) に関する記事を参照してください。
  * [OnPremisesOdbcLinkedService](/dotnet/api/microsoft.azure.management.datafactories.models.onpremisesodbclinkedservice)。 概念情報と例については、「 [Azure Data Factory を使用して ODBC データ ストアからデータを移動する](data-factory-odbc-connector.md) 」を参照してください。

## <a name="version-420"></a>バージョン 4.2.0
### <a name="feature-additions"></a>機能の追加
* 次の新しいアクティビティの種類が追加されました。[AzureMLUpdateResourceActivity](/dotnet/api/microsoft.azure.management.datafactories.models.azuremlupdateresourceactivity) です。 アクティビティの詳細については、「[更新リソース アクティビティを使用して Azure ML モデルを更新する](data-factory-azure-ml-batch-execution-activity.md)」を参照してください。
* 新しい省略可能なプロパティ [updateResourceEndpoint](/dotnet/api/microsoft.azure.management.datafactories.models.azuremllinkedservice) が [AzureMLLinkedService クラス](/dotnet/api/microsoft.azure.management.datafactories.models.azuremllinkedservice)に追加されました。
* [LongRunningOperationInitialTimeout](/dotnet/api/microsoft.azure.management.datafactories.datafactorymanagementclient) プロパティと [LongRunningOperationRetryTimeout](/dotnet/api/microsoft.azure.management.datafactories.datafactorymanagementclient) プロパティが [DataFactoryManagementClient](/dotnet/api/microsoft.azure.management.datafactories.datafactorymanagementclient) クラスに追加されました。
* Data Factory サービスへのクライアントの呼び出しのタイムアウトの構成が許可されました。

## <a name="version-410"></a>バージョン 4.1.0
### <a name="feature-additions"></a>機能の追加
* 次のリンクされたサービスの種類が追加されています。
  * [AzureDataLakeStoreLinkedService](/dotnet/api/microsoft.azure.management.datafactories.models.azuredatalakestorelinkedservice)
  * [AzureDataLakeAnalyticsLinkedService](/dotnet/api/microsoft.azure.management.datafactories.models.azuredatalakeanalyticslinkedservice)
* 次のアクティビティの種類が追加されています。
  * [DataLakeAnalyticsUSQLActivity](/dotnet/api/microsoft.azure.management.datafactories.models.datalakeanalyticsusqlactivity)
* 次のデータセットの型が追加されています。
  * [AzureDataLakeStoreDataset](/dotnet/api/microsoft.azure.management.datafactories.models.azuredatalakestoredataset)
* 次のコピー アクティビティ用のソースとシンクの種類が追加されています。
  * [AzureDataLakeStoreSource](/dotnet/api/microsoft.azure.management.datafactories.models.azuredatalakestoresource)
  * [AzureDataLakeStoreSink](/dotnet/api/microsoft.azure.management.datafactories.models.azuredatalakestoresink)

## <a name="version-401"></a>バージョン 4.0.1
### <a name="breaking-changes"></a>重大な変更
次のクラスの名前が変更されました。 新しい名前は、4.0.0 リリースより前の、クラスの元の名前です。

| 4\.0.0 での名前 | 4\.0.1 での名前 |
|:--- |:--- |
| AzureSqlDataWarehouseDataset |[AzureSqlDataWarehouseTableDataset](/dotnet/api/microsoft.azure.management.datafactories.models.azuresqldatawarehousetabledataset) |
| AzureSqlDataset |[AzureSqlTableDataset](/dotnet/api/microsoft.azure.management.datafactories.models.azuresqltabledataset) |
| AzureDataset |[AzureTableDataset](/dotnet/api/microsoft.azure.management.datafactories.models.azuretabledataset) |
| OracleDataset |[OracleTableDataset](/dotnet/api/microsoft.azure.management.datafactories.models.oracletabledataset) |
| RelationalDataset |[RelationalTableDataset](/dotnet/api/microsoft.azure.management.datafactories.models.relationaltabledataset) |
| SqlServerDataset |[SqlServerTableDataset](/dotnet/api/microsoft.azure.management.datafactories.models.sqlservertabledataset) |

## <a name="version-400"></a>バージョン 4.0.0
### <a name="breaking-changes"></a>重大な変更
* 次のクラスとインターフェイスの名前が変更されました。

| 以前の名前 | 新しい名前 |
|:--- |:--- |
| ITableOperations |[IDatasetOperations](/dotnet/api/microsoft.azure.management.datafactories.idatasetoperations) |
| テーブル |[データセット](/dotnet/api/microsoft.azure.management.datafactories.models.dataset) |
| TableProperties |[DatasetProperties](/dotnet/api/microsoft.azure.management.datafactories.models.datasetproperties) |
| TableTypeProprerties |[DatasetTypeProperties](/dotnet/api/microsoft.azure.management.datafactories.models.datasettypeproperties) |
| TableCreateOrUpdateParameters |[DatasetCreateOrUpdateParameters](/dotnet/api/microsoft.azure.management.datafactories.models.datasetcreateorupdateparameters) |
| TableCreateOrUpdateResponse |[DatasetCreateOrUpdateResponse](/dotnet/api/microsoft.azure.management.datafactories.models.datasetcreateorupdateresponse) |
| TableGetResponse |[DatasetGetResponse](/dotnet/api/microsoft.azure.management.datafactories.models.datasetgetresponse) |
| TableListResponse |[DatasetListResponse](/dotnet/api/microsoft.azure.management.datafactories.models.datasetlistresponse) |
| CreateOrUpdateWithRawJsonContentParameters |[DatasetCreateOrUpdateWithRawJsonContentParameters](/dotnet/api/microsoft.azure.management.datafactories.models.datasetcreateorupdatewithrawjsoncontentparameters) |

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
* [SqlDWSink](/dotnet/api/microsoft.azure.management.datafactories.models.sqldwsink) クラスでは、Azure Synapse Analytics へのべき等コピーをサポートするために、**SliceIdentifierColumnName** と **SqlWriterCleanupScript** の 2 つの新しいプロパティがサポートされるようになりました。 これらのプロパティの詳細については、[Azure Synapse Analytics](data-factory-azure-sql-data-warehouse-connector.md) に関する記事をご覧ください。
* コピー アクティビティの一環として、Azure SQL Database ソースと Azure Synapse Analytics ソースに対するストアド プロシージャの実行がサポートされるようになりました。 [SqlSource](/dotnet/api/microsoft.azure.management.datafactories.models.sqlsource) クラスと [SqlDWSource](/dotnet/api/microsoft.azure.management.datafactories.models.sqldwsource) クラスには、**SqlReaderStoredProcedureName** プロパティと **StoredProcedureParameters** プロパティがあります。 これらのプロパティの詳細については、Azure.com の [Azure SQL Database](data-factory-azure-sql-connector.md#sqlsource) と [Azure Synapse Analytics](data-factory-azure-sql-data-warehouse-connector.md#sqldwsource) に関する記事をご覧ください。