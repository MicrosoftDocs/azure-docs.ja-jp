---
title: 複数の NIC を持つ Linux VM を Azure に作成する
description: Azure CLI または Resource Manager テンプレートを使って、複数の NIC を持つ Linux VM を作成する方法について説明します。
author: cynthn
ms.service: virtual-machines
ms.subservice: networking
ms.topic: how-to
ms.workload: infrastructure
ms.date: 06/07/2018
ms.author: cynthn
ms.openlocfilehash: c0eea74890665297a0d450c8afd0a5d60dd1ae00
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/30/2021
ms.locfileid: "102551812"
---
# <a name="how-to-create-a-linux-virtual-machine-in-azure-with-multiple-network-interface-cards"></a>複数のネットワーク インターフェイス カードを使用して Linux 仮想マシンを Azure に作成する方法


この記事では、Azure CLI を使用して複数の NIC を持つ VM を作成する方法について説明します。

## <a name="create-supporting-resources"></a>関連リソースを作成する
最新の [Azure CLI](/cli/azure/install-az-cli2) をインストールし、[az login](/cli/azure/reference-index) を使用して Azure アカウントにログインします。

次の例では、パラメーター名を独自の値を置き換えます。 たとえば、*myResourceGroup*、*mystorageaccount*、*myVM* といったパラメーター名にします。

最初に、[az group create](/cli/azure/group) を使用して、リソース グループを作成します。 次の例では、*myResourceGroup* という名前のリソース グループを *eastus* に作成します。

```azurecli
az group create --name myResourceGroup --location eastus
```

[az network vnet create](/cli/azure/network/vnet) で仮想ネットワークを作成します。 次の例では、*myVnet* という名前の仮想ネットワークと *mySubnetFrontEnd* という名前のサブネットを作成します。

```azurecli
az network vnet create \
    --resource-group myResourceGroup \
    --name myVnet \
    --address-prefix 10.0.0.0/16 \
    --subnet-name mySubnetFrontEnd \
    --subnet-prefix 10.0.1.0/24
```

[az network vnet subnet create](/cli/azure/network/vnet/subnet) を使用してバックエンド トラフィックのサブネットを作成します。 次の例では、*mySubnetBackEnd* という名前のサブネットを作成します。

```azurecli
az network vnet subnet create \
    --resource-group myResourceGroup \
    --vnet-name myVnet \
    --name mySubnetBackEnd \
    --address-prefix 10.0.2.0/24
```

[az network nsg create](/cli/azure/network/nsg) で、ネットワーク セキュリティ グループを作成します。 次の例では、*myNetworkSecurityGroup* という名前のネットワーク セキュリティ グループを作成します。

```azurecli
az network nsg create \
    --resource-group myResourceGroup \
    --name myNetworkSecurityGroup
```

## <a name="create-and-configure-multiple-nics"></a>複数の NIC を作成して構成する
[az network nic create](/cli/azure/network/nic) を使用して、2 つの NIC を作成します。 次の例では、ネットワーク セキュリティ グループに接続された、*myNic1* と *myNic2* という名前の 2 つの NIC を作成します (1 つの NIC が各サブネットに接続します)。

```azurecli
az network nic create \
    --resource-group myResourceGroup \
    --name myNic1 \
    --vnet-name myVnet \
    --subnet mySubnetFrontEnd \
    --network-security-group myNetworkSecurityGroup
az network nic create \
    --resource-group myResourceGroup \
    --name myNic2 \
    --vnet-name myVnet \
    --subnet mySubnetBackEnd \
    --network-security-group myNetworkSecurityGroup
```

## <a name="create-a-vm-and-attach-the-nics"></a>VM を作成して NIC を接続する
VM を作成するとき、`--nics` を使用して、作成した NIC を指定します。 VM のサイズを選択する際には注意が必要です。 1 つの VM に追加できる NIC の合計数には制限があります。 詳しくは、 [Linux VM のサイズ](../sizes.md)に関する記事をご覧ください。

[az vm create](/cli/azure/vm) を使用して VM を作成します。 次の例では、*myVM* という名前の VM を作成します。

```azurecli
az vm create \
    --resource-group myResourceGroup \
    --name myVM \
    --image UbuntuLTS \
    --size Standard_DS3_v2 \
    --admin-username azureuser \
    --generate-ssh-keys \
    --nics myNic1 myNic2
```

「[複数の NIC 用にゲスト OS を構成する](#configure-guest-os-for-multiple-nics)」の手順で、ゲスト OS にルーティング テーブルを追加します。

## <a name="add-a-nic-to-a-vm"></a>VM に NIC を追加する
前の手順では、複数の NIC を含む VM を作成しました。 Azure CLI を使用して NIC を既存の VM に追加することもできます。 [VM のサイズ](../sizes.md)によってサポートされる NIC の数が異なります。VM のサイズを決める際はご注意ください。 必要な場合は、[VM のサイズを変更できます](change-vm-size.md)。

[az network nic create](/cli/azure/network/nic) を使用して別の仮想 NIC を作成します。 次の例では、バックエンドのサブネットおよび前の手順で作成されたネットワーク セキュリティ グループに接続された *myNic3* という名前の NIC を作成します。

```azurecli
az network nic create \
    --resource-group myResourceGroup \
    --name myNic3 \
    --vnet-name myVnet \
    --subnet mySubnetBackEnd \
    --network-security-group myNetworkSecurityGroup
```

NIC を既存の VM に追加するには、最初に [az vm deallocate](/cli/azure/vm) を使用して VM の割り当てを解除します。 次の例では、*myVM* という名前の VM の割り当てを解除します。


```azurecli
az vm deallocate --resource-group myResourceGroup --name myVM
```

[az vm nic add](/cli/azure/vm/nic) を使用して NIC を追加します。 次の例では、*myNic3* を *myVM* に追加します。

```azurecli
az vm nic add \
    --resource-group myResourceGroup \
    --vm-name myVM \
    --nics myNic3
```

[az vm start](/cli/azure/vm) を使用して VM を起動します。

```azurecli
az vm start --resource-group myResourceGroup --name myVM
```

「[複数の NIC 用にゲスト OS を構成する](#configure-guest-os-for-multiple-nics)」の手順で、ゲスト OS にルーティング テーブルを追加します。

## <a name="remove-a-nic-from-a-vm"></a>NIC を VM から削除する
NIC を既存の VM から削除するには、最初に [az vm deallocate](/cli/azure/vm) を使用して VM の割り当てを解除します。 次の例では、*myVM* という名前の VM の割り当てを解除します。

```azurecli
az vm deallocate --resource-group myResourceGroup --name myVM
```

[az vm nic remove](/cli/azure/vm/nic) を使用して NIC を削除します。 次の例では、*myNic3* を *myVM* から削除します。

```azurecli
az vm nic remove \
    --resource-group myResourceGroup \
    --vm-name myVM \
    --nics myNic3
```

[az vm start](/cli/azure/vm) を使用して VM を起動します。

```azurecli
az vm start --resource-group myResourceGroup --name myVM
```


## <a name="create-multiple-nics-using-resource-manager-templates"></a>Resource Manager テンプレートを使用して複数の NIC を作成する
Azure Resource Manager テンプレートで宣言型の JSON ファイルを使用して環境を定義します。 詳しくは、「[Azure Resource Manager の概要](../../azure-resource-manager/management/overview.md)」をご覧ください。 Resource Manager テンプレートでは、複数の NIC の作成など、デプロイ時にリソースの複数のインスタンスを作成することができます。 *copy* を使用して、作成するインスタンスの数を指定します。

```json
"copy": {
    "name": "multiplenics"
    "count": "[parameters('count')]"
}
```

詳細については、[*copy* を使用した複数のインスタンスの作成](../../azure-resource-manager/templates/copy-resources.md)に関する記事を参照してください。 

`copyIndex()` を使用してリソース名に数値を追加することもできます。これにより、`myNic1`、`myNic2` などを作成することができます。インデックス値を追加する例を次に示します。

```json
"name": "[concat('myNic', copyIndex())]", 
```

完全な例については、「 [Resource Manager テンプレートを使用して複数の NIC を作成する](../../virtual-network/template-samples.md)」を参照してください。

「[複数の NIC 用にゲスト OS を構成する](#configure-guest-os-for-multiple-nics)」の手順で、ゲスト OS にルーティング テーブルを追加します。

## <a name="configure-guest-os-for-multiple-nics"></a>複数の NIC 用にゲスト OS を構成する

前の手順では、仮想ネットワークとサブネットを作成し、NIC を接続した後、VM を作成しました。 パブリック IP アドレスと SSH トラフィックを許可するネットワーク セキュリティ グループの規則は作成していません。 複数の NIC 用にゲスト OS を構成するには、リモート接続を許可し、VM 上でローカルにコマンドを実行する必要があります。

SSH トラフィックを許可するには、次のように [az network nsg rule create](/cli/azure/network/nsg/rule#az-network-nsg-rule-create) を使用してネットワーク セキュリティ グループの規則を作成します。

```azurecli
az network nsg rule create \
    --resource-group myResourceGroup \
    --nsg-name myNetworkSecurityGroup \
    --name allow_ssh \
    --priority 101 \
    --destination-port-ranges 22
```

[az network public-ip create](/cli/azure/network/public-ip#az-network-public-ip-create) を使用してパブリック IP アドレスを作成し、[az network nic ip-config update](/cli/azure/network/nic/ip-config#az-network-nic-ip-config-update) を使用してこれを最初の NIC に割り当てます。

```azurecli
az network public-ip create --resource-group myResourceGroup --name myPublicIP

az network nic ip-config update \
    --resource-group myResourceGroup \
    --nic-name myNic1 \
    --name ipconfig1 \
    --public-ip myPublicIP
```

VM のパブリック IP アドレスを表示するには、次のように [az vm show](/cli/azure/vm#az-vm-show) を使用します。

```azurecli
az vm show --resource-group myResourceGroup --name myVM -d --query publicIps -o tsv
```

次に、VM のパブリック IP アドレスに SSH 接続します。 前の手順で指定した既定のユーザー名は *azureuser* でした。 自分のユーザー名とパブリック IP アドレスを指定してください。

```bash
ssh azureuser@137.117.58.232
```

セカンダリ ネットワーク インターフェイスとの間で送受信を行うには、セカンダリ ネットワーク インターフェイスごとに、オペレーティング システムに永続的なルートを手動で追加する必要があります。 この記事では、*eth1* がセカンダリ インターフェイスです。 オペレーティング システムに永続的なルートを追加する方法は、ディストリビューションによって異なります。 手順については、ディストリビューションのドキュメントを参照してください。

オペレーティング システムにルートを追加する場合、ゲートウェイ アドレスは、ネットワーク インターフェイスがあるサブネットの *.1* です。 たとえば、ネットワーク インターフェイスにアドレス *10.0.2.4* が割り当てられている場合、ルートに指定するゲートウェイは *10.0.2.1* です。 ルートの宛先には特定のネットワークを定義できます。また、インターフェイスのすべてのトラフィックが指定されたゲートウェイを通過するようにするには、*0.0.0.0* の宛先を指定します。 各サブネットのゲートウェイは仮想ネットワークによって管理されます。

セカンダリ インターフェイスのルートを追加したら、`route -n` を使用してそのルートがルート テーブルに含まれていることを確認します。 この記事の VM に 2 つのネットワーク インターフェイスが追加されたルート テーブルの出力例を次に示します。

```bash
Kernel IP routing table
Destination     Gateway         Genmask         Flags Metric Ref    Use Iface
0.0.0.0         10.0.1.1        0.0.0.0         UG    0      0        0 eth0
0.0.0.0         10.0.2.1        0.0.0.0         UG    0      0        0 eth1
10.0.1.0        0.0.0.0         255.255.255.0   U     0      0        0 eth0
10.0.2.0        0.0.0.0         255.255.255.0   U     0      0        0 eth1
168.63.129.16   10.0.1.1        255.255.255.255 UGH   0      0        0 eth0
169.254.169.254 10.0.1.1        255.255.255.255 UGH   0      0        0 eth0
```

再起動後にルート テーブルを再度確認して、追加したルートが再起動後も保持されていることを確認します。 接続をテストするには、たとえば、次のコマンドを入力します。ここで、*eth1* は、セカンダリ ネットワーク インターフェイスの名前です。

```bash
ping bing.com -c 4 -I eth1
```

## <a name="next-steps"></a>次のステップ
複数の NIC を持つ VM を作成する際は、 [Linux VM のサイズ](../sizes.md) を確認してください。 VM の各サイズでサポートされている NIC の最大数に注意してください。

VM をさらにセキュリティで保護するには、Just In Time VM アクセスを使用します。 この機能は、必要に応じて一定期間にわたり、SSH トラフィックに対してネットワーク セキュリティ グループ規則を開きます。 詳細については、「[Manage virtual machine access using just in time (ジャスト イン タイムを使用して仮想マシンへのアクセスを管理する)](../../security-center/security-center-just-in-time.md)」を参照してください。