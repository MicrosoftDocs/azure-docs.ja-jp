---
title: Azure での VM とスケール セットの拡張機能の自動アップグレード
description: Azure で仮想マシンと仮想マシン スケール セットの拡張機能の自動アップグレードを有効にする方法について説明します。
author: mayanknayar
ms.service: virtual-machines
ms.subservice: automatic-extension-upgrade
ms.workload: infrastructure
ms.topic: how-to
ms.date: 02/12/2020
ms.author: manayar
ms.openlocfilehash: fa4fa1c43ab9d31b879bdec8e724e896bd16e14c
ms.sourcegitcommit: dac05f662ac353c1c7c5294399fca2a99b4f89c8
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/04/2021
ms.locfileid: "102123904"
---
# <a name="preview-automatic-extension-upgrade-for-vms-and-scale-sets-in-azure"></a>プレビュー:Azure での VM とスケール セットの拡張機能の自動アップグレード

拡張機能の自動アップグレードは、Azure VM と Azure Virtual Machine Scale Sets 用をプレビューで利用できます。 VM またはスケール セットで拡張機能の自動アップグレードが有効になっていると、拡張機能の発行元がその拡張機能の新しいバージョンをリリースするたびに、拡張機能が自動的にアップグレードされます。

 拡張機能の自動アップグレードには、次の機能があります。
- Azure VM と Azure Virtual Machine Scale Sets でサポートされています。 Service Fabric Virtual Machine Scale Sets は現在はサポートされていません。
- アップグレードは、可用性優先のデプロイ モデルで適用されます (後で詳述)。
- Virtual Machine Scale Sets では、スケール セットの仮想マシンの 20% 以上が 1 回のバッチでアップグレードされます。 最小バッチサイズは 1 仮想マシンです。
- すべての VM サイズ、および Windows と Linux の両方の拡張機能で動作します。
- いつでも自動アップグレードをオプトアウトできます。
- 拡張機能の自動アップグレードは、任意のサイズの Virtual Machine Scale Sets で有効にすることができます。
- サポートされている各拡張機能は個別に登録され、どの拡張機能を自動的にアップグレードするかを選択できます。
- すべてのパブリック クラウド リージョンでサポートされています。


> [!IMPORTANT]
> 拡張機能の自動アップグレードは、現在、パブリック プレビュー段階です。 後述するパブリック プレビュー機能を使用するためには、オプトイン手順が必要です。
> このプレビュー バージョンはサービス レベル アグリーメントなしで提供されています。運用環境のワークロードに使用することはお勧めできません。 特定の機能はサポート対象ではなく、機能が制限されることがあります。
> 詳しくは、[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)に関するページをご覧ください。


## <a name="how-does-automatic-extension-upgrade-work"></a>拡張機能の自動アップグレードのしくみ
拡張機能のアップグレード プロセスにより、VM 上の既存の拡張機能のバージョンが、拡張機能の発行元によって発行された、同じ拡張機能の新しいバージョンに置き換えられます。 新しい拡張機能がインストールされた後、VM の正常性が監視されます。 アップグレードが完了してから 5 分以内に VM が正常な状態にならない場合、拡張機能のバージョンは前のバージョンにロールバックされます。

拡張機能の更新が失敗した場合は、自動的に再試行されます。 再試行は、ユーザーの介入なしに、数日に 1 回試行されます。

### <a name="availability-first-updates"></a>可用性優先の更新
プラットフォームの調整された更新の可用性優先モデルにより、Azure の可用性構成が複数の可用性レベルで尊重されるようになります。

更新中の仮想マシンのグループについては、Azure プラットフォームで更新がオーケストレーションされます。

**複数のリージョン間:**
- Azure 全体のデプロイが失敗するのを防ぐために、更新は Azure 全体を段階的に移動します。
- 1 つの "フェーズ" には 1 つ以上のリージョンを含めることができます。前のフェーズで対象の VM が正常に更新された場合にのみ、更新はフェーズ間を移動します。
- geo ペア リージョンは同時に更新されず、同じリージョン フェーズに置くことはできません。
- 更新の成功は、更新後に VM の正常性を追跡することによって測定できます。 VM の正常性は、VM のプラットフォーム正常性インジケーターによって追跡されます。 Virtual Machine Scale Sets の場合、VM の正常性は、アプリケーション正常性プローブまたはアプリケーション正常性拡張機能によって追跡されます (スケール セットに適用される場合)。

**リージョン内:**
- 異なる Availability Zones の VM は同時に更新されません。
- 可用性セットの一部ではない単一の VM は、サブスクリプション内のすべての VM が同時に更新されることを回避するために、ベストエフォート方式でバッチ処理されます。  

**"セット" 内:**
- 共通の可用性セット内またはスケール セット内のすべての VM が同時に更新されることはありません。  
- 共通の可用性セット内の VM は更新ドメインの境界内で更新され、複数の更新ドメインにまたがる VM は同時に更新されません。  
- 共通の仮想マシン スケール セット内の VM は、バッチにグループ化され、更新ドメインの境界内で更新されます。

### <a name="upgrade-process-for-virtual-machine-scale-sets"></a>Virtual Machine Scale Sets のアップグレード プロセス
1. アップグレード プロセスの開始前に、オーケストレーターにより、(何らかの理由で) 異常な VM がスケール セット全体の 20% を超えていないことが確認されます。

2. アップグレード オーケストレーターによってアップグレードする VM インスタンス バッチが識別されます。 アップグレード バッチには、仮想マシンの最小バッチ サイズに基づき、VM 合計数の最大 20% が入ります。

3. アプリケーション正常性プローブまたはアプリケーション正常性拡張機能が構成されているスケール セットの場合、アップグレードは VM が正常になるまで 5 分間 (または定義されている正常性プローブ構成だけ) 待機した後、次のバッチがアップグレードされます。 アップグレード後に VM の正常性が回復しない場合は、既定で、VM の前のバージョンの拡張機能が再インストールされます。

4. また、アップグレード オーケストレーターにより、アップグレード後に異常が発生した VM の割合も追跡されます。 アップグレード処理中に異常なアップグレード済みインスタンスの割合が 20% を超えた場合、アップグレードは停止します。

上記のプロセスは、スケール セット内のすべてのインスタンスがアップグレードされるまで続行されます。

スケール セットのアップグレード オーケストレーターにより、各バッチをアップグレードする前に、スケール セット全体の正常性が確認されます。 バッチのアップグレード中に、他の計画メンテナンスまたは計画外メンテナンスのアクティビティが同時に発生することがあり、それによってスケール セット仮想マシンの正常性が影響を受ける場合があります。 そのような場合、スケール セットのインスタンスの 20% より多くが異常な状態になると、スケール セットのアップグレードは現在のバッチが終了した時点で停止します。

## <a name="supported-extensions"></a>サポートされる拡張機能
拡張機能の自動アップグレードのプレビューでは、次の拡張機能がサポートされています (さらに、定期的に追加されます)。
- Dependency Agent – [Windows](./extensions/agent-dependency-windows.md) と [Linux](./extensions/agent-dependency-linux.md)
- [アプリケーション正常性拡張機能](../virtual-machine-scale-sets/virtual-machine-scale-sets-health-extension.md) – Windows と Linux


## <a name="enabling-preview-access"></a>プレビュー アクセスの有効化
プレビュー機能を有効にするには、以下で詳しく説明するように、サブスクリプションごとに **AutomaticExtensionUpgradePreview** 機能を 1 回のみオプトインする必要があります。

### <a name="rest-api"></a>REST API
次の例では、お使いのサブスクリプションでプレビューを有効にする方法について説明します。

```
POST on `/subscriptions/{subscriptionId}/providers/Microsoft.Features/providers/Microsoft.Compute/features/AutomaticExtensionUpgradePreview/register?api-version=2015-12-01`
```

機能の登録には最大で 15 分かかる場合があります。 登録状態を確認するには:

```
GET on `/subscriptions/{subscriptionId}/providers/Microsoft.Features/providers/Microsoft.Compute/features/AutomaticExtensionUpgradePreview?api-version=2015-12-01`
```

サブスクリプションに対してこの機能が登録されたら、変更をコンピューティング リソース プロバイダーに伝達することによって、オプトイン プロセスを完了します。

```
POST on `/subscriptions/{subscriptionId}/providers/Microsoft.Compute/register?api-version=2020-06-01`
```

### <a name="azure-powershell"></a>Azure PowerShell
[Register-AzProviderFeature](/powershell/module/az.resources/register-azproviderfeature) コマンドレットを使用して、サブスクリプションでのプレビューを有効にします。

```azurepowershell-interactive
Register-AzProviderFeature -FeatureName AutomaticExtensionUpgradePreview -ProviderNamespace Microsoft.Compute
```

機能の登録には最大で 15 分かかる場合があります。 登録状態を確認するには:

```azurepowershell-interactive
Get-AzProviderFeature -FeatureName AutomaticExtensionUpgradePreview -ProviderNamespace Microsoft.Compute
```

サブスクリプションに対してこの機能が登録されたら、変更をコンピューティング リソース プロバイダーに伝達することによって、オプトイン プロセスを完了します。

```azurepowershell-interactive
Register-AzResourceProvider -ProviderNamespace Microsoft.Compute
```

### <a name="azure-cli"></a>Azure CLI
[az feature register](/cli/azure/feature#az-feature-register) を使用して、サブスクリプションでのプレビューを有効にします。

```azurecli-interactive
az feature register --namespace Microsoft.Compute --name AutomaticExtensionUpgradePreview
```

機能の登録には最大で 15 分かかる場合があります。 登録状態を確認するには:

```azurecli-interactive
az feature show --namespace Microsoft.Compute --name AutomaticExtensionUpgradePreview
```

サブスクリプションに対してこの機能が登録されたら、変更をコンピューティング リソース プロバイダーに伝達することによって、オプトイン プロセスを完了します。

```azurecli-interactive
az provider register --namespace Microsoft.Compute
```


## <a name="enabling-automatic-extension-upgrade"></a>拡張機能の自動アップグレードの有効化
拡張機能の自動アップグレードを拡張機能に対して有効にするには、プロパティ *enableAutomaticUpgrade* が *true* に設定され、すべての拡張機能の定義に個別に追加されていることを確認する必要があります。


### <a name="rest-api-for-virtual-machines"></a>Virtual Machines の REST API
Azure VM で拡張機能に対して拡張機能の自動アップグレードを有効にするには (この例では Dependency Agent 拡張機能)、次のようにします。

```
PUT on `/subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.Compute/virtualMachines/<vmName>/extensions/<extensionName>?api-version=2019-12-01`
```

```json
{    
    "name": "extensionName",
    "type": "Microsoft.Compute/virtualMachines/extensions",
    "location": "<location>",
    "properties": {
        "autoUpgradeMinorVersion": true,
        "enableAutomaticUpgrade": true, 
        "publisher": "Microsoft.Azure.Monitoring.DependencyAgent",
        "type": "DependencyAgentWindows",
        "typeHandlerVersion": "9.5"
        }
}
```

### <a name="rest-api-for-virtual-machine-scale-sets"></a>Virtual Machine Scale Sets の REST API
スケール セット モデルに拡張機能を追加するには、次のようにします。

```
PUT on `/subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.Compute/virtualMachineScaleSets/<vmssName>?api-version=2019-12-01`
```

```json
{
   "location": "<location>",
   "properties": {
        "virtualMachineProfile": {
            "extensionProfile": {
                "extensions": [
                {
                "name": "<extensionName>",
                  "properties": {
                        "autoUpgradeMinorVersion": true,
                        "enableAutomaticUpgrade": true,
                    "publisher": "Microsoft.Azure.Monitoring.DependencyAgent",
                    "type": "DependencyAgentWindows",
                    "typeHandlerVersion": "9.5"
                    }
                }
                ]
            }
        }
    }
}
```

### <a name="azure-powershell-for-virtual-machines"></a>Virtual Machines の Azure PowerShell
[Set-AzVMExtension](/powershell/module/az.compute/set-azvmextension) コマンドレットを使用します。

```azurepowershell-interactive
Set-AzVMExtension -ExtensionName "Microsoft.Azure.Monitoring.DependencyAgent" `
    -ResourceGroupName "myResourceGroup" `
    -VMName "myVM" `
    -Publisher "Microsoft.Azure.Monitoring.DependencyAgent" `
    -ExtensionType "DependencyAgentWindows" `
    -TypeHandlerVersion 9.5 `
    -Location WestUS `
    -EnableAutomaticUpgrade $true
```


### <a name="azure-powershell-for-virtual-machine-scale-sets"></a>Virtual Machine Scale Sets の Azure PowerShell
拡張機能をスケール セット モデルに追加するには、[Add-AzVmssExtension](/powershell/module/az.compute/add-azvmssextension) コマンドレットを使用します。

```azurepowershell-interactive
Add-AzVmssExtension -VirtualMachineScaleSet $vmss
    -Name "Microsoft.Azure.Monitoring.DependencyAgent" `
    -Publisher "Microsoft.Azure.Monitoring.DependencyAgent" `
    -Type "DependencyAgentWindows" `
    -TypeHandlerVersion 9.5 `
    -EnableAutomaticUpgrade $true
```

拡張機能を追加した後、[Update-AzVmss](/powershell/module/az.compute/update-azvmss) を使用してスケール セットを更新します。


### <a name="azure-cli-for-virtual-machines"></a>Virtual Machines 向け Azure CLI
[az vm extension set](/cli/azure/vm/extension#az_vm_extension_set) コマンドレットを使用します。

```azurecli-interactive
az vm extension set \
    --resource-group myResourceGroup \
    --vm-name myVM \
    --name DependencyAgentLinux \
    --publisher Microsoft.Azure.Monitoring.DependencyAgent \
    --version 9.5 \
    --enable-auto-upgrade true
```

### <a name="azure-cli-for-virtual-machine-scale-sets"></a>Virtual Machine Scale Sets の Azure CLI
拡張機能をスケール セット モデルに追加するには、[az vmss extension set](/cli/azure/vmss/extension#az_vmss_extension_set) コマンドレットを使用します。

```azurecli-interactive
az vmss extension set \
    --resource-group myResourceGroup \
    --vmss-name myVMSS \
    --name DependencyAgentLinux \
    --publisher Microsoft.Azure.Monitoring.DependencyAgent \
    --version 9.5 \
    --enable-auto-upgrade true
```

## <a name="extension-upgrades-with-multiple-extensions"></a>複数の拡張機能での拡張機能のアップグレード

VM または Virtual Machine Scale Set に複数の拡張機能を追加し、その拡張機能の自動アップグレードを有効にできます。 同じ VM またはスケール セットに他の拡張機能を追加し、その拡張機能自動アップグレードを有効にしないこともできます。  

1 つの仮想マシンで複数の拡張機能アップグレードが利用できる場合、アップグレードをバッチにまとめることができる場合がありますが、仮想マシン上では、各拡張機能アップグレードが個別に適用されます。 1 つの拡張機能で障害が発生しても、アップグレードされる可能性のある他の拡張機能には影響しません。 たとえば、2 つの拡張機能のアップグレードがスケジュールされていて、最初の拡張機能のアップグレードが失敗した場合でも、2 つ目の拡張機能はアップグレードされます。

VM または仮想マシン スケール セットに[拡張機能のシーケンス処理](../virtual-machine-scale-sets/virtual-machine-scale-sets-extension-sequencing.md)を使用して複数の拡張機能が構成されている場合は、拡張機能の自動アップグレードも適用できます。 拡張機能のシーケンス処理は VM の初回デプロイに適用されます。将来、ある拡張機能に拡張機能アップグレードがあるとすれば、それは個別に適用されます。


## <a name="next-steps"></a>次のステップ
> [!div class="nextstepaction"]
> [アプリケーション正常性拡張機能について確認する](../virtual-machine-scale-sets/virtual-machine-scale-sets-health-extension.md)
