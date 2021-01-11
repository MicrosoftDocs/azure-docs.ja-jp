---
title: Azure File Storage との間でデータをコピーする
description: Azure Data Factory を使用して、Azure File Storage からサポートされるシンク データ ストアにデータをコピーしたり、サポートされるソース データ ストアから Azure Data Factory にデータをコピーしたりする方法を説明します。
services: data-factory
ms.author: jingwang
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 08/21/2020
ms.openlocfilehash: 135993a39a3b06bdabfff4a219df92d41c736a51
ms.sourcegitcommit: 6fc156ceedd0fbbb2eec1e9f5e3c6d0915f65b8e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/21/2020
ms.locfileid: "88718256"
---
# <a name="copy-data-from-or-to-azure-file-storage-by-using-azure-data-factory"></a>Azure Data Factory を使用して File Storage をコピー元またはコピー先としてデータをコピーする

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

この記事では、Azure File Storage をコピー先またはコピー元としてデータをコピーする方法について説明します。 Azure Data Factory については、[入門記事で](introduction.md)をご覧ください。

## <a name="supported-capabilities"></a>サポートされる機能

この Azure File Storage コネクタは、次のアクティビティでサポートされます。

- [サポートされるソース/シンク マトリックス](copy-activity-overview.md)での[コピー アクティビティ](copy-activity-overview.md)
- [Lookup アクティビティ](control-flow-lookup-activity.md)
- [GetMetadata アクティビティ](control-flow-get-metadata-activity.md)
- [アクティビティを削除する](delete-activity.md)

Azure File Storage のデータをサポートされる任意のシンク データ ストアにコピーしたり、サポートされる任意のソース データ ストアのデータを Azure File Storage にコピーしたりできます。 コピー アクティビティでソースおよびシンクとしてサポートされているデータ ストアの一覧については、「[サポートされるデータ ストアと形式](copy-activity-overview.md#supported-data-stores-and-formats)」を参照してください。

具体的には、この Azure File Storage コネクタは、以下をサポートします。

- アカウント キーまたはサービス Shared Access Signature (SAS) の認証を使用したファイルのコピー。
- ファイルをそのままコピーするか、[サポートされているファイル形式と圧縮コーデック](supported-file-formats-and-compression-codecs.md)を使用したファイルの解析/生成。

## <a name="getting-started"></a>作業の開始

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

以下のセクションでは、Azure File Storage に固有の Data Factory エンティティの定義に使用されるプロパティの詳細を説明します。

## <a name="linked-service-properties"></a>リンクされたサービスのプロパティ

この Azure File Storage コネクタでは、次の認証の種類がサポートされています。 詳細については、対応するセクションをご覧ください。

- [アカウント キー認証](#account-key-authentication)
- [Shared Access Signature 認証](#shared-access-signature-authentication)

>[!NOTE]
> Azure File Storage のリンクされたサービスを [レガシ モデル](#legacy-model)で使用していて、ADF 作成 UI 上に "基本認証" として表示されている場合は、引き続きそのままサポートされますが、今後は新しいモデルを使用することをお勧めします。 レガシ モデルではサーバー メッセージ ブロック (SMB) を介してストレージとの間でデータを転送しますが、新しいモデルでは、スループットが向上したストレージ SDK が利用されます。 アップグレードするには、リンクされたサービスを編集して認証方法を "アカウント キー" または "SAS URI" に切り替えます。データセットとコピー アクティビティの変更は不要です。

### <a name="account-key-authentication"></a>アカウント キー認証

Data Factory では、Azure File Storage アカウント キー認証用に次のプロパティがサポートされています。

| プロパティ | 説明 | 必須 |
|:--- |:--- |:--- |
| type | type プロパティは、次のように設定する必要があります:**AzureFileStorage**。 | はい |
| connectionString | Azure File Storage に接続するために必要な情報を指定します。 <br/> アカウント キーを Azure Key Vault に格納して、接続文字列から `accountKey` 構成をプルすることもできます。 詳細については、下記の例と、「[Azure Key Vault への資格情報の格納](store-credentials-in-key-vault.md)」の記事を参照してください。 |はい |
| fileShare | ファイル共有を指定します。 | はい |
| スナップショット | スナップショットからコピーする場合は、 [ファイル共有スナップショット](../storage/files/storage-snapshots-files.md)の日付を指定します。 | いいえ |
| connectVia | データ ストアに接続するために使用される[統合ランタイム](concepts-integration-runtime.md)。 Azure 統合ランタイムまたは自己ホスト型統合ランタイム (データ ストアがプライベート ネットワークにある場合) を使用できます。 指定されていない場合は、既定の Azure 統合ランタイムが使用されます。 |いいえ |

**例:**

```json
{
    "name": "AzureFileStorageLinkedService",
    "properties": {
        "type": "AzureFileStorage",
        "typeProperties": {
            "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountName>;AccountKey=<accountKey>;EndpointSuffix=core.windows.net;",
            "fileShare": "<file share name>"
        },
        "connectVia": {
          "referenceName": "<name of Integration Runtime>",
          "type": "IntegrationRuntimeReference"
        }
    }
}
```

**例: アカウント キーを Azure Key Vault に格納する**

```json
{
    "name": "AzureFileStorageLinkedService",
    "properties": {
        "type": "AzureFileStorage",
        "typeProperties": {
            "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;",
            "fileShare": "<file share name>",
            "accountKey": { 
                "type": "AzureKeyVaultSecret", 
                "store": { 
                    "referenceName": "<Azure Key Vault linked service name>", 
                    "type": "LinkedServiceReference" 
                }, 
                "secretName": "<secretName>" 
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }            
    }
}
```

### <a name="shared-access-signature-authentication"></a>Shared Access Signature 認証

Shared Access Signature を使用すると、ストレージ アカウント内のリソースへの委任アクセスが可能になります。 Shared Access Signature を使用して、ストレージ アカウントのオブジェクトへの制限付きアクセス許可を、期間を指定してクライアントに付与できます。 Shared Access Signature について詳しくは、[Shared Access Signature のモデルの概要](../storage/common/storage-dotnet-shared-access-signature-part-1.md)に関するページをご覧ください。

Data Factory では、Shared Access Signature 認証を使用するための次のプロパティがサポートされています。

| プロパティ | 説明 | 必須 |
|:--- |:--- |:--- |
| type | type プロパティは、次のように設定する必要があります:**AzureFileStorage**。 | はい |
| sasUri | リソースへの Shared Access Signature URI を指定します。 <br/>Data Factory に安全に格納するには、このフィールドを **SecureString** として指定します。 自動ローテーションを使用してトークン部分を削除するために、SAS トークンを Azure Key Vault に配置することもできます。 詳細については、下記の例と、「[Azure Key Vault への資格情報の格納](store-credentials-in-key-vault.md)」を参照してください。 | はい |
| fileShare | ファイル共有を指定します。 | はい |
| スナップショット | スナップショットからコピーする場合は、 [ファイル共有スナップショット](../storage/files/storage-snapshots-files.md)の日付を指定します。 | いいえ |
| connectVia | データ ストアに接続するために使用される[統合ランタイム](concepts-integration-runtime.md)。 Azure 統合ランタイムまたは自己ホスト型統合ランタイム (データ ストアがプライベート ネットワークにある場合) を使用できます。 指定されていない場合は、既定の Azure 統合ランタイムが使用されます。 |いいえ |

**例:**

```json
{
    "name": "AzureFileStorageLinkedService",
    "properties": {
        "type": "AzureFileStorage",
        "typeProperties": {
            "sasUri": {
                "type": "SecureString",
                "value": "<SAS URI of the resource e.g. https://<accountname>.file.core.windows.net/?sv=<storage version>&st=<start time>&se=<expire time>&sr=<resource>&sp=<permissions>&sip=<ip range>&spr=<protocol>&sig=<signature>>"
            },
            "fileShare": "<file share name>",
            "snapshot": "<snapshot version>"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

**例: アカウント キーを Azure Key Vault に格納する**

```json
{
    "name": "AzureFileStorageLinkedService",
    "properties": {
        "type": "AzureFileStorage",
        "typeProperties": {
            "sasUri": {
                "type": "SecureString",
                "value": "<SAS URI of the Azure Storage resource without token e.g. https://<accountname>.file.core.windows.net/>"
            },
            "sasToken": { 
                "type": "AzureKeyVaultSecret", 
                "store": { 
                    "referenceName": "<Azure Key Vault linked service name>", 
                    "type": "LinkedServiceReference" 
                }, 
                "secretName": "<secretName with value of SAS token e.g. ?sv=<storage version>&st=<start time>&se=<expire time>&sr=<resource>&sp=<permissions>&sip=<ip range>&spr=<protocol>&sig=<signature>>" 
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

### <a name="legacy-model"></a>レガシ モデル

| プロパティ | 説明 | 必須 |
|:--- |:--- |:--- |
| type | type プロパティは、次のように設定する必要があります:**AzureFileStorage**。 | はい |
| host | Azure File Storage のエンドポイントを次のように指定します。 <br/>\- UI を使用する場合: `\\<storage name>.file.core.windows.net\<file service name>` を指定します<br/>- JSON を使用する場合: `"host": "\\\\<storage name>.file.core.windows.net\\<file service name>"`。 | はい |
| userid | Azure File Storage にアクセスするユーザーを次のように指定します。 <br/>\- UI を使用する場合: `AZURE\<storage name>` を指定します<br/>\- JSON を使用する場合: `"userid": "AZURE\\<storage name>"`。 | はい |
| password | ストレージ アクセス キーを指定します。 このフィールドを SecureString としてマークして Data Factory に安全に保管するか、[Azure Key Vault に格納されているシークレットを参照](store-credentials-in-key-vault.md)します。 | はい |
| connectVia | データ ストアに接続するために使用される[統合ランタイム](concepts-integration-runtime.md)。 Azure 統合ランタイムまたは自己ホスト型統合ランタイム (データ ストアがプライベート ネットワークにある場合) を使用できます。 指定されていない場合は、既定の Azure 統合ランタイムが使用されます。 |ソースの場合はいいえ、シンクの場合ははい |

**例:**

```json
{
    "name": "AzureFileStorageLinkedService",
    "properties": {
        "type": "AzureFileStorage",
        "typeProperties": {
            "host": "\\\\<storage name>.file.core.windows.net\\<file service name>",
            "userid": "AZURE\\<storage name>",
            "password": {
                "type": "SecureString",
                "value": "<storage access key>"
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

## <a name="dataset-properties"></a>データセットのプロパティ

データセットを定義するために使用できるセクションとプロパティの完全な一覧については、[データセット](concepts-datasets-linked-services.md)に関する記事をご覧ください。 

[!INCLUDE [data-factory-v2-file-formats](../../includes/data-factory-v2-file-formats.md)] 

Azure File Storage では、形式ベースのデータセットの `location` 設定において、次のプロパティがサポートされています。

| プロパティ   | 説明                                                  | 必須 |
| ---------- | ------------------------------------------------------------ | -------- |
| type       | データセットの `location` の type プロパティは、**FileServerLocation** に設定する必要があります。 | はい      |
| folderPath | フォルダーのパス。 フォルダーをフィルター処理するためにワイルドカードを使用する場合は、この設定をスキップし、アクティビティのソースの設定で指定します。 | いいえ       |
| fileName   | 特定の folderPath の下のファイル名。 ファイルをフィルター処理するためにワイルドカードを使用する場合は、この設定をスキップし、アクティビティのソースの設定で指定します。 | いいえ       |

**例:**

```json
{
    "name": "DelimitedTextDataset",
    "properties": {
        "type": "DelimitedText",
        "linkedServiceName": {
            "referenceName": "<Azure File Storage linked service name>",
            "type": "LinkedServiceReference"
        },
        "schema": [ < physical schema, optional, auto retrieved during authoring > ],
        "typeProperties": {
            "location": {
                "type": "FileServerLocation",
                "folderPath": "root/folder/subfolder"
            },
            "columnDelimiter": ",",
            "quoteChar": "\"",
            "firstRowAsHeader": true,
            "compressionCodec": "gzip"
        }
    }
}
```

## <a name="copy-activity-properties"></a>コピー アクティビティのプロパティ

アクティビティの定義に利用できるセクションとプロパティの完全な一覧については、[パイプライン](concepts-pipelines-activities.md)に関する記事を参照してください。 このセクションでは、Azure File Storage のソースとシンクでサポートされるプロパティの一覧を示します。

### <a name="azure-file-storage-as-source"></a>ソースとしての Azure File Storage

[!INCLUDE [data-factory-v2-file-formats](../../includes/data-factory-v2-file-formats.md)] 

Azure File Storage では、形式ベースのコピー ソースの `storeSettings` 設定において、次のプロパティがサポートされています。

| プロパティ                 | 説明                                                  | 必須                                      |
| ------------------------ | ------------------------------------------------------------ | --------------------------------------------- |
| type                     | `storeSettings` の type プロパティは **FileServerReadSettings** に設定する必要があります。 | はい                                           |
| ***コピーするファイルを特定する:*** |  |  |
| オプション 1: 静的パス<br> | データセットに指定されている所定のフォルダーまたはファイル パスからコピーします。 フォルダーからすべてのファイルをコピーする場合は、さらに `*` として `wildcardFileName` を指定します。 |  |
| オプション 2: ファイルのプレフィックス<br>- prefix | ソース ファイルをフィルター処理するために、データセットで構成されている、指定されたファイル共有にあるファイル名のプレフィックス。 `fileshare_in_linked_service/this_prefix` で始まる名前のファイルが選択されます。 ワイルドカード フィルターより優れたパフォーマンスを提供する、Azure File Storage 用のサービス側フィルターを利用します。 [レガシのリンクされたサービス モデル](#legacy-model)を使用する場合、この機能はサポートされません。 | いいえ                                                          |
| オプション 3: ワイルドカード<br>- wildcardFolderPath | ソース フォルダーをフィルター処理するための、ワイルドカード文字を含むフォルダー パス。 <br>使用できるワイルドカーは、`*` (ゼロ文字以上の文字に一致) と `?` (ゼロ文字または 1 文字に一致) です。実際のフォルダー名にワイルドカードまたはこのエスケープ文字が含まれている場合は、`^` を使用してエスケープします。 <br>「[フォルダーとファイル フィルターの例](#folder-and-file-filter-examples)」の他の例をご覧ください。 | いいえ                                            |
| オプション 3: ワイルドカード<br>- wildcardFileName | ソース ファイルをフィルター処理するための、特定の folderPath/wildcardFolderPath の下のワイルドカード文字を含むファイル名。 <br>使用できるワイルドカーは、`*` (ゼロ文字以上の文字に一致) と `?` (ゼロ文字または 1 文字に一致) です。実際のフォルダー名にワイルドカードまたはこのエスケープ文字が含まれている場合は、`^` を使用してエスケープします。  「[フォルダーとファイル フィルターの例](#folder-and-file-filter-examples)」の他の例をご覧ください。 | はい |
| オプション 4: ファイルの一覧<br>- fileListPath | 指定されたファイル セットをコピーすることを示します。 コピーするファイルの一覧を含むテキスト ファイルをポイントします。データセットで構成されているパスへの相対パスであるファイルを 1 行につき 1 つずつ指定します。<br/>このオプションを使用する場合は、データ セットにファイル名を指定しないでください。 その他の例については、[ファイル リストの例](#file-list-examples)を参照してください。 |いいえ |
| ***追加の設定:*** |  | |
| recursive | データをサブフォルダーから再帰的に読み取るか、指定したフォルダーからのみ読み取るかを指定します。 recursive が true に設定され、シンクがファイル ベースのストアである場合、空のフォルダーおよびサブフォルダーはシンクでコピーも作成もされないことに注意してください。 <br>使用可能な値: **true** (既定値) および **false**。<br>`fileListPath` を構成する場合、このプロパティは適用されません。 |いいえ |
| deleteFilesAfterCompletion | 宛先ストアに正常に移動した後、バイナリ ファイルをソース ストアから削除するかどうかを示します。 ファイルの削除はファイルごとに行われるので、コピー操作が失敗した場合、一部のファイルが既に宛先にコピーされソースからは削除されているが、他のファイルはまだソース ストアに残っていることがわかります。 <br/>このプロパティは、データ ソース ストアが Blob、ADLS Gen1、ADLS Gen2、S3、Google Cloud Storage、ファイル、Azure ファイル、SFTP、または FTP であるバイナリ コピー シナリオでのみ有効です。 既定値: false。 |いいえ |
| modifiedDatetimeStart    | ファイルはフィルター処理され、元になる属性は最終更新時刻です。 <br>最終変更時刻が `modifiedDatetimeStart` から `modifiedDatetimeEnd` の間に含まれる場合は、ファイルが選択されます。 時刻は "2018-12-01T05:00:00Z" の形式で UTC タイム ゾーンに適用されます。 <br> プロパティは、ファイル属性フィルターをデータセットに適用しないことを意味する NULL にすることができます。  `modifiedDatetimeStart` に datetime 値を設定し、`modifiedDatetimeEnd` を NULL にした場合は、最終更新時刻属性が datetime 値以上であるファイルが選択されることを意味します。  `modifiedDatetimeEnd` に datetime 値を設定し、`modifiedDatetimeStart` を NULL にした場合は、最終更新時刻属性が datetime 値以下であるファイルが選択されることを意味します。<br/>`fileListPath` を構成する場合、このプロパティは適用されません。 | いいえ                                            |
| modifiedDatetimeEnd      | 上記と同じです。                                               | いいえ                                            |
| maxConcurrentConnections | 同時にストレージ ストアに接続する接続の数。 データ ストアへのコンカレント接続を制限する場合にのみ指定します。 | いいえ                                            |

**例:**

```json
"activities":[
    {
        "name": "CopyFromAzureFileStorage",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Delimited text input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "DelimitedTextSource",
                "formatSettings":{
                    "type": "DelimitedTextReadSettings",
                    "skipLineCount": 10
                },
                "storeSettings":{
                    "type": "FileServerReadSettings",
                    "recursive": true,
                    "wildcardFolderPath": "myfolder*A",
                    "wildcardFileName": "*.csv"
                }
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

### <a name="azure-file-storage-as-sink"></a>シンクとしての Azure File Storage

[!INCLUDE [data-factory-v2-file-sink-formats](../../includes/data-factory-v2-file-sink-formats.md)]

Azure File Storage では、形式ベースのコピー シンクの `storeSettings` 設定において、次のプロパティがサポートされています。

| プロパティ                 | 説明                                                  | 必須 |
| ------------------------ | ------------------------------------------------------------ | -------- |
| type                     | `storeSettings` の type プロパティは **FileServerWriteSettings** に設定する必要があります。 | はい      |
| copyBehavior             | ソースがファイル ベースのデータ ストアのファイルの場合は、コピー動作を定義します。<br/><br/>使用できる値は、以下のとおりです。<br/><b>- PreserveHierarchy (既定値)</b>:ターゲット フォルダー内でファイル階層を保持します。 ソース フォルダーに対するソース ファイルの相対パスと、ターゲット フォルダーに対するターゲット ファイルの相対パスが一致します。<br/><b>- FlattenHierarchy</b>:ソース フォルダーのすべてのファイルをターゲット フォルダーの第一レベルに配置します。 ターゲット ファイルは、自動生成された名前になります。 <br/><b>- MergeFiles</b>:ソース フォルダーのすべてのファイルを 1 つのファイルにマージします。 ファイル名を指定した場合、マージされたファイル名は指定した名前になります。 それ以外は自動生成されたファイル名になります。 | いいえ       |
| maxConcurrentConnections | 同時にデータ ストアに接続する接続の数。 データ ストアへのコンカレント接続を制限する場合にのみ指定します。 | いいえ       |

**例:**

```json
"activities":[
    {
        "name": "CopyToAzureFileStorage",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<Parquet output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "<source type>"
            },
            "sink": {
                "type": "ParquetSink",
                "storeSettings":{
                    "type": "FileServerWriteSettings",
                    "copyBehavior": "PreserveHierarchy"
                }
            }
        }
    }
]
```

### <a name="folder-and-file-filter-examples"></a>フォルダーとファイル フィルターの例

このセクションでは、ワイルドカード フィルターを使用した結果のフォルダーのパスとファイル名の動作について説明します。

| folderPath | fileName | recursive | ソースのフォルダー構造とフィルターの結果 (**太字**のファイルが取得されます)|
|:--- |:--- |:--- |:--- |
| `Folder*` | (空、既定値を使用) | false | FolderA<br/>&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;**File2.json**<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5.csv<br/>AnotherFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;File6.csv |
| `Folder*` | (空、既定値を使用) | true | FolderA<br/>&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;**File2.json**<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File3.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File4.json**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File5.csv**<br/>AnotherFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;File6.csv |
| `Folder*` | `*.csv` | false | FolderA<br/>&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5.csv<br/>AnotherFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;File6.csv |
| `Folder*` | `*.csv` | true | FolderA<br/>&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File3.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File5.csv**<br/>AnotherFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;File6.csv |

### <a name="file-list-examples"></a>ファイル リストの例

このセクションでは、コピー アクティビティのソースでファイル リスト パスを使用した結果の動作について説明します。

次のソース フォルダー構造があり、太字のファイルをコピーするとします。

| サンプルのソース構造                                      | FileListToCopy.txt のコンテンツ                             | ADF の構成                                            |
| ------------------------------------------------------------ | --------------------------------------------------------- | ------------------------------------------------------------ |
| root<br/>&nbsp;&nbsp;&nbsp;&nbsp;FolderA<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File2.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File3.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File5.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;Metadata<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;FileListToCopy.txt | File1.csv<br>Subfolder1/File3.csv<br>Subfolder1/File5.csv | **データセット内:**<br>- フォルダー パス: `root/FolderA`<br><br>**コピー アクティビティ ソース内:**<br>- ファイル リストのパス: `root/Metadata/FileListToCopy.txt` <br><br>ファイル リストのパスは、コピーするファイルの一覧を含む同じデータ ストア内のテキスト ファイルをポイントします。データセットで構成されているパスへの相対パスで 1 行につき 1 つのファイルを指定します。 |

### <a name="recursive-and-copybehavior-examples"></a>recursive と copyBehavior の例

このセクションでは、recursive 値と copyBhavior 値の組み合わせごとに、Copy 操作で行われる動作について説明します。

| recursive | copyBehavior | ソースのフォルダー構造 | ターゲットの結果 |
|:--- |:--- |:--- |:--- |
| true |preserveHierarchy | Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | ターゲット フォルダー Folder1 は、ソースと同じ構造で作成されます。<br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 |
| true |flattenHierarchy | Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | ターゲットの Folder1 は、次の構造で作成されます。 <br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1 の自動生成された名前<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2 の自動生成された名前<br/>&nbsp;&nbsp;&nbsp;&nbsp;File3 の自動生成された名前<br/>&nbsp;&nbsp;&nbsp;&nbsp;File4 の自動生成された名前<br/>&nbsp;&nbsp;&nbsp;&nbsp;File5 の自動生成された名前 |
| true |mergeFiles | Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | ターゲットの Folder1 は、次の構造で作成されます。 <br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1、File2、File3、File4、File5 の内容は、自動生成されたファイル名を持つ 1 つのファイルにマージされます |
| false |preserveHierarchy | Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | ターゲット フォルダー Folder1 は、次の構造で作成されます。<br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/><br/>Subfolder1 と File3、File4、File5 は取得されません。 |
| false |flattenHierarchy | Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | ターゲット フォルダー Folder1 は、次の構造で作成されます。<br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1 の自動生成された名前<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2 の自動生成された名前<br/><br/>Subfolder1 と File3、File4、File5 は取得されません。 |
| false |mergeFiles | Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | ターゲット フォルダー Folder1 は、次の構造で作成されます。<br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1、File2 の内容は 1 つのファイルにマージされ、自動生成されたファイル名が付けられます。 File1 の自動生成された名前<br/><br/>Subfolder1 と File3、File4、File5 は取得されません。 |

## <a name="lookup-activity-properties"></a>Lookup アクティビティのプロパティ

プロパティの詳細については、[Lookup アクティビティ](control-flow-lookup-activity.md)に関するページを参照してください。

## <a name="getmetadata-activity-properties"></a>GetMetadata アクティビティのプロパティ

プロパティの詳細については、[GetMetadata アクティビティ](control-flow-get-metadata-activity.md)に関するページを参照してください。 

## <a name="delete-activity-properties"></a>Delete アクティビティのプロパティ

プロパティの詳細については、[Delete アクティビティ](delete-activity.md)に関するページを参照してください。

## <a name="legacy-models"></a>レガシ モデル

>[!NOTE]
>次のモデルは、下位互換性のために引き続きそのままサポートされます。 今後は、上のセクションで説明した新しいモデルを使用することをお勧めします。ADF オーサリング UI は、新しいモデルを生成するように切り替えられています。

### <a name="legacy-dataset-model"></a>レガシ データセット モデル

| プロパティ | 説明 | 必須 |
|:--- |:--- |:--- |
| type | データセットの type プロパティは、次のように設定する必要があります:**FileShare** |はい |
| folderPath | フォルダーへのパス。 <br/><br/>ワイルドカード フィルターがサポートされています。使用できるワイルドカードは、`*` (ゼロ文字以上の文字に一致) と `?` (ゼロ文字または 1 文字に一致) です。実際のフォルダー名にワイルドカードまたはこのエスケープ文字が含まれている場合は、`^` を使用してエスケープします。 <br/><br/>例: ルートフォルダー/サブフォルダー。「[フォルダーとファイル フィルターの例](#folder-and-file-filter-examples)」の例を参照してください。 |はい |
| fileName | 指定された "folderPath" の下にあるファイルの**名前またはワイルドカード フィルター**。 このプロパティの値を指定しない場合、データセットはフォルダー内のすべてのファイルをポイントします。 <br/><br/>フィルターに使用できるワイルドカードは、`*` (ゼロ文字以上の文字に一致) と `?` (ゼロ文字または 1 文字に一致) です。<br/>- 例 1: `"fileName": "*.csv"`<br/>- 例 2: `"fileName": "???20180427.txt"`<br/>実際のファイル名にワイルドカードまたはこのエスケープ文字が含まれている場合は、`^` を使用してエスケープします。<br/><br/>出力データセットに fileName の指定がなく、アクティビティ シンクに **preserveHierarchy** の指定がない場合、コピー アクティビティは、"Data.0a405f8a-93ff-4c6f-b3be-f69616f1df7a.txt.gz" のように "*Data.[アクティビティ実行 ID GUID].[FlattenHierarchy の場合は GUID].[構成されている場合は形式].[構成されている場合は圧縮]* " のパターンでファイル名を自動生成します。クエリの代わりにテーブル名を使用して表形式のソースからコピーする場合、名前のパターンは "MyTable.csv" のように " *[テーブル名].[形式].[構成されている場合は圧縮]* " になります。 |いいえ |
| modifiedDatetimeStart | ファイルはフィルター処理され、元になる属性は最終更新時刻です。 最終変更時刻が `modifiedDatetimeStart` から `modifiedDatetimeEnd` の間に含まれる場合は、ファイルが選択されます。 時刻は "2018-12-01T05:00:00Z" の形式で UTC タイム ゾーンに適用されます。 <br/><br/> 多数のファイルにファイル フィルターを実行する場合は、この設定を有効にすることで、データ移動の全体的なパフォーマンスが影響を受けることに注意してください。 <br/><br/> プロパティは、ファイル属性フィルターをデータセットに適用しないことを意味する NULL にすることができます。  `modifiedDatetimeStart` に datetime 値を設定し、`modifiedDatetimeEnd` を NULL にした場合は、最終更新時刻属性が datetime 値以上であるファイルが選択されることを意味します。  `modifiedDatetimeEnd` に datetime 値を設定し、`modifiedDatetimeStart` を NULL にした場合は、最終更新時刻属性が datetime 値以下であるファイルが選択されることを意味します。| いいえ |
| modifiedDatetimeEnd | ファイルはフィルター処理され、元になる属性は最終更新時刻です。 最終変更時刻が `modifiedDatetimeStart` から `modifiedDatetimeEnd` の間に含まれる場合は、ファイルが選択されます。 時刻は "2018-12-01T05:00:00Z" の形式で UTC タイム ゾーンに適用されます。 <br/><br/> 多数のファイルにファイル フィルターを実行する場合は、この設定を有効にすることで、データ移動の全体的なパフォーマンスが影響を受けることに注意してください。 <br/><br/> プロパティは、ファイル属性フィルターをデータセットに適用しないことを意味する NULL にすることができます。  `modifiedDatetimeStart` に datetime 値を設定し、`modifiedDatetimeEnd` を NULL にした場合は、最終更新時刻属性が datetime 値以上であるファイルが選択されることを意味します。  `modifiedDatetimeEnd` に datetime 値を設定し、`modifiedDatetimeStart` を NULL にした場合は、最終更新時刻属性が datetime 値以下であるファイルが選択されることを意味します。| いいえ |
| format | ファイルベースのストア間で**ファイルをそのままコピー** (バイナリ コピー) する場合は、入力と出力の両方のデータセット定義で format セクションをスキップします。<br/><br/>特定の形式のファイルを解析または生成する場合、サポートされるファイル形式の種類は、**TextFormat**、**JsonFormat**、**AvroFormat**、**OrcFormat**、**ParquetFormat** です。 形式の **type** プロパティをいずれかの値に設定します。 詳細については、[Text Format](supported-file-formats-and-compression-codecs-legacy.md#text-format)、[Json Format](supported-file-formats-and-compression-codecs-legacy.md#json-format)、[Avro Format](supported-file-formats-and-compression-codecs-legacy.md#avro-format)、[Orc Format](supported-file-formats-and-compression-codecs-legacy.md#orc-format)、[Parquet Format](supported-file-formats-and-compression-codecs-legacy.md#parquet-format) の各セクションを参照してください。 |いいえ (バイナリ コピー シナリオのみ) |
| compression | データの圧縮の種類とレベルを指定します。 詳細については、[サポートされるファイル形式と圧縮コーデック](supported-file-formats-and-compression-codecs-legacy.md#compression-support)に関する記事を参照してください。<br/>サポートされる種類は、**GZip**、**Deflate**、**BZip2**、**ZipDeflate** です。<br/>サポートされるレベルは、**Optimal** と **Fastest** です。 |いいえ |

>[!TIP]
>フォルダーの下のすべてのファイルをコピーするには、**folderPath** のみを指定します。<br>特定の名前の単一のファイルをコピーするには、フォルダー部分で **folderPath**、ファイル名で **fileName** を指定します。<br>フォルダーの下のファイルのサブセットをコピーするには、フォルダー部分で **folderPath**、ワイルドカード フィルターで **fileName** を指定します。

>[!NOTE]
>ファイル フィルターで "fileFilter" プロパティを使用していた場合は、そのまま引き続きサポートされますが、今後は "fileName" に追加された新しいフィルター機能を使用することをお勧めします。

**例:**

```json
{
    "name": "AzureFileStorageDataset",
    "properties": {
        "type": "FileShare",
        "linkedServiceName":{
            "referenceName": "<Azure File Storage linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "folderPath": "folder/subfolder/",
            "fileName": "*",
            "modifiedDatetimeStart": "2018-12-01T05:00:00Z",
            "modifiedDatetimeEnd": "2018-12-01T06:00:00Z",
            "format": {
                "type": "TextFormat",
                "columnDelimiter": ",",
                "rowDelimiter": "\n"
            },
            "compression": {
                "type": "GZip",
                "level": "Optimal"
            }
        }
    }
}
```

### <a name="legacy-copy-activity-source-model"></a>レガシ コピー アクティビティ ソース モデル

| プロパティ | 説明 | 必須 |
|:--- |:--- |:--- |
| type | コピー アクティビティのソースの type プロパティは、次のように設定する必要があります:**FileSystemSource** |はい |
| recursive | データをサブ フォルダーから再帰的に読み取るか、指定したフォルダーからのみ読み取るかを指定します。 recursive が true に設定され、シンクがファイル ベースのストアである場合、空のフォルダー/サブフォルダーはシンクでコピー/作成されないことに注意してください。<br/>使用可能な値: **true** (既定値)、**false** | いいえ |
| maxConcurrentConnections | 同時にストレージ ストアに接続する接続の数。 データ ストアへのコンカレント接続を制限する場合にのみ指定します。 | いいえ |

**例:**

```json
"activities":[
    {
        "name": "CopyFromAzureFileStorage",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Azure File Storage input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "FileSystemSource",
                "recursive": true
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

### <a name="legacy-copy-activity-sink-model"></a>レガシ コピー アクティビティ シンク モデル

| プロパティ | 説明 | 必須 |
|:--- |:--- |:--- |
| type | コピー アクティビティのシンクの type プロパティは、次のように設定する必要があります: **FileSystemSink** |はい |
| copyBehavior | ソースがファイル ベースのデータ ストアのファイルの場合は、コピー動作を定義します。<br/><br/>使用できる値は、以下のとおりです。<br/><b>PreserveHierarchy (既定値)</b>: ファイル階層をターゲット フォルダー内で保持します。 ソース フォルダーに対するソース ファイルの相対パスと、ターゲット フォルダーに対するターゲット ファイルの相対パスが一致します。<br/><b>FlattenHierarchy</b>: ソース フォルダーのすべてのファイルがターゲット フォルダーの第一レベルに配置されます。 ターゲット ファイルは、自動生成された名前になります。 <br/><b>MergeFiles</b>: ソース フォルダーのすべてのファイルを 1 つのファイルにマージします。 ファイル名を指定した場合、マージされたファイル名は指定した名前になります。それ以外の場合は、自動生成されたファイル名になります。 | いいえ |
| maxConcurrentConnections | 同時にストレージ ストアに接続する接続の数。 データ ストアへのコンカレント接続を制限する場合にのみ指定します。 | いいえ |

**例:**

```json
"activities":[
    {
        "name": "CopyToAzureFileStorage",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<Azure File Storage output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "<source type>"
            },
            "sink": {
                "type": "FileSystemSink",
                "copyBehavior": "PreserveHierarchy"
            }
        }
    }
]
```

## <a name="next-steps"></a>次のステップ
Azure Data Factory のコピー アクティビティによってソースおよびシンクとしてサポートされるデータ ストアの一覧については、[サポートされるデータ ストア](copy-activity-overview.md#supported-data-stores-and-formats)の表をご覧ください。
