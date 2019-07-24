---
title: ネットワーク トラフィックをルーティングする - Azure CLI | Microsoft Docs
description: この記事では、Azure CLI を使用してルート テーブルでネットワーク トラフィックをルーティングする方法について説明します。
services: virtual-network
documentationcenter: virtual-network
author: jimdial
manager: jeconnoc
editor: ''
tags: azure-resource-manager
Customer intent: I want to route traffic from one subnet, to a different subnet, through a network virtual appliance.
ms.assetid: ''
ms.service: virtual-network
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: virtual-network
ms.workload: infrastructure
ms.date: 03/13/2018
ms.author: jdial
ms.custom: ''
ms.openlocfilehash: e3106a792f486a62986734e562c96b41dd8a75dc
ms.sourcegitcommit: 1c2cf60ff7da5e1e01952ed18ea9a85ba333774c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/12/2019
ms.locfileid: "59524125"
---
# <a name="route-network-traffic-with-a-route-table-using-the-azure-cli"></a>Azure CLI を使用してルート テーブルでネットワーク トラフィックをルーティングする

Azure は、既定では、トラフィックを仮想ネットワーク内のすべてのサブネット間で自動的にルーティングします。 Azure の既定のルーティングは、独自のルートを作成してオーバーライドすることができます。 カスタム ルートを作成する機能は、たとえば、サブネット間でネットワーク仮想アプライアンス (NVA) を越えてトラフィックをルーティングしたい場合に便利です。 この記事では、次のことについて説明します。

* ルート テーブルの作成
* ルートの作成
* 複数のサブネットを含んだ仮想ネットワークを作成する
* サブネットへのルート テーブルの関連付け
* トラフィックをルーティングする NVA を作成する
* 仮想マシン (VM) を異なるサブネットに展開する
* NVA を介して、あるサブネットから別のサブネットにトラフィックをルーティングする

Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) を作成してください。

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

CLI をローカルにインストールして使用することを選択する場合、このクイック スタートでは、Azure CLI バージョン 2.0.28 以降を実行している必要があります。 バージョンを確認するには、`az --version` を実行します。 インストールまたはアップグレードする必要がある場合は、[Azure CLI のインストール](/cli/azure/install-azure-cli)に関するページを参照してください。 

## <a name="create-a-route-table"></a>ルート テーブルの作成

ルート テーブルを作成するには、この記事で作成されるすべてのリソースのリソース グループを [az group create](/cli/azure/group) で作成しておきます。 

```azurecli-interactive
# Create a resource group.
az group create \
  --name myResourceGroup \
  --location eastus
``` 

[az network route-table create](/cli/azure/network/route-table#az-network-route-table-create) でルート テーブルを作成します。 次の例では、*myRouteTablePublic* という名前のルート テーブルを作成します。 

```azurecli-interactive 
# Create a route table
az network route-table create \
  --resource-group myResourceGroup \
  --name myRouteTablePublic
```

## <a name="create-a-route"></a>ルートの作成

[az network route-table route create](/cli/azure/network/route-table/route#az-network-route-table-route-create) でルート テーブル内にルートを作成します。 

```azurecli-interactive
az network route-table route create \
  --name ToPrivateSubnet \
  --resource-group myResourceGroup \
  --route-table-name myRouteTablePublic \
  --address-prefix 10.0.1.0/24 \
  --next-hop-type VirtualAppliance \
  --next-hop-ip-address 10.0.2.4
``` 

## <a name="associate-a-route-table-to-a-subnet"></a>サブネットへのルート テーブルの関連付け

ルート テーブルをサブネットに関連付けるには、仮想ネットワークとサブネットを作成しておく必要があります。 [az network vnet create](/cli/azure/network/vnet) で、1 つのサブネットを含む仮想ネットワークを作成します。

```azurecli-interactive
az network vnet create \
  --name myVirtualNetwork \
  --resource-group myResourceGroup \
  --address-prefix 10.0.0.0/16 \
  --subnet-name Public \
  --subnet-prefix 10.0.0.0/24
```

[az network vnet subnet create](/cli/azure/network/vnet/subnet) で 2 つの追加のサブネットを作成します。

```azurecli-interactive
# Create a private subnet.
az network vnet subnet create \
  --vnet-name myVirtualNetwork \
  --resource-group myResourceGroup \
  --name Private \
  --address-prefix 10.0.1.0/24

# Create a DMZ subnet.
az network vnet subnet create \
  --vnet-name myVirtualNetwork \
  --resource-group myResourceGroup \
  --name DMZ \
  --address-prefix 10.0.2.0/24
```

[az network vnet subnet update](/cli/azure/network/vnet/subnet) で、*myRouteTablePublic* ルート テーブルを*パブリック* サブネットに関連付けます。

```azurecli-interactive
az network vnet subnet update \
  --vnet-name myVirtualNetwork \
  --name Public \
  --resource-group myResourceGroup \
  --route-table myRouteTablePublic
```

## <a name="create-an-nva"></a>NVA を作成する

NVA は、ルーティング、ファイアウォール、WAN 最適化などのネットワーク機能を実行する VM です。

[az vm create](/cli/azure/vm) を使用して、*DMZ* サブネットに NVA を作成します。 VM を作成すると、既定では、Azure はパブリック IP アドレスを作成し、その VM に割り当てます。 この VM にはインターネットから接続する必要がないため、`--public-ip-address ""` パラメーターは Azure に、パブリック IP アドレスを作成して VM に割り当てることのないよう指示します。 既定のキーの場所にまだ SSH キーが存在しない場合は、コマンドを使って SSH キーを作成します。 特定のキーのセットを使用するには、`--ssh-key-value` オプションを使用します。

```azure-cli-interactive
az vm create \
  --resource-group myResourceGroup \
  --name myVmNva \
  --image UbuntuLTS \
  --public-ip-address "" \
  --subnet DMZ \
  --vnet-name myVirtualNetwork \
  --generate-ssh-keys
```

VM の作成には数分かかります。 Azure が VM の作成を完了し、その VM に関する出力を返すまでは、次の手順に進まないでください。 

ネットワーク インターフェイスが、自身に送信されてきたネットワーク トラフィックのうち、自身の IP アドレス宛てではないものを転送できるように、このネットワーク インターフェイスに対して IP 転送を有効にする必要があります。 [az network nic update](/cli/azure/network/nic) でネットワーク インターフェイスの IP 転送を有効にします。

```azurecli-interactive
az network nic update \
  --name myVmNvaVMNic \
  --resource-group myResourceGroup \
  --ip-forwarding true
```

VM 内では、オペレーティング システム、または VM 内で実行中のアプリケーションも、ネットワーク トラフィックを転送できる必要があります。 [az vm extension set](/cli/azure/vm/extension) を使用して、VM のオペレーティング システム内で IP 転送を有効にします。

```azurecli-interactive
az vm extension set \
  --resource-group myResourceGroup \
  --vm-name myVmNva \
  --name customScript \
  --publisher Microsoft.Azure.Extensions \
  --settings '{"commandToExecute":"sudo sysctl -w net.ipv4.ip_forward=1"}'
```
このコマンドは、実行するのに最大 1 分かかることがあります。

## <a name="create-virtual-machines"></a>仮想マシンを作成する

後の手順で、*パブリック* サブネットからのトラフィックが NVA 経由で*プライベート* サブネットにルーティングされていることを検証できるように、仮想ネットワークに 2 つの VM を作成します。 

[az vm create](/cli/azure/vm) を使用して、*パブリック* サブネット内に VM を作成します。 `--no-wait` パラメーターを使用すると、Azure はバックグラウンドでコマンドを実行できるので、次のコマンドに進むことができます。 この記事を効率化するために、パスワードが使用されています。 通常、キーは運用環境デプロイで使用されます。 キーを使用する場合は、SSH エージェント転送も構成する必要があります。 詳細については、SSH クライアントのドキュメントを参照してください。 次のコマンドの `<replace-with-your-password>` を、使用するパスワードに置き換えます。

```azurecli-interactive
adminPassword="<replace-with-your-password>"

az vm create \
  --resource-group myResourceGroup \
  --name myVmPublic \
  --image UbuntuLTS \
  --vnet-name myVirtualNetwork \
  --subnet Public \
  --admin-username azureuser \
  --admin-password $adminPassword \
  --no-wait
```

*プライベート* サブネット内に VM を作成します。

```azurecli-interactive
az vm create \
  --resource-group myResourceGroup \
  --name myVmPrivate \
  --image UbuntuLTS \
  --vnet-name myVirtualNetwork \
  --subnet Private \
  --admin-username azureuser \
  --admin-password $adminPassword
```

VM の作成には数分かかります。 VM が作成されると、Azure CLI によって次の例のような情報が表示されます。 

```azurecli 
{
  "fqdns": "",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVmPrivate",
  "location": "eastus",
  "macAddress": "00-0D-3A-23-9A-49",
  "powerState": "VM running",
  "privateIpAddress": "10.0.1.4",
  "publicIpAddress": "13.90.242.231",
  "resourceGroup": "myResourceGroup"
}
```
**publicIpAddress** を書き留めておきます。 このアドレスは、後の手順で、インターネットから VM にアクセスするときに使います。

## <a name="route-traffic-through-an-nva"></a>NVA を経由するトラフィックのルーティング

次のコマンドを使用して、*myVmPrivate* VM との SSH セッションを作成します。 *\<publicIpAddress>* を VM のパブリック IP アドレスに置き換えます。 上の例では、IP アドレスは *13.90.242.231* です。

```bash 
ssh azureuser@<publicIpAddress>
```

パスワードの入力を求められたら、「[仮想マシンを作成する](#create-virtual-machines)」で選択したパスワードを入力します。

次のコマンドを使用して、*myVmPrivate* VM に traceroute をインストールします。

```bash 
sudo apt-get install traceroute
```

次のコマンドを使用して、*myVmPrivate* VM から *myVmPublic* VM へのネットワーク トラフィックのルーティングをテストします。

```bash
traceroute myVmPublic
```

応答は次の例のようになります。

```bash
traceroute to myVmPublic (10.0.0.4), 30 hops max, 60 byte packets
1  10.0.0.4 (10.0.0.4)  1.404 ms  1.403 ms  1.398 ms
```

トラフィックは、*myVmPrivate* VM から *myVmPublic* VM に直接ルーティングされていることがわかります。 Azure の既定のルートでは、サブネット間でトラフィックが直接ルーティングされます。 

次のコマンドを使用して、*myVmPrivate* VM から *myVmPublic* VM へ SSH 接続します。

```bash 
ssh azureuser@myVmPublic
```

次のコマンドを使用して、*myVmPublic* VM に traceroute をインストールします。

```bash 
sudo apt-get install traceroute
```

次のコマンドを使用して、*myVmPublic* VM から *myVmPrivate* VM へのネットワーク トラフィックのルーティングをテストします。

```bash
traceroute myVmPrivate
```

応答は次の例のようになります。

```bash
traceroute to myVmPrivate (10.0.1.4), 30 hops max, 60 byte packets
1  10.0.2.4 (10.0.2.4)  0.781 ms  0.780 ms  0.775 ms
2  10.0.1.4 (10.0.0.4)  1.404 ms  1.403 ms  1.398 ms
```
最初のホップが 10.0.2.4 であることを確認できます。これは、NVA のプライベート IP アドレスです。 2 番目のホップは 10.0.1.4 です。これは、*myVmPrivate* VM のプライベート IP アドレスです。 *myRouteTablePublic* ルート テーブルに追加され、"*パブリック*" サブネットに関連付けられているルートにより、Azure はトラフィックを "*プライベート*" サブネットに直接ルーティングするのではなく、NVA 経由でルーティングするようになります。

*myVmPublic* VM と *myVmPrivate* VM の両方に対する SSH セッションを閉じます。

## <a name="clean-up-resources"></a>リソースのクリーンアップ

不要になったら、[az group delete](/cli/azure/group) を使用して、リソース グループとそのグループに含まれているすべてのリソースを削除します。

```azurecli-interactive 
az group delete --name myResourceGroup --yes
```

## <a name="next-steps"></a>次の手順

この記事では、ルート テーブルを作成し、それをサブネットに関連付けました。 トラフィックをパブリック サブネットからプライベート サブネットにルーティングする単純な NVA を作成しました。 [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/category/networking) からファイアウォールや WAN 最適化などのネットワーク機能を実行する、さまざまな事前構成された NVA を展開します。 ルーティングの詳細については、[ルーティングの概要](virtual-networks-udr-overview.md)と[ルート テーブルの管理](manage-route-table.md)に関する記事をご覧ください。

仮想ネットワーク内では多数の Azure リソースをデプロイできますが、一部の Azure PaaS サービスのリソースは仮想ネットワークにデプロイできなません。 ただし、一部の Azure PaaS サービスのリソースへのアクセスを、仮想ネットワーク サブネットからのトラフィックのみに制限できます。 方法については、[PaaS リソースへのネットワーク アクセスの制限](tutorial-restrict-network-access-to-resources-cli.md)に関する記事をご覧ください。
