---
title: "Scheduler の制限と既定値"
description: "Scheduler の制限と既定値"
services: scheduler
documentationcenter: .NET
author: derek1ee
manager: kevinlam1
editor: 
ms.assetid: 88f4a3e9-6dbd-4943-8543-f0649d423061
ms.service: scheduler
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 08/18/2016
ms.author: deli
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: db6b1c196cb468f41c7a7ce34758de346b522abb


---
# <a name="scheduler-limits-and-defaults"></a>Scheduler の制限と既定値
## <a name="scheduler-quotas-limits-defaults-and-throttles"></a>Scheduler のクォータ、制限、既定値、調整
[!INCLUDE [scheduler-limits-table](../../includes/scheduler-limits-table.md)]

## <a name="the-x-ms-request-id-header"></a>x-ms-request-id ヘッダー
Scheduler サービスに対するすべての要求は、**x-ms-request-id**という名前の応答ヘッダーを返します。 このヘッダーには、要求を一意に識別する非透過の値が含まれています。

要求の形式が正しいにもかかわらず要求が常に失敗する場合は、この値を使用して Microsoft にエラーを報告することができます。 このとき、x-ms-request-id の値、要求が行われたおおよその時間、サブスクリプションの識別子、ジョブ コレクションやジョブのほかに、要求で試みた操作の種類もレポートに含めてください。

## <a name="see-also"></a>関連項目
 [What is Scheduler? (Scheduler とは)](scheduler-intro.md)

 [Azure Scheduler の概念、用語集、エンティティ階層構造](scheduler-concepts-terms.md)

 [Azure ポータル内で Scheduler を使用した作業開始](scheduler-get-started-portal.md)

 [Azure Scheduler のプランと課金](scheduler-plans-billing.md)

 [Azure Scheduler REST API リファレンス](https://msdn.microsoft.com/library/mt629143)

 [Azure Scheduler PowerShell コマンドレット リファレンス](scheduler-powershell-reference.md)

 [Azure Scheduler の高可用性と信頼性](scheduler-high-availability-reliability.md)

 [Azure Scheduler 送信認証](scheduler-outbound-authentication.md)




<!--HONumber=Nov16_HO3-->


