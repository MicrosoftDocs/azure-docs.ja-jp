---
title: Azure Virtual Network でのルーティングの制御 - CLI - クラシック | Microsoft Docs
description: クラシック デプロイ モデルで Azure CLI を使用して VNet でのルーティングを制御する方法を説明する
services: virtual-network
documentationcenter: na
author: genlin
manager: dcscontentpm
editor: ''
tags: azure-service-management
ms.assetid: ca2b4638-8777-4d30-b972-eb790a7c804f
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/15/2016
ms.author: genli
ms.openlocfilehash: 1193145b315175e6394db4caf93ab2e76a942ed9
ms.sourcegitcommit: ca359c0c2dd7a0229f73ba11a690e3384d198f40
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/17/2019
ms.locfileid: "71058789"
---
# <a name="control-routing-and-use-virtual-appliances-classic-using-the-azure-cli"></a>Azure CLI を使用してルーティングを制御し仮想アプライアンス (クラシック) を使用する

> [!div class="op_single_selector"]
> * [PowerShell](tutorial-create-route-table-powershell.md)
> * [Azure CLI](tutorial-create-route-table-cli.md)
> * [PowerShell (クラシック)](virtual-network-create-udr-classic-ps.md)
> * [CLI (クラシック)](virtual-network-create-udr-classic-cli.md)

[!INCLUDE [virtual-network-create-udr-intro-include.md](../../includes/virtual-network-create-udr-intro-include.md)]

[!INCLUDE [azure-arm-classic-important-include](../../includes/azure-arm-classic-important-include.md)]

この記事では、クラシック デプロイ モデルについて説明します。 [Resource Manager デプロイ モデルでルーティングを制御し、仮想アプライアンスを使用する](tutorial-create-route-table-cli.md)こともできます。

[!INCLUDE [virtual-network-create-udr-scenario-include.md](../../includes/virtual-network-create-udr-scenario-include.md)]

以下の Azure CLI のサンプル コマンドでは、上記シナリオに基づいて単純な環境が既に作成されていると想定します。 このドキュメントに示されているようにコマンドを実行するのであれば、「 [Azure CLI を使用して VNet (クラシック) を作成する](virtual-networks-create-vnet-classic-cli.md)」に示されている環境を構築します。

[!INCLUDE [azure-cli-prerequisites-include.md](../../includes/azure-cli-prerequisites-include.md)]

## <a name="create-the-udr-for-the-front-end-subnet"></a>フロントエンドのサブネットの UDR を作成する
上記のシナリオに基づいて、フロントエンドのサブネットに必要なルート テーブルとルートを作成するには、次の手順に従います。

1. 次のコマンドを実行して、クラシック モードに切り替えます。

    ```azurecli
    azure config mode asm
    ```

    出力:

        info:    New mode is asm

2. 次のコマンドを実行して、フロントエンドのサブネットのルート テーブルを作成します。

    ```azurecli
    azure network route-table create -n UDR-FrontEnd -l uswest
    ```
   
    出力:
   
        info:    Executing command network route-table create
        info:    Creating route table "UDR-FrontEnd"
        info:    Getting route table "UDR-FrontEnd"
        data:    Name                            : UDR-FrontEnd
        data:    Location                        : West US
        info:    network route-table create command OK
   
    パラメーター:
   
   * **-l (または --location)** 。 NSG が作成される Azure リージョンです。 ここでは、 *westus*です。
   * **-n (または --name)** 。 新しい NSG の名前です。 ここでは、 *NSG-FrontEnd*です。
3. バックエンドのサブネット (192.168.2.0/24) 宛てのすべてのトラフィックを **FW1** VM (192.168.0.4) に送信するために、次のコマンドを実行して、上記で作成済みのルート テーブル内にルートを作成します。

    ```azurecli
    azure network route-table route set -r UDR-FrontEnd -n RouteToBackEnd -a 192.168.2.0/24 -t VirtualAppliance -p 192.168.0.4
    ```

    出力:
   
        info:    Executing command network route-table route set
        info:    Getting route table "UDR-FrontEnd"
        info:    Setting route "RouteToBackEnd" in a route table "UDR-FrontEnd"
        info:    network route-table route set command OK
   
    パラメーター:
   
   * **-r (または --route-table-name)** 。 ルートが追加されるルート テーブルの名前です。 ここでは、 *UDR-FrontEnd*です。
   * **-a (または --address-prefix)** 。 パケットの送信先であるサブネットのアドレス プレフィックスです。 ここでは、 *192.168.2.0/24*です。
   * **-t (または --next-hop-type)** 。 トラフィックの送信先となるオブジェクトの種類です。 指定できる値は *VirtualAppliance*、*VirtualNetworkGateway*、*VNETLocal*、*Internet*、または *None* です。
   * **-p (または --next-hop-ip-address**)。 次のホップ先の IP アドレスです。 ここでは、 *192.168.0.4*です。
4. 次のコマンドを実行して、作成したルート テーブルを **FrontEnd** サブネットに関連付けます。

    ```azurecli
    azure network vnet subnet route-table add -t TestVNet -n FrontEnd -r UDR-FrontEnd
    ```
   
    出力:
   
        info:    Executing command network vnet subnet route-table add
        info:    Looking up the subnet "FrontEnd"
        info:    Looking up network configuration
        info:    Looking up network gateway route tables in virtual network "TestVNet" subnet "FrontEnd"
        info:    Associating route table "UDR-FrontEnd" and subnet "FrontEnd"
        info:    Looking up network gateway route tables in virtual network "TestVNet" subnet "FrontEnd"
        data:    Route table name                : UDR-FrontEnd
        data:      Location                      : West US
        data:      Routes:
        info:    network vnet subnet route-table add command OK    
   
    パラメーター:
   
   * **-t (または --vnet-name)** 。 サブネットが置かれている VNet の名前です。 ここでは、 *TestVNet*です。
   * **-n (または --subnet-name**)。 ルート テーブルが追加されるサブネットの名前です。 ここでは、 *FrontEnd*です。

## <a name="create-the-udr-for-the-back-end-subnet"></a>バックエンドのサブネットの UDR を作成する
シナリオに基づいて、バックエンドのサブネットに必要なルート テーブルとルートを作成するには、次の手順に従います。

1. 次のコマンドを実行して、バックエンドのサブネットのルート テーブルを作成します。

    ```azurecli
    azure network route-table create -n UDR-BackEnd -l uswest
    ```

2. フロントエンドのサブネット (192.168.1.0/24) 宛てのすべてのトラフィックを **FW1** VM (192.168.0.4) に送信するために、次のコマンドを実行して、上記で作成済みのルート テーブル内にルートを作成します。

    ```azurecli
    azure network route-table route set -r UDR-BackEnd -n RouteToFrontEnd -a 192.168.1.0/24 -t VirtualAppliance -p 192.168.0.4
    ```

3. 次のコマンドを実行して、上記で作成したルート テーブルを **BackEnd** サブネットに関連付けます。

    ```azurecli
    azure network vnet subnet route-table add -t TestVNet -n BackEnd -r UDR-BackEnd
    ```

