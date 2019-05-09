---
title: Azure Batch プール サイズ変更開始イベント | Microsoft Docs
description: Batch のプール サイズ変更開始イベントのリファレンスです。
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
ms.openlocfilehash: 63abeaca5a9c87945671e79a9c8d7a2512d0d777
ms.sourcegitcommit: 698a3d3c7e0cc48f784a7e8f081928888712f34b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/31/2019
ms.locfileid: "55471336"
---
# <a name="pool-resize-start-event"></a>プールのサイズ変更の開始イベント

 このイベントは、プールのサイズ変更が開始されたときに発生します。 プール サイズ変更は非同期イベントであるため、サイズ変更操作が完了するとプール サイズ変更の完了イベントが発生することが想定されます。

 次の例では、プールのサイズを 0 ノードから 2 ノードへ手動でサイズ変更する場合のプール サイズ変更開始イベントの本文を示します。

```
{
    "poolId": "myPool1",
    "nodeDeallocationOption": "invalid",
    "currentDedicated": 0,
    "targetDedicated": 2,
    "enableAutoScale": false,
    "isAutoPool": false
}
```

|要素|型|メモ|
|-------------|----------|-----------|
|poolId|String|プールの ID。|
|nodeDeallocationOption|String|プールからノードが削除されるとき、プールのサイズが減少するかを指定します。<br /><br /> 次のいずれかの値になります。<br /><br /> **requeue** – 実行中のタスクを終了して、再度キューに入れます。 このタスクは、ジョブが有効になると再び実行されます。 タスクが終了するとすぐにノードを削除します。<br /><br /> **terminate** – 実行中のタスクを終了します。 タスクは再び実行されることがありません。 タスクが終了するとすぐにノードを削除します。<br /><br /> **taskcompletion** – 現在実行中のタスクが完了することを許可します。 待機中に新しいタスクをスケジュールしません。 すべてのタスクが完了するとノードを削除します。<br /><br /> **Retaineddata** - 現在実行中のタスクを完了できるようにしてから、すべてのタスク データ保有期間が終了するまで待機します。 待機中に新しいタスクをスケジュールしません。 すべてのタスク保有期間が終了したとき、ノードを削除します。<br /><br /> 既定値は requeue です。<br /><br /> プールのサイズが増加している場合、値は**無効**に設定されます。|
|currentDedicated|Int32|プールに現在割り当てられているコンピューティング ノードの数。|
|targetDedicated|Int32|プールに要求されたコンピューティング ノード数。|
|enableAutoScale|Bool|プールのサイズを自動的に調整し続けるかどうかを指定します。|
|isAutoPool|Bool|プールがジョブの AutoPool メカニズムを介して作成されたかどうかを指定します。|
