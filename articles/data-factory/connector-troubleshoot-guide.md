---
title: Azure Data Factory コネクタのトラブルシューティング
description: Azure Data Factory でのコネクタに関する問題のトラブルシューティングを行う方法について説明します。
author: linda33wj
ms.service: data-factory
ms.topic: troubleshooting
ms.date: 02/08/2021
ms.author: jingwang
ms.custom: has-adal-ref
ms.openlocfilehash: 9d8f940e3900c00b1c6f6623dfeff2d92ca85aa3
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102042438"
---
# <a name="troubleshoot-azure-data-factory-connectors"></a>Azure Data Factory コネクタのトラブルシューティング

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

この記事では、Azure Data Factory コネクタに関する問題の一般的なトラブルシューティング方法について説明します。

## <a name="azure-blob-storage"></a>Azure Blob Storage

### <a name="error-code-azurebloboperationfailed"></a>エラー コード:AzureBlobOperationFailed

- **メッセージ**: 「Blob 操作に失敗しました。 ContainerName: %containerName;、パス: %path;。」

- **原因**:Azure Blob Storage 操作に問題があります。

- **推奨事項**:エラーの詳細を確認するには、[Azure Blob Storage エラー コード](/rest/api/storageservices/blob-service-error-codes)に関する記事を参照してください。 詳細については、Azure Blob Storage チームにお問い合わせください。


### <a name="invalid-property-during-copy-activity"></a>コピー アクティビティ中プロパティが無効です

- **メッセージ**: `Copy activity \<Activity Name> has an invalid "source" property. The source type is not compatible with the dataset \<Dataset Name> and its linked service \<Linked Service Name>. Please verify your input against.`

- **原因**:データセットで定義されている型が、Copy アクティビティで定義されているソースまたはシンクの型と一致しません。

- **解決方法**:データセットまたはパイプライン JSON 定義を編集して、型の一貫性を保ち、デプロイを再実行してください。


## <a name="azure-cosmos-db"></a>Azure Cosmos DB

### <a name="error-message-request-size-is-too-large"></a>エラー メッセージ: 要求のサイズが大きすぎます。

- **現象**:既定の書き込みバッチサイズを使用して Azure Cosmos DB にデータをコピーすると、次のエラーが表示されます。`Request size is too large.`

- **原因**:Azure Cosmos DB は、1 つの要求のサイズを 2 MB に制限します。 式は、*要求サイズ = 1 つのドキュメント サイズ <\*書き込みバッチ サイズ*> となります。 ドキュメントのサイズが大きい場合、既定の動作の結果として要求サイズが過度に大きくなります。 書き込みバッチ サイズを調整することができます。

- **解決方法**:Copy アクティビティのシンクで、<*書き込みバッチ サイズ*> の値を小さくします (既定値は 10000)。

### <a name="error-message-unique-index-constraint-violation"></a>エラー メッセージ: 一意なインデックスの制約違反

- **現象**:Azure Cosmos DB にデータをコピーすると、次のエラーが表示されます。

    `Message=Partition range id 0 | Failed to import mini-batch. 
    Exception was Message: {"Errors":["Encountered exception while executing function. Exception = Error: {\"Errors\":[\"Unique index constraint violation.\"]}...`

- **原因**:原因として、次の 2 つが考えられます。

    - 原因 1:書き込み動作として **Insert** を使用する場合、このエラーは、ソース データに同じ ID を持つ行またはオブジェクトがあることを意味します。
    - 原因 2:書き込み動作として **Upsert** を使用し、コンテナーに別の一意のキーを設定した場合、このエラーは、ソース データの行またはオブジェクトの ID はそれぞれ異なりますが、定義された一意のキーとして同じ値が存在することを意味します。

- **解決方法**: 

    - 原因 1 の場合は、書き込み動作として **Upsert** を設定します。
    - 原因 2 の場合は、定義された一意のキーに対して各ドキュメントの値が異なることを確認してください。

### <a name="error-message-request-rate-is-large"></a>エラー メッセージ:要求率が大きいです

- **現象**:Azure Cosmos DB にデータをコピーすると、次のエラーが表示されます。

    `Type=Microsoft.Azure.Documents.DocumentClientException,
    Message=Message: {"Errors":["Request rate is large"]}`

- **原因**:使用されている要求ユニット (RU) の数が、Azure Cosmos DB で構成されている使用可能な RU を超えています。 Azure Cosmos DB が RU を計算する方法については、「[Azure Cosmos DB の要求ユニット](../cosmos-db/request-units.md#request-unit-considerations)」を参照してください。

- **解決方法**:次の 2 つの解決策のいずれかを試してください。

    - <*コンテナー RU*> 数を Azure Cosmos DB でより大きな値に増やします。 このソリューションにより、Copy アクティビティのパフォーマンスが向上しますが、Azure Cosmos DB にかかるコストが増加します。 
    - *writeBatchSize* を 1000 などの小さい値に減らし、*parallelCopies* を 1 などの小さい値に減らします。 このソリューションでは、コピーの実行のパフォーマンスが低下しますが、Azure Cosmos DB にかかるコストは増加しません。

### <a name="columns-missing-in-column-mapping"></a>列マッピングでの列の欠落

- **現象**:列マッピングのために Azure Cosmos DB のスキーマをインポートするときに、一部の列が欠落しています。 

- **原因**:Azure Data Factory は、最初の 10 個の Azure Cosmos DB ドキュメントからスキーマを推測します。 一部のドキュメント列またはプロパティに値が含まれていない場合、Azure Data Factory によってスキーマが検出されず、その結果表示されません。

- **解決方法**:次のコードに示すように、クエリを調整して、列の値を結果セットに空の値で表示するように強制できます。 最初の 10 個のドキュメントで、*impossible* 列が欠落していると見なします。 あるいは、マッピング用の列を手動で追加することもできます。

    ```sql
    select c.company, c.category, c.comments, (c.impossible??'') as impossible from c
    ```

### <a name="error-message-the-guidrepresentation-for-the-reader-is-csharplegacy"></a>エラー メッセージ:リーダーの GuidRepresentation が CSharpLegacy です

- **現象**:汎用一意識別子 (UUID) フィールドを使用して Azure Cosmos DB MongoAPI または MongoDB からデータをコピーすると、次のエラーが表示されます。

    `Failed to read data via MongoDB client., 
    Source=Microsoft.DataTransfer.Runtime.MongoDbV2Connector,Type=System.FormatException, 
    Message=The GuidRepresentation for the reader is CSharpLegacy which requires the binary sub type to be UuidLegacy not UuidStandard.,Source=MongoDB.Bson,’“,`

- **原因**:バイナリ JSON (BSON) で UUID を表すには、次の 2 つの方法があります。UuidStardard と UuidLegacy。 既定では、UuidLegacy はデータの読み取りに使用されます。 MongoDB 内の UUID データが UuidStandard の場合は、エラーが発生します。

- **解決方法**:MongoDB の接続文字列に、*uuidRepresentation=standard* オプションを追加します。 詳細については、[MongoDB 接続文字列](connector-mongodb.md#linked-service-properties)に関する記事を参照してください。
            
## <a name="azure-cosmos-db-sql-api"></a>Azure Cosmos DB (SQL API)

### <a name="error-code-cosmosdbsqlapioperationfailed"></a>エラー コード:CosmosDbSqlApiOperationFailed

- **メッセージ**: `CosmosDbSqlApi operation Failed. ErrorMessage: %msg;.`

- **原因**:CosmosDbSqlApi 操作に問題があります。

- **推奨事項**:エラーの詳細を確認するには、[Azure Cosmos DB のヘルプ ドキュメント](../cosmos-db/troubleshoot-dot-net-sdk.md)を参照してください。 詳細については、Azure Cosmos DB チームにお問い合わせください。

## <a name="azure-data-lake-storage-gen1"></a>Azure Data Lake Storage Gen1

### <a name="error-message-the-underlying-connection-was-closed-could-not-establish-trust-relationship-for-the-ssltls-secure-channel"></a>エラー メッセージ:基になる接続が閉じられました。SSL/TLS のセキュリティで保護されているチャネルに対する信頼関係を確立できませんでした。

- **現象**:コピー アクティビティが次のエラーにより失敗します。 

    `Message: ErrorCode = UserErrorFailedFileOperation, Error Message = The underlying connection was closed: Could not establish trust relationship for the SSL/TLS secure channel.`

- **原因**:TLS ハンドシェイク中に証明書の検証に失敗しました。

- **解決方法**:回避策として、ステージング コピーを使用して、Azure Data Lake Storage Gen1 のトランスポート層セキュリティ (TLS) 検証をスキップします。 この問題を再現し、ネットワーク モニター (netmon) トレースを収集してから、ネットワーク チームに連絡し、ローカル ネットワーク構成を確認する必要があります。

    ![問題をトラブルシューティングするための Azure Data Lake Storage Gen1 接続の図。](./media/connector-troubleshoot-guide/adls-troubleshoot.png)


### <a name="error-message-the-remote-server-returned-an-error-403-forbidden"></a>エラー メッセージ:リモート サーバーがエラー(403) 禁止されています

- **現象**: コピー アクティビティが次のエラーにより失敗します。 

   `Message: The remote server returned an error: (403) Forbidden.   
    Response details: {"RemoteException":{"exception":"AccessControlException""message":"CREATE failed with error 0x83090aa2 (Forbidden. ACL verification failed. Either the resource does not exist or the user is not authorized to perform the requested operation.)....`

- **原因**:原因の 1 つとして、使用するサービス プリンシパルまたはマネージド ID に、特定のフォルダーまたはファイルにアクセスするためのアクセス許可が付与されていないことが考えられます。

- **解決方法**:コピーする必要があるすべてのフォルダーとサブフォルダーに適切なアクセス許可を付与します。 詳しくは、「[Azure Data Factory を使用して Azure Data Lake Storage Gen1 との間でデータをコピーする](connector-azure-data-lake-store.md#linked-service-properties)」をご覧ください。

### <a name="error-message-failed-to-get-access-token-by-using-service-principal-adal-error-service_unavailable"></a>エラー メッセージ: サービス プリンシパルを使用してアクセス トークンを取得できませんでした。 ADAL エラー: service_unavailable

- **現象**:コピー アクティビティが次のエラーにより失敗します。

    `Failed to get access token by using service principal.  
    ADAL Error: service_unavailable, The remote server returned an error: (503) Server Unavailable.`

- **原因**:Azure Active Directory によって所有されているサービス トークン サーバー (STS) が使用できない場合、要求の処理ビジー状態であるため、HTTP エラー 503 が返されます。 

- **解決方法**: 数分後にコピー アクティビティを再実行します。


## <a name="azure-data-lake-storage-gen2"></a>Azure Data Lake Storage Gen2

### <a name="error-code-adlsgen2operationfailed"></a>エラー コード:ADLSGen2OperationFailed

- **メッセージ**: `ADLS Gen2 operation failed for: %adlsGen2Message;.%exceptionData;.`

- **原因と推奨事項**: このエラーはさまざまな原因により発生する可能性があります。 考えられる原因の分析および関連する推奨事項については、以下の一覧を確認してください。

  | 原因分析                                               | 推奨                                               |
  | :----------------------------------------------------------- | :----------------------------------------------------------- |
  | 何らかの操作に失敗したことを示すエラーが Azure Data Lake Storage Gen2 によってスローされた場合。| Azure Data Lake Storage Gen2 によってスローされたエラー メッセージの詳細を確認します。 エラーが一時的なエラーである場合は、操作を再試行してください。 さらに支援が必要な場合は、Azure Storage サポートにお問い合わせください。その際、エラー メッセージに含まれる要求 ID をお知らせください。 |
  | エラー メッセージに "許可されていません" という文字列が含まれている場合は、使用するサービス プリンシパルまたはマネージド ID に、Azure Data Lake Storage Gen2 にアクセスするための十分なアクセス許可がない可能性があります。 | このエラーのトラブルシューティングを行うには、「[Azure Data Factory を使用した Azure Data Lake Storage Gen2 でのデータのコピーと変換](./connector-azure-data-lake-storage.md#service-principal-authentication)」を参照してください。 |
  | エラー メッセージに "InternalServerError" という文字列が含まれている場合は、Azure Data Lake Storage Gen2 によってエラーが返されます。 | このエラーは、一時的なエラーが原因で発生している可能性があります。 その場合は、操作を再試行します。 問題が解決しない場合は、Azure Storage サポートにお問い合わせください。その際、エラー メッセージに含まれる要求 ID をお知らせください。 |

### <a name="request-to-azure-data-lake-storage-gen2-account-caused-a-timeout-error"></a>Azure Data Lake Storage Gen2 アカウントへの要求によりタイムアウト エラーが発生する

- **Message**: 
  * エラー コード = `UserErrorFailedBlobFSOperation`
  * エラー メッセージ = `BlobFS operation failed for: A task was canceled.`

- **原因**:この問題は、通常はセルフホステッド統合ランタイム (IR) コンピューターで発生する Azure Data Lake Storage Gen2 シンク タイムアウト エラーが原因で発生します。

- **推奨事項**: 

    - 可能であれば、セルフホステッド IR マシンとターゲット Azure Data Lake Storage Gen2 アカウントを同じリージョンに配置します。 これにより、ランダム タイムアウト エラーを回避し、パフォーマンスを向上させることができます。

    - ExpressRoute などの特別なネットワーク設定があるかどうかを調べ、ネットワークに十分な帯域幅があることを確認します。 全体的な帯域幅が少ない場合は、セルフホステッド IR 同時実行ジョブの設定を小さくすることをお勧めします。 これにより、複数の同時実行ジョブにわたるネットワーク リソースの競合を回避できます。

    - ファイル サイズが中くらいまたは小さい場合に、このようなタイムアウト エラーを軽減するには、バイナリ以外のコピーにはさらに小さいブロック サイズを使用します。 詳細については、「[Blob Storage Put Block](/rest/api/storageservices/put-block)」をご覧ください。

       カスタム ブロック サイズを指定するには、次に示すように、JSON ファイル エディターでプロパティを編集します。
    
        ```
        "sink": {
            "type": "DelimitedTextSink",
            "storeSettings": {
                "type": "AzureBlobFSWriteSettings",
                "blockSizeInMB": 8
            }
        }
        ```

                  
## <a name="azure-files-storage"></a>Azure Files Storage

### <a name="error-code-azurefileoperationfailed"></a>エラー コード:AzureFileOperationFailed

- **メッセージ**: `Azure File operation Failed. Path: %path;. ErrorMessage: %msg;.`

- **原因**:Azure Files ストレージ操作に問題があります。

- **推奨事項**:エラーの詳細を確認するには、[Azure Files のヘルプ](/rest/api/storageservices/file-service-error-codes)を参照してください。 詳細については、Azure Files チームにお問い合わせください。


## <a name="azure-synapse-analytics-azure-sql-database-and-sql-server"></a>Azure Synapse Analytics、Azure SQL Database、および SQL Server

### <a name="error-code-sqlfailedtoconnect"></a>エラー コード:SqlFailedToConnect

- **メッセージ**: `Cannot connect to SQL Database: '%server;', Database: '%database;', User: '%user;'. Check the linked service configuration is correct, and make sure the SQL Database firewall allows the integration runtime to access.`
- **原因と推奨事項**: このエラーはさまざまな原因により発生する可能性があります。 考えられる原因の分析および関連する推奨事項については、以下の一覧を確認してください。

    | 原因分析                                               | 推奨                                               |
    | :----------------------------------------------------------- | :----------------------------------------------------------- |
    | Azure SQL では、エラー メッセージに "SqlErrorNumber=47073" という文字列が含まれている場合、接続設定で公衆ネットワーク アクセスが拒否されていることを意味します。 | Azure SQL ファイアウォールで、 **[Deny public network access]\(公衆ネットワーク アクセスを拒否する\)** オプションを *[いいえ]* に設定します。 詳細については、「[Azure SQL の接続の設定](../azure-sql/database/connectivity-settings.md#deny-public-network-access)」を参照してください。 |
    | Azure SQL の場合、エラー メッセージに "SqlErrorNumber = [エラー コード]" などの SQL エラー コードが含まれている場合は、『Azure SQL DB トラブルシューティング ガイド』を参照してください。 | 推奨事項については、「[Azure SQL Database および Azure SQL Managed Instance の接続に関する問題とその他のエラーのトラブルシューティング](../azure-sql/database/troubleshoot-common-errors-issues.md)」を参照してください。 |
    | ポート 1433 がファイアウォール許可リストに含まれているかどうかを確認します。 | 詳細については、[SQL Server で使用されるポート](/sql/sql-server/install/configure-the-windows-firewall-to-allow-sql-server-access#ports-used-by-)に関する記事を参照してください。 |
    | エラー メッセージに "SqlException" という文字列が含まれている場合、SQL Database のエラーは、何らかの特定の操作が失敗したことを示します。 | 詳細については、「[データベース エンジンのエラー](/sql/relational-databases/errors-events/database-engine-events-and-errors)」の SQL エラー コードを参照してください。 詳細については、Azure SQL サポートにお問い合わせください。 |
    | これが一時的な問題 (不安定なネットワーク接続など) の場合は、アクティビティ ポリシーに再試行を追加して軽減します。 | 詳細については、「[Azure Data Factory のパイプラインとアクティビティ](./concepts-pipelines-activities.md#activity-policy)」を参照してください。 |
    | "Client with IP address '...' is not allowed to access the server" (IP アドレスが '...' のクライアントはサーバーへのアクセスが許可されていません) という文字列がエラー メッセージに含まれており、Azure SQL Database に接続しようとしている場合、通常、このエラーの原因は Azure SQL Database のファイアウォールの問題です。 | Azure SQL Server のファイアウォールの構成で、 **[Azure サービスおよびリソースにこのサーバーへのアクセスを許可する]** オプションを有効にします。 詳細については、[Azure SQL Database と Azure Synapse の IP ファイアウォール規則](../azure-sql/database/firewall-configure.md)に関するページを参照してください。 |
    
### <a name="error-code-sqloperationfailed"></a>エラー コード:SqlOperationFailed

- **メッセージ**: `A database operation failed. Please search error to get more details.`

- **原因と推奨事項**: このエラーはさまざまな原因により発生する可能性があります。 考えられる原因の分析および関連する推奨事項については、以下の一覧を確認してください。

    | 原因分析                                               | 推奨                                               |
    | :----------------------------------------------------------- | :----------------------------------------------------------- |
    | エラー メッセージに "SqlException" という文字列が含まれている場合、SQL Database により、何らかの特定の操作が失敗したことを示すエラーがスローされます。 | SQL エラーが明確でない場合は、データベースを最新の互換性レベル '150' に変更してみてください。 最新バージョンの SQL エラーをスローすることができます。 詳細については、[ドキュメント](/sql/t-sql/statements/alter-database-transact-sql-compatibility-level#backwardCompat)を参照してください。 <br/> SQL の問題のトラブルシューティングの詳細については、「[データベース エンジンのエラー](/sql/relational-databases/errors-events/database-engine-events-and-errors)」の SQL エラー コードを参照してください。 詳細については、Azure SQL サポートにお問い合わせください。 |
    | エラー メッセージに "PdwManagedToNativeInteropException" という文字列が含まれている場合は、通常、ソースとシンクの列のサイズが一致していないことが原因です。 | ソースの列とシンクの列の両方のサイズを確認してください。 詳細については、Azure SQL サポートにお問い合わせください。 |
    | エラー メッセージに "InvalidOperationException" という文字列が含まれている場合は、通常、入力データが無効であることが原因です。 | 問題が発生している行を特定するには、Copy アクティビティでフォールト トレランス機能を有効にしてください。これにより、問題のある行をストレージにリダイレクトして、さらに調査することができます。 詳細については、「[Azure Data Factory のコピー アクティビティのフォールト トレランス](./copy-activity-fault-tolerance.md)」を参照してください。 |


### <a name="error-code-sqlunauthorizedaccess"></a>エラー コード:SqlUnauthorizedAccess

- **メッセージ**: `Cannot connect to '%connectorName;'. Detail Message: '%message;'`

- **原因**:資格情報が正しくないか、ログイン アカウントが SQL データベースにアクセスできません。

- **推奨事項**:SQL Database にアクセスするための十分なアクセス許可がログイン アカウントにあることを確認します。


### <a name="error-code-sqlopenconnectiontimeout"></a>エラー コード:SqlOpenConnectionTimeout

- **メッセージ**: `Open connection to database timeout after '%timeoutValue;' seconds.`

- **原因**:この問題は、SQL Database の一時的な障害である可能性があります。

- **推奨事項**:接続タイムアウトの値を大きくして、リンク サービス接続文字列の更新操作を再試行してください。


### <a name="error-code-sqlautocreatetabletypemapfailed"></a>エラー コード:SqlAutoCreateTableTypeMapFailed

- **メッセージ**: `Type '%dataType;' in source side cannot be mapped to a type that supported by sink side(column name:'%columnName;') in autocreate table.`

- **原因**:自動作成テーブルが、ソースの要件を満たすことができません。

- **推奨事項**:"*マッピング*" で列のデータ型を更新するか、ターゲット サーバーで手動でシンク テーブルを作成してください。


### <a name="error-code-sqldatatypenotsupported"></a>エラー コード:SqlDataTypeNotSupported

- **メッセージ**: `A database operation failed. Check the SQL errors.`

- **原因**:SQL ソースで問題が発生し、エラーが SqlDateTime オーバーフローに関連している場合、データ値が論理型の範囲 (1/1/1753 12:00:00 AM - 12/31/9999 11:59:59 PM) を超えています。

- **推奨事項**:ソースの SQL クエリで型を文字列にキャストするか、Copy アクティビティの列マッピングで列の型を *String* に変更してください。

- **原因**:SQL シンクで問題が発生し、エラーが SqlDateTime オーバーフローに関連している場合、データ値はシンク テーブルで許容される範囲を超えています。

- **推奨事項**:シンク テーブルで、対応する列の型を *datetime2* 型に更新してください。


### <a name="error-code-sqlinvaliddbstoredprocedure"></a>エラー コード:SqlInvalidDbStoredProcedure

- **メッセージ**: `The specified Stored Procedure is not valid. It could be caused by that the stored procedure doesn't return any data. Invalid Stored Procedure script: '%scriptName;'.`

- **原因**:指定されたストアド プロシージャは無効です。 ストアド プロシージャがデータを返さないという原因が考えられます。

- **推奨事項**:SQL ツールを使用してストアド プロシージャを検証してください。 ストアド プロシージャがデータを返せることを確認してください。


### <a name="error-code-sqlinvaliddbquerystring"></a>エラー コード:SqlInvalidDbQueryString

- **メッセージ**: `The specified SQL Query is not valid. It could be caused by that the query doesn't return any data. Invalid query: '%query;'`

- **原因**:指定された SQL クエリは無効です。 クエリがデータを返さないという原因が考えられます。

- **推奨事項**:SQL ツールを使用して SQL クエリを検証してください。 クエリがデータを返せることを確認してください。


### <a name="error-code-sqlinvalidcolumnname"></a>エラー コード:SqlInvalidColumnName

- **メッセージ**: `Column '%column;' does not exist in the table '%tableName;', ServerName: '%serverName;', DatabaseName: '%dbName;'.`

- **原因**:構成が正しくない可能性があるため、列が見つかりません。

- **推奨事項**:クエリの列、データセットの "*構造*"、およびアクティビティの "*マッピング*" を検証してください。


### <a name="error-code-sqlbatchwritetimeout"></a>エラー コード:SqlBatchWriteTimeout

- **メッセージ**: `Timeouts in SQL write operation.`

- **原因**:この問題は、SQL Database の一時的な障害が原因である可能性があります。

- **推奨事項**:操作を再試行してください。 問題が解決しない場合は、Azure SQL サポートにお問い合わせください。


### <a name="error-code-sqlbatchwritetransactionfailed"></a>エラー コード:SqlBatchWriteTransactionFailed

- **メッセージ**: `SQL transaction commits failed.`

- **原因**:例外の詳細が常にトランザクションのタイムアウトを示す場合は、統合ランタイムとデータベース間のネットワーク待機時間が、既定のしきい値の 30 秒を超えています。

- **推奨事項**:SQL リンク サービス接続文字列を、120 以上の "*接続タイムアウト*" 値を使用して更新し、アクティビティを再実行します。

- **原因**:SQL 接続が切断されているという例外の詳細が断続的に示される場合は、一時的なネットワーク障害または SQL Database 側の問題である可能性があります。

- **推奨事項**:アクティビティを再試行し、SQL Database 側のメトリックを確認してください。


### <a name="error-code-sqlbulkcopyinvalidcolumnlength"></a>エラー コード:SqlBulkCopyInvalidColumnLength

- **メッセージ**: `SQL Bulk Copy failed due to receive an invalid column length from the bcp client.`

- **原因**:一括コピー プログラム ユーティリティ (bcp) クライアントから無効な列長を受け取ったため、SQL 一括コピーに失敗しました。

- **推奨事項**:問題が発生した行を特定するには、Copy アクティビティのフォールト トレランス機能を有効にします。 これにより、問題のある行がストレージにリダイレクトされ、さらに調査できるようになります。 詳細については、「[Azure Data Factory のコピー アクティビティのフォールト トレランス](./copy-activity-fault-tolerance.md)」を参照してください。


### <a name="error-code-sqlconnectionisclosed"></a>エラー コード:SqlConnectionIsClosed

- **メッセージ**: `The connection is closed by SQL Database.`

- **原因**:SQL 接続は、同時実行が多い場合に SQL データベースによって閉じられ、サーバーは接続を終了します。

- **推奨事項**:接続を再試行します。 問題が解決しない場合は、Azure SQL サポートにお問い合わせください。


### <a name="error-message-conversion-failed-when-converting-from-a-character-string-to-uniqueidentifier"></a>エラー メッセージ: 文字列から uniqueidentifier に変換中、変換が失敗しました。

- **現象**:ステージング コピーおよび PolyBase を使用して、(SQL Server などの) 表形式のデータ ソースから Azure Synapse Analytics にデータをコピーすると、次のエラーが発生します。

   `ErrorCode=FailedDbOperation,Type=Microsoft.DataTransfer.Common.Shared.HybridDeliveryException, 
    Message=Error happened when loading data into Azure Synapse Analytics., 
    Source=Microsoft.DataTransfer.ClientLibrary,Type=System.Data.SqlClient.SqlException, 
    Message=Conversion failed when converting from a character string to uniqueidentifier...`

- **原因**:Azure Synapse Analytics PolyBase では、空の文字列を GUID に変換することはできません。

- **解決方法**:Copy アクティビティ シンクの PolyBase の設定で、 **[既定の種類を使用します]** オプションを *false* に設定します。


### <a name="error-message-expected-data-type-decimalxx-offending-value"></a>エラー メッセージ:予期されたデータ型: DECIMAL(x,x)、問題のある値

- **現象**:ステージング コピーおよび PolyBase を使用して、(SQL Server などの) 表形式のデータ ソースから Azure Synapse Analytics にデータをコピーすると、次のエラーが発生します。

    `ErrorCode=FailedDbOperation,Type=Microsoft.DataTransfer.Common.Shared.HybridDeliveryException, 
    Message=Error happened when loading data into Azure Synapse Analytics., 
    Source=Microsoft.DataTransfer.ClientLibrary,Type=System.Data.SqlClient.SqlException, 
    Message=Query aborted-- the maximum reject threshold (0 rows) was reached while reading from an external source: 1 rows rejected out of total 415 rows processed. (/file_name.txt)  
    Column ordinal: 18, Expected data type: DECIMAL(x,x), Offending value:..`

- **原因**:Azure Synapse Analytics の PolyBase では、空の文字列 (null 値) を decimal 型の列に挿入することはできません。

- **解決方法**:Copy アクティビティ シンクの PolyBase の設定で、 **[既定の種類を使用します]** オプションを false に設定します。


### <a name="error-message-java-exception-message-hdfsbridgecreaterecordreader"></a>エラー メッセージ:Java の例外メッセージ:HdfsBridge::CreateRecordReader

- **現象**:PolyBase を使用して Azure Synapse Analytics にデータをコピーすると、次のエラーが発生します。

    `Message=110802;An internal DMS error occurred that caused this operation to fail.  
    Details: Exception: Microsoft.SqlServer.DataWarehouse.DataMovement.Common.ExternalAccess.HdfsAccessException, 
    Message: Java exception raised on call to HdfsBridge_CreateRecordReader.  
    Java exception message:HdfsBridge::CreateRecordReader - Unexpected error encountered creating the record reader.: Error [HdfsBridge::CreateRecordReader - Unexpected error encountered creating the record reader.] occurred while accessing external file.....`

- **原因**:この原因として、スキーマ (列幅の合計) が大きすぎる (1 MB を超える) ことが考えられます。 すべての列のサイズを追加して、ターゲットの Azure Synapse Analytics テーブルのスキーマを確認します。

    - Int = 4 バイト
    - Bigint = 8 バイト
    - Varchar(n)、char(n)、binary(n)、varbinary(n) = n バイト
    - Nvarchar(n)、nchar(n) = n*2 バイト
    - Date = 6 バイト
    - Datetime/(2)、smalldatetime = 16 バイト
    - Datetimeoffset = 20 バイト
    - Decimal = 19 バイト
    - Float = 8 バイト
    - Money = 8 バイト
    - Smallmoney = 4 バイト
    - Real = 4 バイト
    - Smallint = 2 バイト
    - Time = 12 バイト
    - Tinyint = 1 バイト

- **解決方法**: 
    - 列幅を 1 MB 未満に縮小します。
    - または、PolyBase を無効にして一括挿入アプローチを使用します。


### <a name="error-message-the-condition-specified-using-http-conditional-headers-is-not-met"></a>エラー メッセージ:HTTP 条件ヘッダーを使用して指定した条件が満たされません。

- **現象**:SQL クエリを使用して Azure Synapse Analytics からデータをプルすると、次のエラーが発生します。

    `...StorageException: The condition specified using HTTP conditional header(s) is not met...`

- **原因**:Azure Storage で外部テーブルのクエリを実行中に、Azure Synapse Analytics で問題が発生しました。

- **解決方法**:SQL Server Management Studio (SSMS) で同じクエリを実行し、同じ結果が得られるかどうかを確認します。 表示される場合は、Azure Synapse Analytics に対するサポート チケットを作成し、ご利用の Azure Synapse Analytics サーバーとデータベース名を入力します。


### <a name="performance-tier-is-low-and-leads-to-copy-failure"></a>パフォーマンス レベルが低いため、コピーに失敗します

- **現象**:Azure SQL Database にデータをコピーすると、次のエラーが表示されます。`Database operation failed. Error message from database execution : ExecuteNonQuery requires an open and available Connection. The connection's current state is closed.`

- **原因**:Azure SQL Database s1 は入出力 (I/O) の制限に達しています。

- **解決方法**:問題を修正するには、Azure SQL Database のパフォーマンス レベルをアップグレードします。 


### <a name="sql-table-cant-be-found"></a>SQL テーブルが見つかりません 

- **現象**:ハイブリッドからデータをオンプレミスの SQL Server テーブルにコピーすると、次のエラーが表示されます。`Cannot find the object "dbo.Contoso" because it does not exist or you do not have permissions.`

- **原因**:現在の SQL アカウントには、.NET SqlBulkCopy.WriteToServer によって発行された要求を実行するための十分なアクセス許可がありません。

- **解決方法**:より高い特権の SQL アカウントに切り替えます。


### <a name="error-message-string-or-binary-data-is-truncated"></a>エラー メッセージ:文字列またはバイナリ データが切り捨てられました

- **現象**:オンプレミスの Azure SQL Server テーブルにデータをコピーすると、エラーが発生します。 

- **原因**:Cx SQL テーブル スキーマ定義に、想定よりも短い列が 1 つ以上あります。

- **解決方法**:この問題を解決するには、次の方法を試してください。

    1. 問題が発生している行のトラブルシューティングを行うには、SQL シンクの[フォールト トレランス](./copy-activity-fault-tolerance.md)を適用します。特に "redirectIncompatibleRowSettings" を適用します。

        > [!NOTE]
        > フォールト トレランスには、追加の実行時間が必要になる場合があります。これにより、コストが高くなる可能性があります。

    2. SQL テーブル スキーマ列の長さでリダイレクトされたデータを再確認し、更新が必要な列を調べます。

    3. それに応じてテーブル スキーマを更新します。


## <a name="azure-table-storage"></a>Azure Table Storage

### <a name="error-code-azuretableduplicatecolumnsfromsource"></a>エラー コード:AzureTableDuplicateColumnsFromSource

- **メッセージ**: `Duplicate columns with same name '%name;' are detected from source. This is NOT supported by Azure Table Storage sink.`

- **原因**:重複するソース列は、次のいずれかの理由で発生する可能性があります。
   * データベースをソースとして使用し、テーブル結合を適用しています。
   * ヘッダー行に重複する列名を含む非構造化 CSV ファイルがあります。

- **推奨事項**:必要に応じて、ソース列を再確認して修正します。


## <a name="db2"></a>DB2

### <a name="error-code-db2driverrunfailed"></a>エラー コード:DB2DriverRunFailed

- **メッセージ**: `Error thrown from driver. Sql code: '%code;'`

- **原因**:エラー メッセージに "SQLSTATE=51002 SQLCODE=-805" という文字列が含まれている場合は、「[Azure Data Factory を使用して DB2 からデータをコピーする](./connector-db2.md#linked-service-properties)」の「ヒント」に従います。

- **推奨事項**:`packageCollection` プロパティで "NULLID" の設定を試みます。


## <a name="delimited-text-format"></a>区切りテキスト形式

### <a name="error-code-delimitedtextcolumnnamenotallownull"></a>エラー コード:DelimitedTextColumnNameNotAllowNull

- **メッセージ**: `The name of column index %index; is empty. Make sure column name is properly specified in the header row.`

- **原因**:アクティビティに 'firstRowAsHeader' が設定されている場合、最初の行が列名として使用されます。 このエラーは、最初の行に空の値が含まれていることを意味します (たとえば、'ColumnA, ColumnB')。

- **推奨事項**:最初の行を確認し、値が空の場合は修正します。


### <a name="error-code-delimitedtextmorecolumnsthandefined"></a>エラー コード:DelimitedTextMoreColumnsThanDefined

- **メッセージ**: `Error found when processing '%function;' source '%name;' with row number %rowCount;: found more columns than expected column count: %expectedColumnCount;.`

- **原因と推奨事項**: このエラーはさまざまな原因により発生する可能性があります。 考えられる原因の分析および関連する推奨事項については、以下の一覧を確認してください。

  | 原因分析                                               | 推奨                                               |
  | :----------------------------------------------------------- | :----------------------------------------------------------- |
  | 問題のある行の列数が、最初の行の列数を超えています。 データの問題、または列区切り記号や引用符文字の設定が正しくないことが原因である可能性があります。 | エラー メッセージから行数を取得し、行の列を確認して、データを修正してください。 |
  | エラー メッセージに予想される列数が "1" の場合は、正しくない圧縮やフォーマット設定が指定されている可能性があります。これにより、Azure Data Factory によってファイルが正しく解析されませんでした。 | フォーマット設定を確認して、ソース ファイルと一致していることを確認してください。 |
  | ソースがフォルダーの場合、指定されたフォルダーの下にあるファイルのスキーマが異なる可能性があります。 | 指定されたフォルダー内のファイルのスキーマが同じであることを確認します。 |


## <a name="dynamics-365-common-data-service-and-dynamics-crm"></a>Dynamics 365、Common Data Service、Dynamics CRM

### <a name="error-code-dynamicscreateserviceclienterror"></a>エラー コード:DynamicsCreateServiceClientError

- **メッセージ**: `This is a transient issue on Dynamics server side. Try to rerun the pipeline.`

- **原因**:この問題は、Dynamics サーバー側での一時的な問題です。

- **推奨事項**:パイプラインを再実行してください。 再度失敗した場合は、並列処理を減らしてみてください。 問題が解決しない場合は、Dynamics サポートにお問い合わせください。


### <a name="missing-columns-when-you-import-a-schema-or-preview-data"></a>スキーマをインポートするとき、またはデータをプレビューするときに列が見つからない

- **現象**:スキーマをインポートするとき、またはデータをプレビューするときに、いくつかの列が欠落しています。 エラー メッセージ: `The valid structure information (column name and type) are required for Dynamics source.`

- **原因**:この問題は仕様によるものであり、最初の 10 個のレコード内の値が含まれていない列を Azure Data Factory が表示できないためです。 追加した列が正しい形式であることを確認します。 

- **推奨事項**:マッピング タブで列を手動で追加します。


### <a name="error-code-dynamicsmissingtargetformultitargetlookupfield"></a>エラー コード:DynamicsMissingTargetForMultiTargetLookupField

- **メッセージ**: `Cannot find the target column for multi-target lookup field: '%fieldName;'.`

- **原因**:ターゲット列がソースまたは列マッピングに存在しません。

- **推奨事項**:  
  1. ソースにターゲット列が含まれていることを確認します。 
  2. 列マッピングにターゲット列を追加します。 シンク列が *{fieldName}@EntityReference* の形式であることを確認してください。


### <a name="error-code-dynamicsinvalidtargetformultitargetlookupfield"></a>エラー コード:DynamicsInvalidTargetForMultiTargetLookupField

- **メッセージ**: `The provided target: '%targetName;' is not a valid target of field: '%fieldName;'. Valid targets are: '%validTargetNames;'`

- **原因**:複数ターゲット検索フィールドのターゲット エンティティとして不正なエンティティ名が指定されています。

- **推奨事項**:複数ターゲット検索フィールドの有効なエンティティ名を指定します。


### <a name="error-code-dynamicsinvalidtypeformultitargetlookupfield"></a>エラー コード:DynamicsInvalidTypeForMultiTargetLookupField

- **メッセージ**: `The provided target type is not a valid string. Field: '%fieldName;'.`

- **原因**:ターゲット列の値が文字列ではありません。

- **推奨事項**:複数ターゲット検索フィールドに有効な文字列を指定します。


### <a name="error-code-dynamicsfailedtorequetserver"></a>エラー コード:DynamicsFailedToRequetServer

- **メッセージ**: `The Dynamics server or the network is experiencing issues. Check network connectivity or check Dynamics server log for more details.`

- **原因**:Dynamics サーバーが不安定であるか、アクセスできないか、ネットワークに問題が発生しています。

- **推奨事項**:詳細については、ネットワーク接続または Dynamics サーバーのログを確認してください。 さらに支援が必要な場合は、Dynamics サポートにお問い合わせください。
  

## <a name="ftp"></a>FTP

### <a name="error-code-ftpfailedtoconnecttoftpserver"></a>エラー コード:FtpFailedToConnectToFtpServer

- **メッセージ**: `Failed to connect to FTP server. Please make sure the provided server information is correct, and try again.`

- **原因**:FTP サーバーに対し、FTP サーバーへの接続に Secure FTP (SFTP) リンク サービスを使用するなど、リンク サービス タイプとして正しくないものが使用されている可能性があります。

- **推奨事項**:ターゲット サーバーのポートを確認します。 FTP はポート 21 を使用します。


## <a name="http"></a>HTTP

### <a name="error-code-httpfilefailedtoread"></a>エラー コード:HttpFileFailedToRead

- **メッセージ**: `Failed to read data from http server. Check the error from http server：%message;`

- **原因**:このエラーは、Azure Data Factory が HTTP サーバーと通信しているが、HTTP 要求操作が失敗した場合に発生します。

- **推奨事項**:エラー メッセージの HTTP ステータス コードを確認し、リモート サーバーの問題を修正します。


## <a name="oracle"></a>Oracle

### <a name="error-code-argumentoutofrangeexception"></a>エラー コード:ArgumentOutOfRangeException

- **メッセージ**: `Hour, Minute, and Second parameters describe an un-representable DateTime.`

- **原因**:Data Factory では、DateTime 値は、0001-01-01 00:00:00 から 9999-12-31 23:59:59 の範囲でサポートされています。 ただし、Oracle では、紀元前や分/秒 > 59 など、より広い範囲の DateTime 値がサポートされているため、Azure Data Factory でエラーが発生します。

- **推奨事項**: 

    Oracle の値が Azure Data Factory の範囲内にあるかどうかを確認するには、`select dump(<column name>)` を実行します。 

    結果のバイト シーケンスの詳細については、[Oracle に格納されている日付](https://stackoverflow.com/questions/13568193/how-are-dates-stored-in-oracle)に関する記事を参照してください。


## <a name="orc-format"></a>ORC 形式

### <a name="error-code-orcjavainvocationexception"></a>エラー コード:OrcJavaInvocationException

- **メッセージ**: `An error occurred when invoking Java, message: %javaException;.`
- **原因と推奨事項**: このエラーはさまざまな原因により発生する可能性があります。 考えられる原因の分析および関連する推奨事項については、以下の一覧を確認してください。

    | 原因分析                                               | 推奨                                               |
    | :----------------------------------------------------------- | :----------------------------------------------------------- |
    | エラー メッセージに "java.lang.OutOfMemory"、"Java heap space"、および "doubleCapacity" という文字列が含まれている場合は、通常、古いバージョンの Integration Runtime でのメモリ管理の問題です。 | セルフホステッド統合ランタイムを使用している場合は、最新バージョンにアップグレードすることをお勧めします。 |
    | エラー メッセージに "java.lang.OutOfMemory" という文字列が含まれている場合は、統合ランタイムにファイルを処理するのに十分なリソースがありません。 | 統合ランタイムで同時実行を制限してください。 セルフホステッド IR の場合は、8 GB 以上のメモリを備えた強力なマシンにスケールアップしてください。 |
    |エラー メッセージに "NullPointerReference" という文字列が含まれている場合、その原因は一時的なエラーである可能性があります。 | 操作を再試行してください。 問題が解決しない場合は、サポートにお問い合わせください。 |
    | エラー メッセージに "BufferOverflowException" という文字列が含まれている場合、その原因は一時的なエラーである可能性があります。 | 操作を再試行してください。 問題が解決しない場合は、サポートにお問い合わせください。 |
    | エラー メッセージに "java.lang.ClassCastException:org.apache.hadoop.hive.serde2.io.HiveCharWritable can't be cast to org.apache.hadoop.io.Text" (java.lang.ClassCastException:org.apache.hadoop.hive.serde2.io.HiveCharWritable を org.apache.hadoop.io.Text にキャストできません) という文字列が含まれている場合、その原因は Java ランタイム内の型変換の問題である可能性があります。 通常は、ソース データを Java ランタイムで適切に処理できないことを意味します。 | これはデータの問題です。 ORC 形式のデータで、char または varchar の代わりに string を使用してみてください。 |

### <a name="error-code-orcdatetimeexceedlimit"></a>エラー コード:OrcDateTimeExceedLimit

- **メッセージ**: `The Ticks value '%ticks;' for the datetime column must be between valid datetime ticks range -621355968000000000 and 2534022144000000000.`

- **原因**:datetime 値が '0001-01-01 00:00:00' である場合は、[ユリウス暦とグレゴリオ暦](https://en.wikipedia.org/wiki/Proleptic_Gregorian_calendar#Difference_between_Julian_and_proleptic_Gregorian_calendar_dates)の違いが原因で発生する可能性があります。

- **推奨事項**:ティック値を確認し、datetime 値 '0001-01-01 00:00:00' を使用しないようにしてください。


## <a name="parquet-format"></a>Parquet 形式

### <a name="error-code-parquetjavainvocationexception"></a>エラー コード:ParquetJavaInvocationException

- **メッセージ**: `An error occurred when invoking java, message: %javaException;.`

- **原因と推奨事項**: このエラーはさまざまな原因により発生する可能性があります。 考えられる原因の分析および関連する推奨事項については、以下の一覧を確認してください。

    | 原因分析                                               | 推奨                                               |
    | :----------------------------------------------------------- | :----------------------------------------------------------- |
    | エラー メッセージに "java.lang.OutOfMemory"、"Java heap space"、および "doubleCapacity" という文字列が含まれている場合は、通常、古いバージョンの Integration Runtime でのメモリ管理の問題です。 | セルフホステッド IR を使用していて、バージョンが 3.20.7159.1 よりも前の場合は、最新バージョンにアップグレードすることをお勧めします。 |
    | エラー メッセージに "java.lang.OutOfMemory" という文字列が含まれている場合は、統合ランタイムにファイルを処理するのに十分なリソースがありません。 | 統合ランタイムで同時実行を制限してください。 セルフホステッド IR の場合は、8 GB 以上のメモリを持つ強力なマシンにスケールアップします。 |
    | エラー メッセージに "NullPointerReference" という文字列が含まれている場合、一時的なエラーである可能性があります。 | 操作を再試行してください。 問題が解決しない場合は、サポートにお問い合わせください。 |

### <a name="error-code-parquetinvalidfile"></a>エラー コード:ParquetInvalidFile

- **メッセージ**: `File is not a valid Parquet file.`

- **原因**:これは Parquet ファイルの問題です。

- **推奨事項**:入力が有効な Parquet ファイルであるかどうかを確認します。


### <a name="error-code-parquetnotsupportedtype"></a>エラー コード:ParquetNotSupportedType

- **メッセージ**: `Unsupported Parquet type. PrimitiveType: %primitiveType; OriginalType: %originalType;.`

- **原因**:Parquet 形式は、Azure Data Factory ではサポートされていません。

- **推奨事項**:「[Azure Data Factory のコピー アクティビティでサポートされているファイル形式と圧縮コーデック](./supported-file-formats-and-compression-codecs.md)」に移動して、ソース データを再確認します。


### <a name="error-code-parquetmisseddecimalprecisionscale"></a>エラー コード:ParquetMissedDecimalPrecisionScale

- **メッセージ**: `Decimal Precision or Scale information is not found in schema for column: %column;.`

- **原因**:数値の有効桁数と小数点以下桁数が解析されましたが、そのような情報は提供されませんでした。

- **推奨事項**:ソースは、正しい有効桁数と小数点以下桁数の情報を返しません。 問題の列で情報を確認します。


### <a name="error-code-parquetinvaliddecimalprecisionscale"></a>エラー コード:ParquetInvalidDecimalPrecisionScale

- **メッセージ**: `Invalid Decimal Precision or Scale. Precision: %precision; Scale: %scale;.`

- **原因**:スキーマが無効です。

- **推奨事項**:問題の列の有効桁数と小数点以下桁数を確認してください。


### <a name="error-code-parquetcolumnnotfound"></a>エラー コード:ParquetColumnNotFound

- **メッセージ**: `Column %column; does not exist in Parquet file.`

- **原因**:ソース スキーマが、シンク スキーマと一致していません。

- **推奨事項**:アクティビティのマッピングを確認してください。 ソース列を正しいシンク列にマッピングできることを確認してください。


### <a name="error-code-parquetinvaliddataformat"></a>エラー コード:ParquetInvalidDataFormat

- **メッセージ**: `Incorrect format of %srcValue; for converting to %dstType;.`

- **原因**:データは、mappings.source に指定されている型に変換できません。

- **推奨事項**:ソース データを再確認するか、Copy アクティビティの列マッピングでこの列に適切なデータ型を指定してください。 詳細については、「[Azure Data Factory のコピー アクティビティでサポートされているファイル形式と圧縮コーデック](./supported-file-formats-and-compression-codecs.md)」を参照してください。


### <a name="error-code-parquetdatacountnotmatchcolumncount"></a>エラー コード:ParquetDataCountNotMatchColumnCount

- **メッセージ**: `The data count in a row '%sourceColumnCount;' does not match the column count '%sinkColumnCount;' in given schema.`

- **原因**:ソース列数とシンクの列数が一致しません。

- **推奨事項**:ソース列数が 'mapping' のシンク列数と同じであることを確認してください。


### <a name="error-code-parquetdatatypenotmatchcolumntype"></a>エラー コード:ParquetDataTypeNotMatchColumnType

- **メッセージ**: `The data type %srcType; is not match given column type %dstType; at column '%columnIndex;'.`

- **原因**:ソースからのデータは、シンクで定義されている型に変換できません。

- **推奨事項**:mapping.sink に正しい型を指定してください。


### <a name="error-code-parquetbridgeinvaliddata"></a>エラー コード:ParquetBridgeInvalidData

- **メッセージ**: `%message;`

- **原因**:データ値が制限を超えています。

- **推奨事項**:操作を再試行してください。 問題が解決しない場合は、サポートにお問い合わせください。


### <a name="error-code-parquetunsupportedinterpretation"></a>エラー コード:ParquetUnsupportedInterpretation

- **メッセージ**: `The given interpretation '%interpretation;' of Parquet format is not supported.`

- **原因**:このシナリオはサポートされていません。

- **推奨事項**:'ParquetInterpretFor' を 'sparkSql' にしないでください。


### <a name="error-code-parquetunsupportfilelevelcompressionoption"></a>エラー コード:ParquetUnsupportFileLevelCompressionOption

- **メッセージ**: `File level compression is not supported for Parquet.`

- **原因**:このシナリオはサポートされていません。

- **推奨事項**:ペイロード内の 'CompressionType' を削除してください。


### <a name="error-code-usererrorjniexception"></a>エラー コード:UserErrorJniException

- **メッセージ**: `Cannot create JVM: JNI return code [-6][JNI call failed: Invalid arguments.]`

- **原因**:無効な (グローバル) 引数が設定されているため、Java 仮想マシン (JVM) を作成できません。

- **推奨事項**:セルフホステッド IR の *各ノード* をホストするマシンにログインします。 次のように、システム変数が正しく設定されていることを確認します`_JAVA_OPTIONS "-Xms256m -Xmx16g" with memory bigger than 8 G`。 すべての IR ノードを再起動してから、パイプラインを再実行します。


### <a name="arithmetic-overflow"></a>算術オーバーフロー

- **現象**:Parquet ファイルをコピーするときに、次のエラー メッセージが発生しました。`Message = Arithmetic Overflow., Source = Microsoft.DataTransfer.Common`

- **原因**:現時点では、Oracle から Parquet にファイルをコピーするときにサポートされている値は、小数点以下の桁数が 38 桁以内で整数部分の桁数が 20 以下のものだけです。 

- **解決方法**:回避策として、この問題のある列を VARCHAR2 に変換できます。


### <a name="no-enum-constant"></a>列挙型定数はありません

- **現象**:Parquet 形式にデータをコピーするときに、次のエラー メッセージが発生しました。`java.lang.IllegalArgumentException:field ended by &apos;;&apos;`、または `java.lang.IllegalArgumentException:No enum constant org.apache.parquet.schema.OriginalType.test`。

- **原因**: 

    この問題は、列名に空白またはサポートされていない特殊文字 (;{}()\n\t= など) があることによって発生する可能性があります。Parquet はこのような形式をサポートしていないためです。 

    たとえば、*contoso(test)* のような列名は [コード](https://github.com/apache/parquet-mr/blob/master/parquet-column/src/main/java/org/apache/parquet/schema/MessageTypeParser.java) `Tokenizer st = new Tokenizer(schemaString, " ;{}()\n\t");` の丸かっこで囲まれた型を解析します。 このような "test" 型がないため、エラーがスローされます。

    サポートされている型を確認するには、GitHub の [apache/parquet-mr サイト](https://github.com/apache/parquet-mr/blob/master/parquet-column/src/main/java/org/apache/parquet/schema/OriginalType.java)にアクセスしてください。

- **解決方法**: 

    次の点を確認してください。
    - シンク列名に空白が含まれています。
    - 空白がある最初の行が、列名として使用されています。
    - 型 OriginalType はサポートされています。 次の特殊文字を使用しないようにしてください: `,;{}()\n\t=`。 


## <a name="rest"></a>REST

### <a name="error-code-restsinkcallfailed"></a>エラー コード:RestSinkCallFailed

- **メッセージ**: `Rest Endpoint responded with Failure from server. Check the error from server:%message;`

- **原因**:このエラーは Azure Data Factory が HTTP プロトコル経由で REST エンドポイントと通信し、要求操作が失敗した場合に発生します。

- **推奨事項**:エラー メッセージの HTTP ステータス コードまたはメッセージを確認し、リモート サーバーの問題を修正します。

### <a name="unexpected-network-response-from-the-rest-connector"></a>REST コネクタからの予期しないネットワーク応答

- **現象**:エンドポイントは、REST コネクタから予期しない応答 (400、401、403、500) を受け取ることがあります。

- **原因**:REST ソース コネクタは、HTTP 要求を構築するときに、リンク サービス/データセット/コピー元からの URL および HTTP メソッド、ヘッダー、本文をパラメーターとして使用します。 この問題は、指定された 1 つ以上のパラメーターにおける何らかの誤りが原因で発生している可能性があります。

- **解決方法**: 
    - コマンド プロンプト ウィンドウで "curl" を使用して、パラメーターが原因であるかどうかを確認します (**Accept** ヘッダーと **User-Agent** ヘッダーを常に含める必要があります)。
    
      `curl -i -X <HTTP method> -H <HTTP header1> -H <HTTP header2> -H "Accept: application/json" -H "User-Agent: azure-data-factory/2.0" -d '<HTTP body>' <URL>`
      
      コマンドが同じ予期しない応答を返す場合は、予期される応答を返すまで、"curl" を使って上記のパラメーターを修正してください。 

      "curl--help" を使用して、より高度な方法でコマンドを使用することもできます。

    - Data Factory REST コネクタのみが予期しない応答を返す場合、詳細なトラブルシューティングについて、Microsoft サポートにお問い合わせください。
    
    - "curl" は SSL 証明書の検証問題を再現するのに適さない場合があることに注意してください。 一部のシナリオでは、SSL 証明書の検証に問題が発生せず、"curl" コマンドが正常に実行されました。 ただし、同じ URL をブラウザーで実行すると、クライアントがサーバーとの信頼関係を確立するための SSL 証明書は実際には返されません。

      上記のケースでは、**Postman** や **Fiddler** などのツールを使用することをお勧めします。


## <a name="sftp"></a>SFTP

#### <a name="error-code-sftpoperationfail"></a>エラー コード:SftpOperationFail

- **メッセージ**: `Failed to '%operation;'. Check detailed error from SFTP.`

- **原因**:SFTP 操作に問題があります。

- **推奨事項**:SFTP からエラーの詳細を確認します。


### <a name="error-code-sftprenameoperationfail"></a>エラー コード:SftpRenameOperationFail

- **メッセージ**: `Failed to rename the temp file. Your SFTP server doesn't support renaming temp file, set "useTempFileRename" as false in copy sink to disable uploading to temp file.`

- **原因**:SFTP サーバーでは、一時ファイルの名前変更はサポートされません。

- **推奨事項**:コピー シンクで "useTempFileRename" を false に設定して、一時ファイルへのアップロードを無効にします。


### <a name="error-code-sftpinvalidsftpcredential"></a>エラー コード:SftpInvalidSftpCredential

- **メッセージ**: `Invalid SFTP credential provided for '%type;' authentication type.`

- **原因**:秘密キーのコンテンツは Azure Key Vault または SDK からフェッチされますが、正しくエンコードされていません。

- **推奨事項**:  

    秘密キーのコンテンツが Key Vault からのものである場合、SFTP リンク サービスに直接アップロードすると、元のキー ファイルが機能する可能性があります。

    詳細については、「[Azure Data Factory を使用して SFTP サーバーとの間でデータをコピーする](./connector-sftp.md#use-ssh-public-key-authentication)」を参照してください。 秘密キーのコンテンツは、Base64 でエンコードされた SSH 秘密キーのコンテンツです。

    Base64 エンコードを使用して元の秘密キー ファイル "*全体*" をエンコードし、エンコードされた文字列を Key Vault に格納します。 ファイルから **[アップロード]** を選択した場合、元の秘密キー ファイルが、SFTP リンク サービスで使用できるキー ファイルです。

    次に、文字列の生成に使用できるサンプルをいくつか示します。

    - C# コードを使用した場合:

        ```
        byte[] keyContentBytes = File.ReadAllBytes(Private Key Path);
        string keyContent = Convert.ToBase64String(keyContentBytes, Base64FormattingOptions.None);
        ```

    - Python コードを使用した場合:

        ```
        import base64
        rfd = open(r'{Private Key Path}', 'rb')
        keyContent = rfd.read()
        rfd.close()
        print base64.b64encode(Key Content)
        ```

    - サードパーティの Base64 変換ツールを使用します。 [Encode to Base64 format](https://www.base64encode.org/) ツールをお勧めします。

- **原因**:間違ったキー コンテンツ形式が選択されました。

- **推奨事項**:  

    現在、PKCS#8 形式の SSH 秘密キー ("-----BEGIN ENCRYPTED PRIVATE KEY-----" で開始) は、Data Factory での SFTP サーバーへのアクセスにはサポートされていません。 

    キーを "-----BEGIN RSA PRIVATE KEY-----" から始まる従来の SSH キー形式に変換するには、次のコマンドを実行します。

    ```
    openssl pkcs8 -in pkcs8_format_key_file -out traditional_format_key_file
    chmod 600 traditional_format_key_file
    ssh-keygen -f traditional_format_key_file -p
    ```

- **原因**:資格情報または秘密キーのコンテンツが無効です。

- **推奨事項**:キー ファイルまたはパスワードが正しいかどうかを確認するには、WinSCP などのツールを使用して再確認します。

### <a name="sftp-copy-activity-failed"></a>SFTP Copy アクティビティが失敗しました

- **現象**: 
  * エラー コード:UserErrorInvalidColumnMappingColumnNotFound 
  * エラー メッセージ: `Column 'AccMngr' specified in column mapping cannot be found in source data.`

- **原因**:ソースには、"AccMngr" という名前の列が含まれていません。

- **解決方法**:"AccMngr" 列が存在するかどうかを判断するには、コピー先のデータセット列をマップして、データセットの構成を再確認します。


### <a name="error-code-sftpfailedtoconnecttosftpserver"></a>エラー コード:SftpFailedToConnectToSftpServer

- **メッセージ**: `Failed to connect to SFTP server '%server;'.`

- **原因**:エラー メッセージに "ソケット読み取り操作が 3 万ミリ秒後にタイムアウトしました" という文字列が含まれている場合、原因の 1 つとして、SFTP サーバーに無効なリンクされたサービス タイプが使用されていることが考えられます。 たとえば、SFTP サーバーへの接続に、FTP リンク サービスを使用している可能性があります。

- **推奨事項**:ターゲット サーバーのポートを確認します。 既定では、SFTP はポート 22 を使用します。

- **原因**:エラー メッセージに "Server response does not contain SSH protocol identification" (サーバーの応答に SSH プロトコルの ID が含まれていません) という文字列が含まれている場合、原因の 1 つとして、SFTP サーバーによって接続が調整されたことが考えられます。 Data Factory が複数の接続を作成して、SFTP サーバーから並列でダウンロードするため、SFTP サーバーの帯域幅調整が行われる場合があります。 通常、調整が発生すると、サーバーごとに異なるエラーが返されます。

- **推奨事項**:  

    SFTP データセットのコンカレント接続の最大数を 1 として指定し、Copy アクティビティを再実行します。 アクティビティが成功した場合は、調整が原因であることを確認できます。

    低スループットを昇格する場合は、SFTP 管理者に問い合わせて、コンカレント接続数の上限を引き上げるか、次のいずれかを実行できます。

    * セルフホステッド IR を使用している場合は、セルフホステッド IR コンピューターの IP を許可リストに追加します。
    * Azure IR を使用している場合は、[Azure Integration Runtime IP アドレス](./azure-integration-runtime-ip-addresses.md)を追加します。 SFTP サーバーの許可リストに IP の範囲を追加しない場合は、代わりにセルフホステッド IR を使用してください。

## <a name="sharepoint-online-list"></a>SharePoint Online リスト

### <a name="error-code-sharepointonlineauthfailed"></a>エラー コード:SharePointOnlineAuthFailed

- **メッセージ**: `The access token generated failed, status code: %code;, error message: %message;.`

- **原因**:サービス プリンシパル ID とキーが正しく設定されていない可能性があります。

- **推奨事項**:登録されているアプリケーション (サービス プリンシパル ID) とキーが正しく設定されているかどうかを確認します。


## <a name="xml-format"></a>XML 形式

### <a name="error-code-xmlsinknotsupported"></a>エラー コード:XmlSinkNotSupported

- **メッセージ**: `Write data in XML format is not supported yet, choose a different format!`

- **原因**:XML データセットは、Copy アクティビティでシンク データセットとして使用されていました。

- **推奨事項**:シンク データセットとは異なる形式のデータセットを使用します。


### <a name="error-code-xmlattributecolumnnameconflict"></a>エラー コード:XmlAttributeColumnNameConflict

- **メッセージ**: `Column names %attrNames;' for attributes of element '%element;' conflict with that for corresponding child elements, and the attribute prefix used is '%prefix;'.`

- **原因**:属性プレフィックスを使用しました。これが競合の原因になりました。

- **推奨事項**:"attributePrefix" プロパティに別の値を設定します。


### <a name="error-code-xmlvaluecolumnnameconflict"></a>エラー コード:XmlValueColumnNameConflict

- **メッセージ**: `Column name for the value of element '%element;' is '%columnName;' and it conflicts with the child element having the same name.`

- **原因**:子要素名の 1 つを、要素値の列名として使用しました。

- **推奨事項**:"valueColumn" プロパティに別の値を設定します。


### <a name="error-code-xmlinvalid"></a>エラー コード:XmlInvalid

- **メッセージ**: `Input XML file '%file;' is invalid with parsing error '%error;'.`

- **原因**:入力 XML ファイルの形式が正しくありません。

- **推奨事項**:正しい形式になるように XML ファイルを修正します。


## <a name="general-copy-activity-error"></a>一般的な Copy アクティビティのエラー

### <a name="error-code-jrenotfound"></a>エラー コード:JreNotFound

- **メッセージ**: `Java Runtime Environment cannot be found on the Self-hosted Integration Runtime machine. It is required for parsing or writing to Parquet/ORC files. Make sure Java Runtime Environment has been installed on the Self-hosted Integration Runtime machine.`

- **原因**:セルフホステッド IR は、Java ランタイムを見つけることができません。 Java ランタイムは、特定のソースを読み取るために必要です。

- **推奨事項**:統合ランタイム環境を確認するには、[セルフホステッド統合ランタイムの使用](./format-parquet.md#using-self-hosted-integration-runtime)に関する記事を参照してください。


### <a name="error-code-wildcardpathsinknotsupported"></a>エラー コード:WildcardPathSinkNotSupported

- **メッセージ**: `Wildcard in path is not supported in sink dataset. Fix the path: '%setting;'.`

- **原因**:シンク データセットでは、ワイルドカード値はサポートされません。

- **推奨事項**:シンク データセットを確認し、ワイルドカード値を使用せずにパスを書き直します。


### <a name="fips-issue"></a>FIPS の問題

- **現象**:次のエラー メッセージが表示された FIPS 対応のセルフホステッド IR コンピューターでは、Copy アクティビティが失敗します: `This implementation is not part of the Windows Platform FIPS validated cryptographic algorithms.` 

- **原因**:このエラーは、Azure BLOB、SFTP などのコネクタを使用してデータをコピーするときに発生する可能性があります。 Federal Information Processing Standards (FIPS) では、使用が許可される暗号化アルゴリズムの特定のセットを定義します。 マシンで FIPS モードが有効になっている場合、一部のシナリオでは、コピー アクティビティが依存する一部の暗号化クラスがブロックされます。

- **解決方法**:[現在は "FIPS モード" の使用を推奨していない理由](https://techcommunity.microsoft.com/t5/microsoft-security-baselines/why-we-8217-re-not-recommending-8220-fips-mode-8221-anymore/ba-p/701037)と、セルフホステッド IR コンピューターで FIPS を無効にできるかどうかを評価する方法について参照してください。

    または、Azure Data Factory が FIPS をバイパスしてアクティビティを正常に実行できるようにするだけの場合は、次の手順を実行します。

    1. セルフホステッド IR がインストールされているフォルダーを開きます。 パスは通常、*C:\Program Files\Microsoft Integration Runtime \<IR version>\Shared* です。

    2. *diawp.exe.config* ファイルを開き、`<runtime>` セクションの最後で、次のように `<enforceFIPSPolicy enabled="false"/>` を追加します。

        ![FIPS が無効になっていることを示す、diawp.exe.config ファイルのセクションのスクリーンショット。](./media/connector-troubleshoot-guide/disable-fips-policy.png)

    3. ファイルを保存し、セルフホステッド IR コンピューターを再起動します。


## <a name="next-steps"></a>次のステップ

トラブルシューティングのその他のヘルプについては、次のリソースを参照してください。

*  [Data Factory ブログ](https://azure.microsoft.com/blog/tag/azure-data-factory/)
*  [Data Factory の機能のリクエスト](https://feedback.azure.com/forums/270578-data-factory)
*  [Azure のビデオ](https://azure.microsoft.com/resources/videos/index/?sort=newest&services=data-factory)
*  [Microsoft Q&A ページ](/answers/topics/azure-data-factory.html)
*  [Data Factory の Stack Overflow フォーラム](https://stackoverflow.com/questions/tagged/azure-data-factory)
*  [Data Factory に関する Twitter 情報](https://twitter.com/hashtag/DataFactory)