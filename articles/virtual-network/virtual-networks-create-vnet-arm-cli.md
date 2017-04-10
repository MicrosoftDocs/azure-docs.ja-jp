---
title: "仮想ネットワークの作成 - Azure CLI 2.0 | Microsoft Docs"
description: "Azure CLI 2.0 を使用して仮想ネットワークを作成する方法を説明します。"
services: virtual-network
documentationcenter: 
author: jimdial
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 75966bcc-0056-4667-8482-6f08ca38e77a
ms.service: virtual-network
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/15/2016
ms.author: jdial
ms.custom: H1Hack27Feb2017
translationtype: Human Translation
ms.sourcegitcommit: 63f2f6dde56c1b5c4b3ad2591700f43f6542874d
ms.openlocfilehash: 4f59512d83e6d000dd60b3fba46e483be8466292
ms.lasthandoff: 02/28/2017


---
# <a name="create-a-virtual-network-using-the-azure-cli-20"></a>Azure CLI 2.0 を使用した仮想ネットワークの作成

[!INCLUDE [virtual-networks-create-vnet-intro](../../includes/virtual-networks-create-vnet-intro-include.md)]

Azure には、Azure Resource Manager とクラシックという&2; 種類のデプロイメント モデルがあります。 Resource Manager デプロイメント モデルを使用してリソースを作成することをお勧めします。 2 つのモデルの違いの詳細については、[Azure のデプロイメント モデルの概要](../azure-resource-manager/resource-manager-deployment-model.md)に関する記事を参照してください。

## <a name="cli-versions-to-complete-the-task"></a>タスクを完了するための CLI バージョン
次のいずれかの CLI バージョンを使用してタスクを完了できます。

- [Azure CLI 1.0](virtual-networks-create-vnet-cli-nodejs.md) - クラシック デプロイメント モデルと Resource Manager デプロイメント モデル用の CLI
- [Azure CLI 2.0](#create-a-virtual-network) - リソース管理デプロイ モデル用の次世代 CLI (この記事)
 
    他のツールを使用して Resource Manager で VNet を作成することや、次の一覧から別のオプションを選択してクラシック デプロイメント モデルで VNet を作成することもできます。

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

Azure CLI 2.0 を使用して仮想ネットワークを作成するには、次の手順を実行します。

1. 最新の [Azure CLI 2.0](/cli/azure/install-az-cli2) をインストールして構成し、[az login](/cli/azure/#login) を使用して Azure アカウントにログインします。

2. [az group create](/cli/azure/group#create) コマンドに引数 `--name` および `--location` を指定して、VNet 用のリソース グループを作成します。

    ```azurecli
    az group create --name TestRG --location centralus
    ```

3. VNet とサブネットを作成します。

    ```azurecli
    az network vnet create \
    --name TestVNet \
    --resource-group TestRG \
    --location centralus \
    --address-prefix 192.168.0.0/16 \
    --subnet-name FrontEnd \
    --subnet-prefix 192.168.1.0/24
    ```

    予想される出力:
    
    ```json
    {
        "newVNet": {
            "addressSpace": {
            "addressPrefixes": [
            "192.168.0.0/16"
            ]
            },
            "dhcpOptions": {
            "dnsServers": []
            },
            "provisioningState": "Succeeded",
            "resourceGuid": "<guid>",
            "subnets": [
            {
                "etag": "W/\"<guid>\"",
                "id": "/subscriptions/<guid>/resourceGroups/TestRG/providers/Microsoft.Network/virtualNetworks/TestVNet/subnets/FrontEnd",
                "name": "FrontEnd",
                "properties": {
                "addressPrefix": "192.168.1.0/24",
                "provisioningState": "Succeeded"
                },
                "resourceGroup": "TestRG"
            }
            ]
            }
    }
    ```

    使用されるパラメーター:

    - `--name TestVNet`: 作成する VNet の名前です。
    - `--resource-group TestRG`: リソースを制御するリソース グループの名前です。 
    - `--location centralus`: デプロイ先の場所です。
    - `--address-prefix 192.168.0.0/16`: アドレスのプレフィックスとブロックです。  
    - `--subnet-name FrontEnd`: サブネットの名前です。
    - `--subnet-prefix 192.168.1.0/24`: アドレスのプレフィックスとブロックです。

    次のコマンドで使用する基本的な情報を一覧表示するには、[クエリ フィルター](/cli/azure/query-az-cli2)を使用して VNet を照会します。

    ```azurecli
    az network vnet list --query '[?name==`TestVNet`].{Where:location,Name:name,Group:resourceGroup}' -o table
    ```

    次の出力が生成されます。

        Where      Name      Group

        centralus  TestVNet  TestRG

4. サブネットを作成します。

    ```azurecli
    az network vnet subnet create \
    --address-prefix 192.168.2.0/24 \
    --name BackEnd \
    --resource-group TestRG \
    --vnet-name TestVNet
    ```

    予想される出力:

    ```json
    {
    "addressPrefix": "192.168.2.0/24",
    "etag": "W/\"<guid> \"",
    "id": "/subscriptions/<guid>/resourceGroups/TestRG/providers/Microsoft.Network/virtualNetworks/TestVNet/subnets/BackEnd",
    "ipConfigurations": null,
    "name": "BackEnd",
    "networkSecurityGroup": null,
    "provisioningState": "Succeeded",
    "resourceGroup": "TestRG",
    "resourceNavigationLinks": null,
    "routeTable": null
    }
    ```

    使用されるパラメーター:

    - `--address-prefix 192.168.2.0/24`: サブネットの CIDR ブロックです。
    - `--name BackEnd`: 新しいサブネットの名前です。
    - `--resource-group TestRG`: リソース グループです。
    - `--vnet-name TestVNet`: 所有している VNet の名前です。

5. 新しい VNet のプロパティを照会します。

    ```azurecli
    az network vnet show \
    -g TestRG \
    -n TestVNet \
    --query '{Name:name,Where:location,Group:resourceGroup,Status:provisioningState,SubnetCount:subnets | length(@)}' \
    -o table
    ```

    予想される出力:

        Name      Where      Group    Status       SubnetCount

        TestVNet  centralus  TestRG   Succeeded              2

6. サブネットのプロパティを照会します。

    ```azurecli
    az network vnet subnet list \
    -g TestRG \
    --vnet-name testvnet \
    --query '[].{Name:name,CIDR:addressPrefix,Status:provisioningState}' \
    -o table
    ```

    予想される出力:

        Name      CIDR            Status

        FrontEnd  192.168.1.0/24  Succeeded
        BackEnd   192.168.2.0/24  Succeeded

## <a name="next-steps"></a>次のステップ

次の接続方法を確認してください。

- 仮想マシン (VM) を仮想ネットワークに接続する方法。[Linux VM の作成](../virtual-machines/virtual-machines-linux-quick-create-cli.md)に関する記事をご覧ください。 この記事の手順で VNet とサブネットを作成する代わりに、VM を接続する既存の VNet とサブネットを選択できます。
- 仮想ネットワークを他の仮想ネットワークに接続する方法。[VNet の接続](../vpn-gateway/vpn-gateway-howto-vnet-vnet-resource-manager-portal.md)に関する記事をご覧ください。
- サイト間の仮想プライベート ネットワーク (VPN) または ExpressRoute 回線を使用して、仮想ネットワークをオンプレミスのネットワークに接続する方法。 詳しくは、[サイト間 VPN を使用したオンプレミスのネットワークへの VNet の接続](../vpn-gateway/vpn-gateway-howto-multi-site-to-site-resource-manager-portal.md)に関する記事と「[ExpressRoute 回線への VNet のリンク](../expressroute/expressroute-howto-linkvnet-portal-resource-manager.md)」をご覧ください。
