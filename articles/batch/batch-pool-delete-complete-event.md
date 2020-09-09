---
title: Azure Batch のプール削除完了イベント
description: Batch のプール削除完了イベントのリファレンスです。 このイベントは、プールの削除操作が完了したときに出力されます。
ms.topic: reference
ms.date: 04/20/2017
ms.openlocfilehash: f00d32fd5e4eb49fddf4975a9b64ce792d0226dd
ms.sourcegitcommit: 845a55e6c391c79d2c1585ac1625ea7dc953ea89
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/05/2020
ms.locfileid: "85962460"
---
# <a name="pool-delete-complete-event"></a>プール削除の完了イベント

 このイベントは、プールの削除操作が完了したときに出力されます。

 次の例は、プール削除の完了イベントの本文を示しています。

```
{
    "id": "myPool1",
    "startTime": "2016-09-09T22:13:48.579Z",
    "endTime": "2016-09-09T22:14:08.836Z"
}
```

|要素|種類|メモ|
|-------------|----------|-----------|
|`id`|String|プールの ID。|
|`startTime`|DateTime|プールの削除が開始された時刻。|
|`endTime`|DateTime|プールの削除が完了した時刻。|

## <a name="remarks"></a>解説
プールのサイズ変更操作の状態とエラー コードの詳細については、[アカウントからのプールの削除](/rest/api/batchservice/delete-a-pool-from-an-account)を参照してください。
