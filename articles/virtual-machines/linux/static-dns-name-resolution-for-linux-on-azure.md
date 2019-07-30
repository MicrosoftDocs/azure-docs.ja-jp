---
title: Azure CLI で VM の名前解決に内部 DNS を使用する | Microsoft Docs
description: 仮想ネットワーク インターフェイス カードの作成方法および Azure CLI で Azure での VM の名前解決に内部 DNS を使用する方法
services: virtual-machines-linux
documentationcenter: ''
author: vlivech
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: azurecli
ms.topic: article
ms.date: 02/16/2017
ms.author: gwallace
ms.openlocfilehash: d53c4c2120701ca99d0865e2c074c85e629ae81c
ms.sourcegitcommit: de47a27defce58b10ef998e8991a2294175d2098
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/15/2019
ms.locfileid: "67875168"
---
# <a name="create-virtual-network-interface-cards-and-use-internal-dns-for-vm-name-resolution-on-azure"></a>仮想ネットワーク インターフェイス カードを作成して Azure での VM の名前解決に内部 DNS を使用する

この記事では、仮想ネットワーク インターフェイス カード (vNIC) と DNS ラベル名を Azure CLI と共に使用して Linux VM に静的な内部 DNS 名を設定する方法を説明します。 静的 DNS 名は、このドキュメントで使用している Jenkins ビルド サーバーや、Git サーバーなどの永続的なインフラストラクチャ サービスに使用されます。

要件は次のとおりです。

* [Azure アカウント](https://azure.microsoft.com/pricing/free-trial/)
* [SSH パブリック キー ファイルおよびプライベート キー ファイル](mac-create-ssh-keys.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

## <a name="quick-commands"></a>クイック コマンド
タスクをすばやく実行する必要がある場合のために、次のセクションでは、必要なコマンドの詳細について説明します。 詳細な情報と各手順のコンテキストが、ドキュメントの残りの部分に記載されています。[ここからお読みください](#detailed-walkthrough)。 これらの手順を実行するには、[Azure CLI](/cli/azure/install-az-cli2) の最新版をインストールし、[az login](/cli/azure/reference-index) を使用して Azure アカウントにログインする必要があります。

前提条件:リソース グループ、仮想ネットワークおよびサブネット、SSH 受信が設定されたネットワーク セキュリティ グループ。

### <a name="create-a-virtual-network-interface-card-with-a-static-internal-dns-name"></a>静的な内部 DNS 名を使用して仮想ネットワーク インターフェイス カードを作成する
[az network nic create](/cli/azure/network/nic) を使用して vNIC を作成します。 `--internal-dns-name` CLI フラグは DNS のラベルを設定するためのものです。このラベルにより、仮想ネットワーク インターフェイス カード (vNIC) の静的 DNS 名が提供されます。 次の例では、`myNic` という名前の vNIC を作成して `myVnet` 仮想ネットワークに接続し、`jenkins` という名前の内部 DNS 名レコードを作成します。

```azurecli
az network nic create \
    --resource-group myResourceGroup \
    --name myNic \
    --vnet-name myVnet \
    --subnet mySubnet \
    --internal-dns-name jenkins
```

### <a name="deploy-a-vm-and-connect-the-vnic"></a>VM をデプロイして vNIC を接続する
[az vm create](/cli/azure/vm) を使用して VM を作成します。 `--nics` フラグにより、Azure へのデプロイの際に vNIC が VM に接続されます。 次の例では、Azure Managed Disks を使用して `myVM` という名前の VM を作成し、前述の手順の `myNic` という名前の vNIC をアタッチします。

```azurecli
az vm create \
    --resource-group myResourceGroup \
    --name myVM \
    --nics myNic \
    --image UbuntuLTS \
    --admin-username azureuser \
    --ssh-key-value ~/.ssh/id_rsa.pub
```

## <a name="detailed-walkthrough"></a>詳細なチュートリアル

Azure の完全に継続的インテグレーションと継続的なデプロイ (CiCd) インフラストラクチャには、特定のサーバーを静的サーバーまたは有効期間の長いサーバーに設定する必要があります。 仮想ネットワークやネットワーク セキュリティ グループなどの Azure 資産は、ほとんどデプロイされることのない有効期間の長い静的リソースにすることをお勧めします。 いったん仮想ネットワークをデプロイしておくと、インフラストラクチャに悪影響を及ぼすことなく、新しいデプロイで再利用できます。 後で Git リポジトリ サーバーか Jenkins オートメーション サーバーをこの仮想ネットワークに追加することで、開発環境やテスト環境で CiCd を実現できます。  

内部 DNS 名では、Azure 仮想ネットワーク内でのみ解決可能です。 DNS 名は内部でのみ解決可能であり、インターネット外部では解決できないため、インフラストラクチャのセキュリティが強化されます。

次の例では、パラメーター名を独自の値を置き換えます。 `myResourceGroup`、`myNic`、`myVM` などは、例として使われているパラメーター名です。

## <a name="create-the-resource-group"></a>リソース グループの作成
最初に、[az group create](/cli/azure/group) でリソース グループを作成します。 次の例では、`myResourceGroup` という名前のリソース グループを `westus` の場所に作成します。

```azurecli
az group create --name myResourceGroup --location westus
```

## <a name="create-the-virtual-network"></a>仮想ネットワークの作成

次に、VM をデプロイする仮想ネットワークを構築します。 このチュートリアルでは、仮想ネットワークにサブネットを 1 つ含めます。 Azure 仮想ネットワークの詳細については、[仮想ネットワークの作成](../../virtual-network/manage-virtual-network.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json#create-a-virtual-network)に関するページを参照してください。 

[az network vnet create](/cli/azure/network/vnet) で仮想ネットワークを作成します。 次の例では、`myVnet` という名前の仮想ネットワークと `mySubnet` という名前のサブネットを作成します。

```azurecli
az network vnet create \
    --resource-group myResourceGroup \
    --name myVnet \
    --address-prefix 192.168.0.0/16 \
    --subnet-name mySubnet \
    --subnet-prefix 192.168.1.0/24
```

## <a name="create-the-network-security-group"></a>ネットワーク セキュリティ グループを作成する
Azure ネットワーク セキュリティ グループは、ネットワーク層のファイアウォールに相当します。 ネットワーク セキュリティ グループの詳細については、[Azure CLI での NSG の作成方法](../../virtual-network/tutorial-filter-network-traffic-cli.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)に関するページを参照してください。 

[az network nsg create](/cli/azure/network/nsg) で、ネットワーク セキュリティ グループを作成します。 次の例では、`myNetworkSecurityGroup` という名前のネットワーク セキュリティ グループを作成します。

```azurecli
az network nsg create \
    --resource-group myResourceGroup \
    --name myNetworkSecurityGroup
```

## <a name="add-an-inbound-rule-to-allow-ssh"></a>SSH を許可する受信規則を追加する
[az network nsg rule create](/cli/azure/network/nsg/rule) で、ネットワーク セキュリティ グループの受信規則を 1 つ追加します。 次の例では、`myRuleAllowSSH` という名前の規則を作成します。

```azurecli
az network nsg rule create \
    --resource-group myResourceGroup \
    --nsg-name myNetworkSecurityGroup \
    --name myRuleAllowSSH \
    --protocol tcp \
    --direction inbound \
    --priority 1000 \
    --source-address-prefix '*' \
    --source-port-range '*' \
    --destination-address-prefix '*' \
    --destination-port-range 22 \
    --access allow
```

## <a name="associate-the-subnet-with-the-network-security-group"></a>サブネットをネットワーク セキュリティ グループに関連付ける
サブネットをネットワーク セキュリティ グループに関連付けるには、[az network vnet subnet update](/cli/azure/network/vnet/subnet) を使用します。 次の例では、`mySubnet` という名前のサブネットを `myNetworkSecurityGroup` という名前のネットワーク セキュリティ グループに関連付けます。

```azurecli
az network vnet subnet update \
    --resource-group myResourceGroup \
    --vnet-name myVnet \
    --name mySubnet \
    --network-security-group myNetworkSecurityGroup
```


## <a name="create-the-virtual-network-interface-card-and-static-dns-names"></a>仮想ネットワーク インターフェイス カードと静的 DNS 名を作成する
Azure には非常に高い柔軟性が備わっていますが、VM の名前解決に DNS 名を使用するには、DNS ラベルを含む仮想ネットワーク インターフェイス カード (vNIC) を作成する必要があります。 vNIC は、インフラストラクチャのサイクルを通じて異なる VM に接続することで再利用できるため有用です。 このアプローチにより、VM を一時的なものにしたまま vNIC を静的リソースとして保持できます。 vNIC で DNS ラベル付けを使用すると、VNet 内の他の VM からの名前解決を単純化することができます。 解決可能な名前を使用すると、DNS 名 `Jenkins` または Git サーバー `gitrepo` を使って、他の VM がオートメーション サーバーにアクセスできます。  

[az network nic create](/cli/azure/network/nic) を使用して vNIC を作成します。 次の例では、`myNic` という名前の vNIC を作成して `myVnet` と呼ばれる `myVnet` 仮想ネットワークに接続し、`jenkins` と言う名前の内部 DNS 名レコードを作成します。

```azurecli
az network nic create \
    --resource-group myResourceGroup \
    --name myNic \
    --vnet-name myVnet \
    --subnet mySubnet \
    --internal-dns-name jenkins
```

## <a name="deploy-the-vm-into-the-virtual-network-infrastructure"></a>仮想ネットワーク インフラストラクチャに VM をデプロイ
仮想ネットワークとサブネット、SSH 用のポート 22 を除くすべての受信トラフィックをブロックしてサブネットを保護するファイアウォールとして機能するネットワーク セキュリティ グループ、および vNIC の用意ができました。 これで、この既存のネットワーク インフラストラクチャ内に VM をデプロイできます。

[az vm create](/cli/azure/vm) を使用して VM を作成します。 次の例では、Azure Managed Disks を使用して `myVM` という名前の VM を作成し、前述の手順の `myNic` という名前の vNIC をアタッチします。

```azurecli
az vm create \
    --resource-group myResourceGroup \
    --name myVM \
    --nics myNic \
    --image UbuntuLTS \
    --admin-username azureuser \
    --ssh-key-value ~/.ssh/id_rsa.pub
```

CLI フラグを使用して既存のリソースを呼び出すことで、既存のネットワーク内に VM をデプロイするよう Azure に指示します。 繰り返しますが、VNet とサブネットをデプロイしたら、Azure リージョン内でこれらを静的または永続的なリソースにしておくことができます。  

## <a name="next-steps"></a>次の手順
* [Azure CLI コマンドを直接使用して Linux VM 用の独自のカスタム環境を作成する](create-cli-complete.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [テンプレートを使用して Azure に Linux VM を作成する](create-ssh-secured-vm-from-template.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
