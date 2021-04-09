---
title: Azure CLI を使用して Linux 環境を作成する
description: Azure CLI を使用して、ストレージ、Linux VM、仮想ネットワークとサブネット、ロード バランサー、NIC、パブリック IP、ネットワーク セキュリティ グループすべてを新しく作成します。
author: cynthn
ms.service: virtual-machines
ms.collection: linux
ms.topic: how-to
ms.date: 12/14/2017
ms.author: cynthn
ms.openlocfilehash: ff86651d56abe090ca08c508a220362f9a011a3f
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102554702"
---
# <a name="create-a-complete-linux-virtual-machine-with-the-azure-cli"></a>Azure CLI を使用した完全な Linux 仮想マシンの作成
必要なサポート リソースすべてを既定値で作成する単一の Azure CLI コマンドを使用すると、Azure で仮想マシン (VM) を短時間で作成することができます。 仮想ネットワーク、パブリック IP アドレス、ネットワーク セキュリティ グループの規則などのリソースが自動的に作成されます。 実稼働用に環境をより細かく制御する場合は、こうしたリソースを先に作成してから、作成したリソースに VM を追加します。 この記事では、VM の作成方法、および各サポート リソースを 1 つずつ作成する方法を説明します。

[Azure CLI ](/cli/azure/install-az-cli2) の最新版がインストールされていること、および [az login](/cli/azure/reference-index) で Azure アカウントにログインしていることを確認します。

次の例では、パラメーター名を独自の値を置き換えます。 たとえば、*myResourceGroup*、*myVnet*、*myVM* といったパラメーター名にします。

## <a name="create-resource-group"></a>リソース グループの作成
Azure リソース グループとは、Azure リソースのデプロイと管理に使用する論理コンテナーです。 リソース グループは、仮想マシンおよび関連する仮想ネットワーク リソースよりも前に作成する必要があります。 [az group create](/cli/azure/group) で、リソース グループを作成します。 次の例では、*myResourceGroup* という名前のリソース グループを *eastus* に作成します。

```azurecli
az group create --name myResourceGroup --location eastus
```

既定では、Azure CLI コマンドの出力形式は JSON (JavaScript Object Notation) です。 既定の出力をリストまたはテーブルなどに変更するには、[az configure --output](/cli/azure/reference-index) を使用します。 出力形式で 1 回のみ変更するために `--output` を任意のコマンドに追加することもできます。 次の例に、`az group create` コマンドの JSON 形式の出力を示します。

```json                       
{
  "id": "/subscriptions/guid/resourceGroups/myResourceGroup",
  "location": "eastus",
  "name": "myResourceGroup",
  "properties": {
    "provisioningState": "Succeeded"
  },
  "tags": null
}
```

## <a name="create-a-virtual-network-and-subnet"></a>仮想ネットワークとサブネットの作成
次に、VM の作成場所となる Azure の仮想ネットワークとサブネットを作成します。 [az network vnet create](/cli/azure/network/vnet) を使用して、アドレス プレフィックスが *192.168.0.0/16* である *myVnet* という名前の仮想ネットワークを作成します。 また、アドレス プレフィックスが *192.168.1.0/24* である *mySubnet* という名前のサブネットも追加します。

```azurecli
az network vnet create \
    --resource-group myResourceGroup \
    --name myVnet \
    --address-prefix 192.168.0.0/16 \
    --subnet-name mySubnet \
    --subnet-prefix 192.168.1.0/24
```

次の出力では、サブネットが仮想ネットワークの内部に論理的に作成されたことが示されています。

```json
{
  "addressSpace": {
    "addressPrefixes": [
      "192.168.0.0/16"
    ]
  },
  "dhcpOptions": {
    "dnsServers": []
  },
  "etag": "W/\"e95496fc-f417-426e-a4d8-c9e4d27fc2ee\"",
  "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/virtualNetworks/myVnet",
  "location": "eastus",
  "name": "myVnet",
  "provisioningState": "Succeeded",
  "resourceGroup": "myResourceGroup",
  "resourceGuid": "ed62fd03-e9de-430b-84df-8a3b87cacdbb",
  "subnets": [
    {
      "addressPrefix": "192.168.1.0/24",
      "etag": "W/\"e95496fc-f417-426e-a4d8-c9e4d27fc2ee\"",
      "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/virtualNetworks/myVnet/subnets/mySubnet",
      "ipConfigurations": null,
      "name": "mySubnet",
      "networkSecurityGroup": null,
      "provisioningState": "Succeeded",
      "resourceGroup": "myResourceGroup",
      "resourceNavigationLinks": null,
      "routeTable": null
    }
  ],
  "tags": {},
  "type": "Microsoft.Network/virtualNetworks",
  "virtualNetworkPeerings": null
}
```


## <a name="create-a-public-ip-address"></a>パブリック IP アドレスの作成
次に、[az network public-ip create](/cli/azure/network/public-ip) を使用してパブリック IP アドレスを作成します。 インターネットからは、このパブリック IP アドレスで VM に接続することができます。 既定のアドレスは動的であるため、`--domain-name-label` パラメーターを指定して名前付きの DNS エントリを作成します。 次の例では、DNS 名が *mypublicdns* で *myPublicIP* という名前のパブリック IP を作成します  DNS 名は一意である必要があるので、独自の DNS 名を指定します。

```azurecli
az network public-ip create \
    --resource-group myResourceGroup \
    --name myPublicIP \
    --dns-name mypublicdns
```

出力:

```json
{
  "publicIp": {
    "dnsSettings": {
      "domainNameLabel": "mypublicdns",
      "fqdn": "mypublicdns.eastus.cloudapp.azure.com",
      "reverseFqdn": null
    },
    "etag": "W/\"2632aa72-3d2d-4529-b38e-b622b4202925\"",
    "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/publicIPAddresses/myPublicIP",
    "idleTimeoutInMinutes": 4,
    "ipAddress": null,
    "ipConfiguration": null,
    "location": "eastus",
    "name": "myPublicIP",
    "provisioningState": "Succeeded",
    "publicIpAddressVersion": "IPv4",
    "publicIpAllocationMethod": "Dynamic",
    "resourceGroup": "myResourceGroup",
    "resourceGuid": "4c65de38-71f5-4684-be10-75e605b3e41f",
    "tags": null,
    "type": "Microsoft.Network/publicIPAddresses"
  }
}
```


## <a name="create-a-network-security-group"></a>ネットワーク セキュリティ グループの作成
VM で送受信されるトラフィック フローを制御するために、仮想 NIC またはサブネットにネットワーク セキュリティ グループを適用します。 次の例では [az network nsg create](/cli/azure/network/nsg) を使用して、*myNetworkSecurityGroup* という名前のネットワーク セキュリティ グループを作成します。

```azurecli
az network nsg create \
    --resource-group myResourceGroup \
    --name myNetworkSecurityGroup
```

特定のトラフィックを許可または拒否する規則を定義します。 ポート 22 での受信接続を許可するために (SSH アクセスを有効にするため)、[az network nsg rule create](/cli/azure/network/nsg/rule) を使用して、受信規則を作成します。 次の例では、*myNetworkSecurityGroupRuleSSH* という名前の規則を作成します。

```azurecli
az network nsg rule create \
    --resource-group myResourceGroup \
    --nsg-name myNetworkSecurityGroup \
    --name myNetworkSecurityGroupRuleSSH \
    --protocol tcp \
    --priority 1000 \
    --destination-port-range 22 \
    --access allow
```

ポート 80 での受信接続を許可するために (Web トラフィックのため)、別のネットワーク セキュリティ グループ規則を追加します。 次の例では、*myNetworkSecurityGroupRuleHTTP* という名前の規則を作成します。

```azurecli
az network nsg rule create \
    --resource-group myResourceGroup \
    --nsg-name myNetworkSecurityGroup \
    --name myNetworkSecurityGroupRuleWeb \
    --protocol tcp \
    --priority 1001 \
    --destination-port-range 80 \
    --access allow
```

[az network nsg show](/cli/azure/network/nsg) で、ネットワーク セキュリティ グループと規則を調べます。

```azurecli
az network nsg show --resource-group myResourceGroup --name myNetworkSecurityGroup
```

出力:

```json
{
  "defaultSecurityRules": [
    {
      "access": "Allow",
      "description": "Allow inbound traffic from all VMs in VNET",
      "destinationAddressPrefix": "VirtualNetwork",
      "destinationPortRange": "*",
      "direction": "Inbound",
      "etag": "W/\"3371b313-ea9f-4687-a336-a8ebdfd80523\"",
      "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkSecurityGroups/myNetworkSecurityGroup/defaultSecurityRules/AllowVnetInBound",
      "name": "AllowVnetInBound",
      "priority": 65000,
      "protocol": "*",
      "provisioningState": "Succeeded",
      "resourceGroup": "myResourceGroup",
      "sourceAddressPrefix": "VirtualNetwork",
      "sourcePortRange": "*"
    },
    {
      "access": "Allow",
      "description": "Allow inbound traffic from azure load balancer",
      "destinationAddressPrefix": "*",
      "destinationPortRange": "*",
      "direction": "Inbound",
      "etag": "W/\"3371b313-ea9f-4687-a336-a8ebdfd80523\"",
      "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkSecurityGroups/myNetworkSecurityGroup/defaultSecurityRules/AllowAzureLoadBalancerInBou",
      "name": "AllowAzureLoadBalancerInBound",
      "priority": 65001,
      "protocol": "*",
      "provisioningState": "Succeeded",
      "resourceGroup": "myResourceGroup",
      "sourceAddressPrefix": "AzureLoadBalancer",
      "sourcePortRange": "*"
    },
    {
      "access": "Deny",
      "description": "Deny all inbound traffic",
      "destinationAddressPrefix": "*",
      "destinationPortRange": "*",
      "direction": "Inbound",
      "etag": "W/\"3371b313-ea9f-4687-a336-a8ebdfd80523\"",
      "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkSecurityGroups/myNetworkSecurityGroup/defaultSecurityRules/DenyAllInBound",
      "name": "DenyAllInBound",
      "priority": 65500,
      "protocol": "*",
      "provisioningState": "Succeeded",
      "resourceGroup": "myResourceGroup",
      "sourceAddressPrefix": "*",
      "sourcePortRange": "*"
    },
    {
      "access": "Allow",
      "description": "Allow outbound traffic from all VMs to all VMs in VNET",
      "destinationAddressPrefix": "VirtualNetwork",
      "destinationPortRange": "*",
      "direction": "Outbound",
      "etag": "W/\"3371b313-ea9f-4687-a336-a8ebdfd80523\"",
      "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkSecurityGroups/myNetworkSecurityGroup/defaultSecurityRules/AllowVnetOutBound",
      "name": "AllowVnetOutBound",
      "priority": 65000,
      "protocol": "*",
      "provisioningState": "Succeeded",
      "resourceGroup": "myResourceGroup",
      "sourceAddressPrefix": "VirtualNetwork",
      "sourcePortRange": "*"
    },
    {
      "access": "Allow",
      "description": "Allow outbound traffic from all VMs to Internet",
      "destinationAddressPrefix": "Internet",
      "destinationPortRange": "*",
      "direction": "Outbound",
      "etag": "W/\"3371b313-ea9f-4687-a336-a8ebdfd80523\"",
      "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkSecurityGroups/myNetworkSecurityGroup/defaultSecurityRules/AllowInternetOutBound",
      "name": "AllowInternetOutBound",
      "priority": 65001,
      "protocol": "*",
      "provisioningState": "Succeeded",
      "resourceGroup": "myResourceGroup",
      "sourceAddressPrefix": "*",
      "sourcePortRange": "*"
    },
    {
      "access": "Deny",
      "description": "Deny all outbound traffic",
      "destinationAddressPrefix": "*",
      "destinationPortRange": "*",
      "direction": "Outbound",
      "etag": "W/\"3371b313-ea9f-4687-a336-a8ebdfd80523\"",
      "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkSecurityGroups/myNetworkSecurityGroup/defaultSecurityRules/DenyAllOutBound",
      "name": "DenyAllOutBound",
      "priority": 65500,
      "protocol": "*",
      "provisioningState": "Succeeded",
      "resourceGroup": "myResourceGroup",
      "sourceAddressPrefix": "*",
      "sourcePortRange": "*"
    }
  ],
  "etag": "W/\"3371b313-ea9f-4687-a336-a8ebdfd80523\"",
  "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkSecurityGroups/myNetworkSecurityGroup",
  "location": "eastus",
  "name": "myNetworkSecurityGroup",
  "networkInterfaces": null,
  "provisioningState": "Succeeded",
  "resourceGroup": "myResourceGroup",
  "resourceGuid": "47a9964e-23a3-438a-a726-8d60ebbb1c3c",
  "securityRules": [
    {
      "access": "Allow",
      "description": null,
      "destinationAddressPrefix": "*",
      "destinationPortRange": "22",
      "direction": "Inbound",
      "etag": "W/\"9e344b60-0daa-40a6-84f9-0ebbe4a4b640\"",
      "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkSecurityGroups/myNetworkSecurityGroup/securityRules/myNetworkSecurityGroupRuleSSH",
      "name": "myNetworkSecurityGroupRuleSSH",
      "priority": 1000,
      "protocol": "Tcp",
      "provisioningState": "Succeeded",
      "resourceGroup": "myResourceGroup",
      "sourceAddressPrefix": "*",
      "sourcePortRange": "*"
    },
    {
      "access": "Allow",
      "description": null,
      "destinationAddressPrefix": "*",
      "destinationPortRange": "80",
      "direction": "Inbound",
      "etag": "W/\"9e344b60-0daa-40a6-84f9-0ebbe4a4b640\"",
      "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkSecurityGroups/myNetworkSecurityGroup/securityRules/myNetworkSecurityGroupRuleWeb",
      "name": "myNetworkSecurityGroupRuleWeb",
      "priority": 1001,
      "protocol": "Tcp",
      "provisioningState": "Succeeded",
      "resourceGroup": "myResourceGroup",
      "sourceAddressPrefix": "*",
      "sourcePortRange": "*"
    }
  ],
  "subnets": null,
  "tags": null,
  "type": "Microsoft.Network/networkSecurityGroups"
}
```

## <a name="create-a-virtual-nic"></a>仮想 NIC の作成
仮想ネットワーク インターフェイス カード (NIC) は使用時に規則を適用可能なため、プログラム上で使用できます。 [VM サイズ](../sizes.md)に応じて、複数の仮想 NIC を VM にアタッチできます。 次の [az network nic create](/cli/azure/network/nic) コマンドでは、*myNic* という名前の NIC を作成し、ネットワーク セキュリティ グループに関連付けます。 また、この仮想 NIC に *myPublicIP* というパブリック IP アドレスも関連付けています。

```azurecli
az network nic create \
    --resource-group myResourceGroup \
    --name myNic \
    --vnet-name myVnet \
    --subnet mySubnet \
    --public-ip-address myPublicIP \
    --network-security-group myNetworkSecurityGroup
```

出力:

```json
{
  "NewNIC": {
    "dnsSettings": {
      "appliedDnsServers": [],
      "dnsServers": [],
      "internalDnsNameLabel": null,
      "internalDomainNameSuffix": "brqlt10lvoxedgkeuomc4pm5tb.bx.internal.cloudapp.net",
      "internalFqdn": null
    },
    "enableAcceleratedNetworking": false,
    "enableIpForwarding": false,
    "etag": "W/\"04b5ab44-d8f4-422a-9541-e5ae7de8466d\"",
    "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkInterfaces/myNic",
    "ipConfigurations": [
      {
        "applicationGatewayBackendAddressPools": null,
        "etag": "W/\"04b5ab44-d8f4-422a-9541-e5ae7de8466d\"",
        "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkInterfaces/myNic/ipConfigurations/ipconfig1",
        "loadBalancerBackendAddressPools": null,
        "loadBalancerInboundNatRules": null,
        "name": "ipconfig1",
        "primary": true,
        "privateIpAddress": "192.168.1.4",
        "privateIpAddressVersion": "IPv4",
        "privateIpAllocationMethod": "Dynamic",
        "provisioningState": "Succeeded",
        "publicIpAddress": {
          "dnsSettings": null,
          "etag": null,
          "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/publicIPAddresses/myPublicIP",
          "idleTimeoutInMinutes": null,
          "ipAddress": null,
          "ipConfiguration": null,
          "location": null,
          "name": null,
          "provisioningState": null,
          "publicIpAddressVersion": null,
          "publicIpAllocationMethod": null,
          "resourceGroup": "myResourceGroup",
          "resourceGuid": null,
          "tags": null,
          "type": null
        },
        "resourceGroup": "myResourceGroup",
        "subnet": {
          "addressPrefix": null,
          "etag": null,
          "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/virtualNetworks/myVnet/subnets/mySubnet",
          "ipConfigurations": null,
          "name": null,
          "networkSecurityGroup": null,
          "provisioningState": null,
          "resourceGroup": "myResourceGroup",
          "resourceNavigationLinks": null,
          "routeTable": null
        }
      }
    ],
    "location": "eastus",
    "macAddress": null,
    "name": "myNic",
    "networkSecurityGroup": {
      "defaultSecurityRules": null,
      "etag": null,
      "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkSecurityGroups/myNetworkSecurityGroup",
      "location": null,
      "name": null,
      "networkInterfaces": null,
      "provisioningState": null,
      "resourceGroup": "myResourceGroup",
      "resourceGuid": null,
      "securityRules": null,
      "subnets": null,
      "tags": null,
      "type": null
    },
    "primary": null,
    "provisioningState": "Succeeded",
    "resourceGroup": "myResourceGroup",
    "resourceGuid": "b3dbaa0e-2cf2-43be-a814-5cc49fea3304",
    "tags": null,
    "type": "Microsoft.Network/networkInterfaces",
    "virtualMachine": null
  }
}
```


## <a name="create-an-availability-set"></a>可用性セットの作成
可用性セットにより、障害ドメインおよび更新ドメインに VM を分散できます。 今は作成する VM が 1 つだけの場合でも、将来簡単に拡張できるようにするために可用性セットを使用することをお勧めします。 

障害ドメインは共通の電源とネットワーク スイッチを使用する仮想マシンのグループを定義します。 既定で、可用性セット内に構成された仮想マシンは、最大 3 つの障害ドメイン間で分散されます。 これらの障害ドメインのいずれかでハードウェアの問題が生じた場合でも、アプリを実行中の VM の一部は影響を受けずにすみます。

更新ドメインは、仮想マシンと、同時に再起動できる基礎となる物理ハードウェアのグループを示しています。 計画済みメンテナンスの際には、更新ドメインの再起動は逐次的には行われませんが、一度に再起動される更新ドメインは 1 つだけになります。

Azure では、VM を可用性セットに配置すると障害ドメインと更新ドメイン間で自動的に分散されます。 詳細については、[VM の可用性管理](../availability.md)に関する記事を参照してください。

[az vm availability-set create](/cli/azure/vm/availability-set) で、VM の可用性セットを作成します。 次の例では、*myAvailabilitySet* という名前の可用性セットを作成します。

```azurecli
az vm availability-set create \
    --resource-group myResourceGroup \
    --name myAvailabilitySet
```

次の出力では、障害ドメインと更新ドメインが示されています。

```json
{
  "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Compute/availabilitySets/myAvailabilitySet",
  "location": "eastus",
  "managed": null,
  "name": "myAvailabilitySet",
  "platformFaultDomainCount": 2,
  "platformUpdateDomainCount": 5,
  "resourceGroup": "myResourceGroup",
  "sku": {
    "capacity": null,
    "managed": true,
    "tier": null
  },
  "statuses": null,
  "tags": {},
  "type": "Microsoft.Compute/availabilitySets",
  "virtualMachines": []
}
```


## <a name="create-a-vm"></a>VM の作成
これで、インターネットにアクセス可能な VM をサポートするためのネットワーク リソースが作成されました。 次に、VM を作成し SSH キーを使用して保護します。 この例では、最新の LTS に基づいて Ubuntu VM を作成します。 [Azure VM イメージの検索](cli-ps-findimage.md) に関する記事で説明されているように、[az vm image list](/cli/azure/vm/image) を使用すると他のイメージを検索できます。

認証に使用する SSH キーを指定します。 SSH 公開キーのペアが無い場合は、[自分で作成する](mac-create-ssh-keys.md)か、`--generate-ssh-keys` パラメーターを使用して自動で作成できます。 このパラメーターでは、キーのペアが既にある場合は `~/.ssh` にある既存のキーが使用されます。

[az vm create](/cli/azure/vm) コマンドですべてのリソースと情報を指定して、VM を作成します。 次の例では、*myVM* という名前の VM を作成します。

```azurecli
az vm create \
    --resource-group myResourceGroup \
    --name myVM \
    --location eastus \
    --availability-set myAvailabilitySet \
    --nics myNic \
    --image UbuntuLTS \
    --admin-username azureuser \
    --generate-ssh-keys
```

パブリック IP アドレスの作成時に指定した DNS エントリを使用すると、VM に SSH で接続できます。 この `fqdn` は、VM の作成時の出力で示されます。

```json
{
  "fqdns": "mypublicdns.eastus.cloudapp.azure.com",
  "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM",
  "location": "eastus",
  "macAddress": "00-0D-3A-13-71-C8",
  "powerState": "VM running",
  "privateIpAddress": "192.168.1.5",
  "publicIpAddress": "13.90.94.252",
  "resourceGroup": "myResourceGroup"
}
```

```bash
ssh azureuser@mypublicdns.eastus.cloudapp.azure.com
```

出力:

```bash
The authenticity of host 'mypublicdns.eastus.cloudapp.azure.com (13.90.94.252)' can't be established.
ECDSA key fingerprint is SHA256:SylINP80Um6XRTvWiFaNz+H+1jcrKB1IiNgCDDJRj6A.
Are you sure you want to continue connecting (yes/no)? yes
Warning: Permanently added 'mypublicdns.eastus.cloudapp.azure.com,13.90.94.252' (ECDSA) to the list of known hosts.
Welcome to Ubuntu 16.04.3 LTS (GNU/Linux 4.11.0-1016-azure x86_64)

 * Documentation:  https://help.ubuntu.com
 * Management:     https://landscape.canonical.com
 * Support:        https://ubuntu.com/advantage

  Get cloud support with Ubuntu Advantage Cloud Guest:
    https://www.ubuntu.com/business/services/cloud

0 packages can be updated.
0 updates are security updates.


The programs included with the Ubuntu system are free software;
the exact distribution terms for each program are described in the
individual files in /usr/share/doc/*/copyright.

Ubuntu comes with ABSOLUTELY NO WARRANTY, to the extent permitted by
applicable law.

To run a command as administrator (user "root"), use "sudo <command>".
See "man sudo_root" for details.

azureuser@myVM:~$
```

NGINX をインストールすると、VM へのトラフィック フローを確認できます。 NGNIX のインストールは次のコマンドで行います。

```bash
sudo apt-get install -y nginx
```

稼働中の既定の NGINX サイトを表示するには、Web ブラウザーを開いて使用中の FQDN を入力します。

![VM 上の既定 NGINX サイト](media/create-cli-complete/nginx.png)

## <a name="export-as-a-template"></a>テンプレートとしてのエクスポート
同じパラメーターを使用して追加の開発環境を作成する場合や、開発環境に合った運用環境を作成する場合はどのようにすべきでしょうか。 リソース マネージャーでは、環境に合ったすべてのパラメーターを定義する JSON テンプレートを使用します。 この JSON テンプレートを参照することで全体の環境を構築します。 [JSON テンプレートを手動で構築](../../azure-resource-manager/templates/template-syntax.md?toc=/azure/virtual-machines/linux/toc.json)できます。または、既存の環境をエクスポートして JSON テンプレートを作成することもできます。 [az group export](/cli/azure/group) を使って、リソース グループを次のようにエクスポートします。

```azurecli
az group export --name myResourceGroup > myResourceGroup.json
```

このコマンドで、現在の作業ディレクトリ内に `myResourceGroup.json` ファイルが作成されます。 このテンプレートから環境を作成する場合、リソース名をすべて入力するように求められます。 `az group export` コマンドに `--include-parameter-default-value` パラメーターを追加することで、テンプレート ファイルにこれらの名前を入力できます。 リソース名を指定する JSON テンプレートを編集するか、リソース名を指定する [parameters.json ファイルを作成](../../azure-resource-manager/templates/template-syntax.md?toc=/azure/virtual-machines/linux/toc.json) します。

テンプレートから環境を作成するには、次のように [az deployment group create](/cli/azure/deployment/group) を使います。

```azurecli
az deployment group create \
    --resource-group myNewResourceGroup \
    --template-file myResourceGroup.json
```

[テンプレートからデプロイする方法に関する詳細](../../azure-resource-manager/templates/deploy-cli.md?toc=/azure/virtual-machines/linux/toc.json)をご確認ください。 段階的な環境の更新、パラメーター ファイルの使用、単一の保存場所からテンプレートにアクセスする方法を確認してください。

## <a name="next-steps"></a>次のステップ
これで、複数のネットワーク コンポーネントと VM の操作を開始する準備が整いました。 ここで紹介した主要なコンポーネントを使用して、アプリケーションを構築するためにこのサンプル環境を使用できます。