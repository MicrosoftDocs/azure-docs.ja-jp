---
title: Azure Batch タスクの開始イベント | Microsoft Docs
description: Batch タスクの開始イベントのリファレンスです。
services: batch
author: laurenhughes
manager: gwallace
ms.assetid: ''
ms.service: batch
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: big-compute
ms.date: 04/20/2017
ms.author: lahugh
ms.openlocfilehash: 76100f1457123ac88055fddd55eb22a102201adf
ms.sourcegitcommit: 4b431e86e47b6feb8ac6b61487f910c17a55d121
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/18/2019
ms.locfileid: "68322804"
---
# <a name="task-start-event"></a>タスク開始イベント

 スケジューラがコンピューティング ノードでタスクの開始予定を設定すると、このイベントが生成されます。 タスクが再試行またはキューに再び追加されると、このイベントは同じタスクに再度生成されますが、これにより再試行回数とシステム タスクのバージョンは更新されますのでご注意ください。


 次の例は、タスク開始イベントの本文を示しています。

```
{
    "jobId": "job-0000000001",
    "id": "task-5",
    "taskType": "User",
    "systemTaskVersion": 0,
    "nodeInfo": {
        "poolId": "pool-001",
        "nodeId": "tvm-257509324_1-20160908t162728z"
    },
    "multiInstanceSettings": {
        "numberOfInstances": 1
    },
    "constraints": {
        "maxTaskRetryCount": 2
    },
    "executionInfo": {
        "retryCount": 0
    }
}
```

|要素名|Type|メモ|
|------------------|----------|-----------|
|jobId|string|タスクを含むジョブの ID です。|
|id|string|タスクの ID です。|
|taskType|string|タスクの型です。 ジョブ マネージャー タスクを示す 'JobManager' と、ジョブ マネージャー タスクでないことを示す 'User' のいずれかです。|
|systemTaskVersion|Int32|これは、タスクの内部再試行カウンターです。 Batch サービスは一時的問題に対応するタスクを内部で再試行できます。 これらの問題には、内部的なスケジュール エラーや、コンピューティング ノードを異常な状態から回復しようとする動作が含まれます。|
|[nodeInfo](#nodeInfo)|複合型|タスクが実行されたコンピューティング ノードに関する情報が含まれます。|
|[multiInstanceSettings](#multiInstanceSettings)|複合型|このタスクが複数のコンピューティング ノードを必要とする、複数インスタンス タスクであることを指定します。  詳細は [multiInstanceSettings](https://docs.microsoft.com/rest/api/batchservice/get-information-about-a-task) をご覧ください。|
|[constraints](#constraints)|複合型|このタスクに適用される実行の制約。|
|[executionInfo](#executionInfo)|複合型|タスクの実行に関する情報が含まれます。|

###  <a name="nodeInfo"></a> nodeInfo

|要素名|Type|メモ|
|------------------|----------|-----------|
|poolId|string|タスクが実行されたプールの ID。|
|nodeId|string|タスクが実行されたノードの ID。|

###  <a name="multiInstanceSettings"></a> multiInstanceSettings

|要素名|Type|メモ|
|------------------|----------|-----------|
|numberOfInstances|int|タスクに必要なコンピューティング ノードの数。|

###  <a name="constraints"></a> constraints

|要素名|Type|メモ|
|------------------|----------|-----------|
|maxTaskRetryCount|Int32|タスクを再試行できる最大回数。 Batch サービスは、終了コードが 0 以外の場合にタスクを再試行します。<br /><br /> この値によって再試行の回数が限定されますのでご注意ください。 Batch サービスはタスクを 1 回試行してから、上限に達するまで再試行できます。 たとえば、最大再試行回数が 3 の場合、Batch はタスクを最大 4 回試行します (初回試行 1 回と再試行 3 回)。<br /><br /> 最大再試行回数が 0 の場合、Batch サービスはタスクを再試行しません。<br /><br /> 最大再試行回数が -1 の場合、Batch サービスはタスクを無制限に再試行します。<br /><br /> 既定値は 0 (再試行なし) です。|

###  <a name="executionInfo"></a> executionInfo

|要素名|Type|メモ|
|------------------|----------|-----------|
|retryCount|Int32|Batch サービスによりタスクが再試行された回数。 タスクは、0 以外のコードで終了すると、指定された MaxTaskRetryCount まで再試行されます。|
