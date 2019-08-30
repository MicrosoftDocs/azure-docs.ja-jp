---
title: Azure PowerShell のサンプル スクリプト - Linux VM の作成 | Microsoft Docs
description: Azure PowerShell のサンプル スクリプト - Linux VM の作成
services: virtual-machines-linux
documentationcenter: virtual-machines
author: cynthn
manager: gwallace
editor: tysonn
tags: azure-service-management
ms.assetid: ''
ms.service: virtual-machines-linux
ms.topic: sample
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 03/23/2018
ms.author: cynthn
ms.custom: mvc
ms.openlocfilehash: 25032cc4d4e3fb857e319c9303d042dd5b4f677b
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/28/2019
ms.locfileid: "70090878"
---
# <a name="create-a-fully-configured-virtual-machine-with-powershell"></a>PowerShell を使用して、完全に構成された仮想マシンを作成する

このスクリプトでは、オペレーティング システムが Ubuntu の Azure 仮想マシンを作成します。 スクリプトを実行すると、SSH 経由で仮想マシンにアクセスできるようになります。

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [updated-for-az.md](../../../includes/updated-for-az.md)]

## <a name="sample-script"></a>サンプル スクリプト

[!code-powershell[main](../../../powershell_scripts/virtual-machine/create-vm-detailed/create-vm-detailed.ps1 "Create VM detailed")]

## <a name="clean-up-deployment"></a>デプロイのクリーンアップ

次のコマンドを実行して、リソース グループ、VM、すべての関連リソースを削除します。

```powershell
Remove-AzResourceGroup -Name myResourceGroup
```

## <a name="script-explanation"></a>スクリプトの説明

このスクリプトでは、以下のコマンドを実行してデプロイを作成します。 表内の各項目は、コマンドごとのドキュメントにリンクされています。

| command | メモ |
|---|---|
| [New-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/new-azresourcegroup) | すべてのリソースを格納するリソース グループを作成します。 |
| [New-AzVirtualNetworkSubnetConfig](https://docs.microsoft.com/powershell/module/az.network/new-azvirtualnetworksubnetconfig) | サブネット構成を作成します。 この構成は、仮想ネットワークの作成プロセスで使用されます。 |
| [New-AzVirtualNetwork](https://docs.microsoft.com/powershell/module/az.network/new-azvirtualnetwork) | 仮想ネットワークを作成します。 |
| [New-AzPublicIpAddress](https://docs.microsoft.com/powershell/module/az.network/new-azpublicipaddress) | パブリック IP アドレスを作成します。 |
| [New-AzNetworkSecurityRuleConfig](https://docs.microsoft.com/powershell/module/az.network/new-aznetworksecurityruleconfig) | ネットワーク セキュリティ グループ規則の構成を作成します。 この構成は、NSG の作成時に NSG 規則を作成するために使用されます。 |
| [New-AzNetworkSecurityGroup](https://docs.microsoft.com/powershell/module/az.network/new-aznetworksecuritygroup) | ネットワーク セキュリティ グループを作成します。 |
| [Get-AzVirtualNetworkSubnetConfig](https://docs.microsoft.com/powershell/module/az.network/get-azvirtualnetworksubnetconfig) | サブネット情報を取得します。 この情報は、ネットワーク インターフェイスを作成するときに使用されます。 |
| [New-AzNetworkInterface](https://docs.microsoft.com/powershell/module/az.network/new-aznetworkinterface) | ネットワーク インターフェイスを作成します。 |
| [New-AzVMConfig](https://docs.microsoft.com/powershell/module/az.compute/new-azvmconfig) | VM 構成を作成します。 この構成には、VM 名、オペレーティング システム、管理資格情報などの情報が含まれます。 この構成は、VM の作成時に使用されます。 |
| [Set-AzVMOperatingSystem](https://docs.microsoft.com/powershell/module/az.compute/set-azvmoperatingsystem) | 仮想マシンに使用するオペレーティング システムのプロパティを設定します。 |
| [Set-AzVMSourceImage](https://docs.microsoft.com/powershell/module/az.compute/set-azvmsourceimage) | 仮想マシンのイメージを指定します。 |
| [Add-AzVMNetworkInterface](https://docs.microsoft.com/powershell/module/az.compute/add-azvmnetworkinterface) | 仮想マシンにネットワーク インターフェイスを追加します。 |
| [New-AzVM](https://docs.microsoft.com/powershell/module/az.compute/new-azvm) | 仮想マシンを作成します。 |
|[Remove-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/remove-azresourcegroup) | リソース グループと、それに含まれているすべてのリソースを削除します。 |

## <a name="next-steps"></a>次の手順

Azure PowerShell モジュールの詳細については、[Azure PowerShell のドキュメント](/powershell/azure/overview)を参照してください。

その他の仮想マシン用の PowerShell サンプル スクリプトは、[Azure Linux VM のドキュメント](../linux/powershell-samples.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)にあります。
