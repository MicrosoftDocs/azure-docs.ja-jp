---
title: "複数の NIC を持つ VM の作成 - Azure CLI 2.0 | Microsoft Docs"
description: "Azure CLI 2.0 を使用して複数の NIC を持つ VM を作成する方法について説明します。"
services: virtual-network
documentationcenter: na
author: jimdial
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 8e906a4b-8583-4a97-9416-ee34cfa09a98
ms.service: virtual-network
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/02/2016
ms.author: jdial
ms.custom: H1Hack27Feb2017
translationtype: Human Translation
ms.sourcegitcommit: 6d749e5182fbab04adc32521303095dab199d129
ms.openlocfilehash: 4f5eaf5f6ba56709b69d97c1f646f71396fd031b
ms.lasthandoff: 03/22/2017


---
# <a name="create-a-vm-with-multiple-nics-using-the-azure-cli-20"></a>Azure CLI 2.0 を使用して複数の NIC を持つ VM を作成する

[!INCLUDE [virtual-network-deploy-multinic-arm-selectors-include.md](../../includes/virtual-network-deploy-multinic-arm-selectors-include.md)]

[!INCLUDE [virtual-network-deploy-multinic-intro-include.md](../../includes/virtual-network-deploy-multinic-intro-include.md)]

> [!NOTE]
> Azure には、リソースの作成と操作に関して、[Resource Manager とクラシックの](../resource-manager-deployment-model.md) 2 種類のデプロイメント モデルがあります。  この記事では、Resource Manager デプロイメント モデルの使用方法について取り上げていますが、最新のデプロイでは、[クラシック デプロイメント モデル](virtual-network-deploy-multinic-classic-cli.md)ではなくこのモデルをお勧めします。
>

## <a name="create"></a>VM の作成

このタスクは、Azure CLI 2.0 (この記事) または [Azure CLI 1.0](virtual-network-deploy-multinic-cli-nodejs.md) を使用して行うことができます。 以下の手順で "" で囲まれている変数値は、シナリオの設定でリソースを作成するためのものです。 これらの値は、お使いの環境に合わせて変更してください。

1. まだインストールしていない場合は、[Azure CLI 2.0](/cli/azure/install-az-cli2) をインストールします。
2. 「[Linux VM 用の SSH 公開キーと秘密キーのペアの作成](../virtual-machines/virtual-machines-linux-mac-create-ssh-keys.md?toc=%2fazure%2fvirtual-network%2ftoc.json)」の手順を実行して、Linux VM 用の SSH 公開キーと秘密キーのペアを作成します。
3. コマンド シェルで、`az login` コマンドを使用してログインします。
4. 以下のスクリプトを Linux または Mac コンピューターで実行して、VM を作成します。 このスクリプトは、リソース グループ、サブネットを 2 つ持つ 1 つの仮想ネットワーク (VNet)、2 つの NIC、これら 2 つの NIC が関連付けられた VM を作成します。 片方の NIC は 1 つのサブネットに接続し、静的なパブリック IP アドレスとプライベート IP アドレスを割り当てます。 もう片方の NIC は別のサブネットに接続し、静的なプライベート IP アドレスを割り当てますがプライベート IP アドレスは割り当てません。 NIC、パブリック IP アドレス、仮想ネットワーク、および VM リソースはすべて、同一の場所およびサブスクリプション内に存在する必要があります。 すべてのリソースが同一のリソース グループ内に存在する必要はありませんが、以下のスクリプトでは同一グループ内に配置しています。

```bash
#!/bin/sh

RgName="Multi-NIC-VM"
Location="westus"

# Create a resource group.
az group create \
--name $RgName \
--location $Location

# The address is assigned to the resource from a pool of IP adresses unique to each Azure region. 
# Download and view the file from https://www.microsoft.com/en-us/download/details.aspx?id=41653 that lists
# the ranges for each region.

PipName="PIP-WEB"
az network public-ip create \
--name $PipName \
--resource-group $RgName \
--location $Location \
--allocation-method Static

# Create a virtual network with one subnet

VnetName="VNet1"
VnetPrefix="10.0.0.0/16"
VnetSubnet1Name="Front-End"
VnetSubnet1Prefix="10.0.0.0/24"
az network vnet create \
--name $VnetName \
--resource-group $RgName \
--location $Location \
--address-prefix $VnetPrefix \
--subnet-name $VnetSubnet1Name \
--subnet-prefix $VnetSubnet1Prefix

# Create a second subnet within the VNet

VnetSubnet2Name="Back-end"
VnetSubnet2Prefix="10.0.1.0/24"
az network vnet subnet create \
--vnet-name $VnetName \
--resource-group $RgName \
--name $VnetSubnet2Name \
--address-prefix $VnetSubnet2Prefix

# Create a network interface connected to one of the subnets. The NIC is assigned a single dynamic private and
# public IP address by default, but you can instead, assign static addresses, or no public IP address at all.
# You can also assign multiple private or public IP addresses to each NIC. To learn more about IP addressing
# options for NICs, enter the az network nic create -h command.

Nic1Name="NIC-FE"
PrivateIpAddress1="10.0.0.5"

az network nic create \
--name $Nic1Name \
--resource-group $RgName \
--location $Location \
--subnet $VnetSubnet1Name \
--vnet-name $VnetName \
--private-ip-address $PrivateIpAddress1 \
--public-ip-address $PipName

# Create a second network interface and connect it to the other subnet. Though multiple NICs attached to the same
# VM can be connected to different subnets, the subnets must all be within the same VNet. Add additional NICs as necessary.

Nic2Name="NIC-BE"
PrivateIpAddress2="10.0.1.5"

az network nic create \
--name $Nic2Name \
--resource-group $RgName \
--location $Location \
--subnet $VnetSubnet2Name \
--vnet-name $VnetName \
--private-ip-address $PrivateIpAddress2

# Create a VM and attach the two NICs.

VmName="WEB"

# Replace the value for the following **VmSize** variable with a value from the
# https://docs.microsoft.com/azure/virtual-machines/virtual-machines-linux-sizes article. Not all VM sizes support
# more than one NIC, so be sure to select a VM size that supports the number of NICs you want to attach to the VM.
# You must create the VM with at least two NICs if you want to add more after VM creation. If you create a VM with
# only one NIC, you can't add additional NICs to the VM after VM creation, regardless of how many NICs the VM supports.
# The VM size specified in the following variable supports two NICs.

VmSize="Standard_DS2"

# Replace the value for the OsImage variable value with a value for *urn* from the output returned by entering the
# az vm image list command.

OsImage="credativ:Debian:8:latest"

Username="adminuser"

# Replace the following value with the path to your public key file.

SshKeyValue="~/.ssh/id_rsa.pub"

# Before executing the following command, add variable names of additional NICs you may have added to the script that
# you want to attach to the VM. If creating a Windows VM, remove the **ssh-key-value** line and you'll be prompted for
# the password you want to configure for the VM.

az vm create \
--name $VmName \
--resource-group $RgName \
--image $OsImage \
--location $Location \
--size $VmSize \
--nics $Nic1Name $Nic2Name \
--admin-username $Username \
--ssh-key-value $SshKeyValue
```

このスクリプトでは、2 つの NIC を持つ 1 つの VM に加えて次のものも作成します。
- 1 つの Premium 管理ディスク (既定)。ただし、作成するディスクの種類には別のオプションもあります。 詳細については、「[Azure CLI 2.0 を使用して Linux VM を作成する](../virtual-machines/virtual-machines-linux-quick-create-cli.md?toc=%2fazure%2fvirtual-network%2ftoc.json)」を参照してください。
- 2 つのサブネットと 1 つのパブリック IP アドレスを持つ仮想ネットワーク。 代わりに、*既存の*仮想ネットワーク、サブネット、NIC、またはパブリック IP アドレス リソースを使用することもできます。 リソースを別途作成するのではなく、既存のネットワーク リソースを使用する場合は、「`az vm create -h`」と入力します。

## <a name = "validate"></a>VM の作成結果と NIC の検証

1. `az resource list --resouce-group Multi-NIC-VM --output table` コマンドを入力して、スクリプトにより作成されたリソースの一覧を確認します。 出力では、6 つのリソース (2 つの NIC、1 つのディスク、1 つのパブリック IP アドレス、1 つの仮想ネットワーク、1 つの仮想マシン) が返されます。
2. `az network public-ip show --name PIP-WEB --resource-group Multi-NIC-VM --output table` コマンドを入力します。 返された出力で、**IpAddress** の値を確認するとともに、**PublicIpAllocationMethod** の値が *Static* であることを確認してください。
3. 以下のコマンドを実行する前に、<> を削除し、*Username* をスクリプトの **Username** 変数で使用した名前に、*ipAddress* を前の手順の **ipAddress** に置き換えます。 次のコマンドを実行して VM に接続します: `ssh -i ~/.ssh/azure_id_rsa <Username>@<ipAddress>`。 
4. VM への接続後、`sudo ifconfig` コマンドを実行して、*eth0* インターフェイスおよび *eth1* インターフェイスを確認します。 各 NIC には、Azure の DHCP サーバーにより、スクリプトで指定した静的なプライベート IP アドレスが割り当てられています。 NIC に割り当てられた IP アドレスと MAC アドレスは、VM が削除されるまで変わることはありません。 オペレーティング システムでの IP アドレス指定を変更するとコンピューターに接続できなくなる可能性があるため、IP アドレス指定は変更しないことをお勧めします。 パブリック IP アドレスは、Azure インフラストラクチャによりプライベート IP アドレスとの変換が行われるネットワーク アドレスであるため、オペレーティング システムでは表示されません。

## <a name= "clean-up"></a>VM と関連リソースの削除

この演習で作成したリソースを運用環境で使用しない場合は、削除することをお勧めします。 VM、パブリック IP アドレス、およびディスク リソースについては、プロビジョニングされている間は料金が発生します。 この演習で作成したリソースを削除するには、次の手順を実行します。
1. リソース グループ内にあるリソースを確認するには、`az resource list --resource-group Multi-NIC-VM` コマンドを実行します。
2. この記事のスクリプトにより作成されたもの以外のリソースがリソース グループ内に存在しないことを確認してください。 
3. この演習で作成されたすべてのリソースを削除するには、`az group delete --name Multi-NIC-VM` コマンドを実行します。 コマンドによって、リソース グループと含まれるすべてのリソースが削除されます。

## <a name="next-steps"></a>次のステップ

この記事で作成した VM では、すべてのネットワーク トラフィックを送受信できます。 NSG 内に受信規則および送信規則を定義して、各ネットワーク インターフェイスかサブネット、またはその両方で送受信可能なトラフィックを制限できます。 NSG の詳細については、[NSG の概要](virtual-networks-nsg.md)に関する記事を参照してください。
