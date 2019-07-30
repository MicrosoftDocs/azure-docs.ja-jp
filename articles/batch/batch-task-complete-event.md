---
title: Azure Batch タスク完了イベント | Microsoft Docs
description: Batch のタスク完了イベントのリファレンスです。
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
ms.openlocfilehash: 60e5e6cc6fdd839c8bbe44d8e1d2e794e7afb34d
ms.sourcegitcommit: 4b431e86e47b6feb8ac6b61487f910c17a55d121
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/18/2019
ms.locfileid: "68323058"
---
# <a name="task-complete-event"></a>タスク完了イベント

 タスクが完了すると、終了コードに関係なく、このイベントが発生します。 このイベントを使用して、タスクの期間、タスクが実行された場所、タスクが再試行されたかどうかを調べることができます。


 次の例は、タスク完了イベントの本文を示しています。

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
        "startTime": "2016-09-08T16:32:23.799Z",
        "endTime": "2016-09-08T16:34:00.666Z",
        "exitCode": 0,
        "retryCount": 0,
        "requeueCount": 0
    }
}
```

|要素名|Type|メモ|
|------------------|----------|-----------|
|jobId|string|タスクを含むジョブの ID です。|
|id|string|タスクの ID です。|
|taskType|string|タスクの型です。 ジョブ マネージャー タスクを示す 'JobManager' と、ジョブ マネージャー タスクでないことを示す 'User' のいずれかです。 このイベントは、ジョブ準備タスク、ジョブ リリース タスク、または開始タスクでは発生しません。|
|systemTaskVersion|Int32|これは、タスクの内部再試行カウンターです。 Batch サービスは一時的問題に対応するタスクを内部で再試行できます。 これらの問題には、内部的なスケジュール エラーや、コンピューティング ノードを異常な状態から回復しようとする動作が含まれます。|
|[nodeInfo](#nodeInfo)|複合型|タスクが実行されたコンピューティング ノードに関する情報が含まれます。|
|[multiInstanceSettings](#multiInstanceSettings)|複合型|このタスクが複数のコンピューティング ノードが必要な複数インスタンス タスクであることを指定します。  詳細は [multiInstanceSettings](https://docs.microsoft.com/rest/api/batchservice/get-information-about-a-task) をご覧ください。|
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
|numberOfInstances|Int32|タスクに必要なコンピューター ノードの数。|

###  <a name="constraints"></a> constraints

|要素名|Type|メモ|
|------------------|----------|-----------|
|maxTaskRetryCount|Int32|タスクを再試行できる最大回数。 Batch サービスは、終了コードが 0 以外の場合にタスクを再試行します。<br /><br /> この値によって再試行の回数が限定されますのでご注意ください。 Batch サービスはタスクを 1 回試行してから、上限に達するまで再試行できます。 たとえば、最大再試行回数が 3 の場合、Batch はタスクを最大 4 回試行します (初回試行 1 回と再試行 3 回)。<br /><br /> 最大再試行回数が 0 の場合、Batch サービスはタスクを再試行しません。<br /><br /> 最大再試行回数が -1 の場合、Batch サービスはタスクを無制限に再試行します。<br /><br /> 既定値は 0 (再試行なし) です。|

###  <a name="executionInfo"></a> executionInfo

|要素名|Type|メモ|
|------------------|----------|-----------|
|startTime|DateTime|タスクの実行が開始した時刻です。 '実行中' は**実行している**状態に対応するため、タスクがリソース ファイルやアプリケーション パッケージを指定する場合、開始時刻はタスクがこれらのファイルやパッケージのダウンロードやデプロイを開始した時刻を反映します。  タスクが再起動または再実行された場合は、タスクが実行を開始したつい最近の時刻となります。|
|endTime|DateTime|タスクが完了した時刻です。|
|exitCode|Int32|タスクの終了コード。|
|retryCount|Int32|Batch サービスによりタスクが再試行された回数。 タスクは、0 以外の終了コードで終了すると、指定された MaxTaskRetryCount まで再試行されます。|
|requeueCount|Int32|Batch サービスによりタスクがユーザー要求の結果として再度キューに入れられる回数です。<br /><br /> ユーザーが (プールをサイズ変更したり圧縮することで) プールからノードを削除するときやジョブを無効にしているとき、ユーザーはノードで実行中のタスクを再実行のキューに再度入れるよう指定できます。 この回数は、タスクがこうした理由により何回キューに入れられたかを追跡します。|
