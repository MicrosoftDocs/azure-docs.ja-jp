---
title: PowerShell を使用して Azure VM のメンテナンス通知を取得する
description: Azure で実行されている仮想マシンのメンテナンス通知を表示し、PowerShell を使用してセルフサービス メンテナンスを開始します。
author: shants123
ms.service: virtual-machines
ms.subservice: maintenance-control
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 11/19/2019
ms.author: shants
ms.openlocfilehash: 4278aa563d76b783e02e67ee964764864931fd68
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102552373"
---
# <a name="handling-planned-maintenance-using-powershell"></a>PowerShell を使用した計画メンテナンスの処理

**この記事は、Linux と Windows の両方を実行する仮想マシンに適用されます。**

Azure PowerShell を使用して、VM の[メンテナンス](maintenance-notifications.md)の予定を確認できます。 計画メンテナンスに関する情報は、[Get-AzVM](/powershell/module/az.compute/get-azvm) コマンドレットに `-status` パラメーターを指定することで取得できます。
  
計画メンテナンスがある場合にのみ、メンテナンス情報が返されます。 VM に影響を及ぼすメンテナンスがスケジュールされていない場合、コマンドレットはメンテナンス情報を返しません。 


```powershell
Get-AzVM -ResourceGroupName myResourceGroup -Name myVM -Status
```

出力

```
MaintenanceRedeployStatus               : 
  IsCustomerInitiatedMaintenanceAllowed : True
  PreMaintenanceWindowStartTime         : 5/14/2018 12:30:00 PM
  PreMaintenanceWindowEndTime           : 5/19/2018 12:30:00 PM
  MaintenanceWindowStartTime            : 5/21/2018 4:30:00 PM
  MaintenanceWindowEndTime              : 6/4/2018 4:30
  LastOperationResultCode               : None 
```

MaintenanceRedeployStatus では、次のプロパティが返されます。 

| 値 | 説明   |
|-------|---------------|
| IsCustomerInitiatedMaintenanceAllowed | この時点で VM に対してメンテナンスを開始できるかどうかを示します。 |
| PreMaintenanceWindowStartTime         | VM に対してメンテナンスを開始できる場合、メンテナンスのセルフサービス期間の始まりです。 |
| PreMaintenanceWindowEndTime           | VM に対してメンテナンスを開始できる場合、メンテナンスのセルフサービス期間の終わりです。 |
| MaintenanceWindowStartTime            | Azure が VM に対してメンテナンスを開始する、予定メンテナンスの始まりです。 |
| MaintenanceWindowEndTime              | Azure が VM に対してメンテナンスを開始する、予定メンテナンス期間の終わりです。 |
| LastOperationResultCode               | VM に対して最後にメンテナンスを試みたときの結果です。 |



また、VM を指定しないで [Get-AzVM](/powershell/module/az.compute/get-azvm) を実行すると、リソース グループ内のすべての VM のメンテナンス状態を取得することもできます。
 
```powershell
Get-AzVM -ResourceGroupName myResourceGroup -Status
```

次の PowerShell の例は、サブスクリプション ID を取り、メンテナンスがスケジュールされている VM の一覧を返します。

```powershell

function MaintenanceIterator
{
    Select-AzSubscription -SubscriptionId $args[0]

    $rgList= Get-AzResourceGroup 

    for ($rgIdx=0; $rgIdx -lt $rgList.Length ; $rgIdx++)
    {
        $rg = $rgList[$rgIdx]        
    $vmList = Get-AzVM -ResourceGroupName $rg.ResourceGroupName 
        for ($vmIdx=0; $vmIdx -lt $vmList.Length ; $vmIdx++)
        {
            $vm = $vmList[$vmIdx]
            $vmDetails = Get-AzVM -ResourceGroupName $rg.ResourceGroupName -Name $vm.Name -Status
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
Restart-AzVM -PerformMaintenance -name $vm.Name -ResourceGroupName $rg.ResourceGroupName 
```

## <a name="classic-deployments"></a>クラシック デプロイ

[!INCLUDE [classic-vm-deprecation](../../includes/classic-vm-deprecation.md)]

クラシック デプロイ モデルを使用してデプロイされたレガシ VM がまだある場合は、PowerShell を使用して、VM を照会し、メンテナンスを開始できます。

VM のメンテナンスの状態を取得するには、次のように入力します。

```
Get-AzureVM -ServiceName <Service name> -Name <VM name>
```

クラシック VM のメンテナンスを開始するには、次のように入力します。

```
Restart-AzureVM -InitiateMaintenance -ServiceName <service name> -Name <VM name>
```

## <a name="next-steps"></a>次のステップ

[Azure CLI](maintenance-notifications-cli.md) または[ポータル](maintenance-notifications-portal.md)を使用して計画メンテナンスを処理することもできます。
