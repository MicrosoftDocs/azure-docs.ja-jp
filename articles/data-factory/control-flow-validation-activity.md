---
title: Azure Data Factory の検証アクティビティ | Microsoft Docs
description: 検証アクティビティは、ユーザーが指定する特定の条件でアタッチされたデータセットを検証するまで、パイプラインの実行を継続しません。
services: data-factory
documentationcenter: ''
author: djpmsft
ms.author: daperlov
manager: jroth
ms.reviewer: maghan
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 03/25/2019
ms.openlocfilehash: 77fdab04e03429d135875cb2ef223e8c23d312a2
ms.sourcegitcommit: d200cd7f4de113291fbd57e573ada042a393e545
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/29/2019
ms.locfileid: "70141602"
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
名前 | '検証' アクティビティの名前 | string | はい |
type | **Validation** に設定する必要があります。 | string | はい |
dataset | アクティビティは、このデータセット参照が存在していて指定された条件を満たしていること、またはタイムアウトに達していることを検証するまで、実行をブロックします。 提供するデータセットは、"MinimumSize" または "ChildItems" プロパティをサポートする必要があります。 | データセット参照 | はい |
timeout | アクティビティの実行に関するタイムアウトを指定します。 値が指定されていない場合は、既定値の 7 日 ("7.00:00:00") が使用されます。 形式は d.hh:mm:ss です | string | いいえ |
sleep | 検証の試行間の遅延 (秒単位)。 値が指定されていない場合は、既定値の 10 秒が使用されます。 | 整数 | いいえ |
childItems | フォルダーに子項目があるかどうかチェックします。 次のいずれかに設定できます。true:フォルダーが存在していることと項目があることを検証します。 フォルダー内に少なくとも 1 つの項目が存在するか、タイムアウト値に到達するまで、ブロックします。-false:フォルダーが存在していることと、それが空であることを検証します。 フォルダーが空になるまで、またはタイムアウト値に達するまで、ブロックします。 値が指定されていない場合、フォルダーが存在するかタイムアウトに達するまで、アクティビティはブロックされます。 | Boolean | いいえ |
minimumSize | ファイルの最小サイズ (バイト単位)。 値が指定されていない場合は、既定値の 0 バイトが使用されます | 整数 | いいえ |


## <a name="next-steps"></a>次の手順
Data Factory でサポートされている他の制御フロー アクティビティを参照してください。

- [If Condition アクティビティ](control-flow-if-condition-activity.md)
- [ExecutePipeline アクティビティ](control-flow-execute-pipeline-activity.md)
- [ForEach アクティビティ](control-flow-for-each-activity.md)
- [GetMetadata アクティビティ](control-flow-get-metadata-activity.md)
- [ルックアップ アクティビティ](control-flow-lookup-activity.md)
- [Web アクティビティ](control-flow-web-activity.md)
- [Until アクティビティ](control-flow-until-activity.md)
