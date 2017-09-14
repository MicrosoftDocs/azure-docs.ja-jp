---
title: "Azure 仮想ネットワークと Linux 仮想マシン | Microsoft Docs"
description: "チュートリアル - Azure CLI を使用した Azure 仮想ネットワークと Linux 仮想マシンの管理"
services: virtual-machines-linux
documentationcenter: virtual-machines
author: neilpeterson
manager: timlt
editor: tysonn
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 05/10/2017
ms.author: nepeters
ms.custom: mvc
ms.translationtype: HT
ms.sourcegitcommit: f9003c65d1818952c6a019f81080d595791f63bf
ms.openlocfilehash: 2366905b8160675f77cbc41ba97540af70be8c01
ms.contentlocale: ja-jp
ms.lasthandoff: 08/09/2017

---

# <a name="manage-azure-virtual-networks-and-linux-virtual-machines-with-the-azure-cli"></a>Azure CLI を使用した Azure 仮想ネットワークと Linux 仮想マシンの管理

Azure 仮想マシンでは、内部と外部のネットワーク通信に Azure ネットワークが使用されます。 このチュートリアルでは、2 台の仮想マシンをデプロイし、それらの VM に使用する Azure ネットワークを構成する手順について説明します。 このチュートリアルの例では、これらの VM が、データベース バックエンドを持つ Web アプリケーションのホストになっていることを想定していますが、アプリケーションのデプロイは、このチュートリアルでは行いません。 このチュートリアルで学習する内容は次のとおりです。

> [!div class="checklist"]
> * 仮想ネットワークをデプロイする
> * 仮想ネットワーク内にサブネットを作成する
> * 仮想マシンをサブネットに接続する
> * 仮想マシンのパブリック IP アドレスを管理する
> * 着信インターネット トラフィックを保護する
> * VM 間のトラフィックを保護する


[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

CLI をローカルにインストールして使用する場合、このチュートリアルでは、Azure CLI バージョン 2.0.4 以降を実行していることが要件です。 バージョンを確認するには、`az --version` を実行します。 インストールまたはアップグレードする必要がある場合は、「[Azure CLI 2.0 のインストール]( /cli/azure/install-azure-cli)」を参照してください。 

## <a name="vm-networking-overview"></a>VM ネットワークの概要

Azure 仮想ネットワークを使用すると、仮想マシン、インターネット、その他の Azure サービス (Azure SQL Database など) の間でセキュリティ保護されたネットワーク接続を実現できます。 仮想ネットワークは、サブネットと呼ばれる論理セグメントに分割することができます。 サブネットは、ネットワーク フローを制御する目的のほか、セキュリティ境界としても使用されます。 VM をデプロイするときは、通常、そこに仮想ネットワーク インターフェイスが含まれているので、それをサブネットに接続することになります。

## <a name="deploy-virtual-network"></a>仮想ネットワークをデプロイする

このチュートリアルでは、2 つのサブネットから成る単一の仮想ネットワークを作成します。 Web アプリケーションのホストとなるフロントエンド サブネットと、データベース サーバーのホストとなるバックエンド サブネットです。

仮想ネットワークを作成する前に、[az group create](/cli/azure/group#create) でリソース グループを作成します。 次の例では、*myRGNetwork* という名前のリソース グループを場所 eastus に作成します。

```azurecli-interactive 
az group create --name myRGNetwork --location eastus
```

### <a name="create-virtual-network"></a>Create virtual network

仮想ネットワークを作成するには、[az network vnet create](/cli/azure/network/vnet#create) コマンドを使用します。 この例では、ネットワークは *mvVnet* という名前が付けられ、*10.0.0.0/16* というアドレス プレフィックスが指定されます。 また、サブネットは、*mySubnetFrontEnd* という名前と *10.0.1.0/24* というプレフィックスで作成されます。 このチュートリアルの後の方で、このサブネットにフロントエンド VM を接続します。 

```azurecli-interactive 
az network vnet create \
  --resource-group myRGNetwork \
  --name myVnet \
  --address-prefix 10.0.0.0/16 \
  --subnet-name mySubnetFrontEnd \
  --subnet-prefix 10.0.1.0/24
```

### <a name="create-subnet"></a>サブネットの作成

仮想ネットワークに新しいサブネットを追加するには、[az network vnet subnet create](/cli/azure/network/vnet/subnet#create) コマンドを使用します。 この例では、サブネットは *mySubnetBackEnd* という名前が付けられ、*10.0.2.0/24* というアドレス プレフィックスが指定されます。 このサブネットは、すべてのバックエンド サービスで使用されます。

```azurecli-interactive 
az network vnet subnet create \
  --resource-group myRGNetwork \
  --vnet-name myVnet \
  --name mySubnetBackEnd \
  --address-prefix 10.0.2.0/24
```

この時点で、1 つのネットワークが作成され、フロントエンド サービス用とバックエンド サービス用の 2 つのサブネットに分割されました。 次のセクションでは、仮想マシンを作成してこれらのサブネットに接続します。

## <a name="understand-public-ip-address"></a>パブリック IP アドレスについて

Azure リソースにインターネットからアクセスするためには、パブリック IP アドレスが必要です。 このチュートリアル セクションでは、VM を作成して、パブリック IP アドレスの操作方法を説明します。

### <a name="allocation-method"></a>割り当て方法

パブリック IP アドレスは、動的アドレスまたは静的アドレスとして割り当てることができます。 既定では、パブリック IP アドレスが動的に割り当てられます。 動的 IP アドレスは、VM の割り当てが解除されると解放されます。 この動作により、VM の割り当て解除を伴う操作の過程で IP アドレスが変化することになります。

割り当て方法を "静的" に設定することで、VM に割り当てた IP アドレスを確実に維持し、割り当てが解除された状態でも変化しないようにすることができます。 静的に割り当てられた IP アドレスを使用するときに、IP アドレス自体を指定することはできません。 それは、利用可能なアドレスのプールから割り当てられます。

### <a name="dynamic-allocation"></a>動的割り当て

[az vm create](/cli/azure/vm#create) コマンドで VM を作成する際、既定のパブリック IP アドレスの割り当て方法は "動的" です。 次の例では、動的 IP アドレスで VM が作成されます。 

```azurecli-interactive 
az vm create \
  --resource-group myRGNetwork \
  --name myFrontEndVM \
  --vnet-name myVnet \
  --subnet mySubnetFrontEnd \
  --nsg myNSGFrontEnd \
  --public-ip-address myFrontEndIP \
  --image UbuntuLTS \
  --generate-ssh-keys
```

### <a name="static-allocation"></a>静的割り当て

静的なパブリック IP アドレスを割り当てるには、[az vm create](/cli/azure/vm#create) コマンドで仮想マシンを作成するときに `--public-ip-address-allocation static` 引数を追加します。 このチュートリアルでは、この操作について具体的に説明しませんが、次のセクションで、動的割り当ての IP アドレスを静的割り当てのアドレスに変更します。 

### <a name="change-allocation-method"></a>割り当て方法の変更

IP アドレスの割り当て方法は、[az network public-ip update](/cli/azure/network/public-ip#update) コマンドを使用して変更できます。 この例では、フロントエンド VM の IP アドレスの割り当て方法を静的に変更します。

まず、VM の割り当てを解除します。

```azurecli-interactive 
az vm deallocate --resource-group myRGNetwork --name myFrontEndVM
```

[az network public-ip update](/cli/azure/network/public-ip#update) コマンドを使用して、割り当て方法を更新します。 この例では、`--allocation-method` を *static* に設定しています。

```azurecli-interactive 
az network public-ip update --resource-group myRGNetwork --name myFrontEndIP --allocation-method static
```

VM を起動します。

```azurecli-interactive 
az vm start --resource-group myRGNetwork --name myFrontEndVM --no-wait
```

### <a name="no-public-ip-address"></a>パブリック IP アドレスを指定しない

インターネット経由で VM にアクセスできなくてもよいケースもよくあります。 パブリック IP アドレスなしで VM を作成するには、二重引用符の間を空にした `--public-ip-address ""` 引数を使用します。 この構成については、このチュートリアルの中で後から具体的に紹介します。

## <a name="secure-network-traffic"></a>ネットワーク トラフィックのセキュリティ保護

ネットワーク セキュリティ グループ (NSG) には、Azure Virtual Network (VNet) に接続されたリソースへのネットワーク トラフィックを許可または拒否する一連のセキュリティ規則が含まれています。 NSG はサブネットに関連付けることができるほか、個々のネットワーク インターフェイスに関連付けることができます。 NSG をネットワーク インターフェイスに関連付けた場合、関連付けられている VM のみにその NSG が適用されます。 NSG をサブネットに関連付けた場合、そのサブネットに接続されているすべてのリソースにその NSG のルールが適用されます。 

### <a name="network-security-group-rules"></a>ネットワーク セキュリティ グループ ルール

NSG ルールは、トラフィックが許可または拒否されるネットワーク ポートを定義します。 このルールには、特定のシステム (またはサブネット) 間のトラフィックが制御されるように送信元 IP と送信先 IP のアドレス範囲を含めることができます。 また、NSG ルールには優先順位 (1 ～ 4096) も含まれます。 ルールは、優先順位に従って評価されます。 優先順位が 100 のルールは、優先順位が 200 のルールよりも前に評価されます。

すべての NSG に既定のルール一式が含まれています。 既定のルールは削除できませんが、これには最も低い優先順位が割り当てられているため、ルールを作成することで上書きできます。

- **仮想ネットワーク** - 仮想ネットワーク内で発信および着信するトラフィックについては、受信方向と送信方向の両方で許可されます。
- **インターネット** - 送信トラフィックは許可されますが、受信トラフィックはブロックされます。
- **ロード バランサー** - Azure のロード バランサーによる VM とロール インスタンスの正常性プローブが許可されます。 負荷分散セットを使用していない場合は、このルールを上書きできます。

### <a name="create-network-security-groups"></a>ネットワーク セキュリティ グループの作成

ネットワーク セキュリティ グループは、[az vm create](/cli/azure/vm#create) コマンドを使用して、VM と同時に作成できます。 その場合、NSG は VM のネットワーク インターフェイスに関連付けられ、ポート *22* に対するすべての着信トラフィックを許可する NSG ルールが自動的に作成されます。 先ほどこのチュートリアルの中でフロントエンド VM を作成するときに、フロントエンド NSG が自動的に作成されています。 ポート 22 に対する NSG ルールも自動的に作成されています。 

場合によっては、NSG を事前に作成しておいた方がよいことがあります。たとえば、既定の SSH ルールを作成すべきでないときや NSG をサブネットに関連付けるべきでないときです。 

ネットワーク セキュリティ グループを作成するには、[az network nsg create](/cli/azure/network/nsg#create) コマンドを使用します。

```azurecli-interactive 
az network nsg create --resource-group myRGNetwork --name myNSGBackEnd
```

NSG は、ネットワーク インターフェイスにではなくサブネットに関連付けられます。 この構成では、サブネットに関連付けられているすべての VM に NSG ルールが継承されます。

*mySubnetBackEnd* という名前の既存のサブネットを新しい NSG で更新します。

```azurecli-interactive 
az network vnet subnet update \
  --resource-group myRGNetwork \
  --vnet-name myVnet \
  --name mySubnetBackEnd \
  --network-security-group myNSGBackEnd
```

ここで、仮想マシンを作成して、*mySubnetBackEnd* に関連付けます。 `--nsg` 引数には二重引用符で囲まれた空の値が指定されていることに注目してください。 NSG を VM と一緒に作成する必要はありません。 この VM はバックエンド サブネットに関連付けます。そして事前に作成しておいたバックエンド NSG でそのバックエンド サブネットを保護します。 VM には、この NSG が適用されます。 `--public-ip-address` 引数にも二重引用符で囲まれた空の値が指定されていることに注目してください。 この構成では、パブリック IP アドレスなしで VM が作成されます。 

```azurecli-interactive 
az vm create \
  --resource-group myRGNetwork \
  --name myBackEndVM \
  --vnet-name myVnet \
  --subnet mySubnetBackEnd \
  --public-ip-address "" \
  --nsg "" \
  --image UbuntuLTS \
  --generate-ssh-keys
```

### <a name="secure-incoming-traffic"></a>着信トラフィックの保護

フロントエンド VM を作成したときに、ポート 22 に対する着信トラフィックを許可する NSG ルールを作成しました。 このルールによって、VM に対する SSH 接続が許可されます。 この例では、ポート *80* に対するトラフィックも許可する必要があります。 次の構成によって、VM 上の Web アプリケーションへのアクセスが許可されます。

[az network nsg rule create](/cli/azure/network/nsg/rule#create) コマンドを使用して、ポート *80* に対するルールを作成します。

```azurecli-interactive 
az network nsg rule create \
  --resource-group myRGNetwork \
  --nsg-name myNSGFrontEnd \
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

これでフロントエンド VM には、ポート *22* とポート *80* でのみアクセスできるようになりました。 その他すべての着信トラフィックは、このネットワーク セキュリティ グループでブロックされます。 NSG ルールの構成を視覚化すると役に立つ場合があります。 [az network rule list](/cli/azure/network/nsg/rule#list) コマンドを実行すると、NSG ルールの構成が返されます。 

```azurecli-interactive 
az network nsg rule list --resource-group myRGNetwork --nsg-name myNSGFrontEnd --output table
```

出力:

```azurecli-interactive 
Access    DestinationAddressPrefix      DestinationPortRange  Direction    Name                 Priority  Protocol    ProvisioningState    ResourceGroup    SourceAddressPrefix    SourcePortRange
--------  --------------------------  ----------------------  -----------  -----------------  ----------  ----------  -------------------  ---------------  ---------------------  -----------------
Allow     *                                               22  Inbound      default-allow-ssh        1000  Tcp         Succeeded            myRGNetwork      *                      *
Allow     *                                               80  Inbound      http                      200  Tcp         Succeeded            myRGNetwork      *                      *
```

### <a name="secure-vm-to-vm-traffic"></a>VM 間のトラフィックの保護

ネットワーク セキュリティ グループ ルールは、VM 間にも適用できます。 この例では、フロントエンド VM が、ポート *22* とポート *3306* でバックエンド VM と通信する必要があります。 この構成で、フロントエンド VM からの SSH 接続を許可したうえで、さらに、フロントエンド VM 上のアプリケーションからバックエンドの MySQL データベースへの通信を許可することになります。 それ以外、フロントエンド仮想マシンとバックエンド仮想マシン間のトラフィックはすべてブロックする必要があります。

[az network nsg rule create](/cli/azure/network/nsg/rule#create) コマンドを使用して、ポート 22 に対するルールを作成します。 `--source-address-prefix` 引数に *10.0.1.0/24* という値が指定されていることに注目してください。 この構成により、フロントエンド サブネットからのトラフィックのみがこの NSG の通過を許可されます。

```azurecli-interactive 
az network nsg rule create \
  --resource-group myRGNetwork \
  --nsg-name myNSGBackEnd \
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
  --nsg-name myNSGBackEnd \
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
  --nsg-name myNSGBackEnd \
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

これでバックエンド VM には、フロントエンド サブネットからポート *22* とポート *3306* でのみアクセスできるようになりました。 その他すべての着信トラフィックは、このネットワーク セキュリティ グループでブロックされます。 NSG ルールの構成を視覚化すると役に立つ場合があります。 [az network rule list](/cli/azure/network/nsg/rule#list) コマンドを実行すると、NSG ルールの構成が返されます。 

```azurecli-interactive 
az network nsg rule list --resource-group myRGNetwork --nsg-name myNSGBackEnd --output table
```

出力:

```azurecli-interactive 
Access    DestinationAddressPrefix    DestinationPortRange    Direction    Name       Priority  Protocol    ProvisioningState    ResourceGroup    SourceAddressPrefix    SourcePortRange
--------  --------------------------  ----------------------  -----------  -------  ----------  ----------  -------------------  ---------------  ---------------------  -----------------
Allow     *                           22                      Inbound      SSH             100  Tcp         Succeeded            myRGNetwork      10.0.1.0/24            *
Allow     *                           3306                    Inbound      MySQL           200  Tcp         Succeeded            myRGNetwork      10.0.1.0/24            *
Deny      *                           *                       Inbound      denyAll         300  Tcp         Succeeded            myRGNetwork      *                      *
```

## <a name="next-steps"></a>次のステップ

このチュートリアルでは、仮想マシンとの関連で Azure ネットワークを作成し、セキュリティで保護しました。 以下の方法について学習しました。

> [!div class="checklist"]
> * 仮想ネットワークをデプロイする
> * 仮想ネットワーク内にサブネットを作成する
> * 仮想マシンをサブネットに接続する
> * 仮想マシンのパブリック IP アドレスを管理する
> * 着信インターネット トラフィックを保護する
> * VM 間のトラフィックを保護する

次のチュートリアルに進み、仮想マシンのデータを Azure Backup で保護する方法を学習してください。 

> [!div class="nextstepaction"]
> [Azure での Linux 仮想マシンのバックアップ](./tutorial-backup-vms.md)

