---
title: Azure CLI を使用して複数の IP アドレスを持つ VM
titlesuffix: Azure Virtual Network
description: Azure コマンド ライン インターフェイス (CLI) を使用して仮想マシンに複数の IP アドレスを割り当てる方法を説明します。
services: virtual-network
documentationcenter: na
author: asudbring
manager: KumudD
ms.service: virtual-network
ms.subservice: ip-services
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/17/2016
ms.author: allensu
ms.openlocfilehash: 0b5e8f28be9960a2bb4e1406a293ab5e03962681
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "98680571"
---
# <a name="assign-multiple-ip-addresses-to-virtual-machines-using-the-azure-cli"></a>Azure CLI を使用して仮想マシンに複数の IP アドレスを割り当てる

[!INCLUDE [virtual-network-multiple-ip-addresses-intro.md](../../includes/virtual-network-multiple-ip-addresses-intro.md)]

この記事では、Azure CLI を使用して Azure Resource Manager デプロイ モデルで仮想マシン (VM) を作成する方法について説明します。 クラシック デプロイ モデルで作成されたリソースには、複数の IP アドレスを割り当てることはできません。 Azure のデプロイ モデルの詳細については、[デプロイ モデルの概要](../azure-resource-manager/management/deployment-models.md)に関する記事をご覧ください。

[!INCLUDE [virtual-network-multiple-ip-addresses-scenario.md](../../includes/virtual-network-multiple-ip-addresses-scenario.md)]

## <a name="create-a-vm-with-multiple-ip-addresses"></a><a name = "create"></a>複数の IP アドレスを持つ VM を作成する

以下の手順は、シナリオの説明に従って複数の IP アドレスを持つ仮想マシンを作成する方法の例を示しています。 "" で囲まれた変数値と IP アドレスの種類は、実際の実装に合わせて変更してください。 

1. まだインストールしていない場合は、[Azure CLI](/cli/azure/install-azure-cli) をインストールします。
2. 「[Linux VM 用の SSH 公開キーと秘密キーのペアの作成](../virtual-machines/linux/mac-create-ssh-keys.md?toc=%2fazure%2fvirtual-network%2ftoc.json)」の手順を実行して、Linux VM 用の SSH 公開キーと秘密キーのペアを作成します。
3. コマンド シェルで、`az login` コマンドを使用してログインし、使用中のサブスクリプションを選択します。
4. 以下のスクリプトを Linux または Mac コンピューターで実行して、VM を作成します。 このスクリプトは、リソース グループ、1 つの仮想ネットワーク (VNet)、3 つの IP 構成を持つ 1 つの NIC、この NIC が関連付けられた VM を作成します。 NIC、パブリック IP アドレス、仮想ネットワーク、および VM リソースはすべて、同一の場所およびサブスクリプション内に存在する必要があります。 すべてのリソースが同一のリソース グループ内に存在する必要はありませんが、以下のスクリプトでは同一グループ内に配置しています。

```azurecli
    
#!/bin/sh
    
RgName="myResourceGroup"
Location="westcentralus"
az group create --name $RgName --location $Location
    
# Create a public IP address resource with a static IP address using the `--allocation-method Static` option. If you
# do not specify this option, the address is allocated dynamically. The address is assigned to the resource from a pool
# of IP addresses unique to each Azure region. Download and view the file from
# https://www.microsoft.com/en-us/download/details.aspx?id=41653 that lists the ranges for each region.

PipName="myPublicIP"

# This name must be unique within an Azure location.
DnsName="myDNSName"

az network public-ip create \
--name $PipName \
--resource-group $RgName \
--location $Location \
--dns-name $DnsName\
--allocation-method Static

# Create a virtual network with one subnet

VnetName="myVnet"
VnetPrefix="10.0.0.0/16"
VnetSubnetName="mySubnet"
VnetSubnetPrefix="10.0.0.0/24"

az network vnet create \
--name $VnetName \
--resource-group $RgName \
--location $Location \
--address-prefix $VnetPrefix \
--subnet-name $VnetSubnetName \
--subnet-prefix $VnetSubnetPrefix

# Create a network interface connected to the subnet and associate the public IP address to it. Azure will create the
# first IP configuration with a static private IP address and will associate the public IP address resource to it.

NicName="MyNic1"
az network nic create \
--name $NicName \
--resource-group $RgName \
--location $Location \
--subnet $VnetSubnet1Name \
--private-ip-address 10.0.0.4
--vnet-name $VnetName \
--public-ip-address $PipName
    
# Create a second public IP address, a second IP configuration, and associate it to the NIC. This configuration has a
# static public IP address and a static private IP address.

az network public-ip create \
--resource-group $RgName \
--location $Location \
--name myPublicIP2 \
--dns-name mypublicdns2 \
--allocation-method Static

az network nic ip-config create \
--resource-group $RgName \
--nic-name $NicName \
--name IPConfig-2 \
--private-ip-address 10.0.0.5 \
--public-ip-name myPublicIP2

# Create a third IP configuration, and associate it to the NIC. This configuration has  static private IP address and   # no public IP address.

az network nic ip-config create \
--resource-group $RgName \
--nic-name $NicName \
--private-ip-address 10.0.0.6 \
--name IPConfig-3

# Note: Though this article assigns all IP configurations to a single NIC, you can also assign multiple IP configurations
# to any NIC in a VM. To learn how to create a VM with multiple NICs, read the Create a VM with multiple NICs 
# article: https://docs.microsoft.com/azure/virtual-network/virtual-network-deploy-multinic-arm-cli.

# Create a VM and attach the NIC.

VmName="myVm"

# Replace the value for the following **VmSize** variable with a value from the
# https://docs.microsoft.com/azure/virtual-machines/sizes article. The script fails if the VM size
# is not supported in the location you select. Run the `azure vm sizes --location eastcentralus` command to get a full list
# of VMs in US West Central, for example.

VmSize="Standard_DS1"

# Replace the value for the OsImage variable value with a value for *urn* from the output returned by entering the
# `az vm image list` command.

OsImage="credativ:Debian:8:latest"

Username="adminuser"

# Replace the following value with the path to your public key file. If you're creating a Windows VM, remove the following
# line and you'll be prompted for the password you want to configure for the VM.

SshKeyValue="~/.ssh/id_rsa.pub"

az vm create \
--name $VmName \
--resource-group $RgName \
--image $OsImage \
--location $Location \
--size $VmSize \
--nics $NicName \
--admin-username $Username \
--ssh-key-value $SshKeyValue
```

このスクリプトでは、3 つの IP 構成が設定された NIC を備える VM に加えて次のものも作成されます。

- 1 つの Premium マネージド ディスク (既定)。ただし、作成するディスクの種類には別のオプションもあります。 詳細については、[Azure CLI を使用した Linux VM の作成](../virtual-machines/linux/quick-create-cli.md?toc=%2fazure%2fvirtual-network%2ftoc.json)に関する記事をお読みください。
- 1 つのサブネットと 2 つのパブリック IP アドレスを持つ仮想ネットワーク。 代わりに、*既存の* 仮想ネットワーク、サブネット、NIC、またはパブリック IP アドレス リソースを使用することもできます。 リソースを別途作成するのではなく、既存のネットワーク リソースを使用する場合は、「`az vm create -h`」と入力します。

パブリック IP アドレスには、わずかな費用がかかります。 IP アドレスの料金の詳細については、「 [IP アドレスの料金](https://azure.microsoft.com/pricing/details/ip-addresses) 」ページをご覧ください。 サブスクリプション内で使用できるパブリック IP アドレスの数には制限があります。 制限の詳細については、[Azure の制限](../azure-resource-manager/management/azure-subscription-service-limits.md#networking-limits)に関する記事をご覧ください。

VM の作成後、「`az network nic show --name MyNic1 --resource-group myResourceGroup`」コマンドを入力して NIC の構成を確認します。 NIC に関連付けられている IP 構成のリストを表示するには、「`az network nic ip-config list --nic-name MyNic1 --resource-group myResourceGroup --output table`」と入力します。

この記事の「[VM オペレーティング システムに IP アドレスを追加する](#os-config)」に記載されたご使用のオペレーティング システム用の手順に従って、プライベート IP アドレスを VM オペレーティング システムに追加します。

## <a name="add-ip-addresses-to-a-vm"></a><a name="add"></a>VM に IP アドレスを追加する

プライベートおよびパブリック IP アドレスを既存の Azure ネットワーク インターフェイスに追加するには、次の手順を実行します。 各例はこの記事で説明する[シナリオ](#scenario)に基づいています。

1. コマンド シェルを開き、1 つのセッションでこのセクションの残りの手順を実行します。 Azure CLI のインストールと構成をまだ行っていない場合は、[Azure CLI のインストール](/cli/azure/install-az-cli2?toc=%2fazure%2fvirtual-network%2ftoc.json)に関する記事の手順を完了し、`az-login` コマンドで Azure アカウントにログインします。

2. 要件に基づいて、以下のいずれかのセクションの手順を実行します。

    **プライベート IP アドレスを追加する**
    
    NIC にプライベート IP アドレスを追加するには、次のコマンドを使用して IP 構成を作成する必要があります。 静的 IP アドレスは、サブネットの未使用のアドレスである必要があります。

    ```azurecli
    az network nic ip-config create \
    --resource-group myResourceGroup \
    --nic-name myNic1 \
    --private-ip-address 10.0.0.7 \
    --name IPConfig-4
    ```
    
    一意の構成名とプライベート IP アドレス (静的 IP アドレスを使用する構成の場合) を使用して、必要な数だけ構成を作成します。

    **パブリック IP アドレスを追加する**
    
    パブリック IP アドレスを追加するには、新しい IP 構成または既存の IP 構成にパブリック IP アドレスを関連付けます。 必要に応じて、以下のいずれかのセクションの手順を実行します。

    パブリック IP アドレスには、わずかな費用がかかります。 IP アドレスの料金の詳細については、「 [IP アドレスの料金](https://azure.microsoft.com/pricing/details/ip-addresses) 」ページをご覧ください。 サブスクリプション内で使用できるパブリック IP アドレスの数には制限があります。 制限の詳細については、[Azure の制限](../azure-resource-manager/management/azure-subscription-service-limits.md#networking-limits)に関する記事をご覧ください。

    - **リソースを新しい IP 構成に関連付ける**
    
        すべての IP 構成にプライベート IP アドレスが必要であるため、パブリック IP アドレスを新しい IP 構成に追加するときは、必ずプライベート IP アドレスも追加する必要があります。 既存のパブリック IP アドレス リソースを追加することも、新しいリソースを作成することもできます。 新しいパブリック IP アドレス リソースを作成するには、次のコマンドを入力します。
    
        ```azurecli
        az network public-ip create \
        --resource-group myResourceGroup \
        --location westcentralus \
        --name myPublicIP3 \
        --dns-name mypublicdns3
        ```

        静的プライベート IP アドレスと、関連付けられた *myPublicIP3* パブリック IP アドレス リソースで新しい IP 構成を作成するには、次のコマンドを入力します。

        ```azurecli
        az network nic ip-config create \
        --resource-group myResourceGroup \
        --nic-name myNic1 \
        --name IPConfig-5 \
        --private-ip-address 10.0.0.8
        --public-ip-address myPublicIP3
        ```

    - **リソースを既存の IP 構成に関連付ける** パブリック IP アドレス リソースは、このリソースがまだ関連付けられていない IP 構成にのみ関連付けることができます。 IP 構成にパブリック IP アドレスが関連付けられているかどうかを確認するには、次のコマンドを入力します。

        ```azurecli
        az network nic ip-config list \
        --resource-group myResourceGroup \
        --nic-name myNic1 \
        --query "[?provisioningState=='Succeeded'].{ Name: name, PublicIpAddressId: publicIpAddress.id }" --output table
        ```

        返される出力:
    
        ```output
        Name        PublicIpAddressId
        
        ipconfig1   /subscriptions/[Id]/resourceGroups/myResourceGroup/providers/Microsoft.Network/publicIPAddresses/myPublicIP1
        IPConfig-2  /subscriptions/[Id]/resourceGroups/myResourceGroup/providers/Microsoft.Network/publicIPAddresses/myPublicIP2
        IPConfig-3
        ```

        出力の *IpConfig-3* の **PublicIpAddressId** 列が空白であるため、現在、この構成にはパブリック IP アドレス リソースは関連付けられていません。 IpConfig-3 に既存のパブリック IP アドレス リソースを追加することも、次のコマンドを入力して新しいリソースを作成することもできます。

        ```azurecli
        az network public-ip create \
        --resource-group  myResourceGroup
        --location westcentralus \
        --name myPublicIP3 \
        --dns-name mypublicdns3 \
        --allocation-method Static
        ```
    
        パブリック IP アドレス リソースを、*IPConfig-3* という名前の既存の IP 構成に関連付けるには、次のコマンドを入力します。
    
        ```azurecli
        az network nic ip-config update \
        --resource-group myResourceGroup \
        --nic-name myNic1 \
        --name IPConfig-3 \
        --public-ip myPublicIP3
        ```

3. 次のコマンドを入力して、NIC に割り当てられたプライベート IP アドレスとパブリック IP アドレス リソース ID を表示します。

    ```azurecli
    az network nic ip-config list \
    --resource-group myResourceGroup \
    --nic-name myNic1 \
    --query "[?provisioningState=='Succeeded'].{ Name: name, PrivateIpAddress: privateIpAddress, PrivateIpAllocationMethod: privateIpAllocationMethod, PublicIpAddressId: publicIpAddress.id }" --output table
    ```

    返される出力: <br>

    ```output
    Name        PrivateIpAddress    PrivateIpAllocationMethod   PublicIpAddressId
    
    ipconfig1   10.0.0.4            Static                      /subscriptions/[Id]/resourceGroups/myResourceGroup/providers/Microsoft.Network/publicIPAddresses/myPublicIP1
    IPConfig-2  10.0.0.5            Static                      /subscriptions/[Id]/resourceGroups/myResourceGroup/providers/Microsoft.Network/publicIPAddresses/myPublicIP2
    IPConfig-3  10.0.0.6            Static                      /subscriptions/[Id]/resourceGroups/myResourceGroup/providers/Microsoft.Network/publicIPAddresses/myPublicIP3
    ```
    
4. この記事の「[VM オペレーティング システムに IP アドレスを追加する](#os-config)」の手順に従って、NIC に追加したプライベート IP アドレスを、VM オペレーティング システムに追加します。 オペレーティング システムにパブリック IP アドレスは追加しないでください。

[!INCLUDE [virtual-network-multiple-ip-addresses-os-config.md](../../includes/virtual-network-multiple-ip-addresses-os-config.md)]