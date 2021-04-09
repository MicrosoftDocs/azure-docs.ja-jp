---
title: ネットワーク トラフィックのフィルター処理 - Azure CLI | Microsoft Docs
description: この記事では、ネットワーク セキュリティ グループと Azure CLI を使用して、サブネットに対するネットワーク トラフィックをフィルター処理する方法について説明します。
services: virtual-network
documentationcenter: virtual-network
author: KumudD
manager: twooley
editor: ''
tags: azure-resource-manager
Customer intent: I want to filter network traffic to virtual machines that perform similar functions, such as web servers.
ms.assetid: ''
ms.service: virtual-network
ms.devlang: azurecli
ms.topic: how-to
ms.tgt_pltfrm: virtual-network
ms.workload: infrastructure
ms.date: 03/30/2018
ms.author: kumud
ms.custom: devx-track-azurecli
ms.openlocfilehash: d2cabecfb2a70e6d325129335c066bb35aeb02ba
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "98221882"
---
# <a name="filter-network-traffic-with-a-network-security-group-using-the-azure-cli"></a>ネットワーク セキュリティ グループと Azure CLI を使用してネットワーク トラフィックをフィルター処理する

ネットワーク セキュリティ グループを使用して、仮想ネットワーク サブネットとの間で送受信されるネットワーク トラフィックをフィルター処理できます。 ネットワーク セキュリティ グループには、IP アドレス、ポート、およびプロトコルでネットワーク トラフィックをフィルター処理するセキュリティ規則が含まれています。 セキュリティ規則は、サブネットに展開されたリソースに適用されます。 この記事では、次の方法について説明します。

* ネットワーク セキュリティ グループと規則を作成する
* 仮想ネットワークを作成し、ネットワーク セキュリティ グループをサブネットに関連付ける
* 仮想マシン (VM) をサブネットに展開する
* トラフィック フィルターをテストする

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

- この記事では、Azure CLI のバージョン 2.0.28 以降が必要です。 Azure Cloud Shell を使用している場合は、最新バージョンが既にインストールされています。

## <a name="create-a-network-security-group"></a>ネットワーク セキュリティ グループの作成

ネットワーク セキュリティ グループにはセキュリティ規則が含まれています。 セキュリティ規則には、送信元と送信先が指定されています。 送信元と送信先にはアプリケーション セキュリティ グループを指定できます。

### <a name="create-application-security-groups"></a>アプリケーション セキュリティ グループを作成する

まず [az group create](/cli/azure/group) を使用して、この記事で作成したすべてのリソースのリソース グループを作成します。 次の例では、*eastus* の場所にリソース グループを作成します。 

```azurecli-interactive
az group create \
  --name myResourceGroup \
  --location eastus
```

[az network asg create](/cli/azure/network/asg) で、アプリケーション セキュリティ グループを作成します。 アプリケーション セキュリティ グループを使用すると、同様のポート フィルター処理要件を持つサーバーをグループ化できます。 次の例では、2 つのアプリケーション セキュリティ グループを作成します。

```azurecli-interactive
az network asg create \
  --resource-group myResourceGroup \
  --name myAsgWebServers \
  --location eastus

az network asg create \
  --resource-group myResourceGroup \
  --name myAsgMgmtServers \
  --location eastus
```

### <a name="create-a-network-security-group"></a>ネットワーク セキュリティ グループの作成

[az network nsg create](/cli/azure/network/nsg) で、ネットワーク セキュリティ グループを作成します。 次の例では、*myNsg* という名前のネットワーク セキュリティ グループを作成します。 

```azurecli-interactive 
# Create a network security group
az network nsg create \
  --resource-group myResourceGroup \
  --name myNsg
```

### <a name="create-security-rules"></a>セキュリティ規則を作成する

[az network nsg rule create](/cli/azure/network/nsg/rule) でセキュリティ規則を作成します。 次の例では、インターネットから *myWebServers* アプリケーション セキュリティ グループへの、ポート 80 および 443 経由の受信トラフィックを許可する規則を作成します。

```azurecli-interactive
az network nsg rule create \
  --resource-group myResourceGroup \
  --nsg-name myNsg \
  --name Allow-Web-All \
  --access Allow \
  --protocol Tcp \
  --direction Inbound \
  --priority 100 \
  --source-address-prefix Internet \
  --source-port-range "*" \
  --destination-asgs "myAsgWebServers" \
  --destination-port-range 80 443
```

次の例では、インターネットから *myMgmtServers* アプリケーション セキュリティ グループへの、ポート 22 経由の受信トラフィックを許可する規則を作成します。

```azurecli-interactive
az network nsg rule create \
  --resource-group myResourceGroup \
  --nsg-name myNsg \
  --name Allow-SSH-All \
  --access Allow \
  --protocol Tcp \
  --direction Inbound \
  --priority 110 \
  --source-address-prefix Internet \
  --source-port-range "*" \
  --destination-asgs "myAsgMgmtServers" \
  --destination-port-range 22
```

この記事では、*myAsgMgmtServers* VM に対して SSH (ポート 22) がインターネットに公開されています。 運用環境では、ポート 22 をインターネットに公開せずに、[VPN](../vpn-gateway/vpn-gateway-about-vpngateways.md?toc=%2fazure%2fvirtual-network%2ftoc.json) または[プライベート](../expressroute/expressroute-introduction.md?toc=%2fazure%2fvirtual-network%2ftoc.json) ネットワーク接続を使用して、管理する Azure リソースに接続することをお勧めします。

## <a name="create-a-virtual-network"></a>仮想ネットワークの作成

[az network vnet create](/cli/azure/network/vnet) を使用して仮想ネットワークを作成します。 次の例では、*myVirtualNetwork* という名前の仮想ネットワークを作成します。

```azurecli-interactive 
az network vnet create \
  --name myVirtualNetwork \
  --resource-group myResourceGroup \
  --address-prefixes 10.0.0.0/16
```

[az network vnet subnet create](/cli/azure/network/vnet/subnet) で、仮想ネットワークにサブネットを追加します。 次の例では、*mySubnet* という名前のサブネットを仮想ネットワークに追加し、それに *myNsg* ネットワーク セキュリティ グループを関連付けます。

```azurecli-interactive
az network vnet subnet create \
  --vnet-name myVirtualNetwork \
  --resource-group myResourceGroup \
  --name mySubnet \
  --address-prefix 10.0.0.0/24 \
  --network-security-group myNsg
```

## <a name="create-virtual-machines"></a>仮想マシンを作成する

仮想ネットワークに 2 つの VM を作成して、後でトラフィックのフィルター処理を検証できるようにします。 

[az vm create](/cli/azure/vm) を使用して VM を作成します。 次の例では、Web サーバーとして機能する VM を作成します。 `--asgs myAsgWebServers` オプションを指定すると、VM 用に作成するネットワーク インターフェイスが、*myAsgWebServers* アプリケーション セキュリティ グループのメンバーになります。

`--nsg ""` オプションを指定すると、VM の作成時に、作成するネットワーク インターフェイス用に既定のネットワーク セキュリティ グループが作成されるのを回避します。 この記事を効率化するために、パスワードが使用されています。 通常、キーは運用環境デプロイで使用されます。 キーを使用する場合は、残りの手順を実行するために、SSH エージェント転送も構成する必要があります。 詳細については、SSH クライアントのドキュメントを参照してください。 次のコマンドの `<replace-with-your-password>` を、使用するパスワードに置き換えます。

```azurecli-interactive
adminPassword="<replace-with-your-password>"

az vm create \
  --resource-group myResourceGroup \
  --name myVmWeb \
  --image UbuntuLTS \
  --vnet-name myVirtualNetwork \
  --subnet mySubnet \
  --nsg "" \
  --asgs myAsgWebServers \
  --admin-username azureuser \
  --admin-password $adminPassword
```

VM の作成には数分かかります。 VM が作成されると、次の例のような出力が返されます。 

```output
{
  "fqdns": "",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVmWeb",
  "location": "eastus",
  "macAddress": "00-0D-3A-23-9A-49",
  "powerState": "VM running",
  "privateIpAddress": "10.0.0.4",
  "publicIpAddress": "13.90.242.231",
  "resourceGroup": "myResourceGroup"
}
```

**publicIpAddress** を書き留めておきます。 このアドレスは、後の手順で、インターネットから VM にアクセスするときに使います。  管理サーバーとして機能する VM を作成します。

```azurecli-interactive
az vm create \
  --resource-group myResourceGroup \
  --name myVmMgmt \
  --image UbuntuLTS \
  --vnet-name myVirtualNetwork \
  --subnet mySubnet \
  --nsg "" \
  --asgs myAsgMgmtServers \
  --admin-username azureuser \
  --admin-password $adminPassword
```

VM の作成には数分かかります。 VM が作成されたら、返された出力の **publicIpAddress** を書き留めておきます。 このアドレスは、次の手順で VM にアクセスするために使います。 Azure で VM の作成が完了するまで、次の手順に進まないでください。

## <a name="test-traffic-filters"></a>トラフィック フィルターをテストする

次のコマンドを使って、*myVmMgmt* VM との SSH セッションを作成します。 *\<publicIpAddress>* を VM のパブリック IP アドレスに置き換えます。 上の例では、IP アドレスは *13.90.242.231* です。

```bash 
ssh azureuser@<publicIpAddress>
```

パスワードの入力を求められたら、「[仮想マシンを作成する](#create-virtual-machines)」で入力したパスワードを入力します。

この接続は成功します。なぜなら、*myVmMgmt* VM に接続されているネットワーク インターフェイスを含む *myAsgMgmtServers* アプリケーション セキュリティ グループへのインターネットからの受信トラフィックが、ポート 22 で許可されているためです。

次のコマンドを使用して、*myVmMgmt* VM から *myVmWeb* VM へ SSH 接続します。

```bash 
ssh azureuser@myVmWeb
```

各ネットワーク セキュリティ グループ内の既定のセキュリティ規則では、仮想ネットワーク内のすべての IP アドレス間で、すべてのポートを介したトラフィックが許可されるため、この接続は成功します。 *myAsgWebServers* のセキュリティ規則は、インターネットからのポート 22 を介した受信を許可しないため、インターネットから *myVmWeb* VM に SSH 接続することはできません。

次のコマンドを使用して、*myVmWeb* VM に nginx Web サーバーをインストールします。

```bash 
# Update package source
sudo apt-get -y update

# Install NGINX
sudo apt-get -y install nginx
```

*myVmWeb* VM は、インターネットに送信して nginx を取得することを許可されます。なぜなら、既定のセキュリティ規則では、インターネットへの送信トラフィックがすべて許可されるためです。 *myVmWeb* SSH セッションを終了すると、*myVmMgmt* VM の `username@myVmMgmt:~$` プロンプトに戻ります。 *myVmWeb* VM から nginx のようこそ画面を取得するには、次のコマンドを入力します。

```bash
curl myVmWeb
```

*myVmMgmt* VM からログアウトします。 Azure の外部から *myVmWeb* Web サーバーにアクセスできることを確認するには、自身のコンピューターから `curl <publicIpAddress>` と入力します。 この接続は成功します。なぜなら、*myVmWeb* VM に接続されているネットワーク インターフェイスを含む *myAsgWebServers* アプリケーション セキュリティ グループへのインターネットからの受信トラフィックが、ポート 80 で許可されているためです。

## <a name="clean-up-resources"></a>リソースをクリーンアップする

不要になったら、[az group delete](/cli/azure/group) を使用して、リソース グループとそのグループに含まれているすべてのリソースを削除します。

```azurecli-interactive
az group delete --name myResourceGroup --yes
```

## <a name="next-steps"></a>次のステップ

この記事では、ネットワーク セキュリティ グループを作成し、それを仮想ネットワーク サブネットに関連付けました。 ネットワーク セキュリティ グループについて詳しくは、[ネットワーク セキュリティ グループの概要](./network-security-groups-overview.md)と[ネットワーク セキュリティ グループの管理](manage-network-security-group.md)に関する記事を参照してください。

Azure の既定では、サブネット間でトラフィックがルーティングされます。 代わりに、たとえばファイアウォールとして機能する VM を介してサブネット間でトラフィックをルーティングすることもできます。 その方法については、[ルート テーブルの作成](tutorial-create-route-table-cli.md)に関する記事を参照してください。