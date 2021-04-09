---
title: Azure PowerShell を使用して仮想マシン スケール セットを管理する
description: 仮想マシン スケール セットを管理するための一般的な Azure PowerShell コマンドレット (インスタンスを起動および停止したり、スケール セットの容量を変更したりする方法など)。
author: ju-shim
ms.author: jushiman
ms.topic: how-to
ms.service: virtual-machine-scale-sets
ms.subservice: management
ms.date: 05/29/2018
ms.reviewer: mimckitt
ms.custom: mimckitt, devx-track-azurepowershell
ms.openlocfilehash: eee4dd7fae872f6b3ddd01f60aba732edc170766
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "91570574"
---
# <a name="manage-a-virtual-machine-scale-set-with-azure-powershell"></a>Azure PowerShell を使用して仮想マシン スケール セットを管理する

仮想マシン スケール セットのライフサイクルを通して、1 つ以上の管理タスクを実行することが必要になる場合があります。 さらに、各種ライフサイクルのタスクを自動化するスクリプトを作成するほうが便利な場合もあります。 この記事では、これらのタスクを実行するために使用できる一般的な Azure PowerShell コマンドレットのいくつかについて詳細に説明します。

仮想マシン スケール セットを作成する必要がある場合は、[Azure PowerShell でスケール セットを作成](quick-create-powershell.md)できます。

[!INCLUDE [updated-for-az.md](../../includes/updated-for-az.md)]

## <a name="view-information-about-a-scale-set"></a>スケール セットに関する情報を表示する
スケール セットに関する全体的な情報を表示するには、[Get-AzVmss](/powershell/module/az.compute/get-azvmss) を使用します。 次の例では、*myResourceGroup* リソース グループ内の *myScaleSet* という名前のスケール セットに関する情報を取得します。 独自の名前を次のように入力します。

```powershell
Get-AzVmss -ResourceGroupName "myResourceGroup" -VMScaleSetName "myScaleSet"
```


## <a name="view-vms-in-a-scale-set"></a>スケール セットの VM を表示する
スケール セット内の VM インスタンスの一覧を表示するには、[Get-AzVmssVM](/powershell/module/az.compute/get-azvmssvm) を使用します。 次の例では、*myScaleSet* という名前のスケール セットおよび *myResourceGroup* リソース グループ内のすべての VM インスタンスを一覧表示します。 これらの名前には独自の値を指定します。

```powershell
Get-AzVmssVM -ResourceGroupName "myResourceGroup" -VMScaleSetName "myScaleSet"
```

特定の VM インスタンスに関する追加情報を表示するには、[Get-AzVmssVM](/powershell/module/az.compute/get-azvmssvm) に `-InstanceId` パラメーターを追加し、表示するインスタンスを指定します。 次の例では、*myScaleSet* という名前のスケール セットおよび *myResourceGroup* リソース グループ内の VM インスタンス *0* に関する情報を表示します。 独自の名前を次のように入力します。

```powershell
Get-AzVmssVM -ResourceGroupName "myResourceGroup" -VMScaleSetName "myScaleSet" -InstanceId "0"
```

また、1 回の API 呼び出しですべてのインスタンスに対する詳細な *instanceView* 情報を取得することもできます。これは、大規模なインストールで API が調整されるのを回避するのに役立ちます。

```powershell
Get-AzVmssVM -InstanceView -ResourceGroupName "myResourceGroup" -VMScaleSetName "myScaleSet"
```

```rest
GET "https://management.azure.com/subscriptions/<sub-id>/resourceGroups/<resourceGroupName>/providers/Microsoft.Compute/virtualMachineScaleSets/<VMSSName>/virtualMachines?api-version=2019-03-01&%24expand=instanceView"
```

## <a name="change-the-capacity-of-a-scale-set"></a>スケール セットの容量を変更する
前のコマンドでは、スケール セットと VM インスタンスに関する情報を表示しました。 スケール セット内のインスタンスの数を増やすか、または減らすために、その容量を変更できます。 スケール セットは、必要な数の VM を自動的に作成または削除した後、アプリケーション トラフィックを受信するようにそれらの VM を構成します。

最初に、[Get-AzVmss](/powershell/module/az.compute/get-azvmss) を使用してスケール セット オブジェクトを作成し、次に `sku.capacity` に新しい値を指定します。 容量の変更を適用するには、[Update-AzVmss](/powershell/module/az.compute/update-azvmss) を使用します。 次の例では、*myResourceGroup* リソース グループ内の *myScaleSet* を *5* インスタンスの容量に更新します。 独自の値を次のように指定します。

```powershell
# Get current scale set
$vmss = Get-AzVmss -ResourceGroupName "myResourceGroup" -VMScaleSetName "myScaleSet"

# Set and update the capacity of your scale set
$vmss.sku.capacity = 5
Update-AzVmss -ResourceGroupName "myResourceGroup" -Name "myScaleSet" -VirtualMachineScaleSet $vmss
```

スケール セットの容量を更新するには数分かかります。 スケール セットの容量を減らした場合は、最も大きなインスタンス ID を持つ VM が最初に削除されます。


## <a name="stop-and-start-vms-in-a-scale-set"></a>スケール セット内の VM を停止および起動する
スケール セット内の 1 つ以上の VM を停止するには、[Stop-AzVmss](/powershell/module/az.compute/stop-azvmss) を使用します。 `-InstanceId` パラメーターでは、停止する VM を 1 つ以上指定できます。 インスタンス ID を指定しない場合は、スケール セット内のすべての VM が停止されます。 複数の VM を停止するには、各インスタンス ID をコンマで区切ります。

次の例では、*myScaleSet* という名前のスケール セットおよび *myResourceGroup* リソース グループ内のインスタンス *0* を停止します。 独自の値を次のように指定します。

```powershell
Stop-AzVmss -ResourceGroupName "myResourceGroup" -VMScaleSetName "myScaleSet" -InstanceId "0"
```

既定では、停止された VM は割り当て解除されるため、コンピューティング料金は発生しません。 停止されたときに VM をプロビジョニングされた状態のままにする場合は、前のコマンドに `-StayProvisioned` パラメーターを追加します。 停止された VM がプロビジョニングされたままになる場合は、通常のコンピューティング料金が発生します。


### <a name="start-vms-in-a-scale-set"></a>スケール セット内の VM を起動する
スケール セット内の 1 つ以上の VM を起動するには、[Start-AzVmss](/powershell/module/az.compute/start-azvmss) を使用します。 `-InstanceId` パラメーターでは、起動する VM を 1 つ以上指定できます。 インスタンス ID を指定しない場合は、スケール セット内のすべての VM が起動されます。 複数の VM を起動するには、各インスタンス ID をコンマで区切ります。

次の例では、*myScaleSet* という名前のスケール セットおよび *myResourceGroup* リソース グループ内のインスタンス *0* を起動します。 独自の値を次のように指定します。

```powershell
Start-AzVmss -ResourceGroupName "myResourceGroup" -VMScaleSetName "myScaleSet" -InstanceId "0"
```


## <a name="restart-vms-in-a-scale-set"></a>スケール セット内の VM を再起動する
スケール セット内の 1 つ以上の VM を再起動するには、[Restart-AzVmss](/powershell/module/az.compute/restart-azvmss) を使用します。 `-InstanceId` パラメーターでは、再起動する VM を 1 つ以上指定できます。 インスタンス ID を指定しない場合は、スケール セット内のすべての VM が再起動されます。 複数の VM を再起動するには、各インスタンス ID をコンマで区切ります。

次の例では、*myScaleSet* という名前のスケール セットおよび *myResourceGroup* リソース グループ内のインスタンス *0* を再起動します。 独自の値を次のように指定します。

```powershell
Restart-AzVmss -ResourceGroupName "myResourceGroup" -VMScaleSetName "myScaleSet" -InstanceId "0"
```


## <a name="remove-vms-from-a-scale-set"></a>スケール セットから VM を削除する
スケール セット内の 1 つ以上の VM を削除するには、[Remove-AzVmss](/powershell/module/az.compute/remove-azvmss) を使用します。 `-InstanceId` パラメーターでは、削除する VM を 1 つ以上指定できます。 インスタンス ID を指定しない場合は、スケール セット内のすべての VM が削除されます。 複数の VM を削除するには、各インスタンス ID をコンマで区切ります。

次の例では、*myScaleSet* という名前のスケール セットおよび *myResourceGroup* リソース グループ内のインスタンス *0* を削除します。 独自の値を次のように指定します。

```powershell
Remove-AzVmss -ResourceGroupName "myResourceGroup" -VMScaleSetName "myScaleSet" -InstanceId "0"
```


## <a name="next-steps"></a>次のステップ
スケール セットに対する他の一般的なタスクとして、[アプリケーションのデプロイ](virtual-machine-scale-sets-deploy-app.md)や [VM インスタンスのアップグレード](virtual-machine-scale-sets-upgrade-scale-set.md)があります。 また、Azure PowerShell を使用して、[自動スケールの規則を構成する](virtual-machine-scale-sets-autoscale-overview.md)こともできます。
