---
title: クイック スタート:VM ネットワーク トラフィック フィルターの問題を診断する - Azure CLI
titleSuffix: Azure Network Watcher
description: Azure Network Watcher の IP フローの確認機能を使って、仮想マシンのネットワーク トラフィック フィルターの問題を診断するために、Azure CLI を使用する方法について学習します。
services: network-watcher
documentationcenter: network-watcher
author: KumudD
manager: twooley
editor: ''
tags: azure-resource-manager
Customer intent: I need to diagnose a virtual machine (VM) network traffic filter problem that prevents communication to and from a VM.
ms.assetid: ''
ms.service: network-watcher
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: network-watcher
ms.workload: infrastructure
ms.date: 01/07/2021
ms.author: kumud
ms.custom: mvc, devx-track-azurecli
ms.openlocfilehash: 536a9e304d111b16e39c3187560a7a529d05c937
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "98013154"
---
# <a name="quickstart-diagnose-a-virtual-machine-network-traffic-filter-problem---azure-cli"></a>クイック スタート:仮想マシン ネットワーク トラフィック フィルターの問題を診断する - Azure CLI

このクイック スタートでは、仮想マシン (VM) を展開してから、IP アドレスと URL への通信および IP アドレスからの通信をチェックします。 通信障害の原因と解決方法を特定します。

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

- このクイックスタートには、Azure CLI のバージョン 2.0 以降が必要です。 Azure Cloud Shell を使用している場合は、最新バージョンが既にインストールされています。 

- このクイックスタートの Azure CLI コマンドは、Bash シェルで実行するように書式設定されています。

## <a name="create-a-vm"></a>VM の作成

VM を作成する前に、その VM を含めるリソース グループを作成する必要があります。 [az group create](/cli/azure/group) を使用して、リソース グループを作成します。 次の例では、*myResourceGroup* という名前のリソース グループを *eastus* に作成します。

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

[az vm create](/cli/azure/vm) を使用して VM を作成します。 既定のキーの場所にまだ SSH キーが存在しない場合は、コマンドを使って SSH キーを作成します。 特定のキーのセットを使用するには、`--ssh-key-value` オプションを使用します。 次の例では、*myVm* という名前の VM を作成します。

```azurecli-interactive
az vm create \
  --resource-group myResourceGroup \
  --name myVm \
  --image UbuntuLTS \
  --generate-ssh-keys
```

VM の作成には数分かかります。 VM が作成されて、Azure CLI から出力が返されるまでは、次の手順に進まないでください。

## <a name="test-network-communication"></a>ネットワーク通信をテストする

Network Watcher を使ってネットワーク通信をテストするには、最初にテスト対象の VM が含まれるリージョンにおいて Network Watcher を有効にしてから、Network Watcher の IP フローの確認機能を使って通信をテストする必要があります。

### <a name="enable-network-watcher"></a>ネットワーク ウォッチャーを有効にする

米国東部リージョンで既に Network Watcher を有効にしている場合は、「[IP フローの確認を使用する](#use-ip-flow-verify)」に進んでください。 [az network watcher configure](/cli/azure/network/watcher#az-network-watcher-configure) コマンドを使用して、米国東部リージョンにネットワーク ウォッチャーを作成します。

```azurecli-interactive
az network watcher configure \
  --resource-group NetworkWatcherRG \
  --locations eastus \
  --enabled
```

### <a name="use-ip-flow-verify"></a>IP フローの確認を使用する

VM を作成すると、Azure は既定に従って、VM との間でやり取りされるネットワーク トラフィックを許可および拒否します。 後で Azure の既定値をオーバーライドして、他の種類のトラフィックを許可または拒否する場合があります。 さまざまな送信先へのトラフィックまたは送信元 IP アドレスからのトラフィックが許可または拒否されるかどうかをテストするには、[az network watcher test-ip-flow](/cli/azure/network/watcher#az-network-watcher-test-ip-flow) コマンドを使います。

VM から www.bing.com のいずれかの IP アドレスへの送信通信をテストします。

```azurecli-interactive
az network watcher test-ip-flow \
  --direction outbound \
  --local 10.0.0.4:60000 \
  --protocol TCP \
  --remote 13.107.21.200:80 \
  --vm myVm \
  --nic myVmVMNic \
  --resource-group myResourceGroup \
  --out table
```

数秒後に、**AllowInternetOutbound** という名前のセキュリティ規則によってアクセスが許可されていることを示す結果が返されます。

VM から 172.31.0.100 への送信通信をテストします。

```azurecli-interactive
az network watcher test-ip-flow \
  --direction outbound \
  --local 10.0.0.4:60000 \
  --protocol TCP \
  --remote 172.31.0.100:80 \
  --vm myVm \
  --nic myVmVMNic \
  --resource-group myResourceGroup \
  --out table
```

返される結果では、**DefaultOutboundDenyAll** という名前のセキュリティ規則によってアクセスが拒否されることが示されています。

172.31.0.100 から VM への受信通信をテストします。

```azurecli-interactive
az network watcher test-ip-flow \
  --direction inbound \
  --local 10.0.0.4:80 \
  --protocol TCP \
  --remote 172.31.0.100:60000 \
  --vm myVm \
  --nic myVmVMNic \
  --resource-group myResourceGroup \
  --out table
```

返される結果では、**DefaultInboundDenyAll** という名前のセキュリティ規則のためにアクセスが拒否されることが示されています。 VM へのトラフィックまたは VM からのトラフィックを許可または拒否しているセキュリティ規則がわかったので、問題を解決する方法を決定できます。

## <a name="view-details-of-a-security-rule"></a>セキュリティ規則の詳細を表示する

「[IP フローの確認を使用する](#use-ip-flow-verify)」の規則が通信を許可または拒否している理由を特定するには、[az network nic list-effective-nsg](/cli/azure/network/nic#az-network-nic-list-effective-nsg) コマンドを使って、ネットワーク インターフェイスに対して有効なセキュリティ規則を確認します。

```azurecli-interactive
az network nic list-effective-nsg \
  --resource-group myResourceGroup \
  --name myVmVMNic
```

返される出力には、前の「[IP フローの確認を使用する](#use-ip-flow-verify)」の手順で www.bing.com への送信アクセスを許可した **AllowInternetOutbound** 規則に関する次のテキストが含まれます。

```console
{
 "access": "Allow",
 "additionalProperties": {},
 "destinationAddressPrefix": "Internet",
 "destinationAddressPrefixes": [
  "Internet"
 ],
 "destinationPortRange": "0-65535",
 "destinationPortRanges": [
  "0-65535"
 ],
 "direction": "Outbound",
 "expandedDestinationAddressPrefix": [
  "1.0.0.0/8",
  "2.0.0.0/7",
  "4.0.0.0/6",
  "8.0.0.0/7",
  "11.0.0.0/8",
  "12.0.0.0/6",
  ...
 ],
 "expandedSourceAddressPrefix": null,
 "name": "defaultSecurityRules/AllowInternetOutBound",
 "priority": 65001,
 "protocol": "All",
 "sourceAddressPrefix": "0.0.0.0/0",
 "sourceAddressPrefixes": [
  "0.0.0.0/0"
 ],
 "sourcePortRange": "0-65535",
 "sourcePortRanges": [
  "0-65535"
 ]
},
```

前の出力では、**destinationAddressPrefix** が **Internet** であることがわかります。 ただし、13.107.21.200 が **Internet** にどのように関係しているのかはっきりしません。 **expandedDestinationAddressPrefix** には複数のアドレス プレフィックスが列記されています。 リストのプレフィックスの 1 つは **12.0.0.0/6** であり、これは 12.0.0.1-15.255.255.254 の IP アドレス範囲を包含します。 13.107.21.200 はそのアドレス範囲内にあるため、**AllowInternetOutBound** 規則は送信トラフィックを許可します。 さらに、前の出力で示されている規則の中には、この規則をオーバーライドする優先順位の高い (小さい値の) 規則はありません。 ある IP アドレスへの送信通信を拒否するには、その IP アドレスへのポート 80 での送信を拒否する、優先順位の高いセキュリティ規則を追加します。

「[IP フローの確認を使用する](#use-ip-flow-verify)」で `az network watcher test-ip-flow` コマンドを実行して 172.131.0.100 への送信通信をテストしたとき、出力では **DefaultOutboundDenyAll** 規則が通信を拒否したことが示されました. **DefaultOutboundDenyAll** 規則は、`az network nic list-effective-nsg` コマンドからの次の出力で示されている **DenyAllOutBound** 規則と同じです。

```console
{
 "access": "Deny",
 "additionalProperties": {},
 "destinationAddressPrefix": "0.0.0.0/0",
 "destinationAddressPrefixes": [
  "0.0.0.0/0"
 ],
 "destinationPortRange": "0-65535",
 "destinationPortRanges": [
  "0-65535"
 ],
 "direction": "Outbound",
 "expandedDestinationAddressPrefix": null,
 "expandedSourceAddressPrefix": null,
 "name": "defaultSecurityRules/DenyAllOutBound",
 "priority": 65500,
 "protocol": "All",
 "sourceAddressPrefix": "0.0.0.0/0",
 "sourceAddressPrefixes": [
  "0.0.0.0/0"
 ],
 "sourcePortRange": "0-65535",
 "sourcePortRanges": [
  "0-65535"
 ]
}
```

ルールでは、**destinationAddressPrefix** として **0.0.0.0/0** が含まれます。 172.131.0.100 は `az network nic list-effective-nsg` コマンドからの出力の他のどの送信規則の **destinationAddressPrefix** にも含まれないので、規則はこのアドレスへの送信通信を拒否します。 この送信通信を許可するには、172.131.0.100 のポート 80 への送信トラフィックを許可する、優先順位の高いセキュリティ規則を追加します。

「[IP フローの確認を使用する](#use-ip-flow-verify)」で `az network watcher test-ip-flow` コマンドを実行して 172.131.0.100 からの受信通信をテストしたとき、出力では **DefaultInboundDenyAll** 規則が通信を拒否したことが示されました. **DefaultInboundDenyAll** 規則は、`az network nic list-effective-nsg` コマンドからの次の出力で示されている **DenyAllInBound** 規則と同じです。

```console
{
 "access": "Deny",
 "additionalProperties": {},
 "destinationAddressPrefix": "0.0.0.0/0",
 "destinationAddressPrefixes": [
  "0.0.0.0/0"
 ],
 "destinationPortRange": "0-65535",
 "destinationPortRanges": [
  "0-65535"
 ],
 "direction": "Inbound",
 "expandedDestinationAddressPrefix": null,
 "expandedSourceAddressPrefix": null,
 "name": "defaultSecurityRules/DenyAllInBound",
 "priority": 65500,
 "protocol": "All",
 "sourceAddressPrefix": "0.0.0.0/0",
 "sourceAddressPrefixes": [
  "0.0.0.0/0"
 ],
 "sourcePortRange": "0-65535",
 "sourcePortRanges": [
  "0-65535"
 ]
},
```

出力で示されているように、`az network nic list-effective-nsg` コマンドからの出力には、172.131.0.100 から VM へのポート 80 での受信を許可する優先順位の高い規則が他に存在しないため、**DenyAllInBound** 規則が適用されます。 この受信通信を許可するには、ポート 80 での 172.131.0.100 からの受信を許可する優先順位の高いセキュリティ規則を追加します。

このクイック スタートのチェックでは、Azure の構成をテストしました。 チェックから予想どおりの結果が返ったにもかかわらず、まだネットワークの問題がある場合は、VM と通信対象のエンドポイントとの間にファイアウォールが存在しないこと、および VM のオペレーティング システムに通信を許可または拒否するファイアウォールが含まれないことを確認します。

## <a name="clean-up-resources"></a>リソースをクリーンアップする

不要になったら、[az group delete](/cli/azure/group) を使用して、リソース グループとそのグループに含まれているすべてのリソースを削除できます。

```azurecli-interactive
az group delete --name myResourceGroup --yes
```

## <a name="next-steps"></a>次のステップ

このクイック スタートでは、VM を作成し、受信および送信ネットワーク トラフィック フィルターを診断しました。 ネットワーク セキュリティ グループの規則が、VM との間でやり取りされるトラフィックを許可または拒否することを学習しました。 [セキュリティ規則](../virtual-network/network-security-groups-overview.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json)および[セキュリティ規則を作成する](../virtual-network/manage-network-security-group.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json#create-a-security-rule)方法について、さらに詳しく学習してください。

適切なネットワーク トラフィック フィルターを適用してもまだ、ルーティングの構成のために VM への通信が失敗する可能性があります。 VM ネットワークのルーティングの問題を診断する方法については、「[VM のルーティングに関する問題を診断する](diagnose-vm-network-routing-problem-cli.md)」をご覧ください。送信ルーティング、待機時間、およびトラフィック フィルター処理の問題を 1 つのツールで診断する方法については、[接続のトラブルシューティング](network-watcher-connectivity-cli.md)に関するページをご覧ください。