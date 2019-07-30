---
title: Azure 一括分析 | Microsoft Docs
description: Azure 一括分析のリファレンスです。
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
ms.openlocfilehash: 0f8233846f3835a2bbccf9a7072f223d435b90af
ms.sourcegitcommit: 4b431e86e47b6feb8ac6b61487f910c17a55d121
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/18/2019
ms.locfileid: "68323828"
---
# <a name="batch-analytics"></a>一括分析
一括分析のトピックには、Batch サービスのリソースで使用できるイベントとアラートに関するリファレンス情報が含まれています。

Batch 診断ログの有効化と利用の詳細については「[Azure Batch 診断ログ](https://azure.microsoft.com/documentation/articles/batch-diagnostics/)」を参照してください。

## <a name="diagnostic-logs"></a>診断ログ

Azure Batch サービスは、特定の Batch リソースの有効期間中に、次の診断ログ イベントを出力します。

**サービス ログ イベント**
* [プール作成](batch-pool-create-event.md)
* [プール削除の開始](batch-pool-delete-start-event.md)
* [プール削除の完了](batch-pool-delete-complete-event.md)
* [プールのサイズ変更の開始](batch-pool-resize-start-event.md)
* [プールのサイズ変更の完了](batch-pool-resize-complete-event.md)
* [タスク開始](batch-task-start-event.md)
* [タスク完了](batch-task-complete-event.md)
* [タスク失敗](batch-task-fail-event.md)