---
title: Azure Data Factory のコピー アクティビティのフォールト トレランス
description: 互換性のないデータをスキップすることによって Azure Data Factory のコピー アクティビティにフォールト トレランスを追加する方法について説明します。
author: dearandyxu
ms.service: data-factory
ms.topic: conceptual
ms.date: 06/22/2020
ms.author: yexu
ms.openlocfilehash: 0fe1470661c006399ea176af1112d271524b2a1f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "100390965"
---
#  <a name="fault-tolerance-of-copy-activity-in-azure-data-factory"></a>Azure Data Factory のコピー アクティビティのフォールト トレランス
> [!div class="op_single_selector" title1="使用している Data Factory サービスのバージョンを選択してください:"]
> * [Version 1](v1/data-factory-copy-activity-fault-tolerance.md)
> * [現在のバージョン](copy-activity-fault-tolerance.md)

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

ソース ストアからコピー先ストアにデータをコピーする際、データ移動中のエラーによる中断を防ぐために、Azure Data Factory のコピー アクティビティによって特定のレベルのフォールト トレランスが提供されます。 たとえば、ソース ストアからコピー先ストアに数百万の行をコピーしているとします。コピー先のデータベースには主キーが作成されていますが、ソース データベースには主キーが定義されていません。 ソースからコピー先への重複する行のコピーが発生した場合、コピー先のデータベースで PK 違反のエラーが発生します。 このとき、コピー アクティビティには、このようなエラーを処理する方法が 2 つ用意されています。 
- エラーが発生したら直ちに、コピー アクティビティを中止できます。 
- フォールト トレランスを有効にして、互換性のないデータをスキップすることで、残りの部分を引き続きコピーできます。 たとえば今回の場合であれば、重複する行をスキップします。 また、コピー アクティビティ内のセッション ログを有効にすることで、スキップされたデータをログに記録することもできます。 詳細については、「[コピー アクティビティのセッション ログ](copy-activity-log.md)」を参照してください。

## <a name="copying-binary-files"></a>バイナリ ファイルのコピー 

ADF では、バイナリ ファイルをコピーするときに、次のフォールト トレランス シナリオがサポートされます。 次のシナリオにおいて、コピー アクティビティを中止するか、残りのコピーを続行するかを選択できます。

1. ADF によってコピーされる予定のファイルが、他のアプリケーションによって同時に削除されている。
2. 特定のフォルダーまたはファイルの ACL により、ADF で構成されている接続情報よりも高いアクセス許可レベルが要求されているため、ADF がそのフォルダーまたはファイルにアクセスできない。
3. ADF でデータ整合性検証の設定を有効にしている場合に、ソース ストアとコピー先ストアの間で 1 つ以上のファイルの整合性が検証されていない。

### <a name="configuration"></a>構成 
ストレージ ストア間でバイナリ ファイルをコピーする場合、次のようにフォールト トレランスを有効にすることができます。 

```json
"typeProperties": { 
    "source": { 
        "type": "BinarySource", 
        "storeSettings": { 
            "type": "AzureDataLakeStoreReadSettings", 
            "recursive": true 
            } 
    }, 
    "sink": { 
        "type": "BinarySink", 
        "storeSettings": { 
            "type": "AzureDataLakeStoreWriteSettings" 
        } 
    }, 
    "skipErrorFile": { 
        "fileMissing": true, 
        "fileForbidden": true, 
        "dataInconsistency": true,
        "invalidFileName": true        
    }, 
    "validateDataConsistency": true, 
    "logSettings": {
        "enableCopyActivityLog": true,
        "copyActivityLogSettings": {            
            "logLevel": "Warning",
            "enableReliableLogging": false
        },
        "logLocationSettings": {
            "linkedServiceName": {
               "referenceName": "ADLSGen2",
               "type": "LinkedServiceReference"
            },
            "path": "sessionlog/"
        }
    }
} 
```
プロパティ | 説明 | 使用できる値 | 必須
-------- | ----------- | -------------- | -------- 
skipErrorFile | データの移動中にスキップするエラーの種類を指定するプロパティのグループ。 | | いいえ
fileMissing | ADF によりコピーされるのと同時に、他のアプリケーションによって削除されているファイルをスキップするかどうかを指定する、skipErrorFile プロパティ バッグ内のキーと値のペアの 1 つ。 <br/> -True: 他のアプリケーションによって削除されているファイルをスキップして、残りの部分をコピーします。 <br/> - False: データ移動中にソース ストアからいずれかのファイルが削除された場合、コピー アクティビティを中止します。 <br/>このプロパティは既定で true に設定されていることに注意してください。 | True (既定値) <br/>False | いいえ
fileForbidden | ファイルまたはフォルダーの ACL により、ADF で構成されている接続よりも高いアクセス許可レベルが要求されているときに、その特定のファイルをスキップするかどうかを指定する、skipErrorFile プロパティ バッグ内のキーと値のペアの 1 つ。 <br/> -True: そのファイルをスキップして、残りの部分をコピーします。 <br/> - False: フォルダーまたはファイルに対してアクセス許可の問題が発生した場合に、コピー アクティビティを中止します。 | True <br/>False (既定値) | いいえ
dataInconsistency | ソース ストアとコピー先ストアの間で整合性のないデータをスキップするかどうかを指定する、skipErrorFile プロパティ バッグ内のキーと値のペアの 1 つ。 <br/> -True: 整合性のないデータをスキップして、残りの部分をコピーします。 <br/> -False: 整合性のないデータが見つかった場合、コピー アクティビティを中止します。 <br/>このプロパティは、validateDataConsistency を True に設定した場合にのみ有効であることに注意してください。 | True <br/>False (既定値) | いいえ
invalidFileName | 保存先ストアに対してファイル名が無効な場合に特定のファイルをスキップするかどうかを決定する、skipErrorFile プロパティ バッグ内のキーと値のペアの 1 つ。 <br/> -True: 無効なファイル名を持つファイルをスキップして、残りの部分をコピーします。 <br/> -False: ファイルに無効なファイル名が付けられている場合、コピー アクティビティを中止します。 <br/>このプロパティは、任意のストレージ ストアからバイナリ ファイルを ADLS Gen2 にコピーする場合、またはバイナリ ファイルを AWS S3 から任意のストレージ ストアにコピーする場合に機能することに注意してください。 | True <br/>False (既定値) | いいえ
logSettings  | スキップされたオブジェクト名をログに記録するときに指定できるプロパティのグループ。 | &nbsp; | いいえ
linkedServiceName | セッション ログ ファイルを格納するための、[Azure Blob Storage](connector-azure-blob-storage.md#linked-service-properties) または [Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md#linked-service-properties) のリンクされたサービスです。 | `AzureBlobStorage` または `AzureBlobFS` 型のリンクされたサービスの名前。これは、ログ ファイルを格納するために使用するインスタンスを示します。 | いいえ
path | ログ ファイルのパス。 | ログ ファイルの格納に使用するパスを指定します。 パスを指定しないと、サービスによってコンテナーが作成されます。 | いいえ

> [!NOTE]
> バイナリ ファイルをコピーするときに、コピー アクティビティのフォールト トレランスを有効にするための前提条件は次のとおりです。
> 特定のファイルがソース ストアから削除されるときにスキップする場合:
> - ソース データセットとシンク データセットはバイナリ形式である必要があり、圧縮の種類を指定することはできません。 
> - サポートされているデータ トアの種類は、Azure Blob Sorage、Azure Data Lake Storage Gen1、Azure Data Lake Storage Gen2、Azure File Storage、ファイル ステム、FTP、SFTP、Amazon S3、Google Cloud Storage、および HDFS です。
> - ソース データセットの複数のファイル (フォルダー、ワイルドカード、またはファイルのリストを指定できます) を指定する場合にのみ、コピー アクティビティで、特定のエラー ファイルをスキップできます。 コピー先にコピーする 1 つのファイルがソース データセットに指定されている場合、エラーが発生すると、コピー アクティビティは失敗します。
>
> 特定のファイルへのアクセスがソース ストアで禁止されているときにスキップする場合:
> - ソース データセットとシンク データセットはバイナリ形式である必要があり、圧縮の種類を指定することはできません。 
> - サポートされているデータ ストアの種類は、Azure Blob Storage、Azure Data Lake Storage Gen1、Azure Data Lake Storage Gen2、Azure File Storage、SFTP、Amazon S3、HDFS です。
> - ソース データセットの複数のファイル (フォルダー、ワイルドカード、またはファイルのリストを指定できます) を指定する場合にのみ、コピー アクティビティで、特定のエラー ファイルをスキップできます。 コピー先にコピーする 1 つのファイルがソース データセットに指定されている場合、エラーが発生すると、コピー アクティビティは失敗します。
>
> 特定のファイルがソース ストアとターゲット ストアの間で矛盾していることが確認されたときにスキップする場合:
> - 詳細については、[こちら](./copy-activity-data-consistency.md)のデータ整合性ドキュメントを参照してください。

### <a name="monitoring"></a>監視 

#### <a name="output-from-copy-activity"></a>コピー アクティビティからの出力
各コピー アクティビティの実行の出力によって、読み取り、書き込み、およびスキップされたファイルの数を取得できます。 

```json
"output": {
            "dataRead": 695,
            "dataWritten": 186,
            "filesRead": 3,  
            "filesWritten": 1, 
            "filesSkipped": 2, 
            "throughput": 297,
            "logFilePath": "myfolder/a84bf8d4-233f-4216-8cb5-45962831cd1b/",
            "dataConsistencyVerification": 
           { 
                "VerificationResult": "Verified", 
                "InconsistentData": "Skipped" 
           } 
        }

```

#### <a name="session-log-from-copy-activity"></a>コピー アクティビティからのセッション ログ

スキップされたファイル名をログに記録するように構成した場合は、次のパスからログ ファイルを見つけることができます: `https://[your-blob-account].blob.core.windows.net/[path-if-configured]/copyactivity-logs/[copy-activity-name]/[copy-activity-run-id]/[auto-generated-GUID].csv`。 

ログ ファイルは csv ファイルである必要があります。 ログ ファイルのスキーマは次のとおりです。

列 | 説明 
-------- | -----------  
Timestamp | ADF がファイルをスキップしたときのタイムスタンプ。
Level | この項目のログ レベル。 ファイルのスキップを示す項目は、'Warning' レベルになります。
OperationName | 各ファイルでの ADF コピー アクティビティの実行動作。 スキップされるファイルを指定する場合は 'FileSkip' になります。
OperationItem | スキップされるファイル名。
Message | ファイルがスキップされた理由を示す詳細情報。

ログ ファイルの例を次に示します。 
```
Timestamp,Level,OperationName,OperationItem,Message 
2020-03-24 05:35:41.0209942,Warning,FileSkip,"bigfile.csv","File is skipped after read 322961408 bytes: ErrorCode=UserErrorSourceBlobNotExist,'Type=Microsoft.DataTransfer.Common.Shared.HybridDeliveryException,Message=The required Blob is missing. ContainerName: https://transferserviceonebox.blob.core.windows.net/skipfaultyfile, path: bigfile.csv.,Source=Microsoft.DataTransfer.ClientLibrary,'." 
2020-03-24 05:38:41.2595989,Warning,FileSkip,"3_nopermission.txt","File is skipped after read 0 bytes: ErrorCode=AdlsGen2OperationFailed,'Type=Microsoft.DataTransfer.Common.Shared.HybridDeliveryException,Message=ADLS Gen2 operation failed for: Operation returned an invalid status code 'Forbidden'. Account: 'adlsgen2perfsource'. FileSystem: 'skipfaultyfilesforbidden'. Path: '3_nopermission.txt'. ErrorCode: 'AuthorizationPermissionMismatch'. Message: 'This request is not authorized to perform this operation using this permission.'. RequestId: '35089f5d-101f-008c-489e-01cce4000000'..,Source=Microsoft.DataTransfer.ClientLibrary,''Type=Microsoft.DataTransfer.Common.Shared.HybridDeliveryException,Message=Operation returned an invalid status code 'Forbidden',Source=,''Type=Microsoft.Azure.Storage.Data.Models.ErrorSchemaException,Message='Type=Microsoft.Azure.Storage.Data.Models.ErrorSchemaException,Message=Operation returned an invalid status code 'Forbidden',Source=Microsoft.DataTransfer.ClientLibrary,',Source=Microsoft.DataTransfer.ClientLibrary,'." 
```
上記のログから、ADF により bigfile.csv がコピーされたときに、別のアプリケーションによってこのファイルが削除されたため、このファイルがスキップされたことがわかります。 また 3_nopermission.txt は、アクセス許可の問題のために ADF がアクセスを許可されていないため、スキップされました。


## <a name="copying-tabular-data"></a>表形式データのコピー 

### <a name="supported-scenarios"></a>サポートされるシナリオ
コピー アクティビティでは、互換性のない表形式データを検出、スキップ、およびログ記録するための 3 つのシナリオがサポートされます。

- **ソース データの型とシンクのネイティブ型の間の非互換性**。 

    次に例を示します。Blob Storage 内の CSV ファイルから、INT 型の 3 つの列を含むスキーマ定義を持つ SQL データベースにデータをコピーします。 数値データを含む CSV ファイルの行 (123,456,789 など) はシンク ストアに正常にコピーされます。 ただし、数値以外を含む行 (123,456, abc など) は互換性のない行として検出され、スキップされます。

- **ソースとシンクの間での列数の不一致**。

    次に例を示します。Blob Storage 内の CSV ファイルから、6 つの列を含むスキーマ定義を持つ SQL データベースにデータをコピーします。 6 つの列を含む CSV ファイルの行が、シンク ストアに正常にコピーされます。 含まれる列の数が 6 を超える CSV ファイルの行は、互換性のないものとして検出され、スキップされます。

- **SQL Server/Azure SQL Database/Azure Cosmos DB への書き込み時の主キー違反**。

    次に例を示します。データを SQL サーバーから SQL データベースにコピーします。 シンクの SQL データベースでは主キーが定義されていますが、ソースの SQL サーバーではそのような主キーは定義されていません。 ソースに存在する重複している行は、シンクにはコピーできません。 コピー アクティビティでは、ソース データの最初の行のみがシンクにコピーされます。 それ以降のソース行に重複している主キーの値が含まれている場合、互換性のないものとして検出され、スキップされます。

>[!NOTE]
>- PolyBase を使用して Azure Synapse Analytics にデータを読み込むには、コピー アクティビティの拒否ポリシーで "[polyBaseSettings](connector-azure-sql-data-warehouse.md#azure-sql-data-warehouse-as-sink)" を指定して PolyBase のネイティブ フォールト トレランス設定を構成します。 PolyBase 互換性のない行は、次に示すように、通常どおり Blob または ADLS へのリダイレクトを有効にできます。
>- コピー アクティビティが [Amazon Redshift Unload](connector-amazon-redshift.md#use-unload-to-copy-data-from-amazon-redshift) を呼び出すように構成されている場合、この機能は適用されません。
>- コピー アクティビティが [SQL シンクからストアド プロシージャ](./connector-azure-sql-database.md#invoke-a-stored-procedure-from-a-sql-sink)を呼び出すように構成されている場合、この機能は適用されません。

### <a name="configuration"></a>構成
次の例では、コピー アクティビティで互換性のない行をスキップするように構成する JSON 定義について説明します。

```json
"typeProperties": { 
    "source": { 
        "type": "AzureSqlSource" 
    }, 
    "sink": { 
        "type": "AzureSqlSink" 
    }, 
    "enableSkipIncompatibleRow": true, 
    "logSettings": {
        "enableCopyActivityLog": true,
        "copyActivityLogSettings": {            
            "logLevel": "Warning",
            "enableReliableLogging": false
        },
        "logLocationSettings": {
            "linkedServiceName": {
               "referenceName": "ADLSGen2",
               "type": "LinkedServiceReference"
            },
            "path": "sessionlog/"
        }
    } 
}, 
```

プロパティ | 説明 | 使用できる値 | 必須
-------- | ----------- | -------------- | -------- 
enableSkipIncompatibleRow | コピー中に互換性のない行をスキップするかどうかを指定します。 | True<br/>False (既定値) | いいえ
logSettings | 互換性のない行をログに記録するときに指定できるプロパティのグループ。 | &nbsp; | いいえ
linkedServiceName | スキップされた行を含むログを格納する [Azure Blob Storage](connector-azure-blob-storage.md#linked-service-properties) または [Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md#linked-service-properties) のリンクされたサービス。 | `AzureBlobStorage` または `AzureBlobFS` 型のリンクされたサービスの名前。これは、ログ ファイルを格納するために使用するインスタンスを示します。 | いいえ
path | スキップされた行を含むログ ファイルのパス。 | 互換性のないデータをログに記録するパスを指定します。 パスを指定しないと、サービスによってコンテナーが作成されます。 | いいえ

### <a name="monitor-skipped-rows"></a>スキップされた行を監視する
コピー アクティビティの実行が完了したら、コピー アクティビティの出力で、スキップされた行の数を次のように確認できます。

```json
"output": {
            "dataRead": 95,
            "dataWritten": 186,
            "rowsCopied": 9,
            "rowsSkipped": 2,
            "copyDuration": 16,
            "throughput": 0.01,
            "logFilePath": "myfolder/a84bf8d4-233f-4216-8cb5-45962831cd1b/",
            "errors": []
        },

```

互換性のない行をログに記録するように構成した場合は、次のパスからログ ファイルを見つけることができます: `https://[your-blob-account].blob.core.windows.net/[path-if-configured]/copyactivity-logs/[copy-activity-name]/[copy-activity-run-id]/[auto-generated-GUID].csv`。 

ログ ファイルは csv ファイルになります。 ログ ファイルのスキーマは次のとおりです。

列 | 説明 
-------- | -----------  
Timestamp | ADF が互換性のない行をスキップしたときのタイムスタンプ
Level | この項目のログ レベル。 スキップされた行がこの項目に表示される場合は、'Warning' レベルになります
OperationName | 各行での ADF コピー アクティビティの実行動作。 特定の互換性のない行がスキップされたことを記載する場合は、'TabularRowSkip' になります
OperationItem | ソース データ ストアからスキップされた行。
Message | この特定の行に互換性がない理由を示す詳細情報。


ログ ファイルの内容の例は次のとおりです。

```
Timestamp, Level, OperationName, OperationItem, Message
2020-02-26 06:22:32.2586581, Warning, TabularRowSkip, """data1"", ""data2"", ""data3""," "Column 'Prop_2' contains an invalid value 'data3'. Cannot convert 'data3' to type 'DateTime'." 
2020-02-26 06:22:33.2586351, Warning, TabularRowSkip, """data4"", ""data5"", ""data6"",", "Violation of PRIMARY KEY constraint 'PK_tblintstrdatetimewithpk'. Cannot insert duplicate key in object 'dbo.tblintstrdatetimewithpk'. The duplicate key value is (data4)." 
```

上記のサンプル ログ ファイルからは、ソース ストアからコピー先ストアへの型変換の問題により、1 行目の "data1, data2, data3" がスキップされたことがわかります。 別の行 "data4, data5, data6" は、ソース ストアからコピー先ストアへの PK 違反の問題によりスキップされました。 


## <a name="copying-tabular-data-legacy"></a>表形式データのコピー (レガシ):

次に示すのは、表形式のデータのみをコピーする際にフォールト トレランスを有効にする従来の方法です。 新しいパイプラインまたはアクティビティを作成している場合は、代わりに[こちら](#copying-tabular-data)から始めることをお勧めします。

### <a name="configuration"></a>構成
次の例では、コピー アクティビティで互換性のない行をスキップするように構成する JSON 定義について説明します。

```json
"typeProperties": {
    "source": {
        "type": "BlobSource"
    },
    "sink": {
        "type": "SqlSink",
    },
    "enableSkipIncompatibleRow": true,
    "redirectIncompatibleRowSettings": {
         "linkedServiceName": {
              "referenceName": "<Azure Storage or Data Lake Store linked service>",
              "type": "LinkedServiceReference"
            },
            "path": "redirectcontainer/erroroutput"
     }
}
```

プロパティ | 説明 | 使用できる値 | 必須
-------- | ----------- | -------------- | -------- 
enableSkipIncompatibleRow | コピー中に互換性のない行をスキップするかどうかを指定します。 | True<br/>False (既定値) | いいえ
redirectIncompatibleRowSettings | 互換性のない行をログに記録するときに指定できるプロパティのグループ。 | &nbsp; | いいえ
linkedServiceName | スキップされる行が含まれたログを格納する [Azure Storage](connector-azure-blob-storage.md#linked-service-properties) または [Azure Data Lake Store](connector-azure-data-lake-store.md#linked-service-properties) のリンク サービス。 | `AzureStorage` または `AzureDataLakeStore` 型のリンクされたサービスの名前。これは、ログ ファイルを格納するために使用するインスタンスを示します。 | いいえ
path | スキップした行を含むログ ファイルのパス。 | 互換性のないデータをログに記録するパスを指定します。 パスを指定しないと、サービスによってコンテナーが作成されます。 | いいえ

### <a name="monitor-skipped-rows"></a>スキップされた行を監視する
コピー アクティビティの実行が完了したら、コピー アクティビティの出力で、スキップされた行の数を次のように確認できます。

```json
"output": {
            "dataRead": 95,
            "dataWritten": 186,
            "rowsCopied": 9,
            "rowsSkipped": 2,
            "copyDuration": 16,
            "throughput": 0.01,
            "redirectRowPath": "https://myblobstorage.blob.core.windows.net//myfolder/a84bf8d4-233f-4216-8cb5-45962831cd1b/",
            "errors": []
        },

```
互換性のない行をログに記録するように構成した場合は、パス `https://[your-blob-account].blob.core.windows.net/[path-if-configured]/[copy-activity-run-id]/[auto-generated-GUID].csv` でログ ファイルを見つけることができます。 

ログ ファイルは、csv のファイル形式のみです。 スキップされた元のデータは、必要に応じて、列区切り記号にコンマを使用して記録されます。 非互換性の根本原因を確認できるように、ログ ファイルの元のソース データに加えて、"ErrorCode" および "ErrorMessage" という 2 つの列を追加しています。 ErrorCode および ErrorMessage は、二重引用符で囲まれます。 

ログ ファイルの内容の例は次のとおりです。

```
data1, data2, data3, "UserErrorInvalidDataValue", "Column 'Prop_2' contains an invalid value 'data3'. Cannot convert 'data3' to type 'DateTime'."
data4, data5, data6, "2627", "Violation of PRIMARY KEY constraint 'PK_tblintstrdatetimewithpk'. Cannot insert duplicate key in object 'dbo.tblintstrdatetimewithpk'. The duplicate key value is (data4)."
```

## <a name="next-steps"></a>次のステップ
コピー アクティビティの他の記事を参照してください。

- [コピー アクティビティの概要](copy-activity-overview.md)
- [コピー アクティビティのパフォーマンス](copy-activity-performance.md)