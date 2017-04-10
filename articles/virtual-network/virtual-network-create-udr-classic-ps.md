---
title: "Azure Virtual Network でのルーティングの制御 - PowerShell - クラシック | Microsoft Docs"
description: "PowerShell を使用して VNet でのルーティングを制御する方法 | Classic"
services: virtual-network
documentationcenter: na
author: jimdial
manager: carmonm
editor: 
tags: azure-service-management
ms.assetid: d8d07c16-cbe5-4536-acd6-870269346fe3
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/02/2016
ms.author: jdial
translationtype: Human Translation
ms.sourcegitcommit: 6d749e5182fbab04adc32521303095dab199d129
ms.openlocfilehash: e9564d223cb85529f1fa97bc398d35c6debcedae
ms.lasthandoff: 03/22/2017


---
# <a name="control-routing-and-use-virtual-appliances-classic-using-powershell"></a>PowerShell を使用してルーティングを制御し仮想アプライアンス (クラシック) を使用する

> [!div class="op_single_selector"]
> * [PowerShell](virtual-network-create-udr-arm-ps.md)
> * [Azure CLI](virtual-network-create-udr-arm-cli.md)
> * [テンプレート](virtual-network-create-udr-arm-template.md)
> * [PowerShell (クラシック)](virtual-network-create-udr-classic-ps.md)
> * [CLI (クラシック)](virtual-network-create-udr-classic-cli.md)

[!INCLUDE [virtual-network-create-udr-intro-include.md](../../includes/virtual-network-create-udr-intro-include.md)]

> [!IMPORTANT]
> Azure リソースを使用する前に、Azure は現在、Azure Resource Manager デプロイ モデルとクラシック デプロイ モデルの&2; 種類を備えていることを理解しておくことが重要です。 Azure リソースを使用する前に、必ず [デプロイ モデルとツール](../azure-resource-manager/resource-manager-deployment-model.md) について知識をつけておいてください。 この記事の上部にあるオプションを選択すると、さまざまなツールについてのドキュメントを参照できます。 この記事では、クラシック デプロイメント モデルについて説明します。
> 

[!INCLUDE [virtual-network-create-udr-scenario-include.md](../../includes/virtual-network-create-udr-scenario-include.md)]

以下の Azure PowerShell のサンプル コマンドでは、上記シナリオに基づいて単純な環境が既に作成されていると想定します。 このドキュメントに示されているようにコマンドを実行するのであれば、「 [PowerShell を使用して VNet (クラシック) を作成する](virtual-networks-create-vnet-classic-netcfg-ps.md)」に示されている環境を構築します。

[!INCLUDE [azure-ps-prerequisites-include.md](../../includes/azure-ps-prerequisites-include.md)]

## <a name="create-the-udr-for-the-front-end-subnet"></a>フロントエンドのサブネットの UDR を作成する
上記のシナリオに基づいて、フロントエンドのサブネットに必要なルート テーブルとルートを作成するには、次の手順に従います。

1. 次のコマンドを実行して、フロントエンドのサブネットのルート テーブルを作成します。

    ```powershell
    New-AzureRouteTable -Name UDR-FrontEnd -Location uswest `
    -Label "Route table for front end subnet"
    ```

2. バックエンドのサブネット (192.168.2.0/24) 宛てのすべてのトラフィックを **FW1** VM (192.168.0.4) に送信するために、次のコマンドを実行して、上記で作成済みのルート テーブル内にルートを作成します。

    ```powershell
    Get-AzureRouteTable UDR-FrontEnd `
    |Set-AzureRoute -RouteName RouteToBackEnd -AddressPrefix 192.168.2.0/24 `
    -NextHopType VirtualAppliance `
    -NextHopIpAddress 192.168.0.4
    ```

3. 次のコマンドを実行して、ルート テーブルを **FrontEnd** サブネットに関連付けます。

    ```powershell
    Set-AzureSubnetRouteTable -VirtualNetworkName TestVNet `
    -SubnetName FrontEnd `
    -RouteTableName UDR-FrontEnd
    ```

## <a name="create-the-udr-for-the-back-end-subnet"></a>バックエンドのサブネットの UDR を作成する
シナリオに基づいて、バックエンドのサブネットに必要なルート テーブルとルートを作成するには、次の手順に従います。

1. 次のコマンドを実行して、バックエンドのサブネットのルート テーブルを作成します。

    ```powershell
    New-AzureRouteTable -Name UDR-BackEnd `
    -Location uswest `
    -Label "Route table for back end subnet"
    ```

2. フロントエンドのサブネット (192.168.1.0/24) 宛てのすべてのトラフィックを **FW1** VM (192.168.0.4) に送信するために、次のコマンドを実行して、上記で作成済みのルート テーブル内にルートを作成します。

    ```powershell
    Get-AzureRouteTable UDR-BackEnd
    | Set-AzureRoute `
    -RouteName RouteToFrontEnd `
    -AddressPrefix 192.168.1.0/24 `
    -NextHopType VirtualAppliance `
    -NextHopIpAddress 192.168.0.4
    ```

3. 次のコマンドを実行して、上記で作成したルート テーブルを **BackEnd** サブネットに関連付けます。

    ```powershell
    Set-AzureSubnetRouteTable -VirtualNetworkName TestVNet `
    -SubnetName BackEnd `
    -RouteTableName UDR-BackEnd
    ```

## <a name="enable-ip-forwarding-on-the-fw1-vm"></a>FW1 VM で IP 転送を有効にする

FW1 VM で IP 転送を有効にするには、次の手順に従います。

1. IP 転送のステータスを確認するには、次のコマンドを使用します。

    ```powershell
    Get-AzureVM -Name FW1 -ServiceName TestRGFW `
    | Get-AzureIPForwarding
    ```

2. 次のコマンドを実行して、*FW1* VM で IP 転送を有効にします。

    ```powershell
    Get-AzureVM -Name FW1 -ServiceName TestRGFW `
    | Set-AzureIPForwarding -Enable
    ```

