---
title: 強制トンネリングで KMS ライセンス認証を有効にするために Azure カスタム ルートを使用する | Microsoft Docs
description: Azure で強制トンネリングを使用している場合に、Azure カスタム ルートを使用して KMS ライセンス認証を有効にする方法について説明します。
services: virtual-machines-windows, azure-resource-manager
documentationcenter: ''
author: genlin
manager: dcscontentpm
editor: ''
tags: top-support-issue, azure-resource-manager
ms.service: virtual-machines-windows
ms.workload: na
ms.tgt_pltfrm: vm-windows
ms.topic: troubleshooting
ms.date: 12/20/2018
ms.author: genli
ms.openlocfilehash: d554629c4a03b81ee3c04d27f6365c1a8734c952
ms.sourcegitcommit: ca359c0c2dd7a0229f73ba11a690e3384d198f40
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/17/2019
ms.locfileid: "71058189"
---
# <a name="windows-activation-fails-in-forced-tunneling-scenario"></a>強制トンネリングを使用したライセンス認証の問題

この記事では、サイト間 VPN 接続または ExpressRoute シナリオで強制トンネリングを有効にしている場合に発生する可能性がある KMS ライセンス認証の問題を解決する方法について説明します。

## <a name="symptom"></a>症状

Azure 仮想ネットワーク サブネットで[強制トンネリング](../../vpn-gateway/vpn-gateway-forced-tunneling-rm.md)を有効にし、インターネットへのすべてのトラフィックをオンプレミス ネットワークに戻すようにします。 このシナリオでは、Windows を実行する Azure 仮想マシン (VM) が Windows のライセンス認証に失敗します。

## <a name="cause"></a>原因

Azure Windows VM は、Windows のライセンス認証のために Azure KMS サーバーに接続する必要があります。 このライセンス認証では、ライセンス認証要求が Azure のパブリック IP アドレスから送られる必要があります。 強制トンネリング シナリオでは、ライセンス認証要求が Azure のパブリック IP アドレスではなく、オンプレミス ネットワークから送信されるため、ライセンス認証は失敗します。

## <a name="solution"></a>解決策

この問題を解決するには、Azure カスタム ルートを使用して、Azure KMS サーバー に ライセンス認証のトラフィックをルーティングします。

Azure Global クラウドの KMS サーバーの IP アドレスは、23.102.135.246 です。 その DNS 名は、kms.core.windows.net です。 Azure Germany などの他の Azure プラットフォームを使用する場合は、対応する KMS サーバーの IP アドレスを使用する必要があります。 詳細については、後の表を参照してください。

|プラットフォーム| KMS DNS|KMS IP|
|------|-------|-------|
|Azure Global|kms.core.windows.net|23.102.135.246|
|Azure Germany|kms.core.cloudapi.de|51.4.143.248|
|Azure US Government|kms.core.usgovcloudapi.net|23.97.0.13|
|Azure China 21Vianet|kms.core.chinacloudapi.cn|42.159.7.249|


カスタム ルートを追加するには、次の手順を実行します。

### <a name="for-resource-manager-vms"></a>Resource Manager VM の場合

[!INCLUDE [updated-for-az.md](../../../includes/updated-for-az.md)]

> [!NOTE] 
> アクティブ化ではパブリック IP アドレスが使用され、Standard SKU の Load Balancer の構成による影響を受けます。 要件については、「[Azure の送信接続](https://docs.microsoft.com/azure/load-balancer/load-balancer-outbound-connections)」を慎重に確認してください。

1. Azure PowerShell を開き、[Azure サブスクリプションにサインイン](https://docs.microsoft.com/powershell/azure/authenticate-azureps)します。
2. 次のコマンドを実行します。

    ```powershell
    # First, get the virtual network that hosts the VMs that have activation problems. In this case, we get virtual network ArmVNet-DM in Resource Group ArmVNet-DM:

    $vnet = Get-AzVirtualNetwork -ResourceGroupName "ArmVNet-DM" -Name "ArmVNet-DM"

    # Next, create a route table and specify that traffic bound to the KMS IP (23.102.135.246) will go directly out:

    $RouteTable = New-AzRouteTable -Name "ArmVNet-DM-KmsDirectRoute" -ResourceGroupName "ArmVNet-DM" -Location "centralus"

    Add-AzRouteConfig -Name "DirectRouteToKMS" -AddressPrefix 23.102.135.246/32 -NextHopType Internet -RouteTable $RouteTable

    Set-AzRouteTable -RouteTable $RouteTable

    # Next, attach the route table to the subnet that hosts the VMs

    Set-AzVirtualNetworkSubnetConfig -Name "Subnet01" -VirtualNetwork $vnet -AddressPrefix "10.0.0.0/24" -RouteTable $RouteTable

    Set-AzVirtualNetwork -VirtualNetwork $vnet
    ```
3. ライセンス認証の問題がある VM に移動します。 [PsPing](https://docs.microsoft.com/sysinternals/downloads/psping) を使用して、KMS サーバーにアクセスできるかどうかをテストします。

        psping kms.core.windows.net:1688

4. Windows のライセンス認証を試してみて、問題が解決されるかどうかを確認します。

### <a name="for-classic-vms"></a>クラシック VM の場合

1. Azure PowerShell を開き、[Azure サブスクリプションにサインイン](https://docs.microsoft.com/powershell/azure/authenticate-azureps)します。
2. 次のコマンドを実行します。

    ```powershell
    # First, create a new route table:
    New-AzureRouteTable -Name "VNet-DM-KmsRouteGroup" -Label "Route table for KMS" -Location "Central US"

    # Next, get the route table that was created:
    $rt = Get-AzureRouteTable -Name "VNet-DM-KmsRouteTable"

    # Next, create a route:
    Set-AzureRoute -RouteTable $rt -RouteName "AzureKMS" -AddressPrefix "23.102.135.246/32" -NextHopType Internet

    # Apply the KMS route table to the subnet that hosts the problem VMs (in this case, we apply it to the subnet that's named Subnet-1):
    Set-AzureSubnetRouteTable -VirtualNetworkName "VNet-DM" -SubnetName "Subnet-1" 
    -RouteTableName "VNet-DM-KmsRouteTable"
    ```

3. ライセンス認証の問題がある VM に移動します。 [PsPing](https://docs.microsoft.com/sysinternals/downloads/psping) を使用して、KMS サーバーにアクセスできるかどうかをテストします。

        psping kms.core.windows.net:1688

4. Windows のライセンス認証を試してみて、問題が解決されるかどうかを確認します。

## <a name="next-steps"></a>次の手順

- [KMS クライアントのセットアップ キー](https://docs.microsoft.com/windows-server/get-started/kmsclientkeys
)
- [ライセンス認証の方法の確認と選択](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/jj134256(v=ws.11)
)
