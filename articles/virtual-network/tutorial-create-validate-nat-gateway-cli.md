---
title: NAT ゲートウェイの作成とテスト - Azure CLI
titlesuffix: Azure Virtual Network NAT
description: このチュートリアルでは、Azure CLI を使用して NAT ゲートウェイを作成し、NAT サービスをテストする方法について説明します。
services: virtual-network
documentationcenter: na
author: asudbring
manager: KumudD
Customer intent: I want to test a NAT gateway for outbound connectivity for my virtual network.
ms.service: virtual-network
ms.subservice: nat
ms.devlang: na
ms.topic: how-to
ms.workload: infrastructure-services
ms.date: 06/11/2020
ms.author: allensu
ms.custom: devx-track-azurecli
ms.openlocfilehash: 7d4467e557105100fc32940c05fa349722689867
ms.sourcegitcommit: 269da970ef8d6fab1e0a5c1a781e4e550ffd2c55
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/10/2020
ms.locfileid: "88054376"
---
# <a name="tutorial-create-a-nat-gateway-using-azure-cli-and-test-the-nat-service"></a>チュートリアル:Azure CLI を使用した NAT ゲートウェイの作成と、NAT サービスのテスト

このチュートリアルでは、Azure 内の仮想マシンにアウトバウンド接続を提供する NAT ゲートウェイを作成します。 NAT ゲートウェイをテストするために、ソースと宛先の仮想マシンをデプロイします。 パブリック IP アドレスへの送信接続を行って、NAT ゲートウェイをテストします。 それらの接続は、送信元から送信先の仮想マシンに対して確立されます。 このチュートリアルでは、わかりやすくするために、同じリソース グループ内の 2 つの異なる仮想ネットワークにソースと宛先をデプロイしています。


[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

このチュートリアルは、Azure Cloud Shell を使用して行うことができます。また、それぞれのコマンドをローカルで実行してもかまいません。  Azure Cloud Shell を使用したことがない場合は、[今すぐサインイン](https://shell.azure.com)してください。

これらのコマンドをローカルで実行する場合は、CLI をインストールする必要があります。  このチュートリアルでは、Azure CLI バージョン 2.0.71 以降のバージョンが実行されている必要があります。 バージョンを確認するには、`az --version` を実行します。 インストールまたはアップグレードする必要がある場合は、[Azure CLI のインストール]( /cli/azure/install-azure-cli)に関するページを参照してください。


## <a name="create-a-resource-group"></a>リソース グループを作成する

[az group create](https://docs.microsoft.com/cli/azure/group) を使用して、リソース グループを作成します。 Azure リソース グループとは、Azure リソースのデプロイと管理に使用する論理コンテナーです。

次の例では、**myResourceGroupNAT** という名前のリソース グループを **eastus2** という場所に作成します。

```azurecli-interactive
  az group create \
    --name myResourceGroupNAT \
    --location eastus2
    
```

## <a name="create-the-nat-gateway"></a>NAT ゲートウェイの作成

### <a name="create-a-public-ip-address"></a>パブリック IP アドレスの作成

パブリック インターネットにアクセスするには、NAT ゲートウェイに少なくとも 1 つのパブリック IP アドレスが必要です。 [az network public-ip create](https://docs.microsoft.com/cli/azure/network/public-ip) を使用して、**myResourceGroupNAT** に **myPublicIPsource** というパブリック IP アドレス リソースを作成します。

```azurecli-interactive
  az network public-ip create \
  --resource-group myResourceGroupNAT \
  --name myPublicIPsource \
  --sku standard
  
```

### <a name="create-a-public-ip-prefix"></a>パブリック IP プレフィックスの作成

NAT ゲートウェイでは、1 つまたは複数のパブリック IP アドレス リソース、パブリック IP プレフィックス、またはその両方を使用できます。 このシナリオでは、デモンストレーションとしてパブリック IP プレフィックス リソースを追加することにします。   [az network public-ip prefix create](/cli/azure/network/public-ip/prefix?view=azure-cli-latest#az-network-public-ip-prefix-create) を使用して、**myResourceGroupNAT** に **myPublicIPprefixsource** というパブリック IP プレフィックス リソースを作成します。

```azurecli-interactive
  az network public-ip prefix create \
  --resource-group myResourceGroupNAT \
  --name myPublicIPprefixsource \
  --length 31
  
```

### <a name="create-a-nat-gateway-resource"></a>NAT ゲートウェイ リソースの作成

このセクションでは、NAT ゲートウェイ リソースを使用して、次の NAT サービスのコンポーネントを作成して構成する方法について説明します。
  - NAT ゲートウェイ リソースによって変換される送信フローに使用する、パブリック IP プールとパブリック IP プレフィックス。
  - アイドル タイムアウトを既定値の 4 分から 10 分に変更します。

[az network nat gateway create](https://docs.microsoft.com/cli/azure/network/nat?view=azure-cli-latest) を使用して、**myNATgateway** というグローバル Azure NAT ゲートウェイを作成します。 このコマンドでは、パブリック IP アドレス **myPublicIP** とパブリック IP プレフィックス **myPublicIPprefix** の両方を使用します。 さらに、アイドル タイムアウトを 10 分に変更します。

```azurecli-interactive
  az network nat gateway create \
    --resource-group myResourceGroupNAT \
    --name myNATgateway \
    --public-ip-addresses myPublicIPsource \
    --public-ip-prefixes myPublicIPprefixsource \
    --idle-timeout 10       
    
  ```

この時点で、NAT ゲートウェイは機能する状態となっていますが、仮想ネットワークのどのサブネットでそれを使用するかを構成する作業だけ残っています。

## <a name="prepare-the-source-for-outbound-traffic"></a>送信トラフィックのソースの準備

ここでは、完全なテスト環境のセットアップ手順を説明します。 オープンソース ツールを使用して、NAT ゲートウェイを検証するテストをセットアップしましょう。 まず、ソースの作業から始めます。先ほど作成した NAT ゲートウェイが使用されます。

### <a name="configure-virtual-network-for-source"></a>送信元用の仮想ネットワークの構成

VM をデプロイして NAT ゲートウェイをテストする前に、仮想ネットワークを作成しておく必要があります。

[az network Microsoft Azure Virtual Network create](https://docs.microsoft.com/cli/azure/network/vnet) を使用して、**mySubnetsource** というサブネットを持つ **myVnetsource** という仮想ネットワークを **myResourceGroupNAT** に作成します。  この仮想ネットワークの IP アドレス空間は **192.168.0.0/16** です。 この仮想ネットワーク内のサブネットは **192.168.0.0/24** です。

```azurecli-interactive
  az network vnet create \
    --resource-group myResourceGroupNAT \
    --name myVnetsource \
    --address-prefix 192.168.0.0/16 \
    --subnet-name mySubnetsource \
    --subnet-prefix 192.168.0.0/24
    
```

### <a name="configure-nat-service-for-source-subnet"></a>送信元サブネット用の NAT サービスの構成

[az network Microsoft Azure Virtual Network subnet update](https://docs.microsoft.com/cli/azure/network/vnet/subnet) を使用して、仮想ネットワーク **myVnetsource** 内の送信元サブネット **mySubnetsource** で特定の NAT ゲートウェイ リソース **myNATgateway** が使用されるように構成します。 このコマンドにより、指定したサブネット上で NAT サービスがアクティブになります。

```azurecli-interactive
    az network vnet subnet update \
    --resource-group myResourceGroupNAT \
    --vnet-name myVnetsource \
    --name mySubnetsource \
    --nat-gateway myNATgateway
    
```

これで、インターネットを宛先とするすべてのアウトバウンド トラフィックでこの NAT サービスが使用されるようになります。  UDR を構成する必要はありません。

NAT ゲートウェイをテストする前に、送信元 VM を作成する必要があります。  この VM に外部からアクセスするためのインスタンスレベルのパブリック IP として、パブリック IP アドレス リソースを割り当てます。 このアドレスは、テストのためのアクセスのみを目的としたものです。  NAT サービスが他の送信オプションよりも優先される方法を示します。

演習として、この VM をパブリック IP なしで作成し、ジャンプボックスとして使用する別の VM をパブリック IP なしで作成することもできます。

### <a name="create-public-ip-for-source-vm"></a>送信元 VM 用のパブリック IP の作成

送信元 VM へのアクセスに使用するパブリック IP を作成します。 [az network public-ip create](https://docs.microsoft.com/cli/azure/network/public-ip) を使用して、**myResourceGroupNAT** に **myPublicIPsourceVM** というパブリック IP アドレス リソースを作成します。

```azurecli-interactive
  az network public-ip create \
    --resource-group myResourceGroupNAT \
    --name myPublicIPsourceVM \
    --sku standard
    
```

### <a name="create-an-nsg-for-source-vm"></a>送信元 VM 用の NSG の作成

Standard パブリック IP アドレスは "既定でセキュリティ保護" されているため、SSH のインバウンド アクセスを許可する NSG を作成する必要があります。  Azure NAT サービスでは、フロー方向が認識されます。 同じサブネット上に NAT ゲートウェイが構成された後は、この NSG がアウトバウンドに使用されることはありません。 [az network nsg create](https://docs.microsoft.com/cli/azure/network/nsg?view=azure-cli-latest#az-network-nsg-create) を使用して、**myNSGsource** という NSG リソースを **myResourceGroupNAT** に作成します。

```azurecli-interactive
  az network nsg create \
    --resource-group myResourceGroupNAT \
    --name myNSGsource 
    
```

### <a name="expose-ssh-endpoint-on-source-vm"></a>送信元 VM 上の SSH エンドポイントの公開

送信元 VM に SSH でアクセスするためのルールを NSG に作成します。 [az network nsg rule create](https://docs.microsoft.com/cli/azure/network/nsg/rule?view=azure-cli-latest#az-network-nsg-rule-create) を使用して、**ssh** という NSG ルールを作成します。 このルールは、リソース グループ **myResourceGroupNAT** 内の **myNSGsource** という NSG に作成されます。

```azurecli-interactive
  az network nsg rule create \
    --resource-group myResourceGroupNAT \
    --nsg-name myNSGsource \
    --priority 100 \
    --name ssh \
    --description "SSH access" \
    --access allow \
    --protocol tcp \
    --direction inbound \
    --destination-port-ranges 22
    
```

### <a name="create-nic-for-source-vm"></a>送信元 VM 用の NIC の作成

[az network nic create](/cli/azure/network/nic#az-network-nic-create) を使用してネットワーク インターフェイスを作成し、パブリック IP アドレスとネットワーク セキュリティ グループに関連付けます。 

```azurecli-interactive
  az network nic create \
    --resource-group myResourceGroupNAT \
    --name myNicsource \
    --vnet-name myVnetsource \
    --subnet mySubnetsource \
    --public-ip-address myPublicIPSourceVM \
    --network-security-group myNSGsource
    
```

### <a name="create-a-source-vm"></a>送信元 VM の作成

[az vm create](/cli/azure/vm#az-vm-create) を使用して、仮想マシンを作成します。  この VM の SSH キーを生成し、後で使用するために秘密キーを格納します。

```azurecli-interactive
  az vm create \
    --resource-group myResourceGroupNAT \
    --name myVMsource \
    --nics myNicsource \
    --image UbuntuLTS \
    --generate-ssh-keys \
    --no-wait
    
```

コマンドからはすぐに制御が戻りますが、VM がデプロイされるまでには数分かかる場合があります。

## <a name="prepare-destination-for-outbound-traffic"></a>送信トラフィックの宛先の準備

今度は、NAT サービスをテストできるよう、NAT サービスによって変換された送信トラフィックの宛先を作成しましょう。

### <a name="configure-virtual-network-for-destination"></a>宛先の仮想ネットワークの構成

 送信先の仮想マシンを配置することになる仮想ネットワークを作成する必要があります。  そのコマンドの手順は送信元 VM と同じですが、送信先エンドポイントを公開するために少し変更を加えます。

[az network Microsoft Azure Virtual Network create](https://docs.microsoft.com/cli/azure/network/vnet) を使用して、**mySubnetdestination** というサブネットを持つ **myVnetdestination** という仮想ネットワークを **myResourceGroupNAT** に作成します。  この仮想ネットワークの IP アドレス空間は **192.168.0.0/16** です。 この仮想ネットワーク内のサブネットは **192.168.0.0/24** です。

```azurecli-interactive
  az network vnet create \
    --resource-group myResourceGroupNAT \
    --name myVnetdestination \
    --address-prefix 192.168.0.0/16 \
    --subnet-name mySubnetdestination \
    --subnet-prefix 192.168.0.0/24
    
```

### <a name="create-public-ip-for-destination-vm"></a>送信先 VM 用のパブリック IP の作成

送信元 VM へのアクセスに使用するパブリック IP を作成します。 [az network public-ip create](https://docs.microsoft.com/cli/azure/network/public-ip) を使用して、**myResourceGroupNAT** に **myPublicIPdestinationVM** というパブリック IP アドレス リソースを作成します。 

```azurecli-interactive
  az network public-ip create \
  --resource-group myResourceGroupNAT \
  --name myPublicIPdestinationVM \
  --sku standard
  
```

### <a name="create-an-nsg-for-destination-vm"></a>送信先 VM 用の NSG の作成

Standard パブリック IP アドレスは "既定でセキュリティ保護" されているため、SSH のインバウンド アクセスを許可する NSG を作成する必要があります。 Azure NAT サービスでは、フロー方向が認識されます。 同じサブネット上に NAT ゲートウェイが構成された後は、この NSG がアウトバウンドに使用されることはありません。 [az network nsg create](https://docs.microsoft.com/cli/azure/network/nsg?view=azure-cli-latest#az-network-nsg-create) を使用して、**myNSGdestination** という NSG リソースを **myResourceGroupNAT** に作成します。

```azurecli-interactive
    az network nsg create \
    --resource-group myResourceGroupNAT \
    --name myNSGdestination
    
```

### <a name="expose-ssh-endpoint-on-destination-vm"></a>送信先 VM 上の SSH エンドポイントの公開

送信先 VM に SSH でアクセスするためのルールを NSG に作成します。 [az network nsg rule create](https://docs.microsoft.com/cli/azure/network/nsg/rule?view=azure-cli-latest#az-network-nsg-rule-create) を使用して、**ssh** という NSG ルールを作成します。 このルールは、リソース グループ **myResourceGroupNAT** 内の **myNSGdestination** という NSG に作成されます。

```azurecli-interactive
    az network nsg rule create \
    --resource-group myResourceGroupNAT \
    --nsg-name myNSGdestination \
    --priority 100 \
    --name ssh \
    --description "SSH access" \
    --access allow \
    --protocol tcp \
    --direction inbound \
    --destination-port-ranges 22
    
```

### <a name="expose-http-endpoint-on-destination-vm"></a>送信先 VM 上の HTTP エンドポイントの公開

送信先 VM に HTTP でアクセスするためのルールを NSG に作成します。 [az network nsg rule create](https://docs.microsoft.com/cli/azure/network/nsg/rule?view=azure-cli-latest#az-network-nsg-rule-create) を使用して、**myResourceGroupNAT** 内の **myNSGdestination** という NSG に **http** という NSG ルールを作成します。

```azurecli-interactive
    az network nsg rule create \
    --resource-group myResourceGroupNAT \
    --nsg-name myNSGdestination \
    --priority 101 \
    --name http \
    --description "HTTP access" \
    --access allow \
    --protocol tcp \
    --direction inbound \
    --destination-port-ranges 80
    
```

### <a name="create-nic-for-destination-vm"></a>送信先 VM 用の NIC の作成

[az network nic create](/cli/azure/network/nic#az-network-nic-create) を使用してネットワーク インターフェイスを作成し、パブリック IP アドレス **myPublicIPdestinationVM** とネットワーク セキュリティ グループ **myNSGdestination** に関連付けます。 

```azurecli-interactive
    az network nic create \
    --resource-group myResourceGroupNAT \
    --name myNicdestination \
    --vnet-name myVnetdestination \
    --subnet mySubnetdestination \
    --public-ip-address myPublicIPdestinationVM \
    --network-security-group myNSGdestination
    
```

### <a name="create-a-destination-vm"></a>送信先 VM の作成

[az vm create](/cli/azure/vm#az-vm-create) を使用して、仮想マシンを作成します。  この VM の SSH キーを生成し、後で使用するために秘密キーを格納します。

 ```azurecli-interactive
    az vm create \
    --resource-group myResourceGroupNAT \
    --name myVMdestination \
    --nics myNicdestination \
    --image UbuntuLTS \
    --generate-ssh-keys \
    --no-wait
    
```
コマンドからはすぐに制御が戻りますが、VM がデプロイされるまでには数分かかる場合があります。

## <a name="prepare-a-web-server-and-test-payload-on-destination-vm"></a>宛先 VM における Web サーバーの準備とペイロードのテスト

まず、送信先 VM の IP アドレスを検出する必要があります。  送信先 VM のパブリック IP アドレスを取得するには、[az network public-ip show](/cli/azure/network/public-ip#az-network-public-ip-show) を使用します。 

```azurecli-interactive
  az network public-ip show \
    --resource-group myResourceGroupNAT \
    --name myPublicIPdestinationVM \
    --query [ipAddress] \
    --output tsv
    
``` 

>[!IMPORTANT]
>パブリック IP アドレスをコピーしたら、後続の手順で使用できるようメモ帳に貼り付けます。 これが宛先の仮想マシンであることがわかるようにしておいてください。

### <a name="sign-in-to-destination-vm"></a>宛先 VM へのサインイン

Cloud Shell には、前の操作で生成した SSH の資格情報が格納されているはずです。  ご使用のブラウザーで [Azure Cloud Shell](https://shell.azure.com) を開きます。 前の手順で取得した IP アドレスを使用して、仮想マシンに SSH 接続します。 

```bash
ssh <ip-address-destination>
```

サインインしたら、次のコマンドをコピーして貼り付けます。  

```bash
sudo apt -y update && \
sudo apt -y upgrade && \
sudo apt -y install nginx && \
sudo ln -sf /dev/null /var/log/nginx/access.log && \
sudo touch /var/www/html/index.html && \
sudo rm /var/www/html/index.nginx-debian.html && \
sudo dd if=/dev/zero of=/var/www/html/100k bs=1024 count=100

```

これらのコマンドによって仮想マシンが更新され、nginx がインストールされて、100 キロバイトのファイルが作成されます。 このファイルは、NAT サービスを使用してソース VM から取得されます。

宛先 VM との SSH セッションを閉じます。

## <a name="prepare-test-on-source-vm"></a>ソース VM におけるテストの準備

まず、送信元 VM の IP アドレスを検出する必要があります。  送信元 VM のパブリック IP アドレスを取得するには、[az network public-ip show](/cli/azure/network/public-ip#az-network-public-ip-show) を使用します。 

```azurecli-interactive
  az network public-ip show \
    --resource-group myResourceGroupNAT \
    --name myPublicIPsourceVM \
    --query [ipAddress] \
    --output tsv
    
``` 

>[!IMPORTANT]
>パブリック IP アドレスをコピーしたら、後続の手順で使用できるようメモ帳に貼り付けます。 これが送信元の仮想マシンであることがわかるようにしておいてください。

### <a name="sign-in-to-source-vm"></a>送信元 VM へのサインイン

先ほどと同様、SSH の資格情報は Cloud Shell に格納されています。 ご使用のブラウザーで [Azure Cloud Shell](https://shell.azure.com) の新しいタブを開きます。  前の手順で取得した IP アドレスを使用して、仮想マシンに SSH 接続します。 

```bash
ssh <ip-address-source>
```

NAT サービスのテスト準備を行うために、次のコマンドをコピーして貼り付けます。

```bash
sudo apt -y update && \
sudo apt -y upgrade && \
sudo apt install -y nload golang && \
echo 'export GOPATH=${HOME}/go' >> .bashrc && \
echo 'export PATH=${PATH}:${GOPATH}/bin' >> .bashrc && \
. ~/.bashrc &&
go get -u github.com/rakyll/hey

```

このコマンドによって仮想マシンが更新されます。go がインストールされ、GitHub から [hey](https://github.com/rakyll/hey) がインストールされて、ご利用のシェル環境が更新されます。

これで、NAT サービスをテストする準備が整いました。

## <a name="validate-nat-service"></a>NAT サービスの検証

ソース VM にログインしている間、**curl** と **hey** を使用して、宛先 IP アドレスへの要求を生成できます。

curl を使用して 100 キロバイトのファイルを取得します。  下の例の **\<ip-address-destination>** は、先ほどコピーした宛先 IP アドレスに置き換えてください。  **--output** パラメーターは、取得したファイルが破棄されることを示します。

```bash
curl http://<ip-address-destination>/100k --output /dev/null
```

**hey** を使用して、一連の要求を生成することもできます。 この場合も、 **\<ip-address-destination>** は、先ほどコピーした宛先 IP アドレスに置き換えてください。

```bash
hey -n 100 -c 10 -t 30 --disable-keepalive http://<ip-address-destination>/100k
```

このコマンドは、100 件の要求を生成するものです。一度に 10 件ずつ生成し、タイムアウトは 30 秒とします。 TCP 接続は再利用されません。  それぞれの要求で 100 キロバイトが取得されます。  実行の最後に、NAT サービスの稼動状態についていくつかの統計情報が **hey** からレポートされます。

## <a name="clean-up-resources"></a>リソースをクリーンアップする

リソース グループとそこに含まれているすべてのリソースは、不要になったら、[az group delete](/cli/azure/group#az-group-delete) コマンドを使用して削除できます。

```azurecli-interactive 
  az group delete --name myResourceGroupNAT
  
```

## <a name="next-steps"></a>次のステップ
このチュートリアルでは、NAT ゲートウェイを作成し、ソース VM と宛先 VM を作成した後、NAT ゲートウェイをテストしました。

Azure Monitor でメトリックを見て、NAT サービスの稼動状態を確認しましょう。 利用可能な SNAT ポートのリソース枯渇などの問題を診断します。  SNAT ポートのリソース枯渇は、新しいパブリック IP アドレス リソースかパブリック IP プレフィックス リソース、またはその両方を追加することで簡単に解決できます。

- [Virtual Network NAT](./nat-overview.md) について学習する。
- [NAT ゲートウェイ リソース](./nat-gateway-resource.md)について学習する。
- [Azure CLI を使用して NAT ゲートウェイ リソース](./quickstart-create-nat-gateway-cli.md)をデプロイするためのクイックスタート。
- [Azure PowerShell を使用して NAT ゲートウェイ リソース](./quickstart-create-nat-gateway-powershell.md)をデプロイするためのクイックスタート。
- [Azure portal を使用して NAT ゲートウェイ リソース](./quickstart-create-nat-gateway-portal.md)をデプロイするためのクイックスタート。

> [!div class="nextstepaction"]

