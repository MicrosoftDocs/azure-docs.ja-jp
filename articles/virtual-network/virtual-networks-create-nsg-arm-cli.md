---
title: "ネットワーク セキュリティ グループの作成 - Azure CLI 2.0 | Microsoft Docs"
description: "Azure CLI 2.0 を使用してネットワーク セキュリティ グループを作成してデプロイする方法を説明します。"
services: virtual-network
documentationcenter: na
author: jimdial
manager: timlt
editor: tysonn
tags: azure-resource-manager
ms.assetid: 9ea82c09-f4a6-4268-88bc-fc439db40c48
ms.service: virtual-network
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/17/2017
ms.author: jdial
ms.custom: H1Hack27Feb2017
translationtype: Human Translation
ms.sourcegitcommit: 63f2f6dde56c1b5c4b3ad2591700f43f6542874d
ms.openlocfilehash: e7a8f4cff0889a02ef1f7a4d32fda4e57c5d35b0
ms.lasthandoff: 02/28/2017


---
# <a name="create-network-security-groups-using-the-azure-cli-20"></a>Azure CLI 2.0 を使用したネットワーク セキュリティ グループの作成

[!INCLUDE [virtual-networks-create-nsg-selectors-arm-include](../../includes/virtual-networks-create-nsg-selectors-arm-include.md)]

## <a name="cli-versions-to-complete-the-task"></a>タスクを完了するための CLI バージョン 

次のいずれかの CLI バージョンを使用してタスクを完了できます。 

- [Azure CLI 1.0](virtual-networks-create-nsg-cli-nodejs.md) - クラシック デプロイメント モデルと Resource Manager デプロイメント モデル用の CLI 
- [Azure CLI 2.0](#Create-the-nsg-for-the-front-end-subnet) - リソース管理デプロイ モデル用の次世代 CLI (この記事)

[!INCLUDE [virtual-networks-create-nsg-intro-include](../../includes/virtual-networks-create-nsg-intro-include.md)]

[!INCLUDE [virtual-networks-create-nsg-scenario-include](../../includes/virtual-networks-create-nsg-scenario-include.md)]

以下の Azure CLI 2.0 のサンプル コマンドでは、前述のシナリオに基づいて単純な環境が既に作成されていると想定しています。 

## <a name="create-the-nsg-for-the-frontend-subnet"></a>`FrontEnd` サブネット用の NSG の作成

前述のシナリオに基づいて *NSG-FrontEnd* という名前の NSG を作成するには、次の手順に従います。

1. まだ行っていない場合は、最新の [Azure CLI 2.0](/cli/azure/install-az-cli2) をインストールして構成し、[az login](/cli/azure/#login) を使用して Azure アカウントにログインします。 

2. [az network nsg create](/cli/azure/network/nsg#create) コマンドを使用して NSG を作成します。 

    ```azurecli
    az network nsg create \
    --resource-group testrg \
    --name NSG-FrontEnd \
    --location centralus 
    ```

    パラメーター:
   
   * `--resource-group`: NSG の作成場所となるリソース グループの名前です。 ここでは、*TestRG* です。
   * `--location`: 新しい NSG が作成される Azure リージョンです。 ここでは、*westus* です。
   * `--name`: 新しい NSG の名前です。 ここでは、*NSG-FrontEnd* です。

    予想される出力は、すべての既定の規則の一覧を含む非常に大量の情報です。 次の例では、JMESPATH クエリ フィルターを使用した `table` 出力形式による既定の規則を示します。

    ```azurecli
    az network nsg show \
    -g testrg \
    -n nsg-frontend \
    --query 'defaultSecurityRules[].{Access:access,Desc:description,DestPortRange:destinationPortRange,Direction:direction,Priority:priority}' \
    -o table
    ```
   
   出力:

        Access    Desc                                                    DestPortRange    Direction      Priority
        
        Allow     Allow inbound traffic from all VMs in VNET              *                Inbound           65000
        Allow     Allow inbound traffic from azure load balancer          *                Inbound           65001
        Deny      Deny all inbound traffic                                *                Inbound           65500
        Allow     Allow outbound traffic from all VMs to all VMs in VNET  *                Outbound          65000
        Allow     Allow outbound traffic from all VMs to Internet         *                Outbound          65001
        Deny      Deny all outbound traffic                               *                Outbound          65500



3. [az network nsg rule create](/cli/azure/network/nsg/rule#create) コマンドを使用して、インターネットからポート 3389 (RDP) へのアクセスを許可する規則を作成します。

    > [!NOTE]
    > 使用しているシェルによっては、実行前に引数が展開されないように次の引数内の `*` 文字を変更する必要があります。
   
    ```azurecli
    az network nsg rule create \
    --resource-group testrg \
    --nsg-name NSG-FrontEnd \
    --name rdp-rule \
    --access Allow \
    --protocol Tcp \
    --direction Inbound \
    --priority 100 \
    --source-address-prefix Internet \
    --source-port-range "*" \
    --destination-address-prefix "*" \
    --destination-port-range 3389
    ```
   
    予想される出力:
   
    ```json
    {
        "access": "Allow",
        "description": null,
        "destinationAddressPrefix": "*",
        "destinationPortRange": "3389",
        "direction": "Inbound",
        "etag": "W/\"<guid>\"",
        "id": "/subscriptions/<guid>/resourceGroups/testrg/providers/Microsoft.Network/networkSecurityGroups/NSG-FrontEnd/securityRules/rdp-rule",
        "name": "rdp-rule",
        "priority": 100,
        "protocol": "Tcp",
        "provisioningState": "Succeeded",
        "resourceGroup": "testrg",
        "sourceAddressPrefix": "Internet",
        "sourcePortRange": "*"
    }
    ```

    パラメーター:

    * `--resource-group testrg`: 使用するリソース グループです。 大文字と小文字は区別されないことに注意してください。
    * `--nsg-name NSG-FrontEnd`: 規則が作成される NSG の名前です。
    * `--name rdp-rule`: 新しい規則の名前です。
    * `--access Allow`: 規則のアクセス レベルです (Deny または Allow)。
    * `--protocol Tcp`: プロトコル (TCP、UDP、または *) です。
    * `--direction Inbound`: 接続の方向です (Inbound または Outbound)。
    * `--priority 100`: 規則の優先度です。
    * `--source-address-prefix Internet`: CIDR または既定のタグを使用する発信元アドレスのプレフィックスです。
    * `--source-port-range "*"`: 発信元ポート、またはポート範囲です。 接続を開いたポートです。
    * `--destination-address-prefix "*"`: CIDR または既定のタグを使用する接続先アドレスのプレフィックスです。
    * `--destination-port-range 3389`: 接続先ポート、またはポート範囲です。 接続要求を受信するポートです。



4. **az network nsg rule create** コマンドを実行して、インターネットからポート 80 (HTTP) へのアクセスを許可する規則を作成します。
   
    ```azurecli
    az network nsg rule create \
    --resource-group testrg \
    --nsg-name NSG-FrontEnd \
    --name web-rule \
    --access Allow \
    --protocol Tcp \
    --direction Inbound \
    --priority 200 \
    --source-address-prefix Internet \
    --source-port-range "*" \
    --destination-address-prefix "*" \
    --destination-port-range 80
    ```
   
    予想される出力:
   
    ```json
    {
        "access": "Allow",
        "description": null,
        "destinationAddressPrefix": "*",
        "destinationPortRange": "80",
        "direction": "Inbound",
        "etag": "W/\"<guid>\"",
        "id": "/subscriptions/<guid>/resourceGroups/testrg/providers/Microsoft.Network/networkSecurityGroups/NSG-FrontEnd/securityRules/web-rule",
        "name": "web-rule",
        "priority": 200,
        "protocol": "Tcp",
        "provisioningState": "Succeeded",
        "resourceGroup": "testrg",
        "sourceAddressPrefix": "Internet",
        "sourcePortRange": "*"
    }
    ```

5. [az network vnet subnet update](/cli/azure/network/vnet/subnet#update) コマンドを使用して NSG を **FrontEnd** サブネットにバインドします。
        
    ```azurecli
    az network vnet subnet update \
    --vnet-name TestVNET \
    --name FrontEnd \
    --resource-group testrg \
    --network-security-group NSG-FrontEnd
    ```
   
    予想される出力:
   
    ```json
    {
        "addressPrefix": "192.168.1.0/24",
        "etag": "W/\"<guid>\"",
        "id": "/subscriptions/<guid>/resourceGroups/testrg/providers/Microsoft.Network/virtualNetworks/TestVNET/subnets/FrontEnd",
        "ipConfigurations": [
            {
            "etag": null,
            "id": "/subscriptions/<guid>/resourceGroups/TestRG/providers/Microsoft.Network/networkInterfaces/TestNIC/ipConfigurations/ipconfig1",
            "name": null,
            "privateIpAddress": null,
            "privateIpAllocationMethod": null,
            "provisioningState": null,
            "publicIpAddress": null,
            "resourceGroup": "TestRG",
            "subnet": null
            }
        ],
        "name": "FrontEnd",
        "networkSecurityGroup": {
            "defaultSecurityRules": null,
            "etag": null,
            "id": "/subscriptions/<guid>f/resourceGroups/testrg/providers/Microsoft.Network/networkSecurityGroups/NSG-FrontEnd",
            "location": null,
            "name": null,
            "networkInterfaces": null,
            "provisioningState": null,
            "resourceGroup": "testrg",
            "resourceGuid": null,
            "securityRules": null,
            "subnets": null,
            "tags": null,
            "type": null
        },
        "provisioningState": "Succeeded",
        "resourceGroup": "testrg",
        "resourceNavigationLinks": null,
        "routeTable": null
    }
    ```

## <a name="create-the-nsg-for-the-backend-subnet"></a>`BackEnd` サブネット用の NSG の作成
前述のシナリオに基づいて *NSG-BackEnd* という名前の NSG を作成するには、次の手順に従います。

1. **az network nsg create** を使用して `NSG-BackEnd` NSG を作成します。
   
    ```azurecli
    az network nsg create \
    --resource-group testrg \
    --name NSG-BackEnd \
    --location centralus
    ```
   
    前述の手順 2 同様、想定される出力は非常に大きく、既定の規則を含んでいます。
   
2. **az network nsg rule create** コマンドを使用して、`FrontEnd` サブネットからポート 1433 (SQL) へのアクセスを許可する規則を作成します。
   
    ```azurecli
    az network nsg rule create \
    --resource-group testrg \
    --nsg-name NSG-BackEnd \
    --name sql-rule \
    --access Allow \
    --protocol Tcp \
    --direction Inbound \
    --priority 100 \
    --source-address-prefix 192.168.1.0/24 \
    --source-port-range "*" \
    --destination-address-prefix "*" \
    --destination-port-range 1433
    ```
   
    予想される出力:

    ```json  
    {
    "access": "Allow",
    "description": null,
    "destinationAddressPrefix": "*",
    "destinationPortRange": "1433",
    "direction": "Inbound",
    "etag": "W/\"<guid>\"",
    "id": "/subscriptions/<guid>/resourceGroups/testrg/providers/Microsoft.Network/networkSecurityGroups/NSG-BackEnd/securityRules/sql-rule",
    "name": "sql-rule",
    "priority": 100,
    "protocol": "Tcp",
    "provisioningState": "Succeeded",
    "resourceGroup": "testrg",
    "sourceAddressPrefix": "192.168.1.0/24",
    "sourcePortRange": "*"
    }
    ```

3. **az network nsg rule create** コマンドを使用して、インターネットへのアクセスを拒否する規則を作成します。
   
    ```azurecli
    az network nsg rule create \
    --resource-group testrg \
    --nsg-name NSG-BackEnd \
    --name web-rule \
    --access Deny \
    --protocol Tcp  \
    --direction Outbound  \
    --priority 200 \
    --source-address-prefix "*" \
    --source-port-range "*" \
    --destination-address-prefix "*" \
    --destination-port-range "*"
    ```
   
    予想される出力:
   
    ```json
    {
    "access": "Deny",
    "description": null,
    "destinationAddressPrefix": "*",
    "destinationPortRange": "*",
    "direction": "Outbound",
    "etag": "W/\"<guid>\"",
    "id": "/subscriptions/<guid>/resourceGroups/testrg/providers/Microsoft.Network/networkSecurityGroups/NSG-BackEnd/securityRules/web-rule",
    "name": "web-rule",
    "priority": 200,
    "protocol": "Tcp",
    "provisioningState": "Succeeded",
    "resourceGroup": "testrg",
    "sourceAddressPrefix": "*",
    "sourcePortRange": "*"
    }
    ```

4. **az network vnet subnet set** コマンドを使用して NSG を `BackEnd`サブネットにバインドします。
   
    ```azurecli
    az network vnet subnet update \
    --vnet-name TestVNET \
    --name BackEnd \
    --resource-group testrg \
    --network-security-group NSG-BackEnd
    ```
   
    予想される出力:
   
    ```json
    {
    "addressPrefix": "192.168.2.0/24",
    "etag": "W/\"<guid>\"",
    "id": "/subscriptions/<guid>/resourceGroups/testrg/providers/Microsoft.Network/virtualNetworks/TestVNET/subnets/BackEnd",
    "ipConfigurations": null,
    "name": "BackEnd",
    "networkSecurityGroup": {
        "defaultSecurityRules": null,
        "etag": null,
        "id": "/subscriptions/<guid>/resourceGroups/testrg/providers/Microsoft.Network/networkSecurityGroups/NSG-BackEnd",
        "location": null,
        "name": null,
        "networkInterfaces": null,
        "provisioningState": null,
        "resourceGroup": "testrg",
        "resourceGuid": null,
        "securityRules": null,
        "subnets": null,
        "tags": null,
        "type": null
    },
    "provisioningState": "Succeeded",
    "resourceGroup": "testrg",
    "resourceNavigationLinks": null,
    "routeTable": null
    }
    ```

