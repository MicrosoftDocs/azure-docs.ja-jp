---
title: PowerShell を使用した Azure 仮想マシンのメンテナンス コントロール
description: メンテナンス コントロールと PowerShell を使用して Azure VM にメンテナンスを適用するタイミングを制御する方法について学びます。
services: virtual-machines-linux
author: cynthn
ms.service: virtual-machines
ms.topic: article
ms.tgt_pltfrm: vm
ms.workload: infrastructure-services
ms.date: 12/06/2019
ms.author: cynthn
ms.openlocfilehash: e7a5f9ba865ab555bde3125f40ee8675709bef40
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/08/2019
ms.locfileid: "74932256"
---
# <a name="preview-control-updates-with-maintenance-control-and-azure-powershell"></a>プレビュー:メンテナンス コントロールと Azure PowerShell による更新をコントロールする

メンテナンス コントロールを使用して、再起動が不要なプラットフォームの更新を管理します。 Azure は、信頼性、パフォーマンス、セキュリティを改善し、新機能を導入する目的で、インフラストラクチャを頻繁に更新しています。 ほとんどの更新は、ユーザーからは透過的に行われます。 ゲーム、メディア ストリーミング、金融取引などの一部のセンシティブなワークロードでは、数秒間であっても、メンテナンスのために VM がフリーズしたり切断したりすることが許されません。 メンテナンス コントロールが提供するオプションを使用すると、プラットフォームの更新を待機し、35 日間のローリング期間内にそれらの更新を適用できます。 

メンテナンス コントロールを使用すると、分離された VM に更新プログラムを適用するタイミングをユーザーが決定できます。

メンテナンス コントロールを使用すると、次のことができます。
- 更新プログラムを 1 つの更新プログラム パッケージにまとめる。
- 最大 35 日間待機して更新プログラムを適用する。 
- Azure Functions を使用して、メンテナンス期間のプラットフォームの更新を自動化する。
- メンテナンス構成が、複数のサブスクリプションやリソース グループ全体で機能するようにする。 

> [!IMPORTANT]
> メンテナンス コントロールは、現在パブリック プレビューの段階です。
> このプレビュー バージョンはサービス レベル アグリーメントなしで提供されています。運用環境のワークロードに使用することはお勧めできません。 特定の機能はサポート対象ではなく、機能が制限されることがあります。 詳しくは、[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)に関するページをご覧ください。
> 

## <a name="limitations"></a>制限事項

- VM は、[専用ホスト](./linux/dedicated-hosts.md)上にあるか、[分離された VM サイズ](./linux/isolation.md)を使用して作成される必要があります。
- 35日後に、更新が自動的に適用され、可用性の制約は尊重されなくなります。
- ユーザーは、**リソース所有者**のアクセス権を持っている必要があります。


## <a name="enable-the-powershell-module"></a>PowerShell モジュールを有効にする

`PowerShellGet` が最新の状態であることを確認します。

```azurepowershell-interactive
Install-Module -Name PowerShellGet -Repository PSGallery -Force
```

Az. Maintenance PowerShell コマンドレットは、プレビュー段階であるため、Cloud Shell またはローカルの PowerShell インストールで `AllowPrerelease` パラメーターを指定して、モジュールをインストールする必要があります。   

```azurepowershell-interactive
Install-Module -Name Az.Maintenance -AllowPrerelease
```

ローカルにインストールする場合は、管理者として、PowerShell プロンプトを開いてください。

*信頼されていないリポジトリ*からインストールするかどうかを確認するメッセージが表示される場合もあります。 モジュールをインストールするには、`Y` を入力するか、 **[すべてはい]** を選択します。



## <a name="create-a-maintenance-configuration"></a>メンテナンス構成を作成する

構成のコンテナーとして、リソースグループを作成します。 この例では、*myMaintenanceRG*という名前のリソース グループが、*eastus* に作成されます。 使用するリソース グループが既にある場合は、この部分をスキップし、残りの例のリソース グループ名を自分の所有者に置き換えることができます。

```azurepowershell-interactive
New-AzResourceGroup `
   -Location eastus `
   -Name myMaintenanceRG
```

[New-AzMaintenanceConfiguration](https://docs.microsoft.com/powershell/module/az.maintenance/new-azmaintenanceconfiguration) を使用して、メンテナンス構成を作成します。 この例では、ホストを対象とした *myConfig* という名前のメンテナンス構成を作成します。 

```azurepowershell-interactive
$config = New-AzMaintenanceConfiguration `
   -ResourceGroup myMaintenanceRG `
   -Name myConfig `
   -MaintenanceScope host `
   -Location  eastus
```

`-MaintenanceScope host` を使用して、そのメンテナンス構成が、ホストに対する更新をコントロールするために確実に使用されているか確認します。

同じ名前の構成を別の場所に作成しようとすると、エラーが発生します。 構成名は、サブスクリプションに対して一意である必要があります。

[Get-AzMaintenanceConfiguration](https://docs.microsoft.com/powershell/module/az.maintenance/get-azmaintenanceconfiguration) を使用して、使用可能なメンテナンス構成に対してクエリを実行できます。

```azurepowershell-interactive
Get-AzMaintenanceConfiguration | Format-Table -Property Name,Id
```

## <a name="assign-the-configuration"></a>構成を割り当てる

[New-AzConfigurationAssignment](https://docs.microsoft.com/powershell/module/az.maintenance/new-azconfigurationassignment) を使用して、分離された VM または Azure Dedicated Host に構成を割り当てます。

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

### <a name="dedicate-host"></a>専用のホスト

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

[Get-AzMaintenanceUpdat](https://docs.microsoft.com/powershell/module/az.maintenance/get-azmaintenanceupdate) を使用して、保留中の更新プログラムがあるかどうかを確認します。 ログインしている VM と異なる場合は、`-subscription` を使用して、VM の Azure サブスクリプションを指定します。 

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

## <a name="apply-updates"></a>更新プログラムを適用する

[New-AzApplyUpdate](https://docs.microsoft.com/powershell/module/az.maintenance/new-azapplyupdate) を使用して、保留中の更新プログラムを適用します。

### <a name="isolated-vm"></a>分離された VM

分離された VM に更新プログラムを適用する要求を作成します。

```azurepowershell-interactive
New-AzApplyUpdate `
   -ResourceGroupName myResourceGroup `
   -ResourceName myVM `
   -ResourceType VirtualMachines `
   -ProviderName Microsoft.Compute
```

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

## <a name="remove-a-maintenance-configuration"></a>メンテナンスの構成を削除する

[Remove-AzMaintenanceConfiguration](https://docs.microsoft.com/powershell/module/az.maintenance/remove-azmaintenanceconfiguration) を使用して、メンテナンス構成を削除します。

```azurecli-interactive
Remove-AzMaintenanceConfiguration `
   -ResourceGroupName myResourceGroup `
   -Name $config.Name
```

## <a name="next-steps"></a>次の手順
詳細については、[メンテナンスと更新プログラム](maintenance-and-updates.md)に関するページを参照してください。
