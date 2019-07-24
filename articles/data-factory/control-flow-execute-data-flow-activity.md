---
title: Azure Data Factory でのデータ フローの実行アクティビティ | Microsoft Docs
description: データ フローの実行アクティビティでデータ フローを実行します。
services: data-factory
documentationcenter: ''
author: kromerm
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 02/22/2019
ms.author: makromer
ms.openlocfilehash: 856f4bd9c2b04ff10ed598c5e641955e1de99398
ms.sourcegitcommit: d89b679d20ad45d224fd7d010496c52345f10c96
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/12/2019
ms.locfileid: "57792414"
---
# <a name="execute-data-flow-activity-in-azure-data-factory"></a>Azure Data Factory でのデータ フローの実行アクティビティ
データ フローの実行アクティビティを使用して、パイプライン デバッグ (サンドボックス) 実行とパイプライン トリガー実行で ADF データ フローを実行します。

[!INCLUDE [notes](../../includes/data-factory-data-flow-preview.md)]

## <a name="syntax"></a>構文

```json
{
    "name": "MyDataFlowActivity",
    "type": "ExecuteDataFlow",
    "typeProperties": {
      "dataflow": {
         "referenceName": "dataflow1",
         "type": "DataFlowReference"
      },
        "compute": {
          "computeType": "General",
          "coreCount": 8,
      }
}

```

## <a name="type-properties"></a>型のプロパティ

* ```dataflow``` は、実行するデータ フロー エンティティの名前です。
* ```compute``` は、Spark 実行環境を示します。
* ```coreCount``` は、データ フローのこのアクティビティの実行に割り当てるコア数です。

![データ フローの実行](media/data-flow/activity-data-flow.png "データ フローの実行")

### <a name="run-on"></a>Run on (実行先)

データ フローのこの実行のコンピューティング環境を選択します。 既定では、Azure Auto-Resolve Default Integration Runtime が選択されています。 この選択肢では、データ ファクトリと同じリージョンの Spark 環境でデータ フローが実行されます。 コンピューティングの種類がジョブ クラスターになるため、コンピューティング環境の起動に数分かかります。

### <a name="debugging-pipelines-with-data-flows"></a>データ フローを使用したパイプラインのデバッグ

![デバッグ ボタン](media/data-flow/debugbutton.png "デバッグ ボタン")

データ フロー デバッグでは、ウォーミングされたクラスターを利用して、パイプライン デバッグ実行でデータ フローを対話形式でテストします。 パイプライン内でデータ フローをテストするには、パイプライン デバッグ オプションを使用します。

### <a name="compute-type"></a>コンピューティングの種類

データ フローの要件に応じて、[汎用]、[コンピューティング最適化]、または [メモリ最適化] を選択できます。

### <a name="core-count"></a>コア数

ジョブに割り当てるコア数を選択します。 小規模なジョブの場合、コア数が少ないほど効率的に動作します。

### <a name="staging-area"></a>ステージング領域

データを Azure Data Warehouse にシンクする場合は、Polybase バッチ読み込み用のステージングの場所を選択する必要があります。

## <a name="parameterized-datasets"></a>パラメーター化されたデータセット

パラメーター化されたデータセットを使用する場合は、パラメーター値を必ず設定します。

![データ フローの実行のパラメーター](media/data-flow/params.png "パラメーター")

### <a name="debugging-parameterized-data-flows"></a>パラメーター化されたデータ フローのデバッグ

パラメーター化されたデータセットを使用するデータ フローは、データ フローの実行アクティビティを使用してパイプライン デバッグ実行からのみデバッグできます。 現時点では、ADF Data Flow での対話型デバッグ セッションは、パラメーター化されたデータセットでは機能しません。 パイプラインの実行とデバッグ実行では、パラメーターが使用されます。

## <a name="next-steps"></a>次の手順
Data Factory でサポートされている他の制御フロー アクティビティを参照してください。 

- [If Condition アクティビティ](control-flow-if-condition-activity.md)
- [ExecutePipeline アクティビティ](control-flow-execute-pipeline-activity.md)
- [ForEach アクティビティ](control-flow-for-each-activity.md)
- [GetMetadata アクティビティ](control-flow-get-metadata-activity.md)
- [ルックアップ アクティビティ](control-flow-lookup-activity.md)
- [Web アクティビティ](control-flow-web-activity.md)
- [Until アクティビティ](control-flow-until-activity.md)
