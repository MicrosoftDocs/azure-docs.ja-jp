---
title: Azure Batch プールのサイズ変更の完了イベント | Microsoft Docs
description: Batch プールのサイズ変更完了イベントのリファレンスです。
services: batch
author: laurenhughes
manager: jeconnoc
ms.assetid: ''
ms.service: batch
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: big-compute
ms.date: 04/20/2017
ms.author: lahugh
ms.openlocfilehash: 87c98b89a49adbad88841dccbd4ba47d370b2be7
ms.sourcegitcommit: 698a3d3c7e0cc48f784a7e8f081928888712f34b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/31/2019
ms.locfileid: "55474311"
---
# <a name="pool-resize-complete-event"></a>プールのサイズ変更の完了イベント

 このイベントは、プールのサイズ変更が完了または失敗したときに出力されます。

 次の例では、あるプールについてサイズを増加し、正常に完了した、プールのサイズ変更完了イベントの本文を示しています。

```
{
    "id": "p_1_0_01503750-252d-4e57-bd96-d6aa05601ad8",
    "nodeDeallocationOption": "invalid",
    "currentDedicated": 4,
    "targetDedicated": 4,
    "enableAutoScale": false,
    "isAutoPool": false,
    "startTime": "2016-09-09T22:13:06.573Z",
    "endTime": "2016-09-09T22:14:01.727Z",
    "result": "Success",
    "resizeError": "The operation succeeded"
}
```

|要素|型|メモ|
|-------------|----------|-----------|
|id|String|プールの ID。|
|nodeDeallocationOption|String|プールからノードが削除されるとき、プールのサイズが減少するかを指定します。<br /><br /> 次のいずれかの値になります。<br /><br /> **requeue** – 実行中のタスクを終了して、再度キューに入れます。 このタスクは、ジョブが有効になると再び実行されます。 タスクが終了するとすぐにノードを削除します。<br /><br /> **terminate** – 実行中のタスクを終了します。 タスクは再び実行されることがありません。 タスクが終了するとすぐにノードを削除します。<br /><br /> **taskcompletion** – 現在実行中のタスクが完了することを許可します。 待機中に新しいタスクをスケジュールしません。 すべてのタスクが完了したときにノードを削除します。<br /><br /> **Retaineddata** - 現在実行中のタスクが完了することを許可し、すべてのタスク データ保有期間が終了するまで待機します。 待機中に新しいタスクをスケジュールしません。 すべてのタスク保有期間が終了したとき、ノードを削除します。<br /><br /> 既定値は requeue です。<br /><br /> プールのサイズが増加している場合、値は**無効**に設定されます。|
|currentDedicated|Int32|プールに現在割り当てられているコンピューティング ノードの数。|
|targetDedicated|Int32|プールに要求されたコンピューティング ノード数。|
|enableAutoScale|Bool|プールのサイズを自動的に調整し続けるかどうかを指定します。|
|isAutoPool|Bool|プールがジョブの AutoPool メカニズムを介して作成されたかどうかを指定します。|
|startTime|DateTime|プールのサイズ変更が開始された時間。|
|endTime|DateTime|プールのサイズ変更が完了した時間。|
|resultCode|String|サイズ変更の結果。|
|resultMessage|String|サイズ変更のエラーには、結果の詳細が含まれています。<br /><br /> サイズ変更が正常に完了した場合、操作が成功したことが示されています。|