---
title: CLI の例 - Azure CLI を使用して複数の Web サイトを負荷分散する | Microsoft Docs
description: この Azure CLI スクリプトの例では、同じ仮想マシンに複数の Web サイトを負荷分散する方法を示します
services: load-balancer
documentationcenter: load-balancer
author: asudbring
manager: jeconnoc
editor: tysonn
tags: ''
ms.assetid: ''
ms.service: load-balancer
ms.devlang: azurecli
ms.topic: sample
ms.tgt_pltfrm: ''
ms.workload: infrastructure
ms.date: 04/20/2018
ms.author: allensu
ms.openlocfilehash: 63897da887230da74aaaddc464549e9c06ed9543
ms.sourcegitcommit: 9a699d7408023d3736961745c753ca3cec708f23
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/16/2019
ms.locfileid: "68273875"
---
# <a name="azure-cli-script-example-load-balance-multiple-websites"></a>Azure CLI スクリプトの例: 複数の Web サイトの負荷分散

この Azure CLI スクリプトのサンプルでは、可用性セットのメンバーである 2 つの仮想マシン (VM) による仮想ネットワークを作成します。 ロード バランサーが、2 つの独立した IP アドレスのトラフィックを 2 つの VM に送信します。 スクリプトを実行した後は、Web サーバー ソフトウェアの VM へのデプロイ、および複数の Web サイトのホストを、それぞれ独自の IP アドレスで行うことができます。

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>サンプル スクリプト


[!code-azurecli-interactive[main](../../../cli_scripts/load-balancer/load-balance-multiple-web-sites-vm/load-balance-multiple-web-sites-vm.sh  "Load balance multiple web sites")]

## <a name="clean-up-deployment"></a>デプロイのクリーンアップ 

次のコマンドを実行して、リソース グループ、VM、すべての関連リソースを削除します。

```azurecli
az group delete --name myResourceGroup --yes
```

## <a name="script-explanation"></a>スクリプトの説明

このスクリプトでは、次のコマンドを使用して、リソース グループ、仮想ネットワーク、ロード バランサー、およびすべての関連リソースを作成します。 表内の各コマンドは、それぞれのドキュメントにリンクされています。

| command | メモ |
|---|---|
| [az group create](https://docs.microsoft.com/cli/azure/group#az-group-create) | すべてのリソースを格納するリソース グループを作成します。 |
| [az network vnet create](https://docs.microsoft.com/cli/azure/network/vnet#az-network-vnet-create) | Azure 仮想ネットワークとサブネットを作成します。 |
| [az network public-ip create](https://docs.microsoft.com/cli/azure/network/public-ip#az-network-public-ip-create) | 静的 IP アドレスおよび関連付けられる DNS 名を指定してパブリック IP アドレスを作成します。 |
| [az network lb create](https://docs.microsoft.com/cli/azure/network/lb#az-network-lb-create) | Azure ロード バランサーを作成します。 |
| [az network lb probe create](https://docs.microsoft.com/cli/azure/network/lb/probe#az-network-lb-probe-create) | ロード バランサー プローブを作成します。 ロード バランサー プローブは、ロード バランサー セット内の各 VM を監視するために使用されます。 アクセス不能になった VM には、トラフィックがルーティングされなくなります。 |
| [az network lb rule create](https://docs.microsoft.com/cli/azure/network/lb/rule#az-network-lb-rule-create) | ロード バランサー規則を作成します。 このサンプルでは、ポート 80 に対する規則を作成します。 HTTP トラフィックは、ロード バランサーに到着すると、ロード バランサー セット内のいずれかの VM のポート 80 にルーティングされます。 |
| [az network lb frontend-ip create](https://docs.microsoft.com/cli/azure/network/lb/frontend-ip#az-network-lb-frontend-ip-create) | ロード バランサーのフロントエンド IP アドレスを作成します。 |
| [az network lb address-pool create](https://docs.microsoft.com/cli/azure/network/lb/address-pool#az-network-lb-address-pool-create) | バックエンド アドレス プールを作成します。 |
| [az network nic create](https://docs.microsoft.com/cli/azure/network/nic#az-network-nic-create) | 仮想ネットワーク カードを作成し、作成したカードを仮想ネットワークとサブネットに接続します。 |
| [az vm availability-set create](https://docs.microsoft.com/cli/azure/network/lb/rule#az-network-lb-rule-create) | 可用性セットを作成します。 可用性セットでは、障害が発生した場合でもセット全体に影響が及ばないように物理リソース全体に仮想マシンを分散させることで、アプリケーションの稼働時間を確保します。 |
| [az network nic ip-config create](https://docs.microsoft.com/cli/azure/network/nic/ip-config#az-network-nic-ip-config-create) | IP 構成を作成します。 Microsoft.Network/AllowMultipleIpConfigurationsPerNic 機能がサブスクリプションに対して有効になっている必要があります。 --make-primary フラグを使用して、NIC ごとにプライマリ IP 構成として指定できるのは 1 つの構成のみです。 |
| [az vm create](https://docs.microsoft.com/cli/azure/vm/availability-set#az-vm-availability-set-create) | 仮想マシンを作成し、作成したマシンをネットワーク カード、仮想ネットワーク、サブネット、およびNSG に接続します。 このコマンドでは、使用する仮想マシン イメージと管理者の資格情報も指定します。  |
| [az group delete](https://docs.microsoft.com/cli/azure/vm/extension#az-vm-extension-set) | 入れ子になったリソースすべてを含むリソース グループを削除します。 |

## <a name="next-steps"></a>次の手順

Azure CLI の詳細については、[Azure CLI のドキュメント](https://docs.microsoft.com/cli/azure)のページをご覧ください。

その他のネットワーク CLI のサンプル スクリプトは、[Azure のネットワーク概要のドキュメント](../cli-samples.md?toc=%2fazure%2fnetworking%2ftoc.json)に関するページで確認できます。
