---
title: "Azure CLI 2.0 による既存のネットワークへの Linux VM のデプロイ | Microsoft Docs"
description: "Azure CLI 2.0 を使用して Linux 仮想マシンを既存の仮想ネットワークにデプロイする方法を説明します。"
services: virtual-machines-linux
documentationcenter: virtual-machines
author: iainfoulds
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machines-linux
ms.workload: infrastructure
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 01/31/2017
ms.author: iainfou
translationtype: Human Translation
ms.sourcegitcommit: 67d4fee2fc59651903d4c02d1fce84c7b81e5da1
ms.openlocfilehash: c56ad780a1d67102d23c84a18c712ae48cec1eb6
ms.lasthandoff: 02/27/2017


---

# <a name="deploy-a-linux-vm-into-an-existing-virtual-network"></a>既存の仮想ネットワークに Linux VM をデプロイする

この記事では、Azure CLI 2.0 を使用して仮想マシン (VM) を既存の仮想ネットワークにデプロイする方法を説明します。 要件は次のとおりです。

- [Azure アカウント](https://azure.microsoft.com/pricing/free-trial/)
- [SSH パブリック キー ファイルおよびプライベート キー ファイル](virtual-machines-linux-mac-create-ssh-keys.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

これらの手順は、[Azure CLI 1.0](virtual-machines-linux-deploy-linux-vm-into-existing-vnet-using-cli-nodejs.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) を使用して実行することもできます。


## <a name="quick-commands"></a>クイック コマンド
タスクをすばやく実行する必要がある場合のために、次のセクションでは、必要なコマンドの詳細について説明します。 詳細な情報と各手順のコンテキストが、ドキュメントの残りの部分に記載されています。[ここからお読みください](#detailed-walkthrough)。

このカスタム環境を作成するには、最新の [Azure CLI 2.0](/cli/azure/install-az-cli2) をインストールし、[az login](/cli/azure/#login) を使用して Azure アカウントにログインしている必要があります。

次の例では、パラメーター名を独自の値を置き換えます。 `myResourceGroup`、`myVnet`、`myVM` などは、例として使われているパラメーター名です。

**前提条件:** Azure リソース グループ、仮想ネットワークおよびサブネット、SSH インバウンドが設定されたネットワーク セキュリティ グループ、仮想ネットワーク インターフェイス カード。

### <a name="deploy-the-vm-into-the-virtual-network-infrastructure"></a>仮想ネットワーク インフラストラクチャに VM をデプロイ

```azurecli
az vm create \
    --resource-group myResourceGroup \
    --name myVM \
    --image Debian \
    --admin-username azureuser \
    --ssh-key-value ~/.ssh/id_rsa.pub \
    --nics myNic
```

## <a name="detailed-walkthrough"></a>詳細なチュートリアル

仮想ネットワークや ネットワーク セキュリティ グループなどの Azure 資産は、ほとんどデプロイされることのない有効期間の長い静的リソースにすることをお勧めします。 いったん仮想ネットワークをデプロイしておくと、インフラストラクチャに悪影響を及ぼすことなく、新しいデプロイで再利用できます。 仮想ネットワークを従来のハードウェア ネットワーク スイッチと考えると、デプロイごとにまったく新しいハードウェア スイッチを構成する必要はありません。 適切に構成された仮想ネットワークがあれば、新しい VM をその仮想ネットワークに何度もデプロイできます。仮想ネットワークの有効期間中に必要な変更は、あるとしてもごくわずかです。

このカスタム環境を作成するには、最新の [Azure CLI 2.0](/cli/azure/install-az-cli2) をインストールし、[az login](/cli/azure/#login) を使用して Azure アカウントにログインしている必要があります。

次の例では、パラメーター名を独自の値を置き換えます。 `myResourceGroup`、`myVnet`、`myVM` などは、例として使われているパラメーター名です。

## <a name="create-the-resource-group"></a>リソース グループの作成

まず、このチュートリアルで作成するすべてのものを整理する Azure リソース グループを作成します。 リソース グループの詳細については、「[Azure Resource Manager の概要](../azure-resource-manager/resource-group-overview.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)」をご覧ください。 [az group create](/cli/azure/group#create) で、リソース グループを作成します。 次の例では、`myResourceGroup` という名前のリソース グループを `westus` の場所に作成します。

```azurecli
az group create \
    --name myResourceGroup \
    --location westus
```

## <a name="create-the-virtual-network"></a>仮想ネットワークの作成

VM をデプロイする Azure 仮想ネットワークを構築します。 仮想ネットワークの詳細については、「[Azure CLI を使用した仮想ネットワークの作成](../virtual-network/virtual-networks-create-vnet-arm-cli.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)」をご覧ください。 [az network vnet create](/cli/azure/network/vnet#create) で仮想ネットワークを作成します。 次の例では、`myVnet` という名前の仮想ネットワークと `mySubnet` という名前のサブネットを作成します。

```azurecli
az network vnet create \
    --resource-group myResourceGroup \
    --location westus \
    --name myVnet \
    --address-prefix 10.10.0.0/16 \
    --subnet-name mySubnet \
    --subnet-prefix 10.10.1.0/24
```

## <a name="create-the-network-security-group"></a>ネットワーク セキュリティ グループの作成

Azure ネットワーク セキュリティ グループはネットワーク層のファイアウォールに相当します。 ネットワーク セキュリティ グループの詳細については、「[Azure CLI で NSG を作成する方法](../virtual-network/virtual-networks-create-nsg-arm-cli.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)」をご覧ください。 [az network nsg create](/cli/azure/network/nsg#create) で、ネットワーク セキュリティ グループを作成します。 次の例では、`myNetworkSecurityGroup` という名前のネットワーク セキュリティ グループを作成します。

```azurecli
az network nsg create \
    --resource-group myResourceGroup \
    --location westus \
    --name myNetworkSecurityGroup
```

## <a name="add-an-inbound-ssh-allow-rule"></a>受信 SSH 許可ルールの追加

Linux VM はインターネットからのアクセスを必要とするため、受信ポート 22 のトラッフィクを、Linux VM のポート 22 にネットワーク経由で渡すことを許可するルールが必要です。 [az network nsg rule create](/cli/azure/network/nsg/rule#create) で、ネットワーク セキュリティ グループの受信規則を&1; つ追加します。 次の例では、`myNetworkSecurityGroupRuleSSH` という名前の規則を作成します。

```azurecli
az network nsg rule create \
    --resource-group myResourceGroup \
    --nsg-name myNetworkSecurityGroup \
    --name myNetworkSecurityGroupRuleSSH \
    --protocol tcp \
    --direction inbound \
    --priority 1000 \
    --source-address-prefix '*' \
    --source-port-range '*' \
    --destination-address-prefix '*' \
    --destination-port-range 22 \
    --access allow
```

## <a name="attach-the-subnet-to-the-network-security-group"></a>ネットワーク セキュリティ グループへのサブネットの関連付け

ネットワーク セキュリティ グループの規則は、サブネットまたは特定の仮想ネットワーク インターフェイスに適用できます。 ネットワーク セキュリティ グループにサブネットを関連付けます。 [az network vnet subnet update](/cli/azure/network/vnet/subnet#update) を使用して、サブネットをネットワーク セキュリティ グループに関連付けます。

```azurecli
az network vnet subnet update \
    --resource-group myResourceGroup \
    --vnet-name myVnet \
    --name mySubnet \
    --network-security-group myNetworkSecurityGroup
```

## <a name="add-a-virtual-network-interface-card-to-the-subnet"></a>仮想ネットワーク インターフェイス カードのサブネットへの追加

仮想ネットワーク インターフェイス カード (Vnic) は重要であり、異なる VM に接続して再利用できます。 こうして再利用することで、VM は一時的であっても、VNic を静的リソースとして保持できます。 [az network nic create](/cli/azure/network/nic#create)を使用して、VNic を作成してサブネットに関連付けます。 次の例では、`myNic` という名前の Vnic を作成します。

```azurecli
az network nic create \
    --resource-group myResourceGroup \
    --location westus \
    --name myNic \
    --vnet-name myVnet \
    --subnet mySubnet
```

## <a name="deploy-the-vm-into-the-virtual-network-infrastructure"></a>仮想ネットワーク インフラストラクチャに VM をデプロイ

仮想ネットワーク、サブネット、SSH 用のポート 22 を除くすべての受信トラフィックをブロックしてサブネットを保護するファイアウォールとして機能するネットワーク セキュリティ グループの用意ができました。 これで、この既存のネットワーク インフラストラクチャ内に VM をデプロイできます。

[az vm create](/cli/azure/vm#create) を使用して VM を作成します。 完全な VM をデプロイするために Azure CLI 2.0 で使用するフラグの詳細については、「[Azure CLI を使用して、完全な Linux 環境を作成する](virtual-machines-linux-create-cli-complete.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)」をご覧ください。

次の例では、Azure Managed Disks を使用して VM を作成します。 これらのディスクは Azure プラットフォームによって処理されるため、ディスクを格納するための準備も場所も必要ありません。 Managed Disks の詳細については、「[Azure Managed Disks の概要](../storage/storage-managed-disks-overview.md)」をご覧ください。 非管理対象ディスクを使用する場合は、次の追加のメモを参照してください。

```azurecli
az vm create \
    --resource-group myResourceGroup \
    --name myVM \
    --image Debian \
    --admin-username azureuser \
    --ssh-key-value ~/.ssh/id_rsa.pub \
    --nics myNic
```

Managed Disks を使用する場合は、この手順をスキップします。 非管理対象ディスクを使用する場合は、続行するコマンドに次の追加パラメーターを加えて、`mystorageaccount` という名前のストレージ アカウントに非管理対象ディスクを作成する必要があります。 

```azurecli
    --use-unmanaged-disk \
    --storage-account mystorageaccount
```

CLI フラグを使用して既存のリソースを呼び出すことで、既存のネットワーク内に VM をデプロイするよう Azure に指示します。 繰り返しますが、仮想ネットワークとサブネットをデプロイしたら、Azure リージョン内でこれらを静的または永続的なリソースにしておくことができます。 この例では、パブリック IP アドレスを作成して VNic に割り当てていません。したがって、この VM にインターネット経由でパブリックにアクセスすることはできません。 詳細については、「[Azure CLI を使用して静的パブリック IP を持つ VM を作成する](../virtual-network/virtual-network-deploy-static-pip-arm-cli.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)」をご覧ください。

## <a name="next-steps"></a>次のステップ
Azure で仮想マシンを作成する方法の詳細については、次のリソースを参照してください。

* [Azure Resource Manager テンプレートを使用して特定のデプロイを作成する](virtual-machines-linux-cli-deploy-templates.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Azure CLI コマンドを直接使用して Linux VM 用の独自のカスタム環境を作成する](virtual-machines-linux-create-cli-complete.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [テンプレートを使用して Azure に Linux VM を作成する](virtual-machines-linux-create-ssh-secured-vm-from-template.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

