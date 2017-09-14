---
title: "Azure PowerShell スクリプト サンプル - ネットワーク仮想アプライアンス経由のトラフィックのルーティング | Microsoft Docs"
description: "Azure PowerShell スクリプト サンプル - ファイアウォール ネットワーク仮想アプライアンス経由のトラフィックのルーティング。"
services: virtual-network
documentationcenter: virtual-network
author: georgewallace
manager: timlt
editor: tysonn
tags: 
ms.assetid: 
ms.service: virtual-network
ms.devlang: powershell
ms.topic: article
ms.tgt_pltfrm: 
ms.workload: infrastructure
ms.date: 05/16/2017
ms.author: gwallace
ms.translationtype: Human Translation
ms.sourcegitcommit: 67ee6932f417194d6d9ee1e18bb716f02cf7605d
ms.openlocfilehash: 883d28dac72a66c2186d222f72b04d68e532cead
ms.contentlocale: ja-jp
ms.lasthandoff: 05/27/2017


---

# <a name="route-traffic-through-a-network-virtual-appliance"></a>ネットワーク仮想アプライアンス経由のトラフィックのルーティング

このスクリプト サンプルは、フロントエンド サブネットとバックエンド サブネットを含む仮想ネットワークを作成します。 2 つのサブネット間でトラフィックをルーティングするために、IP 転送を有効にした VM も作成します。 スクリプトの実行後には、ファイアウォール アプリケーションなどのネットワーク ソフトウェアを VM にデプロイできます。

必要に応じて、[Azure PowerShell ガイド](https://docs.microsoft.com/powershell/azureps-cmdlets-docs/)の手順に従って Azure PowerShell をインストールし、`Login-AzureRmAccount` を実行して、Azure との接続を作成します。

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>サンプル スクリプト


[!code-powershell[メイン](../../../powershell_scripts/virtual-network/route-traffic-through-nva/route-traffic-through-nva.ps1 "ネットワーク仮想アプライアンス経由のトラフィックのルーティング")]

## <a name="clean-up-deployment"></a>デプロイのクリーンアップ 

次のコマンドを実行して、リソース グループ、VM、すべての関連リソースを削除します。

```powershell
Remove-AzureRmResourceGroup -Name myResourceGroup
```
## <a name="script-explanation"></a>スクリプトの説明

このスクリプトでは、次のコマンドを使用して、リソース グループ、仮想ネットワーク、およびネットワーク セキュリティ グループを作成します。 表内の各コマンドは、それぞれのドキュメントにリンクされています。

| コマンド | メモ |
|---|---|
| [New-AzureRmResourceGroup](/powershell/module/azurerm.resources/new-azurermresourcegroup)  | すべてのリソースを格納するリソース グループを作成します。 |
| [New-AzureRmVirtualNetwork](/powershell/module/azurerm.network/new-azurermvirtualnetwork) | Azure 仮想ネットワークとフロントエンド サブネットを作成します。 |
| [New-AzureRmVirtualNetworkSubnetConfig](/powershell/module/azurerm.network/new-azurermvirtualnetworksubnetconfig) | バックエンドおよび DMZ サブネットを作成します。 |
| [New-AzureRmPublicIpAddress](/powershell/module/azurerm.network/new-azurermpublicipaddress) | インターネットから VM にアクセスするためのパブリック IP アドレスを作成します。 |
| [New-AzureRmNetworkInterface](/powershell/module/azurerm.network/new-azurermnetworkinterface) | 仮想ネットワーク インターフェイスを作成し、IP 転送を有効にします。 |
| [New-AzureRmNetworkSecurityGroup](/powershell/module/azurerm.network/new-azurermnetworksecuritygroup) | ネットワーク セキュリティ グループ (NSG) を作成します。 |
| [New-AzureRmNetworkSecurityRuleConfig](/powershell/module/azurerm.network/new-azurermnetworksecurityruleconfig) | VM に対する着信 HTTP ポートと着信 HTTPS ポートを許可する NSG ルールを作成します。 |
| [Set-AzureRmVirtualNetworkSubnetConfig](/powershell/module/azurerm.network/set-azurermvirtualnetworksubnetconfig)| NSG とルート テーブルをサブネットに関連付けます。 |
| [New-AzureRmRouteTable](/powershell/module/azurerm.network/new-azurermroutetable)| すべてのルートのルート テーブルを作成します。 |
| [New-AzureRMRouteConfig](/powershell/module/azurerm.network/new-azurermrouteconfig)| サブネットや VM 経由のインターネットでトラフィックをルーティングするルートを作成します。 |
| [New-AzureRmVM](/powershell/module/azurerm.compute/new-azurermvm) | 仮想マシンを作成し、NIC をアタッチします。 このコマンドでは、使用する仮想マシン イメージと管理者の資格情報も指定します。 |
| [Remove-AzureRmResourceGroup](/powershell/module/azurerm.resources/remove-azurermresourcegroup)  | リソース グループとそれに含まれるすべてのリソースを削除します。 |

## <a name="next-steps"></a>次のステップ

Azure PowerShell の詳細については、[Azure PowerShell のドキュメント](https://docs.microsoft.com/powershell/azure/overview)を参照してください。

その他のネットワーク PowerShell のサンプル スクリプトは、[Azure のネットワーク概要のドキュメント](../powershell-samples.md?toc=%2fazure%2fnetworking%2ftoc.json)に関するページで確認できます。
