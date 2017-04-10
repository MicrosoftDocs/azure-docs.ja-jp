---
title: "ネットワーク セキュリティ グループの管理 - Azure PowerShell | Microsoft Docs"
description: "PowerShell を使用してネットワーク セキュリティ グループを管理する方法について説明します。"
services: virtual-network
documentationcenter: na
author: jimdial
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 3706ce6c-d9ae-46cb-a048-f0a4e84dc5cc
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/14/2016
ms.author: jdial
ms.custom: H1Hack27Feb2017
translationtype: Human Translation
ms.sourcegitcommit: 63f2f6dde56c1b5c4b3ad2591700f43f6542874d
ms.openlocfilehash: edb23ae41e175061607d3a191c839e1194fa862b
ms.lasthandoff: 02/28/2017


---
# <a name="manage-network-security-groups-using-powershell"></a>PowerShell を使用したネットワーク セキュリティ グループの管理

[!INCLUDE [virtual-network-manage-arm-selectors-include.md](../../includes/virtual-network-manage-nsg-arm-selectors-include.md)]

[!INCLUDE [virtual-network-manage-nsg-intro-include.md](../../includes/virtual-network-manage-nsg-intro-include.md)]

> [!NOTE]
> Azure には、リソースの作成と操作に関して、[Resource Manager とクラシックの](../resource-manager-deployment-model.md)&2; 種類のデプロイメント モデルがあります。 この記事では、Resource Manager デプロイメント モデルの使用方法について取り上げていますが、最新のデプロイでは、クラシック デプロイメント モデルではなくこのモデルをお勧めします。
>

[!INCLUDE [virtual-network-manage-nsg-arm-scenario-include.md](../../includes/virtual-network-manage-nsg-arm-scenario-include.md)]

[!INCLUDE [azure-ps-prerequisites-include.md](../../includes/azure-ps-prerequisites-include.md)]

## <a name="retrieve-information"></a>情報を取得する
既存の NSG を表示し、既存の NSG の規則を取得し、NSG が関連付けられているリソースを検索することができます。

### <a name="view-existing-nsgs"></a>既存の NSG を表示する
サブスクリプションの既存の NSG をすべて表示するには、`Get-AzureRmNetworkSecurityGroup` コマンドレットを実行します。

予想される結果:

    Name                 : NSG-BackEnd
    ResourceGroupName    : RG-NSG
    Location             : westus
    Id                   : /subscriptions/[Subscription Id]/resourceGroups/RG-NSG/providers/
                           Microsoft.Network/networkSecurityGroups/NSG-BackEnd
    Etag                 : W/"[Id]"
    ResourceGuid         : [Id]
    ProvisioningState    : Succeeded
    Tags                 :                            
    SecurityRules        : [...]
    DefaultSecurityRules : [...]
    NetworkInterfaces    : [...]
    Subnets              : [...]

    Name                 : NSG-FrontEnd
    ResourceGroupName    : RG-NSG
    Location             : eastus
    Id                   : /subscriptions/[Subscription Id]/resourceGroups/NRP-RG/providers/
                           Microsoft.Network/networkSecurityGroups/NSG-FrontEnd
    Etag                 : W/"[Id]"
    ResourceGuid         : [Id]
    ProvisioningState    : Succeeded
    Tags                 : 
    SecurityRules        : [...]
    DefaultSecurityRules : [...]
    NetworkInterfaces    : [...]
    Subnets              : [...]

    Name                 : WEB1
    ResourceGroupName    : RG101
    Location             : eastus2
    Id                   : /subscriptions/[Subscription Id]/resourceGroups/RG101/providers/M
                           icrosoft.Network/networkSecurityGroups/WEB1
    Etag                 : W/"[Id]"
    ResourceGuid         : [Id]
    ProvisioningState    : Succeeded
    Tags                 : 
    SecurityRules        : [...]
    DefaultSecurityRules : [...]
    NetworkInterfaces    : [...]
    Subnets              : [...]


特定のリソース グループの NSG 一覧を表示するには、`Get-AzureRmNetworkSecurityGroup` コマンドレットを実行します。

予想される出力:

    Name                 : NSG-BackEnd
    ResourceGroupName    : RG-NSG
    Location             : westus
    Id                   : /subscriptions/[Subscription Id]/resourceGroups/RG-NSG/providers/
                           Microsoft.Network/networkSecurityGroups/NSG-BackEnd
    Etag                 : W/"[Id]"
    ResourceGuid         : [Id]
    ProvisioningState    : Succeeded
    Tags                 :                            
    SecurityRules        : [...]
    DefaultSecurityRules : [...]
    NetworkInterfaces    : [...]
    Subnets              : [...]

    Name                 : NSG-FrontEnd
    ResourceGroupName    : RG-NSG
    Location             : eastus
    Id                   : /subscriptions/[Subscription Id]/resourceGroups/NRP-RG/providers/
                           Microsoft.Network/networkSecurityGroups/NSG-FrontEnd
    Etag                 : W/"[Id]"
    ResourceGuid         : [Id]
    ProvisioningState    : Succeeded
    Tags                 : 
    SecurityRules        : [...]
    DefaultSecurityRules : [...]
    NetworkInterfaces    : [...]
    Subnets              : [...]

### <a name="list-all-rules-for-an-nsg"></a>NSG のすべての規則を一覧表示する
**NSG-FrontEnd**という NSG の規則を表示するには、次のコマンドを実行します。

```powershell
Get-AzureRmNetworkSecurityGroup -ResourceGroupName RG-NSG -Name NSG-FrontEnd | Select SecurityRules -ExpandProperty SecurityRules
```

予想される出力:

    Name                     : rdp-rule
    Id                       : /subscriptions/[Subscription Id]/resourceGroups/RG-NSG/providers/                           Microsoft.Network/networkSecurityGroups/NSG-FrontEnd/securityRules/rdp-rule
    Etag                     : W/"[Id]"
    ProvisioningState        : Succeeded
    Description              : Allow RDP
    Protocol                 : Tcp
    SourcePortRange          : *
    DestinationPortRange     : 3389
    SourceAddressPrefix      : Internet
    DestinationAddressPrefix : *
    Access                   : Allow
    Priority                 : 100
    Direction                : Inbound

    Name                     : web-rule
    Id                       : /subscriptions/[Subscription Id]/resourceGroups/RG-NSG/providers/                           Microsoft.Network/networkSecurityGroups/NSG-FrontEnd/securityRules/web-rule
    Etag                     : W/"[Id]"
    ProvisioningState        : Succeeded
    Description              : Allow HTTP
    Protocol                 : Tcp
    SourcePortRange          : *
    DestinationPortRange     : 80
    SourceAddressPrefix      : Internet
    DestinationAddressPrefix : *
    Access                   : Allow
    Priority                 : 101
    Direction                : Inbound

> [!NOTE]
> `Get-AzureRmNetworkSecurityGroup -ResourceGroupName RG-NSG -Name "NSG-FrontEnd" | Select DefaultSecurityRules -ExpandProperty DefaultSecurityRules` を使用して、**NSG-FrontEnd** NSG の既定の規則を一覧表示することもできます。
> 

### <a name="view-nsgs-associations"></a>NSG の関連付けを表示する
**NSG-FrontEnd** NSG が関連付けられているリソースを表示するには、次のコマンドを実行します。

```powershell
Get-AzureRmNetworkSecurityGroup -ResourceGroupName RG-NSG -Name NSG-FrontEnd
```

次の **NetworkInterfaces** と **Subnets** の各プロパティをご確認ください。

    NetworkInterfaces    : []
    Subnets              : [
                             {
                               "Id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/RG-NSG/providers/Microsoft.Network/virtualNetworks/TestVNet/subnets/FrontEnd",
                               "IpConfigurations": []
                             }
                           ]

上の例では、NSG はネットワーク インターフェイス (NIC) に関連付けられておらず、**FrontEnd**というサブネットに関連付けられています。

## <a name="manage-rules"></a>規則を管理する
既存の NSG に規則を追加し、既存の規則を編集し、規則を削除することができます。

### <a name="add-a-rule"></a>規則を追加する
任意のコンピューターから **NSG-FrontEnd** NSG へのポート **443** に対する**受信**トラフィックを許可する規則を追加するには、次の手順を実行します。

1. 次のコマンドを実行して既存の NSG を取得し、変数に格納します。

    ```powershell   
    $nsg = Get-AzureRmNetworkSecurityGroup -ResourceGroupName RG-NSG -Name NSG-FrontEnd
    ```

2. 次のコマンドを実行して、NSG に規則を追加します。

    ```powershell
    Add-AzureRmNetworkSecurityRuleConfig -NetworkSecurityGroup $nsg `
    -Name https-rule `
    -Description "Allow HTTPS" `
    -Access Allow `
    -Protocol Tcp `
    -Direction Inbound `
    -Priority 102 `
    -SourceAddressPrefix * `
    -SourcePortRange * `
    -DestinationAddressPrefix * `
    -DestinationPortRange 443
    ```

3. NSG に対する変更を保存するには、次のコマンドを実行します。

    ```powershell
    Set-AzureRmNetworkSecurityGroup -NetworkSecurityGroup $nsg
    ```
    予想される出力 (セキュリティ規則のみを表示):
   
        Name                 : NSG-FrontEnd
        ...
        SecurityRules        : [
                                 {
                                   "Name": "rdp-rule",
                                   ...
                                 },
                                 {
                                   "Name": "web-rule",
                                   ...
                                 },
                                 {
                                   "Name": "https-rule",
                                   "Etag": "W/\"[Id]\"",
                                   "Id": "/subscriptions/[Subscription Id]/resourceGroups/RG-NSG/providers/Microsoft.Network/networkSecurityGroups/NSG-FrontEnd/securityRules/https-rule",
                                   "Description": "Allow HTTPS",
                                   "Protocol": "Tcp",
                                   "SourcePortRange": "*",
                                   "DestinationPortRange": "443",
                                   "SourceAddressPrefix": "*",
                                   "DestinationAddressPrefix": "*",
                                   "Access": "Allow",
                                   "Priority": 102,
                                   "Direction": "Inbound",
                                   "ProvisioningState": "Succeeded"
                                 }
                               ]

### <a name="change-a-rule"></a>規則を変更する
上の手順で作成した規則を、 **インターネット** からの受信トラフィックのみを許可する規則に変更するには、次の手順を実行します。

1. 次のコマンドを実行して既存の NSG を取得し、変数に格納します。

    ```powershell 
    $nsg = Get-AzureRmNetworkSecurityGroup -ResourceGroupName RG-NSG -Name NSG-FrontEnd
    ```

2. 新しい規則の設定で、次のコマンドを実行します。

    ```powershell
    Set-AzureRmNetworkSecurityRuleConfig -NetworkSecurityGroup $nsg `
    -Name https-rule `
    -Description "Allow HTTPS" `
    -Access Allow `
    -Protocol Tcp `
    -Direction Inbound `
    -Priority 102 `
    -SourceAddressPrefix * `
    -SourcePortRange Internet `
    -DestinationAddressPrefix * `
    -DestinationPortRange 443
    ```

3. NSG に対する変更を保存するには、次のコマンドを実行します。

    ```powershell
    Set-AzureRmNetworkSecurityGroup -NetworkSecurityGroup $nsg
    ```

    予想される出力 (セキュリティ規則のみを表示):
   
        Name                 : NSG-FrontEnd
        ...
        SecurityRules        : [
                                 {
                                   "Name": "rdp-rule",
                                   ...
                                 },
                                 {
                                   "Name": "web-rule",
                                   ...
                                 },
                                 {
                                   "Name": "https-rule",
                                   "Etag": "W/\"[Id]\"",
                                   "Id": "/subscriptions/[Subscription Id]/resourceGroups/RG-NSG/providers/Microsoft.Network/networkSecurityGroups/NSG-FrontEnd/securityRules/https-rule",
                                   "Description": "Allow HTTPS",
                                   "Protocol": "Tcp",
                                   "SourcePortRange": "*",
                                   "DestinationPortRange": "443",
                                   "SourceAddressPrefix": "Internet",
                                   "DestinationAddressPrefix": "*",
                                   "Access": "Allow",
                                   "Priority": 102,
                                   "Direction": "Inbound",
                                   "ProvisioningState": "Succeeded"
                                 }
                               ]

### <a name="delete-a-rule"></a>規則を削除する
1. 次のコマンドを実行して既存の NSG を取得し、変数に格納します。

    ```powershell
    $nsg = Get-AzureRmNetworkSecurityGroup -ResourceGroupName RG-NSG -Name NSG-FrontEnd
    ```

2. 次のコマンドを実行して NSG から規則を削除します。

    ```powershell
    Remove-AzureRmNetworkSecurityRuleConfig -NetworkSecurityGroup $nsg -Name https-rule
    ```

3. NSG に対する変更を保存するには、次のコマンドを実行します。

    ```powershell
    Set-AzureRmNetworkSecurityGroup -NetworkSecurityGroup $nsg
    ```

    予想される出力 (セキュリティ規則のみを表示。**https-rule** がない点に注目してください):
   
        Name                 : NSG-FrontEnd
        ...
        SecurityRules        : [
                                 {
                                   "Name": "rdp-rule",
                                   ...
                                 },
                                 {
                                   "Name": "web-rule",
                                   ...
                                 }
                               ]

## <a name="manage-associations"></a>関連付けを管理する
NSG をサブネットと NIC に関連付けることができます。 また、NSG とリソースの関連付けを解除することもできます。

### <a name="associate-an-nsg-to-a-nic"></a>NSG を NIC に関連付ける
**NSG-FrontEnd** NSG を **TestNICWeb1** NIC に関連付けるには、次の手順を実行します。

1. 次のコマンドを実行して既存の NSG を取得し、変数に格納します。

    ```powershell
    $nsg = Get-AzureRmNetworkSecurityGroup -ResourceGroupName RG-NSG -Name NSG-FrontEnd
    ```

2. 次のコマンドを実行して既存の NIC を取得し、変数に格納します。

    ```powershell
    $nic = Get-AzureRmNetworkInterface -ResourceGroupName RG-NSG -Name TestNICWeb1
    ```

3. 次のコマンドを実行して、**NIC** 変数の **NetworkSecurityGroup** プロパティを **NSG** 変数の値に設定します。

    ```powershell
    $nic.NetworkSecurityGroup = $nsg
    ```

4. NIC に対する変更を保存するには、次のコマンドを実行します。

    ```powershell
    Set-AzureRmNetworkInterface -NetworkInterface $nic
    ```
   
    予想される出力 ( **NetworkSecurityGroup** プロパティのみを表示):
   
        NetworkSecurityGroup : {
                                 "SecurityRules": [],
                                 "DefaultSecurityRules": [],
                                 "NetworkInterfaces": [],
                                 "Subnets": [],
                                 "Id": "/subscriptions/[Subscription Id]/resourceGroups/RG-NSG/providers/Microsoft.Network/networkSecurityGroups/NSG-FrontEnd"
                               }

### <a name="dissociate-an-nsg-from-a-nic"></a>NSG と NIC の関連付けを解除する
**NSG-FrontEnd** NSG と **TestNICWeb1** NIC の関連付けを解除するには、次の手順を実行します。

1. 次のコマンドを実行して既存の NIC を取得し、変数に格納します。

    ```powershell
    $nic = Get-AzureRmNetworkInterface -ResourceGroupName RG-NSG -Name TestNICWeb1
    ```

2. 次のコマンドを実行して、**NIC** 変数の **NetworkSecurityGroup** プロパティを **$null** に設定します。

    ```powershell
    $nic.NetworkSecurityGroup = $null
    ```

3. NIC に対する変更を保存するには、次のコマンドを実行します。

    ```powershell
    Set-AzureRmNetworkInterface -NetworkInterface $nic
    ```
   
    予想される出力 ( **NetworkSecurityGroup** プロパティのみを表示):
   
        NetworkSecurityGroup : null

### <a name="dissociate-an-nsg-from-a-subnet"></a>NSG とサブネットの関連付けを解除する
**NSG-FrontEnd** NSG と **FrontEnd** サブネットの関連付けを解除するには、次の手順を実行します。

1. 次のコマンドを実行して既存の VNet を取得し、変数に格納します。

    ```powershell
    $vnet = Get-AzureRmVirtualNetwork -ResourceGroupName RG-NSG -Name TestVNet
    ```

2. 次のコマンドを実行して既存の **FrontEnd** サブネットを取得し、変数に格納します。

    ```powershell
    $subnet = Get-AzureRmVirtualNetworkSubnetConfig -VirtualNetwork $vnet -Name FrontEnd
    ```
 
3. 次のコマンドを実行して、**サブネット** 変数の **NetworkSecurityGroup** プロパティを **$null** に設定します。

    ```powershell
    $subnet.NetworkSecurityGroup = $null
    ```

4. サブネットに対する変更を保存するには、次のコマンドを実行します。

    ```powershell
    Set-AzureRmVirtualNetwork -VirtualNetwork $vnet
    ```

    予想される出力 ( **FrontEnd** サブネットのプロパティのみを表示。 **NetworkSecurityGroup**のプロパティがない点に注目してください):
   
            ...
            Subnets           : [
                                  {
                                    "Name": "FrontEnd",
                                    "Etag": "W/\"[Id]\"",
                                    "Id": "/subscriptions/[Subscription Id]/resourceGroups/RG-NSG/providers/Microsoft.Network/virtualNetworks/TestVNet/subnets/FrontEnd",
                                    "AddressPrefix": "192.168.1.0/24",
                                    "IpConfigurations": [
                                      {
                                        "Id": "/subscriptions/[Subscription Id]/resourceGroups/RG-NSG/providers/Microsoft.Network/networkInterfaces/TestNICWeb2/ipConfigurations/ipconfig1"
                                      },
                                      {
                                        "Id": "/subscriptions/[Subscription Id]/resourceGroups/RG-NSG/providers/Microsoft.Network/networkInterfaces/TestNICWeb1/ipConfigurations/ipconfig1"
                                      }
                                    ],
                                    "ProvisioningState": "Succeeded"
                                  },
                                    ...
                                ]

### <a name="associate-an-nsg-to-a-subnet"></a>NSG サブネットへの関連付け
**NSG-FrontEnd** NSG を **FronEnd** サブネットにもう一度関連付けるには、次の手順を実行します。

1. 次のコマンドを実行して既存の VNet を取得し、変数に格納します。

    ```powershell
    $vnet = Get-AzureRmVirtualNetwork -ResourceGroupName RG-NSG -Name TestVNet
    ```

2. 次のコマンドを実行して既存の **FrontEnd** サブネットを取得し、変数に格納します。

    ```powershell
    $subnet = Get-AzureRmVirtualNetworkSubnetConfig -VirtualNetwork $vnet -Name FrontEnd
    ```
 
3. 次のコマンドを実行して既存の NSG を取得し、変数に格納します。

    ```powershell
    $nsg = Get-AzureRmNetworkSecurityGroup -ResourceGroupName RG-NSG -Name NSG-FrontEnd
    ```

4. 次のコマンドを実行して、**サブネット** 変数の **NetworkSecurityGroup** プロパティを **$null** に設定します。

    ```powershell
    $subnet.NetworkSecurityGroup = $nsg
    ```

5. サブネットに対する変更を保存するには、次のコマンドを実行します。

    ```powershell
    Set-AzureRmVirtualNetwork -VirtualNetwork $vnet
    ```

    予想される出力 (**FrontEnd** サブネットの **NetworkSecurityGroup** プロパティのみを表示):
   
        ...
        "NetworkSecurityGroup": {
                                  "SecurityRules": [],
                                  "DefaultSecurityRules": [],
                                  "NetworkInterfaces": [],
                                  "Subnets": [],
                                  "Id": "/subscriptions/[Subscription Id]/resourceGroups/RG-NSG/providers/Microsoft.Network/networkSecurityGroups/NSG-FrontEnd"
                                }
        ...

## <a name="delete-an-nsg"></a>NSG の削除
NSG に関連付けられているリソースがない場合にのみ、NSG を削除できます。 NSG を削除するには、次の手順を実行します。

1. NSG に関連付けられているリソースを確認するには、「[NSG の関連付けを表示する](#View-NSGs-associations)」を参照し、`azure network nsg show` を実行します。
2. NSG に関連付けられている NIC がある場合は、「[NSG と NIC の関連付けを解除する](#Dissociate-an-NSG-from-a-NIC)」を参照し、各 NIC について `azure network nic set` を実行します。 
3. NSG に関連付けられているサブネットがある場合は、「[NSG とサブネットの関連付けを解除する](#Dissociate-an-NSG-from-a-subnet)」を参照し、各サブネットについて `azure network vnet subnet set` を実行します。
4. NSG を削除するには次のコマンドを実行します。

    ```powershell
    Remove-AzureRmNetworkSecurityGroup -ResourceGroupName RG-NSG -Name NSG-FrontEnd -Force
    ```
   
   > [!NOTE]
   > `-Force` パラメーターを指定すると、削除の確認が求められなくなります。
   > 

## <a name="next-steps"></a>次のステップ
* [ログを有効にします](virtual-network-nsg-manage-log.md) 。


