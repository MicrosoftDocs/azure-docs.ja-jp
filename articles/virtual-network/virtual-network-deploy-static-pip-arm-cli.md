---
title: "静的パブリック IP アドレスを持つ VM を作成する - Azure CLI 2.0 | Microsoft Docs"
description: "Azure コマンド ライン インターフェイス (CLI) 2.0 を使用して、静的パブリック IP アドレスを持つ VM を作成する方法について説明します。"
services: virtual-network
documentationcenter: na
author: jimdial
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 55bc21b0-2a45-4943-a5e7-8d785d0d015c
ms.service: virtual-network
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/15/2016
ms.author: jdial
ms.custom: H1Hack27Feb2017
translationtype: Human Translation
ms.sourcegitcommit: 6d749e5182fbab04adc32521303095dab199d129
ms.openlocfilehash: 2c2442e6e0e1617dada3ba277e2478c8daa32c67
ms.lasthandoff: 03/22/2017


---
# <a name="create-a-vm-with-a-static-public-ip-address-using-the-azure-cli-20"></a>Azure CLI 2.0 を使用して静的パブリック IP アドレスを持つ VM を作成する

> [!div class="op_single_selector"]
> * [Azure Portal](virtual-network-deploy-static-pip-arm-portal.md)
> * [PowerShell](virtual-network-deploy-static-pip-arm-ps.md)
> * [Azure CLI 2.0](virtual-network-deploy-static-pip-arm-cli.md)
> * [Azure CLI 1.0](virtual-network-deploy-static-pip-cli-nodejs.md)
> * [テンプレート](virtual-network-deploy-static-pip-arm-template.md)
> * [PowerShell (クラシック)](virtual-networks-reserved-public-ip.md)

[!INCLUDE [virtual-network-deploy-static-pip-intro-include.md](../../includes/virtual-network-deploy-static-pip-intro-include.md)]

Azure には、リソースの作成と操作に関して、[Resource Manager とクラシック](../resource-manager-deployment-model.md?toc=%2fazure%2fvirtual-network%2ftoc.json)の 2 種類のデプロイメント モデルがあります。 この記事では、Resource Manager デプロイメント モデルの使用方法について取り上げていますが、最新のデプロイでは、クラシック デプロイメント モデルではなくこのモデルをお勧めします。

[!INCLUDE [virtual-network-deploy-static-pip-scenario-include.md](../../includes/virtual-network-deploy-static-pip-scenario-include.md)]

## <a name = "create"></a>VM の作成

このタスクは、Azure CLI 2.0 (この記事) または [Azure CLI 1.0](virtual-network-deploy-static-pip-cli-nodejs.md) を使用して行うことができます。 以下の手順で "" で囲まれている変数値は、シナリオの設定でリソースを作成するためのものです。 これらの値は、お使いの環境に合わせて変更してください。

1. まだインストールしていない場合は、[Azure CLI 2.0](/cli/azure/install-az-cli2) をインストールします。
2. 「[Linux VM 用の SSH 公開キーと秘密キーのペアの作成](../virtual-machines/virtual-machines-linux-mac-create-ssh-keys.md?toc=%2fazure%2fvirtual-network%2ftoc.json)」の手順を実行して、Linux VM 用の SSH 公開キーと秘密キーのペアを作成します。
3. コマンド シェルで、`az login` コマンドを使用してログインします。
4. 以下のスクリプトを Linux または Mac コンピューターで実行して、VM を作成します。 Azure パブリック IP アドレス、仮想ネットワーク、ネットワーク インターフェイス、VM リソースはすべて、同一の場所内に存在する必要があります。 すべてのリソースが同一のリソース グループ内に存在する必要はありませんが、以下のスクリプトでは同一グループ内に配置しています。

```bash
RgName="IaaSStory"
Location="westus"

# Create a resource group.

az group create \
--name $RgName \
--location $Location

# Create a public IP address resource with a static IP address using the --allocation-method Static option.
# If you do not specify this option, the address is allocated dynamically. The address is assigned to the
# resource from a pool of IP adresses unique to each Azure region. The DnsName must be unique within the
# Azure location it's created in. Download and view the file from https://www.microsoft.com/en-us/download/details.aspx?id=41653#
# that lists the ranges for each region.

PipName="PIPWEB1"
DnsName="iaasstoryws1"
az network public-ip create \
--name $PipName \
--resource-group $RgName \
--location $Location \
--allocation-method Static \
--dns-name $DnsName

# Create a virtual network with one subnet

VnetName="TestVNet"
VnetPrefix="192.168.0.0/16"
SubnetName="FrontEnd"
SubnetPrefix="192.168.1.0/24"
az network vnet create \
--name $VnetName \
--resource-group $RgName \
--location $Location \
--address-prefix $VnetPrefix \
--subnet-name $SubnetName \
--subnet-prefix $SubnetPrefix

# Create a network interface connected to the VNet with a static private IP address and associate the public IP address
# resource to the NIC.

NicName="NICWEB1"
PrivateIpAddress="192.168.1.101"
az network nic create \
--name $NicName \
--resource-group $RgName \
--location $Location \
--subnet $SubnetName \
--vnet-name $VnetName \
--private-ip-address $PrivateIpAddress \
--public-ip-address $PipName

# Create a new VM with the NIC

VmName="WEB1"

# Replace the value for the VmSize variable with a value from the
# https://docs.microsoft.com/azure/virtual-machines/virtual-machines-linux-sizes article.
VmSize="Standard_DS1"

# Replace the value for the OsImage variable with a value for *urn* from the output returned by entering
# the `az vm image list` command. 

OsImage="credativ:Debian:8:latest"
Username='adminuser'

# Replace the following value with the path to your public key file.
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
# If creating a Windows VM, remove the previous line and you'll be prompted for the password you want to configure for the VM.
```

このスクリプトでは、1 つの VM に加えて次のものも作成します。
- 1 つの Premium 管理ディスク (既定)。ただし、作成するディスクの種類には別のオプションもあります。 詳細については、「[Azure CLI 2.0 を使用して Linux VM を作成する](../virtual-machines/virtual-machines-linux-quick-create-cli.md?toc=%2fazure%2fvirtual-network%2ftoc.json)」を参照してください。
- 仮想ネットワーク、サブネット、NIC、パブリック IP アドレス リソース。 代わりに、*既存の*仮想ネットワーク、サブネット、NIC、またはパブリック IP アドレス リソースを使用することもできます。 リソースを別途作成するのではなく、既存のネットワーク リソースを使用する場合は、「`az vm create -h`」と入力します。

## <a name = "validate"></a>VM の作成とパブリック IP アドレスを検証する

1. `az resource list --resouce-group IaaSStory --output table` コマンドを入力して、スクリプトにより作成されたリソースの一覧を確認します。 出力では、5 つのリソース (ネットワーク インターフェイス、ディスク、パブリック IP アドレス、仮想ネットワーク、仮想マシン) があります。
2. `az network public-ip show --name PIPWEB1 --resource-group IaaSStory --output table` コマンドを入力します。 返された出力で、**IpAddress** の値を確認するとともに、**PublicIpAllocationMethod** の値が *Static* であることを確認してください。
3. 以下のコマンドを実行する前に、<> を削除し、*Username* をスクリプトの **Username** 変数で使用した名前に、*ipAddress* を前の手順の **ipAddress** に置き換えます。 次のコマンドを実行して VM に接続します: `ssh -i ~/.ssh/azure_id_rsa <Username>@<ipAddress>`。 

## <a name= "clean-up"></a>VM と関連リソースの削除

この演習で作成したリソースを運用環境で使用しない場合は、削除することをお勧めします。 VM、パブリック IP アドレス、およびディスク リソースについては、プロビジョニングされている間は料金が発生します。 この演習で作成したリソースを削除するには、次の手順を実行します。

1. リソース グループ内にあるリソースを確認するには、`az resource list --resource-group IaaSStory` コマンドを実行します。
2. この記事のスクリプトにより作成されたもの以外のリソースがリソース グループ内に存在しないことを確認してください。 
3. この演習で作成されたすべてのリソースを削除するには、`az group delete -n IaaSStory` コマンドを実行します。 コマンドによって、リソース グループと含まれるすべてのリソースが削除されます。

## <a name="next-steps"></a>次のステップ

この記事で作成した VM では、すべてのネットワーク トラフィックを送受信できます。 NSG 内に受信規則と送信規則を定義して、ネットワーク インターフェイスかサブネット、またはその両方で送受信可能なトラフィックを制限できます。 NSG の詳細については、[NSG の概要](virtual-networks-nsg.md)に関する記事を参照してください。
