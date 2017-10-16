---
title: "Azure での Windows VM のメンテナンス通知の処理 | Microsoft Docs"
description: "Azure で実行されている Windows 仮想マシンのメンテナンス通知を表示し、セルフサービス メンテナンスを開始します。"
services: virtual-machines-windows
documentationcenter: 
author: zivraf
manager: timlt
editor: 
tags: azure-service-management,azure-resource-manager
ms.assetid: 
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 09/14/2017
ms.author: zivr
ms.openlocfilehash: 90129bee5771534b01ac12473c6cfed77840ae20
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="handling-planned-maintenance-notifications-for-windows-virtual-machines"></a>Windows 仮想マシンに対する計画済みメンテナンスの通知の処理

Azure は、定期的に更新を行い、仮想マシンのホスト インフラストラクチャの信頼性、パフォーマンス、セキュリティの向上に努めています。 更新とは、ホスティング環境の修正、ハードウェアのアップグレードや使用停止などの変更のことです。 これらの更新のほとんどは、ホストされている仮想マシンに影響を及ぼすことなく実行されます。 ただし、更新による影響が生じる場合もあります。

- Azure では、再起動を伴わないメンテナンスの場合、インプレース移行を使用して、ホストの更新中に VM を一時的に停止させます。

- 再起動を伴うメンテナンスの場合は、メンテナンスの予定日時が知らされます。 このような場合は、都合に応じて自分自身でメンテナンスを開始できる時間枠が与えられます。


再起動が必要な計画済みメンテナンスは、段階的にスケジュールされます。 各段階のスコープ (リージョン) はそれぞれ異なります。

- 段階はお客様への通知で始まります。 既定では、サブスクリプションの所有者と共同所有者に通知が送信されます。 通知の受信者は追加できます。また、電子メール、SMS、Webhook などのメッセージング オプションを通知に追加できます。  
- 通知後すぐに、セルフサービス期間が設定されます。 この期間中には、この段階に含まれている仮想マシンを確認し、プロアクティブな再デプロイを使用してメンテナンスを開始できます。 
- セルフサービス期間が過ぎると、予定メンテナンス期間が始まります。 この時点で、Azure は、仮想マシンに対して必要なメンテナンスをスケジュールし、適用します。 

2 つの期間を用意した目的は、Azure によってメンテナンスが自動的に開始される時期を把握した上で、メンテナンスを開始して仮想マシンを再起動するのに必要な時間を十分に確保できるようにすることにあります。


Azure ポータル、PowerShell、REST API、CLI を使用して、VM のメンテナンス期間のクエリを実行し、セルフサービス メンテナンスを開始することができます。

 > [!NOTE]
 > メンテナンスを試みたが失敗に終わった場合は、Azure によって VM に**スキップ済み**というマークが付けられ、予定メンテナンス期間中に VM は再起動されません。 その代わりに、後で、新しいスケジュールに関する連絡があります。 


[!INCLUDE [virtual-machines-common-maintenance-notifications](../../../includes/virtual-machines-common-maintenance-notifications.md)]

## <a name="check-maintenance-status-using-powershell"></a>PowerShell を使用してメンテナンスの状態を確認する

Azure Powershell を使用して、VM のメンテナンスの予定を確認することもできます。 計画済みメンテナンスに関する情報は、[Get AzureRmVM](/powershell/module/azurerm.compute/get-azurermvm) コマンドレットに `-status` パラメーターを指定することで取得できます。
 
計画済みメンテナンスがある場合にのみ、メンテナンス情報が返されます。 VM に影響を及ぼすメンテナンスがスケジュールされていない場合、コマンドレットはメンテナンス情報を返しません。 

```powershell
Get-AzureRmVM -ResourceGroupName rgName -Name vmName -Status
```

MaintenanceRedeployStatus では、次のプロパティが返されます。 
| 値 | Description   |
|-------|---------------|
| IsCustomerInitiatedMaintenanceAllowed | この時点で VM に対してメンテナンスを開始できるかどうかを示します。 ||
| PreMaintenanceWindowStartTime         | VM に対してメンテナンスを開始できる場合、メンテナンスのセルフサービス期間の始まりです。 ||
| PreMaintenanceWindowEndTime           | VM に対してメンテナンスを開始できる場合、メンテナンスのセルフサービス期間の終わりです。 ||
| MaintenanceWindowStartTime            | VM に対してメンテナンスを開始できる場合、予定メンテナンス期間の始まりです。 ||
| MaintenanceWindowEndTime              | VM に対してメンテナンスを開始できる場合、予定メンテナンス期間の終わりです。 ||
| LastOperationResultCode               | VM に対して最後にメンテナンスを試みたときの結果です。 ||



また、VM を指定しないで [Get AzureRmVM](/powershell/module/azurerm.compute/get-azurermvm) を実行すると、リソース グループ内のすべての VM のメンテナンス状態を取得することもできます。
 
```powershell
Get-AzureRmVM -ResourceGroupName rgName --Status
```

次の PowerShell 関数は、サブスクリプション ID を取り、メンテナンスがスケジュールされている VM の一覧を出力します。

```powershell

function MaintenanceIterator
{
    Select-AzureRmSubscription -SubscriptionId $args[0]

    $rgList= Get-AzureRmResourceGroup 

    for ($rgIdx=0; $rgIdx -lt $rgList.Length ; $rgIdx++)
    {
        $rg = $rgList[$rgIdx]
        $vmList = Get-AzureRMVM -ResourceGroupName $rg.ResourceGroupName 
        for ($vmIdx=0; $vmIdx -lt $vmList.Length ; $vmIdx++)
        {
            $vm = $vmList[$vmIdx]
            $vmDetails = Get-AzureRMVM -ResourceGroupName $rg.ResourceGroupName -Name $vm.Name -Status
              if ($vmDetails.MaintenanceRedeployStatus )
            {
                Write-Output "VM: $($vmDetails.Name)  IsCustomerInitiatedMaintenanceAllowed: $($vmDetails.MaintenanceRedeployStatus.IsCustomerInitiatedMaintenanceAllowed) $($vmDetails.MaintenanceRedeployStatus.LastOperationMessage)"               
            }
          }
    }
}

```

### <a name="start-maintenance-on-your-vm-using-powershell"></a>PowerShell を使用して VM に対するメンテナンスを開始する

**IsCustomerInitiatedMaintenanceAllowed** が true に設定されている場合、前のセクションの関数から取得した情報を使用して、次のように VM に対するメンテナンスを開始します。

```powershell
Restart-AzureRmVM -PerformMaintenance -name $vm.Name -ResourceGroupName $rg.ResourceGroupName 
```
 

## <a name="next-steps"></a>次のステップ

[スケジュールされたイベント](scheduled-events.md) を使用して VM でメンテナンス イベントを登録する方法を説明します。