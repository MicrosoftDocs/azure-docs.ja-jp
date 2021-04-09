---
title: Azure Load Balancer の分散モードの構成
titleSuffix: Azure Load Balancer
description: この記事では、ソース IP アフィニティをサポートするための Azure Load Balancer に対する分散モードの構成について説明します。
services: load-balancer
documentationcenter: na
author: asudbring
ms.service: load-balancer
ms.devlang: na
ms.topic: how-to
ms.custom: seodec18
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/04/2021
ms.author: allensu
ms.openlocfilehash: 2d8d5d84d32cdb8cc813d033f3f3fbb453b538fe
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "101739918"
---
# <a name="configure-the-distribution-mode-for-azure-load-balancer"></a>Azure Load Balancer の分散モードを構成する

Azure Load Balancer では、アプリケーションにトラフィックを分散するための 2 つの分散モードがサポートされています。

* ハッシュベース
* 発信元 IP アフィニティ

この記事では、Azure Load Balancer の分散モードを構成する方法について説明します。


## <a name="configure-distribution-mode"></a>分散モードを構成する

---

# <a name="azure-portal"></a>[**Azure portal**](#tab/azure-portal)

分散モードの構成を変更するには、ポータルで負荷分散規則を変更します。

1. Azure portal にサインインし、 **[リソース グループ]** をクリックして、変更するロード バランサーが含まれているリソース グループを見つけます。
2. ロード バランサーの概要画面で、 **[設定]** の **[負荷分散規則]** を選択します。
3. [負荷分散規則] 画面で、分散モードを変更する負荷分散規則を選択します。
4. 規則の **[セッション永続化]** ドロップ ダウン ボックスを変更することで、分散モードが変更されます。 

次のオプションを使用できます。 

* **[None (hash-based)]\(なし (ハッシュベース)\)** - 同じクライアントからの連続した要求が、任意の仮想マシンによって処理できることを指定します。
* **[Client IP (source IP affinity two-tuple)]\(クライアント IP (ソース IP アフィニティ 2 タプル)\)** - 同じクライアント IP アドレスからの連続する要求が、同じ仮想マシンによって処理されることを指定します。
* **[Client IP and protocol (source IP affinity three-tuple)]\(クライアント IP とプロトコル (ソース IP アフィニティ 3 タプル)\)** - 同じクライアント IP アドレスとプロトコルの組み合わせからの連続する要求が、同じ仮想マシンによって処理されることを指定します。

5. 分散モードを選択し、 **[保存]** を選択します。

:::image type="content" source="./media/load-balancer-distribution-mode/session-persistence.png" alt-text="負荷分散規則のセッションの永続化を変更します。" border="true":::


# <a name="powershell"></a>[**PowerShell**](#tab/azure-powershell)

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

PowerShell を使用して、既存の負荷分散規則のロード バランサーの分散設定を変更します。 次のコマンドで、分散モードが更新されます。 

```azurepowershell-interactive
$lb = Get-AzLoadBalancer -Name MyLoadBalancer -ResourceGroupName MyResourceGroupLB
$lb.LoadBalancingRules[0].LoadDistribution = 'default'
Set-AzLoadBalancer -LoadBalancer $lb
```

`LoadDistribution` 要素の値を、必要な負荷分散の種類に合わせて設定します。 

* 2 タプル (ソース IP と接続先 IP) の負荷分散の場合は **SourceIP** を指定します。 

* 3 タプル (ソース IP、接続先 IP、プロトコルの種類) の負荷分散の場合は **SourceIPProtocol** を指定します。 

* 既定の 5 タプルの負荷分散の動作の場合は **Default** を指定します。

# <a name="cli"></a>[**CLI**](#tab/azure-cli)

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

Azure CLI を使用して、既存の負荷分散規則のロード バランサーの分散設定を変更します。  次のコマンドで、分散モードが更新されます。

```azurecli-interactive
az network lb rule update \
    --lb-name myLoadBalancer \
    --load-distribution Default \
    --name myHTTPRule \
    --resource-group myResourceGroupLB 
```
`--load-distribution` の値を、必要な負荷分散の種類に合わせて設定します。

* 2 タプル (ソース IP と接続先 IP) の負荷分散の場合は **SourceIP** を指定します。 

* 3 タプル (ソース IP、接続先 IP、プロトコルの種類) の負荷分散の場合は **SourceIPProtocol** を指定します。 

* 既定の 5 タプルの負荷分散の動作の場合は **Default** を指定します。

この記事で使用されているコマンドの詳細については、「[az network lb rule update](/cli/azure/network/lb/rule#az_network_lb_rule_update)」を参照してください

---

## <a name="next-steps"></a>次のステップ

* [Azure Load Balancer の概要](load-balancer-overview.md)
* [インターネットに接続するロード バランサーの構成の開始](quickstart-load-balancer-standard-public-powershell.md)
* [ロード バランサーのアイドル TCP タイムアウト設定の構成](load-balancer-tcp-idle-timeout.md)