---
title: ゾーンのフロントエンドを使用してロード バランサーを作成する - Azure PowerShell
titlesuffix: Azure Load Balancer
description: Azure PowerShell を使用してゾーン フロントエンドで Standard Load Balancer を作成する方法について説明します
services: load-balancer
documentationcenter: na
author: asudbring
manager: twooley
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.custom: seodec18
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/21/2019
ms.author: allensu
ms.openlocfilehash: a2c637ddbf5b7f5883016afe4a6d58ad9752cfc5
ms.sourcegitcommit: 9a699d7408023d3736961745c753ca3cec708f23
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/16/2019
ms.locfileid: "68275129"
---
#  <a name="create-a-standard-load-balancer-with-zonal-frontend-using-azure-powershell"></a>Azure PowerShell を使用してゾーン フロントエンドで Standard Load Balancer を作成する

この記事では、パブリック IP 標準アドレスを使用してゾーン フロントエンドでパブリック [Standard Load Balancer](https://aka.ms/azureloadbalancerstandard) を作成する手順について説明します。 可用性ゾーンと Standard Load Balancer がどのように機能するかを理解するには、「[Standard Load Balancer と可用性ゾーン](load-balancer-standard-availability-zones.md)」を参照してください。 

Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) を作成してください。

> [!NOTE]
> 可用性ゾーンのサポートは、Azure リソース、リージョン、および VM サイズ ファミリを選択するために使用できます。 使用を開始する方法、および可用性ゾーンを試行する場合にどの Azure リソース、リージョン、および VM サイズ ファミリを使用できるかの詳細については、「[Overview of Availability Zones (可用性ゾーンの概要)](https://docs.microsoft.com/azure/availability-zones/az-overview)」を参照してください。 サポートについては、[StackOverflow](https://stackoverflow.com/questions/tagged/azure-availability-zones) でアクセスするか、または [Azure サポート チケットを開く](../azure-supportability/how-to-create-azure-support-request.md?toc=%2fazure%2fvirtual-network%2ftoc.json)ことができます。

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="log-in-to-azure"></a>Azure にログインする

`Connect-AzAccount` コマンドで Azure サブスクリプションにログインし、画面上の指示に従います。

```azurepowershell-interactive
Connect-AzAccount
```

## <a name="create-resource-group"></a>リソース グループの作成

リソース グループを作成するには、次のコマンドを使用します。

```azurepowershell-interactive
New-AzResourceGroup -Name myResourceGroupZLB -Location westeurope
```

## <a name="create-a-public-ip-standard"></a>パブリック IP Standard の作成 
パブリック IP Standard を作成するには、次のコマンドを使用します。

```azurepowershell-interactive
$publicIp = New-AzPublicIpAddress -ResourceGroupName myResourceGroupZLB -Name 'myPublicIPZonal' `
  -Location westeurope -AllocationMethod Static -Sku Standard -zone 1
```

## <a name="create-a-front-end-ip-configuration-for-the-website"></a>Web サイトのフロントエンド IP 構成の作成

フロントエンド IP 構成を作成するには、次のコマンドを使用します。

```azurepowershell-interactive
$feip = New-AzLoadBalancerFrontendIpConfig -Name 'myFrontEnd' -PublicIpAddress $publicIp
```

## <a name="create-the-back-end-address-pool"></a>バックエンド アドレス プールの作成

バックエンド アドレス プールを作成するには、次のコマンドを使用します。

```azurepowershell-interactive
$bepool = New-AzLoadBalancerBackendAddressPoolConfig -Name 'myBackEndPool'
```

## <a name="create-a-load-balancer-probe-on-port-80"></a>ポート 80 でのロード バランサー プローブの作成

ポート 80 でロード バランサーの正常性プローブを作成するには、次のコマンドを使用します。

```azurepowershell-interactive
$probe = New-AzLoadBalancerProbeConfig -Name 'myHealthProbe' -Protocol Http -Port 80 `
  -RequestPath / -IntervalInSeconds 360 -ProbeCount 5
```

## <a name="create-a-load-balancer-rule"></a>ロード バランサー規則の作成
 ロード バランサー ルールを作成するには、次のコマンドを使用します。

```azurepowershell-interactive
   $rule = New-AzLoadBalancerRuleConfig -Name HTTP -FrontendIpConfiguration $feip -BackendAddressPool  $bepool -Probe $probe -Protocol Tcp -FrontendPort 80 -BackendPort 80
```

## <a name="create-a-load-balancer"></a>ロード バランサーの作成
Standard Load Balancer を作成するには、次のコマンドを使用します。

```azurepowershell-interactive
$lb = New-AzLoadBalancer -ResourceGroupName myResourceGroupZLB -Name 'MyLoadBalancer' -Location westeurope `
  -FrontendIpConfiguration $feip -BackendAddressPool $bepool `
  -Probe $probe -LoadBalancingRule $rule -Sku Standard
```

## <a name="next-steps"></a>次の手順
- [Standard Load Balancer と可用性ゾーン](load-balancer-standard-availability-zones.md)を確認します。