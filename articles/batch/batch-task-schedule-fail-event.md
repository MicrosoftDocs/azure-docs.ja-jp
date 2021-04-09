---
title: Azure Batch タスク スケジュール失敗イベント
description: Batch タスク スケジュール失敗イベントのリファレンスです。 このイベントは、タスクのスケジューリングに失敗し、後で再試行する場合に発生します。
ms.topic: reference
ms.date: 09/20/2020
ms.openlocfilehash: 549281d2b2c371e8f09c584e771cf44f7abc8a00
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "91852074"
---
# <a name="task-schedule-fail-event"></a>タスク スケジュール失敗イベント

 このイベントは、タスクのスケジューリングに失敗し、後で再試行される場合に発生します。 これは、`requiredSlots` が指定されたタスクを実行するために必要なスロットがノード上で不足しているなどのリソースの制限により、タスクのスケジュール設定時に発生する一時的なエラーです。

 次の例は、タスク スケジュール失敗イベントの本文を示しています。

```
{
    "jobId": "job-01",
    "id": "task-01",
    "taskType": "User",
    "systemTaskVersion": 665378862,
    "requiredSlots": 1,
    "nodeInfo": {
        "poolId": "pool-01",
        "nodeId": " "
    },
    "multiInstanceSettings": {
        "numberOfInstances": 1
    },
    "constraints": {
        "maxTaskRetryCount": 0
    },
    "schedulingError": {
        "category": "UserError",
        "code": "JobPreparationTaskFailed",
        "message": "Task cannot run because the job preparation task failed on node"
    }
}
```

|要素名|Type|Notes|
|------------------|----------|-----------|
|`jobId`|String|タスクを含むジョブの ID です。|
|`id`|String|タスクの ID です。|
|`taskType`|String|タスクの型です。 ジョブ マネージャー タスクを示す 'JobManager' と、ジョブ マネージャー タスクでないことを示す 'User' のいずれかです。 このイベントは、ジョブ準備タスク、ジョブ リリース タスク、または開始タスクでは発生しません。|
|`systemTaskVersion`|Int32|これは、タスクの内部再試行カウンターです。 Batch サービスは一時的問題に対応するタスクを内部で再試行できます。 これらの問題には、内部的なスケジュール エラーや、コンピューティング ノードを異常な状態から回復しようとする動作が含まれます。|
|`requiredSlots`|Int32|タスクを実行するために必要なスロット。|
|[`nodeInfo`](#nodeInfo)|複合型|タスクが実行されたコンピューティング ノードに関する情報が含まれます。|
|[`multiInstanceSettings`](#multiInstanceSettings)|複合型|このタスクが複数のコンピューティング ノードが必要な複数インスタンス タスクであることを指定します。  詳細については、[`multiInstanceSettings`](/rest/api/batchservice/get-information-about-a-task) を参照してください。|
|[`constraints`](#constraints)|複合型|このタスクに適用される実行の制約。|
|[`schedulingError`](#schedulingError)|複合型|タスクのスケジュール設定エラーに関する情報が含まれます。|

###  <a name="nodeinfo"></a><a name="nodeInfo"></a> nodeInfo

|要素名|Type|Notes|
|------------------|----------|-----------|
|`poolId`|String|タスクが実行されたプールの ID。|
|`nodeId`|String|タスクが実行されたノードの ID。|

###  <a name="multiinstancesettings"></a><a name="multiInstanceSettings"></a> multiInstanceSettings

|要素名|Type|メモ|
|------------------|----------|-----------|
|`numberOfInstances`|Int32|タスクに必要なコンピューター ノードの数。|

###  <a name="constraints"></a><a name="constraints"></a> constraints

|要素名|Type|メモ|
|------------------|----------|-----------|
|`maxTaskRetryCount`|Int32|タスクを再試行できる最大回数。 Batch サービスは、終了コードが 0 以外の場合にタスクを再試行します。<br /><br /> この値によって再試行の回数が限定されますのでご注意ください。 Batch サービスはタスクを 1 回試行してから、上限に達するまで再試行できます。 たとえば、最大再試行回数が 3 の場合、Batch はタスクを最大 4 回試行します (初回試行 1 回と再試行 3 回)。<br /><br /> 最大再試行回数が 0 の場合、Batch サービスはタスクを再試行しません。<br /><br /> 最大再試行回数が -1 の場合、Batch サービスはタスクを無制限に再試行します。<br /><br /> 既定値は 0 (再試行なし) です。|


###  <a name="schedulingerror"></a><a name="schedulingError"></a> schedulingError

|要素名|Type|Notes|
|------------------|----------|-----------|
|`category`|String|エラーのカテゴリ。|
|`code`|String|タスクのスケジュール設定エラーの識別子。 コードは不変であり、プログラムによって使用されることを意図しています。|
|`message`|String|ユーザー インターフェイスに表示するのに適した、タスクのスケジュール設定エラーについて説明するメッセージ。|
