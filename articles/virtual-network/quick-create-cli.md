---
title: 仮想ネットワークの作成 - クイック スタート - Azure CLI
titlesuffix: Azure Virtual Network
description: このクイック スタートでは、Azure CLI を使用して仮想ネットワークを作成する方法について説明します。 仮想ネットワークによって、仮想マシンなどの Azure リソースが互いにプライベートな通信を行ったりインターネットと通信したりできるようになります。
services: virtual-network
documentationcenter: virtual-network
author: kumudd
Customer intent: I want to create a virtual network so that virtual machines can communicate with privately with each other and with the internet.
ms.service: virtual-network
ms.devlang: azurecli
ms.topic: quickstart
ms.tgt_pltfrm: virtual-network
ms.workload: infrastructure
ms.date: 12/12/2018
ms.author: kumud
ms.openlocfilehash: 6306d893f491f93cc31b7e478afe5632e997285c
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/28/2019
ms.locfileid: "64692640"
---
# <a name="quickstart-create-a-virtual-network-using-the-azure-cli"></a>クイック スタート:Azure CLI を使用した仮想ネットワークの作成

仮想ネットワークによって、仮想マシン (VM) などの Azure リソースが互いにプライベートな通信を行ったりインターネットと通信したりできるようになります。 このクイック スタートでは、仮想ネットワークの作成方法について説明します。 仮想ネットワークを作成したら、2 つの VM を仮想ネットワークにデプロイします。 次にインターネットから VM に接続し、新しい仮想ネットワークを介してプライベートで通信します。

Azure サブスクリプションをお持ちでない場合は、ここで[無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)を作成してください。

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

代わりに Azure CLI をローカルにインストールして使用する場合は、このクイック スタートには Azure CLI バージョン 2.0.28 以降を使用する必要があります。 インストールされているバージョンを確認するには、`az --version` を実行します。 インストールまたはアップグレードについては、「[Azure CLI のインストール](/cli/azure/install-azure-cli)」をご覧ください。

## <a name="create-a-resource-group-and-a-virtual-network"></a>リソース グループと仮想ネットワークを作成する

仮想ネットワークを作成するには、その前に、仮想ネットワークをホストするリソース グループを作成する必要があります。 [az group create](/cli/azure/group) を使用して、リソース グループを作成します。 この例では、*eastus* の場所内に *myResourceGroup* という名前のリソース グループを作成します。

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

[az network vnet create](/cli/azure/network/vnet) を使用して仮想ネットワークを作成します。 この例では、*default* という名前のサブネットを使って、*myVirtualNetwork* という名前の既定の仮想ネットワークを作成します。

```azurecli-interactive
az network vnet create \
  --name myVirtualNetwork \
  --resource-group myResourceGroup \
  --subnet-name default
```

## <a name="create-virtual-machines"></a>仮想マシンを作成する

仮想ネットワーク内に 2 つの VM を作成します。

### <a name="create-the-first-vm"></a>最初の VM を作成する

[az vm create](/cli/azure/vm) を使用して VM を作成します。 既定のキーの場所にまだ SSH キーが存在しない場合は、コマンドを使って SSH キーを作成します。 特定のキーのセットを使用するには、`--ssh-key-value` オプションを使用します。 `--no-wait` オプションを使用すると、VM はバックグラウンドで作成されるため、次の手順に進むことができます。 この例では、*myVm1* という名前の VM を作成します。

```azurecli-interactive
az vm create \
  --resource-group myResourceGroup \
  --name myVm1 \
  --image UbuntuLTS \
  --generate-ssh-keys \
  --no-wait
```

### <a name="create-the-second-vm"></a>2 つ目の VM を作成する

前の手順で `--no-wait` オプションを使用したため、先に進んで *myVm2* という名前の 2 つ目の VM を作成できます。

```azurecli-interactive
az vm create \
  --resource-group myResourceGroup \
  --name myVm2 \
  --image UbuntuLTS \
  --generate-ssh-keys
```

### <a name="azure-cli-output-message"></a>Azure CLI の出力メッセージ

VM の作成には数分かかります。 Azure で VM が作成された後、Azure CLI では次のような出力が返されます。

```azurecli
{
  "fqdns": "",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVm2",
  "location": "eastus",
  "macAddress": "00-0D-3A-23-9A-49",
  "powerState": "VM running",
  "privateIpAddress": "10.0.0.5",
  "publicIpAddress": "40.68.254.142",
  "resourceGroup": "myResourceGroup"
  "zones": ""
}
```

**publicIpAddress** を書き留めておきます。 このアドレスは、次の手順でインターネットから VM に接続するために使用します。

## <a name="connect-to-a-vm-from-the-internet"></a>インターネットから VM に接続する

このコマンドでは、`<publicIpAddress>` を *myVm2* VM のパブリック IP アドレスに置き換えます。

```bash
ssh <publicIpAddress>
```

## <a name="communicate-between-vms"></a>VM 間の通信

*myVm2* VM と *myVm1* VM の間のプライベートな通信を確認するには、次のコマンドを入力します。

```bash
ping myVm1 -c 4
```

*10.0.0.4* から 4 つの応答を受信します。

*myVm2* VM との SSH セッションを終了します。

## <a name="clean-up-resources"></a>リソースのクリーンアップ

不要になったら、[az group delete](/cli/azure/group) を使用して、リソース グループとそのすべてのリソースを削除できます。

```azurecli-interactive
az group delete --name myResourceGroup --yes
```

## <a name="next-steps"></a>次の手順

このクイック スタートでは、既定の仮想ネットワークと 2 つの VM を作成しました。 インターネットから 1 つの VM に接続し、2 つの VM の間でプライベート通信を行いました。 仮想ネットワーク設定について詳しくは、[仮想ネットワークの管理](manage-virtual-network.md)に関する記事をご覧ください。

Azure では、VM 間で無制限のプライベート通信を実行できます。 既定で、Azure では、インターネットから Windows VM への受信リモート デスクトップ接続のみを許可しています。 さまざまな種類の VM ネットワーク通信の構成の詳細については、「[ネットワーク トラフィックをフィルター処理する](tutorial-filter-network-traffic.md)」チュートリアルを参照してください。
