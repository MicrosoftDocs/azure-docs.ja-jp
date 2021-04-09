---
title: Azure Batch プールのサイズ変更の完了イベント
description: Batch プールのサイズ変更完了イベントのリファレンスです。 サイズが増加し、正常に完了したプールの例を参照してください。
ms.topic: reference
ms.date: 12/28/2020
ms.openlocfilehash: 9d3342587b5f6e0e134f4295a8c79deeb23df94b
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "97803648"
---
# <a name="pool-resize-complete-event"></a>プールのサイズ変更の完了イベント

 このイベントは、プールのサイズ変更が完了または失敗したときに出力されます。

 次の例では、あるプールについてサイズを増加し、正常に完了した、プールのサイズ変更完了イベントの本文を示しています。

```
{
   "id": "myPool",
   "nodeDeallocationOption": "invalid",
      "currentDedicatedNodes": 10,
      "targetDedicatedNodes": 10,
   "currentLowPriorityNodes": 5,
     "targetLowPriorityNodes": 5,
   "enableAutoScale": false,
   "isAutoPool": false,
   "startTime": "2016-09-09T22:13:06.573Z",
   "endTime": "2016-09-09T22:14:01.727Z",
   "resultCode": "Success",
   "resultMessage": "The operation succeeded"
}
```

|要素|Type|メモ|
|-------------|----------|-----------|
|`id`|String|プールの ID。|
|`nodeDeallocationOption`|String|プールからノードが削除されるとき、プールのサイズが減少するかを指定します。<br /><br /> 次のいずれかの値になります。<br /><br /> **requeue** – 実行中のタスクを終了して、再度キューに入れます。 このタスクは、ジョブが有効になると再び実行されます。 タスクが終了するとすぐにノードを削除します。<br /><br /> **terminate** – 実行中のタスクを終了します。 タスクは再び実行されることがありません。 タスクが終了するとすぐにノードを削除します。<br /><br /> **taskcompletion** – 現在実行中のタスクが完了することを許可します。 待機中に新しいタスクをスケジュールしません。 すべてのタスクが完了するとノードを削除します。<br /><br /> **Retaineddata** - 現在実行中のタスクが完了することを許可し、すべてのタスク データ保有期間が終了するまで待機します。 待機中に新しいタスクをスケジュールしません。 すべてのタスク保有期間が終了したとき、ノードを削除します。<br /><br /> 既定値は requeue です。<br /><br /> プールのサイズが増加している場合、値は **無効** に設定されます。|
|`currentDedicatedNodes`|Int32|プールに現在割り当てられている専用のコンピューティング ノードの数。|
|`targetDedicatedNodes`|Int32|プールのために要求されている専用のコンピューティング ノードの数。|
|`currentLowPriorityNodes`|Int32|プールに現在割り当てられている優先順位の低いコンピューティング ノードの数。|
|`targetLowPriorityNodes`|Int32|プールのために要求されている優先順位の低いコンピューティング ノードの数。|
|`enableAutoScale`|Bool|プールのサイズを自動的に調整し続けるかどうかを指定します。|
|`isAutoPool`|Bool|プールがジョブの AutoPool メカニズムを介して作成されたかどうかを指定します。|
|`startTime`|DateTime|プールのサイズ変更が開始された時間。|
|`endTime`|DateTime|プールのサイズ変更が完了した時間。|
|`resultCode`|String|サイズ変更の結果。|
|`resultMessage`|String| 結果に関する詳細メッセージ。<br /><br /> サイズ変更が正常に完了した場合、操作が成功したことが示されています。|
