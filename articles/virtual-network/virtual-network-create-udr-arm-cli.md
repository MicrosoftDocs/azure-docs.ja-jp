---
title: "Azure CLI 2.0 を使用してルーティングと仮想アプライアンスを制御する | Microsoft Docs"
description: "Azure CLI 2.0 を使用してルーティングと仮想アプライアンスを制御する方法を説明します。"
services: virtual-network
documentationcenter: na
author: jimdial
manager: carmonm
editor: 
tags: azure-resource-manager
ms.assetid: 5452a0b8-21a6-4699-8d6a-e2d8faf32c25
ms.service: virtual-network
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/12/2017
ms.author: jdial
translationtype: Human Translation
ms.sourcegitcommit: 6d749e5182fbab04adc32521303095dab199d129
ms.openlocfilehash: 3a85fa624dc55f31822f00910b6d124c1d37323f
ms.lasthandoff: 03/22/2017


---
# <a name="create-user-defined-routes-udr-using-the-azure-cli-20"></a>Azure CLI 2.0 を使用してユーザー定義ルート (UDR) を作成する

> [!div class="op_single_selector"]
> * [PowerShell](virtual-network-create-udr-arm-ps.md)
> * [Azure CLI](virtual-network-create-udr-arm-cli.md)
> * [テンプレート](virtual-network-create-udr-arm-template.md)
> * [PowerShell (クラシック デプロイメント)](virtual-network-create-udr-classic-ps.md)
> * [CLI (クラシック デプロイメント)](virtual-network-create-udr-classic-cli.md)

## <a name="cli-versions-to-complete-the-task"></a>タスクを完了するための CLI バージョン 

次のいずれかの CLI バージョンを使用してタスクを完了できます。 

- [Azure CLI 1.0](virtual-network-create-udr-arm-cli-nodejs.md) - クラシック デプロイメント モデルと Resource Manager デプロイメント モデル用の CLI 
- [Azure CLI 2.0](#Create-the-UDR-for-the-front-end-subnet) - リソース管理デプロイ モデル用の次世代 CLI (この記事)

[!INCLUDE [virtual-networks-create-nsg-intro-include](../../includes/virtual-networks-create-nsg-intro-include.md)]

[!INCLUDE [virtual-networks-create-nsg-scenario-include](../../includes/virtual-networks-create-nsg-scenario-include.md)]

[!INCLUDE [virtual-network-create-udr-intro-include.md](../../includes/virtual-network-create-udr-intro-include.md)]

[!INCLUDE [virtual-network-create-udr-scenario-include.md](../../includes/virtual-network-create-udr-scenario-include.md)]

以下の Azure CLI のサンプル コマンドでは、上記シナリオに基づいて単純な環境が既に作成されていると想定します。 このドキュメントに表示されているコマンドを実行するには、まず [このテンプレート](http://github.com/telmosampaio/azure-templates/tree/master/IaaS-NSG-UDR-Before)をデプロイしてテスト環境を構築してから **[Azure にデプロイ]**をクリックし、必要に応じて既定のパラメーター値を置き換えてから、ポータルの指示に従います。


## <a name="create-the-udr-for-the-front-end-subnet"></a>フロントエンドのサブネットの UDR を作成する
上記のシナリオに基づいて、フロントエンドのサブネットに必要なルート テーブルとルートを作成するには、次の手順に従います。

1. [az network route-table create](/cli/azure/network/route-table#create) コマンドを使用して、フロントエンド サブネットのルート テーブルを作成します。

    ```azurecli
    az network route-table create \
    --resource-group testrg \
    --location centralus \
    --name UDR-FrontEnd
    ```

    出力:

    ```json
    {
    "etag": "W/\"<guid>\"",
    "id": "/subscriptions/<guid>/resourceGroups/testrg/providers/Microsoft.Network/routeTables/UDR-FrontEnd",
    "location": "centralus",
    "name": "UDR-FrontEnd",
    "provisioningState": "Succeeded",
    "resourceGroup": "testrg",
    "routes": [],
    "subnets": null,
    "tags": null,
    "type": "Microsoft.Network/routeTables"
    }
    ```

2. [az network route-table route create](/cli/azure/network/route-table/route#create) コマンドを使用して、バックエンド サブネット (192.168.2.0/24) 宛てのすべてのトラフィックを **FW1** VM (192.168.0.4) に送信するルートを作成します。

    ```azurecli 
    az network route-table route create \
    --resource-group testrg \
    --name RouteToBackEnd \
    --route-table-name UDR-FrontEnd \
    --address-prefix 192.168.2.0/24 \
    --next-hop-type VirtualAppliance \
    --next-hop-ip-address 192.168.0.4
    ```

    出力:

    ```json
    {
    "addressPrefix": "192.168.2.0/24",
    "etag": "W/\"<guid>\"",
    "id": "/subscriptions/<guid>/resourceGroups/testrg/providers/Microsoft.Network/routeTables/UDR-FrontEnd/routes/RouteToBackEnd",
    "name": "RouteToBackEnd",
    "nextHopIpAddress": "192.168.0.4",
    "nextHopType": "VirtualAppliance",
    "provisioningState": "Succeeded",
    "resourceGroup": "testrg"
    }
    ```
    パラメーター:

    * **--route-table-name**。 ルートが追加されるルート テーブルの名前です。 ここでは、 *UDR-FrontEnd*です。
    * **--address-prefix**。 パケットの送信先であるサブネットのアドレス プレフィックスです。 ここでは、 *192.168.2.0/24*です。
    * **--next-hop-type**。 トラフィックの送信先となるオブジェクトの種類です。 指定できる値は *VirtualAppliance*、*VirtualNetworkGateway*、*VNETLocal*、*Internet*、または *None* です。
    * **--next-hop-ip-address**。 次のホップ先の IP アドレスです。 ここでは、 *192.168.0.4*です。

3. [az network vnet subnet update](/cli/azure/network/vnet/subnet#update) コマンドを実行して、前に作成したルート テーブルを **FrontEnd** サブネットに関連付けます。

    ```azurecli
    az network vnet subnet update \
    --resource-group testrg \
    --vnet-name testvnet \
    --name FrontEnd \
    --route-table UDR-FrontEnd
    ```

    出力:

    ```json
    {
    "addressPrefix": "192.168.1.0/24",
    "etag": "W/\"<guid>\"",
    "id": "/subscriptions/<guid>/resourceGroups/testrg/providers/Microsoft.Network/virtualNetworks/testvnet/subnets/FrontEnd",
    "ipConfigurations": null,
    "name": "FrontEnd",
    "networkSecurityGroup": null,
    "provisioningState": "Succeeded",
    "resourceGroup": "testrg",
    "resourceNavigationLinks": null,
    "routeTable": {
        "etag": null,
        "id": "/subscriptions/<guid>/resourceGroups/testrg/providers/Microsoft.Network/routeTables/UDR-FrontEnd",
        "location": null,
        "name": null,
        "provisioningState": null,
        "resourceGroup": "testrg",
        "routes": null,
        "subnets": null,
        "tags": null,
        "type": null
        }
    }
    ```

    パラメーター:
    
    * **--vnet-name**。 サブネットが置かれている VNet の名前です。 ここでは、 *TestVNet*です。

## <a name="create-the-udr-for-the-back-end-subnet"></a>バックエンドのサブネットの UDR を作成する

上記のシナリオに基づいて、バックエンドのサブネットに必要なルート テーブルとルートを作成するには、次の手順に従います。

1. 次のコマンドを実行して、バックエンドのサブネットのルート テーブルを作成します。

    ```azurecli
    az network route-table create \
    --resource-group testrg \
    --name UDR-BackEnd \
    --location centralus
    ```

2. フロントエンドのサブネット (192.168.1.0/24) 宛てのすべてのトラフィックを **FW1** VM (192.168.0.4) に送信するために、次のコマンドを実行して、上記で作成済みのルート テーブル内にルートを作成します。

    ```azurecli
    az network route-table route create \
    --resource-group testrg \
    --name RouteToFrontEnd \
    --route-table-name UDR-BackEnd \
    --address-prefix 192.168.1.0/24 \
    --next-hop-type VirtualAppliance \
    --next-hop-ip-address 192.168.0.4
    ```

3. 次のコマンドを実行して、上記で作成したルート テーブルを **BackEnd** サブネットに関連付けます。

    ```azurecli
    az network vnet subnet update \
    --resource-group testrg \
    --vnet-name testvnet \
    --name BackEnd \
    --route-table UDR-BackEnd
    ```

## <a name="enable-ip-forwarding-on-fw1"></a>FW1 で IP 転送を有効にする

**FW1**で使用される NIC での IP 転送を有効にするには、次の手順に従います。

1. JMESPATH フィルターを使用して [az network nic show](/cli/az/network/nic#show) コマンドを実行し、**[IP 転送の有効化]** の現在の **enable-ip-forwarding** 値を表示します。 この値は、*false* に設定されている必要があります。

    ```azurecli
    az network nic show \
    --resource-group testrg \
    --nname nicfw1 \
    --query 'enableIpForwarding' -o tsv
    ```

    出力:

        false

2. 次のコマンドを実行して、IP 転送を有効にします。

    ```azurecli
    az network nic update \
    --resource-group testrg \
    --name nicfw1 \
    --ip-forwarding true
    ```

    コンソールにストリーミングされた出力を調べることも、特定の **enableIpForwarding** 値だけを再テストすることもできます。

    ```azurecli
    az network nic show -g testrg -n nicfw1 --query 'enableIpForwarding' -o tsv
    ```

    出力:

        true

    パラメーター:

    **--ip-forwarding**: *true* または *false*。


