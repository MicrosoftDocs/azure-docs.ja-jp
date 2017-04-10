---
title: "ネットワーク セキュリティ グループの管理 - Azure CLI 2.0 | Microsoft Docs"
description: "Azure コマンド ライン インターフェイス (CLI) 2.0 を使用してネットワーク セキュリティ グループを管理する方法について説明します。"
services: virtual-network
documentationcenter: na
author: jimdial
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: ed17d314-07e6-4c7f-bcf1-a8a2535d7c14
ms.service: virtual-network
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/21/2017
ms.author: jdial
ms.custom: H1Hack27Feb2017
translationtype: Human Translation
ms.sourcegitcommit: 63f2f6dde56c1b5c4b3ad2591700f43f6542874d
ms.openlocfilehash: dcb0455fe223e99b4f1b0035b1d1109ecf5ee268
ms.lasthandoff: 02/28/2017


---
# <a name="manage-network-security-groups-using-the-azure-cli-20"></a>Azure CLI 2.0 を使用したネットワーク セキュリティ グループの管理

[!INCLUDE [virtual-network-manage-arm-selectors-include.md](../../includes/virtual-network-manage-nsg-arm-selectors-include.md)]

## <a name="cli-versions-to-complete-the-task"></a>タスクを完了するための CLI バージョン 

次のいずれかの CLI バージョンを使用してタスクを完了できます。 

- [Azure CLI 1.0](virtual-network-manage-nsg-cli-nodejs.md) - クラシック デプロイメント モデルと Resource Manager デプロイメント モデル用の CLI 
- [Azure CLI 2.0](#View-existing-NSGs) - リソース管理デプロイ モデル用の次世代 CLI (この記事)


[!INCLUDE [virtual-network-manage-nsg-intro-include.md](../../includes/virtual-network-manage-nsg-intro-include.md)]

> [!NOTE]
> Azure には、リソースの作成と操作に関して、[Resource Manager とクラシックの](../resource-manager-deployment-model.md)&2; 種類のデプロイメント モデルがあります。 この記事では、Resource Manager デプロイメント モデルの使用方法について取り上げていますが、最新のデプロイでは、クラシック デプロイメント モデルではなくこのモデルをお勧めします。
> 

[!INCLUDE [virtual-network-manage-nsg-arm-scenario-include.md](../../includes/virtual-network-manage-nsg-arm-scenario-include.md)]

## <a name="prerequisite"></a>前提条件
まだ行っていない場合は、最新の [Azure CLI 2.0](/cli/azure/install-az-cli2) をインストールして構成し、[az login](/cli/azure/#login) を使用して Azure アカウントにログインします。 


## <a name="view-existing-nsgs"></a>既存の NSG を表示する
特定のリソース グループの NSG の一覧を表示するには、[az network nsg list](/cli/azure/network/nsg#list) コマンドを `-o table` の出力形式で実行します。

```azurecli
az network nsg list -g RG-NSG -o table
```

予想される出力:

    Location    Name          ProvisioningState    ResourceGroup    ResourceGuid
    ----------  ------------  -------------------  ---------------  ------------------------------------
    centralus   NSG-BackEnd   Succeeded            RG-NSG           <guid>
    centralus   NSG-FrontEnd  Succeeded            RG-NSG           <guid>

## <a name="list-all-rules-for-an-nsg"></a>NSG のすべての規則を一覧表示する
**NSG-FrontEnd** という名前の NGS の規則を表示するには、[JMESPATH クエリ フィルター](/cli/azure/query-az-cli2)と `-o table` 出力形式を使用して、[az network nsg show](/cli/azure/network/nsg#show) コマンドを実行します。

```azurecli
    az network nsg show \
    --resource-group RG-NSG \
    --name NSG-FrontEnd \
    --query '[defaultSecurityRules[],securityRules[]][].{Name:name,Desc:description,Access:access,Direction:direction,DestPortRange:destinationPortRange,DestAddrPrefix:destinationAddressPrefix,SrcPortRange:sourcePortRange,SrcAddrPrefix:sourceAddressPrefix}' \
    -o table
```

予想される出力:

    Name                           Desc                                                    Access    Direction    DestPortRange    DestAddrPrefix    SrcPortRange    SrcAddrPrefix
    -----------------------------  ------------------------------------------------------  --------  -----------  ---------------  ----------------  --------------  -----------------
    AllowVnetInBound               Allow inbound traffic from all VMs in VNET              Allow     Inbound      *                VirtualNetwork    *               VirtualNetwork
    AllowAzureLoadBalancerInBound  Allow inbound traffic from azure load balancer          Allow     Inbound      *                *                 *               AzureLoadBalancer
    DenyAllInBound                 Deny all inbound traffic                                Deny      Inbound      *                *                 *               *
    AllowVnetOutBound              Allow outbound traffic from all VMs to all VMs in VNET  Allow     Outbound     *                VirtualNetwork    *               VirtualNetwork
    AllowInternetOutBound          Allow outbound traffic from all VMs to Internet         Allow     Outbound     *                Internet          *               *
    DenyAllOutBound                Deny all outbound traffic                               Deny      Outbound     *                *                 *               *
    rdp-rule                                                                               Allow     Inbound      3389             *                 *               Internet
    web-rule                                                                               Allow     Inbound      80               *                 *               Internet
> [!NOTE]
> [az network nsg rule list](/cli/azure/network/nsg/rule#list) を使用して NSG のカスタム規則のみを一覧表示することもできます。
>

## <a name="view-nsg-associations"></a>NSG の関連付けを表示する

**NSG-FrontEnd** NSG が関連付けられているリソースを表示するには、次のように `az network nsg show` コマンドを実行します。 

```azurecli
az network nsg show -g RG-NSG -n nsg-frontend --query '[subnets,networkInterfaces]'
```

次の **NetworkInterfaces** と **Subnets** の各プロパティをご確認ください。

```json
[
  [
    {
      "addressPrefix": null,
      "etag": null,
      "id": "/subscriptions/<guid>/resourceGroups/RG-NSG/providers/Microsoft.Network/virtualNetworks/TestVNET/subnets/FrontEnd",
      "ipConfigurations": null,
      "name": null,
      "networkSecurityGroup": null,
      "provisioningState": null,
      "resourceGroup": "RG-NSG",
      "resourceNavigationLinks": null,
      "routeTable": null
    }
  ],
  null
]
```

上の例では、NSG はネットワーク インターフェイス (NIC) に関連付けられておらず、 **FrontEnd**というサブネットに関連付けられています。

## <a name="add-a-rule"></a>規則を追加する
任意のコンピューターから **NSG-FrontEnd** NSG へのポート **443** に対する**受信**トラフィックを許可する規則を追加するには、次のコマンドを入力します。

```azurecli
az network nsg rule create  \
--resource-group RG-NSG \
--nsg-name NSG-FrontEnd  \
--name allow-https \
--description "Allow access to port 443 for HTTPS" \
--access Allow \
--protocol Tcp  \
--direction Inbound \
--priority 102 \
--source-address-prefix "*"  \
--source-port-range "*"  \
--destination-address-prefix "*" \
--destination-port-range "443"
```

予想される出力:

```json
{
  "access": "Allow",
  "description": "Allow access to port 443 for HTTPS",
  "destinationAddressPrefix": "*",
  "destinationPortRange": "443",
  "direction": "Inbound",
  "etag": "W/\"<guid>\"",
  "id": "/subscriptions/<guid>/resourceGroups/RG-NSG/providers/Microsoft.Network/networkSecurityGroups/NSG-FrontEnd/securityRules/allow-https",
  "name": "allow-https",
  "priority": 102,
  "protocol": "Tcp",
  "provisioningState": "Succeeded",
  "resourceGroup": "RG-NSG",
  "sourceAddressPrefix": "*",
  "sourcePortRange": "*"
}
```

## <a name="change-a-rule"></a>規則を変更する
上の手順で作成した規則を、**インターネット**からの受信トラフィックのみを許可する規則に変更するには、[az network nsg rule update](/cli/azure/network/nsg/rule#update) コマンドを実行します。

```azurecli
az network nsg rule update \
--resource-group RG-NSG \
--nsg-name NSG-FrontEnd \
--name allow-https \
--source-address-prefix Internet
```

予想される出力:

```json
{
"access": "Allow",
"description": "Allow access to port 443 for HTTPS",
"destinationAddressPrefix": "*",
"destinationPortRange": "443",
"direction": "Inbound",
"etag": "W/\"<guid>\"",
"id": "/subscriptions/<guid>/resourceGroups/RG-NSG/providers/Microsoft.Network/networkSecurityGroups/NSG-FrontEnd/securityRules/allow-https",
"name": "allow-https",
"priority": 102,
"protocol": "Tcp",
"provisioningState": "Succeeded",
"resourceGroup": "RG-NSG",
"sourceAddressPrefix": "Internet",
"sourcePortRange": "*"
}
```

## <a name="delete-a-rule"></a>規則を削除する
先ほど作成した規則を削除するには、次のコマンドを実行します。

```azurecli
az network nsg rule delete \
--resource-group RG-NSG \
--nsg-name NSG-FrontEnd \
--name allow-https
```


## <a name="associate-an-nsg-to-a-nic"></a>NSG を NIC に関連付ける
**NSG-FrontEnd** NSG を **TestNICWeb1** NIC と関連付けるには、[az network nic update](/cli/azure/network/nic#update) コマンドを使用します。

```azurecli
az network nic update \
--resource-group RG-NSG \
--name TestNICWeb1 \
--network-security-group NSG-FrontEnd    
```

予想される出力:

```json
{
  "dnsSettings": {
    "appliedDnsServers": [],
    "dnsServers": [],
    "internalDnsNameLabel": null,
    "internalDomainNameSuffix": "k0wkaguidnqrh0ud.gx.internal.cloudapp.net",
    "internalFqdn": null
  },
  "enableAcceleratedNetworking": false,
  "enableIpForwarding": false,
  "etag": "W/\"<guid>\"",
  "id": "/subscriptions/<guid>/resourceGroups/RG-NSG/providers/Microsoft.Network/networkInterfaces/TestNICWeb1",
  "ipConfigurations": [
    {
      "applicationGatewayBackendAddressPools": null,
      "etag": "W/\"<guid>\"",
      "id": "/subscriptions/<guid>/resourceGroups/RG-NSG/providers/Microsoft.Network/networkInterfaces/TestNICWeb1/ipConfigurations/ipconfig1",
      "loadBalancerBackendAddressPools": null,
      "loadBalancerInboundNatRules": null,
      "name": "ipconfig1",
      "primary": true,
      "privateIpAddress": "192.168.1.6",
      "privateIpAddressVersion": "IPv4",
      "privateIpAllocationMethod": "Static",
      "provisioningState": "Succeeded",
      "publicIpAddress": null,
      "resourceGroup": "RG-NSG",
      "subnet": {
        "addressPrefix": null,
        "etag": null,
        "id": "/subscriptions/<guid>/resourceGroups/RG-NSG/providers/Microsoft.Network/virtualNetworks/TestVNet/subnets/FrontEnd",
        "ipConfigurations": null,
        "name": null,
        "networkSecurityGroup": null,
        "provisioningState": null,
        "resourceGroup": "RG-NSG",
        "resourceNavigationLinks": null,
        "routeTable": null
      }
    }
  ],
  "location": "centralus",
  "macAddress": "00-0D-3A-91-A9-60",
  "name": "TestNICWeb1",
  "networkSecurityGroup": {
    "defaultSecurityRules": null,
    "etag": null,
    "id": "/subscriptions/<guid>/resourceGroups/RG-NSG/providers/Microsoft.Network/networkSecurityGroups/NSG-FrontEnd",
    "location": null,
    "name": null,
    "networkInterfaces": null,
    "provisioningState": null,
    "resourceGroup": "RG-NSG",
    "resourceGuid": null,
    "securityRules": null,
    "subnets": null,
    "tags": null,
    "type": null
  },
  "primary": null,
  "provisioningState": "Succeeded",
  "resourceGroup": "RG-NSG",
  "resourceGuid": "<guid>",
  "tags": {},
  "type": "Microsoft.Network/networkInterfaces",
  "virtualMachine": null
}
```

## <a name="dissociate-an-nsg-from-a-nic"></a>NSG と NIC の関連付けを解除する

**TestNICWeb1** NIC から **NSG-FrontEnd** NSG への関連付けを解除するには、[az network nsg rule update](/cli/azure/network/nsg/rule#update) コマンドを再び実行し、`--network-security-group` の引数を空の文字列 (`""`) に置き換えます。

```azurecli
az network nic update --resource-group RG-NSG --name TestNICWeb3 --network-security-group ""
```

出力では、`networkSecurityGroup` キーが null に設定されます。

## <a name="dissociate-an-nsg-from-a-subnet"></a>NSG とサブネットの関連付けを解除する
**FrontEnd** サブネットから **NSG-FrontEnd** NSG への関連付けを解除するには、[az network nsg rule update](/cli/azure/network/nsg/rule#update) コマンドを再び実行し、`--network-security-group` 引数を空の文字列 (`""`) に置き換えます。

```azurecli
az network vnet subnet update \
--resource-group RG-NSG \
--vnet-name testvnet \
--name FrontEnd \
--network-security-group ""
```

出力では、`networkSecurityGroup` キーが null に設定されます。

## <a name="associate-an-nsg-to-a-subnet"></a>NSG サブネットへの関連付け
**NSG-FrontEnd** NSG を **FrontEnd** サブネットと再び関連付けるには、次のコマンドを実行します。

```azurecli
az network vnet subnet update \
--resource-group RG-NSG \
--vnet-name testvnet \
--name FrontEnd \
--network-security-group NSG-FrontEnd
```

出力では、`networkSecurityGroup` キーは次の値のようになります。

```json
"networkSecurityGroup": {
    "defaultSecurityRules": null,
    "etag": null,
    "id": "/subscriptions/0e220bf6-5caa-4e9f-8383-51f16b6c109f/resourceGroups/RG-NSG/providers/Microsoft.Network/networkSecurityGroups/NSG-FrontEnd",
    "location": null,
    "name": null,
    "networkInterfaces": null,
    "provisioningState": null,
    "resourceGroup": "RG-NSG",
    "resourceGuid": null,
    "securityRules": null,
    "subnets": null,
    "tags": null,
    "type": null
  }
  ```

## <a name="delete-an-nsg"></a>NSG の削除
NSG に関連付けられているリソースがない場合にのみ、NSG を削除できます。 NSG を削除するには、次の手順を実行します。

1. NSG に関連付けられているリソースを確認するには、「[NSG の関連付けを表示する](#View-NSGs-associations)」を参照し、`azure network nsg show` を実行します。
2. NSG に関連付けられている NIC がある場合は、「[NSG と NIC の関連付けを解除する](#Dissociate-an-NSG-from-a-NIC)」を参照し、各 NIC について `azure network nic set` を実行します。 
3. NSG に関連付けられているサブネットがある場合は、「[NSG とサブネットの関連付けを解除する](#Dissociate-an-NSG-from-a-subnet)」を参照し、各サブネットについて `azure network vnet subnet set` を実行します。
4. NSG を削除するには次のコマンドを実行します。

    ```azurecli
    az network nsg delete --resource-group RG-NSG --name NSG-FrontEnd
    ```
## <a name="next-steps"></a>次のステップ
* [ログを有効にします](virtual-network-nsg-manage-log.md) 。


