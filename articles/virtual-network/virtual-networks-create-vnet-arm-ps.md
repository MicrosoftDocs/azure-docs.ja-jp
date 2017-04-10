---
title: "仮想ネットワークの作成 - Azure PowerShell | Microsoft Docs"
description: "PowerShell を使用した仮想ネットワークの作成方法を説明します。"
services: virtual-network
documentationcenter: 
author: jimdial
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: a31f4f12-54ee-4339-b968-1a8097ca77d3
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/03/2017
ms.author: jdial
ms.custom: H1Hack27Feb2017
translationtype: Human Translation
ms.sourcegitcommit: 63f2f6dde56c1b5c4b3ad2591700f43f6542874d
ms.openlocfilehash: d229d6add2c06952eaf662bdacc92a5163b1e945
ms.lasthandoff: 02/28/2017


---
# <a name="create-a-virtual-network-using-powershell"></a>PowerShell を使用して仮想ネットワークを作成する

[!INCLUDE [virtual-networks-create-vnet-intro](../../includes/virtual-networks-create-vnet-intro-include.md)]

Azure には、Azure Resource Manager とクラシックという&2; 種類のデプロイメント モデルがあります。 Resource Manager デプロイメント モデルを使用してリソースを作成することをお勧めします。 2 つのモデルの違いの詳細については、[Azure のデプロイメント モデルの概要](../azure-resource-manager/resource-manager-deployment-model.md)に関する記事を参照してください。
 
この記事では、PowerShellを使用して Resource Manager デプロイメント モデルで VNet を作成する方法について説明します。 他のツールを使用して Resource Manager で VNet を作成することや、次の一覧から別のオプションを選択してクラシック デプロイメント モデルで VNet を作成することもできます。

> [!div class="op_single_selector"]
> * [ポータル](virtual-networks-create-vnet-arm-pportal.md)
> * [PowerShell](virtual-networks-create-vnet-arm-ps.md)
> * [CLI](virtual-networks-create-vnet-arm-cli.md)
> * [テンプレート](virtual-networks-create-vnet-arm-template-click.md)
> * [ポータル (クラシック)](virtual-networks-create-vnet-classic-pportal.md)
> * [PowerShell (クラシック)](virtual-networks-create-vnet-classic-netcfg-ps.md)
> * [CLI (クラシック)](virtual-networks-create-vnet-classic-cli.md)

[!INCLUDE [virtual-networks-create-vnet-scenario-include](../../includes/virtual-networks-create-vnet-scenario-include.md)]

## <a name="create-a-virtual-network"></a>仮想ネットワークの作成

Power Shell を使って仮想ネットワークを作成するには、次の順に従います。

1. 「[Azure PowerShell のインストールと構成](/powershell/azureps-cmdlets-docs) 」の手順を実行し、PowerShell をインストール、構成します。

2. 必要に応じて、次に示すように、新しいリソース グループを作成します。 このシナリオでは、 *TestRG*という名前のリソース グループを作成します。 リソース グループの詳細については、「[Azure Resource Manager の概要](../azure-resource-manager/resource-group-overview.md)」を参照してください。

    ```powershell   
    New-AzureRmResourceGroup -Name TestRG -Location centralus
    ```

    予想される出力:

        ResourceGroupName : TestRG
        Location          : centralus
        ProvisioningState : Succeeded
        Tags              :
        ResourceId        : /subscriptions/[Subscription Id]/resourceGroups/TestRG    
3. *TestVNet*という名前の新しい VNet を作成します。

    ```powershell
    New-AzureRmVirtualNetwork -ResourceGroupName TestRG -Name TestVNet `
    -AddressPrefix 192.168.0.0/16 -Location centralus
    ```

    予想される出力:

        Name                  : TestVNet
        ResourceGroupName     : TestRG
        Location              : centralus
        Id                    : /subscriptions/[Subscription Id]/resourceGroups/TestRG/providers/Microsoft.Network/virtualNetworks/TestVNet
        Etag                   : W/"[Id]"
        ProvisioningState          : Succeeded
        Tags                       : 
        AddressSpace               : {
                                   "AddressPrefixes": [
                                     "192.168.0.0/16"
                                   ]
                                  }
        DhcpOptions                : {}
        Subnets                    : []
        VirtualNetworkPeerings     : []
4. 仮想ネットワーク オブジェクトを変数に格納します。

    ```powershell
    $vnet = Get-AzureRmVirtualNetwork -ResourceGroupName TestRG -Name TestVNet
    ```

   > [!TIP]
   > `$vnet = New-AzureRmVirtualNetwork -ResourceGroupName TestRG -Name TestVNet -AddressPrefix 192.168.0.0/16 -Location centralus` を実行すると手順 3 と 4 を組み合わせることができます。
   > 

5. 新しい VNet 変数にサブネットを追加します。

    ```powershell
    Add-AzureRmVirtualNetworkSubnetConfig -Name FrontEnd `
    -VirtualNetwork $vnet -AddressPrefix 192.168.1.0/24
    ```

    予想される出力:
   
        Name                  : TestVNet
        ResourceGroupName     : TestRG
        Location              : centralus
        Id                    : /subscriptions/[Subscription Id]/resourceGroups/TestRG/providers/Microsoft.Network/virtualNetworks/TestVNet
        Etag                  : W/"[Id]"
        ProvisioningState     : Succeeded
        Tags                  :
        AddressSpace          : {
                                  "AddressPrefixes": [
                                    "192.168.0.0/16"
                                  ]
                                }
        DhcpOptions           : {}
        Subnets             : [
                                  {
                                    "Name": "FrontEnd",
                                    "AddressPrefix": "192.168.1.0/24"
                                  }
                                ]
        VirtualNetworkPeerings     : []

6. 作成するサブネットごとに、上記の手順 5. を繰り返します。 このシナリオでは、次のコマンドを使用して *BackEnd* サブネットを作成します。

    ```powershell
    Add-AzureRmVirtualNetworkSubnetConfig -Name BackEnd `
    -VirtualNetwork $vnet -AddressPrefix 192.168.2.0/24
    ```

7. サブネットは作成されますが、この時点では、上記の手順 4. で作成する、VNet の取得に使用するローカル変数にのみ格納されています。 Azure に対する変更を保存するには、次のコマンドを実行します。

    ```powershell
    Set-AzureRmVirtualNetwork -VirtualNetwork $vnet
    ```
   
    予想される出力:
   
        Name                  : TestVNet
        ResourceGroupName     : TestRG
        Location              : centralus
        Id                    : /subscriptions/[Subscription Id]/resourceGroups/TestRG/providers/Microsoft.Network/virtualNetworks/TestVNet
        Etag                  : W/"[Id]"
        ProvisioningState     : Succeeded
        Tags                  :
        AddressSpace          : {
                                  "AddressPrefixes": [
                                    "192.168.0.0/16"
                                  ]
                                }
        DhcpOptions           : {
                                  "DnsServers": []
                                }
        Subnets               : [
                                  {
                                    "Name": "FrontEnd",
                                    "Etag": "W/\"[Id]\"",
                                    "Id": "/subscriptions/[Subscription Id]/resourceGroups/TestRG/providers/Microsoft.Network/virtualNetworks/TestVNet/subnets/FrontEnd",
                                    "AddressPrefix": "192.168.1.0/24",
                                    "IpConfigurations": [],
                                    "ProvisioningState": "Succeeded"
                                  },
                                  {
                                    "Name": "BackEnd",
                                    "Etag": "W/\"[Id]\"",
                                    "Id": "/subscriptions/[Subscription Id]/resourceGroups/TestRG/providers/Microsoft.Network/virtualNetworks/TestVNet/subnets/BackEnd",
                                    "AddressPrefix": "192.168.2.0/24",
                                    "IpConfigurations": [],
                                    "ProvisioningState": "Succeeded"
                                  }
                                ]
        VirtualNetworkPeerings : []

## <a name="next-steps"></a>次のステップ

次の接続方法を確認してください。

- 仮想マシン (VM) を仮想ネットワークに接続する方法。[Windows VM の作成](../virtual-machines/virtual-machines-windows-ps-create.md)に関する記事をご覧ください。 この記事の手順で VNet とサブネットを作成する代わりに、VM を接続する既存の VNet とサブネットを選択できます。
- 仮想ネットワークを他の仮想ネットワークに接続する方法。[VNet の接続](../vpn-gateway/vpn-gateway-vnet-vnet-rm-ps.md)に関する記事をご覧ください。
- サイト間の仮想プライベート ネットワーク (VPN) または ExpressRoute 回線を使用して、仮想ネットワークをオンプレミスのネットワークに接続する方法。 詳しくは、[サイト間 VPN を使用したオンプレミスのネットワークへの VNet の接続](../vpn-gateway/vpn-gateway-howto-multi-site-to-site-resource-manager-portal.md)に関する記事と「[ExpressRoute 回線への VNet のリンク](../expressroute/expressroute-howto-linkvnet-arm.md)」をご覧ください。

