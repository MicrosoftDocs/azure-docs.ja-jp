---
title: "Azure Data Factory の GetMetadata アクティビティ | Microsoft Docs"
description: "SQL Server ストアド プロシージャ アクティビティを使用して、Data Factory パイプラインから Azure SQL Database または Azure SQL Data Warehouse でストアド プロシージャを呼び出す方法について説明します。"
services: data-factory
documentationcenter: 
author: sharonlo101
manager: jhubbard
editor: spelluru
ms.assetid: 1c46ed69-4049-44ec-9b46-e90e964a4a8e
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/31/2017
ms.author: shlo
ms.openlocfilehash: 99182b2ed91f6d60f499be0078077bf52fe8b366
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="get-metadata-activity-in-azure-data-factory"></a>Azure Data Factory の GetMetadata アクティビティ
GetMetadata アクティビティを使用すると、Azure Data Factory で任意のデータのメタデータを取得できます。 このアクティビティは、バージョン 2 の Data Factory でのみサポートされています。 これは、次のシナリオで使用できます。

- 任意のデータのメタデータ情報を検証する
- データが準備完了/使用可能になったらパイプラインをトリガーする

制御フローでは、次の機能を使用できます。
- GetMetadata アクティビティからの出力を条件式で使用することによって検証を実行できます。
- Do-Until ループによって条件が満たされたら、パイプラインをトリガーできます。

GetMetadata アクティビティは必須の入力としてデータセットを受け取り、使用可能なメタデータ情報を出力として出力します。 現在は、Azure BLOB データセットのみがサポートされています。 サポートされるメタデータ フィールドは、size、structure、および lastModified 時間です。  

> [!NOTE]
> この記事は、現在プレビュー段階にある Data Factory のバージョン 2 に適用されます。 一般公開 (GA) されている Data Factory サービスのバージョン 1 を使用している場合は、[Data Factory V1 のドキュメント](v1/data-factory-introduction.md)を参照してください。


## <a name="syntax"></a>構文

### <a name="get-metadata-activity-definition"></a>GetMetadata アクティビティの定義:
次の例では、GetMetadata アクティビティは MyDataset によって表されるデータに関するメタデータを返します。 

```json
{
    "name": "MyActivity",
    "type": "GetMetadata",
    "typeProperties": {
        "fieldList" : ["size", "lastModified", "structure"],
        "dataset": {
            "referenceName": "MyDataset",
            "type": "DatasetReference"
        }
    }
}
```
### <a name="dataset-definition"></a>データセットの定義:

```json
{
    "name": "MyDataset",
    "properties": {
    "type": "AzureBlob",
        "linkedService": {
            "referenceName": "StorageLinkedService",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "folderPath":"container/folder",
            "Filename": "file.json",
            "format":{
                "type":"JsonFormat"
                "nestedSeperator": ","
            }
        }
    }
}
```

### <a name="output"></a>出力
```json
{
    "size": 1024,
    "structure": [
        {
            "name": "id",
            "type": "Int64"
        }, 
    ],
    "lastModified": "2016-07-12T00:00:00Z"
}
```

## <a name="type-properties"></a>型のプロパティ
現在、GetMetadata アクティビティは、Azure ストレージ データセットから次のタイプのメタデータ情報をフェッチできます。

プロパティ | Description | 使用できる値 | 必須
-------- | ----------- | -------------- | --------
fieldList | 必要なメタデータ情報のタイプを一覧表示します。  | <ul><li>size</li><li>structure</li><li>lastModified</li></ul> |    いいえ<br/>空の場合、アクティビティは 3 つのサポートされるすべてのメタデータ情報を返します。 
データセット | GetMetadata アクティビティによってメタデータ アクティビティが取得される参照データセット。 <br/><br/>現在サポートされているデータセットのタイプは Azure BLOB です。 2 つのサブプロパティは次のとおりです。 <ul><li><b>referenceName</b>: 既存の Azure BLOB データセットへの参照</li><li><b>type</b>: データセットが参照されているため、そのタイプは "DatasetReference" です</li></ul> |    <ul><li>String</li><li>DatasetReference</li></ul> | あり

## <a name="next-steps"></a>次のステップ
Data Factory でサポートされている他の制御フロー アクティビティを参照してください。 

- [ExecutePipeline アクティビティ](control-flow-execute-pipeline-activity.md)
- [ForEach アクティビティ](control-flow-for-each-activity.md)
- [ルックアップ アクティビティ](control-flow-lookup-activity.md)
- [Web アクティビティ](control-flow-web-activity.md)