---
title: PowerShell コマンドレット リファレンス - Azure Scheduler
description: Azure Scheduler の PowerShell コマンドレットについて
services: scheduler
ms.service: scheduler
author: derek1ee
ms.author: deli
ms.reviewer: klam
ms.assetid: 9a26c457-d7a1-4e4a-bc79-f26592155218
ms.topic: article
ms.date: 08/18/2016
ms.openlocfilehash: 4b179c50af8b1ffc4313a49da978f178915ec9cc
ms.sourcegitcommit: 6e32f493eb32f93f71d425497752e84763070fad
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/10/2019
ms.locfileid: "59470816"
---
# <a name="powershell-cmdlets-reference-for-azure-scheduler"></a>Azure Scheduler の PowerShell コマンドレット リファレンス

> [!IMPORTANT]
> [Azure Logic Apps](../logic-apps/logic-apps-overview.md) は、廃止予定の Azure Scheduler の後継です。 ジョブをスケジュールするには、[Azure Logic Apps](../scheduler/migrate-from-scheduler-to-logic-apps.md) を代わりにお使いください。 

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Scheduler ジョブとジョブ コレクションを作成および管理するためのスクリプトを作成するために、PowerShell コマンドレットを使用できます。 この記事では、Azure Scheduler 用の主要な PowerShell コマンドレットと、その参考記事へのリンクを列挙します。 お使いの Azure サブスクリプションの Azure PowerShell をインストールするには、[Azure PowerShell をインストールして構成する方法](/powershell/azure/overview)を参照してください。 [Azure Resource Manager コマンドレット](/powershell/azure/overview)の詳細については、「[Azure Resource Manager での Azure PowerShell の使用](../powershell-azure-resource-manager.md)」を参照してください。

| コマンドレット | 説明 |
|--------|-------------|
| [Disable-AzSchedulerJobCollection](/powershell/module/azurerm.scheduler/disable-azurermschedulerjobcollection) |ジョブ コレクションを無効にします。 |
| [Enable-AzureRmSchedulerJobCollection](/powershell/module/azurerm.scheduler/enable-azurermschedulerjobcollection) |ジョブ コレクションを有効にします。 |
| [Get-AzSchedulerJob](/powershell/module/azurerm.scheduler/get-azurermschedulerjob) |Scheduler ジョブを取得します。 |
| [Get-AzSchedulerJobCollection](/powershell/module/azurerm.scheduler/get-azurermschedulerjobcollection) |ジョブ コレクションを取得します。 |
| [Get-AzSchedulerJobHistory](/powershell/module/azurerm.scheduler/get-azurermschedulerjobhistory) |ジョブ履歴を取得します。 |
| [New-AzSchedulerHttpJob](/powershell/module/azurerm.scheduler/new-azurermschedulerhttpjob) |HTTP ジョブを作成します。 |
| [New-AzSchedulerJobCollection](/powershell/module/azurerm.scheduler/new-azurermschedulerjobcollection) |ジョブ コレクションを作成します。 |
| [New-AzSchedulerServiceBusQueueJob](/powershell/module/azurerm.scheduler/new-azurermschedulerservicebusqueuejob) | Service Bus キュー ジョブを作成します。 |
| [New-AzSchedulerServiceBusTopicJob](/powershell/module/azurerm.scheduler/new-azurermschedulerservicebustopicjob) |Service Bus トピック ジョブを作成します。 |
| [New-AzSchedulerStorageQueueJob](/powershell/module/azurerm.scheduler/new-azurermschedulerstoragequeuejob) |Storage キュー ジョブを作成します。 |
| [Remove-AzSchedulerJob](/powershell/module/azurerm.scheduler/remove-azurermschedulerjob) |Scheduler ジョブを削除します。 |
| [Remove-AzSchedulerJobCollection](/powershell/module/azurerm.scheduler/remove-azurermschedulerjobcollection) |ジョブ コレクションを削除します。 |
| [Set-AzSchedulerHttpJob](/powershell/module/azurerm.scheduler/set-azurermschedulerhttpjob) |Scheduler HTTP ジョブを変更します。 |
| [Set-AzSchedulerJobCollection](/powershell/module/azurerm.scheduler/set-azurermschedulerjobcollection) |ジョブ コレクションを変更します。 |
| [Set-AzSchedulerServiceBusQueueJob](/powershell/module/azurerm.scheduler/set-azurermschedulerservicebusqueuejob) |Service Bus キュー ジョブを変更します。 |
| [Set-AzSchedulerServiceBusTopicJob](/powershell/module/azurerm.scheduler/set-azurermschedulerservicebustopicjob) |Service Bus トピック ジョブを変更します。 |
| [Set-AzSchedulerStorageQueueJob](/powershell/module/azurerm.scheduler/set-azurermschedulerstoragequeuejob) |Storage キュー ジョブを変更します。 |
||| 

詳細については、これらのコマンドレットのいずれかを実行してください。 

```
Get-Help <cmdlet name> -Detailed
Get-Help <cmdlet name> -Examples
Get-Help <cmdlet name> -Full
```

## <a name="see-also"></a>関連項目

* [Azure Scheduler とは](scheduler-intro.md)
* [概念、用語集、エンティティ階層構造](scheduler-concepts-terms.md)
* [最初のジョブの作成とスケジュール設定 - Azure ポータル](scheduler-get-started-portal.md)
* [Azure Scheduler REST API リファレンス](https://msdn.microsoft.com/library/mt629143)
