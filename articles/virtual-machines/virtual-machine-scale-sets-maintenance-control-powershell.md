---
title: PowerShell を使用して Azure 仮想マシン スケール セットで OS イメージをアップグレードするためのメンテナンス コントロール
description: メンテナンス コントロールと PowerShell を使用して、OS イメージの自動アップグレードが Azure 仮想マシン スケール セットにロール アウトされるタイミングを制御する方法について説明します。
author: ju-shim
ms.service: virtual-machine-scale-sets
ms.topic: how-to
ms.workload: infrastructure-services
ms.date: 09/11/2020
ms.author: jushiman
ms.openlocfilehash: d8acab17e9d8dfc078b46f6a279cc671a70b0a50
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "91974840"
---
# <a name="preview-maintenance-control-for-os-image-upgrades-on-azure-virtual-machine-scale-sets-using-powershell"></a>プレビュー:PowerShell を使用して Azure 仮想マシン スケール セットで OS イメージをアップグレードするためのメンテナンス コントロール

メンテナンス コントロールを使用すると、ゲスト OS イメージの自動アップグレードを仮想マシン スケール セットに適用するタイミングを決定できます。 このトピックでは、メンテナンス コントロール用の Azure PowerShell オプションについて説明します。 メンテナンス コントロールの使用方法の詳細については、「[Azure 仮想マシン スケール セットのメンテナンス コントロール](virtual-machine-scale-sets-maintenance-control.md)」を参照してください。

> [!IMPORTANT]
> Azure 仮想マシン スケール セットで OS イメージをアップグレードするためのメンテナンス コントロールは、現在パブリック プレビュー段階にあります。
> このプレビュー バージョンはサービス レベル アグリーメントなしで提供されています。運用環境のワークロードに使用することはお勧めできません。 特定の機能はサポート対象ではなく、機能が制限されることがあります。
> 詳しくは、[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)に関するページをご覧ください。


## <a name="enable-the-powershell-module"></a>PowerShell モジュールを有効にする

`PowerShellGet` が最新の状態であることを確認します。    

```azurepowershell-interactive  
Install-Module -Name PowerShellGet -Repository PSGallery -Force 
``` 

`Az.Maintenance` PowerShell モジュールをインストールします。     

```azurepowershell-interactive  
Install-Module -Name Az.Maintenance
``` 

ローカルにインストールする場合は、管理者として PowerShell プロンプトを開いてください。

*信頼されていないリポジトリ* からインストールするかどうかを確認するメッセージが表示される場合もあります。 モジュールをインストールするには、`Y` を入力するか、 **[すべてはい]** を選択します。

## <a name="connect-to-an-azure-account"></a>Azure アカウントに接続する

[Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount) および [Set-AzAccount](/powershell/module/az.accounts/set-azcontext) を使用して、目的の Azure アカウントに接続します。

```azurepowershell-interactive
Connect-AzAccount
Set-AzContext 00a000aa-0a00-0a0a-00aa-a00a000aaa00

$RGName="myMaintenanceRG"
$MaintenanceConfig="myMaintenanceConfig"
$location="eastus2"
$vmss="myMaintenanceVMSS"
```

## <a name="create-a-maintenance-configuration"></a>メンテナンス構成を作成する

構成のコンテナーとして、リソースグループを作成します。 この例では、*myMaintenanceRG* という名前のリソース グループが、*eastus2* に作成されます。 使用するリソース グループが既にある場合は、この部分をスキップできます。 この後の例では、リソース グループ名を実際の名前に置き換えてください。

```azurepowershell-interactive
New-AzResourceGroup `
   -Location $location `
   -Name $RGName
```

[New-AzMaintenanceConfiguration](/powershell/module/az.maintenance/new-azmaintenanceconfiguration) を使用して、メンテナンス構成を作成します。 この例では、OS イメージを対象とした *myConfig* という名前のメンテナンス構成を作成します。 

```azurepowershell-interactive
$config = New-AzMaintenanceConfiguration `
   -ResourceGroup $RGName `
   -Name $MaintenanceConfig `
   -MaintenanceScope OSImage `
   -Location $location `
   -StartDateTime "2020-10-01 00:00" `
   -TimeZone "Pacific Standard Time" `
   -Duration "05:00" `
   -RecurEvery "Day"
```

> [!IMPORTANT]
> メンテナンスの **期間** は、*5 時間* 以上である必要があります。 メンテナンスの **繰り返し** は *Day* に設定する必要があります。

`-MaintenanceScope OSImage` を使用すると、そのメンテナンス構成がゲスト OS に対する更新をコントロールするために確実に使用されているかを確認できます。

同じ名前の構成を別の場所に作成しようとすると、エラーが発生します。 構成名は、リソース グループに対して一意である必要があります。

[Get-AzMaintenanceConfiguration](/powershell/module/az.maintenance/get-azmaintenanceconfiguration) を使用して、使用可能なメンテナンス構成に対してクエリを実行できます。

```azurepowershell-interactive
Get-AzMaintenanceConfiguration | Format-Table -Property Name,Id
```

## <a name="associate-your-virtual-machine-scale-set-to-the-maintenance-configuration"></a>仮想マシン スケール セットをメンテナンス構成に関連付ける

仮想マシン スケール セットは、メンテナンス構成のリージョンとサブスクリプションに関係なく、任意のメンテナンス構成に関連付けることができます。 メンテナンス構成にオプトインすると、スケール セットに対する新しい OS イメージの更新が、次に実行可能なメンテナンス期間に自動的にスケジュールされます。

[New-AzConfigurationAssignment](/powershell/module/az.maintenance/new-azconfigurationassignment) を使用して、仮想マシン スケール セットをメンテナンス構成に関連付けます。

```azurepowershell-interactive
New-AzConfigurationAssignment `
   -ResourceGroupName $RGName `
   -Location $location `
   -ResourceName $vmss `
   -ResourceType VirtualMachineScaleSets `
   -ProviderName Microsoft.Compute `
   -ConfigurationAssignmentName $config.Name`
   -MaintenanceConfigurationId $config.Id
``` 

## <a name="enable-automatic-os-upgrade"></a>OS の自動アップグレードを有効にする

メンテナンス コントロールを使用する仮想マシン スケール セットごとに、OS の自動アップグレードを有効にすることができます。 仮想マシン スケール セットで OS の自動アップグレードを有効にするには、ドキュメント「[Azure 仮想マシン スケール セットによる OS イメージの自動アップグレード](../virtual-machine-scale-sets/virtual-machine-scale-sets-automatic-upgrade.md)」を参照してください。 


## <a name="next-steps"></a>次の手順

Azure で実行されている仮想マシンのメンテナンスと更新について確認する。

> [!div class="nextstepaction"]
> [メンテナンスと更新](maintenance-and-updates.md)