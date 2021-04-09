---
title: VM ネットワーク ルーティングの問題を診断する - Azure CLI
titleSuffix: Azure Network Watcher
description: この記事では、Azure CLI を使用して、Azure Network Watcher の次ホップ機能を使用して仮想マシン ネットワークのルーティングの問題を診断する方法について説明します。
services: network-watcher
documentationcenter: network-watcher
author: damendo
editor: ''
tags: azure-resource-manager
Customer intent: I need to diagnose virtual machine (VM) network routing problem that prevents communication to different destinations.
ms.assetid: ''
ms.service: network-watcher
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: network-watcher
ms.workload: infrastructure
ms.date: 01/07/2021
ms.author: damendo
ms.custom: ''
ms.openlocfilehash: 5dac16e5cc1e88d833bf6d3c2660570fcf8b8e9e
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102216957"
---
# <a name="diagnose-a-virtual-machine-network-routing-problem---azure-cli"></a>仮想マシン ネットワークのルーティングの問題を診断する - Azure CLI

この記事では、仮想マシン (VM) をデプロイし、IP アドレスおよび URL との通信を確認します。 通信障害の原因と解決方法を特定します。

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

- この記事では、Azure CLI のバージョン 2.0 以降が必要です。 Azure Cloud Shell を使用している場合は、最新バージョンが既にインストールされています。 

- この記事の Azure CLI コマンドは、Bash シェルで実行されることを想定して書式設定されています。

## <a name="create-a-vm"></a>VM の作成

VM を作成する前に、その VM を含めるリソース グループを作成する必要があります。 [az group create](/cli/azure/group#az-group-create) を使用して、リソース グループを作成します。 次の例では、*myResourceGroup* という名前のリソース グループを *eastus* に作成します。

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

[az vm create](/cli/azure/vm#az-vm-create) を使用して VM を作成します。 既定のキーの場所にまだ SSH キーが存在しない場合は、コマンドを使って SSH キーを作成します。 特定のキーのセットを使用するには、`--ssh-key-value` オプションを使用します。 次の例では、*myVm* という名前の VM を作成します。

```azurecli-interactive
az vm create \
  --resource-group myResourceGroup \
  --name myVm \
  --image UbuntuLTS \
  --generate-ssh-keys
```

VM の作成には数分かかります。 VM が作成されて、Azure CLI から出力が返されるまでは、次の手順に進まないでください。

## <a name="test-network-communication"></a>ネットワーク通信をテストする

Network Watcher を使用してネットワーク通信をテストするには、最初にテスト対象の VM が含まれるリージョンにおいて Network Watcher を有効にしてから、Network Watcher の次ホップ機能を使用して通信をテストする必要があります。

### <a name="enable-network-watcher"></a>ネットワーク ウォッチャーを有効にする

米国東部リージョンで既にネットワーク ウォッチャーを有効にしている場合は、「[次ホップの使用](#use-next-hop)」に進んでください。 [az network watcher configure](/cli/azure/network/watcher#az-network-watcher-configure) コマンドを使用して、米国東部リージョンにネットワーク ウォッチャーを作成します。

```azurecli-interactive
az network watcher configure \
  --resource-group NetworkWatcherRG \
  --locations eastus \
  --enabled
```

### <a name="use-next-hop"></a>次ホップの使用

Azure では、既定の接続先へのルートを自動的に作成します。 既定のルートをオーバーライドするカスタム ルートを作成できます。 場合によっては、カスタム ルートが通信の失敗の原因になることがあります。 VM からのルーティングをテストするには、[az network watcher show-next-hop](/cli/azure/network/watcher#az-network-watcher-show-next-hop) を使用して、トラフィックが特定のアドレスに送信されるときの次のルーティング ホップを確認します。

VM から www.bing.com のいずれかの IP アドレスへの送信通信をテストします。

```azurecli-interactive
az network watcher show-next-hop \
  --dest-ip 13.107.21.200 \
  --resource-group myResourceGroup \
  --source-ip 10.0.0.4 \
  --vm myVm \
  --nic myVmVMNic \
  --out table
```

数秒後、 **[nextHopType]** が **[インターネット]** であり、 **[routeTableId]** が **[System Route]\(システム ルート\)** であることが出力からわかります。 この結果から、接続先に対して有効なルートが存在することがわかります。

VM から 172.31.0.100 への送信通信をテストします。

```azurecli-interactive
az network watcher show-next-hop \
  --dest-ip 172.31.0.100 \
  --resource-group myResourceGroup \
  --source-ip 10.0.0.4 \
  --vm myVm \
  --nic myVmVMNic \
  --out table
```

返される出力は、 **[nextHopType]** が **[なし]** であり、 **[routeTableId]** が **[System Route]\(システム ルート\)** であることを示しています。 この結果から、送信先への有効なシステム ルートはあるものの、接続先にトラフィックをルーティングする次ホップはないことがわかります。

## <a name="view-details-of-a-route"></a>ルートの詳細の表示

さらに詳細にルーティングを分析するには、[az network nic show-effective-route-table](/cli/azure/network/nic#az-network-nic-show-effective-route-table) コマンドを使用して、ネットワーク インターフェイスの有効なルートを確認します。

```azurecli-interactive
az network nic show-effective-route-table \
  --resource-group myResourceGroup \
  --name myVmVMNic
```

返される出力には、次のテキストが含まれます。

```console
{
  "additionalProperties": {
    "disableBgpRoutePropagation": false
  },
  "addressPrefix": [
    "0.0.0.0/0"
  ],
  "name": null,
  "nextHopIpAddress": [],
  "nextHopType": "Internet",
  "source": "Default",
  "state": "Active"
},
```

「[次ホップの使用](#use-next-hop)」で、13.107.21.200 への送信通信のテストに `az network watcher show-next-hop` コマンドを使用したときは、出力の他のルートにはそのアドレスが含まれていないため、**addressPrefix** が 0.0.0.0/0** のルートがアドレスへのトラフィックのルーティングに使用されました。 既定では、他のルートのアドレス プレフィックス内で指定されていないすべてのアドレスが、インターネットにルーティングされます。

ただし、172.31.0.100 への送信通信のテストに `az network watcher show-next-hop` コマンドを使用したときの結果は、次ホップの種類がないことを示しています。 返される出力には、次のテキストも含まれます。

```console
{
  "additionalProperties": {
    "disableBgpRoutePropagation": false
      },
  "addressPrefix": [
    "172.16.0.0/12"
  ],
  "name": null,
  "nextHopIpAddress": [],
  "nextHopType": "None",
  "source": "Default",
  "state": "Active"
},
```

`az network watcher nic show-effective-route-table` コマンドの出力でもわかるように、アドレス 172.31.0.100 を含む 172.16.0.0/12 プレフィックスへの既定のルートがあるにもかかわらず、 **[nextHopType]** は **[なし]** になっています。 Azure では、172.16.0.0/12 への既定のルートを作成しますが、理由がない限り次ホップの種類は指定しません。 たとえば、仮想ネットワークのアドレス空間に 172.16.0.0/12 アドレス範囲を追加した場合は、ルートの **[nextHopType]** を **[仮想ネットワーク]** に変更します。 **[nextHopType]** として **[仮想ネットワーク]** にチェック マークが表示されます。

## <a name="clean-up-resources"></a>リソースをクリーンアップする

不要になったら、[az group delete](/cli/azure/group#az-group-delete) を使用して、リソース グループとそのグループに含まれているすべてのリソースを削除できます。

```azurecli-interactive
az group delete --name myResourceGroup --yes
```

## <a name="next-steps"></a>次のステップ

この記事では、VM を作成し、VM からのネットワークのルーティングを診断しました。 Azure では複数の既定のルートが作成されることを学習し、2 つの異なる送信先へのルーティングをテストしました。 詳細については、[Azure でのルーティング](../virtual-network/virtual-networks-udr-overview.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json)と[カスタム ルートを作成する](../virtual-network/manage-route-table.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json#create-a-route)方法に関するページを参照してください。

送信 VM 接続の場合は、Network Watcher の[接続のトラブルシューティング](network-watcher-connectivity-cli.md)機能を使用することにより、待機時間に加えて、VM とエンドポイント間で許可/拒否されているネットワーク トラフィックを確認することもできます。 Network Watcher 接続監視機能を使用すると、VM とエンドポイント (IP アドレスや URL など) の間の通信を継時的に監視することができます。 方法については、[ネットワーク接続の監視](connection-monitor.md)に関するページを参照してください。
