---
title: Azure Scheduler の制限、クォータ、およびしきい値
description: Azure Scheduler の制限、クォータ、既定値、およびスロットルしきい値について学習する
services: scheduler
ms.service: scheduler
author: derek1ee
ms.author: deli
ms.reviewer: klam, estfan
ms.topic: article
ms.date: 08/18/2016
ms.openlocfilehash: 610232dab776648bb3dcc7c301ec292e9acad9fc
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "78898521"
---
# <a name="limits-quotas-and-throttle-thresholds-in-azure-scheduler"></a>Azure Scheduler の制限、クォータ、およびスロットルしきい値

> [!IMPORTANT]
> [Azure Logic Apps](../logic-apps/logic-apps-overview.md) は、[廃止される予定](../scheduler/migrate-from-scheduler-to-logic-apps.md#retire-date)の Azure Scheduler の後継です。 Scheduler で設定したジョブを使用し続けるには、できるだけ早く [Azure Logic Apps に移行](../scheduler/migrate-from-scheduler-to-logic-apps.md)してください。 
>
> Scheduler は Azure portal で利用できなくなりましたが、現時点では [REST API](/rest/api/scheduler) と [Azure Scheduler PowerShell コマンドレット](scheduler-powershell-reference.md)がまだ使用できるので、お客様はジョブとジョブ コレクションを管理することができます。

## <a name="limits-quotas-and-thresholds"></a>制限、クォータ、およびしきい値

[!INCLUDE [scheduler-limits-table](../../includes/scheduler-limits-table.md)]

## <a name="x-ms-request-id-header"></a>x-ms-request-id ヘッダー

Scheduler サービスに対するすべての要求は、**x-ms-request-id** という名前の応答ヘッダーを返します。このヘッダーには、要求を一意に識別する非透過の値が含まれています。 要求の形式が正しいにもかかわらず要求が常に失敗する場合は、**x-ms-request-id** 応答ヘッダー値を指定して次の詳細を含めることで、Microsoft にエラーをレポートできます。 

* **x-ms-request-id** 値
* 要求が行われた、おおよその時刻 
* Azure サブスクリプション、ジョブ コレクション、およびジョブの識別子 
* 要求で試行された操作の種類

## <a name="next-steps"></a>次のステップ

* [Azure Scheduler の概念、用語集、エンティティ階層構造](scheduler-concepts-terms.md)
* [Azure Scheduler のプランと課金](scheduler-plans-billing.md)
* [Azure Scheduler REST API リファレンス](/rest/api/scheduler)
* [Azure Scheduler PowerShell コマンドレット リファレンス](scheduler-powershell-reference.md)