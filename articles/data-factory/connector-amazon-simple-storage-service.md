---
title: Amazon Simple Storage Service (S3) からデータをコピーする
description: Azure Data Factory を使用して、Amazon Simple Storage Service (S3) のデータをサポートされているシンク データ ストアにコピーする方法について説明します。
services: data-factory
ms.author: jingwang
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 06/12/2020
ms.openlocfilehash: 023d6734195dabefff12210c2e63a0a4f4f9ac93
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/23/2020
ms.locfileid: "87007675"
---
# <a name="copy-data-from-amazon-simple-storage-service-by-using-azure-data-factory"></a>Azure Data Factory を使用した Amazon Simple Storage Service からのデータのコピー
> [!div class="op_single_selector" title1="使用している Data Factory サービスのバージョンを選択します。"]
>
> * [Version 1](v1/data-factory-amazon-simple-storage-service-connector.md)
> * [現在のバージョン](connector-amazon-simple-storage-service.md)

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

この記事では、Amazon Simple Storage Service (Amazon S3) からデータをコピーする方法を概説します。 Azure Data Factory については、[入門記事で](introduction.md)をご覧ください。

>[!TIP]
>Amazon S3 から Azure Storage へのデータ移行のシナリオの詳細については、「[Azure Data Factory を使用して Amazon S3 から Azure Storage にデータを移行する](data-migration-guidance-s3-azure-storage.md)」を参照してください。

## <a name="supported-capabilities"></a>サポートされる機能

この Amazon S3 コネクタは、次のアクティビティでサポートされます。

- [サポートされるソース/シンク マトリックス](copy-activity-overview.md)での[コピー アクティビティ](copy-activity-overview.md)
- [Lookup アクティビティ](control-flow-lookup-activity.md)
- [GetMetadata アクティビティ](control-flow-get-metadata-activity.md)
- [アクティビティを削除する](delete-activity.md)

具体的には、この Amazon S3 コネクタでは、ファイルをそのままコピーするか、[サポートされているファイル形式と圧縮コーデック](supported-file-formats-and-compression-codecs.md)を使用してファイルを解析することをサポートしています。 [コピー時にファイル メタデータを保持する](#preserve-metadata-during-copy)ことも選択できます。 S3 への要求を認証するために、コネクタでは [AWS Signature Version 4](https://docs.aws.amazon.com/general/latest/gr/signature-version-4.html) が使用されます。

>[!TIP]
>この Amazon S3 コネクタを使用し、[Google Cloud Storage](connector-google-cloud-storage.md) などの*あらゆる S3 対応プロバイダー*からデータをコピーできます。 リンクされているサービスの構成で、対応するサービスの URL を指定します。

## <a name="required-permissions"></a>必要なアクセス許可

Amazon S3 からデータをコピーするには、次のアクセス許可が付与されている必要があります。

- **コピー アクティビティの実行の場合**: Amazon S3 オブジェクト操作のための `s3:GetObject` と `s3:GetObjectVersion`。
- **Data Factory GUI 作成の場合**: Amazon S3 バケット操作のための `s3:ListAllMyBuckets` と `s3:ListBucket`/`s3:GetBucketLocation`。 接続のテストやファイル パスのブラウズなどの操作にもアクセス許可が必要です。 これらのアクセス許可を付与しない場合、リンクされたサービス作成ページでテスト接続をスキップし、データセット設定でパスを直接指定してください。

Amazon S3 のアクセス許可の完全な一覧については、[ポリシーでのアクセス許可の指定](https://docs.aws.amazon.com/AmazonS3/latest/dev/using-with-s3-actions.html)に関する AWS サイトのページを参照してください。

## <a name="getting-started"></a>作業の開始

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)] 

以下のセクションで、Amazon S3 に固有の Data Factory エンティティを定義するために使用されるプロパティについて詳しく説明します。

## <a name="linked-service-properties"></a>リンクされたサービスのプロパティ

Amazon S3 のリンクされたサービスでは、次のプロパティがサポートされます。

| プロパティ | 説明 | 必須 |
|:--- |:--- |:--- |
| type | **type** プロパティは **AmazonS3** に設定する必要があります。 | はい |
| accessKeyId | シークレット アクセス キーの ID。 |はい |
| secretAccessKey | シークレット アクセス キー自体。 このフィールドを **SecureString** としてマークして Data Factory に安全に保管するか、[Azure Key Vault に格納されているシークレットを参照](store-credentials-in-key-vault.md)します。 |はい |
| serviceUrl | 公式の Amazon S3 サービス以外の S3 対応ストレージ プロバイダーからデータをコピーする場合、カスタム S3 エンドポイントを指定します。 たとえば、Google Cloud Storage からデータをコピーするには、`https://storage.googleapis.com` と指定します。 | いいえ |
| connectVia | データ ストアに接続するために使用される[統合ランタイム](concepts-integration-runtime.md)。 データ ストアがプライベート ネットワーク内にある場合、Azure Integration Runtime またはセルフホステッド統合ランタイムを使用できます。 このプロパティが指定されていない場合は、サービスでは、既定の Azure Integration Runtime が使用されます。 |いいえ |

AWS Identity and Access Management (IAM) アカウントで Amazon S3 からデータをコピーするには、このコネクタにアクセス キーが必要です。 [一時的なセキュリティ資格情報](https://docs.aws.amazon.com/IAM/latest/UserGuide/id_credentials_temp.html)は現在、サポートされていません。

>[!TIP]
>公式の Amazon S3 サービス以外の S3 対応ストレージからデータをコピーする場合、カスタム S3 サービス URL を指定します。

次に例を示します。

```json
{
    "name": "AmazonS3LinkedService",
    "properties": {
        "type": "AmazonS3",
        "typeProperties": {
            "accessKeyId": "<access key id>",
            "secretAccessKey": {
                "type": "SecureString",
                "value": "<secret access key>"
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

Amazon S3 では、形式ベースのデータセットの `location` 設定において、次のプロパティがサポートされています。

| プロパティ   | 説明                                                  | 必須 |
| ---------- | ------------------------------------------------------------ | -------- |
| type       | データセットの `location` の **type** プロパティは、**AmazonS3Location** に設定する必要があります。 | はい      |
| bucketName | S3 バケットの名前。                                          | はい      |
| folderPath | 特定のバケットの下のフォルダーへのパス。 ワイルドカードを使用してフォルダーをフィルター処理する場合は、この設定をスキップし、アクティビティのソース設定でこれを指定します。 | いいえ       |
| fileName   | 特定のバケットおよびフォルダー パスの下のファイル名。 ワイルドカードを使用してファイルをフィルター処理する場合は、この設定をスキップし、アクティビティのソース設定でこれを指定します。 | いいえ       |
| version | S3 のバージョン管理が有効になっている場合の S3 オブジェクトのバージョン。 指定しない場合は、最新バージョンがフェッチされます。 |いいえ |

**例:**

```json
{
    "name": "DelimitedTextDataset",
    "properties": {
        "type": "DelimitedText",
        "linkedServiceName": {
            "referenceName": "<Amazon S3 linked service name>",
            "type": "LinkedServiceReference"
        },
        "schema": [ < physical schema, optional, auto retrieved during authoring > ],
        "typeProperties": {
            "location": {
                "type": "AmazonS3Location",
                "bucketName": "bucketname",
                "folderPath": "folder/subfolder"
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

アクティビティの定義に利用できるセクションとプロパティの完全な一覧については、[パイプライン](concepts-pipelines-activities.md)に関する記事を参照してください。 このセクションでは、Amazon S3 ソースでサポートされているプロパティの一覧を示します。

### <a name="amazon-s3-as-a-source-type"></a>ソースの種類としての Amazon S3

[!INCLUDE [data-factory-v2-file-formats](../../includes/data-factory-v2-file-formats.md)] 

Amazon S3 では、形式ベースのコピー ソースの `storeSettings` 設定において、次のプロパティがサポートされています。

| プロパティ                 | 説明                                                  | 必須                                                    |
| ------------------------ | ------------------------------------------------------------ | ----------------------------------------------------------- |
| type                     | `storeSettings` の **type** プロパティは **AmazonS3ReadSettings** に設定する必要があります。 | はい                                                         |
| ***コピーするファイルを特定する:*** |  |  |
| オプション 1: 静的パス<br> | データセットに指定されている所定のバケットまたはフォルダー/ファイル パスからコピーします。 バケットまたはフォルダーからすべてのファイルをコピーする場合は、さらに `*` として `wildcardFileName` を指定します。 |  |
| オプション 2: S3 プレフィックス<br>- prefix | ソース S3 ファイルをフィルター処理するために、データセットで構成されている、指定されたバケットにある S3 キー名のプレフィックス。 名前が `bucket_in_dataset/this_prefix` で始まる S3 キーが選択されます。 ワイルドカード フィルターより優れたパフォーマンスを提供する S3 のサービス側フィルターを利用します。 | いいえ |
| オプション 3: ワイルドカード<br>- wildcardFolderPath | ソース フォルダーをフィルター処理するためにデータセットで構成されている、特定のバケットの下のワイルドカード文字を含むフォルダーのパス。 <br>使用できるワイルドカードは、`*` (ゼロ文字以上の文字に一致) と `?` (ゼロ文字または 1 文字に一致) です。 フォルダー名にワイルドカードまたはこのエスケープ文字が含まれている場合は、`^` を使用してエスケープします。 <br>「[フォルダーとファイル フィルターの例](#folder-and-file-filter-examples)」の他の例をご覧ください。 | いいえ                                            |
| オプション 3: ワイルドカード<br>- wildcardFileName | ソース ファイルをフィルター処理するための、特定のバケットおよびフォルダー パス (またはワイルドカード フォルダー パス) の下のワイルドカード文字を含むファイル名。 <br>使用できるワイルドカードは、`*` (ゼロ文字以上の文字に一致) と `?` (ゼロ文字または 1 文字に一致) です。 フォルダー名にワイルドカードまたはこのエスケープ文字が含まれている場合は、`^` を使用してエスケープします。  「[フォルダーとファイル フィルターの例](#folder-and-file-filter-examples)」の他の例をご覧ください。 | はい |
| オプション 4: ファイルの一覧<br>- fileListPath | 指定されたファイル セットをコピーすることを示します。 コピーするファイルの一覧を含むテキスト ファイルをポイントします。データセットで構成されているパスへの相対パスであるファイルを 1 行につき 1 つずつ指定します。<br/>このオプションを使用している場合は、データ セットにファイル名を指定しないでください。 その他の例については、[ファイル リストの例](#file-list-examples)を参照してください。 |いいえ |
| ***追加の設定:*** |  | |
| recursive | データをサブフォルダーから再帰的に読み取るか、指定したフォルダーからのみ読み取るかを指定します。 **recursive** が **true** に設定され、シンクがファイル ベースのストアである場合、空のフォルダーおよびサブフォルダーはシンクでコピーも作成もされないことに注意してください。 <br>使用可能な値: **true** (既定値) および **false**。<br>`fileListPath` を構成する場合、このプロパティは適用されません。 |いいえ |
| deleteFilesAfterCompletion | 宛先ストアに正常に移動した後、バイナリ ファイルをソース ストアから削除するかどうかを示します。 ファイルの削除はファイルごとに行われるので、コピー操作が失敗した場合、一部のファイルが既に宛先にコピーされソースからは削除されているが、他のファイルはまだソース ストアに残っていることがわかります。 <br/>このプロパティは、データ ソース ストアが Blob、ADLS Gen1、ADLS Gen2、S3、Google Cloud Storage、ファイル、Azure ファイル、SFTP、または FTP であるバイナリ コピー シナリオでのみ有効です。 既定値: false。 |いいえ |
| modifiedDatetimeStart    | ファイルは、属性 (最終変更日時) に基づいてフィルター処理されます。 <br>最終変更時刻が `modifiedDatetimeStart` から `modifiedDatetimeEnd` の間に含まれる場合は、ファイルが選択されます。 時刻は "2018-12-01T05:00:00Z" の形式で UTC タイム ゾーンに適用されます。 <br> プロパティは、ファイル属性フィルターをデータセットに適用しないことを意味する **NULL** にすることができます。  `modifiedDatetimeStart` に datetime 値が設定されており、`modifiedDatetimeEnd` が **NULL** の場合は、最終変更日時属性が datetime 値以上であるファイルが選択されます。  `modifiedDatetimeEnd` に datetime 値が設定されており、`modifiedDatetimeStart` が **NULL** の場合は、最終変更日時属性が datetime 値未満であるファイルが選択されます。<br/>`fileListPath` を構成する場合、このプロパティは適用されません。 | いいえ                                            |
| modifiedDatetimeEnd      | 上記と同じです。                                               | いいえ                                                          |
| maxConcurrentConnections | データ ストアへのコンカレント接続数。 データ ストアへのコンカレント接続を制限する場合にのみ指定します。 | いいえ                                                          |

**例:**

```json
"activities":[
    {
        "name": "CopyFromAmazonS3",
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
                    "type": "AmazonS3ReadSettings",
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

### <a name="folder-and-file-filter-examples"></a>フォルダーとファイル フィルターの例

このセクションでは、ワイルドカード フィルターを使用した結果のフォルダーのパスとファイル名の動作について説明します。

| bucket | key | recursive | ソースのフォルダー構造とフィルターの結果 (太字のファイルが取得されます)|
|:--- |:--- |:--- |:--- |
| bucket | `Folder*/*` | false | bucket<br/>&nbsp;&nbsp;&nbsp;&nbsp;FolderA<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File2.json**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;AnotherFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File6.csv |
| bucket | `Folder*/*` | true | bucket<br/>&nbsp;&nbsp;&nbsp;&nbsp;FolderA<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File2.json**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File3.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File4.json**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File5.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;AnotherFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File6.csv |
| bucket | `Folder*/*.csv` | false | bucket<br/>&nbsp;&nbsp;&nbsp;&nbsp;FolderA<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File2.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;AnotherFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File6.csv |
| bucket | `Folder*/*.csv` | true | bucket<br/>&nbsp;&nbsp;&nbsp;&nbsp;FolderA<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File2.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File3.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File5.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;AnotherFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File6.csv |

### <a name="file-list-examples"></a>ファイル リストの例

このセクションでは、コピー アクティビティのソースでファイル リスト パスを使用した結果の動作について説明します。

次のソース フォルダー構造があり、太字のファイルをコピーするとします。

| サンプルのソース構造                                      | FileListToCopy.txt のコンテンツ                             | Data Factory の構成                                            |
| ------------------------------------------------------------ | --------------------------------------------------------- | ------------------------------------------------------------ |
| bucket<br/>&nbsp;&nbsp;&nbsp;&nbsp;FolderA<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File2.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File3.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File5.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;Metadata<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;FileListToCopy.txt | File1.csv<br>Subfolder1/File3.csv<br>Subfolder1/File5.csv | **データセット内:**<br>- バケット: `bucket`<br>- フォルダー パス: `FolderA`<br><br>**コピー アクティビティ ソース内:**<br>- ファイル リストのパス: `bucket/Metadata/FileListToCopy.txt` <br><br>ファイル リストのパスは、コピーするファイルの一覧を含む同じデータ ストア内のテキスト ファイルをポイントします。データセットで構成されているパスへの相対パスで 1 行につき 1 つのファイルを指定します。 |

## <a name="preserve-metadata-during-copy"></a>コピー中にメタデータを保存する

Amazon S3 から Azure Data Lake Storage Gen2 または Azure Blob ストレージにファイルをコピーする場合、ファイルのメタデータをデータと共に保存することもできます。 詳細については、[メタデータの保存](copy-activity-preserve-metadata.md#preserve-metadata)に関する記事を参照してください。

## <a name="lookup-activity-properties"></a>Lookup アクティビティのプロパティ

プロパティの詳細については、[Lookup アクティビティ](control-flow-lookup-activity.md)に関するページを参照してください。

## <a name="getmetadata-activity-properties"></a>GetMetadata アクティビティのプロパティ

プロパティの詳細については、[GetMetadata アクティビティ](control-flow-get-metadata-activity.md)に関するページを参照してください。 

## <a name="delete-activity-properties"></a>Delete アクティビティのプロパティ

プロパティの詳細については、[Delete アクティビティ](delete-activity.md)に関するページを参照してください。

## <a name="legacy-models"></a>レガシ モデル

>[!NOTE]
>次のモデルは、下位互換性のために引き続きそのままサポートされます。 前述の新しいモデルを使用することをお勧めします。 Data Factory 作成 UI は、新しいモデルの生成に切り替えられました。

### <a name="legacy-dataset-model"></a>レガシ データセット モデル

| プロパティ | 説明 | 必須 |
|:--- |:--- |:--- |
| type | データセットの **type** プロパティは、**AmazonS3Object** を設定する必要があります。 |はい |
| bucketName | S3 バケットの名前。 ワイルドカード フィルターはサポートされていません。 |はい (Copy または Lookup アクティビティの場合)、いいえ (GetMetadata アクティビティの場合) |
| key | 指定されたバケットの下にある S3 オブジェクト キーの名前またはワイルドカード フィルターです。 **prefix** プロパティが指定されていないときにのみ適用されます。 <br/><br/>ワイルドカード フィルターは、フォルダー部分とファイル名部分の両方に対してサポートされます。 使用できるワイルドカードは、`*` (ゼロ文字以上の文字に一致) と `?` (ゼロ文字または 1 文字に一致) です。<br/>- 例 1: `"key": "rootfolder/subfolder/*.csv"`<br/>- 例 2: `"key": "rootfolder/subfolder/???20180427.txt"`<br/>「[フォルダーとファイル フィルターの例](#folder-and-file-filter-examples)」の他の例をご覧ください。 実際のフォルダーまたはファイル名にワイルドカードまたは `^` が含まれている場合は、このエスケープ文字を使用してエスケープします。 |いいえ |
| prefix | S3 オブジェクト キーのプレフィックス。 キーがこのプレフィックスで始まるオブジェクトが選択されます。 **key** プロパティが指定されていないときにのみ適用されます。 |いいえ |
| version | S3 のバージョン管理が有効になっている場合の S3 オブジェクトのバージョン。 バージョンを指定しない場合は、最新バージョンがフェッチされます。 |いいえ |
| modifiedDatetimeStart | ファイルは、属性 (最終変更日時) に基づいてフィルター処理されます。 最終変更時刻が `modifiedDatetimeStart` から `modifiedDatetimeEnd` の間に含まれる場合は、ファイルが選択されます。 時刻は "2018-12-01T05:00:00Z" の形式で UTC タイム ゾーンに適用されます。 <br/><br/> この設定を有効にすると、大量のファイルのフィルター処理を実行する場合にデータ移動の全体的なパフォーマンスに影響することに注意してください。 <br/><br/> プロパティは、ファイル属性フィルターをデータセットに適用しないことを意味する **NULL** にすることができます。  `modifiedDatetimeStart` に datetime 値が設定されており、`modifiedDatetimeEnd` が **NULL** の場合は、最終変更日時属性が datetime 値以上であるファイルが選択されます。  `modifiedDatetimeEnd` に datetime 値が設定されており、`modifiedDatetimeStart` が NULL の場合は、最終変更日時属性が datetime 値未満であるファイルが選択されます。| いいえ |
| modifiedDatetimeEnd | ファイルは、属性 (最終変更日時) に基づいてフィルター処理されます。 最終変更時刻が `modifiedDatetimeStart` から `modifiedDatetimeEnd` の間に含まれる場合は、ファイルが選択されます。 時刻は "2018-12-01T05:00:00Z" の形式で UTC タイム ゾーンに適用されます。 <br/><br/> この設定を有効にすると、大量のファイルのフィルター処理を実行する場合にデータ移動の全体的なパフォーマンスに影響することに注意してください。 <br/><br/> プロパティは、ファイル属性フィルターをデータセットに適用しないことを意味する **NULL** にすることができます。  `modifiedDatetimeStart` に datetime 値が設定されており、`modifiedDatetimeEnd` が **NULL** の場合は、最終変更日時属性が datetime 値以上であるファイルが選択されます。  `modifiedDatetimeEnd` に datetime 値が設定されており、`modifiedDatetimeStart` が **NULL** の場合は、最終変更日時属性が datetime 値未満であるファイルが選択されます。| いいえ |
| format | ファイルベースのストア間でファイルをそのままコピー (バイナリ コピー) する場合は、入力と出力の両方のデータセット定義で format セクションをスキップします。<br/><br/>特定の形式のファイルを解析または生成する場合、サポートされるファイル形式の種類は、**TextFormat**、**JsonFormat**、**AvroFormat**、**OrcFormat**、**ParquetFormat** です。 **format** の **type** プロパティをいずれかの値に設定します。 詳細については、[Text 形式](supported-file-formats-and-compression-codecs-legacy.md#text-format)、[Json 形式](supported-file-formats-and-compression-codecs-legacy.md#json-format)、[Avro 形式](supported-file-formats-and-compression-codecs-legacy.md#avro-format)、[Orc 形式](supported-file-formats-and-compression-codecs-legacy.md#orc-format)、[Parquet 形式](supported-file-formats-and-compression-codecs-legacy.md#parquet-format) の各セクションを参照してください。 |いいえ (バイナリ コピー シナリオのみ) |
| compression | データの圧縮の種類とレベルを指定します。 詳細については、[サポートされるファイル形式と圧縮コーデック](supported-file-formats-and-compression-codecs-legacy.md#compression-support)に関する記事を参照してください。<br/>サポートされる種類は、**GZip**、**Deflate**、**BZip2**、および **ZipDeflate** です。<br/>サポートされるレベルは、**Optimal** と **Fastest** です。 |いいえ |

>[!TIP]
>フォルダーの下のすべてのファイルをコピーするには、バケットに **bucketName**、フォルダー部分に **prefix** を指定します。
>
>特定の名前の単一のファイルをコピーするには、バケットに **bucketName**、フォルダー部分とファイル名に **key** を指定します。
>
>フォルダーの下のファイルのサブセットをコピーするには、バケットに **bucketName**、フォルダー部分とファイル名に **key** を指定します。

**例: prefix の使用**

```json
{
    "name": "AmazonS3Dataset",
    "properties": {
        "type": "AmazonS3Object",
        "linkedServiceName": {
            "referenceName": "<Amazon S3 linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "bucketName": "testbucket",
            "prefix": "testFolder/test",
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

**例: key と version の使用 (省略可能)**

```json
{
    "name": "AmazonS3Dataset",
    "properties": {
        "type": "AmazonS3",
        "linkedServiceName": {
            "referenceName": "<Amazon S3 linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "bucketName": "testbucket",
            "key": "testFolder/testfile.csv.gz",
            "version": "XXXXXXXXXczm0CJajYkHf0_k6LhBmkcL",
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

### <a name="legacy-source-model-for-the-copy-activity"></a>コピー アクティビティのレガシ ソース モデル

| プロパティ | 説明 | 必須 |
|:--- |:--- |:--- |
| type | コピー アクティビティのソースの **type** プロパティを **FileSystemSource** に設定する必要があります。 |はい |
| recursive | データをサブフォルダーから再帰的に読み取るか、指定したフォルダーからのみ読み取るかを指定します。 **recursive** が **true** に設定されていて、シンクがファイル ベースのストアである場合、空のフォルダーまたはサブフォルダーはシンクでコピーも作成もされないことに注意してください。<br/>使用可能な値: **true** (既定値) および **false**。 | いいえ |
| maxConcurrentConnections | 同時にデータ ストアに接続する接続の数。 データ ストアへのコンカレント接続を制限する場合にのみ指定します。 | いいえ |

**例:**

```json
"activities":[
    {
        "name": "CopyFromAmazonS3",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Amazon S3 input dataset name>",
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

## <a name="next-steps"></a>次のステップ
Azure Data Factory のコピー アクティビティによってソースおよびシンクとしてサポートされるデータ ストアの一覧については、[サポートされるデータ ストア](copy-activity-overview.md#supported-data-stores-and-formats)をご覧ください。
