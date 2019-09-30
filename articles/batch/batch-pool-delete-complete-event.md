---
title: Azure Batch プール削除完了イベント | Microsoft Docs
description: Batch のプール削除完了イベントのリファレンスです。
services: batch
author: laurenhughes
manager: gwallace
ms.assetid: ''
ms.service: batch
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: big-compute
ms.date: 04/20/2017
ms.author: lahugh
ms.openlocfilehash: 9bf50bd19ca3f4316c4c2ddbcdd3333745ebefd7
ms.sourcegitcommit: 267a9f62af9795698e1958a038feb7ff79e77909
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/04/2019
ms.locfileid: "70258558"
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

|要素|型|メモ|
|-------------|----------|-----------|
|`id`|string|プールの ID。|
|`startTime`|DateTime|プールの削除が開始された時刻。|
|`endTime`|DateTime|プールの削除が完了した時刻。|

## <a name="remarks"></a>解説
プールのサイズ変更操作の状態とエラー コードの詳細については、[アカウントからのプールの削除](https://docs.microsoft.com/rest/api/batchservice/delete-a-pool-from-an-account)を参照してください。
