---
title: Azure PowerShell スクリプトのサンプル - Azure PowerShell による複数の Web サイトの負荷分散 |Microsoft Docs
description: Azure PowerShell スクリプトのサンプル - 同じ仮想マシンへの複数の Web サイトの負荷分散
services: load-balancer
documentationcenter: load-balancer
author: georgewallace
manager: timlt
editor: tysonn
tags: ''
ms.assetid: ''
ms.service: load-balancer
ms.devlang: powershell
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: infrastructure
ms.date: 05/16/2017
ms.author: gwallace
ms.openlocfilehash: 115efe8a3a98c502931765f67489ded0fa15b7b6
ms.sourcegitcommit: a4efc1d7fc4793bbff43b30ebb4275cd5c8fec77
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/21/2019
ms.locfileid: "56648961"
---
# <a name="load-balance-multiple-websites"></a>複数の Web サイトの負荷分散

このスクリプト サンプルでは、可用性セットのメンバーである 2 つの仮想マシン (VM) による仮想ネットワークを作成します。 ロード バランサーが、2 つの独立した IP アドレスのトラフィックを 2 つの VM に送信します。 スクリプトを実行した後は、Web サーバー ソフトウェアの VM へのデプロイ、および複数の Web サイトのホストを、それぞれ独自の IP アドレスで行うことができます。

必要に応じて、[Azure PowerShell ガイド](https://docs.microsoft.com/powershell/azureps-cmdlets-docs/)の手順に従って Azure PowerShell をインストールし、`Connect-AzAccount` を実行して、Azure との接続を作成します。

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>サンプル スクリプト

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

[!code-powershell[main](../../../powershell_scripts/load-balancer/load-balance-multiple-web-sites-vm/load-balance-multiple-web-sites-vm.ps1  "Load balance multiple web sites")]

## <a name="clean-up-deployment"></a>デプロイのクリーンアップ 

次のコマンドを実行して、リソース グループ、VM、すべての関連リソースを削除します。

```powershell
Remove-AzResourceGroup -Name myResourceGroup
```

## <a name="script-explanation"></a>スクリプトの説明

このスクリプトでは、次のコマンドを使用して、リソース グループ、仮想ネットワーク、ロード バランサー、およびすべての関連リソースを作成します。 表内の各コマンドは、それぞれのドキュメントにリンクされています。

| command | メモ |
|---|---|
| [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) | すべてのリソースを格納するリソース グループを作成します。 |
| [New-AzAvailabilitySet](/powershell/module/az.compute/new-azavailabilityset) | 高可用性を実現する Azure 可用性セットを作成します。 |
| [New-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/new-azvirtualnetworksubnetconfig) | サブネット構成を作成します。 この構成は、仮想ネットワークの作成プロセスで使用されます。 |
| [New-AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork) | 仮想ネットワークを作成します。 |
| [New-AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress) | パブリック IP アドレスを作成します。 |
| [New-AzLoadBalancerFrontendIpConfig](/powershell/module/az.network/new-azloadbalancerfrontendipconfig) | ロード バランサー用のフロントエンド IP 構成を作成します。 |
| [New-AzLoadBalancerBackendAddressPoolConfig](/powershell/module/az.network/new-azloadbalancerbackendaddresspoolconfig) | ロード バランサー用のバックエンド アドレス プール構成を作成します。 |
| [New-AzLoadBalancerProbeConfig](/powershell/module/az.network/new-azloadbalancerprobeconfig) | NLB プローブを作成します。 NLB プローブは、NLB セット内の各 VM の監視に使用します。 アクセス不能になった VM には、トラフィックがルーティングされなくなります。 |
| [New-AzLoadBalancerRuleConfig](/powershell/module/az.network/new-azloadbalancerruleconfig) | NLB 規則を作成します。 このサンプルでは、ポート 80 に対する規則を作成します。 HTTP トラフィックが NLB で受信されると、NLB セット内のいずれかの VM のポート 80 にルーティングされます。 |
| [New-AzLoadBalancer](/powershell/module/az.network/new-azloadbalancer) | ロード バランサーを作成します。 |
| [New-AzNetworkInterfaceIpConfig](/powershell/module/az.network/new-aznetworkinterfaceipconfig) | 仮想ネットワーク インターフェイスの高度な機能を定義します。 |
| [New-AzNetworkInterface](/powershell/module/az.network/new-aznetworkinterface) | ネットワーク インターフェイスを作成します。 |
| [New-AzVMConfig](/powershell/module/az.compute/new-azvmconfig) | VM 構成を作成します。 この構成には、VM 名、オペレーティング システム、管理資格情報などの情報が含まれます。 この構成は、VM の作成時に使用されます。 |
| [New-AzVM](/powershell/module/az.compute/new-azvm) | 仮想マシンを作成します。 |
|[Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) | リソース グループと、それに含まれているすべてのリソースを削除します。 |

## <a name="next-steps"></a>次の手順

Azure PowerShell の詳細については、[Azure PowerShell のドキュメント](https://docs.microsoft.com/powershell/azure/overview)を参照してください。

その他のネットワーク PowerShell のサンプル スクリプトは、[Azure ネットワークの概要ドキュメント](../powershell-samples.md?toc=%2fazure%2fnetworking%2ftoc.json)で確認できます。
