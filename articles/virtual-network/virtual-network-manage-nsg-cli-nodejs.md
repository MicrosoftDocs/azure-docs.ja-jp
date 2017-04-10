---
title: "ネットワーク セキュリティ グループの管理 - Azure CLI 1.0 | Microsoft Docs"
description: "Azure コマンドライン インターフェイス (CLI) 1.0 を使用してネットワーク セキュリティ グループを管理する方法について説明します。"
services: virtual-network
documentationcenter: na
author: jimdial
manager: timlt
editor: 
tags: azure-resource-manager
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/21/2017
ms.author: jdial
ms.custom: H1Hack27Feb2017
translationtype: Human Translation
ms.sourcegitcommit: 63f2f6dde56c1b5c4b3ad2591700f43f6542874d
ms.openlocfilehash: 2e53c3ff2ffbef95d6b72ca6afb3b4de377f0389
ms.lasthandoff: 02/28/2017


---
# <a name="manage-network-security-groups-using-the-azure-cli-10"></a>Azure CLI 1.0 を使用したネットワーク セキュリティ グループの管理

## <a name="cli-versions-to-complete-the-task"></a>タスクを完了するための CLI バージョン 

次のいずれかの CLI バージョンを使用してタスクを完了できます。 

- [Azure CLI 1.0](#View-existing-NSGs) - クラシック デプロイメント モデルと Resource Manager デプロイメント モデル用の CLI 
- [Azure CLI 2.0](virtual-network-manage-nsg-arm-cli.md) - リソース管理デプロイ モデル用の次世代 CLI (この記事)

[!INCLUDE [virtual-network-manage-nsg-intro-include.md](../../includes/virtual-network-manage-nsg-intro-include.md)]

> [!NOTE]
> Azure には、リソースの作成と操作に関して、[Resource Manager とクラシックの](../azure-resource-manager/resource-manager-deployment-model.md)&2; 種類のデプロイメント モデルがあります。 この記事では、Resource Manager デプロイメント モデルの使用方法について取り上げていますが、最新のデプロイでは、クラシック デプロイメント モデルではなくこのモデルをお勧めします。
> 

[!INCLUDE [virtual-network-manage-nsg-arm-scenario-include.md](../../includes/virtual-network-manage-nsg-arm-scenario-include.md)]

[!INCLUDE [azure-cli-prerequisites-include.md](../../includes/azure-cli-prerequisites-include.md)]

## <a name="retrieve-information"></a>情報を取得する
既存の NSG を表示し、既存の NSG の規則を取得し、NSG が関連付けられているリソースを検索することができます。

### <a name="view-existing-nsgs"></a>既存の NSG を表示する
特定のリソース グループの NSG 一覧を表示するには、次のように `azure network nsg list` コマンドを実行します。

```azurecli
azure network nsg list --resource-group RG-NSG
```

予想される出力:

    info:    Executing command network nsg list
    + Getting the network security groups
    data:    Name          Location
    data:    ------------  --------
    data:    NSG-BackEnd   westus
    data:    NSG-FrontEnd  westus
    info:    network nsg list command OK

### <a name="list-all-rules-for-an-nsg"></a>NSG のすべての規則を一覧表示する
**NSG-FrontEnd`azure network nsg show` という NSG の規則を表示するには、次のように ** コマンドを実行します。 

```azurecli
azure network nsg show --resource-group RG-NSG --name NSG-FrontEnd
```

予想される出力:

    info:    Executing command network nsg show
    + Looking up the network security group "NSG-FrontEnd"
    data:    Id                              : /subscriptions/[Subscription Id]/resourceGroups/RG-NSG/providers/Microsoft.Network/networkSecurityGroups/NSG-FrontEnd
    data:    Name                            : NSG-FrontEnd
    data:    Type                            : Microsoft.Network/networkSecurityGroups
    data:    Location                        : westus
    data:    Provisioning state              : Succeeded
    data:    Tags                            : displayName=NSG - Front End
    data:    Security group rules:
    data:    Name                           Source IP          Source Port  Destination IP  Destination Port  Protocol  Direction  Access  Priority
    data:    -----------------------------  -----------------  -----------  --------------  ----------------  --------  ---------  ------  --------
    data:    rdp-rule                       Internet           *            *               3389              Tcp       Inbound    Allow   100
    data:    web-rule                       Internet           *            *               80                Tcp       Inbound    Allow   101
    data:    AllowVnetInBound               VirtualNetwork     *            VirtualNetwork  *                 *         Inbound    Allow   65000
    data:    AllowAzureLoadBalancerInBound  AzureLoadBalancer  *            *               *                 *         Inbound    Allow   65001
    data:    DenyAllInBound                 *                  *            *               *                 *         Inbound    Deny    65500
    data:    AllowVnetOutBound              VirtualNetwork     *            VirtualNetwork  *                 *         Outbound   Allow   65000
    data:    AllowInternetOutBound          *                  *            Internet        *                 *         Outbound   Allow   65001
    data:    DenyAllOutBound                *                  *            *               *                 *         Outbound   Deny    65500
    info:    network nsg show command OK

> [!NOTE]
> `azure network nsg rule list --resource-group RG-NSG --nsg-name NSG-FrontEnd` を使用して、**NSG-FrontEnd** NSG の規則を一覧表示することもできます。
>

### <a name="view-nsg-associations"></a>NSG の関連付けを表示する

**NSG-FrontEnd** NSG が関連付けられているリソースを表示するには、次のように `azure network nsg show` コマンドを実行します。 唯一の違いは、 **--json** パラメーターを使用している点です。

```azurecli
azure network nsg show --resource-group RG-NSG --name NSG-FrontEnd --json
```

次の **NetworkInterfaces** と **Subnets** の各プロパティをご確認ください。

    "networkInterfaces": [],
    ...
    "subnets": [
        {
            "id": "/subscriptions/[Subscription Id]/resourceGroups/RG-NSG/providers/Microsoft.Network/virtualNetworks/TestVNet/subnets/FrontEnd"
        }
    ],
    ...

上の例では、NSG はネットワーク インターフェイス (NIC) に関連付けられておらず、 **FrontEnd**というサブネットに関連付けられています。

## <a name="manage-rules"></a>規則を管理する
既存の NSG に規則を追加し、既存の規則を編集し、規則を削除することができます。

### <a name="add-a-rule"></a>規則を追加する
任意のコンピューターから **NSG-FrontEnd** NSG へのポート **443** に対する**受信**トラフィックを許可する規則を追加するには、次のコマンドを入力します。

```azurecli
azure network nsg rule create --resource-group RG-NSG \
    --nsg-name NSG-FrontEnd \
    --name allow-https \
    --description "Allow access to port 443 for HTTPS" \
    --protocol Tcp \
    --source-address-prefix * \
    --source-port-range * \
    --destination-address-prefix * \
    --destination-port-range 443 \
    --access Allow \
    --priority 102 \
    --direction Inbound
```

予想される出力:

    info:    Executing command network nsg rule create
    + Looking up the network security rule "allow-https"
    + Creating a network security rule "allow-https"
    + Looking up the network security group "NSG-FrontEnd"
    data:    Id                              : /subscriptions/[Subscription Id]/resourceGroups/RG-NSG/providers/Microsoft.Network/networkSecurityGroups/NSG-FrontEnd/securityRules/allow-https
    data:    Name                            : allow-https
    data:    Type                            : Microsoft.Network/networkSecurityGroups/securityRules
    data:    Provisioning state              : Succeeded
    data:    Description                     : Allow access to port 443 for HTTPS
    data:    Source IP                       : *
    data:    Source Port                     : *
    data:    Destination IP                  : *
    data:    Destination Port                : 443
    data:    Protocol                        : Tcp
    data:    Direction                       : Inbound
    data:    Access                          : Allow
    data:    Priority                        : 102
    info:    network nsg rule create command OK

### <a name="change-a-rule"></a>規則を変更する
上の手順で作成した規則を、 **インターネット** からの受信トラフィックのみを許可する規則に変更するには、次のコマンドを実行します。

```azurecli
azure network nsg rule set --resource-group RG-NSG \
    --nsg-name NSG-FrontEnd \
    --name allow-https \
    --source-address-prefix Internet
```

予想される出力:

    info:    Executing command network nsg rule set
    + Looking up the network security group "NSG-FrontEnd"
    + Setting a network security rule "allow-https"
    + Looking up the network security group "NSG-FrontEnd"
    data:    Id                              : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/RG-NSG/providers/Microsoft.Network/networkSecurityGroups/NSG-FrontEnd/securityRules/allow-https
    data:    Name                            : allow-https
    data:    Type                            : Microsoft.Network/networkSecurityGroups/securityRules
    data:    Provisioning state              : Succeeded
    data:    Description                     : Allow access to port 443 for HTTPS
    data:    Source IP                       : Internet
    data:    Source Port                     : *
    data:    Destination IP                  : *
    data:    Destination Port                : 443
    data:    Protocol                        : Tcp
    data:    Direction                       : Inbound
    data:    Access                          : Allow
    data:    Priority                        : 102
    info:    network nsg rule set command OK

### <a name="delete-a-rule"></a>規則を削除する
先ほど作成した規則を削除するには、次のコマンドを実行します。

```azurecli
azure network nsg rule delete --resource-group RG-NSG \
    --nsg-name NSG-FrontEnd \
    --name allow-https \
    --quiet
```

> [!NOTE]
> `--quiet` パラメーターを指定すると、削除の確認が求められなくなります。
>

予想される出力:

    info:    Executing command network nsg rule delete
    + Looking up the network security group "NSG-FrontEnd"
    + Deleting network security rule "allow-https"
    info:    network nsg rule delete command OK

## <a name="manage-associations"></a>関連付けを管理する
NSG をサブネットと NIC に関連付けることができます。 また、NSG とリソースの関連付けを解除することもできます。

### <a name="associate-an-nsg-to-a-nic"></a>NSG を NIC に関連付ける
**NSG-FrontEnd** NSG を **TestNICWeb1** NIC に関連付けるには、次のコマンドを実行します。

```azurecli
azure network nic set --resource-group RG-NSG \
    --name TestNICWeb1 \
    --network-security-group-name NSG-FrontEnd
```

予想される出力:

    info:    Executing command network nic set
    + Looking up the network interface "TestNICWeb1"
    + Looking up the network security group "NSG-FrontEnd"
    + Updating network interface "TestNICWeb1"
    + Looking up the network interface "TestNICWeb1"
    data:    Id                              : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/RG-NSG/providers/Microsoft.Network/networkInterfaces/TestNICWeb1
    data:    Name                            : TestNICWeb1
    data:    Type                            : Microsoft.Network/networkInterfaces
    data:    Location                        : westus
    data:    Provisioning state              : Succeeded
    data:    MAC address                     : 00-0D-3A-30-A1-F8
    data:    Enable IP forwarding            : false
    data:    Tags                            : displayName=NetworkInterfaces - Web
    data:    Network security group          : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/RG-NSG/providers/Microsoft.Network/networkSecurityGroups/NSG-FrontEnd
    data:    Virtual machine                 : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/RG-NSG/providers/Microsoft.Compute/virtualMachines/Web1
    data:    IP configurations:
    data:      Name                          : ipconfig1
    data:      Provisioning state            : Succeeded
    data:      Public IP address             : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/RG-NSG/providers/Microsoft.Network/publicIPAddresses/TestPIPWeb1
    data:      Private IP address            : 192.168.1.5
    data:      Private IP Allocation Method  : Dynamic
    data:      Subnet                        : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/RG-NSG/providers/Microsoft.Network/virtualNetworks/TestVNet/subnets/FrontEnd
    data:
    info:    network nic set command OK

### <a name="dissociate-an-nsg-from-a-nic"></a>NSG と NIC の関連付けを解除する

**NSG-FrontEnd** NSG と **TestNICWeb1** NIC の関連付けを解除するには、次のコマンドを実行します。

```azurecli
azure network nic set --resource-group RG-NSG --name TestNICWeb1 --network-security-group-id ""
```

> [!NOTE]
> `network-security-group-id` パラメーターの値が "" (空白) になります。 空の値を指定することで、NSG に対する関連付けが削除されます。 `network-security-group-name` パラメーターで同じ操作を行うことはできません。
> 

予想される結果:

    info:    Executing command network nic set
    + Looking up the network interface "TestNICWeb1"
    + Updating network interface "TestNICWeb1"
    + Looking up the network interface "TestNICWeb1"
    data:    Id                              : /subscriptions/[Subscription Id]/resourceGroups/RG-NSG/providers/Microsoft.Network/networkInterfaces/TestNICWeb1
    data:    Name                            : TestNICWeb1
    data:    Type                            : Microsoft.Network/networkInterfaces
    data:    Location                        : westus
    data:    Provisioning state              : Succeeded
    data:    MAC address                     : 00-0D-3A-30-A1-F8
    data:    Enable IP forwarding            : false
    data:    Tags                            : displayName=NetworkInterfaces - Web
    data:    Virtual machine                 : /subscriptions/[Subscription Id]/resourceGroups/RG-NSG/providers/Microsoft.Compute/virtualMachines/Web1
    data:    IP configurations:
    data:      Name                          : ipconfig1
    data:      Provisioning state            : Succeeded
    data:      Public IP address             : /subscriptions/[Subscription Id]/resourceGroups/RG-NSG/providers/Microsoft.Network/publicIPAddresses/TestPIPWeb1
    data:      Private IP address            : 192.168.1.5
    data:      Private IP Allocation Method  : Dynamic
    data:      Subnet                        : /subscriptions/[Subscription Id]/resourceGroups/RG-NSG/providers/Microsoft.Network/virtualNetworks/TestVNet/subnets/FrontEnd
    data:
    info:    network nic set command OK

### <a name="dissociate-an-nsg-from-a-subnet"></a>NSG とサブネットの関連付けを解除する
**NSG-FrontEnd** NSG と **FrontEnd** サブネットの関連付けを解除するには、次のコマンドを実行します。

```azurecli
azure network vnet subnet set --resource-group RG-NSG \
    --vnet-name TestVNet \
    --name FrontEnd \
    --network-security-group-id ""
```

予想される出力:

    info:    Executing command network vnet subnet set
    + Looking up the subnet "FrontEnd"
    + Setting subnet "FrontEnd"
    + Looking up the subnet "FrontEnd"
    data:    Id                              : /subscriptions/[Subscription Id]/resourceGroups/RG-NSG/providers/Microsoft.Network/virtualNetworks/TestVNet/subnets/FrontEnd
    data:    Type                            : Microsoft.Network/virtualNetworks/subnets
    data:    ProvisioningState               : Succeeded
    data:    Name                            : FrontEnd
    data:    Address prefix                  : 192.168.1.0/24
    data:    IP configurations:
    data:      /subscriptions/[Subscription Id]/resourceGroups/RG-NSG/providers/Microsoft.Network/networkInterfaces/TestNICWeb2/ipConfigurations/ipconfig1
    data:      /subscriptions/[Subscription Id]/resourceGroups/RG-NSG/providers/Microsoft.Network/networkInterfaces/TestNICWeb1/ipConfigurations/ipconfig1
    data:
    info:    network vnet subnet set command OK

### <a name="associate-an-nsg-to-a-subnet"></a>NSG サブネットへの関連付け
**NSG-FrontEnd** NSG を再度 **FrontEnd** サブネットに関連付けるには、次のコマンドを実行します。

```azurecli
azure network vnet subnet set --resource-group RG-NSG \
    --vnet-name TestVNet \
    --name FrontEnd \
    --network-security-group-name NSG-FronEnd
```

> [!NOTE]
> 上のコマンドが動作するのは、 **NSG-FrontEnd** NSG が仮想ネットワーク **TestVNet** と同じリソース グループ内にある場合のみです。 NSG が別のリソース グループにある場合は、代わりに `--network-security-group-id` パラメーターを使用して、NSG の完全な ID を指定する必要があります。 ID を取得するには、`azure network nsg show --resource-group RG-NSG --name NSG-FrontEnd --json` を実行して、**id** プロパティを確認します。 
> 

予想される出力:

        info:    Executing command network vnet subnet set
        + Looking up the subnet "FrontEnd"
        + Looking up the network security group "NSG-FrontEnd"
        + Setting subnet "FrontEnd"
        + Looking up the subnet "FrontEnd"
        data:    Id                              : /subscriptions/[Subscription Id]/resourceGroups/RG-NSG/providers/Microsoft.Network/virtualNetworks/TestVNet/subnets/FrontEnd
        data:    Type                            : Microsoft.Network/virtualNetworks/subnets
        data:    ProvisioningState               : Succeeded
        data:    Name                            : FrontEnd
        data:    Address prefix                  : 192.168.1.0/24
        data:    Network security group          : [object Object]
        data:    IP configurations:
        data:      /subscriptions/[Subscription Id]resourceGroups/RG-NSG/providers/Microsoft.Network/networkInterfaces/TestNICWeb2/ipConfigurations/ipconfig1
        data:      /subscriptions/[Subscription Id]/resourceGroups/RG-NSG/providers/Microsoft.Network/networkInterfaces/TestNICWeb1/ipConfigurations/ipconfig1
        data:
        info:    network vnet subnet set command OK

## <a name="delete-an-nsg"></a>NSG の削除
NSG に関連付けられているリソースがない場合にのみ、NSG を削除できます。 NSG を削除するには、次の手順を実行します。

1. NSG に関連付けられているリソースを確認するには、「[NSG の関連付けを表示する](#View-NSGs-associations)」を参照し、`azure network nsg show` を実行します。
2. NSG に関連付けられている NIC がある場合は、「[NSG と NIC の関連付けを解除する](#Dissociate-an-NSG-from-a-NIC)」を参照し、各 NIC について `azure network nic set` を実行します。 
3. NSG に関連付けられているサブネットがある場合は、「[NSG とサブネットの関連付けを解除する](#Dissociate-an-NSG-from-a-subnet)」を参照し、各サブネットについて `azure network vnet subnet set` を実行します。
4. NSG を削除するには次のコマンドを実行します。

    ```azurecli
    azure network nsg delete --resource-group RG-NSG --name NSG-FrontEnd --quiet
    ```

    予想される出力:

        info:    Executing command network nsg delete
        + Looking up the network security group "NSG-FrontEnd"
        + Deleting network security group "NSG-FrontEnd"
        info:    network nsg delete command OK

## <a name="next-steps"></a>次のステップ
* [ログを有効にします](virtual-network-nsg-manage-log.md) 。


