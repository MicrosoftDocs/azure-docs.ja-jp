---
title: Azure Data Factory の検証アクティビティ | Microsoft Docs
description: 検証アクティビティは、ユーザーが指定する特定の条件でアタッチされたデータセットを検証するまで、パイプラインの実行を継続しません。
services: data-factory
documentationcenter: ''
author: sharonlo101
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 03/25/2019
ms.author: shlo
ms.openlocfilehash: 46447bdbea93d1f99c5682cf878c2035e6f49b78
ms.sourcegitcommit: 6da4959d3a1ffcd8a781b709578668471ec6bf1b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/27/2019
ms.locfileid: "58522791"
---
# <a name="validation-activity-in-azure-data-factory"></a>Azure Data Factory の検証アクティビティ
パイプラインで検証を使用すると、アタッチされたデータセット参照が存在していて、指定された条件を満たしていること、またはタイムアウトに達していることを検証した後でのみ、パイプラインが実行を継続することを確認できます。


## <a name="syntax"></a>構文

```json

{
    "name": "Validation_Activity",
    "type": "Validation",
    "typeProperties": {
        "dataset": {
            "referenceName": "Storage_File",
            "type": "DatasetReference"
        },
        "timeout": "7.00:00:00",
        "sleep": 10,
        "minimumSize": 20
    }
},
{
    "name": "Validation_Activity_Folder",
    "type": "Validation",
    "typeProperties": {
        "dataset": {
            "referenceName": "Storage_Folder",
            "type": "DatasetReference"
        },
        "timeout": "7.00:00:00",
        "sleep": 10,
        "childItems": true
    }
}

```


## <a name="type-properties"></a>型のプロパティ

プロパティ | 説明 | 使用できる値 | 必須
-------- | ----------- | -------------- | --------
name | '検証' アクティビティの名前 | String | はい |
type | **Validation** に設定する必要があります。 | String | はい |
dataset | アクティビティは、このデータセット参照が存在していて指定された条件を満たしていること、またはタイムアウトに達していることを検証するまで、実行をブロックします。 提供するデータセットは、"MinimumSize" または "ChildItems" プロパティをサポートする必要があります。 | データセット参照 | はい |
timeout | アクティビティの実行に関するタイムアウトを指定します。 値が指定されていない場合は、既定値の 7 日 ("7.00:00:00") が使用されます。 形式は d.hh:mm:ss です | String | いいえ  |
sleep | 検証の試行間の遅延 (秒単位)。 値が指定されていない場合は、既定値の 10 秒が使用されます。 | 整数 | いいえ  |
childItems | フォルダーに子項目があるかどうかチェックします。 次のいずれかに設定できます。true:フォルダーが存在していることと項目があることを検証します。 フォルダー内に少なくとも 1 つの項目が存在するか、タイムアウト値に到達するまで、ブロックします。-false:フォルダーが存在していることと、それが空であることを検証します。 フォルダーが空になるまで、またはタイムアウト値に達するまで、ブロックします。 値が指定されていない場合、フォルダーが存在するかタイムアウトに達するまで、アクティビティはブロックされます。 | Boolean | いいえ  |
minimumSize | ファイルの最小サイズ (バイト単位)。 値が指定されていない場合は、既定値の 0 バイトが使用されます | 整数 | いいえ  |


## <a name="next-steps"></a>次の手順
Data Factory でサポートされている他の制御フロー アクティビティを参照してください。

- [If Condition アクティビティ](control-flow-if-condition-activity.md)
- [ExecutePipeline アクティビティ](control-flow-execute-pipeline-activity.md)
- [ForEach アクティビティ](control-flow-for-each-activity.md)
- [GetMetadata アクティビティ](control-flow-get-metadata-activity.md)
- [ルックアップ アクティビティ](control-flow-lookup-activity.md)
- [Web アクティビティ](control-flow-web-activity.md)
- [Until アクティビティ](control-flow-until-activity.md)
