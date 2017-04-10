---
title: "Azure Data Factory のデータセット列のマッピング | Microsoft Docs"
description: "ソース列を変換先列にマップする方法について説明します。"
services: data-factory
documentationcenter: 
author: linda33wj
manager: jhubbard
editor: monicar
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/21/2017
ms.author: jingwang
translationtype: Human Translation
ms.sourcegitcommit: b4802009a8512cb4dcb49602545c7a31969e0a25
ms.openlocfilehash: 444ac59b0b21795228e08f79a25a638d72403399
ms.lasthandoff: 03/29/2017


---

# <a name="map-source-dataset-columns-to-destination-dataset-columns"></a>ソース データセット列を変換先のデータセット列にマップする
列マッピングは、ソース テーブル マップの “structure” 列を、シンク テーブルの “structure” 列に対し指定するために使用できます。 **columnMapping** プロパティは、コピー アクティビティの **typeProperties** セクションにあります。

列マッピングは、次のシナリオをサポートしています。

* ソース データセット構造のすべての列が、シンク データセット構造のすべての列にマップされる。
* ソース データセット構造の列のサブセットが、シンク データセット構造のすべての列にマップされる。

次のエラー状態では例外が発生します。

* シンク テーブルの “structure” の列数が、マッピングの指定数より多いか少ない。
* 重複したマッピング。
* SQL クエリの結果に、マッピングで指定されている列名がない。

> [!NOTE]
> 以下の例は Azure SQL と Azure BLOB 向けですが、四角形のデータセットをサポートする任意のデータ ストアにも適用できます。 例で適切なデータ ソース内のデータを示すには、データセットとリンクされているサービスの定義を調整します。

## <a name="sample-1--column-mapping-from-azure-sql-to-azure-blob"></a>例 1 – Azure SQL から Azure BLOB への列マッピング
この例の入力テーブルには構造体が 1 つあり、Azure SQL データベース内の SQL テーブルをポイントしています。

```json
{
    "name": "AzureSQLInput",
    "properties": {
        "structure": 
         [
           { "name": "userid"},
           { "name": "name"},
           { "name": "group"}
         ],
        "type": "AzureSqlTable",
        "linkedServiceName": "AzureSqlLinkedService",
        "typeProperties": {
            "tableName": "MyTable"
        },
        "availability": {
            "frequency": "Hour",
            "interval": 1
        },
        "external": true,
        "policy": {
            "externalData": {
                "retryInterval": "00:01:00",
                "retryTimeout": "00:10:00",
                "maximumRetry": 3
            }
        }
    }
}
```

この例の出力テーブルには構造体が 1 つあり、Azure BLOB ストレージ内の BLOB をポイントしています。

```json
{
    "name": "AzureBlobOutput",
    "properties":
    {
         "structure": 
          [
                { "name": "myuserid"},
                { "name": "myname" },
                { "name": "mygroup"}
          ],
        "type": "AzureBlob",
        "linkedServiceName": "StorageLinkedService",
        "typeProperties": {
            "folderPath": "mycontainer/myfolder",
            "fileName":"myfile.csv",
            "format":
            {
                "type": "TextFormat",
                "columnDelimiter": ","
            }
        },
        "availability":
        {
            "frequency": "Hour",
            "interval": 1
        }
    }
}
```

次の JSON は、パイプラインのコピー アクティビティを定義します。 ソースの列は、**Translator** プロパティを使用して、シンク (**columnMappings**) の列にマップされます。

```json
{
    "name": "CopyActivity",
    "description": "description", 
    "type": "Copy",
    "inputs":  [ { "name": "AzureSQLInput"  } ],
    "outputs":  [ { "name": "AzureBlobOutput" } ],
    "typeProperties":    {
        "source":
        {
            "type": "SqlSource"
        },
        "sink":
        {
            "type": "BlobSink"
        },
        "translator": 
        {
            "type": "TabularTranslator",
            "ColumnMappings": "UserId: MyUserId, Group: MyGroup, Name: MyName"
        }
    },
   "scheduler": {
          "frequency": "Hour",
          "interval": 1
        }
}
```
**列マッピングのフロー:**

![列マッピングのフロー](./media/data-factory-map-columns/column-mapping-flow.png)

## <a name="sample-2--column-mapping-with-sql-query-from-azure-sql-to-azure-blob"></a>例 2 – SQL クエリを使用した Azure SQL から Azure BLOB への列マッピング
この例では、“structure” セクションでテーブル名と列名を単純に指定する代わりに、SQL クエリを Azure SQL からデータを抽出するために使用します。 

```json
{
    "name": "CopyActivity",
    "description": "description", 
    "type": "CopyActivity",
    "inputs":  [ { "name": " AzureSQLInput"  } ],
    "outputs":  [ { "name": " AzureBlobOutput" } ],
    "typeProperties":
    {
        "source":
        {
            "type": "SqlSource",
            "SqlReaderQuery": "$$Text.Format('SELECT * FROM MyTable WHERE StartDateTime = \\'{0:yyyyMMdd-HH}\\'', WindowStart)"
        },
        "sink":
        {
            "type": "BlobSink"
        },
        "Translator": 
        {
            "type": "TabularTranslator",
            "ColumnMappings": "UserId: MyUserId, Group: MyGroup,Name: MyName"
        }
    },
    "scheduler": {
          "frequency": "Hour",
          "interval": 1
        }
}
```
この場合、クエリの結果は、ソースの “structure” で指定された列に最初にマップされます。 次に、“structure” ソースからの列が、columnMappings で指定した規則によって、シンク"structure"内の列にマップされます。  クエリは 5 つの列、すなわち、ソースの “structure” で指定されている列よりも 2 つ多い列を返しています。

**列マッピングのフロー**

![列マッピングのフロー - 2](./media/data-factory-map-columns/column-mapping-flow-2.png)

## <a name="next-steps"></a>次のステップ
コピー アクティビティの使用に関するチュートリアルは、次の記事をご覧ください。 

- [Blob Storage から SQL データベースにデータをコピーする](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)

