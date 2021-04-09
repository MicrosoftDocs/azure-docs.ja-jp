---
title: チュートリアル - Linux VM 用の Azure 仮想ネットワークの作成と管理を行う
description: このチュートリアルでは、Azure CLI を使用して、Linux 仮想マシン用の Azure ディスクの作成と管理を行う方法について説明します
services: virtual-machines
documentationcenter: virtual-machines
author: cynthn
manager: gwallace
ms.collection: linux
tags: azure-resource-manager
ms.subservice: networking
ms.assetid: ''
ms.service: virtual-machines
ms.topic: tutorial
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 05/10/2017
ms.author: cynthn
ms.custom: mvc, devx-track-azurecli
ms.openlocfilehash: 6356d9b587847ff2b49574b970109ada0d785660
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102564630"
---
# <a name="tutorial-create-and-manage-azure-virtual-networks-for-linux-virtual-machines-with-the-azure-cli"></a>チュートリアル:Azure CLI を使用して Linux 仮想マシン用の Azure 仮想ネットワークの作成と管理を行う

Azure 仮想マシンでは、内部と外部のネットワーク通信に Azure ネットワークが使用されます。 このチュートリアルでは、2 台の仮想マシンをデプロイし、それらの VM に使用する Azure ネットワークを構成する手順について説明します。 このチュートリアルの例では、これらの VM が、データベース バックエンドを持つ Web アプリケーションのホストになっていることを想定していますが、アプリケーションのデプロイは、このチュートリアルでは行いません。 このチュートリアルでは、以下の内容を学習します。

> [!div class="checklist"]
> * 仮想ネットワークとサブネットの作成
> * パブリック IP アドレスの作成
> * フロントエンド VM の作成
> * ネットワーク トラフィックのセキュリティ保護
> * バックエンド VM の作成

このチュートリアルでは、[Azure Cloud Shell](../../cloud-shell/overview.md) で CLI を使用します。このバージョンは常に更新され最新になっています。 Cloud Shell を開くには、コード ブロックの上部にある **[使ってみる]** を選択します。

CLI をローカルにインストールして使用する場合、このチュートリアルでは、Azure CLI バージョン 2.0.30 以降を実行していることが要件です。 バージョンを確認するには、`az --version` を実行します。 インストールまたはアップグレードする必要がある場合は、[Azure CLI のインストール]( /cli/azure/install-azure-cli)に関するページを参照してください。

## <a name="vm-networking-overview"></a>VM ネットワークの概要

Azure 仮想ネットワークを使用すると、仮想マシン、インターネット、その他の Azure サービス (Azure SQL Database など) の間でセキュリティ保護されたネットワーク接続を実現できます。 仮想ネットワークは、サブネットと呼ばれる論理セグメントに分割することができます。 サブネットは、ネットワーク フローを制御する目的のほか、セキュリティ境界としても使用されます。 VM をデプロイするときは、通常、そこに仮想ネットワーク インターフェイスが含まれているので、それをサブネットに接続することになります。

このチュートリアルを完了すると、次の仮想ネットワーク リソースが作成されます。

![2 つのサブネットのある仮想ネットワーク](./media/tutorial-virtual-network/networktutorial.png)

- *myVNet* - VM 間、およびインターネットと通信するために VM によって使用される仮想ネットワーク。
- *myFrontendSubnet* - フロントエンド リソースによって使用される、*myVNet* 内のサブネット。
- *myPublicIPAddress* - インターネットから *myFrontendVM* にアクセスするために使用するパブリック IP アドレス。
- *myFrontentNic* - *myFrontendVM* が *myBackendVM* と通信するために使用するネットワーク インターフェイス。
- *myFrontendVM* -インターネットと *myBackendVM* との間の通信に使用する VM。
- *myBackendNSG* - *myFrontendVM* と *myBackendVM* 間の通信を制御するネットワーク セキュリティ グループ。
- *myBackendSubnet* - *myBackendNSG* に関連付けられ、バックエンド リソースによって使用されるサブネット。
- *myBackendNic* - *myBackendVM* が *myFrontendVM* と通信するために使用するネットワーク インターフェイス。
- *myBackendVM* - ポート 22 とポート 3306 を使用して *myFrontendVM* と通信する VM。

## <a name="create-a-virtual-network-and-subnet"></a>仮想ネットワークとサブネットの作成

このチュートリアルでは、2 つのサブネットと共に単一の仮想ネットワークが作成されます。 Web アプリケーションのホストとなるフロントエンド サブネットと、データベース サーバーのホストとなるバックエンド サブネットです。

仮想ネットワークを作成する前に、[az group create](/cli/azure/group) でリソース グループを作成します。 次の例では、*myRGNetwork* という名前のリソース グループを場所 eastus に作成します。

```azurecli-interactive 
az group create --name myRGNetwork --location eastus
```

### <a name="create-virtual-network"></a>Create virtual network

仮想ネットワークを作成するには、[az network vnet create](/cli/azure/network/vnet) コマンドを使用します。 この例では、ネットワークに *mvVNet* という名前が付けられ、*10.0.0.0/16* というアドレス プレフィックスが指定されます。 また、サブネットが、*myFrontendSubnet* という名前と *10.0.1.0/24* というプレフィックスで作成されます。 このチュートリアルの後の方で、このサブネットにフロントエンド VM を接続します。 

```azurecli-interactive 
az network vnet create \
  --resource-group myRGNetwork \
  --name myVNet \
  --address-prefix 10.0.0.0/16 \
  --subnet-name myFrontendSubnet \
  --subnet-prefix 10.0.1.0/24
```

### <a name="create-subnet"></a>サブネットの作成

仮想ネットワークに新しいサブネットを追加するには、[az network vnet subnet create](/cli/azure/network/vnet/subnet) コマンドを使用します。 この例では、サブネットに *myBackendSubnet* という名前が付けられ、*10.0.2.0/24* というアドレス プレフィックスが指定されます。 このサブネットは、すべてのバックエンド サービスで使用されます。

```azurecli-interactive 
az network vnet subnet create \
  --resource-group myRGNetwork \
  --vnet-name myVNet \
  --name myBackendSubnet \
  --address-prefix 10.0.2.0/24
```

この時点で、1 つのネットワークが作成され、フロントエンド サービス用とバックエンド サービス用の 2 つのサブネットに分割されました。 次のセクションでは、仮想マシンを作成してこれらのサブネットに接続します。

## <a name="create-a-public-ip-address"></a>パブリック IP アドレスの作成

Azure リソースにインターネットからアクセスするためには、パブリック IP アドレスが必要です。 パブリック IP アドレスの割り当て方法は、動的または静的として構成することができます。 既定では、パブリック IP アドレスが動的に割り当てられます。 動的 IP アドレスは、VM の割り当てが解除されると解放されます。 この動作により、VM の割り当て解除を伴う操作の過程で IP アドレスが変化することになります。

割り当て方法を "静的" に設定することで、VM に割り当てた IP アドレスを確実に維持し、割り当てが解除された状態でも変更されないようにすることができます。 静的に割り当てられた IP アドレスを使用するときに、IP アドレス自体を指定することはできません。 それは、利用可能なアドレスのプールから割り当てられます。

```azurecli-interactive
az network public-ip create --resource-group myRGNetwork --name myPublicIPAddress
```

[az vm create](/cli/azure/vm) コマンドで VM を作成する際、既定のパブリック IP アドレスの割り当て方法は "動的" です。 静的なパブリック IP アドレスを割り当てるには、[az vm create](/cli/azure/vm) コマンドで仮想マシンを作成するときに `--public-ip-address-allocation static` 引数を追加します。 このチュートリアルでは、この操作について具体的に説明しませんが、次のセクションで、動的割り当ての IP アドレスを静的割り当てのアドレスに変更します。 

### <a name="change-allocation-method"></a>割り当て方法の変更

IP アドレスの割り当て方法は、[az network public-ip update](/cli/azure/network/public-ip) コマンドを使用して変更できます。 この例では、フロントエンド VM の IP アドレスの割り当て方法を静的に変更します。

まず、VM の割り当てを解除します。

```azurecli-interactive 
az vm deallocate --resource-group myRGNetwork --name myFrontendVM
```

[az network public-ip update](/cli/azure/network/public-ip) コマンドを使用して、割り当て方法を更新します。 この例では、`--allocation-method` を *static* に設定しています。

```azurecli-interactive 
az network public-ip update --resource-group myRGNetwork --name myPublicIPAddress --allocation-method static
```

VM を起動します。

```azurecli-interactive 
az vm start --resource-group myRGNetwork --name myFrontendVM --no-wait
```

### <a name="no-public-ip-address"></a>パブリック IP アドレスを指定しない

インターネット経由で VM にアクセスできなくてもよいケースもよくあります。 パブリック IP アドレスなしで VM を作成するには、二重引用符の間を空にした `--public-ip-address ""` 引数を使用します。 この構成については、このチュートリアルの中で後から具体的に紹介します。

## <a name="create-a-front-end-vm"></a>フロントエンド VM の作成

[az vm create](/cli/azure/vm) コマンドを使用して、*myFrontendVM* という名前の VM を *myPublicIPAddress* というアドレスで作成します。

```azurecli-interactive 
az vm create \
  --resource-group myRGNetwork \
  --name myFrontendVM \
  --vnet-name myVNet \
  --subnet myFrontendSubnet \
  --nsg myFrontendNSG \
  --public-ip-address myPublicIPAddress \
  --image UbuntuLTS \
  --generate-ssh-keys
```

## <a name="secure-network-traffic"></a>ネットワーク トラフィックのセキュリティ保護

ネットワーク セキュリティ グループ (NSG) には、Azure Virtual Network (VNet) に接続されたリソースへのネットワーク トラフィックを許可または拒否する一連のセキュリティ規則が含まれています。 NSG はサブネットに関連付けることができるほか、個々のネットワーク インターフェイスに関連付けることができます。 NSG をネットワーク インターフェイスに関連付けた場合、関連付けられている VM のみにその NSG が適用されます。 NSG をサブネットに関連付けた場合、そのサブネットに接続されているすべてのリソースにその NSG のルールが適用されます。 

### <a name="network-security-group-rules"></a>ネットワーク セキュリティ グループ ルール

NSG ルールは、トラフィックが許可または拒否されるネットワーク ポートを定義します。 このルールには、特定のシステム (またはサブネット) 間のトラフィックが制御されるように送信元 IP と送信先 IP のアドレス範囲を含めることができます。 また、NSG ルールには優先順位 (1 ～ 4096) も含まれます。 ルールは、優先順位に従って評価されます。 優先順位が 100 のルールは、優先順位が 200 のルールよりも前に評価されます。

すべての NSG に既定のルール一式が含まれています。 既定のルールは削除できませんが、これには最も低い優先順位が割り当てられているため、ルールを作成することでオーバーライドできます。

NSG の既定ルールは以下のとおりです。

- **仮想ネットワーク** - 仮想ネットワーク内で発信および着信するトラフィックについては、受信方向と送信方向の両方で許可されます。
- **インターネット** - 送信トラフィックは許可されますが、受信トラフィックはブロックされます。
- **ロード バランサー** - Azure のロード バランサーによる VM とロール インスタンスの正常性プローブが許可されます。 負荷分散セットを使用していない場合は、このルールをオーバーライドできます。

### <a name="create-network-security-groups"></a>ネットワーク セキュリティ グループの作成

ネットワーク セキュリティ グループは、[az vm create](/cli/azure/vm) コマンドを使用して、VM と同時に作成できます。 その場合、NSG は VM のネットワーク インターフェイスに関連付けられ、ポート *22* に対するすべての着信トラフィックを許可する NSG ルールが自動的に作成されます。 先ほどこのチュートリアルの中でフロントエンド VM を作成するときに、フロントエンド NSG が自動的に作成されています。 ポート 22 に対する NSG ルールも自動的に作成されています。 

場合によっては、NSG を事前に作成しておいた方がよいことがあります。たとえば、既定の SSH ルールを作成すべきでないときや NSG をサブネットに関連付けるべきでないときです。 

ネットワーク セキュリティ グループを作成するには、[az network nsg create](/cli/azure/network/nsg) コマンドを使用します。

```azurecli-interactive 
az network nsg create --resource-group myRGNetwork --name myBackendNSG
```

NSG は、ネットワーク インターフェイスにではなくサブネットに関連付けられます。 この構成では、サブネットに関連付けられているすべての VM に NSG ルールが継承されます。

*myBackendSubnet* という名前の既存のサブネットを新しい NSG で更新します。

```azurecli-interactive 
az network vnet subnet update \
  --resource-group myRGNetwork \
  --vnet-name myVNet \
  --name myBackendSubnet \
  --network-security-group myBackendNSG
```

### <a name="secure-incoming-traffic"></a>受信トラフィックの保護

フロントエンド VM を作成したときに、ポート 22 に対する受信トラフィックを許可する NSG ルールを作成しました。 このルールによって、VM に対する SSH 接続が許可されます。 この例では、ポート *80* に対するトラフィックも許可する必要があります。 次の構成によって、VM 上の Web アプリケーションへのアクセスが許可されます。

[az network nsg rule create](/cli/azure/network/nsg/rule) コマンドを使用して、ポート *80* に対するルールを作成します。

```azurecli-interactive 
az network nsg rule create \
  --resource-group myRGNetwork \
  --nsg-name myFrontendNSG \
  --name http \
  --access allow \
  --protocol Tcp \
  --direction Inbound \
  --priority 200 \
  --source-address-prefix "*" \
  --source-port-range "*" \
  --destination-address-prefix "*" \
  --destination-port-range 80
```

このフロントエンド VM には、ポート *22* とポート *80* でのみアクセスできます。 その他すべての受信トラフィックは、このネットワーク セキュリティ グループでブロックされます。 NSG ルールの構成を視覚化すると役に立つ場合があります。 [az network rule list](/cli/azure/network/nsg/rule) コマンドを実行すると、NSG ルールの構成が返されます。 

```azurecli-interactive 
az network nsg rule list --resource-group myRGNetwork --nsg-name myFrontendNSG --output table
```

### <a name="secure-vm-to-vm-traffic"></a>VM 間のトラフィックを保護する

ネットワーク セキュリティ グループ ルールは、VM 間にも適用できます。 この例では、フロントエンド VM が、ポート *22* とポート *3306* でバックエンド VM と通信する必要があります。 この構成で、フロントエンド VM からの SSH 接続を許可したうえで、さらに、フロントエンド VM 上のアプリケーションからバックエンドの MySQL データベースへの通信を許可することになります。 それ以外、フロントエンド仮想マシンとバックエンド仮想マシン間のトラフィックはすべてブロックする必要があります。

[az network nsg rule create](/cli/azure/network/nsg/rule) コマンドを使用して、ポート 22 に対するルールを作成します。 `--source-address-prefix` 引数に *10.0.1.0/24* という値が指定されていることに注目してください。 この構成により、フロントエンド サブネットからのトラフィックのみがこの NSG の通過を許可されます。

```azurecli-interactive 
az network nsg rule create \
  --resource-group myRGNetwork \
  --nsg-name myBackendNSG \
  --name SSH \
  --access Allow \
  --protocol Tcp \
  --direction Inbound \
  --priority 100 \
  --source-address-prefix 10.0.1.0/24 \
  --source-port-range "*" \
  --destination-address-prefix "*" \
  --destination-port-range "22"
```

ここで、ポート 3306 に対する MySQL トラフィックのルールを追加します。

```azurecli-interactive 
az network nsg rule create \
  --resource-group myRGNetwork \
  --nsg-name myBackendNSG \
  --name MySQL \
  --access Allow \
  --protocol Tcp \
  --direction Inbound \
  --priority 200 \
  --source-address-prefix 10.0.1.0/24 \
  --source-port-range "*" \
  --destination-address-prefix "*" \
  --destination-port-range "3306"
```

最後に、NSG には、同じ VNet 内の VM 間のトラフィックをすべて許可する、という既定のルールがあるため、バックエンド NSG には、すべてのトラフィックをブロックするルールを作成できます。 ここで、`--priority` に *300* という値が指定されていることに注意してください。これは、NSG ルールと MySQL ルールのどちらよりも低い値です。 この構成により、SSH トラフィックと MySQL トラフィックについては、引き続き NSG を通過することが許可されます。

```azurecli-interactive 
az network nsg rule create \
  --resource-group myRGNetwork \
  --nsg-name myBackendNSG \
  --name denyAll \
  --access Deny \
  --protocol Tcp \
  --direction Inbound \
  --priority 300 \
  --source-address-prefix "*" \
  --source-port-range "*" \
  --destination-address-prefix "*" \
  --destination-port-range "*"
```

## <a name="create-back-end-vm"></a>バックエンド VM の作成

ここで、仮想マシンを作成して、*myBackendSubnet* に関連付けます。 `--nsg` 引数には二重引用符で囲まれた空の値が指定されていることに注目してください。 NSG を VM と一緒に作成する必要はありません。 この VM はバックエンド サブネットに関連付けます。そして事前に作成しておいたバックエンド NSG でそのバックエンド サブネットを保護します。 VM には、この NSG が適用されます。 `--public-ip-address` 引数にも二重引用符で囲まれた空の値が指定されていることに注目してください。 この構成では、パブリック IP アドレスなしで VM が作成されます。 

```azurecli-interactive 
az vm create \
  --resource-group myRGNetwork \
  --name myBackendVM \
  --vnet-name myVNet \
  --subnet myBackendSubnet \
  --public-ip-address "" \
  --nsg "" \
  --image UbuntuLTS \
  --generate-ssh-keys
```

このバックエンド VM には、フロントエンド サブネットからポート *22* とポート *3306* でのみアクセスできます。 その他すべての受信トラフィックは、このネットワーク セキュリティ グループでブロックされます。 NSG ルールの構成を視覚化すると役に立つ場合があります。 [az network rule list](/cli/azure/network/nsg/rule) コマンドを実行すると、NSG ルールの構成が返されます。 

```azurecli-interactive 
az network nsg rule list --resource-group myRGNetwork --nsg-name myBackendNSG --output table
```

## <a name="next-steps"></a>次のステップ

このチュートリアルでは、仮想マシンとの関連で Azure ネットワークを作成し、セキュリティで保護しました。 以下の方法を学習しました。

> [!div class="checklist"]
> * 仮想ネットワークとサブネットの作成
> * パブリック IP アドレスの作成
> * フロントエンド VM の作成
> * ネットワーク トラフィックのセキュリティ保護
> * バックエンド VM の作成

VM ディスクの保護の詳細については、[ディスクのバックアップとディザスター リカバリー](../backup-and-disaster-recovery-for-azure-iaas-disks.md)に関するページをご覧ください。