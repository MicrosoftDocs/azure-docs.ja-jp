---
title: チュートリアル - Azure PowerShell で Azure 仮想マシンを管理する | Microsoft Docs
description: このチュートリアルでは、Azure PowerShell を使用して、RBAC、ポリシー、ロック、およびタグを適用することによって Azure 仮想マシンを管理する方法について説明します
services: virtual-machines-windows
documentationcenter: virtual-machines
author: tfitzmac
manager: jeconnoc
editor: tysonn
ms.service: virtual-machines-windows
ms.workload: infrastructure
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: tutorial
ms.date: 12/05/2018
ms.author: tomfitz
ms.custom: mvc
ms.openlocfilehash: 9be421e85d41586c18bee15cd748539e3910021b
ms.sourcegitcommit: cf971fe82e9ee70db9209bb196ddf36614d39d10
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/27/2019
ms.locfileid: "58540654"
---
# <a name="tutorial-learn-about-windows-virtual-machine-management-with-azure-powershell"></a>チュートリアル:Azure PowerShell を使用した Windows 仮想マシンの管理方法の説明

[!INCLUDE [Resource Manager governance introduction](../../../includes/resource-manager-governance-intro.md)]

## <a name="launch-azure-cloud-shell"></a>Azure Cloud Shell を起動する

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Azure Cloud Shell は無料のインタラクティブ シェルです。この記事の手順は、Azure Cloud Shell を使って実行することができます。 一般的な Azure ツールが事前にインストールされており、アカウントで使用できるように構成されています。 

Cloud Shell を開くには、コード ブロックの右上隅にある **[使ってみる]** を選択します。 [https://shell.azure.com/powershell](https://shell.azure.com/powershell) に移動して、別のブラウザー タブで Cloud Shell を起動することもできます。 **[コピー]** を選択してコードのブロックをコピーし、Cloud Shell に貼り付けてから、Enter キーを押して実行します。

## <a name="understand-scope"></a>スコープを理解する

[!INCLUDE [Resource Manager governance scope](../../../includes/resource-manager-governance-scope.md)]

このチュートリアルでは、すべての管理設定をリソース グループに適用して、完了したらこれらの設定を容易に削除できるようにします。

そのリソース グループを作成しましょう。

```azurepowershell-interactive
New-AzResourceGroup -Name myResourceGroup -Location EastUS
```

現在、リソース グループは空です。

## <a name="role-based-access-control"></a>ロールベースのアクセス制御

組織のユーザーがこれらのリソースへの適切なアクセス レベルを持つようにします。 ユーザーに無制限のアクセス権を許可したくはありませんが、ユーザーが自分の作業を実行できるようにすることも必要です。 [ロールベースのアクセス制御](../../role-based-access-control/overview.md)を使うと、あるスコープで特定のアクションを実行するアクセス許可を持つユーザーを管理することができます。

ロールの割り当てを作成および削除するには、`Microsoft.Authorization/roleAssignments/*` アクセス権が必要です。 このアクセス権は、所有者ロールまたはユーザー アクセス管理者ロールを通じて許可されます。

仮想マシン ソリューションを管理するために、一般的に必要なアクセスを提供する、リソースに固有の次の 3 つのロールがあります。

* [Virtual Machine Contributor](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor)
* [Network Contributor](../../role-based-access-control/built-in-roles.md#network-contributor)
* [Storage Account Contributor](../../role-based-access-control/built-in-roles.md#storage-account-contributor)

多くの場合は、個々のユーザーにロールを割り当てる代わりに、類似のアクションを実行する必要のあるユーザーを Azure Active Directory グループにまとめて使用する方が簡単です。 その後、そのグループを適切なロールに割り当てます。 この記事では、仮想マシンを管理するための既存のグループを使用するか、またはポータルを使用して [Azure Active Directory グループを作成](../../active-directory/fundamentals/active-directory-groups-create-azure-portal.md)します。

新しいグループを作成するか、または既存のグループを見つけた後、[New-AzRoleAssignment](https://docs.microsoft.com/powershell/module/az.resources/new-azroleassignment) コマンドを使って、リソース グループの仮想マシン共同作成者ロールに、Azure Active Directory グループを割り当てます。  

```azurepowershell-interactive
$adgroup = Get-AzADGroup -DisplayName <your-group-name>

New-AzRoleAssignment -ObjectId $adgroup.id `
  -ResourceGroupName myResourceGroup `
  -RoleDefinitionName "Virtual Machine Contributor"
```

"**プリンシパル \<guid> がディレクトリにありません**" というエラー メッセージが表示される場合は、まだ新しいグループが Azure Active Directory 全体に伝達されていません。 そのときは、コマンドをもう一度実行してみます。

通常は、デプロイされたリソースを管理するユーザーが確実に割り当てられるようにするために、このプロセスを*ネットワークの共同作業者*と*ストレージ アカウントの共同作業者*に対して繰り返します。 この記事では、これらの手順を省略できます。

## <a name="azure-policy"></a>Azure Policy

[Azure Policy](../../governance/policy/overview.md) は、サブスクリプション内のすべてのリソースが会社の基準を順守するために役立ちます。 サブスクリプションには、既にいくつかのポリシー定義が含まれています。 使用可能なポリシー定義を表示するには、[Get-AzPolicyDefinition](https://docs.microsoft.com/powershell/module/az.resources/Get-AzPolicyDefinition) コマンドを使用します。

```azurepowershell-interactive
(Get-AzPolicyDefinition).Properties | Format-Table displayName, policyType
```

既存のポリシー定義が表示されます。 ポリシーの種類は、**[BuiltIn] (ビルトイン)** または **[カスタム]** のどちらかです。 この中から、割り当てる条件を記述している定義を探します。 この記事では、次のようなポリシーを割り当てます。

* すべてのリソースの場所を制限する。
* 仮想マシンの SKU を制限する。
* マネージド ディスクを使用しない仮想マシンを監査する。

次の例では、表示名に基づいて 3 つのポリシー定義を取得します。 [New-AzPolicyAssignment](https://docs.microsoft.com/powershell/module/az.resources/new-azpolicyassignment) コマンドを使って、それらの定義をリソース グループに割り当てます。 一部のポリシーについては、許可される値を指定するパラメーター値を提供します。

```azurepowershell-interactive
# Values to use for parameters
$locations ="eastus", "eastus2"
$skus = "Standard_DS1_v2", "Standard_E2s_v2"

# Get the resource group
$rg = Get-AzResourceGroup -Name myResourceGroup

# Get policy definitions for allowed locations, allowed SKUs, and auditing VMs that don't use managed disks
$locationDefinition = Get-AzPolicyDefinition | where-object {$_.properties.displayname -eq "Allowed locations"}
$skuDefinition = Get-AzPolicyDefinition | where-object {$_.properties.displayname -eq "Allowed virtual machine SKUs"}
$auditDefinition = Get-AzPolicyDefinition | where-object {$_.properties.displayname -eq "Audit VMs that do not use managed disks"}

# Assign policy for allowed locations
New-AzPolicyAssignment -Name "Set permitted locations" `
  -Scope $rg.ResourceId `
  -PolicyDefinition $locationDefinition `
  -listOfAllowedLocations $locations

# Assign policy for allowed SKUs
New-AzPolicyAssignment -Name "Set permitted VM SKUs" `
  -Scope $rg.ResourceId `
  -PolicyDefinition $skuDefinition `
  -listOfAllowedSKUs $skus

# Assign policy for auditing unmanaged disks
New-AzPolicyAssignment -Name "Audit unmanaged disks" `
  -Scope $rg.ResourceId `
  -PolicyDefinition $auditDefinition
```

## <a name="deploy-the-virtual-machine"></a>仮想マシンをデプロイする

ロールとポリシーを割り当てたため、ソリューションをデプロイする準備ができました。 既定のサイズは Standard_DS1_v2 です。これは、許可される SKU の 1 つです。 この手順の実行時に、資格情報の入力を求められます。 入力した値は、仮想マシンのユーザー名とパスワードとして構成されます。

```azurepowershell-interactive
New-AzVm -ResourceGroupName "myResourceGroup" `
     -Name "myVM" `
     -Location "East US" `
     -VirtualNetworkName "myVnet" `
     -SubnetName "mySubnet" `
     -SecurityGroupName "myNetworkSecurityGroup" `
     -PublicIpAddressName "myPublicIpAddress" `
     -OpenPorts 80,3389
```

デプロイが完了したら、そのソリューションにより多くの管理設定を適用できます。

## <a name="lock-resources"></a>リソースのロック

[リソースのロック](../../azure-resource-manager/resource-group-lock-resources.md)は、組織内のユーザーが重要なリソースを誤って削除したり変更したりするのを防ぎます。 ロールベースのアクセス制御とは異なり、リソースのロックはすべてのユーザーとロールに制限を適用します。 ロック レベルは *CanNotDelete* または *ReadOnly* に設定できます。

仮想マシンとネットワーク セキュリティ グループをロックするには、[New-AzResourceLock](https://docs.microsoft.com/powershell/module/az.resources/new-azresourcelock) コマンドを使用します。

```azurepowershell-interactive
# Add CanNotDelete lock to the VM
New-AzResourceLock -LockLevel CanNotDelete `
  -LockName LockVM `
  -ResourceName myVM `
  -ResourceType Microsoft.Compute/virtualMachines `
  -ResourceGroupName myResourceGroup

# Add CanNotDelete lock to the network security group
New-AzResourceLock -LockLevel CanNotDelete `
  -LockName LockNSG `
  -ResourceName myNetworkSecurityGroup `
  -ResourceType Microsoft.Network/networkSecurityGroups `
  -ResourceGroupName myResourceGroup
```

ロックをテストするには、次のコマンドを実行してみてください。

```azurepowershell-interactive 
Remove-AzResourceGroup -Name myResourceGroup
```

ロックのために削除操作を完了できないことを示すエラーが表示されます。 リソース グループは、ロックを明確に削除した場合にのみ削除できます。 その手順は、「[リソースのクリーンアップ](#clean-up-resources)」に示されています。

## <a name="tag-resources"></a>リソースへのタグ付け

Azure リソースに[タグ](../../azure-resource-manager/resource-group-using-tags.md)を適用すると、カテゴリ別に論理的に整理できます。 各タグは名前と値で構成されます。 たとえば、運用環境のすべてのリソースには名前 "環境" と値 "運用" を適用できます。

[!INCLUDE [Resource Manager governance tags Powershell](../../../includes/resource-manager-governance-tags-powershell.md)]

仮想マシンにタグを適用するには、[Set-AzResource](https://docs.microsoft.com/powershell/module/az.resources/set-azresource) コマンドを使用します。

```azurepowershell-interactive
# Get the virtual machine
$r = Get-AzResource -ResourceName myVM `
  -ResourceGroupName myResourceGroup `
  -ResourceType Microsoft.Compute/virtualMachines

# Apply tags to the virtual machine
Set-AzResource -Tag @{ Dept="IT"; Environment="Test"; Project="Documentation" } -ResourceId $r.ResourceId -Force
```

### <a name="find-resources-by-tag"></a>タグでリソースを見つける

タグの名前と値でリソースを検索するには、[Get-AzResource](https://docs.microsoft.com/powershell/module/az.resources/get-azresource) コマンドを使用します。

```azurepowershell-interactive
(Get-AzResource -Tag @{ Environment="Test"}).Name
```

返される値は、タグ値ですべての仮想マシンを停止するような管理タスクで使用できます。

```azurepowershell-interactive
Get-AzResource -Tag @{ Environment="Test"} | Where-Object {$_.ResourceType -eq "Microsoft.Compute/virtualMachines"} | Stop-AzVM
```

### <a name="view-costs-by-tag-values"></a>タグ値でコストを表示する

[!INCLUDE [Resource Manager governance tags billing](../../../includes/resource-manager-governance-tags-billing.md)]

## <a name="clean-up-resources"></a>リソースのクリーンアップ

ロックされたネットワーク セキュリティ グループは、そのロックが削除されるまで削除できません。 ロックを削除するには、[Remove-AzResourceLock](https://docs.microsoft.com/powershell/module/az.resources/remove-azresourcelock) コマンドを使用します。

```azurepowershell-interactive
Remove-AzResourceLock -LockName LockVM `
  -ResourceName myVM `
  -ResourceType Microsoft.Compute/virtualMachines `
  -ResourceGroupName myResourceGroup
Remove-AzResourceLock -LockName LockNSG `
  -ResourceName myNetworkSecurityGroup `
  -ResourceType Microsoft.Network/networkSecurityGroups `
  -ResourceGroupName myResourceGroup
```

必要がなくなったら、[Remove-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/remove-azresourcegroup) コマンドを使用して、リソース グループ、VM、およびすべての関連リソースを削除できます。

```azurepowershell-interactive
Remove-AzResourceGroup -Name myResourceGroup
```

## <a name="next-steps"></a>次の手順

このチュートリアルでは、カスタム VM イメージを作成しました。 以下の方法について学習しました。

> [!div class="checklist"]
> * ロールにユーザーを割り当てる
> * 基準を強制するポリシーを適用する
> * 重要なリソースをロックで保護する
> * 課金と管理のためにリソースにタグを付ける

次のチュートリアルに進み、仮想マシンの高可用性について学習してください。

> [!div class="nextstepaction"]
> [仮想マシンの監視](tutorial-monitoring.md)

