---
title: "Azure での VM 名前解決に内部 DNS を使用する | Microsoft Docs"
description: "Azure での VM 名前解決に内部 DNS を使用します。"
services: virtual-machines-linux
documentationcenter: 
author: vlivech
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 12/05/2016
ms.author: v-livech
translationtype: Human Translation
ms.sourcegitcommit: 1aeb983730f732a021b828c658cc741f8659c487
ms.openlocfilehash: caae05d89bc0375e08418a015150726e2a6393b9
ms.lasthandoff: 02/27/2017


---

# <a name="using-internal-dns-for-vm-name-resolution-on-azure"></a>Azure での VM 名前解決に内部 DNS を使用する

この記事では、仮想 NIC カード (VNic) と DNS ラベル名を使用して Linux VM に静的な内部 DNS 名を設定する方法を説明します。 静的 DNS 名は、このドキュメントで使用している Jenkins ビルド サーバーや、Git サーバーなどの永続的なインフラストラクチャ サービスに使用されます。

要件は次のとおりです。

* [Azure アカウント](https://azure.microsoft.com/pricing/free-trial/)
* [SSH パブリック キー ファイルおよびプライベート キー ファイル](virtual-machines-linux-mac-create-ssh-keys.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)


## <a name="cli-versions-to-complete-the-task"></a>タスクを完了するための CLI バージョン
次のいずれかの CLI バージョンを使用してタスクを完了できます。

- [Azure CLI 1.0](#quick-commands) - クラシック デプロイメント モデルと Resource Manager デプロイメント モデル用の CLI (本記事)
- [Azure CLI 2.0](virtual-machines-linux-static-dns-name-resolution-for-linux-on-azure.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) - Resource Manager デプロイ モデル用の次世代 CLI


## <a name="quick-commands"></a>クイック コマンド

タスクをすばやく実行する必要がある場合のために、次のセクションでは、必要なコマンドの詳細について説明します。 詳細な情報と各手順のコンテキストが、ドキュメントの残りの部分に記載されています。[ここからお読みください](#detailed-walkthrough)。  

前提条件: リソース グループ、VNet、SSH 受信を使用した NSG、サブネット。

### <a name="create-a-vnic-with-a-static-internal-dns-name"></a>静的な内部 DNS 名を使用して VNic を作成する

`-r` cli フラグは、DNS のラベルを設定する際に使用され、VNic に静的に DNS 名を提供します。

```azurecli
azure network nic create jenkinsVNic \
-g myResourceGroup \
-l westus \
-m myVNet \
-k mySubNet \
-r jenkins
```

### <a name="deploy-the-vm-into-the-vnet-nsg-and-connect-the-vnic"></a>VM を VNet と NSG にデプロイし、VNic を接続する

`-N` は、Azure へのデプロイの際に VNic を 新しい VM に接続します。

```azurecli
azure vm create jenkins \
-g myResourceGroup \
-l westus \
-y linux \
-Q Debian \
-o myStorageAcct \
-u myAdminUser \
-M ~/.ssh/id_rsa.pub \
-F myVNet \
-j mySubnet \
-N jenkinsVNic
```

## <a name="detailed-walkthrough"></a>詳細なチュートリアル

Azure の完全に継続的インテグレーションと継続的なデプロイ (CiCd) インフラストラクチャには、特定のサーバーを静的サーバーまたは有効期間の長いサーバーに設定する必要があります。  仮想ネットワーク (VNet) や ネットワーク セキュリティ グループ (NSG) などの Azure 資産は、ほとんどデプロイされることのない有効期間の長い静的リソースにすることをお勧めします。  VNet をデプロイしたら、インフラストラクチャに悪影響を及ぼすことなく、新しいデプロイで再利用できます。  この静的なネットワークに Git リポジトリ サーバーと Jenkins オートメーション サーバー追加することによって、開発環境やテスト環境に CiCd が提供されます。  

内部 DNS 名では、Azure 仮想ネットワーク内でのみ解決可能です。  DNS 名は内部でのみ解決可能であり、インターネット外部では解決できないため、インフラストラクチャのセキュリティが強化されます。

_各例を実際の名前に置き換えてください。_

## <a name="create-the-resource-group"></a>リソース グループの作成

このチュートリアルで作成するすべてのものを整理するためのリソース グループが必要です。  Azure リソース グループの詳細については、「[Azure Resource Manager の概要](../azure-resource-manager/resource-group-overview.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)」をご覧ください。

```azurecli
azure group create myResourceGroup \
--location westus
```

## <a name="create-the-vnet"></a>VNet の作成

まず、VM を起動する VNet を作成します。  このチュートリアルでは、VNet にサブネットが&1; つ含まれます。  Azure VNet の詳細については、「[Azure CLI を使用した仮想ネットワークの作成](../virtual-network/virtual-networks-create-vnet-arm-cli.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)」をご覧ください。

```azurecli
azure network vnet create myVNet \
--resource-group myResourceGroup \
--address-prefixes 10.10.0.0/24 \
--location westus
```

## <a name="create-the-nsg"></a>NSG の作成

サブネットは既存のネットワーク セキュリティ グループの背後に作成されるので、サブネットの前に NSG を作成します。  Azure NSG はネットワーク層のファイアウォールに相当します。  Azure NSG の詳細については、「[Azure CLI で NSG を作成する方法](../virtual-network/virtual-networks-create-nsg-arm-cli.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)」をご覧ください。

```azurecli
azure network nsg create myNSG \
--resource-group myResourceGroup \
--location westus
```

## <a name="add-an-inbound-ssh-allow-rule"></a>受信 SSH 許可ルールの追加

Linux VM はインターネットからのアクセスを必要とするため、受信ポート 22 のトラッフィクを、Linux VM のポート 22 にネットワーク経由で渡すことを許可するルールが必要です。

```azurecli
azure network nsg rule create inboundSSH \
--resource-group myResourceGroup \
--nsg-name myNSG \
--access Allow \
--protocol Tcp \
--direction Inbound \
--priority 100 \
--source-address-prefix * \
--source-port-range * \
--destination-address-prefix 10.10.0.0/24 \
--destination-port-range 22
```

## <a name="add-a-subnet-to-the-vnet"></a>VNet へのサブネットの追加

VNet 内の VM はサブネットに配置する必要があります。  各 VNet には複数のサブネットを含めることができます。  サブネットを作成し、NSG に関連付けてサブネットにファイアウォールを追加します。

```azurecli
azure network vnet subnet create mySubNet \
--resource-group myResourceGroup \
--vnet-name myVNet \
--address-prefix 10.10.0.0/26 \
--network-security-group-name myNSG
```

これで、サブネットが VNet 内に追加され、NSG と NSG ルールに関連付けられました。

## <a name="creating-static-dns-names"></a>静的 DNS 名の作成

Azure には非常に高い柔軟性が備わっていますが、VM の名前解決に DNS 名を使用するには、DNS のラベル付けを使用して仮想ネットワーク カード (VNic) として作成する必要があります。  VNic は、さまざまな VM に接続して再利用できるので重要です。VM は一時的なものでもかまいませんが、VNic は静的リソースとして保持します。  VNic で DNS ラベル付けを使用すると、VNet 内の他の VM からのシンプルな名前解決を有効にできます。  解決可能な名前を使用すると、DNS 名 `Jenkins` または Git サーバー `gitrepo` を使って、他の VM がオートメーション サーバーにアクセスできます。  VNic を作成し、前の手順で作成したサブネットに関連付けます。

```azurecli
azure network nic create jenkinsVNic \
-g myResourceGroup \
-l westus \
-m myVNet \
-k mySubNet \
-r jenkins
```

## <a name="deploy-the-vm-into-the-vnet-and-nsg"></a>VNet と NSG への VM のデプロイ

VNet、VNet 内のサブネット、SSH 用のポート 22 を除くすべての受信トラフィックをブロックしてサブネットを保護するファイアウォールとして機能する NSG の用意ができました。  これで、この既存のネットワーク インフラストラクチャ内に VM をデプロイできます。

Azure CLI と `azure vm create` コマンドを使用して、既存の Azure リソース グループ、VNet、サブネット、VNic に Linux VM をデプロイします。  CLI を使用して完全な VM をデプロイする方法の詳細については、「[Azure CLI を使用して、完全な Linux 環境を作成する](virtual-machines-linux-create-cli-complete.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)」をご覧ください。

```azurecli
azure vm create jenkins \
--resource-group myResourceGroup myVM \
--location westus \
--os-type linux \
--image-urn Debian \
--storage-account-name mystorageaccount \
--admin-username myAdminUser \
--ssh-publickey-file ~/.ssh/id_rsa.pub \
--vnet-name myVNet \
--vnet-subnet-name mySubnet \
--nic-name jenkinsVNic
```

CLI フラグを使用して既存のリソースを呼び出すことで、既存のネットワーク内に VM をデプロイするよう Azure に指示します。  繰り返しますが、VNet とサブネットをデプロイしたら、Azure リージョン内でこれらを静的または永続的なリソースにしておくことができます。  

## <a name="next-steps"></a>次のステップ

* [Azure Resource Manager テンプレートを使用して特定のデプロイを作成する](virtual-machines-linux-cli-deploy-templates.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Azure CLI コマンドを直接使用して Linux VM 用の独自のカスタム環境を作成する](virtual-machines-linux-create-cli-complete.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [テンプレートを使用して Azure に Linux VM を作成する](virtual-machines-linux-create-ssh-secured-vm-from-template.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

