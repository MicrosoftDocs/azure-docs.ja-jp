---
title: "チュートリアル - Azure VM における高可用性アプリケーションの作成 | Microsoft Docs"
description: "Azure で 3 台の Linux VM とロード バランサーを使って、可用性が高く堅牢なアプリケーションを作成する方法について説明します。"
services: virtual-machines-linux
documentationcenter: virtual-machines
author: iainfoulds
manager: timlt
editor: tysonn
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 03/27/2017
ms.author: iainfou
translationtype: Human Translation
ms.sourcegitcommit: 503f5151047870aaf87e9bb7ebf2c7e4afa27b83
ms.openlocfilehash: f24cca8230e30ca3862d7e7011bcea649c6aec52
ms.lasthandoff: 03/29/2017

---

# <a name="build-a-highly-available-application-on-linux-virtual-machines-in-azure"></a>Azure の Linux 仮想マシンにおける高可用性アプリケーションの作成
このチュートリアルでは、メンテナンス イベントの発生時も正常な動作を維持できる高可用性アプリケーションを作成します。 このアプリでは、ロード バランサーと可用性セット、さらに 3 台の Linux 仮想マシン (VM) を使います。 このチュートリアルでは、Node.js アプリを作成しますが、同じ高可用性コンポーネントとガイドラインでさまざまなアプリケーション フレームワークをデプロイできます。

## <a name="step-1---azure-prerequisites"></a>手順 1 - Azure の前提条件
このチュートリアルに取り組む前に、ターミナル ウィンドウを開いて、最新の [Azure CLI 2.0](/cli/azure/install-azure-cli) がインストールされていることを確認してください。 まだ Azure サブスクリプションにログインしていない場合は、[az login](/cli/azure/#login) でログインし、画面の指示に従ってください。

```azurecli
az login
```

Azure リソース グループとは、Azure リソースのデプロイと管理に使用する論理コンテナーです。 他の Azure リソースを作成する前に、[az group create](/cli/azure/group#create) でリソース グループを作成する必要があります。 次の例では、`myResourceGroup` という名前のリソース グループを `westus` の場所に作成します。

```azurecli
az group create --name myResourceGroup --location westus
```


## <a name="step-2---create-availability-set"></a>手順 2 - 可用性セットの作成
仮想マシンは、論理上の障害ドメインと更新ドメインにまたがって作成することができます。 それぞれの論理ドメインは、基盤となる Azure データセンターにおける特定のハードウェア領域を表します。 複数の VM を作成すると、それらの領域にまたがってコンピューティング リソースとストレージ リソースが分散されます。 万一ハードウェア コンポーネントにメンテナンスが必要な状況が生じても、この分散によってアプリの稼働率が保たれます。 こうした論理上の障害ドメインと更新ドメインは、可用性セットを通じて定義することができます。

可用性セットを作成するには、[az vm availability-set create](/cli/azure/vm/availability-set#create) を使用します。 次の例では、`myAvailabilitySet` という名前の可用性セットを作成します。

```azurecli
az vm availability-set create \
    --resource-group myResourceGroup \
    --name myAvailabilitySet \
    --platform-fault-domain-count 3 \
    --platform-update-domain-count 2
```


## <a name="step-3---create-load-balancer"></a>手順 3 - ロード バランサーの作成
Azure のロード バランサーは、定義されている一連の VM に対し、ロード バランサー規則に従ってトラフィックを分散します。 正常性プローブが各 VM の特定のポートを監視し、稼働している VM にのみトラフィックを分散します。

### <a name="create-a-public-ip-address"></a>パブリック IP アドレスの作成
インターネット上のアプリにアクセスするには、パブリック IP アドレスをロード バランサーに割り当てます。 パブリック IP アドレスは、[az network public-ip create](/cli/azure/public-ip#create) で作成します。 `myPublicIP` という名前のパブリック IP アドレスを作成する例を次に示します。

```azurecli
az network public-ip create --resource-group myResourceGroup --name myPublicIP
```

### <a name="create-a-load-balancer"></a>ロード バランサーの作成
ロード バランサーは、[az network lb create](/cli/azure/network/lb#create) で作成します。 `myPublicIP` アドレスを使用して `myLoadBalancer` という名前のロード バランサーを作成する例を次に示します。

```azurecli
az network lb create \
    --resource-group myResourceGroup \
    --name myLoadBalancer \
    --frontend-ip-name myFrontEndPool \
    --backend-pool-name myBackEndPool \
    --public-ip-address myPublicIP
```

### <a name="create-a-health-probe"></a>正常性プローブの作成
ロード バランサーでアプリの状態を監視するには、正常性プローブを使用します。 正常性プローブは、ロード バランサーのローテーションに含める VM を、正常性チェックへの応答に基づいて動的に追加したり削除したりする働きをします。 既定では、15 秒間隔のチェックが 2 回連続でエラーになった VM はロード バランサーのローテーションから外されます。 正常性プローブは、プロトコルに基づいて作成するか、またはアプリの特定の正常性チェック ページに基づいて作成します。 たとえば、次の例では TCP プローブを作成していますが、この例を応用して `--path healthcheck.js` を追加することもできます。 対象となるホストをロード バランサーのローテーションに維持するには、`healthcheck.js` を作成し、**HTTP 200 OK** 応答を返す必要があります。

正常性プローブは、[az network lb probe create](/cli/azure/network/lb/probe#create) で作成します。 次の例では、個々の VM を監視する `myHealthProbe` という名前の正常性プローブを作成しています。

```azurecli
az network lb probe create \
    --resource-group myResourceGroup \
    --lb-name myLoadBalancer \
    --name myHealthProbe \
    --protocol tcp \
    --port 80
```

### <a name="create-a-load-balancer-rule"></a>ロード バランサー規則の作成
ロード バランサー規則の目的は、一連の VM に対するトラフィックの分散方法を定義することです。

ロード バランサー規則は、[az network lb rule create](/cli/azure/network/lb/rule#create) で作成します。 次の例では、`myLoadBalancerRule` という名前の規則を作成しています。正常性プローブ `myHealthProbe` を使用し、ポート `80` でトラフィックを負荷分散します。

```azurecli
az network lb rule create \
    --resource-group myResourceGroup \
    --lb-name myLoadBalancer \
    --name myLoadBalancerRule \
    --protocol tcp \
    --frontend-port 80 \
    --backend-port 80 \
    --frontend-ip-name myFrontEndPool \
    --backend-pool-name myBackEndPool \
    --probe-name myHealthProbe
```


## <a name="step-4---configure-networking"></a>手順 4 - ネットワークの構成
それぞれの VM には、仮想ネットワーク インターフェイス カード (NIC) が少なくとも 1 つ備わっていて、そこから仮想ネットワークに接続されています。 この仮想ネットワークは、定義されているアクセス規則に基づいてトラフィックをフィルタリングするようにセキュリティで保護されています。

### <a name="create-a-virtual-network"></a>仮想ネットワークの作成
対象の VM にネットワーク接続を提供するには、[az network vnet create](/cli/azure/vnet#create) で仮想ネットワークを作成します。 次の例では、`myVnet` という名前の仮想ネットワークと `mySubnet` という名前のサブネットを作成しています。

```azurecli
az network vnet create --resource-group myResourceGroup --name myVnet --subnet-name mySubnet
```

### <a name="configure-network-security"></a>ネットワーク セキュリティの構成
ネットワーク セキュリティ グループは、VM との間で行き交うトラフィックを規則に基づいて制御します。 たとえば、ポート 80 でトラフィックを許可するための規則を定義します。 ロード バランサーが一連の VM に対してトラフィックを分散する一方で、ネットワーク セキュリティ グループの規則が、許可されていないトラフィックを確実に遮断します。

[az network nsg create](/cli/azure/network/nsg#create) で、ネットワーク セキュリティ グループを作成します。 次の例では、`myNetworkSecurityGroup` という名前のネットワーク セキュリティ グループを作成します。

```azurecli
az network nsg create --resource-group myResourceGroup --name myNetworkSecurityGroup
```

Web トラフィックを目的のアプリに誘導するには、[az network nsg rule create](/cli/azure/network/nsg/rule#create) でネットワーク セキュリティ グループの規則を作成します。 次の例では、`myNetworkSecurityGroupRule` という名前のネットワーク セキュリティ グループの規則を作成しています。

```azurecli
az network nsg rule create \
    --resource-group myResourceGroup \
    --nsg-name myNetworkSecurityGroup \
    --name myNetworkSecurityGroupRule \
    --priority 1001 \
    --protocol tcp \
    --destination-port-range 80
```

### <a name="create-virtual-network-interface-cards"></a>仮想ネットワーク インターフェイス カードの作成 
ロード バランサーは、VM そのものではなく仮想 NIC リソースと連動します。 仮想 NIC は、ロード バランサーに接続され、そのうえで VM に接続されます。

仮想 NIC を作成するには、[az network nic create](/cli/azure/network/nic#create) を使用します。 次の例では、3 つの仮想 NIC を作成しています (以降の手順でアプリ用に作成する各 VM につき仮想 NIC を 1 つ)。 (One virtual NIC for each VM you create for your app in the following steps):

```bash
for i in `seq 1 3`; do
    az network nic create \
        --resource-group myResourceGroup \
        --name myNic$i \
        --vnet-name myVnet \
        --subnet mySubnet \
        --network-security-group myNetworkSecurityGroup \
        --lb-name myLoadBalancer \
        --lb-address-pools myBackEndPool
done
```


## <a name="step-5---build-your-app"></a>手順 5 - アプリの作成
**cloud-init** は、VM をカスタマイズする手段として広く使われています。 **cloud-init** を使ってパッケージをインストールしたりファイルを書き込んだりすることができます。 初回デプロイ時に **cloud_init** が実行されている間、目的のアプリを実行するために特に何かを行う必要はありません。 VM のデプロイが完了し、アプリが実行状態になると、ロード バランサーがトラフィックの負荷分散を開始します。 **cloud-init** の使用の詳細については、「[Use cloud-init to customize a Linux VM during creation (cloud-init を利用し、作成時に Linux VM をカスタマイズする)](virtual-machines-linux-using-cloud-init.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)」を参照してください。

次の **cloud-init** 構成は、**nodejs** と **npm** をインストールした後、アプリの Web プロキシとして **nginx** をインストールして構成します。 さらに、単純な "Hello World" Node.js アプリを作成し、**Express** で初期化して起動します。 別のアプリケーション フレームワークを使いたい場合は、適宜パッケージやデプロイするアプリケーションを調整してください。

`cloud-init.txt` というファイルを作成し、次の構成を貼り付けます。

```yaml
#cloud-config
package_upgrade: true
packages:
  - nginx
  - nodejs
  - npm
write_files:
  - owner: www-data:www-data
  - path: /etc/nginx/sites-available/default
    content: |
      server {
        listen 80;
        location / {
          proxy_pass http://localhost:3000;
          proxy_http_version 1.1;
          proxy_set_header Upgrade $http_upgrade;
          proxy_set_header Connection keep-alive;
          proxy_set_header Host $host;
          proxy_cache_bypass $http_upgrade;
        }
      }
  - owner: azureuser:azureuser
  - path: /home/azureuser/myapp/index.js
    content: |
      var express = require('express')
      var app = express()
      var os = require('os');
      app.get('/', function (req, res) {
        res.send('Hello World from host ' + os.hostname() + '!')
      })
      app.listen(3000, function () {
        console.log('Hello world app listening on port 3000!')
      })
runcmd:
  - nginx -s reload
  - cd "/home/azureuser/myapp"
  - npm init
  - npm install express -y
  - nodejs index.js
```


## <a name="step-6---create-virtual-machines"></a>手順 6 - 仮想マシンの作成
基礎となるコンポーネントがすべて整ったら、アプリを実行するための高可用性 VM を作成します。

### <a name="create-vms"></a>VM の作成
VM を作成するには、[az vm create](/cli/azure/vm#create) を使用します。 次の例では、3 台の VM を作成し、SSH キーを生成します (まだ存在していない場合)。

```bash
for i in `seq 1 3`; do
    az vm create \
        --resource-group myResourceGroup \
        --name myVM$i \
        --availability-set myAvailabilitySet \
        --nics myNic$i \
        --image Canonical:UbuntuServer:14.04.4-LTS:latest \
        --admin-username azureuser \
        --generate-ssh-keys \
        --custom-data cloud-init.txt \
        --no-wait
done
```

3 台の VM をすべて作成して構成するには、数分かかります。 それぞれの VM でアプリがいつ実行されるかは、ロード バランサーの正常性プローブによって自動的に検出されます。 アプリが実行状態になると、ロード バランサー規則によってトラフィックの負荷分散が開始されます。

### <a name="test-your-app"></a>アプリケーションをテストする
ロード バランサーのパブリック IP アドレスを取得するには、[az network public-ip show](/cli/azure/network/public-ip#show) を使用します。 次の例では、先ほど作成した `myPublicIP` の IP アドレスを取得しています。

```azurecli
az network public-ip show \
    --resource-group myResourceGroup \
    --name myPublicIP \
    --query [ipAddress] \
    --output tsv
```

このパブリック IP アドレスを Web ブラウザーに入力します。 アプリが表示され、ロード バランサーによって負荷分散されたトラフィックの宛先となった VM のホスト名が表示されます。

![実行中の Node.js アプリ](./media/virtual-machines-linux-tutorial-load-balance-nodejs/running-nodejs-app.png)

アプリが動作している 3 台の VM すべてに対してロード バランサーからトラフィックが負荷分散されていることを確認するために、Web ブラウザーを強制的に最新の情報に更新します。


## <a name="step-7---management-tasks"></a>手順 7 - 管理タスク
アプリを実行している VM には、OS の更新プログラムをインストールするなどメンテナンスが必要になることもあります。 また、アプリのトラフィックが増大すれば、それに対処するために、新たに VM を追加しなければなりません。 このセクションでは、ロード バランサーから VM を除外したりロード バランサーに対して VM を追加したりする方法について説明します。

### <a name="remove-a-vm-from-the-load-balancer"></a>ロード バランサーから VM を除外する
バックエンド アドレス プールから VM を削除するには、[az network nic ip-config address-pool remove](/cli/azure/network/nic/ip-config/address-pool#remove) を使用します。 次の例では、**myVM2** の仮想 NIC を `myLoadBalancer` から削除しています。

```azurecli
az network nic ip-config address-pool remove \
    --resource-group myResourceGroup \
    --nic-name myNic2 \
    --ip-config-name ipConfig1 \
    --lb-name myLoadBalancer \
    --address-pool myBackEndPool 
```

アプリが動作している残りの 2 台の VM に対してロード バランサーからトラフィックが負荷分散されていることを確認するために、Web ブラウザーを強制的に最新の情報に更新します。 これで VM に対して、OS 更新プログラムのインストールや VM の再起動などのメンテナンスを行うことができます。

### <a name="add-a-vm-to-the-load-balancer"></a>ロード バランサーに VM を追加する
VM のメンテナンスを実施した後や、処理能力の引き上げが必要となった場合は、[az network nic ip-config address-pool add](/cli/azure/network/nic/ip-config/address-pool#add) でバックエンド アドレス プールに VM を追加します。 次の例では、**myVM2** の仮想 NIC を `myLoadBalancer` に追加しています。

```azurecli
az network nic ip-config address-pool add \
    --resource-group myResourceGroup \
    --nic-name myNic2 \
    --ip-config-name ipConfig1 \
    --lb-name myLoadBalancer \
    --address-pool myBackEndPool
```


## <a name="next-steps"></a>次のステップ
このチュートリアルで紹介したいくつかの高可用性機能の詳細については、次の情報を参照してください。

- [Linux 仮想マシンの可用性管理](virtual-machines-linux-manage-availability.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
- [Azure Load Balancer の概要](../load-balancer/load-balancer-overview.md)
- [ネットワーク セキュリティ グループを使用したネットワーク トラフィック フローの制御](../virtual-network/virtual-networks-nsg.md)
- [Azure CLI サンプル スクリプト](virtual-machines-linux-cli-samples.md)
