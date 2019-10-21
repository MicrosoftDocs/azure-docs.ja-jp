---
title: Azure Scheduler の制限、クォータ、およびしきい値
description: Azure Scheduler の制限、クォータ、既定値、およびスロットルしきい値について学習する
services: scheduler
ms.service: scheduler
author: derek1ee
ms.author: deli
ms.reviewer: klam
ms.assetid: 88f4a3e9-6dbd-4943-8543-f0649d423061
ms.topic: article
ms.date: 08/18/2016
ms.openlocfilehash: 293cd956f8270a4863fcc657f58c970096cec1e3
ms.sourcegitcommit: 29880cf2e4ba9e441f7334c67c7e6a994df21cfe
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/26/2019
ms.locfileid: "71300918"
---
# <a name="limits-quotas-and-throttle-thresholds-in-azure-scheduler"></a>Azure Scheduler の制限、クォータ、およびスロットルしきい値

> [!IMPORTANT]
> [Azure Logic Apps](../logic-apps/logic-apps-overview.md) は、[廃止される予定](../scheduler/migrate-from-scheduler-to-logic-apps.md#retire-date)の Azure Scheduler の後継です。 Scheduler で設定したジョブを使用し続けるには、できるだけ早く [Azure Logic Apps に移行](../scheduler/migrate-from-scheduler-to-logic-apps.md)してください。

## <a name="limits-quotas-and-thresholds"></a>制限、クォータ、およびしきい値

[!INCLUDE [scheduler-limits-table](../../includes/scheduler-limits-table.md)]

## <a name="x-ms-request-id-header"></a>x-ms-request-id ヘッダー

Scheduler サービスに対するすべての要求は、**x-ms-request-id** という名前の応答ヘッダーを返します。このヘッダーには、要求を一意に識別する非透過の値が含まれています。 要求の形式が正しいにもかかわらず要求が常に失敗する場合は、**x-ms-request-id** 応答ヘッダー値を指定して次の詳細を含めることで、Microsoft にエラーをレポートできます。 

* **x-ms-request-id** 値
* 要求が行われた、おおよその時刻 
* Azure サブスクリプション、ジョブ コレクション、およびジョブの識別子 
* 要求で試行された操作の種類

## <a name="see-also"></a>関連項目

* [Azure Scheduler とは](scheduler-intro.md)
* [Azure Scheduler の概念、用語集、エンティティ階層構造](scheduler-concepts-terms.md)
