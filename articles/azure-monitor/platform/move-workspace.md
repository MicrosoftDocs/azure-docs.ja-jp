---
title: Azure Monitor で Log Analytics ワークスペースを移動する | Microsoft Docs
description: Log Analytics ワークスペースを別のサブスクリプションまたはリソース グループに移動する方法について説明します。
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 11/13/2019
ms.openlocfilehash: d3937eaa5017ed66641d886ecd45e812f7070b83
ms.sourcegitcommit: 37afde27ac137ab2e675b2b0492559287822fded
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/18/2020
ms.locfileid: "88566286"
---
# <a name="move-a-log-analytics-workspace-to-different-subscription-or-resource-group"></a>Log Analytics ワークスペースを別のサブスクリプションまたはリソース グループに移動する

この記事では、Log Analytics ワークスペースを同じリージョン内の別のリソース グループまたはサブスクリプションに移動する手順について説明します。 Azure portal、PowerShell、Azure CLI、または REST API を使用して Azure リソースを移動する方法について詳しくは、 「[リソースを新しいリソース グループまたはサブスクリプションに移動する](../../azure-resource-manager/management/move-resource-group-and-subscription.md)」をご覧ください。 

> [!IMPORTANT]
> ワークスペースを別のリージョンに移動することはできません。

## <a name="verify-active-directory-tenant"></a>Active Directory テナントを確認する
ワークスペースの移動元と移動先のサブスクリプションは、同じ Azure Active Directory テナント内に存在している必要があります。 Azure PowerShell を使用して、両方のサブスクリプションのテナント ID が同じであることを確認します。

``` PowerShell
(Get-AzSubscription -SubscriptionName <your-source-subscription>).TenantId
(Get-AzSubscription -SubscriptionName <your-destination-subscription>).TenantId
```

## <a name="workspace-move-considerations"></a>ワークスペースの移動に関する考慮事項
ワークスペースにインストールされているマネージド ソリューションは、Log Analytics ワークスペースの移動操作によって移動されます。 接続されたエージェントは接続されたままで、送信済みデータは移動後も引き続きワークスペースに保持されます。 移動操作では、ワークスペースからリンクされたサービスがあってはならないため、ワークスペースの移動を可能にするには、そのリンクに依存するソリューションを削除する必要があります。

自動アカウントのリンクを解除する前に削除する必要があるソリューションには、次のものがあります。

- 更新管理
- 変更の追跡
- 勤務時間外に VM を起動/停止する
- Azure Security Center

>[!IMPORTANT]
> **Azure Sentinel のお客様:**
> - Azure Sentinel がワークスペースにデプロイされた後に、そのワークスペースを他のリソース グループやサブスクリプションに移動することは、**現在はサポートされていません**。 
>
>   ワークスペースを既に移動している場合は、 **[Analytics]** の下のアクティブなルールをすべて無効にし、5 分後に再び有効にします。 これは、ほとんどの場合に効果的です。ただし、繰り返しますが、サポートされておらず、ご自身の責任で行ってください。

### <a name="delete-solutions-in-azure-portal"></a>Azure portal でソリューションを削除する
Azure portal を使用してソリューションを削除するには、次の手順を実行してください。

1. ソリューションがインストールされているリソース グループのメニューを開きます。
2. 削除するソリューションを選択します。
3. **[リソースの削除]** をクリックし、 **[削除]** をクリックして、削除されるリソースを確認します。

![ソリューションを削除する](media/move-workspace/delete-solutions.png)

### <a name="delete-using-powershell"></a>PowerShell を使用して削除する

PowerShell を使用してソリューションを削除するには、次の例に示すように、[Remove-AzResource](/powershell/module/az.resources/remove-azresource?view=azps-2.8.0) コマンドレットを使用します。

``` PowerShell
Remove-AzResource -ResourceType 'Microsoft.OperationsManagement/solutions' -ResourceName "ChangeTracking(<workspace-name>)" -ResourceGroupName <resource-group-name>
Remove-AzResource -ResourceType 'Microsoft.OperationsManagement/solutions' -ResourceName "Updates(<workspace-name>)" -ResourceGroupName <resource-group-name>
Remove-AzResource -ResourceType 'Microsoft.OperationsManagement/solutions' -ResourceName "Start-Stop-VM(<workspace-name>)" -ResourceGroupName <resource-group-name>
```

### <a name="remove-alert-rules-for-startstop-vms-solution"></a>Start/Stop VMs ソリューションのアラート ルールを削除する
**Start/Stop VMs** ソリューションを削除するには、このソリューションによって作成されたアラート ルールも削除する必要があります。 これらのルールを削除するには、Azure portal で次の手順を使用します。

1. **[監視]** メニューを開き、 **[アラート]** を選択します。
2. **[アラート ルールの管理]** をクリックします。
3. 次の 3 つのアラート ルールを選択し、 **[削除]** をクリックします。

   - AutoStop_VM_Child
   - ScheduledStartStop_Parent
   - SequencedStartStop_Parent

    ![規則の削除](media/move-workspace/delete-rules.png)

## <a name="unlink-automation-account"></a>Automation アカウントのリンクを解除する
Azure portal を使用してワークスペースから Automation アカウントのリンクを解除するには、次の手順に従います。

1. **[Automation アカウント]** メニューを開き、削除するアカウントを選択します。
2. メニューの **[関連リソース]** セクションで、 **[Linked workspace]\(リンクされたワークスペース\)** を選択します。 
3. **[ワークスペースのリンクを解除]** をクリックして、Automation アカウントからワークスペースのリンクを解除します。

    ![ワークスペースのリンクの解除](media/move-workspace/unlink-workspace.png)

## <a name="move-your-workspace"></a>ワークスペースを移動する

### <a name="azure-portal"></a>Azure portal
Azure portal を使用してワークスペースを移動するには、次の手順を実行してください。

1. **[Log Analytics ワークスペース]** メニューを開き、ワークスペースを選択します。
2. **[概要]** ページで、 **[リソース グループ]** または **[サブスクリプション]** の横の **[変更]** をクリックします。
3. 新しいページが開き、ワークスペースに関連するリソースの一覧が表示されます。 ワークスペースと同じ宛先サブスクリプションとリソース グループに移動するリソースを選択します。 
4. 移動先の **[サブスクリプション]** と **[リソース グループ]** を選択します。 同じサブスクリプション内の別のリソース グループにワークスペースを移動する場合は、 **[サブスクリプション]** オプションは表示されません。
5. **[OK]** をクリックして、ワークスペースと選択したリソースを移動します。

    ![ポータル](media/move-workspace/portal.png)

### <a name="powershell"></a>PowerShell
PowerShell を使用してワークスペースを移動するには、次の例のように [Move-AzResource](/powershell/module/AzureRM.Resources/Move-AzureRmResource) を使用します。

``` PowerShell
Move-AzResource -ResourceId "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/MyResourceGroup01/providers/Microsoft.OperationalInsights/workspaces/MyWorkspace" -DestinationSubscriptionId "00000000-0000-0000-0000-000000000000" -DestinationResourceGroupName "MyResourceGroup02"
```

> [!IMPORTANT]
> 移動操作後、削除されたソリューションと Automation アカウントのリンクを再構成して、ワークスペースを前の状態に戻す必要があります。


## <a name="next-steps"></a>次のステップ
- 移動をサポートしているリソースのリストは、「[Move operation support for resources](../../azure-resource-manager/management/move-support-resources.md)」(リソースの移動操作のサポート) を参照してください。
