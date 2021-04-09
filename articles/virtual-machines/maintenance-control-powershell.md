---
title: PowerShell を使用した Azure 仮想マシンのメンテナンス コントロール
description: メンテナンス コントロールと PowerShell を使用して Azure VM にメンテナンスを適用するタイミングを制御する方法について学びます。
author: cynthn
ms.service: virtual-machines
ms.subservice: maintenance-control
ms.topic: how-to
ms.workload: infrastructure-services
ms.date: 11/19/2020
ms.author: cynthn
ms.openlocfilehash: 2868d559f0d848095fa7fec174e09e1b9376c4ae
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102552458"
---
# <a name="control-updates-with-maintenance-control-and-azure-powershell"></a>メンテナンス コントロールと Azure PowerShell による更新をコントロールする

メンテナンス コントロールを使用すると、分離された VM や Azure 専用ホストのホスト インフラストラクチャにプラットフォームの更新プログラムを適用するタイミングを決定できます。 このトピックでは、メンテナンス コントロール用の Azure PowerShell オプションについて説明します。 メンテナンス コントロールを使用する利点、その制限、およびその他の管理オプションの詳細については、「[メンテナンス コントロールを使用したプラットフォーム更新プログラムの管理](maintenance-control.md)」を参照してください。
 
## <a name="enable-the-powershell-module"></a>PowerShell モジュールを有効にする

`PowerShellGet` が最新の状態であることを確認します。    

```azurepowershell-interactive  
Install-Module -Name PowerShellGet -Repository PSGallery -Force 
``` 

`Az.Maintenance` PowerShell モジュールをインストールします。     

```azurepowershell-interactive  
Install-Module -Name Az.Maintenance
``` 

ローカルにインストールする場合は、管理者として、PowerShell プロンプトを開いてください。

*信頼されていないリポジトリ* からインストールするかどうかを確認するメッセージが表示される場合もあります。 モジュールをインストールするには、`Y` を入力するか、 **[すべてはい]** を選択します。


## <a name="create-a-maintenance-configuration"></a>メンテナンス構成を作成する

構成のコンテナーとして、リソースグループを作成します。 この例では、*myMaintenanceRG* という名前のリソース グループが、*eastus* に作成されます。 使用するリソース グループが既にある場合は、この部分をスキップし、残りの例のリソース グループ名を自分の所有者に置き換えることができます。

```azurepowershell-interactive
New-AzResourceGroup `
   -Location eastus `
   -Name myMaintenanceRG
```

[New-AzMaintenanceConfiguration](/powershell/module/az.maintenance/new-azmaintenanceconfiguration) を使用して、メンテナンス構成を作成します。 この例では、ホストを対象とした *myConfig* という名前のメンテナンス構成を作成します。 

```azurepowershell-interactive
$config = New-AzMaintenanceConfiguration `
   -ResourceGroup myMaintenanceRG `
   -Name myConfig `
   -MaintenanceScope host `
   -Location  eastus
```

`-MaintenanceScope host` を使用して、そのメンテナンス構成が、ホストに対する更新をコントロールするために確実に使用されているか確認します。

同じ名前の構成を別の場所に作成しようとすると、エラーが発生します。 構成名は、リソース グループに対して一意である必要があります。

[Get-AzMaintenanceConfiguration](/powershell/module/az.maintenance/get-azmaintenanceconfiguration) を使用して、使用可能なメンテナンス構成に対してクエリを実行できます。

```azurepowershell-interactive
Get-AzMaintenanceConfiguration | Format-Table -Property Name,Id
```

### <a name="create-a-maintenance-configuration-with-scheduled-window"></a>日程計画された期間でメンテナンス構成を作成する

Azure でリソースに更新プログラムを適用する日程計画された期間を宣言することもできます。 この例では、毎月第 4 月曜日に 5 時間という日程計画で myConfig という名前のメンテナンス構成が作成されます。 日程計画された期間を作成すると、更新プログラムを手動で適用する必要がなくなります。

```azurepowershell-interactive
$config = New-AzMaintenanceConfiguration `
   -ResourceGroup $RGName `
   -Name $MaintenanceConfig `
   -MaintenanceScope Host `
   -Location $location `
   -StartDateTime "2020-10-01 00:00" `
   -TimeZone "Pacific Standard Time" `
   -Duration "05:00" `
   -RecurEvery "Month Fourth Monday"
```
> [!IMPORTANT]
> メンテナンスの **期間** は、"*2 時間*" 以上である必要があります。 メンテナンスの **繰り返し** は少なくとも 35 日間に 1 回に行われるように設定する必要があります。

メンテナンスの **繰り返し** は、日、週、月単位で表すことができます。 いくつかの例を次に示します。
 - **毎日** - RecurEvery "Day" **または** "3Days" 
 - **毎週** - RecurEvery "3Weeks" **または** "Week Saturday,Sunday" 
 - **毎月** - - RecurEvery "Month day23,day24" **または** "Month Last Sunday" **または** "Month Fourth Monday"  
      

## <a name="assign-the-configuration"></a>構成を割り当てる

[New-AzConfigurationAssignment](/powershell/module/az.maintenance/new-azconfigurationassignment) を使用して、分離された VM または Azure Dedicated Host に構成を割り当てます。

### <a name="isolated-vm"></a>分離された VM

構成の ID を使用して、構成を VM に適用します。 `-ResourceType VirtualMachines` を指定し、`-ResourceName` には VM の名前、`-ResourceGroupName` には VM のリソース グループを指定します。 

```azurepowershell-interactive
New-AzConfigurationAssignment `
   -ResourceGroupName myResourceGroup `
   -Location eastus `
   -ResourceName myVM `
   -ResourceType VirtualMachines `
   -ProviderName Microsoft.Compute `
   -ConfigurationAssignmentName $config.Name `
   -MaintenanceConfigurationId $config.Id
```

### <a name="dedicated-host"></a>専用ホスト

専用ホストに構成を適用するには、`-ResourceType hosts` と `-ResourceParentName` を含める必要もあります。これらには、ホスト グループの名前と `-ResourceParentType hostGroups` を使用します。 


```azurepowershell-interactive
New-AzConfigurationAssignment `
   -ResourceGroupName myResourceGroup `
   -Location eastus `
   -ResourceName myHost `
   -ResourceType hosts `
   -ResourceParentName myHostGroup `
   -ResourceParentType hostGroups `
   -ProviderName Microsoft.Compute `
   -ConfigurationAssignmentName $config.Name `
   -MaintenanceConfigurationId $config.Id
```

## <a name="check-for-pending-updates"></a>保留中の更新プログラムを確認する

[Get-AzMaintenanceUpdat](/powershell/module/az.maintenance/get-azmaintenanceupdate) を使用して、保留中の更新プログラムがあるかどうかを確認します。 ログインしている VM と異なる場合は、`-subscription` を使用して、VM の Azure サブスクリプションを指定します。

表示する更新プログラムがない場合、このコマンドは何も返しません。 それ以外の場合は、PSApplyUpdate オブジェクトが返されます。

```json
{
   "maintenanceScope": "Host",
   "impactType": "Freeze",
   "status": "Pending",
   "impactDurationInSec": 9,
   "notBefore": "2020-02-21T16:47:44.8728029Z",
   "properties": {
      "resourceId": "/subscriptions/39c6cced-4d6c-4dd5-af86-57499cd3f846/resourcegroups/Ignite2019/providers/Microsoft.Compute/virtualMachines/MCDemo3"
} 
```

### <a name="isolated-vm"></a>分離された VM

分離された VM の保留中の更新プログラムを確認します。 この例では、読みやすくするために出力を表形式で表示しています。

```azurepowershell-interactive
Get-AzMaintenanceUpdate `
  -ResourceGroupName myResourceGroup `
  -ResourceName myVM `
  -ResourceType VirtualMachines `
  -ProviderName Microsoft.Compute | Format-Table
```


### <a name="dedicated-host"></a>専用ホスト

専用ホストの保留中の更新プログラムを確認します。 この例では、読みやすくするために出力を表形式で表示しています。 リソースの値は実際の値に置き換えてください。

```azurepowershell-interactive
Get-AzMaintenanceUpdate `
   -ResourceGroupName myResourceGroup `
   -ResourceName myHost `
   -ResourceType hosts `
   -ResourceParentName myHostGroup `
   -ResourceParentType hostGroups `
   -ProviderName Microsoft.Compute | Format-Table
```


## <a name="apply-updates"></a>更新プログラムの適用

[New-AzApplyUpdate](/powershell/module/az.maintenance/new-azapplyupdate) を使用して、保留中の更新プログラムを適用します。

### <a name="isolated-vm"></a>分離された VM

分離された VM に更新プログラムを適用する要求を作成します。

```azurepowershell-interactive
New-AzApplyUpdate `
   -ResourceGroupName myResourceGroup `
   -ResourceName myVM `
   -ResourceType VirtualMachines `
   -ProviderName Microsoft.Compute
```

成功した場合、このコマンドでは `PSApplyUpdate` オブジェクトが返されます。 `Get-AzApplyUpdate` コマンドで Name 属性を指定して、更新プログラムの状態を確認できます。 「[更新プログラムの状態の確認](#check-update-status)」をご覧ください。

### <a name="dedicated-host"></a>専用ホスト

専用ホストに更新プログラムを適用します。

```azurepowershell-interactive
New-AzApplyUpdate `
   -ResourceGroupName myResourceGroup `
   -ResourceName myHost `
   -ResourceType hosts `
   -ResourceParentName myHostGroup `
   -ResourceParentType hostGroups `
   -ProviderName Microsoft.Compute
```

## <a name="check-update-status"></a>更新プログラムの状態の確認
更新プログラムの状態を確認するには、[Get-AzApplyUpdate](/powershell/module/az.maintenance/get-azapplyupdate) を使用します。 次に示すコマンドは、`-ApplyUpdateName` パラメーターに `default` を使用して、最新の更新プログラムの状態を表示します。 更新プログラムの名前を置き換えて ([New-AzApplyUpdate](/powershell/module/az.maintenance/new-azapplyupdate) コマンドによって返されます)、特定の更新プログラムの状態を取得できます。

```text
Status         : Completed
ResourceId     : /subscriptions/12ae7457-4a34-465c-94c1-17c058c2bd25/resourcegroups/TestShantS/providers/Microsoft.Comp
ute/virtualMachines/DXT-test-04-iso
LastUpdateTime : 1/1/2020 12:00:00 AM
Id             : /subscriptions/12ae7457-4a34-465c-94c1-17c058c2bd25/resourcegroups/TestShantS/providers/Microsoft.Comp
ute/virtualMachines/DXT-test-04-iso/providers/Microsoft.Maintenance/applyUpdates/default
Name           : default
Type           : Microsoft.Maintenance/applyUpdates
```
LastUpdateTime は、セルフ メンテナンス期間を使用せずにユーザーまたはプラットフォームが開始した、更新が完了した時刻になります。 今までメンテナンス コントロールによって更新プログラムが適用されたことがない場合は、既定値が表示されます。

### <a name="isolated-vm"></a>分離された VM

特定の仮想マシンの更新プログラムを確認します。

```azurepowershell-interactive
Get-AzApplyUpdate `
   -ResourceGroupName myResourceGroup `
   -ResourceName myVM `
   -ResourceType VirtualMachines `
   -ProviderName Microsoft.Compute `
   -ApplyUpdateName default
```

### <a name="dedicated-host"></a>専用ホスト

専用ホストの更新プログラムを確認します。

```azurepowershell-interactive
Get-AzApplyUpdate `
   -ResourceGroupName myResourceGroup `
   -ResourceName myHost `
   -ResourceType hosts `
   -ResourceParentName myHostGroup `
   -ResourceParentType hostGroups `
   -ProviderName Microsoft.Compute `
   -ApplyUpdateName myUpdateName
```

## <a name="remove-a-maintenance-configuration"></a>メンテナンスの構成を削除する

[Remove-AzMaintenanceConfiguration](/powershell/module/az.maintenance/remove-azmaintenanceconfiguration) を使用して、メンテナンス構成を削除します。

```azurepowershell-interactive
Remove-AzMaintenanceConfiguration `
   -ResourceGroupName myResourceGroup `
   -Name $config.Name
```

## <a name="next-steps"></a>次のステップ
詳細については、[メンテナンスと更新プログラム](maintenance-and-updates.md)に関するページを参照してください。
