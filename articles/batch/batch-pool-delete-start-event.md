---
title: Azure Batch プール削除開始イベント | Microsoft Docs
description: Batch のプール削除開始イベントのリファレンスです。
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
ms.openlocfilehash: 2352971af3844b56f93c16ebaf6cb23bd5fd8a5a
ms.sourcegitcommit: 698a3d3c7e0cc48f784a7e8f081928888712f34b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/31/2019
ms.locfileid: "55474294"
---
# <a name="pool-delete-start-event"></a>プール削除の開始イベント

 このイベントは、プールの削除操作が開始されたときに出力されます。 プールの削除は非同期イベントであるため、削除操作が完了したら、プール削除の完了イベントが出力されることを予測できます。

 次の例は、プール削除の開始イベントの本文を示しています。

```
{
    "id": "myPool1"
}
```

|要素|型|メモ|
|-------------|----------|-----------|
|id|String|プールの ID。|