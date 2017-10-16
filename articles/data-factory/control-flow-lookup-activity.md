---
title: "Azure Data Factory でのルックアップ アクティビティ | Microsoft Docs"
description: "ルックアップ アクティビティを使用して外部ソースから値を検索する方法を説明します。 この出力は、後続のアクティビティによってさらに参照できます。"
services: data-factory
documentationcenter: 
author: sharonlo101
manager: jhubbard
editor: shlo
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/31/2017
ms.author: spelluru
ms.openlocfilehash: 30173f8eea2ccbbcd44018596cf34b3769a64b50
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="lookup-activity-in-azure-data-factory"></a>Azure Data Factory でのルックアップ アクティビティ
ルックアップ アクティビティを使用して、任意の外部ソースからレコード/テーブル名/値を読み取ったり検索したりできます。 この出力は、後続のアクティビティによってさらに参照できます。 

現在、ルックアップでは次のデータ ソースがサポートされています。
- Azure BLOB の JSON ファイル
- オンプレミスの JSON ファイル
- Azure SQL Database (クエリから変換された JSON データ)
- Azure Table Storage (クエリから変換された JSON データ)

ルックアップ アクティビティは、構成ファイルまたはデータ ソースからファイル/レコード/テーブルのリストを動的に取得する場合に役立ちます。 アクティビティからの出力は、他のアクティビティでそれらのアイテムに対してのみ特定の処理を実行するためにさらに使用できます。

> [!NOTE]
> この記事は、現在プレビュー段階にある Data Factory のバージョン 2 に適用されます。 一般公開 (GA) されている Data Factory サービスのバージョン 1 を使用している場合は、[Data Factory V1 のドキュメント](v1/data-factory-introduction.md)を参照してください。


## <a name="example"></a>例
この例では、コピー アクティビティで、Azure SQL Database 内の SQL テーブルから Azure Blob Storage にデータをコピーします。 SQL テーブルの名前は、Blob Storage 内の JSON ファイルに格納されます。 ルックアップ アクティビティは、実行時にテーブル名を検索します。 このアプローチでは、パイプライン/データセットを再デプロイすることなく JSON を動的に変更できます。 

### <a name="pipeline"></a>パイプライン
このパイプラインには、**ルックアップ**と**コピー**の 2 つのアクティビティが含まれます。 

- ルックアップ アクティビティは、Azure Blob Storage 内の場所を表す LookupDataset を使用するように設定されています。 ルックアップ アクティビティは、この場所にある JSON ファイルから SQL テーブルの名前を読み取ります。 
- コピー アクティビティは、ルックアップ アクティビティの出力 (SQL テーブルの名前) を使用します。 ソース データセット (SourceDataset) 内の tableName は、ルックアップ アクティビティからの出力を使用するように設定されています。 コピー アクティビティは、SQL テーブルから SinkDataset で指定された Azure Blob Storage 内の場所にデータをコピーします。 


```json
{
    "name": "LookupPipelineDemo",
    "properties": {
        "activities": [
            {
                "name": "LookupActivity",
                "type": "Lookup",
                "typeProperties": {
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
                        "sqlReaderQuery": "select * from @{activity('LookupActivity').output.tableName}" 
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
ルックアップ データセットは、AzureStorageLinkedService で指定された Azure Storage 内のルックアップ フォルダーにある sourcetable.json ファイルを指します。 

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

### <a name="source-dataset-for-the-copy-activity"></a>コピー アクティビティのソース データセット
ソース データセットは、SQL テーブルの名前であるルックアップ アクティビティの出力を使用します。 コピー アクティビティは、この SQL テーブルから、シンク データセットで指定された Azure Blob Storage 内の場所にデータをコピーします。 

```json
{
    "name": "SourceDataset",
    "properties": {
        "type": "AzureSqlTable",
        "typeProperties":{
            "tableName": "@{activity('LookupActivity').output.tableName}"
        },
        "linkedServiceName": {
            "referenceName": "AzureSqlLinkedService",
            "type": "LinkedServiceReference"
        }
    }
}
```

### <a name="sink-dataset-for-the-copy-activity"></a>コピー アクティビティのシンク データセット
コピー アクティビティは、SQL テーブルから、AzureStorageLinkedService で指定された Azure Storage 内の csv フォルダーにある filebylookup.csv ファイルにデータをコピーします。 

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
この Azure SQL Database には、Blob Storage にコピーされるデータが格納されています。 

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
  "tableName": "Table1",
}
{
   "Id": "2",
  "tableName": "Table2",
}
```
#### <a name="array-of-objects"></a>オブジェクトの配列

```json
[ 
    {
        "Id": "1",
          "tableName": "Table1",
    }
    {
        "Id": "2",
        "tableName": "Table2",
    }
]
```



## <a name="type-properties"></a>型のプロパティ
名前 | Description | 型 | 必須
---- | ----------- | ---- | --------
データセット | データセット属性は、ルックアップ用のデータセット参照を提供するためのものです。 現在サポートされているデータセットの種類は次のとおりです。<ul><li>FileShareDataset</li><li>AzureBlobDataset</li><li>AzureSqlTableDataset</li><li>AzureTableDataset</li> | キーと値のペア | あり
source セクション | データセット固有のソース プロパティ (コピー アクティビティ ソースと同じ) | キーと値のペア | いいえ
firstRowOnly | 最初の行またはすべての行を返します。 | boolean | いいえ

## <a name="next-steps"></a>次のステップ
Data Factory でサポートされている他の制御フロー アクティビティを参照してください。 

- [ExecutePipeline アクティビティ](control-flow-execute-pipeline-activity.md)
- [ForEach アクティビティ](control-flow-for-each-activity.md)
- [GetMetadata アクティビティ](control-flow-get-metadata-activity.md)
- [Web アクティビティ](control-flow-web-activity.md)
