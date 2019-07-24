---
title: Azure Data Factory でのルックアップ アクティビティ | Microsoft Docs
description: ルックアップ アクティビティを使用して外部ソースから値を検索する方法を説明します。 この出力は、後続のアクティビティによってさらに参照できます。
services: data-factory
documentationcenter: ''
author: sharonlo101
manager: craigg
editor: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 06/15/2018
ms.author: shlo
ms.openlocfilehash: bc695bf8398a39460eff9bbe4f791ba92b0fa7e0
ms.sourcegitcommit: 25936232821e1e5a88843136044eb71e28911928
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/04/2019
ms.locfileid: "54019316"
---
# <a name="lookup-activity-in-azure-data-factory"></a>Azure Data Factory でのルックアップ アクティビティ

ルックアップ アクティビティは、Azure Data Factory がサポートするすべてのデータ ソースからデータセットを取得できます。 これは、次のシナリオで使用します。
- オブジェクト名をハード コーディングする代わりに、後続のアクティビティで操作するオブジェクトを動的に決定する。 オブジェクトの例として、ファイルとテーブルがあります。

ルックアップ アクティビティは、構成ファイルやテーブルの内容を読み取って返します。 また、クエリまたはストアド プロシージャの実行結果を返します。 ルックアップ アクティビティからの出力は、それがシングルトン値の場合は、後続のコピーや変換アクティビティで使用できます。 出力が属性の配列の場合は、ForEach アクティビティで使用できます。

## <a name="supported-capabilities"></a>サポートされる機能

次のデータ ソースがルックアップ アクティビティでサポートされています。 ルックアップ アクティビティによって返すことができる行の最大数は 5,000、サイズは最大 2 MB です。 現時点では、タイムアウト前のルックアップ アクティビティの最長期間は 1 時間です。

[!INCLUDE [data-factory-v2-supported-data-stores](../../includes/data-factory-v2-supported-data-stores-for-lookup-activity.md)]

## <a name="syntax"></a>構文

```json
{
    "name": "LookupActivity",
    "type": "Lookup",
    "typeProperties": {
        "source": {
            "type": "<source type>"
            <additional source specific properties (optional)>
        },
        "dataset": { 
            "referenceName": "<source dataset name>",
            "type": "DatasetReference"
        },
        "firstRowOnly": false
    }
}
```

## <a name="type-properties"></a>型のプロパティ
名前 | 説明 | 型 | 必須
---- | ----------- | ---- | --------
dataset | ルックアップ用のデータセット参照を提供します。 対応する各コネクタの記事の 「**データセットのプロパティ**」セクションから詳細を取得します。 | キーと値のペア | はい
source | データセット固有のソース プロパティを含みます (コピー アクティビティ ソースと同じ)。 対応する各コネクタの記事の「**コピー アクティビティのプロパティ**」セクションから詳細を取得します。 | キーと値のペア | はい
firstRowOnly | 最初の行のみまたはすべての行のどちらを返すかを示します。 | Boolean | いいえ。 既定では、 `true`です。

> [!NOTE]

> * **ByteArray** 型のソース列はサポートされていません。
> * データセット定義内の**構造体**はサポートされていません。 テキスト形式のファイルの場合は、ヘッダー行を使用して列名を指定できます。
> * ルックアップ ソースが JSON ファイルの場合、JSON オブジェクトを整形するための `jsonPathDefinition` 設定はサポートされていません。 オブジェクト全体が取得されます。

## <a name="use-the-lookup-activity-result-in-a-subsequent-activity"></a>後続のアクティビティでルックアップ アクティビティの結果を使用する

ルックアップ結果は、アクティビティ実行結果の `output` セクションに返されます。

* **`firstRowOnly` が `true` (既定値) に設定されているときは**、出力形式は次のコードに示すとおりです。 ルックアップ結果は固定の `firstRow` キーの下にあります。 後続のアクティビティで結果を使用するには、パターン `@{activity('MyLookupActivity').output.firstRow.TableName}` を使用します。

    ```json
    {
        "firstRow":
        {
            "Id": "1",
            "TableName" : "Table1"
        }
    }
    ```

* **`firstRowOnly` が `false` に設定されているときは**、出力形式は次のコードに示すとおりです。 `count` フィールドは、返されたレコードの数を示します。 固定された `value` 配列の下に詳細な値が表示されます。 このような場合は、ルックアップ アクティビティの後ろに [Foreach アクティビティ](control-flow-for-each-activity.md)が続きます。 `value` 配列は、パターン `@activity('MyLookupActivity').output.value` を使用して ForEach アクティビティの `items` フィールドに渡します。 `value` 配列の要素にアクセスするには、構文 `@{activity('lookupActivity').output.value[zero based index].propertyname}` を使用します。 例: `@{activity('lookupActivity').output.value[0].tablename}`。

    ```json
    {
        "count": "2",
        "value": [
            {
                "Id": "1",
                "TableName" : "Table1"
            },
            {
                "Id": "2",
                "TableName" : "Table2"
            }
        ]
    } 
    ```

### <a name="copy-activity-example"></a>コピー アクティビティの例
この例では、コピー アクティビティは、Azure SQL データベース インスタンス内の SQL テーブルから Azure Blob Storage にデータをコピーします。 SQL テーブルの名前は、Blob Storage 内の JSON ファイルに格納されます。 ルックアップ アクティビティは、実行時にテーブル名を検索します。 JSON は、この方法を使用して動的に変更されます。 パイプラインやデータセットを再デプロイする必要はありません。 

この例では、最初の行のみのルックアップを示します。 すべての行のルックアップについて、および ForEach アクティビティで結果をチェーンするには、「[Azure Data Factory を使って複数のテーブルを一括コピーする](tutorial-bulk-copy.md)」のサンプルを参照してください。

### <a name="pipeline"></a>パイプライン
このパイプラインには、ルックアップとコピーという 2 つのアクティビティが含まれます。 

- ルックアップ アクティビティは、Azure Blob Storage 内の場所を表す **LookupDataset** を使用するように設定されています。 ルックアップ アクティビティは、この場所にある JSON ファイルから SQL テーブルの名前を読み取ります。 
- コピー アクティビティは、SQL テーブルの名前であるルックアップ アクティビティの出力を使用します。 **SourceDataset** 内の **tableName** プロパティは、ルックアップ アクティビティからの出力を使用するように設定されています。 コピー アクティビティは、SQL テーブルから Azure Blob Storage 内の場所にデータをコピーします。 場所は **SinkDataset** プロパティによって指定されます。 

```json
{
    "name": "LookupPipelineDemo",
    "properties": {
        "activities": [
            {
                "name": "LookupActivity",
                "type": "Lookup",
                "typeProperties": {
                    "source": {
                        "type": "BlobSource"
                    },
                    "dataset": { 
                        "referenceName": "LookupDataset", 
                        "type": "DatasetReference" 
                    }
                }
            },
            {
                "name": "CopyActivity",
                "type": "Copy",
                "typeProperties": {
                    "source": { 
                        "type": "SqlSource", 
                        "sqlReaderQuery": "select * from @{activity('LookupActivity').output.firstRow.tableName}" 
                    },
                    "sink": { 
                        "type": "BlobSink" 
                    }
                },                
                "dependsOn": [ 
                    { 
                        "activity": "LookupActivity", 
                        "dependencyConditions": [ "Succeeded" ] 
                    }
                 ],
                "inputs": [ 
                    { 
                        "referenceName": "SourceDataset", 
                        "type": "DatasetReference" 
                    } 
                ],
                "outputs": [ 
                    { 
                        "referenceName": "SinkDataset", 
                        "type": "DatasetReference" 
                    } 
                ]
            }
        ]
    }
}
```

### <a name="lookup-dataset"></a>ルックアップ データセット
**ルックアップ** データセットは、**AzureStorageLinkedService** によって指定された Azure Storage ルックアップ フォルダー内の **sourcetable.json** ファイルです。 

```json
{
    "name": "LookupDataset",
    "properties": {
        "type": "AzureBlob",
        "typeProperties": {
            "folderPath": "lookup",
            "fileName": "sourcetable.json",
            "format": {
                "type": "JsonFormat",
                "filePattern": "SetOfObjects"
            }
        },
        "linkedServiceName": {
            "referenceName": "AzureStorageLinkedService",
            "type": "LinkedServiceReference"
        }
    }
}
```

### <a name="source-dataset-for-copy-activity"></a>コピー アクティビティの**ソース** データセット
**ソース** データセットは、SQL テーブルの名前であるルックアップ アクティビティの出力を使用します。 コピー アクティビティは、SQL テーブルから Azure Blob Storage 内の場所にデータをコピーします。 場所は **sink** データセットによって指定されます。 

```json
{
    "name": "SourceDataset",
    "properties": {
        "type": "AzureSqlTable",
        "typeProperties":{
            "tableName": "@{activity('LookupActivity').output.firstRow.tableName}"
        },
        "linkedServiceName": {
            "referenceName": "AzureSqlLinkedService",
            "type": "LinkedServiceReference"
        }
    }
}
```

### <a name="sink-dataset-for-copy-activity"></a>コピー アクティビティの**シンク** データセット
コピー アクティビティは、SQL テーブルから、Azure Storage の **csv** フォルダー内の **filebylookup.csv** ファイルにデータをコピーします。 ファイルは **AzureStorageLinkedService** プロパティによって指定されます。 

```json
{
    "name": "SinkDataset",
    "properties": {
        "type": "AzureBlob",
        "typeProperties": {
            "folderPath": "csv",
            "fileName": "filebylookup.csv",
            "format": {
                "type": "TextFormat"                                                                    
            }
        },
        "linkedServiceName": {
            "referenceName": "AzureStorageLinkedService",
            "type": "LinkedServiceReference"
        }
    }
}
```

### <a name="azure-storage-linked-service"></a>Azure Storage のリンクされたサービス
このストレージ アカウントには、SQL テーブルの名前を含む JSON ファイルが格納されています。 

```json
{
    "properties": {
        "type": "AzureStorage",
        "typeProperties": {
            "connectionString": {
                "value": "DefaultEndpointsProtocol=https;AccountName=<StorageAccountName>;AccountKey=<StorageAccountKey>",
                "type": "SecureString"
            }
        }
    },
        "name": "AzureStorageLinkedService"
}
```

### <a name="azure-sql-database-linked-service"></a>Azure SQL Database のリンクされたサービス
この Azure SQL データベース インスタンスには、Blob Storage にコピーされるデータが格納されています。 

```json
{
    "name": "AzureSqlLinkedService",
    "properties": {
        "type": "AzureSqlDatabase",
        "description": "",
        "typeProperties": {
            "connectionString": {
                "value": "Server=<server>;Initial Catalog=<database>;User ID=<user>;Password=<password>;",
                "type": "SecureString"
            }
        }
    }
}
```

### <a name="sourcetablejson"></a>sourcetable.json

#### <a name="set-of-objects"></a>オブジェクトのセット

```json
{
  "Id": "1",
  "tableName": "Table1"
}
{
   "Id": "2",
  "tableName": "Table2"
}
```

#### <a name="array-of-objects"></a>オブジェクトの配列

```json
[ 
    {
        "Id": "1",
        "tableName": "Table1"
    },
    {
        "Id": "2",
        "tableName": "Table2"
    }
]
```

## <a name="limitations-and-workarounds"></a>制限事項と回避策

Lookup アクティビティと提案される回避策のいくつかの制限を次に示します。

| 制限事項 | 対処法 |
|---|---|
| Lookup アクティビティの最大行数は 5,000 行で、最大サイズは 2 MB です。 | 最大行数またはサイズを超えないデータを取得する、内側パイプライン上で外側パイプラインが反復される 2 段のパイプラインを設計します。 |
| | |

## <a name="next-steps"></a>次の手順
Data Factory でサポートされている他の制御フロー アクティビティを参照してください。 

- [パイプラインの実行アクティビティ](control-flow-execute-pipeline-activity.md)
- [ForEach アクティビティ](control-flow-for-each-activity.md)
- [GetMetadata アクティビティ](control-flow-get-metadata-activity.md)
- [Web アクティビティ](control-flow-web-activity.md)
