---
title: Azure Data Factory のメタデータの取得アクティビティ
description: Data Factory パイプライン内でメタデータの取得アクティビティを使用する方法を説明します。
author: linda33wj
ms.service: data-factory
ms.topic: conceptual
ms.date: 02/25/2021
ms.author: jingwang
ms.openlocfilehash: bd8fc3383d6d9a0afb7733cb94643623e6879d23
ms.sourcegitcommit: 24a12d4692c4a4c97f6e31a5fbda971695c4cd68
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/05/2021
ms.locfileid: "102178543"
---
# <a name="get-metadata-activity-in-azure-data-factory"></a>Azure Data Factory のメタデータの取得アクティビティ

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

メタデータの取得アクティビティを使用すると、Azure Data Factory で任意のデータのメタデータを取得できます。 条件式でメタデータの取得アクティビティからの出力を使用して検証を実行することや、後続のアクティビティでメタデータを使用することができます。

## <a name="supported-capabilities"></a>サポートされる機能

メタデータの取得アクティビティは、データセットを入力として受け取り、メタデータ情報を出力として返します。 現在、次のコネクタとそれに対応する取得可能なメタデータがサポートされています。 返されるメタデータの最大サイズは **4 MB** です。

### <a name="supported-connectors"></a>サポートされているコネクタ

**File Storage**

| コネクタ/メタデータ | itemName<br>(ファイル/フォルダー) | itemType<br>(ファイル/フォルダー) | size<br>(ファイル) | created<br>(ファイル/フォルダー) | lastModified<sup>1</sup><br>(ファイル/フォルダー) |childItems<br>(フォルダー) |contentMD5<br>(ファイル) | structure<sup>2</sup><br/>(ファイル) | columnCount<sup>2</sup><br>(ファイル) | exists<sup>3</sup><br>(ファイル/フォルダー) |
|:--- |:--- |:--- |:--- |:--- |:--- |:--- |:--- |:--- |:--- |:--- |
| [Amazon S3](connector-amazon-simple-storage-service.md) | √/√ | √/√ | √ | x/x | √/√ | √ | x | √ | √ | √/√ |
| [Google Cloud Storage](connector-google-cloud-storage.md) | √/√ | √/√ | √ | x/x | √/√ | √ | x | √ | √ | √/√ |
| [Azure BLOB Storage](connector-azure-blob-storage.md) | √/√ | √/√ | √ | x/x | √/√ | √ | √ | √ | √ | √/√ |
| [Azure Data Lake Storage Gen1](connector-azure-data-lake-store.md) | √/√ | √/√ | √ | x/x | √/√ | √ | x | √ | √ | √/√ |
| [Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md) | √/√ | √/√ | √ | x/x | √/√ | √ | √ | √ | √ | √/√ |
| [Azure Files](connector-azure-file-storage.md) | √/√ | √/√ | √ | √/√ | √/√ | √ | x | √ | √ | √/√ |
| [ファイル システム](connector-file-system.md) | √/√ | √/√ | √ | √/√ | √/√ | √ | x | √ | √ | √/√ |
| [SFTP](connector-sftp.md) | √/√ | √/√ | √ | x/x | √/√ | √ | x | √ | √ | √/√ |
| [FTP](connector-ftp.md) | √/√ | √/√ | √ | x/x | x/x | √ | x | √ | √ | √/√ |

<sup>1</sup> メタデータ `lastModified`:
- Amazon S3 および Google Cloud Storage の場合、`lastModified` はバケットとキーに適用されますが、仮想フォルダーには適用されません。また、`exists` はバケットとキーに適用されますが、プレフィックスまたは仮想フォルダーには適用されません。 
- Azure Blob Storage の場合、`lastModified` はコンテナーと BLOB に適用されますが、仮想フォルダーには適用されません。

<sup>2</sup> バイナリ ファイル、JSON ファイル、または XML ファイルからメタデータを取得する場合、メタ データ `structure` および `columnCount` はサポートされません。

<sup>3</sup> メタデータ `exists`: Amazon S3 と Google Cloud Storage の場合、`exists` はバケットとキーには適用されますが、プレフィックスや仮想フォルダーには適用されません。

次のことを考慮してください。

- フォルダーに対してメタデータの取得アクティビティを使用している場合は、指定されたフォルダーに対する LIST/EXECUTE 権限があることを確認してください。
- フォルダー/ファイルに対するワイルドカード フィルターは、Get Metadata アクティビティではサポートされていません。
- コネクタに設定された `modifiedDatetimeStart` および `modifiedDatetimeEnd` フィルター:

    - これらの 2 つのプロパティは、フォルダーからメタデータを取得するときに子項目をフィルター処理するために使用されます。 ファイルからメタデータを取得するときには適用されません。
    - このようなフィルターを使用すると、出力の `childItems` には、指定された範囲内で変更されたファイルのみが含まれ、フォルダーは含まれません。
    - このようなフィルターを適用するには、GetMetadata アクティビティを使用して、指定されたフォルダー内のすべてのファイルを列挙し、変更された時刻を確認します。 予想される修飾ファイル数が少ない場合でも、ファイル数が多いフォルダーを指すことは避けてください。 

**リレーショナル データベース**

| コネクタ/メタデータ | structure | columnCount | exists |
|:--- |:--- |:--- |:--- |
| [Azure SQL Database](connector-azure-sql-database.md) | √ | √ | √ |
| [Azure SQL Managed Instance](../azure-sql/managed-instance/sql-managed-instance-paas-overview.md) | √ | √ | √ |
| [Azure Synapse Analytics](connector-azure-sql-data-warehouse.md) | √ | √ | √ |
| [SQL Server](connector-sql-server.md) | √ | √ | √ |

### <a name="metadata-options"></a>メタデータのオプション

メタデータの取得アクティビティのフィールド リストで次のメタデータの種類を指定して、対応する情報を取得できます。

| メタデータの種類 | 説明 |
|:--- |:--- |
| itemName | ファイルまたはフォルダーの名前です。 |
| itemType | ファイルまたはフォルダーの種類です。 戻り値は `File` または `Folder` です。 |
| size | ファイルのサイズです (バイト単位)。 ファイルにのみ適用されます。 |
| created | ファイルまたはフォルダーの作成日時です。 |
| lastModified | ファイルまたはフォルダーが最後に変更された日時です。 |
| childItems | 特定のフォルダー内のサブフォルダーとファイルの一覧です。 フォルダーにのみ適用されます。 戻り値は、各子項目の名前と種類の一覧です。 |
| contentMD5 | ファイルの MD5 です。 ファイルにのみ適用されます。 |
| structure | ファイルまたはリレーショナル データベース テーブルのデータ構造です。 戻り値は、列名と列の型の一覧です。 |
| columnCount | ファイルまたはリレーショナル テーブル内の列の数です。 |
| exists| ファイル、フォルダー、またはテーブルが存在するかどうかを示します。 メタデータの取得フィールドの一覧内で `exists` が指定されている場合、ファイル、フォルダー、またはテーブルが存在しない場合でもアクティビティは失敗しません。 代わりに、`exists: false` が出力に返されます。 |

> [!TIP]
> ファイル、フォルダー、またはテーブルが存在することを検証する場合は、メタデータの取得フィールドの一覧内で `exists` を指定します。 その後、アクティビティ出力内の `exists: true/false` の結果を確認できます。 フィールドの一覧内で `exists` が指定されていない場合、オブジェクトが見つからないとメタデータの取得アクティビティは失敗します。

> [!NOTE]
> ファイル ストアからメタデータを取得し、`modifiedDatetimeStart` または `modifiedDatetimeEnd` を構成した場合、出力内の `childItems` には、指定されたパスにあるファイルのうち、最終変更時刻が指定された範囲内のものだけが含まれます。 サブフォルダー内の項目は含まれません。

> [!NOTE]
> **structure** フィールド リストに区切りテキストと Excel 形式のデータセットの実際のデータ構造を示すには、`First Row as Header` プロパティを有効にする必要があります。これは、これらのデータ ソースでのみサポートされています。

## <a name="syntax"></a>構文

**GetMetadata アクティビティ**

```json
{
    "name":"MyActivity",
    "type":"GetMetadata",
    "dependsOn":[

    ],
    "policy":{
        "timeout":"7.00:00:00",
        "retry":0,
        "retryIntervalInSeconds":30,
        "secureOutput":false,
        "secureInput":false
    },
    "userProperties":[

    ],
    "typeProperties":{
        "dataset":{
            "referenceName":"MyDataset",
            "type":"DatasetReference"
        },
        "fieldList":[
            "size",
            "lastModified",
            "structure"
        ],
        "storeSettings":{
            "type":"AzureBlobStorageReadSettings"
        },
        "formatSettings":{
            "type":"JsonReadSettings"
        }
    }
}
```

**データセット**

```json
{
    "name":"MyDataset",
    "properties":{
        "linkedServiceName":{
            "referenceName":"AzureStorageLinkedService",
            "type":"LinkedServiceReference"
        },
        "annotations":[

        ],
        "type":"Json",
        "typeProperties":{
            "location":{
                "type":"AzureBlobStorageLocation",
                "fileName":"file.json",
                "folderPath":"folder",
                "container":"container"
            }
        }
    }
}
```

## <a name="type-properties"></a>型のプロパティ

現在、メタデータの取得アクティビティは、次の種類のメタデータ情報を返すことができます。

プロパティ | 説明 | 必須
-------- | ----------- | --------
fieldList | 必要なメタデータ情報の種類です。 サポートされているメタデータに関する詳細は、この記事の「[メタデータのオプション](#metadata-options)」セクションを参照してください。 | はい 
dataset | メタデータの取得アクティビティによってメタデータが取得される参照データセット。 サポートされているコネクタの詳細については、「[機能](#supported-capabilities)」セクションを参照してください。 データセットの構文の詳細については、特定のコネクタのトピックを参照してください。 | はい
formatSettings | 書式の種類のデータセットを使用するときに適用します。 | いいえ
storeSettings | 書式の種類のデータセットを使用するときに適用します。 | いいえ

## <a name="sample-output"></a>サンプル出力

メタデータの取得の結果は、アクティビティの出力に表示されます。 次に、さまざまなメタデータ オプションを示す 2 つのサンプルを示します。 後続のアクティビティで結果を使用するには、次のパターンを使用します: `@{activity('MyGetMetadataActivity').output.itemName}`。

### <a name="get-a-files-metadata"></a>ファイルのメタデータを取得する

```json
{
  "exists": true,
  "itemName": "test.csv",
  "itemType": "File",
  "size": 104857600,
  "lastModified": "2017-02-23T06:17:09Z",
  "created": "2017-02-23T06:17:09Z",
  "contentMD5": "cMauY+Kz5zDm3eWa9VpoyQ==",
  "structure": [
    {
        "name": "id",
        "type": "Int64"
    },
    {
        "name": "name",
        "type": "String"
    }
  ],
  "columnCount": 2
}
```

### <a name="get-a-folders-metadata"></a>フォルダーのメタデータを取得する

```json
{
  "exists": true,
  "itemName": "testFolder",
  "itemType": "Folder",
  "lastModified": "2017-02-23T06:17:09Z",
  "created": "2017-02-23T06:17:09Z",
  "childItems": [
    {
      "name": "test.avro",
      "type": "File"
    },
    {
      "name": "folder hello",
      "type": "Folder"
    }
  ]
}
```

## <a name="next-steps"></a>次のステップ
Data Factory でサポートされている他の制御フロー アクティビティについて学習します。

- [パイプラインの実行アクティビティ](control-flow-execute-pipeline-activity.md)
- [ForEach アクティビティ](control-flow-for-each-activity.md)
- [Lookup アクティビティ](control-flow-lookup-activity.md)
- [Web アクティビティ](control-flow-web-activity.md)
