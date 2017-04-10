---
title: "Azure CLI 2.0 を使用して複数の NIC を持つ Linux VM を作成する | Microsoft Docs"
description: "Azure CLI 2.0 または Resource Manager テンプレートを使って、複数の NIC が接続された Linux VM を作成する方法について説明します。"
services: virtual-machines-linux
documentationcenter: 
author: iainfoulds
manager: timlt
editor: 
ms.assetid: 5d2d04d0-fc62-45fa-88b1-61808a2bc691
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 02/10/2017
ms.author: iainfou
translationtype: Human Translation
ms.sourcegitcommit: 7f3abdd63e43713d9d1f7ff28e44efc08167fddb
ms.openlocfilehash: e50f9ce362177a2aff8da5b1d516973c41d1149e
ms.lasthandoff: 02/27/2017


---
# <a name="create-a-linux-vm-with-multiple-nics"></a>複数の NIC を持つ Linux VM を作成する
Azure では、複数の仮想ネットワーク インターフェイス (NIC) を持つ仮想マシン (VM) を作成できます。 一般的なシナリオは、フロント エンドおよびバック エンド接続用に別々のサブネットを使用するか、監視またはバックアップ ソリューション専用のネットワークを用意することです。 この記事では、複数の NIC を持つ VM を作成するためのクイック コマンドを紹介します。 独自の Bash スクリプト内に複数の NIC を作成する方法など、詳しくは、「[Azure CLI を使用した複数の NIC VM のデプロイ](../virtual-network/virtual-network-deploy-multinic-arm-cli.md)」をご覧ください。 [VM のサイズ](virtual-machines-linux-sizes.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)によってサポートされる NIC の数が異なります。VM のサイズを決める際はご注意ください。

この記事では、Azure CLI 2.0 を使用して複数の NIC を持つ VM を作成する方法について説明します。 これらの手順は、[Azure CLI 1.0](virtual-machines-linux-multiple-nics-nodejs.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) を使用して実行することもできます。

> [!WARNING]
> VM の作成時に複数の NIC をアタッチする必要があります。既存の VM に NIC を追加することはできません。 [元の仮想ディスクに基づいて VM を作成](virtual-machines-linux-copy-vm.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)し、VM をデプロイするときに複数の NIC を作成できます。


## <a name="create-supporting-resources"></a>関連リソースを作成する
最新の [Azure CLI 2.0](/cli/azure/install-az-cli2) をインストールし、[az login](/cli/azure/#login) を使用して Azure アカウントにログインします。

次の例では、パラメーター名を独自の値を置き換えます。 パラメーター名の例には、`myResourceGroup`、`mystorageaccount`、および `myVM` が含まれています。

最初に、[az group create](/cli/azure/group#create) を使用して、リソース グループを作成します。 次の例では、`myResourceGroup` という名前のリソース グループを `WestUS` の場所に作成します。

```azurecli
az group create --name myResourceGroup --location westus
```

[az network vnet create](/cli/azure/network/vnet#create) で仮想ネットワークを作成します。 次の例では、`myVnet` という名前の仮想ネットワークと `mySubnetFrontEnd` という名前のサブネットを作成します。

```azurecli
az network vnet create --resource-group myResourceGroup --name myVnet \
  --address-prefix 192.168.0.0/16 --subnet-name mySubnetFrontEnd --subnet-prefix 192.168.1.0/24
```

[az network vnet subnet create](/cli/azure/network/vnet/subnet#create) を使用してバックエンド トラフィックのサブネットを作成します。 次の例では、`mySubnetBackEnd` という名前のサブネットを作成します。

```azurecli
az network vnet subnet create --resource-group myResourceGroup --vnet-name myVnet \
    --name mySubnetBackEnd --address-prefix 192.168.2.0/24
```

## <a name="create-and-configure-multiple-nics"></a>複数の NIC を作成して構成する
詳しくは、「[Azure CLI を使用した複数の NIC VM のデプロイ](../virtual-network/virtual-network-deploy-multinic-arm-cli.md)」をご覧ください。ここでは、すべての NIC を作成するループ プロセスのスクリプトを作成する方法についても解説しています。

通常は、VM 間でトラフィックを管理、分散するために、[ネットワーク セキュリティ グループ](../virtual-network/virtual-networks-nsg.md)や[ロード バランサー](../load-balancer/load-balancer-overview.md)を作成します。 [az network nsg create](/cli/azure/network/nsg#create) で、ネットワーク セキュリティ グループを作成します。 次の例では、`myNetworkSecurityGroup` という名前のネットワーク セキュリティ グループを作成します。

```azurecli
az network nsg create --resource-group myResourceGroup \
  --name myNetworkSecurityGroup
```

[az network nic create](/cli/azure/network/nic#create) を使用して、2 つの NIC を作成します。 次の例では、ネットワーク セキュリティ グループに接続された、`myNic1` と `myNic2` という名前の&2; つの NIC を作成します (1 つの NIC が各サブネットに接続します)。

```azurecli
az network nic create --resource-group myResourceGroup --name myNic1 \
  --vnet-name myVnet --subnet mySubnetFrontEnd \
  --network-security-group myNetworkSecurityGroup
az network nic create --resource-group myResourceGroup --name myNic2 \
  --vnet-name myVnet --subnet mySubnetBackEnd \
  --network-security-group myNetworkSecurityGroup
```

## <a name="create-a-vm-and-attach-the-nics"></a>VM を作成して NIC を接続する
VM を作成するとき、`--nics` を使用して、作成した NIC を指定します。 VM のサイズを選択する際には注意が必要です。 1 つの VM に追加できる NIC の合計数には制限があります。 詳しくは、 [Linux VM のサイズ](virtual-machines-linux-sizes.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)に関する記事をご覧ください。 

[az vm create](/cli/azure/vm#create) を使用して VM を作成します。 次の例では、[Azure Managed Disks](../storage/storage-managed-disks-overview.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) を使用して、`myVM` という名前の VM を作成します。

```azurecli
az vm create \
    --resource-group myResourceGroup \
    --name myVM \
    --image UbuntuLTS \
    --size Standard_DS2_v2 \
    --admin-username azureuser \
    --ssh-key-value ~/.ssh/id_rsa.pub \
    --nics myNic1 myNic2
```

## <a name="create-multiple-nics-using-resource-manager-templates"></a>Resource Manager テンプレートを使用して複数の NIC を作成する
Azure Resource Manager テンプレートで宣言型の JSON ファイルを使用して環境を定義します。 詳しくは、「[Azure Resource Manager の概要](../azure-resource-manager/resource-group-overview.md)」をご覧ください。 Resource Manager テンプレートでは、複数の NIC の作成など、デプロイ時にリソースの複数のインスタンスを作成することができます。 *copy* を使用して、作成するインスタンスの数を指定します。

```json
"copy": {
    "name": "multiplenics"
    "count": "[parameters('count')]"
}
```

詳細については、[*copy* を使用した複数のインスタンスの作成](../azure-resource-manager/resource-group-create-multiple.md)に関する記事を参照してください。 

`copyIndex()` を使用してリソース名に数値を追加することもできます。これにより、`myNic1`、`myNic2` などを作成することができます。インデックス値を追加する例を次に示します。

```json
"name": "[concat('myNic', copyIndex())]", 
```

完全な例については、「 [Resource Manager テンプレートを使用して複数の NIC を作成する](../virtual-network/virtual-network-deploy-multinic-arm-template.md)」を参照してください。

## <a name="next-steps"></a>次のステップ
複数の NIC を持つ VM を作成する際は、 [Linux VM のサイズ](virtual-machines-linux-sizes.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) を必ず確認してください。 VM の各サイズでサポートされている NIC の最大数に注意してください。 

既存の VM に NIC を追加することはできません。VM をデプロイするときに、すべての NIC を作成する必要があります。 デプロイメントの計画時に、初めから必要なすべてのネットワーク接続があることを確認してください。


