---
title: "複数の NIC を持つ Linux VM を Azure に作成する | Microsoft Docs"
description: "Azure CLI または Resource Manager テンプレートを使って、複数の NIC を持つ Linux VM を作成する方法について説明します。"
services: virtual-machines-linux
documentationcenter: 
author: iainfoulds
manager: timlt
editor: 
ms.assetid: 
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 02/09/2017
ms.author: iainfou
translationtype: Human Translation
ms.sourcegitcommit: 0d8472cb3b0d891d2b184621d62830d1ccd5e2e7
ms.openlocfilehash: 51edde1be97885b92fe48f65d3f477c73cd19338
ms.lasthandoff: 03/21/2017


---
# <a name="create-a-linux-vm-with-multiple-nics-using-the-azure-cli-10"></a>Azure CLI 1.0 を使用して複数の NIC を持つ Linux VM を作成する
Azure では、複数の仮想ネットワーク インターフェイス (NIC) を持つ仮想マシン (VM) を作成できます。 一般的なシナリオは、フロント エンドおよびバック エンド接続用に別々のサブネットを使用するか、監視またはバックアップ ソリューション専用のネットワークを用意することです。 この記事では、複数の NIC を持つ VM を作成するためのクイック コマンドを紹介します。 独自の Bash スクリプト内に複数の NIC を作成する方法など、詳しくは、「[Azure CLI を使用した複数の NIC VM のデプロイ](../virtual-network/virtual-network-deploy-multinic-arm-cli.md)」をご覧ください。 [VM のサイズ](virtual-machines-linux-sizes.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)によってサポートされる NIC の数が異なります。VM のサイズを決める際はご注意ください。

> [!WARNING]
> VM の作成時に複数の NIC をアタッチする必要があります。既存の VM に NIC を追加することはできません。 [元の仮想ディスクに基づいて VM を作成](virtual-machines-linux-copy-vm.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)し、VM をデプロイするときに複数の NIC を作成できます。


## <a name="cli-versions-to-complete-the-task"></a>タスクを完了するための CLI バージョン
次のいずれかの CLI バージョンを使用してタスクを完了できます。

- [Azure CLI 1.0](#create-supporting-resources) - クラシック デプロイメント モデルと Resource Manager デプロイメント モデル用の CLI (本記事)
- [Azure CLI 2.0](virtual-machines-linux-multiple-nics.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) - Resource Manager デプロイ モデル用の次世代 CLI


## <a name="create-supporting-resources"></a>関連リソースを作成する
[Azure CLI](../cli-install-nodejs.md) でログインし、Resource Manager モードを使用していることを確認します。

```azurecli
azure config mode arm
```

次の例では、パラメーター名を独自の値を置き換えます。 パラメーター名の例には、`myResourceGroup`、`mystorageaccount`、および `myVM` が含まれています。

まず、リソース グループを作成します。 次の例では、`myResourceGroup` という名前のリソース グループを `WestUS` の場所に作成します。

```azurecli
azure group create myResourceGroup -l WestUS
```

VM を保持するストレージ アカウントを作成します。 次の例では、`mystorageaccount` という名前のストレージ アカウントを作成します。

```azurecli
azure storage account create mystorageaccount -g myResourceGroup \
    -l WestUS --kind Storage --sku-name PLRS
```

VM を接続する仮想ネットワークを作成します。 次の例では、`192.168.0.0/16` というアドレス プレフィックスで `myVnet` という名前の仮想ネットワークを作成します。

```azurecli
azure network vnet create -g myResourceGroup -l WestUS \
    -n myVnet -a 192.168.0.0/16
```

2 つの仮想ネットワーク サブネットを作成します。1 つはフロントエンド トラフィック用、もう&1; つはバックエンド トラフィック用です。 次の例では、`mySubnetFrontEnd` と `mySubnetBackEnd` という名前の&2; つのサブネットを作成します。

```azurecli
azure network vnet subnet create -g myResourceGroup -e myVnet \
    -n mySubnetFrontEnd -a 192.168.1.0/24
azure network vnet subnet create -g myResourceGroup -e myVnet \
    -n mySubnetBackEnd -a 192.168.2.0/24
```

## <a name="create-and-configure-multiple-nics"></a>複数の NIC を作成して構成する
詳しくは、「[Azure CLI を使用した複数の NIC VM のデプロイ](../virtual-network/virtual-network-deploy-multinic-arm-cli.md)」をご覧ください。ここでは、すべての NIC を作成するループ プロセスのスクリプトを作成する方法についても解説しています。

次の例では、`myNic1` と `myNic2` という名前の&2; つの NIC を作成し、1 つの NIC を各サブネットに接続します。

```azurecli
azure network nic create --resource-group myResourceGroup --location WestUS \
    -n myNic1 --subnet-vnet-name myVnet --subnet-name mySubnetFrontEnd
azure network nic create --resource-group myResourceGroup --location WestUS \
    -n myNic2 --subnet-vnet-name myVnet --subnet-name mySubnetBackEnd
```

通常は、VM 間でトラフィックを管理、分散するために、[ネットワーク セキュリティ グループ](../virtual-network/virtual-networks-nsg.md)や[ロード バランサー](../load-balancer/load-balancer-overview.md)も作成します。 次の例では、`myNetworkSecurityGroup` という名前のネットワーク セキュリティ グループを作成します。

```azurecli
azure network nsg create --resource-group myResourceGroup --location WestUS \
    --name myNetworkSecurityGroup
```

`azure network nic set` を使って NIC をネットワーク セキュリティ グループにバインドします。 次の例では、`myNic1` および `myNic2` を `myNetworkSecurityGroup` とバインドします。

```azurecli
azure network nic set --resource-group myResourceGroup --name myNic1 \
    --network-security-group-name myNetworkSecurityGroup
azure network nic set --resource-group myResourceGroup --name myNic2 \
    --network-security-group-name myNetworkSecurityGroup
```

## <a name="create-a-vm-and-attach-the-nics"></a>VM を作成して NIC を接続する
VM を作成するときに、複数の NIC を指定します。 `--nic-name` を使用して&1; つの NIC を指定する代わりに、`--nic-names` を使用して NIC のコンマ区切りのリストを指定します。 VM のサイズを選択する際には注意が必要です。 1 つの VM に追加できる NIC の合計数には制限があります。 詳しくは、 [Linux VM のサイズ](virtual-machines-linux-sizes.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)に関する記事をご覧ください。 次の例は、複数の NIC を指定し、複数 NIC の使用をサポートする VM のサイズを指定する方法を示しています (`Standard_DS2_v2`)。

```azurecli
azure vm create \
    --resource-group myResourceGroup \
    --name myVM \
    --location WestUS \
    --os-type linux \
    --nic-names myNic1,myNic2 \
    --vm-size Standard_DS2_v2 \
    --storage-account-name mystorageaccount \
    --image-urn UbuntuLTS \
    --admin-username azureuser \
    --ssh-publickey-file ~/.ssh/id_rsa.pub
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


