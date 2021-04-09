---
title: Azure PowerShell のサンプル - 完全な仮想マシン スケール セットを作成する
description: このスクリプトでは、Windows Server 2016 を実行する仮想マシン スケール セットを作成します。ここでは、個々のリソースが構成されて作成されます。
author: mimckitt
ms.author: mimckitt
ms.topic: sample
ms.service: virtual-machine-scale-sets
ms.subservice: powershell
ms.date: 05/29/2018
ms.reviewer: jushiman
ms.custom: mimckitt, devx-track-azurepowershell
ms.openlocfilehash: d37f5f624459db6bc336884987a16c60503492a8
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "89078505"
---
# <a name="create-a-complete-virtual-machine-scale-set-with-powershell"></a>PowerShell を使用して完全な仮想マシン スケール セットを作成する

このスクリプトは、Windows Server 2016 を実行する仮想マシン スケール セットを作成します。 [New-AzVmss で使用できる組み込みのリソースの作成オプション](powershell-sample-create-simple-scale-set.md)を使用する代わりに、個々のリソースが構成され、作成されます。 スクリプトを実行すると、RDP で VM インスタンスにアクセスできるようになります。


[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [updated-for-az.md](../../../includes/updated-for-az.md)]

## <a name="sample-script"></a>サンプル スクリプト

[!code-powershell[main](../../../powershell_scripts/virtual-machine-scale-sets/complete-scale-set/complete-scale-set.ps1 "Create a complete virtual machine scale set")]

## <a name="clean-up-deployment"></a>デプロイのクリーンアップ
次のコマンドを実行して、リソース グループ、スケール セット、すべての関連リソースを削除します。

```powershell
Remove-AzResourceGroup -Name $resourceGroupName
```

## <a name="script-explanation"></a>スクリプトの説明
このスクリプトでは、以下のコマンドを実行してデプロイを作成します。 表内の各項目は、コマンドごとのドキュメントにリンクされています。

| コマンド | Notes |
|---|---|
| [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) | すべてのリソースを格納するリソース グループを作成します。 |
| [New-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/new-azvirtualnetworksubnetconfig) | サブネット構成を作成します。 この構成は、仮想ネットワークの作成プロセスで使用されます。 |
| [New-AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork) | 仮想ネットワークを作成します。 |
| [New-AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress) | パブリック IP アドレスを作成します。 |
| [New-AzLoadBalancerFrontendIpConfig](/powershell/module/az.network/new-azloadbalancerfrontendipconfig) | ロード バランサー用のフロント エンド IP 構成を作成します。 |
| [New-AzLoadBalancerBackendAddressPoolConfig](/powershell/module/az.network/new-azloadbalancerbackendaddresspoolconfig) | ロード バランサー用のバックエンド アドレス プール構成を作成します。 |
| [New-AzLoadBalancerInboundNatRuleConfig](/powershell/module/az.network/new-azloadbalancerinboundnatruleconfig) | ロード バランサー用の受信 NAT 規則の構成を作成します。 |
| [New-AzLoadBalancer](/powershell/module/az.network/new-azloadbalancer) | ロード バランサーを作成します。 |
| [Add-AzLoadBalancerProbeConfig](/powershell/module/az.network/new-azloadbalancerprobeconfig) | ロード バランサー用のプローブの構成を作成します。 |
| [Add-AzLoadBalancerRuleConfig](/powershell/module/az.network/new-azloadbalancerruleconfig) | ロード バランサー用の規則の構成を作成します。 |
| [Set-AzLoadBalancer](/powershell/module/az.Network/Set-azLoadBalancer) | 指定した情報で、ロード バランサーを更新します。 |
| [New-AzVmssIpConfig](/powershell/module/az.Compute/New-azVmssIpConfig) | スケール セット VM インスタンスの IP 構成を作成します。 この VM インスタンスは、ロード バランサー バックエンド プール、NAT プール、および仮想ネットワーク サブネットに接続されます。 |
| [New-AzVmssConfig](/powershell/module/az.Compute/New-azVmssConfig) | スケール セットの構成を作成します。 この構成には、作成する VM インスタンスの数、VM SKU (サイズ)、アップグレード ポリシー モードなどの情報が含まれます。 この構成は、その他のコマンドレットによって追加され、スケール セットの作成時に使用されます。 |
| [Set-AzVmssStorageProfile](/powershell/module/az.Compute/Set-azVmssStorageProfile) | VM インスタンスで使用されるイメージを定義してスケール セットの構成に追加します。 |
| [Set-AzVmssOsProfile](/powershell/module/az.Compute/Set-azVmssStorageProfile) | 管理ユーザー名とパスワードの資格情報、および VM の名前付けプレフィックスを定義します。 これらの値をスケール セットの構成に追加します。 |
| [Add-AzVmssNetworkInterfaceConfiguration](/powershell/module/az.Compute/Add-azVmssNetworkInterfaceConfiguration) | IP 構成に基づいて、仮想ネットワーク インターフェイスをVM インスタンスに追加します。 これらの値をスケール セットの構成に追加します。 |
| [New-AzVmss](/powershell/module/az.Compute/New-azVmss) | スケール セットの構成に指定された情報に基づいてスケール セットを作成します。 |
| [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) | リソース グループと、それに含まれているすべてのリソースを削除します。 |

## <a name="next-steps"></a>次のステップ
Azure PowerShell モジュールの詳細については、[Azure PowerShell のドキュメント](/powershell/azure/)を参照してください。
