---
title: コピー アクティビティのセッション ログ
description: Azure Data Factory でコピー アクティビティのセッション ログを有効にする方法について説明します。
author: dearandyxu
ms.service: data-factory
ms.topic: conceptual
ms.date: 11/11/2020
ms.author: yexu
ms.openlocfilehash: 7cb00d62556babbd8e43e2fac2faa815a63943ed
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "100385270"
---
#  <a name="session-log-in-copy-activity"></a>コピー アクティビティのセッション ログ

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

コピー アクティビティでは、コピーされたファイル名をログに記録することができます。これにより、コピー アクティビティのセッション ログでコピーされたファイルを参照することで、データがコピー元ストアからコピー先ストアに正常にコピーされるとともに、コピー元ストアとコピー先ストアの間で一貫していることを確認できます。  

コピー アクティビティでフォールト トレランス設定を有効にして、問題のあるデータをスキップした場合は、スキップされたファイルとスキップされた行もログに記録されます。  詳細については、[コピー アクティビティのフォールト トレランス](copy-activity-fault-tolerance.md)に関するページを参照してください。 

## <a name="configuration"></a>構成
次の例では、コピー アクティビティでセッション ログを有効にする JSON 定義を示します。 

```json
"typeProperties": {
    "source": {
        "type": "BinarySource",
        "storeSettings": {
            "type": "AzureDataLakeStoreReadSettings",
            "recursive": true
        },
        "formatSettings": {
            "type": "BinaryReadSettings"
        }
    },
    "sink": {
        "type": "BinarySink",
        "storeSettings": {
            "type": "AzureBlobFSWriteSettings"
        }
    },                    
    "skipErrorFile": {
        "fileForbidden": true,
        "dataInconsistency": true
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
enableCopyActivityLog | true に設定すると、コピーされたファイル、スキップされたファイル、またはスキップされた行をログに記録できます。  | True<br/>False (既定値) | いいえ
logLevel | "Info" により、コピーされたファイル、スキップされたファイル、スキップされた行がすべてログに記録されます。 "Warning" により、スキップされたファイルおよびスキップされた行のみがログに記録されます。  | ［情報］<br/>警告 (既定値) | いいえ
enableReliableLogging | true の場合、リライアブル モードのコピー アクティビティにより、各ファイルがコピー先にコピーされると直ちにログがフラッシュされます。  コピー アクティビティでリライアブル ログ モードを有効にして大量のファイルをコピーする場合は、ファイルをコピーするたびに二重書き込み操作が必要になるため、コピー スループットが影響を受けることが予想されます。 1 つの要求は、コピー先ストアに対する要求であり、もう 1 つの要求はログ ストレージ ストアに対する要求です。  ベスト エフォート モードのコピー アクティビティでは、一定期間内のレコードのバッチを使用してログがフラッシュされます。この場合、コピー スループットは影響を受けにくくなります。 コピー アクティビティが失敗したときにログ イベントの最後のバッチがログ ファイルにフラッシュされていない可能性があるため、このモードではログ記録の完全性と適時性は保証されません。 この時点で、コピー先にコピーされたいくつかのファイルはログに記録されません。  | True<br/>False (既定値) | いいえ
logLocationSettings | セッション ログを格納する場所を指定するために使用できるプロパティのグループ。 | | いいえ
linkedServiceName | セッション ログ ファイルを格納するための、[Azure Blob Storage](connector-azure-blob-storage.md#linked-service-properties) または [Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md#linked-service-properties) のリンクされたサービスです。 | `AzureBlobStorage` または `AzureBlobFS` 型のリンクされたサービスの名前。これは、ログ ファイルを格納するために使用するインスタンスを示します。 | いいえ
path | ログ ファイルのパス。 | ログ ファイルを格納するパスを指定します。 パスを指定しないと、サービスによってコンテナーが作成されます。 | いいえ


## <a name="monitoring"></a>監視

### <a name="output-from-copy-activity"></a>コピー アクティビティからの出力
コピー アクティビティが完全に実行された後、各コピー アクティビティの実行の出力からログ ファイルのパスを確認できます。 ログ ファイルは、パス: `https://[your-blob-account].blob.core.windows.net/[logFilePath]/copyactivity-logs/[copy-activity-name]/[copy-activity-run-id]/[auto-generated-GUID].csv` から見つけることができます。  ログ ファイルは csv ファイルになります。 

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

> [!NOTE]
> `enableCopyActivityLog` プロパティが `Enabled` に設定されている場合、ログ ファイル名はシステムによって生成されます。

### <a name="the-schema-of-the-log-file"></a>ログ ファイルのスキーマ

ログ ファイルのスキーマを次に示します。

列 | 説明 
-------- | -----------  
Timestamp | ADF によってオブジェクトの読み取り、書き込み、またはスキップが行われるときのタイムスタンプ。
Level | この項目のログ レベル。 "Warning" または "Info" を指定できます。
OperationName | 各オブジェクトでの ADF コピー アクティビティの実行動作。 "FileRead"、"FileWrite"、"FileSkip"、または "TabularRowSkip" を指定できます。
OperationItem | ファイル名またはスキップされた行。
Message | ファイルがコピー元ストアから読み取られたか、またはコピー先ストアに書き込まれたかを示す詳細情報。 また、ファイルまたは行がスキップされている理由を示す場合もあります。

ログ ファイルの例を次に示します。 
```
Timestamp, Level, OperationName, OperationItem, Message
2020-10-19 08:39:13.6688152,Info,FileRead,"sample1.csv","Start to read file: {""Path"":""sample1.csv"",""ItemType"":""File"",""Size"":104857620,""LastModified"":""2020-10-19T08:22:31Z"",""ETag"":""\""0x8D874081F80C01A\"""",""ContentMD5"":""dGKVP8BVIy6AoTtKnt+aYQ=="",""ObjectName"":null}"
2020-10-19 08:39:56.3190846, Warning, FileSkip, "sample1.csv", "File is skipped after read 548000000 bytes: ErrorCode=DataConsistencySourceDataChanged,'Type=Microsoft.DataTransfer.Common.Shared.HybridDeliveryException,Message=Source file 'sample1.csv' is changed by other clients during the copy activity run.,Source=,'." 
2020-10-19 08:40:13.6688152,Info,FileRead,"sample2.csv","Start to read file: {""Path"":""sample2.csv"",""ItemType"":""File"",""Size"":104857620,""LastModified"":""2020-10-19T08:22:31Z"",""ETag"":""\""0x8D874081F80C01A\"""",""ContentMD5"":""dGKVP8BVIy6AoTtKnt+aYQ=="",""ObjectName"":null}"
2020-10-19 08:40:13.9003981,Info,FileWrite,"sample2.csv","Start to write file from source file: sample2.csv."
2020-10-19 08:45:17.6508407,Info,FileRead,"sample2.csv","Complete reading file successfully. "
2020-10-19 08:45:28.7390083,Info,FileWrite,"sample2.csv","Complete writing file from source file: sample2.csv. File is successfully copied."
```
上記のログ ファイルから、sample1.csv がスキップされたことを確認できます。これは、ソース ストアとコピー先ストアの間で、整合性があることを確認できなかったためです。 sample1.csv が不整合になった詳しい理由として、ADF のコピー アクティビティによってファイルがコピーされていたのと同時に、他のアプリケーションによってそれが変更されていたためであることがわかります。 また、sample2.csv がコピー元ストアからコピー先ストアに正常にコピーされたことも確認できます。

複数の分析エンジンを使用して、ログ ファイルをさらに分析できます。  次に、csv ログ ファイルを SQL データベースにインポートすることで、SQL クエリを使用してログ ファイルを分析する例をいくつか示します (テーブル名は SessionLogDemo とします)。  

-   コピーされたファイル リストを表示する。 
```sql
select OperationItem from SessionLogDemo where Message like '%File is successfully copied%'
```

-   特定の時間範囲内にコピーされたファイル リストを表示する。 
```sql
select OperationItem from SessionLogDemo where TIMESTAMP >= '<start time>' and TIMESTAMP <= '<end time>' and Message like '%File is successfully copied%'
```

-   コピーされた時刻とメタデータを使用して、特定のファイルを表示する。 
```sql
select * from SessionLogDemo where OperationItem='<file name>'
```

-   ある時間範囲内にメタデータがコピーされたファイルのリストを表示する。 
```sql
select * from SessionLogDemo where OperationName='FileRead' and Message like 'Start to read%' and OperationItem in (select OperationItem from SessionLogDemo where TIMESTAMP >= '<start time>' and TIMESTAMP <= '<end time>' and Message like '%File is successfully copied%')
```

-   スキップされたファイルのリストを表示する。 
```sql
select OperationItem from SessionLogDemo where OperationName='FileSkip'
```

-   特定のファイルがスキップされた理由を表示する。 
```sql
select TIMESTAMP, OperationItem, Message from SessionLogDemo where OperationName='FileSkip'
```

-   "Blob ファイルが存在しない" と同じ理由でスキップされたファイルのリストを表示する。 
```sql
select TIMESTAMP, OperationItem, Message from SessionLogDemo where OperationName='FileSkip' and Message like '%UserErrorSourceBlobNotExist%'
```

-   コピーするのに最も時間がかかるファイル名を表示する。
```sql
select top 1 OperationItem, CopyDuration=DATEDIFF(SECOND, min(TIMESTAMP), max(TIMESTAMP)) from SessionLogDemo group by OperationItem order by CopyDuration desc
```


## <a name="next-steps"></a>次のステップ
コピー アクティビティの他の記事を参照してください。

- [コピー アクティビティの概要](copy-activity-overview.md)
- [コピー アクティビティのフォールト トレランス](copy-activity-fault-tolerance.md)
- [コピー アクティビティのデータの一貫性](copy-activity-data-consistency.md)
