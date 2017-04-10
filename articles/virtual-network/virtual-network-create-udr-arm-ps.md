---
title: "Azure でルーティングと仮想アプライアンスを制御する - PowerShell | Microsoft Docs"
description: "PowerShell を使用してルーティングと仮想アプライアンスを制御する方法を説明します。"
services: virtual-network
documentationcenter: na
author: jimdial
manager: carmonm
editor: 
tags: azure-resource-manager
ms.assetid: 9582fdaa-249c-4c98-9618-8c30d496940f
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/23/2016
ms.author: jdial
translationtype: Human Translation
ms.sourcegitcommit: 6d749e5182fbab04adc32521303095dab199d129
ms.openlocfilehash: 3ab24f193c74449ae7414b4ea0675c0aae0211f4
ms.lasthandoff: 03/22/2017


---
# <a name="create-user-defined-routes-udr-using-powershell"></a>PowerShell を使用して、ユーザー定義のルート (UDR) を作成する

> [!div class="op_single_selector"]
> * [PowerShell](virtual-network-create-udr-arm-ps.md)
> * [Azure CLI](virtual-network-create-udr-arm-cli.md)
> * [テンプレート](virtual-network-create-udr-arm-template.md)
> * [PowerShell (クラシック)](virtual-network-create-udr-classic-ps.md)
> * [CLI (クラシック)](virtual-network-create-udr-classic-cli.md)


[!INCLUDE [virtual-network-create-udr-intro-include.md](../../includes/virtual-network-create-udr-intro-include.md)]

> [!IMPORTANT]
> Azure リソースを使用する前に、Azure は現在、Azure Resource Manager デプロイ モデルとクラシック デプロイ モデルの 2 種類を備えていることを理解しておくことが重要です。 Azure リソースを使用する前に、必ず [デプロイ モデルとツール](../azure-resource-manager/resource-manager-deployment-model.md) について知識をつけておいてください。 この記事の上部にあるタブをクリックすると、さまざまなツールについてのドキュメントを参照できます。
>

この記事では、リソース マネージャーのデプロイ モデルについて説明します。 [クラシック デプロイ モデルで UDR を作成](virtual-network-create-udr-classic-ps.md)することもできます。

[!INCLUDE [virtual-network-create-udr-scenario-include.md](../../includes/virtual-network-create-udr-scenario-include.md)]

以下の PowerShell のサンプル コマンドでは、上記シナリオに基づいて単純な環境が既に作成されていると想定します。 このドキュメントに表示されているコマンドを実行するには、まず [このテンプレート](http://github.com/telmosampaio/azure-templates/tree/master/IaaS-NSG-UDR-Before)をデプロイしてテスト環境を構築してから **[Azure にデプロイ]**をクリックし、必要に応じて既定のパラメーター値を置き換えてから、ポータルの指示に従います。

[!INCLUDE [azure-ps-prerequisites-include.md](../../includes/azure-ps-prerequisites-include.md)]

## <a name="create-the-udr-for-the-front-end-subnet"></a>フロントエンドのサブネットの UDR を作成する
上記のシナリオに基づいて、フロントエンドのサブネットに必要なルート テーブルとルートを作成するには、次の手順に従います。

1. 送信したバックエンド サブネット (192.168.2.0/24) 宛てのすべてのトラフィックが **FW1** 仮想アプライアンス (192.168.0.4) にルーティングされるようにするためのルートを作成します。

    ```powershell
    $route = New-AzureRmRouteConfig -Name RouteToBackEnd `
    -AddressPrefix 192.168.2.0/24 -NextHopType VirtualAppliance `
    -NextHopIpAddress 192.168.0.4
    ```

2. 上記で作成したルートが含まれる **westus** リージョンに **UDR-FrontEnd** という名前のルート テーブルを作成します。

    ```powershell
    $routeTable = New-AzureRmRouteTable -ResourceGroupName TestRG -Location westus `
    -Name UDR-FrontEnd -Route $route
    ```

3. サブネットが存在する VNet が格納される変数を作成します。 このシナリオでは、VNet の名前は **TestVNet**です。

    ```powershell
    $vnet = Get-AzureRmVirtualNetwork -ResourceGroupName TestRG -Name TestVNet
    ```

4. 上記で作成したルート テーブルを **FrontEnd** サブネットに関連付けます。

    ```powershell
    Set-AzureRmVirtualNetworkSubnetConfig -VirtualNetwork $vnet -Name FrontEnd `
    -AddressPrefix 192.168.1.0/24 -RouteTable $routeTable
    ```

    > [!WARNING]
    > 上のコマンドの出力では、仮想ネットワーク構成オブジェクトの内容が示されます。これは、PowerShell を実行しているコンピューターにのみ存在します。 これらの設定を Azure に保存するには、**Set-AzureVirtualNetwork** コマンドレットを実行する必要があります。
    > 

5. 新しいサブネット構成を Azure に保存します。

    ```powershell
    Set-AzureRmVirtualNetwork -VirtualNetwork $vnet
    ```

    予想される出力:
   
        Name              : TestVNet
        ResourceGroupName : TestRG
        Location          : westus
        Id                : /subscriptions/[Subscription Id]/resourceGroups/TestRG/providers/Microsoft.Network/virtualNetworks/TestVNet
        Etag              : W/"[Id]"
        ProvisioningState : Succeeded
        Tags              : 
                            Name         Value
                            ===========  =====
                            displayName  VNet 
   
        AddressSpace      : {
                              "AddressPrefixes": [
                                "192.168.0.0/16"
                              ]
                            }
        DhcpOptions       : {
                              "DnsServers": null
                            }
        NetworkInterfaces : null
        Subnets           : [
                                ...,
                              {
                                "Name": "FrontEnd",
                                "Etag": "W/\"[Id]\"",
                                "Id": "/subscriptions/[Id]/resourceGroups/TestRG/providers/Microsoft.Network/virtualNetworks/TestVNet/subnets/FrontEnd",
                                "AddressPrefix": "192.168.1.0/24",
                                "IpConfigurations": [
                                  {
                                    "Id": "/subscriptions/[Subscription Id]/resourceGroups/TestRG/providers/Microsoft.Network/networkInterfaces/NICWEB2/ipConfigurations/ipconfig1"
                                  },
                                  {
                                    "Id": "/subscriptions/[Subscription Id]/resourceGroups/TestRG/providers/Microsoft.Network/networkInterfaces/NICWEB1/ipConfigurations/ipconfig1"
                                  }
                                ],
                                "NetworkSecurityGroup": {
                                  "Id": "/subscriptions/[Subscription Id]/resourceGroups/TestRG/providers/Microsoft.Network/networkSecurityGroups/NSG-FrontEnd"
                                },
                                "RouteTable": {
                                  "Id": "/subscriptions/[Subscription Id]/resourceGroups/TestRG/providers/Microsoft.Network/routeTables/UDR-FrontEnd"
                                },
                                "ProvisioningState": "Succeeded"
                              },
                                ...
                            ]    

## <a name="create-the-udr-for-the-back-end-subnet"></a>バックエンドのサブネットの UDR を作成する

上記のシナリオに基づいて、バックエンドのサブネットに必要なルート テーブルとルートを作成するには、次の手順に従います。

1. 送信したフロントエンド サブネット (192.168.1.0/24) 宛てのすべてのトラフィックが **FW1** 仮想アプライアンス (192.168.0.4) にルーティングされるようにするためのルートを作成します。

    ```powershell
    $route = New-AzureRmRouteConfig -Name RouteToFrontEnd `
    -AddressPrefix 192.168.1.0/24 -NextHopType VirtualAppliance `
    -NextHopIpAddress 192.168.0.4
    ```

2. 上記で作成したルートが含まれる **uswest** リージョンに **UDR-BackEnd** という名前のルート テーブルを作成します。

    ```
    $routeTable = New-AzureRmRouteTable -ResourceGroupName TestRG -Location westus `
    -Name UDR-BackEnd -Route $route
    ```

3. 上記で作成したルート テーブルを **BackEnd** サブネットに関連付けます。

    ```powershell
    Set-AzureRmVirtualNetworkSubnetConfig -VirtualNetwork $vnet -Name BackEnd `
    -AddressPrefix 192.168.2.0/24 -RouteTable $routeTable
    ```

4. 新しいサブネット構成を Azure に保存します。

    ```powershell
    Set-AzureRmVirtualNetwork -VirtualNetwork $vnet
    ```

    予想される出力:
   
        Name              : TestVNet
        ResourceGroupName : TestRG
        Location          : westus
        Id                : /subscriptions/[Subscription Id]/resourceGroups/TestRG/providers/Microsoft.Network/virtualNetworks/TestVNet
        Etag              : W/"[Id]"
        ProvisioningState : Succeeded
        Tags              : 
                            Name         Value
                            ===========  =====
                            displayName  VNet 
   
        AddressSpace      : {
                              "AddressPrefixes": [
                                "192.168.0.0/16"
                              ]
                            }
        DhcpOptions       : {
                              "DnsServers": null
                            }
        NetworkInterfaces : null
        Subnets           : [
                              ...,
                              {
                                "Name": "BackEnd",
                                "Etag": "W/\"[Id]\"",
                                "Id": "/subscriptions/[Subscription Id]/resourceGroups/TestRG/providers/Microsoft.Network/virtualNetworks/TestVNet/subnets/BackEnd",
                                "AddressPrefix": "192.168.2.0/24",
                                "IpConfigurations": [
                                  {
                                    "Id": "/subscriptions/[Subscription Id]/resourceGroups/TestRG/providers/Microsoft.Network/networkInterfaces/NICSQL2/ipConfigurations/ipconfig1"
                                  },
                                  {
                                    "Id": "/subscriptions/[Subscription Id]/resourceGroups/TestRG/providers/Microsoft.Network/networkInterfaces/NICSQL1/ipConfigurations/ipconfig1"
                                  }
                                ],
                                "NetworkSecurityGroup": {
                                  "Id": "/subscriptions/[Subscription Id]/resourceGroups/TestRG/providers/Microsoft.Network/networkSecurityGroups/NSG-BacEnd"
                                },
                                "RouteTable": {
                                  "Id": "/subscriptions/[Subscription Id]/resourceGroups/TestRG/providers/Microsoft.Network/routeTables/UDR-BackEnd"
                                },
                                "ProvisioningState": "Succeeded"
                              }
                            ]

## <a name="enable-ip-forwarding-on-fw1"></a>FW1 で IP 転送を有効にする
**FW1**で使用される NIC での IP 転送を有効にするには、次の手順に従います。

1. FW1 によって使用される NIC の設定が格納される変数を作成します。 このシナリオでは、NIC の名前は **NICFW1**です。

    ```powershell
    $nicfw1 = Get-AzureRmNetworkInterface -ResourceGroupName TestRG -Name NICFW1
    ```

2. IP 転送を有効にし、NIC の設定を保存します。

    ```powershell
    $nicfw1.EnableIPForwarding = 1
    Set-AzureRmNetworkInterface -NetworkInterface $nicfw1
    ```
   
    予想される出力:
   
        Name                 : NICFW1
        ResourceGroupName    : TestRG
        Location             : westus
        Id                   : /subscriptions/[Subscription Id]/resourceGroups/TestRG/providers/Microsoft.Network/networkInterfaces/NICFW1
        Etag                 : W/"[Id]"
        ProvisioningState    : Succeeded
        Tags                 : 
                               Name         Value                  
                               ===========  =======================
                               displayName  NetworkInterfaces - DMZ
   
        VirtualMachine       : {
                                 "Id": "/subscriptions/[Subscription Id]/resourceGroups/TestRG/providers/Microsoft.Compute/virtualMachines/FW1"
                               }
        IpConfigurations     : [
                                 {
                                   "Name": "ipconfig1",
                                   "Etag": "W/\"[Id]\"",
                                   "Id": "/subscriptions/[Subscription Id]/resourceGroups/TestRG/providers/Microsoft.Network/networkInterfaces/NICFW1/ipConfigurations/ipconfig1",
                                   "PrivateIpAddress": "192.168.0.4",
                                   "PrivateIpAllocationMethod": "Static",
                                   "Subnet": {
                                     "Id": "/subscriptions/[Subscription Id]/resourceGroups/TestRG/providers/Microsoft.Network/virtualNetworks/TestVNet/subnets/DMZ"
                                   },
                                   "PublicIpAddress": {
                                     "Id": "/subscriptions/[Subscription Id]/resourceGroups/TestRG/providers/Microsoft.Network/publicIPAddresses/PIPFW1"
                                   },
                                   "LoadBalancerBackendAddressPools": [],
                                   "LoadBalancerInboundNatRules": [],
                                   "ProvisioningState": "Succeeded"
                                 }
                               ]
        DnsSettings          : {
                                 "DnsServers": [],
                                 "AppliedDnsServers": [],
                                 "InternalDnsNameLabel": null,
                                 "InternalFqdn": null
                               }
        EnableIPForwarding   : True
        NetworkSecurityGroup : null
        Primary              : True


