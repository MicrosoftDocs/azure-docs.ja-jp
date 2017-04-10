---
title: "Azure PowerShell のサンプル スクリプト - WordPress | Microsoft Docs"
description: "Azure PowerShell のサンプル スクリプト - WordPress"
services: virtual-machines-linux
documentationcenter: virtual-machines
author: neilpeterson
manager: timlt
editor: tysonn
tags: azure-service-management
ms.assetid: 
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 03/01/2017
ms.author: nepeters
translationtype: Human Translation
ms.sourcegitcommit: 0d8472cb3b0d891d2b184621d62830d1ccd5e2e7
ms.openlocfilehash: 2b8d155108221f70561c01266758ad5261731871
ms.lasthandoff: 03/21/2017

---

# <a name="create-a-wordpress-vm-with-powershell"></a>PowerShell を使用して WordPress VM を作成する

このスクリプトでは、仮想マシンを作成してから、Azure 仮想マシンのカスタム スクリプト拡張機能を使用して WordPress をインストールします。 スクリプトを実行すると、`http://<public IP of VM>/wordpress` にある WordPress 構成サイトにアクセスできるようになります。 

必要に応じて、[Azure PowerShell ガイド](https://docs.microsoft.com/powershell/azureps-cmdlets-docs/)の手順に従って Azure PowerShell をインストールし、`Login-AzureRmAccount` を実行して、Azure との接続を作成します。 また、`id_rsa.pub` という名前の SSH 公開キーをユーザー プロファイルの .ssh ディレクトリに保存する必要があります。

## <a name="sample-script"></a>サンプル スクリプト

[!code-powershell[main](../../../powershell_scripts/virtual-machine/create-wordpress-mysql/create-wordpress-mysql.ps1 "VM WordPress を作成する")]

## <a name="clean-up-deployment"></a>デプロイのクリーンアップ 

次のコマンドを実行して、リソース グループ、VM、すべての関連リソースを削除します。

```powershell
Remove-AzureRmResourceGroup -Name myResourceGroup
```

## <a name="script-explanation"></a>スクリプトの説明

このスクリプトでは、以下のコマンドを実行してデプロイを作成します。 表内の各項目は、コマンドごとのドキュメントにリンクされています。

| コマンド | メモ |
|---|---|
| [New-AzureRmResourceGroup](https://docs.microsoft.com/powershell/resourcemanager/azurerm.resources/v3.2.0/new-azurermresourcegroup) | すべてのリソースを格納するリソース グループを作成します。 |
| [New-AzureRmVirtualNetworkSubnetConfig](https://docs.microsoft.com/powershell/resourcemanager/azurerm.network/v2.1.0/new-azurermvirtualnetworksubnetconfig) | サブネット構成を作成します。 この構成は、仮想ネットワークの作成プロセスで使用されます。 |
| [New-AzureRmVirtualNetwork](https://docs.microsoft.com/powershell/resourcemanager/azurerm.network/v1.0.13/new-azurermvirtualnetwork) | 仮想ネットワークを作成します。 |
| [New-AzureRmPublicIpAddress](https://docs.microsoft.com/powershell/resourcemanager/azurerm.network/v2.1.0/new-azurermpublicipaddress) | パブリック IP アドレスを作成します。 |
| [New-AzureRmNetworkSecurityRuleConfig](https://docs.microsoft.com/powershell/resourcemanager/azurerm.network/v2.1.0/new-azurermnetworksecurityruleconfig) | ネットワーク セキュリティ グループ規則の構成を作成します。 この構成は、NSG の作成時に NSG 規則を作成するために使用されます。 |
| [New-AzureRmNetworkSecurityGroup](https://docs.microsoft.com/powershell/resourcemanager/azurerm.network/v3.1.0/new-azurermnetworksecuritygroup) | ネットワーク セキュリティ グループを作成します。 |
| [Get-AzureRmVirtualNetworkSubnetConfig](https://docs.microsoft.com/powershell/resourcemanager/azurerm.network/v1.0.13/get-azurermvirtualnetworksubnetconfig) | サブネット情報を取得します。 この情報は、ネットワーク インターフェイスを作成するときに使用されます。 |
| [New-AzureRmNetworkInterface](https://docs.microsoft.com/powershell/resourcemanager/azurerm.network/v3.4.0/new-azurermnetworkinterface) | ネットワーク インターフェイスを作成します。 |
| [New-AzureRmVMConfig](https://docs.microsoft.com/powershell/resourcemanager/azurerm.compute/v1.3.4/new-azurermvmconfig) | VM 構成を作成します。 この構成には、VM 名、オペレーティング システム、管理資格情報などの情報が含まれます。 この構成は、VM の作成時に使用されます。 |
| [New-AzureRmVM](https://docs.microsoft.com/powershell/resourcemanager/azurerm.compute/v1.3.4/new-azurermvm) | 仮想マシンを作成します。 |
| [Set-AzureRmVMExtension](https://docs.microsoft.com/powershell/resourcemanager/azurerm.compute/v2.2.0/set-azurermvmextension) | WordPress のインストール スクリプトを呼び出すカスタム スクリプト拡張機能を、仮想マシンに追加します。 |
|[Remove-AzureRmResourceGroup](https://docs.microsoft.com/powershell/resourcemanager/azurerm.resources/v3.5.0/remove-azurermresourcegroup) | リソース グループと、それに含まれているすべてのリソースを削除します。 |

## <a name="next-steps"></a>次のステップ

Azure PowerShell モジュールの詳細については、[Azure PowerShell のドキュメント](https://docs.microsoft.com/powershell/azureps-cmdlets-docs/)を参照してください。

その他の仮想マシン用の PowerShell サンプル スクリプトは、[Azure Linux VM のドキュメント](../virtual-machines-linux-powershell-samples.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)にあります。
