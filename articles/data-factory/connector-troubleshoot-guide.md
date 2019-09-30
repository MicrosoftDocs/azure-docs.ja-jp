---
title: Azure Data Factory コネクタのトラブルシューティング | Microsoft Docs
description: Azure Data Factory でのコネクタに関する問題のトラブルシューティングを行う方法について説明します。
services: data-factory
author: linda33wj
ms.service: data-factory
ms.topic: troubleshooting
ms.date: 08/26/2019
ms.author: jingwang
ms.reviewer: craigg
ms.openlocfilehash: bea5191063cf673f6b1395d46a15536e80b0aa30
ms.sourcegitcommit: d200cd7f4de113291fbd57e573ada042a393e545
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/29/2019
ms.locfileid: "70143222"
---
# <a name="troubleshoot-azure-data-factory-connectors"></a>Azure Data Factory コネクタのトラブルシューティング

この記事では、Azure Data Factory のコネクタの一般的なトラブルシューティング方法について説明します。

## <a name="azure-data-lake-storage"></a>Azure Data Lake Storage

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

## <a name="azure-sql-data-warehouse"></a>Azure SQL Data Warehouse

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

### <a name="error-message-java-exception-messagehdfsbridgecreaterecordreader"></a>エラー メッセージ:Java 例外メッセージ: HdfsBridge::CreateRecordReader

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

## <a name="azure-cosmos-db"></a>Azure Cosmos DB

### <a name="error-message-request-size-is-too-large"></a>エラー メッセージ: 要求のサイズが大きすぎます。

- **現象**: 既定の書き込みバッチ サイズを使用して Azure Cosmos DB にデータをコピーすると、*"**要求のサイズが大きすぎます**"* というエラーが表示されます。

- **原因**: Cosmos DB では、単一の要求のサイズが 2 MB に制限されます。 式は、"要求サイズ = 1 つのドキュメント サイズ * 書き込みバッチ サイズ" となります。 ドキュメントのサイズが大きい場合、既定の動作の結果として要求サイズが過度に大きくなります。 書き込みバッチ サイズを調整することができます。

- **解決方法**: コピー アクティビティのシンクで、"書き込みバッチ サイズ" の値を小さくします (既定値は 10000)。

### <a name="error-message-unique-index-constraint-violation"></a>エラー メッセージ: 一意なインデックスの制約違反

- **現象**: Cosmos DB にデータをコピーすると、次のエラーが発生します。

    ```
    Message=Partition range id 0 | Failed to import mini-batch. 
    Exception was Message: {"Errors":["Encountered exception while executing function. Exception &#61; Error: {\"Errors\":[\"Unique index constraint violation.\"]}... 
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

## <a name="sftp"></a>SFTP

### <a name="error-message-invalid-sftp-credential-provided-for-sshpublickey-authentication-type"></a>エラー メッセージ:認証の種類 'SshPublicKey ' に対して指定した SFTP 資格情報が無効です

- **現象**: `SshPublicKey` 認証を使用すると、次のエラーが表示されます。

    ```
    Invalid Sftp credential provided for 'SshPublicKey' authentication type
    ```

- **原因**: 考えられる 3 つの原因

    1. SFTP にリンクされたサービスを ADF 作成 UI を使用して作成する場合、このエラーは、使用するために選択した秘密キーの形式が間違っていることを意味します。 PKCS#8 形式の SSH 秘密キーが使用できますが、ADF では従来の SSH キー形式しかサポートされていないことに注意してください。 具体的には、PKCS#8 形式と従来のキー形式の違いは、PKCS#8 キー コンテンツでは、“ *-----BEGIN ENCRYPTED PRIVATE KEY-----* ” で開始されますが、従来のキー形式では “ *-----BEGIN RSA PRIVATE KEY-----* ” で開始されます。
    2. Azure Key Vault を使用して秘密キーのコンテンツを保存する場合、または SFTP にリンクされたサービスをプログラムによって作成する場合、このエラーは、秘密キーのコンテンツが正しくないことを意味し、base64 でエンコードされていない可能性があります。
    3. 資格情報または秘密キーのコンテンツが無効です。

- **解決方法**: 

    - 原因 #1 の場合は、次のコマンドを実行してキーを従来のキー形式に変換し、次にそれを ADF 作成 UI で使用します。

        ```
        # Decrypt the pkcs8 key and convert the format to traditional key format
        openssl pkcs8 -in pkcs8_format_key_file -out traditional_format_key_file

        chmod 600 traditional_format_key_file

        # Re-encrypte the key file using passphrase
        ssh-keygen -f traditional_format_key_file -p
        ```

    - 原因 #2 の場合、このような文字列を作成するには、お客様は以下の 2 つの方法を使用できます。
    - サードパーティの base64 変換ツールを使用: 秘密キー コンテンツ全体を [Base64 Encode and Decode](https://www.base64encode.org/) などのツールに貼り付けて、それを base64 形式の文字列にエンコードします。次に、この文字列をキー コンテナーに貼り付けるか、またはこの値を使用して SFTP にリンクされたサービスをプログラムによって作成します。
    - C# コードを使用:

        ```c#
        byte[] keyContentBytes = File.ReadAllBytes(privateKeyPath);
        string keyContent = Convert.ToBase64String(keyContentBytes, Base64FormattingOptions.None);
        ```

    - 原因 #3 の場合は、キー ファイルまたはパスワードが正しいかどうかを他のツールを使用して再確認することで、それを使用して SFTP サーバーに適切にアクセスできるかどうかを検証します。

## <a name="next-steps"></a>次の手順

トラブルシューティングのその他のヘルプについては、次のリソースを参照してください。

*  [Data Factory ブログ](https://azure.microsoft.com/blog/tag/azure-data-factory/)
*  [Data Factory の機能のリクエスト](https://feedback.azure.com/forums/270578-data-factory)
*  [Azure のビデオ](https://azure.microsoft.com/resources/videos/index/?sort=newest&services=data-factory)
*  [MSDN フォーラム](https://social.msdn.microsoft.com/Forums/home?sort=relevancedesc&brandIgnore=True&searchTerm=data+factory)
*  [Data Factory の Stack Overflow フォーラム](https://stackoverflow.com/questions/tagged/azure-data-factory)
*  [Data Factory に関する Twitter 情報](https://twitter.com/hashtag/DataFactory)



