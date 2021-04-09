---
title: PowerShell コマンドレット リファレンス
description: Azure Scheduler の PowerShell コマンドレットについて
services: scheduler
ms.service: scheduler
author: derek1ee
ms.author: deli
ms.reviewer: klam, estfan
ms.topic: article
ms.date: 08/18/2016
ms.openlocfilehash: ccc9f709348d961e49bced00946658a6997837c0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "92368113"
---
# <a name="powershell-cmdlets-reference-for-azure-scheduler"></a>Azure Scheduler の PowerShell コマンドレット リファレンス

> [!IMPORTANT]
> [Azure Logic Apps](../logic-apps/logic-apps-overview.md) は、[廃止される予定](../scheduler/migrate-from-scheduler-to-logic-apps.md#retire-date)の Azure Scheduler の後継です。 Scheduler で設定したジョブを使用し続けるには、できるだけ早く [Azure Logic Apps に移行](../scheduler/migrate-from-scheduler-to-logic-apps.md)してください。 
>
> Scheduler は Azure portal で利用できなくなりましたが、現時点では [REST API](/rest/api/scheduler) と [Azure Scheduler PowerShell コマンドレット](scheduler-powershell-reference.md)がまだ使用できるので、お客様はジョブとジョブ コレクションを管理することができます。

Scheduler ジョブとジョブ コレクションを作成および管理するためのスクリプトを作成するために、PowerShell コマンドレットを使用できます。 この記事では、Azure Scheduler 用の主要な PowerShell コマンドレットと、その参考記事へのリンクを列挙します。 お使いの Azure サブスクリプションの Azure PowerShell をインストールするには、[Azure PowerShell をインストールして構成する方法](/powershell/azure/)を参照してください。 [Azure Resource Manager コマンドレット](/powershell/azure/)の詳細については、「[Azure Resource Manager での Azure PowerShell の使用](../azure-resource-manager/management/manage-resources-powershell.md)」を参照してください。

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

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

```text
Get-Help <cmdlet name> -Detailed
Get-Help <cmdlet name> -Examples
Get-Help <cmdlet name> -Full
```

## <a name="next-steps"></a>次のステップ

* [Azure Scheduler の概念、用語集、エンティティ階層構造](scheduler-concepts-terms.md)
* [Azure Scheduler の制限、既定値、エラー コード](scheduler-limits-defaults-errors.md)
* [Azure Scheduler REST API リファレンス](/rest/api/scheduler)