---
title: "Automation アカウントとリソースを移行する | Microsoft Docs"
description: "この記事では、Azure Automation の Automation アカウントとそれに関連付けられているリソースをサブスクリプション間で移動する方法について説明します。"
services: automation
documentationcenter: 
author: MGoedtel
manager: jwhit
editor: tysonn
ms.assetid: 9c2db4a2-f324-48dc-8ce7-3343bf7230d5
ms.service: automation
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/21/2016
ms.author: magoedte
translationtype: Human Translation
ms.sourcegitcommit: 4fbfb24a2e9d55d718902d468bd25e12f64e7d24
ms.openlocfilehash: 687da15bdaf854254321b59350f47549781676f5


---
# <a name="migrate-automation-account-and-resources"></a>Automation アカウントとリソースを移行する
Azure ポータルで作成した、また、あるリソース グループまたはサブスクリプションから別のリソース グループまたはサブスクリプションに移行する必要のある Automation アカウントとそれに関連付けられたリソース (つまり、資産、Runbook、モジュールなど) については、Azure ポータルで利用できる[リソースの移動](../azure-resource-manager/resource-group-move-resources.md)機能で、移行を簡単に達成できます。 ただし、この操作を行う前に、[リソース移動前のチェックリスト](../azure-resource-manager/resource-group-move-resources.md#checklist-before-moving-resources)を確認した後、Automation に固有の次のリストもチェックする必要があります。   

1. 移動先のサブスクリプション/リソース グループは、移動元と同じリージョン内に存在する必要があります。  つまり、Automation アカウントは、リージョン間で移動することはできません。
2. リソース (Runbook やジョブなど) を移動する場合は、その操作の間、ソース グループとターゲット グループの両方がロックされます。 これらのグループに対する書き込み操作および削除操作は、移動が完了するまでブロックされます。  
3. 既存のサブスクリプションからリソースまたはサブスクリプション ID を参照する Runbook または変数は、移行が完了した後に更新する必要があります。   

> [!NOTE]
> この機能は、クラシック オートメーション リソースの移動をサポートしていません。
>
>

## <a name="to-move-the-automation-account-using-the-portal"></a>ポータルを使用して Automation アカウントを移動するには
1. 自分の Automation アカウントで、ブレードの上部にある **[移動]** をクリックします。<br> ![[移動] オプション](media/automation-migrate-account-subscription/automation-menu-move.png)<br>
2. **[リソースの移動]** ブレードに、Automation アカウントとリソース グループの両方に関連するリソースが表示されます。  ドロップダウン リストから**サブスクリプション**と**リソース グループ**を選択するか、オプション **[新しいリソース グループの作成]** を選択し、指定されたフィールドに新しいリソース グループの名前を入力します。  
3. *[リソースの移動後に新しいリソース ID を使用するにはツールとスクリプトの更新が必要であることを理解しました]* チェックボックスをオンにし、**[OK]** をクリックします。<br> ![[リソースの移動] ブレード](media/automation-migrate-account-subscription/automation-move-resources-blade.png)<br>   

この操作は、完了までに数分かかります。  **[通知]**に、実行されている操作の状態が表示され、検証と移行の実行後に操作が完了します。     

## <a name="to-move-the-automation-account-using-powershell"></a>PowerShell を使用して Automation アカウントを移動するには
既存の Automation リソースを他のリソース グループまたはサブスクリプションに移動するには、**Get-AzureRmResource** コマンドレットを使用して特定の Automation アカウントを取得してから、**Move-AzureRmResource** コマンドレットを使用して移動を実行します。

最初の例では、Automation アカウントを新しいリソース グループに移動する方法を示します。

   ```
    $resource = Get-AzureRmResource -ResourceName "TestAutomationAccount" -ResourceGroupName "ResourceGroup01"
    Move-AzureRmResource -ResourceId $resource.ResourceId -DestinationResourceGroupName "NewResourceGroup"
   ```

上のコード例を実行すると、この操作を実行することの確認を求められます。  **[はい]** をクリックしてスクリプトの続行を許可した後、移行実行中は通知を受信しません。  

新しいサブスクリプションに移動する場合は、 *DestinationSubscriptionId* パラメーターの値を含めます。

   ```
    $resource = Get-AzureRmResource -ResourceName "TestAutomationAccount" -ResourceGroupName "ResourceGroup01"
    Move-AzureRmResource -ResourceId $resource.ResourceId -DestinationResourceGroupName "NewResourceGroup" -DestinationSubscriptionId "SubscriptionId"
   ```

前の例と同じように、移動することの確認を求められます。  

## <a name="next-steps"></a>次のステップ
* 新しいリソース グループまたはサブスクリプションへのリソースの移動については、「[新しいリソース グループまたはサブスクリプションへのリソースの移動](../azure-resource-manager/resource-group-move-resources.md)」を参照してください。
* Azure Automation におけるロールベースのアクセス制御の詳細については、「 [Azure Automation におけるロールベースのアクセス制御](automation-role-based-access-control.md)」を参照してください。
* サブスクリプションを管理するための PowerShell コマンドレットについては、「 [Resource Manager での Azure PowerShell の使用](../azure-resource-manager/powershell-azure-resource-manager.md)
* サブスクリプションを管理するためのポータル機能については、「 [Azure ポータルを使用したリソース管理](../azure-resource-manager/resource-group-portal.md)」を参照してください。



<!--HONumber=Dec16_HO3-->


