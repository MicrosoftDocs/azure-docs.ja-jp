---
title: クラウド サービスの複数の VIP
titlesuffix: Azure Load Balancer
description: MultiVIP の概要とクラウド サービスで複数の Vip を設定する方法
services: load-balancer
documentationcenter: na
author: asudbring
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.custom: seodec18
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/25/2017
ms.author: allensu
ms.openlocfilehash: 3e97bea85d4d97b159168b21b4a6e932e655ccfb
ms.sourcegitcommit: 9a699d7408023d3736961745c753ca3cec708f23
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/16/2019
ms.locfileid: "68274700"
---
# <a name="configure-multiple-vips-for-a-cloud-service"></a>クラウド サービスの複数の VIP を構成する

[!INCLUDE [load-balancer-basic-sku-include.md](../../includes/load-balancer-basic-sku-include.md)]

Azure Cloud Services は、Azure によって提供される IP アドレスを使用して、パブリック インターネット経由でアクセスできます。 このパブリック IP アドレスは、クラウド サービス内の仮想マシン (VM) インスタンスではなく、Azure Load Balancer にリンクされているため、VIP (仮想 IP) と呼ばれます。 クラウド サービス内の VM インスタンスは、1 つの VIP を使用してアクセスできます。

ただし、同じクラウド サービスへのエントリ ポイントとして 1 つ以上の VIP が必要な場合のあるシナリオがあります。 たとえば、クラウド サービスが、既定のポート 443 を使用した SSL 接続を必要とする複数の Web サイトをホストしている場合があります。各サイトはそれぞれ異なる顧客 (テナント) 用にホストされています。 このシナリオでは、Web サイトごとに異なるパブリック IP アドレスが必要です。 次の図は、同じパブリック ポートで複数の SSL 証明書を必要とする一般的なマルチテナント Web ホスティングを示しています。

![Multi VIP SSL scenario](./media/load-balancer-multivip/Figure1.png)

前の例では、すべての VIP が同じパブリック ポート (443) を使用しており、トラフィックはすべての Web サイトをホストするクラウド サービスの内部 IP アドレス用の一意のプライベート ポートの 1 つ以上の負荷分散された VM にリダイレクトされます。

> [!NOTE]
> 同じ仮想マシン セットで複数の SQL AlwaysOn 可用性グループ リスナーをホストする場合にも、複数の VIP を使用する必要があります。

VIP は、既定では動的です。つまり、時間の経過と共にクラウド サービスに割り当てられている実際の IP アドレスが変化する可能性があります。 この問題を防ぐために、サービスの VIP を予約することができます。 予約済み VIP の詳細については、[予約済みパブリック IP](../virtual-network/virtual-networks-reserved-public-ip.md) に関する記事をご覧ください。

> [!NOTE]
> VIP と予約済み IP の価格の詳細については、「 [IP アドレスの価格](https://azure.microsoft.com/pricing/details/ip-addresses/) 」をご覧ください。

PowerShell を使用すると、クラウド サービスで使用される VIP を確認できるだけでなく、VIP の追加 と削除、エンドポイントへの VIP の関連付け、特定の VIP での負荷分散の構成を行うことができます。

## <a name="limitations"></a>制限事項

現時点では、マルチ VIP 機能は次のシナリオに制限されています。

* **IaaS のみ**。 マルチ VIP は、VM を含むクラウド サービスでのみ有効にすることができます。 PaaS シナリオのロール インスタンスでマルチ VIP を使用することはできません。
* **PowerShell のみ**。 マルチ VIP は PowerShell でのみ管理できます。

これらの制限は一時的なものであり、いつでも変更される可能性があります。 今後の変更を確認するには、このページに再度アクセスしてください。

## <a name="how-to-add-a-vip-to-a-cloud-service"></a>VIP をクラウド サービスに追加する方法
VIP をサービスを追加するには、次の PowerShell コマンドを実行します。

```powershell
Add-AzureVirtualIP -VirtualIPName Vip3 -ServiceName myService
```

このコマンドでは、次のサンプルのような結果が表示されます。

    OperationDescription OperationId                          OperationStatus
    -------------------- -----------                          ---------------
    Add-AzureVirtualIP   4bd7b638-d2e7-216f-ba38-5221233d70ce Succeeded

## <a name="how-to-remove-a-vip-from-a-cloud-service"></a>VIP をクラウド サービスから削除する方法
上記の例でサービスに追加された VIP を削除するには、次の PowerShell コマンドを実行します。

```powershell
Remove-AzureVirtualIP -VirtualIPName Vip3 -ServiceName myService
```

> [!IMPORTANT]
> エンドポイントが関連付けられていない場合にのみ、VIP を削除できます。


## <a name="how-to-retrieve-vip-information-from-a-cloud-service"></a>クラウド サービスから VIP 情報を取得する方法
クラウド サービスに関連付けられている VIP を取得するには、次の PowerShell スクリプトを実行します。

```powershell
$deployment = Get-AzureDeployment -ServiceName myService
$deployment.VirtualIPs
```

このスクリプトでは、次のサンプルのような結果が表示されます。

    Address         : 191.238.74.148
    IsDnsProgrammed : True
    Name            : Vip1
    ReservedIPName  :
    ExtensionData   :

    Address         :
    IsDnsProgrammed :
    Name            : Vip2
    ReservedIPName  :
    ExtensionData   :

    Address         :
    IsDnsProgrammed :
    Name            : Vip3
    ReservedIPName  :
    ExtensionData   :

この例では、クラウド サービスには次の 3 つの VIP があります。

* **Vip1** は既定の VIP で、IsDnsProgrammedName の値が true に設定されていることがわかります。
* **Vip2** と **Vip3** は IP アドレスがないので使用されません。 VIP にエンドポイントを関連付ける場合のみ使用されます。

> [!NOTE]
> これらをエンドポイントに関連付けると、サブスクリプションは余分な VIP にのみ課金されます。 価格の詳細については、「 [IP アドレスの価格](https://azure.microsoft.com/pricing/details/ip-addresses/)」をご覧ください。

## <a name="how-to-associate-a-vip-to-an-endpoint"></a>VIP をエンドポイントに関連付ける方法

クラウド サービスの VIP をエンドポイントに関連付けるには、次の PowerShell コマンドを実行します。

```powershell
Get-AzureVM -ServiceName myService -Name myVM1 |
    Add-AzureEndpoint -Name myEndpoint -Protocol tcp -LocalPort 8080 -PublicPort 80 -VirtualIPName Vip2 |
    Update-AzureVM
```

このコマンドは、ポート *80* の *Vip2* という VIP にリンクされたエンドポイントを作成し、このエンドポイントを、ポート *8080* で *TCP* を使用する *myService* というクラウド サービスの *myVM1* という VM にリンクします。

構成を確認するには、次の PowerShell コマンドを実行します。

```powershell
$deployment = Get-AzureDeployment -ServiceName myService
$deployment.VirtualIPs
```

出力は次の例のようになります。

    Address         : 191.238.74.148
    IsDnsProgrammed : True
    Name            : Vip1
    ReservedIPName  :
    ExtensionData   :

    Address         : 191.238.74.13
    IsDnsProgrammed :
    Name            : Vip2
    ReservedIPName  :
    ExtensionData   :

    Address         :
    IsDnsProgrammed :
    Name            : Vip3
    ReservedIPName  :
    ExtensionData   :

## <a name="how-to-enable-load-balancing-on-a-specific-vip"></a>特定の VIP で負荷分散を有効にする方法

負荷分散のために、複数の仮想マシンに 1 つの VIP を関連付けることができます。 たとえば、*myService* というクラウド サービスと、*myVM1*、*myVM2* という 2 つの仮想マシンがあるとします。 クラウド サービスには複数の VIP があり、その中の 1 つが *Vip2*です。 *Vip2* のポート *81* のすべてのトラフィックが、ポート *8181* の *myVM1* と *myVM2* の間で分散されていることを確認する場合は、次の PowerShell スクリプトを実行します。

```powershell
Get-AzureVM -ServiceName myService -Name myVM1 |
    Add-AzureEndpoint -Name myEndpoint -LoadBalancedEndpointSetName myLBSet -Protocol tcp -LocalPort 8181 -PublicPort 81 -VirtualIPName Vip2 -DefaultProbe |
    Update-AzureVM

Get-AzureVM -ServiceName myService -Name myVM2 |
    Add-AzureEndpoint -Name myEndpoint -LoadBalancedEndpointSetName myLBSet -Protocol tcp -LocalPort 8181 -PublicPort 81 -VirtualIPName Vip2  -DefaultProbe |
    Update-AzureVM
```

ロード バランサーを更新して異なる VIP を使用することもできます。 たとえば、次の PowerShell コマンドを実行すると、負荷分散セットは Vip1 という VIP を使用するように変更されます。

```powershell
Set-AzureLoadBalancedEndpoint -ServiceName myService -LBSetName myLBSet -VirtualIPName Vip1
```

## <a name="next-steps"></a>次の手順

[Azure Load Balancer 用の Azure Monitor ログ](load-balancer-monitor-log.md)

[インターネットに接続するロード バランサーの概要](load-balancer-internet-overview.md)

[インターネットに接続するロード バランサーの開始](load-balancer-get-started-internet-arm-ps.md)

[仮想ネットワークの概要](../virtual-network/virtual-networks-overview.md)

[予約済み IP REST API](https://msdn.microsoft.com/library/azure/dn722420.aspx)
