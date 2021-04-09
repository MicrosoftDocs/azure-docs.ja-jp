---
title: Azure Batch 分析
description: 一括分析のトピックには、Batch サービスのリソースで使用できるイベントとアラートに関するリファレンス情報が含まれています。
ms.topic: reference
ms.date: 10/08/2020
ms.openlocfilehash: 0d55ecd7f10e267a9cb469dffcdf26c131c8ce41
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "91849513"
---
# <a name="batch-analytics"></a>一括分析

このセクションのトピックには、Batch サービスのリソースで使用できるイベントとアラートに関するリファレンス情報が含まれています。

Batch 診断ログの有効化と利用の詳細については「[Azure Batch 診断ログ](./batch-diagnostics.md)」を参照してください。

## <a name="diagnostic-logs"></a>診断ログ

Azure Batch サービスは、特定の Batch リソースの有効期間中に、次の診断ログ イベントを出力します。

### <a name="service-log-events"></a>サービス ログ イベント

- [プール作成](batch-pool-create-event.md)
- [プール削除の開始](batch-pool-delete-start-event.md)
- [プール削除の完了](batch-pool-delete-complete-event.md)
- [プールのサイズ変更の開始](batch-pool-resize-start-event.md)
- [プールのサイズ変更の完了](batch-pool-resize-complete-event.md)
- [プールの自動スケーリング](batch-pool-autoscale-event.md)
- [タスク開始](batch-task-start-event.md)
- [タスク完了](batch-task-complete-event.md)
- [タスク失敗](batch-task-fail-event.md)
- [タスク スケジュール失敗](batch-task-schedule-fail-event.md)
