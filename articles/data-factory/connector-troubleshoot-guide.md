---
title: Azure Data Factory コネクタのトラブルシューティング
description: Azure Data Factory でのコネクタに関する問題のトラブルシューティングを行う方法について説明します。
services: data-factory
author: linda33wj
ms.service: data-factory
ms.topic: troubleshooting
ms.date: 07/20/2020
ms.author: jingwang
ms.reviewer: craigg
ms.custom: has-adal-ref
ms.openlocfilehash: c8edb36345de4516077b3c857cff33389062cc7f
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/23/2020
ms.locfileid: "87044544"
---
# <a name="troubleshoot-azure-data-factory-connectors"></a>Azure Data Factory コネクタのトラブルシューティング

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

この記事では、Azure Data Factory のコネクタの一般的なトラブルシューティング方法について説明します。
  

## <a name="azure-blob-storage"></a>Azure Blob Storage

### <a name="error-code--azurebloboperationfailed"></a>エラー コード:AzureBlobOperationFailed

- **メッセージ**: `Blob operation Failed. ContainerName: %containerName;, path: %path;.`

- **原因**:BLOB ストレージ操作で問題が発生しました。

- **推奨事項**:エラーの詳細を確認してください。 BLOB のヘルプ ドキュメントを参照してください: https://docs.microsoft.com/rest/api/storageservices/blob-service-error-codes 。 支援が必要な場合は、ストレージ チームにお問い合わせください。


### <a name="error-code--azureblobservicenotreturnexpecteddatalength"></a>エラー コード:AzureBlobServiceNotReturnExpectedDataLength

- **メッセージ**: `Error occurred when trying to fetch the blob '%name;'. This could be a transient issue and you may rerun the job. If it fails again continuously, contact customer support.`


### <a name="error-code--azureblobnotsupportmultiplefilesintosingleblob"></a>エラー コード:AzureBlobNotSupportMultipleFilesIntoSingleBlob

- **メッセージ**: `Transferring multiple files into a single Blob is not supported. Currently only single file source is supported.`


### <a name="error-code--azurestorageoperationfailedconcurrentwrite"></a>エラー コード:AzureStorageOperationFailedConcurrentWrite

- **メッセージ**: `Error occurred when trying to upload a file. It's possible because you have multiple concurrent copy activities runs writing to the same file '%name;'. Check your ADF configuration.`


## <a name="azure-cosmos-db"></a>Azure Cosmos DB

### <a name="error-message-request-size-is-too-large"></a>エラー メッセージ: 要求のサイズが大きすぎます。

- **現象**: 既定の書き込みバッチ サイズを使用して Azure Cosmos DB にデータをコピーすると、 *"**要求のサイズが大きすぎます**"* というエラーが表示されます。

- **原因**: Cosmos DB では、単一の要求のサイズが 2 MB に制限されます。 式は、"要求サイズ = 1 つのドキュメント サイズ * 書き込みバッチ サイズ" となります。 ドキュメントのサイズが大きい場合、既定の動作の結果として要求サイズが過度に大きくなります。 書き込みバッチ サイズを調整することができます。

- **解決方法**: コピー アクティビティのシンクで、"書き込みバッチ サイズ" の値を小さくします (既定値は 10000)。

### <a name="error-message-unique-index-constraint-violation"></a>エラー メッセージ: 一意なインデックスの制約違反

- **現象**: Cosmos DB にデータをコピーすると、次のエラーが発生します。

    ```
    Message=Partition range id 0 | Failed to import mini-batch. 
    Exception was Message: {"Errors":["Encountered exception while executing function. Exception = Error: {\"Errors\":[\"Unique index constraint violation.\"]}... 
    ```

- **原因**:原因として、次の 2 つが考えられます。

    - 書き込み動作として **Insert** を使用する場合、このエラーは、ソース データに同じ ID を持つ行またはオブジェクトが存在することを意味します。

    - 書き込み動作として **Upsert** を使用し、コンテナーに別の一意のキーを設定した場合、このエラーは、ソース データの行およびオブジェクトの ID はそれぞれ異なりますが、定義された一意のキーとして同じ値が存在することを意味します。

- **解決方法**: 

    - 原因 1 の場合は、書き込み動作として **Upsert** を設定します。
    - 原因 2 の場合は、各ドキュメントの定義された一意のキーの値がそれぞれ異なることを確認してください。

### <a name="error-message-request-rate-is-large"></a>エラー メッセージ:要求率が大きいです

- **現象**: Cosmos DB にデータをコピーすると、次のエラーが発生します。

    ```
    Type=Microsoft.Azure.Documents.DocumentClientException,
    Message=Message: {"Errors":["Request rate is large"]}
    ```

- **原因**: 使用されている要求ユニットが、Cosmos DB で構成されている使用可能な RU を超えています。 Cosmos DB での RU の計算方法については、[こちら](../cosmos-db/request-units.md#request-unit-considerations)を参照してください。

- **解決方法**: 次に示す 2 つのソリューションがあります。

    1. Cosmos DB での値が大きくなるように、**コンテナー RU を増やします**。これにより、コピー アクティビティのパフォーマンスは向上しますが、Cosmos DB でのコストは大きくなります。 

    2. **writeBatchSize** をより小さい値 (1000 など) に減らし、**parallelCopies** を 1 などのより小さい値に設定します。これにより、コピーの実行パフォーマンスは現在よりも低下しますが、Cosmos DB でのコストは増加しません。

### <a name="column-missing-in-column-mapping"></a>列マッピングでの列の欠落

- **現象**: 列マッピングのために Cosmos DB のスキーマをインポートするときに、一部の列が欠落しています。 

- **原因**: ADF では、最初の 10 の Cosmos DB ドキュメントからスキーマが推論されます。 そのようなドキュメントに値を持たない列またはプロパティがある場合、それらは ADF によって検出されないため、表示されません。

- **解決方法**: 次のようにクエリをチューニングして、空の値の場合でも結果セットに列が表示されるようにすることができます (想定: 最初の 10 のドキュメント内で "impossible" 列が欠落しています)。 あるいは、マッピング用の列を手動で追加することもできます。

    ```sql
    select c.company, c.category, c.comments, (c.impossible??'') as impossible from c
    ```

### <a name="error-message-the-guidrepresentation-for-the-reader-is-csharplegacy"></a>エラー メッセージ:リーダーの GuidRepresentation が CSharpLegacy です

- **現象**: UUID フィールドを使用して Cosmos DB MongoAPI/MongoDB からのデータをコピーすると、次のエラーが発生します。

    ```
    Failed to read data via MongoDB client.,
    Source=Microsoft.DataTransfer.Runtime.MongoDbV2Connector,Type=System.FormatException,
    Message=The GuidRepresentation for the reader is CSharpLegacy which requires the binary sub type to be UuidLegacy not UuidStandard.,Source=MongoDB.Bson,’“,
    ```

- **原因**:BSON で UUID を表すには、UuidStardard と UuidLegacy という 2 つの方法があります。 既定では、UuidLegacy はデータの読み取りに使用されます。 MongoDB 内の UUID データが UuidStandard の場合は、エラーが発生します。

- **解決方法**: MongoDB 接続文字列に、オプション "**uuidRepresentation=standard**" を追加します。 詳細については、[MongoDB 接続文字列](connector-mongodb.md#linked-service-properties)に関する記事を参照してください。
            

## <a name="azure-data-lake-storage-gen2"></a>Azure Data Lake Storage Gen2

### <a name="error-code--adlsgen2operationfailed"></a>エラー コード:AdlsGen2OperationFailed

- **メッセージ**: `ADLS Gen2 operation failed for: %adlsGen2Message;.%exceptionData;.`

- **原因**:ADLS Gen2 は、操作が失敗したことを示すエラーをスローします。

- **推奨事項**:ADLS Gen2 によってスローされた詳細なエラー メッセージを確認してください。 一時的な障害が原因の場合は、再試行してください。 さらに支援が必要な場合は、Azure Storage サポートにお問い合わせください。その際、エラー メッセージに含まれる要求 ID をお知らせください。

- **原因**:エラー メッセージに "Forbidden" が含まれている場合、お使いのサービスプリンシパルまたはマネージド ID に、ADLS Gen2 にアクセスするための十分なアクセス許可がない可能性があります。

- **推奨事項**:ヘルプ ドキュメントを参照してください: https://docs.microsoft.com/azure/data-factory/connector-azure-data-lake-storage#service-principal-authentication 。

- **原因**:エラー メッセージに "InternalServerError" が含まれている場合、エラーは ADLS Gen2 によって返されたものです。

- **推奨事項**:一時的な障害が原因の可能性があります。再試行してください。 問題が解決しない場合は、Azure Storage サポートにお問い合わせください。その際、エラー メッセージに含まれる要求 ID をお知らせください。


### <a name="error-code--adlsgen2invalidurl"></a>エラー コード:AdlsGen2InvalidUrl

- **メッセージ**: `Invalid url '%url;' provided, expecting http[s]://<accountname>.dfs.core.windows.net.`


### <a name="error-code--adlsgen2invalidfolderpath"></a>エラー コード:AdlsGen2InvalidFolderPath

- **メッセージ**: `The folder path is not specified. Cannot locate the file '%name;' under the ADLS Gen2 account directly. Please specify the folder path instead.`


### <a name="error-code--adlsgen2operationfailedconcurrentwrite"></a>エラー コード:AdlsGen2OperationFailedConcurrentWrite

- **メッセージ**: `Error occurred when trying to upload a file. It's possible because you have multiple concurrent copy activities runs writing to the same file '%name;'. Check your ADF configuration.`


### <a name="error-code-adlsgen2timeouterror"></a>エラー コード:AdlsGen2TimeoutError

- **メッセージ**: `Request to ADLS Gen2 account '%account;' met timeout error. It is mostly caused by the poor network between the Self-hosted IR machine and the ADLS Gen2 account. Check the network to resolve such error.`


## <a name="azure-data-lake-storage-gen1"></a>Azure Data Lake Storage Gen1

### <a name="error-message-the-underlying-connection-was-closed-could-not-establish-trust-relationship-for-the-ssltls-secure-channel"></a>エラー メッセージ:基になる接続が閉じられました。SSL/TLS のセキュリティで保護されているチャネルに対する信頼関係を確立できませんでした。

- **現象**:コピー アクティビティが次のエラーにより失敗します。 

    ```
    Message: Failure happened on 'Sink' side. ErrorCode=UserErrorFailedFileOperation,'Type=Microsoft.DataTransfer.Common.Shared.HybridDeliveryException,Message=Upload file failed at path STAGING/PLANT/INDIARENEWABLE/LiveData/2020/01/14\\20200114-0701-oem_gibtvl_mannur_data_10min.csv.,Source=Microsoft.DataTransfer.ClientLibrary,''Type=System.Net.WebException,Message=The underlying connection was closed: Could not establish trust relationship for the SSL/TLS secure channel.,Source=System,''Type=System.Security.Authentication.AuthenticationException,Message=The remote certificate is invalid according to the validation procedure.,Source=System,'.
    ```

- **原因**:TLS ハンドシェイク中に証明書の検証に失敗しました。

- **解決方法**:対処法:ADLS Gen1 の TLS 検証をスキップするには、段階的なコピーを使用します。 この問題を再現し、netmon トレースを収集してから、ネットワーク チームに連絡し、[この記事](self-hosted-integration-runtime-troubleshoot-guide.md#how-to-collect-netmon-trace)に従ってローカル ネットワーク構成を確認する必要があります。


    ![ADLS Gen1 のトラブルシューティング](./media/connector-troubleshoot-guide/adls-troubleshoot.png)


### <a name="error-message-the-remote-server-returned-an-error-403-forbidden"></a>エラー メッセージ:リモート サーバーがエラー(403) 禁止されています

- **現象**: コピー アクティビティが次のエラーにより失敗します。 

    ```
    Message: The remote server returned an error: (403) Forbidden.. 
    Response details: {"RemoteException":{"exception":"AccessControlException""message":"CREATE failed with error 0x83090aa2 (Forbidden. ACL verification failed. Either the resource does not exist or the user is not authorized to perform the requested operation.)....
    ```

- **原因**: 原因の 1 つとして、使用するサービス プリンシパルまたはマネージド ID に、特定のフォルダーまたはファイルにアクセスするためのアクセス許可が付与されていないことが考えられます。

- **解決方法**: コピーする必要のあるフォルダーとサブフォルダーのすべてに対して、対応するアクセス許可を付与します。 [こちらのドキュメント](connector-azure-data-lake-store.md#linked-service-properties)をご覧ください。

### <a name="error-message-failed-to-get-access-token-by-using-service-principal-adal-error-service_unavailable"></a>エラー メッセージ: サービス プリンシパルを使用してアクセス トークンを取得できませんでした。 ADAL エラー: service_unavailable

- **現象**: コピー アクティビティが次のエラーにより失敗します。

    ```
    Failed to get access token by using service principal. 
    ADAL Error: service_unavailable, The remote server returned an error: (503) Server Unavailable.
    ```

- **原因**:Azure Active Directory によって所有されているサービス トークン サーバー (STS) が使用できる場合 (つまり、ビジー状態のため要求を処理できない場合)、HTTP エラー 503 が返されます。 

- **解決方法**: 数分後にコピー アクティビティを再実行します。
                  

## <a name="azure-sql-data-warehouseazure-sql-databasesql-server"></a>Azure SQL Data Warehouse/Azure SQL Database/SQL Server

### <a name="error-code--sqlfailedtoconnect"></a>エラー コード:SqlFailedToConnect

- **メッセージ**: `Cannot connect to SQL Database: '%server;', Database: '%database;', User: '%user;'. Check the linked service configuration is correct, and make sure the SQL Database firewall allows the integration runtime to access.`

- **原因**:エラー メッセージに "SqlException" が含まれている場合、SQL Database は、何らかの特定の操作が失敗したことを示すエラーをスローします。

- **推奨事項**:詳細については、次の参照ドキュメントで SQL エラー コードを検索してください: https://docs.microsoft.com/sql/relational-databases/errors-events/database-engine-events-and-errors 。 さらに支援が必要な場合は、Azure SQL サポートにお問い合わせください。

- **原因**:"Client with IP address '...' is not allowed to access the server" (IP アドレスが '...' のクライアントはサーバーへのアクセスが許可されていません) という内容がエラー メッセージに含まれており、Azure SQL データベースに接続しようとしている場合、その原因は通常、Azure SQL データベースのファイアウォールの問題です。

- **推奨事項**:論理 SQL サーバーのファイアウォールの構成で、[Azure サービスおよびリソースにこのサーバーへのアクセスを許可する] オプションを有効にします。 参照ドキュメント: https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure 。


### <a name="error-code--sqloperationfailed"></a>エラー コード:SqlOperationFailed

- **メッセージ**: `A database operation failed. Please search error to get more details.`

- **原因**:エラー メッセージに "SqlException" が含まれている場合、SQL Database は、何らかの特定の操作が失敗したことを示すエラーをスローします。

- **推奨事項**:SQL エラーが明確でない場合は、データベースを最新の互換性レベル '150' に変更してみてください。 最新バージョンの SQL エラーをスローすることができます。 詳細なドキュメントを参照してください: https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql-compatibility-level?view=sql-server-ver15#backwardCompat 。
        SQL の問題のトラブルシューティングの場合、詳細については、次の参照ドキュメントで SQL エラー コードを検索してください: https://docs.microsoft.com/sql/relational-databases/errors-events/database-engine-events-and-errors 。 さらに支援が必要な場合は、Azure SQL サポートにお問い合わせください。

- **原因**:エラー メッセージに "PdwManagedToNativeInteropException" が含まれている場合、通常、ソースとシンクで列のサイズが一致しないことが原因です。

- **推奨事項**:ソースの列とシンクの列の両方のサイズを確認してください。 さらに支援が必要な場合は、Azure SQL サポートにお問い合わせください。

- **原因**:エラー メッセージに "InvalidOperationException" が含まれている場合、通常、無効な入力データが原因です。

- **推奨事項**:問題が発生している行を特定するには、コピー アクティビティでフォールト トレランス機能を有効にしてください。これにより、問題のある行をストレージにリダイレクトして、さらに調査することができます。 参照ドキュメント: https://docs.microsoft.com/azure/data-factory/copy-activity-fault-tolerance 。



### <a name="error-code--sqlunauthorizedaccess"></a>エラー コード:SqlUnauthorizedAccess

- **メッセージ**: `Cannot connect to '%connectorName;'. Detail Message: '%message;'`

- **原因**:資格情報が正しくないか、ログイン アカウントが SQL Database にアクセスできません。

- **推奨事項**:SQL Database にアクセスするための十分なアクセス許可がログイン アカウントにあることを確認してください。


### <a name="error-code--sqlopenconnectiontimeout"></a>エラー コード:SqlOpenConnectionTimeout

- **メッセージ**: `Open connection to database timeout after '%timeoutValue;' seconds.`

- **原因**:SQL Database の一時的な障害の可能性があります。

- **推奨事項**:接続タイムアウトの値を大きくして、リンクされたサービス接続文字列の更新を再試行してください。


### <a name="error-code--sqlautocreatetabletypemapfailed"></a>エラー コード:SqlAutoCreateTableTypeMapFailed

- **メッセージ**: `Type '%dataType;' in source side cannot be mapped to a type that supported by sink side(column name:'%columnName;') in autocreate table.`

- **原因**:自動作成テーブルがソースの要件を満たすことができません。

- **推奨事項**:"マッピング" で列のデータ型を更新するか、ターゲット サーバーで手動でシンク テーブルを作成してください。


### <a name="error-code--sqldatatypenotsupported"></a>エラー コード:SqlDataTypeNotSupported

- **メッセージ**: `A database operation failed. Check the SQL errors.`

- **原因**:SQL ソースで問題が発生し、エラーが SqlDateTime オーバーフローに関連している場合、データ値が論理型の範囲 (1/1/1753 12:00:00 AM - 12/31/9999 11:59:59 PM) を超えています。

- **推奨事項**:ソースの SQL クエリで型を文字列にキャストするか、コピー アクティビティの列マッピングで列の型を "String" に変更してください。

- **原因**:SQL シンクで問題が発生し、エラーが SqlDateTime オーバーフローに関連している場合、データ値がシンク テーブルの許容範囲を超えています。

- **推奨事項**:シンク テーブルで、対応する列の型を "datetime2" 型に更新してください。


### <a name="error-code--sqlinvaliddbstoredprocedure"></a>エラー コード:SqlInvalidDbStoredProcedure

- **メッセージ**: `The specified Stored Procedure is not valid. It could be caused by that the stored procedure doesn't return any data. Invalid Stored Procedure script: '%scriptName;'.`

- **原因**:指定されたストアド プロシージャは無効です。 ストアド プロシージャが何もデータを返さないことが原因である可能性があります。

- **推奨事項**:SQL ツールによってストアド プロシージャを検証してください。 ストアド プロシージャがデータを返せることを確認してください。


### <a name="error-code--sqlinvaliddbquerystring"></a>エラー コード:SqlInvalidDbQueryString

- **メッセージ**: `The specified SQL Query is not valid. It could be caused by that the query doesn't return any data. Invalid query: '%query;'`

- **原因**:指定された SQL クエリは無効です。 クエリが何もデータを返さないことが原因である可能性があります

- **推奨事項**:SQL ツールによって SQL クエリを検証してください。 クエリがデータを返せることを確認してください。


### <a name="error-code--sqlinvalidcolumnname"></a>エラー コード:SqlInvalidColumnName

- **メッセージ**: `Column '%column;' does not exist in the table '%tableName;', ServerName: '%serverName;', DatabaseName: '%dbName;'.`

- **原因**:列が見つかりません。 構成が不適切な可能性があります。

- **推奨事項**:クエリの列、データセットの "構造"、およびアクティビティの "マッピング" を検証してください。


### <a name="error-code--sqlcolumnnamemismatchbycasesensitive"></a>エラー コード:SqlColumnNameMismatchByCaseSensitive

- **メッセージ**: `Column '%column;' in DataSet '%dataSetName;' cannot be found in physical SQL Database. Column matching is case-sensitive. Column '%columnInTable;' appears similar. Check the DataSet(s) configuration to proceed further.`


### <a name="error-code--sqlbatchwritetimeout"></a>エラー コード:SqlBatchWriteTimeout

- **メッセージ**: `Timeouts in SQL write operation.`

- **原因**:SQL Database の一時的な障害の可能性があります。

- **推奨事項**:もう一度試してください。 問題が解決しない場合は、Azure SQL サポートにお問い合わせください。


### <a name="error-code--sqlbatchwritetransactionfailed"></a>エラー コード:SqlBatchWriteTransactionFailed

- **メッセージ**: `SQL transaction commits failed`

- **原因**:例外の詳細が常にトランザクションのタイムアウトを示している場合は、統合ランタイムとデータベース間のネットワーク待機時間が既定のしきい値の 30 秒を超えています。

- **推奨事項**:'接続タイムアウト' の値を 120 以上にして SQL のリンクされたサービス接続文字列を更新し、アクティビティを再実行してください。

- **原因**:例外の詳細が sqlconnection の破損を断続的に示している場合は、単に一時的なネットワーク障害または SQL Database 側の問題である可能性があります

- **推奨事項**:アクティビティを再試行し、SQL Database 側のメトリックを確認してください。


### <a name="error-code--sqlbulkcopyinvalidcolumnlength"></a>エラー コード:SqlBulkCopyInvalidColumnLength

- **メッセージ**: `SQL Bulk Copy failed due to receive an invalid column length from the bcp client.`

- **原因**:BCP クライアントから無効な列の長さを受信したため、SQL 一括コピーが失敗しました。

- **推奨事項**:問題が発生している行を特定するには、コピー アクティビティでフォールト トレランス機能を有効にしてください。これにより、問題のある行をストレージにリダイレクトして、さらに調査することができます。 参照ドキュメント: https://docs.microsoft.com/azure/data-factory/copy-activity-fault-tolerance 。


### <a name="error-code--sqlconnectionisclosed"></a>エラー コード:SqlConnectionIsClosed

- **メッセージ**: `The connection is closed by SQL Database.`

- **原因**:同時実行数が多いときに SQL 接続が SQL Database によって閉じられ、サーバーが接続を終了します。

- **推奨事項**:リモート サーバーで SQL 接続を閉じます。 もう一度試してください。 問題が解決しない場合は、Azure SQL サポートにお問い合わせください。


### <a name="error-code--sqlcreatetablefailedunsupportedtype"></a>エラー コード:SqlCreateTableFailedUnsupportedType

- **メッセージ**: `Type '%type;' in source side cannot be mapped to a type that supported by sink side(column name:'%name;') in autocreate table.`


### <a name="error-message-conversion-failed-when-converting-from-a-character-string-to-uniqueidentifier"></a>エラー メッセージ: 文字列から uniqueidentifier に変換中、変換が失敗しました。

- **現象**:ステージング コピーおよび PolyBase を使用して、SQL Server などの表形式のデータ ソースから Azure SQL Data Warehouse にデータをコピーすると、次のエラーが発生します。

    ```
    ErrorCode=FailedDbOperation,Type=Microsoft.DataTransfer.Common.Shared.HybridDeliveryException,
    Message=Error happened when loading data into SQL Data Warehouse.,
    Source=Microsoft.DataTransfer.ClientLibrary,Type=System.Data.SqlClient.SqlException,
    Message=Conversion failed when converting from a character string to uniqueidentifier...
    ```

- **原因**: Azure SQL Data Warehouse の PolyBase によって空の文字列を GUID に変換できません。

- **解決方法**:コピー アクティビティ シンクの PolyBase の設定で、"**使用型の既定の**" オプションを false に設定します。

### <a name="error-message-expected-data-type-decimalxx-offending-value"></a>エラー メッセージ:予期されたデータ型: DECIMAL(x,x)、問題のある値

- **現象**: ステージング コピーおよび PolyBase を使用して、SQL Server などの表形式のデータ ソースから SQL DW にデータをコピーすると、次のエラーが発生します。

    ```
    ErrorCode=FailedDbOperation,Type=Microsoft.DataTransfer.Common.Shared.HybridDeliveryException,
    Message=Error happened when loading data into SQL Data Warehouse.,
    Source=Microsoft.DataTransfer.ClientLibrary,Type=System.Data.SqlClient.SqlException,
    Message=Query aborted-- the maximum reject threshold (0 rows) was reached while reading from an external source: 1 rows rejected out of total 415 rows processed. (/file_name.txt) 
    Column ordinal: 18, Expected data type: DECIMAL(x,x), Offending value:..
    ```

- **原因**: Azure SQL Data Warehouse の PolyBase では、空の文字列 (null 値) を decimal 型の列に挿入することはできません。

- **解決方法**:コピー アクティビティ シンクの PolyBase の設定で、"**使用型の既定の**" オプションを false に設定します。

### <a name="error-message-java-exception-message-hdfsbridgecreaterecordreader"></a>エラー メッセージ:Java の例外メッセージ:HdfsBridge::CreateRecordReader

- **現象**: PolyBase を使用して Azure SQL Data Warehouse にデータをコピーすると、次のエラーが発生します。

    ```
    Message=110802;An internal DMS error occurred that caused this operation to fail. 
    Details: Exception: Microsoft.SqlServer.DataWarehouse.DataMovement.Common.ExternalAccess.HdfsAccessException, 
    Message: Java exception raised on call to HdfsBridge_CreateRecordReader. 
    Java exception message:HdfsBridge::CreateRecordReader - Unexpected error encountered creating the record reader.: Error [HdfsBridge::CreateRecordReader - Unexpected error encountered creating the record reader.] occurred while accessing external file.....
    ```

- **原因**: 原因として、スキーマ (列の合計幅) が大きすぎる (1 MB を超えている) ことが考えられます。 次のすべての列のサイズを追加して、ターゲットの SQL DW テーブルのスキーマを確認します。

    - Int -> 4 バイト
    - Bigint-> 8 バイト
    - Varchar(n)、char(n)、binary(n)、varbinary(n) -> n バイト
    - Nvarchar(n)、nchar(n) -> n*2 バイト
    - Date -> 6 バイト
    - Datetime/(2)、smalldatetime -> 16 バイト
    - Datetimeoffset -> 20 バイト
    - Decimal -> 19 バイト
    - Float -> 8 バイト
    - Money -> 8 バイト
    - Smallmoney -> 4 バイト
    - Real -> 4 バイト
    - Smallint -> 2 バイト
    - Time -> 12 バイト
    - Tinyint -> 1 バイト

- **解決方法**: 列幅を狭くして 1 MB 未満にします。

- または、PolyBase を無効にして一括挿入アプローチを使用します。

### <a name="error-message-the-condition-specified-using-http-conditional-headers-is-not-met"></a>エラー メッセージ:HTTP 条件ヘッダーを使用して指定した条件が満たされません。

- **現象**:SQL クエリを使用して Azure SQL Data Warehouse からデータをプルすると、次のエラーが発生します。

    ```
    ...StorageException: The condition specified using HTTP conditional header(s) is not met...
    ```

- **原因**:Azure SQL Data Warehouse では、Azure Storage 内の外部テーブルに対するクエリに関する問題が発生しています。

- **解決方法**:SSMS で同じクエリを実行し、同じ結果が表示されるかどうかを確認します。 表示される場合は、Azure SQL Data Warehouse に対するサポート チケットを作成し、ご利用の SQL DW サーバーとデータベース名を入力してさらにトラブルシューティングを行います。
            

## <a name="delimited-text-format"></a>区切りテキスト形式

### <a name="error-code--delimitedtextcolumnnamenotallownull"></a>エラー コード:DelimitedTextColumnNameNotAllowNull

- **メッセージ**: `The name of column index %index; is empty. Make sure column name is properly specified in the header row.`

- **原因**:アクティビティに 'firstRowAsHeader' を設定すると、最初の行が列名として使用されます。 このエラーは、最初の行に空の値が含まれていることを意味します。 次に例を示します。'ColumnA,, ColumnB'。

- **推奨事項**:最初の行を確認し、空の値がある場合は値を修正してください。


### <a name="error-code--delimitedtextmorecolumnsthandefined"></a>エラー コード:DelimitedTextMoreColumnsThanDefined

- **メッセージ**: `Error found when processing '%function;' source '%name;' with row number %rowCount;: found more columns than expected column count: %columnCount;.`

- **原因**:問題のある行の列数が、最初の行の列数を超えています。 データの問題、または列区切り記号/引用符の文字設定が正しくないことが原因である可能性があります。

- **推奨事項**:エラー メッセージの行数を取得し、行の列を確認して、データを修正してください。

- **原因**:予想される列数がエラー メッセージで "1" の場合、間違った圧縮またはフォーマット設定が指定されていて、ADF がファイルを誤って解析する原因になった可能性があります。

- **推奨事項**:フォーマット設定を調べて、ソース ファイルと一致していることを確認してください。

- **原因**:ソースがフォルダーの場合は、指定されたフォルダーにあるファイルのスキーマが異なる可能性があります。

- **推奨事項**:指定されたフォルダーにあるファイルのスキーマが同じであることを確認してください。


### <a name="error-code--delimitedtextincorrectrowdelimiter"></a>エラー コード:DelimitedTextIncorrectRowDelimiter

- **メッセージ**: `The specified row delimiter %rowDelimiter; is incorrect. Cannot detect a row after parse %size; MB data.`


### <a name="error-code--delimitedtexttoolargecolumncount"></a>エラー コード:DelimitedTextTooLargeColumnCount

- **メッセージ**: `Column count reaches limitation when deserializing csv file. Maximum size is '%size;'. Check the column delimiter and row delimiter provided. (Column delimiter: '%columnDelimiter;', Row delimiter: '%rowDelimiter;')`


### <a name="error-code--delimitedtextinvalidsettings"></a>エラー コード:DelimitedTextInvalidSettings

- **メッセージ**: `%settingIssues;`



## <a name="dynamics-365common-data-servicedynamics-crm"></a>Dynamics 365/Common Data Service/Dynamics CRM

### <a name="error-code--dynamicscreateserviceclienterror"></a>エラー コード:DynamicsCreateServiceClientError

- **メッセージ**: `This is a transient issue on dynamics server side. Try to rerun the pipeline.`

- **原因**:これは、Dynamics サーバー側の一時的な問題です。

- **推奨事項**:パイプラインを再実行してください。 引き続き失敗する場合は、並列処理を減らしてください。 それでも失敗する場合は、Dynamics のサポートにお問い合わせください。



## <a name="json-format"></a>JSON 形式

### <a name="error-code--jsoninvalidarraypathdefinition"></a>エラー コード:JsonInvalidArrayPathDefinition

- **メッセージ**: `Error occurred when deserializing source JSON data. Check whether the JsonPath in JsonNodeReference and JsonPathDefintion is valid.`


### <a name="error-code--jsonemptyjobjectdata"></a>エラー コード:JsonEmptyJObjectData

- **メッセージ**: `The specified row delimiter %rowDelimiter; is incorrect. Cannot detect a row after parse %size; MB data.`


### <a name="error-code--jsonnullvalueinpathdefinition"></a>エラー コード:JsonNullValueInPathDefinition

- **メッセージ**: `Null JSONPath detected in JsonPathDefinition.`


### <a name="error-code--jsonunsupportedhierarchicalcomplexvalue"></a>エラー コード:JsonUnsupportedHierarchicalComplexValue

- **メッセージ**: `The retrieved type of data %data; with value %value; is not supported yet. Please either remove the targeted column '%name;' or enable skip incompatible row to skip the issue rows.`


### <a name="error-code--jsonconflictpartitiondiscoveryschema"></a>エラー コード:JsonConflictPartitionDiscoverySchema

- **メッセージ**: `Conflicting partition column names detected.'%schema;', '%partitionDiscoverySchema;'`


### <a name="error-code--jsoninvaliddataformat"></a>エラー コード:JsonInvalidDataFormat

- **メッセージ**: `Error occurred when deserializing source JSON file '%fileName;'. Check if the data is in valid JSON object format.`


### <a name="error-code--jsoninvaliddatamixedarrayandobject"></a>エラー コード:JsonInvalidDataMixedArrayAndObject

- **メッセージ**: `Error occurred when deserializing source JSON file '%fileName;'. The JSON format doesn't allow mixed arrays and objects.`



## <a name="parquet-format"></a>Parquet 形式

### <a name="error-code--parquetjavainvocationexception"></a>エラー コード:ParquetJavaInvocationException

- **メッセージ**: `An error occurred when invoking java, message: %javaException;.`

- **原因**:エラー メッセージに 'OutOfMemory'、'Java heap space'、および 'doubleCapacity' が含まれている場合は、通常、古いバージョンの統合ランタイムでのメモリ管理の問題です。

- **推奨事項**:セルフホステッド統合ランタイムを使用していて、バージョンが 3.20.7159.1 より前の場合は、最新バージョンにアップグレードすることをお勧めします。

- **原因**:エラー メッセージに 'OutOfMemory' が含まれている場合は、統合ランタイムにファイルを処理するのに十分なリソースがありません。

- **推奨事項**:統合ランタイムで同時実行を制限してください。 セルフホステッド統合ランタイムの場合は、8 GB 以上のメモリを備えた強力なマシンにスケールアップしてください。

- **原因**:エラー メッセージに 'NullPointerReference' が含まれている場合は、一時的なエラーである可能性があります。

- **推奨事項**:もう一度試してください。 問題が解決しない場合は、サポートにお問い合わせください。


### <a name="error-code--parquetinvalidfile"></a>エラー コード:ParquetInvalidFile

- **メッセージ**: `File is not a valid parquet file.`

- **原因**:Parquet ファイルの問題です。

- **推奨事項**:入力が有効な Parquet ファイルであることを確認してください。


### <a name="error-code--parquetnotsupportedtype"></a>エラー コード:ParquetNotSupportedType

- **メッセージ**: `Unsupported Parquet type. PrimitiveType: %primitiveType; OriginalType: %originalType;.`

- **原因**:Parquet 形式は、Azure Data Factory ではサポートされていません。

- **推奨事項**:ソース データを再確認してください。 ドキュメントを参照してください: https://docs.microsoft.com/azure/data-factory/supported-file-formats-and-compression-codecs 。


### <a name="error-code--parquetmisseddecimalprecisionscale"></a>エラー コード:ParquetMissedDecimalPrecisionScale

- **メッセージ**: `Decimal Precision or Scale information is not found in schema for column: %column;.`

- **原因**:数値の有効桁数と小数点以下桁数を解析しようとしていますが、このような情報は提供されていません。

- **推奨事項**:'ソース' は正しい有効桁数と小数点以下桁数を返しません。 問題の列の有効桁数と小数点以下桁数を確認してください。


### <a name="error-code--parquetinvaliddecimalprecisionscale"></a>エラー コード:ParquetInvalidDecimalPrecisionScale

- **メッセージ**: `Invalid Decimal Precision or Scale. Precision: %precision; Scale: %scale;.`

- **原因**:スキーマが無効です。

- **推奨事項**:問題の列の有効桁数と小数点以下桁数を確認してください。


### <a name="error-code--parquetcolumnnotfound"></a>エラー コード:ParquetColumnNotFound

- **メッセージ**: `Column %column; does not exist in Parquet file.`

- **原因**:送信元スキーマがシンク スキーマと一致しません。

- **推奨事項**:'アクティビティ' の 'マッピング' を確認してください。 ソース列を右シンク列にマップできることを確認してください。


### <a name="error-code--parquetinvaliddataformat"></a>エラー コード:ParquetInvalidDataFormat

- **メッセージ**: `Incorrect format of %srcValue; for converting to %dstType;.`

- **原因**:データを mappings.source に指定された型に変換できません

- **推奨事項**:ソース データを再確認するか、コピー アクティビティの列マッピングでこの列に適切なデータ型を指定してください。 ドキュメントを参照してください: https://docs.microsoft.com/azure/data-factory/supported-file-formats-and-compression-codecs 。


### <a name="error-code--parquetdatacountnotmatchcolumncount"></a>エラー コード:ParquetDataCountNotMatchColumnCount

- **メッセージ**: `The data count in a row '%sourceColumnCount;' does not match the column count '%sinkColumnCount;' in given schema.`

- **原因**:ソース列の数とシンク列の数が一致しません

- **推奨事項**:'マッピング' でソース列の数がシンク列の数と同じであることを再確認してください。


### <a name="error-code--parquetdatatypenotmatchcolumntype"></a>エラー コード:ParquetDataTypeNotMatchColumnType

- **メッセージ**:データ型 %srcType; が、列 '%columnIndex;' の指定された列の型 %dstType; と一致しません。

- **原因**:ソースからのデータを、シンクで定義されている型に変換できません

- **推奨事項**:mapping.sink に正しい型を指定してください。


### <a name="error-code--parquetbridgeinvaliddata"></a>エラー コード:ParquetBridgeInvalidData

- **メッセージ**: `%message;`

- **原因**:制限を超えるデータ値

- **推奨事項**:もう一度試してください。 問題が解決しない場合は、Microsoft にお問い合わせください。


### <a name="error-code--parquetunsupportedinterpretation"></a>エラー コード:ParquetUnsupportedInterpretation

- **メッセージ**: `The given interpretation '%interpretation;' of parquet format is not supported.`

- **原因**:サポートされていないシナリオ

- **推奨事項**:'ParquetInterpretFor' を 'sparkSql' にしないでください。


### <a name="error-code--parquetunsupportfilelevelcompressionoption"></a>エラー コード:ParquetUnsupportFileLevelCompressionOption

- **メッセージ**: `File level compression is not supported for Parquet.`

- **原因**:サポートされていないシナリオ

- **推奨事項**:ペイロード内の 'CompressionType' を削除してください。



## <a name="general-copy-activity-error"></a>一般的なコピー アクティビティのエラー

### <a name="error-code--jrenotfound"></a>エラー コード:JreNotFound

- **メッセージ**: `Java Runtime Environment cannot be found on the Self-hosted Integration Runtime machine. It is required for parsing or writing to Parquet/ORC files. Make sure Java Runtime Environment has been installed on the Self-hosted Integration Runtime machine.`

- **原因**:セルフホステッド統合ランタイムで Java ランタイムが見つかりません。 Java ランタイムは、特定のソースを読み取るために必要です。

- **推奨事項**:統合ランタイム環境を確認してください。参照ドキュメント: https://docs.microsoft.com/azure/data-factory/format-parquet#using-self-hosted-integration-runtime


### <a name="error-code--wildcardpathsinknotsupported"></a>エラー コード:WildcardPathSinkNotSupported

- **メッセージ**: `Wildcard in path is not supported in sink dataset. Fix the path: '%setting;'.`

- **原因**:シンク データセットでは、ワイルドカードはサポートされません。

- **推奨事項**:シンク データセットを確認し、ワイルドカード値を指定せずにパスを修正してください。


### <a name="error-code--mappinginvalidpropertywithemptyvalue"></a>エラー コード:MappingInvalidPropertyWithEmptyValue

- **メッセージ**: `One or more '%sourceOrSink;' in copy activity mapping doesn't point to any data. Choose one of the three properties 'name', 'path' and 'ordinal' to reference columns/fields.`


### <a name="error-code--mappinginvalidpropertywithnamepathandordinal"></a>エラー コード:MappingInvalidPropertyWithNamePathAndOrdinal

- **メッセージ**: `Mixed properties are used to reference '%sourceOrSink;' columns/fields in copy activity mapping. Please only choose one of the three properties 'name', 'path' and 'ordinal'. The problematic mapping setting is 'name': '%name;', 'path': '%path;','ordinal': '%ordinal;'.`


### <a name="error-code--mappingduplicatedordinal"></a>エラー コード:MappingDuplicatedOrdinal

- **メッセージ**: `Copy activity 'mappings' has duplicated ordinal value "%Ordinal;". Fix the setting in 'mappings'.`


### <a name="error-code--mappinginvalidordinalforsinkcolumn"></a>エラー コード:MappingInvalidOrdinalForSinkColumn

- **メッセージ**: `Invalid 'ordinal' property for sink column under 'mappings' property. Ordinal: %Ordinal;.`


## <a name="next-steps"></a>次のステップ

トラブルシューティングのその他のヘルプについては、次のリソースを参照してください。

*  [Data Factory ブログ](https://azure.microsoft.com/blog/tag/azure-data-factory/)
*  [Data Factory の機能のリクエスト](https://feedback.azure.com/forums/270578-data-factory)
*  [Azure のビデオ](https://azure.microsoft.com/resources/videos/index/?sort=newest&services=data-factory)
*  [Microsoft Q&A 質問ページ](https://docs.microsoft.com/answers/topics/azure-data-factory.html)
*  [Data Factory の Stack Overflow フォーラム](https://stackoverflow.com/questions/tagged/azure-data-factory)
*  [Data Factory に関する Twitter 情報](https://twitter.com/hashtag/DataFactory)
            
