---
title: コピー アクティビティでのデータ整合性の検証
description: Azure Data Factory のコピー アクティビティでデータ整合性の検証を有効にする方法について説明します。
author: dearandyxu
ms.service: data-factory
ms.topic: conceptual
ms.date: 3/27/2020
ms.author: yexu
ms.openlocfilehash: b71657f67c1b9c623d6d48f33b986ac43533cca6
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "100373018"
---
#  <a name="data-consistency-verification-in-copy-activity"></a>コピー アクティビティでのデータ整合性の検証

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

ソース ストアからコピー先ストアにデータを移動するとき、Azure Data Factory コピー アクティビティでは、データがソース ストアからコピー先ストアに正常にコピーされただけでなく、ソース ストアとコピー先ストアの間の整合性も確保されていることを確認するための、追加のデータ整合性検証を行うことができます。 データの移動中に整合性のないファイルが検出されたら、コピー アクティビティを中止するか、またはフォールト トレランス設定を有効にして整合性のないファイルをスキップすることで、その他のデータをコピーし続けることができます。 スキップされたファイル名を取得するには、コピー アクティビティでセッション ログ設定を有効にします。 詳細については、「[コピー アクティビティのセッション ログ](copy-activity-log.md)」を参照してください。

## <a name="supported-data-stores-and-scenarios"></a>サポートされているデータ ストアおよびシナリオ

-   データ整合性の検証は、FTP、sFTP、および HTTP を除くすべてのコネクタでサポートされています。 
-   ステージング コピー シナリオでは、データ整合性の検証はサポートされていません。
-   バイナリ ファイルをコピーする場合は、コピー アクティビティで "PreserveHierarchy" 動作が設定されている場合にのみ、データ整合性の検証を使用できます。
-   データ整合性の検証を有効にし、単一のコピー アクティビティで複数のバイナリ ファイルをコピーする場合は、コピー アクティビティを中止するか、またはフォールト トレランス設定を有効にして整合性のないファイルをスキップすることで、残りのファイルをコピーし続けることができます。 
-   データ整合性の検証を有効にし、単一のコピー アクティビティでテーブルをコピーする場合、コピー元から読み取られた行の数が、コピー先にコピーされた行の数とスキップされた互換性のない行の数の合計と異なると、コピー アクティビティは失敗します。


## <a name="configuration"></a>構成
次の例では、コピー アクティビティでデータ整合性の検証を有効にする JSON 定義を示します。 

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
    "validateDataConsistency": true, 
    "skipErrorFile": { 
        "dataInconsistency": true 
    }, 
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
validateDataConsistency | バイナリ ファイルのコピー時にこのプロパティに true を設定した場合、ソース ストアとコピー先ストアの間でのデータ整合性を確保するために、ソース ストアからコピー先ストアにコピーされた各バイナリ ファイルのファイル サイズ、lastModifiedDate、および MD5 チェックサムがコピー アクティビティにより確認されます。 表形式データをコピーする場合、コピー元から読み取られた行の合計数が、コピー先にコピーされた行の数とスキップされた互換性のない行の数の合計と同じになるように、ジョブの完了後にコピー アクティビティにより合計行数が確認されます。 このオプションを有効にすると、コピーのパフォーマンスが影響を受けることに注意してください。  | True<br/>False (既定値) | いいえ
dataInconsistency | 整合性のないファイルをスキップするかどうかを指定する、skipErrorFile プロパティ バッグ内のキーと値のペアの 1 つです。 <br/> -True: 整合性のないファイルをスキップして、残りの部分をコピーします。<br/> - False: 整合性のないファイルが見つかった場合、コピー アクティビティを中止します。<br/>このプロパティは、バイナリ ファイルをコピーしていて validateDataConsistency を True に設定した場合にのみ有効であることに注意してください。  | True<br/>False (既定値) | いいえ
logSettings | スキップされたファイルをセッション ログでログに記録できるようにするために指定できるプロパティのグループです。 | | いいえ
linkedServiceName | セッション ログ ファイルを格納するための、[Azure Blob Storage](connector-azure-blob-storage.md#linked-service-properties) または [Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md#linked-service-properties) のリンクされたサービスです。 | `AzureBlobStorage` または `AzureBlobFS` 型のリンクされたサービスの名前。これは、ログ ファイルを格納するために使用するインスタンスを示します。 | いいえ
path | ログ ファイルのパス。 | ログ ファイルを格納するパスを指定します。 パスを指定しないと、サービスによってコンテナーが作成されます。 | いいえ

>[!NOTE]
>- Azure Blob または Azure Data Lake Storage Gen2 との間でバイナリ ファイルをコピーするとき、ADF では、[Azure Blob API](/dotnet/api/microsoft.azure.storage.blob.blobrequestoptions?view=azure-dotnet-legacy&preserve-view=true) と [Azure Data Lake Storage Gen2 API](/rest/api/storageservices/datalakestoragegen2/path/update#request-headers) を活用し、ブロック レベル MD5 チェックサム検証が行われます。 ファイル上の ContentMD5 がデータ ソースとして Azure Blob または Azure Data Lake Storage Gen2 に存在する場合、ADF では、ファイルも読み取った後に、レベル MD5 チェックサム検証がファイリングされます。 データのコピー先として Azure Blob または Azure Data Lake Storage Gen2 にファイルをコピーした後、ADF では、Azure Blob または Azure Data Lake Storage Gen2 に ContentMD5 が書き込まれ、データの一貫性検証のために下流のアプリケーションでさらに利用できます。
>- ADF では、ストレージ ストア間でバイナリ ファイルをコピーするとき、ファイル サイズ検証が行われます。

## <a name="monitoring"></a>監視

### <a name="output-from-copy-activity"></a>コピー アクティビティからの出力
コピー アクティビティが完全に実行された後、各コピー アクティビティの実行の出力から、データ整合性検証の結果を確認できます。

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
データ整合性の検証の詳細は、"dataConsistencyVerification プロパティ" から確認できます。

**VerificationResult** の値: 
-   **Verified**:コピーしたデータは、ソース ストアとコピー先ストアとの間で整合性があることが確認されています。 
-   **NotVerified**:コピー アクティビティで validateDataConsistency が有効になっていないため、コピーしたデータの整合性が検証されていません。 
-   **Unsupported**:この特定のコピー ペアでは、データの整合性の確認がサポートされていないため、コピーしたデータの整合性が検証されていません。 

**InconsistentData** の値: 
-   **Found**:ADF のコピー アクティビティで、整合性のないデータが検出されました。 
-   **Skipped**:ADF のコピー アクティビティで、整合性のないデータが検出され、スキップされました。 
-   **None**:ADF のコピー アクティビティで、整合性のないデータは検出されませんでした。 ソース ストアとコピー先ストアの間でデータの整合性があることが確認されたか、またはコピー アクティビティで validateDataConsistency を無効にしたためである可能性があります。 

### <a name="session-log-from-copy-activity"></a>コピー アクティビティからのセッション ログ

整合性のないファイルをログに記録するように構成した場合は、`https://[your-blob-account].blob.core.windows.net/[path-if-configured]/copyactivity-logs/[copy-activity-name]/[copy-activity-run-id]/[auto-generated-GUID].csv` のパスからログ ファイルを見つけることができます。  ログ ファイルは csv ファイルになります。 

ログ ファイルのスキーマは次のとおりです。

列 | 説明 
-------- | -----------  
Timestamp | ADF が整合性のないファイルをスキップしたときのタイムスタンプ。
Level | この項目のログ レベル。 ファイルのスキップを示す項目は、'Warning' レベルになります。
OperationName | 各ファイルでの ADF コピー アクティビティの実行動作。 スキップされるファイルを指定する場合は 'FileSkip' になります。
OperationItem | スキップされるファイル名。
Message | ファイルがスキップされた理由を示す詳細情報。

ログ ファイルの例を次に示します。 
```
Timestamp, Level, OperationName, OperationItem, Message
2020-02-26 06:22:56.3190846, Warning, FileSkip, "sample1.csv", "File is skipped after read 548000000 bytes: ErrorCode=DataConsistencySourceDataChanged,'Type=Microsoft.DataTransfer.Common.Shared.HybridDeliveryException,Message=Source file 'sample1.csv' is changed by other clients during the copy activity run.,Source=,'." 
```
上記のログ ファイルから、sample1.csv がスキップされたことを確認できます。これは、ソース ストアとコピー先ストアの間で、整合性があることを確認できなかったためです。 sample1.csv が不整合になった詳しい理由として、ADF のコピー アクティビティによってファイルがコピーされていたのと同時に、他のアプリケーションによってそれが変更されていたためであることがわかります。 



## <a name="next-steps"></a>次のステップ
コピー アクティビティの他の記事を参照してください。

- [コピー アクティビティの概要](copy-activity-overview.md)
- [コピー アクティビティのフォールト トレランス](copy-activity-fault-tolerance.md)