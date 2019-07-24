---
title: Azure PowerShell スクリプトのサンプル - Docker | Microsoft Docs
description: Azure PowerShell スクリプトのサンプル - Docker
services: virtual-machines-linux
documentationcenter: virtual-machines
author: cynthn
manager: jeconnoc
editor: tysonn
tags: azure-service-management
ms.assetid: ''
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: sample
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 03/02/2017
ms.author: cynthn
ms.custom: mvc
ms.openlocfilehash: 9063d2921e53401e434079803e6dbcd430f1fd6c
ms.sourcegitcommit: 943af92555ba640288464c11d84e01da948db5c0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/09/2019
ms.locfileid: "55981946"
---
# <a name="create-a-docker-host-with-powershell"></a>PowerShell で Docker ホストを作成する

このスクリプトは、Docker を有効にした仮想マシンを作成し、NGINX を実行するコンテナーを起動します。 このスクリプトを実行すると、Azure 仮想マシンの FQDN 経由で NGINX Web サーバーにアクセスできるようになります。

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [updated-for-az-vm.md](../../../includes/updated-for-az-vm.md)]

## <a name="sample-script"></a>サンプル スクリプト

[!code-powershell[main](../../../powershell_scripts/virtual-machine/create-docker-host/create-docker-host.ps1 "Create Docker host")]

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
| [New-AzVM](https://docs.microsoft.com/powershell/module/az.compute/new-azvm) | 仮想マシンを作成します。 |
| [Set-AzVMExtension](https://docs.microsoft.com/powershell/module/az.compute/set-azvmextension) | 仮想マシンに VM 拡張機能を追加します。 このサンプルでは、Docker を構成し、NGINX Docker コンテナーを実行するために、Docker 拡張機能が使用されます。 |
|[Remove-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/remove-azresourcegroup) | リソース グループと、それに含まれているすべてのリソースを削除します。 |

## <a name="next-steps"></a>次の手順

Azure PowerShell モジュールの詳細については、[Azure PowerShell のドキュメント](/powershell/azure/overview)を参照してください。

その他の仮想マシン用の PowerShell サンプル スクリプトは、[Azure Linux VM のドキュメント](../linux/powershell-samples.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)にあります。
