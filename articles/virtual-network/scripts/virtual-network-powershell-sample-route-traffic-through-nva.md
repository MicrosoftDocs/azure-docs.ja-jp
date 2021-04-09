---
title: NVA 経由のトラフィックのルーティング - Azure PowerShell スクリプト サンプル
description: Azure PowerShell スクリプト サンプル - ファイアウォール NVA 経由のトラフィックのルーティング
services: virtual-network
documentationcenter: virtual-network
author: KumudD
manager: mtillman
ms.service: virtual-network
ms.devlang: powershell
ms.topic: sample
ms.tgt_pltfrm: ''
ms.workload: infrastructure
ms.date: 03/20/2018
ms.author: kumud
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: f0bc7ddde7964ecdd003b951a8562a840b8b88e4
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "98223212"
---
# <a name="route-traffic-through-a-network-virtual-appliance-script-sample"></a>ネットワーク仮想アプライアンス経由のトラフィックのルーティングのスクリプト サンプル

このスクリプト サンプルは、フロントエンド サブネットとバックエンド サブネットを含む仮想ネットワークを作成します。 2 つのサブネット間でトラフィックをルーティングするために、IP 転送を有効にした VM も作成します。 スクリプトの実行後には、ファイアウォール アプリケーションなどのネットワーク ソフトウェアを VM にデプロイできます。

Azure [Cloud Shell](https://shell.azure.com/powershell) から、またはローカルの PowerShell インストールからスクリプトを実行することができます。 PowerShell をローカルで使用する場合、このスクリプトでは Az PowerShell モジュール バージョン 5.4.1 以降が必要になります。 インストールされているバージョンを確認するには、`Get-Module -ListAvailable Az` を実行します。 アップグレードする必要がある場合は、[Azure PowerShell モジュールのインストール](/powershell/azure/install-Az-ps)に関するページを参照してください。 PowerShell をローカルで実行している場合、`Connect-AzAccount` を実行して Azure との接続を作成することも必要です。

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>サンプル スクリプト

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

[!code-azurepowershell-interactive[main](../../../powershell_scripts/virtual-network/route-traffic-through-nva/route-traffic-through-nva.ps1 "Route traffic through a network virtual appliance")]

## <a name="clean-up-deployment"></a>デプロイのクリーンアップ

次のコマンドを実行して、リソース グループ、VM、およびすべての関連リソースを削除します。

```powershell
Remove-AzResourceGroup -Name myResourceGroup -Force
```

## <a name="script-explanation"></a>スクリプトの説明

このスクリプトでは次のコマンドを使用して、リソース グループ、仮想ネットワーク、およびネットワーク セキュリティ グループを作成します。 以下の表の各コマンドは、それぞれのドキュメントにリンクされています。

| コマンド | Notes |
|---|---|
| [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup)  | すべてのリソースを格納するリソース グループを作成します。 |
| [New-AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork) | Azure 仮想ネットワークとフロントエンド サブネットを作成します。 |
| [New-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/new-azvirtualnetworksubnetconfig) | バックエンドおよび DMZ サブネットを作成します。 |
| [New-AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress) | インターネットから VM にアクセスするためのパブリック IP アドレスを作成します。 |
| [New-AzNetworkInterface](/powershell/module/az.network/new-aznetworkinterface) | 仮想ネットワーク インターフェイスを作成し、IP 転送を有効にします。 |
| [New-AzNetworkSecurityGroup](/powershell/module/az.network/new-aznetworksecuritygroup) | ネットワーク セキュリティ グループ (NSG) を作成します。 |
| [New-AzNetworkSecurityRuleConfig](/powershell/module/az.network/new-aznetworksecurityruleconfig) | VM に対する着信 HTTP ポートと着信 HTTPS ポートを許可する NSG ルールを作成します。 |
| [Set-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/set-azvirtualnetworksubnetconfig)| NSG とルート テーブルをサブネットに関連付けます。 |
| [New-AzRouteTable](/powershell/module/az.network/new-azroutetable)| すべてのルートのルート テーブルを作成します。 |
| [New-AzRouteConfig](/powershell/module/az.network/new-azrouteconfig)| サブネットや VM 経由のインターネットでトラフィックをルーティングするルートを作成します。 |
| [New-AzVM](/powershell/module/az.compute/new-azvm) | 仮想マシンを作成し、NIC をアタッチします。 このコマンドでは、使用する仮想マシン イメージと管理者の資格情報も指定します。 |
| [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup)  | リソース グループとそれに含まれるすべてのリソースを削除します。 |

## <a name="next-steps"></a>次のステップ

Azure PowerShell の詳細については、[Azure PowerShell のドキュメント](/powershell/azure/)を参照してください。

他の仮想ネットワークの PowerShell スクリプト サンプルについては、[仮想ネットワークの PowerShell サンプル](../powershell-samples.md)に関するページを参照してください。